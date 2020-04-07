---
title: 워크로드 분류
description: 분류를 사용하여 Azure Synapse Analytics에서 쿼리에 대한 동시성, 중요도 및 계산 리소스를 관리하는 방법에 대한 지침입니다.
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
ms.openlocfilehash: e7aa0c402878c994aabe4e12d811a99e300d7e67
ms.sourcegitcommit: bd5fee5c56f2cbe74aa8569a1a5bce12a3b3efa6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80743645"
---
# <a name="azure-synapse-analytics-workload-classification"></a>Azure 시냅스 분석 워크로드 분류

이 문서에서는 Azure Synapse의 Synapse SQL 풀을 사용하여 들어오는 요청에 워크로드 그룹과 중요도를 할당하는 워크로드 분류 프로세스에 대해 설명합니다.

## <a name="classification"></a>분류

> [!Video https://www.youtube.com/embed/QcCRBAhoXpM]

워크로드 관리 분류를 사용하면 [리소스 클래스](resource-classes-for-workload-management.md#what-are-resource-classes) 및 [중요도를](sql-data-warehouse-workload-importance.md)할당하여 워크로드 정책을 요청에 적용할 수 있습니다.

데이터 웨어하우징 워크로드를 분류하는 방법에는 여러 가지가 있지만 가장 간단하고 일반적인 분류는 로드 및 쿼리입니다. 삽입, 업데이트 및 삭제 문으로 데이터를 로드합니다.  selects를 사용하여 데이터를 쿼리합니다. 데이터 웨어하우징 솔루션에는 더 많은 리소스가 있는 상위 리소스 클래스를 할당하는 등 부하 활동에 대한 워크로드 정책이 있는 경우가 많습니다. 부하 활동에 비해 중요도가 낮아짐과 같이 쿼리에 다른 워크로드 정책이 적용될 수 있습니다.

부하 및 쿼리 워크로드를 하위 분류할 수도 있습니다. 하위 분류를 통해 워크로드를 보다 세한 제어할 수 있습니다. 예를 들어 쿼리 워크로드는 큐브 새로 고침, 대시보드 쿼리 또는 임시 쿼리로 구성될 수 있습니다. 이러한 각 쿼리 워크로드를 서로 다른 리소스 클래스 또는 중요도 설정으로 분류할 수 있습니다. 부하는 하위 분류의 이점을 얻을 수도 있습니다. 대규모 변환을 더 큰 리소스 클래스에 할당할 수 있습니다. 중요도가 높아짐에 따라 주요 판매 데이터가 날씨 데이터 나 소셜 데이터 피드 전에 로더가 되도록 할 수 있습니다.

모든 명령문이 리소스를 필요로 하지 않거나 실행에 영향을 미치는 데 중요한 것이 필요하지 않기 때문에 분류되는 것은 아닙니다.  DBCC 명령, BEGIN, 커밋 및 롤백 트랜잭션 문은 분류되지 않습니다.

## <a name="classification-process"></a>분류 프로세스

Azure Synapse의 Synapse SQL 풀에 대한 분류는 [sp_addrolemember](/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)사용하여 해당 리소스 클래스가 할당된 역할에 사용자를 할당하여 오늘 달성됩니다. 리소스 클래스에 로그인을 넘어 요청을 특성화하는 기능은 이 기능을 사용하여 제한됩니다. 이제 [CREATE 워크로드 분류자](/sql/t-sql/statements/create-workload-classifier-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) 구문에서 보다 풍부한 분류 방법을 사용할 수 있습니다.  이 구문을 통해 Synapse SQL 풀 사용자는 `workload_group` 매개 변수를 통해 요청에 할당되는 중요도 및 시스템 리소스의 양을 할당할 수 있습니다.

> [!NOTE]
> 분류는 요청별로 평가됩니다. 단일 세션의 여러 요청을 다르게 분류할 수 있습니다.

## <a name="classification-weighting"></a>분류 가중치

분류 프로세스의 일부로 할당된 워크로드 그룹을 결정하기 위해 가중치가 부여됩니다.  가중치는 다음과 같습니다.

|분류자 매개 변수 |무게   |
|---------------------|---------|
|멤버 이름:사용자      |64       |
|멤버 이름:역할      |32       |
|WLM_LABEL            |16       |
|WLM_CONTEXT          |8        |
|START_TIME/END_TIME  |4        |

`membername` 매개 변수는 필수입니다.  그러나 지정된 멤버 이름이 데이터베이스 역할 대신 데이터베이스 사용자인 경우 사용자에 대한 가중치가 높아지므로 분류자가 선택됩니다.

사용자가 여러 분류자에서 다른 리소스 클래스가 할당되거나 일치하는 여러 역할의 멤버인 경우, 사용자에게 가장 높은 리소스 클래스 할당이 제공됩니다.  이 동작은 기존 리소스 클래스 할당 동작과 일치합니다.

## <a name="system-classifiers"></a>시스템 분류기

워크로드 분류에는 시스템 워크로드 분류자가 있습니다. 시스템 분류자는 기존 리소스 클래스 역할 멤버 자격을 일반적인 중요도의 리소스 클래스 리소스 할당에 매핑합니다. 시스템 분류기를 삭제할 수 없습니다. 시스템 분류기를 보려면 아래 쿼리를 실행할 수 있습니다.

```sql
SELECT * FROM sys.workload_management_workload_classifiers where classifier_id <= 12
```

## <a name="mixing-resource-class-assignments-with-classifiers"></a>리소스 클래스 할당을 분류자와 혼합

사용자 대신 만든 시스템 분류자는 워크로드 분류로 마이그레이션하는 쉬운 경로를 제공합니다. 분류 우선 순위가 있는 리소스 클래스 역할 매핑을 사용하면 중요도가 있는 새 분류자를 만들기 시작할 때 분류가 잘못 될 수 있습니다.

다음과 같은 시나리오를 고려해 보세요.

- 기존 데이터 웨어하우스에는 데이터베이스 사용자 DBAUser가 더 큰 리소스 클래스 역할에 할당되어 있습니다. 리소스 클래스 할당은 sp_addrolemember 수행되었습니다.
- 이제 데이터 웨어하우스가 워크로드 관리로 업데이트됩니다.
- 새 분류 구문을 테스트하기 위해 DBARole(DBAUser의 구성원인 데이터베이스 역할)에는 중간 에 매핑하고 중요도가 높은 분류자를 사용할 수 있습니다.
- DBAUser로 로그인하여 쿼리를 실행하면 쿼리가 largec에 할당됩니다. 사용자가 역할 구성원보다 우선하기 때문입니다.

잘못된 분류 문제 해결을 간소화하려면 워크로드 분류자를 만들 때 리소스 클래스 역할 매핑을 제거하는 것이 좋습니다.  아래 코드는 기존 리소스 클래스 역할 멤버 자격을 반환합니다.  해당 리소스 클래스에서 반환된 각 멤버 이름에 대해 [sp_droprolemember](/sql/relational-databases/system-stored-procedures/sp-droprolemember-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) 실행합니다.

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

- 분류자 만들기에 대한 자세한 내용은 [워크로드 분류자 만들기(Transact-SQL)를](/sql/t-sql/statements/create-workload-classifier-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)참조하십시오.  
- 워크로드 분류기를 만드는 방법에 대한 빠른 시작을 참조하십시오 [워크로드 분류자 만들기](quickstart-create-a-workload-classifier-tsql.md).
- [워크로드 중요도를 구성하는](sql-data-warehouse-how-to-configure-workload-importance.md) 방법 문서와 [워크로드 관리를 관리 및 모니터링하는](sql-data-warehouse-how-to-manage-and-monitor-workload-importance.md)방법을 참조하세요.
- 쿼리 및 할당된 중요도를 보려면 [sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)를 참조하세요.
