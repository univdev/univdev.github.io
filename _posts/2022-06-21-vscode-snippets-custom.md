---
title: "VSCode User Snippet 알아보기"
author:
  name: "박 찬영"
  link: "https://github.com/univdev"
date: "2023-06-21 10:19:00 +0900"
categories: ["Tech", "Talk"]
tags: ["vscode"]
---

# User Snippet?
개발을 하다보면 비슷한 형식의 코드를 반복적으로 작성해야 할 때가 자주 있습니다.  
대표적으로 Redux의 Slice나 Hook과 같은 코드를 작성할 때 자주 이러한 상황이 펼쳐집니다.
{% raw %}
```ts
interface UseExampleArguments {
}

export const useExample = (args: UseExampleArguments) => {
  
};
```
```ts
import { createSlice } from '@reduxjs/toolkit';

const initialState = {

};

export type AuthSliceState = typeof initialState;

export const authSlice = createSlice({
  name: 'AuthSlice',
  initialState,
  reducers: {

  },
  extraReducers(builder) {

  },
});
```
{% endraw %}
명령어를 지정하여 자주 사용 되는 코드 형식을 불러오는 도구를 User Snippet이라고 합니다.
# 어떻게 쓸까요?
Mac의 경우 `Shift` + `Command` + `p` 를 누르면 VSCode의 다양한 기능들을 설정할 수 있는 메뉴가 표시 됩니다.  
![VSCode Context Menu](/assets/post_images/2022-06-21-vscode-snippets-custom/2023-06-21-10-33-28.png)

여기서 `User snippet` 키워드로 검색하면 하나의 메뉴가 나오게 됩니다.  
이 메뉴를 클릭합니다.
![User Snippet](/assets/post_images/2022-06-21-vscode-snippets-custom/2023-06-21-10-34-48.png)

Snippet을 적용할 확장자를 선택해줍니다.  
![Snippet 확장자](/assets/post_images/2022-06-21-vscode-snippets-custom/2023-06-21-10-35-43.png)

확장자를 선택하면 아래와 같이 JSON 파일이 열리게 됩니다.
```json
{}
```

우선 아래와 같이 작성 해봅시다.
{% raw %}
```json
{
  "react hook": {
    "prefix": "rhook",
    "body": []
  }
}
```
{% endraw %}
우선 JSON의 각 `key`는 해당 코드 조각이 어떤 이름으로 불릴지 작성합니다.  
위 예시에서는 `react hook`이라는 이름의 코드 조각을 생성 한 것입니다.

아래는 각 프로퍼티의 역할입니다.
- `prefix`: 이 코드 조각을 불러오기 위한 명령어를 설정합니다. 위 예시에서는 `rhook`이라고 입력하면 실행이 되는 형태입니다.
- `body`: 이 코드 조각을 실행 했을 때 파일에 작성 될 코드를 입력합니다. 코드는 배열로 저장하며, 배열의 각 아이템이 코드의 행이 됩니다.

위에서 작성했던 `useExample`을 `body`에 삽입한다면 아래와 같은 형태가 됩니다.
{% raw %}
```json
{
  "react hook": {
    "prefix": "rhook",
    "body": [
      "interface UseExampleArguments {",
      "}",
      "",
      "export const useExample = (args: UseExampleArguments) => {",
      "\t",
      "};"
    ]
  }
}
```
{% endraw %}
이 Snippet을 저장하고 `.ts` 문서에서 `rhook`을 적고 엔터를 누르면 위에서 봤던 `useExample`이 빠르게 완성 됩니다.
# 자리 표시자
Snippet을 작성하면 기본적으로 마우스 커서가 해당 코드 조각의 가장 아래에 위치합니다.
```ts
interface UseExampleArguments {
}

export const useExample = (args: UseExampleArguments) => {
  
}; // 커서는 여기에
```

하지만 Snippet을 사용하는 대부분의 사람들은 아래와 같은 절차로 코드를 편집하게 됩니다.
- `useExample`을 내가 사용할 이름으로 변경하기
- `useExample` 함수 내부에 코드 작성하기

이렇게 하기 위해서는 마우스로 직접 해당 위치까지 커서를 이동해야 한다는 단점이 있습니다.  
Snippet은 코드 조각을 불러오고나면 커서를 어디에 맞춰야 하는지도 설정할 수 있습니다.

