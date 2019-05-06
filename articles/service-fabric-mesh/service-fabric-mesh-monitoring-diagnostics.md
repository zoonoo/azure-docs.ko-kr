---
title: Azure Service Fabric Mesh 애플리케이션의 모니터링 및 진단 | Microsoft Docs
description: Azure의 Service Fabric Mesh에서 애플리케이션을 모니터링하고 진단하는 방법을 알아봅니다.
services: service-fabric-mesh
documentationcenter: .net
author: srrengar
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric-mesh
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/19/2019
ms.author: srrengar
ms.custom: mvc, devcenter
ms.openlocfilehash: 36c9a5d75c4a72365638619ab85d451df647feb3
ms.sourcegitcommit: c53a800d6c2e5baad800c1247dce94bdbf2ad324
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/30/2019
ms.locfileid: "64939816"
---
# <a name="monitoring-and-diagnostics"></a>모니터링 및 진단
Azure Service Fabric Mesh는 개발자가 가상 머신, 저장소 또는 네트워킹을 관리하지 않고 마이크로 서비스 애플리케이션을 배포할 수 있는 완전히 관리되는 서비스입니다. Service Fabric Mesh의 모니터링 및 진단은 다음 세 가지 주요 진단 데이터 유형으로 분류됩니다.

- 애플리케이션 로그 - 애플리케이션을 계측한 방법에 따라 컨테이너화된 애플리케이션의 로그로 정의됩니다(예: docker 로그).
- 플랫폼 이벤트 - 현재 컨테이너 활성화, 비활성화 및 종료를 포함하는 컨테이너 작업에 관련된 Mesh 플랫폼의 이벤트입니다.
- 컨테이너 메트릭 - 컨테이너에 대한 리소스 사용률 및 성능 메트릭입니다(docker 통계).

이 문서에서는 사용 가능한 최신 미리 보기 버전의 모니터링 및 진단 옵션을 설명합니다.

## <a name="application-logs"></a>애플리케이션 로그

배포된 컨테이너의 docker 로그를 컨테이너별로 볼 수 있습니다. Service Fabric Mesh 애플리케이션 모델에서 각 컨테이너는 애플리케이션의 코드 패키지입니다. 코드 패키지와 연결된 로그를 보려면 다음 명령을 사용합니다.

```cli
az mesh code-package-log get --resource-group <nameOfRG> --app-name <nameOfApp> --service-name <nameOfService> --replica-name <nameOfReplica> --code-package-name <nameOfCodePackage>
```

> [!NOTE]
> "az mesh service-replica" 명령을 사용하여 복제본 이름을 가져올 수 있습니다. 복제본 이름 정수 0부터 증가 됩니다.

투표 애플리케이션에서 VotingWeb.Code 컨테이너의 로그를 확인하면 다음과 같이 표시됩니다.

```cli
az mesh code-package-log get --resource-group <nameOfRG> --application-name SbzVoting --service-name VotingWeb --replica-name 0 --code-package-name VotingWeb.Code
```

## <a name="container-metrics"></a>컨테이너 메트릭 

메시에 서 환경을 많은 컨테이너를 수행 하는 방법을 나타내는 메트릭 노출 합니다. 다음 메트릭은 Azure를 통해 사용할 수 있는 포털 및 Azure CLI를 모니터링 합니다.

| 메트릭 | 설명 | Units|
|----|----|----|
| CpuUtilization | ActualCpu/AllocatedCpu 백분율 | % |
| MemoryUtilization | ActualMem/AllocatedMem 백분율 | % |
| AllocatedCpu | Azure Resource Manager 템플릿을 따라 할당 된 Cpu | (밀리 코어) |
| AllocatedMemory | Azure Resource Manager 템플릿을 따라 할당 된 메모리 | MB |
| ActualCpu | CPU 사용량 | (밀리 코어) |
| ActualMemory | 메모리 사용량 | MB |
| ContainerStatus | 0-잘못 되었습니다. 컨테이너 상태를 알 수 없습니다 <br> 1-보류 중: 컨테이너에 시작 하도록 예약 <br> 2-시작: 컨테이너를 시작 하 고 <br> 3-시작: 컨테이너가는 성공적으로 시작 <br> 4-중지 합니다. 컨테이너를 중지 하는 중 <br> 5-중지 되었습니다. 컨테이너를 중지 했습니다. | N/A |
| ApplicationStatus | 0-알 수 없음: 상태를 가져올 수 없습니다. <br> 1-준비: 응용 프로그램은 성공적으로 실행 <br> 2-미리 보기 버전: 진행 중인 업그레이드가 있는 <br> 3-만드는 중: 응용 프로그램이 만들어지고 <br> 4-삭제: 응용 프로그램을 삭제 하는 중 <br> 5-실패 했습니다. 응용 프로그램 배포 하지 못했습니다. | N/A |
| ServiceStatus | 0-잘못 되었습니다. 서비스는 상태를 현재 되어 있지 않은 <br> 1-확인: 서비스가 정상  <br> 2-경고: 조사가 필요한 문제가 있을 수 있습니다. <br> 3-오류: 조사를 해야 하는 잘못 된 부분이 있는지 <br> 4-알 수 없음: 상태를 가져올 수 없습니다. | N/A |
| ServiceReplicaStatus | 0-잘못 되었습니다. 현재 복제본 상태를 갖지 않습니다. <br> 1-확인: 서비스가 정상  <br> 2-경고: 조사가 필요한 문제가 있을 수 있습니다. <br> 3-오류: 조사를 해야 하는 잘못 된 부분이 있는지 <br> 4-알 수 없음: 상태를 가져올 수 없습니다. | N/A | 
| RestartCount | 컨테이너 다시 시작 횟수 | N/A |

