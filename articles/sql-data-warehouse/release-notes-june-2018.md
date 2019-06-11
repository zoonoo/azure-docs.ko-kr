---
title: Azure SQL Data Warehouse 릴리스 정보 2018년 6월 | Microsoft Docs
description: Azure SQL Data Warehouse에 대한 릴리스 정보입니다.
services: sql-data-warehouse
author: anumjs
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: ''
ms.date: 07/23/2018
ms.author: anjangsh
ms.reviewer: jrasnick
ms.openlocfilehash: 95c59d3e5504058e27cdb4eda311c3917d6c834a
ms.sourcegitcommit: 67625c53d466c7b04993e995a0d5f87acf7da121
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/20/2019
ms.locfileid: "65912239"
---
# <a name="whats-new-in-azure-sql-data-warehouse-june-2018"></a>Azure SQL Data Warehouse의 새로운 기능 2018년 6월
Azure SQL Data Warehouse는 지속적으로 개선 사항을 수신합니다. 이 문서에서는 2018년 6월에 도입된 새로운 기능과 변경 사항에 대해 설명합니다. 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="user-defined-restore-points"></a>사용자 정의 복원 지점
SQL Data Warehouse는 8시간 간격으로 데이터 웨어하우스의 스냅샷을 자동으로 만들어 8시간 RPO(복구 지점 목표)를 보장합니다. 이 자동화 스냅샷은 데이터 웨어하우스 실행에 따른 관리 부담을 완화하지만, 비즈니스 필요에 따라 중대한 시간에 스냅샷을 생성할 필요가 있습니다. 예를 들어, 데이터 부하가 아주 높아지기 직전이나 데이터 웨어하우스에 새 스크립트를 배포하기 직전에 스냅샷을 생성하면 해당 작업 바로 직전의 복원 지점을 사용할 수 있습니다. 

