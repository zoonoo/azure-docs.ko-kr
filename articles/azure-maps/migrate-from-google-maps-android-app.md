---
title: Android 앱 마이그레이션 | Microsoft Docs
description: Google Maps에서 Microsoft Azure 맵으로 Android 앱을 마이그레이션하는 방법에 대 한 자습서입니다.
author: rbrundritt
ms.author: richbrun
ms.date: 12/17/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: ''
ms.openlocfilehash: 60d8fcc9879e89276aad80bbaf3a0edf244a45b8
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75481400"
---
# <a name="migrate-an-android-app-from-google-maps"></a>Google Maps에서 Android 앱 마이그레이션

Azure Maps Android SDK에는 웹 SDK와 매우 유사한 API 인터페이스가 있습니다. 이러한 Sdk 중 하나를 사용 하 여 개발 하는 경우 동일한 개념, 모범 사례 및 아키텍처가 적용 되며 한 정보를 다른 것으로 쉽게 이전할 수 있습니다.

Azure Maps Android SDK는 최소 Android 버전의 API 21: Android 5.0.0 (롤리팝)를 지원 합니다.

그러나 Java에서 제공 되는 모든 예제는 Azure Maps Android SDK와 함께 사용할 수도 있습니다.

또한이 SDK를 사용 하 여 개발 하는 방법에 대 한 자세한 내용은 [Azure Maps Android SDK에 대 한 방법 가이드](how-to-use-android-map-control-library.md) 를 참조 하세요.

## <a name="load-a-map"></a>지도 로드

Google 또는 Azure Maps를 사용 하 여 Android 앱에서 지도를 로드 하는 것은 동일한 여러 단계로 구성 됩니다. SDK 중 하나를 사용 하는 경우 다음을 수행 해야 합니다.

- 하나 이상의 플랫폼에 액세스 하기 위한 API 또는 구독 키를 가져옵니다.
- 작업에 일부 XML을 추가 하 여 map이 렌더링 되어야 하는 위치 및 레이아웃을 지정 하는 방법을 지정 합니다.
- 지도 뷰를 포함 하는 활동의 모든 수명 주기 메서드를 map 클래스의 해당 항목으로 전달 합니다. 특히 다음과 같은 방법으로 전달 해야 합니다.
    - `onCreate(Bundle)`
    - `onStart()`
    - `onResume()`
    - `onPause()`
    - `onStop()`
    - `onDestroy()`
    - `onSaveInstanceState(Bundle)`
    - `onLowMemory()`
- 프로그래밍 방식으로 액세스를 시도 하기 전에 맵이 준비 될 때까지 기다립니다.

**이전: Google Maps**

Android 용 Google Maps SDK를 사용 하 여 지도를 표시 하려면 다음 단계를 수행 합니다.

1.  Google Play 서비스가 설치 되어 있는지 확인 합니다.
2.  Google Maps 서비스에 대 한 종속성을 모듈의 gradle 파일에 추가 **합니다** . 

    `implementation 'com.google.android.gms:play-services-maps:17.0.0'`

1.  Google Maps API 키를 **google\_Maps\_api .xml** 파일의 응용 프로그램 섹션 안에 추가 합니다.
    
    ```xml
    <meta-data android:name="com.google.android.geo.API_KEY" android:value="YOUR_GOOGLE_MAPS_KEY"/>
    ```

1.  기본 활동에 맵 단편 추가:

    ```xml
    <com.google.android.gms.maps.MapView
            android:id="@+id/myMap"
            android:layout_width="match_parent"
            android:layout_height="match_parent"/>
    ```

