---
title: Azure Monitor 로그에 Azure Automation 작업 데이터 전달
description: 이 문서에서는 작업 상태 및 Runbook 작업 스트림을 Azure Monitor 로그에 보내는 방법을 설명합니다.
services: automation
ms.subservice: process-automation
ms.date: 05/22/2020
ms.topic: conceptual
ms.openlocfilehash: 0c8863615446f8b14043cd7bd13e529b7efa1e46
ms.sourcegitcommit: ec682dcc0a67eabe4bfe242fce4a7019f0a8c405
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/09/2020
ms.locfileid: "86186200"
---
# <a name="forward-azure-automation-job-data-to-azure-monitor-logs"></a>Azure Monitor 로그에 Azure Automation 작업 데이터 전달

Azure Automation에서는 Log Analytics 작업 영역으로 Runbook 작업 상태 및 작업 스트림을 보낼 수 있습니다. 이 프로세스는 작업 영역 링크 설정을 포함하지 않고 완전히 독립적입니다. 개별 작업에 대해 Azure Portal에서 또는 PowerShell을 사용하여 작업 로그 및 작업 스트림을 볼 수 있으며 이를 통해 보다 간단한 조사가 가능합니다. 이제 Azure Monitor 로그를 사용하여 다음을 수행할 수 있습니다.

* Automation 작업의 상태에 대한 인사이트 확보
* Runbook 작업 상태(예: 실패 또는 일시 중단)를 기반으로 이메일 또는 경고 트리거
* 작업 스트림에서 고급 쿼리 작성
* Automation 계정 간에 작업 상호 연결
* 사용자 지정 보기와 검색 쿼리를 사용하여 runbook 결과, runbook 작업 상태 및 기타 관련된 핵심 지표 또는 메트릭을 시각화합니다.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites-and-deployment-considerations"></a>필수 구성 요소 및 배포 고려 사항

Automation 로그를 Azure Monitor 로그로 보내려면 다음이 필요합니다.

* 최신 버전의 [Azure PowerShell](/powershell/azureps-cmdlets-docs/).
* Log Analytics 작업 영역. 자세한 내용은 [Azure Monitor 로그 시작](../azure-monitor/overview.md)을 참조하세요.
* Azure Automation 계정에 대한 리소스 ID

다음 명령을 사용하여 Azure Automation 계정에 대한 리소스 ID를 찾습니다.

```powershell-interactive
# Find the ResourceId for the Automation account
Get-AzResource -ResourceType "Microsoft.Automation/automationAccounts"
```

Log Analytics 작업 영역에 대한 리소스 ID를 찾으려면 다음 PowerShell 명령을 실행합니다.

```powershell-interactive
# Find the ResourceId for the Log Analytics workspace
Get-AzResource -ResourceType "Microsoft.OperationalInsights/workspaces"
```

이전 명령의 출력에 둘 이상의 Automation 계정 또는 작업 영역이 있는 경우 다음을 수행하여 Automation 계정의 전체 리소스 ID에 포함된 이름 및 기타 관련 속성을 확인할 수 있습니다.

1. Azure Portal의 **Automation 계정** 페이지에서 Automation 계정을 선택합니다. 
2. 선택한 Automation 계정 페이지의 **계정 설정**에서 **속성**을 선택합니다.  
3. **속성** 페이지에서 아래에 표시된 세부 정보를 확인합니다.

    ![Automation 계정 속성](media/automation-manage-send-joblogs-log-analytics/automation-account-properties.png).

## <a name="azure-monitor-log-records"></a>Azure Monitor 로그 레코드

Azure Automation 진단은 Azure Monitor 로그에 `AzureDiagnostics`로 태그가 지정된 두 가지 유형의 레코드를 만듭니다. 다음 섹션의 표는 Azure Automation에서 생성하는 레코드와 로그 검색 결과에 표시되는 데이터 형식에 대한 예입니다.

### <a name="job-logs"></a>작업 로그

| 속성 | Description |
| --- | --- |
| TimeGenerated |runbook 작업이 실행된 날짜 및 시간입니다. |
| RunbookName_s |runbook의 이름입니다. |
| Caller_s |작업을 시작한 호출자입니다. 가능한 값은 전자 메일 주소 또는 예약된 작업의 시스템입니다. |
| Tenant_g | 호출자에 대한 테넌트를 식별하는 GUID입니다. |
| JobId_g |Runbook 작업을 식별하는 GUID입니다. |
| ResultType |runbook 작업의 상태입니다. 가능한 값은 다음과 같습니다.<br>- 신규<br>- 생성됨<br>- 시작됨<br>- 중지됨<br>- 일시 중단됨<br>- 실패<br>- 완료됨 |
| Category | 데이터 유형의 분류입니다. Automation의 경우 값은 JobLogs입니다. |
| OperationName | Azure에서 수행되는 작업 유형입니다. Automation의 경우 이 값은 Job입니다. |
| 리소스 | Automation 계정의 이름입니다. |
| SourceSystem | Azure Monitor 로그가 데이터를 수집하는 데 사용하는 시스템입니다. 값은 Azure Diagnostics의 경우 항상 Azure입니다. |
| ResultDescription |Runbook 작업 결과 상태입니다. 가능한 값은 다음과 같습니다.<br>- 작업 시작<br>- 작업 실패<br>- Job Completed입니다. |
| CorrelationId |Runbook 작업의 상관 관계 GUID입니다. |
| ResourceId |Runbook의 Azure Automation 계정 리소스 ID입니다. |
| SubscriptionId | Automation 계정에 대한 Azure 구독 GUID입니다. |
| ResourceGroup | Automation 계정에 대한 리소스 그룹의 이름입니다. |
| ResourceProvider | 리소스 공급자입니다. 값은 MICROSOFT.AUTOMATION입니다. |
| ResourceType | 리소스 종류입니다. 값은 AUTOMATIONACCOUNTS입니다. |

