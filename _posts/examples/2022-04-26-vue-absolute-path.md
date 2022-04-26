---
title: "[Vue] 절대 경로 import"
author:
  name: 박 찬영
  link: https://github.com/univdev
date: 2022-04-26 16:13:00 +0900
categories: [Tech, Vue]
tags: [Vue]
---
# 절대 경로
절대 경로에 대한 자세한 설명은 [이 문서][절대 경로]를 참고해주세요!  
# 절대 경로 추가
```javascript
import { defineConfig } from 'vite';
import vue from '@vitejs/plugin-vue';

const path = require('path');

// https://vitejs.dev/config/
export default defineConfig({
  plugins: [vue()],
  resolve: {
    alias: {
      '@': path.resolve(__dirname, './src'),
    },
  },
})
```
{: file="vite.config.js"}
만약 Vite로 설치하지 않으신 분들은 ```vue.config.js``` 파일을 확인해주세요.
# VSCode 자동완성 설정
위 설정만 한다면 파일을 import할 수 있지만, VSCode는 이를 인지하지 못하기 때문에 경로를 자동으로 완성해주지 않습니다.
```json
{
  "compilerOptions": {
    "baseUrl": ".",
    "paths": {
      "@/*": ["./src/*"],
    }
  }
}
```
{: file="tsconfig.json"}
만약 Typescript 프로젝트가 아니라면 ```jsconfig.json```에 정의해주세요.

위 코드를 작성해주시면 VSCode도 절대 경로를 인식할 수 있습니다.

[절대 경로]: https://www.univdev.page/posts/react-absolute-import/