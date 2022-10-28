---
title: "Element가 서로 같은지 비교하기"
author:
  name: "박 찬영"
  link: "https://github.com/univdev"
date: 2022-10-28 09:56:00 +0900
categories: ["Tech", "Talk"]
tags: ["비교", "Element", "Javascript"]
---
# 개요
실제로 웹을 개발하다보면 DOM Element를 직접 핸들링 해야하는 일이 은근히 생깁니다.  
그럴 때, 내가 선택한 Element와 어떤 Element가 서로 동일한 Element인지 비교를 하고 싶을 때가 있는데, Javascript에서는 다행히도 `isEqualNode`와 `isSameNode` Method를 지원합니다.
# 사용해보기
## 코드
```typescript
const element1 = document.createElement('div');
const element2 = document.createElement('div');
const element3 = document.createElement('p');
const element4 = element1;

console.log(
  element1.isEqualNode(element2)
); // true

console.log(
  element1.isSameNode(element2)
); // false

console.log(
  element3.isEqualNode(element3)
); // false

console.log(
  element1.isSameNode(element4)
); // true
```
## isEqualNode
`isEqualNode`는 A Element와 B Element가 **서로 같은 이름의 태그인지 비교합니다.**  
서로 지칭하는 Element가 다르더라도 태그의 이름이 동일하다면 `true`를 반환합니다.
## isSameNode
`isSameNode`는 A Element와 B Element가 **완전히 동일한 대상을 지칭하는지 확인합니다.**  
단순히 태그 이름이 같을 뿐 아니라, Element의 `UniqueID`를 비교하여 아예 동일한 대상이라면 `true`를 반환합니다.
# 레퍼런스
[Stackoverflow - Is there a way to check if two DOM elements are equal?][레퍼런스]

[레퍼런스]: https://stackoverflow.com/questions/3649321/is-there-a-way-to-check-if-two-dom-elements-are-equal