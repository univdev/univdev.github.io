---
title: "React Context"
author:
  name: 박 찬영
  link: https://github.com/univdev
date: 2022-09-30 23:24:00 +0900
categories: [Tech, React]
tags: [React, Context]
---
# Context
`Context`는 React에서 제공하는 기능 중 하나로, 유효범위 내부에 존재하는 컴포넌트들에서 데이터를 주고받는 방법 중 하나입니다.  
# 사용 이유
기본적으로 React 공식 홈페이지에서는 컴포넌트들의 계층이 복잡해질 때 `props`가 아닌 다른 방식으로 데이터를 쉽게 주고 받을 수 있다고 홍보(?)하고 있습니다.
![Prop Drilling](/assets/post_images/2022-09-30-react-context-introduction/20220930233402.png)
_하위 컴포넌트에게 넘겨주기 위해 거쳐가야하는 관문이 많다_

위 사진은 저번 [Atomic Design][Atomic Design] 문서에서 언급했던 `Prop Drilling` 현상을 설명하는 사진입니다.
컴포넌트가 서로 데이터를 공유하기 위해 `props`를 사용하다보면 상위 계층이 갖고있는 데이터를 최하위까지 넘겨주기 위해 생기는 문제가 발생하는데 이를 `Prop Drilling`이라고 합니다.  

`Context`는 이를 어떻게 해결해 줄 수 있을까요?
# 사용법
## React.createContext
우선 `Context`를 사용하기 위해 `Context`를 생성해봅시다.  
`Context`를 생성하기 위해서는 `React.createContext` 함수를 사용해주시면 됩니다. 매개변수로는 해당 `Context`가 생성 초기에 가질 데이터를 선언해주면 됩니다.
```typescript
const ExampleContext = React.createContext({ message: 'Hello world!' });

export default ExampleContext;
```
{: file="context/ExampleContext.ts"}

`Context`를 선언했으니 이제 이를 사용해봐야겠죠?
## Context.Consumer
`Consumer`라는 이름을 가진 API답게 `Context`의 값을 사용만 할 수 있는 컴포넌트입니다.
```tsx
{% raw %}
import ExampleContext from './context/ExampleContext.ts';

export const App = () => {
  return (
    <ExampleContext.Consumer>
      {(value) => {
        return (
          <div>
            오늘의 인사말은 {value.message} 입니다!
          </div>
        );
      }}
    </ExampleContext.Consumer>
  );
};

export default App;
{% endraw %}
```
{: file="index.tsx"}

`Context API`에서 자주 사용되는 `Consumer`는 `Context`가 갖고있는 `value`에 접근할 수 있는 기능을 제공합니다.  
`Context.Consumer` 태그 내부에는 함수가 적용될 수 있는데, `value`를 매개변수로 갖고 있으며 해당 함수에서 반환되는 `JSX`를 렌더링합니다.  

`value`는 일반적인 `State`와 마찬가지로 반응성을 가지고 있어, `Context`의 데이터가 변경이 된다면 **`Context`의 값을 참조하고 있는 컴포넌트들은 모두 리렌더링이 됩니다.**
## Context.Provider
`Provider`는 `Consumer`와 동일하게 값을 참조할 수 있지만 데이터를 이 곳에서 선언할 수도 있습니다.
```tsx
{% raw %}
import ExampleContext from './context/ExampleContext.ts';

export const App = () => {
  const [message, setMessage] = useState<string>('Good bye!');

  return (
    <ExampleContext.Provider value={{ message }}>
      <div>오늘의 인사말은 {message} 입니다!</div>
    </ExampleContext.Provider>
  );
};

export default App;
{% endraw %}
```
{: file="index.tsx"}
## React.useContext
`useContext`는 `Provider` 내부에 존재하는 계층구조의 컴포넌트에서 최하위 계층에서 `Context`값을 참조하는데 도움을 주는 API입니다.
```tsx
{% raw %}
import ExampleContext from './context/ExampleContext.ts';
import MessagePrintComponent from './MessagePrintComponent.tsx';

export const App = () => {
  return (
    <ExampleContext.Provider value={{ message: 'good bye!' }}>
      <MessagePrintComponent />
    </ExampleContext.Provider>
  );
};

export default App;
{% endraw %}
```
{: file="index.tsx"}
위와 같이 `Provider` 내부에 존재하는 `MessagePrintComponent`가 있습니다.

