---
title: 워크로드 중요도
description: Azure Synapse 분석에서 SQL 분석 쿼리의 중요도를 설정하기 위한 지침입니다.
services: synapse-analytics
author: ronortloff
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 02/04/2020
ms.author: rortloff
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: 3dde2ad4af17313bcfce28964f8be1e831317a5a
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "80349953"
---
# <a name="azure-synapse-analytics-workload-importance"></a>Azure 시냅스 분석 워크로드 중요도

이 문서에서는 워크로드 중요도가 Azure Synapse의 SQL Analytics 요청에 대한 실행 순서에 미치는 영향을 설명합니다.

## <a name="importance"></a>중요도

> [!Video https://www.youtube.com/embed/_2rLMljOjw8]

비즈니스 요구 사항으로 인해 데이터 웨어하우징 워크로드가 다른 워크로드보다 더 중요해질 수 있습니다.  회계 기간이 끝나기 전에 미션 크리티컬 판매 데이터가 로드되는 시나리오를 생각해 보십시오.  날씨 데이터와 같은 다른 소스에 대한 데이터 로드에는 엄격한 SLA가 없습니다. 판매 데이터를 로드하기 위한 요청에 대한 중요도가 높고 날씨 데이터 로드 요청에 대한 중요도가 낮으면 판매 데이터 로드가 리소스에 먼저 액세스하고 더 빨리 완료됩니다.

## <a name="importance-levels"></a>중요도 수준

중요도의 다섯 가지 수준이 있습니다: 낮음, below_normal, 정상, above_normal, 및 높음.  중요도를 설정하지 않은 요청은 기본 정상 수준으로 할당됩니다. 중요도수준이 같은 요청은 현재 존재하는 동일한 일정 동작을 갖습니다.

## <a name="importance-scenarios"></a>중요도 시나리오

영업 및 날씨 데이터와 함께 위에서 설명한 기본 중요도 시나리오 외에도 워크로드 중요도가 데이터 처리 및 쿼리 요구 사항을 충족하는 데 도움이 되는 다른 시나리오가 있습니다.

### <a name="locking"></a>잠금

읽기 및 쓰기 활동에 대한 잠금에 대한 액세스는 자연스러운 경합의 한 영역입니다. [파티션 전환](/azure/sql-data-warehouse/sql-data-warehouse-tables-partition) 또는 [OBJECT 이름 바꾸기와](/sql/t-sql/statements/rename-transact-sql?view=azure-sqldw-latest) 같은 활동에는 상승된 잠금이 필요합니다.  워크로드가 중요하지 않으면 Azure Synapse의 SQL Analytics가 처리량을 최적화합니다. 처리량에 대한 최적화는 실행 및 큐에 대기 중인 요청이 동일한 잠금 요구 사항이 있고 리소스를 사용할 수 있는 경우 큐에 대기중인 요청이 이전에 요청 큐에 도착한 더 높은 잠금 요구가 있는 요청을 우회할 수 있음을 의미합니다. 잠금 요구가 높은 요청에 워크로드 중요도가 적용되면 중요도가 높은 요청은 요청 전에 실행되며 중요도가 낮습니다.

다음과 같은 예제를 참조하세요.

- Q1은 SalesFact에서 데이터를 적극적으로 실행하고 선택하고 있습니다.
- Q2는 Q1이 완료되기를 기다리는 대기 중입니다.  오전 9시에 제출되었으며 새 데이터를 SalesFact로 분할하려고 합니다.
- Q3는 오전 9:01에 제출되며 SalesFact에서 데이터를 선택하려고 합니다.

2분기와 3분기의 중요성이 같고 1분기가 계속 실행 중이면 3분기실행이 시작됩니다. Q2는 SalesFact에서 독점적인 잠금을 계속 기다릴 것입니다.  2분기의 중요성이 3분기보다 높으면 3분기가 종료될 때까지 기다렸다가 실행을 시작할 수 있습니다.

### <a name="non-uniform-requests"></a>균일하지 않은 요청

중요도가 쿼리 요구를 충족하는 데 도움이 될 수 있는 또 다른 시나리오는 다른 리소스 클래스의 요청이 제출되는 경우입니다.  앞에서 설명한 것처럼 Azure Synapse의 SQL Analytics는 처리량을 최적화합니다. 혼합 크기 요청(예: smallrc 또는 mediumrc)이 큐에 대기되는 경우 SQL Analytics는 사용 가능한 리소스에 맞는 가장 빠른 도착 요청을 선택합니다. 워크로드 중요도가 적용되면 중요도가 가장 높은 요청이 다음에 예약됩니다.
  
DW500c의 다음 예제를 고려하십시오.

- Q1, Q2, Q3 및 Q4는 작은 쿼리를 실행하고 있습니다.
- Q5는 오전 9시에 중간 rc 리소스 클래스와 함께 제출됩니다.
- Q6은 오전 9:01에 smallrc 리소스 클래스로 제출됩니다.

Q5는 mediumrc이기 때문에 두 개의 동시성 슬롯이 필요합니다. Q5는 실행 중인 쿼리 중 두 개가 완료될 때까지 기다려야 합니다.  그러나 실행 중인 쿼리(Q1-Q4) 중 하나가 완료되면 쿼리를 실행하기 위한 리소스가 존재하기 때문에 Q6가 즉시 예약됩니다.  Q5의 중요성이 Q6보다 높으면 Q6이 실행될 때까지 기다렸다가 실행을 시작할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- 분류자 만들기에 대한 자세한 내용은 [워크로드 분류자 만들기(Transact-SQL)를](/sql/t-sql/statements/create-workload-classifier-transact-sql)참조하십시오.  
- 워크로드 분류에 대한 자세한 내용은 [워크로드 분류](sql-data-warehouse-workload-classification.md)를 참조하십시오.  
- 워크로드 분류기를 만드는 방법에 대한 빠른 시작 [워크로드 분류자 만들기를](quickstart-create-a-workload-classifier-tsql.md) 참조하십시오. 
- [워크로드 중요도 구성](sql-data-warehouse-how-to-configure-workload-importance.md) 및 [워크로드 관리 모니터링 및 관리](sql-data-warehouse-how-to-manage-and-monitor-workload-importance.md) 방법에 대한 문서를 참조하세요.
- 쿼리 및 할당된 중요도를 보려면 [sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?view=azure-sqldw-latest)를 참조하세요.
