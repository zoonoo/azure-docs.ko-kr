---
title: 일반적인 검색 인덱서 문제 해결
titleSuffix: Azure Cognitive Search
description: 데이터 원본 연결, 방화벽 및 누락 된 문서를 포함 하 여 Azure Cognitive Search의 인덱서와 관련 된 오류 및 일반적인 문제를 해결 합니다.
manager: nitinme
author: mgottein
ms.author: magottei
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: c5a16d957f1e0414f92d0cc03442d88d438e4c92
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/23/2019
ms.locfileid: "72793620"
---
# <a name="troubleshooting-common-indexer-issues-in-azure-cognitive-search"></a>Azure Cognitive Search의 일반적인 인덱서 문제 해결

인덱서는 데이터를 Azure Cognitive Search로 인덱싱할 때 많은 문제가 발생할 수 있습니다. 오류의 주요 범주는 다음과 같습니다.

* [데이터 원본에 연결](#data-source-connection-errors)
* [문서 처리](#document-processing-errors)
* [인덱스에 대한 문서 수집](#index-errors)

## <a name="data-source-connection-errors"></a>데이터 원본 연결 오류

### <a name="blob-storage"></a>Blob Storage

#### <a name="storage-account-firewall"></a>스토리지 계정 방화벽

Azure Storage는 구성 가능한 방화벽을 제공합니다. 기본적으로 방화벽은 사용 하지 않도록 설정 되어 있으므로 Azure Cognitive Search는 저장소 계정에 연결할 수 있습니다.

방화벽을 사용하는 경우 특정 오류 메시지가 발생하지 않습니다. 일반적으로 방화벽 오류는 `The remote server returned an error: (403) Forbidden`과 같습니다.

[포털](https://docs.microsoft.com/azure/storage/common/storage-network-security#azure-portal)에서 방화벽을 사용하도록 설정했는지 확인할 수 있습니다. 유일 하 게 지원 되는 해결 방법은 [' 모든 네트워크 '](https://docs.microsoft.com/azure/storage/common/storage-network-security#azure-portal)에서 액세스를 허용 하도록 선택 하 여 방화벽을 비활성화 하는 것입니다.

인덱서에 연결 된 기술 없는 경우 검색 서비스의 IP 주소에 대 한 [예외를 추가](https://docs.microsoft.com/azure/storage/common/storage-network-security#managing-ip-network-rules) 하려고 할 _수_ 있습니다. 그러나이 시나리오는 지원 되지 않으며 작동 하지 않을 수 있습니다.

FQDN (`<your-search-service-name>.search.windows.net`)을 ping 하 여 검색 서비스의 IP 주소를 찾을 수 있습니다.

### <a name="cosmos-db"></a>Cosmos DB

#### <a name="indexing-isnt-enabled"></a>인덱싱을 사용하도록 설정하지 않았습니다.

Azure Cognitive Search는 Cosmos DB 인덱싱에 대해 암시적 종속성이 있습니다. Cosmos DB에서 자동 인덱싱을 해제 하는 경우 Azure Cognitive Search는 성공적인 상태를 반환 하지만 컨테이너 내용 인덱스에는 실패 합니다. 설정을 확인하고 인덱싱을 설정하는 방법에 대한 지침은 [Azure Cosmos DB에서 인덱싱 관리](https://docs.microsoft.com/azure/cosmos-db/how-to-manage-indexing-policy#use-the-azure-portal)를 참조하세요.

## <a name="document-processing-errors"></a>문서 처리 오류

### <a name="unprocessable-or-unsupported-documents"></a>처리할 수 없거나 지원되지 않는 문서

Blob 인덱서 [문서는 문서 형식을 명시적으로 지원합니다.](search-howto-indexing-azure-blob-storage.md#SupportedFormats) 경우에 따라 Blob Storage 컨테이너에는 지원되지 않는 문서가 포함되어 있습니다. 문제가 있는 다른 문서가 있을 수 있습니다. [구성 옵션을 변경](search-howto-indexing-azure-blob-storage.md#DealingWithErrors)하여 이러한 문서에서 인덱서를 중지하지 않을 수 있습니다.

```
PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2019-05-06
Content-Type: application/json
api-key: [admin key]

{
  ... other parts of indexer definition
  "parameters" : { "configuration" : { "failOnUnsupportedContentType" : false, "failOnUnprocessableDocument" : false } }
}
```

### <a name="missing-document-content"></a>누락된 문서 콘텐츠

Blob 인덱서는 [컨테이너의 blob에서 텍스트를 찾아서 추출합니다](search-howto-indexing-azure-blob-storage.md#how-azure-search-indexes-blobs). 텍스트 추출에 대한 몇 가지 문제는 다음과 같습니다.

* 문서에는 스캔된 이미지만 포함됩니다. 스캔된 이미지(JPG)와 같은 텍스트가 아닌 콘텐츠를 포함한 PDF Blob은 표준 Blob 인덱싱 파이프라인에서 결과를 생성하지 않습니다. 텍스트 요소가 있는 이미지 내용이 있는 경우 [인지 검색](cognitive-search-concept-image-scenarios.md) 을 사용 하 여 텍스트를 찾고 추출할 수 있습니다.
* Blob 인덱서는 메타데이터를 인덱스하도록 구성됩니다. 콘텐츠를 추출하려면 [콘텐츠 및 메타데이터를 모두 추출](search-howto-indexing-azure-blob-storage.md#controlling-which-parts-of-the-blob-are-indexed)하도록 Blob 인덱서를 구성해야 합니다.

```
PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2019-05-06
Content-Type: application/json
api-key: [admin key]

{
  ... other parts of indexer definition
  "parameters" : { "configuration" : { "dataToExtract" : "contentAndMetadata" } }
}
```

## <a name="index-errors"></a>인덱스 오류

### <a name="missing-documents"></a>누락된 문서

인덱서는 [데이터 원본](https://docs.microsoft.com/rest/api/searchservice/create-data-source)에서 문서를 찾습니다. 인덱싱되어야 하는 데이터 원본의 문서가 인덱스에서 누락된 것으로 나타나는 경우도 있습니다. 다음과 같은 오류가 발생할 수 있는 일반적인 몇 가지 이유가 있습니다.

* 문서는 인덱스되지 않았습니다. 성공적인 인덱서 실행을 위해 포털을 확인합니다.
* 문서는 인덱서 실행 후에 업데이트되었습니다. 인덱서가 [일정](https://docs.microsoft.com/rest/api/searchservice/create-indexer#indexer-schedule)대로 진행되는 경우 결국 문서를 다시 실행하고 선택합니다.
* 데이터 원본에 지정된 [쿼리](https://docs.microsoft.com/rest/api/searchservice/create-data-source#request-body-syntax)는 문서를 제외합니다. 인덱서는 데이터 원본에 포함되지 않는 문서를 인덱싱할 수 없습니다.
* [필드 매핑](https://docs.microsoft.com/rest/api/searchservice/create-indexer#fieldmappings) 또는 [AI 보강](https://docs.microsoft.com/azure/search/cognitive-search-concept-intro) 문서를 변경 하 여 원하는 것과 다르게 보입니다.
* [문서 조회 API](https://docs.microsoft.com/rest/api/searchservice/lookup-document)를 사용하여 문서를 찾습니다.
