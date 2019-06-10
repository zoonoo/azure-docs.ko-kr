---
title: Azure ExpressRoute 회로 및 피어링 | Microsoft Docs
description: 이 페이지는 ExpressRoute 회로 및 라우팅 도메인/피어링에 대한 개요를 제공합니다.
services: expressroute
author: mialdrid
ms.service: expressroute
ms.topic: conceptual
ms.date: 04/24/2019
ms.author: mialdrid
ms.custom: seodec18
ms.openlocfilehash: c4290473a7c1edce02d74a4a787c62ccf0d9c052
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/30/2019
ms.locfileid: "64924305"
---
# <a name="expressroute-circuits-and-peering"></a>ExpressRoute 회로 및 피어링

ExpressRoute 회로는 연결 공급자를 통해 온-프레미스 인프라를 Microsoft에 연결합니다. 이 문서는 ExpressRoute 회로 및 라우팅 도메인/피어링을 이해하도록 도와줍니다. 다음 그림은 WAN 및 Microsoft 간 연결의 논리적 표현을 보여 줍니다.

![](./media/expressroute-circuit-peerings/expressroute-basic.png)

> [!IMPORTANT]
> Azure 공용 피어 링 사용 되지 않으며 새 ExpressRoute 회로 대 한 불가능 합니다. 새 회로 Microsoft 피어 링 및 개인 피어 링을 지원합니다.  
>

## <a name="circuits"></a>ExpressRoute 회로

ExpressRoute 회로는 연결 공급자를 통한 온-프레미스 인프라와 Microsoft 클라우드 서비스 간의 논리적 연결을 나타냅니다. 여러 ExpressRoute 회로를 주문할 수 있습니다. 각 회로는 동일하거나 다른 지역에 있을 수 있으며 서로 다른 연결 공급자를 통해 프레미스에 연결할 수 있습니다.

ExpressRoute 회로는 물리적 엔터티에 매핑되지 않습니다. 회로는 서비스 키(S 키)라고 하는 표준 GUID를 통해 고유하게 식별됩니다. 서비스 키는 Microsoft, 연결 공급자 및 사용자 간에 교환되는 유일한 정보의 부분입니다. S 키는 보안을 위한 암호가 아닙니다. ExpressRoute 회로와 S 키 사이에는 1:1 매핑이 있습니다.

새 ExpressRoute 회로에는 두 개의 독립 피어링이 포함될 수 있습니다. 프라이빗 피어링 및 Microsoft 피어링. 반면 기존 ExpressRoute 회로에는 3개의 피어링이 포함될 수 있습니다. Azure 공용, Azure 프라이빗 및 Microsoft. 각 피어링은 한 쌍의 독립 BGP 세션으로, 각각 고가용성을 위해 중복 구성됩니다. ExpressRoute 회로와 라우팅 도메인 사이에는 1:N(1 <= N <= 3) 매핑이 있습니다. ExpressRoute 회로는 ExpressRoute 회로마다 1개, 2개 또는 3개의 피어링을 모두 사용할 수 있습니다.

각 회로는 고정 대역폭(50Mbps, 100Mbps, 200Mbps, 500Mbps, 1Gbps, 10Gbps)이며 연결 공급자 및 피어링 위치에 매핑됩니다. 선택한 대역폭은 모든 회로 피어링에서 공유됩니다.

### <a name="quotas"></a>할당량, 제한 및 제약 조건

모든 ExpressRoute 회로에는 기본 할당량 및 제한이 적용됩니다. 할당량에 대한 최신 정보는 [Azure 구독 및 서비스 제한, 할당량 및 제약 조건](../azure-subscription-service-limits.md) 페이지를 참조하세요.

## <a name="routingdomains"></a>ExpressRoute 피어링

ExpressRoute 회로에는 연결된 여러 라우팅 도메인/피어링이 있습니다. Azure 공용, Azure 프라이빗 및 Microsoft. 각 피어링은 고가용성을 위해 활성-활성 또는 부하 공유로 구성된 라우터 쌍에서 동일하게 구성됩니다. Azure 서비스는 IP 주소 지정 스키마를 나타내기 위해 *Azure 공용* 및 *Azure 프라이빗*으로 분류됩니다.

