---
title: '시나리오: Vnet 격리'
titleSuffix: Azure Virtual WAN
description: 라우팅 시나리오-Vnet 격리
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 06/29/2020
ms.author: cherylmc
ms.openlocfilehash: f43f17a0f3742831920836e448de3ef757f2dfa6
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85568083"
---
# <a name="scenario-isolating-vnets"></a>시나리오: Vnet 격리

가상 WAN 가상 허브 라우팅으로 작업할 때 몇 가지 시나리오를 사용할 수 있습니다. 이 시나리오에서 목표는 Vnet 다른에 도달할 수 없도록 하는 것입니다. 이를 Vnet 격리 라고 합니다. VNet 내의 워크 로드는 격리 된 상태로 유지 되며 임의의 시나리오와 마찬가지로 다른 Vnet와 통신할 수 없습니다. 그러나 Vnet는 모든 분기 (VPN, ER 및 사용자 VPN)에 도달 하는 데 필요 합니다. 이 시나리오에서 모든 VPN, Express 경로 및 사용자 VPN 연결은 동일 하 고 하나의 경로 테이블에 연결 됩니다. 모든 VPN, Express 경로 및 사용자 VPN 연결은 경로를 동일한 경로 테이블 집합으로 전파 합니다. 가상 허브 라우팅에 대 한 자세한 내용은 [가상 허브 라우팅 정보](about-virtual-hub-routing.md)를 참조 하세요.

## <a name="scenario-workflow"></a><a name="workflow"></a>시나리오 워크플로

이 시나리오를 구성 하려면 다음 단계를 고려해 야 합니다.

1. 사용자 지정 경로 테이블을 만듭니다. 이 예에서는 경로 테이블을 **RT_VNet**합니다. 경로 테이블을 만들려면 [가상 허브 라우팅을 구성 하는 방법](how-to-virtual-hub-routing.md)을 참조 하세요. 경로 테이블에 대 한 자세한 내용은 [가상 허브 라우팅 정보](about-virtual-hub-routing.md)를 참조 하세요.
2. **RT_VNet** 경로 테이블을 만들 때 다음 설정을 구성 합니다.

   * **연결**: 격리할 vnet를 선택 합니다.
   * **전파**: 분기에 대 한 옵션을 선택 합니다 .이 경로 테이블에 경로를 전파 하는 P2S 분기 (VPN/ER/) 연결입니다.

:::image type="content" source="./media/routing-scenarios/isolated/isolated-vnets.png" alt-text="격리 된 Vnet":::

## <a name="next-steps"></a>다음 단계

* Virtual WAN에 대한 자세한 내용은 [FAQ](virtual-wan-faq.md)를 참조하세요.
* 가상 허브 라우팅에 대 한 자세한 내용은 [가상 허브 라우팅 정보](about-virtual-hub-routing.md)를 참조 하세요.
