---
title: "Atomic Design 설계와 Container Component"
author: 박 찬영
date: 2022-09-27 22:33:00 +0900
categories: [Tech, Talk]
tags: [Atomic Design]
---
# 개요
저는 몇 개월 전부터 `Atomic Design` 방법론에 빠져있어요.  
애초부터 비즈니스 로직과 UI 컴포넌트 로직은 완벽히 분리되어야 한다는 철학이 있었던 저에게 있어 `Atomic Design`은 그 어떤 설계방법보다 아름답다고 느꼈거든요.
# Atomic Design
![아토믹 디자인](/assets/post_images/2022-09-27-atomic-design-with-container-component/20220927235625.png)
_아토믹 디자인하면 많이 보는 이미지_

`Atomic Design`은 이미 유명한 방법론이고 컴포넌트 개발방식을 이용한 요즘 프론트엔드 프레임워크를 사용하는 프론트엔드 개발자라면 한 번 쯤은 들어보신적 있는 방법론일거예요.  
쉽게 말해, **컴포넌트를 현실에 존재하는 물질로서 간주하고 역할을 세분화하는 방법론이예요.**

`Atomic Design`은 보편적으로 5단계의 세분화 과정을 거쳐요.

![원자 레벨 컴포넌트](/assets/post_images/2022-09-27-atomic-design-with-container-component/20220927235939.png)
_대표적인 원자 레벨 컴포넌트인 Input_
## Atom
1. 현실에 존재하는 원자를 비유적으로 표현한 레벨이예요. 원자 레벨이라고 불러요
2. 실제 원자처럼 더 이상 작게 쪼갤 수가 없는 컴포넌트로서, `Input`이나 `Text`와 같은 아주 간단한 기능만을 수행하는 컴포넌트가 원자 레벨 컴포넌트예요.
3. 앞으로 소개할 4단계 중 **가장 재사용성이 높은 컴포넌트라고 볼 수 있어요.**

![분자 레벨 컴포넌트](/assets/post_images/2022-09-27-atomic-design-with-container-component/20220927235845.png)
_Input과 Label이 합쳐진 분자 컴포넌트 Field_
## Molecule
1. 현실에 존재하는 분자를 비유적으로 표현한 레벨이예요. 분자 레벨이라고 불러요
2. 실제 분자처럼 여러 원자 컴포넌트가 합쳐져서 만들어진 컴포넌트예요.
3. `label`과 `input`이 공존하는 `field` 컴포넌트를 예로 들 수 있어요.
4. 원자 레벨의 컴포넌트와 마찬가지로 프로젝트 전체에서 재사용 될 가능성이 높은 컴포넌트예요.

![유기체 레벨 컴포넌트](/assets/post_images/2022-09-27-atomic-design-with-container-component/20220928000000.png)
_다양한 Field와 Button 등이 혼합 된 대표적인 유기체 Form_
## Organism
1. 현실에 존재하는 유기체를 비유적으로 표현한 레벨이예요. 유기체 레벨이라고 불러요
2. 여기서부터는 특정 페이지에만 존재하는 컴포넌트들이 개발될 수 있어요.
3. 경우에 따라 비즈니스 로직을 내포하거나 컴포넌트 내부에 상태나 로직을 선언하여 사용할 수 있어요
4. 재사용 할 수 있는 구조로 만드는게 최고지만 원자나 분자 레벨의 컴포넌트처럼 반드시 준수해야 하는건 아니예요.

## Template
1. 특정 페이지에 접속하면 보이게 되는 레이아웃의 원형이예요.
2. 서버나 Redux와 같은 상태 관리자로부터 데이터를 넘겨받아 `Prop`으로 전달하면 실제 데이터가 바로 보여질 수 있도록 범용성있게 개발해야 하는 컴포넌트예요.
3. 만약 데이터를 넘겨주지 않은 상태로 사용을 한다고 해도 페이지를 구성하는데 있어 임시 UI라도 보여지도록 설계하는게 좋아요

## Page
1. Template 컴포넌트를 직접 사용하는 단계예요.
2. `Next.js`나 `Nuxt.js`를 사용하면 `pages` 폴더 안에 페이지를 정의하잖아요? 그 레벨이예요.
3. 여기서는 `Middleware`를 통해 사용자 자격 검증을 진행하기도 하고 서버와의 직접적인 통신으로 데이터를 불러올 수도 있어요.
4. UI를 페이지에서 구현할 때는 반드시 작성 된 `Template`만을 사용할 수 있어요.
5. 불러온 데이터를 `Template` 컴포넌트에 전달하여 유의미한 데이터를 화면상에 표시할 수 있는거죠.

