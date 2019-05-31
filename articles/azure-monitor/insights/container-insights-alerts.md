---
title: 컨테이너에 대 한 Azure Monitor를 사용 하 여 성능 경고 만들기 | Microsoft Docs
description: 이 문서에서는 컨테이너에 대 한 Azure Monitor를 사용 하 여 메모리 및 CPU 사용률에 대 한 로그 쿼리를 기반으로 하는 사용자 지정 경고를 만드는 방법을 설명 합니다.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/26/2019
ms.author: magoedte
ms.openlocfilehash: 46ac6794272728069d50479f8cd097185bfeeb1a
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/27/2019
ms.locfileid: "65072400"
---
# <a name="how-to-set-up-alerts-for-performance-problems-in-azure-monitor-for-containers"></a>컨테이너에 대 한 Azure Monitor에서 성능 문제에 대 한 경고를 설정 하는 방법
컨테이너에 대 한 azure Monitor 관리 되는 Kubernetes 클러스터 호스트 되는 Azure Kubernetes Service (AKS)에서 또는 Azure Container Instances에 배포 되는 컨테이너 워크 로드의 성능을 모니터링 합니다.

이 문서에서는 다음과 같은 경우에 대 한 경고를 사용 하도록 설정 하는 방법을 설명 합니다.

- 클러스터 노드에서 CPU 또는 메모리 사용률이 임계값을 초과 하는 경우
- 컨트롤러 내의 모든 컨테이너에 CPU 또는 메모리 사용률이 임계값을 비교 하 여 해당 리소스에서 설정 된 제한을 초과 하는 경우
- *NotReady* 상태 노드 계산
- *실패*, *보류 중*, *알 수 없는*를 *실행*, 또는 *성공* pod 단계 수
- 클러스터 노드에서 사용 가능한 디스크 공간이 임계값을 초과 하는 경우 

높은 CPU 또는 메모리 사용률, 사용 가능한 디스크 공간 부족 클러스터 노드에 대 한 경고, 메트릭 경고를 만들거나 메트릭 측정 경고를 제공 하는 쿼리를 사용 합니다. 메트릭 경고의 로그 경고 보다 대기 시간이 낮은 경우 하지만 로그 경고 고급 쿼리 및 큰 정교함을 제공 합니다. 로그 쿼리를 사용 하 여 현재 날짜/시간을 비교 하는 경고는 *이제* 연산자 및 1을 다시 이동 합니다. (컨테이너에 대 한 azure Monitor 저장 모든 날짜 (UTC) 협정 세계시 형식으로 합니다.)

Azure Monitor 경고를 사용 하 여 잘 모르는 경우 [Microsoft Azure의 경고 개요](../platform/alerts-overview.md) 시작 하기 전에 합니다. 로그 쿼리를 사용 하는 경고에 대 한 자세한 내용은 참조 하세요 [Azure Monitor의 로그 경고](../platform/alerts-unified-log.md)합니다. 메트릭 경고에 대 한 자세한 내용은 참조 하세요 [Azure Monitor에서 메트릭 경고](../platform/alerts-metric-overview.md)합니다.

