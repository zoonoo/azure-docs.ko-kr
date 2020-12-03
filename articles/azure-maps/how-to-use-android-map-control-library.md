---
title: Azure Maps Android SDK 시작
description: Microsoft Azure Maps Android SDK에 대해 잘 알고 있어야 합니다. Android Studio에서 프로젝트를 만들고, SDK를 설치 하 고, 대화형 지도를 만드는 방법을 참조 하세요.
author: anastasia-ms
ms.author: v-stharr
ms.date: 11/18/2020
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: 1da003bb8d285dbedde87cbc6cd4708fda2dc38b
ms.sourcegitcommit: 5b93010b69895f146b5afd637a42f17d780c165b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/02/2020
ms.locfileid: "96531264"
---
# <a name="getting-started-with-azure-maps-android-sdk"></a>Azure Maps Android SDK 시작

Azure Maps Android SDK는 Android용 벡터 지도 라이브러리입니다. 이 문서에서는 Azure Maps Android SDK를 설치하고 맵을 로드하는 프로세스를 안내합니다.

## <a name="prerequisites"></a>사전 요구 사항

### <a name="create-an-azure-maps-account"></a>Azure Maps 계정 만들기

1. [Azure Maps 계정을 만듭니다](quick-demo-map-app.md#create-an-azure-maps-account).
2. 기본 키 또는 구독 키라고도 하는 [기본 구독 키를 가져옵니다](quick-demo-map-app.md#get-the-primary-key-for-your-account).
Azure Maps의 인증에 대한 자세한 내용은 [Azure Maps의 인증 관리](./how-to-manage-authentication.md)를 참조하세요.
3. [Google의 Android Studio를 다운로드 하 여 설치](https://developer.android.com/studio/)합니다.

## <a name="create-a-project-in-android-studio"></a>Android Studio에서 프로젝트 만들기

Android Studio 프로젝트를 만들려면 다음 단계를 완료합니다.

1. Android Studio를 시작합니다.
2. **+ 새 프로젝트 만들기** 를 클릭 합니다.
3. **휴대폰 및 태블릿** 탭에서 **빈 작업** 을 클릭 합니다. **다음** 을 클릭합니다.
4. **프로젝트 구성** 에서 최소 SDK로 `API 21: Android 5.0.0 (Lollipop)`을 선택합니다.
5. 를 `Java` 언어로 선택 합니다.
6. `Name`프로젝트에 대 한 기본값을 적용 합니다. **Finish** 를 클릭합니다.

Android Studio를 설치하고 새 프로젝트를 만드는 방법에 대한 자세한 도움말은 [Android Studio 문서](https://developer.android.com/studio/intro/)를 참조하세요.

![Android Studio에서 프로젝트 만들기 ](./media/how-to-use-android-map-control-library/form-factor-android.png)

## <a name="set-up-a-device"></a>장치 설정

개발 하는 동안 응용 프로그램을 테스트 하려면 Android 휴대폰 또는 Android 에뮬레이터를 사용할 수 있습니다.

AVD (Android 가상 장치)를 설정 하는 방법에 대 한 자세한 내용은 [Android Studio 설명서](https://developer.android.com/studio/run/managing-avds)를 참조 하세요.

## <a name="install-the-azure-maps-android-sdk"></a>Azure Maps Android SDK 설치

애플리케이션을 빌드하는 다음 단계에서 Azure Maps Android SDK를 설치합니다.

SDK를 설치하려면 다음 단계를 완료합니다.

1. 프로젝트 탭에서 **Gradle Scripts** 를 확장 합니다. **Gradle (Project: My_Application)** 를 열고 **모든 프로젝트** 섹션에 다음 코드를 추가 합니다 `repositories` .

    ```
    maven {
            url "https://atlas.microsoft.com/sdk/android"
    }
    ```

2. **Gradle (Module: My_Application)** 를 엽니다.

3. 섹션의 **매니페스트의 minsdkversion** `defaultConfig` 이 API 21 이상에 있는지 확인 합니다.

4. Android 섹션에 다음 코드를 추가합니다.

    ```
    compileOptions {
        sourceCompatibility JavaVersion.VERSION_1_8
        targetCompatibility JavaVersion.VERSION_1_8
    }
    ```

5. `dependencies` 섹션에 다음 코드를 추가합니다.

    ```
    implementation "com.microsoft.azure.maps:mapcontrol:0.6"
    ```

6. 주 도구 모음에서 **파일** 을 클릭 한 다음 **Gradle 파일을 사용 하 여 프로젝트 동기화** 를 선택 합니다.

7. `res > layout > activity_main.xml`를 엽니다. `Code`오른쪽 위 모서리에서 보기를 클릭 합니다. 요소 내에 다음 XML을 추가 합니다 `<androidx.constraintlayout.widget.ConstraintLayout>` .
    
    ```XML
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

8. 파일에서 `java > com.example.myapplication > MainActivity.java` 다음을 수행 해야 합니다.

    * Azure Maps SDK에 대 한 가져오기를 추가 합니다.
    * Azure Maps 인증 정보를 설정 합니다.
    * **onCreate** 메서드에서 맵 컨트롤 인스턴스를 가져옵니다.

    모든 응용 프로그램 보기에 대 한 인증 정보를 추가할 필요가 없도록를 호출 하 여 인증 정보를 전역적으로 설정 `AzureMaps.setSubscriptionKey` 합니다. `AzureMaps.setAadProperties`Azure Active Directory를 사용 하 여 인증 하려는 경우를 호출할 수도 있습니다.

    지도 컨트롤은 MainActivity 클래스의 다음 수명 주기 메서드를 재정의 합니다. 이러한 메서드는 Android의 OpenGL 수명 주기를 관리 하는 일을 담당 합니다.

    * onCreate(Bundle)
    * onStart()
    * onResume()
    * onPause()
    * onStop()
    * onDestroy()
    * onSaveInstanceState(Bundle)
    * onLowMemory()

    다음과 같이 파일을 편집 합니다 `MainActivity.java` .

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

>[!WARNING]
>Android Studio 필요한 클래스를 가져올 수 없습니다.  따라서 코드에 확인할 수 없는 참조가 포함 됩니다. 필요한 클래스를 가져오려면 확인 되지 않은 각 참조를 마우스로 가리키고 `Alt + Enter` (Mac에서 옵션 + 반환)를 누릅니다.

Android Studio에서 애플리케이션을 빌드하는 데 몇 초 정도 걸립니다. 빌드가 완료되면 에뮬레이트된 Android 디바이스에서 애플리케이션을 테스트할 수 있습니다. 다음과 같은 맵이 표시됩니다.

:::image type="content" source="./media/how-to-use-android-map-control-library/android-map.png" border="true" alt-text="Android 애플리케이션의 Azure Maps":::

## <a name="localizing-the-map"></a>맵 지역화

Azure Maps Android SDK는 맵의 언어 및 국가별 설정을 설정 하는 세 가지 다른 방법을 제공 합니다.

1. AzureMaps 클래스에서 정적 메서드를 호출 하 여 언어 및 국가별 설정을 설정 합니다.

    ```Java
    static {
        //Set your Azure Maps Key.
        AzureMaps.setSubscriptionKey("<Your Azure Maps Key>");

        //Set the language to be used by Azure Maps.
        AzureMaps.setLanguage("fr-FR");

        //Set the regional view.
        AzureMaps.setLanguage("Auto");
    
    }
    ```

2. 지도 컨트롤 XML에서 언어 및 국가별 설정을 정의 합니다.

    ```XML
    <com.microsoft.azure.maps.mapcontrol.MapControl
        android:id="@+id/myMap"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        app:mapcontrol_language="fr-FR"
        app:mapcontrol_view="Auto"
        />
    ```

3. 지도 컨트롤에서 메서드를 호출 하 여 언어 및 국가별 설정을 설정 합니다. 이 옵션을 사용 하면 런타임 중에 설정을 변경할 수 있습니다.

    ```Java
    mapControl.onReady(map -> {
        map.setStyle(StyleOptions.language("fr-FR"));
        map.setStyle(StyleOptions.view("Auto"));
    
    });
    ```

언어가로 설정 된 Azure Maps 예제는 다음과 같습니다 `fr-FR` .

:::image type="content" source="./media/how-to-use-android-map-control-library/android-localization.png" border="true" alt-text="Azure Maps, 프랑스어로 레이블을 표시하는 맵 이미지":::

지원되는 언어 및 지역 보기의 전체 목록은 [여기](supported-languages.md)에 설명되어 있습니다.

## <a name="navigating-the-map"></a>맵 탐색

여러 가지 방식으로 맵을 확대/축소, 이동, 회전 및 피치할 수 있습니다. 다음에서 맵을 탐색하는 다양한 방법을 자세히 설명합니다.

**맵 확대/축소**

- 두 손가락으로 맵을 터치하고 두 손가락을 모으면 축소되고 두 손가락을 넓히면 확대됩니다.
- 한 수준 확대하려면 맵을 두 번 탭합니다.
- 두 손가락을 두 번 탭 하 여 지도를 한 수준 축소 합니다.
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
> [Android 맵에 도형 추가](./how-to-add-shapes-to-android-map.md)

> [!div class="nextstepaction"]
> [Android 맵에서 맵 스타일 변경](./set-android-map-styles.md)