---
title: 쿼리 저장소 - MariaDB용 Azure 데이터베이스
description: 시간이 지남에 따라 성능을 추적하는 데 도움이 되는 MariaDB용 Azure 데이터베이스의 쿼리 저장소 기능에 대해 알아봅니다.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: a502638744009fc34a7f0a27f8034b89d2c8fa26
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79527812"
---
# <a name="monitor-azure-database-for-mariadb-performance-with-query-store"></a>쿼리 저장소를 사용하여 MariaDB 성능에 대한 Azure 데이터베이스 모니터링

**다음에 적용됩니다.** MariaDB 10.2에 대한 Azure 데이터베이스

Mariadb용 Azure 데이터베이스의 쿼리 저장소 기능은 시간에 따라 쿼리 성능을 추적하는 방법을 제공합니다. 쿼리 저장소는 가장 오래 실행되고 리소스를 가장 많이 사용하는 쿼리를 신속하게 찾도록 지원하여 성능 문제 해결을 단순화합니다. 쿼리 저장소는 쿼리 및 런타임 통계의 기록을 자동으로 캡처하고 검토를 위해 보존합니다. 데이터베이스 사용량 패턴을 볼 수 있도록 데이터를 기간별로 구분합니다. 모든 사용자, 데이터베이스 및 쿼리에 대한 데이터는 MariaDB 인스턴스에 대한 Azure 데이터베이스의 **mysql** 스키마 데이터베이스에 저장됩니다.

## <a name="common-scenarios-for-using-query-store"></a>쿼리 저장소 를 사용하기 위한 일반적인 시나리오

쿼리 저장소는 다음을 포함하여 여러 시나리오에서 사용할 수 있습니다.

- 회귀 된 쿼리 검색
- 지정된 기간에 쿼리가 실행된 횟수를 확인하는 경우
- 전체 기간에 대한 쿼리의 평균 실행 시간을 비교하여 큰 델타를 확인하는 경우

## <a name="enabling-query-store"></a>쿼리 저장소 사용

쿼리 저장소는 옵트인 기능이므로 서버에서 기본적으로 활성화되지 않습니다. 쿼리 저장소는 지정된 서버의 모든 데이터베이스에 대해 전역적으로 활성화되거나 비활성화되며 데이터베이스당 켜거나 끌 수 없습니다.

### <a name="enable-query-store-using-the-azure-portal"></a>Azure Portal을 통해 쿼리 저장소 사용

1. Azure 포털에 로그인하고 MariaDB 서버에 대한 Azure 데이터베이스를 선택합니다.
1. 메뉴의 **설정** 섹션에서 **서버 매개 변수**를 선택합니다.
1. query_store_capture_mode 매개 변수를 검색합니다.
1. 값을 모두로 설정하고 **저장**합니다.

쿼리 저장소에서 대기 통계를 사용하려면 다음을 수행합니다.

1. query_store_wait_sampling_capture_mode 매개 변수를 검색합니다.
1. 값을 모두로 설정하고 **저장**합니다.

mysql 데이터베이스에 데이터의 첫 번째 일괄 처리가 유지될 때까지 최대 20분이 허용됩니다.

## <a name="information-in-query-store"></a>쿼리 저장소의 정보

쿼리 저장소에는 다음과 같은 두 개의 저장소가 있습니다.

- 쿼리 실행 통계 정보를 유지하기 위한 런타임 통계는 저장합니다.
- 대기 통계는 대기 시간 통계 정보를 계속 저장합니다.

공간 사용을 최소화하기 위해 런타임 통계 저장소의 런타임 실행 통계는 고정된 구성 가능한 시간 창을 통해 집계됩니다. 이러한 저장소의 정보는 쿼리 저장소 보기를 쿼리하여 표시됩니다.

다음 쿼리는 쿼리 저장소의 쿼리에 대한 정보를 반환합니다.

```sql
SELECT * FROM mysql.query_store;
```

또는 대기 통계에 대한 이 쿼리는 다음과 같은 것입니다.

```sql
SELECT * FROM mysql.query_store_wait_stats;
```

