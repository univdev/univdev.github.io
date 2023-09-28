---
title: "[React Native] 물리적 Back 버튼으로 Webview 네비게이션 제어하기"
author:
  name: 박 찬영
  link: https://github.com/univdev
date: 2022-03-08 12:49:00 +0900
categories: [Tech, React Native]
tags: [React Native, Webview, Android]
---
# 개요
하이브리드 어플리케이션을 제작할 때 실제 앱 기능은 웹으로 만들고 앱은 웹뷰를 띄워주는 용도로만 사용하는 형태로 개발을 진행합니다.  
그런데 안드로이드의 경우 물리적인 뒤로가기 버튼이 존재하는데 이를 그냥 방치할 경우 물리적 Back 버튼을 눌렀을 때 웹뷰가 뒤로 가지는게 아닌, 앱이 꺼져버리는 현상이 나타나게 됩니다.  
실제로 인앱의 네비게이션을 사용한 것이 아닌 웹뷰의 네비게이션이 사용 되었기 때문이죠.  
이럴 경우 Webview의 ```Navigation Event```와 React Native의 ```HardwareBackButton Event``` 등을 이용하여 직접 제어를 해줘야 합니다.
# Case 1 (실패)
{%raw%}
```jsx
import React, { useRef, useState, useEffect } from 'react';
import { BackHandler } from 'react-native';
import { WebView } from 'react-native-webview';

export default () => {
  const webview = useRef();

  const onPressHardwareBackButton = () => {
    if (webview.current) {
      webview.current.goBack();
      return true;
    } else {
      return false;
    }
  };

  useEffect(() => {
    BackHandler.addEventListener('hardwareBackPress', onPressHardwareBackButton);
    return () => {
      BackHandler.removeEventListener('hardwareBackPress', onPressHardwareBackButton);
    }
  }, []);

  return (
    <WebView
      ref={webview}
      style={{
        overflow: 'hidden',
        opacity: .99,
      }}
      source={{
        uri: 'https://...',
      }}
    />
  );
};
```
{%endraw%}
React Native의 ```BackHandler``` 모듈을 사용하여 뒤로가기 이벤트에 대한 핸들링을 진행할 수 있습니다.

```hardwareBackPress``` 이벤트를 생성하고 두번째 인자로 ```callback``` 함수를 넘겨줍니다. 
이 ```callback``` 함수가 ```true```를 반환하면 하드웨어의 BackNavigation 기능을 ```prevent``` 처리합니다.  
```false```를 반환하면 원래대로 앱 종료 기능이 동작합니다.

```useRef```를 통해 Webview 객체를 받아오고 이 Webview가 현재 네비게이션을 수행할 수 있는 상태에 있는지 ```current```의 존재 여부로 체크합니다.  
네비게이션 수행이 가능하다면 ```webview.current.back``` 메소드를 실행하여 ```webview``` 내에서 뒤로가기 처리를 진행합니다.

## 실패 이유
```webview.current```는 웹뷰가 정상적으로 로드 된 시점에서는 무조건 객체가 들어있기 때문에 제일 첫 페이지에서 뒤로가기 버튼을 눌렀을 때 앱이 종료되는 기능을 수행할 수 없습니다.
# Case 2 (실패)
> 참조: [ricale.log - React Native Webview 안드로이드 백버튼 처리][Case 2 출처]

{%raw%}
```jsx
export default () => {
  const webview = useRef();
  const [navState, setNavState] = useState();

  useEffect(() => {
    const onPress = () => {
      if(navState.canGoBack) {
        // 뒤로 갈 수 있는 상태라면 이전 웹페이지로 이동한다
        webview.current.goBack();
        return true;
      } else {
        return false;
      }
    }
    // 안드로이드 백버튼이 눌렸을 때 이벤트 리스너를 등록한다.
    BackHandler.addEventListener('hardwareBackPress', onPress);
    return () => {
      BackHandler.removeEventListener('hardwareBackPress', onPress);
    }
  }, [navState.canGoBack])

  return (
    <WebView
      ref={webview}
      style={{
        overflow: 'hidden',
        opacity: .99,
      }}
      source={{
        uri: 'https://...',
      }}
      ref={webview}
      onNavigationStateChange={setNavState}
    />
  );
}
```
{%endraw%}
```onNavigationStateChange``` 이벤트는 ```Webview``` 페이지가 이동을 하게 되면 실행되는 이벤트입니다.  
여기에는 라우트 정보가 파라미터로 담겨져오게 되는데 ```canGoBack```이라는 Property도 넘어옵니다. 이 Property는 현재 라우트에서 뒤로 갈 수 있는 페이지가 존재하는지 ```true``` ```false```로 알려주는 Property입니다.  
해당 Property를 ```state```로 관리하여 값이 ```true```면 ```webview.current.back```을 실행하여 웹뷰 이동을 하고, 아닐 경우 하드웨어 뒤로가기 버튼의 본래 기능을 사용하면 됩니다.
## 실패 이유
```onNavigationStateChange```는 일반적인 웹에서 잘 동작하지만 Vue나 React와 같은 프레임워크를 사용하여 제작 된 SPA 웹의 경우 History API로 페이지 이동을 하기 때문에 ```onNavigationStateChange```가 실행되지 않습니다.

