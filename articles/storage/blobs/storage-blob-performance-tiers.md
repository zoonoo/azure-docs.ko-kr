---
title: Blob 저장소 성능 계층 차단 — Azure 저장소
description: Azure 블록 Blob 저장소에 대한 프리미엄 성능 계층과 표준 성능 계층간의 차이점에 대해 설명합니다.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 11/12/2019
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.reviewer: clausjor
ms.openlocfilehash: ff82986b27d038c536872b07e1308b0d48fadaef
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74270214"
---
# <a name="performance-tiers-for-block-blob-storage"></a>블록 Blob 저장소를 위한 성능 계층

기업이 성능에 민감한 클라우드 네이티브 애플리케이션을 배포할 때 다양한 성능 수준에서 비용 효율적인 데이터 스토리지를 위한 옵션을 준비하는 것이 중요합니다.

Azure 블록 Blob 저장소는 두 가지 성능 계층을 제공합니다.

- **프리미엄**: 높은 트랜잭션 속도와 한 자리 일관된 스토리지 대기 시간에 최적화
- **표준**: 고용량 및 높은 처리량에 최적화

다음 고려 사항은 다양한 성능 계층에 적용됩니다.

| 영역 |표준 성능  |프리미엄 성능  |
|---------|---------|---------|
|지역 가용성     |   모든 지역      | [일부 지역에서](https://azure.microsoft.com/global-infrastructure/services/?products=storage)       |
|지원되는 [저장소 계정 유형](../common/storage-account-overview.md#types-of-storage-accounts)     |     범용 v2, BlobStorage, 범용 v1    |    블록블블스토리지     |
|[높은 처리량 블록 Blob 지원](https://azure.microsoft.com/blog/high-throughput-with-azure-blob-storage/)     |    예, 4 MiB 퍼트 블록 또는 PutBlob 크기보다 큰     |    예, 256 키브 퍼트 블록 또는 PutBlob 크기보다 큰    |
|중복     |     [저장소 계정 유형](../common/storage-account-overview.md#types-of-storage-accounts) 보기   |  현재 로컬 중복 저장소(LRS) 및 영역 중복 저장소(ZRS)만 지원<div role="complementary" aria-labelledby="zone-redundant-storage"><sup>1</sup></div>     |

<div id="zone-redundant-storage"><sup>1개</sup> 영역 중복 저장소(ZRS)는 프리미엄 성능 블록 Blob 저장소 계정에 대해 일부 지역에서 사용할 수 있습니다.</div>

비용과 관련하여 프리미엄 성능은 트랜잭션 속도가 높은 애플리케이션에 최적화된 가격 책정을 제공하여 이러한 워크로드에 대한 [총 스토리지 비용을 낮출](https://azure.microsoft.com/blog/reducing-overall-storage-costs-with-azure-premium-blob-storage/) 수 있도록 합니다.

## <a name="premium-performance"></a>프리미엄 성능

프리미엄 성능 블록 Blob 스토리지를 사용하면 고성능 하드웨어를 통해 데이터를 사용할 수 있습니다. 데이터는 낮은 대기 시간에 최적화된 솔리드 스테이트 드라이브(SSD)에 저장됩니다. SSD는 기존 하드 드라이브에 비해 처리량이 높습니다.

프리미엄 성능 스토리지는 빠르고 일관된 응답 시간이 필요한 워크로드에 이상적입니다. 많은 소규모 트랜잭션을 수행하는 워크로드에 가장 적합합니다. 워크로드의 예는 다음과 같습니다.

- **대화형 워크로드 .** 이러한 워크로드에는 전자 상거래 및 매핑 응용 프로그램과 같은 즉각적인 업데이트 및 사용자 피드백이 필요합니다. 예를 들어 전자 상거래 응용 프로그램에서 자주 볼 수 있는 항목이 캐시되지 않을 수 있습니다. 그러나 필요에 따라 고객에게 즉시 표시되어야 합니다.

- **분석**. IoT 시나리오에서는 1초마다 많은 소규모 쓰기 작업이 클라우드로 푸시될 수 있습니다. 대량의 데이터를 가져와 분석 목적으로 집계한 다음 거의 즉시 삭제할 수 있습니다. 프리미엄 블록 Blob 저장소의 높은 섭취 기능은 이러한 유형의 워크로드에 대해 효율적입니다.

- **인공 지능 / 기계 학습 (AI / ML)**. AI/ML은 시각적 개체, 음성 및 텍스트와 같은 다양한 데이터 유형의 소비 및 처리를 다룹니다. 이 고성능 컴퓨팅 유형의 워크로드는 데이터 분석을 위해 신속한 응답과 효율적인 수집 시간이 필요한 대량의 데이터를 처리합니다.

- **데이터 변환**. 데이터의 지속적인 편집, 수정 및 변환이 필요한 프로세스에는 즉각적인 업데이트가 필요합니다. 정확한 데이터 표현을 위해 이 데이터의 소비자는 이러한 변경 사항이 즉시 반영되는지 확인해야 합니다.

## <a name="standard-performance"></a>표준 성능

표준 성능은 다양한 [액세스 계층을](storage-blob-storage-tiers.md) 지원하여 가장 비용 효율적인 방식으로 데이터를 저장합니다. 대용량 데이터 세트의 대용량 및 높은 처리량에 최적화되어 있습니다.

- **백업 및 재해 복구 데이터 집합**. 표준 성능 스토리지는 비용 효율적인 계층을 제공하므로 단기 및 장기 재해 복구 데이터 집합, 보조 백업 및 규정 준수 데이터 보관에 완벽한 사용 사례입니다.

- **미디어 콘텐츠**. 이미지와 비디오는 처음 만들고 저장할 때 자주 액세스되지만 이 콘텐츠 유형은 나이가 들수록 덜 자주 사용됩니다. 표준 성능 저장소는 미디어 콘텐츠 요구에 적합한 계층을 제공합니다. 

- **대량 데이터 처리**. 이러한 종류의 워크로드는 일관된 낮은 대기 시간 대신 비용 효율적인 고처리량 스토리지가 필요하기 때문에 표준 스토리지에 적합합니다. 대규모 원시 데이터 집합은 처리를 위해 준비되고 결국 쿨러 계층으로 마이그레이션됩니다.

## <a name="migrate-from-standard-to-premium"></a>표준에서 프리미엄으로 마이그레이션

기존 표준 성능 저장소 계정을 프리미엄 성능의 블록 Blob 저장소 계정으로 변환할 수 없습니다. 프리미엄 성능 저장소 계정으로 마이그레이션하려면 BlockBlobStorage 계정을 만들고 데이터를 새 계정으로 마이그레이션해야 합니다. 자세한 내용은 [BlockBlobStorage 계정 만들기를](storage-blob-create-account-block-blob.md)참조하십시오.

저장소 계정 간에 Blob을 복사하려면 최신 버전의 [AzCopy](../common/storage-use-azcopy-blobs.md) 명령줄 도구를 사용할 수 있습니다. Azure 데이터 팩터리와 같은 다른 도구도 데이터 이동 및 변환에 사용할 수 있습니다.

## <a name="blob-lifecycle-management"></a>Blob 수명 주기 관리

Blob 저장소 수명 주기 관리는 풍부한 규칙 기반 정책을 제공합니다.

- **프리미엄**: 수명 주기가 끝날 때 데이터가 만료됩니다.
- **표준**: 데이터를 최상의 액세스 계층으로 전환하고 수명 주기가 끝날 때 데이터를 만료합니다.

자세한 내용은 [Azure Blob 저장소 수명 주기 관리를](storage-lifecycle-management-concepts.md)참조하십시오.

프리미엄 블록 Blob 저장소 계정에 저장된 데이터는 핫, 쿨 및 아카이브 계층 간에 이동할 수 없습니다. 그러나 블록 Blob 저장소 계정에서 *다른* 계정의 핫 액세스 계층으로 Blob을 복사할 수 있습니다. 데이터를 다른 계정으로 복사하려면 [URL API에서 차단](/rest/api/storageservices/put-block-from-url) 넣기 API 또는 [AzCopy v10을](../common/storage-use-azcopy-v10.md)사용합니다. **URL에서 차단 넣기** API는 서버에서 데이터를 동기적으로 복사합니다. 모든 데이터가 원래 서버 위치에서 대상 위치로 이동된 후에만 호출이 완료됩니다.

## <a name="next-steps"></a>다음 단계

GPv2 및 Blob 저장소 계정에서 핫, 쿨 및 아카이브를 평가합니다.

- [아카이브 계층에서 Blob 데이터 리하이드레이션에 대해 알아보기](storage-blob-rehydration.md)
- [Azure Storage 메트릭을 활성화하여 현재 Storage 계정의 사용 현황 평가](../common/storage-enable-and-view-metrics.md)
- [지역별 Blob 저장소 및 GPv2 계정에서 핫, 쿨 및 아카이브 가격 확인](https://azure.microsoft.com/pricing/details/storage/)
- [데이터 전송 가격 확인](https://azure.microsoft.com/pricing/details/data-transfers/)
