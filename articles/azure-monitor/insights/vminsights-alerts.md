---
title: VM에 대한 Azure 모니터의 경고
description: VM용 Azure Monitor에서 수집한 성능 데이터에서 경고 규칙을 만드는 방법에 대해 설명합니다.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/23/2020
ms.openlocfilehash: 987537d8497b3d8f2728941334d8328320ec6997
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80289603"
---
# <a name="how-to-create-alerts-from-azure-monitor-for-vms"></a>VM에 대 한 Azure 모니터에서 경고를 만드는 방법
[Azure Monitor의 경고는](../platform/alerts-overview.md) 모니터링 데이터의 흥미로운 데이터와 패턴을 사전에 알려줍니다. VM용 Azure Monitor에는 미리 구성된 경고 규칙이 포함되지 않지만 수집하는 데이터를 기반으로 직접 만들 수 있습니다. 이 문서에서는 샘플 쿼리 집합을 포함하여 경고 규칙을 만드는 방법에 대한 지침을 제공합니다.


## <a name="alert-rule-types"></a>경고 규칙 유형
Azure Monitor에는 경고를 만드는 데 사용되는 데이터에 따라 [다양한 유형의 경고 규칙이](../platform/alerts-overview.md#what-you-can-alert-on) 있습니다. VM에 대한 Azure 모니터에서 수집한 모든 데이터는 로그 경고를 지원하는 Azure 모니터 로그에 [저장됩니다.](../platform/alerts-log.md) 데이터가 Azure 모니터 [메트릭으로](../platform/alerts-log.md) 수집되지 않으므로 현재 VM용 Azure Monitor에서 수집된 성능 데이터와 메트릭 경고를 사용할 수 없습니다. 메트릭 경고에 대한 데이터를 수집하려면 Windows VM용 [진단 확장](../platform/diagnostics-extension-overview.md) 또는 Linux VM용 [Telegraf 에이전트를](../platform/collect-custom-metrics-linux-telegraf.md) 설치하여 성능 데이터를 메트릭으로 수집합니다.

Azure 모니터에는 두 가지 유형의 로그 경고가 있습니다.

- [결과 경고 수는](../platform/alerts-unified-log.md#number-of-results-alert-rules) 쿼리가 지정된 수의 레코드를 반환할 때 단일 경고를 만듭니다. 이는 [Log Analytics 에이전트가](../platform/log-analytics-agent.md) 수집한 Windows 및 Syslog 이벤트와 같은 비숫자 데이터 또는 여러 컴퓨터의 성능 추세를 분석하는 데 이상적입니다.
- [메트릭 측정 경고는](../platform/alerts-unified-log.md#metric-measurement-alert-rules) 경고 규칙에 정의된 임계값을 초과하는 값이 있는 쿼리의 각 레코드에 대해 별도의 경고를 만듭니다. 이러한 경고 규칙은 각 컴퓨터에 대한 개별 경고를 만들 수 있기 때문에 VM용 Azure Monitor에서 수집한 성능 데이터에 이상적입니다.


## <a name="alert-rule-walkthrough"></a>경고 규칙 연습
이 섹션에서는 VM용 Azure Monitor의 성능 데이터를 사용하여 메트릭 측정 경고 규칙을 만드는 데 대해 안내합니다. 다양한 로그 쿼리와 함께 이 기본 프로세스를 사용하여 다양한 성능 카운터에 대해 경고할 수 있습니다.

[Azure Monitor를 사용하여 로그 경고를 만들기, 보기 및 관리의](../platform/alerts-log.md)프로시저에 따라 새 경고 규칙을 만들어 시작합니다. **리소스의**경우 Azure 모니터 VM이 구독에서 사용하는 로그 분석 작업 영역을 선택합니다. 로그 경고 규칙의 대상 리소스는 항상 Log Analytics 작업 영역이므로 로그 쿼리에는 특정 가상 시스템 또는 가상 시스템 크기 집합에 대한 필터가 포함되어야 합니다. 

경고 규칙의 **조건에** 대 한 **검색 쿼리로** [아래 섹션에](#sample-alert-queries) 있는 쿼리 중 하나를 사용 합니다. 쿼리는 *AggregatedValue*라는 숫자 속성을 반환 해야 합니다. 임계값을 초과하는 각 가상 컴퓨터에 대해 별도의 경고를 만들 수 있도록 컴퓨터별로 데이터를 요약해야 합니다.

경고 **논리에서** **메트릭 측정을** 선택한 다음 **임계값을**제공합니다. **트리거 경고 를 기반으로 설정에서**경고를 만들려면 임계값을 초과해야 하는 횟수를 지정합니다. 예를 들어 프로세서가 임계값을 한 번 초과한 다음 정상으로 돌아오는 경우 는 신경 쓰지 않지만 여러 연속 측정에 대한 임계값을 계속 초과하는 경우 주의해야 합니다.

**평가** 섹션은 쿼리실행 빈도와 쿼리의 시간 창을 정의합니다. 아래 표시된 예제에서는 쿼리가 15분마다 실행되고 이전 15분 동안 수집된 성능 값을 평가합니다.


![메트릭 측정 경고 규칙](media/vminsights-alerts/metric-measurement-alert.png)

## <a name="sample-alert-queries"></a>샘플 경고 쿼리
다음 쿼리는 VM용 Azure Monitor에서 수집한 성능 데이터를 사용하여 메트릭 측정 경고 규칙과 함께 사용할 수 있습니다. 각 컴퓨터별로 데이터를 요약하여 임계값을 초과하는 값을 가진 각 컴퓨터에 대해 경고가 만들어집니다.

### <a name="cpu-utilization"></a>CPU 사용률

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms" 
| where Namespace == "Processor" and Name == "UtilizationPercentage" 
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), Computer, _ResourceId
```

### <a name="available-memory-in-mb"></a>MB에서 사용 가능한 메모리

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where Namespace == "Memory" and Name == "AvailableMB"
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), Computer, _ResourceId
```

### <a name="available-memory-in-percentage"></a>사용 가능한 메모리 백분율

```kusto
InsightsMetrics 
| where Origin == "vm.azm.ms" 
| where Namespace == "Memory" and Name == "AvailableMB" 
| extend TotalMemory = toreal(todynamic(Tags)["vm.azm.ms/memorySizeMB"])
| extend AvailableMemoryPercentage = (toreal(Val) / TotalMemory) * 100.0 
| summarize AggregatedValue = avg(AvailableMemoryPercentage) by bin(TimeGenerated, 15m), Computer, _ResourceId 
```

### <a name="logical-disk-used---all-disks-on-each-computer"></a>사용 된 논리 디스크 - 각 컴퓨터의 모든 디스크

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where Namespace == "LogicalDisk" and Name == "FreeSpacePercentage"
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), Computer, _ResourceId 
```

### <a name="logical-disk-used---individual-disks"></a>사용되는 논리 디스크 - 개별 디스크

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

### <a name="logical-disk-data-rate"></a>논리 디스크 데이터 속도

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms" 
| where Namespace == "LogicalDisk" and Name == "BytesPerSecond"
| extend Disk=tostring(todynamic(Tags)["vm.azm.ms/mountId"])
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m) , Computer, _ResourceId, Disk
```

### <a name="network-interfaces-bytes-received---all-interfaces"></a>네트워크 인터페이스 바이트 수신 - 모든 인터페이스

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where Namespace == "Network" and Name == "ReadBytesPerSecond"
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), Computer, _ResourceId 
```

### <a name="network-interfaces-bytes-received---individual-interfaces"></a>네트워크 인터페이스 바이트 수신 - 개별 인터페이스

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where Namespace == "Network" and Name == "ReadBytesPerSecond"
| extend NetworkInterface=tostring(todynamic(Tags)["vm.azm.ms/networkDeviceId"])
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), Computer, _ResourceId, NetworkInterface
```

### <a name="network-interfaces-bytes-sent---all-interfaces"></a>전송된 네트워크 인터페이스 바이트 - 모든 인터페이스

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where Namespace == "Network" and Name == "WriteBytesPerSecond"
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), Computer, _ResourceId
```

### <a name="network-interfaces-bytes-sent---individual-interfaces"></a>전송된 네트워크 인터페이스 바이트 - 개별 인터페이스

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where Namespace == "Network" and Name == "WriteBytesPerSecond"
| extend NetworkInterface=tostring(todynamic(Tags)["vm.azm.ms/networkDeviceId"])
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), Computer, _ResourceId, NetworkInterface
```