1.  **Mainactivity. java** 파일에서 GOOGLE Maps SDK에 대 한 가져오기를 추가 해야 합니다. 지도 뷰를 포함 하는 활동의 모든 수명 주기 메서드를 map 클래스의 해당 항목으로 전달 합니다. `MapView` 인스턴스는 `getMapAsync(OnMapReadyCallback)` 메서드를 사용 하 여 맵 조각에서 검색할 수 있습니다. `MapView`는 맵 시스템과 뷰를 자동으로 초기화 합니다. 다음과 같이 **Mainactivity. java** 파일을 편집 합니다.

    ```java
    import com.google.android.gms.maps.GoogleMap;
    import com.google.android.gms.maps.MapView;
    import com.google.android.gms.maps.OnMapReadyCallback;
    
    import android.support.v7.app.AppCompatActivity;
    import android.os.Bundle;
    
    public class MainActivity extends AppCompatActivity implements OnMapReadyCallback {
    
        MapView mapView;
    
        @Override
        protected void onCreate(Bundle savedInstanceState) {
            super.onCreate(savedInstanceState);
            setContentView(R.layout.activity_main);
    
            mapView = findViewById(R.id.myMap);
    
            mapView.onCreate(savedInstanceState);
            mapView.getMapAsync(this);
        }
    
        @Override
    
        public void onMapReady(GoogleMap map) {
            //Add your post map load code here.
        }
    
        @Override
        public void onResume() {
            super.onResume();
            mapView.onResume();
        }
    
        @Override
        protected void onStart(){
            super.onStart();
            mapView.onStart();
        }
    
        @Override
        public void onPause() {
            super.onPause();
            mapView.onPause();
        }
    
        @Override
        public void onStop() {
            super.onStop();
            mapView.onStop();
        }
    
        @Override
        public void onLowMemory() {
            super.onLowMemory();
            mapView.onLowMemory();
        }
    
        @Override
        protected void onDestroy() {
            super.onDestroy();
            mapView.onDestroy();
        }
    
        @Override
        protected void onSaveInstanceState(Bundle outState) {
            super.onSaveInstanceState(outState);
            mapView.onSaveInstanceState(outState);
        }
    }
    ```

응용 프로그램에서 실행 되는 경우 다음과 같이 맵 컨트롤이 로드 됩니다.

<center>

![간단한 Google Maps](media/migrate-google-maps-android-app/simple-google-maps.png)</center>

**이후: Azure Maps**

Android 용 Azure Maps SDK를 사용 하 여 맵을 표시 하려면 다음 단계를 수행 해야 합니다.

1. 최상위 **gradle** 파일을 열고 **모든 프로젝트**, **리포지토리** 블록 섹션에 다음 코드를 추가 합니다.

    ```
    maven {
            url "https://atlas.microsoft.com/sdk/android"
    }
    ```

2. **App/gradle** 를 업데이트 하 고 다음 코드를 추가 합니다.
    
    1. 프로젝트의 **매니페스트의 minsdkversion** 가 API 21 이상에 있는지 확인 합니다.

    2. Android 섹션에 다음 코드를 추가 합니다.

        ```
        compileOptions {
            sourceCompatibility JavaVersion.VERSION_1_8
            targetCompatibility JavaVersion.VERSION_1_8
        }
        ```
    3. 종속성 블록을 업데이트 하 고 최신 Azure Maps Android SDK에 대 한 새 구현 종속성 줄을 추가 합니다.

        ```
        implementation "com.microsoft.azure.maps:mapcontrol:0.2"
        ```

        > [!Note]
        > Azure Maps Android SDK 정기적으로 업그레이드 되 고 향상 됩니다. [Android 맵 컨트롤 시작](how-to-use-android-map-control-library.md) 설명서를 참조 하 여 최신 Azure Maps 구현 버전 번호를 가져올 수 있습니다. 또한 버전 번호를 "0.2"에서 "0 +"로 설정 하 여 항상 최신 버전을 가리키도록 할 수 있습니다.
    
    4. 도구 모음에서 **파일로** 이동한 다음 **Gradle 파일을 사용 하 여 프로젝트 동기화**를 클릭 합니다.
3. 주 활동에 지도 단편 추가 (res \> 레이아웃 \> 활동\_주 .xml):
    
    ```XML
    <?xml version="1.0" encoding="utf-8"?>
    <FrameLayout
        xmlns:android="http://schemas.android.com/apk/res/android"
        xmlns:app="http://schemas.android.com/apk/res-auto"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        >

        <com.microsoft.azure.maps.mapcontrol.MapControl
            android:id="@+id/mapcontrol"
            android:layout_width="match_parent"
            android:layout_height="match_parent"
            />
    </FrameLayout>
    ```