## <a name="finding-wait-queries"></a>대기 쿼리 찾기

> [!NOTE]
> 대기 통계는 최대 워크로드 시간 동안 사용하도록 설정하거나 중요한 워크로드의 경우 무기한 켜서는 안 됩니다. <br>CPU 사용률이 높거나 vCore가 낮은 서버로 구성된 서버에서 실행되는 워크로드의 경우 대기 통계를 사용하도록 설정할 때 주의해야 합니다. 무기한 켜서는 안 됩니다. 

대기 이벤트 유형은 유사성을 기준으로 서로 다른 대기 이벤트를 버킷으로 결합합니다. 쿼리 저장소는 대기 이벤트 유형, 특정 대기 이벤트 이름 및 해당하는 쿼리를 제공합니다. 이 대기 정보를 쿼리 런타임 통계와 상호 연관시킬 수 있다는 것은 쿼리 성능 특성에 영향을 미치는 내용을 더 잘 이해할 수 있음을 의미합니다.

다음은 쿼리 저장소의 대기 통계를 사용하여 워크로드에 대한 더 많은 통찰력을 얻을 수 있는 방법의 몇 가지 예입니다.

| **관찰** | **작업** |
|---|---|
|최고 잠금 대기 | 영향을 받는 쿼리에 대한 쿼리 텍스트를 확인하고 대상 엔터티를 식별합니다. 쿼리 저장소에서 자주 실행되거나 오래 실행되는 동일한 엔터티를 수정하는 다른 쿼리를 확인합니다. 이러한 쿼리를 식별한 후 애플리케이션 논리를 변경하여 동시성을 개선하거나 덜 제한적인 격리 수준을 사용하는 것이 좋습니다. |
|높은 버퍼 IO 대기 | 쿼리 저장소에서 물리적 읽기 횟수가 많은 쿼리를 찾습니다. 쿼리가 높은 IO 대기 대기와 일치하는 경우 기본 엔터티에 인덱스를 도입하여 검색 대신 검색을 수행하는 것이 좋습니다. 이렇게 하면 쿼리의 IO 오버헤드가 최소화됩니다. 포털에서 서버에 대한 **성능 권장 사항**을 확인하여 쿼리를 최적화하는 이 서버에 대한 인덱스 권장 사항이 있는지 확인합니다. |
|높은 메모리 대기 | 쿼리 저장소에서 메모리 사용량이 많은 상위 쿼리를 찾습니다. 이러한 쿼리는 영향을 받는 쿼리의 추가 진행을 지연시킬 수 있습니다. 포털에서 서버에 대한 **성능 권장 사항**을 확인하여 이러한 쿼리를 최적화하는 인덱스 권장 사항이 있는지 확인합니다.|

## <a name="configuration-options"></a>구성 옵션

쿼리 저장소를 사용하도록 설정하면 데이터가 15분 집계 창에 저장되고 각 창에는 최대 500개의 고유 쿼리가 포함됩니다.

다음 옵션은 쿼리 저장소 매개 변수를 구성하는 데 사용할 수 있습니다.

| **매개 변수** | **설명** | **기본** | **범위** |
|---|---|---|---|
| query_store_capture_mode | 값을 기반으로 쿼리 저장소 기능을 ON/OFF로 설정합니다. 참고: performance_schema 꺼져 있으면 query_store_capture_mode 켜면 performance_schema 켜지고 이 기능에 필요한 성능 스키마 계측기의 하위 집합이 켜집니다. | ALL | 없음, 모두 |
| query_store_capture_interval | 쿼리 저장소 캡처 간격을 몇 분 으로 저장합니다. 쿼리 메트릭이 집계되는 간격을 지정할 수 있습니다. | 15 | 5 - 60 |
| query_store_capture_utility_queries | 켜기 또는 끄기 시스템에서 실행되는 모든 유틸리티 쿼리를 캡처합니다. | 아니요 | 예, 아니요 |
| query_store_retention_period_in_days | 쿼리 저장소에 데이터를 유지 하려면 며칠 의 시간 창입니다. | 7 | 1 - 30 |

