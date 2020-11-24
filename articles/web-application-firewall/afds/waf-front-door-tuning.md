---
title: Azure Front 도어 용 WAF (웹 응용 프로그램 방화벽) 튜닝
description: 이 문서에서는 전방 도어 용 WAF를 조정 하는 방법에 대해 알아봅니다.
services: web-application-firewall
author: mohitkusecurity
ms.service: web-application-firewall
ms.topic: conceptual
ms.date: 12/11/2020
ms.author: mohitku
ms.reviewer: tyao
ms.openlocfilehash: a24f9e78de34b17977a1876cbefb473cc2610db0
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2020
ms.locfileid: "95550048"
---
# <a name="tuning-web-application-firewall-waf-for-azure-front-door"></a>Azure Front 도어 용 WAF (웹 응용 프로그램 방화벽) 튜닝
 
Azure 관리 되는 기본 규칙 집합은 [OWASP CRS (핵심 규칙 집합)](https://github.com/SpiderLabs/owasp-modsecurity-crs/tree/v3.1/dev) 를 기반으로 하며 기본적으로 사용 하도록 설계 되었습니다. Waf를 사용 하는 응용 프로그램 또는 조직의 특정 요구에 맞게 WAF 규칙을 조정 해야 하는 경우가 종종 있습니다. 이는 일반적으로 규칙 제외를 정의 하 고 사용자 지정 규칙을 생성 하 고 문제 또는 가양성을 일으킬 수 있는 규칙을 사용 하지 않도록 설정 하 여 수행 됩니다. WAF (웹 응용 프로그램 방화벽)를 통과 해야 하는 요청이 차단 된 경우 몇 가지 작업을 수행할 수 있습니다.

먼저 front [도어 waf 개요](afds-overview.md) 와 [전방 도어 문서에 대 한 waf 정책을](waf-front-door-create-portal.md) 읽어야 합니다. 또한 [Waf 모니터링 및 로깅을](waf-front-door-monitor.md)사용 하도록 설정 했는지 확인 합니다. 이러한 문서에서는 WAF 함수, WAF 규칙 집합의 작동 방식 및 WAF 로그에 액세스 하는 방법에 대해 설명 합니다.
 
## <a name="understanding-waf-logs"></a>WAF 로그 이해
 
WAF 로그의 목적은 WAF에 의해 일치 되거나 차단 되는 모든 요청을 표시 하는 것입니다. 일치 하거나 차단 된 모든 계산 된 요청의 컬렉션입니다. WAF가 요청 (거짓 긍정)을 차단 하지 않도록 차단 하는 경우 몇 가지 작업을 수행할 수 있습니다. 먼저, 범위를 좁혀 특정 요청을 찾습니다. 원하는 경우 이벤트를 쉽게 식별 하 고 해당 특정 값에 대 한 로그 쿼리를 수행 하기 위해 필드를 포함 하도록 [사용자 지정 응답 메시지를 구성할](./waf-front-door-configure-custom-response-code.md) 수 있습니다 `trackingReference` . 로그를 확인 하 여 요청의 특정 URI, 타임 스탬프 또는 클라이언트 IP를 찾습니다. 관련 로그 항목을 찾으면 가양성에 대 한 작업을 시작할 수 있습니다. 
 
예를 들어 WAF를 통해 전달 하려는 문자열이 포함 된 합법적인 트래픽이 있다고 가정 `1=1` 합니다. 요청은 다음과 같습니다.

```
POST http://afdwafdemosite.azurefd.net/api/Feedbacks HTTP/1.1
Host: afdwafdemosite.azurefd.net
Content-Type: application/x-www-form-urlencoded
Content-Length: 55

UserId=20&captchaId=7&captchaId=15&comment="1=1"&rating=3
```

요청을 시도 하면 WAF는 매개 변수 또는 필드에 *1 = 1* 문자열을 포함 하는 트래픽을 차단 합니다. 이는 종종 SQL 삽입 공격과 관련 된 문자열입니다. 로그를 확인 하 고 요청의 타임 스탬프와 차단/일치 된 규칙을 확인할 수 있습니다.
 
다음 예제에서는 `FrontdoorWebApplicationFirewallLog` 규칙 일치 때문에 생성 된 로그를 탐색 합니다.
 
"RequestUri" 필드에서 특별히 요청이 수행 된 것을 볼 수 있습니다 `/api/Feedbacks/` . `942110`"RuleName" 필드에서 규칙 ID를 찾을 수도 있습니다. 규칙 ID를 알면 [OWASP ModSecurity Core 규칙 집합 공식 리포지토리로](https://github.com/coreruleset/coreruleset) 이동 하 고 해당 [규칙 id](https://github.com/coreruleset/coreruleset/blob/v3.1/dev/rules/REQUEST-942-APPLICATION-ATTACK-SQLI.conf) 로 검색 하 여 코드를 검토 하 고이 규칙과 일치 하는 항목을 정확 하 게 이해할 수 있습니다. 
 
그런 다음 필드를 확인 하 여 `action` 이 규칙이 일치 하는 경우 요청을 차단 하도록 설정 된 것을 확인 하 고,가로 설정 되었으므로 WAF에 의해 요청이 실제로 차단 되었음을 확인 합니다 `policyMode` `prevention` . 
 
이제 필드의 정보를 확인해 보겠습니다 `details` . 여기에서 및 정보를 볼 수 있습니다 `matchVariableName` `matchVariableValue` . 사용자가 웹 앱의 필드에 *1 = 1* 을 입력 했기 때문에이 규칙이 트리거된 것을 확인 `comment` 합니다.
 
```json
{
    "time": "2020-09-24T16:43:04.5422943Z",
    "resourceId": "/SUBSCRIPTIONS/<Subscription ID>/RESOURCEGROUPS/<Resource Group Name>/PROVIDERS/MICROSOFT.NETWORK/FRONTDOORS/AFDWAFDEMOSITE",
    "category": "FrontdoorWebApplicationFirewallLog",
    "operationName": "Microsoft.Network/FrontDoor/WebApplicationFirewallLog/Write",
    "properties": {
        "clientIP": "1.1.1.1",
        "clientPort": "53566",
        "socketIP": "1.1.1.1",
        "requestUri": "http://afdwafdemosite.azurefd.net:80/api/Feedbacks/",
        "ruleName": "DefaultRuleSet-1.0-SQLI-942110",
        "policy": "AFDWAFDemoPolicy",
        "action": "Block",
        "host": "afdwafdemosite.azurefd.net",
        "trackingReference": "0mMxsXwAAAABEalekYeI4S55qpi5R7R0/V1NURURHRTA4MTIAZGI4NGQzZDgtNWQ5Ny00ZWRkLTg2ZGYtZDJjNThlMzI2N2I4",
        "policyMode": "prevention",
        "details": {
            "matches": [
                {
                    "matchVariableName": "PostParamValue:comment",
                    "matchVariableValue": "\"1=1\""
                }
            ],
            "msg": "SQL Injection Attack: Common Injection Testing Detected",
            "data": "Matched Data: \"1=1\" found within PostParamValue:comment: \"1=1\""
        }
    }
}
```
 
또한 액세스 로그를 확인 하 여 지정 된 WAF 이벤트에 대 한 정보를 확장할 수 있습니다. 아래에서 `FrontdoorAccessLog` 위의 이벤트에 대 한 응답으로 생성 된 로그를 검토 합니다.
 
동일 하 게 표시 되는 값을 기반으로 하는 관련 로그를 볼 수 있습니다 `trackingReference` . 및와 같은 일반적인 정보를 제공 하는 다양 한 필드 중에서 `userAgent` `clientIP` 및 필드에 주의를 기울여야 `httpStatusCode` `httpStatusDetails` 합니다. 여기에서 클라이언트가 HTTP 403 응답을 받았음을 확인할 수 있으며,이는이 요청이 거부 되 고 차단 되었음을 나타내는 것입니다. 
 
```json
{
    "time": "2020-09-24T16:43:04.5430764Z",
    "resourceId": "/SUBSCRIPTIONS/<Subscription ID>/RESOURCEGROUPS/<Resource Group Name>/PROVIDERS/MICROSOFT.NETWORK/FRONTDOORS/AFDWAFDEMOSITE",
    "category": "FrontdoorAccessLog",
    "operationName": "Microsoft.Network/FrontDoor/AccessLog/Write",
    "properties": {
        "trackingReference": "0mMxsXwAAAABEalekYeI4S55qpi5R7R0/V1NURURHRTA4MTIAZGI4NGQzZDgtNWQ5Ny00ZWRkLTg2ZGYtZDJjNThlMzI2N2I4",
        "httpMethod": "POST",
        "httpVersion": "1.1",
        "requestUri": "http://afdwafdemosite.azurefd.net:80/api/Feedbacks/",
        "requestBytes": "2160",
        "responseBytes": "324",
        "userAgent": "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/85.0.4183.83 Safari/537.36",
        "clientIp": "1.1.1.1",
        "socketIp": "1.1.1.1",
        "clientPort": "53566",
        "timeToFirstByte": "0.01",
        "timeTaken": "0.011",
        "securityProtocol": "",
        "routingRuleName": "DemoBERoutingRule",
        "rulesEngineMatchNames": [],
        "backendHostname": "13.88.65.130:3000",
        "isReceivedFromClient": true,
        "httpStatusCode": "403",
        "httpStatusDetails": "403",
        "pop": "WST",
        "cacheStatus": "CONFIG_NOCACHE"
    }
}
```

## <a name="resolving-false-positives"></a>가양성 확인
 
가양성을 처리 하는 방법에 대 한 의사 결정을 내리는 것은 응용 프로그램에서 사용 하는 기술에 대해 잘 알고 있어야 합니다. 예를 들어, 기술 스택에 SQL server가 없고 해당 규칙과 관련 된 가양성을 받고 있다고 가정해 보겠습니다. 이러한 규칙을 사용 하지 않도록 설정 하면 보안이 약화 되지 않을 수도 있습니다. 

이 정보를 사용 하는 942110 경우이 예제에서 해당 문자열과 일치 하는 정보를 제공 하는 정보를 사용 하 여 `1=1` 이 합법적인 요청이 차단 되는 것을 중지할 수 있습니다.
 
* 제외 목록 사용
  * 제외 목록에 대 한 자세한 내용은 [FRONT f (웹 응용 프로그램 방화벽)에서 Front 도어 서비스 제외 목록을](waf-front-door-exclusion.md) 참조 하세요. 
* WAF 작업 변경
  * 요청이 규칙 조건과 일치할 경우 수행할 수 있는 작업에 대 한 자세한 내용은 [Waf 작업](afds-overview.md#waf-actions) 을 참조 하세요.
* 사용자 지정 규칙 사용
  * 사용자 지정 규칙에 대 한 자세한 내용은 [Azure Front 도어를 사용한 웹 응용 프로그램 방화벽에 대 한 사용자 지정 규칙](waf-front-door-custom-rules.md) 을 참조 하세요.
* 규칙 사용 안 함 

> [!TIP]
> WAF를 통해 합법적인 요청을 허용 하는 방법을 선택 하는 경우 가능 하면 좁게 설정 해 보세요. 예를 들어 규칙을 완전히 사용 하지 않도록 설정 하는 것 보다 제외 목록을 사용 하는 것이 좋습니다.

### <a name="using-exclusion-lists"></a>제외 목록 사용

제외 목록을 사용할 경우의 한 가지 장점으로, 제외 하도록 선택한 일치 변수만 지정 된 요청에 대해 더 이상 검사 되지 않습니다. 즉, 전체 요청을 검사 하지 않고 특정 조건이 충족 될 경우 제외할 특정 요청 헤더, 요청 쿠키, 쿼리 문자열 인수 또는 요청 본문 게시 인수 중에서 선택할 수 있습니다. 요청에 대해 지정 되지 않은 다른 변수는 여전히 정상적으로 검사 됩니다.
 
제외는 전역 설정 이라고 하는 것이 중요 합니다. 즉, 구성 된 제외는 특정 웹 앱 또는 URI 뿐 아니라 WAF를 통해 전달 되는 모든 트래픽에 적용 됩니다. 예를 들어 *1 = 1* 이 특정 웹 앱에 대 한 본문의 유효한 요청 이지만 동일한 waf 정책에 있는 다른 사용자에 대해서는 그렇지 않은 경우 문제가 될 수 있습니다. 응용 프로그램 마다 서로 다른 제외 목록을 사용 하는 것이 적합 한 경우 각 응용 프로그램에 대해 서로 다른 WAF 정책을 사용 하 여 각 응용 프로그램의 프런트 엔드에 적용 하는 것이 좋습니다.
 
관리 되는 규칙에 대 한 제외 목록을 구성할 때 규칙 집합 내의 모든 규칙, 규칙 그룹 내의 모든 규칙 또는 개별 규칙을 제외 하도록 선택할 수 있습니다. 제외 목록은 [PowerShell](https://docs.microsoft.com/powershell/module/az.frontdoor/New-AzFrontDoorWafManagedRuleExclusionObject?view=azps-4.7.0&viewFallbackFrom=azps-3.5.0), [AZURE CLI](https://docs.microsoft.com/cli/azure/ext/front-door/network/front-door/waf-policy/managed-rules/exclusion?view=azure-cli-latest#ext_front_door_az_network_front_door_waf_policy_managed_rules_exclusion_add), [Rest API](https://docs.microsoft.com/rest/api/frontdoorservice/webapplicationfirewall/policies/createorupdate)또는 Azure Portal를 사용 하 여 구성할 수 있습니다.

* 규칙 수준에서 제외
  * 규칙 수준에서 제외를 적용 하면 지정 된 제외가 해당 개별 규칙에 대해서만 분석 되는 것이 아니라 규칙 집합의 다른 모든 규칙에 따라 분석 됩니다. 이는 제외에 대 한 가장 세분화 된 수준 이며 이벤트 문제를 해결할 때 WAF 로그에서 찾은 정보를 기반으로 관리 되는 규칙 집합을 미세 조정 하는 데 사용할 수 있습니다.
* 규칙 그룹 수준에서 제외
  * 규칙 그룹 수준에서 제외를 적용 하면 지정 된 제외가 특정 규칙 형식 집합에 대해 분석 되지 않습니다. 예를 들어 *Sqli* 를 제외 된 규칙 그룹으로 선택 하면 정의 된 요청 제외가 sqli 특정 규칙에서 검사 되지 않지만 *PHP*, *rfi* 또는 *XSS* 와 같은 다른 그룹의 규칙에 의해 검사 됩니다. 이 유형의 제외는 응용 프로그램이 특정 유형의 공격을 받을 수 없는 경우에 유용할 수 있습니다. 예를 들어, SQL 데이터베이스가 없는 응용 프로그램에는 보안 수준에 부정적인 영향을 주지 않으면 서 모든 *Sqli* 규칙이 제외 될 수 있습니다.
* 규칙 집합 수준에서 제외 
  * 규칙 집합 수준에서 제외를 적용 하면 해당 규칙 집합에서 사용할 수 있는 보안 규칙에 대해 지정 된 제외가 분석 되지 않습니다. 이는 포괄적인 제외 이므로 신중 하 게 사용 해야 합니다.

이 예제에서는 가장 세분화 된 수준 (단일 규칙에 제외 적용)에서 제외를 수행 하 고가 포함 된 일치 변수 **요청 본문 게시 args 이름을** 제외 하려고 `comment` 합니다. 이는 방화벽 로그에서 일치 변수 세부 정보를 볼 수 있기 때문에 분명 `"matchVariableName": "PostParamValue:comment"` 합니다. 특성이 `comment` 인 경우 다른 몇 가지 방법으로이 특성 이름을 찾을 수도 있습니다. [요청 특성 이름 찾기](#finding-request-attribute-names)를 참조 하세요.

![제외 규칙](../media/waf-front-door-tuning/exclusion-rules.png)

![특정 규칙에 대 한 규칙 제외](../media/waf-front-door-tuning/exclusion-rule.png)

때로는 직관적이 지 않을 수 있는 방식으로 특정 매개 변수가 WAF에 전달 되는 경우가 있습니다. 예를 들어 Azure Active Directory를 사용 하 여 인증할 때 전달 되는 토큰이 있습니다. 일반적으로이 토큰은 `__RequestVerificationToken` 요청 쿠키로 전달 됩니다. 그러나 쿠키를 사용 하지 않도록 설정 하는 경우이 토큰은 요청 게시 인수로도 전달 됩니다. 이러한 이유로 Azure AD 토큰 가양성을 해결 하기 위해 `__RequestVerificationToken` 가 및 둘 다에 대 한 제외 목록에 추가 되었는지 확인 해야 `RequestCookieNames` `RequestBodyPostArgsNames` 합니다.

필드 이름 (*선택기*)에 대 한 제외는 waf에서 더 이상 값을 계산 하지 않음을 의미 합니다. 그러나 필드 이름 자체는 계속 계산 되며 드문 경우 이지만 WAF 규칙을 일치 시키고 작업을 트리거할 수 있습니다.

![규칙 집합에 대 한 규칙 제외](../media/waf-front-door-tuning/exclusion-rule-selector.png)

### <a name="changing-waf-actions"></a>WAF 작업 변경

WAF 규칙의 동작을 처리 하는 또 다른 방법은 요청이 규칙의 조건과 일치 하는 경우에 수행할 작업을 선택 하는 것입니다. 사용 가능한 작업은 [Allow, Block, Log 및 Redirect](afds-overview.md#waf-actions)입니다.

이 예제에서는 기본 작업 *블록* 을 규칙 942110에 대 한 *로그* 작업으로 변경 했습니다. 이렇게 하면 WAF에서 요청을 기록 하 고 나머지 낮은 우선 순위 규칙에 대해 동일한 요청을 계속 해 서 평가할 수 있습니다.

![WAF 작업](../media/waf-front-door-tuning/actions.png)

동일한 요청을 수행한 후 로그를 다시 참조할 수 있으며,이 요청이 규칙 ID 942110에서 일치 하는 것을 확인할 수 있으며, `action_s` 이제 필드는 *블록* 대신 *Log* 를 나타냅니다. 그런 다음 로그 쿼리를 확장 하 여 정보를 포함 하 `trackingReference_s` 고이 요청에서 발생 한 다른 내용을 확인 합니다.

![여러 규칙 일치를 보여 주는 로그](../media/waf-front-door-tuning/actions-log.png)

흥미롭게도 규칙 ID 942110이 처리 된 후에는 다른 SQLI 규칙 일치가 발생 합니다 (밀리초). 동일한 요청이 규칙 ID 942310에서 일치 되었으며 이번에는 기본 동작 *블록이* 트리거됨

WAF 튜닝 또는 문제 해결 중에 *로그* 작업을 사용 하는 또 다른 이점은 특정 규칙 그룹 내의 여러 규칙이 일치 하 고 지정 된 요청을 차단 하는지 여부를 식별할 수 있다는 것입니다. 그런 다음 적절 한 수준 (즉, 규칙 또는 규칙 그룹 수준)에서 제외를 만들 수 있습니다. 

### <a name="using-custom-rules"></a>사용자 지정 규칙 사용

WAF 규칙 일치의 원인을 파악 한 후 사용자 지정 규칙을 사용 하 여 WAF가 이벤트에 응답 하는 방법을 조정할 수 있습니다. 사용자 지정 규칙은 관리 되는 규칙 보다 먼저 처리 되 고, 둘 이상의 조건을 포함할 수 있으며, 해당 작업은 [허용, 거부, 로그 또는 리디렉션할](afds-overview.md#waf-actions)수 있습니다. 규칙이 일치 하면 WAF 엔진이 처리를 중지 합니다. 즉, 우선 순위가 낮은 다른 사용자 지정 규칙 및 관리 되는 규칙이 더 이상 실행 되지 않습니다.

아래 예제에서는 두 가지 조건을 사용 하 여 사용자 지정 규칙을 만들었습니다. 첫 번째 조건은 `comment` 요청 본문에서 값을 찾습니다. 두 번째 조건은 `/api/Feedbacks/` 요청 URI에서 값을 찾습니다.

사용자 지정 규칙을 사용 하면 WAF 규칙을 미세 조정 하 고 가양성을 처리할 때 가장 세분화 된 기능을 사용할 수 있습니다. 이 경우 요청 본문 값을 기반으로 하는 작업을 수행 하지 않습니다 `comment` .이 값은 동일한 WAF 정책에서 여러 사이트 또는 앱에 있을 수 있습니다. 특정 요청 URI에도 일치 하는 다른 조건을 포함 하 여 `/api/Feedbacks/` 이 사용자 지정 규칙은 점검 되었다는이 명시적인 사용 사례에 실제로 적용 되도록 합니다. 이렇게 하면 다른 조건에 대해 수행 되는 경우에도 동일한 공격이 WAF 엔진에 의해 검사 되 고 방지 됩니다.

![로그](../media/waf-front-door-tuning/custom-rule.png)

로그를 탐색할 때 `ruleName_s` 만든 사용자 지정 규칙에 지정 된 이름이 필드에 포함 되어 있음을 알 수 있습니다 `redirectcomment` . 필드에서 `action_s` 이 이벤트에 대해 수행 된 *리디렉션* 작업을 확인할 수 있습니다. 필드에서 `details_matches_s` 두 조건에 대 한 세부 정보가 일치 하는 것을 볼 수 있습니다.

### <a name="disabling-rules"></a>규칙 사용 안 함

가양성을 해결 하는 또 다른 방법은 WAF가 악의적 이라고 생각 하는 입력에 일치 하는 규칙을 비활성화 하는 것입니다. WAF 로그를 구문 분석 하 고 규칙을 942110으로 축소 했으므로 Azure Portal에서 사용 하지 않도록 설정할 수 있습니다. [Azure Portal를 사용 하 여 웹 응용 프로그램 방화벽 규칙 사용자 지정을](../ag/application-gateway-customize-waf-rules-portal.md#disable-rule-groups-and-rules)참조 하세요.
 
규칙을 사용 하지 않도록 설정 하는 것은 해당 특정 조건을 충족 하는 모든 요청이 실제로 합법적인 요청 이거나 사용자 환경에 적용 되지 않을 것을 확신할 수 있는 경우 (예: SQL 백 엔드가 아닌 sql 삽입 규칙 사용 안 함)에 유용 합니다. 
 
그러나 규칙을 사용 하지 않도록 설정 하는 것은 WAF 정책에 연결 된 모든 프런트 엔드 호스트에 적용 되는 전역 설정입니다. 규칙을 사용 하지 않도록 선택 하면 WAF 정책에 연결 된 다른 모든 프런트 엔드 호스트에 대해 보호 또는 검색 없이 취약성이 노출 될 수 있습니다.
 
Azure PowerShell를 사용 하 여 관리 되는 규칙을 사용 하지 않으려면 개체 설명서를 참조 하세요 [`PSAzureManagedRuleOverride`](https://docs.microsoft.com/powershell/module/az.frontdoor/new-azfrontdoorwafmanagedruleoverrideobject?view=azps-4.7.0&preserve-view=true) . Azure CLI를 사용 하려면 설명서를 참조 [`az network front-door waf-policy managed-rules override`](https://docs.microsoft.com/cli/azure/ext/front-door/network/front-door/waf-policy/managed-rules/override?view=azure-cli-latest&preserve-view=true) 하세요.

![WAF 규칙](../media/waf-front-door-tuning/waf-rules.png)

## <a name="finding-request-fields"></a>요청 필드 찾기

[Fiddler](https://www.telerik.com/fiddler)와 같은 브라우저 프록시를 사용 하 여 개별 요청을 검사 하 고 웹 페이지의 특정 필드를 확인할 수 있습니다. 이 기능은 WAF의 제외 목록을 사용 하 여 검사에서 특정 필드를 제외 해야 하는 경우에 유용 합니다.

### <a name="finding-request-attribute-names"></a>요청 특성 이름 찾기
 
이 예에서는 문자열이 입력 된 필드를 확인할 수 있습니다 `1=1` `comment` . 이 데이터는 POST 요청의 본문에서 전달 되었습니다.

![본문을 표시 하는 Fiddler 요청](../media/waf-front-door-tuning/fiddler-request-attribute-name.png)

제외할 수 있는 필드입니다. 제외 목록에 대해 자세히 알아보려면 [웹 응용 프로그램 방화벽 제외 목록](./waf-front-door-exclusion.md)을 참조 하세요. 이 경우 다음 제외를 구성 하 여 평가를 제외할 수 있습니다.

![제외 규칙](../media/waf-front-door-tuning/fiddler-request-attribute-name-exclusion.png)

방화벽 로그를 검토 하 여 제외 목록에 추가 해야 하는 항목을 확인 하는 정보를 얻을 수도 있습니다. 로깅을 사용 하도록 설정 하려면 [Azure Front 도어에서 메트릭 및 로그 모니터링](./waf-front-door-monitor.md)을 참조 하세요.

`PT1H.json`검사 하려는 요청이 발생 한 시간에 대 한 파일의 방화벽 로그를 검사 합니다. `PT1H.json``FrontDoorWebApplicationFirewallLog`및 진단 로그가 저장 된 저장소 계정 컨테이너에서 파일을 사용할 수 있습니다 `FrontDoorAccessLog` .

이 예에서는 동일한 트랜잭션 참조를 사용 하 여 요청을 차단 하 고 정확히 동일한 시간에 발생 한 규칙을 볼 수 있습니다.

```json
{
    "time": "2020-09-24T16:43:04.5422943Z",
    "resourceId": "/SUBSCRIPTIONS/<Subscription ID>/RESOURCEGROUPS/<Resource Group Name>/PROVIDERS/MICROSOFT.NETWORK/FRONTDOORS/AFDWAFDEMOSITE",
    "category": "FrontdoorWebApplicationFirewallLog",
    "operationName": "Microsoft.Network/FrontDoor/WebApplicationFirewallLog/Write",
    "properties": {
        "clientIP": "1.1.1.1",
        "clientPort": "53566",
        "socketIP": "1.1.1.1",
        "requestUri": "http://afdwafdemosite.azurefd.net:80/api/Feedbacks/",
        "ruleName": "DefaultRuleSet-1.0-SQLI-942110",
        "policy": "AFDWAFDemoPolicy",
        "action": "Block",
        "host": "afdwafdemosite.azurefd.net",
        "trackingReference": "0mMxsXwAAAABEalekYeI4S55qpi5R7R0/V1NURURHRTA4MTIAZGI4NGQzZDgtNWQ5Ny00ZWRkLTg2ZGYtZDJjNThlMzI2N2I4",
        "policyMode": "prevention",
        "details": {
            "matches": [
                {
                    "matchVariableName": "PostParamValue:comment",
                    "matchVariableValue": "\"1=1\""
                }
            ],
            "msg": "SQL Injection Attack: Common Injection Testing Detected",
            "data": "Matched Data: \"1=1\" found within PostParamValue:comment: \"1=1\""
        }
    }
}
```

Azure 관리 되는 규칙 집합이 작동 하는 방식에 대 한 정보 ( [Azure Front 도어의 웹 응용 프로그램 방화벽](afds-overview.md)참조)는 *action: Block* 속성을 사용 하는 규칙이 요청 본문에 일치 하는 데이터를 기반으로 차단 한다는 것을 알고 있습니다. 세부 정보에서 패턴 ()과 일치 하는 항목을 볼 수 `1=1` 있으며 필드의 이름은로 지정 됩니다 `comment` . 동일한 이전 단계를 수행 하 여이 포함 된 요청 본문 게시 args 이름을 제외 합니다 `comment` .

### <a name="finding-request-header-names"></a>요청 헤더 이름 찾기

Fiddler는 요청 헤더 이름을 찾을 때 유용한 도구입니다. 다음 스크린샷에서는이 GET 요청에 대 한 헤더를 볼 수 있습니다. 여기에는 Content-type, 사용자 에이전트 등이 포함 됩니다. 또한 WAF에서 요청 헤더를 사용 하 여 제외 및 사용자 지정 규칙을 만들 수 있습니다.

![헤더를 보여 주는 Fiddler 요청](../media/waf-front-door-tuning/fiddler-request-header-name.png)

요청 및 응답 헤더를 보는 또 다른 방법은 Edge 또는 Chrome과 같은 브라우저의 개발자 도구 내에서 확인 하는 것입니다. F12 키를 누르거나 마우스 오른쪽 단추를 클릭 하 **Inspect**>  ->  **개발자 도구** 검사 하 고 **네트워크** 탭을 선택할 수 있습니다. 웹 페이지를 로드 하 고 검사 하려는 요청을 클릭 합니다.

![네트워크 검사기 요청](../media/waf-front-door-tuning/network-inspector-request.png)

### <a name="finding-request-cookie-names"></a>요청 쿠키 이름 찾기

요청이 쿠키를 포함 하는 경우 쿠키 탭을 선택 하 여 Fiddler에서 볼 수 있습니다. 쿠키 정보를 사용 하 여 WAF에서 제외 또는 사용자 지정 규칙을 만들 수도 있습니다.

## <a name="next-steps"></a>다음 단계

- [Azure 웹 애플리케이션 방화벽](../overview.md)에 대해 알아봅니다.
- [Front Door를 만드는](../../frontdoor/quickstart-create-front-door.md) 방법을 알아봅니다.
