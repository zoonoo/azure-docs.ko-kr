---
title: Android 지도 컨트롤 시작 | Microsoft Azure Maps
description: 이 문서에서는 Microsoft Azure Maps Android SDK를 사용하여 Android 지도 컨트롤을 시작하는 방법에 대해 알아봅니다.
author: philmea
ms.author: philmea
ms.date: 04/26/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: accd0df2913abc4d82b6f14aaafd03f0f042ca91
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.contentlocale: ko-KR
ms.lasthandoff: 05/19/2020
ms.locfileid: "83648210"
---
# <a name="getting-started-with-azure-maps-android-sdk"></a>Azure Maps Android SDK 시작

Azure Maps Android SDK는 Android용 벡터 지도 라이브러리입니다. 이 문서에서는 Azure Maps Android SDK를 설치하고 맵을 로드하는 프로세스를 안내합니다.

## <a name="prerequisites"></a>사전 요구 사항

### <a name="create-an-azure-maps-account"></a>Azure Maps 계정 만들기

이 문서의 절차를 완료하려면 먼저 S1 가격 책정 계층에서 [Azure Maps 계정 만들기](quick-demo-map-app.md#create-an-account-with-azure-maps)를 수행한 다음 계정에 대한 [기본 키 가져오기](quick-demo-map-app.md#get-the-primary-key-for-your-account)를 수행해야 합니다.

Azure Maps의 인증에 대한 자세한 내용은 [Azure Maps의 인증 관리](./how-to-manage-authentication.md)를 참조하세요.

### <a name="download-android-studio"></a>Android Studio 다운로드

Azure Maps Android SDK를 설치하기 전에 Android Studio을 다운로드하고 빈 작업으로 프로젝트를 만듭니다. Google에서 [Android Studio를 다운로드](https://developer.android.com/studio/)할 수 있습니다. 

## <a name="create-a-project-in-android-studio"></a>Android Studio에서 프로젝트 만들기

우선, 빈 작업으로 새 프로젝트를 만듭니다. Android Studio 프로젝트를 만들려면 다음 단계를 완료합니다.

1. **프로젝트 선택**에서 **휴대폰 및 태블릿**을 선택합니다. 애플리케이션이 이 폼 팩터에서 실행됩니다.
2. **휴대폰 및 태블릿** 탭에서 **빈 작업**을 선택하고 **다음**을 선택합니다.
3. **프로젝트 구성**에서 최소 SDK로 `API 21: Android 5.0.0 (Lollipop)`을 선택합니다. 이는 Azure Maps Android SDK에서 지원하는 가장 초기 버전입니다.
4. 기본 `Activity Name` 및 `Layout Name`을 적용하고 **마침**을 선택합니다.

Android Studio를 설치하고 새 프로젝트를 만드는 방법에 대한 자세한 도움말은 [Android Studio 문서](https://developer.android.com/studio/intro/)를 참조하세요.

![Android Studio에서 프로젝트 만들기 ](./media/how-to-use-android-map-control-library/form-factor-android.png)

## <a name="set-up-a-virtual-device"></a>가상 디바이스 설정

Android Studio를 사용하면 컴퓨터에 가상 Android 디바이스를 설정할 수 있습니다. 이렇게 하면 개발 중에 애플리케이션을 테스트하는 데 도움이 됩니다. 가상 디바이스를 설정하려면 프로젝트 화면의 오른쪽 상단 모서리에 있는 AVD(Android 가상 디바이스) 관리자 아이콘을 선택한 다음 **가상 디바이스 만들기**를 선택합니다. 도구 모음에서 **도구** > **Android** > **AVD 관리자**를 선택하여 AVD 관리자가 될 수도 있습니다. **휴대폰** 범주에서 **Nexus 5X**를 선택한 후 **다음**을 선택합니다.

[Android Studio 문서](https://developer.android.com/studio/run/managing-avds)에서 AVD 설정 방법을 자세히 알아볼 수 있습니다.

![Android Emulator](./media/how-to-use-android-map-control-library/android-emulator.png)

## <a name="install-the-azure-maps-android-sdk"></a>Azure Maps Android SDK 설치

애플리케이션을 빌드하는 다음 단계에서 Azure Maps Android SDK를 설치합니다. SDK를 설치하려면 다음 단계를 완료합니다.

1. 최상위 **build.gradle** 파일을 열고, **all projects**, **repositories** 블록에 다음 코드를 추가합니다.

    ```
    maven {
            url "https://atlas.microsoft.com/sdk/android"
    }
    ```

2. **app/build.gradle**을 업데이트하고 다음 코드를 추가합니다.
    
    1. 프로젝트의 **minSdkVersion**이 API 21 이상인지 확인합니다.

    2. Android 섹션에 다음 코드를 추가합니다.

        ```
        compileOptions {
            sourceCompatibility JavaVersion.VERSION_1_8
            targetCompatibility JavaVersion.VERSION_1_8
        }
        ```
    3. 다음과 같이 종속성 블록을 업데이트하고 최신 Azure Maps Android SDK에 대한 새 구현 종속성 줄을 추가합니다.

        ```
        implementation "com.microsoft.azure.maps:mapcontrol:0.4"
        ```
    
    4. 도구 모음에서 **파일**로 이동한 다음, **Gradle 파일과 프로젝트 동기화**를 클릭합니다.
3. 다음과 같이 기본 작업에 맵 조각을 추가합니다(res \> layout \> activity\_main.xml).
    
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

4. **MainActivity.java** 파일에서 다음을 수행해야 합니다.
    
    * Azure Maps SDK에 대한 가져오기 추가
    * Azure Maps 인증 정보 설정
    * **onCreate** 메서드에서 지도 컨트롤 인스턴스 가져오기

    `setSubscriptionKey` 또는 `setAadProperties` 메서드를 사용하여 `AzureMaps` 클래스에 대한 인증 정보를 전역적으로 설정하면 보기마다 인증 정보를 추가할 필요가 없습니다. 

    지도 컨트롤에는 Android의 OpenGL 수명 주기를 관리하는 자체 수명 주기 메서드가 포함되어 있습니다. 이러한 수명 주기 메서드는 포함된 작업에서 직접 호출해야 합니다. 앱에서 지도 컨트롤의 수명 주기 메서드를 올바르게 호출하려면 지도 컨트롤이 포함된 작업에서 다음 수명 주기 메서드를 재정의해야 합니다. 그리고 각 지도 컨트롤 메서드를 호출해야 합니다. 

    * onCreate(Bundle) 
    * onStart() 
    * onResume() 
    * onPause() 
    * onStop() 
    * onDestroy() 
    * onSaveInstanceState(Bundle) 
    * onLowMemory() 

    **MainActivity.java** 파일을 다음과 같이 편집합니다.
    
    ```java
    package com.example.myapplication;

    //For older versions use: import android.support.v7.app.AppCompatActivity; 
    import androidx.appcompat.app.AppCompatActivity;
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

## <a name="import-classes"></a>클래스 가져오기

위의 단계를 완료하면 일부 코드에 대한 Android Studio의 경고를 받을 수 있습니다. 이러한 경고를 해결하려면 `MainActivity.java`에서 참조되는 클래스를 가져옵니다.

Alt+Enter(Mac에서는 Option+Return)를 선택하여 이러한 클래스를 자동으로 가져올 수 있습니다.

다음 그래픽에 표시된 대로 실행 단추를 선택하여(Mac에서는 Control+R을 누름) 애플리케이션을 빌드합니다.

![[실행]을 클릭합니다.](./media/how-to-use-android-map-control-library/run-app.png)

Android Studio에서 애플리케이션을 빌드하는 데 몇 초 정도 걸립니다. 빌드가 완료되면 에뮬레이트된 Android 디바이스에서 애플리케이션을 테스트할 수 있습니다. 다음과 같은 맵이 표시됩니다.

<center>

![Android 애플리케이션의 Azure Maps](./media/how-to-use-android-map-control-library/android-map.png)</center>

## <a name="localizing-the-map"></a>맵 지역화

Azure Maps Android SDK는 맵의 언어 및 지역 보기를 설정하는 세 가지 방법을 제공합니다. 다음 코드에서는 언어를 프랑스어("fr-FR")로 설정하고 지역 보기를 "auto"로 설정하는 방법을 보여 줍니다. 

첫 번째 옵션은 정적 `setLanguage` 및 `setView` 메서드를 전역적으로 사용하여 `AzureMaps` 클래스에 언어를 전달하고 지역 정보를 보는 것입니다. 이렇게 하면 앱에 로드된 모든 Azure Maps 컨트롤에서 기본 언어 및 지역 보기가 설정됩니다.

```Java
static {
    //Set your Azure Maps Key.
    AzureMaps.setSubscriptionKey("<Your Azure Maps Key>");

    //Set the language to be used by Azure Maps.
    AzureMaps.setLanguage("fr-FR");

    //Set the regional view to be used by Azure Maps.
    AzureMaps.setView("auto");
}
```

두 번째 옵션은 언어 및 보기 정보를 지도 컨트롤 XML에 전달하는 것입니다.

```XML
<com.microsoft.azure.maps.mapcontrol.MapControl
    android:id="@+id/myMap"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    app:mapcontrol_language="fr-FR"
    app:mapcontrol_view="auto"
    />
```

세 번째 옵션은 맵 `setStyle` 메서드를 사용하여 맵의 언어 및 지역 보기를 프로그래밍 방식으로 설정하는 것입니다. 언제든지 이 작업을 수행하여 맵의 언어 및 지역 보기를 변경할 수 있습니다.

```Java
mapControl.onReady(map -> {
    map.setStyle(StyleOptions.language("fr-FR"));
    map.setStyle(StyleOptions.view("auto"));
});
```

다음은 언어가 "fr-FR"로 설정되고 지역 보기가 "auto"로 설정된 Azure Maps의 예입니다.

<center>

![Azure Maps, 프랑스어로 레이블을 표시하는 맵 이미지](./media/how-to-use-android-map-control-library/android-localization.png)
</center>

지원되는 언어 및 지역 보기의 전체 목록은 [여기](supported-languages.md)에 설명되어 있습니다.

## <a name="navigating-the-map"></a>맵 탐색

여러 가지 방식으로 맵을 확대/축소, 이동, 회전 및 피치할 수 있습니다. 다음에서 맵을 탐색하는 다양한 방법을 자세히 설명합니다.

**맵 확대/축소**

- 두 손가락으로 맵을 터치하고 두 손가락을 모으면 축소되고 두 손가락을 넓히면 확대됩니다.
- 한 수준 확대하려면 맵을 두 번 탭합니다.
- 두 손가락으로 두 번 탭하면 맵이 한 수준 축소됩니다.
- 두 번 탭할 때 두 번째 탭에서 손가락을 맵에 고정한 채로 위나 아래로 끌면 확대 또는 축소됩니다.

**맵 이동**

- 맵을 터치하고 원하는 방향으로 끕니다.

**맵 회전**

- 두 손가락으로 맵을 터치하고 회전합니다.

**맵 피치**

- 두 손가락으로 맵을 터치하고 위나 아래로 함께 끕니다.

## <a name="next-steps"></a>다음 단계

맵에 오버레이 데이터를 추가하는 방법을 알아봅니다.

> [!div class="nextstepaction"]
> [Android 맵에 기호 계층 추가](how-to-add-symbol-to-android-map.md)

> [!div class="nextstepaction"]
> [Android 맵에 도형 추가](https://docs.microsoft.com/azure/azure-maps/how-to-add-shapes-to-android-map)

> [!div class="nextstepaction"]
> [Android 맵에서 맵 스타일 변경](https://docs.microsoft.com/azure/azure-maps/set-android-map-styles)
