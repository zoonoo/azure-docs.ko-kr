---
title: 컨테이너용 Azure Monitor를 사용하여 성능 경고 만들기 | Microsoft Docs
description: 이 문서에서는 컨테이너용 Azure Monitor에서 메모리 및 CPU 사용률에 대한 로그 쿼리를 기준으로 사용자 지정 Azure 경고를 만드는 방법을 설명합니다.
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
ms.date: 02/26/2019
ms.author: magoedte
ms.openlocfilehash: e6fdb0d57a44578647c1f16dc76c557296f20ddb
ms.sourcegitcommit: 24906eb0a6621dfa470cb052a800c4d4fae02787
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/27/2019
ms.locfileid: "56886779"
---
# <a name="how-to-set-up-alerts-for-performance-problems-in-azure-monitor-for-containers"></a>컨테이너에 대 한 Azure Monitor에서 성능 문제에 대 한 경고를 설정 하는 방법
컨테이너용 Azure Monitor는 AKS(Azure Kubernetes Service)에 호스트된 Azure Container Instances 또는 관리되는 Kubernetes 클러스터에 배포된 컨테이너 워크로드의 성능을 모니터링합니다. 

이 문서에서는 다음과 같은 경우에 대 한 경고를 사용 하도록 설정 하는 방법을 설명 합니다.

* 경우 클러스터의 노드에서 CPU 및 메모리 사용률 하거나 정의 된 임계값을 초과 합니다.
* CPU 또는 메모리 사용률 컨트롤러 내 컨테이너 중 하나에 해당 하는 리소스에 설정 된 한계를 비교 하 여 정의 된 임계값 초과 하는 경우.

클러스터 또는 컨트롤러에 대 한 높은 CPU 또는 메모리 사용률이 때 경고를 제공 하는 로그 쿼리를 기반으로 하는 메트릭 측정 경고 규칙을 만들 수 있습니다. 쿼리는 이제 연산자를 사용 하는 현재 날짜/시간을 비교 하 고 1 돌아갑니다. 컨테이너용 Azure Monitor에서 저장하는 모든 날짜는 UTC 형식입니다.

Azure Monitor의 경고를 잘 모르겠으면 시작하기 전에 [Microsoft Azure의 경고 개요](../platform/alerts-overview.md)를 참조하세요. 로그 쿼리를 사용하는 경고에 대한 자세한 내용은 [Azure Monitor의 로그 경고](../platform/alerts-unified-log.md)를 참조하세요.

