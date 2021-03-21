---
title: Android maps에 대 한 데이터 원본 만들기 | Microsoft Azure 맵
description: 지도의 데이터 원본을 만드는 방법에 대해 알아보세요. Azure Maps Android SDK에서 사용 하는 데이터 원본에 대해 알아봅니다. GeoJSON 원본 및 벡터 타일.
author: rbrundritt
ms.author: richbrun
ms.date: 2/26/2021
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
zone_pivot_groups: azure-maps-android
ms.openlocfilehash: e870134e2ecd431aa3e5c02638120027f0d47df2
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102101463"
---
# <a name="create-a-data-source-android-sdk"></a>데이터 원본 만들기 (Android SDK)

Azure Maps Android SDK 데이터 원본에 데이터를 저장 합니다. 데이터 원본을 사용 하 여 쿼리 및 렌더링을 위한 데이터 작업을 최적화 합니다. 현재 다음과 같은 두 가지 유형의 데이터 원본이 있습니다.

- **GeoJSON source**: 로컬에서 GeoJSON 형식의 원시 위치 데이터를 관리 합니다. 중소 규모의 데이터 집합에 적합 합니다 (수천 개의 셰이프).
- **벡터 타일 원본**: 지도 바둑판식 배열 시스템을 기반으로 현재 지도 보기의 벡터 타일로 형식이 지정 된 데이터를 로드 합니다. 대규모 데이터 집합 (수백만 또는 수십억 개의 도형)에 이상적입니다.

## <a name="geojson-data-source"></a>GeoJSON 데이터 원본

Azure Maps은 기본 데이터 모델 중 하나로 GeoJSON를 사용 합니다. GeoJSON는 지리 공간적 데이터를 JSON 형식으로 나타내는 데 사용할 수 있는 지리 공간적 표준 방식입니다. Azure Maps Android SDK에서 사용할 수 있는 GeoJSON 클래스를 사용 하 여 GeoJSON 데이터를 쉽게 만들고 serialize 할 수 있습니다. GeoJSON 데이터를 로드 하 고 클래스에 저장 `DataSource` 한 다음 레이어를 사용 하 여 렌더링 합니다. 다음 코드는 Azure Maps에서 GeoJSON 개체를 만드는 방법을 보여 줍니다.

::: zone pivot="programming-language-java-android"

```java
/*
    Raw GeoJSON feature
    
    {
         "type": "Feature",
         "geometry": {
             "type": "Point",
             "coordinates": [-100, 45]
         },
         "properties": {
             "custom-property": "value"
         }
    }

*/

//Create a point feature.
Feature feature = Feature.fromGeometry(Point.fromLngLat(-100, 45));

//Add a property to the feature.
feature.addStringProperty("custom-property", "value");

//Add the feature to the data source.
source.add(feature);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
/*
    Raw GeoJSON feature
    
    {
         "type": "Feature",
         "geometry": {
             "type": "Point",
             "coordinates": [-100, 45]
         },
         "properties": {
             "custom-property": "value"
         }
    }

*/

//Create a point feature.
val feature = Feature.fromGeometry(Point.fromLngLat(-100, 45))

//Add a property to the feature.
feature.addStringProperty("custom-property", "value")

//Add the feature to the data source.
source.add(feature)
```

::: zone-end

또는 아래와 같이 먼저 속성을 JsonObject로 로드 한 다음이를 만들 때 기능에 전달할 수 있습니다.

::: zone pivot="programming-language-java-android"

```java
//Create a JsonObject to store properties for the feature.
JsonObject properties = new JsonObject();
properties.addProperty("custom-property", "value");

Feature feature = Feature.fromGeometry(Point.fromLngLat(-100, 45), properties);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Create a JsonObject to store properties for the feature.
val properties = JsonObject()
properties.addProperty("custom-property", "value")

val feature = Feature.fromGeometry(Point.fromLngLat(-100, 45), properties)
```

::: zone-end

GeoJSON 기능을 만든 후에는 맵의 속성을 통해 지도에 데이터 원본을 추가할 수 있습니다 `sources` . 다음 코드에서는를 만들고 `DataSource` 맵에 추가 하 고 데이터 원본에 기능을 추가 하는 방법을 보여 줍니다.

