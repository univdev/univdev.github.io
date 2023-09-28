---
title: "[Vue] Cannot find module '...' or its corresponding type declarations."
author:
  name: 박 찬영
  link: https://github.com/univdev
date: 2022-04-26 16:30:00 +0900
categories: [Tech, Vue]
tags: [Vue]
---
# 이슈
```vue
<style scoped>

</style>

<template>
  <div class="app">
    <atom-button></atom-button>
  </div>
</template>

<script lang="ts">
import { defineComponent } from 'vue';
import AtomButton from '@/components/atoms/Button';

export default defineComponent({
  components: {
    AtomButton,
  },
});
</script>

```
```
Cannot find module '@/components/atoms/Button' or its corresponding type declarations.
```
Typescript Vue 프로젝트에서 ```.vue``` 없이 파일을 import하려고 하면 위와 같은 이슈가 발생합니다.
# 해결
```typescript
declare module '*.vue' {
  import type { DefineComponent } from 'vue'
  const component: DefineComponent<{}, {}, any>
  export default component
}
```
{: file="src/shims-vue.d.ts"}
```src``` 폴더 안에 ```shims-vue.d.ts``` 파일을 생성하고, 해당 파일에 ```.vue``` 파일에 대한 타입을 정의합니다.  
위 코드는 Vue3에서 사용할 수 있으며, Vue2는 아래 코드를 사용해야 합니다.
```typescript
declare module "*.vue" {
  import Vue from 'vue'
  export default Vue
}
```