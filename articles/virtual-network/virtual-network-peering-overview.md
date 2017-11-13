---
title: "Azure Virtual Network 피어링 | Microsoft Docs"
description: "Azure의 가상 네트워크 피어링에 대해 알아봅니다."
services: virtual-network
documentationcenter: na
author: NarayanAnnamalai
manager: jefco
editor: tysonn
ms.assetid: eb0ba07d-5fee-4db0-b1cb-a569b7060d2a
ms.service: virtual-network
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: narayan;anavin
ms.openlocfilehash: 7d3e6a34b5851a5a35a530b18efc3db3e2249274
ms.sourcegitcommit: 3df3fcec9ac9e56a3f5282f6c65e5a9bc1b5ba22
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2017
---
# <a name="virtual-network-peering"></a>가상 네트워크 피어링

가상 네트워크 피어링을 사용하면 두 개의 Azure [가상 네트워크](virtual-networks-overview.md)를 원활하게 연결할 수 있습니다. 가상 네트워크가 피어링되면 연결성을 위해 하나로 표시됩니다. 트래픽이 *개인* IP 주소를 통해서만 동일한 가상 네트워크에 있는 가상 네트워크 간에 라우팅되는 것과 마찬가지로 피어링된 가상 네트워크에 있는 가상 컴퓨터 간의 트래픽은 Microsoft 백본 인프라를 통해 라우팅됩니다. 

가상 네트워크 피어링을 사용하는 이점은 다음과 같습니다.

* 피어링된 가상 네트워크 간의 네트워크 트래픽이 개인 전용입니다. 가상 네트워크 간의 트래픽이 Microsoft 백본 네트워크에서 유지됩니다. 가상 네트워크 간의 통신에 공용 인터넷, 게이트웨이, 또는 암호화가 필요하지 않습니다.
* 다른 가상 네트워크에 있는 리소스 간에 짧은 대기 시간, 높은 대역폭 연결
* 가상 네트워크가 피어링되면 하나의 가상 네트워크에 있는 리소스가 다른 가상 네트워크에 있는 리소스와 통신할 수 있습니다.
* Azure 구독, 배포 모델 및 Azure 지역(미리 보기)에 데이터를 전송할 수 있습니다.
* Azure Resource Manager를 통해 만든 가상 네트워크를 피어링하거나 클래식 배포 모델을 통해 만든 가상 네트워크에 Resource Manager를 통해 만든 다른 가상 네트워크를 피어링할 수 있는 기능입니다. Azure 배포 모델에 대해 자세히 알아보려면 [Azure 배포 모델 이해](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json)를 참조하세요.
* 피어링을 만들 때 또는 피어링이 생성된 후에 각 가상 네트워크에 있는 리소스에 대한 가동 중지 시간이 없습니다.

## <a name="requirements-constraints"></a>요구 사항 및 제약 조건

