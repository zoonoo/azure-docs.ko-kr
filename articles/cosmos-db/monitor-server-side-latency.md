---
title: Azure Cosmos DB에서 작업에 대한 서버 측 대기 시간을 모니터링하는 방법
description: Azure Cosmos DB 계정 또는 컨테이너에서 작업에 대한 서버 대기 시간을 모니터링하는 방법에 대해 알아봅니다. Azure Cosmos DB 계정의 소유자는 Azure Cosmos 계정의 서버 측 대기 시간 문제를 이해할 수 있습니다.
ms.service: cosmos-db
ms.topic: conceptual
author: kanshiG
ms.author: govindk
ms.date: 04/07/2020
ms.openlocfilehash: 38f5778e363a16117bab482a758666ec81352afd
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/08/2020
ms.locfileid: "80887568"
---
# <a name="how-to-monitor-the-server-side-latency-for-operations-in-an-azure-cosmos-db-container-or-account"></a>Azure Cosmos DB 컨테이너 또는 계정에서 작업에 대한 서버 측 대기 시간을 모니터링하는 방법

Azure Cosmos DB용 Azure 모니터는 계정을 모니터링하고 대시보드를 만드는 메트릭 보기를 제공합니다. Azure Cosmos DB 메트릭은 기본적으로 수집되며, 이 기능은 명시적으로 아무것도 활성화하거나 구성할 필요가 없습니다. 서버 측 대기 시간 메트릭은 작업의 서버 측 대기 시간을 보는 데 사용됩니다. Azure Cosmos DB는 직접 연결을 통해 포인트 읽기/쓰기 작업에 대해 10ms 미만의 SLA를 제공합니다. 포인트 읽기 및 쓰기 작업의 경우 SLA는 [SLA 문서에](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_3/)자세히 설명되어 계산됩니다.

다음과 같은 포인트 작업에 대해 비정상적으로 큰 대기 시간이 있는 경우:

* 파티션 키와 ID를 직접 모드로 설정하거나 설정
* 읽기 또는 쓰기 작업 또는
* 쿼리

진단 로그를 조회하여 반환되는 데이터의 크기를 확인할 수 있습니다. 쿼리 작업에 대한 대기 시간이 계속 증가하는 경우 반환되는 데이터 크기, [처리량 또는 RU/s](cosmosdb-monitor-resource-logs.md#diagnostic-queries) 사용 또는 지정된 기간 동안 이러한 작업 수에 대한 진단 로그를 조회할 수 있습니다. 이렇게 하면 서버 측 대기 시간 문제를 디버깅할 수 있습니다.

## <a name="view-server-side-latency-metric"></a>서버 측 대기 시간 메트릭 보기

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.

1. 왼쪽 탐색 모음에서 **모니터를** 선택하고 **메트릭을 선택합니다.**

   ![Azure 모니터의 메트릭 창](./media/monitor-server-side-latency/monitor-metrics-blade.png)

1. **메트릭** 창 에서 > 필요한 **구독**및 리소스 그룹을 선택 하기 > **리소스** **를**선택 합니다. 리소스 **유형에**대해 **Azure Cosmos DB 계정을**선택하고 기존 Azure Cosmos 계정 중 하나를 선택하고 **적용을**선택합니다.
   
   ![코스모스 DB 계정을 선택하여 메트릭을 확인합니다.](./media/monitor-server-side-latency/select-cosmosdb-account.png)

1. 다음으로 사용 가능한 메트릭 목록에서 **서버 측 대기 시간** 메트릭을 선택합니다. 이 목록에서 사용 가능한 모든 측정항목에 대해 자세히 알아보려면 [카테고리별 측정항목을 참조하세요.](monitor-cosmos-db-reference.md) 이 예제에서는 **서버 측 대기 시간** 및 **평균을** 집계 값으로 선택해 보겠습니다. 이러한 세부 정보 외에도 메트릭의 **시간 범위** 및 **시간 세분성을** 선택할 수도 있습니다. 최대에서는 지난 30일 동안의 측정항목을 볼 수 있습니다.  필터를 적용하면 필터에 따라 차트가 표시됩니다. 선택한 기간 동안 분당 평균 요청 단위 수를 확인할 수 있습니다.  

   ![Azure 포털에서 서버 측 대기 시간 메트릭 선택](./media/monitor-server-side-latency/serverside-latency-metric.png)

## <a name="filters-for-server-side-latency"></a>서버 측 대기 시간 필터

또한 메트릭 및 특정 **CollectionName,** **ConnectionMode,** **DatabaseName,** **OperationType,** **지역**및 **PublicAPIType으로**표시되는 차트를 필터링할 수도 있습니다. 

메트릭을 필터링하려면 **필터 추가를** 선택하고 **PublicAPIType과** 같은 필수 속성을 선택하고 값 **sql을**선택합니다. **OperationType에**대한 다른 필터추가 . 그런 다음 그래프는 선택한 기간 동안 다른 작업에 대한 서버 측 대기 시간을 표시합니다. 저장 프로시저를 통해 실행된 작업은 기록되지 않으므로 OperationType 메트릭에서 사용할 수 없습니다.

각 작업에 대한 **서버 측 대기 시간** 메트릭은 다음 이미지와 같이 표시됩니다.

![서버 측 대기 시간 메트릭에 대한 필터](./media/monitor-server-side-latency/serverside-latency-filters.png)

**분할 적용** 옵션을 사용하여 메트릭을 그룹화할 수도 있습니다.  

## <a name="next-steps"></a>다음 단계

* Azure에서 [진단 설정을](cosmosdb-monitor-resource-logs.md) 사용하여 Azure Cosmos DB 데이터 모니터링
* [Azure 코스모스 DB 제어 평면 작업 감사](audit-control-plane-logs.md)