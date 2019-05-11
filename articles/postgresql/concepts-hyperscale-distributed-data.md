---
title: Azure Database for PostgreSQL – 대규모 (Citus) (미리 보기)에서 분산된 된 데이터
description: 테이블 및 서버 그룹에 배포 하는 분할 된 데이터베이스입니다.
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
# <a name="distributed-data-in-azure-database-for-postgresql--hyperscale-citus-preview"></a>Azure Database for PostgreSQL – 대규모 (Citus) (미리 보기)에서 분산된 된 데이터

이 문서에서는 대규모 (Citus)에 세 개의 테이블 형식에 설명 합니다.
테이블 분산된 하는 방법을 보여 줍니다 분할 된 데이터베이스 및 분할 된 데이터베이스 노드에 배치 하는 방법으로 저장 됩니다.

## <a name="table-types"></a>테이블 형식

대규모 서버 그룹에 있는 테이블의 세 가지 종류가 있습니다, 각각 다른 용도로 사용 합니다.

### <a name="type-1-distributed-tables"></a>유형 1: 분산된 테이블

첫 번째 유형은 이며 대부분의 일반적인 *분산* 테이블입니다. SQL 문에 일반 테이블 표시는 않지만 가로로 *분할 된* 작업자 노드에서 합니다. 즉, 테이블의 행 이라고 하는 조각 테이블의 서로 다른 노드에 저장 된다는 것 *분할 된 데이터베이스*합니다.

대규모 SQL 뿐 아니라를 실행 하지만 작업자에 걸쳐 모든 테이블의 분할 된 데이터베이스를 업데이트 하려면 배포 된 테이블의 스키마를 변경 하므로 클러스터 전체에서 DDL 문 계단식으로 배열 합니다.

#### <a name="distribution-column"></a>배포 열

대규모 알고리즘 분할을 사용 하 여 분할 된 데이터베이스에 행을 할당 합니다. 할당 호출 하는 테이블 열의 값에 따라 명확 하 게 이루어집니다는 *배포 열입니다.* 클러스터 관리자는 테이블을 배포 하는 경우이 열을 지정 해야 합니다.
올바른 선택을 수행 하는 것이 성능 및 기능에 대 한 중요 합니다.

### <a name="type-2-reference-tables"></a>유형 2: 참조 테이블

참조 테이블은 분할 된 단일 데이터베이스에 전체 내용이 집중 되어 분산된 테이블의 형식. 분할 된 데이터베이스는 모든 작업자에 대 한 쿼리는 다른 노드에서 행을 요청 하는 네트워크 오버 헤드 없이 참조 정보를 로컬로 액세스할 수 있도록 모든 작업자에서 복제 됩니다. 참조 테이블 행 마다 별도 분할 된 데이터베이스를 구분 하지 않아도 되므로 배포 열을 있습니다.

참조 테이블은 일반적으로 작지만 이며 모든 작업자 노드에서 실행 되는 쿼리를 관련 된 데이터를 저장 하는 데 사용 됩니다. 예를 들어 주문 상태 또는 제품 범주와 같은 값을 열거 합니다.

### <a name="type-3-local-tables"></a>유형 3: 로컬 테이블

대규모를 사용 하면 연결할 코디네이터 노드는 일반 PostgreSQL 데이터베이스. 일반 테이블 코디네이터에서 만들고 분할 필요가 선택할 수 있습니다.

로컬 테이블에 대 한 좋은 후보는 작은 관리 테이블을 쿼리 조인에에서 참여 하지 않는 것입니다. 예를 들어, 응용 프로그램 로그인 및 인증에 대 한 사용자 테이블입니다.

## <a name="shards"></a>분할 된 데이터베이스

이전 섹션 설명된 어떻게 분산된 테이블은 작업자 노드에서 분할 된 데이터베이스로 저장 됩니다. 이 섹션에서는 자세한 기술 세부 정보를 가져옵니다.

`pg_dist_shard` 코디네이터 테이블 메타 데이터 시스템에서 각 분산된 테이블의 각 분할 된 데이터베이스에 대 한 행을 포함 합니다. 행 (shardminvalue, shardmaxvalue) 해시 공간에서 정수의 범위를 사용 하 여 분할 된 데이터베이스 ID를 찾습니다.

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

### <a name="shard-placements"></a>분할 된 데이터베이스 위치

102027 해당 분할 된 데이터베이스는 해당 행을 사용 하 여 연결을 가정 합니다. 행을 읽거나 이라는 테이블에 기록 `github_events_102027` 작업자 중 하나에 있습니다. 작업자? 전적으로 메타 데이터 테이블에 의해 결정 됩니다 및 작업자로 분할 된 데이터베이스 매핑의 분할 된 데이터베이스 라고 *배치*합니다.

코디네이터 노드는 같은 특정 테이블을 참조 하는 조각으로 쿼리를 다시 작성 `github_events_102027`, 해당 조각 적절 한 작업자에서 실행 됩니다. 분할 된 데이터베이스 ID 102027 보유 노드를 찾는 백그라운드에서 실행 하는 쿼리의 예는 다음과 같습니다.

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
- 하는 방법을 알아봅니다 [배포 열 선택](concepts-hyperscale-choose-distribution-column.md) 분산 테이블
