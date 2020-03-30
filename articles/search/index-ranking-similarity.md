---
title: 유사성 순위 알고리즘
titleSuffix: Azure Cognitive Search
description: 순위에 대한 새로운 유사성 알고리즘을 시도하기 위해 유사성 알고리즘을 설정하는 방법
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/13/2020
ms.openlocfilehash: c327440649300533c94c2a1956e3c45f433c9780
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79409974"
---
# <a name="ranking-algorithm-in-azure-cognitive-search"></a>Azure 인지 검색의 순위 알고리즘

> [!IMPORTANT]
> 2020년 7월 15일부터 새로 생성된 검색 서비스는 BM25 순위 함수를 사용하며, 대부분의 경우 현재 기본 순위보다 사용자 기대치에 더 잘 부합하는 검색 순위를 제공합니다.  BM25는 우수한 순위 외에도 문서 크기와 같은 요인에 따라 결과를 조정하기 위한 구성 옵션을 사용할 수 있습니다.  
>
> 이 변경 사항으로 검색 결과 순서에 약간의 변경 사항이 표시 될 가능성이 높습니다.   이러한 변경의 영향을 테스트하려는 사람들을 위해 2019-05-06-Preview API에서 BM25가 새 인덱스에 대해 점수를 매길 수 있도록 했습니다.  

이 문서에서는 2020년 7월 15일 이전에 만든 서비스를 업데이트하여 새 BM25 순위 알고리즘을 사용하는 방법에 대해 설명합니다.

Azure 인지 검색은 이전에 사용된 *ClassicSimilarity* 구현을 대체할 Okapi BM25 알고리즘 *BM25유사성의*공식 Lucene 구현을 사용합니다. 이전 ClassicSimilarity 알고리즘과 마찬가지로 BM25Similarity는 TF(주파수)와 역문서 빈도(IDF)를 변수로 사용하여 각 문서 쿼리 쌍에 대한 관련성 점수를 계산하는 TF-IDF와 유사한 검색 함수입니다. 순위에 사용됩니다. BM25는 이전 Classic 유사성 알고리즘과 개념적으로 유사하지만 확률적 정보 검색의 루트를 사용하여 이를 개선합니다. 또한 BM25는 사용자가 관련성 점수가 일치하는 용어의 빈도로 어떻게 확장되는지 결정할 수 있도록 하는 고급 사용자 지정 옵션을 제공합니다.

## <a name="how-to-test-bm25-today"></a>오늘 BM25를 테스트하는 방법

새 인덱스를 만들 때 "유사성" 속성을 설정할 수 있습니다. 아래와 같이 *2019-05-06-Preview* 버전을 사용해야 합니다.

```
PUT https://[search service name].search.windows.net/indexes/[index name]?api-version=2019-05-06-Preview
```

```json  
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

2020년 7월 15일 이전에 만든 서비스의 경우: 유사성을 생략하거나 null로 설정된 경우 인덱스는 이전 클래식 유사성 알고리즘을 사용합니다.

2020년 7월 15일 이후에 만든 서비스의 경우: 유사성을 생략하거나 null로 설정된 경우 인덱스는 새 BM25 유사성 알고리즘을 사용합니다.

또한 유사성 값을 *"#Microsoft.Azure.Search.ClassicSimilarity"* 또는 *"#Microsoft.Azure.Search.BM25유사성"* 중 하나로 명시적으로 설정할 수 있습니다.


## <a name="bm25-similarity-parameters"></a>BM25 유사성 매개 변수

BM25 유사성은 계산된 관련성 점수를 제어하기 위해 두 개의 사용자 사용자 지정 가능한 매개 변수를 추가합니다.

### <a name="k1"></a>k1

*k1* 매개 변수는 각 일치 하는 용어의 용어 주파수 문서 쿼리 쌍의 최종 관련성 점수 사이의 크기 조정 함수를 제어 합니다.

값이 0이면 "이진 모델"을 나타내며, 단일 일치 기간의 기여도는 텍스트에 표시되는 횟수에 관계없이 모든 일치하는 문서에 대해 동일하며, k1 값이 클수록 점수가 계속 증가할 수 있습니다. 동일한 용어의 인스턴스가 문서에서 찾을 수 있습니다. 기본적으로 Azure Cognitive Search는 k1 매개 변수에 대해 1.2 값을 사용합니다. 더 높은 k1 값을 사용하는 것은 여러 용어가 검색 쿼리의 일부가 될 것으로 예상되는 경우에 중요할 수 있습니다. 이러한 경우 단일 쿼리와 여러 번 일치하는 문서에서 검색되는 많은 다른 쿼리 용어와 일치하는 문서를 선호할 수 있습니다. 예를 들어, "아폴로 우주 비행"이라는 용어가 포함된 문서에 대한 인덱스를 쿼리할 때 "우주 비행"에 비해 "아폴로"라는 용어가 포함된 그리스 신화에 대한 기사의 점수를 수십 번 낮출 수 있습니다. 명시적으로 "아폴로"와 "우주 비행"시간의 소수를 모두 언급 하는 또 다른 기사. 
 
### <a name="b"></a>b

*b* 매개변수는 문서 길이가 관련성 점수에 미치는 영향을 제어합니다.

값이 0.0이면 문서 길이가 점수에 영향을 미치지 않고 값이 1.0이면 관련성 점수에 대한 용어 빈도의 영향이 문서 길이에 따라 정규화됨을 의미합니다. b 매개 변수에 대한 Azure 인지 검색에 사용되는 기본값은 0.75입니다. 문서 길이에 따라 빈도라는 용어를 정규화하면 더 긴 문서를 처벌하려는 경우에 유용합니다. 경우에 따라 더 긴 문서(예: 전체 소설)는 훨씬 짧은 문서에 비해 관련이 없는 용어가 많이 포함될 가능성이 높습니다.

### <a name="setting-k1-and-b-parameters"></a>k1 및 b 매개변수 설정

b 또는 k1 값을 사용자 지정하려면 BM25를 사용할 때 유사성 개체에 속성으로 추가하기만 하면 됩니다.

```json
    "similarity": {
        "@odata.type": "#Microsoft.Azure.Search.BM25Similarity",
        "b" : 0.5,
        "k1" : 1.3
    }
```

유사성 알고리즘은 인덱스 생성 시에만 설정할 수 있습니다. 즉, 사용 중인 유사성 알고리즘은 기존 인덱스에 대해 변경할 수 없습니다. BM25를 사용하는 기존 인덱스 정의를 업데이트할 때 *"b"* 및 *"k1"* 매개변수를 수정할 수 있습니다. 기존 인덱스에서 이러한 값을 변경하면 인덱스가 몇 초 이상 오프라인 상태가 되어 인덱싱 및 쿼리 요청이 실패합니다. 따라서 업데이트 요청의 쿼리 문자열에서 "allowIndexDowntime=true" 매개 변수를 설정해야 합니다.

```http
PUT https://[search service name].search.windows.net/indexes/[index name]?api-version=[api-version]&allowIndexDowntime=true
```


## <a name="see-also"></a>참조  

 [Azure 인지 검색 REST](https://docs.microsoft.com/rest/api/searchservice/)   
 [인덱스에 점수 매기기 프로필 추가](index-add-scoring-profiles.md)    
 [Azure 인지 검색 REST API&#41;&#40;인덱스 만들기](https://docs.microsoft.com/rest/api/searchservice/create-index)   
  [Azure 인지 검색 .NET SDK](https://docs.microsoft.com/dotnet/api/overview/azure/search?view=azure-dotnet)  
