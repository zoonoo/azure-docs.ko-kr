---
title: Azure Monitor 로그에 Azure Automation 작업 데이터 전달
description: 이 문서에서는 작업 상태 및 runbook 작업 스트림을 Azure Monitor 로그에 보내서 추가 정보 및 관리를 제공 하는 방법을 보여 줍니다.
services: automation
ms.subservice: process-automation
ms.date: 02/05/2019
ms.topic: conceptual
ms.openlocfilehash: a9f4e641e60d6cf1c481c445767422e8b4df683b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81457691"
---
# <a name="forward-job-status-and-job-streams-from-automation-to-azure-monitor-logs"></a>Automation에서 Azure Monitor 로그로 작업 상태 및 작업 스트림 전달

Automation에서는 Log Analytics 작업 영역으로 Runbook 작업 상태 및 작업 스트림을 보낼 수 있습니다. 이 프로세스는 작업 영역 링크 설정을 포함하지 않고 완전히 독립적입니다. 개별 작업에 대해 Azure Portal에서 또는 PowerShell을 사용하여 작업 로그 및 작업 스트림을 볼 수 있으며 이를 통해 보다 간단한 조사가 가능합니다. 이제 Azure Monitor 로그를 사용 하 여 다음을 수행할 수 있습니다.

* Automation 작업의 상태에 대 한 통찰력을 얻습니다.
* Runbook 작업 상태(예: 실패 또는 일시 중단)를 기반으로 이메일 또는 경고 트리거
* 작업 스트림에서 고급 쿼리 작성
* Automation 계정 간에 작업 상호 연결
* 사용자 지정 보기 및 검색 쿼리를 사용 하 여 runbook 결과, runbook 작업 상태 및 기타 관련 키 표시기 또는 메트릭을 시각화 합니다.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

>[!NOTE]
>이 문서는 새 Azure PowerShell Az 모듈을 사용하도록 업데이트되었습니다. AzureRM 모듈은 적어도 2020년 12월까지 버그 수정을 수신할 예정이므로 계속 사용하셔도 됩니다. 새 Az 모듈 및 AzureRM 호환성에 대한 자세한 내용은 [새 Azure PowerShell Az 모듈 소개](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0)를 참조하세요. Hybrid Runbook Worker에 대한 Az 모듈 설치 지침은 [Azure PowerShell 모듈 설치](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)를 참조하세요. Automation 계정의 경우 [Azure Automation에서 Azure PowerShell 모듈을 업데이트하는 방법](automation-update-azure-modules.md)을 사용하여 모듈을 최신 버전으로 업데이트할 수 있습니다.

## <a name="prerequisites-and-deployment-considerations"></a>필수 구성 요소 및 배포 고려 사항

Azure Monitor 로그에 Automation 로그를 보내기 시작 하려면 다음이 필요 합니다.