4. **Mainactivity. java** 파일에서 다음을 수행 해야 합니다.
    
    * Azure Maps SDK에 대 한 가져오기 추가
    * Azure Maps 인증 정보 설정
    * **onCreate** 메서드에서 map 컨트롤 인스턴스 가져오기

    `setSubscriptionKey` 또는 `setAadProperties` 메서드를 사용 하 여 `AzureMaps` 클래스에 대 한 인증 정보를 전역적으로 설정 하면 모든 보기에 인증 정보를 추가할 필요가 없습니다. 

    맵 컨트롤에는 Android의 OpenGL 수명 주기를 관리 하는 자체 수명 주기 방법이 포함 되어 있습니다 .이를 포함 하는 작업에서 직접 호출 해야 합니다. 응용 프로그램이 올바르게 작동 하려면 맵 컨트롤의 수명 주기 메서드를 호출 하 고, 맵 컨트롤을 포함 하는 작업에서 다음 수명 주기 메서드를 재정의 하 고 해당 맵 컨트롤 메서드를 호출 해야 합니다. 

    * `onCreate(Bundle)` 
    * `onStart()` 
    * `onResume()` 
    * `onPause()` 
    * `onStop()` 
    * `onDestroy()` 
    * `onSaveInstanceState(Bundle)` 
    * `onLowMemory()`

    다음과 같이 **Mainactivity. java** 파일을 편집 합니다.
    
    ```java
    package com.example.myapplication;

    import android.support.v7.app.AppCompatActivity;
    import android.os.Bundle;
    import com.microsoft.azure.maps.mapcontrol.AzureMaps;
    import com.microsoft.azure.maps.mapcontrol.MapControl;
    import com.microsoft.azure.maps.mapcontrol.layer.SymbolLayer;
    import com.microsoft.azure.maps.mapcontrol.options.MapStyle;
    import com.microsoft.azure.maps.mapcontrol.source.DataSource;

    public class MainActivity extends AppCompatActivity {
        
        static {
            AzureMaps.setSubscriptionKey("<Your Azure Maps subscription key>");
        }

        MapControl mapControl;

        @Override
        protected void onCreate(Bundle savedInstanceState) {
            super.onCreate(savedInstanceState);
            setContentView(R.layout.activity_main);

            mapControl = findViewById(R.id.mapcontrol);

            mapControl.onCreate(savedInstanceState);
    
            //Wait until the map resources are ready.
            mapControl.onReady(map -> {
                //Add your post map load code here.
    
            });
        }

        @Override
        public void onResume() {
            super.onResume();
            mapControl.onResume();
        }

        @Override
        protected void onStart(){
            super.onStart();
            mapControl.onStart();
        }

        @Override
        public void onPause() {
            super.onPause();
            mapControl.onPause();
        }

        @Override
        public void onStop() {
            super.onStop();
            mapControl.onStop();
        }

        @Override
        public void onLowMemory() {
            super.onLowMemory();
            mapControl.onLowMemory();
        }

        @Override
        protected void onDestroy() {
            super.onDestroy();
            mapControl.onDestroy();
        }

        @Override
        protected void onSaveInstanceState(Bundle outState) {
            super.onSaveInstanceState(outState);
            mapControl.onSaveInstanceState(outState);
        }
    }
    ```

응용 프로그램을 실행 하는 경우 다음과 같이 맵 컨트롤이 로드 됩니다.

<center>

![단순 Azure Maps](media/migrate-google-maps-android-app/simple-azure-maps.png)</center>

Azure Maps 컨트롤은 확대/축소를 지원 하 고 전 세계 보기를 제공 합니다.

> [!TIP]
> Windows에서 Android 에뮬레이터를 사용 하는 경우 OpenGL 및 소프트웨어 가속 그래픽 렌더링과 충돌 때문에 맵이 렌더링 되지 않을 수 있습니다. 다음은이 문제를 해결 하기 위해 일부 작업을 수행 했습니다. AVD 관리자를 열고 편집할 가상 장치를 선택 합니다. **에뮬레이트된 성능** 섹션에서 **그래픽** 옵션을 **하드웨어**로 설정 합니다.

## <a name="localizing-the-map"></a>맵 지역화

대상 그룹이 여러 국가에 걸쳐 분산 되어 있거나 다른 언어를 말하는 경우 지역화가 중요 합니다.

**이전: Google Maps**

맵의 컨텍스트 뷰를 설정 하기 전에 다음 코드를 추가 하 여 기본 활동의 `onCreate` 메서드에서 맵의 언어를 설정할 수 있습니다. 다음은 언어 코드 "fr"을 사용 하 여 언어를 프랑스어로 제한 합니다.

```java
String languageToLoad = "fr";
Locale locale = new Locale(languageToLoad);
Locale.setDefault(locale);
Configuration config = new Configuration();
config.locale = locale;
getBaseContext().getResources().updateConfiguration(config,
        getBaseContext().getResources().getDisplayMetrics());
```

언어가 "fr"로 설정 된 Google Maps의 예는 다음과 같습니다.

<center>

![Google Maps 지역화](media/migrate-google-maps-android-app/google-maps-localization.png)</center>

**이후: Azure Maps**

