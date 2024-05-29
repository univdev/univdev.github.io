---
title: "Mac OS Unity에서 VSCode를 사용하기 위한 설정 방법"
author:
  name: "박 찬영"
  link: "https://github.com/univdev"
date: 2024-05-29 11:18:00 +0900
tags: [Unity, VSCode]
categories: [Tech, Unity]
image:
  path: /assets/post_images/2024-05-29-unity-vscode-setting-for-mac/2024-05-29-11-20-18.png
  alt: Mac OS Unity에서 VSCode를 설정하는 방법
---
![Mac OS Unity에서 VSCode를 설정하는 방법](/assets/post_images/2024-05-29-unity-vscode-setting-for-mac/2024-05-29-11-20-18.png)

# 들어가며
Unity를 처음 설치하고나면 'Visual Studio for Mac'이라는 IDE가 함께 설치되어 기본 에디터로 사용됩니다.  
해당 IDE는 Unity Script를 작성할 때 사용되는데, 상당히 많은 개발자들이 기본 IDE인 Visual Studio를 그대로 사용하기보다 상대적으로 친숙한 Visual Studio Code를 사용하는 편입니다.

이 문서에서는 Mac OS + Unity 환경에서 Visual Studio Code를 사용하기 위해 필요한 초기 설정 방법을 기술합니다.  
생각보다 간단함에도 불구하고 다른 블로그에서 나와있는 방법이 저에게 효과가 없었어서 기존 다른 블로그에 기술 되어있는 내용에 더해 제가 시행한 방법을 추가적으로 기술하였습니다.

