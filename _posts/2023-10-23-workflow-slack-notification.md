---
title: 가장 아름다운 배포 자동화, 근데 이제 Slack을 곁들인
author:
  name: 박 찬영
  link: https://github.com/univdev
categories: [Tech, Talk]
date: 2023-10-23 01:50:00 +0900
tags: [Github]
image:
  path: /assets/post_images/2023-10-23-workflow-slack-notification/2023-10-23-01-00-28.png
  alt: "가장 아름다운 배포 자동화, 근데 이제 Slack을 곁들인"
---
![가장 아름다운 배포 자동화, 근데 이제 Slack을 곁들인](/assets/post_images/2023-10-23-workflow-slack-notification/2023-10-23-01-00-28.png)
> 만약 배포 자동화 방법에 대해서 알고 싶으시다면 제가 [이전에 작성한 글](/posts/vercel-cicd)을 먼저 읽고 오시는 것을 추천 드립니다.
{: .prompt-tip }
# 들어가며
안녕하세요 😁 박찬영입니다.  
지난 글에서는 Vercel을 활용한 자동 배포 환경을 만드는 방법에 대해서 말씀 드렸던 바 있어요.  
이번 문서에서는 지난 번에 분량 조절 문제로 인해 담지 못했던 자동 알림 기능을 추가하는 방법에 대해서 말씀 드리고자 해요.
# 자동 알림은 왜 필요할까요?
프로젝트의 배포는 대부분 다음과 같은 요청에 의해 이루어져요.
- 이번 버전에 추가 된 내용을 최종 사용자에게 릴리즈 해주세요~
- 이번 QA에서 발견 된 이슈 수정 하셨으면 배포 해주세요~
- 이번 버전 개발 다 되셨으면 QA 해야 하는데 배포 부탁드립니다.

그렇다면 개발자는 배포를 진행하고나서 유관자에게 다음과 같은 내용을 포함하여 보고 해야 합니다.
- 이전 버전과의 차이점
- 배포를 진행한 버전

![매우 귀찮은 배포 보고](/assets/post_images/2023-10-23-workflow-slack-notification/2023-10-23-00-59-49.png)
_매우 귀찮은 배포 보고_
보통 이렇게 수동으로 작성한 보고는 보고를 올릴 때의 기분에 따라 텍스트가 달라지기에 검색도 용이하지 않고 배포가 정상적으로 수행 되었는지 매번 확인 하고 전파해야 하기 때문에 수고로움이 매우 큰 작업입니다.  
_(도대체 '3분 뒤'라는 시간은 어떻게 산정한 거야...?_ 😧_)_

이 끔찍한 상황에서 Slack API를 활용하면 다음과 같은 예쁜 메시지를 자동으로 유관자에게 전파 할 수 있습니다.
![자동화에 성공한 Slack Notification](/assets/post_images/2023-10-23-workflow-slack-notification/2023-10-23-01-13-52.png)
_자동화에 성공한 Slack Notification_
# Slack Notification 만들어보기
## Slack 봇 만들기
어떠한 상황에 특정 채널에 메시지를 보내기 위해서는 메시지를 보낼 수 있는 주체가 필요합니다.

Slack에서는 이 역할을 Bot이라는 존재가 수행합니다.

Bot 생성은 무료이며 이 Bot으로 다양한 Task를 수행할 수 있습니다.

1. https://api.slack.com/apps 페이지로 접속합니다.
2. 로그인 합니다. (메시지를 보내고 싶은 채널에 메시지를 보낼 수 있는 권한이 있는 계정으로 로그인 하셔야 합니다.)
3. 로그인을 했다면 보유한 Bot의 목록이 표시 됩니다.
  ![Your Apps 화면](/assets/post_images/2023-10-23-workflow-slack-notification/2023-10-23-01-15-06.png)
4. [Create New App]을 누릅니다.
  ![Slack App 생성 화면](/assets/post_images/2023-10-23-workflow-slack-notification/2023-10-23-01-15-45.png)
5. [App Name] 필드에는 Bot의 이름을 작성합니다.
   - 메시지를 보낼 때 일반 채팅처럼 Speaker의 이름이 보이게 되는데 App Name이 그 역할을 수행합니다.
6. [Pick a workspace to develop your app in] 필드는 Bot이 소속 될 Organization을 선택하여 입력합니다.
7. [Create App] 버튼을 누르면 App이 생성 되고 상세한 설정을 진행할 수 있는 페이지로 이동합니다.
  ![Slack App 세부 설정 화면](/assets/post_images/2023-10-23-workflow-slack-notification/2023-10-23-01-16-48.png)
