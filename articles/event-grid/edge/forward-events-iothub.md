---
title: 이벤트 그리드 이벤트를 IoTHub로 전달 - Azure 이벤트 그리드 IoT 에지 | 마이크로 소프트 문서
description: 이벤트 그리드 이벤트를 IoTHub로 전달
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/29/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: d0034810ff86de2a40e275ca54a2f0f9cbc856c2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76844703"
---
# <a name="tutorial-forward-events-to-iothub"></a>자습서: 이벤트를 IoTHub로 전달

이 문서에서는 라우트를 사용하여 이벤트 그리드 이벤트를 다른 IoT Edge 모듈인 IoTHub로 전달하는 데 필요한 모든 단계를 안내합니다. 다음과 같은 이유로 작업을 수행할 수 있습니다.

* edgeHub의 라우팅을 통해 이미 기존 투자를 계속 사용
* IoT Hub를 통해서만 디바이스의 모든 이벤트를 라우팅하는 것을 선호합니다.

이 자습서를 완료하려면 다음 개념을 이해해야 합니다.

- [이벤트 그리드 개념](concepts.md)
- [IoT Edge 허브](../../iot-edge/module-composition.md) 

## <a name="prerequisites"></a>사전 요구 사항 
이 자습서를 완료하려면 다음과 같은 요건이 필요합니다.

