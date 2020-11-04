---
title: 워크로드 분류
description: 분류를 사용 하 여 Azure Synapse Analytics에서 쿼리에 대 한 동시성, 중요도 및 계산 리소스를 관리 하는 방법에 대 한 지침입니다.
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
ms.openlocfilehash: 6b66b8a9fb3b5eb7dc78c00ba084e8609877dec7
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2020
ms.locfileid: "93323875"
---
# <a name="azure-synapse-analytics-workload-classification"></a>Azure Synapse Analytics 워크 로드 분류

이 문서에서는 Azure Synapse에서 전용 SQL 풀을 사용 하 여 작업 그룹 및 들어오는 요청에 중요도를 할당 하는 작업 분류 프로세스를 설명 합니다.

## <a name="classification"></a>분류

> [!Video https://www.youtube.com/embed/QcCRBAhoXpM]

작업 관리 분류를 사용 하면 [리소스 클래스](resource-classes-for-workload-management.md#what-are-resource-classes) 및 [중요도](sql-data-warehouse-workload-importance.md)할당을 통해 작업 정책을 요청에 적용할 수 있습니다.

데이터 웨어하우징 워크 로드를 분류 하는 방법에는 여러 가지가 있지만 가장 간단 하 고 가장 일반적인 분류는 로드 및 쿼리입니다. Insert, update 및 delete 문을 사용 하 여 데이터를 로드 합니다.  Select를 사용 하 여 데이터를 쿼리 합니다. 데이터 웨어하우징 솔루션에는 더 많은 리소스를 포함 하는 더 높은 리소스 클래스를 할당 하는 것과 같은 로드 작업에 대 한 작업 정책이 종종 있습니다. 다른 작업 정책은 로드 작업과 비교 하 여 낮은 중요도와 같은 쿼리에 적용할 수 있습니다.

부하 및 쿼리 작업을 하위 분류할 수도 있습니다. 하위 분류를 사용 하면 워크 로드를 더 효율적으로 제어할 수 있습니다. 예를 들어 쿼리 작업은 큐브 새로 고침, 대시보드 쿼리 또는 임시 쿼리로 구성 될 수 있습니다. 이러한 각 쿼리 작업을 서로 다른 리소스 클래스 또는 중요도 설정으로 분류할 수 있습니다. 부하는 하위 분류의 이점을 누릴 수도 있습니다. 큰 변환은 큰 리소스 클래스에 할당할 수 있습니다. 중요 한 데이터를 사용 하 여 중요 판매 데이터를 날씨 데이터 또는 소셜 데이터 피드 이전에 로더가 확인할 수 있습니다.

일부 문은 리소스가 필요 하지 않거나 실행에 영향을 주는 데 중요 한 것으로 분류 됩니다.  DBCC commands, BEGIN, COMMIT 및 ROLLBACK TRANSACTION 문은 분류 되지 않습니다.

## <a name="classification-process"></a>분류 프로세스

Azure Synapse에서 전용 SQL 풀의 분류는 현재 [sp_addrolemember](/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)를 사용 하 여 할당 된 해당 리소스 클래스가 있는 역할에 사용자를 할당 하 여 달성 됩니다. 리소스 클래스에 대 한 로그인 이외의 요청에 대 한 특성을 제공 하는 기능은이 기능으로 제한 됩니다. 이제 [CREATE 워크 로드 분류자](/sql/t-sql/statements/create-workload-classifier-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) 구문을 사용 하 여 더 다양 한 분류 방법을 사용할 수 있습니다.  이 구문을 사용 하 여 전용 SQL 풀 사용자는 매개 변수를 통해 요청에 할당 된 시스템 리소스의 양과 중요도를 할당할 수 있습니다 `workload_group` .

> [!NOTE]
> 분류는 요청 단위로 평가 됩니다. 단일 세션의 여러 요청은 다르게 분류 될 수 있습니다.

## <a name="classification-weighting"></a>분류 가중치

분류 프로세스의 일부로 할당 된 작업 그룹을 결정 하기 위해 가중치가 적용 됩니다.  가중치는 다음과 같습니다.

|분류자 매개 변수 |무게   |
|---------------------|---------|
|MEMBERNAME: 사용자      |64       |
|MEMBERNAME: ROLE      |32       |
|WLM_LABEL            |16       |
|WLM_CONTEXT          |8        |
|START_TIME/END_TIME  |4        |

`membername`매개 변수는 필수입니다.  그러나 지정 된 membername가 데이터베이스 역할 대신 데이터베이스 사용자 인 경우 사용자의 가중치가 높아 분류자가 선택 됩니다.

사용자가 여러 분류자에서 다른 리소스 클래스가 할당되거나 일치하는 여러 역할의 멤버인 경우, 사용자에게 가장 높은 리소스 클래스 할당이 제공됩니다.  이 동작은 기존 리소스 클래스 할당 동작과 일치 합니다.

## <a name="system-classifiers"></a>시스템 분류자

작업 분류에는 시스템 작업 분류자가 있습니다. 시스템 분류자는 기존 리소스 클래스 역할 멤버 자격을 리소스 클래스 리소스 할당에 일반적인 중요도로 매핑합니다. 시스템 분류자는 삭제할 수 없습니다. 시스템 분류자를 보려면 아래 쿼리를 실행할 수 있습니다.

```sql
SELECT * FROM sys.workload_management_workload_classifiers where classifier_id <= 12
```

## <a name="mixing-resource-class-assignments-with-classifiers"></a>분류자를 사용 하 여 리소스 클래스 할당 혼합

사용자를 대신해 서 만든 시스템 분류자는 워크 로드 분류로 쉽게 마이그레이션할 수 있는 쉬운 경로를 제공 합니다. 분류 우선 순위와 함께 리소스 클래스 역할 매핑을 사용 하면 중요도가 새로운 분류자 만들기를 시작할 때 잘못 분류 될 수 있습니다.

다음 시나리오를 고려하세요.

- 기존 데이터 웨어하우스에는 largerc 리소스 클래스 역할에 할당 된 데이터베이스 사용자 DBAUser가 있습니다. Sp_addrolemember를 사용 하 여 리소스 클래스 할당을 수행 했습니다.
- 이제 데이터 웨어하우스가 워크 로드 관리로 업데이트 됩니다.
- 새 분류 구문을 테스트 하기 위해 데이터베이스 역할 DBARole (DBAUser가 멤버인)에는 mediumrc 및 high 중요도에 매핑되는 분류자가 만들어집니다.
- DBAUser가 로그인 하 여 쿼리를 실행 하면 쿼리가 largerc에 할당 됩니다. 사용자가 역할 멤버 자격 보다 우선적으로 적용 됩니다.

잘못 된 분류 문제 해결을 간소화 하려면 작업 분류자를 만들 때 리소스 클래스 역할 매핑을 제거 하는 것이 좋습니다.  아래 코드는 기존 리소스 클래스 역할 멤버 자격을 반환 합니다.  해당 하는 리소스 클래스에서 반환 된 각 멤버 이름에 대해 [sp_droprolemember](/sql/relational-databases/system-stored-procedures/sp-droprolemember-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) 를 실행 합니다.

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

- 분류자를 만드는 방법에 대 한 자세한 내용은 [워크 로드 분류자 만들기 (transact-sql)](/sql/t-sql/statements/create-workload-classifier-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)를 참조 하세요.  
- 워크 로드 분류자를 만드는 방법에 대 한 빠른 시작을 참조 하 여 [워크 로드 분류자를 만듭니다](quickstart-create-a-workload-classifier-tsql.md).
- [작업 부하를 구성](sql-data-warehouse-how-to-configure-workload-importance.md) 하는 방법 문서 및 [워크 로드 관리를 관리 하 고 모니터링](sql-data-warehouse-how-to-manage-and-monitor-workload-importance.md)하는 방법 문서를 참조 하세요.
- 쿼리 및 할당된 중요도를 보려면 [sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)를 참조하세요.
