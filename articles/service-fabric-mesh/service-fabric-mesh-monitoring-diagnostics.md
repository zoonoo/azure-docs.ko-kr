---
title: Azure 서비스 패브릭 메시 앱의 모니터링 및 진단
description: Azure의 Service Fabric Mesh에서 애플리케이션을 모니터링하고 진단하는 방법을 알아봅니다.
author: srrengar
ms.topic: conceptual
ms.date: 03/19/2019
ms.author: srrengar
ms.custom: mvc, devcenter
ms.openlocfilehash: 247a1de4d00668371337295616d31caf101f0cc5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75498153"
---
# <a name="monitoring-and-diagnostics"></a>모니터링 및 진단
Azure Service Fabric Mesh는 개발자가 가상 머신, 스토리지 또는 네트워킹을 관리하지 않고 마이크로 서비스 애플리케이션을 배포할 수 있는 완전히 관리되는 서비스입니다. Service Fabric Mesh의 모니터링 및 진단은 다음 세 가지 주요 진단 데이터 유형으로 분류됩니다.

- 애플리케이션 로그 - 애플리케이션을 계측한 방법에 따라 컨테이너화된 애플리케이션의 로그로 정의됩니다(예: docker 로그).
- 플랫폼 이벤트 - 현재 컨테이너 활성화, 비활성화 및 종료를 포함하는 컨테이너 작업에 관련된 Mesh 플랫폼의 이벤트입니다.
- 컨테이너 메트릭 - 컨테이너에 대한 리소스 사용률 및 성능 메트릭입니다(docker 통계).

이 문서에서는 사용 가능한 최신 미리 보기 버전의 모니터링 및 진단 옵션을 설명합니다.

## <a name="application-logs"></a>애플리케이션 로그 전송 사용

배포된 컨테이너의 docker 로그를 컨테이너별로 볼 수 있습니다. Service Fabric Mesh 애플리케이션 모델에서 각 컨테이너는 애플리케이션의 코드 패키지입니다. 코드 패키지와 연결된 로그를 보려면 다음 명령을 사용합니다.

```cli
az mesh code-package-log get --resource-group <nameOfRG> --app-name <nameOfApp> --service-name <nameOfService> --replica-name <nameOfReplica> --code-package-name <nameOfCodePackage>
```

> [!NOTE]
> "az mesh service-replica" 명령을 사용하여 복제본 이름을 가져올 수 있습니다. 복제본 이름은 0에서 정수증가입니다.

투표 애플리케이션에서 VotingWeb.Code 컨테이너의 로그를 확인하면 다음과 같이 표시됩니다.

```cli
az mesh code-package-log get --resource-group <nameOfRG> --application-name SbzVoting --service-name VotingWeb --replica-name 0 --code-package-name VotingWeb.Code
```

## <a name="container-metrics"></a>컨테이너 메트릭 

Mesh 환경은 컨테이너의 성능을 나타내는 몇 가지 메트릭을 노출합니다. 다음 메트릭은 Azure 포털 및 Azure 모니터 CLI를 통해 사용할 수 있습니다.

| 메트릭 | 설명 | Units|
|----|----|----|
| CPU 사용률 | 실제Cpu/할당된Cpu백분율 | % |
| 메모리 사용률 | 실제 Mem/할당된 밈백분율 | % |
| 할당된 Cpu | Azure 리소스 관리자 템플릿에 따라 할당된 CPU | 밀리코어스 |
| 할당된 메모리 | Azure 리소스 관리자 템플릿에 따라 할당된 메모리 | MB |
| 실제 Cpu | CPU 사용량 | 밀리코어스 |
| 실제 메모리 | 메모리 사용량 | MB |
| 컨테이너 상태 | 0 - 유효하지 않음: 컨테이너 상태를 알 수 없음 <br> 1 - 보류 중: 컨테이너가 시작하도록 예약되었습니다. <br> 2 - 시작 : 컨테이너가 시작 중입니다. <br> 3 - 시작: 컨테이너가 성공적으로 시작되었습니다. <br> 4 - 중지: 컨테이너가 중지 중입니다. <br> 5 - 중지됨: 컨테이너가 성공적으로 중지되었습니다. | 해당 없음 |
| 응용 프로그램 상태 | 0 - 알 수 없음: 상태를 검색할 수 없습니다. <br> 1 - 준비: 응용 프로그램이 성공적으로 실행되고 있습니다. <br> 2 - 업그레이드: 업그레이드 진행 중입니다. <br> 3 - 만들기: 응용 프로그램이 생성되고 있습니다. <br> 4 - 삭제: 응용 프로그램이 삭제되고 있습니다. <br> 5 - 실패: 응용 프로그램을 배포하지 못했습니다. | 해당 없음 |
| ServiceStatus | 0 - 유효하지 않음: 서비스에 현재 상태 상태가 없습니다. <br> 1 - 확인 : 서비스가 건강합니다.  <br> 2 - 경고: 조사가 필요한 잘못된 것이 있을 수 있습니다. <br> 3 - 오류 : 조사가 필요한 문제가 있습니다. <br> 4 - 알 수 없음: 상태를 검색할 수 없습니다. | 해당 없음 |
| 서비스 복제 상태 | 0 - 유효하지 않음: 복제본에 현재 상태 상태가 없습니다. <br> 1 - 확인 : 서비스가 건강합니다.  <br> 2 - 경고: 조사가 필요한 잘못된 것이 있을 수 있습니다. <br> 3 - 오류 : 조사가 필요한 문제가 있습니다. <br> 4 - 알 수 없음: 상태를 검색할 수 없습니다. | 해당 없음 | 
| 다시 시작 카운트 | 컨테이너 다시 시작 횟수 | 해당 없음 |

