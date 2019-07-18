---
title: Azure Application Gateway에 대 한 웹 응용 프로그램 방화벽 문제 해결
description: 이 문서에서는 Azure Application Gateway에 대 한 문제 해결 정보에 대 한 응용 프로그램 방화벽 (WAF (웹)
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.date: 6/11/2019
ms.author: ant
ms.topic: conceptual
ms.openlocfilehash: b81ffc5769fa5521094734da5d00aab77aa7cd35
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67082444"
---
# <a name="troubleshoot-web-application-firewall-waf-for-azure-application-gateway"></a>Azure Application Gateway에 대 한 웹 응용 프로그램 방화벽 (WAF) 문제 해결

몇 가지 방법으로 웹 응용 프로그램 방화벽 (WAF)를 통해 전달 해야 하는 요청 차단 되 면 수행할 수 있습니다.

먼저 읽어 보셨다면를 확인 합니다 [WAF 개요](waf-overview.md) 하며 [WAF 구성](application-gateway-waf-configuration.md) 문서. 또한를 사용 하도록 설정 해야 [WAF 모니터링](application-gateway-diagnostics.md) 이 문서에서는 작동 하는 방법을 WAF, WAF 규칙 작업을 설정 하는 방법을 설명 하 고 WAF 로그를 액세스 하는 방법입니다.

## <a name="understanding-waf-logs"></a>이해 WAF 로그

WAF 로그의 목적은 WAF에 의해 차단 되거나 일치 하는 모든 요청을 보여 줍니다. 일치 되거나 차단 되는 모든 계산 요청의 원장 것입니다. WAF (거짓 긍정) 하지 않아야 하는 요청을 차단 하는 것이 표시를 하는 경우에 몇 가지를 수행할 수 있습니다. 먼저, 좁힐 하 고 특정 요청을 찾을. 요청의 특정 URI, 타임 스탬프 또는 트랜잭션 ID를 찾으려면 로그를 확인 합니다. 관련 된 로그 항목을 찾으면 거짓 긍정을 작업할 수 있습니다.

예를 들어 있는 문자열을 포함 하는 정당한 트래픽을 *1 = 1* WAF를 통해 전달 하려는 합니다. WAF를 포함 하는 트래픽을 차단 하면 요청을 하 *1 = 1* 모든 매개 변수 또는 필드에는 문자열입니다. SQL 삽입 공격에 자주 연결 하는 문자열입니다. 로그를 확인 하 고 요청 및 차단 하 고 일치 하는 규칙의 타임 스탬프를 볼 수 있습니다.

다음 예제에서는 네 가지 규칙 (트랜잭션 Id 필드를 사용 하 여) 같은 요청 하는 동안 트리거한 볼 수 있습니다. 첫 번째 사용자 숫자/IP를 사용 하기 때문에 일치 하는 것을 알리는 경고 이므로 변칙 점수를 3으로 증가 하는 요청에 대 한 URL입니다. 일치 하는 다음 규칙은 942130를 찾는 것입니다. 볼 수 있습니다 합니다 *1 = 1* 에 `details.data` 필드입니다. 이 추가 증가 변칙 점수를 3으로 다시 경고 이기도 합니다. 일반적으로 모든 규칙 작업 **Matched** 증가 변칙 점수를 매기고,이 시점에서 변칙 점수를 사용 하는 것이 6입니다. 자세한 내용은 [변칙 점수 매기기 모드](waf-overview.md#anomaly-scoring-mode)합니다.

마지막 두 개의 로그 항목 표시 변칙 점수를 충분히 높은 되었으므로 요청이 차단 되었습니다. 이러한 항목에는 다른 두 보다 다양 한 작업이 있습니다. 실제로 보여 줍니다 *차단* 요청 합니다. 이러한 규칙은 필수 이며 비활성화할 수 없습니다. 이러한 WAF 내부의 핵심 인프라 아니라 더 규칙으로 생각할 해서는 안 됩니다.

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

## <a name="fixing-false-positives"></a>거짓 긍정을 수정

이 내용은와 일치 하는 것 942130 규칙 기술 자료를 *1 = 1* 문자열에서 트래픽을 차단 합니다.이 중지 하려면 몇 가지 작업을 수행할 수 있습니다.

- 제외 목록을 사용 하 여

   참조 [WAF 구성](application-gateway-waf-configuration.md#waf-exclusion-lists) 제외에 대 한 자세한 정보를 나열 합니다.
- 규칙을 사용 하지 않도록 설정 합니다.

### <a name="using-an-exclusion-list"></a>제외 목록을 사용 하 여

양수는 false를 처리 하는 방법에 대 한 합리적된 결정을 하려면 응용 프로그램에서 사용 하는 기술을 익히는 것입니다. 예를 들어 SQL server 기술 스택을, 없습니다 및 이러한 규칙과 관련 된 거짓 긍정을 얻게 됩니다. 이러한 규칙을 사용 하지 않도록 설정 하는 보안을 약화 반드시 하지 않습니다.

제외 목록을 사용 하 여의 이점 중 하나는 요청의 특정 파트만 해제 됩니다. 그러나이 전역 설정 이므로 WAF를 통과 하는 모든 트래픽에 적용 된 특정 제외 항목을 사용 하 여 임을 의미 합니다. 예를 들어,이 경우 초래할 수 문제일 *1 = 1* 수 있지만 특정 앱의 경우 본문에 유효한 요청입니다. 또 다른 이점은 전체 요청을 제외 하는 대신 특정 조건이 충족 될 경우 제외할 본문, 헤더 및 쿠키 간에 선택할 수 있는 것입니다.

경우에 따라 직관적인 수 없는 방식으로 WAF에 특정 매개 변수 전달 되는 경우가 있습니다. 예를 들어, Azure Active Directory를 사용 하 여 인증할 때 전달 되는 토큰이입니다. 이 토큰이 *__RequestVerificationToken*, 일반적으로 요청 쿠키로 전달 가져오기. 쿠키 사용 하지 않는 있는 일부 경우에이 토큰도 요청 특성 또는 "인수"로 전달 됩니다. 있는지 확인 해야 하는 일이 발생 하면 *__RequestVerificationToken* 제외 목록에 추가 되는 **요청 특성 이름** 도 합니다.

![제외](media/waf-tshoot/exclusion-list.png)

이 예에서 제외 하려는 합니다 **요청 특성 이름** 는 equals *text1*합니다. 방화벽 로그의 특성 이름을 볼 수 있으므로이 점이 명확해: **데이터: 일치 하는 데이터: 1 = 1 ARGS:text1 내에서 찾을 수 있습니다. 1=1**. 특성이 **text1**합니다. 다른 몇 가지 특성 이름에이 찾을 수도 있습니다 방식으로 참조 [요청 특성 이름을 찾는](#finding-request-attribute-names)합니다.

![WAF 제외 목록](media/waf-tshoot/waf-config.png)

### <a name="disabling-rules"></a>규칙을 사용 하지 않도록 설정

거짓 긍정 걸림돌로 또 다른 방법은 입력 WAF 생각에서 일치 하는 규칙을 사용 하지 않도록 설정 된 악의적인 것입니다. WAF 로그를 구문 분석 했으면 하 942130까지 규칙 좁혀 졌습니다 때문에 Azure portal에서 비활성화할 수 있습니다. 참조 [Azure portal 통해 웹 응용 프로그램 방화벽 규칙 사용자 지정](application-gateway-customize-waf-rules-portal.md)합니다.

규칙을 사용 하지 않도록 설정의 이점 중 하나 이며 일반적으로 차단할 특정 조건이 포함 된 모든 트래픽을 이것이 유효한 트래픽을 알고 있는 경우 전체 WAF에 대 한 규칙을 비활성화할 수 있는 그러나이 경우만 특정 사용 사례에서 유효한 트래픽 열면 취약점을 전역 설정 이므로 전체 WAF에 대 한 규칙을 비활성화 하 여 합니다.

Azure PowerShell을 사용 하려는 경우 참조 [PowerShell 통해 웹 응용 프로그램 방화벽 규칙 사용자 지정](application-gateway-customize-waf-rules-powershell.md)합니다. Azure CLI를 사용 하려는 경우 참조 [Azure CLI를 통해 웹 응용 프로그램 방화벽 규칙 사용자 지정](application-gateway-customize-waf-rules-cli.md)합니다.

![WAF 규칙](media/waf-tshoot/waf-rules.png)

## <a name="finding-request-attribute-names"></a>찾기 요청 특성 이름

사용 하 여 [Fiddler](https://www.telerik.com/fiddler), 개별 요청을 검사 하 고 웹 페이지의 특정 필드 라고 결정 합니다. 이 특정 필드 제외 목록을 사용 하는 검사에서 제외할 수 있습니다.

이 예제에서는 함을 알 수 있습니다 필드 위치는 *1 = 1* 문자열을 입력 이라고 **text1**합니다.

![Fiddler](media/waf-tshoot/fiddler-1.png)

이 필드를 제외할 수 있습니다. 제외 목록에 대 한 자세한 내용은 참조 하세요 [웹 응용 프로그램 방화벽 요청 크기 제한 및 배제 목록](application-gateway-waf-configuration.md#waf-exclusion-lists)합니다. 다음 제외를 구성 하 여이 경우 평가 제외할 수 있습니다.

![WAF 제외](media/waf-tshoot/waf-exclusion-02.png)

또한 방화벽 로그 정보를 제외 목록에 추가 해야 하는 새로운 참조를 확인할 수 있습니다. 로깅을 사용 하려면 참조 [백 엔드 상태, 진단 로그 및 Application Gateway에 대 한 메트릭을](application-gateway-diagnostics.md)합니다.

방화벽 로그를 검토 하 고 검사 하려는 요청이 발생 한 시간 동안 PT1H.json 파일을 봅니다.

이 예제에서는 동일한 트랜잭션 Id 사용 하 여 네 개의 규칙이 있고 정확히 한 번에 모두 발생 했음을 확인할 수 있습니다.

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

CRS 규칙 하는 방법에 대 한 지식을 사용 하 여 작업을 설정 하 고 시스템을 점수 매기기 이상을 사용 하는 CRS 3.0 규칙 집합 (참조 [Azure Application Gateway에 대 한 웹 응용 프로그램 방화벽](waf-overview.md)) 아래쪽 두 규칙이 있는지 알고는  **작업: 차단** 속성을 차단 하는 총 변칙 점수를 기준으로 합니다. 중점적으로 규칙은 맨 위에 있는 두.

사용자는 숫자 IP 주소를이 예제의 무시 될 수 있는 Application Gateway로 이동 하는 데 사용 하기 때문에 첫 번째 항목이 기록 됩니다.

두 번째 흥미로운 것 하나 (규칙 942130). 세부 정보에서 볼 수는 (1 = 1), 패턴 일치 및 필드 이름은 **text1**합니다. 제외할 동일한 이전 단계를 수행 합니다 **요청 특성 이름** 하는 **같음** **1 = 1**합니다.

## <a name="finding-request-header-names"></a>찾기 요청 헤더 이름

Fiddler는 다시 한 번 요청 헤더 이름을 찾는 데 유용한 도구입니다. 다음 스크린샷에서이 GET 요청에 대 한 포함 하는 헤더를 볼 수 있습니다 *Content-type*를 *User-agent*등입니다.

![Fiddler](media/waf-tshoot/fiddler-2.png)

요청 및 응답 헤더를 확인 하는 다른 방법은 크롬 개발자 도구 내에서 검색할 것입니다. F12 키를 눌러 수 있습니다 또는-> 마우스 오른쪽 단추로 클릭 **검사** -> **개발자 도구**를 선택 합니다 **네트워크** 탭 합니다. 웹 페이지를 로드 하 고 검사 하려는 요청을 클릭 합니다.

![Chrome F12](media/waf-overview/chrome-f12.png)

## <a name="finding-request-cookie-names"></a>찾기 요청 쿠키 이름

요청 쿠키를 포함 하는 경우는 **쿠키** Fiddler에서 보려는 탭을 선택할 수 있습니다.

## <a name="restrict-global-parameters-to-eliminate-false-positives"></a>거짓 긍정을 제거 하기 위해 전역 매개 변수를 제한 합니다.

- 요청 본문 검사를 사용 하지 않도록 설정

   설정 하 여 **검사 요청 본문** 해제 되 고 요청 본문의 모든 트래픽이 계산 하지 것입니다. WAF에서. 이 요청 본문 되지 악성 응용 프로그램에 알고 있는 경우에 유용할 수 있습니다.

   이 옵션을 비활성화 하지 요청 본문에만 검사 됩니다. 제외 목록 기능을 사용 하 여 개별 항목은 제외 되지 않은 헤더 및 쿠키 검사를 유지 합니다.

- 파일 크기 제한

   WAF에 대 한 파일 크기를 제한 하 여 웹 서버에서 수행 하는 공격 가능성을 제한 하는 있습니다. 큰 파일을 업로드 하도록 함으로써 과부하가 백 엔드의 위험이 증가 합니다. 응용 프로그램에 대 한 일반적인 사용 사례에 파일 크기를 제한 하는 것은 공격을 방지 하는 또 다른 방법입니다.

   > [!NOTE]
   > 앱은 모든 파일 업로드 필요는 알고 있는 경우 지정 된 크기를 초과 제한할 수 있습니다 하는 제한을 설정 합니다.

## <a name="next-steps"></a>다음 단계

참조 [Application Gateway에서 웹 응용 프로그램 방화벽을 구성 하는 방법](tutorial-restrict-web-traffic-powershell.md)합니다.
