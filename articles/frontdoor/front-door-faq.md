---
title: Azure Front 도어-질문과 대답
description: 이 페이지에서는 Azure Front 문에 대해 자주 묻는 질문에 대 한 답변을 제공 합니다.
services: frontdoor
documentationcenter: ''
author: duongau
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/18/2020
ms.author: duau
ms.openlocfilehash: 45f9e7a4e508cffd3593cec7bbcea3dd7882a60c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91819026"
---
# <a name="frequently-asked-questions-for-azure-front-door"></a>Azure Front 문에 대 한 질문과 대답

이 문서에서는 Azure Front 도어 기능 및 기능에 대 한 일반적인 질문에 답변 합니다. 질문에 대한 답을 찾지 못한 경우 다음 채널을 통해 (제시된 채널 순서에 따라) 문의할 수 있습니다.

1. 이 문서의 의견 섹션입니다.
2. [Azure Front 도어 UserVoice](https://feedback.azure.com/forums/217313-networking?category_id=345025).
3. **Microsoft 지원:** 새 지원 요청을 만들려면 Azure Portal의 **도움말** 탭에서 **도움말 + 지원** 단추를 선택한 다음 **새 지원 요청**을 선택 합니다.

## <a name="general"></a>일반

### <a name="what-is-azure-front-door"></a>Azure Front Door란?

Azure Front 도어는 ADN (애플리케이션 전달 네트워크) 서비스로, 응용 프로그램에 대 한 다양 한 계층 7 부하 분산 기능을 제공 합니다. 근 실시간 장애 조치(failover)를 통해 전역 부하 분산과 함께 DSA(동적 사이트 가속)를 제공합니다. Azure에서 완전히 관리하는 고가용성 및 확장 가능한 서비스입니다.

### <a name="what-features-does-azure-front-door-support"></a>Azure Front 도어가 지 원하는 기능은 무엇 인가요?

Azure 전면 도어는 DSA (동적 사이트 가속), TLS/SSL 오프 로딩 및 종단 간 TLS, 웹 응용 프로그램 방화벽, 쿠키 기반 세션 선호도, url 경로 기반 라우팅, 무료 인증서 및 다중 도메인 관리 등을 지원 합니다. 지원 되는 기능의 전체 목록은 [Azure Front 문 개요](front-door-overview.md)를 참조 하세요.

### <a name="what-is-the-difference-between-azure-front-door-and-azure-application-gateway"></a>Azure Front 도어와 Azure 애플리케이션 Gateway의 차이점은 무엇 인가요?

전면 도어와 Application Gateway는 모두 계층 7 (HTTP/HTTPS) 부하 분산 장치 이지만 주요 차이점은 프런트 도어가 글로벌 서비스 이지만 Application Gateway는 지역 서비스입니다. 전면 도어를 사용 하면 여러 지역에 걸쳐 다양 한 배율 단위/클러스터/스탬프 단위 간에 부하를 분산할 수 있지만, Application Gateway을 사용 하면 사용자의 v m/컨테이너 간에 부하를 분산할 수 있습니다.

### <a name="when-should-we-deploy-an-application-gateway-behind-front-door"></a>프런트 도어 뒤에 Application Gateway을 배포 해야 하는 경우는 언제 인가요?

프런트 도어 뒤에 Application Gateway를 사용 해야 하는 주요 시나리오는 다음과 같습니다.

- 프런트 도어는 글로벌 수준 에서만 경로 기반 부하 분산을 수행할 수 있지만 VNET (가상 네트워크) 내에서 트래픽 부하를 분산 하려는 경우에는 Application Gateway를 사용 해야 합니다.
- 프런트 도어는 v m/컨테이너 수준에서 작동 하지 않으므로 연결 드레이닝을 수행할 수 없습니다. 그러나 Application Gateway를 사용 하면 연결 드레이닝을 수행할 수 있습니다. 
- 전면 도어 뒤에 Application Gateway 하면 100% TLS/SSL 오프 로드를 수행 하 고 VNET (가상 네트워크) 내에서 HTTP 요청만 라우팅할 수 있습니다.
- 전면 도어 및 Application Gateway는 모두 세션 선호도를 지원 합니다. Front 도어가 사용자 세션의 후속 트래픽을 지정 된 지역의 동일한 클러스터 또는 백 엔드로 전달할 수 있지만, Application Gateway는 트래픽을 클러스터 내의 동일한 서버로 선호도 수 있습니다.  

### <a name="can-we-deploy-azure-load-balancer-behind-front-door"></a>프런트 도어 뒤에 Azure Load Balancer을 배포할 수 있나요?

Azure 전면 도어에는 트래픽을 라우팅하는 데 공용 VIP 또는 공개적으로 사용할 수 있는 DNS 이름이 필요 합니다. 프런트 도어 뒤에 Azure Load Balancer를 배포 하는 것은 일반적인 사용 사례입니다.

### <a name="what-protocols-does-azure-front-door-support"></a>Azure Front 도어가 지 원하는 프로토콜은 무엇 인가요?

Azure 전면 도어는 HTTP, HTTPS 및 HTTP/2를 지원 합니다.

### <a name="how-does-azure-front-door-support-http2"></a>Azure 전면 도어는 HTTP/2를 어떻게 지원 하나요?

HTTP/2 프로토콜 지원은 Azure Front 도어에 연결 하는 클라이언트에만 사용할 수 있습니다. 백 엔드 풀의 백 엔드에 대 한 통신은 HTTP/1.1을 통해 진행 됩니다. HTTP/2 지원은 기본적으로 사용 하도록 설정 되어 있습니다.

### <a name="what-resources-are-supported-today-as-part-of-backend-pool"></a>현재 백 엔드 풀의 일부로 어떤 리소스가 지원되나요?

백 엔드 풀은 저장소, 웹 앱, Kubernetes 인스턴스 또는 공용 연결이 있는 기타 사용자 지정 호스트 이름으로 구성 될 수 있습니다. Azure Front 도어를 사용 하려면 공용 IP 또는 공개적으로 확인할 수 있는 DNS 호스트 이름을 통해 백 엔드를 정의 해야 합니다. 백 엔드 풀의 멤버는 공용 연결이 있는 한 영역, 지역 또는 Azure 외부에 있을 수 있습니다.

### <a name="what-regions-is-the-service-available-in"></a>어떤 지역에서 서비스를 사용할 수 있습니까?

Azure 전면 도어는 글로벌 서비스 이며 특정 Azure 지역에 연결 되지 않습니다. 프런트 도어를 만들 때 지정 해야 하는 유일한 위치는 리소스 그룹 위치입니다 .이는 기본적으로 리소스 그룹의 메타 데이터가 저장 되는 위치를 지정 하는 것입니다. 프런트 도어 리소스 자체는 글로벌 리소스로 생성 되며 구성은 모든 Pop (현재 위치)에 전역적으로 배포 됩니다. 

### <a name="what-are-the-pop-locations-for-azure-front-door"></a>Azure Front 도어의 POP 위치는 무엇 인가요?

Azure 전면 도어에는 Microsoft에서 Azure CDN와 동일한 POP (현재 위치) 위치 목록이 있습니다. Pop의 전체 목록은 [Microsoft에서 AZURE CDN pop 위치](https://docs.microsoft.com/azure/cdn/cdn-pop-locations)를 참조 하세요.

### <a name="is-azure-front-door-a-dedicated-deployment-for-my-application-or-is-it-shared-across-customers"></a>Azure Front 도어가 내 응용 프로그램에 대 한 전용 배포 입니까 아니면 고객 간에 공유 되나요?

Azure 전면 도어는 전역적으로 분산 된 다중 테 넌 트 서비스입니다. 따라서 Front 도어에 대 한 인프라는 모든 고객에서 공유 됩니다. 그러나 Front 도어 프로필을 만들면 응용 프로그램에 필요한 특정 구성을 정의 하 고, 다른 앞면 도어 구성에 영향을 주는 다른 앞면 도어에 대 한 변경 사항은 없습니다.

### <a name="is-http-https-redirection-supported"></a>HTTP->HTTPS 리디렉션이 지원되나요?

예. 실제로 Azure Front 도어는 호스트, 경로 및 쿼리 문자열 리디렉션과 URL 리디렉션의 일부를 지원 합니다. [URL 리디렉션](front-door-url-redirect.md)에 대해 자세히 알아보세요. 

### <a name="in-what-order-are-routing-rules-processed"></a>라우팅 규칙은 어떤 순서로 처리 되나요?

앞으로의 경로에 대 한 경로는 정렬 되지 않으며 가장 일치 하는 항목에 따라 특정 경로가 선택 됩니다. [프런트 도어가 요청을 라우팅 규칙에 일치 시키는 방법](front-door-route-matching.md)에 대해 자세히 알아보세요.

### <a name="how-do-i-lock-down-the-access-to-my-backend-to-only-azure-front-door"></a>내 백 엔드에 대 한 액세스를 Azure Front 도어로 어떻게 할까요? 잠글 수 있나요?

응용 프로그램을 잠가 특정 Front 도어의 트래픽만 수락 하려면 백 엔드에 대 한 IP Acl을 설정 하 고 백 엔드의 트래픽을 Front 도어가 보낸 헤더 ' X-Azure-FDID '의 특정 값으로 제한 해야 합니다. 이러한 단계는 아래에 자세히 설명 되어 있습니다.

- 백 엔드에 대 한 IP ACLing를 구성 하 여 Azure 전면 도어의 백 엔드 IP 주소 공간 및 Azure 인프라 서비스의 트래픽을 허용 합니다. 백 엔드를 ACLing 아래 IP 세부 정보를 참조 하세요.
 
    - 프런트 도어의 IPv4 백 엔드 IP 주소 범위에 대 한 [AZURE IP 범위 및 서비스 태그](https://www.microsoft.com/download/details.aspx?id=56519) 의 *AzureFrontDoor* 섹션을 참조 하거나 [네트워크 보안 그룹](https://docs.microsoft.com/azure/virtual-network/security-overview#security-rules)에서 서비스 태그 *AzureFrontDoor* 를 사용할 수도 있습니다.
    - 서비스 태그에 포함 된 프론트 도어의 **IPv6** 백 엔드 IP 공간은 Azure IP 범위 JSON 파일에 나열 되지 않습니다. 명시적 IPv6 주소 범위를 찾고 있는 경우 현재 다음으로 제한 됩니다. `2a01:111:2050::/44`
    - 가상화 된 호스트 IP 주소를 통한 Azure의 [기본 인프라 서비스](https://docs.microsoft.com/azure/virtual-network/security-overview#azure-platform-considerations) : `168.63.129.16` 및 `169.254.169.254`

    > [!WARNING]
    > 앞 도어의 백 엔드 IP 공간은 나중에 변경 될 수 있지만,이를 위해 [AZURE IP 범위 및 서비스 태그](https://www.microsoft.com/download/details.aspx?id=56519)와 통합 될 예정입니다. 변경 또는 업데이트에 대 한 [AZURE IP 범위 및 서비스 태그](https://www.microsoft.com/download/details.aspx?id=56519) 를 구독 하는 것이 좋습니다.

-    API 버전 이상을 사용 하 여 Front 문에 대해 가져오기 작업을 수행 `2020-01-01` 합니다. API 호출에서 필드를 찾습니다 `frontdoorID` . 프런트 도어로 보낸 들어오는 헤더 '**x-y**'를 필드의 값을 사용 하 여 백 엔드로 필터링 합니다 `frontdoorID` . 또한 `Front Door ID` 전방 도어 포털 페이지의 개요 섹션에서 값을 찾을 수 있습니다. 

- 백 엔드 웹 서버에서 규칙 필터링을 적용 하 여 결과 ' X-y ' 헤더 값을 기준으로 트래픽을 제한 합니다.

  [Microsoft 인터넷 정보 서비스 (IIS)](https://www.iis.net/)에 대 한 예제는 다음과 같습니다.

    ``` xml
    <?xml version="1.0" encoding="UTF-8"?>
    <configuration>
        <system.webServer>
            <rewrite>
                <rules>
                    <rule name="Filter_X-Azure-FDID" patternSyntax="Wildcard" stopProcessing="true">
                        <match url="*" />
                        <conditions>
                            <add input="{HTTP_X_AZURE_FDID}" pattern="xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx" negate="true" />
                        </conditions>
                        <action type="AbortRequest" />
                    </rule>
                </rules>
            </rewrite>
        </system.webServer>
    </configuration>
    ```



### <a name="can-the-anycast-ip-change-over-the-lifetime-of-my-front-door"></a>Front 도어가 지속 되는 동안 애니캐스트 IP가 변경 될 수 있나요?

프런트 도어의 프런트 엔드 애니캐스트 IP는 일반적으로 변경 되지 않으며 전면 도어 수명 동안 정적 상태로 유지 될 수 있습니다. 그러나 동일한를 **보장 하지** 는 않습니다. IP에 대 한 직접적인 종속성을 고려 하지 않습니다.

### <a name="does-azure-front-door-support-static-or-dedicated-ips"></a>Azure 전면 도어는 고정 또는 전용 Ip를 지원 하나요?

아니요, Azure Front 도어가 현재 정적 또는 전용 프런트 엔드 애니캐스트 Ip를 지원 하지 않습니다. 

### <a name="does-azure-front-door-support-x-forwarded-for-headers"></a>Azure Front 도어가 x로 전달 된 헤더를 지원 하나요?

예, Azure Front 도어가 x로 전달 되 고, X 전달 된 호스트 및 X 전달 프로토콜 헤더를 지원 합니다. X로 전달 된 경우-헤더가 이미 있는 경우 Front 도어가 클라이언트 소켓 IP를 추가 합니다. 또는 클라이언트 소켓 IP를 사용 하 여 헤더를 값으로 추가 합니다. X 전달-호스트 및 X-전달-프로토콜의 경우 값이 재정의 됩니다.

[전면 도어 지원 HTTP 헤더](front-door-http-headers-protocol.md)에 대해 자세히 알아보세요.  

### <a name="how-long-does-it-take-to-deploy-an-azure-front-door-does-my-front-door-still-work-when-being-updated"></a>Azure Front 도어를 배포 하는 데 얼마나 걸립니까? 업데이트 하는 동안 내 Front 문이 여전히 작동 하나요?

새 Front 도어가 생성 되거나 기존 전면 도어를 업데이트 하는 경우 전역 배포에 대해 3 ~ 5 분 정도 걸립니다. 즉, 약 3 ~ 5 분 이내에 Front 도어 구성이 전 세계 모든 Pop에 배포 됩니다.

참고-사용자 지정 TLS/SSL 인증서 업데이트는 전역적으로 배포 하는 데 30 분 정도 걸립니다.

경로 또는 백 엔드 풀 등의 모든 업데이트는 원활 하 게 작동 하며 가동 중지 시간 (새 구성이 올바른 경우)을 발생 시킵니다. ' AFD 관리 '에서 ' 자신의 인증서 사용 '으로 전환 하거나 그 반대로 전환 하지 않는 한 인증서 업데이트도 원자성 이며 중단 되지 않습니다.


## <a name="configuration"></a>구성

### <a name="can-azure-front-door-load-balance-or-route-traffic-within-a-virtual-network"></a>Azure Front 도어는 가상 네트워크 내에서 트래픽 부하를 분산 하거나 라우팅할 수 있나요?

Azure 전면 도어 (AFD)에는 트래픽을 라우팅하는 공용 IP 또는 공개적으로 확인할 수 있는 DNS 이름이 필요 합니다. 따라서 가상 네트워크 내에서 직접 라우팅할 수 없는 AFD의 대답은 아닙니다. 그러나 Application Gateway를 사용 하거나 사이에 Azure Load Balancer를 사용 하면이 시나리오를 해결할 수 있습니다.

### <a name="what-are-the-various-timeouts-and-limits-for-azure-front-door"></a>Azure Front 문에 대 한 다양 한 시간 제한 및 제한은 무엇 인가요?

[Azure Front 도어에 대해 문서화 된 시간 제한 및 제한](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#azure-front-door-service-limits)에 대해 알아봅니다.

### <a name="how-long-does-it-take-for-a-rule-to-take-effect-after-being-added-to-the-front-door-rules-engine"></a>프런트 도어 규칙 엔진에 추가 된 후 규칙이 적용 되려면 얼마나 걸립니까?

규칙 엔진 구성은 업데이트를 완료 하는 데 약 10 ~ 15 분이 걸립니다. 업데이트가 완료 되는 즉시 규칙이 적용 될 것으로 예측할 수 있습니다. 

## <a name="performance"></a>성능

### <a name="how-does-azure-front-door-support-high-availability-and-scalability"></a>Azure 전면 도어는 고가용성 및 확장성을 어떻게 지원 하나요?

Azure 전면 도어는 응용 프로그램의 확장성 요구를 충족 하기 위해 대용량의 용량이 포함 된 전 세계적으로 분산 된 다중 테 넌 트 플랫폼입니다. Microsoft의 글로벌 네트워크에 지에서 제공 되는 프런트 도어는 전체 응용 프로그램을 장애 조치 (failover) 하거나 지역 또는 다른 클라우드에서 개별 마이크로 서비스를 장애 조치할 수 있도록 하는 전역 부하 분산 기능을 제공 합니다.

## <a name="tls-configuration"></a>TLS 구성

### <a name="what-tls-versions-are-supported-by-azure-front-door"></a>Azure Front 도어가 지 원하는 TLS 버전은 무엇 인가요?

9 월 2019 일 이후에 만들어진 모든 전방 도어 프로필은 TLS 1.2를 기본 최소값으로 사용 합니다.

전면 도어는 TLS 버전 1.0, 1.1 및 1.2을 지원 합니다. TLS 1.3은 아직 지원 되지 않습니다.

### <a name="what-certificates-are-supported-on-azure-front-door"></a>Azure Front 도어에서 지원 되는 인증서는 무엇 인가요?

프런트 도어 사용자 지정 도메인에서 콘텐츠를 안전 하 게 배달 하기 위해 HTTPS 프로토콜을 사용 하도록 설정 하려면 Azure Front 도어에서 관리 되는 인증서를 사용 하거나 사용자 고유의 인증서를 사용 하도록 선택할 수 있습니다.
전면 도어 관리 옵션은 Digicert를 통해 표준 TLS/SSL 인증서를 프로 비전 하 고 앞 도어의 Key Vault에 저장 합니다. 사용자 고유의 인증서를 사용 하도록 선택 하는 경우 지원 되는 CA에서 인증서를 등록할 수 있으며 표준 TLS, 확장 유효성 검사 인증서 또는 와일드 카드 인증서를 사용할 수 있습니다. 자체 서명 된 인증서는 지원 되지 않습니다. [사용자 지정 도메인에 대해 HTTPS를 사용 하도록 설정 하는 방법을](https://aka.ms/FrontDoorCustomDomainHTTPS)알아봅니다.

### <a name="does-front-door-support-autorotation-of-certificates"></a>전면 도어는 인증서의 autorotation을 지원 하나요?

전면 도어 관리 되는 인증서 옵션의 경우 인증서는 앞 도어로 autorotated 됩니다. 전면 도어 관리 인증서를 사용 하 고 인증서 만료 날짜가 60 일 미만인 경우 지원 티켓을 제출 합니다.
</br>사용자 지정 TLS/SSL 인증서의 경우 autorotation 지원 되지 않습니다. 지정 된 사용자 지정 도메인에 대해 처음으로 설정 된 방법과 마찬가지로 Key Vault의 올바른 인증서 버전을 앞 도어로 지정 하 고, Front 문에 대 한 서비스 사용자가 계속 해 서 Key Vault에 액세스할 수 있도록 해야 합니다. 이 업데이트 된 인증서 롤아웃 작업은 원자성 이며, 인증서에 대 한 주체 이름 또는 SAN이 변경 되지 않는 경우 프로덕션 영향을 발생 시 키 지 않습니다.

### <a name="what-are-the-current-cipher-suites-supported-by-azure-front-door"></a>Azure Front 도어가 지 원하는 현재 암호 그룹은 무엇 인가요?

TLS 1.2의 경우 지원 되는 암호 그룹은 다음과 같습니다. 

- TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
- TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256
- TLS_DHE_RSA_WITH_AES_256_GCM_SHA384
- TLS_DHE_RSA_WITH_AES_128_GCM_SHA256

TLS 1.0/1.1을 사용 하는 사용자 지정 도메인을 사용 하는 경우 다음 암호 그룹이 지원 됩니다.

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

### <a name="can-i-configure-tls-policy-to-control-tls-protocol-versions"></a>Tls 프로토콜 버전을 제어 하는 TLS 정책을 구성할 수 있나요?

Azure Portal 또는 [azure REST API](https://docs.microsoft.com/rest/api/frontdoorservice/frontdoor/frontdoors/createorupdate#minimumtlsversion)를 통해 사용자 지정 도메인 HTTPS 설정의 Azure Front 문에 최소 TLS 버전을 구성할 수 있습니다. 현재는 1.0과 1.2 중에서 선택할 수 있습니다.

### <a name="can-i-configure-front-door-to-only-support-specific-cipher-suites"></a>특정 암호 모음만 지원 하도록 Front 도어를 구성할 수 있나요?

아니요, 특정 암호 그룹에 대 한 Front 도어 구성은 지원 되지 않습니다. 그러나 인증 기관에서 사용자 고유의 사용자 지정 TLS/SSL 인증서를 가져올 수 있습니다 (예를 들어 Verisign, Entrust 또는 Digicert). 인증서를 생성 하는 경우 인증서에 특정 암호 그룹을 표시할 수 있습니다. 

### <a name="does-front-door-support-ocsp-stapling"></a>Front 도어가 OCSP 스테이플링을 지원 하나요?

예, OCSP 스테이플링은 기본적으로 앞 도어에 의해 지원 되며 구성이 필요 하지 않습니다.

### <a name="does-azure-front-door-also-support-re-encryption-of-traffic-to-the-backend"></a>Azure Front 도어가 백 엔드에 대 한 트래픽 다시 암호화도 지원 하나요?

예, Azure Front 도어는 TLS/SSL 오프 로드와 백 엔드에 대 한 트래픽을 다시 암호화 하는 종단 간 TLS를 지원 합니다. 실제로 백 엔드에 대 한 연결이 공용 IP를 통해 수행 되므로 전달 프로토콜로 HTTPS를 사용 하도록 Front 도어를 구성 하는 것이 좋습니다.

### <a name="does-front-door-support-self-signed-certificates-on-the-backend-for-https-connection"></a>프런트 도어는 HTTPS 연결을 위해 백 엔드에서 자체 서명 된 인증서를 지원 하나요?

아니요, 자체 서명 된 인증서는 전면 도어에서 지원 되지 않으며 제한 사항이 두 가지 모두에 적용 됩니다.

1. **백 엔드**: 캐싱을 사용 하도록 설정 된 라우팅 규칙의 원본에 대 한 캐시를 입력 하거나 HTTPS 또는 https 상태 프로브로 트래픽을 전달 하는 경우 자체 서명 된 인증서를 사용할 수 없습니다.
2. **프런트 엔드**: 사용자 지정 도메인에서 HTTPS를 사용 하도록 설정 하는 데 고유한 사용자 지정 TLS/SSL 인증서를 사용 하는 경우 자체 서명 된 인증서를 사용할 수 없습니다.

### <a name="why-is-https-traffic-to-my-backend-failing"></a>백 엔드에 대 한 HTTPS 트래픽이 실패 하는 이유는 무엇 인가요?

상태 프로브 또는 전달 요청에 대해 백 엔드에 대 한 HTTPS 연결에 성공 하는 경우 HTTPS 트래픽이 실패할 수 있는 두 가지 이유가 있을 수 있습니다.

1. **인증서 주체 이름 불일치**: HTTPS 연결의 경우 백 엔드는 백 엔드 호스트 이름과 일치 하는 주체 이름을 가진 유효한 CA에서 인증서를 제공 하는 것으로 예상 합니다. 예를 들어 백 엔드 호스트 이름이로 설정 되 `myapp-centralus.contosonews.net` 고, TLS 핸드셰이크 중에 백 엔드에 표시 되는 인증서가 `myapp-centralus.contosonews.net` 주체 이름에도 그렇지 않은 경우 `*myapp-centralus*.contosonews.net` , 전면 도어는 연결을 거부 하 고 오류를 생성 합니다. 
    1. **해결**방법: 규정 준수 관점에서 권장 되지 않지만, 앞면 도어에 대해 인증서 주체 이름 확인을 사용 하지 않도록 설정 하 여이 오류를 해결할 수 있습니다. 이는 Azure Portal의 설정 및 API의 BackendPoolsSettings 아래에 있습니다.
2. **잘못 된 ca의 백 엔드 호스팅 인증서**: 전방 도어를 사용 하는 백 엔드에서 [유효한 ca](/azure/frontdoor/front-door-troubleshoot-allowed-ca) 의 인증서만 사용할 수 있습니다. 내부 Ca 또는 자체 서명 된 인증서의 인증서는 허용 되지 않습니다.

### <a name="can-i-use-clientmutual-authentication-with-azure-front-door"></a>Azure Front 문에 클라이언트/상호 인증을 사용할 수 있나요?

아니요. Azure 전면 도어가 [RFC 5246](https://tools.ietf.org/html/rfc5246)에서 클라이언트/상호 인증을 도입 하는 TLS 1.2를 지원 하지만, 현재 Azure front 도어가 클라이언트/상호 인증을 지원 하지 않습니다.

## <a name="diagnostics-and-logging"></a>진단 및 로깅

### <a name="what-types-of-metrics-and-logs-are-available-with-azure-front-door"></a>Azure Front 도어를 사용 하 여 어떤 유형의 메트릭 및 로그를 사용할 수 있나요?

로그 및 기타 진단 기능에 대 한 자세한 내용은 [Front 도어에 대 한 메트릭 및 로그 모니터링](front-door-diagnostics.md)을 참조 하세요.

### <a name="what-is-the-retention-policy-on-the-diagnostics-logs"></a>진단 로그에서 보존 정책은 무엇인가요?

진단 로그는 고객 스토리지 계정으로 전달되고 고객은 기본 설정에 따라 보존 정책을 설정할 수 있습니다. 진단 로그를 이벤트 허브 또는 Azure Monitor 로그에도 전송할 수 있습니다. 자세한 내용은 [Azure Front 도어 진단](front-door-diagnostics.md)을 참조 하세요.

### <a name="how-do-i-get-audit-logs-for-azure-front-door"></a>Azure Front 문에 대 한 감사 로그를 가져올 어떻게 할까요? 있나요?

Azure Front 문에 감사 로그를 사용할 수 있습니다. 포털에서 전방 도어의 메뉴 블레이드에서 **활동 로그** 를 클릭 하 여 감사 로그에 액세스 합니다. 

### <a name="can-i-set-alerts-with-azure-front-door"></a>Azure Front 도어를 사용 하 여 경고를 설정할 수 있나요?

예, Azure Front 도어가 경고를 지원 합니다. 경고는 메트릭에 대해 구성됩니다. 

## <a name="next-steps"></a>다음 단계

- [Front Door를 만드는](quickstart-create-front-door.md) 방법을 알아봅니다.
- [Front Door의 작동 원리](front-door-routing-architecture.md)를 알아봅니다.
