---
title: Azure 보안 센터 및 Azure Kubernetes 서비스
description: Azure Kubernetes 서비스와 Azure 보안 센터의 통합에 대해 알아보기
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/04/2019
ms.author: memildin
ms.openlocfilehash: d1cd4691586b27282d221a19c5fb7a1af034ed6e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80125154"
---
# <a name="azure-kubernetes-services-integration-with-security-center"></a>Azure Kubernetes 서비스 보안 센터와의 통합

Azure Kubernetes 서비스(AKS)는 컨테이너화된 응용 프로그램을 개발, 배포 및 관리하기 위한 Microsoft의 관리 형 서비스입니다. 

Azure Security Center의 표준 [계층(가격 책정](security-center-pricing.md)참조)과 함께 AKS를 사용하여 AKS 노드, 클라우드 트래픽 및 보안 제어에 대한 심층적인 가시성을 확보합니다.

보안 센터는 AKS 마스터 노드에서 이미 수집한 데이터를 사용하여 AKS 클러스터에 보안 이점을 제공합니다. 

![Azure 보안 센터 및 AZURE Kubernetes 서비스(AKS) 상위 개요](./media/azure-kubernetes-service-integration/aks-asc-integration-overview.png)

이 두 도구는 함께 최고의 클라우드 네이티브 Kubernetes 보안 오퍼링을 형성합니다. 

## <a name="benefits-of-integration"></a>통합의 이점

두 서비스를 함께 사용하면 다음을 수행할 수 있습니다.

* **보안 권장 사항** - 보안 센터는 AKS 리소스를 식별하고 클러스터에서 개별 가상 머신에 이르기까지 분류합니다. 그런 다음 리소스당 보안 권장 사항을 볼 수 있습니다. 자세한 내용은 참조 권장 사항 [목록에서](recommendations-reference.md#recs-computeapp)컨테이너 권장 사항을 참조하십시오. 

* **환경 강화** - 보안 센터는 Kubernetes 클러스터 및 Docker 구성의 구성을 지속적으로 모니터링합니다. 그런 다음 업계 표준을 반영하는 보안 권장 사항을 생성합니다.

* **런타임 보호** - 다음 AKS 소스를 지속적으로 분석하여 보안 센터는 호스트 *및* AKS 클러스터 수준에서 탐지된 위협 및 악의적인 활동을 경고합니다.
    * 네트워크 데이터 및 프로세스 생성과 같은 원시 보안 이벤트
    * Kubernetes 감사 로그

    자세한 내용은 [Azure 컨테이너에 대한 위협 보호를](threat-protection.md#azure-containers) 참조하세요.

    가능한 경고 목록은 경고 참조 테이블의 다음 섹션인 [AKS 클러스터 수준 경고](alerts-reference.md#alerts-akscluster) 및 [컨테이너 호스트 수준 경고를](alerts-reference.md#alerts-containerhost)참조하십시오.  

![Azure 보안 센터 및 AZURE Kubernetes 서비스(AKS) 자세한 내용](./media/azure-kubernetes-service-integration/aks-asc-integration-detailed.png)

> [!NOTE]
> Kubernetes 환경에서 Azure 보안 센터에서 검색한 일부 데이터에는 중요한 정보가 포함될 수 있습니다.


## <a name="next-steps"></a>다음 단계

보안 센터의 컨테이너 보안 기능에 대해 자세히 알아보려면 다음을 참조하십시오.

* [Azure 보안 센터 및 컨테이너 보안](container-security.md)

* [Azure Container Registry와 통합](azure-container-registry-integration.md)

* [Microsoft의 데이터 관리](https://www.microsoft.com/trust-center/privacy/data-management) - Azure, Intune 및 Office 365를 포함한 Microsoft 서비스의 데이터 정책, Microsoft 데이터 관리 세부 정보 및 데이터에 영향을 주는 보존 정책에 대해 설명합니다.