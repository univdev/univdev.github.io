---
title: "프론트엔드와 백엔드의 역할과 책임"
author:
  name: 박 찬영
  link: https://github.com/univdev
date: 2022-04-21 11:52:00 +0900
categories: [Tech, Talk]
---
# 들어가기에 앞서
이 글은 지극히 **제 개인적인 의견과 경험에 의하여 작성되었습니다.**  
아래 사례에 등장하는 백엔드 개발자분들과 비슷하게 **프론트에 대한 오해를 갖고 계신 분들의 오해를 해소하기 위해서 작성 된 글입니다.**
# 사례
제가 같이 일했던 백엔드 개발자분과 있었던 케이스 몇 개를 말씀드리겠습니다.
## Case 1
```
메일 시스템은 본문에 1,000글자 이내로 본문을 작성할 수 있습니다.
```
위와 같은 요구사항이 들어왔을 떄, 제가 백엔드 개발자로부터 받았던 요구사항입니다.
> 아 찬영씨, 메일 1,000글자 이상으로 넘어간 상태에서 컨펌누르면 안되게 막아주세요~

이 기능은 별다른 Javascript 로직 없이 HTML5의 ```maxlength``` 프로퍼티로 구현하였고, 보안성 테스트를 위해 브라우저의 DOM 편집기에서 ```maxlength``` 프로퍼티를 제거하고 서버에 전송하는 형태로 테스트를 해봤더니 **2천, 3천자의 텍스트도 별다른 필터없이 전송되는 사태가 벌어졌습니다.**
## Case 2
```
관리자는 CMS에서 회원을 등록할 수 있습니다.
회원 테이블의 휴대전화 번호는 필수로 들어가야 하는 값입니다.
```
회원가입 폼을 만들고 필수 값을 입력하지 않은 채 가입 API를 사용했더니 **회원이 별 다른 Validation 절차 없이 가입에 성공하는 사태가 벌어졌습니다.**  
이를 백엔드 개발자분께 말씀드리니 **"휴대전화 필드는 required 걸어주세요"** 라는 말씀을 듣게 되었고, 저는 백엔드에서도 어느 정도의 validation은 걸어주셔야 한다고 말씀드려서 재개발을 요청 드렸고, 추후에 같은 방식으로 가입을 시도했더니 validation이 걸리긴 하는데 ```500``` Exception만 터져나올 뿐, 어떤 사유로 인해 반려가 되었는지는 나오지 않았습니다.
## Case 3
```
관리자에서 게시물의 카테고리를 등록할 때 '행사' 타입의 카테고리는 게시물에 '행사 시작일'과 '행사 종료일' 필드가 나타나야 한다.
'행사' 타입의 카테고리의 하위에 등록 된 카테고리를 설정해도 마찬가지로 추가적인 UI가 보여져야 한다.
이를 위해 카테고리에는 isEvent라는 플래그가 존재하며, 이를 카테고리 추가 및 수정시 설정할 수 있어야 한다.
```
프론트에서는 카테고리를 등록하는 UI를 작성하고, ```isEvent``` 플래그에 따라 활성 / 비활성 되는 스위치를 배치하였습니다.  
또한, 하위 카테고리를 추가할 때는 ```isEvent```를 제어할 수 있는 스위치를 노출하지 않도록 구현했습니다.  
그렇게 하위 카테고리를 추가하니 ```isEvent```가 ```true```인 부모의 하위 카테고리를 생성해도 기본값인 ```false```가 들어가도록 설정되어 있어서 백엔드 개발자분께 여쭤보니 ```isEvent``` 값은 **부모값에서 가져와서 등록해야 한다는 말을 들었습니다.**
# 위 케이스의 문제점
일부 백엔드 개발자분들이 프론트의 역할에 대해 오해가 있는 경우가 있습니다.  
프론트는 사용자가 엉뚱한 길로 빠지게 하지 않고, 정보를 기입하기 위해 최대한 편한 UI를 제공하는 역할을 합니다.  
마치 호텔 프론트에서 투숙객을 맞이하는 스태프와 같은 역할이라고 생각하시면 이해가 쉬울 것 같습니다.

호텔의 프론트에서 업무를 보시는 스태프 분들은 호텔의 매니저가 정한 룰을 고객님께 **안내하고 최대한 편하게 해드리는 역할을 할 뿐**, 정책 그 자체에 손을 대는 역할은 아닌 것처럼 프론트엔드도 이미 사전에 정해진 룰에서 최대한 편한 UI를 제공하는 것이 역할입니다.

Data Validation이나 isEvent와 같이 실제 데이터에 영향을 주는 정책 이슈를 해결하는 것이 백엔드의 역할입니다.  
만약 이를 프론트에서 제어한다면, 소프트웨어를 정해진 규격 하에서 사용하는 일반 사용자들에게는 문제가 없겠지만 클라이언트 변조 기술을 가진 일부 사용자들은 이를 파훼하고 서버에 악영향을 주는 데이터를 삽입할 수도 있기에 실제 보안 정책은 백엔드에서 이루어져야만 합니다.

또한 ```isEvent``` 플래그 제어의 경우에도 '부모 카테고리 수정 시 하위 데이터에 대한 상태 변경' 등 데이터를 탐색해야 하는 경우의 수가 많아지는데, **이를 프론트에서 제어하게 되면 웹 퍼포먼스를 저하하고 사용자 경험에 악영향을 줍니다.** 그러므로 데이터의 깊은 탐색 및 변경에 대한 구현은 백엔드에서 이루어지는게 더 나은 결과를 얻을 수 있습니다.
