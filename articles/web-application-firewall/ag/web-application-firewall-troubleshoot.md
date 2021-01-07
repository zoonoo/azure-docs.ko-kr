---
title: 문제 해결-Azure 웹 응용 프로그램 방화벽
description: 이 문서에서는 Azure 애플리케이션 게이트웨이의 WAF (웹 응용 프로그램 방화벽)에 대 한 문제 해결 정보를 제공 합니다.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.date: 11/14/2019
ms.author: ant
ms.topic: conceptual
ms.openlocfilehash: 483d261a8cc107d01cfb7a405eac43667d7efcc6
ms.sourcegitcommit: 33368ca1684106cb0e215e3280b828b54f7e73e8
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/16/2020
ms.locfileid: "92131839"
---
# <a name="troubleshoot-web-application-firewall-waf-for-azure-application-gateway"></a>Azure 애플리케이션 Gateway에 대 한 WAF (웹 응용 프로그램 방화벽) 문제 해결

WAF (웹 응용 프로그램 방화벽)를 통과 해야 하는 요청이 차단 된 경우 몇 가지 작업을 수행할 수 있습니다.

먼저 [waf 개요](ag-overview.md) 및 [waf 구성](application-gateway-waf-configuration.md) 문서를 읽어야 합니다. 또한 [waf 모니터링](../../application-gateway/application-gateway-diagnostics.md) 을 사용 하도록 설정 했는지 확인 합니다 .이 문서에서는 waf 함수, waf 규칙 집합이 작동 하는 방식 및 waf 로그에 액세스 하는 방법을 설명 합니다.

OWASP 규칙 집합은 매우 엄격 하 게 사용할 수 있도록 설계 되었으며 WAF를 사용 하는 응용 프로그램 또는 조직의 특정 요구에 맞게 조정 됩니다. 이는 전적으로 대부분의 경우에서 제외, 사용자 지정 규칙을 만들기 위해 발생 하거나 문제 또는 가양성을 일으킬 수 있는 규칙을 사용 하지 않도록 설정 하는 데 필요 합니다. 사이트별 및 URI 별 정책을 사용 하면 이러한 변경 내용이 특정 사이트/u r i에만 영향을 줄 수 있으므로 동일한 문제로 실행 되지 않는 다른 사이트에는 변경 내용이 영향을 주지 않아야 합니다. 

## <a name="understanding-waf-logs"></a>WAF 로그 이해

WAF 로그의 목적은 WAF에 의해 일치 되거나 차단 되는 모든 요청을 표시 하는 것입니다. 일치 하거나 차단 된 모든 평가 된 요청에 대 한 원장입니다. WAF가 요청 (거짓 긍정)을 차단 하지 않도록 차단 하는 경우 몇 가지 작업을 수행할 수 있습니다. 먼저, 범위를 좁혀 특정 요청을 찾습니다. 로그를 확인 하 여 요청의 특정 URI, 타임 스탬프 또는 트랜잭션 ID를 찾습니다. 연결 된 로그 항목을 찾으면 가양성에 대 한 작업을 시작할 수 있습니다.

예를 들어 WAF를 통과 하려는 문자열 *1 = 1* 을 포함 하는 합법적인 트래픽이 있다고 가정 합니다. 요청을 시도 하면 WAF는 매개 변수 또는 필드에 *1 = 1* 문자열을 포함 하는 트래픽을 차단 합니다. 이는 종종 SQL 삽입 공격과 관련 된 문자열입니다. 로그를 확인 하 고 요청의 타임 스탬프와 차단/일치 된 규칙을 확인할 수 있습니다.

