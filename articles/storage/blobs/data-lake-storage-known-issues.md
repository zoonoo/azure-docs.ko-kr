---
title: Azure Data Lake Storage Gen2에서 알려진 문제 | Microsoft Docs
description: Azure Data Lake Storage Gen2에서 제한 사항 및 알려진 문제에 대해 알아보기
services: storage
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 12/05/2018
ms.author: normesta
ms.openlocfilehash: 5677649b8f002490900ec32bee954348b2f444e6
ms.sourcegitcommit: 3aa0fbfdde618656d66edf7e469e543c2aa29a57
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/05/2019
ms.locfileid: "55731549"
---
# <a name="known-issues-with-azure-data-lake-storage-gen2"></a>Azure Data Lake Storage Gen2에서 알려진 문제

이 문서에는 Azure Data Lake Storage Gen2에서 알려진 문제 및 임시 제한 사항이 포함됩니다.

## <a name="api-interoperability"></a>API 상호 운용성

Blob Storage API 및 Azure Data Lake Gen2 API는 서로 상호 운용되지 않습니다.

Blob API를 사용하는 사용자 지정 도구, 애플리케이션 또는 스크립트를 가지고 있고 이들을 사용하여 본인의 계정에 업로드하는 모든 콘텐츠로 작업하려는 경우 Blob API가 Azure Data Lake Gen2 API와 상호 운용할 수 있게 될 때까지 본인의 Blob 스토리지 계정에서 계층 구조 네임스페이스를 사용하도록 설정하지 마십시오. 계층 구조 네임스페이스 없이 Storage 계정을 사용하면 디렉터리 및 파일 시스템 액세스 제어 목록과 같은 Data Lake Storage Gen2 특정 기능에 액세스할 수 없습니다.

## <a name="blob-storage-apis"></a>Blob Storage API

Blob Storage API는 아직 Azure Data Lake Storage Gen2 계정에 지원되지 않습니다.

Blob Storage API가 Azure Data Lake Gen2 API와 상호 운용 가능하지 않기 때문에 발생할 수 있는 의도하지 않은 데이터 액세스 문제를 방지하기 위해 이러한 API는 사용하지 않도록 설정됩니다.

사용하지 않도록 설정하기 전에 이러한 API를 사용하여 데이터를 로드했고 해당 데이터에 액세스하기 위한 프로덕션 요구 사항이 있는 경우에는 다음 정보를 사용하여 Microsoft 지원에 문의하세요.

* 구독 ID(이름이 아닌 GUID)

* 스토리지 계정 이름

* 프로덕션 환경에서 많은 영향을 받는지 여부 및 영향을 받는다면 어떤 스토리지 계정에 해당하는지?

* 프로덕션 환경에서 많은 영향을 받지 않더라도 이 데이터를 다른 스토리지 계정에 복사해야 하는지 여부를 알려 주세요. 그렇다면 이유는 무엇인가요?

이러한 상황에서는 이 데이터를 계층 구조 네임스페이스가 사용되지 않는 스토리지 계정에 복사할 수 있도록 제한된 기간 동안 Blob API에 대한 액세스를 복원할 수 있습니다.

비관리형 VM(가상 머신) 디스크는 사용하지 않도록 설정된 Blob Storage API에 따라 달라집니다. 따라서 스토리지 계정에서 계층 구조 네임스페이스를 사용하려면 계층 구조 네임스페이스를 설정하지 않은 스토리지 계정에 비관리형 VM 디스크를 배치하는 것이 좋습니다.

## <a name="azure-storage-explorer"></a>Azure Storage 탐색기

Azure Storage 탐색기를 사용하여 Data Lake Storage Gen2 계정을 보거나 관리하려면 [무료 다운로드](https://azure.microsoft.com/features/storage-explorer/)로 제공되는 도구의 `1.6.0` 이상 버전이 있어야 합니다.

Azure Portal에 포함된 Storage 탐색기 버전은 현재 계층 구조 네임스페이스가 사용하도록 설정된 Data Lake Storage Gen2 계정 보기 또는 관리를 지원하지 않습니다.

## <a name="blob-viewing-tool"></a>Blob 보기 도구

Azure Portal의 Blob 보기 도구는 Azure Data Lake Storage Gen2를 제한적으로 지원합니다.

## <a name="third-party-applications"></a>타사 애플리케이션

타사 애플리케이션에서는 Azure Data Lake Storage Gen2를 지원하지 않습니다.

지원 여부는 각 타사 애플리케이션 공급자의 판단에 맡겨집니다. 현재 Blob Storage API 및 Azure Data Lake Storage Gen2 API는 동일한 콘텐츠를 관리하는 데 사용될 수 없습니다. 이러한 상호 운용성을 설정하기 위해 노력하고 있으므로 다양한 타사 도구가 자동으로 Azure Data Lake Storage Gen2를 지원하게 될 수 있습니다.

## <a name="azcopy-support"></a>AzCopy 지원

AzCopy 버전 8은 Azure Data Lake Storage Gen2를 지원하지 않습니다.

대신 Azure Data Lake Storage Gen2 엔드포인트를 지원하는 최신 미리 보기 버전의 AzCopy([AzCopy v10](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10?toc=%2fazure%2fstorage%2ftables%2ftoc.json))를 사용합니다.

## <a name="azure-event-grid"></a>Azure Event Grid

Azure Data Lake Gen2 계정이 아직 이벤트를 생성하지 않았기 때문에[Azure Event Grid](https://azure.microsoft.com/services/event-grid/)는 해당 계정의 이벤트를 수신하지 않습니다.  

## <a name="soft-delete-and-snapshots"></a>일시 삭제 및 스냅숏

일시 삭제 및 스냅숏은 Azure Data Lake Storage Gen2 계정에 지원되지 않습니다.

[스냅숏](https://docs.microsoft.com/rest/api/storageservices/creating-a-snapshot-of-a-blob) 및 [일시 삭제](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete)를 비롯한 모든 버전 관리 기능은 아직 계층 구조 네임스페이스를 사용하도록 설정한 Storage 계정에 지원되지 않습니다.

## <a name="object-level-storage-tiers"></a>개체 수준 스토리지 계층

개체 수준 스토리지 계층(핫, 콜드 및 보관)은 아직 Azure Data Lake Storage Gen 2 계정에 사용할 수 없지만 계층 구조 네임스페이스를 사용하도록 설정하지 않은 스토리지 계정에 사용할 수 있습니다.

## <a name="azure-blob-storage-lifecycle-management-preview-policies"></a>Azure Blob Storage 수명 주기 관리(미리 보기) 정책

Azure Blob Storage 수명 주기 관리(미리 보기) 정책은 아직 Azure Data Lake Storage Gen2 계정에 지원되지 않습니다.

이러한 정책은 계층 구조 네임스페이스를 사용하도록 설정하지 않은 스토리지 계정에 사용할 수 있습니다.

## <a name="diagnostic-logs"></a>진단 로그

진단 로그는 Azure Data Lake Storage Gen2 계정에 지원되지 않습니다.
