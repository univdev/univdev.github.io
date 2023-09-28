---
title: "Github Issue로 오픈소스 기여하기"
author:
  name: 박 찬영
  link: https://github.com/univdev
date: 2022-05-18 18:42:00 +0900
categories: [Tech, Talk]
tags: [Github]
---
# 오픈소스
오늘날의 IT업계는 수많은 오픈소스로 운영되고 있습니다.  
누군가는 자신의 커리어를 위해서, 누군가는 그저 취미로, 누군가는 돈을 벌기 위해서 오픈소스를 생성하고 사용합니다.  
결과물로 직접적인 수익을 낼 수 없음에도 불구하고 오픈소스 시장은 나날이 성장하며 많은 개발자들이 참여하고 있습니다.

'오픈소스'라는 이름을 들으면 좀 어려워보이고, 전문적인 기술을 요구할 것 같은 느낌이 듭니다.  
근데 사실 따지고보면 별거 없어요. **공개 되어 누구나 참여 가능한 소스코드**일 뿐이기에, Github나 Gitlab에 레포 하나 만들고 ```printf("Hello world!")``` 하나 띄워놔도 오픈소스기 때문이예요.

하지만 우리는 가급적 많은 사람들의 삶의 질을 높여주는 소스코드에 기여하고 싶어합니다.  
그렇다고해서 ```lodash```나 ```react```같은 킬러 라이브러리를 직접 맨땅에서부터 만들기는 많이 어렵죠. 더군다나 이미 뛰어난 실력자들이 개선하는 라이브러리이기 때문에 더 뭘 추가하기도 애매하거든요.  
그럼 우리는 어떻게 기여할 수 있을까요?
# Issue
**Github**나 **Gitlab**과 같은 버전 관리 플랫폼에는 ```Issue```라는 기능이 있습니다.  
```Pull Request```처럼 직접 소스코드에 영향을 미칠 수 있는 기능은 아니지만, 이 라이브러리를 써보고 느낀 불편한 점, 개선 아이디어, 추가하면 좋을 기능 등을 제안하는 기능이예요.  
**"에이 이게 어떻게 오픈소스 기여야"**라고 생각하실 수도 있지만, ```Issue``` 작성도 충분한 기여에 포함됩니다. 오픈소스에서 가장 중요한건 **사용자의 피드백**이기 때문이예요.
# 사례
## 라이브러리 오동작 사례
저는 바쁜 업무가 끝나고 당장에 할 일이 없으면 **Github**에서 오픈소스를 탐방하고, 더 나은 패턴이나 디렉토리 구조, 좋은 라이브러리를 찾아보는 편인데요.  
최근에는 아직 릴리즈 되지도 않은 Nuxt3에 빠져서 Nuxt3와 궁합이 좋은 라이브러리를 찾고 있었어요.  
그러던 와중, [Varlet][Varlet]이라는 [Vuetify][Vuetify] 기반 모바일 친화적 라이브러리를 알게 되었고, 이를 이용하여 [토이 프로젝트][colors]를 만들었어요.  
우여곡절 끝에 다 만들고 ```yarn generate``` 명령어를 입력하여 해당 서비스를 ```Static```하게 빌드하려 했는데, 문제가 발생했습니다.
```
 ERROR  Rollup error: Could not load raw:../../styles/common.css: ENOENT: no such file or directory, open '../../styles/common.css'                                                          nitro 14:11:48
 ERROR  Could not load raw:../../styles/common.css: ENOENT: no such file or directory, open '../../styles/common.css'
```
갑자기 터미널에 이런 에러가 표시되었습니다.  
처음에는 제가 전역으로 ```import```한 CSS 파일에서 발생하는 이슈인가 싶어서 이를 제거하고 빌드해봐도 마찬가지라서 도무지 원인을 알 수가 없었는데, 혹시나 싶어 ```varlet``` 라이브러리의 ```CSS Import``` 구문을 제거하고 빌드한 결과, 빌드가 정상적으로 동작하는 것을 알게 되었어요.
```typescript
import * as Varlet from '@varlet/ui';
import '@varlet/ui/es/style.js'; // <-- 이거

export default defineNuxtPlugin((nuxtApp) => {
  nuxtApp.vueApp.use(Varlet);
});
```
{: file="plugins/varlet-ui.ts"}

