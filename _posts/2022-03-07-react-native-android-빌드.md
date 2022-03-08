---
title: "[React Native] Android 빌드"
author:
  name: 박 찬영
  link: https://github.com/univdev
date: 2022-03-07 18:35:00 +0900
categories: [Tech, React Native]
tags: [React Native, Android]
---
# Keystore 생성
```
cd android/app
keytool -genkey -v -keystore custom-key-name.keystore -alias custom-alias-name -keyalg RSA -keysize 2048 -validity 10000
```
React Native 프로젝트 폴더 안에 있는 ```android/app``` 폴더로 이동한 다음, ```keytools``` 명령어를 실행합니다.  
```keytools``` 명령어 부분의 ```custom-key-name```과 ```custom-alias-name```을 취향에 맞게(?) 변경합니다.  
그러면 사용자의 이름이 뭐고, 조직이 뭐고, 조직 이름이 뭐고 등등 여러가지 질문이 나올텐데 맞게 입력하면 마지막에 해당 답변들이 모두 맞는지를 다시 여쭤봅니다. 이 때 y를 입력하고 엔터를 누르면 keystore 파일이 ```android/app``` 폴더에 생성 됩니다.
# Keystore 정보 기재
앱을 빌드할 때 해당 서명 파일에 대한 정보를 넘겨주기 위해 ```android/gradle.properties``` 파일에 Keystore에 대한 정보를 입력합니다.
```text
MYAPP_RELEASE_STORE_FILE=custom-key-name.keystore
MYAPP_RELEASE_KEY_ALIAS=custom-alias-name
MYAPP_RELEASE_STORE_PASSWORD=password
MYAPP_RELEASE_KEY_PASSWORD=password
```
{: file="android/gradle.properties"}

```custom-key-name```과 ```custom-alias-name```은 위에 Keystore 파일을 생성할 때 입력했던 것과 동일하게 설정해주시고, ```Release Store Password```와 ```Release Key Password```는 Keystore 파일을 생성할 때 물어봤던 질문에 답변했던 비밀번호를 똑같이 입력합니다.
# Keystore 실제 적용
```android/app/build.gradle``` 파일을 열어 아래 내용을 추가합니다.
```gradle
android {
    ...
    defaultConfig { ... }
    signingConfigs {
        release {
            if (project.hasProperty('MYAPP_RELEASE_STORE_FILE')) {
                storeFile file(MYAPP_RELEASE_STORE_FILE)
                storePassword MYAPP_RELEASE_STORE_PASSWORD
                keyAlias MYAPP_RELEASE_KEY_ALIAS
                keyPassword MYAPP_RELEASE_KEY_PASSWORD
            }
        }
    }
    buildTypes {
        release {
            ...
            signingConfig signingConfigs.release
        }
    }
}
```
{: file="android/app/build.gradle"}

```android/app/build.gradle``` 파일을 열어보시면 이미 기존에 생성되어 있던 ```android``` Property가 있는데, 그 내부를 살펴보면 ```defaultConfig```이라는 Property가 존재합니다.  
해당 Property 바로 아랫줄에 위에 적힌 ```signingConfigs``` 객체를 추가합니다.

그 다음, ```android```Property 내부를 살펴보면 ```buildTypes```라는 Property가 이미 있을텐데, 그 내부에 ```release``` Property 안에 ```signingConfig signingConfigs.release``` 를 추가합니다.
# 빌드하기
```
cd android
./gradlew assembleRelease
```
위 명령어를 입력하면 빌드가 완료되고, ```android/app/build/output/apk/release``` 안에 ```.apk``` 파일이 생성됩니다.