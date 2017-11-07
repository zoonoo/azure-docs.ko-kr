---
title: "워크로드 관리를 위한 리소스 클래스 - Azure SQL Data Warehouse | Microsoft Docs"
description: "Azure SQL Data Warehouse의 쿼리에 대한 계산 리소스 및 동시성 리소스를 관리하는 리소스 클래스 사용 지침입니다."
services: sql-data-warehouse
documentationcenter: NA
author: sqlmojo
manager: jhubbard
editor: 
ms.assetid: ef170f39-ae24-4b04-af76-53bb4c4d16d3
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: performance
ms.date: 10/23/2017
ms.author: joeyong;barbkess;kavithaj
ms.openlocfilehash: 122646f73b6e4e7c62eb0e6d4b6672b603d8acb2
ms.sourcegitcommit: b979d446ccbe0224109f71b3948d6235eb04a967
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/25/2017
---
# <a name="resource-classes-for-workload-management"></a>워크로드 관리를 위한 리소스 클래스
Azure SQL Data Warehouse의 쿼리에 대한 계산 리소스 및 동시에 실행되는 동시 쿼리 수를 관리하는 리소스 클래스 사용 지침입니다.
 
## <a name="what-is-workload-management"></a>워크로드 관리란?
워크로드 관리는 모든 쿼리의 전체 성능을 최적화하는 기능입니다. 잘 튜닝된 워크로드는 계산 집약적인지 또는 IO 사용량이 많은지에 관계없이 효율적으로 쿼리를 실행하고 작업을 로드합니다. 

SQL Data Warehouse는 다중 사용자 환경에 대한 워크로드 관리 기능을 제공합니다. 데이터 웨어하우스는 다중 테넌트 워크로드용은 아닙니다.

## <a name="what-are-resource-classes"></a>리소스 클래스란?
리소스 클래스는 쿼리 실행을 제어하는 미리 지정된 리소스 제한입니다. SQL Data Warehouse는 리소스 클래스에 따라 각 쿼리에 대한 계산 리소스를 제한합니다. 

리소스 클래스는 데이터 웨어하우스 워크로드의 전반적인 성능을 관리하는 데 도움이 됩니다. 리소스 클래스를 효율적으로 사용하여 동시에 실행되는 쿼리 수 및 각 쿼리에 할당되는 계산 리소스 수에 제한을 설정하여 워크로드를 관리할 수 있습니다. 

- 리소스 클래스가 작을수록 계산 리소스를 덜 사용하지만 전체 쿼리 동시성은 높아집니다.
- 리소스 클래스가 클수록 더 많은 계산 리소스를 제공하지만 쿼리 동시성은 제한됩니다.

리소스 클래스는 데이터 관리 및 조작 작업을 위해 설계되었습니다. 또한 일부 매우 복잡한 쿼리는 큰 조인과 정렬로 인해 시스템이 디스크에 분산하지 않고 메모리에서 쿼리를 실행하는 경우 도움이 됩니다.

다음은 리소스 클래스에 의해 제어되는 작업입니다.

* INSERT-SELECT, UPDATE, DELETE
* SELECT(사용자 테이블을 쿼리하는 경우)
* ALTER INDEX - REBUILD 또는 REORGANIZE
* ALTER TABLE REBUILD
* CREATE INDEX
* CREATE CLUSTERED COLUMNSTORE INDEX
* CREATE TABLE AS SELECT (CTAS)
* 데이터 로드
* DMS(데이터 이동 서비스)에서 수행하는 데이터 이동 작업

> [!NOTE]  
> DMV(동적 관리 뷰) 또는 다른 시스템 뷰에서 SELECT 문은 어떠한 동시성 제한에 의해서도 제어되지 않습니다. 시스템에서 실행하는 쿼리 수에 관계없이 시스템을 모니터링할 수 있습니다.
> 
> 

## <a name="static-and-dynamic-resource-classes"></a>정적 및 동적 리소스 클래스

리소스 클래스에는 두 가지 유형(동적 및 정적)이 있습니다.

- **정적 리소스 클래스**는 현재 서비스 수준에 관계없이 동일한 양의 메모리를 할당하며 이는 [데이터 웨어하우스 단위](what-is-a-data-warehouse-unit-dwu-cdwu.md)로 측정됩니다. 이 정적 할당은 더 큰 서비스 수준으로 각 리소스 클래스에서 더 많은 쿼리를 실행할 수 있음을 의미합니다.  정적 리소스 클래스의 이름은 staticrc10, staticrc20, staticrc30, staticrc40, staticrc50, staticrc60, staticrc70 및 staticrc80으로 지정됩니다. 이러한 리소스 클래스는 리소스 클래스를 늘려 추가 계산 리소스를 얻으려는 솔루션에 가장 적합합니다.

