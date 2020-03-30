---
title: 확장 서버 그룹 - 하이퍼스케일(Citus) - PostgreSQL용 Azure 데이터베이스
description: 서버 그룹 메모리, 디스크 및 CPU 리소스를 조정하여 증가된 부하처리
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.topic: conceptual
ms.date: 3/16/2020
ms.openlocfilehash: fa48ca287c248155a0271b5134be782d8db1c785
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80063110"
---
# <a name="scale-a-hyperscale-citus-server-group"></a>하이퍼스케일(Citus) 서버 그룹 확장

PostgreSQL용 Azure 데이터베이스 - 하이퍼스케일(Citus)은 증가하는 부하를 처리하기 위해 셀프 서비스 크기 조정을 제공합니다. Azure 포털을 사용하면 새 작업자 노드를 쉽게 추가하고 기존 노드의 vCore를 늘릴 수 있습니다.

## <a name="add-worker-nodes"></a>작업자 노드 추가

노드를 추가하려면 Citus(하이퍼스케일) 서버 그룹의 **구성** 탭으로 이동합니다.  **Worker 노드 수에** 대한 슬라이더를 드래그하여 값이 변경됩니다.

![리소스 슬라이더](./media/howto-hyperscale-scaling/01-sliders-workers.png)

**변경된** 값을 적용하려면 저장 단추를 클릭합니다.

> [!NOTE]
> 증가 및 저장되면 슬라이더를 사용하여 작업자 노드 수를 줄일 수 없습니다.

### <a name="rebalance-shards"></a>샤드 재조정

새로 추가된 노드를 활용하려면 분산 테이블 [샤드의](concepts-hyperscale-distributed-data.md#shards)균형을 조정해야 하며, 이는 일부 샤드를 기존 노드에서 새 노드로 이동하는 것을 의미합니다. 먼저 새 작업자가 프로비저닝을 성공적으로 완료했는지 확인합니다. 그런 다음 psql을 사용하여 클러스터 코디네이터 노드에 연결하고 실행하여 샤드 재조정기를 시작합니다.

```sql
SELECT rebalance_table_shards('distributed_table_name');
```

함수는 `rebalance_table_shards` 인수에 명명된 테이블의 [공동 위치](concepts-hyperscale-colocation.md) 그룹의 모든 테이블의 균형을 조정합니다. 따라서 모든 분산 테이블에 대한 함수를 호출 할 필요가 없으며 각 colocation 그룹의 대표 테이블에 호출할 수 있습니다.

## <a name="increase-or-decrease-vcores-on-nodes"></a>노드에서 vCore를 늘리거나 줄입니다.

> [!NOTE]
> 이 기능은 현재 미리 보기로 제공됩니다. 서버 그룹의 노드에 대한 vCore의 변경을 요청하려면 Azure 지원 에 [문의하십시오.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)

새 노드를 추가하는 것 외에도 기존 노드의 기능을 늘릴 수 있습니다. 컴퓨팅 용량을 위아래로 조정하는 것은 트래픽 요구에 대한 단기 또는 장기 변경뿐만 아니라 성능 실험에 유용할 수 있습니다.

모든 작업자 노드의 vCore를 변경하려면 **구성(작업자 노드당)에서** **vCores 슬라이더를** 조정합니다. 코디네이터 노드의 vCores는 독립적으로 조정할 수 있습니다. 코디네이터 노드 에서 **구성 변경** 링크를 **클릭합니다.** 코디네이터의 vCores 및 저장소 용량에 대한 슬라이더와 함께 대화 상자가 나타납니다. 원하는 대로 슬라이더를 변경하고 **확인을**선택합니다.

## <a name="next-steps"></a>다음 단계

서버 그룹 [성능 옵션에](concepts-hyperscale-configuration-options.md)대해 자세히 알아봅니다.
