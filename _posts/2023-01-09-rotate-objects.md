---
title: "도형의 각도를 회전하는 방법"
author:
  name: 박 찬영
  link: https://github.com/univdev
date: 2023-01-16 18:00:00 +0900
categories: [Tech, Talk]
tags: [React, Next.js, Talk]
---
# 들어가며
![이미지 편집 서비스 Canva](/assets/post_images/2023-01-09-rotate-objects/%E1%84%92%E1%85%AA%E1%84%86%E1%85%A7%E1%86%AB%20%E1%84%80%E1%85%B5%E1%84%85%E1%85%A9%E1%86%A8%202023-01-09%20%E1%84%8B%E1%85%A9%E1%84%8C%E1%85%A5%E1%86%AB%2010.37.55.gif)
_이미지 편집 서비스 Canva_

다중 오브젝트를 한 번에 회전 시키려고 하면 오브젝트의 각도와 위치가 동시에 변경 됩니다.  
본 문서에서는 위 기능을 구현하는 방법에 대해 기술합니다.

# 회전 각도 구하기
우선 두 점 사이의 각도를 구하는 함수에 대해 정의 합니다.

```typescript
interface Point {
	x: number;
	y: number;
}

const getAngleTwoPoints = (p1: Point, p2: Point) => {
  const { x: _x, y: _y } = p1;
  const { x: __x, y: __y } = p2;
  const degree = Math.atan2(__y - _y, __x - _x) * 180 / Math.PI;
  
  return degree;
};
```

x와 y의 number 값을 가지고 있는 interface인 Point를 정의하고 매개변수로 두 개의 Point를 전달 받습니다.

전달 받은 두 점의 좌표를 이용하여 degree 값을 추출하려면 `Math.atan2(y2 - y1, x2- x1) * 180 / Math.PI` 공식을 사용하여 계산하면 됩니다.

# Javascript로 회전 구현하기
우리가 만들 프로젝트에서 오브젝트를 회전 시키기 위한 스토리라인을 생각해봅시다.

1. 사용자는 오브젝트를 선택한다.
2. 오브젝트를 회전 시킬 수 있는 UI가 나타난다.
3. 위에서 말하는 UI란 둥근 버튼을 말하며, 이를 누른 다음 마우스를 움직이면 원하는 방향으로 오브젝트가 회전한다.

아마 대부분의 편집 서비스에서 회전 기능을 이런 식으로 구현할 것이라고 생각 됩니다.

우리에게는 이미 편집 가능한 오브젝트와 이를 회전 시킬 수 있는 UI가 구현 된 상태라고 가정합니다.

![편집](/assets/post_images/2023-01-09-rotate-objects/20230109153239.png)

## 좌표 변수 정의하기
우리는 편집을 진행할 때 세 가지의 좌표 변수를 사용할 예정입니다.

```typescript
const startPoint = useRef<Point>();
const movedPoint = useRef<Point>();
const originPoint = useRef<Point>();
const startAngle = useRef<number>(0);
```

`startPoint`는 마우스를 누른 시점에 확보한 마우스의 좌표입니다.

`movedPoint`는 마우스를 누른 상태에서 이동 하면서 지속적으로 변경 되는 마우스의 좌표입니다.

`originPoint`는 내가 선택한 오브젝트들이 위치 된 좌표 지점을 기준으로 정확히 가운데를 지칭하는 x와 y값으로 구성 된 좌표입니다.

`startAngle`은 회전을 시작할 때  `originPoint`의 회전각을 구해서 저장합니다.

## 회전 시작하기
회전을 시작한다는 것은 사용자가 둥근 버튼에 마우스를 올리고 버튼을 누른 시점을 의미합니다.

```typescript
const startRotate: React.MouseEventHandler<SVGCircleElement> = (event) => {
	const x = event.clientX;
	const y = event.clientY;
	const point = { x, y };

	startPoint.current = point;
	movedPoint.current = point;
	originPoint.current = getOriginPoint(...);

	startAngle.current = getAngleTwoPoints(originPoint.current, startPoint.current);
};
```

위 함수를 `mousedown` 이벤트의 콜백 함수로 사용하면 x, y 좌표 값을 얻을 수 있습니다.

얻어낸 좌표 값을 `startPoint`와 `movedPoint` 에 할당 합니다.

`originPoint`는 내가 선택한 모든 오브젝트들이 차지하는 좌표 범위를 하나의 큰 사각형으로 생각하고 해당 사각형의 가로와 세로 길이를 이등분하여 Point 객체로 만든 좌표 값입니다.

