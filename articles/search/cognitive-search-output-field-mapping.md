---
title: 출력 필드에 입력 매핑
titleSuffix: Azure Cognitive Search
description: 원본 데이터 필드를 추출 및 보강하고 Azure Cognitive Search 인덱스의 출력 필드에 매핑합니다.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 58bb87d5af785d3cffd96f3bd02477f97ed967a9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96001306"
---
# <a name="how-to-map-ai-enriched-fields-to-a-searchable-index"></a>검색 가능한 인덱스에 AI 보강된 필드를 매핑하는 방법

![인덱서 스테이지](./media/cognitive-search-output-field-mapping/indexer-stages-output-field-mapping.png "인덱서 스테이지")

이 문서에서는 검색 가능한 인덱스에서 입력 필드에 보강된 출력 필드를 매핑하는 방법에 대해 알아봅니다. [기술 집합을 정의](cognitive-search-defining-skillset.md)했으면 검색 인덱스에서 지정된 필드에 값을 직접 기여하는 모든 기술의 출력 필드를 매핑해야 합니다.

출력 필드 매핑은 콘텐츠를 보강된 문서에서 인덱스로 이동하는 데 필요합니다.  보강 문서는 사실 정보의 트리입니다. 인덱스에 복합 형식을 지원하는 경우에도 보강 트리의 정보를 문자열 배열과 같이 보다 단순한 형식으로 변환하는 것이 좋습니다. 출력 필드 매핑을 사용하면 정보를 평면화하여 데이터 셰이프를 변환할 수 있습니다. 출력 필드 매핑은 기술 실행 후 항상 발생하지만 기술 세트가 정의되지 않은 경우에도 이 스테이지를 실행할 수 있습니다.

출력 필드 매핑의 예:

* 기술 세트의 일환으로 문서의 각 페이지에 언급된 조직의 이름을 추출했습니다. 이제 이러한 각 조직 이름을 Edm.Collection 형식의 인덱스에 있는 필드(Edm. String)에 매핑해야 합니다.

* 기술 세트의 일환으로 “document/translated_text”라는 새 노드를 생성했습니다. 이 노드의 정보를 인덱스의 특정 필드에 매핑하려고 합니다.

* 기술 세트는 없지만 Cosmos DB 데이터베이스에서 복합 형식을 인덱싱하고 있습니다. 해당 복합 형식의 노드로 이동하여 인덱스의 필드에 매핑하려고 합니다.

> [!NOTE]
> 최근 출력 필드 매핑에서 매핑 함수 기능을 사용하도록 설정했습니다. 매핑 함수에 대한 자세한 내용은 [필드 매핑 함수](./search-indexer-field-mappings.md#field-mapping-functions)를 참조하세요.

## <a name="use-outputfieldmappings"></a>OutputFieldMappings 사용

필드를 매핑하려면 아래와 같이 `outputFieldMappings`을 인덱서 정의에 추가합니다.

```http
PUT https://[servicename].search.windows.net/indexers/[indexer name]?api-version=2020-06-30
api-key: [admin key]
Content-Type: application/json
```

요청 본문은 다음과 같이 구성되어 있습니다.

```json
{
    "name": "myIndexer",
    "dataSourceName": "myDataSource",
    "targetIndexName": "myIndex",
    "skillsetName": "myFirstSkillSet",
    "fieldMappings": [
        {
            "sourceFieldName": "metadata_storage_path",
            "targetFieldName": "id",
            "mappingFunction": {
                "name": "base64Encode"
            }
        }
    ],
    "outputFieldMappings": [
        {
            "sourceFieldName": "/document/content/organizations/*/description",
            "targetFieldName": "descriptions",
            "mappingFunction": {
                "name": "base64Decode"
            }
        },
        {
            "sourceFieldName": "/document/content/organizations",
            "targetFieldName": "orgNames"
        },
        {
            "sourceFieldName": "/document/content/sentiment",
            "targetFieldName": "sentiment"
        }
    ]
}
```

각 출력 필드 매핑의 경우 보강 필드(sourceFieldName)의 데이터 위치 및 인덱스(targetFieldName)에서 참조한 필드 이름을 설정합니다.

## <a name="flattening-information-from-complex-types"></a>복합 형식의 정보 평면화 

sourceFieldName에서 경로는 하나 또는 여러 요소를 나타낼 수 있습니다. 위의 예에서 ```/document/content/sentiment```은 단일 숫자 값을 나타내는 반면 ```/document/content/organizations/*/description```는 여러 조직 설명을 나타냅니다. 

여러 요소가 있는 경우 이런 요소는 요소 각각을 포함한 배열로 “평면화”됩니다. 

더 구체적으로 ```/document/content/organizations/*/description``` 예제의 경우 *설명* 필드의 데이터는 인덱스되기 전에 설명의 플랫 배열과 같습니다.

```
 ["Microsoft is a company in Seattle","LinkedIn's office is in San Francisco"]
```

이는 중요한 원칙이므로 또 다른 예제를 제공하겠습니다. 복합 형식의 배열이 보강 트리의 일부인 것으로 가정합니다. 아래에서 설명한 것과 같은 복합 형식의 배열이 있는 customEntities라는 멤버가 있다고 가정해 보겠습니다.

```json
"document/customEntities": 
[
    {
        "name": "heart failure",
        "matches": [
            {
                "text": "heart failure",
                "offset": 10,
                "length": 12,
                "matchDistance": 0.0
            }
        ]
    },
    {
        "name": "morquio",
        "matches": [
            {
                "text": "morquio",
                "offset": 25,
                "length": 7,
                "matchDistance": 0.0
            }
        ]
    }
    //...
]
```

인덱스에 컬렉션(Edm.String) 형식의 'diseases'라는 필드가 있다고 가정해 보겠습니다. 이는 각 엔터티의 이름을 저장할 위치입니다. 

다음과 같이 "\*" 기호를 사용하면 쉽게 이 작업을 수행할 수 있습니다.

```json
    "outputFieldMappings": [
        {
            "sourceFieldName": "/document/customEntities/*/name",
            "targetFieldName": "diseases"
        }
    ]
```

이 작업을 수행하면 다음과 같이 customEntities 요소의 각 이름이 문자열의 단일 배열로 "평면화"됩니다.

```json
  "diseases" : ["heart failure","morquio"]
```

## <a name="next-steps"></a>다음 단계
검색 가능한 필드에 보강된 필들을 매핑했으면 [인덱스 정의의 일부로서](search-what-is-an-index.md) 검색 가능한 필드 각각에 대해 필드 특성을 설정할 수 있습니다.

필드 매핑에 대한 자세한 내용은 [Azure Cognitive Search 인덱서의 필드 매핑](search-indexer-field-mappings.md)을 참조하세요.