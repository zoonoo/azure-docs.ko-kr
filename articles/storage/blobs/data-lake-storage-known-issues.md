---
title: Azure Data Lake Storage Gen2에서 알려진 문제 | Microsoft Docs
description: Azure 데이터 레이크 저장소 Gen2의 제한 사항 및 알려진 문제에 대해 알아봅니다.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 03/20/2020
ms.author: normesta
ms.reviewer: jamesbak
ms.openlocfilehash: dfa4d65464192b90d4a6f74255faaf8b664ce118
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/22/2020
ms.locfileid: "81767968"
---
# <a name="known-issues-with-azure-data-lake-storage-gen2"></a>Azure Data Lake Storage Gen2에서 알려진 문제

이 문서에서는 Azure Data Lake 저장소 Gen2의 제한 사항 및 알려진 문제에 대해 설명합니다.

## <a name="supported-blob-storage-features"></a>지원되는 Blob 스토리지 기능

이제 계층적 네임스페이스가 있는 계정에서 Blob 저장소 기능이 증가하고 있습니다. 전체 목록은 Azure [Data Lake 저장소 Gen2에서 사용할 수 있는 Blob 저장소 기능을](data-lake-storage-supported-blob-storage-features.md)참조하십시오.

## <a name="supported-azure-service-integrations"></a>지원되는 Azure 서비스 통합

Azure Data Lake Storage Gen2는 데이터를 수집하고 분석을 수행하며 시각적 표현을 만드는 데 사용할 수 있는 여러 Azure 서비스를 지원합니다. 지원되는 Azure 서비스 목록은 [Azure Data Lake Storage Gen2를 지원하는 Azure 서비스를](data-lake-storage-supported-azure-services.md)참조하십시오.

[Azure 데이터 레이크 저장소 Gen2를 지원하는 Azure 서비스를](data-lake-storage-supported-azure-services.md)참조하십시오.

## <a name="supported-open-source-platforms"></a>지원되는 오픈 소스 플랫폼

Data Lake Storage Gen2는 몇 가지 오픈 소스 플랫폼에서 지원합니다. 전체 목록은 Azure [Data Lake Storage Gen2를 지원하는 오픈 소스 플랫폼을](data-lake-storage-supported-open-source-platforms.md)참조하십시오.

[Azure 데이터 레이크 저장소 Gen2를 지원하는 오픈 소스 플랫폼을](data-lake-storage-supported-open-source-platforms.md)참조하십시오.

## <a name="blob-storage-apis"></a>Blob Storage API

Blob API 및 데이터 레이크 스토리지 Gen2 API는 동일한 데이터에서 작동할 수 있습니다.

이 섹션에서는 Blob API 및 데이터 레이크 스토리지 Gen2 API를 사용하여 동일한 데이터에서 작동할 때의 문제와 제한 사항에 대해 설명합니다.

