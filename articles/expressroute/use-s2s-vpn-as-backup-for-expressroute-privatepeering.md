---
title: Azure ExpressRoute 프라이빗 피어링의 백업으로 S2S VPN 사용 | 마이크로 소프트 문서
description: 이 페이지에서는 S2S VPN을 사용하여 Azure ExpressRoute 개인 피어링을 백업하기 위한 아키텍처 권장 사항을 제공합니다.
services: networking
author: rambk
ms.service: expressroute
ms.topic: article
ms.date: 02/05/2020
ms.author: rambala
ms.openlocfilehash: a6a22b667bc66d6ee69bfbd7ad1db88f72d8df0e
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81687859"
---
# <a name="using-s2s-vpn-as-a-backup-for-expressroute-private-peering"></a>ExpressRoute 개인 피어링을 위한 백업으로 S2S VPN 사용

[ExpressRoute 개인 피어링을 사용하여 재해 복구를 위한 설계라는][DR-PP]제목의 문서에서 ExpressRoute 개인 피어링 연결에 대한 백업 연결 솔루션의 필요성과 용도에 따라 지리적 중복 ExpressRoute 회로를 사용하는 방법에 대해 설명했습니다. 이 문서에서는 ExpressRoute 개인 피어링의 백업으로 사이트 간(S2S) VPN을 활용하고 유지하는 방법을 살펴보겠습니다. 

지리적 중복 ExpressRoute 회로와 달리, 당신은 능동 수동 모드에서만 ExpressRoute-VPN 재해 복구 조합을 사용할 수 있습니다. 수동 모드에서 백업 네트워크 연결을 사용하는 주요 과제는 수동 연결이 기본 연결과 함께 실패하는 경우가 많다는 것입니다. 수동 연결실패의 일반적인 원인은 능동적인 유지 관리가 부족하기 때문입니다. 따라서 이 문서에서는 ExpressRoute 개인 피어링을 백업하는 S2S VPN 연결을 확인하고 적극적으로 유지하는 방법에 중점을 두겠습니다.

>[!NOTE] 
>지정된 경로가 ExpressRoute 및 VPN을 통해 보급되는 경우 Azure는 ExpressRoute를 통해 라우팅하는 것을 선호합니다.  
>

이 문서에서는 Azure 관점과 고객 측 네트워크 에지 관점에서 연결을 확인하는 방법을 살펴보겠습니다. Microsoft 네트워크 엔터티를 사용하는 고객 측 네트워크 장치를 관리하든 관계 없이 양쪽 끝에서 유효성을 검사하는 기능이 도움이 됩니다. 

## <a name="example-topology"></a>예제 토폴로지

설정에서 ExpressRoute 회로와 S2S VPN 연결을 통해 Azure 허브 VNet에 연결된 온-프레미스 네트워크가 있습니다. 아래 다이어그램과 같이 Azure 허브 VNet은 스포크 VNet을 차례로 피어로 이동합니다.

![1][1]

설정에서 ExpressRoute 회로는 온-프레미스의 CE(고객 에지) 라우터 쌍에서 종료됩니다. 온-프레미스 LAN은 리더 팔로워 모드에서 작동하는 방화벽 쌍을 통해 CE 라우터에 연결됩니다. S2S VPN은 방화벽에서 직접 종료됩니다.

다음 표에는 토폴로지의 주요 IP 접두사가 나열되어 있습니다.

| **엔터티** | **접두사** |
| --- | --- |
| 온-프레미스 LAN | 10.1.11.0/25 |
| Azure 허브 VNet | 10.17.11.0/25 |
| Azure 스포크 VNet | 10.17.11.128/26 |
| 온-프레미스 테스트 서버 | 10.1.11.10 |
| 스포크 VNet 테스트 VM | 10.17.11.132 |
| 익스프레스루트 기본 연결 p2p 서브넷 | 192.168.11.16/30 |
| 익스프레스루트 보조 연결 p2p 서브넷 | 192.168.11.20/30 |
| VPN 게이트웨이 기본 BGP 피어 IP | 10.17.11.76 |
| VPN 게이트웨이 보조 BGP 피어 IP | 10.17.11.77 |
| 온-프레미스 방화벽 VPN BGP 피어 IP | 192.168.11.88 |
| 방화벽 IP를 향한 기본 CE 라우터 i/f | 192.168.11.0/31 |
| 기본 CE 라우터 IP를 향한 방화벽 i/f | 192.168.11.1/31 |
| 방화벽 IP를 향한 보조 CE 라우터 i/f | 192.168.11.2/31 |
| 보조 CE 라우터 IP를 향한 방화벽 i/f | 192.168.11.3/31 |


