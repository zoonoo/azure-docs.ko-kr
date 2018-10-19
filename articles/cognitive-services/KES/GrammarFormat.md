---
title: 문법 형식 - Knowledge Exploration Service API
titlesuffix: Azure Cognitive Services
description: KES(Knowledge Exploration Service) API의 문법 형식에 대해 알아봅니다.
services: cognitive-services
author: bojunehsu
manager: cgronlun
ms.service: cognitive-services
ms.component: knowledge-exploration
ms.topic: conceptual
ms.date: 03/26/2016
ms.author: paulhsu
ms.openlocfilehash: 4b4010152622cd9a1d8111ac92dd1960e78d4601
ms.sourcegitcommit: f10653b10c2ad745f446b54a31664b7d9f9253fe
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/18/2018
ms.locfileid: "46125156"
---
# <a name="grammar-format"></a>문법 형식

문법은 서비스에서 해석할 수 있는 가중치가 적용된 자연어 쿼리 집합 및 이러한 자연어 쿼리가 의미 체계 쿼리 식으로 변환하는 방법을 지정하는 XML 파일입니다.  문법 구문은 데이터 인덱스 통합 및 의미 체계 함수를 지원하는 확장과 함께 [SRGS](http://www.w3.org/TR/speech-grammar/) 기반의 음성 인식 문법에 대한 W3C 표준입니다.

다음은 문법에 사용할 수 있는 각 구문 요소를 설명합니다.  컨텍스트에서 이러한 요소의 사용을 보여주는 완전한 문법은 [이 예제](#example)를 참조하세요.

### <a name="grammar-element"></a>문법 요소

`grammar` 요소는 문법 사양 XML에서 최상위 요소입니다.  필수 `root` 특성은 문법의 시작 지점을 정의하는 루트 규칙의 이름을 지정합니다.

```xml
<grammar root="GetPapers">
```

### <a name="import-element"></a>요소 가져오기

`import` 요소는 외부 파일에서 특성 참조를 활성화하는 스키마 정의를 가져옵니다. 요소는 최상위 `grammar` 요소의 자식이어야 하며 모든 `attrref` 요소 앞에 나타납니다. 필수 `schema` 특성은 문법 XML 파일과 같은 디렉터리에 있는 스키마 파일의 이름을 지정합니다. 필수 `name` 요소는 후속 `attrref` 요소가 이 스키마 내에서 정의된 특성을 참조할 때 사용하는 스키마 별칭을 지정합니다.

```xml
  <import schema="academic.schema" name="academic"/>
```

### <a name="rule-element"></a>규칙 요소

`rule` 요소는 시스템에서 해석할 수 있는 쿼리 식의 집합을 지정하는 문법 규칙, 구조적 단위를 정의합니다.  요소는 최상위 `grammar` 요소의 자식이어야 합니다.  필수 `id` 특성은 `grammar` 또는 `ruleref` 요소에서 참조되는 규칙의 이름을 지정합니다.

`rule` 요소는 법적 확장의 집합을 정의합니다.  토큰 텍스트는 입력 쿼리에 대해 직접 일치합니다.  `item` 요소는 반복을 지정하고 해석 확률을 변경합니다.  `one-of` 요소는 대체 옵션을 나타냅니다.  `ruleref` 요소는 간단한 것에서 더욱 복잡한 확장의 생성을 활성화합니다.  `attrref` 요소는 인덱스에서 특성 값에 대한 일치를 허용합니다.  `tag` 요소는 해석의 의미 체계를 지정하고 해석 확률을 변경할 수 있습니다.

```xml
<rule id="GetPapers">...</rule>
```

### <a name="example-element"></a>예제 요소

선택적 `example` 요소는 포함되는 `rule` 정의에 의해 수락될 수 있는 예제 구를 지정합니다.  설명서 및/또는 자동화된 테스트에 사용할 수 있습니다.

```xml
<example>papers about machine learning by michael jordan</example>
```

### <a name="item-element"></a>항목 요소

`item` 요소는 문법 구문의 시퀀스를 그룹화합니다.  확장 시퀀스의 반복을 나타내거나 `one-of` 요소와 함께 대안을 지정하는 데 사용할 수 있습니다.

`item` 요소가 `one-of` 요소의 자식이 아닌 경우 `repeat` 특성을 개수 값에 할당하여 포함된 시퀀스의 반복을 지정할 수 있습니다.  "*n*"의 개수 값(여기서 *n*은 정수)은 시퀀스가 정확하게 *n*번 발생해야 함을 나타냅니다.  "*m*-*n*"의 개수 값은 시퀀스가 *m*과 *n*번 사이에서 나타나도록 허용합니다.  "*m*-"의 개수 값은 시퀀스가 *m*번 이상 나타나야 함을 지정합니다.  최솟값을 초과하는 각 추가 반복에 대한 해석 확률을 변경하는 데 선택적 `repeat-logprob` 특성을 사용할 수 있습니다.

```xml
<item repeat="1-" repeat-logprob="-10">...</item>
```

`item` 요소가 `one-of` 요소의 자식으로 나타나는 경우 확장 대체의 집합을 지정합니다.  이 사용에서 선택적 `logprob` 특성은 다른 선택 항목 중 상대 로그 확률을 지정합니다.  0~1 사이의 확률 *p*가 있을 때 이에 해당하는 로그 확률을 log(*p*)로 계산할 수 있으며, 여기서 log()는 자연 로그 함수입니다.  지정되지 않는 경우 `logprob`는 기본값을 0으로 지정하고, 해석 확률을 변경하지 않습니다.  로그 확률은 항상 음수 부동 소수점 값 또는 0입니다.

```xml
<one-of>
  <item>by</item>
  <item logprob="-0.5">written by</item>
  <item logprob="-1">authored by</item>
</one-of>
```

### <a name="one-of-element"></a>요소 중 하나

`one-of` 요소는 자식 `item` 요소의 하나 중에서 대체 확장을 지정합니다.  `item` 요소만 `one-of` 요소 안에 나타날 수 있습니다.  다른 선택 항목 중 상대 확률은 각 자식 `item`의 `logprob` 특성을 통해 지정될 수 있습니다.

```xml
<one-of>
  <item>by</item>
  <item logprob="-0.5">written by</item>
  <item logprob="-1">authored by</item>
</one-of>
```

### <a name="ruleref-element"></a>ruleref 요소

`ruleref` 요소는 다른 `rule` 요소에 대한 참조를 통해 유효한 확장을 지정합니다.  `ruleref` 요소의 사용을 통해 더 간단한 규칙에서 더 복잡한 식을 작성할 수 있습니다.  필수 `uri` 특성은 구문 "#*rulename*"을 사용하는 참조되는 `rule`의 이름을 나타냅니다.  참조되는 규칙의 의미 체계 출력을 캡처하려면 선택적 `name` 특성을 사용하여 의미 체계 출력이 할당되는 변수의 이름을 지정합니다.
 
```xml
<ruleref uri="#GetPaperYear" name="year"/>
```

### <a name="attrref-element"></a>attrref 요소

`attrref` 요소는 인덱스에서 관찰되는 특성 값에 대한 일치를 허용하여 인덱스 특성을 참조합니다.  필수 `uri` 특성은 구문 "*schemaName*#*attrName*"을 사용하여 인덱스 스키마 이름 및 특성 이름을 지정합니다.  *schemaName*이라는 스키마를 가져오는 이전 `import` 요소가 있어야 합니다.  특성 이름은 해당 스키마에 정의된 특성의 이름입니다.

사용자 입력 일치 외에도 `attrref` 요소는 또한 입력 값과 일치하는 인덱스에서 개체의 하위 집합을 선택하는 출력으로 구조화된 쿼리 개체를 반환합니다.  선택적 `name` 특성을 사용하여 쿼리 개체 출력이 저장되어야 하는 변수의 이름을 지정합니다.  쿼리 개체는 더욱 복잡한 식을 형성하도록 다른 쿼리 개체와 구성될 수 있습니다.  자세한 내용은 [의미 해석](SemanticInterpretation.md)을 참조하세요.  

```xml
<attrref uri="academic#Keyword" name="keyword"/>
```

#### <a name="query-completion"></a>쿼리 완료

부분 사용자 쿼리를 해석할 때 쿼리 완료를 지원하려면 각 참조된 특성은 스키마 정의에서 작업으로 "starts_with"를 포함해야 합니다.  사용자 쿼리 접두사를 지정하여 `attrref`는 접두사를 완료하는 인덱스의 모든 값과 일치하고 문법의 별도 해석으로 각 전체 값을 산출합니다.  

예제:
* 쿼리 접두사 "dat"에 대해 `<attrref uri="academic#Keyword" name="keyword"/>`를 일치시키면 "데이터베이스"에 대한 문서에 대해 특정 해석을, "데이터 마이닝"에 대한 문서에 대해 특정 해석 등을 생성합니다.
* 쿼리 접두사 "200"에 대해 `<attrref uri="academic#Year" name="year"/>`를 일치시키면 "2000"의 문서에 대해 특정 해석을, "2001"의 문서에 대해 특정 해석 등을 생성합니다.

#### <a name="matching-operations"></a>작업 일치

정확한 일치 외에도 특성 유형 선택은 선택적 `op` 특성을 통한 접두사 및 불균등 일치를 지원합니다.  인덱스의 개체에 일치하는 값이 없는 경우 문법 경로는 차단되고 서비스는 이 문법 경로를 통해 통과하는 모든 해석을 생성하지 않습니다.   `op` 특성은 기본값을 "eq"로 지정합니다.

```xml
in <attrref uri="academic#Year" name="year"/>
before <attrref uri="academic#Year" op="lt" name="year"/
```

다음 테이블은 각 특성 유형에 대한 지원되는 `op` 값을 나열합니다.  해당 사용은 해당 인덱스 작업을 스키마 특성 정의에 포함시켜야 합니다.

| 특성 유형 | Op 값 | 설명 | 인덱스 작업
|----|----|----|----|
| 문자열 | eq | 문자열 정확한 일치 | equals |
| 문자열 | starts_with | 문자열 접두사 일치 | starts_with |
| Int32, Int64, Double | eq |  숫자 동등 일치 | equals |
| Int32, Int64, Double | lt, le, gt, ge | 숫자 불균등 일치(<, <=, >, >=) | is_between |
| Int32, Int64, Double | starts_with | 10진수 표기법으로 값의 접두사 일치 | starts_with |

예제:
* `<attrref uri="academic#Year" op="lt" name="year"/>`는 입력 문자열 "2000"과 일치하고 2000년 전에 게시된 모든 문서를 단독으로 반환합니다.
* `<attrref uri="academic#Year" op="lt" name="year"/>`는 20년 이전에 게시된 인덱스에 문서가 없으므로 입력 문자열 "20"과 일치하지 않습니다.
* `<attrref uri="academic#Keyword" op="starts_with" name="keyword"/>`는 입력 문자열 "dat"와 일치하고 "데이터베이스", "데이터 마이닝" 등에 대한 단일 해석 문서에서 반환합니다.  이는 드문 사용 사례입니다.
* `<attrref uri="academic#Year" op="starts_with" name="year"/>`는 입력 문자열 "20"과 일치하고 200-299, 2000-2999 등에 게시된 단일 해석 문서에서 반환합니다.  이는 드문 사용 사례입니다.

### <a name="tag-element"></a>태그 요소

`tag` 요소는 문법을 통한 경로를 해석하는 방법을 지정합니다.  세미콜론으로 종료된 명령문의 시퀀스를 포함합니다.  명령문은 다른 변수로의 변수 또는 리터럴의 할당일 수 있습니다.  또한 0개 이상의 매개 변수가 포함된 함수의 출력을 변수에 할당할 수 있습니다.  각 함수 매개 변수는 리터럴 또는 변수를 사용하여 지정될 수 있습니다.  함수가 어떠한 출력도 반환하지 않는 경우 할당이 생략됩니다.  변수 범위는 포함되는 규칙에 로컬입니다.

```xml
<tag>x = 1; y = x;</tag>
<tag>q = All(); q = And(q, q2);</tag>
<tag>AssertEquals(x, 1);</tag>
```

문법의 각 `rule`에는 규칙의 의미 체계 출력을 나타내는 "out"이라는 미리 정의된 변수가 있습니다.  해당 값은 사용자 쿼리 입력과 일치하는 `rule`을 통한 경로로 통과되는 각 의미 체계 명령문을 평가하여 계산됩니다.  평가의 끝에 "out" 변수에 할당된 값은 규칙의 의미 체계 출력입니다.  문법에 대한 사용자 쿼리 해석의 의미 체계 출력은 루트 규칙의 의미 체계 출력입니다.

일부 명령문은 가법 로그 확률 오프셋을 도입하여 해석 경로의 확률을 변경할 수 있습니다.  일부 명령문은 지정된 조건이 충족되지 않는 경우 해석을 완전히 거부할 수 있습니다.

지원되는 의미 체계 함수의 목록은 [의미 체계 함수](SemanticInterpretation.md#semantic-functions)를 참조하세요.

## <a name="interpretation-probability"></a>해석 확률

문법을 통한 해석 경로의 확률은 모든 `<item>` 요소의 누적 로그 확률 및 그에 따라 발생하는 의미 체계 함수입니다.  특정 입력 시퀀스와 일치하는 상대 가능성을 설명합니다.

0~1 사이의 확률 *p*가 있을 때 이에 해당하는 로그 확률을 log(*p*)로 계산할 수 있으며, 여기서 log()는 자연 로그 함수입니다.  로그 확률을 사용하여 시스템은 간단한 추가를 통해 해석 경로의 공동 확률을 누적할 수 있습니다.  또한 이러한 공동 확률 계산에 일반적인 부동 소수점 언더플로를 방지합니다.  설계적으로 로그 확률은 항상 음수 부동 소수점 값 또는 0이며, 여기서 큰 값은 더 높은 가능성을 나타냅니다.

## <a name="example"></a>예

다음은 문법의 다양한 요소를 보여주는 학술 게시 도메인의 예제 XML입니다.

```xml
<grammar root="GetPapers">

  <!-- Import academic data schema-->
  <import schema="academic.schema" name="academic"/>
  
  <!-- Define root rule-->
  <rule id="GetPapers">
    <example>papers about machine learning by michael jordan</example>
    
    papers
    <tag>
      yearOnce = false;
      isBeyondEndOfQuery = false;
      query = All();
    </tag>
  
    <item repeat="1-" repeat-logprob="-10">
      <!-- Do not complete additional attributes beyond end of query -->
      <tag>AssertEquals(isBeyondEndOfQuery, false);</tag>
        
      <one-of>
        <!-- about <keyword> -->
        <item logprob="-0.5">
          about <attrref uri="academic#Keyword" name="keyword"/>
          <tag>query = And(query, keyword);</tag>
        </item>
        
        <!-- by <authorName> [while at <authorAffiliation>] -->
        <item logprob="-1">
          by <attrref uri="academic#Author.Name" name="authorName"/>
          <tag>authorQuery = authorName;</tag>
          <item repeat="0-1" repeat-logprob="-1.5">
            while at <attrref uri="academic#Author.Affiliation" name="authorAffiliation"/>
            <tag>authorQuery = And(authorQuery, authorAffiliation);</tag>
          </item>
          <tag>
            authorQuery = Composite(authorQuery);
            query = And(query, authorQuery);
          </tag>
        </item>
        
        <!-- written (in|before|after) <year> -->
        <item logprob="-1.5">
          <!-- Allow this grammar path to be traversed only once -->
          <tag>
            AssertEquals(yearOnce, false);
            yearOnce = true;
          </tag>
          <ruleref uri="#GetPaperYear" name="year"/>
          <tag>query = And(query, year);</tag>
        </item>
      </one-of>

      <!-- Determine if current parse position is beyond end of query -->
      <tag>isBeyondEndOfQuery = GetVariable("IsBeyondEndOfQuery", "system");</tag>
    </item>
    <tag>out = query;</tag>
  </rule>
  
  <rule id="GetPaperYear">
    <tag>year = All();</tag>
    written
    <one-of>
      <item>
        in <attrref uri="academic#Year" name="year"/>
      </item>
      <item>
        before
        <one-of>
          <item>[year]</item>
          <item><attrref uri="academic#Year" op="lt" name="year"/></item>
        </one-of>
      </item>
      <item>
        after
        <one-of>
          <item>[year]</item>
          <item><attrref uri="academic#Year" op="gt" name="year"/></item>
        </one-of>
      </item>
    </one-of>
    <tag>out = year;</tag>
  </rule>
</grammar>
```
