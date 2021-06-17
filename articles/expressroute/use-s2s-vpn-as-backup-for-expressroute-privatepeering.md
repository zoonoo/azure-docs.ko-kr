---
title: Azure ExpressRoute 프라이빗 피어링의 백업으로 S2S VPN 사용 | Microsoft Docs
description: 이 페이지에서는 S2S VPN을 사용하여 Azure ExpressRoute 프라이빗 피어링을 백업하기 위한 아키텍처 권장 사항을 제공합니다.
services: networking
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 02/05/2020
ms.author: duau
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 024d755b842a156ae52021dc9d44bd31e60088b9
ms.sourcegitcommit: 20acb9ad4700559ca0d98c7c622770a0499dd7ba
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/29/2021
ms.locfileid: "110694998"
---
# <a name="using-s2s-vpn-as-a-backup-for-expressroute-private-peering"></a>ExpressRoute 프라이빗 피어링의 백업으로 S2S VPN 사용

[ExpressRoute 프라이빗 피어링을 사용한 재해 복구를 위한 설계][DR-PP] 문서에서는 ExpressRoute 프라이빗 피어링 연결용 백업 연결 솔루션의 필요성 및 해당 목적으로 지역 중복 ExpressRoute 회로를 사용하는 방법을 설명했습니다. 이 문서에서는 S2S(사이트 간) VPN을 ExpressRoute 프라이빗 피어링의 백업으로 활용하고 유지 관리하는 방법을 살펴봅시다. 

지역 중복 ExpressRoute 회로와는 달리, ExpressRoute-VPN 재해 복구 조합은 활성-수동 모드에서만 사용할 수 있습니다. 수동 모드에서 백업 네트워크 연결을 사용하는 경우의 주요 문제는 수동 연결이 기본 연결과 함께 실패하는 경우가 많다는 것입니다. 수동 연결 오류가 발생하는 일반적인 이유는 활성 유지 관리 부족입니다. 따라서 이 문서에서는 ExpressRoute 프라이빗 피어링을 백업하는 S2S VPN 연결을 확인하고 적극적으로 유지 관리하는 방법을 중점적으로 살펴봅시다.

>[!NOTE] 
>지정된 경로가 ExpressRoute와 VPN 둘 다를 통해 보급되는 경우 Azure는 ExpressRoute를 통한 라우팅을 선호합니다.  
>

이 문서에서는 Azure 관점과 고객 쪽 네트워크 에지 관점에서 연결을 확인하는 방법을 알아봅시다. 한쪽 끝에서 유효성을 검사하는 기능은 Microsoft 네트워크 엔터티와 피어링되는 고객 쪽 네트워크 디바이스를 관리하는지 여부에 관계없이 도움이 됩니다. 

## <a name="example-topology"></a>예제 토폴로지

예제 설치에서 온-프레미스 네트워크는 ExpressRoute 회로와 S2S VPN 연결 둘 다를 통해 Azure 허브 VNet에 연결되어 있습니다. 아래 다이어그램에서 볼 수 있듯이, Azure 허브 VNet은 다시 스포크 VNet에 피어링됩니다.

![1][1]

설치에서 ExpressRoute 회로는 온-프레미스의 CE(“고객 에지”) 라우터 쌍에서 종료됩니다. 온-프레미스 LAN은 리더-팔로워 모드로 작동하는 한 쌍의 방화벽을 통해 CE 라우터에 연결됩니다. S2S VPN은 방화벽에서 직접 종료됩니다.

다음 표에는 토폴로지의 키 IP 접두사가 나와 있습니다.

| **엔터티** | **Prefix** |
| --- | --- |
| 온-프레미스 LAN | 10.1.11.0/25 |
| Azure 허브 VNet | 10.17.11.0/25 |
| Azure 스포크 VNet | 10.17.11.128/26 |
| 온-프레미스 테스트 서버 | 10.1.11.10 |
| 스포크 VNet 테스트 VM | 10.17.11.132 |
| ExpressRoute 기본 연결 p2p 서브넷 | 192.168.11.16/30 |
| ExpressRoute 보조 연결 p2p 서브넷 | 192.168.11.20/30 |
| VPN 게이트웨이 기본 BGP 피어 IP | 10.17.11.76 |
| VPN 게이트웨이 보조 BGP 피어 IP | 10.17.11.77 |
| 온-프레미스 방화벽 VPN BGP 피어 IP | 192.168.11.88 |
| 방화벽 IP 지향 기본 CE 라우터 인터페이스 | 192.168.11.0/31 |
| 기본 CE 라우터 IP 지향 방화벽 인터페이스 | 192.168.11.1/31 |
| 방화벽 IP 지향 보조 CE 라우터 인터페이스 | 192.168.11.2/31 |
| 보조 CE 라우터 IP 지향 방화벽 인터페이스 | 192.168.11.3/31 |


