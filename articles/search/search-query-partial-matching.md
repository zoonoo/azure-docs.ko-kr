---
title: 부분 용어, 패턴 및 특수 문자
titleSuffix: Azure Cognitive Search
description: 와일드카드, 정규식 및 접두사 쿼리를 사용하여 Azure Cognitive Search 쿼리 요청에서 전체 또는 부분 용어를 일치시킵니다. 특수 문자를 포함하여 일치하기 어려운 패턴은 전체 쿼리 구문 및 사용자 지정 분석기를 사용하여 확인할 수 있습니다.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 12/03/2020
ms.openlocfilehash: 2e2625fff802e71f797bf6970e763f2bf11c393e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104584179"
---
# <a name="partial-term-search-and-patterns-with-special-characters-hyphens-wildcard-regex-patterns"></a>특수 문자(하이픈, 와일드카드, 정규식, 패턴)를 사용하는 부분 용어 검색 및 패턴

*부분 용어 검색* 은 용어 조각으로 구성된 쿼리를 의미합니다. 여기서 전체 용어 대신 용어의 시작, 중간 또는 끝(접두사, 중위 또는 접미사 쿼리라고도 함)을 사용할 수 있습니다. 부분 용어 검색에는 쿼리 문자열의 일부인 하이픈, 대시 또는 슬래시와 같은 특수 문자가 포함된 조각 조합이 포함될 수 있습니다. 일반적인 사용 사례에는 전화 번호, URL, 코드 또는 하이픈을 넣은 복합 단어의 일부가 포함됩니다.

