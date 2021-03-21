---
title: CSV blob 검색
titleSuffix: Azure Cognitive Search
description: DelimitedText 구문 분석 모드를 사용 하 여 Azure Blob storage에서 CSV를 추출 하 고 가져옵니다.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/01/2021
ms.openlocfilehash: d9633031ca8358ab0498c2e806b22e6c4ddd3eab
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "99430482"
---
# <a name="how-to-index-csv-blobs-using-delimitedtext-parsing-mode-and-blob-indexers-in-azure-cognitive-search"></a>Azure Cognitive Search에서 delimitedText 구문 분석 모드 및 Blob 인덱서를 사용 하 여 CSV blob을 인덱싱하는 방법

Azure Cognitive Search [blob 인덱서](search-howto-indexing-azure-blob-storage.md) 는 csv `delimitedText` 파일에 대 한 구문 분석 모드를 제공 하 여 csv의 각 줄을 별도의 검색 문서로 처리 합니다. 예를 들어 다음과 같이 쉼표로 구분 된 텍스트를 지정 하면 `delimitedText` 검색 인덱스에 두 개의 문서가 생성 됩니다. 

```text
id, datePublished, tags
1, 2016-01-12, "azure-search,azure,cloud"
2, 2016-07-07, "cloud,mobile"
```

`delimitedText`구문 분석 모드가 없으면 CSV 파일의 전체 내용이 하나의 검색 문서로 처리 됩니다.

단일 blob에서 여러 검색 문서를 만들 때마다 인덱싱 blob을 검토 하 여 [여러 검색](search-howto-index-one-to-many-blobs.md) 문서를 생성 하 여 문서 키 할당의 작동 방식을 이해 해야 합니다. Blob 인덱서는 각 새 문서를 고유 하 게 정의 하는 값을 찾거나 생성할 수 있습니다. 특히 `AzureSearch_DocumentKey` blob이 더 작은 부분으로 구문 분석 될 때 생성 된 일시적을 만들 수 있습니다. 여기서 값은 인덱스에서 검색 문서의 키로 사용 됩니다.

## <a name="setting-up-csv-indexing"></a>CSV 인덱싱 설정

CSV blob을 인덱싱 하려면 `delimitedText` [create 인덱서](/rest/api/searchservice/create-indexer) 요청에서 구문 분석 모드를 사용 하 여 인덱서 정의를 만들거나 업데이트 합니다.

```http
{
  "name" : "my-csv-indexer",
  ... other indexer properties
  "parameters" : { "configuration" : { "parsingMode" : "delimitedText", "firstLineContainsHeaders" : true } }
}
```

`firstLineContainsHeaders` 은(는) 각 Blob의 첫 번째(비어 있지 않은) 줄이 헤더를 포함하는 것을 나타냅니다.
Blob이 초기 헤더 줄을 포함하지 않는 경우 헤더는 인덱서 구성에서 지정되어야 합니다. 

```http
"parameters" : { "configuration" : { "parsingMode" : "delimitedText", "delimitedTextHeaders" : "id,datePublished,tags" } } 
```

`delimitedTextDelimiter` 구성 설정을 사용하여 구분 기호 문자를 사용자 지정할 수 있습니다. 예를 들면 다음과 같습니다.

```http
"parameters" : { "configuration" : { "parsingMode" : "delimitedText", "delimitedTextDelimiter" : "|" } }
```

> [!NOTE]
> 현재는 UTF-8 인코딩만 지원됩니다. 다른 인코딩을 지원해야 하는 경우 [UserVoice](https://feedback.azure.com/forums/263029-azure-search)에서 투표하세요.

> [!IMPORTANT]
> 구분 된 텍스트 구문 분석 모드를 사용 하는 경우 Azure Cognitive Search는 데이터 원본의 모든 blob이 CSV 라고 가정 합니다. 동일한 데이터 원본에서 CSV 및 비 CSV Blob을 지원해야 하는 경우 [UserVoice](https://feedback.azure.com/forums/263029-azure-search)에서 투표하세요.
>

## <a name="request-examples"></a>요청 예제

다음은 이를 모두 포함한 전체 페이로드 예제입니다. 

데이터 원본: 

```http
POST https://[service name].search.windows.net/datasources?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
    "name" : "my-blob-datasource",
    "type" : "azureblob",
    "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>;" },
    "container" : { "name" : "my-container", "query" : "<optional, my-folder>" }
}   
```

인덱서:

```http
POST https://[service name].search.windows.net/indexers?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
  "name" : "my-csv-indexer",
  "dataSourceName" : "my-blob-datasource",
  "targetIndexName" : "my-target-index",
  "parameters" : { "configuration" : { "parsingMode" : "delimitedText", "delimitedTextHeaders" : "id,datePublished,tags" } }
}
```


