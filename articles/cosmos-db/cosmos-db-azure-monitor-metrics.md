---
title: Azure Monitor에서 Azure Cosmos DB 메트릭 가져오기
description: ''
author: SnehaGunda
ms.author: sngun
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/18/2019
ms.openlocfilehash: 2eb61a6b9afa3cabf1733be120dfbdacb7de4534
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/20/2019
ms.locfileid: "67276796"
---
# <a name="monitor-and-debug-azure-cosmos-db-metrics-from-azure-monitor"></a>모니터링 하 고 Azure Monitor에서 Azure Cosmos DB 메트릭 디버그

Azure Monitor API에서 Azure Cosmos DB 메트릭을 볼 수 있습니다. Azure 포털을 포함 하 여, REST API를 통한 액세스 또는 쿼리 하기 메트릭을 사용 하 여 상호 작용 하는 여러 방법을 제공 하는 azure Monitor PowerShell 또는 CLI를 사용 하 여 합니다. Azure Cosmos DB 메트릭은 기본적으로 1 분 빈도로 수집 지연율이 낮은 숫자 값, 이러한 메트릭을 집계할 수 있습니다. 이러한 메트릭을 실시간 시나리오를 지원할 수 있습니다.  

이 문서에서는 다양 한 Azure Cosmos DB 메트릭을 볼 수 있습니다 Azure portal을 사용 하 여 Azure Monitor에서 설명 합니다. 관심이 있다면 일반적인 사용 사례 및 참조 문제를 분석 하 고 이러한 디버그 다시 Azure Cosmos DB 메트릭을 사용 하는 방법 [Monitor 및 Azure Cosmos DB에서 메트릭 사용 하 여 디버그](use-metrics.md) 문서. 기존 Azure Cosmos 계정 중 하나를 사용 하 여 한 데이터베이스, 컨테이너, 지역, 요청 또는 작업 수준에서 다양 한 메트릭을 확인 합니다. 따라서 샘플 데이터를 사용 하 여 Azure Cosmos 계정이 고 해당 데이터에 대해 CRUD 작업을 수행 해야 합니다.

## <a name="view-metrics-from-azure-portal"></a>Azure portal에서 메트릭 보기

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.

1. 선택 **모니터** 왼쪽 탐색 모음에서 선택한 **메트릭**합니다.

   ![Azure Monitor의 메트릭 창](./media/cosmos-db-azure-monitor-metrics/monitor-metrics-blade.png)

1. **메트릭을** 창 > **리소스를 선택** > 필요한 선택 **구독**, 및 **리소스 그룹**합니다. 에 대 한 합니다 **리소스 종류**를 선택 **Azure Cosmos DB 계정**에 기존 Azure Cosmos 계정 중 하나를 선택 합니다 **적용**합니다. 

   ![Cosmos DB 계정 메트릭을 보려면를 선택 합니다.](./media/cosmos-db-azure-monitor-metrics/select-cosmosdb-account.png)

