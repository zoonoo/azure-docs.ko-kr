---
title: '시나리오: VNet 격리'
titleSuffix: Azure Virtual WAN
description: 라우팅 시나리오 - VNet 격리
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 09/22/2020
ms.author: cherylmc
ms.custom: fasttrack-edit
ms.openlocfilehash: e5e2ce17be6d8a1fa82d8a92b9b788f0bd2a37b8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92424750"
---
# <a name="scenario-isolating-vnets"></a>시나리오: VNet 격리

Virtual WAN 가상 허브 라우팅 작업 시 몇 가지 시나리오를 사용할 수 있습니다. 이 시나리오에서는 VNet이 다른 VNet에 연결할 수 없도록 하는 것입니다. 이를 VNet 격리라고 합니다. 가상 허브 라우팅에 대한 자세한 내용은 [가상 허브 라우팅 정보](about-virtual-hub-routing.md)를 참조하세요.

## <a name="design"></a><a name="design"></a>디자인

이 시나리오에서는 특정 VNet 내의 워크로드가 격리된 상태로 남아 있기 때문에 다른 VNet과 통신할 수 없습니다. 그러나 VNet은 모든 분기(VPN, ER 및 사용자 VPN)에 연결하는 데 필요합니다. 필요한 경로 테이블 수를 파악하기 위해 연결 행렬을 빌드할 수 있습니다. 이 시나리오에서 연결 행렬은 다음 표와 같은데, 여기서 각 셀은 원본(행)이 대상(열)과 통신할 수 있는지 여부를 나타냅니다.

| 시작 |   대상 |  *VNet* | *분기* |
| -------------- | -------- | ---------- | ---|
| VNet     | &#8594;| 직접 |   직접    |
| 분기   | &#8594;|  직접  |   직접    |

위의 표에 나와 있는 각 셀은 Virtual WAN 연결(흐름의 ‘시작’ 쪽, 즉 행 머리글)이 대상 접두사(흐름의 ‘대상’ 쪽, 즉 이탤릭체 열 머리글)와 통신하는지 여부를 설명합니다. 이 시나리오에서는 방화벽이나 네트워크 가상 어플라이언스를 사용할 수 없으므로 Virtual WAN에서 직접 통신 흐름이 전달됩니다(따라서 표에 ‘직접’으로 표시됨).

이 연결 행렬은 서로 다른 행 패턴 2개를 제공하며 경로 테이블 2개로 변환됩니다. 가상 WAN에는 이미 Default 경로 테이블이 있으므로 다른 경로 테이블이 필요합니다. 이 예에서는 경로 테이블의 이름을 **RT_VNET** 로 지정합니다.

VNet은 **RT_VNET** 경로 테이블에 연결됩니다. VNet에는 분기에 대한 연결이 필요하기 때문에 분기가 **RT_VNET** 으로 전파되어야 합니다. 그러지 않으면 VNet이 분기 접두사를 인식하지 못합니다. 분기는 항상 Default 경로 테이블에 연결되므로 VNet은 Default 경로 테이블에 전파되어야 합니다. 따라서 최종 디자인은 다음과 같습니다.

* 가상 네트워크:
  * 연결된 경로 테이블: **RT_VNET**
  * 경로 테이블로 전파: **Default**
* 분기:
  * 연결된 경로 테이블: **Default**
  * 경로 테이블로 전파: **RT_VNET** 및 **Default**

분기만 경로 테이블 **RT_VNET** 에 전파되므로 해당 분기는 다른 VNet의 접두사가 아니라 VNet이 인식하는 유일한 접두사입니다.

가상 허브 라우팅에 대한 자세한 내용은 [가상 허브 라우팅 정보](about-virtual-hub-routing.md)를 참조하세요.

## <a name="workflow"></a><a name="workflow"></a>워크플로

이 시나리오를 구성하려면 다음 단계를 고려해야 합니다.

1. 각 허브에서 사용자 지정 경로 테이블을 만듭니다. 이 예에서는 경로 테이블이 **RT_VNet** 입니다. 경로 테이블을 만들려면 [가상 허브 라우팅을 구성하는 방법](how-to-virtual-hub-routing.md)을 참조하세요. 경로 테이블에 대한 자세한 내용은 [가상 허브 라우팅 정보](about-virtual-hub-routing.md)를 참조하세요.
2. **RT_VNet** 경로 테이블을 만들 때 다음 설정을 구성합니다.

   * **연결**: 격리할 VNet을 선택합니다.
   * **전파**: P2S 분기(VPN/ER/) 연결이 이 경로 테이블로 경로를 전파할 것을 암시하는 분기 옵션을 선택합니다.

:::image type="content" source="./media/routing-scenarios/isolated/isolated-vnets.png" alt-text="격리된 VNet":::

## <a name="next-steps"></a>다음 단계

* Virtual WAN에 대한 자세한 내용은 [FAQ](virtual-wan-faq.md)를 참조하세요.
* 가상 허브 라우팅에 대한 자세한 내용은 [가상 허브 라우팅 정보](about-virtual-hub-routing.md)를 참조하세요.
