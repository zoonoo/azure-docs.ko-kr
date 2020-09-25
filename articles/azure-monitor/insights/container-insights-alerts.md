---
title: 컨테이너의 Azure Monitor에 대 한 성능 경고 만들기 | Microsoft Docs
description: 이 문서에서는 컨테이너의 Azure Monitor에서 메모리 및 CPU 사용률에 대 한 로그 쿼리를 기반으로 사용자 지정 경고를 만드는 방법을 설명 합니다.
ms.topic: conceptual
ms.date: 01/07/2020
ms.openlocfilehash: 1041b8bfe299a0daac633e478c906c746759d50f
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/25/2020
ms.locfileid: "91319540"
---
# <a name="how-to-set-up-alerts-for-performance-problems-in-azure-monitor-for-containers"></a>컨테이너용 Azure Monitor 성능 문제에 대한 경고를 설정하는 방법

컨테이너 Azure Monitor Azure Container Instances에 배포 된 컨테이너 작업 부하 또는 AKS (Azure Kubernetes Service)에 호스트 된 관리 되는 Kubernetes 클러스터에 대 한 성능을 모니터링 합니다.

이 문서에서는 다음과 같은 경우에 경고를 사용 하도록 설정 하는 방법을 설명 합니다.

- 클러스터 노드의 CPU 또는 메모리 사용률이 임계값을 초과 하는 경우
- 컨트롤러 내 컨테이너의 CPU 또는 메모리 사용률이 해당 리소스에 설정 된 제한과 비교 하 여 임계값을 초과 하는 경우
- *Notready* 상태 노드 개수
- *실패*, *보류 중*, *알 수 없음*, *실행 중*또는 *성공* pod-단계 수
- 클러스터 노드의 사용 가능한 디스크 공간이 임계값을 초과 하는 경우 

높은 CPU 또는 메모리 사용률 또는 클러스터 노드에서 사용 가능한 디스크 공간 부족에 대 한 경고를 하려면 제공 된 쿼리를 사용 하 여 메트릭 경고 또는 메트릭 측정 경고를 만듭니다. 메트릭 경고는 로그 경고 보다 대기 시간이 짧습니다. 하지만 로그 경고는 고급 쿼리 및 복잡 한 고급 쿼리를 제공 합니다. 로그 경고 쿼리는 날짜/시간을 *현재* 연산자를 사용 하 여 1 시간 뒤로 이동 하 여 현재 날짜와 비교 합니다. 컨테이너의 Azure Monitor 모든 날짜를 UTC (협정 세계시) 형식으로 저장 합니다.

Azure Monitor 경고에 익숙하지 않은 경우 시작 하기 전에 [Microsoft Azure의 경고 개요](../platform/alerts-overview.md) 를 참조 하세요. 로그 쿼리를 사용 하는 경고에 대해 자세히 알아보려면 [Azure Monitor의 로그 경고](../platform/alerts-unified-log.md)를 참조 하세요. 메트릭 경고에 대 한 자세한 내용은 [Azure Monitor에서 메트릭 경고](../platform/alerts-metric-overview.md)를 참조 하세요.

## <a name="resource-utilization-log-search-queries"></a>리소스 사용률 로그 검색 쿼리