Azure Maps은 지도의 언어 및 지역 보기를 설정 하는 세 가지 다른 방법을 제공 합니다. 첫 번째 옵션은 정적 `setLanguage` 및 `setView` 메서드를 전역적으로 사용 하 여 언어 및 지역 뷰 정보를 `AzureMaps` 클래스에 전달 하는 것입니다. 그러면 앱에 로드 된 모든 Azure Maps 컨트롤에 대해 기본 언어 및 지역 보기가 설정 됩니다. 다음은 언어 코드 "fr-fr"을 사용 하 여 언어를 프랑스어로 제한 합니다.

```java
static {
    //Set your Azure Maps Key.
    AzureMaps.setSubscriptionKey("<Your Azure Maps Key>");

    //Set the language to be used by Azure Maps.
    AzureMaps.setLanguage("fr-FR");

    //Set the regional view to be used by Azure Maps.
    AzureMaps.setView("auto");
}
```

두 번째 옵션은 언어 및 뷰 정보를 맵 컨트롤 XML로 전달 하는 것입니다.

```xml
<com.microsoft.azure.maps.mapcontrol.MapControl
    android:id="@+id/myMap"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    app:mapcontrol_language="fr-FR"
    app:mapcontrol_view="auto"
    />
```

세 번째 옵션은 maps `setStyle` 메서드를 사용 하 여 맵의 언어 및 지역별 뷰를 프로그래밍 방식으로 설정 하는 것입니다. 언제 든 지이 작업을 수행 하 여 지도의 언어 및 지역별 보기를 변경할 수 있습니다.

```java
mapControl.onReady(map -> {
    map.setStyle(StyleOptions.language("fr-FR"));
    map.setStyle(StyleOptions.view("auto"));
});
```

언어가 "fr-fr"로 설정 된 Azure Maps 예제는 다음과 같습니다.

<center>

![Azure Maps 지역화](media/migrate-google-maps-android-app/azure-maps-localization.png)</center>

지원 되는 언어 및 지역 보기의 전체 목록은 [여기](supported-languages.md)에 설명 되어 있습니다.

## <a name="setting-the-map-view"></a>지도 보기 설정

Azure와 Google Maps의 동적 맵은 적절 한 메서드를 호출 하 여 프로그래밍 방식으로 새 지리적 위치로 이동할 수 있습니다. 아래 예제에서는 지도를 위성 항공 이미지를 표시 하는 방법을 보여 줍니다. 지도를 좌표를 사용 하 여 위치 (위도: 35.0272, 경도:-111.0225)로 가운데 맞춤 하 고 Google Maps에서 확대/축소 수준을 15로 변경 합니다.

> [!NOTE]
> Google Maps는 Azure Maps 차원에서 256 픽셀인 타일을 사용 하 고는 더 큰 512 픽셀 타일을 사용 합니다. 그러면 Azure Maps에서 Google Maps와 동일한 맵 영역을 로드 하는 데 필요한 네트워크 요청 수가 줄어듭니다. 그러나 지도 컨트롤에서 타일 피라미드가 작동 하는 방식으로 인해 Azure Maps의 큰 타일이 Google Maps에서 지도와 동일한 볼 수 있는 영역을 얻기 위해 Azure Maps 사용할 때 Google Maps에서 사용 되는 확대/축소 수준을 1로 빼야 합니다.

**이전: Google Maps**

Google 지도 지도 컨트롤의 카메라는 지도의 중심 및 확대/축소 수준을 지정할 수 있는 `moveCamera` 메서드를 사용 하 여 프로그래밍 방식으로 이동할 수 있습니다. `setMapType` 메서드를 사용 하 여 표시 되는 맵의 유형을 변경할 수 있습니다.

```java
@Override
public void onMapReady(GoogleMap googleMap) {
    mapView = googleMap;

    mapView.moveCamera(CameraUpdateFactory.newLatLngZoom(new LatLng(35.0272, -111.0225), 15));
    mapView.setMapType(GoogleMap.MAP_TYPE_SATELLITE);
}
```

<center>

![Google Maps 집합 보기](media/migrate-google-maps-android-app/google-maps-set-view.png)</center>

**이후: Azure Maps**

앞에서 설명한 것 처럼 Azure Maps에서 볼 수 있는 동일한 영역을 얻기 위해 Google Maps에서 사용 되는 확대/축소 수준을 1로 빼서이 경우 확대/축소 수준 14를 사용 합니다.

초기 맵 보기는 맵 컨트롤의 XML 특성에서 설정할 수 있습니다.

