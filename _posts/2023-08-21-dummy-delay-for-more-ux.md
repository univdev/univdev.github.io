---
title: "사용자 경험을 위한 의도적인 비효율"
author:
  name: "박 찬영"
  link: "https://github.com/univdev"
date: 2023-08-21 20:23:00 +0900
tags: [Tech, Talk]
image:
  path: /assets/img/posts/2023-08-22-15-50-16.png
  alt: "사용자 경험을 위한 의도적인 비효율"
---
![사용자 경험을 위한 의도적인 비효율](/assets/img/posts/2023-08-22-15-50-16.png)

안녕하세요 👋 박찬영입니다.

오늘은 사용자의 경험을 증가 시키기 위해 의도적으로 소프트웨어의 응답을 늦추는 사례에 대해서 소개하고자 해요.

# 과거의 최적화
개발 업계에 몸 담고 있는 사람이 아니어도 한 번 쯤 들어본 적이 있는 에피소드를 소개 하고자 합니다.

![최적화의 끝판왕, 슈퍼마리오 브라더스](/assets/img/posts/2023-08-21-20-24-17.png)

바로 현대 플랫폼 게임의 선구자, 슈퍼마리오인데요.

슈퍼마리오 초기버전이 등장한 시기는 1985년입니다.  
이 때의 하드웨어 사양은 현재에 비할 바가 못될 정도로 엄청난 저사양이었어요.

닌텐도가 그 때 당시 개발한 ‘패미컴’은 하드웨어의 가용 용량이 50kb 정도로, 현재로 따지자면 저화질 사진 하나도 제대로 담지 못할 용량이었다고 해요.

그래서 당시 닌텐도에서 근무하던 슈퍼마리오 개발자는 게임에 들어가는 리소스 용량을 극한으로 절약하고자 여러가지 시도를 하게 돼요.

![보스 스테이지의 쿠파](/assets/img/posts/2023-08-21-20-24-48.png)

만약 당신이 슈퍼마리오 아트팀의 담당자라고 가정했을 때 **‘까만색 손목 보호대를 착용한 보스를 그려주세요’** 라는 오더가 접수된다면 여러분은 어떻게 하실 건가요?  
아마 오더대로 까만색 손목 보호대를 그대로 그리는 경우가 대부분일 겁니다.

하지만 극도로 한정 된 리소스를 절약해야 하는 슈퍼마리오의 아트팀은 한가지 묘수를 생각했는데요, 슈퍼마리오 게임의 보스 스테이지 뒷 배경이 까만색이라는 점에서 착안하여 **손목 보호대를 아예 그리지 않은 겁니다.**

픽셀 데이터가 없는 손목 보호대 영역은 투명하게 렌더링 될 것이고, 보스 스테이지의 배경이 투과되어 까만색으로 보여질 것이기 때문이죠.

이처럼 과거에는 하드웨어의 제약 사항 때문에 사용자 경험을 위한 여러 장치들을 연구 해야만 했고, 그 연구의 산물이 오늘날 까지도 이어져 온 경우가 많아요.

![젤다의 전설 야생의 숨결 로딩 팁](/assets/img/posts/2023-08-21-20-25-19.png)

그 예시로는 ‘로딩 팁’이 있어요.

캐릭터가 사망하거나 다른 지역으로의 이동이 발생하면 해당 스테이지의 정보를 초기화 하는 등의 작업이 수반 됩니다.
이 과정에서는 게임의 진행을 잠시 멈추고 대기해야 하는데 하드웨어 사양이 안좋았던 옛날에는 로딩 시간이 매우 길고 지루했을거예요.

플레이어가 이 지루한 시간을 그나마 재미있게 보내라는 의미에서 개발자들은 로딩 화면을 재미있게 보낼 수 있도록 여러 시도를 했고, 그 산물 중 하나가 위 사진에 나오는 ‘로딩 팁’입니다.

‘내가 이 게임을 진행하는데 필요한 정보를 줄테니까 무의미하게 시간 버리지 말고 이거라도 봐라’ 라는 의미인거죠.

# 오늘날의 최적화

오늘날에는 위 사례처럼 광적(?)으로 최적화에 집착하는 사례는 찾아보기 힘들어요.
오히려 최적화를 너무 안해서 욕을 먹는 경우가 많죠.

![출시 초기 엄청난 발적화로 욕을 먹은 대표적인 게임, 사이버 펑크](/assets/img/posts/2023-08-21-20-25-47.png)

그렇게 만들어도 게임을 출시하는데 문제가 없을 정도로 가정용 하드웨어가 많이 발전했기 때문이예요.

그런데 무엇이든 너무 과하면 안좋은걸까요? 이번에는 너무 빨라서 생긴 사례에 대해 소개 할게요.

![고스트 오브 쓰시마 커버 이미지](/assets/img/posts/2023-08-21-20-26-20.png)

