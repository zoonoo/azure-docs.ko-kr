---
title: Azure Data Lake Storage Gen2에서 알려진 문제 | Microsoft Docs
description: Azure Data Lake Storage Gen2의 제한 사항 및 알려진 문제에 대해 알아봅니다.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 02/25/2020
ms.author: normesta
ms.reviewer: jamesbak
ms.openlocfilehash: 7fd76be8d17dc1c632e555a56d038d4f5c1e1486
ms.sourcegitcommit: 05b36f7e0e4ba1a821bacce53a1e3df7e510c53a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/06/2020
ms.locfileid: "78668604"
---
# <a name="known-issues-with-azure-data-lake-storage-gen2"></a>Azure Data Lake Storage Gen2에서 알려진 문제

이 문서에서는 Azure Data Lake Storage Gen2의 제한 사항 및 알려진 문제에 대해 설명 합니다.

## <a name="supported-blob-storage-features"></a>지원 되는 Blob storage 기능

이제 많은 Blob 저장소 기능이 계층적 네임 스페이스를 포함 하는 계정으로 작동 합니다. 전체 목록은 [Azure Data Lake Storage Gen2에서 사용할 수 있는 Blob Storage 기능](data-lake-storage-supported-blob-storage-features.md)을 참조 하세요.

## <a name="supported-azure-service-integrations"></a>지원 되는 Azure 서비스 통합

Data Lake Storage gen2는 데이터를 수집하고, 분석을 수행하고, 시각적 표현을 만드는 데 사용할 수 있는 여러 Azure 서비스를 지원합니다. 지원 되는 Azure 서비스 목록은 [Azure Data Lake Storage Gen2를 지 원하는 azure 서비스](data-lake-storage-supported-azure-services.md)를 참조 하세요.

[Azure Data Lake Storage Gen2를 지 원하는 Azure 서비스를](data-lake-storage-supported-azure-services.md)참조 하세요.

## <a name="supported-open-source-platforms"></a>지원되는 오픈 소스 플랫폼

Data Lake Storage Gen2는 몇 가지 오픈 소스 플랫폼에서 지원합니다. 전체 목록은 [Azure Data Lake Storage Gen2를 지 원하는 오픈 소스 플랫폼](data-lake-storage-supported-open-source-platforms.md)을 참조 하세요.

[Azure Data Lake Storage Gen2를 지 원하는 오픈 소스 플랫폼을](data-lake-storage-supported-open-source-platforms.md)참조 하세요.

## <a name="blob-storage-apis"></a>Blob Storage API

Blob Api 및 Data Lake Storage Gen2 Api는 동일한 데이터에 대해 작동할 수 있습니다.

이 섹션에서는 동일한 데이터에 대해 작동 하는 blob Api 및 Data Lake Storage Gen2 Api를 사용 하는 경우의 문제 및 제한 사항에 대해 설명 합니다.

