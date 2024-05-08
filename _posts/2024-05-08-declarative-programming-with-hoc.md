---
title: "HOC와 UI 컴포넌트를 결합한 선언적인 비즈니스 컴포넌트"
categories: [Tech, React]
tags: [React, HOC, Business Component, Component, UI Component, UI, Refactoring]
author:
  name: 박 찬영
  link: https://github.com/univdev
date: 2024-05-08 10:44:00 +0900
image:
  path: /assets/post_images/2024-05-08-declarative-programming-with-hoc/2024-05-08-11-00-56.png
  alt: HOC와 UI 컴포넌트를 결합한 선언적인 비즈니스 컴포넌트
---
![HOC와 UI 컴포넌트를 결합한 선언적인 비즈니스 컴포넌트](/assets/post_images/2024-05-08-declarative-programming-with-hoc/2024-05-08-11-00-56.png)

UI 컴포넌트와 비즈니스 컴포넌트를 서로 적절한 수준에서 분리하여 작업하는 것은 저에게 있어 가장 큰 숙제이자 난관이었습니다.  
비교적 최근까지도 저는 여러가지 고민과 시도를 반복하며 보다 나은 코드와 파일 구조를 작성하는데 하루의 대부분의 시간을 보냈습니다.

- 동일한 UI 컴포넌트를 바라보는 수많은 Container 컴포넌트를 어떻게 관리해야 할까?
- UI 컴포넌트가 꼭 필요할까? (재사용성 안높아보이는건 일단 Container로 만들고 나중에 분리할까?)

이와 같은 고민들을 수차례 반복하며 점점 더 복잡해져만 가는 파일 구조, 코드 복잡도와 전쟁을 벌이고 있었습니다.  
이 부분을 HOC를 통해 해결하여 나름 쾌적한 코드 스멜을 맡으며 작업하고 있기에 이 경험을 공유하고자 이 문서를 작성하였습니다.

일단 본론으로 들어가기 전에 UI 컴포넌트와 Container 컴포넌트가 무엇인지 설명하겠습니다.

# UI 컴포넌트

UI 컴포넌트는 순수하게 `props`를 전달하면 UI에 반영 되어 보여지는 컴포넌트입니다.

그 어떤 비즈니스 로직과도 연결 되어 있지 않아, 페이지 여러 곳에서 사용할 수 있는 컴포넌트의 기본 목적에 가장 충실한 컴포넌트라고 볼 수 있습니다.

![Button 예시](/assets/post_images/2024-05-08-declarative-programming-with-hoc/2024-05-08-10-45-52.png){: width="160"}

가장 간단한 예를 들자면 위 사진과 같은 버튼을 예시로 들 수 있습니다.

하지만 실제 프로젝트를 수행하면서 컴포넌트로 관리하고자 하는 UI는 위 버튼처럼 Atomic한 컴포넌트만 존재하지 않습니다.

![Form 예시](/assets/post_images/2024-05-08-declarative-programming-with-hoc/2024-05-08-10-46-23.png){: width="288"}

위 날짜 입력 Form처럼 굉장히 복잡한 수준의 컴포넌트도 충분히 UI 컴포넌트로 관리하고 싶을 수 있습니다.

## 왜 복잡한 컴포넌트도 순수하게 관리해야 하나요?

우리가 게시판을 만들어 본 적이 한 번이라도 있다면 충분히 경험했을 상황을 예로 들겠습니다.

![게시판 예시](/assets/post_images/2024-05-08-declarative-programming-with-hoc/2024-05-08-10-46-41.png)

위 사진과 같은 게시글 작성 Form이 존재한다고 가정해봅시다.

위 게시판에서 **‘글을 작성한다’**는 시나리오를 수행한다고 생각해보면 프로세스는 다음과 같을 것입니다.

1. 사용자는 게시글 작성 버튼을 눌러서 게시글 작성 페이지에 접속한다.
2. 에디터에 게시글을 입력한다.
3. 작성 버튼을 누른다.

매우 간단한 프로세스입니다.

하지만 **‘글을 수정한다.’**는 시나리오를 생각해보면 어떨까요?

1. 사용자는 게시글을 수정 버튼을 누른다.
2. 게시글 작성 당시와 동일한 기능을 지원하는 게시글 작성 Form에서 수정을 진행한다.
3. 수정 버튼을 눌러 수정을 완료한다.

