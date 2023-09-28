---
title: "[React Native] Webview SSL Error: The Certificate authority is not trusted"
author:
  name: 박 찬영
  link: https://github.io/univdev
date: 2022-03-08 23:42:00 +0900
categories: [Tech, React Native]
tags: [React Native, Webview, Android]
---
![인증서 오류][에러 이미지]
# 개요
회사에서 하이브리드 어플리케이션을 개발하기 위해 웹뷰를 통해 포팅을 하고 있는데 기껏 개발해서 구축해놓은 웹사이트를 웹뷰에서 보니까 이런 에러가 나옵니다.  
인증서 날짜가 유효하지 않다는데 도무지 원인을 모르겠어서 해당 이슈를 어떻게 해결할까 하다가 Webview에서 SSL 관련 처리를 변경하는 방식으로 해결했습니다.
# 해결 방법
```node_modules``` 폴더 안에 설치 된 패키지 중에서 ```react-native-webview/android/src/main/java/com/reactnativecommunity/webview/RNCWebviewManager.java``` 파일을 열면 ```onReceivedSslError```라는 메소드가 있을텐데, ```handler.cancel()``` 부분을 ```handler.proceed()```로 변경합니다.

```java
    @Override
    public void onReceivedSslError(final WebView webView, final SslErrorHandler handler, final SslError error) {
        // onReceivedSslError is called for most requests, per Android docs: https://developer.android.com/reference/android/webkit/WebViewClient#onReceivedSslError(android.webkit.WebView,%2520android.webkit.SslErrorHandler,%2520android.net.http.SslError)
        // WebView.getUrl() will return the top-level window URL.
        // If a top-level navigation triggers this error handler, the top-level URL will be the failing URL (not the URL of the currently-rendered page).
        // This is desired behavior. We later use these values to determine whether the request is a top-level navigation or a subresource request.
        String topWindowUrl = webView.getUrl();
        String failingUrl = error.getUrl();

        // Cancel request after obtaining top-level URL.
        // If request is cancelled before obtaining top-level URL, undesired behavior may occur.
        // Undesired behavior: Return value of WebView.getUrl() may be the current URL instead of the failing URL.
        
        handler.proceed(); // <--- 여기가 원래 handler.cancel()로 되어 있는데 handler.proceed()로 수정하면 됩니다.
        ...
```
{: file="android/src/main/java/com/reactnativecommunity/webview/RNCWebviewManager.java"}
해당 부분은 SSL 인증서가 유효하지 않은 특정 웹사이트에 웹뷰가 접근하려 할 때, ```handler.cancel()```을 실행하면 해당 웹페이지로 접속하는 것을 차단하고, ```handler.proceed()```를 실행하면 웹사이트로 연결하는 역할을 합니다.

[에러 이미지]: https://firebasestorage.googleapis.com/v0/b/univdev-github-io.appspot.com/o/ssl_%E1%84%8B%E1%85%B5%E1%86%AB%E1%84%8C%E1%85%B3%E1%86%BC%E1%84%89%E1%85%A5_%E1%84%8B%E1%85%B5%E1%84%89%E1%85%B2.png?alt=media&token=fb85a73c-6f19-487e-9512-8319d3dd1045