다음 옵션은 특히 대기 통계에 적용됩니다.

| **매개 변수** | **설명** | **기본** | **범위** |
|---|---|---|---|
| query_store_wait_sampling_capture_mode | 대기 통계를 켜고 끌 수 있습니다. | 없음 | 없음, 모두 |
| query_store_wait_sampling_frequency | 대기 샘플링 빈도를 초 단위로 변경합니다. 5~300초. | 30 | 5-300 |

> [!NOTE]
> 현재 **query_store_capture_mode** 이 구성을 대체하므로 **query_store_capture_mode** **query_store_wait_sampling_capture_mode** 모두 대기 통계가 작동하려면 ALL에 사용하도록 설정해야 합니다. **query_store_capture_mode** 꺼져 있으면 대기 통계가 performance_schema 사용하도록 설정하고 쿼리 저장소에서 캡처한 query_text 사용하므로 대기 통계도 꺼져 있습니다.

Azure [포털을](howto-server-parameters.md) 사용하여 매개 변수에 대해 다른 값을 얻거나 설정합니다.

## <a name="views-and-functions"></a>보기 및 함수

다음 보기 및 함수를 사용하여 쿼리 저장소를 보고 관리합니다. [선택 된 권한 공용 역할의](howto-create-users.md#create-additional-admin-users) 모든 사용자가 이러한 보기를 사용 하 여 쿼리 저장소에서 데이터를 볼 수 있습니다. 이러한 보기는 **mysql** 데이터베이스에서만 사용할 수 있습니다.

쿼리는 리터럴 및 상수를 제거한 후 구조를 확인하여 정규화됩니다. 리터럴 값을 제외하고 두 쿼리가 동일한 경우에는 동일한 해시를 포함합니다.

### <a name="mysqlquery_store"></a>mysql.query_store

이 보기는 쿼리 저장소의 모든 데이터를 반환합니다. 각 고유 데이터베이스 ID, 사용자 ID 및 쿼리 ID에 대한 하나의 행이 있습니다.

| **이름** | **데이터 유형** | **IS_NULLABLE** | **설명** |
|---|---|---|---|
| `schema_name`| 바르차르 (64) | 아니요 | 스키마 이름 |
| `query_id`| 빅트 (20) | 아니요| 특정 쿼리에 대해 생성된 고유 ID(동일한 쿼리가 다른 스키마에서 실행되는 경우 새 ID가 생성됩니다.) |
| `timestamp_id` | timestamp| 아니요| 쿼리가 실행되는 타임스탬프입니다. 이는 query_store_interval 구성을 기반으로 합니다.|
| `query_digest_text`| longtext| 아니요| 모든 리터럴을 제거한 후 정규화된 쿼리 텍스트|
| `query_sample_text` | longtext| 아니요| 리터럴을 가진 실제 쿼리의 첫 번째 모양|
| `query_digest_truncated` | bit| YES| 쿼리 텍스트가 잘렸는지 여부입니다. 쿼리가 1KB보다 길면 값이 예입니다.|
| `execution_count` | 빅트 (20)| 아니요| 구성된 간격 기간 동안 이 타임스탬프 ID에 대해 쿼리가 실행된 횟수입니다.|
| `warning_count` | 빅트 (20)| 아니요| 내부 동안 생성된 경고 수|
| `error_count` | 빅트 (20)| 아니요| 간격 동안 이 쿼리가 생성된 오류 수|
| `sum_timer_wait` | double| YES| 간격 동안 이 쿼리의 총 실행 시간|
| `avg_timer_wait` | double| YES| 간격 동안 이 쿼리의 평균 실행 시간|
| `min_timer_wait` | double| YES| 이 쿼리의 최소 실행 시간|
| `max_timer_wait` | double| YES| 최대 실행 시간|
| `sum_lock_time` | 빅트 (20)| 아니요| 이 시간 기간 동안 이 쿼리 실행에 대한 모든 잠금에 소요된 총 시간|
| `sum_rows_affected` | 빅트 (20)| 아니요| 행 수가 영향을 받음|
| `sum_rows_sent` | 빅트 (20)| 아니요| 클라이언트로 전송된 행 수|
| `sum_rows_examined` | 빅트 (20)| 아니요| 검사된 행 수|
| `sum_select_full_join` | 빅트 (20)| 아니요| 전체 조인 수|
| `sum_select_scan` | 빅트 (20)| 아니요| 선택 스캔 수 |
| `sum_sort_rows` | 빅트 (20)| 아니요| 정렬된 행 수|
| `sum_no_index_used` | 빅트 (20)| 아니요| 쿼리에서 인덱스를 사용하지 않은 횟수|
| `sum_no_good_index_used` | 빅트 (20)| 아니요| 쿼리 실행 엔진에서 좋은 인덱스를 사용하지 않은 횟수|
| `sum_created_tmp_tables` | 빅트 (20)| 아니요| 생성된 총 임시 테이블 수|
| `sum_created_tmp_disk_tables` | 빅트 (20)| 아니요| 디스크에서 생성된 총 임시 테이블 수(I/O 생성)|
| `first_seen` | timestamp| 아니요| 집계 창 동안 쿼리의 첫 번째 발생(UTC)|
| `last_seen` | timestamp| 아니요| 이 집계 기간 동안 쿼리의 마지막 발생(UTC)|

### <a name="mysqlquery_store_wait_stats"></a>mysql.query_store_wait_stats

이 보기는 쿼리 저장소의 대기 이벤트 데이터를 반환합니다. 각 고유 데이터베이스 ID, 사용자 ID, 쿼리 ID 및 이벤트에 대한 하나의 행이 있습니다.

| **이름**| **데이터 유형** | **IS_NULLABLE** | **설명** |
|---|---|---|---|
| `interval_start` | timestamp | 아니요| 간격의 시작(15분 증분)|
| `interval_end` | timestamp | 아니요| 간격 의 끝 (15 분 증분)|
| `query_id` | 빅트 (20) | 아니요| 정규화된 쿼리에서 고유 ID생성(쿼리 저장소에서)|
| `query_digest_id` |  varchar(32) | 아니요| 모든 리터럴을 제거한 후 정규화된 쿼리 텍스트(쿼리 저장소에서) |
| `query_digest_text` | longtext | 아니요| 리터럴이 있는 실제 쿼리의 첫 번째 모양(쿼리 저장소에서) |
| `event_type` |  varchar(32) | 아니요| 대기 이벤트의 범주 |
| `event_name` | varchar(128) | 아니요| 대기 이벤트의 이름 |
| `count_star` | 빅트 (20) | 아니요| 쿼리 간격 동안 샘플링된 대기 이벤트 수 |
| `sum_timer_wait_ms` | double | 아니요| 간격 동안 이 쿼리의 총 대기 시간(밀리초) |

### <a name="functions"></a>함수

| **이름**| **설명** |
|---|---|
| `mysql.az_purge_querystore_data(TIMESTAMP)` | 지정된 타임스탬프 전에 모든 쿼리 저장소 데이터를 제거합니다. |
| `mysql.az_procedure_purge_querystore_event(TIMESTAMP)` | 지정된 타임스탬프 전에 모든 대기 이벤트 데이터를 제거합니다. |
| `mysql.az_procedure_purge_recommendation(TIMESTAMP)` | 지정된 타임스탬프 이전에 만료되는 권장 사항을 제거합니다. |

## <a name="limitations-and-known-issues"></a>제한 사항 및 알려진 문제

- MariaDB 서버에 매개 변수가 `default_transaction_read_only` 있는 경우 쿼리 저장소에서 데이터를 캡처할 수 없습니다.
- 쿼리 저장소 기능은 긴 유니코드 쿼리(=\>6000바이트)가 발생하는 경우 중단될 수 있습니다.
- 대기 통계의 보존 기간은 24시간입니다.
- 대기 통계는 샘플 ti 캡처 이벤트의 일부를 사용합니다. 주파수는 매개 변수를 `query_store_wait_sampling_frequency`사용하여 수정할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [쿼리 성능 인사이트에](concepts-query-performance-insight.md) 대해 자세히 알아보기
