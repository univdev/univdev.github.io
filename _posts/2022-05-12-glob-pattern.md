---
title: "Glob 패턴"
author:
  name: 박 찬영
  link: https://github.com/univdev
date: 2022-05-12 10:32:00 +0900
categories: [Tech, Talk]
tags: [Glob]
---
# Glob 패턴
```Glob``` 패턴은 특정 경로, 특정 이름을 가진 파일들을 선택하기 위해 사용하는 패턴입니다.  
이는 **와일드카드** 문자로 이루어져 있습니다.
# 예시
```json
{
  "include": [
    "src/**/*.ts",
    "src/**/*.d.ts",
    "src/**/*.tsx",
    "src/**/*.vue",
    "src/types/*.d.ts"
  ]
}
```
{: file="tsconfig.json"}
타입스크립트 관련 명세를 작성하기 위한 ```json``` 파일을 예시로 들 수 있는데, 프로젝트에 포함할 ```type.d.ts``` 파일들을 자동으로 ```include```하기 위해 사용합니다.
# 패턴 예시
- ```*```
  - ```/```를 제외한 문자열과 매칭
  - ex) ```src/types/*/index.d.ts```
    - ```src/types/example01/index.d.ts```
    - ```src/types/example02/index.d.ts```
    - ```src/types/example03/index.d.ts```
- ```**```
  - ```/```를 포함한 문자열과 매칭
  - ex) ```src/types/**/index.d.ts```
    - ```src/types/example01/example012/index.d.ts```
    - ```src/types/example02/index.d.ts```
- ```?```
  - ```/```를 제외한 한 글자만 매칭
- ```[xyz]```
  - 괄호 안에 들어간 **각각의 글자**와 매칭
- {example01, example02, example03}
  - 괄호 안에 들어간 **각각의 문자열**과 매칭

# 모든 패턴
그 외 자세한 패턴은 [여기][패턴]를 참조해주세요.
# 레퍼런스
[Hugo.log - Glob 패턴과 정규표현식][레퍼런스]

[패턴]: http://www.jedit.org/users-guide/globs.html
[레퍼런스]: https://velog.io/@k7120792/Glob-%ED%8C%A8%ED%84%B4%EA%B3%BC-%EC%A0%95%EA%B7%9C%ED%91%9C%ED%98%84%EC%8B%9D
