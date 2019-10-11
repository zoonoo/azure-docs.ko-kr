---
title: Azure WAF (웹 응용 프로그램 방화벽) v2 사용자 지정 규칙
description: 이 문서에서는 Azure 애플리케이션 Gateway의 WAF (웹 응용 프로그램 방화벽) v2 사용자 지정 규칙에 대 한 개요를 제공 합니다.
services: application-gateway
ms.topic: article
author: vhorne
ms.service: application-gateway
ms.date: 6/18/2019
ms.author: victorh
ms.openlocfilehash: 8ae5c9b6b52ea13e3d0981664e8c920cc5b47a01
ms.sourcegitcommit: b4665f444dcafccd74415fb6cc3d3b65746a1a31
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2019
ms.locfileid: "72263552"
---
# <a name="overview-custom-rules-for-web-application-firewall-v2"></a>개요: 웹 응용 프로그램 방화벽 v2의 사용자 지정 규칙

Azure 애플리케이션 게이트웨이 WAF (웹 응용 프로그램 방화벽) v2는 다양 한 유형의 공격 으로부터 보호를 제공 하는 미리 구성 된 플랫폼 관리 규칙 집합과 함께 제공 됩니다. 이러한 공격에는 사이트 간 스크립팅, SQL 삽입 및 기타 기능이 포함 됩니다. WAF 관리자 인 경우 사용자 고유의 규칙을 작성 하 여 핵심 규칙 집합 규칙을 강화할 수 있습니다. 규칙은 일치 조건에 따라 요청 된 트래픽을 차단 하거나 허용할 수 있습니다.

사용자 지정 규칙을 사용 하면 WAF를 통해 전달 되는 각 요청에 대해 평가 되는 사용자 고유의 규칙을 만들 수 있습니다. 이러한 규칙은 관리 되는 규칙 집합의 나머지 규칙 보다 높은 우선 순위를 보유 합니다. 사용자 지정 규칙에는 규칙 이름, 규칙 우선 순위 및 일치 조건의 배열이 포함 되어 있습니다. 이러한 조건이 충족 되 면 허용 또는 차단 작업을 수행 합니다.

예를 들어 192.168.5.4/24 범위의 IP 주소에서 모든 요청을 차단 하는 규칙을 만들 수 있습니다. 이 규칙에서 연산자는 *Ipmatch*이 고 *MATCHVALUES* 는 IP 주소 범위 (192.168.5.4/24) 이며 *작업* 은 트래픽을 차단 하는 것입니다. 또한 규칙의 이름 및 우선 순위를 설정 합니다.

사용자 지정 규칙은 그래야만 논리를 사용 하 여 보안 요구 사항을 해결 하는 고급 규칙을 지원 합니다. 예를 들어 "(조건 1 *및* 조건 2) *or* 조건 3)"는 조건 1 *과* 조건 2가 충족 *되거나* 조건 3이 충족 될 경우 waf는 사용자 지정 규칙에 지정 된 작업을 수행 해야 함을 의미 합니다.

동일한 규칙 내의 서로 다른 일치 조건은 항상 *및*을 사용 하 여 복잡해 집니다. 예를 들어 *및* 를 사용 하는 규칙은 특정 브라우저를 사용 하는 경우에만 특정 IP 주소에서 트래픽을 차단 하도록 지정할 수 있습니다.

두 가지 조건에 대해 *또는* 를 사용 하려는 경우 두 조건이 다른 규칙에 있어야 합니다. 예를 들어 *또는* 를 사용 하는 규칙은 특정 브라우저를 사용 하는 경우 특정 IP 주소에서 트래픽을 차단 하거나 트래픽을 차단 하도록 지정할 수 있습니다.

