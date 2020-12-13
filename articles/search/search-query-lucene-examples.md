---
title: Full Lucene 쿼리 구문 사용
titleSuffix: Azure Cognitive Search
description: Lucene Azure Cognitive Search 서비스에서 유사 항목 검색, 근접 검색, 용어 상승, 정규식 검색 및 와일드 카드 검색을 위한 쿼리 구문입니다.
manager: nitinme
author: HeidiSteen
ms.author: heidist
tags: Lucene query analyzer syntax
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 10/05/2020
ms.openlocfilehash: 406233fd93ca76a683cf9f9a9e857de9099705ef
ms.sourcegitcommit: 1bdcaca5978c3a4929cccbc8dc42fc0c93ca7b30
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/13/2020
ms.locfileid: "97368548"
---
# <a name="use-the-full-lucene-search-syntax-advanced-queries-in-azure-cognitive-search"></a>"Full" Lucene 검색 구문 사용 (Azure Cognitive Search의 고급 쿼리)

Azure Cognitive Search에 대 한 쿼리를 생성 하는 경우 기본 [단순 쿼리 파서](query-simple-syntax.md) 를 [azure Cognitive Search에서 보다 강력한 Lucene 쿼리 파서로](query-lucene-syntax.md) 대체 하 여 특수화 된 고급 쿼리 정의를 작성할 수 있습니다. 

Lucene 파서는 필드 범위 쿼리, 유사 항목 검색, 중 위 및 접미사 와일드 카드 검색, 근접 검색, 용어 상승, 정규식 검색 등의 복잡 한 쿼리 구문을 지원 합니다. 성능이 늘어나면 처리 요구 사항도 늘어나므로 실행 시간이 약간 길어진다고 예상해야 합니다. 이 문서에서는 전체 구문을 기반으로 쿼리 작업을 보여 주는 예제를 단계별로 진행할 수 있습니다.

