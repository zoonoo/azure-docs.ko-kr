---
title: 고가용성 구성 - 하이퍼스케일(Citus) - PostgreSQL용 Azure 데이터베이스
description: 고가용성을 사용 하거나 사용 하지 않도록 설정 하는 방법
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: a8d4b5949b34d16191e9ec10a1dd39faff3660dc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74977666"
---
# <a name="configure-hyperscale-citus-high-availability"></a>하이퍼스케일(시터스) 고가용성 구성

PostgreSQL용 Azure 데이터베이스 - 하이퍼스케일(Citus)은 데이터베이스 가동 중지 시간을 피하기 위해 HA(고가용성)를 제공합니다. HA를 사용하도록 설정하면 서버 그룹의 모든 노드가 대기를 받게 됩니다. 원래 노드가 비정상상태가 되면 대기가 승격되어 노드를 대체합니다.

> [!IMPORTANT]
> HA는 그룹의 서버 수를 두 배로 늘리기 때문에 비용도 두 배가 됩니다.

HA 활성화는 서버 그룹을 만드는 동안 또는 나중에 Azure 포털의 서버 그룹에 대한 **구성** 탭에서 가능합니다. 사용자 인터페이스는 두 경우 모두 비슷합니다. **고가용성을** YES로 드래그합니다.

![하 슬라이더](./media/howto-hyperscale-high-availability/01-ha-slider.png)

**저장** 버튼을 클릭하여 선택 사항을 적용합니다. HA를 사용하도록 설정하면 서버 그룹이 대기를 프로비저닝하고 데이터를 스트리밍할 때 다소 시간이 걸릴 수 있습니다.

서버 그룹의 **개요** 탭에는 HA가 각 노드에 대해 성공적으로 활성화되었는지 여부를 나타내는 **고가용성** 열과 함께 모든 노드와 해당 대기가 나열됩니다.

![서버 그룹 개요의 ha 열](./media/howto-hyperscale-high-availability/02-ha-column.png)

### <a name="next-steps"></a>다음 단계

[고가용성에](concepts-hyperscale-high-availability.md)대해 자세히 알아보십시오.
