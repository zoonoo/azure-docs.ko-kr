<properties
   pageTitle="SQL 데이터 웨어하우스 | Microsoft Azure의 동시성 및 워크로드 관리"
   description="솔루션 개발을 위한 Azure SQL 데이터 웨어하우스의 동시성 및 워크로드 관리를 이해합니다."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="jrowlandjones"
   manager="barbkess"
   editor="jrowlandjones"/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="01/04/2016"
   ms.author="JRJ@BigBangData.co.uk;barbkess"/>

# SQL 데이터 웨어하우스의 동시성 및 워크로드 관리
대규모로 예측 가능한 성능을 제공하기 위해 SQL 데이터 웨어하우스는 계산 리소스 워크로드 동시성과 계산 리소스 할당을 모두 관리할 수 있는 메커니즘을 구현합니다.

이 문서에서는 동시성 및 워크로드 관리 개념을 소개하며, 두 기능 모두 구현된 방법 및 데이터 웨어하우스에서 이들을 제어할 수 있는 방법을 설명합니다.

## 동시성
SQL 데이터 웨어하우스의 동시성은 **동시 쿼리** 및 **동시성 슬롯** 두 개념에 의해 관리된다는 것을 이해해야 합니다.

동시 쿼리는 여러 쿼리를 동시에 실행하는 것과 같습니다. SQL 데이터 웨어하우스는 **동시 쿼리**를 32개까지 지원합니다. 각 쿼리 실행은 직렬 쿼리(단일 스레드)인지 아니면 병렬 쿼리(다중 스레드)인지 여하와 상관없이 단일 쿼리로 계산됩니다. 이는 고정된 값이며 모든 서비스 수준과 모든 쿼리에 적용됩니다.

동시성 슬롯은 더 동적인 개념이며 사용자 데이터 웨어하우스에 대한 데이터 웨어하우스의 단위(DWU) 서비스 수준 목표를 기준으로 합니다. SQL 데이터 웨어하우스에 할당의 DWU 수를 늘리면 더 많은 계산 리소스가 할당됩니다. 그러나 DWU 수를 늘리면 사용 가능한 **동시성 슬롯** 수도 증가합니다.

일반적으로 동시에 실행하는 각 쿼리는 동시성 슬롯을 한 개 이상 사용합니다. 정확한 슬롯 수는 다음 세 가지 요인에 따라 달라집니다.

1. SQL 데이터 웨어하우스에 대한 DWU 설정
2. 사용자가 속한 **리소스 클래스**
3. 쿼리 또는 작업이 동시성 슬롯 모델에 의해 제어되는지 여부 

