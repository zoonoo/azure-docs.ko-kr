---
title: 쿼리 저장소에서 Azure Database for MariaDB
description: 이 문서에서는 Azure Database에서 쿼리 저장소 기능을 MariaDB에 대 한 설명
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 06/12/2019
ms.openlocfilehash: 9016fa159e868f649901928cdf2dca2f08725e77
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67079393"
---
# <a name="monitor-azure-database-for-mariadb-performance-with-query-store"></a>쿼리 저장소를 사용 하 여 MariaDB 성능에 대 한 Azure Database를 모니터링 합니다.

**적용 대상:**  Azure Database for MariaDB 10.2

> [!NOTE]
> 쿼리 저장소 미리 보기입니다. Azure portal에서 쿼리 저장소에 대 한 지원을 롤아웃하는 아직 못할 지역의 하며

Azure Database for Mariadb에서에서 쿼리 저장소 기능에는 시간에 따른 쿼리 성능을 추적 하는 방법을 제공 합니다. 쿼리 저장소는 가장 오래 실행되고 리소스를 가장 많이 사용하는 쿼리를 신속하게 찾도록 지원하여 성능 문제 해결을 단순화합니다. 쿼리 저장소는 쿼리 및 런타임 통계의 기록을 자동으로 캡처하고 검토를 위해 보존합니다. 데이터베이스 사용량 패턴을 볼 수 있도록 데이터를 기간별로 구분합니다. 모든 사용자, 데이터베이스 및 쿼리 데이터에 저장 되는 **mysql** MariaDB 인스턴스에 대 한 Azure Database에서 데이터베이스 스키마입니다.

## <a name="common-scenarios-for-using-query-store"></a>쿼리 저장소를 사용 하 여 일반적인 시나리오

쿼리 저장소는 다양 한 다음과 같은 시나리오에서에서 사용할 수 있습니다.

- 회귀 된 쿼리를 검색합니다.
- 지정된 기간에 쿼리가 실행된 횟수를 확인하는 경우
- 전체 기간에 대한 쿼리의 평균 실행 시간을 비교하여 큰 델타를 확인하는 경우
- 지난 X시간 동안 가장 오래 실행되는 쿼리를 식별하는 경우
- 리소스에서 대기 중인 최상위 N개 쿼리를 식별하는 경우
- 쿼리에 대 한 대기 특성 이해
- 리소스 대기 및 리소스 경합 존재 하는 위치에 대 한 추세 이해

## <a name="enabling-query-store"></a>쿼리 저장소 사용

쿼리 저장소는 옵트인 기능이므로 서버에서 기본적으로 활성화되지 않습니다. 쿼리 저장소 사용 하도록 설정 되었거나 지정된 된 서버에 있는 모든 데이터베이스에 대 한 전역적으로 사용 하지 않도록 설정 및 데이터베이스당 켜거나 설정할 수 없습니다.

### <a name="enable-query-store-using-the-azure-portal"></a>Azure Portal을 통해 쿼리 저장소 사용

1. Azure portal에 로그인 하 고 Azure Database for MariaDB 서버를 선택 합니다.
1. 선택 **서버 매개 변수** 에 **설정** 메뉴의 섹션입니다.
1. Query_store_capture_mode 매개 변수를 검색 합니다.
1. 모든 값을 설정 하 고 **저장할**합니다.

쿼리 저장소에서 대기 통계를 활성화하려면 다음을 수행합니다.

1. Query_store_wait_sampling_capture_mode 매개 변수를 검색 합니다.
1. 모든 값을 설정 하 고 **저장할**합니다.

Mysql 데이터베이스에서 유지 하는 데이터의 첫 번째 일괄 처리에 대 한 최대 20 분을 허용 합니다.

## <a name="information-in-query-store"></a>쿼리 저장소의 정보

쿼리 저장소에는 다음과 같은 두 개의 저장소가 있습니다.

- 쿼리 실행 통계 정보를 유지 하는 것에 대 한 런타임 통계를 저장 합니다.
- 대기 통계 저장소 대기 통계 정보를 유지 합니다.

