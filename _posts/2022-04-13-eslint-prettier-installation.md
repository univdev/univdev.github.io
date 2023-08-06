---
title: 'ESlint와 Prettier 설치하기'
author:
  name: 박 찬영
  link: https://github.com/univdev
date: 2022-04-13 10:24:00 +0900
categories: [Tech, Talk]
tags: [ESLint, pritter, clean code]
---
# ESLint
ESLint는 코드를 보다 예쁘고 일관성있게 관리하기 위한 최소한의 제한사항입니다.  
VSCode 에디터를 사용하고 있다면 '저장 시 자동으로 포맷' 기능을 활용하여 ESLint에 비준하지 않는 코드 스타일로 작성하더라도 저장만하면 자동으로 에디터가 포맷을 맞춰주는 편리한 기능을 사용할 수 있습니다.
## 설치
ESLint를 간편하게 세팅하기 위한 패키지를 설치합니다. (이미 설치 되어 있다면 아래 명령어는 생략합니다.)
```
npm i -g eslint
```
ESLint를 설치하고 싶은 프로젝트의 루트에 진입한 다음 ```eslint --init``` 명령을 실행합니다.
## 초기화
```
❯ eslint --init
You can also run this command directly using 'npm init @eslint/config'.
? How would you like to use ESLint? … 
  To check syntax only
❯ To check syntax and find problems
  To check syntax, find problems, and enforce code style
```
다음과 같은 세 가지 선택지가 나오는데, 이는 아래와 같습니다.  
- 문법 오류만을 체크한다.
- 문법 오류를 체크하고 문제점을 찾는다.
- 문법 오류, 문제점을 탐색하고 지정한 코드 스타일을 강제한다.

ESLint 비준 엄격성을 체크하는건데, 기본값이 2번으로 맞춰져있으니 2번이 아마 가장 많은 사람들이 선택하는 설정이 아닐까 싶습니다.  
그런데 전 엄격한거 좋아하니 3번으로 설정합니다.

```
? What type of modules does your project use? … 
❯ JavaScript modules (import/export)
  CommonJS (require/exports)
  None of these
```
이번에는 프로젝트 유형을 물어봅니다.  
- import/export로 모듈을 내보내고 불러온다.
- require와 exports로 모듈을 내보내고 불러온다.
- 모듈 안써요

만약 Vue나 React 프로젝트를 개설하여 사용하고 계신 분들이라면 대부분 1번일겁니다.  
express 프로젝트에 적용하고 싶으신 분들은 대부분 2번을 선택할 것이라고 생각합니다. (모든 사람에게 해당되는 경우는 아닙니다.)  
프로젝트의 유형을 보고 결정하시면 됩니다.

```
? Which framework does your project use? … 
❯ React
  Vue.js
  None of these
```
무슨 프레임워크로 개발하는지 물어봅니다.  
React.js, Vue.js에 대한 설정값은 기본적으로 포함되어 있으나 그 외의 프레임워크로 개발을 한다면 **None of these**를 선택하면 됩니다.

```
Does your project use TypeScript? › No / Yes
```
타입 스크립트 쓰고 있냐는 얘기입니다.

```
? Where does your code run? …  (Press <space> to select, <a> to toggle all, <i> to invert selection)
✔ Browser
✔ Node
```
코드가 어디에서 실행되는거냐고 묻습니다.  
내가 Vue나 React를 사용하여 웹 페이지를 개발하고 있는 것이라면 브라우저에서 실행 되므로 'Browser'를 선택하시면 되고, Express와 같은 터미널에서 실행하는 프로그램의 경우 'Node'를 선택하시면 됩니다.

```
? How would you like to define a style for your project? … 
❯ Use a popular style guide
  Answer questions about your style
```
보편적으로 사용되는 코드 스타일을 불러올건지, 본인만이 사용하는 코드 스타일이 있는지 물어봅니다.  
ESLint 환경설정에 익숙하신 분들은 2번을 선택하시면 되지만, 대부분의 개발자들이 사용하는 코드 스타일에 익숙해지는게 범용적이고 좋겠죠? 저는 1번을 선택합니다.

```
? Which style guide do you want to follow? … 
❯ Airbnb: https://github.com/airbnb/javascript
  Standard: https://github.com/standard/standard
  Google: https://github.com/google/eslint-config-google
  XO: https://github.com/xojs/eslint-config-xo
```
어디서 만든 코드 스타일을 불러올건지 물어봅니다.  
아마 대부분의 개발자들은 Airbnb에서 만든 ESlint를 사용할 것이라고 생각합니다. 저도 여태까지 그렇게 개발을 해왔으니 1번을 선택합니다.

