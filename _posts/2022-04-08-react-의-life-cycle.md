---
title: 'React의 Life Cycle'
author:
  name: 박 찬영
  link: https://github.com/univdev
date: 2022-04-08 18:22:00 +0900
categories: [Tech, React]
tags: [React, Lifecycle]
---
# 개요
라이프사이클, 즉 생명주기라는 단어는 Vue나 React와 같은 프론트엔드 개발 도구를 사용해본 경험이 있다면 누구나 한 번 쯤 들어봤을 단어입니다.  
그만큼 해당 개발 도구를 사용하는데 중요한 역할을 하며, 이 부분을 잘 알고 있어야 좋은 서비스를 개발할 수 있습니다.
# Lifecycle
Vue나 React는 컴포넌트 단위로 아주 작게 기능들을 쪼개서 개발을 하는데, **컴포넌트가 생겨나면서 사라지기까지의 모든 과정을 Lifecycle이라고 합니다.**  
## React
React의 생명주기의 경우 세 단계로 구분 됩니다.  
### Mount
컴포넌트가 렌더링 된 단계를 ```Mount```라고 합니다.  
#### 클래스 컴포넌트
```javascript
class Component {
  componentDidMount() {
    // 로직
  }
}
```
클래스 컴포넌트의 경우, ```componentDidMount```라는 메소드로 해당 단계에 실행할 로직을 작성합니다.  
#### 함수형 컴포넌트
```javascript
useEffect(() => {

}, []);
```
함수형 컴포넌트의 경우, ```useEffect``` 함수를 이용합니다.  
두번째 매개변수로 ```[]```를 전달하여, 해당 컴포넌트가 탄생하는 순간에만 실행하도록 구현합니다.
### Update
컴포넌트가 렌더링 된 이후, ```state```나 ```props``` 등의 변경으로 인하여 새로 컴포넌트를 렌더링 하는 단계를 ```Update```라고 합니다.
#### 클래스 컴포넌트
```javascript
class Component {
  componentDidUpdate() {
    // 로직
  }
}
```
컴포넌트가 갱신 될 때 실행할 로직을 ```componentDidUpdate```에 작성합니다.
### Unmount
컴포넌트가 완전히 소멸하는 단계를 ```Unmount```라고 합니다.
#### 클래스 컴포넌트
```javascript
class Component {
  componentWillUnmount() {

  }
}
```
클래스 컴포넌트는 해당 단계에 실행할 로직을 ```componentWillUnmount```라는 메소드에 작성합니다.
#### 함수형 컴포넌트
```javascript
useEffect(() => {
  // 로직
  return () => {
    // Unmount 때 실행할 로직
  };
}, []);
```
기본적으로 함수의 형태는 ```Mount``` 단계와 동일하지만, ```Unmount``` 때 실행할 로직을 담은 함수를 ```return```한다는 점에서 차이가 생깁니다.