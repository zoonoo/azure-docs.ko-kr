---
title: 워크로드 중요도
description: Azure SQL Data Warehouse 쿼리의 중요도를 설정 하는 방법에 대 한 지침입니다.
services: sql-data-warehouse
author: ronortloff
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: workload-management
ms.date: 05/01/2019
ms.author: rortloff
ms.reviewer: jrasnick
ms.custom: seo-lt-2019
ms.openlocfilehash: fea35325f11878373db8dd52b9b2bf08a25b81d1
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73692375"
---
# <a name="azure-sql-data-warehouse-workload-importance"></a>Azure SQL Data Warehouse 워크 로드 중요도

이 문서에서는 워크 로드 중요도가 SQL Data Warehouse 요청에 대 한 실행 순서에 영향을 줄 수 있는 방법을 설명 합니다.

## <a name="importance"></a>중요도

> [!Video https://www.youtube.com/embed/_2rLMljOjw8]

비즈니스 요구 사항에 따라 데이터 웨어하우징 작업이 다른 작업 보다 더 중요 해야 할 수 있습니다.  회계 기간이 종료 되기 전에 중요 한 판매 데이터가 로드 되는 시나리오를 고려해 보세요.  날씨 데이터와 같은 다른 원본에 대 한 데이터 로드에는 엄격한 Sla가 없습니다.   판매 데이터를 로드 하는 요청에 대해 높은 중요도를 설정 하는 것은 데이터의 판매 데이터 로드를 통해 리소스에 대 한 첫 번째 액세스를 보장 하 고 더 빨리 완료할 수 있는지 여부를 로드 하는 요청

## <a name="importance-levels"></a>중요도 수준

중요도는 low, below_normal, normal, above_normal 및 high의 5 가지입니다.  중요도를 설정 하지 않은 요청에는 normal의 기본 수준이 할당 됩니다.  중요도 수준이 같은 요청은 현재 존재 하는 일정 동작이 동일 합니다.

## <a name="importance-scenarios"></a>중요도 시나리오

판매 및 날씨 데이터와 관련 하 여 위에서 설명한 기본 중요도 시나리오 외에도 워크 로드 중요도가 데이터 처리 및 쿼리 요구를 충족 하는 데 도움이 되는 다른 시나리오가 있습니다.

### <a name="locking"></a>잠금

읽기 및 쓰기 작업의 잠금에 대 한 액세스는 자연 경합의 한 영역입니다.  [파티션 전환](/azure/sql-data-warehouse/sql-data-warehouse-tables-partition) 또는 [개체 이름 바꾸기](/sql/t-sql/statements/rename-transact-sql) 와 같은 활동에는 상승 된 잠금이 필요 합니다.  워크 로드의 중요성이 없는 SQL Data Warehouse 처리량을 최적화 합니다.  처리량을 최적화 하는 것은 실행 중이 고 대기 중인 요청이 동일한 잠금 요구와 리소스를 사용할 수 있는 경우 지연 된 요청은 이전에 요청 큐에 도착 한 더 높은 잠금 요구가 있는 요청을 무시할 수 있음을 의미 합니다.  잠금 요구 사항이 높은 요청에 워크 로드 중요도가 적용 되 면 중요도가 더 높은 요청은 중요도가 낮은 요청 보다 먼저 실행 됩니다.

다음 예제를 살펴보세요.

Q1이 적극적으로 실행 중이 고 SalesFact에서 데이터를 선택 합니다.
Q2가 Q1이 완료 될 때까지 대기 하는 중입니다.  오전 9 시에서 전송 되었으며 새 데이터를 SalesFact로 분할 하려고 합니다.
Q3은 오전 9 시 오전 9 시에 제출 되며 SalesFact에서 데이터를 선택 하려고 합니다.

Q2와 Q3의 중요도가 같고 Q1이 여전히 실행 중이면 Q3은 실행을 시작 합니다. Q2는 SalesFact의 배타적 잠금을 계속 대기 합니다.  Q2의 중요성이 Q3 보다 높은 경우 Q3은 실행을 시작 하기 전에 Q2가 완료 될 때까지 기다립니다.

### <a name="non-uniform-requests"></a>균일 하지 않은 요청

쿼리가 요청을 충족 하는 데 도움이 되는 또 다른 시나리오는 다른 리소스 클래스를 사용 하는 요청이 제출 되는 경우입니다.  앞에서 설명한 것 처럼 동일한 중요도에 따라 SQL Data Warehouse 처리량을 최적화 합니다.  Smallrc 또는 mediumrc와 같은 혼합 크기 요청을 큐에 대기 SQL Data Warehouse 하는 경우 사용 가능한 리소스에 맞는 가장 이른 도착 요청을 선택 합니다.  작업 중요도가 적용 되 면 가장 높은 중요도 요청이 다음에 예약 됩니다.
  
DW500c에 대 한 다음 예제를 살펴보십시오.

Q1, Q2, Q3 및 Q4는 smallrc 쿼리를 실행 합니다.
Q5는 오전 9 시에서 mediumrc 리소스 클래스를 사용 하 여 제출 됩니다.
Q6는 smallrc 리소스 클래스를 사용 하 여 오전 9 시에 전송 됩니다.

Q5는 mediumrc 이므로 두 개의 동시성 슬롯이 필요 합니다.  Q5는 실행 중인 두 쿼리가 완료 될 때까지 대기 해야 합니다.  그러나 실행 중인 쿼리 (Q1-Q4) 중 하나가 완료 되 면 리소스는 쿼리를 실행 하기 때문에 Q6이 즉시 예약 됩니다.  Q5가 Q6 보다 중요도가 높은 경우 Q6는 실행을 시작할 수 있을 때까지 Q5가 실행 될 때까지 대기 합니다.

## <a name="next-steps"></a>다음 단계

- 분류자를 만드는 방법에 대 한 자세한 내용은 [워크 로드 분류자 만들기 (transact-sql)](https://docs.microsoft.com/sql/t-sql/statements/create-workload-classifier-transact-sql)를 참조 하세요.  
- 작업 분류 SQL Data Warehouse에 대 한 자세한 내용은 [작업 분류](sql-data-warehouse-workload-classification.md)를 참조 하세요.  
- 작업 분류자를 만드는 방법에 대 한 빠른 시작 [작업 분류자 만들기](quickstart-create-a-workload-classifier-tsql.md) 를 참조 하세요.
- [워크로드 중요도 구성](sql-data-warehouse-how-to-configure-workload-importance.md) 및 [워크로드 관리 모니터링 및 관리](sql-data-warehouse-how-to-manage-and-monitor-workload-importance.md) 방법에 대한 문서를 참조하세요.
- 쿼리 및 할당된 중요도를 보려면 [sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql)를 참조하세요.
