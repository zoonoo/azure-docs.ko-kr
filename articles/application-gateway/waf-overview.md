---
title: Azure Application Gateway에 대 한 웹 응용 프로그램 방화벽 소개
description: 이 문서에서는 Application Gateway용 WAF(웹 애플리케이션 방화벽)의 개요를 제공합니다.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.date: 2/22/2019
ms.author: amsriva
ms.topic: conceptual
ms.openlocfilehash: 830513a03bd65ca14cb0938ae599a676f1bb3bca
ms.sourcegitcommit: 6da4959d3a1ffcd8a781b709578668471ec6bf1b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2019
ms.locfileid: "58518187"
---
# <a name="web-application-firewall-for-azure-application-gateway"></a>Azure Application Gateway에 대 한 웹 응용 프로그램 방화벽

Azure Application Gateway는 일반적인 악용 및 취약점 으로부터 웹 응용 프로그램의 중앙 집중화 된 보호를 제공 하는 웹 응용 프로그램 방화벽 (WAF)를 제공 합니다. 웹 응용 프로그램 점점 더 일반적으로 알려진된 취약성을 악용 하는 악의적 공격의 대상입니다. SQL 삽입 및 사이트 간 스크립팅 되는 가장 일반적인 공격입니다.

응용 프로그램 코드에서 이러한 공격을 방지 하기는 쉽지 않습니다. 엄격한 유지 관리, 패치 및 모니터링 응용 프로그램 토폴로지의 여러 계층에 필요할 수 있습니다. 중앙 집중식된 웹 응용 프로그램 방화벽을 통해 보안 관리가 훨씬 간단 합니다. WAF는 또한 응용 프로그램 관리자 위협 및 침입 방지의 더 나은 assurance를 제공합니다.

WAF 솔루션은 중앙에서 각 개별 웹 응용 프로그램을 보호 하는 대신 알려진된 취약점을 패치 적용 하 여 더 빠른 보안 위협에 대응할 수 있습니다. 기존 application gateway 화재 벽을 사용 하도록 설정 하는 application gateway로 쉽게 변환할 수 있습니다.

