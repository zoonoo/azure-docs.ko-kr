---
title: '자습서: 작업자 노드에서 데이터 분할 - 하이퍼스케일(Citus) - Azure Database for PostgreSQL'
description: 이 자습서에서는 Azure Database for PostgreSQL 하이퍼스케일(Citus)을 사용하여 분산 테이블을 만들고 해당 데이터 분산을 시각화하는 방법을 보여 줍니다.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.custom: mvc
ms.devlang: azurecli
ms.topic: tutorial
ms.date: 12/16/2020
ms.openlocfilehash: b0b076809801a4bb2f055d94ba481736b008b52c
ms.sourcegitcommit: 67cdbe905eb67e969d7d0e211d87bc174b9b8dc0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/09/2021
ms.locfileid: "111854885"
---
# <a name="tutorial-shard-data-on-worker-nodes-in-azure-database-for-postgresql--hyperscale-citus"></a>자습서: Azure Database for PostgreSQL – 하이퍼스케일(Citus)의 작업자 노드에서 데이터 분할

이 자습서에서는 Azure Database for PostgreSQL – 하이퍼스케일(Citus)을 사용하여 다음 작업을 수행하는 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * 해시 분산 분할 만들기
> * 테이블 분할이 배치되는 위치 확인
> * 기울어진 분산 식별
> * 분산 테이블에 대한 제약 조건 만들기
> * 분산 데이터에 대한 쿼리 실행

## <a name="prerequisites"></a>필수 구성 요소

이 자습서에는 두 개의 작업자 노드를 사용하여 실행되는 하이퍼스케일(Citus) 서버 그룹이 필요합니다. 실행되는 서버 그룹이 없는 경우 [서버 그룹 만들기](tutorial-hyperscale-server-group.md) 자습서를 수행한 다음, 해당 그룹으로 돌아갑니다.

## <a name="hash-distributed-data"></a>해시 분산 데이터

테이블 행을 여러 PostgreSQL 서버에 분산하는 것은 하이퍼스케일(Citus)에서 확장 가능한 쿼리를 수행하기 위한 핵심 기술입니다. 여러 노드에서 동시에 기존 데이터베이스보다 더 많은 데이터를 보유할 수 있으며, 대부분의 경우 작업자 CPU를 병렬로 사용하여 쿼리를 실행할 수 있습니다.

필수 구성 요소 섹션에서 두 개의 작업자 노드가 있는 하이퍼스케일(Citus) 서버 그룹을 만들었습니다.

![코디네이터 및 2개 작업자](tutorial-hyperscale-shard/nodes.png)