다음 표에는 토폴로지의 ASN이 나와 있습니다.

| **자율 시스템** | **ASN** |
| --- | --- |
| 온-프레미스 | 65020 |
| Microsoft Enterprise Edge | 12076 |
| 가상 네트워크 게이트웨이(ExR) | 65515 |
| 가상 네트워크 게이트웨이(VPN) | 65515 |

## <a name="high-availability-without-asymmetricity"></a>비대칭이 없는 고가용성

### <a name="configuring-for-high-availability"></a>고가용성을 위한 구성

[ExpressRoute 및 사이트 간 공존 연결 구성][Conf-CoExist]에서는 공존하는 ExpressRoute 회로 및 S2S VPN 연결을 구성하는 방법을 설명합니다. [ExpressRoute를 사용한 고가용성을 위한 설계][HA]에서 설명한 것처럼 ExpressRoute 고가용성을 높이기 위해 예제 설치는 엔드포인트까지 네트워크 중복성(단일 실패 지점 방지)을 유지 관리합니다. 또한 ExpressRoute 회로의 기본 및 보조 연결은 모두 두 연결을 통해 동일한 방식으로 온-프레미스 접두사를 보급하여 활성-활성 모드에서 작동하도록 구성되었습니다. 

ExpressRoute 회로의 기본 연결을 통한 기본 CE 라우터의 온-프레미스 경로 보급 알림은 아래와 같습니다(Junos 명령).

```console
user@SEA-MX03-01> show route advertising-protocol bgp 192.168.11.18 

Cust11.inet.0: 8 destinations, 8 routes (7 active, 0 holddown, 1 hidden)
  Prefix                  Nexthop              MED     Lclpref    AS path
* 10.1.11.0/25            Self                                    I
```

ExpressRoute 회로의 보조 연결을 통한 보조 CE 라우터의 온-프레미스 경로 보급 알림은 아래와 같습니다(Junos 명령).

```console
user@SEA-MX03-02> show route advertising-protocol bgp 192.168.11.22 

Cust11.inet.0: 8 destinations, 8 routes (7 active, 0 holddown, 1 hidden)
  Prefix                  Nexthop              MED     Lclpref    AS path
* 10.1.11.0/25            Self                                    I
```

백업 연결의 고가용성을 높이기 위해 S2S VPN도 활성-활성 모드로 구성되었습니다. Azure VPN Gateway 구성은 아래와 같습니다. VPN 구성 VPN의 일부로 게이트웨이의 BGP 피어 IP 주소(10.17.11.76 및 10.17.11.77)도 나열됩니다.

![2][2]

온-프레미스 경로는 방화벽에서 VPN 게이트웨이의 기본 및 보조 BGP 피어로 보급됩니다. 경로 보급 알림은 아래와 같습니다(Junos).

```console
user@SEA-SRX42-01> show route advertising-protocol bgp 10.17.11.76 

Cust11.inet.0: 14 destinations, 21 routes (14 active, 0 holddown, 0 hidden)
  Prefix                  Nexthop              MED     Lclpref    AS path
* 10.1.11.0/25            Self                                    I

{primary:node0}
user@SEA-SRX42-01> show route advertising-protocol bgp 10.17.11.77    

Cust11.inet.0: 14 destinations, 21 routes (14 active, 0 holddown, 0 hidden)
  Prefix                  Nexthop              MED     Lclpref    AS path
* 10.1.11.0/25            Self                                    I
```

>[!NOTE] 
>활성-활성 모드로 S2S VPN을 구성하면 재해 복구 백업 네트워크 연결에 고가용성을 제공할 뿐만 아니라 백업 연결에 높은 처리량도 제공합니다. 즉, 여러 개의 기본 터널을 강제로 만들도록 활성-활성 모드로 S2S VPN을 구성하는 것이 좋습니다.
>

### <a name="configuring-for-symmetric-traffic-flow"></a>대칭 트래픽 흐름을 위한 구성

ExpressRoute 및 S2S VPN을 통해 지정된 온-프레미스 경로를 보급하는 경우 Azure는 ExpressRoute 경로를 선호합니다. Azure가 공존하는 ExpressRoute보다 S2S VPN 경로를 선호하도록 강제 적용하려면 VPN 연결을 통해 더 구체적인 경로(큰 서브넷 마스크를 사용하는 긴 접두사)를 보급해야 합니다. 여기서는 VPN 연결을 백업으로만 사용하는 것을 목표로 합니다. 따라서 Azure의 기본 경로 선택 동작은 목표와 일치합니다. 