Application Gateway WAF를 더해서 [코어 규칙 집합 (CR)](https://www.owasp.org/index.php/Category:OWASP_ModSecurity_Core_Rule_Set_Project) 3.0 또는 2.2.9의는 OWASP Open Web Application Security 프로젝트 (). WAF를 자동으로 추가 구성이 필요 없는 새로운 취약점에 대 한 보호를 포함 하도록 업데이트 합니다.

![응용 프로그램 게이트웨이 WAF 다이어그램](./media/waf-overview/WAF1.png)

Application Gateway는 응용 프로그램 배달 컨트롤러 (ADC)으로 작동합니다. Secure Sockets Layer (SSL) 종료, 쿠키 기반 세션 선호도, 라운드 로빈 부하 분산, 콘텐츠 기반 라우팅, 다중 웹 사이트 및 향상 된 보안 기능을 호스트 하는 기능을 제공 합니다.

SSL 정책 관리를 포함 하는 응용 프로그램 게이트웨이 보안 향상 기능 및 종단 간 SSL을 지원 합니다. Application Gateway WAF 통합 하 여 응용 프로그램 보안 강화 됩니다. 조합 일반적인 취약점 으로부터 웹 응용 프로그램을 보호합니다. 및 관리 하기 쉽게 구성 하려면 중앙 위치를 제공 합니다.

## <a name="benefits"></a>이점

이 섹션에서는 응용 프로그램 게이트웨이 및 해당 WAF를 제공 하는 핵심적인 장점을 설명 합니다.

### <a name="protection"></a>보호

* 웹 취약점 및 백 엔드 코드를 수정 하지 않고 공격 으로부터 웹 응용 프로그램을 보호 합니다.

* 동시에 여러 웹 응용 프로그램을 보호 합니다. Application Gateway의 인스턴스를 호스팅할 수는 웹 응용 프로그램 방화벽으로 보호 되는 최대 20 개의 웹 사이트입니다.

### <a name="monitoring"></a>모니터링

* 실시간 WAF 로그를 사용 하 여 웹 응용 프로그램에 대 한 공격을 모니터링 합니다. 로그와 통합 되어 [Azure Monitor](../monitoring-and-diagnostics/monitoring-overview.md) WAF 경고를 추적 하 여 추세를 쉽게 모니터링 합니다.

* Application Gateway WAF는 Azure Security Center와 통합 됩니다. Security Center는 모든 Azure 리소스의 보안 상태를 중앙 보기를 제공합니다.

### <a name="customization"></a>사용자 지정

* WAF 규칙 및 응용 프로그램 요구 사항에 맞게 거짓 긍정을 제거 하는 규칙 그룹을 사용자 지정할 수 있습니다.

## <a name="features"></a>기능

- SQL 주입 보호 합니다.
- 사이트 간 스크립팅 공격 보호 합니다.
- 명령 삽입, HTTP 요청 밀 반입, HTTP 응답 분할 원격 파일 포함 등 다른 일반적인 웹 공격 으로부터 보호 합니다.
- HTTP 프로토콜 위반 으로부터 보호 합니다.
- 누락 된 같은 HTTP 프로토콜 이상에 대 한 보호 호스트 사용자-에이전트 및 수락 헤더입니다.
- 봇, 크롤러 및 스캐너 로부터 보호 합니다.
- 일반적인 응용 프로그램 구성 오류 (Apache, IIS 등) 검색 합니다.
- 하 한과 상한을 사용 하 여 구성할 수 있는 요청 크기를 제한합니다.
- 제외 목록을 WAF 평가에서 특정 요청 특성을 생략할 수 있습니다. 일반적인 예에는 인증 또는 암호 필드에 사용 되는 Active Directory 삽입 토큰입니다.

### <a name="core-rule-sets"></a>핵심 규칙 집합

Application Gateway는 CRS 3.0 및 CRS 2.2.9 라는 두 개의 규칙 집합을 지원 합니다. 이러한 규칙을 악의적인 활동 으로부터 웹 응용 프로그램을 보호 합니다.

기본적으로 CRS 3.0으로 미리 구성 된 Application Gateway WAF는 제공 됩니다. 하지만 CRS 2.2.9를 대신 사용 하도록 선택할 수 있습니다. CRS 3.0 제품은 CRS 2.2.9 사용 하 여 비교 하는 거짓 긍정 감소. 할 수도 있습니다 [필요에 맞게 규칙을 사용자 지정](application-gateway-customize-waf-rules-portal.md)합니다.

WAF는 다음 웹 취약점 으로부터 보호 합니다.

- SQL 삽입 공격
- 사이트 간 스크립팅 공격
- 명령 삽입, HTTP 등의 다른 일반적인 공격을 요청 밀 반입, HTTP 응답 분할 및 원격 파일 포함
- HTTP 프로토콜 위반
- 예: 누락 HTTP 프로토콜 이상 호스트 사용자-에이전트 및 수락 헤더
- 봇, 크롤러 및 스캐너
- 일반적인 응용 프로그램 구성 오류 (예: Apache 및 IIS)

#### <a name="owasp-crs-30"></a>OWASP CRS 3.0

다음 표에 나와 있는 것 처럼 13 개의 규칙 그룹이 포함 하는 CRS 3.0. 각 그룹에는 사용할 수 있는 여러 규칙을 포함 합니다.

|규칙 그룹|설명|
|---|---|
|**[REQUEST-911-METHOD-ENFORCEMENT](application-gateway-crs-rulegroups-rules.md#crs911)**|잠금 메서드 (PUT, PATCH)|
|**[REQUEST-913-SCANNER-DETECTION](application-gateway-crs-rulegroups-rules.md#crs913)**|포트 및 환경 스캐너 로부터 보호|
|**[REQUEST-920-PROTOCOL-ENFORCEMENT](application-gateway-crs-rulegroups-rules.md#crs920)**|프로토콜 및 인코딩 문제 로부터 보호|
|**[REQUEST-921-PROTOCOL-ATTACK](application-gateway-crs-rulegroups-rules.md#crs921)**|헤더 주입, 밀수 요청 및 응답 분할 로부터 보호|
|**[REQUEST-930-APPLICATION-ATTACK-LFI](application-gateway-crs-rulegroups-rules.md#crs930)**|파일 및 경로 공격 으로부터 보호|
|**[REQUEST-931-APPLICATION-ATTACK-RFI](application-gateway-crs-rulegroups-rules.md#crs931)**|원격 파일 포함 (RFI) 공격 으로부터 보호|
|**[REQUEST-932-APPLICATION-ATTACK-RCE](application-gateway-crs-rulegroups-rules.md#crs932)**|원격 코드 실행 공격을 다시 보호|
|**[REQUEST-933-APPLICATION-ATTACK-PHP](application-gateway-crs-rulegroups-rules.md#crs933)**|PHP 삽입 공격 으로부터 보호|
|**[REQUEST-941-APPLICATION-ATTACK-XSS](application-gateway-crs-rulegroups-rules.md#crs941)**|사이트 간 스크립팅 공격 으로부터 보호|
|**[REQUEST-942-APPLICATION-ATTACK-SQLI](application-gateway-crs-rulegroups-rules.md#crs942)**|SQL 주입 공격 으로부터 보호|
|**[REQUEST-943-APPLICATION-ATTACK-SESSION-FIXATION](application-gateway-crs-rulegroups-rules.md#crs943)**|세션 고정 공격 으로부터 보호|

#### <a name="owasp-crs-229"></a>OWASP CRS 2.2.9

다음 표에 나와 있는 것 처럼 10 개의 규칙 그룹이 포함 하는 CRS 2.2.9 합니다. 각 그룹에는 사용할 수 있는 여러 규칙을 포함 합니다.

|규칙 그룹|설명|
|---|---|
|**[crs_20_protocol_violations](application-gateway-crs-rulegroups-rules.md#crs20)**|프로토콜 위반 (예: 잘못 된 문자 또는 요청 본문을 사용 하 여 GET) 으로부터 보호|
|**[crs_21_protocol_anomalies](application-gateway-crs-rulegroups-rules.md#crs21)**|잘못 된 헤더 정보 로부터 보호|
|**[crs_23_request_limits](application-gateway-crs-rulegroups-rules.md#crs23)**|인수 또는 제한을 초과 하는 파일 보호|
|**[crs_30_http_policy](application-gateway-crs-rulegroups-rules.md#crs30)**|제한 된 메서드, 헤더 및 파일 유형 으로부터 보호|
|**[crs_35_bad_robots](application-gateway-crs-rulegroups-rules.md#crs35)**|웹 크롤러 및 스캐너 로부터 보호|
|**[crs_40_generic_attacks](application-gateway-crs-rulegroups-rules.md#crs40)**|제네릭 공격 (세션 고정, 원격 파일 포함의 경우 PHP 주입 등) 으로부터 보호|
|**[crs_41_sql_injection_attacks](application-gateway-crs-rulegroups-rules.md#crs41sql)**|SQL 주입 공격 으로부터 보호|
|**[crs_41_xss_attacks](application-gateway-crs-rulegroups-rules.md#crs41xss)**|사이트 간 스크립팅 공격 으로부터 보호|
|**[crs_42_tight_security](application-gateway-crs-rulegroups-rules.md#crs42)**|경로 통과 공격 으로부터 보호|
|**[crs_45_trojans](application-gateway-crs-rulegroups-rules.md#crs45)**|백도어 트로이 목마 로부터 보호|

### <a name="waf-modes"></a>WAF 모드

Application Gateway WAF는 다음 두 가지 모드에서 실행 되도록 구성할 수 있습니다.

* **검색 모드**: 모니터링 하 고 모든 위협 경고를 기록 합니다. Application Gateway에 대 한 진단 로깅 켜면 합니다 **진단** 섹션입니다. 또한 WAF 로그가 선택 되 고 켜져 있는지 확인 해야 합니다. 검색 모드에서 작동 하는 경우 웹 응용 프로그램 방화벽에서 들어오는 요청을 차단 하지 않습니다.
* **방지 모드**: 블록 침입 및 규칙을 검색 하는 공격입니다. 공격자가 "403 무단된 액세스" 예외를 수신 하 고 연결이 종료 됩니다. 방지 모드는 이러한 공격을 WAF 로그를 기록합니다.

### <a name="anomaly-scoring-mode"></a>변칙 점수 매기기 모드
 
OWASP에 트래픽을 차단 하도록 여부를 결정 하기 위한 두 가지 모드가 있습니다. 일반 모드 및 변칙 점수 매기기 모드입니다.

일반 모드에서 모든 규칙과 일치 하는 트래픽은 다른 규칙이 일치와 독립적으로 간주 됩니다. 이 모드는 이해 하기 쉽습니다. 하지만 제한은 얼마나 많은 규칙이 특정 요청을 일치 하는 방법에 대 한 정보가 부족 합니다. 따라서 변칙 점수 매기기 모드가 도입 되었습니다. 해당 OWASP 3에 대 한 기본값입니다. *x*합니다.

변칙 점수 매기기 모드 방화벽 방지 모드에 있을 때에 즉시 모든 규칙과 일치 하는 트래픽을 차단 되지 않습니다. 규칙에는 특정 심각도: *중요*, *오류*를 *경고*, 또는 *공지*합니다. 해당 심각도 변칙 점수 라고 하는 요청에 대 한 숫자 값을 영향을 줍니다. 예를 들어 하나 *경고* 일치 점수 3에 기여 하는 규칙입니다. 하나의 *위험* 일치 항목 5에 기여 하는 규칙입니다.

임계값이 트래픽 차단 하는 변칙 점수에 대 한 5 있습니다. 따라서 단일 *위험* 일치 하는 규칙은 Application Gateway WAF도 방지 모드에서에서 요청을 차단 하는 데 충분 합니다. 하나를 제외한 *경고* 일치 항목에만 트래픽을 차단할를 자체적으로 충분 하지 않습니다는 변칙 점수 3을 증가 하는 규칙입니다.

> [!NOTE]
> WAF 규칙 트래픽에 적용 하는 경우 기록 되는 메시지 동작 값을 "차단 됨."를 포함 합니다. 하지만 트래픽은 실제로 5는 변칙 점수에 대 한 차단 이상.  

### <a name="waf-monitoring"></a>WAF 모니터링

Application Gateway의 상태를 모니터링하는 것이 중요합니다. Azure Security Center에서 Azure Monitor와 통합 하 여 지원 되는 WAF 및 보호 하는 응용 프로그램의 상태를 모니터링 하 고 Azure Monitor 기록 합니다.

![Application Gateway WAF 진단 다이어그램](./media/waf-overview/diagnostics.png)

#### <a name="azure-monitor"></a>Azure Monitor

응용 프로그램 게이트웨이 로그와 통합 되어 [Azure Monitor](../monitoring-and-diagnostics/monitoring-overview.md)합니다. 이 통해 WAF 경고 및 로그를 포함 한 진단 정보를 추적할 수 있습니다. 이 기능에 액세스할 수 있습니다 합니다 **진단** 포털 또는 Azure Monitor를 통해 직접 응용 프로그램 게이트웨이 리소스에 대 한 탭 합니다. 로그를 사용 하도록 설정 하는 방법에 대 한 자세한 내용은 참조 하세요 [Application Gateway 진단](application-gateway-diagnostics.md)합니다.

#### <a name="azure-security-center"></a>Azure Security Center

[Security Center](../security-center/security-center-intro.md) 방지, 감지 및 위협에 대응 하는 데 도움이 됩니다. 에 대 한 향상 된 가시성과 제어권을 통해 Azure 리소스의 보안을 제공합니다. Application Gateway는 [Security Center와 통합](application-gateway-integration-security-center.md)합니다. Security Center는 보호 되지 않는 웹 응용 프로그램을 검색 하도록 환경을 검색 합니다. 이 이러한 취약 한 리소스를 보호 하기 위해 Application Gateway WAF를 권장할 수 있습니다. Security Center에서 직접 방화벽을 만듭니다. 이러한 WAF 인스턴스는 Security Center와 통합 됩니다. 보내는 경고 및 상태 정보 Security Center에 보고 합니다.

![Security Center 개요 창](./media/waf-overview/figure1.png)

#### <a name="logging"></a>로깅

Application Gateway WAF는 각 위협 검색에 대 한 상세 보고를 제공 합니다. 로깅은은 Azure 진단 로그와 통합 됩니다. 경고를.json 형식으로 기록 됩니다. 이러한 로그는 [Azure Monitor 로그](../azure-monitor/insights/azure-networking-analytics.md)와 통합될 수 있습니다.

![Application Gateway 진단 로그는 windows](./media/waf-overview/waf2.png)

```json
{
  "resourceId": "/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/{resourceGroupId}/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/{appGatewayName}",
  "operationName": "ApplicationGatewayFirewall",
  "time": "2017-03-20T15:52:09.1494499Z",
  "category": "ApplicationGatewayFirewallLog",
  "properties": {
    "instanceId": "ApplicationGatewayRole_IN_0",
    "clientIp": "104.210.252.3",
    "clientPort": "4835",
    "requestUri": "/?a=%3Cscript%3Ealert(%22Hello%22);%3C/script%3E",
    "ruleSetType": "OWASP",
    "ruleSetVersion": "3.0",
    "ruleId": "941320",
    "message": "Possible XSS Attack Detected - HTML Tag Handler",
    "action": "Blocked",
    "site": "Global",
    "details": {
      "message": "Warning. Pattern match \"<(a|abbr|acronym|address|applet|area|audioscope|b|base|basefront|bdo|bgsound|big|blackface|blink|blockquote|body|bq|br|button|caption|center|cite|code|col|colgroup|comment|dd|del|dfn|dir|div|dl|dt|em|embed|fieldset|fn|font|form|frame|frameset|h1|head|h ...\" at ARGS:a.",
      "data": "Matched Data: <script> found within ARGS:a: <script>alert(\\x22hello\\x22);</script>",
      "file": "rules/REQUEST-941-APPLICATION-ATTACK-XSS.conf",
      "line": "865"
    }
  }
} 

```

## <a name="application-gateway-waf-sku-pricing"></a>Application Gateway WAF SKU 가격 책정

Application Gateway WAF는 새에서 사용 가능한 SKU입니다. 이 SKU는 클래식 배포 모델을 Azure Resource Manager 프로 비전 모델 에서만에서 사용할 수 있습니다. 또한 WAF SKU는 중간 규모 및 대규모 Application Gateway 인스턴스 크기에만 제공 됩니다. Application Gateway에 대 한 모든 제한 사항이 WAF SKU에도 적용 됩니다.

가격 책정은 기준 시간당 게이트웨이 인스턴스 요금 및 데이터 처리 요금입니다. [Application Gateway 가격](https://azure.microsoft.com/pricing/details/application-gateway/) WAF SKU 표준 SKU 요금과 다릅니다. 데이터 처리 요금은 동일 합니다. 각 규칙 또는 규칙 그룹 무료로 있습니다. 동일한 웹 응용 프로그램 방화벽 뒤에 여러 웹 응용 프로그램을 보호할 수 있습니다. 여러 응용 프로그램을 지 원하는 청구 되지 않습니다.

## <a name="next-steps"></a>다음 단계

참조 [Application Gateway에서 웹 응용 프로그램 방화벽을 구성 하는 방법](tutorial-restrict-web-traffic-powershell.md)합니다.
