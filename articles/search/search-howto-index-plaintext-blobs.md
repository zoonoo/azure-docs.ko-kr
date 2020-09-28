---
title: 일반 텍스트 blob 검색
titleSuffix: Azure Cognitive Search
description: Azure Cognitive Search에서 전체 텍스트 검색을 위해 Azure blob에서 일반 텍스트를 추출 하도록 검색 인덱서를 구성 합니다.
manager: nitinme
author: mgottein
ms.author: magottei
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 09/25/2020
ms.openlocfilehash: 2b01b9a3782d25660462d0cc2bf3aec87baf0023
ms.sourcegitcommit: dc68a2c11bae2e9d57310d39fbed76628233fd7f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/28/2020
ms.locfileid: "91403782"
---
# <a name="how-to-index-plain-text-blobs-in-azure-cognitive-search"></a>Azure Cognitive Search에서 일반 텍스트 blob을 인덱싱하는 방법

[Blob 인덱서](search-howto-indexing-azure-blob-storage.md) 를 사용 하 여 전체 텍스트 검색을 위해 검색 가능한 텍스트를 추출 하는 경우 다양 한 구문 분석 모드를 호출 하 여 더 나은 인덱싱 결과를 얻을 수 있습니다. 기본적으로 인덱서는 구분 된 텍스트 blob을 텍스트의 단일 청크로 구문 분석 합니다. 그러나 모든 blob에 동일한 인코딩의 일반 텍스트가 포함 된 경우 **텍스트 구문 분석 모드**를 사용 하 여 인덱싱 성능을 크게 향상 시킬 수 있습니다.

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

## <a name="help-us-make-azure-cognitive-search-better"></a>Azure Cognitive Search 향상에 도움을 주세요.
요청할 기능이 있거나 개선을 위한 아이디어가 있는 경우 [UserVoice](https://feedback.azure.com/forums/263029-azure-search/)에서 입력을 제공하세요. 기존 기능을 사용 하는 데 도움이 필요 하면 [Stack Overflow](https://stackoverflow.microsoft.com/questions/tagged/18870)에 질문을 게시 하세요.

## <a name="see-also"></a>참고 항목

* [Azure Cognitive Search의 인덱서](search-indexer-overview.md)
* [Blob 인덱서를 구성 하는 방법](search-howto-indexing-azure-blob-storage.md)
* [Blob 인덱싱 개요](search-blob-storage-integration.md)