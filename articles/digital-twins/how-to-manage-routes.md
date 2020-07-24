---
title: 엔드포인트 및 경로 관리
titleSuffix: Azure Digital Twins
description: Azure Digital Twins 데이터에 대 한 끝점 및 이벤트 경로를 설정 하 고 관리 하는 방법을 참조 하세요.
author: alexkarcher-msft
ms.author: alkarche
ms.date: 6/23/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: bc22cf5a21709ccacafe068a60541cc9990d1131
ms.sourcegitcommit: 0e8a4671aa3f5a9a54231fea48bcfb432a1e528c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/24/2020
ms.locfileid: "87132265"
---
# <a name="manage-endpoints-and-routes-in-azure-digital-twins"></a>Azure Digital Twins에서 끝점 및 경로 관리

Azure Digital Twins에서 [이벤트 알림을](how-to-interpret-event-data.md) 다운스트림 서비스로 라우팅하거나 계산 리소스에 연결할 수 있습니다. 이 작업을 수행 하려면 먼저 이벤트를 받을 수 있는 **끝점** 을 설정 하 고 그 다음에 Azure Digital twins에서 생성 된 이벤트를 지정 하는 [**이벤트 경로**](concepts-route-events.md) 를 끝점으로 전달 합니다.

지원 되는 끝점 유형은 다음과 같습니다.
* [이벤트 허브](../event-hubs/event-hubs-about.md)
* [Event Grid](../event-grid/overview.md)
* [Service Bus](../service-bus-messaging/service-bus-messaging-overview.md)

