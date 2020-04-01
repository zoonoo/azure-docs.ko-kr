---
title: 기계 및 애플리케이션 보호
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
ms.sourcegitcommit: ced98c83ed25ad2062cc95bab3a666b99b92db58
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/31/2020
ms.locfileid: "80435642"
---
# <a name="protect-your-machines-and-applications"></a>기계 및 애플리케이션 보호
Azure Security Center에서 잠재적인 보안 취약점을 식별하면 리소스를 강화하고 보호하는 데 필요한 컨트롤을 구성하는 프로세스를 안내하는 권장 사항을 만듭니다.

이 문서에서는 보안 센터의 리소스 보안 섹션의 **계산 및 앱** 페이지를 설명합니다.

이 페이지에 표시될 수 있는 권장 사항의 전체 목록은 [계산 및 앱 권장 사항을](recommendations-reference.md#recs-computeapp)참조하십시오.


## <a name="view-the-security-of-your-compute-and-apps-resources"></a>컴퓨팅 및 앱 리소스의 보안 보기

[![Security Center 대시보드](./media/security-center-virtual-machine-recommendations/compute-and-apps-recs-overview.png)](./media/security-center-virtual-machine-recommendations/compute-and-apps-recs-overview.png#lightbox)

보안 센터의 왼쪽 창에서 계산 및 앱 리소스의 상태를 보려면 **앱 계산&을**선택합니다. 다음 탭을 사용할 수 있습니다.

* **개요**: 모든 계산 및 앱 리소스에 대한 권장 사항과 현재 보안 상태를 나열합니다. 

* [**VM 및 서버**](#vms-and-computers): 각 VM, 컴퓨터 및 각 서버의 현재 보안 상태에 대한 권장 사항을 나열합니다.

* [**VM 스케일 세트**](#vmscale-sets): 스케일 세트에 대한 권장 사항을 나열하고, 

* [**클라우드 서비스**](#cloud-services): 보안 센터에서 모니터링하는 웹 및 작업자 역할에 대한 권장 사항을 나열합니다.

* [**앱 서비스**](#app-services): 앱 서비스 환경에 대한 권장 사항과 각 서비스의 현재 보안 상태를 나열합니다.

* [**컨테이너**](#containers): 컨테이너에 대한 권장 사항 및 구성에 대한 보안 평가 가 나열됩니다.

* **리소스 계산**: 서비스 패브릭 클러스터 및 이벤트 허브와 같은 컴퓨팅 리소스에 대한 권장 사항을 나열합니다.

### <a name="whats-in-each-tab"></a>각 탭에는 무엇이 있습니까?

각 탭에는 여러 섹션이 있으며 각 섹션에서 드릴다운하여 표시된 항목에 대한 추가 세부 정보를 볼 수 있습니다.

각 탭에는 모니터링되는 환경에서 관련 리소스에 대한 권장 사항도 표시됩니다. 첫 번째 열에는 권장 사항이 나열되고 두 번째 열에는 영향을 받는 총 리소스 수가 표시되고 세 번째 열에는 문제의 심각도가 표시됩니다.

각 권장 사항에는 선택하여 수행할 수 있는 작업 집합이 있습니다. 예를 들어, **시스템 업데이트 누락**을 클릭하면 패치가 누락된 VM과 컴퓨터의 수와 누락된 업데이트의 심각도가 나타납니다.

> [!NOTE]
> 보안 권장 사항은 **권장 사항** 페이지의 권장 사항과 동일하지만 선택한 특정 리소스 유형으로 필터링됩니다. 권장 사항을 해결하는 방법에 대한 자세한 내용은 [Azure 보안 센터의 보안 권장 사항 구현을](security-center-recommendations.md)참조하십시오.
>




### <a name="vms-and-servers"></a><a name="vms-and-computers"></a>VM 및 서버
VM 및 컴퓨터 섹션에서는 VM 및 컴퓨터에 대한 모든 보안 권장 사항에 대한 개요를 제공합니다. 기계의 네 가지 유형이 포함되어 있습니다 :

![비 Azure 컴퓨터](./media/security-center-virtual-machine-recommendations/security-center-monitoring-icon1.png) 비 Azure 컴퓨터

![Azure Resource Manager VM](./media/security-center-virtual-machine-recommendations/security-center-monitoring-icon2.png) Azure Resource Manager VM입니다.

![Azure 클래식 VM](./media/security-center-virtual-machine-recommendations/security-center-monitoring-icon3.png) Azure 클래식 VM입니다.

![작업 영역에서 식별된 VM](./media/security-center-virtual-machine-recommendations/security-center-monitoring-icon4.png) 본 구독의 일부인 작업 영역에서만 식별되는 VM 여기에는 이 구독의 작업 영역에 보고하는 다른 구독의 VM과 Operations Manager 직접 에이전트와 함께 설치되었으며 리소스 ID가 없는 VM이 포함됩니다.

각 권장 사항에 표시되는 아이콘은 주의를 기울여야 하는 VM과 컴퓨터 및 권장 사항의 형식을 식별하는 데 도움이 됩니다. 필터를 사용하여 **리소스 종류** 및 **심각도**를 기준으로 목록을 검색할 수도 있습니다.

각 VM에 대한 보안 권장 사항으로 드릴다운하려면 VM을 클릭합니다.
VM 또는 컴퓨터에 대한 보안 세부 정보는 다음과 같습니다. 하단에서 권장 작업 및 각 문제의 심각도를 볼 수 있습니다.

[![클라우드 서비스](./media/security-center-virtual-machine-recommendations/recommendation-list.png)](./media/security-center-virtual-machine-recommendations/recommendation-list.png#lightbox)




### <a name="virtual-machine-scale-sets"></a><a name="vmscale-sets"></a>가상 머신 크기 집합
보안 센터는 확장 집합이 있는지 여부를 자동으로 검색하고 Log Analytics 에이전트를 설치하는 것이 좋습니다.

로그 분석 에이전트를 설치하려면 다음을 수행하십시오. 

1. 권장 사항 **가상 머신 확장 집합에 모니터링 에이전트 설치**를 선택합니다. 모니터링되지 않는 확장 집합의 목록이 표시됩니다.

1. 비정상 확장 집합을 선택합니다. 지침에 따라 기존의 채워진 작업 영역을 사용하여 모니터링 에이전트를 설치하거나 새로 만듭니다. 설정되지 않은 경우 작업 영역 [가격 책정 계층을](security-center-pricing.md) 설정해야 합니다.

   ![MMS 설치](./media/security-center-virtual-machine-recommendations/install-mms.png)

로그 분석 에이전트를 자동으로 설치하도록 새 축척 세트를 설정하려면 다음을 수행하십시오.
1. Azure Policy로 이동한 후 **정의**를 클릭합니다.

1. Windows 가상 **시스템 규모 집합에 대한 로그 분석 에이전트 배포** 정책을 검색하고 클릭합니다.

1. **할당**을 클릭합니다.

1. **범위** 및 **Log Analytics 작업 영역**을 설정하고 **할당**을 클릭합니다.

Azure Policy에서 로그 분석 에이전트를 설치하도록 모든 기존 축척 집합을 설정하려면 **업데이트 관리로** 이동하여 기존 확장 집합에 기존 정책을 적용합니다.





### <a name="cloud-services"></a><a name="cloud-services"></a>클라우드 서비스
클라우드 서비스에는 운영 체제 버전이 오래된 경우 권장 사항이 표시됩니다.

![클라우드 서비스](./media/security-center-virtual-machine-recommendations/security-center-monitoring-fig1-new006-2017.png)

권장 사항이 있는 시나리오에서는 권장 사항의 단계를 수행하여 운영 체제를 업데이트합니다. 업데이트를 사용할 수 있는 경우 경고(문제의 심각도에 따라 빨간색 또는 주황색)가 표시됩니다. 이 권장 사항에 대한 전체 설명을 보려면 **설명** 열 에서 **OS 버전 업데이트를** 클릭합니다.






### <a name="app-services"></a><a name="app-services"></a>앱 서비스
앱 서비스 정보를 보려면 보안 센터의 표준 가격 책정 계층에 있어야 하며 구독에서 앱 서비스를 사용하도록 설정해야 합니다. 이 기능을 사용하도록 설정하는 방법에 대한 지침은 [Azure 보안 센터를 사용하는 앱 서비스 보호를](security-center-app-services.md)참조하십시오.

**App Services**에는 App Service 환경 목록과 Security Center에서 수행된 평가에 기반한 상태 요약이 있습니다.

![App Services](./media/security-center-virtual-machine-recommendations/app-services.png)

다음과 같은 세 가지 유형의 응용 프로그램 서비스가 표시됩니다.

![App Services 환경](./media/security-center-virtual-machine-recommendations/ase.png) App Services 환경

![웹 애플리케이션](./media/security-center-virtual-machine-recommendations/web-app.png) 웹 애플리케이션

![함수 애플리케이션](./media/security-center-virtual-machine-recommendations/function-app.png) 함수 애플리케이션

웹 응용 프로그램을 선택하면 다음 세 개의 탭으로 요약 보기가 열립니다.

   - 권장 사항 : 실패한 보안 센터에서 수행한 평가를 기반으로 **합니다.**
   - **통과된 평가**: 전달된 Security Center에서 수행된 평가의 목록입니다.
   - **사용할 수 없는 평가**: 오류로 인해 실행하지 못한 평가 목록 또는 특정 App Service와 관련이 없는 권장 사항입니다.

   **권장 사항**에는 선택한 웹 애플리케이션에 대한 권장 사항 목록과 각 권장 사항의 심각도가 표시됩니다.

   ![App Services 권장 사항](./media/security-center-virtual-machine-recommendations/app-services-rec.png)

권장 사항을 선택하여 권장 사항에 대한 설명, 비정상 리소스, 정상 리소스, 검사되지 않은 리소스 목록을 확인합니다.

   - **통과된 평가** 열에는 통과된 평가 목록이 표시됩니다. 이러한 평가의 심각도는 항상 녹색입니다.

   - 평가에 대한 설명, 비정상 및 정상 리소스 목록 및 검사되지 않은 리소스 목록에서 통과된 평가를 선택합니다. 비정상 리소스에 대한 탭이 있지만 평가를 통과하기 때문에 이 목록은 항상 비어 있습니다.





### <a name="containers"></a><a name="containers"></a>컨테이너

**컨테이너** 탭을 열면 환경에 따라 다음 세 가지 유형의 리소스가 표시될 수 있습니다.

![컨테이너 호스트](./media/security-center-virtual-machine-recommendations/icon-container-host-rec.png) 컨테이너 호스트 - 도커를 실행하는 VM 

![Kubernetes 서비스](./media/security-center-virtual-machine-recommendations/icon-kubernetes-service-rec.png) Azure Kubernetes 서비스(AKS) 클러스터

![컨테이너 레지스트리](./media/security-center-virtual-machine-recommendations/icon-container-registry-rec.png) Azure 컨테이너 레지스트리(ACR) 레지스트리 - 표준 가격 책정 계층에 있을 때와 Azure 컨테이너 레지스트리 번들을 사용하도록 설정한 경우에만 표시됩니다.

컨테이너 보안 기능을 사용하는 방법에 대한 지침은 [컨테이너보안 모니터링을](monitor-container-security.md)참조하십시오.

Azure 컨테이너 레지스트리 번들의 이점은 [여기에](azure-container-registry-integration.md) 설명되어 있습니다.

Kubernetes 서비스 번들의 이점은 [여기에](azure-kubernetes-service-integration.md) 설명되어 있습니다.

[![컨테이너 탭](./media/security-center-virtual-machine-recommendations/container-recommendations-all-types.png)](./media/security-center-virtual-machine-recommendations/container-recommendations-all-types.png#lightbox)

목록에서 특정 리소스에 대한 권장 사항을 보려면 해당 리소스를 클릭합니다.

#### <a name="visibility-into-container-registries"></a>컨테이너 레지스트리에 대한 가시성

예를 들어 위의 그래픽에 표시된 목록에서 asc-demo ACR 레지스트리를 클릭하면 이 세부 정보 페이지로 연결됩니다.

[![특정 ACR 레지스트리에 대한 권장 사항](./media/security-center-virtual-machine-recommendations/acr-registry-recs-list.png)](./media/security-center-virtual-machine-recommendations/acr-registry-recs-list.png#lightbox)


#### <a name="visibility-into-containers-hosted-on-iaas-linux-machines"></a>IaaS Linux 컴퓨터에 호스트된 컨테이너 파악

Docker를 실행하는 VM 중 하나를 클릭하면 Docker 버전 및 호스트에서 실행되는 이미지 수와 같은 컴퓨터의 컨테이너와 관련된 정보가 포함된 세부 정보 페이지가 표시됩니다.

![VM 실행 중인 도커에 대한 권장 사항](./media/security-center-virtual-machine-recommendations/docker-recommendation.png)


#### <a name="security-recommendations-based-on-cis-benchmark-for-docker"></a>Docker의 CIS 벤치마크에 따른 보안 권장 사항

Security Center는 Docker 구성을 검색하고 평가된 모든 실패 규칙 목록을 제공하여 구성 오류를 표시합니다. Security Center는 이러한 문제를 신속하게 해결하고 시간을 절약하기 위한 지침을 제공합니다. Security Center는 Docker 구성을 지속적으로 평가하고 최신 상태를 제공합니다.

![컨테이너 탭](./media/security-center-virtual-machine-recommendations/container-cis-benchmark.png)


## <a name="next-steps"></a>다음 단계
다른 Azure 리소스 유형에 적용되는 권장 사항에 대해 자세히 알아보려면 다음 문서를 참조하세요.

* [Azure 보안 센터의 보안 권장 사항에 대한 전체 참조 목록](recommendations-reference.md)
* [Azure Security Center에서 ID 및 액세스 모니터링](security-center-identity-access.md)
* [Azure Security Center에서 네트워크 보호](security-center-network-recommendations.md)
* [Azure Security Center에서 Azure SQL 서비스 보호](security-center-sql-service-recommendations.md)