```
? What format do you want your config file to be in? … 
❯ JavaScript
  YAML
  JSON
```
ESLint 파일을 어떤 확장자로 생성할 것인지 물어봅니다.  
ESLint의 설정은 ```.eslintrc``` 파일로 생성되는데, 이 파일은 여러 확장자로 구성될 수 있습니다.  
가장 보편적으로 사용되는 확장자는 ```json```과 ```js```인데, ```json```의 경우 따옴표 맞춰주는게 귀찮으니 문법이 상대적으로 자유롭고 편한 ```javascript```를 선택합니다.

```
eslint-plugin-react@^7.28.0 @typescript-eslint/eslint-plugin@latest eslint-config-airbnb@latest eslint@^7.32.0 || ^8.2.0 eslint-plugin-import@^2.25.3 eslint-plugin-jsx-a11y@^6.5.1 eslint-plugin-react-hooks@^4.3.0 @typescript-eslint/parser@latest
? Would you like to install them now with npm? › No / Yes
```
마지막 질문입니다. **"내가 지금 위에 나열한 패키지들이 필요한데 저것들을 npm으로 설치해도 될까?"** 라고 묻는겁니다.  
거절할 이유 없겠죠? ```yes``` 누릅니다.
## 주의사항
이 글을 작성하는 시점에서 ```@types/react```의 최신 버전은 ```18.0.2```입니다.  
그런데 이 라이브러리를 통해 ESLint를 설치하면 이상하게 자꾸 ```@types/react```가 설치 안됐다고 오류가 표시됩니다.  
```18.0.1```로 다운그레이드하여 사용하면 정상적으로 적용 됩니다.
# Prettier
Prettier도 ESLint와 마찬가지로 일관성 있는 코드 스타일을 지키기 위한 제한 사항입니다.  
다만 ESLint보다 좀 더 범용적인 코드 스타일을 적용할 수 있습니다. 자세한 설명은 [아래 섹션](#prettier와-eslint의-차이)에 기재하겠습니다.
## 설정법
프로젝트 루트 폴더에 ```.prettierrc``` 파일을 생성합니다.  
```json
{
  "tabWidth": 2,
  "useTabs": false,
  "singleQuote": false,
  "printWidth": 80
}
```
{: file=".prettierrc"}
위 코드를 예시로 들 수 있는데, ```.prettierrc``` 파일은 JSON으로 이루어지며, [여기][Pretiter]에서 규칙을 볼 수 있습니다.
만약 프로젝트에 이미 ESLint가 설정 되어 있다면 ESLint에서 Prettier도 인식할 수 있도록 확장할 필요가 있습니다.
```js
module.exports = {
  env: {
    browser: true,
    es2021: true,
  },
  extends: [
    'plugin:@next/next/recommended',
    "eslint:recommended",
    'airbnb',
  ],
  parser: '@typescript-eslint/parser',
  parserOptions: {
    ecmaFeatures: {
      jsx: true,
    },
    ecmaVersion: 'latest',
    sourceType: 'module',
  },
  plugins: [
    'react',
    '@typescript-eslint',
  ],
  rules: {
    'react/react-in-jsx-scope': 'off',
    'react/jsx-filename-extension': 'off',
    'react/function-component-definition': [2, { namedComponents: 'arrow-function' }],
    'react/jsx-props-no-spreading': 'off',
  },
};
```
{: file=".eslintrc.js"}
위 코드는 제가 적용하고 있는 ESLint입니다. ```extends``` 항목을 보시면 ```eslint:recommended```라고 적혀있는 것을 확인하실 수 있는데, 이 부분을 추가하여 ```.prettierrc``` 설정도 ESLint에 추가할 수 있습니다.  
물론 이를 확장하기 위하여 추가적인 패키지 설치도 필요합니다. 아래 명령어를 입력하시면 됩니다.  
**NPM**
```
npm i prettier
```
**Yarn**
```
yarn add prettier
```
# Prettier와 ESLint의 차이
ESLint는 사용하는 프레임워크와 개발 툴에 따른 코드 스타일을 확장하여 적용할 수 있습니다.  
아래 코드로 예를 들겠습니다.
```tsx
const App: FC<Props> = () => {
  return (
    <div />
  );
};

export default App;
```
위 코드의 경우 로직 상 문제는 전혀 없지만, React ESLint의 기본 값을 준수한다면 에러가 나올 것입니다.  
React의 컴포넌트들은 기본적으로 ```import React from 'react'```가 위에 무조건 포함 되도록 설정 되어 있기 때문입니다.  
이처럼 사용하는 개발 툴에 대해 종속적인 설정을 하는데 사용되는데 비해, Prettier는 어떤 프로젝트에 갖다놔도 적용할 수 있는 포맷팅에 사용되기 때문입니다.  
들여쓰기, 탭 문자를 공백으로 할지, 세미콜론을 끝에 찍을 것인지, 작은 따옴표만을 사용할 것인지, 한 줄에 들어갈 수 있는 코드는 몇 자 제한인지 등을 설정할 수 있습니다.

[Pretiter]: https://prettier.io/docs/en/options.html
