---
title: 'Azure 백 엔드 연결 기능의 상호 운용성: 구성 세부 정보 | Microsoft Docs'
description: 이 문서에서는 ExpressRoute, 사이트 간 VPN 및 Azure의 가상 네트워크 피어링 간의 상호 운용성을 분석하는 데 사용할 수 있는 테스트 설정에 대한 구성 세부 정보를 설명합니다.
documentationcenter: na
services: networking
author: rambk
manager: tracsman
ms.service: virtual-network
ms.topic: article
ms.workload: infrastructure-services
ms.date: 10/18/2018
ms.author: rambala
ms.openlocfilehash: 2ceb4aeac55bd555a41c29bd41b00c771490e5f9
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60425757"
---
# <a name="interoperability-in-azure-back-end-connectivity-features-test-configuration-details"></a>Azure 백 엔드 연결 기능의 상호 운용성: 테스트 구성 세부 정보

이 문서에서는 [테스트 설정][Setup]의 구성 세부 정보를 설명합니다. 테스트 설정은 Azure 네트워킹 서비스가 제어 평면 수준 및 데이터 평면 수준에서 상호 운용하는 방법을 분석하도록 도와줍니다.

## <a name="spoke-vnet-connectivity-by-using-vnet-peering"></a>VNet 피어링을 사용하여 스포크 VNet 연결

다음 그림은 스포크 VNet(가상 네트워크)의 Azure Virtual Network 피어링 세부 정보를 보여줍니다. 두 개의 VNet 간에 피어링을 설정하는 방법을 알아보려면 [VNet 피어링 관리][VNet-Config]를 참조하세요. 스포크 VNet이 허브 VNet에 연결된 게이트웨이를 사용하도록 하려면 **원격 게이트웨이 사용**을 선택합니다.

[![1]][1]

다음 그림은 허브 VNet의 VNet 피어링 세부 정보를 보여줍니다. 스포크 VNet이 허브 VNet 게이트웨이를 사용하도록 하려면 **원격 게이트웨이 사용**을 선택합니다.

[![2]][2]

## <a name="branch-vnet-connectivity-by-using-a-site-to-site-vpn"></a>사이트 간 VPN을 사용하여 분기 VNet 연결

Azure VPN Gateway에서 VPN 게이트웨이를 사용하여 허브와 분기 VNet 간의 사이트 간 VNet 연결을 설정합니다. 기본적으로 VPN 게이트웨이 및 Azure ExpressRoute 게이트웨이는 **65515**의 프라이빗 ASN(자치 시스템 번호) 값을 사용합니다. VPN Gateway에서 ASN 값을 변경할 수 있습니다. 테스트 설정에서 분기 VNet VPN 게이트웨이의 ASN 값은 허브 VNet과 분기 VNet 간에 eBGP 라우팅을 지원하도록 **65516**으로 변경됩니다.


[![3]][3]


## <a name="on-premises-location-1-connectivity-by-using-expressroute-and-a-site-to-site-vpn"></a>ExpressRoute 및 사이트 간 VPN을 사용하여 온-프레미스 위치 1 연결

### <a name="expressroute-1-configuration-details"></a>ExpressRoute 1 구성 세부 정보

다음 그림은 온-프레미스 위치 1 CE(고객 에지) 라우터를 지향하는 Azure 지역 1 ExpressRoute 회로 구성을 보여줍니다.

[![4]][4]

다음 그림은 ExpressRoute 1 회로와 허브 VNet 간의 연결 구성을 보여줍니다.

[![5]][5]

