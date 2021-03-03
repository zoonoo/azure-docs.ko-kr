---
title: Azure SQL 차단 문제 이해 및 해결
titleSuffix: Azure SQL Database
description: 차단 및 문제 해결에 대 한 Azure SQL database 관련 항목의 개요입니다.
services: sql-database
dev_langs:
- TSQL
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.reviewer: ''
ms.date: 2/24/2021
ms.openlocfilehash: b829d7045ac520cfe908c3c8809ae17702d6175d
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101691436"
---
# <a name="understand-and-resolve-azure-sql-database-blocking-problems"></a>Azure SQL Database 차단 문제 이해 및 해결
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

## <a name="objective"></a>Objective

이 문서에서는 Azure SQL database의 차단을 설명 하 고 차단 문제를 해결 하는 방법을 보여 줍니다. 

이 문서에서 연결 이라는 용어는 데이터베이스의 단일 로그온 세션을 나타냅니다. 각 연결은 세션 ID (SPID)로 나타나거나 많은 Dmv에 session_id 합니다. 이러한 각 Spid는 일반적으로 별도의 프로세스 컨텍스트가 아니지만 일반적으로 프로세스 라고도 합니다. 대신 각 SPID는 지정 된 클라이언트에서 단일 연결에 대 한 요청을 처리 하는 데 필요한 서버 리소스와 데이터 구조로 구성 됩니다. 단일 클라이언트 응용 프로그램에는 하나 이상의 연결이 있을 수 있습니다. Azure SQL Database 관점에서 보면 단일 클라이언트 컴퓨터에서 단일 클라이언트 응용 프로그램의 여러 연결과 여러 클라이언트 응용 프로그램 또는 여러 클라이언트 컴퓨터에서 여러 연결 간에 차이가 없습니다. 원자 단위입니다. 원본 클라이언트에 관계 없이 하나의 연결에서 다른 연결을 차단할 수 있습니다.

> [!NOTE]
> **이 콘텐츠는 Azure SQL Database에 초점을 맞추고 있습니다.** Azure SQL Database은 안정적인 최신 버전의 Microsoft SQL Server 데이터베이스 엔진을 기반으로 하므로 문제 해결 옵션과 도구는 다를 수 있지만 많은 내용이 비슷합니다. SQL Server 차단에 대 한 자세한 내용은 [차단 문제 SQL Server 이해 및 해결](/troubleshoot/sql/performance/understand-resolve-blocking)을 참조 하세요.

## <a name="understand-blocking"></a>차단 이해 
 
차단은 잠금 기반 동시성을 사용 하는 RDBMS (관계형 데이터베이스 관리 시스템)의 피할 수 있는 디자인 특성입니다. 앞에서 설명한 것 처럼 SQL Server 한 세션에서 특정 리소스에 대 한 잠금을 보유 하 고 있고 두 번째 SPID가 동일한 리소스에서 충돌 하는 잠금 유형을 얻으려고 시도할 때 차단이 발생 합니다. 일반적으로 첫 번째 SPID가 리소스를 잠그는 시간 프레임은 작습니다. 소유 세션이 잠금을 해제 하는 경우 두 번째 연결을 사용 하 여 리소스에 대 한 자체 잠금을 획득 하 고 처리를 계속할 수 있습니다. 이는 정상적인 동작이 며 시스템 성능에 크게 영향을 주지 않고 하루 동안 여러 번 발생할 수 있습니다.

쿼리의 기간 및 트랜잭션 컨텍스트는 잠금이 유지 되는 기간을 결정 하 고 그에 따라 다른 쿼리에 영향을 줍니다. 쿼리가 트랜잭션 내에서 실행 되지 않는 경우 (잠금 힌트가 사용 되지 않음) SELECT 문에 대 한 잠금은 쿼리 중이 아니라 실제로 읽을 때에만 리소스에 대해 유지 됩니다. INSERT, UPDATE 및 DELETE 문의 경우, 데이터 일관성을 위해 그리고 필요한 경우 쿼리를 롤백하는 쿼리 중에 잠금이 유지 됩니다.

트랜잭션 내에서 실행 되는 쿼리의 경우 잠금이 보유 되는 기간은 쿼리 유형, 트랜잭션 격리 수준 및 쿼리에서 잠금 힌트가 사용 되는지 여부에 따라 결정 됩니다. 잠금, 잠금 힌트 및 트랜잭션 격리 수준에 대 한 설명은 다음 문서를 참조 하세요.

