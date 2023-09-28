---
title: "Taxonomy 대응을 위한 좋은 아키텍처"
author:
  name: 박 찬영
  link: https://github.com/univdev
date: 2023-08-23 19:47:00 +0900
categories: [Tech, Architecture]
tags: [React, Architecture, Taxonomy]
toc: true
render_with_liquid: false
image:
  path: /assets/post_images/2023-08-21-good-taxonomy-architecture/2023-08-23-19-32-03.png
  alt: "Taxonomy 대응을 위한 좋은 아키텍처"
---
![Taxonomy 대응을 위한 좋은 아키텍처](/assets/post_images/2023-08-21-good-taxonomy-architecture/2023-08-23-19-32-03.png)

안녕하세요 박찬영입니다 🙂

이번 포스트에서는 Taxonomy를 연동하면서 겪었던 어려운 문제와 Challenge 경험을 공유하기 위해 작성합니다.
동일한 문제를 앓았거나 앓을 예정이신 분들께서 보시고 좋은 영감을 얻어가셨으면 좋겠습니다.

# Taxonomy란?
본 문서의 이해를 위해 간단하게 설명하자면 **‘유저의 행동 패턴을 분석하기 위한 방법’** 입니다.

사용자는 대부분의 경우 개발자의 의도대로 움직여주지 않습니다.  
누르라고 만든 버튼을 사용자가 못찾는다거나 사용 빈도가 적을 것으로 예상 되었던 기능이 가장 호응을 많이 받게 되는 경우가 있는데 이런 상황은 기획과 개발 단계에서 예측할 수 없죠.

심지어 사용자는 본인이 무엇을 원하는지 서비스 제공자에게 알려주지 않습니다.  
서비스 제공자는 사용자가 원하는 니즈를 알아 맞추는 퀴즈를 풀어야만 하는데 Taxonomy는 이를 풀어내는데 결정적인 도움을 제공합니다.

‘사용자가 버튼을 눌렀다’ 는 간단한 이벤트를 알려주는 것 뿐 아니라 해당 사용자가 어떤 사용자인지, 어떤 타이밍에 눌렀는지, 어떤 작업을 하다 눌렀는지 등을 유추할 수 있도록 각종 프로퍼티를 함께 전송할 수 있습니다.

# 좋은 아키텍처가 필요한 이유

제가 이번에 진행했던 Taxonomy 연동 작업에서 실제로 있었던 일을 예시로 설명 드리겠습니다.

![에디터 와이어프레임](/assets/post_images/2023-08-21-good-taxonomy-architecture/2023-08-28-20-17-38.png)

위 사진은 제가 개발한 서비스의 와이어프레임입니다.  
좌측에 보여지는 템플릿 목록에서 템플릿을 하나 선택하면 사용자가 편집하고 있는 에디터에 해당 템플릿이 반영 되는 기능이 있어요.

해당 기능을 위한 Flow는 아래와 같습니다.

- 좌측 템플릿 패널에서 카테고리를 선택하여 카테고리에 맞는 템플릿을 조회할 수 있습니다.
- 템플릿을 선택하면 현재 편집하고 있는 디자인에 덮어쓰기 됩니다.

![디자인 교체 Flow 와이어프레임](/assets/post_images/2023-08-21-good-taxonomy-architecture/2023-08-28-20-20-18.png)

이런 식으로 디자인의 교체가 이루어지는 것이죠.

## 예시 이벤트

지금부터 Taxonomy 연동 작업 중 어려웠던 여러 사례를 설명 드리기 위해 제가 실제로 작업 했던 이벤트를 가지고 설명하겠습니다.

제가 PO분께 전달 받은 Taxonomy Event Spec에는 다음과 같은 Event가 있었습니다.

- 사용자가 [편집 된 디자인을 저장] 할 경우 이벤트 전송
    - **[이벤트 프로퍼티]**
        - 템플릿의 id (만약 템플릿이 교체 되었다면)
        - 해당 템플릿을 선택할 당시에 선택했던 카테고리의 id (만약 템플릿이 교체 되었다면)

## #1. 데이터 Scope에 대한 변경이 자주 발생한다.