```javascript
//Create a data source and add it to the map.
DataSource source = new DataSource();
map.sources.add(source);

//Add GeoJSON feature to the data source.
source.add(feature);
```

다음 코드에서는 GeoJSON 기능, FeatureCollection 및 geometry를 만드는 여러 가지 방법을 보여 줍니다.

::: zone pivot="programming-language-java-android"

```java
//GeoJSON Point Geometry
Point point = Point.fromLngLat(LONGITUDE, LATITUDE);

//GeoJSON Point Geometry
LineString linestring = LineString.fromLngLats(PointList);

//GeoJSON Polygon Geometry
Polygon polygon = Polygon.fromLngLats(listOfPointList);
 
Polygon polygonFromOuterInner = Polygon.fromOuterInner(outerLineStringObject,innerLineStringObject);

//GeoJSON MultiPoint Geometry
MultiPoint multiPoint = MultiPoint.fromLngLats(PointList);

//GeoJSON MultiLineString Geometry
MultiLineString multiLineStringFromLngLat = MultiLineString.fromLngLats(listOfPointList);

MultiLineString multiLineString = MultiLineString.fromLineString(singleLineString);

//GeoJSON MultiPolygon Geometry
MultiPolygon multiPolygon = MultiPolygon.fromLngLats(listOflistOfPointList);

MultiPolygon multiPolygonFromPolygon = MultiPolygon.fromPolygon(polygon);

MultiPolygon multiPolygonFromPolygons = MultiPolygon.fromPolygons(PolygonList);

//GeoJSON Feature
Feature pointFeature = Feature.fromGeometry(Point.fromLngLat(LONGITUDE, LATITUDE));
 
//GeoJSON FeatureCollection 
FeatureCollection featureCollectionFromSingleFeature = FeatureCollection.fromFeature(pointFeature);
 
FeatureCollection featureCollection = FeatureCollection.fromFeatures(listOfFeatures);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//GeoJSON Point Geometry
val point = Point.fromLngLat(LONGITUDE, LATITUDE)

//GeoJSON Point Geometry
val linestring = LineString.fromLngLats(PointList)

//GeoJSON Polygon Geometry
val polygon = Polygon.fromLngLats(listOfPointList)

val polygonFromOuterInner = Polygon.fromOuterInner(outerLineStringObject, innerLineStringObject)

//GeoJSON MultiPoint Geometry
val multiPoint = MultiPoint.fromLngLats(PointList)

//GeoJSON MultiLineString Geometry
val multiLineStringFromLngLat = MultiLineString.fromLngLats(listOfPointList)

val multiLineString = MultiLineString.fromLineString(singleLineString)

//GeoJSON MultiPolygon Geometry
val multiPolygon = MultiPolygon.fromLngLats(listOflistOfPointList)

val multiPolygonFromPolygon = MultiPolygon.fromPolygon(polygon)

val multiPolygonFromPolygons = MultiPolygon.fromPolygons(PolygonList)

//GeoJSON Feature
val pointFeature = Feature.fromGeometry(Point.fromLngLat(LONGITUDE, LATITUDE))

//GeoJSON FeatureCollection 
val featureCollectionFromSingleFeature = FeatureCollection.fromFeature(pointFeature)

val featureCollection = FeatureCollection.fromFeatures(listOfFeatures)
```

::: zone-end

### <a name="serialize-and-deserialize-geojson"></a>GeoJSON Serialize 및 deserialize

기능 컬렉션, 기능 및 geometry 클래스에는 모두 `fromJson()` `toJson()` serialization에 도움이 되는 정적 메서드가 있습니다. 메서드를 통해 전달 된 형식이 지정 된 유효한 JSON 문자열은 `fromJson()` geometry 개체를 만듭니다. `fromJson()`또한이 메서드는 Gson 또는 기타 serialization/deserialization 전략을 사용할 수 있음을 의미 합니다. 다음 코드에서는 문자열 형식 GeoJSON 기능을 사용 하 고이를 Feature 클래스로 deserialize 한 다음 다시 GeoJSON 문자열로 serialize 하는 방법을 보여 줍니다.

::: zone pivot="programming-language-java-android"

