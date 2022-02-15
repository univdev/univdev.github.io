---
title: "Puppeteer 라이브러리를 이용한 웹 크롤러 제작 방법"
author:
  name: 박 찬영
  link: https://github.com/univdev
date: 2022-02-15 14:13:00 +0900
categories: [Tech, NodeJS]
tags: [Tech, Crawler, Puppeteer, Chromium]
---
# 웹 크롤러
개발자라면 한번 쯤 특정 사이트의 자료들을 자동으로 탐색하고 긁어와야 하는 경우가 생깁니다. (아마도...)  
예를 들어, 인기가 많은 어떤 쇼핑몰이 있습니다. 이 쇼핑몰은 신상품이 발매 될 때마다 공전의 히트를 치면서 신상품 공개 몇 분만에 매진이 되기 때문에 매 분 단위로 웹사이트를 봐야만 합니다. 내가 시간이 아주 많은 사람이라서 새로고침 신공을 할 수 있다면 정말 좋겠지만, 대부분은 그렇지 않죠.

이럴 때 사용하는게 웹 크롤러입니다. **웹 크롤러는 홈페이지의 소스코드를 읽는 봇이라고 보면 됩니다.**  
이러한 크롤러에게 타이머 모듈을 붙여서 사용하면 일정 주기마다 코드를 긁고, 이전에 긁었던 코드와 이번에 긁은 코드가 다를 때 알림을 울리게 만들면 쇼핑몰을 매 분마다 직접 읽을 필요가 없어지죠.
# Puppeteer
Puppeteer는 NodeJS 환경에서 웹 크롤러를 만들 때 많은 사람들이 이용하는 패키지입니다.  
Chromium을 사용하여 Headless 환경에서 특정 웹사이트에 방문, 제어 등을 가능하게 합니다. (물론 Headless 옵션을 끌 수도 있습니다.)
# Chromium
Chromium은 Google이 주도하는 웹 브라우저 오픈소스입니다.  
오늘날의 많은 브라우저는 이를 이용하여 제작되었습니다.
# Puppeteer 사용해보기
## Crawler 프로젝트 생성하기
```shell
mkdir my-first-crawler
cd my-first-crawler
npm init
```
위 명령어를 실행하여 ```my-first-crawler``` 폴더가 npm 프로젝트라고 선언합니다.
## 패키지 설치
```shell
yarn add puppeteer
```
위 명령어를 실행하여 Puppeteer 라이브러리를 설치하세요.
## Example 사이트의 스냅샷을 촬영하고 PDF로 만들어보기
### 파일 생성
방금 전에 만든 ```my-first-crawler``` 폴더 최상단에 ```index.js``` 파일을 생성합니다.  
### 코드 작성
```index.js``` 파일 안에 다음과 같이 코드를 작성합니다.
```javascript
const puppeteer = require('puppeteer');

(async () => {
  const browser = await puppeteer.launch();
  const page = await browser.newPage();
  await page.goto('https://example.com');
  await page.pdf({ path: 'hn.pdf', format: 'a4' });

  await browser.close();
})();
```
{: file="my-first-crawler/index.js" }
### 작성한 코드 실행하기
```shell
node index.js
```
### 결과
프로젝트 최상단에 ```hn.pdf``` 라는 파일이 생기게 되고, A4 사이즈로 ```https://example.com``` 페이지가 캡처되어 저장 된 것을 볼 수 있습니다.