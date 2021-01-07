---
title: Azure의 웹 응용 프로그램 방화벽 제외 목록-Azure Portal
description: 이 문서에서는 Azure Portal와 함께 Azure의 제외 목록 구성에 대 한 정보를 제공 합니다.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.date: 11/10/2020
ms.author: victorh
ms.topic: conceptual
ms.openlocfilehash: c2c84b508ee86ebdd82dbcc7040106142187c506
ms.sourcegitcommit: 04fb3a2b272d4bbc43de5b4dbceda9d4c9701310
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/12/2020
ms.locfileid: "94563463"
---
# <a name="web-application-firewall-waf-with-front-door-service-exclusion-lists"></a>프런트 도어 서비스 제외 목록이 포함 된 WAF (웹 응용 프로그램 방화벽) 

경우에 따라 WAF (웹 응용 프로그램 방화벽)에서 응용 프로그램에 허용 하려는 요청을 차단할 수 있습니다. 예를 들어 Active Directory는 인증에 사용 되는 토큰을 삽입 합니다. 이러한 토큰은 WAF 규칙에서 거짓 긍정을 트리거할 수 있는 특수 문자를 포함할 수 있습니다. WAF 제외 목록을 통해 WAF 평가에서 특정 요청 특성을 생략할 수 있습니다.  제외 목록은  [PowerShell](/powershell/module/az.frontdoor/New-AzFrontDoorWafManagedRuleExclusionObject?view=azps-3.5.0), [AZURE CLI](/cli/azure/ext/front-door/network/front-door/waf-policy/managed-rules/exclusion?view=azure-cli-latest#ext-front-door-az-network-front-door-waf-policy-managed-rules-exclusion-add), [Rest API](/rest/api/frontdoorservice/webapplicationfirewall/policies/createorupdate)또는 Azure Portal를 사용 하 여 구성할 수 있습니다. 다음 예에서는 Azure Portal 구성을 보여 줍니다. 
## <a name="configure-exclusion-lists-using-the-azure-portal"></a>Azure Portal를 사용 하 여 제외 목록 구성
**관리 되는 규칙** 의 waf 포털에서 **제외 관리** 에 액세스할 수 있습니다.

![제외 ](../media/waf-front-door-exclusion/exclusion1.png)
 ![ 관리 exclusion_add 관리](../media/waf-front-door-exclusion/exclusion2.png)

 제외 목록 예: ![ 관리 exclusion_define](../media/waf-front-door-exclusion/exclusion3.png)

이 예에서는 *사용자* 헤더 필드의 값을 제외 합니다. 유효한 요청에는 SQL 삽입 규칙을 트리거하는 문자열이 포함 된 *사용자* 필드가 포함 될 수 있습니다. 이 경우 WAF 규칙이 필드에서 아무것도 평가 하지 않도록 *사용자* 매개 변수를 제외할 수 있습니다.

다음 특성은 이름으로 제외 목록에 추가할 수 있습니다. 사용 하는 필드의 값은 WAF 규칙에 대해 평가 되지 않지만 이름이 평가 됩니다. 제외 목록은 필드 값 검사를 제거 합니다.

* 요청 헤더 이름
* 요청 쿠키 이름
* 쿼리 문자열 인수 이름
* 요청 본문 게시 인수 이름

정확히 일치하는 요청 헤더, 본문, 쿠키 또는 쿼리 문자열 특성을 지정할 수 있습니다.  또는 필요한 경우 부분 일치를 지정할 수도 있습니다. 다음 연산자는 지원 되는 일치 조건입니다.

- **equals** : 정확한 일치에 사용됩니다. 예를 들어 **bearerToken** 라는 헤더를 선택 하려면 **bearerToken** 로 설정 된 선택기와 equals 연산자를 사용 합니다.
- **starts with** : 이 연산자는 지정된 선택기 값으로 시작하는 모든 필드와 일치합니다.
- **ends with** : 이 연산자는 지정된 선택기 값으로 끝나는 모든 요청 필드와 일치합니다.
- **contains** : 이 연산자는 지정된 선택기 값을 포함하는 모든 요청 필드와 일치합니다.
- **같음 any** :이 연산자는 모든 요청 필드와 일치 합니다. *는 선택기 값입니다.

헤더 및 쿠키 이름은 대/소문자를 구분 하지 않습니다.

헤더 값, 쿠키 값, post 인수 값 또는 쿼리 인수 값이 일부 규칙에 대해 가양성을 생성 하는 경우 규칙의 고려 사항에서 요청 부분을 제외할 수 있습니다.


|WAF 로그에서 matchVariableName  |포털에서 규칙 제외  |
|---------|---------|
|CookieValue: SOME_NAME        |요청 쿠키 이름이 SOME_NAME 같습니다.|
|HeaderValue: SOME_NAME        |요청 헤더 이름이 SOME_NAME 같습니다.|
|PostParamValue: SOME_NAME     |요청 본문 게시 args 이름이 SOME_NAME 같습니다.|
|QueryParamValue: SOME_NAME    |쿼리 문자열 인수 이름이 SOME_NAME 같습니다.|


현재 WAF 로그에서 위의 matchVariableNames에 대 한 규칙 제외만 지원 합니다. 다른 matchVariableNames 경우 가양성을 제공 하는 규칙을 사용 하지 않도록 설정 하거나 명시적으로 해당 요청을 허용 하는 사용자 지정 규칙을 만들어야 합니다. 특히 matchVariableName가 CookieName, 헤드 Ername, PostParamName 또는 QueryParamName 인 경우 이름 자체가 규칙을 트리거하는 것을 의미 합니다. 지금은 규칙 제외에서 이러한 matchVariableNames를 지원 하지 않습니다.


*FOO* 라는 요청 본문 게시 인수를 제외 하면 waf 로그에서 POSTPARAMVALUE: FOO를 matchVariableName로 표시 하지 않는 규칙이 없습니다. 그러나 post 매개 변수 값이 InitialBodyContents의 일부 이기 때문에 post 매개 변수 FOO의 값과 일치 하는 matchVariableName InitialBodyContents 규칙이 계속 표시 될 수 있습니다.

이전 예제에 표시 된 것 처럼 관리 되는 규칙 집합 내의 모든 규칙, 특정 규칙 그룹에 대 한 규칙 또는 단일 규칙에 제외 목록을 적용할 수 있습니다.

## <a name="define-exclusion-based-on-web-application-firewall-logs"></a>웹 응용 프로그램 방화벽 로그를 기반으로 제외 정의
 [Azure 웹 응용 프로그램 방화벽 모니터링 및 로깅은](waf-front-door-monitor.md) 차단 된 요청에 대해 일치 하는 세부 정보를 표시 합니다. 헤더 값, 쿠키 값, post 인수 값 또는 쿼리 인수 값이 일부 규칙에 대해 가양성을 생성 하는 경우에는 해당 요청의 일부가 규칙에서 고려 되지 않도록 제외할 수 있습니다. 다음 표에서는 WAF 로그와 해당 제외 조건의 예제 값을 보여 줍니다.

|WAF 로그에서 matchVariableName    |포털에서 규칙 제외|
|--------|------|
|CookieValue: SOME_NAME  |요청 쿠키 이름이 SOME_NAME 같습니다.|
|HeaderValue: SOME_NAME  |요청 헤더 이름이 SOME_NAME 같습니다.|
|PostParamValue: SOME_NAME|  요청 본문 게시 args 이름이 SOME_NAME 같습니다.|
|QueryParamValue: SOME_NAME| 쿼리 문자열 인수 이름이 SOME_NAME 같습니다.|


## <a name="next-steps"></a>다음 단계

WAF 설정을 구성한 후 WAF 로그를 보는 방법에 대해 알아봅니다. 자세한 내용은 [Front 도어 진단](../afds/waf-front-door-monitor.md)을 참조 하세요.