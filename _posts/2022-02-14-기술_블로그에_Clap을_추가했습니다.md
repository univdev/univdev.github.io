---
title: 기술 블로그에 Clap을 추가했습니다! (feat. Applause Button)
author:
  name: 박 찬영
  link: https://github.com/univdev
date: 2022-02-14 17:27:00 +0900
categories: [Tech, Talk]
tags: [Talk, Blog, Extension, Jekyll, Applause Button]
---
# 개요
![Clap][Clap]

Github Pages와 같은 정적 웹사이트는 기본적으로 사용자에게 **이미 결과가 정해진 콘텐츠를** 보여주기 위해 탄생한 솔루션이라서 사용자와의 상호작용이 상대적으로 덜 중요합니다.  
하지만 그렇다고 해서 "사용자와의 상호작용이 완전 불필요한가?" 생각해보면 그건 또 아닙니다.  
내가 작성하는 게시물이 옳은 게시물인지, 얼마나 많은 호응을 얻고 있는지에 대해서 알 수 있어야 추후 올라오는 게시물에도 더욱 많은 신경을 쓸 수 있기 때문이죠.

그래서 많은 기술 블로그들은 정적 웹사이트에서 댓글을 사용할 수 있도록 [Disqus][Disqus]와 같은 서비스를 연동하여 사용하기도 합니다.  
본 문서에서 소개할 Clap도 사용자의 호응 척도를 측정하기 위한 서비스입니다.
# Applause Button
제가 블로그에 적용한 서비스는 [Applause Button][Applause Button]입니다.  
특정 플랫폼에 구애받지 않고 모든 유형의 웹사이트에 쉽게 삽입할 수 있다는 장점이 있죠.
## 원리
Applause Button 서비스는 CDN 혹은 NPM 패키지 두가지 방식으로 적용할 수 있습니다.  
```applause-button```라는 Custom Element를 정의하여, 해당 태그를 사용하기만 하면 현재 접속한 URL에 기반하여 박수 횟수를 누적할 수 있는 것이죠.
# 적용 방법
## CDN 추가
페이지의 최상단에 Applause Button을 적용하기 위한 CDN을 적용합니다.
```html
  <link rel="stylesheet" href="https://unpkg.com/applause-button/dist/applause-button.css">
  <script src="https://unpkg.com/applause-button/dist/applause-button.js"></script>
```
## CSS 추가
applause-button 태그의 사이즈를 정하기 위해서 Custom CSS도 추가합니다.
```scss
applause-button {
  width: 80px;
  height: 80px;
  margin: 0 auto;

  .count-container {
    color: inherit;
  }
}
```
혹은
```css
applause-button {
  width: 80px;
  height: 80px;
  margin: 0 auto;
}
applause-button .count-container {
  color: inherit;
}
```
## 페이지에 추가
박수 버튼을 표시할 위치에 태그를 추가해줍니다.  
```html
<applause-button class="mb6"
color={{ site.accent_color | default:'rgb(79,177,186)' }}
url={{ page.url | absolute_url }} >
</applause-button>
```
### Property
```color``` Property는 박수 버튼에 적용할 색상을 적어주시면 됩니다.  
```url``` Property는 해당 웹 페이지의 URL을 적어줍니다.

> 박수는 url property에 작성 된 URL에 따라 누적되기 때문에 반드시 고유한 URL이 되도록 작성해주셔야 합니다.

# 마무리
이처럼 아주 간단한 방법으로 독자들과 상호작용이 가능한 창구를 적용하는게 가능합니다.  
그러니 제가 작성한 글이 마음에 드신다면 다들 박수 한번 부탁드려요!
# 참고
[Lazy Ren - Add Applause Button for Jekyll Post][레퍼런스]

[Disqus]: https://blog.disqus.com/
[Clap]: https://firebasestorage.googleapis.com/v0/b/univdev-github-io.appspot.com/o/clap.png?alt=media&token=0a955a81-0f53-4190-92cf-684e68b9d773
[Applause Button]: https://applause-button.com/
[레퍼런스]: https://lazyren.github.io/devlog/add-applause-button-for-jekyll-post.html