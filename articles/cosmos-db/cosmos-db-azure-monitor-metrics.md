---
title: Azure Monitor에서 Azure Cosmos DB 메트릭 가져오기
description: ''
author: SnehaGunda
ms.author: sngun
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/01/2019
ms.openlocfilehash: 762c910336fa2b50a46eda23cf66d8a7aa383c52
ms.sourcegitcommit: 6794fb51b58d2a7eb6475c9456d55eb1267f8d40
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70241234"
---
# <a name="monitor-and-debug-azure-cosmos-db-metrics-from-azure-monitor"></a>Azure Monitor에서 Azure Cosmos DB 메트릭을 모니터링 하 고 디버그 합니다.

Azure Monitor API에서 Azure Cosmos DB 메트릭을 볼 수 있습니다. Azure Monitor는 Azure Portal, REST API를 통해 액세스 하거나 PowerShell 또는 CLI를 사용 하 여 쿼리 하는 등 메트릭과 상호 작용 하는 여러 가지 방법을 제공 합니다. Azure Cosmos DB 메트릭은 기본적으로 1 분 간격으로 수집 되는 짧은 대기 시간 숫자 값입니다. 이러한 메트릭을 집계할 수도 있습니다. 이러한 메트릭은 실시간 시나리오를 지원할 수 있습니다.  

이 문서에서는 Azure Portal를 사용 하 여 Azure Monitor에서 볼 수 있는 다양 한 Azure Cosmos DB 메트릭을 설명 합니다. 일반적인 사용 사례와 Azure Cosmos DB 메트릭을 사용 하 여 이러한 문제를 분석 하 고 디버그 하는 방법에 관심이 있는 경우 [Azure Cosmos DB 문서에서 메트릭을 사용 하 여 모니터링 및 디버깅](use-metrics.md) 을 참조 하세요. 기존 Azure Cosmos 계정 중 하나를 사용 하 고 데이터베이스, 컨테이너, 지역, 요청 또는 작업 수준에서 다양 한 메트릭을 확인 합니다. 따라서 샘플 데이터가 포함 된 Azure Cosmos 계정이 있는지 확인 하 고 해당 데이터에 대 한 CRUD 작업을 수행 합니다.

## <a name="view-metrics-from-azure-portal"></a>Azure Portal에서 메트릭 보기

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.

1. 왼쪽 탐색 모음에서 **모니터** 를 선택 하 고 **메트릭**을 선택 합니다.

   ![Azure Monitor의 메트릭 창](./media/cosmos-db-azure-monitor-metrics/monitor-metrics-blade.png)

1. **메트릭** 창에서 리소스 > **선택** 하 > 필요한 **구독**및 **리소스 그룹**을 선택 합니다. **리소스 종류**에 대해 **Azure Cosmos DB 계정**을 선택 하 고, 기존 Azure Cosmos 계정 중 하나를 선택 하 고, **적용**을 선택 합니다. 

   ![메트릭을 볼 Cosmos DB 계정 선택](./media/cosmos-db-azure-monitor-metrics/select-cosmosdb-account.png)

