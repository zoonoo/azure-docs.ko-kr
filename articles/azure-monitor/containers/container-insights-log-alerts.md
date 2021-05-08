---
title: 컨테이너 인사이트의 로그 경고 | Microsoft Docs
description: 이 문서에서는 컨테이너 인사이트에서 메모리 및 CPU 사용률에 대한 사용자 지정 로그 경고를 만드는 방법을 설명합니다.
ms.topic: conceptual
ms.date: 01/05/2021
ms.openlocfilehash: 64d499d69194ac338d367ae094e42f4c8af23bef
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101711198"
---
# <a name="how-to-create-log-alerts-from-container-insights"></a>컨테이너 인사이트에서 로그 경고를 만드는 방법

컨테이너 인사이트는 관리형 또는 자체 관리형 Kubernetes 클러스터에 배포된 컨테이너 워크로드의 성능을 모니터링합니다. 중요한 문제가 생겼을 경우 경고하기 위해, 이 문서에서는 AKS 클러스터를 사용하여 다음과 같은 상황에 대한 로그 기반 경고를 만드는 방법을 설명합니다.

- 클러스터 노드의 CPU 또는 메모리 사용률이 임계값을 초과하는 경우
- 컨트롤러 내 컨테이너의 CPU 또는 메모리 사용률이 해당 리소스에 설정된 제한과 비교하여 임계값을 초과하는 경우
- *NotReady* 상태 노드 개수
- *Failed*, *Pending*, *Unknown*, *Running* 또는 *Succeeded* Pod 단계 개수
- 클러스터 노드의 사용 가능한 디스크 공간이 임계값을 초과하는 경우

높은 CPU나 메모리 사용률, 또는 클러스터 노드에서 사용 가능한 디스크 공간 부족에 대해 경고하려면 제공된 쿼리를 사용하여 메트릭 경고 또는 메트릭 측정 경고를 만듭니다. 메트릭 경고는 로그 경고보다 대기 시간이 낮지만, 로그 경고는 고급 쿼리 기능을 제공하며 한층 정교합니다. 로그 경고 쿼리는 *now* 연산자를 사용하고 1시간 이전으로 돌아가는 방법으로 현재와 날짜/시간을 비교합니다. (컨테이너 인사이트는 모든 날짜를 UTC(협정 세계시) 형식으로 저장합니다.)

Azure Monitor 경고에 익숙하지 않은 경우 시작하기 전에 [Microsoft Azure의 경고 개요](../alerts/alerts-overview.md)를 참조하세요. 로그 쿼리를 사용하는 경고에 대한 자세한 내용은 [Azure Monitor의 로그 경고](../alerts/alerts-unified-log.md)를 참조하세요. 메트릭 경고에 대한 자세한 내용은 [Azure Monitor의 메트릭 경고](../alerts/alerts-metric-overview.md)를 참조하세요.

## <a name="resource-utilization-log-search-queries"></a>리소스 사용률 로그 검색 쿼리

