---
title: Azure Security Center 및 Azure Kubernetes 서비스 | Microsoft Docs
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
ms.openlocfilehash: 4fc0654aab1c68888e86dc95de658bc69a01e02d
ms.sourcegitcommit: 5a8c65d7420daee9667660d560be9d77fa93e9c9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/15/2019
ms.locfileid: "74123986"
---
# <a name="azure-kubernetes-services-integration-with-security-center-preview"></a>Security Center와 Azure Kubernetes Services 통합 (미리 보기)
AKS (Azure Kubernetes Service)는 컨테이너 화 된 응용 프로그램 개발, 배포 및 관리를 위한 Microsoft의 관리 되는 서비스입니다. 

AKS를 Azure Security Center의 표준 계층 ( [가격 책정](security-center-pricing.md)참조)과 함께 사용 하 여 AKS 노드, 클라우드 트래픽 및 보안 제어에 대해 깊이 있게 파악할 수 있습니다.

Security Center AKS 마스터 노드에서 이미 수집 된 데이터를 사용 하 여 AKS 클러스터에 보안 이점을 제공 합니다. 

![Azure Security Center 및 AKS (Azure Kubernetes Service) 개략적인 개요](./media/azure-kubernetes-service-integration/aks-asc-integration-overview.png)

이러한 두 도구는 모두 최상의 클라우드 네이티브 Kubernetes 보안 제품을 형성 합니다. 

## <a name="benefits-of-integration"></a>통합의 이점

두 서비스를 함께 사용 하면 다음과 같은 기능을 제공 합니다.

* **보안 권장 사항** SECURITY CENTER-AKS 리소스를 식별 하 고 클러스터에서 개별 가상 머신으로 분류 합니다. 그런 다음 리소스 별로 보안 권장 사항을 볼 수 있습니다. 자세한 내용은 [보안 권장 사항을 구현 하는 방법](security-center-recommendations.md)을 참조 하세요. 

    > [!NOTE]
    > Security Center 권장 사항의 이름이 "(Preview)" 태그로 끝나는 경우 권장 사항의 미리 보기 특성을 참조 합니다. 기능이 아닙니다.

* **환경 강화** -Security Center Kubernetes 클러스터의 구성을 지속적으로 모니터링 하 고 업계 표준을 반영 하는 보안 권장 사항을 생성 합니다.

* **런타임 보호** -다음 AKS 원본에 대 한 지속적인 분석 Security Center을 통해 호스트 *및* AKS 클러스터 수준에서 검색 된 위협 및 악의적인 활동을 경고 합니다. 자세한 내용은 [Azure container service를 참조 하세요. ](https://docs.microsoft.com/azure/security-center/security-center-alerts-compute#azure-containers-)):
    * 네트워크 데이터, 프로세스 만들기 등의 원시 보안 이벤트
    * Kubernetes 감사 로그

![Azure Security Center 및 AKS (Azure Kubernetes Service)에 대 한 자세한 정보](./media/azure-kubernetes-service-integration/aks-asc-integration-detailed.png)

> [!NOTE]
> Kubernetes 환경에서 Azure Security Center 하 여 검색 된 데이터 중 일부에는 중요 한 정보가 포함 될 수 있습니다.

## <a name="next-steps"></a>다음 단계

Security Center의 컨테이너 보안 기능에 대해 자세히 알아보려면 다음을 참조 하세요.

* [Azure Security Center 및 컨테이너 보안](container-security.md)

* [Azure Container Registry와 통합](azure-container-registry-integration.md)

* [가상 컴퓨터 보호](security-center-virtual-machine-protection.md) -Security Center 권장 사항을 설명 합니다.

* [Microsoft의 데이터 관리](https://www.microsoft.com/trust-center/privacy/data-management) -microsoft 서비스의 데이터 정책 (Azure, Intune 및 Office 365 포함), microsoft 데이터 관리의 세부 정보 및 데이터에 영향을 주는 보존 정책에 대해 설명 합니다.