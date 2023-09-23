---
title: "프론트엔드 개발자가 알아야 할 Mobile UI 이름들"
author:
  name: 박 찬영
  link: https://github.com/univdev
date: 2022-02-14 13:58:00 +0900
categories: [Tech, Talk]
tags: [Frontend, Mobile, UI]
---
# 원문
[Nomenclature of mobile app visual elements — Android vs iOS][원문]

> 해당 문서에 나오는 용어 이름과 사진을 참고하여 제 방식대로 간단하게 설명하였습니다.

# 개요
프론트엔드 개발자는 개발자이기도 하면서 디자이너이기도 합니다.  
사용자에게 어떻게 하면 보다 클릭 횟수가 적고 부드러운 사용자 경험을 줄지 생각해야 하는게 디자이너의 업무와 상당량 겹치기 때문입니다.  
그러다보니 개발자인데도 디자이너와 원활한 대화를 하기 위한 스킬이 필수적입니다. 그런 이유로 프론트엔드 개발자는 특정 UI를 업계에서 뭐라고 부르는지에 대해서 자세히 알고 있어야 합니다.

# Top App bar vs Naivgation bar
![Top App bar vs Navigation bar][Navigation Bar]  
**Android** - Top App Bar  
**iOS** - Navigation Bar

상단에 고정적으로 위치하여 앱 탐색에 직접적인 도움을 주고, 메뉴를 통해 사용자가 다양한 기능을 이용할 수 있도록 배치되는 UI입니다.

# Top Navigation vs Segmented Controls
![Top Navigation vs Segmented Controls][Top Navigation]  
**Android** - Top Navigation  
**iOS** - Navigation Bar

하나의 페이지에서 각각 다른 섹션을 표시하고자 할 때 사용하는 네비게이터입니다.

# Bottom Navigation vs Tab Bar
![Bottom Navigation vs Tab Bar][Tab bar]  
**Android** - Bottom Navigation  
**iOS** - Tab Bar  

하단에 위치하여 페이지를 탐색할 수 있도록 도와주는 네비게이션입니다.

# Dialog vs Alert
![Dialog][Dialog]  
**Android**: Dialog  
**iOS**: Alert

상호작용이 일어날 때, 해당 기능에 대해서 사용자의 의견을 묻거나 해당 기능으로 인해 발생할 상황에 대해 안내할 때 사용합니다.

# Full Screen Dialog vs Popover
![Full Screen Dialog][Full Screen Dialog]  
**Android** - Full Screen Dialog  
**iOS** - Popover

기본적으로는 Dialog처럼 기존 화면 위에 표시 되는데, 보여주거나 입력 받아야 할 내용이 많아서 전체 영역을 덮어야 하는 Dialog를 나타냅니다.

# Confirmation Dialog vs Pickers
![Pickers][Pickers]  
**Android**: Confirmation Dialog  
**iOS**: Pickers

사용자로부터 일정 범위 안에 들어오는 값을 선택하여 받을 때 사용하는 UI 입니다.

# Modal Bottom Sheet vs Activity Views
![Activity Views][Activity View]  
**Android** - Modal Bottom Sheet  
**iOS** - Activity Views

화면 아래부터 올라오는 형태를 가진 UI입니다.  
본래 화면과 추가 정보를 동시에 봐야하는 상황에 주로 쓰입니다.

# Dropdown Menu vs Action Sheet
![Dropdown Menu][Dropdown Menu]  
**Android** - Dropdown Menu  
**iOS** - Action Sheet

사용자에게 여러가지 기능 중 하나를 선택하게 하기 위해 사용하는 UI 입니다.

# Floating Action Button vs Call to Action Button
![Floating Action Button][Floating Action Button]  
**Android**: Floating Action Button  
**iOS**: Call to Action Button

해당 화면에서 고정적으로 사용하는 기능을 어떤 상황에서도 사용할 수 있도록 화면의 특정 영역을 항상 차지하고 있는 UI를 뜻합니다.

[원문]: https://uxdesign.cc/nomenclature-of-mobile-app-visual-elements-android-vs-ios-93685e8ef085
[Navigation Bar]: https://miro.medium.com/max/700/1*5YYfPIUo7HyxJOCdOI-0Dw.png
[Top Navigation]: https://miro.medium.com/max/700/1*euVMKWdXXO7vfeRqTeHMdA.png
[Tab bar]: https://miro.medium.com/max/700/1*ZAZOh951yf7DTMYsluoHMQ.png
[Dialog]: https://miro.medium.com/max/700/1*54O6jdRxxqvLH0Q0G2WH9A.png
[Full Screen Dialog]: https://miro.medium.com/max/700/1*l4yf1h0VCoZhhM8hU7PJyw.png
[Pickers]: https://miro.medium.com/max/700/1*NcEO2BzHi7y7b-916bXbbw.png
[Activity View]: https://miro.medium.com/max/700/1*jJm5PX8kbvH26KO-UCsg0g.png
[Dropdown Menu]: https://miro.medium.com/max/700/1*GK0VdXo4UUkuQTnvX3UIAg.png
[Floating Action Button]: https://miro.medium.com/max/700/1*w3Zjg-O-PB00lgx6swvVRw.png