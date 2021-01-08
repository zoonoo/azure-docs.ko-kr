---
title: '자습서: Azure Maps Android SDK에 GeoJSON 데이터 로드 | Microsoft Azure Maps'
description: GeoJSON 데이터 파일을 Azure Maps Android 맵 SDK로 로드하는 방법에 대한 자습서입니다.
author: rbrundritt
ms.author: richbrun
ms.date: 12/10/2020
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.openlocfilehash: b527cd7b3f841b6cb3dcf2dce6930f3bd9bcc184
ms.sourcegitcommit: 66b0caafd915544f1c658c131eaf4695daba74c8
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/18/2020
ms.locfileid: "97681245"
---
# <a name="tutorial-load-geojson-data-into-azure-maps-android-sdk"></a>자습서: GeoJSON 데이터를 Azure Maps Android SDK로 로드

이 자습서에서는 위치 데이터의 GeoJSON 파일을 Azure Maps Android SDK로 가져오는 과정을 안내합니다. 이 자습서에서는 다음 작업을 수행하는 방법을 알아봅니다.

> [!div class="checklist"]
> * Android 애플리케이션에 Azure Maps를 추가합니다.
> * 로컬 파일 또는 웹에서 데이터 원본을 만들고 GeoJSON 파일에 로드합니다.
> * 지도에 데이터를 표시합니다.

## <a name="prerequisites"></a>필수 구성 요소

1. [빠른 시작: Android 앱 만들기](quick-android-map.md)를 완료합니다. 이 자습서에는 해당 빠른 시작에서 사용되는 코드를 확장합니다.
2. [샘플 관심 지점](https://raw.githubusercontent.com/Azure-Samples/AzureMapsCodeSamples/master/AzureMapsCodeSamples/Common/data/geojson/SamplePoiDataSet.json) GeoJSON 파일을 다운로드합니다.

### <a name="import-geojson-data-from-web-or-assets-folder"></a>웹 또는 자산 폴더에서 GeoJSON 데이터 가져오기

대부분의 GeoJSON 파일은 `FeatureCollection` 내에 모든 데이터를 래핑합니다. 이로 인해 GeoJSON 파일이 애플리케이션에 문자열로 로드될 경우, 지도에 추가할 수 있는 GeoJSON `FeatureCollection` 개체로 문자열을 역직렬화하는 기능 컬렉션의 정적 `fromJson` 메서드로 전달될 수 있습니다.

다음 단계에서는 GeoJSON 파일을 애플리케이션으로 가져와서 GeoJSON `FeatureCollection` 개체로 역직렬화하는 방법을 보여 줍니다.

1. [빠른 시작: Android 앱 만들기](quick-android-map.md)를 완료하세요. 다음 단계는 이 애플리케이션을 기반으로 합니다.
2. Android Studio의 프로젝트 패널에서 **app** 폴더를 마우스 오른쪽 단추로 클릭하고 `New > Folder > Assets Folder`로 이동합니다.
3. [샘플 관심 지점](https://raw.githubusercontent.com/Azure-Samples/AzureMapsCodeSamples/master/AzureMapsCodeSamples/Common/data/geojson/SamplePoiDataSet.json) GeoJSON 파일을 자산 폴더로 끌어서 놓습니다.
4. **Utils.java** 라는 새 파일을 만들고, 다음 코드를 해당 파일에 추가합니다. 이 코드는 애플리케이션의 `assets` 폴더 또는 웹(문자열로 된 URL 사용)에서 비동기적으로 파일을 가져오는 `importData`라는 정적 메서드를 제공하고, 간단한 콜백 메서드를 사용하여 UI 스레드로 다시 반환합니다.

    ```java
    //Modify the package name as needed to align with your application.
    package com.example.myapplication;
    
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

5. **MainActivity.java** 파일로 이동하고 `mapControl.onReady` 이벤트에 대한 콜백 내부에 다음 코드를 추가합니다. 이는 `onCreate` 메서드 내부에 있습니다. 이 코드는 import 유틸리티를 사용하여 **SamplePoiDataSet.json** 파일을 문자열로 읽은 다음, `FeatureCollection` 클래스의 정적 `fromJson` 메서드를 사용하여 기능 컬렉션으로 역직렬화합니다. 또한 이 코드는 기능 컬렉션에 있는 모든 데이터의 경계 상자 영역을 계산하고 이를 사용하여 지도의 카메라를 데이터에 집중하도록 설정합니다.

    ```java
    //Create a data source and add it to the map.
    DataSource source = new DataSource();
    map.sources.add(source);
    
    //Import the GeoJSON data and add it to the data source.
    Utils.importData("SamplePoiDataSet.json",
        this,
        (String result) -> {
            //Parse the data as a GeoJSON Feature Collection.
            FeatureCollection fc = FeatureCollection.fromJson(result);
    
            //Add the feature collection to the data source.
            source.add(fc);
    
            //Optionally, update the maps camera to focus in on the data.
    
            //Calculate the bounding box of all the data in the Feature Collection.
            BoundingBox bbox = MapMath.fromData(fc);
    
            //Update the maps camera so it is focused on the data.
            map.setCamera(
                bounds(bbox),

                //Padding added to account for pixel size of rendered points.
                padding(20)
            );
        });
    ```

6. 이제 데이터 원본을 사용하여 지도에 GeoJSON 데이터를 로드하는 코드가 있으므로 지도에 데이터가 표시되는 방법을 지정해야 합니다. 점 데이터의 렌더링 레이어는 여러 가지가 있습니다. 가장 일반적으로 사용되는 레이어는 [거품형 레이어](map-add-bubble-layer-android.md), [기호 레이어](how-to-add-symbol-to-android-map.md) 및 [열 지도 레이어](map-add-heat-map-layer-android.md)입니다. `mapControl.onReady` 이벤트에 대한 콜백에서 데이터를 가져오는 코드 뒤에 데이터를 거품형 레이어에 렌더링하는 다음 코드를 추가합니다.

    ```java
    //Create a layer and add it to the map.
    BubbleLayer layer = new BubbleLayer(source);
    map.layers.add(layer);
    ```

7. 애플리케이션을 실행합니다. 미국을 중심으로 GeoJSON 파일의 각 위치에 원이 오버레이된 지도가 표시됩니다.

    ![GeoJSON 파일의 데이터가 표시된 미국 지도](media/tutorial-load-geojson-file-android/android-import-geojson.png)


## <a name="clean-up-resources"></a>리소스 정리

이 자습서의 리소스를 정리하려면 다음 단계를 수행합니다.

1. Android Studio를 닫고 자신이 만든 애플리케이션을 삭제합니다.
2. 외부 디바이스에서 애플리케이션을 테스트한 경우 해당 디바이스에서 애플리케이션을 제거합니다.

## <a name="next-steps"></a>다음 단계

더 많은 코드 예제와 대화형 코딩 환경을 살펴보려면 다음을 참조하세요.

> [!div class="nextstepaction"]
> [데이터 기반 스타일 식 사용](data-driven-style-expressions-android-sdk.md)

> [!div class="nextstepaction"]
> [기능 정보 표시](display-feature-information-android.md)

> [!div class="nextstepaction"]
> [기호 계층 추가](how-to-add-symbol-to-android-map.md)

> [!div class="nextstepaction"]
> [선 계층 추가](android-map-add-line-layer.md)

> [!div class="nextstepaction"]
> [다각형 계층 추가](how-to-add-shapes-to-android-map.md)
