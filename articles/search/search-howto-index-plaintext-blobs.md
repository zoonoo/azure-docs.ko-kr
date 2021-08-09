---
title: 일반 텍스트 Blob 검색
titleSuffix: Azure Cognitive Search
description: Azure Cognitive Search에서의 전체 텍스트 검색을 위해 Azure Blob에서 일반 텍스트를 추출하도록 검색 인덱서를 구성합니다.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/01/2021
ms.openlocfilehash: b8881d3fa7ade08da103c5af4b828a12e74cc355
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99509455"
---
# <a name="how-to-index-plain-text-blobs-in-azure-cognitive-search"></a>Azure Cognitive Search에서 일반 텍스트 Blob을 인덱싱하는 방법

전체 텍스트 검색을 위해 [Blob 인덱서](search-howto-indexing-azure-blob-storage.md)를 사용하여 검색 가능한 Blob 텍스트를 추출하는 경우 구문 분석 모드를 할당하여 인덱싱 결과를 개선할 수 있습니다. 기본적으로 인덱서는 Blob 콘텐츠를 단일한 텍스트 청크로 보고 구문 분석합니다. 그러나 모든 Blob에 동일한 인코딩의 일반 텍스트가 포함된 경우 `text` 구문 분석 모드를 사용하여 인덱싱 성능을 크게 향상할 수 있습니다.

`text` 구문 분석을 사용할 때의 권장 사항은 다음과 같습니다.

+ 파일 형식은 .txt입니다.
+ 파일은 형식을 가리지 않지만 콘텐츠 자체는 텍스트입니다(예: 프로그램 소스 코드, HTML, XML 등). 마크업 언어로 된 파일의 경우 모든 구문 문자는 정적 텍스트로 제공됩니다.

모든 인덱서가 JSON으로 직렬화된다는 것을 기억하세요. 기본적으로 전체 텍스트 파일의 콘텐츠는 한 개의 큰 필드 내에서 `"content": "<file-contents>"`로 인덱싱됩니다. 모든 새 줄 및 반환 지침은 콘텐츠 필드에 포함되고 `\r\n\`로 표현됩니다.

더 세분화된 결과를 원하고 파일 형식이 호환되는 경우 다음 해결 방법을 사용하는 것이 좋습니다.

+ [`delimitedText`](search-howto-index-csv-blobs.md) 구문 분석 모드(원본이 CSV인 경우)
+ [`jsonArray` 또는 `jsonLines`](search-howto-index-json-blobs.md)(원본이 JSON인 경우)

콘텐츠를 여러 부분으로 분할하는 세 번째 옵션을 사용하려면 [AI 보강](cognitive-search-concept-intro.md) 형식의 고급 기능이 필요합니다. 이 옵션을 사용하면 파일의 청크를 식별하고 서로 다른 검색 필드에 할당하는 분석이 추가됩니다. [기본 제공 기술](cognitive-search-predefined-skills.md)을 통해 완전하거나 부분적인 솔루션을 찾을 수 있지만, 콘텐츠를 파악하고 사용자 지정 학습 모델에서 구체화되며 [사용자 지정 기술](cognitive-search-custom-skill-interface.md)에 래핑된 학습 모델이 보다 효과적인 솔루션일 수 있습니다.

## <a name="set-up-plain-text-indexing"></a>일반 텍스트 인덱싱 설정

일반 텍스트 Blob을 인덱싱하려면 [인덱서 만들기](/rest/api/searchservice/create-indexer) 요청에서 `parsingMode` 구성 속성을 사용하여 인덱서 정의를 만들거나 이를 `text`로 업데이트합니다.

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

구문 분석 모드는 인덱서 정의에 지정됩니다.

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
+ [Blob 인덱서 구성 방법](search-howto-indexing-azure-blob-storage.md)
+ [Blob 인덱싱 개요](search-blob-storage-integration.md)