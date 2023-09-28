---
title: "Tauri로 시작하는 Desktop App 개발 생활"
author:
  name: 박 찬영
  link: https://github.com/univdev
categories: [Tech, Talk]
date: 2022-05-09 18:40:00 +0900
tags: [Tauri]
---
![Tauri][Tauri]
# Tauri
```Tauri```는 웹으로 *Desktop App*을 빌드할 수 있는 툴킷입니다.  
*Desktop App*을 ```Build```하기 위한 툴킷으로는 ```Electron```이 유명하고 이미 널리 사용되고 있습니다.  
그렇지만 ```Electron```은 태생부터 갖고있던 단점들이 존재하며, 이를 해결하기 위해 출시 된 것이 ```Tauri```입니다.
# Electron VS Tauri
![Electron VS Tauri][Electron VS Tauri]
_출처: https://betterprogramming.pub/will-tauri-be-an-electron-killer-38fd6478004_

## 퍼포먼스
실행 속도, 설치파일 사이즈, 번들 사이즈 등 모든 측면에서 ```Tauri```가 ```Electron```을 압도합니다.  
실제 프로덕션에서도 같은 결과를 보여줄지는 봐야 알겠지만 수치상 표현에서는 월등히 앞서네요.  
또한, ```Tauri```는 Webview를 자체 개발한 ```WRY```를 사용한다는 점도 런타임 퍼포먼스에 상당한 이점을 가져다줍니다.
```Chromium```을 사용하는 ```Electron```은 자체적으로 ```Chrome``` 브라우저를 내장하고 있기 때문에 기본적으로 브라우저만큼의 사이즈를 먹고 들어가는 반면, ```Tauri```의 ```WRY```는 브라우저를 자체적으로 내장한 것이 아닌 ```OS```에 내장 되어있는 기본 브라우저에 붙여주는 **접착제**의 역할만을 하기 때문에 번들 사이즈가 매우 가볍습니다.
> 그렇기에 Tauri로 Build 된 앱을 실행하면 Mac은 Safari, Windows는 Edge로 실행되는 것을 보실 수 있습니다.

