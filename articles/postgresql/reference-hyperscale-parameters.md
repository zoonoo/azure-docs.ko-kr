---
title: 서버 매개 변수 - 하이퍼스케일(Citus) - Azure Database for PostgreSQL
description: 하이퍼스케일(Citus) SQL API의 매개 변수
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: reference
ms.date: 08/10/2020
ms.openlocfilehash: 07f966c7b0be542f848f1a0a4eaf2b5549735b4b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "91336244"
---
# <a name="server-parameters"></a>서버 매개 변수

표준 PostgreSQL부터 하이퍼스케일(Citus)까지 하이퍼스케일(Citus)의 동작에 영향을 주는 다양한 서버 매개 변수가 있습니다.
이러한 매개 변수는 하이퍼스케일(Citus) 서버 그룹에 대한 Azure Portal에서 설정할 수 있습니다. **설정** 범주에서 **작업자 노드 매개 변수** 또는 **코디네이터 노드 매개 변수** 를 선택합니다. 이러한 페이지에서 모든 작업자 노드의 매개 변수를 설정하거나 코디네이터 노드의 매개 변수를 설정할 수 있습니다.

## <a name="hyperscale-citus-parameters"></a>하이퍼스케일(Citus) 매개 변수

> [!NOTE]
>
> 이전 버전의 Citus Engine을 실행하는 하이퍼스케일(Citus) 서버 그룹은 아래에 나열된 매개 변수 중 일부를 제공하지 않을 수 있습니다.

### <a name="general-configuration"></a>일반 구성

#### <a name="citususe_secondary_nodes-enum"></a>citus.use\_secondary\_nodes(열거형)

