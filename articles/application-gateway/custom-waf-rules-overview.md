---
title: Azure 웹 응용 프로그램 방화벽 (WAF) 사용자 지정 규칙
description: 이 문서는 Azure Application Gateway에서 사용자 지정 규칙 (WAF) 웹 응용 프로그램 방화벽 개요를 제공합니다.
services: application-gateway
ms.topic: article
author: vhorne
ms.service: application-gateway
ms.date: 6/5/2019
ms.author: victorh
ms.openlocfilehash: d35e1dc96f65e51ab14c4962d1824334cbdb1616
ms.sourcegitcommit: 45e4466eac6cfd6a30da9facd8fe6afba64f6f50
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/07/2019
ms.locfileid: "66752036"
---
# <a name="custom-rules-for-web-application-firewall"></a>웹 응용 프로그램 방화벽에 대 한 사용자 지정 규칙

Azure Application Gateway 웹 응용 프로그램 방화벽 (WAF)는 다양 한 유형의 공격 으로부터 보호를 제공 하는 미리 구성 된, 플랫폼에서 관리 하는 규칙 집합을 사용 하 여 제공 됩니다. 이러한 공격 교차 사이트 스크립팅, SQL 주입 등 포함 됩니다. WAF 관리자 인 경우에 (CR) 규칙을 설정 하는 고유한 규칙 핵심 규칙 확장을 작성 하는 것이 좋습니다. 규칙을 차단 하거나 일치 조건에 따라 요청된 트래픽을 허용 합니다.

사용자 지정 규칙을 사용 하 여 WAF를 통해 전달 되는 각 요청에 대해 계산 되는 사용자 고유의 규칙을 만들 수 있습니다. 이러한 규칙 관리 되는 규칙 집합에서 규칙의 나머지 부분 보다 더 높은 우선 순위를 보유합니다. 사용자 지정 규칙은 규칙 이름, 규칙 우선 순위 및 조건과 일치 하는 배열을 포함 합니다. 이러한 조건이 충족 되는 작업 (허용 또는 차단)에 수행 됩니다.

예를 들어, IP 주소 범위 192.168.5.4/24에서 모든 요청을 차단할 수 있습니다. 이 규칙에서는 연산자가 *IPMatch*는 matchValues IP 주소 범위 (192.168.5.4/24) 이며 차단 동작입니다. 규칙의 이름 및 우선 순위 설정할 수도 있습니다.

사용자 지정 규칙 말고 논리를 사용 하 여 고급 규칙 보안 요구에 해당 주소 수 있도록 지원 합니다. 예를 들어 (조건 1 **하 고** 조건 2) **또는** 조건 3).  이 예제에서는 의미 조건 1 **하 고** 조건 2 충족 되 면 **또는** 3 조건이 충족 되 면 WAF를 사용자 지정 규칙에 지정 된 작업을 수행 해야 합니다.

동일한 규칙 내에서 일치 하는 다양 한 조건을 사용 하 여 항상 중요 **고**입니다. 예를 들어, 특정 IP 주소의 트래픽을 차단할 때 특정 브라우저를 사용 중인 경우에 합니다.

않으려면 **또는** 두 여러 가지 조건에 두 가지 조건 다른 규칙에 있어야 합니다. 특정 브라우저를 사용 하는 경우에 예를 들어, 특정 IP 주소 또는 블록 트래픽이 트래픽과 차단 합니다.

