---
title: 의미 해석 - Knowledge Exploration Service API
titlesuffix: Azure Cognitive Services
description: KES(Knowledge Exploration Service) API에서 의미 해석을 사용하는 방법에 대해 알아봅니다.
services: cognitive-services
author: bojunehsu
manager: cgronlun
ms.service: cognitive-services
ms.component: knowledge-exploration
ms.topic: conceptual
ms.date: 03/26/2016
ms.author: paulhsu
ms.openlocfilehash: 5fcc7b760b5445e57b41787d8818ef11ed926e6c
ms.sourcegitcommit: f10653b10c2ad745f446b54a31664b7d9f9253fe
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/18/2018
ms.locfileid: "46129355"
---
# <a name="semantic-interpretation"></a>의미 해석

의미 해석은 문법을 통해 해석된 각 경로와 의미 체계 출력을 연결합니다.  특히, 서비스는 해석에 의해 다각화되는 `tag` 요소에서 명령문의 시퀀스를 평가하여 최종 출력을 계산합니다.  

명령문은 다른 변수로의 변수 또는 리터럴의 할당일 수 있습니다.  또한 0개 이상의 매개 변수가 포함된 함수의 출력을 변수에 할당할 수 있습니다.  각 함수 매개 변수는 리터럴 또는 변수를 사용하여 지정될 수 있습니다.  함수가 어떠한 출력도 반환하지 않는 경우 할당이 생략됩니다.

```xml
<tag>x = 1; y = x;</tag>
<tag>q = All(); q = And(q, q2);</tag>
<tag>AssertEquals(x, 1);</tag>
```

변수는 문자로 시작하고 영문자(A-Z), 숫자(0-9) 및 밑줄(\_)로만 구성된 이름 식별자를 사용하여 지정됩니다.  해당 형식은 리터럴 또는 할당된 함수 출력 값에서 암시적으로 유추됩니다. 

다음은 현재 지원되는 데이터 형식의 목록입니다.

|type|설명|예|
|----|----|----|
|문자열|0자 이상의 시퀀스|“Hello World!”<br/>""|
|Bool|부울 값|true<br/>false|
|Int32|부호 있는 32비트 정수.  -2.1e9 ~ 2.1e9|123<br/>-321|
|Int64|부호 있는 64비트 정수. -9.2e18 및 9.2e18|9876543210|
|Double|배정밀도 부동 소수점. 1.7e+/-308(15자리)|123.456789<br/>1.23456789e2|
|Guid|전역적으로 고유한 식별자|“602DD052-CC47-4B23-A16A-26B52D30C05B”|
|쿼리|인덱스에서 데이터 개체의 하위 집합을 지정하는 쿼리 식|All()<br/>And(*q1*, *q2*)|

## <a name="semantic-functions"></a>의미 체계 함수

기본 제공된 의미 체계 함수 집합이 있습니다.  정교한 쿼리의 생성을 허용하고 문법 해석에서 상황에 맞는 제어를 제공합니다.

### <a name="and-function"></a>And 함수

`query = And(query1, query2);`

두 개의 입력된 쿼리의 교집합에서 구성된 쿼리를 반환합니다.

### <a name="or-function"></a>Or 함수

`query = Or(query1, query2);`

두 개의 입력된 쿼리의 합집합에서 구성된 쿼리를 반환합니다.

### <a name="all-function"></a>All 함수

`query = All();`

모든 데이터 개체를 포함하는 쿼리를 반환합니다.

다음 예제에서는 All() 함수를 사용하여 하나 이상의 키워드 교집합을 기준으로 하는 쿼리를 반복적으로 만듭니다.

```
<tag>query = All();</tag>
<item repeat="1-">
  <attrref uri="academic#Keyword" name="keyword">
  <tag>query = And(query, keyword);</tag>
</item>
```

### <a name="none-function"></a>None 함수

`query = None();`

데이터 개체를 포함하지 않는 쿼리를 반환합니다.

다음 예제에서는 None() 함수를 사용하여 하나 이상의 키워드 합집합을 기준으로 하는 쿼리를 반복적으로 만듭니다.

```
<tag>query = None();</tag>
<item repeat="1-">
  <attrref uri="academic#Keyword" name="keyword">
  <tag>query = Or(query, keyword);</tag>
</item>
```

### <a name="query-function"></a>Query 함수

```
query = Query(attrName, value)
query = Query(attrName, value, op)
```

