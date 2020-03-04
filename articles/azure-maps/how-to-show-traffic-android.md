---
title: Android map에 트래픽 데이터 표시 | Microsoft Azure 맵
description: 이 문서에서는 Microsoft Azure Maps Android SDK를 사용 하 여 지도에 트래픽 데이터를 표시 하는 방법에 대해 알아봅니다.
author: farah-alyasari
ms.author: v-faalya
ms.date: 02/27/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 37de55d671bb19cfcd9fd494c2e76f658fc7db21
ms.sourcegitcommit: e4c33439642cf05682af7f28db1dbdb5cf273cc6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/03/2020
ms.locfileid: "78249490"
---
# <a name="show-traffic-data-on-the-map-using-azure-maps-android-sdk"></a>Azure Maps Android SDK를 사용 하 여 지도에 트래픽 데이터 표시

흐름 데이터 및 인시던트 데이터는 맵에 표시 될 수 있는 두 가지 트래픽 데이터 유형입니다. 이 가이드에서는 두 가지 유형의 트래픽 데이터를 표시 하는 방법을 보여 줍니다. 인시던트 데이터는 생성,도로 클로저 및 사고와 같은 사물에 대 한 점 및 선 기반 데이터로 구성 됩니다. 흐름 데이터는 도로의 트래픽 흐름에 대 한 메트릭을 표시 합니다.

## <a name="prerequisites"></a>사전 요구 사항

