---
title: 단순 쿼리 구문
titleSuffix: Azure Cognitive Search
description: Azure Cognitive Search의 전체 텍스트 검색 쿼리에 사용되는 간단한 쿼리 구문에 대한 참조입니다.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 04/12/2020
ms.openlocfilehash: 066190ff6b735d30db351ff90c0b6e5173b7f583
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/13/2020
ms.locfileid: "81258867"
---
# <a name="simple-query-syntax-in-azure-cognitive-search"></a>Azure 인지 검색의 간단한 쿼리 구문

Azure Cognitive Search는 두 개의 Lucene 기반 쿼리 언어인 [단순 쿼리 파서](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/simple/SimpleQueryParser.html) 및 [Lucene 쿼리 파서를](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/classic/package-summary.html)구현합니다. 

Azure Cognitive Search에서 간단한 쿼리 구문은 퍼지 검색 작업을 제외합니다. 대신 [퍼지 검색에](search-query-fuzzy.md)전체 Lucene 구문을 사용합니다.

> [!NOTE]
> 간단한 쿼리 구문은 [검색 문서](https://docs.microsoft.com/rest/api/searchservice/search-documents) API의 **검색** 매개 변수에 전달 된 쿼리 식에 사용 되며 해당 API의 [$filter](search-filters.md) 매개 변수에 사용 되는 [OData 구문과](query-odata-filter-orderby-syntax.md) 혼동 되지 않습니다. 이러한 서로 다른 구문에는 쿼리 생성, 문자열 이스케이프 등을 위한 자체 규칙이 있습니다.
>
> Azure Cognitive Search는 **검색** 매개 변수에서 보다 복잡한 쿼리에 대한 대체 [전체 Lucene 쿼리 구문을](query-lucene-syntax.md) 제공합니다. 각 구문의 구문 분석 및 쿼리 구문의 이점에 대해 자세히 알아보려면 [Azure Cognitive Search에서 전체 텍스트 검색의 작동 방식을](search-lucene-query-architecture.md)참조하세요.

## <a name="invoke-simple-parsing"></a>간단한 구문 분석 호출

단순 구문은 기본값입니다. 호출은 전체 구문에서 단순 구문으로 다시 설정하는 경우에만 필요합니다. 구문을 명시적으로 설정하려면 `queryType` 검색 매개 변수를 사용합니다. 유효한 값은 `queryType=full`기본값인 또는 `simple` `full` 을 포함하며 `queryType=simple` 고급 쿼리에 대한 [전체 Lucene 쿼리 파서를](query-lucene-syntax.md) 호출합니다. 

## <a name="syntax-fundamentals"></a> 구문 기본 사항

하나 이상의 단어를 포함하는 모든 텍스트는 쿼리 실행의 유효한 시작점으로 간주됩니다. Azure Cognitive Search는 텍스트 분석 중에 발견된 변형을 포함하여 용어 의 전부 또는 전부를 포함하는 문서와 일치합니다.

이 소리처럼 간단하지만, 더 많은 용어와 연산자가 입력 문자열에 추가됨에 따라 검색 결과를 줄이는 대신 예기치 않은 결과를 생성할 *수* 있는 Azure Cognitive Search의 쿼리 실행 측면이 있습니다. 이 확장이 실제로 발생하는지 여부는 NOT 연산자의 포함여부에 따라 달라지며, AND 또는 OR 동작 측면에서 NOT이 해석되는 방식을 결정하는 **searchMode** 매개 변수 설정과 결합됩니다. 자세한 내용은 [NOT 연산자](#not-operator)를 참조하세요.

### <a name="precedence-operators-grouping"></a>우선 연산자(그룹화)

괄호를 사용(괄호문 내에 연산자를 포함)하여 하위 쿼리를 만들 수 있습니다. 예를 들어, `motel+(wifi||luxury)`는 용어 motel과 "wifi" 또는 "luxury" 중 하나(또는 둘 다)를 포함하는 문서를 검색합니다.|

필드 그룹화는 유사하지만 그룹화 범위가 단일 필드입니다. 예를 들어, `hotelAmenities:(gym+(wifi||pool))`은 필드 "hotelAmenities"에서 "gym"과 "wifi" 또는 "gym"과 "pool"을 검색합니다.  

### <a name="escaping-search-operators"></a>검색 연산자 이스케이프  

검색 연산자의 일부를 검색 텍스트의 일부로 사용하려면 단일 백슬래시()로`\`접합하여 문자를 이스케이프합니다. 예를 들어 `https://`에서 와일드카드 검색의 `://` 경우 쿼리 문자열의 일부인 경우 `search=https\:\/\/*`을 지정합니다. 마찬가지로 이스케이프된 전화 번호 패턴은 다음과 `\+1 \(800\) 642\-7676`같을 수 있습니다.

이스케이프가 필요한 특수 문자는 다음과 같습니다.`- * ? \ /`  

좀 더 일반적인 경우에서 좀 더 간단히 나타내기 위해 이스케이프가 필요하지 않은 다음과 같은 예외 규칙 2가지를 살펴보겠습니다.  

+ NOT 연산자 `-`는 공백 뒤의 첫 번째 문자인 경우에만 이스케이프되고, 단어 중간에 있는 경우에는 이스케이프되지 않아야 합니다. 예를 들어 다음 GUID는 이스케이프 `3352CDD0-EF30-4A2E-A512-3B30AF40F3FD`문자 없이 유효합니다.

+ 접미사 연산자 `*`는 공백 앞의 마지막 문자인 경우에만 이스케이프되고, 단어 중간에 있는 경우에는 이스케이프되지 않아야 합니다. 예를 들어 `4*4=16` 백슬래시가 필요하지 않습니다.

> [!NOTE]  
> 이스케이프는 토큰을 함께 유지하지만 인덱싱 중에 [어휘 분석은](search-lucene-query-architecture.md#stage-2-lexical-analysis) 토큰을 제거할 수 있습니다. 예를 들어 표준 Lucene 분석기는 하이픈, 공백 및 기타 문자에서 단어를 삭제하고 끊습니다. 쿼리 문자열에 특수 문자가 필요한 경우 인덱스에서 해당 문자를 보존하는 분석기가 필요할 수 있습니다. 일부 선택 사항에는 하이픈을 삽입한 단어를 보존하는 Microsoft [자연어 분석기](index-add-language-analyzers.md)또는 보다 복잡한 패턴에 대한 사용자 지정 분석기 등이 있습니다. 자세한 내용은 [부분 용어, 패턴 및 특수 문자를](search-query-partial-matching.md)참조하십시오.

### <a name="encoding-unsafe-and-reserved-characters-in-urls"></a>URL에서 안전하지 않은 문자 및 예약된 문자 인코딩

URL에서 안전하지 않은 문자 및 예약된 문자를 모두 인코딩하세요. 예를 들어 '#'은 URL의 조각/앵커 식별자이므로 안전하지 않은 문자입니다. 이 문자는 URL에서 사용할 경우 `%23`으로 인코딩해야 합니다. '&' 및 '='는 매개 변수를 구분하고 Azure Cognitive Search에서 값을 지정할 때 예약된 문자의 예입니다. 자세한 내용은 [RFC1738: 균일 리소스 로케이터(URL)를](https://www.ietf.org/rfc/rfc1738.txt) 참조하십시오.

안전하지 않은 문자는 ``" ` < > # % { } | \ ^ ~ [ ]``입니다. 예약된 문자는 `; / ? : @ = + &`입니다.

###  <a name="query-size-limits"></a><a name="bkmk_querysizelimits"></a>쿼리 크기 제한

 Azure Cognitive Search에 보낼 수 있는 쿼리 크기에제한이 있습니다. 특히, 최대 1024개 절(AND, OR 등으로 구분된 식)을 사용할 수 있습니다. 또한 한 쿼리의 개별 용어 크기도 약 32KB로 제한됩니다. 애플리케이션이 검색 쿼리를 프로그래밍 방식으로 생성하는 경우 쿼리가 제한 없는 크기로 생성되지 않도록 디자인하는 것이 좋습니다.  

## <a name="boolean-search"></a>부울 검색

쿼리 문자열에 Boolean 연산자(AND, OR, NOT)를 포함시켜 일치하는 문서가 발견되는 풍부한 기준 집합을 작성할 수 있습니다. 

### <a name="and-operator-"></a>AND 연산자 `+`

AND 연산자는 더하기 기호입니다. 예를 들어, `wifi+luxury`는 `wifi`와 `luxury`를 둘 다 포함하는 문서를 검색합니다.

### <a name="or-operator-"></a>OR 연산자 `|`

OR 연산자는 세로줄 또는 파이프 문자입니다. 예를 들어, `wifi | luxury`는 `wifi` 또는 `luxury` 중 하나를 포함하는 문서를 검색합니다.

<a name="not-operator"></a>

### <a name="not-operator--"></a>NOT 연산자 `-`

NOT 연산자는 빼기 기호입니다. 예를 들어 `wifi –luxury` `wifi` 용어가 있거나 `luxury`없는 문서를 검색합니다.

쿼리 요청의 **searchMode** 매개 변수는 NOT 연산자가 있는 용어가 ANDed인지 아니면 다른 용어와 `|` ORed인지 를 제어합니다(다른 용어에 연산자가 없다고 `+` 가정). 유효한 값은 `any`나 `all`입니다.

`searchMode=any`더 많은 결과를 포함하여 쿼리 회수를 `-` 증가시키고 기본적으로 "OR NOT"로 해석됩니다. 예를 들어, `wifi -luxury`는 용어 `wifi`를 포함하는 문서 또는 용어 `luxury`를 포함하지 않는 문서를 검색합니다.

`searchMode=all`더 적은 결과를 포함하여 쿼리의 정밀도를 높이고 기본적으로 "NOT"로 해석됩니다. 예를 들어 `wifi -luxury`는 용어 `wifi`를 포함하고 용어 "luxury"를 포함하지 않는 문서를 검색합니다. 이러한 동작이 `-` 연산자의 좀 더 간단한 동작일 것입니다. 따라서 회수 대신 `searchMode=all` 정밀도에 대 한 검색을 최적화 하려는 `searchMode=any` 경우 대신 사용 하는 `-` 것이 좋습니다 *및* 사용자가 자주 검색에서 연산자 사용.

**searchMode** 설정을 결정할 때는 다양한 응용 프로그램의 쿼리에 대한 사용자 상호 작용 패턴을 고려하십시오. 정보를 검색하는 사용자는 탐색 구조가 내장된 전자 상거래 사이트와 달리 쿼리에 연산자가 포함될 가능성이 높습니다.

<a name="prefix-search"></a>

## <a name="prefix-search"></a>접두사 검색

접미사 연산자는 별표입니다. `*` 예를 들어, `lingui*` 사례를 무시하고 "언어" 또는 "linguini"를 찾을 수 있습니다. 

필터와 마찬가지로 접두사 쿼리는 정확한 일치를 찾습니다. 따라서 관련성 점수가 없습니다(모든 결과는 1.0의 검색 점수를 받습니다). 특히 인덱스가 크고 접두사가 적은 수의 문자로 구성된 경우 접두사 쿼리속도가 느려질 수 있습니다. 

문자열의 마지막 부분과 일치하는 접미사 쿼리를 실행하려면 와일드카드 [검색및](query-lucene-syntax.md#bkmk_wildcard) 전체 Lucene 구문을 사용합니다.

## <a name="phrase-search-"></a>구 검색`"`

용어 검색은 하나 이상의 용어에 대한 쿼리로, 해당 용어는 일치하는 것으로 간주됩니다. 구문 검색은 따옴표로 `" "`둘러싸인 정확한 구입니다. 예를 들어, `Roach Motel`(따옴표 제외)은 순서 및 위치에 관계없이 `Roach` 및/또는 `Motel`을 포함하는 문서를 검색하지만, `"Roach Motel"`(따옴표 포함)은 해당 전체 구를 해당 순서로 포함하는 문서만 검색합니다(텍스트 분석은 계속 적용됨).

## <a name="see-also"></a>참고 항목  

+ [간단한 검색을 위한 쿼리 예제](search-query-simple-examples.md)
+ [전체 Lucene 검색에 대한 쿼리 예제](search-query-lucene-examples.md)
+ [Azure 인지 검색 REST API&#41;&#40;문서 검색](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
+ [Lucene 쿼리 구문](query-lucene-syntax.md)
+ [OData 식 구문](query-odata-filter-orderby-syntax.md) 