SELECT 쿼리에 대한 노드를 선택할 때 사용할 정책을 설정합니다. 'always'로 설정하면 플래너는 [pg_dist_node](reference-hyperscale-metadata.md#worker-node-table)에 'secondary' noderole이라고 표시된 노드만 쿼리합니다.

이 열거형을 지원하는 값은 다음과 같습니다.

-   **never**(기본값): 모든 읽기가 주 노드에서 수행됩니다.
-   **always**: 읽기가 보조 노드에 대해 실행되고 insert/update 문을 사용할 수 없습니다.

#### <a name="cituscluster_name-text"></a>citus.cluster\_name(텍스트)

코디네이터 노드 플래너에게 어떤 클러스터를 조정해야 하는지 알립니다. cluster\_name이 설정되면 플래너는 해당 클러스터의 작업자 노드만 쿼리합니다.

#### <a name="citusenable_version_checks-boolean"></a>citus.enable\_version\_checks(부울)

하이퍼스케일(Citus) 버전을 업그레이드하려면 서버를 다시 시작(새 공유 라이브러리를 선택하기 위해)한 다음, ALTER EXTENSION UPDATE 명령을 실행해야 합니다.  두 단계를 모두 실행하는 데 실패하면 오류 또는 충돌이 발생할 수 있습니다.
따라서 하이퍼스케일(Citus)은 코드 버전과 확장 버전이 일치하는지 검사하고, 일치하지 않으면 오류를 발생시킵니다.

이 값은 기본적으로 true이며 코디네이터에 적용됩니다. 드물긴 하지만 복잡한 업그레이드 프로세스에서 이 매개 변수를 false로 설정하여 검사를 사용하지 않도록 설정해야 하는 경우가 있습니다.

#### <a name="cituslog_distributed_deadlock_detection-boolean"></a>citus.log\_distributed\_deadlock\_detection(부울)

분산 교착 상태 검색 관련 처리를 서버 로그에 기록할지 여부를 지정합니다. 기본값은 false입니다.

#### <a name="citusdistributed_deadlock_detection_factor-floating-point"></a>citus.distributed\_deadlock\_detection\_factor(부동 소수점)

분산 교착 상태를 확인하기 전까지 대기하는 시간을 설정합니다. 특히 대기 시간은 이 값에 PostgreSQL의 [deadlock\_timeout](https://www.postgresql.org/docs/current/static/runtime-config-locks.html) 설정을 곱한 값입니다. 기본값은 `2`입니다. 값이 `-1`이면 분산 교착 상태 검색을 사용하지 않습니다.

#### <a name="citusnode_connection_timeout-integer"></a>citus.node\_connection\_timeout(정수)

`citus.node_connection_timeout` GUC는 연결 설정을 대기하는 최대 기간(밀리초)을 설정합니다. 하나 이상의 작업자 연결이 설정되기 전에 제한 시간이 초과되면 하이퍼스케일(Citus)에서 오류를 발생시킵니다. 이 GUC는 코디네이터와 작업자 간의 연결과 작업자 간의 연결에 영향을 줍니다.

-   기본값: 5초
-   최솟값: 10밀리초
-   최댓값: 1시간

```postgresql
-- set to 30 seconds
ALTER DATABASE foo
SET citus.node_connection_timeout = 30000;
```

### <a name="query-statistics"></a>쿼리 통계

#### <a name="citusstat_statements_purge_interval-integer"></a>citus.stat\_statements\_purge\_interval(정수)

유지 관리 디먼이 `pg_stat_statements`의 일치하지 않는 레코드를 [citus_stat_statements](reference-hyperscale-metadata.md#query-statistics-table)에서 제거하는 빈도를 설정합니다. 이 구성 값은 제거 사이의 시간 간격을 초 단위로 설정하며, 기본값은 10입니다. 값이 0이면 제거를 사용하지 않습니다.

```psql
SET citus.stat_statements_purge_interval TO 5;
```

이 매개 변수는 코디네이터에 적용되며 런타임에 변경할 수 있습니다.

### <a name="data-loading"></a>데이터 로드

#### <a name="citusmulti_shard_commit_protocol-enum"></a>citus.multi\_shard\_commit\_protocol(열거형)

해시 분산 테이블에서 COPY를 수행할 때 사용할 커밋 프로토콜을 설정합니다. 개별 익스텐트 배치에서, COPY를 실행하는 동안 오류가 발생하면 데이터를 수집하지 않도록 트랜잭션 블록에서 COPY가 수행됩니다. 그러나 모든 배치에서 COPY가 성공하지만 모든 트랜잭션을 커밋하기 전에 (하드웨어) 오류가 발생하는 특정 실패 사례가 있습니다. 이 매개 변수를 사용하면 다음 커밋 프로토콜 중에서 선택하여 데이터 손실을 방지할 수 있습니다.

-   **2pc**(기본값): 익스텐트 배치에서 COPY가 수행되는 트랜잭션은 먼저 PostgreSQL의 [2단계 커밋](http://www.postgresql.org/docs/current/static/sql-prepare-transaction.html)을 사용하여 준비된 후 커밋됩니다. COMMIT PREPARED 또는 ROLLBACK PREPARED를 사용하여 실패한 커밋을 수동으로 복구하거나 중단할 수 있습니다.
    2pc를 사용하는 경우 모든 작업자의 [max\_prepared\_transactions](http://www.postgresql.org/docs/current/static/runtime-config-resource.html)를 높여야 하며, max\_connections 값과 동일하게 맞추는 것이 일반적입니다.
-   **1pc**: 익스텐트 배치에서 COPY가 수행되는 트랜잭션은 단일 라운드로 커밋됩니다. 모든 배치에서 COPY가 성공한 후 커밋이 실패할 경우 데이터가 손실될 수 있습니다(드물게 발생함).

#### <a name="citusshard_replication_factor-integer"></a>citus.shard\_replication\_factor(정수)

익스텐트의 복제 계수, 즉, 익스텐트가 배치되는 노드 수를 설정하며, 기본값은 1입니다. 이 매개 변수는 런타임에 설정할 수 있으며 코디네이터에 적용됩니다. 이 매개 변수의 적절한 값은 클러스터의 크기와 노드 실패 비율에 따라 다릅니다.  예를 들어 큰 클러스터를 실행할 때 노드 오류가 더 많이 발생하는 경우에는 이 복제 계수를 높이는 것이 좋습니다.

#### <a name="citusshard_count-integer"></a>citus.shard\_count(정수)

해시 분할 테이블의 익스텐트를 설정하며, 기본값은 32입니다.  이 값은 해시 분할 테이블을 만들 때 [create_distributed_table](reference-hyperscale-functions.md#create_distributed_table)UDF에서 사용합니다. 이 매개 변수는 런타임에 설정할 수 있으며 코디네이터에 적용됩니다.

#### <a name="citusshard_max_size-integer"></a>citus.shard\_max\_size(정수)

익스텐트가 이 값에 도달하면 익스텐트를 분할하는 최대 크기를 설정하며, 기본값은 1GB입니다. 한 익스텐트의 원본 파일 크기(준비에 사용됨)가 이 구성 값을 초과하는 경우 데이터베이스는 새 익스텐트를 만들도록 합니다. 이 매개 변수는 런타임에 설정할 수 있으며 코디네이터에 적용됩니다.

### <a name="planner-configuration"></a>플래너 구성

#### <a name="cituslimit_clause_row_fetch_count-integer"></a>citus.limit\_clause\_row\_fetch\_count(정수)

limit 절 최적화를 위해 작업당 가져올 행 수를 설정합니다.
경우에 따라 limit 절이 있는 쿼리를 선택하여 각 작업의 모든 행을 가져와야 할 수도 있습니다. 이 경우 그리고 근사값이 의미 있는 결과를 생성하는 경우 이 구성 값은 각 익스텐트에서 가져오는 행 수를 설정합니다. 제한 근사치는 기본적으로 사용되지 않으며 이 매개 변수는 -1로 설정됩니다. 이 값은 런타임에 설정할 수 있으며 코디네이터에 적용됩니다.

#### <a name="cituscount_distinct_error_rate-floating-point"></a>citus.count\_distinct\_error\_rate(부동 소수점)

하이퍼스케일(Citus)은 postgresql-hll 확장을 사용하여 개수(고유) 근사치를 계산할 수 있습니다. 이 구성 항목은 개수(고유)를 계산할 때 원하는 오류 발생률을 설정합니다. 기본값인 0.0은 개수(고유)에 근사치를 사용하지 않고, 1.0은 결과의 정확도를 보장하지 않습니다. 최상의 결과를 얻으려면 이 매개 변수를 0.005로 설정하는 것이 좋습니다. 이 값은 런타임에 설정할 수 있으며 코디네이터에 적용됩니다.

#### <a name="citustask_assignment_policy-enum"></a>citus.task\_assignment\_policy(열거형)

> [!NOTE]
> 이 GUC는 [shard_replication_factor](reference-hyperscale-parameters.md#citusshard_replication_factor-integer)가 1보다 크거나 [reference_tables](concepts-hyperscale-distributed-data.md#type-2-reference-tables)에 대한 쿼리인 경우에만 적용됩니다.

작업자에게 작업을 할당할 때 사용할 정책을 설정합니다. 코디네이터는 익스텐트 위치에 따라 작업자에게 작업을 할당합니다. 이 구성 값은 이처럼 작업을 할당할 때 사용할 정책을 지정합니다.
현재 사용할 수 있는 세 가지 작업 할당 정책이 있습니다.

-   **최대:** 최대 정책은 기본값이며 모든 작업자에게 작업을 균등하게 분산하는 것을 목표로 합니다.
-   **라운드 로빈:** 라운드 로빈 정책은 여러 복제본 간에 반복되는 라운드 로빈 방식으로 작업자에게 작업을 할당합니다. 이 정책을 사용하면 테이블의 익스텐트 수가 작업자 수보다 적을 때 클러스터 사용률이 향상됩니다.
-   **첫 번째 복제본:** 첫 번째 복제본 정책은 익스텐트의 배치(복제본) 삽입 순서를 기준으로 작업을 할당합니다. 즉, 익스텐트의 조각 쿼리는 해당 익스텐트의 첫 번째 복제본이 있는 작업자에게 할당됩니다.
    이 방법을 사용하면 어떤 노드에서 어떤 익스텐트가 사용되는지 명확하게 보장(즉, 더 강력한 메모리 상주 보장)할 수 있습니다.

이 매개 변수는 런타임에 설정할 수 있으며 코디네이터에 적용됩니다.

### <a name="intermediate-data-transfer"></a>중간 데이터 전송

#### <a name="citusbinary_worker_copy_format-boolean"></a>citus.binary\_worker\_copy\_format(부울)

이진 복사본 형식을 사용하여 작업자 간에 중간 데이터를 전송합니다.
대형 테이블 조인 중에 하이퍼스케일(Citus)은 여러 작업자 간에 데이터를 동적으로 다시 분할하고 순서를 다시 조정해야 할 수 있습니다. 기본적으로 이 데이터는 텍스트 형식으로 전송됩니다. 이 매개 변수를 사용하면 데이터베이스가 PostgreSQL의 이진 serialization 형식을 사용하여 이 데이터를 전송합니다. 이 매개 변수는 작업자에게 적용되며 postgresql.conf 파일에서 변경해야 합니다. 구성 파일을 편집한 후 사용자는 SIGHUP 신호를 보내거나 서버를 다시 시작하여 변경 내용을 적용할 수 있습니다.

#### <a name="citusbinary_master_copy_format-boolean"></a>citus.binary\_master\_copy\_format(부울)

이진 복사 형식을 사용하여 코디네이터와 작업자 간에 데이터를 전송합니다. 분산 쿼리를 실행하는 경우 작업자는 최종 집계를 위해 코디네이터에게 중간 결과를 전송합니다. 기본적으로 이 데이터는 텍스트 형식으로 전송됩니다. 이 매개 변수를 사용하면 데이터베이스가 PostgreSQL의 이진 serialization 형식을 사용하여 이 데이터를 전송합니다.
이 매개 변수는 런타임에 설정할 수 있으며 코디네이터에 적용됩니다.

#### <a name="citusmax_intermediate_result_size-integer"></a>citus.max\_intermediate\_result\_size(정수)

실행 또는 복합 하위 쿼리에 대한 작업자 노드로 푸시할 수 없는 CTE의 최대 중간 결과 크기(KB)입니다. 기본값은 1GB이며, 값이 -1이면 제한이 없다는 뜻입니다.
제한을 초과하는 쿼리는 취소되고 오류 메시지가 생성됩니다.

### <a name="ddl"></a>DDL

#### <a name="citusenable_ddl_propagation-boolean"></a>citus.enable\_ddl\_propagation(부울)

코디네이터의 DDL 변경 내용을 모든 작업자에게 자동으로 전파할 것인지 여부를 지정합니다. 기본값은 true입니다. 일부 스키마를 변경하려면 테이블에 대한 액세스 배타적 잠금이 필요하며 자동 전파는 모든 작업자에게 순차적으로 적용되기 때문에 하이퍼스케일(Citus) 클러스터의 응답성이 일시적으로 저하될 수 있습니다. 이 설정을 사용하지 않도록 설정하고 변경 내용을 수동으로 전파할 수 있습니다.

### <a name="executor-configuration"></a>실행기 구성

#### <a name="general"></a>일반

##### <a name="citusall_modifications_commutative"></a>citus.all\_modifications\_commutative

하이퍼스케일(Citus)이 동작의 정확성을 보장하기 위해 연산의 교환 규칙을 적용하고 수정 작업에 대한 적절한 잠금을 획득합니다. 예를 들어 INSERT 문이 다른 INSERT 문과 교환되지만 UPDATE 또는 DELETE 문과는 교환되지 않는 것으로 가정합니다. 마찬가지로 UPDATE 또는 DELETE 문이 다른 UPDATE 또는 DELETE 문과 교환되지 않는 것으로 가정합니다. 이러한 예방 조치는 UPDATE 및 DELETE 문이 더 강력한 잠금을 획득하려면 하이퍼스케일(Citus)이 필요하다는 것을 의미합니다.

INSERT 또는 다른 UPDATE 문과 교환되는 UPDATE 문이 있는 경우 이 매개 변수를 true로 설정하여 이러한 교환 가정을 완화할 수 있습니다. 이 매개 변수를 true로 설정하면 모든 명령을 교환되는 것으로 간주하고 공유 잠금을 요청하며, 이를 통해 전체 처리량이 향상될 수 있습니다. 이 매개 변수는 런타임에 설정할 수 있으며 코디네이터에 적용됩니다.

##### <a name="citusremote_task_check_interval-integer"></a>citus.remote\_task\_check\_interval(정수)

하이퍼스케일(Citus)이 작업 추적기 실행기에서 관리하는 작업의 상태를 확인하는 빈도를 설정합니다. 기본값은 10ms입니다. 코디네이터는 작업자에게 작업을 할당한 다음, 각 작업의 진행 상황을 정기적으로 확인합니다. 이 구성 값은 두 검사 간의 시간 간격을 설정합니다. 이 매개 변수는 코디네이터에 적용되며 런타임에 설정할 수 있습니다.

##### <a name="citustask_executor_type-enum"></a>citus.task\_executor\_type(열거형)

하이퍼스케일(Citus)에는 분산 SELECT 쿼리를 실행하는 세 가지 유형의 실행기가 있습니다.  이 구성 매개 변수를 설정하여 원하는 실행기를 선택할 수 있습니다. 이 매개 변수에 허용되는 값은 다음과 같습니다.

-   **adaptive**: 기본값입니다. 여러 익스텐트에 걸쳐 있는 집계 및 공동 배치 조인이 포함된 쿼리에 빠르게 응답하는 데 적합합니다.
-   **task-tracker**: 작업 추적기 실행기는 작업자 노드 전체에서 데이터 순서를 섞어야 하고 효율적인 리소스 관리가 필요한 복잡하고 오래 실행되는 쿼리에 적합합니다.
-   **real-time**(사용되지 않음): 적응형 실행기와 비슷한 용도로 사용되지만 유연성이 떨어지고 작업자 노드의 연결 압력이 가중될 수 있습니다.

이 매개 변수는 런타임에 설정할 수 있으며 코디네이터에 적용됩니다.

##### <a name="citusmulti_task_query_log_level-enum-multi_task_logging"></a>citus.multi\_task\_query\_log\_level(열거형) {#multi_task_logging}

둘 이상의 작업을 생성하는(즉, 일치하는 익스텐트가 2개 이상 있는) 쿼리의 로그 수준을 설정합니다. 로깅은 다중 테넌트 애플리케이션을 마이그레이션하는 동안 유용합니다. 이러한 쿼리에 대한 오류 또는 경고를 선택하여 다중 테넌트 애플리케이션을 찾고 tenant\_id 필터를 추가할 수 있습니다. 이 매개 변수는 런타임에 설정할 수 있으며 코디네이터에 적용됩니다. 이 매개 변수의 기본값은 \'off\'입니다.

이 열거형을 지원하는 값은 다음과 같습니다.

-   **off:** 여러 작업을 생성하는(즉, 여러 익스텐트에 걸쳐 있는) 쿼리의 로깅을 해제합니다.
-   **debug:** DEBUG 심각도 수준에서 명령문을 기록합니다.
-   **log:** LOG 심각도 수준에서 명령문을 기록합니다. 로그 줄에는 실행된 SQL 쿼리가 포함됩니다.
-   **notice:** NOTICE 심각도 수준에서 명령문을 기록합니다.
-   **NOTICE :** WARNING 심각도 수준에서 명령문을 기록합니다.
-   **error:** ERROR 심각도 수준에서 명령문을 기록합니다.

개발 테스트 중에는 `error`를 사용하고 실제 프로덕션 배포 중에는 `log`처럼 더 낮은 로그 수준을 사용하는 것이 좋을 수 있습니다.
`log`를 선택하면 다중 작업 쿼리가 데이터베이스 로그에 표시되고 쿼리 자체가 \"STATEMENT.\" 뒤에 표시됩니다.

```
LOG:  multi-task query about to be executed
HINT:  Queries are split to multiple tasks if they have to be split into several queries on the workers.
STATEMENT:  select * from foo;
```

##### <a name="citusenable_repartition_joins-boolean"></a>citus.enable\_repartition\_joins(부울)

일반적으로 적응형 실행기를 사용하여 다시 분할 조인을 시도하면 오류 메시지와 함께 실패합니다.  그러나 `citus.enable_repartition_joins`를 true로 설정하면 하이퍼스케일(Citus)이 일시적으로 작업 추적기 실행기로 전환하여 조인을 수행할 수 있습니다.  기본값은 false입니다.

#### <a name="task-tracker-executor-configuration"></a>작업 추적기 실행기 구성

##### <a name="citustask_tracker_delay-integer"></a>citus.task\_tracker\_delay(정수)

이 매개 변수는 작업 관리 라운드 사이의 작업 추적기 절전 모드 시간을 설정하며 기본값은 200ms입니다. 작업 추적기 프로세스는 정기적으로 절전 모드를 해제하고, 할당된 모든 작업을 검토하고, 이러한 작업을 예약하고 실행합니다.  그런 다음, 작업 추적기는 이러한 작업을 다시 검토하기 전까지 절전 모드로 전환됩니다.
이 구성 값은 절전 모드 기간을 결정합니다. 이 매개 변수는 작업자에게 적용되며 postgresql.conf 파일에서 변경해야 합니다. 구성 파일을 편집한 후 사용자는 SIGHUP 신호를 보내거나 서버를 다시 시작하여 변경 내용을 적용할 수 있습니다.

이 매개 변수를 줄이면 관리 라운드 간의 시간 간격을 줄여서 작업 추적기 실행기 때문에 발생하는 지연 시간을 줄일 수 있습니다.
익스텐트 쿼리가 짧아서 상태가 정기적으로 업데이트되는 경우 지연 시간을 줄이면 도움이 됩니다.

##### <a name="citusmax_assign_task_batch_size-integer"></a>citus.max\_assign\_task\_batch\_size(정수)

코디네이터의 작업 추적기 실행기는 작업자의 디먼에 작업을 동기적으로 일괄 할당합니다. 이 매개 변수는 단일 일괄 처리에서 할당하는 최대 작업 수를 설정합니다. 일괄 처리 크기를 높게 선택하면 작업을 더 빠르게 할당할 수 있습니다. 그러나 작업자 수가 많으면 모든 작업자가 작업을 가져오는 데 걸리는 시간이 길어질 수 있습니다.  이 매개 변수는 런타임에 설정할 수 있으며 코디네이터에 적용됩니다.

##### <a name="citusmax_running_tasks_per_node-integer"></a>citus.max\_running\_tasks\_per\_node(정수)

작업 추적기 프로세스는 할당된 작업을 적절하게 예약하고 실행합니다. 이 구성 값은 지정된 시간에 한 노드에서 동시에 실행할 최대 작업 수를 설정하며 기본값은 8입니다.

여러 작업이 동시에 디스크를 사용하지 않도록 제한하여 디스크 I/O 경합을 방지할 수 있습니다. 쿼리가 메모리 또는 SSD에서 제공되는 경우 큰 걱정 없이 max\_running\_tasks\_per\_node를 늘릴 수 있습니다.

##### <a name="cituspartition_buffer_size-integer"></a>citus.partition\_buffer\_size(정수)

파티션 작업에 사용할 버퍼 크기를 설정하며 기본값은 8MB입니다.
하이퍼스케일(Citus)을 사용하면 두 개의 큰 테이블이 조인될 때 테이블 데이터를 여러 파일로 다시 분할할 수 있습니다. 이 파티션 버퍼가 꽉 차면 다시 분할된 데이터가 디스크의 파일로 플러시됩니다.  이 구성 항목은 런타임에 설정할 수 있으며 작업자에게 적용됩니다.

#### <a name="explain-output"></a>설명 출력

##### <a name="citusexplain_all_tasks-boolean"></a>citus.explain\_all\_tasks(부울)

기본적으로 하이퍼스케일(Citus)은 분산 쿼리에서 [EXPLAIN](http://www.postgresql.org/docs/current/static/sql-explain.html)을 실행할 때 단일 임의 작업의 출력을 표시합니다. 대부분의 경우 설명 출력은 작업 간에 유사합니다. 경우에 따라 일부 작업이 다르게 계획되거나 실행 시간이 훨씬 더 높을 수 있습니다. 이 경우 이 매개 변수를 사용하도록 설정하면 EXPLAIN 출력에 모든 작업이 포함되므로 도움이 될 수 있습니다. 모든 작업을 설명하면 EXPLAIN이 오래 걸릴 수 있습니다.

## <a name="postgresql-parameters"></a>PostgreSQL 매개 변수

* [DateStyle](https://www.postgresql.org/docs/current/datatype-datetime.html#DATATYPE-DATETIME-OUTPUT) - 날짜 및 시간 값의 표시 형식 설정
* [IntervalStyle](https://www.postgresql.org/docs/current/datatype-datetime.html#DATATYPE-INTERVAL-OUTPUT) - 간격 값의 표시 형식 설정
* [TimeZone](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-TIMEZONE) - 타임스탬프를 표시하고 해석하는 표준 시간대 설정
* [application_name](https://www.postgresql.org/docs/current/runtime-config-logging.html#GUC-APPLICATION-NAME) - 통계 및 로그에 보고할 애플리케이션 이름 설정
* [array_nulls](https://www.postgresql.org/docs/current/runtime-config-compatible.html#GUC-ARRAY-NULLS) - 배열에 NULL 요소 입력을 사용하도록 설정
* [autovacuum](https://www.postgresql.org/docs/current/runtime-config-autovacuum.html#GUC-AUTOVACUUM) - 자동 진공 하위 프로세스를 시작
* [autovacuum_analyze_scale_factor](https://www.postgresql.org/docs/current/runtime-config-autovacuum.html#GUC-AUTOVACUUM-ANALYZE-SCALE-FACTOR) - reltuples의 분수로 표시되는 분석 전의 튜플 삽입, 업데이트 또는 삭제 수
* [autovacuum_analyze_threshold](https://www.postgresql.org/docs/current/runtime-config-autovacuum.html#GUC-AUTOVACUUM-ANALYZE-THRESHOLD) - 분석 전의 최소 튜플 삽입, 업데이트 또는 삭제 수
* [autovacuum_naptime](https://www.postgresql.org/docs/current/runtime-config-autovacuum.html#GUC-AUTOVACUUM-NAPTIME) - 자동 진공 실행 사이의 일시 중지 시간
* [autovacuum_vacuum_cost_delay](https://www.postgresql.org/docs/current/runtime-config-autovacuum.html#GUC-AUTOVACUUM-VACUUM-COST-DELAY) - 자동 진공의 진공 비용 지연(밀리초)
* [autovacuum_vacuum_cost_limit](https://www.postgresql.org/docs/current/runtime-config-autovacuum.html#GUC-AUTOVACUUM-VACUUM-COST-LIMIT) - 내핑 전에 자동 진공에 사용할 수 있는 진공 비용 금액
* [autovacuum_vacuum_scale_factor](https://www.postgresql.org/docs/current/runtime-config-autovacuum.html#GUC-AUTOVACUUM-VACUUM-SCALE-FACTOR) - reltuples의 분수로 표시되는 진공 전의 튜플 업데이트 또는 삭제 수
* [autovacuum_vacuum_threshold](https://www.postgresql.org/docs/current/runtime-config-autovacuum.html#GUC-AUTOVACUUM-VACUUM-THRESHOLD) - 진공 전의 최소 튜플 업데이트 또는 삭제 수
* [autovacuum_work_mem](https://www.postgresql.org/docs/current/runtime-config-resource.html#GUC-AUTOVACUUM-WORK-MEM) - 각 자동 진공 작업자 프로세스에서 사용할 최대 메모리 설정
* [backend_flush_after](https://www.postgresql.org/docs/current/runtime-config-resource.html#GUC-BACKEND-FLUSH-AFTER) - 이전에 수행한 쓰기가 디스크로 플러시되는 페이지 수
* [backslash_quote](https://www.postgresql.org/docs/current/runtime-config-compatible.html#GUC-BACKSLASH-QUOTE) - 문자열 리터럴에서 "\'"가 허용되는지 설정
* [bgwriter_delay](https://www.postgresql.org/docs/current/runtime-config-resource.html#GUC-BGWRITER-DELAY) - 라운드 사이의 백그라운드 기록기 일시 중지 시간
* [backend_flush_after](https://www.postgresql.org/docs/current/runtime-config-resource.html#GUC-BGWRITER-FLUSH-AFTER) - 이전에 수행한 쓰기가 디스크로 플러시되는 페이지 수
* [bgwriter_lru_maxpages](https://www.postgresql.org/docs/current/runtime-config-resource.html#GUC-BGWRITER-LRU-MAXPAGES) - 라운드마다 플러시할 백그라운드 기록기 최대 LRU 페이지 수
* [bgwriter_lru_multiplier](https://www.postgresql.org/docs/current/runtime-config-resource.html#GUC-BGWRITER-LRU-MULTIPLIER) - 라운드마다 확보할 평균 버퍼 사용량의 배수
* [bytea_output](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-BYTEA-OUTPUT) - bytea의 출력 형식 설정
* [check_function_bodies](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-CHECK-FUNCTION-BODIES) - CREATE FUNCTION을 실행하는 동안 함수 본문을 검사합니다.
* [checkpoint_completion_target](https://www.postgresql.org/docs/current/runtime-config-wal.html#GUC-CHECKPOINT-COMPLETION-TARGET) - 검사점 간격의 분수로 표시하는 검사점 중 더티 버퍼를 플러시하는 데 소요된 시간
* [checkpoint_timeout](https://www.postgresql.org/docs/current/runtime-config-wal.html#GUC-CHECKPOINT-TIMEOUT) - 자동 WAL 검사점 사이의 최대 시간을 설정합니다.
* [checkpoint_warning](https://www.postgresql.org/docs/current/runtime-config-wal.html#GUC-CHECKPOINT-WARNING) - 검사점 세그먼트가 이 값보다 자주 채워지는 경우 경고를 사용합니다.
* [client_encoding](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-CLIENT-ENCODING) - 클라이언트의 문자 집합 인코딩을 설정합니다.
* [client_min_messages](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-CLIENT-MIN-MESSAGES) - 클라이언트에 전송되는 메시지 수준을 설정합니다.
* [commit_delay](https://www.postgresql.org/docs/current/runtime-config-wal.html#GUC-COMMIT-DELAY) - 트랜잭션 커밋과 디스크에 대한 WAL 플러시 사이의 지연 시간(마이크로초)을 설정합니다.
* [commit_siblings](https://www.postgresql.org/docs/12/runtime-config-wal.html#GUC-COMMIT-SIBLINGS) - commit_delay를 수행하기 전의 최소 동시 열린 트랜잭션 수를 설정
* [constraint_exclusion](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-CONSTRAINT-EXCLUSION) - 플래너가 제약 조건을 사용하여 쿼리를 최적화할 수 있도록 설정
* [cpu_index_tuple_cost](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-CPU-INDEX-TUPLE-COST) - 인덱스 검사 중에 플래너가 각 인덱스 항목을 처리하는 데 들 것으로 예상되는 비용 설정
* [cpu_operator_cost](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-CPU-OPERATOR-COST) - 플래너가 각 연산자 또는 함수 호출을 처리하는 데 들 것으로 예상되는 비용 설정
* [cpu_tuple_cost](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-CPU-TUPLE-COST) - 플래너가 각 튜플(행)을 처리하는 데 들 것으로 예상되는 비용 설정(낮게)
* [cursor_tuple_fraction](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-CURSOR-TUPLE-FRACTION) - 플래너가 검색할 커서의 예상 행 분수 설정
* [deadlock_timeout](https://www.postgresql.org/docs/current/runtime-config-locks.html#GUC-DEADLOCK-TIMEOUT) - 교착 상태를 확인하기 전에 잠금을 대기하는 시간의 양(밀리초)을 설정합니다.
* [debug_pretty_print](https://www.postgresql.org/docs/current/runtime-config-logging.html#id-1.6.6.11.5.2.3.1.3) - 구문 분석 및 계획 트리 표시 들여쓰기
* [debug_print_parse](https://www.postgresql.org/docs/current/runtime-config-logging.html#id-1.6.6.11.5.2.2.1.3) - 각 쿼리의 구문 분석 트리를 기록
* [debug_print_plan](https://www.postgresql.org/docs/current/runtime-config-logging.html#id-1.6.6.11.5.2.2.1.3) - 각 쿼리의 실행 계획을 기록
* [debug_print_rewritten](https://www.postgresql.org/docs/current/runtime-config-logging.html#id-1.6.6.11.5.2.2.1.3) - 각 쿼리의 다시 작성한 구문 분석 트리를 기록
* [default_statistics_target](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-DEFAULT-STATISTICS-TARGET) - 기본 통계 대상 설정
* [default_tablespace](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-DEFAULT-TABLESPACE) - 테이블 및 인덱스를 만들 기본 테이블스페이스 설정
* [default_text_search_config](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-DEFAULT-TEXT-SEARCH-CONFIG) - 기본 텍스트 검색 구성 설정
* [default_transaction_deferrable](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-DEFAULT-TRANSACTION-DEFERRABLE) - 새 트랜잭션의 기본 연기 가능 상태 설정
* [default_transaction_isolation](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-DEFAULT-TRANSACTION-ISOLATION) - 각 신규 트랜잭션의 트랜잭션 격리 수준 설정
* [default_transaction_read_only](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-DEFAULT-TRANSACTION-READ-ONLY) - 새 트랜잭션의 기본 읽기 전용 상태 설정
* default_with_oids - 기본적으로 OID를 사용하여 새 테이블 작성
* [effective_cache_size](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-EFFECTIVE-CACHE-SIZE) - 디스크 캐시 크기에 대한 플래너의 가정 설정
* [enable_bitmapscan](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-ENABLE-BITMAPSCAN) - 플래너가 비트맵 스캔 계획을 사용하도록 설정
* [enable_gathermerge](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-ENABLE-GATHERMERGE) - 플래너가 수집 병합 계획을 사용하도록 설정
* [enable_hashagg](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-ENABLE-HASHAGG) - 플래너가 해시된 집계 계획을 사용하도록 설정
* [enable_hashjoin](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-ENABLE-HASHJOIN) - 플래너가 해시 조인 계획을 사용하도록 설정
* [enable_indexonlyscan](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-ENABLE-INDEXONLYSCAN) - 플래너가 인덱스 전용 스캔 계획을 사용하도록 설정
* [enable_indexscan](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-ENABLE-INDEXSCAN) - 플래너가 인덱스 스캔 계획을 사용하도록 설정
* [enable_material](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-ENABLE-MATERIAL) - 플래너가 구체화를 사용하도록 설정
* [enable_mergejoin](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-ENABLE-MERGEJOIN) - 플래너가 병합 조인 계획을 사용하도록 설정
* [enable_nestloop](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-ENABLE-NESTLOOP) - 플래너가 중첩된 루프 조인 계획을 사용하도록 설정
* [enable_seqscan](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-ENABLE-SEQSCAN) - 플래너가 순차적 스캔 계획을 사용하도록 설정
* [enable_sort](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-ENABLE-SORT) - 플래너가 명시적 정렬 단계를 사용하도록 설정
* [enable_tidscan](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-ENABLE-TIDSCAN) - 플래너가 TID 스캔 계획을 사용하도록 설정
* [escape_string_warning](https://www.postgresql.org/docs/current/runtime-config-compatible.html#GUC-ESCAPE-STRING-WARNING) - 일반 문자열 리터럴의 백슬래시 이스케이프에 대해 경고
* [exit_on_error](https://www.postgresql.org/docs/current/runtime-config-error-handling.html#GUC-EXIT-ON-ERROR) - 오류 발생 시 세션 종료
* [extra_float_digits](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-EXTRA-FLOAT-DIGITS) - 부동 소수점 값에 대해 표시되는 자릿수 설정
* [force_parallel_mode](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-FORCE-PARALLEL-MODE) - 병렬 쿼리 기능을 사용하도록 강제
* [from_collapse_limit](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-FROM-COLLAPSE-LIMIT) - 하위 쿼리가 축소되지 않는 FROM-list 크기 설정
* [geqo](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-GEQO) - 유전자 쿼리 최적화 사용
* [geqo_effort](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-GEQO-EFFORT) - GEQO: effort를 사용하여 다른 GEQO 매개 변수의 기본값 설정
* [geqo_generations](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-GEQO-GENERATIONS) - GEQO: 알고리즘의 반복 횟수
* [geqo_pool_size](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-GEQO-POOL-SIZE) - GEQO: 모집단의 개인 수
* [geqo_seed](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-GEQO-SEED) - GEQO: 임의의 경로를 선택하기 위한 시드
* [geqo_selection_bias](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-GEQO-SELECTION-BIAS) - GEQO: 모집단 내의 선택적 압력
* [geqo_threshold](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-GEQO-THRESHOLD) - GEQO가 사용되는 FROM 항목의 임계값 설정
* [gin_fuzzy_search_limit](https://www.postgresql.org/docs/current/runtime-config-client.html#id-1.6.6.14.5.2.2.1.3) - 정확한 검색을 위해 GIN에서 허용되는 최대 결과 설정
* [gin_pending_list_limit](https://www.postgresql.org/docs/current/runtime-config-client.html#id-1.6.6.14.2.2.23.1.3) - GIN 인덱스에 대한 보류 중인 목록의 최대 크기 설정
* [idle_in_transaction_session_timeout](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-IDLE-IN-TRANSACTION-SESSION-TIMEOUT) - 유휴 트랜잭션의 최대 허용 기간 설정
* [join_collapse_limit](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-JOIN-COLLAPSE-LIMIT) - JOIN 구문이 평면화되지 않는 FROM-list 크기 설정
* [lc_monetary](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-LC-MONETARY) - 통화 금액의 서식을 지정하기 위한 로캘 설정
* [lc_numeric](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-LC-NUMERIC) - 숫자 서식을 지정하기 위한 로캘 설정
* [lo_compat_privileges](https://www.postgresql.org/docs/current/runtime-config-compatible.html#GUC-LO-COMPAT-PRIVILEGES) - 큰 개체의 권한 검사에 이전 버전과의 호환성 모드를 사용하도록 설정
* [lock_timeout](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-LOCK-TIMEOUT) - 잠금 대기에 대해 허용되는 최대 기간(밀리초) 설정. 0은 이 기능 끄기
* [log_autovacuum_min_duration](https://www.postgresql.org/docs/current/runtime-config-autovacuum.html#) - 자동 진공 작업이 기록되는 최소 실행 시간 설정
* [log_checkpoints](https://www.postgresql.org/docs/current/runtime-config-logging.html#GUC-LOG-CHECKPOINTS) - 각 검사점을 로깅
* [log_connections](https://www.postgresql.org/docs/current/runtime-config-logging.html#GUC-LOG-CONNECTIONS) - 성공한 각 연결을 로깅
* [log_destination](https://www.postgresql.org/docs/current/runtime-config-logging.html#GUC-LOG-DESTINATION) - 서버 로그 출력의 대상 설정
* [log_disconnections](https://www.postgresql.org/docs/current/runtime-config-logging.html#GUC-LOG-DISCONNECTIONS) - 기간을 포함하여 세션 종료를 로깅
* [log_duration](https://www.postgresql.org/docs/current/runtime-config-logging.html#GUC-LOG-DURATION) - 완료된 각 SQL 문의 기간을 로깅
* [log_error_verbosity](https://www.postgresql.org/docs/current/runtime-config-logging.html#GUC-LOG-ERROR-VERBOSITY) - 로깅된 메시지의 세부 정보 표시 설정
* [log_lock_waits](https://www.postgresql.org/docs/current/runtime-config-logging.html#GUC-LOG-LOCK-WAITS) - 긴 잠금 대기를 로깅
* [log_min_duration_statement](https://www.postgresql.org/docs/current/runtime-config-logging.html#GUC-LOG-MIN-DURATION-STATEMENT) - 명령문을 로깅하는 최소 실행 시간(밀리초) 설정. -1은 명령문 로깅 기간을 사용하지 않음
* [log_min_error_statement](https://www.postgresql.org/docs/current/runtime-config-logging.html#GUC-LOG-MIN-ERROR-STATEMENT) - 이 수준 이상에서 오류를 생성하는 모든 명령문을 로깅
* [log_min_messages](https://www.postgresql.org/docs/current/runtime-config-logging.html#GUC-LOG-MIN-MESSAGES) - 로깅되는 메시지 수준 설정
* [log_replication_commands](https://www.postgresql.org/docs/current/runtime-config-logging.html#GUC-LOG-REPLICATION-COMMANDS) - 각 복제 명령을 로깅
* [log_statement](https://www.postgresql.org/docs/current/runtime-config-logging.html#GUC-LOG-STATEMENT) - 로깅하는 명령문의 유형 설정
* [log_statement_stats](https://www.postgresql.org/docs/current/runtime-config-statistics.html#id-1.6.6.12.3.2.1.1.3) - 각 쿼리의 누적 성능 통계를 서버 로그에 작성
* [log_temp_files](https://www.postgresql.org/docs/current/runtime-config-logging.html#GUC-LOG-TEMP-FILES) - 이 값(kb)보다 큰 임시 파일 사용을 로깅
* [maintenance_work_mem](https://www.postgresql.org/docs/current/runtime-config-resource.html#GUC-MAINTENANCE-WORK-MEM) - 유지 관리 작업에 사용할 최대 메모리 설정
* [max_parallel_workers](https://www.postgresql.org/docs/current/runtime-config-resource.html#GUC-MAX-PARALLEL-WORKERS) - 동시에 활성화할 수 있는 최대 병렬 작업자 수 설정
* [max_parallel_workers_per_gather](https://www.postgresql.org/docs/current/runtime-config-resource.html#GUC-MAX-PARALLEL-WORKERS-PER-GATHER) - 실행기 노드당 최대 병렬 프로세스 수 설정
* [max_pred_locks_per_page](https://www.postgresql.org/docs/current/runtime-config-locks.html#GUC-MAX-PRED-LOCKS-PER-PAGE) - 페이지당 최대 조건자 잠금 튜플 수 설정
* [max_pred_locks_per_relation](https://www.postgresql.org/docs/current/runtime-config-locks.html#GUC-MAX-PRED-LOCKS-PER-RELATION) - 관계당 최대 조건자 잠금 페이지 및 튜플 수 설정
* [max_standby_archive_delay](https://www.postgresql.org/docs/current/runtime-config-replication.html#GUC-MAX-STANDBY-ARCHIVE-DELAY) - 상시 대기 서버가 보관된 WAL 데이터를 처리할 때 쿼리가 취소되는 최대 지연 시간 설정
* [max_standby_streaming_delay](https://www.postgresql.org/docs/current/runtime-config-replication.html#GUC-MAX-STANDBY-STREAMING-DELAY) - 상시 대기 서버가 스트리밍된 WAL 데이터를 처리할 때 쿼리가 취소되는 최대 지연 시간 설정
* [max_sync_workers_per_subscription](https://www.postgresql.org/docs/current/runtime-config-replication.html#GUC-MAX-SYNC-WORKERS-PER-SUBSCRIPTION) - 구독당 최대 테이블 동기화 작업자 수
* [max_wal_size](https://www.postgresql.org/docs/current/runtime-config-wal.html#GUC-MAX-WAL-SIZE) - 검사점을 트리거하는 WAL 크기 설정
* [min_parallel_index_scan_size](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-MIN-PARALLEL-INDEX-SCAN-SIZE) - 병렬 스캔의 최소 인덱스 데이터 크기 설정
* [min_wal_size](https://www.postgresql.org/docs/current/runtime-config-wal.html#GUC-MIN-WAL-SIZE) - WAL을 축소하는 최소 크기 설정
* [operator_precedence_warning](https://www.postgresql.org/docs/current/runtime-config-compatible.html#GUC-OPERATOR-PRECEDENCE-WARNING) - PostgreSQL 9.4 이후 의미가 변경된 구문에 대한 경고 표시
* [parallel_setup_cost](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-PARALLEL-SETUP-COST) - 병렬 쿼리에 대한 작업자 프로세스를 시작하기 위한 플래너의 예상 비용 설정
* [parallel_tuple_cost](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-PARALLEL-TUPLE-COST) - 작업자에서 마스터 백 엔드로 각 튜플(행)을 전달하기 위한 플래너의 예상 비용 설정
* [pg_stat_statements 저장](https://www.postgresql.org/docs/current/pgstatstatements.html#id-1.11.7.38.8) - 서버가 종료될 때의 pg_stat_statements 통계 저장
* [pg_stat_statements](https://www.postgresql.org/docs/current/pgstatstatements.html#id-1.11.7.38.8) - pg_stat_statements가 추적할 명령문 선택
* [pg_stat_statements track_utility](https://www.postgresql.org/docs/current/pgstatstatements.html#id-1.11.7.38.8) - pg_stat_statements가 추적할 유틸리티 명령 선택
* [quote_all_identifiers](https://www.postgresql.org/docs/current/runtime-config-compatible.html#GUC-QUOTE-ALL-IDENTIFIERS) - SQL 조각을 생성할 때 모든 식별자를 따옴표로 묶기
* [random_page_cost](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-RANDOM-PAGE-COST) - 디스크 페이지를 비순차적으로 가져오기 위한 플래너의 예상 비용 설정
* [row_security](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-ROW-SECURITY) - 행 보안 사용
* [search_path](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-SEARCH-PATH) - 스키마가 한정되지 않은 이름의 스키마 검색 순서 설정
* [seq_page_cost](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-SEQ-PAGE-COST) - 디스크 페이지를 순차적으로 가져오기 위한 플래너의 예상 비용 설정
* [session_replication_role](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-SESSION-REPLICATION-ROLE) - 트리거 및 다시 쓰기 규칙의 세션 동작 설정
* [standard_conforming_strings](https://www.postgresql.org/docs/current/runtime-config-compatible.html#id-1.6.6.16.2.2.7.1.3) - '...' 문자열이 백슬래시를 문자 그대로 처리
* [statement_timeout](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-STATEMENT-TIMEOUT) - 명령문에 대해 허용되는 최대 기간(밀리초) 설정. 0은 이 기능 끄기
* [synchronize_seqscans](https://www.postgresql.org/docs/current/runtime-config-compatible.html#id-1.6.6.16.2.2.8.1.3) - 동기화된 순차 스캔 사용
* [synchronous_commit](https://www.postgresql.org/docs/current/runtime-config-wal.html#GUC-SYNCHRONOUS-COMMIT) - 현재 트랜잭션의 동기화 수준 설정
* [tcp_keepalives_count](https://www.postgresql.org/docs/current/runtime-config-connection.html#GUC-TCP-KEEPALIVES-COUNT) - TCP keepalive의 최대 재전송 수
* [tcp_keepalives_idle](https://www.postgresql.org/docs/current/runtime-config-connection.html#GUC-TCP-KEEPALIVES-IDLE) - TCP keepalive를 발급하는 시간 간격
* [tcp_keepalives_interval](https://www.postgresql.org/docs/current/runtime-config-connection.html#GUC-TCP-KEEPALIVES-INTERVAL) - TCP keepalive를 다시 전송하는 시간 간격
* [temp_buffers](https://www.postgresql.org/docs/current/runtime-config-resource.html#GUC-TEMP-BUFFERS) - 각 데이터베이스 세션에서 사용하는 최대 임시 버퍼 수 설정
* [temp_tablespaces](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-TEMP-TABLESPACES) - 임시 테이블 및 정렬 파일에 사용할 테이블스페이스 설정
* [track_activities](https://www.postgresql.org/docs/current/runtime-config-statistics.html#GUC-TRACK-ACTIVITIES) - 명령 실행에 대한 정보 수집
* [track_counts](https://www.postgresql.org/docs/current/runtime-config-statistics.html#GUC-TRACK-COUNTS) - 데이터베이스 작업에 대한 통계 수집
* [track_functions](https://www.postgresql.org/docs/current/runtime-config-statistics.html#GUC-TRACK-FUNCTIONS) - 데이터베이스 작업에 대한 함수 수준 통계 수집
* [track_io_timing](https://www.postgresql.org/docs/current/runtime-config-statistics.html#GUC-TRACK-IO-TIMING) - 데이터베이스 I/O 작업에 대한 타이밍 통계 수집
* [transform_null_equals](https://www.postgresql.org/docs/current/runtime-config-compatible.html#GUC-TRANSFORM-NULL-EQUALS) - "expr=NULL"을 "expr IS NULL"로 처리
* [vacuum_cost_delay](https://www.postgresql.org/docs/current/runtime-config-resource.html#GUC-VACUUM-COST-DELAY) - 진공 비용 지연 시간(밀리초)
* [vacuum_cost_limit](https://www.postgresql.org/docs/current/runtime-config-resource.html#GUC-VACUUM-COST-LIMIT) - 내핑 전에 사용할 수 있는 진공 비용 금액
* [vacuum_cost_page_dirty](https://www.postgresql.org/docs/current/runtime-config-resource.html#GUC-VACUUM-COST-PAGE-DIRTY) - 진공에 의해 변경된 페이지의 진공 비용
* [vacuum_cost_page_hit](https://www.postgresql.org/docs/current/runtime-config-resource.html#GUC-VACUUM-COST-PAGE-HIT) - 버퍼 캐시에 있는 페이지의 진공 비용
* [vacuum_cost_page_miss](https://www.postgresql.org/docs/current/runtime-config-resource.html#GUC-VACUUM-COST-PAGE-MISS) - 버퍼 캐시에 없는 페이지의 진공 비용
* [vacuum_defer_cleanup_age](https://www.postgresql.org/docs/current/runtime-config-replication.html#GUC-VACUUM-DEFER-CLEANUP-AGE) - VACUUM 및 HOT 정리가 지연되어야 하는 트랜잭션 수(있는 경우)
* [vacuum_freeze_min_age](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-VACUUM-FREEZE-MIN-AGE) - VACUUM이 테이블 행을 동결해야 하는 최소 기간
* [vacuum_freeze_table_age](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-VACUUM-FREEZE-TABLE-AGE) - VACUUM이 전체 테이블을 스캔하여 튜플을 동결해야 하는 기간
* [vacuum_multixact_freeze_min_age](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-VACUUM-MULTIXACT-FREEZE-MIN-AGE) - VACUUM이 테이블 행의 MultiXactId를 동결해야 하는 최소 기간
* [vacuum_multixact_freeze_table_age](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-VACUUM-MULTIXACT-FREEZE-TABLE-AGE) - VACUUM이 전체 테이블을 스캔하여 튜플을 동결해야 하는 Multixact 기간
* [wal_receiver_status_interval](https://www.postgresql.org/docs/current/runtime-config-replication.html#GUC-WAL-RECEIVER-STATUS-INTERVAL) - WAL 수신기 상태 보고서 간의 최대 간격을 기본으로 설정
* [wal_writer_delay](https://www.postgresql.org/docs/current/runtime-config-wal.html#GUC-WAL-WRITER-DELAY) - WAL 기록기에서 수행되는 WAL 플러시 사이의 시간
* [wal_writer_flush_after](https://www.postgresql.org/docs/current/runtime-config-wal.html#GUC-WAL-WRITER-FLUSH-AFTER) - 플러시를 트리거하는 WAL 기록기에 의해 작성된 WAL의 양
* [work_mem](https://www.postgresql.org/docs/current/runtime-config-resource.html#GUC-WORK-MEM) - 임시 디스크 파일에 쓰기 전에 내부 정렬 작업 및 해시 테이블에서 사용할 메모리 양 설정
* [xmlbinary](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-XMLBINARY) - 이진 값을 XML로 인코딩하는 방법 설정
* [xmloption](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-XMLOPTION) - 암시적 구문 분석 및 serialization 작업에서 XML 데이터를 문서로 간주할 것인지 아니면 콘텐츠 조각으로 간주할 것인지 설정

## <a name="next-steps"></a>다음 단계

* 서버 매개 변수 외에도 또 다른 구성 형태로 하이퍼스케일(Citus) 서버 그룹의 리소스 [구성 옵션](concepts-hyperscale-configuration-options.md)이 있습니다.
* 기본 PostgreSQL 데이터베이스에도 [구성 매개 변수](http://www.postgresql.org/docs/current/static/runtime-config.html)가 있습니다.
