---
title: "Azure Application Gateway에 대한 질문과 대답 | Microsoft Docs"
description: "이 페이지에서는 Azure Application Gateway에 대한 질문과 대답을 제공합니다."
documentationcenter: na
services: application-gateway
author: georgewallace
manager: timlt
editor: tysonn
ms.assetid: d54ee7ec-4d6b-4db7-8a17-6513fda7e392
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/28/2017
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: 432752c895fca3721e78fb6eb17b5a3e5c4ca495
ms.openlocfilehash: 037045c4e76d0fb8e96944fe8a3235223594a034
ms.lasthandoff: 03/30/2017


---

# <a name="frequently-asked-questions-for-application-gateway"></a>Application Gateway에 대한 질문과 대답

## <a name="general"></a>일반

**Q. Application Gateway란?**

Azure Application Gateway는 서비스 형태의 ADC(응용 프로그램 전달 컨트롤러)이며 응용 프로그램에 대한 다양한 계층 7 부하 분산 기능을 제공합니다. Azure에서 완전히 관리되는 고가용성의 확장성 있는 서비스를 제공합니다.

**Q. Application Gateway에서 지원하는 기능은 어떤 것이 있나요?**

Application Gateway에서는 SSL 오프로딩 및 종단 간 SSL, 웹 응용 프로그램 방화벽(미리 보기), 쿠키 기반 세션 선호도, url 경로 기반 라우팅, 다중 사이트 호스팅 등을 지원합니다. 지원되는 기능의 전체 목록은 [Application Gateway 소개](application-gateway-introduction.md)를 참조하세요.

**Q. Application Gateway와 Azure Load Balancer 간의 차이는 무엇인가요?**

Application Gateway는 계층 7 부하 분산 장치입니다. 따라서 Application Gateway는 웹 트래픽(HTTP/HTTPS/WebSocket)만 처리합니다. SSL 종료, 쿠키 기반 세션 선호도, 트래픽 부하 분산을 위한 라운드 로빈과 같은 응용 프로그램 부하 분산 기능을 지원합니다. 부하 분산 장치, 계층 4(TCP/UDP)에서 트래픽 부하 분산.

**Q. Application Gateway에서 지원하는 프로토콜은 무엇인가요?**

Application Gateway는 HTTP, HTTPS 및 WebSocket을 지원합니다.

**Q. 현재 백 엔드 풀의 일부로 어떤 리소스가 지원되나요?**

백 엔드 풀은 NIC, 가상 컴퓨터 크기 집합, 공용 IP, 내부 IP 및 FQDN(정규화된 도메인 이름)으로 구성될 수 있습니다. Azure Web Apps에 대한 지원은 현재 제공되지 않습니다. Application Gateway 백 엔드 풀 멤버는 가용성 집합에 연결되지 않습니다. 백 엔드 풀의 멤버는 IP 연결이 있는 경우 클러스터, 데이터 센터 간 또는 Azure 외부에 있을 수 있습니다.

**Q. 어떤 지역에서 서비스를 사용할 수 있습니까?**

