---
title: Azure 정문 - Azure 포털의 웹 응용 프로그램 방화벽 제외 목록
description: 이 문서에서는 Azure Portal을 통해 Azure Front의 제외 목록 구성에 대한 정보를 제공합니다.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.date: 02/25/2020
ms.author: victorh
ms.topic: conceptual
ms.openlocfilehash: 6ed382e88700e4ecd7f8de20a2c8da7ed3c13566
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77925931"
---
# <a name="web-application-firewall-waf-with-front-door-service-exclusion-lists"></a>정문 서비스 제외 목록이 있는 WAF(웹 응용 프로그램 방화벽) 

WAF(웹 응용 프로그램 방화벽)가 응용 프로그램에 허용할 요청을 차단할 수 있습니다. 예를 들어 Active Directory는 인증에 사용되는 토큰을 삽입합니다. 이러한 토큰에는 WAF 규칙에서 거짓 긍정을 트리거할 수 있는 특수 문자가 포함될 수 있습니다. WAF 제외 목록을 통해 WAF 평가에서 특정 요청 특성을 생략할 수 있습니다.  제외 목록은 [PowserShell,](https://docs.microsoft.com/powershell/module/az.frontdoor/New-AzFrontDoorWafManagedRuleExclusionObject?view=azps-3.5.0) [Azure CLI,](https://docs.microsoft.com/cli/azure/ext/front-door/network/front-door/waf-policy/managed-rules/exclusion?view=azure-cli-latest#ext-front-door-az-network-front-door-waf-policy-managed-rules-exclusion-add) [Rest API](https://docs.microsoft.com/rest/api/frontdoorservice/webapplicationfirewall/policies/createorupdate)또는 Azure 포털을 사용하여 구성할 수 있습니다. 다음 예제에서는 Azure 포털 구성을 보여 주습니다. 
## <a name="configure-exclusion-lists-using-the-azure-portal"></a>Azure 포털을 사용하여 제외 목록 구성
**관리 된 규칙에** 따라 WAF 포털에서 제외 **관리에** 액세스할 수 있습니다.

![제외](../media/waf-front-door-exclusion/exclusion1.png)
![관리 exclusion_add 관리](../media/waf-front-door-exclusion/exclusion2.png)

 예 제외 목록: ![exclusion_define 관리](../media/waf-front-door-exclusion/exclusion3.png)

이 예제에서는 *사용자* 헤더 필드의 값을 제외합니다. 유효한 요청에는 SQL 삽입 규칙을 트리거하는 문자열이 포함된 *사용자* 필드가 포함될 수 있습니다. 이 경우 *WAF* 규칙이 필드의 아무 것도 평가하지 않도록 사용자 매개 변수를 제외할 수 있습니다.

다음 특성을 이름으로 제외 목록에 추가할 수 있습니다. 사용하는 필드의 값은 WAF 규칙에 대해 평가되지 않지만 해당 이름은 평가됩니다. 제외 목록은 필드 값검사를 제거합니다.

* 헤더 이름 요청
* 쿠키 이름 요청
* 쿼리 문자열 args 이름
* 요청 본문 게시물 args 이름

정확히 일치하는 요청 헤더, 본문, 쿠키 또는 쿼리 문자열 특성을 지정할 수 있습니다.  또는 필요한 경우 부분 일치를 지정할 수도 있습니다. 다음 연산자는 지원되는 일치 기준입니다.

- **equals**: 정확한 일치에 사용됩니다. 예를 들어 **bearerToken이라는**헤더를 선택하려면 선택기 집합을 **bearerToken**으로 설정하여 equals 연산자 사용을 사용합니다.
- **starts with**: 이 연산자는 지정된 선택기 값으로 시작하는 모든 필드와 일치합니다.
- **ends with**: 이 연산자는 지정된 선택기 값으로 끝나는 모든 요청 필드와 일치합니다.
- **contains**: 이 연산자는 지정된 선택기 값을 포함하는 모든 요청 필드와 일치합니다.
- **같음**: 이 연산자는 모든 요청 필드와 일치합니다. ※ 선택기 값입니다.

헤더와 쿠키 이름은 대/소문자를 구분하지 않습니다.

관리되는 규칙 집합 내의 모든 규칙, 특정 규칙 그룹에 대한 규칙 또는 이전 예제와 같이 단일 규칙에 제외 목록을 적용할 수 있습니다. 

## <a name="next-steps"></a>다음 단계

WAF 설정을 구성한 후 WAF 로그를 보는 방법을 알아봅니다. 자세한 내용은 [전면 도어 진단을](../afds/waf-front-door-monitor.md)참조하십시오.