## <a name="resource-utilization-log-search-queries"></a>리소스 사용률 로그 검색 쿼리
이 섹션의 쿼리는 각 경고 시나리오를 지원 합니다. 7 단계에서 사용 하 여 [경고 만들기](#create-an-alert-rule) 이 문서의 섹션입니다.

다음 쿼리는 평균 1 분 마다 멤버 노드의 CPU 사용률의 평균 CPU 사용률을 계산합니다.  

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

다음 쿼리는 멤버 노드의 메모리 사용률이 1 분 마다의 평균으로 평균 메모리 사용률을 계산합니다.

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
>다음 쿼리를 사용 하 여 자리 표시자 값 \<-클러스터-이름 > 및 \<컨트롤러 사용자 이름 > 클러스터 및 컨트롤러를 나타냅니다. 경고를 설정할 때 해당 환경에 특정 값으로 대체 합니다.

다음 쿼리는 1 분 마다 컨트롤러의 모든 컨테이너 인스턴스의 CPU 사용률의 평균으로 컨트롤러의 모든 컨테이너의 평균 CPU 사용률을 계산합니다. 컨테이너에 대 한 설정도의 백분율입니다.

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

다음 쿼리는 1 분 마다 컨트롤러의 모든 컨테이너 인스턴스의 메모리 사용률의 평균으로 컨트롤러의 모든 컨테이너의 평균 메모리 사용률을 계산합니다. 컨테이너에 대 한 설정도의 백분율입니다.

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

다음 쿼리는 모든 노드 및 상태에 있는 개수 반환 *준비* 하 고 *NotReady*합니다.

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
모든 단계를 기반으로 다음 쿼리 반환 pod 단계 수를 계산 합니다. *실패*, *보류 중*를 *알 수 없는*를 *실행*, 또는 *성공*합니다.  

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
>와 같은 특정 pod 단계에서 경고를 발생 시 *보류 중인*, *실패*, 또는 *알 수 없는*, 쿼리의 마지막 줄을 수정 합니다. 에 대 한 경고를 예를 들어 *개의 작업이* 사용: <br/>`| summarize AggregatedValue = avg(FailedCount) by bin(TimeGenerated, trendBinSize)`

다음 쿼리는 사용 가능한 공간을 90%를 초과 하는 클러스터 노드 디스크를 반환 합니다. 클러스터 ID를 가져오려면 먼저 다음 쿼리를 실행 하는 값을 복사 합니다 `ClusterId` 속성:

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
| where Namespace == 'disk'            
| extend Tags = todynamic(Tags)            
| project TimeGenerated, ClusterId = Tags['container.azm.ms/clusterId'], Computer = tostring(Tags.hostName), Device = tostring(Tags.device), Path = tostring(Tags.path), DiskMetricName = Name, DiskMetricValue = Val   
| where ClusterId =~ clusterId       
| where DiskMetricName == 'used_percent'
| summarize AggregatedValue = max(DiskMetricValue) by bin(TimeGenerated, trendBinSize)
| where AggregatedValue >= 90
```

## <a name="create-an-alert-rule"></a>경고 규칙 만들기
앞서 제공한 로그 검색 규칙 중 하나를 사용 하 여 Azure Monitor의 로그 경고를 만들려면 다음이 단계를 수행 합니다.  

>[!NOTE]
>컨테이너 리소스 사용률을 사용 하면 새 로그도 전환 해야에 대 한 경고 규칙을 만들려면 다음 절차에 설명 된 대로 API 경고 [로그 경고에 대 한 기본 설정 전환 API](../platform/alerts-log-api-switch.md)합니다.
>

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. 선택 **모니터** 왼쪽 창에서. 아래 **Insights**를 선택 **컨테이너**합니다.
3. 에 **클러스터 모니터링** 탭 목록에서 클러스터를 선택 합니다.
4. 아래 왼쪽 창에서 **모니터링**를 선택 **로그** Azure Monitor 로그 페이지를 엽니다. 이 페이지를 사용 하 여 작성 하 고 Azure Log Analytics 쿼리를 실행 합니다.
5. 에 **로그** 페이지에서 **+ 새로운 경고 규칙**합니다.
6. **조건** 섹션에서를 **때마다의 사용자 지정 로그 검색은 \<논리 정의 안 됨 >** 사전 사용자 지정 로그 상태를 정의 합니다. 합니다 **사용자 지정 로그 검색** 신호 유형은 직접 Azure Monitor 로그 페이지에서에서 경고 규칙을 만들고 있기 때문에 자동으로 선택 됩니다.  
7. 중 하나를 붙여 넣습니다를 [쿼리](#resource-utilization-log-search-queries) 이전에 제공 합니다 **검색 쿼리** 필드.
8. 다음과 같이 경고를 구성 합니다.

    1. **기준** 드롭다운 목록에서 **미터법**을 선택합니다. 메트릭 측정 값이 지정한 임계값 보다 값이 있는 쿼리에서 각 개체에 대 한 경고를 만듭니다.
    1. 에 대 한 **조건을**를 선택 **보다 큰**를 입력 하 고 **75** 는 초기 기준선으로 **임계값** CPU 및 메모리 사용률 경고에 대 한 . 부족 한 디스크 공간 경고에 대 한 입력 **90**합니다. 또는 조건을 충족 하는 다른 값을 입력 합니다.
    1. 에 **트리거 경고 기준** 섹션에서 **연속 위반**합니다. 드롭다운 목록에서 선택 **보다 큰**를 입력 하 고 **2**합니다.
    1. 아래에 있는 컨테이너 CPU 또는 메모리 사용률에 대 한 경고를 구성 하려면 **집계**를 선택 **ContainerName**합니다. 클러스터 노드 디스크 부족 경고를 구성 하려면 선택 **ClusterId**합니다.
    1. 에 **평가에 따라** 으로 설정 합니다 **기간** 값을 **60 분**합니다. 규칙은 5 분 마다 실행 되며 현재 시간에서 지난 1 시간 내에서 생성 된 레코드를 반환 합니다. 잠재적인 데이터 대기 시간에 대 한 와이드 창 계정에는 기간을 설정합니다. 또한 쿼리가 반환 하는 경고 하지 않습니다 발생 하는 거짓 부정 하지 않으려면 데이터는 확인 합니다.

9. 선택 **수행** 경고 규칙을 완료 합니다.
10. 에 이름을 입력 합니다 **경고 규칙 이름** 필드입니다. 지정 된 **설명을** 경고에 대 한 정보를 제공 하는 합니다. 및 제공 된 옵션에서 적절 한 심각도 수준을 선택 합니다.
11. 경고 규칙을 즉시 활성화 하려면에 대 한 기본값을 그대로 **활성화 규칙을 만들면**합니다.
12. 기존 선택 **작업 그룹** 하거나 새 그룹을 만듭니다. 이 단계를 수행 하면 동일한 작업에는 경고가 트리거될 때마다 수행 되는 합니다. 구성 하는 방법에 따라 IT 하거나 인시던트를 관리 하는 DevOps 운영 팀입니다.
13. 선택 **경고 규칙 만들기** 경고 규칙을 완료 합니다. 그 즉시 실행이 시작됩니다.

## <a name="next-steps"></a>다음 단계

- 뷰 [쿼리 예제 로그](container-insights-log-search.md#search-logs-to-analyze-data) 에 미리 정의 된 쿼리 및 평가 하거나 경고를 시각화, 분석 하 여 클러스터에 대 한 사용자 지정 하는 예제를 참조 하세요.
- Azure Monitor 및 AKS 클러스터의 다른 측면을 모니터링 하는 방법에 대 한 자세한 내용은 참조 하세요 [보기 Azure Kubernetes Service 상태](container-insights-analyze.md)합니다.
