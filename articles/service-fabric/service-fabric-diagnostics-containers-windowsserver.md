---
title: "Azure Service Fabric 컨테이너 모니터링 및 진단 | Microsoft Docs"
description: "OMS의 컨테이너 솔루션을 사용하여 Microsoft Azure Service Fabric에서 조정되는 컨테이너를 모니터링하고 진단하는 방법을 알아봅니다."
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
ms.date: 05/10/2017
ms.author: dekapur
ms.openlocfilehash: 874c1a5c4b399ff2254072b7282f05d83a005cc3
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/11/2017
---
# <a name="monitoring-windows-server-containers-with-oms"></a>OMS를 사용하여 Windows Server 컨테이너 모니터링

## <a name="oms-containers-solution"></a>OMS 컨테이너 솔루션

OMS(Operations Management Suite) 팀은 컨테이너의 모니터링 및 진단을 위해 컨테이너 솔루션을 게시했습니다. 이 솔루션은 Service Fabric 솔루션과 함께 Service Fabric에서 조정된 컨테이너 배포를 모니터링하기 위한 유용한 도구입니다. 다음은 이 솔루션의 대시보드 모습을 보여 주는 간단한 예입니다.

![기본 OMS 대시보드](./media/service-fabric-diagnostics-containers-windowsserver/oms-containers-dashboard.png)

이 솔루션은 OMS Log Analytics 도구에서 쿼리할 수 있는 다양한 종류의 로그를 수집하고, 생성되는 메트릭 또는 이벤트를 시각화하는 데도 사용할 수 있습니다. 수집되는 로그 형식은 다음과 같습니다.

1. ContainerInventory: 컨테이너 위치, 이름 및 이미지에 대한 정보를 표시합니다.
2. ContainerImageInventory: ID 또는 크기를 포함하여 배포된 이미지에 대한 정보를 표시합니다.
3. ContainerLog: 특정 오류 로그, docker 로그(stdout 등) 및 기타 항목을 표시합니다.
4. ContainerServiceLog: 실행된 docker 디먼 명령을 표시합니다.
5. Perf: 호스트 컴퓨터의 컨테이너 CPU, 메모리, 네트워크 트래픽, 디스크 I/O, 사용자 지정 메트릭을 포함하는 성능 카운터를 표시합니다.

이 문서에서는 클러스터에 대해 컨테이너는 모니터링을 설정하는 데 필요한 단계를 설명합니다. OMS 컨테이너 솔루션에 대해 자세히 알아보려면 해당 [설명서](../log-analytics/log-analytics-containers.md)를 확인하세요.

## <a name="1-set-up-a-service-fabric-cluster"></a>1. Service Fabric 클러스터 설정

[여기](https://github.com/dkkapur/Service-Fabric/tree/master/ARM%20Templates/SF%20OMS%20Sample)에 나오는 Azure Resource Manager 템플릿을 사용하여 클러스터를 만듭니다. 고유한 OMS 작업 영역 이름을 추가해야 합니다. 또한 이 템플릿은 기본적으로 Service Fabric의 미리 보기 빌드(v255.255)에 클러스터를 배포합니다. 즉, 이 빌드는 프로덕션 환경에서 사용할 수 없으며 다른 Service Fabric 버전으로 업그레이드할 수 없습니다. 장기적으로 또는 프로덕션 환경에서 이 템플릿을 사용하려는 경우 버전을 안정적인 버전 번호로 변경합니다.

클러스터가 설정되면 적절한 인증서를 설치했는지와 클러스터에 연결할 수 있는지 확인합니다.

[Azure Portal](https://portal.azure.com/)로 이동한 후 배포를 찾아 리소스 그룹이 제대로 설정되어 있는지 확인합니다. 리소스 그룹에는 모든 Service Fabric 리소스가 포함되고 Service Fabric 솔루션 뿐 아니라 Log Analytics 솔루션도 있습니다.

기존 Service Fabric 클러스터 수정:
* 진단이 사용되도록 설정되어 있는지 확인(설정되어 있지 않으면 [가상 컴퓨터 확장 집합을 업데이트](/rest/api/virtualmachinescalesets/create-or-update-a-set)하여 설정)
* Azure Marketplace를 통해 "Service Fabric 분석" 솔루션을 만들어 OMS 작업 영역 추가
* 클러스터가 있는 리소스 그룹의 해당 Azure Storage 테이블(WAD에서 설정)에서 데이터를 선택하도록 Service Fabric 솔루션의 데이터 원본 편집
* PowerShell을 통해 또는 가상 컴퓨터 크기 집합을 업데이트하여 [가상 컴퓨터 크기 집합에 대한 확장](/powershell/module/azurerm.compute/add-azurermvmssextension)으로 에이전트 추가(Resource Manager 템플릿을 수정하려는 경우에도 위와 동일한 링크 사용)

## <a name="2-deploy-a-container"></a>2. 컨테이너 배포

클러스터가 준되었으며 액세스할 수 있는지 확인한 경우 컨테이너를 배포합니다. 템플릿에서 설정된 대로 미리 보기 버전을 사용하도록 선택한 경우 Service Fabric의 새 Docker Compose 기능을 탐색할 수도 있습니다. 클러스터에 컨테이너 이미지를 처음 배포할 때는 해당 크기에 따라 이미지를 다운로드하는 데 몇 분 정도 걸립니다.

## <a name="3-add-the-containers-solution"></a>3. 컨테이너 솔루션 추가

Azure Portal에서 Azure Marketplace를 통해 컨테이너 리소스를 만듭니다(모니터링 + 관리 범주 아래). 

![컨테이너 솔루션 추가](./media/service-fabric-diagnostics-containers-windowsserver/containers-solution.png)

만들기 단계에서 OMS 작업 영역을 요청합니다. 위의 배포에서 만든 작업 영역을 선택합니다. 이 단계에서는 OMS 작업 영역 내에 컨테이너 솔루션이 추가되고, 템플릿을 통해 배포된 OMS 에이전트에 의해 자동으로 검색됩니다. 에이전트는 클러스터에서 컨테이너 프로세스에 대한 데이터를 수집하기 시작하고, 약 10-15분 후에 위 대시보드의 이미지와 같은 데이터를 포함하는 솔루션이 표시됩니다.

## <a name="4-next-steps"></a>4. 다음 단계

OMS는 보다 유용하게 작업할 수 있도록 하는 다양한 도구를 작업 영역에 제공합니다. 다음 옵션을 사용하여 요구에 맞게 솔루션을 사용자 지정하세요.
- Log Analytics의 일부로 제공되는 [로그 검색 및 쿼리](../log-analytics/log-analytics-log-searches.md) 기능 알아보기
- 특정 성능 카운터를 선택하도록 OMS 에이전트 구성(작업 영역 홈 > 설정 > 데이터 > Windows 성능 카운터로 이동)
- 검색 및 진단에 도움이 되는 [자동 경고](../log-analytics/log-analytics-alerts.md) 규칙을 설정하도록 OMS 구성