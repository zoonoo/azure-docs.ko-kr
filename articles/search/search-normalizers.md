---
title: 필터, 패싯, 정렬에 대 한 텍스트 정규화
titleSuffix: Azure Cognitive Search
description: 인덱스의 텍스트 필드에 normalizers를 지정 하 여 필터링, 패싯 및 정렬에서 엄격한 키워드 일치 동작을 사용자 지정 합니다.
author: IshanSrivastava
manager: jlembicz
ms.author: ishansri
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/23/2021
ms.openlocfilehash: 3e7a33d9213d7af44d2cfc50baa847534618f7e5
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104609528"
---
# <a name="text-normalization-for-case-insensitive-filtering-faceting-and-sorting"></a>대/소문자를 구분 하지 않는 필터링, 패싯 및 정렬에 대 한 텍스트 정규화

 > [!IMPORTANT]
 > 노 멀 라이저는 **2020-06-30-preview REST API** 를 통해 제공 되는 공개 미리 보기로 제공 됩니다. 미리 보기 기능은 추가 사용 약관에서 있는 그대로 제공 됩니다.

Azure Cognitive Search 인덱스에서 문서를 검색 하 고 검색 하려면 쿼리를 문서 내용과 일치 해야 합니다. 매개 변수가 사용 되는 경우와 같이 일치 하는 토큰을 생성 하기 위해 콘텐츠를 분석 `search` 하거나,, 및와 같이 엄격한 키워드 일치에 그대로 사용할 수 있습니다 `$filter` `facets` `$orderby` . 이 전체 또는 없음 방법은 대부분의 시나리오에 적용 되지만, 전체 분석 체인을 거치지 않고 대/소문자 구분, 악센트 제거, asciifolding 등의 간단한 전처리가 필요한 경우에는 짧습니다.

다음 예제를 살펴보세요.

+ `$filter=City eq 'Las Vegas'` 는 대/소문자에 관계 없이 모든 문서를 사용 하는 경우 적절 하지 않은 "Las Vegas"와 "LAS VEGAS" 및 "Las Vegas"가 포함 된 문서를 반환 합니다.

+ `search=*&facet=City,count:5` 는 동일한 도시에도 불구 하 고 "Las Vegas", "LAS VEGAS" 및 "Las Vegas"를 고유 값으로 반환 합니다.

+ `search=usa&$orderby=City` 는 대/소문자에 관계 없이 동일한 도시를 함께 주문 하는 경우에도 사전순으로 "Las Vegas", "시애틀", "Las Vegas" 등의 도시를 반환 합니다. 

## <a name="normalizers"></a>Normalizers

*노 멀 라이저* 는 키워드 일치를 위한 텍스트 전처리를 담당 하는 검색 엔진의 구성 요소입니다. Normalizers는 쿼리를 토큰화 하지 않는다는 점을 제외 하 고 분석기와 비슷합니다. Normalizers를 사용 하 여 수행할 수 있는 일부 변환은 다음과 같습니다.

+ 소문자 또는 대문자로 변환 합니다.
+ Ö 또는 ê와 같은 악센트 및 분음 부호를 표준화 하는 ASCII 문자 "o" 및 "e"로 정규화 합니다.
+ 및 공백과 같은 문자를 `-` 사용자 지정 문자에 매핑합니다.

인덱스의 텍스트 필드에 Normalizers를 지정할 수 있으며 인덱싱 및 쿼리 실행 모두에 적용 됩니다.

## <a name="predefined-and-custom-normalizers"></a>미리 정의 된 및 사용자 지정 normalizers 

Azure Cognitive Search는 필요에 따라 사용자 지정 하는 기능과 함께 일반적인 사용 사례에 대해 미리 정의 된 normalizers를 지원 합니다.

