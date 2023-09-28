---
title: "Nextjs에서 React Portal 사용해보기"
date: 2022-11-03 10:53:00 +0900
author:
  name: 박 찬영
  link: https://github.com/univdev
categories: ["Tech", "Next.js"]
tags: ["Next.js", "React", "Portal"]
---
# React Portal
웹을 제작하다보면 대부분의 경우 계층 구조로 컴포넌트를 배치하여 렌더링하는 방식으로 문제를 해결할 수 있습니다.  
그런데 간혹 n레벨에 배치 되어야 하는 컴포넌트가 실제로 렌더링 될 때는 Document의 최상단에 배치하고 싶을 수 있습니다.

![Tooltip](/assets/post_images/2022-11-03-nextjs-portal/20221103105627.png)
_Tooltip_
![Modal](/assets/post_images/2022-11-03-nextjs-portal/20221103105711.png)
_Modal_

Tooltip이나 Modal 컴포넌트가 바로 그 대표적인 예시입니다.  
위 두 개의 컴포넌트는 웹 페이지에 존재하는 어떤 요소보다 위에 존재해야 하기 때문에 `<body>` 태그의 바로 하단으로 옮겨줄 필요가 있습니다.  
이런 상황에 간단하게 사용할 수 있는 방법이 바로 `ReactDOM.createPortal()` API입니다.
## ReactDOM.createPortal
```tsx
{% raw %}
const Modal = (props: ModalProps) => {
  const Component = React.createComponent('div', { ...props }, props.children);
  const portal = ReactDOM.createPortal(Component, document.body);

  return Portal
};

const App = () => {
  return (
    <Modal />
  );
}
{% endraw %}
```
`ReactDOM.createPortal`은 두 개의 인자를 받습니다.  
첫번째 인자는 `ReactNode`, 즉 배치하고 싶은 컴포넌트를 넘겨주시면 됩니다.  
두번째 인자는 첫번째 인자에서 넘겨준 `ReactNode`를 배치하고 싶은 `Element`를 넘겨주시면 됩니다.
# Next
정말 사용하기 쉬운 API지만 Next에서 사용할 때 한가지 문제가 있습니다.

```tsx
{% raw %}
const Modal = (props: ModalProps) => {
  const Component = React.createComponent('div', { ...props }, props.children);
  const portal = ReactDOM.createPortal(Component, document.body);

  return Portal
};
{% endraw %}
```
위 코드를 Next.js에서 사용하면 아래와 같은 이슈가 표시될 것입니다.

> document is not defined

Next는 별도의 설정을 하지 않는다면 SSR / CSR을 동시에 진행하기 때문에 SSR 과정에서 `document`를 탐색할 수 없기 때문입니다.  
그렇다면 아래와 같이 코드를 수정해봅시다.

```tsx
{% raw %}
const Modal = (props: ModalProps) => {
  if (typeof window === 'undefined') return <></>;

  const Component = React.createComponent('div', { ...props }, props.children);
  const portal = ReactDOM.createPortal(Component, document.body);

  return Portal
};
{% endraw %}
```
CSR 과정에서는 `window` 객체가 생성될 터이니 `window` 객체가 없다면 렌더링을 진행하지 않는 로직이 추가되었습니다.  
이러면 별 문제가 없어보이지만 아래와 같은 메시지가 나오면서 Hydration 이슈가 발생합니다.

> Hydration failed because the initial UI does not match what was rendered on the server

## Hydration
Hydration의 상세한 내용은 [이 문서][Hydration]를 참고해주세요!  
문제점만 간단하게 요약하자면 SSR에서 생성한 정적 페이지와 CSR에서 초기에 읽어낸 웹페이지의 코드가 서로 상이하여 생기는 문제입니다.

## 해결 방법
```tsx
{% raw %}
const Modal = (props: ModalProps) => {
  const [isCSR, setIsCSR] = useState<boolean>(false);

  useEffect(() => {
    setIsCSR(true);
  }, [])
  
  if (typeof window === 'undefined') return <></>;
  if (!isCSR) return <></>;

  const Component = React.createComponent('div', { ...props }, props.children);
  const portal = ReactDOM.createPortal(Component, document.body);

  return Portal
};
{% endraw %}
```
`isCSR` state를 생성하고, `useEffect` Hook으로 `isCSR`을 수정하여 `isCSR`이 `true`일 때 렌더링을 진행하면 Hydration 문제가 해결됩니다.
# 레퍼런스
[[Next.js] Hydration failed because the initial UI does not match what was rendered on the server 에러][레퍼런스]

[Hydration]: {% post_url 2022-11-03-nextjs-hydration %}
[레퍼런스]: https://velog.io/@hyeonq/Next.js-Hydration-failed-error