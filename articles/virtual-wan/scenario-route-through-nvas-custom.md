---
title: '시나리오: 사용자 지정 설정을 사용 하 여 Nva를 통해 트래픽 라우팅'
titleSuffix: Azure Virtual WAN
description: 이 시나리오는 인터넷 바인딩된 트래픽에 대해 다른 NVA를 사용 하 여 Nva를 통해 트래픽을 라우팅하는 데 도움이 됩니다.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 06/29/2020
ms.author: cherylmc
ms.openlocfilehash: 3fcad59bcfb75b8de6af14eae7e0bcb4962b17eb
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85568300"
---
# <a name="scenario-route-traffic-through-nvas---custom-preview"></a>시나리오: Nva를 통해 트래픽 라우팅-사용자 지정 (미리 보기)

가상 WAN 가상 허브 라우팅으로 작업할 때 몇 가지 시나리오를 사용할 수 있습니다. 이 NVA (네트워크 가상 어플라이언스) 시나리오에서 목표는 VNet <-> 분기로 NVA를 통해 트래픽을 라우팅하는 것이 고, 인터넷에 바인딩된 트래픽에 대해 다른 NVA를 사용 하는 것입니다. 가상 허브 라우팅에 대 한 자세한 내용은 [가상 허브 라우팅 정보](about-virtual-hub-routing.md)를 참조 하세요.

## <a name="scenario-architecture"></a><a name="architecture"></a>시나리오 아키텍처

**그림 1**에는 허브 **1**허브가 있습니다.

* **허브 1** 은 Nva vnet **VNET 4** 및 **vnet 5**에 직접 연결 됩니다.

* Vnet 1, 2, 3 및 분기 간의 트래픽 (VPN/ER/P2S)은 **VNET 4 NVA** 10.4.0.5를 통해 전달 되어야 합니다.

* Vnet 1, 2, 3의 모든 인터넷 바인딩된 트래픽은 **VNET 5 NVA** 10.5.0.5를 통해 전달 될 것으로 예상 됩니다.

**그림 1**

:::image type="content" source="./media/routing-scenarios/nva-custom/figure-1.png" alt-text="그림 1":::

## <a name="scenario-workflow"></a><a name="workflow"></a>시나리오 워크플로

NVA를 통해 라우팅을 설정 하려면 다음 단계를 고려해 야 합니다.

1. VNET5을 통해 인터넷에 바인딩된 트래픽을 이동 하려면 vnet 피어 링을 통해 VNET 5에 직접 연결 하는 Vnet 1, 2, 3이 필요 합니다. 0.0.0.0/0 및 다음 홉 10.5.0.5에 Vnet에 설정 된 UDR도 필요 합니다. 현재 가상 WAN은 0.0.0.0/0에 대 한 가상 허브에서 다음 홉 NVA를 허용 하지 않습니다.

1. Azure Portal에서 가상 허브로 이동 하 여 모든 Vnet 및 분기 연결의 전파를 통해 경로를 알아보는 **RT_Shared** 사용자 지정 경로 테이블을 만듭니다. **그림 2**에서이는 **RT_Shared**빈 사용자 지정 경로 테이블로 표시 됩니다.

   * **경로:** 정적 경로를 추가할 필요는 없습니다.

   * **연결:** Vnet 4 및 5를 선택 합니다 .이는 Vnet 4 및 5 연결이 경로 테이블 **RT_Shared**와 연결 됨을 의미 합니다.

   * **전파:** 모든 분기 및 VNet 연결이이 경로 테이블에 경로를 동적으로 전파 하도록 하려면 분기 및 모든 Vnet를 선택 합니다.

1. Vnet 1, 2, 3에서 분기로 트래픽을 전달 하는 **RT_V2B** 사용자 지정 경로 테이블을 만듭니다.

   * **경로:** 다음 홉을 VNET 4 연결로 사용 하 여 분기 (VPN/ER/P2S) (10.2.0.0/ **16의 경우)에 대**한 집계 된 고정 경로 항목을 추가 합니다. 또한 VNET 4의 분기 접두사 연결에서 정적 경로를 구성 하 고 VNET 4에서 NVA의 특정 IP로 사용할 다음 홉을 지정 해야 합니다.

   * **연결:** 모든 Vnet 1, 2, 3을 선택 합니다. 이는 VNet 연결 1, 2, 3이이 경로 테이블에 연결 되 고이 경로 테이블에서 경로 (정적 및 동적 via 전파)를 학습할 수 있음을 의미 합니다.

   * **전파:** 연결은 경로 테이블에 경로를 전파 합니다. Vnet 1, 2, 3을 선택 하면 Vnet 1, 2, 3에서이 경로 테이블로 경로를 전파할 수 있습니다. 분기 VNet 트래픽이 v에서 NVA를 통해 이동 하는 경우 분기 연결에서 RT_V2B로 경로를 전파할 필요가 없습니다.
  
1. 기본 경로 테이블 **DefaultRouteTable**를 편집 합니다.

   모든 VPN, Express 경로 및 사용자 VPN 연결은 기본 경로 테이블에 연결 됩니다. 모든 VPN, Express 경로 및 사용자 VPN 연결은 경로를 동일한 경로 테이블 집합으로 전파 합니다.

   * **경로:** Vnet 1, 2, 3 ( **그림 2**의 10.1.0.0/16)에 대해 집계 된 정적 경로 항목을 VNET 4 연결로 추가 합니다. 또한 vnet 1, 2 및 3 개의 집계 된 접두사에 대해 VNET 4의 연결에서 정적 경로를 구성 하 고, 다음 홉이 VNET 4에서 NVA의 특정 IP가 되도록 지정 해야 합니다.

   * **연결:** 분기 (VPN/ER/P2S)에 대 한 옵션이 선택 되어 있는지 확인 하 고 온-프레미스 분기 연결이 *defaultroutetable*에 연결 되도록 합니다.

   * **전파 위치:** 분기 (VPN/ER/P2S) 옵션을 선택 하 여 온-프레미스 연결이 *defaultroutetable*에 경로를 전파 하는지 확인 합니다.

**그림 2**

:::image type="content" source="./media/routing-scenarios/nva-custom/figure-2.png" alt-text="그림 2":::

## <a name="next-steps"></a>다음 단계

* Virtual WAN에 대한 자세한 내용은 [FAQ](virtual-wan-faq.md)를 참조하세요.
* 가상 허브 라우팅에 대 한 자세한 내용은 [가상 허브 라우팅 정보](about-virtual-hub-routing.md)를 참조 하세요.
