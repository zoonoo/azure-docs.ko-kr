---
title: Azure Application Gateway에 대한 질문과 대답
description: Azure Application Gateway에 대한 질문과 대답을 찾아보세요.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 05/26/2020
ms.author: victorh
ms.openlocfilehash: fd5617af2da9aa00cb75deb82f83be29db78d79d
ms.sourcegitcommit: 64fc70f6c145e14d605db0c2a0f407b72401f5eb
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/27/2020
ms.locfileid: "83873498"
---
# <a name="frequently-asked-questions-about-application-gateway"></a>Application Gateway에 대한 질문과 대답입니다.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

다음은 Azure Application Gateway에 대해 자주 묻는 질문입니다.

## <a name="general"></a>일반

### <a name="what-is-application-gateway"></a>Application Gateway란?

Azure Application Gateway는 ADC(애플리케이션 제공 컨트롤러)를 서비스로 제공합니다. 애플리케이션에 다양한 레이어 7 부하 분산 기능을 제공합니다. 이 서비스는 Azure에서 관리하는 고가용성의 확장성 있는 완전 관리형 서비스입니다.

### <a name="what-features-does-application-gateway-support"></a>Application Gateway에서 지원하는 기능은 어떤 것이 있나요?

Application Gateway는 자동 스케일링, TLS 오프로딩, 엔드투엔드 TLS, WAF(웹 애플리케이션 방화벽), 쿠키 기반 세션 선호도, URL 경로 기반 라우팅, 다중 사이트 호스팅 및 기타 기능을 지원합니다. 지원되는 기능의 전체 목록은 [Application Gateway 소개](application-gateway-introduction.md)를 참조하세요.

### <a name="how-do-application-gateway-and-azure-load-balancer-differ"></a>Application Gateway와 Azure Load Balancer는 어떻게 다른가요?

Application Gateway는 웹 트래픽(HTTP, HTTPS, WebSocket 및 HTTP/2)에서만 작동하는 레이어 7 부하 분산 장치로써 TLS 종료, 쿠키 기반 세션 선호도, 트래픽 부하 분산을 위한 라운드 로빈 등의 기능을 지원합니다. Load Balancer는 레이어 4(TCP 또는 UDP)에서 트래픽 부하를 분산합니다.

### <a name="what-protocols-does-application-gateway-support"></a>Application Gateway에서 지원하는 프로토콜은 무엇인가요?

Application Gateway는 HTTP, HTTPS, HTTP/2 및 WebSocket을 지원합니다.

### <a name="how-does-application-gateway-support-http2"></a>Application Gateway는 어떻게 HTTP/2를 지원하나요?

