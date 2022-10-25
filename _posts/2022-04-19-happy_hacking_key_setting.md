---
title: "해피해킹 키 세팅"
author:
  name: 박 찬영
  link: https://github.com/univdev
categories: [Tech, Talk]
tags: [Happyhacking]
date: 2022-04-19 11:30:00 +0900
---
# 해피해킹
해피해킹 키보드는 사용자들 사이에서도 호불호가 정말 많이 갈리는 키보드 중 하나입니다.  
고작 해봐야 키보드인데도 키세팅 하나 익히자고 몇 십년간 사용했던 일반적인 키보드의 배열을 정면으로 거슬러야 하기 떄문이죠.

그럼에도 불구하고 개발자 커뮤니티에서 꾸준히 회자되고 있다는 것은 **익숙해지면** 분명 쓸만한 키보드이기 때문일 것입니다.  
그래서 평소 생각은 하고 있었으나 마땅히 살만한 타이밍을 잡지 못했었기 떄문에 고민을 많이 했는데 네이버 스토어에 가격이 적당한게 나와서 구매하게 되었습니다.
# 딥스위치
딥스위치는 해피해킹 키보드에만 있는 특별한 키세팅 방식입니다.  
키보드의 뒷면을 열어보면 아래와 같이 스위치가 6개 존재합니다.

![딥스위치][딥스위치]
_이미지 출처: https://kim-dragon.tistory.com/114_
맥을 사용하는 유저는 1, 3, 5번 스위치를 ON으로 전환하면 우리가 흔히 아는 맥 키보드 세팅을 사용할 수 있습니다.
# 카라비너
[카라비너][카라비너] 사이트에서 프로그램을 다운받습니다.  
해피해킹은 위에서도 말했다시피 키 배열이 절대 평범하지 않습니다.  
그렇기에 키보드를 본인 손에 맞도록 여러가지 커스터마이징을 할 필요가 있습니다.  
카라비너는 키보드 자판 세팅을 도와주는 소프트웨어입니다.

```Karabiner-Elements Preferences``` 소프트웨어를 켜면 상단에 8개의 탭이 보일텐데, ```Misc```탭을 눌러줍니다.  
해당 탭에서 하단에 보면 ```Export & Import``` 영역이 보일텐데, 하단에 있는 ```Open config folder``` 버튼을 눌러줍니다.

해당 버튼을 누르면 파인더가 열리고, 해당 폴더 안에 ```karabinder.json``` 파일이 보일텐데, 해당 파일에 카라비너에서 사용할 키 세팅 명세를 작성할 수 있습니다.  
흔히 볼 수 있는 ```json```파일이지만 카라비너가 이해할 수 있는 문법으로 작성해야 하기에 이를 직접 커스터마이징 하기 위해서는 어느정도의 러닝커브가 존재합니다.


그렇기에 당장 급하게 해피해킹 키보드를 세팅하고 싶은 분들은 카라비너의 ```Complex modifications``` 탭을 누르고, 해당 탭에서 ```Rules```를 선택합니다.  
![카라비너 규칙 추가][카라비너 규칙 추가]
_이미지 출처: https://karabiner-elements.pqrs.org/docs/manual/configuration/configure-complex-modifications/_
하단에 있는 ```Add Rules``` 버튼을 누르면 현재 보유한 규칙들을 활성 / 비활성 시킬 수 있는데 기본적으로 갖고있는 키세팅이 거의 없을겁니다.

![카라비너 규칙 임포트][카라비너 규칙 임포트]
_이미지 출처: https://karabiner-elements.pqrs.org/docs/manual/configuration/configure-complex-modifications/_
상단에 있는 ```import more rules from the internet``` 버튼을 누르면 웹브라우저가 열리며, 다른 사람들이 만들어둔 카라비너 키세팅을 다운 받을 수 있습니다.

[제가 사용하는 키세팅][키세팅] 파일도 공유해드립니다!  
만약 이를 적용하고자 하신다면 `~/.config/karabiner` 경로에 `karabiner.json` 파일로 저장하시면 됩니다.

긴 글 읽어주셔서 감사합니다!

[딥스위치]: https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FeXjAWo%2FbtrdOnht2o2%2FRzw37bhZkjko92Gb8xRjGk%2Fimg.png
[카라비너 규칙 추가]: https://karabiner-elements.pqrs.org/docs/manual/configuration/configure-complex-modifications/images/karabiner-elements-complex-modifications-1@2x.png
[카라비너 규칙 임포트]: https://karabiner-elements.pqrs.org/docs/manual/configuration/configure-complex-modifications/images/karabiner-elements-complex-modifications-2@2x.png
[카라비너]: https://karabiner-elements.pqrs.org/
[키세팅]: https://gist.github.com/univdev/df71a099a151bcd3ad70d1333419af3b