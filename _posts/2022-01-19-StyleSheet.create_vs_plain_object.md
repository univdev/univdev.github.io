---
title: "[React Native] StyleSheet.create vs Plain Object"
author:
  name: 박 찬영
  link: https://github.com/univdev
date: 2022-01-19 15:46:00 +0900
categories: [Tech, React Native]
tags: [React Native, Performance]
---
# StyleSheet.create vs Plain Object
React Native로 앱을 개발해 본 적이 있는 분들이라면 css를 선언할 때
```javascript
const Styles = StyleSheet.create({
  container: {
    flexDirection: 'row',
  },
});
```
처럼 ```StyleSheet.create```를 사용하여 선언하는 방식과
```javascript
const Styles = {
  container: {
    flexDirection: 'row',
  },
};
```
처럼 ```Plain Object```를 통해 선언하는 방식을 본 적이 있을겁니다.  
둘 중 어떤 방식을 취하더라도 스타일을 전달하는데는 문제가 없습니다. 그러면 당연히 ```Plain Object```를 썼을 때보다 ```StyleSheet.create```로 선언을 했을 때 취할 수 있는 장점이 있으니까 React팀이 ```StyleSheet``` 객체를 만들어뒀을 거라고 생각하게 됩니다.

결론부터 말하자면 **아무런 장점이 없습니다.**
# StyleSheet가 주는 장점이 없다고?
네 맞습니다. 장점 없어요. 대부분의 개발자들이 ```StyleSheet``` 객체를 이용함으로서 취할 수 있는 이득이 있다고 생각하고, 그 이점이라고 하면 대부분 **성능 향상**을 떠올립니다.  
하지만 놀랍게도 정말 **장점이 없습니다.**
# 흔히들 하는 오해
## 성능 향상의 이점이 있다.
사실이 아닙니다. 성능면에서 ```Plain Object```와 ```StyleSheet```는 결과값이 완전히 동일하므로 성능면에서 아무런 차이가 없습니다.
## 컴파일 타임에서 스타일 시트의 여부를 구분한다.
사실이 아닙니다. 애초에 ```Vanila Javascript```는 유효성 검사를 할 수 있는 기능이 없습니다.  
물론 런타임 때는 유효성 검사를 하지만 모두가 알다시피 두 방식으로 제작 된 스타일시트는 컴포넌트에 정상적으로 할당이 되며 동작마저 동일하게 이루어집니다.
# 그럼 왜 만든거지?
이 섹션은 [성능 향상](#성능-향상의-이점이-있다) 오해와 뿌리가 같습니다.  
React Native팀은 ```StyleSheet```를 별도로 만들고 성능 향상을 위해 개발을 시도한 적이 있습니다.  
실제로 **React Native**라이브러리의 **StyleSheet** 코드의 [주석][주석]을 보면 성능과 관련한 섹션이 있는데, 매번 스타일 객체를 새로 만드는 대신 Style 요소에 고유의 ID를 생성하고 중복 사용 시 기존에 생성했던 ID를 이용하여 퍼포먼스 향상을 시도한 흔적이 보입니다만, *(아직 구현되지 않음)*이라는 코멘트가 붙여져 있는 것을 볼 수 있습니다.  
그러므로 성능면에서 더 나은 경험을 주려는 시도는 했으나 아직 차이를 보일만한 개발이 이루어지지는 않았다고 볼 수 있습니다.
# 참조
[[Stackoverflow] React Native - What is the benefit of using StyleSheet vs a plain object][참조]

[주석]: https://github.com/facebook/react-native/blob/main/Libraries/StyleSheet/StyleSheet.js#L223
[참조]: https://stackoverflow.com/questions/38958888/react-native-what-is-the-benefit-of-using-stylesheet-vs-a-plain-object