> [!NOTE]
> WAF 사용자 지정 규칙의 최대 수는 100입니다. Application Gateway 제한에 대 한 자세한 내용은 [Azure 구독 및 서비스 제한, 할당량 및 제약 조건](../azure-subscription-service-limits.md#application-gateway-limits)을 참조 하세요.

정규식은 핵심 규칙 집합에 있는 것 처럼 사용자 지정 규칙 에서도 지원 됩니다. 이러한 규칙의 예는 [사용자 지정 웹 응용 프로그램 방화벽 규칙 만들기 및 사용](create-custom-waf-rules.md)의 "예제 3" 및 "예제 5"를 참조 하십시오.

## <a name="allowing-or-blocking-traffic"></a>트래픽 허용 또는 차단

사용자 지정 규칙을 사용 하 여 트래픽을 허용 하거나 차단 하는 것은 간단 합니다. 예를 들어 IP 주소 범위에서 들어오는 모든 트래픽을 차단할 수 있습니다. 특정 브라우저에서 요청을 가져오는 경우 트래픽을 허용 하는 다른 규칙을 만들 수 있습니다.

항목을 허용 하려면 `-Action` 매개 변수가 **허용**으로 설정 되어 있는지 확인 합니다. 항목을 차단 하려면 다음 코드에 표시 된 것 처럼 `-Action` 매개 변수가 **block**으로 설정 되어 있는지 확인 합니다.

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

위의 `$BlockRule`은 Azure Resource Manager의 다음 사용자 지정 규칙에 매핑됩니다.

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

이 사용자 지정 규칙에는 작업을 수행 하기 위해 충족 해야 하는 이름, 우선 순위, 동작 및 일치 조건의 배열이 포함 되어 있습니다. 사용자 지정 규칙 필드에 대 한 설명은 다음 섹션을 참조 하십시오. 사용자 지정 규칙의 예제는 [사용자 지정 웹 응용 프로그램 방화벽 규칙 만들기 및 사용](create-custom-waf-rules.md)을 참조 하세요.

## <a name="custom-rule-fields"></a>사용자 지정 규칙 필드

### <a name="name-optional"></a>Name (선택 사항)

규칙의 이름입니다. 로그에 이름이 표시 됩니다.

### <a name="priority-required"></a>우선 순위 (필수)

- 우선 순위에 따라 규칙이 평가 되는 순서가 결정 됩니다. 값이 낮을수록 규칙의 이전 계산입니다. 허용 되는 범위는 1에서 100 까지입니다.
- 우선 순위는 모든 사용자 지정 규칙에서 고유 해야 합니다. 우선 순위가 40 인 규칙은 우선 순위가 80 인 규칙 보다 먼저 평가 됩니다.

### <a name="rule-type-required"></a>규칙 유형 (필수)

현재 규칙 유형은 **Matchrule**이어야 합니다.

### <a name="match-variable-required"></a>Match 변수 (필수)

Match 변수는 다음 중 하나 여야 합니다.

- RemoteAddr: 원격 컴퓨터 연결의 IP 주소 또는 호스트 이름
- RequestMethod: HTTP 요청 메서드 (GET, POST, PUT, DELETE 등)입니다.
- 문자열 URI에 있는 변수입니다.
- PostArgs: POST 본문에 전송 되는 인수입니다. 이 일치 변수를 사용 하는 사용자 지정 규칙은 Content-type 헤더가 "application/x-www-form-urlencoded" 및 "multipart/form-data"로 설정 된 경우에만 적용 됩니다.
- RequestUri 요청의 URI입니다.
- RequestHeaders 요청의 헤더입니다.
- RequestBody 전체 요청 본문을 포함 하는 변수입니다. 이 일치 변수를 사용 하는 사용자 지정 규칙은 Content-type 헤더가 "application/x-www-form-urlencoded"로 설정 된 경우에만 적용 됩니다. 
- RequestCookies: 요청에 대 한 쿠키입니다.

### <a name="selector-optional"></a>Selector (선택 사항)

이 선택기는 matchVariable 컬렉션의 필드를 설명 합니다. 예를 들어 matchVariable이 "RequestHeaders" 인 경우 선택기는 사용자 에이전트 헤더에 있을 수 있습니다.

### <a name="operator-required"></a>연산자 (필수)

연산자는 다음 중 하나 여야 합니다.

- IPMatch: 이 연산자는 일치 변수가 *Remoteaddr*인 경우에만 사용 됩니다.
- 같거나 입력이 MatchValue와 동일 합니다.
- 포함
- LessThan
- GreaterThan
- LessThanOrEqual
- GreaterThanOrEqual
- 시작 문자
- EndsWith
- Regex

### <a name="negate-condition-optional"></a>부정 조건 (옵션)

현재 조건을 부정 합니다.

### <a name="transform-optional"></a>Transform (선택 사항)

일치를 시도 하기 전에 완료 해야 하는 변환의 이름을 포함 하는 문자열 목록입니다. 변환은 다음과 같습니다.

- 소문자
- Trim
- UrlDecode
- UrlEncode 
- RemoveNulls
- HtmlEntityDecode

### <a name="match-values-required"></a>일치 값 (필수)

MatchValues 필드는와 일치 하는 값 목록으로, *또는*' ed '로 간주할 수 있습니다. 예를 들어 IP 주소 또는 다른 문자열 값을 사용할 수 있습니다. 값 형식은 이전 연산자에 따라 달라 집니다.

### <a name="action-required"></a>작업 (필수)

작업 필드는 다음 옵션을 제공 합니다. 

- 허용: 트랜잭션을 승인 하 고 모든 후속 규칙을 건너뜁니다. 즉, 지정 된 요청이 허용 목록에 추가 되 고, 일치 하는 경우 요청이 추가 평가를 중지 하 고 백 엔드 풀로 전송 됩니다. 허용 목록에 있는 규칙은 추가 사용자 지정 규칙 또는 관리 규칙에 대해 평가 되지 않습니다.

- 차단: *SecDefaultAction* (검색/방지 모드)를 기반으로 트랜잭션을 차단 합니다. 허용 동작과 마찬가지로 요청이 평가 되 고 차단 목록에 추가 된 후에는 평가가 중지 되 고 요청이 차단 됩니다. 동일한 조건을 충족 하는 후속 요청은 평가 되지 않습니다. 단지 차단 됩니다. 

- 로그 규칙을 로그에 기록 하 고 나머지 규칙은 평가를 위해 실행할 수 있습니다. 후속 사용자 지정 규칙은 우선 순위에 따라 관리 되는 규칙에 따라 평가 됩니다.

## <a name="next-steps"></a>다음 단계

이제 사용자 지정 규칙에 대해 알아보았습니다. 사용자 [지정 규칙을 만들](create-custom-waf-rules.md)수 있습니다.