공간 사용량을 최소화 하려면 런타임 통계 저장소의 런타임 실행 통계는 고정 하 고 구성 가능한 기간 동안 집계 됩니다. 이러한 저장소의 정보는 쿼리 저장소 보기를 쿼리하여 표시됩니다.

다음 쿼리는 쿼리 저장소의 쿼리에 대한 정보를 반환합니다.

```sql
SELECT * FROM mysql.query_store;
```

또는이 쿼리 대기 통계에 대 한 합니다.

```sql
SELECT * FROM mysql.query_store_wait_stats;
```

## <a name="finding-wait-queries"></a>대기 쿼리 찾기

대기 이벤트 유형은 유사성을 기준으로 서로 다른 대기 이벤트를 버킷으로 결합합니다. 쿼리 저장소는 대기 이벤트 유형, 특정 대기 이벤트 이름 및 해당하는 쿼리를 제공합니다. 이 대기 정보를 쿼리 런타임 통계와 상호 연관시킬 수 있다는 것은 쿼리 성능 특성에 영향을 미치는 내용을 더 잘 이해할 수 있음을 의미합니다.

다음은 쿼리 저장소의 대기 통계를 사용하여 워크로드에 대한 더 많은 통찰력을 얻을 수 있는 방법의 몇 가지 예입니다.

| **관찰** | **작업** |
|---|---|
|최고 잠금 대기 | 영향을 받는 쿼리에 대한 쿼리 텍스트를 확인하고 대상 엔터티를 식별합니다. 쿼리 저장소에서 자주 실행되거나 오래 실행되는 동일한 엔터티를 수정하는 다른 쿼리를 확인합니다. 이러한 쿼리를 식별한 후 동시성 향상을 위해 애플리케이션 논리를 변경해 보거나 덜 제한적인 격리 수준을 사용합니다. |
|높은 버퍼 IO 대기 | 쿼리 저장소에서 물리적 읽기 횟수가 많은 쿼리를 찾습니다. IO 대기가 높은 쿼리와 일치 하는 경우에 검색 대신 검색을 위해 기본 엔터티에 대 한 인덱스를 도입 하는 것이 좋습니다. 이렇게 하면 쿼리의 IO 오버헤드가 최소화됩니다. 확인 합니다 **성능 권장 사항** 는 쿼리를 최적화 하는이 서버에 대 한 인덱스 권장 사항을 확인 하려면 포털에서 서버에 대 한 합니다. |
|높은 메모리 대기 | 쿼리 저장소에서 메모리 사용량이 많은 상위 쿼리를 찾습니다. 이러한 쿼리는 영향을 받는 쿼리의 추가 진행을 지연시킬 수 있습니다. 확인 합니다 **성능 권장 사항** 는 이러한 쿼리를 최적화 하는 인덱스 권장 사항을 확인 하려면 포털에서 서버에 대 한 합니다.|

## <a name="configuration-options"></a>구성 옵션

쿼리 저장소를 사용하도록 설정하면 데이터가 15분 집계 창에 저장되고 각 창에는 최대 500개의 고유 쿼리가 포함됩니다.

다음 옵션은 쿼리 저장소 매개 변수를 구성하는 데 사용할 수 있습니다.

| **매개 변수** | **설명** | **기본값** | **Range** |
|---|---|---|---|
| query_store_capture_mode | ON/OFF 값을 기반으로 쿼리 저장소 기능을 설정 합니다. 참고: Performance_schema OFF 이면 query_store_capture_mode 켜기 바뀝니다 performance_schema에이 기능에 필요한 성능 스키마 계측의 하위 집합입니다. | ALL | 없음, 모든 |
| query_store_capture_interval | 쿼리 저장소는 분 단위로 간격을 캡처합니다. 쿼리 메트릭이 집계 되는 간격을 지정할 수 있습니다. | 15 | 5 - 60 |
| query_store_capture_utility_queries | 설정 ON 또는 OFF 시스템에서 실행 되는 모든 유틸리티 쿼리 캡처입니다. | 아니요 | 예, 아니요 |
| query_store_retention_period_in_days | 쿼리 저장소에서 데이터를 보존할 일의 시간 창입니다. | 7 | 1 - 30 |

