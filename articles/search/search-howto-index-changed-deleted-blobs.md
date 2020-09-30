---
title: 변경 및 삭제 된 blob
titleSuffix: Azure Cognitive Search
description: Azure Blob storage에서 가져오는 초기 검색 인덱스 빌드 후 후속 인덱싱이 변경 되거나 삭제 된 blob만 선택할 수 있습니다. 이 문서에서는 세부 정보에 대해 설명 합니다.
manager: nitinme
author: MarkHeff
ms.author: maheff
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 09/25/2020
ms.openlocfilehash: 2e73039418233c97fc20242ed7af7df14c5b47ee
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/29/2020
ms.locfileid: "91534780"
---
# <a name="how-to-set-up-change-and-deletion-detection-for-blobs-in-azure-cognitive-search-indexing"></a>Azure Cognitive Search 인덱싱에서 blob에 대 한 변경 및 삭제 검색을 설정 하는 방법

초기 검색 인덱스를 만든 후에는 초기 실행 이후에 만들어지거나 삭제 된 문서만 선택 하도록 후속 인덱서 작업을 구성할 수 있습니다. Azure Blob 저장소에서 발생 하는 검색 콘텐츠의 경우 인덱싱을 트리거하는 일정을 사용 하면 변경 내용 검색이 자동으로 발생 합니다. 기본적으로 서비스는 blob의 타임 스탬프에 따라 결정 된 대로 변경 된 blob만 다시 인덱싱합니다 `LastModified` . 검색 인덱서에 의해 지원 되는 다른 데이터 원본과 달리 blob에는 항상 타임 스탬프가 있으므로 변경 검색 정책을 수동으로 설정 하지 않아도 됩니다.

변경 검색은 지정 되지만 삭제 검색은 그렇지 않습니다. 삭제 된 문서를 검색 하려면 "일시 삭제" 방법을 사용 해야 합니다. Blob을 완전히 삭제해도 해당 문서는 검색 인덱스에서 제거되지 않습니다.

일시 삭제 방식을 구현 하는 방법에는 두 가지가 있습니다. 두 가지 모두 아래에 설명 되어 있습니다.

## <a name="native-blob-soft-delete-preview"></a>네이티브 Blob 일시 삭제(미리 보기)

> [!IMPORTANT]
> 기본 blob 일시 삭제에 대 한 지원은 미리 보기 상태입니다. 미리 보기 기능은 서비스 수준 계약 없이 제공되며, 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요. [REST API 버전 2020-06-30-미리 보기](./search-api-preview.md) 에서이 기능을 제공 합니다. 현재 포털 또는 .NET SDK가 지원 되지 않습니다.

> [!NOTE]
> 네이티브 blob 일시 삭제 정책을 사용 하는 경우 인덱스의 문서에 대 한 문서 키는 blob 속성 또는 blob 메타 데이터 여야 합니다.

이 방법에서는 Azure Blob 저장소에서 제공 하는 [기본 blob 일시 삭제](../storage/blobs/soft-delete-blob-overview.md) 기능을 사용 합니다. 저장소 계정에서 기본 blob 일시 삭제를 사용 하는 경우 데이터 원본에 기본 일시 삭제 정책이 설정 되어 있고 인덱서가 일시 삭제 된 상태로 전환 된 blob을 찾은 경우 인덱서는 인덱스에서 해당 문서를 제거 합니다. Azure Data Lake Storage Gen2에서 blob을 인덱싱하는 경우에는 네이티브 blob 일시 삭제 정책이 지원 되지 않습니다.

다음 단계를 따르십시오.

1. [Azure Blob storage에 대해 네이티브 일시 삭제를](../storage/blobs/soft-delete-blob-overview.md)사용 하도록 설정 합니다. 보존 정책을 인덱서 간격 일정 보다 훨씬 더 높은 값으로 설정 하는 것이 좋습니다. 이러한 방식으로 인덱서를 실행 하는 데 문제가 있거나 인덱싱할 문서 수가 많은 경우 인덱서는 결국 일시 삭제 된 blob을 처리 하는 데 많은 시간이 발생 합니다. Azure Cognitive Search 인덱서는 blob이 일시 삭제 된 상태에 있는 동안 blob을 처리 하는 경우에만 인덱스에서 문서를 삭제 합니다.

1. 데이터 원본에 대 한 기본 blob 일시 삭제 검색 정책을 구성 합니다. 아래에 예제가 나와 있습니다. 이 기능은 미리 보기 상태 이므로 미리 보기 REST API를 사용 해야 합니다.

1. 인덱서를 실행 하거나 인덱서를 일정에 따라 실행 하도록 설정 합니다. 인덱서를 실행 하 고 blob을 처리 하면 문서가 인덱스에서 제거 됩니다.

    ```http
    PUT https://[service name].search.windows.net/datasources/blob-datasource?api-version=2020-06-30-Preview
    Content-Type: application/json
    api-key: [admin key]
    {
        "name" : "blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "<your storage connection string>" },
        "container" : { "name" : "my-container", "query" : null },
        "dataDeletionDetectionPolicy" : {
            "@odata.type" :"#Microsoft.Azure.Search.NativeBlobSoftDeleteDeletionDetectionPolicy"
        }
    }
    ```

