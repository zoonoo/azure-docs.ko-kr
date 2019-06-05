---
title: Azure Virtual Network | Microsoft Docs
description: Azure Virtual Network 개념 및 기능에 대해 알아봅니다.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
tags: azure-resource-manager
Customer intent: As someone with a basic network background that is new to Azure, I want to understand the capabilities of Azure Virtual Network, so that my Azure resources such as VMs, can securely communicate with each other, the internet, and my on-premises resources.
ms.service: virtual-network
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/12/2018
ms.author: kumud
ms.openlocfilehash: 23093cd8bcb5793b9e5b9abc835f64233e666ce1
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/27/2019
ms.locfileid: "66241739"
---
# <a name="what-is-azure-virtual-network"></a>Azure Virtual Network란?

Azure Virtual Network를 사용하면 Azure VM(Virtual Machines)과 같은 다양한 형식의 Azure 리소스가 서로, 인터넷 및 특정 온-프레미스 네트워크와 안전하게 통신할 수 있습니다. 가상 네트워크는 단일 Azure 지역으로 범위가 지정됩니다. Azure [지역](https://azure.microsoft.com/global-infrastructure/regions/)은 대기 시간이 정의된 경계 내에 배치되고, 지역별 짧은 대기 시간 네트워크를 통해 연결된 데이터 센터의 세트입니다. 

가상 네트워크는 서브넷으로 이루어져 있습니다. 서브넷은 가상 네트워크 내 IP 주소의 범위입니다. 가상 네트워크와 같은 서브넷은 단일 Azure 지역으로 범위가 지정됩니다. 

가상 네트워크 피어링을 사용하여 여러 지역의 여러 가상 네트워크를 연결할 수 있습니다.

Azure Virtual Network는 다음과 같은 주요 기능을 제공합니다.

## <a name="isolation-and-segmentation"></a>격리 및 구분

각 Azure [구독](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription) 및 Azure [지역](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#region) 내에서 여러 가상 네트워크를 구현할 수 있습니다. 각 가상 네트워크는 다른 가상 네트워크와 분리됩니다. 각 가상 네트워크에 대해 다음을 수행할 수 있습니다.
- 공용 및 프라이빗(RFC 1918) 주소를 사용하여 사용자 지정 개인 IP 주소 공간을 지정합니다. Azure는 가상 네트워크의 리소스에 사용자가 할당한 주소 공간의 개인 IP 주소를 할당합니다.
- 가상 네트워크를 하나 이상의 서브넷으로 분할하고 가상 네트워크 주소 공간의 일부를 각 서브넷에 할당합니다.
- Azure에서 제공하는 이름 확인을 사용하거나 가상 네트워크의 리소스가 사용할 자체 DNS 서버를 지정합니다.

## <a name="communicate-with-the-internet"></a>인터넷 통신

기본적으로 가상 네트워크의 모든 리소스는 인터넷으로 아웃바운드 통신을 할 수 있습니다. 공용 IP 주소 또는 공용 Load Balancer를 할당하여 리소스에 대해 인바운드로 통신할 수 있습니다. 공용 IP 또는 공용 Load Balancer를 사용하여 아웃바운드 연결을 관리할 수도 있습니다.  Azure의 아웃바운드 연결에 대한 자세한 내용은 [아웃바운드 연결](../load-balancer/load-balancer-outbound-connections.md), [공용 IP 주소](virtual-network-public-ip-address.md) 및 [Load Balancer](../load-balancer/load-balancer-overview.md)를 참조하세요.

>[!NOTE]
>내부 [표준 Load Balancer](../load-balancer/load-balancer-standard-overview.md)만 사용하는 경우 인스턴스 수준 공용 IP 또는 공용 Load Balancer와 함께 작동하도록 [아웃바운드 연결](../load-balancer/load-balancer-outbound-connections.md) 방식을 정의하기 전에는 아웃바운드 연결을 사용할 수 없습니다.

## <a name="communicate-between-azure-resources"></a>Azure 리소스 간 통신

Azure 리소스는 다음 방법 중 하나를 사용하여 서로 안전하게 통신합니다.

- **가상 네트워크를 통해**: Azure App Service Environment, AKS(Azure Kubernetes Service), Azure Virtual Machine Scale Sets 등의 여러 다른 Azure 리소스 및 VM을 가상 네트워크에 배포할 수 있습니다. 가상 네트워크에 배포할 수 있는 Azure 리소스의 전체 목록을 보려면 [가상 네트워크 서비스 통합](virtual-network-for-azure-services.md)을 참조하세요. 
- **가상 네트워크 서비스 엔드포인트를 통해**: 직접 연결을 통해 가상 네트워크 프라이빗 주소 공간 및 가상 네트워크의 ID를 Azure Storage 계정, Azure SQL 데이터베이스 등의 Azure 서비스 리소스로 확장합니다. 서비스 엔드포인트를 사용하면 가상 네트워크에 대해 중요한 Azure 서비스 리소스를 보호할 수 있습니다. 자세한 내용은 [가상 네트워크 서비스 엔드포인트 개요](virtual-network-service-endpoints-overview.md)를 참조하세요.
 
## <a name="communicate-with-on-premises-resources"></a>온-프레미스 리소스와 통신

다음 옵션을 원하는 대로 조합하여 온-프레미스 컴퓨터 및 네트워크를 가상 네트워크에 연결할 수 있습니다.

- **지점 및 사이트 간 VPN(가상 사설망):** 가상 네트워크와 사용자 네트워크의 단일 컴퓨터 간에 설정됩니다. 가상 네트워크와 연결하려는 각 컴퓨터는 연결을 구성해야 합니다. 이 연결 유형은 기존 네트워크를 거의 변경할 필요가 없으므로 Azure을 이제 막 시작하는 사용자나 개발자에게 적합합니다. 컴퓨터와 가상 네트워크 간의 통신은 인터넷을 통한 암호화된 터널로 전송됩니다. 자세한 내용은 [지점-사이트 간 VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#P2S)을 참조하세요.
- **사이트 간 VPN:** 온-프레미스 VPN 디바이스와 가상 네트워크에 배포된 Azure VPN Gateway 간에 설정됩니다. 이 연결 형식을 사용하면 사용자가 권한을 부여하는 모든 온-프레미스 리소스가 가상 네트워크에 액세스할 수 있습니다. 온-프레미스 VPN 디바이스 및 Azure VPN Gateway 간의 통신은 인터넷을 통해 암호화된 터널로 전송됩니다. 자세한 내용은 [사이트 간 VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#s2smulti)을 참조하세요.
- **Azure ExpressRoute:** ExpressRoute 파트너를 통해 사용자 네트워크와 Azure 간에 설정됩니다. 이 연결은 프라이빗 전용입니다. 트래픽은 인터넷을 통해 이동하지 않습니다. 자세한 내용은 [ExpressRoute](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#ExpressRoute)를 참조하세요.

## <a name="filter-network-traffic"></a>네트워크 트래픽 필터링
다음 옵션 중 하나 또는 둘 다를 사용하여 서브넷 간의 네트워크 트래픽을 필터링할 수 있습니다.
- **보안 그룹:** 네트워크 보안 그룹 및 애플리케이션 보안 그룹에는 원본 및 대상 IP 주소, 포트 및 프로토콜을 기준으로 리소스와 주고받는 트래픽을 필터링할 수 있는 여러 개의 인바운드 및 아웃바운드 보안 규칙이 포함될 수 있습니다. 자세한 내용은 [네트워크 보안 그룹](security-overview.md#network-security-groups) 또는 [애플리케이션 보안 그룹](security-overview.md#application-security-groups)을 참조하세요.
- **네트워크 가상 어플라이언스:** 네트워크 가상 어플라이언스는 방화벽, WAN 최적화 또는 기타 네트워크 기능과 같은 네트워크 기능을 수행하는 VM입니다. Azure 가상 네트워크에 배포할 수 있는 네트워크 가상 어플라이언스 목록을 보려면 [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking?page=1&subcategories=appliances)를 참조하세요.

## <a name="route-network-traffic"></a>네트워크 트래픽 라우팅

기본적으로 서브넷, 연결된 가상 네트워크, 온-프레미스 네트워크 및 인터넷 간의 Azure 경로 트래픽입니다. 다음 옵션 중 하나 또는 둘 다를 구현하여 Azure에서 생성되는 기본 경로를 재정의할 수 있습니다.
- **경로 테이블:** 각 서브넷에 대해 트래픽이 라우팅되는 위치를 제어하는 경로를 사용하여 사용자 지정 경로 테이블을 만들 수 있습니다. [경로 테이블](virtual-networks-udr-overview.md#user-defined)에 대해 자세히 알아보세요.
- **BGP(Border Gateway Protocol) 경로:** Azure VPN Gateway 또는 ExpressRoute 연결을 사용하여 가상 네트워크를 온-프레미스 네트워크에 연결하는 경우 온-프레미스 BGP 경로를 가상 네트워크에 전파할 수 있습니다. [Azure VPN Gateway](../vpn-gateway/vpn-gateway-bgp-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 및 [ExpressRoute](../expressroute/expressroute-routing.md?toc=%2fazure%2fvirtual-network%2ftoc.json#dynamic-route-exchange)에서 BGP를 사용하는 방법을 자세히 알아봅니다.

## <a name="connect-virtual-networks"></a>가상 네트워크 연결

가상 네트워크를 서로 연결하면 각 가상 네트워크의 리소스가 가상 네트워크 피어링을 사용하여 서로 통신할 수 있습니다. 연결한 가상 네트워크는 같은 Azure 지역 또는 다른 Azure 지역에 있을 수 있습니다. 자세히 알아보려면 [가상 네트워크 피어링](virtual-network-peering-overview.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계

Azure Virtual Network에 대한 개요를 살펴봤습니다. 가상 네트워크를 사용하여 시작하려면 가상 네트워크를 만들고, 몇몇 VM을 배포하고 VM 간에 통신합니다. 방법을 자세히 알아보려면 [가상 네트워크 만들기](quick-create-portal.md) 빠른 시작을 참조하세요.