- **동적 리소스 클래스**는 현재 서비스 수준에 따라 가변 크기의 메모리를 할당합니다. 즉, 더 큰 서비스 수준으로 확장할 경우 쿼리도 자동으로 더 많은 메모리를 얻게 됩니다. 동적 리소스 클래스의 이름은 smallrc, mediumrc, largerc 및 xlargerc로 지정됩니다. 이러한 리소스 클래스는 계산 크기를 늘려 추가 리소스를 얻으려는 솔루션에 가장 적합합니다. 

[성능 계층](performance-tiers.md)은 동일한 리소스 클래스 이름을 사용하지만 [메모리 및 동시성 사양](performance-tiers.md)은 다릅니다. 


## <a name="assigning-resource-classes"></a>리소스 클래스 할당

데이터베이스 역할에 사용자를 할당하여 리소스 클래스를 구현합니다. 사용자가 쿼리를 실행하면 사용자의 리소스 클래스를 사용하여 쿼리가 실행됩니다. 예를 들어 사용자가 smallrc 또는 staticrc10 데이터베이스 역할의 구성원인 경우 해당 쿼리는 적은 양의 메모리로 실행됩니다. 데이터베이스 사용자가 xlargerc 또는 staticrc80 데이터베이스 역할의 구성원인 경우 해당 쿼리는 많은 양의 메모리로 실행됩니다. 

사용자의 리소스 클래스를 늘리려면 저장 프로시저 [sp_addrolemember](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql)를 사용합니다. 

```sql
EXEC sp_addrolemember 'largerc', 'loaduser';
```

리소스 클래스를 줄이려면 [sp_droprolemember](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-droprolemember-transact-sql)를 사용합니다.  

```sql
EXEC sp_droprolemember 'largerc', 'loaduser';
```

서비스 관리자의 리소스 클래스는 고정되어 있고 변경할 수 없습니다.  서비스 관리자는 프로세스 프로비전 중에 만든 사용자입니다.

### <a name="default-resource-class"></a>기본 리소스 클래스
기본적으로 각 사용자는 작은 리소스 클래스인 **smallrc**의 멤버입니다. 

### <a name="resource-class-precedence"></a>리소스 클래스 우선 순위
사용자는 여러 리소스 클래스의 구성원일 수 있습니다. 사용자가 둘 이상의 리소스 클래스에 속하는 경우:

- 동적 리소스 클래스가 정적 리소스 클래스보다 우선합니다. 예를 들어 사용자가 mediumrc(동적) 및 staticrc80(정적) 둘 다의 구성원인 경우 쿼리는 mediumrc를 사용하여 실행됩니다.
- 큰 리소스 클래스가 작은 리소스 클래스보다 우선합니다. 예를 들어 사용자가 mediumrc 및 largerc의 구성원인 경우 쿼리는 largerc를 사용하여 실행됩니다. 마찬가지로 사용자가 staticrc20 및 statirc80 둘 다의 구성원인 경우 쿼리는 statirc80 리소스 할당을 사용하여 실행됩니다.

### <a name="queries-exempt-from-resource-classes"></a>리소스 클래스에서 쿼리 제외
사용자가 더 큰 리소스 클래스의 구성원인 경우에도 일부 쿼리는 항상 smallrc 리소스 클래스에서 실행됩니다. 이러한 제외 쿼리는 동시성 제한 계산에 포함되지 않습니다. 예를 들어 동시성 제한이 16인 경우 사용할 수 있는 동시성 슬롯에 영향을 주지 않고 많은 사용자가 시스템 뷰에서 선택될 수 있습니다.

다음은 리소스 클래스에서 제외되고 항상 smallrc에서 실행되는 문입니다.

* CREATE 또는 DROP TABLE
* ALTER TABLE ... SWITCH, SPLIT 또는 MERGE PARTITION
* ALTER INDEX DISABLE
* DROP INDEX
* CREATE, UPDATE 또는 DROP STATISTICS
* TRUNCATE TABLE
* ALTER AUTHORIZATION
* CREATE LOGIN
* CREATE, ALTER 또는 DROP USER
* CREATE, ALTER 또는 DROP PROCEDURE
* CREATE 또는 DROP VIEW
* INSERT VALUES
* SELECT(시스템 뷰와 DMV에서)
* EXPLAIN
* DBCC

