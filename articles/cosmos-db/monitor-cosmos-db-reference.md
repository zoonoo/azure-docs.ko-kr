---
title: Azure 코스모스 DB 모니터링 데이터 참조 | 마이크로 소프트 문서
description: Azure Cosmos DB의 데이터 모니터링을 위한 로그 및 메트릭 참조입니다.
author: bwren
services: azure-monitor
ms.service: azure-monitor
ms.topic: reference
ms.date: 11/11/2019
ms.author: bwren
ms.custom: subject-monitoring
ms.subservice: logs
ms.openlocfilehash: d243224192b5761af45d387690f5fb41b84481e6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588725"
---
# <a name="azure-cosmos-db-monitoring-data-reference"></a>Azure Cosmos DB 모니터링 데이터 참조
이 문서에서는 Azure Cosmos DB의 성능 및 가용성을 분석하기 위해 수집된 로그 및 메트릭 데이터에 대한 참조를 제공합니다. Azure Cosmos DB에 대한 모니터링 데이터 수집 및 분석에 대한 자세한 내용은 [코스모스 DB 모니터링을](monitor-cosmos-db.md) 참조하십시오.


## <a name="resource-logs"></a>리소스 로그
다음 표에는 Azure Cosmos DB 리소스 로그가 Azure 모니터 로그 또는 Azure 저장소에서 수집될 때의 속성을 나열합니다. Azure 모니터 로그에서 Microsoft의 **리소스 공급자** 값을 사용 하 고 **AzureDiagnostics** 테이블에서 수집 *됩니다. 문서DB*. 

