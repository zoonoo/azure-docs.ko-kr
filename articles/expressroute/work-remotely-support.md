---
title: Azure ExpressRoute를 사용하여 원격 사용자 지원
description: 이 페이지에서는 Azure ExpressRoute를 활용하여 코로나19 대유행에 따른 원격 근무를 지원하는 방법을 설명합니다.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 03/22/2020
ms.author: duau
ms.openlocfilehash: d51f47b73fe572ce81d3e7b54f902f94fcd11b5b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "89567676"
---
# <a name="using-azure-expressroute-to-create-hybrid-connectivity-to-support-remote-users"></a>Azure ExpressRoute를 사용하여 원격 사용자를 지원하는 하이브리드 연결 만들기

이 문서에서는 ExpressRoute, Azure, Microsoft 네트워크, Azure 파트너 에코시스템을 활용하여 원격 근무하는 방법을 설명합니다.

## <a name="connecting-to-azure-services-with-expressroute"></a>ExpressRoute를 사용하여 Azure 서비스에 연결

>[!NOTE]
>이 문서에서는 ExpressRoute, Azure, Microsoft 네트워크, Azure 파트너 에코시스템을 활용하여 원격 근무하고 코로나19 위기로 인해 발생하는 네트워크 이슈를 완화하는 방법을 설명합니다.
>

[ExpressRoute](expressroute-introduction.md)는 온-프레미스 또는 공동 배치 시설에 있는 인프라와 Microsoft 데이터 센터 간 프라이빗 연결을 지원하는 Azure 서비스입니다. ExpressRoute 연결은 공용 인터넷을 통해 이동하지 않습니다. 인터넷을 통한 기존 연결보다 더 짧고 일관된 대기 시간으로 보안 연결, 안정성, 속도를 제공합니다.

## <a name="useful-links"></a>유용한 링크

* [기존 ExpressRoute 회로의 대역폭을 늘리는 방법](expressroute-howto-circuit-portal-resource-manager.md#modify)
* [ExpressRoute 모니터링, 메트릭 및 경고](expressroute-monitoring-metrics-alerts.md#expressroute-gateway-connections-in-bitsseconds)
* [ExpressRoute를 통한 경로 최적화](expressroute-optimize-routing.md)
* [Microsoft 365용 Azure ExpressRoute](/microsoft-365/enterprise/azure-expressroute)
* [비대칭 라우팅 고려 사항](expressroute-asymmetric-routing.md)
* [Azure Portal에서 지원 요청을 여는 방법](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)

## <a name="troubleshoot"></a>문제 해결

* [ExpressRoute 연결 확인](expressroute-troubleshooting-expressroute-overview.md)
* [Resource Manager](expressroute-troubleshooting-arp-resource-manager.md) 및 [클래식](expressroute-troubleshooting-arp-classic.md)에서 ARP 테이블 가져오기
* [실패한 회로 다시 설정](reset-circuit.md)
* [네트워크 성능 문제 해결](expressroute-troubleshooting-network-performance.md)

## <a name="next-steps"></a>다음 단계

* [ExpressRoute 연결 모델에 대한 자세한 정보](expressroute-connectivity-models.md)
* ExpressRoute 연결 및 라우팅 도메인에 대해 알아봅니다. [ExpressRoute 회로 및 라우팅 도메인](expressroute-circuit-peerings.md) 참조
* 서비스 공급자를 찾습니다. [ExpressRoute 파트너 및 피어링 위치](expressroute-locations.md) 참조
* 모든 필수 조건이 충족되었는지 확인합니다. [ExpressRoute 필수 조건](expressroute-prerequisites.md)을 참조하세요.
* [ExpressRoute 회로 만들기 및 수정](expressroute-howto-circuit-portal-resource-manager.md)
* [ExpressRoute 회로의 피어링 만들기 및 수정](expressroute-howto-routing-portal-resource-manager.md)
* [가상 네트워크를 ExpressRoute 회로에 연결](expressroute-howto-linkvnet-portal-resource-manager.md)
