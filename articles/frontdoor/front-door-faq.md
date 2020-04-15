---
title: Azure 정문 - 자주 묻는 질문
description: 이 페이지에서는 Azure 정문에 대한 자주 묻는 질문에 대한 답변을 제공합니다.
services: frontdoor
documentationcenter: ''
author: sohamnchatterjee
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/13/2020
ms.author: sohamnc
ms.openlocfilehash: e2785baab27f5bfc996b57607816062195a19b2b
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/14/2020
ms.locfileid: "81313764"
---
# <a name="frequently-asked-questions-for-azure-front-door"></a>Azure 정문에 자주 묻는 질문

이 문서에서는 Azure 정문 기능 및 기능에 대한 일반적인 질문에 대한 답변을 제공합니다. 질문에 대한 답을 찾지 못한 경우 다음 채널을 통해 (제시된 채널 순서에 따라) 문의할 수 있습니다.

1. 이 문서의 의견 섹션입니다.
2. [Azure 정문 사용자 음성](https://feedback.azure.com/forums/217313-networking?category_id=345025).
3. **마이크로 소프트 지원 :** Azure 포털에서 **도움말** 탭에서 새 지원 요청을 만들려면 **도움말 + 지원** 단추를 선택한 다음 새 **지원 요청을**선택합니다.

## <a name="general"></a>일반

### <a name="what-is-azure-front-door"></a>Azure Front Door란?

Azure Front Door는 서비스로 응용 프로그램 배달 네트워크(ADN)로 응용 프로그램에 다양한 계층 7 로드 밸런싱 기능을 제공합니다. 거의 실시간 장애 조치와 함께 전역 부하 분산과 함께 동적 사이트 가속(DSA)을 제공합니다. Azure에서 완전히 관리되는 가용성과 확장 성이 뛰어난 서비스입니다.

### <a name="what-features-does-azure-front-door-support"></a>Azure 정문에서 지원하는 기능은 무엇입니까?

Azure Front Door는 동적 사이트 가속(DSA), TLS/SSL 오프로딩 및 엔드 투 엔드 TLS, 웹 응용 프로그램 방화벽, 쿠키 기반 세션 선호도, URL 경로 기반 라우팅, 무료 인증서 및 여러 도메인 관리 등을 지원합니다. 지원되는 기능의 전체 목록은 [Azure 정문 개요를](front-door-overview.md)참조하십시오.

### <a name="what-is-the-difference-between-azure-front-door-and-azure-application-gateway"></a>Azure 정문과 Azure 응용 프로그램 게이트웨이의 차이점은 무엇입니까?

정문 게이트웨이와 애플리케이션 게이트웨이는 모두 계층 7(HTTP/HTTPS) 로드 밸런서이지만, 주요 차이점은 Front Door가 글로벌 서비스인 반면 응용 프로그램 게이트웨이는 지역 서비스라는 점입니다. Front Door는 여러 영역의 다른 스케일 단위/클러스터/스탬프 단위 간에 로드 할 수 있지만 애플리케이션 게이트웨이를 사용하면 스케일 단위 내에 있는 VM/컨테이너 등 간에 저울을 로드할 수 있습니다.

### <a name="when-should-we-deploy-an-application-gateway-behind-front-door"></a>언제 정문 뒤에 애플리케이션 게이트웨이를 배포해야 합니까?

정문 뒤에 응용 프로그램 게이트웨이를 사용해야 하는 주요 시나리오는 다음과 같습니다.

- Front Door는 전역 수준에서만 경로 기반 부하 분산을 수행할 수 있지만 가상 네트워크(VNET) 내에서 트래픽 균형을 더 로드하려는 경우 응용 프로그램 게이트웨이를 사용해야 합니다.
- 전면 문은 VM/컨테이너 수준에서 작동하지 않으므로 연결 드레인을 수행할 수 없습니다. 그러나 응용 프로그램 게이트웨이를 사용하면 연결 드레인을 수행할 수 있습니다. 
- AFD 뒤에 있는 애플리케이션 게이트웨이를 사용하면 100% TLS/SSL 오프로드를 달성하고 가상 네트워크(VNET) 내에서 HTTP 요청만 라우팅할 수 있습니다.
- 전면 도어 및 애플리케이션 게이트웨이 는 모두 세션 선호도를 지원합니다. Front Door는 사용자 세션에서 특정 지역의 동일한 클러스터 또는 백 엔드로 후속 트래픽을 지시할 수 있지만 응용 프로그램 게이트웨이는 트래픽을 클러스터 내의 동일한 서버로 직접 연결할 수 있습니다.  

### <a name="can-we-deploy-azure-load-balancer-behind-front-door"></a>Azure 로드 밸러커를 정문 뒤에 배포할 수 있습니까?

Azure 정문은 트래픽을 라우팅하기 위해 공용 VIP 또는 공개적으로 사용 가능한 DNS 이름이 필요합니다. 전면 도어 뒤에 Azure 로드 밸런서를 배포하는 것이 일반적인 사용 사례입니다.

### <a name="what-protocols-does-azure-front-door-support"></a>Azure 정문에서 지원하는 프로토콜은 무엇입니까?

Azure 정문이 HTTP, HTTPS 및 HTTP/2를 지원합니다.

### <a name="how-does-azure-front-door-support-http2"></a>Azure 정문은 HTTP/2를 어떻게 지원합니까?

HTTP/2 프로토콜 지원은 Azure 정문에만 연결하는 클라이언트에서 사용할 수 있습니다. 백 엔드 풀의 백엔드에 대한 통신은 HTTP/1.1이상입니다. HTTP/2 지원은 기본적으로 활성화되어 있습니다.

### <a name="what-resources-are-supported-today-as-part-of-backend-pool"></a>현재 백 엔드 풀의 일부로 어떤 리소스가 지원되나요?

백 엔드 풀은 저장소, 웹 앱, Kubernetes 인스턴스 또는 공용 연결이 있는 다른 사용자 지정 호스트 이름으로 구성될 수 있습니다. Azure Front Door에서는 공용 IP 또는 공개적으로 해결할 수 있는 DNS 호스트 이름을 통해 백엔드를 정의해야 합니다. 백 엔드 풀의 구성원은 공용 연결이 있는 한 영역, 지역 또는 Azure 외부에 있을 수 있습니다.

### <a name="what-regions-is-the-service-available-in"></a>어떤 지역에서 서비스를 사용할 수 있습니까?

Azure 정문은 전역 서비스이며 특정 Azure 지역에 연결되지 않습니다. Front Door를 만드는 동안 지정해야 하는 유일한 위치는 리소스 그룹 위치이며, 기본적으로 리소스 그룹의 메타데이터가 저장될 위치를 지정합니다. Front Door 리소스 자체는 전역 리소스로 만들어지며 구성은 모든 POP(현재 지점)에 전역적으로 배포됩니다. 

### <a name="what-are-the-pop-locations-for-azure-front-door"></a>Azure 정문에 대한 POP 위치는 무엇입니까?

Azure 정문은 Microsoft의 Azure CDN과 동일한 POP(현재 상태 지점) 위치 목록을 가지고 있습니다. POP의 전체 목록은 [Microsoft의 Azure CDN POP 위치를](https://docs.microsoft.com/azure/cdn/cdn-pop-locations)참조하십시오.

### <a name="is-azure-front-door-a-dedicated-deployment-for-my-application-or-is-it-shared-across-customers"></a>Azure 정문이 내 응용 프로그램에 대한 전용 배포입니까 아니면 고객 간에 공유되고 있습니까?

Azure 정문은 전역적으로 분산된 다중 테넌트 서비스입니다. 따라서 Front Door의 인프라는 모든 고객 간에 공유됩니다. 그러나 정문 프로파일을 작성하여 애플리케이션에 필요한 특정 구성을 정의하고 정도어를 변경하지 않으면 다른 정문 구성에 영향을 미치지 않습니다.

### <a name="is-http-https-redirection-supported"></a>HTTP->HTTPS 리디렉션이 지원되나요?

예. 실제로 Azure Front Door는 호스트, 경로 및 쿼리 문자열 리디렉션과 URL 리디렉션의 일부를 지원합니다. [URL 리디렉션에](front-door-url-redirect.md)대해 자세히 알아보십시오. 

### <a name="in-what-order-are-routing-rules-processed"></a>라우팅 규칙은 어떤 순서로 처리됩니까?

정문에 대한 경로는 정렬되지 않으며 특정 경로는 일치하는 일치에 따라 선택됩니다. [정문에서 요청을 라우팅 규칙과 일치하는 방법에](front-door-route-matching.md)대해 자세히 알아봅니다.

### <a name="how-do-i-lock-down-the-access-to-my-backend-to-only-azure-front-door"></a>내 백 엔드에 대한 액세스를 Azure 정문으로만 잠그려면 어떻게 해야 합니까?

특정 정문에서만 트래픽을 허용하도록 응용 프로그램을 잠그려면 백 엔드에 대한 IP ACL을 설정한 다음 백 엔드의 트래픽을 정문에서 보낸 헤더 'X-Azure-FDID'의 특정 값으로 제한해야 합니다. 이 단계는 다음과 같이 자세히 설명되어 있습니다.

- Azure Front Door의 백 엔드 IP 주소 공간 및 Azure의 인프라 서비스에서만 트래픽을 허용하도록 백 엔드에 대한 IP ACLing을 구성합니다. 백 엔드를 ACLing에 대 한 아래 IP 세부 정보를 참조 하십시오.
 
    - 전면 도어의 IPv4 백 엔드 IP 주소 범위에 대한 [Azure IP 범위 및 서비스 태그의](https://www.microsoft.com/download/details.aspx?id=56519) *AzureFrontDoor.Backend* 섹션을 참조하거나 [네트워크 보안 그룹](https://docs.microsoft.com/azure/virtual-network/security-overview#security-rules) 또는 Azure [방화벽에서](https://docs.microsoft.com/azure/firewall/service-tags)서비스 태그 *AzureFrontDoor.Backend를* 사용할 수도 있습니다.
    - 서비스 태그에 포함된 경우 Front Door의 **IPv6** 백 엔드 IP 공간은 Azure IP 범위 JSON 파일에 나열되지 않습니다. 명시적 IPv6 주소 범위를 찾고 있는 경우 현재`2a01:111:2050::/44`
    - 가상화된 호스트 IP 주소를 통한 Azure의 `168.63.129.16` 기본 인프라 [서비스:](https://docs.microsoft.com/azure/virtual-network/security-overview#azure-platform-considerations)`169.254.169.254`

    > [!WARNING]
    > Front Door의 백 엔드 IP 공간은 나중에 변경될 수 있지만, 이러한 일이 발생하기 전에 [Azure IP 범위 및 서비스 태그와](https://www.microsoft.com/download/details.aspx?id=56519)통합되도록 할 것입니다. 변경 또는 업데이트에 대해 [Azure IP 범위 및 서비스 태그를](https://www.microsoft.com/download/details.aspx?id=56519) 구독하는 것이 좋습니다.

-    API 버전 `2020-01-01` 이상으로 정문에서 GET 작업을 수행합니다. API 호출에서 `frontdoorID` 필드를 찾습니다. 들어오는 헤더에 필터 '**X-Azure-FDID**' 필드의 `frontdoorID`값으로 백 엔드에 정문으로 전송 . 

### <a name="can-the-anycast-ip-change-over-the-lifetime-of-my-front-door"></a>내 정문 수명 동안 임의의 캐스트 IP가 변경 될 수 있습니까?

프론트 도어의 프런트 엔드 anycast IP는 일반적으로 변경되지 않아야하며 정문 수명 동안 정적 상태로 유지될 수 있습니다. 그러나, 같은 **보장은 없습니다.** IP에 대한 직접적인 종속성을 사용하지 마십시오.

### <a name="does-azure-front-door-support-static-or-dedicated-ips"></a>Azure 정문이 정적 또는 전용 IP를 지원합니까?

아니요, Azure 정문은 현재 정적 또는 전용 프런트 엔드 모든 캐스트 IP를 지원하지 않습니다. 

### <a name="does-azure-front-door-support-x-forwarded-for-headers"></a>Azure 정문이 x-forwarded-for 헤더를 지원합니까?

예. Azure 정문에서는 X-전달용, X 전달 호스트 및 X-전달-프로토 헤더를 지원합니다. X-전달-For 헤더가 이미 있는 경우 Front Door는 클라이언트 소켓 IP를 가산합니다. 그렇지 않으면 클라이언트 소켓 IP가 있는 헤더를 값으로 추가합니다. X-전달 호스트 및 X 전달-프로토의 경우 값이 재정의됩니다.

[정문이 지원되는 HTTP 헤더에](front-door-http-headers-protocol.md)대해 자세히 알아보십시오.  

### <a name="how-long-does-it-take-to-deploy-an-azure-front-door-does-my-front-door-still-work-when-being-updated"></a>Azure 정문을 배포하는 데 얼마나 걸합니까? 업데이트 시 정문이 계속 작동합니까?

새로운 정문 생성 또는 기존 정문 업데이트는 전역 배포에 약 3~5분 정도 걸립니다. 즉, 약 3~5분 안에 정문 구성이 전 세계 모든 POP에 배포됩니다.

참고 - 사용자 지정 TLS/SSL 인증서 업데이트는 전 세계에 배포하는 데 약 30분이 소요됩니다.

경로 또는 백 엔드 풀 등에 대한 업데이트는 원활하며 새 구성이 올바른 경우 가동 중지 시간이 0으로 발생합니다. 인증서 업데이트는 원자성이며 'AFD 관리'에서 '사용자 고유의 인증서 사용'으로 전환하거나 그 반대의 경우도 마찬가지인 경우 중단을 일으키지 않습니다.


## <a name="configuration"></a>구성

### <a name="can-azure-front-door-load-balance-or-route-traffic-within-a-virtual-network"></a>Azure 정문 로드 밸런스를 조정하거나 가상 네트워크 내에서 트래픽을 라우팅할 수 있습니까?

AFD(Azure 정문)에는 트래픽을 라우팅하기 위해 공용 IP 또는 공개적으로 해결할 수 있는 DNS 이름이 필요합니다. 따라서 AFD가 가상 네트워크 내에서 직접 라우팅할 수 없지만 그 사이에 응용 프로그램 게이트웨이 또는 Azure 로드 밸런서를 사용하면 이 시나리오가 해결됩니다.

### <a name="what-are-the-various-timeouts-and-limits-for-azure-front-door"></a>Azure 정문에 대한 다양한 시간 제한 및 제한은 무엇입니까?

Azure 정문 에 대한 문서화된 모든 [시간 제한 및 제한에](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#azure-front-door-service-limits)대해 알아봅니다.

## <a name="performance"></a>성능

### <a name="how-does-azure-front-door-support-high-availability-and-scalability"></a>Azure 정문문은 고가용성과 확장성을 어떻게 지원합니까?

Azure Front Door는 응용 프로그램의 확장성 요구 사항을 충족할 수 있는 방대한 용량의 전 세계적으로 분산된 다중 테넌트 플랫폼입니다. Microsoft의 글로벌 네트워크 의 가장자리에서 제공되는 Front Door는 전체 응용 프로그램 또는 지역 또는 다른 클라우드에서 개별 마이크로 서비스를 장애 조치할 수 있는 글로벌 부하 분산 기능을 제공합니다.

## <a name="tls-configuration"></a>TLS 구성

### <a name="what-tls-versions-are-supported-by-azure-front-door"></a>Azure 정문에서 지원하는 TLS 버전은 무엇입니까?

2019년 9월 이후에 생성된 모든 정문 프로파일은 TLS 1.2를 기본 최소값으로 사용합니다.

프론트 도어는 TLS 버전 1.0, 1.1 및 1.2를 지원합니다. TLS 1.3은 아직 지원되지 않습니다.

### <a name="what-certificates-are-supported-on-azure-front-door"></a>Azure 정문에서 지원되는 인증서는 무엇입니까?

Front Door 사용자 지정 도메인에서 콘텐츠를 안전하게 전달하기 위한 HTTPS 프로토콜을 사용하려면 Azure Front Door에서 관리하는 인증서를 사용하거나 자체 인증서를 사용하도록 선택할 수 있습니다.
프론트 도어 관리 옵션은 Digicert를 통해 표준 TLS/SSL 인증서를 제공하고 프론트 도어의 키 볼트에 보관합니다. 사용자 고유의 인증서를 사용하도록 선택한 경우 지원되는 CA의 인증서를 온보딩할 수 있으며 표준 TLS, 확장 유효성 검사 인증서 또는 와일드카드 인증서가 될 수 있습니다. 자체 서명된 인증서는 지원되지 않습니다. [사용자 지정 도메인에 대해 HTTPS를 사용하도록 설정하는 방법에 대해](https://aka.ms/FrontDoorCustomDomainHTTPS)알아봅니다.

### <a name="does-front-door-support-autorotation-of-certificates"></a>프런트 도어는 인증서의 자동 회전을 지원합니까?

정문 관리 인증서 옵션의 경우 인증서는 정문으로 자동 회전됩니다. Front Door 관리 인증서를 사용하고 있고 인증서 만료 날짜가 60일 미만인 경우 지원 티켓을 제출하십시오.
</br>사용자 지정 TLS/SSL 인증서의 경우 자동 회전이 지원되지 않습니다. 지정된 사용자 지정 도메인에 대해 처음 설정된 방법과 마찬가지로 키 볼트의 올바른 인증서 버전을 정문 으로 가리키고 정문 의 서비스 주체가 키 볼트에 계속 액세스할 수 있는지 확인해야 합니다. Front Door에서 업데이트된 이 인증서 롤아웃 작업은 원자성이며 인증서의 주체 이름이나 SAN이 변경되지 않는 한 프로덕션에 영향을 미치지 않습니다.

### <a name="what-are-the-current-cipher-suites-supported-by-azure-front-door"></a>Azure 정문에서 지원하는 현재 암호 제품군은 무엇입니까?

TLS1.2의 경우 다음 암호 제품군이 지원됩니다.

TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384 TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256 TLS_DHE_RSA_WITH_AES_256_GCM_SHA384 TLS_DHE_RSA_WITH_AES_128_GCM_SHA256

TLS1.0/1.1에서 사용자 지정 도메인을 사용하는 경우 다음 암호 제품군이 지원됩니다.

- TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256
- TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384
- TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256
- TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
- TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256
- TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384
- TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256
- TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384
- TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA
- TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA
- TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA
- TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA
- TLS_RSA_WITH_AES_256_GCM_SHA384
- TLS_RSA_WITH_AES_128_GCM_SHA256
- TLS_RSA_WITH_AES_256_CBC_SHA256
- TLS_RSA_WITH_AES_128_CBC_SHA256
- TLS_RSA_WITH_AES_256_CBC_SHA
- TLS_RSA_WITH_AES_128_CBC_SHA
- TLS_DHE_RSA_WITH_AES_128_GCM_SHA256
- TLS_DHE_RSA_WITH_AES_256_GCM_SHA384

### <a name="can-i-configure-tls-policy-to-control-tls-protocol-versions"></a>TLS 프로토콜 버전을 제어하도록 TLS 정책을 구성할 수 있습니까?

Azure 포털 또는 Azure [REST API를](https://docs.microsoft.com/rest/api/frontdoorservice/frontdoor/frontdoors/createorupdate#minimumtlsversion)통해 사용자 지정 도메인 HTTPS 설정에서 Azure 정문에서 최소 TLS 버전을 구성할 수 있습니다. 현재 1.0과 1.2 중에서 선택할 수 있습니다.

### <a name="can-i-configure-front-door-to-only-support-specific-cipher-suites"></a>특정 암호 제품군만 지원하도록 정문 도어를 구성할 수 있습니까?

아니요, 특정 암호 제품군에 대한 정문 구성은 지원되지 않습니다. 그러나 인증 기관(예: Verisign, Entrust 또는 Digicert)에서 사용자 지정 TLS/SSL 인증서를 받을 수 있으며 인증서를 생성할 때 인증서에 특정 암호 제품군이 표시되어 있습니다. 

### <a name="does-front-door-support-ocsp-stapling"></a>정문이 OCSP 스테이플링을 지원합니까?

예. OCSP 스테이플링은 기본적으로 정문에서 지원되며 구성이 필요하지 않습니다.

### <a name="does-azure-front-door-also-support-re-encryption-of-traffic-to-the-backend"></a>Azure 정문도 백 엔드에 대한 트래픽의 재암호화를 지원합니까?

예. Azure 정문은 TLS/SSL 오프로드를 지원하고 백 엔드로 트래픽을 다시 암호화하는 TLS를 끝까지 지원합니다. 실제로 백 엔드에 대한 연결은 공용 IP를 통해 발생하므로 HTTPS를 전달 프로토콜로 사용하도록 정문을 구성하는 것이 좋습니다.

### <a name="does-front-door-support-self-signed-certificates-on-the-backend-for-https-connection"></a>Https 연결의 백 엔드에서 프런트 도어가 자체 서명된 인증서를 지원합니까?

아니요, 자체 서명된 인증서는 Front Door에서 지원되지 않으며 제한 사항은 둘 다에 적용됩니다.

1. **백엔드**: 트래픽을 HTTPS 또는 HTTPS 상태 프로브로 전달하거나 캐싱을 사용하도록 설정한 라우팅 규칙에 대해 원본에서 캐시를 채울 때 자체 서명된 인증서를 사용할 수 없습니다.
2. **Frontend**: 사용자 지정 도메인에서 HTTPS를 사용하도록 설정하기 위해 사용자 지정 TLS/SSL 인증서를 사용할 때는 자체 서명된 인증서를 사용할 수 없습니다.

### <a name="why-is-https-traffic-to-my-backend-failing"></a>백 엔드에 대한 HTTPS 트래픽이 실패하는 이유는 무엇입니까?

상태 프로브또는 전달 요청에 대한 백 엔드에 대한 HTTPS 연결을 성공적으로 수행하려면 HTTPS 트래픽이 실패할 수 있는 두 가지 이유가 있을 수 있습니다.

1. **인증서 주체 이름 불일치**: HTTPS 연결의 경우 Front Door는 백 엔드가 백 엔드 호스트 이름과 일치하는 제목 이름으로 유효한 CA의 인증서를 제공할 것으로 예상합니다. 예를 들어 백 엔드 호스트 이름이 설정되어 `myapp-centralus.contosonews.net` 있고 TLS 핸드셰이크 중에 백 엔드가 `myapp-centralus.contosonews.net` 제공하는 `*myapp-centralus*.contosonews.net` 인증서가 제목 이름에 없는 경우 Front Door는 연결을 거부하고 오류가 발생합니다. 
    1. **해결**방법: 규정 준수 관점에서 권장되지는 않지만 정문에 대한 인증서 주체 이름 확인을 사용하지 않도록 설정하여 이 오류를 해결할 수 있습니다. Azure 포털의 설정 과 API의 백엔드풀설정 아래에 있습니다.
2. **잘못된 CA의 백 엔드 호스팅 인증서**: 유효한 [CA의](/azure/frontdoor/front-door-troubleshoot-allowed-ca) 인증서만 정문으로 백 엔드에서 사용할 수 있습니다. 내부 CA 또는 자체 서명된 인증서의 인증서는 허용되지 않습니다.

## <a name="diagnostics-and-logging"></a>진단 및 로깅

### <a name="what-types-of-metrics-and-logs-are-available-with-azure-front-door"></a>Azure 정문에서 사용할 수 있는 메트릭 및 로그 유형은 무엇입니까?

로그 및 기타 진단 기능에 대한 자세한 내용은 정문 에 [대한 모니터링 메트릭 및 로그를](front-door-diagnostics.md)참조하십시오.

### <a name="what-is-the-retention-policy-on-the-diagnostics-logs"></a>진단 로그에서 보존 정책은 무엇인가요?

진단 로그는 고객 스토리지 계정으로 전달되고 고객은 기본 설정에 따라 보존 정책을 설정할 수 있습니다. 진단 로그를 이벤트 허브 또는 Azure Monitor 로그에도 전송할 수 있습니다. 자세한 내용은 [Azure 정문 진단 을](front-door-diagnostics.md)참조하십시오.

### <a name="how-do-i-get-audit-logs-for-azure-front-door"></a>Azure 정문에 대한 감사 로그를 받으시나요?

Azure 정문에 대한 감사 로그를 사용할 수 있습니다. 포털에서 정문 메뉴 블레이드에서 **활동 로그를** 클릭하여 감사 로그에 액세스합니다. 

### <a name="can-i-set-alerts-with-azure-front-door"></a>Azure 정문으로 경고를 설정할 수 있습니까?

예. Azure 정문은 경고를 지원합니다. 경고는 메트릭에 대해 구성됩니다. 

## <a name="next-steps"></a>다음 단계

- [Front Door를 만드는](quickstart-create-front-door.md) 방법을 알아봅니다.
- [Front Door의 작동 원리](front-door-routing-architecture.md)를 알아봅니다.
