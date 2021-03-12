---
title: 의미 체계 쿼리 만들기
titleSuffix: Azure Cognitive Search
description: 의미 체계 쿼리 유형을 설정 하 여 심층 학습 모델을 쿼리 처리에 연결 하 고, 의도 및 컨텍스트를 검색 순위 및 관련성의 일부로 유추 합니다.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/12/2021
ms.openlocfilehash: 621cfa8977d4d0ed987b7d38407bbf5bbb370950
ms.sourcegitcommit: ec39209c5cbef28ade0badfffe59665631611199
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/12/2021
ms.locfileid: "103232745"
---
# <a name="create-a-semantic-query-in-cognitive-search"></a>Cognitive Search에서 의미 체계 쿼리 만들기

> [!IMPORTANT]
> 의미 체계 쿼리 형식은 미리 보기 상태 이며 미리 보기 REST API 및 Azure Portal를 통해 사용할 수 있습니다. 미리 보기 기능은 [추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)에서 있는 그대로 제공 됩니다. 자세한 내용은 [가용성 및 가격 책정](semantic-search-overview.md#availability-and-pricing)을 참조 하세요.

이 문서에서는 의미 체계 순위를 사용 하는 검색 요청을 작성 하는 방법에 대해 알아봅니다. 이 요청은 의미 체계 캡션과 필요에 따라 [의미 체계 답변](semantic-answers.md)을 반환 하 고 가장 관련성이 높은 용어와 구를 강조 표시 합니다.

캡션 및 대답은 모두 검색 문서의 텍스트에서 약어로 추출 됩니다. 의미 체계 하위 시스템은 캡션 또는 대답의 특징을 포함 하는 콘텐츠를 결정 하지만 새 문장이 나 구를 구성 하지는 않습니다. 이러한 이유로 설명 또는 정의를 포함 하는 내용은 의미 체계 검색에 가장 잘 작동 합니다.

## <a name="prerequisites"></a>전제 조건

+ 표준 계층 (S1, S2, S3)의 search 서비스는 미국 중 북부, 미국 서 부, 미국 서 부 2, 미국 동부 2, 유럽 서 부, 유럽 서부입니다. 이러한 지역 중 하나에 기존 S1 이상 서비스를 사용 하는 경우 새 서비스를 만들지 않고도 액세스를 요청할 수 있습니다.

+ 의미 체계 검색 미리 보기에 액세스: [등록](https://aka.ms/SemanticSearchPreviewSignup)

+ 영어 콘텐츠를 포함 하는 기존 검색 인덱스

+ 쿼리를 보내기 위한 검색 클라이언트

  검색 클라이언트는 쿼리 요청에 대 한 미리 보기 REST Api를 지원 해야 합니다. 미리 보기 Api에 대 한 REST 호출을 수행 하기 위해 수정한 [Postman](search-get-started-rest.md), [Visual Studio Code](search-get-started-vs-code.md)또는 코드를 사용할 수 있습니다. Azure Portal에서 [검색 탐색기](search-explorer.md) 를 사용 하 여 의미 체계 쿼리를 제출할 수도 있습니다.

+ [쿼리 요청](/rest/api/searchservice/preview-api/search-documents) 은이 문서에서 설명 하는 의미 체계 옵션과 기타 매개 변수를 포함 해야 합니다.

## <a name="whats-a-semantic-query"></a>의미 체계 쿼리는 무엇 인가요?

Cognitive Search 쿼리는 쿼리 처리 및 응답의 셰이프를 결정 하는 매개 변수가 있는 요청입니다. *의미 체계 쿼리* 는 일치 결과에 대 한 컨텍스트 및 의미를 평가 하 고, 위쪽으로 관련성이 높은 일치 항목을 승격 하 고, 의미 체계 대답 및 캡션을 반환할 수 있는 의미 체계를 호출 하는 매개 변수를 추가 합니다.

다음 요청은 최소한의 의미 체계 쿼리 (대답 없음)를 나타냅니다.

```http
POST https://[service name].search.windows.net/indexes/[index name]/docs/search?api-version=2020-06-30-Preview      
{    
    "search": " Where was Alan Turing born?",    
    "queryType": "semantic",  
    "searchFields": "title,url,body",  
    "queryLanguage": "en-us"  
}
```

Cognitive Search의 모든 쿼리와 마찬가지로 요청은 단일 인덱스의 문서 컬렉션을 대상으로 합니다. 또한 의미 체계 쿼리는 구문 분석, 분석, 검색 및 점수 매기기를 의미 없는 쿼리로 동일한 순서로 수행 합니다. 

차이점은 관련성 및 점수 매기기에 있습니다. 이 미리 보기 릴리스에서 정의 된 의미 체계 쿼리는 의미 체계 언어 모델을 사용 하 여 *결과* 를 reranked 의미 체계 쿼리는 기본 유사성 순위 알고리즘에 의해 할당 된 점수가 아닌 의미 체계 ranker 가장 관련성이 높은 것으로 간주 되는 일치 항목을 표시할 수 있는 방법을 제공 하는 것입니다.

초기 결과의 상위 50 일치 항목만 의미상 순위를 지정할 수 있으며 모든 항목에는 응답의 캡션이 포함 됩니다. 필요에 따라 **`answer`** 요청에 대 한 매개 변수를 지정 하 여 잠재적 답변을 추출할 수 있습니다. 자세한 내용은 [의미 체계 대답](semantic-answers.md)을 참조 하세요.

## <a name="query-with-search-explorer"></a>Search 탐색기를 사용하여 쿼리

[검색 탐색기](search-explorer.md) 가 의미 체계 쿼리에 대 한 옵션을 포함 하도록 업데이트 되었습니다. 이러한 옵션은 미리 보기에 액세스 한 후 포털에 표시 됩니다. 쿼리 옵션은 의미 체계 쿼리, searchFields 및 맞춤법 수정을 사용할 수 있습니다.

필요한 쿼리 매개 변수를 쿼리 문자열에 붙여 넣을 수도 있습니다.

:::image type="content" source="./media/semantic-search-overview/search-explorer-semantic-query-options.png" alt-text="검색 탐색기의 쿼리 옵션" border="true":::

## <a name="query-using-rest"></a>REST를 사용 하 여 쿼리

[문서 검색 (REST 미리 보기)](/rest/api/searchservice/preview-api/search-documents) 을 사용 하 여 프로그래밍 방식으로 요청을 작성 합니다.

응답에는 캡션 및 강조 표시가 자동으로 포함 됩니다. 응답에 철자 수정 이나 답변이 포함 되도록 하려면 **`speller`** **`answers`** 요청에 선택적 또는 매개 변수를 추가 합니다.

다음 예에서는 호텔-샘플 인덱스를 사용 하 여 의미 체계 대답 및 캡션을 포함 하는 의미 체계 쿼리 요청을 만듭니다.

```http
POST https://[service name].search.windows.net/indexes/hotels-sample-index/docs/search?api-version=2020-06-30-Preview      
{
    "search": "newer hotel near the water with a great restaurant",
    "queryType": "semantic",
    "queryLanguage": "en-us",
    "searchFields": "HotelName,Category,Description",
    "speller": "lexicon",
    "answers": "extractive|count-3",
    "highlightPreTag": "<strong>",
    "highlightPostTag": "</strong>",
    "select": "HotelId,HotelName,Description,Category",
    "count": true
}
```

다음 표에서는 의미 체계 쿼리에 사용 되는 쿼리 매개 변수를 요약 하 여 전체적으로 볼 수 있도록 합니다. 모든 매개 변수의 목록은 [문서 검색 (REST 미리 보기)](/rest/api/searchservice/preview-api/search-documents) 을 참조 하세요.

| 매개 변수 | 형식 | Description |
|-----------|-------|-------------|
| queryType | 문자열 | 유효한 값은 simple, full 및 의미 체계입니다. 의미 체계 쿼리에는 "의미 체계"의 값이 필요 합니다. |
| queryLanguage | 문자열 | 의미 체계 쿼리에 필요 합니다. 현재 "en-us"만 구현 됩니다. |
| searchFields | 문자열 | 검색 가능한 필드를 쉼표로 구분한 목록입니다. 선택 사항 이지만 권장 됩니다. 의미 체계 순위가 발생 하는 필드를 지정 합니다. </br></br>단순 및 전체 쿼리 유형과 달리 필드가 나열 되는 순서에 따라 우선 순위가 결정 됩니다. 자세한 사용 지침은 [Step 2: Set searchFields](#searchfields)를 참조 하세요. |
| 맞춤법 검사기 | 문자열 | 검색 엔진에 도달 하기 전에 철자가 틀린 용어를 수정 하는 선택적 매개 변수 (의미 체계 쿼리와는 관련이 없음)입니다. 자세한 내용은 [쿼리에 맞춤법 수정 추가](speller-how-to-add.md)를 참조 하세요. |
| 답변 |문자열 | 의미 대답이 결과에 포함 되는지 여부를 지정 하는 선택적 매개 변수입니다. 현재 "extractive"만 구현 됩니다. 대답은 최대 5 개를 반환 하도록 구성할 수 있습니다. 기본값은 1입니다. 이 예제에서는 "extractive count3" '의 세 가지 답변을 보여 줍니다. \| 자세한 내용은 [의미 체계 답변 반환](semantic-answers.md)을 참조 하세요.|

### <a name="formulate-the-request"></a>요청 공식화

이 섹션에서는 의미 체계 검색에 필요한 쿼리 매개 변수를 단계별로 설명 합니다.

#### <a name="step-1-set-querytype-and-querylanguage"></a>1 단계: queryType 및 queryLanguage 설정

다음 매개 변수를 나머지에 추가 합니다. 두 매개 변수가 모두 필요 합니다.

```json
"queryType": "semantic",
"queryLanguage": "en-us",
```

QueryLanguage는 인덱스 스키마의 필드 정의에 할당 된 모든 [언어 분석기](index-add-language-analyzers.md) 와 일치 해야 합니다. QueryLanguage이 "en-us" 인 경우 모든 언어 분석기는 영어 변형 ("en-us" 또는 "en lucene") 이어야 합니다. 키워드 또는 단순과 같은 언어에 관계 없는 분석기는 queryLanguage 값과 충돌 하지 않습니다.

쿼리 요청에서 [맞춤법 수정](speller-how-to-add.md)도 사용 하는 경우 설정 하는 queryLanguage 맞춤법 검사기, 답변 및 캡션에 동일 하 게 적용 됩니다. 개별 파트에 대 한 재정의가 없습니다. 

검색 인덱스의 콘텐츠는 여러 언어로 구성 될 수 있지만 쿼리 입력은 대부분의 언어로 구성 될 가능성이 높습니다. 검색 엔진은 queryLanguage, 언어 분석기 및 콘텐츠가 구성 된 언어의 호환성을 확인 하지 않으므로 잘못 된 결과가 발생 하지 않도록 쿼리 범위를 적절 하 게 결정 해야 합니다.

<a name="searchfields"></a>

#### <a name="step-2-set-searchfields"></a>2 단계: searchFields 설정

이 매개 변수는 선택 사항 이지만,이 매개 변수를 생략 해도 오류가 발생 하지는 않지만 정렬 된 필드 목록을 제공 하는 것이 캡션과 답변 모두에 대해 적극 권장 됩니다.

SearchFields 매개 변수는 쿼리에 대해 "의미 유사성 유사성"을 평가할 통로를 식별 하는 데 사용 됩니다. 미리 보기의 경우 모델에서 처리 해야 하는 필드에 대 한 힌트가 필요 하므로 searchFields를 비워 두지 않는 것이 좋습니다.

SearchFields의 순서는 중요 합니다. 기존 단순 또는 전체 Lucene 쿼리에서 searchFields를 이미 사용 하는 경우이 매개 변수를 다시 방문 하 여 의미 체계 쿼리 유형으로 전환할 때 필드 순서를 확인 해야 합니다.

두 개 이상의 searchFields가 지정 된 경우 최적의 결과를 보장 하려면 다음 지침을 따르세요.

+ 컬렉션에는 문자열 필드와 최상위 문자열 필드만 포함 합니다. 컬렉션에 문자열이 아닌 필드 또는 하위 수준 필드를 포함 하는 경우에는 오류가 발생 하지 않지만 이러한 필드는 의미 체계 순위에 사용 되지 않습니다.

+ 첫 번째 필드는 제목이 나 이름과 같이 항상 간결 하 고 25 개 단어 아래에 있는 것이 좋습니다.

+ 인덱스에 텍스트 (예: 사용자가 읽을 수 있고와 같이 사용자가 직접 읽을 수 있음)가 있는 경우 `www.domain.com/name-of-the-document-and-other-details` `www.domain.com/?id=23463&param=eis` 목록에 두 번째를 추가 합니다 (또는 간결한 제목 필드가 없는 경우 먼저).

+ 이러한 필드는 문서의 주요 내용과 같이 의미 체계 쿼리에 대 한 대답이 검색 될 수 있는 설명 필드에 따라 수행 합니다.

필드를 하나만 지정 하는 경우에는 문서의 주요 내용과 같이 의미 체계 쿼리에 대 한 대답이 검색 될 수 있는 설명 필드를 사용 합니다. 충분 한 내용이 제공 되는 필드를 선택 합니다. 시기 적절 하 게 처리할 수 있도록 searchFields의 집합적 내용에 대 한 첫 2만 토큰만 의미 체계 평가와 순위를 갖습니다.

#### <a name="step-3-remove-orderby-clauses"></a>3 단계: orderBy 절 제거

기존 요청에 존재 하는 경우 orderBy 절을 제거 합니다. 의미 점수는 결과를 정렬 하는 데 사용 되며, 명시적 정렬 논리를 포함 하는 경우 HTTP 400 오류가 반환 됩니다.

#### <a name="step-4-add-answers"></a>4 단계: 답변 추가

필요에 따라 답변을 제공 하는 추가 처리를 포함 하려면 "답변"을 추가 합니다. 답변 (및 캡션)은 searchFields에 나열 된 필드의 찾은 통로에서 추출 됩니다. 응답에서 최상의 답을 얻으려면 searchFields에 콘텐츠 서식 있는 필드를 포함 해야 합니다. 자세한 내용은 [의미 체계 대답을 반환 하는 방법](semantic-answers.md)을 참조 하세요.

#### <a name="step-5-add-other-parameters"></a>5 단계: 다른 매개 변수 추가

요청에서 원하는 다른 매개 변수를 설정 합니다. [맞춤법 검사기](speller-how-to-add.md), [선택](search-query-odata-select.md)및 개수와 같은 매개 변수는 요청 및 응답 가독성의 품질을 향상 시킵니다.

필요에 따라 캡션에 적용 되는 강조 표시 스타일을 사용자 지정할 수 있습니다. 캡션은 응답을 요약 하는 문서의 주요 통로에 대해 강조 표시 서식을 적용 합니다. 기본값은 `<em>`입니다. 서식 유형 (예: 노란색 배경)을 지정 하려면 highlightPreTag 및 highlightPostTag를 설정 하면 됩니다.

## <a name="evaluate-the-response"></a>응답 평가

모든 쿼리와 마찬가지로 응답은 검색 가능으로 표시 된 모든 필드 또는 select 매개 변수에 나열 된 필드로 구성 됩니다. 여기에는 원래 관련성 점수가 포함 되며, 요청을 작성 한 방법에 따라 개수 또는 일괄 처리 된 결과가 포함 될 수도 있습니다.

의미 체계 쿼리에서 응답에는 새 의미상 순위 관련성 점수, 일반 텍스트의 캡션 및 강조 표시와 같은 추가 요소가 있습니다.

클라이언트 앱에서는 특정 필드의 전체 내용이 아니라 일치 항목에 대 한 설명으로 캡션을 포함 하도록 검색 페이지를 구성할 수 있습니다. 이는 검색 결과 페이지에 대해 개별 필드가 너무 조밀한 경우 유용 합니다.

위의 예제 쿼리에 대 한 응답은 다음 일치 항목을 최상위 선택 항목으로 반환 합니다. 캡션은 일반 텍스트 및 강조 표시 된 버전과 함께 자동으로 반환 됩니다. 이 특정 쿼리 및 모음에 대 한 답변을 확인할 수 없기 때문에이 예제에서 답변을 생략 합니다.

```json
"@odata.count": 35,
"@search.answers": [],
"value": [
    {
        "@search.score": 1.8810667,
        "@search.rerankerScore": 1.1446577133610845,
        "@search.captions": [
            {
                "text": "Oceanside Resort. Luxury. New Luxury Hotel. Be the first to stay. Bay views from every room, location near the pier, rooftop pool, waterfront dining & more.",
                "highlights": "<strong>Oceanside Resort.</strong> Luxury. New Luxury Hotel. Be the first to stay.<strong> Bay</strong> views from every room, location near the pier, rooftop pool, waterfront dining & more."
            }
        ],
        "HotelName": "Oceanside Resort",
        "Description": "New Luxury Hotel. Be the first to stay. Bay views from every room, location near the pier, rooftop pool, waterfront dining & more.",
        "Category": "Luxury"
    },
```

## <a name="next-steps"></a>다음 단계

의미 체계 순위와 응답은 초기 결과 집합을 통해 빌드됩니다. 초기 결과의 품질을 향상 시키는 논리는 의미 체계 검색으로 전달 됩니다. 다음 단계로, 문자열을 토큰화 하는 방법, 결과를 튜닝할 수 있는 점수 매기기 프로필 및 기본 관련성 알고리즘에 영향을 주는 분석기를 포함 하 여 초기 결과에 기여 하는 기능을 검토 합니다.

+ [텍스트 처리를 위한 분석기](search-analyzers.md)
+ [Cognitive Search 유사성 및 점수 매기기](index-similarity-and-scoring.md)
+ [점수 매기기 프로필 추가](index-add-scoring-profiles.md)
+ [의미 체계 검색 개요](semantic-search-overview.md)
+ [쿼리 용어에 맞춤법 검사 추가](speller-how-to-add.md)
