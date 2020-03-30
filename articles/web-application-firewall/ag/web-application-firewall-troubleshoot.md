---
title: 문제 해결 - Azure 웹 응용 프로그램 방화벽
description: 이 문서에서는 Azure 응용 프로그램 게이트웨이에 대한 WAF(웹 응용 프로그램 방화벽)에 대한 문제 해결 정보를 제공합니다.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.date: 11/14/2019
ms.author: ant
ms.topic: conceptual
ms.openlocfilehash: 33c85752903edd618044ccbab06aff7df9a791da
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74046185"
---
# <a name="troubleshoot-web-application-firewall-waf-for-azure-application-gateway"></a>Azure 응용 프로그램 게이트웨이에 대한 WAF(웹 응용 프로그램 방화벽) 문제 해결

WAF(웹 응용 프로그램 방화벽)를 통과해야 하는 요청이 차단된 경우 수행할 수 있는 몇 가지 방법이 있습니다.

먼저 [WAF 개요](ag-overview.md) 및 [WAF 구성](application-gateway-waf-configuration.md) 문서를 읽었습니다. 또한 [WAF 모니터링을](../../application-gateway/application-gateway-diagnostics.md) 사용하도록 설정했는지 확인하려면 이 문서에서 WAF가 작동하는 방식, WAF 규칙 세트의 작동 방식 및 WAF 로그에 액세스하는 방법을 설명합니다.

## <a name="understanding-waf-logs"></a>WAF 로그 이해

WAF 로그의 목적은 WAF에 의해 일치하거나 차단되는 모든 요청을 표시하는 것입니다. 일치하거나 차단된 모든 평가된 요청의 원장입니다. WAF가 안 되는 요청(거짓 긍정)을 차단하는 경우 몇 가지 작업을 수행할 수 있습니다. 먼저 범위를 좁혀 특정 요청을 찾습니다. 로그를 통해 요청의 특정 URI, 타임스탬프 또는 트랜잭션 ID를 찾습니다. 연결된 로그 항목을 찾으면 거짓 긍정에 따라 조치를 시작할 수 있습니다.

예를 들어 WAF를 통과하려는 문자열 *1=1을* 포함하는 합법적인 트래픽이 있다고 가정해 보겠습니다. 요청을 시도하면 WAF는 매개 변수 또는 필드에 *1=1* 문자열이 포함된 트래픽을 차단합니다. SQL 주입 공격과 관련된 문자열입니다. 로그를 살펴보고 요청의 타임스탬프와 차단/일치하는 규칙을 볼 수 있습니다.

