---
title: "서버와 클라이언트의 우아한(?) 대화 방법, Adapter Pattern (Feat. FSD)"
author:
  name: "박 찬영"
  link: "https://github.com/univdev"
date: 2024-09-10 21:30:00 +0900
categories: [Tech, Talk]
tags: [Adapter Pattern, FSD, Design Pattern, 디자인 패턴]
image:
  path: /assets/post_images/2024-09-10-adapter-pattern/2024-09-10-18-33-03.png
  alt: 서버와 클라이언트의 우아한(?) 대화 방법, Adapter Pattern
---
![서버와 클라이언트의 우아한(?) 대화 방법, Adapter Pattern](/assets/post_images/2024-09-10-adapter-pattern/2024-09-10-18-33-03.png)

## 들어가며
우리는 프로젝트를 수행하면서 다양한 케이스를 마주합니다.

그 중 가장 만나고 싶지 않은 케이스 중 하나는 **Server가 제공하는 데이터의 Interface와 Client에서 사용할 수 있는 Interface가 너무나 상이한 경우**일 것입니다.

프론트엔드 직군만의 문제가 아니라 프로젝트를 진행하다보면 언젠가 반드시 한 번은 마주하는 개발자의 숙명과 같은 것인데 Interface를 수정하거나 BFF와 같은 추가적인 인프라를 구축하지 못하는 상황에서 사용하면 좋은 방법을 문제 사례와 함께 소개 하고자 합니다.

## 문제 발생 경위
문제 상황을 설명 드리기 전에 아래 문제를 이해하기 전에 상황 설명을 먼저 진행해야 할 것 같습니다.

여러 이해관계가 얽혀있고 비즈니스적인 요소도 섞여있어서 복잡한 관계는 배제하고 문제 상황을 간단하게 요약하자면

1. iOS App만 존재하는 서비스가 있습니다.
2. 해당 서비스는 서버에 데이터를 업로드 하지않고 기기에 저장하는 방식으로 구현 되어 있었습니다.
3. 해당 서비스의 **Web 버전 런칭을 하자는 안건이 나왔습니다.**
4. 내부 사정상 Back-End API 개발을 당장 할 수 없으니 Front-End 개발을 선행하고 Back-End를 나중에 개발하는 방향으로 가닥이 잡힙니다.
5. Front-End에서 미래에 개발 될 기능의 Interface를 유추하여 개발을 완료 했습니다.
6. 모바일 대응 목적으로 Back-End가 API 개발을 완료했습니다.
7. Front-End는 이후 Back-End에서 개발한 API를 확인해보니 **Front-End에서 즉각적으로 사용하기 어려운 Interface로 작성 되어 있었습니다. (모바일을 기준으로 만들어졌기 때문)**
8. Web 버전으로 API를 새로 제작하려면 시간이 걸리기에 일단 **모바일에 기준하여 작성 된 API를 우선 연동**해야 하는 상황입니다.
9. **BFF는 이후 API의 수정이 발생할 경우 관리 비용이 증가할 우려**가 있어 구현하지 않기로 결정했습니다.

> 읽으시면서 *“왜 이렇게 개발하지”* 싶은 느낌을 받으셨을 텐데, 회사 내부 상황을 기재하지 않는 선에서 설명한 내용이라 어색함을 느끼시더라도 양해 부탁드립니다 😢

## 문제 상황
Server에서는 다음과 같은 Interface로 이루어진 데이터를 제공합니다.

```tsx
{
	name: string;
	note: string;
	habitRepeat: {
		weeklyRepeatDays: number[] // enum [1, 2, 4, 8, 16, 32, 64]
	}
}
```

사용자의 ‘습관’을 개선하기 위해 `Habit`을 생성하고 특정 요일마다 이를 반복하는 기능에 필요한 데이터 구조입니다.

`weeklyRepeatDays` 프로퍼티는 해당 `Habit`을 무슨 요일마다 반복할 것인지에 대한 Spec입니다.

웹 개발자의 도메인 지식으로는 `0~6` 혹은 `1~7` 사이 숫자를 배열로 포함하여 ‘어떤 요일에만 반복할 것인지’를 결정한다고 이해할 수 있겠습니다만, 문제는 해당 프로퍼티에 삽입되는 숫자의 규칙이 `1, 2, 4 … 64`로 이루어진 등비 수열이였습니다.

도대체 이건 무슨 규칙인가 싶어 개발자에게 문의 해보니

![백엔드 개발자와의 대화](/assets/post_images/2024-09-10-adapter-pattern/2024-09-10-21-18-56.png)

> “왜 그런지 아무도 몰라. iOS에서는 저런식으로 요일 Format을 넣더라고. Web API를 만들 때 이것도 고쳐줄까?”

라고 답변이 왔습니다 😱

## 솔루션 정의
그렇다면 제가 해야 할 일은 명확합니다.