> [AZURE.NOTE] 모든 쿼리에 동시성 슬롯 쿼리 규칙이 적용되지는 않는다는 점에 주의하세요. 그러나 대부분의 사용자 쿼리에는 적용됩니다. 일부 쿼리 및 작업은 동시성 슬롯을 전혀 사용하지 않습니다. 이러한 쿼리 및 작업은 여전히 동시성 쿼리 제한에 따라 제한되므로 두 규칙을 모두 설명할 필요가 있습니다. 자세한 내용은 아래의 [리소스 클래스 예외](#exceptions) 섹션을 참조하세요.

다음 표에서는 동시 쿼리와 동시성 슬롯 모두의 제한을 설명합니다. 여기서는 쿼리가 리소스에 따라 제한되는 경우를 가정합니다.

<!--
| Concurrency Slot Consumption | DW100 | DW200 | DW300 | DW400 | DW500 | DW600 | DW1000 | DW1200 | DW1500 | DW2000 | DW3000 | DW6000 |
| :--------------------------- | :---- | :---- | :---- | :---- | :---- | :---- | :----- | :----- | :----- | :----- | :----- | :----- |
| Max Concurrent Queries       | 32    | 32    | 32    | 32    | 32    | 32    | 32     | 32     | 32     | 32     | 32     | 32     |
| Max Concurrency Slots        | 4     | 8     | 12    | 16    | 20    | 24    | 40     | 48     | 60     | 80     | 120    | 240    |
-->

| 동시성 슬롯 사용량 | DW100 | DW200 | DW300 | DW400 | DW500 | DW600 | DW1000 | DW1200 | DW1500 | DW2000 |
| :--------------------------- | :---- | :---- | :---- | :---- | :---- | :---- | :----- | :----- | :----- | :----- | 
| 최대 동시 쿼리 수 | 32 | 32 | 32 | 32 | 32 | 32 | 32 | 32 | 32 | 32 | 
| 최대 동시성 슬롯 수 | 4 | 8 | 12 | 16 | 20 | 24 | 40 | 48 | 60 | 80 |

SQL 데이터 웨어하우스 쿼리 작업은 이러한 임계값 내에 있어야 합니다. 동시 쿼리가 32개보다 많거나 동시성 슬롯 수를 초과하면 두 임계값을 모두 만족할 수 있게 될 때까지 쿼리가 큐에 저장됩니다.

## 워크로드 관리

SQL 데이터 웨어하우스는 해당 워크로드 관리 구현의 일부로 **데이터베이스 역할** 형식의 네 가지 리소스 클래스를 표시합니다.

역할은 다음과 같습니다.

- smallrc
- mediumrc
- largerc
- xlargerc

리소스 클래스는 SQL 데이터 웨어하우스 작업 관리에서 필수적인 부분입니다. 그리고 쿼리에 할당된 계산 리소스에 적용됩니다.

기본적으로 각 사용자는 작은 리소스 클래스인 smallrc의 멤버입니다. 그러나 어떤 사용자도 더 높은 리소스 클래스 한 개 이상에 추가될 수 있습니다. 일반적으로 SQL 데이터 웨어하우스는 쿼리 실행을 위해 가장 높은 역할 멤버 자격을 가져옵니다. 더 높은 리소스 클래스에 사용자를 추가하면 해당 사용자에 대한 리소스가 증가하지만 더 많은 동시성 슬롯을 사용하게 되며 동시성에 제한을 받을 수 있습니다. 왜냐하면 한 쿼리에 더 많은 리소스가 할당되므로 시스템이 다른 사용자가 사용하는 리소스를 제한해야 하기 때문입니다. 세상에 공짜란 없는 법입니다.

더 높은 리소스 클래스가 관리하는 가장 중요한 리소스는 메모리입니다. 의미 있는 크기를 가진 대부분의 데이터 웨어하우스 테이블은 클러스터형 columnstore 인덱스를 사용합니다. 이 인덱스는 일반적으로 데이터 웨어하우스에 최고의 성능을 제공하지만 이를 유지하기 위해 메모리를 많이 사용해야 합니다. 인덱스 다시 빌드 같은 데이터 관리 작업에 더 높은 리소스 클래스를 사용하면 매우 유리한 경우가 많습니다.

SQL 데이터 웨어하우스에서는 데이터베이스 역할을 사용하여 리소스 클래스를 구현했습니다. 더 높은 리소스 클래스의 멤버가 되려면 단순히 메모리와 우선 순위를 높이고 위에서 설명한 역할/리소스 클래스 중 하나에 데이터베이스 사용자를 추가하기만 하면 됩니다.

### 리소스 클래스 멤버 자격

`sp_addrolemember` 및 `sp_droprolemember` 프로시저를 사용하여 워크로드 관리 데이터베이스 역할에 대해 자기 자신을 추가 및 제거할 수 있습니다. 참고로 이 작업을 수행하려면 `ALTER ROLE` 사용 권한이 필요합니다. ALTER ROLE DDL 구문은 사용할 수 없습니다. 앞에서 언급한 저장 프로시저를 사용해야 합니다. 로그인 및 사용자를 만드는 방법에 대한 전체 예는 이 문서의 끝에 있는 [managing users)[#managing-users] 섹션에 제공됩니다.

> [AZURE.NOTE] 워크로드 관리 그룹에 대해 사용자를 추가하기보다는 더 높은 리소스 클래스에 영구적으로 할당된 별도 로그인/사용자를 통해 더 집약적인 작업을 시작하는 것이 더 간단한 경우가 많습니다.

### 메모리 할당

다음 테이블은 각 쿼리에 사용할 수 있는 메모리 증가 및 이를 실행하는 사용자에게 적용되는 리소스 클래스를 자세히 설명합니다.

<!--
| Memory Available (per dist) | Priority | DW100  | DW200  | DW300  | DW400   | DW500   | DW600   | DW1000  | DW1200  | DW1500  | DW2000  | DW3000  | DW6000   |
| :-------------------------- | :------- | :----  | :----- | :----- | :------ | :------ | :------ | :------ | :------ | :------ | :------ | :------ | :------- |
| smallrc(default) (s)        | Medium   | 100 MB | 100 MB | 100 MB | 100  MB | 100 MB  | 100 MB  | 100 MB  | 100 MB  | 100 MB  | 100 MB  | 100  MB | 100   MB |
| mediumrc (m)                | Medium   | 100 MB | 200 MB | 200 MB | 400  MB | 400 MB  | 400 MB  | 800 MB  | 800 MB  | 800 MB  | 1600 MB | 1600 MB | 3200  MB |
| largerc (l)                 | High     | 200 MB | 400 MB | 400 MB | 800  MB | 800 MB  | 800 MB  | 1600 MB | 1600 MB | 1600 MB | 3200 MB | 3200 MB | 6400  MB |
| xlargerc (xl)               | High     | 400 MB | 800 MB | 800 MB | 1600 MB | 1600 MB | 1600 MB | 3200 MB | 3200 MB | 3200 MB | 6400 MB | 6400 MB | 12800 MB |
-->

<!--
| Memory Available (per dist) | Priority | DW100  | DW200  | DW300  | DW400   | DW500   | DW600   | DW1000  | DW1200  | DW1500  | DW2000  |
| :-------------------------- | :------- | :----  | :----- | :----- | :------ | :------ | :------ | :------ | :------ | :------ | :------ |
| smallrc(default) (s)        | Medium   | 100 MB | 100 MB | 100 MB | 100  MB | 100 MB  | 100 MB  | 100 MB  | 100 MB  | 100 MB  | 100 MB  |
| mediumrc (m)                | Medium   | 100 MB | 200 MB | 200 MB | 400  MB | 400 MB  | 400 MB  | 800 MB  | 800 MB  | 800 MB  | 1600 MB |
| largerc (l)                 | High     | 200 MB | 400 MB | 400 MB | 800  MB | 800 MB  | 800 MB  | 1600 MB | 1600 MB | 1600 MB | 3200 MB |
| xlargerc (xl)               | High     | 400 MB | 800 MB | 800 MB | 1600 MB | 1600 MB | 1600 MB | 3200 MB | 3200 MB | 3200 MB | 6400 MB |
-->

| 사용 가능한 메모리(dist당) | DW100 | DW200 | DW300 | DW400 | DW500 | DW600 | DW1000 | DW1200 | DW1500 | DW2000 |
| :-------------------------- | :----  | :----- | :----- | :------ | :------ | :------ | :------ | :------ | :------ | :------ |
| smallrc(기본값) (s) | 100 MB | 100 MB | 100 MB | 100 MB | 100 MB | 100 MB | 100 MB | 100 MB | 100 MB | 100 MB |
| mediumrc(m) | 100 MB | 200 MB | 200 MB | 400 MB | 400 MB | 400 MB | 800 MB | 800 MB | 800 MB | 1600 MB |
| largerc(l) | 200 MB | 400 MB | 400 MB | 800 MB | 800 MB | 800 MB | 1600 MB | 1600 MB | 1600 MB | 3200 MB |
| xlargerc(xl) | 400 MB | 800 MB | 800 MB | 1600 MB | 1600 MB | 1600 MB | 3200 MB | 3200 MB | 3200 MB | 6400 MB |

### 동시성 슬롯 사용량

또한 위에서 설명했듯이 사용자에게 할당된 리소스 클래스가 높을수록 동시 슬롯 사용량이 더 많습니다. 다음 테이블은 지정된 리소스 클래스의 쿼리당 동시성 슬롯 사용량을 설명합니다.

<!--
| Consumption | DW100 | DW200 | DW300 | DW400 | DW500 | DW600 | DW1000 | DW1200 | DW1500 | DW2000 | DW3000 | DW6000 |
| :--------------------------- | :---- | :---- | :---- | :---- | :---- | :---- | :----- | :----- | :----- | :----- | :----- | :----- |
| Max Concurrent Queries       | 32    | 32    | 32    | 32    | 32    | 32    | 32     | 32     | 32     | 32     | 32     | 32     |
| Max Concurrency Slots        | 4     | 8     | 12    | 16    | 20    | 24    | 40     | 48     | 60     | 80     | 120    | 240    |
| smallrc(default) (s)         | 1     | 1     | 1     | 1     | 1     | 1     | 1      | 1      | 1      | 1      | 1      | 1      |
| mediumrc (m)                 | 1     | 2     | 2     | 4     | 4     | 4     | 8      | 8      | 8      | 16     | 16     | 32     |
| largerc (l)                  | 2     | 4     | 4     | 8     | 8     | 8     | 16     | 16     | 16     | 32     | 32     | 64     |
| xlargerc (xl)                | 4     | 8     | 8     | 16    | 16    | 16    | 32     | 32     | 32     | 64     | 64     | 128    |
-->

| 사용량 과금 | DW100 | DW200 | DW300 | DW400 | DW500 | DW600 | DW1000 | DW1200 | DW1500 | DW2000 |
| :--------------------------- | :---- | :---- | :---- | :---- | :---- | :---- | :----- | :----- | :----- | :----- |
| 최대 동시 쿼리 수 | 32 | 32 | 32 | 32 | 32 | 32 | 32 | 32 | 32 | 32 |
| 최대 동시성 슬롯 수 | 4 | 8 | 12 | 16 | 20 | 24 | 40 | 48 | 60 | 80 |
| smallrc(기본값) (s) | 1 | 1 | 1 | 1 | 1 | 1 | 1 | 1 | 1 | 1 |
| mediumrc(m) | 1 | 2 | 2 | 4 | 4 | 4 | 8 | 8 | 8 | 16 |
| largerc(l) | 2 | 4 | 4 | 8 | 8 | 8 | 16 | 16 | 16 | 32 |
| xlargerc(xl) | 4 | 8 | 8 | 16 | 16 | 16 | 32 | 32 | 32 | 64 |

### 예외

더 높은 리소스 클래스에 의해 쿼리 또는 작업에 할당된 리소스가 변경되지 않는 경우도 있습니다. 일반적으로 작업을 수행하는 데 필요한 리소스가 부족한 경우에 이 문제가 발생합니다. 이러한 경우에는 사용자에게 할당된 리소스 클래스와 상관없이 기본 또는 작은 리소스 클래스(smallrc)가 항상 사용됩니다. 한 예로, `CREATE LOGIN`은 항상 smallrc에서 실행됩니다. 이 작업을 수행하는 데 필요한 리소스가 매우 부족하고 따라서 동시성 슬롯 모델에 쿼리를 포함하는 것이 적합하지 않습니다. 이 작업에 대량의 메모리를 미리 할당하는 것은 낭비입니다. 동시성 슬롯 모델에서 `CREATE LOGIN`을 제외하면 SQL 데이터 웨어하우스의 효율을 훨씬 높일 수 있습니다.

다음은 리소스 클래스에 의해 **제어되는** 문 및 작업의 목록입니다.

- INSERT-SELECT
- UPDATE
- 삭제
- SELECT(사용자 테이블을 쿼리하는 경우)
- ALTER INDEX REBUILD
- ALTER INDEX REORGANIZE
- ALTER TABLE REBUILD
- CREATE INDEX
- CREATE CLUSTERED COLUMNSTORE INDEX
- CREATE TABLE AS SELECT 
- 데이터 로드 
- DMS(데이터 이동 서비스)에서 수행하는 데이터 이동 작업

다음 문은 리소스 클래스를 인식하지 **않습니다**.

- CREATE TABLE
- ALTER TABLE ... SWITCH PARTITION 
- ALTER TABLE ... SPLIT PARTITION 
- ALTER TABLE ... MERGE PARTITION 
- DROP TABLE
- ALTER INDEX DISABLE
- DROP INDEX
- CREATE STATISTICS
- UPDATE STATISTICS
- DROP STATISTICS
- TRUNCATE TABLE
- ALTER AUTHORIZATION
- CREATE LOGIN
- CREATE USER
- ALTER USER
- DROP USER
- CREATE PROCEDURE
- ALTER PROCEDURE
- DROP PROCEDURE
- CREATE VIEW
- DROP VIEW
- INSERT VALUES
- SELECT(시스템 뷰와 DMV에서)
- EXPLAIN
- DBCC

<!--
Removed as these two are not confirmed / supported under SQLDW
- CREATE REMOTE TABLE AS SELECT
- CREATE EXTERNAL TABLE AS SELECT
- REDISTRIBUTE 
-->
> [AZURE.NOTE] 동적 관리 뷰 및 카탈로그 뷰에 대해 단독으로 실행되는 `SELECT` 쿼리가 리소스 클래스에서 제어되지 **않는다는** 점에 대해 자세히 알아보는 것도 중요합니다.

대부분의 최종 사용자 쿼리가 리소스 클래스에 의해 제어될 가능성이 크다는 점에 주의해야 합니다. 플랫폼에서 별도로 제외되지 않은 한, 일반적인 규칙은 활성 쿼리 작업이 동시 쿼리 및 동시성 슬롯 임계값 모두에 맞아야 한다는 것입니다. 최종 사용자는 동시성 슬롯 모델에서 쿼리를 제외하도록 선택할 수 없습니다. 하나 이상의 임계값을 초과하면 쿼리가 큐에 저장되기 시작합니다. 큐에 저장된 쿼리는 제출 시간이 지난 후 우선 순위에 따라 해결됩니다.

### 내부 구조 

SQL 데이터 웨어하우스 내부의 작업 관리는 약간 더 복잡합니다. 리소스 클래스는 리소스 관리자 내에서 워크로드 관리 그룹의 일반 집합에 동적으로 매핑됩니다. 사용되는 그룹은 웨어하우스에 대한 DWU 값에 따라 달라집니다. 하지만 SQL 데이터 웨어하우스에 사용되는 총 8개의 워크로드 그룹이 있습니다. 아래에 이 계정과 키의 예제가 나와 있습니다.

- SloDWGroupC00
- SloDWGroupC01
- SloDWGroupC02
- SloDWGroupC03
- SloDWGroupC04
- SloDWGroupC05
- SloDWGroupC06
- SloDWGroupC07

이러한 8개 그룹은 동시성 슬롯 사용량에 매핑됩니다.

| 워크로드 그룹 | 동시성 슬롯 매핑 | 우선 순위 매핑 |
| :------------  | :----------------------- | :--------------- |
| SloDWGroupC00 | 1 | 중간 |
| SloDWGroupC01 | 2 | 중간 |
| SloDWGroupC02 | 4 | 중간 |
| SloDWGroupC03 | 8 | 중간 |
| SloDWGroupC04 | 16 | 높음 |
| SloDWGroupC05 | 32 | 높음 |
| SloDWGroupC06 | 64 | 높음 |
| SloDWGroupC07 | 128 | 높음 |

따라서 예를 들어, DW500이 SQL 데이터 웨어하우스에 대한 현재 DWU 설정인 경우 활성 워크로드 그룹이 다음과 같이 리소스 클래스에 매핑됩니다.

| 리소스 클래스 | 워크로드 그룹 | 사용된 동시성 슬롯 수 | 중요도 |
| :------------- | :------------- | :---------------------   | :--------- |
| smallrc | SloDWGroupC00 | 1 | 중간 |
| mediumrc | SloDWGroupC02 | 4 | 중간 |
| largerc | SloDWGroupC03 | 8 | 중간 |
| xlargerc | SloDWGroupC04 | 16 | 높음 |

리소스 관리자의 관점에서 메모리 리소스 할당의 차이점을 자세히 살펴보려면 다음 쿼리를 사용합니다.

```
WITH rg
AS
(   SELECT  pn.name									AS node_name
	,		pn.[type]								AS node_type
	,		pn.pdw_node_id							AS node_id
	,		rp.name									AS pool_name
    ,       rp.max_memory_kb*1.0/1024				AS pool_max_mem_MB
    ,       wg.name									AS group_name
    ,       wg.importance							AS group_importance
    ,       wg.request_max_memory_grant_percent		AS group_request_max_memory_grant_pcnt
    ,       wg.max_dop								AS group_max_dop
    ,       wg.effective_max_dop					AS group_effective_max_dop
	,		wg.total_request_count					AS group_total_request_count
	,		wg.total_queued_request_count			AS group_total_queued_request_count
	,		wg.active_request_count					AS group_active_request_count
	,		wg.queued_request_count					AS group_queued_request_count
    FROM    sys.dm_pdw_nodes_resource_governor_workload_groups wg
    JOIN    sys.dm_pdw_nodes_resource_governor_resource_pools rp    ON  wg.pdw_node_id  = rp.pdw_node_id
															        AND wg.pool_id      = rp.pool_id
	JOIN	sys.dm_pdw_nodes pn										ON	wg.pdw_node_id	= pn.pdw_node_id
	WHERE   wg.name like 'SloDWGroup%'
	AND     rp.name = 'SloDWPool'
) 
SELECT	pool_name
,		pool_max_mem_MB
,		group_name
,		group_importance
,		(pool_max_mem_MB/100)*group_request_max_memory_grant_pcnt AS max_memory_grant_MB
,		node_name
,		node_type
,       group_total_request_count
,       group_total_queued_request_count
,       group_active_request_count
,       group_queued_request_count
FROM	rg
ORDER BY 
	node_name
,	group_request_max_memory_grant_pcnt
,	group_importance
;
```

> [AZURE.NOTE] 위의 쿼리는 문제 해결 시 워크로드 그룹의 활성 및 사용 기록을 분석하는 데도 사용할 수 있습니다.

## 워크로드 관리 예제

이 섹션에서는 사용자를 관리하고 대기하는 쿼리를 검색하는 작업에 대해 몇 가지 추가 예를 제공합니다.

### 사용자 관리

사용자에게 SQL 데이터 웨어하우스에 대한 액세스 권한을 부여하려면 먼저 로그인해야 합니다.

SQL 데이터 웨어하우스의 마스터 데이터베이스에 대한 연결을 열고 다음 명령을 실행합니다.

```
CREATE LOGIN newperson WITH PASSWORD = 'mypassword'

CREATE USER newperson for LOGIN newperson
```

[AZURE.NOTE] Azure SQL 데이터베이스 및 SQL 데이터 웨어하우스 모두로 작업할 경우 마스터 데이터베이스에서 로그인을 위한 사용자를 만드는 것도 좋은 방법입니다. 멤버 자격을 부여하기 위해 로그인 시 마스터 수준의 사용자가 필요할 때 사용할 수 있는 두 가지 서버 역할이 있습니다. 역할은 `Loginmanager` 및 `dbmanager`입니다. Azure SQL 데이터베이스 및 SQL 데이터 웨어하우스 모두에서 이러한 역할은 로그인을 관리하고 데이터베이스를 만들 권한을 부여합니다. 이 점이 SQL Server와 다릅니다. 자세한 내용은 [Azure SQL 데이터베이스에서 데이터베이스 및 로그인 관리]를 참조하세요.

로그인을 만든 후에는 이제 사용자 계정을 추가해야 합니다.

SQL 데이터 웨어하우스 데이터베이스에 대한 연결을 열고 다음 명령을 실행합니다.

```
CREATE USER newperson FOR LOGIN newperson
```

사용자에게 전체 권한을 부여해야 합니다. 아래 예제에서는 SQL 데이터 웨어하우스 데이터베이스에 대한 `CONTROL` 권한을 부여합니다. 데이터베이스 수준의 `CONTROL` 권한은 SQL Server의 db\_owner에 해당합니다.

```
GRANT CONTROL ON DATABASE::MySQLDW to newperson
```

워크로드 관리 역할을 보려면 다음 쿼리를 사용합니다.

```
SELECT  ro.[name]           AS [db_role_name]
FROM    sys.database_principals ro
WHERE   ro.[type_desc]      = 'DATABASE_ROLE'
AND     ro.[is_fixed_role]  = 0
;
```

증가하는 워크로드 관리 역할에 사용자를 추가하려면 다음 쿼리를 사용합니다.

``` 
EXEC sp_addrolemember 'largerc', 'newperson' 
```

워크로드 관리 역할에서 사용자를 제거하려면 다음 쿼리를 사용합니다.

``` 
EXEC sp_droprolemember 'largerc', 'newperson' 
```

> [AZURE.NOTE] smallrc에서 사용자를 제거하는 것은 불가능합니다.

어떤 사용자가 지정된 역할의 멤버인지 보려면 다음 쿼리를 사용합니다.

```
SELECT	r.name AS role_principal_name
,		m.name AS member_principal_name
FROM	sys.database_role_members rm
JOIN	sys.database_principals AS r			ON rm.role_principal_id		= r.principal_id
JOIN	sys.database_principals AS m			ON rm.member_principal_id	= m.principal_id
WHERE	r.name IN ('mediumrc','largerc', 'xlargerc')
;
```

### 큐에 저장된 쿼리 검색
동시성 큐에 저장된 쿼리를 식별하려면 언제나 `sys.dm_pdw_exec_requests` DMV를 참조할 수 있습니다.

```
SELECT 	 r.[request_id]									AS Request_ID
		,r.[status]										AS Request_Status
		,r.[submit_time]								AS Request_SubmitTime
		,r.[start_time]									AS Request_StartTime
        ,DATEDIFF(ms,[submit_time],[start_time])		AS Request_InitiateDuration_ms
        ,r.resource_class                               AS Request_resource_class
FROM    sys.dm_pdw_exec_requests r
;
```

SQL 데이터 웨어하우스에는 동시성을 측정하기 위한 특정 대기 유형이 있습니다.

아래에 이 계정과 키의 예제가 나와 있습니다.
 
- LocalQueriesConcurrencyResourceType
- UserConcurrencyResourceType
- DmsConcurrencyResourceType
- BackupConcurrencyResourceType

LocalQueriesConcurrencyResourceType은 동시성 슬롯 프레임워크 외부에 존재하는 쿼리를 참조합니다. `SELECT @@VERSION`과 같은 DMV 쿼리 및 시스템 함수는 로컬 쿼리의 예입니다.

UserConcurrencyResourceType은 동시성 슬롯 프레임워크 내에 존재하는 쿼리를 참조합니다. 최종 사용자 테이블에 대한 쿼리는 이 리소스 유형을 사용할 수 있는 예를 나타냅니다.

DmsConcurrencyResourceType은 데이터 이동 작업으로 초래된 대기를 참조합니다.

데이터베이스가 백업될 때 BackupConcurrencyResourceType을 볼 수 있습니다. 이 리소스 유형에 대한 최대값은 1입니다. 여러 백업을 동시에 요청한 경우 다른 백업 요청은 큐에 저장됩니다.


현재 큐에 저장된 쿼리를 분석하여 요청 대기 중인 리소스를 알아내려면 `sys.dm_pdw_waits` DMV를 참조하세요.

```
SELECT  w.[wait_id]
,       w.[session_id]
,       w.[type]											AS Wait_type
,       w.[object_type]
,       w.[object_name]
,       w.[request_id]
,       w.[request_time]
,       w.[acquire_time]
,       w.[state]
,       w.[priority]
,		SESSION_ID()										AS Current_session
,		s.[status]											AS Session_status
,		s.[login_name]
,		s.[query_count]
,		s.[client_id]
,		s.[sql_spid]
,		r.[command]											AS Request_command
,		r.[label]
,		r.[status]											AS Request_status
,		r.[submit_time]
,		r.[start_time]
,		r.[end_compile_time]
,		r.[end_time]
,		DATEDIFF(ms,r.[submit_time],r.[start_time])			AS Request_queue_time_ms
,		DATEDIFF(ms,r.[start_time],r.[end_compile_time])	AS Request_compile_time_ms
,		DATEDIFF(ms,r.[end_compile_time],r.[end_time])		AS Request_execution_time_ms
,		r.[total_elapsed_time]
FROM    sys.dm_pdw_waits w
JOIN    sys.dm_pdw_exec_sessions s  ON w.[session_id] = s.[session_id]
JOIN    sys.dm_pdw_exec_requests r  ON w.[request_id] = r.[request_id]
WHERE	w.[session_id] <> SESSION_ID()
;
```

지정된 쿼리가 사용하는 리소스 대기만 보려면 `sys.dm_pdw_resource_waits` DMV를 참조하세요. 리소스 대기 시간은 기본 SQL Server가 CPU에 대해 쿼리를 예약하는 데 소요되는 대기 시간을 나타내는 것과 달리 리소스가 제공될 때까지 대기하는 시간만 측정합니다.

```
SELECT  [session_id]
,       [type]
,       [object_type]
,       [object_name]
,       [request_id]
,       [request_time]
,       [acquire_time]
,       DATEDIFF(ms,[request_time],[acquire_time])  AS acquire_duration_ms
,       [concurrency_slots_used]                    AS concurrency_slots_reserved
,       [resource_class]
,       [wait_id]                                   AS queue_position
FROM    sys.dm_pdw_resource_waits
WHERE	[session_id] <> SESSION_ID()
;
```

마지막으로 대기에 대한 기록 추세 분석을 위해 SQL 데이터 웨어하우스는 `sys.dm_pdw_wait_stats` DMV를 제공합니다.

```
SELECT	w.[pdw_node_id]
,		w.[wait_name]
,		w.[max_wait_time]
,		w.[request_count]
,		w.[signal_time]
,		w.[completed_count]
,		w.[wait_time]
FROM	sys.dm_pdw_wait_stats w
;
```

## 다음 단계
더 많은 개발 팁은 [개발 개요][]를 참조하세요.

<!--Image references-->

<!--Article references-->
[개발 개요]: sql-data-warehouse-overview-develop.md

<!--MSDN references-->
[Azure SQL 데이터베이스에서 데이터베이스 및 로그인 관리]: https://msdn.microsoft.com/library/azure/ee336235.aspx

<!--Other Web references-->

<!---HONumber=AcomDC_0107_2016-->