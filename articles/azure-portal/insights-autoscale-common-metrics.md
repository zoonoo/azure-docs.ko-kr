<properties
	pageTitle="Azure Insights: Azure Insights 자동 크기 조정 공용 메트릭. | Microsoft Azure"
	description="클라우드 서비스, 가상 컴퓨터 및 웹앱의 자동 크기 조정에 일반적으로 사용되는 메트릭에 대해 알아봅니다."
	authors="kamathashwin"
	manager=""
	editor=""
	services="azure-portal"
	documentationCenter="na"/>

<tags
	ms.service="azure-portal"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/30/2016"
	ms.author="ashwink"/>

# Azure Insights 자동 크기 조정 공용 메트릭

Azure Insights 자동 크기 조정을 사용하여 원격 분석 데이터(메트릭)에 따라 실행 중인 인스턴트 수를 늘리거나 줄일 수 있습니다. 이 문서에서는 사용하고자 하는 공용 메트릭에 대해 설명합니다. 클라우드 서비스 및 서버 팜용 Azure 포털에서 크기를 조정할 리소스의 메트릭을 선택할 수 있습니다. 그러나 크기를 조정하기 위해 여러 리소스에서 임의 메트릭을 선택할 수도 있습니다.

다음은 크기를 조정하려는 매트릭을 찾고 나열하는 방법에 대한 세부 정보입니다. 가상 컴퓨터 규모 집합 크기 조정에도 다음이 적용됩니다.

## 메트릭 계산
기본적으로 Azure VM v2는 진단 확장이 구성되어 있으며 다음 메트릭이 켜져 있습니다.