맵에 트래픽을 표시 하려면 먼저 [Azure 계정을](quick-demo-map-app.md#create-an-account-with-azure-maps)만들고 [구독 키를 가져와야](quick-demo-map-app.md#get-the-primary-key-for-your-account)합니다. 그런 다음 [Azure Maps Android SDK](https://docs.microsoft.com/azure/azure-maps/how-to-use-android-map-control-library) 를 설치 하 고 맵을 로드 해야 합니다.

## <a name="incidents-traffic-data"></a>인시던트 트래픽 데이터 

`setTraffic`를 호출 하 고 `incidents`다음 라이브러리를 가져와야 합니다.

```java
import static com.microsoft.com.azure.maps.mapcontrol.options.TrafficOptions.incidents;
```

 다음 코드 조각에서는 지도에 트래픽 데이터를 표시 하는 방법을 보여 줍니다. `incidents` 메서드에 부울 값을 전달 하 고이를 `setTraffic` 메서드에 전달 합니다. 

```java
protected void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    mapControl.getMapAsync(map - > {
        map.setTraffic(incidents(true));
    }
}
```

## <a name="flow-traffic-data"></a>흐름 트래픽 데이터

먼저 `setTraffic`를 호출 하 고 `flow`다음 라이브러리를 가져와야 합니다.

```java
import com.microsoft.azure.maps.mapcontrol.options.TrafficFlow;
import static com.microsoft.azure.maps.mapcontrol.options.TrafficOptions.flow;
```

다음 코드 조각을 사용 하 여 트래픽 흐름 데이터를 설정 합니다. 이전 섹션의 코드와 마찬가지로 `flow` 메서드의 반환 값을 `setTraffic` 메서드에 전달 합니다. `flow`에 전달할 수 있는 4 개의 값이 있고 각 값은 `flow`을 트리거하여 해당 값을 반환 합니다. 그러면 `flow`의 반환 값이 `setTraffic`인수로 전달 됩니다. 이러한 4 가지 값에 대해서는 아래 표를 참조 하세요.

| | |
| :-- | :-- |
| TrafficFlow | 지도에 트래픽 데이터를 표시 하지 않습니다. |
| TrafficFlow | 도로의 자유 흐름 속도에 상대적인 트래픽 데이터를 표시 합니다. |
| TrafficFlow RELATIVE_DELAY | 평균 예상 지연 시간 보다 느린 영역을 표시 합니다. |
| TrafficFlow | 도로의 모든 차량에 대 한 절대 속도를 표시 합니다. |

```java
protected void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    mapControl.getMapAsync(map -> {
        map.setTraffic(flow(TrafficFlow.RELATIVE)));
    }
}
```

## <a name="show-incident-traffic-data-by-clicking-a-feature"></a>기능을 클릭 하 여 인시던트 트래픽 데이터 표시

특정 기능에 대 한 인시던트를 얻으려면 아래 코드를 사용할 수 있습니다. 기능을 클릭 하면 코드 논리에서 인시던트를 확인 하 고 인시던트에 대 한 메시지를 작성 합니다. 화면 맨 아래에 세부 정보를 표시 하는 메시지가 표시 됩니다.

1. 먼저 **activity_main .xml > res > 레이아웃**을 편집 해야 합니다. 그러면 아래와 같이 표시 됩니다. `mapcontrol_centerLat`, `mapcontrol_centerLng`및 `mapcontrol_zoom`를 원하는 값으로 바꿀 수 있습니다. 회수, 확대/축소 수준은 0에서 22 사이의 값입니다. 확대/축소 수준 0에서 전체 세계는 단일 타일에 맞게 조정 됩니다.

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
           app:mapcontrol_centerLat="47.6050"
           app:mapcontrol_centerLng="-122.3344"
           app:mapcontrol_zoom="12"
           />

   </FrameLayout>
   ```

2. 다음 코드를 **Mainactivity. java** 파일에 추가 합니다. 패키지는 기본적으로 포함 되어 있으므로 패키지를 맨 위에 보관 해야 합니다.

   ```java
   package <yourpackagename>;
   import androidx.appcompat.app.AppCompatActivity;

   import android.os.Bundle;
   import android.widget.Toast;

   import com.microsoft.azure.maps.mapcontrol.AzureMaps;
   import com.microsoft.azure.maps.mapcontrol.MapControl;
   import com.mapbox.geojson.Feature;
   import com.microsoft.azure.maps.mapcontrol.events.OnFeatureClick;

   import com.microsoft.azure.maps.mapcontrol.options.TrafficFlow;
   import static com.microsoft.azure.maps.mapcontrol.options.TrafficOptions.flow;
   import static com.microsoft.azure.maps.mapcontrol.options.TrafficOptions.incidents;

   public class MainActivity extends AppCompatActivity {

       static {
           AzureMaps.setSubscriptionKey("Your Azure Maps Subscription Key");
       }

       MapControl mapControl;

       @Override
       protected void onCreate(Bundle savedInstanceState) {
           super.onCreate(savedInstanceState);
           setContentView(R.layout.activity_main);

           mapControl = findViewById(R.id.mapcontrol);

           mapControl.onCreate(savedInstanceState);

           //Wait until the map resources are ready.
           mapControl.getMapAsync(map -> {

               map.setTraffic(flow(TrafficFlow.RELATIVE));
               map.setTraffic(incidents(true));

               map.events.add((OnFeatureClick) (features) -> {

                   if (features != null && features.size() > 0) {
                       Feature incident = features.get(0);
                       if (incident.properties() != null) {


                           StringBuilder sb = new StringBuilder();
                           String incidentType = incident.getStringProperty("incidentType");
                           if (incidentType != null) {
                               sb.append(incidentType);
                           }
                           if (sb.length() > 0) sb.append("\n");
                           if ("Road Closed".equals(incidentType)) {
                               sb.append(incident.getStringProperty("from"));
                           } else {
                               String description = incident.getStringProperty("description");
                               if (description != null) {
                                   for (String word : description.split(" ")) {
                                       if (word.length() > 0) {
                                           sb.append(word.substring(0, 1).toUpperCase());
                                           if (word.length() > 1) {
                                               sb.append(word.substring(1));
                                           }
                                           sb.append(" ");
                                       }
                                   }
                               }
                           }
                           String message = sb.toString();

                           if (message.length() > 0) {
                               Toast.makeText(this,message,Toast.LENGTH_LONG).show();
                           }
                       }
                   }
               });
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

3. 응용 프로그램에 위의 코드를 통합 하면 기능을 클릭 하 고 트래픽 인시던트의 세부 정보를 볼 수 있습니다. **Activity_main** 파일에 사용 된 위도, 경도 및 확대/축소 수준 값에 따라 다음 이미지와 유사한 결과가 표시 됩니다.

   <center>

   ![인시던트-지도에 대 한 트래픽](./media/how-to-show-traffic-android/android-traffic.png)

   </center>

## <a name="next-steps"></a>다음 단계

지도에 데이터를 추가 하는 방법에 대 한 자세한 내용은 다음 가이드를 참조 하세요.

> [!div class="nextstepaction"]
> [기호 계층 추가](how-to-add-symbol-to-android-map.md)

> [!div class="nextstepaction"]
> [타일 계층 추가](how-to-add-tile-layer-android-map.md)

> [!div class="nextstepaction"]
> [Android 맵에 셰이프 추가](how-to-add-shapes-to-android-map.md)

> [!div class="nextstepaction"]
> [기능 정보 표시](display-feature-information-android.md)
