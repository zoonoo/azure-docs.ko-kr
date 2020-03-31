---
title: 응용 프로그램 게이트웨이에서 WAF(Azure 웹 응용 프로그램 방화벽) v2 사용자 지정 규칙
description: 이 문서에서는 Azure 응용 프로그램 게이트웨이에서 WAF(웹 응용 프로그램 방화벽) v2 사용자 지정 규칙에 대한 개요를 제공합니다.
services: web-application-firewall
ms.topic: article
author: vhorne
ms.service: web-application-firewall
ms.date: 01/30/2020
ms.author: victorh
ms.openlocfilehash: 072c7bd5b5b292ca4f0e53c59fcb7e9771331a94
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77031734"
---
# <a name="custom-rules-for-web-application-firewall-v2-on-azure-application-gateway"></a>Azure 응용 프로그램 게이트웨이의 웹 응용 프로그램 방화벽 v2에 대한 사용자 지정 규칙

Azure 응용 프로그램 게이트웨이 웹 응용 프로그램 방화벽(WAF) v2에는 다양한 유형의 공격으로부터 보호를 제공하는 플랫폼 관리 규칙 집합이 함께 제공됩니다. 이러한 공격에는 교차 사이트 스크립팅, SQL 주입 등이 포함됩니다. WAF 관리자인 경우 CRS(핵심 규칙 집합) 규칙을 보강하기 위해 고유한 규칙을 작성할 수 있습니다. 규칙은 일치하는 기준에 따라 요청된 트래픽을 차단하거나 허용할 수 있습니다.

사용자 지정 규칙을 사용하면 WAF를 통과하는 각 요청에 대해 평가되는 고유한 규칙을 만들 수 있습니다. 이러한 규칙은 관리형 규칙 세트의 나머지 규칙보다 높은 우선 순위를 갖습니다. 사용자 지정 규칙에는 규칙 이름, 규칙 우선 순위 및 일치 하는 조건의 배열이 포함 됩니다. 이러한 조건이 충족되면 허용 하거나 차단하기 위한 작업이 수행됩니다.

예를 들어 범위 192.168.5.4/24의 IP 주소에서 모든 요청을 차단할 수 있습니다. 이 규칙에서 연산자는 *IPMatch이고,* 일치값은 IP 주소 범위(192.168.5.4/24)이며, 작업은 트래픽을 차단하는 것입니다. 또한 규칙의 이름과 우선 순위를 설정합니다.

사용자 지정 규칙은 복합 논리를 사용하여 보안 요구 사항을 해결하는 고급 규칙을 만듭니다. 예를 들어(조건 1 **및** 조건 2) **또는** 조건 3입니다. 즉, 조건 1 **및** 조건 2가 **충족되거나** 조건 3이 충족되면 WAF는 사용자 지정 규칙에 지정된 작업을 수행해야 합니다.

동일한 규칙 내에서 다른 일치 조건은 항상 를 사용하여 **복합화됩니다.** 예를 들어 특정 IP 주소에서 트래픽을 차단하고 특정 브라우저를 사용하는 경우에만 차단합니다.

**두** 가지 조건을 원할 경우 두 조건이 서로 다른 규칙에 있어야 합니다. 예를 들어 특정 IP 주소에서 트래픽을 차단하거나 특정 브라우저를 사용하는 경우 트래픽을 차단합니다.

