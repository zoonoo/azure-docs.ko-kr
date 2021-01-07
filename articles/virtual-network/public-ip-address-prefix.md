---
title: Azure 공용 IP 주소 접두사
description: Azure 공용 IP 주소 접두사가 무엇인지 예측 가능한 공용 IP 주소를 리소스에 할당하는 데 어떻게 도움이 되는지 알아봅니다.
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.subservice: ip-services
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/29/2020
ms.author: allensu
ms.openlocfilehash: 53dd6d2dda762b3cbf53f4aaec6cd3692a9656e9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "87432576"
---
# <a name="public-ip-address-prefix"></a>공용 IP 주소 접두사

공용 IP 주소 접두사는 Azure에서 예약 된 IP 주소 범위입니다. Azure는 사용자가 지정 하는 수에 따라 구독에 연속적인 주소 범위를 제공 합니다. 

공용 주소에 대해 잘 모르는 경우 [공용 IP 주소](virtual-network-ip-addresses-overview-arm.md#public-ip-addresses)를 참조하세요.

공용 IP 주소는 각 Azure 지역의 주소 풀에서 할당됩니다. Azure에서 각 지역에 사용하는 범위 목록을 [다운로드](https://www.microsoft.com/download/details.aspx?id=56519)할 수 있습니다. 예를 들어 40.121.0.0/16은 Azure에서 미국 동부 지역에 사용되는 100개가 넘는 범위 중 하나입니다. 범위에는 사용 가능한 주소(40.121.0.1 - 40.121.255.254)가 포함됩니다.

이름을 지정하고 접두사에 포함시킬 주소의 수를 지정하여 Azure 지역 및 구독에 공용 IP 주소 접두사를 만듭니다. 

공용 IP 주소 범위는 선택한 접두사를 사용 하 여 할당 됩니다. /28 접두사를 만들면 Azure는 범위 중 하나에서 16 개의 ip 주소를 제공 합니다.

범위를 만들 때까지는 Azure에서 할당될 범위를 알 수 없지만 주소는 연속적입니다. 

공용 IP 주소 접두사에는 요금이 부과 됩니다. 자세한 내용은 [공용 ip 주소 가격 책정](https://azure.microsoft.com/pricing/details/ip-addresses)을 참조 하세요.

## <a name="why-create-a-public-ip-address-prefix"></a>공용 IP 주소 접두사를 만드는 이유

공용 IP 주소 리소스를 만들 때 Azure는 해당 지역에서 사용 되는 모든 범위에서 사용 가능한 공용 IP 주소를 할당 합니다. 

Azure에서 IP 주소를 할당할 때까지 정확한 IP를 알 수 없습니다. 이 프로세스는 특정 IP 주소를 허용 하는 방화벽 규칙을 만들 때 문제가 될 수 있습니다. 추가 된 모든 IP 주소에 대해 해당 하는 방화벽 규칙을 추가 해야 합니다.

공용 IP 주소 접두사에서 리소스에 주소를 할당 하는 경우 방화벽 규칙 업데이트가 필요 하지 않습니다. 전체 범위가 규칙에 추가 됩니다.

## <a name="benefits"></a>이점

- 알려진 범위에서 공용 IP 주소 리소스 만들기
- 현재 할당 한 공용 IP 주소 및 아직 할당 하지 않은 주소를 포함 하는 범위를 사용 하는 방화벽 규칙 구성 이 구성을 통해 새 리소스에 IP 주소를 할당 하는 경우 방화벽 규칙을 변경할 필요가 없습니다.
- 만들 수 있는 범위의 기본 크기는 IP 주소 /28 또는 16개입니다.
- 만들 수 있는 범위 수에 대 한 제한은 없습니다. Azure 구독에 포함할 수 있는 고정 공용 IP 주소의 최대 수에는 제한이 있습니다. 만든 범위의 수는 구독에서 사용할 수 있는 것 보다 더 많은 고정 공용 IP 주소를 포함할 수 없습니다. 자세한 내용은 [Azure 제한](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)을 참조하세요.
- 접두사의 주소를 사용하여 만든 주소는 공용 IP 주소를 할당할 수 있는 Azure 리소스에 할당할 수 있습니다.
- 지정 된 IP 주소와 범위 내에 지정 되지 않은 IP 주소를 쉽게 확인할 수 있습니다.

## <a name="scenarios"></a>시나리오
접두사의 고정 공용 IP 주소에는 다음과 같은 리소스를 연결할 수 있습니다.

|리소스|시나리오|단계|
|---|---|---|
|가상 머신| Azure의 가상 머신에 공용 Ip를 연결 하면 방화벽의 허용 목록에 IP 주소를 추가할 때 관리 오버 헤드가 줄어듭니다. 단일 방화벽 규칙을 사용 하 여 전체 접두사를 추가할 수 있습니다. Azure에서 가상 머신으로 확장할 때 동일한 접두사의 IP를 연결하면 비용, 시간 및 관리 오버헤드를 절약할 수 있습니다.| 접두사의 IP를 가상 머신에 연결하려면: </br> 1. [접두사를 만듭니다](manage-public-ip-address-prefix.md) . </br> 2. [접두사에서 IP를 만듭니다](manage-public-ip-address-prefix.md) . </br> 3. [IP를 가상 머신의 네트워크 인터페이스에 연결](virtual-network-network-interface-addresses.md#add-ip-addresses) 합니다. </br> [가상 머신 확장 집합에 ip를 연결할](https://azure.microsoft.com/resources/templates/101-vmms-with-public-ip-prefix/)수도 있습니다.
| 표준 부하 분산 장치 | 공용 ip를 접두사에서 프런트 엔드 IP 구성 또는 아웃 바운드 규칙의 프런트 엔드 IP 구성에 연결 하면 Azure 공용 IP 주소 공간의 간소화를 보장할 수 있습니다. 연속 된 IP 주소 범위에서 아웃 바운드 연결을 제거 하 여 시나리오를 간소화 합니다. | Ip를 접두사에서 부하 분산 장치에 연결 하려면 다음을 수행 합니다. </br> 1. [접두사를 만듭니다](manage-public-ip-address-prefix.md) . </br> 2. [접두사에서 IP를 만듭니다](manage-public-ip-address-prefix.md) . </br> 3. 부하 분산 장치를 만들 때 위의 2 단계에서 만든 IP를 부하 분산 장치의 프런트 엔드 IP로 선택 하거나 업데이트 합니다. |
| Azure Firewall | 아웃바운드 SNAT의 접두사에서 공용 IP를 사용할 수 있습니다. 모든 아웃 바운드 가상 네트워크 트래픽은 [Azure 방화벽](../firewall/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 공용 IP로 변환 됩니다. | IP를 접두사에서 방화벽에 연결 하려면 다음을 수행 합니다. </br> 1. [접두사를 만듭니다](manage-public-ip-address-prefix.md) . </br> 2. [접두사에서 IP를 만듭니다](manage-public-ip-address-prefix.md) . </br> 3. [Azure 방화벽을 배포할](../firewall/tutorial-firewall-deploy-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json#deploy-the-firewall)때 이전에 접두사에서 제공한 IP를 선택 해야 합니다.|
| Application Gateway v2 | 자동 크기 조정 및 영역 중복 응용 프로그램 게이트웨이 v2에 대해 접두사에서 공용 IP를 사용할 수 있습니다. | IP를 접두사에서 게이트웨이에 연결 하려면 다음을 수행 합니다. </br> 1. [접두사를 만듭니다](manage-public-ip-address-prefix.md) . </br> 2. [접두사에서 IP를 만듭니다](manage-public-ip-address-prefix.md) . </br> 3. Application Gateway을 [배포 하는](../application-gateway/quick-create-portal.md#create-an-application-gateway)경우 이전에 접두사에서 제공한 IP를 선택 해야 합니다.|

## <a name="constraints"></a>제약 조건

- 접두사에 대한 IP 주소를 지정할 수 없습니다. Azure는 사용자가 지정 하는 크기에 따라 접두사에 대 한 IP 주소를 제공 합니다.
- 기본적으로 최대 16 개의 IP 주소 또는/28의 접두사를 만들 수 있습니다. 자세한 내용은 [네트워크 제한 증가 요청](https://docs.microsoft.com/azure/azure-portal/supportability/networking-quota-requests) 및 [Azure 제한](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) 을 참조 하세요.
- 접두사를 만든 후에는 범위를 변경할 수 없습니다.
- 표준 SKU로 만든 고정 공용 IP 주소만 접두사의 범위에서 할당할 수 있습니다. 공용 IP 주소 SKU에 대해 자세히 알아보려면 [공용 IP 주소](virtual-network-ip-addresses-overview-arm.md#public-ip-addresses)를 참조하세요.
- 범위에서 가져온 주소는 Azure Resource Manager 리소스에만 할당할 수 있습니다. 클래식 배포 모델에서 리소스에 주소를 할당할 수 없습니다.
- 접두사로 생성 된 모든 공용 IP 주소는 접두사와 동일한 Azure 지역 및 구독에 있어야 합니다. 동일한 지역 및 구독의 리소스에 주소를 할당 해야 합니다.
- 접두사 내에 있는 주소가 리소스에 연결된 공용 IP 주소 리소스에 할당되어 있으면 접두사를 삭제할 수 없습니다. 먼저 접두사의 IP 주소가 할당되어 있는 모든 공용 IP 주소 리소스를 분리합니다.


## <a name="next-steps"></a>다음 단계

- 공용 IP 주소 접두사 [만들기](manage-public-ip-address-prefix.md)