온-프레미스에서 Azure로 향하는 트래픽이 S2S VPN보다 ExpressRoute 경로를 선호하는지 확인하는 것은 사용자 책임입니다. 온-프레미스 설치에서 CE 라우터 및 방화벽의 로컬 기본 설정은 100입니다. 따라서 ExpressRoute 프라이빗 피어링을 통해 수신된 경로의 로컬 기본 설정을 100보다 큰(예: 150) 값으로 구성하면 Azure로 향하는 트래픽이 정상 상태로 ExpressRoute 회로를 선호하도록 만들 수 있습니다.

ExpressRoute 회로의 기본 연결을 종료하는 기본 CE 라우터의 BGP 구성은 아래와 같습니다. IBGP 세션을 통해 보급된 경로의 로컬 기본 설정 값은 150으로 구성되었습니다. 마찬가지로, ExpressRoute 회로의 보조 연결을 종료하는 보조 CE 라우터의 로컬 기본 설정도 150으로 구성되었는지 확인해야 합니다.

```console
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
```

온-프레미스 방화벽의 라우팅 테이블은 Azure로 향하는 온-프레미스 트래픽이 정상 상태로 ExpressRoute를 통한 경로를 선호하는지 확인합니다(아래 참조).

```console
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
```

위의 경로 테이블에서 허브 및 스포크 VNet 경로(-10.17.11.0/25 및 10.17.11.128/26--)가 VPN 연결보다 ExpressRoute 회로를 선호하는 것을 확인할 수 있습니다. 192.168.11.0 및 192.168.11.2는 CE 라우터 지향 방화벽 인터페이스의 IP입니다.

## <a name="validation-of-route-exchange-over-s2s-vpn"></a>S2S VPN을 통한 경로 교환의 유효성 검사

이 문서의 앞부분에서 VPN 게이트웨이의 기본 및 보조 BGP 피어에 대한 방화벽의 온-프레미스 경로 보급 알림을 확인했습니다. 방화벽이 VPN 게이트웨이의 기본 및 보조 BGP 피어에서 수신한 Azure 경로도 확인해 봅시다.

```console
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
```

마찬가지로, Azure VPN Gateway가 수신한 온-프레미스 네트워크 경로 접두사에 대해 확인하겠습니다. 

```powershell
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
```

위에서 확인한 것처럼 VPN 게이트웨이에는 VPN 게이트웨이의 기본 및 보조 BGP 피어가 둘 다 수신한 경로가 있습니다. 기본 및 보조 ExpressRoute 연결을 통해 수신된 경로(AS 경로 앞에 12076이 추가된 경로)도 표시됩니다. VPN 연결을 통해 수신된 경로를 확인하려면 연결의 온-프레미스 BGP 피어 IP를 알고 있어야 합니다. 고려 중인 설치에서는 192.168.11.88이고 해당 IP에서 수신된 경로를 볼 수 있습니다.

다음으로, Azure VPN Gateway가 온-프레미스 방화벽 BGP 피어에 보급한 경로(192.168.11.88)를 확인해 봅시다.

```powershell
PS C:\Users\user> Get-AzVirtualNetworkGatewayAdvertisedRoute -Peer 192.168.11.88 -ResourceGroupName SEA-Cust11 -VirtualNetworkGatewayName SEA-Cust11-VNet01-gw-vpn |  select Network, NextHop, AsPath, Weight

Network         NextHop     AsPath Weight
-------         -------     ------ ------
10.17.11.0/25   10.17.11.76 65515       0
10.17.11.128/26 10.17.11.76 65515       0
10.17.11.0/25   10.17.11.77 65515       0
10.17.11.128/26 10.17.11.77 65515       0
```

경로 교환이 표시되지 않으면 연결 실패를 나타냅니다. VPN 연결 문제 해결에 대한 도움이 필요하면 [문제 해결: Azure 사이트 간 VPN 연결에서 연결할 수 없고 작동이 중지됨][VPN Troubleshoot]을 참조하세요.

## <a name="testing-failover"></a>장애 조치(failover) 테스트

이제 VPN 연결(컨트롤 플레인)을 통한 성공적인 경로 교환을 확인했으므로 트래픽(데이터 평면)을 ExpressRoute 연결에서 VPN 연결로 전환하겠습니다. 

>[!NOTE] 
>프로덕션 환경에서 장애 조치(failover) 테스트는 서비스를 중단할 수 있으므로 예약된 네트워크 유지 관리 작업 기간에 수행해야 합니다.
>

트래픽 전환을 수행하기 전에 설정에서 온-프레미스 테스트 서버와 스포크 VNet의 테스트 VM 간 현재 경로를 추적해 봅시다.

```console
C:\Users\PathLabUser>tracert 10.17.11.132

Tracing route to 10.17.11.132 over a maximum of 30 hops

  1    <1 ms    <1 ms    <1 ms  10.1.11.1
  2    <1 ms    <1 ms    11 ms  192.168.11.0
  3    <1 ms    <1 ms    <1 ms  192.168.11.18
  4     *        *        *     Request timed out.
  5     6 ms     6 ms     5 ms  10.17.11.132

Trace complete.
```