```java
//Take a stringified GeoJSON object.
String GeoJSON_STRING = "{"
    + "      \"type\": \"Feature\","            
    + "      \"geometry\": {"
    + "            \"type\": \"Point\""
    + "            \"coordinates\": [-100, 45]"
    + "      },"
    + "      \"properties\": {"
    + "            \"custom-property\": \"value\""
    + "      },"
    + "}";

//Deserialize the JSON string into a feature.
Feature feature = Feature.fromJson(GeoJSON_STRING);
 
//Serialize a feature collection to a string.
String featureString = feature.toJson();
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Take a stringified GeoJSON object.
val GeoJSON_STRING = ("{"
        + "      \"type\": \"Feature\","
        + "      \"geometry\": {"
        + "            \"type\": \"Point\""
        + "            \"coordinates\": [-100, 45]"
        + "      },"
        + "      \"properties\": {"
        + "            \"custom-property\": \"value\""
        + "      },"
        + "}")

//Deserialize the JSON string into a feature.
val feature = Feature.fromJson(GeoJSON_STRING)

//Serialize a feature collection to a string.
val featureString = feature.toJson()
```

::: zone-end

### <a name="import-geojson-data-from-web-or-assets-folder"></a>웹 또는 자산 폴더에서 GeoJSON 데이터 가져오기

대부분의 GeoJSON 파일은 FeatureCollection을 포함 합니다. GeoJSON 파일을 문자열로 읽고 메서드를 사용 `FeatureCollection.fromJson` 하 여 deserialize 합니다.

다음 코드는 웹 또는 로컬 자산 폴더에서 문자열로 데이터를 가져오고 콜백 함수를 통해 UI 스레드에 반환 하는 다시 사용할 수 있는 클래스입니다.

::: zone pivot="programming-language-java-android"