![](./media/expressroute-circuit-peerings/expressroute-peerings.png)

### <a name="privatepeering"></a>Azure 개인 피어링

Azure 컴퓨팅 서비스, 즉 가상 머신(IaaS) 및 가상 네트워크 내에 배포된 클라우드 서비스(PaaS)는 프라이빗 피어링 도메인을 통해 연결될 수 있습니다. 프라이빗 피어링 도메인은 Microsoft Azure로의 핵심 네트워크의 신뢰할 수 있는 확장으로 간주됩니다. 핵심 네트워크 및 Azure Vnet(가상 네트워크) 간의 양방향 연결을 설정할 수 있습니다. 이 피어링을 통해 개인 IP 주소에서 가상 머신과 클라우드 서비스에 직접 연결할 수 있습니다.  

둘 이상의 가상 네트워크를 프라이빗 피어링 도메인에 연결할 수 있습니다. 제한 및 제한 사항에 대한 내용은 [FAQ 페이지](expressroute-faqs.md) 를 검토하세요. 제한 사항에 대한 최신 정보는 [Azure 구독 및 서비스 제한, 할당량 및 제약 조건](../azure-subscription-service-limits.md) 페이지에서 확인할 수 있습니다.  라우팅 구성에 대한 자세한 내용은 [라우팅](expressroute-routing.md) 페이지를 참조하세요.

### <a name="microsoftpeering"></a>Microsoft 피어링

[!INCLUDE [expressroute-office365-include](../../includes/expressroute-office365-include.md)]

Microsoft 피어링을 통해 Microsoft 온라인 서비스(Office 365, Dynamics 365 및 Azure PaaS 서비스)에 연결됩니다. Microsoft 피어링 라우팅 도메인을 통해 WAN 및 Microsoft 클라우드 서비스 간의 양방향 연결을 설정합니다. 사용자 또는 연결 공급자가 소유하는 공용 IP 주소에 대해서만 Microsoft 클라우드 서비스에 연결 해야 하고 모든 정의된 규칙을 따라야 합니다. 자세한 내용은 [ExpressRoute 필수 구성 요소](expressroute-prerequisites.md) 페이지를 참조하세요.

지원 서비스, 비용 및 구성 세부 정보에 대한 자세한 내용은 [FAQ 페이지](expressroute-faqs.md) 를 참조하세요. Microsoft 피어링이 지원하는 연결 공급자의 목록에 대한 정보는 [ExpressRoute 위치](expressroute-locations.md) 페이지를 참조하세요.

### <a name="publicpeering"></a>Azure 공용 피어링(새 회로에 사용되지 않음)

