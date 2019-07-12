---
title: 할당량, Sku 및 Azure Kubernetes Service (AKS)에서 지역 가용성
description: 기본 할당량, 제한 된 노드 VM SKU 크기 및 Azure Kubernetes Service (AKS)의 지역 가용성에 알아봅니다.
services: container-service
author: mlearned
ms.service: container-service
ms.topic: conceptual
ms.date: 04/09/2019
ms.author: mlearned
ms.openlocfilehash: 318846cddecdf020e2e751d3a0b9e05fc83bba73
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/07/2019
ms.locfileid: "67614545"
---
# <a name="quotas-virtual-machine-size-restrictions-and-region-availability-in-azure-kubernetes-service-aks"></a>할당량, 가상 머신 크기 제한 및 Azure Kubernetes Service (AKS)에서 지역 가용성

모든 Azure 서비스는 기본 제한 및 할당량 리소스 및 기능을 설정합니다. 특정 가상 머신 (VM) Sku 사용할 수 있도록 제한 됩니다.

이 문서에서는 Azure Kubernetes Service (AKS) 리소스 및 Azure 지역의 AKS의 가용성에 대 한 기본 리소스 제한과 자세히 설명합니다.

## <a name="service-quotas-and-limits"></a>서비스 할당량 및 제한

[!INCLUDE [container-service-limits](../../includes/container-service-limits.md)]

## <a name="provisioned-infrastructure"></a>프로비전된 인프라

다른 모든 네트워크, 계산 및 스토리지 제한은 프로비전된 인프라에 적용됩니다. 와 관련 된 제한을 참조 하세요 [Azure 구독 및 서비스 제한](../azure-subscription-service-limits.md)합니다.

> [!IMPORTANT]
> AKS 클러스터를 업그레이드 하는 경우 추가 리소스 일시적으로 사용 됩니다. 이러한 리소스는 가상 네트워크 서브넷 또는 가상 머신 vCPU 할당량에서 사용 가능한 IP 주소가 포함 됩니다. Windows Server 컨테이너를 사용 하 여 (현재 AKS에서 미리 보기)는에서 노드에 최신 업데이트를 적용 하는 유일한 인증된 방법은 경우 업그레이드 작업을 수행 합니다. 실패 한 클러스터 업그레이드 프로세스는 사용 가능한 IP 주소 공간 또는 vCPU 할당량 이러한 임시 리소스를 처리할 수 없다는 것을 나타낼 수 있습니다. Windows Server 노드 업그레이드 프로세스에 대 한 자세한 내용은 참조 하세요. [AKS에 노드 풀을 업그레이드][nodepool-upgrade]합니다.

## <a name="restricted-vm-sizes"></a>제한 된 VM 크기

AKS 클러스터의 각 노드는 고정된 된 양의 vCPU 및 메모리와 같은 계산 리소스를 포함합니다. AKS 노드 부족 하 여 계산 리소스에 pod 올바르게 실행에 실패할 수 있습니다. 필요한 되도록 *kube 시스템* pod 및 응용 프로그램 안정적으로 예약할 수 있습니다, AKS에 다음 VM Sku를 사용 하지 않습니다.

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

특정 기본 제한 및 할당량은 증대가 가능합니다. 리소스 증가 지 원하는 경우 통해 증가 요청는 [Azure 지원 요청][azure-support] (에 대 한 **문제 유형**를 선택 **할당량**).

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
[region-availability]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service

<!-- LINKS - Internal -->
[vm-skus]: ../virtual-machines/linux/sizes.md
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
