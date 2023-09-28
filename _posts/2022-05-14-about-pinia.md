---
title: "Vue3에서 사용되는 상태 관리자, Pinia"
author:
  name: 박 찬영
  link: https://github.com/univdev
date: 2022-05-14 23:06:00 +0900
categories: [Tech, Vue]
tags: [Pinia]
---
![Pinia][Pinia]
# Pinia
```Pinia```는 ```Composition API``` 기반에서 동작하는 상태 관리자입니다.  
여태까지 ```Vue```에서 사용되던 가장 보편적이고 유명한 상태 관리자는 ```Vuex```였습니다.  
그런데 ```Vue```의 개발자, **Even You**는 본인의 트위터에서 ```Vuex 5```와 ```Pinia```는 [사실상 완전 동일한 프로젝트로 생각해야 한다고 말한 바][Even You Twitter] 있습니다.

```Vuex 5```에서 원하던 기능들의 대부분을 이미 ```Pinia```에서 지원하고 있었기에, ```Vuex``` 프로젝트를 유지하는 대신 ```Pinia```를 공식적으로 지원한다고 ```Pinia```의 [공식 문서][Pinia Official]에 적혀 있습니다.
# 차이점
```Vuex```를 버리고 ```Pinia```를 써야하는 이유에는 어떤 것들이 있을까요?
## Typescript 지원
### Vuex에서
```Vuex```를 이용하여 프로젝트를 구성해보신 적이 있으시다면, ```Vuex```의 상태와 매칭되는 타입 정의가 매우 까다롭다고 생각하셨을 겁니다.

1. Vuex는 ```module```을 지원한다.
2. ```module```은 대부분 각각의 파일에서 구현부가 작성된다.
3. 컴포넌트에서 ```store```를 접근할 때는 ```this.$store```를 통해 ```Vue```의 ```Prototype```에 정의 되어있는 ```$store```객체를 통해 접근한다.

위와 같은 형태로 ```Vuex```를 이용하기 때문에 사실상 뿔뿔이 흩어져있는 ```module``` 파일들에 대한 ```state``` 타입을 추론하는게 어렵습니다.

그래서 ```vuex```를 이용하면서 타입 추론도 하려면 아래와 같은 몇가지 과정을 거쳐야합니다.
```typescript
// vuex.d.ts
import { Store } from 'vuex'

declare module '@vue/runtime-core' {
  // declare your own store states
  interface State {
    count: number
  }

  // provide typings for `this.$store`
  interface ComponentCustomProperties {
    $store: Store<State>
  }
}
```
{: file="something.d.ts"}
일단, **상태가 하나 추가 및 수정 될 때마다** 타입 추론을 위해 ```state```의 구조를 나타내는 ```d.ts``` 파일을 별도로 작성합니다.
```typescript
import type { StateType as AType } from './A';
import type { StateType as BTYpe } from './B';

export type RootState = {
  A: AType,
  B: BType,
};
```
{: file="index.ts"}
또한, ```RootState```를 얻기 위해 필요하지도 않은 ```index.ts``` 파일을 만들어서 모든 ```module```을 하나로 연결시켜줄 구심점을 구성해야 하며
```typescript
import type { RootState } from './index';

export const actions: ActionTree<StateType, RootState> = {
  // ...actions
};
```
{: file="something-module.ts"}
모듈을 만들고 ```Action```을 하나 정의할 때마다 ```ActionTree```의 ```Generic```에 전달할 ```RootState```를 ```index.ts```로 부터 끌고 와야 한다는 점이 있습니다.  
물론 이 모든 과정은 최초 한 번만 하면 되는게 아니라 어떤 ```Module```의 ```State```가 **변하거나 추가되면 다시 해야합니다!**

그래서 저는 ```Vue2 + Vuex + Typescript``` 환경에서 개발할 일이 있을 때는 ```Vuex```에 대한 타입 추론은 아예 포기하고 ```this.$store```를 ```any```로 정의하여 구현한 적도 있습니다.
### Pinia에서
```Pinia```는 ```defineStore```라는 함수를 이용하여 각각의 파일마다 별도의 ```store```를 정의하여 ```module```의 기능을 대신합니다.  
```vuex```의 ```this.$store```와는 달리 ```pinia```는 ```defineStore```가 반환하는 ```hook```을 이용하여 ```store```에 아주 쉽게 접근할 수 있습니다.
그렇기에, 반환 된 ```hook```은 내부 구성 요소에 대한 타입을 포함하고 있으며, **타입 추론에 대한 그 어떤 작업도 추가로 필요하지 않습니다.**

