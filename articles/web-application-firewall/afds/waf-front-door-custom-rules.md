---
title: Azure Front Door에 대한 웹 애플리케이션 방화벽 사용자 지정 규칙
description: 악의적인 공격으로부터 웹 애플리케이션을 보호하는 WAF(Web Application Firewall) 사용자 지정 규칙을 사용하는 방법을 알아봅니다.
author: vhorne
ms.service: web-application-firewall
ms.topic: article
services: web-application-firewall
ms.date: 09/05/2019
ms.author: victorh
ms.openlocfilehash: 158bfe30bf48ee420be8efb9ff32fff0e555d9e7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "79475827"
---
#  <a name="custom-rules-for-web-application-firewall-with-azure-front-door"></a>Azure Front Door를 사용하는 Web Application Firewall의 사용자 지정 규칙

Front Door를 사용하는 Azure WAF(Web Application Firewall)를 사용하면 정의하는 조건에 따라 웹 애플리케이션에 대한 액세스를 제어할 수 있습니다. 사용자 지정 WAF 규칙은 우선 순위 번호, 규칙 유형, 일치 조건, 작업으로 구성됩니다. 사용자 지정 규칙의 두 가지 유형은 일치 규칙과 속도 제한 규칙입니다. 일치 규칙은 일치 조건 세트에 따라 액세스를 제어하는 반면, 속도 제한 규칙은 일치 조건과 들어오는 요청 속도에 따라 액세스를 제어합니다. 않도록 사용자 지정 규칙을 사용하지 않도록 설정하여 평가를 방지하지만 여전히 구성을 유지할 수 있습니다. 

## <a name="priority-match-conditions-and-action-types"></a>우선 순위, 일치 조건, 작업 유형

우선 순위 번호, 규칙 유형, 일치 조건 배열, 작업을 정의하는 사용자 지정 WAF 규칙을 사용하여 액세스를 제어할 수 있습니다. 

- **우선 순위:** WAF 규칙의 평가 순서를 설명하는 고유한 정수입니다. 우선 순위 값이 낮은 규칙은 값이 높은 규칙보다 먼저 평가됩니다. 우선 순위 번호는 모든 사용자 지정 규칙에서 고유해야 합니다.

- **작업:** WAF 규칙이 일치하는 경우 요청을 라우팅하는 방법을 정의합니다. 다음 작업 중 요청이 사용자 지정 규칙과 일치하는 경우 적용할 작업을 선택할 수 있습니다.

    - ‘허용’ - WAF는 요청을 백 엔드에 전달하고, WAF 로그에 항목을 로그하고, 종료합니다.
    - ‘차단’ - 요청이 차단되고 WAF는 요청을 백 엔드에 전달하지 않고 클라이언트에 응답을 보냅니다. WAF는 WAF 로그에 항목을 로그합니다.
    - ‘로그’ - WAF는 WAF 로그에 항목을 로그하고 계속해서 다음 규칙을 평가합니다.
    - ‘리디렉션’ - WAF는 요청을 지정된 URI로 리디렉션하고, WAF 로그에 항목을 로그하고, 종료합니다.

- **일치 조건:** 일치 변수, 연산자, 일치 값을 정의합니다. 각 규칙에는 여러 일치 조건이 포함될 수 있습니다. 일치 조건은 지리적 위치, 클라이언트 IP 주소(CIDR), 크기 또는 문자열 일치를 기반으로 할 수 있습니다. 문자열 일치는 일치 변수 목록과 다를 수 있습니다.
  - **일치 변수:**
    - RequestMethod
    - QueryString
    - PostArgs
    - RequestUri
    - RequestHeader
    - RequestBody
    - 쿠키
  - **연산자:**
    - Any: 일치하는 규칙이 없는 경우 기본 작업을 정의하는 데 종종 사용됩니다. Any는 모두 일치 연산자입니다.
    - 같음
    - 포함
    - LessThan: 크기 제약 조건
    - GreaterThan: 크기 제약 조건
    - LessThanOrEqual: 크기 제약 조건
    - GreaterThanOrEqual: 크기 제약 조건
    - BeginsWith
    - EndsWith
    - Regex
  
  - **Regex** 는 다음 작업을 지원하지 않습니다. 
    - 역참조 및 하위 식 캡처
    - 임의 0 너비 어설션
    - 서브루틴 참조 및 재귀 패턴
    - 조건부 패턴
    - 제어 동사 역추적
    - \C 단일 바이트 지시문
    - \R 줄 바꿈 일치 지시문
    - \K 일치 시작 초기화 지시문
    - 설명선 및 포함된 코드
    - 원자성 그룹화 및 독점적 한정자

  - **부정[선택 사항]:** 조건 결과가 부정되어야 하는 경우 *negate* 조건을 true로 설정할 수 있습니다.
      
  - **변환[선택 사항]:** 일치를 시도하기 전에 실행할 변환 이름이 포함된 문자열의 목록입니다. 다음 변환일 수 있습니다.
     - 대문자 
     - 소문자
     - 공백 제거
     - RemoveNulls
     - UrlDecode
     - UrlEncode
     
   - **일치 값:** 지원되는 HTTP 요청 메서드 값은 다음과 같습니다.
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
     - PROPPATCH
     - MKCOL
     - 복사
     - MOVE

## <a name="examples"></a>예

### <a name="waf-custom-rules-example-based-on-http-parameters"></a>HTTP 매개 변수를 기반으로 하는 WAF 사용자 지정 규칙 예제

다음은 두 개의 일치 조건이 있는 사용자 지정 규칙의 구성을 보여 주는 예제입니다. 요청은 참조 페이지에 정의된 대로 지정된 사이트에서 발생하고 쿼리 문자열에는 “password”가 포함되지 않습니다.

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
“PUT” 메서드 차단의 예제 구성은 다음과 같이 표시됩니다.

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

들어오는 요청의 일부에서 크기 제약 조건을 지정하는 사용자 지정 규칙을 빌드할 수 있습니다. 예를 들어, 아래 규칙은 100자를 초과하는 URL을 차단합니다.

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
- [Azure PowerShell을 사용하여 Web Application Firewall 정책 구성](waf-front-door-custom-rules-powershell.md) 
- [Front Door를 사용하는 웹 애플리케이션 방화벽](afds-overview.md)에 관해 알아봅니다.
- [Front Door를 만드는](../../frontdoor/quickstart-create-front-door.md) 방법을 알아봅니다.

