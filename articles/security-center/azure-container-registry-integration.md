---
title: Azure Security Center 및 Azure Container Registry | Microsoft Docs
description: Azure Container Registry와 Azure Security Center의 통합에 대해 알아봅니다.
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
ms.openlocfilehash: f0de56f968488f0e5d551ad705cc6f8ca6e7bc47
ms.sourcegitcommit: 3f8017692169bd75483eefa96c225d45cd497f06
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73521866"
---
# <a name="azure-container-registry-integration-with-security-center-preview"></a>Security Center와 Azure Container Registry 통합 (미리 보기)

ACR (Azure Container Registry)는 중앙 레지스트리에서 Azure 배포에 대 한 컨테이너 이미지를 저장 하 고 관리 하는 관리 되는 개인 Docker 레지스트리 서비스입니다. 오픈 소스 Docker 레지스트리 2.0을 기반으로 합니다.

Azure Security Center의 표준 계층과 함께 ACR를 사용 하는 경우 ( [가격 책정](security-center-pricing.md)참조) 레지스트리 및 이미지의 취약성을 보다 깊이 있게 파악할 수 있습니다.

[Azure Security Center 내의 ACR (![Azure Container Registry) 권장 사항](media/azure-container-registry-integration/container-security-acr-page.png)](media/azure-container-registry-integration/container-security-acr-page.png#lightbox)

## <a name="benefits-of-integration"></a>통합의 이점

Security Center는 구독에서 ACR 레지스트리를 식별 하 고 원활 하 게 제공 합니다.

* 푸시된 모든 Linux 이미지에 대 한 **Azure native 취약성 검색** Security Center 업계 최고의 취약점 검색 공급 업체 (Qualys)의 스캐너를 사용 하 여 이미지를 검색 합니다. 이 네이티브 솔루션은 기본적으로 원활 하 게 통합 됩니다.

* 알려진 취약성을 포함 하는 Linux 이미지에 대 한 **보안 권장 사항** Security Center 보고 된 각 취약점 및 심각도 분류의 세부 정보를 제공 합니다. 또한 레지스트리에 푸시되는 각 이미지에 있는 특정 취약점을 수정 하는 방법에 대 한 지침을 제공 합니다.

![ACR (Azure Security Center 및 Azure Container Registry) 개략적인 개요](./media/azure-container-registry-integration/aks-acr-integration-detailed.png)

## <a name="next-steps"></a>다음 단계

Security Center의 컨테이너 보안 기능에 대해 자세히 알아보려면 다음을 참조 하세요.

* [Azure Security Center 및 컨테이너 보안](container-security.md)

* [Azure Kubernetes Service와 통합](azure-kubernetes-service-integration.md)

* [가상 컴퓨터 보호](security-center-virtual-machine-protection.md) -Security Center 권장 사항을 설명 합니다.