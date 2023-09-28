---
title: "React Query로 관리하는 효율적인 서버 데이터 핸들링"
author:
  name: 박 찬영
  link: https://github.com/univdev
date: 2022-10-26 00:54:00 +0900
categories: [Tech, React Query]
tags: [React, React Query]
---
![React Query](/assets/post_images/2022-10-26-react-query-tutorial/20221026005504.png)
_React Query_

[React Query][React Query]는 네트워크에서 받아온 데이터를 관리하기 위해 처리해야 하는 다양하고 번거로운 절차들을 대신 처리해주는 라이브러리입니다.  
# React Query
![npm - React Query](/assets/post_images/2022-10-26-react-query-tutorial/20221026010704.png){: width="400" }
_npm 다운로드 횟수_
주간 다운로드 횟수가 백만 회를 넘어갈 정도로 인기가 많은 라이브러리인데, 서버 데이터에 대해 어떤 문제점이 있길래 이렇게나 많은 호응을 얻을 수 있었을까요?
# 사용해야 하는 이유
## 중복 된 네트워크 호출 방지
1. User의 데이터를 호출하는 `useUser` 라는 Hook이 존재합니다.
2. `useUser`라는 Hook을 사용하는 컴포넌트가 존재합니다.
3. 동일한 페이지에 해당 컴포넌트가 여러 번 호출 되었습니다.

이런 경우에는 어떻게 될까요? 동일한 데이터이기에 한번 호출했던 데이터를 계속해서 사용하는게 Best겠지만, 그런 부분들은 뒤로 미뤄두고 위 경우만 봤을 때는 사용 된 컴포넌트의 갯수에 비례하여 네트워크 호출 횟수가 증가하게 될 것 입니다.  
React Query는 중복 된 API 호출을 `Query Key`를 이용하여 구분하고 중복되는 요청에 대해서는 **캐시에 저장해 둔 데이터를 활용하여 네트워크 요청을 최소화합니다.**
## 신선하지 않은(Stale) 데이터 처리
**'신선하지 않은 데이터'**란 실제 서버에서 갖고 있는 데이터와 클라이언트가 갖고 있는 데이터가 서로 일치하지 않을 가능성이 존재하는 데이터를 의미합니다.  
사용자가 서비스에 접속한 이후 아무 행동도 하지 않고 서비스를 방치하는 경우, 아무런 데이터 갱신도 이루어지지 않게 되기에 사용자에게 옳지 않은 데이터를 보여 줄 가능성이 있습니다.

React Query는 이러한 상황에 대비해서 브라우저나 특정 Element에 포커스가 맞춰지면 API를 호출하거나, 일정 시간이 지난 뒤에는 데이터가 상했을 것이라 추측하고 다시 요청하는 등 다양한 방법을 이용해서 데이터를 갱신합니다.
## 데이터 처리 상황 표시
서버로부터 데이터를 불러오는 과정에서 로딩을 표시하거나, 에러가 발생한 경우 에러 메시지를 표시해야 하는 경우가 자주 있습니다.  
원래라면 이 모든 것을 별도의 상태를 작성하고 `try catch` 등을 이용하여 수동적으로 갱신해야 했지만, 이를 대신 처리해줍니다.
## 무한 스크롤 구현
![20221026012124](/assets/post_images/2022-10-26-react-query-tutorial/20221026012124.png)
_Infinite Scroll 출처: https://uxdesign.cc/the-infinite-scroll-effect-how-design-can-hack-your-brain-996a9b8129d9_

아마 UX 개발자가 아니더라도 '무한 스크롤'에 대해서는 다들 아실거라고 생각합니다.  
무한 스크롤을 라이브러리의 도움을 받지 않고 구현한다면, 데이터 저장을 위한 상태, 페이지 저장을 위한 상태, 예외 처리를 위한 상태 등 간단한 기능에 비해 꽤나 복잡하고 수고스러운 과정이 필요합니다.

React Query는 무한 스크롤의 손쉬운 구현을 위해 `useInfiniteQuery` Hook을 지원합니다.

- 페이지 정보에 대한 관리
- 로딩 상태 관리
- 데이터 관리
- 다음 페이지 호출 진행 여부에 대한 관리

위 번거로운 과정들을 각각의 상태로 자동으로 만들어주기 때문에 UI 구현에만 집중할 수 있습니다.
## 그 외
- 네트워크가 유실 되었다가 연결되면 자동으로 API를 다시 호출해줍니다.
- 네트워크 요청이 모종의 이유로 한 번 끊길 경우를 대비하여 재시도 횟수를 설정할 수 있습니다.

위와 같이 직접 구현하기에는 정말 매우 번거로울 것 같지만 반드시 필요한 기능들을 처리해주는 라이브러리라고 이해해주시면 될 것 같습니다.
# 시작해보기
> 본문에서는 바닐라 React Query가 아닌 @tanstack/react-query를 사용합니다.

## 설치
```
npm i -S @tanstack/react-query
# or
yarn add @tanstack/react-query
```