* **Azure 구독** - 아직 계정이 없는 경우 [무료 계정을](https://azure.microsoft.com/free) 만듭니다. 
* **Azure IoT Hub 및 IoT Edge 장치** - 아직 없는 경우 [Linux](../../iot-edge/quickstart-linux.md) 또는 [Windows 장치의](../../iot-edge/quickstart.md) 빠른 시작 단계를 따릅니다.

[!INCLUDE [event-grid-deploy-iot-edge](../../../includes/event-grid-deploy-iot-edge.md)]

## <a name="create-topic"></a>토픽 만들기

이벤트 게시자는 이벤트 그리드 항목을 만들어야 합니다. 이 항목은 게시자가 이벤트를 보낼 수 있는 끝점을 나타냅니다.

1. 다음 콘텐츠로 topic4.json을 만듭니다. 페이로드에 대한 자세한 내용은 [API 설명서를](api.md) 참조하십시오.

   ```json
    {
          "name": "sampleTopic4",
          "properties": {
            "inputschema": "eventGridSchema"
          }
    }
    ```
1. 다음 명령을 실행하여 토픽을 만듭니다. 200 확인의 HTTP 상태 코드는 반환해야 합니다.

    ```sh
    curl -k -H "Content-Type: application/json" -X PUT -g -d @topic4.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic4?api-version=2019-01-01-preview
    ```

1. 다음 명령을 실행하여 항목이 성공적으로 만들어졌는지 확인합니다. 200 확인의 HTTP 상태 코드는 반환해야 합니다.

    ```sh
    curl -k -H "Content-Type: application/json" -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic4?api-version=2019-01-01-preview
    ```

   샘플 출력:

   ```json
        [
          {
            "id": "/iotHubs/eg-iot-edge-hub/devices/eg-edge-device/modules/eventgridmodule/topics/sampleTopic4",
            "name": "sampleTopic4",
            "type": "Microsoft.EventGrid/topics",
            "properties": {
              "endpoint": "https://<edge-vm-ip>:4438/topics/sampleTopic4/events?api-version=2019-01-01-preview",
              "inputSchema": "EventGridSchema"
            }
          }
        ]
   ```

## <a name="create-event-subscription"></a>이벤트 구독 만들기

구독자는 토픽에 게시된 이벤트에 등록할 수 있습니다. 이벤트를 받으려면 관심 있는 주제에 대해 이벤트 그리드 구독을 만들어야 합니다.

[!INCLUDE [event-grid-deploy-iot-edge](../../../includes/event-grid-edge-persist-event-subscriptions.md)]

1. 아래 콘텐츠로 구독4.json을 만듭니다. 페이로드에 대한 자세한 내용은 [API 설명서를](api.md) 참조하십시오.

   ```json
    {
          "properties": {
                "destination": {
                      "endpointType": "edgeHub",
                      "properties": {
                            "outputName": "sampleSub4"
                      }
                }
          }
    }
   ```

   >[!NOTE]
   > 구독자가 `endpointType` `edgeHub`을 지정합니다. 는 `outputName` 이벤트 그리드 모듈이 이 구독과 일치하는 이벤트를 edgeHub로 라우팅할 출력을 지정합니다. 예를 들어 위의 구독과 일치하는 이벤트가 `/messages/modules/eventgridmodule/outputs/sampleSub4`에 기록됩니다.
2. 다음 명령을 실행하여 구독을 만듭니다. 200 확인의 HTTP 상태 코드는 반환해야 합니다.

    ```sh
    curl -k -H "Content-Type: application/json" -X PUT -g -d @subscription4.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic4/eventSubscriptions/sampleSubscription4?api-version=2019-01-01-preview
    ```
3. 다음 명령을 실행하여 구독이 성공적으로 만들어졌는지 확인합니다. 200 확인의 HTTP 상태 코드는 반환해야 합니다.

    ```sh
    curl -k -H "Content-Type: application/json" -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic4/eventSubscriptions/sampleSubscription4?api-version=2019-01-01-preview
    ```

    샘플 출력:

   ```json
        {
          "id": "/iotHubs/eg-iot-edge-hub/devices/eg-edge-device/modules/eventgridmodule/topics/sampleTopic4/eventSubscriptions/sampleSubscription4",
          "type": "Microsoft.EventGrid/eventSubscriptions",
          "name": "sampleSubscription4",
          "properties": {
            "Topic": "sampleTopic4",
            "destination": {
                      "endpointType": "edgeHub",
                      "properties": {
                            "outputName": "sampleSub4"
                      }
                }
          }
        }
    ```

## <a name="set-up-an-edge-hub-route"></a>에지 허브 경로 설정

이벤트 구독의 이벤트를 전달하기 위해 에지 허브의 경로를 업데이트하여 다음과 같이 IoTHub로 전달합니다.

1. [Azure 포털에](https://ms.portal.azure.com) 로그인
1. **IoT 허브로**이동합니다.
1. 메뉴에서 **IoT 에지** 선택
1. 장치 목록에서 대상 장치의 ID를 선택합니다.
1. **모듈 설정**선택 .
1. **다음으로** 경로 섹션을 선택합니다.
1. 경로에서 새 경로를 추가합니다.

  ```sh
  "fromEventGridToIoTHub":"FROM /messages/modules/eventgridmodule/outputs/sampleSub4 INTO $upstream"
  ```

  예를 들면 다음과 같습니다.

  ```json
  {
      "routes": {
        "fromEventGridToIoTHub": "FROM /messages/modules/eventgridmodule/outputs/sampleSub4 INTO $upstream"
      }
  }
  ```

   >[!NOTE]
   > 위의 경로는 이 구독과 일치하는 모든 이벤트를 IoT 허브로 전달합니다. Edge 허브 [라우팅](../../iot-edge/module-composition.md) 기능을 사용하여 추가 필터링하고 이벤트 그리드 이벤트를 다른 IoT Edge 모듈로 라우팅할 수 있습니다.

## <a name="setup-iot-hub-route"></a>설정 IoT 허브 경로

이벤트 그리드 모듈에서 전달된 이벤트를 볼 수 있도록 [IoT Hub 라우팅 자습서를](../../iot-hub/tutorial-routing.md) 참조하여 IoT 허브에서 경로를 설정합니다. 쿼리를 사용하여 `true` 자습서를 단순하게 유지합니다.  



## <a name="publish-an-event"></a>이벤트 게시

1. 다음 콘텐츠로 event4.json을 만듭니다. 페이로드에 대한 자세한 내용은 [API 설명서를](api.md) 참조하십시오.

    ```json
        [
          {
            "id": "eventId-iothub-1",
            "eventType": "recordInserted",
            "subject": "myapp/vehicles/motorcycles",
            "eventTime": "2019-07-28T21:03:07+00:00",
            "dataVersion": "1.0",
            "data": {
              "make": "Ducati",
              "model": "Monster"
            }
          }
        ]
    ```

1. 다음 명령을 실행하여 이벤트를 게시합니다.

    ```sh
    curl -k -H "Content-Type: application/json" -X POST -g -d @event4.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic4/events?api-version=2019-01-01-preview
    ```

## <a name="verify-event-delivery"></a>이벤트 전달 확인

이벤트를 보려면 IoT Hub [라우팅 자습서를](../../iot-hub/tutorial-routing.md) 참조하십시오.

## <a name="cleanup-resources"></a>리소스 정리

* 다음 명령을 실행하여 에지에서 토픽 및 모든 구독을 삭제합니다.

    ```sh
    curl -k -H "Content-Type: application/json" -X DELETE https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic4?api-version=2019-01-01-preview
    ```
* 클라우드에서 IoTHub 라우팅을 설정하는 동안 생성된 모든 리소스도 삭제합니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 이벤트 그리드 항목, 에지 허브 구독 및 게시된 이벤트를 만들었습니다. 이제 에지 허브로 전달하는 기본 단계를 알고 있으므로 다음 문서를 참조하십시오.

* IoT Edge에서 Azure 이벤트 그리드를 사용하는 문제를 해결하려면 [문제 해결 가이드를](troubleshoot.md)참조하십시오.
* [에지 허브](../../iot-edge/module-composition.md) 경로 필터를 사용하여 이벤트를 분할
* [리눅스](persist-state-linux.md) 또는 [윈도우에서](persist-state-windows.md) 이벤트 그리드 모듈의 지속성 설정
* [설명서를](configure-client-auth.md) 따라 클라이언트 인증 구성
* 이 [자습서를](forward-events-event-grid-cloud.md) 따라 이벤트를 클라우드에서 Azure 이벤트 그리드로 전달
* [에지에서 주제 및 구독 모니터링](monitor-topics-subscriptions.md)