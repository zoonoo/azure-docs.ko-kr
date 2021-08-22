---
title: Event Grid 클라우드로 에지 이벤트 전달 - IoT Edge의 Azure Event Grid | Microsoft Docs
description: Event Grid 클라우드로 에지 이벤트를 전달합니다.
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.subservice: iot-edge
ms.date: 05/10/2021
ms.topic: article
ms.openlocfilehash: d1732e6f997af622bd7435b6b2f1a3033bc05a79
ms.sourcegitcommit: 5163ebd8257281e7e724c072f169d4165441c326
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/21/2021
ms.locfileid: "112412734"
---
# <a name="tutorial-forward-events-to-event-grid-cloud"></a>자습서: Event Grid 클라우드로 이벤트 전달

이 문서에서는 Azure 클라우드에서 Event Grid로 에지 이벤트를 전달하는 데 필요한 모든 단계를 설명합니다. 이 작업은 다음과 같은 이유로 수행할 수 있습니다.

* 클라우드에서 에지 이벤트에 대응합니다.
* 클라우드에서 Event Grid에 이벤트를 전달하고 Azure Event Hubs 또는 Azure Storage 큐를 사용하여 클라우드에서 이벤트를 처리하기 전에 이벤트를 버퍼링합니다.

 이 자습서를 완료하려면 [에지](concepts.md) 및 [Azure](../concepts.md)의 Event Grid 개념을 이해하고 있어야 합니다. 추가 대상 유형은 [이벤트 처리기](event-handlers.md)를 참조하세요. 

## <a name="prerequisites"></a>사전 요구 사항 
이 자습서를 완료하려면 다음과 같은 요건이 필요합니다.

* **Azure 구독** - 아직 없는 경우 [체험 계정](https://azure.microsoft.com/free)을 만듭니다. 
* **Azure IoT Hub 및 IoT Edge 디바이스** - 아직 없는 경우 [Linux](../../iot-edge/quickstart-linux.md) 또는 [Windows 디바이스](../../iot-edge/quickstart.md)의 빠른 시작 단계를 수행합니다.

[!INCLUDE [event-grid-deploy-iot-edge](../includes/event-grid-deploy-iot-edge.md)] 
## <a name="create-event-grid-topic-and-subscription-in-cloud"></a>클라우드에서 Event Grid 토픽 및 구독 만들기

[이 자습서](../custom-event-quickstart-portal.md)에 따라 클라우드에서 Event Grid 토픽 및 구독을 만듭니다. 자습서의 뒷부분에서 사용할 새로 만든 토픽의 `topicURL`, `sasKey`, `topicName`을 적어둡니다.

예를 들어 미국 서부에 `testegcloudtopic`이라는 토픽을 만든 경우 값은 다음과 같습니다.

* **TopicUrl**: `https://testegcloudtopic.westus2-1.eventgrid.azure.net/api/events`
* **TopicName**: `testegcloudtopic`
* **SasKey**: 토픽의 **AccessKey** 아래에 있습니다. **key1** 을 사용합니다.

## <a name="create-event-grid-topic-at-the-edge"></a>에지에서 Event Grid 토픽 만들기

1. 다음 콘텐츠를 사용하여 topic3.json을 만듭니다. 페이로드에 대한 자세한 내용은 [API 설명서](api.md)를 참조하세요.

    ```json
        {
          "name": "sampleTopic3",
          "properties": {
            "inputschema": "eventGridSchema"
          }
        }
    ```
1. 다음 명령을 실행하여 토픽을 만듭니다. HTTP 상태 코드 200 OK가 반환되어야 합니다.

    ```sh
    curl -k -H "Content-Type: application/json" -X PUT -g -d @topic3.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic3?api-version=2019-01-01-preview
    ```
1. 다음 명령을 실행하여 토픽이 성공적으로 만들어졌는지 확인합니다. HTTP 상태 코드 200 OK가 반환되어야 합니다.

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
  
## <a name="create-event-grid-subscription-at-the-edge"></a>에지에서 Event Grid 구독 만들기

[!INCLUDE [event-grid-deploy-iot-edge](../includes/event-grid-edge-persist-event-subscriptions.md)]

1. 다음 콘텐츠를 사용하여 subscription3.json을 만듭니다. 페이로드에 대한 자세한 내용은 [API 설명서](api.md)를 참조하세요.

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
   > **endpointUrl** 은 클라우드의 Event Grid 토픽 URL을 지정합니다. **sasKey** 는 Event Grid 클라우드 토픽의 키를 나타냅니다. **topicName** 의 값은 Event Grid에 모든 발신 이벤트 스탬프를 지정하는 데 사용됩니다. Event Grid 도메인 토픽에 게시하는 경우 유용할 수 있습니다. Event Grid 도메인 토픽에 대한 자세한 내용은 [이벤트 도메인](../event-domains.md)을 참조하세요.

    예제:
  
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

2. 다음 명령을 실행하여 구독을 만듭니다. HTTP 상태 코드 200 OK가 반환되어야 합니다.

     ```sh
     curl -k -H "Content-Type: application/json" -X PUT -g -d @subscription3.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic3/eventSubscriptions/sampleSubscription3?api-version=2019-01-01-preview
     ```

3. 다음 명령을 실행하여 구독이 성공적으로 만들어졌는지 확인합니다. HTTP 상태 코드 200 OK가 반환되어야 합니다.

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

## <a name="publish-an-event-at-the-edge"></a>에지에서 이벤트 게시

1. 다음 콘텐츠로 event3.json을 만듭니다. 페이로드에 대한 자세한 내용은 [API 설명서](api.md)를 참조하세요.

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

1. 다음 명령 실행:

    ```sh
    curl -k -H "Content-Type: application/json" -X POST -g -d @event3.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic3/events?api-version=2019-01-01-preview
    ```

## <a name="verify-edge-event-in-cloud"></a>클라우드에서 에지 이벤트 확인

클라우드 토픽을 통해 전송된 이벤트를 보는 방법에 대한 자세한 내용은 [자습서](../custom-event-quickstart-portal.md)를 참조하세요.

## <a name="cleanup-resources"></a>리소스 정리

* 다음 명령을 실행하여 토픽 및 모든 해당 구독을 삭제합니다.

    ```sh
    curl -k -H "Content-Type: application/json" -X DELETE https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic3?api-version=2019-01-01-preview
    ```

* 클라우드(Azure Event Grid)에서 만든 토픽 및 구독도 삭제합니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 에지에서 이벤트를 게시하고 Azure 클라우드에서 Event Grid에 이벤트를 전달했습니다. 이제 클라우드에서 Event Grid로 이벤트를 전달하는 기본 단계를 알고 있습니다.

* IoT Edge의 Azure Event Grid를 사용하는 데 발생하는 문제를 해결하려면 [문제 해결 가이드](troubleshoot.md)를 참조하세요.
* 이 [자습서](forward-events-iothub.md)에 따라 IoTHub에 이벤트 전달
* 이 [자습서](pub-sub-events-webhook-cloud.md)에 따라 클라우드에서 웹후크에 이벤트 전달
* [에지에서 토픽 및 구독 모니터링](monitor-topics-subscriptions.md)
