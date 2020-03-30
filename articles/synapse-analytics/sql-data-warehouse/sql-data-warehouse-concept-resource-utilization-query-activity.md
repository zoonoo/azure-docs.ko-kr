---
title: 관리 용이성 및 모니터링 - 쿼리 활동, 리소스 활용
description: Azure 시냅스 분석을 관리하고 모니터링하는 데 사용할 수 있는 기능을 알아봅니다. Azure Portal과 DMV(Dynamic Management Views)를 사용하여 데이터 웨어하우스의 쿼리 작업 및 리소스 사용률을 이해합니다.
services: synapse-analytics
author: kevinvngo
manager: craigg-msft
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 03/11/2020
ms.author: kevin
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: 9d2a9bb0fcaab38f897987a1922e9c95497821d4
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "80350681"
---
# <a name="monitoring-resource-utilization-and-query-activity-in-azure-synapse-analytics"></a>Azure Synapse 분석에서 리소스 사용률 및 쿼리 활동 모니터링
Azure Synapse Analytics는 Azure 포털 내에서 풍부한 모니터링 환경을 제공하여 데이터 웨어하우스 워크로드에 대한 정보를 제공합니다. 데이터 웨어하우스를 모니터링할 때는 구성 가능한 보존 기간, 경고, 권장 사항, 메트릭과 로그용 사용자 지정 가능한 차트 및 대시보드를 제공하는 도구인 Azure Portal을 사용하는 것이 좋습니다. 또한 포털을 사용하면 Azure Monitor(로그)와 같은 다른 Azure 모니터링 서비스와 로그 분석을 통합하여 데이터 웨어하우스뿐만 아니라 통합된 Azure 분석 플랫폼에 대한 전체적인 모니터링 환경을 제공할 수 있습니다. 모니터링 경험. 이 설명서에는 SQL Analytics를 사용하여 분석 플랫폼을 최적화하고 관리하는 데 사용할 수 있는 모니터링 기능에 대해 설명합니다. 

