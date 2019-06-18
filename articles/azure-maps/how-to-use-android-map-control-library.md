---
title: Azure Maps에서 Android 맵 컨트롤을 사용 하 여 시작 | Microsoft Docs
description: Azure Maps에서 Android 지도 컨트롤입니다.
author: walsehgal
ms.author: v-musehg
ms.date: 04/26/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: e655b442ba9290d4b4525108521f2d1a0c766b48
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "64869810"
---
# <a name="getting-started-with-azure-maps-android-sdk"></a>Azure Maps Android SDK를 사용 하 여 시작

Azure Maps Android SDK는 Android에 대 한 벡터 맵 라이브러리. 이 문서에서는 Azure Maps Android SDK를 설치 하 고 맵을 로드 프로세스를 안내 합니다.

## <a name="prerequisites"></a>필수 조건

### <a name="create-an-azure-maps-account"></a>Azure Maps 계정 만들기

이 문서의 절차를 완료 하려면 먼저 해야 [Azure Maps 계정 만들기](how-to-manage-account-keys.md) S1 가격 책정 계층에서입니다.

### <a name="download-android-studio"></a>Android Studio 다운로드

Android Studio를 다운로드 하 고 Azure Maps Android SDK를 설치 하기 전에 빈 활동을 사용 하 여 프로젝트를 만들려면 해야 합니다. 할 수 있습니다 [Android Studio 다운로드](https://developer.android.com/studio/) Google에서 무료입니다. 

## <a name="create-a-project-in-android-studio"></a>Android Studio에서 프로젝트 만들기

먼저 빈 활동을 사용 하 여 새 프로젝트를 만들 해야 합니다. Android Studio 프로젝트를 만들려면 다음이 단계를 완료 합니다.

1. 아래 **프로젝트를 선택**를 선택 **휴대폰 및 태블릿**합니다. 응용 프로그램은이 폼 팩터에서에서 실행 됩니다.
2. 에 **휴대폰 및 태블릿** 탭을 선택 **빈 활동**를 선택한 후 **다음**합니다.
3. 프로젝트 구성에서 최소 SDK로 `API 21: Android 5.0.0 (Lollipop)`을 선택합니다. **** Azure Maps Android SDK에서 지 원하는 가장 빠른 버전입니다.
4. 기본값을 그대로 `Activity Name` 하 고 `Layout Name` 선택한 **마침**합니다.

참조 된 [Android Studio 설명서](https://developer.android.com/studio/intro/) 자세한 도움이 Android Studio를 설치 하 고 새 프로젝트 만들기.

![프로젝트 만들기](./media/how-to-use-android-map-control-library/form-factor-android.png)

## <a name="set-up-a-virtual-device"></a>가상 디바이스 설정

Android Studio를 사용하면 컴퓨터에 가상 Android 디바이스를 설정할 수 있습니다. 이렇게 개발 중에 응용 프로그램을 테스트할 수 있습니다. 가상 장치를 설정 하려면 프로젝트 화면의 오른쪽 위 모서리에 있는 가상 AVD (Android 장치) 관리자 아이콘을 선택 하 고 선택한 **가상 장치 만들기**합니다. AVD Manager를 선택 하 여 가져올 수도 있습니다 **도구가** > **Android** > **AVD Manager** 도구 모음에서 합니다. 에 **휴대폰** 범주를 선택한 **Nexus 5 배**를 선택한 후 **다음**합니다.

AVD를 설정 하는 방법에 대 한 자세히 알아볼 수 있습니다 합니다 [Android Studio 설명서](https://developer.android.com/studio/run/managing-avds)합니다.

![Android Emulator](./media/how-to-use-android-map-control-library/android-emulator.png)

## <a name="install-the-azure-maps-android-sdk"></a>Azure Maps Android SDK 설치

응용 프로그램을 빌드한 다음 단계는 Azure Maps Android SDK를 설치 하는 것입니다. SDK를 설치 하려면 다음이 단계를 완료 합니다.

1. 최상위 엽니다 **build.gradle** 파일을 다음 코드를 추가 합니다 **모든 프로젝트**, **리포지토리** 섹션을 차단:

    ```
    maven {
            url "https://atlas.microsoft.com/sdk/android"
    }
    ```

2. 업데이트 프로그램 **app/build.gradle** 다음 코드를 추가 합니다.
    
    1. 했는지 프로젝트의 **minSdkVersion** 또는 API 21 이상이 됩니다.

    2. Android 섹션에 다음 코드를 추가 합니다.

        ```
        compileOptions {
            sourceCompatibility JavaVersion.VERSION_1_8
            targetCompatibility JavaVersion.VERSION_1_8
        }
        ```
    3. 종속성 블록을 업데이트 하 고 최신 Azure Maps Android SDK의 새 구현을 종속성 줄을 추가 합니다.

        ```
        implementation "com.microsoft.azure.maps:mapcontrol:0.2"
        ```

    > [!Note]
    > Azure Maps Android SDK를 정기적으로 업그레이드 하 고 향상 합니다. 볼 수 있습니다 합니다 [Android 맵 컨트롤을 사용 하 여 시작](https://docs.microsoft.com/azure/azure-maps/how-to-use-android-map-control-library) 설명서, 최신 Azure Maps 구현 버전 번호를 가져옵니다. 또한 버전 번호를 "0.2"에서 "0 +" 항상 최신 버전을 가리키도록 설정할 수 있습니다.

3. 편집할 **res** > **레이아웃** > **activity_main.xml** 다음으로 바꿉니다.
    
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

4. 에 **MainActivity.java** 파일 해야 합니다.
    
    * Azure Maps SDK에 대 한 가져오기를 추가합니다
    * Azure Maps 인증 정보를 설정 합니다.
    * 맵 컨트롤 인스턴스를 참여 합니다 **onCreate** 메서드

    전역적으로 setSubscriptionKey 또는 setAadProperties 메서드를 사용 하 여 AzureMaps 클래스에 인증 정보를 설정 있도록 없으므로 모든 보기에서 인증 정보를 추가 하는 것이 없습니다. 맵 컨트롤을 포함 하는 작업에서 직접 호출 해야 하는 Android의 OpenGL 수명 주기를 관리 하기 위한 자체 수명 주기 메서드를 포함 합니다. 지도 컨트롤의 수명 주기 메서드를 올바르게 호출 하려면 앱에 대 한 순서 대로 맵 컨트롤을 포함 하는 작업의 다음 수명 주기 메서드를 재정의 하 고 해당 지도 컨트롤 메서드를 호출 해야 합니다. 

    편집 된 **MainActivity.java** 다음과 같이 파일:
    
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

<center>

![Android 맵](./media/how-to-use-android-map-control-library/android-map.png)</center>

## <a name="next-steps"></a>다음 단계

항목에 지도를 추가 하려면 다음을 참조 하세요.

> [!div class="nextstepaction"]
> [Android 맵에 기호 계층을 추가 합니다.](https://review.docs.microsoft.com/azure/azure-maps/how-to-add-symbol-to-android-map)

> [!div class="nextstepaction"]
> [Android 맵에 도형 추가](https://docs.microsoft.com/azure/azure-maps/how-to-add-shapes-to-android-map)

> [!div class="nextstepaction"]
> [Android maps에서 맵 스타일 변경](https://docs.microsoft.com/azure/azure-maps/set-android-map-styles)


