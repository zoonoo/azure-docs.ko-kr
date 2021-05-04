---
title: Azure Data Lake Storage Gen2(미리 보기) 검색
titleSuffix: Azure Cognitive Search
description: Azure Data Lake Storage Gen2에서 콘텐츠 및 메타데이터를 인덱싱하는 방법을 알아봅니다. 이 기능은 현재 공개 미리 보기로 제공됩니다.
manager: nitinme
author: markheff
ms.author: maheff
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 07/11/2020
ms.openlocfilehash: 51c32501015a16819ba9316272ed0d522e1083d7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100545567"
---
# <a name="indexing-documents-in-azure-data-lake-storage-gen2"></a>Azure Data Lake Storage Gen2에서 문서 인덱싱

> [!IMPORTANT] 
> Azure Data Lake Storage Gen2 지원은 현재 공개 미리 보기 상태입니다. 미리 보기 기능은 서비스 수준 계약 없이 제공되며, 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요. [이 양식](https://aka.ms/azure-cognitive-search/indexer-preview)을 작성하여 미리 보기에 대한 액세스를 요청할 수 있습니다. [REST API 버전 2020-06-30-Preview](search-api-preview.md) 및 포털에서 이 기능을 제공합니다. 현재 .NET SDK는 지원되지 않습니다.


Azure Storage 계정을 설정할 때 [계층 구조 네임스페이스](../storage/blobs/data-lake-storage-namespace.md)를 사용하도록 설정할 수 있습니다. 이를 통해 계정의 콘텐츠 컬렉션을 디렉터리 및 중첩된 하위 디렉터리의 계층 구조로 구성할 수 있습니다. 계층 구조 네임스페이스를 사용하도록 설정하여 [Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md)를 활성화합니다.

이 문서에서는 Azure Data Lake Storage Gen2에 있는 문서를 인덱싱하는 방법을 설명합니다.

## <a name="set-up-azure-data-lake-storage-gen2-indexer"></a>Azure Data Lake Storage Gen2 인덱서 설정

Data Lake Storage Gen2에서 콘텐츠를 인덱싱하려면 몇 가지 단계를 수행해야 합니다.

### <a name="step-1-sign-up-for-the-preview"></a>1단계: 미리 보기 등록

[이 양식](https://aka.ms/azure-cognitive-search/indexer-preview)을 작성하여 Data Lake Storage Gen2 인덱서 미리 보기를 등록합니다. 미리 보기에 동의하면 확인 이메일을 받게 됩니다.

### <a name="step-2-follow-the-azure-blob-storage-indexing-setup-steps"></a>2단계: Azure Blob Storage 인덱싱 설정 단계 수행

미리 보기 등록이 성공한 것을 확인하면 인덱싱 파이프라인을 만들 준비가 완료된 것입니다.

[REST API 버전 2020-06-30-Preview](search-api-preview.md) 또는 포털을 사용하여 Data Lake Storage Gen2에서 콘텐츠 및 메타데이터를 인덱싱할 수 있습니다. 지금은 .NET SDK 지원이 없습니다.

Data Lake Storage Gen2에서의 콘텐츠 인덱싱은 Azure Blob Storage의 콘텐츠 인덱싱과 동일합니다. Data Lake Storage Gen2 데이터 원본, 인덱스 및 인덱서를 설정하는 방법을 이해하려면 [Azure Cognitive Search를 사용하여 Azure Blob Storage 문서를 인덱싱하는 방법](search-howto-indexing-azure-blob-storage.md)을 참조하세요. 또한 Blob Storage 문서는 지원되는 문서 형식, 추출되는 Blob 메타데이터 속성, 증분 인덱싱 등에 대한 정보를 제공합니다. 이 정보는 Data Lake Storage Gen2에 대해서도 동일합니다.

## <a name="access-control"></a>Access Control

Azure Data Lake Storage Gen2는 Azure RBAC(Azure 역할 기반 액세스 제어)와 POSIX 같은 ACL(액세스 제어 목록)을 모두 지원하는 [액세스 제어 모델](../storage/blobs/data-lake-storage-access-control.md)을 구현합니다. Data Lake Storage Gen2에서 콘텐츠를 인덱싱할 때 Azure Cognitive Search는 콘텐츠에서 Azure RBAC 및 ACL 정보를 추출하지 않습니다. 따라서 이 정보는 Azure Cognitive Search 인덱스에 포함되지 않습니다.

인덱스의 각 문서에 대한 액세스 제어를 유지하는 것이 중요한 경우에는 애플리케이션 개발자가 [보안 조정](./search-security-trimming-for-azure-search.md)을 구현하는 것이 중요합니다.

## <a name="change-detection"></a>변경 내용 검색

Data Lake Storage Gen2 인덱서는 변경 내용 검색을 지원합니다. 즉, 인덱서가 실행될 때 Blob의 `LastModified` 타임스탬프를 기준으로 변경된 Blob만 다시 인덱싱합니다.

> [!NOTE] 
> Data Lake Storage Gen2에서는 디렉터리의 이름을 바꿀 수 있습니다. 디렉터리의 이름을 바꾸면 해당 디렉터리의 Blob에 대한 타임스탬프가 업데이트되지 않습니다. 결과적으로 인덱서는 이러한 Blob을 다시 인덱싱하지 않습니다. 디렉터리 이름 바꾸기 후에 디렉터리의 Blob이 새 URL을 포함하여 인덱싱해야 하는 경우 나중에 실행되는 동안 인덱서가 다시 인덱싱하도록 디렉터리의 모든 Blob에 대한 `LastModified` 타임스탬프를 업데이트해야 합니다.