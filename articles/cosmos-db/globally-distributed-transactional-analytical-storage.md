---
title: Azure Cosmos 컨테이너에 대 한 전역 분산 트랜잭션 및 분석 저장소
description: Azure Cosmos 컨테이너에 대 한 트랜잭션 및 분석 저장소와 해당 구성 옵션에 대해 알아봅니다.
author: rimman
ms.author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/30/2019
ms.reviewer: sngun
ms.openlocfilehash: 27ca2102ee95273fbedd1a870e57d2ae3318e879
ms.sourcegitcommit: 6fe40d080bd1561286093b488609590ba355c261
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/01/2019
ms.locfileid: "71703388"
---
# <a name="globally-distributed-transactional-and-analytical-storage-for-azure-cosmos-containers"></a>Azure Cosmos 컨테이너에 대 한 전역 분산 트랜잭션 및 분석 저장소

Azure Cosmos 컨테이너는 내부적으로 트랜잭션 저장소 엔진과 업데이트 가능한 분석 저장소 엔진 이라는 두 가지 저장소 엔진으로 지원 됩니다. 저장소 엔진은 모두 더 빠른 업데이트를 위해 로그 구조 및 쓰기에 최적화 되어 있습니다. 그러나 각 항목은 서로 다르게 인코딩됩니다.

* **트랜잭션 저장소 엔진** – 빠른 트랜잭션 읽기 및 쿼리를 위해 행 기반 형식으로 인코딩됩니다.

* **분석 저장소 엔진** -빠른 분석 쿼리 및 검색을 위해 칼럼 형식으로 인코딩됩니다.

![저장소 엔진 및 Azure Cosmos DB API 매핑](./media/globally-distributed-transactional-analytical-storage/storage-engines-api-mapping.png)

트랜잭션 저장소 엔진은 로컬 Ssd에서 지원 되는 반면 분석 저장소는 저렴 한 클러스터 SSD 저장소에 저장 됩니다. 다음 표에서는 트랜잭션과 분석 저장소 간의 주요 차이점을 캡처합니다.


|기능  |트랜잭션 저장소  |분석 저장소 |
|---------|---------|---------|
|Azure Cosmos 컨테이너 당 최대 저장소 |   제한 없음      |    제한 없음     |
|논리적 파티션 키 당 최대 저장소   |   10GB      |   제한 없음      |
|저장소 인코딩  |   내부 형식을 사용 하는 행 기반입니다.   |   Apache Parquet format을 사용 하는 열 기반. |
|저장소 위치 |   로컬/클러스터 간 Ssd에서 지원 되는 복제 된 저장소입니다. |  저렴 한 원격/오프 클러스터 Ssd에서 지원 되는 복제 된 저장소입니다.       |
|영속성  |    99.99999 (7-9 s)     |  99.99999 (7-9 s)       |
|데이터에 액세스 하는 Api  |   SQL, MongoDB, Cassandra, Gremlin, Tables 및 Etcd가 있습니다.       | Apache Spark         |
|보존 (TTL (time-to-live) 또는 TTL)   |  @No__t-0 속성을 사용 하 여 Azure Cosmos 컨테이너에 구성 된 정책 기반입니다.       |   @No__t-0 속성을 사용 하 여 Azure Cosmos 컨테이너에 구성 된 정책 기반입니다.      |
|GB당 가격    |   $0.25/GB      |  $0.02/GB       |
|저장소 트랜잭션 가격    | 프로 비전 된 처리량은 시간당 청구와 함께 100 r u/초 단위로 요금이 $0.008 부과 됩니다.        |  소비 기반 처리량은 1만 쓰기 트랜잭션의 경우 $0.05로 청구 되 고 1만 읽기 트랜잭션에는 $0.004로 청구 됩니다.       |

## <a name="benefits-of-transactional-and-analytical-storage"></a>트랜잭션 및 분석 저장소의 이점

### <a name="no-etl-operations"></a>ETL 작업 없음

일반적인 분석 파이프라인은 계산 및 저장소 계층에 대 한 ETL (추출-변환-로드) 작업을 필요로 하는 여러 단계로 복잡 합니다. 이를 통해 복잡 한 데이터 처리 아키텍처가 생성 됩니다. 저장소 및 계산의 여러 단계에 대 한 높은 비용을 의미 하 고, 다양 한 저장소 및 계산 단계 간에 대량 데이터를 전송 하기 때문에 대기 시간이 깁니다.  

