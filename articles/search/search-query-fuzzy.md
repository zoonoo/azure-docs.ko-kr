---
title: 유사 항목 검색
titleSuffix: Azure Cognitive Search
description: 맞춤법이 틀린 용어 나 오타를 자동으로 수정하려면 "의미했습니까" 검색 환경을 구현합니다.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 04/08/2020
ms.openlocfilehash: 95f6befa7e97984ad64a3ffb2a9b91d343945cbb
ms.sourcegitcommit: 25490467e43cbc3139a0df60125687e2b1c73c09
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/09/2020
ms.locfileid: "81000662"
---
# <a name="fuzzy-search-to-correct-misspellings-and-typos"></a>맞춤법 오류 및 오타를 수정하기 위한 퍼지 검색

Azure Cognitive Search는 입력 문자열의 오타 및 맞춤법이 틀린 용어를 보정하는 쿼리 유형인 퍼지 검색을 지원합니다. 유사한 구도를 갖는 용어를 검색하여 이 작업을 수행합니다. 일치 항목 에 가까운 항목을 대상으로 검색을 확장하면 불일치가 잘못 배치된 문자몇 개일 때 오타를 자동으로 수정하는 효과가 있습니다. 

## <a name="what-is-fuzzy-search"></a>퍼지 검색이란?