1. 다음으로 사용 가능한 메트릭 목록에서 메트릭을 선택할 수 있습니다. 요청 단위, 저장소, 대기 시간, 가용성, Cassandra 및 기타에 특정 한 메트릭을 선택할 수 있습니다. 이 목록에서 사용할 수 있는 모든 메트릭에 대해 자세히 알아보려면이 문서의 [범주 별 메트릭](#metrics-by-category) 섹션을 참조 하세요. 이 예제에서는 **요청 단위** 및 **Avg** 를 집계 값으로 선택 하겠습니다. 

   이러한 세부 정보 외에도 메트릭의 **시간 범위** 와 **시간 세분성** 을 선택할 수 있습니다. 최대는 지난 30 일 동안의 메트릭을 볼 수 있습니다.  필터를 적용 하면 필터를 기반으로 차트가 표시 됩니다. 선택한 기간에 분당 사용한 평균 요청 단위 수를 볼 수 있습니다.  

   ![Azure Portal에서 메트릭 선택](./media/cosmos-db-azure-monitor-metrics/metric-types.png)

## <a name="add-filters-to-metrics"></a>메트릭에 필터 추가

특정 **CollectionName**, **DatabaseName**, **OperationType**, **Region**및 **StatusCode**에 의해 표시 되는 메트릭 및 차트를 필터링 할 수도 있습니다. 메트릭을 필터링 하려면 **필터 추가** 를 선택 하 고 **OperationType** 와 같은 필수 속성을 선택한 후 **쿼리**와 같은 값을 선택 합니다. 그러면 그래프는 선택한 기간에 대해 쿼리 작업에 사용 된 요청 단위를 표시 합니다. 저장 프로시저를 통해 실행 되는 작업은 기록 되지 않으므로 OperationType 메트릭 아래에서 사용할 수 없습니다.

![메트릭 세분성을 선택 하는 필터 추가](./media/cosmos-db-azure-monitor-metrics/add-metrics-filter.png)

**분할 적용** 옵션을 사용 하 여 메트릭을 그룹화 할 수 있습니다. 예를 들어 다음 이미지에 표시 된 것 처럼 작업 유형별 요청 단위를 그룹화 하 고 모든 작업의 그래프를 한 번에 볼 수 있습니다. 

![분할 필터 적용 추가](./media/cosmos-db-azure-monitor-metrics/apply-metrics-splitting.png)


## <a id="metrics-by-category"></a>범주별 메트릭

### <a name="request-metrics"></a>요청 메트릭
            
|메트릭 (메트릭 표시 이름)|Unit (집계 형식) |Description|차원| 시간 단위| 레거시 메트릭 매핑 | 사용법 |
|---|---|---|---| ---| ---| ---|
| TotalRequests (총 요청) | 개수 (개수) | 요청 수| DatabaseName, CollectionName, 지역, StatusCode| 모두 | TotalRequests, Http 2xx, Http 3xx, Http 400, Http 401, Internal Server error, Service Unavailable, Throttled Requests, Average Requests per Second | 상태 코드의 요청을 분 단위로 모니터링 하는 데 사용 됩니다. 초당 평균 요청 수를 가져오려면 개수 집계(분)를 사용한 후 60으로 나눕니다. |
| MetadataRequests (메타 데이터 요청) |개수 (개수) | 메타데이터 요청 수. Azure Cosmos DB는 각 계정에 대 한 시스템 메타 데이터 컨테이너를 유지 관리 하며,이를 통해 컬렉션, 데이터베이스 등의 기능 및 해당 구성을 무료로 열거할 수 있습니다. | DatabaseName, CollectionName, 지역, StatusCode| 모두| |메타데이터 요청으로 인한 제한을 모니터링하는 데 사용합니다.|
| MongoRequests (Mongo 요청) | 개수 (개수) | 생성된 Mongo 요청 수 | DatabaseName, CollectionName, 지역, CommandName, ErrorCode| 모두 |Mongo Query Request Rate, Mongo Update Request Rate, Mongo Delete Request Rate, Mongo Insert Request Rate, Mongo Count Request Rate| Mongo 요청 오류, 명령 유형별 사용량을 모니터링하는 데 사용합니다. |

### <a name="request-unit-metrics"></a>요청 단위 메트릭

|메트릭 (메트릭 표시 이름)|Unit (집계 형식)|Description|차원| 시간 단위| 레거시 메트릭 매핑 | 사용법 |
|---|---|---|---| ---| ---| ---|
| MongoRequestCharge (Mongo 요청 요금) | 개수 (합계) |사용된 Mongo 요청 단위| DatabaseName, CollectionName, 지역, CommandName, ErrorCode| 모두 |Mongo Query Request Charge, Mongo Update Request Charge, Mongo Delete Request Charge, Mongo Insert Request Charge, Mongo Count Request Charge| Mongo 리소스 RU(분)를 모니터링하는 데 사용합니다.|
| TotalRequestUnits (총 요청 단위)| 개수 (합계) | 사용된 요청 단위| DatabaseName, CollectionName, 지역, StatusCode |모두| TotalRequestUnits| 분 단위로 총 RU 사용량을 모니터링하는 데 사용합니다. 초당 사용한 평균 RU를 가져오려면 합계 집계(분)를 사용한 후 60으로 나눕니다.|
| ProvisionedThroughput (프로 비전 된 처리량)| 개수 (최대값) |컨테이너 세분성에서 프로 비전 된 처리량| DatabaseName, ContainerName| 5M| | 컨테이너 당 프로 비전 된 처리량을 모니터링 하는 데 사용 됩니다.|

### <a name="storage-metrics"></a>Storage 메트릭

|메트릭 (메트릭 표시 이름)|Unit (집계 형식)|Description|차원| 시간 단위| 레거시 메트릭 매핑 | 사용법 |
|---|---|---|---| ---| ---| ---|
| 사용 가능한 저장소 (사용 가능한 저장소) |바이트 (합계) | 지역별 5 분 단위로 보고 된 총 사용 가능한 저장소| DatabaseName, CollectionName, Region| 5M| 사용 가능한 스토리지| 사용 가능한 스토리지 용량을 모니터링하는 데 사용합니다(고정 스토리지 컬렉션에만 해당). 최소 단위는 5분이어야 합니다.| 
| DataUsage (데이터 사용) |바이트 (합계) |지역별 5 분 단위로 보고 된 총 데이터 사용량| DatabaseName, CollectionName, Region| 5M |데이터 크기 | 컨테이너 및 지역에서 총 데이터 사용량을 모니터링 하는 데 사용 되는 최소 세분성은 5 분입니다.|
| IndexUsage (인덱스 사용) | 바이트 (합계) |지역별 5 분 단위로 보고 된 총 인덱스 사용량| DatabaseName, CollectionName, Region| 5M| 인덱스 크기| 컨테이너 및 지역에서 총 데이터 사용량을 모니터링 하는 데 사용 되는 최소 세분성은 5 분입니다. |
| DocumentQuota (문서 할당량) | 바이트 (합계) | 지역별 5 분 단위로 보고 된 총 저장소 할당량입니다.| DatabaseName, CollectionName, Region| 5M |스토리지 용량| 컨테이너 및 지역에서 총 할당량을 모니터링 하는 데 사용 되는 최소 세분성은 5 분입니다.|
| DocumentCount (문서 수) | 개수 (합계) |지역별 5 분 단위로 보고 된 총 문서 수| DatabaseName, CollectionName, Region| 5M |문서 수|컨테이너 및 지역에서 문서 수를 모니터링 하는 데 사용 되는 최소 세분성은 5 분입니다.|

### <a name="latency-metrics"></a>대기 시간 메트릭

|메트릭 (메트릭 표시 이름)|Unit (집계 형식)|Description|차원| 시간 단위| 사용법 |
|---|---|---|---| ---| ---|
| ReplicationLatency (복제 대기 시간)| 밀리초 (최소, 최대, 평균) | 지역 사용 계정에 대한 원본 및 대상 지역의 P99 복제 대기 시간| SourceRegion, TargetRegion| 모두 | 지역에서 복제된 계정에 대한 두 지역 간 P99 복제 대기 시간을 모니터링하는 데 사용합니다. |


### <a name="availability-metrics"></a>가용성 메트릭

|메트릭 (메트릭 표시 이름) |Unit (집계 형식)|Description| 시간 단위| 레거시 메트릭 매핑 | 사용법 |
|---|---|---|---| ---| ---|
| ServiceAvailability (서비스 가용성)| 백분율 (최소, 최대) | 1시간 단위의 계정 요청 가용성| 1H | 서비스 가용성 | 전달 된 총 요청 비율을 나타냅니다. 요청은 상태 코드가 410, 500 또는 503인경 우 시스템 오류로 인해 실패한 것으로 간주됩니다. 시간 단위로 계정의 가용성을 모니터링하는 데 사용합니다. |


### <a name="cassandra-api-metrics"></a>Cassandra API 메트릭

|메트릭 (메트릭 표시 이름)|Unit (집계 형식)|설명|차원| 시간 단위| 사용법 |
|---|---|---|---| ---| ---|
| CassandraRequests (Cassandra 요청) | 개수 (개수) | 수행된 Cassandra API 요청 수| DatabaseName, CollectionName, ErrorCode, Region, OperationType, ResourceType| 모두| 분 단위로 Cassandra 요청을 모니터링하는 데 사용합니다. 초당 평균 요청 수를 가져오려면 개수 집계(분)를 사용한 후 60으로 나눕니다.|
| CassandraRequestCharges (Cassandra 요청 요금) | 개수 (합계, 최소값, 최대값, 평균) | Cassandra API 요청에 사용된 요청 단위| DatabaseName, CollectionName, Region, OperationType, ResourceType| 모두| Cassandra API 계정에서 분당 사용된 RU를 모니터링하는 데 사용합니다.|
| CassandraConnectionClosures (Cassandra 연결 클로저) |개수 (개수) |닫힌 Cassandra 연결 수| ClosureReason, Region| 모두 | 클라이언트와 Azure Cosmos DB Cassandra API 간 연결을 모니터링하는 데 사용합니다.|

## <a name="next-steps"></a>다음 단계

* [Azure Cosmos DB 계정 메트릭 창에서 메트릭 보기 및 모니터링](use-metrics.md)

* [Azure Cosmos DB 진단 로깅](logging.md)
