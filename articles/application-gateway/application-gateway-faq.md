---
title: Azure 응용 프로그램 게이트웨이에 대한 자주 묻는 질문
description: Azure 응용 프로그램 게이트웨이에 대해 자주 묻는 질문에 대한 답변을 찾습니다.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 04/01/2020
ms.author: victorh
ms.openlocfilehash: 0e5a683c95e213a0fc37ec4f559e89abfecf034a
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/22/2020
ms.locfileid: "81758918"
---
# <a name="frequently-asked-questions-about-application-gateway"></a>애플리케이션 게이트웨이에 대한 자주 묻는 질문

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

다음은 Azure 응용 프로그램 게이트웨이에 대한 일반적인 질문입니다.

## <a name="general"></a>일반

### <a name="what-is-application-gateway"></a>Application Gateway란?

Azure 응용 프로그램 게이트웨이는 ADC(응용 프로그램 배달 컨트롤러)를 서비스로 제공합니다. 응용 프로그램에 대한 다양한 계층 7 로드 밸런싱 기능을 제공합니다. 이 서비스는 가용성이 높고 확장 가능하며 Azure에서 완전히 관리합니다.

### <a name="what-features-does-application-gateway-support"></a>Application Gateway에서 지원하는 기능은 어떤 것이 있나요?

애플리케이션 게이트웨이는 자동 크기 조정, TLS 오프로딩 및 종단 간 TLS, WAF(웹 응용 프로그램 방화벽), 쿠키 기반 세션 선호도, URL 경로 기반 라우팅, 다중 사이트 호스팅 및 기타 기능을 지원합니다. 지원되는 기능의 전체 목록은 [Application Gateway 소개](application-gateway-introduction.md)를 참조하세요.

### <a name="how-do-application-gateway-and-azure-load-balancer-differ"></a>응용 프로그램 게이트웨이와 Azure 로드 밸러블러는 어떻게 다른가요?

응용 프로그램 게이트웨이는 계층 7 로드 밸러블러이며, 이는 웹 트래픽(HTTP, HTTPS, WebSocket 및 HTTP/2)에서만 작동한다는 것을 의미합니다. TLS 종료, 쿠키 기반 세션 선호도 및 로드 밸런싱 트래픽을 위한 라운드 로빈과 같은 기능을 지원합니다. 로드 밸러저는 계층 4(TCP 또는 UDP)에서 트래픽을 균형 조정합니다.

### <a name="what-protocols-does-application-gateway-support"></a>Application Gateway에서 지원하는 프로토콜은 무엇인가요?

Application Gateway는 HTTP, HTTPS, HTTP/2 및 WebSocket을 지원합니다.

### <a name="how-does-application-gateway-support-http2"></a>Application Gateway는 어떻게 HTTP/2를 지원하나요?

