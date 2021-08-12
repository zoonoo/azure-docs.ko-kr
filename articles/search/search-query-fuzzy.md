---
title: 유사 항목 검색
titleSuffix: Azure Cognitive Search
description: 맞춤법이 틀린 용어 또는 오타 자동 수정을 위해 "알고 계십니까?" 검색 환경을 구현합니다.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/03/2021
ms.openlocfilehash: b2f2c8497d5365104a5ffc361b791450925d0c19
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101694787"
---
# <a name="fuzzy-search-to-correct-misspellings-and-typos"></a>맞춤법 오류 및 오타를 수정하기 위한 유사 항목 검색

Azure Cognitive Search는 입력 문자열에서 오타 및 맞춤법이 틀린 용어를 보정하는 쿼리 유형인 유사 항목 검색을 지원합니다. 비슷한 컴퍼지션이 있는 용어를 검색하여 이 작업을 수행합니다. 거의 일치하는 항목을 포함하도록 검색을 확장할 경우 위치가 잘못된 몇 개의 문자만 차이가 있는 경우 오타가 자동으로 수정됩니다. 

## <a name="what-is-fuzzy-search"></a>유사 항목 검색이란?

비슷한 컴퍼지션이 있는 용어에 대해 일치하는 항목을 생성하는 확장 연습입니다. 유사 항목 검색이 지정된 경우 엔진은 쿼리의 모든 용어에 대해 유사하게 구성된 용어의 그래프([Deterministic Finite Automaton 이론](https://en.wikipedia.org/wiki/Deterministic_finite_automaton)을 따름)를 작성합니다. 예를 들어, 쿼리에 세 가지 용어 "university of washington"이 포함되어 있는 경우 쿼리의 모든 용어 `search=university~ of~ washington~`에 대해 그래프가 생성됩니다. 유사 항목 검색에서 중지 단어 제거가 없으므로 "of"의 그래프가 생성됩니다.

이 그래프는 프로세스의 올바른 변형과 올바르지 않은 변형을 모두 캡처하기 위해 각 용어의 최대 50개의 확장 또는 순열로 구성됩니다. 그러면 엔진은 응답에서 가장 관련성이 높은 일치 항목을 반환합니다. 

"university"와 같은 용어의 경우 그래프에 "unversty, universty, university, universe, inverse"가 있을 수 있습니다. 그래프의 항목과 일치하는 모든 문서가 결과에 포함됩니다. 텍스트를 분석하여 동일한 단어의 다양한 형태("mice" 및 "mouse")를 처리하는 다른 쿼리와 달리, 유사 항목 쿼리의 비교는 텍스트에 대한 언어 분석 없이 액면가로 진행됩니다. 구문적으로 서로 다른 "Universe" 및 "inverse"는 구문상 차이가 작기 때문에 일치합니다.

불일치가 두 개 이하의 편집으로 제한되는 경우 일치가 성공합니다. 여기서 편집은 삽입되거나, 삭제되거나, 대체되거나, 위치가 바뀐 문자입니다. 차등을 지정하는 문자열 수정 알고리즘은 "한 단어를 다른 단어를 변경하는 데 필요한 최소 작업 수(삽입, 삭제, 대체 또는 두 인접 문자의 전위)로 설명되는 [Damerau-Levenshtein 거리](https://en.wikipedia.org/wiki/Damerau%E2%80%93Levenshtein_distance) 메트릭입니다. 

Azure Cognitive Search에서 다음이 수행됩니다.

+ 유사 항목 쿼리는 전체 용어에 적용되지만 AND 구성을 통해 구를 지원할 수 있습니다. 예를 들어 "Unviersty~ of~ "Wshington~"은 "University of Washington"과 일치합니다.

+ 편집의 기본 거리는 2입니다. `~0` 값은 확장을 의미하지 않으며(정확한 용어만 일치 항목으로 간주됨) 하나의 차이 또는 하나의 편집 정도에 대해 `~1`을 지정할 수 있습니다. 

+ 유사 항목 쿼리는 하나의 용어를 최대 50개의 추가 순열로 확장할 수 있습니다. 이 한도는 구성할 수 없지만, 편집 거리를 1로 줄여서 확장 수를 효과적으로 줄일 수 있습니다.

+ 응답은 관련 일치 항목을 포함하는 문서로 구성됩니다(최대 50개).

전체적으로 그래프는 인덱스의 토큰에 대해 일치 조건으로 제출됩니다. 짐작할 수 있듯이 유사 항목 검색은 다른 쿼리 양식보다 기본적으로 더 느립니다. 인덱스의 크기와 복잡성은 응답의 대기 시간을 상쇄할 만큼 장점이 충분히 많은지 여부를 결정할 수 있습니다.

> [!NOTE]
> 유사 항목 검색은 속도가 느릴 수 있으므로 더 짧은 문자 시퀀스(바이그램 및 트라이그램 토큰에 대한 2~3개의 문자 시퀀스)가 진행될 경우 n-그램 인덱싱 등과 같은 대안을 조사하는 것이 유용할 수 있습니다. 언어 및 쿼리 화면에 따라 n-그램을 통해 성능이 향상될 수 있습니다. 물론 n-그램 인덱싱이 훨씬 더 스토리지 집약적이고 훨씬 큰 인덱스를 생성한다는 단점이 있습니다.
>
> 가장 까다로운 사례를 처리하려는 경우에만 고려할 수 있는 다른 대안은 [동의어 맵](search-synonyms.md)입니다. 예를 들어 "search"는 "serach, serch, sarch"에 매핑되고 "retrieve"는 "retreive"에 매핑됩니다.

## <a name="indexing-for-fuzzy-search"></a>유사 항목 검색용 인덱싱

쿼리 처리 중에 확장 그래프를 만드는 데 분석기가 사용되지 않지만, 퍼지 검색 시나리오에서 분석기를 무시해야 하는 것은 아닙니다. 결과적으로 분석기는 인덱싱하는 동안 쿼리가 자유 형식인지, 필터링된 검색인지 또는 입력으로 그래프를 포함하는 유사 항목 검색인지에 관계없이 일치 항목이 검색되는 토큰을 만드는 데 사용됩니다. 

일반적으로 필드별로 분석기를 할당할 때 분석 체인을 미세 조정할지에 대한 결정은 유사 정보 검색과 같은 특수 쿼리 양식이 아닌 기본 사용 사례(필터 또는 전체 텍스트 검색)를 기준으로 합니다. 따라서 유사 항목 검색에 대한 특정 분석기 권장 사항은 없습니다. 

그러나 테스트 쿼리가 예상한 일치 항목을 생성하지 않는 경우 인덱싱 분석기를 변경하고 [언어 분석기](index-add-language-analyzers.md)로 설정하여 더 나은 결과를 얻을 수 있는지 확인할 수 있습니다. 일부 언어, 특히 모음 변형이 나타나는 언어는 Microsoft 자연어 프로세서에서 생성된 어형 변화 및 불규칙한 어형을 활용할 수 있습니다. 경우에 따라 올바른 언어 분석기를 사용하면 사용자가 제공하는 값과 호환되는 방식으로 용어가 토큰화되는지 여부가 달라질 수 있습니다.

## <a name="how-to-use-fuzzy-search"></a>유사 항목 검색을 사용하는 방법

유사 항목 쿼리는 전체 Lucene 쿼리 구문을 사용하고 [lucene 쿼리 파서](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/classic/package-summary.html)를 호출하여 생성됩니다.

1. 쿼리에서 전체 Lucene 파서를 설정합니다(`queryType=full`).

1. 필요에 따라 이 매개 변수(`searchFields=<field1,field2>`)를 사용하여 요청 범위를 특정 필드로 지정합니다. 

1. 전체 용어의 끝에 물결표(`~`) 연산자를 추가합니다(`search=<string>~`).

   편집 거리를 지정하려는 경우 선택적 매개 변수인 0과 2 사이의 숫자(기본값)를 포함합니다(`~1`). 예를 들어, "blue~" 또는 "blue~1"은 "blue", "blues" 및 "glue"를 반환합니다.

Azure Cognitive Search에서 용어 및 거리(최대 2) 외에 쿼리에 설정할 추가 매개 변수는 없습니다.

> [!NOTE]
> 쿼리를 처리하는 동안 유사 항목 쿼리는 [어휘 분석](search-lucene-query-architecture.md#stage-2-lexical-analysis)을 거치지 않습니다. 쿼리 입력은 쿼리 트리에 직접 추가되어 용어 그래프를 만들기 위해 확장됩니다. 유일하게 수행되는 변환은 소문자로 바꾸는 것입니다.

## <a name="testing-fuzzy-search"></a>유사 항목 검색 테스트

간단한 테스트를 위해 쿼리 식을 반복하는 데 [검색 탐색기](search-explorer.md) 또는 [Postman](search-get-started-rest.md)을 사용하는 것이 좋습니다. 두 도구 모두 대화형이므로 여러 용어 변형을 빠르게 단계별로 진행하면서 다시 발생하는 응답을 평가할 수 있습니다.

결과가 모호한 경우 [적중 항목 강조 표시](search-pagination-page-layout.md#hit-highlighting)를 사용하여 응답에서 일치 항목을 쉽게 찾을 수 있습니다. 

> [!Note]
> 적중 항목 강조 표시를 사용하여 일치하는 유사 항목을 찾을 때는 약간의 제한이 있으며 기본 유사 항목 검색에서만 이러한 방식이 가능합니다. 인덱스에 점수 매기기 프로필이 있거나 추가 구문을 사용하여 쿼리를 계층화하는 경우 적중 항목 강조 표시를 통해 일치 항목을 식별하지 못할 수 있습니다. 

### <a name="example-1-fuzzy-search-with-the-exact-term"></a>예제 1: 정확한 단어를 사용하여 유사 항목 검색

검색 문서의 `"Description"` 필드에 다음 문자열이 있다고 가정합니다. `"Test queries with special characters, plus strings for MSFT, SQL and Java."`

"special"에 대한 유사 항목 검색을 시작하고 설명 필드에 적중 항목 강조 표시를 추가합니다.

```console
search=special~&highlight=Description
```

응답에서 적중 항목 강조 표시를 추가했으므로 일치 용어로서 "special"에 서식이 적용됩니다.

```output
"@search.highlights": {
    "Description": [
        "Test queries with <em>special</em> characters, plus strings for MSFT, SQL and Java."
    ]
```

몇 개의 문자("pe")를 삭제하여 "special" 철자를 틀리게 만든 후 요청을 다시 시도합니다.

```console
search=scial~&highlight=Description
```

지금까지 응답은 변경되지 않았습니다. 기본값인 2 정도의 거리를 사용하여 "special"에서 두 문자 "pe"를 제거해도 해당 용어에 대한 일치 항목으로 성공적으로 검색됩니다.

```output
"@search.highlights": {
    "Description": [
        "Test queries with <em>special</em> characters, plus strings for MSFT, SQL and Java."
    ]
```

총 3개를 삭제하기 위해 마지막 1개 문자를 삭제하는 방식으로 검색 단어를 추가로 수정하여 요청을 하나 더 시도합니다("special"에서 "scal"로).

```console
search=scal~&highlight=Description
```

동일한 응답이 반환되지만 "special"과 일치하는 항목을 검색하지 않고, "SQL"에 대한 유사 항목 일치가 진행됩니다.

```output
        "@search.score": 0.4232868,
        "@search.highlights": {
            "Description": [
                "Mix of special characters, plus strings for MSFT, <em>SQL</em>, 2019, Linux, Java."
            ]
```

이 확장된 예제의 핵심은 적중 항목 강조 표시를 통해 모호한 결과를 가져올 수 있다는 사실을 명확히 보여 주는 것입니다. 모든 경우에 동일한 문서가 반환됩니다. 문서 ID에 의존하여 일치 항목을 확인했다면 "special"에서 "SQL"로 전환되었다는 사실을 놓쳤을 수 있습니다.

## <a name="see-also"></a>참고 항목

+ [Azure Cognitive Search에서 전체 텍스트 검색이 작동하는 원리(아키텍처를 구문 분석하는 쿼리)](search-lucene-query-architecture.md)
+ [검색 탐색기](search-explorer.md)
+ [.NET에서 쿼리를 수행하는 방법](./search-get-started-dotnet.md)
+ [REST에서 쿼리를 수행하는 방법](./search-get-started-powershell.md)