Azure Cosmos DB를 사용 하 여 ETL 작업을 수행할 수 있는 오버 헤드가 없습니다. 각 Azure Cosmos 컨테이너는 트랜잭션 및 분석 저장소 엔진 둘 다에서 지원 되며, 트랜잭션 및 분석 저장소 엔진 간의 데이터 전송은 데이터베이스 엔진 내에서 수행 되 고 네트워크 홉 없이 수행 됩니다. 결과 대기 시간 및 비용은 기존 분석 솔루션에 비해 훨씬 더 낮습니다. 그리고 트랜잭션 및 분석 워크 로드 모두에 대해 전역적으로 분산 된 단일 저장소 시스템을 얻을 수 있습니다.  

### <a name="store-multiple-versions-update-and-query-the-analytical-storage"></a>여러 버전 저장, 업데이트 및 분석 저장소 쿼리

분석 저장소는 완전히 업데이트 가능 하며 Azure Cosmos 컨테이너에서 발생 한 모든 트랜잭션 업데이트의 전체 버전 기록을 포함 합니다.

트랜잭션 저장소에 대 한 모든 업데이트는 30 초 이내에 분석 저장소에 표시 되도록 보장 됩니다. 

### <a name="globally-distributed-multi-master-analytical-storage"></a>전역으로 분산 되 고 다중 마스터 분석 저장소

Azure Cosmos 계정이 단일 지역으로 범위가 지정 된 경우 컨테이너의 트랜잭션 및 분석 저장소에 저장 된 데이터도 단일 지역으로 범위가 지정 됩니다. 반면, Azure Cosmos 계정이 전역적으로 분산 된 경우 컨테이너에 저장 된 데이터도 전역적으로 분산 됩니다.

여러 쓰기 영역으로 구성 된 Azure Cosmos 계정에 대해 컨테이너 (트랜잭션 및 분석 저장소 둘 다)에 대 한 쓰기는 항상 로컬 지역에서 수행 되므로 속도가 빠릅니다.

단일 쓰기 지역 (단일 마스터) 또는 다중 쓰기 영역 (다중 마스터 라고도 함)에 관계 없이 단일 또는 다중 지역 Azure Cosmos 계정에 대해 트랜잭션 및 분석 읽기/쿼리가 모두 지정 된 지역에서 로컬로 수행 됩니다.

### <a name="performance-isolation-between-transactional-and-analytical-workloads"></a>트랜잭션 및 분석 워크 로드 간의 성능 격리

지정 된 지역에서 트랜잭션 워크 로드는 컨테이너의 트랜잭션/행 저장소에 대해 작동 합니다. 반면에 분석 워크 로드는 컨테이너의 분석/열 저장소에 대해 작동 합니다. 두 저장소 엔진은 독립적으로 작동 하며 워크 로드 간에 엄격한 성능 격리를 제공 합니다.

트랜잭션 워크 로드는 프로 비전 된 처리량 (RUs)을 사용 합니다. 트랜잭션 워크 로드와 달리 분석 작업 처리량은 실제 사용량을 기준으로 합니다. 분석 워크 로드는 주문형 리소스를 사용 합니다.

### <a name="on-demand-snapshots-and-time-travel-analytics"></a>주문형 스냅숏 및 시간 이동 분석

언제 든 지 컨테이너에서 `CreateSnapshot (name, timestamp)` 명령을 호출 하 여 Azure Cosmos 컨테이너의 분석 저장소에 저장 된 데이터의 스냅숏을 만들 수 있습니다. 스냅숏은 컨테이너에서 수행 된 업데이트의 기록에서 "책갈피"로 지정 됩니다.

![주문형 스냅숏 및 시간 이동 분석](./media/globally-distributed-transactional-analytical-storage/ondemand-analytical-data-snapshots.png)

스냅숏을 만들 때 이름 외에 업데이트 기록의 컨테이너 상태를 정의 하는 타임 스탬프를 지정할 수 있습니다. 그런 다음 스냅숏 데이터를 Spark로 로드 하 고 쿼리를 수행할 수 있습니다.

현재는 컨테이너에서 언제 든 지 스냅숏을 만들 수 있습니다. 일정 또는 사용자 지정 정책에 따라 자동으로 스냅숏을 생성 하는 기능은 아직 지원 되지 않습니다.

### <a name="configure-and-tier-data-between-transactional-and-analytical-storage-independently"></a>트랜잭션 및 분석 저장소 간 데이터를 독립적으로 구성 및 계층 구성