인덱스에 필요한 형식의 토큰이 없으면 특수 문자를 포함하는 부분 용어 검색 및 쿼리 문자열에 문제가 있을 수 있습니다. 인덱싱의 [어휘 분석 단계](search-lucene-query-architecture.md#stage-2-lexical-analysis)(기본 표준 분석기로 가정) 중에 특수 문자가 무시되고, 복합 단어가 분할되고, 공백이 삭제됩니다. 일치하는 항목이 없으면 쿼리가 실패할 수 있습니다. 예를 들어 `+1 (425) 703-6214`와 같은 전화 번호(`"1"`, `"425"`, `"703"`, `"6214"`로 토큰화)는 실제로 인덱스에 존재하지 않기 때문에 `"3-62"` 쿼리에 표시되지 않습니다. 

이 솔루션은 쿼리 문자열에 공백과 문자를 포함할 수 있도록 필요한 경우 공백과 특수 문자를 포함하여 전체 문자열을 유지하는 인덱싱 중에 분석기를 호출합니다. 마찬가지로, 더 작은 부분으로 토큰화되지 않는 전체 문자열을 사용하면 "시작" 또는 "종료" 쿼리로 패턴을 일치할 수 있습니다. 여기서 제공하는 패턴은 어휘 분석에서 변환되지 않은 용어에 대해 평가할 수 있습니다. 그대로 유지되는 문자열에 대한 추가 필드를 만들고, 전체 용어 토큰을 내보내는 콘텐츠 유지 분석기를 사용하는 경우 패턴 일치를 위한 솔루션과 특수 문자를 포함하는 쿼리 문자열을 일치시킬 수 있습니다.

> [!TIP]
> Postman 및 REST API에 익숙한 경우 [쿼리 예제 컬렉션을 다운로드](https://github.com/Azure-Samples/azure-search-postman-samples/)하여 이 문서에 설명된 부분 용어와 특수 문자를 쿼리합니다.

## <a name="about-partial-term-search"></a>부분 용어 검색 정보

Azure Cognitive Search는 인덱스에서 전체 토큰화된 용어를 검색하고, 와일드카드 자리 표시자 연산자(`*` 및 `?`)를 포함하거나 쿼리 형식을 정규식으로 지정하지 않는 한 부분 용어에 대한 일치 항목을 찾을 수 없습니다. 부분 용어는 다음 기술을 사용하여 지정됩니다.

+ [정규식 쿼리](query-lucene-syntax.md#bkmk_regex)는 Apache Lucene에서 유효한 모든 정규식이 될 수 있습니다. 

+ [접두사가 일치하는 와일드카드 연산자](query-simple-syntax.md#prefix-search)는 "Cap'n Jack's Waterfront Inn" 또는 "Gacc Capital"와 일치하는 `search=cap*`와 같이 용어 앞 부분을 포함하며 `*` 또는 `?` 접미사 연산자가 따라오는 일반적으로 인식되는 패턴을 의미합니다. 접두사 일치는 단순 및 전체 Lucene 쿼리 구문에서 지원됩니다.

+ [중위 및 접미사가 일치하는 와일드카드](query-lucene-syntax.md#bkmk_wildcard)는 `*` 및 `?` 연산자를 용어의 시작 부분에 배치하고 정규식 구문(식 앞에 슬래시가 있는 경우)을 사용해야 합니다. 예를 들어 쿼리 문자열(`search=/.*numeric*./`)은 접미사 및 중위 일치로 "alphanumeric" 및 "alphanumerical"에 대한 결과를 반환합니다.

부분 용어 또는 패턴 검색의 경우 및 유사 항목 검색과 같은 몇 가지 다른 쿼리 형태는 쿼리 시에 사용되지 않습니다. 파서가 연산자와 구분 기호를 사용하여 검색하는 이러한 쿼리 폼의 경우 쿼리 문자열은 어휘 분석 없이 엔진으로 전달됩니다. 이러한 쿼리 양식의 경우 필드에 지정된 분석기는 무시됩니다.

> [!NOTE]
> 부분 쿼리 문자열에 URL 조각의 슬래시와 같은 문자가 포함된 경우 이스케이프 문자를 추가해야 할 수 있습니다. JSON에서 슬래시 `/`는 역슬래시 `\`로 이스케이프됩니다. 따라서 `search=/.*microsoft.com\/azure\/.*/`는 URL 조각 "microsoft.com/azure/"에 대한 구문입니다.

## <a name="solving-partialpattern-search-problems"></a>부분/패턴 검색 문제 해결

조각 또는 패턴 또는 특수 문자를 검색해야 하는 경우 더 간단한 토큰화 규칙에 따라 작동하는 사용자 지정분석기로 기본 분석기를 재정의하여 인덱스에 전체 문자열을 유지할 수 있습니다. 한 걸음 물러나 접근 방식은 다음과 같습니다.

1. 문자열의 원본 버전을 저장할 필드 정의(쿼리 시 분석 및 분석되지 않은 텍스트를 원하는 경우)
1. 적절한 세분성 수준에서 토큰을 내보내는 다양한 분석기를 평가하고 선택합니다.
1. 필드에 분석기를 할당합니다.
1. 인덱스 빌드 및 테스트

> [!TIP]
> 분석기 평가는 인덱스를 자주 다시 작성해야 하는 반복적인 프로세스입니다. Postman을 사용하여 이 단계를 더 쉽게 수행할 수 있습니다. [인덱스 만들기](/rest/api/searchservice/create-index), [인덱스 삭제](/rest/api/searchservice/delete-index), [문서 로드](/rest/api/searchservice/addupdate-or-delete-documents) 및 [문서 검색](/rest/api/searchservice/search-documents)을 위한 REST API입니다. 문서 로드의 경우 요청 본문에는 테스트하려는 작은 대표 데이터 세트(예: 전화 번호 또는 제품 코드를 포함하는 필드)가 포함되어야 합니다. 동일한 Postman 컬렉션에서 이러한 API를 사용하여 이러한 단계를 빠르게 순환할 수 있습니다.

## <a name="1---create-a-dedicated-field"></a>1 - 전용 필드 만들기

분석기는 인덱스에서 용어를 토큰화하는 방법을 결정합니다. 분석기는 필드 단위로 할당되므로 다양한 시나리오에 맞게 최적화하기 위해 인덱스에 필드를 만들 수 있습니다. 예를 들어 첫 번째에 대한 일반 전체 텍스트 검색을 지원하고 두 번째에 고급 패턴 일치를 지원하기 위해 "featureCode" 및 "featureCodeRegex"를 정의할 수 있습니다. 각 필드에 할당된 분석기는 각 필드의 내용이 인덱스에서 어떻게 토큰화되는지 결정합니다.  

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
  "analyzer": "my_custom_analyzer"
},
```

<a name="set-an-analyzer"></a>

## <a name="2---set-an-analyzer"></a>2 - 분석기 설정

전체 용어 토큰을 생성하는 분석기를 선택할 때 일반적으로 선택되는 분석기는 다음과 같습니다.

| 분석기 | 동작 |
|----------|-----------|
| [언어 분석기](index-add-language-analyzers.md) | 복합 단어 또는 문자열, 모음 변경 및 동사 양식에서 하이픈을 유지합니다. 쿼리 패턴에 대시가 포함되어 있으면 언어 분석기를 사용하는 것으로 충분할 수 있습니다. |
| [keyword](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/KeywordAnalyzer.html) | 전체 필드의 콘텐츠는 단일 용어로 토큰화됩니다. |
| [whitespace](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/WhitespaceAnalyzer.html) | 공백만 구분합니다. 대시 또는 다른 문자를 포함하는 용어는 단일 토큰으로 처리됩니다. |
| [사용자 지정 분석기](index-add-custom-analyzers.md) | (추천) 사용자 지정 분석기를 만들면 토크나이저와 토큰 필터를 모두 지정할 수 있습니다. 이전 분석기를 그대로 사용해야 합니다. 사용자 지정 분석기를 사용하여 사용할 토크나이저 및 토큰 필터를 선택할 수 있습니다. <br><br>권장되는 조합은 [소문자 토큰 필터](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/LowerCaseFilter.html)를 사용하는 [키워드 토크나이저](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/KeywordTokenizer.html)를 사용하는 것입니다. 기본적으로 기본 제공 [키워드 분석기](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/KeywordAnalyzer.html)는 대문자 텍스트를 소문자 텍스트로 바꾸지 않기 때문에 쿼리가 실패할 수 있습니다. 사용자 지정 분석기는 소문자 토큰 필터를 추가하는 메커니즘을 제공합니다. |

Postman과 같은 웹 API 테스트 도구를 사용하는 경우 [테스트 분석기 REST 호출](/rest/api/searchservice/test-analyzer)을 추가하여 토큰화된 출력을 검사할 수 있습니다.

작업할 채워진 인덱스가 있어야 합니다. 기존 인덱스 및 대시 또는 부분 용어를 포함하는 필드를 지정하는 경우 특정 조건에 대해 다양한 분석기를 사용해 보고 어떤 토큰을 내보낼지 확인할 수 있습니다.  

1. 먼저 표준 분석기를 확인하여 용어가 기본적으로 어떻게 토큰화되는지 확인합니다.

   ```json
   {
   "text": "SVP10-NOR-00",
   "analyzer": "standard"
   }
    ```

1. 응답을 평가하여 인덱스 내에서 텍스트가 어떻게 토큰화되는지 확인합니다. 각 용어가 어떻게 소문자로 바뀌고, 하이픈이 제거되고, 부분 문자열이 개별 토큰으로 분할되는지 확인합니다. 이러한 토큰과 일치하는 쿼리만이 문서를 결과에 반환합니다. "10-NOR"를 포함하는 쿼리는 실패합니다.

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
1. 이제 요청이 `whitespace`나 `keyword` 분석기를 사용하도록 수정합니다.

    ```json
    {
    "text": "SVP10-NOR-00",
    "analyzer": "keyword"
    }
    ```

1. 이제 응답은 문자열의 일부로 유지되는 대시가 유지된 단일 토큰과 대문자로 구성됩니다. 패턴이나 부분 용어(예: "10-NOR")를 검색해야 하는 경우 쿼리 엔진은 이제 일치 항목을 찾기 위한 기준을 갖습니다.


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
> 쿼리 트리를 작성할 때 쿼리 파서가 검색 식에서 소문자를 사용하는 경우가 종종 있습니다. 인덱싱하는 동안 소문자 텍스트 입력을 사용하지 않는 분석기를 사용하는 경우 이로 인해 예상되는 결과를 얻을 수 없을 수 있습니다. 아래의 "사용자 지정분석기 사용" 섹션에 설명된 대로 소문자 토큰 필터를 추가하면 해결됩니다.

## <a name="3---configure-an-analyzer"></a>3 - 분석기 구성
 
분석기를 평가하거나 특정 구성으로 전환하는 경우 필드 정의에 따라 분석기를 지정해야 하며, 기본 제공 분석기를 사용하지 않는 경우 분석기 자체를 구성해야 합니다. 분석기를 교환하는 경우 일반적으로 인덱스를 다시 빌드해야 합니다(삭제, 다시 만들기, 다시 로드). 

### <a name="use-built-in-analyzers"></a>기본 제공 분석기 사용

기본 제공 분석기는 인덱스에 추가 구성이 필요하지 않은 필드 정의의 `analyzer` 속성에 이름으로 지정할 수 있습니다. 다음 예에서는 필드에서 `whitespace` 분석기를 설정하는 방법을 보여 줍니다. 

다른 시나리오 및 다른 기본 제공분석기에 대해 자세히 알아보려면 [기본 제공 분석기](./index-add-custom-analyzers.md#built-in-analyzers)를 참조하세요. 

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

[사용자 지정 분석기](index-add-custom-analyzers.md)를 사용하는 경우 토크나이저, 토큰 필터와 가능한 구성 설정의 사용자 정의 조합을 사용하여 인덱스를 정의합니다. 그런 다음 기본 제공 분석기와 마찬가지로 이를 필드 정의에서 참조합니다.

목표가 전체 용어 토큰화인 경우 **키워드 토크나이저** 및 **소문자 토큰 필터로** 구성된 사용자 지정 분석기를 사용하는 것이 좋습니다.

+ 키워드 토크나이저는 필드의 전체 콘텐츠에 대한 단일 토큰을 생성합니다.
+ 소문자 토큰 필터는 대문자를 소문자 텍스트로 변환합니다. 일반적으로 쿼리 파서는 대문자 텍스트 입력을 모두 소문자로 바꿉니다. 소문자로 변환하면 입력을 토큰화된 용어로 통일합니다.

다음 예제에서는 키워드 토크나이저와 소문자 토큰 필터를 제공하는 사용자 지정 분석기를 보여줍니다.

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
> `keyword_v2` 토크나이저 및 `lowercase` 토큰 필터는 시스템에서 인식되며 기본 구성을 사용하여 이를 먼저 정의하지 않고도 이름으로 참조할 수 있습니다.

## <a name="4---build-and-test"></a>4 - 빌드 및 테스트

시나리오를 지원하는 분석기 및 필드 정의를 사용하여 인덱스를 정의한 후에는 부분 문자열 쿼리를 테스트할 수 있도록 대표 문자열이 있는 문서를 로드합니다. 

이전 섹션에서는 논리에 대해 설명했습니다. 이 섹션에서는 솔루션을 테스트할 때 호출해야 하는 각 API를 단계별로 안내합니다. 앞에서 설명한 것처럼 Postman과 같은 대화형 웹 테스트 도구를 사용하는 경우 이러한 작업을 신속하게 단계별로 수행할 수 있습니다.

+ [인덱스 삭제](/rest/api/searchservice/delete-index)를 사용하면 동일한 이름의 기존 인덱스를 다시 만들 수 있도록 인덱스를 제거합니다.

+ [인덱스 만들기](/rest/api/searchservice/create-index)를 사용하면 분석기 사양을 사용하여 분석기 정의 및 필드를 포함하여 검색 서비스에 인덱스 구조를 만듭니다.

+ [문서 로드](/rest/api/searchservice/addupdate-or-delete-documents)를 사용하면 인덱스와 구조가 동일한 문서 및 검색 가능한 콘텐츠를 가져옵니다. 이 단계를 수행한 후에는 인덱스를 쿼리하거나 테스트할 준비가 된 것입니다.

+ [분석기 테스트](/rest/api/searchservice/test-analyzer)는 [분석기 설정](#set-an-analyzer)에서 도입되었습니다. 다양한 분석기를 사용하여 인덱스의 일부 문자열을 테스트함으로써 용어를 토큰화하는 방법을 이해합니다.

+ [문서 검색](/rest/api/searchservice/search-documents) 에서는 와일드카드 및 정규식에 대한 [단순 구문](query-simple-syntax.md) 또는 [전체 Lucene 구문](query-lucene-syntax.md)을 사용하여 쿼리 요청을 생성하는 방법을 설명합니다.

  "+1 (425) 703-6214"와 일치하는 항목을 찾기 위해 "3-6214"를 쿼리하는 것과 같이 부분 용어 쿼리를 사용하는 경우 간단한 구문 `search=3-6214&queryType=simple`을 사용할 수 있습니다.

  "alphanumeric"과 일치하는 결과를 찾기 위해 "num" 또는 "numeric"을 쿼리하는 경우와 같이 중위 및 접미사 쿼리를 사용하는 경우 전체 Lucene 구문과 정규식 `search=/.*num.*/&queryType=full`을 사용합니다.

## <a name="tune-query-performance"></a>쿼리 성능 조정

Keyword_v2 토크나이저 및 소문자 토큰 필터를 포함하는 권장 구성을 구현하는 경우 인덱스의 기존 토큰에 대한 추가 토큰 필터 처리로 인해 쿼리 성능이 저하되는 것을 알 수 있습니다. 

다음 예에서는 접두사를 더 빠르게 일치시키는 [EdgeNGramTokenFilter](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/ngram/EdgeNGramTokenizer.html)을 추가합니다. 다음과 같은 문자를 포함하는 2~25 문자 조합에 대해 추가 토큰이 생성됩니다(MS, MSF, MSFT, MSFT/, MSFT/S, MSFT/SQ, MSFT/SQL 등). 

짐작할 수 있듯이 추가 토큰화를 사용하면 더 큰 인덱스가 나타납니다. 큰 인덱스를 수용할 수 있는 용량이 충분한 경우 응답 시간이 더 빠른 이 방법이 더 나은 해결책일 수 있습니다.

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

## <a name="next-steps"></a>다음 단계

이 문서에서는 분석기가 어떻게 쿼리 문제에 기여하고 쿼리 문제를 해결하는지 설명합니다. 다음 단계로 분석기가 인덱싱 및 쿼리 처리에 미치는 영향을 자세히 살펴보겠습니다. 특히 텍스트 API 분석을 사용하여 토큰화된 출력을 반환함으로써 분석기가 인덱스에 대해 무엇을 생성하는지 정확히 알 수 있도록 하는 것이 좋습니다.

+ [언어 분석기](search-language-support.md)
+ [Azure Cognitive Search의 텍스트 처리용 분석기](search-analyzers.md)
+ [텍스트 API 분석(REST)](/rest/api/searchservice/test-analyzer)
+ [전체 텍스트 검색 작동 방식(쿼리 아키텍처)](search-lucene-query-architecture.md)