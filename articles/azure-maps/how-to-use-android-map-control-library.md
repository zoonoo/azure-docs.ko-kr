---
title: Azure Maps에서 Android 맵 컨트롤을 사용 하는 방법 | Microsoft Docs
description: Azure Maps에서 Android 지도 컨트롤입니다.
author: walsehgal
ms.author: v-musehg
ms.date: 02/12/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: 15706addbe6b7f6310223978130158c792a47c89
ms.sourcegitcommit: 15e9613e9e32288e174241efdb365fa0b12ec2ac
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/28/2019
ms.locfileid: "57010670"
---
# <a name="how-to-use-the-azure-maps-android-sdk"></a>Azure Maps Android SDK를 사용 하는 방법

Azure Maps Android SDK는 Android에 대 한 벡터 맵 라이브러리. 이 문서에서는 Azure Maps Android SDK를 설치 하 고 맵을 로드 pin 지도에 배치 프로세스를 안내 합니다.

## <a name="prerequisites"></a>필수 조건

### <a name="create-an-azure-maps-account"></a>Azure Maps 계정 만들기

이 문서의 절차를 완료 하려면 먼저 해야 [Azure Maps 계정 만들기](how-to-manage-account-keys.md) S1 가격 책정 계층에서입니다.

### <a name="download-android-studio"></a>Android Studio 다운로드

Android Studio를 다운로드 하 고 Azure Maps Android SDK를 설치 하기 전에 빈 활동을 사용 하 여 프로젝트를 만들려면 해야 합니다. 할 수 있습니다 [Android Studio 다운로드](https://developer.android.com/studio/) Google에서 무료입니다. 

## <a name="create-a-project-in-android-studio"></a>Android Studio에서 프로젝트 만들기

먼저 빈 활동을 사용 하 여 새 프로젝트를 만들 해야 합니다. Android Studio 프로젝트를 만들려면 다음이 단계를 완료 합니다.

1. 아래 **프로젝트를 선택**를 선택 **휴대폰 및 태블릿**합니다. 응용 프로그램은이 폼 팩터에서에서 실행 됩니다.
2. 에 **휴대폰 및 태블릿** 탭을 선택 **빈 활동**를 선택한 후 **다음**합니다.
3. 프로젝트 구성에서 최소 SDK로 `API 21: Android 5.0.0 (Lollipop)`을 선택합니다.**** Azure Maps Android SDK에서 지 원하는 가장 빠른 버전입니다.
4. 기본값을 그대로 `Activity Name` 하 고 `Layout Name` 선택한 **마침**합니다.

참조 된 [Android Studio 설명서](https://developer.android.com/studio/intro/) 자세한 도움이 Android Studio를 설치 하 고 새 프로젝트 만들기.

![프로젝트 만들기](./media/how-to-use-android-map-control-library/form-factor-android.png)

## <a name="set-up-a-virtual-device"></a>가상 디바이스 설정

Android Studio를 사용하면 컴퓨터에 가상 Android 디바이스를 설정할 수 있습니다. 이렇게 개발 중에 응용 프로그램을 테스트할 수 있습니다. 가상 장치를 설정 하려면 프로젝트 화면의 오른쪽 위 모서리에 있는 가상 AVD (Android 장치) 관리자 아이콘을 선택 하 고 선택한 **가상 장치 만들기**합니다. AVD Manager를 선택 하 여 가져올 수도 있습니다 **도구가** > **Android** > **AVD Manager** 도구 모음에서 합니다. 에 **휴대폰** 범주를 선택한 **Nexus 5 배**를 선택한 후 **다음**합니다.

AVD를 설정 하는 방법에 대 한 자세히 알아볼 수 있습니다 합니다 [Android Studio 설명서](https://developer.android.com/studio/run/managing-avds)합니다.

![Android Emulator](./media/how-to-use-android-map-control-library/android-emulator.png)

## <a name="install-the-azure-maps-android-sdk"></a>Azure Maps Android SDK 설치

응용 프로그램을 빌드한 다음 단계는 Azure Maps Android SDK를 설치 하는 것입니다. SDK를 설치 하려면 다음이 단계를 완료 합니다.

1. 다음 코드를 추가 합니다 **모든 프로젝트**를 **리포지토리** 블록에 **build.gradle** 파일입니다.

    ```
    maven {
            url "https://atlas.microsoft.com/sdk/android"
    }
    ```

2. 업데이트 프로그램 **app/build.gradle** 다음 코드를 추가 합니다.

    1. Android 블록에 다음 코드를 추가 합니다.

        ```
        compileOptions {
            sourceCompatibility JavaVersion.VERSION_1_8
            targetCompatibility JavaVersion.VERSION_1_8
        }
        ```
    2. 종속성 블록을 업데이트 하 고 다음 코드를 추가 합니다.

        ```
        implementation "com.microsoft.azure.maps:mapcontrol:0.1"
        ```

3. 다음 XML을 추가 하 여 사용 권한을 설정 하면 **AndroidManifest.xml** 파일:

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <manifest>
        ...
        <uses-permission android:name="android.permission.ACCESS_FINE_LOCATION" />
        ...
    </manifest>
    ```

4. 편집할 **res** > **레이아웃** > **activity_main.xml** 이 XML 같이 표시 됩니다.
    
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

5. **MainActivity.java**를 편집하여 지도 보기 작업 클래스를 만듭니다. 을 편집 하면이 클래스와 같아야 합니다.

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

앞의 단계를 완료 하면 코드의 일부에 대 한 Android Studio에서 경고를 아마도 얻을 수 있습니다. 이러한 경고를 해결 하려면 참조 클래스 가져오기 `MainActivity.java`합니다.

(옵션 + 반환 Mac에서) Alt + Enter를 선택 하 여 이러한 클래스를 자동으로 가져올 수 있습니다.

다음 그래픽 (또는 Mac에서 컨트롤 + R 키를 눌러)에서 응용 프로그램을 빌드하려면 표시 된 것과 같이 실행된 단추를 선택 합니다.

![[실행]을 클릭합니다.](./media/how-to-use-android-map-control-library/run-app.png)

Android Studio에는 응용 프로그램을 빌드하려면 몇 초가 걸립니다. 빌드를 완료 한 후 에뮬레이트된 Android 장치에서 응용 프로그램을 테스트할 수 있습니다. 다음과 같은 맵을 표시 됩니다.

![Android 지도](./media/how-to-use-android-map-control-library/android-map.png)

## <a name="add-a-marker-to-the-map"></a>지도에 표식 추가

표식 지도 추가 하려면 추가 합니다 `mapView.getMapAsync()` 함수를 `MainActivity.java`입니다. 최종 `MainActivity.java` 코드 다음과 같아야 합니다.

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

응용 프로그램을 다시 실행 합니다. 다음과 같이 표식 지도에 표시 됩니다.

![Android 지도 핀](./media/how-to-use-android-map-control-library/android-map-pin.png)