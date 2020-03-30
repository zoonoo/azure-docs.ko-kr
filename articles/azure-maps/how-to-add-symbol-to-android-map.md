---
title: 안드로이드지도에 기호 레이어추가 | 마이크로소프트 Azure 지도
description: 이 문서에서는 Microsoft Azure 지도 Android SDK를 사용하여 맵에 기호 레이어를 추가하여 맵에서 포인트 데이터를 렌더링하는 방법을 배웁니다.
author: philmea
ms.author: philmea
ms.date: 04/26/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 0eca73b5acae715283c05125181e12729ed5a772
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335578"
---
# <a name="add-a-symbol-layer-to-a-map-using-azure-maps-android-sdk"></a>Azure 지도 Android SDK를 사용하여 맵에 기호 레이어 추가

이 문서에서는 Azure Maps Android SDK를 사용하여 데이터 원본의 포인트 데이터를 맵의 심볼 레이어로 렌더링하는 방법을 보여 주어집니다.

## <a name="prerequisites"></a>사전 요구 사항

이 문서의 단계를 완전히 수행하려면 [Azure Maps Android SDK를](https://docs.microsoft.com/azure/azure-maps/how-to-use-android-map-control-library) 설치하여 맵을 로드해야 합니다.

## <a name="add-a-symbol-layer"></a>기호 계층 추가

기호 레이어를 사용하여 맵에 마커를 추가하려면 다음 단계를 따르십시오.

1. 다음 XML처럼 보이면**activity_main.xml을** **resres** > **레이아웃** > 편집합니다.
    
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

2. 다음 코드 조각을 **클래스의 onCreate()** 메서드에 `MainActivity.java` 복사합니다.

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
    
    위의 코드 코드 조각은 먼저 **onReady()** 콜백 메서드를 사용하여 Azure Maps 맵 제어 인스턴스를 가져옵니다. 그런 다음 **DataSource** 클래스를 사용하여 데이터 원본 개체를 만들고 맵에 추가합니다. 그런 다음 점 형상을 포함하는 **피쳐를** 추가합니다. 빨간색 마커 이미지는 기호의 아이콘으로 설정됩니다. **심볼 레이어는** 텍스트 또는 아이콘을 사용하여 데이터 원본에 래핑된 포인트 기반 데이터를 맵의 기호로 렌더링합니다. 그런 다음 심볼 레이어가 만들어지고 데이터 원본이 렌더링될 수 있도록 전달된 다음 맵의 레이어에 추가됩니다.
    
    위의 코드 조각을 추가 한 `MainActivity.java` 후 아래 코드 조각과 같아야합니다.
    
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
    
이 시점에서 응용 프로그램을 실행 하는 경우 다음과 같이 지도에 마커가 표시 됩니다.

<center>

![Android 지도 핀](./media/how-to-add-symbol-to-android-map/android-map-pin.png)</center>


## <a name="next-steps"></a>다음 단계

맵에 더 많은 물건을 추가하려면 다음을 참조하십시오.

> [!div class="nextstepaction"]
> [안드로이드 맵에 셰이프 추가](https://docs.microsoft.com/azure/azure-maps/how-to-add-shapes-to-android-map)

> [!div class="nextstepaction"]
> [기능 정보 표시](display-feature-information-android.md)