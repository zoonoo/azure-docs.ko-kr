---
title: "Azure Search의 분석기 | Microsoft Docs"
description: "인덱스의 검색 가능한 텍스트 필드에 분석기를 할당하여 사용자 지정, 미리 정의 또는 언어 특정 대안으로 기본 표준 Lucene을 바꿉니다."
services: search
manager: jhubbard
author: HeidiSteen
documentationcenter: 
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 09/03/2017
ms.author: heidist
ms.translationtype: HT
ms.sourcegitcommit: ce0189706a3493908422df948c4fe5329ea61a32
ms.openlocfilehash: 6e6c4491b8f66011340d1246495dbded7caf2903
ms.contentlocale: ko-kr
ms.lasthandoff: 09/05/2017

---

# <a name="analyzers-in-azure-search"></a>Azure Search의 분석기

*분석기*는 [전체 텍스트 검색 처리](search-lucene-query-architecture.md)의 구성 요소로써 인덱싱 및 쿼리 작업 모두에 대한 텍스트-토큰 변환을 담당합니다. 인덱싱 중 분석기는 인덱스에 기록되는 토큰화된 용어로 텍스트를 변환합니다. 쿼리 시 분석기는 동일한 변환(텍스트를 토큰화된 용어로)을 수행하지만 이번에는 쿼리 중 읽기 작업을 대상으로 합니다. 

다음 변환은 분석 중에 일반적으로 발생합니다.

+ 필수적이지 않은 단어(중지 단어) 및 문장 부호가 제거됩니다.
+ 구 및 하이픈을 넣은 용어는 구성 요소 부분으로 분류됩니다.
+ 용어는 소문자입니다.
+ 단어는 시제에 관계 없이 일치를 찾을 수 있도록 루트 양식으로 세분화됩니다.

Azure Search는 기본 분석기를 제공합니다. 대안을 선택하여 필드를 기준으로 재정의할 수 있습니다. 이 문서의 목적은 선택한 범위를 설명하고 검색 작업에 분석기를 추가하기 위한 모범 사례를 제공하는 것입니다. 또한 주요 시나리오에 대한 예제 분석기 구성을 보여줍니다.

## <a name="how-analysis-fits-into-full-text-search-processing"></a>전체 텍스트 검색 처리에 분석을 적용하는 방법

분석기는 쿼리 파서에 의해 전달된 용어 입력에서 작동하고 쿼리 트리 개체에 추가되는 분석된 용어를 반환합니다.

 ![Azure Search의 Lucene 쿼리 아키텍처 다이어그램][1]

분석기는 단일 용어 쿼리 또는 구 쿼리에만 사용됩니다. 분석기는 접두사 쿼리, 와일드카드 쿼리, 정규식 쿼리 등 불완전한 용어가 사용된 쿼리 또는 유사 항목 쿼리인 쿼리 형식에는 사용되지 않습니다. 해당 쿼리 형식의 경우 용어는 쿼리 트리를 직접 추가되어 분석 단계를 우회합니다. 이러한 유형의 쿼리 용어에 수행되는 유일한 변환 작업은 소문자 변환입니다.

## <a name="supported-analyzers"></a>지원되는 분석기

다음 목록에서는 Azure Search에서 지원되는 분석기를 설명합니다.

