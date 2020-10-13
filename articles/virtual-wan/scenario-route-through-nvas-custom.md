---
title: '시나리오: 사용자 지정 설정을 사용 하 여 Nva를 통해 트래픽 라우팅'
titleSuffix: Azure Virtual WAN
description: 이 시나리오는 인터넷 바인딩된 트래픽에 대해 다른 NVA를 사용 하 여 Nva를 통해 트래픽을 라우팅하는 데 도움이 됩니다.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 09/22/2020
ms.author: cherylmc
ms.custom: fasttrack-edit
ms.openlocfilehash: e1cf9faeab60264d491539256828151e496ade8f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91267502"
---
# <a name="scenario-route-traffic-through-nvas---custom-preview"></a>시나리오: Nva를 통해 트래픽 라우팅-사용자 지정 (미리 보기)

가상 WAN 가상 허브 라우팅으로 작업할 때 몇 가지 시나리오를 사용할 수 있습니다. 이 NVA (네트워크 가상 어플라이언스) 시나리오에서 목표는 Vnet와 분기 간의 통신을 위해 NVA를 통해 트래픽을 라우팅하는 것이 고, 인터넷에 바인딩된 트래픽에 대해 다른 NVA를 사용 하는 것입니다. 가상 허브 라우팅에 대 한 자세한 내용은 [가상 허브 라우팅 정보](about-virtual-hub-routing.md)를 참조 하세요.

## <a name="design"></a><a name="design"></a>디자인

이 시나리오에서는 명명 규칙을 사용 합니다.

* 사용자가 NVA ( **그림 1**의 VNet 4)를 배포한 가상 네트워크에 대 한 "서비스 VNet"은 비 인터넷 트래픽을 검사 합니다.
* 사용자가 인터넷에 바인딩된 트래픽을 검사 하는 데 사용할 NVA를 배포한 가상 네트워크에 대 한 "DMZ VNet" ( **그림 1**의 VNet 5).
* "NVA 스포크"는 NVA VNet ( **그림 1**의 vnet 1, vnet 2 및 vnet 3)에 연결 된 가상 네트워크에 대 한 것입니다.
* Microsoft에서 관리 하는 가상 WAN 허브의 "허브".

다음 연결 행렬은이 시나리오에서 지원 되는 흐름을 요약 합니다.

**연결 매트릭스**

| From          | 아래와 같이 변경합니다.|*NVA 스포크*|*서비스 VNet*|*DMZ VNet*|*분기 정적*|
|---|---|---|---|---|---|
| **NVA 스포크**| &#8594;|      X |            X |   피어링 |    정적    |
| **서비스 VNet**| &#8594;|    X |            X |      X    |      X       |
| **DMZ VNet** | &#8594;|       X |            X |      X    |      X       |
| **분기** | &#8594;|  정적 |            X |      X    |      X       |

연결 행렬의 각 셀은 특정 트래픽 흐름에 대해 가상 WAN 연결 (흐름의 "From" 쪽에서 행 머리글)이 대상 접두사 (흐름의 "대상" 쪽, 기울임꼴 열 머리글)를 학습 하는지 여부를 설명 합니다. "X"는 가상 WAN에 의해 기본적으로 연결이 제공 됨을 의미 하 고, "정적"은 고정 경로를 사용 하 여 가상 WAN에서 연결이 제공 됨을 의미 합니다. 여러 행에 대해 자세히 살펴보겠습니다.

* NVA 스포크:
  * 스포크는 가상 WAN 허브를 통해 직접 다른 스포크에 도달 합니다.
  * 스포크는 서비스 VNet을 가리키는 정적 경로를 통해 분기에 대 한 연결을 가져옵니다. 분기에서 특정 접두사를 배워야 하는 것은 아닙니다. 그렇지 않으면 보다 구체적이 고 요약을 재정의 합니다.
  * 스포크는 직접 VNet 피어 링을 통해 DMZ VNet에 인터넷 트래픽을 전송 합니다.
