---
title: Azure Cosmos 컨테이너 또는 계정에 대해 정규화된 RU/s 모니터링
description: Azure Cosmos DB에서 작업의 정규화된 요청 단위 사용량을 모니터링하는 방법을 알아봅니다. 어떤 작업이 더 많은 요청 단위를 소비하는지를 Azure Cosmos DB 계정의 소유자가 이해할 수 있습니다.
ms.service: cosmos-db
ms.topic: how-to
author: kanshiG
ms.author: govindk
ms.date: 06/25/2020
ms.openlocfilehash: 8709389208ba1320685b1834b20893f08ef33ed7
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85482907"
---
# <a name="how-to-monitor-normalized-rus-for-an-azure-cosmos-container-or-an-account"></a>Azure Cosmos 컨테이너 또는 계정에 대해 정규화된 RU/s를 모니터링하는 방법

Azure Cosmos DB용 Azure Monitor는 계정을 모니터링하고 대시보드를 만들 수 있는 메트릭 보기를 제공합니다. Azure Cosmos DB 메트릭은 기본적으로 수집되며, 아무것도 명시적으로 활성화하거나 구성할 필요가 없습니다.

**정규화** 된 사용 메트릭은 파티션 키 범위 전반의 요청 단위 사용량과 관련 하 여 복제본의 포화 상태를 확인 하는 데 사용 됩니다. Azure Cosmos DB는 처리량을 모든 실제 파티션에 균등하게 분산합니다. 이 메트릭은 복제본 세트 내에서 최대 처리량 사용률의 초당 보기를 제공합니다. 이 메트릭을 사용 하 여 지정 된 컨테이너에 대 한 파티션 간 사용 현황을 계산 합니다. 이 메트릭을 사용하는 경우 요청 단위 사용률이 높으면 워크로드 요구를 충족하도록 처리량을 늘려야 합니다.

## <a name="what-to-expect-and-do-when-normalized-rus-is-higher"></a>정규화된 RU/s가 높은 경우 필요한 항목 및 수행할 작업

정규화된 RU/s 소비가 100%에 도달하면 클라이언트가 속도 제한 오류를 수신합니다. 클라이언트는 대기 시간을 준수하고 다시 시도해야 합니다. 100% 사용률에 도달하는 짧은 급등이 있으면, 복제본의 처리량이 최대 성능 한계에 도달했다는 의미입니다. 예를 들어, 복제본의 모든 RU/s를 소비하는 저장 프로시저와 같은 단일 작업을 수행하면 정규화된 RU/s 소비에 짧은 급등이 발생합니다. 이 경우 요청 속도가 낮으면 속도 제한 오류가 즉시 발생하지 않습니다. Azure Cosmos DB를 사용하면 요청이 특정 요청에 대해 프로비저닝된 RU/s를 초과하여 청구할 수 있고 해당 기간 내의 다른 요청은 속도가 제한되기 때문입니다.

Azure Monitor 메트릭은 **총 요청** 메트릭을 사용하여 상태 코드별로 작업을 찾는 데 유용합니다. 나중에 이러한 요청을 429 상태 코드로 필터링하고 **작업 유형**별로 분할할 수 있습니다.

속도가 제한된 요청을 찾는 데 권장되는 방법은 진단 로그를 통해 이 정보를 구하는 것이 좋습니다.

정규화된 RU/s 소비가 계속 최대치 100%이거나 100%에 가까우면 처리량을 늘리는 것이 좋습니다. Azure Monitor 메트릭 및 Azure Monitor 로그를 활용하여 부하가 높은 작업이 무엇인지 이 작업의 최대 사용량은 얼마인지 확인할 수 있습니다.

**정규화된 RU 소비** 메트릭은 사용량 측면에서 어떤 파티션 키 범위가 더 웜(warm)한지 그래서 파티션 키 범위에 대한 처리량 왜곡을 제공하는지 확인하는 데도 사용됩니다. 나중에 Azure Monitor 로그에서 **PartitionKeyRUConsumption** 로그를 확인하고 사용량 측면에서 어떤 논리 파티션 키가 핫(hot)한지에 대한 정보를 얻을 수 있습니다.

## <a name="view-the-normalized-request-unit-consumption-metric"></a>정규화된 요청 단위 소비 메트릭 보기

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.

2. 왼쪽 탐색 모음에서 **모니터**를 선택하고 **메트릭**을 선택합니다.

   :::image type="content" source="./media/monitor-normalized-request-units/monitor-metrics-blade.png" alt-text="Azure Monitor의 메트릭 창":::

3. **메트릭** 창 > **리소스 선택**에서 필요한 **구독** 및 **리소스 그룹**을 선택합니다. **리소스 유형**으로 **Azure Cosmos DB 계정**을 선택하고, 기존 Azure Cosmos 계정 중 하나를 선택한 후 **적용**을 선택합니다.

   :::image type="content" source="./media/monitor-normalized-request-units/select-cosmos-db-account.png" alt-text="메트릭을 볼 Azure Cosmos 계정 선택":::

4. 다음에는 사용 가능한 메트릭 목록에서 메트릭을 선택할 수 있습니다. 요청 단위, 스토리지, 대기 시간, 가용성, Cassandra 등과 관련된 메트릭을 선택할 수 있습니다. 이 목록에서 사용 가능한 모든 메트릭에 대해 자세히 알아 보려면 [범주별 메트릭](monitor-cosmos-db-reference.md) 문서를 참조하세요. 이 예에서는 **정규화된 RU 소비** 메트릭과 **Max**를 집계 값으로 선택하겠습니다.

   이러한 세부 정보 외에 메트릭의 **시간 범위**와 **시간 단위**를 선택할 수도 있습니다. 최대는 지난 30일 동안의 메트릭을 볼 수 있습니다.  필터를 적용하면 필터에 기반하여 차트가 표시됩니다.

   :::image type="content" source="./media/monitor-normalized-request-units/normalized-request-unit-usage-metric.png" alt-text="Azure Portal에서 메트릭 선택":::

### <a name="filters-for-normalized-request-unit-consumption"></a>정규화된 요청 단위 소비에 대한 필터

특정 **CollectionName**, **DatabaseName**, **PartitionKeyRangeID**, **Region**별로 표시되는 메트릭 및 차트를 필터링할 수도 있습니다. 메트릭을 필터링하려면 **필터 추가**를 선택하고 필요한 속성(예: **CollectionName** 및 관심 있는 값)을 선택합니다. 그러면 선택한 기간 동안 컨테이너에 소비된 정규화된 RU 소비 단위가 그래프에 표시됩니다.  

**분할 적용** 옵션을 사용하여 메트릭을 그룹화할 수 있습니다.  

각 컨테이너에 대한 정규화된 요청 단위 소비 메트릭은 다음 이미지와 같이 표시됩니다.

:::image type="content" source="./media/monitor-normalized-request-units/normalized-request-unit-usage-filters.png" alt-text="정규화된 요청 단위 소비 메트릭에 필터 적용":::

## <a name="next-steps"></a>다음 단계

* Azure에서 [진단 설정](cosmosdb-monitor-resource-logs.md)을 사용하여 Azure Cosmos DB 데이터를 모니터링합니다.
* [Azure Cosmos DB 컨트롤 플레인 작업 감사](audit-control-plane-logs.md)
