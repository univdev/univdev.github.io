---
title: "Git 원치 않는 파일 제거하기"
author:
  name: 박 찬영
  link: https://github.com/univdev
categories: [Tech, Git]
date: 2023-01-09 18:21:00 +0900
---
![원치 않는 파일 제거하기](/assets/post_images/2023-01-09-remove-git-file/20230109183222.png)  
# 들어가며
가끔 `.env` 파일이나 개발 내용과는 전혀 상관 없는 이미지 파일, 개인적인 자료들이 **실수로** Git 히스토리에 커밋 되어 원격에 올라가는 경우가 있습니다.  
로컬에서 파일을 삭제하고 커밋을 할 경우 최신 이력에서는 삭제 되지만 히스토리를 살펴보면 파일 조회가 가능하기에 보안상 심각한 이슈를 낳을 수 있어요.

```
git filter-branch --force --index-filter "git rm --cached --ignore-unmatch 파일명" --prune-empty --tag-name-filter cat -- --all

git add *
git commit -m "커밋 메시지"
git push origin master --force
```
위 명령어를 입력하시면 해당 파일과 관련 된 모든 커밋 내역이 원격에서도 제거 되어 해당 파일을 조회 할 수 있는 방법이 사라지게 됩니다.
# 레퍼런스
[원격 저장소에서 원하는 파일 기록까지 모두 제거 하는 방법][레퍼런스]

[레퍼런스]: https://yeoossi.tistory.com/43
