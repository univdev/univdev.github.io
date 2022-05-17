---
title: "텍스트 안에 있는 URL에 하이퍼링크 적용하기"
author:
  name: 박 찬영
  link: https://github.com/univdev
date: 2022-05-17 13:55:00 +0900
categories: [Tech, Talk]
tags: [Regexp]
---
# 개요
네이버 블로그, 티스토리 등 고급 에디터를 지원하는 서비스에는 게시글을 작성할 때 링크를 포함시키면 해당 링크에 ```<a>``` 태그가 자동으로 걸리는 기능이 있습니다.
이 기능을 구현하기 위해서는 평문 속 URI를 찾고, ```replace``` 시켜야 합니다.
# 소스코드
```vue
<template>
  <div class="index">
    <div
      class="html"
      v-html="html"
    />
  </div>
</template>

<script lang="ts">
import { defineComponent } from 'vue';

export default defineComponent({
  name: 'HomePage',
  setup () {
    const text = ref('안녕하세요? https://www.naver.com 입니다. https://www.google.com 은 어떠세요?');
    const html = computed(() => {
      const link = new RegExp(/((https|http):\/\/(\w+(\.?))+)/, 'gm');
      return text.value.replace(link, '<a href="$1">$1</a>');
    });
    return {
      html
    };
  }
});
</script>
```
{: file="pages/index.vue"}

위와 같이 작성하면 ```text``` 변수 안에 있는 텍스트에서 ```((https|http):\/\/(\w+(\.?))+)``` 형태로 작성 된 텍스트를 탐색하고, ```<a></a>```로 감싸게 됩니다.