---
title: 내가 주로 쓰는 Next.js ESLint 설정
author:
  name: 박 찬영
  link: https://github.com/univdev
date: 2022-11-18 09:58:00 +0900
categories: ["Tech", "Next.js"]
tags: ["Next.js", "ESLint"]
---
# Package 설치
더욱 정교한 Formatting을 위해 플러그인을 설치합니다.  
```
yarn add -D eslint-plugin-simple-import-sort eslint-plugin-unused-imports

# or

npm install -D eslint-plugin-simple-import-sort eslint-plugin-unused-imports
```

## eslint-plugin-simple-import-sort
import한 Package를 알파벳 순서대로 Sorting 하고, 절대경로로 import 된 그룹과 상대경로로 import 된 그룹을 자동으로 분리시켜 줍니다.
```ts
import React from "react";
import Button from "../Button";

import styles from "./styles.css";
import type { User } from "../../types";
import { getUser } from "../../api";

import PropTypes from "prop-types";
import classnames from "classnames";
import { truncate, formatNumber } from "../../utils";
```
{: file="App.tsx" }

⬇️

```ts
{% raw %}
import classnames from "classnames";
import PropTypes from "prop-types";
import React from "react";

import { getUser } from "../../api";
import type { User } from "../../types";
import { formatNumber, truncate } from "../../utils";
import Button from "../Button";
import styles from "./styles.css";
{% endraw %}
```
{: file="App.tsx" }

## eslint-plugin-unused-imports
import한 Package를 실제로 사용하지 않았을 때 해당 Package의 Import 구문을 자동으로 제거해줍니다.

```tsx
{% raw %}
import React from 'react'
import _ from 'lodash';

export const App = React.Memo(() => {
  return (
    <div className="App"></div>
  ));
}
{% endraw %}
```
{: file="App.tsx" }

⬇️

```tsx
{% raw %}
import React from 'react'

export const App = React.Memo(() => {
  return (
    <div className="App"></div>
  ));
}
{% endraw %}
```
{: file="App.tsx" }

# ESLint 규칙 작성
```json
{% raw %}
{
  "extends": "next",
  "plugins": [
    "eslint-plugin-simple-import-sort",
    "unused-imports"
  ],
  "rules": {
    "react/no-unescaped-entities": "off",
    "@next/next/no-page-custom-font": "off",
    "react/self-closing-comp": ["error", {
      "component": true,
      "html": true
    }],
    "react/jsx-first-prop-new-line": "error",
    "comma-dangle": ["error", {
      "arrays": "always-multiline",
      "objects": "always-multiline",
      "imports": "never",
      "exports": "never",
      "functions": "never"
    }],
    "indent": ["error", 2],
    "quotes": ["error", "single"],
    "eol-last": "error",
    "semi": ["error", "always"],
    "simple-import-sort/imports": "error",
    "simple-import-sort/exports": "error",
    "no-unused-vars": ["error", { "vars": "all", "args": "none", "ignoreRestSiblings": false }],
    "unused-imports/no-unused-imports-ts": ["error"],
    "comma-spacing": ["error", { "before": false, "after": true }]
  }
}
{% endraw %}
```
{: file=".eslintrc.json" }