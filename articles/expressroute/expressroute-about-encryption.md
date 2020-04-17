---
title: 'Azure 익스프레스라우팅: 암호화 에 대해'
description: 익스프레스루트 암호화에 대해 자세히 알아보세요.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 12/13/2019
ms.author: cherylmc
ms.openlocfilehash: d48f83df4e83fd1d1fe38eea76432e1b1c711b76
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81461466"
---
# <a name="expressroute-encryption"></a>익스프레스루트 암호화
 
ExpressRoute는 네트워크와 Microsoft 네트워크 간에 트래버스하는 데이터의 기밀성과 무결성을 보장하기 위해 몇 가지 암호화 기술을 지원합니다.

## <a name="point-to-point-encryption-by-macsec-faq"></a>MACsec FAQ에 의한 포인트 간 암호화
MACsec은 [IEEE 표준입니다.](https://1.ieee802.org/security/802-1ae/) MAC(미디어 액세스 제어) 수준 또는 네트워크 계층 2에서 데이터를 암호화합니다. [ExpressRoute Direct를](expressroute-erdirect-about.md)통해 Microsoft에 연결할 때 MACsec을 사용하여 네트워크 장치와 Microsoft 네트워크 장치 간의 물리적 링크를 암호화할 수 있습니다. MACsec은 기본적으로 익스프레스루트 다이렉트 포트에서 비활성화됩니다. 암호화를 위해 고유한 MACsec 키를 가져와 [Azure 키 볼트](../key-vault/general/overview.md)에 저장합니다. 키를 회전할 시기를 결정합니다. 아래의 다른 자주 묻는 질문(FAQ)을 참조하십시오.
### <a name="can-i-enable-macsec-on-my-expressroute-circuit-provisioned-by-an-expressroute-provider"></a>익스프레스루트 제공업체가 프로비전한 익스프레스루트 서킷에서 MACsec을 활성화할 수 있습니까?
아니요. MACsec은 한 엔터티(예: 고객)가 소유한 키로 물리적 링크의 모든 트래픽을 암호화합니다. 따라서 익스프레스루트 다이렉트에서만 사용할 수 있습니다.
### <a name="can-i-encrypt-some-of-the-expressroute-circuits-on-my-expressroute-direct-ports-and-leave-other-circuits-on-the-same-ports-unencrypted"></a>ExpressRoute 다이렉트 포트의 일부 ExpressRoute 회로를 암호화하고 동일한 포트에 다른 회로를 암호화하지 않은 상태로 둘 수 있습니까? 
아니요. MACsec이 활성화되면 모든 네트워크 제어 트래픽(예: BGP 데이터 트래픽 및 고객 데이터 트래픽)이 암호화됩니다. 
### <a name="when-i-enabledisable-macsec-or-update-macsec-key-will-my-on-premises-network-lose-connectivity-to-microsoft-over-expressroute"></a>MACsec을 활성화/비활성화하거나 MACsec 키를 업데이트하면 온-프레미스 네트워크가 ExpressRoute를 통해 Microsoft에 대한 연결이 끊어지나요?
예. MACsec 구성의 경우 미리 공유된 키 모드만 지원합니다. 즉, 장치와 Microsoft의 (우리의 API를 통해)에서 키를 업데이트해야합니다. 이 변경 사항은 원자성이 아니므로 양측 간에 주요 불일치가 있을 때 연결이 끊어질 수 있습니다. 구성 변경에 대한 유지 관리 기간을 예약하는 것이 좋습니다. 가동 중지 시간을 최소화하기 위해 네트워크 트래픽을 다른 링크로 전환한 후 한 번에 ExpressRoute Direct의 한 링크에서 구성을 업데이트하는 것이 좋습니다.  
### <a name="will-traffic-continue-to-flow-if-theres-a-mismatch-in-macsec-key-between-my-devices-and-microsofts"></a>내 장치와 Microsoft 의 장치 간에 MACsec 키에 불일치가 있는 경우 트래픽이 계속 흐르나요?
아니요. MACsec이 구성되고 키 불일치가 발생하면 Microsoft에 대한 연결이 끊어지게 됩니다. 즉, 암호화되지 않은 연결로 돌아가서 데이터를 노출하지 않습니다. 
### <a name="will-enabling-macsec-on-expressroute-direct-degrade-network-performance"></a>ExpressRoute Direct에서 MACsec을 사용하도록 설정하면 네트워크 성능이 저하됩니다.
MACsec 암호화 및 암호 해독은 우리가 사용하는 라우터의 하드웨어에서 발생합니다. 우리 측에 성능에 미치는 영향은 없습니다. 그러나 사용하는 장치에 대해 네트워크 공급업체에 문의하여 MACsec에 성능에 영향을 미치는지 확인해야 합니다.
### <a name="which-cipher-suites-are-supported-for-encryption"></a>암호화에 대해 지원되는 암호 제품군은 무엇입니까?
우리는 AES128 및 AES256을 지원합니다.

## <a name="end-to-end-encryption-by-ipsec-faq"></a>IPsec FAQ에 의한 종단 간 암호화
IPsec은 [IETF 표준입니다.](https://tools.ietf.org/html/rfc6071) 인터넷 프로토콜(IP) 수준 또는 네트워크 계층 3에서 데이터를 암호화합니다. IPsec을 사용하여 온-프레미스 네트워크와 Azure의 가상 네트워크(VNET) 간의 종단 간 연결을 암호화할 수 있습니다. 아래의 다른 자주 묻는 질문(FAQ)을 참조하십시오.
### <a name="can-i-enable-ipsec-in-addition-to-macsec-on-my-expressroute-direct-ports"></a>익스프레스루트 다이렉트 포트에서 MACsec 외에 IPsec을 활성화할 수 있습니까?
예. MACsec은 귀하와 Microsoft 간의 물리적 연결을 보호합니다. IPsec은 Azure에서 사용자및 가상 네트워크 간의 종단 간 연결을 보호합니다. 독립적으로 활성화할 수 있습니다. 
### <a name="can-i-use-azure-vpn-gateway-to-set-up-the-ipsec-tunnel-between-my-on-premises-network-and-my-azure-virtual-network"></a>Azure VPN 게이트웨이를 사용하여 온-프레미스 네트워크와 Azure 가상 네트워크 간에 IPsec 터널을 설정할 수 있습니까?
예. ExpressRoute 회로의 Microsoft 피어링을 통해 이 IPsec 터널을 설정할 수 있습니다. 구성 [가이드를](site-to-site-vpn-over-microsoft-peering.md)따르십시오.
### <a name="can-i-use-azure-vpn-gateway-to-set-up-the-ipsec-tunnel-over-azure-private-peering"></a>Azure VPN 게이트웨이를 사용하여 Azure 개인 피어링을 통해 IPsec 터널을 설정할 수 있습니까?
Azure Virtual WAN을 채택하는 경우 [다음 단계를](../virtual-wan/vpn-over-expressroute.md) 수행하여 종단 간 연결을 암호화할 수 있습니다. 일반 Azure VNET이 있는 경우 VNET에 타사 VPN 게이트웨이를 배포하고 해당 게이트웨이와 온-프레미스 VPN 게이트웨이 사이에 IPsec 터널을 설정할 수 있습니다.
### <a name="what-is-the-throughput-i-will-get-after-enabling-ipsec-on-my-expressroute-connection"></a>ExpressRoute 연결에서 IPsec을 활성화한 후 얻을 수 있는 처리량은 무엇입니까?
Azure VPN 게이트웨이를 사용하는 경우 [여기에서 성능 번호를 확인하십시오.](../vpn-gateway/vpn-gateway-about-vpngateways.md) 타사 VPN 게이트웨이를 사용하는 경우 공급업체에 성능 번호를 확인하십시오.

## <a name="next-steps"></a>다음 단계
MACsec 구성에 대한 자세한 내용은 [MACsec 구성을](expressroute-howto-macsec.md) 참조하십시오.

IPsec 구성에 대한 자세한 내용은 [IPsec 구성을](site-to-site-vpn-over-microsoft-peering.md) 참조하십시오.
