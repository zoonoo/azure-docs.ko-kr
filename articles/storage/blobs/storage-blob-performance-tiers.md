---
title: Azure Block Blob storage 성능 계층-Azure Storage
description: Azure blob 저장소에 대 한 성능 계층.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 10/02/2019
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.reviewer: clausjor
ms.openlocfilehash: e0d746f1b01784bc383c12543936f06dae66ca09
ms.sourcegitcommit: f7f70c9bd6c2253860e346245d6e2d8a85e8a91b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2019
ms.locfileid: "73063249"
---
# <a name="azure-block-blob-storage-performance-tiers"></a>Azure 블록 Blob 저장소 성능 계층

기업은 성능이 중요 한 클라우드 네이티브 응용 프로그램을 배포할 때 다양 한 성능 수준에서 비용 효율적인 데이터 저장소에 대 한 옵션을 선택 하는 것이 중요 합니다.

Azure 블록 blob 저장소는 다음과 같은 두 가지 성능 계층을 제공 합니다.

- 프리미엄: 높은 트랜잭션 속도 및 단일 자릿수가 일관 된 저장소 대기 시간에 최적화 됨
- 표준: 고용량 및 높은 처리량에 최적화 됨

다음은 다양 한 성능 계층에 적용 되는 고려 사항입니다.

- 표준 성능은 모든 [Azure 지역](https://azure.microsoft.com/global-infrastructure/services/?products=storage)에서 사용할 수 있습니다. 프리미엄 성능은 [선택 지역](https://azure.microsoft.com/global-infrastructure/services/?products=storage)에서 사용할 수 있습니다.
- Premium 성능은 이러한 워크 로드에 대 한 [총 저장소 비용을 줄일](https://azure.microsoft.com/blog/reducing-overall-storage-costs-with-azure-premium-blob-storage/) 수 있도록 트랜잭션 요금이 높은 응용 프로그램에 대해 최적화 된 가격 책정을 제공 합니다.
- 블록 blob에 대 한 프리미엄 성능을 얻으려면 BlockBlobStorage 계정 유형을 사용 해야 합니다.
- 표준 성능은 범용 v1, 범용 v2 및 Blob storage 계정에서 사용할 수 있습니다.
- 프리미엄 및 표준 성능은 모두 [높은 처리량 블록 blob](https://azure.microsoft.com/blog/high-throughput-with-azure-blob-storage/)을 지원 합니다. 높은 처리량 블록 blob은 256 KiB 보다 높은 프리미엄 성능에 사용할 수 있습니다. 높은 처리량 블록 blob은 4 MiB Put 블록 또는 Put Blob 크기 보다 큰 표준 성능에 사용할 수 있습니다.
- 프리미엄 성능은 현재 LRS (로컬 중복 저장소) 에서만 사용할 수 있습니다.

## <a name="premium-performance"></a>프리미엄 성능

프리미엄 성능 블록 blob 저장소는 고성능 하드웨어를 통해 데이터를 사용할 수 있도록 합니다. 데이터는 짧은 대기 시간에 최적화 된 Ssd (반도체 드라이브)에 저장 됩니다. Ssd는 기존 하드 드라이브에 비해 더 높은 처리량을 제공 합니다.

프리미엄 성능 블록 blob 저장소는 빠르고 일관 된 응답 시간이 필요한 워크 로드에 적합 합니다. 많은 작은 트랜잭션을 수행 하는 작업에 가장 적합 합니다.

## <a name="standard-performance"></a>표준 성능

표준 성능은 가장 비용 효율적인 방식으로 데이터를 저장 하는 다양 한 [액세스 계층](storage-blob-storage-tiers.md) 을 지원 합니다. 대용량 데이터 집합에 대 한 높은 용량과 처리량에 최적화 되어 있습니다.

## <a name="blob-lifecycle-management"></a>Blob 수명 주기 관리

Blob Storage 수명 주기 관리는 다음과 같은 다양 한 규칙 기반 정책을 제공 합니다.

- 프리미엄-수명 주기 종료 시 데이터 만료
- 표준-최상의 액세스 계층으로 데이터를 전환 하 고 수명 주기 종료 시 데이터 만료

자세히 알아보려면 [Azure Blob 저장소 수명 주기 관리](storage-lifecycle-management-concepts.md)를 참조 하세요.

프리미엄 블록 blob 저장소 계정에 저장 된 데이터는 핫, 쿨 및 보관 계층 간에 이동할 수 없습니다. 그러나 블록 blob 저장소 계정에서 *다른* 계정의 핫 액세스 계층으로 blob을 복사할 수 있습니다. URL API [의 Put 블록](/rest/api/storageservices/put-block-from-url) 또는 [AzCopy v10](../common/storage-use-azcopy-v10.md) 를 사용 하 여 다른 계정으로 데이터를 복사 합니다. **URL의 Put 블록** 은 서버의 데이터를 동기적으로 복사 합니다. 이 호출은 모든 데이터가 원래 서버 위치에서 대상 위치로 이동 된 후에만 완료 됩니다.

## <a name="next-steps"></a>다음 단계

GPv2 및 Blob 저장소 계정에서 핫, 쿨 및 보관 평가

- [지역별 핫, 쿨 및 보관의 가용성 확인](https://azure.microsoft.com/regions/#services)
- [Azure Blob Storage 수명 주기 관리](storage-lifecycle-management-concepts.md)
- [보관 계층에서 blob 데이터 리하이드레이션에 대 한 자세한 정보](storage-blob-rehydration.md)
- [Azure Storage 메트릭을 활성화하여 현재 Storage 계정의 사용 현황 평가](../common/storage-enable-and-view-metrics.md)
- [지역별 Blob Storage 및 GPv2 계정에서 핫, 쿨 및 보관 가격 책정 확인](https://azure.microsoft.com/pricing/details/storage/)
- [데이터 전송 가격 확인](https://azure.microsoft.com/pricing/details/data-transfers/)
