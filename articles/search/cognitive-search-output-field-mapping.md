---
title: 출력 필드에 입력 매핑
titleSuffix: Azure Cognitive Search
description: 원본 데이터 필드를 추출 및 보강하고 Azure 인지 검색 인덱스의 출력 필드에 매핑합니다.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: f0537af684632a08a39e3e681900d62238365073
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74280975"
---
# <a name="how-to-map-ai-enriched-fields-to-a-searchable-index"></a>AI가 풍부한 필드를 검색 가능한 인덱스에 매핑하는 방법

이 문서에서는 검색 가능한 인덱스에서 입력 필드에 보강된 출력 필드를 매핑하는 방법에 대해 알아봅니다. [기술 집합을 정의](cognitive-search-defining-skillset.md)했으면 검색 인덱스에서 지정된 필드에 값을 직접 기여하는 모든 기술의 출력 필드를 매핑해야 합니다. 

출력 필드 매핑은 보강된 문서에서 인덱스로 콘텐츠를 이동하려면 필요합니다.  보강된 문서는 실제로 정보 트리이며 인덱스에 복잡한 형식에 대한 지원이 있더라도 보강된 트리에서 보다 간단한 형식(예: 문자열 배열)으로 정보를 변환할 수 있습니다. 출력 필드 매핑을 사용하면 정보를 병합하여 데이터 셰이프 변환을 수행할 수 있습니다.

## <a name="use-outputfieldmappings"></a>OutputFieldMappings 사용
필드를 매핑하려면 아래와 같이 `outputFieldMappings`을 인덱서 정의에 추가합니다.

```http
PUT https://[servicename].search.windows.net/indexers/[indexer name]?api-version=2019-05-06
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
            "targetFieldName": "descriptions"
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

각 출력 필드 매핑에 대해 보강된 문서 트리(sourceFieldName)의 데이터 위치와 인덱스(targetFieldName)에서 참조되는 필드 이름을 설정합니다.

## <a name="flattening-information-from-complex-types"></a>복합 유형에서 정보를 병합 

sourceFieldName에서 경로는 하나 또는 여러 요소를 나타낼 수 있습니다. 위의 예에서 ```/document/content/sentiment```은 단일 숫자 값을 나타내는 반면 ```/document/content/organizations/*/description```는 여러 조직 설명을 나타냅니다. 

여러 요소가 있는 경우 이런 요소는 요소 각각을 포함한 배열로 “평면화”됩니다. 

더 구체적으로 ```/document/content/organizations/*/description``` 예제의 경우 *설명* 필드의 데이터는 인덱스되기 전에 설명의 플랫 배열과 같습니다.

```
 ["Microsoft is a company in Seattle","LinkedIn's office is in San Francisco"]
```

이것은 중요한 원칙이므로 또 다른 예를 들어 보겠습니다. 보강 트리의 일부로 복잡한 형식의 배열이 있다고 가정해 보아보십입니다. 아래에 설명된 것과 같은 복잡한 형식의 배열이 있는 customEntities라는 멤버가 있다고 가정해 보겠습니다.

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

인덱스에 엔터티의 각 이름을 저장하려는 Edm.String 형식의 '질병'이라는 필드가 있다고 가정해 보겠습니다. 

이것은 다음과 같이 " 기호를\*사용하여 쉽게 수행 할 수 있습니다.

```json
    "outputFieldMappings": [
        {
            "sourceFieldName": "/document/customEntities/*/name",
            "targetFieldName": "diseases"
        }
    ]
```

이 작업은 단순히 다음과 같은 문자열의 단일 배열로 사용자 정의의 각 이름을 "병합"합니다.

```json
  "diseases" : ["heart failure","morquio"]
```

## <a name="next-steps"></a>다음 단계
검색 가능한 필드에 보강된 필들을 매핑했으면 [인덱스 정의의 일부로서](search-what-is-an-index.md) 검색 가능한 필드 각각에 대해 필드 특성을 설정할 수 있습니다.

필드 매핑에 대한 자세한 내용은 [Azure 인지 검색 인덱서의 필드 매핑을](search-indexer-field-mappings.md)참조하십시오.
