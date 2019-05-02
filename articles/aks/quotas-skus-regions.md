---
title: 할당량, SKU 및 Azure Kubernetes Service (AKS)에서 지역 가용성
description: 기본 할당량, 제한 된 노드 VM SKU 크기 및 Azure Kubernetes Service (AKS)의 지역 가용성에 알아봅니다.
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: conceptual
ms.date: 04/09/2019
ms.author: iainfou
ms.openlocfilehash: abeb9ef6e467b62cf7332e01e1b77c710b9ba4f4
ms.sourcegitcommit: 524625dd12e0537173616a991802075e2dc9da12
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/24/2019
ms.locfileid: "64413011"
---
# <a name="quotas-virtual-machine-size-restrictions-and-region-availability-in-azure-kubernetes-service-aks"></a>할당량, 가상 머신 크기 제한 및 Azure Kubernetes Service (AKS)에서 지역 가용성

모든 Azure 서비스에는 리소스와 기능에 대한 특정 기본 제한과 할당량이 있습니다. 노드 크기에 대 한 특정 가상 머신 (VM)는 Sku는 다음 사용 하도록 제한 합니다.

이 문서에서는 Azure Kubernetes Service (AKS) 리소스 뿐만 아니라 Azure 지역의 AKS 서비스 가용성에 대 한 기본 리소스 제한과 자세히 설명합니다.

## <a name="service-quotas-and-limits"></a>서비스 할당량 및 제한

[!INCLUDE [container-service-limits](../../includes/container-service-limits.md)]

## <a name="provisioned-infrastructure"></a>프로비전된 인프라

다른 모든 네트워크, 계산 및 스토리지 제한은 프로비전된 인프라에 적용됩니다. 관련 제한은 [Azure 구독 및 서비스 제한](../azure-subscription-service-limits.md)을 참조하세요.

## <a name="restricted-vm-sizes"></a>제한 된 VM 크기

AKS 클러스터의 각 노드는 고정된 된 양의 vCPU 및 메모리와 같은 계산 리소스를 포함합니다. AKS 노드 부족 하 여 계산 리소스에 pod 올바르게 실행에 실패할 수 있습니다. 필요한 되도록 *kube 시스템* pod 및 응용 프로그램 안정적으로 예약할 수 있습니다, AKS에 다음 VM Sku를 사용할 수 없습니다.

- Standard_A0
- Standard_A1
- Standard_A1_v2
- Standard_B1s
- Standard_B1ms
- Standard_F1
- Standard_F1s

VM 유형과 해당 계산 리소스에 대 한 자세한 내용은 참조 하세요. [Azure에서 가상 머신 크기][vm-skus]합니다.

## <a name="region-availability"></a>지역 가용성

위치의 최신 목록은 있습니다를 배포할 수 및 클러스터 실행 참조 [AKS 지역 가용성][region-availability]합니다.

## <a name="next-steps"></a>다음 단계

특정 기본 제한 및 할당량은 증대가 가능합니다. 이러한 증대를 지원하는 하나 이상의 리소스에 대해 증대를 요청하려면 [Azure 지원 요청][azure-support] (**발급 요청**에 대해 "할당량" 선택)을 제출합니다.

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
[region-availability]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service

<!-- LINKS - Internal -->
[vm-skus]: ../virtual-machines/linux/sizes.md
