---
title: 회로 대역폭 업그레이드 정보| Azure ExpressRoute
description: 이 문서에서는 ExpressRoute 회로 대역폭을 업그레이드하기 위한 모범 사례를 알아봅니다.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: conceptual
ms.date: 06/08/2021
ms.author: duau
ms.openlocfilehash: 75570beb243fca802ecebebf34edf02b3d8ca9a5
ms.sourcegitcommit: 190658142b592db528c631a672fdde4692872fd8
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/11/2021
ms.locfileid: "112006494"
---
# <a name="about-upgrading-expressroute-circuit-bandwidth"></a>ExpressRoute 회로 대역폭 업그레이드 정보

ExpressRoute는 Microsoft의 글로벌 네트워크에 대한 전용 및 프라이빗 연결입니다. ExpressRoute 파트너의 네트워크 또는 MSEE(Microsoft Enterprise Edge) 디바이스에 대한 직접 연결을 통해 연결을 지원합니다. 물리적 연결을 구성하고 테스트한 후에는 ExpressRoute 회로를 만들고 피어링을 구성하여 레이어 2 및 레이어 3 연결을 사용하도록 설정할 수 있습니다.

## <a name="upgrade-circuit-bandwidth"></a><a name="upgrade"></a>회로 대역폭 업그레이드

회로 대역폭을 업그레이드하려면 ExpressRoute Direct 또는 ExpressRoute 파트너에 [충분한 대역폭을 사용](#considerations)해야 업그레이드에 성공합니다.

용량을 사용할 수 있는 경우 다음 방법을 사용하여 회로를 업그레이드할 수 있습니다.

* [Azure Portal](expressroute-howto-circuit-portal-resource-manager.md#modify)
* [PowerShell](expressroute-howto-circuit-arm.md#modify)
* [Azure CLI](howto-circuit-cli.md#modify)

## <a name="capacity-considerations"></a><a name="considerations"></a>용량 고려 사항

### <a name="insufficient-capacity-for-physical-connection"></a>물리적 연결을 위한 용량 부족

ExpressRoute 회로는 Microsoft와 ExpressRoute 파트너 간의 물리적 연결에 생성됩니다. 물리적 연결의 용량이 고정되어 있습니다. 회로 크기를 늘릴 수 없는 경우 기존 회로의 기본 물리적 연결에 업그레이드를 위한 용량이 없는 것입니다. 회로 크기를 변경하려면 새 회로를 만들어야 합니다.

새 ExpressRoute 회로를 성공적으로 만든 후에는 기존 가상 네트워크를 이 회로에 연결해야 합니다. 그런 다음, 이전 회로의 프로비전을 해제하기 전에 새 ExpressRoute 회로의 연결을 테스트하고 유효성을 검사할 수 있습니다. 다음은 가동 중지 시간과 프로덕션 작업 로도 중단을 최소화하기 위해 권장되는 마이그레이션 단계입니다.

### <a name="insufficient-expressroute-partner-bandwidth"></a><a name="bandwidth"></a>ExpressRoute 파트너 대역폭 부족

용량 오류로 인해 새 ExpressRoute 회로를 만들 수 없는 경우 이 ExpressRoute 파트너는 이 피어링 위치에서 Microsoft에 연결할 수 있는 용량이 없음을 의미합니다. 추가 용량을 요청하려면 ExpressRoute 파트너에게 문의하세요.

새 용량이 프로비전되면 [회로 대역폭 업그레이드](#upgrade) 섹션에 포함된 단계를 사용하여 새 회로를 만들고, 연결을 구성하고, 이전 회로를 삭제할 수 있습니다.


### <a name="insufficient-expressroute-direct-bandwidth"></a><a name="bandwidth"></a>ExpressRoute Direct 대역폭 부족

ExpressRoute Direct에 용량이 충분하지 않은 경우 두 가지 옵션을 사용할 수 있습니다. 더 이상 필요하지 않은 ExpressRoute Direct 리소스에 연결된 회로를 삭제하거나 새 ExpressRoute Direct 리소스를 만들 수 있습니다. ExpressRoute Direct 리소스 관리에 대한 지침은 [ExpressRoute Direct를 구성하는 방법](how-to-expressroute-direct-portal.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계

* [회로 만들기 및 수정](expressroute-howto-circuit-portal-resource-manager.md)
* [피어링 구성 만들기 및 수정](expressroute-howto-routing-portal-resource-manager.md)
* [가상 네트워크를 ExpressRoute 회로에 연결합니다.](expressroute-howto-linkvnet-portal-resource-manager.md)
