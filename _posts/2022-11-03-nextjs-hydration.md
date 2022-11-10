---
title: "Nextjs의 hydration이란?"
date: 2022-11-03 10:53:00 +0900
author:
  name: 박 찬영
  link: https://github.com/univdev
categories: ["Tech", "Nextjs"]
tags: ["Nextjs", "React", "Hydration"]
---
# Hydration
![Hydration](https://raw.githubusercontent.com/univdev/markdown-images/master/20221103113303.png)
_Hydration을 검색하면 나오는 이미지_
**Hydration**은 위 이미지처럼 **'수분 공급'**을 의미합니다.  
Hydration을 이해하기 전에 CSR, SSR이 무엇인지 간단하게 짚고 넘어가야 합니다.
# CSR
Client Side Rendering의 약자입니다.  
Web은 단순히 홈페이지라는 개념을 넘어서 응용프로그램의 영역을 대체한지 오래입니다.  
사용자의 Interaction을 감지하고 Feedback을 전달하기에 기존의 정적인 웹은 애로사항이 많았습니다.

CSR을 도입하면서 페이지가 이동할 때 아름다운 애니메이션을 보여주는게 가능해졌고, 이전 페이지에서 불러왔던 리소스를 다음 페이지로 이동할 때 재호출을 하지 않아도 된다는 점에서 트래픽을 아낄 수 있다는 장점도 생겨났어요.  
하지만 기존 웹에서는 생각치도 못했던 단점이 생겨나기 시작했습니다.

- 느린 초기로딩 속도
  - 기존의 HTML, CSS, Javascript는 웹사이트를 구동하기 위한 최소한의 다운로드만 진행하면 됐었습니다.
  - 하지만 CSR이 도입되면서 CSR을 구현해줄 라이브러리도 함께 사용하다보니 웹에 접속하는 모든 사람이 React나 Vue와 같은 라이브러리를 같이 다운로드 해야만 합니다.
- 검색엔진 최적화
  - CSR은 말 그대로 홈페이지 접속 이후 Javascript가 DOM을 생성하거나 교체하는 등의 작업을 통해 웹을 구현하는 것을 말합니다.
  - 검색엔진들은 웹을 탐색하면서 읽어낸 HTML을 토대로 해당 웹사이트의 요소들을 파악하지만 CSR은 크롤러가 웹을 읽는 타이밍에 HTML을 생성하지 못하므로 페이지 색인에 문제가 생깁니다.

이 문제들을 해결하기 위해 도입된 개념이 SSR입니다.
# SSR
Server Side Rendering의 약자입니다.  
말 그대로 서버에서 HTML 및 CSS 등을 생성하여 사용자에게 제공하는 것을 말합니다.  
SSR의 장점은 다음과 같습니다.

- 빠른 초기로딩 속도
  - 웹서비스가 호스팅 되는 곳과 물리적으로 가까운 컴퓨터에서 HTML, CSS에 대한 구성을 끝내므로 반응속도가 상대적으로 빠릅니다.
  - Javascript가 HTML을 생성하기 전에도 이미 만들어진 HTML을 보여줄 수 있으므로 사용자로 하여금 더 빠른 반응속도를 체감하게 해줍니다.
- 검색엔진 최적화
  - 서버에서 HTML을 생성하고 제공하므로 검색엔진 최적화에 대한 문제점이 없어집니다.

# 본론으로 돌아와서
하지만 Server Side Rendering은 한가지 치명적인 문제가 있는데, HTML을 그려줄 수는 있어도 실제로 브라우저가 렌더링 한 것이 아니기 때문에 Javascript의 코드를 DOMElement와 매칭시켜서 동적인 페이지를 구현하지는 못한다는 것입니다.  
그래서 SSR로 인해 렌더링 된 HTML 페이지는 뼈대만 존재하는 **바싹 마른 형태**로 존재하게 되는데 이렇게 건조한 웹 페이지에 **수분 공급**을 해줄 필요가 있습니다.  
**이 작업을 Hydration이라고 합니다.**

요약하자면 본 문서의 핵심 내용은 다음과 같습니다.
1. CSR은 동적인 웹사이트를 만들 수 있어서 좋아요. 하지만 초기로딩이 느리고 검색엔진에 잘 잡히지 않을 수 있어요.
2. SSR은 초기로딩이 빠르고 검색엔진에도 잘 잡혀서 사용자 유입에서 이점을 가져올 수 있어요.
3. Next.js는 SSR과 CSR을 병행해서 사용합니다.
4. SSR에서 만든 페이지는 다 좋은데 Javascript의 동적인 이벤트들을 Element에 할당하지 못해요.
5. 그렇기 때문에 SSR에서 만들어진 매마른 페이지에 동적 이벤트라는 수분 공급을 해주는 과정을 Hydration이라고 합니다.

# 레퍼런스
[React의 Hydration에 대하여][레퍼런스]

[레퍼런스]: https://velog.io/@huurray/React-Hydration-%EC%97%90-%EB%8C%80%ED%95%98%EC%97%AC