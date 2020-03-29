---
title: CSV Blob을 검색
titleSuffix: Azure Cognitive Search
description: 분산된 텍스트 구문 분석 모드를 사용하여 Azure Blob 저장소에서 CSV를 추출하고 가져옵니다.
manager: nitinme
author: mgottein
ms.author: magottei
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: bf600890bfed570e712a159005b8ef5267298cc0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76122324"
---
# <a name="how-to-index-csv-blobs-using-delimitedtext-parsing-mode-and-blob-indexers-in-azure-cognitive-search"></a>Azure 인지 검색에서 delimitedText 구문 분석 모드 및 Blob 인덱서를 사용하여 CSV Blob을 인덱싱하는 방법

기본적으로 [Azure Cognitive Search Blob 인덱서에서는](search-howto-indexing-azure-blob-storage.md) 제한된 텍스트 Blob을 단일 텍스트 청크로 구문 분석합니다. 그러나 CSV 데이터를 포함하는 Blob을 사용하는 경우 Blob의 각 줄을 별도 파일로 처리하려고 합니다. 예를 들어, 다음 구분 기호로 분리된 텍스트를 각각 "id", "datePublished" 및 "tags" 필드가 포함된 두 개의 문서로 구문 분석할 수 있습니다. 

    id, datePublished, tags
    1, 2016-01-12, "azure-search,azure,cloud" 
    2, 2016-07-07, "cloud,mobile" 

이 문서에서는 `delimitedText` 구문 분석 모드를 설정하여 Azure 인지 검색 Blob 인덱서를 사용하여 CSV Blob을 구문 분석하는 방법을 배웁니다. 

> [!NOTE]
> [일대다 인덱싱의](search-howto-index-one-to-many-blobs.md) 인덱서 구성 권장 사항을 따라 하나의 Azure Blob에서 여러 검색 문서를 출력합니다.

## <a name="setting-up-csv-indexing"></a>CSV 인덱싱 설정
CSV Blob을 인덱싱하려면 인덱서 만들기 `delimitedText` 요청에서 구문 분석 모드로 인덱서 정의를 만들거나 [업데이트합니다.](https://docs.microsoft.com/rest/api/searchservice/create-indexer)

    {
      "name" : "my-csv-indexer",
      ... other indexer properties
      "parameters" : { "configuration" : { "parsingMode" : "delimitedText", "firstLineContainsHeaders" : true } }
    }

`firstLineContainsHeaders` 은(는) 각 Blob의 첫 번째(비어 있지 않은) 줄이 헤더를 포함하는 것을 나타냅니다.
Blob이 초기 헤더 줄을 포함하지 않는 경우 헤더는 인덱서 구성에서 지정되어야 합니다. 

    "parameters" : { "configuration" : { "parsingMode" : "delimitedText", "delimitedTextHeaders" : "id,datePublished,tags" } } 

`delimitedTextDelimiter` 구성 설정을 사용하여 구분 기호 문자를 사용자 지정할 수 있습니다. 예를 들어:

    "parameters" : { "configuration" : { "parsingMode" : "delimitedText", "delimitedTextDelimiter" : "|" } }

> [!NOTE]
> 현재는 UTF-8 인코딩만 지원됩니다. 다른 인코딩을 지원해야 하는 경우 [UserVoice](https://feedback.azure.com/forums/263029-azure-search)에서 투표하세요.

> [!IMPORTANT]
> 구분된 텍스트 구문 분석 모드를 사용하는 경우 Azure Cognitive Search는 데이터 원본의 모든 Blob이 CSV라고 가정합니다. 동일한 데이터 원본에서 CSV 및 비 CSV Blob을 지원해야 하는 경우 [UserVoice](https://feedback.azure.com/forums/263029-azure-search)에서 투표하세요.
> 
> 

## <a name="request-examples"></a>요청 예제
다음은 이를 모두 포함한 전체 페이로드 예제입니다. 

데이터 원본: 

    POST https://[service name].search.windows.net/datasources?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key]

    {
        "name" : "my-blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>;" },
        "container" : { "name" : "my-container", "query" : "<optional, my-folder>" }
    }   

인덱서:

    POST https://[service name].search.windows.net/indexers?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "my-csv-indexer",
      "dataSourceName" : "my-blob-datasource",
      "targetIndexName" : "my-target-index",
      "parameters" : { "configuration" : { "parsingMode" : "delimitedText", "delimitedTextHeaders" : "id,datePublished,tags" } }
    }

## <a name="help-us-make-azure-cognitive-search-better"></a>Azure 인지 검색을 더 잘 만드는 데 도움이 됩니다.
요청할 기능이 있거나 개선을 위한 아이디어가 있는 경우 [UserVoice](https://feedback.azure.com/forums/263029-azure-search/)에서 입력을 제공하세요.

