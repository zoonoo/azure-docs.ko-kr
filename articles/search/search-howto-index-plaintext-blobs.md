---
title: 일반 텍스트 blob 검색
titleSuffix: Azure Cognitive Search
description: Azure Cognitive Search에서 전체 텍스트 검색을 위해 Azure blob에서 일반 텍스트를 추출 하도록 검색 인덱서를 구성 합니다.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/01/2021
ms.openlocfilehash: b8881d3fa7ade08da103c5af4b828a12e74cc355
ms.sourcegitcommit: b85ce02785edc13d7fb8eba29ea8027e614c52a2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/03/2021
ms.locfileid: "99509455"
---
# <a name="how-to-index-plain-text-blobs-in-azure-cognitive-search"></a>Azure Cognitive Search에서 일반 텍스트 blob을 인덱싱하는 방법

[Blob 인덱서](search-howto-indexing-azure-blob-storage.md) 를 사용 하 여 전체 텍스트 검색을 위해 검색 가능한 blob 텍스트를 추출 하는 경우 더 나은 인덱싱 결과를 얻기 위해 구문 분석 모드를 할당할 수 있습니다. 기본적으로 인덱서는 단일 텍스트 청크로 blob 콘텐츠를 구문 분석 합니다. 그러나 모든 blob에 동일한 인코딩의 일반 텍스트가 포함 된 경우에는 구문 분석 모드를 사용 하 여 인덱싱 성능을 크게 향상 시킬 수 있습니다 `text` .

구문 분석 사용에 대 한 권장 사항은 `text` 다음과 같습니다.

+ 파일 형식은 .txt입니다.
+ 파일은 모든 형식 이지만 콘텐츠 자체는 텍스트 (예: 프로그램 소스 코드, HTML, XML 등)입니다. 표시 언어의 파일의 경우 모든 구문 문자는 정적 텍스트로 제공 됩니다.

모든 인덱서가 JSON으로 serialize 됨을 기억 합니다. 기본적으로 전체 텍스트 파일의 내용은의 한 개의 한 필드 내에서 인덱싱됩니다 `"content": "<file-contents>"` . 모든 줄 바꿈 및 반환 지침은 내용 필드에 포함 되 고로 표현 됩니다 `\r\n\` .

더 세부적인 결과를 원하는 경우와 파일 형식이 호환 되는 경우 다음 해결 방법을 고려 하십시오.

+ [`delimitedText`](search-howto-index-csv-blobs.md) 구문 분석 모드 (원본이 CSV 인 경우)
+ [ `jsonArray` 또는 `jsonLines` ](search-howto-index-json-blobs.md)소스가 JSON 인 경우

콘텐츠를 여러 부분으로 분할 하는 세 번째 옵션에는 [AI 보강](cognitive-search-concept-intro.md)형식의 고급 기능이 필요 합니다. 파일의 청크를 식별 하 고 다른 검색 필드에 할당 하는 분석을 추가 합니다. [기본 제공 기술을](cognitive-search-predefined-skills.md)통해 전체 또는 부분 솔루션을 찾을 수 있지만, 사용자 지정 [기술](cognitive-search-custom-skill-interface.md)에 래핑된 사용자 지정 학습 모델에서 구분 하 여 콘텐츠를 이해 하는 학습 모델을 사용할 가능성이 높습니다.

## <a name="set-up-plain-text-indexing"></a>일반 텍스트 인덱싱 설정

일반 텍스트 blob을 인덱싱하 `parsingMode` `text` 는 경우 [create 인덱서](/rest/api/searchservice/create-indexer) 요청에서 구성 속성을 사용 하 여 인덱서 정의를 만들거나 업데이트 합니다.

```http
PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
  ... other parts of indexer definition
  "parameters" : { "configuration" : { "parsingMode" : "text" } }
}
```

기본적으로 `UTF-8` 인코딩이 간주됩니다. 다른 인코딩을 지정하려면 `encoding` 구성 속성을 사용하세요. 

```http
{
  ... other parts of indexer definition
  "parameters" : { "configuration" : { "parsingMode" : "text", "encoding" : "windows-1252" } }
}
```

## <a name="request-example"></a>요청 예제

구문 분석 모드는 인덱서 정의에 지정 됩니다.

```http
POST https://[service name].search.windows.net/indexers?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
  "name" : "my-plaintext-indexer",
  "dataSourceName" : "my-blob-datasource",
  "targetIndexName" : "my-target-index",
  "parameters" : { "configuration" : { "parsingMode" : "delimitedText", "delimitedTextHeaders" : "id,datePublished,tags" } }
}
```

## <a name="next-steps"></a>다음 단계

+ [Azure Cognitive Search의 인덱서](search-indexer-overview.md)
+ [Blob 인덱서를 구성 하는 방법](search-howto-indexing-azure-blob-storage.md)
+ [Blob 인덱싱 개요](search-blob-storage-integration.md)