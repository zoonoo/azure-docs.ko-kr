---
title: Azure Traffic Manager | Microsoft Docs
description: 이 문서에서는 Azure Traffic Manager의 개요를 제공합니다. 애플리케이션에 대한 사용자 트래픽을 부하 분산하기에 적합한 선택인지 알아보세요.
services: traffic-manager
author: duongau
ms.service: traffic-manager
customer intent: As an IT admin, I want to learn about Traffic Manager and what I can use it for.
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/19/2021
ms.author: duau
ms.openlocfilehash: 8c8897218b153c8584c89abab98934268ccd555d
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/05/2021
ms.locfileid: "102182165"
---
# <a name="what-is-traffic-manager"></a>Traffic Manager란?
Azure Traffic Manager는 DNS 기반 트래픽 부하 분산 장치입니다. 이 서비스를 사용하면 글로벌 Azure 지역에서 공용 연결 애플리케이션에 트래픽을 배포할 수 있습니다. 또한 Traffic Manager는 고가용성과 빠른 응답성을 갖춘 퍼블릭 엔드포인트를 제공합니다.

Traffic Manager는 DNS를 사용하여 트래픽 라우팅 메서드에 따라 클라이언트 요청을 적절한 서비스 엔드포인트로 보냅니다. 또한 Traffic Manager는 모든 엔드포인트에 대한 상태 모니터링을 제공합니다. 엔드포인트는 Azure의 내부 또는 외부에서 호스팅되는 인터넷 연결 서비스일 수 있습니다. Traffic Manager는 다양한 애플리케이션 요구와 자동 장애 조치(failover)에 맞는 [트래픽 라우팅 방법](traffic-manager-routing-methods.md) 및 [엔드포인트 모니터링 옵션](traffic-manager-monitoring.md)을 제공합니다. Traffic Manager는 전체 Azure 지역의 오류를 포함한, 오류에 대해 복원력을 갖습니다.

>[!NOTE]
> Azure는 사용자 시나리오를 위한 완전히 관리되는 부하 분산 솔루션 모음을 제공합니다. 
> * 애플리케이션 계층에서 한 지역의 서버 간에 부하를 분산하려면 [Application Gateway](../application-gateway/overview.md)를 검토하세요.
> * 웹 트래픽의 글로벌 라우팅을 최적화하고 빠른 글로벌 장애 조치(failover)를 통해 최상위 계층 최종 사용자 성능과 안정성을 최적화해야 하는 경우 [Front Door](../frontdoor/front-door-overview.md)를 참조하세요.
> * 네트워크 계층 부하 분산을 수행하려면 [Load Balancer](../load-balancer/load-balancer-overview.md)를 검토하세요. 
> 
> 필요에 따라 엔드투엔드 시나리오에서 이러한 솔루션을 조합하여 이점을 얻을 수 있습니다.
> Azure 부하 분산 옵션 비교는 [Azure의 부하 분산 옵션 개요](/azure/architecture/guide/technology-choices/load-balancing-overview)를 참조하세요.

Traffic Manager는 다음 기능을 제공합니다.

## <a name="increase-application-availability"></a>애플리케이션 가용성 향상

Traffic Manager는 엔드포인트를 모니터링하여 엔드포인트가 중단될 경우 자동 장애 조치를 제공함으로써 중요한 애플리케이션에 대한 높은 가용성을 제공합니다.
    
## <a name="improve-application-performance"></a>애플리케이션 성능 향상

Azure를 통해 전 세계에 있는 데이터 센터에서 클라우드 서비스와 웹 사이트를 실행할 수 있습니다. Traffic Manager는 대기 시간이 가장 짧은 엔드포인트로 트랙픽을 보내 웹 사이트의 응답성을 향상시킬 수 있습니다.

## <a name="service-maintenance-without-downtime"></a>가동 중지 시간 없이 서비스 유지 관리

가동 중지 시간 없이 애플리케이션에 계획된 유지 관리 작업을 수행할 수 있습니다. Traffic Manager는 유지 관리가 진행 중인 동안 트래픽을 대체 엔드포인트로 전달할 수 있습니다.

## <a name="combine-hybrid-applications"></a>하이브리드 애플리케이션 결합

Traffic Manager는 &quot;[클라우드로 버스트](https://azure.microsoft.com/overview/what-is-cloud-bursting/)&quot;, &quot;클라우드로 마이그레이션&quot; 및 &quot;클라우드로 장애 조치(Failover)&quot; 시나리오를 포함한 하이브리드 클라우드 및 온-프레미스 배포에 사용할 수 있도록 외부, Azure가 아닌 엔드포인트을 지원합니다.

## <a name="distribute-traffic-for-complex-deployments"></a>복잡한 배포의 트래픽 분산

[중첩 Traffic Manager 프로필](traffic-manager-nested-profiles.md)을 사용하여 더 크고 복잡한 배포 요구 사항에 맞게 확장되는 세련되고 유연한 규칙을 만들도록 여러 트래픽 라우팅 방법을 결합할 수 있습니다.

## <a name="pricing"></a>가격 책정

가격 책정 정보는 [Traffic Manager 가격 책정](https://azure.microsoft.com/pricing/details/traffic-manager/)을 참조하세요.


## <a name="next-steps"></a>다음 단계

- [Traffic Manager 프로필을 만드는 방법](./quickstart-create-traffic-manager-profile.md)을 알아보세요.
- [Traffic Manager 작동 방식](traffic-manager-how-it-works.md)을 알아보세요.
- Traffic Manager에 대한 [질문과 대답](traffic-manager-FAQs.md)을 살펴보세요.