다음 목록은 ExpressRoute 프라이빗 피어링 연결에 대한 기본 CE 라우터 구성을 보여줍니다. (Cisco ASR1000 라우터는 테스트 설정에서 CE 라우터로 사용됩니다.) 사이트 간 VPN과 ExpressRoute 회로가 온-프레미스 네트워크를 Azure에 연결하기 위해 병렬로 구성되는 경우 Azure는 기본적으로 ExpressRoute 회로를 우선 순위로 지정합니다. 비대칭 라우팅을 방지하려면 온-프레미스 네트워크도 사이트 간 VPN 연결에 비해 ExpressRoute 연결을 우선 순위로 지정해야 합니다. 다음 구성은 BGP **로컬 기본 설정** 특성을 사용하여 우선 순위를 설정합니다.

    interface TenGigabitEthernet0/0/0.300
     description Customer 30 private peering to Azure
     encapsulation dot1Q 30 second-dot1q 300
     ip vrf forwarding 30
     ip address 192.168.30.17 255.255.255.252
    !
    interface TenGigabitEthernet1/0/0.30
     description Customer 30 to south bound LAN switch
     encapsulation dot1Q 30
     ip vrf forwarding 30
     ip address 192.168.30.0 255.255.255.254
     ip ospf network point-to-point
    !
    router ospf 30 vrf 30
     router-id 10.2.30.253
     redistribute bgp 65021 subnets route-map BGP2OSPF
     network 192.168.30.0 0.0.0.1 area 0.0.0.0
    default-information originate always
     default-metric 10
    !
    router bgp 65021
     !
     address-family ipv4 vrf 30
      network 10.2.30.0 mask 255.255.255.128
      neighbor 192.168.30.18 remote-as 12076
      neighbor 192.168.30.18 activate
      neighbor 192.168.30.18 next-hop-self
      neighbor 192.168.30.18 soft-reconfiguration inbound
      neighbor 192.168.30.18 route-map prefer-ER-over-VPN in
      neighbor 192.168.30.18 prefix-list Cust30_to_Private out
     exit-address-family
    !
    route-map prefer-ER-over-VPN permit 10
     set local-preference 200
    !
    ip prefix-list Cust30_to_Private seq 10 permit 10.2.30.0/25
    !

### <a name="site-to-site-vpn-configuration-details"></a>사이트 간 VPN 구성 세부 정보

다음 목록은 사이트 간 VPN 연결에 대한 기본 CE 라우터 구성을 보여줍니다.

    crypto ikev2 proposal Cust30-azure-proposal
     encryption aes-cbc-256 aes-cbc-128 3des
     integrity sha1
     group 2
    !
    crypto ikev2 policy Cust30-azure-policy
     match address local 66.198.12.106
     proposal Cust30-azure-proposal
    !
    crypto ikev2 keyring Cust30-azure-keyring
     peer azure
      address 52.168.162.84
      pre-shared-key local IamSecure123
      pre-shared-key remote IamSecure123
    !
    crypto ikev2 profile Cust30-azure-profile
     match identity remote address 52.168.162.84 255.255.255.255
     identity local address 66.198.12.106
     authentication local pre-share
     authentication remote pre-share
     keyring local Cust30-azure-keyring
    !
    crypto ipsec transform-set Cust30-azure-ipsec-proposal-set esp-aes 256 esp-sha-hmac
     mode tunnel
    !
    crypto ipsec profile Cust30-azure-ipsec-profile
     set transform-set Cust30-azure-ipsec-proposal-set
     set ikev2-profile Cust30-azure-profile
    !
    interface Loopback30
     ip address 66.198.12.106 255.255.255.255
    !
    interface Tunnel30
     ip vrf forwarding 30
     ip address 10.2.30.125 255.255.255.255
     tunnel source Loopback30
     tunnel mode ipsec ipv4
     tunnel destination 52.168.162.84
     tunnel protection ipsec profile Cust30-azure-ipsec-profile
    !
    router bgp 65021
     !
     address-family ipv4 vrf 30
      network 10.2.30.0 mask 255.255.255.128
      neighbor 10.10.30.254 remote-as 65515
      neighbor 10.10.30.254 ebgp-multihop 5
      neighbor 10.10.30.254 update-source Tunnel30
      neighbor 10.10.30.254 activate
      neighbor 10.10.30.254 soft-reconfiguration inbound
     exit-address-family
    !
    ip route vrf 30 10.10.30.254 255.255.255.255 Tunnel30

## <a name="on-premises-location-2-connectivity-by-using-expressroute"></a>ExpressRoute를 사용하여 온-프레미스 위치 2 연결

