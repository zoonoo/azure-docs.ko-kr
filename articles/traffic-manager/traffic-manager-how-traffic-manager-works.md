---
title: "Traffic Manager 작동 방식 | Microsoft Docs"
description: "이 문서에서는 Azure Traffic Manager 작동 방식에 대해 설명합니다."
services: traffic-manager
documentationcenter: 
author: kumudd
manager: timlt
editor: 
ms.assetid: a6c9370d-e60d-440f-aa82-b6d3fa5416b0
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/11/2016
ms.author: kumud
translationtype: Human Translation
ms.sourcegitcommit: 8827793d771a2982a3dccb5d5d1674af0cd472ce
ms.openlocfilehash: 6ba1732840d094497a9e2cb7a7ea7825a7f4bc89

---

# <a name="how-traffic-manager-works"></a>트래픽 관리자 작동 방식

Azure Traffic Manager를 사용하면 응용 프로그램 끝점에 트래픽 분산을 제어할 수 있습니다. 끝점은 Azure의 내부 또는 외부에서 호스팅되는 모든 인터넷 연결 서비스입니다.

트래픽 관리자는 다음과 같은 두 가지 주요 이점을 제공합니다.

1. 여러 가지 [트래픽 라우팅 메서드](traffic-manager-routing-methods.md)
2. [끝점 상태 연속 모니터링](traffic-manager-monitoring.md) 및 끝점이 실패할 경우 자동 장애 조치(failover)

클라이언트가 서비스에 연결하려고 시도하면 먼저 IP 주소에 대한 서비스의 DNS 이름을 확인합니다. 그런 다음 클라이언트는 해당 IP 주소에 연결하여 서비스에 액세스합니다.

**가장 중요한 사항은 트래픽 관리자가 DNS 수준에서 작동한다는 점입니다.**  Traffic Manager는 DNS를 사용하여 트래픽 라우팅 메서드의 규칙에 따라 클라이언트를 특정 서비스 끝점에 연결합니다. 클라이언트는 선택한 끝점에 **직접** 연결됩니다. Traffic Manager는 프록시 또는 게이트웨이가 아닙니다. Traffic Manager는 클라이언트와 서비스 간에 전달되는 트래픽을 표시하지 않습니다.

## <a name="traffic-manager-example"></a>트래픽 관리자 예제

Contoso Corp에서 새 파트너 포털을 개발했습니다. 이 포털의 URL은 https://partners.contoso.com/login.aspx입니다. 응용 프로그램은 3개의 Azure 지역에서 호스팅됩니다. 가용성을 개선하고 전역 성능을 최대화하려면 Traffic Manager를 사용하여 클라이언트 트래픽을 사용 가능한 가장 가까운 끝점에 배포합니다.

이 구성을 설정하려면

* 3개의 서비스 인스턴스를 배포합니다. 이러한 배포의 DNS 이름은 'contoso us.cloudapp.net', 'contoso eu.cloudapp.net' 및 'contoso asia.cloudapp.net'입니다.
* 그런 다음 'contoso.trafficmanager.net'이라는 이름의 Traffic Manager 프로필을 만들고 3개의 끝점에 '성능' 트래픽 라우팅 메서드를 사용하도록 구성합니다.
* 마지막으로, DNS CNAME 레코드를 사용하여 'contoso.trafficmanager.net'을 가리키는 베니티 도메인 이름 'partners.contoso.com'을 구성합니다.

![트래픽 관리자 DNS 구성][1]

> [!NOTE]
> Azure 트래픽 관리자에서 베니티 도메인을 사용할 때는 베니티 도메인 이름을 트래픽 관리자 도메인 이름으로 가리키는 CNAME을 사용해야 합니다. DNS 표준은 도메인의 'apex'(또는 루트)에서 CNAME을 만들 수 없습니다. 따라서 'contoso.com'('naked' 도메인이라고도 함)에 대한 CNAME을 만들 수 없습니다. 'contoso.com'의 도메인(예: 'www.contoso.com')에 대해서만 CNAME을 만들 수 있습니다. 이 제한을 해결하려면 간단한 HTTP 리디렉션을 사용하여 대체 이름(예: 'www.contoso.com')으로 'contoso.com'에 대한 요청을 보내는 것이 좋습니다.