게시글 수정의 경우 기존에 사용자가 입력했던 게시글의 제목과 내용 등을 모두 불러와 Form에 초기부터 입력이 되도록 구현해야 합니다.

이럴 때 작성 Form과 수정 Form이 동일한 기능을 지원하게 하면서, 상황에 따라 일부 기능의 수정이 가능하도록 구현할 필요가 있습니다.

그렇기 때문에 **Form과 같은 유기체 성격의 컴포넌트도 UI 컴포넌트로 분리할 필요가 있는 것입니다.**

# Container 컴포넌트

Container 컴포넌트는 다른 말로 Business 컴포넌트라고도 불립니다.
말 그대로 특정 도메인 안에서만 쓰이는 로직이 포함 된 컴포넌트입니다.

방금 전에 언급 했던 게시글 작성과 수정으로 예시를 들겠습니다.

우리는 게시판의 유연한 사용을 위해 `PostForm.tsx` 라는 UI 컴포넌트를 만들었다고 생각해봅시다.

`PostForm.tsx` 을 사용하여 게시글 수정 기능을 구현하고자 한다면 다음과 같이 구현할 수 있습니다.
{% raw %}
```tsx
/*
 * PostForm.tsx를 사용하여 게시글 수정을 진행하기 위한 Container 컴포넌트입니다.
 */
const UpdatePostFormContainer = () => {
	const [post, setPost] = useState<Post>();
	
	useEffect(() => {
		fetch(POST_SERVER_DOMAIN)
			.then((response) => response.json())
			.then((post) => setPost(post));
	}, []);
	
	return (
		<PostForm
			{ ...post }
		/>
	);
}
```
{% endraw %}
1. `fetch` API를 사용하여 게시글을 불러옵니다.
2. `PostForm` 컴포넌트에 불러온 데이터를 Bind합니다.

이처럼 ‘특정 API 서버’와 같은 다른 프로젝트에서 재사용하기 매우 어려운, 비즈니스 로직에 깊게 연관 된 로직이 포함 된 컴포넌트를 Container 컴포넌트라고 부릅니다.

## 왜 Props를 사용하지 않고 Container 컴포넌트로 분리 하나요?

결론부터 말씀 드리자면 **관심사를 분리하기 위해** 사용합니다.

`props`를 사용한다는 것은 다른 말로 하자면 컴포넌트를 사용하는 레이어에서 `props`로 전달할 데이터를 Fetching 하는 작업이 수반 되어야 한다는 얘기입니다.

대부분의 경우 컴포넌트에 넘겨줄 데이터를 Fetching 하는 주체는 페이지가 될 것입니다.
UI 요소를 배치하고자 하는 경우 페이지에 배치해야 하기 때문입니다.

하지만 Page의 경우 대부분 수행하고자 하는 코드의 무게가 무겁습니다.

페이지에는 보통 UI를 배치하는 코드만으로도 부담이 상당히 가해집니다.
여기에 데이터를 Fetching하는 코드에 대한 책임마저 페이지에게 일임한다면 페이지의 복잡도는 기하급수적으로 증가하게 될 것입니다.

{% raw %}
```tsx
import React, { useState, useEffect } from 'react';

const ComplexPage = () => {
  const [data, setData] = useState([]);
  const [loading, setLoading] = useState(false);
  const [error, setError] = useState(null);
  const [query, setQuery] = useState('default');

  useEffect(() => {
    setLoading(true);
    fetch(`https://api.example.com/data?query=${query}`)
      .then(response => response.json())
      .then(setData)
      .catch(setError)
      .finally(() => setLoading(false));
  }, [query]);

  const handleChange = event => {
    setQuery(event.target.value);
  };

  const handleRefresh = () => {
    setLoading(true);
    fetch(`https://api.example.com/data?query=${query}`)
      .then(response => response.json())
      .then(setData)
      .catch(setError)
      .finally(() => setLoading(false));
  };

  return (
    <div>
      <h1>Complex Page Example</h1>
      {error && <p>Error loading data!</p>}
      <input type="text" value={query} onChange={handleChange} />
      <button onClick={handleRefresh}>Refresh</button>
      {loading ? (
        <p>Loading...</p>
      ) : (
        <ul>
          {data.map((item, index) => (
            <li key={index}>{item.name}</li>
          ))}
        </ul>
      )}
    </div>
  );
};