### <a name="job-streams"></a>작업 스트림
| 속성 | Description |
| --- | --- |
| TimeGenerated |runbook 작업이 실행된 날짜 및 시간입니다. |
| RunbookName_s |runbook의 이름입니다. |
| Caller_s |작업을 시작한 호출자입니다. 가능한 값은 전자 메일 주소 또는 예약된 작업의 시스템입니다. |
| StreamType_s |작업 스트림의 유형입니다. 가능한 값은 다음과 같습니다.<br>\- 진행<br>- 출력<br>- 경고<br>- 오류<br>- 디버그<br>- Verbose입니다. |
| Tenant_g | 호출자에 대한 테넌트를 식별하는 GUID입니다. |
| JobId_g |Runbook 작업을 식별하는 GUID입니다. |
| ResultType |runbook 작업의 상태입니다. 가능한 값은 다음과 같습니다.<br>- 진행 중 |
| Category | 데이터 유형의 분류입니다. Automation의 경우 값은 JobStreams입니다. |
| OperationName | Azure에서 수행한 작업의 유형입니다. Automation의 경우 이 값은 Job입니다. |
| 리소스 | Automation 계정의 이름입니다. |
| SourceSystem | Azure Monitor 로그가 데이터를 수집하는 데 사용하는 시스템입니다. 값은 Azure Diagnostics의 경우 항상 Azure입니다. |
| ResultDescription |Runbook의 출력 스트림을 포함하는 설명입니다. |
| CorrelationId |Runbook 작업의 상관 관계 GUID입니다. |
| ResourceId |Runbook의 Azure Automation 계정 리소스 ID입니다. |
| SubscriptionId | Automation 계정에 대한 Azure 구독 GUID입니다. |
| ResourceGroup | Automation 계정에 대한 리소스 그룹의 이름입니다. |
| ResourceProvider | 리소스 공급자입니다. 값은 MICROSOFT.AUTOMATION입니다. |
| ResourceType | 리소스 종류입니다. 값은 AUTOMATIONACCOUNTS입니다. |

## <a name="set-up-integration-with-azure-monitor-logs"></a>Azure Monitor 로그와 통합 설정

1. 컴퓨터의 **시작** 화면에서 Windows PowerShell을 시작합니다.
2. 다음 PowerShell 명령을 실행하고 이전 섹션의 값을 사용하여 `$automationAccountId` 및 `$workspaceId`의 값을 편집합니다.

   ```powershell-interactive
   $workspaceId = "resource ID of the log analytics workspace"
   $automationAccountId = "resource ID of your Automation account"

   Set-AzDiagnosticSetting -ResourceId $automationAccountId -WorkspaceId $workspaceId -Enabled 1
   ```

이 스크립트를 실행한 후 작성 중인 새 `JobLogs` 또는 `JobStreams`의 Azure Monitor 로그에 레코드가 표시될 때까지 1시간 정도 걸릴 수 있습니다.

로그를 보려면 Log Analytics 로그 검색에서 다음 쿼리를 실행합니다. `AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION"`

### <a name="verify-configuration"></a>구성 확인

Automation 계정이 Log Analytics 작업 영역으로 로그를 보내는지 확인하려면 다음 PowerShell 명령을 사용하여 Automation 계정에서 진단이 올바르게 구성되었는지 확인합니다.

```powershell-interactive
Get-AzDiagnosticSetting -ResourceId $automationAccountId
```

출력에서 다음을 확인합니다.

* `Logs`에서 `Enabled`의 값은 True입니다.
* `WorkspaceId`는 Log Analytics 작업 영역에 대한 `ResourceId` 값으로 설정됩니다.

## <a name="view-automation-logs-in-azure-monitor-logs"></a>Azure Monitor 로그에서 Automation 로그 보기

Automation 작업 로그를 Azure Monitor 로그로 보내기 시작했으므로 Azure Monitor 로그 내에서 이러한 로그로 수행할 수 있는 작업을 살펴보겠습니다.

로그를 보려면 다음 쿼리를 실행합니다. `AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION"`

### <a name="send-an-email-when-a-runbook-job-fails-or-suspends"></a>Runbook 작업이 실패하거나 일시 중단된 경우 전자 메일 보내기

