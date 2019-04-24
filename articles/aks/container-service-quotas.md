---
title: AKS(Azure Kubernetes Service)의 할당량 및 지역 가용성
description: AKS(Azure Kubernetes Service)의 기본 할당량 및 지역 가용성을 다룹니다.
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: conceptual
ms.date: 04/01/2019
ms.author: iainfou
ms.openlocfilehash: 8feeaf2e8ee99405ed0de8291fc97dc50db6a386
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60466586"
---
# <a name="quotas-and-region-availability-for-azure-kubernetes-service-aks"></a>AKS(Azure Kubernetes Service)의 할당량 및 지역 가용성

모든 Azure 서비스에는 리소스와 기능에 대한 특정 기본 제한과 할당량이 있습니다. 다음 섹션에서는 여러 AKS(Azure Kubernetes Service) 리소스의 기본 리소스 제한과, Azure 지역의 AKS 서비스 가용성에 대해 자세히 설명합니다.

## <a name="service-quotas-and-limits"></a>서비스 할당량 및 제한

[!INCLUDE [container-service-limits](../../includes/container-service-limits.md)]

## <a name="provisioned-infrastructure"></a>프로비전된 인프라

다른 모든 네트워크, 계산 및 스토리지 제한은 프로비전된 인프라에 적용됩니다. 관련 제한은 [Azure 구독 및 서비스 제한](../azure-subscription-service-limits.md)을 참조하세요.

## <a name="region-availability"></a>지역 가용성

AKS(Azure Kubernetes Service)는 다음 지역에서 제공됩니다.

- 오스트레일리아 동부
- 오스트레일리아 남동부
- 캐나다 중부
- 캐나다 동부
- 인도 중부
- 미국 중부
- 동아시아
- 미국 동부
- 미국 동부2
- 프랑스 중부
- 일본 동부
- 한국 중부
- 한국 남부
- 북유럽
- 동남아시아
- 미국 중남부
- 인도 남부
- 영국 남부
- 영국 서부
- 서유럽
- 미국 서부
- 미국 서부 2

## <a name="next-steps"></a>다음 단계

특정 기본 제한 및 할당량은 증대가 가능합니다. 이러한 증대를 지원하는 하나 이상의 리소스에 대해 증대를 요청하려면 [Azure 지원 요청][azure-support] (**발급 요청**에 대해 "할당량" 선택)을 제출합니다.

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
