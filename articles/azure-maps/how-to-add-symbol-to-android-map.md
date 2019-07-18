---
title: Azure Maps에서 Android에 대 한 기호 계층을 지도 추가 | Microsoft Docs
description: Azure Maps Android SDK를 사용 하 여 지도에 기호를 추가 하는 방법
author: walsehgal
ms.author: v-musehg
ms.date: 04/26/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: add6e23d023753e217c102dc946837a71a64c781
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "64871079"
---
# <a name="add-a-symbol-layer-to-a-map-using-azure-maps-android-sdk"></a>Azure Maps Android SDK를 사용 하 여 지도에 기호가 계층을 추가 합니다.

이 문서에서는 Azure Maps Android SDK를 사용 하 여 지도에 기호가 계층으로 데이터 원본에서 지점 데이터를 렌더링 하는 방법을 보여 줍니다.

## <a name="prerequisites"></a>필수 조건

이 문서의 단계를 완전히 수행 하려면 설치 해야 [Azure Maps Android SDK](https://docs.microsoft.com/azure/azure-maps/how-to-use-android-map-control-library) 지도 로드 합니다.

## <a name="add-a-symbol-layer"></a>기호 계층 추가

표식 기호 계층을 사용 하 여 지도 추가 하려면 아래 단계를 따르세요.

1. 편집할 **res** > **레이아웃** > **activity_main.xml** 다음 XML 같이 표시 됩니다.
    
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
            app:mapcontrol_centerLat="47.64"
            app:mapcontrol_centerLng="-122.33"
            app:mapcontrol_zoom="12"
            />

    </FrameLayout>
    ```

2. 다음 코드 조각을 복사 합니다 **onCreate()** 메서드의 프로그램 `MainActivity.java` 클래스입니다.

    ```Java
    mapControl.onReady(map -> {
    
        //Create a data source and add it to the map.
        DataSource dataSource = new DataSource();
        map.sources.add(dataSource);
    
        //Create a point feature and add it to the data source.
        dataSource.add(Feature.fromGeometry(Point.fromLngLat(-122.33, 47.64)));
    
        //Add a custom image icon to the map resources.
        map.images.add("my-icon", R.drawable.mapcontrol_marker_red);
    
        //Create a symbol layer and add it to the map.
        map.layers.add(new SymbolLayer(dataSource,
            iconImage("my-icon")));
        });
    
    ```
    
    위의 코드는 Azure Maps 맵 컨트롤 인스턴스를 사용 하 여 먼저 가져옵니다 합니다 **onReady()** 콜백 메서드입니다. 그런 다음 사용 하 여 데이터 원본 개체를 만듭니다는 **DataSource** 지도에 추가 하는 클래스입니다. 그런 다음 추가 **기능** 지점 geometry를 포함 합니다. 빨간색 표식 이미지의 기호에 대 한 아이콘으로 설정 됩니다. A **기호 계층** 텍스트 또는 아이콘을 사용 하 여 데이터 원본 맵에서 기호로 래핑됩니다 점 기반 데이터를 렌더링 합니다. 기호 계층 만들어집니다 및 데이터 소스를 렌더링 하려면에 전달 되는 지도 계층에 추가 되 고 있습니다.
    
    위의 코드 조각을 추가한 후에 `MainActivity.java` 아래와 같습니다.
    
    ```Java
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
                AzureMaps.setSubscriptionKey("<Your Azure Maps subscription key>");
            }
    
        MapControl mapControl;
        @Override
        protected void onCreate(Bundle savedInstanceState) {
            super.onCreate(savedInstanceState);
            setContentView(R.layout.activity_main);
    
            mapControl = findViewById(R.id.mapcontrol);
    
            mapControl.onCreate(savedInstanceState);
    
            mapControl.onReady(map -> {
    
                //Create a data source and add it to the map.
                DataSource dataSource = new DataSource();
                map.sources.add(dataSource);
            
                //Create a point feature and add it to the data source.
                dataSource.add(Feature.fromGeometry(Point.fromLngLat(-122.33, 47.64)));
            
                //Add a custom image icon to the map resources.
                map.images.add("my-icon", R.drawable.mapcontrol_marker_red);
            
                //Create a symbol layer and add it to the map.
                map.layers.add(new SymbolLayer(dataSource,
                    iconImage("my-icon")));
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
    
이 시점에서 응용 프로그램을 실행 하는 경우 다음과 같이 지도에 표식을 표시 됩니다.

<center>

![Android 지도 핀](./media/how-to-add-symbol-to-android-map/android-map-pin.png)</center>


## <a name="next-steps"></a>다음 단계

지도 더 많은 대상을 추가 하려면 다음을 참조 하세요.

> [!div class="nextstepaction"]
> [Android 맵에 도형 추가](https://docs.microsoft.com/azure/azure-maps/how-to-add-shapes-to-android-map)