**"아직 ```Nuxt3```가 정식 릴리즈 되지 않았기 때문에 그런건가?"** 싶다가도 [공식 문서][공식 문서]를 보아하니 ```Nuxt3```도 지원하는 것 같더라구요.  
설치 방법이 잘못 됐나 싶어 해당 문서를 다시 꼼꼼히 읽어봐도 제가 이미 설정한 것에서 더 진행할 일이 없었어요.  
## 커뮤니티 탐색
이런 경우에는 보통 [StackOverflow][StackOverflow]에서 찾아보는 편인데, 자료가 나오질 않았습니다.
## 이슈 탐색
그렇다면 최종적으로 쓰는 방법인데, [라이브러리의 컨트리뷰터와 직접 얘기할 수 있는 창구][Github Issue]로 찾아가는거죠.  
보통은 여기에 비슷한 경험을 한 다른 유저가 질문글을 올려놓거든요.  
하지만 검색을 해봐도 **저와 비슷한 경험을 한 유저가 없었어요.**  
## 이슈 작성
없다면 제가 직접 물어봐야겠죠?  
레포지토리의 ```Issue``` 페이지에 접속하면 아래와 같은 화면이 표시됩니다.  
![Issue][Issue 이미지]  
우측 상단 **[New Issue]**를 누르면 이슈를 작성할 수 있어요.

보통 오픈소스는 많은 사람들의 참여를 전제로 만들기 때문에 균일한 포맷을 유지하기 위해 ```Template```을 적용하고 있어요.  
템플릿이 있다면 아래와 같이 목록이 표시 될텐데, 지금 상황에 맞는 템플릿을 골라주세요. **(템플릿이 없으면 이 화면이 안뜰거예요.)**  
![Template][Template]

마지막으로는 ```Template``` 양식에 맞춰서 궁금한 점을 물어보시면 됩니다.  
만약 ```Template```이 없다면 자유 양식으로 최대한 보기 좋게 적어주시는게 매너겠죠?
![Issue Write][Issue Write]

이런 과정을 통해 [내가 해결 못한 이슈에 대한 글][내가 작성한 이슈]을 작성했습니다!
## 결과
![답변][답변]

원했던 해결 방법을 알아냈을 뿐 아니라, **"우리의 문서에 실수가 있었습니다. 곧 수정하겠습니다. 피드백 감사합니다!"** 라는 답변까지 받았습니다.  
추후에 어떻게 될지는 모르겠지만 또 다른 누군가가 겪었을 수도 있는, 혹은 겪을 문제를 공론화해서 해당 오픈소스의 개발 계획에 영향을 미치게 되었죠. **더군다나 Github Star가 2,700개인 거대 프로젝트에 말이죠!**  
어때요? 아직도 단순 ```Issue```제기는 오픈소스 기여가 아니라고 생각하시나요?

어렵게만 생각하지 마시고 한번 도전해보세요!

[Varlet]: https://github.com/varletjs
[Vuetify]: https://vuetifyjs.com/en/
[colors]: https://github.com/funapp-day/colors
[공식 문서]: https://varlet-varletjs.vercel.app/#/en-US/serverSideRendering
[StackOverflow]: https://stackoverflow.com/
[Github Issue]: https://github.com/varletjs/varlet/issues
[Issue 이미지]: https://user-images.githubusercontent.com/26304279/169000517-acebd75c-d3b0-4faf-959f-03f9becfeba1.png
[Template]: https://user-images.githubusercontent.com/26304279/169001884-963ebf08-8dc2-4bf0-967f-24c0041566c3.png
[Issue Write]: https://user-images.githubusercontent.com/26304279/169002472-c3850603-9eac-45c2-a632-82989e958ac0.png
[내가 작성한 이슈]: https://github.com/varletjs/varlet/issues/564
[답변]: https://user-images.githubusercontent.com/26304279/169003112-ee3610d2-bf4b-4f29-8917-ed52210f3c99.png