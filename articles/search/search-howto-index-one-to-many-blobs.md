---
title: 여러 문서를 포함하는 인덱스 Blob
titleSuffix: Azure Cognitive Search
description: Azure Congitive 검색 Blob 인덱서를 사용하여 텍스트 콘텐츠에 대한 Azure Blob을 크롤링하여 각 Blob에서 하나 이상의 검색 인덱스 문서를 생성할 수 있습니다.
manager: nitinme
author: arv100kri
ms.author: arjagann
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 1840bda0ecc9462a5d8f796b616d728d0bb412f7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74112272"
---
# <a name="indexing-blobs-to-produce-multiple-search-documents"></a>여러 검색 문서를 생성하는 Blob 인덱싱
기본적으로 Blob 인덱서에서는 Blob의 내용을 단일 검색 문서로 처리합니다. 특정 **구문 분석Mode 값은** 개별 Blob이 여러 검색 문서를 생성할 수 있는 시나리오를 지원합니다. 인덱서가 Blob에서 둘 이상의 검색 문서를 추출할 수 있도록 허용하는 다양한 유형의 **구문 분석모드는** 다음과 같습니다.
+ `delimitedText`
+ `jsonArray`
+ `jsonLines`

## <a name="one-to-many-document-key"></a>일대다 문서 키
Azure 인지 검색 인덱스에 표시되는 각 문서는 문서 키로 고유하게 식별됩니다. 

구문 분석 모드가 지정되지 않고 인덱스의 키 필드에 대한 명시적 매핑이 없는 `metadata_storage_path` 경우 Azure Cognitive Search는 속성을 키로 자동으로 [매핑합니다.](search-indexer-field-mappings.md) 이 매핑을 통해 각 Blob이 고유한 검색 문서로 표시됩니다.

위에 나열된 구문 분석 모드를 사용하는 경우 하나의 Blob이 "많은" 검색 문서에 매핑되므로 Blob 메타데이터만을 기반으로 하는 문서 키가 적합하지 않습니다. 이러한 제약 조건을 극복하기 위해 Azure Cognitive Search는 Blob에서 추출된 각 개별 엔터티에 대해 "일대다" 문서 키를 생성할 수 있습니다. 이 속성의 `AzureSearch_DocumentKey` 이름이 지정되고 Blob에서 추출된 각 개별 엔터티에 추가됩니다. 이 속성의 값은 _Blob에서_ 각 개별 엔터티에 대해 고유하게 보장되며 엔터티는 별도의 검색 문서로 표시됩니다.

기본적으로 키 인덱스 필드에 대한 명시적 필드 매핑이 `AzureSearch_DocumentKey` 지정되지 않은 경우 `base64Encode` 필드 매핑 기능을 사용하여 매핑됩니다.

## <a name="example"></a>예제
다음 필드가 있는 인덱스 정의를 가정합니다.
+ `id`
+ `temperature`
+ `pressure`
+ `timestamp`

Blob 컨테이너에는 다음과 같은 구조의 Blob이 있습니다.

_Blob1.json_

    { "temperature": 100, "pressure": 100, "timestamp": "2019-02-13T00:00:00Z" }
    { "temperature" : 33, "pressure" : 30, "timestamp": "2019-02-14T00:00:00Z" }

_Blob2.json_

    { "temperature": 1, "pressure": 1, "timestamp": "2018-01-12T00:00:00Z" }
    { "temperature" : 120, "pressure" : 3, "timestamp": "2013-05-11T00:00:00Z" }

인덱서를 만들고 **parsingMode를** `jsonLines` 키 필드에 대한 명시적 필드 매핑을 지정하지 않고 다음 매핑이 암시적으로 적용됩니다.
    
    {
        "sourceFieldName" : "AzureSearch_DocumentKey",
        "targetFieldName": "id",
        "mappingFunction": { "name" : "base64Encode" }
    }

이 설정으로 인해 다음 정보가 포함된 Azure 인지 검색 인덱스가 생성됩니다(간결성을 위해 인코딩된 id가 단축되었습니다).

| id | 온도 | pressure | timestamp |
|----|-------------|----------|-----------|
| AHR0 ... YjEuanNvbjsx | 100 | 100 | 2019-02-13T00:00:00Z |
| AHR0 ... YjEuanNvbjsy | 33 | 30 | 2019-02-14T00:00:00Z |
| AHR0 ... 이지우안Nvbjsx | 1 | 1 | 2018-01-12T00:00:00Z |
| AHR0 ... 이지우안Nvbjsy | 120 | 3 | 2013-05-11T00:00:00Z |

## <a name="custom-field-mapping-for-index-key-field"></a>인덱스 키 필드에 대한 사용자 지정 필드 매핑

이전 예제와 동일한 인덱스 정의를 가정하면 Blob 컨테이너에 다음 구조의 Blob이 있다고 가정합니다.

_Blob1.json_

    recordid, temperature, pressure, timestamp
    1, 100, 100,"2019-02-13T00:00:00Z" 
    2, 33, 30,"2019-02-14T00:00:00Z" 

_Blob2.json_

    recordid, temperature, pressure, timestamp
    1, 1, 1,"2018-01-12T00:00:00Z" 
    2, 120, 3,"2013-05-11T00:00:00Z" 

`delimitedText` **구문 분석 모드로**인덱서를 만들 때 다음과 같이 키 필드에 필드 매핑 함수를 설정하는 것이 자연스럽게 느껴질 수 있습니다.

    {
        "sourceFieldName" : "recordid",
        "targetFieldName": "id"
    }

그러나 이 매핑은 `recordid` 필드가 _Blob에서_고유하지 않기 때문에 인덱스에 4개의 문서가 표시되지 는 _않습니다._ 따라서 속성에서 "일대다" 구문 분석 `AzureSearch_DocumentKey` 모드의 키 인덱스 필드에 적용된 암시적 필드 매핑을 사용하는 것이 좋습니다.

명시적 필드 매핑을 설정하려면 모든 **Blob에서**각 개별 엔터티에 대해 _sourceField가_ 고유해야 합니다.

> [!NOTE]
> 추출된 엔터티당 고유성을 보장하는 데 사용되는 `AzureSearch_DocumentKey` 접근 방식은 변경될 수 있으므로 응용 프로그램의 요구에 맞게 이 값에 의존해서는 안 됩니다.

## <a name="next-steps"></a>다음 단계

Blob 인덱싱의 기본 구조 및 워크플로에 익숙하지 않은 경우 먼저 [Azure 인지 검색을 사용하여 Azure Blob 저장소인덱싱을](search-howto-index-json-blobs.md) 검토해야 합니다. 다른 Blob 콘텐츠 유형에 대한 구문 분석 모드에 대한 자세한 내용은 다음 문서를 검토하십시오.

> [!div class="nextstepaction"]
> [인덱싱 CSV blobs](search-howto-index-csv-blobs.md)
> [인덱싱 JSON Blobs](search-howto-index-json-blobs.md)
