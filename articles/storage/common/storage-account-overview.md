---
title: Storage 계정 개요
titleSuffix: Azure Storage
description: Azure Storage의 다양한 스토리지 계정 유형에 대해 알아봅니다. 계정 이름, 성능 계층, 액세스 계층, 중복, 암호화, 엔드포인트 등을 살펴봅니다.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 04/02/2021
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: d7eca7d8f3cd40f4a3961f0ac478fba290be3041
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/03/2021
ms.locfileid: "106279666"
---
# <a name="storage-account-overview"></a>Storage 계정 개요

Azure Storage 계정에는 Blob, 파일, 큐, 테이블, 디스크 등, 모든 Azure Storage 데이터 개체가 포함됩니다. 스토리지 계정은 Azure Storage 데이터에 고유한 네임스페이스를 제공하여 전 세계 어디서나 HTTP 또는 HTTPS를 통해 액세스할 수 있도록 합니다. Azure Storage 계정의 데이터는 지속적이고 가용성이 높으며 안전하고 대규모로 확장 가능합니다.

Azure Storage 계정을 만드는 방법은 [스토리지 계정 만들기](storage-account-create.md)를 참조하세요.

## <a name="types-of-storage-accounts"></a>스토리지 계정 유형

Azure Storage는 여러 유형의 스토리지 계정을 제공합니다. 각각의 유형은 서로 다른 기능을 지원하며 고유의 가격 책정 모델이 있습니다. 스토리지 계정을 만들기 전에 애플리케이션에 적합한 계정 유형을 결정하는 데 이러한 차이점을 고려합니다. 스토리지 계정 유형은 다음과 같습니다.

- **범용 v2 계정**: Blob, 파일, 큐 및 테이블에 대한 기본 스토리지 계정 유형입니다. 대부분의 시나리오에 대해 Azure Storage를 사용하는 것이 좋습니다.
- **범용 v1 계정**: Blob, 파일, 큐 및 테이블에 대한 레거시 계정 유형입니다. 가능한 경우 범용 v2 계정을 대신 사용합니다.
- **BlockBlobStorage 계정**: 블록 Blob 및 추가 Blob에 대한 프리미엄 성능 특성을 갖춘 스토리지 계정입니다. 트랜잭션 속도가 높은 시나리오 또는 더 작은 개체를 사용하거나 지속적으로 짧은 스토리지 대기 시간이 필요한 시나리오에 추천됩니다.
- **FileStorage 계정**: 프리미엄 성능 특성을 갖춘 파일 전용 스토리지 계정입니다. 엔터프라이즈 또는 고성능 규모의 애플리케이션에 추천됩니다.
- **BlobStorage 계정**: 레거시 Blob 전용 스토리지 계정입니다. 가능한 경우 범용 v2 계정을 대신 사용합니다.

다음 표에서는 스토리지 계정 유형, 지원되는 서비스 및 각 계정 유형에 지원되는 배포 모델에 대해 설명합니다.

| 스토리지 계정 유형 | 지원되는 서비스 | 중복 옵션 | 배포 모델<sup>1</sup> |
|--|--|--|--|
| 범용 V2 | Blob, File, Queue, Table, Disk 및 Data Lake Gen2<sup>2</sup> | LRS, GRS, RA-GRS, ZRS, GZRS, RA-GZRS<sup>3</sup> | 리소스 관리자 |
| 범용 V1 | Blob, 파일, 큐, 테이블 및 디스크 | LRS, GRS, RA-GRS | Resource Manager, 클래식 |
| BlockBlobStorage | Blob(블록 Blob 및 추가 Blob만) | LRS, ZRS<sup>3</sup> | 리소스 관리자 |
| FileStorage | 파일에만 해당 | LRS, ZRS<sup>3</sup> | 리소스 관리자 |
| BlobStorage | Blob(블록 Blob 및 추가 Blob만) | LRS, GRS, RA-GRS | 리소스 관리자 |

<sup>1</sup>Azure Resource Manager 배포 모델을 사용하는 것이 좋습니다. 클래식 배포 모델을 사용하는 스토리지 계정도 일부 위치에서는 계속 만들 수 있고 기존 클래식 계정도 계속 지원됩니다. 자세한 내용은 [Azure Resource Manager 및 클래식 배포: 배포 모델 및 리소스 상태 이해](../../azure-resource-manager/management/deployment-models.md)를 참조하세요.