```xml
<com.microsoft.azure.maps.mapcontrol.MapControl
    android:id="@+id/myMap"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    app:mapcontrol_cameraLat="35.0272"
    app:mapcontrol_cameraLng="-111.0225"
    app:mapcontrol_zoom="14"
    app:mapcontrol_style="satellite"
    />
```

지도 보기는 지도 `setCamera` 및 `setStyle` 메서드를 사용 하 여 프로그래밍 방식으로 업데이트할 수 있습니다.

```java
mapControl.onReady(map -> {
    //Set the camera of the map.
    map.setCamera(center(35.0272, -111.0225), zoom(14));

    //Set the style of the map.
    map.setStyle(style(MapStyle.SATELLITE));
});
```

<center>

![Azure Maps 집합 뷰](media/migrate-google-maps-android-app/azure-maps-set-view.png)</center>

**추가 리소스:**

- [지원 되는 지도 스타일](supported-map-styles.md)

## <a name="adding-a-marker"></a>표식 추가

지점 데이터는 맵의 이미지를 사용 하 여 맵에서 렌더링 되는 경우가 많습니다. 이러한 이미지를 종종 표식, 압정, 핀 또는 기호 라고 합니다. 다음 예제에서는 지점 데이터를 지도에 표식으로 렌더링 합니다 (위도: 51.5, 경도:-0.2).

**이전: Google Maps**

Google Maps를 사용 하면 지도 `addMarker` 메서드를 사용 하 여 표식이 추가 됩니다.

```java
@Override
public void onMapReady(GoogleMap googleMap) {
    mapView = googleMap;

    mapView.addMarker(new MarkerOptions().position(new LatLng(47.64, -122.33)));
}
```

<center>

Google Maps 표식 ![](media/migrate-google-maps-android-app/google-maps-marker.png)</center>

**이후: Azure Maps**

Azure Maps 지점에서는 먼저 데이터 원본에 데이터를 추가한 다음 해당 데이터 원본을 기호 계층에 연결 하 여 맵에서 데이터를 렌더링할 수 있습니다. 데이터 원본은 지도 컨트롤에서 공간 데이터의 관리를 최적화 하 고 기호 계층은를 사용 하 여 요소 데이터를 이미지 및/또는 텍스트로 렌더링 하는 방법을 지정 합니다.

```java
mapControl.onReady(map -> {
    //Create a data source and add it to the map.
    DataSource dataSource = new DataSource();
    map.sources.add(dataSource);

    //Create a point feature and add it to the data source.
    dataSource.add(Feature.fromGeometry(Point.fromLngLat(-122.33, 47.64)));

    //Create a symbol layer and add it to the map.
    map.layers.add(new SymbolLayer(dataSource));
});
```

<center>

![Azure Maps 표식](media/migrate-google-maps-android-app/azure-maps-marker.png)</center>

## <a name="adding-a-custom-marker"></a>사용자 지정 표식 추가

사용자 지정 이미지를 사용 하 여 지도의 요소를 나타낼 수 있습니다. 아래 예제에서 사용 되는 다음 이미지는 사용자 지정 이미지를 사용 하 여 지도의 점을 (위도: 51.5, 경도:-0.2) 표시 하 고 압정 아이콘의 점이 지도의 올바른 위치에 맞게 조정 되도록 표식의 위치를 오프셋 합니다.

<center>

![노란색 압정 이미지](media/migrate-google-maps-web-app/ylw_pushpin.png)<br/>
ylw\_압정</center>

두 예에서 위의 이미지는 앱 리소스의 그릴 수 있는 폴더에 추가 됩니다.

**이전: Google Maps**

Google Maps를 사용 하면 표식의 `icon` 옵션을 통해 사용자 지정 이미지를 로드 하 여 표식에 사용할 수 있습니다. 이미지의 점을 좌표에 맞추려면 `anchor` 옵션을 사용할 수 있습니다. 앵커는 이미지의 크기를 기준으로 합니다 (이 경우 0.2 단위 너비, 1 단위 높음).

```java
@Override
public void onMapReady(GoogleMap googleMap) {
    mapView = googleMap;

    mapView.addMarker(new MarkerOptions().position(new LatLng(47.64, -122.33))
    .icon(BitmapDescriptorFactory.fromResource(R.drawable.ylw_pushpin))
    .anchor(0.2f, 1f));
}
```

<center>

![Google Maps 사용자 지정 표식](media/migrate-google-maps-android-app/google-maps-custom-marker.png)</center>

