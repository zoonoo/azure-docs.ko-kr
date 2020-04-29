---
title: VM용 Azure Monitor의 경고
description: VM용 Azure Monitor 의해 수집 된 성능 데이터에서 경고 규칙을 만드는 방법을 설명 합니다.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/23/2020
ms.openlocfilehash: 987537d8497b3d8f2728941334d8328320ec6997
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "80289603"
---
# <a name="how-to-create-alerts-from-azure-monitor-for-vms"></a>VM용 Azure Monitor에서 경고를 만드는 방법
[Azure Monitor의 경고](../platform/alerts-overview.md) 는 모니터링 데이터에서 관심 있는 데이터 및 패턴을 사전에 알려 줍니다. VM용 Azure Monitor는 미리 구성 된 경고 규칙을 포함 하지 않지만 수집 된 데이터를 기반으로 직접 만들 수 있습니다. 이 문서에서는 샘플 쿼리 집합을 포함 하 여 경고 규칙을 만드는 방법에 대 한 지침을 제공 합니다.


## <a name="alert-rule-types"></a>경고 규칙 유형
Azure Monitor에는 경고를 생성 하는 데 사용 되는 데이터를 기반으로 하는 [다양 한 유형의 경고 규칙이](../platform/alerts-overview.md#what-you-can-alert-on) 있습니다. VM용 Azure Monitor에서 수집 된 모든 데이터는 [로그 경고](../platform/alerts-log.md)를 지 원하는 Azure Monitor 로그에 저장 됩니다. 데이터가 Azure Monitor 메트릭에 수집 되지 않기 때문에 현재 VM용 Azure Monitor에서 수집 된 성능 데이터와 함께 [메트릭 경고](../platform/alerts-log.md) 를 사용할 수 없습니다. 메트릭 경고에 대 한 데이터를 수집 하려면 Windows Vm 용 [진단 확장](../platform/diagnostics-extension-overview.md) 또는 Linux Vm 용 [Telegraf agent](../platform/collect-custom-metrics-linux-telegraf.md) 를 설치 하 여 성능 데이터를 메트릭에 수집 합니다.

Azure Monitor에서 로그 경고에는 두 가지 유형이 있습니다.

- [결과 경고 수](../platform/alerts-unified-log.md#number-of-results-alert-rules) 쿼리가 적어도 지정 된 수의 레코드를 반환할 때 단일 경고를 생성 합니다. 이러한 데이터는 [Log Analytics 에이전트가](../platform/log-analytics-agent.md) 수집 하는 Windows 및 Syslog 이벤트와 같은 숫자가 아닌 데이터 또는 여러 컴퓨터에서 성능 추세를 분석 하는 데 적합 합니다.
- [메트릭 단위 경고](../platform/alerts-unified-log.md#metric-measurement-alert-rules) 는 경고 규칙에 정의 된 임계값을 초과 하는 값이 있는 쿼리의 각 레코드에 대해 별도의 경고를 만듭니다. 이러한 경고 규칙은 각 컴퓨터에 대 한 개별 경고를 만들 수 있으므로 VM용 Azure Monitor에 의해 수집 되는 성능 데이터에 적합 합니다.


## <a name="alert-rule-walkthrough"></a>경고 규칙 연습
이 섹션에서는 VM용 Azure Monitor의 성능 데이터를 사용 하 여 메트릭 측정 경고 규칙을 만드는 과정을 안내 합니다. 다양 한 성능 카운터에 대 한 경고를 위해 다양 한 로그 쿼리에서이 기본 프로세스를 사용할 수 있습니다.

먼저 [Azure Monitor를 사용 하 여 로그 경고 만들기, 보기 및 관리](../platform/alerts-log.md)의 절차에 따라 새 경고 규칙을 만듭니다. **리소스**의 경우 구독에서 Azure Monitor vm이 사용 하는 Log Analytics 작업 영역을 선택 합니다. 로그 경고 규칙에 대 한 대상 리소스는 항상 Log Analytics 작업 영역 이므로 로그 쿼리는 특정 가상 머신 또는 가상 머신 확장 집합에 대 한 필터를 포함 해야 합니다. 

경고 규칙의 **조건** 에 대해 [아래 섹션](#sample-alert-queries) 의 쿼리 중 하나를 **검색 쿼리로**사용 합니다. 이 쿼리는 *AggregatedValue*라는 숫자 속성을 반환 해야 합니다. 임계값을 초과 하는 각 가상 컴퓨터에 대해 별도의 경고를 만들 수 있도록 컴퓨터 별로 데이터를 요약 해야 합니다.

**경고 논리**에서 **메트릭 측정** 을 선택 하 고 **임계값**을 입력 합니다. **경고에 기반**하 여 경고를 생성 하기 전에 임계값을 초과 해야 하는 횟수를 지정 합니다. 예를 들어 프로세서가 임계값을 한 번 초과 하 고 정상으로 반환 하는 경우에는 문제가 되지 않을 수 있지만 연속 하는 여러 측정에서 임계값을 초과 하는 경우 주의 해야 합니다.

**계산 된 기반** 섹션에서는 쿼리를 실행 하는 빈도와 쿼리의 시간 창을 정의 합니다. 아래에 표시 된 예제에서 쿼리는 15 분 마다 실행 되 고 이전 15 분 동안 수집 된 성능 값을 평가 합니다.


![메트릭 측정 경고 규칙](media/vminsights-alerts/metric-measurement-alert.png)

## <a name="sample-alert-queries"></a>샘플 경고 쿼리
다음 쿼리는 VM용 Azure Monitor에 의해 수집 된 성능 데이터를 사용 하는 메트릭 측정 경고 규칙과 함께 사용할 수 있습니다. 각 컴퓨터에 대해 데이터를 요약 하 여 임계값을 초과 하는 값이 있는 각 컴퓨터에 대해 경고가 생성 됩니다.

### <a name="cpu-utilization"></a>CPU 사용률

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms" 
| where Namespace == "Processor" and Name == "UtilizationPercentage" 
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), Computer, _ResourceId
```

### <a name="available-memory-in-mb"></a>사용 가능한 메모리 (MB)

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where Namespace == "Memory" and Name == "AvailableMB"
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), Computer, _ResourceId
```

### <a name="available-memory-in-percentage"></a>사용 가능한 메모리 (%)

```kusto
InsightsMetrics 
| where Origin == "vm.azm.ms" 
| where Namespace == "Memory" and Name == "AvailableMB" 
| extend TotalMemory = toreal(todynamic(Tags)["vm.azm.ms/memorySizeMB"])
| extend AvailableMemoryPercentage = (toreal(Val) / TotalMemory) * 100.0 
| summarize AggregatedValue = avg(AvailableMemoryPercentage) by bin(TimeGenerated, 15m), Computer, _ResourceId 
```

### <a name="logical-disk-used---all-disks-on-each-computer"></a>사용 되는 논리 디스크-각 컴퓨터의 모든 디스크

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where Namespace == "LogicalDisk" and Name == "FreeSpacePercentage"
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), Computer, _ResourceId 
```

### <a name="logical-disk-used---individual-disks"></a>논리 디스크 사용-개별 디스크

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where Namespace == "LogicalDisk" and Name == "FreeSpacePercentage"
| extend Disk=tostring(todynamic(Tags)["vm.azm.ms/mountId"])
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), Computer, _ResourceId, Disk
```

### <a name="logical-disk-iops"></a>논리 디스크 IOPS

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms" 
| where Namespace == "LogicalDisk" and Name == "TransfersPerSecond"
| extend Disk=tostring(todynamic(Tags)["vm.azm.ms/mountId"])
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m) ), Computer, _ResourceId, Disk
```

### <a name="logical-disk-data-rate"></a>논리 디스크 데이터 전송률

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms" 
| where Namespace == "LogicalDisk" and Name == "BytesPerSecond"
| extend Disk=tostring(todynamic(Tags)["vm.azm.ms/mountId"])
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m) , Computer, _ResourceId, Disk
```

