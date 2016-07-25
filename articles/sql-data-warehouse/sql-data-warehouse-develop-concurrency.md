<properties
   pageTitle="SQL 데이터 웨어하우스 | Microsoft Azure의 동시성 및 워크로드 관리"
   description="솔루션 개발을 위한 Azure SQL 데이터 웨어하우스의 동시성 및 워크로드 관리를 이해합니다."
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
   ms.date="07/12/2016"
   ms.author="jrj;barbkess;sonyama"/>

# SQL 데이터 웨어하우스의 동시성 및 워크로드 관리

사용자는 예측 가능한 성능을 대규모로 제공하기 위해 SQL 데이터 웨어하우스를 사용하여 메모리 및 CPU 우선 순위 지정과 같은 리소스 할당 뿐 아니라 동시성 수준을 제어할 수 있습니다. 이 문서에서는 동시성 및 워크로드 관리 개념을 소개하며, 두 기능 모두 구현된 방법 및 데이터 웨어하우스에서 이들을 제어할 수 있는 방법을 설명합니다. SQL 데이터 웨어하우스 워크로드 관리는 다중 사용자 환경을 지원합니다. 다중 테넌트 워크로드용은 아닙니다.

## 동시성 한도

SQL 데이터 웨어하우스를 사용하여 최대 1,024개의 동시 연결을 할 수 있습니다. 1,024개의 모든 연결을 통해 동시에 쿼리를 제출할 수 있습니다. 그러나 처리량을 최적화하기 위해 SQL 데이터 웨어하우스는 각 쿼리에 최소 메모리 부여를 위해 일부 쿼리를 큐에 대기하게 할 수 있습니다. 쿼리 실행 시 동시성 한도에 이른 경우 큐에 대기하기가 발생합니다. SQL 데이터 웨어하우스는 동시성 한도에 이른 경우 쿼리를 큐에 대기하도록 하여 활성 쿼리가 긴급하게 필요한 메모리 리소스에 액세스할 수 있게 하여 총 처리량을 늘릴 수 있습니다.

동시성 한도는 **동시 쿼리 수** 및 **동시성 슬롯 수**라는 두 개념에 의해 제어됩니다. 쿼리가 실행하려면 동시성 한도 및 동시성 슬롯 할당 하에서 실행해야 합니다.

