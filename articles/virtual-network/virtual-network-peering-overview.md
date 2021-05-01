---
title: Azure Virtual Network 피어링
titlesuffix: Azure Virtual Network
description: Azure Virtual Network에서 네트워크를 연결할 수 있는 방법 등 Azure의 가상 네트워크 피어링에 대해 알아봅니다.
services: virtual-network
documentationcenter: na
author: altambaw
ms.service: virtual-network
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/15/2019
ms.author: kumud
ms.openlocfilehash: 1356c896abff8c525df283251240d027123405fc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104778292"
---
# <a name="virtual-network-peering"></a>가상 네트워크 피어링

가상 네트워크 피어링을 사용하면 Azure에서 두 개 이상의 [가상 네트워크](virtual-networks-overview.md)를 원활하게 연결할 수 있습니다. 가상 네트워크는 연결하기 위해 하나로 표시됩니다. 피어링된 가상 네트워크에 있는 가상 머신 간의 트래픽은 Microsoft 백본 인프라를 사용합니다. 동일한 네트워크에 있는 가상 컴퓨터 간의 트래픽과 마찬가지로 트래픽은 Microsoft *개인* 네트워크만을 통해 라우팅됩니다.

Azure는 다음 유형의 피어링을 지원합니다.

* **가상 네트워크 피어링**: 동일한 Azure 지역 내에서 가상 네트워크를 연결합니다.
* **글로벌 가상 네트워크 피어링**: Azure 지역에서 가상 네트워크를 연결합니다.

로컬 또는 글로벌 가상 네트워크 피어링을 사용하는 이점은 다음과 같습니다.

* 다른 가상 네트워크에 있는 리소스 간에 짧은 대기 시간, 높은 대역폭 연결
* 한 가상 네트워크의 리소스가 다른 가상 네트워크의 리소스와 통신할 수 있는 기능입니다.
* Azure 구독, Azure Active Directory 테넌트, 배포 모델 및 Azure 지역에서 가상 네트워크 간에 데이터를 전송할 수 있는 기능입니다.
* Azure Resource Manager를 통해 만든 가상 네트워크를 피어링할 수 있는 기능입니다.
* 리소스 관리자를 통해 만든 가상 네트워크를 클래식 배포 모델을 통해 만든 가상 네트워크에 피어링할 수 있는 기능입니다. Azure 배포 모델에 대해 자세히 알아보려면 [Azure 배포 모델 이해](../azure-resource-manager/management/deployment-models.md?toc=%2fazure%2fvirtual-network%2ftoc.json)를 참조하세요.
* 피어링을 만들 때 또는 피어링이 생성된 후에 각 가상 네트워크에 있는 리소스에 대한 가동 중지 시간이 없습니다.

피어링된 가상 네트워크 간의 네트워크 트래픽이 프라이빗 전용입니다. 가상 네트워크 간의 트래픽이 Microsoft 백본 네트워크에서 유지됩니다. 가상 네트워크 간의 통신에 공용 인터넷, 게이트웨이, 또는 암호화가 필요하지 않습니다.

## <a name="connectivity"></a>연결

피어링된 가상 네트워크의 경우, 가상 네트워크의 리소스는 피어링된 가상 네트워크의 리소스와 직접 연결할 수 있습니다.

동일한 지역에서 피어링된 가상 네트워크에 있는 가상 머신 간의 네트워크 대기 시간은 단일 가상 네트워크 내의 대기 시간과 같습니다. 네트워크 처리량은 해당 크기에 비례하는 가상 머신에 허용되는 대역폭을 기반으로 합니다. 피어링 내에서 대역폭에 대한 추가 제한이 없습니다.

피어링된 가상 네트워크에 있는 가상 머신 간의 트래픽은 게이트웨이나 공용 인터넷을 통해서가 아니라 Microsoft 백본 인프라를 통해 직접 라우팅됩니다.

가상 네트워크의 네트워크 보안 그룹을 적용하여 다른 가상 네트워크 또는 서브넷에 대한 액세스를 차단할 수 있습니다.
가상 네트워크 피어링을 구성할 때 가상 네트워크 간에 네트워크 보안 그룹 규칙을 열거나 닫을 수 있습니다. 피어링된 가상 네트워크 간의 전체 연결 기능을 여는 경우, 네트워크 보안 그룹을 적용하여 특정 액세스를 차단하거나 거부할 수 있습니다. 전체 연결은 기본 옵션입니다. 네트워크 보안 그룹에 대한 자세한 내용은 [보안 그룹](./network-security-groups-overview.md)을 참조하세요.

## <a name="service-chaining"></a>서비스 체이닝

서비스 체이닝을 사용하면 사용자 정의 경로를 통해 피어링된 네트워크의 가상 네트워크에서 가상 어플라이언스 또는 게이트웨이로 트래픽을 전송할 수 있습니다.

