---
title: 끝점 및 경로 관리 (Api 및 CLI)
titleSuffix: Azure Digital Twins
description: Azure Digital Twins 데이터에 대 한 끝점 및 이벤트 경로를 설정 하 고 관리 하는 방법을 참조 하세요.
author: alexkarcher-msft
ms.author: alkarche
ms.date: 10/12/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: ce922e3ce39bc3df9f4c242558644922e5713300
ms.sourcegitcommit: d6a739ff99b2ba9f7705993cf23d4c668235719f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/24/2020
ms.locfileid: "92494811"
---
# <a name="manage-endpoints-and-routes-in-azure-digital-twins-apis-and-cli"></a>Azure Digital Twins (Api 및 CLI)에서 끝점 및 경로 관리

[!INCLUDE [digital-twins-route-selector.md](../../includes/digital-twins-route-selector.md)]

Azure Digital Twins에서 [이벤트 알림을](how-to-interpret-event-data.md) 다운스트림 서비스 또는 연결 된 계산 리소스로 라우팅할 수 있습니다. 이 작업을 수행 하려면 먼저 이벤트를 받을 수 있는 **끝점** 을 설정 합니다. 그런 다음 Azure Digital Twins에 의해 생성 되는 이벤트를 지정 하는  [**이벤트 경로**](concepts-route-events.md) 를 끝점으로 전달할 수 있습니다.

