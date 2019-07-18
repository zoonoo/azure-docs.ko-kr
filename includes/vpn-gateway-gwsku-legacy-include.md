---
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 11/09/2018
ms.author: cherylmc
ms.openlocfilehash: f2ee442d0d6fecf34449ad28f058615a1274bbea
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67182091"
---
레거시(이전) VPN 게이트웨이 SKU는 다음과 같습니다.

* Basic
* Standard
* HighPerformance

VPN Gateway는 UltraPerformance 게이트웨이 SKU를 사용하지 않습니다. UltraPerformance SKU에 대한 내용은 [ExpressRoute](../articles/expressroute/expressroute-about-virtual-network-gateways.md) 설명서를 참조하세요.

이전 SKU를 사용할 경우 다음 사항을 고려합니다.

* 정책 기반 VPN 유형을 사용하려는 경우 기본 SKU를 사용해야 합니다. 정책 기반 VPN(이전에는 정적 라우팅이라고 함)은 다른 SKU에서 지원되지 않습니다.
* BGP는 기본 SKU에 지원되지 않습니다.
* ExpressRoute-VPN Gateway 공존 구성은 기본 SKU에서 지원되지 않습니다.
* 활성-활성 S2S VPN Gateway 연결은 HighPerformance SKU에서만 구성할 수 있습니다.