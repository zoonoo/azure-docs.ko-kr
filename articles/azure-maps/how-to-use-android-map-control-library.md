---
title: Azure Maps에서 Android 지도 컨트롤을 사용하는 방법 | Microsoft Docs
description: Azure Maps에서 Android 지도 컨트롤을 사용합니다.
author: walsehgal
ms.author: v-musehg
ms.date: 02/12/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: e3f7579324e1218cc2e2c3594889db776da6e529
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/12/2019
ms.locfileid: "56119144"
---
# <a name="how-to-use-azure-maps-android-sdk"></a>Azure Maps Android SDK 사용 방법

Azure Maps Android SDK는 Android용 벡터 지도 라이브러리입니다. 이 문서에서는 Azure Maps Android SDK를 설치하고, 지도를 로드하고, 핀을 배치하는 과정을 안내합니다.

## <a name="prerequisites-to-get-started"></a>시작하기 위한 필수 조건

### <a name="create-an-azure-maps-account"></a>Azure Maps 계정 만들기 

이 가이드의 단계를 수행하려면 먼저 [계정 및 키 관리](how-to-manage-account-keys.md)를 참조하여 S1 가격 책정 계층으로 계정 구독을 만들고 관리해야 합니다.

### <a name="download-android-studio"></a>Android Studio 다운로드

