---
title: '시나리오: NVA를 통해 트래픽 라우팅'
titleSuffix: Azure Virtual WAN
description: NVA를 통한 트래픽 라우팅
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 06/29/2020
ms.author: cherylmc
ms.openlocfilehash: ed64b9d281cfbbf8202a99335ea2759b27a6fc42
ms.sourcegitcommit: 5cace04239f5efef4c1eed78144191a8b7d7fee8
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/08/2020
ms.locfileid: "86142965"
---
# <a name="scenario-route-traffic-through-an-nva"></a>시나리오: NVA를 통해 트래픽 라우팅

가상 WAN 가상 허브 라우팅으로 작업할 때 몇 가지 시나리오를 사용할 수 있습니다. 이 NVA 시나리오에서 목표는 NVA (네트워크 가상 어플라이언스)를 통해 분기에서 VNet으로, VNet에서 분기로 트래픽을 라우팅하는 것입니다. 가상 허브 라우팅에 대 한 자세한 내용은 [가상 허브 라우팅 정보](about-virtual-hub-routing.md)를 참조 하세요.

> [!NOTE]
> 라우팅 기능 중 일부는 여전히 롤아웃 될 수 있습니다. 사용자의 지역에서 롤아웃이 아직 발생 하지 않은 경우에는 다음 버전의 문서에 있는 단계를 사용 하세요.
>* [Azure Portal 문서](virtual-wan-route-table-nva-portal.md)
>* [PowerShell 문서](virtual-wan-route-table-nva.md)
>

## <a name="scenario-architecture"></a><a name="architecture"></a>시나리오 아키텍처

**그림 1**에는 두 개의 허브가 있습니다. **허브 1** 및 **허브 2**.

* **허브 1** 및 **허브 2** 는 nva Vnet **VNET 2** 및 **vnet 4**에 직접 연결 됩니다.

* Vnet **5** 와 **Vnet 6** 은 **vnet 2**와 피어 링 됩니다.

* Vnet **7** 및 **Vnet 8** 은 **vnet 4**로 피어 링 됩니다.

* **Vnet 5, 6, 7, 8** 은 간접 스포크 이며, 가상 허브에 직접 연결 되지 않습니다.

**그림 1**

:::image type="content" source="./media/routing-scenarios/nva/nva.png" alt-text="그림 1":::

## <a name="scenario-workflow"></a><a name="workflow"></a>시나리오 워크플로

NVA를 통해 라우팅을 설정 하려면 다음 단계를 고려해 야 합니다.

1. NVA 스포크 VNet 연결을 식별 합니다. **그림 1**에서는 **vnet 2 연결 (eastusconn)** 및 **vnet 4 연결 (weconn)** 을 사용할 수 있습니다.

   UDRs가 설정 되어 있는지 확인 합니다.
   * VNET 5와 6에서 VNET 2로, NVA IP
   * VNET 7 및 8에서 VNET 4 NVA IP로 
   
   VNET 5, 6, 7, 8을 가상 허브에 직접 연결할 필요는 없습니다. Vnet 5, 6, 7, 8의 NSGs가 branch (VPN/ER/P2S) 또는 Vnet에 연결 된 theire remote Vnet에 대 한 트래픽을 허용 하는지 확인 합니다. 예를 들어 VNET 5, 6은 NSGs가 원격 허브 2에 연결 된 온-프레미스 주소 접두사 및 Vnet 7, 8에 대 한 트래픽을 허용 하는지 확인 해야 합니다. 

2. Vnet 2, 5, 6에서 허브 1의 기본 경로 테이블에 대 한 집계 된 고정 경로 항목을 추가 합니다. 

   :::image type="content" source="./media/routing-scenarios/nva/nva-static-expand.png" alt-text="예제":::

3. VNET 2의 가상 네트워크 연결에서 Vnet 5, 6의 고정 경로를 구성 합니다. 가상 네트워크 연결에 대 한 라우팅 구성을 설정 하려면 [가상 허브 라우팅](how-to-virtual-hub-routing.md#routing-configuration)을 참조 하세요.

4. Vnet 4, 7, 8의 집계 된 정적 경로 항목을 허브 1의 기본 경로 테이블에 추가 합니다.

5. 허브 2의 기본 경로 테이블에 대해 2, 3 및 4 단계를 반복 합니다.

이렇게 하면 아래 그림에 표시 된 것 처럼 라우팅 구성이 변경 됩니다.

   :::image type="content" source="./media/routing-scenarios/nva/nva-result.png" alt-text="결과":::

## <a name="next-steps"></a>다음 단계

* Virtual WAN에 대한 자세한 내용은 [FAQ](virtual-wan-faq.md)를 참조하세요.
* 가상 허브 라우팅에 대 한 자세한 내용은 [가상 허브 라우팅 정보](about-virtual-hub-routing.md)를 참조 하세요.
