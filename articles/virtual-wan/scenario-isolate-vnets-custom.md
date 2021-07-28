---
title: '시나리오: VNet에 대한 사용자 지정 격리'
titleSuffix: Azure Virtual WAN
description: 특정 VNet 세트가 다른 특정 VNet 세트에 연결할 수 없도록 하는 Virtual WAN 라우팅 사용자 지정 격리 시나리오에 대해 알아봅니다. 그러나 VNet는 모든 분기에 연결하는 데 필요합니다.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 04/27/2021
ms.author: cherylmc
ms.openlocfilehash: 56a0fe561d026f1b01f27cf3015c31820e8110bb
ms.sourcegitcommit: 62e800ec1306c45e2d8310c40da5873f7945c657
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2021
ms.locfileid: "108162044"
---
# <a name="scenario-custom-isolation-for-vnets"></a>시나리오: VNet에 대한 사용자 지정 격리

Virtual WAN 가상 허브 라우팅 작업 시 사용할 수 있는 시나리오가 상당히 많습니다. VNet에 대한 사용자 지정 격리 시나리오에서 목표는 특정 VNet 세트가 다른 특정 VNet 세트에 연결할 수 없도록 방지하는 것입니다. 그러나 VNet은 모든 분기(VPN/ER/사용자 VPN)에 연결하는 데 필요합니다. 가상 허브 라우팅에 대한 자세한 내용은 [가상 허브 라우팅 정보](about-virtual-hub-routing.md)를 참조하세요.

## <a name="design"></a><a name="design"></a>디자인

필요한 경로 테이블 수를 파악하기 위해 연결 행렬을 빌드할 수 있습니다. 이 시나리오에서 연결 행렬은 다음과 같은데, 여기서 각 셀은 원본(행)이 대상(열)과 통신할 수 있는지 여부를 나타냅니다.

| 시작 | 아래와 같이 변경합니다.| *Blue VNet* | *Red VNet* | *분기*|
|---|---|---|---|---|
| **Blue VNet** |   &#8594;|   직접     |           |  직접 |
| **Red VNet**  |   &#8594;|              |   직접  |  직접 |
| **분기**   |   &#8594;|   직접     |   직접  |  직접 |

위의 표에 나와 있는 각 셀은 Virtual WAN 연결(흐름의 ‘시작’ 쪽, 즉 행 머리글)이 대상(흐름의 ‘대상’ 쪽, 즉 이탤릭체 열 머리글)과 통신하는지 여부를 설명합니다. 이 시나리오에서는 방화벽이나 네트워크 가상 어플라이언스를 사용할 수 없으므로 Virtual WAN에서 직접 통신 흐름이 전달됩니다(따라서 표에 ‘직접’으로 표시됨).

다른 행 패턴의 수는 이 시나리오에서 필요한 경로 테이블 수입니다. 이 경우에는 가상 네트워크를 위한 **RT_BLUE** 와 **RT_RED** 그리고 분기를 위한 **Default**, 이렇게 경로 테이블이 3개 있습니다. 분기는 항상 Default 경로 테이블에 연결되어야 합니다.

분기는 Red와 Blue VNet 둘 다의 접두사를 인식해야 합니다. 그래야 모든 VNet이 Default(또는 **RT_BLUE** 나 **RT_RED**)로 전파됩니다. Blue와 Red VNet은 분기 접두사를 인식해야 합니다. 그래야 분기가 **RT_BLUE** 와 **RT_RED** 경로 테이블 둘 다에도 전파됩니다. 따라서 최종 디자인은 다음과 같습니다.

* Blue 가상 네트워크:
  * 연결된 경로 테이블: **RT_BLUE**
  * 경로 테이블로 전파: **RT_BLUE** 및 **Default**
* Red 가상 네트워크:
  * 연결된 경로 테이블: **RT_RED**
  * 경로 테이블로 전파: **RT_RED** 및 **Default**
* 분기:
  * 연결된 경로 테이블: **기본**
  * 경로 테이블로 전파: **RT_BLUE**, **RT_RED**, **Default**

> [!NOTE]
> 모든 분기가 Default 경로 테이블에 연결되어 있어야 하고 동일한 라우팅 테이블 집합으로 전파되어야 하므로 모든 분기의 연결 프로필이 동일합니다. 즉, VNet의 Red/Blue 개념은 분기에 적용될 수 없습니다.

> [!NOTE]
> 가상 WAN을 여러 지역에 배포하는 경우에는 모든 허브에서 **RT_BLUE** 및 **RT_RED** 경로 테이블을 만들어야 하고 각 VNet 연결의 경로는 전파 레이블을 사용하여 모든 가상 허브의 경로 테이블에 전파해야 합니다.

가상 허브 라우팅에 대한 자세한 내용은 [가상 허브 라우팅 정보](about-virtual-hub-routing.md)를 참조하세요.

## <a name="workflow"></a><a name="architecture"></a>워크플로

**그림 1** 에는 파란색 및 빨강색 VNet 연결이 있습니다.

* 파란색으로 연결된 VNet은 서로 연결될 수 있을 뿐만 아니라 모든 분기(VPN/ER/P2S) 연결에 도달할 수 있습니다.
* 빨간색으로 연결된 VNet은 서로 연결될 수 있을 뿐만 아니라 모든 분기(VPN/ER/P2S) 연결에 도달할 수 있습니다.

라우팅을 설정하는 경우 다음 단계를 고려하세요.

1. Azure Portal에서 **RT_BLUE** 와 **RT_RED** 라는 사용자 지정 경로 테이블 2개를 만듭니다.
2. 경로 테이블 **RT_BLUE** 에 대해 다음 설정을 적용합니다.
   * **연결**: 파란색 VNet을 모두 선택합니다.
   * **전파**: 분기에 대해 P2S 분기(VPN/ER/) 연결이 이 경로 테이블로 경로를 전파할 것을 암시하는 분기 옵션을 선택합니다.
3. 빨간색 VNet과 분기(VPN/ER/P2S)의 **RT_RED** 경로 테이블에 대해 동일한 단계를 반복합니다.

이렇게 하면 아래 그림에 표시된 것처럼 라우팅 구성이 변경됩니다.

**그림 1**

:::image type="content" source="./media/routing-scenarios/custom-isolation/custom.png" alt-text="그림 1":::

## <a name="next-steps"></a>다음 단계

* Virtual WAN에 대한 자세한 내용은 [FAQ](virtual-wan-faq.md)를 참조하세요.
* 가상 허브 라우팅에 대한 자세한 내용은 [가상 허브 라우팅 정보](about-virtual-hub-routing.md)를 참조하세요.
