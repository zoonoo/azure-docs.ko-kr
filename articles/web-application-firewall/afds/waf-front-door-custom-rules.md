---
title: Azure 정문용 웹 응용 프로그램 방화벽 사용자 지정 규칙
description: 웹 응용 프로그램 방화벽(WAF) 사용자 지정 규칙을 사용하여 악의적인 공격으로부터 웹 응용 프로그램을 보호하는 방법을 알아봅니다.
author: vhorne
ms.service: web-application-firewall
ms.topic: article
services: web-application-firewall
ms.date: 09/05/2019
ms.author: victorh
ms.openlocfilehash: 158bfe30bf48ee420be8efb9ff32fff0e555d9e7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79475827"
---
#  <a name="custom-rules-for-web-application-firewall-with-azure-front-door"></a>Azure 정문이 있는 웹 응용 프로그램 방화벽에 대한 사용자 지정 규칙

전면 문이 있는 WAF(Azure Web Application 방화벽)를 사용하면 정의한 조건에 따라 웹 응용 프로그램에 대한 액세스를 제어할 수 있습니다. 사용자 지정 WAF 규칙은 우선 순위 번호, 규칙 유형, 일치 조건 및 작업으로 구성됩니다. 사용자 지정 규칙에는 일치 규칙과 속도 제한 규칙의 두 가지 유형이 있습니다. 일치 규칙은 일치 조건 집합을 기반으로 액세스를 제어하고 속도 제한 규칙은 일치 조건 및 들어오는 요청의 비율에 따라 액세스를 제어합니다. 사용자 지정 규칙을 사용하지 않도록 설정하여 평가되지 않지만 구성을 유지할 수 있습니다. 

## <a name="priority-match-conditions-and-action-types"></a>우선 순위, 일치 조건 및 작업 유형

우선 순위 번호, 규칙 유형, 일치 조건 배열 및 작업을 정의하는 사용자 지정 WAf 규칙을 통해 액세스를 제어할 수 있습니다. 

- **우선 순위:** WAF 규칙의 평가 순서를 설명하는 고유한 정수입니다. 우선 순위가 낮은 규칙은 값이 높은 규칙 보다 앞서 평가됩니다. 우선 순위 번호는 모든 사용자 지정 규칙 중에서 고유해야 합니다.

- **작업:** WAF 규칙이 일치하는 경우 요청을 라우팅하는 방법을 정의합니다. 요청이 사용자 지정 규칙과 일치하는 경우 적용할 아래 작업 중 하나를 선택할 수 있습니다.

    - *허용* - WAF는 퀘스트를 백 엔드로 전달하고 WAF 로그 및 종료에 항목을 기록합니다.
    - *차단* - 요청이 차단되고 WAF는 백 엔드로 요청을 전달하지 않고 클라이언트에 응답을 보냅니다. WAF는 WAF 로그에 항목을 기록합니다.
    - *로그* - WAF는 WAF 로그에 항목을 기록하고 다음 규칙을 계속 평가합니다.
    - *리디렉션* - WAF는 요청을 지정된 URI로 리디렉션하고 WAF 로그에 항목을 기록하고 종료합니다.

- **일치 조건:** 일치 변수, 연산자 및 일치 값을 정의합니다. 각 규칙에는 여러 일치 조건이 포함될 수 있습니다. 일치 조건은 지리적 위치, 클라이언트 IP 주소(CIDR), 크기 또는 문자열 일치를 기반으로 할 수 있습니다. 문자열 일치는 일치 변수 목록과 반대일 수 있습니다.
  - **일치 변수:**
    - 요청 방법
    - QueryString
    - 포스트 아그 (주)
    - 요청우리
    - RequestHeader
    - RequestBody
    - 쿠키
  - **연산자:**
    - Any: 일치하는 규칙이 없는 경우 기본 작업을 정의하는 데 자주 사용됩니다. 모든 연산자는 일치하는 연산자입니다.
    - 같음
    - 포함
    - LessThan: 크기 제약 조건
    - 그레이터Than: 크기 제약 조건
    - LessThanOr같음: 크기 제약 조건
    - 그레이터ThanOrEqual: 크기 제약 조건
    - BeginsWith
    - EndsWith
    - Regex
  
  - **정규식은** 다음 작업을 지원하지 않습니다. 
    - 역참조 및 하위 표현식 캡처
    - 임의의 0 너비 어설션
    - 서브루틴 참조 및 재귀 패턴
    - 조건부 패턴
    - 역추적 제어 동사
    - \C 단일 바이트 지시문
    - \R 줄 바호 일치 지시문
    - 일치 재설정 지시문 \K 시작
    - 콜아웃 및 임베디드 코드
    - 원자 그룹화 및 소유 수량자

  - **부정 [선택 사항]:** 조건의 결과를 무효화해야 하는 경우 *부정 조건을* true로 설정할 수 있습니다.
      
  - **변환 [선택 사항]:** 일치를 시도하기 전에 수행할 변환 이름이 있는 문자열 목록입니다. 다음과 같은 변환이 될 수 있습니다.
     - 대문자 
     - 소문자
     - Trim
     - 제거Nulls
     - URL디코드
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
     - 소품 패치
     - MKCOL
     - 복사
     - MOVE

## <a name="examples"></a>예

### <a name="waf-custom-rules-example-based-on-http-parameters"></a>http 매개 변수를 기반으로 하는 WAF 사용자 지정 규칙 예제

다음은 두 개의 일치 조건이 있는 사용자 지정 규칙의 구성을 보여 주는 예제입니다. 요청은 참조자가 정의한 지정된 사이트에서 온 것이며 쿼리 문자열에는 "암호"가 포함되어 있지 않습니다.

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
"PUT" 메서드를 차단하는 예제 구성은 다음과 같습니다.

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

들어오는 요청의 일부에 크기 제약 조건을 지정하는 사용자 지정 규칙을 작성할 수 있습니다. 예를 들어 아래 규칙에서는 100자 보다 긴 URL을 차단합니다.

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
- [Azure PowerShell을 사용하여 웹 응용 프로그램 방화벽 정책 구성](waf-front-door-custom-rules-powershell.md) 
- [정문으로 웹 애플리케이션 방화벽에](afds-overview.md) 대해 알아보기
- [Front Door를 만드는](../../frontdoor/quickstart-create-front-door.md) 방법을 알아봅니다.

