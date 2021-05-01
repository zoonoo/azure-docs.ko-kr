---
title: 회로 대역폭 업그레이드 정보| Azure ExpressRoute
description: 이 문서에서는 ExpressRoute 회로 대역폭을 업그레이드하기 위한 모범 사례를 알아봅니다.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: conceptual
ms.date: 07/07/2020
ms.author: duau
ms.openlocfilehash: 7831e7944321e074c312853e1534c47970ebdfdb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "89397953"
---
# <a name="about-upgrading-expressroute-circuit-bandwidth"></a>ExpressRoute 회로 대역폭 업그레이드 정보

ExpressRoute를 사용하면 Microsoft의 전체 네트워크에 전용 및 개인 연결을 사용할 수 있습니다. ExpressRoute 파트너의 네트워크 또는 MSEE(Microsoft Enterprise Edge) 디바이스에 대한 직접 연결을 통해 연결을 지원합니다. 물리적 연결을 구성하고 테스트한 후에는 ExpressRoute 회로를 만들고 피어링을 구성하여 레이어 2 및 레이어 3 연결을 사용하도록 설정할 수 있습니다.

## <a name="upgrade-circuit-bandwidth"></a><a name="upgrade"></a>회로 대역폭 업그레이드

회로 대역폭을 업그레이드하려면 ExpressRoute Direct 또는 ExpressRoute 파트너에 [충분한 대역폭을 사용](#considerations)해야 업그레이드에 성공합니다.

용량을 사용할 수 있는 경우 다음 방법을 사용하여 회로를 업그레이드할 수 있습니다.

* [Azure Portal](expressroute-howto-circuit-portal-resource-manager.md#modify)
* [PowerShell](expressroute-howto-circuit-arm.md#modify)
* [Azure CLI](howto-circuit-cli.md#modify)

## <a name="capacity-considerations"></a><a name="considerations"></a>용량 고려 사항

### <a name="insufficient-expressroute-partner-bandwidth"></a><a name="bandwidth"></a>ExpressRoute 파트너 대역폭 부족

ExpressRoute 파트너에 충분한 용량이 없는 경우 원하는 대역폭으로 구성된 새 회로를 만들어야 합니다. 연결을 유지하기 위해 새로 생성된 회로가 프로비전되고 피어링이 구성되고(개인 피어링 관련) ExpressRoute 가상 네트워크 게이트웨이에 대한 연결 개체가 프로비전될 때까지 이전 회로를 삭제하지 마세요.

ExpressRoute 파트너에 사용 가능한 용량이 충분하지 않은 경우 원하는 피어링 위치에서 추가 용량을 요청해야 합니다. 새 용량이 프로비전되면 [회로 대역폭 업그레이드](#upgrade) 섹션의 문서에 포함된 단계를 사용하여 새 회로를 만들고, 연결을 구성하고, 이전 회로를 삭제할 수 있습니다.


### <a name="insufficient-expressroute-direct-bandwidth"></a><a name="bandwidth"></a>ExpressRoute Direct 대역폭 부족

ExpressRoute Direct에 충분한 용량이 없는 경우 더 이상 필요하지 않은 ExpressRoute Direct 리소스에 연결된 회로를 삭제하거나 새 ExpressRoute Direct 리소스를 만들 수 있습니다. ExpressRoute Direct 리소스를 관리하는 방법에 대한 지침은 [ExpressRoute Direct를 구성하는 방법](how-to-expressroute-direct-portal.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계

* [회로 만들기 및 수정](expressroute-howto-circuit-portal-resource-manager.md)
* [피어링 구성 만들기 및 수정](expressroute-howto-routing-portal-resource-manager.md)
* [가상 네트워크를 ExpressRoute 회로에 연결합니다.](expressroute-howto-linkvnet-portal-resource-manager.md)