```typescript
import { defineStore } from 'pinia';

export const useCart = defineStore('cart', {
  state: () => ({
    items: [],
  }),
  actions: {
    addItem(item: ItemType) {
      this.items.push(item);
    },
  },
});
```
{: file="cart.ts"}
```cart.ts``` 파일을 정의하면, 실제 프로젝트에서는 아래와 같이 사용할 수 있습니다.
```vue
<script type="ts">
import { defineComponent } from 'vue';
import { ItemType, useCart } from '@/store/cart';

export default defineComponent({
  name: 'HomePage',
  setup () {
    const cart = useCart(); // cart.items, cart.addItem
    return {
      cart,
    };
  }
});
</script>
```
```defineStore``` 함수가 반환한 커스텀 hook인 ```useCart```를 이용하여 선언만 한다면 ```state```, ```actions```가 모두 변수 ```cart```에 객체로 할당되는 것을 보실 수 있습니다.
## 구성요소
### Vuex에서
```Vuex```는 아래와 같은 구성요소를 갖습니다.
- ```namespace```
  - ```module```을 별도의 ```name```으로 분리하여 관리할 것인지를 정의하는 프로퍼티
- ```state```
  - 모듈 하나에 하나만 보유 가능
  - 해당 모듈에서 보관할 상태값의 집합
- ```mutation```
  - ```state```를 변환할 수 있는 사실상 유일한 방법
  - 비동기 로직을 처리할 수 없으며, ```mutation``` 함수의 첫번째 인자로 현재 ```state``` 값을 가져올 수 있다.
- ```getter```
  - ```state```값을 단순히 반환하기만 하는 함수의 집합
  - ```state```값의 수정이 불가능하며, 특정 규칙에 따라 필터링 된 ```state```를 얻을 때 요긴하게 사용된다.
- ```actions```
  - 비동기 처리가 가능하며, ```state```를 변환할 때 쓰는 함수의 집합
  - 직접적으로 ```state```를 변환할 수는 없으며, ```commit``` 함수를 통해 ```mutation```을 호출하여 수정이 가능하다.

### Pinia에서
```Pinia```는 아래와 같은 구성요소를 갖습니다.
- ```state```
  - ```store```하나에 하나만 보유 가능
  - 해당 ```store```에서 관리 될 상태값의 집합
- ```actions```
  - ```state```를 변환할 때 사용하는 함수의 집합
  - 비동기 처리가 가능하며, ```vuex```와는 다르게 직접 ```state```의 값을 제어할 수 있다.
- ```getter```
  - ```state```값을 단순히 반환하기만 하는 함수의 집합
  - ```state```값의 수정이 불가능하며, 특정 규칙에 따라 필터링 된 ```state```를 얻을 때 요긴하게 사용된다.

```Vuex```와는 다르게 ```mutation```이 제거되고 ```actions```에서 상태를 모두 제어합니다.  
이로 인해 ```actions```에서 ```state```를 제어하기 위해 ```mutation```을 일일히 만들지 않아도 됩니다.

또한, ```module```이라는 개념이 사라지고, 개별적인 ```store```로 관리 되기에 ```namespace```도 없어집니다.
## SFC 문법 지원
### Vuex에서
**SFC 문법을 지원하지 않습니다.**
### Pinia에서
```Pinia```는 ```Composition API```를 지원합니다.  
그러므로, ```Vuex```와 같은 구조로 정의하지 않고, ```SFC```의 ```setup```을 정의할 때와 동일하게 사용할 수 있습니다.

```typescript
export const useCart = defineStore('cart', () => {
  const items: ItemType[] = reactive([]);
  /**
   * items 배열에 item을 추가합니다.
   * @param item
   */
  const addItem = (item: ItemType) => {
    items.push(item);
  };
  /**
   * items 배열에 들어있는 item을 name을 기준으로 변경합니다.
   * count 프로퍼티는 예외적으로 기존 값에 추가됩니다.
   * @param name
   * @param item
   * @returns
   */
  const updateByName = (name: string, item: ItemType) => {
    const index = items.findIndex(i => i.name === name);
    if (index <= -1) {
      return;
    }
    const original = items[index];
    items.splice(index, 1, { ...original, ...item, count: original.count + item.count });
  };
  return {
    items,
    addItem,
    updateByName
  };
});
```
{: file="cart.ts"}

이처럼 ```state```, ```actions```, ```getters``` 등을 명시적으로 구분하지 않아도, ```ref```, ```reactive```. ```computed```와 같은 ```Composition API Hooks```를 이용하여 ```setup``` API를 사용하는 ```SFC```와 완전히 동일한 형태로 구현해도 **아주 자연스럽게 동작합니다.**

심지어 특정 상태값의 변이를 트래킹하기 위해 ```watch```를 사용할 수도 있습니다!
# 요구사항
```Pinia```는 ```Vue3``` 혹은 ```Vue2 + Composition API``` 환경에서만 동작합니다.
# 예시 프로젝트
[여기][example]를 보시면 ```Nuxt3 + Pinia + Element UI```를 통해 제작한 아주 간단한 형태의 장바구니 프로젝트를 보실 수 있습니다.

[Pinia]: https://user-images.githubusercontent.com/26304279/168429298-d9a2e055-82f0-415d-b5d9-215e4f7f3998.png
[Even You Twitter]: https://twitter.com/youyuxi/status/1463429442076745730?lang=eu
[Pinia Official]: https://pinia.vuejs.org/introduction.html#comparison-with-vuex
[example]: https://github.com/univdev/nuxt3-pinia-element-plus