[‘고스트 오브 쓰시마’](https://namu.wiki/w/%EA%B3%A0%EC%8A%A4%ED%8A%B8%20%EC%98%A4%EB%B8%8C%20%EC%93%B0%EC%8B%9C%EB%A7%88)(이하 고오쓰)라는 2020년도에 발매한 게임이 있습니다.
이 게임도 마찬가지로 ‘로딩 팁’ 시스템을 추가했어요.

캐릭터가 사망하면 게임을 진행하는 팁을 알려주는 화면이 표시가 되는거죠.  
하지만 개발자들은 이 시스템을 도입하고 한가지 문제를 겪게 돼요.

하드웨어의 사양이 너무 빨라서 로딩 창에 표시 되는 팁을 읽을 시간조차 주어지지 않았던거죠.

이러한 문제점을 인지한 고오쓰 개발진은 ‘로딩 시간을 의도적으로 늘려봐’ 라는 오더를 개발팀에게 주문하였고 지금의 로딩 팁은 ‘로딩은 진작에 끝난 팁 화면’이 되어 버린 것이죠.

이처럼 사용자들이 익숙한 ‘로딩 팁’이라는 UX를 경험시켜 주기 위해 의도적으로 비효율을 발생 시키는 사례도 존재합니다.

# 직접 겪은 사례

![딜레이가 없는 로딩 메시지](/assets/img/posts/loading_message_undelay.gif)

위 화면은 사용자가 저장 버튼을 누를 때 보여지는 Toast 메시지입니다.  
혹시 눈치 채셨나요? 순간적으로 ‘Doodling with Data.’ 라는 메시지가 로딩 중에 표시되고 있어요.

우리 팀의 백엔드 개발자분들이 열일을 해주신 덕분에 API 응답 속도가 정말 너무 빠르더라구요.

[로딩 중 메시지] → [성공 / 실패 메시지]

와 같이 로딩 메시지가 보여지도록 구현 하였는데, 로딩 중이라는 것을 알리는 메시지가 너무 순식간에 지나가버려서 제 역할을 하지 못한 것이죠.

# 더미 딜레이 적용

저는 사용자 경험을 위해 ‘사용자가 인지할 수 있는’ 로딩 메시지를 보여주길 원했고, 고의적인 딜레이를 추가하여서 이 문제를 해결 했습니다.

고의적인 딜레이를 만드는데 있어서 제가 생각한 조건은 다음과 같습니다.

- 개발자가 직접 딜레이 시간을 기입하여 재사용이 가능해야 할 것
- 실제 로딩 속도가 고의적인 딜레이를 상회한다면 딜레이를 주지 말고 완료 할 것
    - 실제 로딩 속도도 긴데 딜레이까지 추가 되면 사용자 경험을 심각하게 저해할 수 있기 때문

위와 같은 고민을 녹여 DummyDelay Class를 작성했습니다.

```tsx
/**
 * API와 같은 다양한 비동기 호출에서 고의적으로 딜레이를 시켜서 로딩 화면을 보여주기 위한 클래스입니다.
 */
export class DummyDelay {
  /**
   * promise api를 전달받으면 promise api가 완료 되기 까지의 실제 시간과 최소 시간을 비교하여
   * 설정한 최소 시간보다 api의 응답 시간이 길면 가짜 딜레이를 사용하지 않고
   * 만약 그보다 짧으면 남은 시간만큼 가짜 딜레이를 진행합니다.
   * @params promise
   * @params delay
   * @params promise
   */
  public static start<T>(promise: Promise<T>, delay: number) {
    return new Promise<T>((resolve, reject) => {
      const startTime = (new Date()).getTime();
      
      promise
        .then((response) => {
          const endTime = (new Date()).getTime();
          const takenTime = endTime - startTime;

          if (takenTime > delay) {
            resolve(response);
          } else {
            window.setTimeout(() => resolve(response), delay - takenTime);
          }
        })
        .catch((error) => {
          const endTime = (new Date()).getTime();
          const takenTime = endTime - startTime;

          if (takenTime >= delay) {
            reject(error);
          } else {
            window.setTimeout(() => reject(error), delay - takenTime);
          }
        });
    });
  }
};
```

`DummyDelay.start` 메소드는 다음과 같은 절차에 의해 실행 됩니다.

1. 첫 번째 매개변수로 promise callback을 받습니다. (API 호출 등)
2. promise callback을 실행하기 전에 `DummyDelay.start` 함수의 실행 시간을 저장합니다.
3. 완료 혹은 실패 시 시점을 저장하여 함수의 시작 시간과 비교합니다.
4. 종료까지 걸린 시간이 딜레이보다 길 경우 `resolve`를 실행하여 값을 반환합니다.
5. 그렇지 않다면 남은 시간만큼 딜레이를 주어 사용자가 설정한 딜레이 시간을 채워서 값을 반환합니다.

![딜레이가 적용 된 로딩 메시지](/assets/img/posts/loading_message_delayed.gif)

DummyClass를 적용한 예시입니다.

# 마치며

하드웨어의 사양 부족으로 인해 생겨난 여러가지 해결책들이 오늘날에 와서는 ‘없으면 허전한’ 요소로서 취급 되는 상황도 있다는 것을 새삼스럽게 알게 되었습니다.

단순히 속도적인 측면으로서 생각해보면 위 케이스들에서 제가 만들어낸 DummyDelay Class, 고오쓰의 로딩 팁 등은 필요하지 않은 것이 사실입니다.

그러나 이미 이러한 요소들에 익숙해진 사용자들의 시선을 맞추기 위해 속도를 희생 하면서 까지 보여줘야 하는 하나의 컨텐츠가 되어버린 것이죠.

미래에는 속도를 희생 하지도 않고, 사용자들의 경험도 충족 시켜줄 새로운 UX가 나오게 되지 않을까 싶습니다.