다음 예제에서는 TransactionId 필드를 사용 하 여 동일한 요청 중에 4 개의 규칙이 트리거되는 것을 볼 수 있습니다. 처음에는 사용자가 요청에 대 한 숫자/i p URL을 사용 했기 때문에 일치 하는 것으로 나타납니다. 일치 한 다음 규칙은 942130입니다 .이 규칙은 찾고 있는 규칙입니다. 필드에서 *1 = 1* 을 확인할 수 있습니다 `details.data` . 그러면 경고가 발생 하므로 이상 점수 점수가 3 씩 늘어납니다. 일반적으로 동작이 **일치** 하는 모든 규칙은 이상 점수를 늘리고이 시점에서 변칙 점수는 6입니다. 자세한 내용은 [변칙 점수 매기기 모드](ag-overview.md#anomaly-scoring-mode)를 참조 하세요.

마지막 두 개의 로그 항목은 변칙 점수가 충분히 크기 때문에 요청이 차단 되었음을 보여 줍니다. 이러한 항목은 다른 두 항목의 동작과 다릅니다. 실제로 요청이 *차단* 된 것을 보여 줍니다. 이러한 규칙은 필수 이며 사용 하지 않도록 설정할 수 없습니다. 규칙으로 간주 되는 것은 아니지만 WAF 내부의 핵심 인프라로 간주 하면 됩니다.

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

## <a name="fixing-false-positives"></a>가양성 수정

이 정보와 규칙 942130이 *1 = 1* 문자열과 일치 하는 정보를 사용 하 여이를 방지 하기 위해 몇 가지 작업을 수행할 수 있습니다.

- 제외 목록 사용

   제외 목록에 대 한 자세한 내용은 [Waf 구성](application-gateway-waf-configuration.md#waf-exclusion-lists) 을 참조 하세요.
- 규칙을 사용 하지 않도록 설정 합니다.

### <a name="using-an-exclusion-list"></a>제외 목록 사용

가양성을 처리 하는 방법에 대 한 의사 결정을 내리는 것은 응용 프로그램에서 사용 하는 기술에 대해 잘 알고 있어야 합니다. 예를 들어, 기술 스택에 SQL server가 없고 해당 규칙과 관련 된 가양성을 받고 있다고 가정해 보겠습니다. 이러한 규칙을 사용 하지 않도록 설정 하면 보안이 약화 되지 않을 수도 있습니다.

제외 목록을 사용할 경우의 한 가지 장점 중 하나는 요청의 특정 부분만 사용 하지 않도록 설정 된다는 것입니다. 그러나이는 전역 설정 이므로 WAF를 통해 전달 되는 모든 트래픽에 특정 제외가 적용 될 수 있음을 의미 합니다. 예를 들어 *1 = 1* 은 특정 앱에 대 한 본문의 유효한 요청 이지만 다른 항목에 대해서는 그렇지 않은 경우 문제가 발생할 수 있습니다. 또 다른 혜택은 전체 요청을 제외 하는 것과는 반대로 특정 조건이 충족 될 경우 본문, 헤더 및 제외할 쿠키 중에서 선택할 수 있는 것입니다.

때로는 직관적이 지 않을 수 있는 방식으로 특정 매개 변수가 WAF에 전달 되는 경우가 있습니다. 예를 들어 Azure Active Directory를 사용 하 여 인증할 때 전달 되는 토큰이 있습니다. 이 토큰 *__RequestVerificationToken*은 일반적으로 요청 쿠키로 전달 됩니다. 그러나 쿠키를 사용 하지 않도록 설정 하는 경우이 토큰은 요청 특성 또는 "arg"로도 전달 됩니다. 이 경우 *__RequestVerificationToken* 를 제외 목록에 **요청 특성 이름** 으로도 추가 해야 합니다.

![제외](../media/web-application-firewall-troubleshoot/exclusion-list.png)

이 예제에서는 *text1*과 같은 **요청 특성 이름을** 제외 하려고 합니다. 이는 방화벽 로그에서 특성 이름을 볼 수 있기 때문에 분명 합니다. **데이터: 일치 하는 데이터: 1 = 1은 ARGS: text1:1 =** 1입니다. 특성이 **text1**입니다. 다른 몇 가지 방법으로이 특성 이름을 찾을 수도 있습니다. [요청 특성 이름 찾기](#finding-request-attribute-names)를 참조 하세요.

![WAF 제외 목록](../media/web-application-firewall-troubleshoot/waf-config.png)

### <a name="disabling-rules"></a>규칙 사용 안 함

가양성을 해결 하는 또 다른 방법은 WAF가 악의적 이라고 생각 하는 입력에 일치 하는 규칙을 비활성화 하는 것입니다. WAF 로그를 구문 분석 하 고 규칙을 942130으로 축소 했으므로 Azure Portal에서 사용 하지 않도록 설정할 수 있습니다. [Azure Portal를 통해 웹 응용 프로그램 방화벽 규칙 사용자 지정을](application-gateway-customize-waf-rules-portal.md)참조 하세요.

규칙을 사용 하지 않도록 설정 하는 방법 중 하나는 일반적으로 차단 되는 특정 조건을 포함 하는 모든 트래픽이 유효한 트래픽 이라는 것을 알고 있으면 전체 WAF에 대해 해당 규칙을 사용 하지 않도록 설정할 수 있다는 것입니다. 그러나 특정 사용 사례의 유효한 트래픽만 있으면 전체 WAF에 대해 전역 설정 이므로 해당 규칙을 사용 하지 않도록 설정 하 여 취약성을 엽니다.

Azure PowerShell를 사용 하려면 [PowerShell을 통해 웹 응용 프로그램 방화벽 규칙 사용자 지정](application-gateway-customize-waf-rules-powershell.md)을 참조 하세요. Azure CLI를 사용 하려면 [Azure CLI를 통해 웹 응용 프로그램 방화벽 규칙 사용자 지정](application-gateway-customize-waf-rules-cli.md)을 참조 하세요.

![WAF 규칙](../media/web-application-firewall-troubleshoot/waf-rules.png)

## <a name="finding-request-attribute-names"></a>요청 특성 이름 찾기

[Fiddler](https://www.telerik.com/fiddler)의 도움으로 개별 요청을 검사 하 고 웹 페이지의 특정 필드를 확인 합니다. 이를 통해 제외 목록을 사용 하 여 특정 필드를 검사에서 제외할 수 있습니다.

이 예제에서는 *1 = 1* 문자열을 입력 한 필드가 **text1**이라고 표시 되는 것을 볼 수 있습니다.

:::image type="content" source="../media/web-application-firewall-troubleshoot/fiddler-1.png" alt-text="진행률 Telerik Fiddler 웹 디버거의 스크린샷 Raw 탭에서 1 = 1은 이름 text1 뒤에 표시 됩니다." border="false":::

제외할 수 있는 필드입니다. 제외 목록에 대해 자세히 알아보려면 [웹 응용 프로그램 방화벽 요청 크기 제한 및 제외 목록](application-gateway-waf-configuration.md#waf-exclusion-lists)을 참조 하세요. 이 경우 다음 제외를 구성 하 여 평가를 제외할 수 있습니다.

![WAF 제외](../media/web-application-firewall-troubleshoot/waf-exclusion-02.png)

방화벽 로그를 검토 하 여 제외 목록에 추가 해야 하는 항목을 확인 하는 정보를 얻을 수도 있습니다. 로깅을 사용 하도록 설정 하려면 [백 엔드 상태, 리소스 로그 및 Application Gateway에 대 한 메트릭](../../application-gateway/application-gateway-diagnostics.md)을 참조 하세요.

방화벽 로그를 검토 하 고 검사 하려는 요청이 발생 한 시간에 대 한 PT1H.js파일을 봅니다.

이 예제에서는 동일한 TransactionID를 사용 하는 네 개의 규칙이 있으며 모두 정확히 동일한 시간에 발생 했음을 확인할 수 있습니다.

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

CRS 규칙 집합이 작동 하는 방식에 대 한 정보를 알고 있으며, CRS 규칙 집합 3.0이 변칙 점수 매기기 시스템에서 작동 하는 경우 ( [Azure 애플리케이션 게이트웨이의 웹 응용 프로그램 방화벽](ag-overview.md)참조) **작업: 차단** 된 속성의 하위 두 규칙이 총 이상 점수에 따라 차단 됨 가장 중요 한 규칙은 위의 두 가지입니다.

사용자가 숫자 IP 주소를 사용 하 여 Application Gateway로 이동 했기 때문에 첫 번째 항목이 기록 됩니다 .이 경우에는 무시 해도 됩니다.

두 번째 (규칙 942130)는 흥미로운 항목입니다. 패턴 (1 = 1)과 일치 하는 세부 정보에서 볼 수 있으며 필드의 이름은 **text1**로 지정 됩니다. 동일한 이전 단계에 따라 **1 = 1** **과** 같은 **요청 특성 이름을** 제외 합니다.

## <a name="finding-request-header-names"></a>요청 헤더 이름 찾기

Fiddler는 요청 헤더 이름을 찾을 때 유용한 도구입니다. 다음 스크린샷에서는이 GET 요청에 대 한 헤더를 볼 수 있습니다. 여기에는 *content-type*, *사용자 에이전트*등이 포함 됩니다.

:::image type="content" source="../media/web-application-firewall-troubleshoot/fiddler-2.png" alt-text="진행률 Telerik Fiddler 웹 디버거의 스크린샷 Raw 탭에는 연결, content-type 및 사용자 에이전트와 같은 요청 헤더 정보가 나열 됩니다." border="false":::

요청 및 응답 헤더를 보는 또 다른 방법은 Chrome의 개발자 도구 내에서 확인 하는 것입니다. F12 키를 누르거나 마우스 오른쪽 단추를 클릭 하 **Inspect**>  ->  **개발자 도구**검사 하 고 **네트워크** 탭을 선택할 수 있습니다. 웹 페이지를 로드 하 고 검사 하려는 요청을 클릭 합니다.

![Chrome F12](../media/web-application-firewall-troubleshoot/chrome-f12.png)

## <a name="finding-request-cookie-names"></a>요청 쿠키 이름 찾기

요청이 쿠키를 포함 하는 경우 **쿠키 탭을 선택 하 여 Fiddler** 에서 볼 수 있습니다.

## <a name="restrict-global-parameters-to-eliminate-false-positives"></a>가양성을 제거 하도록 전역 매개 변수 제한

- 요청 본문 검사 사용 안 함

   **요청 본문 검사** 를 off로 설정 하면 모든 트래픽의 요청 본문이 waf에 의해 평가 되지 않습니다. 이는 요청 본문이 응용 프로그램에 악성이 아닌 것을 알고 있는 경우에 유용할 수 있습니다.

   이 옵션을 사용 하지 않도록 설정 하면 요청 본문만 검사 되지 않습니다. 제외 목록 기능을 사용 하 여 개별 항목을 제외 하지 않는 한 헤더와 쿠키는 검사 된 상태로 유지 됩니다.

- 파일 크기 제한

   WAF의 파일 크기를 제한 하 여 웹 서버에 대 한 공격 발생 가능성을 제한 합니다. 대량 파일을 업로드할 수 있도록 하 여 백 엔드의 위험이 높아집니다. 응용 프로그램에 대 한 일반적인 사용 사례로 파일 크기를 제한 하는 것은 공격을 방지 하는 또 다른 방법입니다.

   > [!NOTE]
   > 앱이 지정 된 크기를 초과 하 여 파일을 업로드 하지 않아도 되는 경우 제한을 설정 하 여 제한할 수 있습니다.

## <a name="firewall-metrics-waf_v1-only"></a>방화벽 메트릭 (WAF_v1에만 해당)

V1 웹 응용 프로그램 방화벽의 경우 이제 포털에서 다음 메트릭을 사용할 수 있습니다. 

1. 웹 응용 프로그램 방화벽 차단 된 요청 수 차단 된 요청 수
2. 웹 응용 프로그램 방화벽에서 차단 된 규칙 수 일치 된 모든 규칙 **및** 요청이 차단 됨
3. 웹 응용 프로그램 방화벽 총 규칙 배포 평가 중 일치 된 모든 규칙
     
메트릭을 사용 하도록 설정 하려면 포털에서 **메트릭** 탭을 선택 하 고 세 메트릭 중 하나를 선택 합니다.

## <a name="next-steps"></a>다음 단계

[Application Gateway에서 웹 응용 프로그램 방화벽을 구성 하는 방법을](tutorial-restrict-web-traffic-powershell.md)참조 하세요.
