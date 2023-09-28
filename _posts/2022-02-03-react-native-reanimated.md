---
title: "[React Native] React Native Reanimated 패키지 설치 방법"
author:
  name: 박 찬영
  link: https://github.com/unidev
date: 2022-02-03 21:52:00 +0900
categories: [Tech, React Native]
tags: [React Native, Animation]
---
# 개요
React Native에서는 애니메이션을 구현하기가 정말 어렵습니다. 특히 웹에서 현란한 애니메이션을 구현해 본 적 있는 사람이라면 상대적으로 크게 느껴질 대목이라고 생각합니다.  
웹이라면 CSS3 혹은 Javascript를 통해서 간편하게 애니메이션을 구현할 수 있는데, React Native 환경에서는 간단한 Opacity 애니메이션이라면 몰라도 Reflow 레벨의 애니메이션을 구현하기에는 상당히 어렵죠.

그래서 해당 문제점을 보완하고자 React Native Community에서는 ```React Native Reanimated```라는 패키지를 개발했습니다.  
하지만 공식 문서의 설치 가이드를 따르면 에러가 발생하는 부분이 있기에 이 문제를 해결한 방법을 적고자 합니다.
# 문제점
제가 설치한 패키지는 2.3.1 버전입니다. 공식 문서에 적힌 모든 사항을 준수 해서 설치했는데도 아래와 같은 문제가 발생했습니다.

> Invariant Violation: TurboModuleRegistry.getEnforcing(...): 'NativeReanimated' could not be found. Verify that a module by this name is registered in the native binary.

어떤 문서에서는 1.x 버전으로 다운그레이드를 해서 해결했다고 하는데 이 방법은 제가 개인적으로 굉장히 싫어하는 방법이기에 다른 방법을 찾아 해결하였고, 설치 과정부터 차근차근 아래에 기재해보도록 하겠습니다.
# 설치 방법
## 패키지 설치
아래와 같이 패키지를 설치해줍니다.
```sheel
yarn add react-native-reanimated
```
## Babel 명시
Babel에 ```React native reanimated``` 패키지에 관하여 명시해줍니다.  
```javascript
  module.exports = {
      ...
      plugins: [
          ...
          'react-native-reanimated/plugin',
      ],
  };
```
> 반드시 ```react-native-reanimated/plugin``` 패키지는 ```plugins``` 목록에서 가장 하단에 배치 되어야 합니다.

## Android
```android/app/build.gradle``` 파일에 이미 존재하는 ```enableHermes``` 속성을 ```true```로 변경해줍니다.
```gradle
project.ext.react = [
  enableHermes: true  // <- here | clean and rebuild if changing
]
```

```mainApplication.java``` 파일에 다음과 같이 추가해줍니다.
```java
  import com.facebook.react.bridge.JSIModulePackage; // <- add
  import com.swmansion.reanimated.ReanimatedJSIModulePackage; // <- add
  ...
  private final ReactNativeHost mReactNativeHost = new ReactNativeHost(this) {
  ...

      @Override
      protected String getJSMainModuleName() {
        return "index";
      }

      @Override
      protected JSIModulePackage getJSIModulePackage() {
        return new ReanimatedJSIModulePackage(); // <- add
      }
    };
  ...
```
## iOS
iOS는 autolink를 지원하기 때문에 별도의 파일 수정이 필요 없습니다. ```ios``` 폴더에 들어가서 ```pod install```만 입력하면 됩니다.
## 캐시 제거
가장 중요한 파트입니다. 이 부분에 명시 된 내용을 시행하지 않으면 위에 기재한 이슈가 발생할 수 있습니다.
```shell
yarn start --reset-cache
```
위 내용을 입력하면 문제 없이 실행되는 것을 보실 수 있습니다.