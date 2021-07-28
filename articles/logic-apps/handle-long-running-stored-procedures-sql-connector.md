---
title: SQL 커넥터에서 장기 실행 저장 프로시저 처리
description: Azure Logic Apps에서 SQL 커넥터를 사용할 때 시간 초과되는 저장 프로시저를 처리하는 방법
services: logic-apps
ms.suite: integration
ms.reviewer: camerost, logicappspm
ms.topic: conceptual
ms.date: 10/27/2020
ms.openlocfilehash: f5b04c563dc81497f591788dc4890d379c0f898f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "93102965"
---
# <a name="handle-stored-procedure-timeouts-in-the-sql-connector-for-azure-logic-apps"></a>Azure Logic Apps용 SQL 커넥터에서 저장 프로시저 시간 초과 처리

논리 앱이 너무 큰 결과 집합으로 작업하여 [SQL 커넥터](../connectors/connectors-create-api-sqlazure.md)가 동시에 모든 결과를 반환하지 않거나 결과 집합의 크기와 구조를 더 세부적으로 제어하려는 경우, 결과를 원하는 방식으로 구성하는 [저장 프로시저](/sql/relational-databases/stored-procedures/stored-procedures-database-engine)를 만들 수 있습니다. SQL 커넥터는 [Azure Logic Apps](../logic-apps/logic-apps-overview.md)를 사용하여 액세스할 수 있는 여러 백 엔드 기능을 제공하므로 SQL 데이터베이스 테이블로 작업하는 비즈니스 작업을 더 쉽게 자동화할 수 있습니다.