> [!NOTE]
> ServiceStatus 및 ServiceReplicaStatus 값은 동일 합니다 [HealthState](https://docs.microsoft.com/dotnet/api/system.fabric.health.healthstate?view=azure-dotnet) Service Fabric에서. 

각 메트릭에 여러 수준에서 집계를 볼 수 있도록 다른 차원을 제공 됩니다. 차원의 현재 목록 아래와 같습니다.

* ApplicationName
* ServiceName
* ServiceReplicaName
* CodePackageName

> [!NOTE]
> CodePackageName 차원 Linux 응용 프로그램에 대해 사용할 수 없는 경우 

여러 구성 요소에 해당 하는 각 차원의 [Service Fabric 응용 프로그램 모델](service-fabric-mesh-service-fabric-resources.md#applications-and-services)

### <a name="azure-monitor-cli"></a>Azure Monitor CLI

명령의 전체 목록을의 수를 [Azure Monitor CLI docs](https://docs.microsoft.com/cli/azure/monitor/metrics?view=azure-cli-latest#az-monitor-metrics-list) 있지만 아래 몇 가지 유용한 예가 포함 되어 있습니다 

리소스 ID를 각 예제에서는이 패턴을 따릅니다.

`"/subscriptions/<your sub ID>/resourcegroups/<your RG>/providers/Microsoft.ServiceFabricMesh/applications/<your App name>"`


* 응용 프로그램에서 컨테이너의 CPU 사용률

```cli
    az monitor metrics list --resource <resourceId> --metric "CpuUtilization"
```
* 각 서비스 복제본에 대 한 메모리 사용률
```cli
    az monitor metrics list --resource <resourceId> --metric "MemoryUtilization" --dimension "ServiceReplicaName"
``` 

* 1 시간 창에서 각 컨테이너에 대 한 다시 시작 
```cli
    az monitor metrics list --resource <resourceId> --metric "RestartCount" --start-time 2019-02-01T00:00:00Z --end-time 2019-02-01T01:00:00Z
``` 

* 서비스 전반에 걸쳐 평균 CPU 사용률이 1 시간 창의 "VotingWeb" 라는
```cli
    az monitor metrics list --resource <resourceId> --metric "CpuUtilization" --start-time 2019-02-01T00:00:00Z --end-time 2019-02-01T01:00:00Z --aggregation "Average" --filter "ServiceName eq 'VotingWeb'"
``` 

### <a name="metrics-explorer"></a>메트릭 탐색기

메트릭 탐색기는 망상 조직 응용 프로그램에 대 한 모든 메트릭을 시각화할 수는 포털에서 블레이드입니다. 이 블레이드는 포털 및 Azure monitor 블레이드 후자는 Azure Monitor를 지 원하는 모든 Azure 리소스에 대 한 메트릭을 보는 데 사용할 응용 프로그램의 페이지에 액세스할 수 있습니다. 

![메트릭 탐색기](./media/service-fabric-mesh-monitoring-diagnostics/metricsexplorer.png)


<!--
### Container Insights

In addition to the metrics explorer, we also have a dashboard available out of the box that shows sample metrics over time under the Insights blade in the application's page in the portal. 

![Container Insights](./media/service-fabric-mesh-monitoring-diagnostics/containerinsights.png)
-->

## <a name="next-steps"></a>다음 단계
* Service Fabric Mesh에 대한 자세한 내용은 [Service Fabric Mesh 개요](service-fabric-mesh-overview.md)를 참조하세요.
* Azure Monitor 메트릭 명령에 대 한 자세한 내용은 체크 아웃 합니다 [Azure Monitor CLI docs](https://docs.microsoft.com/cli/azure/monitor/metrics?view=azure-cli-latest#az-monitor-metrics-list)합니다.
