---
title: 포함 파일
description: 포함 파일
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 09/17/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 32e4658af48a0ae3bde08de18cf1d8204878d671
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/22/2020
ms.locfileid: "91024940"
---
### <a name="is-bgp-supported-on-all-azure-vpn-gateway-skus"></a>BGP가 모든 Azure VPN Gateway SKU를 지원하나요?
BGP는 기본 SKU를 제외한 모든 Azure VPN Gateway SKU에서 지원됩니다.

### <a name="can-i-use-bgp-with-azure-policy-based-vpn-gateways"></a>Azure 정책 기반 VPN 게이트웨이에 BGP를 사용할 수 있나요?
아니요. BGP는 경로 기반 VPN 게이트웨이에서만 지원됩니다.

### <a name="what-asn-autonomous-system-numbers-can-i-use"></a>어떤 ASN(자치 시스템 번호)을 사용할 수 있나요?
Azure 또는 IANA에서 예약된 범위를 **제외**하고 온-프레미스 네트워크와 Azure 가상 네트워크 모두에 자체 공용 ASN 또는 프라이빗 ASN을 사용할 수 있습니다.

> [!IMPORTANT]
>
> 다음 ASNs는 Azure 또는 IANA에서 예약됩니다.
> * Azure에서 예약된 ASN:
>    * 공용 ASN: 8074, 8075, 12076
>    * 프라이빗 ASN: 65515, 65517, 65518, 65519, 65520
> * [IANA에서 예약된](http://www.iana.org/assignments/iana-as-numbers-special-registry/iana-as-numbers-special-registry.xhtml) ASN
>    * 23456, 64496-64511, 65535-65551 및 429496729
>
> Azure VPN 게이트웨이에 연결할 때 온-프레미스 VPN 디바이스에 대해 이러한 ASN을 지정할 수 없습니다.
>

### <a name="can-i-use-32-bit-4-byte-asns-autonomous-system-numbers"></a>32비트(4바이트) ASN(자치 시스템 번호)을 사용할 수 있나요?
예, Azure VPN Gateway는 이제 32비트(4바이트) ASN을 지원합니다. PowerShell/CLI/SDK를 사용하여 ASN을 10진수 형식으로 구성하세요.

### <a name="what-private-asns-can-i-use"></a>사용할 수 있는 프라이빗 ASN은 무엇인가요?
사용할 수 있는 프라이빗 ASN의 범위는 다음과 같습니다.

* 64512-65514, 65521-65534

이러한 ASN은 IANA 또는 Azure에서 사용하도록 예약되지 않으므로 Azure VPN Gateway에 할당하는 데 사용할 수 있습니다.

### <a name="what-address-does-azure-vpn-gateway-use-for-bgp-peer-ip"></a>Azure VPN 게이트웨이는 BGP 피어 IP에 어떤 주소를 사용하나요?

* 기본적으로 Azure VPN 게이트웨이는 활성-대기 VPN 게이트웨이에 대해 GatewaySubnet 범위에서 단일 IP 주소를 할당하거나 활성-활성 VPN 게이트웨이에 대해 두 개의 IP 주소를 할당합니다. 이러한 주소는 VPN 게이트웨이를 만들 때 자동으로 할당됩니다. PowerShell(Get-AzVirtualNetworkGateway, "bgpPeeringAddress" 속성 찾기) 또는 Azure Portal([게이트웨이 구성] 페이지의 "BGP ASN 구성" 속성 아래)에서 할당된 실제 BGP IP 주소를 가져올 수 있습니다.

* 온-프레미스 VPN 라우터가 **APIPA** IP 주소(169.254.x.x)를 BGP IP 주소로 사용하는 경우 Azure VPN 게이트웨이에서 추가 **Azure APIPA BGP IP 주소**를 지정해야 합니다. Azure VPN 게이트웨이는 로컬 네트워크 게이트웨이에 지정된 온-프레미스 APIPA BGP 피어와 함께 사용할 APIPA 주소를 선택하거나 비 APIPA 온-프레미스 BGP 피어에 대한 개인 IP 주소를 선택합니다. 자세한 내용은 [BGP 구성](../articles/vpn-gateway/bgp-howto.md)을 참조하세요.

### <a name="what-are-the-requirements-for-the-bgp-peer-ip-addresses-on-my-vpn-device"></a>VPN 디바이스에서 BGP 피어 IP 주소에 대 한 요구 사항은 무엇인가요?
온-프레미스 BGP 피어 주소는 VPN 디바이스의 공용 IP 주소 또는 VPN Gateway의 VNet 주소 공간에 있는 것과 동일하지 **않아야 합니다**. VPN 디바이스에서 BGP 피어 IP에 다른 IP 주소를 사용합니다. 이는 디바이스의 루프백 인터페이스에 할당된 주소, 일반 IP 주소 또는 APIPA 주소일 수 있습니다. 디바이스가 BGP에 APIPA 주소를 사용하는 경우 [BGP 구성](../articles/vpn-gateway/bgp-howto.md)에 설명된 대로 Azure VPN 게이트웨이에서 APIPA BGP IP 주소를 지정해야 합니다. 위치를 나타내는 해당 로컬 네트워크 게이트웨이에서 이 주소를 지정합니다.

### <a name="what-should-i-specify-as-my-address-prefixes-for-the-local-network-gateway-when-i-use-bgp"></a>BGP를 사용할 때 로컬 네트워크 게이트웨이에 대해 내 주소 접두어로 무엇을 지정해야 하나요?

> [!IMPORTANT]
>
>이는 이전에 문서화된 요구 사항에서 변경된 사항입니다. 연결에 BGP를 사용하는 경우 해당 로컬 네트워크 게이트웨이 리소스에 대해 **주소 공간** 필드를 ***비어*** 두어야 합니다. Azure VPN 게이트웨이는 내부적으로 IPsec 터널을 통해 온-프레미스 BGP 피어 IP에 호스트 경로를 추가합니다. 주소 공간 필드에 /32 경로를 추가하지 **마세요**. 이는 중복되며 APIPA 주소를 온 프레미스 VPN 디바이스 BGP IP로 사용하는 경우 이 필드에 추가할 수 없습니다. 주소 공간 필드에 다른 접두사를 추가하면 BGP를 통해 학습된 경로 외에 Azure VPN 게이트웨이에서 **고정 경로**로 추가됩니다.
>

### <a name="can-i-use-the-same-asn-for-both-on-premises-vpn-networks-and-azure-vnets"></a>온-프레미스 VPN 네트워크와 Azure VNet에 동일한 ASN을 사용할 수 있나요?
아니요. 온-프레미스 네트워크와 Azure VNet을 함께 BGP에 연결하려면 서로 다른 ASN을 할당해야 합니다. 크로스 프레미스 연결에 대한 BGP 활성화 여부에 관계없이 Azure VPN Gateway에 할당되는 기본 ASN은 65515입니다. VPN 게이트웨이를 만들 때 다른 ASN을 적용하여 이 기본값을 다시 정의하거나, 게이트웨이를 만든 후 ASN을 변경할 수 있습니다. 해당하는 Azure 로컬 네트워크 게이트웨이에 온-프레미스 ASN을 할당해야 합니다.

### <a name="what-address-prefixes-will-azure-vpn-gateways-advertise-to-me"></a>Azure VPN 게이트웨이가 나에게 알리는 주소 접두어는 무엇인가요?
Azure VPN 게이트웨이는 온-프레미스 BGP 디바이스에 다음 경로를 알립니다.

* VNet 주소 접두어
* Azure VPN 게이트웨이에 연결된 각 로컬 네트워크 게이트웨이의 주소 접두어
* Azure VPN 게이트웨이에 연결된 다른 BGP 피어링 세션에서 확인한 경로( **기본 경로 또는 다른 VNet 접두어와 겹치는 경로 제외**)

### <a name="how-many-prefixes-can-i-advertise-to-azure-vpn-gateway"></a>Azure VPN 게이트웨이에 접두사를 몇 개나 보급할 수 있나요?
최대 4000개의 접두사를 지원합니다. 접두사의 수가 제한을 초과하는 경우 BGP 세션은 삭제됩니다.

### <a name="can-i-advertise-default-route-00000-to-azure-vpn-gateways"></a>Azure VPN 게이트웨이에 기본 경로(0.0.0.0/0)를 보급할 수 있나요?
예.

그러면 모든 VNet 송신 트래픽을 온-프레미스 사이트로 강제하고 VNet VM이 인터넷에서 VM으로 RDP 또는 SSH와 같이 인터넷의 공용 통신을 직접 수락하지 않도록 방지하게 됩니다.

### <a name="can-i-advertise-the-exact-prefixes-as-my-virtual-network-prefixes"></a>Virtual Network 접두사로 정확한 접두사를 보급할 수 있나요?

아니요. Virtual Network 주소 접두사와 동일한 접두사의 보급은 Azure 플랫폼에서 차단되거나 필터링됩니다. 그러나 Virtual Network 내에 포함된 접두사의 상위 집합에 해당하는 접두사를 보급할 수 있습니다. 

예를 들어 가상 네트워크에서 10.0.0.0/16 주소 공간을 사용하는 경우 10.0.0.0/8은 보급할 수 있지만, 10.0.0.0/16 또는 10.0.0.0/24는 보급할 수 없습니다.

### <a name="can-i-use-bgp-with-my-vnet-to-vnet-connections"></a>내 VNet-VNet 연결에 BPG를 사용할 수 있나요?
예. 크로스 프레미스 연결과 VNet-VNet 연결에 모두 BGP를 사용할 수 있습니다.

### <a name="can-i-mix-bgp-with-non-bgp-connections-for-my-azure-vpn-gateways"></a>BGP를 비 BGP 연결과 혼합하여 내 Azure VPN 게이트웨이에 사용할 수 있나요?
예. BGP와 비 BGP 연결을 동일한 Azure VPN 게이트웨이에 혼합 사용할 수 있습니다.

### <a name="does-azure-vpn-gateway-support-bgp-transit-routing"></a>Azure VPN 게이트웨이가 BGP 전송 라우팅을 지원하나요?
예. BGP 전송 라우팅이 지원됩니다. 단 Azure VPN 게이트웨이가 기본 경로를 타 BGP 피어에 알리지 **않는다**는 점이 다릅니다. 여러 Azure VPN 게이트웨이 간 전송 라우팅을 활성화하려면 모든 중간 VNet-VNet 연결에서 BGP를 활성화해야 합니다. 자세한 내용은 [BGP 정보](../articles/vpn-gateway/vpn-gateway-bgp-overview.md)를 참조하세요.

### <a name="can-i-have-more-than-one-tunnel-between-azure-vpn-gateway-and-my-on-premises-network"></a>Azure VPN 게이트웨이와 내 온-프레미스 네트워크 간에 터널이 여러 개 있을 수 있나요?
예, Azure VPN 게이트웨이와 내 온-프레미스 네트워크 간에 S2S VPN 터널을 여러 개 구축할 수 있습니다. 이러한 모든 터널은 Azure VPN 게이트웨이의 총 터널 수와 비교하여 계산되며 두 터널 모두에서 BGP를 활성화해야 합니다.

예를 들어 Azure VPN 게이트웨이와 온-프레미스 네트워크 중 하나 간에 2개의 중복 터널이 있는 경우 총 Azure VPN 게이트웨이 할당량 중 2개 터널을 사용하는 것입니다.

### <a name="can-i-have-multiple-tunnels-between-two-azure-vnets-with-bgp"></a>두 Azure VNet과 BGP와 간에 여러 터널이 있을 수 있나요?
예, 하지만 하나 이상의 가상 네트워크 게이트웨이가 active-active 구성에 있어야 합니다.

### <a name="can-i-use-bgp-for-s2s-vpn-in-an-expressroutes2s-vpn-coexistence-configuration"></a>ExpressRoute/S2S VPN 공존 구성에서 S2S VPN에 BGP를 사용할 수 있나요?
예. 

### <a name="what-should-i-add-to-my-on-premises-vpn-device-for-the-bgp-peering-session"></a>BGP 피어링 세션에 대해 온-프레미스 VPN 디바이스에 무엇을 추가해야 하나요?
IPsec S2S VPN 터널을 가리키는 VPN 디바이스에서 Azure BGP 피어 IP 주소의 호스트 경로를 추가해야 합니다. 예를 들어, Azure VPN 피어 IP가 "10.12.255.30"이라면 VPN 디바이스에서 일치하는 IPsec 터널 인터페이스의 nexthop 인터페이스와 "10.12.255.30"에 대한 호스트 경로를 추가해야 합니다.