* 분기
  * 분기는 서비스 VNet을 가리키는 정적 라우팅을 통해 스포크로 이동 합니다. 요약 된 고정 경로를 재정의 하는 Vnet에서 특정 접두사를 학습 하면 안 됩니다.
* 서비스 VNet은 모든 VNet 및 모든 분기에서 연결할 수 있어야 하는 공유 서비스 VNet과 비슷합니다.
* 지원 되는 트래픽만 직접 VNet 피어 링을 통해 제공 되므로 DMZ VNet은 가상 WAN을 통해 연결할 필요가 없습니다. 그러나 구성 단순화를 위해 DMZ VNet과 동일한 연결 모델을 사용할 것입니다.

따라서 연결 매트릭스가 3 개의 경로 테이블로 변환 하는 세 가지 고유한 연결 패턴을 제공 합니다. 다른 Vnet에 대 한 연결은 다음과 같습니다.

* NVA 스포크:
  * 연결 된 경로 테이블: **RT_V2B**
  * 경로 테이블에 전파: **RT_V2B** 및 **RT_SHARED**
* NVA Vnet (내부 및 인터넷):
  * 연결 된 경로 테이블: **RT_SHARED**
  * 경로 테이블에 전파: **RT_SHARED**
* 분기
  * 연결 된 경로 테이블: **기본값**
  * 경로 테이블에 전파: **RT_SHARED** 및 **기본값**

이러한 정적 경로를 사용 하 여 VNet 간 및 분기 간 트래픽이 서비스 VNet (VNet 4)에서 NVA를 통과 하는지 확인 해야 합니다.

| 설명 | 경로 테이블 | 고정 경로              |
| ----------- | ----------- | ------------------------- |
| 분기    | RT_V2B      | 10.2.0.0/16-> vnet4conn  |
| NVA 스포크  | 기본값     | 10.1.0.0/16-> vnet4conn  |

이제 가상 WAN에서 패킷을 보낼 연결을 알고 있지만 연결에서 이러한 패킷을 받을 때 수행할 작업을 알고 있어야 합니다. 연결 경로 테이블이 사용 됩니다.

| 설명 | 연결 | 고정 경로            |
| ----------- | ---------- | ----------------------- |
| VNet2Branch | vnet4conn  | 10.2.0.0/16-> 10.4.0.5 |
| Branch2VNet | vnet4conn  | 10.1.0.0/16-> 10.4.0.5 |

이 시점에서 모든 항목을 준비 해야 합니다.

가상 허브 라우팅에 대 한 자세한 내용은 [가상 허브 라우팅 정보](about-virtual-hub-routing.md)를 참조 하세요.

## <a name="architecture"></a><a name="architecture"></a>아키텍처

**그림 1**에는 허브 **1**허브가 있습니다.

* **허브 1** 은 Nva vnet **VNet 4** 및 **vnet 5**에 직접 연결 됩니다.

* Vnet 1, 2, 3 및 분기 간의 트래픽 (VPN/ER/P2S)은 **VNet 4 NVA** 10.4.0.5를 통해 전달 되어야 합니다.

* Vnet 1, 2, 3의 모든 인터넷 바인딩된 트래픽은 **VNet 5 NVA** 10.5.0.5를 통해 전달 될 것으로 예상 됩니다.

**그림 1**

:::image type="content" source="./media/routing-scenarios/nva-custom/figure-1.png" alt-text="그림 1":::

## <a name="workflow"></a><a name="workflow"></a>워크플로

NVA를 통해 라우팅을 설정 하려면 다음 단계를 고려해 야 합니다.

1. 인터넷 바인딩 트래픽이 VNet 5를 통과할 수 있도록 하려면 Vnet 1, 2, 3이 필요 합니다. vnet 피어 링을 통해 vnet 5에 직접 연결 합니다. 0.0.0.0/0 및 다음 홉 10.5.0.5에 Vnet에 설정 된 UDR도 필요 합니다. 현재 가상 WAN은 0.0.0.0/0에 대 한 가상 허브에서 다음 홉 NVA를 허용 하지 않습니다.