| 범주 | 설명 |
|----------|-------------|
| [미리 정의 된 normalizers](#predefined-normalizers) | 기본 제공 되며, 구성 없이 사용할 수 있습니다. |
|[사용자 지정 normalizers](#add-custom-normalizers) | 고급 시나리오 에는 char 및 token 필터로 구성 된 기존 요소 조합에 대 한 사용자 정의 구성이 필요 합니다. <sup>1</sup>|

<sup>(1)</sup> normalizers는 항상 단일 토큰을 생성 하므로 Custom normalizers 토크 나이저를 지정 하지 않습니다.

## <a name="how-to-specify-normalizers"></a>Normalizers를 지정 하는 방법

Normalizers는 `Edm.String` `Collection(Edm.String)` `filterable` , `sortable` 또는 `facetable` 속성이 모두 true로 설정 된 텍스트 필드 (및)에서 필드 단위로 지정할 수 있습니다. 노 멀 라이저를 설정 하는 것은 선택 사항이 며 `null` 기본적으로입니다. 편의를 위해 사용자 지정 항목을 구성 하기 전에 미리 정의 된 normalizers를 평가 하는 것이 좋습니다. 결과가 예상과 다른 경우 다른 노 멀 라이저을 시도 하세요.

Normalizers는 새 필드가 인덱스에 추가 된 경우에만 지정할 수 있습니다. 인덱스를 삭제 하 고 다시 만들 때 초기 단계에서 표준화 요구 사항을 평가 하 고 normalizers을 할당 하는 것이 좋습니다. 이미 만들어진 필드에는 Normalizers을 지정할 수 없습니다.

1. [인덱스](/rest/api/searchservice/create-index)에서 필드 정의를 만들 때 **노 멀 라이저** 속성을와 같은 [미리 정의 된 노 멀 라이저](#predefined-normalizers) `lowercase` 또는 사용자 지정 노 멀 라이저 (동일한 인덱스 스키마에 정의 됨) 중 하나로 설정 합니다.  
 
   ```json
   "fields": [
    {
      "name": "Description",
      "type": "Edm.String",
      "retrievable": true,
      "searchable": true,
      "filterable": true,
      "analyzer": "en.microsoft",
      "normalizer": "lowercase"
      ...
    },
   ```

2. 사용자 지정 normalizers는 먼저 인덱스의 **[normalizers]** 섹션에서 정의 된 다음, 이전 단계와 같이 필드 정의에 할당 되어야 합니다. 자세한 내용은 [인덱스 만들기](/rest/api/searchservice/create-index) 및 [사용자 지정 normalizers 추가](#add-custom-normalizers)를 참조 하세요.


   ```json
   "fields": [
    {
      "name": "Description",
      "type": "Edm.String",
      "retrievable": true,
      "searchable": true,
      "analyzer": null,
      "normalizer": "my_custom_normalizer"
    },
   ```

 
> [!NOTE]
> 기존 필드의 노 멀 라이저를 변경 하려면 인덱스를 완전히 다시 작성 해야 합니다. 개별 필드는 다시 작성할 수 없습니다.

인덱스를 다시 작성 하는 데 비용이 많이 드는 프로덕션 인덱스에 대 한 좋은 해결 방법은 기존 필드와 동일한 새 필드를 만든 다음 새 노 멀 라이저를 사용 하는 것입니다. [인덱스 업데이트](/rest/api/searchservice/update-index)를 사용하여 새 필드를 통합하고 [mergeOrUpload](/rest/api/searchservice/addupdate-or-delete-documents)를 사용하여 새 필드를 채웁니다. 나중에 계획된 인덱스 서비스의 일환으로 인덱스를 정리하여 오래된 필드를 제거할 수 있습니다.

## <a name="add-custom-normalizers"></a>사용자 지정 normalizers 추가

사용자 지정 normalizers는 인덱스 스키마 내에 정의 되며 field 속성을 사용 하 여 지정할 수 있습니다. 사용자 지정 노 멀 라이저의 정의에는 이름, 형식, 하나 이상의 문자 필터 및 토큰 필터가 포함 됩니다. 문자 필터 및 토큰 필터는 사용자 지정 노 멀 라이저에 대 한 빌딩 블록이 며 텍스트 처리를 담당 합니다. 이러한 필터는 왼쪽에서 오른쪽으로 적용 됩니다.

 는 `token_filter_name_1` 토큰 필터의 이름이 고,는 `char_filter_name_1` `char_filter_name_2` 문자 필터의 이름입니다. 유효한 값은 아래의 [지원 되는 토큰 필터](#supported-token-filters) 및 문자 필터 표를 참조 하세요.

노 멀 라이저 정의는 큰 인덱스의 일부입니다. 나머지 인덱스에 대한 내용은 [인덱스 만들기 API](/rest/api/searchservice/create-index)를 참조하세요.

```
"normalizers":(optional)[
   {
      "name":"name of normalizer",
      "@odata.type":"#Microsoft.Azure.Search.CustomNormalizer",
      "charFilters":[
         "char_filter_name_1",
         "char_filter_name_2"
      ],
      "tokenFilters":[
         "token_filter_name_1
      ]
   }
],
"charFilters":(optional)[
   {
      "name":"char_filter_name_1",
      "@odata.type":"#char_filter_type",
      "option1":value1,
      "option2":value2,
      ...
   }
],
"tokenFilters":(optional)[
   {
      "name":"token_filter_name_1",
      "@odata.type":"#token_filter_type",
      "option1":value1,
      "option2":value2,
      ...
   }
]
```

사용자 지정 normalizers는 인덱스를 만들 때 또는 나중에 기존 항목을 업데이트 하 여 추가할 수 있습니다. 사용자 지정 노 멀 라이저를 기존 인덱스에 추가 하려면 [업데이트 인덱스](/rest/api/searchservice/update-index) 에서 **allowindexdowntime 중지 시간** 플래그를 지정 해야 하며,이 경우 몇 초 동안 인덱스를 사용할 수 없습니다.

## <a name="normalizers-reference"></a>Normalizers 참조

### <a name="predefined-normalizers"></a>미리 정의 된 normalizers
|**이름**|**설명 및 옵션**|  
|-|-|  
|표준| 텍스트 뒤에 asciifolding을 Lowercases.|  
|소문자| 문자를 소문자로 변환 합니다.|
|대문자| 문자를 대문자로 변환 합니다.|
|asciifolding| 기본 라틴어 유니코드 블록에 없는 문자를 해당 ASCII로 변환 합니다 (있는 경우). 예를 들어 à을로 변경 합니다.|
|elision| 토큰의 시작 부분에서 생략을 제거 합니다.|

### <a name="supported-char-filters"></a>지원 되는 문자 필터
Char 필터에 대 한 자세한 내용은 [Char 필터 참조](index-add-custom-analyzers.md#CharFilter)를 참조 하세요.
+ [매핑으로](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/charfilter/MappingCharFilter.html)  
+ [pattern_replace](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/pattern/PatternReplaceCharFilter.html)

### <a name="supported-token-filters"></a>지원 되는 토큰 필터
아래 목록은 normalizers에 대해 지원 되는 토큰 필터를 보여주고, 어휘 분석과 관련 된 전체 토큰 필터의 하위 집합입니다. 필터에 대 한 자세한 내용은 [토큰 필터 참조](index-add-custom-analyzers.md#TokenFilters)를 참조 하세요.

+ [arabic_normalization](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/ar/ArabicNormalizationFilter.html)
+ [asciifolding](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/miscellaneous/ASCIIFoldingFilter.html)
+ [cjk_width](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/cjk/CJKWidthFilter.html)  
+ [elision](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/util/ElisionFilter.html)  
+ [german_normalization](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/de/GermanNormalizationFilter.html)
+ [hindi_normalization](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/hi/HindiNormalizationFilter.html)  
+ [indic_normalization](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/in/IndicNormalizationFilter.html)
+ [persian_normalization](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/fa/PersianNormalizationFilter.html)
+ [scandinavian_normalization](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/miscellaneous/ScandinavianNormalizationFilter.html)  
+ [scandinavian_folding](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/miscellaneous/ScandinavianFoldingFilter.html)
+ [sorani_normalization](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/ckb/SoraniNormalizationFilter.html)  
+ [구분](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/LowerCaseFilter.html)
+ [바꾸는](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/UpperCaseFilter.html)


## <a name="custom-normalizer-example"></a>사용자 지정 노 멀 라이저 예제

아래 예제에서는 해당 하는 문자 필터 및 토큰 필터를 사용 하는 사용자 지정 노 멀 라이저 정의를 보여 줍니다. Char 필터 및 토큰 필터에 대 한 사용자 지정 옵션은 명명 된 구문으로 별도로 지정 된 다음 아래 그림과 같이 노 멀 라이저 정의에서 참조 됩니다.

* 사용자 지정 노 멀 라이저 "my_custom_normalizer"는 `normalizers` 인덱스 정의의 섹션에서 정의 됩니다.
* 노 멀 라이저은 두 개의 char 필터와 세 개의 토큰 필터 (생략, 소문자 및 사용자 지정 asciifolding 필터 "my_asciifolding")로 구성 됩니다.
* 첫 번째 문자 필터 "map_dash"는 모든 대시를 밑줄로 바꾸고 두 번째 문자 "remove_whitespace"는 모든 공백을 제거 합니다.

```json
  {
     "name":"myindex",
     "fields":[
        {
           "name":"id",
           "type":"Edm.String",
           "key":true,
           "searchable":false,
        },
        {
           "name":"city",
           "type":"Edm.String",
           "filterable": true,
           "facetable": true,
           "normalizer": "my_custom_normalizer"
        }
     ],
     "normalizers":[
        {
           "name":"my_custom_normalizer",
           "@odata.type":"#Microsoft.Azure.Search.CustomNormalizer",
           "charFilters":[
              "map_dash",
              "remove_whitespace"
           ],,
           "tokenFilters":[              
              "my_asciifolding",
              "elision",
              "lowercase",
           ]
        }
     ],
     "charFilters":[
        {
           "name":"map_dash",
           "@odata.type":"#Microsoft.Azure.Search.MappingCharFilter",
           "mappings":["-=>_"]
        },
        {
           "name":"remove_whitespace",
           "@odata.type":"#Microsoft.Azure.Search.MappingCharFilter",
           "mappings":["\\u0020=>"]
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
```

## <a name="see-also"></a>참고 항목
+ [언어 및 텍스트 처리를 위한 분석기](search-analyzers.md)

+ [문서 검색 REST API](/rest/api/searchservice/search-documents) 