# Unity 설치하기
Unity를 설치하기 위해 **Unity Hub를 먼저 설치 해주셔야 합니다.**  
Unity Hub는 에디터를 비롯한 게임 개발에 필요한 교육자료 및 패키지를 통합적으로 관리하기 위한 소프트웨어입니다.  
Mac OS는 [여기](https://store.unity.com/download)에서 Hub를 다운로드 해야 합니다.

Unity Hub를 설치 후 실행하면 다음과 같은 화면이 표시 됩니다.

![Unity Hub](/assets/post_images/2024-05-29-unity-vscode-setting-for-mac/2024-05-29-11-30-57.png){: width="600" }

아마 Unity가 설치 되어 있지 않으실테니 [Install Editor] 버튼을 눌러 원하시는 버전의 Unity 설치를 진행해주세요.

# Unity 실행하기
설치 된 Unity를 실행하기 위해서는 프로젝트를 생성하셔야 합니다.  
Unity Hub 좌측 네비게이션을 보시면 [Projects] 버튼이 있는데 해당 버튼을 클릭하면 아래 사진과 같은 화면이 표시 됩니다.

![Projects 화면](/assets/post_images/2024-05-29-unity-vscode-setting-for-mac/2024-05-29-11-34-51.png){: width="600" }

우측 상단에 있는 [New Project] 버튼을 클릭하셔서 프로젝트를 생성해주세요.  

![Unity Project generate](/assets/post_images/2024-05-29-unity-vscode-setting-for-mac/2024-05-29-11-36-54.png){: width="600" }

원래라면 해당 화면에서 개발하실 게임의 성격에 맞는 템플릿을 고르셔야 하겠지만 지금은 Unity 에디터를 켜는게 중요하기 때문에 아무거나 생성하시면 됩니다.

프로젝트 생성을 무사히 마치셨다면 프로젝트를 선택하여 Unity 에디터를 여실 수 있습니다.

# Unity IDE 변경하기
[Unity] -> [Settings] 메뉴를 순서대로 누르면 아래 사진과 같이 'Preference' 탭이 열리게 됩니다.

![Unity Preference](/assets/post_images/2024-05-29-unity-vscode-setting-for-mac/2024-05-29-11-41-57.png)

[External Tools] 메뉴를 선택하시면 'External Script Editor' 라는 항목이 보이게 되는데, 기본 설정은 'Visual Studio for Mac' 으로 되어 있을 것입니다.  
해당 항목을 'Visual Studio Code'로 변경해주시면 됩니다.  
(만약 해당 항목이 존재하지 않는다면 'Browse...' 를 눌러 컴퓨터에 설치 된 VSCode 프로그램을 직접 찾아 선택하는 방식으로도 해결하실 수 있습니다.)

# Visual Studio Code Intellisense 문제 해결하기
Unity에서 VSCode를 연결하는 과정은 문제 없이 수행했으나, 이후 스크립트를 작성하려고 하면 자동완성이 되지 않는 문제로 인해 당황하게 됩니다.  
**여기부터가 본 문서를 작성하게 된 이유이자 핵심이니 잘 따라해주시기 바랍니다.**

# VSCode Extensions 설치하기
우선 아래 Extension을 모두 설치해주시기 바랍니다.
- [C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
- [C# Dev Kit](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csdevkit)
- [Unity](https://marketplace.visualstudio.com/items?itemName=VisualStudioToolsForUnity.vstuc)

# .NET 관련 소프트웨어 설치하기
VSCode Extension을 모두 설치하셨다면 컴퓨터에 설치해주셔야 하는 소프트웨어가 두 개 있습니다.
- [Mono](https://www.mono-project.com/download/stable/)
- [.NET Framework](https://dotnet.microsoft.com/ko-kr/download/dotnet) ('최신'이라는 표시가 붙은 버전으로 설치하시길 권장합니다.)

위 소프트웨어를 모두 설치하시고 VSCode를 실행하시면 성공적으로 동작하는 경우가 있고, 저처럼 자동완성이 여전히 동작하지 않는 경우가 있습니다.  
만약 자동완성이 제대로 동작하지 않으신다면 VSCode를 켰을 때 높은 확률로 다음과 같은 오류를 보셨을 것입니다.

![Path 오류](/assets/post_images/2024-05-29-unity-vscode-setting-for-mac/2024-05-29-11-55-30.png){: width="360" }

컴퓨터에 .NET이 성공적으로 설치가 되었으나 Visual Studio Code가 해당 프레임워크를 실행하려면 컴퓨터가 해당 프로그램이 설치 된 경로를 명시적으로 알고 있어야 하는데 그렇지 않아 발생하는 이슈입니다.  
이런 경우 .NET이 설치 된 경로를 path에 등록하시면 해결하실 수 있습니다.

# path 설정하기 (위 과정을 모두 마쳐도 자동완성이 안 될 경우)
제 경우, `/usr/local/share/dotnet/dotnet` 경로에 .NET이 설치가 되었습니다.  
혹시 확인해보고 싶으시다면 터미널을 여신 뒤 `cd /usr/local/share/dotnet` 명령어를 입력하였을 때 `dotnet` 폴더에 정상적으로 접근이 되는지 확인하시면 됩니다.

Mac에서 Zsh를 사용하시고 계신다면 `~/.zshrc`에, Bash를 사용하고 계신다면 `~/.bash_profile`에 환경 변수를 등록하실 수 있습니다.  
저는 Zsh를 사용하고 있기에 Zsh 기준으로 설명 드리겠습니다.

1. 터미널에서 `vi ~/.zshrc` 를 입력합니다.
2. `~/.zshrc` 문서가 열리면 방향키를 이용하여 문서의 최하단으로 이동합니다.
3. `i` 키를 눌러 Insert 모드를 활성화 합니다.
4. `export PATH="$PATH:/usr/local/share/dotnet/dotnet"` 구문을 추가합니다.
5. `esc` 키를 눌러 Insert 모드를 종료합니다.
6. `:wq` 를 입력하여 변경사항을 저장 후 터미널로 돌아옵니다.
7. `source ~/.zshrc` 를 입력하여 변경 된 path를 적용합니다.
8. VSCode를 재실행합니다.

위 과정을 모두 마치시면 VSCode에서 자동완성 기능이 정상 동작 하는 것을 확인하실 수 있습니다.

![자동완성 정상동작 성공!](/assets/post_images/2024-05-29-unity-vscode-setting-for-mac/autocomplete_success.gif)

자동 완성을 비롯한 Auto Import 기능도 정상적으로 동작 하는 것을 보실 수 있습니다.

# 마치며
제가 Unity에서 VSCode를 쓰고 싶어서 여러 블로그의 설치 가이드를 따라해보다가 실패하길 반복하면서 알게 된 사실을 문서화 하였습니다.  
이 기록이 여러분들의 시간을 조금이나마 아낄 수 있다면 기쁠 것 같습니다.