> [!NOTE]
> WAF 사용자 지정 규칙의 최대 수는 100개입니다. 응용 프로그램 게이트웨이 제한에 대한 자세한 내용은 [Azure 구독 및 서비스 제한, 할당량 및 제약 조건을](../../azure-resource-manager/management/azure-subscription-service-limits.md#application-gateway-limits)참조하십시오.

정규식은 CRS 규칙 집합과 마찬가지로 사용자 지정 규칙에서도 지원됩니다. 예제 3 및 5에서 사용자 [지정 웹 응용 프로그램 방화벽 규칙을 사용 하](create-custom-waf-rules.md)여 예제 3 및 5를 참조 합니다.

## <a name="allowing-vs-blocking"></a>허용 vs. 차단

사용자 지정 규칙으로 트래픽을 허용하고 차단하는 것은 간단합니다. 예를 들어 다양한 IP 주소에서 들어오는 모든 트래픽을 차단할 수 있습니다. 요청이 특정 브라우저에서 오는 경우 트래픽을 허용하는 다른 규칙을 만들 수 있습니다.

무언가를 허용하려면 매개 `-Action` 변수가 **허용으로**설정되어 있는지 확인합니다. 무언가를 차단하려면 매개 `-Action` 변수가 **차단으로**설정되어 있는지 확인합니다.

```azurepowershell
$AllowRule = New-AzApplicationGatewayFirewallCustomRule `
   -Name example1 `
   -Priority 2 `
   -RuleType MatchRule `
   -MatchCondition $condition `
   -Action Allow

$BlockRule = New-AzApplicationGatewayFirewallCustomRule `
   -Name example2 `
   -Priority 2 `
   -RuleType MatchRule `
   -MatchCondition $condition `
   -Action Block
```

이전 `$BlockRule` 은 Azure 리소스 관리자의 다음 사용자 지정 규칙에 매핑됩니다.

```json
"customRules": [
      {
        "name": "blockEvilBot",
        "priority": 2,
        "ruleType": "MatchRule",
        "action": "Block",
        "matchConditions": [
          {
            "matchVariables": [
              {
                "variableName": "RequestHeaders",
                "selector": "User-Agent"
              }
            ],
            "operator": "Contains",
            "negationConditon": false,
            "matchValues": [
              "evilbot"
            ],
            "transforms": [
              "Lowercase"
            ]
          }
        ]
      }
    ], 
```

이 사용자 지정 규칙에는 이름, 우선 순위, 작업 및 작업이 수행되기 위해 충족되어야 하는 일치 조건의 배열이 포함됩니다. 이러한 필드에 대한 자세한 설명은 다음 필드 설명을 참조하십시오. 예를 들어 사용자 지정 규칙에서는 [사용자 지정 웹 응용 프로그램 방화벽 규칙 만들기 및 사용을](create-custom-waf-rules.md)참조하십시오.

## <a name="fields-for-custom-rules"></a>사용자 지정 규칙에 대한 필드

### <a name="name-optional"></a>이름 [선택 사항]

규칙의 이름입니다.  로그에 나타납니다.

### <a name="priority-required"></a>우선 순위 [필수]

- 규칙 평가 순서를 결정합니다. 값이 낮을수록 규칙의 평가가 빠집니다. 허용 범위는 1-100입니다. 
- 모든 사용자 지정 규칙에서 고유해야 합니다. 우선 순위가 40인 규칙은 우선 순위가 80인 규칙 보다 앞에 평가됩니다.

### <a name="rule-type-required"></a>규칙 유형 [필수]

현재 는 **MatchRule**이어야 합니다.

### <a name="match-variable-required"></a>일치 변수 [필수]

변수 중 하나여야 합니다.

- RemoteAddr - 원격 컴퓨터 연결의 IP 주소/호스트 이름
- 요청 방법 - HTTP 요청 방법 (GET, POST, PUT, DELETE 등)
- 쿼리 문자열 - URI의 변수
- PostArgs - POST 본문에 전송된 인수입니다. 이 일치 변수를 사용하는 사용자 지정 규칙은 '콘텐츠 유형' 헤더가 '응용 프로그램/x-www-form-urlencoded' 및 '다중 파트/양식 데이터'로 설정된 경우에만 적용됩니다.
- 요청 - 요청의 URI
- 요청헤더 - 요청헤더
- RequestBody - 전체 요청 본문을 포함합니다. 이 일치 변수를 사용하는 사용자 지정 규칙은 '콘텐츠 유형' 헤더가 '응용 프로그램/x-www-form-urlencoded'로 설정된 경우에만 적용됩니다. 
- 요청쿠키 – 요청의 쿠키

### <a name="selector-optional"></a>선택기 [선택 사항]

matchVariable 컬렉션의 필드를 설명 합니다. 예를 들어 matchVariable이 RequestHeaders인 경우 선택기는 *사용자 에이전트* 헤더에 있을 수 있습니다.

### <a name="operator-required"></a>연산자 [필수]

다음 연산자 중 하나여야 합니다.

- IPMatch - 일치 변수가 *RemoteAddr인* 경우에만 사용됩니다.
- 같음 - 입력은 일치 값과 동일합니다.
- 포함
- LessThan
- GreaterThan
- LessThanOrEqual
- GreaterThanOrEqual
- BeginsWith
- EndsWith
- Regex
- 지오매치(미리 보기)

### <a name="negate-condition-optional"></a>부정 조건 [선택 사항]

현재 조건을 무효화합니다.

### <a name="transform-optional"></a>변환 [선택 사항]

일치를 시도하기 전에 수행할 변환 이름이 있는 문자열 목록입니다. 다음과 같은 변환이 될 수 있습니다.

- 소문자
- Trim
- URL디코드
- UrlEncode 
- 제거Nulls
- Html엔티엔티디코드

### <a name="match-values-required"></a>일치 값 [필수]

*OR*'ed로 생각할 수 있는 일치하는 값 목록입니다. 예를 들어 IP 주소 또는 다른 문자열일 수 있습니다. 값 형식은 이전 연산자에 따라 다릅니다.

### <a name="action-required"></a>조치 [필수]

- 허용 - 다른 모든 규칙을 건너뛰고 트랜잭션을 승인합니다. 지정된 요청이 허용 목록에 추가되고 일치하면 요청이 추가 평가를 중지하고 백 엔드 풀로 전송됩니다. 허용 목록에 있는 규칙은 추가 사용자 지정 규칙 또는 관리되는 규칙에 대해 평가되지 않습니다.
- 차단 - *SecDefaultAction(감지/방지* 모드)을 기반으로 트랜잭션을 차단합니다. 허용 작업과 마찬가지로 요청이 평가되고 차단 목록에 추가되면 평가가 중지되고 요청이 차단됩니다. 그 이후의 요청은 동일한 조건을 충족하지 않으며 평가되지 않으며 차단됩니다. 
- 로그 - 규칙이 로그에 쓸 수 있지만 나머지 규칙은 평가를 위해 실행되도록 할 수 있습니다. 다른 사용자 지정 규칙은 우선 순위순으로 평가되고 관리되는 규칙이 뒤따릅니다.

## <a name="geomatch-custom-rules-preview"></a>지오매치 사용자 지정 규칙(미리 보기)

사용자 지정 규칙을 사용하면 응용 프로그램 및 보안 정책의 정확한 요구 사항에 맞게 맞춤형 규칙을 만들 수 있습니다. 국가/지역별로 웹 응용 프로그램에 대한 액세스를 제한할 수 있습니다. 자세한 내용은 [지오매치 사용자 지정 규칙(미리 보기)을](geomatch-custom-rules.md)참조하십시오.

## <a name="next-steps"></a>다음 단계

사용자 지정 규칙에 대해 알아보려면 [사용자 지정 규칙을 직접 만듭니다.](create-custom-waf-rules.md)
