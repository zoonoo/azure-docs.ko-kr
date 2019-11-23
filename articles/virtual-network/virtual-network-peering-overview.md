---
title: Azure Virtual Network 피어링
titlesuffix: Azure Virtual Network
description: Azure의 가상 네트워크 피어링에 대해 알아봅니다.
services: virtual-network
documentationcenter: na
author: anavinahar
ms.service: virtual-network
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/15/2019
ms.author: anavin
ms.openlocfilehash: 59854d7d46f533510bea97a6845554fc0ce83dbb
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/22/2019
ms.locfileid: "74328360"
---
# <a name="virtual-network-peering"></a>가상 네트워크 피어링

Virtual network peering enables you to seamlessly connect networks in [Azure Virtual Network](virtual-networks-overview.md). The virtual networks appear as one for connectivity purposes. The traffic between virtual machines uses the Microsoft backbone infrastructure. Like traffic between virtual machines in the same network, traffic is routed through Microsoft's *private* network only.

Azure supports the following types of peering:

* Virtual network peering: Connect virtual networks within the same Azure region.
* Global virtual network peering: Connecting virtual networks across Azure regions.

로컬 또는 글로벌 가상 네트워크 피어링을 사용하는 이점은 다음과 같습니다.

* 다른 가상 네트워크에 있는 리소스 간에 짧은 대기 시간, 높은 대역폭 연결
* The ability for resources in one virtual network to communicate with resources in a different virtual network.
* The ability to transfer data between virtual networks across Azure subscriptions, Azure Active Directory tenants, deployment models, and Azure regions.
* The ability to peer virtual networks created through the Azure Resource Manager.
* The ability to peer a virtual network created through Resource Manager to one created through the classic deployment model. Azure 배포 모델에 대해 자세히 알아보려면 [Azure 배포 모델 이해](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json)를 참조하세요.
* 피어링을 만들 때 또는 피어링이 생성된 후에 각 가상 네트워크에 있는 리소스에 대한 가동 중지 시간이 없습니다.

피어링된 가상 네트워크 간의 네트워크 트래픽이 프라이빗 전용입니다. 가상 네트워크 간의 트래픽이 Microsoft 백본 네트워크에서 유지됩니다. 가상 네트워크 간의 통신에 공용 인터넷, 게이트웨이, 또는 암호화가 필요하지 않습니다.

## <a name="connectivity"></a>연결

For peered virtual networks, resources in either virtual network can directly connect with resources in the peered virtual network.

동일한 지역에서 피어링된 가상 네트워크에 있는 가상 머신 간의 네트워크 대기 시간은 단일 가상 네트워크 내의 대기 시간과 같습니다. 네트워크 처리량은 해당 크기에 비례하는 가상 머신에 허용되는 대역폭을 기반으로 합니다. 피어링 내에서 대역폭에 대한 추가 제한이 없습니다.

피어링된 가상 네트워크에 있는 가상 머신 간의 트래픽은 게이트웨이나 공용 인터넷을 통해서가 아니라 Microsoft 백본 인프라를 통해 직접 라우팅됩니다.

You can apply network security groups in either virtual network to block access to other virtual networks or subnets.
When configuring virtual network peering, either open or close the network security group rules between the virtual networks. If you open full connectivity between peered virtual networks, you can apply network security groups to block or deny specific access. Full connectivity is the default option. To learn more about network security groups, see [Security groups](security-overview.md).

## <a name="service-chaining"></a>서비스 체이닝

Service chaining enables you to direct traffic from one virtual network to a virtual appliance or gateway in a peered network through user-defined routes.

To enable service chaining, configure user-defined routes that point to virtual machines in peered virtual networks as the *next hop* IP address. User-defined routes could also point to virtual network gateways to enable service chaining.

You can deploy hub-and-spoke networks, where the hub virtual network hosts infrastructure components such as a network virtual appliance or VPN gateway. 모든 스포크 가상 네트워크는 허브 가상 네트워크와 피어링할 수 있습니다. Traffic flows through network virtual appliances or VPN gateways in the hub virtual network.

가상 네트워크 피어링을 사용하면 사용자 정의 경로의 다음 홉이 피어링된 가상 네트워크 또는 VPN 게이트웨이에 있는 가상 머신의 IP 주소가 될 수 있습니다. You can't route between virtual networks with a user-defined route that specifies an Azure ExpressRoute gateway as the next hop type. 사용자 정의 경로에 대한 자세한 내용은 [사용자 정의 경로 개요](virtual-networks-udr-overview.md#user-defined)를 참조하세요. To learn how to create a hub and spoke network topology, see [Hub-spoke network topology in Azure](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json).

## <a name="gateways-and-on-premises-connectivity"></a>게이트웨이 및 온-프레미스 연결

Each virtual network, including a peered virtual network, can have its own gateway. A virtual network can use its gateway to connect to an on-premises network. You can also configure [virtual network-to-virtual network connections](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json) by using gateways, even for peered virtual networks.

When you configure both options for virtual network interconnectivity, the traffic between the virtual networks flows through the peering configuration. The traffic uses the Azure backbone.