**이후: Azure Maps**

Azure Maps 기호 계층은 사용자 지정 이미지도 지원 하지만 먼저 이미지를 지도 리소스에 로드 하 고 고유한 ID를 할당 해야 합니다. 그러면 기호 계층에서이 ID를 참조할 수 있습니다. 기호는 `iconOffset` 옵션을 사용 하 여 이미지의 올바른 지점에 맞게 오프셋할 수 있습니다. 아이콘 오프셋은 픽셀 단위입니다. 기본적으로 오프셋은 이미지의 아래쪽 중심을 기준으로 하지만 `iconAnchor` 옵션을 사용 하 여 조정할 수 있습니다. 이 예제에서는 `iconAnchor` 옵션을 `"center"` 설정 하 고 아이콘 오프셋을 사용 하 여 이미지를 오른쪽으로 5 픽셀 이동 하 고 압정 이미지의 점에 맞춰 15 픽셀 위쪽으로 이동 합니다.

```java
mapControl.onReady(map -> {
    //Create a data source and add it to the map.
    DataSource dataSource = new DataSource();
    map.sources.add(dataSource);

    //Create a point feature and add it to the data source.
    dataSource.add(Feature.fromGeometry(Point.fromLngLat(-122.33, 47.64)));

    //Load the custom image icon into the map resources.
    map.images.add("my-yellow-pin", R.drawable.ylw_pushpin);

    //Create a symbol that uses the custom image icon and add it to the map.
    map.layers.add(new SymbolLayer(dataSource,
        iconImage("my-yellow-pin"),
        iconAnchor(AnchorType.CENTER
        iconOffset(new Float[]{5f, -15f})));
});
```

<center>

사용자 지정 표식 Azure Maps ![](media/migrate-google-maps-android-app/azure-maps-custom-marker.png)</center>

## <a name="adding-a-polyline"></a>다중선 추가

다중선은 지도의 선 또는 경로를 나타내는 데 사용 됩니다. 다음 예에서는 지도에서 파선 다중선을 만드는 방법을 보여 줍니다.

**이전: Google Maps**

Google Maps를 사용 하면 `PolylineOptions` 클래스를 사용 하 여 폴리라인을 만들고 `addPolyline` 메서드를 사용 하 여 맵에 추가할 수 있습니다. 스트로크 색은 `color` 옵션을 사용 하 여 설정 하 고, 스트로크 너비는 width 옵션을 사용 하 여 설정 하 고, 스트로크 파선 배열은 `pattern` 옵션을 사용 하 여 설정할 수 있습니다.

```java
@Override
public void onMapReady(GoogleMap googleMap) {
    mapView = googleMap;

    //Create the options for the polyline.
    PolylineOptions lineOptions = new PolylineOptions()
            .add(new LatLng(46, -123))
            .add(new LatLng(49, -122))
            .add(new LatLng(46, -121))
            .color(Color.RED)
            .width(10f)
            .pattern(Arrays.<PatternItem>asList(
                    new Dash(30f), new Gap(30f)));

    //Add the polyline to the map.
    Polyline polyline = mapView.addPolyline(lineOptions);
}
```

<center>

![Google Maps 폴리라인](media/migrate-google-maps-android-app/google-maps-polyline.png)</center>

**이후: Azure Maps**

Azure Maps에서는 다중선을 LineString 또는 MultiLineString 개체 라고 합니다. 이러한 개체를 데이터 원본에 추가 하 고 선 계층을 사용 하 여 렌더링할 수 있습니다. 스트로크 너비 및 대시 배열 "픽셀" 단위는 두 Sdk에서 동일한 값을 사용 하 여 동일한 결과를 생성 하는 Azure Maps 웹 SDK와 일치 합니다.

```java
mapControl.onReady(map -> {
    //Create a data source and add it to the map.
    DataSource dataSource = new DataSource();
    map.sources.add(dataSource);

    //Create an array of points.
    List<Point> points = Arrays.asList(
        Point.fromLngLat(-123, 46),
        Point.fromLngLat(-122, 49),
        Point.fromLngLat(-121, 46));

    //Create a LineString feature and add it to the data source.
    dataSource.add(Feature.fromGeometry(LineString.fromLngLats(points)));

    //Create a line layer and add it to the map.
    map.layers.add(new LineLayer(dataSource,
        strokeColor("red"),
        strokeWidth(4f),
        strokeDashArray(new Float[]{3f, 3f})));
});
```

<center>

![Azure Maps 폴리라인](media/migrate-google-maps-android-app/azure-maps-polyline.png)</center>

