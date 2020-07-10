---
title: Event Grid 클라우드 Azure Event Grid IoT Edge로 edge 이벤트 전달 Microsoft Docs
description: Event Grid 클라우드로 edge 이벤트 전달
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 07/08/2020
ms.topic: article
ms.openlocfilehash: bfe150a45c70bc5bed18f8e929c9567905cd38f5
ms.sourcegitcommit: 1e6c13dc1917f85983772812a3c62c265150d1e7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/09/2020
ms.locfileid: "86171604"
---
# <a name="tutorial-forward-events-to-event-grid-cloud"></a>자습서: Event Grid 클라우드에 이벤트 전달

이 문서에서는 edge 이벤트를 Azure 클라우드에서 Event Grid으로 전달 하는 데 필요한 모든 단계를 안내 합니다. 다음과 같은 이유로이 작업을 수행 하는 것이 좋습니다.

* 클라우드의 edge 이벤트에 대응 합니다.
* 클라우드에서 Event Grid에 이벤트를 전달 하 고 Azure Event Hubs 또는 Azure Storage 큐를 사용 하 여 클라우드에서 처리 하기 전에 이벤트를 버퍼링 합니다.

 이 자습서를 완료 하려면 [edge](concepts.md) 및 [Azure](../concepts.md)에 대 한 Event Grid 개념을 이해 해야 합니다. 추가 대상 형식은 [이벤트 처리기](event-handlers.md)를 참조 하세요. 

## <a name="prerequisites"></a>필수 구성 요소 
이 자습서를 완료하려면 다음과 같은 요건이 필요합니다.

