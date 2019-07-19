---
title: Azure 전면 도어 용 웹 응용 프로그램 방화벽 사용자 지정 규칙
description: 악의적인 공격 으로부터 웹 응용 프로그램을 보호 하는 WAF (웹 응용 프로그램 방화벽) 사용자 지정 규칙을 사용 하는 방법에 대해 알아봅니다.
author: KumudD
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/07/2019
ms.author: kumud
ms.reviewer: tyao
ms.openlocfilehash: 02b335de7f105d768168d5f798ec9109136d7430
ms.sourcegitcommit: fa45c2bcd1b32bc8dd54a5dc8bc206d2fe23d5fb
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/12/2019
ms.locfileid: "67846272"
---
#  <a name="custom-rules-for-web-application-firewall-with-azure-front-door"></a>Azure Front 도어를 사용 하는 웹 응용 프로그램 방화벽에 대 한 사용자 지정 규칙
Azure WAF (웹 응용 프로그램 방화벽)와 Front 도어 서비스를 사용 하면 정의 된 조건에 따라 웹 응용 프로그램에 대 한 액세스를 제어할 수 있습니다. 사용자 지정 WAF 규칙은 우선 순위 번호, 규칙 유형, 일치 조건 및 동작으로 구성 됩니다. 사용자 지정 규칙에는 두 가지 유형, 즉 일치 규칙 및 요율 제한 규칙이 있습니다. 일치 규칙은 일치 조건에 따라 액세스를 제어 하는 반면, 속도 제한 규칙은 일치 조건 및 들어오는 요청 비율에 따라 액세스를 제어 합니다. 사용자 지정 규칙을 사용 하지 않도록 설정 하 여 평가를 방지할 수 있지만 여전히 구성을 유지 합니다. 이 문서에서는 http 매개 변수를 기반으로 하는 일치 규칙을 설명 합니다.

## <a name="priority-match-conditions-and-action-types"></a>우선 순위, 일치 조건 및 작업 유형
우선 순위 번호, 규칙 유형, 일치 조건 및 동작을 정의 하는 사용자 지정 WAf 규칙을 사용 하 여 액세스를 제어할 수 있습니다. 

- **Priority:** waf 규칙의 계산 순서를 설명 하는 고유한 정수입니다. 값이 낮은 규칙이 높은 값을 가진 규칙 보다 먼저 평가 됩니다.

- **작업:** waf 규칙이 일치 하는 경우 요청을 라우팅하는 방법을 정의 합니다. 다음 작업 중 하나를 선택 하 여 요청이 사용자 지정 규칙과 일치 하는 경우에 적용할 수 있습니다.

    - *Allow* -waf는 퀘스트를 백 엔드에 전달 하 고 waf 로그에 항목을 기록 하 고 종료 합니다.
    - *차단* 요청이 차단 되 고 waf는 요청을 백 엔드에 전달 하지 않고 클라이언트에 응답을 보냅니다. WAF는 WAF 로그에 항목을 기록 합니다.
    - *Log* -WAF는 waf 로그에 항목을 기록 하 고 계속 해 서 다음 규칙을 평가 합니다.
    - *리디렉션* -waf 요청을 지정 된 URI로 리디렉션하고 waf 로그에 항목을 기록 하 고 종료 합니다.

- **일치 조건:** 일치 변수, 연산자 및 일치 값을 정의 합니다. 각 규칙에는 여러 일치 조건이 포함 될 수 있습니다. 일치 조건은 다음 *일치 변수*를 기반으로 할 수 있습니다.
    - RemoteAddr (클라이언트 IP)
    - RequestMethod
    - QueryString
    - PostArgs
    - RequestUri
    - RequestHeader
    - RequestBody

- **연산자:** 목록에는 다음이 포함 됩니다.
    - Any: 일치 하는 규칙이 없는 경우 기본 동작을 정의 하는 데 종종 사용 됩니다. Any는 모두 일치 연산자입니다.
    - IPMatch: RemoteAddr 변수에 대 한 IP 제한 정의
    - GeoMatch: RemoteAddr 변수에 대 한 지역 필터링 정의
    - Equal
    - 포함
    - LessThan: size 제약 조건
    - GreaterThan: size 제약 조건
    - LessThanOrEqual: size 제약 조건
    - GreaterThanOrEqual: size 제약 조건
    - 시작 문자
     - EndsWith

조건 결과가 부정 되어야 하는 경우 *부정* 조건을 true로 설정할 수 있습니다.

*Match value* 는 가능한 일치 값 목록을 정의 합니다.
지원 되는 HTTP 요청 메서드 값은 다음과 같습니다.
- 가져오기
- 올리기
- PUT
- HEAD
- DELETE
- 잠기지
- 잠금을
- PROFILE
- OPTIONS
- PROPFIND
- REQUESTS
- MKCOL
- 복사
- MOVE

## <a name="examples"></a>예

### <a name="waf-custom-rules-example-based-on-http-parameters"></a>Http 매개 변수를 기반으로 하는 WAF 사용자 지정 규칙 예제

다음은 두 개의 일치 조건이 있는 사용자 지정 규칙의 구성을 보여 주는 예제입니다. 요청은 참조 페이지에 정의 된 대로 지정 된 사이트에서 수행 되 고 쿼리 문자열에는 "password"가 포함 되지 않습니다.

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
- [웹 응용 프로그램 방화벽](waf-overview.md) 에 대 한 자세한 정보
- [Front Door를 만드는](quickstart-create-front-door.md) 방법을 알아봅니다.