지정된 *op* 작업(기본값은 “eq”)에 따라 *attrName* 특성이 *value* 값에 일치하는 데이터 개체만 포함하는 쿼리를 반환합니다.  일반적으로 `attrref` 요소를 사용하여 일치하는 입력 쿼리 문자열을 기반으로 하는 쿼리를 만듭니다.  값이 지정되어 있거나 다른 수단을 통해 얻은 경우 Query() 함수를 사용하여 이 값과 일치하는 쿼리를 만들 수 있습니다.

다음 예제에서는 Query() 함수를 사용하여 특정 10년 동안 학술 저서를 지정하기 위한 지원을 구현합니다.

```xml
written in the 90s
<tag>
  beginYear = Query("academic#Year", 1990, "ge");
  endYear = Query("academic#Year", 2000, "lt");
  year = And(beginYear, endYear);
</tag>
```

### <a name="composite-function"></a>Composite 함수

`query = Composite(innerQuery);`

*attr* 공통 composite 특성의 하위 특성에 대한 일치로 이루어진 *innerQuery*를 캡슐화하는 쿼리를 반환합니다.  캡슐화는 개별적으로 *innerQuery*를 충족하는 하나 이상의 값을 갖기 위해 일치하는 데이터 개체의 *attr* 복합 특성이 필요합니다.  결합 특성 중 하위 특성의 쿼리는 Composite() 함수를 사용하여 캡슐화되어야 다른 쿼리와 결합할 수 있습니다.

예를 들어 다음 쿼리는 “harry shum”이 “microsoft”에 있는 동안 저술한 학술 저서를 반환합니다.
```
Composite(And(Query("academic#Author.Name", "harry shum"), 
              Query("academic#Author.Affiliation", "microsoft")));
```

반면에 다음 쿼리는 “harry shum”이 저서 중 한 명이며, “microsoft”가 소속 중 하나인 학술 저서를 반환합니다.
```
And(Composite(Query("academic#Author.Name", "harry shum"), 
    Composite(Query("academic#Author.Affiliation", "microsoft")));
```

### <a name="getvariable-function"></a>GetVariable 함수

`value = GetVariable(name, scope);`

지정된 *scope*에서 정의된 *이름* 변수의 값을 반환합니다.  *name*은 문자로 시작하고 영문자(A-Z), 숫자(0-9) 및 밑줄(_)로만 구성된 식별자입니다.  *scope*은 “request” 또는 “system”으로 설정할 수 있습니다.  다양한 범위에서 정의된 변수는 의미 체계 함수의 출력을 통해 정의된 변수를 비롯한 다른 변수와는 다릅니다.

요청 범위 변수는 현재 해석 요청 내 모든 해석에서 공유됩니다.  문법에서 해석에 대한 검색을 제어하는 데 사용할 수 있습니다.

시스템 변수는 서비스에서 미리 정의되며 시스템의 현재 상태에 대한 다양한 통계를 검색하는 데 사용할 수 있습니다.  다음은 현재 지원되는 시스템 변수의 집합입니다.

|이름|type|설명|
|----|----|----|
|IsAtEndOfQuery|Bool|현재 해석이 모든 입력된 쿼리 텍스트에 일치하는 경우 true|
|IsBeyondEndOfQuery|Bool|현재 해석이 입력된 쿼리 텍스트 외에 완료된 항목을 제안한 경우 true|

### <a name="setvariable-function"></a>SetVariable 함수

`SetVariable(name, value, scope);`

지정한 *scope*의 *name* 변수에 *value*를 할당합니다.  *name*은 문자로 시작하고 영문자(A-Z), 숫자(0-9) 및 밑줄(_)로만 구성된 식별자입니다.  현재 *scope*에 대한 유효한 값은 “request”입니다.  설정할 수 있는 시스템 변수가 없습니다.

요청 범위 변수는 현재 해석 요청 내 모든 해석에서 공유됩니다.  문법에서 해석에 대한 검색을 제어하는 데 사용할 수 있습니다.

### <a name="assertequals-function"></a>AssertEquals 함수

`AssertEquals(value1, value2);`

*value1* 및 *value2*가 동일하면 함수는 성공하고 파생 작업이 없습니다.  그렇지 않으면 함수가 실패하고 해석을 거부합니다.

### <a name="assertnotequals-function"></a>AssertNotEquals 함수

`AssertNotEquals(value1, value2);`

*value1* 및 *value2*가 동일하지 않으면 함수는 성공하고 파생 작업이 없습니다.  그렇지 않으면 함수가 실패하고 해석을 거부합니다.


