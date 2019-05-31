---
title: Azure SQL Data Warehouse 분류 | Microsoft Docs
description: 분류를 사용하여 동시성, 중요성, 관리 및 Azure SQL Data Warehouse의 쿼리에 대한 리소스를 계산하는 지침입니다.
services: sql-data-warehouse
author: ronortloff
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: workload management
ms.date: 05/01/2019
ms.author: rortloff
ms.reviewer: jrasnick
ms.openlocfilehash: 3edae23183896651efcbf7f867204a618a10c85d
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/27/2019
ms.locfileid: "66236893"
---
# <a name="azure-sql-data-warehouse-workload-classification"></a>Azure SQL Data Warehouse 작업 분류

이 문서는 들어오는 요청에 리소스 클래스와 중요도를 할당하는 SQL Data Warehouse 워크로드 분류 프로세스를 설명합니다.

## <a name="classification"></a>분류

> [!Video https://www.youtube.com/embed/QcCRBAhoXpM]

워크로드 관리 분류는 [리소스 클래스](resource-classes-for-workload-management.md#what-are-resource-classes) 및 [중요도](sql-data-warehouse-workload-importance.md) 할당을 통해 요청에 적용되는 워크로드 정책을 허용합니다.

데이터 웨어하우징 워크로드를 분류하는 방법은 많은 방법이 있지만, 가장 간단하고 일반적인 분류는 로드 및 쿼리입니다. Insert, update 및 delete 문을 사용하여 데이터를 로드합니다.  Select를 사용하여 데이터를 쿼리할 수 있습니다. 데이터 웨어하우징 솔루션은 더 높은 리소스 클래스에 더 많은 리소스를 할당하는 등의 로드 작업에 대한 워크로드 정책을 종종 가집니다. 로드 작업에 비해 낮은 중요도와 같은 쿼리에는 다른 워크로드 정책을 적용할 수 있습니다.

로드 및 쿼리 워크로드를 하위 분류할 수도 있습니다. 하위 분류로 워크로드를 더욱 더 제어할 수 있습니다. 예를 들어 큐브 새로 고침, 대시보드 쿼리나 임시 쿼리로 워크로드를 구성할 수 있습니다. 각각 다른 리소스 클래스 또는 중요도 설정으로 이러한 쿼리 워크로드를 분류할 수 있습니다. 하위 분류는 로드에도 도움이 됩니다. 더 큰 리소스 클래스에 큰 변환을 할당할 수 있습니다. 더 높은 중요도를 사용하여 날씨 데이터와 소셜 데이터 피드 전에 핵심 판매 데이터가 로더가 되도록 할 수 있습니다.

모든 문이 리소스가 필요하거나 실행에 영향을 미치는 중요성이 필요한 것은 아니므로, 모든 문이 분류되는 것은 아닙니다.  DBCC 명령, BEGIN, COMMIT 및 ROLLBACK TRANSACTION 문은 분류되지 않습니다.

## <a name="classification-process"></a>분류 프로세스

현재 SQL Data Warehouse의 분류는 [sp_addrolemember](/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql)를 사용하여 할당된 리소스 클래스가 해당 하는 역할에 사용자를 할당하여 이루어집니다. 리소스 클래스에 로그인을 넘어 요청을 특성화하는 기능은 이 기능으로 제한됩니다. 분류에 대한 다양한 메서드는 [CREATE WORKLOAD CLASSIFIER](/sql/t-sql/statements/create-workload-classifier-transact-sql) 구문으로 가능합니다.  이 구문을 사용하여 SQL Data Warehouse 사용자는 중요도 및 리소스 클래스를 요청에 할당할 수 있습니다.  

> [!NOTE]
> 분류는 요청 별로 평가됩니다. 단일 세션에서 여러 요청을 다른 방식으로 분류할 수 있습니다.

## <a name="classification-precedence"></a>분류 우선 순위

분류 프로세스의 일환으로, 리소스 클래스 할당을 결정하는 우선 순위가 주어집니다. 데이터베이스 사용자를 기반으로 하는 분류는 역할 멤버 자격에 우선합니다. 분류자를 만든 경우 UserA 데이터베이스 사용자를 mediumrc 리소스 클래스에 매핑합니다. 그런 다음 largerc 리소스 클래스에 RoleA 데이터베이스 역할(UserA가 멤버인)을 매핑하세요. 데이터베이스 사용자를 mediumrc 리소스 클래스에 매핑하는 분류자는 largerc 리소스 클래스에 RoleA 데이터베이스 역할을 매핑하는 분류자보다 우선 적용됩니다.

사용자가 여러 분류자에서 일치하거나 할당된 다양한 리소스 클래스를 사용하는 여러 역할의 멤버인 경우, 사용자에게는 가장 높은 리소스 클래스 할당이 주어집니다.  이 동작은 기존 리소스 클래스 할당 동작과 일치합니다.

## <a name="system-classifiers"></a>시스템 분류자

워크로드 분류는 시스템 워크 로드 분류자를 가집니다. 시스템 분류자는 보통 중요도를 사용하여 리소스 클래스 리소스 할당에 기존 리소스 클래스 역할 멤버 자격을 매핑합니다. 시스템 분류자는 삭제할 수 없습니다. 시스템 분류자를 보려면 아래 쿼리를 실행합니다.

```sql
SELECT * FROM sys.workload_management_workload_classifiers where classifier_id <= 12
```

## <a name="mixing-resource-class-assignments-with-classifiers"></a>분류자를 사용하여 리소스 클래스 할당 혼합

사용자를 대신해 생성된 시스템 분류자는 워크로드 분류로 마이그레이션하기 위한 편리한 경로를 제공합니다. 분류 우선 순위로 리소스 클래스 역할 매핑을 사용하면, 중요도를 사용하여 새 분류자를 만들기 사작할 때 잘못 분류될 수 있습니다.

다음과 같은 시나리오를 고려해 보세요.

- 기존 데이터 웨어하우스에 데이터베이스 사용자 DBAUser largerc 리소스 클래스 역할을 할당 합니다. 리소스 클래스 할당은 sp_addrolemember를 사용하여 수행되었습니다.
- 데이터 웨어하우스 워크 로드 관리를 사용 하 여 이제 업데이트 됩니다.
- 새 분류 구문을 테스트 하려면 데이터베이스 역할 (즉 DBAUser 소속) DBARole, 생성 되 게 mediumrc와 높은 중요도로 매핑하면 분류자를 있습니다.
- DBAUser 로그인 하 고 쿼리를 실행 하는 경우 쿼리는 largerc에 할당 됩니다. 사용자가 역할 멤버 자격보다 우선 순위가 높기 때문입니다.

오분류 문제를 간단히 해결하려면, 워크로드 분류자를 만들때 리소스 클래스 역할 매핑을 제거하는 것이 좋습니다.  아래 코드는 기존 리소스 클래스의 역할 멤버 자격을 반환합니다.  [sp_droprolemember](/sql/relational-databases/system-stored-procedures/sp-droprolemember-transact-sql)를 실행하여 각 멤버 이름을 해당 리소스 클래스에서 반환합니다.

```sql
SELECT  r.name AS [Resource Class]
,       m.name AS membername
FROM    sys.database_role_members rm
JOIN    sys.database_principals AS r ON rm.role_principal_id = r.principal_id
JOIN    sys.database_principals AS m ON rm.member_principal_id = m.principal_id
WHERE   r.name IN ('mediumrc','largerc','xlargerc','staticrc10','staticrc20','staticrc30','staticrc40','staticrc50','staticrc60','staticrc70','staticrc80');

--for each row returned run
sp_droprolemember ‘[Resource Class]’, membername
```

## <a name="next-steps"></a>다음 단계

- 분류자를 만드는 방법에 대 한 자세한 내용은 참조는 [워크 로드 분류자 만들기 (TRANSACT-SQL)](https://docs.microsoft.com/sql/t-sql/statements/create-workload-classifier-transact-sql)합니다.  
- 와 [워크로드 분류자 만들기](quickstart-create-a-workload-classifier-tsql.md)를 참조하세요.
- 방법 문서를 참조 하세요 [워크 로드 구성 중요도](sql-data-warehouse-how-to-configure-workload-importance.md) 하는 방법과 [관리 하 고 워크 로드 관리를 모니터링](sql-data-warehouse-how-to-manage-and-monitor-workload-importance.md).
- 쿼리 및 할당된 중요도를 보려면 [sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql)를 참조하세요.