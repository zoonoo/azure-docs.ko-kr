---
title: Azure Cosmos DB에서 다중 지역 배포에 대해 비용 최적화
description: 이 문서에서는 Azure Cosmos DB에서 다중 지역 배포 비용을 관리하는 방법을 설명합니다.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/23/2020
ms.openlocfilehash: 010ca40f4f3aacd6353aecd150e944672cc09066
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93097514"
---
# <a name="optimize-multi-region-cost-in-azure-cosmos-db"></a>Azure Cosmos DB에서 여러 지역 비용 최적화
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

언제든지 Azure Cosmos 계정에서 지역을 추가 및 제거할 수 있습니다. 다양한 Azure Cosmos 데이터베이스 및 컨테이너에 대해 구성하는 처리량은 계정과 연결된 각 지역에 예약됩니다. 시간당 프로 비전 된 처리량이 Azure Cosmos 계정에 대 한 모든 데이터베이스 및 컨테이너에서 구성 된 r u/초의 합계이 `T` 고, 데이터베이스 계정과 연결 된 azure 지역 수는 이며 `N` , 지정 된 시간 동안 Cosmos 계정에 대해 프로 비전 된 총 처리량은와 같습니다 `T x N RU/s` .

단일 쓰기 지역으로 프로비전된 처리량 비용은 100RU당 $0.008/시간이며, 여러 쓰기 가능 지역으로 프로비전된 처리량 비용은 100RU당 $0.016/시간입니다. 자세한 내용은 Azure Cosmos DB [가격 책정 페이지](https://azure.microsoft.com/pricing/details/cosmos-db/)를 참조하세요.

## <a name="costs-for-multiple-write-regions"></a>여러 쓰기 지역에 대한 비용

다중 지역 쓰기 시스템에서 쓰기 작업에 사용 가능한 순 RUs는 `N` `N` 가 쓰기 영역의 수를 늘리는 시간을 늘립니다. 단일 지역 쓰기와 달리 모든 지역은 이제 쓰기 가능 하 고 충돌 해결을 지원 합니다. 비용 계획 관점에서 `M` 전 세계의 쓰기를 수행 하려면 `RUs` 컨테이너 또는 데이터베이스 수준에서 M을 프로 비전 해야 합니다. 원하는 수의 지역을 추가하고 쓰기에 사용하여 전 세계적으로 `M` RU에 상당하는 쓰기를 수행할 수 있습니다.

### <a name="example"></a>예제

단일 지역 쓰기를 위해 구성 되 고 1, 000 r u/s로 프로 비전 된 1 TB의 데이터를 저장 하는 경우 미국 서 부에 구성 된 컨테이너가 있다고 가정 합니다. 동일한 저장소 및 처리량을 사용 하 여 지역, 미국 동부를 추가 하 고 앱에서 두 지역의 컨테이너에 쓰는 기능을 원하는 경우를 가정해 보겠습니다. 총 월간 청구 금액은 다음과 같습니다(1개월은 31일로 가정).

|**Item**|**사용량(월간)**|**비용**|**월간 비용**|
|----|----|----|----|
|미국 서 부의 컨테이너에 대 한 처리량 청구서 (단일 쓰기 지역) |10K r u/초 * 24 시간 * 31 일 |시간당 100 $0.008/초 |$584.06 |
|2 개 지역에 있는 컨테이너에 대 한 처리량 청구-미국 서 부 & 미국 동부 (여러 쓰기 지역) |2 * 10K r u/초 * 24 시간 * 31 일|100RU당 $0.016/시간 |$2336.26 |
|미국 서부의 컨테이너에 대한 스토리지 청구 |1TB (또는 1024 GB) |$0.25/GB |$256 |
|2 지역에 대 한 저장소 청구서-미국 서 부 & 미국 동부 |2 * 1TB (또는 3072 g b) |$0.25/GB |$768 |

## <a name="improve-throughput-utilization-on-a-per-region-basis"></a>지역별 처리량 사용률 개선

예를 들어 하나 이상의 미달 사용 읽기 지역에서 비효율적으로 사용 하는 경우 읽기 지역에서 변경 피드를 사용 하 여 읽기 지역에서 RUs를 최대한 활용 하거나 과도 하 게 사용 되는 경우 다른 보조로 이동 하는 단계를 수행할 수 있습니다. 먼저 쓰기 지역에서 프로 비전 된 처리량 (RUs)을 최적화 해야 합니다. 과도 한 쿼리를 사용 하는 경우에도 매우 큰 쿼리를 사용 하는 것은 어려울 수 있습니다. 전반적으로, 지역에서 소비 된 처리량을 모니터링 하 고 주문형 지역을 추가 하거나 제거 하 여 읽기 및 쓰기 처리량을 확장 하 고, 동일한 지역에 배포 된 앱의 대기 시간에 미치는 영향을 이해 해야 합니다.

## <a name="next-steps"></a>다음 단계

이제 다음 문서를 사용하여 Azure Cosmos DB의 비용 최적화에 대해 좀 더 자세히 알아볼 수 있습니다.

* [개발 및 테스트용으로 최적화](optimize-dev-test.md)에 대한 자세한 정보
* [Azure Cosmos DB 요금 청구 이해](understand-your-bill.md)에 대한 자세한 정보
* [처리량 비용 최적화](optimize-cost-throughput.md)에 대한 자세한 정보
* [스토리지 비용 최적화](optimize-cost-storage.md)에 대한 자세한 정보
* [읽기 및 쓰기 비용 최적화](optimize-cost-reads-writes.md)에 대한 자세한 정보
* [쿼리 비용 최적화](./optimize-cost-reads-writes.md)에 대한 자세한 정보