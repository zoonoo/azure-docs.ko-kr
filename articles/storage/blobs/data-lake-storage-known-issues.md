---
title: Azure Data Lake Storage Gen2에서 알려진 문제 | Microsoft Docs
description: Azure Data Lake Storage Gen2의 제한 사항 및 알려진 문제에 대해 알아봅니다.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 05/10/2020
ms.author: normesta
ms.reviewer: jamesbak
ms.openlocfilehash: e80d1a05765d224dc4682c6f64faccc8c81f8ebd
ms.sourcegitcommit: 801a551e047e933e5e844ea4e735d044d170d99a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/11/2020
ms.locfileid: "83007469"
---
# <a name="known-issues-with-azure-data-lake-storage-gen2"></a>Azure Data Lake Storage Gen2에서 알려진 문제

이 문서에서는 Azure Data Lake Storage Gen2의 제한 사항 및 알려진 문제에 대해 설명 합니다.

## <a name="supported-blob-storage-features"></a>지원되는 Blob 스토리지 기능

이제 많은 Blob 저장소 기능이 계층적 네임 스페이스를 포함 하는 계정으로 작동 합니다. 전체 목록은 [Azure Data Lake Storage Gen2에서 사용할 수 있는 Blob Storage 기능](data-lake-storage-supported-blob-storage-features.md)을 참조 하세요.

## <a name="supported-azure-service-integrations"></a>지원 되는 Azure 서비스 통합

Azure Data Lake Storage Gen2는 데이터를 수집 하 고 분석을 수행 하 고 시각적 표현을 만드는 데 사용할 수 있는 여러 Azure 서비스를 지원 합니다. 지원 되는 Azure 서비스 목록은 [Azure Data Lake Storage Gen2를 지 원하는 azure 서비스](data-lake-storage-supported-azure-services.md)를 참조 하세요.

[Azure Data Lake Storage Gen2를 지 원하는 Azure 서비스를](data-lake-storage-supported-azure-services.md)참조 하세요.

## <a name="supported-open-source-platforms"></a>지원되는 오픈 소스 플랫폼

Data Lake Storage Gen2는 몇 가지 오픈 소스 플랫폼에서 지원합니다. 전체 목록은 [Azure Data Lake Storage Gen2를 지 원하는 오픈 소스 플랫폼](data-lake-storage-supported-open-source-platforms.md)을 참조 하세요.

[Azure Data Lake Storage Gen2를 지 원하는 오픈 소스 플랫폼을](data-lake-storage-supported-open-source-platforms.md)참조 하세요.

## <a name="blob-storage-apis"></a>Blob Storage API

Blob Api 및 Data Lake Storage Gen2 Api는 동일한 데이터에 대해 작동할 수 있습니다.

이 섹션에서는 동일한 데이터에 대해 작동 하는 blob Api 및 Data Lake Storage Gen2 Api를 사용 하는 경우의 문제 및 제한 사항에 대해 설명 합니다.