1. Azure Portal에서 가상 허브로 이동 하 여 모든 Vnet 및 분기 연결의 전파를 통해 경로를 알아보는 **RT_Shared** 사용자 지정 경로 테이블을 만듭니다. **그림 2**에서이는 **RT_Shared**빈 사용자 지정 경로 테이블로 표시 됩니다.

   * **경로:** 정적 경로를 추가할 필요는 없습니다.

   * **연결:** Vnet 4 및 5를 선택 합니다 .이는 Vnet 4 및 5 연결이 경로 테이블 **RT_Shared**와 연결 됨을 의미 합니다.

   * **전파:** 모든 분기 및 VNet 연결이이 경로 테이블에 경로를 동적으로 전파 하도록 하려면 분기 및 모든 Vnet를 선택 합니다.

1. Vnet 1, 2, 3에서 분기로 트래픽을 전달 하는 **RT_V2B** 사용자 지정 경로 테이블을 만듭니다.

   * **경로:** 다음 홉을 VNet 4 연결로 사용 하 여 분기 (VPN/ER/P2S) (10.2.0.0/ **16의 경우)에 대**한 집계 된 고정 경로 항목을 추가 합니다. 또한 VNet 4의 분기 접두사 연결에서 정적 경로를 구성 하 고 VNet 4에서 NVA의 특정 IP로 사용할 다음 홉을 지정 해야 합니다.

   * **연결:** 모든 Vnet 1, 2, 3을 선택 합니다. 이는 VNet 연결 1, 2, 3이이 경로 테이블에 연결 되 고이 경로 테이블에서 경로 (정적 및 동적 via 전파)를 학습할 수 있음을 의미 합니다.

   * **전파:** 연결은 경로 테이블에 경로를 전파 합니다. Vnet 1, 2, 3을 선택 하면 Vnet 1, 2, 3에서이 경로 테이블로 경로를 전파할 수 있습니다. 분기 VNet 트래픽이 VNet 4에서 NVA를 통해 이동 하는 경우 분기 연결에서 RT_V2B로 경로를 전파할 필요가 없습니다.
  
1. 기본 경로 테이블 **DefaultRouteTable**를 편집 합니다.

   모든 VPN, Express 경로 및 사용자 VPN 연결은 기본 경로 테이블에 연결 됩니다. 모든 VPN, Express 경로 및 사용자 VPN 연결은 경로를 동일한 경로 테이블 집합으로 전파 합니다.

   * **경로:** Vnet 1, 2, 3 ( **그림 2**의 10.1.0.0/16)에 대해 집계 된 정적 경로 항목을 VNet 4 연결로 추가 합니다. 또한 vnet 1, 2 및 3 개의 집계 된 접두사에 대해 VNet 4의 연결에서 정적 경로를 구성 하 고, 다음 홉이 VNet 4에서 NVA의 특정 IP가 되도록 지정 해야 합니다.

   * **연결:** 분기 (VPN/ER/P2S)에 대 한 옵션이 선택 되어 있는지 확인 하 고 온-프레미스 분기 연결이 *defaultroutetable*에 연결 되도록 합니다.

   * **전파 위치:** 분기 (VPN/ER/P2S) 옵션을 선택 하 여 온-프레미스 연결이 *defaultroutetable*에 경로를 전파 하는지 확인 합니다.

**그림 2**

:::image type="content" source="./media/routing-scenarios/nva-custom/figure-2.png" alt-text="그림 1" lightbox="./media/routing-scenarios/nva-custom/figure-2.png":::

## <a name="next-steps"></a>다음 단계

* Virtual WAN에 대한 자세한 내용은 [FAQ](virtual-wan-faq.md)를 참조하세요.
* 가상 허브 라우팅에 대 한 자세한 내용은 [가상 허브 라우팅 정보](about-virtual-hub-routing.md)를 참조 하세요.