SQL Data Warehouse에서 지 원하는 [사용자 정의 복원 지점](https://azure.microsoft.com/blog/quick-recovery-time-with-sql-data-warehouse-using-user-defined-restore-points/) 를 통해 합니다 [새로 만들기-AzSqlDatabaseRestorePoint](https://docs.microsoft.com/powershell/module/az.sql/new-azsqldatabaserestorepoint) cmdlet.

```powershell
New-AzSqlDatabaseRestorePoint
    -ResourceGroupName $ResourceGroupName
    -ServerName $ServerName
    -DatabaseName $DatabaseName
    -RestorePointLabel $RestorePointName
```

## <a name="column-level-security"></a>열 수준 보안
데이터 웨어하우스에서 데이터 액세스 및 보안을 관리하는 일은 고객 및 파트너와 신뢰를 구축하는 데 매우 중요합니다. SQL Data Warehouse는 데이터 웨어하우스를 다시 디자인할 필요 없이 사용자 액세스를 테이블의 특정 열로 제한하여 중요한 데이터를 볼 수 있는 권한을 조정하는 [CLS(열 수준 보안)을 지원](https://azure.microsoft.com/blog/column-level-security-is-now-supported-in-azure-sql-data-warehouse/)합니다.

CLS(열 수준 보안)에서는 표준 [GRANT](https://docs.microsoft.com/azure/sql-data-warehouse/column-level-security) T-SQL 문을 사용하여 사용자의 실행 컨텍스트 또는 해당 그룹 멤버 자격을 기준으로 테이블 열에 대한 액세스를 제어할 수 있습니다. 액세스 제한 논리는 다른 애플리케이션 계층의 데이터가 아닌 데이터베이스 계층에 있으므로 전반적인 보안 구현이 간소화됩니다.


```sql
CREATE USER Nurse WITHOUT LOGIN;   
GRANT SELECT ON Membership(MemberID, FirstName, LastName, Phone, Email) TO Nurse;   
EXECUTE AS USER = 'Nurse';
SELECT * FROM Membership;

Msg 230, Level 14, State 1, Line 12 
The SELECT permission was denied on the column 'SSN' of the object 'Membership', database 'CLS_TestDW', schema 'dbo'.
```

## <a name="objectschemaname"></a>OBJECT_SCHEMA_NAME
[OBJECT_SCHEMA_NAME()](https://docs.microsoft.com/sql/t-sql/functions/object-schema-name-transact-sql) 함수는 스키마 범위 개체에 대한 데이터베이스 스키마 이름을 반환합니다. 이 함수는 개체 스키마 유효성 검사를 수행할 때 ETL 도구에서 일반적인 함수입니다. 

```sql
SELECT
    OBJECT_SCHEMA_NAME([object_id]) [table_schema]
    , [name]                        [table_name]
FROM
    [sys].[tables];
```

**샘플 결과**
```
table_schema    | table_name
-----------------------------
dbo               customer
dbo               lineitem
dbo               nation
dbo               orders
```

## <a name="support-for-the-systimezoneinfo-view"></a>sys.time_zone_info 보기 지원
[sys.time_zone_info](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-time-zone-info-transact-sql) 보기는 Azure SQL Data Warehouse에서 지원되는 표준 시간대에 대한 정보를 반환합니다.

```sql
SELECT * FROM [sys].[time_zone_info];
```

**샘플 결과**
```
name                            | current_utc_offset | is_currently_dst
-------------------------------------------------------------------------
Pacific Standard Time (Mexico)    -07:00               1
US Mountain Standard Time         -07:00               0
Mountain Standard Time (Mexico)   -06:00               1
Central Standard Time             -05:00               1
```

## <a name="auto-stats-operations-appear-in-sysdmpdwexecrequests-behavior-change"></a>자동 통계 작업이 sys.dm_pdw_exec_requests에 나타남(동작 변경 내용)

[통계 자동 작성](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-tables-statistics#automatic-creation-of-statistics)이 도입되면서, Azure SQL Data Warehouse는 쿼리 실행을 최적화하기 위해 통계를 생성합니다. 2018년 6월 릴리스에는 [CREATE STATISTICS](https://docs.microsoft.com/sql/t-sql/statements/create-statistics-transact-sql) 작업이 실행될 때마다 [sys.dm_pdw_exec_requests](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql) 보기에 레코드를 추가하여 통계가 자동 생성되는 시점을 모니터링하는 기능이 추가되어 있습니다.

```sql
SELECT
    [start_time]
    , [end_time]
    , [command]
FROM
    [sys].[dm_pdw_exec_requests]
WHERE
    [command] LIKE 'CREATE STATISTICS _WA_Sys%';
```
**샘플 결과**
```
start_time                | end_time                | command
------------------------------------------------------------------------------------------------------------------------------
2018-06-06 19:06:26.173    2018-06-06 19:06:26.173    CREATE STATISTICS _WA_Sys_00000001_63D998CC ON dbo.LineItem(l_orderkey);
```

## <a name="next-steps"></a>다음 단계
SQL Data Warehouse에 대한 내용을 파악했으므로 [SQL Data Warehouse 만들기][create a SQL Data Warehouse]에 대해 신속히 알아봅니다. Azure를 처음 사용하는 경우 새 용어를 발견하면 [Azure 용어집][Azure glossary]을 유용하게 사용할 수 있습니다. 또는 그 밖의 SQL Data Warehouse 리소스를 살펴봅니다.  

* [고객 성공 사례]
* [블로그]
* [기능 요청]
* [비디오]
* [고객 자문 팀 블로그]
* [Stack Overflow 포럼]
* [Twitter]


[블로그]: https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/
[고객 자문 팀 블로그]: https://blogs.msdn.microsoft.com/sqlcat/tag/sql-dw/
[고객 성공 사례]: https://azure.microsoft.com/case-studies/?service=sql-data-warehouse
[기능 요청]: https://feedback.azure.com/forums/307516-sql-data-warehouse
[Stack Overflow 포럼]: https://stackoverflow.com/questions/tagged/azure-sqldw
[Twitter]: https://twitter.com/hashtag/SQLDW
[비디오]: https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse
[create a SQL Data Warehouse]: ./create-data-warehouse-portal.md
[Azure glossary]: ../azure-glossary-cloud-terminology.md