# Case 3 (실패)
> 참조: [ricale.log - React Native Webview 안드로이드 백버튼 처리][Case 2 출처]

{%raw%}
```jsx
import React, { useRef, useState, useEffect } from 'react';
import { BackHandler } from 'react-native';
import { WebView } from 'react-native-webview';

export default () => {
  const INJECTED_CODE = `
    (function() {
      function wrap(fn) {
        return function wrapper() {
          var res = fn.apply(this, arguments);
          window.ReactNativeWebView.postMessage('navigationStateChange');
          return res;
        }
      }

      history.pushState = wrap(history.pushState);
      history.replaceState = wrap(history.replaceState);
      window.addEventListener('popstate', function() {
        window.ReactNativeWebView.postMessage('navigationStateChange');
      });
    })();

    true;
    `;

  const onPressHardwareBackButton = () => {
    if (webview.current) {
      webview.current.goBack();
      return true;
    } else {
      return false;
    }
  };

  useEffect(() => {
    BackHandler.addEventListener('hardwareBackPress', onPressHardwareBackButton);
    return () => {
      BackHandler.removeEventListener('hardwareBackPress', onPressHardwareBackButton);
    }
  }, []);

  const webview = useRef();

  return (
    <WebView
      ref={webview}
      style={{
        overflow: 'hidden',
        opacity: .99,
      }}
      source={{
        uri: 'https://...',
      }}
      ref={ref}
      onLoadStart={() => ref.current.injectJavaScript(INJECTED_CODE)}
      onNavigationStateChange={setNavState}
    />
  );
}
```
{%endraw%}

```javascript``` 로직을 ```Webview```에 삽입하여 History API가 사용 되었음을 감지하고 ```onpopstate``` 이벤트가 실행 될 때마다 ```onNavigationStateChange``` 이벤트를 ```Webview```로 전달합니다.
## 실패 이유
원문 작성자의 글을 읽어보면 CSR 타이밍에 해당 로직을 Webview에 삽입하기 위해서 ```injectedJavascript``` Property가 아닌 ```onLoadStart``` 이벤트에서 사용한다고 나와있습니다.  
그런데 이렇게 작성을 하니까 ```window.ReactNativeWebView``` 객체를 찾지 못해 이벤트 전달이 되지가 않았습니다.
# Case 4 (성공)
> 출처: [React Native Webview 24번 이슈][Case 4 출처]
{%raw%}
```jsx
import React, { useRef, useState, useEffect } from 'react';
import { BackHandler } from 'react-native';
import { WebView } from 'react-native-webview';

export default () => {
  const webview = useRef();
  const [isCanGoBack, setIsCanGoBack] = useState(false);
  const onPressHardwareBackButton = () => {
    if (webview.current && isCanGoBack) {
      webview.current.goBack();
      return true;
    } else {
      return false;
    }
  };

  useEffect(() => {
    BackHandler.addEventListener('hardwareBackPress', onPressHardwareBackButton);
    return () => {
      BackHandler.removeEventListener('hardwareBackPress', onPressHardwareBackButton);
    }
  }, [isCanGoBack]);

  return (
    <WebView
      ref={webview}
      style={{
        overflow: 'hidden',
        opacity: .99,
      }}
      source={{
        uri: 'https://...',
      }}
      injectedJavaScript={`
        (function() {
          function wrap(fn) {
            return function wrapper() {
              var res = fn.apply(this, arguments);
              window.ReactNativeWebView.postMessage('navigationStateChange');
              return res;
            }
          }
    
          history.pushState = wrap(history.pushState);
          history.replaceState = wrap(history.replaceState);
          window.addEventListener('popstate', function() {
            window.ReactNativeWebView.postMessage('navigationStateChange');
          });
        })();
    
        true;
      `}
    onMessage={({ nativeEvent: state }) => {
      if (state.data === 'navigationStateChange') {
        // Navigation state updated, can check state.canGoBack, etc.
        setIsCanGoBack(state.canGoBack);
      }
    }}/>
  );
};
```
{%endraw%}
```injectedJavascript``` Property로 Javascript 로직을 전달하여 실행하고 ```onNavigationStateChange```로 받는게 아닌 ```onMessage```로 모든 메시지를 받은 다음 ```state.data```가 ```navigationStateChange```인 데이터만 선별하여 ```canGoBack```을 받아 ```state```에 적용합니다.

```useEffect```는 ```state```가 변경 될 때마다 뒤로 가기가 가능한지 불가능한지를 구분해야 하기 때문에 ```useEffect```의 실행 조건을 ```[isCanGoBack]```으로 설정합니다.

[Case 2 출처]: https://velog.io/@ricale/React-Native-WebView-%EC%95%88%EB%93%9C%EB%A1%9C%EC%9D%B4%EB%93%9C-%EB%B0%B1%EB%B2%84%ED%8A%BC-%EC%B2%98%EB%A6%AC
[Case 4 출처]: https://github.com/react-native-webview/react-native-webview/issues/24#issuecomment-483956651