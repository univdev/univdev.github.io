---
title: "사용자의 액션을 분석해보자! Amplitude의 Taxonomy 연동 방법"
author:
  name: "박 찬영"
  link: https://github.com/univdev
categories: [Tech, Talk]
tags: [Taxonomy, Amplitude, React]
date: 2023-08-06 23:25:00 +0900
image:
  path: /assets/post_images/2023-08-06-amplitude-taxonomy-for-fe/2023-08-22-15-48-49.png
  alt: Amplitude Taxonomy 연동 가이드
---
![Amplitude Taxonomy 연동 가이드](/assets/post_images/2023-08-06-amplitude-taxonomy-for-fe/2023-08-22-15-48-49.png)
## 들어가며
안녕하세요 😀 박찬영입니다.  
저는 회사에서 신규 서비스 런칭을 앞두고 있기 때문에 정말 많은 Task가 몰아치는 하루를 보내고 있어요 😭  
그렇게 해결했던 수많은 Task 중 문서로 남겨두면 유용할 법한 내용을 가져왔어요!
## Amplitude란?
Amplitude는 서비스의 사용성을 증대시키는데 도움을 줄 수 있는 다양한 정보를 수집하고 응용하기 위한 서비스입니다.

![Amplitude에서 지원하는 서비스들](/assets/post_images/2023-08-06-amplitude-taxonomy-for-fe/2023-08-06-23-35-42.png)
## Taxonomy
Taxonomy란 유저들이 어떠한 ‘행위’를 했을 때 해당 행위에 대한 데이터들을 수집하여 유저가 ‘어떤 버튼을 많이 눌렀는가’, ‘이 행동을 한 유저는 어떠한 공통점을 보이는가’ 등  
유저들의 상황을 이해하고 이를 기반으로 서비스의 사용성을 증대시키기 위한 행동 수집 방식입니다.  
본 문서에서는 많은 서비스들이 사용하는 서비스인 Amplitude를 사용하여 Taxonomy Event를 수집하는 방법에 대해 알아보도록 하겠습니다.
## Amplitude Browser SDK 설치
Amplitude에 이벤트를 전송하거나 유저 정보에 대한 설정을 하기 위해서는 Browser SDK가 필요합니다.  
Amplitude는 다양한 플랫폼과 패키지매니저를 지원하므로 가급적 [npm을 이용하여 설치](https://www.npmjs.com/package/@amplitude/analytics-browser) 하는 것을 권장합니다.
### npm
```bash
npm i -S @amplitude/analytics-browser
```
### yarn
```bash
yarn add @amplitude/analytics-browser
```
## SDK 설정
위에서 설치한 SDK를 프로젝트에 사용하기 위해서는 Amplitude 서비스와 내 프로젝트를 연결하는 과정이 필요합니다.
### Amplitude API Key 발급
[Find your Amplitude Project API Credentials - Amplitude Developer Center](https://www.docs.developers.amplitude.com/analytics/find-api-credentials/)

1. [설정](https://analytics.amplitude.com/pixo/settings/projects) 페이지에 접근합니다.
2. Projects 페이지에서 API Key를 발급 받을 프로젝트를 선택합니다.
    ![프로젝트 페이지 접속](/assets/post_images/2023-08-06-amplitude-taxonomy-for-fe/2023-08-06-23-37-15.png)
3. 이 곳에서 API Key를 발급 받을 수 있습니다.
    ![API Key 발급](/assets/post_images/2023-08-06-amplitude-taxonomy-for-fe/2023-08-06-23-37-43.png)
### 프로젝트 적용
일반적인 라이브러리의 초기화 방법과 동일하다고 보시면 됩니다.
1. 어느 곳에 접속하더라도 프로젝트에 대한 초기화가 이루어질 수 있도록 프로젝트 진입부에 아래와 같은 코드를 추가 해야 합니다.
    ```bash
    import * as amplitude from '@amplitude/analytics-browser';
    
    amplitude.init('<YOUR_API_KEY>');
    ```
2. Next.js의 경우 `_app.tsx` 페이지에 코드를 적용하면 됩니다.
    1. 다만 저의 경우는 `_app.tsx` 파일이 각종 라이브러리의 초기화 로직으로 지저분해지는 것을 방지하기 위해 `AmplitudeSDK.tsx` 컴포넌트를 생성하여 코드를 작성하였습니다.
        ```jsx
        // AmplitudeSDK.tsx
        
        import * as amplitude from '@amplitude/analytics-browser';
        import { HTMLAttributes, useEffect } from 'react';;
        
        export interface AmplitudeSDKProps extends HTMLAttributes<HTMLDivElement> {
        }
        
        export const AmplitudeSDK = ({ ...others }: AmplitudeSDKProps) => {
          useEffect(() => {
            amplitude.init(process.env.AMPLITUDE_TAXONOMY_API_KEY as string);
          }, []);
        
          return (
            <></>
          );
        };
        ```
        이렇게 만들어진 `AmplitudeSDK.tsx` 파일은 `_app.tsx`에 적용하면 됩니다.
        ```jsx
        <QueryClientProvider>
          <Provider store={store}>
            <AppHead />
            <AmplitudeSDK /> // 이렇게 추가해 주세요
        		{ Layout(<Component { ...props.pageProps } {...props} />) }
          </Provider>
        </QueryClientProvider>
        ```
## 계획 된 이벤트 설정 방법
본격적으로 Taxonomy를 활용한 이벤트 수집을 시작하기 전에 이벤트를 설정하는 방법에 대해 알아보겠습니다.
### 이벤트 목록 확인하기
![대시보드](/assets/post_images/2023-08-06-amplitude-taxonomy-for-fe/2023-08-06-23-38-43.png)  
[Amplitude Dashboard 페이지](https://app.amplitude.com/analytics/pixo/home)에서 **[Data]** 항목을 선택합니다.

![이벤트 목록](/assets/post_images/2023-08-06-amplitude-taxonomy-for-fe/2023-08-06-23-39-12.png)  
좌측 메뉴의 **[Event]** 탭을 클릭하면 프로젝트에 설정 된 이벤트를 볼 수 있습니다.
### Taxonomy Status
![](/assets/post_images/2023-08-06-amplitude-taxonomy-for-fe/2023-08-06-23-39-54.png)  
![](/assets/post_images/2023-08-06-amplitude-taxonomy-for-fe/2023-08-06-23-40-07.png)  
![](/assets/post_images/2023-08-06-amplitude-taxonomy-for-fe/2023-08-06-23-40-24.png)  

Amplitude의 이벤트 Status에는 세 가지 [Status]가 존재합니다.

- Live: 사전에 수집하기로 했었던 이벤트이며, 서비스에서 수집 된 케이스가 하나 이상 존재하는 이벤트
- Planned: 계획은 했지만 서비스에서 수집 된 데이터가 없는 이벤트
- Unexpected: 계획하지 않았지만 서비스에서 수신 된 이벤트

세 가지 상태에서 실제로 유의미하게 사용할 수 있는 데이터는 [Planned] 데이터이므로 사전에 필요할 것 같은 데이터를 정의하는 과정이 필요합니다.
### 이벤트 수집 계획 작성하기
![이벤트 수집 계획](/assets/post_images/2023-08-06-amplitude-taxonomy-for-fe/2023-08-06-23-40-44.png)  
이벤트 페이지 우측 상단에 존재하는 **[Create event]** 버튼을 누르면 다음 프로세스를 통해 이벤트 계획을 수립할 수 있습니다

1. 이벤트 이름을 작성합니다.
2. 이벤트에 대한 세부 설정을 진행합니다.
    
    ![이벤트 생성 이후 화면](/assets/post_images/2023-08-06-amplitude-taxonomy-for-fe/2023-08-06-23-41-08.png)  
    세부 설정으로는 이벤트 프로퍼티, 카테고리, 수집 상태 등을 설정할 수 있습니다.
### 이벤트 프로퍼티
유저의 행동 패턴을 분석할 때 “어떤 유저가 버튼을 눌렀대요” 라는 막연한 사실만을 통보받는다면 해당 유저가 왜 하필 그 시간대에 버튼을 눌렀는지와 같은 세부적인 추론을 할 수 없습니다.  
그러한 니즈를 해결하기 위해 이벤트를 실행하는 사람의 의도를 추론하기 위한 여러 단서를 붙여서 이벤트와 함께 전송할 수 있는데 이를 **이벤트 프로퍼티**라고 부릅니다.

![이벤트 프로퍼티 설정 화면](/assets/post_images/2023-08-06-amplitude-taxonomy-for-fe/2023-08-06-23-41-41.png)  
[Add Property] 버튼을 클릭하면 프로퍼티를 추가할 수 있습니다.  
추가 된 프로퍼티를 클릭하여 세부 정보를 살펴보면 

![이벤트 프로퍼티 타입 설정 화면](/assets/post_images/2023-08-06-amplitude-taxonomy-for-fe/2023-08-06-23-42-17.png)  
이처럼 해당 프로퍼티에 들어갈 값을 설정할 수 있습니다.

이벤트 프로퍼티는 문자열, 숫자, Enum 등 다양한 자료형을 지원합니다.
만약 하나의 이벤트에 다수의 프로퍼티를 설정한다면 추후 `json` 자료형을 이용하여 한번에 다수의 프로퍼티를 설정할 수도 있습니다.
## Amplitude에 이벤트 전송하기
이벤트를 전송하는 방법은 매우 간단합니다.

Amplitude SDK에 내장 되어 있는 `track` 메소드를 사용하면 전송할 수 있습니다.
```jsx
import { track } from '@amplitude/analytics-browser';

track('event-name');
```

단순 이벤트의 발생 여부에 대해서만 전송한다면 위와 같이 사용할 수 있습니다.  
만약 이벤트 프로퍼티와 함께 이벤트를 전송해야 한다면 다음과 같이 작성하면 됩니다.

```jsx
import { track } from '@amplitude/analytics-browser';

track({
	'event-name': 'click-button',
	'event-property': {
		'button-color': 'red',
		'button-identity': 'redirect-home',
	}
});
```

만약 연결하는데 성공했을 경우 해당 이벤트를 발생시키면 브라우저 개발자도구 [Network] 탭에서 다음과 같은 요청을 볼 수 있습니다.  
![Amplitude Network Request](/assets/post_images/2023-08-06-amplitude-taxonomy-for-fe/2023-08-06-23-42-51.png)  
짧은 순간에 여러 차례의 이벤트 송신이 발생하면 일정시간 뒤에 발생했던 요청들을 한번에 묶어서 전송하는 모습을 보실 수 있습니다.

이 로직은 `@amplitude/analytics-browser` 라이브러리가 내부적으로 처리하기 때문에 개발자 입장에서는 고려할 필요가 없다는 장점이 있습니다.
## 선 송신 후 계획
우리는 [[Taxonomy Status]](https://www.notion.so/Amplitude-Taxonomy-for-FE-bdd56ef231a04526a163ba443c2a541d?pvs=21) 항목을 통해 ‘예측되지 못한 이벤트도 수신할 수 있구나’ 라는 사실을 알게 되었습니다.
`Unexpected` 상태는 어떻게 사용하면 좋을까요?

Amplitude는 `Unexpected` 상태의 이벤트를 `Planned`로 바꿀 수 있는 솔루션을 제공하고 있습니다.

![Unexpected 이벤트 조회](/assets/post_images/2023-08-06-amplitude-taxonomy-for-fe/2023-08-06-23-43-22.png)  
상단을 보시면 [Review Unexpected] 탭이 존재하는데, 이 탭에서 예측되지 못한 이벤트들을 모아서 볼 수 있습니다.

![Unexpected 이벤트 상세 페이지](/assets/post_images/2023-08-06-amplitude-taxonomy-for-fe/2023-08-06-23-44-05.png)  
예측되지 못한 이벤트 중 하나를 선택하면 [This event is not in your tracking plan.] 옆에 보이는 `Add` 버튼이 존재합니다.

예측되지 못한 이벤트를 이런식으로 계획의 범주 안에 포함 시킬 수 있습니다.

그러면 이걸 어떻게 써먹을 수 있을까요?
예상 되는 시나리오는 크게 두 가지 있습니다.

### #1. 계획은 나중에 등록하고 일단 코딩부터 할 때
Amplitude에 수많은 이벤트를 등록하는 것도 하나의 비용입니다.
Taxonomy를 개발자에게 연결 해달라고 요청을 할 때 계획 작업이 완료 되어있지 않으면 개발자는 일을 진행할 수가 없는 의존 관계가 형성이 되겠죠.

위에서 설명한 방법을 이용한다면 개발자가 먼저 모든 이벤트들을 적재적소에 배치하고 송신하면 예측되지 못한 이벤트로 수집이 될 것이고, 이러한 이벤트들을 추후에 Amplitude에서 계획 이벤트로 변경하여 등록하는 방식으로 업무를 수행할 수 있습니다.

### #2. 사전에 계획하지 못한 행동을 수집할 때
이벤트를 계획하고 수집을 시작했더니 무시할 수 없는 숫자의 사용자가 예상과는 다른 행동을 보일 때 예측되지 못한 이벤트가 수집 될 수 있습니다.  
이러한 사용자들의 패턴도 분석하고 싶다면 향후 계획 이벤트로 변경하여 정식으로 수집하는 것도 하나의 방법이 될 수 있습니다.