* [데이터베이스 엔진에서의 잠금](/sql/relational-databases/sql-server-transaction-locking-and-row-versioning-guide)
* [잠금 및 행 버전 관리 사용자 지정](/sql/relational-databases/sql-server-transaction-locking-and-row-versioning-guide#customizing-locking-and-row-versioning)
* [잠금 모드](/sql/relational-databases/sql-server-transaction-locking-and-row-versioning-guide#lock_modes)
* [잠금 호환성](/sql/relational-databases/sql-server-transaction-locking-and-row-versioning-guide#lock_compatibility)
* [트랜잭션](/sql/t-sql/language-elements/transactions-transact-sql)

시스템 성능에 부정적인 영향을 주는 지점에 잠금 및 차단이 지속 되는 경우 다음 이유 중 하나로 인해 발생 합니다.

* SPID는 오랜 시간 동안 리소스 집합에 대 한 잠금을 보유 한 후 해제 합니다. 이 차단 유형은 시간이 지남에 따라 자체적으로 해결 되지만 성능이 저하 될 수 있습니다.

* SPID는 리소스 집합에 대 한 잠금을 보유 하 고 해제 하지 않습니다. 이 차단 유형은 자체적으로 해결 되지 않으며 영향을 받는 리소스에 대 한 액세스를 무기한 방지 합니다.

첫 번째 시나리오에서는 여러 Spid로 인해 시간이 지남에 따라 서로 다른 리소스에 대 한 차단이 발생 하 여 이동 대상이 생성 되는 상황을 매우 유연 하 게 사용할 수 있습니다. 이러한 상황은 개별 쿼리에 대 한 문제 범위를 좁히기 위해 [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) 를 사용 하 여 문제를 해결 하기 어렵습니다. 반면 두 번째 상황에서는 일관 된 상태를 유지 하 여 쉽게 진단할 수 있습니다.

## <a name="applications-and-blocking"></a>응용 프로그램 및 차단

차단 문제를 발생 시킬 때 서버 쪽 튜닝 및 플랫폼 문제에 집중 하는 경향이 있을 수 있습니다. 그러나 데이터베이스에 대 한 주의를 기울여 해결 하지 못할 수 있으며, 클라이언트 응용 프로그램 및 제출 하는 쿼리를 검사 하는 데 시간과 에너지를 더 잘 파악할 수 있습니다. 응용 프로그램에서 발생 하는 데이터베이스 호출에 대해 표시 되는 표시 수준에 관계 없이, 차단 문제에는 응용 프로그램에서 전송한 정확한 SQL 문 검사와 쿼리 취소, 연결 관리, 모든 결과 행 페치 등에 대 한 응용 프로그램의 정확한 동작이 모두 필요 합니다. 개발 도구에서 연결 관리, 쿼리 취소, 쿼리 제한 시간, 결과 페치 등을 명시적으로 제어할 수 없는 경우 차단 문제를 확인할 수 없습니다. 특히 성능에 민감한 OLTP 환경에서는 Azure SQL Database 용 응용 프로그램 개발 도구를 선택 하기 전에 이러한 가능성을 면밀 하 게 검토 해야 합니다. 

데이터베이스 및 응용 프로그램의 디자인 및 생성 단계에서 데이터베이스 성능에 주의 합니다. 특히 각 쿼리에 대해 리소스 사용, 격리 수준 및 트랜잭션 경로 길이를 평가 해야 합니다. 각 쿼리 및 트랜잭션은 가능한 한 간단 하 게 만들어야 합니다. 좋은 연결 관리 규칙을 사용 하지 않고 실행 해야 하는 경우, 응용 프로그램의 사용자 수가 적으면 성능이 저하 될 수 있지만 사용자 수가 증가 하면 성능이 상당히 저하 될 수 있습니다. 

적절 한 응용 프로그램 및 쿼리 디자인을 사용 하 여 Azure SQL Database는 단일 서버에서 수천 명의 동시 사용자를 지원할 수 있으며,이를 거의 차단 하지 않습니다.

> [!Note]
> 응용 프로그램 개발 지침에 대 한 자세한 내용은 [연결 문제 해결 및 Azure SQL Database 및 AZURE SQL Managed Instance](troubleshoot-common-errors-issues.md) 및 [일시적인 오류 처리](/aspnet/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/transient-fault-handling)를 참조 하세요.

## <a name="troubleshoot-blocking"></a>차단 문제 해결

어떤 차단 상황에 있든 관계 없이 잠금 문제를 해결 하는 방법은 동일 합니다. 이러한 논리적 분판에는이 문서의 나머지 구성을 지시 하는 것입니다. 이 개념은 헤드 차단기를 찾고 쿼리에서 수행 하는 작업 및 차단 하는 이유를 식별 하는 것입니다. 문제가 있는 쿼리가 식별 되 면 (즉, 오랜 시간 동안 잠금을 보유 하 고 있는 경우) 다음 단계는 차단이 발생 한 이유를 분석 하 고 확인 하는 것입니다. 이유를 이해 한 후에는 쿼리 및 트랜잭션을 다시 디자인 하 여 변경할 수 있습니다.

문제 해결 단계:

1. 주 차단 세션 (헤드 차단기) 식별

2. 차단을 유발 하는 쿼리 및 트랜잭션 찾기 (오랜 기간 동안 잠금을 보유 하 고 있는 경우)

3. 장기간 차단이 발생 하는 이유 분석/이해

4. 쿼리 및 트랜잭션을 다시 디자인 하 여 차단 문제 해결

이제 적절 한 데이터 캡처를 사용 하 여 주 차단 세션을 정확 하 게 파악 하는 방법에 대해 살펴보겠습니다.

## <a name="gather-blocking-information"></a>차단 정보 수집

블로킹 문제를 해결 하는 어려움을 막기 위해 데이터베이스 관리자는 Azure SQL database에서 잠금 및 차단 상태를 지속적으로 모니터링 하는 SQL 스크립트를 사용할 수 있습니다. 이 데이터를 수집 하기 위해 기본적으로 두 가지 방법이 있습니다. 

첫 번째는 DMOs (동적 관리 개체)를 쿼리하여 결과를 시간에 따른 비교에 저장 하는 것입니다. 이 문서에서 참조 되는 일부 개체는 Dmv (동적 관리 뷰) 이며 일부는 동적 관리 함수 (DMFs)입니다. 두 번째 방법은 Xevent를 사용 하 여 실행 중인 작업을 캡처하는 것입니다. 


## <a name="gather-information-from-dmvs"></a>Dmv에서 정보 수집

블로킹 문제를 해결 하기 위해 Dmv를 참조 하는 것은 차단 체인 및 SQL 문 헤드의 SPID (세션 ID)를 식별 하는 목표입니다. 차단 되 고 있는 처리 되지 않은 Spid를 찾습니다. 다른 SPID의 SPID가 차단 되는 경우 리소스를 소유 하는 SPID (차단 SPID)를 조사 합니다. 소유자 SPID도 차단 되 고 있나요? 체인을 탐색 하 여 헤드 차단을 찾은 다음 잠금을 유지 하는 이유를 조사할 수 있습니다.

대상 Azure SQL database에서 이러한 각 스크립트를 실행 해야 합니다.

* Sp_who 및 sp_who2 명령은 현재 세션을 모두 표시 하는 이전 명령입니다. DMV sys.dm_exec_sessions는 더 쉽게 쿼리하고 필터링 할 수 있는 더 많은 데이터를 결과 집합에 반환 합니다. 다른 쿼리의 핵심 sys.dm_exec_sessions를 찾을 수 있습니다. 

* 특정 세션이 이미 식별 된 경우를 사용 `DBCC INPUTBUFFER(<session_id>)` 하 여 세션에서 제출한 마지막 문을 찾을 수 있습니다. 쿼리 및 필터링이 용이 하 고 session_id 및 request_id를 제공 하는 결과 집합에서 sys.dm_exec_input_buffer DMF (동적 관리 함수)를 사용 하 여 비슷한 결과를 반환할 수 있습니다. 예를 들어 session_id 66 및 request_id 0으로 전송 된 최신 쿼리를 반환 하려면 다음을 수행 합니다.

```sql
SELECT * FROM sys.dm_exec_input_buffer (66,0);
```

* Sys.dm_exec_requests를 참조 하 고 blocking_session_id 열을 참조 합니다. Blocking_session_id = 0 이면 세션이 차단 되지 않습니다. Sys.dm_exec_requests는 현재 실행 중인 요청만 나열 하지만 모든 연결 (활성 또는 그렇지 않음)은 sys.dm_exec_sessions에 나열 됩니다. 다음 쿼리에서 sys.dm_exec_requests와 sys.dm_exec_sessions 간에이 공통 조인을 작성 합니다.

* [Sys.dm_exec_sql_text](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-sql-text-transact-sql) 또는 [sys.dm_exec_input_buffer](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-input-buffer-transact-sql) dmv를 사용 하 여 적극적으로 실행 중인 쿼리와 현재 SQL 일괄 처리 텍스트 또는 입력 버퍼 텍스트를 찾으려면이 샘플 쿼리를 실행 합니다. Sys.dm_exec_sql_text의 필드에서 반환 된 데이터가 `text` NULL 이면 쿼리가 현재 실행 되 고 있지 않습니다. 이 경우 `event_info` sys.dm_exec_input_buffer의 필드에는 SQL 엔진에 전달 된 마지막 명령 문자열이 포함 됩니다. 이 쿼리를 사용 하 여 session_id에 의해 차단 된 session_ids 목록을 비롯 한 다른 세션을 차단 하는 세션을 식별할 수도 있습니다. 

```sql
WITH cteBL (session_id, blocking_these) AS 
(SELECT s.session_id, blocking_these = x.blocking_these FROM sys.dm_exec_sessions s 
CROSS APPLY    (SELECT isnull(convert(varchar(6), er.session_id),'') + ', '  
                FROM sys.dm_exec_requests as er
                WHERE er.blocking_session_id = isnull(s.session_id ,0)
                AND er.blocking_session_id <> 0
                FOR XML PATH('') ) AS x (blocking_these)
)
SELECT s.session_id, blocked_by = r.blocking_session_id, bl.blocking_these
, batch_text = t.text, input_buffer = ib.event_info, * 
FROM sys.dm_exec_sessions s 
LEFT OUTER JOIN sys.dm_exec_requests r on r.session_id = s.session_id
INNER JOIN cteBL as bl on s.session_id = bl.session_id
OUTER APPLY sys.dm_exec_sql_text (r.sql_handle) t
OUTER APPLY sys.dm_exec_input_buffer(s.session_id, NULL) AS ib
WHERE blocking_these is not null or r.blocking_session_id > 0
ORDER BY len(bl.blocking_these) desc, r.blocking_session_id desc, r.session_id;
```

* Microsoft 지원에서 제공 하는 보다 정교한 샘플 쿼리를 실행 하 여 차단 체인에 포함 된 세션의 쿼리 텍스트를 비롯 한 여러 세션 차단 체인의 헤드를 식별 합니다.

```sql
WITH cteHead ( session_id,request_id,wait_type,wait_resource,last_wait_type,is_user_process,request_cpu_time
,request_logical_reads,request_reads,request_writes,wait_time,blocking_session_id,memory_usage
,session_cpu_time,session_reads,session_writes,session_logical_reads
,percent_complete,est_completion_time,request_start_time,request_status,command
,plan_handle,sql_handle,statement_start_offset,statement_end_offset,most_recent_sql_handle
,session_status,group_id,query_hash,query_plan_hash) 
AS ( SELECT sess.session_id, req.request_id, LEFT (ISNULL (req.wait_type, ''), 50) AS 'wait_type'
    , LEFT (ISNULL (req.wait_resource, ''), 40) AS 'wait_resource', LEFT (req.last_wait_type, 50) AS 'last_wait_type'
    , sess.is_user_process, req.cpu_time AS 'request_cpu_time', req.logical_reads AS 'request_logical_reads'
    , req.reads AS 'request_reads', req.writes AS 'request_writes', req.wait_time, req.blocking_session_id,sess.memory_usage
    , sess.cpu_time AS 'session_cpu_time', sess.reads AS 'session_reads', sess.writes AS 'session_writes', sess.logical_reads AS 'session_logical_reads'
    , CONVERT (decimal(5,2), req.percent_complete) AS 'percent_complete', req.estimated_completion_time AS 'est_completion_time'
    , req.start_time AS 'request_start_time', LEFT (req.status, 15) AS 'request_status', req.command
    , req.plan_handle, req.[sql_handle], req.statement_start_offset, req.statement_end_offset, conn.most_recent_sql_handle
    , LEFT (sess.status, 15) AS 'session_status', sess.group_id, req.query_hash, req.query_plan_hash
    FROM sys.dm_exec_sessions AS sess
    LEFT OUTER JOIN sys.dm_exec_requests AS req ON sess.session_id = req.session_id
    LEFT OUTER JOIN sys.dm_exec_connections AS conn on conn.session_id = sess.session_id 
    )
, cteBlockingHierarchy (head_blocker_session_id, session_id, blocking_session_id, wait_type, wait_duration_ms,
wait_resource, statement_start_offset, statement_end_offset, plan_handle, sql_handle, most_recent_sql_handle, [Level])
AS ( SELECT head.session_id AS head_blocker_session_id, head.session_id AS session_id, head.blocking_session_id
    , head.wait_type, head.wait_time, head.wait_resource, head.statement_start_offset, head.statement_end_offset
    , head.plan_handle, head.sql_handle, head.most_recent_sql_handle, 0 AS [Level]
    FROM cteHead AS head
    WHERE (head.blocking_session_id IS NULL OR head.blocking_session_id = 0)
    AND head.session_id IN (SELECT DISTINCT blocking_session_id FROM cteHead WHERE blocking_session_id != 0)
    UNION ALL
    SELECT h.head_blocker_session_id, blocked.session_id, blocked.blocking_session_id, blocked.wait_type,
    blocked.wait_time, blocked.wait_resource, h.statement_start_offset, h.statement_end_offset,
    h.plan_handle, h.sql_handle, h.most_recent_sql_handle, [Level] + 1
    FROM cteHead AS blocked
    INNER JOIN cteBlockingHierarchy AS h ON h.session_id = blocked.blocking_session_id and h.session_id!=blocked.session_id --avoid infinite recursion for latch type of blocking
    WHERE h.wait_type COLLATE Latin1_General_BIN NOT IN ('EXCHANGE', 'CXPACKET') or h.wait_type is null
    )
SELECT bh.*, txt.text AS blocker_query_or_most_recent_query 
FROM cteBlockingHierarchy AS bh 
OUTER APPLY sys.dm_exec_sql_text (ISNULL ([sql_handle], most_recent_sql_handle)) AS txt;
```

* 장기 실행 또는 커밋되지 않은 트랜잭션을 catch 하려면 [sys.dm_tran_database_transactions](/sql/relational-databases/system-dynamic-management-views/sys-dm-tran-database-transactions-transact-sql), [sys.dm_tran_session_transactions](/sql/relational-databases/system-dynamic-management-views/sys-dm-tran-session-transactions-transact-sql), [sys.dm_exec_connections](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-connections-transact-sql)및 sys.dm_exec_sql_text를 포함 하 여 현재 열려 있는 트랜잭션을 볼 수 있도록 다른 dmv 집합을 사용 합니다. 추적 트랜잭션과 연결 된 여러 Dmv가 있습니다. 여기에서 [트랜잭션에 대 한 추가 dmv](/sql/relational-databases/system-dynamic-management-views/transaction-related-dynamic-management-views-and-functions-transact-sql) 를 참조 하십시오. 

```sql
SELECT [s_tst].[session_id],
[database_name] = DB_NAME (s_tdt.database_id),
[s_tdt].[database_transaction_begin_time], 
[sql_text] = [s_est].[text] 
FROM sys.dm_tran_database_transactions [s_tdt]
INNER JOIN sys.dm_tran_session_transactions [s_tst] ON [s_tst].[transaction_id] = [s_tdt].[transaction_id]
INNER JOIN sys.dm_exec_connections [s_ec] ON [s_ec].[session_id] = [s_tst].[session_id]
CROSS APPLY sys.dm_exec_sql_text ([s_ec].[most_recent_sql_handle]) AS [s_est];
```

* SQL의 스레드/작업 계층에 있는 참조 [sys.dm_os_waiting_tasks](/sql/relational-databases/system-dynamic-management-views/sys-dm-os-waiting-tasks-transact-sql) 입니다. 요청에 현재 발생 하 고 있는 SQL 대기 유형에 대 한 정보를 반환 합니다. Sys.dm_exec_requests 처럼 활성 요청만 sys.dm_os_waiting_tasks에 의해 반환 됩니다. 

> [!Note]
> 시간 경과에 따른 집계 된 대기 통계를 비롯 한 대기 유형에 대 한 자세한 내용은 DMV [sys.dm_db_wait_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-wait-stats-azure-sql-database)를 참조 하세요. 이 DMV는 현재 데이터베이스에 대해서만 집계 대기 통계를 반환 합니다.

* 쿼리에 의해 배치 된 잠금에 대 한 자세한 내용은 [Sys.dm_tran_locks](/sql/relational-databases/system-dynamic-management-views/sys-dm-tran-locks-transact-sql) DMV를 사용 합니다. 이 DMV는 프로덕션 SQL Server에서 많은 양의 데이터를 반환할 수 있으며 현재 보유 중인 잠금을 진단 하는 데 유용 합니다. 

Sys.dm_os_waiting_tasks에 대 한 내부 조인 때문에 다음 쿼리에서는 현재 차단 된 요청, 대기 상태 및 해당 잠금에 대 한 sys.dm_tran_locks 출력을 제한 합니다.

```sql
SELECT table_name = schema_name(o.schema_id) + '.' + o.name
, wt.wait_duration_ms, wt.wait_type, wt.blocking_session_id, wt.resource_description
, tm.resource_type, tm.request_status, tm.request_mode, tm.request_session_id
FROM sys.dm_tran_locks AS tm
INNER JOIN sys.dm_os_waiting_tasks as wt ON tm.lock_owner_address = wt.resource_address
LEFT OUTER JOIN sys.partitions AS p on p.hobt_id = tm.resource_associated_entity_id
LEFT OUTER JOIN sys.objects o on o.object_id = p.object_id or tm.resource_associated_entity_id = o.object_id
WHERE resource_database_id = DB_ID()
AND object_name(p.object_id) = '<table_name>';
```

* Dmv를 사용 하 여 쿼리 결과를 시간에 따라 저장 하면 지정 된 시간 간격 동안 차단을 검토 하 여 지속형 차단 또는 추세를 식별할 수 있는 데이터 요소가 제공 됩니다. 

## <a name="gather-information-from-extended-events"></a>확장 이벤트에서 정보 수집

위의 정보 외에도 서버에서 작업 추적을 캡처하여 Azure SQL Database에서 차단 문제를 철저히 조사 해야 하는 경우가 많습니다. 예를 들어 세션이 트랜잭션 내에서 여러 문을 실행 하는 경우 전송 된 마지막 문만 표시 됩니다. 그러나 이전 문 중 하나에서 잠금이 유지 되는 이유가 원인일 수 있습니다. 추적을 사용 하면 현재 트랜잭션 내에서 세션에 의해 실행 된 모든 명령을 볼 수 있습니다.

SQL Server에서 추적을 캡처하는 방법에는 두 가지가 있습니다. 확장 이벤트 (Xevent) 및 프로파일러 추적. 그러나 [SQL Server Profiler](/sql/tools/sql-server-profiler/sql-server-profiler) 는 Azure SQL Database에 대해 지원 되지 않는 추적 기술입니다. [확장 이벤트](/sql/relational-databases/extended-events/extended-events) 는 관찰 된 시스템에 더 많은 영향을 주지 않고 더 많은 영향을 주는 최신 추적 기술 이며, 해당 인터페이스는 SSMS (SQL Server Management Studio)에 통합 되어 있습니다. 

SSMS에서 [확장 이벤트 새 세션 마법사](/sql/relational-databases/extended-events/quick-start-extended-events-in-sql-server) 를 사용 하는 방법을 설명 하는 문서를 참조 하세요. 그러나 Azure SQL database의 경우 SSMS는 개체 탐색기의 각 데이터베이스에 확장 이벤트 하위 폴더를 제공 합니다. 확장 이벤트 세션 마법사를 사용 하 여 다음과 같은 유용한 이벤트를 캡처할 수 있습니다. 

-   범주 오류:
    -   Attention
    -   Error_reported  
    -   Execution_warning

-   범주 경고: 
    -   Missing_join_predicate

-   범주 실행:
    -   Rpc_completed
    -   Rpc_starting 
    -   Sql_batch_completed
    -   Sql_batch_starting

-   잠금
    -   Lock_deadlock

-   세션
    -   Existing_connection
    -   로그인
    -   Logout

## <a name="identify-and-resolve-common-blocking-scenarios"></a>일반적인 차단 시나리오 식별 및 해결

위의 정보를 검토 하 여 대부분의 차단 문제의 원인을 확인할 수 있습니다. 이 문서의 나머지 부분에서는이 정보를 사용 하 여 몇 가지 일반적인 차단 시나리오를 식별 하 고 해결 하는 방법을 설명 합니다. 이 토론에서는 차단 스크립트 (이전에 참조)를 사용 하 여 차단 Spid에 대 한 정보를 캡처하고 XEvent 세션을 사용 하 여 응용 프로그램 작업을 캡처한 것으로 가정 합니다.

## <a name="analyze-blocking-data"></a>차단 데이터 분석 

* Dmv sys.dm_exec_requests의 출력을 검토 하 고 sys.dm_exec_sessions blocking_these 및 session_id를 사용 하 여 차단 체인의 헤드를 확인 합니다. 이는 차단 되 고 차단 중인 요청을 가장 명확 하 게 식별 합니다. 차단 및 차단 되는 세션을 자세히 확인 합니다. 차단 체인에 대 한 공통 또는 루트가 있나요? 공통 테이블을 공유 하 고, 차단 체인에 포함 된 하나 이상의 세션에서 쓰기 작업을 수행 하 고 있습니다. 

* Dmv sys.dm_exec_requests의 출력을 검토 하 고 차단 체인의 헤드에 대 한 Spid 정보를 sys.dm_exec_sessions 합니다. 다음 필드를 찾습니다. 

    -    `sys.dm_exec_requests.status`  
    이 열은 특정 요청의 상태를 표시 합니다. 일반적으로 절전 모드 상태는 SPID가 실행을 완료 하 고 응용 프로그램이 다른 쿼리 또는 일괄 처리를 제출할 때까지 대기 중임을 나타냅니다. 실행 가능 또는 실행 중 상태는 SPID가 현재 쿼리를 처리 하 고 있음을 나타냅니다. 다음 표에서는 다양 한 상태 값에 대 한 간략 한 설명을 제공 합니다.

    | 상태 | 의미 |
    |:-|:-|
    | 배경 | SPID가 교착 상태 검색, 로그 기록기 또는 검사점과 같은 백그라운드 작업을 실행 하 고 있습니다. |
    | 중지 중 | SPID가 현재 실행 되 고 있지 않습니다. 이는 일반적으로 SPID가 응용 프로그램에서 명령을 대기 중임을 나타냅니다. |
    | 실행 중 | SPID가 현재 스케줄러에서 실행 되 고 있습니다. |
    | 실행 가능 | SPID가 스케줄러의 실행 가능한 큐에 있으며 스케줄러 시간을 얻기 위해 대기 중입니다. |
    | 일시 중단 | SPID가 잠금 또는 래치와 같은 리소스를 기다리고 있습니다. | 
                       
    -    `sys.dm_exec_sessions.open_transaction_count`  
    이 필드는이 세션에서 열린 트랜잭션 수를 알려 줍니다. 이 값이 0 보다 크면 SPID는 열린 트랜잭션 내에 있으며 트랜잭션 내의 모든 문에서 획득 한 잠금을 보유 하 고 있을 수 있습니다.

    -    `sys.dm_exec_requests.open_transaction_count`  
    마찬가지로이 필드는이 요청에서 열린 트랜잭션 수를 알려 줍니다. 이 값이 0 보다 크면 SPID는 열린 트랜잭션 내에 있으며 트랜잭션 내의 모든 문에서 획득 한 잠금을 보유 하 고 있을 수 있습니다.

    -   `sys.dm_exec_requests.wait_type`, `wait_time`, `last_wait_type`  
     `sys.dm_exec_requests.wait_type`   이 NULL 인 경우 요청은 현재 대기 중이 아닌 것으로,이  `last_wait_type`   값은 요청이 마지막으로 발생 한를 나타냅니다  `wait_type`   . 및 가장 일반적인 대기 유형에 대 한 설명에 대 한 자세한 내용은  `sys.dm_os_wait_stats` [sys.dm_os_wait_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-os-wait-stats-transact-sql)를 참조 하세요.  `wait_time`   값을 사용 하 여 요청이 진행 되 고 있는지 여부를 확인할 수 있습니다. Sys.dm_exec_requests 테이블에 대 한 쿼리가  `wait_time`   sys.dm_exec_requests의 이전 쿼리 값 보다 작은 열에 값을 반환 하는 경우 이전  `wait_time`   잠금이 획득 되 고 해제 되었으며 현재 새 잠금 (0이 아닌 것으로 가정)에서 대기 중임을 나타냅니다 `wait_time` . 이는 `wait_resource`   요청이 대기 중인 리소스를 표시 하는 sys.dm_exec_requests 간의 출력을 비교 하 여 확인할 수 있습니다.

    -   `sys.dm_exec_requests.wait_resource` 이 필드는 차단 된 요청이 대기 중인 리소스를 나타냅니다. 다음 표에서는 일반적인  `wait_resource`   형식 및 해당 의미를 보여 줍니다.

    | 리소스 | 형식 | 예제 | 설명 | 
    |:-|:-|:-|:-|
    | 테이블 | DatabaseID: ObjectID: IndexID | 탭: 5:261575970:1 | 이 경우 데이터베이스 ID 5는 pubs 예제 데이터베이스이 고 개체 ID 261575970는 titles 테이블 이며 1은 클러스터형 인덱스입니다. |
    | 페이지 | DatabaseID: FileID: PageID | 페이지: 5:1:104 | 이 경우 데이터베이스 ID 5는 pubs이 고, 파일 ID 1은 주 데이터 파일이 며, 페이지 104는 titles 테이블에 속하는 페이지입니다. 페이지가 속한 object_id 식별 하려면 동적 관리 함수 [sys.dm_db_page_info](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-page-info-transact-sql)를 사용 하 여에서 DatabaseID, FileId, PageId를 전달 `wait_resource` 합니다. | 
    | 키 | DatabaseID: Hobt_id (인덱스 키에 대 한 해시 값) | 키: 5:72057594044284928 (3300a4f361aa) | 이 경우 데이터베이스 ID 5는 Pubs 이며 Hobt_ID 72057594044284928는 object_id 261575970 (titles 테이블)에 대 한 index_id 2에 해당 합니다. Hobt_id를 특정 index_id 및 object_id에 연결 하려면 sys.debug 카탈로그 뷰를 사용 합니다. 인덱스 키 해시를 특정 키 값으로 해시 하는 방법은 없습니다. |
    | 행 | DatabaseID: FileID: PageID: Slot (row) | RID: 5:1:104:3 | 이 경우 데이터베이스 ID 5는 pubs이 고, 파일 ID 1은 주 데이터 파일이 며, 페이지 104은 titles 테이블에 속하는 페이지이 고, 슬롯 3은 페이지의 행 위치를 나타냅니다. |
    | Compile  | DatabaseID: FileID: PageID: Slot (row) | RID: 5:1:104:3 | 이 경우 데이터베이스 ID 5는 pubs이 고, 파일 ID 1은 주 데이터 파일이 며, 페이지 104은 titles 테이블에 속하는 페이지이 고, 슬롯 3은 페이지의 행 위치를 나타냅니다. |

    -    `sys.dm_tran_active_transactions`[Sys.dm_tran_active_transactions](/sql/relational-databases/system-dynamic-management-views/sys-dm-tran-active-transactions-transact-sql) DMV는 커밋 또는 롤백을 대기 하는 트랜잭션의 전체 그림을 위해 다른 dmv에 조인할 수 있는 열린 트랜잭션에 대 한 데이터를 포함 합니다. 다음 쿼리를 사용 하 여 [sys.dm_tran_session_transactions](/sql/relational-databases/system-dynamic-management-views/sys-dm-tran-session-transactions-transact-sql)을 포함 하 여 다른 dmv에 조인 된 열려 있는 트랜잭션에 대 한 정보를 반환할 수 있습니다. 트랜잭션의 현재 상태, `transaction_begin_time` 및 기타 상황 데이터를 고려 하 여 차단의 원본 인지 여부를 평가 합니다.

    ```sql
    SELECT tst.session_id, [database_name] = db_name(s.database_id)
    , tat.transaction_begin_time
    , transaction_duration_s = datediff(s, tat.transaction_begin_time, sysdatetime()) 
    , transaction_type = CASE tat.transaction_type  WHEN 1 THEN 'Read/write transaction'
                                                    WHEN 2 THEN 'Read-only transaction'
                                                    WHEN 3 THEN 'System transaction'
                                                    WHEN 4 THEN 'Distributed transaction' END
    , input_buffer = ib.event_info, tat.transaction_uow     
    , transaction_state  = CASE tat.transaction_state    
                WHEN 0 THEN 'The transaction has not been completely initialized yet.'
                WHEN 1 THEN 'The transaction has been initialized but has not started.'
                WHEN 2 THEN 'The transaction is active - has not been committed or rolled back.'
                WHEN 3 THEN 'The transaction has ended. This is used for read-only transactions.'
                WHEN 4 THEN 'The commit process has been initiated on the distributed transaction.'
                WHEN 5 THEN 'The transaction is in a prepared state and waiting resolution.'
                WHEN 6 THEN 'The transaction has been committed.'
                WHEN 7 THEN 'The transaction is being rolled back.'
                WHEN 8 THEN 'The transaction has been rolled back.' END 
    , transaction_name = tat.name, request_status = r.status
    , azure_dtc_state = CASE tat.dtc_state 
                        WHEN 1 THEN 'ACTIVE'
                        WHEN 2 THEN 'PREPARED'
                        WHEN 3 THEN 'COMMITTED'
                        WHEN 4 THEN 'ABORTED'
                        WHEN 5 THEN 'RECOVERED' END
    , tst.is_user_transaction, tst.is_local
    , session_open_transaction_count = tst.open_transaction_count  
    , s.host_name, s.program_name, s.client_interface_name, s.login_name, s.is_user_process
    FROM sys.dm_tran_active_transactions tat 
    INNER JOIN sys.dm_tran_session_transactions tst  on tat.transaction_id = tst.transaction_id
    INNER JOIN Sys.dm_exec_sessions s on s.session_id = tst.session_id 
    LEFT OUTER JOIN sys.dm_exec_requests r on r.session_id = s.session_id
    CROSS APPLY sys.dm_exec_input_buffer(s.session_id, null) AS ib;
    ```

    -   기타 열

        [Sys.dm_exec_sessions](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-sessions-transact-sql) 및 [sys.dm_exec_request](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql) 의 나머지 열은 문제 루트에 대 한 통찰력을 제공할 수 있습니다. 이러한 유용성은 문제의 상황에 따라 달라 집니다. 예를 들어, 특정 클라이언트 (호스트 이름), 특정 네트워크 라이브러리 (net_library), SPID에서 제출한 마지막 일괄 처리가 sys.dm_exec_sessions 된 시간, `last_request_start_time` sys.dm_exec_requests에서를 사용 하 여 실행 된 시간 등의 특정 클라이언트 에서만 문제가 발생 하는지 확인할 수 있습니다 `start_time` .


## <a name="common-blocking-scenarios"></a>일반적인 차단 시나리오

아래 표에서는 일반적인 증상을 가능한 원인에 매핑합니다.  

`wait_type`, `open_transaction_count` 및 열은 `status` [sys.dm_exec_request](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql)에서 반환 되는 정보를 참조 하 고 다른 열은 [sys.dm_exec_sessions](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-sessions-transact-sql)에서 반환 될 수 있습니다. "해결 되었습니까?" 열은 차단이 자체적으로 해결 되는지 여부 또는 명령을 통해 세션을 종료 해야 하는지 여부를 나타냅니다 `KILL` . 자세한 내용은 [KILL (transact-sql)](/sql/t-sql/language-elements/kill-transact-sql)을 참조 하세요.

| 시나리오 | Waittype | Open_Tran | 상태 | 확인? | 기타 증상 |  
|:-|:-|:-|:-|:-|:-|--|
| 1 | NOT NULL | >= 0 | runnable | 쿼리가 완료 되 면 예입니다. | Sys.dm_exec_sessions **읽기**, **cpu_time** 및/또는 **memory_usage** 열은 시간이 지남에 따라 증가 합니다. 작업을 완료 하면 쿼리의 기간이 높아집니다. |
| 2 | NULL | \>0 | sleeping | 아니요. 그러나 SPID는 중지할 수 있습니다. | 이 SPID에 대 한 확장 이벤트 세션에서 쿼리 제한 시간 또는 취소가 발생 했음을 나타내는 주의 신호가 표시 될 수 있습니다. |
| 3 | NULL | \>= 0 | runnable | 아니요. 는 클라이언트가 모든 행을 페치 하거나 연결을 닫을 때까지 확인 되지 않습니다. SPID는 중지할 수 있지만 최대 30 초까지 걸릴 수 있습니다. | Open_transaction_count = 0이 고 SPID가 잠금 상태를 유지 하는 동안 트랜잭션 격리 수준이 기본값 (읽기 COMMMITTED) 인 경우이 문제가 발생할 수 있습니다. |  
| 4 | 상황에 따라 다름 | \>= 0 | runnable | 아니요. 는 클라이언트가 쿼리를 취소 하거나 연결을 닫을 때까지 확인 되지 않습니다. Spid는 중지할 수 있지만 최대 30 초까지 걸릴 수 있습니다. | 차단 체인의 헤드에 있는 SPID의 sys.dm_exec_sessions에 있는 **호스트 이름** 열은 차단 중인 spid 중 하 나와 동일 합니다. |  
| 5 | NULL | \>0 | 롤백 | 예. | 이 SPID의 확장 이벤트 세션에서 쿼리 제한 시간 또는 취소가 발생 했거나 단순히 rollback 문이 실행 되었음을 나타내는 주의가 표시 될 수 있습니다. |  
| 6 | NULL | \>0 | sleeping | 올라가. Windows NT에서 세션이 더 이상 활성 상태가 아닌 것으로 확인 되 면 Azure SQL Database 연결이 끊어집니다. | `last_request_start_time`Sys.dm_exec_sessions 값이 현재 시간 보다 빠릅니다. |

이러한 시나리오에서는 다음과 같은 시나리오가 확장 될 것입니다. 

1.  실행 시간이 긴 일반적인 실행 쿼리로 인 한 차단

    **해결** 방법:이 유형의 블로킹 문제에 대 한 해결 방법은 쿼리를 최적화 하는 방법을 찾는 것입니다. 실제로이 클래스의 차단 문제는 성능 문제 일 수 있으며,이에 따라 이러한 문제를 해결 해야 합니다. 실행 속도가 낮은 특정 쿼리 문제를 해결 하는 방법에 대 한 자세한 내용은 [SQL Server에서 느리게 실행 되는 쿼리를 해결 하는 방법](/troubleshoot/sql/performance/troubleshoot-slow-running-queries)을 참조 하세요. 자세한 내용은 [성능 모니터링 및 조정](/sql/relational-databases/performance/monitor-and-tune-for-performance)을 참조하세요. 

    SSMS의 [쿼리 저장소](/sql/relational-databases/performance/best-practice-with-the-query-store) 에 있는 보고서는 가장 비용이 많이 드는 쿼리를 식별 하는 데 유용한 도구 이며 최적이 지 않은 실행 계획 이기도 합니다. 또한 [Query Performance Insight](query-performance-insight-use.md)를 포함 하 여 Azure SQL database에 대 한 Azure Portal의 [지능형 성능](intelligent-insights-overview.md) 섹션을 검토 합니다.

    다른 사용자를 차단 하는 장기 실행 쿼리가 있고 최적화할 수 없는 경우 OLTP 환경에서 [데이터베이스의 동기 읽기 전용 복제본](read-scale-out.md)인 전용 보고 시스템으로 이동 하는 것이 좋습니다.

1.  커밋되지 않은 트랜잭션이 있는 중지 SPID로 인 한 차단

    이 차단 유형은 일반적으로 명령을 중지 하거나 대기 하는 SPID로 식별 될 수 있지만 트랜잭션 중첩 수준 ( `@@TRANCOUNT` `open_transaction_count` sys.dm_exec_requests)이 0 보다 큽니다. 응용 프로그램에서 쿼리 제한 시간을 사용 하거나 필요한 수의 롤백 및/또는 커밋 문을 실행 하지 않고 취소를 실행 하는 경우이 문제가 발생할 수 있습니다. SPID는 쿼리 제한 시간 또는 취소를 수신 하면 현재 쿼리 및 일괄 처리를 종료 하지만 트랜잭션을 자동으로 롤백하거나 커밋하지 않습니다. 이는 단일 쿼리를 취소 하 여 전체 트랜잭션을 롤백해야 하는 것으로 간주할 수 Azure SQL Database 않으므로 응용 프로그램은이를 담당 합니다. 쿼리 제한 시간 또는 취소는 확장 이벤트 세션에서 SPID에 대 한 주의 신호 이벤트로 표시 됩니다.

    커밋되지 않은 명시적 트랜잭션을 보여 주려면 다음 쿼리를 실행 합니다.

    ```sql
    CREATE TABLE #test (col1 INT);
    INSERT INTO #test SELECT 1;
    BEGIN TRAN
    UPDATE #test SET col1 = 2 where col1 = 1;
    ```

    그런 다음 동일한 창에서이 쿼리를 실행 합니다.
    ```sql
    SELECT @@TRANCOUNT;
    ROLLBACK TRAN
    DROP TABLE #test;
    ```

    두 번째 쿼리의 출력은 트랜잭션 중첩 수준이 one 임을 나타냅니다. 트랜잭션에서 획득 한 모든 잠금은 트랜잭션이 커밋되거나 롤백될 때까지 유지 됩니다. 응용 프로그램이 트랜잭션을 명시적으로 열고 커밋하는 경우 통신 또는 기타 오류로 인해 세션 및 해당 트랜잭션이 열린 상태로 유지 될 수 있습니다. 

    Sys.dm_tran_active_transactions에 따라 위의 스크립트를 사용 하 여 현재 커밋되지 않은 트랜잭션을 식별 합니다.

    **해결** 방법:

     -   또한이 클래스의 차단 문제는 성능 문제가 될 수도 있으며,이에 따라 이러한 문제를 해결 해야 합니다. 쿼리 실행 시간을 감소 시킬 수 있는 경우 쿼리 제한 시간 또는 취소가 발생 하지 않습니다. 응용 프로그램에서 발생 해야 하는 시간 제한 또는 취소 시나리오를 처리할 수 있는 것이 중요 하지만 쿼리의 성능을 검사 하는 것이 유용할 수도 있습니다. 
     
     -    응용 프로그램은 트랜잭션 중첩 수준을 적절 하 게 관리 해야 합니다. 그렇지 않으면 이러한 방식으로 쿼리를 취소 한 후 차단 문제가 발생할 수 있습니다. 다음을 살펴보세요.  

            *    클라이언트 응용 프로그램의 오류 처리기에서 `IF @@TRANCOUNT > 0 ROLLBACK TRAN` 다음 오류를 실행 합니다. 클라이언트 응용 프로그램에서 트랜잭션이 열려 있다고 생각 하지 않는 경우에도 마찬가지입니다. 일괄 처리 중에 호출 된 저장 프로시저에서 클라이언트 응용 프로그램의 지식 없이 트랜잭션을 시작할 수 있으므로 열려 있는 트랜잭션을 확인 해야 합니다. 쿼리를 취소 하는 것과 같은 특정 조건에서는 프로시저에서 현재 문을 실행 하는 것을 방지 하므로 프로시저에 트랜잭션을 확인 하 고 중단 하는 논리가 있는 경우 `IF @@ERROR <> 0` 에도이 롤백 코드는 이러한 경우에 실행 되지 않습니다.  
            *    연결 풀링을 응용 프로그램에서 사용 하는 경우 웹 기반 응용 프로그램과 같은 풀로 다시 연결을 해제 하기 전에 연결 풀링을 일시적으로 사용 하지 않도록 설정 하면 클라이언트 응용 프로그램에서 오류를 적절히 처리 하도록 수정할 때까지 문제를 완화할 수 있습니다. 연결 풀링을 사용 하지 않도록 설정 하면 연결을 해제 하면 Azure SQL Database 연결의 물리적 연결이 끊어지고 서버가 열려 있는 모든 트랜잭션을 롤백합니다.  
            *    `SET XACT_ABORT ON`연결에 사용 하거나 트랜잭션을 시작 하 고 오류 발생 시 정리 하지 않는 저장 프로시저에서를 사용 합니다. 런타임 오류가 발생 하는 경우이 설정은 열린 모든 트랜잭션을 중단 하 고 클라이언트에 컨트롤을 반환 합니다. 자세한 내용은 [SET XACT_ABORT (transact-sql)](/sql/t-sql/statements/set-xact-abort-transact-sql)를 참조 하세요.
    > [!NOTE]
    > 연결 풀에서 다시 사용 될 때까지 연결이 다시 설정 되지 않으므로 사용자가 트랜잭션을 열고 연결 풀에 대 한 연결을 해제할 수 있습니다. 그러나 몇 초 동안 다시 사용 되지 않을 수도 있습니다 .이 시간 동안 트랜잭션이 열려 있는 상태로 유지 됩니다. 연결이 다시 사용 되지 않는 경우 연결이 시간 초과 되 고 연결 풀에서 제거 되 면 트랜잭션이 중단 됩니다. 따라서 클라이언트 응용 프로그램이 해당 오류 처리기에서 트랜잭션을 중단 하거나를 사용 하 여 `SET XACT_ABORT ON` 이러한 잠재적 지연을 방지 하는 것이 좋습니다.

    > [!CAUTION]
    > 다음 `SET XACT_ABORT ON` 으로 오류를 발생 시키는 문 뒤의 t-sql 문은 실행 되지 않습니다. 이는 기존 코드의 의도 된 흐름에 영향을 줄 수 있습니다.

1.  해당 클라이언트 응용 프로그램이 완료 될 때까지 모든 결과 행을 가져오지 않은 SPID에의 한 차단

    서버에 쿼리를 보낸 후 모든 응용 프로그램은 모든 결과 행을 즉시 인출 하 여 완료 해야 합니다. 응용 프로그램에서 모든 결과 행을 인출 하지 않는 경우 다른 사용자를 차단 하 여 테이블에 대 한 잠금을 유지할 수 있습니다. 서버에 SQL 문을 투명 하 게 전송 하는 응용 프로그램을 사용 하는 경우 응용 프로그램은 모든 결과 행을 가져와야 합니다. 그렇지 않은 경우 (이 작업을 수행 하도록 구성할 수 없는 경우) 차단 문제를 해결할 수 없습니다. 문제를 방지 하기 위해 잘못 동작 하는 응용 프로그램을 보고 또는 의사 결정 지원 데이터베이스로 제한할 수 있습니다.
    
    > [!NOTE]
    > Azure SQL Database에 연결 하는 응용 프로그램에 대 한 [재시도 논리에 대 한 지침](./troubleshoot-common-connectivity-issues.md#retry-logic-for-transient-errors) 을 참조 하세요. 
    
    **해결** 방법: 완료 될 결과의 모든 행을 인출 하기 위해 응용 프로그램을 다시 작성 해야 합니다. 쿼리의 [ORDER by 절에서 OFFSET 및 FETCH](/sql/t-sql/queries/select-order-by-clause-transact-sql#using-offset-and-fetch-to-limit-the-rows-returned) 를 사용 하 여 서버 쪽 페이징을 수행 하지는 않습니다.

1.  롤백 상태인 SPID로 인 한 차단

    사용자 정의 트랜잭션 외부에서 종료 되거나 취소 된 데이터 수정 쿼리가 롤백됩니다. 이는 클라이언트 네트워크 세션의 연결을 끊는 부작용으로 발생 하거나, 요청이 교착 상태가 발생 한 것으로 선택 된 경우에도 발생할 수 있습니다. 이는 롤백 **명령을** 나타낼 수 있는 sys.dm_exec_requests의 출력을 관찰 하 여 확인할 수 있으며 **percent_complete 열** 에 진행률이 표시 될 수 있습니다. 

    2019에 도입 된 [가속화 된 데이터베이스 복구 기능](../accelerated-database-recovery.md) 덕분에 긴 롤백이 거의 발생 하지 않습니다.
    
    **해결** 방법: SPID가 적용 된 변경 내용을 롤백할 때까지 기다립니다. 

    이러한 상황을 방지 하려면 OLTP 시스템에서 사용량이 많은 시간 동안 대량 일괄 쓰기 작업 또는 인덱스 생성 또는 유지 관리 작업을 수행 하지 마십시오. 가능 하면 활동이 적은 기간 동안 이러한 작업을 수행 합니다.

1.  분리 된 연결로 인 한 차단

    클라이언트 응용 프로그램에서 오류 또는 클라이언트 워크스테이션을 다시 시작 하는 경우 일부 조건에서 서버에 대 한 네트워크 세션이 즉시 취소 되지 않을 수 있습니다. Azure SQL Database 관점에서 클라이언트는 여전히 존재 하는 것 처럼 보이지만 획득 한 모든 잠금은 계속 유지 될 수 있습니다. 자세한 내용은 [SQL Server에서 분리 된 연결 문제를 해결 하는 방법](/sql/t-sql/language-elements/kill-transact-sql#remarks)을 참조 하세요. 

    **해결** 방법: 클라이언트 응용 프로그램의 리소스를 적절 하 게 정리 하지 않고 연결을 끊은 경우 명령을 사용 하 여 SPID를 종료할 수 있습니다 `KILL` . `KILL`명령은 SPID 값을 입력으로 사용 합니다. 예를 들어 SPID 99를 중지 하려면 다음 명령을 실행 합니다.

    ```sql
    KILL 99
    ```

## <a name="see-also"></a>참고 항목

* [Azure SQL Database 및 Azure SQL Managed Instance의 모니터링 및 성능 튜닝](./monitor-tune-overview.md)
* [쿼리 저장소를 사용한 성능 모니터링](/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store)
* [트랜잭션 잠금 및 행 버전 관리 지침](/sql/relational-databases/sql-server-transaction-locking-and-row-versioning-guide)
* [SET TRANSACTION ISOLATION LEVEL](/sql/t-sql/statements/set-transaction-isolation-level-transact-sql)
* [빠른 시작: SQL Server의 확장 이벤트](/sql/relational-databases/extended-events/quick-start-extended-events-in-sql-server)
* [AI를 사용하여 데이터베이스 성능을 모니터링하고 문제를 해결하는 Intelligent Insights](intelligent-insights-overview.md)

## <a name="learn-more"></a>자세한 정보

* [Azure SQL Database: 자동 튜닝을 사용 하 여 성능 튜닝 향상](https://channel9.msdn.com/Shows/Data-Exposed/Azure-SQL-Database-Improving-Performance-Tuning-with-Automatic-Tuning)
* [자동 조정으로 Azure SQL Database 성능 향상](https://channel9.msdn.com/Shows/Azure-Friday/Improve-Azure-SQL-Database-Performance-with-Automatic-Tuning)
* [Azure SQL을 사용하여 일관된 성능 제공](/learn/modules/azure-sql-performance/)
* [Azure SQL Database 및 Azure SQL Managed Instance를 사용 하 여 연결 문제 및 기타 오류 해결](troubleshoot-common-errors-issues.md)
* [일시적인 오류 처리](/aspnet/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/transient-fault-handling)