> [!Note]
> Azure 공용 피어 링에 각 BGP 세션에 연결 된 NAT IP 주소 1 NAT IP 주소 2 개 이상인 경우, Microsoft 피어 링으로 이동 합니다. Microsoft 피어 링 선택적 접두사 보급 알림에 대 한 경로 필터를 사용 하 여 뿐만 아니라 고유한 NAT 할당을 구성할 수 있습니다. 자세한 내용은 [Microsoft 피어 링으로 이동](https://docs.microsoft.com/azure/expressroute/how-to-move-peering)합니다.
>

Azure Storage, SQL Databases 및 Websites와 같은 서비스는 공용 IP 주소에 제공됩니다. 공용 피어링 라우팅 도메인을 통해 공용 IP 주소(클라우드 서비스의 VIP 포함)에서 호스팅되는 서비스에 개인적으로 연결할 수 있습니다. 인터넷을 통해 연결하지 않고도 공용 피어링 도메인을 DMZ에 연결하고 WAN에서 해당 공용 IP 주소의 모든 Azure 서비스에 연결할 수 있습니다.

연결은 항상 사용자의 WAN에서 Microsoft Azure 서비스로 시작됩니다. Microsoft Azure 서비스가 라우팅 도메인을 통해 네트워크로의 연결을 시작할 수 없습니다. 공용 피어링을 사용하도록 설정하면 모든 Azure 서비스에 연결할 수 있습니다. Microsoft에서 경로를 보급하는 서비스는 사용자가 선택할 수 없습니다.

필요한 경로만 이용하도록 네트워크 내에 사용자 지정 경로 필터를 정의할 수 있습니다. 라우팅 구성에 대한 자세한 내용은 [라우팅](expressroute-routing.md) 페이지를 참조하세요.

공용 피어링 라우팅 도메인을 통해 지원되는 서비스에 대한 자세한 내용은 [FAQ](expressroute-faqs.md)를 참조하세요.

## <a name="peeringcompare"></a>피어링 비교

다음 테이블에서 3개의 피어링을 비교합니다.

|  | **개인 피어링** | **Microsoft 피어링** |  **공용 피어링**(새 회로에 사용되지 않음) |
| --- | --- | --- | --- |
| **피어링당 지원되는 최대값 # 접두사** |기본적으로 4000, ExpressRoute 프리미엄으로 10,000 |200 |200 |
| **지원되는 IP 주소 범위** |WAN 내의 유효한 IP 주소. |사용자 또는 연결 공급자가 소유한 공용 IP 주소. |사용자 또는 연결 공급자가 소유한 공용 IP 주소. |
| **AS 번호 요구 사항** |프라이빗 및 공용 AS 번호. 공용 AS 번호를 사용하려는 경우 해당 번호를 소유하고 있어야 합니다. |프라이빗 및 공용 AS 번호. 하지만, 공용 IP 주소의 소유권을 증명해야 합니다. |프라이빗 및 공용 AS 번호. 하지만, 공용 IP 주소의 소유권을 증명해야 합니다. |
| **지원되는 IP 프로토콜**| IPv4 |  IPv4, IPv6 | IPv4 |
| **라우팅 인터페이스 IP 주소** |RFC1918 및 공용 IP 주소 |라우팅 레지스트리의 사용자에게 등록된 공용 IP 주소. |라우팅 레지스트리의 사용자에게 등록된 공용 IP 주소. |
| **MD5 해시 지원** |예 |예 |예 |

ExpressRoute 회로의 일부로 하나 이상의 라우팅 도메인을 사용할 수 있습니다. 단일 라우팅 도메인으로 결합하려는 경우 모든 라우팅 도메인을 동일 VPN에 넣도록 선택할 수 있습니다. 위의 도표와 유사한 다른 라우팅 도메인에도 넣습니다. 프라이빗 피어링이 직접 핵심 네트워크에 연결되고 공용 및 Microsoft 피어링 링크가 DMZ에 연결되는 것이 권장 구성입니다.

각 피어링에는 별도 BGP 세션이 필요합니다(각 피어링 형식에 한 쌍). BGP 세션 쌍은 항상 사용 가능한 링크를 제공합니다. 계층 2 연결 공급자를 통해 연결하는 경우, 사용자가 라우팅을 구성하고 관리합니다. ExpressRoute를 설정하기 위한 [워크플로](expressroute-workflows.md) 를 검토하여 자세히 알아볼 수 있습니다.

## <a name="health"></a>ExpressRoute 상태

ExpressRoute 회로는 NPM([네트워크 성능 모니터](https://docs.microsoft.com/azure/networking/network-monitoring-overview))을 사용하여 가용성, VNet에 대한 연결성 및 대역폭 사용률에 대해 모니터링할 수 있습니다.

NPM은 Azure 프라이빗 피어링 및 Microsoft 피어링의 상태를 모니터링합니다. 자세한 내용은 [포스트](https://azure.microsoft.com/blog/monitoring-of-azure-expressroute-in-preview/)를 확인하세요.

## <a name="next-steps"></a>다음 단계

* 서비스 공급자를 찾습니다. [ExpressRoute 서비스 공급자 및 위치](expressroute-locations.md)를 참조하세요.
* 모든 필수 조건이 충족되었는지 확인합니다. [ExpressRoute 필수 조건](expressroute-prerequisites.md)을 참조하세요.
* ExpressRoute 연결을 구성합니다.
  * [ExpressRoute 회로 만들기 및 관리](expressroute-howto-circuit-portal-resource-manager.md)
  * [ExpressRoute 회로에 대한 라우팅(피어링) 구성](expressroute-howto-routing-portal-resource-manager.md)
