---
title: 블록 blob storage 성능 계층-Azure Storage
description: Azure 블록 blob 저장소에 대 한 프리미엄 및 표준 성능 계층 간의 차이점을 설명 합니다.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 11/12/2019
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.reviewer: clausjor
ms.openlocfilehash: ff82986b27d038c536872b07e1308b0d48fadaef
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/27/2020
ms.locfileid: "74270214"
---
# <a name="performance-tiers-for-block-blob-storage"></a>블록 blob 저장소에 대 한 성능 계층

기업은 성능이 중요 한 클라우드 네이티브 응용 프로그램을 배포할 때 다양 한 성능 수준에서 비용 효율적인 데이터 저장소에 대 한 옵션을 선택 하는 것이 중요 합니다.

Azure 블록 blob 저장소는 다음과 같은 두 가지 성능 계층을 제공 합니다.

- **프리미엄**: 높은 트랜잭션 속도 및 단일 자릿수가 일관 된 저장소 대기 시간에 최적화 됨
- **표준**: 고용량 및 높은 처리량에 최적화 됨

다음은 다양 한 성능 계층에 적용 되는 고려 사항입니다.

| 영역 |표준 성능  |프리미엄 성능  |
|---------|---------|---------|
|지역 가용성     |   모든 지역      | [영역 선택](https://azure.microsoft.com/global-infrastructure/services/?products=storage)       |
|지원 되는 [저장소 계정 유형](../common/storage-account-overview.md#types-of-storage-accounts)     |     범용 v2, BlobStorage, 범용 v1    |    BlockBlobStorage     |
|[높은 처리량 블록 blob](https://azure.microsoft.com/blog/high-throughput-with-azure-blob-storage/) 지원     |    예, 4 MiB PutBlock 또는 Putblock 크기를 초과 합니다.     |    예, 256 KiB PutBlock 또는 Putblock 크기 보다 큼    |
|중복     |     [저장소 계정 유형을](../common/storage-account-overview.md#types-of-storage-accounts) 참조 하세요.   |  현재 LRS (로컬 중복 저장소) 및 ZRS (영역 redudant 저장소)만 지원 합니다.<div role="complementary" aria-labelledby="zone-redundant-storage"><sup>1</sup></div>     |

<div id="zone-redundant-storage"><sup>1</sup> ZRS (영역 중복 저장소)는 프리미엄 성능 블록 blob storage 계정에 대 한 선택 영역에서 사용할 수 있습니다.</div>

비용과 관련 하 여 premium 성능은 이러한 워크 로드에 대 한 [총 저장소 비용을 절감할](https://azure.microsoft.com/blog/reducing-overall-storage-costs-with-azure-premium-blob-storage/) 수 있도록 트랜잭션 요금이 높은 응용 프로그램에 대해 최적화 된 가격 책정을 제공 합니다.

## <a name="premium-performance"></a>프리미엄 성능

프리미엄 성능 블록 blob 저장소는 고성능 하드웨어를 통해 데이터를 사용할 수 있도록 합니다. 데이터는 짧은 대기 시간에 최적화 된 Ssd (반도체 드라이브)에 저장 됩니다. Ssd는 기존 하드 드라이브에 비해 더 높은 처리량을 제공 합니다.

프리미엄 성능 저장소는 빠르고 일관 된 응답 시간이 필요한 워크 로드에 이상적입니다. 많은 작은 트랜잭션을 수행 하는 작업에 가장 적합 합니다. 예제 작업은 다음과 같습니다.

- **대화형 작업**. 이러한 작업을 수행 하려면 전자 상거래 및 매핑 응용 프로그램과 같은 사용자 의견 및 즉각적인 업데이트가 필요 합니다. 예를 들어 전자 상거래 응용 프로그램에서 자주 표시 되지 않는 항목은 캐시 되지 않을 가능성이 높습니다. 그러나 요청 시 고객에 게 즉시 표시 되어야 합니다.

- **분석**. IoT 시나리오에서는 초 마다 많은 작은 쓰기 작업을 클라우드에 푸시할 수 있습니다. 분석 목적으로 집계 된 다음 거의 즉시 삭제 된 많은 양의 데이터를에서 수행할 수 있습니다. 프리미엄 블록 blob 저장소의 높은 수집 기능을 사용 하면 이러한 유형의 작업을 효율적으로 수행할 수 있습니다.

- **인공 지능/기계 학습 (AI/ML)**. AI/ML은 시각적 개체, 음성 및 텍스트와 같은 다양 한 데이터 형식의 사용량과 처리를 다룹니다. 이 고성능 컴퓨팅 유형은 데이터 분석에 대 한 신속한 응답과 효율적인 수집 시간을 필요로 하는 대량의 데이터를 처리 합니다.

- **데이터 변환**. 데이터를 일관 되 게 편집, 수정 및 변환 해야 하는 프로세스에는 즉시 업데이트가 필요 합니다. 정확한 데이터 표현의 경우이 데이터의 소비자는 즉시 반영 되는 이러한 변경 내용을 확인 해야 합니다.

## <a name="standard-performance"></a>표준 성능

표준 성능은 가장 비용 효율적인 방식으로 데이터를 저장 하는 다양 한 [액세스 계층](storage-blob-storage-tiers.md) 을 지원 합니다. 대용량 데이터 집합에 대 한 높은 용량과 처리량에 최적화 되어 있습니다.

- **백업 및 재해 복구 데이터 집합** Standard performance storage는 비용 효율적인 계층을 제공 하므로 단기 및 장기 재해 복구 데이터 집합, 보조 백업 및 규정 준수 데이터 보관에 대 한 완벽 한 사용 사례를 제공 합니다.

- **미디어 콘텐츠**. 이미지 및 비디오는 처음 생성 및 저장 될 때 자주 액세스 되지만이 콘텐츠 형식은 오래 된 경우 자주 사용 됩니다. Standard performance storage는 미디어 콘텐츠 요구 사항에 적합 한 계층을 제공 합니다. 

- **대량 데이터 처리**. 이러한 종류의 작업은 일관 된 짧은 대기 시간 보다 비용 효율적인 처리량이 높은 저장소를 필요로 하기 때문에 표준 저장소에 적합 합니다. 대량 원시 데이터 집합은 처리를 위해 준비 되 고 궁극적으로는 궁극적 계층으로 마이그레이션됩니다.

## <a name="migrate-from-standard-to-premium"></a>표준에서 프리미엄으로 마이그레이션

기존 standard performance storage 계정을 프리미엄 성능으로 블록 blob storage 계정으로 변환할 수 없습니다. Premium performance storage 계정으로 마이그레이션하려면 BlockBlobStorage 계정을 만들고 데이터를 새 계정으로 마이그레이션해야 합니다. 자세한 내용은 [BlockBlobStorage 계정 만들기](storage-blob-create-account-block-blob.md)를 참조 하세요.

저장소 계정 간에 blob을 복사 하려면 최신 버전의 [AzCopy](../common/storage-use-azcopy-blobs.md) 명령줄 도구를 사용할 수 있습니다. Azure Data Factory와 같은 기타 도구는 데이터 이동 및 변환에도 사용할 수 있습니다.

## <a name="blob-lifecycle-management"></a>Blob 수명 주기 관리

Blob 저장소 수명 주기 관리는 다음과 같은 다양 한 규칙 기반 정책을 제공 합니다.

- **Premium**: 수명 주기의 끝에서 데이터를 만료 합니다.
- **표준**: 데이터를 최상의 액세스 계층으로 전환 하 고 수명 주기 종료 시 데이터를 만료 합니다.

자세히 알아보려면 [Azure Blob 저장소 수명 주기 관리](storage-lifecycle-management-concepts.md)를 참조 하세요.

프리미엄 블록 blob 저장소 계정에 저장 된 데이터는 핫, 쿨 및 보관 계층 간에 이동할 수 없습니다. 그러나 블록 blob 저장소 계정에서 *다른* 계정의 핫 액세스 계층으로 blob을 복사할 수 있습니다. 다른 계정으로 데이터를 복사 하려면 [URL API의 Put 블록](/rest/api/storageservices/put-block-from-url) 또는 [AzCopy v10](../common/storage-use-azcopy-v10.md)를 사용 합니다. **URL의 Put 블록** 은 서버의 데이터를 동기적으로 복사 합니다. 이 호출은 모든 데이터가 원래 서버 위치에서 대상 위치로 이동 된 후에만 완료 됩니다.

## <a name="next-steps"></a>다음 단계

GPv2 및 Blob 저장소 계정에서 핫, 쿨 및 보관을 평가 합니다.

- [보관 계층에서 blob 데이터 리하이드레이션에 대 한 자세한 정보](storage-blob-rehydration.md)
- [Azure Storage 메트릭을 활성화하여 현재 Storage 계정의 사용 현황 평가](../common/storage-enable-and-view-metrics.md)
- [지역별 Blob storage 및 GPv2 계정에서 핫, 쿨 및 보관 가격 책정 확인](https://azure.microsoft.com/pricing/details/storage/)
- [데이터 전송 가격 확인](https://azure.microsoft.com/pricing/details/data-transfers/)
