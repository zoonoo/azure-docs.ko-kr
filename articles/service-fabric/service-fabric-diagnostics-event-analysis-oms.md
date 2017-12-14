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
ms.openlocfilehash: 977c5d64a32157b39aa6b618196dde20c4c3cc8e
ms.sourcegitcommit: 922687d91838b77c038c68b415ab87d94729555e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/13/2017
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

클러스터에서 이를 시작하려면 [OMS Log Analytics 설정](service-fabric-diagnostics-oms-setup.md)을 참조하세요.

## <a name="using-the-oms-agent"></a>OMS 에이전트 사용

EventFlow 및 WAD는 진단 및 모니터링에 좀 더 모듈 방식으로 접근할 수 있으므로 집계 솔루션으로 사용하는 것이 좋습니다. 예를 들어 EventFlow의 출력을 변경하려는 경우 실제 계측을 변경할 필요 없이 구성 파일을 간단히 수정하기만 하면 됩니다. 그러나 OMS Log Analytics를 사용하는 데 투자하기로 결정한 경우 [OMS 에이전트](../log-analytics/log-analytics-windows-agent.md)를 설정해야 합니다. 또한 아래 설명된 대로 클러스터에 컨테이너를 배포하는 경우 OMS 에이전트를 사용해야 합니다. 

이에 대한 단계는 [OMS Agent를 클러스터에 추가](service-fabric-diagnostics-oms-agent.md)를 참조하세요.

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

[OMS Log Analytics를 사용하여 컨테이너 모니터링](service-fabric-diagnostics-oms-containers.md)에서는 클러스터에 대해 컨테이너 모니터링을 설정하는 데 필요한 단계를 설명합니다. OMS 컨테이너 솔루션에 대해 자세히 알아보려면 해당 [설명서](../log-analytics/log-analytics-containers.md)를 확인하세요.

## <a name="next-steps"></a>다음 단계

다음 OMS 도구 및 옵션을 탐색하여 필요에 맞게 작업 영역을 사용자 지정합니다.

* 온-프레미스 클러스터의 경우 OMS는 OMS로 데이터를 보내는 데 사용할 수 있는 게이트웨이(HTTP 전달 프록시)를 제공합니다. 자세한 내용은 [OMS 게이트웨이를 사용하여 인터넷 액세스 없이 OMS에 컴퓨터 연결](../log-analytics/log-analytics-oms-gateway.md)을 참조하세요.
* 검색 및 진단에 도움이 되는 [자동 경고](../log-analytics/log-analytics-alerts.md)를 설정하도록 OMS 구성
* Log Analytics의 일부로 제공되는 [로그 검색 및 쿼리](../log-analytics/log-analytics-log-searches.md) 기능 알아보기