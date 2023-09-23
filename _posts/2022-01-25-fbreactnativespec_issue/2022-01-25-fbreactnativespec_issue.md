---
title: "[React Native] in target 'FBReactNativeSpec' from project 'Pods' 해결"
author:
    name: 박 찬영
    link: https://github.io/univdev
date: 2022-01-25 14:32:00 +0900
categories: [Tech, React Native]
tags: [React Native]
---
# 개요
```nvm```으로 ```node``` 패키지를 설치한 사람에게서 발견할 수 있는 이슈입니다.  
만약 ```which node```를 입력했을 때 ```/usr/local/bin/node```가 아닌, ```/Users/[username]/.nvm ```과 같이 ```.nvm```이 포함 된 경로로 ```node```가 잡힌다면 이에 해당합니다.
# 원인
```Cocoapods```을 비롯한 React Native 필수 패키지는 Homebrew를 이용하여 설치가 되는데, ```node```패키지가 ```nvm```에 의해서 설치될 경우 나머지 패키지를 탐색하지 못하기 때문입니다.
# 해결 방법
1. nvm 폴더를 삭제합니다. ```/Users/[username]/.nvm ```
2. nvm 패키지를 설치할 때 ```~/.zshrc```와 같은 환경 변수 기록 파일에 기재했던 ```nvm``` 환경 변수를 같이 제거해준다.
```text
export NVM_DIR="$HOME/.nvm" [ -s "$NVM_DIR/nvm.sh" ] && \. "$NVM_DIR/nvm.sh" # This loads nvm [ -s "$NVM_DIR/bash_completion" ] && \. "$NVM_DIR/bash_completion" # This loads nvm bash_completion
```
3. 만약 제거 이후에 ```which node```를 입력했는데 ```/usr/local/bin/node```가 안잡힌다면 ```brew install node```를 실행한다.