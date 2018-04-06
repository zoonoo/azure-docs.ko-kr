---
title: Azure Virtual Network | Microsoft Docs
description: Azure Virtual Network 개념 및 기능에 대해 알아봅니다.
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 9633de4b-a867-4ddf-be3c-a332edf02e24
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 3/1/2018
ms.author: jdial
ms.openlocfilehash: 8d02afcc590482fdca4705ac582d85bb985dd3c2
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/23/2018
---
# <a name="what-is-azure-virtual-network"></a>Azure Virtual Network란?

Azure Virtual Network에서는 Azure 리소스가 서로 통신하고 인터넷과 통신할 수 있습니다. 가상 네트워크는 Azure 클라우드의 리소스를 다른 리소스와 격리시킵니다. 가상 네트워크를 다른 가상 네트워크 또는 온-프레미스 네트워크에 연결할 수 있습니다. 

Azure Virtual Network는 다음과 같은 광범위한 기능을 제공합니다.
- **[격리:](#isolation)** 가상 네트워크는 서로 격리되어 있습니다. 동일한 CIDR(예: 10.0.0.0/0) 주소 블록을 사용하는 개별 가상 네트워크를 만들어 개발, 테스트, 프로덕션 용도에 이용할 수 있습니다. 반대로, 여러 CIDR 주소 블록을 사용하는 가상 네트워크를 만들어 네트워크를 서로 연결할 수도 있습니다. 가상 네트워크를 여러 서브넷으로 분할할 수 있습니다. Azure는 가상 네트워크에 배포된 리소스에 대한 내부 이름 확인을 제공합니다. 필요에 따라 Azure 내부 이름 확인을 사용하는 대신 고유한 DNS 서버를 사용하도록 가상 네트워크를 구성할 수 있습니다.
- **[인터넷 통신:](#internet)** 가상 네트워크에 배포된 가상 머신과 같은 리소스는 기본적으로 인터넷에 액세스할 수 있습니다. 또한 필요에 따라 특정 리소스에 대한 인바운드 액세스를 사용하도록 설정할 수 있습니다.
- **[Azure 리소스 통신](#within-vnet)**: 가상 네트워크에 배포된 Azure 리소스는 서로 다른 서브넷에 배포된 경우에도 개인 IP 주소를 사용하여 서로 통신할 수 있습니다. Azure는 서브넷, 연결된 가상 네트워크 및 온-프레미스 네트워크 간에 기본 라우팅을 제공하므로 수동으로 경로를 구성하고 관리할 필요가 없습니다. 하지만 원한다면 Azure의 라우팅을 사용자 지정할 수 있습니다.
- **[가상 네트워크 연결:](#connect-vnets)** 가상 네트워크를 서로 연결하면 한 가상 네트워크의 리소스가 다른 가상 네트워크의 리소스와 통신할 수 있습니다.
- **[온-프레미스 연결](#connect-on-premises)**: 가상 네트워크를 온-프레미스 네트워크에 연결하여 리소스가 서로 통신하도록 할 수 있습니다.
- **[트래픽 필터링](#filtering)**: 가상 네트워크의 리소스와의 네트워크 트래픽을 원본 IP 주소 및 포트, 대상 IP 주소 및 포트, 프로토콜 등으로 필터링할 수 있습니다.
- **[라우팅:](#routing)** 필요에 따라 자체 경로를 구성하거나 네트워크 게이트웨이를 통해 BGP(경계 게이트웨이 프로토콜) 경로를 전파하여 Azure의 기본 라우팅을 재정의할 수 있습니다.

## <a name = "isolation"></a>네트워크 격리 및 분할

각 Azure [구독](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription) 및 Azure [지역](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#region) 내에서 여러 가상 네트워크를 구현할 수 있습니다. 각 가상 네트워크는 다른 가상 네트워크와 분리됩니다. 각 가상 네트워크에 대해 다음을 수행할 수 있습니다.
- 공용 및 사설(RFC 1918) 주소를 사용하여 사용자 지정 사설 IP 주소 공간을 지정합니다. Azure는 가상 네트워크의 리소스에 사용자가 할당한 주소 공간의 개인 IP 주소를 할당합니다.
- 가상 네트워크를 하나 이상의 서브넷으로 분할하고 가상 네트워크 주소 공간의 일부를 각 서브넷에 할당합니다.
- Azure에서 제공하는 이름 확인을 사용하거나 가상 네트워크의 리소스가 사용할 자체 DNS 서버를 지정합니다. 가상 네트워크의 이름 확인에 대한 자세한 내용은 [가상 네트워크의 리소스에 대한 이름 확인](virtual-networks-name-resolution-for-vms-and-role-instances.md)을 참조하세요.

## <a name = "internet"></a>인터넷 통신
가상 네트워크의 모든 리소스는 인터넷으로 아웃바운드 통신을 할 수 있습니다. 기본적으로 리소스의 개인 IP 주소는 Azure 인프라에서 선택한 공용 IP 주소로 SNAT(원본 네트워크 주소 변환)가 이루어집니다. 아웃바운드 인터넷 연결에 대한 자세한 내용은 [Azure에서 아웃바운드 연결 이해](..\load-balancer\load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json)를 참조하세요. 아웃바운드 인터넷 연결을 차단하려면 사용자 지정 경로 또는 트래픽 필터링을 구현하면 됩니다.

인터넷에서 Azure 리소스에 인바운드로 통신하거나 SNAT 없이 인터넷에 아웃바운드로 통신하려면 리소스에 공용 IP 주소가 할당되어야 합니다. 공용 IP 주소에 대해 자세히 알아보려면 [공용 IP 주소](virtual-network-public-ip-address.md)를 참조하세요.

## <a name="within-vnet"></a>Azure 리소스 간 보안 통신

가상 네트워크 내에서 가상 머신을 배포할 수 있습니다. 가상 머신은 네트워크 인터페이스를 통해 가상 네트워크의 다른 리소스와 통신합니다. 네트워크 인터페이스에 대한 자세한 내용은 [네트워크 인터페이스](virtual-network-network-interface.md)를 참조하세요.

또한 Azure App Service Environments, Azure Virtual Machine Scale Sets 등의 여러 가지 Azure 리소스를 가상 네트워크에 배포할 수 있습니다. 가상 네트워크에 배포할 수 있는 Azure 리소스의 전체 목록은 [Azure 서비스에 대한 가상 네트워크 서비스 통합](virtual-network-for-azure-services.md)을 참조하세요.

일부 리소스는 가상 네트워크에 배포할 수 없지만 가상 네트워크 내의 리소스로만 통신을 제한할 수 있습니다. 리소스 액세스를 제한하는 방법에 대한 자세한 내용은 [가상 네트워크 서비스 끝점](virtual-network-service-endpoints-overview.md)을 참조하세요. 

## <a name="connect-vnets"></a>가상 네트워크 연결

가상 네트워크를 서로 연결하면 각 가상 네트워크의 리소스가 가상 네트워크 피어링을 사용하여 서로 통신할 수 있습니다. 서로 다른 가상 네트워크에 있는 리소스 간에 이루어지는 통신의 대역폭 및 대기 시간은 리소스가 같은 가상 네트워크에 있을 때와 동일합니다. 피어링에 대한 자세히 내용은 [가상 네트워크 피어링](virtual-network-peering-overview.md)을 참조하세요.

## <a name="connect-on-premises"></a>온-프레미스 네트워크에 연결

다음 옵션을 원하는 대로 조합하여 온-프레미스 네트워크를 가상 네트워크에 연결할 수 있습니다.
- **지점-사이트 간 VPN(가상 사설망):** 네트워크의 가상 네트워크와 단일 PC 사이에 설정됩니다. 가상 네트워크와 연결하려는 각 PC는 독립적으로 연결을 구성해야 합니다. 이 연결 유형은 기존 네트워크를 거의 변경할 필요가 없으므로 Azure을 이제 막 시작하는 사용자나 개발자에게 적합합니다. 이 연결은 SSTP 프로토콜을 사용하여 PC와 가상 네트워크 사이에 인터넷을 통한 암호화된 통신을 제공합니다. 트래픽이 인터넷을 트래버스하므로 지점 및 사이트 간 VPN의 대기 시간은 예측할 수 없습니다.
- **사이트 간 VPN:** VPN 장치와 가상 네트워크에 배포된 Azure VPN Gateway 사이에 설정됩니다. 이 연결 형식을 사용하면 사용자가 권한을 부여하는 모든 온-프레미스 리소스가 가상 네트워크에 액세스할 수 있습니다. 이 연결은 IPSec/IKE VPN으로 온-프레미스 장치와 Azure VPN Gateway 간에 인터넷을 통한 암호화된 통신을 제공합니다. 트래픽이 인터넷을 트래버스하므로 사이트 간 연결의 대기 시간은 예측할 수 없습니다.
- **Azure ExpressRoute:** ExpressRoute 파트너를 통해 네트워크와 Azure 간에 설정됩니다. 이 연결은 사설 전용입니다. 트래픽이 인터넷을 트래버스하지 않습니다. 트래픽이 인터넷을 트래버스하지 않으므로 ExpressRoute 연결에 대한 대기 시간을 예측할 수 있습니다.

모든 이전 연결 옵션에 대한 자세한 내용은 [연결 토폴로지 다이어그램](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#diagrams)을 참조하세요.

## <a name="filtering"></a>네트워크 트래픽 필터링
다음 옵션 중 하나 또는 둘 다를 사용하여 서브넷 간의 네트워크 트래픽을 필터링할 수 있습니다.
- **네트워크 보안 그룹:** 네트워크 보안 그룹에는 원본 및 대상 IP 주소, 포트 및 프로토콜을 기준으로 트래픽을 필터링할 수 있는 여러 개의 인바운드 및 아웃바운드 보안 규칙이 포함될 수 있습니다. 가상 머신의 각 네트워크 인터페이스에 네트워크 보안 그룹을 적용할 수 있습니다. 네트워크 인터페이스 또는 다른 Azure 리소스가 있는 서브넷에 네트워크 보안 그룹을 적용할 수도 있습니다. 네트워크 보안 그룹에 대한 자세한 내용은 [네트워크 보안 그룹](security-overview.md#network-security-groups)을 참조하세요.
- **네트워크 가상 어플라이언스:** 네트워크 가상 어플라이언스는 방화벽과 같은 네트워크 기능을 수행하는 소프트웨어를 실행하는 가상 머신입니다. 사용 가능한 네트워크 가상 어플라이언스 목록은 [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking?page=1&subcategories=appliances)에서 확인할 수 있습니다. WAN 최적화 및 기타 네트워크 트래픽 기능을 제공하는 네트워크 가상 어플라이언스도 사용할 수 있습니다. 네트워크 가상 어플라이언스는 일반적으로 사용자 정의 경로 또는 BGP 경로와 함께 사용됩니다. 네트워크 가상 어플라이언스를 사용하여 가상 네트워크 간 트래픽을 필터링할 수도 있습니다.

## <a name="routing"></a>네트워크 트래픽 라우팅

Azure는 가상 네트워크의 모든 서브넷에 연결된 리소스가 서로 통신하고 인터넷에 연결할 수 있게 해주는 경로 테이블을 기본적으로 생성합니다. 다음 옵션 중 하나 또는 둘 다를 구현하여 Azure에서 생성되는 기본 경로를 재정의할 수 있습니다.
- **경로 테이블:** 각 서브넷에 대해 트래픽이 라우팅되는 위치를 제어하는 경로로 사용자 지정 경로 테이블을 만들 수 있습니다. 사용자 지정 라우팅에 대해 자세히 알아보려면 [사용자 지정 라우팅](virtual-networks-udr-overview.md#user-defined)을 참조하세요.
- **BGP 경로:** Azure VPN Gateway 또는 ExpressRoute 연결을 사용하여 온-프레미스 네트워크에 가상 네트워크를 연결하는 경우 가상 네트워크로 BGP 경로를 전파할 수 있습니다.

## <a name="next-steps"></a>다음 단계

Azure Virtual Network에 대한 개요를 살펴봤습니다. 가상 네트워크를 만들어서 Azure Virtual Machines를 배포하여 Azure Virtual Network의 일부 기능을 활용하는 방법을 알아봅니다.

> [!div class="nextstepaction"]
> [가상 네트워크 만들기](quick-create-portal.md)
