<properties
   pageTitle="DMV를 사용하여 작업 모니터링 | Microsoft Azure"
   description="DMV를 사용하여 작업을 모니터링하는 방법을 알아봅니다."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="sonyam"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="04/12/2016"
   ms.author="sonyama;barbkess;sahajs"/>

# DMV를 사용하여 작업 모니터링

이 문서에서는 DMV(동적 관리 뷰)를 사용하여 작업을 모니터링하고 Azure SQL 데이터 웨어하우스의 쿼리 실행을 조사하는 방법을 설명합니다.

## 연결 모니터링

[sys.dm\_pdw\_exec\_sessions][] 보기를 사용하면 Azure SQL 데이터 웨어하우스 데이터베이스에 대한 연결을 모니터링할 수 있습니다. 이 보기에는 활성 세션뿐 아니라 최근에 연결이 끊긴 세션의 기록도 포함되어 있습니다. 이 보기의 기본 키인 session\_id는 새로 로그온할 때마다 순차적으로 할당됩니다.

```sql
SELECT * FROM sys.dm_pdw_exec_sessions where status <> 'Closed';
```

## 쿼리 실행 조사
쿼리 실행을 모니터링하려면 먼저 [sys.dm\_pdw\_exec\_requests][]를 확인합니다. 이 보기에는 진행 중인 쿼리와 최근에 완료된 쿼리의 기록이 모두 포함되어 있습니다. 이 보기의 기본 키인 request\_id는 각 쿼리를 고유하게 식별하며 각 새 쿼리에 대해 순차적으로 할당됩니다. 이 테이블에서 특정 session\_id를 쿼리하면 지정된 로그온에 대한 모든 쿼리가 표시됩니다.

특정 쿼리의 실행을 조사하려는 시나리오에서는 보통 다음과 같은 단계를 수행합니다.

### 1단계: 조사할 쿼리 찾기

```sql
-- Monitor running queries
SELECT * FROM sys.dm_pdw_exec_requests WHERE status = 'Running';

-- Find the 10 longest running queries
SELECT TOP 10 * FROM sys.dm_pdw_exec_requests ORDER BY total_elapsed_time DESC;
```

조사할 쿼리의 요청 ID를 적어 둡니다.

### 2단계: 쿼리가 리소스를 대기 중인지 확인

```sql
-- Find waiting tasks for your session.
-- Replace request_id with value from Step 1.

SELECT waits.session_id,
      waits.request_id,  
      requests.command,
      requests.status,
      requests.start_time,  
      waits.type,  
      waits.object_type,
      waits.object_name,  
      waits.state  
FROM   sys.dm_pdw_waits waits
   JOIN  sys.dm_pdw_exec_requests requests
   ON waits.request_id=requests.request_id
WHERE waits.request_id = 'QID33188'
ORDER BY waits.object_name, waits.object_type, waits.state;
```

위 쿼리의 결과에는 쿼리의 대기 상태가 표시됩니다.

- 쿼리가 다른 쿼리의 리소스를 대기 중인 경우 상태는 **AcquireResources**입니다.
- 쿼리가 필요한 리소스를 모두 가지고 있으며 대기 중이 아닌 경우 상태는 **Granted**입니다. 이 경우 쿼리 단계를 계속 진행합니다.

### 3단계: 쿼리 계획에서 가장 오래 실행되는 단계 찾기

요청 ID를 사용하여 [sys.dm\_pdw\_request\_steps][]에서 쿼리 계획 단계의 목록을 검색합니다. 총 경과 시간을 확인하여 장기 실행 단계를 찾습니다.

```sql

-- Find the distributed query plan steps for a specific query.
-- Replace request_id with value from Step 1.

SELECT * FROM sys.dm_pdw_request_steps
WHERE request_id = 'QID33209'
ORDER BY step_index;
```

장기 실행 단계의 단계 인덱스를 저장합니다.

장기 실행 쿼리 단계의 *operation\_type* 열을 확인합니다.

- OnOperation, RemoteOperation, ReturnOperation 등의 **SQL 작업**에 대해 4a단계를 진행합니다.
- **데이터 이동 작업**에 ShuffleMoveOperation, BroadcastMoveOperation, TrimMoveOperation, PartitionMoveOperation, MoveOperation, CopyOperation 등의 4b 단계를 진행합니다.

### 4a단계: SQL 단계의 실행 진행률 찾기

요청 ID와 단계 인덱스를 사용하여 [sys.dm\_pdw\_sql\_requests][]에서 정보를 검색합니다. 이 보기에는 SQL Server의 배포된 인스턴스에 대한 쿼리 실행 세부 정보가 포함되어 있습니다. 쿼리가 계속 실행되고 있으며 SQL Server 배포에서 계획을 가져오려는 경우 배포 ID와 SPID를 적어 둡니다.