```java
import android.content.Context;
import android.os.Handler;
import android.os.Looper;
import android.webkit.URLUtil;

import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStream;
import java.io.InputStreamReader;
import java.net.URL;
import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;

import javax.net.ssl.HttpsURLConnection;

public class Utils {

    interface SimpleCallback {
        void notify(String result);
    }

    /**
     * Imports data from a web url or asset file name and returns it to a callback.
     * @param urlOrFileName A web url or asset file name that points to data to load.
     * @param context The context of the app.
     * @param callback The callback function to return the data to.
     */
    public static void importData(String urlOrFileName, Context context, SimpleCallback callback){
        importData(urlOrFileName, context, callback, null);
    }
    
    /**
     * Imports data from a web url or asset file name and returns it to a callback.
     * @param urlOrFileName A web url or asset file name that points to data to load.
     * @param context The context of the app.
     * @param callback The callback function to return the data to.
     * @param error A callback function to return errors to.
     */
    public static void importData(String urlOrFileName, Context context, SimpleCallback callback, SimpleCallback error){
        if(urlOrFileName != null && callback != null) {
            ExecutorService executor = Executors.newSingleThreadExecutor();
            Handler handler = new Handler(Looper.getMainLooper());

            executor.execute(() -> {
                String data = null;

                try {

                    if(URLUtil.isNetworkUrl(urlOrFileName)){
                        data = importFromWeb(urlOrFileName);
                    } else {
                        //Assume file is in assets folder.
                        data = importFromAssets(context, urlOrFileName);
                    }

                    final String result = data;

                    handler.post(() -> {
                        //Ensure the resulting data string is not null or empty.
                        if (result != null && !result.isEmpty()) {
                            callback.notify(result);
                        } else {
                            error.notify("No data imported.");
                        }
                    });
                } catch(Exception e) {
                    if(error != null){
                        error.notify(e.getMessage());
                    }
                }
            });
        }
    }

    /**
     * Imports data from an assets file as a string.
     * @param context The context of the app.
     * @param fileName The asset file name.
     * @return
     * @throws IOException
     */
    private static String importFromAssets(Context context, String fileName) throws IOException {
        InputStream stream = null;

        try {
            stream = context.getAssets().open(fileName);

            if(stream != null) {
                return readStreamAsString(stream);
            }
        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            // Close Stream and disconnect HTTPS connection.
            if (stream != null) {
                stream.close();
            }
        }

        return null;
    }

    /**
     * Imports data from the web as a string.
     * @param url URL to the data.
     * @return
     * @throws IOException
     */
    private static String importFromWeb(String url) throws IOException {
        InputStream stream = null;
        HttpsURLConnection connection = null;
        String result = null;

        try {
            connection = (HttpsURLConnection) new URL(url).openConnection();

            //For this use case, set HTTP method to GET.
            connection.setRequestMethod("GET");

            //Open communications link (network traffic occurs here).
            connection.connect();

            int responseCode = connection.getResponseCode();
            if (responseCode != HttpsURLConnection.HTTP_OK) {
                throw new IOException("HTTP error code: " + responseCode);
            }

            //Retrieve the response body as an InputStream.
            stream = connection.getInputStream();

            if (stream != null) {
                return readStreamAsString(stream);
            }
        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            // Close Stream and disconnect HTTPS connection.
            if (stream != null) {
                stream.close();
            }
            if (connection != null) {
                connection.disconnect();
            }
        }

        return result;
    }

    /**
     * Reads an input stream as a string.
     * @param stream Stream to convert.
     * @return
     * @throws IOException
     */
    private static String readStreamAsString(InputStream stream) throws IOException {
        //Convert the contents of an InputStream to a String.
        BufferedReader in = new BufferedReader(new InputStreamReader(stream, "UTF-8"));

        String inputLine;
        StringBuffer response = new StringBuffer();

        while ((inputLine = in.readLine()) != null) {
            response.append(inputLine);
        }

        in.close();

        return response.toString();
    }
}
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
import android.content.Context
import android.os.Handler
import android.os.Looper
import android.webkit.URLUtil
import java.net.URL
import java.util.concurrent.ExecutorService
import java.util.concurrent.Executors

class Utils {
    companion object {

        /**
            * Imports data from a web url or asset file name and returns it to a callback.
            * @param urlOrFileName A web url or asset file name that points to data to load.
            * @param context The context of the app.
            * @param callback The callback function to return the data to.
            */
        fun importData(urlOrFileName: String?, context: Context, callback: (String?) -> Unit) {
            importData(urlOrFileName, context, callback, null)
        }

        /**
            * Imports data from a web url or asset file name and returns it to a callback.
            * @param urlOrFileName A web url or asset file name that points to data to load.
            * @param context The context of the app.
            * @param callback The callback function to return the data to.
            * @param error A callback function to return errors to.
            */
        public fun importData(urlOrFileName: String?, context: Context, callback: (String?) -> Unit, error: ((String?) -> Unit)?) {
            if (urlOrFileName != null && callback != null) {
                val executor: ExecutorService = Executors.newSingleThreadExecutor()
                val handler = Handler(Looper.getMainLooper())
                executor.execute {
                    var data: String? = null
                    
                    try {
                        data = if (URLUtil.isNetworkUrl(urlOrFileName)) {
                            URL(urlOrFileName).readText()
                        } else { //Assume file is in assets folder.
                            context.assets.open(urlOrFileName).bufferedReader().use{
                                it.readText()
                            }
                        }

                        handler.post {
                            //Ensure the resulting data string is not null or empty.
                            if (data != null && !data.isEmpty()) {
                                callback(data)
                            } else {
                                error!!("No data imported.")
                            }
                        }
                    } catch (e: Exception) {
                        error!!(e.message)
                    }
                }
            }
        }
    }
}
```

::: zone-end

아래 코드에서는이 유틸리티를 사용 하 여 GeoJSON 데이터를 문자열로 가져오고 콜백을 통해 UI 스레드에 반환 하는 방법을 보여 줍니다. 콜백에서 문자열 데이터를 GeoJSON 기능 컬렉션으로 serialize 하 고 데이터 원본에 추가할 수 있습니다. 필요에 따라 지도 카메라를 업데이트 하 여 데이터에 초점을 맞출 수 있습니다.

::: zone pivot="programming-language-java-android"