* Blob API와 데이터 레이크 저장소 API를 모두 사용하여 동일한 파일 인스턴스에 쓸 수 없습니다. Data Lake Storage Gen2 API를 사용하여 파일에 쓰는 경우 해당 파일의 블록은 [블록 목록](https://docs.microsoft.com/rest/api/storageservices/get-block-list) Blob API 를 호출하는 데 표시되지 않습니다. 데이터 레이크 스토리지 Gen2 API 또는 Blob API를 사용하여 파일을 덮어쓸 수 있습니다. 파일 속성에는 영향을 주지 않습니다.

* 구분 기호를 지정하지 않고 [Blobs 목록](https://docs.microsoft.com/rest/api/storageservices/list-blobs) 작업을 사용하면 결과에 디렉터리와 Blob이 모두 포함됩니다. 구분 기호를 사용하도록 선택한 경우 정방향 슬래시()만`/`사용합니다. 이것은 유일한 지원되는 구분 기호입니다.

* [Blob 삭제](https://docs.microsoft.com/rest/api/storageservices/delete-blob) API를 사용하여 디렉터리를 삭제하면 해당 디렉터리가 비어 있는 경우에만 삭제됩니다. 즉, Blob API 삭제 디렉터리를 재귀적으로 사용할 수 없습니다.

이러한 Blob REST API는 지원되지 않습니다.

* [Blob 넣기(페이지)](https://docs.microsoft.com/rest/api/storageservices/put-blob)
* [페이지 배치](https://docs.microsoft.com/rest/api/storageservices/put-page)
* [페이지 범위 가져오기](https://docs.microsoft.com/rest/api/storageservices/get-page-ranges)
* [증분 복사 Blob](https://docs.microsoft.com/rest/api/storageservices/incremental-copy-blob)
* [URL에서 페이지 넣기](https://docs.microsoft.com/rest/api/storageservices/put-page-from-url)
* [Blob 넣기(부가 하기)](https://docs.microsoft.com/rest/api/storageservices/put-blob)
* [추가 블록](https://docs.microsoft.com/rest/api/storageservices/append-block)
* [URL에서 차단 을 부화](https://docs.microsoft.com/rest/api/storageservices/append-block-from-url)

관리되지 않는 VM 디스크는 계층적 네임스페이스가 있는 계정에서는 지원되지 않습니다. 저장소 계정에 계층적 네임스페이스를 사용하도록 설정하려면 관리되지 않는 VM 디스크를 계층적 네임스페이스 기능이 활성화되어 있지 않은 저장소 계정에 배치합니다.

<a id="api-scope-data-lake-client-library" />

## <a name="file-system-support-in-sdks-powershell-and-azure-cli"></a>SDK, PowerShell 및 Azure CLI의 파일 시스템 지원

- Get 및 set ACL 작업은 현재 재귀적이지 않습니다.
- [Azure CLI](data-lake-storage-directory-file-acl-cli.md) 지원은 공개 미리 보기입니다.


## <a name="lifecycle-management-policies"></a>수명 주기 관리 정책

* Blob 스냅숏 삭제는 아직 지원되지 않습니다.  

## <a name="archive-tier"></a>아카이브 계층

현재 아카이브 액세스 계층에 영향을 주는 버그가 있습니다.


## <a name="blobfuse"></a>Blobfuse

Blobfuse는 지원되지 않습니다.

<a id="known-issues-tools" />

## <a name="azcopy"></a>AzCopy

AzCopy[(AzCopy v10)의](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10?toc=%2fazure%2fstorage%2ftables%2ftoc.json)최신 버전만 사용합니다.AzCopy v8.1과 같은 이전 버전의 AzCopy는 지원되지 않습니다.

<a id="storage-explorer" />

## <a name="azure-storage-explorer"></a>Azure Storage Explorer

버전 이상만 `1.6.0` 사용합니다.

<a id="explorer-in-portal" />

## <a name="storage-explorer-in-the-azure-portal"></a>Azure 포털의 저장소 탐색기

ACL은 아직 지원되지 않습니다.

<a id="third-party-apps" />

## <a name="thirdpartyapplications"></a>타사 응용 프로그램

REST API를 사용하여 작동하는 타사 응용 프로그램은 Blob API를 호출하는 Data Lake Storage Gen2 응용 프로그램과 함께 사용하는 경우 계속 작동합니다.

## <a name="access-control-lists-acl-and-anonymous-read-access"></a>액세스 제어 목록(ACL) 및 익명 읽기 액세스

컨테이너에 [익명 읽기 액세스가](storage-manage-access-to-resources.md) 부여된 경우 ACL은 해당 컨테이너 또는 해당 컨테이너의 파일에 영향을 주지 않습니다.

## <a name="windows-azure-storage-blob-wasb-driver-unsupported-with-data-lake-storage-gen2"></a>Windows Azure 저장소 Blob(WASB) 드라이버(데이터 레이크 저장소 Gen2에서 지원되지 않음)

현재 Blob API에서만 작동하도록 설계된 WASB 드라이버는 몇 가지 일반적인 시나리오에서 문제가 발생합니다. 특히 계층적 네임스페이스 지원 저장소 계정의 클라이언트인 경우 Data Lake Storage의 다중 프로토콜 액세스는 이러한 문제를 완화하지 않습니다. 

당분간은 (그리고 대부분 가까운 미래에) WASB 드라이버를 계층적 네임스페이스 지원 저장소 계정에 클라이언트로 사용하는 고객을 지원하지 않습니다. 대신 Hadoop 환경에서 Azure [Blob 파일 시스템(ABFS)](data-lake-storage-abfs-driver.md) 드라이버를 사용하도록 선택하는 것이 좋습니다. Hadoop branch-3보다 이전 버전이 있는 온프레미스 Hadoop 환경에서 마이그레이션하려는 경우 사용자 및 조직에 적합한 경로에서 연락을 드릴 수 있도록 Azure 지원 티켓을 열어 주십시오.