```tsx
{% raw %}
import ExampleContext from './context/ExampleContext.ts';

export const MessagePrintComponent = () => {
  const value = React.useContext(ExampleContext);

  return (
    <div>
      <div>오늘의 인사말은 {value.message} 입니다!</div>
    </div>
  );
};

export default MessagePrintComponent;
{% endraw %}
```
{: file="MessagePrintComponent.tsx"}
`useContext`를 사용하면 `Provider` 하위에 존재하는 컴포넌트에서도 굳이 `props`로 받지 않더라도 데이터를 직접 참조할 수 있습니다.

> 그게 '가능'은 하다는거지 컴포넌트의 의존성이 심해지므로 **일반적인 경우에는 `props`로 데이터를 전달받는게 훨씬 좋습니다.**

# 주의사항
## 리렌더링 이슈
React에서의 컴포넌트 리렌더링은 어떤 컴포넌트에서 사용되고 있는 `state`의 참조를 확인합니다.  
그렇다보니 만약 `Context`가 최상위가 아니라 페이지 전체 계층 중 중간쯤 단계에서 사용하고 있을 때 이런 문제가 발생할 수 있습니다.

```tsx
{% raw %}
const Top = () => {
  const [number, setNumber] = useState<number>(0);

  return (
    <>
      <Middle number={number} />
      <button onClick={setNumber(number + 1)}>number 증가</button>
    </>
  );
};
{% endraw %}
```
{: file="top.tsx"}

```tsx
{% raw %}
export const Middle = (props) => {
  return (
    <AnyContext.Provider value={{ message: 'Hello world!' }}>
      { props.number }
      <Bottom />
    </AnyContext.Provider>
  );
};
{% endraw %}
```
{: file="middle.tsx"}

```tsx
{% raw %}
export const Bottom = () => {
  const value = React.useContext(AnyContext);

  return (
    <div>
      { value.message }
    </div>
  );
};
{% endraw %}
```
{: file="bottom.tsx"}

1. `Top` 컴포넌트는 `number`라는 `state`를 갖고 있습니다.
2. `Top` 컴포넌트는 버튼을 누르면 `number`가 1씩 증가합니다.
3. `Top` 컴포넌트는 `Middle` 컴포넌트에게 `number`를 전달합니다.
4. `Top` 컴포넌트는 `Middle`이라는 하위 컴포넌트를 갖고 있습니다.
5. `Middle` 컴포넌트는 `AnyContext.Provider`를 갖고 있습니다.
6. `Middle` 컴포넌트는 `Bottom`이라는 하위 컴포넌트를 갖고 있습니다.
7. `Middle` 컴포넌트는 상위 컴포넌트로부터 `number`를 받아서 출력합니다.
8. `Bottom` 컴포넌트는 `useProvider`라는 API를 이용하여 `AnyContext`의 데이터를 참조하고 있습니다.

이 상황에서 `Bottom` 컴포넌트가 렌더링 되기 위한 최고의 상황은 `AnyContext`의 `Value`가 수정될 때 뿐일거예요.  
하지만 `setNumber`가 실행될 때마다 `Bottom`마저 리렌더링이 됩니다.

그 이유는, `Top` 컴포넌트의 `state`를 `middle` 컴포넌트가 바라보고 있으니 `number`가 변할 때마다 `middle`이 리렌더링 됩니다.  
그 과정에서 `Provider`가 리렌더링 되면서 전달되는 `value`의 값마저 재정의 되기 때문에 React는 해당 `Provider`하위에서 `AnyContext`의 value를 참조하는 모든 컴포넌트를 리렌더링 시키게 됩니다.

## 해결방법
`Provider`에 전달할 value를 `state`로 정의하면 됩니다.

```tsx
{% raw %}
export const Middle = (props) => {
  const [message, setMessage] = useState<string>('Hello world');

  return (
    <AnyContext.Provider value={{ message }}>
      { props.number }
      <Bottom />
    </AnyContext.Provider>
  );
};
{% endraw %}
```
{: file="middle.tsx"}

이렇게 정의하면 상위 컴포넌트의 값이 바뀌어서 `Middle` 컴포넌트가 리렌더링이 된다고 해도 상태로서 정의 된 `message`의 값이 재정의 되지 않으니 React는 `Provider`의 value가 변경되었다고 판단하지 않기 때문입니다.

# 레퍼런스
[React - Context][React 공식 홈페이지]

[Atomic Design]: {% post_url 2022-09-27-atomic-design-with-container-component %}
[React 공식 홈페이지]: https://ko.reactjs.org/docs/context.html