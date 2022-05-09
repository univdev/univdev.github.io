---
title: "[Vue] Teleport"
author:
  name: 박 찬영
  link: https://github.com/univdev
date: 2022-04-21 18:12:00 +0900
categories: [Tech, Vue]
tags: [Vue, Vue3, Portal, Teleport]
---
# 개요
Vue3에서는 정말 많은게 새로 추가되고 삭제 되었습니다.  
Composition API, ref, reactive 등의 변경점이 있는데, 이들 중 제가 가장 관심이 있었던건 Teleport였습니다.

Teleport란 [React의 Portal][React Portal]과 같은 기능을 합니다.  
Teleport는 컴포넌트 내부의 특정 객체를 페이지 내 원하는 위치에 렌더링하는 기술입니다.  
아마 SPA 개발 툴을 사용해보지 않은 분들께서는 **"고작 엘리먼트 위치 변경하는게 그렇게 관심받을 일인가?"**라고 생각 하실 수도 있습니다.  
**그런 분들은 이 문서를 꼭 읽어주세요!**
# 컴포넌트 주도 개발의 문제점
컴포넌트 주도 개발이란, UI를 아주 작은 단위로 쪼개서 이를 서로 연결하여 하나의 기능을 만드는 형태의 개발 방식입니다.  
React, Vue와 같은 프레임워크는 이 방식을 따르고 있어요.  
그럼 여기서 예시를 하나 들어봅시다.
![Dialog][Dialog]
_Dialog_
위 움짤과 같은 Dialog는 컴포넌트를 어떻게 구성하면 좋을까요?  
아마 대부분 아래와 같은 구성 요소를 가질겁니다.
- 백그라운드 마스크
- 컨텐츠가 보여질 팝업

컨텐츠가 보여질 팝업이야 일반적인 UI를 만들 듯 구성하면 아무런 문제가 될 게 없을 것 같습니다.  
그런데 백그라운드에 씌워질 마스크는 어떻게 구현해야 할까요?  
깊게 생각할 것 없이 아래와 같은 CSS를 갖도록 구현될 것 같습니다.

```css
.dialog-mask {
  position: fixed;
  left: 0;
  top: 0;
  width: 100%;
  height: 100%;
  background-color: rgba(0, 0, 0, .45);
}
```
일반적인 상황에서는 문제 없이 동작하겠지만, 아주 만약에 ```position: fixed```가 적용 된 Element의 하위에서 Dialog를 사용해야 할 일이 생긴다면 어떻게 될까요?  
아마 부모 Element에 종속적으로 동작하게 될 것입니다.  
그렇기에 이 마스크만 부모의 간섭을 받지 않는 페이지의 최상단, 즉 body 태그의 직계 자식으로 올려 줄 필요가 있습니다.
# Teleport 없이 마스크 옮기기
## Javascript
```javascript
export default {
  // ...
  mounted() {
    const mask = document.createElement('div');
    mask.setAttribute('class', 'mask');
    window.document.body.append(mask);
  },
  beforeDestroy() {
    document.querySelector('.mask').remove();
  },
  // ...
};
```
이런 식으로 컴포넌트가 마운트 되는 순간에 ```document.createElement```를 이용하여 ```body```의 뒤쪽에 추가하는 방법이 있을 수 있습니다.  
물론 이렇게 하면 **body 태그에 마스크를 추가하는 것** 자체는 문제가 없을거예요.  
그런데 이제 마스크에 이벤트를 추가하거나, 또 다른 Element를 자식으로 배치하는 등의 상황에서는 문제가 심각해집니다.  
정상적인 프레임워크의 흐름을 따르지 않으므로 라이프사이클 및 Element Rendering 등의 상황에서 프레임워크가 주는 이점을 얻지 못하게 됩니다. 커스터마이징이 죽고 싶을 정도로 어려워지는건 덤이구요.  
또한 Scoped CSS를 사용하지도 못하겠네요.
## 최상단 컴포넌트에 마스크를 배치하기
```vue
<template>
  <div class="mask">
    <child />
  </div>
</template>
```
{: file="parent.vue"}
```vue
<template>
  <div class="dialog"></div>
</template>
```
{: file="child.vue"}

이런식으로 제어한다면 컴포넌트의 제어 하에 마스크를 그렸으니 위에서 말한 문제점은 해소가 된 듯 합니다.  
```parent.vue``` 파일에서 CSS를 정의하면 마스크에도 적용이 될거고, 컴포넌트에서 그려졌으니 생명주기도 정상적으로 적용이 될겁니다.