export default ComplexPage;

// 😱😱😱😱😱
```
{% endraw %}

그렇기에 이미 관심사가 잘 분리 되어 있는 UI 컴포넌트를 기준으로 비즈니스 로직을 추가한 Container 컴포넌트를 제작하여 관리하는 것입니다.

## Container 컴포넌트는 문제가 없나요?

비즈니스 코드가 수정 되어야 하는 일부 상황에서 동일한 UI 컴포넌트를 참조하는 Container 컴포넌트가 많아질 수 있다는 단점이 존재합니다.

- 게시물을 불러오는 API 서버가 서로 다른 Endpoint를 가지는 경우
- 각각의 상황에서 Fetching하는 게시물의 interface가 서로 상이하여 이를 규격화 할 필요가 있는 경우

위 예시처럼 비즈니스 로직이 조금씩 상이하여 재사용이 어려운 경우 새로운 컴포넌트를 다시 만들어서 비즈니스 로직을 연결해야 하는데, 이렇게 구현할 경우 아래와 같이 불필요하게 복잡하고 많은 Container 컴포넌트가 생겨날 수 있습니다.

```tsx
- EditPostFormContainer.tsx
- FreeBoardPostFormContainer.tsx
- HumorBoardPostFormContainer.tsx
- AwesomePostFormContainer.tsx

...
```

# 비즈니스 로직을 HOC에게 위임해보자

HOC는 특정 함수를 통해 새로운 컴포넌트를 반환하는 구현 방법입니다.

HOC의 구현 및 사용 방법은 다음과 같습니다.

{% raw %}
```tsx
// PostForm.tsx

export const PostForm = ({ title, content }: PostFormProps) => {
	return (
		<form>
			<input value={title} name="title" />
			<textarea name="content">{ content }</textarea>
		</form>
	);
}
```
{% endraw %}

이처럼 `title`과 `content`를 전달 받아 Form을 구현하는 UI 컴포넌트가 있습니다.

이 컴포넌트에 데이터를 자동으로 할당해주는 HOC를 구현해봅시다.

{% raw %}
```tsx
// withPostData.ts

const withPostData = (Component: ComponentType<typeof PostForm>) => {
	return () => {
		const [post, setPost] = useState();
		
		useEffect(() => {
			fetch(POST_SERVER_DOMAIN)
				.then((response) => response.json())
				.then((post) => setPost(post));
		}, []);
	
		return (
			<Component
				title={post.title}
				content={post.content}
			/>
		);
	};
}
```
{% endraw %}

`withPostData` 라는 함수는 JSX Element를 반환하는 함수를 반환하도록 구현되어 있습니다.

다시 말해, React Component를 함수 내에서 자체적으로 제작하여 반환한다는 뜻입니다.

그러므로 페이지에서 다음과 같이 사용할 수 있습니다.
{% raw %}
```tsx
// page.tsx

export const Page = () => {
	return (
		<PostFormWithData />
	);
}

const PostFormWithData = withPostData(PostForm);
```
{% endraw %}

이처럼 매우 선언적인 코드로 Data Fetching, Data Bind, UI 배치와 같은 작업을 모두 수행할 수 있게 되었습니다. 🎉

## HOC를 활용한 데이터 바인딩의 또다른 장점

만약 당신이 게시판의 형태가 매우 다양한 웹사이트를 운영하고 있다고 가정해봅시다.

- 자유게시판
- 유머게시판
- QnA 게시판
- 등등…

Container 컴포넌트를 활용하였다면 UI 컴포넌트에 대응 되는 Container 컴포넌트를 동일한 갯수 (혹은 그 이상)만큼 생성해야 합니다.

```
- FreeBoardPostForm.tsx
- HumorBoardPostForm.tsx
- QnABoardPostForm.tsx

- FreeBoardEditPostFormContainer.tsx
- HumorBoardEditPostFormContainer.tsx
- QnABoardEditPostFormContainer.tsx
- ...

😱😱😱
```

하지만 당신이 HOC를 이용한 데이터 바인딩을 사용했다면 다음과 같이 간단하게 제작할 수 있습니다.

{% raw %}
```tsx
// HumorBoardPostForm.tsx