Google에서 [Android Studio](https://developer.android.com/studio/)를 다운로드할 수 있습니다. Azure Maps Android SDK를 설치하려면 먼저 Android Studio를 다운로드하고 빈 작업으로 프로젝트를 만들어야 합니다.

## <a name="create-a-project-in-android-studio"></a>Android Studio에서 프로젝트 만들기

빈 작업으로 새 프로젝트를 만들어야 합니다. 새 Android Studio 프로젝트를 만들려면 아래 단계를 수행합니다.

1. 프로젝트 선택에서 애플리케이션을 실행할 폼 팩터로 “휴대폰 및 태블릿”을 선택합니다.
2. 폼 팩터에서 빈 작업을 클릭하고 **다음**을 클릭합니다.
3. 프로젝트 구성에서 최소 SDK로 `API 21: Android 5.0.0 (Lollipop)`을 선택합니다. Azure Maps Android SDK에서 지원하는 가장 낮은 버전입니다.
4. 기본 `Activity Name` 및 `Layout Name`을 그대로 적용하고 **마침**을 클릭합니다.

Android Studio를 설치하고 새 프로젝트를 만드는 방법에 대한 자세한 도움말은 [Android Studio 문서](https://developer.android.com/studio/intro/)를 참조하세요.

![새 프로젝트 만들기](./media/how-to-use-android-map-control-library/form-factor-android.png)

## <a name="set-up-a-virtual-device"></a>가상 디바이스 설정

Android Studio를 사용하면 컴퓨터에 가상 Android 디바이스를 설정할 수 있습니다. 이렇게 하면 개발하는 동안 애플리케이션을 테스트하는 데 도움이 됩니다. 가상 디바이스를 설정하려면 프로젝트 화면의 오른쪽 위에 있는 AVD(Android 가상 디바이스) 관리자 아이콘을 클릭합니다. 그런 다음, **가상 디바이스 만들기** 단추를 클릭합니다. 도구 모음의 도구 > Android > AVD 관리자를 통해 관리자를 사용할 수도 있습니다. **휴대폰** 범주에서 **Nexus 5X**를 선택하고 **다음**을 클릭합니다.

[Android Studio 문서](https://developer.android.com/studio/run/managing-avds)에서 AVD 설정 방법을 자세히 알아보세요.

![Android Emulator](./media/how-to-use-android-map-control-library/android-emulator.png)

## <a name="install-azure-maps-android-sdk"></a>Azure Maps Android SDK 설치

애플리케이션 빌드를 진행하기 전에 Azure Maps Android SDK를 설치하려면 아래 단계를 수행합니다. 

1. **build.gradle** 파일의 **allprojects** 리포지토리 블록에 다음을 추가합니다.

    ```
    maven {
            url "https://atlas.microsoft.com/sdk/android"
    }
    ```

2. **app/build.gradle**을 업데이트하고 다음을 추가합니다.

    1. Android 블록에 다음을 추가합니다.

        ```
        compileOptions {
            sourceCompatibility JavaVersion.VERSION_1_8
            targetCompatibility JavaVersion.VERSION_1_8
        }
        ```
    2. 종속성 블록을 업데이트하고 다음을 추가합니다.

        ```
        implementation "com.microsoft.azure.maps:mapcontrol:0.1"
        ```

3. ‘AndroidManifest.xml’에 다음을 추가하여 사용 권한을 설정합니다.

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <manifest>
        ...
        <uses-permission android:name="android.permission.ACCESS_FINE_LOCATION" />
        ...
    </manifest>
    ```

4. **res > layout > activity_main.xml**을 아래 XML과 같이 편집합니다.
    
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
            app:mapcontrol_cameraTargetLat="47.64"
            app:mapcontrol_cameraTargetLng="-122.33"
            app:mapcontrol_cameraZoom="12"
            />

    </FrameLayout>
    ```

5. **MainActivity.java**를 편집하여 지도 보기 작업 클래스를 만듭니다. 편집한 후의 클래스는 다음과 같아야 합니다.

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
        
        static{
            AzureMaps.setSubscriptionKey("{subscription-key}");
        }

        MapControl mapControl;

        @Override
        protected void onCreate(Bundle savedInstanceState) {
            super.onCreate(savedInstanceState);
            setContentView(R.layout.activity_main);

            mapControl = findViewById(R.id.mapcontrol);

            mapControl.onCreate(savedInstanceState);

        }

        @Override
        public void onResume() {
            super.onResume();
            mapControl.onResume();
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

## <a name="import-classes"></a>클래스 가져오기

위의 단계를 완료하면 코드의 일부 텍스트에 Android Studio의 경고가 표시될 가능성이 큽니다. 이 문제를 처리하려면 `MainActivity.java`에서 참조되는 일부 클래스를 가져와야 합니다.

`Alt`+`Enter`(Mac에서는 `Option`+`Return`)를 누르면 해당 클래스를 자동으로 가져올 수 있습니다. 

**‘앱’ 실행** 단추(Mac에서는 `Control`+`R`)를 클릭하여 애플리케이션을 빌드합니다.

![[실행]을 클릭합니다.](./media/how-to-use-android-map-control-library/run-app.png)

Android Studio에서 애플리케이션을 빌드하는 데 몇 초 정도 걸립니다. 빌드가 완료되면 에뮬레이트된 Android 디바이스에서 애플리케이션을 테스트할 수 있습니다. 아래와 같은 지도가 표시됩니다.

![Android 지도](./media/how-to-use-android-map-control-library/android-map.png)

## <a name="add-a-marker-to-the-map"></a>지도에 표식 추가

지도에 표식을 추가하려면 `MainActivity.java`에 `mapView.getMapAsync()` 함수를 추가합니다. 최종 `MainActivity.java`는 다음과 같아야 합니다.

```java
package com.example.myapplication;

import android.app.Activity;
import android.os.Bundle;
import com.mapbox.geojson.Feature;
import com.mapbox.geojson.Point;
import com.microsoft.azure.maps.mapcontrol.AzureMaps;
import com.microsoft.azure.maps.mapcontrol.MapControl;
import com.microsoft.azure.maps.mapcontrol.layer.SymbolLayer;
import com.microsoft.azure.maps.mapcontrol.source.DataSource;
import static com.microsoft.azure.maps.mapcontrol.options.SymbolLayerOptions.iconImage;
public class MainActivity extends AppCompatActivity {
    
    static{
            AzureMaps.setSubscriptionKey("{subscription-key}");
        }

    MapControl mapControl;
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        mapControl = findViewById(R.id.mapcontrol);

        mapControl.onCreate(savedInstanceState);

        mapControl.getMapAsync(map -> {
            DataSource dataSource = new DataSource();
            dataSource.add(Feature.fromGeometry(Point.fromLngLat(-122.33, 47.64)));

            SymbolLayer symbolLayer = new SymbolLayer(dataSource);
            symbolLayer.setOptions(iconImage("my-icon"));

            map.images.add("my-icon", R.drawable.mapcontrol_marker_red);
            map.sources.add(dataSource);
            map.layers.add(symbolLayer);
        });
    }

    @Override
    public void onStart() {
        super.onStart();
        mapControl.onStart();
    }

    @Override
    public void onResume() {
        super.onResume();
        mapControl.onResume();
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

애플리케이션을 다시 실행하면 아래와 같이 지도에 표식이 나타납니다.

![Android 지도 핀](./media/how-to-use-android-map-control-library/android-map-pin.png)