---
title: Azure Application Gateway에 대 한 질문과 대답
description: Azure Application Gateway에 대 한 질문과 대답을 찾습니다.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.workload: infrastructure-services
ms.date: 6/1/2019
ms.author: victorh
ms.openlocfilehash: 25779cd62f70df41a7734d2f8097504923dcb3a3
ms.sourcegitcommit: 087ee51483b7180f9e897431e83f37b08ec890ae
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/31/2019
ms.locfileid: "66430874"
---
# <a name="frequently-asked-questions-about-application-gateway"></a>Application Gateway에 대 한 질문과 대답

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Azure Application Gateway에 대 한 일반적인 질문은 다음과 같습니다.

## <a name="general"></a>일반

### <a name="what-is-application-gateway"></a>Application Gateway란?

Azure Application Gateway는 응용 프로그램 배달 컨트롤러 (ADC)를 서비스로 제공합니다. 응용 프로그램에 대 한 다양 한 계층 7 부하 분산 기능을 제공합니다. 이 서비스는 고가용성, 확장성 및 Azure에서 완전히 관리 되는 경우

### <a name="what-features-does-application-gateway-support"></a>Application Gateway에서 지원하는 기능은 어떤 것이 있나요?

Application Gateway는 자동 크기 조정, SSL 오프 로딩 및 종단 간 SSL, 웹 응용 프로그램 방화벽 (WAF), 쿠키 기반 세션 선호도, URL 경로 기반 라우팅, 다중 사이트 호스팅 및 기타 기능을 지원 합니다. 지원되는 기능의 전체 목록은 [Application Gateway 소개](application-gateway-introduction.md)를 참조하세요.

### <a name="how-do-application-gateway-and-azure-load-balancer-differ"></a>수행할 Application Gateway 및 Azure Load Balancer와 어떻게?

Application Gateway는 웹 트래픽 (HTTP, HTTPS, WebSocket을 및 HTTP/2)에 작동 하는 계층 7 부하 분산. 부하 분산에 대 한 SSL 종료, 쿠키 기반 세션 선호도, 라운드 로빈 등 기능을 지원 트래픽입니다. 부하 분산 장치 부하 분산 트래픽을 계층 4에서 (TCP 또는 UDP) 합니다.

### <a name="what-protocols-does-application-gateway-support"></a>Application Gateway에서 지원하는 프로토콜은 무엇인가요?

Application Gateway는 HTTP, HTTPS, HTTP/2 및 WebSocket을 지원합니다.

### <a name="how-does-application-gateway-support-http2"></a>Application Gateway는 어떻게 HTTP/2를 지원하나요?