### <a name="reindexing-un-deleted-blobs-using-native-soft-delete-policies"></a>삭제 되지 않은 blob 인덱스 (네이티브 일시 삭제 정책 사용)

저장소 계정에서 기본 일시 삭제를 사용 하도록 설정 하 여 Azure Blob storage에서 blob을 삭제 하면 blob은 일시 삭제 된 상태로 전환 되어 보존 기간 내에 해당 blob을 삭제 하지 않아도 되는 옵션을 제공 합니다. 인덱서를 처리 한 후 삭제를 취소 하면 인덱서가 복원 된 blob을 항상 인덱싱하지는 않습니다. 이는 인덱서는 blob의 타임 스탬프에 따라 인덱싱할 blob을 결정 하기 때문입니다 `LastModified` . 일시 삭제 된 blob을 삭제 하지 않으면 해당 `LastModified` 타임 스탬프가 업데이트 되지 않으므로 인덱서가 최신 타임 스탬프를 사용 하 여 blob을 이미 처리 한 경우에는 삭제 되지 않은 `LastModified` blob의 인덱스를 다시 만들지 않습니다. 

삭제 되지 않은 blob이 인덱싱해야이 되도록 하려면 blob의 타임 스탬프를 업데이트 해야 합니다 `LastModified` . 이 작업을 수행 하는 한 가지 방법은 해당 blob의 메타 데이터를 다시 저장는 것입니다. 메타 데이터를 변경할 필요는 없지만, `LastModified` 인덱서는이 blob을 다시 인덱싱 해야 한다는 것을 알 수 있도록 메타 데이터를 다시 저장 blob의 타임 스탬프를 업데이트 합니다.

## <a name="soft-delete-using-custom-metadata"></a>사용자 지정 메타 데이터를 사용 하 여 일시 삭제

이 방법에서는 blob의 메타 데이터를 사용 하 여 검색 인덱스에서 문서를 제거 해야 하는 시기를 표시 합니다. 이 메서드에는 인덱스에서 검색 문서를 삭제 한 다음 Azure Storage에서 blob을 삭제 하는 두 가지 별도의 작업이 필요 합니다.

다음 단계를 따르십시오.

1. Blob에 사용자 지정 메타 데이터 키-값 쌍을 추가 하 여 논리적으로 삭제 되는 Azure Cognitive Search를 표시 합니다.

1. 데이터 원본에 대 한 일시 삭제 열 검색 정책을 구성 합니다. 아래에 예제가 나와 있습니다.

1. 인덱서가 blob을 처리 하 고 인덱스에서 문서를 삭제 한 후에는 Azure Blob storage에서 blob을 삭제할 수 있습니다.

예를 들어 다음 정책은 `true` 값의 메타데이터 속성 `IsDeleted`가 있는 경우 Blob을 삭제해야 하는 것으로 간주합니다.

```http
    PUT https://[service name].search.windows.net/datasources/blob-datasource?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key]

    {
        "name" : "blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "<your storage connection string>" },
        "container" : { "name" : "my-container", "query" : null },
        "dataDeletionDetectionPolicy" : {
            "@odata.type" :"#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy",
            "softDeleteColumnName" : "IsDeleted",
            "softDeleteMarkerValue" : "true"
        }
    }
```

### <a name="reindexing-un-deleted-blobs-using-custom-metadata"></a>삭제 되지 않은 blob의 인덱스 (사용자 지정 메타 데이터 사용)

인덱서는 삭제 된 blob을 처리 하 고 인덱스에서 해당 하는 검색 문서를 제거한 후 blob의 `LastModified` 타임 스탬프가 마지막 인덱서 실행 보다 오래 된 경우 나중에 복원 하는 경우 해당 blob을 다시 방문 하지 않습니다.

해당 문서를 다시 인덱싱 하려면 해당 `"softDeleteMarkerValue" : "false"` blob에 대 한를 변경 하 고 인덱서를 다시 실행 합니다.

## <a name="help-us-make-azure-cognitive-search-better"></a>Azure Cognitive Search 향상에 도움을 주세요.

요청할 기능이 있거나 개선을 위한 아이디어가 있는 경우 [UserVoice](https://feedback.azure.com/forums/263029-azure-search/)에서 입력을 제공하세요. 기존 기능을 사용 하는 데 도움이 필요 하면 [Stack Overflow](https://stackoverflow.microsoft.com/questions/tagged/18870)에 질문을 게시 하세요.

## <a name="next-steps"></a>다음 단계

* [Azure Cognitive Search의 인덱서](search-indexer-overview.md)
* [Blob 인덱서를 구성 하는 방법](search-howto-indexing-azure-blob-storage.md)
* [Blob 인덱싱 개요](search-blob-storage-integration.md)