---
title: "SSR 환경에서의 React Query"
author:
  name: 박 찬영
  link: https://github.com/univdev
date: 2023-01-16 18:00:00 +0900
categories: [Tech, React Query]
tags: [React, Next.js, React Query]
image:
  path: /assets/post_images/2023-01-16-react-query-ssr/20230116184315.png
  alt: "SSR 환경에서의 React Query"
---
![React Query](/assets/post_images/2023-01-16-react-query-ssr/20230116184315.png)  

React Query는 서버와 클라이언트 사이에서 데이터를 관리하기 위한 좋은 라이브러리입니다.  
자세한 설명은 [React Query][React Query] 문서를 확인 해주세요.

# SSR 환경에서 사용하기
React Query는 `useQuery`라는 `hook`을 이용하여 사용하는 점에서 알 수 있듯이 클라이언트 렌더링 때 데이터를 호출하고 관리하는 라이브러리입니다.  
그러다보니 웹 서버에 제일 처음 접속하여 수신 받는 HTML 문서에는 **API 호출 이후에 렌더링 되는 데이터들**이 전혀 존재하지 않습니다.  
이는 초기 로딩 속도 및 검색 엔진 최적화에서 그다지 바람직하지 않아요.

이 문제를 해결하기 위해 React Query 공식 페이지에서 알려주는 솔루션에 대해서 소개합니다.
## Solution 1
Next.js 환경에서 SSR을 구현하기 위한 보편적인 방법은 `getStaticProps` 함수 혹은 `getServerSideProps`를 사용하는 것입니다.
{% raw %}
```tsx
export const getServerSideProps = () => {
  const data = fetch(api).then((r) => r.json()).then((r) => r);

  return {
    props: {
      data,
    },
  };
};
```
{% endraw %}
위 코드를 사용하면 api 라는 Endpoint에 데이터를 호출하여 SSR에서 데이터를 가져온 뒤, props 객체에 데이터를 담아 페이지 렌더링을 진행할 수 있습니다.
{% raw %}
```tsx
const getData = () => fetch(api).then((r) => r.json()).then((r) => r);

export const IndexPage = (props) => {
  const data = useQuery({
    queryKey: ['data'],
    queryFn: getData,
    initialData: props.data, // 여기 중요!
  });

  return (
    <div>
      { data.isSuccess && data }
    </div>
  );
};

export const getServerSideProps = () => {
  const data = getData();

  return {
    props: {
      data,
    },
  };
};
```
{% endraw %}
`getServerSideProps`에서 데이터를 호출한 뒤에 `useQuery`의 프로퍼티 중 하나인 `initialData`에 데이터를 전달하면 Next.js는 서버에서 불러온 데이터를 기반으로 HTML을 생성하여 기존의 문제점을 해결할 수 있습니다.  
## Solution 2
**가급적 이 방법을 사용하시길 권장합니다.**  
두 번째 방법은 `dehydrate`와 `hydrate`를 사용하는 방법입니다.
{% raw %}
```tsx
export interface AppProps {
  dehydratedState: DehydratedState;
};

export const App = ({ Component, ...rest }: AppPropsWithLayout<AppProps>) => {
  const Layout = Component.Layout || ((page) => page);

  return (
    <QueryClientProvider>
      <Hydrate state={rest.pageProps.dehydratedState}>
        { Component }
      </Hydrate>
    </QueryClientProvider>
  );
};

export default App;
```
{: file="/pages/_app.tsx"}
{% endraw %}
우선 `/pages/_app.tsx` 파일에 `Hydrate` Provider를 추가합니다.  
`pageProps` 객체에는 열린 페이지 컴포넌트에 할당 되는 `props`가 객체로 전달 되는데, 이 안에 `dehydratedState` 라는 값이 포함 될 예정입니다.  
`dehydratedState`를 `Hydrate` Provider에 전달합니다.

{% raw %}
```tsx
import { QueryClient, dehydrate } from '@tanstack/react-query';

const getData = () => fetch(api).then((r) => r.json()).then((r) => r);

export const getServerSideProps = () => {
  const queryClient = new QueryClient();

  await queryClient.prefetchQuery({
    queryKey: ['data'],
    queryFn: getData,
  });

  return {
    props: {
      dehydratedState: dehydrate(queryClient),
    },
  };
};
```
{% endraw %}
이후 데이터가 실제로 호출 되는 하위 페이지에서는 `getServerSideProps` 함수 내에서 `QueryClient`를 선언한 뒤 `prefetchQuery` 메소드를 사용하여 API를 호출합니다.  
`prefetchQuery` 메소드는 `useQuery` 메소드와 유사하지만 실제로 데이터를 반환하지는 않고 해당 API에서 넘어온 데이터를 캐싱하는 역할만 합니다.

`dehydratedState`는 페이지 컴포넌트의 `props`에 할당 되어 `_app.tsx` 파일에서 `pageProps` 객체로 참조가 가능합니다.  
Dehydrate된 `QueryClient`를 `Hydrate`라는 컴포넌트가 CSR을 시작할 때 동일한 Query를 호출하는 부분을 찾아 `initialData`에 할당하는 역할을 수행합니다.

```tsx
import { QueryClient, dehydrate } from '@tanstack/react-query';

const getData = () => fetch(api).then((r) => r.json()).then((r) => r);

export const IndexPage = () => {
  const data = useQuery({
    queryKey: ['data'],
    queryFn: getData,
  });

  return (
    <div>
      { data.isSuccess && data }
    </div>
  );
};

export const getServerSideProps = () => {
  const queryClient = new QueryClient();

  await queryClient.prefetchQuery({
    queryKey: ['data'],
    queryFn: getData,
  });

  return {
    props: {
      dehydratedState: dehydrate(queryClient),
    },
  };
};
```
그러면 위와 같이 `initialData` 없이 SSR 처리가 가능합니다.
# 마치며
SSR 환경에서의 개발을 진행하시다 보면 `hydrate`라는 용어를 굉장히 많이 접하실거예요.  
이 상황에서 말하는 `hydrate`는 대부분 '서버에서 만들어진 데이터를 클라이언트에서 활용할 수 있도록 동기화 시켜주는 작업'이라고 생각하시면 될 것 같습니다.  
[이에 대해 작성했던 문서][Hydration]가 있으니 한 번 쯤 읽어주시면 좋을 것 같습니다.

[React Query]: /posts/react-query-tutorial/
[Hydration]: /posts/nextjs-hydration/