## <a name="resource-utilization-log-search-queries"></a>리소스 사용률 로그 검색 쿼리
이 섹션의 쿼리는 각 경고 시나리오를 지원 하기 위해 제공 됩니다. 쿼리는 아래에서 7 단계에 필요 합니다 [경고 만들기](#create-alert-rule) 섹션 아래.  

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
>클러스터 이름과 컨트롤러 이름-에 대 한 자리 표시자 문자열 값을 포함 하는 아래 쿼리에서 <-클러스터-이름 > 및 < your 컨트롤러-이름 >. 경고를 설정 하기 전에 환경에 특정 값을 사용 하 여 자리 표시자를 대체 합니다. 


다음 쿼리는 컨테이너에 대 한 설정도의 백분율로 매분 컨트롤러의 모든 컨테이너 인스턴스의 CPU 사용률의 평균으로 컨트롤러의 모든 컨테이너의 평균 CPU 사용률을 계산 합니다.

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

다음 쿼리는 컨테이너에 대 한 설정도의 백분율로 매분 컨트롤러의 모든 컨테이너 인스턴스의 메모리 사용률의 평균으로 컨트롤러의 모든 컨테이너의 평균 메모리 사용률을 계산 합니다.

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

## <a name="create-alert-rule"></a>경고 규칙 만들기
이전에 제공 되는 로그 검색 규칙 중 하나를 사용 하 여 Azure Monitor의 로그 경고를 만들려면 다음 단계를 수행 합니다.  

>[!NOTE]
>다음 절차에 설명 된 대로 새 로그 경고 API로 전환 해야 [로그 경고에 대 한 기본 설정 전환 API](../platform/alerts-log-api-switch.md) 컨테이너 리소스 사용률에 대 한 경고 규칙을 만들려는 경우입니다. 
>

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. Azure Portal의 왼쪽 창에서 **모니터**를 선택합니다. **인사이트** 섹션에서 **컨테이너**를 선택합니다.    
3. **모니터링된 클러스터** 탭의 목록에서 클러스터 이름을 클릭하여 클러스터를 선택합니다.
4. **모니터링** 섹션 아래의 왼쪽 창에서 **로그**를 선택하여 Azure Monitor 로그 페이지를 엽니다. 이 페이지는 Azure Log Analytics 쿼리를 작성하고 실행하는 데 사용됩니다.
5. **로그** 페이지에서 **+ 새 경고 규칙**을 클릭합니다.
6. **조건** 섹션에서 미리 정의된 사용자 지정 로그 조건인 **사용자 지정 로그 검색이 <logic undefined>일 때마다**를 클릭합니다. Azure Monitor 로그 페이지에서 직접 경고 규칙을 만들기 시작했기 때문에 **사용자 지정 로그 검색** 신호 유형이 자동으로 선택됩니다.  
7. 중 하나를 붙여 넣습니다를 [쿼리](#resource-utilization-log-search-queries) 이전에 제공 합니다 **검색 쿼리** 필드. 

8. 다음 정보로 경고를 구성합니다.

    a. **기준** 드롭다운 목록에서 **미터법**을 선택합니다. 메트릭 측정값은 쿼리에서 지정된 임계값을 초과하는 값을 포함한 각 개체에 대해 경고를 만듭니다.  
    b. **조건**에서 **보다 큼**을 선택한 다음, 초기 기준선 **임계값**으로 **75**를 입력하거나 기준에 맞는 값을 입력합니다.  
    다. **경고 트리거 기준** 섹션에서 **연속 위반**을 선택한 다음, 드롭다운 목록에서 **보다 큼**을 선택하고 값 **2**를 입력합니다.  
    d. 아래에 있는 컨테이너 CPU 또는 메모리 사용률에 대 한 경고를 구성 하는 경우 **집계** 선택 **ContainerName** 드롭 다운 목록에서.  
    e. **평가 기준** 섹션에서 **기간 값**을 60분으로 수정합니다. 규칙이 5분마다 실행되고 최근 1시간 이내에 만들어진 레코드를 반환합니다. 기간을 더 넓은 기간으로 설정하면 잠재적인 데이터 대기 시간이 발생할 수 있으므로 경고가 발생하지 않는 거짓 부정을 방지하기 위해 쿼리에서 데이터를 반환하도록 합니다. 

9. **완료**를 클릭하여 경고 규칙을 완료합니다.
10. **경고 규칙 이름** 필드에 경고 이름을 입력합니다. 경고의 세부 사항을 자세히 설명하는 **설명**을 지정하고, 제공된 옵션에서 적절한 심각도를 선택합니다.
11. 생성 시 경고 규칙을 즉시 활성화하려면 **규칙을 만들면 바로 사용**에 기본값을 적용합니다.
12. 최종 단계에서는 경고가 트리거될 때마다 동일한 작업이 수행되고 정의한 각 규칙에 대해 사용할 수 있도록 기존 **작업 그룹**을 선택하거나 새 작업 그룹을 만듭니다. IT 또는 DevOps 작업이 인시던트를 관리하는 방식에 따라 구성합니다. 
13. **경고 규칙 만들기**를 클릭하여 경고 규칙을 완료합니다. 그 즉시 실행이 시작됩니다.

## <a name="next-steps"></a>다음 단계

* [로그 쿼리 예제](container-insights-analyze.md#search-logs-to-analyze-data)의 일부를 검토하여 다른 경고 시나리오에 대해 평가하고 사용하거나 사용자 지정할 수 있는 미리 정의된 쿼리 또는 예제를 알아봅니다. 
* Azure Monitor를 사용하고 AKS 클러스터의 다른 측면을 모니터링하는 방법을 계속 알아보려면 [Azure Kubernetes Service 상태 보기](container-insights-analyze.md)를 참조하세요.
