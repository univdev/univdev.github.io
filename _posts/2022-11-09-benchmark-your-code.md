---
title: "소스코드의 퍼포먼스를 벤치마킹하는 방법"
author:
  name: 박 찬영
  link: https://github.com/univdev
date: 2022-11-09 16:58:00 +0900
categories: ["Tech", "Talk"]
tags: ["Benchmark", "Javascript"]
---
# 개요
프로젝트를 진행하다보면 하나의 Feature를 해결하기 위한 다양한 방법들을 마주할 때가 많아요.  
예를 들어, 특정 Element를 복제하고 싶을 때 사용할 수 있는 API는 크게 `createElement`와 `cloneNode`가 있습니다.

둘 중 어떤걸 써도 문제의 해결은 가능하지만 가급적 좋은 길을 선택하고자 할 때, 어떤 API가 보다 좋은 성능을 내는지를 측정하고 싶을 수 있습니다.
# MeasureThat
[![MeasureThat](/assets/post_images/2022-11-09-benchmark-your-code/20221109170554.png)][MeasureThat]
_소스코드 Benchmark 사이트_
[MeasureThat][MeasureThat]에서 직접 소스코드를 작성하고 실행하여 두 코드 사이의 Performance를 측정할 수 있습니다.  
## 사용법
![Create Benchmark](/assets/post_images/2022-11-09-benchmark-your-code/20221109170910.png)

시작 페이지에서 **[+ New benchmark]** 버튼을 클릭하시면 위 사진과 같은 페이지가 등장합니다.  
이 페이지에서 코드를 작성하고 테스트 케이스를 추가하는 등의 작업을 진행하실 수 있습니다.

![Code](/assets/post_images/2022-11-09-benchmark-your-code/20221109170821.png)

위와 같이 코드를 입력하고 Benchmark를 실행하면

![Chart](/assets/post_images/2022-11-09-benchmark-your-code/20221109170506.png)
_Benchmark_

위 사진처럼 두 코드간 Performance를 비교할 수 있습니다.  
동일한 `div`를 10만번 생성한다고 가정했을 때, `cloneNode`보다 `createElement`가 상대적으로 적은 비용을 지출한다는 사실을 알게 되었네요!

[MeasureThat]: https://measurethat.net/