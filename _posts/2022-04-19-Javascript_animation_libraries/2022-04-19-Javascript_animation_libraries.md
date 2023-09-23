---
title: "Javascript 애니메이션 라이브러리"
author:
  name: 박 찬영
  link: https://github.com/univdev
date: 2022-04-19 13:14:00 +0900
categories: [Tech, Talk]
tags: [Animation, Javascript]
---
<div style="padding-bottom: 56.25%; position: relative;">
  <iframe style="position: absolute; right: 0; top: 0; right: 0; left: 0;" width="100%" height="100%" src="https://www.youtube.com/embed/wbDpZwDRgRk" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>
</div>

# 개요
오늘날의 웹사이트에는 다양한 애니메이션 효과가 기본적으로 포함됩니다.  
CSS, Javascript를 통해서 DOM을 제어하기에 가능한 일입니다.  
그런데 단순한 애니메이션 정도면 몰라도 복잡한 애니메이션이나 스크롤 애니메이션, 3D그래픽을 이용한 애니메이션 등은 직접 만들기에는 무리가 있습니다. 그런 복잡한 애니메이션을 쉽게 만들 수 있도록 도와주는 라이브러리를 소개합니다.
# [Typeit][Typeit]
![Typeit][Typeit gif]

웹사이트에 타이밍 애니메이션 효과를 줄 수 있습니다.
## 장점
- SSR 최적화
- 가벼운 사이즈
- 간편함

# [ScrollOut][ScrollOut]
웹사이트의 특정 영역이 Viewport안에 들어오거나 나갈 경우 요소가 나타나고 사라지는 등의 애니메이션 효과를 구현할 수 있습니다.  
HTML Attribute와 CSS Transition, keyframe만을 이용하여 애니메이션을 구현하기 때문에 애니메이션의 퍼포먼스가 좋고 라이브러리의 사이즈가 가볍습니다.
## 장점
- 매우 가벼운 라이브러리
- 높은 성능의 애니메이션
- 간편함

# [Anime.js][Anime.js]
상대적으로 구현하기 복잡한 애니메이션을 구현하도록 도와주는 라이브러리입니다.  
Javascript 기반으로 실제 DOM의 위치를 변경하여 다시 그리는 형태로 애니메이션을 구현합니다.  
상대적으로 사용하기 어려운 라이브러리지만 원하는 애니메이션을 폭넓게 구현할 수 있습니다.

# [Rellax][Rellax]
패럴랙스 스크롤을 쉽게 구현할 수 있도록 도와주는 라이브러리입니다.  
## 패럴랙스 스크롤
![Parallax Scrol][Parallax Scroll]

패럴랙스 스크롤이란, 스크롤 정도에 따라서 DOM의 일부 객체가 서로 다른 속도로 이동하는 효과를 말합니다.

[Typeit]: https://www.typeitjs.com/
[Typeit gif]: https://raw.githubusercontent.com/alexmacarthur/typeit/HEAD/readme-demo.gif
[ScrollOut]: https://scroll-out.github.io/
[Anime.js]: https://animejs.com/
[Rellax]: https://chriscavs.github.io/rallax-demo/
[Parallax Scroll]: https://s3.amazonaws.com/www-inside-design/uploads/2019/06/image9.gif