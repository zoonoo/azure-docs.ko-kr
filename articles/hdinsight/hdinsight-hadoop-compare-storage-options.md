---
title: Azure HDInsight 클러스터에 사용할 스토리지 옵션 비교
description: 스토리지 유형 및 Azure HDInsight에서 작동하는 원리에 대한 개요를 제공합니다.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: seoapr2020
ms.date: 04/21/2020
ms.openlocfilehash: bfbe311d9768923eee8c1b0cc4f3b4ec1a7ad69a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "82186308"
---
# <a name="compare-storage-options-for-use-with-azure-hdinsight-clusters"></a>Azure HDInsight 클러스터에 사용할 스토리지 옵션 비교

HDInsight 클러스터를 만들 때 몇 가지 Azure storage 서비스 중에서 선택할 수 있습니다.

* [Azure Storage](./overview-azure-storage.md)
* [Azure Data Lake Storage Gen2](./overview-data-lake-storage-gen1.md)
* [Azure Data Lake Storage Gen1](./overview-data-lake-storage-gen2.md)

이 문서에서는 이러한 스토리지 유형 및 각 유형의 고유 기능에 대한 개요를 제공합니다.

## <a name="storage-types-and-features"></a>저장소 유형 및 기능

다음 표에서는 다양 한 버전의 HDInsight에서 지원 되는 Azure Storage 서비스를 요약 합니다.

| 저장소 서비스 | 계정 유형 | 네임 스페이스 형식 | 지원되는 서비스 | 지원되는 성능 계층 | 지원되는 액세스 계층 | HDInsight 버전 | 클러스터 유형 |
|---|---|---|---|---|---|---|---|
|Azure Data Lake Storage Gen2| 범용 V2 | 계층 구조 (파일 시스템) | Blob | Standard | 핫, 쿨, 보관 | 3.6 이상 | Spark 2.1 및 2.2을 제외한 모든|
|Azure Storage| 범용 V2 | Object | Blob | Standard | 핫, 쿨, 보관 | 3.6 이상 | 모두 |
|Azure Storage| 범용 V1 | Object | Blob | Standard | 해당 없음 | 모두 | 모두 |
|Azure Storage| Blob Storage * * | Object | 블록 Blob | Standard | 핫, 쿨, 보관 | 모두 | 모두 |
|Azure Data Lake Storage Gen1| 해당 없음 | 계층 구조 (파일 시스템) | 해당 없음 | 해당 없음 | 해당 없음 | 3.6만 해당 | HBase를 제외한 모든 |

* * HDInsight 클러스터의 경우에는 보조 저장소 계정만 BlobStorage 유형이 될 수 있으며 페이지 Blob은 지원 되는 저장소 옵션이 아닙니다.

Azure Storage 계정 유형에 대 한 자세한 내용은 [Azure Storage 계정 개요](../storage/common/storage-account-overview.md) 를 참조 하세요.

Azure Storage 액세스 계층에 대 한 자세한 내용은 [Azure Blob storage: 프리미엄 (미리 보기), 핫, 쿨 및 보관 저장소 계층](../storage/blobs/storage-blob-storage-tiers.md) 을 참조 하세요.

기본 및 선택적 보조 저장소에 대 한 서비스 조합을 사용 하 여 클러스터를 만들 수 있습니다. 다음 표에는 HDInsight에서 현재 지원 되는 클러스터 저장소 구성이 요약 되어 있습니다.

| HDInsight 버전 | 기본 저장소 | 보조 저장소 | 지원됨 |
|---|---|---|---|
| 3.6 & 4.0 | 범용 V1, 범용 V2 | 범용 V1, 범용 V2, BlobStorage (블록 Blob) | 예 |
| 3.6 & 4.0 | 범용 V1, 범용 V2 | Data Lake Storage Gen2 | 아니요 |
| 3.6 & 4.0 | Data Lake Storage Gen2 * | Data Lake Storage Gen2 | 예 |
| 3.6 & 4.0 | Data Lake Storage Gen2 * | 범용 V1, 범용 V2, BlobStorage (블록 Blob) | 예 |
| 3.6 & 4.0 | Data Lake Storage Gen2 | Data Lake Storage Gen1 | 아니요 |
| 3.6 | Data Lake Storage Gen1 | Data Lake Storage Gen1 | 예 |
| 3.6 | Data Lake Storage Gen1 | 범용 V1, 범용 V2, BlobStorage (블록 Blob) | 예 |
| 3.6 | Data Lake Storage Gen1 | Data Lake Storage Gen2 | 아니요 |
| 4.0 | Data Lake Storage Gen1 | 모두 | 아니요 |
| 4.0 | 범용 V1, 범용 V2 | Data Lake Storage Gen1 | 아니요 |

* = 클러스터 액세스에 동일한 관리 되는 id를 사용 하도록 모두 설정 하는 한 하나 또는 여러 개의 Data Lake Storage Gen2 계정일 수 있습니다.

> [!NOTE]
> Data Lake Storage Gen2 기본 저장소는 Spark 2.1 또는 2.2 클러스터에 대해 지원 되지 않습니다.

## <a name="next-steps"></a>다음 단계

* [Azure Storage 개요](./overview-azure-storage.md)
* [Azure Data Lake Storage Gen1 개요](./overview-data-lake-storage-gen1.md)
* [Azure Data Lake Storage Gen2 개요](./overview-data-lake-storage-gen2.md)
* [Azure Data Lake Storage Gen2 소개](../storage/blobs/data-lake-storage-introduction.md)
* [Azure Storage 소개](../storage/common/storage-introduction.md)
