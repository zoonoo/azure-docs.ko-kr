---
title: Azure Search Blob 인덱서를 사용하여 CSV Blob 인덱싱 - Azure Search
description: Azure Search 인덱스를 사용하여 전체 텍스트 검색을 위해 Azure Blob Storage의 CSV Blob을 크롤링합니다. 인덱서는 Azure Blob Storage와 같은 선택된 데이터 원본에 대해 데이터 수집을 자동화합니다.
ms.date: 05/02/2019
author: mgottein
manager: nitinme
ms.author: magottei
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.custom: seodec2018
ms.openlocfilehash: b135fd1a0758567a7b504996bf442a913741fe59
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2019
ms.locfileid: "69656765"
---
# <a name="indexing-csv-blobs-with-azure-search-blob-indexer"></a>Azure Search Blob 인덱서를 사용하여 CSV Blob 인덱싱

> [!Note]
> delimitedText 구문 분석 모드는 미리 보기로 제공 되며 프로덕션 용도로는 사용할 수 없습니다. 이 기능은 [REST API 버전 2019-05-06-미리 보기](search-api-preview.md)에서 제공됩니다. 지금은 .NET SDK 지원이 없습니다.
>

기본적으로 [Azure Search Blob 인덱서](search-howto-indexing-azure-blob-storage.md) 는 단일 텍스트 청크로 구분된 텍스트 Blob을 구문 분석합니다. 그러나 CSV 데이터를 포함하는 Blob을 사용하는 경우 Blob의 각 줄을 별도 파일로 처리하려고 합니다. 예를 들어, 다음 구분 기호로 분리된 텍스트를 각각 "id", "datePublished" 및 "tags" 필드가 포함된 두 개의 문서로 구문 분석할 수 있습니다. 

    id, datePublished, tags
    1, 2016-01-12, "azure-search,azure,cloud" 
    2, 2016-07-07, "cloud,mobile" 

이 문서에서는 구문 분석 모드를 `delimitedText` 설정 하 여 Azure Search blob indexerby를 사용 하 여 CSV blob을 구문 분석 하는 방법을 알아봅니다. 

> [!NOTE]
> 일대다 [인덱싱](search-howto-index-one-to-many-blobs.md) 의 인덱서 구성 권장 사항에 따라 하나의 Azure blob에서 여러 검색 문서를 출력 합니다.

## <a name="setting-up-csv-indexing"></a>CSV 인덱싱 설정
CSV blob을 인덱싱 하려면 `delimitedText` [create 인덱서](https://docs.microsoft.com/rest/api/searchservice/create-indexer) 요청에서 구문 분석 모드를 사용 하 여 인덱서 정의를 만들거나 업데이트 합니다.

    {
      "name" : "my-csv-indexer",
      ... other indexer properties
      "parameters" : { "configuration" : { "parsingMode" : "delimitedText", "firstLineContainsHeaders" : true } }
    }

`firstLineContainsHeaders` 은(는) 각 Blob의 첫 번째(비어 있지 않은) 줄이 헤더를 포함하는 것을 나타냅니다.
Blob이 초기 헤더 줄을 포함하지 않는 경우 헤더는 인덱서 구성에서 지정되어야 합니다. 

    "parameters" : { "configuration" : { "parsingMode" : "delimitedText", "delimitedTextHeaders" : "id,datePublished,tags" } } 

`delimitedTextDelimiter` 구성 설정을 사용하여 구분 기호 문자를 사용자 지정할 수 있습니다. 예:

    "parameters" : { "configuration" : { "parsingMode" : "delimitedText", "delimitedTextDelimiter" : "|" } }

> [!NOTE]
> 현재는 UTF-8 인코딩만 지원됩니다. 다른 인코딩을 지원해야 하는 경우 [UserVoice](https://feedback.azure.com/forums/263029-azure-search)에서 투표하세요.

> [!IMPORTANT]
> 구분된 텍스트 구문 분석 모드를 사용하는 경우 Azure Search는 데이터 원본의 모든 Blob을 CSV로 가정합니다. 동일한 데이터 원본에서 CSV 및 비 CSV Blob을 지원해야 하는 경우 [UserVoice](https://feedback.azure.com/forums/263029-azure-search)에서 투표하세요.
> 
> 

## <a name="request-examples"></a>요청 예제
다음은 이를 모두 포함한 전체 페이로드 예제입니다. 

데이터 원본: 

    POST https://[service name].search.windows.net/datasources?api-version=2019-05-06-Preview
    Content-Type: application/json
    api-key: [admin key]

    {
        "name" : "my-blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>;" },
        "container" : { "name" : "my-container", "query" : "<optional, my-folder>" }
    }   

인덱서:

    POST https://[service name].search.windows.net/indexers?api-version=2019-05-06-Preview
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "my-csv-indexer",
      "dataSourceName" : "my-blob-datasource",
      "targetIndexName" : "my-target-index",
      "parameters" : { "configuration" : { "parsingMode" : "delimitedText", "delimitedTextHeaders" : "id,datePublished,tags" } }
    }

## <a name="help-us-make-azure-search-better"></a>Azure Search 개선 지원
요청할 기능이 있거나 개선을 위한 아이디어가 있는 경우 [UserVoice](https://feedback.azure.com/forums/263029-azure-search/)에서 입력을 제공하세요.

