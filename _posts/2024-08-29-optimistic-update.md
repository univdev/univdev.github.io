---
title: "사용자 경험 증대를 위한 낙관적 업데이트 (feat. React Query)"
author:
  name: "박 찬영"
  link: "https://github.com/univdev"
date: 2024-08-29 14:57:00 +0900
category: [Tech, Talk]
tags: ["React Query", "Optimistic Update", "낙관적 업데이트", "UX"]
image:
  path: /assets/post_images/2024-08-29.optimistic-update/2024-08-29-14-52-26.png
  alt: 낙관적 업데이트
---
![낙관적 업데이트](/assets/post_images/2024-08-29-optimistic-update/2024-08-29-14-52-26.png)

# 낙관적 업데이트 (Optimistic Update)
낙관적 업데이트는 서버 혹은 외부 디펜던시에 의존하는 업데이트 요청이 *‘반드시 성공할 것’* 이라는 믿음 하에 구현하는 업데이트 방식입니다.

# 비동기 요청의 문제점
주로 외부 디펜던시에 의존하는 업데이트 방식 (게시글의 작성 혹은 수정)은 비동기 요청으로 진행합니다.  
이렇게 처리하는 요청의 경우 응답이 돌아올 때 까지 짧게는 수 ms에서 부터 길게는 10s가 넘는 시간이 소요 될 수 있습니다.

이런 경우 대부분의 프론트엔드 개발자는 이러한 비동기 요청이 수행되는 과정에서 사용자가 불편함을 느끼지 않도록 Loading Indicator 혹은 Skeleton UI 등의 요소로 *“저 지금 열심히 당신의 요청을 처리하고 있어요”* 라고 어필을 해야 합니다.  
하지만 이러한 로딩 방식은 뭔가 조금 아쉽습니다.  
Skeleton UI나 Loading Indicator 방식은 작업 상황이 업데이트 중임을 알려줄 수는 있어도 결국 작업 상황이 완료되지 못한 상태이기 때문에 **유의미한 결과를 반영하진 않기 때문입니다.**

# 낙관적 업데이트의 Principle
낙관적 업데이트를 구현하기 위한 원칙(Principle)은 ‘사용자가 낙관적 업데이트를 수행할 수 있는 정보를 제공하느냐’가 관건입니다.

예를 들자면 사용자가 게시글을 작성하는 경우 아래와 같은 Payload를 제공할 것입니다.

```tsx
{
	title: string;
	content: string;
	files: (File | string)[];
}
```

위와 같이 사용자가 제공한 정보는 게시글 상세 페이지와 목록 페이지를 구현하는데 있어 가장 핵심적인 데이터를 제공하고 있습니다.

낙관적 업데이트는 사용자가 요청을 보낼 때 제공한 데이터를 기반으로 **‘서버에 반영되지 않은 가짜 데이터’를 UI에 선반영** 하는 방식으로 구현하기에, 사용자가 보낸 데이터가 UI를 구성하는데 부족함이 없는지 우선적으로 판단해야 합니다.

# React Query를 이용하여 간단하게 적용해보자

