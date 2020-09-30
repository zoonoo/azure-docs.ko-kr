---
title: '자습서: 사용자 지정 분석기 만들기'
titleSuffix: Azure Cognitive Search
description: Azure Cognitive Search에서 검색 결과의 품질을 향상시키기 위해 사용자 지정 분석기를 빌드하는 방법을 알아봅니다.
manager: liamca
author: dereklegenzoff
ms.author: delegenz
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 09/25/2020
ms.openlocfilehash: ac7cee2c1d72b4102fb397aa8093c2d38686fc88
ms.sourcegitcommit: 4313e0d13714559d67d51770b2b9b92e4b0cc629
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/27/2020
ms.locfileid: "91397269"
---
# <a name="tutorial-create-a-custom-analyzer-for-phone-numbers"></a>자습서: 전화 번호에 대한 사용자 지정 분석기 만들기

[분석기](search-analyzers.md)는 모든 검색 솔루션의 주요 구성 요소입니다. 검색 결과의 품질을 향상시키려면 분석기가 작동하는 방식과 이러한 결과에 미치는 영향을 이해하는 것이 중요합니다.

경우에 따라 사용 가능한 텍스트 필드와 같이 올바른 [언어 분석기](index-add-language-analyzers.md)를 선택하기만 하면 검색 결과가 향상됩니다. 그러나 전화 번호, URL 또는 이메일을 정확하게 검색하는 것과 같은 일부 시나리오에서는 사용자 지정 분석기를 사용해야 할 수도 있습니다.

이 자습서에서는 Postman 및 Azure Cognitive Search의 [REST API](/rest/api/searchservice/)를 사용하여 다음을 수행합니다.

> [!div class="checklist"]
> * 분석기의 작동 방식 설명
> * 전화 번호 검색을 위한 사용자 지정 분석기 정의
> * 사용자 지정 분석기에서 텍스트를 토큰화하는 방법 테스트
> * 결과를 더 향상시키기 위해 별도의 인덱싱 및 검색 분석기 만들기

## <a name="prerequisites"></a>필수 구성 요소

이 자습서에 필요한 서비스와 도구는 다음과 같습니다.