다음 표에는 토폴로지의 ASN이 나열되어 있습니다.

| **자율 시스템** | **Asn** |
| --- | --- |
| 온-프레미스 | 65020 |
| 마이크로소프트 엔터프라이즈 에지 | 12076 |
| 가상 네트워크 GW(ExR) | 65515 |
| 가상 네트워크 GW (VPN) | 65515 |

## <a name="high-availability-without-asymmetricity"></a>비대칭성 없는 고가용성

### <a name="configuring-for-high-availability"></a>고가용성 을 위한 구성

[ExpressRoute 및 사이트 간 공존 연결을 구성하여][Conf-CoExist] 공존하는 ExpressRoute 회로 및 S2S VPN 연결을 구성하는 방법에 대해 설명합니다. [ExpressRoute를 사용하여 고가용성을 위한 설계에서][HA]설명했듯이 ExpressRoute 고가용성을 개선하기 위해 설정은 네트워크 중복성(단일 실패 지점 방지)을 끝점까지 유지합니다. 또한 ExpressRoute 회로의 기본 연결과 보조 연결은 두 연결을 통해 온-프레미스 접두사를 동일한 방식으로 광고하여 활성 모드로 작동하도록 구성됩니다. 

ExpressRoute 회로의 기본 연결을 통해 기본 CE 라우터의 온-프레미스 라우팅 광고는 아래 표시됩니다(Junos 명령)

    user@SEA-MX03-01> show route advertising-protocol bgp 192.168.11.18 

    Cust11.inet.0: 8 destinations, 8 routes (7 active, 0 holddown, 1 hidden)
      Prefix                  Nexthop              MED     Lclpref    AS path
    * 10.1.11.0/25            Self                                    I

ExpressRoute 회로의 보조 연결을 통해 보조 CE 라우터의 온-프레미스 경로 광고는 아래 표시됩니다(Junos 명령)

    user@SEA-MX03-02> show route advertising-protocol bgp 192.168.11.22 

    Cust11.inet.0: 8 destinations, 8 routes (7 active, 0 holddown, 1 hidden)
      Prefix                  Nexthop              MED     Lclpref    AS path
    * 10.1.11.0/25            Self                                    I

백업 연결의 고가용성을 개선하기 위해 S2S VPN도 활성 모드로 구성됩니다. Azure VPN 게이트웨이 구성은 다음과 같습니다. VPN 구성 VPN의 일부로 게이트웨이의 BGP 피어 IP 주소(10.17.11.76 및 10.17.11.77)도 나열됩니다.

![2][2]

온-프레미스 경로는 VPN 게이트웨이의 기본 및 보조 BGP 피어에 방화벽에 의해 보급됩니다. 경로 광고는 아래(Junos)에 표시됩니다.

    user@SEA-SRX42-01> show route advertising-protocol bgp 10.17.11.76 

    Cust11.inet.0: 14 destinations, 21 routes (14 active, 0 holddown, 0 hidden)
      Prefix                  Nexthop              MED     Lclpref    AS path
    * 10.1.11.0/25            Self                                    I

    {primary:node0}
    user@SEA-SRX42-01> show route advertising-protocol bgp 10.17.11.77    

    Cust11.inet.0: 14 destinations, 21 routes (14 active, 0 holddown, 0 hidden)
      Prefix                  Nexthop              MED     Lclpref    AS path
    * 10.1.11.0/25            Self                                    I

>[!NOTE] 
>S2S VPN을 활성 모드로 구성하면 재해 복구 백업 네트워크 연결에 고가용성을 제공 할뿐만 아니라 백업 연결에 더 높은 처리량을 제공합니다. 즉, S2S VPN을 활성 모드로 구성하는 것이 여러 기본 터널을 강제로 만들 때 권장됩니다.
>

### <a name="configuring-for-symmetric-traffic-flow"></a>대칭 트래픽 흐름 구성

지정된 온-프레미스 경로가 ExpressRoute 및 S2S VPN을 통해 보급될 때 Azure는 ExpressRoute 경로를 선호합니다. Azure가 공존하는 ExpressRoute보다 S2S VPN 경로를 선호하도록 하려면 VPN 연결을 통해 보다 구체적인 경로(더 큰 서브넷 마스크가 있는 더 긴 접두사)를 광고해야 합니다. 여기서 우리의 목표는 VPN 연결을 백으로만 사용하는 것입니다. 따라서 Azure의 기본 경로 선택 동작은 목표와 일치합니다. 

