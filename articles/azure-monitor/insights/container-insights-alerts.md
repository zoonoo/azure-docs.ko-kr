---
title: 컨테이너에 대한 Azure 모니터에 대한 성능 경고 만들기 | 마이크로 소프트 문서
description: 이 문서에서는 컨테이너에 대한 Azure Monitor의 메모리 및 CPU 사용률에 대한 로그 쿼리를 기반으로 사용자 지정 경고를 만드는 방법에 대해 설명합니다.
ms.topic: conceptual
ms.date: 01/07/2020
ms.openlocfilehash: 5d73f4399d10683597fb2a2e8a3a2ab4ba0d1165
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75730928"
---
# <a name="how-to-set-up-alerts-for-performance-problems-in-azure-monitor-for-containers"></a>컨테이너용 Azure Monitor 성능 문제에 대한 경고를 설정하는 방법

컨테이너용 Azure Monitor는 Azure 컨테이너 인스턴스또는 AKS(Azure Kubernetes Service)에서 호스팅되는 관리되는 Kubernetes 클러스터에 배포되는 컨테이너 워크로드의 성능을 모니터링합니다.

이 문서에서는 다음 상황에 대한 경고를 사용하도록 설정하는 방법에 대해 설명합니다.

- 클러스터 노드의 CPU 또는 메모리 사용률이 임계값을 초과하는 경우
- 컨트롤러 내의 컨테이너에 대한 CPU 또는 메모리 사용률이 해당 리소스에 설정된 제한과 비교하여 임계값을 초과하는 경우
- *NotReady* 상태 노드 수
- *실패*, *보류 중*, 알 *수 없음,* *실행*중 또는 *성공한* 포드 위상 개수
- 클러스터 노드의 여유 디스크 공간이 임계값을 초과하는 경우 

높은 CPU 또는 메모리 사용률 또는 클러스터 노드의 낮은 사용 률디스크 공간에 대해 경고하려면 제공된 쿼리를 사용하여 메트릭 경고 또는 메트릭 측정 경고를 만듭니다. 메트릭 경고는 로그 경고보다 대기 시간이 낮습니다. 그러나 로그 경고는 고급 쿼리와 더 정교함을 제공합니다. 로그 경고 쿼리는 *현재* 연산자 및 1시간 뒤로 이동하여 날짜 시간을 현재와 비교합니다. 컨테이너용 Azure 모니터는 모든 날짜를 UTC(조정된 유니버설 타임) 형식으로 저장합니다.

Azure 모니터 경고에 익숙하지 않은 경우 시작하기 전에 [Microsoft Azure의 경고 개요를](../platform/alerts-overview.md) 참조하세요. 로그 쿼리를 사용하는 경고에 대한 자세한 내용은 [Azure Monitor의 로그 경고를](../platform/alerts-unified-log.md)참조하십시오. 메트릭 경고에 대한 자세한 내용은 [Azure 모니터의 메트릭 경고를](../platform/alerts-metric-overview.md)참조하십시오.

## <a name="resource-utilization-log-search-queries"></a>리소스 사용률 로그 검색 쿼리

