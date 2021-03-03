---
title: 순위 유사성 알고리즘 구성
titleSuffix: Azure Cognitive Search
description: 유사성 알고리즘을 설정 하 여 순위를 지정 하는 새로운 유사성 알고리즘을 시도 하는 방법
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/02/2021
ms.openlocfilehash: 9f806b512ae8e118fca8f32115c8be3b493fd681
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/02/2021
ms.locfileid: "101677790"
---
# <a name="configure-ranking-algorithms-in-azure-cognitive-search"></a>Azure Cognitive Search에서 순위 알고리즘 구성

Azure Cognitive Search는 두 가지 유사성 순위 알고리즘을 지원 합니다.

+ 2020 7 월 15 일까 지 모든 search 서비스에서 사용 하는 *클래식 유사성* 알고리즘입니다.
+ 7 월 15 일 이후에 생성 되는 모든 search 서비스에 사용 되는 *Okapi BM25* 알고리즘의 구현입니다.

BM25 순위는 사용자의 기대 수준에 맞게 정렬 되는 검색 순위를 생성 하는 경향이 있기 때문에 새로운 기본값입니다. 또한 문서 크기와 같은 요소에 따라 결과를 튜닝 하기 위한 구성 옵션을 사용할 수 있습니다. 2020 년 7 월 15 일 이후에 만들어진 새 서비스의 경우 BM25이 자동으로 사용 되며 유일한 유사성 알고리즘입니다. 새 서비스에서 유사성을 ClassicSimilarity로 설정 하려고 하면 서비스에서 해당 알고리즘을 지원 하지 않기 때문에 HTTP 400 오류가 반환 됩니다.

2020 년 7 월 15 일 이전에 생성 된 이전 서비스의 경우 클래식 유사성은 기본 알고리즘으로 유지 됩니다. 이전 서비스에서는 아래에 설명 된 대로 검색 인덱스에 대 한 속성을 설정 하 여 BM25를 호출할 수 있습니다. 클래식에서 BM25로 전환 하는 경우 검색 결과가 정렬 되는 방식에 대 한 몇 가지 차이점을 확인할 수 있습니다.

> [!NOTE]
> 의미 체계 검색은 기대와 결과 사이의 간격을 좁힐 수 있는 추가 의미 체계 다시 순위 알고리즘입니다. 다른 알고리즘과 달리 기존 결과 집합을 반복 하는 추가 기능입니다. 미리 보기 의미 체계 검색 알고리즘을 사용 하려면 새 서비스를 만들어야 하며 [의미 체계 쿼리 유형을](semantic-how-to-query-request.md)지정 해야 합니다. 자세한 내용은 [의미 체계 검색 개요](semantic-search-overview.md)를 참조 하세요.

## <a name="create-a-search-index-for-bm25-scoring"></a>BM25 점수 매기기를 위한 검색 인덱스 만들기

2020 년 7 월 15 일 이전에 생성 된 검색 서비스를 실행 하는 경우 유사성 속성을 인덱스 정의에서 BM25Similarity 또는 ClassicSimilarity로 설정할 수 있습니다. 유사성 속성이 생략 되거나 null로 설정 된 경우 인덱스는 클래식 알고리즘을 사용 합니다.

유사성 알고리즘은 인덱스를 만들 때에만 설정할 수 있습니다. 그러나 BM25를 사용 하 여 인덱스를 만든 후에는 기존 인덱스를 업데이트 하 여 BM25 매개 변수를 설정 하거나 수정할 수 있습니다.