### <a name="network-interfaces-bytes-received---all-interfaces"></a>네트워크 인터페이스 수신 바이트-모든 인터페이스

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where Namespace == "Network" and Name == "ReadBytesPerSecond"
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), Computer, _ResourceId 
```

### <a name="network-interfaces-bytes-received---individual-interfaces"></a>네트워크 인터페이스 수신 바이트-개별 인터페이스

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where Namespace == "Network" and Name == "ReadBytesPerSecond"
| extend NetworkInterface=tostring(todynamic(Tags)["vm.azm.ms/networkDeviceId"])
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), Computer, _ResourceId, NetworkInterface
```

### <a name="network-interfaces-bytes-sent---all-interfaces"></a>네트워크 인터페이스 전송 바이트-모든 인터페이스

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where Namespace == "Network" and Name == "WriteBytesPerSecond"
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), Computer, _ResourceId
```

### <a name="network-interfaces-bytes-sent---individual-interfaces"></a>네트워크 인터페이스 전송 바이트-개별 인터페이스

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where Namespace == "Network" and Name == "WriteBytesPerSecond"
| extend NetworkInterface=tostring(todynamic(Tags)["vm.azm.ms/networkDeviceId"])
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), Computer, _ResourceId, NetworkInterface
```

### <a name="virtual-machine-scale-set"></a>가상 머신 크기 집합
구독 ID, 리소스 그룹 및 가상 머신 확장 집합 이름으로을 수정 합니다.

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where _ResourceId startswith "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/my-resource-group/providers/Microsoft.Compute/virtualMachineScaleSets/my-vm-scaleset" 
| where Namespace == "Processor" and Name == "UtilizationPercentage"
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), _ResourceId
```

### <a name="specific-virtual-machine"></a>특정 가상 컴퓨터
구독 ID, 리소스 그룹 및 VM 이름으로을 수정 합니다.

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where _ResourceId =~ "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/my-resource-group/providers/Microsoft.Compute/virtualMachines/my-vm" 
| where Namespace == "Processor" and Name == "UtilizationPercentage"
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m)
```

### <a name="cpu-utilization-for-all-compute-resources-in-a-subscription"></a>구독의 모든 계산 리소스에 대 한 CPU 사용률
구독 ID를 사용 하 여을 수정 합니다.

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where _ResourceId startswith "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx" and (_ResourceId contains "/providers/Microsoft.Compute/virtualMachines/" or _ResourceId contains "/providers/Microsoft.Compute/virtualMachineScaleSets/")
| where Namespace == "Processor" and Name == "UtilizationPercentage"
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), _ResourceId
```

### <a name="cpu-utilization-for-all-compute-resources-in-a-resource-group"></a>리소스 그룹의 모든 계산 리소스에 대 한 CPU 사용률
구독 ID 및 리소스 그룹을 사용 하 여을 수정 합니다.

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where _ResourceId startswith "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/my-resource-group/providers/Microsoft.Compute/virtualMachines/"
or _ResourceId startswith "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/my-resource-group/providers/Microsoft.Compute/virtualMachineScaleSets/" 
| where Namespace == "Processor" and Name == "UtilizationPercentage"
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), _ResourceId

```

## <a name="next-steps"></a>다음 단계

- [Azure Monitor의 경고](../platform/alerts-overview.md)에 대해 자세히 알아보세요.
- [VM용 Azure Monitor의 데이터를 사용 하 여 로그 쿼리에](vminsights-log-search.md)대해 자세히 알아보세요.