## Context 설정
```tsx
{%raw%}
import {
  useQuery,
  useMutation,
  useQueryClient,
  QueryClient,
  QueryClientProvider,
} from '@tanstack/react-query';

// Create a client
const queryClient = new QueryClient();

function App() {
  return (
    // Provide the client to your App
    <QueryClientProvider client={queryClient}>
      <Todos />
    </QueryClientProvider>
  )
}
{%endraw%}
```
대부분의 React 라이브러리가 그렇듯 서비스의 최상단에 Context를 래핑해줘야 합니다.  
래핑을 하셨다면 이제 하위 모든 페이지 혹은 컴포넌트에서 React Query의 다양한 Hook들을 사용하실 수 있습니다.
# Hooks
## useQuery
`useQuery` Hook은 API를 한 개 요청하고 결과값을 받아올 때 쓰이는 Hook이기에, 가장 보편적이고 많이 쓰입니다.  
```tsx
{%raw%}
export const App = () => {
  const { isLoading, data } = useQuery(['user', 1], () => fetch('/api/users/1').then((res) => res.json()));
};
{%endraw%}
```
위와 같이 사용하실 수 있습니다.  
첫번째 인자로 들어가는 배열에는 **'이 요청이 어떤 API를 호출했는지 구분할 수 있는 식별 키'**가 들어갑니다.  
아까 위에서 동일한 네트워크 요청이 여러 횟수 발생하면 있는거 재활용해서 쓴다고 했었죠? 그 로직을 처리할 때 사용됩니다. 동일한 식별 키의 요청이 들어오면 재활용 할지 새로 불러올지 라이브러리가 알아서 선택하는거죠.

두번째 인자로는 `Promise`를 Return하는 함수가 포함되어야 합니다.  
여기서 반환되는 `Promise`가 성공적으로 완료되면 해당 Hook이 반환하는 `data` Property에 결과값이 적용됩니다.  
`Promise`가 아직 처리되지 않았다면 `isLoading` Property가 `true`가 되기에 로딩 UI 처리도 손쉽게 하실 수 있습니다.
## useQueries
이름만 봐도 대충 뭔지 아시겠죠?  
동시에 여러 API 요청을 처리하기 위해 만들어진 Hook입니다. 그냥 단순히 `useQuery`를 여러개 핸들링하기 귀찮으니 이를 하나로 묶어둔거라 사용법도 완전히 동일합니다.  
```typescript
const results = useQueries({
  queries: [
    { queryKey: ['post', 1], queryFn: fetchPost, staleTime: Infinity},
    { queryKey: ['post', 2], queryFn: fetchPost, staleTime: Infinity}
  ]
})
```
파라미터에는 `object` 타입의 객체를 전달받으며, `queries` Property에 위 코드와 같이 배열로 처리할 요청과 고유 키, 옵션 등을 보내면 됩니다.
## useInfiniteQuery
위에서 예시로 잠깐 나왔던 무한 스크롤 기법을 만들기 위해 필요한 보편적인 로직을 제공하는 Hook입니다.  
```typescript
const {
  fetchNextPage, // 다음 페이지 데이터를 불러올 수 있는 함수
  fetchPreviousPage, // 이전 페이지 데이터를 불러올 수 있는 함수
  hasNextPage, // 다음 페이지가 존재하는지 구분할 수 있는 식별자
  hasPreviousPage, // 이전 페이지가 존재하는지 구분할 수 있는 식별자
  isFetchingNextPage, // 다음 페이지를 불러오고 있는 중인지 구분할 수 있는 식별자
  isFetchingPreviousPage, // 이전 데이터를 불러오고 있는 중인지 구분할 수 있는 식별자
  ...result // etc...
} = useInfiniteQuery(queryKey, ({ pageParam = 1 }) => fetchPage(pageParam), {
  ...options,
  getNextPageParam: (lastPage, allPages) => lastPage.nextCursor,
  getPreviousPageParam: (firstPage, allPages) => firstPage.prevCursor,
})
```
단순히 다음 페이지를 불러오는 방식만 고려한 것이 아니라 양방향 스크롤도 고려하여 만들어졌기에 유연하게 사용하실 수 있습니다.
## 그 외
가장 많이 쓰이는 세가지 Hook에 대해서 알아봤습니다.  
그 외에도 `Parallel Queries`나 `Dependent Queries` 등 다양한 상황에서 활용하실 수 있는 Hook들이 존재합니다.
# Tanstack Devtools 설치하기
저는 React Query를 사용할 때 오리지널 버전보다는 `Tanstack`에서 만든 `@tanstack/react-query`를 사용하는데요.  
`Tanstack`팀에서 제작한 강력한 개발툴을 확장해서 사용할 수 있기 때문입니다.

우선 Devtools를 설치하기 위해 아래 명령어를 입력해줍니다.
```
npm i -S @tanstack/react-query-devtools
# or
yarn add @tanstack/react-query-devtools
```
그 다음, React Query Context를 사용했던 위치로 돌아가서 Context 바로 아래에 `<ReactQueryDevtools />`를 추가합니다.  
```tsx
{%raw%}
import {
  useQuery,
  useMutation,
  useQueryClient,
  QueryClient,
  QueryClientProvider,
} from '@tanstack/react-query';
import { ReactQueryDevtools } from '@tanstack/react-query-devtools';

// Create a client
const queryClient = new QueryClient();

function App() {
  return (
    // Provide the client to your App
    <QueryClientProvider client={queryClient}>
      <ReactQueryDevtools />
      <Todos />
    </QueryClientProvider>
  )
}
{%endraw%}
```

그러면 다음과 같이 React Query로 호출한 모든 네트워크의 상태를 관찰할 수 있는 개발 도구가 표시됩니다. 🎉  
![React Query Debugger](/assets/post_images/2022-10-26-react-query-tutorial/20221026150737.png)
_React Query Debugger_

[React Query]: https://tanstack.com/query/v4