<sup>2</sup>Azure Data Lake Storage Gen2는 Azure Blob Storage를 기반으로 하는 빅 데이터 분석 전용 기능 집합입니다. Data Lake Storage Gen2는 계층 구조 네임스페이스를 사용하도록 설정된 범용 V2 스토리지 계정에서만 지원됩니다. Data Lake Storage Gen2에 대한 자세한 내용은 [Azure Data Lake Storage Gen2 소개](../blobs/data-lake-storage-introduction.md)를 참조하세요.

<sup>3</sup>ZRS(영역 중복 스토리지) 및 GZRS/RA-GZRS(지리적 영역 중복 스토리지)는 특정 지역의 표준 범용 V2, BlockBlobStorage 및 FileStorage 계정에 대해서만 사용할 수 있습니다. Azure Storage 중복 옵션에 대한 자세한 내용은 [Azure Storage 중복](storage-redundancy.md)을 참조하세요.

### <a name="storage-account-redundancy"></a>스토리지 계정 중복

스토리지 계정의 중복 옵션으로는 다음과 같은 것이 있습니다.

- **LRS(로컬 중복 스토리지)** : 간단하고 저렴한 중복 전략입니다. 데이터는 주 지역의 단일 물리적 위치 내에서 동기적으로 세 번 복사됩니다.
- **ZRS(영역 중복 스토리지)** : 고가용성이 필요한 시나리오를 위한 중복입니다. 데이터는 주 지역에 있는 3개의 Azure 가용성 영역에서 동기적으로 복사됩니다.
- **GRS(지역 중복 스토리지)** : 지역 간 중복을 통해 지역 중단으로부터 보호합니다. 데이터는 주 지역에서 동기적으로 세 번 복사된 다음, 보조 지역에 비동기적으로 복사됩니다. 보조 지역에 대한 읽기 액세스의 경우 RA-GRS(읽기 액세스 지역 중복 스토리지)를 사용합니다.
- **GZRS(지역 영역 중복 스토리지)** : 고가용성 및 최대 내구성이 모두 필요한 시나리오를 위한 중복입니다. 데이터는 주 지역의 3개의 Azure 가용성 영역에서 동기적으로 복사된 다음, 보조 지역에 비동기적으로 복사됩니다. 보조 지역에 대한 읽기 액세스의 경우 RA-GZRS(읽기 액세스 지역 영역 중복 스토리지)를 사용합니다.

Azure Storage의 중복 옵션에 대한 자세한 내용은 [Azure Storage 중복성](storage-redundancy.md)을 참조하세요.

### <a name="general-purpose-v2-accounts"></a>범용 v2 계정

범용 v2 스토리지 계정은 최신 Azure Storage 기능을 지원하고 모든 범용 v1의 기능 및 Blob Storage 계정을 통합합니다. 범용 v2 계정은 업계 경쟁력 있는 트랜잭션 가격 뿐만 아니라 Azure Storage에 대해서도 가장 낮은 기가바이트당 용량 가격을 제공합니다. 범용 v2 스토리지 계정은 다음 Azure Storage 서비스를 지원합니다.

- Blob(모든 유형: 블록, 추가, 페이지)
- Data Lake Gen2
- 파일
- 디스크
- 큐
- 테이블

> [!NOTE]
> 대부분의 시나리오에서 범용 v2 스토리지 계정을 사용하는 것이 좋습니다. 가동 중지 시간이 없고 데이터를 복사할 필요 없이 범용 v1 또는 Blob Storage 계정을 범용 v2 계정으로 쉽게 업그레이드할 수 있습니다. 그러나 업그레이드는 실행 취소할 수 없습니다.
>
> 범용 v2 계정으로의 업그레이드에 대한 자세한 내용은 [범용 v2 스토리지 계정으로 업그레이드](storage-account-upgrade.md)를 참조하세요.