이 섹션의 쿼리는 각 경고 시나리오를 지원 합니다. 이 문서에서 [경고 만들기](#create-an-alert-rule) 섹션의 7 단계에서 사용 됩니다.

다음 쿼리는 평균 CPU 사용률을 1 분 마다 구성원 노드의 CPU 사용률의 평균으로 계산 합니다.  

```kusto
let endDateTime = now();
let startDateTime = ago(1h);
let trendBinSize = 1m;
let capacityCounterName = 'cpuCapacityNanoCores';
let usageCounterName = 'cpuUsageNanoCores';
KubeNodeInventory
| where TimeGenerated < endDateTime
| where TimeGenerated >= startDateTime
// cluster filter would go here if multiple clusters are reporting to the same Log Analytics workspace
| distinct ClusterName, Computer
| join hint.strategy=shuffle (
  Perf
  | where TimeGenerated < endDateTime
  | where TimeGenerated >= startDateTime
  | where ObjectName == 'K8SNode'
  | where CounterName == capacityCounterName
  | summarize LimitValue = max(CounterValue) by Computer, CounterName, bin(TimeGenerated, trendBinSize)
  | project Computer, CapacityStartTime = TimeGenerated, CapacityEndTime = TimeGenerated + trendBinSize, LimitValue
) on Computer
| join kind=inner hint.strategy=shuffle (
  Perf
  | where TimeGenerated < endDateTime + trendBinSize
  | where TimeGenerated >= startDateTime - trendBinSize
  | where ObjectName == 'K8SNode'
  | where CounterName == usageCounterName
  | project Computer, UsageValue = CounterValue, TimeGenerated
) on Computer
| where TimeGenerated >= CapacityStartTime and TimeGenerated < CapacityEndTime
| project ClusterName, Computer, TimeGenerated, UsagePercent = UsageValue * 100.0 / LimitValue
| summarize AggregatedValue = avg(UsagePercent) by bin(TimeGenerated, trendBinSize), ClusterName
```

다음 쿼리는 평균 메모리 사용률을 1 분 마다 멤버 노드의 메모리 사용률의 평균으로 계산 합니다.

```kusto
let endDateTime = now();
let startDateTime = ago(1h);
let trendBinSize = 1m;
let capacityCounterName = 'memoryCapacityBytes';
let usageCounterName = 'memoryRssBytes';
KubeNodeInventory
| where TimeGenerated < endDateTime
| where TimeGenerated >= startDateTime
// cluster filter would go here if multiple clusters are reporting to the same Log Analytics workspace
| distinct ClusterName, Computer
| join hint.strategy=shuffle (
  Perf
  | where TimeGenerated < endDateTime
  | where TimeGenerated >= startDateTime
  | where ObjectName == 'K8SNode'
  | where CounterName == capacityCounterName
  | summarize LimitValue = max(CounterValue) by Computer, CounterName, bin(TimeGenerated, trendBinSize)
  | project Computer, CapacityStartTime = TimeGenerated, CapacityEndTime = TimeGenerated + trendBinSize, LimitValue
) on Computer
| join kind=inner hint.strategy=shuffle (
  Perf
  | where TimeGenerated < endDateTime + trendBinSize
  | where TimeGenerated >= startDateTime - trendBinSize
  | where ObjectName == 'K8SNode'
  | where CounterName == usageCounterName
  | project Computer, UsageValue = CounterValue, TimeGenerated
) on Computer
| where TimeGenerated >= CapacityStartTime and TimeGenerated < CapacityEndTime
| project ClusterName, Computer, TimeGenerated, UsagePercent = UsageValue * 100.0 / LimitValue
| summarize AggregatedValue = avg(UsagePercent) by bin(TimeGenerated, trendBinSize), ClusterName
```
>[!IMPORTANT]
>다음 쿼리는 자리 표시자 값 및를 사용 \<your-cluster-name> \<your-controller-name> 하 여 클러스터와 컨트롤러를 나타냅니다. 경고를 설정할 때 사용자 환경에 특정 한 값으로 대체 합니다.

다음 쿼리는 컨트롤러에 있는 모든 컨테이너의 평균 CPU 사용률을 1 분 마다 컨트롤러에 있는 모든 컨테이너 인스턴스의 평균 CPU 사용률으로 계산 합니다. 측정값은 컨테이너에 대해 설정 된 한도의 백분율입니다.

```kusto
let endDateTime = now();
let startDateTime = ago(1h);
let trendBinSize = 1m;
let capacityCounterName = 'cpuLimitNanoCores';
let usageCounterName = 'cpuUsageNanoCores';
let clusterName = '<your-cluster-name>';
let controllerName = '<your-controller-name>';
KubePodInventory
| where TimeGenerated < endDateTime
| where TimeGenerated >= startDateTime
| where ClusterName == clusterName
| where ControllerName == controllerName
| extend InstanceName = strcat(ClusterId, '/', ContainerName),
         ContainerName = strcat(controllerName, '/', tostring(split(ContainerName, '/')[1]))
| distinct Computer, InstanceName, ContainerName
| join hint.strategy=shuffle (
    Perf
    | where TimeGenerated < endDateTime
    | where TimeGenerated >= startDateTime
    | where ObjectName == 'K8SContainer'
    | where CounterName == capacityCounterName
    | summarize LimitValue = max(CounterValue) by Computer, InstanceName, bin(TimeGenerated, trendBinSize)
    | project Computer, InstanceName, LimitStartTime = TimeGenerated, LimitEndTime = TimeGenerated + trendBinSize, LimitValue
) on Computer, InstanceName
| join kind=inner hint.strategy=shuffle (
    Perf
    | where TimeGenerated < endDateTime + trendBinSize
    | where TimeGenerated >= startDateTime - trendBinSize
    | where ObjectName == 'K8SContainer'
    | where CounterName == usageCounterName
    | project Computer, InstanceName, UsageValue = CounterValue, TimeGenerated
) on Computer, InstanceName
| where TimeGenerated >= LimitStartTime and TimeGenerated < LimitEndTime
| project Computer, ContainerName, TimeGenerated, UsagePercent = UsageValue * 100.0 / LimitValue
| summarize AggregatedValue = avg(UsagePercent) by bin(TimeGenerated, trendBinSize) , ContainerName
```

다음 쿼리는 컨트롤러에 있는 모든 컨테이너의 평균 메모리 사용률을 1 분 마다 컨트롤러에 있는 모든 컨테이너 인스턴스의 평균 메모리 사용률으로 계산 합니다. 측정값은 컨테이너에 대해 설정 된 한도의 백분율입니다.

```kusto
let endDateTime = now();
let startDateTime = ago(1h);
let trendBinSize = 1m;
let capacityCounterName = 'memoryLimitBytes';
let usageCounterName = 'memoryRssBytes';
let clusterName = '<your-cluster-name>';
let controllerName = '<your-controller-name>';
KubePodInventory
| where TimeGenerated < endDateTime
| where TimeGenerated >= startDateTime
| where ClusterName == clusterName
| where ControllerName == controllerName
| extend InstanceName = strcat(ClusterId, '/', ContainerName),
         ContainerName = strcat(controllerName, '/', tostring(split(ContainerName, '/')[1]))
| distinct Computer, InstanceName, ContainerName
| join hint.strategy=shuffle (
    Perf
    | where TimeGenerated < endDateTime
    | where TimeGenerated >= startDateTime
    | where ObjectName == 'K8SContainer'
    | where CounterName == capacityCounterName
    | summarize LimitValue = max(CounterValue) by Computer, InstanceName, bin(TimeGenerated, trendBinSize)
    | project Computer, InstanceName, LimitStartTime = TimeGenerated, LimitEndTime = TimeGenerated + trendBinSize, LimitValue
) on Computer, InstanceName
| join kind=inner hint.strategy=shuffle (
    Perf
    | where TimeGenerated < endDateTime + trendBinSize
    | where TimeGenerated >= startDateTime - trendBinSize
    | where ObjectName == 'K8SContainer'
    | where CounterName == usageCounterName
    | project Computer, InstanceName, UsageValue = CounterValue, TimeGenerated
) on Computer, InstanceName
| where TimeGenerated >= LimitStartTime and TimeGenerated < LimitEndTime
| project Computer, ContainerName, TimeGenerated, UsagePercent = UsageValue * 100.0 / LimitValue
| summarize AggregatedValue = avg(UsagePercent) by bin(TimeGenerated, trendBinSize) , ContainerName
```

다음 쿼리는 *준비* 됨 및 *notready*상태의 모든 노드와 개수를 반환 합니다.

```kusto
let endDateTime = now();
let startDateTime = ago(1h);
let trendBinSize = 1m;
let clusterName = '<your-cluster-name>';
KubeNodeInventory
| where TimeGenerated < endDateTime
| where TimeGenerated >= startDateTime
| distinct ClusterName, Computer, TimeGenerated
| summarize ClusterSnapshotCount = count() by bin(TimeGenerated, trendBinSize), ClusterName, Computer
| join hint.strategy=broadcast kind=inner (
    KubeNodeInventory
    | where TimeGenerated < endDateTime
    | where TimeGenerated >= startDateTime
    | summarize TotalCount = count(), ReadyCount = sumif(1, Status contains ('Ready'))
                by ClusterName, Computer,  bin(TimeGenerated, trendBinSize)
    | extend NotReadyCount = TotalCount - ReadyCount
) on ClusterName, Computer, TimeGenerated
| project   TimeGenerated,
            ClusterName,
            Computer,
            ReadyCount = todouble(ReadyCount) / ClusterSnapshotCount,
            NotReadyCount = todouble(NotReadyCount) / ClusterSnapshotCount
| order by ClusterName asc, Computer asc, TimeGenerated desc
```
다음 쿼리는 모든 단계 ( *실패*, *보류 중*, *알 수 없음*, *실행 중*또는 *성공*)를 기반으로 pod 단계 수를 반환 합니다.  

```kusto
let endDateTime = now();
    let startDateTime = ago(1h);
    let trendBinSize = 1m;
    let clusterName = '<your-cluster-name>';
    KubePodInventory
    | where TimeGenerated < endDateTime
    | where TimeGenerated >= startDateTime
    | where ClusterName == clusterName
    | distinct ClusterName, TimeGenerated
    | summarize ClusterSnapshotCount = count() by bin(TimeGenerated, trendBinSize), ClusterName
    | join hint.strategy=broadcast (
        KubePodInventory
        | where TimeGenerated < endDateTime
        | where TimeGenerated >= startDateTime
        | distinct ClusterName, Computer, PodUid, TimeGenerated, PodStatus
        | summarize TotalCount = count(),
                    PendingCount = sumif(1, PodStatus =~ 'Pending'),
                    RunningCount = sumif(1, PodStatus =~ 'Running'),
                    SucceededCount = sumif(1, PodStatus =~ 'Succeeded'),
                    FailedCount = sumif(1, PodStatus =~ 'Failed')
                 by ClusterName, bin(TimeGenerated, trendBinSize)
    ) on ClusterName, TimeGenerated
    | extend UnknownCount = TotalCount - PendingCount - RunningCount - SucceededCount - FailedCount
    | project TimeGenerated,
              TotalCount = todouble(TotalCount) / ClusterSnapshotCount,
              PendingCount = todouble(PendingCount) / ClusterSnapshotCount,
              RunningCount = todouble(RunningCount) / ClusterSnapshotCount,
              SucceededCount = todouble(SucceededCount) / ClusterSnapshotCount,
              FailedCount = todouble(FailedCount) / ClusterSnapshotCount,
              UnknownCount = todouble(UnknownCount) / ClusterSnapshotCount
| summarize AggregatedValue = avg(PendingCount) by bin(TimeGenerated, trendBinSize)
```

>[!NOTE]
>*보류 중*, *실패*또는 *알 수 없음*과 같은 특정 pod 단계에 대해 경고 하려면 쿼리의 마지막 줄을 수정 합니다. 예를 들어 *Failedcount* 에 대해 경고 하려면 다음을 사용 합니다. <br/>`| summarize AggregatedValue = avg(FailedCount) by bin(TimeGenerated, trendBinSize)`

다음 쿼리는 사용 가능한 90%의 사용 가능한 공간을 초과 하는 클러스터 노드 디스크를 반환 합니다. 클러스터 ID를 가져오려면 먼저 다음 쿼리를 실행 하 고 속성의 값을 복사 합니다 `ClusterId` .

```kusto
InsightsMetrics
| extend Tags = todynamic(Tags)            
| project ClusterId = Tags['container.azm.ms/clusterId']   
| distinct tostring(ClusterId)   
``` 

```kusto
let clusterId = '<cluster-id>';
let endDateTime = now();
let startDateTime = ago(1h);
let trendBinSize = 1m;
InsightsMetrics
| where TimeGenerated < endDateTime
| where TimeGenerated >= startDateTime
| where Origin == 'container.azm.ms/telegraf'            
| where Namespace == 'container.azm.ms/disk'            
| extend Tags = todynamic(Tags)            
| project TimeGenerated, ClusterId = Tags['container.azm.ms/clusterId'], Computer = tostring(Tags.hostName), Device = tostring(Tags.device), Path = tostring(Tags.path), DiskMetricName = Name, DiskMetricValue = Val   
| where ClusterId =~ clusterId       
| where DiskMetricName == 'used_percent'
| summarize AggregatedValue = max(DiskMetricValue) by bin(TimeGenerated, trendBinSize)
| where AggregatedValue >= 90
```

## <a name="create-an-alert-rule"></a>경고 규칙 만들기

앞에서 제공 된 로그 검색 규칙 중 하나를 사용 하 여 Azure Monitor에서 로그 경고를 만들려면 다음 단계를 수행 합니다. ARM 템플릿을 사용 하 여 만들려면 [Azure 리소스 템플릿을 사용 하 여 로그 경고 생성 샘플](../platform/alerts-log-create-templates.md)을 참조 하세요.

>[!NOTE]
>컨테이너 리소스 사용률에 대 한 경고 규칙을 만들기 위해 다음 절차에서는 [로그 경고에 대 한 api 기본 설정 전환](../platform/alerts-log-api-switch.md)에 설명 된 대로 새 로그 경고 API로 전환 해야 합니다.
>

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. Azure Portal에서 **Log Analytics 작업 영역**을 검색하여 선택합니다.
3. Log Analytics 작업 영역 목록에서 컨테이너에 대 한 Azure Monitor를 지 원하는 작업 영역을 선택 합니다. 
4. 왼쪽 창에서 **로그** 를 선택 하 여 Azure Monitor 로그 페이지를 엽니다. 이 페이지를 사용 하 여 Azure Log Analytics 쿼리를 작성 하 고 실행 합니다.
5. **로그** 페이지에서 이전에 제공 된 [쿼리](#resource-utilization-log-search-queries) 중 하나를 **검색 쿼리** 필드에 붙여넣은 다음 **실행** 을 선택 하 여 결과의 유효성을 검사 합니다. 이 단계를 수행 하지 않으면 **+ 새 경고** 옵션을 선택할 수 없습니다.
6. **+ 새 경고** 를 선택 하 여 로그 경고를 만듭니다.
7. **조건** 섹션에서 **사용자 지정 로그 \<logic undefined> 검색이** 미리 정의 된 사용자 지정 로그 조건이 될 때마다를 선택 합니다. **사용자 지정 로그 검색** 신호 유형은 Azure Monitor 로그 페이지에서 직접 경고 규칙을 만들기 때문에 자동으로 선택 됩니다.  
8. 이전에 제공 된 [쿼리](#resource-utilization-log-search-queries) 중 하나를 **검색 쿼리** 필드에 붙여넣습니다.
9. 다음과 같이 경고를 구성 합니다.

    1. **기준** 드롭다운 목록에서 **메트릭 측정**을 선택 합니다. 메트릭 측정은 쿼리에서 지정 된 임계값 보다 높은 값을 갖는 각 개체에 대 한 경고를 생성 합니다.
    1. **조건**에 대해 **보다 큼**을 선택 하 고 CPU 및 메모리 사용률 경고에 대 한 초기 기준 **임계값** 으로 **75** 을 입력 합니다. 디스크 공간 부족 경고에 대해 **90**을 입력 합니다. 또는 조건을 충족 하는 다른 값을 입력 합니다.
    1. **경고 트리거 기준** 섹션에서 **연속 위반**을 선택 합니다. 드롭다운 목록에서 **보다 큼**을 선택 하 고 **2**를 입력 합니다.
    1. 컨테이너 CPU 또는 메모리 사용률에 대 한 경고를 구성 하려면 **집계 설정**에서 **ContainerName**을 선택 합니다. 클러스터 노드 디스크 부족 경고에 대해 구성 하려면 **ClusterId**를 선택 합니다.
    1. **평가 기준** 섹션에서 **기간** 값을 **60 분**으로 설정 합니다. 규칙은 5 분 마다 실행 되며 현재 시간에서 지난 1 시간 이내에 생성 된 레코드를 반환 합니다. 잠재적 데이터 대기 시간에 대 한 기간을 와이드 창 계정으로 설정 합니다. 또한 경고가 발생 하지 않는 거짓 음수를 방지 하기 위해 쿼리에서 데이터를 반환 하는지 확인 합니다.

10. **완료** 를 선택 하 여 경고 규칙을 완료 합니다.
11. **경고 규칙 이름** 필드에 이름을 입력 합니다. 경고에 대 한 세부 정보를 제공 하는 **설명을** 지정 합니다. 제공 된 옵션에서 적절 한 심각도 수준을 선택 합니다.
12. 경고 규칙을 즉시 활성화 하려면 **만들 때 규칙 사용**의 기본값을 그대로 사용 합니다.
13. 기존 **작업 그룹** 을 선택 하거나 새 그룹을 만듭니다. 이 단계를 수행 하면 경고가 트리거될 때마다 동일한 작업이 수행 됩니다. IT 또는 DevOps 운영 팀에서 인시던트를 관리 하는 방법에 따라 구성 합니다.
14. 경고 규칙 **만들기** 를 선택 하 여 경고 규칙을 완료 합니다. 그 즉시 실행이 시작됩니다.

## <a name="next-steps"></a>다음 단계

- [로그 쿼리 예](container-insights-log-search.md#search-logs-to-analyze-data) 를 확인 하 여 미리 정의 된 쿼리 및 예제를 확인 하거나 사용자 지정 하 여 클러스터에 대 한 경고, 시각화 또는 분석을 평가 하거나 사용자 지정 합니다.
- Azure Monitor 및 Kubernetes 클러스터의 다른 측면을 모니터링 하는 방법에 대 한 자세한 내용은 [Kubernetes cluster performance](container-insights-analyze.md) 및 [view Kubernetes cluster health](container-insights-health.md)를 참조 하세요.
