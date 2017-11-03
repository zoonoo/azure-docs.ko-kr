---
title: "OMS를 사용하여 Azure Service Fabric 이벤트 분석 | Microsoft Docs"
description: "Azure Service Fabric 클러스터의 모니터링 및 진단을 위해 OMS를 사용하여 이벤트를 시각화 및 분석하는 방법에 대해 알아봅니다."
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/15/2017
ms.author: dekapur
ms.openlocfilehash: f0cefab15a115719ea9c378546a7e6004bd06187
ms.sourcegitcommit: a7c01dbb03870adcb04ca34745ef256414dfc0b3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/17/2017
---
# <a name="event-analysis-and-visualization-with-oms"></a>OMS를 사용하여 이벤트 분석 및 시각화

OMS(Operations Management Suite)는 클라우드에 호스트된 응용 프로그램 및 서비스를 모니터링하고 진단하는 데 도움이 되는 관리 서비스 모음입니다. OMS에 대한 자세한 개요와 OMS에서 무엇을 제공하는지 보려면 [OMS란?](../operations-management-suite/operations-management-suite-overview.md)을 참조하세요.

## <a name="log-analytics-and-the-oms-workspace"></a>Log Analytics 및 OMS 작업 영역

Log Analytics는 Azure Storage 테이블이나 에이전트를 비롯한 관리 리소스에서 데이터를 수집하여 중앙 리포지토리에서 유지 관리합니다. 그런 다음 데이터를 분석, 경고 및 시각화하거나 내보낼 수 있습니다. Log Analytics는 이벤트, 성능 데이터 또는 기타 사용자 지정 데이터를 지원합니다.

OMS가 구성되면 대시보드에서 데이터를 쿼리하거나 시각화할 수 있는 특정 *OMS 작업 영역*에 액세스할 수 있습니다.

Log Analytics가 데이터를 수신한 후 OMS는 들어오는 데이터를 모니터링하기 위해 미리 패키지화되고 여러 시나리오에 맞게 사용자 지정된 솔루션인 여러 *관리 솔루션*을 사용합니다. 여기에는 Service Fabric 클러스터를 사용할 때 진단 및 모니터링과 가장 관련된 두 개의 솔루션인 *Service Fabric 분석* 솔루션과 *컨테이너* 솔루션이 포함됩니다. OMS에는 살펴볼 만한 여러 가지 솔루션이 있지만 사용자 지정 솔루션도 만들 수 있습니다. 자세한 내용은 [여기](../operations-management-suite/operations-management-suite-solutions.md)를 참조하세요. 클러스터에 사용하도록 선택한 각 솔루션은 Log Analytics와 함께 동일한 OMS 작업 영역에서 구성됩니다. 작업 영역을 통해 사용자 지정 대시보드 및 데이터의 시각화뿐만 아니라 수집, 처리 및 분석하려는 데이터를 수정할 수 있습니다.

## <a name="setting-up-an-oms-workspace-with-the-service-fabric-analytics-solution"></a>Service Fabric 분석 솔루션을 사용하여 OMS 작업 영역 설정
Service Fabric 솔루션은 플랫폼 및 응용 프로그램 수준에서 들어오는 다양한 로그 채널을 보여 주는 대시보드가 포함되어 있고 Service Fabric 특정 로그를 쿼리하는 기능을 제공하므로 OMS 작업 영역에 포함하는 것이 좋습니다. 다음은 클러스터에 배포된 단일 응용 프로그램이 있는 비교적 단순한 Service Fabric 솔루션입니다.

![OMS SF 솔루션](media/service-fabric-diagnostics-event-analysis-oms/service-fabric-solution.png)

Resource Manager 템플릿을 사용하거나 Azure Marketplace에서 직접 OMS 작업 영역을 프로비저닝하고 구성할 수 있습니다. 클러스터를 배포하는 중이면 전자를 사용하고, 진단을 사용하도록 설정된 클러스터가 이미 배포된 경우에는 후자를 사용합니다.

