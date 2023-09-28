---
title: "'구르미랑 반모' i18n(국제화) 적용기"
author:
  name: 박 찬영
  link: https://github.com/univdev
date: 2022-04-25 17:27:00 +0900
categories: [Tech, Talk]
tags: [i18n]
---
# i18n
> i18n은 Internatinalization의 약자입니다. '아이에이틴엔' 혹은 '아이엷여덜엔' 등으로 발음합니다.  

국제화란, 인터넷 서비스 등이 특정 국가나 시장에 진출하기 위해여 대상 문화권 사람들이 어려움 없이 서비스를 이용할 수 있도록 하는 작업입니다.  
디자인, 번역, 텍스트 진행방향 변경 등이 모두 국제화 과정에 포함 됩니다.
# 개요
회사에서 개발중인 서비스, ['구르미랑 반모'][구르미랑] 서비스의 브랜드 페이지가 국제화 대상 서비스입니다.  
본래 해당 프로젝트는 텍스트가 정적으로 소스코드에 포함 되어 작성 되었으나, 영어권 국가 서비스 론칭을 위해 국제화 작업이 필요하다는 기획팀의 요구사항이 있었습니다.
# @nuxtjs/i18n
## 사용 이유
본 프로젝트는 SEO 최적화를 위해 Nuxtjs로 개발 되어 있었기에 [Nuxtjs][Nuxtjs] 환경에서 사용할 수 있는 국제화 라이브러리가 필요했습니다.  
그래서 몇 분 간의 인터넷 서핑 결과 Nuxtjs 전용으로 포팅 된 ```@nuxtjs/i18n``` 모듈을 발견하게 되었고, 이를 적용하였습니다.
## 초기 설정
우선 한국어 언어팩을 만들어야 합니다.  
```lang/ko.js``` 파일을 만들고, 아래와 같이 작성합니다.
```javascript
export default {
  pages: {
    main: {
      first: {
        caption: '반려인의 하나된 마음',
        title: '건강하고 행복하게<br>오랫동안 함께하는 반려생활',
      },
    }
  }, // 실제 언어팩의 일부 발췌
};
```
{: file="lang/ko.js"}
한국어 언어팩을 만들었다면 각국의 언어팩을 통합으로 관리 할 ```index.js``` 파일을 작성합니다.  
앞으로 새로운 국가의 언어팩이 추가 된다면 이 페이지에서도 해당 파일을 import하여 export해야 합니다.
```javascript
import ko from './ko';

export default {
  ko,
};
```
{: file="lang/index.js"}
그 다음 언어팩과 ```@nuxtjs/i18n``` 모듈을 연결하기 위해서 ```nuxt.config.js``` 파일에 아래와 같이 작성합니다.
```javascript
import messages from './lang';

export default {
  modules: [
    // ...
    '@nuxtjs/i18n',
  ],
  i18n: {
    detectBrowserLanguage: { // 브라우저의 언어 설정에 맞춰 기본 언어를 설정함
      useCookie: false, // true면 언어 설정에 관한 내용을 쿠키에 저장하여 할당할 수 있음
    },
    defaultLocale: 'ko', // 기본 언어 설정 (한국어)
    locales: [ // 지원할 언어의 명세를 작성합니다. 한국어와 영어만 지원할 예정이니 ko, en을 작성했습니다.
      { code: 'ko', name: 'KOR', iso: 'ko-KR', dir: 'ltr' },
      { code: 'en', name: 'ENG', iso: 'en-US', dir: 'ltr' },
    ],
    vueI18n: {
      fallbackLocale: 'ko', // 만약 설정 된 국가코드와 매칭되는 언어팩 데이터가 없다면 대체어로 한국어 언어팩에서 데이터를 추출합니다.
      messages, // 위에서 작성한 lang/index.js를 messages라는 key로 할당합니다.
    },
  },
};
```
{: file="nuxt.config.js"}
이렇게 하면 기본적인 i18n 모듈 설정이 끝났습니다.
## 사용해보기
이제 실제 페이지에 정적으로 기입 되어 있는 텍스트를 동적으로 변경합니다.
```vue
<template lang="pug">
  section.content-wrap
    .main-row-box.st-main-01
      .inner-base-box
        p.p1.wow.fadeInLeft(data-wow-delay='.5s') 반려인의 하나된 마음
</template>
<!-- 실제 프로젝트의 코드 일부 발췌 -->
```
{: file="pages/index.vue"}
기존에 이렇게 정적으로 기입되어 있던 텍스트를 아래와 같이 변경해야 합니다.
{%raw%}
```vue
<template lang="pug">
  section.content-wrap
    .main-row-box.st-main-01
      .inner-base-box
        p.p1.wow.fadeInLeft(data-wow-delay='.5s') {{ $t('pages.main.first.caption') }}
</template>
<!-- 실제 프로젝트의 코드 일부 발췌 -->
```
{%endraw%}
{: file="pages/index.vue"}
위 코드는 아래와 같은 구조의 언어팩에서 해당 텍스트를 가져오는 것을 의미합니다.
```javascript
export default {
  pages: {
    main: {
      first: {
        caption: '반려인의 하나된 마음',
      },
    },
  },
};
```
{: file="lang/ko.js"}
## 설정 언어 변경
언어팩을 적용했으니 활성화 하고싶은 언어팩을 설정할 수 있는 기능이 필요할 수 있습니다.  
본 프로젝트에는 우측 상단에 아래와 같은 버튼을 배치했습니다.

