---
title: AI-보강 입력 필드를 출력 필드에 매핑
titleSuffix: Azure Cognitive Search
description: 원본 데이터 필드를 추출 및 보강 하 고 Azure Cognitive Search 인덱스의 출력 필드에 매핑합니다.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 77f378f4e0c4a8e4827523e244f7b18c2a9ba336
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/23/2019
ms.locfileid: "72792114"
---
# <a name="how-to-map-ai-enriched-fields-to-a-searchable-index"></a>AI-보강 필드를 검색 가능한 인덱스에 매핑하는 방법

이 문서에서는 검색 가능한 인덱스에서 입력 필드에 보강된 출력 필드를 매핑하는 방법에 대해 알아봅니다. [기술 집합을 정의](cognitive-search-defining-skillset.md)했으면 검색 인덱스에서 지정된 필드에 값을 직접 기여하는 모든 기술의 출력 필드를 매핑해야 합니다. 필드 매핑은 보강된 문서에서 콘텐츠를 인덱스로 이동하는 데 필요합니다.


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
각 출력 필드 매핑의 경우 인덱스(targetFieldName)에서 참조된 대로 필드 이름 및 보강된 필드 (sourceFieldName)의 이름을 설정합니다.

sourceFieldName에서 경로는 하나 또는 여러 요소를 나타낼 수 있습니다. 위의 예에서 ```/document/content/sentiment```은 단일 숫자 값을 나타내는 반면 ```/document/content/organizations/*/description```는 여러 조직 설명을 나타냅니다. 여러 요소가 있는 경우 이런 요소는 요소 각각을 포함한 배열로 “평면화”됩니다. 더 구체적으로 ```/document/content/organizations/*/description``` 예제의 경우 *설명* 필드의 데이터는 인덱스되기 전에 설명의 플랫 배열과 같습니다.

```
 ["Microsoft is a company in Seattle","LinkedIn's office is in San Francisco"]
```
## <a name="next-steps"></a>다음 단계
검색 가능한 필드에 보강된 필들을 매핑했으면 [인덱스 정의의 일부로서](search-what-is-an-index.md) 검색 가능한 필드 각각에 대해 필드 특성을 설정할 수 있습니다.

필드 매핑에 대 한 자세한 내용은 [Azure Cognitive Search 인덱서의 필드 매핑](search-indexer-field-mappings.md)을 참조 하세요.