### <a name="deploying-oms-using-a-resource-management-template"></a>Resource Management 템플릿을 사용하여 OMS 배포

리소스 관리자 템플릿을 사용하여 클러스터를 배포할 때 템플릿은 새 OMS 작업 영역을 만들고 여기에 Service Fabric 솔루션을 추가하고 적절한 저장소 테이블에서 데이터를 읽도록 구성할 수 있습니다.

>[!NOTE]
>이렇게 하려면 OMS/Log Analytics에서 정보를 읽어 올 Azure Storage 테이블이 존재하기 위해 진단을 사용하도록 설정해야 합니다.

위의 작업을 수행하는 샘플 템플릿은 [여기](https://azure.microsoft.com/resources/templates/service-fabric-oms/)에 있으며 요구 사항에 따라 사용하고 수정할 수 있습니다. 더 많은 옵션이 필요한 경우 OMS 작업 영역을 설정하는 프로세스의 위치에 따라 다양한 옵션을 제공하는 몇 가지 템플릿이 있습니다. 이러한 템플릿은 [Service Fabric 및 OMS 템플릿](https://azure.microsoft.com/resources/templates/?term=service+fabric+OMS)에서 찾을 수 있습니다.

### <a name="deploying-oms-using-through-azure-marketplace"></a>Azure Marketplace를 통해 OMS 배포

클러스터를 배포한 후에 OMS 작업 영역을 추가하려면 Azure Marketplace로 이동하고 *"Service Fabric 분석"*을 검색합니다.

![Marketplace의 OMS SF 분석](media/service-fabric-diagnostics-event-analysis-oms/service-fabric-analytics.png)

* **만들기**를 클릭합니다.
* Service Fabric 분석 만들기 창에서 *OMS 작업 영역* 필드에 대해 **작업 영역 선택**, **새 작업 영역 만들기**를 차례로 클릭합니다. 필요한 항목을 입력합니다. 단, Service Fabric 클러스터와 OMS 작업 영역에 대한 구독이 동일해야 합니다. 입력 항목의 유효성이 검사되면 OMS 작업 영역이 배포되기 시작하며, 몇 분밖에 안 걸립니다.
* 완료되면 Service Fabric 분석 만들기 창 맨 아래에서 **만들기**를 다시 클릭합니다. *OMS 작업 영역* 아래에 새 작업 영역이 표시되는지 확인합니다. 이렇게 하면 방금 만든 작업 영역에 이 솔루션이 추가됩니다.


작업 영역에 새 솔루션은 추가되지만, 클러스터에서 가져온 진단 데이터에 해당 작업 영역을 연결해야 합니다. Service Fabric 분석 솔루션을 만든 리소스 그룹으로 이동합니다. *ServiceFabric(\<nameOfOMSWorkspace\>)*이 보입니다.

* 솔루션을 클릭하여 솔루션 설정, 작업 영역 설정을 변경할 수 있는 개요 페이지로 이동한 다음, OMS 포털로 이동합니다.
* 왼쪽 탐색 메뉴에서 *작업 영역 데이터 원본*의 **저장소 계정 로그**를 클릭합니다.

    ![포털의 Service Fabric 분석 솔루션](media/service-fabric-diagnostics-event-analysis-oms/service-fabric-analytics-portal.png)

* *저장소 계정 로그* 페이지에서 맨 위의 **추가**를 클릭하여 작업 영역에 클러스터 로그를 추가합니다.
* **저장소 계정**을 클릭하여 클러스터에서 만든 적절한 계정을 추가합니다. 기본 이름을 사용한 경우 저장소 계정의 이름은 *sfdg\<resourceGroupName\>*으로 지정됩니다. 또한, 클러스터를 배포하기 위해 사용한 Azure Resource Manager 템플릿을 확인하고, `applicationDiagnosticsStorageAccountName`에 사용한 값을 확인하여 확인할 수도 있습니다. 계정 이름이 표시되지 않을 경우 아래로 스크롤하여 **추가 로드**를 클릭해야 할 수도 있습니다. 표시되면 오른쪽 저장소 계정 이륾을 클릭하여 선택합니다.
* 다음으로, *데이터 형식*을 **Service Fabric 이벤트**로 지정해야 합니다.
* *원본*은 *WADServiceFabric\*EventTable*로 자동 설정되어야 합니다.
* **확인**을 클릭하여 작업 영역을 클러스터 로그에 연결합니다.

    ![OMS에 저장소 계정 로그 추가](media/service-fabric-diagnostics-event-analysis-oms/add-storage-account.png)

* 이제 계정이 작업 영역의 데이터 원본에서 *저장소 계정 로그*의 일부로 표시됩니다.

이를 통해 이제 OMS Log Analytics 작업 영역에서 Service Fabric 분석 솔루션을 추가했으며, 이제는 클러스터의 플랫폼 및 응용 프로그램 로그 표에 제대로 연결됩니다. 같은 방식으로 작업 영역에 추가적인 원본을 추가할 수 있습니다.

## <a name="using-the-oms-agent"></a>OMS 에이전트 사용

EventFlow 및 WAD는 진단 및 모니터링에 좀 더 모듈 방식으로 접근할 수 있으므로 집계 솔루션으로 사용하는 것이 좋습니다. 예를 들어 EventFlow의 출력을 변경하려는 경우 실제 계측을 변경할 필요 없이 구성 파일을 간단히 수정하기만 하면 됩니다. 그러나 OMS를 사용하기로 결정하고 이벤트 분석을 위해 계속 사용하려는 경우(사용하는 유일한 플랫폼일 필요는 없지만 플랫폼 중 적어도 하나) [OMS 에이전트](../log-analytics/log-analytics-windows-agents.md) 설정을 탐색하는 것이 좋습니다. 또한 아래 설명된 대로 클러스터에 컨테이너를 배포하는 경우 OMS 에이전트를 사용해야 합니다.

이 작업을 수행하는 프로세스는 각 노드에 에이전트가 설치되도록 Resource Manager 템플릿에 가상 컴퓨터 확장 집합 확장으로 에이전트를 추가하기만 하면 되므로 비교적 간단합니다 Service Fabric 솔루션과 함께 OMS 작업 영역을 배포하고(위의 설명 참조) 노드에 에이전트를 추가하는 샘플 Resource Manager 템플릿은 [Windows](https://github.com/ChackDan/Service-Fabric/tree/master/ARM%20Templates/SF%20OMS%20Samples/Windows) 또는 [Linux](https://github.com/ChackDan/Service-Fabric/tree/master/ARM%20Templates/SF%20OMS%20Samples/Linux)를 실행하는 클러스터에서 찾을 수 있습니다.

이 방법의 장점은 다음과 같습니다.

* 성능 카운터 및 메트릭 측면에서 더 다양한 데이터를 제공합니다.
* 클러스터의 구성을 업데이트하지 않고도 메트릭을 클러스터에서 수집하도록 간편하게 구성합니다. OMS 포털에서 에이전트 설정을 변경할 수 있으며 에이전트가 자동으로 다시 시작하여 필요한 구성을 맞춥니다. 특정 성능 카운터를 선택하도록 OMS 에이전트를 구성하려면 작업 영역 **홈 > 설정 > 데이터 > Windows 성능 카운터**로 이동하고 수집하려는 데이터를 선택합니다.
* OMS/Log Analytics에서 데이터를 선택하려면 먼저 저장되어야 하는데 이보다 데이터가 더 빠르게 표시됩니다.
* 컨테이너 모니터링은 Docker 로그(stdout, stderr) 및 통계(컨테이너 및 노드 수준의 성능 메트릭)를 선택할 수 있기 때문에 훨씬 쉽습니다.

단, 에이전트가 모든 응용 프로그램과 함께 클러스터에 배포되므로 클러스터의 응용 프로그램 성능에 영향을 줄 수 있습니다.

## <a name="monitoring-containers"></a>컨테이너 모니터링

Service Fabric 클러스터에 컨테이너를 배포할 때는 클러스터에 OMS 에이전트가 설치되고 모니터링 및 진단이 가능하도록 컨테이너 솔루션이 OMS 작업 영역에 추가되어 있는 것이 좋습니다. 작업 영역에서 컨테이너 솔루션의 모양은 다음과 같습니다.

![기본 OMS 대시보드](./media/service-fabric-diagnostics-event-analysis-oms/oms-containers-dashboard.png)

에이전트를 사용하면 OMS에서 쿼리하거나 성능 지표를 시각화하는 데 사용할 수 있는 몇 가지 컨테이너별 로그를 수집할 수 있습니다. 수집되는 로그 형식은 다음과 같습니다.

* ContainerInventory: 컨테이너 위치, 이름 및 이미지에 대한 정보를 표시합니다.
* ContainerImageInventory: ID 또는 크기를 포함하여 배포된 이미지에 대한 정보를 표시합니다.
* ContainerLog: 특정 오류 로그, docker 로그(stdout 등) 및 기타 항목을 표시합니다.
* ContainerServiceLog: 실행된 docker 디먼 명령을 표시합니다.
* Perf: 호스트 컴퓨터의 컨테이너 CPU, 메모리, 네트워크 트래픽, 디스크 I/O, 사용자 지정 메트릭을 포함하는 성능 카운터를 표시합니다.

이 문서에서는 클러스터에 대해 컨테이너는 모니터링을 설정하는 데 필요한 단계를 설명합니다. OMS 컨테이너 솔루션에 대해 자세히 알아보려면 해당 [설명서](../log-analytics/log-analytics-containers.md)를 확인하세요.

작업 영역에서 컨테이너 솔루션을 설정하려면 위에서 설명한 단계를 수행하여 클러스터 노드에 OMS 에이전트를 배포해야 합니다. 클러스터가 준비되면 컨테이너를 배치합니다. 클러스터에 컨테이너 이미지를 처음 배포할 때는 해당 크기에 따라 이미지를 다운로드하는 데 몇 분 정도 걸립니다.

Azure Marketplace에서 *컨테이너 모니터링 솔루션*을 검색하고 모니터링 + 관리 범주 아래에 나타나야 하는 **컨테이너 모니터링 솔루션** 결과를 만듭니다.

![컨테이너 솔루션 추가](./media/service-fabric-diagnostics-event-analysis-oms/containers-solution.png)

만들기 단계에서 OMS 작업 영역을 요청합니다. 위의 배포에서 만든 작업 영역을 선택합니다. 이 단계에서는 OMS 작업 영역 내에 컨테이너 솔루션이 추가되고, 템플릿을 통해 배포된 OMS 에이전트에 의해 자동으로 검색됩니다. 에이전트는 클러스터에서 컨테이너 프로세스에 대한 데이터를 수집하기 시작하고, 약 15분 미만으로 위 대시보드의 이미지와 같은 데이터를 포함하는 솔루션이 표시됩니다.


## <a name="next-steps"></a>다음 단계

다음 OMS 도구 및 옵션을 탐색하여 필요에 맞게 작업 영역을 사용자 지정합니다.

* 온-프레미스 클러스터의 경우 OMS는 OMS로 데이터를 보내는 데 사용할 수 있는 게이트웨이(HTTP 전달 프록시)를 제공합니다. 자세한 내용은 [OMS 게이트웨이를 사용하여 인터넷 액세스 없이 OMS에 컴퓨터 연결](../log-analytics/log-analytics-oms-gateway.md)을 참조하세요.
* 검색 및 진단에 도움이 되는 [자동 경고](../log-analytics/log-analytics-alerts.md)를 설정하도록 OMS 구성
* Log Analytics의 일부로 제공되는 [로그 검색 및 쿼리](../log-analytics/log-analytics-log-searches.md) 기능 알아보기