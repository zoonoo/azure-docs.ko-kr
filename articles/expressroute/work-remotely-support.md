---
title: Azure ExpressRoute를 사용하여 원격 사용자를 지원
description: 이 페이지에서는 COVID-19 전염병으로 인해 Azure ExpressRoute를 활용하여 원격으로 작업을 활성화하는 방법에 대해 설명합니다.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: article
ms.date: 03/22/2020
ms.author: ajitbhu
ms.openlocfilehash: 2fe990fd9c5922dd2e059dbb212cd8bb1cd3726e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80336662"
---
# <a name="using-azure-expressroute-to-create-hybrid-connectivity-to-support-remote-users"></a>Azure ExpressRoute를 사용하여 원격 사용자를 지원하는 하이브리드 연결을 만듭니다.

이 문서에서는 ExpressRoute, Azure, Microsoft 네트워크 및 Azure 파트너 에코시스템을 활용하여 원격으로 작업하는 방법을 설명합니다.

## <a name="connecting-to-azure-services-with-expressroute"></a>익스프레스루트를 사용하여 Azure 서비스에 연결

>[!NOTE]
>이 문서에서는 ExpressRoute, Azure, Microsoft 네트워크 및 Azure 파트너 에코시스템을 활용하여 원격으로 작업하고 COVID-19 위기로 인해 직면하고 있는 네트워크 문제를 완화하는 방법에 대해 설명합니다.
>

[ExpressRoute는](expressroute-introduction.md) Microsoft 데이터 센터와 구내 또는 코로케이션 시설에 있는 인프라 간의 개인 연결을 가능하게 하는 Azure 서비스입니다. ExpressRoute 연결은 공용 인터넷을 통해 이동하지 않습니다. 인터넷을 통한 일반적인 연결보다 낮고 일관된 대기 시간으로 안전한 연결, 안정성 및 속도를 제공합니다.

## <a name="useful-links"></a>유용한 링크

* [기존 ExpressRoute 회로의 대역폭을 늘리는 방법](expressroute-howto-circuit-portal-resource-manager.md#modify)
* [익스프레스루트 모니터링, 메트릭 및 경고](expressroute-monitoring-metrics-alerts.md#expressroute-gateway-connections-in-bitsseconds)
* [익스프레스루트를 통해 경로 최적화](expressroute-optimize-routing.md)
* [O365용 Azure 익스프레스루트](https://docs.microsoft.com/office365/enterprise/azure-expressroute?redirectSourcePath=%252farticle%252f6d2534a2-c19c-4a99-be5e-33a0cee5d3bd)
* [비대칭 라우팅 고려 사항](expressroute-asymmetric-routing.md)
* [Azure 포털에서 지원 요청을 여는 방법](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)

## <a name="troubleshoot"></a>문제 해결

* [ExpressRoute 연결 확인](expressroute-troubleshooting-expressroute-overview.md)
* [리소스 관리자](expressroute-troubleshooting-arp-resource-manager.md) 및 [클래식에서](expressroute-troubleshooting-arp-classic.md) ARP 테이블 얻기
* [실패한 회로 재설정](reset-circuit.md)
* [네트워크 성능 문제 해결](expressroute-troubleshooting-network-performance.md)

## <a name="next-steps"></a>다음 단계

* [익스프레스루트 연결 모델에 대해 알아보기](expressroute-connectivity-models.md)
* ExpressRoute 연결 및 라우팅 도메인에 대해 알아봅니다. [익스프레스루트 회로 및 라우팅 도메인](expressroute-circuit-peerings.md) 보기
* 서비스 공급자를 찾습니다. [익스프레스루트 파트너 및 피어링 위치](expressroute-locations.md) 보기
* 모든 필수 조건이 충족되었는지 확인합니다. [ExpressRoute 필수 조건](expressroute-prerequisites.md)을 참조하세요.
* [익스프레스루트 회로 생성 및 수정](expressroute-howto-circuit-portal-resource-manager.md)
* [ExpressRoute 회로의 피어링 만들기 및 수정](expressroute-howto-routing-portal-resource-manager.md)
* [가상 네트워크를 ExpressRoute 회로에 연결](expressroute-howto-linkvnet-portal-resource-manager.md)
