---
title: "Sentry를 이용한 빛보다 빠른 버그 수정"
author:
  name: "박 찬영"
  link: "https://github.com/univdev"
date: 2024-05-31 11:26:00 +0900
categories: [Tech, Next.js]
tags: [Nextjs, Sentry, Bug, 트러블슈팅, 버그]
image:
  path: /assets/post_images/2024-05-31-install-sentry-on-nextjs/2024-05-31-11-40-37.png
  alt: "Sentry를 이용한 빛보다 빠른 버그 수정"
---
![Sentry를 이용한 빛보다 빠른 버그 수정](/assets/post_images/2024-05-31-install-sentry-on-nextjs/2024-05-31-11-40-37.png)

# 들어가며
서비스를 운영함에 있어서 가장 두려운 순간은 고객이 오류를 마주하는 상황일 것입니다.  
물론 개발할 때 여러가지 시나리오를 고려하여 사용자가 최대한 오류를 마주하지 않도록 구현해야 하겠지만 사람이 개발한 물건에 완벽이란 존재하기 어려운 개념입니다.  
굉장히 촘촘하게 여러 시나리오를 예상하고 커버해도 언제나 빈틈은 존재하기 때문입니다.

그렇기에 서비스 운영자(혹은 개발자)는 사용자에게 발생한 오류를 **최대한 빨리** 수정하는 것이 중요합니다.  
하지만 사용자는 서비스를 이용하다가 오류가 발견되어 더 이상 서비스 이용을 진행할 수 없다 판단될 때, 대부분의 경우 운영측에 이를 알리기보단 서비스를 이탈하는 선택을 하게 됩니다.

이러한 상황 속에서 서비스를 운영하는 측에서는 사용자가 버그를 발견하면 자동적으로 버그 발생 사실을 알기를 원했고, 이로 인해 생겨난 것이 오늘 소개 할 [Sentry](https://sentry.io/)와 같은 '오류 수집 도구'입니다.

# 오류 수집 도구
오류 수집 도구는 말 그대로 서비스에서 발생한 버그를 수집하는 도구입니다.  
대부분의 경우 클라이언트 혹은 API의 이용자가 정상적인 시나리오를 벗어나거나 인프라에 문제가 생기는 등의 상황에서 Exception을 발생시킬 것입니다.  
오류 수집 도구는 이와 같은 상황에서 발생한 Exception을 감지하여 해당 Exception을 발생 시킨 코드 라인, 상황, 시간, 유저가 사용한 브라우저 등을 자세하게 수집하여 버그를 수정하는데 도움을 줍니다.

# Next.js 환경에서 Sentry 연동하기
Sentry는 오늘날 가장 많이 사용하는 오류 수집 도구 중 하나라서 굉장히 많은 플랫폼을 지원합니다.

![Sentry 지원 플랫폼](/assets/post_images/2024-05-31-install-sentry-on-nextjs/2024-05-31-11-47-15.png){: width="600" }

## Sentry 프로젝트 개설하기
이 문서에서는 *Next.js* 환경에서 Sentry를 적용하는 방법에 대해 설명합니다.

![프로젝트 페이지](/assets/post_images/2024-05-31-sentry-nextjs-issue/2024-06-03-11-00-50.png)
[sentry.io](https://sentry.io/) 페이지에서 오류를 수집할 프로젝트를 추가할 수 있습니다.  

![프로젝트 추가 페이지](/assets/post_images/2024-05-31-sentry-nextjs-issue/2024-06-03-11-02-40.png)
프로젝트 페이지에서 [Create Project] 버튼을 클릭하면 위 사진과 같은 페이지를 보실 수 있습니다.  
이 페이지에서 오류를 수집할 프로젝트의 유형, 오류 수집 빈도, 프로젝트 이름을 입력하기만 하면 간단하게 프로젝트 개설이 완료 됩니다.

## Sentry와 내 프로젝트 연동하기
우선 터미널을 이용하여 Sentry를 적용하고자 하는 Next.js 프로젝트의 최상단에 접근합니다.  
그리고 다음과 같은 명령어를 입력합니다.

```shell
npx @sentry/wizard@latest -i nextjs
```
이 명령어는 Next.js 프로젝트에서 Sentry의 설치 및 초기 설정을 자동으로 진행 해줍니다.  
명령어 입력 후 프로젝트 설정을 위해 몇가지 설정이 필요합니다.

```
◇  Next.js does not seem to be installed. Do you still want to continue?
│  Yes
│
◇  Are you using Sentry SaaS or self-hosted Sentry?
│  Sentry SaaS (sentry.io)
│
◇  Do you already have a Sentry account?
│  Yes (이 과정에서 [sentry.io](https://sentry.io/)에 로그인이 필요합니다.)
│
●  If the browser window didn't open automatically, please open the following link to log into Sentry:
│  
│  https://sentry.io/account/settings/wizard/.../
│
◇  Login complete.
│
◆  Select your Sentry project.
│  ● organization-exxxa1xx1/sentry-project-name
│  ○ organization-ex1xa1xx1/sentry-project-name2
└
```

명령어를 입력하면 `sentry.(client|edge|server).config` 파일과 함께 Sentry에 에러를 기록하기 위한 여러 예시코드를 추가해줍니다.  
예시코드는 프로덕션에서 사용 되지 않을 코드이기에 삭제 해주셔도 무방합니다.
