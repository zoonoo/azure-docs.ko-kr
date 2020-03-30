---
title: 안드로이드지도에 교통 데이터 표시 | 마이크로소프트 Azure 지도
description: 이 문서에서는 Microsoft Azure 지도 Android SDK를 사용하여 맵에 트래픽 데이터를 표시하는 방법을 알아봅니다.
author: philmea
ms.author: philmea
ms.date: 02/27/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: e5611eeb08ac370e12cf452d57a87e449fbd80da
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335385"
---
# <a name="show-traffic-data-on-the-map-using-azure-maps-android-sdk"></a>Azure지도 안드로이드 SDK를 사용하여지도에 교통 데이터 표시

흐름 데이터 및 인시던트 데이터는 맵에 표시할 수 있는 두 가지 유형의 트래픽 데이터입니다. 이 가이드에서는 두 가지 유형의 트래픽 데이터를 모두 표시하는 방법을 보여 주었습니다. 인시던트 데이터는 건설, 도로 폐쇄 및 사고와 같은 사물에 대한 포인트 및 라인 기반 데이터로 구성됩니다. 흐름 데이터는 도로의 트래픽 흐름에 대한 메트릭을 보여줍니다.

## <a name="prerequisites"></a>사전 요구 사항

맵에 트래픽을 표시하려면 Azure 계정을 [만들고](quick-demo-map-app.md#create-an-account-with-azure-maps) [구독 키를 가져와야](quick-demo-map-app.md#get-the-primary-key-for-your-account)합니다. 그런 다음 [Azure 지도 Android SDK를](https://docs.microsoft.com/azure/azure-maps/how-to-use-android-map-control-library) 설치하고 맵을 로드해야 합니다.

## <a name="incidents-traffic-data"></a>인시던트 트래픽 데이터 

다음 라이브러리를 가져와서 호출해야 `setTraffic` 합니다. `incidents`

```java
import static com.microsoft.com.azure.maps.mapcontrol.options.TrafficOptions.incidents;
```

 다음 코드 조각은 맵에 교통 데이터를 표시하는 방법을 보여 주었습니다. 부울 값을 메서드에 `incidents` 전달하고 메서드에 `setTraffic` 전달합니다. 

```java
protected void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    mapControl.getMapAsync(map - > {
        map.setTraffic(incidents(true));
    }
}
```

## <a name="flow-traffic-data"></a>흐름 트래픽 데이터

먼저 다음 라이브러리를 가져와서 호출해야 `setTraffic` `flow`합니다.

```java
import com.microsoft.azure.maps.mapcontrol.options.TrafficFlow;
import static com.microsoft.azure.maps.mapcontrol.options.TrafficOptions.flow;
```

다음 코드 조각을 사용하여 트래픽 흐름 데이터를 설정합니다. 이전 섹션의 코드와 마찬가지로 `flow` 메서드의 반환 값을 메서드에 `setTraffic` 전달합니다. 에 전달할 `flow`수 있는 네 개의 값이 있으며 `flow` 각 값은 각 값을 반환하기 위해 트리거됩니다. 그러면 `flow` 반환 값이 `setTraffic`인수로 전달됩니다. 다음 네 가지 값은 아래 표를 참조하십시오.

| | |
| :-- | :-- |
| 트래픽 흐름.없음 | 지도에 교통 데이터를 표시하지 않습니다. |
| 트래픽 흐름.상대 | 도로의 자유 흐름 속도를 기준으로 한 교통 데이터를 표시합니다. |
| 트래픽흐름.RELATIVE_DELAY | 평균 예상 지연보다 느린 영역표시 |
| 트래픽 흐름.절대 | 도로에있는 모든 차량의 절대 속도를 보여줍니다 |

```java
protected void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    mapControl.getMapAsync(map -> {
        map.setTraffic(flow(TrafficFlow.RELATIVE)));
    }
}
```

## <a name="show-incident-traffic-data-by-clicking-a-feature"></a>피처를 클릭하여 인시던트 트래픽 데이터 표시

특정 기능에 대한 인시던트를 얻으려면 아래 코드를 사용할 수 있습니다. 기능을 클릭하면 코드 논리에서 인시던트를 확인하고 인시던트에 대한 메시지를 작성합니다. 세부 정보가 있는 메시지가 화면 하단에 표시됩니다.

1. 먼저 **activity_main.xml에 > > 레이아웃을**편집하여 아래와 같이 보이도록해야 합니다. `mapcontrol_centerLat`을 `mapcontrol_centerLng`및 원하는 `mapcontrol_zoom` 값으로 바꿀 수 있습니다. 상기 하면 확대/축소 수준은 0에서 22 사이의 값입니다. 확대/축소 수준 0에서는 전체 세계가 단일 타일에 맞습니다.

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

2. **MainActivity.java** 파일에 다음 코드를 추가합니다. 패키지는 기본적으로 포함되어 있으므로 패키지를 맨 위에 두어야 합니다.

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

3. 응용 프로그램에 위의 코드를 통합하면 기능을 클릭하고 교통 사고의 세부 정보를 볼 수 있습니다. **activity_main.xml** 파일에 사용된 위도, 경도 및 확대/축소 수준 값에 따라 다음 이미지와 유사한 결과가 표시됩니다.

   <center>

   ![지도에서 발생한 사고 트래픽](./media/how-to-show-traffic-android/android-traffic.png)

   </center>

## <a name="next-steps"></a>다음 단계

맵에 데이터를 추가하는 방법을 알아보려면 다음 가이드를 참조하십시오.

> [!div class="nextstepaction"]
> [기호 레이어 추가](how-to-add-symbol-to-android-map.md)

> [!div class="nextstepaction"]
> [타일 계층 추가](how-to-add-tile-layer-android-map.md)

> [!div class="nextstepaction"]
> [안드로이드맵에 셰이프 추가](how-to-add-shapes-to-android-map.md)

> [!div class="nextstepaction"]
> [기능 정보 표시](display-feature-information-android.md)
