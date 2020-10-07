---
title: '자습서: Microsoft Azure Map에서 지오펜스 만들기 및 디바이스 추적'
description: 지오펜스를 설정하는 방법에 대한 자습서. Azure Maps Spatial 서비스를 사용하여 지오펜스와 관련된 디바이스를 추적하는 방법을 알아봅니다.
author: anastasia-ms
ms.author: v-stharr
ms.date: 8/20/2020
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: 7a0c39b6d2369a1279fee3905083f0660a4aabb8
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/25/2020
ms.locfileid: "91335197"
---
# <a name="tutorial-set-up-a-geofence-by-using-azure-maps"></a>자습서: Azure Maps를 사용하여 지오펜스 설정

이 자습서에서는 Azure Maps 지오펜스 서비스를 만들고 사용하는 방법에 대한 기본 사항을 안내합니다. 다음과 같은 시나리오 컨텍스트에서 알아볼 것입니다.

*건설 분야의 현장 관리자는 건설 구역의 경계를 출입하는 장비를 추적해야 합니다. 일부 장비가 이러한 경계에 들어가거나 나갈 때마다 운영 관리자에게 이메일 알림을 보냅니다.*

Azure Maps는 건설 구역을 출입하는 장비의 추적을 지원하는 다양한 서비스를 제공합니다. 이 자습서에서는 다음을 수행합니다.

