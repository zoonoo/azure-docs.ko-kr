---
title: Application Gateway와 백엔드 풀 간의 보안 트래픽
titleSuffix: Azure Virtual WAN
description: 라우팅 시나리오 - 보안 Virtual WAN 허브에 연결된 스포크 VNet에 배포되는 애플리케이션 게이트웨이를 통과하는 안전한 트래픽입니다.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 04/12/2021
ms.author: cherylmc
ms.openlocfilehash: d9cb1251b90cf1c928f8286072bcd91e5ddf767e
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107315343"
---
# <a name="scenario-secure-traffic-between-application-gateway-and-backend-pools"></a>시나리오: Application Gateway와 백엔드 풀 간의 보안 트래픽

Virtual WAN 가상 허브 라우팅 작업 시 사용할 수 있는 시나리오가 상당히 많습니다. 이 시나리오에서는 사용자 트래픽이 보안 Virtual WAN 허브(Azure Firewall을 사용하는 Virtual WAN 허브)에 연결 된 스포크 VNet에 배포된 애플리케이션 게이트웨이를 통해 Azure에 들어갑니다. 그 목표는 보안 가상 허브에서 Azure Firewall을 사용하여 애플리케이션 게이트웨이와 백엔드 풀 간의 트래픽을 검사하는 것입니다.

이 시나리오에는 애플리케이션 게이트웨이와 백엔드 풀이 동일한 VNet에 있는지 또는 다른 Vnet에 있는지에 따라 두 가지 특정 디자인 패턴이 있습니다.

* **시나리오 1:** 애플리케이션 게이트웨이와 백엔드 풀이 동일한 가상 네트워크에서 Virtual WAN 허브(별도 서브넷)에 피어링되어 있습니다.
* **시나리오 2:** 애플리케이션 게이트웨이와 백엔드 풀이 서로 다른 가상 네트워크에서 Virtual WAN 허브에 피어링되어 있습니다.

## <a name="scenario-1---same-vnet"></a><a name="scenario-1"></a>시나리오 1 - 동일한 VNet

이 시나리오에서는 애플리케이션 게이트웨이와 백엔드 풀이 동일한 가상 네트워크에서 Virtual WAN 허브(별도 서브넷)에 피어링되어 있습니다.

:::image type="content" source="./media/routing-scenarios/secured-application-gateway/same-vnet.png" alt-text="시나리오 1의 다이어그램." lightbox="./media/routing-scenarios/secured-application-gateway/same-vnet.png":::

### <a name="workflow"></a>워크플로

현재 Virtual WAN 경로 테이블에서 스포크 가상 네트워크로 보급되는 경로는 전체 가상 네트워크에 적용되며, 스포크 VNet의 서브넷에는 적용되지 않습니다. 따라서 이 시나리오를 사용하려면 사용자 정의 경로가 필요합니다. UDR(사용자 정의 경로)에 대한 자세한 내용은 [Virtual Network 사용자 지정 경로](../virtual-network/virtual-networks-udr-overview.md#user-defined)를 참조하세요.


1. Azure Firewall Manager에서 애플리케이션 게이트웨이와 백엔드 풀이 있는 스포크 가상 네트워크에서 **보안 인터넷 트래픽 사용** 및 **보안 개인 트래픽 사용** 을 선택합니다.
1. 애플리케이션 게이트웨이 서브넷에 UDR(사용자 정의 경로)을 구성합니다.

   * 애플리케이션 게이트웨이에서 트래픽을 인터넷으로 직접 전송하게 하려면 다음 UDR을 지정합니다.

     * **주소 접두사:** 0.0.0.0.0/0
     * **다음 홉:** 인터넷

   * 애플리케이션 게이트웨이가 Virtual WAN 허브의 Azure Firewall을 통해 백엔드 풀로 트래픽을 전송하게 하려면 다음 UDR을 지정합니다.

      * **주소 접두사:** 백엔드 풀 서브넷(10.2.0.0/24)
      * **다음 홉:** Azure Firewall 개인 IP

1. 백엔드 풀 서브넷에 UDR(사용자 정의 경로)을 구성합니다.

   * **주소 접두사:** Application Gateway 서브넷
   * **다음 홉:** Azure Firewall 개인 IP

## <a name="scenario-2---different-vnets"></a><a name="scenario-2"></a>시나리오 2 - 다른 Vnet

이 시나리오에서는 애플리케이션 게이트웨이와 백엔드 풀이 서로 다른 가상 네트워크에서 Virtual WAN 허브에 피어링되어 있습니다.

:::image type="content" source="./media/routing-scenarios/secured-application-gateway/different-vnets.png" alt-text="시나리오 2의 다이어그램." lightbox="./media/routing-scenarios/secured-application-gateway/different-vnets.png":::

### <a name="workflow"></a>워크플로

현재 Virtual WAN 경로 테이블에서 스포크 가상 네트워크로 보급되는 경로는 전체 가상 네트워크에 적용되며, 스포크 VNet의 서브넷에는 적용되지 않습니다. 따라서 이 시나리오를 사용하려면 사용자 정의 경로가 필요합니다. UDR(사용자 정의 경로)에 대한 자세한 내용은 [Virtual Network 사용자 지정 경로](../virtual-network/virtual-networks-udr-overview.md#user-defined)를 참조하세요.

1. **Azure Firewall Manager** 에서 스포크 가상 네트워크 둘 다 **보안 인터넷 트래픽 사용** 및 **보안 개인 트래픽 사용** 을 선택합니다.

1. 애플리케이션 게이트웨이 서브넷에 UDR(사용자 정의 경로)을 구성합니다. 애플리케이션 게이트웨이에서 트래픽을 인터넷으로 직접 전송하게 하려면 다음 UDR을 지정합니다.

   * **주소 접두사:** 0.0.0.0.0/0
   * **다음 홉:** 인터넷

## <a name="next-steps"></a>다음 단계

* 가상 허브 라우팅에 대한 자세한 내용은 [가상 허브 라우팅 정보](about-virtual-hub-routing.md)를 참조하세요.
* 사용자 정의 경로에 대한 자세한 내용은 [Virtual Network 사용자 지정 경로](../virtual-network/virtual-networks-udr-overview.md#user-defined)를 참조하세요.
* Virtual WAN 보안 가상 허브에 대한 자세한 내용은 [보안 가상 허브](../firewall-manager/secured-virtual-hub.md)를 참조하세요.
