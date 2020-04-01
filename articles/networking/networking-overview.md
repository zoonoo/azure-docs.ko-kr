---
title: Azure 네트워킹 서비스 개요
description: Azure의 네트워킹 서비스와 연결 서비스, 응용 프로그램 보호 서비스, 응용 프로그램 배달 서비스 및 네트워크 모니터링의 기능에 대해 알아봅니다.
services: networking
documentationcenter: na
author: KumudD
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.workload: infrastructure-services
ms.date: 03/12/2020
ms.author: kumud
ms.openlocfilehash: c6b845eda1df39ccf5e4b2b1d6a615f3bc932b66
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/01/2020
ms.locfileid: "80474944"
---
# <a name="azure-networking-services-overview"></a>Azure 네트워킹 서비스 개요

Azure의 네트워킹 서비스는 함께 또는 별도로 사용할 수 있는 다양한 네트워킹 기능을 제공합니다. 다음과 같은 주요 기능에 대해 알아보려면 그 중 하나를 클릭합니다.
- [**연결 서비스**](#connect): Azure - VNet(가상 네트워크), 가상 WAN, ExpressRoute, VPN 게이트웨이, 가상 네트워크 NAT 게이트웨이, Azure DNS, 피어링 서비스 및 Azure Bastion에서 이러한 네트워킹 서비스의 조합 또는 조합을 사용하여 Azure 리소스및 온-프레미스 리소스를 연결합니다.
- [**응용 프로그램 보호 서비스**](#protect) Azure - 개인 링크, DDoS 보호, 방화벽, 네트워크 보안 그룹, 웹 응용 프로그램 방화벽 및 가상 네트워크 엔드포인트에서 이러한 네트워킹 서비스의 조합 또는 조합을 사용하여 응용 프로그램을 보호합니다.
- [**애플리케이션 전송 서비스**](#deliver) Azure - CDN( 콘텐츠 배달 네트워크), Azure 정문 서비스, 트래픽 관리자, 응용 프로그램 게이트웨이, 인터넷 분석기 및 로드 밸러커에서 이러한 네트워킹 서비스의 조합 또는 이러한 네트워킹 서비스를 사용하여 Azure 네트워크에서 응용 프로그램을 제공합니다.
- [**네트워크 모니터링**](#monitor) - Azure - 네트워크 감시자, ExpressRoute 모니터, Azure 모니터 또는 VNet 터미널 액세스 포인트(TAP)에서 이러한 네트워킹 서비스의 조합 또는 조합을 사용하여 네트워크 리소스를 모니터링합니다.

## <a name="connectivity-services"></a><a name="connect"></a>연결 서비스
 
이 섹션에서는 Azure 리소스 간의 연결, 온-프레미스 네트워크에서 Azure 리소스로의 연결, Azure - VNet(가상 네트워크), 가상 WAN, ExpressRoute, VPN 게이트웨이, 가상 네트워크 NAT 게이트웨이, Azure DNS, Azure 피어링 서비스 및 Azure Bastion에서 분기연결을 제공하는 서비스에 대해 설명합니다.

|서비스|왜 사용합니까?|시나리오|
|---|---|---|
|[가상 네트워크](#vnet)|Azure 리소스가 서로 인터넷 및 온-프레미스 네트워크와 안전하게 통신할 수 있도록 합니다.| <p>[네트워크 트래픽 필터링](../virtual-network/tutorial-filter-network-traffic.md)</p> <p>[네트워크 트래픽 라우팅](../virtual-network/tutorial-create-route-table-portal.md)</p> <p>[리소스에 대한 네트워크 액세스 제한](../virtual-network/tutorial-restrict-network-access-to-resources.md)</p> <p>[가상 네트워크 연결](../virtual-network/tutorial-connect-virtual-networks-portal.md)</p>|
|[ExpressRoute](#expressroute)|온-프레미스 네트워크를 연결 공급자가 용이하게 하는 개인 연결을 통해 Microsoft 클라우드로 확장합니다.|<p>[ExpressRoute 회로 만들기 및 수정](../expressroute/expressroute-howto-circuit-portal-resource-manager.md)</p> <p>[ExpressRoute 회로의 피어링 만들기 및 수정](../expressroute/expressroute-howto-routing-portal-resource-manager.md)</p> <p>[VNet을 ExpressRoute 회로에 연결](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md)</p> <p>[ExpressRoute 회로에 대한 경로 필터 구성 및 관리](../expressroute/how-to-routefilter-portal.md)</p>|
|[VPN Gateway](#vpngateway)|Azure 가상 네트워크와 공용 인터넷을 통해 온-프레미스 위치 간에 암호화된 트래픽을 보냅니다.|<p>[사이트 간 연결](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md)</p> <p>[VNet-VNet 연결](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)</p> <p>[지점 및 사이트 간 연결](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md)</p>|
|[가상 WAN](#virtualwan)|Azure에 대한 분기 연결을 최적화하고 자동화합니다. Azure 지역은 분기를 연결하도록 선택할 수 있는 허브 역할을 합니다.|<p>[사이트 간 연결,](../virtual-wan/virtual-wan-site-to-site-portal.md) [익스프레스루트 연결](../virtual-wan/virtual-wan-expressroute-portal.md)</p>|
|[Azure DNS](#dns)|Microsoft Azure 인프라를 사용하여 이름 확인을 제공하는 DNS 도메인을 호스팅합니다.|<p>[Azure DNS에서 도메인 호스트](../dns/dns-delegate-domain-azure-dns.md)</p><p>[웹 앱에 대한 DNS 레코드 만들기](../dns/dns-web-sites-custom-domain.md)</p> <p>[트래픽 관리자에 대한 별칭 레코드 만들기](../dns/tutorial-alias-tm.md)</p> <p>[공용 IP 주소에 대한 별칭 레코드 만들기](../dns/tutorial-alias-pip.md)</p> <p>[영역 리소스 레코드에 대한 별칭 레코드 만들기](../dns/tutorial-alias-rr.md)</p>|
|[Azure Bastion](#bastion)|TLS를 통해 Azure 포털에서 가상 시스템에 직접 안전하고 원활한 RDP/SSH 연결을 구성합니다. Azure Bastion을 통해 연결할 때 가상 시스템에 공용 IP 주소가 필요하지 않습니다.|<p>[Azure Bastion 호스트 만들기](../bastion/bastion-create-host-portal.md)</p><p>[SSH를 사용하여 리눅스 VM에 연결](../bastion/bastion-connect-vm-ssh.md)</p><p>[RDP를 사용하여 Windows VM에 연결](../bastion/bastion-connect-vm-rdp.md)</p>|
|[가상 네트워크 NAT 게이트웨이](#nat)|NAT 게이트웨이를 만들어 가상 시스템에 대한 아웃바운드 연결을 제공합니다.|<p>[NAT 게이트웨이 만들기](../virtual-network/quickstart-create-nat-gateway-portal.md)</p>|
|[Azure 피어링 서비스(미리 보기)](#azurepeeringservice)|서비스 프로바이더와 협력하여 공용 네트워크를 통해 Microsoft 클라우드로 의 한 최적의 신뢰할 수 있는 라우팅을 제공합니다.|<p>[Azure 피어링 서비스 등록](../peering-service/azure-portal.md)</p>|
||||


### <a name="virtual-network"></a><a name="vnet"></a>가상 네트워크

Azure Virtual Network(VNet)는 Azure의 프라이빗 네트워크의 기본 구성 요소입니다. VNet을 사용하여 다음을 수행할 수 있습니다.
- **Azure 리소스 간 통신**: Azure 앱 서비스 환경, AZURE Kubernetes 서비스(AKS) 및 Azure 가상 시스템 확장 집합과 같은 여러 다른 유형의 Azure 리소스를 가상 네트워크에 배포할 수 있습니다. 가상 네트워크에 배포할 수 있는 Azure 리소스의 전체 목록을 보려면 [가상 네트워크 서비스 통합](../virtual-network/virtual-network-for-azure-services.md)을 참조하세요.
- **서로 통신**: 가상 네트워크를 서로 연결하여 가상 네트워크 피어링을 사용하여 가상 네트워크의 리소스가 서로 통신할 수 있도록 합니다. 연결한 가상 네트워크는 같은 Azure 지역 또는 다른 Azure 지역에 있을 수 있습니다. 자세한 내용은 [가상 네트워크 피어링](../virtual-network/virtual-network-peering-overview.md)을 참조하세요.
- **인터넷과 통신**: VNet의 모든 리소스는 기본적으로 기본적으로 인터넷으로 아웃바운드통신을 할 수 있습니다. 공용 IP 주소 또는 공용 Load Balancer를 할당하여 리소스에 대해 인바운드로 통신할 수 있습니다. 공용 IP [주소](../virtual-network/virtual-network-public-ip-address.md) 또는 공용 [로드 밸런서를](../load-balancer/load-balancer-overview.md) 사용하여 아웃바운드 연결을 관리할 수도 있습니다.
- **온-프레미스 네트워크와 통신**: [VPN 게이트웨이](../vpn-gateway/vpn-gateway-about-vpngateways.md) 또는 [ExpressRoute를](../expressroute/expressroute-introduction.md)사용하여 온-프레미스 컴퓨터 및 네트워크를 가상 네트워크에 연결할 수 있습니다.

자세한 내용은 [Azure 가상 네트워크란 무엇입니까?](../virtual-network/virtual-networks-overview.md)

### <a name="expressroute"></a><a name="expressroute"></a>ExpressRoute
ExpressRoute를 사용하면 연결 공급자가 지원하는 개인 연결을 통해 온-프레미스 네트워크를 Microsoft 클라우드로 확장할 수 있습니다. 이 연결은 프라이빗 전용입니다. 트래픽은 인터넷을 통해 이동하지 않습니다. ExpressRoute를 사용하면 Microsoft Azure, Office 365 및 Dynamics 365와 같은 Microsoft 클라우드 서비스에 대한 연결을 설정할 수 있습니다.  자세한 내용은 [ExpressRoute란 무엇입니까?](../expressroute/expressroute-introduction.md)

![Azure ExpressRoute](./media/networking-overview/expressroute-connection-overview.png)

### <a name="vpn-gateway"></a><a name="vpngateway"></a>VPN Gateway
VPN 게이트웨이를 사용하면 온-프레미스 위치에서 가상 네트워크에 대한 암호화된 크로스-프레미스 연결을 만들거나 VNet 간에 암호화된 연결을 만들 수 있습니다. VPN 게이트웨이 연결에는 사이트 간, 지점 간 또는 VNet에서 VNet으로 의 다양한 구성이 제공됩니다.
다음 다이어그램은 동일한 가상 네트워크에 대한 여러 사이트 간 VPN 연결을 보여 줍니다.

![사이트 간 Azure VPN 게이트웨이 연결](./media/networking-overview/vpngateway-multisite-connection-diagram.png)

다양한 유형의 VPN 연결에 대한 자세한 내용은 [VPN 게이트웨이](../vpn-gateway/vpn-gateway-about-vpngateways.md)를 참조하십시오.

### <a name="virtual-wan"></a><a name="virtualwan"></a>가상 WAN
Azure Virtual WAN은 Azure를 통해 최적화된 자동 분기 연결을 제공하는 네트워킹 서비스입니다. Azure 지역은 분기를 연결하도록 선택할 수 있는 허브 역할을 합니다. 또한 Azure 백본을 활용하여 분기를 연결하고 분기 및 VNet 간 연결을 설정할 수 있습니다. Azure Virtual WAN은 사이트 간 VPN, ExpressRoute, 지점 간 사용자 VPN과 같은 많은 Azure 클라우드 연결 서비스를 단일 운영 인터페이스로 통합합니다. Azure VNet에 대한 연결은 가상 네트워크 연결을 사용하여 설정합니다. 자세한 내용은 [Azure 가상 WAN이란 무엇입니까?](../virtual-wan/virtual-wan-about.md)

![Virtual WAN 다이어그램](./media/networking-overview/virtualwan1.png)

### <a name="azure-dns"></a><a name="dns"></a>Azure DNS
Azure DNS는 Microsoft Azure 인프라를 사용하여 이름 확인을 제공하는 DNS 도메인에 대한 호스팅 서비스입니다. Azure에 도메인을 호스트하면 다른 Azure 서비스와 동일한 자격 증명, API, 도구 및 대금 청구를 사용하여 DNS 레코드를 관리할 수 있습니다. 자세한 내용은 [Azure DNS란 무엇입니까?](../dns/dns-overview.md)

### <a name="azure-bastion"></a><a name="bastion"></a>Azure Bastion
Azure Bastion 서비스는 가상 네트워크 내에서 프로비저닝하는 새로운 완전 플랫폼 관리형 PaaS 서비스입니다. TLS를 통해 Azure 포털에서 가상 시스템에 직접 안전하고 원활한 RDP/SSH 연결을 제공합니다. Azure Bastion을 통해 연결하는 경우에는 가상 머신에 공용 IP 주소가 필요하지 않습니다. 자세한 내용은 [Azure 요새란 무엇입니까?](../bastion/bastion-overview.md)

![Azure 요새 아키텍처](./media/networking-overview/architecture.png)

### <a name="virtual-network-nat-gateway"></a><a name="nat"></a>가상 네트워크 NAT 게이트웨이
가상 네트워크 NAT(네트워크 주소 변환)는 가상 네트워크에 대한 아웃바운드 전용 인터넷 연결을 간소화합니다. 서브넷에서 구성할 때 모든 아웃바운드 연결은 지정된 정적 공용 IP 주소를 사용합니다. 아웃바운드 연결은 로드 밸런서 또는 공용 IP 주소가 가상 시스템에 직접 연결되지 않고도 가능합니다. 자세한 내용은 [가상 네트워크 NAT 게이트웨이란 무엇입니까?](../virtual-network/nat-overview.md) 

![가상 네트워크 NAT 게이트웨이](./media/networking-overview/flow-map.png)

### <a name="azure-peering-service"></a><a name="azurepeeringservice"></a>Azure 피어링 서비스
Azure 피어링 서비스는 Office 365, Dynamics 365, SaaS(서비스로서의 소프트웨어) 서비스, Azure 또는 공용 인터넷을 통해 액세스할 수 있는 모든 Microsoft 서비스와 같은 Microsoft 클라우드 서비스에 대한 고객 연결을 향상시킵니다. 자세한 내용은 [Azure 피어링 서비스란 무엇입니까?](../peering-service/about.md)

## <a name="application-protection-services"></a><a name="protect"></a>응용 프로그램 보호 서비스

이 섹션에서는 Azure의 네트워킹 서비스- Azure에서 이러한 네트워킹 서비스의 중 또는 이러한 네트워킹 서비스를 사용하여 응용 프로그램을 보호합니다.

|서비스|왜 사용합니까?|시나리오|
|---|---|---|
|[DDoS 보호](#ddosprotection) |과도한 IP 트래픽 요금으로부터 보호하여 애플리케이션을 위한 고가용성|[Azure DDoS 보호 관리](../virtual-network/manage-ddos-protection.md)|
|[웹 응용 프로그램 방화벽](#waf)|<p>[응용 프로그램 게이트웨이를 갖춘 Azure WAF는](../web-application-firewall/ag/ag-overview.md) 공용 및 개인 주소 공간의 엔터티에 대한 지역 보호를 제공합니다.</p><p>[전면 도어가 있는 Azure WAF는](../web-application-firewall/afds/afds-overview.md) 네트워크 에지에서 공용 끝점에 대한 보호를 제공합니다.</p>|<p>[봇 보호 규칙 구성](../frontdoor/waf-front-door-policy-configure-bot-protection.md)</p> <p>[사용자 지정 응답 코드 구성](../frontdoor/waf-front-door-configure-custom-response-code.md)</p> <p>[IP 제한 규칙 구성](../frontdoor/waf-front-door-configure-ip-restriction.md)</p> <p>[속도 제한 규칙 구성](../frontdoor/waf-front-door-rate-limit-powershell.md)</p> |
|[Azure Firewall](#firewall)|Azure Firewall은 Azure Virtual Network 리소스를 보호하는 관리되는 클라우드 기반 네트워크 보안 서비스입니다. 고가용성 및 무제한 클라우드 확장성이 내장되어 있는 서비스 형태의 완전한 상태 저장 방화벽입니다.|<p>[Vnet에 Azure 방화벽 배포](../firewall/tutorial-firewall-deploy-portal.md)</p> <p>[- 하이브리드 네트워크에 Azure 방화벽 배포](../firewall/tutorial-hybrid-ps.md)</p> <p>[Azure 방화벽 DNAT를 사용하여 인바운드 트래픽 필터링](../firewall/tutorial-firewall-dnat.md)</p>|
|[네트워크 보안 그룹](#nsg)|모든 네트워크 트래픽 흐름에 대한 VM/서브넷의 전체 세분화된 분산 엔드 노드 제어|[네트워크 보안 그룹을 사용하여 네트워크 트래픽 필터링](../virtual-network/tutorial-filter-network-traffic.md)|
|[가상 네트워크 서비스 엔드포인트](#serviceendpoints)|일부 Azure 서비스 리소스에 대한 네트워크 액세스를 가상 네트워크 서브넷으로 제한할 수 있습니다.|[PaaS 리소스에 대한 네트워크 액세스 제한](../virtual-network/tutorial-restrict-network-access-to-resources-powershell.md)|
[Private Link](#privatelink)|가상 네트워크의 개인 끝점을 통해 Azure PaaS 서비스(예: Azure 저장소 및 SQL 데이터베이스) 및 Azure 호스팅 고객 소유/파트너 서비스에 액세스할 수 있습니다.|<p>[프라이빗 엔드포인트 만들기](../private-link/create-private-endpoint-portal.md)</p><p>[비공개 링크 서비스 만들기](../private-link/create-private-link-service-portal.md)</p>|
|||
### <a name="ddos-protection"></a><a name="ddosprotection"></a>DDoS Protection 
[Azure DDoS 보호는](../virtual-network/manage-ddos-protection.md) 가장 정교한 DDoS 위협에 대한 대책을 제공합니다. 이 서비스는 가상 네트워크에 배포된 응용 프로그램 및 리소스에 대해 향상된 DDoS 완화 기능을 제공합니다. 또한 Azure DDoS 보호를 사용하는 고객은 적극적인 공격 중에 DDoS 전문가를 참여시키기 위해 DDoS 신속 응답 지원에 액세스할 수 있습니다.

![DDoS Protection](./media/networking-overview/ddos-protection.png)

### <a name="web-application-firewall"></a><a name="waf"></a>웹 응용 프로그램 방화벽

[WAF(Azure Web Application 방화벽)는](../web-application-firewall/overview.md) SQL 주입 및 교차 사이트 스크립팅과 같은 일반적인 웹 익스플로잇 및 취약점으로부터 웹 응용 프로그램을 보호합니다. Azure WAF는 관리되는 규칙을 통해 OWASP 상위 10개 취약점으로부터 즉시 보호합니다. 또한 고객은 소스 IP 범위에 따라 추가 보호를 제공하고 헤더, 쿠키, 양식 데이터 필드 또는 쿼리 문자열 매개 변수와 같은 속성을 요청하기 위해 고객 관리 규칙인 사용자 지정 규칙을 구성할 수도 있습니다.

고객은 공용 및 개인 주소 공간의 엔터티에 지역 보호를 제공하는 [응용 프로그램 게이트웨이를 사용하여 Azure WAF를](../application-gateway/waf-overview.md) 배포하도록 선택할 수 있습니다. 고객은 네트워크 에지에서 공용 끝점에 대한 보호를 제공하는 [Front Door를 사용하여 Azure WAF를](../frontdoor/waf-overview.md) 배포할 수도 있습니다.

![웹 애플리케이션 방화벽](./media/networking-overview/waf-overview.png)


### <a name="azure-firewall"></a><a name="firewall"></a>Azure Firewall
Azure Firewall은 Azure Virtual Network 리소스를 보호하는 관리되는 클라우드 기반 네트워크 보안 서비스입니다. Azure 방화벽을 사용하면 구독 및 가상 네트워크 전체에서 응용 프로그램 및 네트워크 연결 정책을 중앙에서 만들거나 적용하고 기록할 수 있습니다. Azure Firewall은 가상 네트워크 리소스에 정적 공용 IP 주소를 사용하기 때문에 외부 방화벽이 사용자의 가상 네트워크에서 시작된 트래픽을 식별할 수 있습니다. 

Azure 방화벽에 대한 자세한 내용은 [Azure 방화벽 설명서를](../firewall/overview.md)참조하십시오.

![방화벽 개요](./media/networking-overview/firewall-threat.png)

### <a name="network-security-groups"></a><a name="nsg"></a>네트워크 보안 그룹
Azure 가상 네트워크의 Azure 리소스와 네트워크 보안 그룹이 주고 받는 네트워크 트래픽을 필터링할 수 있습니다. 자세한 내용은 [보안 개요](../virtual-network/security-overview.md)를 참조하세요.

### <a name="service-endpoints"></a><a name="serviceendpoints"></a>서비스 끝점
VNet(Virtual Network) 서비스 엔드포인트는 직접 연결을 통해 가상 네트워크 프라이빗 주소 공간 및 Azure 서비스에 대한 VNet의 ID를 확장합니다. 엔드포인트를 사용하면 가상 네트워크에 대해 중요한 Azure 서비스 리소스를 보호할 수 있습니다. VNet에서 Azure 서비스에 대한 트래픽은 Microsoft Azure 백본 네트워크에 항상 유지됩니다. 자세한 내용은 [가상 네트워크 서비스 엔드포인트](../virtual-network/virtual-network-service-endpoints-overview.md)를 참조하세요.

![가상 네트워크 서비스 엔드포인트](./media/networking-overview/vnet-service-endpoints-overview.png)

### <a name="azure-private-link"></a><a name="privatelink"></a>Azure Private Link
[Azure 개인 링크를](../private-link/private-link-overview.md) 사용하면 가상 네트워크의 개인 끝점을 통해 Azure PaaS 서비스(예: Azure 저장소 및 SQL 데이터베이스) 및 Azure 호스팅 고객 소유/파트너 서비스에 액세스할 수 있습니다.
가상 네트워크와 서비스 간의 트래픽은 Microsoft 백본 네트워크를 이동합니다. 공용 인터넷에 서비스를 노출하는 것은 더 이상 필요하지 않습니다. 가상 네트워크에서 사용자 고유의 개인 링크 서비스를 만들어 고객에게 제공할 수 있습니다.

![프라이빗 엔드포인트 개요](./media/networking-overview/private-endpoint.png)


## <a name="application-delivery-services"></a><a name="deliver"></a>애플리케이션 전송 서비스

이 섹션에서는 네트워크 감시자, ExpressRoute 모니터, Azure 모니터 또는 VNet 터미널 액세스 포인트(TAP)와 같은 응용 프로그램을 제공하는 데 도움이 되는 Azure의 네트워킹 서비스에 대해 설명합니다.

|서비스|왜 사용합니까?|시나리오|
|---|---|---|
|[Content Delivery Network](#cdn)|사용자에게 고대역폭 콘텐츠를 제공합니다. CDN은 최종 사용자와 가까운 POP(현재 위치 시점) 위치에 있는 에지 서버에 캐시된 콘텐츠를 저장하여 대기 시간을 최소화합니다.|<p>[웹 앱에 CDN 추가](../cdn/cdn-add-to-web-app.md)</p> <p>[- HTTPS를 통해 Azure CDN 사용자 정의 도메인을 사용하여 액세스 저장소 Blobs](..//cdn/cdn-storage-custom-domain-https.md)</p> <p>[Azure CDN 끝점에 사용자 지정 도메인 추가](../cdn/cdn-map-content-to-custom-domain.md)</p> <p>[Azure CDN 사용자 지정 도메인에서 HTTPS 구성](../cdn/cdn-custom-ssl.md?tabs=option-1-default-enable-https-with-a-cdn-managed-certificate)</p>|
|[Azure Front Door Service](#frontdoor)|최고의 성능을 최적화하고 고가용성을 위해 즉각적인 글로벌 장애 조치(failover)를 최적화하여 웹 트래픽에 대한 글로벌 라우팅을 정의, 관리 및 모니터링할 수 있습니다.|<p>[Azure Front Door Service에 사용자 지정 도메인 추가](../frontdoor/front-door-custom-domain.md)</p> <p>[Front Door 사용자 지정 도메인에서 HTTPS 구성](../frontdoor/front-door-custom-domain-https.md)</p><p>[지역 필터링 웹 응용 프로그램 방화벽 정책 설정](../frontdoor/front-door-tutorial-geo-filtering.md)|
|[Traffic Manager](#trafficmanager)|고가용성 및 응답성을 제공하면서 DNS를 기반으로 트래픽을 글로벌 Azure 리전의 서비스에 배포합니다.|<p> [짧은 대기 시간을 위해 트래픽 라우팅](../traffic-manager/tutorial-traffic-manager-improve-website-response.md)</p><p>[우선순위 엔드포인트로 트래픽 라우팅](../traffic-manager/traffic-manager-configure-priority-routing-method.md)</p><p> [가중된 엔드포인트를 사용하여 트래픽 제어](../traffic-manager/tutorial-traffic-manager-weighted-endpoint-routing.md)</p><p>[끝점의 지리적 위치를 기반으로 트래픽 경로](../traffic-manager/traffic-manager-configure-geographic-routing-method.md)</p> <p> [사용자의 서브넷에 따라 트래픽 라우팅](../traffic-manager/tutorial-traffic-manager-subnet-routing.md)</p>|
|[Load Balancer](#loadbalancer)|가용성 영역과 VNet에 트래픽을 라우팅하여 지역 부하 분산을 제공합니다. 리소스 간 및 리소스 간에 트래픽을 라우팅하여 지역 응용 프로그램을 빌드하여 내부 부하 분산을 제공합니다.|<p> [VM에 내부 트래픽 부하 분산](../load-balancer/tutorial-load-balancer-standard-manage-portal.md)</p> <p>[가상 네트워크 내의 VM 에서 부하 균형 트래픽](../load-balancer/tutorial-load-balancer-basic-internal-portal.md)<p>[특정 VM의 특정 포트로 트래픽을 전달](../load-balancer/tutorial-load-balancer-port-forwarding-portal.md)</p><p> [로드 분산 및 아웃바운드 규칙 구성](../load-balancer/configure-load-balancer-outbound-cli.md)</p>|
|[Application Gateway](#applicationgateway)|Azure Application Gateway는 웹 애플리케이션에 대한 트래픽을 관리할 수 있도록 하는 웹 트래픽 부하 분산 장치입니다.|<p>[Azure Application Gateway를 통해 웹 트래픽 보내기](../application-gateway/quick-create-portal.md)</p><p>[자습서: Azure 포털을 사용하여 TLS 종료를 사용하여 응용 프로그램 게이트웨이를 구성합니다.](../application-gateway/create-ssl-portal.md)</p><p>[URL 경로 기반 리디렉션으로 애플리케이션 게이트웨이 만들기](../application-gateway/create-url-route-portal.md) </p>|
|

### <a name="content-delivery-network"></a><a name="cdn"></a>Content Delivery Network
Azure CDN(콘텐츠 전송 네트워크)은 전 세계에 전략적으로 배치된 물리적 노드에서 콘텐츠를 캐싱하여 사용자에게 고대역폭 콘텐츠를 신속하게 전송할 수 있는 글로벌 솔루션을 개발자에게 제공합니다. Azure CDN에 대한 자세한 내용은 [Azure 콘텐츠 배달 네트워크를](../cdn/cdn-overview.md) 참조하십시오.

![Azure CDN](./media/networking-overview/cdn-overview.png)

### <a name="azure-front-door-service"></a><a name="frontdoor"></a>Azure 정문 서비스
Azure Front Door Service를 사용하면 최적의 성능과 고가용성을 지원하는 즉시 글로벌 장애 조치(failover)를 최적으로 구현하여 웹 트래픽의 글로벌 라우팅을 정의, 관리, 모니터링할 수 있습니다. Front Door를 사용하면 글로벌(다중 지역) 소비자 및 기업 애플리케이션을 글로벌 Azure 잠재 고객에게 도달하는 견고한 고성능의 맞춤형 최신 애플리케이션, API 및 콘텐츠로 변환할 수 있습니다. 자세한 내용은 [Azure 정문](../frontdoor/front-door-overview.md)을 참조하십시오.


### <a name="traffic-manager"></a><a name="trafficmanager"></a>Traffic Manager

Azure Traffic Manager는 트래픽을 전 세계 Azure 지역의 서비스에 적절하게 분산하는 한편, 고가용성과 빠른 응답성을 제공하는 DNS 기반 트래픽 부하 분산 장치입니다. 트래픽 관리자는 우선 순위, 가중치, 성능, 지리적, 다중 값 또는 서브넷과 같은 트래픽을 분산하는 다양한 트래픽 라우팅 방법을 제공합니다. 트래픽 라우팅 방법에 대한 자세한 내용은 [트래픽 관리자 라우팅 방법을](../traffic-manager/traffic-manager-routing-methods.md)참조하십시오.

다음 다이어그램은 트래픽 관리자를 사용하여 끝점 우선 순위 기반 라우팅을 보여 주며 다음과 같은 점을 보여 주며 있습니다.

![Azure Traffic Manager '우선 순위' 트래픽 라우팅 메서드](./media/networking-overview/priority.png)

트래픽 관리자에 대한 자세한 내용은 [Azure 트래픽 관리자란 무엇입니까?](../traffic-manager/traffic-manager-overview.md)

### <a name="load-balancer"></a><a name="loadbalancer"></a>Load Balancer
Azure Load Balancer는 모든 UDP 및 TCP 프로토콜에 대해 대기 시간이 낮은 고성능 계층 4 부하 분산을 제공합니다. 인바운드 및 아웃 바운드 연결을 관리합니다. 내부 부하가 분산된 공용 엔드포인트를 구성할 수 있습니다. 서비스 가용성 관리 옵션을 검색하는 TCP 및 HTTP 상태를 사용하여 백 엔드 풀 대상에 인바운드 연결을 매핑하는 규칙을 정의할 수 있습니다. Load Balancer에 대한 자세한 내용은 [Load Balancer 개요](../load-balancer/load-balancer-overview.md) 문서를 참고하세요.

다음 그림에서는 외부 및 내부 부하 분산 장치를 모두 활용하는 인터넷 연결 다중 계층 애플리케이션을 보여줍니다.

![Azure 로드 밸러커 예제](./media/networking-overview/load-balancer.png)


### <a name="application-gateway"></a><a name="applicationgateway"></a>Application Gateway
Azure Application Gateway는 웹 애플리케이션에 대한 트래픽을 관리할 수 있도록 하는 웹 트래픽 부하 분산 장치입니다. 응용 프로그램 제공 컨트롤러(ADC)는 응용 프로그램에 다양한 계층 7 로드 밸런싱 기능을 제공합니다. 자세한 내용은 [Azure 응용 프로그램 게이트웨이란 무엇입니까?](../application-gateway/overview.md)

다음 다이어그램은 응용 프로그램 게이트웨이를 통한 URL 경로 기반 라우팅을 보여 주며 있습니다.

![애플리케이션 게이트웨이 예제](./media/networking-overview/figure1-720.png)

## <a name="network-monitoring-services"></a><a name="monitor"></a>네트워크 모니터링 서비스
이 섹션에서는 네트워크 감시자, ExpressRoute 모니터, Azure 모니터 및 가상 네트워크 TAP와 같은 네트워크 리소스를 모니터링하는 데 도움이 되는 Azure의 네트워킹 서비스에 대해 설명합니다.

|서비스|왜 사용합니까?|시나리오|
|---|---|---|
|[Network Watcher](#networkwatcher)|연결 문제를 모니터링하고 문제를 해결하고, VPN, NSG 및 라우팅 문제를 진단하고, VM에서 패킷을 캡처하고, Azure Functions 및 논리 앱을 사용하여 트리거링 진단 도구를 자동화하는 데 도움이 됩니다.|<p>[VM 트래픽 필터 문제 진단](../network-watcher/diagnose-vm-network-traffic-filtering-problem.md)</p><p>[VM 라우팅 문제 진단](../network-watcher/diagnose-vm-network-routing-problem.md)</p><p>[VM 간의 통신 모니터링](../network-watcher/connection-monitor.md)</p><p>[네트워크 간 통신 문제 진단](../network-watcher/diagnose-communication-problem-between-networks.md)</p><p>[VM과 주고 받는 네트워크 트래픽 로깅](../network-watcher/network-watcher-nsg-flow-logging-portal.md)</p>|
|[익스프레스루트 모니터](#expressroutemonitor)|네트워크 성능, 가용성 및 활용도에 대한 실시간 모니터링 제공, 네트워크 토폴로지 자동 검색 지원, 오류 격리 속도 향상, 일시적 네트워크 문제 감지, 과거 네트워크 성능 특성 분석, 다중 구독 지원|<p>[ExpressRoute에 대한 네트워크 성능 모니터 구성](../expressroute/how-to-npm.md)</p><p>[ExpressRoute 모니터링, 메트릭 및 경고](../expressroute/expressroute-monitoring-metrics-alerts.md)</p>|
|[Azure Monitor](#azuremonitor)|응용 프로그램이 수행하는 방식을 이해하고 응용 프로그램에 영향을 미치는 문제와 해당 응용 프로그램이 의존하는 리소스를 사전에 식별합니다.|<p>[Traffic Manager의 메트릭 및 경고](../traffic-manager/traffic-manager-metrics-alerts.md)</p><p>[표준 로드 밸러커에 대한 Azure 모니터 진단](../load-balancer/load-balancer-standard-diagnostics.md)</p><p>[Azure Firewall 로그 및 메트릭 모니터링](../firewall/tutorial-diagnostics.md)</p><p>[Azure 웹 애플리케이션 방화벽 모니터링 및 로깅](../frontdoor/waf-front-door-monitor.md)</p>|
|[가상 네트워크 탭](#vnettap)|패킷 수집기로 가상 머신 네트워크 트래픽의 지속적인 스트리밍 제공, 네트워크 및 애플리케이션 성능 관리 솔루션 및 보안 분석 도구 지원|[VNet TAP 리소스 만들기](../virtual-network/tutorial-tap-virtual-network-cli.md)|
|

### <a name="network-watcher"></a><a name="networkwatcher"></a>Network Watcher
Azure Network Watcher는 Azure 가상 네트워크의 리소스를 모니터링 및 진단하고 메트릭을 보고 그에 대한 로그를 활성화 또는 비활성화하는 도구를 제공합니다. 자세한 내용은 [네트워크 감시자란 무엇입니까?](../network-watcher/network-watcher-monitoring-overview.md?toc=%2fazure%2fnetworking%2ftoc.json)
### <a name="expressroute-monitor"></a><a name="expressroutemonitor"></a>익스프레스루트 모니터
ExpressRoute 회로 메트릭, 진단 로그 및 경고를 보는 방법에 대한 자세한 내용은 [ExpressRoute 모니터링, 메트릭 및 경고를](../expressroute/expressroute-monitoring-metrics-alerts.md?toc=%2fazure%2fnetworking%2ftoc.json)참조하십시오.
### <a name="azure-monitor"></a><a name="azuremonitor"></a>Azure Monitor
Azure Monitor는 클라우드 및 온-프레미스 환경에서 원격 분석 데이터를 수집, 분석하고 그에 따라 조치를 취하는 포괄적인 솔루션을 제공함으로써 애플리케이션의 성능과 가용성을 최대화합니다. 애플리케이션을 수행하는 방법과 애플리케이션 및 종속된 리소스에 영향을 주는 문제를 사전에 식별하는 방법을 파악할 수 있습니다. 자세한 내용은 [Azure 모니터 개요를](../azure-monitor/overview.md?toc=%2fazure%2fnetworking%2ftoc.json)참조하십시오.
### <a name="virtual-network-tap"></a><a name="vnettap"></a>가상 네트워크 탭
Azure 가상 네트워크 TAP(터미널 액세스 지점)을 사용하면 네트워크 패킷 수집기 또는 분석 도구로 가상 머신 네트워크 트래픽을 지속적으로 스트리밍할 수 있습니다. 수집기 또는 분석 도구는 [네트워크 가상 어플라이언스](https://azure.microsoft.com/solutions/network-appliances/) 파트너가 제공합니다. 

다음 그림은 가상 네트워크 TAP이 작동하는 방법을 보여 줍니다. 

![가상 네트워크 TAP이 작동하는 방법](./media/networking-overview/virtual-network-tap-architecture.png)

자세한 내용은 [가상 네트워크 TAP입니다.](../virtual-network/virtual-network-tap-overview.md)

## <a name="next-steps"></a>다음 단계

- [첫 번째 가상 네트워크 만들기](../virtual-network/quick-create-portal.md?toc=%2fazure%2fnetworking%2ftoc.json) 문서의 단계를 완료하여 첫 번째 VNet을 만들고 일부 VM을 연결합니다.
- [지점 간 연결 문서 구성 문서의](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md?toc=%2fazure%2fnetworking%2ftoc.json)단계를 완료하여 컴퓨터를 VNet에 연결합니다.
- [인터넷 연결 부하 분산 장치 만들기](../load-balancer/load-balancer-get-started-internet-portal.md?toc=%2fazure%2fnetworking%2ftoc.json) 문서의 단계를 완료하여 공용 서버에 인터넷 트래픽의 부하를 분산합니다.
 
 
   
