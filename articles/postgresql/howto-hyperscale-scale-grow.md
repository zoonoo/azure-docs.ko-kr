---
title: 크기 조정 서버 그룹-Hyperscale (Citus)-Azure Database for PostgreSQL
description: 서버 그룹 메모리, 디스크 및 CPU 리소스를 조정 하 여 증가 된 부하 처리
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: how-to
ms.date: 11/17/2020
ms.openlocfilehash: 59e6e73c99569b0a35c56d65c1a7ccdfcb394c0f
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/21/2020
ms.locfileid: "95026423"
---
# <a name="scale-a-hyperscale-citus-server-group"></a>Citus (Hyperscale) 서버 그룹 크기 조정

Citus (Azure Database for PostgreSQL-Hyperscale)는 늘어난 부하를 처리 하기 위한 셀프 서비스 크기 조정을 제공 합니다. Azure Portal를 사용 하면 새 작업자 노드를 쉽게 추가 하 고 기존 노드의 vCores를 늘릴 수 있습니다. 노드를 추가 하면 가동 중지 시간이 발생 하지 않으며 쿼리를 중단 하지 않고 분할를 새 노드 (분할 된 [균형 재조정](howto-hyperscale-scale-rebalance.md)이라고 함)로 이동 하기도 합니다.

## <a name="add-worker-nodes"></a>작업자 노드 추가

노드를 추가 하려면 Citus (Hyperscale) 서버 그룹의 **Compute + storage** 탭으로 이동 합니다.  **작업자 노드 수** 에 대 한 슬라이더를 끌면 값이 변경 됩니다.

:::image type="content" source="./media/howto-hyperscale-scaling/01-sliders-workers.png" alt-text="리소스 슬라이더":::

**저장** 단추를 클릭 하 여 변경 된 값이 적용 되도록 합니다.

> [!NOTE]
> 증가 하 고 저장 한 후에는 슬라이더를 사용 하 여 작업자 노드 수를 줄일 수 없습니다.

> [!NOTE]
> 새로 추가 된 노드를 활용 하려면 [분산 테이블 분할의 균형](howto-hyperscale-scale-rebalance.md)을 다시 조정 해야 합니다 .이는 일부 [분할](concepts-hyperscale-distributed-data.md#shards) 을 기존 노드에서 새 노드로 이동 하는 것을 의미 합니다.

## <a name="increase-or-decrease-vcores-on-nodes"></a>노드에서 vCores 증가 또는 감소

새 노드를 추가 하는 것 외에도 기존 노드의 기능을 늘릴 수 있습니다. 계산 용량을 늘리거나 줄일 수 있는 것은 성능 실험 뿐만 아니라 트래픽 요구에 대 한 단기 또는 장기 변경에 유용할 수 있습니다.

모든 작업자 노드에 대 한 vCores를 변경 하려면 **구성 (작업자 노드당)** 에서 **vcores** 슬라이더를 조정 합니다. 코디네이터 노드의 vCores는 독립적으로 조정할 수 있습니다. **구성 (코디네이터 노드)** 에서 **vcores** 슬라이더를 조정 합니다.

## <a name="next-steps"></a>다음 단계

- 서버 그룹 [성능 옵션](concepts-hyperscale-configuration-options.md)에 대해 자세히 알아보세요.
- 모든 작업자 노드가 병렬 쿼리에 참여할 수 있도록 [분산 테이블 분할 균형](howto-hyperscale-scale-rebalance.md) 조정