```java
//Create a data source and add it to the map.
DataSource dataSource = new DataSource();
map.sources.add(dataSource);

//Import the geojson data and add it to the data source.
Utils.importData("URL_or_FilePath_to_GeoJSON_data",
    this,
    (String result) -> {
        //Parse the data as a GeoJSON Feature Collection.
        FeatureCollection fc = FeatureCollection.fromJson(result);

        //Add the feature collection to the data source.
        dataSource.add(fc);

        //Optionally, update the maps camera to focus in on the data.

        //Calculate the bounding box of all the data in the Feature Collection.
        BoundingBox bbox = MapMath.fromData(fc);

        //Update the maps camera so it is focused on the data.
        map.setCamera(
            bounds(bbox),
            padding(20));
    });
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Create a data source and add it to the map.
DataSource source = new DataSource();
map.sources.add(source);

//Import the GeoJSON data and add it to the data source.
Utils.importData("SamplePoiDataSet.json", this) { 
    result: String? ->
        //Parse the data as a GeoJSON Feature Collection.
            val fc = FeatureCollection.fromJson(result!!)

        //Add the feature collection to the data source.
        source.add(fc)

        //Optionally, update the maps camera to focus in on the data.

        //Calculate the bounding box of all the data in the Feature Collection.
        val bbox = MapMath.fromData(fc);

        //Update the maps camera so it is focused on the data.
        map.setCamera(
            bounds(bbox),

            //Padding added to account for pixel size of rendered points.
            padding(20)
        )
    }
```

::: zone-end

## <a name="vector-tile-source"></a>벡터 타일 원본

벡터 타일 소스는 벡터 타일 계층에 액세스 하는 방법을 설명 합니다. 클래스를 사용 `VectorTileSource` 하 여 벡터 타일 소스를 인스턴스화합니다. 벡터 타일 계층은 타일 계층과 비슷하지만 동일 하지는 않습니다. 타일 계층은 래스터 이미지입니다. 벡터 타일 계층은 압축 파일 ( **Pf** 형식)입니다. 이 압축 파일은 벡터 맵 데이터 및 하나 이상의 계층을 포함 합니다. 각 계층의 스타일에 따라 파일을 클라이언트에서 렌더링 하 고 스타일을 지정할 수 있습니다. 벡터 타일의 데이터에는 요소, 선 및 다각형 형식의 지리적 기능이 포함 되어 있습니다. 래스터 타일 계층 대신 벡터 타일 계층을 사용할 경우 다음과 같은 몇 가지 이점이 있습니다.

- 벡터 타일의 파일 크기는 일반적으로 해당 하는 래스터 타일 보다 훨씬 작습니다. 따라서 대역폭이 줄어듭니다. 낮은 대기 시간, 더 빠른 맵 및 더 나은 사용자 환경을 의미 합니다.
- 벡터 타일은 클라이언트에서 렌더링 되므로 표시 되는 장치의 해상도에 맞게 조정 됩니다. 따라서 렌더링 된 맵은 명확 하 고 명확한 레이블이 있는 잘 정의 된 것으로 나타납니다.
- 클라이언트에 새 스타일을 적용할 수 있으므로 벡터 맵에서 데이터의 스타일을 변경 하는 경우 데이터를 다시 다운로드할 필요가 없습니다. 반면 래스터 타일 계층의 스타일을 변경 하는 경우 일반적으로 서버에서 타일을 로드 한 다음 새 스타일을 적용 해야 합니다.
- 데이터가 벡터 형식으로 전달 되므로 데이터를 준비 하는 데 필요한 서버 쪽 처리가 줄어듭니다. 따라서 최신 데이터를 더 빨리 사용할 수 있습니다.

