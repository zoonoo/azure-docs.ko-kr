---
title: 컨테이너의 Azure Monitor에서 로그를 쿼리 하는 방법 | Microsoft Docs
description: 컨테이너에 대 한 Azure Monitor는 메트릭 및 로그 데이터를 수집 하 고이 문서에서는 레코드를 설명 하 고 샘플 쿼리를 포함 합니다.
ms.topic: conceptual
ms.date: 06/01/2020
ms.openlocfilehash: 08c42fab84cb5180497f8da4f077b9bd82283ad4
ms.sourcegitcommit: 1bf144dc5d7c496c4abeb95fc2f473cfa0bbed43
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2020
ms.locfileid: "95747682"
---
# <a name="how-to-query-logs-from-azure-monitor-for-containers"></a>컨테이너의 Azure Monitor에서 로그를 쿼리 하는 방법

컨테이너에 대 한 Azure Monitor는 컨테이너 호스트 및 컨테이너에서 성능 메트릭, 인벤토리 데이터 및 상태 정보를 수집 합니다. 데이터는 3 분 마다 수집 되 고 Azure Monitor의 Log Analytics 작업 영역으로 전달 됩니다. 이 데이터는 Azure Monitor에서 [쿼리에](../log-query/log-query-overview.md) 사용할 수 있습니다. 마이그레이션 계획, 용량 분석, 검색 및 주문형 성능 문제 해결을 포함하는 시나리오에 이 데이터를 적용할 수 있습니다.

## <a name="container-records"></a>컨테이너 레코드

다음 표에서는 컨테이너에 대 한 Azure Monitor에 의해 수집 된 레코드에 대 한 세부 정보를 제공 합니다. 열 설명 목록은 [ContainerInventory](/azure/azure-monitor/reference/tables/containerinventory) 및 [ContainerLog](/azure/azure-monitor/reference/tables/containerlog) 테이블에 대 한 참조를 참조 하세요.

| 데이터 | 데이터 원본 | 데이터 형식 | 필드 |
|------|-------------|-----------|--------|
| 컨테이너 인벤토리 | Kubelet | `ContainerInventory` | TimeGenerated, 컴퓨터, 컨테이너 이름, ContainerHostname, 이미지, ImageTag, ContainerState, ExitCode, EnvironmentVar, 명령, CreatedTime, StartedTime, FinishedTime, SourceSystem, ContainerID, ImageID |
| 컨테이너 로그 | Docker | `ContainerLog` | TimeGenerated, 컴퓨터, 이미지 ID, 컨테이너 이름, LogEntrySource, LogEntry, SourceSystem, ContainerID |
| 컨테이너 노드 인벤토리 | Kube API | `ContainerNodeInventory`| TimeGenerated, 컴퓨터, ClassName_s, DockerVersion_s, OperatingSystem_s, Volume_s, Network_s, NodeRole_s, OrchestratorType_s, InstanceID_g, SourceSystem|
| Kubernetes 클러스터의 Pod 인벤토리 | Kube API | `KubePodInventory` | TimeGenerated, 컴퓨터, ClusterId, ContainerCreationTimeStamp, PodUid, PodCreationTimeStamp, ContainerRestartCount, PodRestartCount, PodStartTime, ContainerStartTime, ServiceName, ControllerKind, ControllerName, 컨테이너 상태, ContainerStatusReason, ContainerID, ContainerName, Name, PodLabel, Namespace, PodStatus, ClusterName, Poduid, SourceSystem |
| Kubernetes 클러스터의 노드 부분 인벤토리 | Kube API | `KubeNodeInventory` | TimeGenerated, Computer, ClusterName, ClusterId, LastTransitionTimeReady, Labels, Status, KubeletVersion, KubeProxyVersion, CreationTimeStamp, SourceSystem | 
| Kubernetes 이벤트 | Kube API | `KubeEvents` | TimeGenerated, Computer, ClusterId_s, FirstSeen_t, LastSeen_t, Count_d, ObjectKind_s, Namespace_s, Name_s, Reason_s, Type_s, TimeGenerated_s, SourceComponent_s, ClusterName_s, Message,  SourceSystem | 
| Kubernetes 클러스터의 서비스 | Kube API | `KubeServices` | TimeGenerated, ServiceName_s, Namespace_s, SelectorLabels_s, ClusterId_s, ClusterName_s, ClusterIP_s, ServiceType_s, SourceSystem | 
| Kubernetes 클러스터의 노드 부분에 대한 성능 메트릭 | 사용 메트릭은 cAdvisor에서 가져오고 Kube api에서 제한 됩니다. | 성능 &#124; (ObjectName = = "K8SNode") | Computer, ObjectName, CounterName &#40;cpuAllocatableNanoCores, memoryAllocatableBytes, cpuCapacityNanoCores, memoryCapacityBytes, memoryRssBytes, cpuUsageNanoCores, memoryWorkingsetBytes, restartTimeEpoch&#41;, CounterValue, TimeGenerated, Countervalue, SourceSystem | 
| Kubernetes 클러스터의 컨테이너 부분에 대한 성능 메트릭 | 사용 메트릭은 cAdvisor에서 가져오고 Kube api에서 제한 됩니다. | 성능 &#124; (ObjectName = = "K8SContainer") | CounterName &#40; cpuRequestNanoCores, memoryRequestBytes, cpuLimitNanoCores, memoryWorkingSetBytes, restartTimeEpoch, cpuUsageNanoCores, memoryRssBytes&#41;, CounterValue, TimeGenerated, Countervalue, SourceSystem | 
| 사용자 지정 메트릭 ||`InsightsMetrics` | 컴퓨터, 이름, 네임 스페이스, 원본, SourceSystem, 태그<sup>1</sup>, Timegenerated, Type, Va, _ResourceId | 

