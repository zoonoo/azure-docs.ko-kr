---
title: 안드로이드지도에 모양을 추가 | 마이크로소프트 Azure 지도
description: 이 문서에서는 Microsoft Azure 지도 Android SDK를 사용하여 맵에서 다른 모양을 렌더링하는 방법을 배웁니다.
author: philmea
ms.author: philmea
ms.date: 04/26/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 7a793ff35675c876dc429976ebee96887b12735a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335603"
---
# <a name="add-a-shape-to-a-map-using-azure-maps-android-sdk"></a>Azure 지도 Android SDK를 사용하여 맵에 셰이프 추가

이 문서에서는 Azure 지도 Android SDK를 사용하여 맵에서 셰이프를 렌더링하는 방법을 보여 주어집니다.

## <a name="prerequisites"></a>사전 요구 사항

이 문서에서 프로세스를 완료하려면 맵을 로드하려면 [Azure Maps Android SDK를](https://docs.microsoft.com/azure/azure-maps/how-to-use-android-map-control-library) 설치해야 합니다.


## <a name="add-a-line-to-the-map"></a>맵에 선 추가

**선 레이어를**사용하여 맵에 선을 추가하고 아래 단계를 수행하여 맵에 선을 추가할 수 있습니다.

1. **activity_main.xml에 > > 레이아웃을** 편집하여 아래와 같이 보입니다.

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
            app:mapcontrol_centerLat="40.743270"
            app:mapcontrol_centerLng="-74.004420"
            app:mapcontrol_zoom="12"
            />
    
    </FrameLayout>
    ```

2. 아래의 코드 조각을 **클래스의 onCreate()** 메서드에 `MainActivity.java` 복사합니다.

    ```Java
    mapControl.onReady(map -> {

        //Create a data source and add it to the map.
        DataSource dataSource = new DataSource();
        map.sources.add(dataSource);
    
        //Create a list of points.
        List<Point> points = Arrays.asList(
            Point.fromLngLat(-73.972340, 40.743270),
            Point.fromLngLat(-74.004420, 40.756800));
    
        //Create a LineString feature and add it to the data source.
        dataSource.add(LineString.fromLngLats(points));
    
        //Create a line layer and add it to the map.
        map.layers.add(new LineLayer(dataSource,
            strokeColor("blue"),
            strokeWidth(5f)));
    });

    ```
    
    위의 코드 코드 조각은 먼저 **onReady()** 콜백 메서드를 사용하여 Azure Maps 맵 제어 인스턴스를 가져옵니다. 그런 다음 **DataSource** 클래스를 사용하여 데이터 원본 개체를 만들고 맵에 추가합니다. 그런 다음 **Point** 개체 목록을 만듭니다. **LineString은** 점 목록에서 만들어지고 데이터 원본에 추가됩니다. **선 레이어는** 맵의 데이터 원본에 래핑된 선 오브젝트를 렌더링합니다. 그런 다음 선 레이어가 만들어지고 데이터 원본이 추가됩니다.

    위의 코드 조각을 추가 한 `MainActivity.java` 후 아래 코드 조각과 같아야합니다.
    
    ```Java
    package com.example.myapplication;

    import android.app.Activity;
    import android.os.Bundle;
    import com.mapbox.geojson.LineString;
    import com.mapbox.geojson.Point;
    import android.support.v7.app.AppCompatActivity;
    import com.microsoft.azure.maps.mapcontrol.layer.LineLayer;
    import com.microsoft.azure.maps.mapcontrol.options.LineLayerOptions;
    import com.microsoft.azure.maps.mapcontrol.source.DataSource;
    import java.util.Arrays;
    import java.util.List;
    import com.microsoft.azure.maps.mapcontrol.AzureMaps;
    import com.microsoft.azure.maps.mapcontrol.MapControl;
    import static com.microsoft.azure.maps.mapcontrol.options.LineLayerOptions.strokeColor;
    import static com.microsoft.azure.maps.mapcontrol.options.LineLayerOptions.strokeWidth;
        
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
            
                //Create a list of points.
                List<Point> points = Arrays.asList(
                    Point.fromLngLat(-73.972340, 40.743270),
                    Point.fromLngLat(-74.004420, 40.756800));
            
                //Create a LineString feature and add it to the data source.
                dataSource.add(LineString.fromLngLats(points));
            
                //Create a line layer and add it to the map.
                map.layers.add(new LineLayer(dataSource,
                    strokeColor("blue"),
                    strokeWidth(5f)));
            });    
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

지금 응용 프로그램을 실행하는 경우 아래와 같이 지도에 줄이 표시됩니다.

<center>

![Android 맵에서 렌더링된 선](./media/how-to-add-shapes-to-android-map/android-map-line.png)</center>


## <a name="add-a-polygon-to-the-map"></a>맵에 다각형 추가

**다각형 레이어를** 사용하면 다각형 영역을 맵에 렌더링할 수 있습니다. 아래 단계에 따라 맵에 다각형을 추가합니다.

