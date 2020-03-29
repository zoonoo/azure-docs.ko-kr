---
title: 이벤트 그리드 클라우드로 에지 이벤트 전달 - Azure 이벤트 그리드 IoT 에지 | 마이크로 소프트 문서
description: 에지 이벤트를 이벤트 그리드 클라우드로 전달
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/29/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: 7184fb5c45ce41de2bd63b55fb67cbd9ba6361e3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76844720"
---
# <a name="tutorial-forward-events-to-event-grid-cloud"></a>자습서: 이벤트 그리드 클라우드로 이벤트 전달

이 문서에서는 Azure 클라우드의 이벤트 그리드로 에지 이벤트를 전달하는 데 필요한 모든 단계를 안내합니다. 다음과 같은 이유로 작업을 수행할 수 있습니다.

* 클라우드의 에지 이벤트에 반응합니다.
* 클라우드에서 이벤트 그리드로 이벤트를 전달하고 Azure 이벤트 허브 또는 Azure Storage 큐를 사용하여 클라우드에서 이벤트를 처리하기 전에 이벤트를 버퍼링합니다.

 이 자습서를 완료하려면 [에지](concepts.md) 및 [Azure](../concepts.md). 추가 대상 유형에 대한 [경우 이벤트 처리기를](event-handlers.md)참조하십시오. 

## <a name="prerequisites"></a>사전 요구 사항 
이 자습서를 완료하려면 다음과 같은 요건이 필요합니다.

* **Azure 구독** - 아직 계정이 없는 경우 [무료 계정을](https://azure.microsoft.com/free) 만듭니다. 
* **Azure IoT Hub 및 IoT Edge 장치** - 아직 없는 경우 [Linux](../../iot-edge/quickstart-linux.md) 또는 [Windows 장치의](../../iot-edge/quickstart.md) 빠른 시작 단계를 따릅니다.

[!INCLUDE [event-grid-deploy-iot-edge](../../../includes/event-grid-deploy-iot-edge.md)] 
## <a name="create-event-grid-topic-and-subscription-in-cloud"></a>클라우드에서 이벤트 그리드 토픽 및 구독 생성

[이 자습서를](../custom-event-quickstart-portal.md)따라 클라우드에서 이벤트 그리드 항목 및 구독을 만듭니다. 에 기록하고 `topicName` 자습서의 나중에 사용할 새로 만든 항목의 항목을 기록합니다. `topicURL` `sasKey`

예를 들어 미국 서부에서 `testegcloudtopic` 명명된 주제를 만든 경우 값은 다음과 같습니다.

* **토픽Url**:`https://testegcloudtopic.westus2-1.eventgrid.azure.net/api/events`
* **주제 이름**:`testegcloudtopic`
* **SasKey**: 주제의 **AccessKey에서** 사용할 수 있습니다. **키1을**사용합니다.

## <a name="create-event-grid-topic-at-the-edge"></a>가장자리에 이벤트 그리드 토픽 만들기

1. 다음 콘텐츠로 topic3.json을 만듭니다. 페이로드에 대한 자세한 내용은 [API 설명서를](api.md) 참조하십시오.

    ```json
        {
          "name": "sampleTopic3",
          "properties": {
            "inputschema": "eventGridSchema"
          }
        }
    ```
1. 다음 명령을 실행하여 토픽을 만듭니다. 200 확인의 HTTP 상태 코드는 반환해야 합니다.

    ```sh
    curl -k -H "Content-Type: application/json" -X PUT -g -d @topic3.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic3?api-version=2019-01-01-preview
    ```
1. 다음 명령을 실행하여 항목이 성공적으로 만들어졌는지 확인합니다. 200 확인의 HTTP 상태 코드는 반환해야 합니다.

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
  
## <a name="create-event-grid-subscription-at-the-edge"></a>에지에서 이벤트 그리드 구독 만들기

[!INCLUDE [event-grid-deploy-iot-edge](../../../includes/event-grid-edge-persist-event-subscriptions.md)]

1. 다음 콘텐츠로 구독3.json을 만듭니다. 페이로드에 대한 자세한 내용은 [API 설명서를](api.md) 참조하십시오.

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
   > **endpointUrl은** 클라우드의 이벤트 그리드 토픽 URL을 지정합니다. **sasKey** 는 이벤트 그리드 클라우드 항목의 키를 나타냅니다. **topicName의** 값은 모든 나가는 이벤트를 이벤트 그리드에 스탬프하는 데 사용됩니다. 이벤트 그리드 도메인 항목에 게시할 때 유용할 수 있습니다. 이벤트 그리드 도메인 항목에 대한 자세한 내용은 [이벤트 도메인을](../event-domains.md) 참조하십시오.

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

2. 다음 명령을 실행하여 구독을 만듭니다. 200 확인의 HTTP 상태 코드는 반환해야 합니다.

     ```sh
     curl -k -H "Content-Type: application/json" -X PUT -g -d @subscription3.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic3/eventSubscriptions/sampleSubscription3?api-version=2019-01-01-preview
     ```

3. 다음 명령을 실행하여 구독이 성공적으로 만들어졌는지 확인합니다. 200 확인의 HTTP 상태 코드는 반환해야 합니다.

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

## <a name="publish-an-event-at-the-edge"></a>가장자리에 이벤트 게시

1. 다음 콘텐츠로 event3.json을 만듭니다. 페이로드에 대한 자세한 내용은 [API 설명서를](api.md) 참조하십시오.

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

클라우드 항목에서 전달되는 이벤트 보기에 대한 자세한 내용은 [자습서를](../custom-event-quickstart-portal.md)참조하십시오.

## <a name="cleanup-resources"></a>리소스 정리

* 다음 명령을 실행하여 토픽 및 모든 구독을 삭제합니다.

    ```sh
    curl -k -H "Content-Type: application/json" -X DELETE https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic3?api-version=2019-01-01-preview
    ```

* 클라우드에서 만든 토픽 및 구독(Azure Event)도 삭제합니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 에지에 이벤트를 게시 하 고 Azure 클라우드에서 이벤트 그리드로 전달 했습니다. 이제 클라우드에서 이벤트 그리드로 전달하는 기본 단계를 알 수 있습니다.

* IoT Edge에서 Azure 이벤트 그리드를 사용하는 문제를 해결하려면 [문제 해결 가이드를](troubleshoot.md)참조하십시오.
* 이 [자습서를](forward-events-iothub.md) 따라 IoTHub로 이벤트 전달
* 이 [자습서를](pub-sub-events-webhook-cloud.md) 따라 클라우드에서 Webhook에 이벤트를 전달
* [에지에서 주제 및 구독 모니터링](monitor-topics-subscriptions.md)