# 겁도 없이 덤비는 낯선이여
위 설명만 들어보면 정말 완벽하게 독립적인 UI 컴포넌트를 만들 수 있을 것 같은 예감이 듭니다.  
당신은 이제 **"오! 이제 알겠어! 나는 이제 컴포넌트 개발 마스터다!"** 라는 패기로운 마인드로 개발을 시작하게 됩니다.

하지만 곧 이론과 현실은 다름을 깨달으며 `Atomic Design`을 거두게 됩니다.
## Prop Drilling
이제 `Atomic Design`을 마스터했다고 생각한 당신은 회사 프로젝트에 적용을 해보기로 합니다.  
근데 예상보다 프로젝트가 커지면서 컴포넌트의 `Depth`가 깊어집니다.

점점 무거워지는 `Props`를 보며 **"아 뭔가 망한 것 같은데"** 라는 생각이 들게 됩니다.

![Prop Drilling](/assets/post_images/2022-09-27-atomic-design-with-container-component/20220928000705.png)
_???: 나는 필요도 없는 데이터를 자꾸 넘겨주네_

`Atom`이나 `Molecule` 단계의 컴포넌트는 아무리 하찮더라도 본연의 역할을 다하기 위해서라면 `Props`가 반드시 필요합니다.  
하지만 최하위 레벨까지 값을 내려주기 위해서는 거쳐야만 하는 관문들이 너무나 많아졌어요.  
만약 당신이 `Atomic Design` 방법론과 함께 `Typescript`까지 같이 사용하고 있다면 상황은 최악으로 치닫게 됩니다.  
컴포넌트가 받아야 할 `Props`에 대한 `Interface`를 어떻게 관리해야 할지도 막막할거거든요.

당장 그 컴포넌트에 필요하지도 않은 데이터들인데 `Interface`에 넣고 관리하고 있노라면 퇴근 생각이 절실해집니다.
## Tooltip이랑 Modal은 어디다가 놔야 돼?
문제는 또 있습니다. 버튼을 눌렀을 때 `Tooltip`이나 `Modal`을 표시하고 싶습니다. 그렇다면 당신은 해당 컴포넌트들을 어디다가 배치해야 할까요?  
버튼 내부에다가 두게 되면 버튼은 더 이상 원자 레벨이 아니게 되고, 그렇다고 분자로 올리자니 최소의 단위로 쪼개야하는 방법론의 정체성을 부정하게 됩니다.
## 페이지가 너무 무거워요
가장 심각한 문제는 페이지의 무게가 너무 무거워진다는겁니다.  
`Template` 컴포넌트에 `Props`로 데이터를 넘기고, 이벤트를 핸들링해야 하는데 이를 처리할 레벨이 `Page`밖에 남아있지 않습니다.  
그렇다면 `Page`에서 API 요청도 날리고, 데이터 파싱도 진행하고, 이벤트 처리도 해야하니 페이지 코드가 심각하게 복잡해집니다.
## Template을 다시 쓰긴 하나?
`Atomic Design` 방법론에 따르면 `Template` 레벨의 컴포넌트는 페이지의 레이아웃 자체를 재활용하는 상황에 대비하여 만드는 껍데기입니다.  
하지만 웹앱 개발 경험을 다수 쌓으셨다면 느끼시겠지만 일반적인 컴포넌트는 재활용을 자주 할 수 있지만 페이지 레이아웃을 **여러 페이지에서 끌어다가 활용하는 경우는 없다고 봐도 무방할 정도로 드문 일입니다.**  
쓸 확률이 희박한 컴포넌트를 만들겠다고 안그래도 힘든 Prop Drilling에 한 `Depth`를 추가하는 꼴밖에 되지 않아요.

이와같이 완벽해보이기만 했던 이론도 실제로 사용하면서 많은 문제를 느끼게 됩니다.  
그렇지만 분명히 `Atomic Design` 방법론이 추구하고자 하는 모습은 굉장히 매력적이게 다가옵니다. 그렇기에 많은 개발자들은 `Atomic Design`을 프로젝트 형태에 맞게 변형하여 채택합니다.

이 문서에서 소개하고자 하는 `Container Component`도 그 일환중 하나예요.
# Container Component
이 방법론을 설명하기에 앞서, 여러분들은 한가지 사실을 인정하셔야 합니다.  
**"더러운 컴포넌트는 반드시 필요하다"** 라는 것을요.

