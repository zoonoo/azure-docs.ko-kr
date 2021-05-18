---
title: 워크로드 중요도
description: Azure Synapse Analytics에서 전용 SQL 풀 쿼리의 중요도를 설정하는 방법에 대한 지침입니다.
services: synapse-analytics
author: ronortloff
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 02/04/2020
ms.author: rortloff
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: 06d1957d182f2cabc336afcfc47a790442a3cb9a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98678409"
---
# <a name="azure-synapse-analytics-workload-importance"></a>Azure Synapse Analytics 워크로드 중요도

이 문서에서는 워크로드 중요도가 Azure Synapse의 전용 SQL 풀 요청의 실행 순서에 영향을 주는 방법을 설명합니다.

## <a name="importance"></a>중요도

> [!Video https://www.youtube.com/embed/_2rLMljOjw8]

비즈니스 요구 사항에 따라 데이터 웨어하우징 작업이 다른 작업 보다 더 중요할 수 있습니다.  회계 기간이 종료되기 전에 중요한 판매 데이터가 로드되는 시나리오를 고려해 보세요.  날씨 데이터와 같은 다른 원본에 대한 데이터 로드에는 엄격한 SLA가 없습니다. 판매 데이터를 로드하는 요청에 대해 높은 중요도를 설정하고, 날씨 데이터를 로드하는 요청에 낮은 중요도를 설정하면, 판매 데이터 로드에서 리소스에 먼저 액세스하여 더 빨리 로드를 완료할 수 있습니다.

## <a name="importance-levels"></a>중요도 수준

중요도에는 low, below_normal, normal, above_normal 및 high의 다섯 가지 수준이 있습니다.  중요도를 설정하지 않은 요청에는 기본 수준인 normal이 할당됩니다. 중요도 수준이 동일한 요청은 현재 존재하는 동일한 일정 동작을 갖습니다.

## <a name="importance-scenarios"></a>중요도 시나리오

판매 및 날씨 데이터와 관련하여 위에서 설명한 기본 중요도 시나리오 외에도 워크로드 중요도가 데이터 처리 및 쿼리 요구를 충족하는 데 도움이 되는 다른 시나리오가 있습니다.

### <a name="locking"></a>잠금

읽기 및 쓰기 작업의 잠금에 대한 액세스는 자연스런 경합의 한 영역입니다. [파티션 전환](sql-data-warehouse-tables-partition.md) 또는 [개체 이름 바꾸기](/sql/t-sql/statements/rename-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)와 같은 활동에는 관리자 권한 잠금이 필요합니다.  워크로드 중요성이 없는 경우, Azure Synapse의 전용 SQL 풀은 처리량을 최적화합니다. 처리량을 최적화하는 것은 실행 중이고, 대기 중인 요청이 동일한 잠금 요구를 가지며, 리소스를 사용할 수 있는 경우, 대기 중인 요청은 이전에 요청 큐에 도착한 더 높은 잠금 요구가 있는 요청을 무시할 수 있음을 의미합니다. 잠금 요구 사항이 높은 요청에 워크로드 중요도가 적용되면, 중요도가 높은 요청은 중요도가 낮은 쿼리보다 먼저 실행됩니다.

다음 예제를 참조하세요.

- Q1이 실제 실행 중이고, SalesFact에서 데이터를 선택합니다.
- Q2가 Q1이 완료될 때까지 대기하는 중입니다.  오전 9시에서 전송되었으며, 새 데이터를 SalesFact로 파티션 전환하려고 합니다.
- Q3은 오전 9:01에 제출되며, SalesFact에서 데이터를 선택 하려고 합니다.

Q2와 Q3의 중요도가 같고, Q1이 여전히 실행 중이면 Q3은 실행을 시작합니다. Q2는 SalesFact의 배타적 잠금을 계속 대기합니다.  Q2의 중요도가 Q3보다 높은 경우, Q3은 실행을 시작하기 전에 Q2가 완료될 때까지 기다립니다.

### <a name="non-uniform-requests"></a>균일하지 않은 요청

중요도가 쿼리 요구를 충족하는 데 도움이 되는 또 다른 시나리오는 다른 리소스 클래스를 사용하는 요청이 제출되는 경우입니다.  앞에서 설명한 것처럼, 동일 중요도에 따라 Azure Synapse의 전용 SQL 풀은 처리량을 최적화합니다. Smallrc 또는 mediumrc와 같은 혼합 크기 요청이 대기 중일 때, 전용 SQL 풀은 사용 가능한 리소스 내에서 적합한 가장 이른 도착 요청을 선택합니다. 워크로드 중요도가 적용되면, 가장 높은 중요도 요청이 다음에 예약됩니다.
  
DW500c에 대한 다음 예제를 잘 살펴보세요.

- Q1, Q2, Q3 및 Q4는 smallrc 쿼리를 실행합니다.
- Q5는 오전 9시에 mediumrc 리소스 클래스를 사용하여 제출됩니다.
- Q6는 오전 9:01에 smallrc 리소스 클래스를 사용하여 제출됩니다.

Q5는 mediumrc이므로 두 개의 동시성 슬롯이 필요합니다. Q5는 실행 중인 두 개의 쿼리가 완료될 때까지 대기해야 합니다.  그러나 실행 중인 쿼리(Q1~Q4) 중 하나가 완료되면, 리소스는 쿼리를 실행하려고 하기 때문에 Q6이 즉시 예약됩니다.  Q5가 Q6보다 중요도가 높은 경우, Q6는 Q5가 실행될 때까지 대기한 후 실행을 시작합니다.

## <a name="next-steps"></a>다음 단계

- 분류자를 만드는 방법에 대한 자세한 내용은 [워크로드 분류자 만들기(Transact-SQL)](/sql/t-sql/statements/create-workload-classifier-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)를 참조하세요.  
- 워크로드 분류에 대한 자세한 내용은 [워크로드 분류](sql-data-warehouse-workload-classification.md)를 참조하세요.  
- 워크로드 분류자를 만드는 방법에 대한 빠른 시작 [워크로드 분류자 만들기](quickstart-create-a-workload-classifier-tsql.md)를 참조하세요.
- [워크로드 중요도 구성](sql-data-warehouse-how-to-configure-workload-importance.md) 및 [워크로드 관리 모니터링 및 관리](sql-data-warehouse-how-to-manage-and-monitor-workload-importance.md) 방법에 대한 문서를 참조하세요.
- 쿼리 및 할당된 중요도를 보려면 [sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)를 참조하세요.