Application Gateway는 모든 공용 Azure 지역에서 사용할 수 있습니다. [Azure China](https://www.azure.cn/) 및 [Azure Government](https://azure.microsoft.com/en-us/overview/clouds/government/)에서도 사용할 수 있습니다.

**Q. 내 구독에 대한 전용 배포인가요? 아니면 고객 간에 공유되나요?**

Application Gateway는 가상 네트워크에서 전용 배포입니다.

**Q. HTTP->HTTPS 리디렉션이 지원되나요?**

현재는 지원되지 않습니다.

**Q. Application Gateway의 IP 및 DNS는 어디에서 확인하나요?**

공용 IP 주소를 끝점으로 사용하는 경우 이 정보를 공용 IP 주소 리소스 또는 포털의 Application Gateway에 대한 개요 페이지에서 확인할 수 있습니다. 내부 IP 주소는 개요 페이지에서 확인할 수 있습니다.

**Q. Application Gateway의 수명 중에 IP 또는 DNS가 변경되나요?**

게이트웨이가 고객에 의해 중지 및 시작된 경우 VIP를 변경할 수 있습니다. Application Gateway와 연결된 DNS는 게이트웨이 수명 중에 변경되지 않습니다. 이러한 이유로 CNAME 별칭을 사용하고 Application Gateway의 DNS 주소를 가리키도록 하는 것이 좋습니다.

**Q. Application Gateway에서 고정 IP를 지원하나요?**

아니요, Application Gateway에서는 고정 공용 IP 주소를 지원하지 않고 고정 내부 IP를 지원합니다.

**Q. Application Gateway가 게이트웨이에서 여러 공용 IP를 지원하나요?**

Application Gateway에서는 하나의 공용 IP 주소만 지원됩니다.

**Q. Application Gateway에서 x-forwarded-for 헤더를 지원하나요?**

예, Application Gateway는 백 엔드로 전달되는 요청에 x-forwarded-for, x-forwarded-proto 및 x-forwarded-port 헤더를 삽입합니다. x-forwarded-for 헤더의 형식은 쉼표로 구분된 IP:Port 목록입니다. x-forwarded-proto 에 대해 유효한 값은 http 또는 https입니다. X-forwarded-port는 Application Gateway에서 요청이 도달한 포트를 지정합니다.

## <a name="configuration"></a>구성

**Q. Application Gateway가 가상 네트워크에서 항상 배포되나요?**

예, Application Gateway는 항상 가상 네트워크 서브넷에 배포됩니다. 이 서브넷은 Application Gateway만 포함할 수 있습니다.

**Q. Application Gateway는 가상 네트워크 외부 인스턴스와 통신할 수 있나요?**

Application Gateway는 IP 연결이 있는 경우 가상 네트워크 외부 인스턴스와 통신할 수 있습니다. 백 엔드 풀 멤버로 내부 IP를 사용할 계획인 경우 [VNET 피어링](../virtual-network/virtual-network-peering-overview.md) 또는 [VPN Gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md)가 필요합니다.

**Q. Application Gateway 서브넷에서 다른 항목을 배포할 수 있나요?**

아니요, 하지만 다른 응용 프로그램 게이트웨이는 서브넷에 배포할 수 있습니다.

**Q. Application Gateway 서브넷에서 네트워크 보안 그룹이 지원되나요?**

Application Gateway 서브넷에서 네트워크 보안 그룹이 지원되지만 백 엔드 상태가 적절히 작동하려면 포트 65503-65534에 대해 예외가 적용되어야 합니다. 아웃바운드 인터넷 연결이 차단되지 않아야 합니다.

**Q. Application Gateway에서 한도는 어떻게 되나요? 이러한 한도를 늘릴 수 있나요?**

한도를 보려면 [Application Gateway 한도](../azure-subscription-service-limits.md#application-gateway-limits)를 방문하세요.

**Q. 외부 및 내부 트래픽 모두에 Application Gateway를 동시에 사용할 수 있나요?**

예, Application Gateway에서는 Application Gateway당 내부 IP 하나와 외부 IP 하나를 지원합니다.

**Q. VNet 피어링이 지원되나요?**

예, VNet 피어링이 지원되며 다른 가상 네트워크에서 트래픽을 부하 분산시키는 데 도움이 됩니다.

**Q. ExpressRoute 또는 VPN 터널과 연결된 경우 온-프레미스 서버와 통신할 수 있나요?**

예, 트래픽이 허용되기만 한다면 가능합니다.

**Q. 한 개의 백 엔드 풀에서 서로 다른 포트로 많은 응용 프로그램을 제공할 수 있나요?**

마이크로 서비스 아키텍처가 지원됩니다. 서로 다른 포트의 프로브에 대해 여러 http 설정이 구성되어야 합니다.

**Q. 사용자 지정 프로브가 응답 데이터에 와일드 카드/regex를 지원하나요?**

사용자 지정 프로브는 응답 데이터에 와일드 카드 또는 regex를 지원하지 않습니다.

**Q. 사용자 지정 프로브에 대한 호스트 필드는 무엇을 나타내나요?**

호스트 필드는 프로브를 보낼 이름을 지정합니다. 다중 사이트를 Application Gateway에 구성하는 경우에만 적용할 수 있습니다. 그렇지 않으면 '127.0.0.1'을 사용합니다. 이 값은 VM 호스트 이름과 다르며 \<프로토콜\>://\<호스트\>:\<포트\>\<경로\> 형식입니다. 

## <a name="performance"></a>성능

**Q. Application Gateway는 고가용성과 확장성을 어떤 방식으로 지원하나요?**

Application Gateway는 2개 이상의 인스턴스가 배포된 경우 고가용성 시나리오를 지원합니다. Azure에서는 이러한 인스턴스를 업데이트 및 장애 도메인 간에 배포하여 모든 인스턴스가 동시에 실패하는 일이 없도록 합니다. Application Gateway는 로드를 공유하기 위해 동일한 게이트웨이의 여러 인스턴스를 추가하여 확장성을 지원합니다.

**Q. Application Gateway에서 데이터 센터 간 DR 시나리오를 어떻게 달성할 수 있나요?**

고객은 Traffic Manager를 사용하여 서로 다른 데이터 센터에 있는 여러 Application Gateway 간에 트래픽을 분산시킬 수 있습니다.

**Q. 자동 크기 조정이 지원되나요?**

아니요, 하지만 Application Gateway는 임계값에 도달할 때 경고하는 데 사용할 수 있는 처리량 메트릭을 포함합니다. 인스턴스를 수동으로 추가하거나 크기를 변경해도 게이트웨이가 다시 시작되지 않으며 기존 트래픽에 영향을 주지 않습니다.

**Q. 수동 강화/규모 축소 시 가동 중지 시간이 발생하나요?**

가동 중지 시간 없이 인스턴스가 업그레이드 도메인 및 장애 도메인 간에 배포됩니다.

**Q. 중단 없이 인스턴스 크기를 중간에서 큼으로 변경할 수 있나요?**

예, Azure에서는 인스턴스를 업데이트 및 장애 도메인 간에 배포하여 모든 인스턴스가 동시에 실패하는 일이 없도록 합니다. Application Gateway는 로드를 공유하기 위해 동일한 게이트웨이의 여러 인스턴스를 추가하여 확장성을 지원합니다.

## <a name="ssl-configuration"></a>SSL 구성

**Q. Application Gateway에서 어떤 인증서가 지원되나요?**

자체 서명된 인증서, CA 인증서 및 와일드 카드 인증서가 지원됩니다. EV 인증서는 지원되지 않습니다.

**Q. Application Gateway에서 지원되는 현재 암호 그룹은 무엇인가요?**

다음은 현재 지원되는 암호 그룹을 우선 순위에 따라 나열한 것입니다.

TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384_P384

TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256_P256

TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384_P256

TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256_P256

TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA_P256

TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA_P256

TLS_RSA_WITH_AES_256_GCM_SHA384

TLS_RSA_WITH_AES_128_GCM_SHA256

TLS_RSA_WITH_AES_256_CBC_SHA256

TLS_RSA_WITH_AES_128_CBC_SHA256

TLS_RSA_WITH_AES_256_CBC_SHA

TLS_RSA_WITH_AES_128_CBC_SHA

TLS_RSA_WITH_3DES_EDE_CBC_SHA

**Q. Application Gateway에서 백 엔드에 대해 트래픽의 재암호화를 지원하나요?**

예, Applicated Gateway는 SSL 오프로드와 백 엔드에 대한 트래픽을 재암호화하는 종단 간 SSL을 지원합니다.

**Q. SSL 프로토콜 버전을 제어하는 SSL 정책을 구성할 수 있나요?**

예, TLS1.0, TLS1.1 및 TLS1.2를 거부하도록 Application Gateway를 구성할 수 있습니다. SSL 2.0 및 3.0은 기본적으로 비활성화되며 구성할 수 없습니다.

**Q. 암호 그룹을 제어하는 SSL 정책을 구성할 수 있나요?**

아니요, 현재는 아닙니다.

**Q. 몇 개의 SSL 인증서가 지원되나요?**

최대 20개의 SSL 인증서가 지원됩니다.

**Q. 백 엔드 재암호화에 몇 개의 인증 인증서가 지원되나요?**

5의 기본값으로 최대 10개의 인증 인증서가 지원됩니다.

**Q. Application Gateway가 Azure Key Vault와 고유하게 통합되나요?**

아니요, Azure Key Vault와 통합되지 않습니다.

## <a name="web-application-firewall-waf-configuration"></a>WAF(웹 응용 프로그램 방화벽) 구성

**Q. WAF SKU는 표준 SKU에서 사용 가능한 모든 기능을 제공하나요?**

예, WAF는 표준 SKU에서 모든 기능을 지원합니다.

**Q. Application Gateway에서 지원하는 CRS 버전은 무엇인가요?**

Application Gateway는 CRS [2.2.9](application-gateway-crs-rulegroups-rules.md#owasp229) 및 CRS [3.0](application-gateway-crs-rulegroups-rules.md#owasp30)을 지원합니다.

**Q. WAF를 모니터링하려면 어떻게 하나요?**

WAF는 진단 로깅을 통해 모니터링되며 진단 로깅에 대한 자세한 내용은 [Application Gateway에 대한 진단 로깅 및 메트릭](application-gateway-diagnostics.md)에서 확인할 수 있습니다.

**Q. 검색 모드에서 트래픽을 차단하나요?**

아니요, 검색 모드는 WAF 규칙을 트리거한 트래픽만 로깅합니다.

**Q. WAF 규칙을 사용자 지정하려면 어떻게 하나요?**

예, WAF 규칙은 사용자 지정이 가능합니다. 사용자 지정 방법에 대한 자세한 내용을 보려면 [WAF 규칙 그룹 및 규칙 사용자 지정](application-gateway-customize-waf-rules-portal.md)을 방문하세요.

**Q. 현재 사용 가능한 규칙은 무엇인가요?**

현재 WAF는 [OWASP 상위 10개 취약점](https://www.owasp.org/index.php/Top10#OWASP_Top_10_for_2013)에서 확인할 수 있는 OWASP(Open Web Application Security Project)에 의해 식별된 상위 10개 취약점 대부분에 대해 보안 기준을 제공하는 CRS [2.2.9](application-gateway-crs-rulegroups-rules.md#owasp229) 및 [3.0](application-gateway-crs-rulegroups-rules.md#owasp30)을 지원합니다.

* SQL 삽입 공격 보호

* 교차 사이트 스크립팅 공격 보호

* 명령 삽입, HTTP 요청 밀반입, HTTP 응답 분할, 원격 파일 포함 공격 등의 일반 웹 공격 보호

* HTTP 프로토콜 위반 보호

* 누락된 호스트 사용자-에이전트 및 수락 헤더 같은 HTTP 프로토콜 이상 보호

* 보트, 크롤러 및 스캐너 방지

* 일반적인 응용 프로그램 구성 오류(예: Apache, IIS 등) 검색

**Q. WAF에서 DDoS 방지도 지원하나요?**

아니요, WAF는 DDoS 방지를 제공하지 않습니다.

## <a name="diagnostics-and-logging"></a>진단 및 로깅

**Q. Application Gateway에서 어떤 유형의 로그를 사용할 수 있나요?**

Application Gateway에서는 3가지 로그를 사용할 수 있습니다. 이러한 로그 및 기타 진단 기능에 대한 자세한 내용은 [Application Gateway에 대한 백 엔드 상태, 진단 로깅 및 메트릭](application-gateway-diagnostics.md)을 참조하세요.

- **ApplicationGatewayAccessLog** - 이 로그는 Application Gateway 프런트 엔드에 제출된 각 요청을 포함합니다. 이 데이터에는 호출자의 IP, 요청된 URL, 응답 대기 시간, 반환 코드, 바이트 입출력을 포함합니다. 액세스 로그는 300초마다 수집됩니다. 이 로그에는 응용 프로그램 게이트웨이 인스턴스당 하나의 레코드가 포함됩니다.
- **ApplicationGatewayPerformanceLog** - 이 로그는 인스턴스 단위로 처리된 총 요청, 처리량(바이트), 실패한 총 요청, 실패한 요청 수, 정상 및 비정상 백 엔드 인스턴스 수 등의 성능 정보를 캡처합니다.
- **ApplicationGatewayFirewallLog** - 이 로그는 웹 응용 프로그램 방화벽으로 구성된 Application Gateway의 검색 모드 또는 방지 모드를 통해 로깅된 요청을 포함합니다.

**Q. 내 백 엔드 풀 멤버가 정상인지 어떻게 알 수 있나요?**

PowerShell cmdlet `Get-AzureRmApplicationGatewayBackendHealth`를 사용하거나 [Application Gateway 진단](application-gateway-diagnostics.md)을 방문하여 포털을 통해 상태를 확인할 수 있습니다.

**Q. 진단 로그에서 보존 정책은 무엇인가요?**

진단 로그는 고객 저장소 계정으로 전달되고 고객은 기본 설정에 따라 보존 정책을 설정할 수 있습니다. 진단 로그는 Event Hub 또는 Log Analytics로도 전송할 수 있습니다. 자세한 내용을 보려면 [Application Gateway 진단](application-gateway-diagnostics.md)을 방문하세요.

**Q. Application Gateway에 대한 감사 로그를 어떻게 얻나요?**

Application Gateway에 대해 감사 로그를 사용할 수 있습니다. 포털에서 감사 로그에 액세스하려면 Application Gateway의 메뉴 블레이드에서 **활동 로그**를 클릭합니다. 

**Q. Application Gateway로 경고를 설정할 수 있나요?**

예, Application Gateway는 경고를 지원하며 메트릭에 따라 경고를 해제하도록 구성합니다.  Application Gateway에서는 현재 경고를 구성할 수 있는 "처리량" 메트릭을 포함합니다. 경고에 대한 자세한 내용을 보려면 [경고 알림 받기](../monitoring-and-diagnostics/insights-receive-alert-notifications.md)를 방문하세요.

**Q. 백 엔드 상태에서 알 수 없는 상태를 반환할 경우 원인은 무엇인가요?**

백 엔드에 액세스하는 가장 일반적인 이유는 NSG 또는 사용자 지정 DNS로 차단되는 경우입니다. 자세한 내용은 [Application Gateway에 대한 백 엔드 상태, 진단 로깅 및 메트릭](application-gateway-diagnostics.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계

Application Gateway에 대한 자세한 내용은 [Application Gateway 소개](application-gateway-introduction.md)를 참조하세요.