> [!NOTE]
> 서비스 상태 및 서비스복제 상태 값은 서비스 패브릭의 [상태 상태와](https://docs.microsoft.com/dotnet/api/system.fabric.health.healthstate?view=azure-dotnet) 동일합니다. 

각 측정항목은 다양한 측정기준에서 사용할 수 있으므로 다양한 수준에서 집계를 볼 수 있습니다. 현재 차원 목록은 다음과 같습니다.

* ApplicationName
* ServiceName
* 서비스복제 이름
* 코드 패키지 이름

> [!NOTE]
> Linux 응용 프로그램에서CodePackageName 차원을 사용할 수 없습니다. 

각 차원은 서비스 패브릭 [응용 프로그램 모델의](service-fabric-mesh-service-fabric-resources.md#applications-and-services) 다른 구성 요소에 해당합니다.

### <a name="azure-monitor-cli"></a>Azure 모니터 CLI

명령의 전체 목록은 [Azure 모니터 CLI 문서에서](https://docs.microsoft.com/cli/azure/monitor/metrics?view=azure-cli-latest#az-monitor-metrics-list) 사용할 수 있지만 아래에 몇 가지 유용한 예제가 포함되어 있습니다. 

각 예제에서 리소스 ID는 이 패턴을 따릅니다.

`"/subscriptions/<your sub ID>/resourcegroups/<your RG>/providers/Microsoft.ServiceFabricMesh/applications/<your App name>"`


* 응용 프로그램에서 컨테이너의 CPU 사용률

```cli
    az monitor metrics list --resource <resourceId> --metric "CpuUtilization"
```
* 각 서비스 복제본에 대한 메모리 사용률
```cli
    az monitor metrics list --resource <resourceId> --metric "MemoryUtilization" --dimension "ServiceReplicaName"
``` 

* 1시간 동안 각 컨테이너에 대해 다시 시작 
```cli
    az monitor metrics list --resource <resourceId> --metric "RestartCount" --start-time 2019-02-01T00:00:00Z --end-time 2019-02-01T01:00:00Z
``` 

* 1시간 동안 "VotingWeb"이라는 서비스의 평균 CPU 사용률
```cli
    az monitor metrics list --resource <resourceId> --metric "CpuUtilization" --start-time 2019-02-01T00:00:00Z --end-time 2019-02-01T01:00:00Z --aggregation "Average" --filter "ServiceName eq 'VotingWeb'"
``` 

### <a name="metrics-explorer"></a>메트릭 탐색기

메트릭 탐색기는 메시 응용 프로그램에 대한 모든 메트릭을 시각화할 수 있는 포털의 블레이드입니다. 이 블레이드는 포털및 Azure 모니터 블레이드의 응용 프로그램 페이지에서 액세스할 수 있으며, 이 블레이드는 Azure Monitor를 지원하는 모든 Azure 리소스에 대한 메트릭을 보는 데 사용할 수 있습니다. 

![메트릭 탐색기](./media/service-fabric-mesh-monitoring-diagnostics/metricsexplorer.png)


<!--
### Container Insights

In addition to the metrics explorer, we also have a dashboard available out of the box that shows sample metrics over time under the Insights blade in the application's page in the portal. 

![Container Insights](./media/service-fabric-mesh-monitoring-diagnostics/containerinsights.png)
-->

## <a name="next-steps"></a>다음 단계
* Service Fabric Mesh에 대한 자세한 내용은 [Service Fabric Mesh 개요](service-fabric-mesh-overview.md)를 참조하세요.
* Azure 모니터 메트릭 명령에 대해 자세히 알아보려면 [Azure 모니터 CLI 문서를 확인하십시오.](https://docs.microsoft.com/cli/azure/monitor/metrics?view=azure-cli-latest#az-monitor-metrics-list)
