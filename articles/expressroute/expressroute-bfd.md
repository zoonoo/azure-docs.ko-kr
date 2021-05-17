---
title: 'Azure ExpressRoute: BFD 구성'
description: 이 문서는 ExpressRoute 회로의 프라이빗 피어링을 통해 BFD(Bidirectional Forwarding Detection)를 구성하는 방법에 대한 지침을 제공합니다.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: article
ms.date: 12/14/2020
ms.author: duau
ms.openlocfilehash: 1ad91b00c14744ab1f0cbb414defeaffb85559fe
ms.sourcegitcommit: 4a54c268400b4158b78bb1d37235b79409cb5816
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2021
ms.locfileid: "108127799"
---
# <a name="configure-bfd-over-expressroute"></a>ExpressRoute를 통해 BFD 구성

ExpressRoute는 프라이빗 피어링 및 Microsoft 피어링을 통해 BFD(양방향 전달 검색)를 지원합니다. ExpressRoute를 통해 BFD를 사용하도록 설정하면 MSEE(Microsoft Enterprise Edge) 디바이스와 ExpressRoute 회로가 구성된 라우터(CE/PE) 간의 링크 오류 검색 속도를 높일 수 있습니다. Edge 라우팅 디바이스 또는 Partner Edge 라우팅 디바이스 또는 Partner Edge 라우팅 디바이스를 통해 ExpressRoute를 구성할 수 있습니다(관리형 Layer 3 연결 서비스에 동의한 경우). 이 문서에서는 BFD의 필요성과 ExpressRoute를 통해 BFD를 활성화하는 방법을 안내합니다.

## <a name="need-for-bfd"></a>BFD의 필요성

다음 다이어그램에서는 ExpressRoute 회로를 통해 BFD를 활성화할 경우의 장점을 보여줍니다. [![1]][1]

Layer 2 연결 또는 관리형 Layer 3 연결을 사용하여 ExpressRoute 회로를 활성화할 수 있습니다. 두 경우 모두, ExpressRoute 연결 경로에 둘 이상의 Layer-2 디바이스가 있는 경우 경로의 연결 오류를 감지하는 역할은 상위 BGP 세션에서 수행합니다.

MSEE 디바이스에서 BGP 연결 유지 및 대기 시간은 일반적으로 각각 60초와 180초로 구성됩니다. 따라서 링크 오류가 발생하면 링크 오류를 감지하고 트래픽을 대체 연결로 전환하는 데 최대 3분이 걸릴 수 있습니다.

Edge 피어링 디바이스에서 하위 BGP 연결 유지 및 대기 시간을 구성하여 BGP 타이머를 제어할 수 있습니다. 두 피어링 디바이스 간에 BGP 타이머가 동일하지 않으면 BGP 세션이 낮은 시간 값을 사용하여 설정됩니다. BGP 연결 유지는 최소 3초 및 대기 시간 10초로 설정할 수 있습니다. 그러나 프로토콜은 프로세스를 많이 사용하므로 매우 적극적인 BGP 타이머를 설정하는 것은 좋지 않습니다.

이 시나리오에서는 BFD가 도움이 될 수 있습니다. BFD는 초 단위 미만의 시간 간격으로 오버헤드가 낮은 연결 오류 감지를 지원합니다. 


## <a name="enabling-bfd"></a>BFD 사용

BFD는 기본적으로 MSEE에서 새로 생성된 모든 ExpressRoute 프라이빗 피어링 인터페이스에 구성됩니다. 따라서 BFD를 사용하도록 설정하려면 기본 및 보조 디바이스 모두에서 BFD를 구성하기만 하면 됩니다. BFD 구성은 2단계 프로세스입니다. 인터페이스에서 BFD를 구성한 다음 BGP 세션에 연결합니다.

CE/PE(Cisco IOS XE 사용) 구성 예는 아래에 나와 있습니다. 

```console
interface TenGigabitEthernet2/0/0.150
   description private peering to Azure
   encapsulation dot1Q 15 second-dot1q 150
   ip vrf forwarding 15
   ip address 192.168.15.17 255.255.255.252
   bfd interval 300 min_rx 300 multiplier 3


router bgp 65020
   address-family ipv4 vrf 15
      network 10.1.15.0 mask 255.255.255.128
      neighbor 192.168.15.18 remote-as 12076
      neighbor 192.168.15.18 fall-over bfd
      neighbor 192.168.15.18 activate
      neighbor 192.168.15.18 soft-reconfiguration inbound
   exit-address-family
```

>[!NOTE]
>기존의 프라이빗 피어링에 BFD를 활성화하려면 피어링을 다시 설정해야 합니다. [ExpressRoute 피어링 다시 설정][ResetPeering] 참조
>

## <a name="bfd-timer-negotiation"></a>BFD 타이머 협상

BFD 피어 간의 전송 속도는 둘 중 더 느린 쪽을 따릅니다. MSEE BFD 전송/수신 간격 300밀리초로 설정됩니다. 특정 시나리오에서 간격은 750밀리초의 높은 값으로 설정될 수 있습니다. 더 큰 값을 구성하면 이러한 간격이 더 길어질 수 있지만 더 짧게 만들 수는 없습니다.

>[!NOTE]
>지리적 중복 ExpressRoute 회로를 구성했거나 사이트 간 IPSec VPN 연결을 백업으로 사용하는 경우 BFD를 사용하면 ExpressRoute 연결 실패 후 더 빠르게 장애 조치(failover)를 수행할 수 있습니다. 
>

## <a name="next-steps"></a>다음 단계

자세한 정보 또는 도움말을 보려면 다음 링크를 확인하세요.

- [ExpressRoute 회로 만들기 및 수정][CreateCircuit]
- [ExpressRoute 회로의 라우팅 만들기 및 수정][CreatePeering]

<!--Image References-->
[1]: ./media/expressroute-bfd/bfd-need.png "연결 오류 추론 시간을 가속화하는 BFD"

<!--Link References-->
[CreateCircuit]: ./expressroute-howto-circuit-portal-resource-manager.md
[CreatePeering]: ./expressroute-howto-routing-portal-resource-manager.md
[ResetPeering]: ./expressroute-howto-reset-peering.md