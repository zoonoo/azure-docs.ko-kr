---
title: Azure Database for MySQL 쿼리 저장소
description: 시간에 따른 성능을 추적 하는 데 도움이 되는 Azure Database for MySQL의 쿼리 저장소 기능에 대해 알아봅니다.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: c8891fc96e3e511e4127b4e114a45b5a865cf8eb
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/05/2019
ms.locfileid: "73603021"
---
# <a name="monitor-azure-database-for-mysql-performance-with-query-store"></a>쿼리 저장소를 사용 하 여 Azure Database for MySQL 성능 모니터링

**적용 대상:** Azure Database for MySQL 5.7

Azure Database for MySQL의 쿼리 저장소 기능은 시간이 지남에 따라 쿼리 성능을 추적 하는 방법을 제공 합니다. 쿼리 저장소는 가장 오래 실행되고 리소스를 가장 많이 사용하는 쿼리를 신속하게 찾도록 지원하여 성능 문제 해결을 단순화합니다. 쿼리 저장소는 쿼리 및 런타임 통계의 기록을 자동으로 캡처하고 검토를 위해 보존합니다. 데이터베이스 사용량 패턴을 볼 수 있도록 데이터를 기간별로 구분합니다. 모든 사용자, 데이터베이스 및 쿼리에 대 한 데이터는 Azure Database for MySQL 인스턴스의 **mysql** 스키마 데이터베이스에 저장 됩니다.

## <a name="common-scenarios-for-using-query-store"></a>쿼리 저장소 사용에 대 한 일반적인 시나리오

쿼리 저장소는 다음과 같은 다양 한 시나리오에서 사용할 수 있습니다.

- 회귀 쿼리 검색
- 지정된 기간에 쿼리가 실행된 횟수를 확인하는 경우
- 전체 기간에 대한 쿼리의 평균 실행 시간을 비교하여 큰 델타를 확인하는 경우

## <a name="enabling-query-store"></a>쿼리 저장소 사용

쿼리 저장소는 옵트인 기능이므로 서버에서 기본적으로 활성화되지 않습니다. 쿼리 저장소는 지정 된 서버에 있는 모든 데이터베이스에 대해 전역적으로 사용 하거나 사용 하지 않도록 설정 되며 데이터베이스당 설정 하거나 해제할 수 없습니다.

### <a name="enable-query-store-using-the-azure-portal"></a>Azure Portal을 통해 쿼리 저장소 사용

1. Azure Portal에 로그인 하 고 Azure Database for MySQL 서버를 선택 합니다.
1. 메뉴의 **설정** 섹션에서 **서버 매개 변수**를 선택합니다.
1. Query_store_capture_mode 매개 변수를 검색 합니다.
1. 값을 모두로 설정 하 고 **저장**합니다.

쿼리 저장소에서 대기 통계를 활성화하려면 다음을 수행합니다.

1. Query_store_wait_sampling_capture_mode 매개 변수를 검색 합니다.
1. 값을 모두로 설정 하 고 **저장**합니다.

데이터의 첫 번째 일괄 처리가 mysql 데이터베이스에 유지 되는 데 최대 20 분이 걸립니다.

## <a name="information-in-query-store"></a>쿼리 저장소의 정보

쿼리 저장소에는 다음과 같은 두 개의 저장소가 있습니다.

- 쿼리 실행 통계 정보를 유지 하기 위한 런타임 통계 저장소입니다.
- 대기 통계 저장소는 대기 통계 정보를 유지 합니다.

공간 사용을 최소화 하기 위해 런타임 통계 저장소의 런타임 실행 통계는 고정 구성 가능 기간 동안 집계 됩니다. 이러한 저장소의 정보는 쿼리 저장소 보기를 쿼리하여 표시됩니다.

다음 쿼리는 쿼리 저장소의 쿼리에 대한 정보를 반환합니다.

```sql
SELECT * FROM mysql.query_store;
```

또는 대기 통계 쿼리:

```sql
SELECT * FROM mysql.query_store_wait_stats;
```

## <a name="finding-wait-queries"></a>대기 쿼리 찾기

