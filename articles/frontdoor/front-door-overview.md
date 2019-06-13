---
title: Azure Front Door Service | Microsoft Docs
description: 이 문서는 Azure Front Door의 개요를 제공합니다. 애플리케이션에 대한 사용자 트래픽을 부하 분산하기에 적합한 선택인지 알아보세요.
services: frontdoor
documentationcenter: ''
author: sharad4u
editor: ''
ms.service: frontdoor
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/29/2018
ms.author: sharadag
ms.openlocfilehash: 2d4c93b7ef707f5b09bc2f5f93d56c50c1bb3458
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60736329"
---
# <a name="what-is-azure-front-door-service"></a>Azure Front Door Service란?
Azure Front Door Service를 사용하면 최적의 성능과 고가용성을 지원하는 즉시 글로벌 장애 조치(failover)를 최적으로 구현하여 웹 트래픽의 글로벌 라우팅을 정의, 관리, 모니터링할 수 있습니다. Front Door를 사용하면 글로벌(다중 지역) 소비자 및 기업 애플리케이션을 글로벌 Azure 잠재 고객에게 도달하는 견고한 고성능의 맞춤형 최신 애플리케이션, API 및 콘텐츠로 변환할 수 있습니다.

Front Door는 Layer 7 또는 HTTP/HTTPS 계층에서 작동하며, TCP가 분할된 애니캐스트 프로토콜과 Microsoft의 글로벌 네트워크를 사용하여 글로벌 연결을 개선합니다. 따라서 구성에서 선택한 라우팅 방법에 따라 Front Door가 클라이언트 요청을 가장 빠르고 가장 가용성이 높은 애플리케이션 백엔드로 라우팅합니다. 애플리케이션 백엔드는 Azure의 내부 또는 외부에서 호스팅되는 모든 인터넷 연결 서비스입니다. Front Door는 다양한 애플리케이션 요구와 자동 장애 조치(failover)에 맞는 [트래픽 라우팅 방법](front-door-routing-methods.md) 및 [백엔드 상태 모니터링 옵션](front-door-health-probes.md)을 제공합니다. [Traffic Manager](../traffic-manager/traffic-manager-overview.md)와 유사한 Front Door는 전체 Azure 지역의 오류를 포함한, 오류에 대해 복원력을 갖습니다.

>[!NOTE]
> Azure는 사용자 시나리오를 위한 완전히 관리되는 부하 분산 솔루션 모음을 제공합니다. DNS 기반 글로벌 라우팅이 필요하고, TLS(Transport Layer Security) 프로토콜 종료("SSL 오프로드") 또는 HTTP/HTTPS 요청별 응용 프로그램 계층 처리에 대한 요구 사항이 **없는** 경우 [Traffic Manager](../traffic-manager/traffic-manager-overview.md)를 검토하세요. 특정 지역의 서버 간 부하 분산이 필요한 경우 응용 프로그램 계층은 [Application Gateway](../application-gateway/application-gateway-introduction.md)를 살펴보고, 네트워크 계층 부하 분산은 [Load Balancer](../load-balancer/load-balancer-overview.md)를 살펴보세요. 필요에 따라 엔드투엔드 시나리오에서 이러한 솔루션을 조합하여 이점을 얻을 수 있습니다.

다음 기능이 Front Door에 포함됩니다.

