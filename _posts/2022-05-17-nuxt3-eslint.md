---
title: "내가 쓰는 Nuxt3 ESLint 설정"
author:
  name: 박 찬영
  link: https://github.com/univdev
date: 2022-05-17 10:50:00 +0900
categories: [Tech, Nuxt]
tags: [Nuxt3, ESLint]
---
# 패키지 설치
## yarn
```
yarn add -D eslint eslint-plugin-vue typescript @typescript-eslint/eslint-plugin @typescript-eslint/parser vite-plugin-eslint @nuxtjs/eslint-config-typescript
```
## npm
```
npm i -D eslint eslint-plugin-vue typescript @typescript-eslint/eslint-plugin @typescript-eslint/parser vite-plugin-eslint @nuxtjs/eslint-config-typescript
```
# .eslintrc
```json
{
  "env": {
      "browser": true,
      "es2021": true,
      "node": true
  },
  "extends": [
      "eslint:recommended",
      "plugin:vue/vue3-essential",
      "plugin:@typescript-eslint/recommended",
      "@nuxtjs/eslint-config-typescript"
  ],
  "parserOptions": {
      "ecmaVersion": "latest",
      "parser": "@typescript-eslint/parser",
      "sourceType": "module"
  },
  "plugins": [
      "vue",
      "@typescript-eslint"
  ],
  "rules": {
    "brace-style": ["error", "1tbs"],
    "semi": ["error", "always"],
    "vue/no-v-model-argument": "off",
    "vue/component-tags-order": [
        "error",
        {
            "order": ["style", "template", "script"],
        }
    ],
  }
}
```
{: file=".eslintrc"}