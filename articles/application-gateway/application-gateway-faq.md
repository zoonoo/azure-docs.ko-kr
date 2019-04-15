---
title: Azure Application Gateway에 대한 질문과 대답
description: 이 페이지에서는 Azure Application Gateway에 대한 질문과 대답을 제공합니다.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.workload: infrastructure-services
ms.date: 3/20/2019
ms.author: victorh
ms.openlocfilehash: c40f372d3574f940e475a6626f998adae37a6d61
ms.sourcegitcommit: 04716e13cc2ab69da57d61819da6cd5508f8c422
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/02/2019
ms.locfileid: "58851160"
---
# <a name="frequently-asked-questions-for-application-gateway"></a>Application Gateway에 대한 질문과 대답

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="general"></a>일반

### <a name="what-is-application-gateway"></a>Application Gateway란?

Azure Application Gateway는 서비스 형태의 ADC(애플리케이션 전달 컨트롤러)이며 애플리케이션에 대한 다양한 계층 7 부하 분산 기능을 제공합니다. Azure에서 완전히 관리되는 고가용성의 확장성 있는 서비스를 제공합니다.

### <a name="what-features-does-application-gateway-support"></a>Application Gateway에서 지원하는 기능은 어떤 것이 있나요?

Application Gateway에서는 자동 크기 조정, SSL 오프로딩 및 종단 간 SSL, 웹 애플리케이션 방화벽, 쿠키 기반 세션 선호도, URL 경로 기반 라우팅, 다중 사이트 호스팅 등을 지원합니다. 지원되는 기능의 전체 목록은 [Application Gateway 소개](application-gateway-introduction.md)를 참조하세요.

### <a name="what-is-the-difference-between-application-gateway-and-azure-load-balancer"></a>Application Gateway와 Azure Load Balancer 간의 차이는 무엇인가요?

Application Gateway는 웹 트래픽을만 사용 (HTTP/HTTPS/WebSocket/HTTP/2)를 사용 하 여 작동 하는 계층 7 부하 분산. SSL 종료, 쿠키 기반 세션 선호도, 트래픽 부하 분산을 위한 라운드 로빈과 같은 기능을 지원합니다. 부하 분산 장치는 계층 4(TCP/UDP)에서 트래픽 부하를 분산합니다.

### <a name="what-protocols-does-application-gateway-support"></a>Application Gateway에서 지원하는 프로토콜은 무엇인가요?

Application Gateway는 HTTP, HTTPS, HTTP/2 및 WebSocket을 지원합니다.

### <a name="how-does-application-gateway-support-http2"></a>Application Gateway는 어떻게 HTTP/2를 지원하나요?