Azure Maps는 [Mapbox Vector 타일 사양](https://github.com/mapbox/vector-tile-spec)(개방형 표준)을 준수 합니다. Azure Maps는 플랫폼의 일부로 다음 벡터 타일 서비스를 제공 합니다.

- 도로 타일 [설명서](/rest/api/maps/renderv2/getmaptilepreview)  |  [데이터 형식 세부 정보](https://developer.tomtom.com/maps-api/maps-api-documentation-vector/tile)
- 트래픽 인시던트 [설명서](/rest/api/maps/traffic/gettrafficincidenttile)  |  [데이터 형식 세부 정보](https://developer.tomtom.com/traffic-api/traffic-api-documentation-traffic-incidents/vector-incident-tiles)
- 트래픽 흐름 [설명서](/rest/api/maps/traffic/gettrafficflowtile)  |  [데이터 형식 세부 정보](https://developer.tomtom.com/traffic-api/traffic-api-documentation-traffic-flow/vector-flow-tiles)
- Azure Maps 작성자는 또한 [Get 타일 렌더링 V2](/rest/api/maps/renderv2/getmaptilepreview) 를 통해 사용자 지정 벡터 타일을 만들고 액세스할 수 있습니다.

> [!TIP]
> 웹 SDK를 사용 하 여 Azure Maps render service에서 벡터 또는 래스터 이미지 타일을 사용 하는 경우를 `atlas.microsoft.com` 자리 표시자로 바꿀 수 있습니다 `azmapsdomain.invalid` . 이 자리 표시자는 맵에 사용 되는 동일한 도메인으로 바뀌고 동일한 인증 세부 정보도 자동으로 추가 됩니다. 이렇게 하면 Azure Active Directory 인증을 사용 하는 경우 렌더링 서비스에 대 한 인증이 매우 간단해 집니다.

지도에 벡터 타일 원본의 데이터를 표시 하려면 데이터 렌더링 계층 중 하나에 원본을 연결 합니다. 벡터 원본을 사용 하는 모든 계층은 옵션에 값을 지정 해야 합니다 `sourceLayer` . 다음 코드는 Azure Maps traffic flow vector 타일 서비스를 벡터 타일 원본으로 로드 한 다음 선 계층을 사용 하 여 지도에 표시 합니다. 이 벡터 타일 원본에는 원본 계층에서 "트래픽 흐름" 이라는 단일 데이터 집합이 있습니다. 이 데이터 집합의 줄 데이터에는 `traffic_level` 이 코드에서 색을 선택 하 고 선의 크기를 조정 하는 데 사용 되는 라는 속성이 있습니다.

::: zone pivot="programming-language-java-android"

```java
//Formatted URL to the traffic flow vector tiles, with the maps subscription key appended to it.
String trafficFlowUrl = "https://azmapsdomain.invalid/traffic/flow/tile/pbf?api-version=1.0&style=relative&zoom={z}&x={x}&y={y}";

//Create a vector tile source and add it to the map.
VectorTileSource source = new VectorTileSource(
    tiles(new String[] { trafficFlowUrl }),
    maxSourceZoom(22)
);
map.sources.add(source);

//Create a layer for traffic flow lines.
LineLayer layer = new LineLayer(source,
    //The name of the data layer within the data source to pass into this rendering layer.
    sourceLayer("Traffic flow"),

    //Color the roads based on the traffic_level property.
    strokeColor(
        interpolate(
            linear(),
            get("traffic_level"),
            stop(0, color(Color.RED)),
            stop(0.33, color(Color.YELLOW)),
            stop(0.66, color(Color.GREEN))
        )
    ),

    //Scale the width of roads based on the traffic_level property.
    strokeWidth(
        interpolate(
            linear(),
            get("traffic_level"),
            stop(0, 6),
            stop(1,1)
        )
    )
);

//Add the traffic flow layer below the labels to make the map clearer.
map.layers.add(layer, "labels");
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Formatted URL to the traffic flow vector tiles, with the maps subscription key appended to it.
val trafficFlowUrl = "https://azmapsdomain.invalid/traffic/flow/tile/pbf?api-version=1.0&style=relative&zoom={z}&x={x}&y={y}"

//Create a vector tile source and add it to the map.
val source = VectorTileSource(
    tiles(arrayOf(trafficFlowUrl)),
    maxSourceZoom(22)
)
map.sources.add(source)

//Create a layer for traffic flow lines.
val layer = LineLayer(
    source,  //The name of the data layer within the data source to pass into this rendering layer.
    sourceLayer("Traffic flow"),  //Color the roads based on the traffic_level property.
    strokeColor(
        interpolate(
            linear(),
            get("traffic_level"),
            stop(0, color(Color.RED)),
            stop(0.33, color(Color.YELLOW)),
            stop(0.66, color(Color.GREEN))
        )
    ),  //Scale the width of roads based on the traffic_level property.
    strokeWidth(
        interpolate(
            linear(),
            get("traffic_level"),
            stop(0, 6),
            stop(1, 1)
        )
    )
)

//Add the traffic flow layer below the labels to make the map clearer.
map.layers.add(layer, "labels")
```

::: zone-end

![트래픽 흐름 수준을 표시 하는 색으로 구분 된 이동 선이 있는 지도](media/create-data-source-android-sdk/android-vector-tile-source-line-layer.png)

## <a name="connecting-a-data-source-to-a-layer"></a>계층에 데이터 원본 연결

데이터는 렌더링 레이어를 사용 하 여 맵에서 렌더링 됩니다. 하나 이상의 렌더링 레이어에서 단일 데이터 원본을 참조할 수 있습니다. 다음 렌더링 계층에는 데이터 원본이 필요 합니다.

- [거품형 계층](map-add-bubble-layer-android.md) -지도에서 점 데이터를 크기가 조정 된 원으로 렌더링 합니다.
- [기호 계층](how-to-add-symbol-to-android-map.md) -지점 데이터를 아이콘이 나 텍스트로 렌더링 합니다.
- [열 지도 계층](map-add-heat-map-layer-android.md) -점 데이터를 밀도 열 맵으로 렌더링 합니다.
- [선 계층](android-map-add-line-layer.md) -선을 렌더링 하거나 다각형의 윤곽선을 렌더링 합니다.
- [다각형 계층](how-to-add-shapes-to-android-map.md) -단색 또는 이미지 패턴으로 다각형 영역을 채웁니다.

다음 코드에서는 데이터 원본을 만들고 지도에 추가한 다음 거품형 계층에 연결 하는 방법을 보여 줍니다. 그런 다음 원격 위치에서 데이터 원본으로 GeoJSON point 데이터를 가져옵니다.

::: zone pivot="programming-language-java-android"

```java
//Create a data source and add it to the map.
DataSource source = new DataSource();
map.sources.add(source);

//Create a layer that defines how to render points in the data source and add it to the map.
BubbleLayer layer = new BubbleLayer(source);
map.layers.add(layer);

//Import the geojson data and add it to the data source.
Utils.importData("URL_or_FilePath_to_GeoJSON_data",
    this,
    (String result) -> {
        //Parse the data as a GeoJSON Feature Collection.
        FeatureCollection fc = FeatureCollection.fromJson(result);

        //Add the feature collection to the data source.
        dataSource.add(fc);

        //Optionally, update the maps camera to focus in on the data.

        //Calculate the bounding box of all the data in the Feature Collection.
        BoundingBox bbox = MapMath.fromData(fc);

        //Update the maps camera so it is focused on the data.
        map.setCamera(
            bounds(bbox),
            padding(20));
    });
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Create a data source and add it to the map.
val source = DataSource()
map.sources.add(source)

//Create a layer that defines how to render points in the data source and add it to the map.
val layer = BubbleLayer(source)
map.layers.add(layer)

//Import the geojson data and add it to the data source.
Utils.importData("URL_or_FilePath_to_GeoJSON_data", this) { 
    result: String? ->
        //Parse the data as a GeoJSON Feature Collection.
        val fc = FeatureCollection.fromJson(result!!)
    
        //Add the feature collection to the data source.
        dataSource.add(fc)
    
        //Optionally, update the maps camera to focus in on the data.
        //Calculate the bounding box of all the data in the Feature Collection.
        val bbox = MapMath.fromData(fc)
    
        //Update the maps camera so it is focused on the data.
        map.setCamera(
            bounds(bbox),
            padding(20)
        )
    }
```

::: zone-end

이러한 데이터 소스에 연결 하지 않는 추가 렌더링 계층이 있지만 렌더링을 위해 데이터를 직접 로드 합니다.

- [타일 계층](how-to-add-tile-layer-android-map.md) -수퍼는 지도 위에 래스터 타일 계층을 적용 합니다.

## <a name="one-data-source-with-multiple-layers"></a>여러 계층이 있는 하나의 데이터 원본

여러 레이어는 단일 데이터 원본에 연결할 수 있습니다. 이 옵션이 유용 하 게 사용할 수 있는 여러 가지 시나리오가 있습니다. 예를 들어 사용자가 다각형을 그리는 시나리오를 생각해 보겠습니다. 사용자가 지도에 점이 추가 될 때 다각형 영역을 렌더링 하 고 채워야 합니다. 스타일이 지정 된 선을 추가 하 여 다각형을 윤곽선으로 만들면 사용자가 그릴 때 다각형의 가장자리를 더 쉽게 볼 수 있습니다. 다각형의 개별 위치를 편리 하 게 편집 하기 위해 각 위치 위에 핀 이나 표식 같은 핸들을 추가할 수 있습니다.

![단일 데이터 원본에서 데이터를 렌더링 하는 여러 레이어를 보여 주는 지도](media/create-data-source-android-sdk/multiple-layers-one-datasource.png)

대부분의 매핑 플랫폼에서 다각형 개체, 선 개체 및 다각형의 각 위치에 대 한 pin이 필요 합니다. 다각형이 수정 될 때 줄과 핀을 수동으로 업데이트 해야 하며,이는 빠르게 복잡 해질 수 있습니다.

Azure Maps를 사용 하는 경우 아래 코드와 같이 데이터 소스에 단일 다각형이 필요 합니다.

::: zone pivot="programming-language-java-android"

```java
//Create a data source and add it to the map.
DataSource source = new DataSource();
map.sources.add(source);

//Create a polygon and add it to the data source.
source.add(Polygon.fromLngLats(/* List of points */));

//Create a polygon layer to render the filled in area of the polygon.
PolygonLayer polygonLayer = new PolygonLayer(source,
    fillColor("rgba(255,165,0,0.2)")
);

//Create a line layer for greater control of rendering the outline of the polygon.
LineLayer lineLayer = new LineLayer(source,
    strokeColor("orange"),
    strokeWidth(2f)
);

//Create a bubble layer to render the vertices of the polygon as scaled circles.
BubbleLayer bubbleLayer = new BubbleLayer(source,
    bubbleColor("orange"),
    bubbleRadius(5f),
    bubbleStrokeColor("white"),
    bubbleStrokeWidth(2f)
);

//Add all layers to the map.
map.layers.add(new Layer[] { polygonLayer, lineLayer, bubbleLayer });
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Create a data source and add it to the map.
val source = DataSource()
map.sources.add(source)

//Create a polygon and add it to the data source.
source.add(Polygon.fromLngLats())

//Create a polygon layer to render the filled in area of the polygon.
val polygonLayer = PolygonLayer(
    source,
    fillColor("rgba(255,165,0,0.2)")
)

//Create a line layer for greater control of rendering the outline of the polygon.
val lineLayer = LineLayer(
    source,
    strokeColor("orange"),
    strokeWidth(2f)
)

//Create a bubble layer to render the vertices of the polygon as scaled circles.
val bubbleLayer = BubbleLayer(
    source,
    bubbleColor("orange"),
    bubbleRadius(5f),
    bubbleStrokeColor("white"),
    bubbleStrokeWidth(2f)
)

//Add all layers to the map.
map.layers.add(arrayOf<Layer>(polygonLayer, lineLayer, bubbleLayer))
```

::: zone-end

> [!TIP]
> 메서드를 사용 하 여 지도에 레이어를 추가 하는 경우 `map.layers.add` 기존 계층의 ID 또는 인스턴스를 두 번째 매개 변수로 전달할 수 있습니다. 그러면 기존 계층 아래에 추가 되는 새 계층이 삽입 됩니다. 계층 ID를 전달 하는 것 외에도이 메서드는 다음 값도 지원 합니다.
>
> - `"labels"` -지도 레이블 계층 아래에 새 계층을 삽입 합니다.
> - `"transit"` -지도 이동 및 전송 계층 아래에 새 계층을 삽입 합니다.

## <a name="next-steps"></a>다음 단계

맵에 추가할 더 많은 코드 예제를 보려면 다음 문서를 참조하세요.

> [!div class="nextstepaction"]
> [데이터 기반 스타일 식 사용](create-data-source-android-sdk.md)

> [!div class="nextstepaction"]
> [기호 계층 추가](how-to-add-symbol-to-android-map.md)

> [!div class="nextstepaction"]
> [거품형 계층 추가](map-add-bubble-layer-android.md)

> [!div class="nextstepaction"]
> [선 계층 추가](android-map-add-line-layer.md)

> [!div class="nextstepaction"]
> [다각형 계층 추가](how-to-add-shapes-to-android-map.md)

> [!div class="nextstepaction"]
> [열 지도 추가](map-add-heat-map-layer-android.md)

> [!div class="nextstepaction"]
> [웹 SDK 코드 샘플](/samples/browse/?products=azure-maps)