---
title: Azure HDInsight 클러스터에 사용할 스토리지 옵션 비교
description: 스토리지 유형 및 Azure HDInsight에서 작동하는 원리에 대한 개요를 제공합니다.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/21/2020
ms.openlocfilehash: b351b9a4edd81da2983cca93127513f9041716bd
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/22/2020
ms.locfileid: "81869834"
---
# <a name="compare-storage-options-for-use-with-azure-hdinsight-clusters"></a>Azure HDInsight 클러스터에 사용할 스토리지 옵션 비교

HDInsight 클러스터를 만들 때 몇 가지 다른 Azure 저장소 서비스 중에서 선택할 수 있습니다.

* [Azure Storage](./overview-azure-storage.md)
* [Azure Data Lake Storage Gen2](./overview-data-lake-storage-gen1.md)
* [Azure Data Lake Storage Gen1](./overview-data-lake-storage-gen2.md)

이 문서에서는 이러한 스토리지 유형 및 각 유형의 고유 기능에 대한 개요를 제공합니다.

## <a name="storage-types-and-features"></a>스토리지 유형 및 기능

다음 표에는 다양한 버전의 HDInsight에서 지원되는 Azure Storage 서비스가 요약되어 있습니다.

| 스토리지 서비스 | 계정 유형 | 네임스페이스 유형 | 지원되는 서비스 | 지원되는 성능 계층 | 지원되는 액세스 계층 | HDInsight 버전 | 클러스터 유형 |
|---|---|---|---|---|---|---|---|
|Azure Data Lake Storage Gen2| 범용 V2 | 계층적(파일 시스템) | Blob | 표준 | 뜨거운, 시원한, 아카이브, 아카이브, 시원한, 시원한, 시원한, 시원한, 아카이브 | 3.6 이상 | 스파크 2.1과 2.2를 제외한 모든|
|Azure Storage| 범용 V2 | Object | Blob | 표준 | 뜨거운, 시원한, 아카이브, 아카이브, 시원한, 시원한, 시원한, 시원한, 아카이브 | 3.6 이상 | 모두 |
|Azure Storage| 범용 V1 | Object | Blob | 표준 | N/A | 모두 | 모두 |
|Azure Storage| Blob 스토리지** | Object | 블록 Blob | 표준 | 뜨거운, 시원한, 아카이브, 아카이브, 시원한, 시원한, 시원한, 시원한, 아카이브 | 모두 | 모두 |
|Azure Data Lake Storage Gen1| N/A | 계층적(파일 시스템) | N/A | N/A | N/A | 3.6 만 | HBase를 제외한 모든 |

**HDInsight 클러스터의 경우 보조 저장소 계정만 BlobStorage 유형이 될 수 있으며 페이지 Blob은 지원되는 저장소 옵션이 아닙니다.

Azure Storage 계정 유형에 대한 자세한 내용은 [Azure 저장소 계정 개요를](../storage/common/storage-account-overview.md) 참조하십시오.

Azure Storage 액세스 계층에 대한 자세한 내용은 [Azure Blob 저장소: 프리미엄(미리 보기), 핫, 쿨 및 보관 저장소 계층을](../storage/blobs/storage-blob-storage-tiers.md) 참조하십시오.

기본 및 선택적 보조 저장소에 대한 서비스 조합을 사용하여 클러스터를 만들 수 있습니다. 다음 표에는 HDInsight에서 현재 지원되는 클러스터 저장소 구성이 요약되어 있습니다.

| HDInsight 버전 | 기본 스토리지 | 보조 스토리지 | 지원됨 |
|---|---|---|---|
| 3.6 & 4.0 | 범용 V1, 범용 V2 | 범용 V1, 범용 V2, BlobStorage(블록 Blob) | 예 |
| 3.6 & 4.0 | 범용 V1, 범용 V2 | Data Lake Storage Gen2 | 예 |
| 3.6 & 4.0 | 데이터 레이크 스토리지 Gen2* | Data Lake Storage Gen2 | 예 |
| 3.6 & 4.0 | 데이터 레이크 스토리지 Gen2* | 범용 V1, 범용 V2, BlobStorage(블록 Blob) | 예 |
| 3.6 & 4.0 | Data Lake Storage Gen2 | Data Lake Storage Gen1 | 예 |
| 3.6 | Data Lake Storage Gen1 | Data Lake Storage Gen1 | 예 |
| 3.6 | Data Lake Storage Gen1 | 범용 V1, 범용 V2, BlobStorage(블록 Blob) | 예 |
| 3.6 | Data Lake Storage Gen1 | Data Lake Storage Gen2 | 예 |
| 4.0 | Data Lake Storage Gen1 | 모두 | 예 |
| 4.0 | 범용 V1, 범용 V2 | Data Lake Storage Gen1 | 예 |

*=클러스터 액세스에 동일한 관리되는 ID를 사용하도록 모두 설정되어 있는 한 하나 또는 여러 개의 Data Lake Storage Gen2 계정이 될 수 있습니다.

> [!NOTE]
> 데이터 레이크 스토리지 Gen2 기본 스토리지는 Spark 2.1 또는 2.2 클러스터에서 지원되지 않습니다.

## <a name="next-steps"></a>다음 단계

* [Azure 저장소 개요](./overview-azure-storage.md)
* [Azure 데이터 레이크 스토리지 Gen1 개요](./overview-data-lake-storage-gen1.md)
* [Azure 데이터 레이크 스토리지 Gen2 개요](./overview-data-lake-storage-gen2.md)
* [Azure Data Lake Storage Gen2 소개](../storage/blobs/data-lake-storage-introduction.md)
* [Azure Storage 소개](../storage/common/storage-introduction.md)