<sup>1</sup> *Tags* 속성은 해당 메트릭에 대 한 [여러 차원을](../platform/data-platform-metrics.md#multi-dimensional-metrics) 나타냅니다. 테이블에 수집 및 저장 된 메트릭과 레코드 속성에 대 한 설명에 대 한 자세한 내용은 `InsightsMetrics` [InsightsMetrics 개요](https://github.com/microsoft/OMS-docker/blob/vishwa/june19agentrel/docs/InsightsMetrics.md)를 참조 하세요.

## <a name="search-logs-to-analyze-data"></a>로그를 검색하여 데이터 분석

Azure Monitor 로그는 추세를 찾고, 병목 상태를 진단 하거나, 예측 하거나, 현재 클러스터 구성이 최적 상태 인지 여부를 확인 하는 데 도움이 되는 데이터의 상관 관계를 확인 하는 데 도움이 됩니다. 미리 정의된 로그 검색을 즉시 사용하거나, 원하는 방식으로 정보를 반환하도록 사용자 지정할 수 있습니다.

**분석에서 보기** 드롭다운 목록의 미리 보기 창에서 **Kubernetes 이벤트 로그 보기** 또는 **컨테이너 로그 보기** 옵션을 선택 하 여 작업 영역에서 데이터에 대 한 대화형 분석을 수행할 수 있습니다. 사용자가 있던 Azure Portal 페이지의 오른쪽에 **로그 검색** 페이지가 나타납니다.

![Log Analytics에서 데이터 분석](./media/container-insights-analyze/container-health-log-search-example.png)

작업 영역에 전달 되는 컨테이너 로그 출력은 STDOUT 및 STDERR입니다. 컨테이너용 Azure Monitor는 AKS(Azure로 관리되는 Kubernetes)를 모니터링하고 이로 인해 생성되는 데이터 양이 많으므로 Kube 시스템은 오늘 수집되지 않습니다. 

### <a name="example-log-search-queries"></a>로그 검색 쿼리 예제

한두 가지 예제로 시작하는 쿼리를 작성한 다음, 요구 사항에 맞게 수정하는 것이 유용한 경우가 많습니다. 고급 쿼리를 작성하는 데 도움이 되도록 다음과 같은 샘플 쿼리를 테스트해 볼 수 있습니다.

| 쿼리 | Description | 
|-------|-------------|
| ContainerInventory<br> &#124; project Computer, Name, Image, ImageTag, ContainerState, CreatedTime, StartedTime, FinishedTime<br> &#124; render table | 컨테이너의 수명 주기 정보 모두 나열| 
| KubeEvents_CL<br> &#124; where not(isempty(Namespace_s))<br> &#124; sort by TimeGenerated desc<br> &#124; render table | kubernetes 이벤트|
| ContainerImageInventory<br> &#124; summarize AggregatedValue = count() by Image, ImageTag, Running | 이미지 인벤토리 | 
| **꺾은선형 차트 표시 옵션을 선택**:<br> Perf<br> &#124; where ObjectName == "K8SContainer" and CounterName == "cpuUsageNanoCores" &#124; summarize AvgCPUUsageNanoCores = avg(CounterValue) by bin(TimeGenerated, 30m), InstanceName | 컨테이너 CPU | 
| **꺾은선형 차트 표시 옵션을 선택**:<br> Perf<br> &#124; where ObjectName == "K8SContainer" and CounterName == "memoryRssBytes" &#124; summarize AvgUsedRssMemoryBytes = avg(CounterValue) by bin(TimeGenerated, 30m), InstanceName | 컨테이너 메모리 |
| InsightsMetrics<br> &#124; 이름 = = "requests_count"<br> TimeGenerated = bin (TimeGenerated, 1m)으로 &#124; 요약 Val = any (Val)<br> TimeGenerated asc 별 정렬 &#124;<br> &#124; project RequestsPerMinute = Val-prev (Val), TimeGenerated <br> &#124; 렌더링 막대 차트  | 사용자 지정 메트릭으로 분당 요청 수 |

## <a name="query-prometheus-metrics-data"></a>프로메테우스 메트릭 데이터 쿼리

다음 예제는 노드당 디스크 읽기 수/초를 보여 주는 프로메테우스 메트릭 쿼리입니다.

```
InsightsMetrics
| where Namespace == 'container.azm.ms/diskio'
| where TimeGenerated > ago(1h)
| where Name == 'reads'
| extend Tags = todynamic(Tags)
| extend HostName = tostring(Tags.hostName), Device = Tags.name
| extend NodeDisk = strcat(Device, "/", HostName)
| order by NodeDisk asc, TimeGenerated asc
| serialize
| extend PrevVal = iif(prev(NodeDisk) != NodeDisk, 0.0, prev(Val)), PrevTimeGenerated = iif(prev(NodeDisk) != NodeDisk, datetime(null), prev(TimeGenerated))
| where isnotnull(PrevTimeGenerated) and PrevTimeGenerated != TimeGenerated
| extend Rate = iif(PrevVal > Val, Val / (datetime_diff('Second', TimeGenerated, PrevTimeGenerated) * 1), iif(PrevVal == Val, 0.0, (Val - PrevVal) / (datetime_diff('Second', TimeGenerated, PrevTimeGenerated) * 1)))
| where isnotnull(Rate)
| project TimeGenerated, NodeDisk, Rate
| render timechart

```

스크랩으로 필터링 Azure Monitor는 프로메테우스 메트릭을 보려면 "프로메테우스"를 지정 합니다. 다음은 kubernetes 네임 스페이스에서 프로메테우스 메트릭을 보는 샘플 쿼리입니다 `default` .

```
InsightsMetrics 
| where Namespace == "prometheus"
| extend tags=parse_json(Tags)
| summarize count() by Name
```

또한 이름으로 프로메테우스 데이터를 직접 쿼리할 수 있습니다.

```
InsightsMetrics 
| where Namespace == "prometheus"
| where Name contains "some_prometheus_metric"
```

### <a name="query-config-or-scraping-errors"></a>쿼리 구성 또는 스크랩 오류

다음 예제 쿼리는 구성 또는 스크랩 오류를 조사 하기 위해 테이블의 정보 이벤트를 반환 합니다 `KubeMonAgentEvents` .

```
KubeMonAgentEvents | where Level != "Info" 
```

출력은 다음 예제와 유사한 결과를 표시 합니다.

![에이전트의 정보 이벤트에 대 한 쿼리 결과 기록](./media/container-insights-log-search/log-query-example-kubeagent-events.png)

## <a name="next-steps"></a>다음 단계

컨테이너의 Azure Monitor에는 미리 정의 된 경고 집합이 포함 되지 않습니다. [컨테이너에 대 한 Azure Monitor를 사용 하 여 성능 경고 만들기](./container-insights-log-alerts.md) 를 검토 하 여 devops 또는 운영 프로세스 및 절차를 지원 하기 위해 높은 CPU 및 메모리 사용률에 대해 권장 되는 경고를 만드는 방법을 알아봅니다.