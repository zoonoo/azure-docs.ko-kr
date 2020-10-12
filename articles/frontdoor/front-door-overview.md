---
title: Azure Front Door | Microsoft Docs
description: 이 문서는 Azure Front Door의 개요를 제공합니다.
services: frontdoor
documentationcenter: ''
author: duongau
editor: ''
ms.service: frontdoor
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/27/2020
ms.author: duau
ms.openlocfilehash: bf0d802dc4ea4d18122a12b107dfb0cc0af80d63
ms.sourcegitcommit: ef69245ca06aa16775d4232b790b142b53a0c248
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/06/2020
ms.locfileid: "91773453"
---
# <a name="what-is-azure-front-door"></a>Azure Front Door란?

Azure Front Door는 Microsoft 글로벌 에지 네트워크를 사용하여 빠르고, 안전하고, 확장성이 뛰어난 웹 애플리케이션을 만들기 위한 확장 가능한 글로벌 진입점입니다. Front Door를 사용하면 글로벌 소비자 및 기업 애플리케이션을 글로벌 Azure 잠재 고객에게 도달하는 콘텐츠가 있는 견고한 고성능의 맞춤형 최신 애플리케이션으로 변환할 수 있습니다.

:::image type="content" source="media/front-door-overview/front-door-visual-diagram.png#center" alt-text="Front Door 아키텍처":::

Front Door는 Layer 7(HTTP/HTTPS 계층)에서 작동하며, TCP가 분할된 애니캐스트 프로토콜과 Microsoft의 글로벌 네트워크를 사용하여 글로벌 연결을 개선합니다. 라우팅 방법에 따라 Front Door가 클라이언트 요청을 가장 빠르고 가장 가용성이 높은 애플리케이션 백 엔드로 라우팅합니다. 애플리케이션 백엔드는 Azure의 내부 또는 외부에서 호스팅되는 모든 인터넷 연결 서비스입니다. Front Door는 다양한 애플리케이션 요구와 자동 장애 조치(failover) 시나리오에 맞는 [트래픽 라우팅 방법](front-door-routing-methods.md) 및 [백 엔드 상태 모니터링 옵션](front-door-health-probes.md)을 제공합니다. [Traffic Manager](../traffic-manager/traffic-manager-overview.md)와 유사한 Front Door는 전체 Azure 지역에 대한 오류를 포함한, 오류에 대해 복원력을 갖습니다.

>[!NOTE]
> Azure는 사용자 시나리오를 위한 완전히 관리되는 부하 분산 솔루션 모음을 제공합니다. 
> * DNS 기반 글로벌 라우팅을 수행하고 HTTP/HTTPS 요청 또는 애플리케이션 계층 처리당 TLS(Transport Layer Security) 프로토콜 종료("SSL 오프로드")에 대한 요구 사항이 **없는** 경우 [Traffic Manager](../traffic-manager/traffic-manager-overview.md)를 검토하세요. 
> * 애플리케이션 계층에서 한 지역의 서버 간에 부하를 분산하려면 [Application Gateway](../application-gateway/application-gateway-introduction.md)를 검토하세요.
> * 네트워크 계층 부하 분산을 수행하려면 [Load Balancer](../load-balancer/load-balancer-overview.md)를 검토하세요. 
> 
> 필요에 따라 엔드투엔드 시나리오에서 이러한 솔루션을 조합하여 이점을 얻을 수 있습니다.
> Azure 부하 분산 옵션 비교는 [Azure의 부하 분산 옵션 개요](https://docs.microsoft.com/azure/architecture/guide/technology-choices/load-balancing-overview)를 참조하세요.

## <a name="why-use-azure-front-door"></a>Azure Front Door를 사용하는 이유

Front Door를 사용하면 동적 웹 애플리케이션 및 정적 콘텐츠를 빌드, 운영 및 확장할 수 있습니다. Front Door를 사용하면 빠른 글로벌 장애 조치(failover)를 통한 최상위 계층 최종 사용자 성능 및 안정성을 최적으로 구현하여 웹 트래픽의 글로벌 라우팅을 정의, 관리 및 모니터링할 수 있습니다.

Front Door에 포함된 주요 기능:

* **[분할 TCP](front-door-routing-architecture.md#splittcp)** 기반 **[애니캐스트 프로토콜](front-door-routing-architecture.md#anycast)** 을 사용하여 애플리케이션 성능 가속화

* 백 엔드 리소스에 대한 인텔리전트 **[상태 프로브](front-door-health-probes.md)** 모니터링

*  요청에 대한 **[URL 경로 기반](front-door-route-matching.md)** 라우팅

* 효율적인 애플리케이션 인프라를 위해 여러 웹 사이트를 호스트할 수 있습니다. 

* 쿠키 기반 **[세션 선호도](front-door-routing-methods.md#affinity)**

* **[SSL 오프로딩](front-door-custom-domain-https.md)** 및 인증서 관리

* 사용자 고유의 **[사용자 지정 도메인](front-door-custom-domain.md)** 을 정의합니다. 

* 통합 **[WAF(Web Application Firewall)](../web-application-firewall/overview.md)** 를 사용한 애플리케이션 보안.

* **[URL 리디렉션](front-door-url-redirect.md)** 을 사용하여 HTTP 트래픽을 HTTPS로 리디렉션합니다.

* **[URL 다시 쓰기](front-door-url-rewrite.md)** 를 사용하는 사용자 지정 전달 경로

* 엔드투엔드 IPv6 연결 및 **[HTTP/2 프로토콜](front-door-http2.md)** 의 기본 지원

## <a name="pricing"></a>가격 책정

가격 정보는 [Front Door 가격 책정](https://azure.microsoft.com/pricing/details/frontdoor/)을 참조하세요. [Azure Front Door용 SLA](https://azure.microsoft.com/en-us/support/legal/sla/frontdoor/v1_0/)를 참조하세요.

## <a name="whats-new"></a>새로운 기능

RSS 피드를 구독하고 [Azure 업데이트](https://azure.microsoft.com/updates/?category=networking&query=Azure%20Front%20Door) 페이지에서 최신 Azure Front Door 기능 업데이트를 확인합니다.

## <a name="next-steps"></a>다음 단계

- [Front Door를 만드는](quickstart-create-front-door.md) 방법을 알아봅니다.
- [Front Door의 작동 원리](front-door-routing-architecture.md)를 알아봅니다.
