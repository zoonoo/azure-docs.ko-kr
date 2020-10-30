---
title: Azure Data Lake Storage에 대 한 프리미엄 계층 Microsoft Docs
description: Azure Data Lake Storage Gen2에서 프리미엄 성능 계층 사용
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.custom: references_regions
ms.date: 10/28/2020
ms.author: normesta
ms.openlocfilehash: 9704ff7dec02a0de044863ec2da6280d65b30ef0
ms.sourcegitcommit: 4f4a2b16ff3a76e5d39e3fcf295bca19cff43540
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93043522"
---
# <a name="premium-tier-for-azure-data-lake-storage"></a>Azure Data Lake Storage에 대 한 프리미엄 계층

Azure Data Lake Storage Gen2은 이제 [프리미엄 성능 계층](storage-blob-performance-tiers.md#premium-performance)을 지원 합니다. 프리미엄 성능 계층은 빅 데이터 분석 응용 프로그램 및 일관성 있는 대기 시간이 낮고 트랜잭션 수가 많은 워크 로드에 적합 합니다. 

> [!NOTE]
> Azure Data Lake Storage에 대 한 프리미엄 계층은 공개 미리 보기로 제공 됩니다. [지역 가용성](#regional-availability)을 참조 하세요.

## <a name="workloads-that-can-benefit-from-the-premium-performance-tier"></a>프리미엄 성능 계층의 이점을 누릴 수 있는 워크 로드

예제 작업에는 대화형 작업, IoT, 스트리밍 분석, 인공 지능 및 기계 학습이 포함 됩니다. 

**대화형 작업** 

이러한 작업에는 전자 상거래 및 매핑 응용 프로그램, 대화형 비디오 응용 프로그램 등의 사용자 의견 및 즉각적인 업데이트가 필요 합니다. 예를 들어 전자 상거래 응용 프로그램에서 자주 표시 되지 않는 항목은 캐시 되지 않을 가능성이 높습니다. 그러나 요청 시 고객에 게 즉시 표시 되어야 합니다. 또 다른 예로, 데이터 과학자 분석가 및 개발자는 프리미엄 성능 계층을 사용 하는 계정에 저장 된 데이터에 대해 쿼리를 실행 하 여 시간이 중요 한 통찰력을 훨씬 더 빠르게 파생할 수 있습니다. 

**IoT/streaming analytics** 

IoT 시나리오에서는 초 마다 많은 작은 쓰기 작업을 클라우드에 푸시할 수 있습니다. 많은 양의 데이터를 수집 하 여 분석을 위해 집계 한 다음 거의 즉시 삭제할 수 있습니다. 프리미엄 성능 계층의 높은 수집 기능을 통해 이러한 유형의 작업을 효율적으로 수행할 수 있습니다. 

**인공 지능/기계 학습 (AI/ML)** 

AI/ML은 시각적 개체, 음성 및 텍스트와 같은 다양 한 데이터 형식의 사용량과 처리를 다룹니다. 이 고성능 컴퓨팅 유형은 데이터 분석에 대 한 신속한 응답과 효율적인 수집 시간을 필요로 하는 대량의 데이터를 처리 합니다. 

## <a name="cost-effectiveness"></a>비용 효과

프리미엄 성능 계층의 저장소 비용은 더 높고 표준 성능 계층에 비해 트랜잭션 비용은 낮아집니다. 응용 프로그램 및 워크 로드에서 많은 수의 트랜잭션을 실행 하는 경우 프리미엄 성능 계층은 비용 효율적일 수 있습니다.

다음 표에서는 Azure Data Lake Storage에 대 한 프리미엄 계층의 비용 효율성을 보여 줍니다. 각 열은 한 달의 트랜잭션 수를 나타냅니다.  각 행은 읽은 트랜잭션의 비율을 나타냅니다. 테이블의 각 셀은 읽기 트랜잭션 백분율 및 실행 된 트랜잭션 수와 관련 된 비용 감소 비율을 보여 줍니다. 

예를 들어 사용자의 계정이 미국 동부 2 지역에 있고, 계정이 포함 된 트랜잭션 수가 90M을 초과 하 고, 해당 트랜잭션 중 70%가 읽기 트랜잭션이 면 프리미엄 성능 계층은 더 비용 효율적입니다.

> [!div class="mx-imgBorder"]
> ![이미지 위치](./media/premium-tier-for-data-lake-storage/premium-performance-data-lake-storage-cost-analysis-table.png)

> [!NOTE] 
> 각 TB 데이터의 초당 트랜잭션 수를 기반으로 비용 효율성을 평가 하려는 경우 테이블의 아래쪽에 표시 되는 열 머리글을 사용할 수 있습니다.

가격 책정에 대 한 자세한 내용은 [Azure Data Lake Storage Gen2 가격 책정](https://azure.microsoft.com/pricing/details/storage/data-lake/) 페이지를 참조 하세요.

## <a name="feature-availability"></a>기능 가용성 

일부 Blob storage 기능을 사용 하지 못할 수도 있고 프리미엄 성능 계층을 부분적 으로만 지원할 수도 있습니다. 전체 목록은 [Azure Data Lake Storage Gen2에서 사용할 수 있는 Blob storage 기능](data-lake-storage-supported-blob-storage-features.md)을 참조 하세요. 그런 다음 [알려진 문제](data-lake-storage-known-issues.md) 목록을 검토 하 여 기능에 대 한 모든 차이를 평가 합니다.

## <a name="enabling-the-premium-performance-tier"></a>프리미엄 성능 계층 사용 

**계층 구조 네임 스페이스** 설정을 **사용 하도록 설정** 된 blockblobstorage 계정을 만들어 Azure Data Lake Storage에 대 한 프리미엄 계층을 사용할 수 있습니다. 전체 지침은 [BlockBlobStorage 계정 계정 만들기](storage-blob-create-account-block-blob.md) 를 참조 하세요.

계정을 만들 때 **Premium** Performance 옵션과 **blockblobstorage** 계정 종류를 선택 해야 합니다.

> [!div class="mx-imgBorder"]
> ![Blockblobstorageacount 만들기](./media/premium-tier-for-data-lake-storage/create-block-blob-storage-account.png)

**저장소 계정 만들기** 페이지의 **고급** 탭에서 **계층 구조 네임 스페이스** 설정을 사용 하도록 설정 합니다. 계정을 만들 때이 설정을 사용 하도록 설정 해야 합니다. 나중에 사용 하도록 설정할 수 없습니다.

다음 이미지는 **저장소 계정 만들기** 페이지에서이 설정을 보여 줍니다.

> [!div class="mx-imgBorder"]
> ![계층 구조 네임 스페이스 설정](./media/create-data-lake-storage-account/hierarchical-namespace-feature.png)

## <a name="regional-availability"></a>국가별 가용성

Azure Data Lake Storage에 대 한 프리미엄 계층은 다음 지역에서 제공 됩니다.

|Azure 지역|중복성|
|--|--|
|미국 동부|LRS, ZRS|
|미국 동부 2|LRS, ZRS|
|미국 중부|LRS|
|미국 서부|LRS|
|미국 서부 2|LRS, ZRS|
|미국 중서부|LRS|
|미국 중남부|LRS|
|캐나다 중부|LRS|
|캐나다 동부|LRS|
|북유럽|LRS, ZRS|
|서유럽|LRS, ZRS|
|영국 남부|LRS|
|영국 서부|LRS|
|프랑스 중부|LRS|
|아시아 동부|LRS|
|한국 중부|LRS|
|한국 남부|LRS|
|인도 중부|LRS|
|인도 서부|LRS|
|아랍에미리트 북부|LRS|
|일본 동부|LRS|
|일본 서부|LRS|
|동남아시아|LRS, ZRS|
|오스트레일리아 동부|LRS, ZRS|
|오스트레일리아 동남부|LRS|
|브라질 남부|LRS|


## <a name="next-steps"></a>다음 단계

Azure Data Lake Storage [블로그 알림에](https://www.microsoft.com)대 한 프리미엄 계층을 참조 하세요.