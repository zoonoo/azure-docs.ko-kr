---
title: 테이블 코로케이션 - 하이퍼스케일(Citus) - PostgreSQL용 Azure 데이터베이스
description: 더 빠른 쿼리를 위해 관련 정보를 함께 저장하는 방법
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 05/06/2019
ms.openlocfilehash: 7e4073ec45f4c21f33d20924a9948e72f961c7f8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74967340"
---
# <a name="table-colocation-in-azure-database-for-postgresql--hyperscale-citus"></a>PostgreSQL에 대 한 Azure 데이터베이스에서 테이블 colocation – 하이퍼 스케일 (시터스)

코로케이션은 동일한 노드에 관련 정보를 함께 저장하는 것을 의미합니다. 네트워크 트래픽 없이 필요한 모든 데이터를 사용할 수 있는 경우 쿼리가 빠르게 진행될 수 있습니다. 서로 다른 노드에서 관련 데이터를 공동 배치하면 쿼리가 각 노드에서 병렬로 효율적으로 실행될 수 있습니다.

## <a name="data-colocation-for-hash-distributed-tables"></a>해시 분산 테이블의 데이터 코로케이션

PostgreSQL - 하이퍼스케일(Citus)에 대한 Azure 데이터베이스에서 분포 열에 있는 값의 해시가 샤드의 해시 범위 내에 있는 경우 행이 샤드에 저장됩니다. 해시 범위가 같은 샤드는 항상 동일한 노드에 배치됩니다. 분포 열 값이 동일한 행은 항상 테이블 전체에서 동일한 노드에 있습니다.

![파편](media/concepts-hyperscale-colocation/colocation-shards.png)

## <a name="a-practical-example-of-colocation"></a>코로케이션의 실용적인 예

다중 테넌트 웹 분석 SaaS의 일부일 수 있는 다음 표를 고려하십시오.

```sql
CREATE TABLE event (
  tenant_id int,
  event_id bigint,
  page_id int,
  payload jsonb,
  primary key (tenant_id, event_id)
);

CREATE TABLE page (
  tenant_id int,
  page_id int,
  path text,
  primary key (tenant_id, page_id)
);
```

이제 고객 대면 대시보드에서 발급할 수 있는 쿼리에 응답하려고 합니다. 예제 쿼리는 '테넌트 6의 '/blog'로 시작하는 모든 페이지의 지난 주 방문 횟수 반환'입니다.

데이터가 단일 서버 배포 옵션에 있는 경우 SQL에서 제공하는 다양한 관계형 작업 집합을 사용하여 쿼리를 쉽게 표현할 수 있습니다.

```sql
SELECT page_id, count(event_id)
FROM
  page
LEFT JOIN  (
  SELECT * FROM event
  WHERE (payload->>'time')::timestamptz >= now() - interval '1 week'
) recent
USING (tenant_id, page_id)
WHERE tenant_id = 6 AND path LIKE '/blog%'
GROUP BY page_id;
```

이 쿼리에 대한 [작업 집합이](https://en.wikipedia.org/wiki/Working_set) 메모리에 맞는 한 단일 서버 테이블이 적절한 솔루션입니다. 하이퍼스케일(Citus) 배포 옵션을 사용하여 데이터 모델을 확장할 수 있는 기회를 살펴보겠습니다.

### <a name="distribute-tables-by-id"></a>ID별로 테이블 배포

테넌트 수와 각 테넌트에 대해 저장된 데이터가 증가함에 따라 단일 서버 쿼리가 느려지기 시작합니다. 작업 세트는 메모리에 맞지 않게 되고 CPU는 병목 현상이 됩니다.

이 경우 하이퍼스케일(Citus)을 사용하여 여러 노드에서 데이터를 조각할 수 있습니다. 샤드를 결정할 때 가장 먼저 해야 할 가장 중요한 선택은 분포 열입니다. 먼저 이벤트 테이블과 `event_id` `page_id` 테이블에 사용할 수 있습니다. `page`

```sql
-- naively use event_id and page_id as distribution columns

SELECT create_distributed_table('event', 'event_id');
SELECT create_distributed_table('page', 'page_id');
```

데이터가 여러 작업자에 분산되면 PostgreSQL 노드 하나에서와 마찬가지로 조인을 수행할 수 없습니다. 대신 다음 두 가지 쿼리를 발행해야 합니다.

```sql
-- (Q1) get the relevant page_ids
SELECT page_id FROM page WHERE path LIKE '/blog%' AND tenant_id = 6;

-- (Q2) get the counts
SELECT page_id, count(*) AS count
FROM event
WHERE page_id IN (/*…page IDs from first query…*/)
  AND tenant_id = 6
  AND (payload->>'time')::date >= now() - interval '1 week'
GROUP BY page_id ORDER BY count DESC LIMIT 10;
```

그런 다음 두 단계의 결과를 응용 프로그램에서 결합해야 합니다.

쿼리를 실행하려면 노드에 분산된 샤드의 데이터를 참조해야 합니다.

![비효율적인 쿼리](media/concepts-hyperscale-colocation/colocation-inefficient-queries.png)

이 경우 데이터 분포는 다음과 같은 상당한 단점을 만듭니다.

-   각 샤드를 쿼리하고 여러 쿼리를 실행하는 오버헤드입니다.
-   많은 행을 클라이언트에 반환하는 Q1의 오버헤드입니다.
-   Q2가 커집니다.
-   여러 단계로 쿼리를 작성해야 하므로 응용 프로그램을 변경해야 합니다.

데이터가 분산되므로 쿼리를 병렬화할 수 있습니다. 쿼리가 수행하는 작업량이 많은 샤드를 쿼리하는 오버헤드보다 훨씬 큰 경우에만 유용합니다.

### <a name="distribute-tables-by-tenant"></a>테넌트별로 테이블 배포

하이퍼스케일(Citus)에서는 분포 열 값이 같은 행이 동일한 노드에 있음이 보장됩니다. 다시 시작하여 배포 열로 `tenant_id` 테이블을 만들 수 있습니다.

```sql
-- co-locate tables by using a common distribution column
SELECT create_distributed_table('event', 'tenant_id');
SELECT create_distributed_table('page', 'tenant_id', colocate_with => 'event');
```

이제 하이퍼스케일(Citus)은 수정없이 원래의 단일 서버 쿼리에 응답할 수 있습니다(Q1):

```sql
SELECT page_id, count(event_id)
FROM
  page
LEFT JOIN  (
  SELECT * FROM event
  WHERE (payload->>'time')::timestamptz >= now() - interval '1 week'
) recent
USING (tenant_id, page_id)
WHERE tenant_id = 6 AND path LIKE '/blog%'
GROUP BY page_id;
```

하이퍼스케일(Citus)은 필터 및 tenant_id 조인하기 때문에 특정 테넌트에 대한 데이터를 포함하는 공동 배치된 샤드 집합을 사용하여 전체 쿼리에 응답할 수 있음을 알고 있습니다. 단일 PostgreSQL 노드는 단일 단계로 쿼리에 응답할 수 있습니다.

![더 나은 쿼리](media/concepts-hyperscale-colocation/colocation-better-query.png)

경우에 따라 고유한 제약 조건 및 조인 조건에 테넌트 ID를 포함하도록 쿼리 및 테이블 스키마를 변경해야 합니다. 이 변경 사항은 일반적으로 간단합니다.

## <a name="next-steps"></a>다음 단계

- [다중 테넌트 자습서에서](tutorial-design-database-hyperscale-multi-tenant.md)테넌트 데이터가 공존하는 방법을 확인합니다.
