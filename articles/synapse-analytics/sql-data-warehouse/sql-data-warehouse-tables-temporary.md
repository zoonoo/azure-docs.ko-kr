---
title: 임시 테이블
description: Synapse SQL 풀에서 임시 테이블을 사용하기 위한 필수 지침으로 세션 수준 임시 테이블의 원칙을 강조표시합니다.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/01/2019
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 64490bbd44066389186a59e851045b6becbe7acc
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/03/2020
ms.locfileid: "80632475"
---
# <a name="temporary-tables-in-synapse-sql-pool"></a>시냅스 SQL 풀의 임시 테이블
이 문서에서는 임시 테이블을 사용하기 위한 필수 지침을 제공하고 세션 수준 임시 테이블의 원리를 강조해서 설명합니다. 

이 문서의 정보를 사용하면 코드를 모듈화하여 재사용성과 유지 관리 의 용이성을 모두 향상시킬 수 있습니다.

## <a name="what-are-temporary-tables"></a>임시 테이블이란?
임시 테이블은 특히 중간 결과가 일시적인 변환 중에 데이터를 처리할 때 유용합니다. SQL 풀에서 임시 테이블은 세션 수준에 있습니다.  

임시 테이블은 생성된 세션에만 표시되며 해당 세션이 로그오프될 때 자동으로 삭제됩니다.  

임시 테이블은 결과가 원격 스토리지 대신 로컬로 기록되기 때문에 성능상의 이점을 제공합니다.

## <a name="create-a-temporary-table"></a>임시 테이블 만들기
임시 테이블은 테이블 이름 앞에 `#`을 붙여 만듭니다.  다음은 그 예입니다.

```sql
CREATE TABLE #stats_ddl
(
    [schema_name]        NVARCHAR(128) NOT NULL
,    [table_name]            NVARCHAR(128) NOT NULL
,    [stats_name]            NVARCHAR(128) NOT NULL
,    [stats_is_filtered]     BIT           NOT NULL
,    [seq_nmbr]              BIGINT        NOT NULL
,    [two_part_name]         NVARCHAR(260) NOT NULL
,    [three_part_name]       NVARCHAR(400) NOT NULL
)
WITH
(
    DISTRIBUTION = HASH([seq_nmbr])
,    HEAP
)
```

정확히 동일한 접근 방식을 사용하여 `CTAS` 를 통해 임시 테이블을 만들 수도 있습니다.

```sql
CREATE TABLE #stats_ddl
WITH
(
    DISTRIBUTION = HASH([seq_nmbr])
,    HEAP
)
AS
(
SELECT
        sm.[name]                                                                AS [schema_name]
,        tb.[name]                                                                AS [table_name]
,        st.[name]                                                                AS [stats_name]
,        st.[has_filter]                                                            AS [stats_is_filtered]
,       ROW_NUMBER()
        OVER(ORDER BY (SELECT NULL))                                            AS [seq_nmbr]
,                                 QUOTENAME(sm.[name])+'.'+QUOTENAME(tb.[name])  AS [two_part_name]
,        QUOTENAME(DB_NAME())+'.'+QUOTENAME(sm.[name])+'.'+QUOTENAME(tb.[name])  AS [three_part_name]
FROM    sys.objects            AS ob
JOIN    sys.stats            AS st    ON    ob.[object_id]        = st.[object_id]
JOIN    sys.stats_columns    AS sc    ON    st.[stats_id]        = sc.[stats_id]
                                    AND st.[object_id]        = sc.[object_id]
JOIN    sys.columns            AS co    ON    sc.[column_id]        = co.[column_id]
                                    AND    sc.[object_id]        = co.[object_id]
JOIN    sys.tables            AS tb    ON    co.[object_id]        = tb.[object_id]
JOIN    sys.schemas            AS sm    ON    tb.[schema_id]        = sm.[schema_id]
WHERE    1=1
AND        st.[user_created]   = 1
GROUP BY
        sm.[name]
,        tb.[name]
,        st.[name]
,        st.[filter_definition]
,        st.[has_filter]
)
;
``` 

> [!NOTE]
> `CTAS`는(은) 강력한 명령이며 트랜잭션 로그 공간을 사용한다는 점에서 효율적이라는 추가적인 이점이 있습니다. 
> 
> 

## <a name="dropping-temporary-tables"></a>임시 테이블 삭제
새 세션이 만들어지면 임시 테이블이 존재하지 않습니다.  

동일한 이름의 임시 프로시저를 호출하여 명령문이 `CREATE TABLE` 성공했는지 확인하는 경우 다음 예제와 같이 간단한 `DROP` 사전 사용 확인을 사용할 수 있습니다.

```sql
IF OBJECT_ID('tempdb..#stats_ddl') IS NOT NULL
BEGIN
    DROP TABLE #stats_ddl
END
```

코딩 일관성을 위해 이 패턴을 테이블과 임시 테이블 모두에 사용하는 것이 좋습니다.  또한 코드에서 임시 테이블을 `DROP TABLE` 완료하면 임시 테이블을 제거하는 데 사용하는 것이 좋습니다.  

저장 프로시저 개발에서는 이러한 개체가 정리되도록 프로시저 끝에 드롭 명령이 함께 번들로 제공되는 것을 확인하는 것이 일반적입니다.

```sql
DROP TABLE #stats_ddl
```

## <a name="modularizing-code"></a>코드 모듈화
임시 테이블은 사용자 세션의 어느 곳에서나 볼 수 있으므로 이 기능을 활용하여 응용 프로그램 코드를 모듈화하는 데 도움이 될 수 있습니다.  

