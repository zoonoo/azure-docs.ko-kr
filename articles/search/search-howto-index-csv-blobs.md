---
title: Azure Search Blob 인덱서를 사용하여 CSV Blob 인덱싱 | Microsoft Docs
description: Azure Search를 사용하여 CSV Blob을 인덱싱하는 방법 알아보기
author: chaosrealm
manager: jlembicz
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.date: 12/28/2017
ms.author: eugenesh
ms.openlocfilehash: f4c1238b5ff03d9fc52238660134fde774b3474e
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/18/2018
---
# <a name="indexing-csv-blobs-with-azure-search-blob-indexer"></a>Azure Search Blob 인덱서를 사용하여 CSV Blob 인덱싱
기본적으로 [Azure Search Blob 인덱서](search-howto-indexing-azure-blob-storage.md) 는 단일 텍스트 청크로 구분된 텍스트 Blob을 구문 분석합니다. 그러나 CSV 데이터를 포함하는 Blob을 사용하는 경우 Blob의 각 줄을 별도 파일로 처리하려고 합니다. 예를 들어 다음 구분된 텍스트가 제공됩니다. 

    id, datePublished, tags
    1, 2016-01-12, "azure-search,azure,cloud" 
    2, 2016-07-07, "cloud,mobile" 

각각 "id", "datePublished" 및 "tags" 필드를 포함하는 두 개의 문서로 구문 분석하려고 할 수 있습니다.

이 문서에서는 Azure Search Blob 인덱서를 사용하여 CSV Blob을 구문 분석하는 방법을 배웁니다. 

> [!IMPORTANT]
> 이 기능은 현재 미리 보기 상태입니다. **2015-02-28-Preview**버전을 사용하여 REST API로만 제공됩니다. 미리 보기 API는 테스트 및 평가 용도로 제공되며 프로덕션 환경에는 사용되지 않는다는 점을 유념하세요. 
> 
> 

## <a name="setting-up-csv-indexing"></a>CSV 인덱싱 설정
CSV Blob을 인덱싱하려면 `delimitedText` 구문 분석 모드를 사용하여 인덱서 정의를 만들거나 업데이트합니다.  

    {
      "name" : "my-csv-indexer",
      ... other indexer properties
      "parameters" : { "configuration" : { "parsingMode" : "delimitedText", "firstLineContainsHeaders" : true } }
    }

인덱서 만들기 API에 대한 자세한 내용은 [인덱서 만들기](https://docs.microsoft.com/rest/api/searchservice/create-indexer)를 확인하세요.

`firstLineContainsHeaders` 은(는) 각 Blob의 첫 번째(비어 있지 않은) 줄이 헤더를 포함하는 것을 나타냅니다.
Blob이 초기 헤더 줄을 포함하지 않는 경우 헤더는 인덱서 구성에서 지정되어야 합니다. 

    "parameters" : { "configuration" : { "parsingMode" : "delimitedText", "delimitedTextHeaders" : "id,datePublished,tags" } } 

`delimitedTextDelimiter` 구성 설정을 사용하여 구분 기호 문자를 사용자 지정할 수 있습니다. 예: 

    "parameters" : { "configuration" : { "parsingMode" : "delimitedText", "delimitedTextDelimiter" : "|" } }

> [!NOTE]
> 현재는 UTF-8 인코딩만 지원됩니다. 다른 인코딩에 대한 지원이 필요한 경우 [UserVoice 사이트](https://feedback.azure.com/forums/263029-azure-search)를 통해 알려주세요.

> [!IMPORTANT]
> 구분된 텍스트 구문 분석 모드를 사용하는 경우 Azure Search는 데이터 원본의 모든 Blob을 CSV로 가정합니다. 동일한 데이터 원본에서 CSV 및 비 CSV Blob을 지원해야 하는 경우 [UserVoice 사이트](https://feedback.azure.com/forums/263029-azure-search)를 통해 알려주세요.
> 
> 

## <a name="request-examples"></a>요청 예제
다음은 이를 모두 포함한 전체 페이로드 예제입니다. 

데이터 원본: 

    POST https://[service name].search.windows.net/datasources?api-version=2015-02-28-Preview
    Content-Type: application/json
    api-key: [admin key]

    {
        "name" : "my-blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>;" },
        "container" : { "name" : "my-container", "query" : "<optional, my-folder>" }
    }   

인덱서:

    POST https://[service name].search.windows.net/indexers?api-version=2015-02-28-Preview
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "my-csv-indexer",
      "dataSourceName" : "my-blob-datasource",
      "targetIndexName" : "my-target-index",
      "parameters" : { "configuration" : { "parsingMode" : "delimitedText", "delimitedTextHeaders" : "id,datePublished,tags" } }
    }

## <a name="help-us-make-azure-search-better"></a>Azure Search 개선 지원
기능 요청 또는 개선에 대한 아이디어가 있는 경우 [UserVoice 사이트](https://feedback.azure.com/forums/263029-azure-search/)를 통해 연락해 주세요.