다음 예제에서는 TransactionId 필드를 사용하여 동일한 요청 중에 네 개의 규칙이 트리거되는 것을 볼 수 있습니다. 첫 번째는 사용자가 요청에 숫자 /IP URL을 사용했기 때문에 일치한다고 말하며 경고이기 때문에 이상 점수가 3 으로 증가합니다. 일치하는 다음 규칙은 942130입니다. 필드에서 *1=1을* `details.data` 볼 수 있습니다. 이렇게 하면 경고이기 때문에 이상 점수가 다시 3점 증가합니다. 일반적으로 **일치하는** 동작이 있는 모든 규칙은 변칙 점수를 증가시키고 이 시점에서 변칙 점수는 6이 됩니다. 자세한 내용은 [변칙 점수 매기기 모드를](ag-overview.md#anomaly-scoring-mode)참조하십시오.

마지막 두 로그 항목은 변칙 점수가 충분히 높았기 때문에 요청이 차단된 것을 보여 준다. 이러한 항목은 다른 두 항목과 다른 작업을 갖습니다. 실제로 요청을 *차단한* 것으로 표시됩니다. 이러한 규칙은 필수이며 비활성화할 수 없습니다. 그들은 규칙으로 생각해서는 안되며 WAF 내부의 핵심 인프라로 간주되어서는 안됩니다.

```json
{ 
    "resourceId": "/SUBSCRIPTIONS/A6F44B25-259E-4AF5-888A-386FED92C11B/RESOURCEGROUPS/DEMOWAF_V2/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/DEMOWAF-V2", 
    "operationName": "ApplicationGatewayFirewall", 
    "category": "ApplicationGatewayFirewallLog", 
    "properties": { 
        "instanceId": "appgw_3", 
        "clientIp": "167.220.2.139", 
        "clientPort": "", 
        "requestUri": "\/", 
        "ruleSetType": "OWASP_CRS", 
        "ruleSetVersion": "3.0.0", 
        "ruleId": "920350", 
        "message": "Host header is a numeric IP address", 
        "action": "Matched", 
        "site": "Global", 
        "details": { 
            "message": "Warning. Pattern match \\\"^[\\\\\\\\d.:]+$\\\" at REQUEST_HEADERS:Host. ", 
            "data": "40.90.218.160", 
            "file": "rules\/REQUEST-920-PROTOCOL-ENFORCEMENT.conf\\\"", 
            "line": "791" 
        }, 
        "hostname": "vm000003", 
        "transactionId": "AcAcAcAcAKH@AcAcAcAcAyAt" 
    } 
} 
{ 
    "resourceId": "/SUBSCRIPTIONS/A6F44B25-259E-4AF5-888A-386FED92C11B/RESOURCEGROUPS/DEMOWAF_V2/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/DEMOWAF-V2", 
    "operationName": "ApplicationGatewayFirewall", 
    "category": "ApplicationGatewayFirewallLog", 
    "properties": { 
        "instanceId": "appgw_3", 
        "clientIp": "167.220.2.139", 
        "clientPort": "", 
        "requestUri": "\/", 
        "ruleSetType": "OWASP_CRS", 
        "ruleSetVersion": "3.0.0", 
        "ruleId": "942130", 
        "message": "SQL Injection Attack: SQL Tautology Detected.", 
        "action": "Matched", 
        "site": "Global", 
        "details": { 
            "message": "Warning. Pattern match \\\"(?i:([\\\\\\\\s'\\\\\\\"`\\\\\\\\(\\\\\\\\)]*?)([\\\\\\\\d\\\\\\\\w]++)([\\\\\\\\s'\\\\\\\"`\\\\\\\\(\\\\\\\\)]*?)(?:(?:=|\\u003c=\\u003e|r?like|sounds\\\\\\\\s+like|regexp)([\\\\\\\\s'\\\\\\\"`\\\\\\\\(\\\\\\\\)]*?)\\\\\\\\2|(?:!=|\\u003c=|\\u003e=|\\u003c\\u003e|\\u003c|\\u003e|\\\\\\\\^|is\\\\\\\\s+not|not\\\\\\\\s+like|not\\\\\\\\s+regexp)([\\\\\\\\s'\\\\\\\"`\\\\\\\\(\\\\\\\\)]*?)(?!\\\\\\\\2)([\\\\\\\\d\\\\\\\\w]+)))\\\" at ARGS:text1. ", 
            "data": "Matched Data: 1=1 found within ARGS:text1: 1=1", 
            "file": "rules\/REQUEST-942-APPLICATION-ATTACK-SQLI.conf\\\"", 
            "line": "554" 
        }, 
        "hostname": "vm000003", 
        "transactionId": "AcAcAcAcAKH@AcAcAcAcAyAt" 
    } 
} 
{ 
    "resourceId": "/SUBSCRIPTIONS/A6F44B25-259E-4AF5-888A-386FED92C11B/RESOURCEGROUPS/DEMOWAF_V2/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/DEMOWAF-V2", 
    "operationName": "ApplicationGatewayFirewall", 
    "category": "ApplicationGatewayFirewallLog", 
    "properties": { 
        "instanceId": "appgw_3", 
        "clientIp": "167.220.2.139", 
        "clientPort": "", 
        "requestUri": "\/", 
        "ruleSetType": "", 
        "ruleSetVersion": "", 
        "ruleId": "0", 
        "message": "Mandatory rule. Cannot be disabled. Inbound Anomaly Score Exceeded (Total Score: 8)", 
        "action": "Blocked", 
        "site": "Global", 
        "details": { 
            "message": "Access denied with code 403 (phase 2). Operator GE matched 5 at TX:anomaly_score. ", 
            "data": "", 
            "file": "rules\/REQUEST-949-BLOCKING-EVALUATION.conf\\\"", 
            "line": "57" 
        }, 
        "hostname": "vm000003", 
        "transactionId": "AcAcAcAcAKH@AcAcAcAcAyAt" 
    } 
} 
{ 
    "resourceId": "/SUBSCRIPTIONS/A6F44B25-259E-4AF5-888A-386FED92C11B/RESOURCEGROUPS/DEMOWAF_V2/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/DEMOWAF-V2", 
    "operationName": "ApplicationGatewayFirewall", 
    "category": "ApplicationGatewayFirewallLog", 
    "properties": { 
        "instanceId": "appgw_3", 
        "clientIp": "167.220.2.139", 
        "clientPort": "", 
        "requestUri": "\/", 
        "ruleSetType": "", 
        "ruleSetVersion": "", 
        "ruleId": "0", 
        "message": "Mandatory rule. Cannot be disabled. Inbound Anomaly Score Exceeded (Total Inbound Score: 8 - SQLI=5,XSS=0,RFI=0,LFI=0,RCE=0,PHPI=0,HTTP=0,SESS=0): SQL Injection Attack: SQL Tautology Detected.", 
        "action": "Blocked", 
        "site": "Global", 
        "details": { 
            "message": "Warning. Operator GE matched 5 at TX:inbound_anomaly_score. ", 
            "data": "", 
            "file": "rules\/RESPONSE-980-CORRELATION.conf\\\"", 
            "line": "73" 
        }, 
        "hostname": "vm000003", 
        "transactionId": "AcAcAcAcAKH@AcAcAcAcAyAt" 
    }
}
```

## <a name="fixing-false-positives"></a>거짓 긍정 수정

이 정보와 규칙 942130이 *1=1* 문자열과 일치하는 것이라는 지식으로 트래픽을 차단하는 몇 가지 작업을 수행할 수 있습니다.

- 제외 목록 사용

   제외 목록에 대한 자세한 내용은 [WAF 구성을](application-gateway-waf-configuration.md#waf-exclusion-lists) 참조하십시오.
- 규칙을 사용하지 않도록 설정합니다.

### <a name="using-an-exclusion-list"></a>제외 목록 사용

거짓 긍정 처리에 대해 정보에 입각한 결정을 내리려면 응용 프로그램에서 사용하는 기술을 숙지하는 것이 중요합니다. 예를 들어 기술 스택에 SQL 서버가 없고 이러한 규칙과 관련된 거짓 긍정이 있다고 가정해 보겠습니다. 이러한 규칙을 사용하지 않도록 설정해도 보안이 약화되는 것은 아닙니다.

제외 목록을 사용하면 요청의 특정 부분만 사용할 수 없게 된다는 이점이 있습니다. 그러나 이는 전역 설정이므로 WAF를 통과하는 모든 트래픽에 특정 제외가 적용된다는 것을 의미합니다. 예를 들어 *1=1이* 특정 앱에 대한 본문에서 유효한 요청이지만 다른 앱의 요청이 아닌 경우 문제가 발생할 수 있습니다. 또 다른 이점은 전체 요청을 제외하는 대신 특정 조건이 충족되는 경우 제외할 본문, 헤더 및 쿠키 중에서 선택할 수 있다는 것입니다.

경우에 따라 특정 매개 변수가 직관적이지 않은 방식으로 WAF에 전달되는 경우가 있습니다. 예를 들어 Azure Active Directory를 사용하여 인증할 때 전달되는 토큰이 있습니다. *__RequestVerificationToken*이 토큰은 일반적으로 요청 쿠키로 전달됩니다. 그러나 쿠키가 비활성화된 경우 이 토큰은 요청 특성 또는 "arg"로도 전달됩니다. 이 경우 *__RequestVerificationToken* **요청 특성 이름으로** 제외 목록에 추가되도록 해야 합니다.

![제외](../media/web-application-firewall-troubleshoot/exclusion-list.png)

이 예제에서는 *text1과*동일한 **Request 특성 이름을** 제외하려고 합니다. 이는 방화벽 로그에서 속성 이름을 볼 수 있기 때문에 명백합니다: **데이터: 일치하는 데이터: ARGS:text1: 1=1 에서 발견된 1=1**. 특성은 **text1**. 이 특성 이름은 요청 [속성 이름 찾기를](#finding-request-attribute-names)참조하는 몇 가지 다른 방법을 찾을 수도 있습니다.

![WAF 제외 목록](../media/web-application-firewall-troubleshoot/waf-config.png)

### <a name="disabling-rules"></a>규칙 사용 설정 사용 설정

거짓 긍정을 극복하는 또 다른 방법은 WAF가 악의적이라고 생각한 입력에 일치하는 규칙을 사용하지 않도록 설정하는 것입니다. WAF 로그를 구문 분석하고 규칙을 942130으로 좁혀 있으므로 Azure 포털에서 비활성화할 수 있습니다. [Azure 포털을 통해 웹 응용 프로그램 방화벽 규칙 사용자 지정을](application-gateway-customize-waf-rules-portal.md)참조하세요.

규칙을 사용하지 않도록 설정하는 한 가지 이점은 일반적으로 차단되는 특정 조건이 포함된 모든 트래픽이 유효한 트래픽임을 알고 있으면 전체 WAF에 대해 해당 규칙을 비활성화할 수 있다는 것입니다. 그러나 특정 사용 사례에서 유효한 트래픽인 경우 전역 설정이므로 전체 WAF에 대해 해당 규칙을 사용하지 않도록 설정하여 취약점을 엽니다.

Azure PowerShell을 사용하려면 PowerShell 을 [통해 웹 응용 프로그램 방화벽 규칙을 사용자 지정을](application-gateway-customize-waf-rules-powershell.md)참조하세요. Azure CLI를 사용하려면 [Azure CLI를 통해 웹 응용 프로그램 방화벽 규칙을 사용자](application-gateway-customize-waf-rules-cli.md)지정참조하세요.

![WAF 규칙](../media/web-application-firewall-troubleshoot/waf-rules.png)

## <a name="finding-request-attribute-names"></a>요청 특성 이름 찾기

[Fiddler의](https://www.telerik.com/fiddler)도움으로 개별 요청을 검사하고 웹 페이지의 특정 필드를 호출합니다. 이렇게 하면 제외 목록을 사용하여 검사에서 특정 필드를 제외하는 데 도움이 될 수 있습니다.

이 예제에서는 *1=1* 문자열이 입력된 필드를 **text1이라고**하는 것을 볼 수 있습니다.

![Fiddler](../media/web-application-firewall-troubleshoot/fiddler-1.png)

제외할 수 있는 필드입니다. 제외 목록에 대한 자세한 내용은 [웹 응용 프로그램 방화벽 요청 크기 제한 및 제외 목록을](application-gateway-waf-configuration.md#waf-exclusion-lists)참조하십시오. 이 경우 다음 제외를 구성하여 평가를 제외할 수 있습니다.

![WAF 제외](../media/web-application-firewall-troubleshoot/waf-exclusion-02.png)

또한 방화벽 로그를 검사하여 제외 목록에 추가해야 하는 정보를 확인할 수 있습니다. 로깅을 사용하려면 [응용 프로그램 게이트웨이에 대한 백 엔드 상태, 진단 로그 및 메트릭을](../../application-gateway/application-gateway-diagnostics.md)참조하십시오.

방화벽 로그를 검사하고 검사할 요청이 발생한 시간에 대한 PT1H.json 파일을 봅니다.

이 예제에서는 동일한 TransactionID를 가진 네 개의 규칙이 있고 모두 동일한 시간에 발생했는지 확인할 수 있습니다.

```json
-   {
-       "resourceId": "/SUBSCRIPTIONS/A6F44B25-259E-4AF5-888A-386FED92C11B/RESOURCEGROUPS/DEMOWAF_V2/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/DEMOWAF-V2",
-       "operationName": "ApplicationGatewayFirewall",
-       "category": "ApplicationGatewayFirewallLog",
-       "properties": {
-           "instanceId": "appgw_3",
-           "clientIp": "167.220.2.139",
-           "clientPort": "",
-           "requestUri": "\/",
-           "ruleSetType": "OWASP_CRS",
-           "ruleSetVersion": "3.0.0",
-           "ruleId": "920350",
-           "message": "Host header is a numeric IP address",
-           "action": "Matched",
-           "site": "Global",
-           "details": {
-               "message": "Warning. Pattern match \\\"^[\\\\\\\\d.:]+$\\\" at REQUEST_HEADERS:Host. ",
-               "data": "40.90.218.160",
-               "file": "rules\/REQUEST-920-PROTOCOL-ENFORCEMENT.conf\\\"",
-               "line": "791"
-           },
-           "hostname": "vm000003",
-           "transactionId": "AcAcAcAcAKH@AcAcAcAcAyAt"
-       }
-   }
-   {
-       "resourceId": "/SUBSCRIPTIONS/A6F44B25-259E-4AF5-888A-386FED92C11B/RESOURCEGROUPS/DEMOWAF_V2/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/DEMOWAF-V2",
-       "operationName": "ApplicationGatewayFirewall",
-       "category": "ApplicationGatewayFirewallLog",
-       "properties": {
-           "instanceId": "appgw_3",
-           "clientIp": "167.220.2.139",
-           "clientPort": "",
-           "requestUri": "\/",
-           "ruleSetType": "OWASP_CRS",
-           "ruleSetVersion": "3.0.0",
-           "ruleId": "942130",
-           "message": "SQL Injection Attack: SQL Tautology Detected.",
-           "action": "Matched",
-           "site": "Global",
-           "details": {
-               "message": "Warning. Pattern match \\\"(?i:([\\\\\\\\s'\\\\\\\"`\\\\\\\\(\\\\\\\\)]*?)([\\\\\\\\d\\\\\\\\w]++)([\\\\\\\\s'\\\\\\\"`\\\\\\\\(\\\\\\\\)]*?)(?:(?:=|\\u003c=\\u003e|r?like|sounds\\\\\\\\s+like|regexp)([\\\\\\\\s'\\\\\\\"`\\\\\\\\(\\\\\\\\)]*?)\\\\\\\\2|(?:!=|\\u003c=|\\u003e=|\\u003c\\u003e|\\u003c|\\u003e|\\\\\\\\^|is\\\\\\\\s+not|not\\\\\\\\s+like|not\\\\\\\\s+regexp)([\\\\\\\\s'\\\\\\\"`\\\\\\\\(\\\\\\\\)]*?)(?!\\\\\\\\2)([\\\\\\\\d\\\\\\\\w]+)))\\\" at ARGS:text1. ",
-               "data": "Matched Data: 1=1 found within ARGS:text1: 1=1",
-               "file": "rules\/REQUEST-942-APPLICATION-ATTACK-SQLI.conf\\\"",
-               "line": "554"
-           },
-           "hostname": "vm000003",
-           "transactionId": "AcAcAcAcAKH@AcAcAcAcAyAt"
-       }
-   }
-   {
-       "resourceId": "/SUBSCRIPTIONS/A6F44B25-259E-4AF5-888A-386FED92C11B/RESOURCEGROUPS/DEMOWAF_V2/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/DEMOWAF-V2",
-       "operationName": "ApplicationGatewayFirewall",
-       "category": "ApplicationGatewayFirewallLog",
-       "properties": {
-           "instanceId": "appgw_3",
-           "clientIp": "167.220.2.139",
-           "clientPort": "",
-           "requestUri": "\/",
-           "ruleSetType": "",
-           "ruleSetVersion": "",
-           "ruleId": "0",
-           "message": "Mandatory rule. Cannot be disabled. Inbound Anomaly Score Exceeded (Total Score: 8)",
-           "action": "Blocked",
-           "site": "Global",
-           "details": {
-               "message": "Access denied with code 403 (phase 2). Operator GE matched 5 at TX:anomaly_score. ",
-               "data": "",
-               "file": "rules\/REQUEST-949-BLOCKING-EVALUATION.conf\\\"",
-               "line": "57"
-           },
-           "hostname": "vm000003",
-           "transactionId": "AcAcAcAcAKH@AcAcAcAcAyAt"
-       }
-   }
-   {
-       "resourceId": "/SUBSCRIPTIONS/A6F44B25-259E-4AF5-888A-386FED92C11B/RESOURCEGROUPS/DEMOWAF_V2/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/DEMOWAF-V2",
-       "operationName": "ApplicationGatewayFirewall",
-       "category": "ApplicationGatewayFirewallLog",
-       "properties": {
-           "instanceId": "appgw_3",
-           "clientIp": "167.220.2.139",
-           "clientPort": "",
-           "requestUri": "\/",
-           "ruleSetType": "",
-           "ruleSetVersion": "",
-           "ruleId": "0",
-           "message": "Mandatory rule. Cannot be disabled. Inbound Anomaly Score Exceeded (Total Inbound Score: 8 - SQLI=5,XSS=0,RFI=0,LFI=0,RCE=0,PHPI=0,HTTP=0,SESS=0): SQL Injection Attack: SQL Tautology Detected.",
-           "action": "Blocked",
-           "site": "Global",
-           "details": {
-               "message": "Warning. Operator GE matched 5 at TX:inbound_anomaly_score. ",
-               "data": "",
-               "file": "rules\/RESPONSE-980-CORRELATION.conf\\\"",
-               "line": "73"
-           },
-           "hostname": "vm000003",
-           "transactionId": "AcAcAcAcAKH@AcAcAcAcAyAt"
-       }
-   }
```

CRS 규칙 세트의 작동 방식과 CRS 규칙 집합 3.0이 변칙 점수 매기기 시스템에서 [작동하는지(Azure 응용 프로그램 게이트웨이용 웹 응용 프로그램 방화벽](ag-overview.md)참조) 작업중인 아래두 **규칙인 차단된** 속성이 전체 변칙 점수를 기반으로 차단된다는 것을 알 수 있습니다. 집중해야 할 규칙은 상위 2개입니다.

첫 번째 항목은 사용자가 숫자 IP 주소를 사용하여 응용 프로그램 게이트웨이로 이동했기 때문에 기록되며 이 경우 무시할 수 있습니다.

두 번째 (규칙 942130)는 흥미로운 것입니다. 세부 정보에서 패턴(1=1)과 일치하고 필드의 이름이 **text1로**지정되어 있음을 볼 수 있습니다. 동일한 이전 단계를 수행하여 **1=1과** **동일한** **요청 특성 이름을** 제외합니다.

## <a name="finding-request-header-names"></a>요청 헤더 이름 찾기

Fiddler는 요청 헤더 이름을 다시 한 번 찾는 유용한 도구입니다. 다음 스크린샷에서는 *콘텐츠 유형,* *사용자 에이전트*등을 포함하는 이 GET 요청에 대한 헤더를 볼 수 있습니다.

![Fiddler](../media/web-application-firewall-troubleshoot/fiddler-2.png)

요청 및 응답 헤더를 보는 또 다른 방법은 Chrome의 개발자 도구 내부를 살펴보는 것입니다. F12를 누르거나 개발자**도구** **검사에** -> > 마우스 오른쪽 단추로 클릭하고 **네트워크** 탭을 선택할 수 있습니다.

![크롬 F12](../media/web-application-firewall-troubleshoot/chrome-f12.png)

## <a name="finding-request-cookie-names"></a>요청 쿠키 이름 찾기

요청에 쿠키가 포함되어 있는 경우 **쿠키** 탭을 선택하여 Fiddler에서 볼 수 있습니다.

## <a name="restrict-global-parameters-to-eliminate-false-positives"></a>전역 매개 변수를 제한하여 거짓 긍정 제거

- 요청 본문 검사 비활성화

   검사 **요청 본문을** 끄기로 설정하면 모든 트래픽의 요청 본문이 WAF에서 평가되지 않습니다. 이 기능은 요청 본문의 응용 프로그램에 악성이 아니라는 것을 알고 있는 경우에 유용할 수 있습니다.

   이 옵션을 사용하지 않도록 설정하면 요청 본문만 검사되지 않습니다. 헤더와 쿠키는 제외 목록 기능을 사용하여 개별 헤더를 제외하지 않는 한 검사된 상태로 유지됩니다.

- 파일 크기 제한

   WAF의 파일 크기를 제한하면 웹 서버에 대한 공격 가능성을 제한할 수 있습니다. 대용량 파일을 업로드할 수 있으므로 백 엔드가 과부하될 위험이 커지게 됩니다. 파일 크기를 응용 프로그램의 일반 사용 사례로 제한하는 것은 공격을 방지하는 또 다른 방법입니다.

   > [!NOTE]
   > 앱에서 지정된 크기 이상의 파일 업로드가 필요하지 않은 경우 제한을 설정하여 이를 제한할 수 있습니다.

## <a name="firewall-metrics-waf_v1-only"></a>방화벽 메트릭(WAF_v1 만)

v1 웹 응용 프로그램 방화벽의 경우 포털에서 다음 메트릭을 사용할 수 있습니다. 

1. 웹 응용 프로그램 방화벽 차단 된 요청 수 차단 된 요청의 수를 계산 합니다.
2. 웹 응용 프로그램 방화벽 차단 규칙 일치 **하 고** 요청이 차단 된 모든 규칙을 계산
3. 웹 응용 프로그램 방화벽 총 규칙 배포 평가 중에 일치 된 모든 규칙
     
메트릭을 사용하려면 포털에서 **메트릭** 탭을 선택하고 세 가지 측정항목 중 하나를 선택합니다.

## <a name="next-steps"></a>다음 단계

[응용 프로그램 게이트웨이에서 웹 응용 프로그램 방화벽을 구성하는 방법을](tutorial-restrict-web-traffic-powershell.md)참조하십시오.