## <a name="accelerate-application-performance"></a>애플리케이션 성능 가속
Front Door는 분할된 TCP 기반 애니캐스트 프로토콜을 사용하여 최종 사용자가 가장 가까운 Front Door POP(Point of Presence)에 즉시 연결하도록 합니다. Front Door POP에서 애플리케이션 백 엔드에 연결하는 데 필요한 Microsoft의 글로벌 네트워크를 사용하여 성능을 유지하면서 더 높은 가용성 및 안정성을 확보하세요. 이러한 백 엔드 연결은 또한 최소 네트워크 대기 시간에 기반을 두고 있습니다. [분할된 TCP](front-door-routing-architecture.md#splittcp) 및 [애니캐스트 프로토콜](front-door-routing-architecture.md#anycast) 같은 Front Door 라우팅 기법에 대해 자세히 알아보세요.

## <a name="increase-application-availability-with-smart-health-probes"></a>스마트 상태 프로브를 사용하여 애플리케이션 가용성 향상

Front Door는 스마트 상태 프로브를 사용하고, 백 엔드의 대기 시간 및 가용성을 모니터링하고, 백 엔드가 다운될 때 즉각적인 자동 장애 조치(failover)를 제공하여 중요한 애플리케이션에 고가용성을 제공합니다. 따라서 가동 중지 시간 없이 애플리케이션에 계획된 유지 관리 작업을 실행할 수 있습니다. Front Door는 유지 관리가 진행 중인 동안 트래픽을 대체 백 엔드로 전달합니다.

## <a name="url-based-routing"></a>URL 기반 라우팅
URL 경로 기반 라우팅을 사용하여 요청의 URL 경로에 따라 트래픽을 백 엔드 풀로 라우팅할 수 있습니다. 시나리오 중 하나는 여러 콘텐츠 형식에 대한 요청을 서로 다른 백 엔드 풀로 라우팅하는 것입니다.

예를 들어 `http://www.contoso.com/users/*`에 대한 요청은 UserProfilePool로 라우팅되고, `http://www.contoso.com/products/*`에 대한 요청은 ProductInventoryPool로 라우팅됩니다.  Front Door는 최고의 매칭 알고리즘을 사용하여 더 복잡한 경로 매칭도 지원하므로 경로 패턴 중에 일치하는 패턴이 없을 경우 `http://www.contoso.com/*`에 기본 회람 규칙이 선택되고, 기본 범용 회람 규칙에 트래픽이 전달됩니다. [경로 매칭](front-door-route-matching.md)에 대해 자세히 알아보세요.

## <a name="multiple-site-hosting"></a>다중 사이트 호스팅
다중 사이트 호스팅을 통해 동일한 Front Door 구성에서 둘 이상의 웹 사이트를 구성할 수 있습니다. 이 기능을 사용하면 여러 웹 사이트를 단일 Front Door 구성으로 추가하여 배포에 보다 효율적인 토폴로지를 구성할 수 있습니다. 애플리케이션의 아키텍처에 따라 각 웹 사이트를 백 엔드 풀로 전달하거나 다양한 웹 사이트가 동일한 백 엔드 풀로 전달되도록 Azure Front Door Service를 구성할 수 있습니다. 예를 들어 Front Door는 ImagePool 및 VideoPool이라는 두 가지 백 엔드 풀에서 `images.contoso.com` 및 `videos.contoso.com`의 트래픽을 제공할 수 있습니다. 또는 MediaPool이라고 하는 단일 백 엔드 풀에 트래픽을 전달하도록 프런트 엔드 호스트를 구성할 수 있습니다.

마찬가지로, 동일한 Front Door에 `www.contoso.com` 및 `www.fabrikam.com`이라는 두 가지 도메인을 구성할 수 있습니다.

## <a name="session-affinity"></a>세션 선호도
쿠키 기반 세션 선호도 기능은 동일한 응용 프로그램 백 엔드에서 사용자 세션을 유지하려는 경우에 유용합니다. Front Door 관리 쿠키를 사용하면 사용자 세션의 후속 트래픽이 동일한 애플리케이션 백 엔드로 전달되어 처리됩니다. 이 기능은 세션 상태가 사용자 세션의 백 엔드에 로컬로 저장된 경우에 특히 중요합니다.

## <a name="secure-sockets-layer-ssl-termination"></a>SSL(Secure Sockets Layer) 종료
Front Door는 개별 사용자가 응용 프로그램 백 엔드를 사용하여 시간과 노력이 많이 드는 연결을 설정하는 대신 Front Door 환경과 SSL 연결을 설정할 수 있는 에지에서 SSL 종단을 지원합니다. 또한 Front Door는 Front Door 환경과 백 엔드 간에 HTTP와 HTTPS 연결을 모두 지원합니다. 따라서 엔드투엔드 SSL 암호화도 설정할 수 있습니다. 예를 들어 애플리케이션 워크로드의 Front Door가 활성 서비스의 웜 연결 재사용으로 인해 1분 안에 5000개 이상의 요청을 수신하는 경우 애플리케이션 백 엔드와 약 500개의 연결만 설정하여 백 엔드의 상단한 로드를 줄일 수 있습니다.

## <a name="custom-domains-and-certificate-management"></a>사용자 지정 도메인 및 인증서 관리
Front Door를 사용하여 콘텐츠를 제공할 때 고유한 도메인 이름을 Front Door URL에 표시하려는 경우 사용자 지정 도메인이 필요합니다. 볼 수 있는 도메인 이름이 있다면 고객에게 편리하고 브랜딩 목적상 유용합니다.
또한 Front Door는 사용자 지정 도메인 이름에 HTTPS를 지원합니다. 트래픽에 Front Door 관리 인증서를 선택하거나 사용자 지정 SSL 인증서를 업로드하여 이 기능을 사용하세요.

## <a name="application-layer-security"></a>애플리케이션 계층 보안
Azure Front Door를 사용하면 액세스 제어에 대한 사용자 지정 웹 응용 프로그램 방화벽(WAF) 규칙을 작성하여 클라이언트 IP 주소, 국가 코드 및 http 매개 변수를 기준으로 HTTP/HTTPS 워크로드 만료를 방지할 수 있습니다. 또한 Front Door를 사용하면 악성 봇 트래픽을 차단하는 등급 제한 규칙을 만들 수 있습니다. 

Front Door 플랫폼 자체는 [Azure DDoS Protection](../virtual-network/ddos-protection-overview.md) Basic으로 보호됩니다. 추가 보호를 위해 VNET에서 Azure DDoS Protection Standard를 활성화하고, 자동 튜닝 및 완화를 통해 네트워크 계층(TCP/UDP) 공격으로부터 리소스를 보호할 수 있습니다. Front Door는 계층 7 역방향 프록시이며, 웹 트래픽만 백 엔드를 통과하도록 허용하고 기본적으로 다른 종류의 트래픽은 차단합니다.

## <a name="url-rewrite"></a>URL 다시 쓰기
Front Door는 백 엔드에 전달할 요청을 생성할 때 사용할 선택적 사용자 지정 전달 경로를 구성할 수 있게 하여 [URL 재작성](front-door-url-rewrite.md)을 지원합니다. Front Door는 백 엔드에 요청을 전달할 때 전송할 호스트 헤더를 추가로 구성할 수 있게 해줍니다.

## <a name="protocol-support---ipv6-and-http2-traffic"></a>프로토콜 지원 - IPv6 및 HTTP/2 트래픽
Azure Front Door는 기본적으로 엔드투엔드 IPv6 연결을 지원하고 HTTP/2 프로토콜도 지원합니다. 

HTTP/2 프로토콜을 사용하면 장기 실행 TCP 연결을 통해 애플리케이션 백 엔드와 클라이언트 간의 전이중 통신을 수행할 수 있습니다. HTTP/2를 사용하면 백 엔드와 클라이언트 간의 대화형 통신이 가능하며, HTTP 기반 구현에서 필요에 따라 폴링하지 않고도 양방향 통신을 수행할 수 있습니다. HTTP/2 프로토콜은 HTTP와 달리 오버헤드가 낮고 여러 요청 또는 응답에 동일한 TCP 연결을 다시 사용하므로 리소스를 더 효율적으로 활용할 수 있습니다. [Azure Front Door Service의 HTTP/2 지원](front-door-http2.md)에 대해 자세히 알아보세요.

## <a name="pricing"></a>가격

가격 정보는 [Front Door 가격 책정](https://azure.microsoft.com/pricing/details/frontdoor/)을 참조하세요.

## <a name="next-steps"></a>다음 단계

- [Front Door를 만드는 방법](quickstart-create-front-door.md)을 알아봅니다.
- [Front Door의 작동 원리](front-door-routing-architecture.md)를 알아봅니다.