1. Server Interface에서 넘어오는 등비수열이 각각 일 ~ 토 요일과 매칭 된다는 사실은 확실합니다.
2. 웹에서 개발한 컴포넌트에 맞게 이를 0~6 사이 숫자로 매칭하면 됩니다.

### Adapter
Adapter는 우리가 흔히 **해외여행 갈 때 사용하는 돼지코를 의미하는 그 어댑터가 맞습니다.**

데이터의 Format을 환경에 맞게 변환하는 모습이 마치 전압을 바꾸기 위해 어댑터를 사용하는 모습과 비슷하다고 해서 지어진 이름이라 추측 해봅니다 🤔

### 문제 해결하기 (Feat. FSD)
현재 개발 하고 있는 프로젝트는 [FSD 아키텍처를](https://emewjin.github.io/feature-sliced-design/) 사용하고 있습니다.
*(언젠가 FSD 아키텍처에 대해 문서로 작성하겠습니다. 많은 기대 부탁드려요* 😄*)*

```
src
  ㄴ features
    ㄴ Habits
      ㄴ helpers
        ㄴ HabitsIOSAdapter.helper.ts
  ㄴ entities
    ㄴ Habits
      ㄴ types
        ㄴ Habits.types.ts
  ㄴ shared
    ㄴ types
      ㄴ Adapter.types.ts
```

디렉토리 구조는 위와 같습니다.

여기서 주목해야 할 파일은

- `Habits.types.ts`
- `HabitsIOSAdapter.helper.ts`
- `Adapter.types.ts`

입니다.

우선 Adapter의 구현체를 작성하기 전에 Interface를 먼저 정의하겠습니다.

```tsx
// shared/types/Adapter.types.ts

export interface Adapter<I, O> {
	adapt(input: I, ...args: any[]): O
}
```

`Adapter.types.ts` 파일에 앞으로 사용할 모든 Adapter의 Interface를 작성합니다.

`...args` 는 추후 `adapt` 함수를 실구현 할 때 ‘Interface 변경에 필요한 추가적인 데이터’를 매개변수로 전달 받아야 하는 상황에서 확장이 가능하도록 넣어둔 매개변수입니다.

위 Interface를 토대로 실제 구현체는 아래와 같이 구현할 수 있습니다.

```tsx
// features/Habits/helpers/HabitsIOSAdapter.helper.ts

export class HabitIOSAdapter implements Adapter<IHabitIOS, IHabitWeb> {
	public adapt(input: IHabitIOS, otherDependencies: string[]) {
		return {
			// Interface를 변경하는 로직
		};
	}
}
```

만약 Interface를 변경하는 로직을 구현하는 과정에서 추가적인 함수 구현이 필요한 경우, 일반적인 Method 구현 방식을 사용하시면 됩니다.

```tsx
// features/Habits/helpers/HabitsIOSAdapter.helper.ts

export class HabitIOSAdapter implements Adapter<IHabitIOS, IHabitWeb> {
	public adapt(input: IHabitIOS, otherDependencies: string[]) {
		return {
			// Interface를 변경하는 로직
		};
	}
	
	private convertWeeklyRepeatDays() {
		return // ...
	}
}
```

추가적으로 `IHabitIOS` 와 같은 Interface는 `entities` 레이어에 있는 `Habits.types.ts`에 작성 해주시면 됩니다.

```tsx
// entities/Habits/types/Habits.types.ts

export type IHabitIOS = {
	// ...
}

export type IHabitWeb = {
	// ...
}
```

## 무슨 이점이 있을까?
Adapter Pattern을 FSD Architecture와 함께 사용한 예시를 보여드렸습니다.

이렇게 정리하면 크게 두가지 이점이 있습니다.

1. 파일의 흐름이 `shared` → `entities` → `features` 와 같이 단방향으로 흐르기 때문에 파일간 참조 관계가 명확해집니다.
2. `Adapter` 클래스를 통해 변환 로직을 한 파일에 집중할 수 있으므로 추후 Interface를 수정하는 등의 작업을 할 때 수정해야 하는 파일이 명확하여 작업 비용이 매우 저렴해집니다.

## 마치며
어쩌다보니 Adapter Pattern과 함께 FSD까지 같이 설명드리게 되었는데요.  
*‘은총알은 없다’* 라고들 하지만 상당히 많은 프로젝트에 사용할 수 있는 공통적인 패턴은 존재하기에 ‘디자인 패턴’의 존재가 성립할 수 있는거겠죠.

FSD는 요즘 제가 실무에서 애용하고 있는 아키텍처입니다.  
언젠가 기회가 되면 FSD에 대해서도 작성해보도록 하겠습니다.  
만약 그 때가 되면 이 글 마지막에 링크를 첨부하겠습니다.

긴 글 읽어주셔서 감사합니다! 👏