---
title: "Safari SVG Blurry 현상 해결 방법"
author:
  name: "박 찬영"
  link: "https://github.com/univdev"
date: 2022-12-13 15:47:00 +0900
categories: [Tech, Talk]
tags: [SVG, Safari]
---
![Safari SVG Blurry 해결 방법](/assets/post_images/2022-12-13-safari-svg-blurry/20230111094752.png)
# 들어가며
회사에서 서비스 되는 앱에서 사용할 WebView 페이지를 작업하던 중 발견한 이슈입니다.  
iOS 앱에서 WebView 페이지를 접속하면 SVG로 추출한 이미지인데도 불구하고 화질 저하가 일어난다는 기묘한 이슈였습니다.

구글에 Safari SVG 화질 저하에 대해 검색해보니 꽤 오래 전부터 이 이슈에 대해 논의 되고 있었다는 것을 알 수 있었습니다.  
화질 저하가 얼마나 심한지 알아보시려면 [이 페이지][예시]에 Chrome과 Safari 각각 접속하셔서 화질 차이를 보신다면 바로 아실 수 있을거예요.
# 해결 방법
SVG 파일을 `<img />` 태그가 아니라 `<object />` 태그로 호출하면 해결 됩니다.

```html
<object
  data="/image.svg"
></object>
```

[예시]: http://kb.zeitweisen.com/Index.html