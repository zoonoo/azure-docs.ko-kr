---
title: 서버 그룹 스케일링 - 하이퍼스케일(Citus) - Azure Database for PostgreSQL
description: 서버 그룹 메모리, 디스크 및 CPU 리소스를 조정하여 증가된 부하 처리
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: how-to
ms.date: 11/17/2020
ms.openlocfilehash: 59e6e73c99569b0a35c56d65c1a7ccdfcb394c0f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "95026423"
---
# <a name="scale-a-hyperscale-citus-server-group"></a>하이퍼스케일(Citus) 서버 그룹 스케일링

Azure Database for PostgreSQL - 하이퍼스케일(Citus)은 늘어난 부하를 처리하기 위한 셀프 서비스 스케일링 기능을 제공합니다. Azure Portal을 사용하면 쉽게 새 작업자 노드를 추가하고 기존 노드의 vCore를 늘릴 수 있습니다. 노드를 추가해도 가동 중지 시간이 발생하지 않으며 쿼리를 중단하지 않고 분할된 데이터베이스가 새 노드로 이동됩니다([분할된 데이터베이스 리밸런싱](howto-hyperscale-scale-rebalance.md)이라고 함).

## <a name="add-worker-nodes"></a>작업자 노드 추가

노드를 추가하려면 하이퍼스케일(Citus) 서버 그룹의 **컴퓨팅 + 스토리지** 탭으로 이동합니다.  **작업자 노드 수** 에 대한 슬라이더를 끌어 값을 변경합니다.

:::image type="content" source="./media/howto-hyperscale-scaling/01-sliders-workers.png" alt-text="리소스 슬라이더":::

**저장** 단추를 클릭하여 변경된 값을 적용합니다.

> [!NOTE]
> 작업자 노드 수를 늘리고 저장한 다음에는 슬라이더를 사용하여 줄일 수 없습니다.

> [!NOTE]
> 새로 추가된 노드를 활용하려면 [분산 테이블 분할된 데이터베이스를 리밸런스](howto-hyperscale-scale-rebalance.md)해야 합니다. 이는 일부 [분할된 데이터베이스](concepts-hyperscale-distributed-data.md#shards)를 기존 노드에서 새 노드로 이동하는 것을 의미합니다.

## <a name="increase-or-decrease-vcores-on-nodes"></a>노드에서 vCore 늘리기 또는 줄이기

새 노드를 추가할 뿐만 아니라 기존 노드의 기능을 향상할 수 있습니다. 컴퓨팅 용량을 늘리거나 줄이면 트래픽 수요의 단기 또는 장기 변경뿐만 아니라 성능 실험에 유용할 수 있습니다.

모든 작업자 노드에 대한 vCore를 변경하려면 **구성(작업자 노드당)** 에서 **vCore** 슬라이더를 조정합니다. 코디네이터 노드의 vCore는 독립적으로 조정할 수 있습니다. **구성(코디네이터 노드)** 에서 **vCore** 슬라이더를 조정합니다.

## <a name="next-steps"></a>다음 단계

- 서버 그룹 [성능 옵션](concepts-hyperscale-configuration-options.md)에 대해 자세히 알아보기
- 모든 작업자 노드가 병렬 쿼리에 참여할 수 있도록 [분산 테이블 분할된 데이터베이스 리밸런스](howto-hyperscale-scale-rebalance.md)