1. 다음 사용 가능한 메트릭 목록에서 메트릭을 선택할 수 있습니다. 요청 단위, 저장소, 대기 시간, 가용성, Cassandra 및 다른 관련 메트릭을 선택할 수 있습니다. 모든 사용 가능한 메트릭이이 목록에 대 한 세부 정보에 알아보려면 합니다 [범주별으로 메트릭을](#metrics-by-category) 이 문서의 섹션입니다. 이 예제에서는 선택 해 보겠습니다 **요청 단위** 하 고 **Avg** 집계 값으로. 

   이러한 세부 정보 외에도 선택할 수도 있습니다는 **시간 범위** 하 고 **시간 세분성** 메트릭의 합니다. 최대, 지난 30 일에 대 한 메트릭을 볼 수 있습니다.  필터를 적용 하면 차트에 필터를 기준으로 표시 됩니다. 선택한 기간에 대 한 분당 사용 된 요청 단위 평균 수를 볼 수 있습니다.  

   ![Azure portal에서 메트릭을 선택합니다](./media/cosmos-db-azure-monitor-metrics/metric-types.png)

## <a name="add-filters-to-metrics"></a>메트릭에 필터 추가

또한 메트릭 및 특정에서 표시 된 차트를 필터링 할 수 있습니다 **CollectionName**를 **DatabaseName**에 **OperationType**를 **지역**, 및 **StatusCode**합니다. 필터링 하려면 메트릭을 선택 합니다 **필터 추가** 와 같은 필수 속성을 선택 하 고 **OperationType** 와 같은 값을 선택 하 고 **쿼리**합니다. 다음 그래프는 선택한 기간에 대 한 쿼리 작업에 사용 되는 요청 단위가 표시 됩니다. OperationType 메트릭을에서 사용할 수 있도록 저장 프로시저를 통해 실행 되는 작업이 기록 되지 않습니다.

![메트릭 세분성을 선택할 필터 추가](./media/cosmos-db-azure-monitor-metrics/add-metrics-filter.png)

사용 하 여 메트릭을 그룹화 할 수 있습니다 합니다 **분할 적용** 옵션입니다. 예를 들어, 작업 유형 초당 요청 단위를 그룹화 하 고 다음 그림에 나와 있는 것 처럼에 한 번에 모든 작업에 대 한 그래프를 볼 수 있습니다. 

![추가 분할 필터 적용](./media/cosmos-db-azure-monitor-metrics/apply-metrics-splitting.png)


## <a id="metrics-by-category"></a>범주별으로 메트릭

### <a name="request-metrics"></a>메트릭 요청
            
|메트릭 (메트릭 표시 이름)|단위 (집계 형식) |설명|차원| 시간 단위| 레거시 메트릭 매핑 | 사용 현황 |
|---|---|---|---| ---| ---| ---|
| TotalRequests (총 요청 수) | 개수 (개수) | 요청 수| DatabaseName, CollectionName, 지역, StatusCode| 모두 | TotalRequests, Http 2xx, Http 3xx, Http 400, Http 401, Internal Server error, Service Unavailable, Throttled Requests, Average Requests per Second | 분 단위로 상태 코드별 요청, 컬렉션을 모니터링하는 데 사용합니다. 초당 평균 요청 수를 가져오려면 개수 집계(분)를 사용한 후 60으로 나눕니다. |
| MetadataRequests (메타 데이터 요청 수) |개수 (개수) | 메타데이터 요청 수. Azure Cosmos DB 컬렉션, 데이터베이스 등을 열거할 수 있도록 각 계정에 대 한 시스템 메타 데이터 컬렉션을 유지 하 고 해당 구성을 무료로 제공 합니다. | DatabaseName, CollectionName, 지역, StatusCode| 모두| |메타데이터 요청으로 인한 제한을 모니터링하는 데 사용합니다.|
| MongoRequests (Mongo 요청 수) | 개수 (개수) | 생성된 Mongo 요청 수 | DatabaseName, CollectionName, 지역, CommandName, ErrorCode| 모두 |Mongo Query Request Rate, Mongo Update Request Rate, Mongo Delete Request Rate, Mongo Insert Request Rate, Mongo Count Request Rate| Mongo 요청 오류, 명령 유형별 사용량을 모니터링하는 데 사용합니다. |

### <a name="request-unit-metrics"></a>요청 단위 메트릭

|메트릭 (메트릭 표시 이름)|단위 (집계 형식)|설명|차원| 시간 단위| 레거시 메트릭 매핑 | 사용 현황 |
|---|---|---|---| ---| ---| ---|
| MongoRequestCharge (Mongo 요청 요금) | 수 (합계) |사용된 Mongo 요청 단위| DatabaseName, CollectionName, 지역, CommandName, ErrorCode| 모두 |Mongo Query Request Charge, Mongo Update Request Charge, Mongo Delete Request Charge, Mongo Insert Request Charge, Mongo Count Request Charge| Mongo 리소스 RU(분)를 모니터링하는 데 사용합니다.|
| TotalRequestUnits (총 요청 단위)| 수 (합계) | 사용된 요청 단위| DatabaseName, CollectionName, 지역, StatusCode |모두| TotalRequestUnits| 분 단위로 총 RU 사용량을 모니터링하는 데 사용합니다. 초당 사용한 평균 RU를 가져오려면 합계 집계(분)를 사용한 후 60으로 나눕니다.|
| ProvisionedThroughput (프로 비전 된 처리량)| 수 (최대) |컬렉션 단위로 프로비전된 처리량| DatabaseName, CollectionName| 5M| | 컬렉션당 프로비전된 처리량을 모니터링하는 데 사용합니다.|

### <a name="storage-metrics"></a>Storage 메트릭

|메트릭 (메트릭 표시 이름)|단위 (집계 형식)|설명|차원| 시간 단위| 레거시 메트릭 매핑 | 사용 현황 |
|---|---|---|---| ---| ---| ---|
| AvailableStorage (사용 가능한 저장소) |바이트 (합계) | 지역당 5 분 간격 보고 사용 가능한 총 저장소| DatabaseName, CollectionName, Region| 5M| 사용 가능한 스토리지| 사용 가능한 스토리지 용량을 모니터링하는 데 사용합니다(고정 스토리지 컬렉션에만 해당). 최소 단위는 5분이어야 합니다.| 
| DataUsage (데이터 사용량) |바이트 (합계) |지역당 5 분 간격 보고 된 총 데이터 사용| DatabaseName, CollectionName, Region| 5M |데이터 크기 | 컬렉션 및 지역의 총 데이터 사용량을 모니터링하는 데 사용합니다. 최소 단위는 5분이어야 합니다.|
| IndexUsage (인덱스 사용) | 바이트 (합계) |지역당 5 분 간격 보고 된 총 인덱스 사용량| DatabaseName, CollectionName, Region| 5M| 인덱스 크기| 컬렉션 및 지역의 총 데이터 사용량을 모니터링하는 데 사용합니다. 최소 단위는 5분이어야 합니다. |
| DocumentQuota (문서 할당량) | 바이트 (합계) | 총 저장소 할당량 지역당 5 분 간격 보고 합니다.| DatabaseName, CollectionName, Region| 5M |저장소 용량| 컬렉션 및 지역의 총 할당량을 모니터링하는 데 사용합니다. 최소 단위는 5분이어야 합니다.|
| DocumentCount (문서 수) | 수 (합계) |지역당 5 분 간격 보고 된 총 문서 수| DatabaseName, CollectionName, Region| 5M |문서 수|컬렉션 및 지역의 문서 수를 모니터링하는 데 사용합니다. 최소 단위는 5분이어야 합니다.|

### <a name="latency-metrics"></a>대기 시간 메트릭

|메트릭 (메트릭 표시 이름)|단위 (집계 형식)|설명|차원| 시간 단위| 사용 현황 |
|---|---|---|---| ---| ---|
| ReplicationLatency (복제 대기 시간)| 시간 (밀리초) (최소, 최대, 평균) | 지역 사용 계정에 대한 원본 및 대상 지역의 P99 복제 대기 시간| SourceRegion, TargetRegion| 모두 | 지역에서 복제된 계정에 대한 두 지역 간 P99 복제 대기 시간을 모니터링하는 데 사용합니다. |


### <a name="availability-metrics"></a>가용성 메트릭

|메트릭 (메트릭 표시 이름) |단위 (집계 형식)|설명| 시간 단위| 레거시 메트릭 매핑 | 사용 현황 |
|---|---|---|---| ---| ---|
| ServiceAvailability (서비스 가용성)| % (최소, 최대) | 1시간 단위의 계정 요청 가용성| 1H | 서비스 가용성 | 요청을 전달 하는 합계의 백분율을 나타냅니다. 요청은 상태 코드가 410, 500 또는 503인경 우 시스템 오류로 인해 실패한 것으로 간주됩니다. 시간 단위로 계정의 가용성을 모니터링하는 데 사용합니다. |


### <a name="cassandra-api-metrics"></a>Cassandra API 메트릭

|메트릭 (메트릭 표시 이름)|단위 (집계 형식)|설명|차원| 시간 단위| 사용 현황 |
|---|---|---|---| ---| ---|
| CassandraRequests (Cassandra 요청) | 개수 (개수) | 수행된 Cassandra API 요청 수| DatabaseName, CollectionName, ErrorCode, Region, OperationType, ResourceType| 모두| 분 단위로 Cassandra 요청을 모니터링하는 데 사용합니다. 초당 평균 요청 수를 가져오려면 개수 집계(분)를 사용한 후 60으로 나눕니다.|
| CassandraRequestCharges (Cassandra 요청 요금) | 개수 (Sum, Min, Max, Avg) | Cassandra API 요청에 사용된 요청 단위| DatabaseName, CollectionName, Region, OperationType, ResourceType| 모두| Cassandra API 계정에서 분당 사용된 RU를 모니터링하는 데 사용합니다.|
| CassandraConnectionClosures (Cassandra 연결 Closure) |개수 (개수) |닫힌 Cassandra 연결 수| ClosureReason, Region| 모두 | 클라이언트와 Azure Cosmos DB Cassandra API 간 연결을 모니터링하는 데 사용합니다.|

## <a name="next-steps"></a>다음 단계

* [Azure Cosmos DB 계정 메트릭 창에서 보기 및 모니터링 메트릭](use-metrics.md)

* [Azure Cosmos db에서 진단 로깅](logging.md)