> [!Note]
> 전체 Lucene 쿼리 구문을 통해 사용하도록 설정된 특수화된 쿼리 구문 대부분이 [텍스트 분석](search-lucene-query-architecture.md#stage-2-lexical-analysis)되지 않으므로 형태소 분석 또는 기본형 분석을 예상한 경우에 당황할 수 있습니다. 어휘 분석은 완전한 용어(용어 쿼리 또는 구 쿼리)에만 수행됩니다. 불완전한 용어가 있는 쿼리 형식(접두사 쿼리, 와일드카드 쿼리, regex 쿼리, 유사 항목 쿼리)은 분석 단계를 건너뛰고 쿼리 트리에 직접 추가됩니다. 부분 쿼리 용어에 대해 수행 되는 유일한 변환은 소문자로입니다. 
>

## <a name="nyc-jobs-examples"></a>NYC 작업 예제

다음 예에서는 [뉴욕 OpenData 이니셔티브의 도시](https://nycopendata.socrata.com/)에서 제공 하는 데이터 집합을 기반으로 하 여 사용 가능한 작업으로 구성 된 [NYC jobs 검색 인덱스](https://azjobsdemo.azurewebsites.net/) 를 활용 합니다. 이 데이터는 최신 또는 완료로 간주되어서는 안 됩니다. 인덱스는 Microsoft에서 제공 하는 샌드박스 서비스에 있습니다. 즉, 이러한 쿼리를 사용해 볼 수 있는 Azure 구독 또는 Azure Cognitive Search 필요 하지 않습니다.

GET 또는 POST에서 HTTP 요청을 발급 하는 데 필요한 작업은 Postman 또는 동등한 도구입니다. 이러한 도구에 익숙하지 않은 경우 [빠른 시작: Azure Cognitive Search REST API 살펴보기](search-get-started-rest.md)를 참조 하세요.

## <a name="set-up-the-request"></a>요청 설정

1. 요청 헤더의 값은 다음과 같아야 합니다.

   | 키 | 값 |
   |-----|-------|
   | 콘텐츠 형식 | `application/json`|
   | api-key  | `252044BE3886FE4A8E3BAA4F595114BB` </br> NYC Jobs 인덱스를 호스트 하는 샌드박스 검색 서비스에 대 한 실제 쿼리 API 키입니다. |

1. 동사를로 설정 **`GET`** 합니다.

1. URL을로 설정 합니다. **`https://azs-playground.search.windows.net/indexes/nycjobs/docs/search=*&api-version=2020-06-30&queryType=full`**

   + 인덱스의 documents 컬렉션에는 검색 가능한 모든 콘텐츠가 포함 됩니다. 요청 헤더에 제공 된 쿼리 API 키는 documents 컬렉션을 대상으로 하는 읽기 작업에만 적용 됩니다.

   + **`$count=true`** 검색 조건과 일치 하는 문서 수를 반환 합니다. 빈 검색 문자열에서 개수는 인덱스의 모든 문서 (NYC 작업의 경우 약 2558)가 됩니다.

   + **`search=*`** null 또는 빈 검색에 해당 하는 지정 되지 않은 쿼리입니다. 특히 유용 하지는 않지만 모든 값을 사용 하 여 인덱스에서 검색할 수 있는 모든 필드를 표시 하는 것이 가장 간단 합니다.

   + **`queryType=full`** 전체 Lucene 분석기를 호출 합니다.

1. 확인 단계에서 다음 요청을 GET에 붙여넣고 **보내기** 를 클릭합니다. 결과는 자세한 JSON 문서로 반환됩니다.

   ```http
   https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2020-06-30&$count=true&search=*&queryType=full
   ```

### <a name="how-to-invoke-full-lucene-parsing"></a>전체 Lucene 구문 분석을 호출하는 방법

**`queryType=full`** 기본 단순 쿼리 구문을 재정의 하는 전체 쿼리 구문을 호출 하려면를 추가 합니다. 이 문서의 모든 예제는 **`queryType=full`** 전체 구문이 Lucene 쿼리 파서에서 처리 됨을 나타내는 검색 매개 변수를 지정 합니다. 

```http
POST /indexes/nycjobs/docs/search?api-version=2020-06-30
{
    "count": true,
    "queryType": "full",
    "search": "*"
}
```

## <a name="example-1-query-scoped-to-a-list-of-fields"></a>예제 1: 필드 목록으로 범위가 지정 된 쿼리

이 첫 번째 예제는 파서 특정적이지는 않지만 Azure에서는 이를 사용하여 첫 번째 기본 쿼리 개념인 포함을 소개하려고 합니다. 이 예에서는 쿼리 실행과 응답을 모두 특정 필드에만 제한 합니다. 도구가 Postman 또는 Search 탐색기인 경우 판독 가능한 JSON 응답을 구성하는 방법을 파악하는 것이 중요합니다. 

이 쿼리는  **`searchFields`** 매개 변수를 통해 **`select`** 응답의 동일한 필드를 지정 하 여의 business_title만을 대상으로 합니다.

```http
POST https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2020-06-30
{
    "count": true,
    "queryType": "full",
    "search": "*",
    "searchFields": "business_title",
    "select": "business_title"
}
```

이 쿼리에 대한 응답은 다음 스크린샷과 비슷하게 표시됩니다.

  ![점수를 사용 하는 postman 샘플 응답](media/search-query-lucene-examples/postman-sample-results.png)

응답에서 검색 점수를 보았을 수 있습니다. 검색은 전체 텍스트 검색이 아니거나 조건이 제공 되지 않았기 때문에 순위가 없으면 **1** 의 균일 점수가 발생 합니다. 빈 검색의 경우 행이 임의의 순서로 반환 됩니다. 실제 조건을 포함하는 경우 검색 점수가 의미 있는 값으로 바뀌는 것을 볼 수 있습니다.

## <a name="example-2-fielded-search"></a>예제 2: 필드 지정 검색

Full Lucene 구문은 특정 필드에 대 한 개별 검색 식의 범위 지정을 지원 합니다. 이 예제에서는 junior 라는 용어가 포함 된 비즈니스 타이틀을 검색 합니다. 및를 사용 하 여 여러 필드를 지정할 수 있습니다.

```http
POST /indexes/nycjobs/docs?api-version=2020-06-30
{
    "count": true,
    "queryType": "full",
    "search": "business_title:(senior NOT junior) AND posting_type:external",
    "searchFields": "business_title, posting_type",
    "select": "business_title, posting_type"
}
```

이 쿼리에 대 한 응답은 다음 스크린샷 (posting_type 표시 되지 않음)과 유사 하 게 표시 됩니다.

  :::image type="content" source="media/search-query-lucene-examples/intrafieldfilter.png" alt-text="Postman 샘플 응답 검색 식" border="false":::

검색 식은 단일 단어, 구 또는 괄호 안의 좀 더 복잡 한 식일 수 있으며 선택적으로 부울 연산자를 사용할 수 있습니다. 몇 가지 예는 다음과 같습니다.

+ `business_title:(senior NOT junior)`
+ `state:("New York" OR "New Jersey")`
+ `business_title:(senior NOT junior) AND posting_type:external`

이 경우 필드에서 두 개의 고유 위치를 검색 하는 경우 처럼 두 문자열을 단일 엔터티로 평가 하려면 여러 문자열을 따옴표로 묶어야 합니다 `state` . 도구에 따라 큰따옴표를 이스케이프 해야 할 수도 있습니다 `\` . 

**FieldName: searchExpression** 에 지정 된 필드는 검색 가능한 필드 여야 합니다. 인덱스 특성을 필드 정의에서 사용 하는 방법에 대 한 자세한 내용은 [인덱스 만들기 (Azure Cognitive Search REST API)](/rest/api/searchservice/create-index) 를 참조 하세요.

> [!NOTE]
> 위의 예에서는 **`searchFields`** 쿼리의 각 부분에 명시적으로 지정 된 필드 이름이 있으므로 매개 변수가 생략 됩니다. 그러나 **`searchFields`** 쿼리에 및 문을 사용 하 여 여러 부분이 있는 경우에도를 사용할 수 있습니다. 예를 들어 쿼리는 `search=business_title:(senior NOT junior) AND external&searchFields=posting_type` `senior NOT junior` 필드에만 일치 하지만 `business_title` 필드와 "external"을 일치 시킵니다 `posting_type` . 에서 제공 하는 필드 이름이 `fieldName:searchExpression` 항상 우선 **`searchFields`** `business_title` 합니다. 즉,이 예제에서를 생략할 수 있습니다 **`searchFields`** .

## <a name="example-3-fuzzy-search"></a>예제 3: 유사 항목 검색

전체 Lucene 구문은 비슷한 구문을 갖는 용어를 일치시키는 유사 항목 검색도 지원합니다. 유사 항목 검색을 수행하려면 편집 거리를 지정하는 0과 2 사이의 값을 선택적 매개 변수로 포함하여 단일 단어의 끝에 물결표`~` 기호를 추가합니다. 예를 들어, `blue~` 또는 `blue~1`은 blue, blues 및 glue를 반환합니다.

```http
POST /indexes/nycjobs/docs?api-version=2020-06-30
{
    "count": true,
    "queryType": "full",
    "search": "business_title:asosiate~",
    "searchFields": "business_title",
    "select": "business_title"
}
```

구는 직접 지원 되지 않지만와 같이 여러 부분으로 구성 된 구의 각 용어에 유사 항목 일치를 지정할 수 있습니다 `search=business_title:asosiate~ AND comm~` .  아래 스크린샷에서는 응답에 *커뮤니티 연결* 에 대 한 일치 항목이 포함 되어 있습니다.

  :::image type="content" source="media/search-query-lucene-examples/fuzzysearch.png" alt-text="유사 항목 검색 응답" border="false":::

> [!Note]
> 유사 항목 쿼리는 [분석](search-lucene-query-architecture.md#stage-2-lexical-analysis)되지 않습니다. 불완전한 용어가 있는 쿼리 형식(접두사 쿼리, 와일드카드 쿼리, regex 쿼리, 유사 항목 쿼리)은 분석 단계를 건너뛰고 쿼리 트리에 직접 추가됩니다. 부분 쿼리 용어에 대해 수행 되는 유일한 변환은 소문자로입니다.
>

## <a name="example-4-proximity-search"></a>예제 4: 근접 검색

근접 검색은 문서에서 서로 근접한 용어를 찾는 데 사용됩니다. 구 끝에 물결표("~") 기호, 그리고 근접 경계를 생성하는 단어 수를 넣습니다. 예를 들어, "hotel airport"~5는 문서에서 서로 5개의 단어 내에서 hotel과 airport라는 용어를 찾게 됩니다.

이 쿼리는 "선임" 및 "분석가" 라는 용어를 검색 합니다. 여기서 각 용어는 둘 이상의 단어로 구분 되며 따옴표는 이스케이프 됩니다 ( `\"` ).

```http
POST /indexes/nycjobs/docs?api-version=2020-06-30
{
    "count": true,
    "queryType": "full",
    "search": "business_title:\"senior analyst\"~1",
    "searchFields": "business_title",
    "select": "business_title"
}
```

이 쿼리에 대 한 응답은 다음 스크린샷 처럼 표시 됩니다. 

  :::image type="content" source="media/search-query-lucene-examples/proximity-before.png" alt-text="근접 쿼리" border="false":::

`~0`"선임 분석가" 라는 용어 사이에 거리 ()를 제거 하 여 다시 시도 합니다. 이전 쿼리에서는 10개 문장이 반환되지만 이번 쿼리에서는 8개 문서이 반환됩니다.

```http
POST /indexes/nycjobs/docs?api-version=2020-06-30
{
    "count": true,
    "queryType": "full",
    "search": "business_title:\"senior analyst\"~0",
    "searchFields": "business_title",
    "select": "business_title"
}
```

## <a name="example-5-term-boosting"></a>예제 5: 용어 상승

용어 상승은 해당 용어가 포함되지 않은 문서와 상대적으로, 상승된 용어가 포함된 경우 문서에 더 높은 순위를 매기는 것을 의미합니다. 용어를 상승 시키려면 `^` 검색 하는 용어의 끝에 상승 계수 (숫자)와 함께 캐럿, 기호를 사용 합니다.

이 “before” 쿼리에서는 *computer analyst* 용어가 포함된 직업을 검색합니다. 여기서 *computer* 와 *analyst* 두 단어가 포함된 결과는 없지만, *computer* 직업은 결과의 맨 위에 나옵니다.

```http
POST /indexes/nycjobs/docs?api-version=2020-06-30
{
    "count": true,
    "queryType": "full",
    "search": "business_title:computer analyst",
    "searchFields": "business_title",
    "select": "business_title"
}
```

"after" 쿼리에서는 검색을 반복합니다. 이번에는 두 단어가 존재하지 않을 경우 용어 *computer* 보다 용어 *analyst* 가 포함된 결과를 상승시킵니다. 사람이 읽을 수 있는 쿼리 버전은 `search=business_title:computer analyst^2` 입니다. Postman에서 작동 가능한 쿼리의 경우 `^2` 는로 인코딩됩니다 `%5E2` .

```http
POST /indexes/nycjobs/docs?api-version=2020-06-30
{
    "count": true,
    "queryType": "full",
    "search": "business_title:computer analyst%5e2",
    "searchFields": "business_title",
    "select": "business_title"
}
```

이 쿼리에 대한 응답은 다음 스크린샷과 비슷하게 표시됩니다.

  :::image type="content" source="media/search-query-lucene-examples/termboostingafter.png" alt-text="after 용어 상승" border="false":::

용어 상승은 평가 프로필은 특정 용어가 아닌 특정 필드를 상승시킨다는 점에서 평가 프로필과는 다릅니다. 다음 예제는 차이점을 설명하는 데 도움이 됩니다.

musicstoreindex 예제에서 **genre** 와 같이, 특정 필드에서 일치 항목을 상승시키는 평가 프로필을 고려해 보세요. 용어 상승은 일부 검색어를 다른 것보다 높게 더 상승시키는 데 사용될 수 있습니다. 예를 들어, "rock^2 electronic"은 **genre** 필드에 검색어가 있는 문서를 인덱스의 다른 검색 가능 필드보다 높게 상승시킵니다. 또한, 용어 상승 값(2)의 결과로 "rock"이라는 검색어가 포함된 문서는 "electronic"이라는 다른 검색어보다 높은 순위로 매겨집니다.

계수 수준을 설정할 때 상승 계수가 높을수록 해당 용어는 다른 검색어에 비해 관련성이 더 높아집니다. 기본적으로, 상승 계수는 1입니다. 상승 계수는 양수이어야 하지만, 1보다 작을 수 있습니다(예: 0.2).

## <a name="example-6-regex"></a>예제 6: 정규식

정규식 검색은 [RegExp 클래스](https://lucene.apache.org/core/6_6_1/core/org/apache/lucene/util/automaton/RegExp.html)에 나와 있는 것처럼 슬래시("/") 사이의 내용에 기반하여 일치 항목을 찾습니다.

```http
POST /indexes/nycjobs/docs?api-version=2020-06-30
{
    "count": true,
    "queryType": "full",
    "search": "business_title:/(Sen|Jun)ior/",
    "searchFields": "business_title",
    "select": "business_title"
}
```

이 쿼리에 대한 응답은 다음 스크린샷과 비슷하게 표시됩니다.

  :::image type="content" source="media/search-query-lucene-examples/regex.png" alt-text="Regex 쿼리" border="false":::

> [!Note]
> 정규식 쿼리는 [분석](./search-lucene-query-architecture.md#stage-2-lexical-analysis)되지 않습니다. 부분 쿼리 용어에 대해 수행 되는 유일한 변환은 소문자로입니다.
>

## <a name="example-7-wildcard-search"></a>예제 7: 와일드카드 검색

일반적으로 다중(\*) 또는 단일(?) 문자 와일드카드 검색에 인식된 구문을 사용할 수 있습니다. Lucene 쿼리 커서는 구가 아닌 단일 용어에 이러한 기호의 사용을 지원합니다.

이 쿼리에서 programming 및 programmer라는 용어가 있는 직함이 포함된 접두사 'prog'가 포함되어 있는 직업을 검색합니다. `*`또는 `?` 기호를 검색의 첫 문자로 사용할 수 없습니다.

```http
POST /indexes/nycjobs/docs?api-version=2020-06-30
{
    "count": true,
    "queryType": "full",
    "search": "business_title:prog*",
    "searchFields": "business_title",
    "select": "business_title"
}
```

이 쿼리에 대한 응답은 다음 스크린샷과 비슷하게 표시됩니다.

  :::image type="content" source="media/search-query-lucene-examples/wildcard.png" alt-text="와일드카드 쿼리" border="false":::

> [!Note]
> 와일드카드 쿼리는 [분석](./search-lucene-query-architecture.md#stage-2-lexical-analysis)되지 않습니다. 부분 쿼리 용어에 대해 수행 되는 유일한 변환은 소문자로입니다.
>

## <a name="next-steps"></a>다음 단계

코드에서 쿼리를 지정 해 보세요. 다음 링크는 Azure Sdk를 사용 하 여 검색 쿼리를 설정 하는 방법을 설명 합니다.

+ [.NET SDK를 사용 하 여 인덱스 쿼리](search-get-started-dotnet.md)
+ [Python SDK를 사용 하 여 인덱스 쿼리](search-get-started-python.md)
+ [JavaScript SDK를 사용 하 여 인덱스 쿼리](search-get-started-javascript.md)

추가 구문 참조, 쿼리 아키텍처 및 예제는 다음 링크에서 찾을 수 있습니다.

+ [고급 쿼리를 작성하기 위한 Lucene 구문 퀴리 예제](search-query-lucene-examples.md)
+ [Azure Cognitive Search의 전체 텍스트 검색 작동 방식](search-lucene-query-architecture.md)
+ [단순 쿼리 구문](query-simple-syntax.md)
+ [Full Lucene 쿼리 구문](query-lucene-syntax.md)
+ [필터 구문](search-query-odata-filter.md)