| 클라이언트 라이브러리 | 유사성 속성 |
|----------------|---------------------|
| .NET  | [SearchIndex. 유사성](/dotnet/api/azure.search.documents.indexes.models.searchindex.similarity) |
| Java | [SearchIndex. setSimilarity](/java/api/com.azure.search.documents.indexes.models.searchindex.setsimilarity) |
| JavaScript | [SearchIndex. 유사성](/javascript/api/@azure/search-documents/searchindex#similarity) |
| Python | [SearchIndex의 유사성 속성](/python/api/azure-search-documents/azure.search.documents.indexes.models.searchindex) |

### <a name="rest-example"></a>REST 예제

다음 예제에 나와 있는 것 처럼 [REST API](/rest/api/searchservice/create-index)를 사용할 수도 있습니다.

```http
PUT https://[search service name].search.windows.net/indexes/[index name]?api-version=2020-06-30
{
    "name": "indexName",
    "fields": [
        {
            "name": "id",
            "type": "Edm.String",
            "key": true
        },
        {
            "name": "name",
            "type": "Edm.String",
            "searchable": true,
            "analyzer": "en.lucene"
        },
        ...
    ],
    "similarity": {
        "@odata.type": "#Microsoft.Azure.Search.BM25Similarity"
    }
}
```

## <a name="bm25-similarity-parameters"></a>BM25 유사성 매개 변수

BM25 유사성은 계산 된 관련성 점수를 제어 하기 위해 두 개의 사용자 지정 가능 매개 변수를 추가 합니다. 인덱스를 만드는 동안 BM25 매개 변수를 설정 하거나 인덱스를 만드는 동안 BM25 알고리즘이 지정 된 경우 인덱스 업데이트로 설정할 수 있습니다.

| 속성 | Type | 설명 |
|----------|------|-------------|
| k1 | number | 각 일치 조건의 용어 빈도와 문서 쿼리 쌍의 최종 관련성 점수 사이의 크기 조정 함수를 제어 합니다. 값은 일반적으로 0.0 ~ 3.0 이며 기본값은 1.2입니다. </br></br>값 0.0은 "이진 모델"을 나타냅니다 .이는 단어가 텍스트에 표시 되는 횟수에 관계 없이 일치 하는 모든 문서에 대해 일치 하는 단일 용어에 대 한 기여는 동일 하지만, 더 큰 k1 값을 사용 하면 동일한 용어의 인스턴스가 문서에서 모두 발견 될 때 점수를 계속 증가 시킬 수 있습니다. </br></br>여러 용어가 검색 쿼리의 일부가 될 것으로 생각 되는 경우 더 높은 k1 값을 사용 하는 것이 중요할 수 있습니다. 이러한 경우에는 여러 다른 쿼리 용어와 일치 하는 문서를 한 번만 일치 하는 문서를 여러 번 검색 하는 것이 좋습니다. 예를 들어 "아폴로 Spaceflight" 라는 용어가 포함 된 문서에 대 한 인덱스를 쿼리할 때 "아폴로" 및 "Spaceflight"를 모두 명확 하 게 설명 하는 다른 문서와 비교 하 여 "Spaceflight" 라는 용어를 포함 하 여 "아폴로" 라는 용어를 포함 하는 문서 점수를 낮출 수 있습니다. |
| b | number | 문서의 길이가 관련성 점수에 미치는 영향을 제어 합니다. 값은 0에서 1 사이 이며 기본값은 0.75입니다. </br></br>0.0 값은 문서의 길이가 점수에 영향을 주지 않음을 의미 하는 반면, 1.0 값은 문서 길이로 관련성 점수에 대 한 용어 빈도의 영향을 정규화 함을 의미 합니다. </br></br>문서 길이의 용어 빈도를 정규화 하는 것은 긴 문서를 penalize 하는 경우에 유용 합니다. 경우에 따라 긴 문서에 비해 긴 문서 (예: 전체 novel)에 관련성이 많은 용어가 포함 될 가능성이 높습니다. |

### <a name="setting-k1-and-b-parameters"></a>K1 및 b 매개 변수 설정

B 또는 k1 값을 설정 하거나 수정 하려면 BM25 유사성 개체에 추가 합니다. 기존 인덱스에서 이러한 값을 설정 하거나 변경 하면 최소한 몇 초 동안 인덱스를 오프 라인 상태로 전환 하므로 활성 인덱싱 및 쿼리 요청이 실패 합니다. 따라서 업데이트 요청의 "allowIndexDowntime 중지 시간 = true" 매개 변수를 설정 해야 합니다.

```http
PUT https://[search service name].search.windows.net/indexes/[index name]?api-version=2020-06-30&allowIndexDowntime=true
{
    "similarity": {
        "@odata.type": "#Microsoft.Azure.Search.BM25Similarity",
        "b" : 0.5,
        "k1" : 1.3
    }
}
```

## <a name="see-also"></a>참고 항목  

+ [REST API 참조](/rest/api/searchservice/)
+ [인덱스에 점수 매기기 프로필 추가](index-add-scoring-profiles.md)
+ [인덱스 API 만들기](/rest/api/searchservice/create-index)
+ [Azure Cognitive Search .NET SDK](/dotnet/api/overview/azure/search)