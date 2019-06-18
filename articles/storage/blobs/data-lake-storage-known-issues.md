---
title: Azure Data Lake Storage Gen2에서 알려진 문제 | Microsoft Docs
description: Azure Data Lake Storage Gen2에서 제한 사항 및 알려진 문제에 대해 알아보기
services: storage
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 04/26/2019
ms.author: normesta
ms.openlocfilehash: 446b49cbf3fdf3d4cde37b2a7c4ac2d9f0a811b1
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67061339"
---
# <a name="known-issues-with-azure-data-lake-storage-gen2"></a>Azure Data Lake Storage Gen2에서 알려진 문제

이 문서는 기능 및 도구는 아직 지원 되지 않거나 부분적 으로만 지원 되는 계층적 네임 스페이스 (Azure Data Lake 저장소 Gen2)를 포함 하는 저장소 계정으로 나열 합니다.

<a id="blob-apis-disabled" />

## <a name="blob-storage-apis"></a>Blob Storage API

Blob 저장소 Api는 Blob Storage Api를 Azure Data Lake Gen2 Api를 사용 하 여 상호 운용 가능한 아직 때문에 발생할 수 있는 기능 작동 문제를 방지 하기 위해 비활성화 됩니다.

### <a name="what-to-do-with-existing-tools-applications-and-services"></a>기존 도구, 응용 프로그램 및 서비스를 사용 하 여 수행할 작업

모든 계정에 업로드 하는 콘텐츠를 사용 하 여 작업을 사용 하려면 모든 Blob Api를이 사용 하는 경우 다음 사용 하지 않는 Blob 저장소 계정에는 계층적 네임 스페이스 Blob Api Azure Data Lake Gen2 Api와 상호 운용이 가능 해질 때까지 합니다.

계층적 네임 스페이스가 없는 저장소 계정을 사용 하 여 다음 디렉터리 및 파일 시스템 액세스 제어 목록 같은 Data Lake 저장소 Gen2 특정 기능에 액세스할 수 없는 의미 합니다.

### <a name="what-to-do-with-unmanaged-virtual-machine-vm-disks"></a>관리 되지 않는 가상 머신 (VM) 디스크를 사용 하 여 수행할 작업

Blob Storage Api를 사용 안 함된에 따라 달라 집니다, 그리고이 저장소 계정에는 계층적 네임 스페이스를 사용 하도록 설정 하려는 보십시오 계층 구조 네임 스페이스 기능을 사용 하지 않은 저장소 계정에 배치 됩니다.

### <a name="what-to-do-if-you-used-blob-apis-to-load-data-before-blob-apis-were-disabled"></a>Blob Api 사용 하지 않도록 설정 된 전에 데이터를 로드 하려면 Blob Api를 사용 하는 경우 수행할 작업

사용하지 않도록 설정하기 전에 이러한 API를 사용하여 데이터를 로드했고 해당 데이터에 액세스하기 위한 프로덕션 요구 사항이 있는 경우에는 다음 정보를 사용하여 Microsoft 지원에 문의하세요.

> [!div class="checklist"]
> * 구독 ID (GUID, 이름 아님).
> * 저장소 계정 이름입니다.
> * 프로덕션 환경에 영향을 받는 적극적으로 여부와 그는 저장소 계정.
> * 프로덕션 환경에서 많은 영향을 받지 않더라도 이 데이터를 다른 스토리지 계정에 복사해야 하는지 여부를 알려 주세요. 그렇다면 이유는 무엇인가요?

이러한 상황에서는 이 데이터를 계층 구조 네임스페이스 기능이 사용되지 않는 스토리지 계정에 복사할 수 있도록 제한된 기간 동안 Blob API에 대한 액세스를 복원할 수 있습니다.

## <a name="all-other-features-and-tools"></a>다른 모든 기능 및 도구