끝점 및 경로는 [이벤트 경로 api](/rest/api/digital-twins/dataplane/eventroutes), [.net (c #) SDK](/dotnet/api/overview/azure/digitaltwins/client?view=azure-dotnet-preview&preserve-view=true)또는 [Azure Digital twins CLI](how-to-use-cli.md)를 사용 하 여 관리할 수 있습니다. 이 문서에서는 이러한 메커니즘을 통해 끝점과 경로를 만드는 과정을 안내 합니다.

[Azure Portal](https://portal.azure.com)를 통해 관리할 수도 있습니다. 포털을 대신 사용 하는이 문서의 버전에 대해서는 [*방법: 끝점 및 경로 관리 (포털)*](how-to-manage-routes-portal.md)를 참조 하세요.

## <a name="prerequisites"></a>사전 요구 사항

* **Azure 계정이** 필요 합니다 ( [여기](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)에서 무료로 설정할 수 있음).
* Azure 구독에는 **Azure Digital Twins 인스턴스가** 필요 합니다. 인스턴스가 아직 없는 경우 [*방법: 인스턴스 및 인증 설정*](how-to-set-up-instance-portal.md)의 단계를 사용 하 여 인스턴스를 만들 수 있습니다. 이 문서의 뒷부분에서 사용할 수 있도록 다음 값을 설정 하는 것이 유용 합니다.
    - 인스턴스 이름
    - 리소스 그룹
    
## <a name="create-an-endpoint-for-azure-digital-twins"></a>Azure Digital Twins에 대 한 끝점 만들기

다음은 인스턴스에 대해 만들 수 있는 지원 되는 끝점 유형입니다.
* [Event Grid](../event-grid/overview.md)
* [Event Hubs](../event-hubs/event-hubs-about.md)
* [Service Bus](../service-bus-messaging/service-bus-messaging-overview.md)

다른 끝점 형식에 대 한 자세한 내용은 [*Azure 메시징 서비스 중에서 선택*](../event-grid/compare-messaging-services.md)을 참조 하세요.

끝점을 Azure Digital Twins에 연결 하려면 끝점에 사용 하는 event grid 토픽, event hub 또는 Service Bus이 이미 존재 해야 합니다. 

### <a name="create-an-event-grid-endpoint"></a>Event Grid 끝점 만들기

다음 예제에서는 Azure CLI를 사용 하 여 event grid 형식 끝점을 만드는 방법을 보여 줍니다. [Azure Cloud Shell](https://shell.azure.com)를 사용 하거나 [CLI를 로컬로 설치할](/cli/azure/install-azure-cli?preserve-view=true&view=azure-cli-latest)수 있습니다.

먼저 event grid 토픽을 만듭니다. 다음 명령을 사용 하거나 *사용자 지정 이벤트* Event Grid 빠른 시작의 [ *사용자 지정 항목 만들기* 섹션](../event-grid/custom-event-quickstart-portal.md#create-a-custom-topic) 을 방문 하 여 단계를 더 자세히 볼 수 있습니다.

```azurecli-interactive
az eventgrid topic create -g <your-resource-group-name> --name <your-topic-name> -l <region>
```

> [!TIP]
> Azure CLI 명령으로 전달할 수 있는 Azure 지역 이름 목록을 출력하려면 다음 명령을 실행합니다.
> ```azurecli-interactive
> az account list-locations -o table
> ```

토픽을 만든 후에는 다음 [Azure Digital TWINS CLI 명령을](how-to-use-cli.md)사용 하 여 Azure Digital twins에 연결할 수 있습니다.

```azurecli-interactive
az dt endpoint create eventgrid --endpoint-name <Event-Grid-endpoint-name> --eventgrid-resource-group <Event-Grid-resource-group-name> --eventgrid-topic <your-Event-Grid-topic-name> -n <your-Azure-Digital-Twins-instance-name>
```

이제 event grid 토픽은 인수를 사용 하 여 지정 된 이름으로 Azure Digital Twins 내부의 끝점으로 사용할 수 있습니다 `--endpoint-name` . 일반적으로 해당 이름을 **이벤트 경로의**대상으로 사용 합니다. [이는이 문서의 뒷부분에서](#create-an-event-route) Azure DIGITAL twins 서비스 API를 사용 하 여 만듭니다.

### <a name="create-an-event-hubs-or-service-bus-endpoint"></a>Event Hubs 또는 Service Bus 엔드포인트 만들기

Event Hubs 또는 Service Bus 끝점을 만드는 프로세스는 위에 표시 된 Event Grid 프로세스와 비슷합니다.

먼저 끝점으로 사용할 리소스를 만듭니다. 필요한 항목은 다음과 같습니다.
* Service Bus: _Service Bus 네임 스페이스_, _Service Bus 항목_, _권한 부여 규칙_
* Event Hubs: _Event Hubs 네임 스페이스_, _이벤트 허브_, _권한 부여 규칙_

그런 다음, 다음 명령을 사용 하 여 Azure Digital Twins에서 끝점을 만듭니다. 

* Service Bus 토픽 끝점 추가 (미리 만든 Service Bus 리소스가 필요 함)
```azurecli-interactive 
az dt endpoint create servicebus --endpoint-name <Service-Bus-endpoint-name> --servicebus-resource-group <Service-Bus-resource-group-name> --servicebus-namespace <Service-Bus-namespace> --servicebus-topic <Service-Bus-topic-name> --servicebus-policy <Service-Bus-topic-policy> -n <your-Azure-Digital-Twins-instance-name>
```

* Event Hubs 끝점 추가 (미리 만든 Event Hubs 리소스 필요)
```azurecli-interactive
az dt endpoint create eventhub --endpoint-name <Event-Hub-endpoint-name> --eventhub-resource-group <Event-Hub-resource-group> --eventhub-namespace <Event-Hub-namespace> --eventhub <Event-Hub-name> --eventhub-policy <Event-Hub-policy> -n <your-Azure-Digital-Twins-instance-name>
```

### <a name="create-an-endpoint-with-dead-lettering"></a>배달 못 한 문자를 사용 하 여 끝점 만들기

끝점이 특정 기간 내에 이벤트를 전달할 수 없거나 특정 횟수 만큼 이벤트를 배달 하려고 시도한 후에는 배달 되지 않은 이벤트를 저장소 계정으로 보낼 수 있습니다. 이 프로세스를 **배달 못 한 문자**라고 합니다.

배달 못 한 편지 처리를 사용 하도록 설정 된 끝점을 만들려면 [ARM api](/rest/api/digital-twins/controlplane/endpoints/digitaltwinsendpoint_createorupdate) 를 사용 하 여 끝점을 만들어야 합니다. 

배달 못한 편지 위치를 설정하기 전에 컨테이너를 포함하는 스토리지 계정이 있어야 합니다. 끝점을 만들 때이 컨테이너의 URL을 제공 합니다. 배달 못 한 편지는 SAS 토큰을 포함 하는 컨테이너 URL로 제공 됩니다. 해당 토큰 `write` 은 저장소 계정 내에서 대상 컨테이너에 대 한 권한만 필요 합니다. 완전히 구성 된 URL의 형식은 다음과 같습니다. `https://<storageAccountname>.blob.core.windows.net/<containerName>?<SASToken>`

SAS 토큰에 대 한 자세한 내용은 [sas (공유 액세스 서명)를 사용 하 여 Azure Storage 리소스에 대해 제한 된 액세스 권한 부여](/azure/storage/common/storage-sas-overview) 를 참조 하세요.

배달 못 한 편지에 대해 자세히 알아보려면 [*개념: 이벤트 경로*](concepts-route-events.md#dead-letter-events)를 참조 하세요.

#### <a name="configuring-the-endpoint"></a>끝점 구성

끝점을 만들 때 `deadLetterSecret` `properties` 저장소 계정에 대 한 컨테이너 URL 및 SAS 토큰을 포함 하는 요청 본문의 개체에를 추가 합니다.

```json
{
  "properties": {
    "endpointType": "EventGrid",
    "TopicEndpoint": "https://contosoGrid.westus2-1.eventgrid.azure.net/api/events",
    "accessKey1": "xxxxxxxxxxx",
    "accessKey2": "xxxxxxxxxxx",
    "deadLetterSecret":"https://<storageAccountname>.blob.core.windows.net/<containerName>?<SASToken>"
  }
}
```

자세한 내용은 Azure Digital Twins REST API 설명서: [끝점-DigitalTwinsEndpoint CreateOrUpdate](/rest/api/digital-twins/controlplane/endpoints/digitaltwinsendpoint_createorupdate)를 참조 하세요.

### <a name="message-storage-schema"></a>메시지 저장소 스키마

배달 못 한 메시지는 저장소 계정에서 다음 형식으로 저장 됩니다.

`{container}/{endpointName}/{year}/{month}/{day}/{hour}/{eventId}.json`

배달 못 한 편지 메시지는 원래 끝점에 전달 하기 위한 원래 이벤트의 스키마와 일치 합니다.

다음은 쌍 [만들기 알림에](how-to-interpret-event-data.md#digital-twin-life-cycle-notifications)대 한 배달 못 한 편지 메시지의 예입니다.

```json
{
  "specversion": "1.0",
  "id": "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "type": "Microsoft.DigitalTwins.Twin.Create",
  "source": "<yourInstance>.api.<yourregion>.da.azuredigitaltwins-test.net",
  "data": {
    "$dtId": "<yourInstance>xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "$etag": "W/\"xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxxxxx\"",
    "TwinData": "some sample",
    "$metadata": {
      "$model": "dtmi:test:deadlettermodel;1",
      "room": {
        "lastUpdateTime": "2020-10-14T01:11:49.3576659Z"
      }
    }
  },
  "subject": "<yourInstance>xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "time": "2020-10-14T01:11:49.3667224Z",
  "datacontenttype": "application/json",
  "traceparent": "00-889a9094ba22b9419dd9d8b3bfe1a301-f6564945cb20e94a-01"
}
```

## <a name="create-an-event-route"></a>이벤트 경로 만들기

실제로 Azure Digital Twins에서 끝점으로 데이터를 전송 하려면 **이벤트 경로**를 정의 해야 합니다. 개발자는 Azure Digital Twins **Eventroutes 경로 api** 를 사용 하 여 시스템 및 다운스트림 서비스 전체에서 이벤트 흐름을 연결할 수 있습니다. 이벤트 경로에 대 한 자세한 내용은 [*Azure Digital Twins 이벤트 라우팅*](concepts-route-events.md)을 참조 하세요.

이 단원의 샘플에서는 [.net (c #) SDK](/dotnet/api/overview/azure/digitaltwins/client?view=azure-dotnet-preview&preserve-view=true)를 사용 합니다.

**필수 조건**: 경로 만들기로 이동 하기 전에이 문서의 앞부분에서 설명한 대로 끝점을 만들어야 합니다. 끝점 설정이 완료 되 면 이벤트 경로를 계속 만들 수 있습니다.

>[!NOTE]
>최근에 끝점을 배포한 경우 새 이벤트 경로에 대 한 사용을 시도 **하기 전에** 배포를 완료 했는지 확인 합니다. 끝점이 준비 되지 않았기 때문에 경로 배포가 실패 하면 몇 분 정도 기다렸다가 다시 시도 하세요.
>
> 이 흐름을 스크립팅 하는 경우 경로 설정으로 이동 하기 전에 끝점 서비스의 배포를 완료 하는 데 2-3 분의 대기 시간으로 구축 하 여이를 고려해 볼 수 있습니다.

### <a name="creation-code-with-apis-and-the-c-sdk"></a>Api 및 c # SDK를 사용 하 여 코드 작성

이벤트 경로는 [데이터 평면 api](how-to-use-apis-sdks.md#overview-data-plane-apis)를 사용 하 여 정의 됩니다. 

경로 정의에는 다음 요소가 포함 될 수 있습니다.
* 사용 하려는 경로 이름
* 사용 하려는 끝점의 이름입니다.
* 끝점으로 전송 되는 이벤트를 정의 하는 필터입니다. 

경로 이름이 없으면 메시지가 Azure 디지털 쌍 외부에서 라우팅되지 않습니다. 경로 이름이 있고 필터가 이면 `true` 모든 메시지가 끝점으로 라우팅됩니다. 경로 이름이 있고 다른 필터가 추가 된 경우 필터를 기반으로 메시지가 필터링 됩니다.

하나의 경로에서 여러 알림과 이벤트 유형을 선택할 수 있어야 합니다. 

`CreateEventRoute` 는 이벤트 경로를 추가 하는 데 사용 되는 SDK 호출입니다. 사용법의 예는 다음과 같습니다.

```csharp
EventRoute er = new EventRoute("endpointName");
er.Filter = "true"; //Filter allows all messages
await client.CreateEventRoute("routeName", er);
```

> [!TIP]
> 모든 SDK 함수는 동기 및 비동기 버전으로 제공 됩니다.

### <a name="event-route-sample-code"></a>이벤트 경로 샘플 코드

다음 코드 샘플에서는 이벤트 경로를 만들고, 나열 하 고, 삭제 하는 방법을 보여 줍니다.
```csharp
try
{
    Console.WriteLine("Create a route: testRoute1");
    EventRoute er = new EventRoute("< your - endpoint - name >");
    // Make a filter that passes everything
    er.Filter = "true";
    client.CreateEventRoute("< your - route - name >", er);
    Console.WriteLine("Create route succeeded. Now listing routes:");
    Pageable <EventRoute> result = client.GetEventRoutes();
    foreach (EventRoute r in result)
    {
        Console.WriteLine($"Route {r.Id} to endpoint {r.EndpointName} with filter {r.Filter} ");
    }
    Console.WriteLine("Deleting routes:");
    foreach (EventRoute r in result)
    {
        Console.WriteLine($"Deleting route {r.Id}:");
        client.DeleteEventRoute(r.Id);
    }
}
catch (RequestFailedException e)
{
    Console.WriteLine($"*** Error in event route processing ({e.ErrorCode}):\n${e.Message}");
}
```

## <a name="filter-events"></a>이벤트 필터링

필터링이 없으면 끝점은 Azure Digital Twins에서 다양 한 이벤트를 수신 합니다.
* Azure digital 쌍 서비스 API를 사용 하 여 [디지털](concepts-twins-graph.md) 쌍에서 발생 하는 원격 분석
* 쌍 속성 변경 알림, Azure Digital Twins 인스턴스의 모든 쌍에 대 한 속성 변경 시 발생
* 쌍 또는 관계가 만들어지거나 삭제 될 때 발생 하는 수명 주기 이벤트

이벤트 경로에 끝점에 대 한 **필터** 를 추가 하 여 전송 되는 이벤트를 제한할 수 있습니다.

필터를 추가 하려면 다음 본문을 사용 하 여 PUT 요청을 *https://{호스트}/EventRoutes/myNewRoute? api-version = 2020-10-31* 에 사용할 수 있습니다.

```json  
{
    "endpointName": "<endpoint-name>",
    "filter": "<filter-text>"
}
``` 

다음은 지원 되는 경로 필터입니다. *텍스트 스키마 필터* 열의 세부 정보를 사용 하 여 `<filter-text>` 위의 요청 본문에서 자리 표시자를 바꿉니다.

[!INCLUDE [digital-twins-route-filters](../../includes/digital-twins-route-filters.md)]

## <a name="manage-endpoints-and-routes-with-cli"></a>CLI를 사용 하 여 끝점 및 경로 관리

Azure Digital Twins CLI를 사용 하 여 끝점과 경로를 관리할 수도 있습니다. CLI 사용 방법과 사용할 수 있는 명령에 대 한 자세한 내용은 [*방법: Azure Digital Twins CLI 사용*](how-to-use-cli.md)을 참조 하세요.

[!INCLUDE [digital-twins-route-metrics](../../includes/digital-twins-route-metrics.md)]

## <a name="next-steps"></a>다음 단계

받을 수 있는 다양 한 이벤트 메시지 유형에 대해 읽어 보세요.
* [*방법: 이벤트 데이터 해석*](how-to-interpret-event-data.md)