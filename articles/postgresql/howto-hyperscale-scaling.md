---
title: Azure Database for PostgreSQL-Hyperscale (Citus) 서버 그룹 크기 조정
description: 서버 그룹 메모리, 디스크 및 CPU 리소스를 조정 하 여 증가 된 부하 처리
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.topic: conceptual
ms.date: 9/17/2019
ms.openlocfilehash: a69b4988a5ee835381de986edaa5899b09aa9e4e
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/25/2019
ms.locfileid: "71262493"
---
# <a name="scale-a-hyperscale-citus-server-group"></a>Citus (Hyperscale) 서버 그룹 크기 조정

Citus (Azure Database for PostgreSQL-Hyperscale)는 늘어난 부하를 처리 하기 위한 셀프 서비스 크기 조정을 제공 합니다. Azure Portal를 사용 하면 새 작업자 노드를 쉽게 추가할 수 있습니다.

이렇게 하려면 Citus (Hyperscale) 서버 그룹의 **구성** 탭으로 이동 합니다.
**작업자 노드 수** 에 대 한 슬라이더를 끌어 값을 변경 합니다.

![리소스 슬라이더](./media/howto-hyperscale-scaling/01-sliders-workers.png)

"저장" 단추를 클릭 하 여 변경 된 값이 적용 되도록 합니다.

> [!NOTE]
> 증가 하 고 저장 한 후에는 슬라이더를 사용 하 여 작업자 노드 수를 줄일 수 없습니다.
>
> 또한이 사용자 인터페이스를 사용 하 여 코디네이터 또는 작업자에서 vCores 및 저장소를 조정할 수 없습니다. 코디네이터 또는 작업자 노드에 대 한 크기 조정 계산이 필요한 경우 지원 티켓을 엽니다.

새로 추가 된 노드를 활용 하려면 분산 테이블 [분할](concepts-hyperscale-distributed-data.md#shards)의 균형을 다시 조정 해야 합니다 .이는 일부 분할을 기존 노드에서 새 노드로 이동 하는 것을 의미 합니다. 분할 된 다시 분산 장치를 시작 하려면 psql을 사용 하 여 클러스터 코디네이터 노드에 연결 하 고 다음을 실행 합니다.

```sql
SELECT rebalance_table_shards('distributed_table_name');
```

함수 `rebalance_table_shards` 는 해당 인수에서 이라는 테이블의 [공동 배치](concepts-hyperscale-colocation.md) 그룹에 있는 모든 테이블의 균형을 다시 조정 합니다. 따라서 분산 된 모든 테이블에 대해 함수를 호출할 필요는 없으며, 각 공동 배치 그룹의 대표 테이블에 대해이 함수를 호출 하기만 하면 됩니다.

## <a name="next-steps"></a>다음 단계

서버 그룹 [성능 옵션](concepts-hyperscale-configuration-options.md)에 대해 자세히 알아보세요.
