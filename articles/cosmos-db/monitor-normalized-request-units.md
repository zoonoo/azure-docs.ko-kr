---
title: Azure Cosmos 컨테이너 또는 계정에 대해 정규화 된 r u/초 모니터링
description: Azure Cosmos DB에서 작업의 정규화 된 요청 단위 사용을 모니터링 하는 방법에 대해 알아봅니다. Azure Cosmos DB 계정의 소유자는 더 많은 요청 단위를 사용 하는 작업을 파악할 수 있습니다.
ms.service: cosmos-db
ms.topic: conceptual
author: kanshiG
ms.author: govindk
ms.date: 05/10/2020
ms.openlocfilehash: 23001bdaab0732dbeb088ebadefa90a27e622b19
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/12/2020
ms.locfileid: "83118793"
---
# <a name="how-to-monitor-normalized-rus-for-an-azure-cosmos-container-or-an-account"></a>Azure Cosmos 컨테이너 또는 계정에 대해 정규화 된 r u/s를 모니터링 하는 방법

Azure Cosmos DB Azure Monitor는 계정을 모니터링 하 고 대시보드를 만드는 메트릭 보기를 제공 합니다. Azure Cosmos DB 메트릭은 기본적으로 수집 되며,이 기능을 사용 하면 명시적으로 모든 항목을 사용 하거나 구성할 필요가 없습니다.

정규화 된 r u **소비** 메트릭은 파티션 키 범위에서 복제본이 wrt 요청 단위 사용량에 얼마나 잘 포화 되었는지 확인 하는 데 사용 됩니다. Azure Cosmos DB은 모든 실제 파티션에 처리량을 균등 하 게 분산 합니다. 이 메트릭은 복제본 집합 내의 최대 처리량 사용률에 대 한 초당 보기를 제공 합니다. 이 메트릭을 사용 하 여 요청 단위 사용률의 높은 백분율이 표시 되는 경우 작업 요구 사항에 맞게 처리량을 늘려야 합니다.

## <a name="what-to-expect-and-do-when-normalized-rus-is-higher"></a>정규화 된 r u/s가 더 높은 경우에는 무엇을 예측 하 고 수행할 수 있습니다.

정규화 된 r u/초 소비가 100%에 도달 하면 클라이언트에서 비율 제한 오류를 수신 합니다. 클라이언트는 대기 시간을 준수 하 고 다시 시도 해야 합니다. 100% 사용률에 도달 하는 짧은 스파이크가 있는 경우 복제본의 처리량이 최대 성능 제한에 도달 했음을 의미 합니다. 예를 들어, 복제본에서 모든 o s/s를 사용 하는 저장 프로시저와 같은 단일 작업을 수행 하면 정규화 된 r u/초에 짧은 스파이크가 발생 합니다. 이러한 경우 요청 속도가 낮은 경우 즉시 속도가 제한 되는 오류는 발생 하지 않습니다. 이는 Azure Cosmos DB 요청에서 특정 요청에 대해 프로 비전 된 r u/초 보다 더 많은 요금을 청구 하 고 해당 기간 내의 다른 요청은 사용 빈도가 제한 되기 때문입니다.

Azure Monitor 메트릭은 **총 요청** 메트릭을 사용 하 여 상태 코드 별로 작업을 찾는 데 도움이 됩니다. 나중에 429 상태 코드로 이러한 요청을 필터링 하 고 **작업 유형별로**분할할 수 있습니다.

요금 제한 된 요청을 찾기 위해 진단 로그를 통해이 정보를 가져오는 것이 좋습니다.

100% 정규화 된 r u/초 사용량이 나 100%에 가까운 연속 사용량이 있는 경우 처리량을 늘리는 것이 좋습니다. Azure monitor 메트릭 및 Azure monitor 로그를 활용 하 여 더 많은 작업 및 피크 사용량을 확인할 수 있습니다.

**정규화** 된 사용 메트릭은 사용 측면에서 더 웜 된 파티션 키 범위를 확인 하는 데도 사용 됩니다. 따라서 파티션 키 범위에 대 한 처리량의 오차를 제공 합니다. 나중에 후속 작업을 수행 하 여 사용 측면에서 핫으로 제공 되는 논리 파티션 키에 대 한 정보를 가져올 수 있습니다 Azure Monitor 로그 **의 파티션 키** 에 대 한 정보를 확인할 수 있습니다.

## <a name="view-the-normalized-request-unit-consumption-metric"></a>정규화 된 요청 단위 소비 메트릭 보기

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.

2. 왼쪽 탐색 모음에서 **모니터** 를 선택 하 고 **메트릭**을 선택 합니다.

   ![Azure Monitor의 메트릭 창](./media/monitor-normalized-request-units/monitor-metrics-blade.png)

3. **메트릭** 창에서 리소스 > **선택** 하 > 필요한 **구독**및 **리소스 그룹**을 선택 합니다. **리소스 종류**에 대해 **Azure Cosmos DB 계정**을 선택 하 고, 기존 Azure Cosmos 계정 중 하나를 선택 하 고, **적용**을 선택 합니다.

   ![메트릭을 보려면 Azure Cosmos 계정을 선택 하세요.](./media/monitor-normalized-request-units/select-cosmos-db-account.png)

4. 다음으로 사용 가능한 메트릭 목록에서 메트릭을 선택할 수 있습니다. 요청 단위, 저장소, 대기 시간, 가용성, Cassandra 및 기타에 특정 한 메트릭을 선택할 수 있습니다. 이 목록에서 사용할 수 있는 모든 메트릭에 대해 자세히 알아보려면 [범주별 메트릭](monitor-cosmos-db-reference.md) 문서를 참조 하세요. 이 예에서는 **정규화** 된 기능 메트릭 및 **최대값** 을 집계 값으로 선택 하겠습니다.

   이러한 세부 정보 외에도 메트릭의 **시간 범위** 와 **시간 세분성** 을 선택할 수 있습니다. 최대는 지난 30 일 동안의 메트릭을 볼 수 있습니다.  필터를 적용 하면 필터를 기반으로 차트가 표시 됩니다.

   ![Azure Portal에서 메트릭 선택](./media/monitor-normalized-request-units/normalized-request-unit-usage-metric.png)

### <a name="filters-for-normalized-request-unit-consumption"></a>정규화 된 요청 단위 소비에 대 한 필터

특정 **CollectionName**, **DatabaseName**, **PartitionKeyRangeID**및 **Region**에 의해 표시 되는 메트릭 및 차트를 필터링 할 수도 있습니다. 메트릭을 필터링 하려면 **필터 추가** 를 선택 하 고 필요한 속성 (예: **CollectionName** 및 관심 있는 값)을 선택 합니다. 그러면 그래프에 선택한 기간 동안 컨테이너에 사용 된 정규화 된 사용 단위가 표시 됩니다.  

**분할 적용** 옵션을 사용 하 여 메트릭을 그룹화 할 수 있습니다.  

각 컨테이너에 대 한 정규화 된 요청 단위 소비 메트릭은 다음 이미지와 같이 표시 됩니다.

![정규화 된 요청 단위 소비 메트릭에 필터 적용](./media/monitor-normalized-request-units/normalized-request-unit-usage-filters.png)

## <a name="next-steps"></a>다음 단계

* Azure에서 [진단 설정을](cosmosdb-monitor-resource-logs.md) 사용 하 여 Azure Cosmos DB 데이터를 모니터링 합니다.
* [Azure Cosmos DB 제어 평면 작업 감사](audit-control-plane-logs.md)