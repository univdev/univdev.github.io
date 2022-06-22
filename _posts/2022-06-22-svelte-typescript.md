---
title: "Svelte에 적용하는 Typescript"
author:
  name: 박 찬영
  link: https://github.com/univdev
date: 2022-06-22 15:59:00 +0900
categories: [Tech, Svelte]
tags: [Svelte, Typescript]
---
# 개요
이번에 토이프로젝트를 제작하기 위해 Sapper + Typescript를 사용해보기로 결정했습니다.  
원래는 Nuxt3를 써보려 했는데 아직 정식 릴리즈가 되지 않아 Sapper가 좀 더 끌리더라구요.
# Sapper란?
Svelte에 서버 엔진을 붙인 프레임워크입니다.  
React의 Next.js, Vue의 Nuxt를 생각해보시면 될 것 같아요.
# 설치
## 패키지 설치
**npm**
```
npm i --D svelte-preprocess typescript
```
**yarn**
```
yarn add -D svelte-preprocess typescript
```
## tsconfig.json 추가
```json
{
    "include": ["src/**/*"],
    "exclude": ["node_modules/*"],
    "compilerOptions": {
        "target": "es2015",
        "module": "es2015",
        "types": ["svelte"]
    }
}
```
{: file="tsconfig.json"}
## rollup.config.js 편집
```javascript
// ...
import autoPreprocess from "svelte-preprocess"; // add this

export default {
    client: {
        plugins: [
            svelte({
                preprocess: autoPreprocess(), // add this

    // ...

    server: {
        plugins: [
            svelte({
                preprocess: autoPreprocess(), // add this
                // ...
            }),
    // ...
```
{: file="rollup.config.js"}
## d.ts 추가
```typescript
declare module '*.jpg';
```
{: file="types/index.d.ts"}
# 출처
[Use Typescript with svelte sapper][출처]

[출처]: https://dev.to/mhaecker/use-typescript-with-svelte-sapper-45n8