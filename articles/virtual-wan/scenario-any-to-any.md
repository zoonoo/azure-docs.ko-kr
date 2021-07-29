---
title: '시나리오: 보편적 연결(Any-to-Any)'
titleSuffix: Azure Virtual WAN
description: 스포크에서 다른 스포크에 연결할 수 있는 Virtual WAN Any-to-Any 라우팅 시나리오에 대해 알아봅니다.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 04/27/2021
ms.author: cherylmc
ms.custom: fasttrack-edit
ms.openlocfilehash: b27d1327eac8e108c462fd3c0f19a257a5385428
ms.sourcegitcommit: e1d5abd7b8ded7ff649a7e9a2c1a7b70fdc72440
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/27/2021
ms.locfileid: "110575993"
---
# <a name="scenario-any-to-any"></a>시나리오: 보편적 연결(Any-to-Any)

Virtual WAN 가상 허브 라우팅으로 작업할 때 몇 가지 시나리오를 사용할 수 있습니다. 보편적 연결(Any-to-Any) 시나리오에서 모든 스포크는 다른 스포크에 도달할 수 있습니다. 여러 허브가 있는 경우 표준 Virtual WAN에서 허브-투-허브(허브 간) 라우팅이 기본적으로 사용됩니다. Azure Portal, [Azure 빠른 시작 템플릿](quickstart-any-to-any-template.md) 등의 다양한 방법을 사용하여 이 구성을 만들 수 있습니다. 가상 허브 라우팅에 대한 자세한 내용은 [가상 허브 라우팅 정보](about-virtual-hub-routing.md)를 참조하세요. 

## <a name="design"></a><a name="design"></a>디자인

Virtual WAN 시나리오에서 필요한 경로 테이블 수를 파악하기 위해 연결 매트릭스를 작성할 수 있습니다. 여기서 각 셀은 원본(행)이 대상(열)과 통신할 수 있는지 여부를 나타냅니다.

| 시작 |   대상 |  *VNet* | *분기* |
| -------------- | -------- | ---------- | ---|
| VNet     | &#8594;| 직접 | 직접 |
| 분기   | &#8594;| 직접  | 직접 |

위의 표에 나와 있는 각 셀은 Virtual WAN 연결(흐름의 '시작' 쪽, 즉 행 머리글)이 대상 접두사(흐름의 '대상' 쪽, 즉 이탤릭체 열 머리글)와 통신하는지 여부를 설명합니다. 이 시나리오에서는 방화벽이나 네트워크 가상 어플라이언스를 사용할 수 없으므로 Virtual WAN에서 직접 통신 흐름이 전달됩니다(따라서 표에 '직접'으로 표시됨).

VNet 및 분기(VPN, ExpressRoute 및 사용자 VPN)로부터의 모든 연결에는 동일한 연결 요구 사항이 있으므로 단일 경로 테이블이 필요합니다. 결과적으로 모든 연결이 동일한 경로 테이블인 기본 경로 테이블과 연결되고 전파됩니다.

* 가상 네트워크:
  * 연결된 경로 테이블: **기본**
  * 경로 테이블로 전파: **기본**
* 분기:
  * 연결된 경로 테이블: **기본**
  * 경로 테이블로 전파: **기본**

가상 허브 라우팅에 대한 자세한 내용은 [가상 허브 라우팅 정보](about-virtual-hub-routing.md)를 참조하세요.

## <a name="architecture"></a><a name="architecture"></a>아키텍처

**그림 1** 에서는 모든 VNet 및 분기(VPN, ExpressRoute, P2S)가 서로 연결할 수 있습니다. 가상 허브에서는 연결이 다음과 같이 작동합니다.

* VPN 연결은 VPN 사이트를 VPN 게이트웨이에 연결합니다.
* 가상 네트워크 연결은 가상 네트워크를 가상 허브에 연결합니다. 가상 허브의 라우터는 VNet 간의 전송 기능을 제공합니다.
* ExpressRoute 연결은 ExpressRoute 회로를 ExpressRoute 게이트웨이에 연결합니다.

연결의 라우팅 구성을 **없음** 또는 사용자 지정 경로 테이블로 설정하지 않은 한 이러한 연결은 기본적으로 만들 때 기본 경로 테이블과 연결됩니다. 또한 이러한 연결은 기본적으로 기본 경로 테이블에 경로를 전파합니다. 이를 통해 모든 스포크(VNet, VPN, ER, P2S)가 서로 연결할 수 있는 보편적 연결(Any-to-Any) 시나리오를 사용할 수 있습니다.

**그림 1**

:::image type="content" source="./media/routing-scenarios/any-any/figure-1.png" alt-text="그림 1":::

## <a name="workflow"></a><a name="workflow"></a>워크플로

이 시나리오는 표준 Virtual WAN에서 기본적으로 사용됩니다. WAN 구성에서 분기 간 연결 설정이 사용되지 않는 경우 분기 스포크 간의 연결을 허용하지 않습니다. VPN/ExpressRoute/사용자 VPN은 Virtual WAN에서 분기 스포크로 간주됩니다.

## <a name="next-steps"></a>다음 단계

* Virtual WAN에 대한 자세한 내용은 [FAQ](virtual-wan-faq.md)를 참조하세요.
* 가상 허브 라우팅에 대한 자세한 내용은 [가상 허브 라우팅 정보](about-virtual-hub-routing.md)를 참조하세요.