위에서 설명한 이벤트를 올바르게 전송하기 위해서는 [내가 템플릿 교체 당시 선택했던 카테고리]와 [교체한 템플릿의 정보]를 알아야 합니다.
하지만 이는 현재 구조에서는 불가능한 작업이었습니다.

![편집 플로우 그래프](/assets/post_images/2023-08-21-good-taxonomy-architecture/2023-08-27-23-03-36.png)

위 이미지는 에디터 화면의 컴포넌트 구조를 나타낸 그래프입니다.

보시면 [템플릿 패널] 컴포넌트 내부에서 카테고리와 템플릿 리스트에 대한 정보를 모두 가지고 있는데, 정작 저장이 이루어지는 곳은 [헤더] 컴포넌트 하위에 존재하는 [저장 버튼] 컴포넌트입니다.

처음 설계 당시에는 Taxonomy Spec이 정해지지 않았기 때문에 템플릿 탐색에 필요한 데이터를 굳이 전역으로 올려서 다루지 않았기에 생긴 일이죠.

이 문제를 해결하려면 [템플릿 패널] 컴포넌트에서 다루고 있는 데이터를 **[편집 페이지]와 동일하거나 더욱 상위의 개념으로 끌어올려야 합니다.**

![편집 플로우 그래프 (데이터 노드가 추가 된 버전)](/assets/post_images/2023-08-21-good-taxonomy-architecture/2023-08-27-23-07-51.png)

다른 Scope에서 사용하고 있는 데이터를 요구하는 택소노미의 경우 데이터 자체의 Scope를 변경하지 않으면 사용할 수가 없습니다.  
하지만 단순히 값을 전역 레벨로 올려 사용하게 되면 관심사 분리가 모호해지고 이는 추후 기술적인 빚을 야기할 수 있습니다.

## #2. 불필요한 데이터의 개입

Taxonomy 연동 과정이 어려운 두 번째 이유는 사용자의 경험을 위해 설계한 Flow에 불필요한 데이터가 개입하게 된다는 점입니다.

예시를 하나 들어볼게요.

![저장 플로우 그래프](/assets/post_images/2023-08-21-good-taxonomy-architecture/2023-08-27-23-28-50.png)

단순하게 생각했을 때 [저장]이라는 행위를 처리하기 위해 필요한 데이터는 현재 작업물에 필요한 데이터 뿐입니다.

![저장 플로우 그래프 (데이터 노드가 추가 된 버전)](/assets/post_images/2023-08-21-good-taxonomy-architecture/2023-08-27-23-30-37.png)

하지만 저장 로직에 Taxonomy Event에 대한 전송 로직을 추가 하기 위해서는 저장과는 관련이 없을 수도 있는 데이터가 저장 Flow에 개입하게 됩니다.

이 과정에서 택소노미 코드와 저장 코드를 명확하게 분류하지 못한다면 이해하기 모호하고 유지보수하기 굉장히 힘든 Flow로 변질 될 가능성이 있습니다.

## #3. 오염되는 소스코드

[저장] 기능은 단순히 사용자가 저장 버튼을 눌렀을 때만 실행 되는 것이 아닙니다.

예기치 못한 상황에서 작업물을 보호하기 위해 작업물을 저장하는 등 여러 부분에서 저장이 이루어지기 때문에 Taxonomy에 대한 로직도 시나리오마다 대응을 해줘야 합니다.

![여러 Case에 대한 저장 로직](/assets/post_images/2023-08-21-good-taxonomy-architecture/2023-08-27-23-32-43.png)

이러한 Task는 자연스럽게 Taxonomy 관련 코드를 파편화 되게 만들며, 더 나아가 Taxonomy Event Property에 대한 관리도 어렵게 만들기 때문에 유지보수를 위해서 Taxonomy의 처리와 관련한 모든 함수를 한 곳에 모아 둘 필요가 있습니다.

# Context를 이용한 Taxonomy 로직 분리

## 페이지 구조 분석

