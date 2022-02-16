---
title: "[React Native] Lottie와 React Native를 이용한 역동적인 앱 만들기"
author:
  name: 박 찬영
  link: https://github.com/univdev
date: 2022-01-26 10:14:00 +0900
categories: [Tech, React Native]
tags: [React Native, Lottie]
---
# Lottie
![로고][로고]
[Lottie][로티]는 웹이나 앱 환경에서 역동적이며 멋있는 복잡한 애니메이션을 구현하는 것을 도와주는 라이브러리입니다.  
# 장점
- 개발자는 코드만으로는 구현하기 힘든 애니메이션을 쉽게 구현할 수 있게 해줍니다.
- 디자이너는 복잡한 애니메이션을 구현하기 위해 개발자의 눈치를 보지 않아도 되는 이점이 있습니다.
- 일반적인 GIF의 경우 프레임 드랍, 성능 이슈 등을 동반하는 반면, Lottie는 해당 이슈로부터 벗어날 수 있습니다.

# React Native의 예제
## 패키지 설치
Lottie를 실제로 사용해보기 위해서 패키지를 설치합니다.
```shell
yarn add lottie-react-native
yarn add lottie-ios@3.2.3
```
아이폰 개발을 위해서 pod를 설치합니다.
```shell
cd ios
pod install
```
## 애니메이션 애셋 다운로드
코드를 작성하기 전에, 마음에 드는 애니메이션 파일을 구해야 합니다. *(직접 [Adobe의 After Effect][After Effect]를 이용하여 만들수도 있습니다.)*  
이 문서에서는 다른 누군가가 만들어 둔 애셋을 다운받아 진행할 예정입니다.  
다른 사람이 만들어놓은 예쁜 애셋들은 [Lottie 공식 홈페이지][공홈]에서 받으실 수 있습니다!

여기 마침 [적절한 스플래시 애니메이션][예제 파일]이 있으니 얘를 사용해서 역동적인 애니메이션을 보여주는 스플래시 스크린을 만들어봅시다.  
페이지에 방문하면 우측 상단에 있는 [Download] 버튼을 눌러 [Lottie JSON] 타입으로 다운로드 받아봅시다.  
다운로드 받은 Lottie JSON 파일을 ```[RN Project Name]/assets/lottie``` 경로에 집어넣고 파일의 이름을 ```splash.json```이라고 명명합니다.
## 실제 코드 작성하기
스플래시 스크린을 구현하기 위해 페이지를 구성하는 코드를 작성해봅시다.  
도입부에는 사용할 패키지를 ```import```하는 작업이 필요하겠죠?
```jsx
import React from 'react';
import { View } from 'react-native';
import LottieView from 'lottie-react-native';
```
```import``` 코드 아래쪽에 실제 페이지에 반영 될 컴포넌트를 정의하고 해당 컴포넌트를 ```export```합니다.  
```LottieView``` 컴포넌트를 이용하여 ```required```한 애니메이션 JSON을 실제로 보여줄 수 있습니다.
```jsx
{% raw %}
const App = () => {
  return (
    <View
      style={{
        flex: 1,
        height: '100%',
        alignItems: 'center',
        justifyContent: 'center',
      }}
    >
      <LottieView
        style={{
          width: '100%',
          height: '100%',
        }}
        source={require('./assets/lottie/splash.json')}
        autoPlay
        loop={false}
      />
    </View>
  );
};

export default App;
{% endraw %}
```
## 결과물
![결과물][결과]  
아주 예쁜 스플래시 스크린이 만들어졌네요!

[로고]: https://i.pinimg.com/originals/37/7d/e4/377de42aabf87d7f489773b512a220dd.gif
[로티]: https://lottiefiles.com
[After Effect]: https://lottiefiles.com/plugins/after-effects
[공홈]: https://lottiefiles.com/
[예제 파일]: https://lottiefiles.com/12100-splash-app-rtour24
[결과]: https://firebasestorage.googleapis.com/v0/b/univdev-github-io.appspot.com/o/lottie_result.gif?alt=media&token=7c133e4d-e6fd-428f-92a8-864521b0d726