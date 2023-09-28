---
title: "기술 블로그에 Giscus를 추가했습니다!"
author:
  name: 박 찬영
  link: https://github.com/univdev
date: 2022-11-10 14:56:00 +0900
categories: ["Tech", "Talk"]
tags: ["Giscus"]
---
# 개요
기술 블로그를 Github Pages로 이전하면서 정적인 웹사이트로 바뀐 이후, 기술 블로그에 방문하는 분들의 리액션을 알 수 있는 창구가 필요해졌습니다.  
그래서 지난번에 게시물 하단에 `applause-button`를 추가했었죠.

![applause button](/assets/post_images/2022-11-10-add-giscus/20221110150110.png)
_가장 큰 호응을 얻고 있는 Pinia 게시글_

하지만 박수 횟수로는 방문자들의 니즈를 알 수가 없다는 문제가 있습니다.  
이 문제를 해결할 수 있는 가장 좋은 방법은 '댓글'을 이용하는 것인데, 기술 블로그에 추가할 수 있는 확장 프로그램이 있다는 것은 알고 있었지만 여태까지 설치를 미루다가 이제야 추가하게 되었습니다.

![댓글](/assets/post_images/2022-11-10-add-giscus/20221110150454.png)
_드디어 추가한 댓글 확장 프로그램_

# Extensions
정적 웹사이트에 댓글을 추가하기 위해 사람들이 가장 많이 사용하는 확장 서비스는 총 세 가지가 있습니다.

- Disqus
- Utterances
- Giscus

여기서 가장 유명한 서비스는 Disqus인데, 간단한 기술 블로그에 설치하기에는 너무 거창한 기능들이 많아서 후보군에서 제외하게 되었습니다.  
그래서 Utterances와 Giscus 둘 중 하나를 골라서 사용해야 하는 상황이였는데, 최종적으로 Giscus를 결정하게 되었습니다.

# Utterances vs Giscus
Utterances와 Giscus는 둘 다 Github API를 사용하여 댓글을 저장한다는 공통점이 있습니다.  
Github의 Repository에 댓글을 저장하여 API로 불러와서 사용하는 형태예요.

두 서비스의 가장 큰 차이점은 Utterances는 Issue를, Giscus는 Discussions를 사용한다는 점입니다.  
개인적으로 댓글을 저장하기 위한 저장소로 사용하기에 Issue는 맞지가 않는 것 같아 Giscus의 철학을 따르게 되었습니다.
# Giscus 앱 설치
1. [giscus 앱][앱 다운로드 페이지]을 **'댓글을 저장할 Repository'**에 설치해주세요.

# Discussions 활성화
1. Repository의 설정 페이지로 들어갑니다.
![설정 페이지](/assets/post_images/2022-11-10-add-giscus/20221110152133.png)
2. 'Features' 섹션에서 Discussions를 활성화합니다.
![Discussions 활성화](/assets/post_images/2022-11-10-add-giscus/20221110152213.png)

# Giscus를 내 블로그에 추가
1. [Giscus 설정 페이지][설정 페이지]로 들어갑니다.
![Giscus 설정 페이지](/assets/post_images/2022-11-10-add-giscus/20221110151220.png)
2. [설정] 섹션에서 댓글을 저장할 나의 레포지토리 이름을 입력합니다.
![저장소 이름 입력](/assets/post_images/2022-11-10-add-giscus/20221110151342.png)
3. 페이지와 Discussions를 연결할 방법을 선택합니다. (Github API에서 댓글을 조회할 수 있는 방법을 결정하는거예요.)
![페이지와 Discussions를 연결할 방법 선택](/assets/post_images/2022-11-10-add-giscus/20221110151428.png)
4. Giscus를 이용하여 게시글에 댓글을 달면 Discussion을 생성하고 댓글이 달리는데, 해당 Discussion에 사용 될 카테고리를 선택합니다.
![카테고리 선택](/assets/post_images/2022-11-10-add-giscus/20221110151606.png)
5. 추가 기능을 선택합니다.
![추가 기능 선택](/assets/post_images/2022-11-10-add-giscus/20221110151642.png) 
6. 적용하실 테마를 선택해주세요.
![테마 선택](/assets/post_images/2022-11-10-add-giscus/20221110151708.png)
7. 생성 된 `script` 태그를 웹 페이지에 추가해주세요.
![스크립트 태그 추가](/assets/post_images/2022-11-10-add-giscus/20221110151815.png)
8. 댓글창을 띄우고 싶은 위치에 `giscus` 클래스를 가진 Element를 배치해주세요. 스크립트가 활성화 되면서 댓글창으로 변환됩니다.

# 마치며
블로그를 방문해주시는 분들은 정말 많은데 박수 말고 다른 의견을 들을 수 있는 창구를 이제야 만들게 되었네요 😅  
본문 내용에 문제가 있거나, 추가로 궁금하신 내용이 있으시다면 댓글 기능을 이용해주세요!

[앱 다운로드 페이지]: https://github.com/apps/giscus
[설정 페이지]: https://giscus.app/ko