{% raw %}
```json
{
  "react hook": {
    "prefix": "rhook",
    "body": [
      "interface ${1:useExample}Arguments {",
      "}",
      "",
      "export const ${1:useExample} = (args: ${1:UseExample}Arguments) => {",
      "\t${2}",
      "};"
    ]
  }
}
```
{% endraw %}
위의 코드 조각에는 아까 전에는 없었던 `${1}`과 `${2}`가 추가 되었습니다. **이는 앞으로 '자리 표시자'라고 불립니다.**  
- `${n}`: `n`번째로 커서를 위치 시킨다는 의미이며, 만약 동일한 번호가 연속해서 등장한다면 다중 선택이 됩니다. `Tab`을 통해 다음 인덱스에 포커싱을 할 수 있습니다.
- `${n:defaultValue}`:`:defaultValue` 옵션을 추가하여 해당 칸에 기본적으로 삽입 할 데이터를 설정할 수 있습니다.

# 변수
자리 표시자 기능을 활용하여 `UseExample`을 손쉽게 수정할 수 있게 되었습니다만 이름을 수정해야 하는 편집 과정이 여전히 번거롭습니다.  
Snippet에는 이러한 과정을 더욱 간편하게 바꿀 수 있는 변수들이 존재합니다.

여기서는 자주 사용 되는 변수 몇 가지만 짚고 넘어가겠습니다.  
**자세한 내용은 [여기](https://code.visualstudio.com/docs/editor/userdefinedsnippets#_variables)를 참고해주세요.**

- `TM_FILENAME`: 확장자가 포함 된 현재 파일명입니다.
- `TM_FILENAME_BASE` 확장자가 제거 된 현재 파일명입니다.
- `TM_SELECTED_TEXT`: 현재 선택 된 텍스트입니다.

만약 위와 같은 변수를 사용하여 `usePost.ts` 파일에 `usePost`라는 hook을 만들고자 한다면 아래와 같이 Snippet을 작성할 수 있습니다.
{% raw %}
```json
{
	"react hook": {
		"prefix": "rhook",
		"body": [
			"interface ${TM_FILENAME_BASE}Arguments {",
			"}",
			"",
			"export const ${TM_FILENAME_BASE} = (args: ${TM_FILENAME_BASE}Arguments) => {",
			"\t${2}",
			"};",
			""
		]
	},
}
```
{% endraw %}
# 변수 변형
처음에 비하면 많이 편해졌습니다만 아직 한 가지 불편한 점이 남아 있어요.  
`interface`와 `args: type`을 설정할 때는 `UseExample`이라는 `PascalCase`로 나타내고 함수명은 `useExample`이라는 `camelCase`로 나타내야 한다는 점인데요.  
변수 변형 기능을 사용하면 이러한 문제 역시도 해결이 가능합니다.

{% raw %}
```json
{
	"react hook": {
		"prefix": "rhook",
		"body": [
			"interface ${TM_FILENAME_BASE/(.*)/${1:/pascalcase}/}Arguments {",
			"}",
			"",
			"export const ${TM_FILENAME_BASE/(.*)/${1:/camelcase}/} = (args: ${TM_FILENAME_BASE/(.*)/${1:/pascalcase}/}Arguments) => {",
			"\t${2}",
			"};",
			""
		]
	}
}
```
{% endraw %}

변수 변형의 규칙은 아래를 따릅니다.  
```
${TM_FILENAME/(.*)\\..+$/$1/}
  |           |         |  |
  |           |         |  |-> no options
  |           |         |
  |           |         |-> references the contents of the first
  |           |             capture group
  |           |
  |           |-> regex to capture everything before
  |               the final `.suffix`
  |
  |-> resolves to the filename

```
`${text/regex/capture/option}` 형태로 기술할 수 있습니다.  
- `text`: 해당 변수에 삽입 될 데이터입니다. 위의 경우에서는 `TM_FILENAME_BASE` 변수입니다.
- `regex`: 텍스트를 편집하기 위한 규칙을 지정하는 정규표현식이 들어갑니다. `(.*)`는 모든 형태의 문자를 전부 선택한다는 의미입니다.
- `capture`: 위 정규표현식으로 캡처 된 그룹의 인덱스를 작성합니다.
- `option`: 지정된 인덱스를 편집하기 위한 규칙을 지정합니다.

# 마치며
주로 사용하는 Snippet 편집 규칙에 대해서 설명이 모두 끝났습니다!  
만약 Extension 개발 계획이 있으신 분들이라면 여기서 설명한 기능 외에도 정말 많은 기능이 있으니 [공식 문서](https://code.visualstudio.com/docs/editor/userdefinedsnippets)를 참조 해주시면 되겠습니다.
