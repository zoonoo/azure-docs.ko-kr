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
ms.date: 11/04/2019
ms.author: memildin
ms.openlocfilehash: d5c74ac760b5746d37468d692c2a46d5aedbee72
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "80436172"
---
# <a name="azure-kubernetes-services-integration-with-security-center"></a>Security Center와 Azure Kubernetes Services 통합

AKS (Azure Kubernetes Service)는 컨테이너 화 된 응용 프로그램 개발, 배포 및 관리를 위한 Microsoft의 관리 되는 서비스입니다. 

AKS를 Azure Security Center의 표준 계층 ( [가격 책정](security-center-pricing.md)참조)과 함께 사용 하 여 AKS 노드, 클라우드 트래픽 및 보안 제어에 대해 깊이 있게 파악할 수 있습니다.

Security Center AKS 마스터 노드에서 이미 수집 된 데이터를 사용 하 여 AKS 클러스터에 보안 이점을 제공 합니다. 

![Azure Security Center 및 AKS (Azure Kubernetes Service) 개략적인 개요](./media/azure-kubernetes-service-integration/aks-asc-integration-overview.png)

이러한 두 도구는 모두 최상의 클라우드 네이티브 Kubernetes 보안 제품을 형성 합니다. 

## <a name="benefits-of-integration"></a>통합의 이점

두 서비스를 함께 사용 하면 다음과 같은 기능을 제공 합니다.

* **보안 권장 사항** SECURITY CENTER-AKS 리소스를 식별 하 고 클러스터에서 개별 가상 머신으로 분류 합니다. 그런 다음 리소스 별로 보안 권장 사항을 볼 수 있습니다. 자세한 내용은 [권장 사항 목록](recommendations-reference.md#recs-containers)에서 컨테이너 권장 사항을 참조 하세요. 

* **환경 강화** -Security Center Kubernetes 클러스터 및 Docker 구성의 구성을 지속적으로 모니터링 합니다. 그런 다음 업계 표준을 반영 하는 보안 권장 사항을 생성 합니다.

* **런타임 보호** -다음 AKS 원본에 대 한 지속적인 분석 Security Center을 통해 호스트 *및* AKS 클러스터 수준에서 검색 된 위협 및 악의적인 활동을 경고 합니다.
    * 네트워크 데이터, 프로세스 만들기 등의 원시 보안 이벤트
    * Kubernetes 감사 로그

    자세한 내용은 [Azure 컨테이너에 대 한 위협 방지](threat-protection.md#azure-containers) 를 참조 하세요.

    가능한 경고 목록은 경고 참조 테이블에서 [AKS 클러스터 수준 경고](alerts-reference.md#alerts-akscluster) 및 [컨테이너 호스트 수준 경고](alerts-reference.md#alerts-containerhost)섹션을 참조 하십시오.  

![Azure Security Center 및 AKS (Azure Kubernetes Service)에 대 한 자세한 정보](./media/azure-kubernetes-service-integration/aks-asc-integration-detailed.png)

> [!NOTE]
> Kubernetes 환경에서 Azure Security Center 하 여 검색 된 데이터 중 일부에는 중요 한 정보가 포함 될 수 있습니다.


## <a name="next-steps"></a>다음 단계

Security Center의 컨테이너 보안 기능에 대해 자세히 알아보려면 다음을 참조 하세요.

* [Azure Security Center 및 컨테이너 보안](container-security.md)

* [Azure Container Registry와 통합](azure-container-registry-integration.md)

* [Microsoft의 데이터 관리](https://www.microsoft.com/trust-center/privacy/data-management) -microsoft 서비스의 데이터 정책 (Azure, Intune 및 Office 365 포함), microsoft 데이터 관리의 세부 정보 및 데이터에 영향을 주는 보존 정책에 대해 설명 합니다.
