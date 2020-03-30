---
title: Azure 보안 센터 및 Azure 컨테이너 레지스트리
description: Azure 보안 센터와 Azure 컨테이너 레지스트리의 통합에 대해 자세히 알아보기
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/19/2019
ms.author: memildin
ms.openlocfilehash: 069ce6ca1e76a9bd954031708702c973387abbaa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78205996"
---
# <a name="azure-container-registry-integration-with-security-center"></a>보안 센터와의 Azure 컨테이너 레지스트리 통합

Azure 컨테이너 레지스트리(ACR)는 중앙 레지스트리에서 Azure 배포에 대한 컨테이너 이미지를 저장하고 관리하는 관리되는 개인 Docker 레지스트리 서비스입니다. 그것은 오픈 소스 Docker 레지스트리를 기반으로 2.0.

Azure 보안 센터의 표준 계층에 있는 경우 컨테이너 레지스트리 번들을 추가할 수 있습니다. 이 선택적 기능은 ARM 기반 레지스트리의 이미지 취약성에 대한 심층적인 가시성을 제공합니다. 구독 수준에서 번들을 활성화하거나 비활성화하여 구독의 모든 레지스트리를 포함합니다. 이 기능은 [가격 책정 페이지에](security-center-pricing.md)표시된 대로 이미지당 요금이 부과됩니다. 컨테이너 레지스트리 번들을 활성화하면 보안 센터에서 레지스트리로 푸시되는 이미지를 검색할 준비가 되었는지 확인합니다. 

이미지가 레지스트리로 푸시될 때마다 보안 센터는 해당 이미지를 자동으로 검사합니다. 이미지 스캔을 트리거하려면 리포지토리로 푸시합니다.

스캔이 완료되면(일반적으로 약 10분 후) 다음과 같은 보안 센터 권장 사항에서 결과를 확인할 수 있습니다.

[![Azure 컨테이너 레지스트리(ACR) 호스팅 이미지에서 발견된 취약점에 대한 Azure 보안 센터 권장 사항 샘플](media/azure-container-registry-integration/container-security-acr-page.png)](media/azure-container-registry-integration/container-security-acr-page.png#lightbox)

## <a name="benefits-of-integration"></a>통합의 이점

보안 센터는 구독에서 ARM 기반 ACR 레지스트리를 식별하고 다음을 원활하게 제공합니다.

* 푸시된 모든 Linux 이미지에 대한 **Azure 네이티브 취약성 검색.** 보안 센터는 업계 최고의 취약점 검색 공급업체인 Qualys의 스캐너를 사용하여 이미지를 스캔합니다. 이 기본 솔루션은 기본적으로 원활하게 통합됩니다.

* 알려진 취약점이 있는 Linux 이미지에 대한 **보안 권장 사항입니다.** 보안 센터는 보고된 각 취약점과 심각도 분류에 대한 세부 정보를 제공합니다. 또한 레지스트리에 푸시된 각 이미지에서 발견된 특정 취약점을 해결하는 방법에 대한 지침을 제공합니다.

![Azure 보안 센터 및 Azure 컨테이너 레지스트리(ACR) 상위 개요](./media/azure-container-registry-integration/aks-acr-integration-detailed.png)

## <a name="next-steps"></a>다음 단계

보안 센터의 컨테이너 보안 기능에 대해 자세히 알아보려면 다음을 참조하십시오.

* [Azure 보안 센터 및 컨테이너 보안](container-security.md)

* [Azure Kubernetes Service와 통합](azure-kubernetes-service-integration.md)

* [가상 시스템 보호](security-center-virtual-machine-protection.md) - 보안 센터의 권장 사항 설명