* 동일한 지역의 가상 네트워크 피어링은 일반 공급됩니다. 서로 다른 지역의 가상 네트워크 피어링은 미국 중서부, 캐나다 중부 및 미국 서부 2에서 현재 미리 보기 상태입니다. 다른 지역에서 가상 네트워크를 피어링하기 전에 먼저 미리 보기에 대한 [구독을 등록](virtual-network-create-peering.md#register)해야 합니다. 미리 보기에 대한 등록을 완료하지 않은 경우 다른 지역에서 가상 네트워크 간 피어링 만들기 시도는 실패합니다.
    > [!WARNING]
    > 교차 지역에서 만든 가상 네트워크 피어링은 일반 공급 릴리스의 피어링과 동일한 수준의 가용성과 신뢰성을 가질 수 없습니다. 가상 네트워크 피어링은 기능상의 제약이 있거나, 일부 Azure 지역에서 사용하지 못할 수 있습니다. 이 기능의 가용성 및 상태에 대한 최신 알림을 보려면 [Azure Virtual Network 업데이트](https://azure.microsoft.com/updates/?product=virtual-network) 페이지를 참조하세요.

* 피어링된 가상 네트워크에는 겹치지 않는 IP 주소 공간이 있어야 합니다.
* 가상 네트워크가 다른 가상 네트워크와 피어링되면 주소 범위를 추가하거나 가상 네트워크의 주소 공간에서 삭제할 수 없습니다. 피어링된 가상 네트워크의 주소 공간에 주소 범위를 추가해야 하는 경우 피어링을 제거하고, 주소 공간을 추가한 후 피어링을 다시 추가해야 합니다.
* 가상 네트워크 피어링은 두 개의 가상 네트워크 간에 가능합니다. 피어링을 통해 파생된 전이적 관계가 없습니다. 예를 들어 virtualNetworkA가 virtualNetworkB와 피어링되고 virtualNetworkB가 virtualNetworkC와 피어링되는 경우 virtualNetworkA는 virtualNetworkC로 피어링되지 *않습니다*.
* 서로 다른 두 구독의 권한 있는 사용자([특정 권한](create-peering-different-deployment-models-subscriptions.md#permissions) 참조)가 피어링 권한을 부여하고 구독이 동일한 Azure Active Directory 테넌트에 연결되어 있는 한 두 구독에 있는 가상 네트워크를 피어링할 수 있습니다. [VPN Gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#V2V)를 사용하여 다른 Active Directory 테넌트에 연결된 구독에서 가상 네트워크를 연결할 수 있습니다.
* 두 가상 네트워크를 Resource Manager 배포 모델을 통해 만들거나, 하나는 Resource Manager 배포 모델을 통해 만들고 다른 하나는 클래식 배포 모델을 통해 만든 경우 이 두 가상 네트워크를 피어링할 수 있습니다. 그러나 클래식 배포 모델을 통해 만든 가상 네트워크는 서로 피어링될 수 없습니다. [VPN Gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#V2V)를 사용하여 클래식 배포 모델을 통해 만든 가상 네트워크를 연결할 수 없습니다.
* 피어링된 가상 네트워크에 있는 가상 컴퓨터 간의 통신에 추가 대역폭이 제한되지 않지만 적용되는 가상 컴퓨터의 크기에 따라 최대 네트워크 대역폭이 제한됩니다. 다양한 가상 컴퓨터 크기의 최대 네트워크 대역폭에 대한 자세한 내용을 알아보려면 [Windows](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 또는 [Linux](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 가상 컴퓨터 크기 문서를 참조하세요.

     ![기본 가상 네트워크 피어링](./media/virtual-networks-peering-overview/figure03.png)

## <a name="connectivity"></a>연결

가상 네트워크가 피어링된 후에 가상 네트워크의 리소스는 피어링된 가상 네트워크의 리소스와 직접 연결할 수 있습니다.

동일한 지역에서 피어링된 가상 네트워크에 있는 가상 컴퓨터 간의 네트워크 대기 시간은 단일 가상 네트워크 내의 대기 시간과 같습니다. 네트워크 처리량은 해당 크기에 비례하는 가상 컴퓨터에 허용되는 대역폭을 기반으로 합니다. 피어링 내에서 대역폭에 대한 추가 제한이 없습니다.

피어링된 가상 네트워크에 있는 가상 컴퓨터 간의 트래픽은 게이트웨이나 공용 인터넷을 통해서가 아니라 Microsoft 백본 인프라를 통해 직접 라우팅됩니다.

가상 네트워크의 가상 컴퓨터는 동일한 지역에서 피어링된 가상 네트워크에 있는 내부 부하 분산 장치에 액세스할 수 있습니다. 내부 부하 분산 장치에 대한 지원은 전역적으로 피어링된 가상 네트워크에 확장되지 않습니다(미리 보기). 전역 가상 네트워크 피어링의 일반 공급 릴리스는 내부 부하 분산 장치에 대해 지원됩니다.

원하는 경우 다른 가상 네트워크 또는 서브넷에 대한 액세스를 차단하기 위해 네트워크 보안 그룹을 각 가상 네트워크에 적용할 수 있습니다.
가상 네트워크 피어링을 구성할 때 가상 네트워크 간에 네트워크 보안 그룹 규칙을 열거나 닫을 수 있습니다. 피어링된 가상 네트워크 간의 전체 연결을 여는 경우(기본 옵션임) 특정 서브넷 또는 가상 컴퓨터에 네트워크 보안 그룹을 적용하여 특정 액세스를 차단하거나 거부할 수 있습니다. 네트워크 보안 그룹에 대한 자세한 내용은 [네트워크 보안 그룹 개요](virtual-networks-nsg.md)를 참조하세요.

## <a name="service-chaining"></a>서비스 체이닝

피어링된 가상 네트워크에서 가상 컴퓨터를 가리키는 사용자 정의 경로를 "다음 홉" IP 주소로 구성하여 서비스 체이닝을 사용할 수 있습니다. 서비스 체이닝을 사용하면 사용자 정의 경로를 통해 피어링된 가상 네트워크의 가상 네트워크에서 가상 어플라이언스로 트래픽을 전송할 수 있습니다.

허브 및 스포크 유형의 환경을 효율적으로 만들 수도 있습니다. 그러면 허브가 네트워크 가상 어플라이언스와 같은 인프라 구성 요소를 호스팅할 수 있습니다. 모든 스포크 가상 네트워크는 허브 가상 네트워크와 피어링할 수 있습니다. 허브 가상 네트워크에서 실행되는 네트워크 가상 어플라이언스를 통해 트래픽을 전달할 수 있습니다. 즉, 가상 네트워크 피어링을 사용하면 사용자 정의 경로에서 대한 다음 홉 IP 주소가 피어링된 가상 네트워크에 있는 가상 컴퓨터의 IP 주소가 될 수 있습니다. 사용자 정의 경로에 대한 자세한 내용은 [사용자 정의 경로 개요](virtual-networks-udr-overview.md)를 참조하세요. 허브 및 스포크 네트워크 토폴로지를 만드는 방법에 대한 자세한 내용은 [허브 및 스포크 네트워크 토폴로지](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json#virtual network-peering)를 참조하세요.

## <a name="gateways-and-on-premises-connectivity"></a>게이트웨이 및 온-프레미스 연결

각 가상 네트워크는 다른 가상 네트워크와 피어링되었는지 여부와 상관없이 고유한 게이트웨이를 가지고 있으며 이를 온-프레미스 네트워크에 연결하는 데 사용할 수 있습니다. 가상 네트워크가 피어링된 경우에도 게이트웨이를 사용하여 [가상 네트워크 간 연결](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md)을 구성할 수도 있습니다.

가상 네트워크 연결을 위한 옵션을 모두 구성한 경우 가상 네트워크 간의 트래픽은 피어링 구성(즉, Azure 백본)을 통해 전달됩니다.

동일한 지역에서 가상 네트워크가 피어링되면 피어링된 가상 네트워크에 있는 게이트웨이를 온-프레미스 네트워크의 전송 지점으로 구성할 수 있습니다. 이 경우 원격 게이트웨이를 사용하는 가상 네트워크는 고유한 게이트웨이를 사용할 수 없습니다. 가상 네트워크는 하나의 게이트웨이만을 가질 수 있습니다. 게이트웨이는 다음 그림에서 보여준 대로 피어링된 가상 네트워크의 로컬 게이트웨이 또는 원격 게이트웨이일 수 있습니다.

![가상 네트워크 피어링 전송](./media/virtual-networks-peering-overview/figure04.png)

게이트웨이 전송은 다른 배포 모델 또는 다른 지역을 통해 만든 가상 네트워크 간의 피어링 관계에서 지원되지 않습니다. 피어링 관계인 두 가상 네트워크는 Resource Manager를 통해 만들고 게이트웨이 전송을 작동시키기 위해 동일한 지역에 있어야 합니다. 전역적으로 피어링된 가상 네트워크는 현재 게이트웨이 전송을 지원하지 않습니다.

단일 Azure ExpressRoute 연결을 공유하는 가상 네트워크가 피어링된 경우 둘 사이의 트래픽은 피어링 관계(즉, Azure 백본 네트워크)를 거치게 됩니다. 로컬 게이트웨이 각각에서 가상 네트워크를 사용하여 온-프레미스 회로에 연결할 수 있습니다. 또는 공유 게이트웨이를 사용하고 온-프레미스 연결에 대한 전송을 구성할 수 있습니다.

## <a name="permissions"></a>권한

가상 네트워크 피어링은 권한 있는 작업입니다. VirtualNetworks 네임스페이스에서 별도 기능입니다. 사용자는 특정 권한을 지정하여 피어링하는 권한을 부여할 수 있습니다. 가상 네트워크에 대한 읽기-쓰기 액세스 권한이 있는 사용자는 이러한 권한을 자동으로 상속합니다.

피어링 기능의 관리자 또는 권한있는 사용자 중 하나인 사용자는 다른 가상 네트워크에 대한 피어링 작업을 시작할 수 있습니다. 최소한으로 필요한 수준의 사용 권한은 네트워크 참여자입니다. 다른 쪽에 피어링에 대해 동일한 요청이 있고 다른 요구 사항을 충족하는 경우 피어링이 설정됩니다.

예를 들어 myVirtualNetworkA 및 myVirtualNetworkB라는 가상 네트워크를 피어링한 경우 계정에는 각 가상 네트워크에 대한 다음과 같은 최소 역할 또는 사용 권한이 할당되어야 합니다.

|가상 네트워크|배포 모델|역할|권한|
|---|---|---|---|
|myVirtualNetworkA|리소스 관리자|[네트워크 참여자](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)|Microsoft.Network/virtualNetworks/virtualNetworkPeerings/write|
| |클래식|[클래식 네트워크 참여자](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#classic-network-contributor)|해당 없음|
|myVirtualNetworkB|리소스 관리자|[네트워크 참여자](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)|Microsoft.Network/virtualNetworks/peer|
||클래식|[클래식 네트워크 참여자](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#classic-network-contributor)|Microsoft.ClassicNetwork/virtualNetworks/peer|

## <a name="monitor"></a>모니터

Resource Manager를 통해 만든 두 가상 네트워크를 피어링할 때는 피어링의 각 가상 네트워크에 대해 피어링을 구성해야 합니다.
피어링 연결 상태를 모니터링할 수 있습니다. 피어링 상태는 다음 상태 중 하나입니다.

* **시작됨:** 첫 번째 가상 네트워크에서 두 번째 가상 네트워크로의 피어링을 만들면 피어링 상태는 시작됨입니다.

* **연결됨:** 두 번째 가상 네트워크에서 첫 번째 가상 네트워크로의 피어링을 만들면 피어링 상태는 연결됨입니다. 첫 번째 가상 네트워크의 피어링 상태를 보면 해당 상태가 시작됨에서 연결됨으로 변경된 것을 알 수 있습니다. 두 가상 네트워크 피어링의 피어링 상태가 연결됨이 될 때까지는 피어링이 설정되지 않습니다.

* **연결이 끊김**: 연결이 설정된 후에 피어링 연결이 삭제되면 피어링 상태는 연결이 끊김입니다.

## <a name="troubleshoot"></a>문제 해결

피어링 연결에서 트래픽 흐름의 문제를 해결하려면 [유효 경로를 확인](virtual-network-routes-troubleshoot-portal.md)할 수 있습니다.

Network Watcher의 [연결성 확인](../network-watcher/network-watcher-connectivity-portal.md)을 사용하여 피어링된 가상 네트워크에서 가상 컴퓨터에 대한 연결 문제를 해결할 수 있습니다. 연결성 확인을 통해 원본 VM의 네트워크 인터페이스에서 대상 VM의 네트워크 인터페이스에 직접 라우팅되는 방법을 확인할 수 있습니다.

## <a name="limits"></a>제한

단일 가상 네트워크에 허용되는 피어링의 수에 대한 제한이 있습니다. 자세한 내용은 [Azure 네트워킹 제한](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)을 참조하세요.

## <a name="pricing"></a>가격

가상 네트워크 피어링 연결을 활용하는 수신 및 송신 트래픽에 대한 명목 요금이 부과됩니다. 자세한 내용은 [가격 책정 페이지](https://azure.microsoft.com/pricing/details/virtual-network)를 참조하세요.

## <a name="next-steps"></a>다음 단계

* 가상 네트워크 피어링 자습서를 완료합니다. 가상 네트워크 피어링은 동일하거나 다른 구독에 존재하는 동일하거나 다른 배포 모델을 통해 만든 가상 네트워크 간에 만들어집니다. 다음 시나리오 중 하나에 대한 자습서를 완료합니다.

    |Azure 배포 모델  | 구독  |
    |---------|---------|
    |둘 다 Resource Manager |[동일](virtual-network-create-peering.md)|
    | |[다름](create-peering-different-subscriptions.md)|
    |하나는 Resource Manager, 다른 하나는 클래식     |[동일](create-peering-different-deployment-models.md)|
    | |[다름](create-peering-different-deployment-models-subscriptions.md)|

* [허브 및 스포크 네트워크 토폴로지](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json#virtual network-peering)를 만드는 방법을 알아봅니다.
* 모든 [가상 네트워크 피어링 설정 및 변경 방법](virtual-network-manage-peering.md)에 대해 자세히 알아봅니다.
