---
title: 부분 용어, 패턴 및 특수 문자
titleSuffix: Azure Cognitive Search
description: 와일드카드, 정규식 및 접두사 쿼리를 사용하여 Azure Cognitive Search 쿼리 요청의 전체 또는 일부 용어와 일치합니다. 전체 쿼리 구문 및 사용자 지정 분석기를 사용하여 특수 문자를 포함하는 일치하기 어려운 패턴을 해결할 수 있습니다.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 04/02/2020
ms.openlocfilehash: 7f001a0d443e4ec668aedaabb7505884163bf37e
ms.sourcegitcommit: 67addb783644bafce5713e3ed10b7599a1d5c151
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/05/2020
ms.locfileid: "80666778"
---
# <a name="partial-term-search-and-patterns-with-special-characters---azure-cognitive-search-wildcard-regex-patterns"></a>특수 문자가 있는 부분 용어 검색 및 패턴 - Azure 인지 검색(와일드카드, 정규식, 패턴)

*부분 용어 검색은* 문자열의 첫 번째, 마지막 또는 내부 부분과 같은 용어 조각으로 구성된 쿼리를 나타냅니다. *패턴은* 조각의 조합일 수 있으며, 때로는 쿼리의 일부인 대시 또는 슬래시와 같은 특수 문자가 있을 수 있습니다. 일반적인 사용 사례에는 전화 번호, URL, 사람 또는 제품 코드 또는 복합 단어의 일부에 대한 쿼리가 포함됩니다.

인덱스에 패턴 일치에 필요한 형식의 용어가 없는 경우 부분 검색에 문제가 있을 수 있습니다. 인덱싱의 텍스트 분석 단계에서 기본 표준 분석기를 사용하여 특수 문자가 삭제되고 복합 문자열과 복합 문자열이 분할되어 일치하는 항목이 없을 때 패턴 쿼리가 실패합니다. 예를 들어 해당 콘텐츠가 실제로 인덱스에 `"703"` `"6214"`존재하지 않기 때문에 (에 `"3-62"` `+1 (425) 703-6214` `"1"` `"425"`대한 토큰화)와 같은 전화 번호가 쿼리에 표시되지 않습니다. 

해결 방법은 필요한 경우 공백 및 특수 문자를 포함하여 전체 문자열을 보존하는 분석기를 호출하여 부분 용어 및 패턴을 지원할 수 있도록 하는 것입니다. 그대로 문자열에 대한 추가 필드를 만들고 콘텐츠 보존 분석기를 사용하는 것이 솔루션의 기본입니다.

## <a name="what-is-partial-search-in-azure-cognitive-search"></a>Azure 인지 검색의 부분 검색이란 무엇입니까?

Azure 인지 검색에서 부분 검색 및 패턴은 다음 형태로 사용할 수 있습니다.

