---
title: Azure Digital Twins와 Time Series Insights 간의 모델 동기화 Microsoft Docs
description: ADT의 자산 모델을 Azure TSI의 자산 모델로 변환 하는 데 사용 되는 모범 사례 및 도구
ms.service: time-series-insights
services: time-series-insights
author: msrsrinivasan
ms.author: radhsrin
manager: diviso
ms.workload: big-data
ms.topic: conceptual
ms.date: 09/22/2020
ms.custom: dpalled
ms.openlocfilehash: c3948a5bdfce583384992fb87bf40e9e7251974d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91346391"
---
# <a name="model-synchronization-between-azure-digital-twins-and-time-series-insights-gen2"></a>Azure Digital Twins와 Time Series Insights Gen2 간의 모델 동기화

이 문서에서는 Azure Digital Twins (ADT)의 자산 모델을 TWINS (Azure Time Series Insights)의 자산 모델로 변환 하는 데 사용 되는 모범 사례 및 도구에 대해 설명 합니다.  이 문서는 Azure Time Series Insights와 Azure 디지털 쌍의 통합을 설명 하는 두 부분으로 구성 된 자습서 시리즈의 두 번째 부분입니다. Azure Digital Twins를 Time Series Insights와 통합 하면 Digital Twins의 원격 분석 및 계산 된 속성에 대 한 기록을 보관 하 고 추적할 수 있습니다. 이 자습서 시리즈는 Azure Digital Twins와 Time Series Insights를 통합 하는 개발자를 대상으로 합니다. 1 부에서는  [Azure Digital Twins의 실제 시계열 데이터를 Time Series Insights 하는 데이터 파이프라인을 설정 하는 방법](https://docs.microsoft.com/azure/digital-twins/how-to-integrate-time-series-insights) 에 대해 설명 합니다. 자습서 시리즈의 두 번째 부분에서는 Azure Digital twins와 Time Series Insights 간의 자산 모델 동기화에 대해 설명 합니다. 이 자습서에서는 TS ID (Time Series ID)에 대 한 명명 규칙을 선택 및 설정 하 고 TSM (시계열 모델)에서 수동으로 계층을 설정 하는 최선의 방법에 대해 설명 합니다.

## <a name="choosing-a-time-series-id"></a>시계열 ID 선택

시계열 ID는 Time Series Insights 자산을 식별 하는 데 사용 되는 고유 식별자입니다. 시계열 데이터 (시간 값 쌍 인 필드의 원격 분석)는 TSID 아래에 나열 된 변수를 사용 하 여 표현 됩니다. Azure Digital Twins에서 쌍 속성과 원격 분석는 각각 쌍으로 생성 된 쌍 및 측정의 상태를 나타내는 데 사용 됩니다. TSM의 현재 디자인을 따라 Tsm는 고유 해야 합니다. Azure Digital 쌍에서 쌍의 쌍 id를 사용 하거나 속성 또는 원격 분석 이름과 함께 사용 하면 항상 쌍에서 고유한 TS ID를 만듭니다. 일반적인 경우는 **_`<Twin ID>`_** TSID이 되며 속성 및 원격 분석 이름은 TSM의 변수가 됩니다. 그러나와 같은 복합 키 형식을 사용 하 여 Time Series Insights 자산 계층 구조를 평면화 하는 것이 선호 되는 사용 사례가 있습니다 **_`<Twin ID>+ <Delimiter of Choice> + <Name of the Property or Telemetry>`_** . 이후 사례를 설명 하는 예를 살펴보겠습니다. 쌍으로 구성 된 건물에 있는 방을 쌍으로 간주 하 고 쌍 ID Room22를 가집니다. 온도 설정 속성은 TSM에서 **_Room22_TempMea_** 으로 변환 될 **_TSID Room22_TempSetting_** 및 온도 측정으로 변환 됩니다.

[![시계열 ID 선택](media/tutorials-model-sync/choosing-tsid.png)](media/tutorials-model-sync/choosing-tsid.png#lightbox)

## <a name="contextualizing-time-series"></a>Contextualizing 시계열

Time Series Insights는 자산 계층 구조를 통해 데이터를 Contextualization 하 고 Time Series Insights 탐색기에서 트리 뷰를 통해 데이터를 쉽게 탐색 하는 데 사용 됩니다. 시계열 유형 및 계층은 Time Series Insights에서 TSM (시계열 모델)을 사용 하 여 정의 됩니다. TSM의 형식은 변수를 정의 하는 데 도움이 되는 반면, 계층 수준 및 인스턴스 필드 값은 Time Series Insights 탐색기에서 트리 뷰를 생성 하는 데 사용 됩니다. TSM에 대 한 자세한 내용은 [온라인 Time Series Insights 설명서](https://docs.microsoft.com/azure/time-series-insights/concepts-model-overview)를 참조 하세요.

Azure Digital Twins에서 자산 간 연결은 쌍 관계를 사용 하 여 표현 됩니다. 쌍 관계는 단순히 연결 된 자산의 그래프입니다. 그러나 시계열 정보에서 자산의 관계는 본질적으로 계층적입니다. 즉, 자산은 부모-자식 종류 od 관계를 공유 하며 트리 구조를 사용 하 여 표시 됩니다. Azure Digital Twins에서 Time Series Insights 계층으로 관계 정보를 변환 하려면 Azure Digital Twins에서 관련 계층 관계를 선택 해야 합니다. Azure Digital Twins는 DTDL (디지털 쌍 정의 언어) 이라는 개방형 표준 모델링 언어를 사용 합니다. DTDL 모델에는 JSON-LD 이라는 변형을 사용 하 여 설명 되어 있습니다. 사양에 대 한 자세한 내용은 [Dtdl 설명서](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md) 를 참조 하세요.

[![자산 간 연결](media/tutorials-model-sync/asset-connection.png)](media/tutorials-model-sync/asset-connection.png#lightbox)

## <a name="translating-graph-representation-in-azure-digital-twins-to-tree-structure-in-time-series-insights"></a>Time Series Insights에서 Azure Digital Twins의 그래프 표시를 트리 구조로 변환

자습서의 다음 섹션에서는 Azure Digital Twins의 graph 구조를 Time Series Insights의 트리 구조로 수동으로 변환 하는 몇 가지 핵심 시나리오를 캡처합니다.

샘플 시스템:이 자습서에서는 다음 예제를 사용 하 여 아래에 설명 된 개념을 설명 합니다. 단일 층과 두 개의 방을 포함 하는 간단 하 고 가상의 빌딩 관리 시스템입니다. 여기에는 각 방에 하나씩 세 개의 온도 조절 장치 있고 바닥에 공통적인 다른 요소가 있습니다. 또한 방 간의 파이프 연결을 통해 Room21에서 Room22까지 급수 흐름을 측정 하는 급수 흐름 측정기도 있습니다. 쌍 간의 공간 관계를 살펴보면 두 유형의 관계를 모두 갖습니다.

1. 가장 일반적인 계층 관계입니다. 예를 들어 Building40-> Floor01-> FloorTS *-> 온도
1. 일반적인 순환 관계가 아닙니다. 예를 들어 Building40에서 시작 하 여 FlowMtr은 서로 다른 두 경로를 통해 추적할 수 있습니다.

   1. Building40-> Floor01-> Room21-> FlowMtr *-> Flow
   1. Building40-> Floor01-> Room22-> FlowMtr *-> Flow  
      여기서 "Flow"는 Room21와 Room22 간의 물 흐름을 측정 하는 실제 원격 분석입니다.

> [!Note]
>
> `*` FloorTS는 FloorThermoStat를 나타내며 FlowMtr은 흐름 측정기를 나타내며 일반적으로 TSID으로 선택 됩니다. 온도 및 흐름은 Time Series Insights 변수 라고 하는 원시 원격 분석입니다.

여러 분기에서 한 자산을 표현할 수 없는 Time Series Insights의 현재 제한 사항을 고려 하 여 다음 섹션에서는 Time Series Insights의 계층적 및 순환 관계 모델링에 대해 설명 합니다.

## <a name="case-1-hierarchical-parent-child-relationship"></a>사례 1: 계층 구조 (부모-자식) 관계

이는 쌍 간의 가장 일반적인 관계 유형입니다. 다음 그림에서는 순수 부모-자식 관계를 모델링 하는 방법에 대해 설명 합니다. 인스턴스 필드 및 TSID는 아래와 같이 쌍 id에서 파생 됩니다. Time Series Insights 탐색기를 사용 하 여 인스턴스 필드를 수동으로 업데이트할 수 있지만 "Api를 사용 하 여 인스턴스 필드 업데이트" 라는 섹션에서는 azure 함수를 사용 하 여 Azure Digital Twins의 모델 변경 사항을 수신 하 고 Time Series Insights에서 인스턴스 필드를 업데이트 하는 방법을 설명 합니다.

[![쌍 Id 매핑](media/tutorials-model-sync/mapping-twin-ids.png)](media/tutorials-model-sync/mapping-twin-ids.png#lightbox)

[![쌍 Id 매핑 2](media/tutorials-model-sync/mapping-twin-ids2.png)](media/tutorials-model-sync/mapping-twin-ids2.png#lightbox)

## <a name="case-2-circular-relationship"></a>사례 2: 순환 관계

### <a name="circular-relationship-in-azure-digital-twins-to-single-hierarchy-relationship-in-time-series-insights"></a>Time Series Insights에서 Azure Digital Twins의 순환 관계가 단일 계층 관계에 있습니다.

TSID는 고유 해야 하 고 하나의 계층 에서만 표현할 수 있는 경우이 사례는 ' _Room21 '_ 쌍에서 _' Flow '_ 라는 원격 분석이 있는 _' flowmtr '_ 를 나타냅니다. 향후에는 Time Series Insights에서 TSM의 시계열에 대 한 다중 표현을 지원할 수 있는 경우 원격 분석 _' Flow '_ 는 _' room 21 '_ 및 _' room 22 '_ 아래에 표시 됩니다.

다음 스크린샷에서는 Azure Digital Twins의 쌍 Id를 TWINS의 인스턴스 필드와 Time Series Insights의 결과 계층 구조에 수동으로 매핑하는 방법을 보여 줍니다.

[![Azure Digital Twins에서 쌍 Id 매핑](media/tutorials-model-sync/mapping-twin-ids-adt.png)](media/tutorials-model-sync/mapping-twin-ids-adt.png#lightbox)

### <a name="circular-relationship-in-azure-digital-twins-to-multiple-hierarchies-in-time-series-insights-using-duplicates"></a>중복을 사용 하 여 Azure Digital Twins Time Series Insights의 여러 계층으로 Wins의 순환 관계

자습서의 1 부에서는 클라이언트 프로그램 (Azure 함수)을 통해 IoT Hub 또는 다른 이벤트 원본에서 Azure Digital Twins로 원격 분석 데이터를 전송 하는 방법을 설명 합니다. 이 방법은 동일한 클라이언트 프로그램을 사용 하 여 부모 쌍의 관련 속성을 업데이트 하는 것을 제안 합니다. 지정 된 예제에서는 IoT Hub에서 FlowMtr 원격 분석을 읽고 FlowMtr 쌍의 속성 "Flow"를 업데이트 하는 동안 프로그램은 원본의 모든 가능한 부모 쌍에서 해당 속성을 업데이트할 수도 있습니다. 이 예제에서는 Room21의 ' 아웃 플로 ' 관계를 사용 하 여 식별 되는 "outflowmea" 속성 및 Room22의 "inflowmea" 속성을 사용 합니다.  아래 스크린샷은 Time Series Insights explorer의 최종 사용자 환경을 보여 줍니다. 이 접근 방식을 사용 하 여 데이터 중복이 있음을 유의 해야 합니다.

[![Time Series Insights 탐색기](media/tutorials-model-sync/tsi-explorer.png)](media/tutorials-model-sync/tsi-explorer.png#lightbox)

아래 코드 조각에서는 클라이언트 응용 프로그램에서 Azure Digital Twins Api를 사용 하 여 쌍 관계를 탐색 하는 방법을 보여 줍니다.

> [!Note]
>
> 이 코드 조각 예제에서는 독자가 자습서의 1 [부에서](https://docs.microsoft.com/azure/digital-twins/tutorial-end-to-end#set-up-the-sample-function-app) 잘 알고 있고 "ProcessHubToDTEvents" 함수 내에서이 코드 변경이 수행 되었다고 가정 합니다.

```csharp
if (propertyPath.Equals("/Flow"))
{
//Update the flow value property of the flow meter
await AdtUtilities.UpdateTwinProperty(client, twinId, "replace",
propertyPath, "double", propertyValue, log);

//also update the sending end flow
string parentIdOutflow = await AdtUtilities.FindParent(client, twinId,
"outflow", log);
if (parentIdOutflow != null)
await AdtUtilities.UpdateTwinProperty(client, parentIdOutflow, "replace", "outflow", "double", propertyValue, log);
else
log.LogInformation("Unable to find Parent with outflow
relationship for " + twinId );
//and receiving end flow value
string parentIdinflow = await AdtUtilities.FindParent(client, twinId,
"inflow", log);
if (parentIdinflow != null)

await AdtUtilities.UpdateTwinProperty(client, parentIdinflow,
"replace", "inflow", "double", propertyValue, log);
else
log.LogInformation("Unable to find Parent with inflow
relationship for " + twinId);
}
```

## <a name="updating-instance-fields-using-apis"></a>Api를 사용 하 여 인스턴스 필드 업데이트

자습서의이 섹션에서는 Time Series Insights 모델 api를 사용 하 여 프로그래밍 방식으로, 쌍의 생성, 삭제 또는 쌍 간 관계 변경, 인스턴스 필드 및 계층 업데이트와 같은 Azure Digital 쌍의 모델 변경에 대 한 수신을 설명 합니다. 이 Time Series Insights 모델 업데이트 방법은 일반적으로 Azure 함수를 통해 구현 됩니다. Azure Digital Twins에서 쌍 추가 또는 삭제와 같은 이벤트 알림은 라우트된 다운스트림 서비스 (예: Azure 함수에 공급할 수 있는 Event Hubs)가 될 수 있습니다. 이벤트 라우팅 및 필터링에 대 한 자세한 내용은 [여기](https://docs.microsoft.com/azure/digital-twins/how-to-manage-routes-portal)에 설명 되어 있습니다.  이 섹션의 나머지 부분에서는 azure 함수에서 Time Series Insights 모델 Api를 사용 하 여 Azure Digital Twins에서 쌍 추가 (한 가지 유형의 모델 변경)에 대 한 응답으로 Time Series Insights 모델을 업데이트 하는 방법을 설명 합니다.

### <a name="receiving-and-identifying-twin-addition-event-notification"></a>쌍 추가 이벤트 알림 받기 및 식별

```csharp
[FunctionName("RouteEventsToTsi")]
public async Task Run([EventGridTrigger]EventGridEvent eventGridEvent)
{
    try
    {
        if (eventGridEvent != null && eventGridEvent.Data != null)
        {
            logger.LogInformation($"EventType: {eventGridEvent.EventType}");
            logger.LogInformation($"EventGridEvent: {JsonConvert.SerializeObject(eventGridEvent)}");

            //Shape event and Send event data to event hub and tsi
            await SendEventToEventHubAsync(eventGridEvent).ConfigureAwait(false);

            //If a new twin was created, update the newly created instance in TSI with info retrieved from ADT
            if (eventGridEvent.EventType == Constants.TwinCreateEventType)
            {
                //retrieve building, floor and room of value twin
                var twinInfo = await RetrieveTwinInfoAsync(eventGridEvent).ConfigureAwait(false);
                //Update Tsi instance with type(sensor type), hierarchy(space hierarchy) and instance fields(twin info retrieved above)
                var instance = await CreateInstanceToSendAsync(twinInfo).ConfigureAwait(false);
                var instanceToUpdate = new List<TimeSeriesInstance>() { instance };
                var response = await tsiClient.TimeSeriesInstances.ExecuteBatchAsync(new InstancesBatchRequest(update: instanceToUpdate)).ConfigureAwait(false);
            }
        }
    }
    catch (Exception ex)
    {
        logger.LogError($"Exception: {ex.Message}");
    }
}
```

### <a name="creating-time-series-insights-client-and-adding-instance-details"></a>Time Series Insights 클라이언트 만들기 및 인스턴스 정보 추가

```csharp
private async Task<TimeSeriesInstance> CreateInstanceToSendAsync(Dictionary<string, string> twinInfo)
{
    try
    {
        tsiClient = await GetTSIClientAsync().ConfigureAwait(false);

        var timeSeriesId = new object[] { twinInfo[Constants.DtId] };
        var instances = await tsiClient.TimeSeriesInstances.ExecuteBatchAsync(
            new InstancesBatchRequest(
                get: new InstancesRequestBatchGetOrDelete(
                    new IList<object>[] { timeSeriesId }))).ConfigureAwait(false);
        var instance = instances.Get.First().Instance;

        if (instance != null)
        {
            instance = await AddHierarchyToInstanceAsync(instance).ConfigureAwait(false);
            instance = await AddTypeToInstanceAsync(instance, twinInfo[Constants.TwinType]).ConfigureAwait(false);

            instance.InstanceFields = new Dictionary<string, object>
                            {
                                { "Building", twinInfo[Constants.BuildingName] },
                                { "Floor", twinInfo[Constants.FloorName] },
                                { "Room", twinInfo[Constants.ParentName] }
                            };

            //If value twin is a sensor value twin, add sensor type instance field to diff from spatial value twin
            if (twinInfo[Constants.ParentType] == Constants.Sensor)
            {
                instance.InstanceFields.Add(Constants.SensorType, twinInfo[Constants.TwinType]);
            }
            return instance;
        }
        else
        {
            logger.LogError($"instance with id {twinInfo[Constants.DtId]} not found");
            return new TimeSeriesInstance();
        }
    }
    catch (Exception e)
    {
        logger.LogError(e.Message);
        throw;
    }
}
```

### <a name="applying-hierarchy-info-to-instance"></a>인스턴스에 계층 정보를 적용 하는 중

```csharp
private async Task<TimeSeriesInstance> AddHierarchyToInstanceAsync(TimeSeriesInstance instance)
{
    if (instance.HierarchyIds == null)
    {
        TimeSeriesHierarchy spacesHierarchy = null;
        try
        {
            var hierarchy = await RunGetHierarchiesAsync(Constants.SpaceHierarchy).ConfigureAwait(false);
            spacesHierarchy = hierarchy.First(h => h.Name.Equals(Constants.SpaceHierarchy));
            instance.HierarchyIds = new List<Guid?>();
            instance.HierarchyIds.Add(spacesHierarchy.Id);
        }
        catch (Exception ex)
        {
            logger.LogWarning($"Hierarchy 'space hierarchy' not found, {ex}");
            throw;
        }
    }
    return instance;
}
```

## <a name="next-steps"></a>다음 단계

자습서 시리즈 중 세 번째는 Time Series Insights Api를 사용 하 여 Azure Digital Twins에서 기록 데이터를 쿼리 하는 방법을 보여 주는 것입니다. 진행 중인 작업이 며 준비 되 면 섹션이 업데이트 됩니다. 한편 판독기는 [Time Series Insights 데이터 쿼리 API 설명서](https://docs.microsoft.com/azure/time-series-insights/concepts-query-overview)를 참조 하는 것이 좋습니다.