* **Azure 구독** -아직 없는 경우 [무료 계정](https://azure.microsoft.com/free) 을 만듭니다. 
* **Azure IoT Hub 및 IoT Edge 장치** - [Linux](../../iot-edge/quickstart-linux.md) 또는 [Windows 장치](../../iot-edge/quickstart.md) 에 대 한 빠른 시작 (아직 없는 경우)의 단계를 따릅니다.

[!INCLUDE [event-grid-deploy-iot-edge](../../../includes/event-grid-deploy-iot-edge.md)] 
## <a name="create-event-grid-topic-and-subscription-in-cloud"></a>클라우드에서 event grid 토픽 및 구독 만들기

[이 자습서](../custom-event-quickstart-portal.md)를 수행 하 여 클라우드에서 event grid 토픽 및 구독을 만듭니다. 자습서의 `topicURL` `sasKey` `topicName` 뒷부분에서 사용할 새로 만든 토픽의, 및를 적어둡니다.

예를 들어 미국 서 부에 라는 토픽을 만든 경우 `testegcloudtopic` 값은 다음과 같습니다.

* **TopicUrl**:`https://testegcloudtopic.westus2-1.eventgrid.azure.net/api/events`
* **TopicName**:`testegcloudtopic`
* **SasKey**: 항목의 **AccessKey** 에서 사용할 수 있습니다. **Key1**을 사용 합니다.

## <a name="create-event-grid-topic-at-the-edge"></a>Edge에서 event grid 토픽을 만듭니다.

1. 다음 콘텐츠를 사용 하 여 topic3.js를 만듭니다. 페이로드에 대 한 자세한 내용은 [API 설명서](api.md) 를 참조 하세요.

    ```json
        {
          "name": "sampleTopic3",
          "properties": {
            "inputschema": "eventGridSchema"
          }
        }
    ```
1. 다음 명령을 실행 하 여 토픽을 만듭니다. HTTP 상태 코드 200을 반환 해야 합니다.

    ```sh
    curl -k -H "Content-Type: application/json" -X PUT -g -d @topic3.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic3?api-version=2019-01-01-preview
    ```
1. 다음 명령을 실행 하 여 항목이 성공적으로 만들어졌는지 확인 합니다. HTTP 상태 코드 200을 반환 해야 합니다.

    ```sh
    curl -k -H "Content-Type: application/json" -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic3?api-version=2019-01-01-preview
    ```

   샘플 출력:

   ```json
        [
          {
            "id": "/iotHubs/eg-iot-edge-hub/devices/eg-edge-device/modules/eventgridmodule/topics/sampleTopic3",
            "name": "sampleTopic3",
            "type": "Microsoft.EventGrid/topics",
            "properties": {
              "endpoint": "https://<edge-vm-ip>:4438/topics/sampleTopic3/events?api-version=2019-01-01-preview",
              "inputSchema": "EventGridSchema"
            }
          }
        ]
   ```
  
## <a name="create-event-grid-subscription-at-the-edge"></a>에 지에서 Event Grid 구독 만들기

[!INCLUDE [event-grid-deploy-iot-edge](../../../includes/event-grid-edge-persist-event-subscriptions.md)]

1. 다음 콘텐츠를 사용 하 여 subscription3.js를 만듭니다. 페이로드에 대 한 자세한 내용은 [API 설명서](api.md) 를 참조 하세요.

   ```json
        {
          "properties": {
            "destination": {
              "endpointType": "eventGrid",
              "properties": {
                        "endpointUrl": "<your-event-grid-cloud-topic-endpoint-url>?api-version=2018-01-01",
                        "sasKey": "<your-event-grid-topic-saskey>",
                        "topicName": null
              }
            }
          }
    }
   ```

   >[!NOTE]
   > **Endpointurl** 은 클라우드의 EVENT GRID 토픽 url을 지정 합니다. **SasKey** 는 Event Grid 클라우드 토픽의 키를 참조 합니다. **TopicName** 의 값을 사용 하 여 나가는 모든 이벤트를 Event Grid에 스탬프를 지정할 수 있습니다. 이는 Event Grid 도메인 항목에 게시할 때 유용할 수 있습니다. Event Grid 도메인 항목에 대 한 자세한 내용은 [이벤트 도메인](../event-domains.md) 을 참조 하세요.

    예를 들면 다음과 같습니다.
  
    ```json
        {
          "properties": {
            "destination": {
              "endpointType": "eventGrid",
              "properties": {
                "endpointUrl": "https://testegcloudtopic.westus2-1.eventgrid.azure.net/api/events?api-version=2018-01-01",
                 "sasKey": "<your-event-grid-topic-saskey>",
                 "topicName": null
              }
            }
          }
        }
    ```

2. 다음 명령을 실행 하 여 구독을 만듭니다. HTTP 상태 코드 200을 반환 해야 합니다.

     ```sh
     curl -k -H "Content-Type: application/json" -X PUT -g -d @subscription3.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic3/eventSubscriptions/sampleSubscription3?api-version=2019-01-01-preview
     ```

3. 다음 명령을 실행 하 여 구독이 성공적으로 만들어졌는지 확인 합니다. HTTP 상태 코드 200을 반환 해야 합니다.

    ```sh
    curl -k -H "Content-Type: application/json" -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic3/eventSubscriptions/sampleSubscription3?api-version=2019-01-01-preview
    ```

    샘플 출력:

    ```json
         {
          "id": "/iotHubs/eg-iot-edge-hub/devices/eg-edge-device/modules/eventgridmodule/topics/sampleTopic3/eventSubscriptions/sampleSubscription3",
          "type": "Microsoft.EventGrid/eventSubscriptions",
          "name": "sampleSubscription3",
          "properties": {
            "Topic": "sampleTopic3",
            "destination": {
              "endpointType": "eventGrid",
              "properties": {
                "endpointUrl": "https://testegcloudtopic.westus2-1.eventgrid.azure.net/api/events?api-version=2018-01-01",
                "sasKey": "<your-event-grid-topic-saskey>",
                "topicName": null
              }
            }
          }
        }
    ```

## <a name="publish-an-event-at-the-edge"></a>에 지에 이벤트 게시

1. 다음 콘텐츠를 사용 하 여 event3.js를 만듭니다. 페이로드에 대 한 자세한 내용은 [API 설명서](api.md) 를 참조 하세요.

    ```json
        [
          {
            "id": "eventId-egcloud-0",
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

1. 다음 명령을 실행합니다.

    ```sh
    curl -k -H "Content-Type: application/json" -X POST -g -d @event3.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic3/events?api-version=2019-01-01-preview
    ```

## <a name="verify-edge-event-in-cloud"></a>클라우드에서 edge 이벤트 확인

클라우드 항목에서 제공 하는 이벤트를 보는 방법에 대 한 자세한 내용은 [자습서](../custom-event-quickstart-portal.md)를 참조 하세요.

## <a name="cleanup-resources"></a>리소스 정리

* 다음 명령을 실행 하 여 토픽 및 모든 해당 구독을 삭제 합니다.

    ```sh
    curl -k -H "Content-Type: application/json" -X DELETE https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic3?api-version=2019-01-01-preview
    ```

* 클라우드에서 만든 토픽 및 구독 (Azure Event Grid)도 삭제 합니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는에 지에 이벤트를 게시 하 고 Azure 클라우드에서 Event Grid으로 전달 했습니다. 이제 클라우드에서 Event Grid으로 전달 하는 기본 단계를 알고 있습니다.

* IoT Edge에서 Azure Event Grid를 사용 하 여 발생 하는 문제를 해결 하려면 [문제 해결 가이드](troubleshoot.md)를 참조 하세요.
* 이 [자습서](forward-events-iothub.md) 에 따라 IoTHub로 이벤트 전달
* 이 [자습서](pub-sub-events-webhook-cloud.md) 에 따라 클라우드의 Webhook에 이벤트 전달
* [Edge에서 토픽 및 구독 모니터링](monitor-topics-subscriptions.md)