* 최신 버전의 [Azure PowerShell](https://docs.microsoft.com/powershell/azureps-cmdlets-docs/)입니다.
* Log Analytics 작업 영역. 자세한 내용은 [Azure Monitor 로그 시작](../log-analytics/log-analytics-get-started.md)을 참조 하세요.
* Azure Automation 계정에 대 한 리소스 ID입니다.

다음 명령을 사용 하 여 Azure Automation 계정에 대 한 리소스 ID를 찾습니다.

```powershell-interactive
# Find the ResourceId for the Automation account
Get-AzResource -ResourceType "Microsoft.Automation/automationAccounts"
```

Log Analytics 작업 영역에 대 한 리소스 ID를 찾으려면 다음 PowerShell 명령을 실행 합니다.

```powershell-interactive
# Find the ResourceId for the Log Analytics workspace
Get-AzResource -ResourceType "Microsoft.OperationalInsights/workspaces"
```

이전 명령의 출력에 둘 이상의 Automation 계정 또는 작업 영역이 있는 경우 구성 해야 하는 이름을 찾고 리소스 ID에 대 한 값을 복사 합니다.

1. Azure Portal **automation 계정** 블레이드에서 automation 계정을 선택 하 고 **모든 설정**을 선택 합니다. 
2. **모든 설정** 블레이드의 **계정 설정**에서 **속성**을 선택 합니다.  
3. **속성** 블레이드에서 아래에 표시 된 속성을 확인 합니다.

    ![Automation 계정 속성](media/automation-manage-send-joblogs-log-analytics/automation-account-properties.png).

## <a name="azure-monitor-log-records"></a>Azure Monitor 로그 레코드

Azure Automation 진단은로 `AzureDiagnostics`태그가 지정 된 Azure Monitor 로그에 두 가지 유형의 레코드를 만듭니다. 다음 섹션의 테이블은 Azure Automation에서 생성 하는 레코드와 로그 검색 결과에 표시 되는 데이터 형식에 대 한 예입니다.

### <a name="job-logs"></a>작업 로그

| 속성 | Description |
| --- | --- |
| TimeGenerated |runbook 작업이 실행된 날짜 및 시간입니다. |
| RunbookName_s |runbook의 이름입니다. |
| Caller_s |작업을 시작한 호출자입니다. 가능한 값은 전자 메일 주소 또는 예약된 작업의 시스템입니다. |
| Tenant_g | 호출자에 대 한 테 넌 트를 식별 하는 GUID입니다. |
| JobId_g |Runbook 작업을 식별 하는 GUID입니다. |
| ResultType |runbook 작업의 상태입니다. 가능한 값은 다음과 같습니다.<br>- 신규<br>-만듦<br>- 시작됨<br>- 중지됨<br>- 일시 중단됨<br>- 실패<br>- 완료됨 |
| 범주 | 데이터 유형의 분류입니다. Automation의 경우 값은 JobLogs입니다. |
| OperationName | Azure에서 수행 되는 작업의 유형입니다. Automation의 경우 이 값은 Job입니다. |
| 리소스 | Automation 계정의 이름 |
| SourceSystem | Azure Monitor 로그가 데이터를 수집 하는 데 사용 하는 시스템입니다. 값은 항상 azure 진단을 위한 Azure입니다. |
| ResultDescription |Runbook 작업 결과 상태입니다. 가능한 값은 다음과 같습니다.<br>- 작업 시작<br>- 작업 실패<br>- Job Completed입니다. |
| CorrelationId |Runbook 작업의 상관 관계 GUID입니다. |
| ResourceId |Runbook의 Azure Automation 계정 리소스 ID입니다. |
| SubscriptionId | Automation 계정에 대 한 Azure 구독 GUID입니다. |
| ResourceGroup | Automation 계정에 대 한 리소스 그룹의 이름입니다. |
| ResourceProvider | 리소스 공급자입니다. 값은 MICROSOFT입니다. 자동화할. |
| ResourceType | 리소스 형식입니다. 값은 AUTOMATIONACCOUNTS입니다. |

### <a name="job-streams"></a>작업 스트림
| 속성 | Description |
| --- | --- |
| TimeGenerated |runbook 작업이 실행된 날짜 및 시간입니다. |
| RunbookName_s |runbook의 이름입니다. |
| Caller_s |작업을 시작한 호출자입니다. 가능한 값은 전자 메일 주소 또는 예약된 작업의 시스템입니다. |
| StreamType_s |작업 스트림의 유형입니다. 가능한 값은 다음과 같습니다.<br>- 진행<br>- 출력<br>- 경고<br>- 오류<br>- 디버그<br>- Verbose입니다. |
| Tenant_g | 호출자에 대 한 테 넌 트를 식별 하는 GUID입니다. |
| JobId_g |Runbook 작업을 식별 하는 GUID입니다. |
| ResultType |runbook 작업의 상태입니다. 가능한 값은 다음과 같습니다.<br>- 진행 중 |
| 범주 | 데이터 유형의 분류입니다. Automation의 경우 값은 JobStreams입니다. |
| OperationName | Azure에서 수행 되는 작업의 유형입니다. Automation의 경우 이 값은 Job입니다. |
| 리소스 | Automation 계정의 이름입니다. |
| SourceSystem | Azure Monitor 로그가 데이터를 수집 하는 데 사용 하는 시스템입니다. 값은 항상 azure 진단을 위한 Azure입니다. |
| ResultDescription |Runbook의 출력 스트림을 포함 하는 설명입니다. |
| CorrelationId |Runbook 작업의 상관 관계 GUID입니다. |
| ResourceId |Runbook의 Azure Automation 계정 리소스 ID입니다. |
| SubscriptionId | Automation 계정에 대 한 Azure 구독 GUID입니다. |
| ResourceGroup | Automation 계정에 대 한 리소스 그룹의 이름입니다. |
| ResourceProvider | 리소스 공급자입니다. 값은 MICROSOFT입니다. 자동화할. |
| ResourceType | 리소스 형식입니다. 값은 AUTOMATIONACCOUNTS입니다. |

## <a name="setting-up-integration-with-azure-monitor-logs"></a>Azure Monitor 로그와의 통합 설정

1. 컴퓨터의 **시작** 화면에서 Windows PowerShell을 시작합니다.
2. 다음 PowerShell 명령을 실행 하 고 이전 섹션의 값을 `[your resource ID]` 사용 `[resource ID of the log analytics workspace]` 하 여 및에 대 한 값을 편집 합니다.

   ```powershell-interactive
   $workspaceId = "[resource ID of the log analytics workspace]"
   $automationAccountId = "[resource ID of your Automation account]"

   Set-AzDiagnosticSetting -ResourceId $automationAccountId -WorkspaceId $workspaceId -Enabled 1
   ```

이 스크립트를 실행 한 후에는 새 `JobLogs` 또는 `JobStreams` 작성 된의 Azure Monitor 로그에 레코드를 표시 하기 시작 하기 전에 1 시간이 걸릴 수 있습니다.

로그를 보려면 log analytics 로그 검색에서 다음 쿼리를 실행 합니다.`AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION"`

### <a name="verify-configuration"></a>구성 확인

Automation 계정이 Log Analytics 작업 영역으로 로그를 보내고 있는지 확인 하려면 다음 PowerShell 명령을 사용 하 여 Automation 계정에 진단이 올바르게 구성 되어 있는지 확인 합니다.

```powershell-interactive
Get-AzDiagnosticSetting -ResourceId $automationAccountId
```

출력에서 다음을 확인 합니다.

* 에서 `Logs`의 `Enabled` 값은 True입니다.
* `WorkspaceId`는 Log Analytics 작업 영역 `ResourceId` 에 대 한 값으로 설정 됩니다.

## <a name="viewing-automation-logs-in-azure-monitor-logs"></a>Azure Monitor 로그에서 자동화 로그 보기

Automation 작업 로그를 Azure Monitor 로그로 보내기 시작 했으므로 Azure Monitor 로그 내에서 이러한 로그로 수행할 수 있는 작업을 살펴보겠습니다.

로그를 보려면 다음 쿼리를 실행합니다. `AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION"`

### <a name="send-an-email-when-a-runbook-job-fails-or-suspends"></a>Runbook 작업이 실패하거나 일시 중단된 경우 전자 메일 보내기

고객이 자주 묻는 질문 중 하나는 Runbook 작업에 문제가 발생한 경우 이메일 또는 텍스트를 보낼 수 있는지 여부입니다.

경고 규칙을 만들려면 경고를 호출 해야 하는 runbook 작업 레코드에 대 한 로그 검색을 만드는 것으로 시작 합니다. **경고** 단추를 클릭하여 경고 규칙을 만들고 구성합니다.

1. Log Analytics 작업 영역 개요 페이지에서 **로그 보기**를 클릭 합니다.
2. 쿼리 필드에 다음 검색을 입력 하 여 경고에 대 한 로그 검색 쿼리를 만듭니다.`AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobLogs" and (ResultType == "Failed" or ResultType == "Suspended")`<br><br>다음을 사용 하 여 runbook 이름을 기준으로 그룹화 할 수도 있습니다.`AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobLogs" and (ResultType == "Failed" or ResultType == "Suspended") | summarize AggregatedValue = count() by RunbookName_s`

   둘 이상의 Automation 계정 또는 구독에서 작업 영역으로의 로그를 설정한 경우 구독 또는 Automation 계정별로 경고를 그룹화할 수 있습니다. Automation 계정 이름은 검색의 `Resource` 필드에서 찾을 수 있습니다. `JobLogs`
3. **규칙 만들기** 화면을 열려면 페이지 맨 위에 있는 **새 경고 규칙** 을 클릭 합니다. 경고 구성 옵션에 자세한 내용은 [Azure의 로그 경고](../azure-monitor/platform/alerts-unified-log.md)를 참조하세요.

### <a name="find-all-jobs-that-have-completed-with-errors"></a>오류와 함께 완료된 모든 작업 찾기

오류에 대한 경고 외에도, runbook 작업에 대해 비종료 오류가 발생하는 경우를 확인할 수 있습니다. 이러한 경우 PowerShell은 오류 스트림을 생성 하지만 종료 되지 않는 오류로 인해 작업이 일시 중단 되거나 실패 하지 않습니다.

1. Log Analytics 작업 영역에서 **로그**를 클릭 합니다.
2. 쿼리 필드에를 입력 `AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobStreams" and StreamType_s == "Error" | summarize AggregatedValue = count() by JobId_g`합니다.
3. **검색** 단추를 클릭 합니다.

### <a name="view-job-streams-for-a-job"></a>작업에 대한 작업 스트림 보기

작업을 디버깅할 때 작업 스트림을 살펴볼 수도 있습니다. 다음 쿼리는 GUID가 2ebd22ea-e05e-4eb9-9d76-d73cbd4356e0인 단일 작업의 모든 스트림을 보여 줍니다.

`AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobStreams" and JobId_g == "2ebd22ea-e05e-4eb9-9d76-d73cbd4356e0" | sort by TimeGenerated asc | project ResultDescription`

### <a name="view-historical-job-status"></a>기록 작업 상태 보기

마지막으로 시간이 지남에 따라 작업 기록을 시각화할 수 있습니다. 이 쿼리를 사용하여 시간이 지남에 따른 작업 상태를 검색할 수 있습니다.

`AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobLogs" and ResultType != "started" | summarize AggregatedValue = count() by ResultType, bin(TimeGenerated, 1h)`
<br> ![Log Analytics 기록 작업 상태 차트](media/automation-manage-send-joblogs-log-analytics/historical-job-status-chart.png)<br>

## <a name="removing-diagnostic-settings"></a>진단 설정 제거

Automation 계정에서 진단 설정을 제거 하려면 다음 명령을 실행 합니다.

```powershell-interactive
$automationAccountId = "[resource ID of your Automation account]"

Remove-AzDiagnosticSetting -ResourceId $automationAccountId
```
## <a name="next-steps"></a>다음 단계

* Log Analytics 문제 해결에 대 한 도움말 [은 Log Analytics 더 이상 데이터를 수집 하지 않는 문제 해결](../azure-monitor/platform/manage-cost-storage.md#troubleshooting-why-log-analytics-is-no-longer-collecting-data)을 참조 하세요.
* 다른 검색 쿼리를 생성 하 고 Azure Monitor 로그를 사용 하 여 Automation 작업 로그를 검토 하는 방법에 대 한 자세한 내용은 [Azure Monitor 로그의 로그 검색](../log-analytics/log-analytics-log-searches.md)을 참조 하세요.
* Runbook에서 출력 및 오류 메시지를 만들고 검색 하는 방법을 이해 하려면 [runbook 출력 및 메시지](automation-runbook-output-and-messages.md)를 참조 하세요.
* Runbook 실행, runbook 작업 모니터링 방법 및 기타 기술 세부 정보에 대해 자세히 알아보려면 [runbook 작업 추적](automation-runbook-execution.md)을 참조 하세요.
* Azure Monitor 로그 및 데이터 수집 소스에 대 한 자세한 내용은 [Azure Monitor 로그에서 Azure storage 데이터 수집 개요](../azure-monitor/platform/collect-azure-metrics-logs.md)를 참조 하세요.