이 섹션의 쿼리는 각 경고 시나리오를 지원합니다. 이 문서의 [경고 만들기](#create-an-alert-rule) 섹션의 7단계에서 사용됩니다.

다음 쿼리는 평균 CPU 사용률을 멤버 노드의 CPU 사용률의 평균으로 분당 계산합니다.  

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

다음 쿼리는 평균 메모리 사용률을 1분마다 멤버 노드의 메모리 사용률의 평균으로 계산합니다.

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
>다음 쿼리는 클러스터 이름 \<> \<자리 표시자 값을 사용하고 컨트롤러 이름> 사용하여 클러스터 및 컨트롤러를 나타냅니다. 경고를 설정할 때 사용자 환경에 특정한 값으로 바꿉습니다.

다음 쿼리는 컨트롤러의 모든 컨테이너 의 평균 CPU 사용률을 매 분마다 컨트롤러의 모든 컨테이너 인스턴스의 CPU 사용률평균으로 계산합니다. 측정값은 컨테이너에 대해 설정된 제한의 백분율입니다.

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

다음 쿼리는 컨트롤러의 모든 컨테이너의 평균 메모리 사용률을 매 분마다 컨트롤러의 모든 컨테이너 인스턴스의 평균 메모리 사용률로 계산합니다. 측정값은 컨테이너에 대해 설정된 제한의 백분율입니다.

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

다음 쿼리는 *Ready* 및 *NotReady*의 상태가 있는 모든 노드 및 개수를 반환합니다.

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
다음 쿼리는 *실패,* *보류 중,* *실행* *중*또는 *성공*의 모든 단계를 기반으로 포드 단계 수를 반환합니다.  

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
>*보류 중*, *실패*또는 알 *수 없음*과 같은 특정 포드 단계에 대해 경고하려면 쿼리의 마지막 줄을 수정합니다. 예를 들어 *FailedCount* 사용에 대해 경고하려면 다음을 수행합니다. <br/>`| summarize AggregatedValue = avg(FailedCount) by bin(TimeGenerated, trendBinSize)`

다음 쿼리는 사용된 90%의 사용 공간을 초과하는 클러스터 노드 디스크를 반환합니다. 클러스터 ID를 얻으려면 먼저 다음 쿼리를 실행하고 속성에서 값을 복사합니다. `ClusterId`

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

다음 단계에 따라 이전에 제공된 로그 검색 규칙 중 하나를 사용하여 Azure Monitor에서 로그 경고를 만듭니다. ARM 템플릿을 사용하여 만들려면 [Azure 리소스 템플릿을 사용하여 샘플 로그 경고 만들기를](../platform/alerts-log.md#sample-log-alert-creation-using-azure-resource-template)참조하십시오.

>[!NOTE]
>컨테이너 리소스 사용률에 대한 경고 규칙을 만드는 다음 절차에서는 로그 경고에 [대한 Switch API 기본 설정에](../platform/alerts-log-api-switch.md)설명된 대로 새 로그 경고 API로 전환해야 합니다.
>

1. [Azure 포털에](https://portal.azure.com)로그인합니다.
2. Azure 포털에서 **로그 분석 작업 영역을**검색하고 선택합니다.
3. 로그 분석 작업 영역 목록에서 컨테이너에 대한 Azure 모니터를 지원하는 작업 영역을 선택합니다. 
4. 왼쪽 창에서 **로그를** 선택하여 Azure Monitor 로그 페이지를 엽니다. 이 페이지를 사용하여 Azure Log Analytics 쿼리를 작성하고 실행합니다.
5. **로그** 페이지에서 검색 **쿼리** 필드에 앞에서 제공한 [쿼리](#resource-utilization-log-search-queries) 중 하나를 붙여넣은 다음 **실행을** 선택하여 결과의 유효성을 검사합니다. 이 단계를 수행하지 않으면 **+New 경고** 옵션을 선택할 수 없습니다.
6. **+새 경고를** 선택하여 로그 경고를 만듭니다.
7. **조건** 섹션에서 **사용자 지정 로그 검색이 \<정의되지 않은 논리가**>미리 정의된 사용자 지정 로그 조건을 선택합니다. Azure Monitor 로그 페이지에서 직접 경고 규칙을 만들기 때문에 **사용자 지정 로그 검색** 신호 유형이 자동으로 선택됩니다.  
8. 이전에 제공된 [쿼리](#resource-utilization-log-search-queries) 중 하나를 **검색 쿼리** 필드에 붙여넣습니다.
9. 다음과 같이 경고를 구성합니다.

    1. **드롭다운** 목록에서 **메트릭 측정을**선택합니다. 메트릭 측정은 지정된 임계값을 초과하는 값이 있는 쿼리의 각 개체에 대한 경고를 만듭니다.
    1. **조건의**경우 보다 **큰**을 선택하고 CPU 및 메모리 사용률 경고에 대한 초기 기준 **임계값으로** **75를** 입력합니다. 낮은 디스크 공간 경고의 경우 **90을**입력합니다. 또는 기준을 충족하는 다른 값을 입력합니다.
    1. 트리거 **경고 에 따라** 섹션에서 **연속 위반을 선택합니다.** 드롭다운 목록에서 **보다 큰**을 선택하고 **2를**입력합니다.
    1. 컨테이너 CPU 또는 메모리 사용률에 대한 경고를 구성하려면 **에서 집계 에서**컨테이너 **이름을**선택합니다. 클러스터 노드 낮은 디스크 경고를 구성하려면 **ClusterId**를 선택합니다.
    1. **섹션에 따라 평가된** 섹션에서 **기간** 값을 **60분으로**설정합니다. 규칙은 5분마다 실행되고 현재 시간에서 마지막 1시간 이내에 생성된 레코드를 반환합니다. 기간을 넓은 창으로 설정하면 잠재적인 데이터 대기 시간이 설명됩니다. 또한 경고가 발생하지 않는 거짓 부정을 피하기 위해 쿼리가 데이터를 반환하도록 합니다.

10. 완료를 **Done** 선택하여 경고 규칙을 완료합니다.
11. **Alert 규칙 이름** 필드에 이름을 입력합니다. 경고에 대한 세부 정보를 제공하는 **설명을** 지정합니다. 제공된 옵션에서 적절한 심각도 수준을 선택합니다.
12. 경고 규칙을 즉시 활성화하려면 만들 때 Enable 규칙에 대한 기본값을 **수락합니다.**
13. 기존 **작업 그룹을** 선택하거나 새 그룹을 만듭니다. 이 단계는 경고가 트리거될 때마다 동일한 작업을 수행하도록 합니다. IT 또는 DevOps 운영 팀이 인시던트를 관리하는 방법에 따라 구성합니다.
14. **경고 규칙 만들기를** 선택하여 경고 규칙을 완료합니다. 그 즉시 실행이 시작됩니다.

## <a name="next-steps"></a>다음 단계

- [로그 쿼리 예제를](container-insights-log-search.md#search-logs-to-analyze-data) 보고 미리 정의된 쿼리 및 예제를 보고 클러스터에 대한 경고, 시각화 또는 분석을 위해 평가하거나 사용자 지정합니다.
- Azure 모니터및 Kubernetes 클러스터의 다른 측면을 모니터링하는 방법에 대한 자세한 내용은 [Kubernetes 클러스터 성능 보기](container-insights-analyze.md) 및 [Kubernetes 클러스터 상태 보기를](container-insights-health.md)참조하십시오.
