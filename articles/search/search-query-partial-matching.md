---
title: 패턴 및 특수 문자 일치
titleSuffix: Azure Cognitive Search
description: 와일드 카드 및 접두사 쿼리를 사용 하 여 Azure Cognitive Search 쿼리 요청에서 전체 또는 부분 용어를 일치 시킵니다. 특수 문자를 포함 하는 일치 패턴은 전체 쿼리 구문 및 사용자 지정 분석기를 사용 하 여 확인할 수 있습니다.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/14/2020
ms.openlocfilehash: ec1422d03cce78bdd8206f6687a78b63ddf989dc
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/15/2020
ms.locfileid: "75989619"
---
# <a name="match-on-patterns-and-special-characters-dashes"></a>패턴 및 특수 문자 일치 (대시)

특수 문자 (`-, *, (, ), /, \, =`)를 포함 하는 쿼리 또는 큰 용어 내의 부분 용어를 기반으로 하는 쿼리 패턴의 경우, 일반적으로 인덱스에 올바른 형식으로 필요한 내용이 포함 되어 있는지 확인 하기 위해 추가 구성 단계가 필요 합니다. 

기본적으로 `+1 (425) 703-6214`와 같은 전화 번호는 `"1"`, `"425"`, `"703"`, `"6214"`으로 토큰화 됩니다. 짐작할 수 있듯이, 대시를 포함 하는 부분 용어 `"3-62"`검색은 인덱스에 실제로 존재 하지 않기 때문에 실패 합니다. 

부분 문자열이 나 특수 문자를 검색 해야 하는 경우에는 보다 간단한 토큰화 규칙에 따라 작동 하는 사용자 지정 분석기를 사용 하 여 기본 분석기를 재정의할 수 있습니다 .이 분석기를 사용 하면 전체 용어를 유지할 수 있습니다. 자를. 다시 한 단계를 수행 하는 방법은 다음과 같습니다.

+ 미리 정의 된 분석기를 선택 하거나 원하는 출력을 생성 하는 사용자 지정 분석기를 정의 합니다.
+ 필드에 분석기를 할당 합니다.
+ 인덱스를 빌드하고 테스트 합니다.

이 문서에서는 이러한 작업을 안내 합니다. 여기에 설명 된 방법은 다른 시나리오에서 유용 합니다. 와일드 카드 및 정규식 쿼리에는 패턴 일치를 위한 기준으로 전체 용어가 필요 합니다. 

