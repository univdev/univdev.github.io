---
title: "ESLint - 'process' is not defined"
author:
  name: 박 찬영
  link: https://github.com/univdev
date: 2022-04-18 18:51:00 +0900
categories: [Tech, Talk]
---
# 이슈
```typescript
if (process.env.NODE_ENV === 'production') // ...
// 'process' is not defined
```
```process```가 일반적인 변수처럼 취급되어 해당 페이지 내 코드에서 정의 되어 있지 않다고 에러를 표시합니다.
# 해결법
```json
{
  "env": {
      "node": true,
  }
}
```
{: file=".eslintrc.json"}
```.eslintrc.json``` 파일 내 ```env``` 객체에 ```node``` 속성을 추가하면 해결 됩니다.
# 레퍼런스
[Stackoverflow - 'process' is not defined][레퍼런스]

[레퍼런스]: https://stackoverflow.com/questions/50894000/eslint-process-is-not-defined