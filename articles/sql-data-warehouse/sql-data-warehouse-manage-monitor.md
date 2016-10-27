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
   ms.date="10/08/2016"
   ms.author="sonyama;barbkess"/>


# <a name="monitor-your-workload-using-dmvs"></a>DMV를 사용하여 작업 모니터링

이 문서에서는 DMV(동적 관리 뷰)를 사용하여 작업을 모니터링하고 Azure SQL 데이터 웨어하우스의 쿼리 실행을 조사하는 방법을 설명합니다.

## <a name="permissions"></a>권한

이 문서의 DMV를 쿼리하려면 VIEW DATABASE STATE 또는 CONTROL 권한이 필요합니다. 일반적으로 VIEW DATABASE STATE 권한 부여를 선호합니다. 훨씬 제한적이기 때문입니다.

```sql
GRANT VIEW DATABASE STATE TO myuser;
```

## <a name="monitor-connections"></a>연결 모니터링

SQL Data Warehouse에 대한 모든 로그인은 [sys.dm_pdw_exec_sessions][]에 기록됩니다.  이 DMV에는 마지막 10,000회의 로그인 정보가 포함됩니다.  session_id(기본 키)는 각각의 새 로그인에 대해 순차적으로 할당됩니다.

```sql
-- Other Active Connections
SELECT * FROM sys.dm_pdw_exec_sessions where status <> 'Closed' and session_id <> session_id();
```

## <a name="monitor-query-execution"></a>쿼리 실행 모니터링

SQL Data Warehouse에 대해 실행되는 모든 쿼리는 [sys.dm_pdw_exec_requests][]에 기록됩니다.  이 DMV에는 마지막으로 실행한 쿼리 10,000개가 포함됩니다.  이 DMV의 기본 키인 request_id는 각 쿼리를 고유하게 식별합니다.  request_id는 각각의 새 쿼리에 대해 순차적으로 할당되며 쿼리 ID를 나타내는 QID가 접두사로 추가됩니다.  이 DMV에서 지정된 session_id를 쿼리하면 지정된 로그온에 대한 모든 쿼리가 표시됩니다.

>[AZURE.NOTE] 저장 프로시저는 여러 요청 ID를 사용합니다.  요청 ID는 순차적으로 할당됩니다. 

특정 쿼리에 대한 쿼리 실행 계획 및 시간을 조사하기 위해 수행하는 단계는 다음과 같습니다.

### <a name="step-1:-identify-the-query-you-wish-to-investigate"></a>1단계: 조사하려는 쿼리 식별

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

-- Find a query with the Label 'My Query'
-- Use brackets when querying the label column, as it it a key word
SELECT  *
FROM    sys.dm_pdw_exec_requests
WHERE   [label] = 'My Query';
```

위의 쿼리 결과에서 조사할 쿼리의 **요청 ID를 적어 둡니다** .

**일시 중단됨** 상태의 쿼리는 동시성 제한으로 인해 대기 중인 쿼리입니다. 이러한 쿼리는 sys.dm_pdw_waits 대기 쿼리에도 UserConcurrencyResourceType 형식으로 표시됩니다. 동시성 제한에 대한 자세한 내용은 [동시성 및 워크로드 관리][] 를 참조하세요. 쿼리는 개체 잠금 등의 기타 이유로 인해 대기 상태일 수도 있습니다.  쿼리가 리소스를 대기 중인 경우 이 문서 뒷부분의 [리소스를 대기 중인 쿼리 조사][] 를 참조하세요.

sys.dm_pdw_exec_requests 테이블에서 쿼리 조회를 간소화하려면 [LABEL][]을 사용하여 sys.dm_pdw_exec_requests 보기에서 조회할 수 있는 주석을 쿼리에 할당합니다.

```sql
-- Query with Label
SELECT *
FROM sys.tables
OPTION (LABEL = 'My Query')
;
```

### <a name="step-2:-investigate-the-query-plan"></a>2단계: 쿼리 계획 조사

요청 ID를 사용하여 [sys.dm_pdw_request_steps][]에서 쿼리의 DSQL(분산된 SQL) 계획을 검색합니다.

```sql
-- Find the distributed query plan steps for a specific query.
-- Replace request_id with value from Step 1.

SELECT * FROM sys.dm_pdw_request_steps
WHERE request_id = 'QID####'
ORDER BY step_index;
```

DSQL 계획의 시간이 생각보다 오래 걸리는 경우 계획이 여러 DSQL 단계를 포함하여 복잡하거나 한 단계에 시간이 오래 걸리는 것일 수 있습니다.  계획에 많은 단계가 포함되어 있으며 여러 이동 작업이 수행되는 경우에는 테이블 분산을 최적화하여 데이터 이동을 줄일 수 있습니다. [테이블 분산][] 문서에서는 쿼리를 확인하기 위해 데이터를 이동해야 하는 이유와, 데이터 이동을 최소화하기 위한 몇 가지 분산 전략에 대해 설명합니다.

한 단계에서 추가 세부 정보를 조사하려면 오래 실행되는 쿼리 단계의 *operation_type* 열을 확인하고 **단계 인덱스**를 적어 둡니다.

- OnOperation, RemoteOperation, ReturnOperation 등의 **SQL 작업**에 대해 3a단계를 진행합니다.
- ShuffleMoveOperation, BroadcastMoveOperation, TrimMoveOperation, PartitionMoveOperation, MoveOperation, CopyOperation 등의 **데이터 이동 작업**에 대해 3b단계를 진행합니다.

### <a name="step-3a:-investigate-sql-on-the-distributed-databases"></a>3a단계: 분산 데이터베이스에서 SQL 조사

요청 ID와 단계 인덱스를 사용하여 [sys.dm_pdw_sql_requests][]에서 세부 정보를 검색합니다. 이 보기에는 모든 분산 데이터베이스에 대한 쿼리 단계의 실행 정보가 포함되어 있습니다.

```sql
-- Find the distribution run times for a SQL step.
-- Replace request_id and step_index with values from Step 1 and 3.

