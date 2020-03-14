---
title: 크기 조정 서버 그룹-Hyperscale (Citus)-Azure Database for PostgreSQL
description: 서버 그룹 메모리, 디스크 및 CPU 리소스를 조정 하 여 증가 된 부하 처리
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.topic: conceptual
ms.date: 12/17/2019
ms.openlocfilehash: bec2a40d8cf5fb178418ec6bb59a52a0bfe3eb8c
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/13/2020
ms.locfileid: "79280444"
---
# <a name="scale-a-hyperscale-citus-server-group"></a>Citus (Hyperscale) 서버 그룹 크기 조정

Citus (Azure Database for PostgreSQL-Hyperscale)는 늘어난 부하를 처리 하기 위한 셀프 서비스 크기 조정을 제공 합니다. Azure Portal를 사용 하면 새 작업자 노드를 쉽게 추가 하 고 기존 노드의 vCores를 늘릴 수 있습니다.

## <a name="add-worker-nodes"></a>작업자 노드 추가

노드를 추가 하려면 Citus (Hyperscale) 서버 그룹의 **구성** 탭으로 이동 합니다.  **작업자 노드 수** 에 대 한 슬라이더를 끌면 값이 변경 됩니다.

![리소스 슬라이더](./media/howto-hyperscale-scaling/01-sliders-workers.png)

**저장** 단추를 클릭 하 여 변경 된 값이 적용 되도록 합니다.

> [!NOTE]
> 증가 하 고 저장 한 후에는 슬라이더를 사용 하 여 작업자 노드 수를 줄일 수 없습니다.

### <a name="rebalance-shards"></a>분할 리 밸런스

새로 추가 된 노드를 활용 하려면 분산 테이블 [분할](concepts-hyperscale-distributed-data.md#shards)의 균형을 다시 조정 해야 합니다 .이는 일부 분할을 기존 노드에서 새 노드로 이동 하는 것을 의미 합니다. 먼저 새 작업자의 프로 비전이 성공적으로 완료 되었는지 확인 합니다. 그런 다음 psql을 사용 하 여 클러스터 코디네이터 노드에 연결 하 고 다음을 실행 하 여 분할 된 분할을 시작 합니다.

```sql
SELECT rebalance_table_shards('distributed_table_name');
```

`rebalance_table_shards` 함수는 해당 인수에서 이라는 테이블의 [공동 배치](concepts-hyperscale-colocation.md) 그룹에 있는 모든 테이블의 균형을 다시 조정 합니다. 따라서 분산 된 모든 테이블에 대해 함수를 호출할 필요는 없으며, 각 공동 배치 그룹의 대표 테이블에 대해이 함수를 호출 하기만 하면 됩니다.

## <a name="increase-vcores"></a>VCores 늘리기

새 노드를 추가 하는 것 외에도 기존 노드의 기능을 늘릴 수 있습니다. 이 기능은 현재 미리 보기 상태입니다. 서버 그룹의 노드에 대해 증가 하는 vCores를 요청 하려면 [Azure 지원에 문의](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)하세요.

## <a name="next-steps"></a>다음 단계

서버 그룹 [성능 옵션](concepts-hyperscale-configuration-options.md)에 대해 자세히 알아보세요.
