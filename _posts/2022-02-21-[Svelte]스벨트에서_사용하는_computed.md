---
title: "[Svelte] 스벨트에서 사용하는 Computed"
author:
  name: 박 찬영
  link: https://github.com/univdev
date: 2022-02-21 09:58:00 +0900
categories: [Tech, Svelte]
tags: [Svelte, Computed]
---
# Vuejs의 Computed
```Computed```는 [Vue.js][Vuejs]에서 사용되는 [컴포넌트의 Property][Vuejs Computed]입니다.  
```javascript
export default {
  computed: {
    sum() { // 3
      const { a, b } = this;
      return a + b;
    },
  },
  data() {
    return {
      a: 1,
      b: 2,
    };
  },
}
```
위 코드와 같이 ```computed``` Property 내부에 로직을 정의하면 내부에서 사용 된 상태값이 변경 될 때마다 해당 상태값도 같이 변경되어 반환되는 기능을 하고 있습니다.  
# Svelte의 Computed
Svelte에서는 ```Declarations```라는 이름으로 불립니다.  
```html
<script>
let number = 1;
$: pow = Math.pow(number, 2);
</script>

<div>
  {number}의 제곱은 {pow}입니다.
</div>
```
{: file="index.svelte"}
이런 식으로 ```$:``` 키워드를 사용하여 정의할 수 있습니다.

[Vuejs]: https://vuejs.org/
[Vuejs Computed]: https://kr.vuejs.org/v2/guide/computed.html