![OriginPoint 좌표값](/assets/post_images/2023-01-09-rotate-objects/%E1%84%89%E1%85%B3%E1%84%8F%E1%85%B3%E1%84%85%E1%85%B5%E1%86%AB%E1%84%89%E1%85%A3%E1%86%BA%202023-01-09%20%E1%84%8B%E1%85%A9%E1%84%92%E1%85%AE%201.45.02.png)

다시 말해, 위 빨간 동그라미 영역의 좌표가 `originPoint` 가 되는 겁니다.

## 회전 진행하기
마우스가 눌리는 이벤트를 통해 좌표 값을 전달 받고 저장 했으니 이제는 마우스 이동 이벤트를 이용하여 이동 각도를 계산하고 실제로 회전을 해보겠습니다.

```typescript
const rotating = (event: MouseEvent) => {
	const x = event.clientX;
	const y = event.clientY;
	const point = { x, y };

	movedPoint.current = point;

	const angle = getAngleTwoPoints(originPoint.current, movedPoint.current) - startAngle.current;
 };
```

위 함수를 `mousemove` 이벤트의 콜백 함수로 할당합니다.

마우스가 이동 할 때마다 현재 마우스의 좌표를 받아오고, 위와 같은 원리로 각도를 불러올 수 있습니다.

회전을 시작할 때 `startAngle`을 저장하여 현 시점에 활용하는 이유는, `event.clientX`와 `event.clientY`의 경우 마우스가 회전 버튼을 눌렀을 때 회전 버튼이 위치한 좌표를 뽑아오는게 아니라 내가 누른 화면의 좌표 정보를 받아오는 것이기 때문에 버튼의 왼쪽 상단을 눌렀을 때와 우측 하단을 눌렀을 때의 각도 차이가 생기게 됩니다.

이를 방지하기 위해 회전을 시작 할 때의 각도를 미리 구해서 회전 시 제거하면 초기 값을 0도로 초기화 할 수 있습니다.

위 방식을 통해 구한 각도를 사용하여 각 오브젝트가 기존에 갖고 있었던 각도 값에서 새로 구해진 각도를 더하면 오브젝트의 각도 회전을 구현할 수 있습니다.

## 오브젝트 이동 시키기
회전은 상대적으로 간단하게 진행 되었는데, 가장 중요한건 중심점을 기준으로 위치가 회전하도록 구현해야 한다는 것입니다.

이 기능을 구현하기 위해서는 어떤 좌표에 찍힌 꼭짓점이 중심축을 기준으로 회전 했을 때 어디로 이동하게 되는 지 알아야 할 필요가 있어요.

![x' 구하는 공식](/assets/post_images/2023-01-09-rotate-objects/20230109155018.png)  
![y' 구하는 공식](/assets/post_images/2023-01-09-rotate-objects/20230109155039.png)

위 공식을 사용하면 이동 된 x’와 y’를 구할 수 있습니다.

```typescript
const left = (x - p) * Math.cos(radian) - (y - q) * Math.sin(radian) + p;
const top = (x - p) * Math.sin(radian) + (y - q) * Math.cos(radian) + q;
```

소스코드로 표현하면 위 처럼 나타낼 수 있습니다.

공식에 대입 할 x와 y는 오브젝트가 위치할 좌표인데, 이 점은 각 오브젝트가 가진 각도값에 영향을 받아서는 안 되기에 중심점을 사용해야 합니다.

p와 q는 회전축의 x, y 좌표입니다.

주의할 점은 `Math.cos`와 `Math.sin` 함수에  들어갈 각도는 Degree 값이 아닌 Radian 값이기 때문에 한 번 변환이 필요합니다.

```typescript
const degree2Radian = (degree: number) => {
  return Math.PI * degree / 180;
};
```

위 함수를 사용하면 Degree 값을 Radian으로 변환 할 수 있습니다.

# 마치며
![완성](/assets/post_images/2023-01-09-rotate-objects/%E1%84%92%E1%85%AA%E1%84%86%E1%85%A7%E1%86%AB%20%E1%84%80%E1%85%B5%E1%84%85%E1%85%A9%E1%86%A8%202023-01-09%20%E1%84%8B%E1%85%A9%E1%84%92%E1%85%AE%202.26.28.gif)  
위 과정을 통해 회전각과 이동할 좌표를 구했으니 실제 오브젝트에 반영을 하면 아래와 같은 회전 기능을 만들 수 있습니다.