---
title: Azure Express 경로를 사용 하 여 원격 사용자 지원
description: 이 페이지에서는 Azure Express 경로를 활용 하 여 COVID-19 전염병로 원격 작업을 사용 하도록 설정 하는 방법을 설명 합니다.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: article
ms.date: 03/22/2020
ms.author: ajitbhu
ms.openlocfilehash: 2fe990fd9c5922dd2e059dbb212cd8bb1cd3726e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "80336662"
---
# <a name="using-azure-expressroute-to-create-hybrid-connectivity-to-support-remote-users"></a>Azure Express 경로를 사용 하 여 원격 사용자를 지 원하는 하이브리드 연결 만들기

이 문서에서는 Express 경로, Azure, Microsoft 네트워크 및 Azure 파트너 에코 시스템을 활용 하 여 원격으로 작업 하는 방법을 설명 합니다.

## <a name="connecting-to-azure-services-with-expressroute"></a>Express 경로를 사용 하 여 Azure 서비스에 연결

>[!NOTE]
>이 문서에서는 Express 경로, Azure, Microsoft 네트워크 및 Azure 파트너 에코 시스템을 활용 하 여 원격으로 작업 하 고 COVID-19 위기 때문에 직면 하 고 있는 네트워크 문제를 완화 하는 방법을 설명 합니다.
>

[Express](expressroute-introduction.md) 경로는 Microsoft 데이터 센터와 온-프레미스 또는 공동 배치 기능의 인프라 간에 개인 연결을 가능 하 게 하는 Azure 서비스입니다. ExpressRoute 연결은 공용 인터넷을 통해 이동하지 않습니다. 인터넷을 통한 일반 연결 보다 낮은 대기 시간과 일관성 있는 보안 연결, 안정성 및 속도를 제공 합니다.

## <a name="useful-links"></a>유용한 링크

* [기존 Express 경로 회로에 대 한 대역폭을 늘리는 방법](expressroute-howto-circuit-portal-resource-manager.md#modify)
* [Express 경로 모니터링, 메트릭 및 경고](expressroute-monitoring-metrics-alerts.md#expressroute-gateway-connections-in-bitsseconds)
* [Express 경로를 통한 경로 최적화](expressroute-optimize-routing.md)
* [O365 용 Azure Express 경로](https://docs.microsoft.com/office365/enterprise/azure-expressroute?redirectSourcePath=%252farticle%252f6d2534a2-c19c-4a99-be5e-33a0cee5d3bd)
* [비대칭 라우팅 고려 사항](expressroute-asymmetric-routing.md)
* [Azure Portal에서 지원 요청을 여는 방법](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)

## <a name="troubleshoot"></a>문제 해결

* [ExpressRoute 연결 확인](expressroute-troubleshooting-expressroute-overview.md)
* [리소스 관리자](expressroute-troubleshooting-arp-resource-manager.md) 및 [클래식](expressroute-troubleshooting-arp-classic.md) 에서 ARP 테이블 가져오기
* [실패 한 회로 다시 설정](reset-circuit.md)
* [네트워크 성능 문제 해결](expressroute-troubleshooting-network-performance.md)

## <a name="next-steps"></a>다음 단계

* [Express 경로 연결 모델에 대 한 자세한 정보](expressroute-connectivity-models.md)
* ExpressRoute 연결 및 라우팅 도메인에 대해 알아봅니다. [Express 경로 회로 및 라우팅 도메인을](expressroute-circuit-peerings.md) 참조 하세요.
* 서비스 공급자를 찾습니다. [Express 경로 파트너 및 피어 링 위치를](expressroute-locations.md) 참조 하세요.
* 모든 필수 조건이 충족되었는지 확인합니다. [ExpressRoute 필수 조건](expressroute-prerequisites.md)을 참조하세요.
* [ExpressRoute 회로 만들기 및 수정](expressroute-howto-circuit-portal-resource-manager.md)
* [ExpressRoute 회로의 피어링 만들기 및 수정](expressroute-howto-routing-portal-resource-manager.md)
* [가상 네트워크를 ExpressRoute 회로에 연결](expressroute-howto-linkvnet-portal-resource-manager.md)