[HTTP/2 지원을](https://docs.microsoft.com/azure/application-gateway/configuration-overview#http2-support)참조하십시오.

### <a name="what-resources-are-supported-as-part-of-a-backend-pool"></a>백 엔드 풀의 일부로 지원되는 리소스는 무엇입니까?

[지원되는 백 엔드 리소스를](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#backend-pools)참조하십시오.

### <a name="in-what-regions-is-application-gateway-available"></a>응용 프로그램 게이트웨이를 사용할 수 있는 지역은 무엇입니까?

Application Gateway는 Azure 전체의 모든 지역에서 사용할 수 있습니다. Azure [중국 21Vianet](https://www.azure.cn/) 및 Azure [정부에서도](https://azure.microsoft.com/overview/clouds/government/)사용할 수 있습니다.

### <a name="is-this-deployment-dedicated-for-my-subscription-or-is-it-shared-across-customers"></a>이 배포는 내 구독전용입니까, 아니면 고객 간에 공유되고 있습니까?

Application Gateway는 가상 네트워크에서 전용 배포입니다.

### <a name="does-application-gateway-support-http-to-https-redirection"></a>응용 프로그램 게이트웨이는 HTTP-HTTPS 리디렉션을 지원합니까?

리디렉션은 지원됩니다. [응용 프로그램 게이트웨이 리디렉션 개요를](application-gateway-redirect-overview.md)참조하십시오.

### <a name="in-what-order-are-listeners-processed"></a>수신기는 어떤 순서로 처리되나요?

수신기 [처리 순서를](https://docs.microsoft.com/azure/application-gateway/configuration-overview#order-of-processing-listeners)참조하십시오.

### <a name="where-do-i-find-the-application-gateway-ip-and-dns"></a>응용 프로그램 게이트웨이 IP 및 DNS는 어디에서 찾을 수 있습니까?

공용 IP 주소를 엔드포인트로 사용하는 경우 공용 IP 주소 리소스에서 IP 및 DNS 정보를 찾을 수 있습니다. 또는 응용 프로그램 게이트웨이의 개요 페이지에서 포털에서 찾을 수 있습니다. 내부 IP 주소를 사용하는 경우 개요 페이지에서 정보를 찾습니다.

v2 SKU의 경우 공용 IP 리소스를 열고 **구성을**선택합니다. **DNS 이름 레이블(선택 사항)** 필드를 사용하여 DNS 이름을 구성할 수 있습니다.

### <a name="what-are-the-settings-for-keep-alive-timeout-and-tcp-idle-timeout"></a>Keep-Alive 시간 아웃 및 TCP 유휴 시간 시간에 대한 설정은 무엇입니까?

*Keep-Alive 시간 아웃은* 응용 프로그램 게이트웨이가 클라이언트가 클라이언트를 다시 사용하거나 닫기 전에 영구 연결에서 다른 HTTP 요청을 보낼 때까지 기다리는 기간을 제어합니다. *TCP 유휴 시간 초과는* 활동이 없는 경우 TCP 연결이 열려 있는 기간을 제어합니다. 

응용 프로그램 게이트웨이 v1 SKU의 *Keep-Alive 시간 시간은* 120초이고 v2 SKU에서는 75초입니다. *TCP 유휴 시간 설정은* 응용 프로그램 게이트웨이의 v1 및 v2 SKU의 프런트 엔드 가상 IP(VIP)에서 4분 기본값입니다. 이러한 값은 변경할 수 없습니다.

### <a name="does-the-ip-or-dns-name-change-over-the-lifetime-of-the-application-gateway"></a>응용 프로그램 게이트웨이의 수명 동안 IP 또는 DNS 이름이 변경됩니까?

응용 프로그램 게이트웨이 V1 SKU에서 응용 프로그램 게이트웨이를 중지하고 시작하면 VIP가 변경될 수 있습니다. 그러나 응용 프로그램 게이트웨이와 연결된 DNS 이름은 게이트웨이의 수명 동안 변경되지 않습니다. DNS 이름은 변경되지 않으므로 CNAME 별칭을 사용하여 응용 프로그램 게이트웨이의 DNS 주소를 가리해야 합니다. 응용 프로그램 게이트웨이 V2 SKU에서 IP 주소를 정적으로 설정할 수 있으므로 IP 및 DNS 이름은 응용 프로그램 게이트웨이의 수명 동안 변경되지 않습니다. 

### <a name="does-application-gateway-support-static-ip"></a>Application Gateway에서 고정 IP를 지원하나요?

예. 응용 프로그램 게이트웨이 v2 SKU는 정적 공용 IP 주소를 지원합니다. v1 SKU는 고정 내부 IP를 지원합니다.

### <a name="does-application-gateway-support-multiple-public-ips-on-the-gateway"></a>Application Gateway가 게이트웨이에서 여러 공용 IP를 지원하나요?

응용 프로그램 게이트웨이는 하나의 공용 IP 주소만 지원합니다.

### <a name="how-large-should-i-make-my-subnet-for-application-gateway"></a>Application Gateway에 대해 얼마나 큰 서브넷을 만들어야 하나요?

[응용 프로그램 게이트웨이 서브넷 크기 고려 사항을](https://docs.microsoft.com/azure/application-gateway/configuration-overview#size-of-the-subnet)참조하십시오.

### <a name="can-i-deploy-more-than-one-application-gateway-resource-to-a-single-subnet"></a>둘 이상의 Application Gateway 리소스를 단일 서브넷에 배포할 수 있나요?

예. 지정된 응용 프로그램 게이트웨이 배포의 여러 인스턴스 외에도 다른 응용 프로그램 게이트웨이 리소스를 포함하는 기존 서브넷에 다른 고유한 응용 프로그램 게이트웨이 리소스를 프로비전할 수 있습니다.

단일 서브넷은 Standard_v2 표준 응용 프로그램 게이트웨이를 함께 지원할 수 없습니다.

### <a name="does-application-gateway-v2-support-user-defined-routes-udr"></a>애플리케이션 게이트웨이 v2가 UDR(사용자 정의 경로)을 지원합니까?

예, 하지만 특정 시나리오만 가능합니다. 자세한 내용은 [Application Gateway 구성 개요](configuration-overview.md#user-defined-routes-supported-on-the-application-gateway-subnet)를 참조하세요.

### <a name="does-application-gateway-support-x-forwarded-for-headers"></a>Application Gateway에서 x-forwarded-for 헤더를 지원하나요?

예. [요청에 대한 수정 사항을](https://docs.microsoft.com/azure/application-gateway/how-application-gateway-works#modifications-to-the-request)참조하십시오.

### <a name="how-long-does-it-take-to-deploy-an-application-gateway-will-my-application-gateway-work-while-its-being-updated"></a>응용 프로그램 게이트웨이를 배포하는 데 얼마나 걸하나요? 업데이트되는 동안 응용 프로그램 게이트웨이가 작동합니까?

새 Application Gateway v1 SKU 배포 시 프로비전하는 데 최대 20분이 걸릴 수 있습니다. 인스턴스 크기 또는 개수에 대한 변경은 중단되지 않으며 이 기간 동안 게이트웨이는 활성 상태로 유지됩니다.

v2 SKU를 사용하는 대부분의 배포는 프로비전하는 데 약 6분이 소요됩니다. 그러나 배포 유형에 따라 시간이 더 오래 걸릴 수 있습니다. 예를 들어 인스턴스가 많은 여러 가용성 영역에 대한 배포에는 6분 이상이 걸릴 수 있습니다. 

### <a name="can-i-use-exchange-server-as-a-backend-with-application-gateway"></a>Exchange Server를 응용 프로그램 게이트웨이의 백 엔드로 사용할 수 있습니까?

아니요. 응용 프로그램 게이트웨이는 SMTP, IMAP 및 POP3와 같은 전자 메일 프로토콜을 지원하지 않습니다.

### <a name="is-there-guidance-available-to-migrate-from-the-v1-sku-to-the-v2-sku"></a>v1 SKU에서 v2 SKU로 마이그레이션할 수 있는 지침이 있습니까?

예. 자세한 내용은 [Azure 응용 프로그램 게이트웨이 및 웹 응용 프로그램 방화벽을 v1에서 v2로 마이그레이션합니다.](migrate-v1-v2.md)

### <a name="will-the-application-gateway-v1-sku-continue-to-be-supported"></a>응용 프로그램 게이트웨이 v1 SKU가 계속 지원되나요?

예. 응용 프로그램 게이트웨이 v1 SKU는 계속 지원됩니다. 그러나 해당 SKU의 기능 업데이트를 활용하려면 v2로 이동하는 것이 좋습니다. 자세한 내용은 [자동 크기 조정 및 영역 중복 응용 프로그램 게이트웨이 v2를](application-gateway-autoscaling-zone-redundant.md)참조하십시오.

## <a name="performance"></a>성능

### <a name="how-does-application-gateway-support-high-availability-and-scalability"></a>Application Gateway는 고가용성과 확장성을 어떤 방식으로 지원하나요?

응용 프로그램 게이트웨이 v1 SKU는 두 개 이상의 인스턴스를 배포한 경우 고가용성 시나리오를 지원합니다. Azure는 이러한 인스턴스를 업데이트 및 오류 도메인에 분산하여 인스턴스가 모두 동시에 실패하지 않도록 합니다. v1 SKU는 로드를 공유하기 위해 동일한 게이트웨이의 여러 인스턴스를 추가하여 확장성을 지원합니다.

v2 SKU의 경우에는 여러 장애 도메인과 업데이트 도메인에 새 인스턴스가 자동으로 분산됩니다. 영역 중복성을 선택하면 최신 인스턴스도 가용성 영역에 분산되어 구역 오류 복원력을 제공합니다.

### <a name="how-do-i-achieve-a-dr-scenario-across-datacenters-by-using-application-gateway"></a>응용 프로그램 게이트웨이를 사용하여 데이터 센터 전체에서 DR 시나리오를 달성하려면 어떻게 해야 합니까?

트래픽 관리자를 사용하여 서로 다른 데이터 센터의 여러 응용 프로그램 게이트웨이에 트래픽을 배포합니다.

### <a name="does-application-gateway-support-autoscaling"></a>애플리케이션 게이트웨이가 자동 크기 조정을 지원합니까?

예. Application Gateway v2 SKU는 자동 크기 조정을 지원합니다. 자세한 내용은 [자동 크기 조정 및 영역 중복 응용 프로그램 게이트웨이를](application-gateway-autoscaling-zone-redundant.md)참조하십시오.

### <a name="does-manual-or-automatic-scale-up-or-scale-down-cause-downtime"></a>수동 또는 자동 확장 또는 축소로 인해 가동 중지 시간이 발생합니까?

아니요. 인스턴스가 업그레이드 도메인 및 장애 도메인 간에 배포됩니다.

### <a name="does-application-gateway-support-connection-draining"></a>Application Gateway는 연결 드레이닝을 지원하나요?

예. 연결 드레인을 설정하여 중단 없이 백 엔드 풀 내에서 멤버를 변경할 수 있습니다. 자세한 내용은 [응용 프로그램 게이트웨이의 연결 드레인 섹션을](features.md#connection-draining)참조하십시오.

### <a name="can-i-change-instance-size-from-medium-to-large-without-disruption"></a>중단 없이 인스턴스 크기를 중간에서 큼으로 변경할 수 있나요?

예.

## <a name="configuration"></a>구성

### <a name="is-application-gateway-always-deployed-in-a-virtual-network"></a>Application Gateway가 가상 네트워크에서 항상 배포되나요?

예. 응용 프로그램 게이트웨이는 항상 가상 네트워크 서브넷에 배포됩니다. 이 서브넷에는 응용 프로그램 게이트웨이만 포함될 수 있습니다. 자세한 내용은 [가상 네트워크 및 서브넷 요구 사항을](https://docs.microsoft.com/azure/application-gateway/configuration-overview#azure-virtual-network-and-dedicated-subnet)참조하십시오.

### <a name="can-application-gateway-communicate-with-instances-outside-of-its-virtual-network-or-outside-of-its-subscription"></a>애플리케이션 게이트웨이가 가상 네트워크 외부 또는 구독 외부의 인스턴스와 통신할 수 있습니까?

IP 연결이 있는 한 애플리케이션 게이트웨이는 IP에 있는 가상 네트워크 외부의 인스턴스와 통신할 수 있습니다. 응용 프로그램 게이트웨이는 구독이 있는 외부의 인스턴스와통신할 수도 있습니다. 내부 IP를 백 엔드 풀 멤버로 사용하려는 경우 [가상 네트워크 피어링](../virtual-network/virtual-network-peering-overview.md) 또는 [Azure VPN 게이트웨이를](../vpn-gateway/vpn-gateway-about-vpngateways.md)사용합니다.

### <a name="can-i-deploy-anything-else-in-the-application-gateway-subnet"></a>Application Gateway 서브넷에서 다른 항목을 배포할 수 있나요?

아니요. 그러나 서브넷에 다른 응용 프로그램 게이트웨이를 배포할 수 있습니다.

### <a name="are-network-security-groups-supported-on-the-application-gateway-subnet"></a>응용 프로그램 게이트웨이 서브넷에서 네트워크 보안 그룹이 지원되나요?

[응용 프로그램 게이트웨이 서브넷의 네트워크 보안 그룹을](https://docs.microsoft.com/azure/application-gateway/configuration-overview#network-security-groups-on-the-application-gateway-subnet)참조하십시오.

### <a name="does-the-application-gateway-subnet-support-user-defined-routes"></a>응용 프로그램 게이트웨이 서브넷이 사용자 정의 경로를 지원합니까?

[응용 프로그램 게이트웨이 서브넷에서 지원되는 사용자 정의 경로를](https://docs.microsoft.com/azure/application-gateway/configuration-overview#user-defined-routes-supported-on-the-application-gateway-subnet)참조하십시오.

### <a name="what-are-the-limits-on-application-gateway-can-i-increase-these-limits"></a>Application Gateway에서 한도는 어떻게 되나요? 이러한 한도를 늘릴 수 있나요?

[응용 프로그램 게이트웨이 제한](../azure-resource-manager/management/azure-subscription-service-limits.md#application-gateway-limits)을 참조하십시오.

### <a name="can-i-simultaneously-use-application-gateway-for-both-external-and-internal-traffic"></a>외부 트래픽과 내부 트래픽 모두에 응용 프로그램 게이트웨이를 동시에 사용할 수 있습니까?

예. 애플리케이션 게이트웨이는 애플리케이션 게이트웨이당 하나의 내부 IP와 하나의 외부 IP를 지원합니다.

### <a name="does-application-gateway-support-virtual-network-peering"></a>애플리케이션 게이트웨이가 가상 네트워크 피어링을 지원합니까?

예. 가상 네트워크 피어링은 다른 가상 네트워크의 트래픽 로드 균형을 유지하는 데 도움이 됩니다.

### <a name="can-i-talk-to-on-premises-servers-when-theyre-connected-by-expressroute-or-vpn-tunnels"></a>익스프레스루트 또는 VPN 터널로 연결된 온-프레미스 서버와 대화할 수 있습니까?

예, 트래픽이 허용되기만 한다면 가능합니다.

### <a name="can-one-backend-pool-serve-many-applications-on-different-ports"></a>하나의 백 엔드 풀이 다른 포트에서 많은 응용 프로그램을 제공할 수 있습니까?

마이크로 서비스 아키텍처가 지원됩니다. 다른 포트에서 프로브하려면 여러 HTTP 설정을 구성해야 합니다.

### <a name="do-custom-probes-support-wildcards-or-regex-on-response-data"></a>사용자 지정 프로브가 응답 데이터에 와일드카드 또는 정규식을 지원합니까?

아니요. 

### <a name="how-are-routing-rules-processed-in-application-gateway"></a>응용 프로그램 게이트웨이에서 라우팅 규칙은 어떻게 처리되나요?

[처리 규칙 순서를](https://docs.microsoft.com/azure/application-gateway/configuration-overview#order-of-processing-rules)참조하십시오.

### <a name="for-custom-probes-what-does-the-host-field-signify"></a>사용자 지정 프로브의 경우 호스트 필드는 무엇을 의미합니까?

호스트 필드는 응용 프로그램 게이트웨이에서 다중 사이트를 구성할 때 프로브를 보낼 이름을 지정합니다. 그렇지 않으면 '127.0.0.1'을 사용합니다. 이 값은 가상 시스템 호스트 이름과 다릅니다. 그 \<형식은\>\<프로토콜입니다\<\>\<.\>\>

### <a name="can-i-allow-application-gateway-access-to-only-a-few-source-ip-addresses"></a>몇 개의 소스 IP 주소에만 응용 프로그램 게이트웨이 액세스를 허용할 수 있습니까?

예. [특정 소스 IP에 대한 액세스 제한을](https://docs.microsoft.com/azure/application-gateway/configuration-overview#allow-application-gateway-access-to-a-few-source-ips)참조하십시오.

### <a name="can-i-use-the-same-port-for-both-public-facing-and-private-facing-listeners"></a>공용 및 개인 용 수신기 모두에 동일한 포트를 사용할 수 있습니까?

아니요.

### <a name="does-application-gateway-support-ipv6"></a>애플리케이션 게이트웨이가 IPv6를 지원합니까?

응용 프로그램 게이트웨이 v2는 현재 IPv6를 지원하지 않습니다. IPv4만 사용하여 이중 스택 VNet에서 작동할 수 있지만 게이트웨이 서브넷은 IPv4 전용이어야 합니다. 응용 프로그램 게이트웨이 v1은 듀얼 스택 VNet을 지원하지 않습니다. 

## <a name="configuration---tls"></a>구성 - TLS

### <a name="what-certificates-does-application-gateway-support"></a>응용 프로그램 게이트웨이가 지원하는 인증서는 무엇입니까?

응용 프로그램 게이트웨이는 자체 서명된 인증서, CA(인증 기관) 인증서, 확장 유효성 검사(EV) 인증서 및 와일드카드 인증서를 지원합니다.

### <a name="what-cipher-suites-does-application-gateway-support"></a>응용 프로그램 게이트웨이가 지원하는 암호 제품군은 무엇입니까?

응용 프로그램 게이트웨이는 다음 암호 제품군을 지원합니다. 

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

TLS 옵션을 사용자 지정하는 방법에 대한 자세한 내용은 [응용 프로그램 게이트웨이에서 TLS 정책 버전 및 암호 제품군 구성을](application-gateway-configure-ssl-policy-powershell.md)참조하십시오.

### <a name="does-application-gateway-support-reencryption-of-traffic-to-the-backend"></a>응용 프로그램 게이트웨이는 백 엔드에 대한 트래픽의 재암호화를 지원합니까?

예. 응용 프로그램 게이트웨이는 백 엔드에 대한 트래픽을 다시 암호화하는 TLS 오프로드 및 종단 간 TLS를 지원합니다.

### <a name="can-i-configure-tls-policy-to-control-tls-protocol-versions"></a>TLS 프로토콜 버전을 제어하도록 TLS 정책을 구성할 수 있습니까?

예. TLS1.0, TLS1.1 및 TLS1.2를 거부하도록 응용 프로그램 게이트웨이를 구성할 수 있습니다. 기본적으로 SSL 2.0 및 3.0은 이미 비활성화되어 있으며 구성할 수 없습니다.

### <a name="can-i-configure-cipher-suites-and-policy-order"></a>암호 그룹 및 정책 순서를 구성할 수 있나요?

예. 응용 프로그램 게이트웨이에서 [암호 제품군을 구성할](application-gateway-ssl-policy-overview.md)수 있습니다. 사용자 지정 정책을 정의하려면 다음 암호 제품군 중 하나 이상을 사용하도록 설정합니다. 

* TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256 
* TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256
* TLS_DHE_RSA_WITH_AES_128_GCM_SHA256
* TLS_RSA_WITH_AES_128_GCM_SHA256
* TLS_RSA_WITH_AES_256_CBC_SHA256
* TLS_RSA_WITH_AES_128_CBC_SHA256

응용 프로그램 게이트웨이는 SHA256을 백 엔드 관리를 위해 사용합니다.

### <a name="how-many-tlsssl-certificates-does-application-gateway-support"></a>응용 프로그램 게이트웨이가 지원하는 TLS/SSL 인증서는 몇 개입니까?

애플리케이션 게이트웨이는 최대 100개의 TLS/SSL 인증서를 지원합니다.

### <a name="how-many-authentication-certificates-for-backend-reencryption-does-application-gateway-support"></a>응용 프로그램 게이트웨이가 지원하는 백 엔드 재암호화에 대한 인증 인증서는 몇 개입니까?

응용 프로그램 게이트웨이는 최대 100개의 인증 인증서를 지원합니다.

### <a name="does-application-gateway-natively-integrate-with-azure-key-vault"></a>응용 프로그램 게이트웨이는 기본적으로 Azure 키 볼트와 통합됩니까?

예. 응용 프로그램 게이트웨이 v2 SKU는 키 볼트를 지원합니다. 자세한 내용은 [Key Vault 인증서를 통해 TLS 종료를](key-vault-certs.md)참조하십시오.

### <a name="how-do-i-configure-https-listeners-for-com-and-net-sites"></a>.com 및 .net 사이트에 대한 HTTPS 수신기를 구성하려면 어떻게 해야 합니까? 

여러 도메인 기반(호스트 기반) 라우팅의 경우 다중 사이트 리스너를 만들고, HTTPS를 프로토콜로 사용하는 수신기를 설정하고, 수신기를 라우팅 규칙과 연결할 수 있습니다. 자세한 내용은 응용 프로그램 게이트웨이 를 [사용하여 여러 사이트 호스팅을](https://docs.microsoft.com/azure/application-gateway/multiple-site-overview)참조하십시오.

### <a name="can-i-use-special-characters-in-my-pfx-file-password"></a>.pfx 파일 암호에 특수 문자를 사용할 수 있습니까?

아니요, .pfx 파일 암호에는 숫자 문자만 사용하십시오.

## <a name="configuration---web-application-firewall-waf"></a>구성 - 웹 응용 프로그램 방화벽(WAF)

### <a name="does-the-waf-sku-offer-all-the-features-available-in-the-standard-sku"></a>WAF SKU는 표준 SKU에서 사용할 수 있는 모든 기능을 제공합니까?

예. WAF는 표준 SKU의 모든 기능을 지원합니다.

### <a name="how-do-i-monitor-waf"></a>WAF를 모니터링하려면 어떻게 하나요?

진단 로깅을 통해 WAF를 모니터링합니다. 자세한 내용은 [응용 프로그램 게이트웨이에 대한 진단 로깅 및 메트릭을](application-gateway-diagnostics.md)참조하십시오.

### <a name="does-detection-mode-block-traffic"></a>검색 모드에서 트래픽을 차단하나요?

아니요. 검색 모드는 WAF 규칙을 트리거하는 트래픽만 기록합니다.

### <a name="can-i-customize-waf-rules"></a>WAF 규칙을 사용자 지정할 수 있나요?

예. 자세한 내용은 [WAF 규칙 그룹 및 규칙 사용자 지정을](application-gateway-customize-waf-rules-portal.md)참조하십시오.

### <a name="what-rules-are-currently-available-for-waf"></a>현재 WAF에 사용할 수 있는 규칙은 무엇입니까?

WAF는 현재 [CRS 2.2.9,](../web-application-firewall/ag/application-gateway-crs-rulegroups-rules.md#owasp229) [3.0](../web-application-firewall/ag/application-gateway-crs-rulegroups-rules.md#owasp30)및 [3.1을](../web-application-firewall/ag/application-gateway-crs-rulegroups-rules.md#owasp31)지원합니다. 이러한 규칙은 OWASP(개방형 웹 응용 프로그램 보안 프로젝트)가 식별하는 상위 10개 취약점 대부분에 대해 기준 보안을 제공합니다. 

* SQL 삽입 공격 보호
* 교차 사이트 스크립팅 보호
* 명령 주입, HTTP 요청 밀수, HTTP 응답 분할 및 원격 파일 포함 공격과 같은 일반적인 웹 공격에 대한 보호
* HTTP 프로토콜 위반 보호
* 누락된 호스트 사용자-에이전트 및 수락 헤더 같은 HTTP 프로토콜 이상 보호
* 보트, 크롤러 및 스캐너 방지
* 일반적인 응용 프로그램 잘못된 구성 감지(즉, 아파치, IIS 등)

자세한 내용은 [OWASP 상위 10개 취약점을](https://www.owasp.org/index.php/Top10#OWASP_Top_10_for_2013)참조하십시오.

### <a name="does-waf-support-ddos-protection"></a>WAF가 DDoS 보호를 지원합니까?

예. 응용 프로그램 게이트웨이가 배포된 가상 네트워크에서 DDoS 보호를 활성화할 수 있습니다. 이 설정을 사용하면 Azure DDoS 보호 서비스가 응용 프로그램 게이트웨이 가상 IP(VIP)도 보호합니다.

## <a name="configuration---ingress-controller-for-aks"></a>구성 - AKS용 컨트롤러 를 가져옵니다.

### <a name="what-is-an-ingress-controller"></a>인그레스 컨트롤러란?

Kubernetes를 사용하면 `deployment` `service` 클러스터 내부에 포드 그룹을 노출할 수 있는 리소스와 생성을 사용할 수 있습니다. 동일한 서비스를 외부에 노출하기 [`Ingress`](https://kubernetes.io/docs/concepts/services-networking/ingress/) 위해 로드 분산, TLS 종료 및 이름 기반 가상 호스팅을 제공하는 리소스가 정의됩니다.
이 `Ingress` 리소스를 충족하려면 `Ingress` 리소스의 변경 내용을 수신하고 로드 밸런서 정책을 구성하는 수신 컨트롤러가 필요합니다.

응용 프로그램 게이트웨이 침투 컨트롤러를 사용하면 [Azure 응용 프로그램 게이트웨이를](https://azure.microsoft.com/services/application-gateway/) AKS 클러스터라고도 하는 [Azure Kubernetes 서비스의](https://azure.microsoft.com/services/kubernetes-service/) 침투로 사용할 수 있습니다.

### <a name="can-a-single-ingress-controller-instance-manage-multiple-application-gateways"></a>단일 인적 컨트롤러 인스턴스가 여러 애플리케이션 게이트웨이를 관리할 수 있습니까?

현재 한 인스턴스의 인그레스 컨트롤러는 하나의 응용 프로그램 게이트웨이에만 연결할 수 있습니다.

## <a name="diagnostics-and-logging"></a>진단 및 로깅

### <a name="what-types-of-logs-does-application-gateway-provide"></a>응용 프로그램 게이트웨이에서 제공하는 로그 유형은 무엇입니까?

응용 프로그램 게이트웨이는 세 개의 로그를 제공합니다. 

* **ApplicationGatewayAccessLog**: 액세스 로그에는 응용 프로그램 게이트웨이 프런트 엔드에 제출된 각 요청이 포함됩니다. 데이터에는 호출자의 IP, 요청된 URL, 응답 대기 시간, 반환 코드 및 바이트가 포함됩니다. 응용 프로그램 게이트웨이당 하나의 레코드가 포함되어 있습니다.
* **ApplicationGatewayPerformanceLog**: 성능 로그는 각 응용 프로그램 게이트웨이에 대한 성능 정보를 캡처합니다. 정보에는 바이트의 처리량, 제공된 총 요청, 실패한 요청 수 및 정상 및 비정상 백 엔드 인스턴스 수가 포함됩니다.
* **ApplicationGatewayFirewallLog**: WAF로 구성하는 응용 프로그램 게이트웨이의 경우 방화벽 로그에 검색 모드 또는 예방 모드를 통해 기록되는 요청이 포함되어 있습니다.

모든 로그는 60초마다 수집됩니다. 자세한 내용은 [응용 프로그램 게이트웨이에 대한 백 엔드 상태, 진단 로그 및 메트릭을](application-gateway-diagnostics.md)참조하십시오.

### <a name="how-do-i-know-if-my-backend-pool-members-are-healthy"></a>내 백 엔드 풀 멤버가 정상인지 어떻게 알 수 있나요?

PowerShell cmdlet `Get-AzApplicationGatewayBackendHealth` 또는 포털을 사용하여 상태를 확인합니다. 자세한 내용은 [응용 프로그램 게이트웨이 진단을](application-gateway-diagnostics.md)참조하십시오.

### <a name="whats-the-retention-policy-for-the-diagnostic-logs"></a>진단 로그의 보존 정책은 무엇입니까?

진단 로그는 고객의 저장소 계정으로 흐르게 됩니다. 고객은 기본 설정에 따라 보존 정책을 설정할 수 있습니다. 진단 로그는 이벤트 허브 또는 Azure Monitor 로그로 보낼 수도 있습니다. 자세한 내용은 [응용 프로그램 게이트웨이 진단을](application-gateway-diagnostics.md)참조하십시오.

### <a name="how-do-i-get-audit-logs-for-application-gateway"></a>Application Gateway에 대한 감사 로그를 어떻게 얻나요?

포털에서 응용 프로그램 게이트웨이의 메뉴 블레이드에서 **활동 로그를** 선택하여 감사 로그에 액세스합니다. 

### <a name="can-i-set-alerts-with-application-gateway"></a>Application Gateway로 경고를 설정할 수 있나요?

예. 응용 프로그램 게이트웨이에서 경고는 메트릭에 대해 구성됩니다. 자세한 내용은 [응용 프로그램 게이트웨이 메트릭](https://docs.microsoft.com/azure/application-gateway/application-gateway-metrics) 및 경고 알림 [수신](../monitoring-and-diagnostics/insights-receive-alert-notifications.md)을 참조하십시오.

### <a name="how-do-i-analyze-traffic-statistics-for-application-gateway"></a>Application Gateway에 대한 트래픽 통계를 분석하려면 어떻게 해야 하나요?

여러 가지 방법으로 액세스 로그를 보고 분석할 수 있습니다. Azure 모니터 로그, Excel, Power BI 등을 사용합니다.

응용 프로그램 게이트웨이 액세스 로그에 대 한 인기 있는 [GoAccess](https://goaccess.io/) 로그 분석기를 설치 하 고 실행 하는 리소스 관리자 템플릿을 사용할 수도 있습니다. GoAccess는 고유 방문자, 요청된 파일, 호스트, 운영 체제, 브라우저 및 HTTP 상태 코드와 같은 중요한 HTTP 트래픽 통계를 제공합니다. 자세한 내용은 GitHub에서 리소스 [관리자 템플릿 폴더의 Readme 파일을](https://aka.ms/appgwgoaccessreadme)참조하십시오.

### <a name="what-could-cause-backend-health-to-return-an-unknown-status"></a>백엔드 상태가 알 수 없는 상태를 반환하는 원인은 무엇입니까?

일반적으로 응용 프로그램 게이트웨이 서브넷에서 네트워크 보안 그룹(NSG), 사용자 지정 DNS 또는 사용자 정의 라우팅(UDR)에 의해 백 엔드에 대한 액세스가 차단되면 알 수 없는 상태가 표시됩니다. 자세한 내용은 [응용 프로그램 게이트웨이에 대한 백 엔드 상태, 진단 로깅 및 메트릭을](application-gateway-diagnostics.md)참조하십시오.

### <a name="is-there-any-case-where-nsg-flow-logs-wont-show-allowed-traffic"></a>NSG 흐름 로그에 허용된 트래픽이 표시되지 않는 경우가 있습니까?

예. 다음 시나리오에서 구성이 일치하는 경우 NSG 흐름 로그에서 허용된 트래픽이 표시되지 않습니다.
- 응용 프로그램 게이트웨이 v2를 배포했습니다.
- 응용 프로그램 게이트웨이 서브넷에 NSG가 있습니다.
- 해당 NSG에 NSG 흐름 로그를 사용하도록 설정했습니다.

### <a name="how-do-i-use-application-gateway-v2-with-only-private-frontend-ip-address"></a>개인 프런트 엔드 IP 주소만 있는 응용 프로그램 게이트웨이 V2를 사용하려면 어떻게 해야 합니까?

응용 프로그램 게이트웨이 V2는 현재 개인 IP 모드만 지원하지 않습니다. 다음과 같은 조합을 지원합니다
* 프라이빗 IP 및 퍼블릭 IP
* 공용 IP만

그러나 개인 IP만 사용하여 응용 프로그램 게이트웨이 V2를 사용하려면 아래 프로세스를 따를 수 있습니다.
1. 공용 및 개인 프런트 엔드 IP 주소를 모두 사용하여 응용 프로그램 게이트웨이 만들기
2. 공용 프런트 엔드 IP 주소에 대한 수신기를 만들지 마십시오. 응용 프로그램 게이트웨이는 수신기가 만들어지지 않으면 공용 IP 주소의 트래픽을 수신하지 않습니다.
3. 응용 프로그램 게이트웨이 서브넷에 대한 [네트워크 보안 그룹을](https://docs.microsoft.com/azure/virtual-network/security-overview) 만들고 우선 순위에 따라 다음 구성을 첨부합니다.
    
    a. 소스에서 **GatewayManager** 서비스 태그로 트래픽을 허용하고 대상을 **Any** 및 대상 포트로 **65200-65535로**허용합니다. 이 포트 범위는 Azure 인프라 통신에 필요합니다. 이러한 포트는 인증서 인증에 의해 보호(잠김)됩니다. 게이트웨이 사용자 관리자를 포함한 외부 엔터티는 적절한 인증서가 없으면 해당 끝점에서 변경을 시작할 수 없습니다.
    
    b. **AzureLoadBalancer** 서비스 태그 및 대상 포트를 **Any로** 원본에서 트래픽을 허용
    
    다. 소스에서 모든 인바운드 트래픽을 **인터넷** 서비스 태그로 거부하고 대상 포트를 **Any로**거부합니다. 인바운드 규칙에서 이 규칙에 *가장 낮은 우선 순위를* 부여합니다.
    
    d. 개인 IP 주소의 액세스가 차단되지 않도록 가상 네트워크 인바운드 허용과 같은 기본 규칙을 유지합니다.
    
    e. 아웃바운드 인터넷 연결은 차단할 수 없습니다. 그렇지 않으면 로깅, 메트릭 등의 문제에 직면하게 됩니다.

개인 IP 전용 액세스를 위한 ![샘플 NSG 구성: 개인 IP 액세스용 응용 프로그램 게이트웨이 V2 NSG 구성](./media/application-gateway-faq/appgw-privip-nsg.png)

### <a name="does-application-gateway-affinity-cookie-support-samesite-attribute"></a>응용 프로그램 게이트웨이 선호도 쿠키가 SameSite 특성을 지원합니까?
예, [크롬 브라우저](https://www.chromium.org/Home) [v80 업데이트는](https://chromiumdash.appspot.com/schedule) SameSite= Lax로 처리될 동일사이트 특성없이 HTTP 쿠키에 대한 위임을 도입했습니다. 즉, 응용 프로그램 게이트웨이 선호도 쿠키는 타사 컨텍스트에서 브라우저에서 전송되지 않습니다. 이 시나리오를 지원하기 위해 응용 프로그램 게이트웨이는 기존 응용 프로그램 *게이트웨이Affinity* 쿠키 외에 *응용 프로그램 게이트웨이AffinityCORS라는* 또 다른 쿠키를 삽입합니다.  이러한 쿠키는 비슷하지만 *ApplicationGatewayAffinityCORS* 쿠키에는 두 가지 속성이 추가되었습니다. * 보안*. 이러한 특성은 원본 간 요청에 대해서도 고정 세션을 유지합니다. 자세한 내용은 [쿠키 기반 선호도 섹션을](configuration-overview.md#cookie-based-affinity) 참조하십시오.

## <a name="next-steps"></a>다음 단계

응용 프로그램 게이트웨이에 대한 자세한 내용은 [Azure 응용 프로그램 게이트웨이란 무엇입니까?](overview.md)
