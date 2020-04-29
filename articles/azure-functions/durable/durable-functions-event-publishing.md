---
title: Azure Event Grid에 게시하는 Durable Functions(미리 보기)
description: Durable Functions에 대한 자동 Azure Event Grid 게시를 구성하는 방법을 알아봅니다.
ms.topic: conceptual
ms.date: 03/14/2019
ms.openlocfilehash: 671f7bd5221a936ea9dad0f0cece895bdbe9512f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81535488"
---
# <a name="durable-functions-publishing-to-azure-event-grid-preview"></a>Azure Event Grid에 게시하는 Durable Functions(미리 보기)

이 문서에서는 오케스트레이션 수명 주기 이벤트(예: 만들어짐, 완료됨 및 실패)를 사용자 지정 [Azure Event Grid 토픽](https://docs.microsoft.com/azure/event-grid/overview)에 게시하도록 Durable Functions를 설정하는 방법을 설명합니다.

이 기능이 유용한 몇 가지 시나리오는 다음과 같습니다.

* **파랑/녹색 배포와 같은 Devops 시나리오**: [병렬 배포 전략](durable-functions-versioning.md#side-by-side-deployments)을 구현 하기 전에 실행 중인 작업이 있는지 확인할 수 있습니다.

* **고급 모니터링 및 진단 지원**: Azure SQL Database 또는 Azure Cosmos DB와 같이 쿼리에 최적화 된 외부 저장소에서 오케스트레이션 상태 정보를 추적할 수 있습니다.

* **장기 실행 백그라운드 작업**: 장기 실행 백그라운드 작업에 Durable Functions를 사용하는 경우 이 기능을 사용하면 현재 상태를 알 수 있습니다.

## <a name="prerequisites"></a>전제 조건

* Durable Functions 프로젝트에 [microsoft.azure.webjobs.extensions.durabletask](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DurableTask) 를 설치 합니다.
* [Azure Storage 에뮬레이터](../../storage/common/storage-use-emulator.md) 를 설치 하거나 (Windows에만 해당) 기존 Azure Storage 계정을 사용 합니다.
* [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest)를 설치하거나 [Azure Cloud Shell](../../cloud-shell/overview.md)을 사용합니다.

## <a name="create-a-custom-event-grid-topic"></a>사용자 지정 Event Grid 토픽 만들기

Durable Functions에서 이벤트를 보내기 위한 Event Grid 토픽을 만듭니다. 다음 지침에서는 Azure CLI를 사용하여 토픽을 만드는 방법을 보여 줍니다. [PowerShell을 사용](../../event-grid/custom-event-quickstart-powershell.md) 하거나 [Azure Portal를](../../event-grid/custom-event-quickstart-portal.md)사용 하 여이 작업을 수행할 수도 있습니다.

### <a name="create-a-resource-group"></a>리소스 그룹 만들기

`az group create` 명령을 사용하여 리소스 그룹을 만듭니다. 현재 Azure Event Grid는 모든 지역을 지원 하지 않습니다. 지원 되는 지역에 대 한 자세한 내용은 [Azure Event Grid 개요](../../event-grid/overview.md)를 참조 하세요.

```azurecli
az group create --name eventResourceGroup --location westus2
```

### <a name="create-a-custom-topic"></a>사용자 지정 토픽 만들기

Event Grid 토픽은 이벤트를 게시하는 사용자 정의 엔드포인트를 제공합니다. `<topic_name>`을 토픽의 고유한 이름으로 바꿉니다. 토픽 이름은 DNS 항목이 되므로 고유해야 합니다.

```azurecli
az eventgrid topic create --name <topic_name> -l westus2 -g eventResourceGroup
```

## <a name="get-the-endpoint-and-key"></a>엔드포인트 및 키 가져오기

토픽의 엔드포인트를 가져옵니다. `<topic_name>`을 선택한 이름으로 바꿉니다.

```azurecli
az eventgrid topic show --name <topic_name> -g eventResourceGroup --query "endpoint" --output tsv
```

토픽 키를 가져옵니다. `<topic_name>`을 선택한 이름으로 바꿉니다.

```azurecli
az eventgrid topic key list --name <topic_name> -g eventResourceGroup --query "key1" --output tsv
```

이제 토픽에 이벤트를 보낼 수 있습니다.

## <a name="configure-event-grid-publishing"></a>Event Grid 게시 구성

Durable Functions 프로젝트에서 `host.json` 파일을 찾습니다.

### <a name="durable-functions-1x"></a>1.x Durable Functions

`durableTask` 속성에 `eventGridTopicEndpoint` 및 `eventGridKeySettingName`을 추가합니다.

```json
{
  "durableTask": {
    "eventGridTopicEndpoint": "https://<topic_name>.westus2-1.eventgrid.azure.net/api/events",
    "eventGridKeySettingName": "EventGridKey"
  }
}
```

### <a name="durable-functions-2x"></a>Durable Functions 2.x

파일의 `notifications` `durableTask` 속성에 섹션을 추가 하 고을 선택한 `<topic_name>` 이름으로 바꿉니다. 또는 `extensions` 속성이 `durableTask` 없는 경우에는 다음 예제와 같이 만듭니다.

```json
{
  "version": "2.0",
  "extensions": {
    "durableTask": {
      "notifications": {
        "eventGrid": {
          "topicEndpoint": "https://<topic_name>.westus2-1.eventgrid.azure.net/api/events",
          "keySettingName": "EventGridKey"
        }
      }
    }
  }
}
```

가능한 Azure Event Grid 구성 속성은 [호스트의 json 설명서](../functions-host-json.md#durabletask)에서 찾을 수 있습니다. `host.json` 파일을 구성한 후 함수 앱은 Event Grid 항목에 수명 주기 이벤트를 보냅니다. 로컬 및 Azure에서 함수 앱을 실행 하는 경우에 작동 합니다.

함수 앱 및 `local.settings.json`에서 토픽 키에 대한 앱 설정을 지정합니다. 다음 JSON은 로컬 디버깅에 대한 `local.settings.json` 샘플입니다. `<topic_key>`를 토픽 키로 바꿉니다.  

```json
{
    "IsEncrypted": false,
    "Values": {
        "AzureWebJobsStorage": "UseDevelopmentStorage=true",
        "AzureWebJobsDashboard": "UseDevelopmentStorage=true",
        "EventGridKey": "<topic_key>"
    }
}
```

[저장소 에뮬레이터](../../storage/common/storage-use-emulator.md) 를 사용 하는 경우 (Windows에만 해당) 작동 하는지 확인 합니다. 실행하기 전에 `AzureStorageEmulator.exe clear all` 명령을 실행하는 것이 좋습니다.

기존 Azure Storage 계정을 사용 하는 경우에서 `UseDevelopmentStorage=true` `local.settings.json` 을 해당 연결 문자열로 바꿉니다.

## <a name="create-functions-that-listen-for-events"></a>이벤트를 수신 대기하는 함수 만들기

Azure Portal 사용 하 여 Durable Functions 앱에서 게시 한 이벤트를 수신 대기 하는 다른 함수 앱을 만듭니다. Event Grid 항목과 동일한 지역에서 찾는 것이 가장 좋습니다.

### <a name="create-an-event-grid-trigger-function"></a>Event Grid 트리거 함수 만들기

수명 주기 이벤트를 받는 함수를 만듭니다. **사용자 지정 함수**를 선택합니다.

![사용자 지정 함수 만들기 선택](./media/durable-functions-event-publishing/functions-portal.png)

Event Grid 트리거를 선택 하 고 언어를 선택 합니다.

![Event Grid 트리거 선택](./media/durable-functions-event-publishing/eventgrid-trigger.png)

함수 이름을 입력한 다음, `Create`를 선택합니다.

![Event Grid 트리거 만들기](./media/durable-functions-event-publishing/eventgrid-trigger-creation.png)

다음 코드가 있는 함수가 만들어집니다.

# <a name="c-script"></a>[C# 스크립트](#tab/csharp-script)

```csharp
#r "Newtonsoft.Json"
using Newtonsoft.Json;
using Newtonsoft.Json.Linq;
using Microsoft.Extensions.Logging;

public static void Run(JObject eventGridEvent, ILogger log)
{
    log.LogInformation(eventGridEvent.ToString(Formatting.Indented));
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
module.exports = async function(context, eventGridEvent) {
    context.log(typeof eventGridEvent);
    context.log(eventGridEvent);
}
```

---

`Add Event Grid Subscription`를 선택합니다. 이 작업은 사용자가 만든 Event Grid 토픽에 대한 Event Grid 구독을 추가합니다. 자세한 내용은 [Azure Event Grid의 개념](https://docs.microsoft.com/azure/event-grid/concepts)을 참조하세요.

![Event Grid 트리거 링크 선택](./media/durable-functions-event-publishing/eventgrid-trigger-link.png)

**토픽 종류**에 대해 `Event Grid Topics`를 선택합니다. Event Grid 토픽에 대해 만든 리소스 그룹을 선택합니다. 그런 다음, Event Grid 토픽의 인스턴스를 선택합니다. `Create`를 누릅니다.

![Event Grid 구독을 만듭니다.](./media/durable-functions-event-publishing/eventsubscription.png)

이제 수명 주기 이벤트를 받을 준비가 되었습니다.

## <a name="run-durable-functions-app-to-send-the-events"></a>Durable Functions 앱을 실행 하 여 이벤트 보내기

이전에 구성한 Durable Functions 프로젝트에서 로컬 컴퓨터에 대 한 디버깅을 시작 하 고 오케스트레이션을 시작 합니다. 앱이 Event Grid에 Durable Functions 수명 주기 이벤트를 게시 합니다. Event Grid에서 Azure Portal의 로그를 확인 하 여 만든 수신기 함수를 트리거 하는지 확인 합니다.

```
2019-04-20T09:28:21.041 [Info] Function started (Id=3301c3ef-625f-40ce-ad4c-9ba2916b162d)
2019-04-20T09:28:21.104 [Info] {
    "id": "054fe385-c017-4ce3-b38a-052ac970c39d",
    "subject": "durable/orchestrator/Running",
    "data": {
        "hubName": "DurableFunctionsHub",
        "functionName": "Sample",
        "instanceId": "055d045b1c8a415b94f7671d8df693a6",
        "reason": "",
        "runtimeStatus": "Running"
    },
    "eventType": "orchestratorEvent",
    "eventTime": "2019-04-20T09:28:19.6492068Z",
    "dataVersion": "1.0",
    "metadataVersion": "1",
    "topic": "/subscriptions/<your_subscription_id>/resourceGroups/eventResourceGroup/providers/Microsoft.EventGrid/topics/durableTopic"
}

2019-04-20T09:28:21.104 [Info] Function completed (Success, Id=3301c3ef-625f-40ce-ad4c-9ba2916b162d, Duration=65ms)
2019-04-20T09:28:37.098 [Info] Function started (Id=36fadea5-198b-4345-bb8e-2837febb89a2)
2019-04-20T09:28:37.098 [Info] {
    "id": "8cf17246-fa9c-4dad-b32a-5a868104f17b",
    "subject": "durable/orchestrator/Completed",
    "data": {
        "hubName": "DurableFunctionsHub",
        "functionName": "Sample",
        "instanceId": "055d045b1c8a415b94f7671d8df693a6",
        "reason": "",
        "runtimeStatus": "Completed"
    },
    "eventType": "orchestratorEvent",
    "eventTime": "2019-04-20T09:28:36.5061317Z",
    "dataVersion": "1.0",
    "metadataVersion": "1",
    "topic": "/subscriptions/<your_subscription_id>/resourceGroups/eventResourceGroup/providers/Microsoft.EventGrid/topics/durableTopic"
}
2019-04-20T09:28:37.098 [Info] Function completed (Success, Id=36fadea5-198b-4345-bb8e-2837febb89a2, Duration=0ms)
```

## <a name="event-schema"></a>이벤트 스키마

다음 목록에서는 수명 주기 이벤트 스키마를 설명합니다.

* **`id`**: Event Grid 이벤트에 대 한 고유 식별자입니다.
* **`subject`**: 이벤트 주체에 대 한 경로입니다. `durable/orchestrator/{orchestrationRuntimeStatus}`. `{orchestrationRuntimeStatus}`는 `Running`, `Completed`, `Failed` 및 `Terminated`입니다.  
* **`data`**: 특정 매개 변수를 Durable Functions 합니다.
  * **`hubName`**: [Taskhub](durable-functions-task-hubs.md) 이름.
  * **`functionName`**: Orchestrator 함수 이름입니다.
  * **`instanceId`**: InstanceId를 Durable Functions 합니다.
  * **`reason`**: 추적 이벤트와 연결 된 추가 데이터입니다. 자세한 내용은 [Durable Functions의 진단(Azure Functions)](durable-functions-diagnostics.md)을 참조하세요.
  * **`runtimeStatus`**: 오케스트레이션 런타임 상태입니다. 실행 중, 완료됨, 실패, 취소됨입니다.
* **`eventType`**: "orchestratorEvent"
* **`eventTime`**: 이벤트 시간 (UTC)입니다.
* **`dataVersion`**: 수명 주기 이벤트 스키마의 버전입니다.
* **`metadataVersion`**: 메타 데이터의 버전입니다.
* **`topic`**: Event grid 토픽 리소스입니다.

## <a name="how-to-test-locally"></a>로컬로 테스트하는 방법

로컬로 테스트 하려면 [Azure 함수 Event Grid 로컬 디버깅 트리거](../functions-debug-event-grid-trigger-local.md)를 참조 하세요.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Durable Functions의 인스턴스 관리 알아보기](durable-functions-instance-management.md)

> [!div class="nextstepaction"]
> [Durable Functions의 버전 관리 알아보기](durable-functions-versioning.md)