코디네이터 노드의 메타데이터 테이블은 작업자와 분산 데이터를 추적합니다. 활성 작업자는 [pg_dist_node](reference-hyperscale-metadata.md#worker-node-table) 테이블에서 확인할 수 있습니다.

```sql
select nodeid, nodename from pg_dist_node where isactive;
```
```
 nodeid | nodename
--------+-----------
      1 | 10.0.0.21
      2 | 10.0.0.23
```

> [!NOTE]
> 하이퍼스케일(Citus)의 노드 이름은 가상 네트워크의 내부 IP 주소이며, 표시되는 실제 주소가 다를 수 있습니다.

### <a name="rows-shards-and-placements"></a>행, 분할 및 배치

작업자 노드의 CPU 및 스토리지 리소스를 사용하려면 테이블 데이터를 서버 그룹 전체에 분산해야 합니다.  테이블을 분산하면 각 행이 *분할* 이라는 논리 그룹에 할당됩니다. 테이블을 만들어 분산해 보겠습니다.

```sql
-- create a table on the coordinator
create table users ( email text primary key, bday date not null );

-- distribute it into shards on workers
select create_distributed_table('users', 'email');
```

하이퍼스케일(Citus)은 *분산 열* 의 값에 따라 각 행을 분할에 할당합니다. 여기서 이 열은 `email`로 지정되었습니다. 모든 행은 정확히 하나의 분할에 있고, 모든 분할은 여러 행을 포함할 수 있습니다.

![분할을 가리키는 행이 있는 사용자 테이블](tutorial-hyperscale-shard/table.png)

[pg_dist_shard](reference-hyperscale-metadata.md#shard-table) 메타데이터 테이블을 계산하여 알 수 있듯이 `create_distributed_table()`은 기본적으로 32개의 분할을 만듭니다.

```sql
select logicalrelid, count(shardid)
  from pg_dist_shard
 group by logicalrelid;
```
```
 logicalrelid | count
--------------+-------
 users        |    32
```

하이퍼스케일(Citus)은 `pg_dist_shard` 테이블을 사용하여 분산 열에 있는 값의 해시에 따라 행을 분할에 할당합니다. 이 자습서에서 해싱 세부 정보는 중요하지 않습니다. 중요한 것은 어떤 값이 어떤 분할 ID에 매핑되는지 확인하기 위해 쿼리할 수 있다는 것입니다.

```sql
-- Where would a row containing hi@test.com be stored?
-- (The value doesn't have to actually be present in users, the mapping
-- is a mathematical operation consulting pg_dist_shard.)
select get_shard_id_for_distribution_column('users', 'hi@test.com');
```
```
 get_shard_id_for_distribution_column
--------------------------------------
                               102008
```

행을 분할에 매핑하는 것은 전적으로 논리적입니다. 분할은 하이퍼스케일(Citus)에서 *분할 배치* 라고 하는 스토리지에 대한 특정 작업자 노드에 할당해야 합니다.

![작업자에 할당된 분할](tutorial-hyperscale-shard/shard-placement.png)

[pg_dist_placement](reference-hyperscale-metadata.md#shard-placement-table)에서 분할 배치를 살펴볼 수 있습니다.
여기에 표시된 다른 메타데이터 테이블과 조인하면 각 분할이 있는 위치가 표시됩니다.

```sql
-- limit the output to the first five placements

select
    shard.logicalrelid as table,
    placement.shardid as shard,
    node.nodename as host
from
    pg_dist_placement placement,
    pg_dist_node node,
    pg_dist_shard shard
where placement.groupid = node.groupid
  and shard.shardid = placement.shardid
order by shard
limit 5;
```
```
 table | shard  |    host
-------+--------+------------
 users | 102008 | 10.0.0.21
 users | 102009 | 10.0.0.23
 users | 102010 | 10.0.0.21
 users | 102011 | 10.0.0.23
 users | 102012 | 10.0.0.21
```

### <a name="data-skew"></a>데이터 기울이기

서버 그룹은 데이터를 작업자 노드에 균등하게 배치하는 경우와 관련 데이터를 모두 동일한 작업자에 배치하는 경우에 가장 효율적으로 실행됩니다. 이 섹션에서는 첫 번째 경우, 즉 배치의 균일성에 초점을 둡니다.

`users` 테이블에 대한 샘플 데이터를 만들어 설명해 보겠습니다.

```sql
-- load sample data
insert into users
select
    md5(random()::text) || '@test.com',
    date_trunc('day', now() - random()*'100 years'::interval)
from generate_series(1, 1000);
```

분할 크기를 확인하려면 분할에서 [테이블 크기 함수](https://www.postgresql.org/docs/current/functions-admin.html#FUNCTIONS-ADMIN-DBSIZE)를 실행할 수 있습니다.

```sql
-- sizes of the first five shards
select *
from
    run_command_on_shards('users', $cmd$
      select pg_size_pretty(pg_table_size('%1$s'));
    $cmd$)
order by shardid
limit 5;
```
```
 shardid | success | result
---------+---------+--------
  102008 | t       | 16 kB
  102009 | t       | 16 kB
  102010 | t       | 16 kB
  102011 | t       | 16 kB
  102012 | t       | 16 kB
```

분할 크기가 동일하다는 것을 알 수 있습니다. 배치가 이미 작업자 간에 균등하게 분산되어 있으므로 작업자 노드가 거의 동일한 수의 행을 보유하고 있다고 유추할 수 있습니다.

분산 열(`email`)의 속성으로 인해 `users` 예제의 행이 균등하게 분산되었습니다.

1. 이메일 주소 수가 분할 수보다 크거나 같습니다.
2. 이메일 주소당 행 수가 비슷했습니다(이 경우 이메일을 키로 선언했으므로 정확히 주소당 하나의 행이 있음).

두 속성 중 하나가 실패하는 테이블 및 분산 열을 선택하면 작업자의 데이터 크기가 균일하지 않게 됩니다. 즉, *데이터 기울이기* 가 발생합니다.

### <a name="add-constraints-to-distributed-data"></a>분산 데이터에 제약 조건 추가

하이퍼스케일(Citus)을 사용하면 [데이터베이스 제약 조건](https://www.postgresql.org/docs/current/ddl-constraints.html)을 포함하여 관계형 데이터베이스의 안전을 계속 활용할 수 있습니다.
그러나 제한 사항이 있습니다. 분산 시스템의 특성으로 인해 하이퍼스케일(Citus)은 작업자 노드 간의 고유성 제약 조건 또는 참조 무결성을 상호 참조하지 않습니다.

관련 테이블이 있는 `users` 테이블 예제를 살펴보겠습니다.

```sql
-- books that users own
create table books (
    owner_email text references users (email),
    isbn text not null,
    title text not null
);

-- distribute it
select create_distributed_table('books', 'owner_email');
```

효율성을 위해 `users`와 동일한 방식으로 소유자의 이메일 주소에 따라 `books`를 분산합니다. 비슷한 열 값에 따라 분산하는 것을 [공동 배치](concepts-hyperscale-colocation.md)라고 합니다.

키가 분산 열에 있으므로 사용자에게 외래 키가 있는 책을 분산하는 데 문제가 없었습니다. 그러나 `isbn`을 키로 만드는 데 문제가 있습니다.

```sql
-- will not work
alter table books add constraint books_isbn unique (isbn);
```
```
ERROR:  cannot create constraint on "books"
DETAIL: Distributed relations cannot have UNIQUE, EXCLUDE, or
        PRIMARY KEY constraints that do not include the partition column
        (with an equality operator if EXCLUDE).
```

분산 테이블에서 열을 고유한 모듈로인 분산 열로 만드는 것이 가장 좋습니다.

```sql
-- a weaker constraint is allowed
alter table books add constraint books_isbn unique (owner_email, isbn);
```

위의 제약 조건은 isbn을 사용자별로 고유하게 만들 뿐입니다. 또 다른 옵션은 책을 분산 테이블이 아닌 [참조 테이블](howto-hyperscale-modify-distributed-tables.md#reference-tables)로 만들고 책을 사용자와 연결하는 별도의 분산 테이블을 만드는 것입니다.

## <a name="query-distributed-tables"></a>분산 테이블 쿼리

이전 섹션에서는 분산 테이블 행이 작업자 노드의 분할에 배치되는 방식을 살펴보았습니다. 대부분의 경우 데이터가 서버 그룹에 저장되는 방법 또는 위치를 알 필요가 없습니다. 하이퍼스케일(Citus)에는 일반 SQL 쿼리를 자동으로 분할하는 분산 쿼리 실행기가 있습니다. 이는 데이터에 가까운 작업자 노드에서 병렬로 실행됩니다.

예를 들어 사용자의 평균 연령을 찾는 쿼리를 실행하여 분산된 `users` 테이블을 코디네이터의 일반 테이블처럼 처리할 수 있습니다.

```sql
select avg(current_date - bday) as avg_days_old from users;
```
```
    avg_days_old
--------------------
 17926.348000000000
```

![코디네이터를 통해 분할로 이동하는 쿼리](tutorial-hyperscale-shard/query-fragments.png)

하이퍼스케일(Citus) 실행기는 내부적으로 각 분할에 대해 별도의 쿼리를 만들고, 이를 작업자에서 실행하고, 결과를 결합합니다. PostgreSQL EXPLAIN 명령을 사용하면 이를 확인할 수 있습니다.

```sql
explain select avg(current_date - bday) from users;
```
```
                                  QUERY PLAN
----------------------------------------------------------------------------------
 Aggregate  (cost=500.00..500.02 rows=1 width=32)
   ->  Custom Scan (Citus Adaptive)  (cost=0.00..0.00 rows=100000 width=16)
     Task Count: 32
     Tasks Shown: One of 32
     ->  Task
       Node: host=10.0.0.21 port=5432 dbname=citus
       ->  Aggregate  (cost=41.75..41.76 rows=1 width=16)
         ->  Seq Scan on users_102040 users  (cost=0.00..22.70 rows=1270 width=4)
```

출력에는 102040 분할(10.0.0.21 작업자의 `users_102040` 테이블)에서 실행되는 *쿼리 조각* 에 대한 실행 계획의 예가 표시됩니다. 다른 조각은 이와 비슷하므로 표시되지 않습니다. 작업자 노드에서 분할 테이블을 검사하여 집계를 적용하는 것을 확인할 수 있습니다. 코디네이터 노드는 집계를 결합하여 최종 결과를 생성합니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 분산 테이블을 만들고, 해당 분할 및 배치에 대해 알아보았습니다. 고유성 및 외래 키 제약 조건을 사용하는 것과 관련된 문제를 확인했고, 마지막으로 분산 쿼리가 높은 수준에서 작동하는 방법을 살펴보았습니다.

* 하이퍼스케일(Citus) [테이블 형식](concepts-hyperscale-nodes.md)에 대해 자세히 알아보기
* [분산 열 선택](concepts-hyperscale-choose-distribution-column.md)에 대한 추가 팁 얻기
* [테이블 공동 배치](concepts-hyperscale-colocation.md)의 이점 알아보기
