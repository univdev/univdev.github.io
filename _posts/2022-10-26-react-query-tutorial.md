---
title: "React Query로 관리하는 효율적인 서버 데이터 핸들링"
author:
  name: 박 찬영
  link: https://github.com/univdev
date: 2022-10-26 00:54:00 +0900
categories: [Tech, Talk]
tags: [React, React Query]
---
![React Query](https://raw.githubusercontent.com/univdev/markdown-images/master/20221026005504.png)
_React Query_

[React Query][React Query]는 네트워크에서 받아온 데이터를 관리하기 위해 처리해야 하는 다양하고 번거로운 절차들을 대신 처리해주는 라이브러리입니다.  
# React Query
![npm - React Query](https://raw.githubusercontent.com/univdev/markdown-images/master/20221026010704.png){: width="400" }
_npm 다운로드 횟수_
주간 다운로드 횟수가 백만 회를 넘어갈 정도로 인기가 많은 라이브러리인데, 서버 데이터에 대해 어떤 문제점이 있길래 이렇게나 많은 호응을 얻을 수 있었을까요?
# 사용해야 하는 이유
## 중복 된 네트워크 호출 방지
1. User의 데이터를 호출하는 `useUser` 라는 Hook이 존재합니다.
2. `useUser`라는 Hook을 사용하는 컴포넌트가 존재합니다.
3. 동일한 페이지에 해당 컴포넌트가 여러 번 호출 되었습니다.

이런 경우에는 어떻게 될까요? 동일한 데이터이기에 한번 호출했던 데이터를 계속해서 사용하는게 Best겠지만, 그런 부분들은 뒤로 미뤄두고 위 경우멘 봤을 때는 사용 된 컴포넌트의 갯수에 비례하여 네트워크 호출 횟수가 증가하게 될 것 입니다.  
React Query는 중복 된 API 호출을 `Query Key`를 이용하여 구분하고 중복되는 요청에 대해서는 **캐시에 저장해 둔 데이터를 활용하여 네트워크 요청을 최소화합니다.**
## 신선하지 않은(Stale) 데이터 처리
**'신선하지 않은 데이터'**란 실제 서버에서 갖고 있는 데이터와 클라이언트가 갖고 있는 데이터가 서로 일치하지 않을 가능성이 존재하는 데이터를 의미합니다.  
사용자가 서비스에 접속한 이후 아무 행동도 하지 않고 서비스를 방치하는 경우, 아무런 데이터 갱신도 이루어지지 않게 되기에 사용자에게 옳지 않은 데이터를 보여 줄 가능성이 있습니다.

React Query는 이러한 상황에 대비해서 브라우저나 특정 Element에 포커스가 맞춰지면 API를 호출하거나, 일정 시간이 지난 뒤에는 데이터가 상했을 것이라 추측하고 다시 요청하는 등 다양한 방법을 이용해서 데이터를 갱신합니다.
## 데이터 처리 상황 표시
서버로부터 데이터를 불러오는 과정에서 로딩을 표시하거나, 에러가 발생한 경우 에러 메시지를 표시해야 하는 경우가 자주 있습니다.  
원래라면 이 모든 것을 별도의 상태를 작성하고 `try catch` 등을 이용하여 수동적으로 갱신해야 했지만, 이를 대신 처리해줍니다.
## 무한 스크롤 구현
![20221026012124](https://raw.githubusercontent.com/univdev/markdown-images/master/20221026012124.png)
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
# 직접 사용해보기
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
} from '@tanstack/react-query'

// Create a client
const queryClient = new QueryClient()

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

[React Query]: https://tanstack.com/query/v4