---
title: 리소스, SKU, 지역에 대한 한도
titleSuffix: Azure Kubernetes Service
description: AKS(Azure Kubernetes Service)의 기본 할당량, 제한된 노드 VM SKU 크기, 지역 가용성에 대해 알아봅니다.
services: container-service
ms.topic: conceptual
ms.date: 03/25/2021
ms.openlocfilehash: 3e1e74834153584525d2093d2a1bb8ba8e991e5a
ms.sourcegitcommit: 6ed3928efe4734513bad388737dd6d27c4c602fd
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/07/2021
ms.locfileid: "107011467"
---
# <a name="quotas-virtual-machine-size-restrictions-and-region-availability-in-azure-kubernetes-service-aks"></a>AKS(Azure Kubernetes Service)의 할당량, 가상 머신 크기 제한 및 지역 가용성

모든 Azure 서비스는 특정 VM(가상 머신) SKU에 대한 사용 제한을 포함하여 리소스 및 기능에 대한 기본 한도 및 할당량을 설정합니다.

이 문서는 AKS(Azure Kubernetes Service)의 기본 리소스 한도와 Azure 지역의 Azure 가용성에 대해 자세히 설명합니다.

## <a name="service-quotas-and-limits"></a>서비스 할당량 및 제한

[!INCLUDE [container-service-limits](../../includes/container-service-limits.md)]

## <a name="provisioned-infrastructure"></a>프로비전된 인프라

다른 모든 네트워크, 컴퓨팅 및 스토리지 제한은 프로비전된 인프라에 적용됩니다. 관련 한도는 [Azure 구독 및 서비스 한도](../azure-resource-manager/management/azure-subscription-service-limits.md)를 참조하세요.

> [!IMPORTANT]
> AKS 클러스터를 업그레이드하는 경우 추가 리소스가 일시적으로 사용됩니다. 이러한 리소스에는 가상 네트워크 서브넷 또는 가상 머신 vCPU 할당량에서 사용 가능한 IP 주소가 포함됩니다. 
>
> Windows Server 컨테이너의 경우 업그레이드 작업을 수행하여 최신 노드 업데이트를 적용할 수 있습니다. 이러한 임시 리소스 처리에 사용할 수 있는 IP 주소 공간 또는 vCPU 할당량이 없으면 클러스터 업그레이드 프로세스가 실패합니다. Windows Server 노드 업그레이드 프로세스에 대한 자세한 내용은 [AKS의 노드 풀 업그레이드][nodepool-upgrade]를 참조하세요.

## <a name="restricted-vm-sizes"></a>제한된 VM 크기

AKS 클러스터의 각 노드에는 vCPU 및 메모리 같은 고정된 양의 컴퓨팅 리소스가 포함되어 있습니다. AKS 노드에 컴퓨팅 리소스가 부족한 경우 Pod가 제대로 실행되지 않을 수 있습니다. 필수 *kube-system* Pod 및 애플리케이션을 안정적으로 예약할 수 있도록 하려면 **AKS에서 다음 VM SKU를 사용하지 마세요**.

- Standard_A0
- Standard_A1
- Standard_A1_v2
- Standard_B1s
- Standard_B1ms
- Standard_F1
- Standard_F1s

VM 유형 및 해당 컴퓨팅 리소스에 대한 자세한 내용은 [Azure의 가상 머신 크기][vm-skus]를 참조하세요.

## <a name="region-availability"></a>지역 가용성

클러스터를 배포하고 실행할 수 있는 위치에 대한 최신 목록은 [AKS 지역 가용성][region-availability]을 참조하세요.

## <a name="next-steps"></a>다음 단계

특정 기본 한도 및 할당량을 늘릴 수 있습니다. 리소스에서 증가를 지원하는 경우 [Azure 지원 요청][azure-support]을 통해 증가를 요청합니다(**문제 유형** 에 대해 **할당량** 선택).

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
[region-availability]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service

<!-- LINKS - Internal -->
[vm-skus]: ../virtual-machines/sizes.md
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