> [!NOTE]
> 최대 WAF 사용자 지정 규칙은 100입니다. Application Gateway 제한에 대 한 자세한 내용은 참조 하세요. [Azure 구독 및 서비스 제한, 할당량 및 제약 조건](../azure-subscription-service-limits.md#application-gateway-limits)합니다.

정규식 에서도 지원 되 사용자 지정 규칙에서 마찬가지로 CRS 규칙 집합입니다. 이러한 예가 예제 3 및 5를 참조 하세요. [사용자 지정 웹 응용 프로그램 방화벽 규칙 만들기 및 사용 하 여](create-custom-waf-rules.md)입니다.

## <a name="allowing-vs-blocking"></a>허용 및 차단

허용 및 차단 트래픽을 사용자 지정 규칙을 사용 하 여 간단 합니다. 예를 들어, 일정 범위의 IP 주소에서 들어오는 모든 트래픽을 차단할 수 있습니다. 특정 브라우저에서 요청 하는 경우 트래픽을 허용 하는 다른 규칙을 만들 수 있습니다.

것을 허용 하려면 있는지를 확인 합니다 `-Action` 매개 변수는 설정 **허용**합니다. 것을 차단 하려면 있는지를 확인 합니다 `-Action` 매개 변수는 설정 **블록**합니다.

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

이전 `$BlockRule` Azure Resource Manager에서 다음 사용자 지정 규칙에 매핑됩니다.

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

이 사용자 지정 규칙 이름, 우선 순위, 작업 및 작업 수행을 위해 충족 해야 하는 조건과 일치 하는 배열을 포함 합니다. 에 대 한 자세한 내용은 이러한 필드는 다음 필드 설명을 참조 하십시오. 예를 들어 사용자 지정 규칙을 확인할 [사용자 지정 웹 응용 프로그램 방화벽 규칙 만들기 및 사용 하 여](create-custom-waf-rules.md)입니다.

## <a name="fields-for-custom-rules"></a>사용자 지정 규칙에 대 한 필드

### <a name="name-optional"></a>이름 [선택 사항]

이 규칙의 이름입니다. 이 이름은 로그에 나타납니다.

### <a name="priority-required"></a>우선 순위 [필수]

- 규칙에서 평가 되는 순서를 결정 합니다. 낮은 값이 고, 규칙의 이전 평가 합니다.
-여야 모든 사용자 지정 규칙 내에서 고유 합니다. 우선 순위가 100 규칙 우선 순위가 200 인 규칙 보다 먼저 평가 됩니다.

### <a name="rule-type-required"></a>규칙 유형 [필수]

현재, 있어야 **MatchRule**합니다.

### <a name="match-variable-required"></a>일치 변수 [필수]

변수 중 하나 여야 합니다.

- RemoteAddr-원격 컴퓨터 연결의 IP 주소/호스트
- RequestMethod – HTTP 요청 메서드 (GET, POST, PUT, DELETE 및 등입니다.)
- QueryString-URI의 변수
- PostArgs – 인수는 POST 본문이 전송
- RequestUri – 요청 URI
- RequestHeaders – 요청 헤더
- RequestBody-요청 본문
- RequestCookies – 요청 쿠키

### <a name="selector-optional"></a>[선택 사항] 선택기

MatchVariable 컬렉션의 필드를 설명합니다. 선택기에 있을 수는 matchVariable RequestHeaders 인 경우에 예를 들어 합니다 *User-agent* 헤더입니다.

### <a name="operator-required"></a>[필수] 연산자

다음 연산자 중 하나 여야 합니다.

- IPMatch-변수 일치 하는 경우에 사용 *RemoteAddr*
- Equals – 입력을 MatchValue 동일
- 포함
- LessThan
- GreaterThan
- LessThanOrEqual
- GreaterThanOrEqual
- 시작 문자
- endsWith
- Regex

### <a name="negate-condition-optional"></a>부정할 조건 [옵션]

현재 상태를 부정합니다.

### <a name="transform-optional"></a>변환 [선택 사항]

일치 항목 앞 작업을 수행 하는 변환의 이름으로 문자열 목록을 시도 됩니다. 다음 변환은 일 수 있습니다.

- 소문자
- Trim
- UrlDecode
- UrlEncode 
- RemoveNulls
- HtmlEntityDecode

### <a name="match-values-required"></a>[필수] 값과 일치

것으로 간주 될 수 있는, 비교할 값 목록을 *또는*' ed 합니다. 예를 들어, IP 주소 또는 기타 문자열 수 있습니다. 값 형식으로 이전 연산자에 따라 달라 집니다.

### <a name="action-required"></a>작업 [필수]

- 허용-모든 후속 규칙 건너뛰기 트랜잭션 권한을 부여 합니다. 즉, 지정된 된 요청 추가 허용 목록와 일치 하면 요청 중지 추가 평가 하 고 백 엔드 풀으로 전송 됩니다. 허용 목록에 있는 규칙은 사용자 지정 규칙 추가 또는 관리 되는 규칙에 대해 평가 되지 않습니다.
- 블록 – 차단를 바탕으로 트랜잭션을 *SecDefaultAction* (검색/방지 모드). 허용 동작 마찬가지로 요청 평가 되 고 차단 목록에 추가 되 면 평가 중지 되 고 요청이 차단 합니다. 후 동일한 조건을 충족 하는 모든 요청 평가 되지 않습니다 하 고만 차단 됩니다. 
- 로그 – 규칙 로그에 쓸 수 있지만 나머지 규칙 평가 대 한 실행 수 있습니다. 후속 사용자 지정 규칙은 다음 관리 되는 규칙이 우선 순위에 따라에서 평가 됩니다.

## <a name="next-steps"></a>다음 단계

사용자 지정 규칙에 대 한 파악 한 후 [사용자 고유의 사용자 지정 규칙을 만들](create-custom-waf-rules.md)합니다.