SELECT * FROM sys.dm_pdw_sql_requests
WHERE request_id = 'QID####' AND step_index = 2;
```

쿼리 단계가 실행되고 있으면 [DBCC PDW_SHOWEXECUTIONPLAN][]을 사용하여 특정 분산에서 현재 실행 중인 단계에 대해 SQL Server 계획 캐시에서 SQL Server 예상 계획을 검색할 수 있습니다.

```sql
-- Find the SQL Server execution plan for a query running on a specific SQL Data Warehouse Compute or Control node.
-- Replace distribution_id and spid with values from previous query.

DBCC PDW_SHOWEXECUTIONPLAN(1, 78);
```

### <a name="step-3b:-investigate-data-movement-on-the-distributed-databases"></a>3b단계: 분산 데이터베이스에서 데이터 이동 조사

요청 ID 및 단계 인덱스를 사용하여 [sys.dm_pdw_dms_workers][]에서 각 분산에 대해 실행 중인 데이터 이동 단계에 대한 정보를 검색합니다.

```sql
-- Find the information about all the workers completing a Data Movement Step.
-- Replace request_id and step_index with values from Step 1 and 3.

SELECT * FROM sys.dm_pdw_dms_workers
WHERE request_id = 'QID####' AND step_index = 2;
```

- *total_elapsed_time* 열을 검사하여 특정 배포에서 데이터 이동 시간이 다른 배포보다 오래 걸리는지 확인합니다.
- 장기 실행 배포의 경우 *rows_processed* 열을 검사하여 해당 배포에서 이동되는 행 수가 다른 배포보다 훨씬 큰지 확인합니다. 이동되는 행 수가 훨씬 많으면 기본 데이터가 기울어진 것일 수 있습니다.

쿼리가 실행되고 있으면 [DBCC PDW_SHOWEXECUTIONPLAN][]을 사용하여 특정 배포 내에서 현재 실행 중인 SQL 단계에 대한 SQL Server 계획 캐시에서 SQL Server 예상 계획을 검색할 수 있습니다.

```sql
-- Find the SQL Server estimated plan for a query running on a specific SQL Data Warehouse Compute or Control node.
-- Replace distribution_id and spid with values from previous query.

DBCC PDW_SHOWEXECUTIONPLAN(55, 238);
```

<a name="waiting"></a>
## <a name="monitor-waiting-queries"></a>대기 중인 쿼리 모니터링

쿼리가 리소스를 대기하는 중이어서 진행되고 있지 않은 경우, 쿼리가 대기 중인 모든 리소스를 표시하는 쿼리는 다음과 같습니다.

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

쿼리가 적극적으로 다른 쿼리의 리소스를 대기 중인 경우 상태는 **AcquireResources**입니다.  쿼리가 필요한 리소스를 모두 가지고 있으면 상태는 **Granted**입니다.

## <a name="next-steps"></a>다음 단계
DMV에 대한 자세한 내용은 [시스템 뷰][]를 참조하세요.
모범 사례에 대한 자세한 내용은 [SQL Data Warehouse 모범 사례][]를 참조하세요.

<!--Image references-->

<!--Article references-->
[관리 개요]: ./sql-data-warehouse-overview-manage.md
[SQL 데이터 웨어하우스 모범 사례]: ./sql-data-warehouse-best-practices.md
[시스템 뷰]: ./sql-data-warehouse-reference-tsql-system-views.md
[테이블 분산]: ./sql-data-warehouse-tables-distribute.md
[동시성 및 워크로드 관리]: ./sql-data-warehouse-develop-concurrency.md
[리소스를 대기 중인 쿼리 조사]: ./sql-data-warehouse-manage-monitor.md#waiting

<!--MSDN references-->
[sys.dm_pdw_dms_workers]: http://msdn.microsoft.com/library/mt203878.aspx
[sys.dm_pdw_exec_requests]: http://msdn.microsoft.com/library/mt203887.aspx
[sys.dm_pdw_exec_sessions]: http://msdn.microsoft.com/library/mt203883.aspx
[sys.dm_pdw_request_steps]: http://msdn.microsoft.com/library/mt203913.aspx
[sys.dm_pdw_sql_requests]: http://msdn.microsoft.com/library/mt203889.aspx
[DBCC PDW_SHOWEXECUTIONPLAN]: http://msdn.microsoft.com/library/mt204017.aspx
[DBCC PDW_SHOWSPACEUSED]: http://msdn.microsoft.com/library/mt204028.aspx
[LABEL]: https://msdn.microsoft.com/library/ms190322.aspx



<!--HONumber=Oct16_HO2-->