| Category | 설명 |
|----------|-------------|
| [표준 Lucene 분석기](https://lucene.apache.org/core/4_0_0/analyzers-common/org/apache/lucene/analysis/standard/StandardAnalyzer.html) | 기본값 인덱싱 및 쿼리를 위해 자동으로 사용됩니다. 사양 또는 구성이 필요하지 않습니다. 이 범용 분석기는 대부분의 언어와 시나리오에서 잘 작동합니다.|
| 미리 정의된 분석기 | 제한된 사용자 지정을 그대로 사용하는 완성된 제품으로 제공됩니다. <br/>특수 및 언어와 같은 두 가지 형식이 있습니다. "미리 정의"하는 방법은 사용자 지정 없이 이름별로 참조하는 것입니다. <br/><br/>[특수(언어 중립적) 분석기](https://docs.microsoft.com/rest/api/searchservice/custom-analyzers-in-azure-search#AnalyzerTable)는 텍스트 입력에 특수 처리 또는 최소한의 처리를 필요로 합니다. 미리 정의된 비언어 분석기는 **Asciifolding**, **키워드**, **패턴**, **단순**, **중지**, **공백**을 포함합니다.<br/><br/>[언어 분석기](https://docs.microsoft.com/rest/api/searchservice/language-support)는 개별 언어에 대해 풍부한 언어 지원을 제공합니다. Azure Search는 35개의 Lucene 언어 분석기 및 50개의 Microsoft 자연어 처리 분석기를 지원합니다. |
|[사용자 지정 분석기](https://docs.microsoft.com/rest/api/searchservice/Custom-analyzers-in-Azure-Search) | 하나의 토크 나이저(필수) 및 선택적 필터(char 또는 토큰)로 구성된 기존 요소를 결합한 사용자 정의 구성입니다.|

**패턴** 또는 **중지**와 같은 미리 정의된 분석기를 사용자 지정하여 [미리 정의된 분석기 참조](https://docs.microsoft.com/rest/api/searchservice/custom-analyzers-in-azure-search#AnalyzerTable)에서 설명한 다른 옵션을 사용할 수 있습니다. 미리 정의된 분석기 중 일부에만 옵션을 설정할 수 있습니다. 사용자 지정으로 *myPatternAnalyzer*와 같은 이름을 가진 새 구성을 제공하여 Lucene 패턴 분석기와 구분합니다.

## <a name="how-to-specify-analyzer"></a>분석기를 지정하는 방법

1. 사용자 지정 분석기의 경우 인덱스에 `analyzer` 정의를 만듭니다. 자세한 내용은 [인덱스 만들기](https://docs.microsoft.com/rest/api/searchservice/create-index) 및 [사용자 지정 분석기 > 만들기](https://docs.microsoft.com/rest/api/searchservice/Custom-analyzers-in-Azure-Search#create-a-custom-analyzer)를 참조하세요.

2. 분석기를 사용하려는 각 필드에서 `analyzer` 속성을 [인덱스의 필드 정의](https://docs.microsoft.com/rest/api/searchservice/create-index)에 있는 대상 분석기의 이름으로 설정합니다. 유효한 값은 미리 정의된 분석기, 언어 분석기 또는 인덱스 스키마에 이전에 정의된 사용자 지정 분석기를 포함합니다.

 또는 `analyzer` 속성 대신 `indexAnalyzer` 및 `searchAnalyzer` 매개 변수 필드를 사용하여 인덱싱 및 쿼리에 대해 서로 다른 분석기를 설정할 수 있습니다. 

3. 새 텍스트 처리 동작을 호출하는 인덱스를 다시 빌드합니다.

## <a name="best-practices"></a>모범 사례

이 섹션에서는 분석기를 보다 효율적으로 사용하는 방법에 대한 조언을 제공합니다.

### <a name="one-analyzer-for-read-write-unless-you-have-specific-requirements"></a>특정 요구 사항이 없는 경우 읽기 쓰기에 대해 하나의 분석기

Azure Search는 추가적인 `indexAnalyzer` 및 `searchAnalyzer` 필드 매개 변수를 통해 인덱싱 및 검색에 서로 다른 분석기를 지정할 수 있습니다. 분석기를 지정하지 않으면 `analyzer` 속성을 통해 설정된 분석기가 인덱싱과 검색에 모두 사용됩니다. `analyzer`를 지정하지 않으면 기본 표준 Lucene 분석기가 사용됩니다.

특정 요구 사항이 따로 지정되지 않으면 인덱싱 및 쿼리 모두에 대해 동일한 분석기를 사용하는 것이 일반적인 규칙입니다. 일반적으로 토큰을 만드는 분석기가 쿼리 중 나중에 토큰을 찾는 데 사용되는 동일한 분석기인 경우에 더 효율적입니다. 

### <a name="test-during-active-development"></a>활성 개발 중 테스트

표준 분석기를 재정의하려면 인덱스를 다시 빌드해야 합니다. 가능하면 프로덕션 환경에 인덱스를 이동하기 전에 활성 개발 중 사용할 분석기를 결정합니다.

### <a name="compare-analyzers-side-by-side"></a>분석기 병렬 비교

[API 분석](https://docs.microsoft.com/rest/api/searchservice/test-analyzer)을 사용하는 것이 좋습니다. 응답은 제공한 텍스트에 특정 분석기에 의해 생성되므로 토큰화된 용어로 구성되어 있습니다. 

> [!Tip]
> [검색 분석기 데모](http://alice.unearth.ai/)에서는 표준 Lucene 분석기, Lucene의 영어 분석기 및 Microsoft의 영어 자연어 프로세서를 나란히 비교합니다. 제공한 각 검색 입력의 경우 각 분석기의 결과가 인접한 창에 표시됩니다.

## <a name="examples"></a>예

다음 예제에서는 몇 가지 주요 시나리오에 대한 분석기 정의를 보여줍니다.

<a name="Example1"></a>
### <a name="example-1-custom-options"></a>예제1: 사용자 지정 옵션

이 예제에서는 사용자 지정 옵션을 사용하는 분석기 정을 보여줍니다. char 필터, 토크나이저 및 토큰 필터에 대한 사용자 지정 옵션은 명명된 구문으로 개별 지정되고 분석기 정의에서 참조됩니다. 미리 정의된 요소도 그대로 사용되며 이름별로 참조하기만 하면 됩니다.

이 예제를 진행합니다.

* 분석기는 검색 가능한 필드에 대한 필드 클래스의 속성입니다.
* 사용자 지정 분석기는 인덱스 정의의 일부입니다. 가볍게 사용자 지정(예: 한 필터에서 단일 옵션 사용자 지정)되거나 여러 위치에서 사용자 지정될 수도 있습니다.
* 이 경우에 사용자 지정 분석기는 "my_analyzer"이며, 사용자 지정된 표준 토크나이저 "my_standard_tokenizer" 및 소문자 필터와 사용자 지정된 asciifolding 필터 "my_asciifolding"라는 두 개의 토큰 필터를 차례로 사용합니다.
* 또한 토크나이저 앞에 밑줄이 표시된 모든 대시를 바꾸는 사용자 지정 "map_dash" char 필터를 정의합니다(표준 토크나이저는 대시로 나뉘지만 밑줄이 없음).

~~~~
  {
     "name":"myindex",
     "fields":[
        {
           "name":"id",
           "type":"Edm.String",
           "key":true,
           "searchable":false
        },
        {
           "name":"text",
           "type":"Edm.String",
           "searchable":true,
           "analyzer":"my_analyzer"
        }
     ],
     "analyzers":[
        {
           "name":"my_analyzer",
           "@odata.type":"#Microsoft.Azure.Search.CustomAnalyzer",
           "charFilters":[
              "map_dash"
           ],
           "tokenizer":"my_standard_tokenizer",
           "tokenFilters":[
              "my_asciifolding",
              "lowercase"
           ]
        }
     ],
     "charFilters":[
        {
           "name":"map_dash",
           "@odata.type":"#Microsoft.Azure.Search.MappingCharFilter",
           "mappings":["-=>_"]
        }
     ],
     "tokenizers":[
        {
           "name":"my_standard_tokenizer",
           "@odata.type":"#Microsoft.Azure.Search.StandardTokenizer",
           "maxTokenLength":20
        }
     ],
     "tokenFilters":[
        {
           "name":"my_asciifolding",
           "@odata.type":"#Microsoft.Azure.Search.AsciiFoldingTokenFilter",
           "preserveOriginal":true
        }
     ]
  }
~~~~

<a name="Example2"></a>
### <a name="example-2-override-the-default-analyzer"></a>예제2: 기본 분석기 재정의

표준 분석기는 기본값입니다. 패턴 분석기와 같은 다른 미리 정의된 분석기로 기본값을 바꾸려 한다고 가정합니다. 사용자 지정 옵션을 설정하지 않은 경우 필드 정의에서 이름으로 지정하기만 하면 됩니다.

"analyzer" 요소에서 필드 기준으로 표준 분석기를 재정의합니다. 전역 재정의가 없습니다. 이 예제에서 `text1`는 패턴 분석기를 사용하고 분석기를 지정하지 않는 `text2`는 기본값을 사용합니다.

~~~~
  {
     "name":"myindex",
     "fields":[
        {
           "name":"id",
           "type":"Edm.String",
           "key":true,
           "searchable":false
        },
        {
           "name":"text1",
           "type":"Edm.String",
           "searchable":true,
           "analyzer":"pattern"
        },
        {
           "name":"text2",
           "type":"Edm.String",
           "searchable":true
        }
     ]
  }
~~~~

<a name="Example3"></a>
### <a name="example-3-different-analyzers-for-indexing-and-search-operations"></a>예제3: 인덱싱 및 검색 작업에 대한 여러 분석기

미리 보기 API는 인덱싱 및 검색에 대해 다른 분석기를 지정하기 위한 추가 인덱스 특성을 포함합니다. `searchAnalyzer` 및 `indexAnalyzer` 특성은 한 쌍으로 지정되어야 하며 단일 `analyzer` 특성을 바꿉니다.


~~~~
  {
     "name":"myindex",
     "fields":[
        {
           "name":"id",
           "type":"Edm.String",
           "key":true,
           "searchable":false
        },
        {
           "name":"text",
           "type":"Edm.String",
           "searchable":true,
           "indexAnalyzer":"whitespace",
           "searchAnalyzer":"simple"
        },
     ],
  }
~~~~

<a name="Example4"></a>
### <a name="example-4-language-analyzer"></a>예제4: 언어 분석기

다른 언어의 문자열을 포함하는 필드는 언어 분석기를 사용할 수 있지만 다른 필드는 기본값을 그대로 둡니다(또는 다른 미리 정의되거나 사용자 지정된 분석기 사용). 언어 분석기를 사용하는 경우 인덱싱 및 검색 작업에 사용해야 합니다. 언어 분석기를 사용하는 필드는 인덱싱 및 검색에 다른 분석기를 가질 수 없습니다.

~~~~
  {
     "name":"myindex",
     "fields":[
        {
           "name":"id",
           "type":"Edm.String",
           "key":true,
           "searchable":false
        },
        {
           "name":"text",
           "type":"Edm.String",
           "searchable":true,
           "IndexAnalyzer":"whitespace",
           "searchAnalyzer":"simple"
        },
        {
           "name":"text_fr",
           "type":"Edm.String",
           "searchable":true,
           "analyzer":"fr.lucene"
        }
     ],
  }
~~~~

## <a name="next-steps"></a>다음 단계

+ [전체 텍스트 검색이 Azure Search에서 작동하는 방식](search-lucene-query-architecture.md)의 포괄적인 설명을 검토하세요. 이 문서에서는 화면에서 직관적이지 않은 것처럼 보일 수 있는 동작을 설명하는 예제를 사용합니다.

+ 포털의 Search 탐색기에서 [문서 검색](https://docs.microsoft.com/rest/api/searchservice/search-documents#examples) 예제 섹션 또는 [단순 쿼리 구문](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search)의 추가 쿼리 구문을 사용해 보세요.

+ [언어별 어휘 분석기](https://docs.microsoft.com/rest/api/searchservice/language-support)를 적용하는 방법을 알아보세요.

+ 개별 필드에 대해 최소한의 처리 또는 특수한 처리를 수행하려면 [사용자 지정 분석기를 구성](https://docs.microsoft.com/rest/api/searchservice/custom-analyzers-in-azure-search)하세요.

+ 이 데모 웹 사이트의 인접한 창에서 [표준 및 영어 분석기를 나란히 비교](http://alice.unearth.ai/)하세요. 

## <a name="see-also"></a>참고 항목

 [문서 검색 REST API](https://docs.microsoft.com/rest/api/searchservice/search-documents) 

 [단순 쿼리 구문](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search) 

 [전체 Lucene 쿼리 구문](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search) 
 
 [검색 결과 처리](https://docs.microsoft.com/azure/search/search-pagination-page-layout)

<!--Image references-->
[1]: ./media/search-lucene-query-architecture/architecture-diagram2.png