* Blob Api와 Data Lake Storage Api를 모두 사용 하 여 동일한 파일 인스턴스에 쓸 수는 없습니다. Data Lake Storage Gen2 Api를 사용 하 여 파일에 쓰는 경우 해당 파일의 블록이 [Get Block List](https://docs.microsoft.com/rest/api/storageservices/get-block-list) blob API에 대 한 호출에 표시 되지 않습니다. Data Lake Storage Gen2 Api 또는 Blob Api를 사용 하 여 파일을 덮어쓸 수 있습니다. 이는 파일 속성에 영향을 주지 않습니다.

* 구분 기호를 지정 하지 않고 [Blob 나열](https://docs.microsoft.com/rest/api/storageservices/list-blobs) 작업을 사용 하는 경우 결과에는 디렉터리와 blob이 모두 포함 됩니다. 구분 기호를 사용 하도록 선택 하는 경우 슬래시 ()만 사용 `/` 합니다. 유일 하 게 지원 되는 구분 기호입니다.

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

## <a name="file-system-support-in-sdks-powershell-and-azure-cli"></a>Sdk, PowerShell 및 Azure CLI에서 파일 시스템 지원

- Get 및 set ACL 작업은 현재 재귀적이 아닙니다.
- [Azure CLI](data-lake-storage-directory-file-acl-cli.md) 지원은 공개 미리 보기로 제공 됩니다.


## <a name="lifecycle-management-policies"></a>수명 주기 관리 정책

Blob 스냅숏의 삭제는 아직 지원 되지 않습니다. 

## <a name="archive-tier"></a>보관 계층

현재 보관 액세스 계층에 영향을 주는 버그가 있습니다.

## <a name="blobfuse"></a>Blobfuse

Blobfuse는 지원 되지 않습니다.

<a id="known-issues-tools" />

## <a name="azcopy"></a>AzCopy

최신 버전의 AzCopy ([AzCopy v10](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10?toc=%2fazure%2fstorage%2ftables%2ftoc.json))만 사용 합니다.AzCopy v 8.1과 같은 이전 버전의 AzCopy는 지원 되지 않습니다.

<a id="storage-explorer" />

## <a name="azure-storage-explorer"></a>Azure Storage Explorer

이상 버전만 사용  `1.6.0`   합니다.

<a id="explorer-in-portal" />

## <a name="storage-explorer-in-the-azure-portal"></a>Azure Portal Storage 탐색기

Acl은 아직 지원 되지 않습니다.

<a id="third-party-apps" />

## <a name="thirdpartyapplications"></a>타사 응용 프로그램

REST Api를 사용 하 여 작동 하는 타사 응용 프로그램은 Blob Api를 호출 하는 응용 프로그램 Data Lake Storage Gen2 사용할 경우 계속 작동 합니다.

## <a name="access-control-lists-acl-and-anonymous-read-access"></a>ACL (액세스 제어 목록) 및 익명 읽기 액세스

컨테이너에 [익명 읽기 액세스](storage-manage-access-to-resources.md) 권한이 부여 된 경우 acl은 해당 컨테이너 또는 해당 컨테이너의 파일에 영향을 주지 않습니다.

## <a name="premium-performance-block-blob-storage-accounts"></a>프리미엄-성능 블록 blob storage 계정

### <a name="diagnostic-logs"></a>진단 로그

Azure Portal를 사용 하 여 진단 로그를 아직 사용할 수 없습니다. PowerShell을 사용 하 여 사용 하도록 설정할 수 있습니다. 다음은 그 예입니다. 

```powershell
#To login
Connect-AzAccount

#Set default block blob storage account.
Set-AzCurrentStorageAccount -Name premiumGen2Account -ResourceGroupName PremiumGen2Group

#Enable logging
Set-AzStorageServiceLoggingProperty -ServiceType Blob -LoggingOperations read,write,delete -RetentionDays 14
```

### <a name="lifecycle-management-policies"></a>수명 주기 관리 정책

- 수명 주기 관리 정책은 프리미엄 블록 blob storage 계정에서 아직 지원 되지 않습니다. 

- 데이터를 프리미엄 계층에서 하위 계층으로 이동할 수 없습니다. 

- **Blob 삭제** 작업은 현재 지원 되지 않습니다. 

### <a name="hdinsight-support"></a>HDInsight 지원

N HDInsight 클러스터를 만들 때 계층 구조 네임 스페이스 기능이 사용 하도록 설정 된 블록 blob storage 계정을 아직 선택할 수 없습니다. 그러나 계정을 만든 후에는 해당 계정을 클러스터에 연결할 수 있습니다.

### <a name="dremio-support"></a>Dremio 지원

Dremio는 계층 구조 네임 스페이스 기능이 활성화 된 블록 blob 저장소 계정에 아직 연결 되지 않습니다. 

## <a name="windows-azure-storage-blob-wasb-driver-unsupported-with-data-lake-storage-gen2"></a>Windows Azure Storage Blob (WASB) 드라이버 (Data Lake Storage Gen2에서 지원 되지 않음)

현재 Blob API 에서만 작동 하도록 설계 된 WASB 드라이버는 몇 가지 일반적인 시나리오에서 문제가 발생 합니다. 특히 계층적 네임 스페이스 사용 저장소 계정에 대 한 클라이언트 인 경우 Data Lake Storage에 대 한 다중 프로토콜 액세스는 이러한 문제를 완화 하지 않습니다. 

시간 (및 예측 가능한 미래)의 경우 WASB 드라이버를 계층적 네임 스페이스 사용 저장소 계정의 클라이언트로 사용 하는 고객을 지원 하지 않습니다. 대신 Hadoop 환경에서 [Azure Blob 파일 시스템 (ABFS)](data-lake-storage-abfs-driver.md) 드라이버를 사용 하도록 선택 하는 것이 좋습니다. Hadoop 분기-3 이전 버전을 사용 하 여 온-프레미스 Hadoop 환경에서 마이그레이션하려면 Azure 지원 티켓을 여세요. 그러면 사용자와 조직에 대 한 올바른 경로를 사용 하 여 연락을 받을 수 있습니다.
