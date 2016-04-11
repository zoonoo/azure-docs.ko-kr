<properties
   pageTitle="SQL 데이터 웨어하우스의 임시 테이블 | Microsoft Azure"
   description="솔루션 개발을 위한 Azure SQL 데이터 웨어하우스의 임시 테이블 사용을 위한 팁"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="jrowlandjones"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="03/23/2016"
   ms.author="mausher;jrj;barbkess;sonyama"/>

# SQL 데이터 웨어하우스의 임시 테이블
특히 중간 결과가 일시적인 변환 중 데이터를 처리할 때 임시 테이블은 매우 유용합니다. 임시 테이블은 SQL 데이터 웨어하우스의 세션 수준에서 존재합니다. 이 테이블은 로컬 임시 테이블로 정의되지만 SQL Server 테이블과 달리 세션 내 어디에서나 액세스할 수 있습니다.

이 문서에서는 임시 테이블을 사용하기 위한 몇 가지 필수 지침을 제공하고 세션 수준 임시 테이블의 원리을 강조해서 설명합니다. 이 정보를 사용하면 코드를 모듈화하는 데 도움이 될 수 있습니다. 코드 모듈화는 유지 관리 용이성 및 코드 재사용에 중요합니다.

## 임시 테이블 만들기
임시 테이블 만들기는 매우 간단합니다. 아래 예제와 같이 테이블 이름 앞에 #만 붙이면 됩니다.

```sql
CREATE TABLE #stats_ddl
(
	[schema_name]			NVARCHAR(128) NOT NULL
,	[table_name]            NVARCHAR(128) NOT NULL
,	[stats_name]            NVARCHAR(128) NOT NULL
,	[stats_is_filtered]     BIT           NOT NULL
,	[seq_nmbr]              BIGINT        NOT NULL
,	[two_part_name]         NVARCHAR(260) NOT NULL
,	[three_part_name]       NVARCHAR(400) NOT NULL
)
WITH
(
	DISTRIBUTION = HASH([seq_nmbr])
,	HEAP
)
```

정확히 동일한 접근 방식을 사용하여 `CTAS`를 통해 임시 테이블을 만들 수도 있습니다.

```sql
CREATE TABLE #stats_ddl
WITH
(
	DISTRIBUTION = HASH([seq_nmbr])
,	HEAP
)
AS
(
SELECT
		sm.[name]				                                                AS [schema_name]
,		tb.[name]				                                                AS [table_name]
,		st.[name]				                                                AS [stats_name]
,		st.[has_filter]			                                                AS [stats_is_filtered]
,       ROW_NUMBER()
        OVER(ORDER BY (SELECT NULL))                                            AS [seq_nmbr]
,								 QUOTENAME(sm.[name])+'.'+QUOTENAME(tb.[name])  AS [two_part_name]
,		QUOTENAME(DB_NAME())+'.'+QUOTENAME(sm.[name])+'.'+QUOTENAME(tb.[name])  AS [three_part_name]
FROM	sys.objects			AS ob
JOIN	sys.stats			AS st	ON	ob.[object_id]		= st.[object_id]
JOIN	sys.stats_columns	AS sc	ON	st.[stats_id]		= sc.[stats_id]
									AND st.[object_id]		= sc.[object_id]
JOIN	sys.columns			AS co	ON	sc.[column_id]		= co.[column_id]
									AND	sc.[object_id]		= co.[object_id]
JOIN	sys.tables			AS tb	ON	co.[object_id]		= tb.[object_id]
JOIN	sys.schemas			AS sm	ON	tb.[schema_id]		= sm.[schema_id]
WHERE	1=1
AND		st.[user_created]   = 1
GROUP BY
		sm.[name]
,		tb.[name]
,		st.[name]
,		st.[filter_definition]
,		st.[has_filter]
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
``` 

>[AZURE.NOTE] `CTAS`는 매우 강력한 명령이며 트랜잭션 로그 공간을 사용한다는 점에서 매우 효율적이라는 추가적인 이점이 있습니다.


## 임시 테이블 삭제

`CREATE TABLE` 문이 정상적으로 수행되려면 세션에 테이블이 존재하지 않는지 확인해야 합니다. 아래 패턴을 사용하여 간단한 사전 존재 여부 확인을 수행할 수 있습니다.

```sql
IF OBJECT_ID('tempdb..#stats_ddl') IS NOT NULL
BEGIN
	DROP TABLE #stats_ddl
END
```

> [AZURE.NOTE] 코딩 일관성을 유지하려면 테이블 및 임시 테이블 모두에 대해 이 패턴을 사용하는 것이 좋습니다.

