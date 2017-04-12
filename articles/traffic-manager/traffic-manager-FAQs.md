---
title: Azure Traffic Manager - FAQ | Microsoft Docs
description: "이 문서에서는 Traffic Manager에 대한 질문과 대답을 제공합니다."
services: traffic-manager
documentationcenter: 
author: kumudd
manager: timlt
editor: 
ms.assetid: 75d5ff9a-f4b9-4b05-af32-700e7bdfea5a
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/15/2017
ms.author: kumud
translationtype: Human Translation
ms.sourcegitcommit: 4f2230ea0cc5b3e258a1a26a39e99433b04ffe18
ms.openlocfilehash: 8c4c8db3cf57537dd77d33b3ded2dc24167f511f
ms.lasthandoff: 03/25/2017

---

# <a name="traffic-manager-frequently-asked-questions-faq"></a>Traffic Manager FAQ(질문과 대답)

## <a name="traffic-manager-basics"></a>Traffic Manager 기본 사항

### <a name="what-ip-address-does-traffic-manager-use"></a>트래픽 관리자가 사용하는 IP 주소는 어떻게 되나요?

[Traffic Manager 작동 방식](../traffic-manager/traffic-manager-overview.md#how-traffic-manager-works)에서 설명했듯이 Traffic Manager는 DNS 수준에서 작동합니다. 클라이언트를 적절한 서비스 끝점으로 보내기 위해 DNS 응답을 전송합니다. 그러면 클라이언트는 트래픽 관리자를 통해서가 아니라 직접 서비스 끝점에 연결합니다.

따라서 트래픽 관리자는 클라이언트가 연결할 끝점 또는 IP 주소를 제공하지 않습니다. 따라서 서비스에 고정 IP 주소가 필요한 경우 Traffic Manager가 아니라 서비스에서 구성해야 합니다.

### <a name="does-traffic-manager-support-sticky-sessions"></a>Traffic Manager는 '고정' 세션을 지원하나요?

[Traffic Manager 작동 방식](../traffic-manager/traffic-manager-overview.md#how-traffic-manager-works)에서 설명했듯이 Traffic Manager는 DNS 수준에서 작동합니다. 클라이언트를 적절한 서비스 끝점으로 보내기 위해 DNS 응답을 사용합니다. 클라이언트는 Traffic Manager를 통해서가 아니라 직접 서비스 끝점에 연결합니다. 따라서 Traffic Manager는 클라이언트와 서비스 간에 HTTP 트래픽을 표시하지 않습니다.

또한 Traffic Manager에서 수신하는 DNS 쿼리의 원본 IP 주소는 클라이언트가 아니라 재귀 DNS 서비스에 속합니다. 따라서 Traffic Manager에는 개별 클라이언트를 추적할 방법이 없으므로 '고정' 세션을 구현할 수 없습니다. 이 제한은 모든 DNS 기반 트래픽 관리 시스템에 공통적으로 적용되며, Traffic Manager에 특정되지 않습니다.

### <a name="why-am-i-seeing-an-http-error-when-using-traffic-manager"></a>Traffic Manager를 사용할 때 HTTP 오류가 나타나는 이유는 무엇인가요?

[Traffic Manager 작동 방식](../traffic-manager/traffic-manager-overview.md#how-traffic-manager-works)에서 설명했듯이 Traffic Manager는 DNS 수준에서 작동합니다. 클라이언트를 적절한 서비스 끝점으로 보내기 위해 DNS 응답을 사용합니다. 그러면 클라이언트는 트래픽 관리자를 통해서가 아니라 직접 서비스 끝점에 연결합니다. Traffic Manager는 클라이언트와 서버 간에 HTTP 트래픽을 표시하지 않습니다. 따라서 표시된 모든 HTTP 오류는 응용 프로그램에서 가져온 것이어야 합니다. 클라이언트를 응용 프로그램에 연결하는 경우 모든 DNS 확인 단계를 완료합니다. Traffic Manager가 응용 프로그램 트래픽 흐름에 있는 모든 상호 작용을 포함합니다.

따라서 추가 조사는 응용 프로그램에 중점을 두어야 합니다.

클라이언트의 브라우저에서 보낸 HTTP 호스트 헤더는 문제의 가장 일반적인 소스입니다. 사용자가 사용하는 도메인 이름에 대한 올바른 호스트 헤더를 수락하도록 응용 프로그램을 구성하는지 확인합니다. Azure App Service를 사용하는 끝점의 경우 [Traffic Manager를 사용하는 Azure App Service의 웹앱에 대한 사용자 지정 도메인 이름 구성](../app-service-web/web-sites-traffic-manager-custom-domain-name.md)을 참조하세요.

### <a name="what-is-the-performance-impact-of-using-traffic-manager"></a>트래픽 관리자를 사용할 때 성능 영향은 무엇인가요?

[Traffic Manager 작동 방식](../traffic-manager/traffic-manager-overview.md#how-traffic-manager-works)에서 설명했듯이 Traffic Manager는 DNS 수준에서 작동합니다. 클라이언트가 서비스 끝점에 직접 연결되므로 연결이 설정된 후 트래픽 관리자를 사용하면 성능 영향이 발생하지 않습니다.

Traffic Manager는 DNS 수준에서 응용 프로그램과 통합하므로 추가 DNS 조회를 DNS 확인 체인에 삽입해야 합니다. DNS 확인 시간에 대한 트래픽 관리자의 영향은 최소입니다. Traffic Manager는 이름 서버의 글로벌 네트워크를 사용하며 DNS 쿼리가 사용 가능한 가장 가까운 이름 서버로 라우팅되도록 [애니캐스트](https://en.wikipedia.org/wiki/Anycast) 네트워킹을 사용합니다. 또한 DNS 응답의 캐싱은 트래픽 관리자를 사용하여 발생한 추가 DNS 대기 시간이 세션 중 일부에만 적용되는 것을 의미합니다.

성능 메서드는 사용 가능한 가장 가까운 끝점으로 트래픽을 라우팅합니다. 최종적인 결론은 이 메서드와 연결된 전체 성능 영향을 최소화해야 한다는 것입니다. DNS 대기 시간 증가는 끝점에 대한 더 낮은 네트워크 대기 시간으로 오프셋되어야 합니다.

### <a name="what-application-protocols-can-i-use-with-traffic-manager"></a>트래픽 관리자에는 어떤 응용 프로그램 프로토콜을 사용할 수 있나요?

[Traffic Manager 작동 방식](../traffic-manager/traffic-manager-overview.md#how-traffic-manager-works)에서 설명했듯이 Traffic Manager는 DNS 수준에서 작동합니다. DNS 조회가 완료되면 클라이언트는 트래픽 관리자를 통해서가 아닌 응용 프로그램 끝점에 직접 연결됩니다. 따라서 이 연결에서는 모든 응용 프로그램 프로토콜을 사용할 수 있습니다. 그러나 Traffic Manager의 끝점 상태 검사에는 HTTP 또는 HTTPS 끝점이 필요합니다. 상태 검사의 끝점은 클라이언트가 연결된 응용 프로그램 끝점과 다를 수 있습니다.

### <a name="can-i-use-traffic-manager-with-a-naked-domain-name"></a>'naked' 도메인 이름으로 Traffic Manager를 사용할 수 있나요?

아니요. DNS 표준은 CNAME이 동일한 이름의 다른 DNS 레코드와 함께 존재하는 것을 허용하지 않습니다. DNS 영역의 apex(또는 루트)는 항상 SOA 및 권한이 있는 NS 레코드라는 두 개의 기존 DNS 레코드를 포함합니다. 즉, DNS 표준을 위반하지 않고 영역 apex에 CNAME 레코드를 만들 수 없습니다.

Traffic Manager는 베니티 DNS 이름을 매핑하는 데 DNS CNAME 레코드가 필요합니다. 예를 들어 www.contoso.com을 Traffic Manager 프로필 DNS 이름 contoso.trafficmanager.net에 매핑합니다. 또한 Traffic Manager 프로필은 클라이언트가 연결해야 하는 끝점을 나타내는 보조 DNS CNAME을 반환합니다.

이 문제를 해결하려면 naked 도메인 이름에서 다른 URL로 트래픽을 연결하는 HTTP 리디렉션을 사용하는 것이 좋습니다. 그런 다음 Traffic Manager를 사용할 수 있습니다. 예를 들어 naked 도메인 'contoso.com'에서는 사용자를 Traffic Manager DNS 이름을 가리키는 'www.contoso.com'으로 리디렉션할 수 있습니다.

트래픽 관리자에서 naked 도메인에 대한 전체 지원은 기능 백로그에서 추적됩니다. [커뮤니티 피드백 사이트에서 투표](https://feedback.azure.com/forums/217313-networking/suggestions/5485350-support-apex-naked-domains-more-seamlessly)하여 이 기능 요청에 지원을 등록할 수 있습니다.

### <a name="does-traffic-manager-consider-the-client-subnet-address-when-handling-dns-queries"></a>DNS 쿼리를 처리할 때 Traffic Manager는 클라이언트 서브넷 주소를 고려하나요? 
아니요. 현재 Traffic Manager는 지리적 및 성능 라우팅 방법에 대해 조회를 수행할 때 대부분의 경우 DNS 확인자의 IP 주소에 해당하는 수신된 DNS 쿼리의 원본 IP 주소만 고려합니다.  
특히 지원하는 확인자의 클라이언트 서브넷 주소를 DNS 서버로 전달할 수 있는 [EDNS0(DNS에 대한 확장 메커니즘)](https://tools.ietf.org/html/rfc2671)을 제공하는 [RFC 7871 – DNS 쿼리의 클라이언트 서브넷](https://tools.ietf.org/html/rfc7871)은 현재 Traffic Manager에서 지원되지 않습니다. [커뮤니티 피드백 사이트](https://feedback.azure.com/forums/217313-networking)를 통해 이 기능 요청에 대한 지원을 받도록 등록할 수 있습니다.


## <a name="traffic-manager-geographic-traffic-routing-method"></a>Traffic Manager 지리적 트래픽 라우팅 방법

### <a name="what-are-some-use-cases-where-geographic-routing-is-useful"></a>지리적 라우팅이 유용한 사용 사례에는 어떤 것이 있습니까? 
지리적 라우팅 형식은 Azure 고객이 지리적 지역에 따라 사용자를 구분해야 하는 시나리오에서 사용할 수 있습니다. 예를 들어 지리적 트래픽 라우팅 방법을 사용하게 되면 특정 지역의 사용자에게 다른 지역의 사용자와는 다른 사용자 환경을 제공할 수 있습니다. 또 다른 예는 특정 지역의 해당 사용자들이 해당 지역의 끝점에서만 제공받도록 하는 로컬 데이터 독립성 지침을 준수하는 것입니다.

### <a name="what-are-the-regions-that-are-supported-by-traffic-manager-for-geographic-routing"></a>지리적 라우팅에 대해 Traffic Manager에서 지원되는 지역은 어디입니까? 
Traffic Manager에서 사용되는 국가/지역 계층 구조는 [여기](traffic-manager-geographic-regions.md)에서 확인할 수 있습니다. 이 페이지가 변경 내용으로 최신 상태를 유지하는 동안 [Azure Traffic Manager REST API](https://docs.microsoft.com/rest/api/trafficmanager/)를 사용하여 프로그래밍 방식으로 동일한 정보를 검색할 수 있습니다. 

### <a name="how-does-traffic-manager-determine-where-a-user-is-querying-from"></a>Traffic Manager는 어떻게 사용자가 쿼리하는 위치를 결정합니까? 
Traffic Manager는 쿼리의 원본 IP를 찾고(대개 사용자 대신 쿼리를 수행하는 로컬 DNS 확인자일 수 있음) 지역 맵에 대한 내부 IP를 사용하여 위치를 결정합니다. 이 맵은 인터넷에서 변화를 확인하기 위해 정기적으로 업데이트됩니다. 

### <a name="is-it-guaranteed-that-traffic-manager-will-correctly-determine-the-exact-geographic-location-of-the-user-in-every-case"></a>Traffic Manager가 모든 경우에 사용자의 정확한 지리적 위치를 올바르게 결정한다고 보장할 수 있나요?
아니요. 다음과 같은 이유로 Traffic Manager에서는 DNS 쿼리의 원본 IP 주소에서 유추되는 지리적 하위 지역이 사용자 위치에 항상 올바르게 응답한다고는 보장할 수 없습니다. 

- 첫째, 이전 FAQ에 설명된 것처럼 보고 있는 원본 IP 주소는 DNS 확인자가 사용자를 대신해서 조회를 수행하는 주소입니다. DNS 확인자의 지리적 위치는 사용자의 지리적 위치에 대한 적절한 프록시이지만, 해당 DNS 확인자 서비스의 사용 공간 및 고객이 사용하도록 선택한 특정 DNS 확인자 서비스에 따라 다를 수 있습니다. 예를 들어 말레이시아에 있는 고객은 싱가포르에 있는 해당 DNS 서버가 해당 사용자/장치에 대한 쿼리 확인을 처리하도록 선택되었을 수 있는 DNS 확인자 서비스를 사용하도록 장치 설정에 지정할 수 있습니다. 이 경우 Traffic Manager에는 싱가포르 위치에 해당하는 확인자의 IP 주소만 표시됩니다. 이 페이지에서 클라이언트 서브넷 주소 지원과 관련된 이전 FAQ도 참조하세요.

- 둘째, Traffic Manager는 내부 맵을 사용하여 IP 주소-지리적 하위 지역 변환을 수행합니다. 이 맵은 정확도를 높이고 인터넷의 변화하는 특성을 고려하기 위해 지속적으로 유효성이 검사되고 업데이트되지만, 제공하는 정보가 모든 IP 주소의 지리적 위치를 정확히 나타내지 않을 수도 있습니다.


###  <a name="does-an-endpoint-need-to-be-physically-located-in-the-same-region-as-the-one-it-is-configured-with-for-geographic-routing"></a>끝점을 지리적 라우팅에 대해 구성된 것과 물리적으로 동일한 지역에 배치해야 합니까? 
아니요, 끝점의 위치는 지역이 매핑될 수 있는 곳에 대한 제한이 없습니다. 예를 들어 인도에 있는 모든 사용자는 미국 중부 Azure 지역에 있는 끝점으로 향할 수 있습니다.

### <a name="can-i-assign-geographic-regions-to-endpoints-in-a-profile-that-is-not-configured-to-do-geographic-routing"></a>지리적 라우팅을 수행하도록 구성되어 있지 않은 프로필의 끝점에 지리적 지역을 할당할 수 있습니까? 
예. 프로필의 라우팅 방법이 지리적이 아닌 경우 [Azure Traffic Manager REST API](https://docs.microsoft.com/rest/api/trafficmanager/)를 사용하여 지리적 지역을 해당 프로필의 끝점에 할당할 수 있습니다. 비지리적 라우팅 형식 프로필의 경우 이 구성은 무시됩니다. 이러한 프로필을 나중에 지리적 라우팅 형식으로 변경하면 Traffic Manager는 그러한 매핑을 사용하게 됩니다.


### <a name="when-i-try-to-change-the-routing-method-of-an-existing-profile-to-geographic-i-am-getting-an-error"></a>기존 프로필의 라우팅 방법을 지리적으로 변경하려고 시도하면 오류가 발생합니다.
지리적 라우팅을 사용하는 프로필의 모든 끝점에는 매핑되어 있는 지역이 하나 이상 있어야 합니다. 기존 프로필을 지리적 라우팅 형식으로 변환하려면 라우팅 형식을 지리적으로 변경하기 전에 먼저 [Azure Traffic Manager REST API](https://docs.microsoft.com/rest/api/trafficmanager/)를 사용하여 지리적 위치를 모든 해당 끝점에 연결해야 합니다. 포털을 사용하는 경우 먼저 끝점을 삭제하고, 프로필의 라우팅 방법을 지리적으로 변경한 다음, 지리적 지역 매핑과 함께 끝점을 추가해야 합니다. 


###  <a name="why-is-it-strongly-recommended-that-customers-create-nested-profiles-instead-of-endpoints-under-a-profile-with-geographic-routing-enabled"></a>고객에게 지리적 라우팅이 활성화된 프로필의 끝점보다 중첩 프로필을 만드는 것을 권장하는 이유는 무엇입니까? 
지리적 라우팅 형식을 사용하는 경우 프로필 내 하나의 끝점에만 지역을 할당할 수 있습니다. 해당 끝점이 연결된 자식 프로필을 갖는 중첩 형식이 아닌 경우, 해당 끝점의 이벤트가 비정상 상태이면 Traffic Manager는 트래픽을 계속 전송합니다. 트래픽을 보내지 않는다고 더 나을 것이 없기 때문입니다. 할당된 지역이 비정상 상태가 된 끝점에 할당된 지역의 "부모"인 경우라도 Traffic Manager는 다른 끝점으로 장애 조치하지 않습니다. (예로 스페인 지역을 갖는 끝점이 비정상 상태인 경우 유럽 지역이 할당된 다른 끝점으로 장애 조치하지 않습니다.) 이는 고객이 그들의 프로필에서 설정한 지리적 경계를 Traffic Manager가 존중하도록 하기 위함입니다. 끝점이 비정상이 될 때 다른 끝점으로 장애 조치하는 것의 이점을 얻으려면 지리적 지역을 개별 끝점 대신에 프로필 내 여러 끝점을 사용하는 중첩 프로필로 할당하는 것이 좋습니다. 이 방식에서 중첩 자식 프로필의 끝점이 실패하면 트래픽은 동일한 중첩 자식 프로필 내부의 다른 끝점으로 장애 조치할 수 있습니다.

### <a name="are-there-any-restrictions-on-the-api-version-that-supports-this-routing-type"></a>이 라우팅 형식을 지원하는 API 버전에 제한이 있습니까?

예. API 버전 2017-03-01 이상만 지리적 라우팅 형식을 지원합니다. 이전 API 버전은 지리적 라우팅 형식의 프로필을 생성하거나 지리적 지역을 끝점으로 할당하는 데 사용할 수 없습니다. 이전 API 버전을 사용하여 Azure 구독에서 프로필을 검색한 경우 지리적 라우팅 형식의 프로필은 반환되지 않습니다. 또한 이전 API 버전을 사용하는 경우, 지리적 지역 할당을 사용하는 끝점이 있는 반환된 모든 프로필은 지리적 지역 할당이 나타나지 않습니다.



## <a name="traffic-manager-endpoints"></a>Traffic Manager 끝점

### <a name="can-i-use-traffic-manager-with-endpoints-from-multiple-subscriptions"></a>여러 구독에서 끝점으로 Traffic Manager를 사용할 수 있습니까?

Azure Web Apps의 경우 여러 구독에서 끝점을 사용하는 것은 가능하지 않습니다. Azure Web Apps은 Web Apps와 함께 사용되는 모든 사용자 지정 도메인 이름을 단일 구독 내에서만 사용할 것을 요구합니다. 여러 구독에서 동일한 도메인 이름을 가지고 Web Apps를 사용하는 것은 불가능합니다.

다른 끝점 유형의 경우 Traffic Manager를 둘 이상의 구독에서 끝점과 함께 사용할 수 있습니다. Traffic Manager를 구성하는 방법은 클래식 배포 모델 또는 리소스 관리자 환경을 사용하는지에 따라 달라집니다.

* 리소스 관리자에서 모든 구독의 끝점은 Traffic Manager 프로필을 구성하는 사용자가 끝점에 대한 읽기 액세스가 있는 한 Traffic Manager에 추가될 수 있습니다. 이러한 권한은 [Azure Resource Manager 역할 기반 액세스 제어(RBAC)](../active-directory/role-based-access-control-configure.md)를 사용하여 부여될 수 있습니다.
* 클래식 배포 모델 인터페이스에서 Traffic Manager는 Traffic Manager 프로필과 동일한 구독에 있는 Azure 끝점으로 구성된 Cloud Services 또는 Web Apps가 필요합니다. 다른 구독의 클라우드 서비스 끝점은 '외부' 끝점으로 Traffic Manager에 추가될 수 있습니다. 이러한 외부 끝점은 외부 비율이 아닌 Azure 끝점 비율로 청구됩니다.

### <a name="can-i-use-traffic-manager-with-cloud-service-staging-slots"></a>클라우드 서비스 '스테이징' 슬롯으로 Traffic Manager를 사용할 수 있습니까?

예. 클라우드 서비스 '스테이징' 슬롯은 외부 끝점으로 Traffic Manager에서 구성될 수 있습니다. 상태 검사는 여전히 Azure 끝점 비율로 요금이 청구됩니다. 외부 끝점 유형을 사용 중이므로 기본 서비스에 대한 변경 내용은 자동으로 선택되지 않습니다. 외부 끝점인 경우 Traffic Manager는 클라우드 서비스가 중지되거나 삭제될 때 감지할 수 없습니다. 따라서 Traffic Manager는 끝점이 비활성화되거나 삭제될 때까지 상태 검사에 대한 요금 청구를 계속합니다.

### <a name="does-traffic-manager-support-ipv6-endpoints"></a>Traffic Manager는 IPv6 끝점을 지원하나요?

Traffic Manager는 현재 IPv6으로 주소 지정이 가능한 이름 서버를 제공하지 않습니다. 하지만 IPv6 클라이언트를 IPv6 끝점에 연결하여 Traffic Manager를 여전히 사용할 수 있습니다. 클라이언트는 Traffic Manager에 직접 DNS 요청을 만들지 않습니다. 대신 클라이언트는 재귀 DNS 서비스를 사용합니다. IPv6 전용 클라이언트는 IPv6를 통해 재귀 DNS 서비스에 요청을 보냅니다. 그런 다음 재귀 서비스는 IPv4를 사용하여 Traffic Manager 이름 서버에 연결할 수 있어야 합니다.

Traffic Manager는 끝점의 DNS 이름으로 응답합니다. IPv6 끝점을 지원하기 위해 끝점 DNS 이름이 IPv6 주소를 가리키게 하는 DNS AAAA 레코드가 존재해야 합니다. Traffic Manager 상태 검사는 IPv4 주소만 지원합니다. 서비스는 IPv4 끝점을 동일한 DNS 이름에 노출해야 합니다.

### <a name="can-i-use-traffic-manager-with-more-than-one-web-app-in-the-same-region"></a>동일한 지역에서 둘 이상의 웹앱에 Traffic Manager를 사용할 수 있습니까?

일반적으로 Traffic Manager는 다른 지역에 배포된 응용 프로그램에 트래픽을 보내는 데 사용됩니다. 그러나 응용 프로그램이 동일한 지역에 둘 이상의 배포를 가지는 곳에 사용될 수도 있습니다. Traffic Manager Azure 끝점은 동일한 Traffic Manager 프로필에 추가되는 동일한 Azure 지역에서 둘 이상의 웹앱 끝점을 허용하지 않습니다.

다음 단계는 이 제약 조건에 대한 해결 방법을 제공합니다.

1. 끝점이 다른 웹앱 '배율 단위'에 있는지 확인합니다. 도메인 이름은 지정된 배율 단위의 단일 사이트로 매핑되어야 합니다. 따라서 동일한 배율 단위의 두 Web Apps는 Traffic Manager 프로필을 공유할 수 없습니다.
2. 각 웹앱에 사용자 지정 호스트 이름으로 베니티 도메인 이름을 추가합니다. 각 웹앱은 다른 배율 단위에 있어야 합니다. 모든 Web Apps는 동일한 구독에 속해야 합니다.
3. 하나(및 하나만)의 웹앱 끝점을 Azure 끝점으로 Traffic Manager 프로필에 추가합니다.
4. 외부 끝점으로 Traffic Manager 프로필에 각 추가 웹앱 끝점을 추가합니다. 외부 끝점은 리소스 관리자 배포 모델을 사용해서만 추가할 수 있습니다.
5. Traffic Manager 프로필 DNS 이름(<…>.trafficmanager.net)을 가리키는 베니티 도메인에 DNS CNAME 레코드를 만듭니다.
6. Traffic Manager 프로필 DNS 이름이 아닌 베니티 도메인 이름을 통해 사이트에 액세스합니다.

##  <a name="traffic-manager-endpoint-monitoring"></a>Traffic Manager 끝점 모니터링

### <a name="is-traffic-manager-resilient-to-azure-region-failures"></a>Traffic Manager 자체는 Azure 하위 지역 장애에 복원력이 있나요?

Traffic Manager는 Azure에서 가용성 높은 응용 프로그램을 전달하는 데 있어서 핵심적인 구성 요소입니다.
높은 가용성을 제공하려면 Traffic Manager는 매우 높은 수준의 가용성을 유지해야 하고 지역 실패에 대해 복원력도 뛰어나야 합니다.

기본적으로 Traffic Manager 구성 요소는 모든 Azure 지역의 전체 오류에 대해 복원력을 갖습니다. 이러한 복원력은 DNS 이름 서버, API, 저장소 계층 및 끝점 모니터링 서비스 등 모든 Traffic Manager 구성 요소에 해당됩니다.

전체 Azure 하위 지역의 가동 중단과 같은 드문 경우에도, Traffic Manager는 계속 정상적으로 작동해야 합니다. 여러 Azure 하위 지역에 배포된 응용 프로그램은 Traffic Manager에 의존하여 응용 프로그램의 사용 가능한 인스턴스로 트래픽을 전달할 수 있습니다.

### <a name="how-does-the-choice-of-resource-group-location-affect-traffic-manager"></a>리소스 그룹의 위치 선택은 Traffic Manager에 어떤 영향을 주나요?

Traffic Manager는 단일 전역 서비스입니다. 지역에 영향을 받지 않습니다. 리소스 그룹의 위치 선택은 해당 리소스 그룹에 배포된 Traffic Manager 프로필에 어떤 영향도 주지 않습니다.

Azure Resource Manager에서는 모든 리소스 그룹에서 '위치'를 지정해야 하며 이를 통해 해당 리소스 그룹에 배포된 리소스의 기본 위치가 결정됩니다. Traffic Manager 프로필을 만들면 리소스 그룹에 만들어집니다. 모든 Traffic Manager 프로필은 위치로 **전역**을 사용하고 리소스 그룹 기본값을 재정의합니다.

### <a name="how-do-i-determine-the-current-health-of-each-endpoint"></a>각 끝점의 현재 상태를 확인하려면 어떻게 해야 하나요?

각 끝점의 현재 상태 모니터링 상태 및 전체 프로필은 Azure 포털에 표시됩니다. 이 정보는 Traffic Manager [REST API](https://msdn.microsoft.com/library/azure/mt163667.aspx), [PowerShell cmdlets](https://msdn.microsoft.com/library/mt125941.aspx) 및 [크로스 플랫폼 Azure CLI](../cli-install-nodejs.md)를 통해서도 사용할 수 있습니다.

Azure는 지난 끝점 상태에 대한 기록 정보를 제공하지 않으며 끝점 상태 변경에 대한 경고를 생성하는 기능도 없습니다.

### <a name="can-i-monitor-https-endpoints"></a>HTTPS 끝점을 모니터링할 수 있나요?

예. Traffic Manager는 HTTPS를 통한 검색을 지원합니다. 모니터링 구성에서 프로토콜로 **HTTPS**를 구성합니다.

Traffic Manager는 다음을 포함하는 인증서 유효성 검사를 제공할 수 없습니다.

* 서버 쪽 인증서의 유효성이 검사되지 않습니다.
* SNI 서버 쪽 인증서가 지원되지 않습니다.
* 클라이언트 인증서는 지원되지 않습니다.

### <a name="what-host-header-do-endpoint-health-checks-use"></a>끝점 상태 검사에 어떤 호스트 헤더가 사용되나요?

Traffic Manager는 HTTP 및 HTTPS 상태 검사에서 호스트 헤더를 사용합니다. Traffic Manager가 사용하는 호스트 헤더는 프로필에 구성된 끝점 대상의 이름입니다. 호스트 헤더에 사용된 값은 target 속성과 별도로 지정할 수 없습니다.

### <a name="what-are-the-ip-addresses-from-which-the-health-checks-originate"></a>상태 검사가 시작되는 IP 주소는 무엇인가요?

다음 목록에는 Traffic Manager 상태 검사가 시작될 수 있는 IP 주소가 나와 있습니다. 이 목록의 IP 주소에서 들어오는 연결의 끝점에서는 상태 검사가 허용됩니다.

* 40.68.30.66
* 40.68.31.178
* 137.135.80.149
* 137.135.82.249
* 23.96.236.252
* 65.52.217.19
* 40.87.147.10
* 40.87.151.34
* 13.75.124.254
* 13.75.127.63
* 52.172.155.168
* 52.172.158.37
* 104.215.91.84
* 13.75.153.124
* 13.84.222.37
* 23.101.191.199
* 23.96.213.12
* 137.135.46.163
* 137.135.47.215
* 191.232.208.52
* 191.232.214.62
* 13.75.152.253
* 104.41.187.209
* 104.41.190.203

## <a name="traffic-manager-nested-profiles"></a>Traffic Manager 중첩 프로필

### <a name="how-do-i-configure-nested-profiles"></a>중첩 프로필을 구성하려면 어떻게 해야 하나요?

중첩 Traffic Manager 프로필은 Azure Resource Manager 및 클래식 Azure REST API, Azure PowerShell cmdlet, 플랫폼 간 Azure CLI 명령을 사용하여 구성할 수 있습니다. 새 Azure Portal을 통해서도 지원됩니다. 클래식 포털을 통해서는 지원되지 않습니다.

### <a name="how-many-layers-of-nesting-does-traffic-manger-support"></a>트래픽 관리자가 지원하는 중첩 계층 수는 얼마나 되나요?

프로필을 10개 수준 깊이까지 중첩할 수 있습니다. '루프'는 허용되지 않습니다.

### <a name="can-i-mix-other-endpoint-types-with-nested-child-profiles-in-the-same-traffic-manager-profile"></a>동일한 트래픽 관리자 프로필에서 중첩 자식 프로필과 다른 끝점 유형을 혼합할 수 있나요?

예. 프로필 내에서 서로 다른 유형의 끝점을 결합하는 방법에는 제한이 없습니다.

### <a name="how-does-the-billing-model-apply-for-nested-profiles"></a>중첩 프로필에는 요금 청구 모델이 어떻게 적용되나요?

중첩 프로필을 사용한다고 해서 가격이 더 부과되지는 않습니다.

Traffic Manager 요금 청구는 끝점 상태 검사 및 수백만 개의 DNS 쿼리로 구성됩니다.

* 끝점 상태 검사: 부모 프로필에서 끝점으로 구성된 자식 프로필에 대해서는 요금이 부과되지 않습니다. 하위 프로필에서 끝점 모니터링은 일반적인 방식으로 청구됩니다.
* DNS 쿼리: 각 쿼리는 한 번만 계산됩니다. 하위 프로필의 끝점을 반환하는 상위 프로필에 대한 쿼리는 상위 프로필에 대해서만 계산됩니다.

자세한 내용은 [Traffic Manager 가격 책정 페이지](https://azure.microsoft.com/pricing/details/traffic-manager/)를 참조하세요.

### <a name="is-there-a-performance-impact-for-nested-profiles"></a>중첩 프로필이 성능에 영향을 미치나요?

아니요. 중첩 프로필을 사용해도 성능에 미치는 영향은 없습니다.

Traffic Manager 이름 서버는 각 DNS 쿼리를 처리하는 경우 프로필 계층 구조를 내부적으로 통과합니다. 상위 프로필에 대한 DNS 쿼리는 하위 프로필의 끝점에 대한 DNS 응답을 받을 수 있습니다. 단일 CNAME 레코드는 단일 프로필이나 중첩 프로필을 사용하는 경우 사용합니다. 계층 구조에서 각 프로필에 대한 CNAME 레코드를 만들 필요가 없습니다.

### <a name="how-does-traffic-manager-compute-the-health-of-a-nested-endpoint-in-a-parent-profile"></a>Traffic Manager에서 상위 프로필의 중첩 끝점 상태를 계산하려면 어떻게 하나요?

상위 프로필에서는 하위 프로필에 대한 상태 검사를 직접 수행하지 않습니다. 대신, 하위 프로필 끝점의 상태는 하위 프로필의 전반적인 상태를 계산하는 데 사용됩니다. 이 정보는 중첩 프로필 계층 구조로 전파되어 중첩 끝점의 상태를 확인합니다. 상위 프로필은 이 집계된 상태를 사용하여 하위 프로필에 트래픽을 보낼 수 있을지 결정합니다.

다음 테이블에서는 중첩 끝점에 대한 Traffic Manager의 상태 검사 동작에 대해 설명합니다.

| 자식 프로필 모니터 상태 | 부모 끝점 모니터 상태 | 참고 사항 |
| --- | --- | --- |
| Disabled. 하위 프로필을 사용하지 않도록 설정했습니다. |중지됨 |부모 끝점 상태는 Stopped이며 Disabled가 아닙니다. Disabled 상태는 부모 프로필에서 끝점을 사용할 수 없도록 설정했음을 표시하도록 예약되어 있습니다. |
| Degraded. 하나 이상의 자식 프로필 끝점이 Degraded 상태입니다. |Online: 자식 프로필의 Online 끝점 수가 MinChildEndpoints 값 이상입니다.<BR>CheckingEndpoint: 자식 프로필의 Online 및 CheckingEndpoint 끝점 수 합계가 MinChildEndpoints 값 이상입니다.<BR>Degraded: 그렇지 않은 경우 |트래픽이 CheckingEndpoint 상태의 끝점으로 라우팅됩니다. MinChildEndpoints를 너무 높게 설정하는 경우 끝점의 성능이 항상 저하됩니다. |
| Online. 하나 이상의 하위 프로필 끝점이 온라인 상태입니다. 성능이 저하된 끝점이 없습니다. |위 내용을 참조하세요. | |
| CheckingEndpoints. 하나 이상의 하위 프로필 끝점이 'CheckingEndpoint'입니다. '온라인' 또는 '성능이 저하'된 끝점이 없습니다. |위와 동일합니다. | |
| Inactive. 모든 하위 프로필 끝점이 사용 안 함 또는 중지됨이거나 끝점이 없는 프로필입니다. |중지됨 | |

## <a name="next-steps"></a>다음 단계:
- 트래픽 관리자 [끝점 모니터링 및 자동 장애 조치(failover)](../traffic-manager/traffic-manager-monitoring.md)에 대해 자세히 알아봅니다.
- 트래픽 관리자 [트래픽 라우팅 방법](../traffic-manager/traffic-manager-routing-methods.md)에 대해 자세히 알아봅니다.