참조 [http/2 지원은](https://docs.microsoft.com/azure/application-gateway/configuration-overview#http2-support) 에 Application gateway는 HTTP/2 프로토콜을 지원 하는 방법에 대해 알아봅니다.

### <a name="what-resources-are-supported-today-as-part-of-backend-pool"></a>현재 백 엔드 풀의 일부로 어떤 리소스가 지원되나요?

참조 [지원 되는 백 엔드 리소스](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#backend-pool) 알아보려면 리소스는 Application gateway에서 지원 됩니다.

### <a name="what-regions-is-the-service-available-in"></a>어떤 지역에서 서비스를 사용할 수 있습니까?

Application Gateway는 Azure 전체의 모든 지역에서 사용할 수 있습니다. [Azure China 21Vianet](https://www.azure.cn/) 및 [Azure Government](https://azure.microsoft.com/overview/clouds/government/)에서도 사용할 수 있습니다.

### <a name="is-this-a-dedicated-deployment-for-my-subscription-or-is-it-shared-across-customers"></a>내 구독에 대한 전용 배포인가요? 아니면 고객 간에 공유되나요?

Application Gateway는 가상 네트워크에서 전용 배포입니다.

### <a name="is-http-https-redirection-supported"></a>HTTP->HTTPS 리디렉션이 지원되나요?

리디렉션은 지원됩니다. 자세한 내용은 [Application Gateway 리디렉션 개요](application-gateway-redirect-overview.md)를 참조하세요.

### <a name="in-what-order-are-listeners-processed"></a>수신기는 어떤 순서로 처리되나요?

참조 [수신기 처리 순서](https://docs.microsoft.com/azure/application-gateway/configuration-overview#order-of-processing-listeners)합니다.

### <a name="where-do-i-find-application-gateways-ip-and-dns"></a>Application Gateway의 IP 및 DNS는 어디에서 확인하나요?

공용 IP 주소를 엔드포인트로 사용하는 경우 공용 IP 주소 리소스 또는 포털의 Application Gateway 개요 페이지에서 이 정보를 확인할 수 있습니다. 내부 IP 주소는 개요 페이지에서 확인할 수 있습니다.

### <a name="what-is-keep-alive-timeout-and-tcp-idle-timeout-setting-on-application-gateway"></a>연결 유지 시간 제한 및 Application Gateway의 TCP 유휴 시간 제한 설정 이란?

V1 SKU에서 연결 유지 시간 제한은 v2 SKU에서 연결 유지 시간 제한 120 초로 75 초 TCP 유휴 시간 제한 Application Gateway의 VIP 프런트 엔드에서 4 분 기본값입니다.

### <a name="does-the-ip-or-dns-name-change-over-the-lifetime-of-the-application-gateway"></a>Application Gateway의 수명 중에 IP 또는 DNS 이름이 변경되나요?

Application Gateway를 중지한 후에 시작하면 VIP가 변경될 수 있습니다. Application Gateway와 연결된 DNS 이름은 게이트웨이 수명 중에 변경되지 않습니다. 그러므로 CNAME 별칭을 사용하고 해당 별칭이 Application Gateway의 DNS 주소를 가리키도록 설정하는 것이 좋습니다.

### <a name="does-application-gateway-support-static-ip"></a>Application Gateway에서 고정 IP를 지원하나요?

예. Application Gateway v2 SKU는 고정 공용 IP 주소를 지원합니다. v1 SKU는 고정 내부 IP를 지원합니다.

### <a name="does-application-gateway-support-multiple-public-ips-on-the-gateway"></a>Application Gateway가 게이트웨이에서 여러 공용 IP를 지원하나요?

Application Gateway에서는 공용 IP 주소가 하나만 지원됩니다.

### <a name="how-large-should-i-make-my-subnet-for-application-gateway"></a>Application Gateway에 대해 얼마나 큰 서브넷을 만들어야 하나요?

참조 [Application Gateway 서브넷 크기 고려 사항](https://docs.microsoft.com/azure/application-gateway/configuration-overview#size-of-the-subnet) 배포에 필요한 서브넷 크기를 알아야 합니다.

### <a name="q-can-i-deploy-more-than-one-application-gateway-resource-to-a-single-subnet"></a>Q. 둘 이상의 Application Gateway 리소스를 단일 서브넷에 배포할 수 있나요?

예. 지정된 Application Gateway 배포의 여러 인스턴스를 보유하는 것 외에도, 다른 애플리케이션 게이트웨이 리소스를 포함하는 기존 서브넷에 다른 고유한 Application Gateway 리소스를 프로비전할 수 있습니다.

동일한 서브넷에서 Standard_v2와 표준 Application Gateway의 혼합은 지원되지 않습니다.

### <a name="does-application-gateway-support-x-forwarded-for-headers"></a>Application Gateway에서 x-forwarded-for 헤더를 지원하나요?

예. 참조 [요청 수정](https://docs.microsoft.com/azure/application-gateway/how-application-gateway-works#modifications-to-the-request) Application Gateway에서 지원 되는 x-전달 기능에 대 한 헤더에 대해 자세히 알아보려면 합니다.

### <a name="how-long-does-it-take-to-deploy-an-application-gateway-does-my-application-gateway-still-work-when-being-updated"></a>Application Gateway를 배포하는 데 얼마의 시간이 걸리나요? Application Gateway가 업데이트되어도 여전히 작동하나요?

새 Application Gateway v1 SKU 배포 시 프로비전하는 데 최대 20분이 걸릴 수 있습니다. 인스턴스 크기/수가 변경되어도 중단되지 않으며, 게이트웨이는 이 시간 동안 활성 상태를 유지합니다.

V1 SKU 배포의 경우 프로비전하는 데 5~6분이 걸릴 수 있습니다.

### <a name="can-exchange-server-be-used-as-backend-with-application-gateway"></a>Exchange server 응용 프로그램 게이트웨이와 백 엔드도 사용할 수 있습니다.

아니요, Application Gateway는 SMTP, IMAP 및 POP3 등의 전자 메일 프로토콜을 지원 하지 않습니다. 

## <a name="performance"></a>성능

### <a name="how-does-application-gateway-support-high-availability-and-scalability"></a>Application Gateway는 고가용성과 확장성을 어떤 방식으로 지원하나요?

둘 이상의 인스턴스가 배포된 경우 Application Gateway v1 SKU는 고가용성 시나리오를 지원합니다. Azure에서는 이러한 인스턴스를 업데이트 및 장애 도메인 간에 배포하여 모든 인스턴스가 동시에 실패하는 일이 없도록 합니다. v1 SKU는 로드를 공유하기 위해 동일한 게이트웨이의 여러 인스턴스를 추가하여 확장성을 지원합니다.

v2 SKU의 경우에는 여러 장애 도메인과 업데이트 도메인에 새 인스턴스가 자동으로 분산됩니다. 중복 수준을 0으로 선택하면 영역 장애 시의 복원력을 제공하기 위해 최신 인스턴스도 여러 가용성 영역으로 분산됩니다.

### <a name="how-do-i-achieve-dr-scenario-across-data-centers-with-application-gateway"></a>Application Gateway에서 데이터 센터 간 DR 시나리오를 어떻게 달성할 수 있나요?

고객은 Traffic Manager를 사용하여 서로 다른 데이터 센터에 있는 여러 Application Gateway 간에 트래픽을 분산시킬 수 있습니다.

### <a name="is-autoscaling-supported"></a>자동 크기 조정이 지원되나요?

예. Application Gateway v2 SKU는 자동 크기 조정을 지원합니다. 자세한 내용은 [자동 크기 조정 및 영역 중복 Application Gateway(공개 미리 보기)](application-gateway-autoscaling-zone-redundant.md)를 참조하세요.

### <a name="does-manual-scale-updown-cause-downtime"></a>수동 강화/규모 축소 시 가동 중지 시간이 발생하나요?

가동 중지 시간은 발생하지 않습니다. 인스턴스가 업그레이드 도메인 및 장애 도메인 간에 배포됩니다.

### <a name="does-application-gateway-support-connection-draining"></a>Application Gateway는 연결 드레이닝을 지원하나요?

예. 중지 없이 백 엔드 풀 내에서 멤버를 변경하도록 연결 드레이닝을 구성할 수 있습니다. 이렇게 하면 해당 연결이 닫히거나 구성 가능한 제한 시간이 만료될 때까지 기존 연결을 이전 목적지로 계속 보낼 수 있습니다. 연결 드레이닝은 현재 처리 중인 연결이 완료될 때까지만 대기합니다. Application Gateway는 애플리케이션 세션 상태를 인식하지 못합니다.

### <a name="can-i-change-instance-size-from-medium-to-large-without-disruption"></a>중단 없이 인스턴스 크기를 중간에서 큼으로 변경할 수 있나요?

예, Azure에서는 인스턴스를 업데이트 및 장애 도메인 간에 배포하여 모든 인스턴스가 동시에 실패하는 일이 없도록 합니다. Application Gateway는 로드를 공유하기 위해 동일한 게이트웨이의 여러 인스턴스를 추가하여 크기 조정을 지원합니다.

## <a name="configuration"></a>구성

### <a name="is-application-gateway-always-deployed-in-a-virtual-network"></a>Application Gateway가 가상 네트워크에서 항상 배포되나요?

예, Application Gateway는 항상 가상 네트워크 서브넷에 배포됩니다. 이 서브넷은 Application Gateway만 포함할 수 있습니다. 참조 [가상 네트워크 및 서브넷 요구 사항](https://docs.microsoft.com/azure/application-gateway/configuration-overview#azure-virtual-network-and-dedicated-subnet) Application Gateway에 대 한 서브넷 고려 사항을 이해 합니다.

### <a name="can-application-gateway-communicate-with-instances-outside-of-the-virtual-network-it-is-in-or-outside-of-the-subscription-it-is-in"></a>Application Gateway에 가상 네트워크 외부에서 또는 구독에 외부 인스턴스와 통신할 수 있나요?

Application Gateway는 IP 연결이으로 있는 가상 네트워크 외부에서 또는 구독에 외부 인스턴스와 통신할 수 있습니다. 백 엔드 풀 멤버로 내부 IP를 사용할 계획인 경우 [VNET 피어링](../virtual-network/virtual-network-peering-overview.md) 또는 [VPN Gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md)가 필요합니다.

### <a name="can-i-deploy-anything-else-in-the-application-gateway-subnet"></a>Application Gateway 서브넷에서 다른 항목을 배포할 수 있나요?

아니요, 그러나 서브넷에 다른 애플리케이션 게이트웨이를 배포할 수 있습니다.

### <a name="are-network-security-groups-supported-on-the-application-gateway-subnet"></a>Application Gateway 서브넷에서 네트워크 보안 그룹이 지원되나요?

참조 [Application Gateway 서브넷에 대 한 네트워크 보안 그룹 제한이](https://docs.microsoft.com/azure/application-gateway/configuration-overview#network-security-groups-on-the-application-gateway-subnet) 지원 응용 프로그램 게이트웨이 서브넷에 네트워크 보안 그룹에 알아봅니다.

### <a name="are-user-defined-routes-supported-on-the-application-gateway-subnet"></a>애플리케이션 게이트웨이 서브넷에서 사용자 정의 경로가 지원되나요?

참조 [사용자 정의 경로 제한](https://docs.microsoft.com/azure/application-gateway/configuration-overview#user-defined-routes-supported-on-the-application-gateway-subnet) 응용 프로그램 게이트웨이 서브넷에 지원 되는 사용자 정의 경로 대해 자세히 알아보려면 합니다.

### <a name="what-are-the-limits-on-application-gateway-can-i-increase-these-limits"></a>Application Gateway에서 한도는 어떻게 되나요? 이러한 한도를 늘릴 수 있나요?

한도를 보려면 [Application Gateway 한도](../azure-subscription-service-limits.md#application-gateway-limits)를 참조하세요.

### <a name="can-i-use-application-gateway-for-both-external-and-internal-traffic-simultaneously"></a>외부 및 내부 트래픽 모두에 Application Gateway를 동시에 사용할 수 있나요?

예. Application Gateway에서는 Application Gateway당 내부 IP 하나와 외부 IP 하나를 지원합니다.

### <a name="is-vnet-peering-supported"></a>VNet 피어링이 지원되나요?

예, VNet 피어링이 지원되며 다른 가상 네트워크에서 트래픽을 부하 분산시키는 데 도움이 됩니다.

### <a name="can-i-talk-to-on-premises-servers-when-they-are-connected-by-expressroute-or-vpn-tunnels"></a>온-프레미스 서버가 ExpressRoute 또는 VPN 터널과 연결되어 있으면 이 서버와 통신할 수 있나요?

예, 트래픽이 허용되기만 한다면 가능합니다.

### <a name="can-i-have-one-backend-pool-serving-many-applications-on-different-ports"></a>한 개의 백 엔드 풀에서 서로 다른 포트로 많은 애플리케이션을 제공할 수 있나요?

마이크로 서비스 아키텍처가 지원됩니다. 서로 다른 포트의 프로브에 대해 여러 http 설정이 구성되어야 합니다.

### <a name="do-custom-probes-support-wildcardsregex-on-response-data"></a>사용자 지정 프로브가 응답 데이터에 와일드 카드/regex를 지원하나요?

사용자 지정 프로브는 응답 데이터에 와일드 카드 또는 regex를 지원하지 않습니다.

### <a name="how-are-rules-processed"></a>규칙은 어떻게 처리되나요?

참조 [처리 규칙의 순서](https://docs.microsoft.com/azure/application-gateway/configuration-overview#order-of-processing-rules) 는 Application Gateway에서 프로세스를 라우팅 규칙 하는 방법을 알아야 합니다.

### <a name="what-does-the-host-field-for-custom-probes-signify"></a>사용자 지정 프로브에 대한 호스트 필드는 무엇을 나타내나요?

호스트 필드는 프로브를 보낼 이름을 지정합니다. 다중 사이트를 Application Gateway에 구성하는 경우에만 적용할 수 있습니다. 그렇지 않으면 '127.0.0.1'을 사용합니다. 이 값은 VM 호스트 이름과 다르며 \<프로토콜\>://\<호스트\>:\<포트\>\<경로\> 형식입니다.

### <a name="can-i-whitelist-application-gateway-access-to-a-few-source-ips"></a>몇 가지 원본 IP에 대한 Application Gateway 액세스를 허용 목록에 추가할 수 있나요?

예. 참조 [특정 원본 Ip에 대 한 액세스를 제한](https://docs.microsoft.com/azure/application-gateway/configuration-overview#whitelist-application-gateway-access-to-a-few-source-ips) 원본 Ip는만 허용 목록에 있는지 확인 하는 방법을 알아보려면 Application Gateway를 액세스할 수 있습니다.

### <a name="can-the-same-port-be-used-for-both-public-and-private-facing-listeners"></a>공용 및 개인 연결 수신기 모두에 대해 동일한 포트를 사용할 수 있나요?

아니요, 지원되지 않습니다.

## <a name="configuration---ssl"></a>구성-SSL

### <a name="what-certificates-are-supported-on-application-gateway"></a>Application Gateway에서 어떤 인증서가 지원되나요?

자체 서명 된 인증서, CA 인증서, EV 인증서 및 와일드 카드 인증서 지원 됩니다.

### <a name="what-are-the-current-cipher-suites-supported-by-application-gateway"></a>Application Gateway에서 지원되는 현재 암호 그룹은 무엇인가요?

Application Gateway에서 현재 지원되는 현재 암호 그룹은 다음과 같습니다. SSL 옵션을 사용자 지정하는 방법에 대해 알아보려면 [Application Gateway에서 SSL 정책 버전 및 암호 그룹 구성](application-gateway-configure-ssl-policy-powershell.md)을 참조하세요.

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

### <a name="does-application-gateway-also-support-re-encryption-of-traffic-to-the-backend"></a>Application Gateway에서 백 엔드에 대해 트래픽의 재암호화를 지원하나요?

예, Application Gateway는 SSL 오프로드와 백 엔드에 대한 트래픽을 다시 암호화하는 종단 간 SSL을 지원합니다.

### <a name="can-i-configure-ssl-policy-to-control-ssl-protocol-versions"></a>SSL 프로토콜 버전을 제어하는 SSL 정책을 구성할 수 있나요?

예, TLS1.0, TLS1.1 및 TLS1.2를 거부하도록 Application Gateway를 구성할 수 있습니다. SSL 2.0 및 3.0은 기본적으로 비활성화되며 구성할 수 없습니다.

### <a name="can-i-configure-cipher-suites-and-policy-order"></a>암호 그룹 및 정책 순서를 구성할 수 있나요?

예, [암호 그룹을 구성](application-gateway-ssl-policy-overview.md)하도록 지원됩니다. 사용자 지정 정책을 정의할 때 다음 암호 그룹 중 하나 이상을 사용해야 합니다. Application Gateway는 백 엔드 관리를 위해 SHA256을 사용합니다.

* TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256 
* TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256
* TLS_DHE_RSA_WITH_AES_128_GCM_SHA256
* TLS_RSA_WITH_AES_128_GCM_SHA256
* TLS_RSA_WITH_AES_256_CBC_SHA256
* TLS_RSA_WITH_AES_128_CBC_SHA256

### <a name="how-many-ssl-certificates-are-supported"></a>몇 개의 SSL 인증서가 지원되나요?

최대 100개의 SSL 인증서가 지원됩니다.

### <a name="how-many-authentication-certificates-for-backend-re-encryption-are-supported"></a>백 엔드 재암호화에 몇 개의 인증 인증서가 지원되나요?

5의 기본값으로 최대 10개의 인증 인증서가 지원됩니다.

### <a name="does-application-gateway-integrate-with-azure-key-vault-natively"></a>Application Gateway가 Azure Key Vault와 고유하게 통합되나요?

아니요, Azure Key Vault와 통합되지 않습니다.

### <a name="how-to-configure-https-listeners-for-com-and-net-sites"></a>.Com 및.net 사이트에 대 한 HTTPS 수신기를 구성 하는 방법 

여러 도메인 기반 (호스트 기반) 라우팅에 대 한 다중 사이트 수신기를 만들려면, 수신기 구성에서 프로토콜로 HTTPS를 선택 및 라우팅 규칙을 사용 하 여 수신기를 연결할 수 있습니다. 자세한 내용은 참조 하세요. [Application Gateway를 사용 하 여 여러 사이트 호스팅](https://docs.microsoft.com/azure/application-gateway/multiple-site-overview)합니다. 

## <a name="configuration---web-application-firewall-waf"></a>구성-웹 응용 프로그램 방화벽 (WAF)

### <a name="does-the-waf-sku-offer-all-the-features-available-with-the-standard-sku"></a>WAF SKU는 표준 SKU에서 사용 가능한 모든 기능을 제공하나요?

예, WAF는 표준 SKU에서 모든 기능을 지원합니다.

### <a name="what-is-the-crs-version-application-gateway-supports"></a>Application Gateway에서 지원하는 CRS 버전은 무엇인가요?

Application Gateway는 CRS [2.2.9](application-gateway-crs-rulegroups-rules.md#owasp229) 및 CRS [3.0](application-gateway-crs-rulegroups-rules.md#owasp30)을 지원합니다.

### <a name="how-do-i-monitor-waf"></a>WAF를 모니터링하려면 어떻게 하나요?

WAF는 진단 로깅을 통해 모니터링되며 진단 로깅에 대한 자세한 내용은 [Application Gateway에 대한 진단 로깅 및 메트릭](application-gateway-diagnostics.md)에서 확인할 수 있습니다.

### <a name="does-detection-mode-block-traffic"></a>검색 모드에서 트래픽을 차단하나요?

아니요, 검색 모드는 WAF 규칙을 트리거한 트래픽만 로깅합니다.

### <a name="can-i-customize-waf-rules"></a>WAF 규칙을 사용자 지정할 수 있나요?

예, WAF 규칙은 사용자 지정할 수 있습니다. 자세한 내용은 [WAF 규칙 그룹 및 규칙 사용자 지정](application-gateway-customize-waf-rules-portal.md)을 참조하세요.

### <a name="what-rules-are-currently-available"></a>현재 사용 가능한 규칙은 무엇인가요?

현재 WAF는 [OWASP 상위 10개 취약점](https://www.owasp.org/index.php/Top10#OWASP_Top_10_for_2013)에서 확인할 수 있는 OWASP(Open Web Application Security Project)에 의해 식별된 상위 10개 취약점 대부분에 대해 보안 기준을 제공하는 CRS [2.2.9](application-gateway-crs-rulegroups-rules.md#owasp229) 및 [3.0](application-gateway-crs-rulegroups-rules.md#owasp30)을 지원합니다.

* SQL 삽입 공격 보호

* 교차 사이트 스크립팅 공격 보호

* 명령 삽입, HTTP 요청 밀반입, HTTP 응답 분할, 원격 파일 포함 공격 등의 일반 웹 공격 보호

* HTTP 프로토콜 위반 보호

* 누락된 호스트 사용자-에이전트 및 수락 헤더 같은 HTTP 프로토콜 이상 보호

* 보트, 크롤러 및 스캐너 방지

* 일반적인 애플리케이션 구성 오류(즉 Apache, IIS 등) 검색

### <a name="does-waf-also-support-ddos-prevention"></a>WAF에서 DDoS 방지도 지원하나요?

예. Application Gateway가 배포된 VNet에서 DDos 보호를 사용하도록 설정할 수 있습니다. 이렇게 하면 Azure DDos Protection 서비스를 사용하여 Application Gateway VIP도 보호됩니다.

## <a name="diagnostics-and-logging"></a>진단 및 로깅

### <a name="what-types-of-logs-are-available-with-application-gateway"></a>Application Gateway에서 어떤 유형의 로그를 사용할 수 있나요?

Application Gateway에서는 3가지 로그를 사용할 수 있습니다. 이러한 로그 및 기타 진단 기능에 대한 자세한 내용은 [Application Gateway에 대한 백 엔드 상태, 진단 로그 및 메트릭](application-gateway-diagnostics.md)을 참조하세요.

* **ApplicationGatewayAccessLog** - 이 액세스 로그에는 Application Gateway 프런트 엔드에 제출된 각 요청이 포함되어 있습니다. 이 데이터에는 호출자의 IP, 요청된 URL, 응답 대기 시간, 반환 코드, 바이트 입출력을 포함합니다. 액세스 로그는 300초마다 수집됩니다. 이 로그에는 Application Gateway 인스턴스당 하나의 레코드가 포함됩니다.
* **ApplicationGatewayPerformanceLog** - 이 성능 로그는 처리된 총 요청 수, 처리량(바이트), 실패한 요청 수, 실패한 요청 수, 정상 및 비정상 백 엔드 인스턴스 수 등을 포함한 인스턴스별 성능 정보를 캡처합니다.
* **ApplicationGatewayFirewallLog** - 이 방화벽 로그에는 웹 애플리케이션 방화벽으로 구성된 애플리케이션 게이트웨이의 검색 모드 또는 방지 모드를 통해 기록된 요청이 포함되어 있습니다.

### <a name="how-do-i-know-if-my-backend-pool-members-are-healthy"></a>내 백 엔드 풀 멤버가 정상인지 어떻게 알 수 있나요?

PowerShell cmdlet `Get-AzApplicationGatewayBackendHealth`를 사용하거나 [Application Gateway 진단](application-gateway-diagnostics.md)을 방문하여 포털을 통해 상태를 확인할 수 있습니다.

### <a name="what-is-the-retention-policy-on-the-diagnostics-logs"></a>진단 로그에서 보존 정책은 무엇인가요?

진단 로그는 고객 스토리지 계정으로 전달되고 고객은 기본 설정에 따라 보존 정책을 설정할 수 있습니다. 진단 로그를 이벤트 허브 또는 Azure Monitor 로그에도 전송할 수 있습니다. 자세한 내용은 [Application Gateway 진단](application-gateway-diagnostics.md)을 참조하세요.

### <a name="how-do-i-get-audit-logs-for-application-gateway"></a>Application Gateway에 대한 감사 로그를 어떻게 얻나요?

Application Gateway에 대해 감사 로그를 사용할 수 있습니다. 포털에서 클릭 **활동 로그** 감사 로그에 액세스 하려면 application gateway의 메뉴 블레이드에 있습니다. 

### <a name="can-i-set-alerts-with-application-gateway"></a>Application Gateway로 경고를 설정할 수 있나요?

예. Application Gateway는 경고를 지원합니다. 경고는 메트릭에 대해 구성됩니다. Application Gateway 메트릭에 대해 자세히 알아보려면 [Application Gateway 메트릭](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics#metrics)을 참조하세요. 경고에 대해 자세히 알아보려면 [경고 알림 받기](../monitoring-and-diagnostics/insights-receive-alert-notifications.md)를 참조하세요.

### <a name="how-do-i-analyze-traffic-statistics-for-application-gateway"></a>Application Gateway에 대한 트래픽 통계를 분석하려면 어떻게 해야 하나요?

Azure Monitor 로그, Excel, Power BI 등의 여러 메커니즘을 통해 액세스 로그를 보고 분석할 수 있습니다.

또한 Application Gateway 액세스 로그에 대해 널리 사용되는 [GoAccess](https://goaccess.io/) 로그 분석기를 설치하고 실행하는 Resource Manager 템플릿을 게시했습니다. GoAccess는 고유 방문자, 요청한 파일, 호스트, 운영 체제, 브라우저, HTTP 상태 코드 및 기타 유용한 HTTP 트래픽 통계를 제공 합니다. 자세한 내용은 [GitHub의 Resource Manager 템플릿 폴더에 대한 추가 정보 파일](https://aka.ms/appgwgoaccessreadme)을 참조하세요.

### <a name="backend-health-returns-unknown-status-what-could-be-causing-this-status"></a>백 엔드 상태에서 알 수 없는 상태를 반환할 경우 이 상태의 원인은 무엇인가요?

가장 일반적인 원인에 대 한 액세스는 백 엔드 NSG를 사용자 지정 DNS에 의해 차단 됩니다 또는 응용 프로그램 게이트웨이 서브넷에서 UDR을 해야 합니다. 자세한 내용은 [Application Gateway에 대한 백 엔드 상태, 진단 로깅 및 메트릭](application-gateway-diagnostics.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계

Application Gateway에 대해 자세히 알아보려면 [Azure Application Gateway란?](overview.md)을 참조하세요.
