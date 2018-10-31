---
title: 'ExpressRoute, 사이트 간 VPN 및 VNet 피어링의 상호 운용성 - 구성 세부 정보: Azure 백 엔드 연결 기능 상호 운용성 | Microsoft Docs'
description: 이 페이지는 ExpressRoute, 사이트 간 VPN 및 VNet 피어링 기능의 상호 운용성을 분석하는 데 사용되는 테스트 설정의 구성 세부 정보를 제공합니다.
documentationcenter: na
services: networking
author: rambk
manager: tracsman
ms.service: expressroute,vpn-gateway,virtual-network
ms.topic: article
ms.workload: infrastructure-services
ms.date: 10/18/2018
ms.author: rambala
ms.openlocfilehash: d94900b764331c6fff0e0384e6edbebc88ac938b
ms.sourcegitcommit: 9e179a577533ab3b2c0c7a4899ae13a7a0d5252b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/23/2018
ms.locfileid: "49947098"
---
# <a name="interoperability-of-expressroute-site-to-site-vpn-and-vnet-peering---test-configuration-details"></a>ExpressRoute, 사이트 간 VPN 및 VNet 피어링의 상호 운용성 - 테스트 구성 세부 정보

이 문서에서는 테스트 설정의 구성 세부 정보를 탐색해 보겠습니다. 테스트 설정을 검토하려면 [테스트 설정][Setup]을 참조하세요. 

##<a name="spoke-vnet-connectivity-using-vnet-peering"></a>VNet 피어링을 사용하여 스포크 VNet 연결

다음 Azure Portal 스크린샷은 스포크 VNet의 VNet 피어링 세부 정보를 보여 줍니다. 두 가상 네트워크 간의 VNet 피어링을 구성하는 단계별 지침은 [VNet 피어링 관리][VNet-Config]를 참조하세요. 스포크 VNet이 허브 VNet에 연결된 게이트웨이를 사용하도록 하려면 *원격 게이트웨이 사용*을 확인해야 합니다.

[![1]][1]

다음 Azure Portal 스크린샷은 허브 VNet의 VNet 피어링 세부 정보를 보여 줍니다. 허브 VNet에서 스포크 VNet이 자체의 게이트웨이를 사용하도록 하려면 *원격 게이트웨이 사용*을 확인해야 합니다.

[![2]][2]

##<a name="branch-vnet-connectivity-using-site-to-site-vpn"></a>사이트 간 VPN을 사용하여 분기 VNet 연결

허브 VNet과 분기 VNet 간의 사이트 간 VNet 연결은 VPN Gateway를 사용하여 구성됩니다. 기본적으로 VPN 및 ExpressRoute 게이트웨이는 개인 ASN 값 65515를 사용하여 구성됩니다. VPN Gateway를 사용하여 ASN 값을 변경할 수 있습니다. 다음 Azure Portal 스크린샷과 같은 테스트 설정에서 분기 VNet VPN Gateway의 ASN 값은 허브 VNet과 분기 VNet 간에 eBGP 라우팅을 사용하도록 65516으로 변경됩니다.


[![3]][3]


##<a name="location-1-on-premises-connectivity-using-expressroute-and-site-to-site-vpn"></a>ExpressRoute 및 사이트 간 VPN을 사용하여 위치-1 온-프레미스 연결

###<a name="expressroute1-configuration-details"></a>ExpressRoute1 구성 세부 정보

다음 포털 스크린샷은 위치-1 온-프레미스 CE 라우터를 지향하는 Azure 지역 1 ExpressRoute 회로 구성을 보여 줍니다.

[![4]][4]

다음 포털 스크린샷은 ExpressRoute1 회로와 허브 VNet 간의 연결 구성을 보여 줍니다.

[![5]][5]