서비스 체인을 사용하도록 설정하려면 피어링된 가상 네트워크의 가상 컴퓨터를 *다음 홉* IP 주소로 가리키는 사용자 정의 경로를 구성합니다. 또한 사용자 정의 경로는 서비스 체이닝을 사용하도록 설정할 수 있는 가상 네트워크 게이트웨이를 가리킬 수 있습니다.

허브 가상 네트워크가 네트워크 가상 어플라이언스 또는 VPN Gateway와 같은 인프라 구성 요소를 호스트하는 허브 및 스포크 네트워크를 배포할 수 있습니다. 모든 스포크 가상 네트워크는 허브 가상 네트워크와 피어링할 수 있습니다. 트래픽은 허브 가상 네트워크에서 네트워크 가상 어플라이언스 또는 VPN Gateway를 통해 흐릅니다.

가상 네트워크 피어링을 사용하면 사용자 정의 경로의 다음 홉이 피어링된 가상 네트워크 또는 VPN 게이트웨이에 있는 가상 머신의 IP 주소가 될 수 있습니다. Azure ExpressRoute 게이트웨이를 다음 홉 형식으로 지정하는 사용자 정의 경로를 사용하여 가상 네트워크 간에 라우팅할 수는 없습니다. 사용자 정의 경로에 대한 자세한 내용은 [사용자 정의 경로 개요](virtual-networks-udr-overview.md#user-defined)를 참조하세요. 허브 및 스포크 네트워크 토폴로지를 만드는 방법을 알아보려면 [허브 스포크 네트워크 토폴로지](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json)를 참조하세요.

## <a name="gateways-and-on-premises-connectivity"></a>게이트웨이 및 온-프레미스 연결

피어링된 가상 네트워크를 포함하는 각 가상 네트워크에는 고유한 게이트웨이가 있을 수 있습니다. 가상 네트워크는 해당 게이트웨이를 사용하여 온-프레미스 네트워크에 연결할 수 있습니다. 또한 피어링된 가상 네트워크에서도 게이트웨이를 사용하여 [가상 네트워크 간 연결](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json)을 구성할 수 있습니다.

가상 네트워크 상호 연결 옵션을 모두 구성하면 가상 네트워크 간의 트래픽이 피어링 구성을 통해 흐릅니다. 이 트래픽은 Azure 백본을 사용합니다.

또한 피어링된 가상 네트워크의 게이트웨이를 온-프레미스 네트워크의 전송 지점으로 구성할 수 있습니다. 이 경우 원격 게이트웨이를 사용하는 가상 네트워크는 고유한 게이트웨이를 사용할 수 없습니다. 가상 네트워크는 게이트웨이를 하나만 포함할 수 있습니다. 다음 그림에 나와 있는 바와 같이, 게이트웨이는 피어링된 가상 네트워크의 로컬 또는 원격 게이트웨이입니다.

![가상 네트워크 피어링 전송](./media/virtual-networks-peering-overview/local-or-remote-gateway-in-peered-virual-network.png)

가상 네트워크 피어링과 글로벌 가상 네트워크 피어링은 모두 게이트웨이 전송을 지원합니다.

다른 배포 모델을 통해 만든 가상 네트워크 간의 게이트웨이 전송이 지원됩니다. 게이트웨이는 Resource Manager 모델의 가상 네트워크에 있어야 합니다. 전송을 위한 게이트웨이 사용에 관해 자세히 알아보려면 [가상 네트워크 피어링에서 전송을 위한 VPN 게이트웨이 구성](../vpn-gateway/vpn-gateway-peering-gateway-transit.md?toc=%2fazure%2fvirtual-network%2ftoc.json)을 참조하세요.

단일 Azure ExpressRoute 연결을 공유하는 가상 네트워크를 피어링하면 이들 간의 트래픽이 피어링 관계를 거칩니다. 해당 트래픽은 Azure 백본 네트워크를 사용합니다. 로컬 게이트웨이 각각에서 가상 네트워크를 사용하여 온-프레미스 회로에 연결할 수 있습니다. 또는 공유 게이트웨이를 사용하고, 온-프레미스 연결에 대한 전송을 구성할 수 있습니다.

## <a name="troubleshoot"></a>문제 해결

가상 네트워크가 피어링되었는지 확인하려면 유효 경로를 확인하면 됩니다. 가상 네트워크의 서브넷에서 네트워크 인터페이스의 경로를 확인합니다. 가상 네트워크 피어링이 존재하는 경우에는 가상 네트워크 내의 모든 서브넷에는 피어링된 각 가상 네트워크의 각 주소 공간에 대해 다음 홉 형식의 *VNet 피어링* 을 사용하는 경로가 있습니다. 자세한 내용은 [가상 머신 라우팅 문제 진단](diagnose-network-routing-problem.md)을 참조하세요.

또한 Azure Network Watcher를 사용하여 피어링된 가상 네트워크에서 가상 머신에 대한 연결 문제를 해결할 수 있습니다. 연결 확인을 통해 원본 가상 컴퓨터의 네트워크 인터페이스에서 대상 가상 컴퓨터의 네트워크 인터페이스로 트래픽을 라우팅하는 방법을 확인할 수 있습니다. 자세한 내용은 [Azure Portal을 사용하여 Azure Network Watcher와의 연결 문제 해결](../network-watcher/network-watcher-connectivity-portal.md#check-connectivity-to-a-virtual-machine)을 참조하세요.

[가상 네트워크 피어링 문제 해결](virtual-network-troubleshoot-peering-issues.md)도 사용해 볼 수 있습니다.

## <a name="constraints-for-peered-virtual-networks"></a>피어링된 가상 네트워크의 제약 조건<a name="requirements-and-constraints"></a>

가상 네트워크가 글로벌 피어링될 때만 다음 제약 조건이 적용됩니다.

* 하나의 가상 네트워크에 있는 리소스는 전역적으로 피어링된 가상 네트워크에 있는 기본 ILB(내부 Load Balancer)의 프런트 엔드 IP 주소와 통신할 수 없습니다.
* 기본 Load Balancer를 사용하는 일부 서비스는 글로벌 가상 네트워크 피어링을 통해 작동하지 않습니다. 자세한 내용은 [글로벌 VNet 피어링 및 Load Balancer와 관련된 제약 조건](virtual-networks-faq.md#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers)을 참조하세요.

자세한 내용은 [요구 사항 및 제약 조건](virtual-network-manage-peering.md#requirements-and-constraints)을 참조하세요. 지원되는 피어링 수에 대해 자세히 알아보려면 [네트워킹 제한](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)을 참조하세요.

## <a name="permissions"></a>사용 권한

가상 네트워크 피어링을 만드는 데 필요한 사용 권한에 대해 자세히 알아보려면 [사용 권한](virtual-network-manage-peering.md#permissions)을 참조하세요.

## <a name="pricing"></a>가격 책정

가상 네트워크 피어링 연결을 사용하는 수신/송신 트래픽에는 명목상의 요금이 있습니다. 자세한 내용은 [가상 네트워크 가격 책정](https://azure.microsoft.com/pricing/details/virtual-network)을 참조하세요.

게이트웨이 전송은 가상 네트워크가 피어링된 가상 네트워크에서 VPN/ExpressRoute 게이트웨이를 활용할 수 있도록 하는 피어링 속성입니다. 게이트웨이 전송은 프레미스 간/네트워크 간 연결에서 모두 작동합니다. 피어링된 가상 네트워크의 게이트웨이(수신 또는 송신)에 대한 트래픽은 스포크 VNet(또는 비게이트웨이 VNet)에서 가상 네트워크 피어링 요금을 발생시킵니다. 자세한 내용은 VPN Gateway 요금에 대한 [VPN Gateway 가격 책정](https://azure.microsoft.com/pricing/details/vpn-gateway/) 및 ExpressRoute 게이트웨이 요금에 대한 ExpressRoute 게이트웨이 가격 책정을 참조하세요.

>[!NOTE]
> 이전 버전의 이 문서에서는 가상 네트워크 피어링 요금이 게이트웨이 전송을 사용하는 스포크 VNet(또는 비게이트웨이 VNet)에 적용되지 않는다고 설명했습니다. 이제 가격 책정 페이지에 따라 정확한 가격이 반영됩니다.

## <a name="next-steps"></a>다음 단계

* 두 가상 네트워크 간에 피어링을 만들 수 있습니다. 네트워크는 동일한 구독, 동일한 구독에 있는 다른 배포 모델 또는 다른 구독에 속할 수 있습니다. 다음 시나리오 중 하나에 대한 자습서를 완료합니다.

    |Azure 배포 모델             | Subscription  |
    |---------                          |---------|
    |둘 다 Resource Manager              |[동일](tutorial-connect-virtual-networks-portal.md)|
    |                                   |[다름](create-peering-different-subscriptions.md)|
    |하나는 Resource Manager, 다른 하나는 클래식  |[동일](create-peering-different-deployment-models.md)|
    |                                   |[다름](create-peering-different-deployment-models-subscriptions.md)|

* 허브 및 스포크 네트워크 토폴로지를 만드는 방법을 알아보려면 [허브 스포크 네트워크 토폴로지](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json)를 참조하세요.
* 모든 가상 네트워크 피어링 설정에 대해 자세히 알아보려면 [가상 네트워크 피어링 만들기, 변경 또는 삭제](virtual-network-manage-peering.md)를 참조하세요.
* 일반적인 가상 네트워크 피어링 및 글로벌 가상 네트워크 피어링 질문에 대한 답변은 [VNet 피어링](virtual-networks-faq.md#vnet-peering)을 참조하세요.
