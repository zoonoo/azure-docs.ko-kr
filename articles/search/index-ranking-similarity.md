---
title: 유사성 알고리즘 구성
titleSuffix: Azure Cognitive Search
description: 이전 검색 서비스에서 BM25를 사용하도록 설정하는 방법 및 인덱스 내용에 맞게 BM25 매개 변수를 수정하는 방법을 알아봅니다.
author: puneet-hariharan-MSFT
ms.author: puhariharan
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/12/2021
ms.openlocfilehash: dc046516b154ac1e3b7a1361e78e157902b6bff6
ms.sourcegitcommit: cd8e78a9e64736e1a03fb1861d19b51c540444ad
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/25/2021
ms.locfileid: "112968702"
---
# <a name="configure-the-similarity-ranking-algorithm-in-azure-cognitive-search"></a>Azure Cognitive Search에서 유사성 순위 알고리즘 구성

Azure Cognitive Search는 두 가지 유사성 순위 알고리즘을 지원합니다.

+ 2020년 7월 15일까지 모든 검색 서비스에서 사용된 *클래식 유사성* 알고리즘입니다.
+ 7월 15일 이후에 생성된 모든 검색 서비스에 사용되는 *Okapi BM25* 알고리즘의 구현입니다.

BM25 순위는 사용자의 기대에 더욱 부합하는 검색 순위를 생성하는 경향이 있기 때문에 새로운 기본값입니다. 문서 크기 등의 요소에 따라 결과를 튜닝하기 위한 [매개 변수](#set-bm25-parameters)를 함께 제공합니다. 

2020년 7월 15일 이후에 생성된 새 서비스의 경우 BM25가 자동으로 사용되며 이는 유일한 유사성 알고리즘입니다. 새 서비스에서 유사성을 ClassicSimilarity로 설정하면 서비스에서 해당 알고리즘을 지원하지 않기 때문에 HTTP 400 오류가 반환됩니다.

2020년 7월 15일 이전에 생성된 이전 서비스의 경우 클래식 유사성은 기본 알고리즘으로 유지됩니다. 이전 서비스는 아래에 설명된 것과 같이 인덱스별로 BM25로 업그레이드할 수 있습니다. 클래식에서 BM25로 전환하는 경우 검색 결과가 정렬되는 방식에 대한 몇 가지 차이점을 확인할 수 있습니다.

> [!NOTE]
> 현재 선택된 지역에서 표준 서비스에 대해 미리 보기로 제공되는 의미 체계 순위는 더욱 관련성 있는 결과를 생성하기 위한 추가 단계입니다. 다른 알고리즘과 달리, 기존 결과 집합을 반복하는 추가 기능입니다. 자세한 내용은 [의미 체계 검색 개요](semantic-search-overview.md) 및 [의미 체계 순위](semantic-ranking.md)를 참조하세요.

## <a name="enable-bm25-scoring-on-older-services"></a>이전 서비스에서 BM25 점수 매기기 사용

2020년 7월 15일 이전에 생성된 검색 서비스를 실행하는 경우 새 인덱스에서 유사성 속성을 설정하여 BM25를 사용하도록 설정할 수 있습니다. 이 속성은 새 인덱스에만 표시되므로 기존 인덱스에 BM25를 사용하려면 새 유사성 속성을 "Microsoft.Azure.Search.BM25Similarity"로 설정하여 인덱스를 삭제하고 [다시 빌드](search-howto-reindex.md)해야 합니다.

유사성 속성을 포함하는 인덱스가 있는 경우 BM25Similarity 또는 ClassicSimilarity 간에 전환할 수 있습니다. 

다음 링크에서는 Azure SDK의 유사성 속성을 설명합니다. 

| 클라이언트 라이브러리 | 유사성 속성 |
|----------------|---------------------|
| .NET  | [SearchIndex.Similarity](/dotnet/api/azure.search.documents.indexes.models.searchindex.similarity) |
| Java | [SearchIndex.setSimilarity](/java/api/com.azure.search.documents.indexes.models.searchindex.setsimilarity) |
| JavaScript | [SearchIndex.Similarity](/javascript/api/@azure/search-documents/searchindex#similarity) |
| Python | [SearchIndex의 유사성 속성](/python/api/azure-search-documents/azure.search.documents.indexes.models.searchindex) |

### <a name="rest-example"></a>REST 예제

다음 예제와 같이 [REST API](/rest/api/searchservice/create-index)를 사용할 수도 있습니다.

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

BM25 유사성은 계산된 관련성 점수를 제어하기 위해 두 개의 사용자 지정 가능 매개 변수를 추가합니다. 인덱스를 만드는 동안 BM25 매개 변수를 설정할 수 있으며, 또는 인덱스를 만드는 동안 BM25 알고리즘이 지정된 경우에는 인덱스 업데이트로 설정할 수 있습니다.

| 속성 | 형식 | Description |
|----------|------|-------------|
| k1 | number | 각 일치 조건의 용어 빈도와 문서 쿼리 쌍의 최종 관련성 점수 간에 크기 조정 함수를 제어합니다. 값은 일반적으로 0.0~3.0이며 기본값은 1.2입니다. </br></br>값 0.0은 "이진 모델"을 나타냅니다. 여기서 해당 용어가 텍스트에 나타나는 횟수에 관계없이 일치하는 모든 문서에 대해 일치하는 단일 용어의 기여도는 동일합니다. k1 값이 클수록 문서에서 동일한 용어의 인스턴스가 더 많이 발견됨에 따라 점수가 계속 증가합니다. </br></br>여러 용어가 검색 쿼리의 일부가 될 것으로 생각되는 경우 더 높은 k1 값을 사용하는 것이 중요할 수 있습니다. 이 경우에는 단일 검색어와 여러 번 일치하는 문서보다는 검색되는 여러 쿼리 용어와 일치하는 문서가 좋습니다. 예를 들어 "Apollo Spaceflight"라는 용어가 포함된 문서의 인덱스를 쿼리하는 경우 "Apollo"와 "Spaceflight"를 몇 번만 명시적으로 언급한 다른 문서와 비교할 때 "Spaceflight"를 언급하지 않고도 "Apollo"라는 용어가 포함된 그리스 신화에 대한 문서의 점수를 낮출 수 있습니다. |
| b | number | 문서 길이가 관련성 점수에 미치는 영향을 제어합니다. 값은 0에서 1 사이이며 기본값은 0.75입니다. </br></br>0\.0 값은 문서 길이가 점수에 영향을 주지 않음을 의미하며, 1.0 값은 문서 길이로 관련성 점수에 대 한 용어 빈도의 영향을 정규화함을 의미합니다. </br></br>문서 길이의 용어 빈도를 정규화하는 것은 긴 문서를 penalize하는 경우에 유용합니다. 어떤 경우에는 짧은 문서보다 긴 문서(예: 완전한 소설)가 관련 없는 용어를 훨씬 많이 포함할 가능성이 높습니다. |

### <a name="setting-k1-and-b-parameters"></a>K1 및 b 매개 변수 설정

B 또는 k1 값을 설정하거나 수정하려면 BM25 유사성 개체에 추가합니다. 기존 인덱스에서 이러한 값을 설정하거나 변경하면 최소한 몇 초 동안 인덱스를 오프라인 상태로 전환하므로 활성 인덱싱 및 쿼리 요청이 실패합니다. 따라서 업데이트 요청의 "allowIndexDowntime=true" 매개 변수를 설정해야 합니다.

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