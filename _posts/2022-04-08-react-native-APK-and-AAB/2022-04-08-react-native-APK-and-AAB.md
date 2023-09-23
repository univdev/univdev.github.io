---
title: '[React Native] APK와 AAB'
author:
  name: 박 찬영
  link: https://github.com/univdev
date: 2022-04-08 17:48:00 +0900
categories: [Tech, React Native]
tags: [React Native, Android]
---
# 개요
지난번에는 [RN으로 제작한 앱을 APK로 빌드하는 방법][APK 빌드]에 대하여 알아보았습니다.  
하지만 이를 Playstore에 직접 올려 배포하기 위해서는 APK 빌드가 아닌, **AAB 빌드**에 대하여 알아봐야 합니다.
# AAB
APK는 윈도우에서의 EXE처럼 운영체제에서 구동되는 소프트웨어의 확장자입니다. 개발자는 물론이고 사용자들 사이에서도 널리 알려져있죠.  
그렇다면 AAB는 무엇일까요? 다소 생소한 이름인데, 이는 **APK를 사용자의 단말기에 설치하는 것을 도와주는 번들 파일**의 확장자입니다.  
## 번들파일
![Webpack][Webpack]
_번들러로 유명한 Webpack_
번들파일이란 위 사진과 같이 뿔뿔이 흩어져있는 각종 파일들을 병합하여 생성 된 파일입니다.  
이 작업을 **번들링**이라 하고, 이 작업을 수행해주는 도구를 **번들러**라고 합니다.
## 장점
AAB 파일은 사용자의 단말기에 APK를 설치하기 위한 모든 정보를 갖고 있는 파일입니다.  
이를 Playstore에 업로드하여 배포하면 사용자 단말기의 여러 특성을 고려하여 **반드시 필요한 파일들로만 이루어진** APK를 설치하게 만들 수 있습니다.  
한국어 사용자에게 영어 언어팩을 설치한다던가, 구형 단말기에서 신형 단말기의 기능을 구현하기 위한 코드를 설치하는 등의 불필요한 작업을 생략하여 보다 가벼운 어플리케이션을 배포하는 것이 가능하게 됩니다.

또한, 기존에는 각각의 단말기에 설치하기 위한 APK 파일을 여러 개로 빌드하여 관리해야 한다는 단점도 존재했는데 AAB 배포를 이용한다면 앱 개발자는 AAB 파일 하나에만 집중할 수 있으니 생산성의 증가도 생각해볼 수 있겠죠.
## 단점
AAB 파일은 Google의 Playstore를 통해서만 배포할 수 있습니다.  
그렇기 때문에 원스토어, 아마존 앱스토어와 같은 서드파티 스토어에 배포하는 것이 불가능합니다.
# APK를 이용한 배포
과거에는 Playstore에도 APK를 이용하여 앱을 배포할 수 있었으나, 구글 플레이스토어 정책 변경으로 인하여 더 이상 APK를 이용한 배포가 불가능합니다.
# AAB 파일 빌드
```
./gradlew bundleRelease
```
위 명령어로 빌드하면 ```android/app/outputs/bundle/release``` 폴더 안에 생성됩니다.  
위 명령어를 수행하기 이전 까지의 과정을 보시려면 [이 문서][APK 빌드]를 참고해주세요.
# 레퍼런스
[요즘 IT - 구글이 안드로이드 앱 배포를 APK 대신 AAB로 바꾸는 이유][레퍼런스]

[APK 빌드]: /posts/react-native-android-%EB%B9%8C%EB%93%9C/
[Webpack]: https://firebasestorage.googleapis.com/v0/b/univdev-github-io.appspot.com/o/webpack.png?alt=media&token=a4306aa0-0013-4a38-9ead-2ce31ab240f7
[레퍼런스]: https://yozm.wishket.com/magazine/detail/912/