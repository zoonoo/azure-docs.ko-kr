---
title: 분산 데이터 - 하이퍼스케일(Citus) - Azure DB for PostgreSQL
description: Azure DB for PostgreSQL에서 분산 테이블, 참조 테이블, 로컬 테이블 및 분할된 데이터베이스에 대해 알아봅니다.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 05/06/2019
ms.openlocfilehash: 7757fdb4953640597a805c3d74a9e1ef08ef2c07
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "86114502"
---
# <a name="distributed-data-in-azure-database-for-postgresql--hyperscale-citus"></a>Azure DB for PostgreSQL에서 분산 데이터 – 하이퍼스케일(Citus)

이 문서에서는 Azure DB for PostgreSQL – 하이퍼스케일(Citus)의 세 가지 테이블 형식에 대해 간략하게 설명합니다.
분산 테이블이 분할된 데이터베이스로 저장되는 방식과 분할된 데이터베이스가 노드에 배치되는 방식을 보여줍니다.

## <a name="table-types"></a>테이블 유형

하이퍼스케일(Citus) 서버 그룹에는 각각 다른 용도로 사용되는 세 가지 유형의 테이블이 있습니다.

### <a name="type-1-distributed-tables"></a>유형 1: 분산 테이블

가장 일반적인 첫 번째 유형은 분산 테이블입니다. 이러한 테이블은 SQL 문에 대한 일반 테이블처럼 보이지만 작업자 노드 간에 수평 분할됩니다. 즉, 테이블의 행이 분할된 데이터베이스라는 조각 테이블의 다른 노드에 저장됩니다.

하이퍼스케일(Citus)은 클러스터 전체에서 SQL뿐만 아니라 DDL 문을 실행합니다.
분산 테이블의 스키마를 변경하면 작업자 간에 테이블의 모든 분할된 데이터베이스를 연속으로 업데이트합니다.

#### <a name="distribution-column"></a>배포 열

하이퍼스케일(Citus)은 분할할 행을 할당하기 위해 알고리즘 분할을 사용합니다. 할당은 배포 열이라는 테이블 열의 값에 따라 명확하게 결정됩니다. 클러스터 관리자는 테이블을 배포할 때 이 열을 지정해야 합니다.
성능 및 기능을 위해 적절한 선택이 중요합니다.

### <a name="type-2-reference-tables"></a>유형 2: 참조 테이블

참조 테이블은 전체 내용이 단일 분할된 데이터베이스로 집중되는 분산 테이블 유형입니다. 분할된 데이터베이스가 모든 작업자에 복제됩니다. 모든 작업자에 대한 쿼리는 다른 노드에서 행을 요청하는 네트워크 오버헤드 없이 참조 정보에 로컬로 액세스할 수 있습니다. 참조 테이블에는 행마다 개별 분할된 데이터베이스를 구분할 필요가 없으므로 배포 열이 없습니다.

참조 테이블은 일반적으로 작지만 모든 작업자 노드에서 실행되는 쿼리와 관련된 데이터를 저장하는 데 사용됩니다. 주문 상태 또는 제품 범주와 같은 열거형 값을 예로 들 수 있습니다.

### <a name="type-3-local-tables"></a>유형 3: 로컬 테이블

하이퍼스케일(Citus)을 사용하는 경우 연결하는 코디네이터 노드는 일반 PostgreSQL 데이터베이스입니다. 코디네이터에서 일반 테이블을 만들고 분할하지 않도록 선택할 수 있습니다.

로컬 테이블에 적합한 후보는 조인 쿼리에 참여하지 않는 작은 관리 테이블입니다. 애플리케이션 로그인 및 인증을 위한 사용자 테이블을 예로 들 수 있습니다.

## <a name="shards"></a>분할된 데이터베이스

이젠 섹션에서는 분산 테이블이 작업자 노드에서 분할된 데이터베이스로 저장되는 방법에 대해 다루었습니다. 이 섹션에서는 더 기술적인 세부 사항을 설명합니다.

코디네이터의 `pg_dist_shard` 메타데이터 테이블은 시스템에 있는 각 분산 테이블의 각 분할된 데이터베이스에 대한 행이 포함되어 있습니다. 행은 해시 공간의 정수 범위(shardminvalue, shardmaxvalue)와 분할된 데이터베이스 ID를 일치시킵니다.

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

코디네이터 노드가 `github_events`의 행을 보유하고 있는 분할된 데이터베이스를 확인하려는 경우 행의 배포 열 값을 해시합니다. 그런 다음 노드는 해시된 값을 포함하는 분할된 범위를 확인합니다. 범위는 해시 함수의 이미지가 분리 합집합이 되도록 정의됩니다.

### <a name="shard-placements"></a>분할된 데이터베이스 영역 배치

분할된 데이터베이스 102027이 문제의 행과 관련이 있다고 가정합니다. 작업자 중 하나의 `github_events_102027`이라는 테이블에서 행을 읽거나 씁니다. 어떤 작업자일까요? 작업자는 전적으로 메타데이터 테이블을 통해 결정됩니다. 작업자에 대한 분할된 데이터베이스의 매핑은 분할된 데이터베이스 영역 배치라고 합니다.

코디네이터 노드는 `github_events_102027`과 같은 특정 테이블을 참조하는 조각에 쿼리를 작성하며 적합한 작업자에서 해당 조각을 실행합니다. 다음은 분할된 데이터베이스 ID 102027을 보유하는 노드를 찾도록 내부적으로 실행되는 쿼리의 예입니다.

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

```output
┌─────────┬───────────┬──────────┐
│ shardid │ nodename  │ nodeport │
├─────────┼───────────┼──────────┤
│  102027 │ localhost │     5433 │
└─────────┴───────────┴──────────┘
```

## <a name="next-steps"></a>다음 단계

- 분산 테이블에 [배포 열을 선택](concepts-hyperscale-choose-distribution-column.md)하는 방법에 대해 알아봅니다.