온-프레미스에서 Azure로 향하는 트래픽이 S2S VPN보다 ExpressRoute 경로를 선호하도록 하는 것은 당사의 책임입니다. 온-프레미스 설정에서 CE 라우터 및 방화벽의 기본 로컬 기본 설정은 100입니다. 따라서 ExpressRoute 개인 피어링을 통해 수신된 경로의 로컬 기본 설정을 100(예: 150)보다 큰 설정하여 Azure로 향하는 트래픽이 정상 상태에서 ExpressRoute 회로를 선호하도록 할 수 있습니다.

ExpressRoute 회로의 기본 연결을 종료하는 기본 CE 라우터의 BGP 구성은 다음과 같습니다. iBGP 세션에 대해 보급된 경로의 로컬 기본 설정 값은 150으로 구성됩니다. 마찬가지로, ExpressRoute 회로의 보조 연결을 종료하는 보조 CE 라우터의 로컬 기본 설정도 150으로 구성되도록 해야 합니다.

    user@SEA-MX03-01> show configuration routing-instances Cust11 
    description "Customer 11 VRF";
    instance-type virtual-router;
    interface xe-0/0/0:0.110;
    interface ae0.11;
    protocols {
      bgp {
        group ibgp {
            type internal;
            local-preference 150;
            neighbor 192.168.11.1;
        }
        group ebgp {
            peer-as 12076;
            bfd-liveness-detection {
                minimum-interval 300;
                multiplier 3;
            }
            neighbor 192.168.11.18;
        }
      }
    }

온-프레미스 방화벽의 라우팅 테이블은 Azure로 향하는 온-프레미스 트래픽의 경우 기본 경로가 정상 상태의 ExpressRoute를 초과한다는 것을 확인합니다(아래 참조).

    user@SEA-SRX42-01> show route table Cust11.inet.0 10.17.11.0/24    

    Cust11.inet.0: 14 destinations, 21 routes (14 active, 0 holddown, 0 hidden)
    + = Active Route, - = Last Active, * = Both

    10.17.11.0/25      *[BGP/170] 2d 00:34:04, localpref 150
                          AS path: 12076 I, validation-state: unverified
                        > to 192.168.11.0 via reth1.11
                          to 192.168.11.2 via reth2.11
                        [BGP/170] 2d 00:34:01, localpref 150
                          AS path: 12076 I, validation-state: unverified
                        > to 192.168.11.2 via reth2.11
                        [BGP/170] 2d 21:12:13, localpref 100, from 10.17.11.76
                          AS path: 65515 I, validation-state: unverified
                        > via st0.118
                        [BGP/170] 2d 00:41:51, localpref 100, from 10.17.11.77
                          AS path: 65515 I, validation-state: unverified
                        > via st0.119
    10.17.11.76/32     *[Static/5] 2d 21:12:16
                        > via st0.118
    10.17.11.77/32     *[Static/5] 2d 00:41:56
                        > via st0.119
    10.17.11.128/26    *[BGP/170] 2d 00:34:04, localpref 150
                          AS path: 12076 I, validation-state: unverified
                        > to 192.168.11.0 via reth1.11
                          to 192.168.11.2 via reth2.11
                        [BGP/170] 2d 00:34:01, localpref 150
                          AS path: 12076 I, validation-state: unverified
                        > to 192.168.11.2 via reth2.11
                        [BGP/170] 2d 21:12:13, localpref 100, from 10.17.11.76
                          AS path: 65515 I, validation-state: unverified
                        > via st0.118
                        [BGP/170] 2d 00:41:51, localpref 100, from 10.17.11.77
                          AS path: 65515 I, validation-state: unverified
                        > via st0.119

위의 경로 표에서 허브 및 스포크 VNet 경로--10.17.11.0/25 및 10.17.11.128/26--우리는 익스프레스루트 회로가 VPN 연결보다 선호되는 것을 참조하십시오. 192.168.11.0 및 192.168.11.2는 CE 라우터를 향한 방화벽 인터페이스의 IP입니다.

## <a name="validation-of-route-exchange-over-s2s-vpn"></a>S2S VPN을 통한 경로 교환 검증