## <a name="resource-utilization"></a>리소스 사용률 
다음 메트릭은 SQL 분석에 대한 Azure 포털에서 사용할 수 있습니다. 이러한 메트릭은 [Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/data-collection#metrics)를 통해 나타납니다.


| 메트릭 이름             | 설명                                                  | 집계 형식 |
| ----------------------- | ------------------------------------------------------------ | ---------------- |
| CPU 비율          | 데이터 웨어하우스에 대한 모든 노드에서의 CPU 사용률      | 평균, 최소, 최대    |
| 데이터 IO 비율      | 데이터 웨어하우스에 대한 모든 노드에서의 IO 사용률       | 평균, 최소, 최대    |
| 메모리 백분율       | 데이터 웨어하우스의 모든 노드에서 메모리 사용률(SQL Server) | 평균, 최소, 최대   |
| 활성 쿼리          | 시스템에서 실행 중인 활성 쿼리 수             | 합계              |
| 큐에 대기된 쿼리          | 실행 시작을 기다리는 큐에 대기 된 쿼리 수          | 합계              |
| 성공적인 연결  | 데이터에 대한 연결 성공 횟수                 | 합계, 카운트       |
| 실패한 연결      | 데이터 웨어하우스에 대한 연결 실패 횟수           | 합계, 카운트       |
| 방화벽에 의해 차단     | 차단된 데이터 웨어하우스에 대한 로그인 횟수     | 합계, 카운트       |
| DWU 제한               | 데이터 웨어하우스의 서비스 수준 목표                | 평균, 최소, 최대    |
| DWU 백분율          | CPU 비율과 데이터 IO 비율 사이의 최댓값        | 평균, 최소, 최대    |
| DWU 사용됨                | DWU 한도 * DWU 비율                                   | 평균, 최소, 최대    |
| 캐시 적중 비율    | (캐시 적중/캐시 누락) * 100, 여기서 캐시 적중은 로컬 SSD 캐시에서 적중된 모든 columnstore 세그먼트에 대한 합계이며, 캐시 누락은 모든 노드에 걸쳐 로컬 SSD 캐시에서 누락된 columnstore 세그먼트에 대한 합계입니다. | 평균, 최소, 최대    |
| 캐시 사용 비율   | (사용된 캐시/캐시 용량) * 100, 여기서 사용된 캐시는 모든 노드에 걸친 로컬 SSD 캐시의 모든 바이트에 대한 합계이며, 캐시 용량은 모든 노드에 걸친 로컬 SSD 캐시의 스토리지 용량에 대한 합계입니다 | 평균, 최소, 최대    |
| 로컬 tempdb 백분율 | 모든 컴퓨팅 노드의 로컬 tempdb 사용률 - 5분마다 값을 내보냅니다. | 평균, 최소, 최대    |
| 데이터 스토리지 크기 | 데이터베이스에 로드된 데이터의 총 크기입니다. 여기에는 CCI에 있는 데이터와 비 CCI 테이블의 크기가 전체 데이터베이스 파일 크기로 측정되는 비 CCI 테이블이 포함됩니다. | 합계 |
| 재해 복구 크기 | 24시간마다 수행되는 지오 백업의 총 크기 | 합계 |
| 스냅샷 스토리지 크기 | 데이터베이스 복원 지점을 제공하기 위해 수행된 스냅숏의 총 크기입니다. 여기에는 자동화된 사용자 정의 스냅샷이 포함됩니다. | 합계 |

측정항목 보기 및 경고 설정 시 고려해야 할 사항:

- 사용되는 DWU는 SQL 풀 **전체에서 높은 수준의 사용 설명만** 나타내며 포괄적인 사용률 지표가 아닙니다. 확장 또는 축소 여부를 결정하려면 동시성, 메모리, tempdb 및 적응형 캐시 용량과 같이 DWU의 영향을 받을 수 있는 모든 요소를 고려하십시오. 다양한 [DWU 설정에서 워크로드를 실행하여](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-manage-compute-overview#finding-the-right-size-of-data-warehouse-units) 비즈니스 목표를 충족하는 데 가장 적합한 작업을 결정하는 것이 좋습니다.
- 실패하고 성공한 연결은 논리 서버가 아닌 특정 데이터 웨어하우스에 대해 보고됩니다.
- 메모리 백분율은 데이터 웨어하우스가 유휴 상태인 경우에도 사용률을 반영합니다. 이 메트릭을 다른 메트릭(tempdb, gen2 캐시)과 함께 사용하여 추가 캐시 용량을 확장하는 것이 요구 사항을 충족하기 위해 워크로드 성능을 향상시키는지 에 대한 전체적인 결정을 내릴 수 있습니다.


## <a name="query-activity"></a>쿼리 활동
T-SQL을 통해 SQL 분석을 모니터링할 때 프로그래밍 방식으로 환경을 제공하는 경우 이 서비스는 DMV(동적 관리 뷰) 집합을 제공합니다. 이러한 보기는 워크로드로 인한 성능 병목 상태를 적극적으로 해결하고 식별할 때 유용합니다.

SQL 애널리틱스에서 제공하는 DMV 목록을 보려면 이 [설명서를](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-reference-tsql-system-views#sql-data-warehouse-dynamic-management-views-dmvs)참조하십시오. 

## <a name="metrics-and-diagnostics-logging"></a>메트릭 및 진단 로깅
메트릭과 로그는 모두 Azure Monitor, 특히 [Azure Monitor 로그](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview) 구성 요소로 내보낼 수 있으며 [로그 쿼리를](https://docs.microsoft.com/azure/log-analytics/log-analytics-tutorial-viewdata)통해 프로그래밍 방식으로 액세스할 수 있습니다. SQL 분석의 로그 대기 시간은 약 10~15분입니다. 대기 시간에 영향을 미치는 요인에 대한 자세한 내용은 다음 설명서를 참조하십시오.


## <a name="next-steps"></a>다음 단계
다음 방법 가이드에서는 데이터 웨어하우스를 모니터링하고 관리할 때의 일반적인 시나리오와 사용 사례에 대해 설명합니다.

- [DMV를 사용하여 데이터 웨어하우스 워크로드 모니터링](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-manage-monitor)