## <a name="adding-a-polygon"></a>다각형 추가

다각형은 지도의 영역을 나타내는 데 사용 됩니다. 다음 예에서는 지도의 가운데 좌표를 기준으로 삼각형을 형성 하는 다각형을 만드는 방법을 보여 줍니다.

**이전: Google Maps**

Google Maps를 사용 하면 `PolygonOptions` 클래스를 사용 하 여 다각형을 만들고 `addPolygon` 메서드를 사용 하 여 지도에 추가할 수 있습니다. 채우기 및 스트로크 색은 `fillColor` 및 `strokeColor` 옵션을 사용 하 여 설정할 수 있으며, 스트로크 너비는 `strokeWidth` 옵션을 사용 하 여 설정 됩니다.

```java
@Override
public void onMapReady(GoogleMap googleMap) {
    mapView = googleMap;

    //Create the options for the polygon.
    PolygonOptions polygonOptions = new PolygonOptions()
            .add(new LatLng(46, -123))
            .add(new LatLng(49, -122))
            .add(new LatLng(46, -121))
            .add(new LatLng(46, -123))  //Close the polygon.
            .fillColor(Color.argb(128, 0, 128, 0))
            .strokeColor(Color.RED)
            .strokeWidth(5f);

    //Add the polygon to the map.
    Polygon polygon = mapView.addPolygon(polygonOptions);
}
```

<center>

Google 지도 polygon을 ![](media/migrate-google-maps-android-app/google-maps-polygon.png)</center>

**이후: Azure Maps**

Azure Maps에서 Polygon 및 MultiPolygon 개체는 데이터 원본에 추가 하 고 레이어를 사용 하 여 지도에서 렌더링할 수 있습니다. 다각형의 면적은 다각형 계층에서 렌더링 될 수 있습니다. 다각형의 윤곽선은 선 계층을 사용 하 여 렌더링할 수 있습니다. 스트로크 너비 및 대시 배열 "픽셀" 단위는 두 Sdk에서 동일한 값을 사용 하 여 동일한 결과를 생성 하는 Azure Maps 웹 SDK와 일치 합니다.

```java
mapControl.onReady(map -> {
    //Create a data source and add it to the map.
    DataSource dataSource = new DataSource();
    map.sources.add(dataSource);

    //Create an array of point arrays to create polygon rings.
    List<List<Point>> rings = Arrays.asList(Arrays.asList(
        Point.fromLngLat(-123, 46),
        Point.fromLngLat(-122, 49),
        Point.fromLngLat(-121, 46),
        Point.fromLngLat(-123, 46)));

    //Create a Polygon feature and add it to the data source.
    dataSource.add(Feature.fromGeometry(Polygon.fromLngLats(rings)));

    //Add a polygon layer for rendering the polygon area.
    map.layers.add(new PolygonLayer(dataSource,
        fillColor("green"),
        fillOpacity(0.5f)));

    //Add a line layer for rendering the polygon outline.
    map.layers.add(new LineLayer(dataSource,
        strokeColor("red"),
        strokeWidth(2f)));
});
```

<center>

![Azure Maps polygon](media/migrate-google-maps-android-app/azure-maps-polygon.png)</center>

## <a name="overlay-a-tile-layer"></a>타일 계층 오버레이

Google Maps에서 이미지 오버레이가 라고도 하는 타일 계층을 사용 하면 지도 바둑판식 배열 시스템과 맞춘 더 작은 바둑판식 이미지로 분할 된 레이어 이미지를 오버레이 할 수 있습니다. 이는 레이어 이미지 또는 매우 큰 데이터 집합을 겹치게 하는 일반적인 방법입니다.

다음 예에서는 아이오 환경적 Mesonet 아이오 State 대학의 날씨 레이더 타일 계층을 오버레이 합니다. 타일의 크기는 256 픽셀입니다.

**이전: Google Maps**

Google Maps를 사용 하 여 타일 계층을 지도 위에 겹쳐서 표시할 수 있습니다 `TileOverlayOptions` 클래스를 사용 하 고 `addTileLauer` 메서드를 사용 하 여 맵에 추가 합니다. 타일을 반투명 하 게 만들려면 `transparency` 옵션이 0.2 또는 20% 투명으로 설정 되어 있습니다.

