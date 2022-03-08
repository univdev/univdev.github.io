---
title: "HTML을 Jade(PUG)로 바꿔주는 사이트"
author:
  name: 박 찬영
  link: https://github.io/univdev
date: 2022-02-24 14:52:00 +0900
categories: [Tech, Talk]
tags: [Tech, Jade, PUG, HTML]
---
# 서비스
![서비스 이미지][이미지]
<p style="text-align: center;">
  <a href="https://html2pug.vercel.app/">html2pug</a>
</p>

# 개요
저는 Vue로 작업을 할 때 HTML작업은 pug를 사용하여 작업을 합니다.  
처음부터 제가 개발을 했던 프로젝트라면 pug로 개발을 시작했겠지만, 기존에 개발되던 프로젝트를 인수받아 작업하는 것이라면 얘기가 좀 달라집니다.

기존에 작성 되어 있던 템플릿을 HTML에서 pug로 변환하는 작업이 필요해지는데, 이를 쉽게 도와주는 서비스를 소개합니다.
# 차별점
## 다른 변환 사이트
- 한글을 강제로 HEX로 인코딩 하기 때문에 소스코드의 가독성을 해칩니다.
- HTML Attribute의 따옴표를 강제로 큰 따옴표로 변환하거나, 작은 따옴표로 변환합니다.
- 트리 탐색 알고리즘의 문제로 인해 트리가 깊어지면 해당 부분이 잘리는 이슈가 있습니다.
- Depth를 커스터마이징 할 수가 없어서 코드 컨벤션을 맞추기 힘든 부분이 있습니다.

## 해당 사이트
- 한글은 한글 그대로 변환해줍니다.
- HTML Attribute의 따옴표를 어떻게 설정하고 싶은지 체크할 수 있습니다.
- 트리 탐색 알고리즘에 대한 이슈가 없습니다.
- Depth를 입맛대로 커스터마이징 할 수 있습니다.
- **Vue에 맞는 문법으로 변환할 수도 있습니다.**

[서비스]: https://html2pug.vercel.app/
[이미지]: https://firebasestorage.googleapis.com/v0/b/univdev-github-io.appspot.com/o/html_to_pug.png?alt=media&token=7adb51bd-eeed-434e-bde2-2c46786fda85