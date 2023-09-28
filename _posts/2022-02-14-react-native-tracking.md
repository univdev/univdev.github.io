---
title: "[React Native] 유저 위치 트래킹"
author:
  name: 박 찬영
  link: https://github.com/univdev
date: 2022-02-14 11:25:00 +0900
categories: [Tech, React Native]
tags: [React Native, GPS, Coordinates]
---
# 개요
가끔 어플리케이션을 만들 때 사용자의 위치를 추적해야 하는 경우가 있습니다.  
주변 맛집을 찾아주거나, 운전 중 도로 상황을 알려주기 위한 어플리케이션을 제작할 때를 예로 들 수 있는데요.  
사용자의 위치 정보를 추적하는 방법을 아래에 기재합니다.
# 패키지 설치
사용자의 위치 정보를 손쉽게 추적하기 위한 패키지를 설치합니다.
```shell
yarn add react-native-geolocation-service
```
# 추적 권한
사용자의 현재 위치를 추적하는 것은 **매우 민감한 사항**입니다.  
그러다보니 위치에 접근하는 기능은 웹, 안드로이드, iOS 불문하고 사용자에게 위치 정보 접근 동의를 받는 절차를 거쳐야 합니다.
## iOS
```javascript
import Geolocation from 'react-native-geolocation-service';

const App = () => {
  Geolocation.requestAuthorization(level);
};

export default App;
```
iOS는 ```react-native-geolocation-service``` 패키지에서 제공하는 ```requestAuthorization``` 메소드를 통해 쉽게 동의를 받을 수 있습니다.  
대부분의 경우에서 앱을 접속하고 처음 보는 페이지에 적용하여 동의를 받아둔 상태에서 서비스를 제공합니다.
## Android
Android는 Android 정책에서 제공하는 권한 부여 방식을 사용해야 합니다.  
```xml
    <uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION" />
    <uses-permission android:name="android.permission.ACCESS_FINE_LOCATION" />
```
{: file="android/app/src/main/AndroidManifest.xml" }
```/android/app/src/main/AndroidManifest.xml``` 파일에 위에 기재한 코드를 적용합니다.

```jsx
import { PermissionsAndroid } from 'react-native';

export default () => {
  useEffect(() => {
    PermissionsAndroid.requestMultiple([
      PermissionsAndroid.PERMISSIONS.ACCESS_COARSE_LOCATION,
      PermissionsAndroid.PERMISSIONS.ACCESS_FINE_LOCATION,
    ]);
  }, []);
  // ...
}
```
마지막으로, 권한을 부여받을 상황에 맞는 페이지에 해당 코드를 추가합니다. 보통은 앱에 접속하자마자 권한을 받으므로, ```App.jsx``` 파일에 적용합니다.
# 사용자의 위치를 한 번만 얻기
```javascript
Geolocation.getCurrentPosition(
  (position) => {
    console.log(position);
  },
  (error) => {
    // See error code charts below.
    console.log(error.code, error.message);
  },
  { enableHighAccuracy: true, timeout: 15000, maximumAge: 10000 }
);
```
```Geolocation``` 패키지의 ```getCurrentPosition``` 메소드를 사용하면 사용자의 디바이스가 있는 위치를 받아올 수 있습니다.
# 사용자의 위치를 계속 추적하기
```javascript
Geolocation.watchPosition((position) => {
  const { latitude, longitude } = position.coords;
  console.log(latitude, longitude);
});
```
```Geolocation``` 패키지의 ```watchPosition``` 메소드를 사용하면 사용자의 디바이스의 위치가 변경 될 때마다 ```callback```이 실행되어 사용자의 위치를 갱신할 수 있습니다.
# 같이 보면 좋을 문서
[react-native-geolocation-service API 문서][API 문서]  
[[React Native] 네이버 지도 연동][네이버 지도 연동]

[API 문서]: https://www.npmjs.com/package/react-native-geolocation-service#API
[네이버 지도 연동]: https://univdev.github.io/posts/React_Native_%EB%84%A4%EC%9D%B4%EB%B2%84_%EC%A7%80%EB%8F%84_%EC%97%B0%EB%8F%99/