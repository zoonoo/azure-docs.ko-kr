---
title: 메트릭, 경고 및 진단 로그
description: 풀, 작업 등과 같은 Azure Batch 계정 리소스에 대해 진단 로그 이벤트를 기록 및 분석합니다.
ms.topic: how-to
ms.date: 05/29/2020
ms.custom: seodec18
ms.openlocfilehash: abf9ef53d3f2e3ffeffabfe9b7c77dc5c5debec3
ms.sourcegitcommit: 5cace04239f5efef4c1eed78144191a8b7d7fee8
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/08/2020
ms.locfileid: "86145097"
---
# <a name="batch-metrics-alerts-and-logs-for-diagnostic-evaluation-and-monitoring"></a>진단 평가 및 모니터링을 위한 일괄 처리 메트릭, 경고 및 로그
 
이 문서에서는 [Azure Monitor](../azure-monitor/overview.md)의 기능을 사용하여 배치 계정을 모니터링하는 방법을 설명합니다. Azure Monitor는 배치 계정의 리소스에 대해 [메트릭](../azure-monitor/platform/data-platform-metrics.md) 및 [진단 로그](../azure-monitor/platform/platform-logs-overview.md)를 수집합니다. 이 데이터를 다양한 방법으로 수집하고 사용하여 배치 계정을 모니터링하고 문제를 진단합니다. 또한 메트릭이 지정된 값에 도달할 때 알림을 받을 수 있도록 [메트릭 경고](../azure-monitor/platform/alerts-overview.md)를 구성할 수 있습니다.

## <a name="batch-metrics"></a>일괄 처리 메트릭

메트릭은 Azure 리소스에서 내보내고 Azure Monitor 서비스에서 사용 하는 Azure 원격 분석 데이터 (성능 카운터 라고도 함)입니다. Batch 계정의 메트릭에 대 한 예로는 풀 만들기 이벤트, 우선 순위가 낮은 노드 수 및 태스크 완료 이벤트가 있습니다.

