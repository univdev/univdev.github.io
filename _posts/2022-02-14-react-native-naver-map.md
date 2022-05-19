---
title: "[React Native] 네이버 지도 연동"
author:
  name: 박 찬영
  link: https://github.com/univdev
date: 2022-02-14 11:25:00 +0900
categories: [Tech, React Native]
tags: [React Native, Map]
---
# 개요
웹, 앱을 불문하고 사용 될 정도로 지도 서비스는 굉장히 범용적이고 유용한 서비스입니다.  
전세계적으로 구글 지도를 사용하지만 국내에서는 구글 지도의 한계 때문에 국내에서 서비스하는 지도를 사용해야 하는데, 보통 네이버 지도와 카카오 지도를 사용합니다.  
이 문서에서는 네이버 지도를 연동하는 방법에 대해서 기술합니다.
# 패키지 설치
네이버 지도를 이용하기 위하여 패키지를 설치해줍니다.
```shell
yarn add react-native-nmap
```
# 초기 설정
패키지를 설치하고 React Native에 연동하기 위해 거쳐야만 하는 과정이 있습니다.
## 어플리케이션 등록
네이버에서 제공하는 API 서비스를 이용하기 위해서는 개발하고 있는 어플리케이션을 등록해야 합니다.  
자세한 내용은 [여기][네이버 API]를 참고해주세요.

> 아래부터는 Application을 네이버에 등록했다는 전제 하에 기술합니다.

## Android
### Client ID 연동
```gradle
allprojects {
    repositories {
        ...
        // 네이버 지도 저장소
        maven {
            url 'https://naver.jfrog.io/artifactory/maven/'
        }
    }
}
```
{: file="/android/build.gradle" }
```/android/build.gradle``` 파일에 ```//네이버 지도 저장소``` 아래에 적힌 ```maven```을 추가합니다.  
```xml
  <meta-data
      android:name="com.naver.maps.map.CLIENT_ID"
      android:value="YOUR_CLIENT_ID_HERE" />
```
{: file="/android/app/src/AndroidManifest.xml" }
```/android/app/src/AndroidManifest.xml``` 파일의 ```<application>``` 태그 안쪽에 해당 구문을 추가합니다.  
```YOUR_CLIENT_ID_HERE```를 네이버에서 발급 받은 ```CLIENT ID``` 토큰으로 대체해줍니다.
> ```com.naver.maps.map.CLIENT_ID```의 ```CLIENT_ID```는 대체하지 마세요!

## iOS
### Client ID 연동
```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
...
    <key>NMFClientId</key>
    <string>YOUR_CLIENT_ID_HERE</string>
...
<dict>
<plist>
```
{: file="info.plist" }
```info.plist``` 파일에 ```key```와 ```string``` 태그를 한 쌍으로 추가합니다.  
```YOUR_CLIENT_ID_HERE``` 문자열을 ```CLIENT ID``` 토큰으로 대체해줍니다.
### git-lfs 설치 및 적용
```shell
npm i -g git-lfs
cd ios
git-lfs install
```
iOS에서는 네이버 맵을 원활하게 사용하기 위해서 ```git-lfs```를 설치해야 합니다.  
네이버 지도는 아무래도 저장되는 정보가 방대하다보니 Git에 업로드 할 때 문제가 발생합니다. Git 서버에서 제공하는 용량에 제한을 받지 않기 위해서 사용합니다.  
### 패키지 설치
```shell
pod install
```
마지막으로 ```ios``` 폴더에서 ```pod install```을 입력하여 iOS에서 사용할 패키지를 연동해줍니다.
# 실제 사용
{%raw%}
```jsx
import NaverMapView, {Circle, Marker, Path, Polyline, Polygon} from "react-native-nmap";

function MyMap() {
    const P0 = {latitude: 37.564362, longitude: 126.977011};
    const P1 = {latitude: 37.565051, longitude: 126.978567};
    const P2 = {latitude: 37.565383, longitude: 126.976292};

    return <NaverMapView style={{width: '100%', height: '100%'}}
                         showsMyLocationButton={true}
                         center={{...P0, zoom: 16}}
                         onTouch={e => console.warn('onTouch', JSON.stringify(e.nativeEvent))}
                         onCameraChange={e => console.warn('onCameraChange', JSON.stringify(e))}
                         onMapClick={e => console.warn('onMapClick', JSON.stringify(e))}>
        <Marker coordinate={P0} onClick={() => console.warn('onClick! p0')}/>
        <Marker coordinate={P1} pinColor="blue" onClick={() => console.warn('onClick! p1')}/>
        <Marker coordinate={P2} pinColor="red" onClick={() => console.warn('onClick! p2')}/>
        <Path coordinates={[P0, P1]} onClick={() => console.warn('onClick! path')} width={10}/>
        <Polyline coordinates={[P1, P2]} onClick={() => console.warn('onClick! polyline')}/>
        <Circle coordinate={P0} color={"rgba(255,0,0,0.3)"} radius={200} onClick={() => console.warn('onClick! circle')}/>
        <Polygon coordinates={[P0, P1, P2]} color={`rgba(0, 0, 0, 0.5)`} onClick={() => console.warn('onClick! polygon')}/>
    </NaverMapView>
}
```
{%endraw%}
{: file="example/App.jsx" }
```react-native-nmap``` 패키지는 지도를 구성하기 위한 각종 컴포넌트를 제공합니다.  
```NaverMapView```는 우리가 보는 지도 그 자체를 표시하는 View이고, 이 지도 내부에서 사용할 ```Marker``` ```Polygon``` ```Circle```도 사용할 수 있습니다.  

자세한 내용은 [여기][컴포넌트]에 기재 된 문서를 확인해주세요!

[네이버 API]: https://navermaps.github.io/android-map-sdk/guide-ko/1.html
[컴포넌트]: https://github.com/QuadFlask/react-native-naver-map#%EC%BB%B4%ED%8F%AC%EB%84%8C%ED%8A%B8