## <a name="how-clients-connect-using-traffic-manager"></a>트래픽 관리자를 사용하여 클라이언트를 연결하는 방법

이전 예제를 계속하면 클라이언트가 https://partners.contoso.com/login.aspx 페이지를 요청하는 경우 클라이언트는 다음 단계를 수행하여 DNS 이름을 확인하고 연결을 설정합니다.

![트래픽 관리자를 사용하여 연결 설정][2]

1. 클라이언트는 DNS 쿼리를 구성된 재귀 DNS 서비스로 전송하여 'partners.contoso.com'이라는 이름을 확인합니다. '로컬 DNS' 서비스라고도 하는 재귀 DNS 서비스는 DNS 도메인을 직접 호스트하지 않습니다. 대신 클라이언트에서는 DNS 이름을 확인하는 데 필요한 인터넷에 권한 있는 다양한 DNS 서비스를 연결하는 작업을 오프로드합니다.
2. DNS 이름을 확인하기 위해 재귀 DNS 서비스는 'contoso.com' 도메인에 대한 이름 서버를 찾습니다. 그런 다음 이러한 이름 서버에 연결하여 'partners.contoso.com' DNS 레코드를 요청합니다. contoso.com DNS 서버는 contoso.trafficmanager.net을 가리키는 CNAME 레코드를 반환합니다.
3. 다음으로 재귀 DNS 서비스는 Azure Traffic Manager 서비스에서 제공하는 'trafficmanager.net' 도메인에 대한 이름 서버를 찾습니다. 그런 다음 해당 DNS 서버에 'contoso.trafficmanager.net' DNS 레코드에 대한 요청을 보냅니다.
4. 트래픽 관리자 이름 서버가 요청을 받습니다. 다음 항목에 따라 끝점을 선택합니다.

    - 각 끝점의 구성된 상태(사용하지 않는 끝점은 반환되지 않음)
    - 트래픽 관리자 상태 검사에서 확인된 각 끝점의 현재 상태. 자세한 내용은 [트래픽 관리자 끝점 모니터링](traffic-manager-monitoring.md)을 참조하세요.
    - 선택된 트래픽 라우팅 메서드. 자세한 내용은 [Traffic Manager 라우팅 메서드](traffic-manager-routing-methods.md)를 참조하세요.

5. 선택한 끝점은 다른 DNS CNAME 레코드로 반환됩니다. 이 경우에 contoso us.cloudapp.net을 반환한다고 가정하겠습니다.
6. 다음으로 재귀 DNS 서비스가 'cloudapp.net' 도메인에 대한 이름 서버를 찾습니다. 이러한 이름 서버에 연결하여 'contoso-us.cloudapp.net' DNS 레코드를 요청합니다. 미국 기반 서비스 끝점의 IP 주소를 포함하는 DNS 'A' 레코드가 반환됩니다.
7. 재귀 DNS 서비스는 결과를 통합하고 클라이언트에 단일 DNS 응답을 반환합니다.
8. 클라이언트는 DNS 결과를 받고 지정 IP 주소에 연결합니다. 클라이언트는 Traffic Manager를 통해서가 아니라 직접 응용 프로그램 서비스 끝점에 연결합니다. 해당 끝점은 HTTPS 끝점이므로 클라이언트는 필요한 SSL/TLS 핸드셰이크를 수행한 다음 ‘/login.aspx’ 페이지에 대해 HTTP GET 요청을 합니다.

재귀 DNS 서비스는 받는 DNS 응답을 캐시합니다. 클라이언트 장치의 DNS 확인자도 결과를 캐시합니다. 캐싱을 통해 후속 DNS 쿼리는 다른 이름 서버를 쿼리하는 대신 캐시의 데이터를 사용하여 더 신속하게 답변을 받을 수 있습니다. 캐시의 기간은 각 DNS 레코드의 'TTL(time-to-live)' 속성에 의해 결정됩니다. 짧은 값은 캐시가 빨리 만료되므로 Traffic Manager 이름 서버에 여러 차례의 왕복이 발생합니다. 긴 값은 실패한 끝점에서 트래픽을 멀리 이동하는 데 더 긴 시간이 걸립니다. 트래픽 관리자를 사용하면 응용 프로그램의 요구를 가장 잘 분산하는 값을 선택할 수 있도록 트래픽 관리자 DNS 응답에 사용되는 TTL을 구성할 수 있습니다.

