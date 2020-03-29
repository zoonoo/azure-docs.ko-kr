---
title: 클라우드에서 이벤트 게시, 구독 - Azure 이벤트 그리드 IoT 에지 | 마이크로 소프트 문서
description: IoT Edge의 이벤트 그리드를 사용하여 웹후크를 사용하여 클라우드에서 이벤트를 게시, 구독
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/29/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: c82f1edfc3acd73c1d38425f963aaaf2976a1cc5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76844595"
---
# <a name="tutorial-publish-subscribe-to-events-in-cloud"></a>튜토리얼: 클라우드에서 이벤트 게시, 구독

이 문서에서는 IoT Edge의 이벤트 그리드를 사용하여 이벤트를 게시하고 구독하는 데 필요한 모든 단계를 안내합니다. 이 자습서에서는 이벤트 처리기로 Azure 함수를 사용합니다. 추가 대상 유형에 대한 [경우 이벤트 처리기를](event-handlers.md)참조하십시오.

[이벤트 그리드 개념을](concepts.md) 참조하여 진행하기 전에 이벤트 그리드 항목 및 구독이 무엇인지 이해합니다.

## <a name="prerequisites"></a>사전 요구 사항 
이 자습서를 완료하려면 다음과 같은 요건이 필요합니다.

* **Azure 구독** - 아직 계정이 없는 경우 [무료 계정을](https://azure.microsoft.com/free) 만듭니다. 
* **Azure IoT Hub 및 IoT Edge 장치** - 아직 없는 경우 [Linux](../../iot-edge/quickstart-linux.md) 또는 [Windows 장치의](../../iot-edge/quickstart.md) 빠른 시작 단계를 따릅니다.

[!INCLUDE [event-grid-deploy-iot-edge](../../../includes/event-grid-deploy-iot-edge.md)]

## <a name="create-an-azure-function-in-the-azure-portal"></a>Azure 포털에서 Azure 함수 만들기

[자습서에서](../../azure-functions/functions-create-first-azure-function.md) 설명하는 단계를 수행하여 Azure 함수를 만듭니다. 

코드 조각을 다음 코드로 바꿉니다.

```csharp
#r "Newtonsoft.Json"

using System.Net;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Extensions.Primitives;
using Newtonsoft.Json;

public static async Task<IActionResult> Run(HttpRequest req, ILogger log)
{
    log.LogInformation("C# HTTP trigger function processed a request.");

    string requestBody = await new StreamReader(req.Body).ReadToEndAsync();
    dynamic data = JsonConvert.DeserializeObject(requestBody);

    log.LogInformation($"C# HTTP trigger received {data}.");
    return data != null
        ? (ActionResult)new OkResult()
        : new BadRequestObjectResult("Please pass in the request body");
}
```

새 함수에서 오른쪽 상단에 있는 **함수 URL 받기를** 선택하고 기본값(Function**키)을**선택한 다음 **복사**를 선택합니다. 자습서의 나중에 함수 URL 값을 사용합니다.

> [!NOTE]
> EventGrid [이벤트 트리거를](../../azure-functions/functions-overview.md) 사용하는 이벤트에 반응하는 방법에 대한 자세한 샘플 및 자습서는 Azure Functions 설명서를 참조하십시오.

## <a name="create-a-topic"></a>토픽 만들기

이벤트 게시자는 이벤트 그리드 항목을 만들어야 합니다. 토픽은 게시자가 이벤트를 보낼 수 있는 끝점을 나타냅니다.

1. 다음 콘텐츠로 topic2.json을 만듭니다. 페이로드에 대한 자세한 내용은 [API 설명서를](api.md) 참조하십시오.

    ```json
         {
          "name": "sampleTopic2",
          "properties": {
            "inputschema": "eventGridSchema"
          }
        }
    ```
1. 다음 명령을 실행하여 토픽을 만듭니다. 200 확인의 HTTP 상태 코드는 반환해야 합니다.

    ```sh
    curl -k -H "Content-Type: application/json" -X PUT -g -d @topic2.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic2?api-version=2019-01-01-preview
    ```
1. 다음 명령을 실행하여 항목이 성공적으로 만들어졌는지 확인합니다. 200 확인의 HTTP 상태 코드는 반환해야 합니다.

    ```sh
    curl -k -H "Content-Type: application/json" -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic2?api-version=2019-01-01-preview
    ```

   샘플 출력:

   ```json
        [
          {
            "id": "/iotHubs/eg-iot-edge-hub/devices/eg-edge-device/modules/eventgridmodule/topics/sampleTopic2",
            "name": "sampleTopic2",
            "type": "Microsoft.EventGrid/topics",
            "properties": {
              "endpoint": "https://<edge-vm-ip>:4438/topics/sampleTopic2/events?api-version=2019-01-01-preview",
              "inputSchema": "EventGridSchema"
            }
          }
        ]
   ```

## <a name="create-an-event-subscription"></a>이벤트 구독 만들기

구독자는 토픽에 게시된 이벤트에 등록할 수 있습니다. 이벤트를 받으려면 구독자가 관심 있는 주제에 대해 이벤트 그리드 구독을 만들어야 합니다.

[!INCLUDE [event-grid-deploy-iot-edge](../../../includes/event-grid-edge-persist-event-subscriptions.md)]

1. 다음 콘텐츠로 구독2.json을 만듭니다. 페이로드에 대한 자세한 내용은 [API 설명서를](api.md) 참조하십시오.

    ```json
        {
          "properties": {
            "destination": {
              "endpointType": "WebHook",
              "properties": {
                "endpointUrl": "<your-az-func-cloud-url>"
              }
            }
          }
        }
    ```

   >[!NOTE]
   > **끝점 유형은** 구독자가 웹후크임을 지정합니다.  **endpointUrl은** 구독자가 이벤트를 수신 대기하는 URL을 지정합니다. 이 URL은 이전에 설정한 Azure Function 샘플에 해당합니다.
2. 다음 명령을 실행하여 구독을 만듭니다. 200 확인의 HTTP 상태 코드는 반환해야 합니다.

    ```sh
    curl -k -H "Content-Type: application/json" -X PUT -g -d @subscription2.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic2/eventSubscriptions/sampleSubscription2?api-version=2019-01-01-preview
    ```
3. 다음 명령을 실행하여 구독이 성공적으로 만들어졌는지 확인합니다. 200 확인의 HTTP 상태 코드는 반환해야 합니다.

    ```sh
    curl -k -H "Content-Type: application/json" -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic2/eventSubscriptions/sampleSubscription2?api-version=2019-01-01-preview
    ```

    샘플 출력:

   ```json
        {
          "id": "/iotHubs/eg-iot-edge-hub/devices/eg-edge-device/modules/eventgridmodule/topics/sampleTopic2/eventSubscriptions/sampleSubscription2",
          "type": "Microsoft.EventGrid/eventSubscriptions",
          "name": "sampleSubscription2",
          "properties": {
            "Topic": "sampleTopic2",
            "destination": {
              "endpointType": "WebHook",
              "properties": {
                "endpointUrl": "<your-az-func-cloud-url>"
              }
            }
          }
        }
    ```

## <a name="publish-an-event"></a>이벤트 게시

1. 다음 콘텐츠로 event2.json을 만듭니다. 페이로드에 대한 자세한 내용은 [API 설명서를](api.md) 참조하십시오.

    ```json
        [
          {
            "id": "eventId-func-1",
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
    curl -k -H "Content-Type: application/json" -X POST -g -d @event2.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic2/events?api-version=2019-01-01-preview
    ```

## <a name="verify-event-delivery"></a>이벤트 전달 확인

함수의 **모니터** 옵션에서 Azure 포털에서 제공되는 이벤트를 볼 수 있습니다.

## <a name="cleanup-resources"></a>리소스 정리

* 다음 명령을 실행하여 토픽 및 모든 구독을 삭제합니다.

    ```sh
    curl -k -H "Content-Type: application/json" -X DELETE https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic2?api-version=2019-01-01-preview
    ```

* Azure 포털에서 만든 Azure 함수를 삭제합니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 이벤트 그리드 항목, 구독 및 게시된 이벤트를 만들었습니다. 이제 기본 단계를 알고 있으므로 다음 문서를 참조하십시오.

* IoT Edge에서 Azure 이벤트 그리드를 사용하는 문제를 해결하려면 [문제 해결 가이드를](troubleshoot.md)참조하십시오.
* [필터를](advanced-filtering.md)사용하여 구독을 생성/업데이트합니다.
* [리눅스](persist-state-linux.md) 또는 [윈도우에서](persist-state-windows.md) 이벤트 그리드 모듈의 지속성 설정
* [설명서를](configure-client-auth.md) 따라 클라이언트 인증 구성
* 이 [자습서를](forward-events-event-grid-cloud.md) 따라 이벤트를 클라우드에서 Azure 이벤트 그리드로 전달
* [에지에서 주제 및 구독 모니터링](monitor-topics-subscriptions.md)
