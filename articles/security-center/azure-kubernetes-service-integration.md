---
title: Azure Security Center 및 Azure Kubernetes 서비스
description: Azure Kubernetes Services와 Azure Security Center의 통합에 대해 알아봅니다.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/30/2020
ms.author: memildin
ms.openlocfilehash: 94d1bccc9a7f45d24d8c5b92aecba54d9f7f630a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85800183"
---
# <a name="azure-kubernetes-services-integration-with-security-center"></a>Security Center와 Azure Kubernetes Services 통합

AKS (Azure Kubernetes Service)는 컨테이너 화 된 응용 프로그램 개발, 배포 및 관리를 위한 Microsoft의 관리 되는 서비스입니다. 

Azure Security Center의 표준 계층에 있는 경우 AKS 번들 ( [가격 책정](security-center-pricing.md)참조)을 추가 하 여 AKS 노드, 클라우드 트래픽 및 보안 제어를 보다 깊이 있게 파악할 수 있습니다.

Security Center 및 AKS는 모두 최상의 클라우드 기본 Kubernetes 보안 제품을 형성 합니다.

## <a name="what-are-the-components-of-security-centers-kubernetes-protection"></a>Security Center Kubernetes 보호의 구성 요소는 무엇 인가요?

Kubernetes에 대 한 Security Center 보호는 두 가지 요소를 조합 하 여 제공 됩니다.

- **가상 컴퓨터에 대 한 Azure Security Center의 위협 방지** -Security Center 다른 vm에서 사용 하는 것과 동일한 Log Analytics 에이전트 Security Center를 사용 하 여 AKS 노드에서 발생 하는 보안 문제를 보여줄 수 있습니다. 또한 에이전트는 컨테이너 관련 분석을 모니터링 합니다.

- **Azure Security Center의 선택적 Kubernetes 번들** -Kubernetes 번들은 AKS 서비스를 통해 Kubernetes 하위 시스템에서 로그 및 정보를 받습니다. 이러한 로그는 AKS 서비스를 통해 Azure에서 이미 사용할 수 있습니다. Security Center의 Kubernetes 번들을 사용 하도록 설정 하는 경우 로그에 대 한 Security Center 액세스 권한을 부여 합니다. 따라서 Security Center AKS 마스터 노드에서 이미 수집 된 데이터를 사용 하 여 AKS 클러스터에 보안 이점을 제공 합니다. Kubernetes 환경에서 Azure Security Center 하 여 검색 된 데이터 중 일부에는 중요 한 정보가 포함 될 수 있습니다.

    ![Azure Security Center 및 AKS (Azure Kubernetes Service) 개략적인 개요](./media/azure-kubernetes-service-integration/aks-asc-integration-overview.png)

## <a name="what-protections-are-provided"></a>제공 되는 보호는 무엇 인가요?

두 서비스를 함께 사용 하면 다음과 같은 기능을 제공 합니다.

* **보안 권장 사항** SECURITY CENTER-AKS 리소스를 식별 하 고 클러스터에서 개별 가상 머신으로 분류 합니다. 그런 다음 리소스 별로 보안 권장 사항을 볼 수 있습니다. 자세한 내용은 [권장 사항 목록](recommendations-reference.md#recs-containers)에서 컨테이너 권장 사항을 참조 하세요. 

* **환경 강화** -Security Center Kubernetes 클러스터 및 Docker 구성의 구성을 지속적으로 모니터링 합니다. 그런 다음 업계 표준을 반영 하는 보안 권장 사항을 생성 합니다.

* **런타임 보호** -다음 AKS 원본에 대 한 지속적인 분석 Security Center을 통해 호스트 *및* AKS 클러스터 수준에서 검색 된 위협 및 악의적인 활동을 경고 합니다. [컨테이너에 대 한 위협 방지에 대해 자세히 알아보세요](threat-protection.md#azure-containers).


     

![Azure Security Center 및 AKS (Azure Kubernetes Service)에 대 한 자세한 정보](./media/azure-kubernetes-service-integration/aks-asc-integration-detailed.png)



## <a name="aks-with-security-center-faq"></a>Security Center FAQ로 AKS

### <a name="can-i-still-get-aks-protections-without-the-log-analytics-agent"></a>Log Analytics 에이전트가 없으면 여전히 AKS 보호를 받을 수 있나요?

위에서 설명한 것 처럼 선택적 Kubernetes 번들은 클러스터 수준에서 보호를 제공 하며, Azure Security Center 표준 계층의 Log Analytics 에이전트는 노드를 보호 합니다. 

가능 하면 가장 완전 한 보호를 위해 둘 다 배포 하는 것이 좋습니다.

호스트에 에이전트를 설치 하지 않도록 선택 하는 경우 위협 방지 혜택 및 보안 경고의 하위 집합만 받게 됩니다. 네트워크 분석 및 악의적인 서버와의 통신과 관련된 경고는 계속 받을 수 있습니다.



## <a name="next-steps"></a>다음 단계

Security Center의 컨테이너 보안 기능에 대해 자세히 알아보려면 다음을 참조 하세요.

* [Azure Security Center 및 컨테이너 보안](container-security.md)

* [Azure Container Registry와 통합](azure-container-registry-integration.md)

* [Microsoft의 데이터 관리](https://www.microsoft.com/trust-center/privacy/data-management) -microsoft 서비스의 데이터 정책 (Azure, Intune 및 Microsoft 365 포함), microsoft 데이터 관리의 세부 정보 및 데이터에 영향을 주는 보존 정책에 대해 설명 합니다.
