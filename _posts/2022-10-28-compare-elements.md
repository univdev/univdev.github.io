---
title: "Element가 서로 같은지 비교하기"
author:
  name: "박 찬영"
  link: "https://github.com/univdev"
date: 2022-10-28 09:56:00 +0900
categories: ["Tech", "Javascript"]
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
`isEqualNode`는 A Element와 B Element가 **서로 같은 성질을 가지고 있는지 판단합니다.**  
여기서 같은 성질이란, 태그의 이름이나 속성, 하위에 존재하는 자식들이 동일한지에 따라 판단하게 됩니다.  
물론 자식을 보유할 수 없는 태그도 있고, 태그마다 허용하는 속성이 모두 다 다르니 노드의 유형에 따라 달라진다고 합니다.

> The specific set of data points that must match varies depending on the types of the nodes.

## isSameNode
`isSameNode`는 A Element와 B Element가 **완전히 동일한 대상을 지칭하는지 확인합니다.**  
단순히 태그 이름이 같을 뿐 아니라, Element의 `UniqueID`를 비교하여 아예 동일한 대상이라면 `true`를 반환합니다.
# 레퍼런스
[Stackoverflow - Is there a way to check if two DOM elements are equal?][레퍼런스]

[레퍼런스]: https://stackoverflow.com/questions/3649321/is-there-a-way-to-check-if-two-dom-elements-are-equal