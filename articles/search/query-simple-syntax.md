---
title: 단순 쿼리 구문
titleSuffix: Azure Cognitive Search
description: Azure Cognitive Search의 전체 텍스트 검색 쿼리에 사용되는 간단한 쿼리 구문에 대한 참조입니다.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/10/2020
translation.priority.mt:
- de-de
- es-es
- fr-fr
- it-it
- ja-jp
- ko-kr
- pt-br
- ru-ru
- zh-cn
- zh-tw
ms.openlocfilehash: fc1eb1836badc3ced688750bbc7c7a164773d022
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77152672"
---
# <a name="simple-query-syntax-in-azure-cognitive-search"></a>Azure 인지 검색의 간단한 쿼리 구문

Azure Cognitive Search는 두 개의 Lucene 기반 쿼리 언어인 [단순 쿼리 파서](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/simple/SimpleQueryParser.html) 및 [Lucene 쿼리 파서를](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/classic/package-summary.html)구현합니다. Azure Cognitive Search에서 간단한 쿼리 구문은 퍼지/슬롭 옵션을 제외합니다.  

> [!NOTE]
> 간단한 쿼리 구문은 [검색 문서](https://docs.microsoft.com/rest/api/searchservice/search-documents) API의 **검색** 매개 변수에 전달 된 쿼리 식에 사용 되며 해당 API의 [$filter](search-filters.md) 매개 변수에 사용 되는 [OData 구문과](query-odata-filter-orderby-syntax.md) 혼동 되지 않습니다. 이러한 서로 다른 구문에는 쿼리 생성, 문자열 이스케이프 등을 위한 자체 규칙이 있습니다.
>
> Azure Cognitive Search는 **검색** 매개 변수에서 보다 복잡한 쿼리에 대한 대체 [전체 Lucene 쿼리 구문을](query-lucene-syntax.md) 제공합니다. 각 구문의 구문 분석 및 쿼리 구문의 이점에 대해 자세히 알아보려면 [Azure Cognitive Search에서 전체 텍스트 검색의 작동 방식을](search-lucene-query-architecture.md)참조하세요.

## <a name="how-to-invoke-simple-parsing"></a>단순 구문 분석 호출 방법

단순 구문은 기본값입니다. 호출은 전체 구문에서 단순 구문으로 다시 설정하는 경우에만 필요합니다. 구문을 명시적으로 설정하려면 `queryType` 검색 매개 변수를 사용합니다. 유효한 값에는 기본값이 `simple`인 `simple|full`과 Lucene용 `full`이 포함됩니다. 

## <a name="query-behavior-anomalies"></a>쿼리 동작 변칙

하나 이상의 단어를 포함하는 모든 텍스트는 쿼리 실행의 유효한 시작점으로 간주됩니다. Azure Cognitive Search는 텍스트 분석 중에 발견된 변형을 포함하여 용어 의 전부 또는 전부를 포함하는 문서와 일치합니다. 

이 소리처럼 간단하지만, 더 많은 용어와 연산자가 입력 문자열에 추가됨에 따라 검색 결과를 줄이는 대신 예기치 않은 결과를 생성할 *수* 있는 Azure Cognitive Search의 쿼리 실행 측면이 있습니다. 이러한 증가가 실제로 발생하는지 여부는 NOT이 AND 또는 OR 동작과 관련해서 해석되는 방식을 결정하는 `searchMode` 매개 변수와 NOT 연산자를 포함하는지에 따라 좌우됩니다. 기본값인 `searchMode=Any`와 NOT 연산자를 지정할 경우 연산은 OR 작업으로 계산되므로 `"New York" NOT Seattle`은 시애틀이 아닌 모든 도시를 반환합니다.  

일반적으로는 콘텐츠를 검색하는 애플리케이션의 사용자 상호 작용 패턴에서 이러한 동작이 나타나기 쉽습니다. 이 경우 사용자는 기본 제공 방식의 탐색 구조를 갖는 전자 상거래 사이트와 달리, 쿼리에 연산자를 포함할 가능성이 높습니다. 자세한 내용은 [NOT 연산자](#not-operator)를 참조하세요. 

## <a name="boolean-operators-and-or-not"></a>부울 연산자(및, 또는, 아님) 

쿼리 문자열에 연산을 포함시켜 일치하는 문서가 발견되는 풍부한 기준 집합을 작성할 수 있습니다. 

### <a name="and-operator-"></a>AND 연산자 `+`

AND 연산자는 더하기 기호입니다. 예를 들어, `wifi+luxury`는 `wifi`와 `luxury`를 둘 다 포함하는 문서를 검색합니다.

### <a name="or-operator-"></a>OR 연산자 `|`

OR 연산자는 세로줄 또는 파이프 문자입니다. 예를 들어, `wifi | luxury`는 `wifi` 또는 `luxury` 중 하나를 포함하는 문서를 검색합니다.

<a name="not-operator"></a>

### <a name="not-operator--"></a>NOT 연산자 `-`

NOT 연산자는 빼기 기호입니다. 예를 들어, `wifi –luxury`는 `wifi` 용어를 포함 및/또는 `luxury`를 포함하지 않는 문서를 검색합니다(및/또는은 `searchMode`로 제어).

> [!NOTE]  
>  `searchMode` 옵션은 `+` 또는 `|` 연산자가 없는 경우 NOT 연산자가 있는 용어가 쿼리의 다른 용어와 AND로 연결될지 또는 OR로 연결될지를 제어합니다. 앞서 설명한 것처럼 `searchMode`를 `any`(기본값) 또는 `all`로 설정할 수 있습니다. `any`를 사용하는 경우 더 많은 결과를 포함하여 쿼리 재현율을 높이고, 기본적으로 `-`은 "OR NOT"으로 해석됩니다. 예를 들어, `wifi -luxury`는 용어 `wifi`를 포함하는 문서 또는 용어 `luxury`를 포함하지 않는 문서를 검색합니다. `all`을 사용하는 경우 더 적은 수의 결과를 포함하여 쿼리 정확도를 높이고, 기본적으로 "AND NOT"으로 해석됩니다. 예를 들어 `wifi -luxury`는 용어 `wifi`를 포함하고 용어 "luxury"를 포함하지 않는 문서를 검색합니다. 이러한 동작이 `-` 연산자의 좀 더 간단한 동작일 것입니다. 따라서 재현율이 아니라 정확도에 따라 최적화하려고 *하며* 사용자가 검색에서 `-` 연산자를 자주 사용하는 경우 `searchMode=any` 대신 `searchMode=all`을 사용하는 것이 좋습니다.

## <a name="suffix-operator"></a>접미사 연산자 

접미사 연산자는 별표입니다. `*` 예를 들어, `lux*`는 대/소문자를 무시하고 `lux`로 시작하는 용어가 포함된 문서를 검색합니다.  

## <a name="phrase-search-operator"></a>구 검색 연산자 

구 연산자는 따옴표로 `" "`구를 동봉합니다. 예를 들어, `Roach Motel`(따옴표 제외)은 순서 및 위치에 관계없이 `Roach` 및/또는 `Motel`을 포함하는 문서를 검색하지만, `"Roach Motel"`(따옴표 포함)은 해당 전체 구를 해당 순서로 포함하는 문서만 검색합니다(텍스트 분석은 계속 적용됨).

## <a name="precedence-operator"></a>우선 순위 연산자 

우선 순위 연산자는 괄호 안에 문자열을 둘러싸는다. `( )` 예를 들어, `motel+(wifi | luxury)` 모텔 용어와 (또는 `wifi` `luxury` 둘 다) 포함된 문서를 검색합니다.  

## <a name="escaping-search-operators"></a>검색 연산자 이스케이프  

 위의 기호를 검색 텍스트의 실제 부분으로 사용하려면 백슬래시를 접두사로 사용하여 이스케이프해야 합니다. 예를 들어, `luxury\+hotel`은 용어 `luxury+hotel`이 됩니다. 좀 더 일반적인 경우에서 좀 더 간단히 나타내기 위해 이스케이프가 필요하지 않은 다음과 같은 예외 규칙 2가지를 살펴보겠습니다.  

- NOT 연산자 `-`는 공백 뒤의 첫 번째 문자인 경우에만 이스케이프되고, 단어 중간에 있는 경우에는 이스케이프되지 않아야 합니다. 예를 들어, `wi-fi`는 단일 단어이지만 GUID(예: `3352CDD0-EF30-4A2E-A512-3B30AF40F3FD`)는 단일 토큰으로 처리됩니다.
- 접미사 연산자 `*`는 공백 앞의 마지막 문자인 경우에만 이스케이프되고, 단어 중간에 있는 경우에는 이스케이프되지 않아야 합니다. 예를 들어, `wi*fi`는 단일 토큰으로 처리됩니다.

> [!NOTE]  
>  이스케이프는 토큰을 유지하지만, 분석 모드에 따라 텍스트 분석 시 토큰이 분할될 수 있습니다. 자세한 내용은 [언어 지원 &#40;Azure 인지 검색 REST API&#41;](index-add-language-analyzers.md) 를 참조하십시오.  

## <a name="see-also"></a>참조  

+ [Azure 인지 검색 REST API&#41;&#40;문서 검색](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) 
+ [Lucene 쿼리 구문](query-lucene-syntax.md)
+ [OData 식 구문](query-odata-filter-orderby-syntax.md) 