<!--
Removed as these two are not confirmed / supported under SQLDW
- CREATE REMOTE TABLE AS SELECT
- CREATE EXTERNAL TABLE AS SELECT
- REDISTRIBUTE
-->

## <a name="recommendations"></a>권장 사항
특정 유형의 쿼리 또는 로드 작업만 실행하는 사용자를 만드는 것이 좋습니다. 그러면 리소스 클래스를 자주 변경하는 대신 해당 사용자에게 영구 리소스 클래스를 제공합니다. 정적 리소스 클래스는 워크로드에서 더 광범위한 제어를 허용하므로 동적 리소스 클래스를 고려하기 전에 먼저 정적 리소스 클래스를 사용하는 것이 좋습니다.

### <a name="resource-classes-for-load-users"></a>로드 사용자를 위한 리소스 클래스
`CREATE TABLE`은 기본적으로 클러스터형 columnstore 인덱스를 사용합니다. columnstore 인덱스로 데이터를 압축하는 작업은 메모리 사용량이 많은 작업이며 메모리 압력으로 인해 인덱스 품질이 저하될 수 있습니다. 따라서 데이터 로드 시에는 더 높은 리소스 클래스가 필요할 가능성이 큽니다. 로드 작업의 메모리가 충분하도록 하려면 로드를 실행하도록 지정된 사용자를 만들고 해당 사용자에게 더 높은 리소스 클래스를 할당하면 됩니다.

로드를 효율적으로 처리하는 데 필요한 메모리는 로드된 테이블의 특성과 데이터의 크기에 따라 다릅니다. 메모리 요구 사항에 대한 자세한 내용은 [행 그룹 품질 최대화](sql-data-warehouse-memory-optimizations-for-columnstore-compression.md)를 참조하세요.

메모리 요구 사항이 결정되면 로드 사용자에게 정적 또는 동적 리소스 클래스를 할당할지 선택합니다.

- 테이블 메모리 요구 사항이 특정 범위에 속하는 경우 정적 리소스 클래스를 사용합니다. 적절한 메모리로 로드가 실행됩니다. 데이터 웨어하우스의 크기를 조정할 때 로드 작업에 추가 메모리가 필요하지 않습니다. 정적 리소스 클래스를 사용하면 메모리 할당이 일정하게 유지됩니다. 이러한 일관성으로 메모리를 절약하여 더 많은 쿼리를 동시에 실행할 수 있습니다. 이처럼 새로운 솔루션에는 제어 기능이 더 나은 정적 리소스 클래스를 우선 사용하는 것이 좋습니다.
- 테이블 메모리 요구 사항이 매우 광범위한 경우 동적 리소스 클래스를 사용합니다. 로드 작업은 현재 DWU 또는 cDWU 수준에서 제공하는 것보다 더 많은 메모리를 필요로 할 수 있습니다. 따라서 데이터 웨어하우스 크기 조정으로 로드 작업에 더 많은 메모리를 추가하여 좀 더 빠르게 로드를 수행할 수 있습니다.

### <a name="resource-classes-for-queries"></a>쿼리에 대한 리소스 클래스

일부 쿼리는 계산 집약적이고 일부 쿼리는 그렇지 않습니다.  

- 복잡한 쿼리이기는 하지만 높은 동시성을 요구하지 않는 경우 동적 리소스 클래스를 선택합니다.  예를 들어 매일 또는 매주 보고서를 생성하는 경우 리소스가 가끔 필요합니다. 보고서가 많은 양의 데이터를 처리하는 경우 데이터 웨어하우스 크기를 조정하면 사용자의 기존 리소스 클래스에 더 많은 메모리를 제공할 수 있습니다.
- 하루 중 리소스 요구 사항이 다른 경우 정적 리소스 클래스를 선택합니다. 예를 들어 데이터 웨어하우스가 여러 사용자에 의해 쿼리되는 경우에는 정적 리소스 클래스가 잘 작동합니다. 데이터 웨어하우스의 크기를 조정할 때 사용자에게 할당된 메모리 양은 변경하지 않습니다. 따라서 시스템에서 더 많은 쿼리를 동시에 실행할 수 있습니다.

적절한 메모리 권한을 선택하는 작업은 쿼리되는 데이터의 양, 테이블 스키마의 특성, 다양한 조인, 선택 및 그룹 조건자 등 많은 요인에 따라 달라집니다. 일반적으로 더 많은 메모리를 할당하면 쿼리를 더 빠르게 완료할 수 있지만 전체적인 동시성은 떨어집니다. 동시성이 문제가 되지 않을 경우 메모리를 과도하게 할당해도 처리량은 괜찮습니다. 