다음 단계는 Runbook 작업에 문제가 발생한 경우 사용자에게 알리는 경고를 Azure Monitor에서 설정하는 방법을 보여 줍니다.

경고 규칙을 만들려면 경고를 호출해야 하는 runbook 작업 레코드에 대한 로그 검색을 만드는 것으로 시작합니다. **경고** 단추를 클릭하여 경고 규칙을 만들고 구성합니다.

1. Log Analytics 작업 영역 개요 페이지에서 **로그 보기**를 클릭합니다.

2. 쿼리 필드에 다음 검색을 입력하여 경고에 대한 로그 검색 쿼리를 만듭니다. `AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobLogs" and (ResultType == "Failed" or ResultType == "Suspended")`<br><br>다음을 사용하여 Runbook 이름별로 그룹화할 수도 있습니다. `AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobLogs" and (ResultType == "Failed" or ResultType == "Suspended") | summarize AggregatedValue = count() by RunbookName_s`

   둘 이상의 Automation 계정 또는 구독에서 작업 영역으로의 로그를 설정한 경우 구독 또는 Automation 계정별로 경고를 그룹화할 수 있습니다. Automation 계정 이름은 `JobLogs` 검색의 `Resource` 필드에서 찾을 수 있습니다.

3. **규칙 만들기** 화면을 열려면 페이지 위쪽에서 **새 경고 규칙**을 클릭합니다. 경고 구성 옵션에 자세한 내용은 [Azure의 로그 경고](../azure-monitor/platform/alerts-unified-log.md)를 참조하세요.

### <a name="find-all-jobs-that-have-completed-with-errors"></a>오류와 함께 완료된 모든 작업 찾기

오류에 대한 경고 외에도, runbook 작업에 대해 비종료 오류가 발생하는 경우를 확인할 수 있습니다. 이러한 경우에 PowerShell은 오류 스트림을 생성하지만 비종료 오류가 발생해도 작업이 일시 중단되거나 실패하지 않습니다.

1. Log Analytics 작업 영역에서 **로그**를 클릭합니다.

2. 쿼리 필드에 `AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobStreams" and StreamType_s == "Error" | summarize AggregatedValue = count() by JobId_g`를 입력합니다.

3. **검색** 단추를 클릭합니다.

### <a name="view-job-streams-for-a-job"></a>작업에 대한 작업 스트림 보기

작업을 디버깅할 때 작업 스트림을 살펴볼 수도 있습니다. 다음 쿼리는 GUID가 2ebd22ea-e05e-4eb9-9d76-d73cbd4356e0인 단일 작업의 모든 스트림을 보여 줍니다.

`AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobStreams" and JobId_g == "2ebd22ea-e05e-4eb9-9d76-d73cbd4356e0" | sort by TimeGenerated asc | project ResultDescription`

### <a name="view-historical-job-status"></a>기록 작업 상태 보기

마지막으로 시간별 작업 기록을 시각화할 수 있습니다. 이 쿼리를 사용하여 시간이 지남에 따른 작업 상태를 검색할 수 있습니다.

`AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobLogs" and ResultType != "started" | summarize AggregatedValue = count() by ResultType, bin(TimeGenerated, 1h)`
<br> ![Log Analytics 기록 작업 상태 차트](media/automation-manage-send-joblogs-log-analytics/historical-job-status-chart.png)<br>

## <a name="remove-diagnostic-settings"></a>진단 설정 제거

Automation 계정에서 진단 설정을 제거하려면 다음 명령을 실행합니다.

```powershell-interactive
$automationAccountId = "[resource ID of your Automation account]"

Remove-AzDiagnosticSetting -ResourceId $automationAccountId
```

## <a name="next-steps"></a>다음 단계

* Azure Monitor 로그를 사용하여 검색 쿼리를 작성하고 Automation 작업 로그를 검토하는 방법에 대한 자세한 내용은 [Azure Monitor 로그의 로그 검색](../azure-monitor/log-query/log-query-overview.md)을 참조하세요.
* Runbook에서 출력 및 오류 메시지를 만들고 검색하는 방법을 이해하려면 [Runbook 출력 모니터링](automation-runbook-output-and-messages.md)을 참조하세요.
* Runbook 실행, Runbook 작업 모니터링 방법 및 기타 기술 세부 정보를 알아보려면 [Azure Automation에서 Runbook 실행](automation-runbook-execution.md)을 참조하세요.
* Azure Monitor 로그 및 데이터 수집 소스에 대해 자세히 알아보려면 [Azure Monitor 로그에서 Azure 스토리지 데이터 수집 개요](../azure-monitor/platform/resource-logs.md#send-to-log-analytics-workspace)를 참조하세요.
* Log Analytics 문제 해결에 대한 도움말은 [Log Analytics에서 더 이상 데이터를 수집하지 않는 문제 해결](../azure-monitor/platform/manage-cost-storage.md#troubleshooting-why-log-analytics-is-no-longer-collecting-data)을 참조하세요.
