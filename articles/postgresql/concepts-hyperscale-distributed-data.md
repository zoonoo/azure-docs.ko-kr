---
title: Azure Database for PostgreSQL – 하이퍼스케일 (Citus) (미리 보기)에서 분산된 데이터
description: 서버 그룹에 분산된 테이블 및 분할입니다.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 05/06/2019
ms.openlocfilehash: 9020ee690d93a1b477471fac4a482a909fca5935
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65077337"
---
# <a name="distributed-data-in-azure-database-for-postgresql--hyperscale-citus-preview"></a>Azure Database for PostgreSQL – 하이퍼스케일 (Citus) (미리 보기)에서 분산된 데이터

이 문서에서는 하이퍼스케일 (Citus)에 세 개의 테이블 형식에 설명 합니다.
분산된 테이블이 분할로 저장되는 방법과 분할이 노드에 배치 되는 방법을 보여줍니다.

## <a name="table-types"></a>테이블 형식

하이퍼스케일 서버 그룹에는 각각 다른 용도로 사용되는 세 가지 유형의 테이블이 있습니다.

### <a name="type-1-distributed-tables"></a>유형 1: 분산된 테이블

첫 번째 유형은 이며 대부분의 일반적인 *분산* 테이블입니다. SQL 문에서는 일반 테이블로 표시되지만 작업자 노드에 걸쳐 가로로 *분할* 됩니다. 즉, 테이블의 행이 *분할*이라 불리는 조각 테이블로 서로 다른 노드에 저장 된다는 것을 의미합니다.

하이퍼스케일은 SQL 뿐만 아니라 클러스터 전체에서 DDL 구문도 실행하므로 분산된 테이블의 스키마의 변경이 작업자에 걸쳐 모든 테이블의 분할을 업데이트하기 위해 계단식으로 배열합니다.

#### <a name="distribution-column"></a>분산 열

하이퍼스케일은 분할에 열을 할당하기 위해 알고리즘 분할을 사용합니다. 그 할당은 *분산 열*이라는 불리는 테이블 열의 값에 따라 명확하게 이루어집니다.  클러스터 관리자는 테이블을 분산 하는 경우이 열을 지정 해야 합니다.
올바른 선택을 수행 하는 것이 성능 및 기능에 대해 중요합니다.

### <a name="type-2-reference-tables"></a>유형 2: 참조 테이블

참조 테이블은 전체 내용이 단일 분할에 집중된 분할된 테이블의 유형입니다. 그 분할은 모든 노드로 복제되어 다른 노드에서 행을 요청하는 네트워크 오버헤드 없이 작업자에서 쿼리는 로컬에서 참조 정보를 액세스할 수 있습니다. 참조 테이블은 행 마다 별도 분할을 구분하지 않아도 되므로 분산 열이 없습니다..

참조 테이블은 일반적으로 작으며 모든 작업자 노드에서 실행 되는 쿼리와 관련된 데이터를 저장하는데 사용됩니다. 예를 들어 주문 상태 또는 제품 범주와 같은 값을 열거 합니다.

### <a name="type-3-local-tables"></a>유형 3: 로컬 테이블

하이퍼스케일을 사용할 때 연결할 코디네이터 노드는 일반 PostgreSQL 데이터베이스입니다. 코디네이터에서 일반 테이블을 만들고 분할하지 않도록 선택할 수 있습니다.

로컬 테이블에 대 한 좋은 후보는 쿼리 조인에 참여하는 않는 작은 관리 테이블입니다. 예를 들어, 응용 프로그램 로그인 및 인증에 대한 사용자 테이블입니다.

## <a name="shards"></a>분할

이전 섹션에서 분산된 테이블이 작업자 노드에서 샤드로 저장되는 방법을 설명했습니다. 이 섹션에서는 자세한 기술 세부 정보를 가져옵니다.

코디네이터의 `pg_dist_shard` 메타데이터 테이블은  시스템에서 각 분산된 테이블의 각샤드에 대 한 행을 포함 합니다. 행은 해시 공간(shardminvalue, shardmaxvalue)에서 정수의 범위를 사용하여 샤드 ID와 일치합니다.

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

분할이 `github_events`의 행을 보유하는 것을 코디네이터 노드가 결정하려는 경우, 행에서 분산 열의 값을 해시하고 해시 값을 포함하는 분할의 범위를 확인합니다. (범위를 정의하여 해시 함수의 이미지가 비연속 조합이 되도록 합니다.)

### <a name="shard-placements"></a>분할 배치

질문에서 해당 분할 102027이 해당 행과 연관된 것으로 가정합니다. 그 행은 작업자 중의 하나에서 `github_events_102027`이라는 테이블을 읽거나 쓸 것입니다. 그것은 전적으로 메타 데이터 테이블에 의해 결정 되며 작업자로 분할 매핑은 분할 *배치*로 알려져 있습니다.

코디네이터 노드는 `github_events_102027` 같은 특정 테이블을 참조 하는 조각으로 쿼리를 다시 작성하고, 적절한 작업자에서 해당 조각을 실행합니다. 분할 ID 102027을 보유하는 노드를 찾기 위해 백그라운드에서 실행되는 쿼리의 예는 다음과 같습니다.

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
