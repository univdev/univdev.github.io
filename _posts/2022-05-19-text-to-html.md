---
title: 평문을 HTML로 변환하기
author:
  name: 박 찬영
  link: https://github.com/univdev
date: 2022-05-19 11:32:00 +0900
categories: [Tech, Talk]
tags: [Javascript]
---
# 기능
```textarea``` 형태의 에디터에서 작성 된 글은 평문으로 작성됩니다.  
이를 HTML 문서에서 보여주게 된다면 줄내림 등의 양식이 적용되지 않기에, 이를 변환시켜주는 함수입니다.  
덤으로 ```https://www.naver.com``` 같은 링크가 들어오면 ```a``` 태그를 래핑하는 기능도 포함 되었습니다.
# 코드
{% raw %}
```javascript
/** text2html
 * 텍스트를 html 형태로 변환시킵니다. 줄내림, 링크 자동 변환 등을 지원합니다.
 * @author: 박 찬영
 * @param {text} string
 * @return {html} string
 */

const text2html = text => {
  let html = text;
  html = uri2link(html);
  html = nl2br(html);
  return html;
};

export const uri2link = text => {
  const linkRegex = /(http|https):\/\/(\w+:{0,1}\w*@)?(\S+)(:[0-9]+)?(\/|\/([\w#!:.?+=&%@!\-\/]))?/gm;
  return text.replace(linkRegex, '<a href="$1">$1</a>');
};

export const nl2br = text => {
  const nlRegex = /\n/gm;
  return text.replace(nlRegex, '<br />');
};

export default text2html;
```
{% endraw %}