![언어 설정][Languages]

### 설정할 수 있는 언어팩 목록 불러오기
```javascript
export default {
  computed: {
    locales() {
      return this.$i18n.locales;
    },
  },
};
```
```computed``` Property를 활용하면 ```nuxt.configs.js```에서 설정했던 ```locales```를 Array 형태로 불러올 수 있습니다.
{%raw%}
```vue
<template lang="pug">
  .lang-box
    a(
      v-for="(locale, key) in locales"
      :key="key"
      href="#"
      @click.prevent.stop="onChangeLanguage(locale.code)") {{ locale.name }}
</template>
```
{%endraw%}
불러온 ```locales``` 정보를 기반으로 UI를 배치합니다.  
그런데 단순히 UI를 배치하는 것뿐 아니라 선택 된 언어에 대응되는 버튼에 특수 효과를 부여하고 싶을 수 있습니다.
```javascript
export default {
  computed: {
    currentLocale() {
      return this.$i18n.locale;
    },
  },
};
```
```$18n.locale``` Property에는 현재 선택 된 언어의 ```code``` 정보가 담겨 있습니다.  
이를 기반으로 ```class```를 할당하여 특수 효과를 넣을 수 있습니다. 아래와 같이 말이죠.
{%raw%}
```vue
<template lang="pug">
  a(
    v-for="(locale, key) in locales"
    :key="key"
    :class="{ 'on': locale.code === currentLocale }"
    href="#"
    @click.prevent.stop="onChangeLanguage(locale.code)") {{ locale.name }}
</template>
```
{%endraw%}
그럼 이제 언어를 변경할 수 있는 UI도 만들었으니 실제로 언어를 변경할 수 있게끔 해줘야겠죠?
```javascript
export default {
  methods: {
    onChangeLanguage(code) {
      this.$i18n.setLocale(code);
    },
  },
}
```
```$18n.setLocale``` Method를 사용하시면 매개변수로 전달 된 코드에 맞는 언어팩으로 변경 됩니다.  
그런데 ```$i18n.setLocale``` 함수를 사용하고나면 언어를 변경할 때 URI의 Segment 부분이 좀 특이하게 바뀝니다.  
기존에는 ```/usage``` 페이지로 되어 있었는데 ```$i18n.setLocale('en')```을 사용하니까 ```/en/usage```로 변경된 것을 볼 수 있습니다.  
```@nuxtjs/i18n``` 모듈은 설정 된 언어 코드에 기반해서 URI를 자동적으로 매핑해줍니다. **(검색엔진 최적화를 위해서예요!)**

아주 만약, 언어팩 설정에 따라 동일한 페이지의 경로를 다르게 보여주고 싶다면 [이 방법][custom-path]을 고려해보실 수도 있어요.
## nuxt-link 수정하기
기존에 작성 된 ```nuxt-link```로 네비게이션을 이용하여 ```/en/usage``` 페이지에서 ```/en/careticons``` 페이지로 이동하고자 했는데 네비게이션을 클릭하니까 ```en/usage```에서 ```/careticons``` 페이지로 이동하는 문제가 발견되었습니다.  
{%raw%}
```vue
<template>
  <nuxt-link
    v-for="locale in availableLocales"
    :key="locale.code"
    :to="switchLocalePath(locale.code)">{{ locale.name }}</nuxt-link>
</template>
```
{%endraw%}
[공식 문서][nuxt - i18n lang-switcher]를 참고하니 ```switchLocalePath```라는 함수를 이용해야 하는 것 같습니다.  
그런데 이미 모든 페이지에서 네비게이션을 사용하고 있기에 이를 모두 수정하기는 매우 번거롭고, ```nuxt-link``` 태그마다 ```switchLocalePath``` 함수를 사용하여 경로를 래핑해주는 것도 별로 보기 좋지 않습니다.

그렇기에 ```switchLocalePath``` 함수를 기본적으로 래핑해주는 컴포넌트를 따로 만들어야 합니다.
```components/I18nLink.vue``` 파일을 만들고, 아래와 같이 작성해줍니다.
```vue
<template lang="pug">
  nuxt-link(
    v-bind="{ ...$attrs, ...$props }"
    :to="localePath(to)")
    slot
</template>

<script>
export default {
  props: {
    to: {
      type: [String, Object],
      require: false,
      default: null,
    },
  },
};
</script>
```
{: file="components/I18nLink.vue"}
컴포넌트를 위와 같이 제작하면 ```nuxt-link``` 컴포넌트에서 사용 가능한 모든 ```Property```를 별도의 수정 없이 그대로 활용하실 수 있습니다.  
이제 Visual Studio Code의 파일 전체 탐색(```Command + Shift + F```)을 이용하여 ```nuxt-link```를 모두 찾아내고, ```i18n-link```로 대체해줍니다.

[구르미랑]: https://brand.goormirang.com/
[Nuxtjs]: https://nuxtjs.org
[Languages]: https://user-images.githubusercontent.com/26304279/165042499-08b8be11-0f9a-4941-be99-9c48dfbb6ba4.png
[custom-path]: https://i18n.nuxtjs.org/custom-paths/
[nuxt - i18n lang-switcher]: https://i18n.nuxtjs.org/lang-switcher/