다음 옵션은 특히 대기 통계에 적용됩니다.

| **매개 변수** | **설명** | **기본값** | **Range** |
|---|---|---|---|
| query_store_wait_sampling_capture_mode | ON을 설정 하면 허용 / 대기 통계를 해제 합니다. | 없음 | 없음, 모든 |
| query_store_wait_sampling_frequency | 초에서 대기 샘플링 빈도 변경 합니다. 5에서 300 초입니다. | 30 | 5-300 |

> [!NOTE]
> 현재 **query_store_capture_mode** 둘 다를 의미 하는이 구성에서는 대체 **query_store_capture_mode** 하 고 **query_store_wait_sampling_capture_mode** 작업 대기 통계에 대 한 모든 사용 하도록 설정 해야 합니다. 하는 경우 **query_store_capture_mode** 꺼져 대기 통계 꺼져도 사용 하도록 설정 performance_schema 및 쿼리 저장소에서 캡처하지 query_text 대기 통계 사용 하므로 다음입니다.

사용 합니다 [Azure portal](howto-server-parameters.md) 가져오거나 매개 변수에 대해 다른 값을 설정 합니다.

## <a name="views-and-functions"></a>보기 및 함수

다음 보기 및 함수를 사용하여 쿼리 저장소를 보고 관리합니다. 모든 사용자에 게는 [선택 권한 public 역할](howto-create-users.md#create-additional-admin-users) 쿼리 저장소의 데이터를 보려면 이러한 뷰를 사용할 수 있습니다. 이러한 보기에만 제공 되는 **mysql** 데이터베이스입니다.

쿼리는 리터럴 및 상수를 제거한 후 구조를 확인하여 정규화됩니다. 리터럴 값을 제외하고 두 쿼리가 동일한 경우에는 동일한 해시를 포함합니다.

### <a name="mysqlquerystore"></a>mysql.query_store

이 보기는 쿼리 저장소의 모든 데이터를 반환합니다. 각 고유 데이터베이스 ID, 사용자 ID 및 쿼리 ID에 대한 하나의 행이 있습니다.

| **Name** | **데이터 형식** | **IS_NULLABLE** | **설명** |
|---|---|---|---|
| `schema_name`| varchar(64) | 아니요 | 스키마의 이름 |
| `query_id`| bigint(20) | 아니요| 다른 스키마를 새 ID에서 동일한 쿼리를 실행 하는 경우 특정 쿼리의 경우 생성 된 고유 ID가 만들어집니다. |
| `timestamp_id` | timestamp| 아니요| 쿼리가 실행 되는 타임 스탬프입니다. 이 query_store_interval 구성 기반|
| `query_digest_text`| longtext| 아니요| 모든 리터럴은 제거한 후 정규화 된 쿼리 텍스트|
| `query_sample_text` | longtext| 아니요| 첫 번째 모양의 리터럴 사용 하 여 실제 쿼리|
| `query_digest_truncated` | bit| 예| 여부 쿼리 텍스트가 잘렸습니다. 값 쿼리는 1KB 보다 긴 경우 예 됩니다.|
| `execution_count` | bigint(20)| 아니요| 이 타임 스탬프 ID / 구성 된 간격 동안 쿼리 실행 횟수|
| `warning_count` | bigint(20)| 아니요| 이 쿼리는 내부 중 생성 된 경고 수|
| `error_count` | bigint(20)| 아니요| 이 쿼리 간격 동안 발생 한 오류 수|
| `sum_timer_wait` | double| 예| 이 쿼리 간격의 총 실행 시간|
| `avg_timer_wait` | double| 예| 간격 동안이 쿼리에 대 한 평균 실행 시간|
| `min_timer_wait` | double| 예| 이 쿼리에 대 한 최소 실행 시간|
| `max_timer_wait` | double| 예| 최대 실행 시간|
| `sum_lock_time` | bigint(20)| 아니요| 이 기간 동안이 쿼리 실행에 대 한 모든 잠금을에 소요 된 총 시간|
| `sum_rows_affected` | bigint(20)| 아니요| 영향을 받는 행 수|
| `sum_rows_sent` | bigint(20)| 아니요| 클라이언트에 보내는 행 수|
| `sum_rows_examined` | bigint(20)| 아니요| 검사된 행 수|
| `sum_select_full_join` | bigint(20)| 아니요| 전체 조인 수|
| `sum_select_scan` | bigint(20)| 아니요| 선택 검색 수 |
| `sum_sort_rows` | bigint(20)| 아니요| 정렬 된 행 수|
| `sum_no_index_used` | bigint(20)| 아니요| 쿼리 인덱스를 사용 하지 않은 횟수|
| `sum_no_good_index_used` | bigint(20)| 아니요| 쿼리 실행 엔진이 적절 한 인덱스를 사용 하지 않은 횟수|
| `sum_created_tmp_tables` | bigint(20)| 아니요| 생성 된 임시 테이블의 총 수|
| `sum_created_tmp_disk_tables` | bigint(20)| 아니요| 총 디스크 (I/O 생성)에서 만든 임시 테이블|
| `first_seen` | timestamp| 아니요| 처음 (UTC) 쿼리의 집계 기간 동안|
| `last_seen` | timestamp| 아니요| 마지막 (UTC) 쿼리의이 집계 기간 동안|

### <a name="mysqlquerystorewaitstats"></a>mysql.query_store_wait_stats

이 보기는 쿼리 저장소의 대기 이벤트 데이터를 반환합니다. 각 고유 데이터베이스 ID, 사용자 ID, 쿼리 ID 및 이벤트에 대한 하나의 행이 있습니다.

| **Name**| **데이터 형식** | **IS_NULLABLE** | **설명** |
|---|---|---|---|
| `interval_start` | timestamp | 아니요| (15 분 증분) 간격의 시작|
| `interval_end` | timestamp | 아니요| (15 분 증분) 간격의 끝|
| `query_id` | bigint(20) | 아니요| 정규화 된 쿼리에서 (쿼리 저장소)에서 생성 된 고유 ID|
| `query_digest_id` | varchar(32) | 아니요| 정규화 된 쿼리 텍스트 (쿼리 저장소)에서 모든 리터럴은 제거한 후 |
| `query_digest_text` | longtext | 아니요| 리터럴 (쿼리 저장소)에서 실제 쿼리의 첫 번째 모양 |
| `event_type` | varchar(32) | 아니요| 대기 이벤트의 범주 |
| `event_name` | varchar(128) | 아니요| 대기 이벤트의 이름 |
| `count_star` | bigint(20) | 아니요| 샘플링 간격의 쿼리에 대 한 대기 이벤트 수 |
| `sum_timer_wait_ms` | double | 아니요| 총 대기 시간 (밀리초)이 쿼리 간격 |

### <a name="functions"></a>Functions

| **Name**| **설명** |
|---|---|
| `mysql.az_purge_querystore_data(TIMESTAMP)` | 지정 된 타임 스탬프 하기 전에 모든 쿼리 저장소 데이터를 제거합니다. |
| `mysql.az_procedure_purge_querystore_event(TIMESTAMP)` | 모든 삭제 대기 전에 지정 된 타임 스탬프 이벤트 데이터 |
| `mysql.az_procedure_purge_recommendation(TIMESTAMP)` | 지정 된 타임 스탬프 하기 전에 해당 만료 되는 권장 사항 제거 |

## <a name="limitations-and-known-issues"></a>제한 사항 및 알려진 문제

- MariaDB 서버에 있는 경우 매개 변수 `default_transaction_read_only` 쿼리 저장소에 데이터를 캡처할 수 없습니다.
- 쿼리 저장소 기능을 중단 하 긴 유니코드 쿼리를 발견할 경우 (\>= 6000 바이트)입니다.
- 대기 통계에 대 한 보존 기간은 24 시간입니다.
- 대기 통계 이벤트의 일부만 샘플 ti 캡처를 사용 합니다. 매개 변수를 사용 하 여 빈도 수정할 수 있습니다 `query_store_wait_sampling_frequency`합니다.

## <a name="next-steps"></a>다음 단계

- 자세한 내용은 [Query Performance Insights](concepts-query-performance-insight.md)