+ [접두사 검색(](query-simple-syntax.md#prefix-search)예: `search=cap*`"Cap'n Jack's Waterfront Inn" 또는 "Gacc Capital")에서 일치합니다. 접두사 검색에 단순히 쿼리 구문을 사용할 수 있습니다.

+ [와일드카드 검색](query-lucene-syntax.md#bkmk_wildcard) 또는 접미사를 포함하여 포함된 문자열의 패턴 또는 일부를 검색하는 [정규식입니다.](query-lucene-syntax.md#bkmk_regex) 와일드카드 및 정규식에는 전체 Lucene 구문이 필요합니다. 

  부분 용어 검색의 몇 가지 예는 다음과 같습니다. 접미사 쿼리의 경우 "상숫자"라는 용어가 주어지면 와일드카드 검색()을`search=/.*numeric.*/`사용하여 일치를 찾습니다. URL 조각과 같은 문자를 포함하는 부분 용어의 경우 이스케이프 문자를 추가해야 할 수 있습니다. JSON에서는 뒤로 슬래시로 전진 슬래시가 `/` `\`이스케이프됩니다. 따라서 URL `search=/.*microsoft.com\/azure\/.*/` 조각 "microsoft.com/azure/"에 대한 구문입니다.

앞서 언급했듯이 위의 모든 인덱스에는 표준 분석기가 제공하지 않는 패턴 일치에 도움이 되는 형식의 문자열이 포함되어야 합니다. 이 문서의 단계를 수행하면 이러한 시나리오를 지원하는 데 필요한 콘텐츠가 있는지 확인할 수 있습니다.

## <a name="solving-partial-search-problems"></a>부분 검색 문제 해결

패턴 이나 특수 문자를 검색 해야 하는 경우 전체 문자열을 유지 하 여 간단한 토큰화 규칙에서 작동 하는 사용자 지정 분석기를 기본 분석기를 재정의할 수 있습니다. 한 걸음 뒤로 물러서면 다음과 같은 접근 방식이 보입니다.

+ 문자열의 손상되지 않은 버전을 저장할 필드 정의(분석 및 분석되지 않은 텍스트를 원한다고 가정).
+ 미리 정의된 분석기를 선택하거나 사용자 지정 분석기를 정의하여 손상되지 않은 문자열을 출력합니다.
+ 필드에 분석기 할당
+ 인덱스 빌드 및 테스트

> [!TIP]
> 분석기 평가는 빈번한 인덱스 재생이 필요한 반복 프로세스입니다. Postman, [인덱스 만들기에](https://docs.microsoft.com/rest/api/searchservice/create-index)대한 REST API, [인덱스 삭제,](https://docs.microsoft.com/rest/api/searchservice/delete-index)[문서 로드](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents)및 문서 검색을 사용하여 이 단계를 더 쉽게 수행할 수 [있습니다.](https://docs.microsoft.com/rest/api/searchservice/search-documents) 문서 로드의 경우 요청 본문에테스트할 작은 대표 데이터 집합(예: 전화 번호 또는 제품 코드가 있는 필드)이 포함되어야 합니다. 동일한 Postman 컬렉션에 이러한 API를 사용하면 이러한 단계를 빠르게 순환할 수 있습니다.

## <a name="duplicate-fields-for-different-scenarios"></a>다양한 시나리오에 대한 중복 필드

분석기는 필드별로 할당되므로 인덱스에 필드를 만들어 다양한 시나리오에 맞게 최적화할 수 있습니다. 특히 첫 번째 텍스트 검색과 두 번째 패턴 일치에 대한 일반 텍스트 검색을 지원하도록 "기능 코드" 및 "기능CodeRegex"를 정의할 수 있습니다.

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

## <a name="choose-an-analyzer"></a>분석기 선택

전체 기간 토큰을 생성하는 분석기를 선택할 때 다음과 같은 분석기는 일반적인 선택 사항입니다.

| 분석기 | 동작 |
|----------|-----------|
| [keyword](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/KeywordAnalyzer.html) | 전체 필드의 콘텐츠는 단일 용어로 토큰화됩니다. |
| [whitespace](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/WhitespaceAnalyzer.html) | 공백에서만 분리됩니다. 대시 또는 기타 문자를 포함하는 용어는 단일 토큰으로 처리됩니다. |
| [사용자 지정 분석기](index-add-custom-analyzers.md) | (권장) 사용자 지정 분석기를 만들면 토큰화기와 토큰 필터를 모두 지정할 수 있습니다. 이전 분석기를 있는 것으로 사용해야 합니다. 사용자 지정 분석기를 사용하면 사용할 토큰화기 및 토큰 필터를 선택할 수 있습니다. <br><br>권장되는 조합은 [소문자 토큰 필터가](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/LowerCaseFilter.html)있는 [키워드 토큰화입니다.](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/KeywordTokenizer.html) 그 자체로 미리 정의된 [키워드 분석기는](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/KeywordAnalyzer.html) 쿼리가 실패할 수 있는 대소문자 텍스트를 소문자로 사용하지 않습니다. 사용자 지정 분석기는 소문자 토큰 필터를 추가하기 위한 메커니즘을 제공합니다. |

Postman과 같은 웹 API 테스트 도구를 사용하는 경우 [테스트 분석기 REST 호출을](https://docs.microsoft.com/rest/api/searchservice/test-analyzer) 추가하여 토큰화된 출력을 검사할 수 있습니다.

작업할 기존 인덱스가 있어야 합니다. 기존 인덱스와 대시 또는 부분 용어가 포함된 필드를 감안할 때 특정 용어에 대해 다양한 분석기를 시도하여 어떤 토큰이 방출되는지 확인할 수 있습니다.  

1. 표준 분석기를 확인하여 용어가 기본적으로 토큰화되는 방식을 확인합니다.

   ```json
   {
   "text": "SVP10-NOR-00",
   "analyzer": "standard"
   }
    ```

1. 응답을 평가하여 텍스트가 인덱스 내에서 토큰화되는 방식을 확인합니다. 각 용어가 소문자화되고 어떻게 분리되는지 확인합니다.

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
1. `whitespace` 또는 `keyword` 분석기를 사용하도록 요청을 수정합니다.

    ```json
    {
    "text": "SVP10-NOR-00",
    "analyzer": "keyword"
    }
    ```

1. 이제 응답은 대문자로 구성된 단일 토큰으로 구성되며 대시는 문자열의 일부로 보존됩니다. 패턴 또는 부분 용어를 검색해야 하는 경우 쿼리 엔진은 이제 일치를 찾을 수 있는 기반을 제공합니다.


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
> 쿼리 트리를 작성할 때 쿼리 구문 분석기는 검색 식에서 종종 소문자 용어를 사용합니다. 소문자 텍스트 입력이 아닌 분석기를 사용하고 있고 예상 결과를 얻지 못하는 경우 이 유도가 될 수 있습니다. 해결 방법은 아래 "사용자 지정 분석기 사용" 섹션에 설명된 대로 소문자 토큰 필터를 추가하는 것입니다.

## <a name="configure-an-analyzer"></a>분석기 구성
 
분석기를 평가하든 특정 구성으로 진행하든 필드 정의에 분석기를 지정하고 기본 제공 분석기를 사용하지 않는 경우 분석기 자체를 구성해야 합니다. 분석기를 교환할 때일반적으로 인덱스를 다시 작성해야 합니다(삭제, 다시 만들기 및 다시 로드). 

### <a name="use-built-in-analyzers"></a>내장 분석기 사용

내장 또는 미리 정의된 분석기는 인덱스에 `analyzer` 추가 구성이 필요하지 않고 필드 정의의 속성에 이름으로 지정할 수 있습니다. 다음 예제에서는 필드에 `whitespace` 분석기를 설정하는 방법을 보여 줍니다. 사용 가능한 기본 제공 분석기에 대한 자세한 내용은 [미리 정의된 분석기 목록을](https://docs.microsoft.com/azure/search/index-add-custom-analyzers#predefined-analyzers-reference)참조하십시오. 

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

### <a name="use-custom-analyzers"></a>사용자 지정 분석기 사용

사용자 지정 분석기를 사용하는 경우 가능한 구성 설정과 함께 토큰화기, 토큰 필터의 사용자 정의 조합으로 인덱스에서 [정의합니다.](index-add-custom-analyzers.md) 그런 다음 기본 제공 분석기와 마찬가지로 필드 정의에서 참조합니다.

목표가 전체 기간 토큰화인 경우 **키워드 토큰화기와** **소문자 토큰 필터로** 구성된 사용자 지정 분석기를 권장합니다.

+ 키워드 토큰화는 필드의 전체 내용에 대해 단일 토큰을 만듭니다.
+ 소문자 토큰 필터는 대문자문자를 소문자 텍스트로 변환합니다. 쿼리 구문 분석자는 일반적으로 대문자 텍스트 입력을 소문자로 표시합니다. 낮은 대하는 토큰화된 용어로 입력을 균질화합니다.

다음 예제에서는 키워드 토큰화기 및 소문자 토큰 필터를 제공하는 사용자 지정 분석기를 보여 줍니다.

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
],

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
> `keyword_v2` 토큰화 및 `lowercase` 토큰 필터는 시스템에 알려져 있으며 기본 구성을 사용하므로 먼저 정의하지 않고도 이름으로 참조할 수 있습니다.

## <a name="build-and-test"></a>빌드 및 테스트

시나리오를 지원하는 분석기 및 필드 정의가 있는 인덱스를 정의한 후에는 부분 문자열 쿼리를 테스트할 수 있도록 대표 문자열이 있는 문서를 로드합니다. 

이전 섹션에서는 논리를 설명했습니다. 이 섹션에서는 솔루션을 테스트할 때 호출해야 하는 각 API를 단계별로 수행합니다. 앞서 언급했듯이 Postman과 같은 대화형 웹 테스트 도구를 사용하는 경우 이러한 작업을 신속하게 단계별로 수행할 수 있습니다.

+ [인덱스 삭제는](https://docs.microsoft.com/rest/api/searchservice/delete-index) 동일한 이름의 기존 인덱스를 제거하여 다시 만들 수 있습니다.

+ [인덱스 만들기는](https://docs.microsoft.com/rest/api/searchservice/create-index) 분석기 사양이 있는 분석기 정의 및 필드를 포함하여 검색 서비스에 인덱스 구조를 만듭니다.

+ [로드 문서는](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) 인덱스와 동일한 구조를 가진 문서와 검색 가능한 콘텐츠를 가져오습니다. 이 단계가 끝나면 인덱스를 쿼리하거나 테스트할 준비가 된 것입니다.

+ [테스트 분석기는](https://docs.microsoft.com/rest/api/searchservice/test-analyzer) [분석기 선택에서 도입되었습니다.](#choose-an-analyzer) 다양한 분석기를 사용하여 인덱스의 일부 문자열을 테스트하여 용어가 토큰화되는 방식을 이해합니다.

+ [Search Documents는](https://docs.microsoft.com/rest/api/searchservice/search-documents) 와일드카드 및 정규식에 대한 [간단한 구문](query-simple-syntax.md) 또는 [전체 Lucene 구문을](query-lucene-syntax.md) 사용하여 쿼리 요청을 생성하는 방법을 설명합니다.

## <a name="tips-and-best-practices"></a>팁과 모범 사례

### <a name="tune-query-performance"></a>쿼리 성능 조정

keyword_v2 토큰화 및 소문자 토큰 필터를 포함하는 권장 구성을 구현하는 경우 인덱스의 기존 토큰에 대한 추가 토큰 필터 처리로 인해 쿼리 성능이 저하될 수 있습니다. 

다음 예제는 접두사 일치를 더 빠르게 만들기 위해 [EdgeNGramTokenFilter를](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/ngram/EdgeNGramTokenizer.html) 추가합니다. 추가 토큰은 문자를 포함하는 2-25자 조합에 대해 생성됩니다(MS, MSF, MSFT, MSFT/S, MSFT/S, MSFT/SQ, MSFT/SQL). 당신이 상상할 수 있듯이, 추가 토큰화 결과 더 큰 인덱스.

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
],

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

### <a name="use-different-analyzers-for-indexing-and-query-processing"></a>인덱싱 및 쿼리 처리를 위해 다른 분석기 사용

분석기는 인덱싱 및 쿼리 실행 중에 호출됩니다. 둘 다에 대해 동일한 분석기를 사용하는 것이 일반적이지만 각 워크로드에 대해 사용자 지정 분석기를 구성할 수 있습니다. 분석기 재정의는 `analyzers` 단면의 인덱스 [정의에](https://docs.microsoft.com/rest/api/searchservice/create-index) 지정된 다음 특정 필드에서 참조됩니다. 

인덱싱 중에만 사용자 지정 분석이 필요한 경우 사용자 지정 분석기를 인덱싱에만 적용하고 쿼리에 표준 Lucene 분석기(또는 다른 분석기)를 계속 사용할 수 있습니다.

역할별 분석을 지정하려면 기본 `indexAnalyzer` `searchAnalyzer` `analyzer` 속성 대신 설정한 각 필드에 속성을 설정할 수 있습니다.

```json
"name": "featureCode",
"indexAnalyzer":"my_customanalyzer",
"searchAnalyzer":"standard",
```

## <a name="next-steps"></a>다음 단계

이 문서에서는 분석기 모두 쿼리 문제에 기여하고 쿼리 문제를 해결하는 방법을 설명합니다. 다음 단계로 인덱싱 및 쿼리 처리에 대한 분석기의 영향을 자세히 살펴봅니다. 특히 텍스트 분석 API를 사용하여 토큰화된 출력을 반환하여 분석기에서 인덱스에 대해 만드는 내용을 정확하게 확인할 수 있습니다.

+ [언어 분석기](search-language-support.md)
+ [Azure 인지 검색에서 텍스트 처리를 위한 분석기](search-analyzers.md)
+ [텍스트 API 분석(REST)](https://docs.microsoft.com/rest/api/searchservice/test-analyzer)
+ [전체 텍스트 검색 작동 방식(쿼리 아키텍처)](search-lucene-query-architecture.md)