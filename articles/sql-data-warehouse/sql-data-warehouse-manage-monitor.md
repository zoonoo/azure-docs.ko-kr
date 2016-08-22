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
   ms.date="07/22/2016"
   ms.author="sonyama;barbkess;sahajs"/>

# DMV를 사용하여 작업 모니터링

이 문서에서는 DMV(동적 관리 뷰)를 사용하여 작업을 모니터링하고 Azure SQL 데이터 웨어하우스의 쿼리 실행을 조사하는 방법을 설명합니다.

## 연결 모니터링

[sys.dm\_pdw\_exec\_sessions][] 보기를 사용하면 Azure SQL 데이터 웨어하우스 데이터베이스에 대한 연결을 모니터링할 수 있습니다. 이 보기에는 활성 세션뿐 아니라 최근에 연결이 끊긴 세션의 기록도 포함되어 있습니다. 이 보기의 기본 키인 session\_id는 새로 로그온할 때마다 순차적으로 할당됩니다.

```sql
SELECT * FROM sys.dm_pdw_exec_sessions where status <> 'Closed';
```

## 쿼리 실행 모니터링

쿼리 실행을 모니터링하려면 먼저 [sys.dm\_pdw\_exec\_requests][]를 확인합니다. 이 보기에는 진행 중인 쿼리와 최근에 완료된 쿼리의 기록이 모두 포함되어 있습니다. 이 보기의 기본 키인 request\_id는 각 쿼리를 고유하게 식별하며 각 새 쿼리에 대해 순차적으로 할당됩니다. 이 테이블에서 특정 session\_id를 쿼리하면 지정된 로그온에 대한 모든 쿼리가 표시됩니다.

>[AZURE.NOTE] 저장 프로시저는 여러 request\_id를 사용합니다. 요청 ID는 순차적으로 할당됩니다.

특정 쿼리에 대한 쿼리 실행 계획 및 시간을 조사하기 위해 수행하는 단계는 다음과 같습니다.

### 1단계: 조사하려는 쿼리 식별

```sql
-- Monitor active queries
SELECT * 
FROM sys.dm_pdw_exec_requests 
WHERE status not in ('Completed','Failed','Cancelled')
  AND session_id <> session_id()
ORDER BY submit_time DESC;

-- Find top 10 queries longest running queries
SELECT TOP 10 * 
FROM sys.dm_pdw_exec_requests 
ORDER BY total_elapsed_time DESC;
```

위의 쿼리 결과에서 조사할 쿼리의 **요청 ID를 적어 둡니다**.

[동시성 및 워크로드 관리][]에 자세히 설명되어 있는 동시성 제한으로 인해 일시 중단 상태의 쿼리가 큐에 대기됩니다. 이러한 쿼리는 UserConcurrencyResourceType 형식의 sys.dm\_pdw\_waits 대기 쿼리에도 표시됩니다. 쿼리가 잠금 등의 다른 이유를 기다릴 수도 있습니다. 쿼리가 리소스를 대기 중인 경우 [리소스를 대기 중인 쿼리 조사][]를 참조하세요.

### 2단계: 쿼리 계획에서 가장 오래 실행되는 단계 찾기

요청 ID를 사용하여 [sys.dm\_pdw\_request\_steps][]에서 쿼리 계획 단계의 목록을 검색합니다. 총 경과 시간을 확인하여 장기 실행 단계를 찾습니다.

```sql
-- Find the distributed query plan steps for a specific query.
-- Replace request_id with value from Step 1.

SELECT * FROM sys.dm_pdw_request_steps
WHERE request_id = 'QID####'
ORDER BY step_index;
```

장기 실행 쿼리 단계의 *operation\_type* 열을 확인하고 **단계 인덱스**를 적어둡니다.

- OnOperation, RemoteOperation, ReturnOperation 등의 **SQL 작업**에 대해 3a단계를 진행합니다.
- ShuffleMoveOperation, BroadcastMoveOperation, TrimMoveOperation, PartitionMoveOperation, MoveOperation, CopyOperation 등의 **데이터 이동 작업**에 대해 3b단계를 진행합니다.

### 3a단계: SQL 단계의 실행 진행률 찾기

요청 ID와 단계 인덱스를 사용하여 [sys.dm\_pdw\_sql\_requests][]에서 세부 정보를 검색합니다. 이 보기에는 SQL Server의 배포된 각 인스턴스에 대한 쿼리 실행 세부 정보가 포함되어 있습니다.

```sql
-- Find the distribution run times for a SQL step.
-- Replace request_id and step_index with values from Step 1 and 3.

SELECT * FROM sys.dm_pdw_sql_requests
WHERE request_id = 'QID####' AND step_index = 2;
```

