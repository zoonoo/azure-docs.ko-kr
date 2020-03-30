---
title: 안드로이드지도 컨트롤시작하기 | 마이크로소프트 Azure 지도
description: 이 문서에서는 마이크로소프트 Azure지도 안드로이드 SDK를 사용하여 안드로이드지도 컨트롤을 시작하는 방법을 배우게됩니다.
author: philmea
ms.author: philmea
ms.date: 04/26/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: 55bfb5030d0a168e7556240212fcd5f3be30a289
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335357"
---
# <a name="getting-started-with-azure-maps-android-sdk"></a>Azure 지도 Android SDK로 시작하기

Azure지도 안드로이드 SDK는 안드로이드에 대한 벡터지도 라이브러리입니다. 이 문서에서는 Azure Maps Android SDK를 설치하고 맵을 로드하는 프로세스를 안내합니다.

## <a name="prerequisites"></a>사전 요구 사항

### <a name="create-an-azure-maps-account"></a>Azure Maps 계정 만들기

이 문서의 절차를 완료하려면 먼저 S1 가격 책정 계층에서 [Azure Maps 계정을 만들고](quick-demo-map-app.md#create-an-account-with-azure-maps) 계정에 대한 기본 [키를 얻어야](quick-demo-map-app.md#get-the-primary-key-for-your-account) 합니다.

Azure Maps의 인증에 대한 자세한 내용은 [Azure Maps의 인증 관리](./how-to-manage-authentication.md)를 참조하세요.

### <a name="download-android-studio"></a>안드로이드 스튜디오 다운로드

Android Studio를 다운로드하고 Azure 지도 Android SDK를 설치하기 전에 빈 활동이 있는 프로젝트를 만듭니다. 당신은 구글에서 무료로 [안드로이드 스튜디오를 다운로드](https://developer.android.com/studio/) 할 수 있습니다. 

## <a name="create-a-project-in-android-studio"></a>Android Studio에서 프로젝트 만들기

먼저 빈 활동이 있는 새 프로젝트를 만듭니다. 다음 단계를 완료하여 Android Studio 프로젝트를 만듭니다.

1. **프로젝트 선택에서** **전화 및 태블릿을**선택합니다. 응용 프로그램은 이 폼 팩터에서 실행됩니다.
2. 전화 **및 태블릿** 탭에서 **빈 활동을**선택한 다음 **다음**을 선택합니다.
3. 프로젝트 구성에서 최소 SDK로 을 선택합니다. 이것은 Azure지도 안드로이드 SDK에서 지원하는 최초의 버전입니다.
4. 기본값을 `Activity Name` 수락하고 `Layout Name` **완료를 선택합니다.**

Android Studio 설치 및 새 프로젝트 만들기에 대한 자세한 내용은 [Android Studio 설명서를](https://developer.android.com/studio/intro/) 참조하십시오.

![안드로이드 스튜디오에서 프로젝트 만들기 ](./media/how-to-use-android-map-control-library/form-factor-android.png)

## <a name="set-up-a-virtual-device"></a>가상 디바이스 설정

Android Studio를 사용하면 컴퓨터에 가상 Android 디바이스를 설정할 수 있습니다. 이렇게 하면 개발 중에 응용 프로그램을 테스트하는 데 도움이 됩니다. 가상 장치를 설정하려면 프로젝트 화면의 오른쪽 상단 모서리에 있는 Android 가상 장치(AVD) 관리자 아이콘을 선택한 다음 **가상 장치 만들기를**선택합니다. 또한 도구 모음에서 **도구** > **안드로이드** > AVD 관리자를 선택하여**AVD 관리자에** 도착 할 수 있습니다. **휴대폰** 범주에서 **Nexus 5X를**선택한 다음 **다음을**선택합니다.

[Android Studio 설명서에서](https://developer.android.com/studio/run/managing-avds)AVD 설정에 대해 자세히 알아볼 수 있습니다.

![Android Emulator](./media/how-to-use-android-map-control-library/android-emulator.png)

## <a name="install-the-azure-maps-android-sdk"></a>Azure지도 안드로이드 SDK를 설치

응용 프로그램을 빌드하는 다음 단계는 Azure 지도 Android SDK를 설치하는 것입니다. 다음 단계를 완료하여 SDK를 설치합니다.

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
        implementation "com.microsoft.azure.maps:mapcontrol:0.2"
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

    지도 컨트롤에는 Android의 OpenGL 수명 주기를 관리하는 자체 수명 주기 메서드가 포함되어 있습니다. 이러한 수명 주기 메서드는 포함된 활동에서 직접 호출해야 합니다. 앱에서 맵 컨트롤의 수명 주기 메서드를 올바르게 호출하려면 맵 컨트롤이 포함된 Activity에서 다음 수명 주기 메서드를 재정의해야 합니다. 또한 각 맵 제어 메서드를 호출해야 합니다. 

    * 온 만들기(번들) 
    * 온스타트() 
    * @resume() 
    * onPause() 
    * 온스톱() 
    * 온Destroy() 
    * 온세이브인스턴스스테이트(번들) 
    * 온로우메모리() 

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

위의 단계를 완료하면 Android Studio에서 일부 코드에 대한 경고가 표시됩니다. 이러한 경고를 해결하려면 에서 `MainActivity.java`참조되는 클래스를 가져옵니다.

Alt+Enter(Mac에서 옵션+반환)를 선택하여 이러한 클래스를 자동으로 가져올 수 있습니다.

다음 그래픽(또는 Mac에서 Control+R 을 누르고 있음)과 같이 실행 단추를 선택하여 응용 프로그램을 빌드합니다.

![[실행]을 클릭합니다.](./media/how-to-use-android-map-control-library/run-app.png)

안드로이드 스튜디오는 응용 프로그램을 구축하는 데 몇 초 정도 걸릴 것입니다. 빌드가 완료되면 에뮬레이트된 Android 장치에서 응용 프로그램을 테스트할 수 있습니다. 다음과 같은 맵이 표시됩니다.

<center>

![안드로이드 응용 프로그램에서 Azure지도](./media/how-to-use-android-map-control-library/android-map.png)</center>

## <a name="localizing-the-map"></a>맵 지역화

Azure 지도 Android SDK는 맵의 언어 및 지역 보기를 설정하는 세 가지 방법을 제공합니다. 다음 코드는 언어를 프랑스어("fr-FR")로 설정하고 지역 보기를 "자동"으로 설정하는 방법을 보여 주며, 

첫 번째 옵션은 언어를 전달하고 정적 `AzureMaps` `setLanguage` 및 `setView` 메서드를 전역적으로 사용하여 클래스에 지역 정보를 보는 것입니다. 그러면 앱에 로드된 모든 Azure Maps 컨트롤에서 기본 언어 및 지역 보기가 설정됩니다.

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

다음은 언어가 "fr-FR"로 설정되고 지역 보기가 "자동"으로 설정된 Azure Maps의 예입니다.

<center>

![Azure 지도, 프랑스어로 레이블을 보여주는 지도 이미지](./media/how-to-use-android-map-control-library/android-localization.png)
</center>

지원되는 언어 및 지역 보기의 전체 목록은 [여기](supported-languages.md)에 설명되어 있습니다.

## <a name="next-steps"></a>다음 단계

맵에 오버레이 데이터를 추가하는 방법에 대해 알아봅니다.

> [!div class="nextstepaction"]
> [Android 맵에 기호 레이어 추가](how-to-add-symbol-to-android-map.md)

> [!div class="nextstepaction"]
> [안드로이드 맵에 셰이프 추가](https://docs.microsoft.com/azure/azure-maps/how-to-add-shapes-to-android-map)

> [!div class="nextstepaction"]
> [안드로이드지도에서지도 스타일을 변경](https://docs.microsoft.com/azure/azure-maps/set-android-map-styles)