성능을 튜닝하려면 다른 리소스 클래스를 사용합니다. 다음 섹션에서는 최상의 리소스 클래스를 파악하는 데 도움이 되는 저장 프로시저를 제공합니다.

## <a name="example-code-for-finding-the-best-resource-class"></a>최상의 리소스 클래스를 찾기 위한 예제 코드
 
다음 저장 프로시저를 사용하여 지정된 SLO에서 리소스 클래스별 메모리 부여 및 동시성을 확인하고 지정된 리소스 클래스의 분할되지 않은 CCI 테이블에 대해 메모리 집약적 CCI 작업을 수행하기 위한 가장 적절한 리소스 클래스를 확인할 수 있습니다.

이 저장 프로시저의 용도는 다음과 같습니다.  
1. 지정된 SLO에서 리소스 클래스별로 메모리 부여 및 동시성을 파악하도록 도와줍니다. 사용자는 이 예제에 나와 있는 것처럼 스키마 및 테이블 이름 둘 다에 대해 NULL을 제공해야 합니다.  
2. 지정된 리소스 클래스에서 분할되지 않은 CCI 테이블에 대해 메모리 집약적 CCI 작업(로드, 테이블 복사, 인덱스 다시 작성 등)을 수행하기 위해 가장 적합한 리소스 클래스를 찾아내도록 도와줍니다. 저장 프로시저는 테이블 스키마를 사용하여 필요한 메모리 부여량을 확인합니다.

### <a name="dependencies--restrictions"></a>종속성 및 제한 사항:
- 이 저장 프로시저는 분할된 cci 테이블의 메모리 요구 사항을 계산하도록 디자인되지 않았습니다.    
- 이 저장 프로시저는 CTAS/INSERT-SELECT의 SELECT 부분에 대한 메모리 요구 사항을 고려하지 않으며 이를 SELECT로 간주합니다.
- 이 저장 프로시저는 이 저장 프로시저가 만들어진 세션에서 사용 가능한 임시 테이블을 사용합니다.    
- 이 저장 프로시저는 현재 제공 환경(예: 하드웨어 구성, DMS 구성)에 의존하므로 제공된 구성이 변경될 경우 이 저장 프로시저가 제대로 작동하지 않습니다.  
- 이 저장 프로시저는 기존의 제공된 동시성 제한에 의존하므로 이러한 동시성 제한이 변경되면 이 저장 프로시저가 제대로 작동하지 않습니다.  
- 이 저장 프로시저는 기존에 제공된 리소스 클래스에 의존하므로 이러한 리소스 클래스가 변경되면 이 저장 프로시저가 제대로 작동하지 않습니다.  

>  [!NOTE]  
>  제공된 매개 변수로 저장 프로시저를 실행한 후에 출력이 표시되지 않으면 다음 2가지 경우로 가정할 수 있습니다. <br />1. DW 매개 변수 중 하나에 잘못된 SLO 값이 포함되어 있습니다. <br />2. 또는 테이블에 CCI 작업과 일치하는 리소스 클래스가 없습니다. <br />예를 들어 DW100에서 부여 가능한 최대 메모리는 400MB이고, 테이블 스키마 크기가 400MB의 요구 사항을 충족할 크기여야 합니다.
      
### <a name="usage-example"></a>사용법 예제:
구문  
`EXEC dbo.prc_workload_management_by_DWU @DWU VARCHAR(7), @SCHEMA_NAME VARCHAR(128), @TABLE_NAME VARCHAR(128)`  
1. @DWU: NULL 매개 변수를 제공하여 DW DB에서 현재 DWU를 추출하거나 지원되는 모든 DWU를 'DW100' 형식으로 제공
2. @SCHEMA_NAME: 테이블의 스키마 이름 제공
3. @TABLE_NAME: 관심 있는 테이블 이름 제공

이 저장 프로시저 실행 예제:  
```sql  
EXEC dbo.prc_workload_management_by_DWU 'DW2000', 'dbo', 'Table1';  
EXEC dbo.prc_workload_management_by_DWU NULL, 'dbo', 'Table1';  
EXEC dbo.prc_workload_management_by_DWU 'DW6000', NULL, NULL;  
EXEC dbo.prc_workload_management_by_DWU NULL, NULL, NULL;  
```

다음은 이전 예제에서 사용된 Table1을 만드는 문입니다.
`CREATE TABLE Table1 (a int, b varchar(50), c decimal (18,10), d char(10), e varbinary(15), f float, g datetime, h date);`