이 문서의 뒷부분에서 는 방화벽의 온-프레미스 라우팅 보급 알림을 VPN 게이트웨이의 기본 및 보조 BGP 피어로 확인했습니다. 또한 VPN 게이트웨이의 기본 및 보조 BGP 피어에서 방화벽에서 수신한 Azure 경로를 확인해 보겠습니다.

    user@SEA-SRX42-01> show route receive-protocol bgp 10.17.11.76 table Cust11.inet.0 

    Cust11.inet.0: 14 destinations, 21 routes (14 active, 0 holddown, 0 hidden)
      Prefix                  Nexthop              MED     Lclpref    AS path
      10.17.11.0/25           10.17.11.76                             65515 I
      10.17.11.128/26         10.17.11.76                             65515 I

    {primary:node0}
    user@SEA-SRX42-01> show route receive-protocol bgp 10.17.11.77 table Cust11.inet.0    

    Cust11.inet.0: 14 destinations, 21 routes (14 active, 0 holddown, 0 hidden)
      Prefix                  Nexthop              MED     Lclpref    AS path
      10.17.11.0/25           10.17.11.77                             65515 I
      10.17.11.128/26         10.17.11.77                             65515 I

마찬가지로 Azure VPN 게이트웨이에서 받은 온-프레미스 네트워크 경로 접두사를 확인해 보겠습니다. 

    PS C:\Users\user> Get-AzVirtualNetworkGatewayLearnedRoute -ResourceGroupName SEA-Cust11 -VirtualNetworkGatewayName SEA-Cust11-VNet01-gw-vpn | where {$_.Network -eq "10.1.11.0/25"} | select Network, NextHop, AsPath, Weight

    Network      NextHop       AsPath      Weight
    -------      -------       ------      ------
    10.1.11.0/25 192.168.11.88 65020        32768
    10.1.11.0/25 10.17.11.76   65020        32768
    10.1.11.0/25 10.17.11.69   12076-65020  32769
    10.1.11.0/25 10.17.11.69   12076-65020  32769
    10.1.11.0/25 192.168.11.88 65020        32768
    10.1.11.0/25 10.17.11.77   65020        32768
    10.1.11.0/25 10.17.11.69   12076-65020  32769
    10.1.11.0/25 10.17.11.69   12076-65020  32769

위에서 볼 수 있듯이 VPN 게이트웨이에는 VPN 게이트웨이의 기본 및 보조 BGP 피어가 수신한 경로가 있습니다. 또한 기본 및 보조 ExpressRoute 연결을 통해 수신된 경로(AS 경로가 12076로 준비된 경로)를 통해 수신된 경로에 대한 가시성도 제공합니다. VPN 연결을 통해 수신된 경로를 확인하려면 연결의 온-프레미스 BGP 피어 IP를 알아야 합니다. 고려 중인 설정에서 192.168.11.88이며, 우리는 그것에서 받은 경로를 볼 수 있습니다.

다음으로 Azure VPN 게이트웨이에서 온-프레미스 방화벽 BGP 피어(192.168.11.88)로 보급하는 경로를 확인해 보겠습니다.

    PS C:\Users\user> Get-AzVirtualNetworkGatewayAdvertisedRoute -Peer 192.168.11.88 -ResourceGroupName SEA-Cust11 -VirtualNetworkGatewayName SEA-Cust11-VNet01-gw-vpn |  select Network, NextHop, AsPath, Weight

    Network         NextHop     AsPath Weight
    -------         -------     ------ ------
    10.17.11.0/25   10.17.11.76 65515       0
    10.17.11.128/26 10.17.11.76 65515       0
    10.17.11.0/25   10.17.11.77 65515       0
    10.17.11.128/26 10.17.11.77 65515       0


경로 교환을 볼 수 없습니다 연결 실패를 나타냅니다. [문제 해결: Azure 사이트 간 VPN 연결은 연결할 수 없으며][VPN Troubleshoot] VPN 연결 문제 해결에 대한 도움말 작업을 중지합니다.

## <a name="testing-failover"></a>테스트 장애 조치

이제 VPN 연결(제어 평면)을 통해 성공적인 경로 교환을 확인되었으므로 ExpressRoute 연결에서 VPN 연결로 트래픽(데이터 평면)을 전환하도록 설정되어 있습니다. 

>[!NOTE] 
>프로덕션 환경에서 장애 조치 테스트는 서비스 중단이 될 수 있으므로 예약된 네트워크 유지 관리 작업 기간 동안 수행해야 합니다.
>

트래픽 전환을 하기 전에 온-프레미스 테스트 서버에서 스포크 VNet의 테스트 VM에 대한 설정의 현재 경로를 추적해 보겠습니다.

    C:\Users\PathLabUser>tracert 10.17.11.132

    Tracing route to 10.17.11.132 over a maximum of 30 hops

      1    <1 ms    <1 ms    <1 ms  10.1.11.1
      2    <1 ms    <1 ms    11 ms  192.168.11.0
      3    <1 ms    <1 ms    <1 ms  192.168.11.18
      4     *        *        *     Request timed out.
      5     6 ms     6 ms     5 ms  10.17.11.132

    Trace complete.