- [Windows VM v2용 게스트 메트릭](#compute-metrics-for-windows-vm-v2-as-a-guest-os)
- [Linux VM v2용 게스트 메트릭](#compute-metrics-for-linux-vm-v2-as-a-guest-os)

`Get MetricDefinitions` API/PoSH/CLI를 사용하여 VMSS 리소스에 사용할 수 있는 메트릭을 볼 수 있습니다.

VM 규모 집합을 사용 중인데 특정 메트릭이 목록에 표시되지 않는 경우, 이는 진단 확장에서 *사용하지 않도록 설정*되었을 수 있습니다.

특정 메트릭이 원하는 빈도로 샘플링 또는 전송되고 있지 않은 경우 진단 구성을 업데이트할 수 있습니다.

위 경우 중 하나가 해당되면 PowerShell에 대한 [PowerShell을 사용하여 Windows를 실행하는 가상 컴퓨터에서 Azure 진단을 사용하도록 설정](../virtual-machines/virtual-machines-windows-ps-extensions-diagnostics.md)을 검토하여 메트릭을 사용하도록 Azure VM 진단 확장을 구성 및 업데이트합니다. 이 문서에는 샘플 진단 구성 파일도 포함되어 있습니다.

### 게스트 OS로 Windows VM v2용 메트릭 계산

Azure에서 새 VM(v2)을 만들 때 진단 확장을 사용하여 진단을 사용하도록 설정합니다.

PowerShell에서 다음 명령을 사용하여 메트릭 목록을 생성할 수 있습니다.


```
Get-AzureRmMetricDefinition -ResourceId <resource_id> | Format-Table -Property Name,Unit
```

다음 메트릭에 대한 경고를 만들 수 있습니다.

|메트릭 이름|	단위|
|---|---|
|\\Processor(\_Total)\\% Processor Time |백분율|
|\\Processor(\_Total)\\% Privileged Time |백분율|
|\\Processor(\_Total)\\% User Time |백분율|
|\\Processor Information(\_Total)\\Processor Frequency |개수|
|\\System\\Processes| 개수|
|\\Process(\_Total)\\Thread Count| 개수|
|\\Process(\_Total)\\Handle Count |개수|
|\\Memory\\% Committed Bytes In Use |백분율|
|\\Memory\\Available Bytes| Bytes|
|\\Memory\\Committed Bytes |Bytes|
|\\Memory\\Commit Limit| Bytes|
|\\Memory\\Pool Paged Bytes| Bytes|
|\\Memory\\Pool Nonpaged Bytes| Bytes|
|\\PhysicalDisk(\_Total)\\% Disk Time| 백분율|
|\\PhysicalDisk(\_Total)\\% Disk Read Time| 백분율|
|\\PhysicalDisk(\_Total)\\% Disk Write Time| 백분율|
|\\PhysicalDisk(\_Total)\\Disk Transfers/sec |초당 개수|
|\\PhysicalDisk(\_Total)\\Disk Reads/sec |초당 개수|
|\\PhysicalDisk(\_Total)\\Disk Writes/sec |초당 개수|
|\\PhysicalDisk(\_Total)\\Disk Bytes/sec |초당 바이트 수|
|\\PhysicalDisk(\_Total)\\Disk Read Bytes/sec| 초당 바이트 수|
|\\PhysicalDisk(\_Total)\\Disk Write Bytes/sec |초당 바이트 수|
|\\PhysicalDisk(\_Total)\\Avg. Disk Queue Length| 개수|
|\\PhysicalDisk(\_Total)\\Avg. Disk Read Queue Length| 개수|
|\\PhysicalDisk(\_Total)\\Avg. Disk Write Queue Length | 개수|
|\\PhysicalDisk(\_Total)\\%Free Space| 백분율|
|\\PhysicalDisk(\_Total)\\Free Megabytes| 개수|



### 게스트 OS로 Linux VM v2용 메트릭 계산

Azure에서 새 VM(v2)을 만들 때 진단 확장을 사용하여 기본적으로 진단을 사용하도록 설정합니다.

PowerShell에서 다음 명령을 사용하여 메트릭 목록을 생성할 수 있습니다.

```
Get-AzureRmMetricDefinition -ResourceId <resource_id> | Format-Table -Property Name,Unit
```

 다음 메트릭에 대한 경고를 만들 수 있습니다.

|메트릭 이름|	단위|
|---|---|
|\\Memory\\AvailableMemory |바이트|
|\\Memory\\PercentAvailableMemory|	백분율|
|\\Memory\\UsedMemory|	바이트|
|\\Memory\\PercentUsedMemory|	백분율|
|\\Memory\\PercentUsedByCache |백분율|
|\\Memory\\PagesPerSec|	초당 개수|
|\\Memory\\PagesReadPerSec|	초당 개수|
|\\Memory\\PagesWrittenPerSec |초당 개수|
|\\Memory\\AvailableSwap |바이트|
|\\Memory\\PercentAvailableSwap|	백분율|
|\\Memory\\UsedSwap |바이트|
|\\Memory\\PercentUsedSwap|	백분율|
|\\Processor\\PercentIdleTime|	백분율|
|\\Processor\\PercentUserTime|	백분율|
|\\Processor\\PercentNiceTime |백분율|
|\\Processor\\PercentPrivilegedTime |백분율|
|\\Processor\\PercentInterruptTime|	백분율|
|\\Processor\\PercentDPCTime|	백분율|
|\\Processor\\PercentProcessorTime |백분율|
|\\Processor\\PercentIOWaitTime |백분율|
|\\PhysicalDisk\\BytesPerSecond|	초당 바이트 수|
|\\PhysicalDisk\\ReadBytesPerSecond|	초당 바이트 수|
|\\PhysicalDisk\\WriteBytesPerSecond|	초당 바이트 수|
|\\PhysicalDisk\\TransfersPerSecond |초당 개수|
|\\PhysicalDisk\\ReadsPerSecond |초당 개수|
|\\PhysicalDisk\\WritesPerSecond |초당 개수|
|\\PhysicalDisk\\AverageReadTime|	초|
|\\PhysicalDisk\\AverageWriteTime |초|
|\\PhysicalDisk\\AverageTransferTime|	초|
|\\PhysicalDisk\\AverageDiskQueueLength|	개수|
|\\NetworkInterface\\BytesTransmitted |바이트|
|\\NetworkInterface\\BytesReceived |바이트|
|\\NetworkInterface\\PacketsTransmitted |개수|
|\\NetworkInterface\\PacketsReceived |개수|
|\\NetworkInterface\\BytesTotal |바이트|
|\\NetworkInterface\\TotalRxErrors|	개수|
|\\NetworkInterface\\TotalTxErrors|	개수|
|\\NetworkInterface\\TotalCollisions|	개수|




## 일반적으로 사용되는 웹(서버 팜) 메트릭

Http 큐 길이와 같이 공용 웹 서버 메트릭을 기반으로 자동 크기 조정을 수행할 수도 있습니다. 메트릭 이름은 **HttpQueueLength** 입니다. 다음 섹션에는 사용 가능한 서버 팜(웹앱) 메트릭이 나열되어 있습니다.

### 웹앱 메트릭

PowerShell에서 다음 명령을 사용하여 웹앱 메트릭 목록을 생성할 수 있습니다.

```
Get-AzureRmMetricDefinition -ResourceId <resource_id> | Format-Table -Property Name,Unit
```

다음 메트릭에 대한 경고를 만들거나 크기를 조정할 수 있습니다.

|메트릭 이름|	단위|
|---|---|
|CpuPercentage|	백분율|
|MemoryPercentage |백분율|
|DiskQueueLength|	개수|
|HttpQueueLength|	개수|
|BytesReceived|	바이트|
|BytesSent|	바이트|


## 일반적으로 사용되는 저장소 메트릭
저장소 큐의 메시지 수인 저장소 큐 길이의 크기를 조정할 수 있습니다. 저장소 큐 길이는 특수한 메트릭으로, 인스턴스당 메시지 수가 임계값으로 적용됩니다. 이는 인스턴스가 두 개이고 임계값이 100으로 설정된 경우 큐에서 총 메시지 수가 200일 때 크기가 조정됨을 의미합니다. 예를 들어 인스턴스당 메시지가 100개입니다.

Azure 포털의 **설정** 블레이드에서 이를 구성할 수 있습니다. VM 규모 집합의 경우 *metricName*을 *ApproximateMessageCount*로 사용하고 저장소 큐 ID를 *metricResourceUri*로 전달하도록 ARM 템플릿에서 자동 크기 조정 설정을 업데이트할 수 있습니다.


```
"metricName": "ApproximateMessageCount",
 "metricNamespace": "",
 "metricResourceUri": "/subscriptions/s1/resourceGroups/rg1/providers/Microsoft.ClassicStorage/storageAccounts/mystorage/services/queue/queues/mystoragequeue"
```

## 자주 사용되는 서비스 버스 메트릭

서비스 버스 큐의 메시지 수인 서비스 버스 큐 길이의 크기를 조정할 수 있습니다. 서비스 버스 큐 길이는 특수한 메트릭으로, 인스턴스당 메시지 수가 지정된 임계값으로 적용됩니다. 이는 인스턴스가 두 개이고 임계값이 100으로 설정된 경우 큐에서 총 메시지 수가 200일 때 크기가 조정됨을 의미합니다. 예를 들어 인스턴스당 메시지가 100개입니다.

VM 규모 집합의 경우 *metricName*을 *ApproximateMessageCount*로 사용하고 저장소 큐 ID를 *metricResourceUri*로 전달하도록 ARM 템플릿에서 자동 크기 조정 설정을 업데이트할 수 있습니다.

```
"metricName": "MessageCount",
 "metricNamespace": "",
"metricResourceUri": "/subscriptions/s1/resourceGroups/rg1/providers/Microsoft.ServiceBus/namespaces/mySB/queues/myqueue"
```

>[AZURE.NOTE] 서비스 버스의 경우 리소스 그룹 개념이 없지만 Azure Resource Manager가 지역마다 기본 리소스 그룹을 만듭니다. 리소스 그룹은 일반적으로 'Default-ServiceBus-[region]' 형식입니다. 예를 들어 'Default-ServiceBus-EastUS', 'Default-ServiceBus-WestUS', 'Default-ServiceBus-AustraliaEast' 등입니다.

<!---HONumber=AcomDC_0330_2016-->