온-프레미스 위치 2에 더 근접한 두 번째 ExpressRoute 회로는 온-프레미스 위치 2를 허브 VNet에 연결합니다. 다음 그림은 두 번째 ExpressRoute 구성을 보여줍니다.

[![6]][6]

다음 그림은 두 번째 ExpressRoute 회로와 허브 VNet 간의 연결 구성을 보여줍니다.

[![7]][7]

ExpressRoute 1은 허브 VNet과 온-프레미스 위치 1을 모두 서로 다른 Azure 지역의 원격 VNet에 연결합니다.

[![8]][8]

## <a name="expressroute-and-site-to-site-vpn-connectivity-in-tandem"></a>탠덤에서 ExpressRoute 및 사이트 간 VPN 연결

###  <a name="site-to-site-vpn-over-expressroute"></a>ExpressRoute를 통한 사이트 간 VPN

ExpressRoute Microsoft 피어링을 사용하여 온-프레미스 네트워크와 Azure VNet 간에 개인적으로 데이터를 교환하도록 사이트 간 VPN을 구성할 수 있습니다. 이 구성을 사용하면 비밀성, 신뢰성 및 무결성을 유지하며 데이터를 교환할 수 있습니다. 데이터 교환은 재생 방지이기도 합니다. ExpressRoute Microsoft 피어링을 사용하여 터널 모드에서 사이트 간 IPsec VPN을 구성하는 방법에 대한 자세한 내용은 [ExpressRoute Microsoft 피어링을 통한 사이트 간 VPN][S2S-Over-ExR]을 참조하세요. 

Microsoft 피어링을 사용하는 사이트 간 VPN 구성에 대한 기본 제한은 처리량입니다. IPsec 터널을 통한 처리량은 VPN Gateway 용량에 의해 제한됩니다. VPN Gateway 처리량은 ExpressRoute 처리량보다 낮습니다. 이 시나리오에서 매우 안전한 트래픽의 경우 IPsec 터널 및 모든 다른 트래픽의 경우 프라이빗 피어링을 사용하면 ExpressRoute 대역폭 사용량을 최적화하는 데 도움이 될 수 있습니다.

### <a name="site-to-site-vpn-as-a-secure-failover-path-for-expressroute"></a>ExpressRoute에 대한 안전한 장애 조치(failover) 경로인 사이트 간 VPN

ExpressRoute는 고가용성을 보장하기 위해 중복 회로 쌍으로 제공됩니다. 다른 Azure 지역에서 지역 중복 ExpressRoute 연결을 구성할 수 있습니다. 또한 테스트 설정에서 설명한 것처럼, Azure 지역 내에서 ExpressRoute 연결에 대한 장애 조치(failover) 경로를 만드는 데 사이트 간 VPN을 사용할 수 있습니다. ExpressRoute 및 사이트 간 VPN을 통해 동일한 접두사를 보급하는 경우 Azure는 ExpressRoute를 우선 순위로 지정합니다. ExpressRoute와 사이트 간 VPN 간의 비대칭 라우팅을 방지하기 위해 온-프레미스 네트워크 구성도 사이트 간 VPN 연결을 사용하기 전에 ExpressRoute 연결을 사용하여 교환해야 합니다.

ExpressRoute 및 사이트 간 VPN의 공존 연결을 구성하는 방법에 대한 자세한 내용은 [ExpressRoute 및 사이트 간 공존][ExR-S2S-CoEx]을 참조하세요.

## <a name="extend-back-end-connectivity-to-spoke-vnets-and-branch-locations"></a>백 엔드 연결을 스포크 VNet 및 분기 위치로 확장

### <a name="spoke-vnet-connectivity-by-using-vnet-peering"></a>VNet 피어링을 사용하여 스포크 VNet 연결

허브와 스포크 VNet 아키텍처는 널리 사용됩니다. 허브는 스포크 VNet 간 및 온-프레미스 네트워크에 대해 중앙 연결 지점 역할을 하는 Azure의 VNet입니다. 스포크는 허브와 피어링하는 VNet이며 워크로드를 격리하는 데 사용할 수 있습니다. 트래픽은 ExpressRoute 또는 VPN 연결을 통해 온-프레미스 데이터 센터와 허브 사이를 흐릅니다. 아키텍처에 대한 자세한 내용은 [Azure에서 허브-스포크 네트워크 토폴로지 구현][Hub-n-Spoke]을 참조하세요.

