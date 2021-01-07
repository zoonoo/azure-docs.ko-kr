---
title: SQL 커넥터에서 장기 실행 저장 프로시저 처리
description: Azure Logic Apps에서 SQL 커넥터를 사용 하는 경우 제한 시간이 초과 되는 저장 프로시저를 처리 하는 방법
services: logic-apps
ms.suite: integration
ms.reviewer: camerost, logicappspm
ms.topic: conceptual
ms.date: 10/27/2020
ms.openlocfilehash: f5b04c563dc81497f591788dc4890d379c0f898f
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93102965"
---
# <a name="handle-stored-procedure-timeouts-in-the-sql-connector-for-azure-logic-apps"></a>Azure Logic Apps 용 SQL connector에서 저장 프로시저 시간 제한을 처리 합니다.

논리 앱이 결과 집합을 사용 하 여 [SQL connector](../connectors/connectors-create-api-sqlazure.md) 가 동시에 모든 결과를 반환 하지 않거나 결과 집합의 크기와 구조를 더 세부적으로 제어 하려는 경우 결과를 원하는 방식으로 구성 하는 [저장 프로시저](/sql/relational-databases/stored-procedures/stored-procedures-database-engine) 를 만들 수 있습니다. Sql 커넥터는 [Azure Logic Apps](../logic-apps/logic-apps-overview.md) 를 사용 하 여 액세스할 수 있는 여러 백 엔드 기능을 제공 하므로 sql database 테이블로 작업 하는 비즈니스 작업을 보다 쉽게 자동화할 수 있습니다.