설정의 기본 및 보조 ExpressRoute 지점 간 연결 서브넷은 각각 192.168.11.16/30 및 192.168.11.20/30입니다. 위 추적 경로의 3단계에서 기본 MSEE의 인터페이스 IP인 192.168.11.18을 적중하는 것을 확인할 수 있습니다. MSEE 인터페이스의 현재 상태를 통해 예상대로 현재 ExpressRoute를 통한 경로가 사용되고 있음을 확인할 수 있습니다.

[ExpressRoute 회로 피어링 다시 설정][RST]에서 보고된 것처럼 다음 PowerShell 명령을 사용하여 ExpressRoute 회로의 기본 및 보조 피어링을 둘 다 사용하지 않도록 설정해 봅시다.

```powershell
$ckt = Get-AzExpressRouteCircuit -Name "expressroute name" -ResourceGroupName "SEA-Cust11"
$ckt.Peerings[0].State = "Disabled"
Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```

장애 조치(failover) 전환 시간은 BGP 수렴 시간에 따라 달라집니다. 예제 설치에서는 장애 조치(failover)를 전환하는 데 몇 초(10 미만) 정도 걸립니다. 전환 후에 경로 추적을 반복하면 다음 경로가 표시됩니다.

```console
C:\Users\PathLabUser>tracert 10.17.11.132

Tracing route to 10.17.11.132 over a maximum of 30 hops

  1    <1 ms    <1 ms    <1 ms  10.1.11.1
  2     *        *        *     Request timed out.
  3     6 ms     7 ms     9 ms  10.17.11.132

Trace complete.
```

경로 추적 결과는 S2S VPN을 통한 백업 연결이 활성 상태이고 기본 및 보조 ExpressRoute 연결이 둘 다 실패할 경우 서비스 연속성을 제공할 수 있음을 확인합니다. 장애 조치(failover) 테스트를 완료하기 위해 다음 명령 집합을 통해 ExpressRoute 연결을 다시 사용하도록 설정하고 트래픽 흐름을 정규화해 봅시다.

```powershell
$ckt = Get-AzExpressRouteCircuit -Name "expressroute name" -ResourceGroupName "SEA-Cust11"
$ckt.Peerings[0].State = "Enabled"
Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```

트래픽이 ExpressRoute로 다시 전환되었는지 확인하려면 traceroute를 반복하여 ExpressRoute 프라이빗 피어링을 통과하는지 확인합니다.

## <a name="next-steps"></a>다음 단계

ExpressRoute는 Microsoft 네트워크 내에서 단일 실패 지점이 없는 고가용성을 위해 설계되었습니다. ExpressRoute 회로는 계속해서 단일 지리적 지역 및 서비스 공급자로만 한정됩니다. S2S VPN은 ExpressRoute 회로에 적합한 재해 복구 수동 백업 솔루션일 수 있습니다. 신뢰할 수 있는 수동 백업 연결 솔루션을 구현하려면 수동 구성의 정기적인 유지 관리와 연결의 주기적인 유효성 검사가 중요합니다. VPN 구성의 부실화를 방지하고 유지 관리 기간 동안 이 문서에 설명된 유효성 검사 및 장애 조치(failover) 테스트 단계를 주기적으로(예: 분기마다) 반복해야 합니다.

VPN 게이트웨이 메트릭에 따라 모니터링 및 경고를 사용하도록 설정하려면 [VPN 게이트웨이 메트릭에 대한 경고 설정][VPN-alerts]을 참조하세요.

ExpressRoute 오류 발생 후에 BGP 수렴을 신속하게 수행하려면 [ExpressRoute를 통해 BFD를 구성][BFD]합니다.

<!--Image References-->
[1]: ./media/use-s2s-vpn-as-backup-for-expressroute-privatepeering/topology.png "고려 중인 토폴로지"
[2]: ./media/use-s2s-vpn-as-backup-for-expressroute-privatepeering/vpn-gw-config.png "VPN 게이트웨이 구성"

<!--Link References-->
[DR-PP]: ./designing-for-disaster-recovery-with-expressroute-privatepeering.md
[Conf-CoExist]: ./expressroute-howto-coexist-resource-manager.md
[HA]: ./designing-for-high-availability-with-expressroute.md
[VPN Troubleshoot]: ../vpn-gateway/vpn-gateway-troubleshoot-site-to-site-cannot-connect.md
[VPN-alerts]: ../vpn-gateway/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric.md
[BFD]: ./expressroute-bfd.md
[RST]: ./expressroute-howto-reset-peering.md