> [!NOTE]
> 대기 통계는 최대 작업 시간 동안 사용 하도록 설정 하거나 중요 한 워크 로드에 대해 무기한으로 설정 하면 안 됩니다. <br>높은 CPU 사용률 또는 더 낮은 vCores로 구성 된 서버에서 실행 되는 워크 로드의 경우 대기 통계를 사용 하도록 설정할 때는 주의 해야 합니다. 무기한으로 설정 하면 안 됩니다. 

대기 이벤트 유형은 유사성을 기준으로 서로 다른 대기 이벤트를 버킷으로 결합합니다. 쿼리 저장소는 대기 이벤트 유형, 특정 대기 이벤트 이름 및 해당하는 쿼리를 제공합니다. 이 대기 정보를 쿼리 런타임 통계와 상호 연관시킬 수 있다는 것은 쿼리 성능 특성에 영향을 미치는 내용을 더 잘 이해할 수 있음을 의미합니다.

다음은 쿼리 저장소의 대기 통계를 사용하여 워크로드에 대한 더 많은 통찰력을 얻을 수 있는 방법의 몇 가지 예입니다.

| **관찰** | **동작** |
|---|---|
|최고 잠금 대기 | 영향을 받는 쿼리에 대한 쿼리 텍스트를 확인하고 대상 엔터티를 식별합니다. 쿼리 저장소에서 자주 실행되거나 오래 실행되는 동일한 엔터티를 수정하는 다른 쿼리를 확인합니다. 이러한 쿼리를 식별한 후 동시성 향상을 위해 애플리케이션 논리를 변경해 보거나 덜 제한적인 격리 수준을 사용합니다. |
|높은 버퍼 IO 대기 | 쿼리 저장소에서 물리적 읽기 횟수가 많은 쿼리를 찾습니다. IO 대기 시간이 높은 쿼리와 일치 하는 경우에는 검색 대신 검색을 수행 하기 위해 기본 엔터티에 대 한 인덱스를 도입 하는 것이 좋습니다. 이렇게 하면 쿼리의 IO 오버헤드가 최소화됩니다. 포털에서 서버에 대한 **성능 권장 사항**을 확인하여 쿼리를 최적화하는 이 서버에 대한 인덱스 권장 사항이 있는지 확인합니다. |
|높은 메모리 대기 | 쿼리 저장소에서 메모리 사용량이 많은 상위 쿼리를 찾습니다. 이러한 쿼리는 영향을 받는 쿼리의 추가 진행을 지연시킬 수 있습니다. 포털에서 서버에 대한 **성능 권장 사항**을 확인하여 이러한 쿼리를 최적화하는 인덱스 권장 사항이 있는지 확인합니다.|

## <a name="configuration-options"></a>구성 옵션

쿼리 저장소를 사용하도록 설정하면 데이터가 15분 집계 창에 저장되고 각 창에는 최대 500개의 고유 쿼리가 포함됩니다.

다음 옵션은 쿼리 저장소 매개 변수를 구성하는 데 사용할 수 있습니다.

| **매개 변수** | **설명** | **Default** | **범위** |
|---|---|---|---|
| query_store_capture_mode | 값에 따라 쿼리 저장소 기능을 설정/해제 합니다. 참고: performance_schema 해제 된 경우 query_store_capture_mode를 켜면이 기능에 필요한 performance_schema 및 성능 스키마 계측의 하위 집합이 설정 됩니다. | ALL | 없음, 모두 |
| query_store_capture_interval | 쿼리 저장소 캡처 간격 (분)입니다. 쿼리 메트릭이 집계 되는 간격을 지정할 수 있습니다. | 15 | 5 - 60 |
| query_store_capture_utility_queries | 시스템에서 실행 되는 모든 유틸리티 쿼리를 캡처하도록 설정 하거나 해제 합니다. | 아니요 | 예, 아니요 |
| query_store_retention_period_in_days | 쿼리 저장소에 데이터를 유지할 기간 (일)입니다. | 7 | 1 - 30 |

다음 옵션은 특히 대기 통계에 적용됩니다.

