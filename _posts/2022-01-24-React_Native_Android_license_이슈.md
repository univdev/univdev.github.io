---
title: "[React Native] Please accept all necessary Android SDK licenses using Android SDK Manager"
author:
  name: 박 찬영
  link: https://github.com/univdev
date: 2022-01-24 15:32:00 +0900
categories: [Tech, React Native]
tags: [React Native, Android, M1]
---
# 개요
최근 제 맥북 패키지가 여러모로 꼬여서 아예 공장 초기화를 돌린다음 개발환경을 다시 세팅했습니다.  
React Native 환경은 개발환경 초기 세팅이 아주 뭣같기로 유명합니다.  
실행만 하면 충돌이 일어나는데 원인은 모르겠는 상황이 참 많이 터지죠. 이번에 작성할 포스트도 그에 관해서 작성할 예정입니다.
# 사건
개발 환경 설정법에 따라서 JDK 설치하고, Android Studio 설치하고, 에뮬레이터도 설치하고나서 ```yarn android```를 실행했습니다.  
정상적으로 나올 것을 기대하고 실행했는데, 역시 한 번에 되는 법은 없듯이 에러가 바로 튀어나왔습니다.
```text
error Failed to install the app. Please accept all necessary Android SDK licenses using Android SDK Manager: "$ANDROID_HOME/tools/bin/sdkmanager --licenses".
Error: Command failed: ./gradlew app:installDebug -PreactNativeDevServerPort=8081
```
뭔가 라이센스 어쩌구 하는 에러가 나왔습니다.  
보아하니 Android SDK에 대한 라이센스를 수락해달라는 내용 같은데, 저번에 설치할 때는 못봤던 이슈라서 좀 난감했습니다.  
구글에다가 검색해보니 해결 방법을 찾게 되어 밑에 정리합니다.
# 해결 방법
1. Android studio의 SDK Manager를 실행합니다.
2. Appearance & Behavior 탭을 선택합니다.
3. Android SDK 탭을 선택합니다.
4. ```Android SDK Command-line Tools(latest)``` SDK가 미설치 되어있다면 해당 SDK를 설치합니다.
5. ```open ~/.zshrc```를 실행하고 가장 아랫줄에 환경 변수를 작성해줍니다.
```text
export ANDROID_SDK=/Users/<your_computer_name>/Library/Android/sdk
export PATH=/Users/<your_computer_name>/Library/Android/sdk/platform-tools:$PATH
```
6. ```source ~/.zshrc```를 실행하여 환경 변수를 적용합니다.

# 참고
[M1 React Native Android Studio 시뮬레이터 이슈][참고]

[참고]: https://velog.io/@seri_ous/M1-React-Native-Android-Studio-%EC%8B%9C%EB%AE%AC%EB%A0%88%EC%9D%B4%ED%84%B0