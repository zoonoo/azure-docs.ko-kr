---
title: Azure 데이터 레이크 스토리지 Gen2를 통해 검색(미리 보기)
titleSuffix: Azure Cognitive Search
description: Azure Data Lake 저장소 Gen2에서 콘텐츠 및 메타데이터를 인덱싱하는 방법을 알아봅니다. 이 기능은 현재 공개 미리 보기 상태입니다.
manager: nitinme
author: markheff
ms.author: maheff
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 4b725c8a1bf0649a640c02a9a1828ec9014d36d6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76905649"
---
# <a name="indexing-documents-in-azure-data-lake-storage-gen2"></a>Azure 데이터 레이크 저장소 Gen2의 문서 인덱싱

> [!IMPORTANT] 
> Azure 데이터 레이크 저장소 Gen2 지원은 현재 공개 미리 보기상태입니다. 미리 보기 기능은 서비스 수준 계약 없이 제공되며, 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요. [이 양식을](https://aka.ms/azure-cognitive-search/indexer-preview)작성하여 미리 보기에 대한 액세스를 요청할 수 있습니다. 이 기능은 [REST API 버전 2019-05-06-미리 보기](search-api-preview.md)에서 제공됩니다. 현재 포털 또는 .NET SDK 지원이 없습니다.


Azure 저장소 계정을 설정할 때 [계층적 네임스페이스를](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-namespace)사용하도록 설정할 수 있습니다. 이렇게 하면 계정의 콘텐츠 컬렉션을 디렉터리 및 중첩 하위 디렉터리 계층 구조로 구성할 수 있습니다. 계층적 네임스페이스를 사용하도록 설정하면 [Azure Data Lake 저장소 Gen2를](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-introduction)사용하도록 설정합니다.

이 문서에서는 Azure Data Lake Storage Gen2에 있는 인덱싱 문서를 시작하는 방법을 설명합니다.

## <a name="set-up-azure-data-lake-storage-gen2-indexer"></a>Azure 데이터 레이크 스토리지 Gen2 인덱서 설정

Data Lake Storage Gen2에서 콘텐츠를 인덱싱하기 위해 완료하는 데 필요한 몇 가지 단계가 있습니다.

### <a name="step-1-sign-up-for-the-preview"></a>1단계: 미리 보기 등록

[이 양식을](https://aka.ms/azure-cognitive-search/indexer-preview)작성하여 데이터 레이크 스토리지 Gen2 인덱서 미리 보기에 등록하십시오. 미리 보기에 수락되면 확인 이메일을 받게 됩니다.

### <a name="step-2-follow-the-azure-blob-storage-indexing-setup-steps"></a>2단계: Azure Blob 저장소 인덱싱 설정 단계를 따릅니다.

미리 보기 등록이 성공했음을 확인한 후에는 인덱싱 파이프라인을 만들 준비가 된 것입니다.

[REST API 버전 2019-05-06 미리 보기를](search-api-preview.md)사용하여 Data Lake Storage Gen2의 콘텐츠 및 메타데이터를 인덱싱할 수 있습니다. 현재 포털 또는 .NET SDK 지원이 없습니다.

데이터 레이크 저장소 Gen2의 인덱싱 콘텐츠는 Azure Blob 저장소의 인덱싱 콘텐츠와 동일합니다. Data Lake Storage Gen2 데이터 원본, 인덱스 및 인덱서를 설정하는 방법을 이해하려면 [Azure Cognitive Search를 사용하여 Azure Blob 저장소에서 문서를 인덱싱하는 방법을](search-howto-indexing-azure-blob-storage.md)참조하십시오. 또한 Blob 저장소 문서에서는 지원되는 문서 형식, 추출되는 Blob 메타데이터 속성, 증분 인덱싱 등에 대한 정보를 제공합니다. 이 정보는 데이터 레이크 스토리지 Gen2에 대해 동일합니다.

## <a name="access-control"></a>Access Control

Azure Data Lake Storage Gen2는 AZURE 역할 기반 액세스 제어(RBAC) 및 POSIX와 같은 액세스 제어 목록(ACL)을 모두 지원하는 액세스 제어 [모델을](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control) 구현합니다. 데이터 레이크 저장소 Gen2에서 콘텐츠를 인덱싱할 때 Azure Cognitive Search는 콘텐츠에서 RBAC 및 ACL 정보를 추출하지 않습니다. 따라서 이 정보는 Azure 인지 검색 인덱스에 포함되지 않습니다.

인덱스의 각 문서에 대한 액세스 제어를 유지하는 것이 중요한 경우 응용 프로그램 개발자는 [보안 트리밍을](https://docs.microsoft.com/azure/search/search-security-trimming-for-azure-search)구현해야 합니다.

## <a name="change-detection"></a>변경 감지

데이터 레이크 스토리지 Gen2 인덱서가 변경 감지를 지원합니다. 즉, 인덱서를 실행하면 Blob의 `LastModified` 타임스탬프에 의해 결정된 대로 변경된 Blob만 다시 인덱싱됩니다.

> [!NOTE] 
> 데이터 레이크 스토리지 Gen2를 사용하면 디렉토리의 이름을 바꿀 수 있습니다. 디렉터리 이름이 바뀌면 해당 디렉터리에서 Blob에 대한 타임스탬프가 업데이트되지 않습니다. 따라서 인덱서에서는 해당 Blob을 다시 인덱싱하지 않습니다. 이제 새 URL이 있기 때문에 디렉터리 이름을 변경한 후 디렉터리에서 Blob을 다시 인덱싱해야 `LastModified` 하는 경우 인덱서가 나중에 실행하는 동안 다시 인덱싱할 수 있도록 디렉터리에 있는 모든 Blob에 대한 타임스탬프를 업데이트해야 합니다.
