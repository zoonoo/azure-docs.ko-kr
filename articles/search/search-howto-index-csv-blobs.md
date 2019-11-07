---
title: DelimitedText 구문 분석 모드를 사용 하 여 CSV blob 인덱싱 (미리 보기)
titleSuffix: Azure Cognitive Search
description: 현재 공개 미리 보기로 제공 되는 delimitedText 구문 분석 모드를 사용 하 여 Azure Blob storage에서 CSV를 추출 하 고 가져옵니다.
manager: nitinme
author: mgottein
ms.author: magottei
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 4edeb8d535504c305319aad35637bb1b09f65984
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/07/2019
ms.locfileid: "73719241"
---
# <a name="how-to-index-csv-blobs-using-delimitedtext-parsing-mode-and-blob-indexers-in-azure-cognitive-search"></a>Azure Cognitive Search에서 delimitedText 구문 분석 모드 및 Blob 인덱서를 사용 하 여 CSV blob을 인덱싱하는 방법 

> [!IMPORTANT] 
> DelimitedText 구문 분석 모드는 현재 공개 미리 보기로 제공 됩니다. 미리 보기 기능은 서비스 수준 계약 없이 제공 되며 프로덕션 워크 로드에는 권장 되지 않습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요. 이 기능은 [REST API 버전 2019-05-06-미리 보기](search-api-preview.md)에서 제공됩니다. 현재 포털 또는 .NET SDK가 지원 되지 않습니다.

기본적으로 [Azure Cognitive Search blob 인덱서](search-howto-indexing-azure-blob-storage.md) 는 분리 된 텍스트 blob을 텍스트의 단일 청크로 구문 분석 합니다. 그러나 CSV 데이터를 포함하는 Blob을 사용하는 경우 Blob의 각 줄을 별도 파일로 처리하려고 합니다. 예를 들어, 다음 구분 기호로 분리된 텍스트를 각각 "id", "datePublished" 및 "tags" 필드가 포함된 두 개의 문서로 구문 분석할 수 있습니다. 

    id, datePublished, tags
    1, 2016-01-12, "azure-search,azure,cloud" 
    2, 2016-07-07, "cloud,mobile" 

이 문서에서는 `delimitedText` 구문 분석 모드를 설정 하 여 Azure Cognitive Search blob 인덱서를 사용 하 여 CSV blob을 구문 분석 하는 방법을 설명 합니다. 

> [!NOTE]
> 일대다 [인덱싱](search-howto-index-one-to-many-blobs.md) 의 인덱서 구성 권장 사항에 따라 하나의 Azure blob에서 여러 검색 문서를 출력 합니다.

## <a name="setting-up-csv-indexing"></a>CSV 인덱싱 설정
CSV blob을 인덱싱 하려면 [Create 인덱서](https://docs.microsoft.com/rest/api/searchservice/create-indexer) 요청에서 `delimitedText` 구문 분석 모드를 사용 하 여 인덱서 정의를 만들거나 업데이트 합니다.

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
> 구분 된 텍스트 구문 분석 모드를 사용 하는 경우 Azure Cognitive Search는 데이터 원본의 모든 blob이 CSV 라고 가정 합니다. 동일한 데이터 원본에서 CSV 및 비 CSV Blob을 지원해야 하는 경우 [UserVoice](https://feedback.azure.com/forums/263029-azure-search)에서 투표하세요.
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

## <a name="help-us-make-azure-cognitive-search-better"></a>Azure Cognitive Search 향상에 도움을 주세요.
요청할 기능이 있거나 개선을 위한 아이디어가 있는 경우 [UserVoice](https://feedback.azure.com/forums/263029-azure-search/)에서 입력을 제공하세요.

