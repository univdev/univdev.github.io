---
title: "Vite 환경에서 .env 활용하기"
author:
  name: 박 찬영
  link: https://github.com/univdev
date: 2022-05-10 18:37:00 +0900
categories: [Tech, Vue]
tags: [Vite, Vue]
---
# Vite
```vite```는 **번들러**의 일종입니다.  
ES6 문법을 따르기에 ```Webpack```보다 상대적으로 빠른 것이 장점이예요.  
```Vue3```와 ```Nuxt3```는 기본적으로 ```Vite```를 지원하도록 설계되어 장래성이 뛰어난 개발도구입니다.
# Environment Variables
**Environment Variables**는 프로젝트 내에서 사용할 **환경변수**입니다.  
**환경변수**란, 현재 프로젝트의 구동 환경 및 서버 도메인 등 환경설정에 관한 변수를 말합니다.  
**NodeJS** 환경에서 무언가를 개발하신적이 있다면 한번 쯤 환경변수를 써보신적이 있으실텐데, 개발모드 / 프로덕션모드를 구분짓기 위하여 ```process.env.NODE_ENV```를 사용해보셨다면 **환경변수를 사용해보신 경험이 있으신겁니다.**
# 배경
저는 여태까지 ```Vue2```를 메이저 프레임워크로 사용했기 때문에 ```process.env```로 환경변수를 사용했습니다.  
그런데 이번에 토이 프로젝트를 ```Vue3 + Vite``` 환경으로 제작하던 도중 ```process.env```를 사용하니 ```process is not defined``` 에러가 뜨기 시작했습니다.

직감적으로 **"번들러가 바뀌어서 그런가?"**라고 생각 되었고, ```vite```에서 환경변수를 설정하는 방법을 찾기 시작했습니다.
# Vite에서 환경변수 설정하기
## 환경변수 디렉토리 설정
> 본 섹션은 [여기][Vite 환경변수 설정]를 참조했습니다.

```javascript
module.exports = {
  envDir: './src/env',
}
```
{: file="vite.config.ts"}
```vite.config.js``` 파일에서 ```envDir``` 프로퍼티를 활용하여 ```.env``` 파일이 저장 될 디렉토리를 설정합니다.  
위 코드대로라면 ```src/env``` 폴더 안에 ```.env``` 파일을 생성해주면 되겠네요!
## 환경변수 파일 생성
```Vite```는 다양한 상황에서 능동적으로 대응할 수 있도록 다양한 타입의 ```.env``` 파일을 지원합니다.
```
.env                # 모든 상황에서 로드되는 기본 파일
.env.local          # 로컬에서만 갖고있는 파일입니다. Git에 업로드하는 과정에서 제외됩니다.
.env.[mode]         # 프로젝트가 실행 된 환경에 따라 로드 됩니다.
.env.[mode].local   # 프로젝트가 실행 된 환경에 따라 로드 됩니다. 로컬에만 존재하며, Git에 업로드하는 과정에서 제외됩니다.
```
```.env``` 파일은 어떤 상황에서나 읽을 수 있는 환경변수를 기재할 수 있습니다.  
```.env.[mode]```는 ```development``` 혹은 ```production```와 같은 **프로젝트의 구동 환경**에 따라 서로 다른 환경 변수를 할당하려 할 때 사용합니다. ```import.meta.env.MODE```에 따라서 ```.env.[MODE]```가 할당 되며, 이렇게 할당 된 환경변수는 ```.env``` 파일로 선언 된 환경변수보다 **더 높은 우선순위를 차지합니다.**
## 환경변수 정의
```
GITHUB_REPOSITORY_URI = "https://github.com/univdev/text-generator"
VITE_API_SERVER = "https://example.com/api"
```
{: file=".env"}
```변수명 = "값"``` 문법대로 작성하시면 해당 이름의 환경변수를 사용하실 수 있습니다.    
다만, ```Vite```로 구동되는 실제 프로젝트에서 사용하기 위해서는 환경변수명 앞에 ```VITE_```를 반드시 붙여주셔야 합니다.  
접두사 ```VITE_```를 붙이지 않을 경우, ```VITE```가 참조할 수 없는 환경 변수가 됩니다.

실제로 사용하실 때는 ```import.meta.env.VITE_API_SERVER```로 사용하실 수 있습니다.
## 타입스크립트 정의
```typescript
interface ImportMetaEnv {
  readonly VITE_API_SERVER: string
}

interface ImportMeta {
  readonly env: ImportMetaEnv
}
```
{: file="src/types/import.meta.d.ts"}
위와 같이 ```d.ts``` 파일을 정의해주면 ```VSCode```에서 타입 추론이 가능해집니다.  
```json
{
  "include": [
    "src/types/*.d.ts"
  ],
}
```
{: file="tsconfig.json"}
만약 ```d.ts``` 폴더가 정의가 되어있지 않다면 ```tsconfig.json``` 파일에서 위와 같이 정의해야 합니다.  
예시에서는 ```src/types``` 폴더 안에 파일을 정의했으니 ```src/types/*.d.ts```로 ```include``` 했습니다.

[Vite 환경변수 설정]: https://vitejs-kr.github.io/guide/env-and-mode.html