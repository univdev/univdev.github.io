---
title: "[Nuxt] Failed to execute put on cache 이슈"
author:
  name: 박 찬영
  link: https://github.com/univdev
date: 2022-03-17 16:28:00 +0900
categories: [Tech, Nuxt]
tags: [Tech, Nuxt, ServiceWorker]
---
# 개요
Nuxt로 개발을 하던 도중, 어느 순간부터 개발자 도구를 열면 다음과 같이 ```service-worker.js```라는 곳에서 에러를 쭉쭉 뱉고 있었습니다.  
찾아본 결과, 다음과 같은 방법으로 해결할 수 있었습니다.  
![Failed to execute put on cache][이슈]
# 해결 방법
```javascript
navigator.serviceWorker.getRegistrations().then(function(registrations) {
 for(let registration of registrations) {
  registration.unregister()
} });
```
{: file="devtools"}

Chrome에서 개발자도구 -> console 탭을 열고, 위 소스코드를 입력해서 실행시키면 해당 이슈가 발생하지 않습니다!

[이슈]: https://firebasestorage.googleapis.com/v0/b/univdev-github-io.appspot.com/o/execute_put_on_cache_issue.png?alt=media&token=ba7d2f53-8d2a-48ef-8f81-771bd62258f4