참조 [http/2 지원은](https://docs.microsoft.com/azure/application-gateway/configuration-overview#http2-support)합니다.

### <a name="what-resources-are-supported-as-part-of-a-backend-pool"></a>백 엔드 풀의 일부로 어떤 리소스가 지원 되나요?

참조 [지원 되는 백 엔드 리소스](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#backend-pool)합니다.

### <a name="in-what-regions-is-application-gateway-available"></a>어떤 지역에서 응용 프로그램 게이트웨이 사용할 수 있는?

Application Gateway는 Azure 전체의 모든 지역에서 사용할 수 있습니다. 사용할 수 있는 이기도 [Azure 중국 21Vianet](https://www.azure.cn/) 하 고 [Azure Government](https://azure.microsoft.com/overview/clouds/government/)합니다.

### <a name="is-this-deployment-dedicated-for-my-subscription-or-is-it-shared-across-customers"></a>이 배포는 내 구독에 대 한 전용 되었거나 고객 간에 공유 됩니다.

Application Gateway는 가상 네트워크에서 전용 배포입니다.

### <a name="does-application-gateway-support-http-to-https-redirection"></a>Application Gateway에 HTTP-HTTPS 리디렉션을 지원 하나요?

리디렉션은 지원됩니다. 참조 [Application Gateway 리디렉션 개요](application-gateway-redirect-overview.md)합니다.

### <a name="in-what-order-are-listeners-processed"></a>수신기는 어떤 순서로 처리되나요?

참조 된 [처리 하는 수신기의 순서](https://docs.microsoft.com/azure/application-gateway/configuration-overview#order-of-processing-listeners)합니다.

### <a name="where-do-i-find-the-application-gateway-ip-and-dns"></a>응용 프로그램 게이트웨이 IP 및 DNS를 찾을 위치

공용 IP 주소 끝점을 사용 하는 경우에 공용 IP 주소 리소스에 IP 및 DNS 정보를 알려드립니다. 또는 application gateway에 대 한 개요 페이지의 포털에 있습니다. 내부 IP 주소를 사용 하는 경우 개요 페이지의 정보를 찾아보십시오.

### <a name="what-are-the-settings-for-keep-alive-timeout-and-tcp-idle-timeout"></a>연결 유지 시간 제한 및 TCP 유휴 시간 제한에 대 한 설정 이란?

 Application Gateway v1 SKU에서 연결 유지 시간 제한은 120 초입니다. V2 SKU에 대 한 연결 유지 시간 제한을 75 초입니다. TCP 유휴 시간 제한 되는 응용 프로그램 게이트웨이의 프런트 엔드 가상 ip (VIP)는 4 분 기본값.

### <a name="does-the-ip-or-dns-name-change-over-the-lifetime-of-the-application-gateway"></a>IP 또는 DNS 이름을 application gateway의 수명 동안 변경 되나요?

VIP는 중지 하 고 application gateway를 시작 하는 경우 변경할 수 있습니다. 하지만 응용 프로그램 게이트웨이에 연결 된 DNS 이름을 gateway의 수명 동안 변경 되지 않습니다. DNS 이름을 변경 하지 않으므로 CNAME 별칭을 사용 하 고 응용 프로그램 게이트웨이의 DNS 주소를 가리키도록 해야 합니다.

### <a name="does-application-gateway-support-static-ip"></a>Application Gateway에서 고정 IP를 지원하나요?

예, Application Gateway v2 SKU는 고정 공용 IP 주소를 지원합니다. v1 SKU는 고정 내부 IP를 지원합니다.

### <a name="does-application-gateway-support-multiple-public-ips-on-the-gateway"></a>Application Gateway가 게이트웨이에서 여러 공용 IP를 지원하나요?

Application gateway는 하나의 공용 IP 주소를 지원합니다.

### <a name="how-large-should-i-make-my-subnet-for-application-gateway"></a>Application Gateway에 대해 얼마나 큰 서브넷을 만들어야 하나요?

참조 [Application Gateway 서브넷 크기 고려 사항](https://docs.microsoft.com/azure/application-gateway/configuration-overview#size-of-the-subnet)합니다.

### <a name="can-i-deploy-more-than-one-application-gateway-resource-to-a-single-subnet"></a>둘 이상의 Application Gateway 리소스를 단일 서브넷에 배포할 수 있나요?

예. 지정된 된 응용 프로그램 게이트웨이 배포의 여러 인스턴스 이외에 다른 응용 프로그램 게이트웨이 리소스를 포함 하는 기존 서브넷에 다른 고유한 응용 프로그램 게이트웨이 리소스를 제공할 수 있습니다.

단일 서브넷 Standard_v2와 표준 응용 프로그램 게이트웨이 함께 지원할 수 없습니다.

### <a name="does-application-gateway-support-x-forwarded-for-headers"></a>Application Gateway에서 x-forwarded-for 헤더를 지원하나요?

예. 참조 [요청 수정](https://docs.microsoft.com/azure/application-gateway/how-application-gateway-works#modifications-to-the-request)합니다.

### <a name="how-long-does-it-take-to-deploy-an-application-gateway-will-my-application-gateway-work-while-its-being-updated"></a>어떻게 걸리나요 application gateway를 배포 하 시겠습니까? 내 응용 프로그램 게이트웨이 업데이트 하는 동안 유지 됩니까?

새 Application Gateway v1 SKU 배포 시 프로비전하는 데 최대 20분이 걸릴 수 있습니다. 중단, 한 인스턴스 크기 또는 수를 변경 하지 않습니다 하 고 게이트웨이이 시간 동안 활성 상태로 유지 합니다.

V2 SKU를 사용 하는 배포 프로 비전 6 분 정도 걸릴 수 있습니다.

### <a name="can-i-use-exchange-server-as-a-backend-with-application-gateway"></a>Application Gateway를 사용 하 여 백 엔드로 Exchange Server를 사용할 수 있나요?

아니요. Application Gateway는 SMTP, IMAP 및 POP3 등의 전자 메일 프로토콜을 지원 하지 않습니다. 

## <a name="performance"></a>성능

### <a name="how-does-application-gateway-support-high-availability-and-scalability"></a>Application Gateway는 고가용성과 확장성을 어떤 방식으로 지원하나요?

V1 응용 프로그램 게이트웨이 SKU는 둘 이상의 인스턴스를 배포한 경우 고가용성 시나리오를 지원 합니다. Azure는 업데이트 및 장애 도메인 간에 인스턴스가 동시에 모든 실패 하지는 이러한 인스턴스를 배포 합니다. v1 SKU는 로드를 공유하기 위해 동일한 게이트웨이의 여러 인스턴스를 추가하여 확장성을 지원합니다.

v2 SKU의 경우에는 여러 장애 도메인과 업데이트 도메인에 새 인스턴스가 자동으로 분산됩니다. 영역 중복을 선택 하면 최신 인스턴스 영역 실패 복원 력을 제공 하는 가용성 영역도 분산 됩니다.

### <a name="how-do-i-achieve-a-dr-scenario-across-datacenters-by-using-application-gateway"></a>달성 하려면 어떻게 DR 시나리오를 데이터 센터에서 응용 프로그램 게이트웨이 사용 하 여?

여러 데이터 센터에서 여러 application gateway 간에 트래픽을 분산 하기 위해 Traffic Manager를 사용 합니다.

### <a name="does-application-gateway-support-autoscaling"></a>Application Gateway에 자동 크기 조정을 지원 하나요?

예. Application Gateway v2 SKU는 자동 크기 조정을 지원합니다. 자세한 내용은 [자동 크기 조정 및 영역 중복 응용 프로그램 게이트웨이](application-gateway-autoscaling-zone-redundant.md)합니다.

### <a name="does-manual-scale-up-or-scale-down-cause-downtime"></a>수동 확장 또는 규모 축소 원인 가동 중지 시간?

아니요. 인스턴스가 업그레이드 도메인 및 장애 도메인 간에 배포됩니다.

### <a name="does-application-gateway-support-connection-draining"></a>Application Gateway는 연결 드레이닝을 지원하나요?

예. 연결 드레이닝 중지 하지 않고 백 엔드 풀 내에서 멤버를 변경 하려면 설정할 수 있습니다. 이 설치는 닫습니다 보낼 기존 연결 이전 대상 될 때까지 해당 연결을 계속할 수 있습니다 또는 구성 가능한 시간 제한 만료를 허용 합니다. 연결 드레이닝 완료에 대 한 현재 진행 중인 연결만 기다립니다. Application Gateway의 응용 프로그램 세션 상태를 인식 하지 못합니다.

### <a name="can-i-change-instance-size-from-medium-to-large-without-disruption"></a>중단 없이 인스턴스 크기를 중간에서 큼으로 변경할 수 있나요?

예. Azure 인스턴스 인스턴스가 동시에 모든 실패 하지는 업데이트 및 장애 도메인 분산 됩니다. Application Gateway는 로드를 공유하기 위해 동일한 게이트웨이의 여러 인스턴스를 추가하여 크기 조정을 지원합니다.

## <a name="configuration"></a>구성

### <a name="is-application-gateway-always-deployed-in-a-virtual-network"></a>Application Gateway가 가상 네트워크에서 항상 배포되나요?

예. Application Gateway는 항상 가상 네트워크 서브넷에 배포 됩니다. 이 서브넷은 application gateway만 포함할 수 있습니다. 자세한 내용은 [가상 네트워크 및 서브넷 요구 사항](https://docs.microsoft.com/azure/application-gateway/configuration-overview#azure-virtual-network-and-dedicated-subnet)합니다.

### <a name="can-application-gateway-communicate-with-instances-outside-of-its-virtual-network-or-outside-of-its-subscription"></a>Application Gateway 해당 구독 외부 또는 가상 네트워크 외부 인스턴스와 통신할 수 있나요?

IP 연결을 설정한으로 Application Gateway에 가상 네트워크 외부 인스턴스와 통신할 수 있습니다. Application Gateway 수도 있는 구독 외부 인스턴스와 통신할 수 있습니다. 사용 하 여 백 엔드 풀 멤버로 내부 Ip를 사용 하려는 경우 [가상 네트워크 피어 링](../virtual-network/virtual-network-peering-overview.md) 하거나 [Azure VPN Gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md)합니다.

### <a name="can-i-deploy-anything-else-in-the-application-gateway-subnet"></a>Application Gateway 서브넷에서 다른 항목을 배포할 수 있나요?

아니요. 하지만 다른 응용 프로그램 게이트웨이 서브넷에 배포할 수 있습니다.

### <a name="are-network-security-groups-supported-on-the-application-gateway-subnet"></a>응용 프로그램 게이트웨이 서브넷에 네트워크 보안 그룹이 지원 되나요?

참조 [Application Gateway 서브넷의 네트워크 보안 그룹](https://docs.microsoft.com/azure/application-gateway/configuration-overview#network-security-groups-on-the-application-gateway-subnet)합니다.

### <a name="does-the-application-gateway-subnet-support-user-defined-routes"></a>응용 프로그램 게이트웨이 서브넷은 사용자 정의 경로 지원 하나요?

참조 [Application Gateway 서브넷에서 지원 되는 사용자 정의 경로](https://docs.microsoft.com/azure/application-gateway/configuration-overview#user-defined-routes-supported-on-the-application-gateway-subnet)합니다.

### <a name="what-are-the-limits-on-application-gateway-can-i-increase-these-limits"></a>Application Gateway에서 한도는 어떻게 되나요? 이러한 한도를 늘릴 수 있나요?

참조 [Application Gateway 제한](../azure-subscription-service-limits.md#application-gateway-limits)합니다.

### <a name="can-i-simultaneously-use-application-gateway-for-both-external-and-internal-traffic"></a>동시에 사용할 수 응용 프로그램 게이트웨이 외부 및 내부 트래픽에 대 한?

예. Application Gateway는 하나의 내부 IP 및 응용 프로그램 게이트웨이 당 하나의 외부 IP를 지원합니다.

### <a name="does-application-gateway-support-virtual-network-peering"></a>가상 네트워크 피어 링 Application Gateway 지원 하나요?

예. 가상 네트워크 피어 링 하면 다른 가상 네트워크의 트래픽을 부하를 분산할 수 있습니다.

### <a name="can-i-talk-to-on-premises-servers-when-theyre-connected-by-expressroute-or-vpn-tunnels"></a>와 통신할 수 있나요 온-프레미스 서버가 ExpressRoute 또는 VPN 터널과 연결 되어 있을 때?

예, 트래픽이 허용되기만 한다면 가능합니다.

### <a name="can-one-backend-pool-serve-many-applications-on-different-ports"></a>하나의 백 엔드 풀이 다른 포트로 많은 응용 프로그램을 사용할 수 있습니다?

마이크로 서비스 아키텍처가 지원 됩니다. 서로 다른 포트에서 프로브 여러 HTTP 설정을 구성 해야 합니다.

### <a name="do-custom-probes-support-wildcards-or-regex-on-response-data"></a>사용자 지정 프로브를 지원 하나요 와일드 카드 또는 regex 응답 데이터에?

아니요. 

### <a name="how-are-routing-rules-processed-in-application-gateway"></a>Application Gateway의 라우팅 규칙을 처리 하는 방법

참조 [처리 규칙의 순서](https://docs.microsoft.com/azure/application-gateway/configuration-overview#order-of-processing-rules)합니다.

### <a name="for-custom-probes-what-does-the-host-field-signify"></a>사용자 지정 프로브에 대 한 항목은 호스트 필드를 나타내는?

호스트 필드는 프로브를 구성한 경우 멀티 사이트를 보내려면 응용 프로그램 게이트웨이 이름을 지정 합니다. 그렇지 않으면 '127.0.0.1'을 사용 합니다. 이 값은 가상 컴퓨터 호스트 이름과 다릅니다. 해당 형식은 \<프로토콜\>://\<호스트\>:\<포트\>\<경로\>합니다.

### <a name="can-i-allow-application-gateway-access-to-only-a-few-source-ip-addresses"></a>응용 프로그램 게이트웨이에 대 한 몇 가지 원본 IP 주소만 허용 합니까?

예. 참조 [특정 원본 Ip에 대 한 액세스를 제한](https://docs.microsoft.com/azure/application-gateway/configuration-overview#allow-application-gateway-access-to-a-few-source-ips)합니다.

### <a name="can-i-use-the-same-port-for-both-public-facing-and-private-facing-listeners"></a>공용 및 개인 연결 수신기에 대 한 동일한 포트를 사용할 수 있습니까?

아니요.

### <a name="is-there-guidance-available-to-migrate-from-the-v1-sku-to-the-v2-sku"></a>이 지침 v1 SKU에서 v2 SKU로 마이그레이션에 사용할 수 있습니까?

예. 자세한 세부 정보를 참조 하십시오 [마이그레이션할 Azure Application Gateway 및 Web Application Firewall v1에서 v2로](migrate-v1-v2.md)합니다.


## <a name="configuration---ssl"></a>구성-SSL

### <a name="what-certificates-does-application-gateway-support"></a>Application Gateway에서 어떤 인증서를 지원 하나요?

Application Gateway는 자체 서명 된 인증서, CA (인증 기관) 인증서, 확장 유효성 검사 (EV) 인증서 및 와일드 카드 인증서를 지원 합니다.

### <a name="what-cipher-suites-does-application-gateway-support"></a>어떤 암호 그룹은 Application Gateway 지원?

Application Gateway는 다음 암호 그룹을 지원 합니다. 

- TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
- TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256
- TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384
- TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256
- TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA
- TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA
- TLS_DHE_RSA_WITH_AES_256_GCM_SHA384
- TLS_DHE_RSA_WITH_AES_128_GCM_SHA256
- TLS_DHE_RSA_WITH_AES_256_CBC_SHA
- TLS_DHE_RSA_WITH_AES_128_CBC_SHA
- TLS_RSA_WITH_AES_256_GCM_SHA384
- TLS_RSA_WITH_AES_128_GCM_SHA256
- TLS_RSA_WITH_AES_256_CBC_SHA256
- TLS_RSA_WITH_AES_128_CBC_SHA256
- TLS_RSA_WITH_AES_256_CBC_SHA
- TLS_RSA_WITH_AES_128_CBC_SHA
- TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384
- TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256
- TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384
- TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256
- TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA
- TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA
- TLS_DHE_DSS_WITH_AES_256_CBC_SHA256
- TLS_DHE_DSS_WITH_AES_128_CBC_SHA256
- TLS_DHE_DSS_WITH_AES_256_CBC_SHA
- TLS_DHE_DSS_WITH_AES_128_CBC_SHA
- TLS_RSA_WITH_3DES_EDE_CBC_SHA
- TLS_DHE_DSS_WITH_3DES_EDE_CBC_SHA

SSL 옵션을 사용자 지정 하는 방법에 대 한 자세한 내용은 [Application Gateway에서 SSL 구성 정책 버전 및 암호 그룹](application-gateway-configure-ssl-policy-powershell.md)합니다.

### <a name="does-application-gateway-support-reencryption-of-traffic-to-the-backend"></a>Application Gateway 백 엔드에 대 한 트래픽의 재 암호화를 지원 하나요?

예. Application Gateway는 백 엔드에 트래픽을 다시 암호화 하는 종단 간 SSL 및 SSL 오프 로드를 지원 합니다.

### <a name="can-i-configure-ssl-policy-to-control-ssl-protocol-versions"></a>SSL 프로토콜 버전을 제어 하는 SSL 정책을 구성할 수 있나요?

예. TLS1.0, TLS1.1 및 TLS1.2를 거부 하도록 Application Gateway를 구성할 수 있습니다. 기본적으로 SSL 2.0 및 3.0은 이미 비활성화 되어 하 고 구성할 수 없습니다.

### <a name="can-i-configure-cipher-suites-and-policy-order"></a>암호 그룹 및 정책 순서를 구성할 수 있나요?

예. Application Gateway을 수행할 수 있습니다 [나열한 구성](application-gateway-ssl-policy-overview.md)합니다. 사용자 지정 정책을 정의 하려면 다음 암호 그룹 중 하나 이상을 사용 하도록 설정 합니다. 

* TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256 
* TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256
* TLS_DHE_RSA_WITH_AES_128_GCM_SHA256
* TLS_RSA_WITH_AES_128_GCM_SHA256
* TLS_RSA_WITH_AES_256_CBC_SHA256
* TLS_RSA_WITH_AES_128_CBC_SHA256

Application Gateway는 백 엔드 관리를 위한에 SHA256을 사용 합니다.

### <a name="how-many-ssl-certificates-does-application-gateway-support"></a>Application Gateway에서 SSL 인증서의 수를 지원 하나요?

Application Gateway는 최대 100 개의 SSL 인증서를 지원 합니다.

### <a name="how-many-authentication-certificates-for-backend-reencryption-does-application-gateway-support"></a>Application Gateway는 백 엔드 재 암호화에 대 한 몇 개의 인증 인증서를 지원 하나요?

Application Gateway는 최대 10 개의 인증 인증서를 지원 합니다. 기본값은 5입니다.

### <a name="does-application-gateway-natively-integrate-with-azure-key-vault"></a>Application Gateway를 Azure Key Vault를 사용 하 여 고유 하 게 통합은?

예, Application Gateway v2 SKU는 키 자격 증명 모음을 지원합니다. 자세한 내용은 [Key Vault 인증서를 사용 하 여 SSL 종료](key-vault-certs.md)합니다.

### <a name="how-do-i-configure-https-listeners-for-com-and-net-sites"></a>.Com 및.net 사이트에 대 한 HTTPS 수신기를 구성 하려면 어떻게 해야 하나요? 

여러 도메인 기반 (호스트 기반) 라우팅에 대 한 다중 사이트 수신기를 만들려면, HTTPS 프로토콜을 사용 하는 수신기를 설정 및 라우팅 규칙을 사용 하 여 수신기를 연결할 수 있습니다. 자세한 내용은 [Application Gateway를 사용 하 여 여러 사이트 호스팅](https://docs.microsoft.com/azure/application-gateway/multiple-site-overview)합니다. 

## <a name="configuration---web-application-firewall-waf"></a>구성-웹 응용 프로그램 방화벽 (WAF)

### <a name="does-the-waf-sku-offer-all-the-features-available-in-the-standard-sku"></a>WAF SKU는 표준 SKU에서 제공 되는 모든 기능을 제공 하나요?

예. WAF는 표준 SKU의 모든 기능을 지원합니다.

### <a name="which-crs-versions-does-application-gateway-support"></a>Application Gateway는 CRS 버전을 지원 하나요?

Application Gateway는 CRS [2.2.9](application-gateway-crs-rulegroups-rules.md#owasp229) 및 CRS [3.0](application-gateway-crs-rulegroups-rules.md#owasp30)을 지원합니다.

### <a name="how-do-i-monitor-waf"></a>WAF를 모니터링하려면 어떻게 하나요?

진단 로깅을 통해 WAF를 모니터링 합니다. 자세한 내용은 [진단 로깅 및 Application Gateway에 대 한 메트릭을](application-gateway-diagnostics.md)합니다.

### <a name="does-detection-mode-block-traffic"></a>검색 모드에서 트래픽을 차단하나요?

아니요. 검색 모드는 WAF 규칙을 트리거한 트래픽만을 로깅합니다.

### <a name="can-i-customize-waf-rules"></a>WAF 규칙을 사용자 지정할 수 있나요?

예. 자세한 내용은 [사용자 지정 WAF 규칙 그룹 및 규칙](application-gateway-customize-waf-rules-portal.md)합니다.

### <a name="what-rules-are-currently-available-for-waf"></a>어떤 규칙은 WAF에 대 한 현재 사용할 수 있습니까?

WAF CRS는 현재 지원 [2.2.9](application-gateway-crs-rulegroups-rules.md#owasp229) 하 고 [3.0](application-gateway-crs-rulegroups-rules.md#owasp30)합니다. 이러한 규칙에는 OWASP Open Web Application Security Project ()를 식별 하는 상위 10 개 취약점 대부분에 대해 보안 기준을 제공 합니다. 

* SQL 삽입 공격 보호
* 사이트 간 스크립팅 공격 보호
* 명령 삽입, HTTP 요청 밀 반입, HTTP 응답 분할 원격 파일 포함 공격 등 일반적인 웹 공격 으로부터 보호
* HTTP 프로토콜 위반 보호
* 누락된 호스트 사용자-에이전트 및 수락 헤더 같은 HTTP 프로토콜 이상 보호
* 보트, 크롤러 및 스캐너 방지
* 일반적인 응용 프로그램 구성 오류 (즉, Apache, IIS 및 등) 검색

자세한 내용은 [OWASP 상위 10 개 취약점](https://www.owasp.org/index.php/Top10#OWASP_Top_10_for_2013)합니다.

### <a name="does-waf-support-ddos-protection"></a>WAF는 DDoS 보호를 지원 하나요?

예. Application gateway를 배포 하는 가상 네트워크에서 DDoS protection을 사용할 수 있습니다. 이 설정은 Azure DDoS Protection 서비스는 응용 프로그램 게이트웨이 VIP (가상 IP)를 보호 하는 것을 보장 합니다.

### <a name="is-there-guidance-available-to-migrate-from-the-v1-sku-to-the-v2-sku"></a>이 지침 v1 SKU에서 v2 SKU로 마이그레이션에 사용할 수 있습니까?

예. 자세한 세부 정보를 참조 하십시오 [마이그레이션할 Azure Application Gateway 및 Web Application Firewall v1에서 v2로](migrate-v1-v2.md)합니다.

## <a name="diagnostics-and-logging"></a>진단 및 로깅

### <a name="what-types-of-logs-does-application-gateway-provide"></a>Application Gateway에서 어떤 유형의 로그를 제공 하나요?

Application Gateway는 세 가지 로그를 제공합니다. 

* **ApplicationGatewayAccessLog**: 액세스 로그는 application gateway 프런트 엔드에 제출 된 각 요청을 포함 합니다. 데이터 입출력 호출자의 IP, 요청 URL, 응답 대기 시간, 반환 코드 및 바이트 포함 합니다. 액세스 로그는 300 초 마다 수집 됩니다. 응용 프로그램 게이트웨이 당 하나의 레코드를 포함합니다.
* **ApplicationGatewayPerformanceLog**: 성능 로그는 각 application gateway에 대 한 성능 정보를 캡처합니다. 정보 바이트에 대 한 처리량, 정상 및 비정상 백 엔드 인스턴스 수 및 실패 한 요청 수, 총 요청을 처리 합니다.
* **ApplicationGatewayFirewallLog**: WAF를 사용 하 여 구성한 application gateway 방화벽 로그 검색 모드 또는 방지 모드를 통해 로깅된 요청을 포함 합니다.

자세한 내용은 [백 엔드 상태, 진단 로그 및 Application Gateway에 대 한 메트릭을](application-gateway-diagnostics.md)합니다.

### <a name="how-do-i-know-if-my-backend-pool-members-are-healthy"></a>내 백 엔드 풀 멤버가 정상인지 어떻게 알 수 있나요?

PowerShell cmdlet을 사용 하 여 상태를 확인할 `Get-AzApplicationGatewayBackendHealth` 또는 포털입니다. 자세한 내용은 [Application Gateway 진단](application-gateway-diagnostics.md)을 참조하세요.

### <a name="whats-the-retention-policy-for-the-diagnostic-logs"></a>진단 로그의 보존 정책은 무엇 인가요?

고객의 저장소 계정에 진단 로그 흐름입니다. 고객은 해당 기본 설정에 따라 보존 정책을 설정할 수 있습니다. 진단 로그는 또한 이벤트 허브 또는 Azure Monitor 로그를 보낼 수 있습니다. 자세한 내용은 [Application Gateway 진단](application-gateway-diagnostics.md)을 참조하세요.

### <a name="how-do-i-get-audit-logs-for-application-gateway"></a>Application Gateway에 대한 감사 로그를 어떻게 얻나요?

Application gateway의 메뉴 블레이드에서 포털에서 선택 **활동 로그** 감사 로그에 액세스할 수 있습니다. 

### <a name="can-i-set-alerts-with-application-gateway"></a>Application Gateway로 경고를 설정할 수 있나요?

예. Application Gateway에서 메트릭 경고 구성 됩니다. 자세한 내용은 [Application Gateway 메트릭](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics#metrics) 하 고 [경고 알림 받기](../monitoring-and-diagnostics/insights-receive-alert-notifications.md)합니다.

### <a name="how-do-i-analyze-traffic-statistics-for-application-gateway"></a>Application Gateway에 대한 트래픽 통계를 분석하려면 어떻게 해야 하나요?

확인 하 고 여러 가지 방법으로 액세스 로그를 분석할 수 있습니다. Azure Monitor 로그, Excel, Power BI 및 등을 사용 합니다.

설치 하 고 실행 널리 사용 되는 Resource Manager 템플릿을 사용할 수도 있습니다 [GoAccess](https://goaccess.io/) Application Gateway 액세스 로그에 대 한 분석기를 기록 합니다. GoAccess 고유 방문자, 요청 된 파일, 호스트, 운영 체제, 브라우저 및 HTTP 상태 코드와 같은 유용한 HTTP 트래픽 통계를 제공 합니다. 자세한 내용은 GitHub에 대 한 참조를 [Resource Manager 템플릿 폴더에 대 한 추가 정보 파일](https://aka.ms/appgwgoaccessreadme)합니다.

### <a name="what-could-cause-backend-health-to-return-an-unknown-status"></a>백 엔드 상태를 알 수 없는 상태를 반환할 원인을?

일반적으로 네트워크 보안 그룹 (NSG), 사용자 지정 DNS 또는 응용 프로그램 게이트웨이 서브넷에서 (UDR) 사용자 정의 라우팅을 백 엔드에 대 한 액세스 차단 되었을 때 알 수 없는 상태가 표시 됩니다. 자세한 내용은 [백 엔드 상태, 진단 로깅 및 Application Gateway에 대 한 메트릭을](application-gateway-diagnostics.md)합니다.

## <a name="next-steps"></a>다음 단계

Application Gateway에 대 한 자세한 내용은 참조 하세요 [Azure Application Gateway 란?](overview.md)합니다.