다음 표에서 다른 모든 기능 및 도구는 아직 지원 되지 않거나 부분적 으로만 지원 되는 계층적 네임 스페이스 (Azure Data Lake 저장소 Gen2)를 포함 하는 저장소 계정으로 나열 합니다.

| 기능 / 도구    | 자세한 정보    |
|--------|-----------|
| **Data Lake 저장소 Gen2 저장소 계정에 대 한 Api** | 부분적으로 지원됨 <br><br>Data Lake 저장소 Gen2를 사용할 수 있습니다 **REST** Api와 이지만.NET, Java, Python Sdk와 같은 다른 Blob Sdk의 Api는 아직 사용할 수 없습니다.|
| **AZCopy** | 버전 별로 지원 <br><br>최신 버전의 AzCopy 사용 하 여 ([AzCopy v10](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10?toc=%2fazure%2fstorage%2ftables%2ftoc.json)). 이전 버전의 AzCopy AzCopy v8.1 등 지원 되지 않습니다.|
| **Azure Blob 저장소 수명 주기 관리 정책** | 아직 지원 되지 않음 |
| **Azure Content Delivery Network (CDN)** | 아직 지원 되지 않음|
| **Azure Event Grid** | 아직 지원 되지 않음 |
| **Azure search** |아직 지원 되지 않음|
| **Azure Storage 탐색기** | 버전 별로 지원 <br><br>사용 하 여 버전만 `1.6.0` 이상. <br>버전 `1.6.0` 로 제공 되는 [무료 다운로드](https://azure.microsoft.com/features/storage-explorer/)합니다.|
| **Blob 컨테이너 Acl** |아직 지원 되지 않음|
| **Blobfuse** |아직 지원 되지 않음|
| **사용자 지정 도메인** |아직 지원 되지 않음|
| **진단 로그** |아직 지원 되지 않음|
| **파일 시스템 탐색기** | 제한적된 지원 |
| **변경할 수 없는 저장소** |아직 지원 되지 않음 <br><br>변경할 수 없는 저장소에서 데이터를 저장 하는 기능을 제공 된 [웜 (한 번 쓰기, 읽기 여러)](https://docs.microsoft.com/azure/storage/blobs/storage-blob-immutable-storage) 상태입니다.|
| **개체 수준 계층** |아직 지원 되지 않음 <br><br>예를 들면 다음과 같습니다. Premium, 핫, 콜드, 및 보관 계층입니다.|
| **Powershell 및 CLI 지원** | 제한 된 기능 <br><br>Powershell 또는 CLI를 사용 하 여 계정을 만들면 됩니다. 작업을 수행 하거나 파일 시스템, 디렉터리 및 파일에 액세스 제어 목록을 설정할 수 없습니다.|
| **정적 웹 사이트** |아직 지원 되지 않음 <br><br>파일을 제공 하는 기능 특히 [정적 웹 사이트](https://docs.microsoft.com/azure/storage/blobs/storage-blob-static-website)합니다.|
| **타사 응용 프로그램** | 제한적된 지원 <br><br>REST Api를 사용 하는 타사 응용 프로그램이 계속 Data Lake 저장소 Gen2를 사용 하 여 사용 하는 경우 작동 합니다. <br>Blob Api를 사용 하는 응용 프로그램에 있는 경우 해당 응용 프로그램을 가장 있을 문제 Data Lake 저장소 Gen2를 사용 하 여 해당 응용 프로그램을 사용 하는 경우입니다. 자세한 내용은 참조는 [Data Lake 저장소 Gen2 저장소 계정에 대 한 Api는 사용 하지 않도록 설정 하는 저장소 Blob](#blob-apis-disabled) 이 문서의 섹션입니다.|
| **버전 관리 기능** |아직 지원 되지 않음 <br><br>여기에 [스냅숏을](https://docs.microsoft.com/rest/api/storageservices/creating-a-snapshot-of-a-blob) 하 고 [일시 삭제](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete)합니다.|
|