비슷한 구성을 갖는 용어에 따라 일치하는 확장 연습입니다. 퍼지 검색이 지정되면 엔진은 쿼리의 모든 전체 용어에 대해 유사하게 구성된 용어의 [그래프(결정적 유한 한 자동 자동 이론에](https://en.wikipedia.org/wiki/Deterministic_finite_automaton)기반)를 작성합니다. 예를 들어 쿼리에 "워싱턴 대학"이라는 세 용어가 포함된 경우 쿼리의 `search=university~ of~ washington~` 모든 용어에 대해 그래프가 만들어집니다(퍼지 검색에서 중지 단어 제거가 없으므로 "of"는 그래프를 가져옵니다).

그래프는 각 용어의 최대 50개의 확장 또는 순열로 구성되며 프로세스에서 올바른 변형과 잘못된 변형을 모두 캡처합니다. 그런 다음 엔진은 응답에서 가장 중요한 일치 항목을 반환합니다. 

"대학"과 같은 용어의 경우 그래프에는 "불변, 유니버시티, 대학, 우주, 역"이 있을 수 있습니다. 그래프의 문서와 일치하는 모든 문서가 결과에 포함됩니다. 텍스트를 분석하여 동일한 단어("마우스" 및 "마우스")의 다른 형태를 처리하는 다른 쿼리와 달리 퍼지 쿼리의 비교는 텍스트에 대한 언어 분석 없이 액면가로 수행됩니다. 구문 불일치가 작기 때문에 "우주"와 "역"은 서로 다른 것입니다.

불일치가 두 개 이하의 편집으로 제한되는 경우 일치가 성공하며, 여기서 편집은 삽입, 삭제, 대체 또는 전치된 문자입니다. 차동을 지정하는 문자열 보정 알고리즘은 "한 단어를 다른 단어로 변경하는 데 필요한 최소 작업 수(삽입, 삭제, 대체 또는 두 인접 문자의 전치)"로 설명되는 [Damerau-Levenshtein 거리](https://en.wikipedia.org/wiki/Damerau%E2%80%93Levenshtein_distance) 메트릭입니다. 

Azure 인지 검색에서:

+ 퍼지 쿼리는 전체 용어에 적용되지만 AND 구성을 통해 구를 지원할 수 있습니다. 예를 들어, "Wshington~"의 "Unviersty~"는 "워싱턴 대학"과 일치합니다.

+ 편집의 기본 거리는 2입니다. 값은 `~0` 확장이 없음을 나타내지만(정확한 용어만 일치하는 것으로 간주됨) 1정도의 차이 또는 하나의 편집을 지정할 `~1` 수 있습니다. 

+ 퍼지 쿼리는 용어를 최대 50개의 추가 순열까지 확장할 수 있습니다. 이 제한은 구성할 수 없지만 편집 거리를 1로 줄여 확장 횟수를 효과적으로 줄일 수 있습니다.

+ 응답은 관련 일치 검색이 포함된 문서(최대 50개)로 구성됩니다.

전체적으로 그래프는 인덱스의 토큰에 대한 일치 기준으로 제출됩니다. 당신이 상상할 수 있듯이 퍼지 검색은 본질적으로 다른 쿼리 양식 보다 느립니다. 인덱스의 크기와 복잡성은 응답의 대기 시간을 상쇄하기에 충분한 이점이 있는지 여부를 결정할 수 있습니다.

> [!NOTE]
> 퍼지 검색이 느린 경향이 있기 때문에 짧은 문자 시퀀스(bigram 및 trigram 토큰에 대한 2자 및 3자 시퀀스)가 진행되는 n-gram 인덱싱과 같은 대안을 조사하는 것이 좋습니다. 언어 및 쿼리 표면에 따라 n-gram은 더 나은 성능을 제공할 수 있습니다. 단점은 n-gram 인덱싱이 매우 저장 집약적이며 훨씬 더 큰 인덱스를 생성한다는 것입니다.
>
> 가장 심각한 경우를 처리하려는 경우 고려할 수있는 또 다른 대안은 [동의어맵이](search-synonyms.md)될 것입니다. 예를 들어 "검색"을 "serach, serch, sarch"로 매핑하거나 "검색"을 "retreive"로 매핑합니다.

## <a name="indexing-for-fuzzy-search"></a>퍼지 검색을 위한 인덱싱

분석기는 확장 그래프를 만드는 쿼리 처리 중에 사용되지 않지만 퍼지 검색 시나리오에서 분석기를 무시해야 한다는 의미는 아닙니다. 결국 분석기는 인덱싱 중에 검색이 수행되는 토큰을 만드는 데 사용됩니다. 

일반적으로 분석기를 필드별로 할당할 때 분석 체인을 미세 조정하는 결정은 퍼지 검색과 같은 특수한 쿼리 양식이 아닌 기본 사용 사례(필터 또는 전체 텍스트 검색)를 기반으로 합니다. 이러한 이유로 퍼지 검색에 대한 특정 분석기 권장 사항은 없습니다. 

그러나 테스트 쿼리가 예상한 일치 항목을 생성하지 않는 경우 인덱싱 분석기를 변경하여 [언어 분석기로](index-add-language-analyzers.md)설정하여 더 나은 결과를 얻을 수 있는지 확인할 수 있습니다. 일부 언어, 특히 모음 돌연변이가 있는 언어는 Microsoft 자연어 프로세서에서 생성된 변곡점과 불규칙한 단어 형식의 이점을 활용할 수 있습니다. 경우에 따라 올바른 언어 분석기를 사용하면 용어가 사용자가 제공하는 값과 호환되는 방식으로 토큰화되는지 여부에 차이가 있습니다.

## <a name="how-to-use-fuzzy-search"></a>퍼지 검색 사용 방법

퍼지 쿼리는 Lucene 쿼리 [파서를](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/classic/package-summary.html)호출하는 전체 Lucene 쿼리 구문을 사용하여 생성됩니다.

1. 쿼리 ()에서`queryType=full`전체 루센 파서를 설정합니다.

1. 선택적으로 이 매개 변수()를`searchFields=<field1,field2>`사용하여 요청을 특정 필드로 범위를 정합니다. 

1. 전체 기간 ()의`~``search=<string>~`끝에 물결선 () 연산을 부가하십시오.

   편집 거리()를`~1`지정하려면 선택적 매개변수인 0과 2 사이의 숫자(기본값)를 포함합니다. 예를 들어, "blue~" 또는 "blue~1"은 "blue", "blues" 및 "glue"를 반환합니다.

Azure Cognitive Search에서는 용어 및 거리(최대 2) 외에 쿼리에 설정할 추가 매개 변수가 없습니다.

> [!NOTE]
> 쿼리 처리 중에 퍼지 쿼리는 [어휘 분석을](search-lucene-query-architecture.md#stage-2-lexical-analysis)거치지 않습니다. 쿼리 입력은 쿼리 트리에 직접 추가되고 확장되어 용어 그래프를 만듭니다. 수행되는 유일한 변환은 하위 대/소문자입니다.

## <a name="how-to-test-fuzzy-search"></a>퍼지 검색을 테스트하는 방법

간단한 테스트의 경우 쿼리 식을 반복하기 위해 [검색 탐색기](search-explorer.md) 또는 [Postman을](search-get-started-postman.md) 사용하는 것이 좋습니다. 두 도구 모두 대화형이기 때문에 용어의 여러 변형을 빠르게 단계별로 단계별로 살펴보고 다시 돌아오는 응답을 평가할 수 있습니다.

결과가 모호한 경우 [강조 표시를 누르면](search-pagination-page-layout.md#hit-highlighting) 응답에서 일치 를 식별하는 데 도움이 됩니다. 

> [!Note]
> 흐릿한 일치 항목을 식별하기 위해 히트 강조 표시를 사용하면 제한이 있으며 기본 퍼지 검색에만 적용됩니다. 인덱스에 점수 매기기 프로필이 있거나 쿼리를 추가 구문으로 계층화하는 경우 조회 를 누르면 일치 하는 것을 식별하지 못할 수 있습니다. 

### <a name="example-1-fuzzy-search-with-the-exact-term"></a>예 1: 정확한 용어로 퍼지 검색

다음 문자열이 검색 `"Description"` 문서의 필드에 있다고 가정합니다.`"Test queries with special characters, plus strings for MSFT, SQL and Java."`

"특수"에 퍼지 검색으로 시작하고 설명 필드에 히트 강조 표시를 추가 :

    search=special~&highlight=Description

응답에서 히트 강조 표시를 추가했기 때문에 서식이 일치하는 용어로 "특수"에 적용됩니다.

    "@search.highlights": {
        "Description": [
            "Test queries with <em>special</em> characters, plus strings for MSFT, SQL and Java."
        ]

여러 글자 ("pe")를 꺼내서 "특수"철자를 잘못 입력하여 요청을 다시 시도하십시오.

    search=scial~&highlight=Description

지금까지 응답에 대한 변경 사항은 없습니다. 기본값인 2도 거리를 사용하여 "특수"에서 두 문자 "pe"를 제거하면 해당 용어에서 성공적인 일치가 가능합니다.

    "@search.highlights": {
        "Description": [
            "Test queries with <em>special</em> characters, plus strings for MSFT, SQL and Java."
        ]

한 번 더 요청을 시도하면 총 3개의 삭제에 대해 마지막 문자 하나를 꺼내서 검색어를 수정합니다("특수"에서 "scal"로).

    search=scal~&highlight=Description

동일한 응답이 반환되지만 이제 "특수"와 일치하는 대신 퍼지 일치가 "SQL"에 있습니다.

            "@search.score": 0.4232868,
            "@search.highlights": {
                "Description": [
                    "Mix of special characters, plus strings for MSFT, <em>SQL</em>, 2019, Linux, Java."
                ]

이 확장된 예제의 요점은 강조 표시가 모호한 결과를 가져올 수 있는 명확성을 설명하는 것입니다. 모든 경우에 동일한 문서가 반환됩니다. 일치를 확인하기 위해 문서 ID에 의존했다면 "특수"에서 "SQL"로의 전환을 놓쳤을 수 있습니다.

## <a name="see-also"></a>참조

+ [Azure 인지 검색(쿼리 구문 분석 아키텍처)에서 전체 텍스트 검색이 작동하는 방식](search-lucene-query-architecture.md)
+ [탐색기 검색](search-explorer.md)
+ [.NET에서 쿼리를 수행하는 방법](search-query-dotnet.md)
+ [REST에서 쿼리를 수행하는 방법](search-create-index-rest-api.md)
