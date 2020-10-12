---
title: '시나리오: Vnet에 대 한 사용자 지정 격리'
titleSuffix: Azure Virtual WAN
description: 라우팅 시나리오-선택한 Vnet에서 서로 연결할 수 없도록 방지 합니다.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 09/22/2020
ms.author: cherylmc
ms.openlocfilehash: 12bc99d24472780f87a6b2a83befdbbf12944860
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91267723"
---
# <a name="scenario-custom-isolation-for-vnets"></a>시나리오: Vnet에 대 한 사용자 지정 격리

가상 WAN 가상 허브 라우팅으로 작업할 때 몇 가지 시나리오를 사용할 수 있습니다. Vnet에 대 한 사용자 지정 격리 시나리오에서 목표는 특정 Vnet 집합이 다른 특정 Vnet 집합에 도달할 수 없도록 하는 것입니다. 그러나 Vnet는 모든 분기 (VPN/ER/User VPN)에 연결 하는 데 필요 합니다. 가상 허브 라우팅에 대 한 자세한 내용은 [가상 허브 라우팅 정보](about-virtual-hub-routing.md)를 참조 하세요.

## <a name="design"></a><a name="design"></a>디자인

필요한 경로 테이블 수를 파악 하기 위해 연결 행렬을 작성할 수 있습니다. 이 시나리오에서 각 셀은 원본 (행)이 대상 (열)과 통신할 수 있는지 여부를 나타내는 다음과 같습니다.

| From | 아래와 같이 변경합니다.| *파란색 Vnet* | *레드 Vnet* | *분기*|
|---|---|---|---|---|
| **파란색 Vnet** |   &#8594;|      X        |               |       X      |
| **레드 Vnet**  |   &#8594;|              |       X       |       X      |
| **분기**   |   &#8594;|     X        |       X       |       X      |

위의 표에 나와 있는 각 셀은 가상 wan 연결 (흐름의 "From" 쪽, 테이블의 행 머리글)이 특정 트래픽 흐름에 대 한 대상 접두사를 학습 하는지 여부를 설명 합니다. 여기서 "X"는 가상 WAN에서 연결이 제공 됨을 의미 합니다.

다른 행 패턴의 수는이 시나리오에서 필요한 경로 테이블 수입니다. 이 경우 가상 네트워크에 대 한 **RT_BLUE** 및 **RT_RED** 를 호출 하는 세 개의 경로 경로 테이블과 분기에 대 한 **기본값이** 제공 됩니다. 분기는 항상 기본 라우팅 테이블에 연결 되어야 합니다.

분기는 Red 및 Blue Vnet에서 접두사를 파악 해야 하므로 모든 Vnet는 기본값 ( **RT_BLUE** 또는 **RT_RED**)으로 전파 되어야 합니다. Blue와 Red Vnet는 분기 접두사를 배워야 하므로 분기가 **RT_BLUE** 경로 테이블 모두에 전파 되 고 **RT_RED** . 결과적으로 최종 설계는 다음과 같습니다.

* 파란색 가상 네트워크:
  * 연결 된 경로 테이블: **RT_BLUE**
  * 경로 테이블에 전파: **RT_BLUE** 및 **기본값**
* 빨간색 가상 네트워크:
  * 연결 된 경로 테이블: **RT_RED**
  * 경로 테이블에 전파: **RT_RED** 및 **기본값**
* 분기
  * 연결 된 경로 테이블: **기본값**
  * 경로 테이블에 전파: **RT_BLUE**, **RT_RED** 및 **기본값**

> [!NOTE]
> 모든 분기가 기본 경로 테이블에 연결 되어 있어야 하 고 동일한 라우팅 테이블 집합으로 전파 되어야 하므로 모든 분기에 동일한 연결 프로필이 있습니다. 즉, Vnet에 대 한 빨강/파랑 개념은 분기에 적용 될 수 없습니다.

> [!NOTE]
> 가상 WAN을 여러 지역에 배포 하는 경우에는 **RT_BLUE** 를 만들고 모든 허브에 경로 테이블을 **RT_RED** 해야 하며 각 VNet 연결의 경로는 전파 레이블을 사용 하 여 모든 가상 허브의 경로 테이블에 전파 해야 합니다.

가상 허브 라우팅에 대 한 자세한 내용은 [가상 허브 라우팅 정보](about-virtual-hub-routing.md)를 참조 하세요.

## <a name="workflow"></a><a name="architecture"></a>워크플로

**그림 1**에는 파란색 및 빨강 VNet 연결이 있습니다.

* 파란색으로 연결 된 Vnet는 서로 연결 될 수 있을 뿐만 아니라 모든 분기 (VPN/ER/P2S) 연결에 도달할 수 있습니다.
* Red Vnet는 서로 연결 될 수 있을 뿐만 아니라 모든 분기 (VPN/ER/P2S) 연결에 도달할 수 있습니다.

라우팅을 설정 하는 경우 다음 단계를 고려 하십시오.

1. Azure Portal, **RT_BLUE** 및 **RT_RED**에서 두 개의 사용자 지정 경로 테이블을 만듭니다.
2. 경로 테이블 **RT_BLUE**의 경우 다음 설정에 대해:
   * **연결**: 모든 Blue vnet를 선택 합니다.
   * **전파**: 분기의 경우 분기에 대 한 옵션을 선택 합니다. (VPN/ER/P2S) 연결은 경로를이 경로 테이블에 전파 합니다.
3. Red Vnet 및 분기 (VPN/ER/P2S)에 대 한 **RT_RED** 경로 테이블에 대해 동일한 단계를 반복 합니다.

이렇게 하면 아래 그림에 표시 된 것 처럼 라우팅 구성이 변경 됩니다.

**그림 1**

:::image type="content" source="./media/routing-scenarios/custom-isolation/custom.png" alt-text="그림 1":::

## <a name="next-steps"></a>다음 단계

* Virtual WAN에 대한 자세한 내용은 [FAQ](virtual-wan-faq.md)를 참조하세요.
* 가상 허브 라우팅에 대 한 자세한 내용은 [가상 허브 라우팅 정보](about-virtual-hub-routing.md)를 참조 하세요.