지역 내의 VNet 피어링에서 스포크 VNet은 허브 VNet 게이트웨이(VPN과 ExpressRoute 게이트웨이 모두)를 사용하여 원격 네트워크와 통신할 수 있습니다.

### <a name="branch-vnet-connectivity-by-using-site-to-site-vpn"></a>사이트 간 VPN을 사용하여 분기 VNet 연결

서로 다른 지역에 있는 분기 VNet 및 온-프레미스 네트워크를 허브 VNet을 통해 서로 통신하도록 할 수 있습니다. 이 구성에 대한 네이티브 Azure 솔루션은 VPN을 사용하는 사이트 간 VPN 연결입니다. 대안은 허브의 라우팅에 대해 NVA(네트워크 가상 어플라이언스)를 사용하는 것입니다.

자세한 내용은 [VPN Gateway란?][VPN] 및 [고가용성 NVA 배포][Deploy-NVA]를 참조하세요.

## <a name="next-steps"></a>다음 단계

테스트 설정의 [제어 평면 분석][Control-Analysis] 및 토폴로지에서 다른 VNet 또는 VLAN의 보기에 대해 알아봅니다.

테스트 설정의 [제어 평면 분석][Data-Analysis] 및 Azure 네트워크 모니터링 기능 보기에 대해 알아봅니다.

다음에 대해 [ExpressRoute FAQ][ExR-FAQ]를 참조하세요.
-   ExpressRoute 게이트웨이에 연결할 수 있는 ExpressRoute 회로 개수를 알아봅니다.
-   ExpressRoute 회로에 연결할 수 있는 ExpressRoute 게이트웨이 개수를 알아봅니다.
-   ExpressRoute의 다른 크기 조정 제한 사항에 대해 알아봅니다.


<!--Image References-->
[1]: ./media/backend-interoperability/SpokeVNet_peering.png "스포크 VNet의 VNet 피어링"
[2]: ./media/backend-interoperability/HubVNet-peering.png "허브 VNet의 VNet 피어링"
[3]: ./media/backend-interoperability/BranchVNet-VPNGW.png "분기 VNet의 VPN Gateway 구성"
[4]: ./media/backend-interoperability/ExR1.png "ExpressRoute 1 구성"
[5]: ./media/backend-interoperability/ExR1-Hub-Connection.png "허브 VNet ExR 게이트웨이에 대한 ExpressRoute 1의 연결 구성"
[6]: ./media/backend-interoperability/ExR2.png "ExpressRoute 2 구성"
[7]: ./media/backend-interoperability/ExR2-Hub-Connection.png "허브 VNet ExR 게이트웨이에 대한 ExpressRoute 2의 연결 구성"
[8]: ./media/backend-interoperability/ExR2-Remote-Connection.png "원격 VNet ExR 게이트웨이에 대한 ExpressRoute 2의 연결 구성"

<!--Link References-->
[Setup]: https://docs.microsoft.com/azure/networking/connectivty-interoperability-preface
[ExpressRoute]: https://docs.microsoft.com/azure/expressroute/expressroute-introduction
[VPN]: https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways
[VNet]: https://docs.microsoft.com/azure/virtual-network/tutorial-connect-virtual-networks-portal
[Configuration]: https://docs.microsoft.com/azure/networking/connectivty-interoperability-configuration
[Control-Analysis]:https://docs.microsoft.com/azure/networking/connectivty-interoperability-control-plane
[Data-Analysis]: https://docs.microsoft.com/azure/networking/connectivty-interoperability-data-plane
[ExR-FAQ]: https://docs.microsoft.com/azure/expressroute/expressroute-faqs
[S2S-Over-ExR]: https://docs.microsoft.com/azure/expressroute/site-to-site-vpn-over-microsoft-peering
[ExR-S2S-CoEx]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-coexist-resource-manager
[Hub-n-Spoke]: https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/hub-spoke
[Deploy-NVA]: https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/nva-ha
[VNet-Config]: https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-peering