[Context](https://react.dev/learn/passing-data-deeply-with-context)는 Nested Component 구조에서 데이터를 여러 컴포넌트에 효율적으로 전파하기 위한 가장 간단한 솔루션입니다.

Taxonomy 로직 분리를 위해 우선 현재 프로젝트의 컴포넌트 구조를 다시 살펴봅시다.

![컴포넌트 그래프](/assets/post_images/2023-08-21-good-taxonomy-architecture/2023-08-27-23-33-55.png)

위 사진은 편집 페이지의 컴포넌트 구조입니다.  
사용자는 Root에 해당하는 [페이지] 레벨로 진입하여 서비스를 이용하게 됩니다.

Taxonomy가 수집하고자 하는 정보들은 유저의 행동 패턴이기에 이를 토대로  
**‘사용자의 모든 행위는 페이지에서 이루어지니까 Taxonomy 분리도 페이지 단위로 할 수 있다.’**  
는 결론을 세울 수 있게 됩니다.

## Taxonomy 수집을 위한 Values Context 생성

![컴포넌트 그래프 (데이터 노드가 추가 된 버전)](/assets/post_images/2023-08-21-good-taxonomy-architecture/2023-08-27-23-36-54.png)

저는 이러한 결론을 토대로 페이지 레벨을 아우르는 Context를 만들었습니다.

이 Context는 해당 페이지에서 전송 되어야만 하는 Taxonomy 이벤트와 관련한 Value들을 저장하는 Context입니다.

- 템플릿 패널에서 사용자가 조회하는 카테고리 리스트의 id
- 템플릿 패널에서 사용자가 선택한 템플릿의 id

이로 인해 비즈니스 로직을 자체적으로 수행하는 컴포넌트 내부에서만 갖고 있는 여러 데이터들을 상위 레벨로 끌어올려 적절하게 사용할 수 있게 되었습니다.

또한 React의 Context는 Provider를 생성할 때마다 Value를 초기화 해야합니다.  
Value를 초기화 하는 코드가 페이지 컴포넌트에서 너무 많은 비중을 차지하게 되면 코드 가독성을 해칠 수 있으니  
컴포넌트 생성부에서 공용으로 사용할 Provider도 같이 정의합니다.

```tsx
// 택소노미 데이터를 핸들링 하기 위한 Context입니다.
export const TaxonomyValuesContext = createContext<TaxonomyValuesContextArguments>({});

export const TaxonomyValuesContextProvider = ({ children }: { children: ReactNode }) => {
  const [selectedTemplateCategory, setSelectedTemplateCategory] = useState<TaxonomyTemplateCategory>();
  const [selectedTemplateId, setSelectedTemplateId] = useState<string>();

  return (
    <TaxonomyValuesContext.Provider
      value={{
        selectedTemplateCategory,
        setSelectedTemplateCategory,
        selectedTemplateId,
        setSelectedTemplateId,
      }}
    >
      { children }
    </TaxonomyValuesContext.Provider>
  );
};
```

<aside>
⚠️ 이렇게 생성한 Context는 순수하게 Taxonomy 수집 용도로만 사용 되어야 합니다.  
비즈니스 로직에 사용 될 경우 목적이 불분명해지고 추적할 수 없는 리렌더링이 이루어질 수 있습니다.

</aside>

## Taxonomy 수집을 위한 Event Context 생성

Taxonomy에서 수집하고자 하는 이벤트는 비즈니스 로직에 포함 되어야만 하는 경우가 있을 수 있습니다.

- 파일의 전송이 완료 된 경우
- 사용자가 게시물을 업로드 했으나 네트워크 요청에 실패한 경우
- 사용자가 결제를 완료 했을 때 지불 된 금액과 상품명을 수집하는 경우

위와 같은 여러 상황에서 이벤트를 수집하려고 하면 Taxonomy 코드가 여러 컴포넌트 내부에 놓이게 됩니다.

![Taxonomy 로직이 각 컴포넌트 내부에 정의 된 컴포넌트 그래프](/assets/post_images/2023-08-21-good-taxonomy-architecture/2023-08-27-23-38-24.png)

위 그래프와 같은 상황에서는 다음과 같은 문제점을 발생시킬 수 있어요.

- 특정 이벤트를 삭제 해야하는데 도대체 어디에 작성 된 건지 모르겠어요.
- 동일한 이벤트의 프로퍼티를 수정해야 하는데 너무 여러곳에서 사용 되어서 수정하기가 힘들어요.
- 도대체 갑자기 왜 이벤트 전송이 발생하는지 추적할 수가 없어요.

이러한 문제를 해결하기 위해 이벤트 전송 관련 함수만을 포함하는 Event Context를 따로 생성하였습니다.

![Taxonomy 로직을 Context에 위임한 컴포넌트 그래프](/assets/post_images/2023-08-21-good-taxonomy-architecture/2023-08-27-23-40-41.png)

Event Context를 그래프와 같이 생성함으로써 다음과 같은 이점을 얻을 수 있습니다.

- Taxonomy Event 전송을 위한 함수의 구현부를 한 곳에 집약할 수 있어요.
- 파편화 현상이 없어지므로 추후 이벤트의 추적과 수정이 용이해요.

또한 Value Context와 마찬가지로 Provider 초기화 코드를 페이지에 작성하지 않기 위해 Provider를 따로 정의하면 페이지 컴포넌트의 코드를 깔끔하게 유지할 수 있습니다.

```tsx
export const TaxonomyEventsContext = createContext<TaxonomyEventsContextArguments>({});

export const TaxonomyEventsContextProvider = ({ children }: { children: ReactNode }) => {
  const {
    selectedTemplateCategory,
    selectedTemplateId,
  } = useContext(TaxonomyValuesContext);

  const setTaxonomyCreateSave = () => {
    // 저장 시 Taxonomy 이벤트 전송
    track({
      event_type: 'create_save',
      event_properties: {
        selectedTemplateCategory,
        selectedTemplateId,
      },
    });
  }

  return (
    <TaxonomyEventsContext.Provider
      value={{
        setTaxonomyCreateSave,
      }}
    >
      { children }
    </TaxonomyEventsContext.Provider>
  );
};
```

## 복잡한 Context 정리

페이지 컴포넌트는 개발 도중 발생하는 여러 이슈로 인해 이미 Context Provider가 매우 복잡하게 선언 된 상황일 수 있습니다.

```tsx
<TaxonomyValueContextProvider>
  <TaxonomyEventContextProvider>
    <FirstProvider>
      <SecondProvider>
        <ThirdProvider>
          { children }
        </ThirdProvider>
      </SecondProvider>
    </FirstProvider>
  </TaxonomyEventContextProvider>
</TaxonomyValueContextProvider>

// Terrible Context Hell
```

위에서 설명한 아키텍처를 채용했다면 페이지 컴포넌트에는 두 개의 Provider가 추가로 존재하게 됩니다.

Context Hell 현상은 그 자체만으로 코드의 가독성을 해치며 코드를 읽기 싫게 만듭니다.

Taxonomy Value와 Taxonomy Event Context는 ‘이벤트 전송’이라는 하나의 역할에 충실한 Context이기 때문에 이 둘을 하나로 묶으면 Provider를 한 개로 줄일 수 있습니다.

```tsx
// Taxonomy.tsx

import { ReactNode } from 'react';

import { TaxonomyEventsContextProvider } from './TaxonomyEvents/context';
import { TaxonomyValuesContextProvider } from './TaxonomyValues/context';

export const TaxonomyContextProvider = ({ children }: { children: ReactNode }) => {
  return (
    <TaxonomyValuesContextProvider>
      <TaxonomyEventsContextProvider>
        { children }
      </TaxonomyEventsContextProvider>
    </TaxonomyValuesContextProvider>
  );
};
```

```tsx
// page.tsx

<TaxonomyContextProvider>
  <FirstProvider>
    <SecondProvider>
      <ThirdProvider>
        { children }
      </ThirdProvider>
    </SecondProvider>
  </FirstProvider>
</TaxonomyContextProvider>

// A little terrible Context Hell
```

# 마치며

Taxonomy를 연동하면서 가장 어려웠던 점은 기존 로직과의 의존성을 최대한 배제하는 작업이었습니다.

이로 인해 기존 Flow를 전면적으로 수정 하느라 힘든 부분들이 다소 있었지만 의존성을 덜어내기 위한 아키텍처를 진지하게 고민해볼 수 있었고, 프로젝트의 구조를 다시 살펴볼 수 있는 시간이 되어서 나름 유의미한 시간이었다 생각합니다.

저의 삽질이 다른 많은 사람들의 시간을 절약해 줄 수 있길 바라며 긴 글 마치겠습니다.

읽어주셔서 감사합니다 🙌