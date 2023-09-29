---
title: "Next.js + Vercel + Github Workflow를 결합한 가장 아름다운 배포 자동화"
author:
  name: 박 찬영
  link: https://github.com/univdev
date: 2023-09-29 23:21:00 +0900
categories: [Tech, Talk]
tags: [Vercel, Next.js, CICD]
image:
  path: /assets/post_images/2023-09-29-vercel-cicd/2023-09-30-00-57-09.png
  alt: "Next.js + Vercel 가장 아름다운 배포 자동화"
---
![Next.js + Vercel 가장 아름다운 배포 자동화](/assets/post_images/2023-09-29-vercel-cicd/2023-09-30-00-57-09.png)  
안녕하세요 😁 박찬영입니다.  
오늘은 Next.js와 Vercel을 활용한 배포 자동화 방법에 대해서 얘기 하고자 해요.  
이번에 제가 회사에서 진행하고 있었던 프로젝트가 AWS에서 Vercel로 인프라를 이전하게 되었어요.  
그러한 이유로 AWS의 CI/CD를 사용할 수가 없기 때문에 Vercel에 맞는 자동 배포를 구성할 할 필요가 있어 이번 Task를 진행하게 되었습니다.
# CI/CD란?
현업에서 활약하고 계신 분들은 당연하고 개발자를 지망하시는 분들도 CI/CD에 대해서는 한번 쯤 들어보신 적이 있으실거예요.  
CI/CD는 Continuous Integration와 Continuous Delivery의 약자를 결합한 단어입니다.  
간단하게 말해서 *"지속적으로 통합하고 지속적으로 배포해라"*입니다.  
## CI
![CI 프로세스](/assets/post_images/2023-09-29-vercel-cicd/2023-09-29-23-49-03.png)  
CI는 이처럼 개발자가 개발한 프로젝트를 빌드하고 테스트를 진행하는 일련의 프로세스를 말해요.  
여러 개발자들이 하나의 프로젝트에서 동시적인 개발을 진행하게 되면 분명히 높은 확률로 충돌 등이 일어나게 되는데 잘 짜여진 CI는 이러한 상황을 미연에 방지해줘요.
## CD
![CD 프로세스](/assets/post_images/2023-09-29-vercel-cicd/2023-09-29-23-56-05.png)  
CD는 지속적으로 고객에게 프로젝트를 전달하기 위한 프로세스를 말해요.  
여기서 말하는 고객은 최종 사용자가 될 수도 있고, 조직 내부의 QA팀이 될 수도 있어요.  
배포는 하루에도 수 없이 발생할 수 있는 중요한 과정이며, 또한 매우 귀찮고 시간을 많이 잡아먹는 작업이에요.  
이 작업을 자동화 하는 것이 CD의 핵심이라고 할 수 있어요.
## 왜 배포를 자동화 해야 할까요?
![왜 배포 자동화를 해야 할까요?](/assets/post_images/2023-09-29-vercel-cicd/2023-09-30-00-09-51.png)  
개발자가 Staging 혹은 Production 서버에 결과물을 배포하는 과정에는 다음과 같아요.
1. Local Server에서 개발을 열심히 진행합니다.
2. Build 커맨드를 입력하여 실제로 오류 없이 Build가 동작 하는지 시험 해봅니다.
3. Test 커맨드를 입력하여 테스트를 진행합니다.
4. 위 과정에서 문제가 없었다면 웹(혹은 서버) 호스팅 서비스에 Terminal을 사용하여 접속합니다.
5. `git pull` 명령어를 이용하여 원격 서버의 프로젝트 파일을 최신화 합니다.
6. Build 커맨드를 입력하여 실제로 사용할 파일을 만듭니다.
7. Build 된 파일을 실행합니다.
8. 유관자에게 새로운 버전이 배포 되었다는 사실을 알립니다.
9. 만약 배포 이후에 버그가 발견 되었다면 **1번으로 돌아가서 이 과정을 다시 수행해야 합니다.**

