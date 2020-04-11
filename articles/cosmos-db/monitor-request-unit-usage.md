---
title: Azure Cosmos DB에서 작업의 처리량 사용량 모니터링
description: Azure Cosmos DB에서 작업의 처리량을 모니터링하거나 작업의 단위 사용을 요청하는 방법을 알아봅니다. Azure Cosmos DB 계정의 소유자는 요청 단위를 더 많이 차지하는 작업을 이해할 수 있습니다.
ms.service: cosmos-db
ms.topic: conceptual
author: kanshiG
ms.author: govindk
ms.date: 04/09/2020
ms.openlocfilehash: bc39ef199a5d40d3eaa75023277d3e00b93e131a
ms.sourcegitcommit: fb23286d4769442631079c7ed5da1ed14afdd5fc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/10/2020
ms.locfileid: "81115431"
---
# <a name="how-to-monitor-throughput-or-request-unit-usage-of-an-operation-in-azure-cosmos-db"></a>Azure Cosmos DB에서 처리량을 모니터링하거나 작업의 단위 사용을 요청하는 방법

Azure Cosmos DB용 Azure 모니터는 계정을 모니터링하고 대시보드를 만드는 메트릭 보기를 제공합니다. Azure Cosmos DB 메트릭은 기본적으로 수집되며, 이 기능은 명시적으로 아무것도 활성화하거나 구성할 필요가 없습니다. **총 요청 단위** 메트릭은 다양한 작업 유형에 대한 요청 단위 사용을 얻는 데 사용됩니다. 나중에 처리량의 대부분을 사용하는 작업을 분석할 수 있습니다. 기본적으로 처리량 데이터는 1분 간격으로 집계됩니다. 그러나 시간 세분성 옵션을 변경하여 집계 단위를 변경할 수 있습니다.

요청 단위 사용 데이터를 분석하는 방법에는 두 가지가 있습니다.

* 지정된 시간 간격 내에서 더 많은 요청 단위를 취하는 작업입니다.
* 일반적으로 더 많은 요청 단위를 사용 하 여 워크로드를 지배 하는 작업입니다.
이 분석을 통해 삽입, upsert 와 같은 작업에 집중하고 인덱싱을 살펴볼 수 있습니다. 특정 필드를 오버/언더인덱싱하고 있는지 확인하여 [인덱싱 정책을](index-policy.md#include-exclude-paths) 수정하여 경로를 포함하거나 제외할 수 있습니다.

특정 쿼리가 더 많은 요청 단위를 취하고 있는 경우 다음과 같은 작업을 수행할 수 있습니다.

* 적절한 양의 데이터를 요청하는 경우 다시 고려하십시오.
* 필터 절이 있는 인덱스를 사용하도록 쿼리를 수정합니다.
* 저렴한 UDF 함수 호출을 수행합니다.
* 파티션 키를 정의하여 쿼리에서 다른 파티션으로 팬을 최소화합니다.
* 호출 응답, 진단 로그 세부 정보에서 반환된 쿼리 메트릭을 사용하고 [쿼리 성능 튜닝](sql-api-query-metrics.md) 문서를 참조하여 쿼리 실행에 대해 자세히 알아볼 수도 있습니다.
* 합계에서 시작한 다음 올바른 차원을 사용하여 평균 사용률을 확인할 수 있습니다.

## <a name="view-the-total-request-unit-usage-metric"></a>총 요청 단위 사용 메트릭 보기

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.

1. 왼쪽 탐색 모음에서 **모니터를** 선택하고 **메트릭을 선택합니다.**

   ![Azure 모니터의 메트릭 창](./media/monitor-request-unit-usage/monitor-metrics-blade.png)

1. **메트릭** 창 에서 > 필요한 **구독**및 리소스 그룹을 선택 하기 > **리소스** **를**선택 합니다. 리소스 **유형에**대해 **Azure Cosmos DB 계정을**선택하고 기존 Azure Cosmos 계정 중 하나를 선택하고 **적용을**선택합니다.

   ![Azure 코스모스 DB 계정을 선택하여 메트릭을 봅니다.](./media/monitor-request-unit-usage/select-cosmos-db-account.png)

1. 다음으로 사용 가능한 메트릭 목록에서 **총 요청 단위** 메트릭을 선택합니다. 이 목록에서 사용 가능한 모든 측정항목에 대해 자세히 알아보려면 [카테고리별 측정항목을 참조하세요.](monitor-cosmos-db-reference.md) 이 예제에서는 **총 요청 단위** 및 **평균을** 집계 값으로 선택해 보겠습니다. 이러한 세부 정보 외에도 메트릭의 **시간 범위** 및 **시간 세분성을** 선택할 수도 있습니다. 최대에서는 지난 30일 동안의 측정항목을 볼 수 있습니다.  필터를 적용하면 필터에 따라 차트가 표시됩니다. 선택한 기간 동안 분당 평균 요청 단위 수를 확인할 수 있습니다.  

   ![Azure 포털에서 메트릭 선택](./media/monitor-request-unit-usage/request-unit-usage-metric.png)

## <a name="filters-for-request-unit-usage"></a>요청 단위 사용에 대한 필터

또한 메트릭을 필터링하고 특정 **CollectionName,** **DatabaseName,** **OperationType,** **지역,** **상태**및 **상태 코드로**표시되는 차트를 얻을 수 있습니다. **필터 추가** 및 **분할 적용** 옵션을 사용하면 요청 단위 사용량을 필터링하고 메트릭을 그룹화할 수 있습니다.

각 작업의 요청 단위 사용량을 합계(sum) 또는 평균으로 얻으려면 **분할 적용을** 선택하고 다음 이미지와 같이 **작업 유형** 및 필터 값을 선택합니다.

   ![코스모스 DB Azure 모니터의 작업에 대한 요청 단위](./media/monitor-request-unit-usage/request-unit-usage-operations.png)

컬렉션별 요청 단위 사용량을 보려면 **분할 적용을** 선택하고 컬렉션 이름을 필터로 선택합니다. 대시보드 내에서 컬렉션을 선택할 때 다음과 같은 채팅이 표시됩니다. 그런 다음 특정 컬렉션 이름을 선택하여 자세한 내용을 볼 수 있습니다.

   ![Cosmos DB Azure 모니터의 컬렉션에 의한 모든 작업에 대한 요청 단위](./media/monitor-request-unit-usage/request-unit-usage-collection.png)

## <a name="next-steps"></a>다음 단계

* Azure에서 [진단 설정을](cosmosdb-monitor-resource-logs.md) 사용하여 Azure Cosmos DB 데이터를 모니터링합니다.
* [Azure 코스모스 DB 제어 평면 작업 감사](audit-control-plane-logs.md)