다음 구성은 ExpressRoute 개인 피어링 연결과 관련된 기본 CE 라우터(Cisco ASR1000 라우터가 테스트 설정의 CE 라우터로 사용됨)의 목록입니다. 사이트 간 VPN과 ExpressRoute 회로 모두 온-프레미스 네트워크를 Azure에 연결하기 위해 병렬로 구성되는 경우 Azure는 기본적으로 ExpressRoute 회로를 선호합니다. 비대칭 라우팅을 방지하려면 온-프레미스 네트워크도 ExpressRoute와 사이트 간 VPN 둘 다를 통해 수신되는 경로에 대해 사이트 간 VPN보다 ExpressRoute를 선호해야 합니다. BGP 로컬 기본 설정 특성을 사용하여 다음 구성에서 이렇게 할 수 있습니다. 

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

###<a name="site-to-site-vpn-configuration-details"></a>사이트 간 VPN 구성 세부 정보

다음은 사이트 간 VPN 연결과 관련된 기본 CE 라우터 구성의 목록입니다.

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

##<a name="location-2-on-premises-connectivity-using-expressroute"></a>ExpressRoute를 사용하여 위치 2 온-프레미스 연결

위치 2 온-프레미스에 더 근접한 두 번째 ExpressRoute는 위치 2 온-프레미스를 허브 VNet에 연결합니다. 다음 포털 스크린샷은 두 번째 ExpressRoute 구성을 보여 줍니다.

[![6]][6]

다음 포털 스크린샷은 두 번째 ExpressRoute 회로와 허브 VNet 간의 연결 구성을 보여 줍니다.

[![7]][7]

ExpressRoute1은 허브 VNet과 위치-1 온-프레미스를 모두 서로 다른 Azure 지역의 원격 VNet에 연결합니다.

[![8]][8]

## <a name="further-reading"></a>추가 참고 자료

### <a name="using-expressroute-and-site-to-site-vpn-connectivity-in-tandem"></a>탠덤에서 ExpressRoute 및 사이트 간 VPN 연결 사용

#### <a name="site-to-site-vpn-over-expressroute"></a>ExpressRoute를 통한 사이트 간 VPN

사이트 간 VPN은 ExpressRoute Microsoft 피어링을 통해 온-프레미스 네트워크와 Azure VNet 간에 비밀성, 재생 방지, 신뢰성 및 무결성을 가지고 개인적으로 데이터를 교환하도록 구성할 수 있습니다. ExpressRoute Microsoft 피어링을 통해 터널 모드에서 사이트 간 IPSec VPN을 구성하는 방법에 관한 자세한 내용은 [ExpressRoute Microsoft 피어링을 통한 사이트 간 VPN][S2S-Over-ExR]을 참조하세요. 

Microsoft 피어링을 통해 S2S VPN을 구성할 때 주요 제한은 처리량입니다. IPSec 터널을 통한 처리량은 VPN GW 용량에 의해 제한됩니다. VPN GW 처리량은 ExpressRoute 처리량에 비해 작습니다. 그러한 시나리오에서 매우 안전한 트래픽의 경우 IPSec 터널 및 모든 다른 트래픽의 경우 개인 피어링을 사용하면 ExpressRoute 대역폭 사용량을 최적화하는 데 도움이 될 수 있습니다.

#### <a name="site-to-site-vpn-as-a-secure-failover-path-for-expressroute"></a>ExpressRoute에 대한 안전한 장애 조치(failover) 경로인 사이트 간 VPN
ExpressRoute는 고가용성을 보장하기 위해 중복 회로 쌍으로 제공됩니다. 다른 Azure 지역에서 지역 중복 ExpressRoute 연결을 구성할 수 있습니다. 또한 이 테스트 설정에서 수행한 것처럼 지정된 Azure 지역 내에서 ExpressRoute 연결에 대한 장애 조치(failover) 경로를 원하는 경우 사이트 간 VPN을 사용하여 그렇게 할 수 있습니다. ExpressRoute 및 S2S VPN을 통해 동일한 접두사를 보급하는 경우 Azure는 S2S VPN보다 ExpressRoute를 선호합니다. ExpressRoute와 S2S VPN 간의 비대칭 라우팅을 방지하기 위해 온-프레미스 네트워크 구성도 S2S VPN 연결보다 ExpressRoute를 선호하여 교환해야 합니다.