8. 스크롤을 조금만 더 내리면 Bot의 프로필을 설정할 수 있습니다.
  ![Slack App 프로필 설정 화면](/assets/post_images/2023-10-23-workflow-slack-notification/2023-10-23-01-17-27.png)
 - Bot의 이름
 - Bot의 프로필 사진
 - Bot의 소개말 등 
  다양한 내용들을 기입할 수 있습니다.
    
## Slack 메시지 템플릿 제작하기
Slack은 일반적인 텍스트부터 버튼과 같은 액션이 포함 된 특수한 대화까지 모두 입력할 수 있습니다.  
이러한 기능은 당연히 Bot도 이용할 수 있습니다.  
다만 Slack이 자체적으로 개발한 Format을 사용하기 때문에 Documentation을 읽어보면서 하나 하나 제작하기에는 다소 시간이 많이 소모 됩니다.

그래서 Slack은 자신들의 Format을 보다 쉽게 작성할 수 있도록 [Slack Block Kit](https://app.slack.com/block-kit-builder)을 지원합니다.  
![Slack Block Kit](/assets/post_images/2023-10-23-workflow-slack-notification/2023-10-23-01-18-33.png)
_Slack Block Kit_

좌측 메뉴에서 내가 추가하고 싶은 양식을 클릭하면 Slack JSON Format을 자동으로 완성 시켜주기에 내가 원하는 Format을 쉽게 제작할 수 있습니다.  
![Slack App 테스트 메시지 화면](/assets/post_images/2023-10-23-workflow-slack-notification/2023-10-23-01-19-05.png)

일반적인 배포 Notification에 사용되는 위와 같은 형태는 다음과 같이 표현합니다.

{% raw %}
```json
{
  "blocks": [
	  {
		  "type": "header",
		  "text": {
			  "type": "plain_text",
			  "text": "배포를 진행합니다.",
			  "emoji": true
			}
	  },
	  {
		  "type": "divider"
	  },
	  {
		  "type": "section",
		  "fields": [
			  {
				  "type": "mrkdwn",
				  "text": "*버전*\n`0.0.1`"
			  },
			  {
			  	"type": "mrkdwn",
			  	"text": "*배포 담당자*\nMe"
			  }
		  ]
	  }
  ]
}
```
{% endraw %}

## Incoming Webhook 발급하기
Slack은 각각의 채널마다 메시지를 송신할 수 있는 Incoming Webhook을 지원합니다.

1. 좌측 메뉴에서 [Incoming Webhooks] 메뉴를 선택합니다.
2. Incoming Webhooks 메뉴를 활성화 합니다.
  ![Incoming Webhooks 화면](/assets/post_images/2023-10-23-workflow-slack-notification/2023-10-23-01-21-53.png)  
3. [Add New Webhook to Workspace] 버튼을 클릭해서 특정 채널의 Webhook을 발급 받습니다.
  ![Webhook 생성 화면](/assets/post_images/2023-10-23-workflow-slack-notification/2023-10-23-01-22-19.png)  
  터미널을 열어서 [Sample curl request to post to a channel] 에 적힌 curl을 입력하면 해당 채널에 “Hello, World!” 라는 메시지가 입력 됩니다.
4. 생성 된 Webhook URL을 CI/CD Slack Notification 연동에 활용할 수 있습니다.

## Webhook URL을 Repository에 등록하기
Webhook URL은 별다른 인증 과정 없이 특정 채널에 메시지를 보낼 수 있는 중요한 URL입니다.  
이는 다시 말해 유출 될 경우 Bot을 이용해 **허가 되지 않은 사용자가 특정 채널에 메시지를 무단으로 보낼 수 있다는 뜻입니다.**

Github CI/CD 스크립트에 평문으로 Webhook URL을 입력한다면 심각한 보안상 이슈를 낳을 수 있기 때문에 반드시 Github에서 지원하는 Secret Variables를 사용해야 합니다.

1. CI/CD 스크립트를 적용할 Repository에 접속합니다.
2. 상단 [Settings] 탭을 클릭합니다.
3. 좌측 메뉴에서 [Secret and Variables] 탭을 클릭하면 나오는 [Actions] 메뉴를 클릭합니다.
4. [New repository secret] 버튼을 클릭합니다.
5. [Name]은 `SLACK_WEBHOOK_URL`로 설정합니다.
6. [Add Secret] 버튼을 눌러서 Secret Variable을 등록합니다.

## Github Action을 이용하여 Slack Notification 만들기

1. 프로젝트 폴더에 진입합니다.
2. 최상단 경로에 `.github/workflows` 폴더를 생성합니다.
3. `send-slack-notification.yaml` 파일을 생성합니다.
    1. 해당 파일명은 편의상 지정한 이름이며, 상황마다 다르게 지정할 수 있습니다.
4. 아래와 같은 스크립트를 입력합니다.
   {% raw %}
    ```yaml
    name: Send slack message
    
    on:
      push:
        branches:
          - "develop"
    
    env:
      GITHUB_REPOSITORY: ${{ github.server_url }}/${{ github.repository }}
      ACTION_URI: ${{ github.server_url }}/${{ github.repository }}/actions/runs/${{ github.run_id }}
    
    jobs:
      deploy:
        runs-on: ubuntu-latest
        steps:
          - uses: actions/checkout@v2
          # 배포를 시작 하기 전에 Slack에 메시지를 보냅니다.
          - name: Send ready deploy message
            uses: slackapi/slack-github-action@v1.24.0
            if: always()
            env:
              SLACK_WEBHOOK_URL: ${{ secrets.SLACK_WEBHOOK_URL }}
              SLACK_WEBHOOK_TYPE: INCOMING_WEBHOOK
            with:
              payload: |
                {
                  "text": "배포가 시작 됩니다!",
                  "blocks": [
                    {
                      "type": "header",
                      "text": {
                        "type": "plain_text",
                        "text": "서버에 배포를 진행하고 있습니다! :runner:",
                        "emoji": true
                      }
                    },
                    {
                      "type": "section",
                      "fields": [
                        {
                          "type": "mrkdwn",
                          "text": "*작업자*\n${{ github.actor }}"
                        },
                        {
                          "type": "mrkdwn",
                          "text": "*현재 버전*\n`${{ github.ref_type }}/${{ github.ref_name }}`"
                        }
                      ]
                    },
                    {
                      "type": "divider"
                    },
                    {
                      "type": "actions",
                      "elements": [
                        {
                          "type": "button",
                          "text": {
                            "type": "plain_text",
                            "emoji": true,
                            "text": "Github 바로 가기"
                          },
                          "url": "${{ env.GITHUB_REPOSITORY }}"
                        },
                        {
                          "type": "button",
                          "text": {
                            "type": "plain_text",
                            "emoji": true,
                            "text": "패치노트 바로 가기"
                          },
                          "url": "${{ env.GITHUB_REPOSITORY }}/releases/tag/${{ github.ref_name }}"
                        },
                        {
                          "type": "button",
                          "text": {
                            "type": "plain_text",
                            "emoji": true,
                            "text": "웹사이트 이동하기"
                          },
                          "style": "primary",
                          "url": "${{ inputs.WEBSITE_URI }}"
                        }
                      ]
                    }
                  ]
                }
    
          # Build 스크립트를 이 곳에 작성해주세요.
    
          # 배포 완료에 대한 Slack 메시지를 전송합니다.
          - name: Send complete deploy message
            uses: slackapi/slack-github-action@v1.24.0
            if: success()
            env:
              SLACK_WEBHOOK_URL: ${{ secrets.SLACK_WEBHOOK_URL }}
              SLACK_WEBHOOK_TYPE: INCOMING_WEBHOOK
            with:
              payload: |
                {
                  "text": "배포가 완료 되었습니다!",
                  "blocks": [
                    {
                      "type": "header",
                      "text": {
                        "type": "plain_text",
                        "text": "서버에 배포가 완료 되었습니다! :party_congrats:",
                        "emoji": true
                      }
                    },
                    {
                      "type": "section",
                      "fields": [
                        {
                          "type": "mrkdwn",
                          "text": "*작업자*\n${{ github.actor }}"
                        },
                        {
                          "type": "mrkdwn",
                          "text": "*현재 버전*\n`${{ github.ref_type }}/${{ github.ref_name }}`"
                        }
                      ]
                    },
                    {
                      "type": "divider"
                    },
                    {
                      "type": "actions",
                      "elements": [
                        {
                          "type": "button",
                          "text": {
                            "type": "plain_text",
                            "emoji": true,
                            "text": "Github 바로 가기"
                          },
                          "url": "${{ env.GITHUB_REPOSITORY }}"
                        },
                        {
                          "type": "button",
                          "text": {
                            "type": "plain_text",
                            "emoji": true,
                            "text": "패치노트 바로 가기"
                          },
                          "url": "${{ env.GITHUB_REPOSITORY }}/releases/tag/${{ github.ref_name }}"
                        },
                        {
                          "type": "button",
                          "text": {
                            "type": "plain_text",
                            "emoji": true,
                            "text": "웹사이트 이동하기"
                          },
                          "style": "primary",
                          "url": "${{ inputs.WEBSITE_URI }}"
                        }
                      ]
                    }
                  ]
                }
    
          # 배포 실패시 메시지를 전송합니다.
          - name: Failed deploy
            uses: slackapi/slack-github-action@v1.24.0
            if: failure()
            with:
              payload: |
                {
                  "blocks": [
                    {
                      "type": "header",
                      "text": {
                        "type": "plain_text",
                        "text": "배포가 실패했어요 :scream:",
                        "emoji": true
                      }
                    },
                    {
                      "type": "section",
                      "fields": [
                        {
                          "type": "mrkdwn",
                          "text": "*작업자*\n${{ github.actor }}"
                        },
                        {
                          "type": "mrkdwn",
                          "text": "*현재 버전*\n`${{ github.ref_type }}/${{ github.ref_name }}`"
                        }
                      ]
                    },
                    {
                      "type": "divider"
                    },
                    {
                      "type": "actions",
                      "elements": [
                        {
                          "type": "button",
                          "text": {
                            "type": "plain_text",
                            "emoji": true,
                            "text": "Github 바로 가기"
                          },
                          "url": "${{ env.GITHUB_REPOSITORY }}"
                        },
                        {
                          "type": "button",
                          "text": {
                            "type": "plain_text",
                            "emoji": true,
                            "text": "Log 확인하기"
                          },
                          "style": "danger",
                          "url": "${{ env.ACTION_URI }}"
                        }
                      ]
                    }
                  ]
                }
    ```
    {% endraw %}
    위 스크립트대로 작성하면 다음과 같은 레이아웃의 메시지가 작성 됩니다 🎉
    ![자동화 된 slack Notification](/assets/post_images/2023-10-23-workflow-slack-notification/2023-10-23-01-24-13.png)
    _자동화 된 Slack Notification_

# 스크립트 분석해보기
## Action Checkout
Slack Notification을 Github Workflow에서 사용할 수 있도록 해주는 Checkout Action에는 여러가지가 있지만 그 중에서 Slack API 팀이 만든 `slackapi/slack-github-action@v1.24.0` 를 사용하였습니다.  
[slack-github-action Github 바로 가기 🔗](https://github.com/slackapi/slack-github-action)

## if문
Github Action에서 지원하는 if문은 해당 Step의 진행 여부를 결정합니다.  
조건문이 false를 반환한다면 해당 Step이 Skip됩니다.

{% raw %}
```yaml
if: success()
if: failure()
if: always()
if: ${{ env.EXAMPLE == 'This is false' }} # false
```
{% endraw %}
Github에서는 `success` `failure` `always`와 같은 함수를 제공하는데, 이는 이전 step의 결과에 따라 각기 다른 결과값을 반환하여 특정 step의 에러 여부를 통해 다음 step의 진행 여부를 결정할 수 있습니다.

- `success` : 이전 Step의 결과가 성공했을 때
- `failure` : 이전 Step의 결과가 실패했을 때
- `always` : 이전 Step의 결과에 상관 없이 항상

## env 정의
{% raw %}
```yaml
env:
  SLACK_WEBHOOK_URL: ${{ secrets.SLACK_WEBHOOK_URL }}
  SLACK_WEBHOOK_TYPE: INCOMING_WEBHOOK
```
{% endraw %}
`slackapi/slack-github-action@v.1.24.0` 패키지에서 사용할 `env` 데이터를 정의합니다.

## Context
`github.actor` `github.ref_type` 과 같은 Context를 통해 보다 다양한 형태의 메시지를 전송할 수 있습니다.  
자세한 내용은 [여기](https://docs.github.com/ko/actions/learn-github-actions/contexts)를 참조 해주세요 🙌

# 마치며
문서로 뽑으니 두 편밖에 나오지 않았지만 분량 자체로 보자면 매우 긴 여정을 드디어 끝내게 되었군요 😅  
배포 자동화 업무를 진행하면서 느낀 점이 있다면 여태까지의 저는 프론트엔드 개발에만 집중했지 내가 개발한 서비스가 사용자에게 어떻게 Delivery 되는 지, 전체적인 과정에 대해서는 무심했었던 것 아닌가 라는 생각이 들었어요.  
여러 사람과 기술적인 얘기를 나누다보면 여러 솔루션을 사용해보고 무엇이 좋네 저게 좋네 하는 식으로 티키타카가 이루어지는데 저는 그런 대화에 끼기에는 소양이 부족했거든요.  

이번 배포 자동화 문제를 제가 해결하겠다고 자발적으로 나선 것도 직접적인 서비스 개발에서 잠시 벗어나 서비스의 배포 프로세스를 배우고 익히고 싶다는 목적이 있었거든요.  
실제로 같이 개발하는 팀원 모두가 유용하게 사용하는 모습을 보니 좋은 서비스를 개발 했을 때와는 다른 즐거움도 있더라구요. 🙂  
이런 즐거움을 조금만 더 일찍 알았었다면 좋았을텐데 하는 아쉬움도 있습니다만 이제야 제가 무엇을 모르는지 감을 잡은 것 같아서 매우 즐겁게 진행했던 Task였습니다.