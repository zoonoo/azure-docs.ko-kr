---
title: 'Azure Express 경로: Azure Portal을 사용 하 여 회로 피어 링 다시 설정'
description: Azure Portal를 사용 하 여 Azure Express 경로 회로의 피어 링을 사용 하지 않도록 설정 하 고 사용 하도록 설정 하는 방법을 알아봅니다.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 11/30/2020
ms.author: duau
ms.openlocfilehash: d4d6b0b0cce4f5304f7c5790ef2bda05633be52f
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/03/2020
ms.locfileid: "96582987"
---
# <a name="reset-expressroute-circuit-peerings-use-the-azure-portal"></a>Express 경로 회로 피어 링을 사용 하 여 Azure Portal

이 문서에서는 Azure Portal를 사용 하 여 Express 경로 회로의 피어 링을 사용 하지 않도록 설정 하 고 설정 하는 방법을 설명 합니다. 피어 링을 사용 하지 않도록 설정 하면 Express 경로 회로의 기본 연결과 보조 연결 모두에 대 한 BGP 세션이 종료 됩니다. 이 피어 링을 통해 Microsoft에 연결 되지 않습니다. 피어 링을 사용 하도록 설정 하면 Express 경로 회로의 기본 연결 및 보조 연결 모두에서 BGP 세션이 시작 됩니다. 이 피어 링을 통해 Microsoft에 다시 연결 합니다. ExpressRoute 회로에서 Microsoft 피어링 및 Azure 프라이빗 피어링을 별도로 사용 및 사용하지 않도록 설정할 수 있습니다. Express 경로 회로에서 처음으로 피어 링을 구성 하는 경우 피어 링은 기본적으로 사용 하도록 설정 됩니다.

ExpressRoute 피어링을 다시 설정하는 것이 도움이 되는 몇 가지 시나리오가 있습니다.
* 재해 복구 디자인 및 구현을 테스트합니다. 예를 들어 두 개의 ExpressRoute 회로가 있습니다. 한 회로의 피어링은 사용하지 않도록 설정하고 네트워크 트래픽을 다른 회로로 강제로 장애 조치(Failover)할 수 있습니다.
* Azure 개인 피어 링 또는 Express 경로 회로의 Microsoft 피어 링에서 BFD (양방향 전달 검색)를 사용 하도록 설정 합니다. Microsoft 피어 링에서 1 2018 년 8 월 및 Microsoft 피어 링 후에 Express 경로 회로가 생성 되는 경우 Azure 개인 피어 링에서 BFD를 기본적으로 사용할 수 있습니다. 10 2020 년 1 월 1 일 이후에 Express 경로 회로가 생성 되는 경우 그 이전에 회로를 만들었으면 BFD가 사용되도록 설정되지 않습니다. 피어링을 사용하지 않도록 설정했다가 다시 사용하도록 설정하여 BFD를 사용하도록 설정할 수 있습니다. 

### <a name="sign-in-to-the-azure-portal"></a>Azure Portal에 로그인

브라우저에서 [Azure 포털](https://portal.azure.com) 로 이동하고 Azure 계정으로 로그인합니다.

## <a name="reset-a-peering"></a>피어링 다시 설정

1. 피어 링 구성 변경에 사용할 회로를 선택 합니다.

    :::image type="content" source="./media/expressroute-howto-reset-peering-portal/expressroute-circuit-list.png" alt-text="Express 경로 회로 목록":::

1. 사용 하거나 사용 하지 않도록 설정할 피어 링 구성을 선택 합니다.

    :::image type="content" source="./media/expressroute-howto-reset-peering-portal/expressroute-circuit.png" alt-text="Express 경로 회로 개요":::

1. 피어 링 **사용** 을 선택 취소 하 고 **저장** 을 선택 하 여 피어 링 구성을 사용 하지 않습니다.

    :::image type="content" source="./media/expressroute-howto-reset-peering-portal/disable-peering.png" alt-text="개인 피어 링 사용 안 함":::

1. **피어 링 사용** 을 선택 하 고 **저장** 을 선택 하 여 피어 링을 다시 사용 하도록 설정할 수 있습니다.

## <a name="next-steps"></a>다음 단계
Express 경로 문제를 해결 하는 데 도움이 필요한 경우 다음 문서를 확인 하세요.
* [ExpressRoute 연결 확인](expressroute-troubleshooting-expressroute-overview.md)
* [네트워크 성능 문제 해결](expressroute-troubleshooting-network-performance.md)
