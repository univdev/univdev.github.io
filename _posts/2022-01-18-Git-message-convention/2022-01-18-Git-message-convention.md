---
title: "Git 메시지 컨벤션"
author:
  name: 박 찬영
  link: https://github.com/univdev
date: 2022-01-18 18:33:00 +0900
categories: [Tech, Git]
tags: [Git]
---
# 개요
우리는 협업 과정에서 하나 이상의 [버전 관리 툴][버전 관리 툴]을 사용합니다. 
소스코드의 공유, 소스코드 백업 등 셀 수도 없이 다양한 이유가 있겠지만 **'이력 관리'**라는 강력한 기능을 활용하는 점에 대해서는 다른 의견이 없을겁니다.  
둘 이상이 소프트웨어 개발에 참여하다보면 서로가 개발한 부분이 ```Conflict```가 발생하기도 할거고, 어제까지만 해도 이슈 없이 동작했던 부분인데 오늘 확인해보면 갑자기 안되기도 하죠.  
이럴 때 보통 어디부터 잘못 되었나 History를 살펴봅니다.  
이렇게나 중요한 기능인데도 불구하고 많은 개발자들은 **Commit 메시지에 대해서는 세상 관대해지는 경우가 많습니다.**

![안좋은 커밋 메시지 예제][Bad Commit Messages]

단순히 **기능 추가**, **문서 수정** 등 굉장히 포괄적인 메시지를 남김과 동시에 해당 메시지와 전혀 상관없는 변경 이력마저 한 커밋에 몰아서 ```Push```하는 경우도 정말 많죠.  
~~(다른 회사에서 근무할 때는 커밋 메시지를 **"dd"**로 통일해서 올리는 개발자도 있었습니다...)~~

그렇다면 어떻게 이 문제를 해결할 수 있을까요?
# Code 컨벤션이 있다면 Commit 컨벤션도 물론 있지
다행히도 이러한 문제를 우리만 깨달은 것은 아닌지 해외의 개발자들이 무수한 논의 끝에 만들어놓은 좋은 컨벤션이 있습니다.  
이 문서에서는 해당 내용들을 정리해보겠습니다.
# 문법
## 커밋 메시지 구조
```VSCode``` 혹은 GUI 버전 관리 툴에서 커밋을 하는 사람들은 모르는 사람이 많지만, Commit 메시지는 ```HEAD``` ```BODY``` ```FOOTER``` 세 가지 구조로 나뉩니다.
- **HEAD**: 우리가 흔히 아는 커밋 메시지가 HEAD입니다.
- **BODY**: 이 작업이 왜 필요한지 상세히 기술하는 영역입니다.
- **FOOTER**: 이 작업을 통해 어떤 이슈를 수정했는지 기재하는 영역입니다.
  - Github나 Gitlab은 단순히 Git Server의 역할만 하는 것이 아닌, Agile을 위한 Sprint Planning 기능도 지원합니다. 또한 소프트웨어에서 발견되는 Issue들도 정리할 수 있죠.
  - Sprint 플랜을 추가하거나 Issue를 등록하면 해당 내용에 매칭되는 고유 번호가 발급되는데, 작업 내용과 연관 된 번호를 Footer에 적습니다.

## HEAD 컨벤션
HEAD에는 간략하게 50자 이내로 변경 이유와 이 커밋으로 인해 어떤 점이 달라지는지를 명시해야 합니다.
- feat: 기존에 없던 기능 개발
- fix: 이슈 수정
- docs: 코드와는 상관이 없는 문서가 추가 및 수정 될 경우
- style: 코드 컨벤션 등이 수정 될 경우
- design: 디자인이 변경되어 해당 부분을 수정하는 코드를 짤 경우
- refactor: 코드를 리팩토링 할 경우
- test: 테스트 코드를 추가할 경우
- chore: ```package.json```이나 ```nuxt.config.js```처럼 패키지 설치 및 환경 설정에 관련된 내용이 수정 될 경우

### 예시
```text
  feat: 회원가입 기능 개발
  fix: 회원가입 시 사용자 이름이 안보이는 이슈
```
## 과거형대신 명령형
커밋 메시지에는 과거형으로 적지 말고 현재형으로 적어야 합니다.
```text
feat: 회원가입 기능을 개발함 (x)
feat: 회원가입 기능 개발 (o)
```
## 끝에 점을 붙이지 마세요
커밋 메시지는 점을 붙이지 않습니다.
```text
feat: 회원가입 기능을 개발함. (x)
feat: 회원가입 기능 개발 (o)
```
## 대문자로 시작하지 마세요
영어로 커밋 메시지를 남기는 경우에 해당되는 얘기인데, 커밋 메시지는 소문자로 시작합니다.
```text
feat: Add join member (x)
feat: add join member (o)
```
## BODY도 마찬가지로 과거형 대신 명령형을 사용합니다.
HEAD와 같습니다.
## FOOTER 컨벤션
이 변경 이력으로 인해 수정 된 주요 이슈를 간략하게 적어야 합니다.  
이슈를 수정했다면 아래와 같이 작성합니다.
```text
Resolved: #110
```
여기서 ```110```번은 발급 된 Issue의 번호입니다.
# 예시
feat: Summarize changes in around 50 characters or less <-- HEAD

More detailed explanatory text, if necessary. Wrap it to about 72 <-- BODY
characters or so. In some contexts, the first line is treated as the
subject of the commit and the rest of the text as the body. The
blank line separating the summary from the body is critical (unless
you omit the body entirely); various tools like `log`, `shortlog`
and `rebase` can get confused if you run the two together.

Explain the problem that this commit is solving. Focus on why you
are making this change as opposed to how (the code explains that).
Are there side effects or other unintuitive consequenses of this
change? Here's the place to explain them.

Further paragraphs come after blank lines.

 - Bullet points are okay, too

 - Typically a hyphen or asterisk is used for the bullet, preceded
   by a single space, with blank lines in between, but conventions
   vary here

If you use an issue tracker, put references to them at the bottom,
like this:

Resolves: #123 <-- FOOTER
See also: #456, #789
# 참고
[Commit message format][Commit message format]

[버전 관리 툴]: https://ko.wikipedia.org/wiki/%EB%B2%84%EC%A0%84_%EA%B4%80%EB%A6%AC
[Git]: https://git-scm.com/
[Bad Commit Messages]: https://firebasestorage.googleapis.com/v0/b/univdev-github-io.appspot.com/o/bad-git-commits.png?alt=media&token=c234473c-0b0d-411f-9bc0-4ab5cc20e1f2
[Commit message format]: https://gist.github.com/develar/273e2eb938792cf5f86451fbac2bcd51