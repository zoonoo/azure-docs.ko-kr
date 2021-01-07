---
title: 'Azure Express 경로: 구성 BFD'
description: 이 문서는 ExpressRoute 회로의 프라이빗 피어링을 통해 BFD(Bidirectional Forwarding Detection)를 구성하는 방법에 대한 지침을 제공합니다.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: article
ms.date: 12/14/2020
ms.author: duau
ms.openlocfilehash: 254f5909e7ed8db4dc18ade2677a3213b268cf41
ms.sourcegitcommit: 63d0621404375d4ac64055f1df4177dfad3d6de6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/15/2020
ms.locfileid: "97511266"
---
# <a name="configure-bfd-over-expressroute"></a>ExpressRoute를 통해 BFD 구성

Express 경로는 개인 및 Microsoft 피어 링을 통해 BFD (양방향 전달 검색)를 지원 합니다. Express 경로를 통해 BFD를 사용 하도록 설정 하면 MSEE (Microsoft Enterprise edge) 장치와 Express 경로 회로가 구성 된 라우터 (CE/PE) 간에 링크 실패 검색 속도를 높일 수 있습니다. Edge 라우팅 장치 또는 파트너에 지 라우팅 장치 (관리 되는 계층 3 연결 서비스를 사용 하 여 전환한 경우)를 통해 Express 경로를 구성할 수 있습니다. 이 문서에서는 BFD의 필요성과 ExpressRoute를 통해 BFD를 활성화하는 방법을 안내합니다.

## <a name="need-for-bfd"></a>BFD의 필요성

다음 다이어그램에서는 ExpressRoute 회로를 통해 BFD를 활성화할 경우의 장점을 보여줍니다. [![1]][1]

Layer 2 연결 또는 관리형 Layer 3 연결을 사용하여 ExpressRoute 회로를 활성화할 수 있습니다. 두 경우 모두 Express 경로 연결 경로에 2 개 이상의 계층 2 장치가 있는 경우 경로에서 링크 실패를 검색 하는 책임은 overlying BGP 세션에 있습니다.

MSEE 장치에서 BGP 연결 유지 및 보류 시간은 일반적으로 각각 60 및 180 초로 구성 됩니다. 이러한 이유로 링크 오류가 발생 하는 경우 링크 오류를 검색 하 고 트래픽을 대체 연결로 전환 하는 데 최대 3 분 정도 걸릴 수 있습니다.

Edge 피어 링 장치에서 하위 BGP 연결 유지 및 대기 시간을 구성 하 여 BGP 타이머를 제어할 수 있습니다. 두 피어 링 장치 간에 BGP 타이머가 동일 하지 않으면 BGP 세션이 낮은 시간 값을 사용 하 여 설정 됩니다. BGP keep-alive는 최소 3 초 및 보류 시간 (10 초)으로 설정할 수 있습니다. 그러나 프로토콜은 프로세스를 많이 사용 하므로 매우 적극적인 BGP 타이머를 설정 하는 것은 좋지 않습니다.

이 시나리오에서는 BFD가 도움이 될 수 있습니다. BFD는 초 단위 미만의 시간 간격으로 오버헤드가 낮은 연결 오류 감지를 지원합니다. 


## <a name="enabling-bfd"></a>BFD 사용

BFD는 기본적으로 MSEE에서 새로 생성된 모든 ExpressRoute 프라이빗 피어링 인터페이스에 구성됩니다. 따라서 BFD를 사용 하도록 설정 하려면 기본 및 보조 장치 모두에서 BFD를 구성 하기만 하면 됩니다. BFD 구성은 2 단계 프로세스입니다. 인터페이스에서 BFD를 구성한 다음 BGP 세션에 연결 합니다.

예제 CE/PE (Cisco IOS XE 사용) 구성은 다음과 같습니다. 

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

BFD 피어 간의 전송 속도는 둘 중 더 느린 쪽을 따릅니다. MSEE BFD 전송/수신 간격 300밀리초로 설정됩니다. 특정 시나리오에서 간격은 750밀리초의 높은 값으로 설정될 수 있습니다. 더 큰 값을 구성 하면 이러한 간격이 더 길어질 수 있지만 더 짧게 만들 수는 없습니다.

>[!NOTE]
>지역 중복 Express 경로 회로를 구성 했거나 사이트 간 IPSec VPN 연결을 백업으로 사용 하는 경우 BFD를 사용 하도록 설정 하면 Express 경로 연결 오류로 인해 보다 빠르게 장애 조치를 수행할 수 있습니다. 
>

## <a name="next-steps"></a>다음 단계

자세한 정보 또는 도움말을 보려면 다음 링크를 확인하세요.

- [ExpressRoute 회로 만들기 및 수정][CreateCircuit]
- [ExpressRoute 회로의 라우팅 만들기 및 수정][CreatePeering]

<!--Image References-->
[1]: ./media/expressroute-bfd/BFD_Need.png "연결 오류 추론 시간을 가속화하는 BFD"

<!--Link References-->
[CreateCircuit]: ./expressroute-howto-circuit-portal-resource-manager.md
[CreatePeering]: ./expressroute-howto-routing-portal-resource-manager.md
[ResetPeering]: ./expressroute-howto-reset-peering.md