---
title: 고가용성 구성 - 하이퍼스케일(Citus) - Azure Database for PostgreSQL
description: 고가용성을 사용하거나 사용하지 않도록 설정하는 방법
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: how-to
ms.date: 07/27/2020
ms.openlocfilehash: 46b842994cbcf7efe66d5992c79246d77626e268
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "90907389"
---
# <a name="configure-hyperscale-citus-high-availability"></a>하이퍼스케일(Citus) 고가용성 구성

Azure Database for PostgreSQL - 하이퍼스케일(Citus)은 데이터베이스 가동 중지 시간을 방지하기 위한 HA(고가용성)를 제공합니다. HA를 사용하도록 설정하면 서버 그룹의 모든 노드에 대기 서버가 제공됩니다. 원래 노드가 비정상 상태가 되면 노드를 대체하기 위해 대기 서버가 승격됩니다.

> [!IMPORTANT]
> HA는 그룹의 서버 수를 두 배로 늘리기 때문에 비용도 두 배로 증가합니다.

서버 그룹을 만들 때 또는 나중에 Azure Portal의 서버 그룹 **컴퓨팅 + 스토리지** 탭에서 HA를 사용하도록 설정할 수 있습니다. 사용자 인터페이스는 두 경우 모두 유사하게 표시됩니다. **고가용성** 슬라이더를 아니요에서 예로 끕니다.

:::image type="content" source="./media/howto-hyperscale-high-availability/01-ha-slider.png" alt-text="HA 슬라이더":::

**저장** 단추를 클릭하여 선택 내용을 적용합니다. HA를 사용하도록 설정하는 경우 서버 그룹에서 대기 서버를 프로비저닝하고 데이터를 대기 서버로 스트리밍해야 하므로 시간이 걸릴 수 있습니다.

서버 그룹 **개요** 탭에는 모든 노드와 해당 대기 서버가 나열되며, 각 노드에서 HA가 사용하도록 설정되었는지 여부가 **고가용성** 열에 표시됩니다.

:::image type="content" source="./media/howto-hyperscale-high-availability/02-ha-column.png" alt-text="서버 그룹 개요의 HA 열":::

### <a name="next-steps"></a>다음 단계

[고가용성](concepts-hyperscale-high-availability.md)에 대해 자세히 알아봅니다.
