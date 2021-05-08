---
title: Azure Front Door의 Web Application Firewall 제외 목록 - Azure portal
description: 이 문서에서는 Azure Portal을 사용하는 Azure Front의 제외 목록 구성에 대한 정보를 제공합니다.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.date: 11/10/2020
ms.author: victorh
ms.topic: conceptual
ms.openlocfilehash: 83baf03c414d9b0f7acb6a93db03794a539a3c58
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/22/2021
ms.locfileid: "107860836"
---
# <a name="web-application-firewall-waf-with-front-door-service-exclusion-lists"></a>Front Door Service 제외 목록이 포함된 WAF(Web Application Firewall) 

경우에 따라 WAF(Web Application Firewall)에서 애플리케이션에 허용하려는 요청을 차단할 수 있습니다. 예를 들어 Active Directory가 인증에 사용되는 토큰을 삽입합니다. 해당 토큰은 WAF 규칙에서 가양성을 유발할 수 있는 특수 문자를 포함할 수 있습니다. WAF 제외 목록을 통해 WAF 평가에서 특정 요청 특성을 생략할 수 있습니다.  제외 목록은 [PowerShell](/powershell/module/az.frontdoor/New-AzFrontDoorWafManagedRuleExclusionObject), [Azure CLI](/cli/azure/network/front-door/waf-policy/managed-rules/exclusion#az_network_front_door_waf_policy_managed_rules_exclusion_add), [Rest API](/rest/api/frontdoorservice/webapplicationfirewall/policies/createorupdate) 또는 Azure Portal을 사용하여 구성할 수 있습니다. 다음 예에서는 Azure Portal 구성을 보여 줍니다. 
## <a name="configure-exclusion-lists-using-the-azure-portal"></a>Azure Portal을 사용하여 제외 목록 구성
**관리형 규칙** 의 WAF 포털에서 **제외 관리** 에 액세스할 수 있습니다.

![제외 관리](../media/waf-front-door-exclusion/exclusion1.png)
![exclusion_add 관리](../media/waf-front-door-exclusion/exclusion2.png)

 제외 목록 예: ![exclusion_define 관리](../media/waf-front-door-exclusion/exclusion3.png)

이 예에서는 ‘사용자’ 헤더 필드의 값을 제외합니다. 유효한 요청에는 SQL 삽입 규칙을 트리거하는 문자열이 포함된 ‘사용자’ 필드가 포함될 수 있습니다. 이 경우 WAF 규칙이 필드에서 아무것도 평가하지 않도록 ‘사용자’ 매개 변수를 제외할 수 있습니다.

다음 특성은 이름으로 제외 목록에 추가할 수 있습니다. 사용하는 필드의 값은 WAF 규칙에 따라 평가되지 않지만 해당 이름은 평가됩니다. 제외 목록은 필드 값 검사를 제거합니다.

* 요청 헤더 이름
* 요청 쿠키 이름
* 쿼리 문자열 인수 이름
* 요청 본문 게시물 인수 이름

정확히 일치하는 요청 헤더, 본문, 쿠키 또는 쿼리 문자열 특성을 지정할 수 있습니다.  또는 필요한 경우 부분 일치를 지정할 수도 있습니다. 다음 연산자는 지원되는 일치 조건입니다.

- **equals**: 정확한 일치에 사용됩니다. 예를 들어 **bearerToken** 이라는 헤더를 선택하려면 **bearerToken** 으로 설정된 선택기 세트와 equals 연산자를 사용합니다.
- **starts with**: 이 연산자는 지정된 선택기 값으로 시작하는 모든 필드와 일치합니다.
- **ends with**: 이 연산자는 지정된 선택기 값으로 끝나는 모든 요청 필드와 일치합니다.
- **contains**: 이 연산자는 지정된 선택기 값을 포함하는 모든 요청 필드와 일치합니다.
- **Equals any**: 이 연산자는 모든 요청 필드와 일치합니다. *는 선택기 값입니다.

헤더 및 쿠키 이름은 대/소문자를 구분하지 않습니다.

헤더 값, 쿠키 값, Post 인수 값 또는 쿼리 인수 값이 일부 규칙에 대해 가양성을 생성하는 경우 규칙에 따라 해당 요청 부분을 고려 사항에서 제외할 수 있습니다.


|WAF 로그에서의 matchVariableName  |Portal에서 규칙 제외  |
|---------|---------|
|CookieValue:SOME_NAME        |요청 쿠키 이름이 SOME_NAME과 같음|
|HeaderValue:SOME_NAME        |요청 헤더 이름이 SOME_NAME과 같음|
|PostParamValue:SOME_NAME     |요청 본문 Post 인수 이름이 SOME_NAME과 같음|
|QueryParamValue:SOME_NAME    |쿼리 문자열 인수 이름이 SOME_NAME과 같음|


현재 해당 WAF 로그에서 위의 matchVariableNames에 대한 규칙 제외만 지원합니다. 다른 matchVariableNames의 경우 가양성을 제공하는 규칙을 사용하지 않도록 설정하거나 명시적으로 해당 요청을 허용하는 사용자 지정 규칙을 만들어야 합니다. 특히 matchVariableName이 CookieName, HeaderName, PostParamName 또는 QueryParamName인 경우 이름 자체가 규칙을 트리거하는 것을 의미합니다. 현재 규칙 제외는 matchVariableNames를 지원하지 않습니다.


*FOO* 라는 요청 본문 post 인수를 제외하면 WAF 로그에서 어떤 규칙도 PostParamValue:FOO를 matchVariableName로 표시하지 않아야 합니다. 그러나 post 매개 변수 값이 InitialBodyContents의 일부이기 때문에 post 매개 변수 FOO의 값과 일치하는 matchVariableName InitialBodyContents 규칙이 계속 표시될 수 있습니다.

이전 예에서 본 것처럼 관리형 규칙 집합 내의 모든 규칙, 특정 규칙 그룹에 대한 규칙 또는 단일 규칙에 제외 목록을 적용할 수 있습니다.

## <a name="define-exclusion-based-on-web-application-firewall-logs"></a>Web Application Firewall 로그를 기반으로 제외 정의
 [Azure Web Application Firewall 모니터링 및 로깅](waf-front-door-monitor.md)은 차단된 요청과 일치하는 세부 정보를 표시합니다. 헤더 값, 쿠키 값, Post 인수 값 또는 쿼리 인수 값이 일부 규칙에 대해 가양성을 생성하는 경우 규칙에 따라 해당 요청 부분을 고려 사항에서 제외할 수 있습니다. 다음 표에서는 WAF 로그와 해당 제외 조건의 예제 값을 보여 줍니다.

|WAF 로그에서의 matchVariableName    |Portal에서 규칙 제외|
|--------|------|
|CookieValue:SOME_NAME  |요청 쿠키 이름이 SOME_NAME과 같음|
|HeaderValue:SOME_NAME  |요청 헤더 이름이 SOME_NAME과 같음|
|PostParamValue:SOME_NAME|  요청 본문 Post 인수 이름이 SOME_NAME과 같음|
|QueryParamValue:SOME_NAME| 쿼리 문자열 인수 이름이 SOME_NAME과 같음|


## <a name="next-steps"></a>다음 단계

WAF 설정을 구성한 후에는 WAF 로그 확인 방법을 살펴봅니다. 자세한 내용은 [Front Door 진단](../afds/waf-front-door-monitor.md)을 참조하세요.