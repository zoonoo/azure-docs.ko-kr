---
title: Azure 전면 도어 용 웹 응용 프로그램 방화벽 사용자 지정 규칙
description: 악의적인 공격 으로부터 웹 응용 프로그램을 보호 하는 WAF (웹 응용 프로그램 방화벽) 사용자 지정 규칙을 사용 하는 방법에 대해 알아봅니다.
author: vhorne
ms.service: web-application-firewall
ms.topic: article
services: web-application-firewall
ms.date: 09/05/2019
ms.author: victorh
ms.openlocfilehash: 158bfe30bf48ee420be8efb9ff32fff0e555d9e7
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "79475827"
---
#  <a name="custom-rules-for-web-application-firewall-with-azure-front-door"></a>Azure Front 도어를 사용 하는 웹 응용 프로그램 방화벽에 대 한 사용자 지정 규칙

전면 도어를 사용 하는 Azure WAF (웹 응용 프로그램 방화벽)를 사용 하면 정의 된 조건에 따라 웹 응용 프로그램에 대 한 액세스를 제어할 수 있습니다. 사용자 지정 WAF 규칙은 우선 순위 번호, 규칙 유형, 일치 조건 및 동작으로 구성 됩니다. 사용자 지정 규칙에는 두 가지 유형, 즉 일치 규칙 및 요율 제한 규칙이 있습니다. 일치 규칙은 일치 조건 집합에 따라 액세스를 제어 하는 반면, 속도 제한 규칙은 일치 조건 및 들어오는 요청 비율에 따라 액세스를 제어 합니다. 사용자 지정 규칙을 사용 하지 않도록 설정 하 여 평가를 방지할 수 있지만 여전히 구성을 유지 합니다. 

## <a name="priority-match-conditions-and-action-types"></a>우선 순위, 일치 조건 및 작업 유형

우선 순위 번호, 규칙 유형, 일치 조건 배열 및 동작을 정의 하는 사용자 지정 WAf 규칙을 사용 하 여 액세스를 제어할 수 있습니다. 

- **Priority:** waf 규칙의 계산 순서를 설명 하는 고유한 정수입니다. 우선 순위 값이 낮은 규칙은 값이 높은 규칙 보다 먼저 평가 됩니다. 우선 순위 번호는 모든 사용자 지정 규칙에서 고유 해야 합니다.

- **작업:** waf 규칙이 일치 하는 경우 요청을 라우팅하는 방법을 정의 합니다. 다음 작업 중 하나를 선택 하 여 요청이 사용자 지정 규칙과 일치 하는 경우에 적용할 수 있습니다.

    - *Allow* -waf는 퀘스트를 백 엔드에 전달 하 고 waf 로그에 항목을 기록 하 고 종료 합니다.
    - *차단* 요청이 차단 되 고 waf는 요청을 백 엔드에 전달 하지 않고 클라이언트에 응답을 보냅니다. WAF는 WAF 로그에 항목을 기록 합니다.
    - *Log* -WAF는 waf 로그에 항목을 기록 하 고 계속 해 서 다음 규칙을 평가 합니다.
    - *리디렉션* -waf 요청을 지정 된 URI로 리디렉션하고 waf 로그에 항목을 기록 하 고 종료 합니다.

