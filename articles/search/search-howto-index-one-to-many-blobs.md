---
title: 여러 문서를 포함하는 인덱스 Blob
titleSuffix: Azure Cognitive Search
description: Azure Cognitive Search Blob 인덱서를 사용하여 텍스트 콘텐츠에 대한 Azure Blob을 크롤링합니다. 여기서 각 Blob은 하나 이상의 검색 인덱스 문서를 생성할 수 있습니다.
manager: nitinme
author: arv100kri
ms.author: arjagann
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/01/2021
ms.openlocfilehash: ea22b3cff8a0303c4e6698db4090df0f5ed2153a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99430983"
---
# <a name="indexing-blobs-to-produce-multiple-search-documents"></a>Blob을 인덱싱하여 여러 검색 문서 생성

기본적으로 Blob 인덱서에서는 Blob의 내용을 단일 검색 문서로 처리합니다. 검색 인덱스에서 Blob의 보다 세부적인 표현을 원하는 경우 **parsingMode** 값을 설정하여 하나의 Blob에서 여러 검색 문서를 만들 수 있습니다. 많은 검색 문서가 생성되는 **parsingMode** 값으로는 `delimitedText`([CSV](search-howto-index-csv-blobs.md)의 경우)와 `jsonArray` 또는 `jsonLines`([JSON](search-howto-index-json-blobs.md)의 경우)가 있습니다.

해당 구문 분석 모드를 사용하는 경우 나타나는 새 검색 문서에는 고유한 문서 키가 있어야 하며 해당 값이 어디에서 오는지를 파악하는 데 문제가 발생합니다. 부모 Blob에는 `metadata_storage_path property` 형식의 고유 값이 하나 이상 있지만, 해당 값이 두 개 이상의 검색 문서에 기여하는 경우 키가 더 이상 인덱스에서 고유하지 않게 됩니다.

이 문제를 해결하기 위해 Blob 인덱서는 단일 Blob 부모에서 만든 각 자식 검색 문서를 고유하게 식별하는 `AzureSearch_DocumentKey`를 생성합니다. 이 문서에서는 이 기능의 작동 방식에 대해 설명합니다.

## <a name="one-to-many-document-key"></a>일대다 문서 키

Azure Cognitive Search 인덱스로 표시되는 각 문서는 문서 키로 고유하게 식별됩니다. 

구문 분석 모드가 지정되지 않은 상태에서 검색 문서 키에 대한 인덱서 정의에 [명시적 필드 매핑](search-indexer-field-mappings.md)이 없는 경우, Blob 인덱서는 자동으로 `metadata_storage_path property`를 문서 키로 매핑합니다. 이 매핑을 사용하면 각 Blob이 개별적인 검색 문서로 표시되고, 이 필드 매핑을 직접 만들지 않아도 됩니다(일반적으로 이름과 형식이 동일한 필드만 자동으로 매핑됨).

위에 나열된 구문 분석 모드 중 하나를 사용하는 경우 하나의 Blob이 “여러” 검색 문서에 매핑되어 Blob 메타데이터를 기반으로 하는 문서 키만 적합하지 않도록 만듭니다. 이 제약 조건을 해결하기 위해 Azure Cognitive Search는 Blob에서 추출된 개별 엔터티에 대해 “일대다” 문서 키를 생성할 수 있습니다. 이 속성의 이름은 AzureSearch_DocumentKey이며, Blob에서 추출된 개별 엔터티에 추가됩니다. 이 속성의 값은 여러 Blob의 개별 엔터티에 대해 고유하게 유지되며 엔터티는 개별 검색 문서로 표시됩니다.

기본적으로 키 인덱스 필드에 대한 명시적 필드 매핑을 지정하지 않으면 `base64Encode` 필드 매핑 함수를 사용하여 `AzureSearch_DocumentKey`가 매핑됩니다.

## <a name="example"></a>예제

다음 필드가 있는 인덱스 정의가 있다고 가정하겠습니다.

+ `id`
+ `temperature`
+ `pressure`
+ `timestamp`

그리고 Blob 컨테이너에는 다음 구조의 Blob이 있습니다.