## 모바일 기기 지원
현재는 ```Electron``` ```Tauri``` 모두 **모바일 기기를 지원하지 않습니다.**  
다만 ```Electron```은 아예 모바일 관련 언급이 없는 반면, ```Tauri```는 [로드맵][Tauri Mobile Roadmap]에서 ```Mobile Build```에 대한 지원을 약속한 바 있습니다.  
코멘트를 보면 **1년 뒤**에 지원이 가능할 것 같다고 적혀있는걸 봐서 22년 2분기를 예상하지만 **확정은 아닙니다.**  
하지만 ```Mobile Build```에 대한 언급이 있다는 것 자체만으로 매우 굉장한 일이며, 추후에는 현재 ```React Native```가 맡고있는 하이브리드 어플리케이션의 영역을 완전히 가져올 수도 있는 엄청난 가능성을 내포합니다.
# 설치 가이드
## Rust Cargo 설치
https://doc.rust-lang.org/cargo/getting-started/installation.html  
```Tauri```로 프로젝트를 실행 / 빌드하기 위해서는 ```Cargo```를 설치해야 합니다.  
```
curl https://sh.rustup.rs -sSf | sh
```
위 명령어를 ```Terminal```에 입력 후 실행합니다.
## 웹 프로젝트가 이미 있는 경우
```
yarn add -D @tauri-apps/cli
# OR
npm install -D @tauri-apps/cli
```
프로젝트의 최상위 폴더에서 위 명령어를 입력하여 ```@tauri-apps/cli```를 설치합니다.
```json
{
  "scripts": {
    "tauri": "tauri"
  }
}
```
{: file="package.json"}
```package.json``` 파일의 ```scripts```에 위 명령어 세트를 추가합니다.
```
yarn add @tauri-apps/api
# OR
npm install @tauri-apps/api
```
만약 프로젝트가 ```webpack```이나 ```vite```를 이용하여 동작한다면 위 명령어를 입력해주세요.
## Vue 프로젝트로 시작하기
https://github.com/tauri-apps/vue-cli-plugin-tauri  
최선의 방법은 ```vue-cli-plugin-tauri```를 이용하는 것입니다.
```
vue create my-tauri-app
```
위 명령어로 ```Vue`` 프로젝트를 개설합니다.  
```
cd my-tauri-app
vue add tauri
```
만들어진 ```Vue``` 프로젝트의 ```root```에서 ```vue add tauri``` 커맨드를 입력하여 ```tauri```를 설치합니다.  
이 경우에는 알아서 ```package.json```에 ```scripts```를 설정해주기 때문에 명령어를 수동으로 입력할 필요가 없습니다.
# 실행
## Vue CLI를 사용하지 않은 경우
```
yarn tauri dev
```
위 명령어를 이용하여 개발 모드를 실행할 수 있습니다.  
다만, 실제 프로젝트와 연결되어 있는것은 아니기 때문에 ```yarn dev``` 명령어를 통해 **개발서버를 별도로 켠 다음에 위 명령어를 실행해야 합니다.**
```
yarn tauri build
```
빌드는 위 명령어로 진행합니다.  
기본적으로는 프로젝트 폴더 내 ```dist``` 디렉토리 안에 있는 빌드 된 파일들을 래핑하여 실행파일을 제작합니다.  
만약 **빌드 파일이 다른 폴더에 생성된다면 해당 폴더의 경로를 설정해야 합니다.**
## Vue CLI를 사용한 경우
```
yarn tauri:serve
```
```Tauri```를 이용하여 프로젝트를 개발모드로 실행합니다.  
```yarn dev``` 명령어 없이도 서버를 자동으로 실행해줍니다.
```
yarn tauri:build
```
```Tauri```를 이용하여 빌드합니다.
# 크로스 플랫폼 빌드
```Tauri```는 기본적으로 멀티 플랫폼을 지원하지만 **빌드를 하나의 플랫폼에서 진행할 수는 없습니다.**  
[공식 문서][Cross Platform build]를 보시면 아래와 같이 적혀있습니다.

> 지금은 Cross Platform 빌드를 지원하지 않습니다. 만약 당신이 Cross Platform 빌드를 원한다면 VM을 사용하거나, Github Actions를 사용해야 합니다.

현재 빌드가 가능한 항목은 아래와 같습니다.

**Windows:** ```.exe```, ```.msi```, ```.msi.zip```  
**macOS:** ```.app```, ```.dmg```, ```.app.tar.gz```  
**Linux:** ```.deb```, ```.AppImage```, ```.AppImage.tar.gz```
# 마치며
## Tauri
- 상대적으로 빠르다.
- 상대적으로 가볍다.
- 대부분의 프론트엔드 프레임워크(Vue.js, React, Svelte)와 호환된다.
- 커뮤니티가 아직 작다.
- 기능을 심화적으로 사용하고 싶다면 ```Rust```를 배워야 한다.
## Electron
- 상대적으로 무겁고 느리다. (그래도 빠른편임)
- 역사가 오래되어 커뮤니티가 방대하고, 예시 프로젝트도 많다.
- NodeJS 생태계에 있다보니 러닝커브가 매우 낮다.

[Tauri]: https://user-images.githubusercontent.com/26304279/167368736-136c44f9-4ab9-4e58-ab06-ad3a269fc30c.jpg
[Electron VS Tauri]: https://user-images.githubusercontent.com/26304279/167371457-b1138088-4c19-4f47-8555-43f46cc7419a.png
[Chrome Memory]: https://user-images.githubusercontent.com/26304279/167375424-702ec6f8-78b4-4f1c-862c-d245c4163494.png
[Tauri Mobile Roadmap]: https://github.com/tauri-apps/tauri/issues/843#issuecomment-957354045
[Cross Platform build]: https://jonaskruckenberg.github.io/tauri-docs-wip/building/building-your-application.html#cross-platform-compilation