쿼리가 실행되고 있으면 [DBCC PDW\_SHOWEXECUTIONPLAN][]을 사용하여 특정 배포 내에서 현재 실행 중인 SQL 단계에 대한 SQL Server 계획 캐시에서 SQL Server 예상 계획을 검색할 수 있습니다.

```sql
-- Find the SQL Server execution plan for a query running on a specific SQL Data Warehouse Compute or Control node.
-- Replace distribution_id and spid with values from previous query.

DBCC PDW_SHOWEXECUTIONPLAN(1, 78);
```

### 3b단계: DMS 단계의 실행 진행률 찾기

요청 ID 및 단계 인덱스를 사용하여 [sys.dm\_pdw\_dms\_workers][]의 각 배포에서 실행 중인 데이터 이동 단계에 대한 정보를 검색합니다.

```sql
-- Find the information about all the workers completing a Data Movement Step.
-- Replace request_id and step_index with values from Step 1 and 3.

SELECT * FROM sys.dm_pdw_dms_workers
WHERE request_id = 'QID####' AND step_index = 2;
```

- *total\_elapsed\_time* 열을 검사하여 특정 배포에서 데이터 이동 시간이 다른 배포보다 오래 걸리는지 확인합니다.
- 장기 실행 배포의 경우 *rows\_processed* 열을 검사하여 해당 배포에서 이동되는 행 수가 다른 배포보다 훨씬 큰지 확인합니다. 이동되는 행 수가 훨씬 많으면 기본 데이터가 기울어진 것일 수 있습니다.

쿼리가 실행되고 있으면 [DBCC PDW\_SHOWEXECUTIONPLAN][]을 사용하여 특정 배포 내에서 현재 실행 중인 SQL 단계에 대한 SQL Server 계획 캐시에서 SQL Server 예상 계획을 검색할 수 있습니다.

```sql
-- Find the SQL Server estimated plan for a query running on a specific SQL Data Warehouse Compute or Control node.
-- Replace distribution_id and spid with values from previous query.

DBCC PDW_SHOWEXECUTIONPLAN(55, 238);
```

<a name="waiting"></a>
## 대기 중인 쿼리 모니터링

리소스를 대기하고 있으므로 쿼리가 진행되고 있지 않음을 확인할 경우 다음 쿼리를 실행하면 쿼리가 대기 중인 모든 리소스가 표시됩니다.

```sql
-- Find queries 
-- Replace request_id with value from Step 1.

SELECT waits.session_id,
      waits.request_id,  
      requests.command,
      requests.status,
      requests.start_time,  
      waits.type,
      waits.state,
      waits.object_type,
      waits.object_name
FROM   sys.dm_pdw_waits waits
   JOIN  sys.dm_pdw_exec_requests requests
   ON waits.request_id=requests.request_id
WHERE waits.request_id = 'QID####'
ORDER BY waits.object_name, waits.object_type, waits.state;
```

쿼리가 적극적으로 다른 쿼리의 리소스를 대기 중인 경우 상태는 **AcquireResources**입니다. 쿼리가 필요한 리소스를 모두 가지고 있으면 상태는 **Granted**입니다.

## 다음 단계
DMV(Dynamic Management Views)에 대한 자세한 내용은 [시스템 뷰][]를 참조하세요. SQL 데이터 웨어하우스 관리에 대한 팁은 [관리 개요][]를 참조하세요. 모범 사례는 [SQL 데이터 웨어하우스 모범 사례][]를 참조하세요.

<!--Image references-->

<!--Article references-->
[관리 개요]: ./sql-data-warehouse-overview-manage.md
[SQL 데이터 웨어하우스 모범 사례]: ./sql-data-warehouse-best-practices.md
[시스템 뷰]: ./sql-data-warehouse-reference-tsql-system-views.md
[동시성 및 워크로드 관리]: ./sql-data-warehouse-develop-concurrency.md
[리소스를 대기 중인 쿼리 조사]: ./sql-data-warehouse-manage-monitor.md#waiting

<!--MSDN references-->
[sys.dm\_pdw\_dms\_workers]: http://msdn.microsoft.com/library/mt203878.aspx
[sys.dm\_pdw\_exec\_requests]: http://msdn.microsoft.com/library/mt203887.aspx
[sys.dm\_pdw\_exec\_sessions]: http://msdn.microsoft.com/library/mt203883.aspx
[sys.dm\_pdw\_request\_steps]: http://msdn.microsoft.com/library/mt203913.aspx
[sys.dm\_pdw\_sql\_requests]: http://msdn.microsoft.com/library/mt203889.aspx
[DBCC PDW\_SHOWEXECUTIONPLAN]: http://msdn.microsoft.com/library/mt204017.aspx
[DBCC PDW_SHOWSPACEUSED]: http://msdn.microsoft.com/library/mt204028.aspx

<!---HONumber=AcomDC_0810_2016-->