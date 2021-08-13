---
title: 'Azure ExpressRoute: Azure Portal을 사용하여 회로 피어링 초기화'
description: Azure Portal를 사용하여 Azure ExpressRoute 회로의 피어링을 사용하지 않도록 설정하거나 사용하도록 설정하는 방법을 알아봅니다.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 11/30/2020
ms.author: duau
ms.openlocfilehash: 432ecedbbb8965926499380eb1165fdf43018426
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97680286"
---
# <a name="reset-expressroute-circuit-peerings-by-using-the-azure-portal"></a>Azure Portal를 사용하여 ExpressRoute 회로 피어링 초기화

이 문서에서는 Azure Portal를 사용하여 Azure ExpressRoute 회로의 피어링을 사용하지 않도록 설정하거나 사용하도록 설정하는 방법을 설명합니다. 피어링을 사용하지 않도록 설정하는 경우, ExpressRoute 회로의 기본 연결 및 보조 연결 둘 다에서 Border Gateway Protocol(BGP) 세션이 종료됩니다. 피어링을 사용하도록 설정하는 경우 ExpressRoute 회로의 기본 연결 및 보조 연결 둘 다에서 BGP 세션이 복구됩니다.

> [!Note]
> ExpressRoute 회로에서 피어링을 처음 구성할 때, 피어링이 기본적으로 사용되도록 설정됩니다.

다음 시나리오에서는 ExpressRoute 피어링을 초기화하는 것이 유용할 수 있습니다.

* 재해 복구 디자인 및 구현을 테스트합니다. 예를 들어 두 개의 ExpressRoute 회로가 있다고 가정합니다. 한 회로의 피어링은 사용하지 않도록 설정하고 네트워크 트래픽이 다른 회로를 사용하도록 할 수 있습니다.

* Azure 개인 피어링 또는 Microsoft 피어링에서 양방향 전달 탐색(BFD)을 사용하도록 설정하려고 합니다. ExpressRoute 회로가 Azure 개인 피어링에서 2020년 1월 10일 이전 또는 Microsoft 피어링에서 2018년 8월 1일 이전에 생성된 경우, BFD가 기본적으로 사용하도록 설정되지 않았습니다. BFD를 사용하도록 피어링을 초기화합니다.

## <a name="sign-in-to-the-azure-portal"></a>Azure Portal에 로그인

브라우저에서 [Azure Portal](https://portal.azure.com)로 이동하고, Azure 계정으로 로그인합니다.

## <a name="reset-a-peering"></a>피어링 다시 설정

ExpressRoute 회로에서 Microsoft 피어링 및 Azure 개인 피어링을 독립적으로 초기화할 수 있습니다.

1. 변경할 회로를 선택합니다.

    :::image type="content" source="./media/expressroute-howto-reset-peering-portal/expressroute-circuit-list.png" alt-text="ExpressRoute 회로 목록에서 회로를 선택하는 것을 보여주는 스크린샷.":::

1. 초기화하려는 피어링 구성을 선택합니다.

    :::image type="content" source="./media/expressroute-howto-reset-peering-portal/expressroute-circuit.png" alt-text="ExpressRoute 회로 개요에서 피어링을 선택하는 것을 보여주는 스크린샷.":::

1. **피어링 사용** 확인란의 선택을 취소하고 **저장** 을 선택하여 피어링 구성을 사용하지 않도록 설정합니다.

    :::image type="content" source="./media/expressroute-howto-reset-peering-portal/disable-peering.png" alt-text="피어링 사용 확인란의 선택을 취소하는 스크린샷.":::

1. **피어링 사용** 확인란을 선택한 다음 **저장** 을 선택하여 피어링 구성을 다시 사용하도록 설정합니다.

## <a name="next-steps"></a>다음 단계

ExpressRoute 문제를 해결하려면 다음 문서를 참조하세요.

* [ExpressRoute 연결 확인](expressroute-troubleshooting-expressroute-overview.md)
* [네트워크 성능 문제 해결](expressroute-troubleshooting-network-performance.md)
