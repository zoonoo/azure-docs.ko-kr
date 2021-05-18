---
title: 'Azure ExpressRoute: 연결 모델'
description: 고객 네트워크, Microsoft Azure, Microsoft 365 서비스 간 연결을 검토합니다. 고객은 MPLS 공급자, 클라우드 Exchange, 이더넷을 사용할 수 있습니다.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: conceptual
ms.date: 10/07/2020
ms.author: duau
ms.openlocfilehash: fb35a03b5dd8780445eb27f485966e3c3452feea
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "91978760"
---
# <a name="expressroute-connectivity-models"></a>ExpressRoute 연결 모델
온-프레미스 네트워크와 Microsoft 클라우드 간 연결은 [CloudExchange 공동 배치](#CloudExchange), [지점 간 이더넷 연결](#Ethernet), [Any-to-Any(IPVPN) 연결](#IPVPN), [ExpressRoute Direct](#Direct)라는 네 가지 방법으로 만들 수 있습니다. 연결 공급자가 하나 이상의 연결 모델을 제공할 수도 있습니다. 연결 공급자로 작업하여 사용자에게 적합한 다양한 모델을 선택할 수 있습니다.
<br><br>

:::image type="content" source="./media/expressroute-connectivity-models/expressroute-connectivity-models-diagram.png" alt-text="ExpressRoute 연결 모델 다이어그램":::

## <a name="co-located-at-a-cloud-exchange"></a><a name="CloudExchange"></a>클라우드 Exchange에 배치합니다.
클라우드 교환이 있는 시설에 공동 배치된 경우 공동 배치 공급자의 이더넷 교환을 통해 Microsoft 클라우드에 대한 가상 교차 연결을 주문할 수 있습니다. 공동 배치 공급자는 공동 시설의 인프라와 Microsoft 클라우드 간에 2계층 간 연결 또는 관리된 3계층 간 연결을 제공할 수 있습니다.

## <a name="point-to-point-ethernet-connections"></a><a name="Ethernet"></a>지점 간 이더넷 연결
지점 간 이더넷 연결을 통해 온-프레미스 데이터 센터/사무소를 Microsoft 클라우드에 연결할 수 있습니다. 지점 간 이더넷 공급자는 사이트와 Microsoft 클라우드 간에 2계층 연결 또는 관리된 3계층 연결을 제공합니다.

## <a name="any-to-any-ipvpn-networks"></a><a name="IPVPN"></a>임의(IPVPN)의 네트워크
Microsoft 클라우드로 WAN을 통합할 수 있습니다. IPVPN 공급자(일반적으로 MPLS VPN)는 지사 및 데이터 센터 간에 임의의 연결을 제공합니다. Microsoft 클라우드는 다른 지사와 마찬가지로 보이도록 WAN에 상호 연결될 수 있습니다. WAN 공급자는 일반적으로 관리된 3계층 연결을 제공합니다. ExpressRoute 기능 및 특징은 위의 모든 연결 모델에서 모두 동일합니다.

## <a name="direct-from-expressroute-sites"></a><a name="Direct"></a>ExpressRoute 사이트에서 직접
전 세계에 전략적으로 분산된 피어링 위치에서 Microsoft 글로벌 네트워크에 직접 연결할 수 있습니다. [ExpressRoute Direct](expressroute-erdirect-about.md)는 대규모 활성/활성 연결을 지원하는 이중 100Gbps 또는 10Gbps 연결을 제공합니다.

## <a name="next-steps"></a>다음 단계
* ExpressRoute 연결 및 라우팅 도메인에 대해 알아봅니다. [ExpressRoute 회로 및 라우팅 도메인](expressroute-circuit-peerings.md)을 참조하세요.
* ExpressRoute 기능에 대해 알아봅니다. [ExpressRoute 기술 개요](expressroute-introduction.md) 참조
* 서비스 공급자를 찾습니다. [ExpressRoute 파트너 및 피어링 위치](expressroute-locations.md)를 확인하세요.
* 모든 필수 조건이 충족되었는지 확인합니다. [ExpressRoute 필수 조건](expressroute-prerequisites.md)을 참조하세요.
* [라우팅](expressroute-routing.md), [NAT](expressroute-nat.md) 및 [QoS](expressroute-qos.md)에 대한 요구 사항을 참조하세요.
* ExpressRoute 연결을 구성합니다.
  * [ExpressRoute 회로 만들기](expressroute-howto-circuit-portal-resource-manager.md)
  * [라우팅 구성](expressroute-howto-routing-portal-resource-manager.md)
  * [VNet을 ExpressRoute 회로에 연결](expressroute-howto-linkvnet-portal-resource-manager.md)