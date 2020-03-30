---
title: Azure Kubernetes 서비스(AKS)의 할당량, SUS 및 지역 가용성
description: 기본 할당량, 제한된 노드 VM SKU 크기 및 AZURE Kubernetes 서비스(AKS)의 지역 가용성에 대해 알아봅니다.
services: container-service
ms.topic: conceptual
ms.date: 04/09/2019
ms.openlocfilehash: 03e7396932f0813ef4bd00d644dcdaddfe229e6a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79252832"
---
# <a name="quotas-virtual-machine-size-restrictions-and-region-availability-in-azure-kubernetes-service-aks"></a>Azure Kubernetes 서비스(AKS)의 할당량, 가상 시스템 크기 제한 및 지역 가용성

모든 Azure 서비스는 리소스 및 기능에 대한 기본 제한 및 할당량을 설정합니다. 특정 가상 컴퓨터(VM) SCO도 사용할 수 없습니다.

이 문서에서는 AKS(Azure Kubernetes) 리소스에 대한 기본 리소스 제한과 Azure 리전에서 AKS의 가용성에 대해 자세히 설명합니다.

## <a name="service-quotas-and-limits"></a>서비스 할당량 및 제한

[!INCLUDE [container-service-limits](../../includes/container-service-limits.md)]

## <a name="provisioned-infrastructure"></a>프로비전된 인프라

다른 모든 네트워크, 컴퓨팅 및 스토리지 제한은 프로비전된 인프라에 적용됩니다. 관련 한도는 [Azure 구독 및 서비스 제한을](../azure-resource-manager/management/azure-subscription-service-limits.md)참조하십시오.

> [!IMPORTANT]
> AKS 클러스터를 업그레이드하면 추가 리소스가 일시적으로 사용됩니다. 이러한 리소스에는 가상 네트워크 서브넷또는 가상 컴퓨터 vCPU 할당량에서 사용 가능한 IP 주소가 포함됩니다. 현재 AKS에서 미리 보기 중인 Windows Server 컨테이너를 사용하는 경우 노드에 최신 업데이트를 적용하는 유일한 승인된 방법은 업그레이드 작업을 수행하는 것입니다. 실패한 클러스터 업그레이드 프로세스는 이러한 임시 리소스를 처리하기 위해 사용 가능한 IP 주소 공간이나 vCPU 할당량이 없음을 나타낼 수 있습니다. Windows Server 노드 업그레이드 프로세스에 대한 자세한 내용은 [AKS의 노드 풀 업그레이드를][nodepool-upgrade]참조하십시오.

## <a name="restricted-vm-sizes"></a>제한된 VM 크기

AKS 클러스터의 각 노드에는 vCPU 및 메모리와 같은 고정된 양의 계산 리소스가 포함되어 있습니다. AKS 노드에 계산 리소스가 부족한 경우 포드가 올바르게 실행되지 않을 수 있습니다. 필요한 *kube 시스템* 포드와 응용 프로그램을 안정적으로 예약할 수 있도록 **하려면 AKS에서 다음 VM SKU를 사용하지 마십시오.**

- Standard_A0
- Standard_A1
- Standard_A1_v2
- Standard_B1s
- Standard_B1ms
- Standard_F1
- Standard_F1s

VM 형식 및 해당 계산 리소스에 대한 자세한 내용은 [Azure의 가상 컴퓨터 크기][vm-skus]를 참조하십시오.

## <a name="region-availability"></a>지역 가용성

클러스터를 배포하고 실행할 수 있는 최신 목록은 [AKS 지역 가용성을][region-availability]참조하십시오.

## <a name="next-steps"></a>다음 단계

특정 기본 제한 및 할당량은 증대가 가능합니다. 리소스가 증가를 지원하는 경우 [Azure 지원 요청(문제][azure-support] **유형,** **할당량**선택)을 통해 증가를 요청합니다.

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
[region-availability]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service

<!-- LINKS - Internal -->
[vm-skus]: ../virtual-machines/linux/sizes.md
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