> [!div class="checklist"]
> * 모니터링하려는 건설 현장 구역을 정의하는 [지오펜싱 GeoJSON 데이터](geofence-geojson.md)를 업로드합니다. [데이터 업로드 API](https://docs.microsoft.com/rest/api/maps/data/uploadpreview)를 사용하여 지오펜스를 다각형 좌표로 Azure Maps 계정에 업로드합니다.
> * 장비가 지오펜스 영역을 출입할 때 트리거되어 이메일 알림을 건설 현장 운영 관리자에 보내도록 두 개의 [논리 앱](https://docs.microsoft.com/azure/event-grid/handler-webhooks#logic-apps)을 설정합니다.
> * [Azure Event Grid](https://docs.microsoft.com/azure/event-grid/overview)를 사용하여 Azure Maps 지오펜스 출입 이벤트를 구독합니다. 두 개의 논리 앱에 정의된 HTTP 엔드포인트를 호출하는 두 개의 webhook 이벤트 구독을 설정합니다. 그러면 논리 앱은 지오펜스에서 나가거나 들어오는 장비에 대한 적절한 이메일 알림을 보냅니다.
> * 지오펜스 영역에서 일부 장비가 들어오고 나갈 때 [지오펜스 가져오기 검색 API](https://docs.microsoft.com/rest/api/maps/spatial/getgeofence)를 사용하여 알림을 받습니다.

## <a name="prerequisites"></a>사전 요구 사항

1. [Azure Maps 계정을 만듭니다](quick-demo-map-app.md#create-an-azure-maps-account).
2. 기본 키 또는 구독 키라고도 하는 [기본 구독 키를 가져옵니다](quick-demo-map-app.md#get-the-primary-key-for-your-account).

이 자습서에서는 [Postman](https://www.postman.com/) 애플리케이션을 사용하지만, 다른 API 개발 환경을 선택해도 됩니다.

## <a name="upload-geofencing-geojson-data"></a>지오펜싱 GeoJSON 데이터 업로드

이 자습서에서는 `FeatureCollection`이 포함된 지오펜싱 GeoJSON 데이터를 업로드합니다. `FeatureCollection`에는 건설 현장 내에서 다각형 영역을 정의하는 두 개의 지오펜스가 있습니다. 첫 번째 지오펜스에는 시간 만료 또는 제한이 없습니다. 두 번째 지오펜스는 근무 시간(태평양 표준 시간대 오전 9:00~오후 5:00) 동안에만 쿼리할 수 있으며, 2022년 1월 1일 이후에는 더 이상 유효하지 않습니다. GeoJSON 형식에 대한 자세한 내용은 [지오펜싱 GeoJSON 데이터](geofence-geojson.md)를 참조하세요.

>[!TIP]
>지오펜싱 데이터는 언제든지 업데이트할 수 있습니다. 자세한 내용은 [데이터 업로드 API](https://docs.microsoft.com/rest/api/maps/data/uploadpreview)를 참조하세요.

1. Postman 앱을 엽니다. 위쪽 근처에서 **새로 만들기**를 선택합니다. **새로 만들기** 창에서 **컬렉션**을 선택합니다. 컬렉션 이름을 지정하고, **만들기**를 선택합니다.

2. 요청을 만들려면 **새로 만들기**를 다시 선택합니다. **새로 만들기** 창에서 **요청**을 선택합니다. 요청에 대한 **요청 이름**을 입력합니다. 이전 단계에서 만든 컬렉션을 선택한 다음, **저장**을 선택합니다.

3. 작성기 탭에서 **POST** HTTP 메서드를 선택하고, 다음 URL을 입력하여 지오펜싱 데이터를 Azure Maps에 업로드합니다. 이 요청 및 이 문서에 언급된 기타 요청에 대한 `{Azure-Maps-Primary-Subscription-key}`를 기본 구독 키로 바꿉니다.

    ```HTTP
    https://atlas.microsoft.com/mapData/upload?subscription-key={Azure-Maps-Primary-Subscription-key}&api-version=1.0&dataFormat=geojson
    ```

    URL 경로의 `geojson` 매개 변수는 업로드되는 데이터의 데이터 형식을 나타냅니다.

4. **본문** 탭을 선택합니다. 입력 형식으로 **raw**, **JSON**을 차례로 선택합니다. 다음 GeoJSON 데이터를 복사하여 **Body** 텍스트 영역에 붙여넣습니다.

   ```JSON
   {
      "type": "FeatureCollection",
      "features": [
        {
          "type": "Feature",
          "geometry": {
            "type": "Polygon",
            "coordinates": [
              [
                [
                  -122.13393688201903,
                  47.63829579223815
                ],
                [
                  -122.13389128446579,
                  47.63782047131512
                ],
                [
                  -122.13240802288054,
                  47.63783312249837
                ],
                [
                  -122.13238388299942,
                  47.63829037035086
                ],
                [
                  -122.13393688201903,
                  47.63829579223815
                ]
              ]
            ]
          },
          "properties": {
            "geometryId": "1"
          }
        },
        {
          "type": "Feature",
          "geometry": {
            "type": "Polygon",
            "coordinates": [
              [
                [
                  -122.13374376296996,
                  47.63784758098976
                ],
                [
                  -122.13277012109755,
                  47.63784577367854
                ],
                [
                  -122.13314831256866,
                  47.6382813338708
                ],
                [
                  -122.1334782242775,
                  47.63827591198201
                ],
                [
                  -122.13374376296996,
                  47.63784758098976
                ]
              ]
            ]
          },
          "properties": {
            "geometryId": "2",
            "validityTime": {
            "expiredTime": "2022-01-01T00:00:00",
            "validityPeriod": [
                {
                  "startTime": "2020-07-15T16:00:00",
                  "endTime": "2020-07-15T24:00:00",
                  "recurrenceType": "Daily",
                  "recurrenceFrequency": 1,
                  "businessDayOnly": true
                }
              ]
            }
          }
        }
      ]
   }
   ```

5. **보내기**를 선택하고, 요청이 처리될 때까지 기다립니다. 요청이 완료되면 응답의 **헤더** 탭으로 이동합니다. **위치** 키의 값(`status URL`)을 복사합니다.

    ```http
    https://atlas.microsoft.com/mapData/operations/<operationId>?api-version=1.0
    ```

6. API 호출의 상태를 확인하려면 `status URL`에 대한 **GET** HTTP 요청을 만듭니다. 인증을 위해 기본 구독 키를 URL에 추가해야 합니다. **GET** 요청은 다음 URL과 같습니다.

   ```HTTP
   https://atlas.microsoft.com/mapData/<operationId>/status?api-version=1.0&subscription-key={Subscription-key}
   ```

7. **GET** HTTP 요청이 성공적으로 완료되면 `resourceLocation`이 반환됩니다. `resourceLocation`에는 업로드된 콘텐츠에 대한 고유한 `udid`가 포함되어 있습니다. 이 자습서의 마지막 섹션에서 지오펜스 가져오기 API를 쿼리할 수 있도록 이 `udid`를 저장합니다. 필요에 따라 다음 단계에서 `resourceLocation` URL을 사용하여 이 리소스에서 메타데이터를 검색할 수 있습니다.

      ```json
      {
          "status": "Succeeded",
          "resourceLocation": "https://atlas.microsoft.com/mapData/metadata/{udid}?api-version=1.0"
      }
      ```

8. 콘텐츠 메타데이터를 검색하려면 7단계에서 검색된 `resourceLocation` URL에 대한 **GET** HTTP 요청을 만듭니다. 인증을 위해 기본 구독 키를 URL에 추가해야 합니다. **GET** 요청은 다음 URL과 같습니다.

    ```http
   https://atlas.microsoft.com/mapData/metadata/{udid}?api-version=1.0&subscription-key={Azure-Maps-Primary-Subscription-key}
    ```

9. **GET** HTTP 요청이 성공적으로 완료되면 7단계의 `resourceLocation`에서 지정한 `udid`가 응답 본문에 포함됩니다. 또한 나중에 콘텐츠를 액세스하고 다운로드하는 위치와 콘텐츠에 대한 기타 메타데이터도 포함됩니다. 전체 응답의 예제는 다음과 같습니다.

    ```json
    {
        "udid": "{udid}",
        "location": "https://atlas.microsoft.com/mapData/{udid}?api-version=1.0",
        "created": "7/15/2020 6:11:43 PM +00:00",
        "updated": "7/15/2020 6:11:45 PM +00:00",
        "sizeInBytes": 1962,
        "uploadStatus": "Completed"
    }
    ```

## <a name="create-workflows-in-azure-logic-apps"></a>Azure Logic Apps에서 워크플로 만들기

다음으로, 이메일 알림을 트리거하는 두 개의 [논리 앱](https://docs.microsoft.com/azure/event-grid/handler-webhooks#logic-apps) 엔드포인트를 만듭니다. 첫 번째 논리 앱 엔드포인트를 만드는 방법은 다음과 같습니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

2. Azure Portal의 왼쪽 위 모서리에서 **리소스 만들기**를 선택합니다.

3. **Marketplace 검색** 상자에서 **Logic App**을 입력합니다.

4. 결과에서 **논리 앱** > **만들기**를 선택합니다.

5. **Logic App** 페이지에서 다음 값을 입력합니다.
    * 이 논리 앱에 사용하려는 **구독**
    * 이 논리 앱에 대한 **리소스 그룹** 이름 **새로 만들기**를 선택할 수도 있고 **기존 리소스 그룹 사용**을 선택할 수도 있습니다.
    * 논리 앱의 **Logic App 이름**. 여기서는 `Equipment-Enter`를 이름으로 사용합니다.

    이 자습서의 목적을 위해 그 외의 값은 기본 설정을 유지합니다.

    :::image type="content" source="./media/tutorial-geofence/logic-app-create.png" alt-text="논리 앱 만들기 스크린샷":::

6. **검토 + 만들기**를 선택합니다. 설정을 검토하고, **만들기**를 선택하여 배포를 제출합니다. 배포가 성공적으로 완료되면 **리소스로 이동**을 선택합니다. 그러면 **논리 앱 디자이너**로 이동됩니다.

7. 트리거 형식을 선택합니다. **일반적인 트리거로 시작** 섹션이 나올 때까지 아래로 스크롤합니다. **HTTP 요청을 수신한 경우**를 선택합니다.

     :::image type="content" source="./media/tutorial-geofence/logic-app-trigger.png" alt-text="논리 앱 만들기 스크린샷":::

8. 논리 앱 디자이너의 오른쪽 위 모서리에서 **저장**을 선택합니다. **HTTP POST URL**이 자동으로 생성됩니다. URL을 저장합니다. 다음 섹션에서 이벤트 엔드포인트를 만드는 데 필요합니다.

    :::image type="content" source="./media/tutorial-geofence/logic-app-httprequest.png" alt-text="논리 앱 만들기 스크린샷":::

9. **+ 새 단계**를 선택합니다. 이제 작업을 선택합니다. 검색 상자에 `outlook.com email`를 입력합니다. **작업** 목록에서 아래로 스크롤하여 **이메일 보내기(V2)** 를 선택합니다.
  
    :::image type="content" source="./media/tutorial-geofence/logic-app-designer.png" alt-text="논리 앱 만들기 스크린샷":::

10. Outlook 계정에 로그인합니다. 논리 앱이 계정에 액세스할 수 있도록 **예**를 선택합니다. 이메일을 보내기 위한 필드를 채웁니다.

    :::image type="content" source="./media/tutorial-geofence/logic-app-email.png" alt-text="논리 앱 만들기 스크린샷":::

    >[!TIP]
    > 이메일 알림에서 `geometryId` 또는 `deviceId`와 같은 GeoJSON 응답 데이터를 검색할 수 있습니다. Event Grid에서 보낸 데이터를 읽도록 Logic Apps를 구성할 수 있습니다. 이벤트 데이터를 사용하고 이메일 알림에 전달하도록 Logic App을 구성하는 방법에 대한 자세한 내용은 [자습서: Event Grid 및 Logic Apps를 사용하여 Azure IoT Hub 이벤트에 대한 이메일 알림 보내기](https://docs.microsoft.com/azure/event-grid/publish-iot-hub-events-to-logic-apps)를 참조하세요.

11. 논리 앱 디자이너의 왼쪽 위 모서리에서 **저장**을 선택합니다.

장비가 건설 현장을 출입할 때 관리자에게 알리는 두 번째 논리 앱을 만들려면 3~11단계를 반복합니다. 논리 앱 이름을 `Equipment-Exit`로 지정합니다.

## <a name="create-azure-maps-events-subscriptions"></a>Azure Maps 이벤트 구독 만들기

Azure Maps는 [세 가지 이벤트 유형](https://docs.microsoft.com/azure/event-grid/event-schema-azure-maps)을 지원합니다. 여기서는 두 개의 서로 다른 이벤트 구독을 만들어야 합니다. 하나는 지오펜스 입장 이벤트이고, 다른 하나는 지오펜스 퇴장 이벤트입니다.

다음 단계에서는 지오펜스 입장 이벤트에 대한 이벤트 구독을 만드는 방법을 보여줍니다. 비슷한 방식으로 단계를 반복하여 지오펜스 퇴장 이벤트를 구독할 수 있습니다.

1. Azure Maps 계정으로 이동합니다. 대시보드에서 **구독**을 선택합니다. 구독 이름을 선택하고, 설정 메뉴에서 **이벤트**를 선택합니다.

    :::image type="content" source="./media/tutorial-geofence/events-tab.png" alt-text="논리 앱 만들기 스크린샷":::

2. 이벤트 구독을 만들려면 이벤트 페이지에서 **+ 이벤트 구독**을 선택합니다.

    :::image type="content" source="./media/tutorial-geofence/create-event-subscription.png" alt-text="논리 앱 만들기 스크린샷":::

3. **이벤트 구독 만들기** 페이지에서 다음 값을 입력합니다.
    * 이벤트 구독의 **이름**
    * **이벤트 스키마**는 *Event Grid 스키마*여야 합니다.
    * 이 이벤트 구독의 **시스템 토픽 이름**으로, 여기서는 `Contoso-Construction`입니다.
    * **이벤트 유형 필터**에서 이벤트 유형으로 `Geofence Entered`를 선택합니다.
    * **엔드포인트 유형**으로 `Web Hook`를 선택합니다.
    * **엔드포인트**에는 이전 섹션에서 만든 논리 앱 입장 엔드포인트의 HTTP POST URL을 복사합니다. 이 URL을 저장하지 않은 경우 논리 앱 디자이너로 돌아가서 HTTP 트리거 단계에서 복사하면 됩니다.

    :::image type="content" source="./media/tutorial-geofence/events-subscription.png" alt-text="논리 앱 만들기 스크린샷":::

4. **만들기**를 선택합니다.

이전 섹션에서 만든 논리 앱 퇴장 엔드포인트에 대해 1~4단계를 반복합니다. 3단계에서 이벤트 유형으로 `Geofence Exited`를 선택해야 합니다.

## <a name="use-spatial-geofence-get-api"></a>Spatial Geofence Get API 사용

[Spatial Geofence Get API](https://docs.microsoft.com/rest/api/maps/spatial/getgeofence)를 사용하여 장비가 지오펜스를 출입할 때 운영 관리자에게 이메일 알림을 보냅니다.

각 장비에는 `deviceId`가 있습니다. 이 자습서에서는 고유 ID가 `device_1`인 단일 장비를 추적합니다.

다음 다이어그램에서는 지오펜스 외부의 어딘가에 있는 *시작* 위치에서 시작하여 시간 경과에 따라 장비의 5개 위치를 보여줍니다. 이 자습서의 목적을 위해 해당 위치에서 디바이스를 쿼리하지 않으므로 *시작* 위치는 정의되지 않습니다.

초기 지오펜스 출입을 나타내는 장비 위치를 사용하여 [Spatial Geofence Get API](https://docs.microsoft.com/rest/api/maps/spatial/getgeofence)를 쿼리하면 Event Grid에서 적절한 논리 앱 엔드포인트를 호출하여 운영 관리자에게 이메일 알림을 보냅니다.

다음 각 섹션에서는 장비의 서로 다른 5개 위치 좌표를 사용하여 API 요청을 수행합니다.

![Azure Maps의 지오펜스 맵 다이어그램](./media/tutorial-geofence/geofence.png)

### <a name="equipment-location-1-47638237-122132483"></a>장비 위치 1(47.638237,-122.132483)

1. Postman 앱의 위쪽 근처에서 **새로 만들기**를 선택합니다. **새로 만들기** 창에서 **요청**을 선택합니다. 요청에 대한 **요청 이름**을 입력합니다. *위치 1*로 지정합니다. [지오펜싱 GeoJSON 데이터 업로드 섹션](#upload-geofencing-geojson-data)에서 만든 컬렉션을 선택한 다음, **저장**을 선택합니다.

2. 작성기 탭에서 **GET** HTTP 메서드를 선택하고, 다음 URL을 입력합니다. `{Azure-Maps-Primary-Subscription-key}`를 기본 구독 키로 바꾸고, `{udid}`를 [지오펜싱 GeoJSON 데이터 업로드 섹션](#upload-geofencing-geojson-data)에서 저장한 `udid`로 바꿔야 합니다.

   ```HTTP
   https://atlas.microsoft.com/spatial/geofence/json?subscription-key={subscription-key}&api-version=1.0&deviceId=device_01&udid={udid}&lat=47.638237&lon=-122.1324831&searchBuffer=5&isAsync=True&mode=EnterAndExit
   ```

3. **보내기**를 선택합니다. 응답 창에 다음과 같은 GeoJSON이 표시됩니다.

    ```json
    {
      "geometries": [
        {
          "deviceId": "device_1",
          "udId": "64f71aa5-bbee-942d-e351-651a6679a7da",
          "geometryId": "1",
          "distance": -999.0,
          "nearestLat": 47.638291,
          "nearestLon": -122.132483
        },
        {
          "deviceId": "device_1",
          "udId": "64f71aa5-bbee-942d-e351-651a6679a7da",
          "geometryId": "2",
          "distance": 999.0,
          "nearestLat": 47.638053,
          "nearestLon": -122.13295
        }
      ],
      "expiredGeofenceGeometryId": [],
      "invalidPeriodGeofenceGeometryId": [],
      "isEventPublished": true
    }
    ```

이전 GeoJSON 응답에서 주 사이트 지오펜스로부터의 음의 거리는 장비가 지오펜스 내부에 있음을 의미합니다. 하위 사이트 지오펜스로부터의 양의 거리는 해당 장비가 하위 사이트 지오펜스 외부에 있음을 의미합니다. 이 디바이스는 처음으로 주 사이트 지오펜스 내부에 배치되었기 때문에 `isEventPublished` 매개 변수가 `true`로 설정됩니다. 운영 관리자는 장비가 지오펜스에 들어왔다는 이메일 알림을 받습니다.

### <a name="location-2-4763800-122132531"></a>위치 2(47.63800,-122.132531)

1. Postman 앱의 위쪽 근처에서 **새로 만들기**를 선택합니다. **새로 만들기** 창에서 **요청**을 선택합니다. 요청에 대한 **요청 이름**을 입력합니다. *위치 2*로 지정합니다. [지오펜싱 GeoJSON 데이터 업로드 섹션](#upload-geofencing-geojson-data)에서 만든 컬렉션을 선택한 다음, **저장**을 선택합니다.

2. 작성기 탭에서 **GET** HTTP 메서드를 선택하고, 다음 URL을 입력합니다. `{Azure-Maps-Primary-Subscription-key}`를 기본 구독 키로 바꾸고, `{udid}`를 [지오펜싱 GeoJSON 데이터 업로드 섹션](#upload-geofencing-geojson-data)에서 저장한 `udid`로 바꿔야 합니다.

   ```HTTP
   https://atlas.microsoft.com/spatial/geofence/json?subscription-key={subscription-key}&api-version=1.0&deviceId=device_01&udId={udId}&lat=47.63800&lon=-122.132531&searchBuffer=5&isAsync=True&mode=EnterAndExit
   ```

3. **보내기**를 선택합니다. 응답 창에 다음과 같은 GeoJSON이 표시됩니다.

    ```json
    {
      "geometries": [
        {
          "deviceId": "device_01",
          "udId": "64f71aa5-bbee-942d-e351-651a6679a7da",
          "geometryId": "1",
          "distance": -999.0,
          "nearestLat": 47.637997,
          "nearestLon": -122.132399
        },
        {
          "deviceId": "device_01",
          "udId": "64f71aa5-bbee-942d-e351-651a6679a7da",
          "geometryId": "2",
          "distance": 999.0,
          "nearestLat": 47.63789,
          "nearestLon": -122.132809
        }
      ],
      "expiredGeofenceGeometryId": [],
      "invalidPeriodGeofenceGeometryId": [],
      "isEventPublished": false
    }
    ````

이전 GeoJSON 응답에서 장비는 주 사이트 지오펜스에 남아 있지만 하위 사이트 지오펜스에 들어가지 않았습니다. 따라서 `isEventPublished` 매개 변수가 `false`로 설정되고 운영 관리자에게 이메일 알림이 제공되지 않습니다.

### <a name="location-3-4763810783315048-12213336020708084"></a>위치 3(47.63810783315048,-122.13336020708084)

1. Postman 앱의 위쪽 근처에서 **새로 만들기**를 선택합니다. **새로 만들기** 창에서 **요청**을 선택합니다. 요청에 대한 **요청 이름**을 입력합니다. *위치 3*으로 지정합니다. [지오펜싱 GeoJSON 데이터 업로드 섹션](#upload-geofencing-geojson-data)에서 만든 컬렉션을 선택한 다음, **저장**을 선택합니다.

2. 작성기 탭에서 **GET** HTTP 메서드를 선택하고, 다음 URL을 입력합니다. `{Azure-Maps-Primary-Subscription-key}`를 기본 구독 키로 바꾸고, `{udid}`를 [지오펜싱 GeoJSON 데이터 업로드 섹션](#upload-geofencing-geojson-data)에서 저장한 `udid`로 바꿔야 합니다.

    ```HTTP
      https://atlas.microsoft.com/spatial/geofence/json?subscription-key={subscription-key}&api-version=1.0&deviceId=device_01&udid={udid}&lat=47.63810783315048&lon=-122.13336020708084&searchBuffer=5&isAsync=True&mode=EnterAndExit
      ```

3. **보내기**를 선택합니다. 응답 창에 다음과 같은 GeoJSON이 표시됩니다.

    ```json
    {
      "geometries": [
        {
          "deviceId": "device_01",
          "udId": "64f71aa5-bbee-942d-e351-651a6679a7da",
          "geometryId": "1",
          "distance": -999.0,
          "nearestLat": 47.638294,
          "nearestLon": -122.133359
        },
        {
          "deviceId": "device_01",
          "udId": "64f71aa5-bbee-942d-e351-651a6679a7da",
          "geometryId": "2",
          "distance": -999.0,
          "nearestLat": 47.638161,
          "nearestLon": -122.133549
        }
      ],
      "expiredGeofenceGeometryId": [],
      "invalidPeriodGeofenceGeometryId": [],
      "isEventPublished": true
    }
    ````

이전 GeoJSON 응답에서 장비는 주 사이트 지오펜스에 남아 있지만 하위 사이트 지오펜스에 들어갔습니다. 따라서 `isEventPublished` 매개 변수가 `true`로 설정됩니다. 운영 관리자는 장비가 지오펜스에 들어왔다는 이메일 알림을 받습니다.

>[!NOTE]
>근무 시간 이후에 장비가 하위 사이트로 이동하면 이벤트가 게시되지 않으며 운영 관리자에게 알림이 제공되지 않습니다.  

### <a name="location-4-47637988-1221338344"></a>위치 4(47.637988,-122.1338344)

1. Postman 앱의 위쪽 근처에서 **새로 만들기**를 선택합니다. **새로 만들기** 창에서 **요청**을 선택합니다. 요청에 대한 **요청 이름**을 입력합니다. *위치 4*로 지정합니다. [지오펜싱 GeoJSON 데이터 업로드 섹션](#upload-geofencing-geojson-data)에서 만든 컬렉션을 선택한 다음, **저장**을 선택합니다.

2. 작성기 탭에서 **GET** HTTP 메서드를 선택하고, 다음 URL을 입력합니다. `{Azure-Maps-Primary-Subscription-key}`를 기본 구독 키로 바꾸고, `{udid}`를 [지오펜싱 GeoJSON 데이터 업로드 섹션](#upload-geofencing-geojson-data)에서 저장한 `udid`로 바꿔야 합니다.

    ```HTTP
    https://atlas.microsoft.com/spatial/geofence/json?subscription-key={subscription-key}&api-version=1.0&deviceId=device_01&udid={udid}&lat=47.637988&userTime=2023-01-16&lon=-122.1338344&searchBuffer=5&isAsync=True&mode=EnterAndExit
    ```

3. **보내기**를 선택합니다. 응답 창에 다음과 같은 GeoJSON이 표시됩니다.

    ```json
    {
      "geometries": [
        {
          "deviceId": "device_01",
          "udId": "64f71aa5-bbee-942d-e351-651a6679a7da",
          "geometryId": "1",
          "distance": -999.0,
          "nearestLat": 47.637985,
          "nearestLon": -122.133907
        }
      ],
      "expiredGeofenceGeometryId": [
        "2"
      ],
      "invalidPeriodGeofenceGeometryId": [],
      "isEventPublished": false
    }
    ````

이전 GeoJSON 응답에서 장비는 주 사이트 지오펜스에 남아 있지만 하위 사이트 지오펜스에서 나갔습니다. 그러나 `userTime` 값은 지오펜스 데이터에 정의된 `expiredTime` 이후입니다. 따라서 `isEventPublished` 매개 변수가 `false`로 설정되고 운영 관리자에게 이메일 알림이 제공되지 않습니다.

### <a name="location-5-4763799--122134505"></a>위치 5(47.63799, -122.134505)

1. Postman 앱의 위쪽 근처에서 **새로 만들기**를 선택합니다. **새로 만들기** 창에서 **요청**을 선택합니다. 요청에 대한 **요청 이름**을 입력합니다. *위치 5*로 지정합니다. [지오펜싱 GeoJSON 데이터 업로드 섹션](#upload-geofencing-geojson-data)에서 만든 컬렉션을 선택한 다음, **저장**을 선택합니다.

2. 작성기 탭에서 **GET** HTTP 메서드를 선택하고, 다음 URL을 입력합니다. `{Azure-Maps-Primary-Subscription-key}`를 기본 구독 키로 바꾸고, `{udid}`를 [지오펜싱 GeoJSON 데이터 업로드 섹션](#upload-geofencing-geojson-data)에서 저장한 `udid`로 바꿔야 합니다.

    ```HTTP
    https://atlas.microsoft.com/spatial/geofence/json?subscription-key={subscription-key}&api-version=1.0&deviceId=device_01&udid={udid}&lat=47.63799&lon=-122.134505&searchBuffer=5&isAsync=True&mode=EnterAndExit
    ```

3. **보내기**를 선택합니다. 응답 창에 다음과 같은 GeoJSON이 표시됩니다.

    ```json
    {
      "geometries": [
      {
        "deviceId": "device_01",
        "udId": "64f71aa5-bbee-942d-e351-651a6679a7da",
        "geometryId": "1",
        "distance": -999.0,
        "nearestLat": 47.637985,
        "nearestLon": -122.133907
      },
      {
        "deviceId": "device_01",
        "udId": "64f71aa5-bbee-942d-e351-651a6679a7da",
        "geometryId": "2",
        "distance": 999.0,
        "nearestLat": 47.637945,
        "nearestLon": -122.133683
      }
      ],
      "expiredGeofenceGeometryId": [],
      "invalidPeriodGeofenceGeometryId": [],
      "isEventPublished": true
    }
    ````

이전 GeoJSON 응답에서 장비가 주 사이트 지오펜스에서 나갔습니다. 따라서 `isEventPublished` 매개 변수가 `true`로 설정되고 운영 관리자는 장비가 지오펜스에서 나갔다는 이메일 알림을 받습니다.


[Event Grid 및 Logic Apps를 사용하여 이메일 알림을 보내](https://docs.microsoft.com/azure/event-grid/publish-iot-hub-events-to-logic-apps)고 Azure Maps를 사용하여 [Event Grid에서 지원되는 이벤트 처리기](https://docs.microsoft.com/azure/event-grid/event-handlers)를 확인할 수도 있습니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Azure Logic Apps에서 콘텐츠 형식 처리](https://docs.microsoft.com/azure/logic-apps/logic-apps-content-type)
