---
title: "[React] 절대 경로 import"
author:
  name: 박 찬영
  link: https://github.com/univdev
categories: [Tech, React]
tags: [React]
---
# import 지옥
```javascript
import Component from '../../../../../../../components/BasicComponent';

// ...
```
React로 프로젝트를 개발하다보면 위와 같은 import 지옥이 되는 경우가 잦습니다.  
보통 실제 라우트 구조에 맞춰서 페이지 컴포넌트를 제작하기 때문에 ```depth```가 깊은 페이지에서 ```root``` 폴더까지 올라가려면 어쩔 수 없는 상황이예요.
# 절대경로 선언
## 절대경로란
절대경로는 이름 그대로 **어떤 상황에서도 변하지 않는 경로**를 의미합니다.  
import를 선언하는 페이지의 위치와는 상관 없이 일관성 있는 경로를 작성하기 때문에 추후 파일 위치가 이동해도 import를 다시 맞추지 않아도 되는 장점이 있어요.
## 방법
타입스크립트 프로젝트라면 ```tsconfig.json```, 자바스크립트 프로젝트라면 ```jsconfig.json``` 파일에 아래와 같이 작성합니다.
```json
{
  "compilerOptions": {
    "baseUrl": "src"
  },
  "include": ["src"]
}
```
절대 경로의 진입점을 ```compilerOptions/baseUrl```에 작성하시고, 불러 올 폴더를 ```include```에 작성하시면 됩니다. (보통은 ```src```를 사용하시니 그대로 쓰셔도 괜찮을 거예요.)  
위와 같이 작성하고나면 프로젝트에서 컴포넌트를 import할 때 아래와 같이 사용하실 수 있습니다.
```javascript
import Button from 'components/Button';
```
# 출처
[React - Importing a component][문서]

[문서]: https://create-react-app.dev/docs/importing-a-component/#absolute-imports