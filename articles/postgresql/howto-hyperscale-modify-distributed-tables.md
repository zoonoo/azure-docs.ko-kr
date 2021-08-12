---
title: 분산 테이블 수정 - 하이퍼스케일(Citus) - Azure Database for PostgreSQL
description: Azure Portal을 사용하여 분산 테이블을 만들고 수정하는 SQL 명령 - Azure Portal을 사용하는 하이퍼스케일(Citus)
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: how-to
ms.date: 8/10/2020
ms.openlocfilehash: cf9f9ca5b8690a38c6e5aa6f519378c0a2e3a4f2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "95026440"
---
# <a name="distribute-and-modify-tables"></a>테이블 분산 및 수정

## <a name="distributing-tables"></a>테이블 분산

분산 테이블을 만들려면 먼저 테이블 스키마를 정의해야 합니다. 이렇게 하려면 일반 PostgreSQL 테이블을 사용하는 것과 같은 방법으로 [CREATE TABLE](http://www.postgresql.org/docs/current/static/sql-createtable.html) 문을 사용하여 테이블을 정의할 수 있습니다.

```sql
CREATE TABLE github_events
(
    event_id bigint,
    event_type text,
    event_public boolean,
    repo_id bigint,
    payload jsonb,
    repo jsonb,
    actor jsonb,
    org jsonb,
    created_at timestamp
);
```

그런 다음 create\_distributed\_table() 함수를 사용하여 테이블 분산 열을 지정하고 작업자 분할을 만들 수 있습니다.

```sql
SELECT create_distributed_table('github_events', 'repo_id');
```

함수 호출은 github \_이벤트 테이블이 리포지토리\_ id 열(열 값을 해시하여)에 배포되어야 함을 하이퍼스케일(Citus)에 알립니다. 또한 함수는 citus.shard\_count 및 citus.shard\_replication\_factor 구성 값을 사용하는 작업자 노드에 분할을 만듭니다.

분할의 총 citus.shard\_count 수를 만들어 각 분할이 해시 공간의 일정 부분을 차지하고 기본 citus.shard\_replication\_factor 구성 값을 기본으로 복제됩니다. 작업자에 생성된 분할된 복제본의 테이블 스키마, 인덱스 및 제약 조건 정의가 코디네이터의 테이블과 동일 합니다. 복제본이 만들어지면 함수는 코디네이터에 모든 분산 메타데이터를 저장합니다.

만들어진 각각의 분할은 고유한 분할 ID를 할당받고 모든 복제본은 같은 분할 ID를 갖습니다. 분할은 작업자 노드에 \'tablename\_shardid\' 라는 이름의 일반 PostgreSQL 테이블로 표시되며 tablename은 분산 테이블의 이름, shard ID는 할당된 고유 ID입니다. 작업자 postgres 인스턴스에 연결하여 개별 분할에서 명령을 보거나 실행할 수 있습니다.

이제 분산 테이블에 데이터를 삽입하고 이에 대한 쿼리를 실행할 준비가 되었습니다. 이 섹션에서 사용되는 UDF에 대해 [테이블 및 분할 DDL](reference-hyperscale-functions.md#table-and-shard-ddl)에서 자세히 알아볼 수 있습니다.

### <a name="reference-tables"></a>참조 테이블

위의 메서드는 테이블을 여러 개의 수평 분할로 분산합니다.  다른 방법으로는 테이블을 단일 분할로 분산하고 해당 분할을 모든 작업자 노드에 복제하는 것입니다. 이러한 방식으로 분산된 테이블을 *참조 테이블이라고 합니다.* 클러스터의 여러 노드에서 자주 액세스해야 하는 데이터를 저장하는 데 사용됩니다.

참조 테이블의 일반적인 후보는 다음과 같습니다.

-   더 큰 분산 테이블과 조인해야 하는 작은 테이블.
-   테넌트 ID 열이 없거나 테넌트와 연결되지 않은\' 다중 테넌트 앱의 테이블. (또는 마이그레이션 중에 테넌트와 연결된 일부 테이블.)
-   여러 열에 고유한 제약 조건이 필요하고 충분히 작은 테이블.

예를 들어 다중 테넌트 전자상거래 사이트에서 해당 상점의 트랜잭션에 대한 판매세 계산을 해야 한다고 가정합니다. 세금 정보는 테넌트별로 다릅니다\'. 공유 테이블에 배치하는 것이 합리적입니다. 미국 중심의 참조 테이블은 다음과 같습니다.

```postgresql
-- a reference table

CREATE TABLE states (
  code char(2) PRIMARY KEY,
  full_name text NOT NULL,
  general_sales_tax numeric(4,3)
);

-- distribute it to all workers

SELECT create_reference_table('states');
```

이제 쇼핑 카트에 대한 계산세 한 개와 같은 쿼리는 네트워크 오버헤드 없이 `states` 테이블에 조인할 수 있으며 유효성 검사를 향상하기 위해 상태 코드에 외래 키를 추가할 수 있습니다.

테이블을 단일 복제된 분할로 분산시키는 것 외에, `create_reference_table` UDF는 이를 하이퍼스케일(Citus) 메타데이터 테이블에 참조 테이블로 표시합니다. 하이퍼스케일(Citus)은 이러한 방식으로 표시된 테이블을 수정하기 위해 2단계 커밋([2PC](https://en.wikipedia.org/wiki/Two-phase_commit_protocol))을 자동으로 수행하여 강력한 일관성을 보장합니다.

분할된 데이터베이스 수가 1인 분산 테이블이 있는 경우 다음과 같이 인식된 참조 테이블로 업그레이드할 수 있습니다.

```postgresql
SELECT upgrade_to_reference_table('table_name');
```

참조 테이블을 사용하는 또 다른 예제는 [다중 테넌트 데이터베이스 자습서](tutorial-design-database-hyperscale-multi-tenant.md)를 참조하세요.

### <a name="distributing-coordinator-data"></a>코디네이터 데이터 분산

기존 PostgreSQL 데이터베이스가 하이퍼스케일(Citus) 클러스터의 코디네이터 노드로 변환되는 경우 해당 테이블의 데이터는 응용 프로그램에 대한 중단을 최소화하면서 효율적으로 분산될 수 있습니다.

앞에서 설명한 `create_distributed_table` 함수는 비어 있거나 비어 있지 않은 테이블 모두에서 작동하며 후자의 경우 클러스터 전체에서 테이블 행을 자동으로 분산합니다. 다음 메시지의 유무에 따라 데이터를 복사하는지 알 수 있습니다. \"NOTICE: Copying data from local table\...\" 예시는 다음과 같습니다.

```postgresql
CREATE TABLE series AS SELECT i FROM generate_series(1,1000000) i;
SELECT create_distributed_table('series', 'i');
NOTICE:  Copying data from local table...
 create_distributed_table
 --------------------------

 (1 row)
```

데이터를 마이그레이션하는 동안 테이블에 대한 쓰기를 차단하고, 함수가 커밋되면 보류 중인 쓰기가 분산 쿼리로 처리됩니다. 함수가 실패하면 쿼리는 다시 로컬 상태가 됩니다. 읽기는 정상적으로 계속되고 함수가 커밋되면 배포 쿼리가 됩니다.

테이블 A와 B를 분산할 A에 B에 대한 외래 키가 있다면, 키 대상 테이블 B를 먼저 분산합니다. 잘못된 순서로 수행하면 오류가 발생합니다.

```
ERROR:  cannot create foreign key constraint
DETAIL:  Referenced table must be a distributed table or a reference table.
```

올바른 순서로 배포할 수 없는 경우에는 외래 키를 삭제하고, 테이블을 분산한 뒤, 외래 키를 다시 만듭니다.

Amazon RDS에서 하이퍼스케일(Citus) 클라우드와 같은 외부 데이터베이스에서 데이터를 마이그레이션하는 경우 먼저 `create_distributed_table`를 통해 하이퍼스케일(Citus) 분산 테이블을 만든 다음 데이터를 테이블에 복사합니다.
분산 테이블에 복사하면 코디네이터 노드의 공간이 부족하지 않습니다.

## <a name="colocating-tables"></a>공동 배치 테이블

공동 배치는 동일한 컴퓨터에 관련 정보를 보관하는 것을 의미합니다. 이를 통해 전체 데이터 집합에 대한 가로 확장성을 활용하면서 효율적인 쿼리를 사용할 수 있습니다. 자세한 내용은 [공동 배치](concepts-hyperscale-colocation.md)를 참조하세요.

테이블은 그룹으로 공동 배치됩니다. 테이블의 공동 배치 그룹 할당을 수동으로 제어하려면 선택적 매개 변수 `colocate_with`을 `create_distributed_table`에서 찾아 사용합니다. 테이블의 공동 배치를 고려하지 않는\' 경우 이 매개 변수를\' 생략합니다. 이 값은 `'default'`값으로 설정되며, 이 값은 동일한 분산 열 유형, 분할 개수, 복제 요인이 있는 다른 기본 공동 배치 테이블을 사용하여 테이블을 그룹화합니다. 이 암시적 공동 배치를 중단하거나 업데이트하려는 경우에는 `update_distributed_table_colocation()`를 사용할 수 있습니다.

```postgresql
-- these tables are implicitly co-located by using the same
-- distribution column type and shard count with the default
-- co-location group

SELECT create_distributed_table('A', 'some_int_col');
SELECT create_distributed_table('B', 'other_int_col');
```

새 테이블이 해당하는 암시적 공동 배치 그룹의 다른 테이블과 관련되지 않은 경우에는 `colocated_with => 'none'`을 지정합니다.

```postgresql
-- not co-located with other tables

SELECT create_distributed_table('A', 'foo', colocate_with => 'none');
```

관련 없는 테이블을 자체의 공동 배치 그룹으로 분할하면 동일한 그룹의 분할이 함께 이동해야 하므로 [분할 리밸런싱](howto-hyperscale-scale-rebalance.md) 성능이 향상됩니다.

테이블이 실제로 관련된 경우(예: 조인되는 경우) 명시적으로 함께 지정하는 편이 적합합니다. 적절한 공동 배치의 이점은 다른 리밸런싱 오버헤드보다 더 중요합니다.

여러 테이블을 명시적으로 공동 배치하려면 하나를 분산한 후 나머지를 공동 배치 그룹에 배치합니다. 예를 들면 다음과 같습니다.

```postgresql
-- distribute stores
SELECT create_distributed_table('stores', 'store_id');

-- add to the same group as stores
SELECT create_distributed_table('orders', 'store_id', colocate_with => 'stores');
SELECT create_distributed_table('products', 'store_id', colocate_with => 'stores');
```

배치 그룹에 대한 정보는 [pg_dist_colocation](reference-hyperscale-metadata.md#colocation-group-table) 테이블에 저장되며, [pg_dist_partition](reference-hyperscale-metadata.md#partition-table)은 그룹에 할당된 테이블을 표시합니다.

## <a name="dropping-tables"></a>테이블 삭제

표준 PostgreSQL DROP TABLE 명령을 사용하여 분산 테이블을 제거할 수 있습니다. 일반 테이블과 마찬가지로 DROP TABLE은 대상 테이블에 대해 존재하는 인덱스, 규칙, 트리거 및 제약 조건을 제거합니다. 또한 작업자 노드에서 분할된 데이터베이스를 삭제하고 해당 메타데이터를 정리합니다.

```sql
DROP TABLE github_events;
```

## <a name="modifying-tables"></a>테이블 수정

하이퍼스케일(Citus)은 다양한 종류의 DDL 문을 자동으로 전파합니다.
코디네이터 노드에서 분산 테이블을 수정하면 작업자의 분할된 데이터베이스도 업데이트됩니다. 다른 DDL 문에는 수동 전파가 필요하며 분산 열을 수정하는 문과 같은 다른 문은 금지됩니다.
자동 전파에 부적절한 DDL을 실행하려고 하면 오류가 발생하며 코디네이터 노드의 테이블이 변경되지 않은 상태로 유지됩니다.

다음은 전파가 가능한 DDL 문의 범주에 대한 참조 내용입니다.
자동 전파는 [구성 매개 변수](reference-hyperscale-parameters.md#citusenable_ddl_propagation-boolean) 로 사용하거나 사용하지 않도록 설정할 수 있습니다

### <a name="addingmodifying-columns"></a>열 추가/수정

하이퍼스케일(Citus)은 대부분의 [ALTER TABLE](https://www.postgresql.org/docs/current/static/ddl-alter.html) 명령을 자동으로 전파합니다. 단일 컴퓨터 PostgreSQL 데이터베이스와 마찬가지로 열을 추가하거나 기본값을 변경할 수 있습니다.

```postgresql
-- Adding a column

ALTER TABLE products ADD COLUMN description text;

-- Changing default value

ALTER TABLE products ALTER COLUMN price SET DEFAULT 7.77;
```

기존 열의 이름을 바꾸거나 해당 데이터 형식을 변경하는 것과 같은 중요한 변경 내용도 괜찮습니다. 그러나 [배포 열의](concepts-hyperscale-nodes.md#distribution-column) 데이터 형식은 변경할 수 없습니다.
이 열은 테이블 데이터가 하이퍼스케일(Citus) 클러스터를 통해 배포되는 방식을 결정하고 데이터 형식을 수정하려면 데이터를 이동해야 합니다.

이를 시도하면 오류가 발생합니다.

```postgres
-- assumining store_id is the distribution column
-- for products, and that it has type integer

ALTER TABLE products
ALTER COLUMN store_id TYPE text;

/*
ERROR:  XX000: cannot execute ALTER TABLE command involving partition column
LOCATION:  ErrorIfUnsupportedAlterTableStmt, multi_utility.c:2150
*/
```

### <a name="addingremoving-constraints"></a>제약 조건 추가/제거

하이퍼스케일(Citus)을 사용하면 데이터베이스 제약 조건(PostgreSQL [docs](https://www.postgresql.org/docs/current/static/ddl-constraints.html)을 참조)을 포함한 관계형 데이터베이스의 안전을 계속 유지할 수 있습니다.
분산 시스템의 특성으로 인해 하이퍼스케일(Citus)은 작업자 노드 간의 고유성 제약 조건 또는 참조 무결성을 상호 참조하지 않습니다.

공동 배치 분산 테이블 간에 외래 키를 설정하려면 항상 키에 분산 열을 포함해야 합니다. 분산 열을 포함하면 키를 복합 상태로 만들 수 있습니다.

다음과 같은 상황에서 외래 키를 만들 수 있습니다.

-   분산되지 않은 두 로컬 테이블 사이,
-   두 참조 테이블 사이,
-   키가 분산 열을 포함하고 있는 상태에서 두 개의 [공동 배치](concepts-hyperscale-colocation.md) 분산 테이블 사이, 또는
-   [참조 테이블](concepts-hyperscale-nodes.md#type-2-reference-tables)을 참조하는 분산 테이블

참조 테이블에서 분산 테이블로의 외래 키는 지원되지 않습니다.

> [!NOTE]
>
> 기본 키와 고유성 제약 조건에는 배포 열이 포함되어야 합니다. 비 분산 열에 추가하면 오류가 생성됩니다

이 예에서는 분산 테이블에 기본 키 및 외래 키를 만드는 방법을 보여줍니다.

```postgresql
--
-- Adding a primary key
-- --------------------

-- We'll distribute these tables on the account_id. The ads and clicks
-- tables must use compound keys that include account_id.

ALTER TABLE accounts ADD PRIMARY KEY (id);
ALTER TABLE ads ADD PRIMARY KEY (account_id, id);
ALTER TABLE clicks ADD PRIMARY KEY (account_id, id);

-- Next distribute the tables

SELECT create_distributed_table('accounts', 'id');
SELECT create_distributed_table('ads',      'account_id');
SELECT create_distributed_table('clicks',   'account_id');

--
-- Adding foreign keys
-- -------------------

-- Note that this can happen before or after distribution, as long as
-- there exists a uniqueness constraint on the target column(s) which
-- can only be enforced before distribution.

ALTER TABLE ads ADD CONSTRAINT ads_account_fk
  FOREIGN KEY (account_id) REFERENCES accounts (id);
ALTER TABLE clicks ADD CONSTRAINT clicks_ad_fk
  FOREIGN KEY (account_id, ad_id) REFERENCES ads (account_id, id);
```

마찬가지로 고유성 제약 조건에 분산 열을 포함합니다.

```postgresql
-- Suppose we want every ad to use a unique image. Notice we can
-- enforce it only per account when we distribute by account id.

ALTER TABLE ads ADD CONSTRAINT ads_unique_image
  UNIQUE (account_id, image_url);
```

Not-null 제약 조건은 작업자 간에 조회가 필요 하지 않으므로 모든 열에(분산 여부 무관)적용할 수 있습니다.

```postgresql
ALTER TABLE ads ALTER COLUMN image_url SET NOT NULL;
```

### <a name="using-not-valid-constraints"></a>NOT VALID 제약 조건 사용

경우에 따라 새 행에 제약 조건을 적용하는 것이 유용할 수 있습니다. 이때 기존 비준수 행은 변경되지 않은 상태로 유지할 수 있습니다. 하이퍼스케일(Citus)은 CHECK 제약 조건과 외래 키에 대해 이 기능을 지원하며, PostgreSQL\'의 \"NOT VALID\" 제약 조건 지정을 사용합니다.

예를 들어 사용자 프로필을 [참조 테이블](concepts-hyperscale-nodes.md#type-2-reference-tables)에 저장하는 응용 프로그램이 있다고 가정합니다.

```postgres
-- we're using the "text" column type here, but a real application
-- might use "citext" which is available in a postgres contrib module

CREATE TABLE users ( email text PRIMARY KEY );
SELECT create_reference_table('users');
```

이 과정에서 몇 가지 비 주소를 테이블에 가져올 것으로 가정합니다.

```postgres
INSERT INTO users VALUES
   ('foo@example.com'), ('hacker12@aol.com'), ('lol');
```

주소의 유효성을 검사하려고 하지만 PostgreSQL에서는 일반적으로 기존 행에 대해 실패하는 CHECK 제약 조건을 추가할 수 없습니다. 그러나 유효하지 않은 것으로 표시된 제약 조건은 *허용* 합니다.

```postgres
ALTER TABLE users
ADD CONSTRAINT syntactic_email
CHECK (email ~
   '^[a-zA-Z0-9.!#$%&''*+/=?^_`{|}~-]+@[a-zA-Z0-9](?:[a-zA-Z0-9-]{0,61}[a-zA-Z0-9])?(?:\.[a-zA-Z0-9](?:[a-zA-Z0-9-]{0,61}[a-zA-Z0-9])?)*$'
) NOT VALID;
```

이제 새 행이 보호됩니다.

```postgres
INSERT INTO users VALUES ('fake');

/*
ERROR:  new row for relation "users_102010" violates
        check constraint "syntactic_email_102010"
DETAIL:  Failing row contains (fake).
*/
```

나중에 사용량이 많지 않은 시간에 데이터베이스 관리자는 잘못된 행을 수정하고 제약 조건의 유효성을 다시 검증하려고 시도할 수 있습니다.

```postgres
-- later, attempt to validate all rows
ALTER TABLE users
VALIDATE CONSTRAINT syntactic_email;
```

PostgreSQL 설명서에는 [ALTER TABLE](https://www.postgresql.org/docs/current/sql-altertable.html) 섹션의 NOT VALID 및 VALIDATE CONSTRAINT에 대한 자세한 내용이 포함되어 있습니다.

### <a name="addingremoving-indices"></a>인덱스 추가/제거

하이퍼스케일(Citus)은 [인덱스](https://www.postgresql.org/docs/current/static/sql-createindex.html)의 추가 및 제거를 지원합니다.

```postgresql
-- Adding an index

CREATE INDEX clicked_at_idx ON clicks USING BRIN (clicked_at);

-- Removing an index

DROP INDEX clicked_at_idx;
```

인덱스를 추가하면 쓰기 잠금이 적용되며, 다중 테넌트 \"레코드 시스템에서는 바람직하지 않을 수 있습니다.\" 응용 프로그램 가동 중지 시간을 최소화하려면 인덱스를 [동시에](https://www.postgresql.org/docs/current/static/sql-createindex.html#SQL-CREATEINDEX-CONCURRENTLY) 만듭니다. 이 메서드는 표준 인덱스 작성보다 총 작업이 더 많이 필요하며 완료하는 데 시간이 오래 걸립니다. 그러나 인덱스가 작성되는 동안 정상적인 작업을 계속할 수 있기 때문에 프로덕션 환경에서 새 인덱스를 추가하는 데 유용합니다.

```postgresql
-- Adding an index without locking table writes

CREATE INDEX CONCURRENTLY clicked_at_idx ON clicks USING BRIN (clicked_at);
```