### <a name="virtual-machine-scale-set"></a>가상 머신 크기 집합
구독 ID, 리소스 그룹 및 가상 시스템 규모 집합 이름으로 수정합니다.

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where _ResourceId startswith "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/my-resource-group/providers/Microsoft.Compute/virtualMachineScaleSets/my-vm-scaleset" 
| where Namespace == "Processor" and Name == "UtilizationPercentage"
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), _ResourceId
```

### <a name="specific-virtual-machine"></a>특정 가상 머신
구독 ID, 리소스 그룹 및 VM 이름으로 수정합니다.

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where _ResourceId =~ "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/my-resource-group/providers/Microsoft.Compute/virtualMachines/my-vm" 
| where Namespace == "Processor" and Name == "UtilizationPercentage"
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m)
```

### <a name="cpu-utilization-for-all-compute-resources-in-a-subscription"></a>구독의 모든 계산 리소스에 대한 CPU 사용률
구독 ID로 수정합니다.

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where _ResourceId startswith "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx" and (_ResourceId contains "/providers/Microsoft.Compute/virtualMachines/" or _ResourceId contains "/providers/Microsoft.Compute/virtualMachineScaleSets/")
| where Namespace == "Processor" and Name == "UtilizationPercentage"
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), _ResourceId
```

### <a name="cpu-utilization-for-all-compute-resources-in-a-resource-group"></a>리소스 그룹의 모든 계산 리소스에 대한 CPU 사용률
구독 ID 및 리소스 그룹으로 수정합니다.

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where _ResourceId startswith "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/my-resource-group/providers/Microsoft.Compute/virtualMachines/"
or _ResourceId startswith "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/my-resource-group/providers/Microsoft.Compute/virtualMachineScaleSets/" 
| where Namespace == "Processor" and Name == "UtilizationPercentage"
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), _ResourceId

```

## <a name="next-steps"></a>다음 단계

- [Azure 모니터에서 경고에](../platform/alerts-overview.md)대해 자세히 알아봅니다.
- [VM에 대한 Azure 모니터의 데이터를 사용하여 로그 쿼리에 대해](vminsights-log-search.md)자세히 알아봅니다.