## useQuery의 동작 원리
우리는 React에서 서버 및 외부 디펜던시와 요청을 주고 받을 때 [React Query](https://tanstack.com/query/latest/docs/framework/react/overview)를 주로 사용합니다.

React Query는 이러한 쓰임에 맞게 낙관적 업데이트를 쉽게 구현할 수 있는 여러 툴킷을 제공하기에 직접 해당 기능을 구현하는 것 보다 매우 쉽게 낙관적 업데이트를 구현할 수 있습니다.

일반적으로 어떤 데이터를 Fetching 할 때 다음과 같은 코드를 작성할 것입니다.

```tsx
const usePostListQuery = () => {
  const query = useQuery({
	  queryKey: ['post', 'all'],
	  queryFn: () => ...,
	  staleTime: 1000 * 60 * 5, // 5분 간격으로 데이터를 Refetch
  });
}
```

React Query는 In-memory Cache를 구현하기 위해 Query Client라는 객체를 앱 전역적으로 공유하여 사용합니다.

다시 말해, `useQuery` 훅을 이용하여 캐싱한 데이터는 Query Client 내부에 저장되어 앱 전역적으로 공유되고 있다는 뜻입니다.

`useQuery`를 사용할 때 필수적으로 제공하는 프로퍼티인 `queryKey`는 이러한 캐싱 메커니즘에서 데이터를 저장하는 위치를 지칭할 때 사용하기 때문에 이를 이용하여 데이터 업데이트 요청 시 인위적으로 Query Client에 적용 된 데이터를 수정하는 것이 가능합니다.

낙관적 업데이트는 위에서 설명한 기능을 토대로 캐싱 된 데이터를 인위적으로 수정하여 UI에 반영하는 방식입니다.

## useMutation에서 인위적으로 데이터 수정하기
React Query를 사용하여 서버 데이터를 업데이트 할 때 `useMutation` 훅을 사용합니다.

```tsx
const useCreatePostMutation = () => {
	const mutation = useMutation({
		mutationFn: (payload) => ...,
	});
}
```

`useMutation` 훅에는 `onMutate` `onSettled` `onSuccess` `onError`와 같이 다양한 이벤트를 핸들링할 수 있도록 Callback을 전달할 수 있는 프로퍼티를 지원합니다.

- `onMutate` - 요청 시작
- `onSettled` - 응답 결과에 상관 없이 요청이 종료되면 발생
- `onSuccess` - `mutationFn`에 전달한 함수의 결과가 resolve일 경우
- `onError` - `mutationFn`에 전달한 함수의 결과가 reject일 경우

낙관적 업데이트의 경우 ‘UI의 선반영’ 을 목적으로 하기 때문에 `onMutate` 를 이용합니다.

코드로 작성하면 아래와 같습니다.

```tsx
const useCreatePostMutation = () => {
	const queryClient = useQueryClient();

	const mutation = useMutation({
		mutationFn: (payload) => ...,
		onMutate: (_, payload) => {
			queryClient.getQueryCache().findAll({ queryKey: ['post', 'all'] })
				.map((cache) => {
					const data = queryClient.getQueryData(cache.queryKey);
					queryClient.setQueryData(cache.queryKey, { data: [...data, { ...payload }] });
				});
		},
	});
}
```

`useQueryClient` 훅을 사용하면 Query Client 바운더리 내부에서 Query Client에 대한 정보를 호출할 수 있습니다.

Query Client 인스턴스에는 `getQueryCache` `getQueryData` `setQueryData` 등 현재 캐싱 데이터 맵을 제어할 수 있는 여러 메소드가 존재합니다.

`getQueryData`와 `setQueryData`는 `queryKey`  프로퍼티를 통해 저장 된 데이터의 읽기 및 쓰기가 가능하지만 `queryKey`가 정확히 기입 된 데이터에 대해서만 대응이 가능하기 때문에 `getQueryCache` 메소드를 통해 `queryKey`에 대한 검색을 선행하고, 검색 된 Cache에서 Query Key를 직접 호출하여 제어하는 방식으로 사용합니다.

## 낙관적 업데이트 구현 방법 정리
1. `['post', 'all', { perPage: 100, page: 1 }]` 과 같은 형태로 기입 된 캐시 데이터의 경우 Query Key를 온전히 동일하게 삽입하여 수정하는게 사실상 불가능함
2. 그렇기에 `getQueryCache` 메소드를 사용하여 `['post', 'all']` 로 시작하는 Query Key를 검색하여 매칭 된 전체 캐시를 불러옴
3. `.forEach`로 반복하여 해당 캐시의 Query Key를 정확히 불러온 뒤 `getQueryData` 및 `setQueryData`로 캐싱 된 데이터를 수정함 

## 네트워크 요청이 실패한다면?
낙관적 업데이트의 가정 자체가 ‘반드시 성공할 것’ 이기 때문에 디펜던시의 안정성을 신용하여 업데이트를 선행합니다.  
하지만 디펜던시를 절대적으로 신용하는 것은 매우 위험한 행동이기에, **요청이 실패할 경우에 대한 예외 처리를 반드시 구현해야 합니다.**

해결책이야 다양하겠지만 가장 많이 쓰는 방식은 *‘선반영한 데이터를 원래 데이터로 돌려놓는 방식’* 이기에 저 역시 이 방법을 사용하겠습니다.  
저는 해당 작업을 ‘롤백(Rollback)’이라고 표현 하겠습니다.

### 롤백 기능 구현 방법
`useMutation` 훅의 `onMutate`와 `onSuccess` `onSettled` `onError` 이벤트는 데이터를 단방향으로 전송할 수 있습니다.

`onMutate`에서 어떤 데이터를 Return 하면  `onSettled` `onSuccess` `onError`의 세번째 매개변수에서 해당 Return 값을 받아오는 것이 가능합니다.

```tsx
 const useCreatePostMutation = () => {
	const queryClient = useQueryClient();

	const mutation = useMutation({
		mutationFn: (payload) => ...,
		onMutate: (_, payload) => {
			const previousPostListData = new Map<QueryKey, Data>();
		
			queryClient.getQueryCache().findAll({ queryKey: ['post', 'all'] })
				.map((cache) => {
					const data = queryClient.getQueryData(cache.queryKey);
					queryClient.setQueryData(cache.queryKey, { data: [...data, { ...payload }] });
					previousPostListData.set(cache.queryKey, data);
				});
				
			return {
				previousPostListData,
			};
		},
	});
}
```

위 로직은 `previousPostListData` 라는 Map 변수를 `onMutate`에서 Return 하는 로직이 추가 되었습니다.

낙관적 업데이트를 수행하는 도중 `.forEach` 내부에서 기존 데이터를 Map에 삽입하고 `onMutate` 의 반환 값으로 사용합니다.

최종적으로는 `onSettle` `onError` `onSuccess` 에서 다음과 같이 받아올 수 있습니다.

```tsx
 const useCreatePostMutation = () => {
	const queryClient = useQueryClient();

	const mutation = useMutation({
		mutationFn: (payload) => ...,
		onMutate: (_, payload) => {
			const previousPostListData = new Map<QueryKey, Data>();
		
			queryClient.getQueryCache().findAll({ queryKey: ['post', 'all'] })
				.map((cache) => {
					const data = queryClient.getQueryData(cache.queryKey);
					queryClient.setQueryData(cache.queryKey, { data: [...data, { ...payload }] });
					previousPostListData.set(cache.queryKey, data);
				});
				
			return {
				previousPostListData,
			};
		},
		onError: (_, __, ctx) => {
			if (ctx?.previousPostListData) {
				// ...
			}
		}
	});
}
```

`onError` Callback의 세번째 매개변수로 `previousPostListData` 를 받아올 수 있습니다.

언제나 유효하게 존재하는 값이 아닐 수 있기 때문에 `if`문 등을 사용하여 유효한 값인지 검사를 진행한 후 롤백 로직을 구현하면 됩니다.

```tsx
{
	// ... useMutation 로직들
	onError: (_, __, ctx) => {
		if (ctx?.previousPostListData) {
			ctx.previousPostListData.forEach((value, queryKey) => {
				queryClient.setQueryData(queryKey, value);
			});
		}
	}
} 
```

롤백 로직은 위와 같이 구현 하실 수 있습니다.

`queryKey` 와 이전 데이터를 Key-Value 쌍으로 가진 Map을 반복하여 `queryClient`의 데이터를 Set 하는 방식으로 아주 간단히 롤백 로직을 구현 하실 수 있습니다.

## 데이터 최신화
아마 눈치가 빠르신 분이라면 사용자가 기입한 데이터를 선반영 하는 작업만 수행해서는 부족하다는 것을 눈치 채셨을 겁니다.

그 이유는 사용자가 데이터를 기입할 때 **데이터의 PK를 같이 전달하지는 않기 때문입니다.**

대부분의 경우 서버에 저장 된 데이터를 읽거나 새로 쓰기 위해서 PK를 사용합니다.  
그렇기 때문에 프론트엔드에서 게시물 상세 조회 페이지의 라우트를 구현할 때 `/pages/a40efAC32` 와 같이 ‘조회하고 있는 데이터의 PK’ 를 `path` 혹은 `SearchParams`로 전달하여 데이터를 호출합니다.

위에서 작성한 선반영 로직만을 기입하면 해당 데이터가 서버에 어떤 PK로 등록 되었는지 알 수 있는 방법이 없기 때문에 선반영 이후 **네트워크에서 응답이 돌아오면 최신 데이터로 갱신하는 작업이 수반 되어야 합니다.**

React Query는 이런 케이스도 아주 간단히 처리할 수 있도록 도와줍니다.

```tsx
{
	// ...useMutate 로직
	onSuccess: () => {
		queryClient.invalidateQueries({ queryKey: ['post', 'all'] });
	}
}
```

위 코드처럼 단 한줄만 추가하면 기존 캐싱 된 데이터를 모두 Stale 상태로 변경하여 해당 데이터를 사용하는 페이지에 방문할 때 Fetch를 진행합니다.

위에서 사용한 `setQueryData` 혹은 `getQueryData`와 다른점은 Query Key가 부분 매칭 되어도 모두 만료 처리 하기 때문에 `getQueryCache` 메소드로 모든 캐시 데이터를 검색할 필요가 없다는 점입니다.

# 마치며
이번 문서에서는 Loading Indicator나 Skeleton UI보다 훨씬 진보 된 로딩 화면을 구현하는 방법에 대해서 알아 봤습니다.

저도 비교적 최근에 낙관적 업데이트를 도입해 봤는데 프론트엔드에서 UX를 개선하는 방법은 공부 하면 할수록 다양하다는 것을 느낍니다.

이 문서를 통해 여러분도 저와 같은 감상을 느끼고, 낙관적 업데이트를 도입 해보시면서 보다 나은 사용성의 앱을 세상에 많이 공개 해주시면 더할 나위 없이 기쁠 것 같습니다.

감사합니다.