## <a name="faq"></a>FAQ

### <a name="what-ip-address-does-traffic-manager-use"></a>트래픽 관리자가 사용하는 IP 주소는 어떻게 되나요?

트래픽 관리자 작동 방식에서 설명했듯이 트래픽 관리자는 DNS 수준에서 작동합니다. 클라이언트를 적절한 서비스 끝점으로 보내기 위해 DNS 응답을 전송합니다. 그러면 클라이언트는 트래픽 관리자를 통해서가 아니라 직접 서비스 끝점에 연결합니다.

따라서 트래픽 관리자는 클라이언트가 연결할 끝점 또는 IP 주소를 제공하지 않습니다. 따라서 서비스에 고정 IP 주소가 필요한 경우 Traffic Manager가 아니라 서비스에서 구성해야 합니다.

### <a name="does-traffic-manager-support-sticky-sessions"></a>Traffic Manager는 '고정' 세션을 지원하나요?

[앞](#how-clients-connect-using-traffic-manager)에서 설명된 것처럼 Traffic Manager는 DNS 수준에서 작동합니다. 클라이언트를 적절한 서비스 끝점으로 보내기 위해 DNS 응답을 사용합니다. 클라이언트는 Traffic Manager를 통해서가 아니라 직접 서비스 끝점에 연결합니다. 따라서 Traffic Manager는 클라이언트와 서비스 간에 HTTP 트래픽을 표시하지 않습니다.

또한 Traffic Manager에서 수신하는 DNS 쿼리의 원본 IP 주소는 클라이언트가 아니라 재귀 DNS 서비스에 속합니다. 따라서 Traffic Manager에는 개별 클라이언트를 추적할 방법이 없으므로 '고정' 세션을 구현할 수 없습니다. 이 제한은 모든 DNS 기반 트래픽 관리 시스템에 공통적으로 적용되며, Traffic Manager에 특정되지 않습니다.

### <a name="why-am-i-seeing-an-http-error-when-using-traffic-manager"></a>Traffic Manager를 사용할 때 HTTP 오류가 나타나는 이유는 무엇인가요?

[앞](#how-clients-connect-using-traffic-manager)에서 설명된 것처럼 Traffic Manager는 DNS 수준에서 작동합니다. 클라이언트를 적절한 서비스 끝점으로 보내기 위해 DNS 응답을 사용합니다. 그러면 클라이언트는 트래픽 관리자를 통해서가 아니라 직접 서비스 끝점에 연결합니다. Traffic Manager는 클라이언트와 서버 간에 HTTP 트래픽을 표시하지 않습니다. 따라서 표시된 모든 HTTP 오류는 응용 프로그램에서 가져온 것이어야 합니다. 클라이언트를 응용 프로그램에 연결하는 경우 모든 DNS 확인 단계를 완료합니다. Traffic Manager가 응용 프로그램 트래픽 흐름에 있는 모든 상호 작용을 포함합니다.

따라서 추가 조사는 응용 프로그램에 중점을 두어야 합니다.

클라이언트의 브라우저에서 보낸 HTTP 호스트 헤더는 문제의 가장 일반적인 소스입니다. 사용자가 사용하는 도메인 이름에 대한 올바른 호스트 헤더를 수락하도록 응용 프로그램을 구성하는지 확인합니다. Azure App Service를 사용하는 끝점의 경우 [Traffic Manager를 사용하는 Azure App Service의 웹앱에 대한 사용자 지정 도메인 이름 구성](../app-service-web/web-sites-traffic-manager-custom-domain-name.md)을 참조하세요.

### <a name="what-is-the-performance-impact-of-using-traffic-manager"></a>트래픽 관리자를 사용할 때 성능 영향은 무엇인가요?

[앞](#how-clients-connect-using-traffic-manager)에서 설명된 것처럼 Traffic Manager는 DNS 수준에서 작동합니다. 클라이언트가 서비스 끝점에 직접 연결되므로 연결이 설정된 후 트래픽 관리자를 사용하면 성능 영향이 발생하지 않습니다.

트래픽 관리자는 DNS 수준에서 응용 프로그램과 통합되므로 추가 DNS 조회를 을 DNS 확인 체인에 삽입해야 합니다( [트래픽 관리자 예제](#traffic-manager-example)참조). DNS 확인 시간에 대한 트래픽 관리자의 영향은 최소입니다. Traffic Manager는 이름 서버의 글로벌 네트워크를 사용하며 DNS 쿼리가 사용 가능한 가장 가까운 이름 서버로 라우팅되도록 [애니캐스트](https://en.wikipedia.org/wiki/Anycast) 네트워킹을 사용합니다. 또한 DNS 응답의 캐싱은 트래픽 관리자를 사용하여 발생한 추가 DNS 대기 시간이 세션 중 일부에만 적용되는 것을 의미합니다.

성능 메서드는 사용 가능한 가장 가까운 끝점으로 트래픽을 라우팅합니다. 최종적인 결론은 이 메서드와 연결된 전체 성능 영향을 최소화해야 한다는 것입니다. DNS 대기 시간 증가는 끝점에 대한 더 낮은 네트워크 대기 시간으로 오프셋되어야 합니다.

### <a name="what-application-protocols-can-i-use-with-traffic-manager"></a>트래픽 관리자에는 어떤 응용 프로그램 프로토콜을 사용할 수 있나요?

[앞](#how-clients-connect-using-traffic-manager)에서 설명된 것처럼 Traffic Manager는 DNS 수준에서 작동합니다. DNS 조회가 완료되면 클라이언트는 트래픽 관리자를 통해서가 아닌 응용 프로그램 끝점에 직접 연결됩니다. 따라서 이 연결에서는 모든 응용 프로그램 프로토콜을 사용할 수 있습니다. 그러나 Traffic Manager의 끝점 상태 검사에는 HTTP 또는 HTTPS 끝점이 필요합니다. 상태 검사의 끝점은 클라이언트가 연결된 응용 프로그램 끝점과 다를 수 있습니다.

### <a name="can-i-use-traffic-manager-with-a-naked-domain-name"></a>'naked' 도메인 이름으로 Traffic Manager를 사용할 수 있나요?

아니요. DNS 표준은 CNAME이 동일한 이름의 다른 DNS 레코드와 함께 존재하는 것을 허용하지 않습니다. DNS 영역의 apex(또는 루트)는 항상 SOA 및 권한이 있는 NS 레코드라는 두 개의 기존 DNS 레코드를 포함합니다. 즉, DNS 표준을 위반하지 않고 영역 apex에 CNAME 레코드를 만들 수 없습니다.

[Traffic Manager 예제](#traffic-manager-example)에 설명된 대로 Traffic Manager는 베니티 DNS 이름을 매핑하는 데 DNS CNAME 레코드가 필요합니다. 예를 들어 www.contoso.com을 Traffic Manager 프로필 DNS 이름 contoso.trafficmanager.net에 매핑합니다. 또한 Traffic Manager 프로필은 클라이언트가 연결해야 하는 끝점을 나타내는 보조 DNS CNAME을 반환합니다.

이 문제를 해결하려면 naked 도메인 이름에서 다른 URL로 트래픽을 연결하는 HTTP 리디렉션을 사용하는 것이 좋습니다. 그런 다음 Traffic Manager를 사용할 수 있습니다. 예를 들어 naked 도메인 'contoso.com'에서는 사용자를 Traffic Manager DNS 이름을 가리키는 'www.contoso.com'으로 리디렉션할 수 있습니다.

트래픽 관리자에서 naked 도메인에 대한 전체 지원은 기능 백로그에서 추적됩니다. [커뮤니티 피드백 사이트에서 투표](https://feedback.azure.com/forums/217313-networking/suggestions/5485350-support-apex-naked-domains-more-seamlessly)하여 이 기능 요청에 지원을 등록할 수 있습니다.

## <a name="next-steps"></a>다음 단계

트래픽 관리자 [끝점 모니터링 및 자동 장애 조치(failover)](traffic-manager-monitoring.md)에 대해 자세히 알아봅니다.

트래픽 관리자 [트래픽 라우팅 방법](traffic-manager-routing-methods.md)에 대해 자세히 알아봅니다.

<!--Image references-->
[1]: ./media/traffic-manager-how-traffic-manager-works/dns-configuration.png
[2]: ./media/traffic-manager-how-traffic-manager-works/flow.png



<!--HONumber=Nov16_HO5-->


