---
title: 유사성 알고리즘 구성
titleSuffix: Azure Cognitive Search
description: 이전 검색 서비스에서 BM25를 사용 하도록 설정 하는 방법 및 인덱스 내용에 맞게 BM25 매개 변수를 수정 하는 방법에 대해 알아봅니다.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/12/2021
ms.openlocfilehash: 52b3523d3c092f1b9375f53038cc3b20d0ddedcc
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2021
ms.locfileid: "103232837"
---
# <a name="configure-the-similarity-ranking-algorithm-in-azure-cognitive-search"></a>Azure Cognitive Search에서 유사성 순위 알고리즘 구성

Azure Cognitive Search는 두 가지 유사성 순위 알고리즘을 지원 합니다.

+ 2020 7 월 15 일까 지 모든 search 서비스에서 사용 하는 *클래식 유사성* 알고리즘입니다.
+ 7 월 15 일 이후에 생성 되는 모든 search 서비스에 사용 되는 *Okapi BM25* 알고리즘의 구현입니다.

BM25 순위는 사용자의 기대 수준에 맞게 정렬 되는 검색 순위를 생성 하는 경향이 있기 때문에 새로운 기본값입니다. 문서 크기와 같은 요소에 따라 결과를 튜닝 하기 위한 [매개 변수](#set-bm25-parameters) 를 제공 합니다. 

2020 년 7 월 15 일 이후에 만들어진 새 서비스의 경우 BM25이 자동으로 사용 되며 유일한 유사성 알고리즘입니다. 새 서비스에서 유사성을 ClassicSimilarity로 설정 하려고 하면 서비스에서 해당 알고리즘을 지원 하지 않기 때문에 HTTP 400 오류가 반환 됩니다.

2020 년 7 월 15 일 이전에 생성 된 이전 서비스의 경우 클래식 유사성은 기본 알고리즘으로 유지 됩니다. 이전 서비스는 아래에 설명 된 대로 인덱스 별로 BM25로 업그레이드할 수 있습니다. 클래식에서 BM25로 전환 하는 경우 검색 결과가 정렬 되는 방식에 대 한 몇 가지 차이점을 확인할 수 있습니다.

> [!NOTE]
> 현재 선택 된 지역에서 표준 서비스에 대해 미리 보기로 제공 되는 의미 체계 순위는 더 적절 한 결과를 생성 하는 추가 단계입니다. 다른 알고리즘과 달리 기존 결과 집합을 반복 하는 추가 기능입니다. 자세한 내용은 [의미 체계 검색 개요](semantic-search-overview.md) 및 [의미 체계 순위](semantic-ranking.md)를 참조 하세요.

## <a name="enable-bm25-scoring-on-older-services"></a>이전 서비스에서 BM25 점수 매기기 사용

2020 년 7 월 15 일 이전에 생성 된 검색 서비스를 실행 하는 경우 새 인덱스에서 유사성 속성을 설정 하 여 BM25를 사용 하도록 설정할 수 있습니다. 이 속성은 새 인덱스에만 표시 되므로 기존 인덱스를 BM25 하려면 새 유사성 속성을 "BM25Similarity"로 설정 하 여 인덱스를 삭제 하 고 [다시 작성](search-howto-reindex.md) 해야 합니다.

유사성 속성을 포함 하는 인덱스가 있는 경우 BM25Similarity 또는 ClassicSimilarity를 전환할 수 있습니다. 

다음 링크는 Azure Sdk의 유사성 속성을 설명 합니다. 

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

## <a name="set-bm25-parameters"></a>BM25 매개 변수 설정

BM25 유사성은 계산 된 관련성 점수를 제어 하기 위해 두 개의 사용자 지정 가능 매개 변수를 추가 합니다. 인덱스를 만드는 동안 BM25 매개 변수를 설정 하거나 인덱스를 만드는 동안 BM25 알고리즘이 지정 된 경우 인덱스 업데이트로 설정할 수 있습니다.

| 속성 | Type | Description |
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