| **매개 변수** | **설명** | **Default** | **범위** |
|---|---|---|---|
| query_store_wait_sampling_capture_mode | 대기 통계를 켜고 끌 수 있습니다. | 없음 | 없음, 모두 |
| query_store_wait_sampling_frequency | 대기 샘플링 빈도 (초)를 변경 합니다. 5 ~ 300 초. | 30 | 5-300 |

> [!NOTE]
> 현재이 구성을 대체 **query_store_capture_mode** . 즉, **query_store_capture_mode** 와 **query_store_wait_sampling_capture_mode** 모두 대기 통계가 작동 하려면 모든를 사용 하도록 설정 해야 합니다. **Query_store_capture_mode** 해제 된 경우 대기 통계는 사용 performance_schema를 사용 하 고 쿼리 저장소에서 캡처한 query_text를 활용 하므로 대기 통계만 꺼집니다.

 [Azure Portal](howto-server-parameters.md) 또는 [Azure CLI](howto-configure-server-parameters-using-cli.md) 를 사용 하 여 매개 변수에 대 한 다른 값을 가져오거나 설정 합니다.

## <a name="views-and-functions"></a>보기 및 함수

다음 보기 및 함수를 사용하여 쿼리 저장소를 보고 관리합니다. [권한 선택 public 역할](howto-create-users.md#how-to-create-additional-admin-users-in-azure-database-for-mysql) 의 모든 사용자는 이러한 뷰를 사용 하 여 쿼리 저장소의 데이터를 볼 수 있습니다. 이러한 보기는 **mysql** 데이터베이스 에서만 사용할 수 있습니다.

쿼리는 리터럴 및 상수를 제거한 후 구조를 확인하여 정규화됩니다. 리터럴 값을 제외하고 두 쿼리가 동일한 경우에는 동일한 해시를 포함합니다.

### <a name="mysqlquery_store"></a>mysql.query_store

이 보기는 쿼리 저장소의 모든 데이터를 반환합니다. 각 고유 데이터베이스 ID, 사용자 ID 및 쿼리 ID에 대한 하나의 행이 있습니다.

| **이름** | **데이터 형식** | **IS_NULLABLE** | **설명** |
|---|---|---|---|
| `schema_name`| varchar(64) | 아니요 | 스키마의 이름입니다. |
| `query_id`| bigint(20) | 아니요| 특정 쿼리에 대해 생성 되는 고유 ID로, 같은 쿼리가 다른 스키마에서 실행 되는 경우 새 ID가 생성 됩니다. |
| `timestamp_id` | timestamp| 아니요| 쿼리가 실행 되는 타임 스탬프입니다. Query_store_interval 구성을 기반으로 합니다.|
| `query_digest_text`| longtext| 아니요| 모든 리터럴을 제거한 후 정규화 된 쿼리 텍스트입니다.|
| `query_sample_text` | longtext| 아니요| 리터럴을 사용한 실제 쿼리의 첫 번째 모양|
| `query_digest_truncated` | bit| 예| 쿼리 텍스트가 잘린 지 여부를 나타냅니다. 쿼리가 1kb 보다 길면 값은 예입니다.|
| `execution_count` | bigint(20)| 아니요| 구성 된 간격 동안이 타임 스탬프 ID/에 대해 쿼리를 실행 한 횟수입니다.|
| `warning_count` | bigint(20)| 아니요| 내부에서이 쿼리가 생성 된 경고의 수입니다.|
| `error_count` | bigint(20)| 아니요| 이 쿼리가 시간 간격 동안 생성 된 오류 수입니다.|
| `sum_timer_wait` | double| 예| 이 쿼리의 전체 실행 시간 (간격)|
| `avg_timer_wait` | double| 예| 이 쿼리에 대 한 평균 실행 시간 (간격)|
| `min_timer_wait` | double| 예| 이 쿼리의 최소 실행 시간|
| `max_timer_wait` | double| 예| 최대 실행 시간|
| `sum_lock_time` | bigint(20)| 아니요| 이 기간 동안이 쿼리 실행의 모든 잠금에 소요 된 총 시간입니다.|
| `sum_rows_affected` | bigint(20)| 아니요| 영향을 받는 행 수|
| `sum_rows_sent` | bigint(20)| 아니요| 클라이언트로 전송 된 행 수|
| `sum_rows_examined` | bigint(20)| 아니요| 검사된 행 수|
| `sum_select_full_join` | bigint(20)| 아니요| 전체 조인 수|
| `sum_select_scan` | bigint(20)| 아니요| 선택 검색 수 |
| `sum_sort_rows` | bigint(20)| 아니요| 정렬 된 행 수|
| `sum_no_index_used` | bigint(20)| 아니요| 쿼리에서 인덱스를 사용 하지 않은 횟수|
| `sum_no_good_index_used` | bigint(20)| 아니요| 쿼리 실행 엔진이 양호한 인덱스를 사용 하지 않은 횟수|
| `sum_created_tmp_tables` | bigint(20)| 아니요| 만든 임시 테이블의 총 수|
| `sum_created_tmp_disk_tables` | bigint(20)| 아니요| 디스크에 생성 된 임시 테이블의 총 수입니다 (i/o 생성).|
| `first_seen` | timestamp| 아니요| 집계 창 중 쿼리의 첫 번째 발생 (UTC)|
| `last_seen` | timestamp| 아니요| 이 집계 창에 있는 쿼리의 마지막 발생 (UTC)입니다.|

### <a name="mysqlquery_store_wait_stats"></a>mysql.query_store_wait_stats

이 보기는 쿼리 저장소의 대기 이벤트 데이터를 반환합니다. 각 고유 데이터베이스 ID, 사용자 ID, 쿼리 ID 및 이벤트에 대한 하나의 행이 있습니다.

| **이름**| **데이터 형식** | **IS_NULLABLE** | **설명** |
|---|---|---|---|
| `interval_start` | timestamp | 아니요| 간격의 시작 (15 분 증분)|
| `interval_end` | timestamp | 아니요| 간격의 끝 (15 분 증분)|
| `query_id` | bigint(20) | 아니요| 쿼리 저장소에서 정규화 된 쿼리에 대해 생성 된 고유 ID|
| `query_digest_id` | varchar(32) | 아니요| 쿼리 저장소에서 모든 리터럴을 제거한 후의 정규화 된 쿼리 텍스트입니다. |
| `query_digest_text` | longtext | 아니요| 리터럴을 사용한 실제 쿼리의 첫 번째 모양 (쿼리 저장소에서) |
| `event_type` | varchar(32) | 아니요| 대기 이벤트의 범주입니다. |
| `event_name` | varchar(128) | 아니요| 대기 이벤트의 이름입니다. |
| `count_star` | bigint(20) | 아니요| 쿼리 간격 중 샘플링 된 대기 이벤트 수 |
| `sum_timer_wait_ms` | double | 아니요| 간격 중이 쿼리의 총 대기 시간 (밀리초)입니다. |

### <a name="functions"></a>함수

| **이름**| **설명** |
|---|---|
| `mysql.az_purge_querystore_data(TIMESTAMP)` | 지정 된 타임 스탬프 이전의 모든 쿼리 저장소 데이터를 제거 합니다. |
| `mysql.az_procedure_purge_querystore_event(TIMESTAMP)` | 지정 된 타임 스탬프 보다 먼저 대기 이벤트 데이터를 모두 제거 합니다. |
| `mysql.az_procedure_purge_recommendation(TIMESTAMP)` | 만료 날짜가 지정 된 타임 스탬프 보다 이전 인 권장 구성을 제거 합니다. |

## <a name="limitations-and-known-issues"></a>제한 사항 및 알려진 문제

- MySQL 서버에 `default_transaction_read_only` 매개 변수가 있으면 쿼리 저장소 데이터를 캡처할 수 없습니다.
- 긴 유니코드 쿼리 (\>= 6000 바이트)를 발견 하면 쿼리 저장소 기능을 중단할 수 있습니다.
- 대기 통계의 보존 기간은 24 시간입니다.
- 대기 통계는 샘플을 사용 하 여 이벤트의 비율을 캡처합니다. `query_store_wait_sampling_frequency`매개 변수를 사용 하 여 빈도를 수정할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [Query Performance Insights](concepts-query-performance-insight.md) 에 대 한 자세한 정보