이 섹션의 쿼리는 각 경고 시나리오를 지원하며 이 문서의 [경고 만들기](#create-an-alert-rule) 섹션의 7단계에서 사용됩니다.

다음 쿼리는 평균 CPU 활용률을 분당 멤버 노드 CPU 활용률의 평균으로 계산합니다.  

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

다음 쿼리는 평균 메모리 활용률을 분당 멤버 노드 메모리 활용률의 평균으로 계산합니다.

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
>다음 쿼리는 자리 표시자 값 \<your-cluster-name> 및 \<your-controller-name>를 사용하여 클러스터와 컨트롤러를 나타냅니다. 경고 설정 시 해당 값을 사용자 환경에 맞는 값으로 바꿉니다.

다음 쿼리는 컨트롤러에 있는 모든 컨테이너의 평균 CPU 사용률을 1분마다 컨트롤러에 있는 모든 컨테이너 인스턴스의 평균 CPU 사용률로 계산합니다. 측정값은 컨테이너에 설정된 제한의 백분율입니다.

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

다음 쿼리는 컨트롤러에 있는 모든 컨테이너의 평균 메모리 사용률을 1분마다 컨트롤러에 있는 모든 컨테이너 인스턴스의 평균 메모리 사용률로 계산합니다. 측정값은 컨테이너에 설정된 제한의 백분율입니다.

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

다음 쿼리는 *Ready* 및 *NotReady* 상태의 모든 노드와 개수를 반환합니다.

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
다음 쿼리는 모든 단계(*Failed*, *Pending*, *Unknown*, *Running* 또는 *Succeeded*)를 기반으로 Pod 단계 개수를 반환합니다.  

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
        | summarize PodStatus=any(PodStatus) by TimeGenerated, PodUid, ClusterName
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
>*Pending*, *Failed* 또는 *Unknown* 과 같은 특정 Pod 단계에 대해 경고하려면 쿼리의 마지막 줄을 수정합니다. 예를 들어 *FailedCount* 에 대해 경고하려면 다음을 사용합니다. <br/>`| summarize AggregatedValue = avg(FailedCount) by bin(TimeGenerated, trendBinSize)`

다음 쿼리는 사용 가능한 공간의 사용률이 90%를 초과하는 클러스터 노드 디스크를 반환합니다. 클러스터 ID를 가져오려면 먼저 다음 쿼리를 실행하고 `ClusterId` 속성의 값을 복사합니다.

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

이 섹션에서는 컨테이너 인사이트의 성능 데이터를 사용하여 메트릭 측정 경고 규칙을 만드는 과정을 안내합니다. 다양한 로그 쿼리에서 해당 기본 프로세스를 사용하여 여러 가지 성능 카운터에 대해 경고할 수 있습니다. 시작하려면 이전에 제공된 로그 검색 쿼리 중 하나를 사용합니다. ARM 템플릿을 사용하여 만들려면 [Azure 리소스 템플릿을 사용하여 샘플 로그 경고 만들기](../alerts/alerts-log-create-templates.md)를 참조하세요.

>[!NOTE]
>컨테이너 리소스 사용률에 대한 경고 규칙을 만들기 위해, 다음 절차에서는 [로그 경고의 API 기본 설정 전환](../alerts/alerts-log-api-switch.md)에 설명되어 있는 대로 새 로그 경고 API로 전환해야 합니다.
>

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. Azure Portal에서 **Log Analytics 작업 영역** 을 검색하여 선택합니다.
3. Log Analytics 작업 영역 목록에서 컨테이너 인사이트를 지원하는 작업 영역을 선택합니다. 
4. 왼쪽 창에서 **로그** 를 선택하여 Azure Monitor 로그 페이지를 엽니다. 이 페이지를 사용하여 Azure 로그 쿼리를 작성하고 실행하게 됩니다.
5. **로그** 페이지에서 이전에 제공된 [쿼리](#resource-utilization-log-search-queries) 중 하나를 **검색 쿼리** 필드에 붙여넣은 다음 **실행** 을 선택하여 결과의 유효성을 검사합니다. 이 단계를 수행하지 않으면 **+새로운 경고** 옵션을 선택할 수 없습니다.
6. **+새로운 경고** 를 선택하여 로그 경고를 만듭니다.
7. **조건** 섹션에서 **사용자 지정 로그 검색이 \<logic undefined>** 사전 지정된 사용자 지정 로그 조건일 때마다를 선택합니다. Azure Monitor 로그 페이지에서 직접 경고 규칙을 만들기 때문에 **사용자 지정 로그 검색** 신호 유형이 자동으로 선택됩니다.  
8. 이전에 제공된 [쿼리](#resource-utilization-log-search-queries) 중 하나를 **검색 쿼리** 필드에 붙여넣습니다.
9. 다음과 같이 경고를 구성합니다.

    1. **기준** 드롭다운 목록에서 **미터법** 을 선택합니다. 메트릭 측정값은 쿼리에서 지정된 임계값보다 높은 값을 갖는 각 개체에 대해 경고를 만듭니다.
    1. **조건** 에서 **보다 큼** 을 선택한 다음, CPU 및 메모리 사용률 경고에 대해 초기 기준선 **임계값** 으로 **75** 를 입력합니다. 디스크 공간 부족 경고에 대해 **90** 을 입력하거나 원하는 조건을 충족하는 다른 값을 입력합니다.
    1. **경고 트리거 기준** 섹션에서 **연속 위반** 을 선택합니다. 드롭다운 목록에서 **보다 큼** 을 선택하고 **2** 를 입력합니다.
    1. 컨테이너 CPU 또는 메모리 사용률에 대한 경고를 구성하려면 **집계** 에서 **ContainerName** 을 선택합니다. 클러스터 노드 디스크 부족에 대한 경고를 구성하려면 **ClusterId** 를 선택합니다.
    1. **평가 기준** 섹션에서 **기간** 값을 **60분** 으로 수정합니다. 규칙이 5분마다 실행되고 최근 1시간 이내에 만들어진 레코드를 반환합니다. 잠재적 데이터 대기 시간에 대한 기간을 넓은 시간 범위 계정으로 설정합니다. 이렇게 하면 쿼리가 데이터를 반환하도록 하여 경고가 발생하지 않는 거짓 부정을 방지할 수 있습니다.

10. **완료** 를 선택하여 경고 규칙을 완료합니다.
11. **경고 규칙 이름** 필드에 이름을 입력합니다. 경고에 대한 세부 정보를 제공하는 **설명** 을 지정합니다. 제공된 옵션 중에서 적절한 심각도 수준을 선택합니다.
12. 경고 규칙을 즉시 활성화하려면 **규칙을 만들면 바로 사용** 에 기본값을 적용합니다.
13. 기존 **작업 그룹** 을 선택하거나 새 그룹을 만듭니다. 이 단계를 수행하면 경고가 트리거될 때마다 동일한 작업이 수행되도록 할 수 있습니다. IT 또는 DevOps 작업 팀이 인시던트를 관리하는 방식에 따라 구성합니다.
14. **경고 규칙 만들기** 를 선택하여 경고 규칙을 완료합니다. 그 즉시 실행이 시작됩니다.

## <a name="next-steps"></a>다음 단계

- 클러스터를 경고, 시각화 또는 분석하기 위해 평가하거나 사용자 지정하는 미리 정의된 쿼리 및 예제를 보려면 [로그 쿼리 예제](container-insights-log-search.md#search-logs-to-analyze-data)를 확인하세요.

- Azure Monitor 및 Kubernetes 클러스터의 기타 측면을 모니터링하는 방법에 대한 자세한 내용은 [Kubernetes 클러스터 성능 보기](container-insights-analyze.md) 및 [Kubernetes 클러스터 상태 보기](./container-insights-overview.md)를 참조하세요.