'더러운 컴포넌트'란 `Redux`나 `MobX`와 같은 외부 라이브러리를 제어하거나 `API` 요청을 이용해 데이터를 직접 불러오는 로직 등이 포함 된, 즉 **비즈니스와 매우 Deep하게 연결되어 있어서 의존성이 굉장히 큰 컴포넌트를 말합니다.**  
우리는 이런 더러운 컴포넌트를 `Container`라고 부르기로 해요.
## Atomic Design과 함께 써보기
그렇다면 이제 `Atomic Design` 방법론과 이를 결합해봅시다.

원자와 분자, 유기체 레벨은 그대로 가져갑니다.  
다만 유기체 다음 레벨에 `Container`라는 계층을 하나 추가할거예요.

`Container` 계층에서는 `Atom` `Molecule` `Organism` 그 어떤 컴포넌트도 들어올 수 있지만, 가급적 역할은 하나로 세분화 해야 합니다.

![이미지](/assets/post_images/2022-09-27-atomic-design-with-container-component/20220928002948.png)
_많은 앱에서 사용되는 AppBar_

위 헤더의 역할은 다음과 같아요.
1. 뒤로가기 버튼을 누르면 뒤로 간다.
2. 키보드 버튼을 누르면 키 설정 Modal이 나온다.
3. 다운로드 버튼을 누르면 파일이 다운로드 된다.

이 `AppBar`는 하나의 `Organism`입니다. 그렇다면 각 버튼에 대한 이벤트 처리가 되어야 하겠죠?  
이를 이제 `AppBarContainer` 라는 `Container`로 포장합니다.

```tsx
{% raw %}
export const AppBarContainer = (props: PropsType) => {
  const navigator = useNavigator();
  const [isVisible, setVisible] = useState<boolean>(false);

  return (
    <>
      <AppBar
        onClickBackButton={navigator.goBack}
        onClickKeyboardButton={() => setVisible(true)}
        onClickDownloadButton={() => download()}
      />
      <Dialog
        dialog={{
          open: isVisible,
          onClose: () => setVisible(false),
        }}
        visibleConfirmButton={true}
        onConfirm={() => setVisible(false)}
      />
    </>
  );
};
{% endraw %}
```
{: file="AppBarContainer.tsx" }

이처럼 `AppBar`에서 수행할 로직을 `Container`에 정의합니다.  
덤으로 아까 언급했던 `Modal`과 같은 컴포넌트도 이 곳에 배치합니다.

`window.navigation`에 접근하여 네비게이션을 핸들링하기도 하고 파일 다운로드 로직을 연결하여 직접 다운로드를 시킬 수도 있습니다.  
**페이지에 과중하게 부담되는 역할을 컴포넌트마다 분산시킬 수 있어요!**

이렇게 `Container`까지 잘 만들었다면 이걸 이제 어떻게 써야할까요?  
## Template
이제 `Template`이 등장합니다!

```tsx
{% raw %}
export const ExampleTemplate = () => {
   <>
      <AppBarContainer />
      <ContentContainer />
      <FooterContainer />
   <>
};
{% endraw %}
```
{: file="ExampleTemplate.tsx" }

위와 같이 `Template`을 `Container`들을 묶어서 간단하게 표현할 수 있어요!

### 이러면 Template도 오염되지 않나요?
더러운 컴포넌트라고 정의한 `Container`가 `Template` 내부에 포함되었으니 `Template`도 의존성이 생겨버렸어요.  
하지만 `Template` 레벨의 컴포넌트는 재사용 될 가능성이 상대적으로 희박하므로 **"의존성이 생겨도 별 문제가 없을 것이다"** 라는 판단 하에 이렇게 사용하도록 용도를 변경했습니다.

`Template` 레벨을 통해 `Container`를 묶어주면 페이지 로직은 아래와 같이 매우 깔끔하게 만들어집니다!
```tsx
{% raw %}
const Page: NextPageWithLayout = (props: PropsType) => {
  return (
    <>
      <Head>
        <title>{title}</title>
        <meta property="og:image" content={image} key={'og:image'} />
        <meta property="viewport" content="width=device-width,height=device-height,initiali-scale=1,minimum-scale=1,maximum-scale=1" key={'viewport'} />
      </Head>
      <PageTemplate />
    </>
  );
};

// ...

export default Page;
{% endraw %}
```
{: file="Page.tsx"}

페이지는 이제 `Middleware`나 라우팅 관련 로직만을 처리할 수 있도록 코드를 깔끔하게 분리할 수 있겠네요!
# 레퍼런스
[Effective Atomic Design][레퍼런스]

[레퍼런스]: https://kciter.so/posts/effective-atomic-design