시나리오에 따라 두 개의 저장소 엔진을 개별적으로 사용 하거나 사용 하지 않도록 설정할 수 있습니다. 다음은 각 시나리오에 대 한 구성입니다.

|시나리오 |트랜잭션 저장소 설정  |분석 저장소 설정 |
|---------|---------|---------|
|분석 워크 로드를 독점적으로 실행 (무한 보존) |  DefaultTimeToLive = 0       |  ColumnStoreTimeToLive =-1       |
|트랜잭션 워크 로드를 단독으로 실행 (무한 보존)  |   DefaultTimeToLive =-1      |  ColumnStoreTimeToLive = 0       |
|트랜잭션 및 분석 워크 로드 실행 (무한 보존)   |   DefaultTimeToLive =-1      | ColumnStoreTimeToLive =-1        |
|트랜잭션 및 분석 워크 로드를 실행 합니다 (다른 보존 간격 (저장소 계층화 라고도 함)).  |  DefaultTimeToLive = <Value1>       |     ColumnStoreTimeToLive = <Value2>    |

1. **분석 워크 로드 전용으로 컨테이너 구성 (무한 보존 포함)**

   분석 워크 로드에 대 한 Azure Cosmos 컨테이너를 독점적으로 구성할 수 있습니다. 이 구성에는 트랜잭션 저장소에 대 한 비용을 지불할 필요가 없는 장점이 있습니다. 분석 워크 로드에만 컨테이너를 사용 하는 경우 Cosmos 컨테이너에서 0을 0으로 @no__t 설정 하 여 트랜잭션 저장소를 사용 하지 않도록 설정 하 고,-1을-1로 @no__t 설정 하 여 영구 보존이 있는 분석 저장소를 사용 하도록 설정할 수 있습니다.

   ![무기한 보존을 포함 하는 분석 워크 로드](./media/globally-distributed-transactional-analytical-storage/analytical-workload-configuration.png)

1. **트랜잭션 워크 로드 전용으로 컨테이너 구성 (무한 보존 포함)**

   Azure Cosmos 컨테이너는 트랜잭션 워크 로드에 대해서만 구성할 수 있습니다. 컨테이너에서-0을 0으로 @no__t 설정 하 여 분석 저장소를 사용 하지 않도록 설정 하 고,-1을-1로 @no__t 설정 하 여 무한 보존이 있는 분석 저장소를 사용 하도록 설정할 수 있습니다.

   ![영구 보존이 있는 트랜잭션 워크 로드](./media/globally-distributed-transactional-analytical-storage/transactional-workload-configuration.png)

1. **트랜잭션 및 분석 워크 로드 모두에 대해 컨테이너 구성 (무한 보존 포함)**

   트랜잭션 및 분석 워크 로드에 대 한 Azure Cosmos 컨테이너를 전체 성능 격리로 구성할 수 있습니다. @No__t-0을-1로 설정 하 여 분석 저장소를 사용 하도록 설정 하 고-1을-1로 @no__t 설정 하 여 영구 보존이 있는 트랜잭션 저장소를 사용 하도록 설정할 수 있습니다.

   ![무기한 보존을 포함 하는 트랜잭션 및 분석 워크 로드](./media/globally-distributed-transactional-analytical-storage/analytical-transactional-configuration-infinite-retention.png)

1. **저장소 계층화를 사용 하 여 트랜잭션 및 분석 작업에 대 한 컨테이너 구성**

   서로 다른 보존 간격을 사용 하 여 트랜잭션 및 분석 워크 로드 간 전체 성능 격리를 통해 Azure Cosmos 컨테이너를 구성할 수 있습니다. Azure Cosmos DB는 분석 저장소가 트랜잭션 저장소 보다 긴 기간 동안 항상 유지 되도록 적용 합니다.

   @No__t-0을 < 값 1 > 설정 하 고 `ColumnStoreTimeToLive`을 < 값 2 >로 설정 하 여 분석 저장소를 사용 하도록 설정 하 여 트랜잭션 저장소에서 무한 보존을 사용 하도록 설정할 수 있습니다. Azure Cosmos DB은 < 값 2 >를 < 값 1 > 보다 항상 큰지를 적용 합니다.

   ![저장소 계층화를 사용 하는 트랜잭션 및 분석 워크 로드](./media/globally-distributed-transactional-analytical-storage/analytical-transactional-configuration-specified-retention.png)

## <a name="next-steps"></a>다음 단계

* [Azure Cosmos DB의 ttl (Time to live)](time-to-live.md)
