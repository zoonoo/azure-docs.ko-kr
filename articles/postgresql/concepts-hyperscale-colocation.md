---
title: 테이블 공동 배치 - 하이퍼스케일(Citus) - Azure Database for PostgreSQL
description: 더 빠른 쿼리를 위해 관련 정보를 함께 저장하는 방법
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 05/06/2019
ms.openlocfilehash: 842563319e09a001fd6e85403d8aee6fb14690ee
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "90884428"
---
# <a name="table-colocation-in-azure-database-for-postgresql--hyperscale-citus"></a>Azure Database for PostgreSQL – 하이퍼스케일(Citus)의 테이블 공동 배치

공동 배치는 동일한 노드에 관련 정보를 함께 저장하는 것을 의미합니다. 필요한 모든 데이터를 네트워크 트래픽 없이 사용할 수 있으면 쿼리가 신속하게 진행될 수 있습니다. 서로 다른 노드에 있는 관련 데이터를 공동 배치하면 각 노드에서 쿼리를 효율적으로 병렬 실행할 수 있습니다.

## <a name="data-colocation-for-hash-distributed-tables"></a>해시 분산 테이블의 데이터 공동 배치

Azure Database for PostgreSQL – 하이퍼스케일(Citus)에서 배포 열의 값 해시가 분할된 데이터베이스의 해시 범위 내에 있으면 행이 분할된 데이터베이스에 저장됩니다. 해시 범위가 같은 분할된 데이터베이스는 항상 동일한 노드에 배치됩니다. 배포 열 값이 같은 행은 항상 테이블의 동일한 노드에 있습니다.

:::image type="content" source="media/concepts-hyperscale-colocation/colocation-shards.png" alt-text="분할된 데이터베이스":::

## <a name="a-practical-example-of-colocation"></a>공동 배치의 실제 예제

다중 테넌트 웹 분석 SaaS에 포함될 수 있는 다음 테이블을 살펴보세요.

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

이제 고객용 대시보드에서 실행될 수 있는 쿼리에 응답하려고 합니다. 예제 쿼리는 “테넌트 6에서 ‘/blog’로 시작하는 모든 페이지에 대한 지난 주의 방문 횟수 반환”입니다.

데이터가 단일 서버 배포 옵션을 사용하는 경우 SQL에서 제공하는 다양한 관계형 작업 집합을 통해 쿼리를 쉽게 표현할 수 있습니다.

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

이 쿼리의 [작업 집합](https://en.wikipedia.org/wiki/Working_set)이 메모리에 들어가기만 있으면 단일 서버 테이블이 적절한 솔루션입니다. 하이퍼스케일(Citus) 배포 옵션을 사용하여 데이터 모델을 스케일링할 수 있는 기회를 살펴봅시다.

### <a name="distribute-tables-by-id"></a>ID로 테이블 배포

단일 서버 쿼리는 테넌트 수와 각 테넌트에 대해 저장된 데이터가 증가함에 따라 속도가 느려지기 시작합니다. 작업 집합이 더 이상 메모리에 들어가지 않고 CPU가 병목 상태가 됩니다.

이 경우 하이퍼스케일(Citus)을 사용하여 여러 노드에 데이터를 분할할 수 있습니다. 분할을 결정할 때 선택해야 하는 가장 중요한 첫 번째 사항은 배포 열입니다. event 테이블의 `event_id` 및 `page` 테이블의 `page_id`를 사용하는 기본 선택 항목으로 시작해 봅시다.

```sql
-- naively use event_id and page_id as distribution columns

SELECT create_distributed_table('event', 'event_id');
SELECT create_distributed_table('page', 'page_id');
```

데이터가 여러 작업자에게 분산된 경우 단일 PostgreSQL 노드에서와 같이 조인을 수행할 수 없습니다. 대신, 다음 두 개의 쿼리를 실행해야 합니다.

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

그런 다음 두 단계의 결과를 애플리케이션에서 결합해야 합니다.

쿼리를 실행하려면 여러 노드에 분할된 데이터베이스의 데이터를 참조해야 합니다.

:::image type="content" source="media/concepts-hyperscale-colocation/colocation-inefficient-queries.png" alt-text="비효율적인 쿼리":::

이 경우 데이터 배포 시 다음과 같은 중요한 단점이 생깁니다.

-   각 분할된 데이터베이스를 쿼리하고 여러 쿼리를 실행하는 오버헤드
-   클라이언트에 많은 행을 반환하는 Q1의 오버헤드
-   Q2가 커집니다.
-   여러 단계로 쿼리를 작성해야 하므로 애플리케이션을 변경해야 합니다.

데이터가 분산되어 있으므로 쿼리를 병렬 처리할 수 있습니다. 쿼리에서 수행하는 작업량이 많은 분할된 데이터베이스를 쿼리하는 오버헤드보다 훨씬 큰 경우에만 유용합니다.

### <a name="distribute-tables-by-tenant"></a>테넌트별 테이블 배포

하이퍼스케일(Citus)에서 배포 열 값이 같은 행은 동일한 노드에 배치됩니다. `tenant_id`를 배포 열로 사용하여 처음부터 테이블을 새로 만들 수 있습니다.

```sql
-- co-locate tables by using a common distribution column
SELECT create_distributed_table('event', 'tenant_id');
SELECT create_distributed_table('page', 'tenant_id', colocate_with => 'event');
```

이제 하이퍼스케일(Citus)에서 수정(Q1) 없이 원래의 단일 서버 쿼리에 응답할 수 있습니다.

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

tenant_id로 필터링 및 조인하기 때문에 하이퍼스케일(Citus)에서 특정 테넌트에 대한 데이터가 포함된 공동 배치된 분할된 데이터베이스 집합을 사용하여 전체 쿼리에 응답할 수 있음을 알고 있습니다. 단일 PostgreSQL 노드에서 한 단계로 쿼리에 응답할 수 있습니다.

:::image type="content" source="media/concepts-hyperscale-colocation/colocation-better-query.png" alt-text="향상된 쿼리":::

고유 제약 조건과 조인 조건에 테넌트 ID가 포함되도록 쿼리와 테이블 스키마를 변경해야 하는 경우가 있습니다. 이 변경 작업은 일반적으로 간단합니다.

## <a name="next-steps"></a>다음 단계

- [다중 테넌트 자습서](tutorial-design-database-hyperscale-multi-tenant.md)에서 테넌트 데이터를 공동 배치하는 방법을 참조하세요.
