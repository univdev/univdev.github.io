---
title: "[React Native] Android에서 캡처 방지하기"
author:
  name: 박 찬영
  link: https://github.com/univdev
date: 2022-01-28 10:53:00 +0900
categories: [Tech, React Native]
tags: [React Native, Android]
---
# 개요
특정 회사의 내부 인원들만 사용하는 안드로이드 전용 어플리케이션을 개발하게 되었습니다.  
해당 앱에는 특정 조직의 구성원들의 정보가 들어있는데, **사진이나 현 직장 정보 및 전화번호와 같은 매우 민감한 정보들이 포함되어 있기 때문에** 앱스토어에도 올리지 않고 조직 내부 인원들에게 따로 공유해서 사용할 앱입니다.  
그렇기 때문에 보안사항이 꽤 중요하게 작용하는데, 화면 캡처도 당연히 방지해야 하는 상황입니다.  
Android는 User Level Application이 OS Level의 기능을 핸들링하는걸 꽤 관대하게 여기기 때문에 분명히 해당 기능이 있을 것이라 판단하고 방법을 찾았습니다.  
역시 기대를 저버리지 않았고, 해당 기능을 어떻게 적용했는지에 대해 적어보고자 합니다.
# 캡처 차단하는 방법
1. ```/android/app/src/main/java/com/{Project_Name}/MainActivity.java``` 파일에 접근합니다.
2. 코드를 ```MainActivity``` 클래스에 적용합니다.
  ```java
  @Override
  protected void onCreate(Bundle savedInstanceState) {
      super.onCreate(savedInstanceState);
      getWindow().setFlags(WindowManager.LayoutParams.FLAG_SECURE, WindowManager.LayoutParams.FLAG_SECURE);
  }
  ```
3. 해당 문서의 상단부에 아래 코드를 적용시킵니다.
  ```java
  import android.os.Bundle;
  import android.view.WindowManager;
  ```
4. 스크린샷을 찍어보면 '보안정책에 따라 화면을 캡처할 수 없어요' 라고 나오는 것을 확인할 수 있습니다.

# 주의할 점
저의 테스트 환경은 M1Pro 맥북의 Android 애뮬레이터입니다.  
여기서 테스트를 한 결과, 애뮬레이터에서 지원하는 스크린샷 기능은 정상적으로 작동하기 때문에 <u>제대로 차단이 되었는지를 확인하고 싶다면 실제 안드로이드 기기를 연결하여 확인을 해봐야 합니다.</u>