```sql
-- Find the distribution run times for a SQL step.
-- Replace request_id and step_index with values from Step 1 and 3.

SELECT * FROM sys.dm_pdw_sql_requests
WHERE request_id = 'QID33209' AND step_index = 2;
```


쿼리가 현재 실행되고 있으면 [DBCC PDW\_SHOWEXECUTIONPLAN][]을 사용하여 특정 배포에 대해 현재 실행 중인 SQL 단계의 SQL Server 실행 계획을 검색할 수 있습니다.

```sql
-- Find the SQL Server execution plan for a query running on a specific SQL Data Warehouse Compute or Control node.
-- Replace distribution_id and spid with values from previous query.

DBCC PDW_SHOWEXECUTIONPLAN(1, 78);

```

### 4b단계: DMS 단계의 실행 진행률 찾기

요청 ID 및 단계 인덱스를 사용하여 [sys.dm\_pdw\_dms\_workers][]의 각 배포에서 실행 중인 데이터 이동 단계에 대한 정보를 검색합니다.

```sql
-- Find the information about all the workers completing a Data Movement Step.
-- Replace request_id and step_index with values from Step 1 and 3.

SELECT * FROM sys.dm_pdw_dms_workers
WHERE request_id = 'QID33209' AND step_index = 2;

```

- *total\_elapsed\_time* 열을 검사하여 특정 배포에서 데이터 이동 시간이 다른 배포보다 오래 걸리는지 확인합니다.
- 장기 실행 배포의 경우 *rows\_processed* 열을 검사하여 해당 배포에서 이동되는 행 수가 다른 배포보다 훨씬 큰지 확인합니다. 이동되는 행 수가 훨씬 많으면 기본 데이터가 기울어진 것일 수 있습니다.

쿼리가 현재 실행되고 있으면 [DBCC PDW\_SHOWEXECUTIONPLAN][]을 사용하여 특정 배포에 대해 현재 실행 중인 DMS 단계의 SQL Server 실행 계획을 검색할 수 있습니다.

```sql
-- Find the SQL Server execution plan for a query running on a specific SQL Data Warehouse Compute or Control node.
-- Replace distribution_id and spid with values from previous query.

DBCC PDW_SHOWEXECUTIONPLAN(55, 238);

```


## 데이터 기울이기 조사

[DBCC PDW\_SHOWSPACEUSED][]를 사용하여 테이블에 사용되는 공간을 조회합니다.

```sql
-- Find data skew for a distributed table
DBCC PDW_SHOWSPACEUSED("dbo.FactInternetSales");
```

이 쿼리의 결과는 데이터베이스 배포 60개에 각각 저장된 테이블 행 수를 보여 줍니다. 최적의 성능을 얻으려면 분산 테이블의 행을 모든 배포에 균등하게 나누어야 합니다.

자세히 알아보려면 [분산 테이블에 대한 데이터 오차 관리][] 또는 [테이블 디자인][]을 참조하세요.

## 다음 단계
Transact-SQL 및 DMV(동적 관리 뷰)에 대한 자세한 내용은 [참조 개요][]를 참조하세요. SQL 데이터 웨어하우스 관리에 대한 추가 팁은 [관리 개요][]를 참조하세요.

<!--Image references-->

<!--Article references-->
[관리 개요]: sql-data-warehouse-overview-manage.md
[테이블 디자인]: sql-data-warehouse-develop-table-design.md
[참조 개요]: sql-data-warehouse-overview-reference.md
[분산 테이블에 대한 데이터 오차 관리]: sql-data-warehouse-manage-distributed-data-skew.md

<!--MSDN references-->
[sys.dm\_pdw\_dms\_workers]: http://msdn.microsoft.com/library/mt203878.aspx
[sys.dm\_pdw\_exec\_requests]: http://msdn.microsoft.com/library/mt203887.aspx
[sys.dm\_pdw\_exec\_sessions]: http://msdn.microsoft.com/library/mt203883.aspx
[sys.dm\_pdw\_request\_steps]: http://msdn.microsoft.com/library/mt203913.aspx
[sys.dm\_pdw\_sql\_requests]: http://msdn.microsoft.com/library/mt203889.aspx
[DBCC PDW\_SHOWEXECUTIONPLAN]: http://msdn.microsoft.com/library/mt204017.aspx
[DBCC PDW\_SHOWSPACEUSED]: http://msdn.microsoft.com/library/mt204028.aspx

<!---HONumber=AcomDC_0413_2016-->