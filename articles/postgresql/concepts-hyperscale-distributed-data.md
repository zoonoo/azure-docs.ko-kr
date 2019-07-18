---
title: Azure Database for PostgreSQL – 하이퍼스케일(Citus)(미리 보기)에서 분산된 데이터
description: 서버 그룹에 분산된 테이블 및 분할입니다.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 05/06/2019
ms.openlocfilehash: 9020ee690d93a1b477471fac4a482a909fca5935
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "65077337"
---
# <a name="distributed-data-in-azure-database-for-postgresql--hyperscale-citus-preview"></a>Azure Database for PostgreSQL – 하이퍼스케일(Citus)(미리 보기)에서 분산된 데이터

이 문서에서는 하이퍼스케일(Citus)의 세 개의 테이블 형식에 대해 설명합니다.
분산된 테이블이 분할로 저장되는 방법과 분할이 노드에 배치되는 방법을 보여줍니다.

## <a name="table-types"></a>테이블 형식

하이퍼스케일 서버 그룹에는 각각 다른 용도로 사용되는 세 가지 유형의 테이블이 있습니다.

### <a name="type-1-distributed-tables"></a>유형 1: 분산된 테이블

첫 번째 유형이자 가장 일반적인 유형은 *분산* 테이블입니다. SQL 문에서는 일반 테이블로 표시되지만 작업자 노드에 걸쳐 가로로 *분할*됩니다. 즉, 테이블의 행이 *분할*이라 불리는 조각 테이블로 서로 다른 노드에 저장된다는 것을 의미합니다.

하이퍼스케일은 클러스터 전체에서 SQL 뿐만 아니라 DDL 문도 실행하므로 분산 테이블의 스키마를 변경하면 전체 테이블의 분할을 업데이트할 수 있습니다.

#### <a name="distribution-column"></a>분산 열

하이퍼스케일은 분할에 열을 할당하기 위해 알고리즘 분할을 사용합니다. 그 할당은 *분산 열*이라는 불리는 테이블 열의 값에 따라 결정되어 이루어집니다. 클러스터 관리자는 테이블을 분산하는 경우 이 열을 지정해야 합니다.
올바른 선택을 하는 것이 성능 및 기능을 위해 중요합니다.

### <a name="type-2-reference-tables"></a>유형 2: 참조 테이블

참조 테이블은 전체 내용이 단일 분할에 집중된 분할 테이블의 유형입니다. 그 분할은 모든 작업자로 복제되어 다른 노드에서 행을 요청하는 네트워크 오버헤드 없이 모든 작업자 쿼리는 로컬에서 참조 정보를 액세스할 수 있습니다. 참조 테이블은 행마다 별도 분할을 구분하지 않아도 되므로 분산 열이 없습니다.

참조 테이블은 일반적으로 작으며 모든 작업자 노드에서 실행되는 쿼리와 관련된 데이터를 저장하는 데 사용됩니다. 예를 들어 주문 상태 또는 제품 범주와 같은 값을 열거합니다.

### <a name="type-3-local-tables"></a>유형 3: 로컬 테이블

하이퍼스케일을 사용할 때 연결할 코디네이터 노드는 일반 PostgreSQL 데이터베이스입니다. 코디네이터에서 일반 테이블을 만들고 분할하지 않도록 선택할 수 있습니다.

좋은 로컬 테이블 후보는 쿼리 조인에 참여하지 않는 작은 관리 테이블입니다. 예를 들어, 응용 프로그램 로그인 및 인증을 위한 사용자 테이블입니다.

## <a name="shards"></a>분할

이전 섹션에서 분산 테이블이 작업자 노드에서 분할된 데이터베이스로 저장되는 방법을 설명했습니다. 이 섹션에서는 자세한 기술 세부 정보를 다룹니다.

코디네이터의 `pg_dist_shard` 메타데이터 테이블은 시스템에서 각 분산된 테이블의 각 분할된 데이터베이스에 대한 행을 포함합니다. 해당 행은 각 분할된 데이터베이스 ID를 해시 공간(shardminvalue, shardmaxvalue) 내 정수의 범위와 매칭합니다.

```sql
SELECT * from pg_dist_shard;
 logicalrelid  | shardid | shardstorage | shardminvalue | shardmaxvalue 
---------------+---------+--------------+---------------+---------------
 github_events |  102026 | t            | 268435456     | 402653183
 github_events |  102027 | t            | 402653184     | 536870911
 github_events |  102028 | t            | 536870912     | 671088639
 github_events |  102029 | t            | 671088640     | 805306367
 (4 rows)
```

코디네이터 노드는 분할 된 데이터베이스의 행을 보유를 결정 하려는 경우 `github_events`, 행에는 배포 열의 값을 해시 하 고 어떤 분할 영역이 확인\'의범위 해시 값을 포함 합니다. (범위 정의 된 이미지 해시 함수를 해당 비연속 union 되도록 합니다.)

### <a name="shard-placements"></a>분할 배치

분할 102027이 해당 행과 연관된다고 가정합니다. 행은 작업자 중 하나의 `github_events_102027` 테이블에 읽히거나 쓰이게 됩니다. 어느 작업자일까요? 이는 전적으로 메타 데이터 테이블에 의해 결정되며, 작업자에 분할을 매핑하는 것을 분할 *배치*라고 합니다.

코디네이터 노드는 `github_events_102027` 같은 특정 테이블을 참조하는 조각으로 쿼리를 다시 작성하고, 적절한 작업자에서 해당 조각을 실행합니다. 분할 ID 102027을 보유하는 노드를 찾기 위해 백그라운드에서 실행되는 쿼리의 예는 다음과 같습니다.

```sql
SELECT
    shardid,
    node.nodename,
    node.nodeport
FROM pg_dist_placement placement
JOIN pg_dist_node node
  ON placement.groupid = node.groupid
 AND node.noderole = 'primary'::noderole
WHERE shardid = 102027;
```

    ┌─────────┬───────────┬──────────┐
    │ shardid │ nodename  │ nodeport │
    ├─────────┼───────────┼──────────┤
    │  102027 │ localhost │     5433 │
    └─────────┴───────────┴──────────┘

## <a name="next-steps"></a>다음 단계
- 분산된 테이블을 위한 [분산 열을 선택](concepts-hyperscale-choose-distribution-column.md)하는 방법을 알아봅니다.
