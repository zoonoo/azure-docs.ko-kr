---
title: Azure Database for PostgreSQL의 서버 개념
description: 이 문서에서는 Azure Database for PostgreSQL 서버 구성 및 관리를 위한 고려 사항 및 지침을 제공합니다.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 05/06/2019
ms.openlocfilehash: d03cfd49887adf1f6a4650e374d3e13eeca735a4
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/27/2019
ms.locfileid: "65077472"
---
# <a name="table-colocation-in-azure-database-for-postgresql--hyperscale-citus-preview"></a>Azure database에서 for PostgreSQL – 대규모 (Citus) (미리 보기) 테이블 공동

공동 배치는 동일한 노드에 관련된 정보를 함께 저장 의미 합니다. 쿼리는 필요한 모든 데이터를 모든 네트워크 트래픽 없이 사용할 수 있는 경우에 빠르게 이동할 수 있습니다. 서로 다른 노드에 관련된 데이터를 공동 배치 쿼리를를 각 노드에서 동시에 효율적으로 실행할 수 있습니다.

## <a name="data-colocation-for-hash-distributed-tables"></a>해시 분산 테이블에 대 한 데이터 공동 배치

하이퍼 스케일, 해시 배포 열에 있는 값의 분할 된 데이터베이스의 해시 범위에 속하는 경우에 행 분할 된 데이터베이스에 저장 됩니다. 항상 동일한 해시 범위를 사용 하 여 분할 된 데이터베이스를 동일한 노드에 배치 됩니다. 균등 분포 열 값을 가진 행은 항상 동일한 노드에서 여러 테이블입니다.

![분할 된 데이터베이스](media/concepts-hyperscale-colocation/colocation-shards.png)

## <a name="a-practical-example-of-colocation"></a>공동 배치의 실제 예제

SaaS 다중 테 넌 트 웹 분석을 포함 될 수 있는 다음 테이블을 고려해 야 합니다.

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

이제는 고객 지향 대시보드에서 같은 발급 될 수 있습니다 하는 쿼리에 응답 해야 합니다. "로 시작 하는 모든 페이지에 대 한 지난 주의 방문 횟수를 반환 합니다. ' / 블로그 ' 테 넌 트 6."

데이터를 단일 서버 배포 옵션의 경우 다양 한 SQL에서 제공 하는 관계형 작업을 사용 하 여이 쿼리를 쉽게 표현할 수 것:

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

하기만 합니다 [작업 집합](https://en.wikipedia.org/wiki/Working_set) 단일 서버 테이블은 적절 한 솔루션에이 쿼리가 메모리에 적합 한에 대 한 합니다. 그러나 대규모 배포 옵션을 사용 하 여 데이터 모델 확장의 기회를 생각해 보겠습니다.

### <a name="distributing-tables-by-id"></a>ID 별로 테이블 배포

단일 서버 쿼리는 각 테 넌 트에 대 한 저장 된 데이터 및 테 넌 트 수가 증가 함에 따라 속도 저하를 시작 합니다. 작업 메모리의 맞춤을 설정 하 고 CPU는 병목 상태가 발생 합니다.

이 경우 대규모를 사용 하 여 여러 노드 간에 데이터를 분할 된 데이터베이스 수 있습니다. 가장 중요 한 첫 번째를 선택할 때 분할은 배포 열을 확인 해야 합니다. 사용 하는 것은 순진한 선택 하 여 시작 해 보겠습니다 `event_id` 이벤트 테이블 및 `page_id` 에 대 한는 `page` 테이블:

```sql
-- naively use event_id and page_id as distribution columns

SELECT create_distributed_table('event', 'event_id');
SELECT create_distributed_table('page', 'page_id');
```

데이터도 다른 작업자에 걸쳐 분산 되어 있는 경우 단일 PostgreSQL 노드에서 것 처럼 조인을 수행 수 없습니다. 대신, 두 개의 쿼리를 실행 해야 합니다.

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

그런 다음 두 단계의 결과 응용 프로그램 사용 하 여 결합 해야 합니다.

쿼리를 실행 노드를 분산 하는 분할 된 데이터베이스에 데이터를 참조 해야 합니다.

![비효율적인 쿼리](media/concepts-hyperscale-colocation/colocation-inefficient-queries.png)

이 경우 데이터 분포 상당한 단점을 만듭니다.

-   각 분할 된 데이터베이스에 여러 쿼리를 실행 중인 쿼리에서 오버 헤드
-   Q1 사용 하는 오버 헤드를 클라이언트에 여러 행을 반환
-   Q2 커지고
-   여러 단계에서 쿼리를 작성할 필요가 응용 프로그램을 변경할 필요가

데이터 분산 된 쿼리를 병렬 처리할 수 있습니다. 그러나 것만 유용한 쿼리를 수행 하는 작업의 양이 많은 분할 된 데이터베이스를 쿼리 하는 오버 헤드 보다 훨씬 큰 경우.

### <a name="distributing-tables-by-tenant"></a>테 넌 트 별로 테이블 배포

하이퍼 스케일, 동일한 배포 열 값을 가진 행 동일한 노드에서 되도록 보장 됩니다. 다시 시작을 만들 수 있습니다 사용 하 여 테이블 `tenant_id` 배포 열으로 합니다.

```sql
-- co-locate tables by using a common distribution column
SELECT create_distributed_table('event', 'tenant_id');
SELECT create_distributed_table('page', 'tenant_id', colocate_with => 'event');
```

이제 하이퍼 스케일 (Q1) 수정 하지 않고 원래 단일 서버 쿼리에 응답할 수 있습니다.

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

필터 및 tenant_id에 대 한 조인 때문에 대규모 전체 쿼리는 특정 테 넌 트에 대 한 데이터를 포함 하는 공동 배치 된 분할 된 데이터베이스 집합을 사용 하 여 응답할 수 있는지 알고 있습니다. 단일 PostgreSQL 노드 단일 단계에서 쿼리에 응답할 수 있습니다.

![더 나은 쿼리](media/concepts-hyperscale-colocation/colocation-better-query.png)

일부 경우에 쿼리 및 테이블 스키마를 반드시 조인 조건 및 unique 제약 조건에 테 넌 트 ID를 포함 하도록 변경 합니다. 그러나 이것이 일반적으로 간단 하 게 변경 합니다.

## <a name="next-steps"></a>다음 단계

- 테 넌 트 데이터에 배치 하는 방법을 참조는 [다중 테 넌 트 자습서](tutorial-design-database-hyperscale-multi-tenant.md)
