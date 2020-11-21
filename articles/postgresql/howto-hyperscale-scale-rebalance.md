---
title: 균형 재조정 분할-Hyperscale (Citus)-Azure Database for PostgreSQL
description: 성능 향상을 위해 분할를 서버 전체에 고르게 분산
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: how-to
ms.date: 11/17/2020
ms.openlocfilehash: b69c4fc3ff16cf30181a3529f61af7126b92950b
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/21/2020
ms.locfileid: "95026389"
---
# <a name="rebalance-shards-in-hyperscale-citus-server-group"></a>Citus (Hyperscale) 서버 그룹에서 분할 리 밸런스

새로 추가 된 노드를 활용 하려면 분산 테이블 [분할](concepts-hyperscale-distributed-data.md#shards)의 균형을 다시 조정 해야 합니다 .이는 일부 분할을 기존 노드에서 새 노드로 이동 하는 것을 의미 합니다. 먼저 새 작업자의 프로 비전이 성공적으로 완료 되었는지 확인 합니다. 그런 다음 psql을 사용 하 여 클러스터 코디네이터 노드에 연결 하 고 다음을 실행 하 여 분할 된 분할을 시작 합니다.

```sql
SELECT rebalance_table_shards('distributed_table_name');
```

[Rebalance_table_shards](reference-hyperscale-functions.md#rebalance_table_shards) 함수는 해당 인수에서 이라는 테이블의 [공동 배치](concepts-hyperscale-colocation.md) 그룹에 있는 모든 테이블의 균형을 다시 조정 합니다. 따라서 분산 된 모든 테이블에 대해 함수를 호출할 필요는 없으며, 각 공동 배치 그룹의 대표 테이블에 대해이 함수를 호출 하기만 하면 됩니다.

**다음 단계**


- 서버 그룹 [성능 옵션](concepts-hyperscale-configuration-options.md)에 대해 자세히 알아보세요.
- [서버 그룹 확장](howto-hyperscale-scale-grow.md) 또는 축소
- [Rebalance_table_shards](reference-hyperscale-functions.md#rebalance_table_shards) 참조 자료를 참조 하세요.