+ [Postman 데스크톱 앱](https://www.getpostman.com/)
+ [만들기](search-create-service-portal.md) 또는 [기존 검색 서비스 찾기](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices)

## <a name="download-files"></a>파일 다운로드

이 자습서의 소스 코드는 [Azure-Samples/azure-search-postman-samples](https://github.com/Azure-Samples/azure-search-postman-samples) GitHub 리포지토리의 [custom-analyzers](https://github.com/Azure-Samples/azure-search-postman-samples/tree/master/custom-analyzers) 폴더에 있습니다.

## <a name="1---create-azure-cognitive-search-service"></a>1 - Azure Cognitive Search 서비스 만들기

이 자습서를 완료하려면 [포털에서 만들](search-create-service-portal.md) 수 있는 Azure Cognitive Search 서비스가 필요합니다. 이 연습은 체험 계층을 사용하여 완료할 수 있습니다.

다음 단계에서는 검색 서비스의 이름 및 해당 API 키를 알고 있어야 합니다. 이러한 항목을 찾는 방법을 잘 모르는 경우 이 [빠른 시작](search-create-service-portal.md#get-a-key-and-url-endpoint)을 확인하세요.


## <a name="2---set-up-postman"></a>2 - Postman 설정

다음으로, Postman을 시작하고 [Azure-Samples/azure-search-postman-samples](https://github.com/Azure-Samples/azure-search-postman-samples)에서 다운로드한 컬렉션을 가져옵니다.

컬렉션을 가져오려면 **파일** > **가져오기**로 차례로 이동한 다음, 가져올 컬렉션 파일을 선택합니다.

각 요청에 대해 다음을 수행해야 합니다.

1. `<YOUR-SEARCH-SERVICE>`을 검색 서비스의 이름으로 바꿉니다.

1. `<YOUR-ADMIN-API-KEY>`를 검색 서비스의 기본 키 또는 보조 키로 바꿉니다.

  :::image type="content" source="media/search-get-started-postman/postman-url.png" alt-text="Postman 요청 URL 및 헤더" border="false":::

Postman에 익숙하지 않은 경우 [Postman을 사용하여 Azure Cognitive Search REST API 살펴보기](search-get-started-postman.md)를 참조하세요.

## <a name="3---create-an-initial-index"></a>3 - 초기 인덱스 만들기

이 단계에서는 초기 인덱스를 만들고, 문서를 인덱스에 로드한 다음, 문서를 쿼리하여 초기 검색의 수행 방식을 확인합니다.

### <a name="create-index"></a>인덱스 만들기

먼저 `id` 및 `phone_number`의 두 개의 필드를 사용하여 `tutorial-basic-index`라는 간단한 인덱스를 만듭니다. 분석기를 아직 정의하지 않았으므로 `standard.lucene` 분석기가 기본적으로 사용됩니다.

인덱스를 만들기 위해 다음 요청을 보냅니다.

```http
PUT https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/indexes/tutorial-basic-index?api-version=2019-05-06
  Content-Type: application/json
  api-key: <YOUR-ADMIN-API-KEY>

  {
    "fields": [
      {
        "name": "id",
        "type": "Edm.String",
        "key": true,
        "searchable": true,
        "filterable": false,
        "facetable": false,
        "sortable": true
      },
      {
        "name": "phone_number",
        "type": "Edm.String",
        "sortable": false,
        "searchable": true,
        "filterable": false,
        "facetable": false
      }
    ]
  }
```

### <a name="load-data"></a>데이터 로드

다음으로, 데이터를 인덱스에 로드합니다. 경우에 따라 수집되는 전화 번호의 형식을 제어하지 못할 수 있으므로 다양한 종류의 형식을 테스트합니다. 검색 솔루션에서 형식에 관계없이 일치하는 모든 전화 번호를 반환하는 것이 가장 좋습니다.

데이터는 다음 요청을 사용하여 인덱스에 로드됩니다. 

```http
POST https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/indexes/tutorial-basic-index/docs/index?api-version=2019-05-06
  Content-Type: application/json
  api-key: <YOUR-ADMIN-API-KEY>

  {
    "value": [
      {
        "@search.action": "upload",  
        "id": "1",
        "phone_number": "425-555-0100"
      },
      {
        "@search.action": "upload",  
        "id": "2",
        "phone_number": "(321) 555-0199"
      },
      {  
        "@search.action": "upload",  
        "id": "3",
        "phone_number": "+1 425-555-0100"
      },
      {  
        "@search.action": "upload",  
        "id": "4",  
        "phone_number": "+1 (321) 555-0199"
      },
      {
        "@search.action": "upload",  
        "id": "5",
        "phone_number": "4255550100"
      },
      {
        "@search.action": "upload",  
        "id": "6",
        "phone_number": "13215550199"
      },
      {
        "@search.action": "upload",  
        "id": "7",
        "phone_number": "425 555 0100"
      },
      {
        "@search.action": "upload",  
        "id": "8",
        "phone_number": "321.555.0199"
      }
    ]  
  }
```

인덱스의 데이터를 사용하여 검색을 시작할 준비가 되었습니다.

### <a name="search"></a>검색

검색을 직관적으로 만들려면 사용자가 쿼리 형식을 특정 방식으로 지정하지 않는 것이 가장 좋습니다. 사용자는 `(425) 555-0100`을 위에서 보여 주는 형식 중 하나로 검색할 수 있으며 결과가 계속 반환될 것으로 예상합니다. 이 단계에서는 몇 가지 쿼리 샘플을 테스트하여 해당 쿼리의 수행 방식을 확인합니다.

먼저 `(425) 555-0100`을 검색합니다.

```http
GET https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/indexes/tutorial-basic-index/docs?api-version=2019-05-06&search=(425) 555-0100
  Content-Type: application/json
  api-key: <YOUR-ADMIN-API-KEY>  
```

이 쿼리는 **4개의 예상 결과 중 3개**를 반환하지만, **2개의 예기치 않은 결과**도 반환합니다.

```json
{
    "value": [
        {
            "@search.score": 0.05634898,
            "phone_number": "+1 425-555-0100"
        },
        {
            "@search.score": 0.05634898,
            "phone_number": "425 555 0100"
        },
        {
            "@search.score": 0.05634898,
            "phone_number": "425-555-0100"
        },
        {
            "@search.score": 0.020766128,
            "phone_number": "(321) 555-0199"
        },
        {
            "@search.score": 0.020766128,
            "phone_number": "+1 (321) 555-0199"
        }
    ]
}
```

다음으로, 형식이 지정되지 않은 `4255550100` 숫자를 검색해 보겠습니다.

```http
GET https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/indexes/tutorial-basic-index/docs?api-version=2019-05-06&search=4255550100
  api-key: <YOUR-ADMIN-API-KEY>
```

이 쿼리는 **4개의 올바른 일치 항목 중 하나**만 반환하므로 더 나쁩니다.

```json
{
    "value": [
        {
            "@search.score": 0.6015292,
            "phone_number": "4255550100"
        }
    ]
}
```

이러한 결과는 누구에게나 혼란스럽습니다. 다음 섹션에서는 이러한 결과가 발생하는 이유에 대해 자세히 살펴보겠습니다.

## <a name="4---debug-search-results"></a>4 - 검색 결과 디버그

이러한 검색 결과를 이해하려면 먼저 분석기의 작동 방식을 이해해야 합니다. 여기서는 [텍스트 분석 API](/rest/api/searchservice/test-analyzer)를 사용하여 기본 분석기를 테스트한 다음, 요구 사항을 충족하는 분석기를 만들 수 있습니다.

### <a name="how-analyzers-work"></a>분석기의 작동 방식

[분석기](search-analyzers.md)는 쿼리 문자열과 인덱싱된 문서의 텍스트를 처리하는 [전체 텍스트 검색 엔진](search-lucene-query-architecture.md)의 구성 요소입니다. 시나리오에 따라 여러 분석기가 다양한 방법으로 텍스트를 조작합니다. 이 시나리오에서는 전화 번호에 맞게 조정되는 분석기를 빌드해야 합니다.

분석기를 구성하는 세 가지 구성 요소는 다음과 같습니다.

+ [**문자 필터**](#CharFilters) - 입력 텍스트에서 개별 문자를 제거하거나 바꿉니다.
+ [**토크나이저**](#Tokenizers) - 입력 텍스트를 토큰으로 분할합니다. 이 토큰은 검색 인덱스의 키가 됩니다.
+ [**토큰 필터**](#TokenFilters) - 토크나이저에서 생성된 토큰을 조작합니다.

아래 다이어그램에서는 이러한 세 가지 구성 요소가 함께 작동하여 문장을 토큰화하는 방법을 확인할 수 있습니다.

  :::image type="content" source="media/tutorial-create-custom-analyzer/analyzers-explained.png" alt-text="Postman 요청 URL 및 헤더":::

그런 다음, 이러한 토큰은 반전된 인덱스에 저장되어 전체 텍스트를 빠르게 검색할 수 있도록 허용합니다.  반전된 인덱스는 어휘 분석 중에 추출된 모든 고유한 용어를 해당 용어가 발생한 문서에 매핑하여 전체 텍스트 검색을 가능하게 합니다. 아래 다이어그램에서는 이 예를 확인할 수 있습니다.

  :::image type="content" source="media/tutorial-create-custom-analyzer/inverted-index-explained.png" alt-text="Postman 요청 URL 및 헤더":::

모든 검색에서 반전된 인덱스에 저장된 용어를 검색하게 됩니다. 사용자가 쿼리를 실행하는 경우 다음과 같이 수행됩니다.

1. 쿼리를 구문 분석하고 쿼리 용어를 분석합니다.
1. 그런 다음, 반전된 인덱스에서 일치하는 용어가 있는 문서를 검색합니다.
1. 마지막으로, [유사성 알고리즘](index-ranking-similarity.md)에 따라 검색된 문서의 순위를 매깁니다.

  :::image type="content" source="media/tutorial-create-custom-analyzer/query-architecture-explained.png" alt-text="Postman 요청 URL 및 헤더":::

쿼리 용어가 반전된 인덱스의 용어와 일치하지 않으면 결과가 반환되지 않습니다. 쿼리가 작동하는 방법에 대한 자세한 내용은 [전체 텍스트 검색에 대한 이 문서](search-lucene-query-architecture.md)를 참조하세요.

> [!Note]
> [부분 용어 쿼리](search-query-partial-matching.md)는 이 규칙의 중요한 예외입니다. 이러한 쿼리(접두사 쿼리, 와일드카드 쿼리, 정규식 쿼리)는 일반 용어 쿼리와 달리 어휘 분석 프로세스를 무시합니다. 부분 용어는 소문자로 처리한 후에만 인덱스의 용어와 일치시킵니다. 분석기가 이러한 유형의 쿼리를 지원하도록 구성되지 않으면 일치하는 용어가 인덱스에 없으므로 예기치 않은 결과가 발생하는 경우가 많습니다.

### <a name="test-analyzer-using-the-analyze-text-api"></a>텍스트 분석 API를 사용하여 분석기 테스트

Azure Cognitive Search는 분석기를 테스트하여 분석기에서 텍스트를 처리하는 방법을 이해할 수 있도록 하는 [텍스트 분석 API](/rest/api/searchservice/test-analyzer)를 제공합니다.

텍스트 분석 API는 다음 요청을 사용하여 호출됩니다.

```http
POST https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/indexes/tutorial-basic-index/analyze?api-version=2019-05-06
  Content-Type: application/json
  api-key: <YOUR-ADMIN-API-KEY>

  {
      "text": "(425) 555-0100",
      "analyzer": "standard.lucene"
  }
```

그런 다음, API는 텍스트에서 추출된 토큰의 목록을 반환합니다. 표준 Lucene 분석기에서 전화 번호를 세 개의 개별 토큰으로 분할하는 것을 알 수 있습니다.

```json
{
    "tokens": [
        {
            "token": "425",
            "startOffset": 1,
            "endOffset": 4,
            "position": 0
        },
        {
            "token": "555",
            "startOffset": 6,
            "endOffset": 9,
            "position": 1
        },
        {
            "token": "0100",
            "startOffset": 10,
            "endOffset": 14,
            "position": 2
        }
    ]
}
```

반대로, 문장 부호 없이 형식이 지정된 `4255550100` 전화 번호는 단일 토큰으로 토큰화됩니다.

```json
{
  "text": "4255550100",
  "analyzer": "standard.lucene"
}
```

```json
{
    "tokens": [
        {
            "token": "4255550100",
            "startOffset": 0,
            "endOffset": 10,
            "position": 0
        }
    ]
}
```

쿼리 용어와 인덱싱된 문서가 모두 분석됩니다. 이전 단계의 검색 결과를 다시 생각해 보면 이러한 결과가 반환된 이유를 확인할 수 있습니다.

첫 번째 쿼리에서는 해당 용어 중 하나인 `555`가 검색한 용어 중 하나와 일치하므로 잘못된 전화 번호가 반환되었습니다. 두 번째 쿼리에서는 `4255550100`과 일치하는 용어가 있는 유일한 레코드이므로 하나의 숫자만 반환되었습니다.

## <a name="5---build-a-custom-analyzer"></a>5 - 사용자 지정 분석기 빌드

이제 여기서 보여 주는 결과를 이해했으므로 토큰화 논리를 향상시키도록 사용자 지정 분석기를 빌드해 보겠습니다.

목표는 쿼리 또는 인덱싱된 문자열의 형식에 관계없이 전화 번호를 직관적으로 검색하는 것입니다. 이 결과를 얻기 위해 [문자 필터](#CharFilters), [토크나이저](#Tokenizers) 및 [토큰 필터](#TokenFilters)를 지정합니다.

<a name="CharFilters"></a>

### <a name="character-filters"></a>문자 필터

문자 필터는 텍스트를 토크나이저로 보내기 전에 처리하는 데 사용됩니다. 일반적으로 문자 필터는 HTML 요소를 필터링하거나 특수 문자를 대체하는 데 사용됩니다.

전화 번호의 경우 동일한 특수 문자 및 공백이 모든 전화 번호 형식에 포함되어 있지 않으므로 공백 및 특수 문자를 제거하려고 합니다.

```json
"charFilters": [
    {
      "@odata.type": "#Microsoft.Azure.Search.MappingCharFilter",
      "name": "phone_char_mapping",
      "mappings": [
        "-=>",
        "(=>",
        ")=>",
        "+=>",
        ".=>",
        "\\u0020=>"
      ]
    }
  ]
```

위의 필터는 입력에서 `-`, `(`, `)`, `+`, `.` 및 공백을 제거합니다.

|입력|출력|  
|-|-|  
|`(321) 555-0199`|`3215550199`|  
|`321.555.0199`|`3215550199`|

<a name="Tokenizers"></a>

### <a name="tokenizers"></a>토크나이저

토크나이저는 텍스트를 토큰으로 분할하고 문장 부호와 같은 일부 문자를 삭제합니다. 대부분의 경우 토큰화의 목표는 문장을 개별 단어로 분할하는 것입니다.

이 시나리오에서는 전화 번호를 단일 용어로 캡처하려고 하므로 `keyword_v2` 키워드 토크나이저를 사용합니다. 이는 이 문제를 해결하는 유일한 방법이 아닙니다. 아래 [대체 방법](#Alternate) 섹션을 참조하세요.

키워드 토크나이저는 항상 단일 용어로 지정된 것과 동일한 텍스트를 출력합니다.

|입력|출력|  
|-|-|  
|`The dog swims.`|`[The dog swims.]`|  
|`3215550199`|`[3215550199]`|

<a name="TokenFilters"></a>

### <a name="token-filters"></a>토큰 필터

토큰 필터는 토크나이저에서 생성된 토큰을 필터링하거나 수정합니다. 토큰 필터의 일반적인 용도 중 하나는 소문자 토큰 필터를 사용하여 모든 문자를 소문자로 처리하는 것입니다. 또 다른 일반적인 용도는 `the`, `and` 또는 `is`와 같은 중지 단어를 필터링하는 것입니다.

이 시나리오에서는 이러한 필터 중 하나를 사용할 필요가 없지만, nGram 토큰 필터를 사용하여 전화 번호의 부분 검색을 허용합니다.

```json
"tokenFilters": [
  {
    "@odata.type": "#Microsoft.Azure.Search.NGramTokenFilterV2",
    "name": "custom_ngram_filter",
    "minGram": 3,
    "maxGram": 20
  }
]
```

#### <a name="ngramtokenfilterv2"></a>NGramTokenFilterV2

[nGram_v2 토큰 필터](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/ngram/NGramTokenFilter.html)는 `minGram` 및 `maxGram` 매개 변수를 기반으로 하여 토큰을 지정된 크기의 n-그램으로 분할합니다.

전화 분석기의 경우 사용자가 검색할 것으로 예상되는 가장 짧은 하위 문자열이므로 `minGram`을 `3`으로 설정합니다. 내선 번호도 포함하여 모든 전화 번호가 단일 n-그램에 맞도록 `maxGram`이 `20`으로 설정됩니다.

 n-그램의 부작용은 일부 가양성이 반환된다는 것입니다. 이 문제는 7단계에서 n-그램 토큰 필터가 포함되지 않은 검색을 위한 별도의 분석기를 빌드하여 해결합니다.

|입력|출력|  
|-|-|  
|`[12345]`|`[123, 1234, 12345, 234, 2345, 345]`|  
|`[3215550199]`|`[321, 3215, 32155, 321555, 3215550, 32155501, 321555019, 3215550199, 215, 2155, 21555, 215550, ... ]`|

### <a name="analyzer"></a>분석기

문자 필터, 토크나이저 및 토큰 필터를 적절히 사용하면 분석기를 정의할 준비가 됩니다.

```json
"analyzers": [
  {
    "@odata.type": "#Microsoft.Azure.Search.CustomAnalyzer",
    "name": "phone_analyzer",
    "tokenizer": "custom_tokenizer_phone",
    "tokenFilters": [
      "custom_ngram_filter"
    ],
    "charFilters": [
      "phone_char_mapping"
    ]
  }
]
```

|입력|출력|  
|-|-|  
|`12345`|`[123, 1234, 12345, 234, 2345, 345]`|  
|`(321) 555-0199`|`[321, 3215, 32155, 321555, 3215550, 32155501, 321555019, 3215550199, 215, 2155, 21555, 215550, ... ]`|

이제 출력의 토큰을 검색할 수 있습니다. 쿼리에 이러한 토큰이 포함되면 전화 번호가 반환됩니다.

사용자 지정 분석기가 정의되면 다음 단계에서 사용자 지정 분석기를 테스트할 수 있도록 인덱스를 다시 만듭니다. 간단히 하기 위해 Postman 컬렉션에서 정의한 분석기를 사용하여 `tutorial-first-analyzer`라는 새 인덱스를 만듭니다.

## <a name="6---test-the-custom-analyzer"></a>6 - 사용자 지정 분석기 테스트

인덱스가 만들어지면 이제 다음 요청을 사용하여 만든 분석기를 테스트할 수 있습니다.

```http
POST https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/indexes/tutorial-first-analyzer/analyze?api-version=2019-05-06
  Content-Type: application/json
  api-key: <YOUR-ADMIN-API-KEY>  

  {
    "text": "+1 (321) 555-0199",
    "analyzer": "phone_analyzer"
  }
```

그러면 전화 번호에서 생성된 토큰 컬렉션을 볼 수 있습니다.

```json
{
    "tokens": [
        {
            "token": "132",
            "startOffset": 1,
            "endOffset": 17,
            "position": 0
        },
        {
            "token": "1321",
            "startOffset": 1,
            "endOffset": 17,
            "position": 0
        },
        {
            "token": "13215",
            "startOffset": 1,
            "endOffset": 17,
            "position": 0
        },
        ...
        ...
        ...
    ]
}
```

## <a name="7---build-a-custom-analyzer-for-queries"></a>7 - 쿼리를 위한 사용자 지정 분석기 빌드

사용자 지정 분석기를 사용하여 인덱스에 대해 몇 가지 샘플 쿼리를 수행하면 회수가 향상되고 일치하는 전화 번호가 모두 반환됩니다. 그러나 n-그램 토큰 필터로 인해 일부 가양성이 반환되기도 합니다. 이는 n-그램 토큰 필터의 일반적인 부작용입니다.

가양성을 방지하기 위해 쿼리를 위한 별도의 분석기를 만듭니다. 이 분석기는 `custom_ngram_filter`를 **제외**하고는 이미 만든 분석기와 동일합니다.

```json
    {
      "@odata.type": "#Microsoft.Azure.Search.CustomAnalyzer",
      "name": "phone_analyzer_search",
      "tokenizer": "custom_tokenizer_phone",
      "tokenFilters": [],
      "charFilters": [
        "phone_char_mapping"
      ]
    }
```

그런 다음, 인덱스 정의에서 `indexAnalyzer` 및 `searchAnalyzer`를 모두 지정합니다.

```json
    {
      "name": "phone_number",
      "type": "Edm.String",
      "sortable": false,
      "searchable": true,
      "filterable": false,
      "facetable": false,
      "indexAnalyzer": "phone_analyzer",
      "searchAnalyzer": "phone_analyzer_search"
    }
```

이렇게 변경하면 모든 설정이 완료됩니다. 인덱스를 다시 만들고, 데이터를 인덱싱한 다음, 쿼리를 다시 테스트하여 검색이 예상대로 작동하는지 확인합니다. Postman 컬렉션을 사용하는 경우 `tutorial-second-analyzer`라는 세 번째 인덱스를 만듭니다.

<a name="Alternate"></a>

## <a name="alternate-approaches"></a>대체 방법

위의 분석기는 검색을 위한 유연성을 최대화하도록 설계되었습니다. 그러나 잠재적으로 중요하지 않은 여러 용어를 인덱스에 저장하는 데 비용이 들어갑니다.

아래 예제에서는 이 작업에도 사용할 수 있는 다른 분석기를 보여 줍니다. 

분석기는 전화 번호를 논리적 청크 단위로 분할하기 어려운 `14255550100`과 같은 입력 데이터를 제외하고 잘 작동합니다. 예를 들어 분석기는 `425` 지역 코드에서 `1` 국가 번호를 구분할 수 없습니다. 사용자가 국가 번호를 검색에 포함하지 않은 경우 이러한 불일치로 인해 위의 번호가 반환되지 않습니다.

```json
"analyzers": [
  {
    "@odata.type": "#Microsoft.Azure.Search.CustomAnalyzer",
    "name": "phone_analyzer_shingles",
    "tokenizer": "custom_tokenizer_phone",
    "tokenFilters": [
      "custom_shingle_filter"
    ]
  }
],
"tokenizers": [
  {
    "@odata.type": "#Microsoft.Azure.Search.StandardTokenizerV2",
    "name": "custom_tokenizer_phone",
    "maxTokenLength": 4
  }
],
"tokenFilters": [
  {
    "@odata.type": "#Microsoft.Azure.Search.ShingleTokenFilter",
    "name": "custom_shingle_filter",
    "minShingleSize": 2,
    "maxShingleSize": 6,
    "tokenSeparator": ""
  }
]
```

아래 예제에서는 전화 번호가 일반적으로 사용자가 검색할 것으로 예상되는 청크로 분할되어 있음을 알 수 있습니다.

|입력|출력|  
|-|-|  
|`(321) 555-0199`|`[321, 555, 0199, 321555, 5550199, 3215550199]`|

요구 사항에 따라 이는 문제에 대한 더 효율적인 방법일 수 있습니다.

## <a name="reset-and-rerun"></a>다시 설정하고 다시 실행

간단히 하기 위해 이 자습서에서는 세 개의 새 인덱스를 만들었습니다. 그러나 개발 초기 단계에서 인덱스를 삭제하고 다시 만드는 것이 일반적입니다. 인덱스는 Azure Portal 또는 다음 API 호출을 사용하여 삭제할 수 있습니다.

```http
DELETE https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/indexes/tutorial-basic-index?api-version=2019-05-06
  api-key: <YOUR-ADMIN-API-KEY>
```

## <a name="takeaways"></a>핵심 내용

이 자습서에서는 사용자 지정 분석기를 빌드하고 테스트하는 프로세스를 보여 줍니다. 인덱스를 만들고, 데이터를 인덱싱한 다음, 인덱스를 쿼리하여 반환되는 검색 결과를 확인했습니다. 여기서는 텍스트 분석 API를 사용하여 어휘 분석 프로세스가 실제로 작동하는지 확인했습니다.

이 자습서에서 정의한 분석기는 전화 번호를 쉽게 검색하는 솔루션을 제공하지만, 이 동일한 프로세스를 사용하여 모든 시나리오에 대한 사용자 지정 전화 분석기를 빌드할 수 있습니다.

## <a name="clean-up-resources"></a>리소스 정리

사용자 고유의 구독에서 작업하는 경우 프로젝트를 종료할 때 더 이상 필요하지 않은 리소스를 제거하는 것이 좋습니다. 계속 실행되는 리소스에는 요금이 부과될 수 있습니다. 리소스를 개별적으로 삭제하거나 리소스 그룹을 삭제하여 전체 리소스 세트를 삭제할 수 있습니다.

왼쪽 탐색 창의 [모든 리소스] 또는 [리소스 그룹] 링크를 사용하여 포털에서 리소스를 찾고 관리할 수 있습니다.

## <a name="next-steps"></a>다음 단계

이제 사용자 지정 분석기를 만드는 방법을 알아보았으므로 다양한 검색 환경을 구축하는 데 사용할 수 있는 다양한 필터, 토크나이저 및 분석기를 모두 살펴보겠습니다.

> [!div class="nextstepaction"]
> [Azure Cognitive Search의 사용자 지정 분석기](index-add-custom-analyzers.md)