범용 v2 스토리지 계정은 사용 패턴을 기준으로 데이터 저장을 위한 여러 액세스 계층을 제공합니다. 자세한 내용은 [블록 Blob 데이터에 대한 액세스 계층](#access-tiers-for-block-blob-data)을 참조하세요.

### <a name="general-purpose-v1-accounts"></a>범용 v1 계정

범용 v1 스토리지 계정은 모든 Azure Storage 서비스에 대한 액세스를 제공하지만 최신 기능 또는 기가바이트당 가장 낮은 가격 책정이 포함되지 않을 수 있습니다. 범용 v1 스토리지 계정은 다음 Azure Storage 서비스를 지원합니다.

- Blob(모든 유형)
- 파일
- 디스크
- 큐
- 테이블

이러한 시나리오에는 범용 v1 계정을 사용할 수 있습니다.

- 애플리케이션에 Azure 클래식 배포 모델이 필요합니다. 범용 v2 계정 및 Blob Storage 계정은 Azure Resource Manager 배포 모델만 지원합니다.

- 애플리케이션은 트랜잭션 집약적이거나 상당한 지역 복제 대역폭을 사용하지만 대용량이 필요하지는 않습니다. 이 경우 범용 v1이 가장 경제적인 선택이 될 수 있습니다.

- [Storage 서비스 REST API](/rest/api/storageservices/Versioning-for-the-Azure-Storage-Services) 2014-02-14 이전 버전이나 클라이언트 라이브러리 4.x 미만 버전을 사용합니다. 애플리케이션을 업그레이드할 수 없습니다.

> [!NOTE]
> Microsoft는 대부분의 시나리오에 범용 v2 계정을 권장하지만 Microsoft는 신규 및 기존 고객에게 범용 v1 계정을 계속 지원합니다. 새 지역에서 Azure Storage를 사용할 수 있을 때마다 이러한 지역에서 범용 v1 스토리지 계정을 만들 수 있습니다. Microsoft는 현재 범용 v1 계정에 대한 지원을 중단할 계획이 없으며, Azure Storage 기능의 사용을 중단하기 적어도 1년 전에 사전 고지를 제공할 것입니다. Microsoft는 범용 v1 계정에 대한 보안 업데이트를 계속 제공할 예정이지만 이 계정 유형에 대한 새로운 기능 개발은 예정에 없습니다.
>
> 2020년 10월 1일부터, 새 Azure Storage 지역의 범용 v1 계정 가격 책정 방식은 해당 지역의 범용 v2 계정 가격 책정 방식과 동일합니다. 기존 Azure Storage 지역의 가격은 변경되지 않았습니다. 특정 지역에서 범용 v1 계정의 가격 책정 정보는 Azure Storage 가격 책정 페이지를 참조하세요. 지역을 선택한 후 **가격 옵션** 옆에 있는 **기타** 를 선택합니다.

### <a name="blockblobstorage-accounts"></a>BlockBlobStorage 계정

BlockBlobStorage 계정은 구조화되지 않은 개체 데이터를 블록 Blob 또는 추가 Blob으로 저장하기 위한 프리미엄 성능 계층의 특수 스토리지 계정입니다. BlockBlobStorage 계정은 범용 v2 및 BlobStorage 계정보다 낮고 일관된 대기 시간 및 더 높은 트랜잭션 속도를 제공합니다.

BlockBlobStorage 계정은 현재 핫, 쿨 또는 보관 액세스 계층에 대한 계층화를 지원하지 않습니다. 이 유형의 스토리지 계정은 페이지 Blob, 테이블 또는 큐를 지원하지 않습니다.

### <a name="filestorage-accounts"></a>FileStorage 계정

FileStorage 계정은 프리미엄 파일 공유를 저장하고 만드는 데 사용되는 특수 스토리지 계정입니다. 이 스토리지 계정 유형은 파일을 지원하지만 블록 Blob, 추가 Blob, 페이지 Blob, 테이블 또는 큐를 지원하지 않습니다.

FileStorage 계정은 IOPS 버스트와 같은 고유한 성능 전용 특성을 제공합니다. 이러한 특성에 대한 자세한 내용은 파일 계획 가이드의 [파일 공유 스토리지 계층](../files/storage-files-planning.md#storage-tiers) 섹션을 참조하세요.

## <a name="naming-storage-accounts"></a>스토리지 계정 이름 지정

스토리지 계정의 이름을 지정할 때는 다음 규칙에 유의하세요.

- Storage 계정 이름은 3자에서 24자 사이여야 하고 숫자 및 소문자만 포함할 수 있습니다.
- 스토리지 계정 이름은 Azure 내에서 고유해야 합니다. 두 개의 스토리지 계정이 같은 이름을 사용할 수 없습니다.

## <a name="performance-tiers"></a>성능 계층

만드는 스토리지 계정 유형에 따라 표준 및 프리미엄 성능 계층 중에서 선택할 수 있습니다. 다음 표에는 스토리지 계정 유형에 사용할 수 있는 성능 계층이 요약되어 있습니다.

| 스토리지 계정 유형 | 지원되는 성능 계층 |
|--|--|
| 범용 V2 | 표준, 프리미엄<sup>1</sup> |
| 범용 V1 | 표준, 프리미엄<sup>1</sup> |
| BlockBlobStorage | Premium |
| FileStorage | Premium |
| BlobStorage | Standard |

<sup>1</sup>범용 v2 및 범용 v1 계정에 대한 프리미엄 성능은 디스크 및 페이지 Blob에만 사용할 수 있습니다. 블록 또는 추가 Blob에 대한 프리미엄 성능은 BlockBlobStorage 계정에서만 사용할 수 있습니다. 파일에 대한 프리미엄 성능은 FileStorage 계정에서만 사용할 수 있습니다.

### <a name="general-purpose-storage-accounts"></a>범용 스토리지 계정

다음 성능 계층 중 하나에 대해 범용 스토리지 계정을 구성할 수 있습니다.

- Blob, 파일, 테이블, 큐 및 Azure 가상 머신 디스크를 저장하기 위한 표준 성능 계층 표준 스토리지 계정의 확장성 목표에 대한 자세한 내용은 [표준 스토리지 계정의 확장성 목표](scalability-targets-standard-account.md)를 참조하세요.
- 비관리형 가상 머신 디스크를 저장하기 위한 프리미엄 성능 계층 Microsoft에서는 비관리 디스크 대신 관리 디스크를 Azure 가상 머신과 함께 사용할 것을 권장합니다. 프리미엄 성능 계층의 확장성 목표에 대한 자세한 내용은 [프리미엄 페이지 Blob Storage 계정의 확장성 목표](../blobs/scalability-targets-premium-page-blobs.md)를 참조하세요.

### <a name="blockblobstorage-storage-accounts"></a>BlockBlobStorage 스토리지 계정

BlockBlobStorage 스토리지 계정은 블록 Blob 및 추가 Blob을 저장하기 위한 프리미엄 성능 계층을 제공합니다. 자세한 내용은 [프리미엄 블록 Blob Storage 계정에 대한 확장성 목표](../blobs/scalability-targets-premium-block-blobs.md)를 참조하세요.

### <a name="filestorage-storage-accounts"></a>FileStorage 스토리지 계정

FileStorage 스토리지 계정은 Azure 파일 공유에 대한 프리미엄 성능 계층을 제공합니다. 자세한 내용은 [Azure Files 확장성 및 성능 목표](../files/storage-files-scale-targets.md)를 참조하세요.

## <a name="access-tiers-for-block-blob-data"></a>블록 Blob 데이터를 위한 액세스 계층

Azure Storage는 사용 패턴에 따라 블록 Blob 데이터 액세스를 위한 여러 가지 옵션을 제공합니다. Azure Storage의 액세스 계층은 특정 데이터 사용 패턴에 맞게 최적화됩니다. 필요에 따라 적합한 계층을 선택하면 가장 경제적인 방식으로 블록 Blob 데이터를 저장할 수 있습니다.

사용 가능한 액세스 계층은 다음과 같습니다.

- **핫** 액세스 계층. 이 액세스 계층은 스토리지 계층의 개체에 자주 액세스하는 데 최적화되어 있습니다. 핫 계층의 데이터 액세스는 가장 비용 효율적이지만, 스토리지 비용이 더 높습니다. 기본적으로 새 스토리지 계정은 핫 계층에 만들어집니다.
- **쿨** 액세스 계층. 이 계층은 자주 액세스하지 않고 최소 30일 동안 저장된 많은 양의 데이터를 저장하는 데 최적화되어 있습니다. 쿨 계층에 데이터를 저장하는 것은 상당히 비용 효율적이지만, 데이터 액세스 비용이 핫 계층의 데이터에 액세스하는 것보다 더 높을 수 있습니다.
- 보관 계층. 이 계층은 개별 블록 Blob에만 사용할 수 있습니다. 보관 계층은 몇 시간의 검색 대기 시간을 허용할 수 있고, 최소 180일 동안 보관 계층에 남아 있는 데이터에 맞게 최적화되어 있습니다. 보관 계층은 데이터를 저장하는 데 사용할 수 있는 가장 비용 효율적인 옵션입니다. 그러나 해당 데이터에 액세스하는 것은 핫 또는 쿨 계층의 데이터에 액세스하는 것보다 비용이 많이 듭니다.

데이터의 사용 패턴에 변화가 있으면 언제든 이 액세스 계층 간에 전환할 수 있습니다. 액세스 계층에 대한 자세한 내용은 [Azure Blob Storage: 핫, 쿨 및 보관 액세스 계층](../blobs/storage-blob-storage-tiers.md)을 참조하세요.

다음 표에서는 각 유형의 스토리지 계정에서 Blob에 사용할 수 있는 액세스 계층을 보여 줍니다.

| 스토리지 계정 유형 | 지원되는 액세스 계층 |
|--|--|
| 범용 V2 | 핫, 쿨, 보관<sup>1</sup> |
| 범용 V1 | 해당 없음 |
| BlockBlobStorage | 해당 없음 |
| FileStorage | 해당 없음 |
| BlobStorage | 핫, 쿨, 보관<sup>1</sup> |

<sup>1</sup> 보관 스토리지 및 Blob 수준 계층화는 블록 Blob만 지원합니다. 보관 계층은 스토리지 계정 수준이 아니라 개별 Blob 수준에서만 사용할 수 있습니다. 자세한 내용은 [Azure Blob Storage의 액세스 계층 - 핫, 쿨, 보관](../blobs/storage-blob-storage-tiers.md)을 참조하세요.

> [!IMPORTANT]
> 기존 스토리지 계정 또는 Blob에 대한 액세스 계층을 변경하면 추가 비용이 발생할 수 있습니다. 자세한 내용은 [스토리지 계정 청구](#storage-account-billing)를 참조하세요.

## <a name="storage-account-endpoints"></a>Storage 계정 엔드포인트

스토리지 계정은 데이터에 대해 Azure에서 고유의 네임스페이스를 제공합니다. Azure Storage 계정에 저장한 모든 개체는 고유 계정 이름을 포함하는 주소를 갖습니다. 계정 이름과 Azure Storage 서비스 엔드포인트의 조합이 스토리지 계정의 엔드포인트가 됩니다.

다음 표에는 각 Azure Storage 서비스의 엔드포인트가 나와 있습니다.

| 스토리지 서비스 | 엔드포인트 |
|--|--|
| Blob 스토리지 | `https://<storage-account>.blob.core.windows.net` |
| Azure Data Lake Storage Gen2 | `https://<storage-account>.dfs.core.windows.net` |
| Azure 파일 | `https://<storage-account>.file.core.windows.net` |
| Queue Storage | `https://<storage-account>.queue.core.windows.net` |
| Table Storage | `https://<storage-account>.table.core.windows.net` |

> [!NOTE]
> 블록 Blob 및 Blob Storage 계정은 Blob service 엔드포인트만 노출합니다.

스토리지 계정의 개체 위치를 엔드포인트에 추가하여 스토리지 계정의 개체에 액세스하기 위한 URL을 작성합니다. 예를 들어 Blob 주소의 형식은 다음과 같습니다. http://*mystorageaccount*.blob.core.windows.net/*mycontainer*/*myblob*

Blob에 사용자 지정 도메인 이름을 사용하도록 스토리지 계정을 구성할 수도 있습니다. 자세한 내용은 [Azure Storage 계정에 대한 사용자 지정 도메인 이름 구성](../blobs/storage-custom-domain-name.md)을 참조하세요.  

## <a name="migrating-a-storage-account"></a>스토리지 계정 마이그레이션

다음 표에서는 스토리지 계정 이동, 업그레이드 또는 마이그레이션에 대한 참고 자료를 요약하고 설명합니다.

| 마이그레이션 시나리오 | 세부 정보 |
|--|--|
| 스토리지 계정을 다른 구독으로 이동 | Azure Resource Manager는 리소스를 다른 구독으로 이동하는 옵션을 제공합니다. 자세한 내용은 [새 리소스 그룹 또는 구독으로 리소스 이동](../../azure-resource-manager/management/move-resource-group-and-subscription.md)을 참조하세요. |
| 스토리지 계정을 다른 리소스 그룹으로 이동 | Azure Resource Manager는 리소스를 다른 리소스 그룹으로 이동하는 옵션을 제공합니다. 자세한 내용은 [새 리소스 그룹 또는 구독으로 리소스 이동](../../azure-resource-manager/management/move-resource-group-and-subscription.md)을 참조하세요. |
| 스토리지 계정을 다른 지역으로 이동 | 스토리지 계정을 이동하려면 다른 지역에 스토리지 계정의 복사본을 만듭니다. 그런 다음, AzCopy 또는 원하는 다른 도구를 사용하여 해당 계정으로 데이터를 이동합니다. 자세한 내용은 [Azure Storage 계정을 다른 지역으로 이동](storage-account-move.md)을 참조하세요. |
| 범용 v2 스토리지 계정으로 업그레이드 | 범용 v1 스토리지 계정 또는 Blob Storage 계정을 범용 v2 계정으로 업그레이드할 수 있습니다. 이 동작은 실행 취소할 수 없습니다. 자세한 내용은 [범용 v2 스토리지 계정으로 업그레이드](storage-account-upgrade.md)를 참조하세요. |
| 클래식 스토리지 계정을 Azure Resource Manager로 마이그레이션 | Azure Resource Manager 배포 모델은 기능, 확장성 및 보안 측면에서 클래식 배포 모델보다 우수합니다. 클래식 스토리지 계정을 Azure Resource Manager로 마이그레이션하는 방법에 대한 자세한 내용은 **클래식에서 Azure Resource Manager로 IaaS 리소스의 플랫폼 지원 마이그레이션** 에서 [스토리지 계정 마이그레이션](../../virtual-machines/migration-classic-resource-manager-overview.md#migration-of-storage-accounts)을 참조하세요. |

## <a name="copying-data-into-a-storage-account"></a>스토리지 계정에 데이터 복사

Microsoft는 온-프레미스 스토리지 디바이스나 타사 클라우드 스토리지 공급자에서 데이터를 가져오기 위한 유틸리티와 라이브러리를 제공합니다. 사용하는 솔루션은 전송 데이터의 크기에 따라 다릅니다.

범용 v1 또는 Blob Storage 계정에서 범용 v2 계정으로 업그레이드할 경우 데이터가 자동으로 마이그레이션됩니다. 이 경로로 계정을 업그레이드하는 것이 좋습니다. 그러나 범용 v1 계정에서 Blob Storage 계정으로 데이터를 이동할 경우 아래에서 설명하는 도구 및 라이브러리를 사용하여 수동으로 데이터를 마이그레이션합니다.

### <a name="azcopy"></a>AzCopy

AzCopy는 Azure Storage의 데이터를 고속으로 복사하기 위해 설계된 Windows 명령줄 유틸리티입니다. AzCopy를 사용하여 기존 범용 스토리지 계정의 데이터를 Blob Storage 계정으로 복사하거나, 온-프레미스 스토리지 디바이스의 데이터를 업로드할 수 있습니다. 자세한 내용은 [AzCopy 명령줄 유틸리티를 사용하여 데이터 전송](./storage-use-azcopy-v10.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)을 참조하세요.

### <a name="data-movement-library"></a>데이터 이동 라이브러리

.NET용 Azure Storage 데이터 이동 라이브러리는 AzCopy를 구동하는 핵심 데이터 이동 프레임워크를 기반으로 합니다. 이 라이브러리는 AzCopy와 유사하게 성능이 높고 안정적이며 사용이 간편한 데이터 전송 작업을 제공합니다. 데이터 이동 라이브러리를 사용하여 AzCopy 기능을 고유하게 활용할 수 있습니다. 자세한 내용은 [.NET용 Azure Storage 데이터 이동 라이브러리](https://github.com/Azure/azure-storage-net-data-movement)를 참조하세요.

### <a name="rest-api-or-client-library"></a>REST API 또는 클라이언트 라이브러리

범용 v1 스토리지 계정에서 Blob Storage 계정으로 데이터를 마이그레이션하는 사용자 지정 애플리케이션을 만들 수 있습니다. Azure 클라이언트 라이브러리 또는 Azure Storage 서비스 REST API 중 하나를 사용합니다. Azure Storage는 NET, Java, C++, Node.JS, PHP, Ruby, Python 등, 여러 언어와 플랫폼을 위한 다양한 클라이언트 라이브러리를 제공합니다. 이 클라이언트 라이브러리는 재시도 논리, 로깅, 병렬 업로드와 같은 고급 기능을 제공합니다. HTTP/HTTPS 요청이 가능한 모든 언어로 호출할 수 있는 REST API에 대해 바로 개발할 수도 있습니다.

Azure Storage REST API에 대한 자세한 내용은 [Azure Storage REST API 참조](/rest/api/storageservices/)를 참조하세요.

> [!IMPORTANT]
> 클라이언트 쪽 암호화를 사용하여 암호화된 Blob은 Blob에 암호화 관련 메타데이터를 저장합니다. 클라이언트 쪽 암호화를 사용하여 암호화된 Blob을 복사하는 경우 복사 작업에서 Blob 메타데이터, 특히 암호화 관련 메타데이터를 유지해야 합니다. 암호화 메타데이터 없이 Blob을 복사하면 Blob 콘텐츠를 다시 검색할 수 없습니다. 암호화 관련 메타데이터에 대한 자세한 내용은 [Azure Storage 클라이언트 쪽 암호화](../common/storage-client-side-encryption.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)를 참조하세요.

## <a name="encryption"></a>암호화

스토리지 계정의 모든 데이터는 서비스 쪽에서 암호화됩니다. 암호화에 대한 자세한 내용은 [미사용 데이터에 대한 Azure Storage 서비스 암호화](storage-service-encryption.md)를 참조하세요.

## <a name="storage-account-billing"></a>Storage 계정 사용 비용

스토리지 계정 사용량에 따라 Azure Storage 요금이 청구됩니다. 스토리지 계정의 모든 개체는 그룹으로 합산 청구됩니다. 스토리지 비용은 다음 요인에 따라 계산됩니다.

- **지역** 이란 계정이 기초하는 지리적 위치를 의미합니다.
- **계정 유형** 은 사용 중인 스토리지 계정 유형을 나타냅니다.
- **액세스 계층** 은 범용 v2 또는 Blob 스토리지 계정에 지정한 데이터 사용 패턴을 나타냅니다.
- **용량** 은 데이터를 저장하는 데 사용하는 스토리지 계정 할당량이 어느 정도인지를 나타냅니다.
- **복제** 에 따라 한 번에 유지 관리되는 데이터의 복사본 수 및 위치가 결정됩니다.
- **트랜잭션** 은 Azure Storage에 대한 모든 읽기 및 쓰기 작업을 나타냅니다.
- **데이터 송신** 은 Azure 지역 외부에서 전송되는 데이터를 나타냅니다. 스토리지 계정의 데이터에 동일한 지역에서 실행되지 않는 애플리케이션이 액세스하는 경우 데이터 송신 요금이 부과됩니다. 리소스 그룹을 기반으로 동일한 지역의 데이터 및 서비스를 그룹화하여 송신 요금을 제한하는 방법에 대한 자세한 내용은 [Azure 리소스 그룹이란?](/azure/cloud-adoption-framework/govern/resource-consistency/resource-access-management#what-is-an-azure-resource-group)을 참조하세요.

[Azure Storage 가격 책정](https://azure.microsoft.com/pricing/details/storage/) 페이지는 계정 유형, 스토리지 용량, 복제 및 트랜잭션에 대한 자세한 가격 정보를 제공합니다. [데이터 전송 가격 정보](https://azure.microsoft.com/pricing/details/data-transfers/) 에서는 데이터 송신에 대한 자세한 가격 정보를 제공합니다. [Azure Storage 가격 계산기](https://azure.microsoft.com/pricing/calculator/?scenario=data-management) 를 사용하여 비용을 예측할 수 있습니다.

[!INCLUDE [cost-management-horizontal](../../../includes/cost-management-horizontal.md)]

## <a name="next-steps"></a>다음 단계

- [스토리지 계정을 만드는](storage-account-create.md)
- [블록 Blob 스토리지 계정 만들기](../blobs/storage-blob-create-account-block-blob.md)
- [범용 v2 스토리지 계정으로 업그레이드](storage-account-upgrade.md)
- [삭제된 스토리지 계정 복구](storage-account-recover.md)