1. **activity_main.xml에 > > 레이아웃을** 편집하여 아래와 같이 보입니다.

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
            app:mapcontrol_centerLat="40.78"
            app:mapcontrol_centerLng="-73.97"
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
    
        //Create a list of point arrays to create polygon rings.
        List<List<Point>> rings = Arrays.asList(Arrays.asList(
            Point.fromLngLat(-73.98235, 40.76799),
            Point.fromLngLat(-73.95785, 40.80044),
            Point.fromLngLat(-73.94928, 40.7968),
            Point.fromLngLat(-73.97317, 40.76437),
            Point.fromLngLat(-73.98235, 40.76799)));
    
        //Create a Polygon feature and add it to the data source.
        dataSource.add(Feature.fromGeometry(Polygon.fromLngLats(rings)));
    
        //Add a polygon layer for rendering the polygon area.
        map.layers.add(new PolygonLayer(dataSource,
            fillColor("red")));
    
        //Add a line layer for rendering the polygon outline.
        map.layers.add(new LineLayer(dataSource,
            strokeColor("blue"),
            strokeWidth(2f)));
    });
    ```

    위의 코드 코드 조각은 먼저 **onReady()** 콜백 메서드를 사용하여 Azure Maps 맵 제어 인스턴스를 가져옵니다. 그런 다음 **DataSource** 클래스를 사용하여 데이터 원본 개체를 만들고 맵에 추가합니다. 그런 다음 **다각형** 개체가 **포인트** 개체 목록에서 만들어지고 데이터 원본에 추가됩니다. **다각형 레이어는** 맵의 데이터 원본에 래핑된 데이터를 렌더링합니다. 그런 다음 다각형 레이어를 만들어 다각형 영역을 렌더링하고 데이터 원본을 추가합니다. **선 레이어는** 데이터 원본에 래핑된 선 오브젝트를 렌더링합니다. 코드 조각의 마지막 부분에서는 다각형의 윤곽선을 렌더링하는 선 레이어를 만들고 데이터 원본을 추가합니다.

    위의 코드 조각을 추가 한 `MainActivity.java` 후 아래 코드 조각과 같아야합니다.

    ```Java
    package com.example.myapplication;
    
    import android.app.Activity;
    import android.os.Bundle;
    import java.util.Arrays;
    import android.util.Log;
    import java.util.Collections;
    import android.support.v7.app.AppCompatActivity;
    import com.mapbox.geojson.Point;
    import com.mapbox.geojson.Polygon;
    import com.mapbox.geojson.Feature;
    import com.microsoft.azure.maps.mapcontrol.layer.LineLayer;
    import com.microsoft.azure.maps.mapcontrol.options.LineLayerOptions;
    import com.microsoft.azure.maps.mapcontrol.layer.PolygonLayer;
    import com.microsoft.azure.maps.mapcontrol.source.DataSource;
    import com.microsoft.azure.maps.mapcontrol.AzureMaps;
    import com.microsoft.azure.maps.mapcontrol.MapControl;
    import static com.microsoft.azure.maps.mapcontrol.options.LineLayerOptions.strokeColor;
    import static com.microsoft.azure.maps.mapcontrol.options.LineLayerOptions.strokeWidth;
    import static com.microsoft.azure.maps.mapcontrol.options.PolygonLayerOptions.fillColor;
    
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
            
                //Create a list of point arrays to create polygon rings.
                List<List<Point>> rings = Arrays.asList(Arrays.asList(
                    Point.fromLngLat(-73.98235, 40.76799),
                    Point.fromLngLat(-73.95785, 40.80044),
                    Point.fromLngLat(-73.94928, 40.7968),
                    Point.fromLngLat(-73.97317, 40.76437),
                    Point.fromLngLat(-73.98235, 40.76799)));
            
                //Create a Polygon feature and add it to the data source.
                dataSource.add(Feature.fromGeometry(Polygon.fromLngLats(rings)));
            
                //Add a polygon layer for rendering the polygon area.
                map.layers.add(new PolygonLayer(dataSource,
                    fillColor("red")));
            
                //Add a line layer for rendering the polygon outline.
                map.layers.add(new LineLayer(dataSource,
                    strokeColor("blue"),
                    strokeWidth(2f)));
            });    
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

지금 응용 프로그램을 실행하는 경우 아래와 같이 지도에 다각형이 표시됩니다.

<center>

![안드로이드 맵에서 렌더링된 다각형](./media/how-to-add-shapes-to-android-map/android-map-polygon.png)</center>


## <a name="next-steps"></a>다음 단계

맵에 더 많은 데이터를 추가하려면 다음 을 수행합니다.

> [!div class="nextstepaction"]
> [기호 레이어 추가](how-to-add-symbol-to-android-map.md)

> [!div class="nextstepaction"]
> [타일 계층 추가](how-to-add-tile-layer-android-map.md)

> [!div class="nextstepaction"]
> [기능 정보 표시](display-feature-information-android.md)