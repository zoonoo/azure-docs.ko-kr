---
title: IoTHub에 Event Grid 이벤트 전달 - IoT Edge의 Azure Event Grid | Microsoft Docs
description: IoTHub에 Event Grid 이벤트 전달
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.subservice: iot-edge
ms.date: 05/10/2021
ms.topic: article
ms.openlocfilehash: 30fa58a79068a1dc367f4ac330efd01685a4401c
ms.sourcegitcommit: 5163ebd8257281e7e724c072f169d4165441c326
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/21/2021
ms.locfileid: "112415591"
---
# <a name="tutorial-forward-events-to-iothub"></a>자습서: IoTHub에 이벤트 전달

이 문서에서는 경로를 사용하여 다른 IoT Edge 모듈인 IoTHub에 Event Grid 이벤트를 전달하는 데 필요한 모든 단계를 설명합니다. 이 작업은 다음과 같은 이유로 수행할 수 있습니다.

* 이미 갖춰져 있는 기존 투자를 edgeHub의 경로 지정에서 계속 사용하려고 합니다.
* IoT Hub를 통해서만 디바이스의 모든 이벤트 경로를 지정하려고 합니다.

이 자습서를 완료하려면 다음 개념을 이해하고 있어야 합니다.

- [Event Grid 개념](concepts.md)
- [IoT Edge 허브](../../iot-edge/module-composition.md) 

## <a name="prerequisites"></a>필수 구성 요소 
이 자습서를 완료하려면 다음과 같은 요건이 필요합니다.