You can also configure the gateway in the peered virtual network as a transit point to an on-premises network. In this case, the virtual network that is using a remote gateway can't have its own gateway. A virtual network has only one gateway. The gateway is either a local or remote gateway in the peered virtual network, as shown in the following diagram:

![가상 네트워크 피어링 전송](./media/virtual-networks-peering-overview/local-or-remote-gateway-in-peered-virual-network.png)

Both virtual network peering and global virtual network peering support gateway transit.

Gateway transit between virtual networks created through different deployment models is supported. The gateway must be in the virtual network in the Resource Manager model. 전송을 위한 게이트웨이 사용에 관해 자세히 알아보려면 [가상 네트워크 피어링에서 전송을 위한 VPN 게이트웨이 구성](../vpn-gateway/vpn-gateway-peering-gateway-transit.md?toc=%2fazure%2fvirtual-network%2ftoc.json)을 참조하세요.

When you peer virtual networks that share a single Azure ExpressRoute connection, the traffic between them goes through the peering relationship. That traffic uses the Azure backbone network. 로컬 게이트웨이 각각에서 가상 네트워크를 사용하여 온-프레미스 회로에 연결할 수 있습니다. Otherwise, you can use a shared gateway and configure transit for on-premises connectivity.

## <a name="troubleshoot"></a>문제 해결

To confirm that virtual networks are peered, you can check effective routes. Check routes for a network interface in any subnet in a virtual network. 가상 네트워크 피어링이 존재하는 경우에는 가상 네트워크 내의 모든 서브넷에는 피어링된 각 가상 네트워크의 각 주소 공간에 대해 다음 홉 형식의 *VNet 피어링*을 사용하는 경로가 있습니다. For more information, see [Diagnose a virtual machine routing problem](diagnose-network-routing-problem.md).

You can also troubleshoot connectivity to a virtual machine in a peered virtual network using Azure Network Watcher. A connectivity check lets you see how traffic is routed from a source virtual machine's network interface to a destination virtual machine's network interface. For more information, see [Troubleshoot connections with Azure Network Watcher using the Azure portal](../network-watcher/network-watcher-connectivity-portal.md#check-connectivity-to-a-virtual-machine).

You can also try the [Troubleshoot virtual network peering issues](virtual-network-troubleshoot-peering-issues.md).

## Constraints for peered virtual networks<a name="requirements-and-constraints"></a>

가상 네트워크가 글로벌 피어링될 때만 다음 제약 조건이 적용됩니다.

* Resources in one virtual network can't communicate with the front-end IP address of a Basic Internal Load Balancer (ILB)  in a globally peered virtual network.
* Some services that use a Basic load balancer don't work over global virtual network peering. For more information, see [What are the constraints related to Global VNet Peering and Load Balancers?](virtual-networks-faq.md#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers).

For more information, see [Requirements and constraints](virtual-network-manage-peering.md#requirements-and-constraints). To learn more about the supported number of peerings, see [Networking limits](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).

## <a name="permissions"></a>권한

To learn about permissions required to create a virtual network peering, see [Permissions](virtual-network-manage-peering.md#permissions).

## <a name="pricing"></a>가격

There's a nominal charge for ingress and egress traffic that uses a virtual network peering connection. For more information, see [Virtual Network pricing](https://azure.microsoft.com/pricing/details/virtual-network).

Gateway Transit is a peering property that enables a virtual network to utilize a VPN/ExpressRoute gateway in a peered virtual network. Gateway transit works for both cross premises and network-to-network connectivity. Traffic to the gateway (ingress or egress) in the peered virtual network incurs virtual network peering charges. For more information, see [VPN Gateway pricing](https://azure.microsoft.com/pricing/details/vpn-gateway/) for VPN gateway charges and ExpressRoute Gateway pricing for ExpressRoute gateway charges.

>[!NOTE]
> A previous version of this document stated that virtual network peering charges would not apply with Gateway Transit. It now reflects accurate pricing per the pricing page.

## <a name="next-steps"></a>다음 단계

* You can create a peering between two virtual networks. The networks can belong to the same subscription, different deployment models in the same subscription, or different subscriptions. 다음 시나리오 중 하나에 대한 자습서를 완료합니다.

    |Azure 배포 모델             | Subscription  |
    |---------                          |---------|
    |둘 다 Resource Manager              |[동일](tutorial-connect-virtual-networks-portal.md)|
    |                                   |[다름](create-peering-different-subscriptions.md)|
    |하나는 Resource Manager, 다른 하나는 클래식  |[동일](create-peering-different-deployment-models.md)|
    |                                   |[다름](create-peering-different-deployment-models-subscriptions.md)|

* To learn how to create a hub and spoke network topology, see [Hub-spoke network topology in Azure](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json).
* To learn about all virtual network peering settings, see [Create, change, or delete a virtual network peering](virtual-network-manage-peering.md).
* For answers to common virtual network peering and global virtual network peering questions, see [VNet Peering](virtual-networks-faq.md#vnet-peering).