이 과정은 글로 나열만 해도 벌써부터 피곤함이 느껴집니다 🤮 실제로는 이보다 더 악랄하겠죠.  
규모가 큰 프로젝트에서의 Build 시간만 따져봐도 10분이 넘는 경우도 있거든요.  
이 과정을 자동화 한다면 개발자는 Git에 새로운 커밋 하나를 올리거나, 배포 버튼을 누르는 간단한 작업 하나만으로 이 모든 작업에서 자유로워질 수 있어요.  
배포 자동화의 중요성을 이제 잘 아시겠죠?
# Vercel
![Vercel](/assets/post_images/2023-09-29-vercel-cicd/2023-09-30-00-28-25.png)  
우선 배포 자동화를 진행하기 전에 이번에 사용한 Vercel에 대해서 짧게 핥아보고 갈 필요가 있어요.  
[Vercel](https://vercel.com/)은 Next.js 혹은 React를 이용하여 만들어진 프로젝트를 배포하는데 최적화 된 솔루션입니다.  
Vercel의 특징이자 가장 큰 장점은 다음과 같습니다.
- Zero Config을 지향하기 때문에 AWS같은 솔루션과는 비교가 안될 정도로 초기 설정이 단순합니다.
- Next.js의 개발사이다 보니 React와 Next.js에 관한 배포와 최적화는 타 플랫폼과 비교가 불가능합니다.
  - 폰트 최적화
  - 이미지 최적화 및 캐싱
  - Edge Function 등

## Vercel의 운영 환경 이해하기
Vercel은 세 가지의 배포 환경이 존재합니다.
### Development
`vercel dev`를 로컬 환경에서 실행했을 때 Development 환경으로 구동 됩니다.
- Vercel이 지원하는 `dev` 명령어는 `Vercel`의 환경을 다운로드 받아 개발모드로 실행하도록 해주는 명령어입니다.
- Vercel에 등록 된 모든 설정값과 개발 모드에서 불러올 수 있는 환경 변수를 적용한 상태로 로컬 환경에서 체험할 수 있습니다.

### Preview
- 일반적인 `staging`의 역할을 하는 환경입니다.
- `master` 혹은 `main` 이라는 이름을 가진 브랜치 외 나머지 브랜치에 히스토리가 추가 되면 자동으로 배포 됩니다.
- `vercel deploy --prebuilt` 명령어로 직접 배포할 수도 있습니다.
- Preview는 배포가 이루어질 때마다 새로운 형태의 도메인이 새로 발급 됩니다.
  ![Preview 히스토리](/assets/post_images/2023-09-29-vercel-cicd/2023-09-30-00-32-48.png)
- 이전 버전의 Preview 도메인이 스냅샷처럼 남아있기에 과거 버전과 현재 버전을 따로 접속해서 열람하는 것도 가능합니다.

### Production
- 실제 사용자에게 전달 될 서비스입니다.
- `master` 혹은 `main` 이라는 이름을 가진 브랜치에 새로운 히스토리가 추가 되면 배포 됩니다.
- `vercel deploy` 명령어로 직접 배포할 수도 있습니다.

# 프로젝트 추가
## Vercel CLI를 이용한 프로젝트 추가

1. npm을 이용하여 Vercel CLI를 설치합니다.
    
    ```bash
    npm i -g vercel
    ```
    
2. Vercel 로그인을 진행합니다.
    
    ```bash
    vercel login
    ```
    
3. Vercel로 호스팅 하고자 하는 프로젝트의 Root에서 아래의 명령어를 입력합니다.
    
    <aside>
    ⚠️ 현재 프로젝트의 구조가 Monorepo라도 package 폴더 내부가 아닌 Monorepo 레포지토리 최상위 폴더에서 사용해야 합니다.
    
    </aside>
    
    ```bash
    vercel link
    ```
    
4. 위 명령어를 사용하면 Vercel 프로젝트와 Git Repository를 연결하는 작업을 시작합니다.
    1. 현재 디렉토리에 Vercel 설정을 진행하는지 묻습니다. Y를 입력해주세요.
        
        ```bash
        # 현재 디렉토리에 Vercel 설정을 진행합니까?
        ? Set up “~/repository-path”? [Y/n]: Y
        ```
        
    2. 해당 프로젝트의 scope를 설정 해주세요.
        
        ```bash
        # 해당 프로젝트의 scope를 설정 해주세요.
        ? Which scope should contain your project?
          [ ] organization1
        	[ ] organization2
        	[ ] organization3
        ```
        
        - Vercel과 연결 된 계정에 포함 된 모든 조직이 나옵니다.
        - 이 프로젝트를 소유할 조직을 선택하시면 됩니다.
    3. Vercel 프로젝트의 생성 혹은 연결 여부를 결정합니다.
        
        ```bash
        # Vercel에 이미 프로젝트가 생성 되어 있고, 해당 프로젝트의 설정값을 그대로
        # 연결하길 원하면 Y를 입력합니다.
        ? Link to existing project? [y/N]:
        ```
        
        - 만약 Vercel에 이미 존재하는 프로젝트와 연결하는 경우 Y를 입력해주세요.
        - 프로젝트 설정을 마저 진행하시려면 N을 입력해주세요.
    4. Vercel 프로젝트의 이름을 입력해주세요.
        
        ```bash
        # 이 프로젝트와 연결 될 Vercel 프로젝트의 이름을 입력합니다. (입력한 이름대로 생성 됩니다.)
        ? What's your project's name?
        ```
        
    5. 배포가 이루어질 프로젝트의 경로를 입력합니다.
        
        ```bash
        # Vercel을 통해 배포를 진행할 패키지의 Root 디렉토리를 입력합니다.
        # 모놀리식 프로젝트라면 기본값인 ./를 사용하면 됩니다.
        # 모노레포 프로젝트라면 packages/web과 같은 배포할 패키지의 상대 경로를 입력해야 합니다.
        ? In which directory is your code located? ./
        ```
        
        - 일반적인 단일레포(모놀리식)의 경우 기본값인 `./`대로 설정하셔도 무방합니다.
        - 만약 모노레포라면 실제 Vercel을 이용하여 배포를 진행할 프로젝트가 저장된 곳의 상대 경로를 입력합니다.
        - 만약 `packages/web`에 있는 패키지만 Vercel을 이용하여 호스팅 하려면 `packages/web`을 입력합니다.
    
    f. 배포 명령어를 설정합니다.
    
    ```bash
    # 위에서 입력한 여러 설정값을 바탕으로 Vercel의 배포 프로세스에서 사용할 명령어를 설정합니다.
    # Build Command는 일반적으로 사용하고 있는 프로젝트 빌드를 위한 커맨드 입니다.
    # Development Command는 개발모드 설정을 위한 커맨드 입니다.
    # Output Directory는 빌드 이후에 배포 될 파일들이 추가 되는 폴더입니다.
    No framework detected. Default Project Settings:
    - Build Command: `npm run vercel-build` or `npm run build`
    - Development Command: None
    - Install Command: `yarn install`, `pnpm install`, `npm install`, or `bun install`
    - Output Directory: `public` if it exists, or `.`
    ? Want to modify these settings? [y/N]
    ```
    
    - Build Command는 프로젝트를 빌드할 때 사용하는 명령어입니다.
    - Development Command는 개발모드 실행을 위해 사용하는 명령어입니다.
    - Install Command는 패키지를 설치할 때 사용하는 명령어입니다.
    - Output Directory는 빌드 이후 번들링 된 파일들이 저장 될 경로입니다.

## Next.js 환경을 위한 배포 명령어 설정
개발 된 프로젝트가 Next.js로 만들어져 있다면 위 설정 과정 중 '배포 명령어 설정 과정'에서 일부 값을 변경해야 합니다.

### Build Command
특별한 일이 없다면 Next.js의 기본 빌드 명령어와 동일하므로 변경하지 않습니다.

### Development Command
사용할 일이 없다면 변경하지 않습니다.

### Install Command
패키지를 설치하기 위한 명령어로서 특별한 일이 없다면 변경하지 않습니다.

### Output Directory
기본값은 `public`이며 만약 `public` 폴더가 없다면 현재 프로젝트를 가리킵니다.

- 만약 배포하고자 하는 프로젝트가 정적 빌드를 이용한 정적 페이지 호스팅이라면 기본값으로 둬도 괜찮습니다.
- 서버사이드 렌더링 프로젝트라면 이 경로를 `.next`로 변경해야 합니다.

# Vercel 프로젝트 설정
CI/CD 설정을 위해 Vercel 관리 도구에서 설정해야 하는 몇가지 옵션이 존재합니다.

## 개설한 프로젝트 진입하기
![Vercel 프로젝트 목록 화면](/assets/post_images/2023-09-29-vercel-cicd/2023-09-30-00-34-47.png)
_Vercel 프로젝트 목록 화면_

[Vercel](https://vercel.com/)에 접속 후 로그인을 진행하면 위와 같이 새로 추가한 프로젝트가 존재합니다.

해당 프로젝트를 눌러 상세 페이지에 진입하면 다음과 같은 화면이 보여집니다.

![Vercel 프로젝트 상세 화면](/assets/post_images/2023-09-29-vercel-cicd/2023-09-30-00-35-05.png)
_Vercel 프로젝트 상세 화면_

## 자동배포 차단하기
### 자동 배포를 차단해야 하는 이유
Vercel은 Git Repository와 연결 된 순간부터 Git의 커밋 내역을 바탕으로 자동 배포를 진행합니다.

자동 배포는 정말 간편하지만 일부 상황에서는 방해 요소로 작용합니다.

- 빌드 완료 후 Slack 혹은 Discord Notification 전송
- 원하는 시점에 배포하도록 설정하고자 하는 경우
- 그 외 디테일한 추가 설정이 필요한 경우

### 방법
1. 프로젝트 상단 [Settings] 탭을 클릭합니다.
2. Settings 페이지 좌측 네비게이션에서 [Git] 메뉴를 클릭합니다.
3. 제일 아래로 내려보면 등장하는 [Ignored Build Step]을 확인합니다.
4. 기본값이 [Automatic]으로 되어 있는데 이를 [Don’t build anything]으로 변경합니다.
5. [Save] 버튼을 누릅니다.
6. 이제 어떤 브랜치에 새로운 히스토리가 푸쉬 되어도 배포가 진행 되지 않습니다. 🎉

## Staging 도메인 개설하기
1. 프로젝트 상단 [Project] 탭을 클릭합니다.
2. 프로젝트 제목 옆에 보이는 [Domains] 버튼을 클릭합니다.
3. Staging 서버로 연결 될 도메인을 입력하고 [Add] 버튼을 클릭합니다.
    ![Domain 추가 화면](/assets/post_images/2023-09-29-vercel-cicd/2023-09-30-00-41-23.png)
    
    <aside>
    ⚠️ 단, 여기서 사용할 수 있는 도메인은 Vercel 프로젝트와 연결 된 도메인이어야 합니다.
    만약 내 소유의 도메인을 등록하고자 한다면 [도메인 인증과정](https://vercel.com/guides/why-is-my-vercel-domain-unverified)을 따르셔야 합니다.
    그게 아니라면 기본 도메인의 원형을 따르는 도메인만 설정이 가능합니다.
    
    원형: example.vercel.app
    가능한 도메인: staging-example.vercel.app
    
    </aside>
    
4. 개설한 도메인의 상세 설정에서 Git Branch를 `master`나 `main`이 아닌 아무 브랜치로 설정합니다.
    ![Domain 설정 화면](/assets/post_images/2023-09-29-vercel-cicd/2023-09-30-00-41-47.png)
    
    <aside>
    ✅ Vercel의 기본 설정은 Git Repository의 히스토리와 브랜치 이름을 가지고 Production 여부를 판단하기 때문입니다.
    
    </aside>
    

## 환경변수 설정하기
프로젝트 내부에서 여러가지 이유로 `.env` 파일 안에 어떠한 값을 정의하여 사용하는 경우가 있습니다.

번들러가 번들링을 할 때 해당 `env` 변수를 읽어야 하므로 Vercel 프로젝트에 환경 변수를 정의하는 작업이 필요합니다.

1. 상단 [Settings] 탭을 클릭합니다.
2. 좌측 [Environment Variables] 메뉴를 클릭합니다.
3. 아래 사진의 영역에서 환경 변수를 설정하실 수 있습니다.
    ![환경 변수 설정 화면](/assets/post_images/2023-09-29-vercel-cicd/2023-09-30-00-42-08.png)  
    - 환경 변수마다 어떤 빌드 상태일 때 불러올지 설정할 수 있습니다.
        - production
        - preview
        - development
    - 환경 변수를 필드에 일일이 입력해도 되지만 기존에 작성 되어 있던 `.env` 파일을 해당 영역에 드래그 드롭하면 자동으로 입력 됩니다.

# Github Workflow를 이용한 CI/CD

Vercel은 Git Repository 연결만으로 간편한 자동 배포를 지원하지만 Github Action을 이용하여 CI/CD를 작성하면 다음과 같은 이점이 있습니다.

- Slack, Discord와 같은 메신저에 Notification을 연결할 수 있습니다.
- 특정 버전을 선택하여 배포를 진행하는 등 커밋 히스토리에 기반한 배포 이외의 방법을 사용할 수 있습니다.
- 그 외 복잡한 사전/사후 프로세스를 진행할 수 있습니다. (유닛 테스트, e2e 테스트 등)

## Vercel 환경 변수 알아내기

아래 배포 프로세스를 진행하기 위해서는 Vercel에서 얻을 수 있는 네 가지의 환경 변수가 필요합니다.

### Organization ID

1. [개발 프로젝트와 Vercel 프로젝트를 서로 연결하는 과정을](#vercel-cli를-이용한-프로젝트-추가) 진행했다면 해당 프로젝트 폴더의 root에 `.vercel` 폴더가 생깁니다.
2. `.vercel/project.json` 파일을 열어보면 `orgId` 프로퍼티가 존재하는데, 이 값이 Organization ID입니다.

### Project ID

1. [개발 프로젝트와 Vercel 프로젝트를 서로 연결하는 과정을](#vercel-cli를-이용한-프로젝트-추가) 진행했다면 해당 프로젝트 폴더의 root에 `.vercel` 폴더가 생깁니다.
2. `.vercel/project.json` 파일을 열어보면 `projectId` 프로퍼티가 존재하는데, 이 값이 Project ID입니다.

### Token
1. [Vercel](https://vercel.com) 페이지로 접속합니다.
2. 로그인 합니다.
3. 우측 상단 프로필 이미지를 클릭합니다.
4. [Settings]를 클릭합니다.
5. 좌측 메뉴에서 [Tokens]를 클릭합니다.
6. [Token Name] [Scope] [Expiration]을 입력하고 [Create] 버튼을 누릅니다.
    1. Scope는 반드시 배포를 진행할 프로젝트가 소속 된 조직으로 설정합니다.
7. 발급 된 Token을 사용합니다.
    1. 한번 발급 된 Token은 **어떠한 방법으로도 재열람이 불가능하니 반드시 다른 곳에 안전하게 보관해야 합니다.**

### Team ID
1. [Vercel](https://vercel.com) 페이지로 접속합니다.
2. 로그인 합니다.
3. 좌측 상단 조직명을 클릭합니다.
4. 상단 [Settings]를 클릭합니다.
5. [General] 탭의 중간 쯤에 있는 Team ID를 복사합니다.

## Github Secret Key 등록하기
CI/CD를 진행하기 위해서는 위에서 얻은 여러 Key 값을 활용해야 합니다.

하지만 Key 값은 보안상 매우 엄중히 관리 되어야 하기 때문에 Repository에 그대로 노출 되는 **Github Action에 평문으로 작성하면 보안상 심각한 이슈를 낳을 수 있습니다.**

그렇기에 Github는 보안상 민감한 Key를 별도의 저장소에 보관하고 관리할 수 있도록 Secret Variables 저장소를 지원합니다.
1. Vercel에 배포할 프로젝트의 Github Repository에 진입합니다.
2. 상단 [Settings] 탭을 클릭합니다.
3. 좌측 메뉴 [Security] 항목의 [Secrets and variables] 탭을 클릭합니다.
4. 하위의 [Actions] 탭을 클릭합니다.
5. [New repository secret] 버튼을 클릭합니다.
  ![Secret 추가 화면](/assets/post_images/2023-09-29-vercel-cicd/2023-09-30-00-55-01.png)
6. 위에서 얻은 네 가지 Key를 다음의 이름으로 저장합니다.
    1. Organization ID → VERCEL_ORG_ID
    2. Project ID → VERCEL_PROJECT_ID
    3. Team ID → VERCEL_TEAM_ID
    4. Token → VERCEL_TOKEN
7. 저장 했다면 다음과 같은 Secret Variables 목록을 보실 수 있습니다.
  ![추가 된 Secret 목록](/assets/post_images/2023-09-29-vercel-cicd/2023-09-30-00-55-25.png)

## .github 디렉토리 만들기
1. 프로젝트 Root 폴더에 `.github` 폴더를 만듭니다.
2. `.github` 폴더 안에 `workflows` 폴더를 만듭니다.

## Preview 배포 자동화 스크립트 작성하기
1. 위에서 생성한 `.github/workflows` 폴더 안에 `vercel-preview-deploy.yaml` 파일을 생성합니다.
2. 아래와 같이 스크립트를 작성합니다.
    
    ```yaml
    name: Vercel Preview Deployment
    env:
      VERCEL_ORG_ID: ${{ secrets.VERCEL_ORG_ID }}
      VERCEL_PROJECT_ID: ${{ secrets.VERCEL_PROJECT_ID }}
    
    on:
      push:
        branches:
          - "develop"
    
    jobs:
      Deploy-Preview:
        runs-on: ubuntu-latest
        steps:
          - uses: actions/checkout@v2
          - name: Install Vercel CLI
            run: npm install --global vercel@latest
          - name: Pull Vercel Environment Information
            run: vercel pull --yes --environment=preview --token=${{ secrets.VERCEL_TOKEN }}
          - name: Build Project Artifacts
            run: vercel build --token=${{ secrets.VERCEL_TOKEN }}
          - name: Assign beta domain
            run: |
              vercel deploy --prebuilt --token=${{ secrets.VERCEL_TOKEN }} > domain.txt
              vercel alias set `cat domain.txt` staging-logomakershop-biz.vercel.app --scope ${{ secrets.VERCEL_TEAM_ID }} --token ${{ secrets.VERCEL_TOKEN }}
    ```
3. 원격 Repository에 푸쉬합니다.

## Preview 배포 스크립트 이해하기
- VERCEL 배포에 필요한 조직 ID와 PROJECT_ID를 설정합니다.
    
    ```yaml
    name: Vercel Preview Deployment
    env:
      VERCEL_ORG_ID: ${{ secrets.VERCEL_ORG_ID }}
      VERCEL_PROJECT_ID: ${{ secrets.VERCEL_PROJECT_ID }}
    ```
    
- `develop` 브랜치에 새로운 히스토리가 추가 되면 해당 Workflow가 작동합니다.
    
    ```yaml
    on:
      push:
        branches:
          - "develop"
    ```
    
- Ubuntu 가상머신을 통해 아래 프로세스를 수행합니다.
    
    ```yaml
    jobs:
      Deploy-Preview:
        runs-on: ubuntu-latest
    ```
    
    - 해당 프로세스의 이름을 `Deploy-Preview`라고 명명합니다.
- 배포를 위한 Vercel 패키지를 설치합니다.
    
    ```yaml
    - name: Install Vercel CLI
      run: npm install --global vercel@latest
    ```
    
- Vercel 설정값을 다운로드 합니다.
    ```yaml
      - name: Pull Vercel Environment Information
        run: vercel pull --yes --environment=preview --token=${{ secrets.VERCEL_TOKEN }}
    ```
    - 배포를 위한 인스턴스는 `.vercel` 파일이 없으므로 Vercel 설정값을 다운 받아야 합니다.
    - `--yes` 플래그는 해당 명령어에 대한 Confirm 여부를 모두 Yes로 설정합니다.
- 프로젝트를 빌드합니다.
    
    ```yaml
    - name: Build Project Artifacts
      run: vercel build --token=${{ secrets.VERCEL_TOKEN }}
    ```
    
- 새로운 Preview 버전을 생성하고 미리 생성해둔 staging 도메인을 별명으로 정합니다.
    ```yaml
      - name: Assign beta domain
        run: |
          vercel deploy --prebuilt --token=${{ secrets.VERCEL_TOKEN }} > domain.txt
          vercel alias set `cat domain.txt` staging-logomakershop-biz.vercel.app --scope ${{ secrets.VERCEL_TEAM_ID }} --token ${{ secrets.VERCEL_TOKEN }}
    ```
    - `vercel deploy --prebuilt` 명령어를 이용하여 Preview 배포를 실행합니다.
    - `vercel deploy --prebuilt` 명령어는 새롭게 발급 된 도메인을 Output으로 반환합니다.
    - 새로 발급 된 도메인을 `domain.txt` 파일에 저장합니다.
    - `vercel alias set` 명령어로 `domain.txt` 파일에 저장 된 도메인을 Staging 도메인에도 연결합니다.

### ❓ 왜 alias가 필요한가요?
[위에서 언급 했듯](#preview)이 Preview는 매번 새로운 도메인을 생성하여 배포합니다.

매번 새로운 도메인을 생성하게 되면 고정 된 URL이 필요한 여러 상황에서 방해가 됩니다.

- Preview 확인이 필요한 내부 관계자에게 최신 Preview 주소를 매번 고지 해줘야 합니다.
- Cognito와 같이 Redirect URI를 입력 해야 이용할 수 있는 서드파티 솔루션을 사용할 수 없습니다.

위와 같은 이유로 언제나 최신 Preview를 유지하는 고정 된 도메인이 필요합니다.

## Production 배포 자동화 스크립트 작성하기
1. `.github/workflows` 폴더에 `vercel-product-deploy.yaml` 파일을 생성합니다.
2. 해당 파일에 다음과 같이 스크립트를 작성합니다.
    
    ```yaml
    name: Vercel Production Deployment
    env:
      VERCEL_ORG_ID: ${{ secrets.VERCEL_ORG_ID }}
      VERCEL_PROJECT_ID: ${{ secrets.VERCEL_PROJECT_ID }}
    
    on:
      push:
        branches:
          - "master" # or main
    
    jobs:
      Deploy-Production:
        runs-on: ubuntu-latest
        steps:
          - uses: actions/checkout@v2
          - name: Install Vercel CLI
            run: npm install --global vercel@latest
          - name: Pull Vercel Environment Information
            run: vercel pull --yes --environment=production --token=${{ secrets.VERCEL_TOKEN }}
          - name: Build Project Artifacts
            run: vercel build --prod --token=${{ secrets.VERCEL_TOKEN }}
          - name: Deploy Project Artifacts to Vercel
            run: vercel deploy --prebuilt --prod --token=${{ secrets.VERCEL_TOKEN }}
    ```
    

## Production 배포 스크립트 이해하기
- Vercel 프로젝트에 대한 설정을 진행합니다.
    
    ```yaml
    name: Vercel Production Deployment
    env:
      VERCEL_ORG_ID: ${{ secrets.VERCEL_ORG_ID }}
      VERCEL_PROJECT_ID: ${{ secrets.VERCEL_PROJECT_ID }}
    ```
- `master` 브랜치에 새로운 히스토리가 푸쉬 되면 해당 Workflow가 동작합니다.
    
    ```yaml
    on:
      push:
        branches:
          - "master" # or main
    ```
- Ubuntu 가상머신을 통해 아래 프로세스를 실행합니다.
    ```yaml
    jobs:
      Deploy-Production:
        runs-on: ubuntu-latest
    ```
- Vercel 배포를 위한 CLI 패키지를 npm에서 설치합니다.
    ```yaml
      - name: Install Vercel CLI
        run: npm install --global vercel@latest
    ```
- Vercel 설정을 다운로드 합니다.
    ```yaml
    
    - name: Pull Vercel Environment Information
      run: vercel pull --yes --environment=preview --token=${{ secrets.VERCEL_TOKEN }}
    ```
- 빌드 합니다.
    ```yaml
    - name: Build Project Artifacts
            run: vercel build --prod --token=${{ secrets.VERCEL_TOKEN }}
    ```
    - `--prod` 플래그를 활용하면 Production 모드로 빌드합니다.
- 배포를 진행합니다.

# 마치며
이번 문서에서는 이렇게 Next.js와 Vercel 그리고 Github Workflow를 활용한 배포 자동화를 진행해봤습니다 🎉  
제가 처음 연동을 진행했을 때 겪었던 모든 트러블을 상세히 작성하느라 다소 분량 조절에 실패한 듯한 느낌이 들지만 이 문서 하나로 다른 많은 개발자분들이 시간을 아낄 수 있다고 생각하면 이보다 보람찬 일도 또 없을 것 같습니다.  
다음번에는 Slack Bot을 이용하여 유관자들에게 배포 상태에 대한 알림을 자동으로 보내주는 방법도 기재할 예정이오니 많은 관심 부탁드립니다 🙇