또한 코드에서 사용을 완료한 경우 `DROP TABLE`을 사용하여 임시 테이블을 제거하는 것이 좋습니다.

```sql
DROP TABLE #stats_ddl
```

저장 프로시저 개발에서는 프로시저 끝에 삭제 명령이 번들로 포함되어 있는지 확인하여 이러한 개체가 정리되는지 알 수 있습니다.

## 코드 모듈화

임시 테이블 사용자 세션의 어디에서나 볼 수 있다는 사실은 응용 프로그램 코드를 모듈화하는 데 실제로 이용될 수 있습니다.

작업 예제를 만들어 봅니다.

아래 저장 프로시저는 위에서 언급한 예제도 포함합니다. 이 코드를 사용하여 데이터베이스의 모든 열에 대한 통계를 업데이트하는 데 필요한 DDL을 생성할 수 있습니다.

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
		sm.[name]				                                                AS [schema_name]
,		tb.[name]				                                                AS [table_name]
,		st.[name]				                                                AS [stats_name]
,		st.[has_filter]			                                                AS [stats_is_filtered]
,       ROW_NUMBER()
        OVER(ORDER BY (SELECT NULL))                                            AS [seq_nmbr]
,								 QUOTENAME(sm.[name])+'.'+QUOTENAME(tb.[name])  AS [two_part_name]
,		QUOTENAME(DB_NAME())+'.'+QUOTENAME(sm.[name])+'.'+QUOTENAME(tb.[name])  AS [three_part_name]
FROM	sys.objects			AS ob
JOIN	sys.stats			AS st	ON	ob.[object_id]		= st.[object_id]
JOIN	sys.stats_columns	AS sc	ON	st.[stats_id]		= sc.[stats_id]
									AND st.[object_id]		= sc.[object_id]
JOIN	sys.columns			AS co	ON	sc.[column_id]		= co.[column_id]
									AND	sc.[object_id]		= co.[object_id]
JOIN	sys.tables			AS tb	ON	co.[object_id]		= tb.[object_id]
JOIN	sys.schemas			AS sm	ON	tb.[schema_id]		= sm.[schema_id]
WHERE	1=1
AND		st.[user_created]   = 1
GROUP BY
		sm.[name]
,		tb.[name]
,		st.[name]
,		st.[filter_definition]
,		st.[has_filter]
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

이 단계에서는 테이블에 아무 작업도 발생하지 않았습니다. 이 프로시저는 단순히 통계를 업데이트하는 데 필요한 DDL을 생성하고 임시 테이블에 해당 코드를 저장했습니다.

그러나 저장 프로시저 끝에 `DROP TABLE` 명령이 포함되어 있지 않습니다. 하지만 코드를 강력하고 반복 가능하게 만들기 위해 저장 프로시저에 사전 존재 여부 확인을 포함했습니다. 따라서 `CTAS`는 세션에 있던 중복된 개체의 결과로 인해 실패하지 않을 것입니다.

이제, 흥미로운 내용을 살펴보겠습니다.

SQL 데이터 웨어하우스에서 임시 테이블을 만든 프로시저의 외부에서 임시 테이블을 사용할 수 있습니다. 이 점이 SQL Server와 다릅니다. 세션 내의 **어디에서나** 임시 테이블을 사용할 수 있습니다.

모듈식 및 관리 가능 코드가 더 많아질 수 있습니다. 아래 예제를 확인하세요.

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

결과 코드는 훨씬 더 간결합니다.

이 기능을 사용하여 인라인 및 다중 문 함수가 대체될 수 있는 경우도 있습니다.

> [AZURE.NOTE] 또한 이 솔루션을 확장할 수 있습니다. 예를 들어 단일 테이블만 업데이트하려는 경우 #stats\_ddl 테이블을 필터링하기만 하면 됩니다.

## 임시 테이블 제한 사항
임시 테이블을 구현하는 경우 SQL 데이터 웨어하우스는 두 가지 제한 사항을 적용합니다.

기본 제한 사항은 다음과 같습니다.

- 전역 임시 테이블은 지원되지 않습니다.
- 임시 테이블에서 뷰를 만들 수 없습니다.

## 다음 단계
더 많은 개발 팁은 [개발 개요][]를 참조하세요.

<!--Image references-->

<!--Article references-->
[개발 개요]: sql-data-warehouse-overview-develop.md

<!--MSDN references-->

<!--Other Web references-->

<!---HONumber=AcomDC_0330_2016-->