[지원되는 일괄 처리 메트릭 목록](../azure-monitor/platform/metrics-supported.md#microsoftbatchbatchaccounts)을 참조하세요.

이러한 메트릭은 다음과 같습니다.

- 추가 구성 없이 각 배치 계정에서 기본값으로 사용 가능
- 1분마다 생성
- 자동으로 유지되지 않으나 30일 롤링 기록이 있습니다. 활동 메트릭을 진단 로깅의 일환으로 유지할 수 있습니다.

## <a name="view-batch-metrics"></a>일괄 처리 메트릭 보기

Azure Portal에서 계정의 **개요** 페이지에는 기본적으로 키 노드, 코어 및 작업 메트릭이 표시 됩니다.

Azure Portal에서 모든 Batch 계정 메트릭을 보려면 다음을 수행 합니다.

1. Azure Portal에서 **모든 서비스**  >  **배치 계정**을 선택 하 고 batch 계정의 이름을 선택 합니다.
2. **모니터링**에서 **메트릭**을 선택합니다.
3. **메트릭 추가** 를 선택한 다음 드롭다운 목록에서 메트릭을 선택 합니다.
4. 메트릭에 대 한 **집계** 옵션을 선택 합니다. 개수 기반 메트릭 (예: "전용 코어 수" 또는 "낮은 우선 순위 노드 수")의 경우 **평균** 집계를 사용 합니다. 이벤트 기반 메트릭 (예: "풀 크기 조정 완료 이벤트")의 경우 **개수**"집계를 사용 합니다.

   > [!WARNING]
   > 차트의 기간 동안 수신 된 모든 데이터 요소의 값을 더하는 "Sum" 집계를 사용 하지 마세요.

5. 추가 메트릭을 추가 하려면 3 단계와 4 단계를 반복 합니다.

Azure Monitor Api를 사용 하 여 프로그래밍 방식으로 메트릭을 검색할 수도 있습니다. 예제는 [.net을 사용 하 여 Azure Monitor 메트릭 검색](https://azure.microsoft.com/resources/samples/monitor-dotnet-metrics-api/)을 참조 하세요.

### <a name="batch-metric-reliability"></a>일괄 처리 메트릭 안정성

메트릭은 추세를 식별 하는 데 도움이 될 수 있으며 데이터 분석에 사용할 수 있습니다. 메트릭 배달이 보장 되지 않으며, 잘못 된 순서 전달, 데이터 손실 및/또는 중복이 발생할 수 있다는 점에 유의 해야 합니다. 이로 인해 단일 이벤트를 사용 하 여 경고 또는 트리거 함수를 사용 하지 않는 것이 좋습니다. 경고 임계값을 설정 하는 방법에 대 한 자세한 내용은 다음 섹션을 참조 하세요.

지난 3 분 동안 내보낸 메트릭은 아직 집계할 수 있으므로이 기간 동안 메트릭 값이 보고 될 수 있습니다.

## <a name="batch-metric-alerts"></a>일괄 처리 메트릭 경고

지정 된 메트릭의 값이 할당 된 임계값을 초과할 때 트리거되는 거의 실시간 *메트릭 경고* 를 구성할 수 있습니다. 경고가 “활성화”되었을 때(임계값ㅇ르 초과했고 경고 조건에 부합함)와 “해결”되었을 때(임계값을 다시 넘었고 조건에 더 이상 부합하지 않음) 경고가 알림을 생성합니다.

메트릭이 순차적 배달, 데이터 손실 및/또는 중복의 영향을 받기 때문에 단일 데이터 요소에 대해 트리거되는 경고는 권장 되지 않습니다. 경고를 만들 때 이러한 불일치를 고려 하는 데 임계값을 사용할 수 있습니다.

예를 들어 풀의 구성을 조정할 수 있도록 우선 순위가 낮은 코어 수가 일정 수준으로 낮아질 경우 메트릭 경고를 구성할 수 있습니다. 최상의 결과를 위해 10 분 이상의 기간을 설정 합니다 .이 경우 평균 낮은 우선 순위 코어 수가 전체 기간에 대 한 임계값 아래로 떨어지면 경고가 트리거됩니다. 이렇게 하면 더 정확한 결과를 얻을 수 있도록 메트릭이 집계 되는 데 더 많은 시간이 소요 됩니다. 

Azure Portal에서 메트릭 경고를 구성 하려면 다음을 수행 합니다.

1. **모든 서비스** > **배치 계정**을 차례로 선택한 다음, 배치 계정의 이름을 선택합니다.
2. **모니터링**아래에서 **경고**를 선택 하 고 **새 경고 규칙**을 선택 합니다.
3. **조건 선택**을 클릭 한 다음 메트릭을 선택 합니다. **차트 기간**, **임계값 유형**, **연산자**및 **집계 유형에**대 한 값을 확인 하 고 **임계값**을 입력 합니다. 그런 후 **완료**를 선택합니다.
4. 기존 작업 그룹을 선택하거나 새 작업 그룹을 만들어서 경고에 작업 그룹을 추가합니다.
5. **경고 규칙 세부 정보** 섹션에서 **경고 규칙 이름** 및 **설명** 을 입력 하 고 **심각도** 를 선택 합니다.
6. **경고 규칙 만들기**를 선택합니다.

메트릭 경고를 만드는 방법에 대 한 자세한 내용은 [Azure Monitor에서 메트릭 경고의 작동 방식 이해](../azure-monitor/platform/alerts-metric-overview.md) 및 [Azure Monitor를 사용 하 여 메트릭 경고 만들기, 보기 및 관리](../azure-monitor/platform/alerts-metric.md)를 참조 하세요.

Azure Monitor [REST API](/rest/api/monitor/)를 사용 하 여 거의 실시간 경고를 구성할 수도 있습니다. 자세한 내용은 [Microsoft Azure의 경고 개요](../azure-monitor/platform/alerts-overview.md)를 참조 하세요. 경고에 작업, 태스크 또는 풀 관련 정보를 포함 하려면 [Azure Monitor 경고를 사용 하 여 이벤트에 응답](../azure-monitor/learn/tutorial-response.md)에서 검색 쿼리에 대 한 정보를 참조 하세요.

## <a name="batch-diagnostics"></a>일괄 처리 진단

진단 로그에는 각 리소스의 작업을 설명하는 Azure 리소스에서 내보낸 정보가 포함됩니다. 일괄 처리의 경우 다음 로그를 수집할 수 있습니다.

- 풀이나 작업 같은 개별 일괄 처리 리소스의 수명 주기 동안 Azure Batch 서비스가 내보내는 **서비스 로그**.
- 계정 수준의 **메트릭** 로그.

진단 로그의 컬렉션을 사용하도록 설정하는 것은 기본적으로 사용되지 않습니다. 모니터링할 각 배치 계정에 대해 진단 로그를 명시적으로 사용하도록 설정해야 합니다.

### <a name="log-destinations"></a>로그 대상

일반적인 시나리오는 로그 대상으로 Azure Storage 계정을 선택하는 것입니다. Azure Storage에 로그를 저장하려면 로그 컬렉션을 사용하도록 설정하기 전에 계정을 만듭니다. 스토리지 계정을 배치 계정과 연결한 경우 해당 계정을 로그 대상으로 선택할 수 있습니다.

또는 다음을 수행할 수 있습니다.

- 일괄 처리 진단 로그 이벤트를 [Azure Event Hub](../event-hubs/event-hubs-about.md)로 스트리밍합니다. Event Hubs는 초당 수백 건의 이벤트를 수집하여 모든 실시간 분석 공급자를 통해 변환 및 저장할 수 있습니다. 
- 진단 로그를 [Azure Monitor 로그](../azure-monitor/log-query/log-query-overview.md)로 보냅니다. 여기서 진단 로그를 분석하거나 추가 분석을 위해 Power BI 또는 Excel로 내보낼 수 있습니다.

> [!NOTE]
> Azure 서비스를 사용하여 진단 로그 데이터를 저장하거나 처리하려면 추가 비용이 발생할 수 있습니다. 

### <a name="enable-collection-of-batch-diagnostic-logs"></a>일괄 처리 진단 로그의 컬렉션을 사용하도록 설정

Azure Portal에서 새 진단 설정을 만들려면 다음 단계를 수행 합니다.

1. Azure Portal에서 **모든 서비스**  >  **배치 계정**을 선택 하 고 batch 계정의 이름을 선택 합니다.
2. **모니터링** 아래에서 **진단 설정**을 선택합니다.
3. **진단 설정**에서 **진단 설정 추가**를 선택 합니다.
4. 설정의 이름을 입력 합니다.
5. 대상 선택: **Log Analytics에 보내기**, **저장소 계정에 보관**또는 **이벤트 허브로 스트림** 저장소 계정을 선택 하는 경우 필요에 따라 보존 정책을 설정할 수 있습니다. 보존 일 수를 지정하지 않으면 데이터는 스토리지 계정의 수명 동안 보존됩니다.
6. **Servicelog**, **allmetrics**또는 둘 다를 선택 합니다.
7. **저장** 을 선택 하 여 진단 설정을 만듭니다.

[리소스 관리자 템플릿을](../azure-monitor/platform/diagnostic-settings-template.md)사용 하거나 Azure PowerShell 또는 Azure CLI을 사용 하 여 진단 설정을 구성 하기 위해 [Azure Portal에서 Azure Monitor를 통해 수집을 사용 하도록 설정할](../azure-monitor/platform/diagnostic-settings.md) 수도 있습니다. 자세한 내용은 [Azure platform Logs 개요](../azure-monitor/platform/platform-logs-overview.md)를 참조 하세요.

### <a name="access-diagnostics-logs-in-storage"></a>스토리지에서 진단 로그에 액세스

일괄 처리 진단 로그를 스토리지 계정에 보관하는 경우 관련된 이벤트가 발생하자마자 스토리지 계정에 스토리지 컨테이너가 만들어집니다. Blob은 다음과 같은 명명 패턴에 따라 생성됩니다.

```json
insights-{log category name}/resourceId=/SUBSCRIPTIONS/{subscription ID}/
RESOURCEGROUPS/{resource group name}/PROVIDERS/MICROSOFT.BATCH/
BATCHACCOUNTS/{Batch account name}/y={four-digit numeric year}/
m={two-digit numeric month}/d={two-digit numeric day}/
h={two-digit 24-hour clock hour}/m=00/PT1H.json
```

예를 들면 다음과 같습니다.

```json
insights-metrics-pt1m/resourceId=/SUBSCRIPTIONS/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/
RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.BATCH/
BATCHACCOUNTS/MYBATCHACCOUNT/y=2018/m=03/d=05/h=22/m=00/PT1H.json
```

각 `PT1H.json` Blob 파일에는 Blob URL에 지정된 시간 내에서 발생한 JSON 형식의 이벤트가 포함됩니다(예: `h=12`). 현재 시간 동안 이벤트는 발생하는 순서대로 `PT1H.json` 파일에 추가됩니다. 진단 로그 이벤트는 시간당 개별 Blob으로 나뉘므로 분 값(`m=00`)은 항상 `00`입니다. (모든 시간은 UTC입니다.)

다음은 `PT1H.json` 로그 파일의 `PoolResizeCompleteEvent` 항목의 예입니다. 여기에는 전용 노드와 우선 순위가 낮은 노드의 현재 개수와 목표 개수, 그리고 작업의 시작 시간과 종료 시간에 대한 정보가 포함되어 있습니다.

```json
{ "Tenant": "65298bc2729a4c93b11c00ad7e660501", "time": "2019-08-22T20:59:13.5698778Z", "resourceId": "/SUBSCRIPTIONS/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.BATCH/BATCHACCOUNTS/MYBATCHACCOUNT/", "category": "ServiceLog", "operationName": "PoolResizeCompleteEvent", "operationVersion": "2017-06-01", "properties": {"id":"MYPOOLID","nodeDeallocationOption":"Requeue","currentDedicatedNodes":10,"targetDedicatedNodes":100,"currentLowPriorityNodes":0,"targetLowPriorityNodes":0,"enableAutoScale":false,"isAutoPool":false,"startTime":"2019-08-22 20:50:59.522","endTime":"2019-08-22 20:59:12.489","resultCode":"Success","resultMessage":"The operation succeeded"}}
```

저장소 계정에서 진단 로그의 스키마에 대 한 자세한 내용은 [Azure 리소스 로그를 저장소 계정에 보관](../azure-monitor/platform/resource-logs.md#send-to-azure-storage)을 참조 하세요. 스토리지 계정에서 로그를 프로그래밍 방식으로 액세스하려면 Storage API를 사용합니다.

### <a name="service-log-events"></a>서비스 로그 이벤트

Azure Batch 서비스 로그 (수집 된 경우)는 풀 또는 작업과 같은 개별 일괄 처리 리소스의 수명 동안 Azure Batch 서비스에서 내보낸 이벤트를 포함 합니다. 일괄 처리에서 내보내는 각 이벤트는 JSON 형식으로 기록됩니다. 예를 들어 샘플 **풀 만들기 이벤트**의 본문은 다음과 같습니다.

```json
{
    "poolId": "myPool1",
    "displayName": "Production Pool",
    "vmSize": "Small",
    "cloudServiceConfiguration": {
        "osFamily": "5",
        "targetOsVersion": "*"
    },
    "networkConfiguration": {
        "subnetId": " "
    },
    "resizeTimeout": "300000",
    "targetDedicatedComputeNodes": 2,
    "maxTasksPerNode": 1,
    "vmFillType": "Spread",
    "enableAutoscale": false,
    "enableInterNodeCommunication": false,
    "isAutoPool": false
}
```

Batch 서비스에서 내보내는 서비스 로그 이벤트는 다음과 같습니다.

- [풀 만들기](batch-pool-create-event.md)
- [풀 삭제 시작](batch-pool-delete-start-event.md)
- [풀 삭제 완료](batch-pool-delete-complete-event.md)
- [풀 크기 조정 시작](batch-pool-resize-start-event.md)
- [풀 크기 조정 완료](batch-pool-resize-complete-event.md)
- [작업 시작](batch-task-start-event.md)
- [작업 완료](batch-task-complete-event.md)
- [작업 실패](batch-task-fail-event.md)

## <a name="next-steps"></a>다음 단계

- Batch 솔루션을 빌드하는 데 사용할 수 있는 [Batch API 및 도구](batch-apis-tools.md)에 대해 알아봅니다.
- [일괄 처리 솔루션 모니터링](monitoring-overview.md)에 대해 자세히 알아봅니다.