> [!TIP]
> Analyers 평가는 자주 인덱스를 다시 작성 해야 하는 반복적인 프로세스입니다. Postman을 사용 하 여이 단계를 더 쉽게 수행할 수 있습니다. [인덱스 만들기](https://docs.microsoft.com/rest/api/searchservice/create-index), [인덱스 삭제](https://docs.microsoft.com/rest/api/searchservice/delete-index),[문서 로드](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents)및 [문서 검색](https://docs.microsoft.com/rest/api/searchservice/search-documents)을 위한 REST api입니다. 문서 로드의 경우 요청 본문에는 테스트 하려는 작은 대표 데이터 집합 (예: 전화 번호 또는 제품 코드를 포함 하는 필드)이 포함 되어야 합니다. 동일한 Postman 컬렉션에서 이러한 Api를 사용 하 여 이러한 단계를 빠르게 순환할 수 있습니다.

## <a name="choosing-an-analyzer"></a>분석기 선택

전체 용어 토큰을 생성 하는 분석기를 선택할 때 다음 분석기를 선택 하는 것이 일반적입니다.

| 분석기 | 동작 |
|----------|-----------|
| [keyword](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/KeywordAnalyzer.html) | 전체 필드의 콘텐츠는 단일 용어로 토큰화 됩니다. |
| [whitespace](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/WhitespaceAnalyzer.html) | 공백만을 구분 합니다. 대시 또는 다른 문자를 포함 하는 용어는 단일 토큰으로 처리 됩니다. |
| [사용자 지정 분석기](index-add-custom-analyzers.md) | 바람직하지 사용자 지정 분석기를 만들면 토크 및 token 필터를 모두 지정할 수 있습니다. 이전 분석기를 있는 그대로 사용 해야 합니다. 사용자 지정 분석기를 사용 하 여 사용할 토크 나이저 및 토큰 필터를 선택할 수 있습니다. <br><br>권장 되는 조합은 [소문자 토큰 필터](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/LowerCaseFilter.html)를 사용 하는 [키워드 토크](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/KeywordTokenizer.html) 를 사용 하는 것입니다. 미리 정의 된 [키워드인 analyzer](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/KeywordAnalyzer.html) 는 단독으로 소문자 텍스트를 따르지 않으므로 쿼리가 실패할 수 있습니다. 사용자 지정 분석기는 소문자 토큰 필터를 추가 하는 메커니즘을 제공 합니다. |

Postman과 같은 웹 API 테스트 도구를 사용 하는 경우 [테스트 분석기 REST 호출](https://docs.microsoft.com/rest/api/searchservice/test-analyzer) 을 추가 하 여 토큰화 된 출력을 검사할 수 있습니다. 기존 인덱스 및 대시 또는 부분 용어를 포함 하는 필드를 지정 하는 경우 특정 조건에 대해 다양 한 분석기를 시도 하 여 어떤 토큰을 내보낼지 확인할 수 있습니다.  

1. 표준 분석기를 확인 하 여 기본적으로 용어가 토큰화 되는 방법을 확인 합니다.

   ```json
   {
   "text": "SVP10-NOR-00",
   "analyzer": "standard"
   }
    ```

1. 응답을 평가 하 여 인덱스 내에서 텍스트가 토큰화 되는 방법을 확인 합니다. 각 용어는 소문자와 구분 되는 방식을 확인 합니다.

    ```json
    {
        "tokens": [
            {
                "token": "svp10",
                "startOffset": 0,
                "endOffset": 5,
                "position": 0
            },
            {
                "token": "nor",
                "startOffset": 6,
                "endOffset": 9,
                "position": 1
            },
            {
                "token": "00",
                "startOffset": 10,
                "endOffset": 12,
                "position": 2
            }
        ]
    }
    ```
1. `whitespace` 또는 `keyword` analyzer를 사용 하도록 요청을 수정 합니다.

    ```json
    {
    "text": "SVP10-NOR-00",
    "analyzer": "keyword"
    }
    ```

1. 이제 응답은 문자열의 일부로 유지 되는 대시가 포함 된 단일 토큰 (대문자)으로 구성 됩니다. 패턴 또는 부분을 검색 해야 하는 경우 쿼리 엔진은 이제 일치 하는 항목을 찾기 위한 기준을 갖습니다.


    ```json
    {

        "tokens": [
            {
                "token": "SVP10-NOR-00",
                "startOffset": 0,
                "endOffset": 12,
                "position": 0
            }
        ]
    }
    ```
> [!Important]
> 쿼리 트리를 작성할 때 쿼리 파서가 검색 식에서 소문자를 사용 하는 경우가 종종 있습니다. 소문자 텍스트 입력을 사용 하지 않는 분석기를 사용 하는 경우 예상 되는 결과를 얻지 못한 이유 때문일 수 있습니다. 이 솔루션은 lwower case 토큰 필터를 추가 하는 것입니다.

## <a name="analyzer-definitions"></a>분석기 정의
 
분석기를 평가 하거나 특정 구성으로 전환 하는 경우에는 필드 정의에 분석기를 지정 해야 하며, 기본 제공 분석기를 사용 하지 않는 경우 분석기 자체를 구성 해야 합니다. 분석기를 교환 하는 경우 일반적으로 인덱스를 다시 작성 해야 합니다 (삭제, 다시 만들기 및 다시 로드). 

### <a name="use-built-in-analyzers"></a>기본 제공 분석기 사용

기본 제공 되거나 미리 정의 된 분석기는 인덱스에 추가 구성이 필요 없는 필드 정의의 `analyzer` 속성에서 이름으로 지정할 수 있습니다. 다음 예에서는 필드에 `whitespace` analyzer를 설정 하는 방법을 보여 줍니다.

```json
    {
      "name": "phoneNumber",
      "type": "Edm.String",
      "key": false,
      "retrievable": true,
      "searchable": true,
      "analyzer": "whitespace"
    }
```
사용 가능한 모든 기본 제공 분석기에 대 한 자세한 내용은 [미리 정의 된 분석기 목록](https://docs.microsoft.com/azure/search/index-add-custom-analyzers#predefined-analyzers-reference)을 참조 하십시오. 

### <a name="use-custom-analyzers"></a>사용자 지정 분석기 사용

[사용자 지정 분석기](index-add-custom-analyzers.md)를 사용 하는 경우에는 가능한 구성 설정을 사용 하 여 사용자 정의 토크 토크, tokenfilter의 사용자 정의 조합을 사용 하 여 인덱스에이를 정의 합니다. 그런 다음 기본 제공 분석기와 마찬가지로 필드 정의에서 참조 합니다.

목표가 전체 용어 토큰화 인 경우 **키워드 토크** 및 **소문자 토큰 필터로** 구성 된 사용자 지정 분석기를 권장 합니다.

+ 키워드 토크는 필드의 전체 내용에 대 한 단일 토큰을 만듭니다.
+ 소문자 토큰 필터는 대문자를 소문자 텍스트로 변환 합니다. 일반적으로 쿼리 파서는 대문자 텍스트 입력을 모두 소문자로 바꿉니다. 소문자로는 토큰화 된 용어를 사용 하 여 입력을 homogenizes 합니다.

다음 예제에서는 키워드 토크 및 소문자 토큰 필터를 제공 하는 사용자 지정 분석기를 보여 줍니다.

```json
{
"fields": [
  {
  "name": "accountNumber",
  "analyzer":"myCustomAnalyzer",
  "type": "Edm.String",
  "searchable": true,
  "filterable": true,
  "retrievable": true,
  "sortable": false,
  "facetable": false
  }
]

"analyzers": [
  {
  "@odata.type":"#Microsoft.Azure.Search.CustomAnalyzer",
  "name":"myCustomAnalyzer",
  "charFilters":[],
  "tokenizer":"keyword_v2",
  "tokenFilters":["lowercase"]
  }
],
"tokenizers":[],
"charFilters": [],
"tokenFilters": []
```

> [!NOTE]
> `keyword_v2` 토크 및 `lowercase` token 필터는 시스템에서 인식 되며 기본 구성을 사용 하 여이를 먼저 정의 하지 않고도 이름으로 참조할 수 있습니다.

## <a name="tips-and-best-practices"></a>팁과 모범 사례

### <a name="tune-query-performance"></a>쿼리 성능 조정

Keyword_v2 토크 및 소문자 토큰 필터를 포함 하는 권장 구성을 구현 하는 경우 인덱스의 기존 토큰에 대 한 추가 토큰 필터 처리로 인해 쿼리 성능이 저하 되는 것을 알 수 있습니다. 

다음 예에서는 접두사를 더 빠르게 일치 시키는 [EdgeNGramTokenFilter](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/ngram/EdgeNGramTokenizer.html) 을 추가 합니다. 문자를 포함 하는 2-25 문자 조합에에 대 한 추가 토큰이 생성 됩니다 (예: MS, MSF, MSFT, MSFT/, MSFT/S, MSFT/SQ, MSFT/SQL). 짐작할 수 있듯이 추가 토큰화 결과는 더 큰 인덱스입니다.

```json
{
"fields": [
  {
  "name": "accountNumber",
  "analyzer":"myCustomAnalyzer",
  "type": "Edm.String",
  "searchable": true,
  "filterable": true,
  "retrievable": true,
  "sortable": false,
  "facetable": false
  }
]

"analyzers": [
  {
  "@odata.type":"#Microsoft.Azure.Search.CustomAnalyzer",
  "name":"myCustomAnalyzer",
  "charFilters":[],
  "tokenizer":"keyword_v2",
  "tokenFilters":["lowercase", "my_edgeNGram"]
  }
],
"tokenizers":[],
"charFilters": [],
"tokenFilters": [
  {
  "@odata.type":"#Microsoft.Azure.Search.EdgeNGramTokenFilterV2",
  "name":"my_edgeNGram",
  "minGram": 2,
  "maxGram": 25,
  "side": "front"
  }
]
```

### <a name="use-different-analyzers-for-indexing-and-query-processing"></a>인덱싱 및 쿼리 처리에 다른 분석기 사용

분석기는 인덱싱 중에 그리고 쿼리를 실행 하는 동안 호출 됩니다. 둘 다에 대해 동일한 분석기를 사용 하는 것이 일반적 이지만 각 워크 로드에 대해 사용자 지정 분석기를 구성할 수 있습니다. 분석기 재정의는 `analyzers` 섹션의 [인덱스 정의](https://docs.microsoft.com/rest/api/searchservice/create-index) 에 지정 된 다음 특정 필드에서 참조 됩니다. 

인덱싱을 수행 하는 동안 사용자 지정 분석을 수행 해야 하는 경우에는 인덱싱에만 사용자 지정 분석기를 적용 하 고 쿼리에 표준 Lucene analyzer (또는 다른 분석기)를 계속 사용할 수 있습니다.

역할별 분석을 지정 하려면 각 속성에 대 한 필드에 속성을 설정 하 고 기본 `analyzer` 속성 대신 `indexAnalyzer` 및 `searchAnalyzer`를 설정 하면 됩니다.

```json
"name": "featureCode",
"indexAnalyzer":"my_customanalyzer",
"searchAnalyzer":"standard",
```

### <a name="duplicate-fields-for-different-scenarios"></a>여러 시나리오에 대 한 중복 필드

또 다른 옵션은 각 시나리오에 맞게 최적화 하기 위해 필드별 분석기 할당을 활용 합니다. 특히 "featureCode" 및 "featureCodeRegex"를 정의 하 여 첫 번째의 전체 텍스트 검색 및 두 번째의 고급 패턴 일치를 지원할 수 있습니다.

```json
{
  "name": "featureCode",
  "type": "Edm.String",
  "retrievable": true,
  "searchable": true,
  "analyzer": null
},
{
  "name": "featureCodeRegex",
  "type": "Edm.String",
  "retrievable": true,
  "searchable": true,
  "analyzer": "my_customanalyzer"
},
```

## <a name="next-steps"></a>다음 단계

이 문서에서는 분석기에서 쿼리 문제를 해결 하 고 쿼리 문제를 해결 하는 방법을 설명 합니다. 다음 단계로 인덱싱 및 쿼리 처리에 대 한 분석기의 영향을 자세히 살펴보겠습니다. 특히 분석기가 인덱스에 대해 만드는 작업을 정확히 확인할 수 있도록 분석 텍스트 API를 사용 하 여 토큰화 된 출력을 반환 하는 것이 좋습니다.

+ [언어 분석기](search-language-support.md)
+ [Azure Cognitive Search에서 텍스트 처리를 위한 분석기](search-analyzers.md)
+ [텍스트 API 분석 (REST)](https://docs.microsoft.com/rest/api/searchservice/test-analyzer)
+ [전체 텍스트 검색의 작동 방식 (쿼리 아키텍처)](search-lucene-query-architecture.md)