예를 들어 여러 행을 가져오거나 삽입할 때 논리 앱은 이러한 [제한](../logic-apps/logic-apps-limits-and-config.md)내에서 [ **until** 루프](../logic-apps/logic-apps-control-flow-loops.md#until-loop) 를 사용 하 여 이러한 행을 반복할 수 있습니다. 그러나 논리 앱이 수천 또는 수백만 개의 행으로 작업 해야 하는 경우에는 데이터베이스에 대 한 호출로 인해 발생 하는 비용을 최소화 하는 것이 좋습니다. 자세한 내용은 [SQL 커넥터를 사용 하 여 대량 데이터 처리](../connectors/connectors-create-api-sqlazure.md#handle-bulk-data)를 참조 하세요.

<a name="timeout-limit"></a>

## <a name="timeout-limit-on-stored-procedure-execution"></a>저장 프로시저 실행에 대 한 제한 시간 제한

SQL 커넥터의 저장 프로시저 시간 제한 제한은 [2 분 미만](/connectors/sql/#known-issues-and-limitations)입니다. 일부 저장 프로시저는이 제한 시간 보다 오래 걸리고 오류가 발생할 수 있습니다 `504 Timeout` . 이러한 장기 실행 프로세스는 이러한 용도로 명시적으로 저장 프로시저로 코딩 됩니다. 제한 시간 제한으로 인해 Azure Logic Apps에서 이러한 프로시저를 호출 하면 문제가 발생할 수 있습니다. SQL connector는 기본적으로 비동기 모드를 지원 하지 않지만이 문제를 해결 하 고 SQL 완료 트리거, 네이티브 SQL 통과 쿼리, 상태 테이블 및 서버 쪽 작업을 사용 하 여이 모드를 시뮬레이션할 수 있습니다. 이 작업의 경우 [Azure SQL Database](../azure-sql/database/sql-database-paas-overview.md)에 대해 [Azure 탄력적 작업 에이전트](../azure-sql/database/elastic-jobs-overview.md) 를 사용할 수 있습니다. [온-프레미스](/sql/sql-server/sql-server-technical-documentation) 및 [Azure SQL Managed Instance](../azure-sql/managed-instance/sql-managed-instance-paas-overview.md)SQL Server [SQL Server 에이전트](/sql/ssms/agent/sql-server-agent)를 사용할 수 있습니다.

예를 들어 실행이 완료 되는 데 제한 시간 제한 보다 오래 걸리는 장기 실행 저장 프로시저가 있다고 가정 합니다. SQL 커넥터를 사용 하 여 논리 앱에서이 저장 프로시저를 실행 하면 `HTTP 504 Gateway Timeout` 결과로 오류가 발생 합니다.

```sql
CREATE PROCEDURE [dbo].[WaitForIt]
   @delay char(8) = '00:03:00'
AS
BEGIN
   SET NOCOUNT ON;
   WAITFOR DELAY @delay
END
```

저장 프로시저를 직접 호출 하는 대신 *작업 에이전트* 를 사용 하 여 백그라운드에서 프로시저를 비동기적으로 실행할 수 있습니다. 논리 앱을 통해 상호 작용할 수 있는 상태 테이블에 입력 및 출력을 저장할 수 있습니다. 입력 및 출력이 필요 하지 않거나 저장 프로시저의 테이블에 결과를 이미 작성 한 경우에는이 방법을 단순화할 수 있습니다.

> [!IMPORTANT]
> 저장 프로시저와 모든 작업이 *idempotent* 되었는지 확인 합니다. 즉, 결과에 영향을 주지 않고 여러 번 실행할 수 있습니다. 비동기 처리가 실패 하거나 시간이 초과 되 면 작업 에이전트가 단계를 다시 시도 하 고, 따라서 저장 프로시저를 여러 번 다시 시도할 수 있습니다. 출력 복제를 방지 하려면 개체를 만들기 전에 이러한 [모범 사례와 방법을](../azure-sql/database/elastic-jobs-overview.md#idempotent-scripts)검토 합니다.

다음 섹션에서는 Azure SQL Database에 대해 Azure 탄력적 작업 에이전트를 사용 하는 방법을 설명 합니다. SQL Server 및 Azure SQL Managed Instance의 경우 SQL Server 에이전트를 사용할 수 있습니다. 일부 관리 정보는 서로 다르지만 기본 단계는 Azure SQL Database에 대 한 작업 에이전트 설정과 동일 하 게 유지 됩니다.

<a name="azure-sql-database"></a>

## <a name="job-agent-for-azure-sql-database"></a>Azure SQL Database에 대 한 작업 에이전트

[Azure SQL Database](../azure-sql/database/sql-database-paas-overview.md)에 대 한 저장 프로시저를 실행할 수 있는 작업을 만들려면 [Azure 탄력적 작업 에이전트](../azure-sql/database/elastic-jobs-overview.md)를 사용 합니다. Azure Portal에서 작업 에이전트를 만듭니다. 이 방법은 에이전트에서 사용 하는 데이터베이스에 *에이전트 데이터베이스* 라고도 하는 여러 저장 프로시저를 추가 합니다. 그런 다음 대상 데이터베이스에서 저장 프로시저를 실행 하 고 완료 되 면 출력을 캡처하는 작업을 만들 수 있습니다.

작업을 만들려면 먼저 [Azure 탄력적 작업 에이전트에 대 한 전체 설명서](../azure-sql/database/elastic-jobs-overview.md)에 설명 된 대로 사용 권한, 그룹 및 대상을 설정 해야 합니다. 다음 섹션에 설명 된 대로 대상 데이터베이스에 지원 테이블을 만들어야 합니다.

<a name="create-state-table"></a>

### <a name="create-state-table-for-registering-parameters-and-storing-inputs"></a>매개 변수 등록 및 입력 저장을 위한 상태 테이블 만들기

SQL 에이전트 작업은 입력 매개 변수를 허용 하지 않습니다. 대신 대상 데이터베이스에서 매개 변수를 등록 하 고 저장 프로시저를 호출 하는 데 사용할 입력을 저장 하는 상태 테이블을 만듭니다. 모든 에이전트 작업 단계는 대상 데이터베이스에 대해 실행 되지만 작업의 저장 프로시저는 에이전트 데이터베이스에 대해 실행 됩니다. 

상태 테이블을 만들려면 다음 스키마를 사용 합니다.

```sql
CREATE TABLE [dbo].[LongRunningState](
   [jobid] [uniqueidentifier] NOT NULL,
   [rowversion] [timestamp] NULL,
   [parameters] [nvarchar](max) NULL,
   [start] [datetimeoffset](7) NULL,
   [complete] [datetimeoffset](7) NULL,
   [code] [int] NULL,
   [result] [nvarchar](max) NULL,
   CONSTRAINT [PK_LongRunningState] PRIMARY KEY CLUSTERED
      (   [jobid] ASC
      )WITH (STATISTICS_NORECOMPUTE = OFF, IGNORE_DUP_KEY = OFF) ON [PRIMARY]
      ) ON [PRIMARY] TEXTIMAGE_ON [PRIMARY]
```

결과 테이블이 [SQL Server Management Studio (SMSS)](/sql/ssms/download-sql-server-management-studio-ssms)에서 어떻게 표시 되는지는 다음과 같습니다.

![저장 프로시저에 대 한 입력을 저장 하는 생성 된 상태 테이블을 보여 주는 스크린샷](media/handle-long-running-stored-procedures-sql-connector/state-table-input-parameters.png)

성능 향상을 위해 에이전트 작업에서 연결 된 레코드를 찾을 수 있는지 확인 하기 위해 테이블은 작업 실행 ID ()를 `jobid` 기본 키로 사용 합니다. 원하는 경우 입력 매개 변수에 대해 개별 열을 추가할 수도 있습니다. 앞에서 설명한 스키마는 일반적으로 여러 매개 변수를 처리할 수 있지만에서 계산 되는 크기로 제한 됩니다 `NVARCHAR(MAX)` .

<a name="create-top-level-job"></a>

### <a name="create-a-top-level-job-to-run-the-stored-procedure"></a>최상위 작업을 만들어 저장 프로시저를 실행 합니다.

장기 실행 저장 프로시저를 실행 하려면 에이전트 데이터베이스에이 최상위 작업 에이전트를 만듭니다.

```sql
EXEC jobs.sp_add_job 
   @job_name='LongRunningJob',
   @description='Execute Long-Running Stored Proc',
   @enabled = 1
```

이제 저장 프로시저를 매개 변수화 하 고, 실행 하 고, 완료 하는 단계를 작업에 추가 합니다. 기본적으로 작업 단계는 12 시간 후에 시간 초과 됩니다. 저장 프로시저에 더 많은 시간이 필요 하거나 이전에 프로시저의 시간을 제한 하려면 `step_timeout_seconds` 매개 변수를 초 단위로 지정 된 다른 값으로 변경할 수 있습니다. 기본적으로 한 단계에는 각 다시 시도 사이에 백오프 시간 제한이 있는 10 개의 기본 제공 재시도를 사용 하 여 이점에 사용할 수 있습니다.

추가 하는 단계는 다음과 같습니다.

1. 테이블에 매개 변수가 나타날 때까지 기다립니다 `LongRunningState` .

   이 첫 번째 단계에서는 테이블에 매개 변수가 추가 될 때까지 대기 `LongRunningState` 하며,이는 작업이 시작 된 직후에 발생 합니다. 작업 실행 ID ()가 `jobid` 테이블에 추가 되지 않는 경우에 `LongRunningState` 만 단계가 실패 하 고 기본 다시 시도 또는 백오프 시간 제한은 대기 작업을 수행 합니다.

   ```sql
   EXEC jobs.sp_add_jobstep
      @job_name='LongRunningJob',
      @step_name= 'Parameterize WaitForIt',
      @step_timeout_seconds = 30,
      @command= N'
         IF NOT EXISTS(SELECT [jobid] FROM [dbo].[LongRunningState]
            WHERE jobid = $(job_execution_id))
            THROW 50400, ''Failed to locate call parameters (Step1)'', 1',
      @credential_name='JobRun',
      @target_group_name='DatabaseGroupLongRunning'
   ```

1. 상태 테이블에서 매개 변수를 쿼리하고 저장 프로시저에 전달 합니다. 이 단계에서는 백그라운드 에서도 프로시저를 실행 합니다. 

   저장 프로시저에 매개 변수가 필요 하지 않은 경우에는 저장 프로시저를 직접 호출 하면 됩니다. 그렇지 않고 매개 변수를 전달 하려면를 사용 합니다 .이 매개 `@timespan` `@callparams` 변수를 확장 하 여 추가 매개 변수를 전달할 수도 있습니다.

   ```sql
   EXEC jobs.sp_add_jobstep
      @job_name='LongRunningJob',
      @step_name='Execute WaitForIt',
      @command=N'
         DECLARE @timespan char(8)
         DECLARE @callparams NVARCHAR(MAX)
         SELECT @callparams = [parameters] FROM [dbo].[LongRunningState]
            WHERE jobid = $(job_execution_id))
         SET @timespan = @callparams
         EXECUTE [dbo].[WaitForIt] @delay = @timespan', 
      @credential_name='JobRun',
      @target_group_name='DatabaseGroupLongRunning'
   ```

1. 작업을 완료 하 고 결과를 기록 합니다.

   ```sql
   EXEC jobs.sp_add_jobstep
      @job_name='LongRunningJob',
      @step_name='Complete WaitForIt',
      @command=N'
         UPDATE [dbo].[LongRunningState]
            SET [complete] = GETUTCDATE(),
               [code] = 200,
               [result] = ''Success''
            WHERE jobid = $(job_execution_id)',
      @credential_name='JobRun',
      @target_group_name='DatabaseGroupLongRunning'
   ```

<a name="start-job-pass-parameters"></a>

### <a name="start-the-job-and-pass-the-parameters"></a>작업을 시작 하 고 매개 변수를 전달 합니다.

작업을 시작 하려면 [ **SQL 쿼리 실행** 작업과](/connectors/sql/#execute-a-sql-query-(v2)) 함께 통과 기본 쿼리를 사용 하 고 작업의 매개 변수를 상태 테이블에 즉시 푸시합니다. `jobid`대상 테이블의 특성에 **대 한** 입력을 제공 하기 위해 Logic Apps 이전 작업의 테이블 출력을 반복 하는 for each 루프를 추가 합니다. 각 작업 실행 ID에 대해 동적 데이터 출력을 사용 하는 **행 삽입** 작업을 실행 하 여 `ResultSets JobExecutionId` 작업에서 압축을 풀고 대상 저장 프로시저에 전달 하는 매개 변수를 추가 합니다.

![작업을 시작 하 고 저장 프로시저에 매개 변수를 전달 하는 데 사용할 동작을 보여 주는 스크린샷](media/handle-long-running-stored-procedures-sql-connector/start-job-actions.png)

작업이 완료 되 면 `LongRunningState` [ **항목이 수정 될 때** 트리거](/connectors/sql/#when-an-item-is-modified-(v2))를 사용 하 여 결과를 쉽게 트리거할 수 있도록 작업이 테이블을 업데이트 합니다. 출력이 필요 하지 않거나 출력 테이블을 모니터링 하는 트리거가 이미 있는 경우이 부분을 건너뛸 수 있습니다.

![항목이 수정 된 경우에 대 한 SQL 트리거를 보여 주는 스크린샷](media/handle-long-running-stored-procedures-sql-connector/trigger-on-results-after-job-completes.png)

<a name="sql-on-premises-or-managed-instance"></a>

## <a name="job-agent-for-sql-server-or-azure-sql-managed-instance"></a>SQL Server 또는 Azure SQL Managed Instance에 대 한 작업 에이전트

동일한 시나리오의 경우 [온-프레미스](/sql/sql-server/sql-server-technical-documentation) 및 [Azure SQL Managed Instance](../azure-sql/managed-instance/sql-managed-instance-paas-overview.md)SQL Server에 대 한 [SQL Server 에이전트](/sql/ssms/agent/sql-server-agent) 를 사용할 수 있습니다. 일부 관리 정보는 서로 다르지만 기본 단계는 Azure SQL Database에 대 한 작업 에이전트 설정과 동일 하 게 유지 됩니다.

## <a name="next-steps"></a>다음 단계

[SQL Server, Azure SQL Database 또는 Azure SQL에 연결 Managed Instance](../connectors/connectors-create-api-sqlazure.md)