* **Azure 구독** - 아직 없는 경우 [체험 계정](https://azure.microsoft.com/free)을 만듭니다. 
* **Azure IoT Hub 및 IoT Edge 디바이스** - 아직 없는 경우 [Linux](../../iot-edge/quickstart-linux.md) 또는 [Windows 디바이스](../../iot-edge/quickstart.md)의 빠른 시작 단계를 수행합니다.

[!INCLUDE [event-grid-deploy-iot-edge](../includes/event-grid-deploy-iot-edge.md)]

## <a name="create-topic"></a>토픽 만들기

이벤트 게시자는 Event Grid 토픽을 만들어야 합니다. 토픽은 게시자가 이벤트를 보낼 수 있는 엔드포인트를 나타냅니다.

1. 다음 콘텐츠를 사용하여 topic4.json을 만듭니다. 페이로드에 대한 자세한 내용은 [API 설명서](api.md)를 참조하세요.

   ```json
    {
          "name": "sampleTopic4",
          "properties": {
            "inputschema": "eventGridSchema"
          }
    }
    ```
1. 다음 명령을 실행하여 토픽을 만듭니다. HTTP 상태 코드 200 OK가 반환되어야 합니다.

    ```sh
    curl -k -H "Content-Type: application/json" -X PUT -g -d @topic4.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic4?api-version=2019-01-01-preview
    ```

1. 다음 명령을 실행하여 토픽이 성공적으로 만들어졌는지 확인합니다. HTTP 상태 코드 200 OK가 반환되어야 합니다.

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

구독자는 토픽에 게시된 이벤트에 등록할 수 있습니다. 이벤트를 수신하려면 관심 있는 토픽에 Event Grid 구독을 만들어야 합니다.

[!INCLUDE [event-grid-deploy-iot-edge](../includes/event-grid-edge-persist-event-subscriptions.md)]

1. 아래 콘텐츠를 사용하여 subscription4.json을 만듭니다. 페이로드에 대한 자세한 내용은 [API 설명서](api.md)를 참조하세요.

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
   > `endpointType`은 구독자를 `edgeHub`로 지정합니다. `outputName`은 Event Grid 모듈에서 이 구독과 일치하는 이벤트를 edgeHub로 경로 지정할 출력을 지정합니다. 예를 들어 위의 구독과 일치하는 이벤트는 `/messages/modules/eventgridmodule/outputs/sampleSub4`에 기록됩니다.
2. 다음 명령을 실행하여 구독을 만듭니다. HTTP 상태 코드 200 OK가 반환되어야 합니다.

    ```sh
    curl -k -H "Content-Type: application/json" -X PUT -g -d @subscription4.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic4/eventSubscriptions/sampleSubscription4?api-version=2019-01-01-preview
    ```
3. 다음 명령을 실행하여 구독이 성공적으로 만들어졌는지 확인합니다. HTTP 상태 코드 200 OK가 반환되어야 합니다.

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

다음과 같이 이벤트 구독의 이벤트를 IoTHub에 전달하도록 에지 허브 경로를 업데이트합니다.

1. [Azure 포털](https://ms.portal.azure.com)
1. **IoT Hub** 로 이동합니다.
1. 메뉴에서 **IoT Edge** 를 선택합니다.
1. 디바이스 목록에서 대상 디바이스의 ID를 선택합니다.
1. **모듈 설정** 을 선택합니다.
1. **다음** 을 선택하여 경로 섹션에서 계속합니다.
1. 경로에서 새 경로를 추가합니다.

  ```sh
  "fromEventGridToIoTHub":"FROM /messages/modules/eventgridmodule/outputs/sampleSub4 INTO $upstream"
  ```

  예제:

  ```json
  {
      "routes": {
        "fromEventGridToIoTHub": "FROM /messages/modules/eventgridmodule/outputs/sampleSub4 INTO $upstream"
      }
  }
  ```

   >[!NOTE]
   > 위의 경로에서는 이 구독과 일치하는 모든 이벤트를 IoT Hub로 전달합니다. [에지 허브 경로 지정](../../iot-edge/module-composition.md) 기능을 사용하여 추가로 필터링하고 Event Grid 이벤트를 다른 IoT Edge 모듈로 경로 지정할 수 있습니다.

## <a name="setup-iot-hub-route"></a>IoT Hub 경로 설정

Event Grid 모듈에서 전달된 이벤트를 볼 수 있도록 IoT Hub에서 경로를 설정하려면 [IoT Hub 경로 지정 자습서](../../iot-hub/tutorial-routing.md)를 참조하세요. 쿼리에서 자습서를 간단하게 유지하도록 하려면 `true`를 사용합니다.  



## <a name="publish-an-event"></a>이벤트 게시

1. 다음 콘텐츠로 event4.json을 만듭니다. 페이로드에 대한 자세한 내용은 [API 설명서](api.md)를 참조하세요.

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

## <a name="verify-event-delivery"></a>이벤트 전송 확인

이벤트를 보는 단계는 IoT Hub [경로 지정 자습서](../../iot-hub/tutorial-routing.md)를 참조하세요.

## <a name="cleanup-resources"></a>리소스 정리

* 다음 명령을 실행하여 에지의 토픽 및 모든 해당 구독을 삭제합니다.

    ```sh
    curl -k -H "Content-Type: application/json" -X DELETE https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic4?api-version=2019-01-01-preview
    ```
* 클라우드에서 IoTHub 경로를 설정하는 동안 만들어진 리소스도 모두 삭제합니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 Event Grid 토픽, 에지 허브 구독, 게시된 이벤트를 만들었습니다. 이제 에지 허브로 전달하는 기본 단계를 알고 있으므로 다음 문서를 참조하세요.

* IoT Edge의 Azure Event Grid를 사용하는 데 발생하는 문제를 해결하려면 [문제 해결 가이드](troubleshoot.md)를 참조하세요.
* [에지 허브](../../iot-edge/module-composition.md) 경로 필터를 사용하여 이벤트 분할
* [Linux](persist-state-linux.md) 또는 [Windows](persist-state-windows.md)에서 Event Grid 모듈에 대한 지속성 설정
* [설명서](configure-client-auth.md)에 따라 클라이언트 인증 구성
* 이 [자습서](forward-events-event-grid-cloud.md)에 따라 클라우드에서 Azure Event Grid로 이벤트 전달
* [에지에서 토픽 및 구독 모니터링](monitor-topics-subscriptions.md)