- **동시 쿼리 수**란 간단히 말해 동시에 실행하는 쿼리 수입니다. SQL 데이터 웨어하우스는 더 큰 DW 크기인, DW1000이상에서 최대 32개의 **동시 쿼리**를 지원합니다. 그러나 동시 쿼리 수는 DWU의 수에 따라 달라짐으로 DWU에 따른 한도를 보여주기 위해 테이블을 제공했습니다.
- **동시성 슬롯**은 더 동적인 개념입니다. 동시에 실행하는 각 쿼리는 동시성 슬롯을 한 개 이상 사용합니다. 쿼리가 사용하는 정확한 슬롯 수는 SQL 데이터 웨어하우스의 크기와 쿼리의 [리소스 클래스](#resource-classes)에 따라 달라집니다.

아래 표에 동시 쿼리와 동시성 슬롯의 한도가 나와 있습니다.

### 동시성 한도

| | DW100 | DW200 | DW300 | DW400 | DW500 | DW600 | DW1000 | DW1200 | DW1500 | DW2000 | DW3000 | DW6000 |
| :--------------------------- | ----: | ----: | ----: | ----: | ----: | ----: | -----: | -----: | -----: | -----: | -----: | -----: |
| 최대 동시 쿼리 수 | 32 | 32 | 32 | 32 | 32 | 32 | 32 | 32 | 32 | 32 | 32 | 32 |
| 최대 동시성 슬롯 수 | 4 | 8 | 12 | 16 | 20 | 24 | 40 | 48 | 60 | 80 | 120 | 240 |

이러한 임계값 중 하나가 충족되면, 새로운 쿼리는 큐에 대기합니다. 큐에 대기 중인 쿼리는 쿼리가 완료되고 쿼리 수와 슬롯이 한도 밑으로 떨어질 때 선입 선출 방식으로 실행됩니다.

> [AZURE.NOTE]  DMV(동적 관리 뷰)에서 단독으로 실행하는 SELECT 쿼리 또는 카탈로그 뷰는 리소스 클래스에 의해 제어되지 **않**습니다. 이렇게 하면 사용자는 모든 동시성 슬롯이 사용 중이더라도 시스템을 모니터링할 수 있습니다.

## 리소스 클래스

리소스 클래스는 더 많은 메모리 또는 CPU 주기가 지정된 사용자에 의해 실행되는 쿼리에 할당되도록 할 수 있기 때문에 SQL 데이터 웨어하우스 워크로드 관리의 핵심적인 부분입니다. **데이터베이스 역할**의 형태로 된 네 가지 리소스 클래스가 있습니다. 네 가지 리소스 클래스에는 **smallrc, mediumrc, largerc 및 xlargerc**가 있습니다. Smallrc의 사용자에게 더 작은 메모리 양이 주어져 더 높은 동시성을 허용합니다. 반면, xlargerc에 할당된 사용자는 많은 양의 메모리가 주어져 더 적은 쿼리가 동시에 실행될 수 있습니다.

더 큰 메모리 할당에서 유용하지 않아 리소스 클래스 할당을 무시하고 대신 작은 리소스 클래스에서 실행하는 몇가지 쿼리 유형이 있습니다. 이러한 쿼리가 항상 작은 리소스 클래스에서 실행되도록 강제한다면 동시성 슬롯이 부족할 때에도 실행할 수 있으며 필요 이상으로 많은 슬롯을 사용하는 것을 방지할 수 있습니다. 이러한 [리소스 클래스 예외](#resource-class-exceptions)는 이 문서의 뒷부분에 나와 있습니다.

기본적으로 각 사용자는 작은 리소스 클래스인 smallrc의 멤버입니다. 프로시저 `sp_addrolemember`은 리소스 클래스를 늘리는 데 사용되고 `sp_droprolemember`는 리소스 클래스를 줄이는 데 사용 됩니다. 예를 들어 이 명령은 loaduser의 리소스 클래스를 largerc로 늘립니다.

```sql
EXEC sp_addrolemember 'largerc', 'loaduser'
```

사용자의 리소스 클래스를 변경하기 보다는 리소스 클래스에 영구적으로 할당되는 사용자를 만드는 것이 좋습니다. 예를 들어 클러스터형 Columnstore 테이블에 로드는 더 많은 메모리를 할당하면 더 높은 품질의 인덱스를 만듭니다. 로드가 더 높은 메모리에 액세스할 수 있도록 하려면 특별히 데이타 로드에 대한 사용자를 만들고 이 사용자를 더 높은 리소스 클래스에 영구적으로 할당합니다.

리소스 클래스에 대한 추가적인 몇 가지 자세한 정보.

- 사용자의 리소스 클래스를 변경하려면 `ALTER ROLE` 권한이 필요합니다.
- 사용자가 하나 이상의 더 높은 리소스 클래스에 추가될 수 있는 반면, 사용자는 할당된 가장 높은 리소스 클래스의 특성을 갖게 됩니다. 즉, 사용자가 mediumrc와 largerc에 할당된다면, 더 높은 리소스 클래스인 largerc가 적용될 리소스 클래스입니다.
- 시스템 관리 사용자의 리소스 클래스는 변경할 수 없습니다.
 
사용자를 만들고 리소스 클래스에 지정하는 것에 대한 자세한 정보 및 예제는 이 문서의 끝에 있는 [사용자 관리](#Managing-users) 섹션에서 찾을 수 있습니다.

## 메모리 할당

사용자의 리소스 클래스를 늘리는 데 따른 장단점이 있습니다. 사용자에 대한 리소스 클래스를 늘리면 쿼리가 더 많은 메모리에 액세스하여 더 빠르게 실행될 수도 있는 한편 실행될 수 있는 동시 쿼리 수는 줄입니다. 이는 많은 양의 메모리를 단일 쿼리에 할당하는 것과 또한 메모리 할당을 필요로 하는 다른 동시 쿼리를을 허용하는 것 사이의 절충입니다. 쿼리에 대해 더 많은 메모리가 한 명의 사용자에게 주어질 경우 다른 사용자는 쿼리를 실행하는 데 사용할 메모리를 갖지 못합니다.

다음 표는 DWU 및 리소스 클래스에 의해 각 배포에 할당된 메모리를 매핑합니다. SQL 데이터 웨어하우스에는 데이터베이스당 배포가 60개 있으므로 xlarge 리소스 클래스의 DW2000에서 실행하는 쿼리는 각 데이터베이스 내에서 6,400 Mb에 액세스하게 될 것입니다.

### 배포 당 메모리 할당(MB)

| | DW100 | DW200 | DW300 | DW400 | DW500 | DW600 | DW1000 | DW1200 | DW1500 | DW2000 | DW3000 | DW6000 |
| :------------- | ----: | ----: | ----: | ----: | ----: | ----: | -----: | -----: | -----: | -----: | -----: | -----: |
| smallrc | 100 | 100 | 100 | 100 | 100 | 100 | 100 | 100 | 100 | 100 | 100 | 100 |
| mediumrc | 100 | 200 | 200 | 400 | 400 | 400 | 800 | 800 | 800 | 1,600 | 1,600 | 3,200 |
| largerc | 200 | 400 | 400 | 800 | 800 | 800 | 1,600 | 1,600 | 1,600 | 3,200 | 3,200 | 6,400 |
| xlargerc | 400 | 800 | 800 | 1,600 | 1,600 | 1,600 | 3,200 | 3,200 | 3,200 | 6,400 | 6,400 | 12,800 |


위와 동일한 예제를 사용하여 xlarge 리소스 클래스의 DW2000에서 실행하는 시스템 전반 쿼리에 총 메모리의 375 GB(6,400 mb * 60 배포 / 1,024 Gb로 변환)가 할당됩니다.

### 시스템 전반 메모리 할당(GB)

| | DW100 | DW200 | DW300 | DW400 | DW500 | DW600 | DW1000 | DW1200 | DW1500 | DW2000 | DW3000 | DW6000 |
| :------------- | ----: | ----: | ----: | ----: | ----: | ----: | -----: | -----: | -----: | -----: | -----: | -----: |
|smallrc | 6 | 6 | 6 | 6 | 6 | 6 | 6 | 6 | 6 | 6 | 6 | 6 |
|mediumrc | 6 | 12 | 12 | 23 | 23 | 23 | 47 | 47 | 47 | 94 | 94 | 188 |
|largerc | 12 | 23 | 23 | 47 | 47 | 47 | 94 | 94 | 94 | 188 | 188 | 375 |
|xlargerc | 23 | 47 | 47 | 94 | 94 | 94 | 188 | 188 | 188 | 375 | 375 | 750 |

## 동시성 슬롯 사용량

위에서 설명했듯이 리소스 클래스가 높을수록 메모리가 더 많이 부여됩니다. 메모리는 고정된 리소스이므로 쿼리 당 메모리를 더 많이 할당할수록 지원할 수 있는 동시성은 줄어듭니다. 다음 표는 DWU가 사용할 수 있는 동시성 슬롯 수는 물론 각 리소스 클래스가 사용하는 슬롯을 반복합니다.

### 동시성 슬롯의 할당 및 사용량

| | DW100 | DW200 | DW300 | DW400 | DW500 | DW600 | DW1000 | DW1200 | DW1500 | DW2000 | DW3000 | DW6000 |
| :---------------------- | ----: | ----: | ----: | ----: | ----: | ----: | -----: | -----: | -----: | -----: | -----: | -----: |
| **할당** | | | | | | | | | | | | |
| 최대 동시 쿼리 수 | 32 | 32 | 32 | 32 | 32 | 32 | 32 | 32 | 32 | 32 | 32 | 32 |
| 최대 동시성 슬롯 수 | 4 | 8 | 12 | 16 | 20 | 24 | 40 | 48 | 60 | 80 | 80 | 80 |
| **슬롯 사용량** | | | | | | | | | | | | |
| smallrc | 1 | 1 | 1 | 1 | 1 | 1 | 1 | 1 | 1 | 1 | 1 | 1 |
| mediumrc | 1 | 2 | 2 | 4 | 4 | 4 | 8 | 8 | 8 | 16 | 16 | 32 |
| largerc | 2 | 4 | 4 | 8 | 8 | 8 | 16 | 16 | 16 | 32 | 32 | 64 |
| xlargerc | 4 | 8 | 8 | 16 | 16 | 16 | 32 | 32 | 32 | 64 | 64 | 128 |

이 표를 통해 쿼리 DW100으로 실행되는 SQL 데이터 웨어하우스는 4개의 동시 smallrc 쿼리 또는 2개의 동시 largerc 쿼리를 허용함을 볼 수 있습니다.

## 쿼리 중요도

내부적으로 리소스 클래스의 동작을 제어하는 총 8개의 워크로드 그룹이 있습니다. 그러나 워크로드 그룹 8개 중 4개 만이 모든 지정된 DWU에서 활용됩니다. 각 워크로드 그룹은 smallrc, mediumrc, largerc, 또는 xlargerc에 할당되기 때문에 이렇게 하는 것이 합리적입니다. 이들 배후 워크로드 그룹을 이해하는 것이 중요한 점은 이들 워크로드 그룹의 일부가 더 높은 **중요도**에 설정된다는 점입니다. 중요도가 CPU 예약에 사용됩니다. 높은 중요도를 갖고 실행되는 쿼리는 중간 중요도를 가진 것보다 3X 더 많은 CPU 주기를 받게 될 것입니다. 따라서 동시성 슬롯 매핑은 또한 CPU 중요도를 결정합니다. 쿼리가 16개 이상의 슬롯을 사용할 경우 중요도 높음으로 실행됩니다.

각 워크로드 그룹에 대한 중요도 매핑은 다음과 같습니다.

| 워크로드 그룹 | 동시성 슬롯 매핑 | 중요도 매핑 |
| :------------------  | :----------------------: | :----------------- |
| SloDWGroupC00 | 1 | 중간 |
| SloDWGroupC01 | 2 | 중간 |
| SloDWGroupC02 | 4 | 중간 |
| SloDWGroupC03 | 8 | 중간 |
| SloDWGroupC04 | 16 | 높음 |
| SloDWGroupC05 | 32 | 높음 |
| SloDWGroupC06 | 64 | 높음 |
| SloDWGroupC07 | 128 | 높음 |

DW500 SQL 데이터 웨어하우스의 경우 활성 워크로드 그룹이 다음과 같이 리소스 클래스에 매핑됩니다.

| 리소스 클래스 | 워크로드 그룹 | 사용된 동시성 슬롯 수 | 중요도 |
| :--------------- | :------------- | :--------------------: | :--------- |
| smallrc | SloDWGroupC00 | 1 | 중간 |
| mediumrc | SloDWGroupC02 | 4 | 중간 |
| largerc | SloDWGroupC03 | 8 | 중간 |
| xlargerc | SloDWGroupC04 | 16 | 높음 |


다음 DMV 쿼리는 리소스 관리자의 관점에서 메모리 리소스 할당의 차이점을 자세히 살펴보거나, 또는 문제를 해결할 때 워크로드 그룹의 활성 및 사용 기록을 분석하는 데도 사용할 수 있습니다.

```sql
WITH rg
AS
(   SELECT  
     pn.name						AS node_name
    ,pn.[type]						AS node_type
    ,pn.pdw_node_id					AS node_id
    ,rp.name						AS pool_name
    ,rp.max_memory_kb*1.0/1024				AS pool_max_mem_MB
    ,wg.name						AS group_name
    ,wg.importance					AS group_importance
    ,wg.request_max_memory_grant_percent		AS group_request_max_memory_grant_pcnt
    ,wg.max_dop						AS group_max_dop
    ,wg.effective_max_dop				AS group_effective_max_dop
    ,wg.total_request_count				AS group_total_request_count
    ,wg.total_queued_request_count			AS group_total_queued_request_count
    ,wg.active_request_count				AS group_active_request_count
    ,wg.queued_request_count				AS group_queued_request_count
    FROM    sys.dm_pdw_nodes_resource_governor_workload_groups wg
    JOIN    sys.dm_pdw_nodes_resource_governor_resource_pools rp    
            ON  wg.pdw_node_id  = rp.pdw_node_id
    	    AND wg.pool_id      = rp.pool_id
    JOIN    sys.dm_pdw_nodes pn	
            ON	wg.pdw_node_id	= pn.pdw_node_id
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

## 리소스 클래스 예외

그러나 대부분의 쿼리는 리소스 클래스를 인식하지만 몇 가지 예외가 있습니다. 일반적으로 작업을 수행하는 데 필요한 리소스가 부족한 경우에 이 문제가 발생합니다. 즉, 예외는 일반적으로 쿼리가 더 높은 리소스 클래스에 의해 할당된 더 높은 메모리를 전혀 활용하지 않는 경우입니다. 이러한 경우에는 사용자에게 할당된 리소스 클래스와 상관없이 기본 또는 작은 리소스 클래스(smallrc)가 항상 사용됩니다. 예를 들어, `CREATE LOGIN`은 항상 smallrc에서 실행됩니다. 이 작업을 수행하는 데 필요한 리소스가 매우 부족하고 따라서 동시성 슬롯 모델에 쿼리를 포함하는 것이 적합하지 않습니다. 이 작업에 대량의 메모리를 미리 할당하는 것은 낭비입니다. 동시성 슬롯 모델에서 `CREATE LOGIN`을 제외하면 SQL 데이터 웨어하우스의 효율을 훨씬 높일 수 있습니다.

다음 문은 리소스 클래스를 인식하지 **않습니다**.

- CREATE 또는 DROP TABLE
- ALTER TABLE ... SWITCH, SPLIT 또는 MERGE PARTITION
- ALTER INDEX DISABLE
- DROP INDEX
- CREATE, UPDATE 또는 DROP STATISTICS
- TRUNCATE TABLE
- ALTER AUTHORIZATION
- CREATE LOGIN
- CREATE, ALTER 또는 DROP USER
- CREATE, ALTER 또는 DROP PROCEDURE
- CREATE 또는 DROP VIEW
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

### 동시성 한도를 적용하는 쿼리

대부분의 최종 사용자 쿼리가 리소스 클래스에 의해 제어될 가능성이 크다는 점에 주의해야 합니다. 플랫폼에서 별도로 제외되지 않은 한, 일반적인 규칙은 활성 쿼리 작업이 동시 쿼리 및 동시성 슬롯 임계값 모두에 맞아야 한다는 것입니다. 최종 사용자는 동시성 슬롯 모델에서 쿼리를 제외하도록 선택할 수 없습니다. 하나 이상의 임계값을 초과하면 쿼리가 큐에 저장되기 시작합니다. 큐에 저장된 쿼리는 제출 시간이 지난 후 우선 순위에 따라 해결됩니다.

다시 한 번 강조하지만 다음 문은 리소스 클래스를 **인식**합니다.

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


## 사용자 관리

1. **Create login:** SQL 데이터 웨어하우스의 **마스터** 데이터베이스에 대한 연결을 열고 다음 명령을 실행합니다.
	
	```sql
	CREATE LOGIN newperson WITH PASSWORD = 'mypassword';
	CREATE USER newperson for LOGIN newperson;
	```

	> [AZURE.NOTE] Azure SQL 데이터베이스 및 Azure SQL 데이터 웨어하우스의 master 데이터베이스에서 로그인을 위한 사용자를 만드는 것도 좋은 방법입니다. 멤버 자격을 부여하기 위해 로그인 시 마스터 수준의 사용자가 필요할 때 사용할 수 있는 두 가지 서버 역할이 있습니다. 역할은 `Loginmanager` 및 `dbmanager`입니다. Azure SQL 데이터베이스 및 SQL 데이터 웨어하우스 모두에서 이러한 역할은 로그인을 관리하고 데이터베이스를 만들 권한을 부여합니다. 이 점이 SQL Server와 다릅니다. 자세한 내용은 [Azure SQL 데이터베이스에서 데이터베이스 및 로그인 관리][]를 참조하세요.

2. **사용자 계정 만들기:** **SQL 데이터 웨어하우스 데이터베이스**에 대한 연결을 열고 다음 명령을 실행합니다.

	```sql
	CREATE USER newperson FOR LOGIN newperson;
	```

3. **권한 부여:** 아래 예제에서는 SQL 데이터 웨어하우스 데이터베이스에 대한 `CONTROL` 권한을 부여합니다. 데이터베이스 수준의 `CONTROL` 권한은 SQL Server의 db\_owner에 해당합니다.

	```sql
	GRANT CONTROL ON DATABASE::MySQLDW to newperson;
	```

4. **리소스 클래스 증가:** 증가하는 워크로드 관리 역할에 사용자를 추가하려면 다음 쿼리를 사용합니다.

	```sql
	EXEC sp_addrolemember 'largerc', 'newperson'
	```

5. **리소스 클래스 감소:** 워크로드 관리 역할에서 사용자를 제거하려면 다음 쿼리를 사용합니다.

	```sql
	EXEC sp_droprolemember 'largerc', 'newperson';
	```

	> [AZURE.NOTE] smallrc에서 사용자를 제거하는 것은 불가능합니다.

## 큐에 대기 중인 쿼리 검색 및 다른 DMV

`sys.dm_pdw_exec_requests` DMV는 동시성 큐에 대기 중인 쿼리를 식별하는 데 사용할 수 있습니다.

```sql
SELECT 	 r.[request_id]				 AS Request_ID
	,r.[status]				 AS Request_Status
	,r.[submit_time]			 AS Request_SubmitTime
	,r.[start_time]				 AS Request_StartTime
        ,DATEDIFF(ms,[submit_time],[start_time]) AS Request_InitiateDuration_ms
        ,r.resource_class                         AS Request_resource_class
FROM    sys.dm_pdw_exec_requests r;
```

워크로드 관리 역할은 `sys.database_principals`을 사용해 볼 수 있습니다.

```sql
SELECT  ro.[name]           AS [db_role_name]
FROM    sys.database_principals ro
WHERE   ro.[type_desc]      = 'DATABASE_ROLE'
AND     ro.[is_fixed_role]  = 0;
```

다음 쿼리는 각 사용자에게 어떤 역할이 할당되었는지 보여줍니다.

```sql
SELECT	r.name AS role_principal_name
,		m.name AS member_principal_name
FROM	sys.database_role_members rm
JOIN	sys.database_principals AS r			ON rm.role_principal_id		= r.principal_id
JOIN	sys.database_principals AS m			ON rm.member_principal_id	= m.principal_id
WHERE	r.name IN ('mediumrc','largerc', 'xlargerc');
```

SQL 데이터 웨어하우스에 다음과 같은 대기 유형이 있습니다.

- LocalQueriesConcurrencyResourceType: 은 동시성 슬롯 프레임워크 외부에 존재하는 쿼리. `SELECT @@VERSION`과 같은 DMV 쿼리 및 시스템 함수는 로컬 쿼리의 예입니다.
- UserConcurrencyResourceType: 동시성 슬롯 프레임워크 내에 존재하는 쿼리. 최종 사용자 테이블에 대한 쿼리는 이 리소스 유형을 사용할 수 있는 예를 나타냅니다.
- DmsConcurrencyResourceType: 데이터 이동 작업으로 초래된 대기.
- BackupConcurrencyResourceType: 이 대기는 데이터베이스가 백업 중임을 나타냅니다. 이 리소스 유형에 대한 최대값은 1입니다. 여러 백업을 동시에 요청한 경우 다른 백업 요청은 큐에 저장됩니다.

`sys.dm_pdw_waits` DMV는 어떤 리소스를 요청 대기 중인지 알아내는 데 사용할 수 있습니다.

```sql
SELECT  w.[wait_id]
,       w.[session_id]
,       w.[type]			AS Wait_type
,       w.[object_type]
,       w.[object_name]
,       w.[request_id]
,       w.[request_time]
,       w.[acquire_time]
,       w.[state]
,       w.[priority]
,	SESSION_ID()			AS Current_session
,	s.[status]			AS Session_status
,	s.[login_name]
,	s.[query_count]
,	s.[client_id]
,	s.[sql_spid]
,	r.[command]			AS Request_command
,	r.[label]
,	r.[status]			AS Request_status
,	r.[submit_time]
,	r.[start_time]
,	r.[end_compile_time]
,	r.[end_time]
,	DATEDIFF(ms,r.[submit_time],r.[start_time])		AS Request_queue_time_ms
,	DATEDIFF(ms,r.[start_time],r.[end_compile_time])	AS Request_compile_time_ms
,	DATEDIFF(ms,r.[end_compile_time],r.[end_time])		AS Request_execution_time_ms
,	r.[total_elapsed_time]
FROM    sys.dm_pdw_waits w
JOIN    sys.dm_pdw_exec_sessions s  ON w.[session_id] = s.[session_id]
JOIN    sys.dm_pdw_exec_requests r  ON w.[request_id] = r.[request_id]
WHERE	w.[session_id] <> SESSION_ID();
```

`sys.dm_pdw_resource_waits` DMV는 지정된 쿼리가 사용하도록 대기 중인 리소스 만을 보여줍니다. 리소스 대기 시간은 기본 SQL Server가 CPU에 대해 쿼리를 예약하는 데 소요되는 대기 시간을 나타내는 것과 달리 리소스가 제공될 때까지 대기하는 시간만 측정합니다.

```sql
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
WHERE	[session_id] <> SESSION_ID();
```

`sys.dm_pdw_wait_stats` DMV는 기록 추세 분석에 사용할 수 있습니다.

```sql
SELECT	w.[pdw_node_id]
,		w.[wait_name]
,		w.[max_wait_time]
,		w.[request_count]
,		w.[signal_time]
,		w.[completed_count]
,		w.[wait_time]
FROM	sys.dm_pdw_wait_stats w;
```

## 다음 단계

데이터베이스 사용자 및 보안을 관리하는 방법에 대 한 자세한 내용은 [SQL 데이터 웨어하우스에서 데이터베이스 보호][]를 참조합니다. 더 큰 리소스 클래스가 클러스터된 columnstore 인덱스 품질을 향상할 수 있는 방법에 대한 자세한 내용은 [인덱스를 다시 작성하여 세그먼트 품질 개선]를 참조합니다.

<!--Image references-->

<!--Article references-->
[SQL 데이터 웨어하우스에서 데이터베이스 보호]: ./sql-data-warehouse-overview-manage-security.md
[인덱스를 다시 작성하여 세그먼트 품질 개선]: ./sql-data-warehouse-tables-index.md#rebuilding-indexes-to-improve-segment-quality

<!--MSDN references-->
[Azure SQL 데이터베이스에서 데이터베이스 및 로그인 관리]: https://msdn.microsoft.com/library/azure/ee336235.aspx

<!--Other Web references-->

<!---HONumber=AcomDC_0713_2016-->