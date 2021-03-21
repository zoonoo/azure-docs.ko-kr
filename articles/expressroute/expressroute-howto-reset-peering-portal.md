---
title: 'Azure Express 경로: Azure Portal을 사용 하 여 회로 피어 링 다시 설정'
description: Azure Portal를 사용 하 여 Azure Express 경로 회로의 피어 링을 사용 하지 않도록 설정 하 고 사용 하도록 설정 하는 방법을 알아봅니다.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 11/30/2020
ms.author: duau
ms.openlocfilehash: 432ecedbbb8965926499380eb1165fdf43018426
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "97680286"
---
# <a name="reset-expressroute-circuit-peerings-by-using-the-azure-portal"></a>Azure Portal를 사용 하 여 Express 경로 회로 피어 링 다시 설정

이 문서에서는 Azure Portal를 사용 하 여 Azure Express 경로 회로의 피어 링을 사용 하지 않도록 설정 하 고 설정 하는 방법을 설명 합니다. 피어 링을 사용 하지 않도록 설정 하면 Express 경로 회로의 기본 연결과 보조 연결 모두에 대 한 BGP (Border Gateway Protocol) 세션이 종료 됩니다. 피어 링을 사용 하도록 설정 하면 Express 경로 회로의 기본 연결 및 보조 연결 모두에서 BGP 세션이 복원 됩니다.

> [!Note]
> Express 경로 회로에서 처음으로 피어 링을 구성 하는 경우 피어 링은 기본적으로 사용 하도록 설정 됩니다.

다음 시나리오에서는 Express 경로 피어 링을 다시 설정 하는 것이 유용할 수 있습니다.

* 재해 복구 디자인 및 구현을 테스트 하 고 있습니다. 예를 들어 Express 경로 회로 두 개가 있다고 가정 합니다. 한 회로의 피어 링을 사용 하지 않도록 설정 하 고 네트워크 트래픽이 다른 회로를 사용 하도록 강제할 수 있습니다.

* Azure 개인 피어 링 또는 Microsoft 피어 링에서 BFD (양방향 전달 검색)를 사용 하도록 설정 하려고 합니다. Azure 개인 피어 링 또는 2020 년 1 월 10 일 이전에는 Express 경로 회로를 2018 년 8 월 1 일 이전에 만든 경우 Microsoft 피어 링에서 BFD는 기본적으로 사용 하도록 설정 되지 않았습니다. BFD를 사용 하도록 피어 링을 다시 설정 합니다.

## <a name="sign-in-to-the-azure-portal"></a>Azure Portal에 로그인

브라우저에서 [Azure Portal](https://portal.azure.com)으로 이동한 다음 Azure 계정으로 로그인 합니다.

## <a name="reset-a-peering"></a>피어링 다시 설정

Express 경로 회로에서 Microsoft 피어 링 및 Azure 개인 피어 링을 독립적으로 다시 설정할 수 있습니다.

1. 변경 하려는 회로를 선택 합니다.

    :::image type="content" source="./media/expressroute-howto-reset-peering-portal/expressroute-circuit-list.png" alt-text="Express 경로 회로 목록에서 회로를 선택 하는 것을 보여 주는 스크린샷":::

1. 다시 설정 하려는 피어 링 구성을 선택 합니다.

    :::image type="content" source="./media/expressroute-howto-reset-peering-portal/expressroute-circuit.png" alt-text="Express 경로 회로 개요에서 피어 링을 선택 하는 것을 보여 주는 스크린샷":::

1. **피어 링 사용** 확인란의 선택을 취소 하 고 **저장** 을 선택 하 여 피어 링 구성을 사용 하지 않도록 설정 합니다.

    :::image type="content" source="./media/expressroute-howto-reset-peering-portal/disable-peering.png" alt-text="피어 링 사용 확인란의 선택을 취소 하는 스크린샷":::

1. **피어 링 사용** 확인란을 선택한 다음 **저장** 을 선택 하 여 피어 링 구성을 다시 사용 하도록 설정 합니다.

## <a name="next-steps"></a>다음 단계

Express 경로 문제를 해결 하려면 다음 문서를 참조 하세요.

* [ExpressRoute 연결 확인](expressroute-troubleshooting-expressroute-overview.md)
* [네트워크 성능 문제 해결](expressroute-troubleshooting-network-performance.md)
