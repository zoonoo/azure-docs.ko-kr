---
title: Azure Service Fabric Mesh 앱에서 모니터링 및 진단
description: Azure의 Service Fabric Mesh에서 애플리케이션을 모니터링하고 진단하는 방법을 알아봅니다.
author: srrengar
ms.topic: conceptual
ms.date: 03/19/2019
ms.author: srrengar
ms.custom: mvc, devcenter, devx-track-azurecli
ms.openlocfilehash: d8859293b4853cbfa8c3b3dd0e7d1bfe4f75fc40
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107766172"
---
# <a name="monitoring-and-diagnostics"></a>모니터링 및 진단

> [!IMPORTANT]
> Azure Service Fabric Mesh의 미리 보기가 사용 중지되었습니다. 새 배포는 더이상 Service Fabric Mesh API를 통해 허용되지 않습니다. 기존 배포에 대한 지원은 2021년 4월 28일까지 계속됩니다.
> 
> 자세한 내용은 [Azure Service Fabric Mesh 미리 보기 사용 중지](https://azure.microsoft.com/updates/azure-service-fabric-mesh-preview-retirement/)를 참조하세요.

Azure Service Fabric Mesh는 개발자가 가상 머신, 스토리지 또는 네트워킹을 관리하지 않고 마이크로 서비스 애플리케이션을 배포할 수 있는 완전히 관리되는 서비스입니다. Service Fabric Mesh의 모니터링 및 진단은 다음 세 가지 주요 진단 데이터 유형으로 분류됩니다.

- 애플리케이션 로그 - 애플리케이션을 계측한 방법에 따라 컨테이너화된 애플리케이션의 로그로 정의됩니다(예: docker 로그).
- 플랫폼 이벤트 - 현재 컨테이너 활성화, 비활성화 및 종료를 포함하는 컨테이너 작업에 관련된 Mesh 플랫폼의 이벤트입니다.
- 컨테이너 메트릭 - 컨테이너에 대한 리소스 사용률 및 성능 메트릭입니다(docker 통계).

이 문서에서는 사용 가능한 최신 미리 보기 버전의 모니터링 및 진단 옵션을 설명합니다.

## <a name="application-logs"></a>애플리케이션 로그 전송 사용

배포된 컨테이너의 docker 로그를 컨테이너별로 볼 수 있습니다. Service Fabric Mesh 애플리케이션 모델에서 각 컨테이너는 애플리케이션의 코드 패키지입니다. 코드 패키지와 연결된 로그를 보려면 다음 명령을 사용합니다.

```azurecli
az mesh code-package-log get --resource-group <nameOfRG> --app-name <nameOfApp> --service-name <nameOfService> --replica-name <nameOfReplica> --code-package-name <nameOfCodePackage>
```

> [!NOTE]
> "az mesh service-replica" 명령을 사용하여 복제본 이름을 가져올 수 있습니다. 복제본 이름은 0부터 증가하는 정수입니다.

투표 애플리케이션에서 VotingWeb.Code 컨테이너의 로그를 확인하면 다음과 같이 표시됩니다.

```azurecli
az mesh code-package-log get --resource-group <nameOfRG> --application-name SbzVoting --service-name VotingWeb --replica-name 0 --code-package-name VotingWeb.Code
```

## <a name="container-metrics"></a>컨테이너 메트릭 

메시 환경에는 컨테이너의 성능을 나타내는 몇 가지 메트릭이 표시됩니다. Azure Portal 및 Azure Monitor CLI를 통해 다음 메트릭을 사용할 수 있습니다.

| 메트릭 | 설명 | 단위|
|----|----|----|
| CpuUtilization | ActualCpu/AllocatedCpu(%) | % |
| MemoryUtilization | ActualCpu/AllocatedMem(%) | % |
| AllocatedCpu | Azure Resource Manager 템플릿당 할당된 CPU | 밀리코어 |
| AllocatedMemory | Azure Resource Manager 템플릿당 할당된 메모리 | MB |
| ActualCpu | CPU 사용량 | 밀리코어 |
| ActualMemory | 메모리 사용량 | MB |
| ContainerStatus | 0 - 유효하지 않음: 컨테이너 상태를 알 수 없습니다. <br> 1 - 보류 중: 컨테이너가 시작되도록 예약되었습니다. <br> 2 - 시작 중: 컨테이너를 시작하는 중입니다. <br> 3 - 시작됨: 컨테이너를 시작했습니다. <br> 4 - 중지 중: 컨테이너를 중지하는 중입니다. <br> 5 - 중지됨: 컨테이너를 중지했습니다. | 해당 없음 |
| ApplicationStatus | 0 - 알 수 없음: 상태를 검색할 수 없습니다. <br> 1 - 준비: 애플리케이션이 실행 중입니다. <br> 2 - 업그레이드 중: 업그레이드가 진행 중입니다. <br> 3 - 만들기: 애플리케이션을 만드는 중입니다. <br> 4 - 삭제 중: 애플리케이션을 삭제하는 중입니다. <br> 5 - 실패: 애플리케이션을 배포하지 못했습니다. | 해당 없음 |
| ServiceStatus | 0 - 유효하지 않음: 현재 서비스에 상태 정보가 없습니다. <br> 1 - 정상: 서비스가 정상 상태입니다.  <br> 2 - 경고: 조사가 필요한 문제가 있는 것 같습니다. <br> 3 - 오류: 조사가 필요한 문제가 있습니다. <br> 4 - 알 수 없음: 상태를 검색할 수 없습니다. | 해당 없음 |
| ServiceReplicaStatus | 0 - 유효하지 않음: 현재 복제본에 상태 정보가 없습니다. <br> 1 - 정상: 서비스가 정상 상태입니다.  <br> 2 - 경고: 조사가 필요한 문제가 있는 것 같습니다. <br> 3 - 오류: 조사가 필요한 문제가 있습니다. <br> 4 - 알 수 없음: 상태를 검색할 수 없습니다. | 해당 없음 | 
| RestartCount | 컨테이너 다시 시작 수 | 해당 없음 |

> [!NOTE]
> ServiceStatus 및 ServiceReplicaStatus 값은 Service Fabric의 [HealthState](/dotnet/api/system.fabric.health.healthstate)와 동일합니다.

각 메트릭은 서로 다른 차원에서 사용할 수 있으므로 서로 다른 수준에서 집계를 볼 수 있습니다. 현재 차원 목록은 다음과 같습니다.

* ApplicationName
* ServiceName
* ServiceReplicaName
* CodePackageName

> [!NOTE]
> CodePackageName 차원은 Linux 애플리케이션에서 사용할 수 없습니다. 

각 차원은 [Service Fabric 애플리케이션 모델](service-fabric-mesh-service-fabric-resources.md#applications-and-services)의 다른 구성 요소에 해당합니다.

### <a name="azure-monitor-cli"></a>Azure Monitor CLI

전체 명령 목록은 [Azure Monitor CLI 설명서](/cli/azure/monitor/metrics#az_monitor_metrics_list)에서 사용할 수 있지만 아래에 몇 가지 유용한 예가 포함되어 있습니다. 

각 예에서 리소스 ID는 이 패턴을 따릅니다.

`"/subscriptions/<your sub ID>/resourcegroups/<your RG>/providers/Microsoft.ServiceFabricMesh/applications/<your App name>"`


* 애플리케이션의 컨테이너 CPU 사용률

```azurecli
    az monitor metrics list --resource <resourceId> --metric "CpuUtilization"
```
* 각 서비스 복제본의 메모리 사용률
```azurecli
    az monitor metrics list --resource <resourceId> --metric "MemoryUtilization" --dimension "ServiceReplicaName"
``` 

* 1시간 동안 각 컨테이너의 다시 시작 수 
```azurecli
    az monitor metrics list --resource <resourceId> --metric "RestartCount" --start-time 2019-02-01T00:00:00Z --end-time 2019-02-01T01:00:00Z
``` 

* 1시간 동안 "VotingWeb" 서비스 전반의 평균 CPU 사용률
```azurecli
    az monitor metrics list --resource <resourceId> --metric "CpuUtilization" --start-time 2019-02-01T00:00:00Z --end-time 2019-02-01T01:00:00Z --aggregation "Average" --filter "ServiceName eq 'VotingWeb'"
``` 

### <a name="metrics-explorer"></a>메트릭 탐색기

메트릭 탐색기는 메시 애플리케이션에 대한 모든 메트릭을 시각화할 수 있는 포털의 블레이드입니다. 이 블레이드는 포털 및 Azure Monitor 블레이드의 애플리케이션 페이지에서 액세스할 수 있으며, Azure Monitor 블레이드는 Azure Monitor를 지원하는 모든 Azure 리소스의 메트릭을 보는 데 사용할 수 있습니다. 

![메트릭 탐색기](./media/service-fabric-mesh-monitoring-diagnostics/metricsexplorer.png)


<!--
### Container Insights

In addition to the metrics explorer, we also have a dashboard available out of the box that shows sample metrics over time under the Insights blade in the application's page in the portal. 

![Container Insights](./media/service-fabric-mesh-monitoring-diagnostics/containerinsights.png)
-->

## <a name="next-steps"></a>다음 단계
* Service Fabric Mesh에 대한 자세한 내용은 [Service Fabric Mesh 개요](service-fabric-mesh-overview.md)를 참조하세요.
* Azure Monitor 메트릭 명령에 대한 자세한 내용은 [Azure Monitor CLI 설명서](/cli/azure/monitor/metrics#az_monitor_metrics_list)를 참조하세요.
