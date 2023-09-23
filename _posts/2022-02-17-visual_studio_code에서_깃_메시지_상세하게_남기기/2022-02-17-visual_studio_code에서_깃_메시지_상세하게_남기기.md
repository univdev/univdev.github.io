---
title: "Visual studio code에서 깃 메시지 상세하게 남기기"
author:
  name: 박 찬영
  link: https://github.com/univdev
date: 2022-02-17 11:33:00 +0900
categories: [Tech, Git]
tags: [Git, Commit, Tech]
---
# 개요
저번에 포스팅한 [Git 메시지 컨벤션][Git 메시지 컨벤션] 문서에서 보면 HEAD와 BODY에 대한 소개가 간단하게 나옵니다.  
해당 글을 요약하자면, **"많은 개발자들이 커밋 메시지에 대해서 무신경하니까 컨벤션에 맞게 상세하게 작성하는게 좋다"**라는 내용을 말하는 글이였습니다.  

저는 Git을 사용할 때 별도의 GUI 툴을 사용하지 않고 CLI로만 처리하거나, Visual studio code에 내장 되어 있는 소스 관리 툴을 사용하는 편입니다.  
그런데 Visual Studio Code에 있는 Commit Message 입력창은 너무 작아서 HEAD, BODY 구분이 없이 HEAD만 작성하게 되는 경향이 있는 것 같습니다.

![깃 메시지 UI][깃 메시지 UI]

어떻게 하면 Visual Studio Code에서도 HEAD, BODY 구분 지어 메시지를 작성할 수 있을까요?
# 방법
![깃 메시지 방법][깃 메시지 방법]

제일 첫 줄에는 HEAD에 들어갈 메시지를 작성해주시고, 엔터를 이용하여 줄을 두 번 내린 다음 작성하시면 해당 문구는 BODY로 작성되게 됩니다.
# 결과
![깃 메시지 결과][깃 메시지 결과]

이렇게 보기 좋게 커밋이 올라갑니다!

[Git 메시지 컨벤션]: https://univdev.github.io/posts/Git_%EB%A9%94%EC%8B%9C%EC%A7%80_%EC%BB%A8%EB%B2%A4%EC%85%98/
[깃 메시지 UI]: https://firebasestorage.googleapis.com/v0/b/univdev-github-io.appspot.com/o/%E1%84%80%E1%85%B5%E1%86%BA_%E1%84%86%E1%85%A6%E1%84%89%E1%85%B5%E1%84%8C%E1%85%B5_UI.png?alt=media&token=9ea5c3b3-b793-4a92-8486-f7335dfc6abc
[깃 메시지 방법]: https://firebasestorage.googleapis.com/v0/b/univdev-github-io.appspot.com/o/%E1%84%80%E1%85%B5%E1%86%BA_%E1%84%86%E1%85%A6%E1%84%89%E1%85%B5%E1%84%8C%E1%85%B5_%E1%84%87%E1%85%A1%E1%86%BC%E1%84%87%E1%85%A5%E1%86%B8.png?alt=media&token=b3eaad66-70d4-4ff4-8e41-ffa691edf51e
[깃 메시지 결과]: https://firebasestorage.googleapis.com/v0/b/univdev-github-io.appspot.com/o/%E1%84%80%E1%85%B5%E1%86%BA_%E1%84%86%E1%85%A6%E1%84%89%E1%85%B5%E1%84%8C%E1%85%B5_%E1%84%80%E1%85%A7%E1%86%AF%E1%84%80%E1%85%AA%E1%84%86%E1%85%AE%E1%86%AF.png?alt=media&token=a59c5f97-6da4-45c3-9c35-0095729f3a46