_Blob1.json_

```json
{ "temperature": 100, "pressure": 100, "timestamp": "2020-02-13T00:00:00Z" }
{ "temperature" : 33, "pressure" : 30, "timestamp": "2020-02-14T00:00:00Z" }
```

_Blob2.json_

```json
{ "temperature": 1, "pressure": 1, "timestamp": "2019-01-12T00:00:00Z" }
{ "temperature" : 120, "pressure" : 3, "timestamp": "2017-05-11T00:00:00Z" }
```

키 필드에 대해 명시적 필드 매핑을 지정하지 않은 상태로 인덱서를 만들고 **parsingMode** 를 `jsonLines`으로 설정하면 다음 매핑이 암시적으로 적용됩니다.

```http
{
    "sourceFieldName" : "AzureSearch_DocumentKey",
    "targetFieldName": "id",
    "mappingFunction": { "name" : "base64Encode" }
}
```

이렇게 설정하면 다음 그림과 같이 명확한 문서 키가 생성됩니다(간결성을 위해 단축된 base64로 인코딩된 ID).

| ID | 온도 | 압력 | timestamp |
|----|-------------|----------|-----------|
| aHR0 ... YjEuanNvbjsx | 100 | 100 | 2020-02-13T00:00:00Z |
| aHR0 ... YjEuanNvbjsy | 33 | 30 | 2020-02-14T00:00:00Z |
| aHR0 ... YjIuanNvbjsx | 1 | 1 | 2019-01-12T00:00:00Z |
| aHR0 ... YjIuanNvbjsy | 120 | 3 | 2017-05-11T00:00:00Z |

## <a name="custom-field-mapping-for-index-key-field"></a>인덱스 키 필드에 대한 사용자 지정 필드 매핑

인덱스 정의가 이전 예제와 동일하다는 가정하에, Blob 컨테이너에 다음 구조의 Blob이 있다고 가정합니다.

_Blob1.json_

```json
recordid, temperature, pressure, timestamp
1, 100, 100,"2019-02-13T00:00:00Z" 
2, 33, 30,"2019-02-14T00:00:00Z" 
```

_Blob2.json_

```json
recordid, temperature, pressure, timestamp
1, 1, 1,"2018-01-12T00:00:00Z" 
2, 120, 3,"2013-05-11T00:00:00Z" 
```

`delimitedText` **parsingMode** 를 사용하여 인덱서를 만들 때 다음과 같이 키 필드에 필드 매핑 함수를 설정하는 것이 당연하게 느껴질 수 있습니다.

```http
{
    "sourceFieldName" : "recordid",
    "targetFieldName": "id"
}
```

그러나 `recordid` 필드는 ‘여러 Blob에 걸쳐’ 고유하지 않으므로 해당 매핑을 통해 4개의 문서가 인덱스에 표시되지 ‘않습니다’.  따라서 `AzureSearch_DocumentKey` 속성에서 적용된 암시적 필드 매핑을 “일대다” 구문 분석 모드의 키 인덱스 필드에 사용하는 것이 좋습니다.

명시적 필드 매핑을 설정하려면 **모든 Blob에서** 개별 엔터티에 대해 _sourceField_ 가 고유한지 확인합니다.

> [!NOTE]
> `AzureSearch_DocumentKey`가 추출된 엔터티당 고유성을 보장하는 데 사용하는 접근 방식은 변경될 수 있으므로 애플리케이션의 요구 사항에 따른 해당 값에 의존해서는 안 됩니다.

## <a name="next-steps"></a>다음 단계

Blob 인덱싱의 기본 구조 및 워크플로에 익숙하지 않은 경우, 먼저 [Azure Cognitive Search를 통한 Azure Blob Storage 인덱싱](search-howto-index-json-blobs.md)을 검토해야 합니다. 여러 Blob 콘텐츠 형식의 구문 분석 모드에 대한 자세한 내용은 다음 문서를 참조하세요.

> [!div class="nextstepaction"]
> [CSV Blob 인덱싱](search-howto-index-csv-blobs.md)
> [JSON Blob 인덱싱](search-howto-index-json-blobs.md)
