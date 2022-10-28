---
title: "각도가 적용 된 사각형 도형의 각 꼭짓점 좌표 구하기"
author:
  name: 박 찬영
  link: https://github.com/univdev
date: 2022-09-26 21:04:00 +0900
categories: ["Tech", "Javascript"]
---
# 개요
![사각형의 각 꼭짓점][사각형]

회사 프로젝트를 진행하다가 어떤 영역에 배치 된 사각형이 어떤 각도로 회전했을 때, 그 사각형의 각 꼭짓점을 알아야만 하는 상황이 생겼어요.  
장시간의 리서칭과 사내 개발자분의 조언을 통해 완성한 소스코드입니다.

# 소스코드
```typescript
export const getRectPoint = (
  x: number,
  y: number,
  radian: number
): Point => {
  const left = (x) * Math.cos(radian) - (y) * Math.sin(radian) + p;
  const top = (x) * Math.sin(radian) + (y) * Math.cos(radian) + q;

  return { x: left, y: top };
};
```

위 소스코드는 아래 공식을 참고하여 만들어졌어요.

![공식1][공식1]  
![공식2][공식2]

> 회전 된 사각형의 `x` 좌표는 회전 되지 않은 사각형의 `x`를 `Math.cos(radian)`과 곱한 값과, 회전되지 않은 `y` 좌표의 값과 `Math.sin(radian)` 값을 곱하여 더한 값과 동일합니다.

> 회전 된 사각형의 `y` 좌표는 회전 되지 않은 사각형의 `-x`를 `Math.sin(radian)`과 곱한 값과, 회전되지 않은 `y` 좌표의 값과 `Math.cos(radian)` 값을 곱하여 더한 값과 동일합니다.

# 문제점
다만 이 소스코드는 회전축이 매번 `(0, 0)`으로 고정되어 있어요. 하지만 제가 필요로 하는 것은 회전축이 상황에따라 변경되어도 정상적으로 작동하는 로직이기에 기존 소스코드에서 약간 수정하여 해당 기능을 완성했어요.

# 완성 된 코드
```typescript
export const getRectPoint = (
  x: number,
  y: number,
  p: number,
  q: number,
  radian: number
): Point => {
  const left = (x - p) * Math.cos(radian) - (y - q) * Math.sin(radian) + p;
  const top = (x - p) * Math.sin(radian) + (y - q) * Math.cos(radian) + q;

  return { x: left, y: top };
};
```

> 기존에 `(0, 0)`을 기준으로 작성 된 코드에서, 회전축의 좌표를 추가로 입력 받을 수 있는 매개변수 `p`와 `q`를 추가했어요.

# 주의
**본 소스코드에 사용 된 회전 값은 `radian`이므로, `degree` 값을 넣을 시 정상 동작하지 않습니다!**

# 레퍼런스
[Stackoverflow - Get rotated rectangle points from x, y, width, height and rotation][레퍼런스]

[사각형]: https://i.stack.imgur.com/yGjc6.png
[공식1]: https://i.stack.imgur.com/ZcK0R.png
[공식2]: https://i.stack.imgur.com/TywRp.png
[레퍼런스]: https://stackoverflow.com/questions/38297082/get-rotated-rectangle-points-from-x-y-width-height-and-rotation