---
title: Lucene 쿼리 구문
titleSuffix: Azure Cognitive Search
description: 와일드카드, 유사 항목 검색, RegEx 및 기타 고급 쿼리 구문에 대한 Azure Cognitive Search에 사용되는 전체 Lucene 쿼리 구문에 대한 참조입니다.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/08/2021
ms.openlocfilehash: c7cc9ba4cddb21dd68af4a4e3253361e1e3e62fd
ms.sourcegitcommit: 8bca2d622fdce67b07746a2fb5a40c0c644100c6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/09/2021
ms.locfileid: "111754892"
---
# <a name="lucene-query-syntax-in-azure-cognitive-search"></a>Azure Cognitive Search의 Lucene 쿼리 구문

쿼리를 만들 때 특수 쿼리 형식(와일드카드, 유사 항목 검색, 근접 검색, 정규식)에 대해 [Lucene 쿼리 파서](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/classic/package-summary.html) 구문을 선택할 수 있습니다. Lucene 쿼리 파서 구문의 상당 부분이 [Azure Cognitive Search에서 그대로 구현](search-lucene-query-architecture.md)됩니다. *범위 검색* 은 예외이며 이는 **`$filter`** 구문을 통해 생성됩니다. 

Full Lucene 구문을 사용하려면 queryType을 “full”로 설정하고 와일드카드, 유사 항목 검색 또는 전체 구문에서 지원하는 다른 쿼리 형식 중 하나에 대한 쿼리 식 패턴을 전달합니다. REST에서 쿼리 식은 [검색 문서(REST API)](/rest/api/searchservice/search-documents) 요청의 **`search`** 매개 변수에 제공됩니다.

## <a name="example-full-syntax"></a>예(전체 구문)

다음 예는 전체 구문을 사용하여 생성된 검색 요청입니다. 이 예제에서는 현장 검색 및 용어 부스트가 등장합니다. 항목 필드가 “budget”이라는 용어를 포함하는 호텔을 찾습니다. 용어 boost 값 (3)의 결과로 “recently renovated” 문구가 포함된 문서가 더 높은 순위가 됩니다.  

```http
POST /indexes/hotels-sample-index/docs/search?api-version=2020-06-30
{
  "queryType": "full",
  "search": "category:budget AND \"recently renovated\"^3",
  "searchMode": "all"
}
```

이 예제에서는 특정 쿼리 유형과는 관련이 없지만 **`searchMode`** 매개 변수는 관련이 있습니다. 쿼리에서 연산자를 사용할 때마다 일반적으로 `searchMode=all`을 설정하여 *모든* 조건이 일치하는지 확인해야 합니다.  

추가 예제에 대해서는 [Lucene 쿼리 구문 예제](search-query-lucene-examples.md)를 참조하세요. SearchMode를 비롯한 쿼리 요청 및 매개 변수에 대한 자세한 내용은 [문서 검색(REST API)](/rest/api/searchservice/Search-Documents)을 참조하세요.

## <a name="syntax-fundamentals"></a><a name="bkmk_syntax"></a> 구문 기본 사항  

다음 구문 기본 사항은 Lucene 구문을 사용하는 모든 쿼리에 적용됩니다.  

### <a name="operator-evaluation-in-context"></a>컨텍스트의 연산자 평가

배치에 따라 기호가 연산자로 해석될지 아니면 단지 문자열의 다른 문자로 해석될지가 결정됩니다.

예를 들어, Lucene 전체 구문의 경우, 유사 항목 검색 및 근접 검색에 물결표(~)를 사용합니다. 따옴표로 묶은 구 다음에 ~를 배치하면 근접 검색을 호출합니다. 용어 끝에 ~를 배치하면 유사 항목 검색을 호출합니다.

