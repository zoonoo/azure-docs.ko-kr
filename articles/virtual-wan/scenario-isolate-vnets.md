---
title: '시나리오: Vnet 격리'
titleSuffix: Azure Virtual WAN
description: 라우팅 시나리오-Vnet 격리
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 08/03/2020
ms.author: cherylmc
ms.custom: fasttrack-edit
ms.openlocfilehash: b8a0a8093ab5f4d6c5e528bce592d5c029de30a7
ms.sourcegitcommit: 5a3b9f35d47355d026ee39d398c614ca4dae51c6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/02/2020
ms.locfileid: "89400044"
---
# <a name="scenario-isolating-vnets"></a>시나리오: Vnet 격리

가상 WAN 가상 허브 라우팅으로 작업할 때 몇 가지 시나리오를 사용할 수 있습니다. 이 시나리오에서 목표는 Vnet 다른에 도달할 수 없도록 하는 것입니다. 이를 Vnet 격리 라고 합니다. 가상 허브 라우팅에 대 한 자세한 내용은 [가상 허브 라우팅 정보](about-virtual-hub-routing.md)를 참조 하세요.

## <a name="design"></a><a name="design"></a>디자인

이 시나리오에서는 특정 VNet 내의 워크 로드가 격리 된 상태를 유지 하 고 다른 Vnet와 통신할 수 없습니다. 그러나 Vnet는 모든 분기 (VPN, ER 및 사용자 VPN)에 도달 하는 데 필요 합니다. 필요한 경로 테이블 수를 파악 하기 위해 연결 행렬을 작성할 수 있습니다. 이 시나리오에서 각 셀은 원본 (행)이 대상 (열)과 통신할 수 있는지 여부를 나타내는 다음 표와 같습니다.

| 시작 |   대상 |  *Vnet* | *분기* |
| -------------- | -------- | ---------- | ---|
| VNet     | &#8594;|           |     X    |
| 분기   | &#8594;|    X     |     X    |

위의 표에 나와 있는 각 셀은 특정 트래픽 흐름에 대 한 대상 접두사 (흐름의 "보낸 사람" 쪽, 행 머리글)가 대상 접두사 (흐름의 "대상" 쪽, 기울임꼴 열 머리글)를 학습 하는지 여부를 설명 합니다. 여기서 "X"는 가상 WAN에서 연결이 제공 됨을 의미 합니다.

이 연결 매트릭스는 두 개의 서로 다른 행 패턴을 제공 하며 두 개의 경로 테이블로 변환 됩니다. 가상 WAN에는 이미 기본 경로 테이블이 있으므로 다른 경로 테이블이 필요 합니다. 이 예에서는 경로 테이블의 이름을 **RT_VNET**로 합니다.

Vnet는이 **RT_VNET** 경로 테이블에 연결 됩니다. 분기에 대 한 연결이 필요 하기 때문에 분기는 **RT_VNET** 로 전파 되어야 합니다. 그렇지 않으면 분기 접두사가 vnet. 분기는 항상 기본 경로 테이블에 연결 되므로 Vnet는 기본 경로 테이블에 전파 해야 합니다. 결과적으로 최종 설계는 다음과 같습니다.

* 가상 네트워크:
  * 연결 된 경로 테이블: **RT_VNET**
  * 경로 테이블에 전파: **기본값**
* 분기
  * 연결 된 경로 테이블: **기본값**
  * 경로 테이블에 전파: **RT_VNET** 및 **기본값**

분기는 경로 테이블 **RT_VNET**에만 전파 되므로 vnet는 다른 vnet의 접두사가 아니라 유일한 접두사가 됩니다.

가상 허브 라우팅에 대 한 자세한 내용은 [가상 허브 라우팅 정보](about-virtual-hub-routing.md)를 참조 하세요.

## <a name="workflow"></a><a name="workflow"></a>워크플로

이 시나리오를 구성 하려면 다음 단계를 고려해 야 합니다.

1. 각 허브에서 사용자 지정 경로 테이블을 만듭니다. 이 예에서는 경로 테이블을 **RT_VNet**합니다. 경로 테이블을 만들려면 [가상 허브 라우팅을 구성 하는 방법](how-to-virtual-hub-routing.md)을 참조 하세요. 경로 테이블에 대 한 자세한 내용은 [가상 허브 라우팅 정보](about-virtual-hub-routing.md)를 참조 하세요.
2. **RT_VNet** 경로 테이블을 만들 때 다음 설정을 구성 합니다.

   * **연결**: 격리할 vnet를 선택 합니다.
   * **전파**: 분기에 대 한 옵션을 선택 합니다 .이 경로 테이블에 경로를 전파 하는 P2S 분기 (VPN/ER/) 연결입니다.

:::image type="content" source="./media/routing-scenarios/isolated/isolated-vnets.png" alt-text="격리 된 Vnet":::

## <a name="next-steps"></a>다음 단계

* Virtual WAN에 대한 자세한 내용은 [FAQ](virtual-wan-faq.md)를 참조하세요.
* 가상 허브 라우팅에 대 한 자세한 내용은 [가상 허브 라우팅 정보](about-virtual-hub-routing.md)를 참조 하세요.
