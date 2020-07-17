---
title: Synapse SQL에서 임시 테이블 사용
description: Synapse SQL에서 임시 테이블을 사용 하기 위한 필수 지침을 제공 합니다.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: 090f453771dba6f537ad60605c6e9b96f3ca9957
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "81428759"
---
# <a name="temporary-tables-in-synapse-sql"></a>Synapse SQL의 임시 테이블

이 문서에는 임시 테이블 사용에 대 한 필수 지침이 포함 되어 있으며 Synapse SQL 내에서 세션 수준 임시 테이블의 원리를 강조 표시 합니다. 

SQL 풀 및 SQL 주문형 (미리 보기) 리소스는 모두 임시 테이블을 사용할 수 있습니다. SQL 주문형 요청에는이 문서의 끝 부분에서 설명 하는 제한 사항이 있습니다. 

## <a name="what-are-temporary-tables"></a>임시 테이블이란?

특히 중간 결과가 일시적인 변환 중 데이터를 처리할 때 임시 테이블은 유용합니다. Synapse SQL을 사용 하면 임시 테이블이 세션 수준에서 존재 합니다.  이러한 임시 테이블은 만들어진 세션에만 표시됩니다. 따라서 해당 세션이 로그오프되면 자동으로 삭제됩니다. 

## <a name="temporary-tables-in-sql-pool"></a>SQL 풀의 임시 테이블

SQL 풀 리소스에서 임시 테이블은 결과가 원격 스토리지 대신 로컬로 기록되기 때문에 성능상의 이점을 제공합니다.

### <a name="create-a-temporary-table"></a>임시 테이블 만들기

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

### <a name="dropping-temporary-tables"></a>임시 테이블 삭제
새 세션이 만들어지면 임시 테이블이 존재하지 않습니다.  그러나 이름이 같은 임시를 만드는 동일한 저장 프로시저를 호출 하는 경우 `CREATE TABLE` 문이 성공적으로 실행 되도록 하려면 다음과 같이 간단한 사전 존재 검사를 사용 합니다 `DROP` . 

```sql
IF OBJECT_ID('tempdb..#stats_ddl') IS NOT NULL
BEGIN
    DROP TABLE #stats_ddl
END
```

코딩 일관성을 유지하려면 테이블 및 임시 테이블 모두에 대해 이 패턴을 사용하는 것이 좋습니다.  를 사용 하 여 `DROP TABLE` 작업이 완료 되 면 임시 테이블을 제거 하는 것도 좋습니다.  

저장 프로시저 개발에서는 프로시저 끝에 삭제 명령이 번들로 포함되어 있는지 확인하여 이러한 개체가 정리되는지 알 수 있습니다.

```sql
DROP TABLE #stats_ddl
```

### <a name="modularizing-code"></a>코드 모듈화
임시 테이블은 사용자 세션의 모든 위치에서 사용할 수 있습니다. 그러면이 기능을 활용 하 여 응용 프로그램 코드를 모듈화 수 있습니다.  이를 보여 주기 위해 다음 저장 프로시저는 DDL을 생성 하 여 통계 이름별로 데이터베이스의 모든 통계를 업데이트 합니다.

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

이 단계에서 발생 하는 유일한 작업은 #stats_ddl 임시 테이블을 생성 하는 저장 프로시저를 만드는 것입니다.  저장 프로시저는 이미 있는 경우 #stats_ddl를 삭제 합니다. 이 삭제는 세션 내에서 두 번 이상 실행 하는 경우 실패 하지 않도록 합니다.  

저장 `DROP TABLE` 프로시저의 끝에는이 없으므로 저장 프로시저가 완료 되 면 생성 된 테이블은 유지 되 고 저장 프로시저 외부에서 읽을 수 있습니다.  

다른 SQL Server 데이터베이스와 달리 Synapse SQL에서는 임시 테이블을 만든 프로시저 외부에서 임시 테이블을 사용할 수 있습니다.  SQL 풀을 통해 만든 임시 테이블은 **세션 내부에서** 사용할 수 있습니다. 결과적으로 아래 샘플에서 볼 수 있듯이 모듈식 및 관리 하기 쉬운 코드를 사용할 수 있습니다.

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

### <a name="temporary-table-limitations"></a>임시 테이블 제한 사항

SQL 풀에는 임시 테이블에 대 한 몇 가지 구현 제한 사항이 있습니다.

- 세션 범위 임시 테이블만 지원 됩니다.  전역 임시 테이블은 지원되지 않습니다.
- 임시 테이블에는 뷰를 만들 수 없습니다.
- 임시 테이블은 해시 또는 라운드 로빈 배포를 통해서만 만들 수 있습니다.  복제된 임시 테이블 배포가 지원되지 않습니다. 

## <a name="temporary-tables-in-sql-on-demand-preview"></a>SQL 주문형 임시 테이블 (미리 보기)

SQL 주문형 임시 테이블은 지원 되지만 사용이 제한 됩니다. 이러한 파일은 파일을 대상으로 하는 쿼리에서 사용할 수 없습니다. 

예를 들어 저장소에 있는 파일의 데이터를 사용 하 여 임시 테이블에 조인할 수 없습니다. 임시 테이블의 수는 100 개로 제한 되 고 총 크기는 100MB로 제한 됩니다.

## <a name="next-steps"></a>다음 단계

테이블 개발에 대한 자세한 내용은 [Synapse SQL 리소스를 사용한 테이블 디자인](develop-tables-overview.md) 문서를 참조하세요.