ExpressRoute 및 사이트 간 VPN이 공존하는 연결에 대한 자세한 내용은 [ExpressRoute 및 사이트 간 공존][ExR-S2S-CoEx]을 참조하세요.

### <a name="extending-backend-connectivity-to-spoke-vnets-and-branch-locations"></a>백 엔드 연결을 스포크 VNet 및 분기 위치로 확장

#### <a name="spoke-vnet-connectivity-using-vnet-peering"></a>VNet 피어링을 사용하여 스포크 VNet 연결

허브와 스포크 VNet 아키텍처는 널리 사용됩니다. 허브는 스포크 VNet 간 및 온-프레미스 네트워크에 대해 중앙 연결 지점 역할을 하는 Azure의 VNet(가상 네트워크)입니다. 스포크는 허브와 피어링하는 VNet이며 워크로드를 격리하는 데 사용할 수 있습니다. 트래픽은 ExpressRoute 또는 VPN 연결을 통해 온-프레미스 데이터 센터와 허브 사이를 흐릅니다. 아키텍처에 대한 자세한 내용은 [허브 및 스포크 아키텍처][Hub-n-Spoke]를 참조하세요.

하위 지역 내에서 VNet 피어링을 통해 스포크 VNet은 허브 VNet 게이트웨이(VPN과 ExpressRoute 게이트웨이 모두)를 사용하여 원격 네트워크와 통신할 수 있습니다.

#### <a name="branch-vnet-connectivity-using-site-to-site-vpn"></a>사이트 간 VPN을 사용하여 분기 VNet 연결

분기 VNet(다른 하위 지역의)과 온-프레미스 네트워크가 허브 VNet을 통해 서로 통신하도록 하려는 경우 원시 Azure 솔루션은 VPN을 사용하는 사이트 간 VPN 연결입니다. 대안 옵션은 허브의 라우팅을 위해 NVA를 사용하는 것입니다.

VPN Gateway 구성에 대해서는 [VPN Gateway 구성][VPN]을 참조하세요. 고가용성 NVA 배포에 대해서는 [고가용성 NVA 배포][Deploy-NVA]를 참조하세요.

## <a name="next-steps"></a>다음 단계

테스트 설정에 대한 제어 평면 분석 및 토폴로지의 서로 다른 VNet/VLAN 뷰를 이해하려면 [제어 평면 분석][Control-Analysis]을 참조하세요.

테스트 설정의 제어 평면 분석 및 Azure 네트워크 모니터링 기능 뷰는 [데이터 평면 분석][Data-Analysis]을 참조하세요.

ExpressRoute 게이트웨이에 연결할 수 있는 ExpressRoute 회로 수, ExpressRoute 회로에 연결할 수 있는 ExpressRoute 게이트웨이 수 또는 ExpressRoute의 다른 규모 제한에 대해 알아보려면 [ExpressRoute FAQ][ExR-FAQ]를 참조하세요.


<!--Image References-->
[1]: ./media/backend-interoperability/SpokeVNet_peering.png "스포크 VNet의 VNet 피어링"
[2]: ./media/backend-interoperability/HubVNet-peering.png "허브 VNet의 VNet 피어링"
[3]: ./media/backend-interoperability/BranchVNet-VPNGW.png "분기 VNet의 VPN GW 구성"
[4]: ./media/backend-interoperability/ExR1.png "ExpressRoute1 구성"
[5]: ./media/backend-interoperability/ExR1-Hub-Connection.png "허브 VNet ExR GW에 대한 ExpressRoute1의 연결 구성"
[6]: ./media/backend-interoperability/ExR2.png "ExpressRoute2 구성"
[7]: ./media/backend-interoperability/ExR2-Hub-Connection.png "허브 VNet ExR GW에 대한 ExpressRoute2의 연결 구성"
[8]: ./media/backend-interoperability/ExR2-Remote-Connection.png "원격 VNet ExR GW에 대한 ExpressRoute2 연결 구성"

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




