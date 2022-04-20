---
title: "나를 칭찬해주세요 페이지 제작 완료! (feat. Svelte)"
author:
  name: 박 찬영
  link: https://github.io/univdev
date: 2022-02-20 20:36:00 +0900
categories: [Tech, Talk]
---
# 구축한 웹사이트
![찬영이를 칭찬해주세요][찬영이를 칭찬해주세요 웹사이트 대문]

<p style="text-align: center;">
  <a href="https://univdev-recommends.firebaseapp.com/" target="_blank">찬영이를 칭찬해주세요!</a> 웹사이트
</p>
# 개요
원래는 추천사를 Github의 README 형태로 관리하고 있었습니다.  
그러다보니 비개발자인 친구 혹은 버전 관리 경험이 전무한 개발자들에게 추천사 작성을 부탁하기가 어려웠다는 점이 문제로 다가왔습니다.

![컨트리뷰터][컨트리뷰터]

그럼에도 불구하고 Fork와 Pull Request를 공부하여 추천사를 작성해준 고마운 친구들이 있었지만 방치해도 될 문제는 아니라고 생각되어 보다 쉽게 추천사를 작성해줄 수 있도록 에디터를 하나 만들어야겠다고 생각하게 되었습니다.
# 개발 도구
본 웹사이트는 Svelte + Sapper를 사용하여 제작되었습니다.  
백엔드는 Firebase Functions가 사용 되었고, Database는 NoSQL 기반의 Firebase Firestore가 사용되었습니다.

## Svelte
2021년 스택오버플로우에서 조사한 프론트엔드 개발자에게 가장 많은 사랑을 받는 개발 도구 1위  
## Sapper
Svelte를 기반으로 한 SSR 및 자동 라우트 기능을 지원하는 프레임워크 (Vue의 Nuxt와 비슷한 포지션)
## 익숙하지도 않은 Svelte를 사용한 이유
![Svelte][Svelte]

일단 개발자들이 사용해보면서 긍정적인 경험을 느꼈다고 한 것에는 이유가 있었을 것이고, 현업에 당장 적용하기 힘든 개발 방식에 대한 학습은 토이 프로젝트에서 진행하면서 하나씩 배워나가는게 좋을 것 같다고 생각되어 **굳이** 적용 해보게 되었습니다.
## Firebase를 사용하게 된 이유
![Firebase][Firebase]

Firebase는 꽤 예전부터 많은 개발자들의 개발 비용을 줄여준 유용한 Serverless 플랫폼입니다.  
그럼에도 불구하고 회사에서 웹앱을 개발할 때 Push Notification이나 Dynamic Link 정도를 구현하기 위해서 사용 했던 것을 제외한다면 어차피 자체적으로 개발 된 백엔드 API를 사용했기 때문에 Firebase에 대해서 굳이 공부하려고 하지도 않았던 것 같습니다.

그러던 도중 어쩌다가 기회가 되어 Firebase의 Firestore를 연결하여 사용해보았는데, 별도의 데이터베이스 설계, 백엔드 API 작성의 수고가 없다는게 어떤 의미인지 몸소 깨닫게 되었고 그 이후부터 Firebase의 팬이 되었기 때문에 본 프로젝트에 사용하였습니다.
# 의존성 패키지
## [Firebase][Firebase Link]
Google의 Firebase 플랫폼 서비스와 NodeJS의 개발 환경을 손쉽게 연동 및 사용할 수 있는 라이브러리입니다.  
본 어플리케이션에서 사용한 서비스는 Firestore와 Functions가 있습니다.
## [Papercss][PaperCSS]
보다 귀여운 웹페이지를 제작할 수 있게 도와주는 CSS Library입니다.
## [moment][moment]
손쉽게 날짜를 계산할 수 있는 패키지입니다.
## [canvas-confetti][Canvas Confetti]
Canvas를 통해 다양한 파티클 효과를 사용할 수 있는 라이브러리입니다.
## [node-sens][Node Sens]
Naver의 Sens 서비스를 쉽게 이용할 수 있도록 설계한 NodeJS 라이브러리입니다.  
Sens는 외부 프로그램에서 문자를 전송할 수 있도록 API를 지원하는데, 이 과정에서 Header에 담아서 보내야 할 정보의 암호화가 번거로워 사용하게 되었습니다.

[찬영이를 칭찬해주세요 웹사이트 대문]: https://firebasestorage.googleapis.com/v0/b/univdev-github-io.appspot.com/o/%E1%84%8E%E1%85%A1%E1%86%AB%E1%84%8B%E1%85%A7%E1%86%BC%E1%84%8B%E1%85%B5%E1%84%85%E1%85%B3%E1%86%AF_%E1%84%8E%E1%85%B5%E1%86%BC%E1%84%8E%E1%85%A1%E1%86%AB%E1%84%92%E1%85%A2%E1%84%8C%E1%85%AE%E1%84%89%E1%85%A6%E1%84%8B%E1%85%AD.png?alt=media&token=117af760-eb0e-4a97-a881-4594c5f315ea
[찬영이를 칭찬해주세요]: https://univdev-recommends.firebaseapp.com/
[컨트리뷰터]: https://firebasestorage.googleapis.com/v0/b/univdev-github-io.appspot.com/o/%E1%84%8E%E1%85%AE%E1%84%8E%E1%85%A5%E1%86%AB%E1%84%89%E1%85%A1_%E1%84%8F%E1%85%A5%E1%86%AB%E1%84%90%E1%85%B3%E1%84%85%E1%85%B5%E1%84%87%E1%85%B2%E1%84%90%E1%85%A5.png?alt=media&token=13f79482-f44c-454e-9d51-59c357926fde
[Svelte]: https://firebasestorage.googleapis.com/v0/b/univdev-github-io.appspot.com/o/svelte.png?alt=media&token=d1556ee0-f3a9-4324-93fc-edeb4a99d7d4
[Firebase]: https://firebasestorage.googleapis.com/v0/b/univdev-github-io.appspot.com/o/Firebase_Logo.png?alt=media&token=13a7c022-f5e0-4b27-8638-d855bb051d60
[Firebase Link]: https://firebase.google.com/
[PaperCSS]: https://www.getpapercss.com/
[moment]: https://www.npmjs.com/package/moment
[Canvas Confetti]: https://www.npmjs.com/package/canvas-confetti
[Node Sens]: https://www.npmjs.com/package/node-sens