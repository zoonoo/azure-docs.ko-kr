---
title: 컴퓨터 및 응용 프로그램 보호
description: 이 문서에서는 가상 머신, 컴퓨터, 웹 앱 및 App Service 환경을 보호하는 데 도움이 되는 Security Center의 권장 사항을 설명합니다.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 47fa1f76-683d-4230-b4ed-d123fef9a3e8
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/11/2020
ms.author: memildin
ms.openlocfilehash: 93dde2e873a00303dcb8563caed4d56dbf11cc12
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "80435642"
---
# <a name="protect-your-machines-and-applications"></a>컴퓨터 및 응용 프로그램 보호
Azure Security Center에서 잠재적인 보안 취약점을 식별 하는 경우 리소스를 강화 하 고 보호 하는 데 필요한 컨트롤을 구성 하는 과정을 안내 하는 권장 사항을 만듭니다.

이 문서에서는 Security Center 리소스 보안 섹션의 **계산 및 앱** 페이지에 대해 설명 합니다.

이 페이지에 표시 될 수 있는 권장 사항의 전체 목록은 [Compute 및 apps 권장 사항](recommendations-reference.md#recs-computeapp)을 참조 하세요.


## <a name="view-the-security-of-your-compute-and-apps-resources"></a>계산 및 앱 리소스의 보안 보기

[![Security Center 대시보드](./media/security-center-virtual-machine-recommendations/compute-and-apps-recs-overview.png)](./media/security-center-virtual-machine-recommendations/compute-and-apps-recs-overview.png#lightbox)

계산 및 앱 리소스의 상태를 보려면 Security Center의 왼쪽 창에서 **compute & apps**를 선택 합니다. 다음 탭을 사용할 수 있습니다.

* **개요**: 모든 계산 및 앱 리소스에 대 한 권장 사항 및 현재 보안 상태를 나열 합니다. 

* [**Vm 및 서버**](#vms-and-computers): vm, 컴퓨터 및 각각의 현재 보안 상태에 대 한 권장 사항을 나열 합니다.

* [**VM 규모 집합**](#vmscale-sets): 확장 집합에 대 한 권장 사항을 나열 합니다. 

* [**Cloud services**](#cloud-services):에서 모니터링 하는 웹 및 작업자 역할에 대 한 권장 사항을 나열 Security Center

* [**App services**](#app-services): app service environment에 대 한 권장 사항 및 각각의 현재 보안 상태를 나열 합니다.

* [**컨테이너**](#containers): 컨테이너의 권장 사항 및 해당 구성의 보안 평가를 나열 합니다.

* **계산 리소스**: Service Fabric 클러스터 및 Event hubs와 같은 계산 리소스에 대 한 권장 사항을 나열 합니다.

### <a name="whats-in-each-tab"></a>각 탭에는 무엇이 있나요?

각 탭에는 여러 섹션이 있으며 각 섹션에서 드릴 다운 하 여 표시 되는 항목에 대 한 추가 세부 정보를 볼 수 있습니다.

각 탭에는 모니터링 되는 환경의 관련 리소스에 대 한 권장 사항도 표시 됩니다. 첫 번째 열에는 권장 사항이 나열 되 고, 두 번째 열에는 영향을 받는 총 리소스 수가 표시 되 고, 세 번째 열에는 문제의 심각도가 표시 됩니다.

각 권장 사항에는 선택하여 수행할 수 있는 작업 집합이 있습니다. 예를 들어, **시스템 업데이트 누락**을 클릭하면 패치가 누락된 VM과 컴퓨터의 수와 누락된 업데이트의 심각도가 나타납니다.

> [!NOTE]
> 보안 권장 사항은 **권장 사항** 페이지의 경우와 동일 하지만 여기서는 선택한 특정 리소스 종류에 따라 필터링 됩니다. 권장 사항을 해결 하는 방법에 대 한 자세한 내용은 [Azure Security Center에서 보안 권장 사항 구현](security-center-recommendations.md)을 참조 하세요.
>




### <a name="vms-and-servers"></a><a name="vms-and-computers"></a>Vm 및 서버
Vm 및 컴퓨터 섹션에서는 Vm 및 컴퓨터에 대 한 모든 보안 권장 사항에 대 한 개요를 제공 합니다. 다음 네 가지 유형의 컴퓨터가 포함 됩니다.

![비 Azure 컴퓨터](./media/security-center-virtual-machine-recommendations/security-center-monitoring-icon1.png) 비 Azure 컴퓨터

![Azure Resource Manager VM](./media/security-center-virtual-machine-recommendations/security-center-monitoring-icon2.png) Azure Resource Manager VM입니다.

![Azure 클래식 VM](./media/security-center-virtual-machine-recommendations/security-center-monitoring-icon3.png) Azure 클래식 VM입니다.

![작업 영역에서 식별된 VM](./media/security-center-virtual-machine-recommendations/security-center-monitoring-icon4.png) 본 구독의 일부인 작업 영역에서만 식별되는 VM 여기에는이 구독의 작업 영역에 보고 하는 다른 구독의 Vm, Operations Manager direct agent와 함께 설치 된 Vm 및 리소스 ID가 없습니다.

각 권장 사항에 표시되는 아이콘은 주의를 기울여야 하는 VM과 컴퓨터 및 권장 사항의 형식을 식별하는 데 도움이 됩니다. 필터를 사용하여 **리소스 종류** 및 **심각도**를 기준으로 목록을 검색할 수도 있습니다.

각 VM에 대한 보안 권장 사항으로 드릴다운하려면 VM을 클릭합니다.
VM 또는 컴퓨터에 대한 보안 세부 정보는 다음과 같습니다. 맨 아래에서 권장 되는 작업과 각 문제의 심각도를 볼 수 있습니다.

[![클라우드 서비스](./media/security-center-virtual-machine-recommendations/recommendation-list.png)](./media/security-center-virtual-machine-recommendations/recommendation-list.png#lightbox)




### <a name="virtual-machine-scale-sets"></a><a name="vmscale-sets"></a>가상 머신 확장 집합
Security Center 확장 집합이 있는지 여부를 자동으로 검색 하 고 Log Analytics 에이전트를 설치할 것을 권장 합니다.

Log Analytics 에이전트를 설치 하려면 다음을 수행 합니다. 

1. 권장 사항 **가상 머신 확장 집합에 모니터링 에이전트 설치**를 선택합니다. 모니터링되지 않는 확장 집합의 목록이 표시됩니다.

1. 비정상 확장 집합을 선택합니다. 지침에 따라 기존의 채워진 작업 영역을 사용하여 모니터링 에이전트를 설치하거나 새로 만듭니다. 작업 영역 [가격 책정 계층](security-center-pricing.md) 을 설정 하지 않은 경우 설정 해야 합니다.

   ![MMS 설치](./media/security-center-virtual-machine-recommendations/install-mms.png)

Log Analytics 에이전트를 자동으로 설치 하도록 새 확장 집합을 설정 하려면 다음을 수행 합니다.
1. Azure Policy로 이동한 후 **정의**를 클릭합니다.

1. **Windows 가상 머신 확장 집합에 대 한 정책 배포 Log Analytics 에이전트** 를 검색 하 고 클릭 합니다.

1. **할당**을 클릭합니다.

1. **범위** 및 **Log Analytics 작업 영역**을 설정하고 **할당**을 클릭합니다.

Log Analytics 에이전트를 설치 하도록 기존 크기 집합을 모두 설정 하려면 Azure Policy에서 **수정** 으로 이동 하 고 기존 정책을 기존 확장 집합에 적용 합니다.





### <a name="cloud-services"></a><a name="cloud-services"></a>클라우드 서비스
클라우드 서비스에는 운영 체제 버전이 오래된 경우 권장 사항이 표시됩니다.

![클라우드 서비스](./media/security-center-virtual-machine-recommendations/security-center-monitoring-fig1-new006-2017.png)

권장 사항이 있는 시나리오에서 권장 사항의 단계에 따라 운영 체제를 업데이트 합니다. 업데이트를 사용할 수 있는 경우 문제의 심각도에 따라 경고가 표시 됩니다 (빨간색 또는 주황색). 이 권장 사항에 대 한 전체 설명은 **설명** 열에서 **OS 버전 업데이트** 를 클릭 합니다.






### <a name="app-services"></a><a name="app-services"></a>앱 서비스
App Service 정보를 보려면 Security Center의 표준 가격 책정 계층을 사용 하 고 구독에서 App Service를 사용 하도록 설정 해야 합니다. 이 기능을 사용 하는 방법에 대 한 지침은 [Azure Security Center를 사용 하 여 App Service 보호](security-center-app-services.md)를 참조 하세요.

**App Services**에는 App Service 환경 목록과 Security Center에서 수행된 평가에 기반한 상태 요약이 있습니다.

![App Services](./media/security-center-virtual-machine-recommendations/app-services.png)

표시 되는 응용 프로그램 서비스에는 다음 세 가지 유형이 있습니다.

![App Services 환경](./media/security-center-virtual-machine-recommendations/ase.png) App Services 환경

![웹 애플리케이션](./media/security-center-virtual-machine-recommendations/web-app.png) 웹 애플리케이션

![함수 애플리케이션](./media/security-center-virtual-machine-recommendations/function-app.png) 함수 애플리케이션

웹 응용 프로그램을 선택 하는 경우 다음 세 개의 탭이 포함 된 요약 보기가 열립니다.

   - **권장 사항**: 실패 한 Security Center에서 수행 하는 평가를 기준으로 합니다.
   - **통과된 평가**: 전달된 Security Center에서 수행된 평가의 목록입니다.
   - **사용할 수 없는 평가**: 오류로 인해 실행하지 못한 평가 목록 또는 특정 App Service와 관련이 없는 권장 사항입니다.

   **권장 사항**에는 선택한 웹 애플리케이션에 대한 권장 사항 목록과 각 권장 사항의 심각도가 표시됩니다.

   ![App Services 권장 사항](./media/security-center-virtual-machine-recommendations/app-services-rec.png)

권장 사항을 선택하여 권장 사항에 대한 설명, 비정상 리소스, 정상 리소스, 검사되지 않은 리소스 목록을 확인합니다.

   - **통과 된 평가** 열에는 통과 된 평가 목록이 표시 됩니다. 이러한 평가의 심각도는 항상 녹색입니다.

   - 평가에 대한 설명, 비정상 및 정상 리소스 목록 및 검사되지 않은 리소스 목록에서 통과된 평가를 선택합니다. 비정상 리소스에 대한 탭이 있지만 평가를 통과하기 때문에 이 목록은 항상 비어 있습니다.





### <a name="containers"></a><a name="containers"></a>컨테이너

사용자 환경에 따라 **컨테이너** 탭을 열면 다음과 같은 세 가지 유형의 리소스 중 하나가 표시 될 수 있습니다.

![컨테이너 호스트](./media/security-center-virtual-machine-recommendations/icon-container-host-rec.png) 컨테이너 호스트-docker를 실행 하는 Vm 

![Kubernetes 서비스](./media/security-center-virtual-machine-recommendations/icon-kubernetes-service-rec.png) AKS (Azure Kubernetes Service) 클러스터

![컨테이너 레지스트리](./media/security-center-virtual-machine-recommendations/icon-container-registry-rec.png) ACR (Azure Container Registry) 레지스트리-표준 가격 책정 계층에 있고 Azure Container Registry 번들을 사용 하는 경우에만 표시 됩니다.

컨테이너 보안 기능을 사용 하는 방법에 대 한 지침은 [컨테이너의 보안 모니터링](monitor-container-security.md)을 참조 하세요.

Azure Container Registry 번들의 이점은 [여기](azure-container-registry-integration.md) 에 설명 되어 있습니다.

Kubernetes Services 번들의 이점은 [여기](azure-kubernetes-service-integration.md) 에 설명 되어 있습니다.

[![컨테이너 탭](./media/security-center-virtual-machine-recommendations/container-recommendations-all-types.png)](./media/security-center-virtual-machine-recommendations/container-recommendations-all-types.png#lightbox)

목록에서 특정 리소스에 대 한 권장 사항을 보려면 해당 리소스를 클릭 합니다.

#### <a name="visibility-into-container-registries"></a>컨테이너 레지스트리 표시

예를 들어 위의 그림에 표시 된 목록에서 asc-demo ACR 레지스트리를 클릭 하면이 세부 정보 페이지로 이어질 수 있습니다.

[![특정 ACR 레지스트리에 대 한 권장 사항](./media/security-center-virtual-machine-recommendations/acr-registry-recs-list.png)](./media/security-center-virtual-machine-recommendations/acr-registry-recs-list.png#lightbox)


#### <a name="visibility-into-containers-hosted-on-iaas-linux-machines"></a>IaaS Linux 컴퓨터에 호스트된 컨테이너 파악

Docker를 실행 하는 Vm 중 하나를 클릭 하면 Docker 버전 및 호스트에서 실행 되는 이미지 수와 같은 컴퓨터의 컨테이너와 관련 된 정보가 세부 정보 페이지에 표시 됩니다.

![Docker를 실행 하는 VM에 대 한 권장 사항](./media/security-center-virtual-machine-recommendations/docker-recommendation.png)


#### <a name="security-recommendations-based-on-cis-benchmark-for-docker"></a>Docker의 CIS 벤치마크에 따른 보안 권장 사항

Security Center는 Docker 구성을 검색하고 평가된 모든 실패 규칙 목록을 제공하여 구성 오류를 표시합니다. Security Center는 이러한 문제를 신속하게 해결하고 시간을 절약하기 위한 지침을 제공합니다. Security Center는 Docker 구성을 지속적으로 평가하고 최신 상태를 제공합니다.

![컨테이너 탭](./media/security-center-virtual-machine-recommendations/container-cis-benchmark.png)


## <a name="next-steps"></a>다음 단계
다른 Azure 리소스 유형에 적용 되는 권장 사항에 대해 자세히 알아보려면 다음 문서를 참조 하세요.

* [Azure Security Center의 보안 권장 사항에 대 한 전체 참조 목록](recommendations-reference.md)
* [Azure Security Center에서 ID 및 액세스 모니터링](security-center-identity-access.md)
* [Azure Security Center에서 네트워크 보호](security-center-network-recommendations.md)
* [Azure Security Center에서 Azure SQL 서비스 보호](security-center-sql-service-recommendations.md)