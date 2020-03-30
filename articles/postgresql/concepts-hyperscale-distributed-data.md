---
title: 분산 데이터 - 하이퍼스케일(Citus) - PostgreSQL용 Azure 데이터베이스
description: PostgreSQL용 Azure 데이터베이스에서 분산 테이블, 참조 테이블, 로컬 테이블 및 샤드에 대해 알아봅니다.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 05/06/2019
ms.openlocfilehash: ade7632dc042741a07bdb59e34e30b3fb464e0e9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79243654"
---
# <a name="distributed-data-in-azure-database-for-postgresql--hyperscale-citus"></a>PostgreSQL에 대 한 Azure 데이터베이스에 분산 된 데이터 – 하이퍼 스케일 (시터스)

이 문서에서는 PostgreSQL – 하이퍼스케일(Citus)에 대한 Azure 데이터베이스의 세 가지 테이블 유형에 대해 간략하게 설명합니다.
분산 테이블이 샤드로 저장되는 방법과 노드에 샤드가 배치되는 방식을 보여 주는 것입니다.

## <a name="table-types"></a>테이블 형식

하이퍼스케일(Citus) 서버 그룹에는 각각 다른 용도로 사용되는 세 가지 유형의 테이블이 있습니다.

### <a name="type-1-distributed-tables"></a>유형 1: 분산 테이블

첫 번째 형식이며 가장 일반적인 형식은 분산 테이블입니다. SQL 문에 대한 일반 테이블로 보이지만 작업자 노드 간에 수평으로 분할됩니다. 즉, 테이블의 행은 샤드라는 조각 테이블에 다른 노드에 저장됩니다.

하이퍼스케일(Citus)은 클러스터 전체에서 SQL뿐만 아니라 DDL 문을 실행합니다.
분산 테이블의 스키마를 변경하여 작업자 간에 테이블의 모든 샤드를 업데이트합니다.

#### <a name="distribution-column"></a>분포 열

하이퍼스케일(Citus)은 알고리즘 샤딩을 사용하여 샤드에 행을 할당합니다. 할당은 분포 열이라는 테이블 열의 값을 기반으로 결정적으로 이루어집니다. 클러스터 관리자는 테이블을 배포할 때 이 열을 지정해야 합니다.
올바른 선택을 하는 것은 성능과 기능에 중요합니다.

### <a name="type-2-reference-tables"></a>유형 2: 참조 테이블

참조 테이블은 전체 목차를 단일 샤드에 집중하는 분산 테이블의 유형입니다. 샤드는 모든 작업자에 복제됩니다. 모든 작업자의 쿼리는 다른 노드에서 행을 요청하는 네트워크 오버헤드 없이 로컬로 참조 정보에 액세스할 수 있습니다. 참조 테이블에는 행당 별도의 샤드를 구분할 필요가 없으므로 분포 열이 없습니다.

참조 테이블은 일반적으로 작으며 모든 작업자 노드에서 실행되는 쿼리와 관련된 데이터를 저장하는 데 사용됩니다. 예를 들어 주문 상태 또는 제품 범주와 같은 열거된 값이 있습니다.

### <a name="type-3-local-tables"></a>유형 3: 로컬 테이블

하이퍼스케일(Citus)을 사용하는 경우 연결하는 코디네이터 노드는 일반 PostgreSQL 데이터베이스입니다. 코디네이터에 일반 테이블을 만들고 샤드하지 않도록 선택할 수 있습니다.

로컬 테이블에 대한 좋은 후보는 조인 쿼리에 참여하지 않는 작은 관리 테이블입니다. 응용 프로그램 로그인 및 인증에 대한 사용자 테이블이 있습니다.

## <a name="shards"></a>파편

이전 섹션에서는 분산 테이블이 작업자 노드에 샤드로 저장되는 방법을 설명했습니다. 이 섹션에서는 자세한 기술 세부 사항에 대해 설명합니다.

코디네이터의 메타데이터 테이블에는 `pg_dist_shard` 시스템의 각 분산 테이블의 각 샤드에 대한 행이 포함되어 있습니다. 행은 해시 공간(샤드민값, 샤드맥스값)의 다양한 정수와 샤드 ID와 일치합니다.

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

코디네이터 노드가 행을 보유하는 샤드를 `github_events`확인하려는 경우 행의 분포 열 값을 해시합니다. 그런 다음 노드는\'해시된 값을 포함하는 샤드 s 범위를 확인합니다. 해시 함수의 이미지가 분리 결합이 되도록 범위가 정의됩니다.

### <a name="shard-placements"></a>샤드 배치

샤드 102027이 해당 행과 연결되어 있다고 가정합니다. 행은 작업자 중 하나에서 호출된 `github_events_102027` 테이블에서 읽거나 씁쓸합니다. 어떤 작업자? 이는 전적으로 메타데이터 테이블에 의해 결정됩니다. 샤드를 작업자로 매핑하는 것을 샤드 배치라고 합니다.

코디네이터 노드는 쿼리를 같은 `github_events_102027` 특정 테이블을 참조하고 해당 작업자에서 해당 조각을 실행하는 조각으로 다시 작성합니다. 다음은 샤드 ID 102027을 보유한 노드를 찾기 위해 무대 뒤에서 실행되는 쿼리의 예입니다.

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
- 분산 [테이블의 배포 열을 선택하는](concepts-hyperscale-choose-distribution-column.md) 방법을 알아봅니다.
