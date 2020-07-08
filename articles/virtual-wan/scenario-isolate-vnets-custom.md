---
title: '시나리오: Vnet에 대 한 사용자 지정 격리'
titleSuffix: Azure Virtual WAN
description: 라우팅 시나리오-선택한 Vnet에서 서로 연결할 수 없도록 방지 합니다.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 06/29/2020
ms.author: cherylmc
ms.openlocfilehash: 3719956df0dce62ee69d8e306ff2cad27197616d
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85568089"
---
# <a name="scenario-custom-isolation-for-vnets"></a>시나리오: Vnet에 대 한 사용자 지정 격리

가상 WAN 가상 허브 라우팅으로 작업할 때 몇 가지 시나리오를 사용할 수 있습니다. Vnet에 대 한 사용자 지정 격리 시나리오에서 목표는 특정 Vnet 집합이 다른 특정 Vnet 집합에 도달할 수 없도록 하는 것입니다. 그러나 Vnet는 모든 분기 (VPN/ER/User VPN)에 연결 하는 데 필요 합니다.

이 시나리오에서는 VPN, Express 경로 및 사용자 VPN 연결 (통칭 하 여 분기)이 동일한 경로 테이블 (기본 경로 테이블)에 연결 됩니다. 모든 VPN, Express 경로 및 사용자 VPN 연결은 경로를 동일한 경로 테이블 집합으로 전파 합니다. 가상 허브 라우팅에 대 한 자세한 내용은 [가상 허브 라우팅 정보](about-virtual-hub-routing.md)를 참조 하세요.

## <a name="scenario-workflow"></a><a name="architecture"></a>시나리오 워크플로

**그림 1**에는 파란색 및 빨강 VNet 연결이 있습니다.

* 파란색으로 연결 된 Vnet는 서로 연결 될 수 있을 뿐만 아니라 모든 분기 (VPN/ER/P2S) 연결에 도달할 수 있습니다.
* Red Vnet는 서로 연결 될 수 있을 뿐만 아니라 모든 분기 (VPN/ER/P2S) 연결에 도달할 수 있습니다.

라우팅을 설정 하는 경우 다음 단계를 고려 하십시오.

1. Azure Portal, **RT_BLUE** 및 **RT_RED**에서 두 개의 사용자 지정 경로 테이블을 만듭니다.
2. 경로 테이블 **RT_BLUE**의 경우에서 다음을 수행 합니다.
   * **연결**: 모든 파란색 vnet를 선택 합니다.
   * **전파**: 분기의 경우 분기에 대 한 옵션을 선택 합니다. (VPN/ER/P2S) 연결은 경로를이 경로 테이블에 전파 합니다.
3. RED Vnet 및 분기 (VPN/ER/P2S)에 대 한 **RT_RED** 경로 테이블에 대해 동일한 단계를 반복 합니다.

이렇게 하면 아래 그림에 표시 된 것 처럼 라우팅 구성이 변경 됩니다.

**그림 1**

:::image type="content" source="./media/routing-scenarios/custom-isolation/custom.png" alt-text="그림 1":::

## <a name="next-steps"></a>다음 단계

* Virtual WAN에 대한 자세한 내용은 [FAQ](virtual-wan-faq.md)를 참조하세요.
* 가상 허브 라우팅에 대 한 자세한 내용은 [가상 허브 라우팅 정보](about-virtual-hub-routing.md)를 참조 하세요.