| Azure Storage 필드 또는 속성 | Azure 모니터 로그 속성 | 설명 |
| --- | --- | --- |
| **시간** | **TimeGenerated** | 작업이 발생한 날짜 및 시간(UTC)입니다. |
| **Resourceid** | **리소스** | 로그가 사용하도록 설정된 Azure Cosmos DB 계정입니다.|
| **범주** | **범주** | Azure 코스모스 DB 로그, **DataPlaneRequests,** **MongoRequests,** **쿼리런타임 통계,** **파티션키통계,** **파티션키루소비,** **ControlPlaneRequests는** 사용 가능한 로그 유형입니다. |
| **작업이름** | **작업 이름** | 작업의 이름입니다. 이 값은 Create, Update, Read, ReadFeed, Delete, Replace, Execute, SqlQuery, Query, JSQuery, Head, HeadFeed 또는 Upsert 작업 중 하나일 수 있습니다.   |
| **속성** | 해당 없음 | 이 필드의 내용은 다음 행에 설명되어 있습니다. |
| **Activityid** | **activityId_g** | 기록된 작업의 고유 GUID입니다. |
| **Useragent** | **userAgent_s** | 요청을 수행하는 클라이언트 사용자 에이전트를 지정하는 문자열입니다. 형식은 {사용자 에이전트 이름}/{버전}입니다.|
| **요청리소스 유형** | **requestResourceType_s** | 액세스한 리소스 유형입니다. 이 값은 Database, Container, Document, Attachment, User, Permission, StoredProcedure, Trigger, UserDefinedFunction 또는 Offer 같은 리소스 유형 중 하나일 수 있습니다. |
| **statusCode** | **statusCode_s** | 작업의 응답 상태입니다. |
| **requestResourceId** | **Resourceid** | 요청에 관련된 리소스 ID입니다. 값은 수행된 작업에 따라 databaseRid, collectionRid 또는 documentRid를 가리킬 수 있습니다.|
| **clientIpAddress** | **clientIpAddress_s** | 클라이언트의 IP 주소입니다. |
| **requestCharge** | **requestCharge_s** | 작업에서 사용된 RU 수입니다. |
| **collectionRid** | **collectionId_s** | 컬렉션의 고유 ID입니다.|
| **기간** | **duration_s** | 작업 기간(밀리초)입니다. |
| **requestLength** | **requestLength_s** | 요청 길이(바이트)입니다. |
| **responseLength** | **responseLength_s** | 응답 길이(바이트)입니다.|
| **resourceTokenUserRid** | **resourceTokenUserRid_s** | 이 값은 인증에 [리소스 토큰](https://docs.microsoft.com/azure/cosmos-db/secure-access-to-data#resource-tokens)이 사용될 경우 비어 있지 않습니다. 값은 사용자의 리소스 ID를 가리킵니다. |

모든 Azure Monitor 로그 범주 및 연결된 스키마에 대한 링크 목록은 [Azure Monitor 로그 범주 및 스키마](../azure-monitor/platform/diagnostic-logs-schema.md)를 참조하십시오. 

## <a name="metrics"></a>메트릭
다음 표에는 Azure CosmOS DB에 대해 수집된 플랫폼 메트릭이 나열됩니다. 모든 메트릭은 네임스페이스 **코스모스 DB 표준 메트릭에 저장됩니다.**

모든 Azure 모니터 지원 메트릭 목록(CosmosDB 포함)은 [Azure 모니터 지원 메트릭을](../azure-monitor/platform/metrics-supported.md)참조하십시오. 

#### <a name="request-metrics"></a>요청 메트릭
            
|메트릭(메트릭 표시 이름)|단위(집계 유형) |설명|차원| 시간 단위| 레거시 메트릭 매핑 | 사용 |
|---|---|---|---| ---| ---| ---|
| 총 요청(총 요청) | 카운트(카운트) | 요청 수| DatabaseName, CollectionName, 지역, StatusCode| 모두 | TotalRequests, Http 2xx, Http 3xx, Http 400, Http 401, Internal Server error, Service Unavailable, Throttled Requests, Average Requests per Second | 상태 코드당 요청을 모니터링하는 데 사용됩니다. 초당 평균 요청 수를 가져오려면 개수 집계(분)를 사용한 후 60으로 나눕니다. |
| 메타데이터 요청(메타데이터 요청) |카운트(카운트) | 메타데이터 요청 수. Azure Cosmos DB는 각 계정에 대해 시스템 메타데이터 컨테이너를 유지 관리하여 컬렉션, 데이터베이스 등을 개명하고 구성을 무료로 수행할 수 있습니다. | DatabaseName, CollectionName, 지역, StatusCode| 모두| |메타데이터 요청으로 인한 제한을 모니터링하는 데 사용합니다.|
| 몽고 요청 (몽고 요청) | 카운트(카운트) | 생성된 Mongo 요청 수 | DatabaseName, CollectionName, 지역, CommandName, ErrorCode| 모두 |Mongo Query Request Rate, Mongo Update Request Rate, Mongo Delete Request Rate, Mongo Insert Request Rate, Mongo Count Request Rate| Mongo 요청 오류, 명령 유형별 사용량을 모니터링하는 데 사용합니다. |

#### <a name="request-unit-metrics"></a>요청 단위 메트릭

|메트릭(메트릭 표시 이름)|단위(집계 유형)|설명|차원| 시간 단위| 레거시 메트릭 매핑 | 사용 |
|---|---|---|---| ---| ---| ---|
| 몽고 요청 요금 (몽고 요청 요금) | 카운트(합계) |사용된 Mongo 요청 단위| DatabaseName, CollectionName, 지역, CommandName, ErrorCode| 모두 |Mongo Query Request Charge, Mongo Update Request Charge, Mongo Delete Request Charge, Mongo Insert Request Charge, Mongo Count Request Charge| Mongo 리소스 RU(분)를 모니터링하는 데 사용합니다.|
| 총 요청 단위(총 요청 단위)| 카운트(합계) | 사용된 요청 단위| DatabaseName, CollectionName, 지역, StatusCode |모두| TotalRequestUnits| 분 단위로 총 RU 사용량을 모니터링하는 데 사용합니다. 초당 사용한 평균 RU를 가져오려면 합계 집계(분)를 사용한 후 60으로 나눕니다.|
| 프로비저닝처리량(프로비저닝처리량)| 개수(최대) |컨테이너 세분성에 따라 프로비저닝된 처리량| 데이터베이스 이름, 컨테이너 이름| 5M| | 컨테이너당 프로비저닝된 처리량을 모니터링하는 데 사용됩니다.|

#### <a name="storage-metrics"></a>Storage 메트릭

|메트릭(메트릭 표시 이름)|단위(집계 유형)|설명|차원| 시간 단위| 레거시 메트릭 매핑 | 사용 |
|---|---|---|---| ---| ---| ---|
| 사용 가능한 저장소(사용 가능한 저장소) |바이트(합계) | 리전당 5분 분 단위로 보고된 총 사용 가능한 저장소| DatabaseName, CollectionName, Region| 5M| 사용 가능한 스토리지| 사용 가능한 스토리지 용량을 모니터링하는 데 사용합니다(고정 스토리지 컬렉션에만 해당). 최소 단위는 5분이어야 합니다.| 
| 데이터 사용량(데이터 사용량) |바이트(합계) |리전당 5분 분 단위로 보고된 총 데이터 사용량| DatabaseName, CollectionName, Region| 5M |데이터 크기 | 컨테이너 및 리전에서 총 데이터 사용량을 모니터링하는 데 사용되는 최소 세분성은 5분이어야 합니다.|
| 인덱스 사용(인덱스 사용) | 바이트(합계) |리전당 5분 분 단위로 보고된 총 인덱스 사용량| DatabaseName, CollectionName, Region| 5M| 인덱스 크기| 컨테이너 및 리전에서 총 데이터 사용량을 모니터링하는 데 사용되는 최소 세분성은 5분이어야 합니다. |
| 문서 할당량(문서 할당량) | 바이트(합계) | 리전당 5분 단위로 보고된 총 스토리지 할당량입니다.| DatabaseName, CollectionName, Region| 5M |스토리지 용량| 컨테이너 및 리전에서 총 할당량을 모니터링하는 데 사용되는 최소 세분성은 5분이어야 합니다.|
| 문서 수(문서 수) | 카운트(합계) |지역별 5분 분 단위로 보고된 총 문서 수| DatabaseName, CollectionName, Region| 5M |문서 수|컨테이너 및 리전에서 문서 수를 모니터링하는 데 사용되는 최소 세분성은 5분이어야 합니다.|

#### <a name="latency-metrics"></a>대기 시간 메트릭

|메트릭(메트릭 표시 이름)|단위(집계 유형)|설명|차원| 시간 단위| 사용 |
|---|---|---|---| ---| ---|
| 복제지연(복제 대기 시간)| 밀리초(최소, 최대, 평균) | 지역 사용 계정에 대한 원본 및 대상 지역의 P99 복제 대기 시간| SourceRegion, TargetRegion| 모두 | 지역에서 복제된 계정에 대한 두 지역 간 P99 복제 대기 시간을 모니터링하는 데 사용합니다. |
| 서버 측 대기 시간| 밀리초(평균) | 서버가 요청을 처리하는 데 걸린 시간입니다. | 컬렉션 이름, 연결 모드, 데이터베이스 이름, 작업 유형, publicAPIType, 지역 | 모두 | Azure Cosmos DB 서버에서 요청 대기 시간을 모니터링하는 데 사용됩니다. |



#### <a name="availability-metrics"></a>가용성 메트릭

|메트릭(메트릭 표시 이름) |단위(집계 유형)|설명| 시간 단위| 레거시 메트릭 매핑 | 사용 |
|---|---|---|---| ---| ---|
| 서비스 가용성(서비스 가용성)| 백분율(최소, 최대) | 1시간 단위의 계정 요청 가용성| 1H | 서비스 가용성 | 총 전달 된 요청의 백분율을 나타냅니다. 요청은 상태 코드가 410, 500 또는 503인경 우 시스템 오류로 인해 실패한 것으로 간주됩니다. 시간 단위로 계정의 가용성을 모니터링하는 데 사용합니다. |


#### <a name="cassandra-api-metrics"></a>Cassandra API 메트릭

|메트릭(메트릭 표시 이름)|단위(집계 유형)|설명|차원| 시간 단위| 사용 |
|---|---|---|---| ---| ---|
| 카산드라 요청 (카산드라 요청) | 카운트(카운트) | 수행된 Cassandra API 요청 수| DatabaseName, CollectionName, ErrorCode, Region, OperationType, ResourceType| 모두| 분 단위로 Cassandra 요청을 모니터링하는 데 사용합니다. 초당 평균 요청 수를 가져오려면 개수 집계(분)를 사용한 후 60으로 나눕니다.|
| 카산드라 요청 요금 (카산드라 요청 요금) | 카운트 (합계, 최소, 최대, 평균) | Cassandra API 요청에 사용된 요청 단위| DatabaseName, CollectionName, Region, OperationType, ResourceType| 모두| Cassandra API 계정에서 분당 사용된 RU를 모니터링하는 데 사용합니다.|
| 카산드라커넥션클로저클로저스 (카산드라 연결 클로저) |카운트(카운트) |닫힌 Cassandra 연결 수| ClosureReason, Region| 모두 | 클라이언트와 Azure Cosmos DB Cassandra API 간 연결을 모니터링하는 데 사용합니다.|

## <a name="see-also"></a>관련 항목

- [Azure 코스모스 DB 모니터링에](monitor-cosmos-db.md) 대한 설명은 Azure 코스모스 DB 모니터링을 참조하십시오.
- Azure 리소스 모니터링에 대한 자세한 내용은 [Azure 모니터를 통해 Azure 리소스](../azure-monitor/insights/monitor-azure-resource.md) 모니터링을 참조하십시오.
