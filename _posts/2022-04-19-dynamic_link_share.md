---
title: "[React Native] Dynamic Link로 소셜 공유 만들기"
author:
  name: 박 찬영
  link: https://github.com/univdev
date: 2022-04-19 15:39:00 +0900
categories: [Tech, React Native]
tags: [React Native, Dynamic link]
---
# 내가 보는 페이지 공유
네이버, 페이스북, 트위터 등 여러 앱을 서핑하다보면 내가 보고 있는 페이지를 공유하고 싶어질 때가 있습니다.  
그런데 웹은 현재 페이지의 URL을 공유하면 되지만 앱의 특정 스크린을 표시하려면 어떻게 해야 할까요?
# Dynamic URL
Dynamic URL을 설명하자면 별도로 글을 하나 더 써야할 정도로 설명할 내용이 많지만 쉽게 설명하자면 **앱이 깔려있으면 앱을 띄우고, 앱이 없다면 다른 페이지를 웹에서 띄우도록 도와주는** 솔루션입니다.  
또한, 앱을 빌드할 때 생성되는 앱의 Custom URL은 보안상의 이유로 **특정 페이지에 들어가자마자 자동으로 리다이렉트**가 이루어지는 수행할 수 없는 반면 Dynamic URL은 평범한 웹 주소의 형태 (https)를 하고 있기 떄문에 리다이렉트 형태로 사용해도 문제 없이 앱을 표시할 수 있습니다.
# Dynamic Link 연결하기
## 프리픽스 추가
![Dynamic Link][Dynamic Link]

Dynamic Link를 앱에 연결하기 위해선 프리픽스를 설정해야 합니다.  
> 프리픽스는 https://prefix.app.com에서의 prefix.app.com을 말합니다.

여기서 설정한 프리픽스를 기반으로 파이어베이스 링크가 생성이 됩니다.
## 도메인 인증
![Dynamic Link][Dynamic Link 2]

소유한 도메인의 소유권을 증명하기 위해서 해당 도메인의 DNS를 설정해줍니다.

[Dynamic Link]: https://firebasestorage.googleapis.com/v0/b/univdev-github-io.appspot.com/o/firebase-dynamic-link-%E1%84%8B%E1%85%A7%E1%86%AB%E1%84%80%E1%85%A7%E1%86%AF-1.png?alt=media&token=62bb1c01-6127-4e7d-b8dc-b32ea40fa79e
[Dynamic Link 2]: https://firebasestorage.googleapis.com/v0/b/univdev-github-io.appspot.com/o/firebase-dynamic-link-%E1%84%8B%E1%85%A7%E1%86%AB%E1%84%80%E1%85%A7%E1%86%AF-2.png?alt=media&token=587ab85e-7820-4000-b9ca-79f79b5a8346