### <a name="stored-procedure-definition"></a>저장 프로시저 정의

```sql  
-------------------------------------------------------------------------------
-- Dropping prc_workload_management_by_DWU procedure if it exists.
-------------------------------------------------------------------------------
IF EXISTS (SELECT * FROM sys.objects WHERE type = 'P' AND name = 'prc_workload_management_by_DWU')
DROP PROCEDURE dbo.prc_workload_management_by_DWU
GO

-------------------------------------------------------------------------------
-- Creating prc_workload_management_by_DWU.
-------------------------------------------------------------------------------
CREATE PROCEDURE dbo.prc_workload_management_by_DWU
(@DWU VARCHAR(7),
 @SCHEMA_NAME VARCHAR(128),
 @TABLE_NAME VARCHAR(128)
)
AS
IF @DWU IS NULL
BEGIN
-- Selecting proper DWU for the current DB if not specified.
SET @DWU = (
  SELECT 'DW'+CAST(COUNT(*)*100 AS VARCHAR(10))
  FROM sys.dm_pdw_nodes
  WHERE type = 'COMPUTE')
END

DECLARE @DWU_NUM INT
SET @DWU_NUM = CAST (SUBSTRING(@DWU, 3, LEN(@DWU)-2) AS INT)

-- Raise error if either schema name or table name is supplied but not both them supplied
--IF ((@SCHEMA_NAME IS NOT NULL AND @TABLE_NAME IS NULL) OR (@TABLE_NAME IS NULL AND @SCHEMA_NAME IS NOT NULL))
--     RAISEERROR('User need to supply either both Schema Name and Table Name or none of them')
       
-- Dropping temp table if exists.
IF OBJECT_ID('tempdb..#ref') IS NOT NULL
BEGIN
  DROP TABLE #ref;
END

-- Creating ref. temp table (CTAS) to hold mapping info.
-- CREATE TABLE #ref
CREATE TABLE #ref
WITH (DISTRIBUTION = ROUND_ROBIN)
AS 
WITH
-- Creating concurrency slots mapping for various DWUs.
alloc
AS
(
  SELECT 'DW100' AS DWU, 4 AS max_queries, 4 AS max_slots, 1 AS slots_used_smallrc, 1 AS slots_used_mediumrc,
        2 AS slots_used_largerc, 4 AS slots_used_xlargerc, 1 AS slots_used_staticrc10, 2 AS slots_used_staticrc20,
        4 AS slots_used_staticrc30, 4 AS slots_used_staticrc40, 4 AS slots_used_staticrc50,
        4 AS slots_used_staticrc60, 4 AS slots_used_staticrc70, 4 AS slots_used_staticrc80
  UNION ALL
    SELECT 'DW200', 8, 8, 1, 2, 4, 8, 1, 2, 4, 8, 8, 8, 8, 8
  UNION ALL
    SELECT 'DW300', 12, 12, 1, 2, 4, 8, 1, 2, 4, 8, 8, 8, 8, 8
  UNION ALL
    SELECT 'DW400', 16, 16, 1, 4, 8, 16, 1, 2, 4, 8, 16, 16, 16, 16
  UNION ALL
     SELECT 'DW500', 20, 20, 1, 4, 8, 16, 1, 2, 4, 8, 16, 16, 16, 16
  UNION ALL
    SELECT 'DW600', 24, 24, 1, 4, 8, 16, 1, 2, 4, 8, 16, 16, 16, 16
  UNION ALL
    SELECT 'DW1000', 32, 40, 1, 8, 16, 32, 1, 2, 4, 8, 16, 32, 32, 32
  UNION ALL
    SELECT 'DW1200', 32, 48, 1, 8, 16, 32, 1, 2, 4, 8, 16, 32, 32, 32
  UNION ALL
    SELECT 'DW1500', 32, 60, 1, 8, 16, 32, 1, 2, 4, 8, 16, 32, 32, 32
  UNION ALL
    SELECT 'DW2000', 32, 80, 1, 16, 32, 64, 1, 2, 4, 8, 16, 32, 64, 64
  UNION ALL
   SELECT 'DW3000', 32, 120, 1, 16, 32, 64, 1, 2, 4, 8, 16, 32, 64, 64
  UNION ALL
    SELECT 'DW6000', 32, 240, 1, 32, 64, 128, 1, 2, 4, 8, 16, 32, 64, 128
)
-- Creating workload mapping to their corresponding slot consumption and default memory grant.
,map
AS
(
  SELECT 'SloDWGroupC00' AS wg_name,1 AS slots_used,100 AS tgt_mem_grant_MB
  UNION ALL
    SELECT 'SloDWGroupC01',2,200
  UNION ALL
    SELECT 'SloDWGroupC02',4,400
  UNION ALL
    SELECT 'SloDWGroupC03',8,800
  UNION ALL
    SELECT 'SloDWGroupC04',16,1600
  UNION ALL
    SELECT 'SloDWGroupC05',32,3200
  UNION ALL
    SELECT 'SloDWGroupC06',64,6400
  UNION ALL
    SELECT 'SloDWGroupC07',128,12800
)
-- Creating ref based on current / asked DWU.
, ref
AS
(
  SELECT  a1.*
  ,       m1.wg_name          AS wg_name_smallrc
  ,       m1.tgt_mem_grant_MB AS tgt_mem_grant_MB_smallrc
  ,       m2.wg_name          AS wg_name_mediumrc
  ,       m2.tgt_mem_grant_MB AS tgt_mem_grant_MB_mediumrc
  ,       m3.wg_name          AS wg_name_largerc
  ,       m3.tgt_mem_grant_MB AS tgt_mem_grant_MB_largerc
  ,       m4.wg_name          AS wg_name_xlargerc
  ,       m4.tgt_mem_grant_MB AS tgt_mem_grant_MB_xlargerc
  ,       m5.wg_name          AS wg_name_staticrc10
  ,       m5.tgt_mem_grant_MB AS tgt_mem_grant_MB_staticrc10
  ,       m6.wg_name          AS wg_name_staticrc20
  ,       m6.tgt_mem_grant_MB AS tgt_mem_grant_MB_staticrc20
  ,       m7.wg_name          AS wg_name_staticrc30
  ,       m7.tgt_mem_grant_MB AS tgt_mem_grant_MB_staticrc30
  ,       m8.wg_name          AS wg_name_staticrc40
  ,       m8.tgt_mem_grant_MB AS tgt_mem_grant_MB_staticrc40
  ,       m9.wg_name          AS wg_name_staticrc50
  ,       m9.tgt_mem_grant_MB AS tgt_mem_grant_MB_staticrc50
  ,       m10.wg_name          AS wg_name_staticrc60
  ,       m10.tgt_mem_grant_MB AS tgt_mem_grant_MB_staticrc60
  ,       m11.wg_name          AS wg_name_staticrc70
  ,       m11.tgt_mem_grant_MB AS tgt_mem_grant_MB_staticrc70
  ,       m12.wg_name          AS wg_name_staticrc80
  ,       m12.tgt_mem_grant_MB AS tgt_mem_grant_MB_staticrc80
  FROM alloc a1
  JOIN map   m1  ON a1.slots_used_smallrc     = m1.slots_used
  JOIN map   m2  ON a1.slots_used_mediumrc    = m2.slots_used
  JOIN map   m3  ON a1.slots_used_largerc     = m3.slots_used
  JOIN map   m4  ON a1.slots_used_xlargerc    = m4.slots_used
  JOIN map   m5  ON a1.slots_used_staticrc10    = m5.slots_used
  JOIN map   m6  ON a1.slots_used_staticrc20    = m6.slots_used
  JOIN map   m7  ON a1.slots_used_staticrc30    = m7.slots_used
  JOIN map   m8  ON a1.slots_used_staticrc40    = m8.slots_used
  JOIN map   m9  ON a1.slots_used_staticrc50    = m9.slots_used
  JOIN map   m10  ON a1.slots_used_staticrc60    = m10.slots_used
  JOIN map   m11  ON a1.slots_used_staticrc70    = m11.slots_used
  JOIN map   m12  ON a1.slots_used_staticrc80    = m12.slots_used
-- WHERE   a1.DWU = @DWU
  WHERE   a1.DWU = UPPER(@DWU)
)
SELECT  DWU
,       max_queries
,       max_slots
,       slots_used
,       wg_name
,       tgt_mem_grant_MB
,       up1 as rc
,       (ROW_NUMBER() OVER(PARTITION BY DWU ORDER BY DWU)) as rc_id
FROM
(
    SELECT  DWU
    ,       max_queries
    ,       max_slots
    ,       slots_used
    ,       wg_name
    ,       tgt_mem_grant_MB
    ,       REVERSE(SUBSTRING(REVERSE(wg_names),1,CHARINDEX('_',REVERSE(wg_names),1)-1)) as up1
    ,       REVERSE(SUBSTRING(REVERSE(tgt_mem_grant_MBs),1,CHARINDEX('_',REVERSE(tgt_mem_grant_MBs),1)-1)) as up2
    ,       REVERSE(SUBSTRING(REVERSE(slots_used_all),1,CHARINDEX('_',REVERSE(slots_used_all),1)-1)) as up3
    FROM    ref AS r1
    UNPIVOT
    (
        wg_name FOR wg_names IN (wg_name_smallrc,wg_name_mediumrc,wg_name_largerc,wg_name_xlargerc,
        wg_name_staticrc10, wg_name_staticrc20, wg_name_staticrc30, wg_name_staticrc40, wg_name_staticrc50,
        wg_name_staticrc60, wg_name_staticrc70, wg_name_staticrc80)
    ) AS r2
    UNPIVOT
    (
        tgt_mem_grant_MB FOR tgt_mem_grant_MBs IN (tgt_mem_grant_MB_smallrc,tgt_mem_grant_MB_mediumrc,
        tgt_mem_grant_MB_largerc,tgt_mem_grant_MB_xlargerc, tgt_mem_grant_MB_staticrc10, tgt_mem_grant_MB_staticrc20,
        tgt_mem_grant_MB_staticrc30, tgt_mem_grant_MB_staticrc40, tgt_mem_grant_MB_staticrc50,
        tgt_mem_grant_MB_staticrc60, tgt_mem_grant_MB_staticrc70, tgt_mem_grant_MB_staticrc80)
    ) AS r3
    UNPIVOT
    (
        slots_used FOR slots_used_all IN (slots_used_smallrc,slots_used_mediumrc,slots_used_largerc,
        slots_used_xlargerc, slots_used_staticrc10, slots_used_staticrc20, slots_used_staticrc30,
        slots_used_staticrc40, slots_used_staticrc50, slots_used_staticrc60, slots_used_staticrc70,
        slots_used_staticrc80)
    ) AS r4
) a
WHERE   up1 = up2
AND     up1 = up3
;
-- Getting current info about workload groups.
WITH  
dmv  
AS  
(
  SELECT
          rp.name                                           AS rp_name
  ,       rp.max_memory_kb*1.0/1048576                      AS rp_max_mem_GB
  ,       (rp.max_memory_kb*1.0/1024)
          *(request_max_memory_grant_percent/100)           AS max_memory_grant_MB
  ,       (rp.max_memory_kb*1.0/1048576)
          *(request_max_memory_grant_percent/100)           AS max_memory_grant_GB
  ,       wg.name                                           AS wg_name
  ,       wg.importance                                     AS importance
  ,       wg.request_max_memory_grant_percent               AS request_max_memory_grant_percent
  FROM    sys.dm_pdw_nodes_resource_governor_workload_groups wg
  JOIN    sys.dm_pdw_nodes_resource_governor_resource_pools rp    ON  wg.pdw_node_id  = rp.pdw_node_id
                                                                  AND wg.pool_id      = rp.pool_id
  WHERE   rp.name = 'SloDWPool'
  GROUP BY
          rp.name
  ,       rp.max_memory_kb
  ,       wg.name
  ,       wg.importance
  ,       wg.request_max_memory_grant_percent
)
-- Creating resource class name mapping.
,names
AS
(
  SELECT 'smallrc' as resource_class, 1 as rc_id
  UNION ALL
    SELECT 'mediumrc', 2
  UNION ALL
    SELECT 'largerc', 3
  UNION ALL
    SELECT 'xlargerc', 4
  UNION ALL
    SELECT 'staticrc10', 5
  UNION ALL
    SELECT 'staticrc20', 6
  UNION ALL
    SELECT 'staticrc30', 7
  UNION ALL
    SELECT 'staticrc40', 8
  UNION ALL
    SELECT 'staticrc50', 9
  UNION ALL
    SELECT 'staticrc60', 10
  UNION ALL
    SELECT 'staticrc70', 11
  UNION ALL
    SELECT 'staticrc80', 12
)
,base AS
(   SELECT  schema_name
    ,       table_name
    ,       SUM(column_count)                   AS column_count
    ,       ISNULL(SUM(short_string_column_count),0)   AS short_string_column_count
    ,       ISNULL(SUM(long_string_column_count),0)    AS long_string_column_count
    FROM    (   SELECT  sm.name                                             AS schema_name
                ,       tb.name                                             AS table_name
                ,       COUNT(co.column_id)                                 AS column_count
                           ,       CASE    WHEN co.system_type_id IN (36,43,106,108,165,167,173,175,231,239) 
                                AND  co.max_length <= 32 
                                THEN COUNT(co.column_id) 
                        END                                                 AS short_string_column_count
                ,       CASE    WHEN co.system_type_id IN (165,167,173,175,231,239) 
                                AND  co.max_length > 32 and co.max_length <=8000
                                THEN COUNT(co.column_id) 
                        END                                                 AS long_string_column_count
                FROM    sys.schemas AS sm
                JOIN    sys.tables  AS tb   on sm.[schema_id] = tb.[schema_id]
                JOIN    sys.columns AS co   ON tb.[object_id] = co.[object_id]
                           WHERE tb.name = @TABLE_NAME AND sm.name = @SCHEMA_NAME
                GROUP BY sm.name
                ,        tb.name
                ,        co.system_type_id
                ,        co.max_length            ) a
GROUP BY schema_name
,        table_name
)
, size AS
(
SELECT  schema_name
,       table_name
,       75497472                                            AS table_overhead

,       column_count*1048576*8                              AS column_size
,       short_string_column_count*1048576*32                       AS short_string_size,       (long_string_column_count*16777216) AS long_string_size
FROM    base
UNION
SELECT CASE WHEN COUNT(*) = 0 THEN 'EMPTY' END as schema_name
         ,CASE WHEN COUNT(*) = 0 THEN 'EMPTY' END as table_name
         ,CASE WHEN COUNT(*) = 0 THEN 0 END as table_overhead
         ,CASE WHEN COUNT(*) = 0 THEN 0 END as column_size
         ,CASE WHEN COUNT(*) = 0 THEN 0 END as short_string_size

,CASE WHEN COUNT(*) = 0 THEN 0 END as long_string_size
FROM   base
)
, load_multiplier as 
(
SELECT  CASE 
                     WHEN FLOOR(8 * (CAST (@DWU_NUM AS FLOAT)/6000)) > 0 THEN FLOOR(8 * (CAST (@DWU_NUM AS FLOAT)/6000)) 
                     ELSE 1 
              END AS multipliplication_factor
) 
       SELECT  r1.DWU
       , schema_name
       , table_name
       , rc.resource_class as closest_rc_in_increasing_order
       , max_queries_at_this_rc = CASE
             WHEN (r1.max_slots / r1.slots_used > r1.max_queries)
                  THEN r1.max_queries
             ELSE r1.max_slots / r1.slots_used
                  END
       , r1.max_slots as max_concurrency_slots
       , r1.slots_used as required_slots_for_the_rc
       , r1.tgt_mem_grant_MB  as rc_mem_grant_MB
       , CAST((table_overhead*1.0+column_size+short_string_size+long_string_size)*multipliplication_factor/1048576    AS DECIMAL(18,2)) AS est_mem_grant_required_for_cci_operation_MB       
       FROM    size, load_multiplier, #ref r1, names  rc
       WHERE r1.rc_id=rc.rc_id
                     AND CAST((table_overhead*1.0+column_size+short_string_size+long_string_size)*multipliplication_factor/1048576    AS DECIMAL(18,2)) < r1.tgt_mem_grant_MB
       ORDER BY ABS(CAST((table_overhead*1.0+column_size+short_string_size+long_string_size)*multipliplication_factor/1048576    AS DECIMAL(18,2)) - r1.tgt_mem_grant_MB)
GO
```




## <a name="next-steps"></a>다음 단계
데이터베이스 사용자 및 보안을 관리하는 방법에 대한 자세한 내용은 [SQL Data Warehouse에서 데이터베이스 보호][Secure a database in SQL Data Warehouse]를 참조하세요. 더 큰 리소스 클래스가 클러스터형 columnstore 인덱스 품질을 향상시키는 방법에 대한 자세한 내용은 [Columnstore 압축을 위한 메모리 최적화](sql-data-warehouse-memory-optimizations-for-columnstore-compression.md)를 참조하세요.

<!--Image references-->

<!--Article references-->
[Secure a database in SQL Data Warehouse]: ./sql-data-warehouse-overview-manage-security.md
[Rebuilding indexes to improve segment quality]: ./sql-data-warehouse-tables-index.md#rebuilding-indexes-to-improve-segment-quality
[Secure a database in SQL Data Warehouse]: ./sql-data-warehouse-overview-manage-security.md

<!--MSDN references-->
[Managing Databases and Logins in Azure SQL Database]:https://msdn.microsoft.com/library/azure/ee336235.aspx

<!--Other Web references-->
