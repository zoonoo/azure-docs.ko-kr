---
title: Azure Virtual Network | Microsoft Docs
description: "Azure Virtual Network 개념 및 기능에 대해 알아봅니다."
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 9633de4b-a867-4ddf-be3c-a332edf02e24
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/23/2017
ms.author: jdial
translationtype: Human Translation
ms.sourcegitcommit: 4f2230ea0cc5b3e258a1a26a39e99433b04ffe18
ms.openlocfilehash: 186b8331d2fcfc16bd41eb08badb200e2abf9e30
ms.lasthandoff: 03/25/2017


---
# <a name="azure-virtual-network"></a>Azure 가상 네트워크

Azure Virtual Network 서비스를 사용하면 Azure 리소스와 가상 네트워크(VNet)를 서로 안전하게 연결할 수 있습니다. VNet은 클라우드의 사용자 네트워크를 나타내는 표현입니다. 구독 전용 Azure 클라우드를 논리적으로 격리한 것이 VNet입니다. 또한 온-프레미스 네트워크에 VNet을 연결할 수도 있습니다. 다음 그림은 Azure Virtual Network 서비스의 기능 중 일부를 보여 줍니다.

![네트워크 다이어그램](./media/virtual-networks-overview/virtual-network-overview.png)

다음 Azure Virtual Network 기능에 대한 자세한 내용을 보려면 해당 기능을 클릭합니다.
- **[격리:](#isolation)** VNet은 서로 완전히 격리됩니다. 동일한 CIDR 주소 블록을 사용하는 개발, 테스트 및 프로덕션 환경에 대해 개별 VNet을 만들 수 있습니다. 반대로, 다른 CIDR 주소 블록을 사용하고 네트워크를 함께 연결하는 여러 VNet을 만들 수도 있습니다. VNet을 여러 서브넷으로 분할할 수 있습니다. Azure는 VNet에 연결되는 VM 및 클라우드 서비스 역할 인스턴스에 대한 내부 이름 확인을 제공합니다. 필요에 따라 Azure 내부 이름 확인을 사용하는 대신, 고유한 DNS 서버를 사용하도록 VNet을 구성할 수 있습니다.
- **[인터넷 연결:](#internet)** VNet에 연결되는 모든 Azure VM(Virtual Machines) 및 클라우드 서비스 역할 인스턴스는 기본적으로 인터넷에 액세스할 수 있습니다. 또한 필요에 따라 특정 리소스에 대한 인바운드 액세스를 사용하도록 설정할 수 있습니다.
- **[Azure 리소스 연결:](#within-vnet)** 클라우드 서비스 및 VM과 같은 Azure 리소스를 동일한 VNet에 연결할 수 있습니다. 리소스는 서로 다른 서브넷에 있더라도 개인 IP 주소를 사용하여 서로 연결할 수 있습니다. Azure는 서브넷, VNet 및 온-프레미스 네트워크 간에 기본 라우팅을 제공하므로 경로를 구성하고 관리할 필요가 없습니다.
- **[VNet 연결:](#connect-vnets)** VNet을 서로 연결하여 어떤 VNet에 연결된 리소스도 다른 VNet의 모든 리소스와 통신하도록 할 수 있습니다.
- **[온-프레미스 연결:](#connect-on-premises)** VNet을 네트워크와 Azure 간의 개인 네트워크 연결을 통해 또는 인터넷의 사이트 간 VPN 연결을 통해 온-프레미스 네트워크에 연결할 수 있습니다.
- **[트래픽 필터링:](#filtering)** VM 및 클라우드 서비스 역할 인스턴스 네트워크 트래픽을 원본 IP 주소 및 포트, 대상 IP 주소 및 포트 및 프로토콜별로 인바운드 및 아웃바운드에 따라 필터링할 수 있습니다.
- **[라우팅:](#routing)** 자체 경로를 구성하여 또는 네트워크 게이트웨이 통해 BGP 경로를 사용하여 Azure의 기본 라우팅을 선택적으로 재정의할 수 있습니다.

## <a name = "isolation"></a>네트워크 격리 및 분할

각 Azure 구독 및 Azure 지역 내에서 여러 VNet을 구현할 수 있습니다. 각 VNet은 다른 VNet에서 격리됩니다. 각 VNet에 대해 다음을 수행할 수 있습니다.
- 공용 및 개인(RFC 1918) 주소를 사용하여 사용자 지정 개인 IP 주소 공간을 지정합니다. Azure에서는 VNet에 연결된 리소스를 사용자가 할당한 주소 공간의 개인 IP 주소에 할당합니다.
- VNet을 하나 이상의 서브넷으로 분할하고 VNet 주소 공간의 일부를 각 서브넷에 할당합니다.
- Azure 제공 이름 확인을 사용하거나 VNet에 연결된 리소스에서 사용할 수 있도록 자체 DNS 서버를 지정합니다. VNet의 이름 확인에 대한 자세한 내용은 [VM 및 클라우드 서비스의 이름 확인](virtual-networks-name-resolution-for-vms-and-role-instances.md) 문서를 참조하세요.

## <a name = "internet"></a>클러스터에 연결
VNet에 연결된 모든 리소스는 기본적으로 인터넷에 아웃바운드로 연결됩니다. 리소스의 개인 IP 주소는 Azure 인프라에서 공용 IP 주소로 변환하는 원본 네트워크 주소입니다. 아웃바운드 인터넷 연결에 대한 자세한 내용은 [Azure에서 아웃바운드 연결 이해](..\load-balancer\load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json#standalone-vm-with-no-instance-level-public-ip-address) 문서를 참조하세요. 사용자 지정 라우팅 및 트래픽 필터링을 구현하여 기본 연결을 변경할 수 있습니다.

인터넷에서 Azure 리소스에 인바운드로 통신하거나 SNAT 없이 인터넷에 아웃바운드로 통신하려면 공용 IP 주소에 리소스가 할당되어야 합니다. 공용 IP 주소에 대해 자세히 알아보려면 [공용 IP 주소](virtual-network-public-ip-address.md)를 참조하세요.

## <a name="within-vnet"></a>Azure 리소스 연결
VM(가상 컴퓨터), 클라우드 서비스, App Service 환경 및 가상 컴퓨터 크기 집합과 같은 일부 Azure 리소스를 VNet에 연결할 수 있습니다. VM은 NIC(네트워크 인터페이스)를 통해 VNet 내의 서브넷에 연결합니다. NIC에 대한 자세한 내용은 [네트워크 인터페이스](virtual-network-network-interface.md) 문서를 참조하세요.

## <a name="connect-vnets"></a>가상 네트워크 연결

VNet을 서로 연결하여 VNet에 연결된 리소스가 VNet을 통해 서로 통신하도록 할 수 있습니다. 다음 옵션 중 하나 또는 둘 다를 사용하여 VNet을 서로 연결할 수 있습니다.
- **피어링:** 같은 Azure 지역 내 다른 Azure VNet에 연결되어 있는 리소스가 서로 통신할 수 있도록 합니다. VNet 내의 대역폭 및 대기 시간은 리소스가 동일한 VNet에 연결된 경우와 같습니다. 피어링에 대한 자세히 내용은 [가상 네트워크 피어링](virtual-network-peering-overview.md) 문서를 참조하세요.
- **VNet 간 연결:** 리소스가 같거나 다른 Azure 위치 내의 다른 Azure VNet에 연결될 수 있습니다. 피어링과 달리 트래픽이 Azure VPN Gateway를 통과해야 하기 때문에 VNet 간에 대역폭이 제한됩니다. VNet 간 연결을 사용하여 VNet을 연결하는 방법에 대한 자세한 내용은 [VNet 간 연결 구성](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 문서를 참조하세요.

## <a name="connect-on-premises"></a>온-프레미스 네트워크에 연결

다음 옵션을 조합하여 온-프레미스 네트워크를 VNet에 연결할 수 있습니다.
- **지점-사이트 간 VPN(가상 사설망):** 네트워크에 연결된 단일 PC와 VNet 간에 설정됩니다. 이 연결 유형은 기존 네트워크를 거의 변경할 필요가 없으므로 Azure 사용을 막 시작하는 사용자나 개발자에게 유용합니다. 이 연결은 SSTP 프로토콜을 사용하여 PC 및 VNet 간에 인터넷을 통한 암호화된 통신을 제공합니다. 트래픽이 인터넷을 통과하므로 지점-사이트 VPN의 대기 시간은 예측 가능하지 않으며 트래픽이 암호화됩니다.
- **사이트 간 VPN:** VPN 장치 와 Azure VPN Gateway 간에 설정됩니다. 이 연결 유형에서는 권한을 부여한 모든 온-프레미스 리소스에서 VNet에 액세스할 수 있습니다. 이 연결은 온-프레미스 장치와 Azure VPN Gateway 간에 인터넷을 통한 암호화된 통신을 제공하는 IPSec/IKE VPN입니다. 트래픽이 인터넷을 통과하므로 사이트 간 연결의 대기 시간은 예측 가능하지 않습니다.
- **Azure ExpressRoute:** ExpressRoute 파트너를 통해 네트워크와 Azure 간에 설정됩니다. 이 연결은 전용입니다. 트래픽이 인터넷을 통과하지 않습니다. 트래픽이 인터넷을 통과하지 않고 암호화되지 않으므로 ExpressRoute 연결에 대한 대기 시간은 예측 가능합니다.

모든 이전 연결 옵션에 대한 자세한 내용은 [연결 토폴로지 다이어그램](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#a-namediagramsaconnection-topology-diagrams) 문서를 읽어보세요.

## <a name="filtering"></a>네트워크 트래픽 필터링
<!---Get confirmation that a UDR on the gateway subnet is supported. Need to provide some additional info as to the key differences between the two options.--->

다음 옵션 중 하나 또는 둘 다를 사용하여 서브넷 간의 네트워크 트래픽을 필터링할 수 있습니다.
- **NSG(네트워크 보안 그룹):** 각 NSG에는 원본 및 대상 IP 주소, 포트 및 프로토콜을 기준으로 트래픽을 필터링할 수 있는 여러 개의 인바운드 및 아웃바운드 보안 규칙이 포함될 수 있습니다. VM의 각 NIC에 NSG를 적용할 수 있습니다. NIC 또는 Azure 리소스가 연결된 서브넷에 NSG를 적용할 수도 있습니다. NSG에 대한 자세한 내용은 [네트워크 보안 그룹](virtual-networks-nsg.md)을 참조하세요.
- **NVA(네트워크 가상 어플라이언스):** NVA는 방화벽과 같은 네트워크 기능을 수행하는 소프트웨어를 실행하는 VM입니다. [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking?page=1&subcategories=appliances)에서 사용할 수 있는 NVA 목록을 확인하세요. WAN 최적화 및 기타 네트워크 트래픽 기능을 제공하는 NVA도 사용할 수 있습니다. NVA는 일반적으로 사용자 정의 또는 BGP 경로와 함께 사용됩니다. 또한 NVA를 사용하여 VNet 간의 트래픽을 필터링할 수도 있습니다.

## <a name="routing"></a>네트워크 트래픽 라우팅

Azure는 VNet의 모든 서브넷에 연결된 리소스가 기본적으로 서로 통신할 수 있도록 하는 경로 테이블을 만듭니다. 다음 옵션 중 하나 또는 둘 다를 구현하여 Azure에서 생성되는 기본 경로를 재정의할 수 있습니다.
- **사용자 정의 경로:** 각 서브넷에 대해 트래픽이 라우팅되는 위치를 제어하는 경로로 사용자 지정 경로 테이블을 만들 수 있습니다. 사용자 정의 경로에 대해 자세히 알아보려면 [사용자 정의 경로](virtual-networks-udr-overview.md) 문서를 참조하세요.
- **BGP 경로:** Azure VPN Gateway 또는 ExpressRoute 연결을 사용하여 온-프레미스 네트워크에 VNet을 연결하는 경우 VNet으로 BGP 경로를 전파할 수 있습니다.

## <a name="pricing"></a>가격

가상 네트워크, 서브넷, 경로 테이블 또는 네트워크 보안 그룹은 무료입니다. 아웃바운드 인터넷 대역폭 사용량, 공용 IP 주소, 가상 네트워크의 피어링, VPN Gateway 및 ExpressRoute는 각각 자체적인 가격 책정 구조를 갖습니다. 자세한 내용은 [가상 네트워크](https://azure.microsoft.com/pricing/details/virtual-network), [VPN Gateway](https://azure.microsoft.com/pricing/details/vpn-gateway), 및 [ExpressRoute](https://azure.microsoft.com/pricing/details/expressroute) 가격 책정 페이지를 참조하세요.


## <a name="next-steps"></a>다음 단계

- [첫 번째 가상 네트워크 만들기](virtual-network-get-started-vnet-subnet.md) 문서의 단계를 완료하여 첫 번째 VNet을 만들고 일부 VM을 연결합니다.
- [지점 및 사이트 간 연결 구성](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 문서의 단계를 완료하여 VNet에 대해 지점 및 사이트 간 연결을 만듭니다.