const FreeboardEditPage = () => {
	return (
		<PostFormWithData />
	);
}

const PostFormWithData = withPostData(FreeBoardPostForm);
```
{% endraw %}

{% raw %}
```tsx
// HumorBoardPostPage.tsx

const HumorBoardEditPage = () => {
	return (
		<PostFormWithData />
	);
}

const PostFormWithData = withPostData(HumorBoardPostForm);
```
{% endraw %}

{% raw %}
```tsx
// QnABoardPostForm.tsx

const QnABoardEditPage = () => {
	return (
		<PostFormWithData />
	);
}

const PostFormWithData = withPostData(QnABoardPostForm);
```
{% endraw %}

Container 컴포넌트의 구현 없이 매우 선언적으로 페이지 내에서 바로 게시물 수정 기능을 구현하는데 성공하였습니다. 🎉

물론 이런 이점을 누리려면 각각의 게시판이 동일한 로직을 거쳐서 게시물 데이터를 할당 받을 수 있도록 interface를 잘 구성해야 하겠습니다.

# 번외) Suspense와 HOC를 활용한 선언적인 스켈레톤 코드

React 18에서 추가 된 Suspense 컴포넌트는 하위에 사용 된 컴포넌트에서 Promise가 throw 되면 `fallback` Prop으로 전달한 UI를 노출시킵니다.
{% raw %}
```tsx
const PromiseExample = () => {
	return (
		<Suspense fallback={<LoadingIndicator />}>
			<VerySlowComponent /> // 여기서 Promise를 throw하고 있다면 LoadingIndicator 노출
		</Suspense>
	);
}
```
{% endraw %}

HOC Data Fetching 부분을 간단하게 수정하는 것만으로도 Suspense와 결합하여 스켈레톤 UI를 보여주는 선언적인 코드를 작성할 수 있습니다.
{% raw %}
```tsx
// withData.ts

export const withData = () => {
	return () => {
	  const [data, setData] = useState(null);
	
	  useEffect(() => {
	    fakeFetch().then(response => {
	      setData(response.data);
	    });
	  }, []);
	
	  if (!data) {
	    // 데이터가 아직 로드되지 않았을 때는 Suspense가 처리할 수 있도록 Promise를 throw합니다.
	    throw fakeFetch();
	  }
	
	  return <div>{data}</div>;
	};
}
```
{% endraw %}

HOC를 위와 같이 작성하면 이를 사용하여 아래와 같이 구현이 가능합니다.
{% raw %}
```tsx
const PromiseExample = () => {
	return (
		<Suspense fallback={<LoadingIndicator />}>
			<VerySlowComponent />
		</Suspense>
	);
}

const VerySlowComponent = withData(UIComponent);
```
{% endraw %}

# 후기

이처럼 HOC를 사용하여 데이터를 쉽게 불러오고 할당하고 사용하는 방법에 대해 알아보았습니다.

문서 초입에 말씀 드렸던 것 처럼 부끄럽게도 저는 얼마 전 까지 UI 컴포넌트, Container 컴포넌트에 대한 폴더 구조와 중복 코드 관리 방법에 대해 정말 오랜 기간 고민하였습니다.

이 과정에서 정말 여러가지를 시도 해보았습니다.

- UI 컴포넌트를 만들지 말고 모든 컴포넌트를 Container 컴포넌트로 만들어본다던가
- Data Fetching을 위한 Hook을 세밀하게 분리하여 페이지에서 최대한 코드 정의 없이 Hook만으로 Fetching 해본다던가

하지만 어떤 방법을 써도 프로젝트 규모가 커지면 결국에는 스파게티가 되어버렸고, 그 스파게티를 해결하기 위해 고생만 하기도 했던 경험이 많았는데 HOC를 사용한 방법은 아직 아무런 문제 없이 쾌적한 코드 환경을 조성해주고 있습니다.

‘은총알은 없다’는 말이 있듯이, 결국에는 이 역시도 사용범위가 거대해지면 분명 어떠한 문제점이 나타날 것이라 생각합니다만 그 때는 그 상황에 맞는 해결 방법이 있으리라 믿으며 글을 마칩니다.