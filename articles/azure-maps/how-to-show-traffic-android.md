---
title: Android maps에 트래픽 데이터 표시 | Microsoft Azure 맵
description: 이 문서에서는 Microsoft Azure Maps Android SDK를 사용 하 여 지도에 트래픽 데이터를 표시 하는 방법에 대해 알아봅니다.
author: rbrundritt
ms.author: richbrun
ms.date: 2/26/2021
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: cpendle
zone_pivot_groups: azure-maps-android
ms.openlocfilehash: 36b3666f12b48468467e76f4c281d58d8018478c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102098539"
---
# <a name="show-traffic-data-on-the-map-android-sdk"></a>지도에 트래픽 데이터 표시 (Android SDK)

흐름 데이터 및 인시던트 데이터는 맵에 표시 될 수 있는 두 가지 트래픽 데이터 유형입니다. 이 가이드에서는 두 가지 유형의 트래픽 데이터를 표시 하는 방법을 보여 줍니다. 인시던트 데이터는 생성,도로 클로저 및 사고와 같은 사물에 대 한 점 및 선 기반 데이터로 구성 됩니다. 흐름 데이터는 도로의 트래픽 흐름에 대 한 메트릭을 표시 합니다.

## <a name="prerequisites"></a>필수 구성 요소

[빠른 시작: Android 앱 만들기](quick-android-map.md) 문서의 단계를 완료 해야 합니다. 이 문서의 코드 블록은 maps 이벤트 처리기에 삽입할 수 있습니다 `onReady` .

## <a name="show-traffic-on-the-map"></a>맵에 트래픽 표시

Azure Maps에서 사용할 수 있는 트래픽 데이터에는 두 가지 유형이 있습니다.

- 인시던트 데이터 - 건설, 도로 폐쇄 및 사고와 같은 사항에 대한 점 및 선 기반 데이터로 구성됩니다.
- 흐름 데이터 - 도로의 트래픽 흐름에 대한 메트릭을 제공합니다. 트래픽 흐름 데이터를 사용하여 도로 색을 조정하는 경우가 많습니다. 이 색은 속도 제한 또는 다른 메트릭을 기준으로 흐름을 저하시키는 트래픽 양을 기반으로 합니다. 맵의 트래픽 옵션에 전달할 수 있는 4 개의 값이 있습니다 `flow` .

    |흐름 값 | 설명|
    | :-- | :-- |
    | TrafficFlow | 지도에 트래픽 데이터를 표시 하지 않습니다. |
    | TrafficFlow | 도로의 자유 흐름 속도에 상대적인 트래픽 데이터를 표시 합니다. |
    | TrafficFlow.RELATIVE_DELAY | 평균 예상 지연 시간 보다 느린 영역을 표시 합니다. |
    | TrafficFlow | 도로의 모든 차량에 대 한 절대 속도를 표시 합니다. |

다음 코드에서는 맵에 트래픽 데이터를 표시하는 방법을 보여줍니다.

::: zone pivot="programming-language-java-android"

```java
//Show traffic on the map using the traffic options.
map.setTraffic(
    incidents(true),
    flow(TrafficFlow.RELATIVE)
);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
map.setTraffic(
    incidents(true),
    flow(TrafficFlow.RELATIVE)
)
```

::: zone-end

다음 스크린샷은 맵의 실시간 트래픽 정보를 렌더링 하는 위의 코드를 보여 줍니다.

![실시간 트래픽 정보를 표시 하는 맵](media/how-to-show-traffic-android/android-show-traffic.png)

## <a name="get-traffic-incident-details"></a>트래픽 인시던트 정보 가져오기

트래픽 인시던트에 대 한 세부 정보는 맵에 인시던트를 표시 하는 데 사용 되는 기능의 속성 내에서 사용할 수 있습니다. 트래픽 인시던트는 Azure Maps 트래픽 인시던트 벡터 타일 서비스를 사용 하 여 맵에 추가 됩니다. [여기에 설명](https://developer.tomtom.com/traffic-api/traffic-api-documentation-traffic-incidents/vector-incident-tiles)된 경우 이러한 벡터 타일에 있는 데이터의 형식입니다. 다음 코드는 맵에 클릭 이벤트를 추가 하 고 클릭 한 트래픽 인시던트 기능을 검색 하 여 일부 세부 정보를 포함 하는 알림 메시지를 표시 합니다.

::: zone pivot="programming-language-java-android"

```java
//Show traffic information on the map.
map.setTraffic(
    incidents(true),
    flow(TrafficFlow.RELATIVE)
);

//Add a click event to the map.
map.events.add((OnFeatureClick) (features) -> {

    if (features != null && features.size() > 0) {
        Feature incident = features.get(0);

        //Ensure that the clicked feature is an traffic incident feature.
        if (incident.properties() != null && incident.hasProperty("incidentType")) {

            StringBuilder sb = new StringBuilder();
            String incidentType = incident.getStringProperty("incidentType");

            if (incidentType != null) {
                sb.append(incidentType);
            }

            if (sb.length() > 0) {
                sb.append("\n");
            }

            //If the road is closed, find out where it is closed from.
            if ("Road Closed".equals(incidentType)) {
                String from = incident.getStringProperty("from");

                if (from != null) {
                    sb.append(from);
                }
            } else {
                //Get the description of the traffic incident.
                String description = incident.getStringProperty("description");

                if (description != null) {
                    sb.append(description);
                }
            }

            String message = sb.toString();

            if (message.length() > 0) {
                Toast.makeText(this, message, Toast.LENGTH_LONG).show();
            }
        }
    }
});
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Show traffic information on the map.
map.setTraffic(
    incidents(true),
    flow(TrafficFlow.RELATIVE)
)

//Add a click event to the map.
map.events.add(OnFeatureClick { features: List<Feature>? ->
    if (features != null && features.size > 0) {
        val incident = features[0]

        //Ensure that the clicked feature is an traffic incident feature.
        if (incident.properties() != null && incident.hasProperty("incidentType")) {
            val sb = StringBuilder()
            val incidentType = incident.getStringProperty("incidentType")

            if (incidentType != null) {
                sb.append(incidentType)
            }

            if (sb.length > 0) {
                sb.append("\n")
            }

            //If the road is closed, find out where it is closed from.
            if ("Road Closed" == incidentType) {
                val from = incident.getStringProperty("from")
                if (from != null) {
                    sb.append(from)
                }
            } else { //Get the description of the traffic incident.
                val description = incident.getStringProperty("description")
                if (description != null) {
                    sb.append(description)
                }
            }

            val message = sb.toString()
            if (message.length > 0) {
                Toast.makeText(this, message, Toast.LENGTH_LONG).show()
            }
        }
    }
})
```

::: zone-end

다음 스크린샷은 인시던트 정보를 표시 하는 알림 메시지가 포함 된 맵의 실시간 트래픽 정보를 렌더링 하는 위의 코드를 보여 줍니다.

![인시던트 정보를 표시 하는 알림 메시지를 사용 하 여 실시간 트래픽 정보를 표시 하는 맵](media/how-to-show-traffic-android/android-traffic-details.png)

## <a name="next-steps"></a>다음 단계

지도에 데이터를 추가 하는 방법에 대 한 자세한 내용은 다음 가이드를 참조 하세요.

> [!div class="nextstepaction"]
> [타일 레이어 추가](how-to-add-tile-layer-android-map.md)