예를 들어 여러 행을 가져오거나 삽입하는 경우 논리 앱은 이 [한도](../logic-apps/logic-apps-limits-and-config.md) 내에서 [**until** 루프](../logic-apps/logic-apps-control-flow-loops.md#until-loop)를 사용하여 해당 행을 반복할 수 있습니다. 하지만 논리 앱에서 수천 또는 수백만 개의 행으로 작업해야 하는 경우 데이터베이스 호출로 인한 비용을 최소화하는 것이 좋습니다. 자세한 내용은 [SQL 커넥터를 사용하여 대량 데이터 처리](../connectors/connectors-create-api-sqlazure.md#handle-bulk-data)를 참조하세요.

<a name="timeout-limit"></a>

## <a name="timeout-limit-on-stored-procedure-execution"></a>저장 프로시저 실행에 대한 시간 제한 한도

SQL 커넥터의 저장 프로시저 시간 제한 한도는 [2분 미만](/connectors/sql/#known-issues-and-limitations)입니다. 일부 저장 프로시저는 완료하는 데 걸리는 시간이 이 한도를 초과하여 `504 Timeout` 오류가 발생할 수 있습니다. 이 목적을 위해 장기 실행 프로세스가 명시적으로 저장 프로시저로 코딩되는 경우도 있습니다. 시간 제한 한도로 인해 Azure Logic Apps에서 해당 프로시저를 호출할 때 문제가 발생할 수 있습니다. SQL 커넥터는 기본적으로 비동기 모드를 지원하지 않지만 SQL 완료 트리거, 네이티브 SQL 통과 쿼리, 상태 테이블, 서버 쪽 작업을 사용하여 문제를 해결하고 이 모드를 시뮬레이션할 수 있습니다. 이 작업을 위해 [Azure SQL Database](../azure-sql/database/sql-database-paas-overview.md)용 [Azure 탄력적 작업 에이전트](../azure-sql/database/elastic-jobs-overview.md)를 사용할 수 있습니다. [온-프레미스 SQL Server](/sql/sql-server/sql-server-technical-documentation)와 [Azure SQL Managed Instance](../azure-sql/managed-instance/sql-managed-instance-paas-overview.md)의 경우 [SQL Server 에이전트](/sql/ssms/agent/sql-server-agent)를 사용할 수 있습니다.

예를 들어 실행을 완료하는 데 걸리는 시간이 시간 제한 한도를 초과하는 다음 장기 실행 저장 프로시저가 있다고 가정합니다. SQL 커넥터를 사용하여 논리 앱에서 이 저장 프로시저를 실행하면 그 결과로 `HTTP 504 Gateway Timeout` 오류가 발생합니다.

```sql
CREATE PROCEDURE [dbo].[WaitForIt]
   @delay char(8) = '00:03:00'
AS
BEGIN
   SET NOCOUNT ON;
   WAITFOR DELAY @delay
END
```

저장 프로시저를 직접 호출하는 대신 ‘작업 에이전트’를 사용하여 백그라운드에서 프로시저를 비동기적으로 실행할 수 있습니다. 입력과 출력을 상태 테이블에 저장한 후에 논리 앱을 통해 조작할 수 있습니다. 입력과 출력이 필요하지 않거나 저장 프로시저의 테이블에 결과를 이미 쓰고 있는 경우에는 이 방법을 간소화할 수 있습니다.

> [!IMPORTANT]
> 저장 프로시저와 모든 작업이 *idempotent* 로, 결과에 영향을 주지 않고 여러 번 실행될 수 있는지 확인합니다. 비동기 처리가 실패하거나 시간 초과되면 작업 에이전트가 단계와 저장 프로시저를 여러 번 다시 시도할 수 있습니다. 출력 복제를 방지하려면 개체를 만들기 전에 [모범 사례 및 방법](../azure-sql/database/elastic-jobs-overview.md#idempotent-scripts)을 검토합니다.

다음 섹션에서는 Azure SQL Database용 Azure 탄력적 작업 에이전트를 사용하는 방법을 설명합니다. SQL Server와 Azure SQL Managed Instance의 경우 SQL Server 에이전트를 사용할 수 있습니다. 일부 관리 정보는 다르지만 기본 단계는 Azure SQL Database용 작업 에이전트 설정과 동일합니다.

<a name="azure-sql-database"></a>

## <a name="job-agent-for-azure-sql-database"></a>Azure SQL Database용 작업 에이전트

[Azure SQL Database](../azure-sql/database/sql-database-paas-overview.md)에 대한 저장 프로시저를 실행할 수 있는 작업을 만들려면 [Azure 탄력적 작업 에이전트](../azure-sql/database/elastic-jobs-overview.md)를 사용합니다. Azure Portal에서 작업 에이전트를 만듭니다. 이 방법은 에이전트에서 사용하는 데이터베이스(‘에이전트 데이터베이스’라고도 함)에 몇 개의 저장 프로시저를 추가합니다. 그런 다음, 대상 데이터베이스에서 저장 프로시저를 실행하고 완료 시 출력을 캡처하는 작업을 만들 수 있습니다.

작업을 만들려면 먼저 [Azure 탄력적 작업 에이전트에 대한 전체 설명서](../azure-sql/database/elastic-jobs-overview.md)에 설명된 대로 사용 권한, 그룹, 대상을 설정해야 합니다. 다음 섹션에 설명된 대로 대상 데이터베이스에서 지원 테이블도 만들어야 합니다.

<a name="create-state-table"></a>

### <a name="create-state-table-for-registering-parameters-and-storing-inputs"></a>매개 변수를 등록하고 입력을 저장하기 위한 상태 테이블 만들기

SQL 에이전트 작업은 입력 매개 변수를 허용하지 않습니다. 대신, 대상 데이터베이스에서 매개 변수를 등록하고 저장 프로시저 호출에 사용할 입력을 저장하는 상태 테이블을 만듭니다. 모든 에이전트 작업 단계는 대상 데이터베이스에 대해 실행되지만, 작업의 저장 프로시저는 에이전트 데이터베이스에 대해 실행됩니다. 

상태 테이블을 만들려면 다음 스키마를 사용합니다.

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

[SMSS(SQL Server Management Studio)](/sql/ssms/download-sql-server-management-studio-ssms)에서 결과 테이블이 표시되는 모양은 다음과 같습니다.

![저장 프로시저의 입력을 저장하는, 생성된 상태 테이블 스크린샷](media/handle-long-running-stored-procedures-sql-connector/state-table-input-parameters.png)

뛰어난 성능을 보장하고 에이전트 작업이 연결된 레코드를 찾을 수 있도록 하기 위해 테이블에서 작업 실행 ID(`jobid`)가 기본 키로 사용됩니다. 원하는 경우 입력 매개 변수에 사용할 개별 열을 추가할 수도 있습니다. 앞서 설명한 스키마는 여러 매개 변수를 보다 일반적으로 처리할 수 있지만 `NVARCHAR(MAX)`에서 계산된 크기로 제한됩니다.

<a name="create-top-level-job"></a>

### <a name="create-a-top-level-job-to-run-the-stored-procedure"></a>저장 프로시저를 실행할 최상위 작업 만들기

장기 실행 저장 프로시저를 실행하려면 에이전트 데이터베이스에서 다음 최상위 작업 에이전트를 만듭니다.

```sql
EXEC jobs.sp_add_job 
   @job_name='LongRunningJob',
   @description='Execute Long-Running Stored Proc',
   @enabled = 1
```

이제 저장 프로시저를 매개 변수화하고 실행 및 완료하는 단계를 작업에 추가합니다. 기본적으로 작업 단계는 12시간 후에 시간 초과됩니다. 저장 프로시저에 더 오랜 시간이 필요하거나 프로시저가 더 빨리 시간 초과되게 하려는 경우 `step_timeout_seconds` 매개 변수를 초 단위로 지정된 다른 값으로 변경할 수 있습니다. 기본적으로 단계마다 10회 다시 시도가 기본 제공되며, 다시 시도 사이에 활용할 수 있는 백오프 시간 제한이 있습니다.

추가할 단계는 다음과 같습니다.

1. `LongRunningState` 테이블에 매개 변수가 나타날 때까지 기다립니다.

   첫 번째 단계는 `LongRunningState` 테이블에 매개 변수가 추가될 때까지 대기합니다. 작업이 시작된 후 바로 매개 변수가 추가됩니다. 작업 실행 ID(`jobid`)가 `LongRunningState` 테이블에 추가되지 않으면 단계는 실패하고, 기본 다시 시도 또는 백오프 시간 제한으로 인해 대기합니다.

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

1. 상태 테이블에서 매개 변수를 쿼리하여 저장 프로시저에 전달합니다. 이 단계에서 프로시저도 백그라운드에서 실행합니다. 

   저장 프로시저에 매개 변수가 필요하지 않은 경우에는 저장 프로시저를 직접 호출하면 됩니다. 그렇지 않고 `@timespan` 매개 변수를 전달하려면 `@callparams`를 사용합니다. 추가 매개 변수를 전달하기 위해 확장할 수도 있습니다.

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

1. 작업을 완료하고 결과를 기록합니다.

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

### <a name="start-the-job-and-pass-the-parameters"></a>작업 시작 및 매개 변수 전달

작업을 시작하려면 [**SQL 쿼리 실행** 작업](/connectors/sql/#execute-a-sql-query-(v2))과 함께 통과 기본 쿼리를 사용하고 작업의 매개 변수를 상태 테이블에 즉시 푸시합니다. 대상 테이블의 `jobid` 특성에 입력을 제공하기 위해 Logic Apps는 이전 작업의 테이블 출력을 반복하는 **For each** 루프를 추가합니다. 각 작업 실행 ID에 대해 동적 데이터 출력인 `ResultSets JobExecutionId`를 사용하는 **행 삽입** 작업을 실행하여 압축을 풀고 대상 저장 프로시저에 전달할 작업의 매개 변수를 추가합니다.

![작업을 시작하고 저장 프로시저에 매개 변수를 전달하는 데 사용할 작업 스크린샷](media/handle-long-running-stored-procedures-sql-connector/start-job-actions.png)

작업이 완료되면 `LongRunningState` 테이블을 업데이트하므로 [**항목이 수정된 경우** 트리거](/connectors/sql/#when-an-item-is-modified-(v2))를 사용하여 결과를 쉽게 트리거할 수 있습니다. 출력이 필요하지 않거나 출력 테이블을 모니터링하는 트리거가 이미 있는 경우에는 이 부분을 건너뛰어도 됩니다.

![항목이 수정된 경우에 대한 SQL 트리거 스크린샷](media/handle-long-running-stored-procedures-sql-connector/trigger-on-results-after-job-completes.png)

<a name="sql-on-premises-or-managed-instance"></a>

## <a name="job-agent-for-sql-server-or-azure-sql-managed-instance"></a>SQL Server 또는 Azure SQL Managed Instance용 작업 에이전트

동일한 시나리오에서 [온-프레미스 SQL Server](/sql/sql-server/sql-server-technical-documentation) 및 [Azure SQL Managed Instance](../azure-sql/managed-instance/sql-managed-instance-paas-overview.md)용 [SQL Server 에이전트](/sql/ssms/agent/sql-server-agent)를 사용할 수 있습니다. 일부 관리 정보는 다르지만 기본 단계는 Azure SQL Database용 작업 에이전트 설정과 동일합니다.

## <a name="next-steps"></a>다음 단계

[SQL Server, Azure SQL Database 또는 Azure SQL Managed Instance에 연결](../connectors/connectors-create-api-sqlazure.md)