- **일치 조건:** 일치 변수, 연산자 및 일치 값을 정의 합니다. 각 규칙에는 여러 일치 조건이 포함 될 수 있습니다. 일치 조건은 지리적 위치, CIDR (클라이언트 IP 주소), 크기 또는 문자열 일치를 기반으로 할 수 있습니다. 문자열 일치는 일치 변수 목록과 다를 수 있습니다.
  - **일치 변수:**
    - RequestMethod
    - QueryString
    - PostArgs
    - RequestUri
    - RequestHeader
    - RequestBody
    - 쿠키
  - **연산자**
    - Any: 일치 하는 규칙이 없는 경우 기본 동작을 정의 하는 데 종종 사용 됩니다. Any는 모두 일치 연산자입니다.
    - 같음
    - 포함
    - LessThan: size 제약 조건
    - GreaterThan: size 제약 조건
    - LessThanOrEqual: size 제약 조건
    - GreaterThanOrEqual: size 제약 조건
    - BeginsWith
    - EndsWith
    - Regex
  
  - **Regex** 는 다음 작업을 지원 하지 않습니다. 
    - 하위 식 역참조 및 캡처
    - 임의의 너비가 0 인 어설션
    - 서브루틴 참조 및 재귀 패턴
    - 조건부 패턴
    - 역 추적 컨트롤 동사
    - \C 단일 바이트 지시문
    - \R 줄 바꿈 일치 지시어
    - 일치 다시 설정 지시어의 \K 시작
    - 설명선 및 포함 된 코드
    - 원자성 그룹화 및 possessive 수량자

  - **부정 [선택 사항]:** 조건 결과가 부정 되어야 하는 경우 *부정* 조건을 true로 설정할 수 있습니다.
      
  - **Transform [선택 사항]:** 일치를 시도 하기 전에 변환 이름이 포함 된 문자열의 목록입니다. 이러한 변환은 다음과 같을 수 있습니다.
     - 대문자 
     - 소문자
     - Trim
     - RemoveNulls
     - UrlDecode
     - UrlEncode
     
   - **일치 값:** 지원 되는 HTTP 요청 메서드 값은 다음과 같습니다.
     - GET
     - POST
     - PUT
     - HEAD
     - Delete
     - LOCK
     - UNLOCK
     - 프로필
     - OPTIONS
     - PROPFIND
     - REQUESTS
     - MKCOL
     - 복사
     - MOVE

## <a name="examples"></a>예

### <a name="waf-custom-rules-example-based-on-http-parameters"></a>Http 매개 변수를 기반으로 하는 WAF 사용자 지정 규칙 예제

다음은 두 개의 일치 조건이 있는 사용자 지정 규칙의 구성을 보여 주는 예제입니다. 요청은 참조 페이지에 정의 된 대로 지정 된 사이트에서 시작 하 고 쿼리 문자열에는 "password"가 포함 되지 않습니다.

```
# http rules example
{
  "name": "AllowFromTrustedSites",
  "priority": 1,
  "ruleType": "MatchRule",
  "matchConditions": [
    {
      "matchVariable": "RequestHeader",
      "selector": "Referer",
      "operator": "Equal",
      "negateCondition": false,
      "matchValue": [
        "www.mytrustedsites.com/referpage.html"
      ]
    },
    {
      "matchVariable": "QueryString",
      "operator": "Contains",
      "matchValue": [
        "password"
      ],
      "negateCondition": true
    }
  ],
  "action": "Allow",
  "transforms": []
}

```
"PUT" 메서드 차단에 대 한 예제 구성은 다음과 같이 표시 됩니다.

``` 
# http Request Method custom rules
{
  "name": "BlockPUT",
  "priority": 2,
  "ruleType": "MatchRule",
  "matchConditions": [
    {
      "matchVariable": "RequestMethod",
      "selector": null,
      "operator": "Equal",
      "negateCondition": false,
      "matchValue": [
        "PUT"
      ]
    }
  ],
  "action": "Block",
  "transforms": []
}
```

### <a name="size-constraint"></a>크기 제약 조건

들어오는 요청의 일부에 대해 크기 제약 조건을 지정 하는 사용자 지정 규칙을 작성할 수 있습니다. 예를 들어 아래 규칙은 100 자 보다 긴 Url을 차단 합니다.

```
# http parameters size constraint
{
  "name": "URLOver100",
  "priority": 5,
  "ruleType": "MatchRule",
  "matchConditions": [
    {
      "matchVariable": "RequestUri",
      "selector": null,
      "operator": "GreaterThanOrEqual",
      "negateCondition": false,
      "matchValue": [
        "100"
      ]
    }
  ],
  "action": "Block",
  "transforms": []
}
```

## <a name="next-steps"></a>다음 단계
- [Azure PowerShell를 사용 하 여 웹 응용 프로그램 방화벽 정책 구성](waf-front-door-custom-rules-powershell.md) 
- [프런트 도어를 사용 하는 웹 응용 프로그램 방화벽](afds-overview.md) 에 대 한 자세한 정보
- [Front Door를 만드는](../../frontdoor/quickstart-create-front-door.md) 방법을 알아봅니다.

