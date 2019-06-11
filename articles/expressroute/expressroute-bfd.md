---
title: ExpressRoute를 통해 BFD 구성 - Azure | Microsoft Docs
description: 이 문서는 ExpressRoute 회로의 프라이빗 피어링을 통해 BFD(Bidirectional Forwarding Detection)를 구성하는 방법에 대한 지침을 제공합니다.
services: expressroute
author: rambk
ms.service: expressroute
ms.topic: article
ms.date: 8/17/2018
ms.author: rambala
ms.custom: seodec18
ms.openlocfilehash: 14f65851e50ed25024524f6d988ba2b2f2b3aeba
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60367673"
---
# <a name="configure-bfd-over-expressroute"></a>ExpressRoute를 통해 BFD 구성

ExpressRoute는 프라이빗 피어링을 통해 BFD(양방향 전달 검색)를 지원합니다. ExpressRoute를 통해 BFD를 활성화하면 ExpressRoute 회로(PE)를 종단시킬 Microsoft Enterprise 에지(MSEE) 기기와 라우터 사이에 연결 오류 감지를 촉진할 수 있습니다. Customer Edge 라우팅 디바이스 또는 Partner Edge 라우팅 디바이스를 통해 ExpressRoute를 종료할 수 있습니다(관리형 Layer 3 연결 서비스에 동의한 경우). 이 문서에서는 BFD의 필요성과 ExpressRoute를 통해 BFD를 활성화하는 방법을 안내합니다.

## <a name="need-for-bfd"></a>BFD의 필요성

다음 다이어그램에서는 ExpressRoute 회로를 통해 BFD를 활성화할 경우의 장점을 보여줍니다. [![1]][1]

Layer 2 연결 또는 관리형 Layer 3 연결을 사용하여 ExpressRoute 회로를 활성화할 수 있습니다. 두 경우 모두, ExpressRoute 연결 경로에 하나 이상의 Layer-2 디바이스가 있는 경우 경로의 연결 오류를 감지하는 역할은 상위 BGP에서 수행합니다.

MSEE 디바이스에서 BGP keepalive 및 hold-time은 일반적으로 각각 60초와 180초로 구성됩니다. 따라서 연결 오류 후에 연결 오류를 감지하고 대체 연결로 트래픽을 전환하는 데 최대 3분이 걸립니다.

Customer Edge 피어링 디바이스에서 하위 BGP keepalive 및 hold-time을 구성하여 BGP 타이머를 제어할 수 있습니다. 두 피어링 디바이스 간에 BGP 타이머가 일치하지 않으면 피어 간의 BGP 세션에서 하위 타이머 값을 사용합니다. BGP는 최소 3초까지 설정할 수 있고, hold-time은 10초 단위로 설정할 수 있습니다. 그러나 BGP 타이머를 공격적으로 설정하는 것은 좋지 않습니다. 프로토콜이 프로세스를 많이 사용하기 때문입니다.

이 시나리오에서는 BFD가 도움이 될 수 있습니다. BFD는 초 단위 미만의 시간 간격으로 오버헤드가 낮은 연결 오류 감지를 지원합니다. 


## <a name="enabling-bfd"></a>BFD 사용

BFD는 기본적으로 MSEE에서 새로 생성된 모든 ExpressRoute 프라이빗 피어링 인터페이스에 구성됩니다. 따라서 BFD를 활성화하려면 PE에 BFD를 구성하기만 하면 됩니다. BFD를 구성하는 과정은 2단계 프로세스입니다. 인터페이스에 BFD를 구성한 다음, BGP 세션에 연결하기만 하면 됩니다.

PE(Cisco IOS XE 사용) 구성 예는 아래에 나와 있습니다. 

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

>[!NOTE]
>기존의 프라이빗 피어링에 BFD를 활성화하려면 피어링을 다시 설정해야 합니다. [ExpressRoute 피어링 다시 설정][ResetPeering] 참조
>

## <a name="bfd-timer-negotiation"></a>BFD 타이머 협상

BFD 피어 간의 전송 속도는 둘 중 더 느린 쪽을 따릅니다. MSEE BFD 전송/수신 간격 300밀리초로 설정됩니다. 특정 시나리오에서 간격은 750밀리초의 높은 값으로 설정될 수 있습니다. 더 높은 값을 구성하면 이러한 간격을 강제로 늘릴 수 있지만 간격을 단축할 수는 없습니다.

>[!NOTE]
>지역 중복 ExpressRoute 프라이빗 피어링 회로를 구성했거나 사이트 간 IPSec VPN 연결을 ExpressRoute 프라이빗 피어링의 백업으로 사용하는 경우 프라이빗 피어링을 통해 BFD를 활성화하면 ExpressRoute 연결 실패 후 장애 조치(failover)가 더 빨리 수행됩니다. 
>

## <a name="next-steps"></a>다음 단계

자세한 정보 또는 도움말을 보려면 다음 링크를 확인하세요.

- [ExpressRoute 회로 만들기 및 수정][CreateCircuit]
- [ExpressRoute 회로의 라우팅 만들기 및 수정][CreatePeering]

<!--Image References-->
[1]: ./media/expressroute-bfd/BFD_Need.png "연결 오류 추론 시간을 가속화하는 BFD"

<!--Link References-->
[CreateCircuit]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-circuit-portal-resource-manager 
[CreatePeering]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-routing-portal-resource-manager
[ResetPeering]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-reset-peering






