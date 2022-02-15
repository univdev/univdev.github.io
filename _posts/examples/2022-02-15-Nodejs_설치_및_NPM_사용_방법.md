---
title: "NodeJS 설치 및 NPM 사용 예시"
author:
  name: 박 찬영
  link: https://github.com/univdev
date: 2022-02-15 14:42:00 +0900
categories: [Tech, NodeJS]
tags: [NodeJS]
---
# 개요
NodeJS는 Google이 제작한 Javascript 엔진인 V8 엔진을 이용하여 만들어진 Javascript 런타임입니다.  
본래 Javascript라고 하면 브라우저 상에서만 동작하여 홈페이지를 만들 때 제외하고는 아예 쓸 수가 없는 언어 중 하나였습니다.  
그런데 NodeJS라는 생태계가 생겨나면서 웹, 서버를 포함한 거의 대부분의 분야에서 Javascript를 사용할 수 있게 되었습니다.
# 설치 방법
[여기][NodeJS]에 들어가서 원하는 버전을 다운 받으시면 됩니다.  
안정적인 구동을 원하신다면 LTS 버전을, 최신 기능을 이용하고 싶다면 현재 버전을 다운받아주세요.

[NodeJS]: https://nodejs.org/ko/
# NPM
NodeJS를 설치할 때 ```npm```이라는 패키지 매니저도 같이 설치됩니다.
```npm```은 [NPM 커뮤니티][NPM]에 업로드 된 패키지를 프로젝트에 손쉽게 적용하기 위해 사용하는 툴입니다.
## 사용 예시
[Moment][Momentjs]라는 라이브러리가 있습니다.  
많은 Javascript 개발자들이 사용하는 날짜 계산 라이브러리인데, 이걸로 예제를 들어보겠습니다.
### 프로젝트 폴더 생성
```shell
mkdir my-first-npm
cd my-first-npm
npm init
```
위 명령어를 입력하면 ```my-first-npm```이라는 폴더를 생성하고, 이 폴더 내부를 ```npm```에 의해서 관리되는 프로젝트라고 선언하게 됩니다.  
```package.json```파일이 폴더 내부에 생기게 될텐데, 이는 아직 몰라도 됩니다.  
### 패키지 설치
```shell
npm i -S moment
```
이 명령어를 입력하면 해당 프로젝트 내부에서 ```moment``` 패키지를 사용할 수 있도록 다운로드를 받게 됩니다.  
### 코드 작성
폴더 내부에서 ```index.js``` 폴더를 생성하고 해당 파일에 아래와 같은 코드를 작성합니다.
```javascript
const moment = require('moment');

(() => {
  const now = moment().format('YYYY-MM-DD');
  const tomorrow = moment().add(1, 'day').format('YYYY-MM-DD');
  const yesterday = moment().add(-1, 'day').format('YYYY-MM-DD');

  console.log('now: ', now);
  console.log('tomorrow: ', tomorrow);
  console.log('yesterday: ', yesterday);
})();
```
{: file="my-first-npm/index.js" }
### 작성한 코드 실행
```shell
node index.js
```
{: file="명령어" }
터미널에서 위와 같은 명령어를 실행하면 오늘 날짜, 내일 날짜, 어제 날짜 순서대로 표시가 됩니다.
```shell
now: 2022-02-15
tomorrow: 2022-02-16
yesterday: 2022-02-14
```
{: file="결과" }
원래라면 날짜를 계산하고 포맷팅을 하기까지 상당량의 코드가 들어갔겠지만 이를 ```npm``` 생태계의 라이브러리를 이용한다면 매우 쉽게 구현할 수 있습니다.
# npm보다는 yarn
저는 패키지 매니저로 npm보다는 yarn을 주로 사용하기 때문에 yarn으로 설명 된 문서가 많습니다.  
문서의 흐름을 매끄럽게 따라가고 싶으시다면 아래 명령어를 입력하여 yarn을 설치해주세요!
```shell
npm i -g yarn
```

[NPM]: https://www.npmjs.com/
[Momentjs]: https://www.npmjs.com/package/moment