예를 들어 다음 저장 프로시저는 DDL을 생성하여 데이터베이스의 모든 통계를 통계 이름으로 업데이트합니다.

```sql
CREATE PROCEDURE    [dbo].[prc_sqldw_update_stats]
(   @update_type    tinyint -- 1 default 2 fullscan 3 sample 4 resample
    ,@sample_pct     tinyint
)
AS

IF @update_type NOT IN (1,2,3,4)
BEGIN;
    THROW 151000,'Invalid value for @update_type parameter. Valid range 1 (default), 2 (fullscan), 3 (sample) or 4 (resample).',1;
END;

IF @sample_pct IS NULL
BEGIN;
    SET @sample_pct = 20;
END;

IF OBJECT_ID('tempdb..#stats_ddl') IS NOT NULL
BEGIN
    DROP TABLE #stats_ddl
END

CREATE TABLE #stats_ddl
WITH
(
    DISTRIBUTION = HASH([seq_nmbr])
)
AS
(
SELECT
        sm.[name]                                                                AS [schema_name]
,        tb.[name]                                                                AS [table_name]
,        st.[name]                                                                AS [stats_name]
,        st.[has_filter]                                                            AS [stats_is_filtered]
,       ROW_NUMBER()
        OVER(ORDER BY (SELECT NULL))                                            AS [seq_nmbr]
,                                 QUOTENAME(sm.[name])+'.'+QUOTENAME(tb.[name])  AS [two_part_name]
,        QUOTENAME(DB_NAME())+'.'+QUOTENAME(sm.[name])+'.'+QUOTENAME(tb.[name])  AS [three_part_name]
FROM    sys.objects            AS ob
JOIN    sys.stats            AS st    ON    ob.[object_id]        = st.[object_id]
JOIN    sys.stats_columns    AS sc    ON    st.[stats_id]        = sc.[stats_id]
                                    AND st.[object_id]        = sc.[object_id]
JOIN    sys.columns            AS co    ON    sc.[column_id]        = co.[column_id]
                                    AND    sc.[object_id]        = co.[object_id]
JOIN    sys.tables            AS tb    ON    co.[object_id]        = tb.[object_id]
JOIN    sys.schemas            AS sm    ON    tb.[schema_id]        = sm.[schema_id]
WHERE    1=1
AND        st.[user_created]   = 1
GROUP BY
        sm.[name]
,        tb.[name]
,        st.[name]
,        st.[filter_definition]
,        st.[has_filter]
)
SELECT
    CASE @update_type
    WHEN 1
    THEN 'UPDATE STATISTICS '+[two_part_name]+'('+[stats_name]+');'
    WHEN 2
    THEN 'UPDATE STATISTICS '+[two_part_name]+'('+[stats_name]+') WITH FULLSCAN;'
    WHEN 3
    THEN 'UPDATE STATISTICS '+[two_part_name]+'('+[stats_name]+') WITH SAMPLE '+CAST(@sample_pct AS VARCHAR(20))+' PERCENT;'
    WHEN 4
    THEN 'UPDATE STATISTICS '+[two_part_name]+'('+[stats_name]+') WITH RESAMPLE;'
    END AS [update_stats_ddl]
,   [seq_nmbr]
FROM    t1
;
GO
```

이 단계에서 발생한 유일한 작업은 DDL 문을 #stats_ddl 임시 테이블을 생성하는 저장 프로시저를 만드는 것입니다.  

이 저장 프로시저는 세션 내에서 두 번 이상 실행해도 실패하지 않도록 기존 #stats_ddl 삭제합니다.  

그러나 저장 프로시저 끝에는 `DROP TABLE` 이 없으므로 저장 프로시저가 완료되면 저장 프로시저 외부에서 읽을 수 있도록 만든 테이블이 그대로 남아 있습니다.  

SQL 풀에서는 다른 SQL Server 데이터베이스와 달리 임시 테이블을 만든 프로시저 외부에서 사용할 수 있습니다.  SQL 풀 임시 테이블은 세션 내의 **어느 곳에서나** 사용할 수 있습니다. 이 기능은 다음 예제와 같이 보다 모듈식 및 관리 가능한 코드로 이어질 수 있습니다.

```sql
EXEC [dbo].[prc_sqldw_update_stats] @update_type = 1, @sample_pct = NULL;

DECLARE @i INT              = 1
,       @t INT              = (SELECT COUNT(*) FROM #stats_ddl)
,       @s NVARCHAR(4000)   = N''

WHILE @i <= @t
BEGIN
    SET @s=(SELECT update_stats_ddl FROM #stats_ddl WHERE seq_nmbr = @i);

    PRINT @s
    EXEC sp_executesql @s
    SET @i+=1;
END

DROP TABLE #stats_ddl;
```

## <a name="temporary-table-limitations"></a>임시 테이블 제한 사항
SQL 풀은 임시 테이블을 구현할 때 몇 가지 제한 사항이 적용됩니다.  현재 세션 범위의 임시 테이블만 지원됩니다.  전역 임시 테이블은 지원되지 않습니다.  

또한 임시 테이블에서보기를 만들 수 없습니다.  임시 테이블은 해시 또는 라운드 로빈 분포로만 만들 수 있습니다.  복제된 임시 테이블 배포는 지원되지 않습니다. 

## <a name="next-steps"></a>다음 단계
테이블 개발에 대해 자세히 알아보려면 [테이블 개요](sql-data-warehouse-tables-overview.md)를 참조하세요.