다른 끝점에 대 한 자세한 내용은 [*Azure 메시징 서비스 중에서 선택*](https://docs.microsoft.com/azure/event-grid/compare-messaging-services)을 참조 하세요.

끝점과 경로는 [**Eventroutes api**](how-to-use-apis-sdks.md), [.net (c #) SDK](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/digitaltwins/Azure.DigitalTwins.Core)또는 [Azure Digital twins CLI](how-to-use-cli.md)로 관리 됩니다. [Azure Portal](https://portal.azure.com)를 통해 관리할 수도 있습니다.

## <a name="create-an-endpoint-for-azure-digital-twins"></a>Azure Digital Twins에 대 한 끝점 만들기

끝점을 Azure Digital Twins에 연결 하려면 끝점에 사용 중인 이벤트 허브, event grid 토픽 또는 Service Bus이 이미 존재 해야 합니다. 

다음 예제에서는 Azure CLI 사용 하 여 event grid 토픽을 만드는 방법을 보여 줍니다.

```azurecli
az eventgrid topic create -g <your-resource-group-name> --name <your-topic-name> -l <region>
```

> [!TIP]
> Azure CLI 명령으로 전달할 수 있는 Azure 지역 이름 목록을 출력하려면 다음 명령을 실행합니다.
> ```azurecli
> az account list-locations -o table
> ```

토픽을 만든 후에는 다음 명령을 사용 하 여 Azure Digital Twins에 연결할 수 있습니다.

```azurecli
az dt endpoint create eventgrid --endpoint-name <Event-Grid-endpoint-name> --eventgrid-resource-group <Event-Grid-resource-group-name> --eventgrid-topic <your-Event-Grid-topic-name> -n <your-Azure-Digital-Twins-instance-name>
```

이렇게 하면 인수를 사용 하 여 지정 된 이름으로 Azure Digital Twins 내에서 event grid 항목을 사용할 수 있습니다 `--endpoint-name` . 일반적으로이 이름을 **이벤트 경로의**대상으로 사용 합니다 .이는 다음 섹션에서 Azure Digital twins 서비스 API를 사용 하 여 만듭니다.

이벤트 허브 및 Service Bus 끝점에 대해 동일한 명령이 있습니다.

* Service Bus 토픽 끝점 추가 (미리 만든 Service Bus 리소스가 필요 함)
```azurecli 
az dt endpoint create servicebus --endpoint-name <Service-Bus-endpoint-name> --servicebus-resource-group <Service-Bus-resource-group-name> --servicebus-namespace <Service-Bus-namespace> --servicebus-topic <Service-Bus-topic-name> --servicebus-policy <Service-Bus-topic-policy> -n <your-Azure-Digital-Twins-instance-name>
```

* 이벤트 허브 끝점 추가 (미리 만든 이벤트 허브 리소스 필요)
```azurecli
az dt endpoint create eventhub --endpoint-name <Event-Hub-endpoint-name> --eventhub-resource-group <Event-Hub-resource-group> --eventhub-namespace <Event-Hub-namespace> --eventhub <Event-Hub-name> --eventhub-policy <Event-Hub-policy> -n <your-Azure-Digital-Twins-instance-name>
```

## <a name="manage-event-routes-with-apis"></a>Api를 사용 하 여 이벤트 경로 관리

실제로 Azure Digital Twins에서 끝점으로 데이터를 전송 하려면 이벤트 경로를 정의 해야 합니다. 개발자는 Azure Digital Twins **Eventroutes 경로 api** 를 사용 하 여 시스템 및 다운스트림 서비스 전체에서 이벤트 흐름을 연결할 수 있습니다. 이벤트 경로에 대 한 자세한 내용은 [*Azure Digital Twins 이벤트 라우팅*](concepts-route-events.md)을 참조 하세요.

끝점 설정이 완료 되 면 이벤트 경로를 계속 만들 수 있습니다.

>[!NOTE]
>최근에 끝점을 배포한 경우 새 이벤트 경로에 대 한 사용을 시도 **하기 전에** 배포를 완료 했는지 확인 합니다. 끝점이 준비 되지 않았기 때문에 경로 배포가 실패 하면 몇 분 정도 기다렸다가 다시 시도 하세요.
>
> 이 흐름을 스크립팅 하는 경우 경로 설정으로 이동 하기 전에 끝점 서비스의 배포를 완료 하는 데 2-3 분의 대기 시간으로 구축 하 여이를 고려해 볼 수 있습니다.

이 문서의 샘플에서는 c # SDK를 사용 합니다.

이벤트 경로는 데이터 평면 Api를 사용 하 여 정의 됩니다. 경로 정의에는 다음 요소가 포함 될 수 있습니다.
* 사용 하려는 경로 ID
* 사용 하려는 끝점의 이름입니다.
* 끝점으로 전송 되는 이벤트를 정의 하는 필터입니다. 

경로 ID가 없는 경우 메시지는 Azure 디지털 쌍 외부에서 라우팅됩니다. 경로 ID가 있고 필터가 이면 `true` 모든 메시지가 끝점으로 라우팅됩니다. 경로 ID가 있고 다른 필터가 추가 된 경우 필터를 기반으로 메시지가 필터링 됩니다.

하나의 경로에서 여러 알림과 이벤트 유형을 선택할 수 있어야 합니다. 

`CreateEventRoute`는 이벤트 경로를 추가 하는 데 사용 되는 SDK 호출입니다. 사용법의 예는 다음과 같습니다.

```csharp
EventRoute er = new EventRoute("endpointName");
er.Filter("true"); //Filter allows all messages
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
        Console.WriteLine($"Route {r.Id} to endpoint {r.EndpointId} with filter {r.Filter} ");
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

### <a name="filter-events"></a>이벤트 필터링

필터링이 없으면 끝점은 Azure Digital Twins에서 다양 한 이벤트를 수신 합니다.
* Azure digital 쌍 서비스 API를 사용 하 여 [디지털](concepts-twins-graph.md) 쌍에서 발생 하는 원격 분석
* 쌍 속성 변경 알림, Azure Digital Twins 인스턴스의 모든 쌍에 대 한 속성 변경 시 발생
* 쌍 또는 관계가 만들어지거나 삭제 될 때 발생 하는 수명 주기 이벤트
* Azure Digital Twins 인스턴스에서 구성 된 [모델이](concepts-models.md) 추가 되거나 삭제 될 때 발생 하는 모델 변경 이벤트

끝점에 필터를 추가 하 여 전송 되는 이벤트를 제한할 수 있습니다.

필터를 추가 하려면 다음 본문을 사용 하 여 PUT 요청을 *https://{호스트}/EventRoutes/myNewRoute? api-version = 2020-05 -31-preview* 에 사용할 수 있습니다.

```json  
{
    "endpointName": "<endpoint-name>",
    "filter": "<filter-text>"
}
``` 

다음은 지원 되는 경로 필터입니다.

| 필터 이름 | 설명 | 필터 스키마 | 지원되는 값 | 
| --- | --- | --- | --- |
| 형식 | 디지털 쌍 인스턴스를 통해 흐르는 [이벤트의 유형입니다](./concepts-route-events.md#types-of-event-messages) . | `"filter" : "type = '<eventType>'"` | `Microsoft.DigitalTwins.Twin.Create` <br> `Microsoft.DigitalTwins.Twin.Delete` <br> `Microsoft.DigitalTwins.Twin.Update`<br>`Microsoft.DigitalTwins.Relationship.Create`<br>`Microsoft.DigitalTwins.Relationship.Update`<br> `Microsoft.DigitalTwins.Relationship.Delete` <br> `microsoft.iot.telemetry`  |
| 원본 | Azure Digital Twins 인스턴스의 이름 | `"filter" : "source = '<hostname>'"`|  **알림**:`<yourDigitalTwinInstance>.<yourRegion>.azuredigitaltwins.net` <br> **원격 분석의 경우**:`<yourDigitalTwinInstance>.<yourRegion>.azuredigitaltwins.net/digitaltwins/<twinId>`|
| 주체 | 위의 이벤트 원본에 대 한 컨텍스트의 이벤트 설명입니다. | `"filter": " subject = '<subject>'"` | **알림의 경우**: 제목은입니다.`<twinid>` <br> 또는 여러 부분이 나 Id로 고유 하 게 식별 되는 주제에 대 한 URI 형식:<br>`<twinid>/relationships/<relationshipid>`<br> **원격 분석의**경우: 주체는와 같은 구성 요소 경로입니다 (쌍으로 원격 분석을 내보내는 경우) `comp1.comp2` . 구성 요소에서 원격 분석을 내보내지 않는 경우에는 해당 제목 필드가 비어 있습니다. |
| 데이터 스키마 | DTDL 모델 ID | `"filter": "dataschema = 'dtmi:example:com:floor4;2'"` | **원격 분석의 경우**: 데이터 스키마는 쌍의 모델 ID 또는 원격 분석을 내보내는 구성 요소입니다. <br>**알림**: 데이터 스키마는 지원 되지 않습니다.|
| 내용 유형 | 데이터 값의 콘텐츠 형식 | `"filter": "datacontenttype = '<contentType>'"` | `application/json` |
| 사양 버전 | 사용 중인 이벤트 스키마의 버전 | `"filter": "specversion = '<version>'"` | `1.0`이어야 합니다. 이는 CloudEvents 스키마 버전 1.0을 나타냅니다. |
| True/False | 필터링 없이 경로를 만들거나 경로를 사용 하지 않도록 설정할 수 있습니다. | `"filter" : "<true/false>"` | `true`= 필터링 없이 경로를 사용할 수 있습니다. <br> `false`= 경로를 사용할 수 없음 |
<!--
| ID | *Not implemented for public preview* | "filter": "id = '…'" | |
| Schema | *Not implemented for public preview*  | "filter": "dataschema = '…'" | |
-->

다음 작업을 사용 하 여 필터를 결합할 수도 있습니다.

| 필터 이름 | 필터 스키마 | 예제 | 
| --- | --- | --- |
| 및/또는 | `"filter": "<filter1> AND <filter2>"` | `"filter": "type != 'microsoft.iot.telemetry' AND datacontenttype = 'application/json'"` |
| 및/또는 | `"filter": "<filter1> OR <filter2>"` | `"filter": "type != 'microsoft.iot.telemetry' OR datacontenttype = 'application/json'"` |
| 중첩 된 작업 | `"filter": "(<Comparison1>) AND (<Comparison2>)"` | `"filter": "(type != 'microsoft.iot.telemetry' OR datacontenttype = 'application/json') OR (specversion != '1.0')"` |

> [!NOTE]
> 미리 보기 중에는 문자열 동일성만 지원 됩니다 (=,! =).

필터를 구현 하거나 업데이트 하는 경우 변경 내용이 데이터 파이프라인에 반영 되는 데 몇 분 정도 걸릴 수 있습니다.

## <a name="manage-endpoints-and-routes-with-cli"></a>CLI를 사용 하 여 끝점 및 경로 관리

Azure Digital Twins CLI를 사용 하 여 끝점과 경로를 관리할 수도 있습니다. 명령은 [*방법: Azure Digital Twins CLI 사용*](how-to-use-cli.md)에서 찾을 수 있습니다.

## <a name="monitor-event-routes"></a>이벤트 경로 모니터링

개수, 대기 시간 및 실패율과 같은 라우팅 메트릭은 [Azure Portal](https://portal.azure.com/)볼 수 있습니다. 

포털 홈페이지에서 Azure Digital Twins 인스턴스를 검색 하 여 세부 정보를 가져옵니다. Azure Digital Twins 인스턴스 메뉴에서 **메트릭** 옵션을 선택 하 여 *메트릭* 페이지를 표시 합니다.

:::image type="content" source="media/how-to-manage-routes/metrics.png" alt-text="Azure Portal에서 Azure Digital Twins 인스턴스의 메트릭 페이지":::

여기에서 인스턴스에 대 한 메트릭을 보고 사용자 지정 보기를 만들 수 있습니다.

## <a name="next-steps"></a>다음 단계

받을 수 있는 다양 한 이벤트 메시지 유형에 대해 읽어 보세요.
* [*방법: 이벤트 데이터 해석*](how-to-interpret-event-data.md)