그렇지만 Dialog를 끄고 켜는 모든 상황에서 부모와 자식간 데이터와 이벤트를 전달받아야 한다는 점이 굉장히 큰 리스크로 다가오게 되었습니다.  
```parent.vue```에서 ```isVisibleDialog``` 변수를 들고있고, 이 변수에 따라 마스크를 활성 / 비활성 해야한다면 ```child.vue```에서 Dialog를 표시하는 버튼을 누를 때마다 ```emit```으로 마스크가 존재하는 부모까지 이벤트를 전달해야 마스크의 활성 / 비활성 여부를 제어할 수 있어요.  
```vue
<template>
  <Child>
    <Child>
    <Child>
      <!-- 여기에 버튼이 있다면? -->
    </Child>
    </Child>
  </Child>
</template>
```
{: file="parent.vue"}
만약 위와 같은 구조로 이루어져있다면 정말 너무 끔찍하네요.

또한, 인적 실수로 인해 이벤트를 상단까지 전달하는 것에 실패하여 마스크를 제어하지 못하게 된다면 Dialog는 표시 되는데 마스크는 표시 되지 않는 현상이 일어날 수 있어요.  
Dialog와 마스크는 사실상 하나의 컴포넌트라고 봐도 무방한데도 서로의 행동에서 결과를 예측하기 어렵게 됩니다.
# Teleport 이용하기
## 사용법
```vue
<template>
  <teleport to="css-selector">
    <component />
  </teleport>
</template>
```
위 처럼 ```teleport``` 태그를 활용하시면 됩니다.  
```teleport``` 태그는 ```to``` Property를 받는데, 이 Property에 CSS Selector를 넣으면 ```teleport``` 태그 안에 있는 Element들이 해당 셀렉터 내부로 이동합니다. scoped CSS도 정상적으로 동작하고, 컴포넌트의 Life cycle대로 탄생하고 소멸합니다.
## 예시
```vue
<style scoped>
  .dialog__bg {
    position: fixed;
    left: 0;
    top: 0;
    width: 100%;
    height: 100%;
    pointer-events: none;
    z-index: 10;
  }
  .dialog__bg.dialog__bg--active {
    background-color: rgba(0, 0, 0, .4);
    pointer-events: inherit;
  }
  .dialog__content {
    border-radius: 4px;
    background-color: #FFF;
    max-width: 500px;
    width: 100%;
    position: fixed;
    right: 50%;
    top: 50%;
    transform: translate(50%, -50%);
    z-index: 11;
  }
  .dialog__content .dialog-title {
    padding: 16px;
    font-weight: bold;
    font-size: 21px;
  }
  .dialog__content .dialog-content {
    padding: 16px;
    padding-top: 0;
    font-size: 14px;
  }
</style>

<template>
  <div
    v-if="visible"
    class="dialog"
  >
    <teleport to='body'>
      <div
        :class="{ 'dialog__bg': true, 'dialog__bg--active': visible }"
        @click="onClickModal" />
    </teleport>
    <div class="dialog__content">
      <div class="dialog-title">
        <slot name="title"></slot>
      </div>
      <div class="dialog-content">
        <slot name="content"></slot>
      </div>
    </div>
  </div>
</template>

<script lang="ts">
import { defineComponent, PropType } from 'vue'
export default defineComponent({
  props: {
    visible: {
      type: Boolean as PropType<boolean>,
      required: false,
      default: false,
    },
  },
  setup(props, { emit }) {
    const onClickModal = () => {
      emit('update:visible');
    };
    return {
      onClickModal,
    };
  },
});
</script>
```
{: file="dialog.vue"}
```vue
<template>
  <div class="dialog-example">
    <button
      type="button"
      @click="onClickShowDialogButton">
      Dialog 표시
    </button>
    <Dialog
      v-model:visible="isVisible">
      <template v-slot:title>
        제목
      </template>
      <template v-slot:content>
        내용
      </template>
    </Dialog>
  </div>
</template>

<script lang="ts">
import { defineComponent, ref } from 'vue'
import Dialog from './components/Dialog/index.vue';
export default defineComponent({
  components: {
    Dialog,
  },
  setup() {
    const isVisible = ref(false);
    const onClickShowDialogButton = () => {
      isVisible.value = true;
    };
    return {
      isVisible,
      onClickShowDialogButton,
    };
  },
});
</script>
```
{: file="App.vue"}

위 코드처럼 Teleport 태그를 이용하면 아주 깔끔하고 간단한 방법으로 Dialog를 만들 수 있습니다.  
예시 코드는 [여기][Vue Dialog]에서 확인 가능합니다.
# Vue2는 못써요?
안타깝지만 Vue2에서는 Element를 특정 위치로 보내는 기능이 기본적으로 내장 되어 있지 않습니다.  
하지만 다행스럽게도 Vue Community에서 [이 기능을 지원하는 라이브러리][Portal Vue]를 제작했습니다.

[React Portal]: https://ko.reactjs.org/docs/portals.html
[Dialog]: https://firebasestorage.googleapis.com/v0/b/univdev-github-io.appspot.com/o/dialog-example.gif?alt=media&token=37621902-5c60-4426-96bd-5b33948d68a5
[Vue Dialog]: https://github.com/univdev/vue-dialog
[Portal Vue]: https://portal-vue.linusb.org/