"business~analyst"와 같은 용어 내에서 해당 문자는 연산자로 평가되지 않습니다. 이 경우 쿼리가 단어 또는 구 쿼리라고 가정할 경우 [어휘 분석](search-lucene-query-architecture.md#stage-2-lexical-analysis)을 사용한 [전체 텍스트 검색](search-lucene-query-architecture.md)은 ~를 제거하고 용어 "business~analyst"를 business OR analyst의 두 용어로 분할합니다.

위의 예제는 물결표(~)이지만 모든 연산자에 동일한 원칙이 적용됩니다.

### <a name="escaping-special-characters"></a>특수 문자 이스케이프

검색 텍스트의 일부로 검색 연산자를 사용하려면 단일 백슬래시(`\`)를 접두사로 사용하여 문자를 이스케이프합니다. 예를 들어 `https://`에서 `://`가 쿼리 문자열의 일부인 와일드카드 검색을 하려면 `search=https\:\/\/*`을 지정합니다. 마찬가지로 이스케이프된 전화번호 패턴은 `\+1 \(800\) 642\-7676`과 같을 수 있습니다.

이스케이프가 필요한 특수 문자는 다음과 같습니다.  
`+ - & | ! ( ) { } [ ] ^ " ~ * ? : \ /`  

> [!NOTE]  
> 이스케이프는 토큰을 함께 유지하지만 인덱싱 중에 [어휘 분석](search-lucene-query-architecture.md#stage-2-lexical-analysis)은 제거가 가능합니다. 예를 들어 표준 Lucene 분석기는 하이픈, 공백 및 기타 문자에서 단어를 구분합니다. 쿼리 문자열에 특수 문자가 필요한 경우 인덱스에 해당 문자가 유지되는 분석기가 필요할 수 있습니다. 일부 옵션에는 하이픈을 넣은 단어를 유지하는 Microsoft 자연어 [분석기](index-add-language-analyzers.md) 또는 보다 복잡한 패턴을 위한 사용자 지정 분석기가 포함됩니다. 자세한 내용은 [부분 용어, 패턴 및 특수 문자](search-query-partial-matching.md)를 참조하세요.

### <a name="encoding-unsafe-and-reserved-characters-in-urls"></a>URL에서 안전하지 않은 문자 및 예약된 문자 인코딩

URL에서 안전하지 않은 문자 및 예약된 문자를 모두 인코딩하세요. 예를 들어 ‘#’은 URL에서 조각/앵커 식별자이므로 안전하지 않은 문자입니다. 이 문자는 URL에서 사용할 경우 `%23`으로 인코딩해야 합니다. ‘&’와 ‘=’는 매개 변수를 구분하고 Azure Cognitive Search에서 값을 지정하므로 예약된 문자에 포함됩니다. 자세한 내용은 [RFC1738: URL(Uniform Resource locator)](https://www.ietf.org/rfc/rfc1738.txt)을 참조하세요.

안전하지 않은 문자는 ``" ` < > # % { } | \ ^ ~ [ ]``입니다. 예약된 문자는 `; / ? : @ = + &`입니다.

## <a name="boolean-operators"></a><a name="bkmk_boolean"></a> 부울 연산자

쿼리 문자열에 부울 연산자를 포함하여 일치하는 항목의 정밀도를 높일 수 있습니다. 전체 구문은 문자 연산자 외에도 텍스트 연산자를 지원합니다. 항상 텍스트 부울 연산자(AND, OR, NOT)는 모두 대문자로 지정합니다.

|텍스트 연산자 | 문자 | 예제 | 사용량 |
|--------------|----------- |--------|-------|
| AND | `&`, `+` | `wifi + luxury` | 일치 항목에 포함되어야 하는 용어를 지정합니다. 이 예에서 쿼리 엔진은 `wifi` 및 `luxury`를 모두 포함하는 문서를 찾습니다. 더하기표(`+`)는 필수 용어에 사용됩니다. 예를 들어, `+wifi +luxury`는 두 용어가 단일 문서의 필드에 나타나야 한다고 명시합니다.|
| 또는 | `|` | `wifi | luxury` | 용어 중 하나가 발견될 때 일치하는 항목을 찾습니다. 이 예제에서 쿼리 엔진은 `wifi` 또는 `luxury` 양쪽 모두를 포함하는 문서에 일치하는 항목을 반환합니다. OR은 기본 결합 연산자이므로 `wifi luxury`가 `wifi | luxury`와 동일한 것처럼 생략할 수도 있습니다.|
| NOT | `!`, `-` | `wifi –luxury` | 용어를 제외한 문서에 일치하는 항목을 반환합니다. 예를 들어 `wifi –luxury`는 `wifi` 용어를 포함하지만 `luxury`이 아닌 문서를 검색합니다. <br/><br/>쿼리 요청의 `searchMode` 매개 변수는 NOT 연산자를 사용하는 용어가 쿼리의 다른 용어와 함께 ANDed 또는 ORed 여부를 제어합니다(다른 조건에 `+` 또는 `|` 연산자가 없는 경우). 유효한 값은 `any`나 `all`입니다.  <br/><br/>`searchMode=any`를 사용하는 경우 더 많은 결과를 포함하여 쿼리 재현율을 높이고, 기본값으로 `-`는 “OR NOT”으로 해석됩니다. 예를 들어, `wifi -luxury`는 용어 `wifi`를 포함하는 문서 또는 용어 `luxury`를 포함하지 않는 문서를 검색합니다.  <br/><br/>`searchMode=all`은 더 작은 결과를 포함하여 쿼리의 정밀도를 늘리고, 기본값으로 -는 “AND NOT”으로 해석됩니다. 예를 들어 `wifi -luxury`는 용어 `wifi`를 포함하고 용어 "luxury"를 포함하지 않는 문서를 검색합니다. 이러한 동작이 `-` 연산자의 좀 더 간단한 동작일 것입니다. 따라서 `searchMode=all`을 `searchMode=any` 대신 사용하는 것이 재현율이 아니라 정밀도에 따라 최적화하고자 *하거나* 사용자가 검색에서 `-` 연산자를 자주 사용하는 경우 좋습니다.<br/><br/>`searchMode` 설정을 결정할 때 다양한 애플리케이션의 쿼리에 대한 사용자 상호 작용 패턴을 고려합니다. 정보를 검색하는 사용자는 더 많은 기본 제공 탐색 구조를 포함하는 전자상거래 사이트와는 달리 쿼리에 연산자를 포함할 가능성이 높습니다. |

##  <a name="fielded-search"></a><a name="bkmk_fields"></a> 필드 지정 검색

`fieldName:searchExpression` 구문을 사용하여 필드 지정 검색 작업을 정의할 수 있습니다. 여기서 검색 식은 단일 단어나 구 또는 괄호 안의 보다 복잡한 식입니다. 선택적으로 부울 연산자를 사용할 수 있습니다. 몇 가지 예는 다음과 같습니다.  

- genre:jazz NOT history  

- artists:("Miles Davis" "John Coltrane")

두 문자열이 단일 엔터티로 평가되길 원하는 경우(이 경우에 `artists` 필드에서 두 개의 다른 예술가 검색) 여러 문자열을 인용 부호로 묶어야 합니다.  

`fieldName:searchExpression`에 지정한 필드는 `searchable` 필드여야 합니다.  필드 정의에서 인덱스 특성이 사용되는 방법에 대한 자세한 내용은 [인덱스 만들기](/rest/api/searchservice/create-index)를 참조하세요.  

> [!NOTE]
> 필드 지정 검색 식을 사용할 때 각 필드 지정 검색 식에 명시적으로 지정된 필드 이름이 있으므로 `searchFields` 매개 변수를 사용할 필요는 없습니다. 그러나 일부 부분의 범위가 특정 필드로 지정되고 나머지는 여러 필드에 적용될 수 있는 쿼리를 실행하려는 경우에도 `searchFields` 매개 변수를 사용할 수 있습니다. 예를 들어 `search=genre:jazz NOT history&searchFields=description` 쿼리는 `jazz`와 `genre` 필드에서만 일치하지만 `NOT history`와는 `description` 필드에서 일치합니다. `fieldName:searchExpression`에서 제공하는 필드 이름이 항상 `searchFields` 매개 변수보다 우선 순위를 지닙니다. 따라서 이 예제에서는 `searchFields` 매개 변수에 `genre`를 포함할 필요가 없습니다.

##  <a name="fuzzy-search"></a><a name="bkmk_fuzzy"></a> 유사 항목 검색

유사 항목 검색은 유사한 구성이 있는 일치 항목을 찾아 두 개 이하의 거리 기준을 충족하는 최대 50개 용어로 확장합니다. 자세한 내용은 [유사 항목 검색](search-query-fuzzy.md)을 참조하세요.

유사 항목 검색을 수행하려면 한 단어의 끝에 물결표("~") 기호를 붙입니다. 그리고 선택적으로 편집 거리를 지정하는 0과 2(기본값) 사이의 수를 매개 변수로 붙입니다. 예를 들어, "blue~" 또는 "blue~1"은 "blue", "blues" 및 "glue"를 반환합니다.

유사 항목 검색은 구가 아닌 용어에만 적용할 수 있지만 각 용어에 물결표를 여러 부분으로 구성된 이름이나 구에 추가할 수 있습니다. 예를 들어 “Unviersty~ of~ Wshington~”은 “University of Washington”과 일치합니다.
 
##  <a name="proximity-search"></a><a name="bkmk_proximity"></a> 근접 검색

근접 검색은 문서에서 서로 근접한 용어를 찾는 데 사용됩니다. 구 끝에 물결표("~") 기호, 그리고 근접 경계를 생성하는 단어 수를 넣습니다. 예를 들어, `"hotel airport"~5`는 문서에서 서로 5개의 단어 내에서 “hotel”과 “airport”라는 용어를 찾게 됩니다.  

##  <a name="term-boosting"></a><a name="bkmk_termboost"></a> 용어 상승

용어 상승은 해당 용어가 포함되지 않은 문서와 상대적으로, 상승된 용어가 포함된 경우 문서에 더 높은 순위를 매기는 것을 의미합니다. 이것은 평가 프로필은 특정 용어가 아닌 특정 필드를 상승시킨다는 점에서 평가 프로필과는 다릅니다.  

다음 예제는 차이점을 설명하는 데 도움이 됩니다. [musicstoreindex 예제](index-add-scoring-profiles.md#bkmk_ex)에서 *genre* 와 같이 특정 필드에서 일치 항목을 상승시키는 점수 매기기 프로필을 고려해 보세요. 용어 상승은 일부 검색어를 다른 것보다 높게 더 상승시키는 데 사용될 수 있습니다. 예를 들어, `rock^2 electronic`은 genre 필드에 검색어가 있는 문서를 인덱스의 다른 검색 가능 필드보다 높게 상승시킵니다. 또한, 용어 상승 값(2)의 결과로 *rock* 이라는 검색어가 포함된 문서는 *electronic* 이라는 다른 검색어보다 높은 순위로 매겨집니다.  

 용어를 상승시키려면 검색하려는 용어 끝 부분에 상승 계수(숫자)와 함께 캐럿("^") 기호를 사용합니다. 또한 구를 상승시킬 수도 있습니다. 상승 계수가 높을수록 해당 용어는 다른 검색어에 비해 더 관련성이 높아집니다. 기본적으로, 상승 계수는 1입니다. 상승 계수는 양수여야 하지만, 1 미만일 수도 있습니다(예: 0.20).  

##  <a name="regular-expression-search"></a><a name="bkmk_regex"></a> 정규식 검색  
 정규식 검색은 [RegExp 클래스](https://lucene.apache.org/core/6_6_1/core/org/apache/lucene/util/automaton/RegExp.html)에 설명된 대로 Apache Lucene에서 유효한 패턴을 기준으로 일치하는 항목을 찾습니다. Azure Cognitive Search에서 정규식은 슬래시(`/`)로 묶여 있습니다.

 예를 들어 "motel" 또는 "호텔"를 포함하는 문서를 찾으려면 `/[mh]otel/`을 지정합니다. 정규식 검색은 단일 단어를 기준으로 일치 항목을 찾습니다.

일부 도구 및 언어는 추가 이스케이프 문자 요구 사항을 적용합니다. JSON의 경우 슬래시를 포함하는 문자열은 백슬래시를 사용하여 이스케이프됩니다. “microsoft.com/azure/”는 `search=/.*microsoft.com\/azure\/.*/`이 되어 `search=/.* <string-placeholder>.*/`가 정규식을 설정하며, `microsoft.com\/azure\/`은 이스케이프된 슬래시가 있는 문자열입니다.

##  <a name="wildcard-search"></a><a name="bkmk_wildcard"></a> 와일드카드 검색

일반적으로 다중(`*`) 또는 단일(`?`) 문자 와일드카드 검색에 인식된 구문을 사용할 수 있습니다. Full Lucene 구문은 접두사, 중위 및 접미사 일치를 지원합니다. 

Lucene 쿼리 커서는 구가 아닌 단일 용어에 이러한 기호의 사용을 지원합니다.

| 접미사 형식 | 설명 및 예제 |
|------------|--------------------------|
| 접두사 | 용어 조각은 `*` 또는 `?` 앞에 옵니다.  예를 들어 `search=alpha*`의 쿼리 식은 “영숫자” 또는 “사전순”을 반환합니다. 접두사 일치는 단순 구문 및 전체 구문 모두에서 지원됩니다. |
| suffix | 용어 조각은 구성을 구분하기 위해 슬래시가 포함된 `*` 또는 `?` 뒤에 옵니다. 예를 들어 `search=/.*numeric./`은 "영숫자"를 반환합니다. |
| 중위  | 용어 조각은 `*` 또는 `?`를 묶습니다.  예를 들어 `search=/.non*al./`은 "nonnumerical" 및 "nonsensical"을 반환합니다. |

하나의 식에서 연산자를 결합할 수 있습니다. 예를 들어 `980?2*`는 "98072-1222" 및 "98052-1234"와 일치합니다. 여기서 `?`는 단일(필수) 문자와 일치하고 `*`는 뒤에 오는 임의 길이의 문자와 일치합니다.

접미사 및 중위 일치에는 정규식 슬래시 `/` 구분 기호가 필요합니다. 일반적으로 코드를 작성할 때는 * 또는 ?를 사용할 수 없습니다. 단어의 첫 번째 문자 또는 용어 내의 기호(`/` 제외)입니다. Postman 또는 Azure Portal과 같은 특정 도구에서는 이스케이프가 기본 제공되며 구분 기호 없이 쿼리를 실행할 수 있는 경우가 많습니다.

> [!NOTE]  
> 규칙으로서 패턴 일치는 속도가 느리므로 용어에서 문자 시퀀스에 대한 토큰을 만드는 edge n-gram 토큰화와 같은 대체 메서드를 탐색하는 것이 좋습니다. n-gram 토큰화를 사용하면 인덱스가 더 커지지만 인덱싱 중인 문자열의 길이와 패턴 구성에 따라 쿼리가 더 빠르게 실행될 수 있습니다.
>

### <a name="impact-of-an-analyzer-on-wildcard-queries"></a>분석기가 와일드카드 쿼리에 끼치는 영향

쿼리 구문 분석 중에 접두사, 접미사, 와일드카드 또는 정규식으로 작성된 쿼리는 [어휘 분석](search-lucene-query-architecture.md#stage-2-lexical-analysis)을 우회하여 쿼리 트리에 있는 그대로 전달됩니다. 쿼리에 지정된 형식의 문자열이 인덱스에 포함된 경우에만 일치가 검색됩니다. 대부분의 경우 부분 용어 및 패턴 일치가 성공하도록 문자열 무결성을 유지하는 인덱싱 중에 분석기가 필요합니다. 자세한 내용은 [Azure Cognitive Search 쿼리에서 부분 용어 검색](search-query-partial-matching.md)을 참조하세요.

검색 쿼리 ‘terminat*’에서 ‘terminate’, ‘termination’ 및 ‘terminates’와 같은 용어를 포함하는 결과를 반환하도록 하려는 경우를 가정해봅니다.

en.lucene(영어 Lucene) 분석기를 사용하는 경우 각 용어의 적극적 형태소 분석을 적용합니다. 예를 들어 ‘terminate’, ‘termination’, ‘terminates’는 모두 인덱스의 ‘termi’ 토큰으로 토큰화됩니다. 하지만 와일드카드 또는 유사 항목 검색을 사용하는 쿼리의 용어는 전혀 분석되지 않으므로 ‘terminat*’ 쿼리와 일치하는 결과가 없습니다.

반면에 Microsoft 분석기(이 경우에는 en.microsoft 분석기)는 약간 더 고급형이며 형태소 분석 대신 분류 정리를 사용합니다. 그러므로 생성된 모든 토큰은 유효한 영어 단어여야 합니다. 예를 들어 ‘terminate’, ‘terminates’ 및 ‘termination’은 대부분 인덱스에서 온전하게 유지되며 와일드카드 및 유사 항목 검색에 많은 영향을 주는 시나리오에 적합합니다.

## <a name="scoring-wildcard-and-regex-queries"></a> 와일드카드 및 정규식 쿼리 점수 매기기

Azure Cognitive Search는 텍스트 쿼리에 대해 빈도 기반 점수 매기기([TF IDF](https://en.wikipedia.org/wiki/Tf%E2%80%93idf))를 사용합니다. 그러나 용어 범위가 광범위할 수 있는 와일드카드 및 정규식 쿼리의 경우 순위 오류가 발생하여 더 드물게 나오는 용어가 검색되지 않도록 빈도 계수가 무시됩니다. 모든 일치 항목은 와일드카드 및 정규식에 대한 동일하게 처리됩니다.

## <a name="special-characters"></a>특수 문자

경우에 따라 ‘❤’ 이모지 또는 ‘€’ 기호와 같은 특수 문자를 검색해야 할 수 있습니다. 이 경우 사용하는 분석기에서 해당 문자를 필터링하지 않는지 확인합니다. 표준 분석기는 인덱스에서 제외하고 많은 특수 문자를 건너뜁니다.

특수 문자를 토큰화하는 분석기에는 “공백” 분석기가 포함됩니다. 이 분석기는 공백으로 구분되는 모든 문자 시퀀스를 토큰으로 간주하므로 “❤” 문자열은 토큰으로 간주됩니다. 또한 Microsoft English analyzer(“en.microsoft”)와 같은 언어 분석기는 “€” 문자열을 토큰으로 사용합니다. [분석기를 테스트](/rest/api/searchservice/test-analyzer)하여 지정된 쿼리에 생성되는 토큰을 확인할 수 있습니다.

유니코드 문자를 사용하는 경우 쿼리 URL에서 기호가 제대로 이스케이프되었는지 확인합니다. 예를 들어 “❤”는 이스케이프 시퀀스 `%E2%9D%A4+`를 사용합니다. Postman은 이 변환을 자동으로 수행합니다.  

## <a name="precedence-grouping"></a>우선 순위(그룹화)

괄호를 사용(괄호문 내에 연산자를 포함)하여 하위 쿼리를 만들 수 있습니다. 예를 들어, `motel+(wifi|luxury)`는 용어 motel과 "wifi" 또는 "luxury" 중 하나(또는 둘 다)를 포함하는 문서를 검색합니다.|

필드 그룹화는 유사하지만 그룹화 범위가 단일 필드입니다. 예를 들어, `hotelAmenities:(gym+(wifi|pool))`은 필드 "hotelAmenities"에서 "gym"과 "wifi" 또는 "gym"과 "pool"을 검색합니다.  

## <a name="query-size-limits"></a>쿼리 크기 제한

Azure Cognitive Search로 보낼 수 있는 쿼리의 크기에는 제한이 있습니다. 특히, 최대 1024개 절(AND, OR 등으로 구분된 식)을 사용할 수 있습니다. 또한 한 쿼리의 개별 용어 크기도 약 32KB로 제한됩니다. 애플리케이션이 검색 쿼리를 프로그래밍 방식으로 생성하는 경우 쿼리가 제한 없는 크기로 생성되지 않도록 디자인하는 것이 좋습니다.  

## <a name="see-also"></a>참고 항목

+ [단순 검색 쿼리 예제](search-query-simple-examples.md)
+ [전체 Lucene 검색 쿼리 예제](search-query-lucene-examples.md)
+ [문서 검색](/rest/api/searchservice/Search-Documents)
+ [필터 및 정렬을 위한 OData 식 구문](query-odata-filter-orderby-syntax.md)   
+ [Azure Cognitive Search의 단순 쿼리 구문](query-simple-syntax.md)
