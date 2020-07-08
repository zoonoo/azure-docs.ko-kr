---
title: Event Grid 원본으로 Azure Maps
description: Azure Event Grid를 사용한 Azure Maps 이벤트에 대해 제공되는 속성과 스키마를 설명합니다.
services: event-grid
author: femila
ms.service: event-grid
ms.topic: conceptual
ms.date: 04/09/2020
ms.author: femila
ms.openlocfilehash: f015bf682d7ce3475aba5baa73ab72b1426691fe
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84560683"
---
# <a name="azure-maps-as-an-event-grid-source"></a>Event Grid 원본으로 Azure Maps

이 문서에서는 Azure Maps 이벤트에 대한 속성과 스키마를 제공합니다. 이벤트 스키마에 대한 소개는 [Azure Event Grid 이벤트 스키마](https://docs.microsoft.com/azure/event-grid/event-schema)를 참조하세요. 또한 이벤트 원본으로 Azure Maps를 사용 하는 빠른 시작 및 자습서의 목록을 제공 합니다.

## <a name="event-grid-event-schema"></a>Event Grid 이벤트 스키마

### <a name="available-event-types"></a>사용할 수 있는 이벤트 유형

Azure Maps 계정은 다음과 같은 이벤트 유형을 내보냅니다.

| 이벤트 유형 | 설명 |
| ---------- | ----------- |
| Microsoft.Maps.GeofenceEntered | 수신된 좌표가 지정된 지오펜스 외부에서 내부로 이동한 경우에 발생합니다. |
| Microsoft.Maps.GeofenceExited | 수신된 좌표가 지정된 지오펜스 내부에서 외부로 이동한 경우에 발생합니다. |
| Microsoft.Maps.GeofenceResult | 상태와 관계없이 지오펜싱 쿼리에서 결과가 반환될 때마다 발생합니다. |

### <a name="event-examples"></a>이벤트 예제

다음 예제에서는 **GeofenceEntered** 이벤트의 스키마를 보여 줍니다.

```JSON
{   
   "id":"7f8446e2-1ac7-4234-8425-303726ea3981", 
   "topic":"/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.Maps/accounts/{accountName}", 
   "subject":"/spatial/geofence/udid/{udid}/id/{eventId}", 
   "data":{   
      "geometries":[   
         {   
            "deviceId":"device_1", 
            "udId":"1a13b444-4acf-32ab-ce4e-9ca4af20b169", 
            "geometryId":"2", 
            "distance":-999.0, 
            "nearestLat":47.618786, 
            "nearestLon":-122.132151 
         } 
      ], 
      "expiredGeofenceGeometryId":[   
      ], 
      "invalidPeriodGeofenceGeometryId":[   
      ] 
   }, 
   "eventType":"Microsoft.Maps.GeofenceEntered", 
   "eventTime":"2018-11-08T00:54:17.6408601Z", 
   "metadataVersion":"1", 
   "dataVersion":"1.0" 
}
```

다음 예제에서는 **GeofenceResult**의 스키마를 보여 줍니다. 

```JSON
{   
   "id":"451675de-a67d-4929-876c-5c2bf0b2c000", 
   "topic":"/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.Maps/accounts/{accountName}", 
   "subject":"/spatial/geofence/udid/{udid}/id/{eventId}", 
   "data":{   
      "geometries":[   
         {   
            "deviceId":"device_1", 
            "udId":"1a13b444-4acf-32ab-ce4e-9ca4af20b169", 
            "geometryId":"1", 
            "distance":999.0, 
            "nearestLat":47.609833, 
            "nearestLon":-122.148274 
         }, 
         {   
            "deviceId":"device_1", 
            "udId":"1a13b444-4acf-32ab-ce4e-9ca4af20b169", 
            "geometryId":"2", 
            "distance":999.0, 
            "nearestLat":47.621954, 
            "nearestLon":-122.131841 
         } 
      ], 
      "expiredGeofenceGeometryId":[   
      ], 
      "invalidPeriodGeofenceGeometryId":[   
      ] 
   }, 
   "eventType":"Microsoft.Maps.GeofenceResult", 
   "eventTime":"2018-11-08T00:52:08.0954283Z", 
   "metadataVersion":"1", 
   "dataVersion":"1.0" 
}
```

### <a name="event-properties"></a>이벤트 속성

이벤트에는 다음과 같은 최상위 데이터가 있습니다.

| 속성 | 형식 | Description |
| -------- | ---- | ----------- |
| 토픽 | string | 이벤트 원본에 대한 전체 리소스 경로입니다. 이 필드는 쓸 수 없습니다. Event Grid는 이 값을 제공합니다. |
| subject | string | 게시자가 정의한 이벤트 주체에 대한 경로입니다. |
| eventType | string | 이 이벤트 원본에 대해 등록된 이벤트 유형 중 하나입니다. |
| eventTime | string | 공급자의 UTC 시간을 기준으로 이벤트가 생성되는 시간입니다. |
| id | 문자열 | 이벤트에 대한 고유 식별자입니다. |
| 데이터 | object | 지오펜싱 이벤트 데이터입니다. |
| dataVersion | string | 데이터 개체의 스키마 버전입니다. 게시자가 스키마 버전을 정의합니다. |
| metadataVersion | string | 이벤트 메타데이터의 스키마 버전입니다. Event Grid는 최상위 속성의 스키마를 정의합니다. Event Grid는 이 값을 제공합니다. |

데이터 개체의 속성은 다음과 같습니다.

| 속성 | 형식 | 설명 |
| -------- | ---- | ----------- |
| apiCategory | string | 이벤트의 API 범주입니다. |
| apiName | string | 이벤트의 API 이름입니다. |
| issues | 개체 | 처리 중에 발생한 문제를 나열합니다. 문제가 반환되면 응답과 함께 기하 도형이 반환되지 않습니다. |
| responseCode | number | HTTP 응답 코드 |
| geometries | 개체 | 좌표 위치를 포함하거나 위치 주위의 searchBuffer와 겹치는 펜스 기하 도형을 나열합니다. |

Maps API에서 오류가 발생하면 오류 개체가 반환됩니다. 오류 개체의 속성은 다음과 같습니다.

| 속성 | 형식 | 설명 |
| -------- | ---- | ----------- |
| 오류 | ErrorDetails |Maps API에서 오류가 발생하면 이 개체가 반환됩니다.  |

Maps API에서 오류가 발생하면 ErrorDetails 개체가 반환됩니다. ErrorDetails 또는 개체의 속성은 다음과 같습니다.

| 속성 | 형식 | 설명 |
| -------- | ---- | ----------- |
| code | string | HTTP 상태 코드입니다. |
| message | 문자열 | 사용 가능한 경우, 사용자가 읽을 수 있는 오류 설명입니다. |
| innererror | InnerError | 사용 가능한 경우, 오류에 대한 서비스 특정 정보가 포함된 개체입니다. |

InnerError는 오류에 대한 서비스 특정 정보가 포함된 개체입니다. InnerError 개체의 속성은 다음과 같습니다. 

| 속성 | 형식 | 설명 |
| -------- | ---- | ----------- |
| code | string | 오류 메시지입니다. |

기하 도형 개체는 요청의 사용자 시간을 기준으로, 만료된 지오펜스의 기하 도형 ID를 나열합니다. 기하 도형 개체에 포함된 기하 도형 항목의 속성은 다음과 같습니다. 

| 속성 | 형식 | 설명 |
|:-------- |:---- |:----------- |
| deviceId | string | 디바이스의 ID입니다. |
| distance | string | <p>좌표에서 가장 가까운 지오펜스 경계까지의 거리입니다. 양수는 좌표가 지오펜스 외부에 있음을 의미합니다. 좌표가 지오펜스 외부에 있지만 가장 가까운 지오펜스 경계에서 searchBuffer 값보다 멀리 떨어진 경우의 값은 999입니다. 음수는 좌표가 지오펜스 내부에 있음을 의미합니다. 좌표가 다각형 내부에 있지만 가장 가까운 지오펜싱 경계에서 searchBuffer 값보다 멀리 떨어진 경우의 값은 -999입니다. 값 999는 좌표가 지오펜스 외부에 있다는 신뢰도가 높음을 의미합니다. 값 -999는 좌표가 지오펜스 내부에 있다는 신뢰도가 높음을 의미합니다.<p> |
| geometryid |string | 고유 ID는 지오펜스 기하 도형을 식별합니다. |
| nearestlat | number | 기하 도형에서 가장 가까운 지점의 위도입니다. |
| nearestlon | number | 기하 도형에서 가장 가까운 지점의 경도입니다. |
| udId | string | 지오펜스를 업로드할 때 사용자 업로드 서비스에서 반환되는 고유 ID입니다. 지오펜싱 POST API에는 포함되지 않습니다. |

데이터 개체의 속성은 다음과 같습니다.

| 속성 | 형식 | 설명 |
| -------- | ---- | ----------- |
| expiredGeofenceGeometryId | string[] | 요청의 사용자 시간을 기준으로, 만료된 지오펜스의 기하 도형 ID 목록입니다. |
| geometries | geometries[] |좌표 위치를 포함하거나 위치 주위의 searchBuffer와 겹치는 펜스 기하 도형을 나열합니다. |
| invalidPeriodGeofenceGeometryId | string[]  | 요청의 사용자 시간을 기준으로, 잘못된 기간에 있는 지오펜스의 기하 도형 ID 목록입니다. |
| isEventPublished | boolean | 하나 이상의 이벤트가 Azure Maps 이벤트 구독자에 게시된 경우에는 true이고, Azure Maps 이벤트 구독자에 이벤트가 게시되지 않은 경우에는 false입니다. |

## <a name="tutorials-and-how-tos"></a>자습서 및 방법
|제목  |Description  |
|---------|---------|
| [Event Grid를 사용하여 Azure Maps 이벤트에 대응](../azure-maps/azure-maps-event-grid-integration.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Event Grid와 Azure Maps 통합에 관한 개요입니다. |
| [자습서: 지 오를 설정 합니다.](../azure-maps/tutorial-geofence.md?toc=%2fazure%2fevent-grid%2ftoc.json) | 이 자습서에서는 Azure Maps를 사용하여 지오펜스를 설정하는 기본 단계를 안내합니다. Azure Event Grid를 사용하여 지오펜스 결과를 스트림하고, 지오펜스 결과를 기반으로 하는 알림을 설정합니다. |

## <a name="next-steps"></a>다음 단계

* Azure Event Grid에 대한 소개는 [Event Grid란?](overview.md)을 참조하세요.
* Azure Event Grid 구독을 만드는 방법에 대한 자세한 내용은 [Event Grid 구독 스키마](subscription-creation-schema.md)를 참조하세요.