[HTTP/2 지원](https://docs.microsoft.com/azure/application-gateway/configuration-overview#http2-support)을 참조하세요.

### <a name="what-resources-are-supported-as-part-of-a-backend-pool"></a>백 엔드 풀의 일부로 어떤 리소스가 지원되나요?

[지원되는 백 엔드 리소스](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#backend-pools)를 참조하세요.

### <a name="in-what-regions-is-application-gateway-available"></a>Application Gateway를 사용할 수 있는 지역은 어디인가요?

Application Gateway는 Azure 전체의 모든 지역에서 사용할 수 있습니다. [Azure 중국 21Vianet](https://www.azure.cn/) 및 [Azure Government](https://azure.microsoft.com/overview/clouds/government/)에서도 사용할 수 있습니다.

### <a name="is-this-deployment-dedicated-for-my-subscription-or-is-it-shared-across-customers"></a>이 배포가 내 구독 전용인가요 아니면 고객 사이에서 공유되나요?

Application Gateway는 가상 네트워크에서 전용 배포입니다.

### <a name="does-application-gateway-support-http-to-https-redirection"></a>Application Gateway는 HTTP-HTTPS 리디렉션을 지원하나요?

리디렉션은 지원됩니다. [Application Gateway 리디렉션 개요](application-gateway-redirect-overview.md)를 참조하세요.

### <a name="in-what-order-are-listeners-processed"></a>수신기는 어떤 순서로 처리되나요?

[수신기 처리 순서](https://docs.microsoft.com/azure/application-gateway/configuration-overview#order-of-processing-listeners)를 참조하세요.

### <a name="where-do-i-find-the-application-gateway-ip-and-dns"></a>Application Gateway IP 및 DNS는 어디서 찾나요?

공용 IP 주소를 엔드포인트로 사용하는 경우 공용 IP 주소 리소스에서 IP 및 DNS 정보를 찾을 수 있습니다. 또는 포털의 애플리케이션 게이트웨이에 대한 개요 페이지에서 찾을 수 있습니다. 내부 IP 주소를 사용하는 경우 개요 페이지에서 정보를 확인하세요.

v2 SKU의 경우 공용 IP 리소스를 열고 **구성**을 선택합니다. **DNS 이름 레이블(옵션)** 필드를 사용하여 DNS 이름을 구성할 수 있습니다.

### <a name="what-are-the-settings-for-keep-alive-timeout-and-tcp-idle-timeout"></a>Keep-Alive 시간 제한 및 TCP 유휴 시간 제한의 설정은 어떻게 되나요?

*Keep-Alive 시간 제한*은 클라이언트가 영구 연결에 대한 요청을 다른 HTTP 요청으로 보낼 때까지 Application Gateway가 대기하는 시간을 조정합니다. 이 시간이 지나면 Application Gateway가 연결을 재사용하거나 종료합니다. *TCP 유휴 시간 제한*은 작업이 없을 때 TCP 연결이 열린 상태로 유지되는 시간을 제어합니다. 

Application Gateway v1 SKU의 *Keep-Alive 시간 제한*은 120초이고 v2 SKU에서는 75초입니다. *TCP 유휴 시간 제한*은 Application Gateway v1 및 v2 SKU의 프런트 엔드 VIP(가상 IP)에서 기본적으로 4분입니다. 이 값을 변경할 수 없습니다.

### <a name="does-the-ip-or-dns-name-change-over-the-lifetime-of-the-application-gateway"></a>애플리케이션 게이트웨이의 수명 주기 중에 IP 또는 DNS 이름이 변경되나요?

Application Gateway V1 SKU에서는 애플리케이션 게이트웨이를 중지하고 시작할 때 VIP가 변경될 수 있습니다. 그러나 애플리케이션 게이트웨이와 연결된 DNS 이름은 게이트웨이 수명 주기 중에 변경되지 않습니다. DNS 이름은 변경되지 않으므로 CNAME 별칭을 사용하고 해당 별칭이 애플리케이션 게이트웨이의 DNS 주소를 가리키도록 설정해야 합니다. Application Gateway V2 SKU에서는 IP 주소를 고정 IP로 설정할 수 있으므로, 애플리케이션 게이트웨이의 수명 주기 동안 IP 및 DNS 이름이 변경되지 않습니다. 

### <a name="does-application-gateway-support-static-ip"></a>Application Gateway에서 고정 IP를 지원하나요?

예, Application Gateway v2 SKU는 고정 공용 IP 주소를 지원합니다. v1 SKU는 고정 내부 IP를 지원합니다.

### <a name="does-application-gateway-support-multiple-public-ips-on-the-gateway"></a>Application Gateway가 게이트웨이에서 여러 공용 IP를 지원하나요?

애플리케이션 게이트웨이는 공용 IP 주소를 하나만 지원합니다.

### <a name="how-large-should-i-make-my-subnet-for-application-gateway"></a>Application Gateway에 대해 얼마나 큰 서브넷을 만들어야 하나요?

[Application Gateway 서브넷 크기 고려 사항](https://docs.microsoft.com/azure/application-gateway/configuration-overview#size-of-the-subnet)을 참조하세요.

### <a name="can-i-deploy-more-than-one-application-gateway-resource-to-a-single-subnet"></a>둘 이상의 Application Gateway 리소스를 단일 서브넷에 배포할 수 있나요?

예. 지정된 Application Gateway 배포의 여러 인스턴스 외에도, 다른 애플리케이션 게이트웨이 리소스를 포함하는 기존 서브넷에 다른 고유한 Application Gateway 리소스를 프로비저닝할 수 있습니다.

단일 서브넷은 v2 및 v1 Application Gateway SKU를 둘 다 지원할 수 없습니다.

### <a name="does-application-gateway-v2-support-user-defined-routes-udr"></a>Application Gateway v2는 UDR(사용자 정의 경로)을 지원하나요?

예, 하지만 특정 시나리오에서만 지원합니다. 자세한 내용은 [Application Gateway 구성 개요](configuration-overview.md#user-defined-routes-supported-on-the-application-gateway-subnet)를 참조하세요.

### <a name="does-application-gateway-support-x-forwarded-for-headers"></a>Application Gateway에서 x-forwarded-for 헤더를 지원하나요?

예. [요청 수정](https://docs.microsoft.com/azure/application-gateway/how-application-gateway-works#modifications-to-the-request)을 참조하세요.

### <a name="how-long-does-it-take-to-deploy-an-application-gateway-will-my-application-gateway-work-while-its-being-updated"></a>애플리케이션 게이트웨이를 배포하는 데 얼마나 걸리나요? 애플리케이션 게이트웨이를 업데이트하는 중에도 작동하나요?

새 Application Gateway v1 SKU 배포 시 프로비전하는 데 최대 20분이 걸릴 수 있습니다. 인스턴스 크기 또는 수를 변경해도 중단되지 않으며, 게이트웨이는 이 시간 동안 활성 상태를 유지합니다.

v2 SKU를 사용하는 대부분의 배포는 프로비저닝하는 데 약 6분이 소요됩니다. 그러나 배포 유형에 따라 시간이 더 오래 걸릴 수 있습니다. 예를 들어 여러 인스턴스가 있는 여러 가용성 영역에 배포하는 경우 6분 넘게 걸릴 수 있습니다. 

### <a name="can-i-use-exchange-server-as-a-backend-with-application-gateway"></a>Exchange Server를 Application Gateway와 함께 백 엔드로 사용할 수 있나요?

아니요. Application Gateway는 SMTP, IMAP, POP3 등의 이메일 프로토콜을 지원하지 않습니다.

### <a name="is-there-guidance-available-to-migrate-from-the-v1-sku-to-the-v2-sku"></a>v1 SKU에서 v2 SKU로 마이그레이션할 때 사용할 수 있는 지침이 있나요?

예. 자세한 내용은 [Azure Application Gateway 및 웹 애플리케이션 방화벽을 v1에서 v2로 마이그레이션](migrate-v1-v2.md)을 참조하세요.

### <a name="will-the-application-gateway-v1-sku-continue-to-be-supported"></a>Application Gateway v1 SKU는 계속 지원되나요?

예. Application Gateway v1 SKU는 계속 지원됩니다. 그러나 해당 SKU의 기능 업데이트를 활용하려면 v2로 전환하는 것이 좋습니다. 자세한 내용은 [자동 스케일링 및 영역 중복 Application Gateway v2](application-gateway-autoscaling-zone-redundant.md)를 참조하세요.

### <a name="does-application-gateway-v2-support-proxying-requests-with-ntlm-authentication"></a>Application Gateway V2는 NTLM 인증을 사용한 프록시 요청을 지원하나요?

아니요. Application Gateway V2는 아직 NTLM 인증을 사용한 프록시 요청을 지원하지 않습니다.

### <a name="does-application-gateway-affinity-cookie-support-samesite-attribute"></a>Application Gateway 선호도 쿠키는 SameSite 특성을 지원하나요?
예, [Chromium 브라우저](https://www.chromium.org/Home) [v80 업데이트](https://chromiumdash.appspot.com/schedule)에서는 SameSite 특성이 없는 HTTP 쿠키를 SameSite=Lax로 처리해 달라는 요구를 수락했습니다. 즉, 타사 컨텍스트에서는 브라우저가 Application Gateway 선호도 쿠키를 보내지 않습니다. 

이 시나리오를 지원하기 위해, Application Gateway는 기존 *ApplicationGatewayAffinity* 쿠키 외에도 *ApplicationGatewayAffinityCORS*라는 또 다른 쿠키를 삽입합니다.  이러한 쿠키는 유사하지만, *ApplicationGatewayAffinityCORS* 쿠키에 다음과 같은 두 가지 특성이 더 추가되었습니다. *SameSite=None; Secure*. 이러한 특성은 원본 간 요청에서도 고정 세션을 유지합니다. 자세한 내용은 [쿠키 기반 선호도 섹션](configuration-overview.md#cookie-based-affinity)을 참조하세요.

## <a name="performance"></a>성능

### <a name="how-does-application-gateway-support-high-availability-and-scalability"></a>Application Gateway는 고가용성과 확장성을 어떤 방식으로 지원하나요?

인스턴스를 2개 이상 배포한 경우 Application Gateway v1 SKU는 고가용성 시나리오를 지원합니다. 모든 인스턴스가 동시에 실패하는 일이 없도록, Azure는 이러한 인스턴스를 업데이트 및 장애 도메인에 배포합니다. v1 SKU는 로드를 공유하기 위해 동일한 게이트웨이의 여러 인스턴스를 추가하여 확장성을 지원합니다.

v2 SKU의 경우에는 여러 장애 도메인과 업데이트 도메인에 새 인스턴스가 자동으로 분산됩니다. 영역 중복을 선택하면 영역 장애 시의 복원력을 제공하기 위해 최신 인스턴스도 여러 가용성 영역에 분산됩니다.

### <a name="how-do-i-achieve-a-dr-scenario-across-datacenters-by-using-application-gateway"></a>Application Gateway를 사용하여 데이터 센터 간 DR 시나리오를 구현하려면 어떻게 할까요?

Traffic Manager를 사용하여 서로 다른 데이터 센터에 있는 여러 Application Gateway에 트래픽을 분산할 수 있습니다.

### <a name="does-application-gateway-support-autoscaling"></a>Application Gateway는 자동 스케일링을 지원하나요?

예. Application Gateway v2 SKU는 자동 크기 조정을 지원합니다. 자세한 내용은 [자동 스케일링 및 영역 중복 Application Gateway](application-gateway-autoscaling-zone-redundant.md)를 참조하세요.

### <a name="does-manual-or-automatic-scale-up-or-scale-down-cause-downtime"></a>수동 또는 자동 스케일 업/스케일 다운 시 가동 중지 시간이 발생하나요?

아니요. 인스턴스가 업그레이드 도메인 및 장애 도메인 간에 배포됩니다.

### <a name="does-application-gateway-support-connection-draining"></a>Application Gateway는 연결 드레이닝을 지원하나요?

예. 중지 없이 백 엔드 풀 내에서 멤버를 변경하도록 연결 드레이닝을 설정할 수 있습니다. 자세한 내용은 [Application Gateway 연결 드레이닝 섹션](features.md#connection-draining)을 참조하세요.

### <a name="can-i-change-instance-size-from-medium-to-large-without-disruption"></a>중단 없이 인스턴스 크기를 중간에서 큼으로 변경할 수 있나요?

예.

## <a name="configuration"></a>구성

### <a name="is-application-gateway-always-deployed-in-a-virtual-network"></a>Application Gateway가 가상 네트워크에서 항상 배포되나요?

예. Application Gateway는 항상 가상 네트워크 서브넷에 배포됩니다. 이 서브넷은 애플리케이션 게이트웨이만 포함할 수 있습니다. 자세한 내용은 [가상 네트워크 및 서브넷 요구 사항](https://docs.microsoft.com/azure/application-gateway/configuration-overview#azure-virtual-network-and-dedicated-subnet)을 참조하세요.

### <a name="can-application-gateway-communicate-with-instances-outside-of-its-virtual-network-or-outside-of-its-subscription"></a>Application Gateway는 가상 네트워크 외부 또는 구독 외부의 인스턴스와 통신할 수 있나요?

IP 연결이 설정되어 있는 한, Application Gateway는 현재 속한 가상 네트워크의 외부에 있는 인스턴스와 통신할 수 있습니다. 또한 Application Gateway는 현재 속한 구독 외부의 인스턴스와 통신할 수 있습니다. 백 엔드 풀 멤버로 내부 IP를 사용할 계획이면 [가상 네트워크 피어링](../virtual-network/virtual-network-peering-overview.md) 또는 [Azure VPN Gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md)가 필요합니다.

### <a name="can-i-deploy-anything-else-in-the-application-gateway-subnet"></a>Application Gateway 서브넷에서 다른 항목을 배포할 수 있나요?

아니요. 그러나 서브넷에 다른 애플리케이션 게이트웨이를 배포할 수 있습니다.

### <a name="are-network-security-groups-supported-on-the-application-gateway-subnet"></a>애플리케이션 게이트웨이 서브넷에서 네트워크 보안 그룹이 지원되나요?

[Application Gateway 서브넷의 네트워크 보안 그룹](https://docs.microsoft.com/azure/application-gateway/configuration-overview#network-security-groups-on-the-application-gateway-subnet)을 참조하세요.

### <a name="does-the-application-gateway-subnet-support-user-defined-routes"></a>애플리케이션 게이트웨이 서브넷에서 사용자 정의 경로를 지원하나요?

[Application Gateway 서브넷에서 지원되는 사용자 정의 경로](https://docs.microsoft.com/azure/application-gateway/configuration-overview#user-defined-routes-supported-on-the-application-gateway-subnet)를 참조하세요.

### <a name="what-are-the-limits-on-application-gateway-can-i-increase-these-limits"></a>Application Gateway에서 한도는 어떻게 되나요? 이러한 한도를 늘릴 수 있나요?

[Application Gateway 제한](../azure-resource-manager/management/azure-subscription-service-limits.md#application-gateway-limits)을 참조하세요.

### <a name="can-i-simultaneously-use-application-gateway-for-both-external-and-internal-traffic"></a>외부 트래픽과 내부 트래픽 둘 다에 Application Gateway를 동시에 사용할 수 있나요?

예. Application Gateway는 애플리케이션 게이트웨이마다 내부 IP 하나와 외부 IP 하나를 지원합니다.

### <a name="does-application-gateway-support-virtual-network-peering"></a>Application Gateway는 가상 네트워크 피어링을 지원하나요?

예. 가상 네트워크 피어링은 다른 가상 네트워크의 트래픽 부하를 분산하는 데 도움이 됩니다.

### <a name="can-i-talk-to-on-premises-servers-when-theyre-connected-by-expressroute-or-vpn-tunnels"></a>온-프레미스 서버가 ExpressRoute 또는 VPN 터널과 연결되어 있으면 이 서버와 통신할 수 있나요?

예, 트래픽이 허용되기만 한다면 가능합니다.

### <a name="can-one-backend-pool-serve-many-applications-on-different-ports"></a>백 엔드 풀 하나가 여러 포트에서 여러 애플리케이션에 서비스를 제공할 수 있나요?

마이크로서비스 아키텍처가 지원됩니다. 여러 포트에서 탐색하려면 여러 HTTP 설정을 구성해야 합니다.

### <a name="do-custom-probes-support-wildcards-or-regex-on-response-data"></a>사용자 지정 프로브는 응답 데이터의 와일드 카드 또는 regex를 지원하나요?

아니요. 

### <a name="how-are-routing-rules-processed-in-application-gateway"></a>Application Gateway에서 회람 규칙은 어떻게 처리되나요?

[처리 규칙 순서](https://docs.microsoft.com/azure/application-gateway/configuration-overview#order-of-processing-rules)를 참조하세요.

### <a name="for-custom-probes-what-does-the-host-field-signify"></a>사용자 지정 프로브의 호스트 필드는 무엇을 나타내나요?

호스트 필드는 Application Gateway에서 다중 사이트를 구성한 경우 프로브를 보낼 이름을 지정합니다. 지정하지 않으면 '127.0.0.1'이 사용됩니다. 이 값은 가상 머신 호스트 이름과 다릅니다. 형식은 \<프로토콜\>://\<호스트\>:\<포트\>\<경로\>입니다.

### <a name="can-i-allow-application-gateway-access-to-only-a-few-source-ip-addresses"></a>Application Gateway가 일부 원본 IP 주소에만 액세스하도록 허용할 수 있나요?

예. [특정 원본 IP로 액세스 제한](https://docs.microsoft.com/azure/application-gateway/configuration-overview#allow-application-gateway-access-to-a-few-source-ips)을 참조하세요.

### <a name="can-i-use-the-same-port-for-both-public-facing-and-private-facing-listeners"></a>퍼블릭 연결 수신기와 프라이빗 연결 수신기에 똑같은 포트를 사용할 수 있나요?

아니요.

### <a name="does-application-gateway-support-ipv6"></a>Application Gateway는 IPv6를 지원하나요?

Application Gateway v2는 현재 IPv6를 지원하지 않습니다. IPv4만 사용하는 이중 스택 VNet에서 작동할 수 있지만, 게이트웨이 서브넷이 IPv4 전용이어야 합니다. Application Gateway v1은 이중 스택 VNet을 지원하지 않습니다. 

### <a name="how-do-i-use-application-gateway-v2-with-only-private-frontend-ip-address"></a>Application Gateway V2에서 개인 프런트 엔드 IP 주소만 사용하려면 어떻게 할까요?

현재 Application Gateway V2는 사설 IP 전용 모드를 지원하지 않습니다. 지원되는 조합은 다음과 같습니다.
* 사설 IP 및 공용 IP
* 공용 IP 전용

하지만 아래 프로세스를 수행하면 Application Gateway V2에서 사설 IP만 사용할 수 있습니다.
1. 공용 및 개인 프런트 엔드 IP 주소를 모두 사용하는 Application Gateway를 만듭니다.
2. 공용 프런트 엔드 IP 주소용 수신기를 만들지 않습니다. 공용 IP 주소용 수신기를 만들지 않으면 Application Gateway는 공용 IP 주소의 트래픽을 수신 대기하지 않습니다.
3. 다음 구성을 우선 순위 순서대로 사용하여 Application Gateway 서브넷에 대한 [네트워크 보안 그룹](https://docs.microsoft.com/azure/virtual-network/security-overview)을 만들고 연결합니다.
    
    a. 원본 **GatewayManager** 서비스 태그, 대상 **모두**, 대상 포트 **65200-65535**의 트래픽을 허용합니다. 이 포트 범위는 Azure 인프라 통신에 필요합니다. 이러한 포트는 인증서 인증을 통해 보호(잠금)됩니다. 게이트웨이 사용자 관리자를 비롯한 외부 엔터티는 적절한 인증서 없이는 엔드포인트에 대한 변경 작업을 시작할 수 없습니다.
    
    b. 원본 **AzureLoadBalancer** 서비스 태그, 대상 및 대상 포트 **모두**의 트래픽을 허용합니다.
    
    다. 원본 **인터넷** 서비스 태그, 대상 및 대상 포트는 **모두**의 모든 인바운드 트래픽을 거부합니다. 인바운드 규칙에서 이 규칙에 *가장 낮은 우선 순위*를 지정합니다.
    
    d. 개인 IP 주소에 대한 액세스가 차단되지 않도록 VirtualNetwork 인바운드 허용과 같은 기본 규칙을 유지합니다.
    
    e. 아웃바운드 인터넷 연결은 차단할 수 없습니다. 차단할 경우 로깅, 메트릭 등에서 문제가 발생합니다.

사설 IP 전용 액세스를 위한 NSG 구성 샘플: ![사설 IP 전용 액세스를 위한 Application Gateway V2 NSG 구성](./media/application-gateway-faq/appgw-privip-nsg.png)

## <a name="configuration---tls"></a>구성 - TLS

### <a name="what-certificates-does-application-gateway-support"></a>Application Gateway는 어떤 인증서를 지원하나요?

Application Gateway는 자체 서명된 인증서, CA(인증 기관) 인증서, EV(확장 유효성 검사) 인증서 및 와일드 카드 인증서를 지원합니다.

### <a name="what-cipher-suites-does-application-gateway-support"></a>Application Gateway는 어떤 암호 그룹을 지원하나요?

Application Gateway는 다음과 같은 암호 그룹을 지원합니다. 

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

TLS 옵션을 사용자 지정하는 방법은 [Application Gateway에서 TLS 정책 버전 및 암호 그룹 구성](application-gateway-configure-ssl-policy-powershell.md)을 참조하세요.

### <a name="does-application-gateway-support-reencryption-of-traffic-to-the-backend"></a>Application Gateway는 백 엔드에 대해 트래픽의 재암호화를 지원하나요?

예. Application Gateway는 TLS 오프로드와 백 엔드에 대한 트래픽을 다시 암호화하는 엔드투엔드 TLS를 지원합니다.

### <a name="can-i-configure-tls-policy-to-control-tls-protocol-versions"></a>TLS 프로토콜 버전을 제어하는 TLS 정책을 구성할 수 있나요?

예. TLS1.0, TLS1.1 및 TLS1.2를 거부하도록 Application Gateway를 구성할 수 있습니다. SSL 2.0 및 3.0은 기본적으로 비활성화되며 구성할 수 없습니다.

### <a name="can-i-configure-cipher-suites-and-policy-order"></a>암호 그룹 및 정책 순서를 구성할 수 있나요?

예. Application Gateway에서는 [암호 그룹을 구성](application-gateway-ssl-policy-overview.md)할 수 있습니다. 사용자 지정 정책을 정의하려면 다음 암호 그룹 중 하나 이상을 사용하도록 설정해야 합니다. 

* TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256 
* TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256
* TLS_DHE_RSA_WITH_AES_128_GCM_SHA256
* TLS_RSA_WITH_AES_128_GCM_SHA256
* TLS_RSA_WITH_AES_256_CBC_SHA256
* TLS_RSA_WITH_AES_128_CBC_SHA256

Application Gateway는 백 엔드 관리에 SHA256을 사용합니다.

### <a name="how-many-tlsssl-certificates-does-application-gateway-support"></a>Application Gateway는 TLS/SSL 인증서를 몇 개까지 지원하나요?

Application Gateway는 최대 100의 TLS/SSL 인증서를 지원합니다.

### <a name="how-many-authentication-certificates-for-backend-reencryption-does-application-gateway-support"></a>Application Gateway는 백 엔드 재암호화에 인증 인증서를 몇 개까지 지원하나요?

Application Gateway는 최대 100의 인증 인증서를 지원합니다.

### <a name="does-application-gateway-natively-integrate-with-azure-key-vault"></a>Application Gateway는 기본적으로 Azure Key Vault와 통합되나요?

예, Application Gateway v2 SKU는 Key Vault를 지원합니다. 자세한 내용은 [Key Vault 인증서를 사용한 TLS 종료](key-vault-certs.md)를 참조하세요.

### <a name="how-do-i-configure-https-listeners-for-com-and-net-sites"></a>.com 및 .net 사이트에 대해 HTTPS 수신기를 구성하려면 어떻게 할까요?? 

다중 도메인 기반(호스트 기반) 라우팅의 경우 다중 사이트 수신기를 만들고, HTTPS를 프로토콜로 사용하는 수신기를 설정하고, 수신기를 회람 규칙과 연결할 수 있습니다. 자세한 내용은 [Application Gateway를 사용하여 여러 사이트 호스팅](https://docs.microsoft.com/azure/application-gateway/multiple-site-overview)을 참조하세요.

### <a name="can-i-use-special-characters-in-my-pfx-file-password"></a>.pfx 파일 암호에 특수 문자를 사용해도 되나요?

아니요, .pfx 파일 암호에는 영숫자 문자만 사용할 수 있습니다.

## <a name="configuration---ingress-controller-for-aks"></a>구성 - AKS용 수신 컨트롤러

### <a name="what-is-an-ingress-controller"></a>수신 컨트롤러란 무엇인가요?

Kubernetes를 사용하면 `deployment` 및 `service` 리소스를 만들어서 클러스터 내부에서 Pod 그룹을 공개할 수 있습니다. 외부에서 동일한 서비스를 공개하기 위해 부하 분산, TLS 종료 및 이름 기반 가상 호스팅을 제공하는 [`Ingress`](https://kubernetes.io/docs/concepts/services-networking/ingress/) 리소스가 정의됩니다.
이 `Ingress` 리소스를 충족하려면 `Ingress` 리소스의 변경 내용을 수신 대기하고 부하 분산 장치 정책을 구성하는 수신 컨트롤러가 필요합니다.

Application Gateway Ingress Controller는 [Azure Application Gateway](https://azure.microsoft.com/services/application-gateway/)를 AKS 클러스터라고도 하는 [Azure Kubernetes Service](https://azure.microsoft.com/services/kubernetes-service/)의 수신으로 사용할 수 있습니다.

### <a name="can-a-single-ingress-controller-instance-manage-multiple-application-gateways"></a>단일 수신 컨트롤러 인스턴스가 여러 Application Gateway를 관리할 수 있나요?

현재는 수신 컨트롤러 인스턴스 하나를 Application Gateway 하나에만 연결할 수 있습니다.

## <a name="diagnostics-and-logging"></a>진단 및 로깅

### <a name="what-types-of-logs-does-application-gateway-provide"></a>Application Gateway는 어떤 유형의 로그를 제공하나요?

Application Gateway는 다음 세 가지 로그를 제공합니다. 

* **ApplicationGatewayAccessLog**: 이 액세스 로그에는 애플리케이션 게이트웨이 프런트 엔드에 제출된 각 요청이 포함되어 있습니다. 이 데이터에는 호출자의 IP, 요청된 URL, 응답 대기 시간, 반환 코드, 바이트 입출력이 포함되어 있습니다. 애플리케이션 게이트웨이마다 하나의 레코드가 포함됩니다.
* **ApplicationGatewayPerformanceLog**: 성능 로그는 각 애플리케이션 게이트웨이의 성능 정보를 캡처합니다. 이 정보에는 처리량(바이트), 처리된 총 요청 수, 실패한 요청 수, 정상 및 비정상 백 엔드 인스턴스 수가 포함됩니다.
* **ApplicationGatewayFirewallLog**: WAF를 사용하여 구성하는 애플리케이션 게이트웨이의 경우 방화벽 로그에는 검색 모드 또는 방지 모드를 통해 기록된 요청이 포함됩니다.

모든 로그는 60초마다 수집됩니다. 자세한 내용은 [Application Gateway에 대한 백 엔드 상태, 진단 로그 및 메트릭](application-gateway-diagnostics.md)을 참조하세요.

### <a name="how-do-i-know-if-my-backend-pool-members-are-healthy"></a>내 백 엔드 풀 멤버가 정상인지 어떻게 알 수 있나요?

PowerShell cmdlet `Get-AzApplicationGatewayBackendHealth` 또는 포털을 사용하여 상태를 확인할 수 있습니다. 자세한 내용은 [Application Gateway 진단](application-gateway-diagnostics.md)을 참조하세요.

### <a name="whats-the-retention-policy-for-the-diagnostic-logs"></a>진단 로그의 보존 정책은 무엇인가요?

진단 로그는 고객의 스토리지 계정으로 전송됩니다. 고객은 원하는 대로 보존 정책을 설정할 수 있습니다. 진단 로그를 이벤트 허브 또는 Azure Monitor 로그에도 전송할 수 있습니다. 자세한 내용은 [Application Gateway 진단](application-gateway-diagnostics.md)을 참조하세요.

### <a name="how-do-i-get-audit-logs-for-application-gateway"></a>Application Gateway에 대한 감사 로그를 어떻게 얻나요?

포털의 애플리케이션 게이트웨이 메뉴 블레이드에서 **활동 로그**를 선택하여 감사 로그에 액세스하면 됩니다. 

### <a name="can-i-set-alerts-with-application-gateway"></a>Application Gateway로 경고를 설정할 수 있나요?

예. Application Gateway에서 경고는 메트릭에 대해 구성됩니다. 자세한 내용은 [Application Gateway 메트릭](https://docs.microsoft.com/azure/application-gateway/application-gateway-metrics) 및 [경고 알림 받기](../monitoring-and-diagnostics/insights-receive-alert-notifications.md)를 참조하세요.

### <a name="how-do-i-analyze-traffic-statistics-for-application-gateway"></a>Application Gateway에 대한 트래픽 통계를 분석하려면 어떻게 해야 하나요?

여러 가지 방법으로 액세스 로그를 살펴보고 분석할 수 있습니다. Azure Monitor 로그, Excel, Power BI 등을 사용할 수 있습니다.

Application Gateway 액세스 로그에 널리 사용되는 [GoAccess](https://goaccess.io/) 로그 분석기를 설치하고 실행하는 Resource Manager 템플릿을 사용할 수도 있습니다. GoAccess는 고유 방문자, 요청한 파일, 호스트, 운영 체제, 브라우저, HTTP 상태 코드 등의 유용한 HTTP 트래픽 통계를 제공합니다. 자세한 내용은 GitHub에서 [Resource Manager 템플릿 폴더의 Readme 파일](https://aka.ms/appgwgoaccessreadme)을 참조하세요.

### <a name="what-could-cause-backend-health-to-return-an-unknown-status"></a>백 엔드 상태가 알 수 없는 상태로 돌아가는 이유는 무엇인가요?

일반적으로 애플리케이션 게이트웨이 서브넷에서 NSG(네트워크 보안 그룹), 사용자 지정 DNS 또는 UDR(사용자 정의 라우팅)에 의해 백 엔드 액세스가 차단되는 경우에 알 수 없음 상태로 표시됩니다. 자세한 내용은 [Application Gateway에 대한 백 엔드 상태, 진단 로깅 및 메트릭](application-gateway-diagnostics.md)을 참조하세요.

### <a name="is-there-any-case-where-nsg-flow-logs-wont-show-allowed-traffic"></a>NSG 흐름 로그에 허용된 트래픽이 표시되지 않는 경우가 있나요?

예. 구성이 다음 시나리오와 일치하는 경우 NSG 흐름 로그에 허용된 트래픽이 표시되지 않습니다.
- Application Gateway v2를 배포했습니다.
- 애플리케이션 게이트웨이 서브넷에 NSG가 있습니다.
- 이 NSG에서 NSG 흐름 로그를 사용하도록 설정했습니다.

### <a name="how-do-i-use-application-gateway-v2-with-only-private-frontend-ip-address"></a>Application Gateway V2에서 개인 프런트 엔드 IP 주소만 사용하려면 어떻게 할까요?

현재 Application Gateway V2는 사설 IP 전용 모드를 지원하지 않습니다. 지원되는 조합은 다음과 같습니다.
* 사설 IP 및 공용 IP
* 공용 IP 전용

하지만 아래 프로세스를 수행하면 Application Gateway V2에서 사설 IP만 사용할 수 있습니다.
1. 공용 및 개인 프런트 엔드 IP 주소를 모두 사용하는 Application Gateway를 만듭니다.
2. 공용 프런트 엔드 IP 주소용 수신기를 만들지 않습니다. 공용 IP 주소용 수신기를 만들지 않으면 Application Gateway는 공용 IP 주소의 트래픽을 수신 대기하지 않습니다.
3. 다음 구성을 우선 순위 순서대로 사용하여 Application Gateway 서브넷에 대한 [네트워크 보안 그룹](https://docs.microsoft.com/azure/virtual-network/security-overview)을 만들고 연결합니다.
    
    a. 원본 **GatewayManager** 서비스 태그, 대상 **모두**, 대상 포트 **65200-65535**의 트래픽을 허용합니다. 이 포트 범위는 Azure 인프라 통신에 필요합니다. 이러한 포트는 인증서 인증을 통해 보호(잠금)됩니다. 게이트웨이 사용자 관리자를 비롯한 외부 엔터티는 적절한 인증서 없이는 엔드포인트에 대한 변경 작업을 시작할 수 없습니다.
    
    b. 원본 **AzureLoadBalancer** 서비스 태그, 대상 포트 **모두**의 트래픽을 허용합니다.
    
    다. 원본 **인터넷** 서비스 태그, 대상 포트 **모두**의 모든 인바운드 트래픽을 거부합니다. 인바운드 규칙에서 이 규칙에 *가장 낮은 우선 순위*를 지정합니다.
    
    d. 개인 IP 주소에 대한 액세스가 차단되지 않도록 VirtualNetwork 인바운드 허용과 같은 기본 규칙을 유지합니다.
    
    e. 아웃바운드 인터넷 연결은 차단할 수 없습니다. 차단할 경우 로깅, 메트릭 등에서 문제가 발생합니다.

사설 IP 전용 액세스를 위한 NSG 구성 샘플: ![사설 IP 전용 액세스를 위한 Application Gateway V2 NSG 구성](./media/application-gateway-faq/appgw-privip-nsg.png)

### <a name="does-application-gateway-affinity-cookie-support-samesite-attribute"></a>Application Gateway 선호도 쿠키는 SameSite 특성을 지원하나요?
예, [Chromium 브라우저](https://www.chromium.org/Home) [v80 업데이트](https://chromiumdash.appspot.com/schedule)에서는 SameSite 특성이 없는 HTTP 쿠키를 SameSite=Lax로 처리해 달라는 요구를 수락했습니다. 즉, 타사 컨텍스트에서는 브라우저가 Application Gateway 선호도 쿠키를 보내지 않습니다. 이 시나리오를 지원하기 위해, Application Gateway는 기존 *ApplicationGatewayAffinity* 쿠키 외에도 *ApplicationGatewayAffinityCORS*라는 또 다른 쿠키를 삽입합니다.  이러한 쿠키는 유사하지만, *ApplicationGatewayAffinityCORS* 쿠키에 다음과 같은 두 가지 특성이 더 추가되었습니다. *SameSite=None; Secure*. 이러한 특성은 원본 간 요청에서도 고정 세션을 유지합니다. 자세한 내용은 [쿠키 기반 선호도 섹션](configuration-overview.md#cookie-based-affinity)을 참조하세요.

## <a name="next-steps"></a>다음 단계

Application Gateway에 대한 자세한 내용은 [Azure Application Gateway란?](overview.md)을 참조하세요.
