<properties
   pageTitle="DMV를 사용하여 작업 모니터링 | Microsoft Azure"
   description="DMV를 사용하여 작업을 모니터링하는 방법을 알아봅니다."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="sahaj08"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="08/06/2015"
   ms.author="sahajs"/>

# DMV를 사용하여 작업 모니터링

이 문서에서는 DMV(동적 관리 뷰)를 사용하여 작업을 모니터링하고 Azure SQL 데이터 웨어하우스의 쿼리 실행을 조사하는 방법을 설명합니다.




## 권한

SQL 데이터 웨어하우스에서 동적 관리 뷰를 쿼리하려면 **VIEW DATABASE STATE** 권한이 있어야 합니다. **VIEW DATABASE STATE** 권한은 현재 데이터베이스 내의 모든 개체에 대한 정보를 반환합니다. 특정 데이터베이스 사용자에게 **VIEW DATABASE STATE** 권한을 부여하려면 다음 쿼리를 실행합니다.

```

GRANT VIEW DATABASE STATE TO database_user;

```




## 연결 모니터링

*sys.dm\_pdw\_nodes\_exec\_connections* 뷰를 사용하여 Azure SQL 데이터 웨어하우스 데이터베이스에 설정된 연결에 대한 정보를 검색할 수 있습니다. 또한 *sys.dm\_exec\_sessions* 뷰는 모든 활성 사용자 연결에 대한 정보를 검색할 때 유용합니다.

```

SELECT * FROM sys.dm_pdw_nodes_exec_connections;
SELECT * FROM sys.dm_pdw_nodes_exec_sessions;

```


다음 쿼리를 사용하여 현재 연결에 대한 정보를 검색할 수 있습니다.

```

SELECT * 
FROM sys.dm_pdw_nodes_exec_connections AS c 
   JOIN sys.dm_pdw_nodes_exec_sessions AS s 
   ON c.session_id = s.session_id 
WHERE c.session_id = @@SPID;

```





## 쿼리 실행 조사
쿼리가 완료되지 않거나 예상보다 오래 실행되는 경우가 발생할 수 있습니다. 이러한 경우 다음 단계를 사용하여 데이터를 수집하고 문제 범위를 좁힐 수 있습니다.



### 1단계: 조사할 쿼리 찾기

```

-- Monitor running queries
SELECT * FROM sys.dm_pdw_exec_requests WHERE status = 'Running';

-- Find the longest running queries
SELECT * FROM sys.dm_pdw_exec_requests ORDER BY total_elapsed_time DESC;

```

쿼리의 요청 ID를 저장합니다.


  
### 2단계: 쿼리가 리소스를 대기 중인지 확인

```

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


위 쿼리의 결과는 요청의 대기 상태를 보여 줍니다.

- 쿼리가 다른 쿼리의 리소스를 대기 중인 경우 상태는 **AcquireResources**입니다.
- 쿼리가 필요한 리소스를 모두 가지고 있으며 대기 중이 아닌 경우 상태는 **Granted**입니다. 이 경우 쿼리 단계를 계속 진행합니다.




### 3단계: 가장 오래 실행되는 쿼리 단계 찾기

요청 ID를 사용하여 분산된 모든 쿼리 단계 목록을 검색합니다. 총 경과 시간을 확인하여 장기 실행 단계를 찾습니다.

```

-- Find the distributed query plan steps for a specific query.
-- Replace request_id with value from Step 1.
 
SELECT * FROM sys.dm_pdw_request_steps
WHERE request_id = 'QID33209'
ORDER BY step_index;

```

장기 실행 단계의 단계 인덱스를 저장합니다.

장기 실행 쿼리 단계의 *operation\_type* 열을 확인합니다. - **SQL 작업**(OnOperation, RemoteOperation, ReturnOperation)의 경우 4a단계를 진행합니다. - **데이터 이동 작업**(ShuffleMoveOperation, BroadcastMoveOperation, TrimMoveOperation, PartitionMoveOperation, MoveOperation, CopyOperation)의 경우 4b단계를 진행합니다.



### 4a단계: SQL 단계의 실행 진행률 찾기

요청 ID와 단계 인덱스를 사용하여 쿼리에서 SQL 단계의 일부로 SQL Server 쿼리 배포에 대한 정보를 검색합니다. 노드 ID 및 SPID를 저장합니다.

```

-- Find the distribution run times for a SQL step.
-- Replace request_id and step_index with values from Step 1 and 3.

SELECT * FROM sys.dm_pdw_sql_requests
WHERE request_id = 'QID33209' AND step_index = 2;

```


다음 쿼리를 사용하여 특정 노드의 SQL 단계에 대한 SQL Server 실행 계획을 검색합니다.

```

-- Find the SQL Server execution plan for a query running on a specific SQL Data Warehouse Compute or Control node. 
-- Replace node_id and spid with values from previous query.

DBCC PDW_SHOWEXECUTIONPLAN(1, 78);

```



### 4b단계: DMS 단계의 실행 진행률 찾기

요청 ID와 단계 인덱스를 사용하여 각 배포에서 실행되는 데이터 이동 단계에 대한 정보를 검색합니다.

```

-- Find the information about all the workers completing a Data Movement Step.
-- Replace request_id and step_index with values from Step 1 and 3.
 
SELECT * FROM sys.dm_pdw_dms_workers
WHERE request_id = 'QID33209' AND step_index = 2;

```

- *total\_elapsed\_time* 열을 검사하여 특정 배포에서 데이터 이동 시간이 다른 배포보다 오래 걸리는지 확인합니다. 
- 장기 실행 배포에 대한 *rows\_processed* 열을 검사하여 해당 배포에서 이동되는 행 수가 다른 배포보다 훨씬 큰지 확인합니다. 이는 쿼리에 데이터 기울이기가 있음을 보여 줍니다.





## 데이터 기울이기 조사

```

-- Find data skew for a distributed table
DBCC PDW_SHOWSPACEUSED("dbo.FactInternetSales");

```


이 쿼리의 결과는 데이터베이스 배포 60개에 각각 저장된 테이블 행 수를 보여 줍니다. 최적의 성능을 얻으려면 분산 테이블의 행을 모든 배포에 균등하게 나누어야 합니다. 자세한 내용은 [테이블 디자인][]을 참조하세요.



## 다음 단계
SQL 데이터 웨어하우스 관리에 대한 자세한 팁은 [관리 개요][]를 참조하세요.

<!--Image references-->

<!--Article references-->
[관리 개요]: sql-data-warehouse-overview-manage.md
[테이블 디자인]: sql-data-warehouse-develop-table-design.md

<!--MSDN references-->

<!---HONumber=August15_HO7-->