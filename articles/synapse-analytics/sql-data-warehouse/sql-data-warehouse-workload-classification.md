---
title: 전용 SQL 풀의 워크로드 분류
description: 분류를 사용하여 Azure Synapse Analytics에서 전용 SQL 풀의 쿼리 동시성, 중요도 및 계산 리소스를 관리하는 방법에 대한 지침입니다.
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
ms.openlocfilehash: 7cd3619aa60f1bd8ac13ff767857b44348989285
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98678426"
---
# <a name="workload-classification-for-dedicated-sql-pool-in-azure-synapse-analytics"></a>Azure Synapse Analytics의 전용 SQL 풀에 대한 워크로드 분류

이 문서에서는 Azure Synapse에서 전용 SQL 풀을 사용하여 들어오는 요청에 워크로드 그룹 및 중요도를 할당하는 워크로드 분류 프로세스를 설명합니다.

## <a name="classification"></a>분류

> [!Video https://www.youtube.com/embed/QcCRBAhoXpM]

워크로드 관리 분류를 사용하면 [리소스 클래스](resource-classes-for-workload-management.md#what-are-resource-classes) 및 [중요도](sql-data-warehouse-workload-importance.md) 할당을 통해 워크로드 정책을 요청에 적용할 수 있습니다.

데이터 웨어하우징 워크로드를 분류하는 방법은 여러 가지가 있지만 가장 간단하고 일반적인 분류는 로드 및 쿼리입니다. Insert, Update 및 Delete 문을 사용하여 데이터를 로드할 수 있습니다.  Select를 사용하여 데이터를 쿼리할 수 있습니다. 흔히 데이터 웨어하우징 솔루션에는 로드 작업에 대한 워크로드 정책이 있습니다. 예를 들어 더 많은 리소스를 가진 더 높은 리소스 클래스를 할당하는 정책이 있을 수 있습니다. 로드 작업에 비해 중요도를 낮게 하는 등의 다양한 워크로드 정책을 쿼리에 적용할 수 있습니다.

또한 로드 및 쿼리 워크로드를 하위 분류할 수도 있습니다. 하위 분류를 사용하면 워크로드를 더 효율적으로 제어할 수 있습니다. 예를 들어 쿼리 워크로드는 큐브 새로 고침, 대시보드 쿼리 또는 임시 쿼리로 구성될 수 있습니다. 다양한 리소스 클래스 또는 중요도 설정을 사용하여 각 쿼리 워크로드를 분류할 수 있습니다. 또한 로드는 하위 분류의 이점을 누릴 수 있습니다. 대규모 변환을 더 큰 리소스 클래스에 할당할 수 있습니다. 주요 판매 데이터가 날씨 데이터 또는 소셜 데이터 피드 이전의 로더가 되도록 더 높은 중요도를 사용할 수 있습니다.

일부 문은 리소스가 필요하지 않거나 실행에 영향을 주는 중요도가 필요하지 않기 때문에 분류되지 않습니다.  DBCC 명령, BEGIN, COMMIT 및 ROLLBACK TRANSACTION 문은 분류되지 않습니다.

## <a name="classification-process"></a>분류 프로세스

현재 전용 SQL 풀 분류는 [sp_addrolemember](/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)를 사용하여 할당된 해당 리소스 클래스가 있는 역할에 사용자를 할당하여 수행됩니다. 리소스 클래스에 로그인하는 것 이상의 요청에 대한 특성을 제공하는 기능은 이 기능으로 제한됩니다. 이제 [CREATE 워크로드 분류자](/sql/t-sql/statements/create-workload-classifier-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) 구문을 사용하여 더 다양한 분류 방법을 사용할 수 있습니다.  이 구문을 사용하여 전용 SQL 풀 사용자는 `workload_group` 매개 변수를 통해 요청에 할당된 시스템 리소스의 양과 중요도를 할당할 수 있습니다.

> [!NOTE]
> 분류는 요청 단위로 평가됩니다. 단일 세션의 여러 요청은 다르게 분류될 수 있습니다.

## <a name="classification-weighting"></a>분류 가중치

분류 프로세스의 일부로 할당된 작업 그룹을 결정하기 위해 가중치가 적용됩니다.  가중치는 다음과 같습니다.

|분류자 매개 변수 |무게   |
|---------------------|---------|
|MEMBERNAME:USER      |64       |
|MEMBERNAME:ROLE      |32       |
|WLM_LABEL            |16       |
|WLM_CONTEXT          |8        |
|START_TIME/END_TIME  |4        |

`membername` 매개 변수는 필수입니다.  하지만 지정된 membername이 데이터베이스 역할 대신 데이터베이스 사용자인 경우 사용자 가중치가 더 높아 분류자가 선택됩니다.

사용자가 여러 분류자에서 다른 리소스 클래스가 할당되거나 일치하는 여러 역할의 멤버인 경우, 사용자에게 가장 높은 리소스 클래스 할당이 제공됩니다.  이 동작은 기존 리소스 클래스 할당 동작과 일치합니다.

## <a name="system-classifiers"></a>시스템 분류자

워크로드 분류에는 시스템 워크로드 분류자가 있습니다. 시스템 분류자는 기존 리소스 클래스 역할 멤버 자격을 리소스 클래스 리소스 할당에 일반 중요도로 매핑합니다. 시스템 분류자는 드롭할 수 없습니다. 시스템 분류자를 보려면 아래 쿼리를 실행할 수 있습니다.

```sql
SELECT * FROM sys.workload_management_workload_classifiers where classifier_id <= 12
```

## <a name="mixing-resource-class-assignments-with-classifiers"></a>분류자를 사용하여 리소스 클래스 할당 혼합

사용자를 대신해 만든 시스템 분류자는 워크로드 분류로 쉽게 마이그레이션할 수 있는 쉬운 경로를 제공합니다. 분류 우선 순위와 함께 리소스 클래스 역할 매핑을 사용하면 중요도가 새로운 분류자 만들기를 시작할 때 잘못 분류될 수 있습니다.

다음 시나리오를 고려하세요.

- 기존 데이터 웨어하우스에는 largerc 리소스 클래스 역할에 할당된 데이터베이스 사용자 DBAUser가 있습니다. sp_addrolemember를 사용하여 리소스 클래스 할당을 수행했습니다.
- 이제 데이터 웨어하우스가 워크로드 관리로 업데이트됩니다.
- 새 분류 구문을 테스트하기 위해 데이터베이스 역할 DBARole(DBAUser가 멤버인)에는 mediumrc 및 높은 중요도에 매핑되는 분류자가 만들어집니다.
- DBAUser가 로그인하여 쿼리를 실행하면 쿼리가 largerc에 할당됩니다. 사용자가 역할 멤버 자격보다 우선적으로 적용됩니다.

잘못된 분류 문제 해결을 간소화하려면 워크로드 분류자를 만들 때 리소스 클래스 역할 매핑을 제거하는 것이 좋습니다.  아래 코드는 기존 리소스 클래스 역할 멤버 자격을 반환합니다.  해당하는 리소스 클래스에서 반환된 각각의 멤버 이름에 대해 [sp_droprolemember](/sql/relational-databases/system-stored-procedures/sp-droprolemember-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)를 실행합니다.

```sql
SELECT  r.name AS [Resource Class]
,       m.name AS membername
FROM    sys.database_role_members rm
JOIN    sys.database_principals AS r ON rm.role_principal_id = r.principal_id
JOIN    sys.database_principals AS m ON rm.member_principal_id = m.principal_id
WHERE   r.name IN ('mediumrc','largerc','xlargerc','staticrc10','staticrc20','staticrc30','staticrc40','staticrc50','staticrc60','staticrc70','staticrc80');

--for each row returned run
sp_droprolemember '[Resource Class]', membername
```

## <a name="next-steps"></a>다음 단계

- 분류자를 만드는 방법에 대한 자세한 내용은 [워크로드 분류자 만들기(Transact-SQL)](/sql/t-sql/statements/create-workload-classifier-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)를 참조하세요.  
- 워크로드 분류자를 만드는 방법에 대한 빠른 시작 [워크로드 분류자 만들기](quickstart-create-a-workload-classifier-tsql.md)를 참조하세요.
- [워크로드 중요도 구성](sql-data-warehouse-how-to-configure-workload-importance.md)에 대한 방법 문서 및 [워크로드 관리 모니터링 및 관리](sql-data-warehouse-how-to-manage-and-monitor-workload-importance.md) 방법을 참조하세요.
- 쿼리 및 할당된 중요도를 보려면 [sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)를 참조하세요.