설정의 기본 및 보조 ExpressRoute 지점 간 연결 서브넷은 각각 192.168.11.16/30 및 192.168.11.20/30입니다. 위의 추적 경로에서 3 단계에서 우리는 기본 MSEE의 인터페이스 IP인 192.168.11.18을 타격하는 것을 볼 수 있습니다. MSEE 인터페이스의 존재는 예상대로 우리의 현재 경로가 ExpressRoute를 통해 있음을 확인합니다.

[Reset ExpressRoute 회로 피어링에][RST]보고된 대로 다음 파워쉘 명령을 사용하여 ExpressRoute 회로의 기본 피어링과 보조 피어링을 모두 사용하지 않도록 설정해 보겠습니다.

    $ckt = Get-AzExpressRouteCircuit -Name "expressroute name" -ResourceGroupName "SEA-Cust11"
    $ckt.Peerings[0].State = "Disabled"
    Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt

장애 조치 전환 시간은 BGP 수렴 시간에 따라 다릅니다. 설정에서 장애 조치 전환은 몇 초(10초 미만)가 걸립니다. 전환 후 추적 경로의 반복은 다음 경로를 표시합니다.

    C:\Users\PathLabUser>tracert 10.17.11.132

    Tracing route to 10.17.11.132 over a maximum of 30 hops

      1    <1 ms    <1 ms    <1 ms  10.1.11.1
      2     *        *        *     Request timed out.
      3     6 ms     7 ms     9 ms  10.17.11.132

    Trace complete.

추적 경로 결과는 S2S VPN을 통한 백업 연결이 활성 상태임을 확인하고 기본 및 보조 ExpressRoute 연결이 모두 실패할 경우 서비스 연속성을 제공할 수 있습니다. 장애 조치 테스트를 완료하려면 다음 명령 집합을 사용하여 ExpressRoute 연결을 다시 활성화하고 트래픽 흐름을 정규화해 보겠습니다.

    $ckt = Get-AzExpressRouteCircuit -Name "expressroute name" -ResourceGroupName "SEA-Cust11"
    $ckt.Peerings[0].State = "Enabled"
    Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt

트래픽이 다시 익스프레스루트로 전환되었는지 확인하려면 추적 경로를 반복하고 ExpressRoute 개인 피어링을 통과하고 있는지 확인합니다.

## <a name="next-steps"></a>다음 단계

ExpressRoute는 Microsoft 네트워크 내에서 단일 실패 지점없이 고가용성을 위해 설계되었습니다. 여전히 ExpressRoute 회로는 단일 지리적 지역과 서비스 공급자에 국한되어 있습니다. S2S VPN은 ExpressRoute 회로에 대한 좋은 재해 복구 수동 백업 솔루션이 될 수 있습니다. 신뢰할 수 있는 수동 백업 연결 솔루션의 경우 수동 구성의 정기적인 유지 관리 및 정기적인 유효성 검사 연결이 중요합니다. VPN 구성이 부실해지도록 하지 말고 주기적으로(매 분기마다) 유지 관리 기간 동안 이 문서에 설명된 유효성 검사 및 장애 조치 테스트 단계를 반복해야 합니다.

VPN 게이트웨이 메트릭을 기반으로 모니터링 및 경고를 활성화하려면 [VPN 게이트웨이 메트릭에 대한 경고 설정을 참조하세요.][VPN-alerts]

ExpressRoute 오류 에 따른 BGP 수렴을 신속하게 해결하려면 [ExpressRoute를 통해 BFD를 구성합니다.][BFD]

<!--Image References-->
[1]: ./media/use-s2s-vpn-as-backup-for-expressroute-privatepeering/topology.png "고려 중인 토폴로지"
[2]: ./media/use-s2s-vpn-as-backup-for-expressroute-privatepeering/vpn-gw-config.png "VPN GW 구성"

<!--Link References-->
[DR-PP]: https://docs.microsoft.com/azure/expressroute/designing-for-disaster-recovery-with-expressroute-privatepeering
[Conf-CoExist]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-coexist-resource-manager
[HA]: https://docs.microsoft.com/azure/expressroute/designing-for-high-availability-with-expressroute
[VPN Troubleshoot]: https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-troubleshoot-site-to-site-cannot-connect
[VPN-alerts]: https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric
[BFD]: https://docs.microsoft.com/azure/expressroute/expressroute-bfd
[RST]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-reset-peering



