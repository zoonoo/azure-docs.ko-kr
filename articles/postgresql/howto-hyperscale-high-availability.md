---
title: 고가용성-Hyperscale (Citus) 구성-Azure Database for PostgreSQL
description: 고가용성을 사용 하거나 사용 하지 않도록 설정 하는 방법
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: how-to
ms.date: 07/27/2020
ms.openlocfilehash: f08fb01cf26ddf73d31b575242b27d7d8b4017d9
ms.sourcegitcommit: 3fc3457b5a6d5773323237f6a06ccfb6955bfb2d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/11/2020
ms.locfileid: "90033281"
---
# <a name="configure-hyperscale-citus-high-availability"></a>Citus (Hyperscale) 고가용성 구성

Citus (Azure Database for PostgreSQL-Hyperscale)는 데이터베이스 가동 중지 시간을 방지 하기 위해 HA (고가용성)를 제공 합니다. HA를 사용 하는 경우 서버 그룹의 모든 노드에는 대기 모드가 표시 됩니다. 원본 노드가 비정상 상태가 되 면이를 대체 하기 위해 대기를 승격 합니다.

> [!IMPORTANT]
> HA는 그룹에 있는 서버 수를 두 배로 증가 하기 때문에 비용을 두 배로 증가 시킬 수 있습니다.

HA를 사용 하도록 설정 하는 작업은 서버 그룹을 만들 때 또는 나중에 Azure Portal 서버 그룹의 **Compute + storage** 탭에서 수행할 수 있습니다. 사용자 인터페이스는 두 경우 모두 유사 하 게 보입니다. **높은 가용성** 을 위해 슬라이더를 아니요에서 예로 끕니다.

![ha 슬라이더](./media/howto-hyperscale-high-availability/01-ha-slider.png)

**저장** 단추를 클릭 하 여 선택 항목을 적용 합니다. HA를 사용 하도록 설정 하는 것은 서버 그룹이 해당 항목을 프로 비전 하 고 데이터를 스트리밍하는 데 시간이 걸릴 수 있습니다.

서버 그룹에 대 한 **개요** 탭에는 각 노드에 대해 HA를 사용 하도록 설정 했는지 여부를 나타내는 **고가용성** 열과 함께 모든 노드와 해당 노드가 나열 됩니다.

![서버 그룹 개요의 ha 열](./media/howto-hyperscale-high-availability/02-ha-column.png)

### <a name="next-steps"></a>다음 단계

[고가용성](concepts-hyperscale-high-availability.md)에 대해 자세히 알아보세요.
