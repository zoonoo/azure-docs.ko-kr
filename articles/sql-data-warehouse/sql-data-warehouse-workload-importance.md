---
title: 워크로드 중요도
description: Azure Synapse Analytics에서 SQL 분석 쿼리를 위한 중요도를 설정 하기 위한 지침을 제공 합니다.
services: sql-data-warehouse
author: ronortloff
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: workload-management
ms.date: 02/04/2020
ms.author: rortloff
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: de7bb28770bc356514c392c3478fd0e33658f878
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/29/2020
ms.locfileid: "78191775"
---
# <a name="azure-synapse-analytics-workload-importance"></a>Azure Synapse Analytics 워크 로드 중요도

이 문서에서는 워크 로드 중요도가 Azure Synapse의 SQL Analytics 요청 실행 순서에 영향을 주는 방법을 설명 합니다.

## <a name="importance"></a>중요도

> [!Video https://www.youtube.com/embed/_2rLMljOjw8]

비즈니스 요구 사항에는 데이터 웨어하우징 워크로드가 다른 메트릭보다 더 중요해질 수 있습니다.  회계 기간이 끝나기 전에 중요한 판매 데이터를 로드하는 경우를 고려해 보십시오.  날씨 데이터와 같은 다른 원본에 대한 데이터 로드에는 엄격한 SLA가 없습니다. 판매 데이터를 로드 하는 요청에 대해 높은 중요도를 설정 하 고 날씨 데이터를 로드 하는 요청에 중요도를 설정 하면 판매 데이터 로드에서 리소스에 처음으로 액세스 하 여 더 빨리 완료할 수 있습니다.

## <a name="importance-levels"></a>중요도 수준

다섯 가지 수준의 중요도가 있습니다: 낮음, 보통 이하, 보통, 보통 이상 및 높음.  중요도를 설정하지 않은 요청에는 기본 수준인 보통이 할당됩니다. 동일한 중요도 수준의 요청은 현재 존재하는 예약 동작이 동일합니다.

## <a name="importance-scenarios"></a>중요도 시나리오

위에서 설명한 판매와 날씨 데이터를 사용한 기본 중요도 시나리오를 넘어서, 워크로드 중요도가 데이터 처리 및 쿼리 요구를 충족시키는 데 도움이 되는 다른 시나리오가 있습니다.

### <a name="locking"></a>잠금

읽기 및 쓰기에 대한 잠금 액세스는 자연스런 경합의 한 영역입니다. [파티션 전환](/azure/sql-data-warehouse/sql-data-warehouse-tables-partition) 또는 [개체 이름 바꾸기](/sql/t-sql/statements/rename-transact-sql?view=azure-sqldw-latest) 와 같은 활동에는 상승 된 잠금이 필요 합니다.  워크 로드의 중요성이 없으면 Azure Synapse의 SQL Analytics는 처리량을 최적화 합니다. 처리량을 최적화하는 것은, 실행 및 대기 중인 요청이 동일한 잠금이 필요하고 리소스를 사용할 수 있을 때, 대기 요청은 먼저 요청 큐에 도착한 높은 잠금 요구 요청을 무시할 수 있다는 것을 의미합니다. 워크로드 중요도는 높은 잠금을 사용하는 요청에 적용됩니다. 높은 중요도를 가진 요청은 낮은 중요도를 가진 요청보다 먼저 실행됩니다.

다음 예를 살펴 보십시오.

- Q1은 실행 중이며 SalesFact에서 데이터를 조회합니다.
- Q2는 Q1이 완료될 때까지 대기 큐에 대기됩니다.  오전 9시에 전송되어 SalesFact에 새 데이터를 파티션 전환하려고 합니다.
- Q3는 오전 9:01분에 전송되어 SalesFact에서 데이터를 조회하려고 합니다.

Q2 및 Q3가 동일한 중요도를 가지고 있고 Q1이 계속 실행 중이라면, Q3가 실행을 시작합니다. Q2는 SalesFact에서 배타적 잠금으로 계속 기다립니다.  Q2가 Q3보다 중요도가 더 높다면, Q3는 실행을 시작하기 전에 Q2가 실행을 완료할 때까지 기다립니다.

### <a name="non-uniform-requests"></a>균일 하지 않은 요청

중요도가 쿼리 요구 사항을 충족시키는 데 도움이 되는 또 다른 시나리오는 다른 리소스 클래스가 있는 요청이 제출되는 경우입니다.  앞에서 설명한 것 처럼 동일한 중요도에 따라 Azure Synapse의 SQL Analytics는 처리량을 최적화 합니다. Smallrc 또는 mediumrc와 같은 혼합 크기 요청을 큐에 대기 하는 경우 SQL Analytics는 사용 가능한 리소스에 맞는 가장 이른 도착 요청을 선택 합니다. 워크로드 중요도가 적용되는 경우, 가장 높은 중요도 요청이 다음에 예약됩니다.
  
DW500c에 대 한 다음 예제를 살펴보십시오.

- Q1, Q2, Q3, 및 Q4가 smallrc 쿼리를 실행합니다.
- Q5가 오전 9시에 mediumrc 리소스 클래스를 사용하여 제출됩니다.
- Q6가 오전 9시 01분에 smallrc 리소스 클래스를 사용하여 제출됩니다.

Q5가 mediumrc이기 때문에 두 개의 동시성 슬롯이 필요합니다. Q5는 두 실행 중인 쿼리를 완료할 때까지 대기해야 합니다.  그러나 실행 중인 쿼리(Q1-Q4) 중 하나가 완료되면, 쿼리를 실행하는 리소스가 존재하기 때문에 Q6가 즉시 예약됩니다.  Q5가 Q6보다 중요도가 더 높으면, Q6는 실행을 시작하기 전에 Q5가 실행될 때까지 대기합니다.

## <a name="next-steps"></a>다음 단계

- 분류자를 만드는 방법에 대 한 자세한 내용은 [워크 로드 분류자 만들기 (transact-sql)](/sql/t-sql/statements/create-workload-classifier-transact-sql)를 참조 하세요.  
- 작업 분류에 대 한 자세한 내용은 [작업 분류](sql-data-warehouse-workload-classification.md)를 참조 하세요.  
- 작업 분류자를 만드는 방법에 대 한 빠른 시작 [작업 분류자 만들기](quickstart-create-a-workload-classifier-tsql.md) 를 참조 하세요. 
- [워크로드 중요도 구성](sql-data-warehouse-how-to-configure-workload-importance.md) 및 [워크로드 관리 모니터링 및 관리](sql-data-warehouse-how-to-manage-and-monitor-workload-importance.md) 방법에 대한 문서를 참조하세요.
- 쿼리 및 할당된 중요도를 보려면 [sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?view=azure-sqldw-latest)를 참조하세요.