* Blob Api와 Data Lake Storage Api를 모두 사용 하 여 동일한 파일 인스턴스에 쓸 수는 없습니다. Data Lake Storage Gen2 Api를 사용 하 여 파일에 쓰는 경우 해당 파일의 블록이 [Get Block List](https://docs.microsoft.com/rest/api/storageservices/get-block-list) blob API에 대 한 호출에 표시 되지 않습니다. Data Lake Storage Gen2 Api 또는 Blob Api를 사용 하 여 파일을 덮어쓸 수 있습니다. 이는 파일 속성에 영향을 주지 않습니다.

* 구분 기호를 지정 하지 않고 [Blob 나열](https://docs.microsoft.com/rest/api/storageservices/list-blobs) 작업을 사용 하는 경우 결과에는 디렉터리와 blob이 모두 포함 됩니다. 구분 기호를 사용 하도록 선택 하는 경우 슬래시 (`/`)만 사용 합니다. 유일 하 게 지원 되는 구분 기호입니다.

* [Blob 삭제](https://docs.microsoft.com/rest/api/storageservices/delete-blob) API를 사용 하 여 디렉터리를 삭제 하는 경우 해당 디렉터리는 비어 있는 경우에만 삭제 됩니다. 즉, Blob API delete 디렉터리를 재귀적으로 사용할 수 없습니다.

이러한 Blob REST Api는 지원 되지 않습니다.

* [Blob 배치 (페이지)](https://docs.microsoft.com/rest/api/storageservices/put-blob)
* [페이지 배치](https://docs.microsoft.com/rest/api/storageservices/put-page)
* [페이지 범위 가져오기](https://docs.microsoft.com/rest/api/storageservices/get-page-ranges)
* [Blob 증분 복사](https://docs.microsoft.com/rest/api/storageservices/incremental-copy-blob)
* [URL에서 페이지 배치](https://docs.microsoft.com/rest/api/storageservices/put-page-from-url)
* [Blob 배치 (추가)](https://docs.microsoft.com/rest/api/storageservices/put-blob)
* [추가 블록](https://docs.microsoft.com/rest/api/storageservices/append-block)
* [URL의 블록 추가](https://docs.microsoft.com/rest/api/storageservices/append-block-from-url)

계층 네임 스페이스가 있는 계정에서는 관리 되지 않는 VM 디스크가 지원 되지 않습니다. 저장소 계정에서 계층적 네임 스페이스를 사용 하도록 설정 하려면 계층 구조 네임 스페이스 기능이 사용 하도록 설정 되지 않은 저장소 계정에 관리 되지 않는 VM 디스크를 저장 합니다.

<a id="api-scope-data-lake-client-library" />

## <a name="file-system-support-in-sdks"></a>Sdk에서 파일 시스템 지원

- [.Net](data-lake-storage-directory-file-acl-dotnet.md), [Java](data-lake-storage-directory-file-acl-java.md) , [Python](data-lake-storage-directory-file-acl-python.md)및 [JavaScript](data-lake-storage-directory-file-acl-javascript.md) 는 공개 미리 보기로 제공 됩니다. 다른 Sdk는 현재 지원 되지 않습니다.
- Get 및 set ACL 작업은 현재 재귀적이 아닙니다.

## <a name="file-system-support-in-powershell-and-azure-cli"></a>PowerShell 및 Azure CLI에서 파일 시스템 지원

- [PowerShell](data-lake-storage-directory-file-acl-powershell.md) 및 [Azure CLI](data-lake-storage-directory-file-acl-cli.md) 지원은 공개 미리 보기로 제공 됩니다.
- Get 및 set ACL 작업은 현재 재귀적이 아닙니다.

## <a name="lifecycle-management-policies"></a>수명 주기 관리 정책

* Blob 스냅숏의 삭제는 아직 지원 되지 않습니다.  

* 현재 수명 주기 관리 정책 및 보관 액세스 계층에 영향을 주는 몇 가지 버그가 있습니다. 

## <a name="diagnostic-logs"></a>진단 로그

Azure Storage 탐색기 1.10. x를 사용 하 여 진단 로그를 볼 수 없습니다. 로그를 보려면 AzCopy 또는 Sdk를 사용 하세요.

## <a name="blobfuse"></a>Blobfuse

Blobfuse는 지원 되지 않습니다.



<a id="known-issues-tools" />

## <a name="azcopy"></a>AzCopy

최신 버전의 AzCopy ([AzCopy v10](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10?toc=%2fazure%2fstorage%2ftables%2ftoc.json))만 사용 합니다. AzCopy v 8.1과 같은 이전 버전의 AzCopy는 지원 되지 않습니다.

<a id="storage-explorer" />

## <a name="azure-storage-explorer"></a>Azure Storage Explorer

버전 `1.6.0` 이상만 사용 합니다. 현재 특정 시나리오에서 인증 오류가 발생할 수 있는 버전 `1.11.0` 에 영향을 주는 저장소 버그가 있습니다. 저장소 버그에 대 한 수정 사항이 롤아웃 되 고 있습니다 .이 문제를 해결 하려면 [무료로 다운로드할](https://docs.microsoft.com/azure/vs-azure-tools-storage-explorer-relnotes)수 있는 버전 `1.10.x` 를 사용 하는 것이 좋습니다. `1.10.x` 는 저장소 버그의 영향을 받지 않습니다.

<a id="explorer-in-portal" />

## <a name="storage-explorer-in-the-azure-portal"></a>Azure Portal Storage 탐색기

Acl은 아직 지원 되지 않습니다.

<a id="third-party-apps" />

## <a name="thirdpartyapplications"></a>타사 응용 프로그램

REST Api를 사용 하 여 작동 하는 타사 응용 프로그램은 Blob Api를 호출 하는 응용 프로그램 Data Lake Storage Gen2 사용할 경우 계속 작동 합니다.

## <a name="access-control-lists-acl-and-anonymous-read-access"></a>ACL (액세스 제어 목록) 및 익명 읽기 액세스

컨테이너에 [익명 읽기 액세스](storage-manage-access-to-resources.md) 권한이 부여 된 경우 acl은 해당 컨테이너 또는 해당 컨테이너의 파일에 영향을 주지 않습니다.

## <a name="windows-azure-storage-blob-wasb-driver"></a>Windows Azure Storage Blob (WASB) 드라이버

현재는 계층 네임 스페이스가 있는 계정과 함께 WASB 드라이버를 사용 하는 것과 관련 된 몇 가지 문제가 있습니다. 워크 로드에서 [Azure Blob 파일 시스템 (ABFS)](data-lake-storage-abfs-driver.md) 드라이버를 사용 하는 것이 좋습니다. 