```java
@Override
public void onMapReady(GoogleMap googleMap) {
    mapView = googleMap;

    //Create the options for the tile layer.
    TileOverlayOptions tileLayer = new TileOverlayOptions()
        .tileProvider(new UrlTileProvider(256, 256) {
            @Override
            public URL getTileUrl(int x, int y, int zoom) {

                try {
                    //Define the URL pattern for the tile images.
                    return new URL(String.format("https://mesonet.agron.iastate.edu/cache/tile.py/1.0.0/nexrad-n0q-900913/%d/%d/%d.png", zoom, x, y));
                }catch (MalformedURLException e) {
                    throw new AssertionError(e);
                }
            }
        }).transparency(0.2f);

    //Add the tile layer to the map.
    mapView.addTileOverlay(tileLayer);
}
```

<center>

Google Maps 타일 계층을 ![](media/migrate-google-maps-android-app/google-maps-tile-layer.png)</center>

**이후: Azure Maps**

Azure Maps에서 타일 계층을 다른 레이어와 거의 같은 방식으로 지도에 추가할 수 있습니다. X, y 및 zoom 자리 표시 자가 있는 형식이 지정 된 URL입니다. `{x}`, `{y}`, `{z}`는 각각 타일에 액세스할 위치를 계층에 알리는 데 사용 됩니다. Azure Maps의 타일 계층은 `{quadkey}`, `{bbox-epsg-3857}` 및 `{subdomain}` 자리 표시자도 지원 합니다. 타일 계층을 반투명 하 게 만들기 위해 불투명도 값 0.8이 사용 됩니다. 불투명도 및 투명도는 유사 하지만 반전 된 값을 사용 합니다. 둘 사이를 변환 하려면 숫자 1에서 해당 값을 뺍니다.

> [!TIP]
> Azure Maps 레이어는 기본 지도 계층을 포함 하 여 다른 계층 아래에서 쉽게 렌더링할 수 있습니다. 쉽게 읽을 수 있도록 지도 레이블 아래에 타일 계층을 렌더링 하는 것이 좋습니다. `map.layers.add` 메서드는 아래에 새 계층을 삽입할 계층의 id 인 두 번째 매개 변수를 사용 합니다. 지도 레이블 아래에 타일 계층을 삽입 하려면 다음 코드를 사용할 수 있습니다. `map.layers.add(myTileLayer, "labels");`

```java
mapControl.onReady(map -> {
    //Add a tile layer to the map, below the map labels.
    map.layers.add(new TileLayer(
        tileUrl("https://mesonet.agron.iastate.edu/cache/tile.py/1.0.0/nexrad-n0q-900913/{z}/{x}/{y}.png"),
        opacity(0.8f),
        tileSize(256)
    ), "labels");
});
```

<center>

![Azure Maps 타일 계층](media/migrate-google-maps-android-app/azure-maps-tile-layer.png)</center>

## <a name="show-traffic"></a>교통량 표시

트래픽 데이터는 Azure와 Google maps 모두에 중첩 될 수 있습니다.

**이전: Google Maps**

Google Maps를 사용 하면 지도의 `setTrafficEnabled` 메서드에 true를 전달 하 여 지도 위에 트래픽 흐름 데이터를 겹쳐서 표시할 수 있습니다.

```java
@Override
public void onMapReady(GoogleMap googleMap) {
    mapView = googleMap;

    mapView.setTrafficEnabled(true);
}
```

<center>

![Google Maps 트래픽](media/migrate-google-maps-android-app/google-maps-traffic.png)</center>

**이후: Azure Maps**

Azure Maps는 트래픽 표시를 위한 여러 가지 옵션을 제공 합니다. 여행 여행 및 사고와 같은 트래픽 인시던트를 지도에 아이콘으로 표시할 수 있습니다. 트래픽 흐름, 색으로 구분 된도로는 지도에 중첩 될 수 있으며, 일반적인 예상 지연 시간 또는 절대 지연 시간을 기준으로 게시 된 속도 제한을 기준으로 색을 변경할 수 있습니다. Azure Maps의 인시던트 데이터는 1 분 마다 업데이트 되 고 데이터 흐름은 2 분 마다 업데이트 됩니다.

```java
mapControl.onReady(map -> {
    map.setTraffic(
        incidents(true),
        flow(TrafficFlow.RELATIVE));
});
```

<center>

트래픽 Azure Maps ![](media/migrate-google-maps-android-app/azure-maps-traffic.png)</center>

## <a name="next-steps"></a>다음 단계

Azure Maps Android SDK에 대해 자세히 알아보세요.

> [!div class="nextstepaction"]
> [Android 맵 컨트롤을 사용 하는 방법](how-to-use-android-map-control-library.md)
