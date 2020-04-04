---
title: Azure Monitor 로그에 Azure Automation 작업 데이터 전달
description: 이 문서에서는 추가 통찰력 및 관리를 제공하기 위해 작업 상태 및 Runbook 작업 스트림을 Azure Monitor 로그로 보내는 방법을 보여 줍니다.
services: automation
ms.subservice: process-automation
ms.date: 02/05/2019
ms.topic: conceptual
ms.openlocfilehash: 54f77f55a127cd712d43419eb6a85fd5d93a478c
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/03/2020
ms.locfileid: "80652170"
---
# <a name="forward-job-status-and-job-streams-from-automation-to-azure-monitor-logs"></a>자동화에서 Azure 모니터 로그로 작업 상태 및 작업 스트림 전달

Automation에서는 Log Analytics 작업 영역으로 Runbook 작업 상태 및 작업 스트림을 보낼 수 있습니다. 이 프로세스는 작업 영역 링크 설정을 포함하지 않고 완전히 독립적입니다. 개별 작업에 대해 Azure Portal에서 또는 PowerShell을 사용하여 작업 로그 및 작업 스트림을 볼 수 있으며 이를 통해 보다 간단한 조사가 가능합니다. 이제 Azure 모니터 로그를 사용하면 다음을 수행할 수 있습니다.

* Automation 작업에 대한 통찰력 확보
* Runbook 작업 상태(예: 실패 또는 일시 중단)를 기반으로 이메일 또는 경고 트리거
* 작업 스트림에서 고급 쿼리 작성
* Automation 계정 간에 작업 상호 연결
* 시간별 작업 기록 시각화

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites-and-deployment-considerations"></a>필수 구성 요소 및 배포 고려 사항

자동화 로그를 Azure Monitor 로그로 보내기 시작하려면 다음을 수행해야 합니다.

* [Azure PowerShell의](https://docs.microsoft.com/powershell/azureps-cmdlets-docs/)최신 릴리스.
* Log Analytics 작업 영역. 자세한 내용은 [Azure Monitor 로그 시작 을](../log-analytics/log-analytics-get-started.md)참조하십시오.
* Azure 자동화 계정의 리소스 ID입니다.

다음 명령을 사용하여 Azure 자동화 계정의 리소스 ID를 찾습니다.

```powershell-interactive
# Find the ResourceId for the Automation Account
Get-AzResource -ResourceType "Microsoft.Automation/automationAccounts"
```

로그 분석 작업 영역의 리소스 ID를 찾으려면 다음 PowerShell 명령을 실행합니다.

```powershell-interactive
# Find the ResourceId for the Log Analytics workspace
Get-AzResource -ResourceType "Microsoft.OperationalInsights/workspaces"
```

이전 명령의 출력에 자동화 계정 또는 작업 영역이 두 개 이상 있는 경우 리소스 ID의 값을 구성하고 복사해야 하는 이름을 찾습니다.

1. Azure 포털에서 **자동화 계정** 블레이드에서 자동화 계정을 선택하고 **모든 설정을**선택합니다. 
2. 모든 **설정** 블레이드에서 **계정 설정에서** **속성을**선택합니다.  
3. **속성** 블레이드에서 이러한 값을 기록합니다.<br> ![자동화 계정](media/automation-manage-send-joblogs-log-analytics/automation-account-properties.png)속성 .


## <a name="azure-monitor-log-records"></a>Azure Monitor 로그 레코드

Azure 자동화 진단은 `AzureDiagnostics`Azure Monitor 로그에서 두 가지 유형의 레코드를 만듭니다. 다음 섹션의 테이블은 Azure Automation에서 생성하는 레코드의 예와 로그 검색 결과에 나타나는 데이터 형식입니다.

### <a name="job-logs"></a>작업 로그

| 속성 | 설명 |
| --- | --- |
| TimeGenerated |runbook 작업이 실행된 날짜 및 시간입니다. |
| RunbookName_s |runbook의 이름입니다. |
| Caller_s |작업을 시작한 호출자입니다. 가능한 값은 전자 메일 주소 또는 예약된 작업의 시스템입니다. |
| Tenant_g | 호출자의 테넌트를 식별하는 GUID입니다. |
| JobId_g |Runbook 작업을 식별하는 GUID입니다. |
| ResultType |runbook 작업의 상태입니다. 가능한 값은 다음과 같습니다.<br>- 신규<br>- 생성됨<br>- 시작됨<br>- 중지됨<br>- 일시 중단됨<br>- 실패<br>- 완료됨 |
| Category | 데이터 유형의 분류입니다. Automation의 경우 값은 JobLogs입니다. |
| OperationName | Azure에서 수행되는 작업 유형입니다. Automation의 경우 이 값은 Job입니다. |
| 리소스 | 자동화 계정의 이름 |
| SourceSystem | Azure 모니터 로그가 데이터를 수집하는 데 사용하는 시스템입니다. 값은 항상 Azure 진단에 대 한 Azure입니다. |
| ResultDescription |Runbook 작업 결과 상태입니다. 가능한 값은 다음과 같습니다.<br>- 작업 시작<br>- 작업 실패<br>- Job Completed입니다. |
| CorrelationId |Runbook 작업의 상관 관계 GUID입니다. |
| ResourceId |Runbook의 Azure 자동화 계정 리소스 ID입니다. |
| SubscriptionId | 자동화 계정에 대한 Azure 구독 GUID입니다. |
| ResourceGroup | 자동화 계정의 리소스 그룹의 이름입니다. |
| ResourceProvider | 리소스 공급자입니다. 값은 마이크로소프트입니다. 자동화. |
| ResourceType | 리소스 형식입니다. 값은 자동화 계정입니다. |

### <a name="job-streams"></a>작업 스트림
| 속성 | 설명 |
| --- | --- |
| TimeGenerated |runbook 작업이 실행된 날짜 및 시간입니다. |
| RunbookName_s |runbook의 이름입니다. |
| Caller_s |작업을 시작한 호출자입니다. 가능한 값은 전자 메일 주소 또는 예약된 작업의 시스템입니다. |
| StreamType_s |작업 스트림의 유형입니다. 가능한 값은 다음과 같습니다.<br>- 진행<br>- 출력<br>- 경고<br>- 오류<br>- 디버그<br>- Verbose입니다. |
| Tenant_g | 호출자의 테넌트를 식별하는 GUID입니다. |
| JobId_g |Runbook 작업을 식별하는 GUID입니다. |
| ResultType |runbook 작업의 상태입니다. 가능한 값은 다음과 같습니다.<br>- 진행 중 |
| Category | 데이터 유형의 분류입니다. Automation의 경우 값은 JobStreams입니다. |
| OperationName | Azure에서 수행된 작업 유형입니다. Automation의 경우 이 값은 Job입니다. |
| 리소스 | 자동화 계정의 이름입니다. |
| SourceSystem | Azure 모니터 로그가 데이터를 수집하는 데 사용하는 시스템입니다. 값은 항상 Azure 진단에 대 한 Azure입니다. |
| ResultDescription |Runbook의 출력 스트림을 포함하는 설명입니다. |
| CorrelationId |Runbook 작업의 상관 관계 GUID입니다. |
| ResourceId |Runbook의 Azure 자동화 계정 리소스 ID입니다. |
| SubscriptionId | 자동화 계정에 대한 Azure 구독 GUID입니다. |
| ResourceGroup | 자동화 계정의 리소스 그룹의 이름입니다. |
| ResourceProvider | 리소스 공급자입니다. 값은 마이크로소프트입니다. 자동화. |
| ResourceType | 리소스 형식입니다. 값은 자동화 계정입니다. |

## <a name="setting-up-integration-with-azure-monitor-logs"></a>Azure 모니터 로그와의 통합 설정

1. 컴퓨터의 **시작** 화면에서 Windows PowerShell을 시작합니다.
2. 다음 PowerShell 명령을 실행하고 이전 섹션의 `[your resource ID]` `[resource ID of the log analytics workspace]` 값과 에 대한 값을 편집합니다.

   ```powershell-interactive
   $workspaceId = "[resource ID of the log analytics workspace]"
   $automationAccountId = "[resource ID of your Automation account]"

   Set-AzDiagnosticSetting -ResourceId $automationAccountId -WorkspaceId $workspaceId -Enabled 1
   ```

이 스크립트를 실행한 후 새 `JobLogs` 레코드 또는 `JobStreams` 작성 중인 Azure Monitor 로그에서 레코드를 보기 시작하기 까지 1시간이 걸릴 수 있습니다.

로그를 보려면 로그 분석 로그 검색에서 다음 쿼리를 실행합니다.`AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION"`

### <a name="verify-configuration"></a>구성 확인

자동화 계정이 Log Analytics 작업 영역으로 로그를 보내고 있는지 확인하려면 다음 PowerShell 명령을 사용하여 자동화 계정에 진단이 올바르게 구성되어 있는지 확인합니다.

```powershell-interactive
Get-AzDiagnosticSetting -ResourceId $automationAccountId
```

출력에서 다음을 확인합니다.

* 아래 `Logs`의 `Enabled` 값은 True입니다.
* `WorkspaceId`로그 분석 `ResourceId` 작업 영역의 값으로 설정됩니다.

## <a name="viewing-automation-logs-in-azure-monitor-logs"></a>Azure 모니터 로그에서 자동화 로그 보기

이제 자동화 작업 로그를 Azure Monitor 로그로 보내기 시작했습니다.

로그를 보려면 다음 쿼리를 실행합니다. `AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION"`

### <a name="send-an-email-when-a-runbook-job-fails-or-suspends"></a>Runbook 작업이 실패하거나 일시 중단된 경우 전자 메일 보내기

고객이 자주 묻는 질문 중 하나는 Runbook 작업에 문제가 발생한 경우 이메일 또는 텍스트를 보낼 수 있는지 여부입니다.

경고 규칙을 만들려면 경고를 호출해야 하는 Runbook 작업 레코드에 대한 로그 검색을 만들어 시작합니다. **경고** 단추를 클릭하여 경고 규칙을 만들고 구성합니다.

1. 로그 분석 작업 영역 개요 페이지에서 **로그 보기를 클릭합니다.**
2. 다음 검색을 쿼리 필드에 입력하여 경고에 대한 로그 검색 쿼리를 만듭니다.`AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobLogs" and (ResultType == "Failed" or ResultType == "Suspended")`<br><br>다음을 사용하여 Runbook 이름으로 그룹화할 수도 있습니다.`AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobLogs" and (ResultType == "Failed" or ResultType == "Suspended") | summarize AggregatedValue = count() by RunbookName_s`

   둘 이상의 Automation 계정 또는 구독에서 작업 영역으로의 로그를 설정한 경우 구독 또는 Automation 계정별로 경고를 그룹화할 수 있습니다. 자동화 계정 이름은 의 `Resource` 검색 필드에서 찾을 `JobLogs`수 있습니다.
3. **규칙 만들기** 화면을 열려면 페이지 위쪽에서 **+ 새 경고 규칙**을 클릭합니다. 경고 구성 옵션에 자세한 내용은 [Azure의 로그 경고](../azure-monitor/platform/alerts-unified-log.md)를 참조하세요.

### <a name="find-all-jobs-that-have-completed-with-errors"></a>오류와 함께 완료된 모든 작업 찾기

오류에 대한 경고 외에도, runbook 작업에 대해 비종료 오류가 발생하는 경우를 확인할 수 있습니다. 이러한 경우 PowerShell은 오류 스트림을 생성하지만 종료되지 않는 오류로 인해 작업이 일시 중단되거나 실패하지는 않습니다.

1. 로그 분석 작업 영역에서 **로그를 클릭합니다.**
2. 쿼리 필드에 을 `AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobStreams" and StreamType_s == "Error" | summarize AggregatedValue = count() by JobId_g`입력합니다.
3. **검색** 버튼을 클릭합니다.

### <a name="view-job-streams-for-a-job"></a>작업에 대한 작업 스트림 보기

작업을 디버깅할 때 작업 스트림을 살펴볼 수도 있습니다. 다음 쿼리는 GUID가 2ebd22ea-e05e-4eb9-9d76-d73cbd4356e0인 단일 작업의 모든 스트림을 보여 줍니다.

`AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobStreams" and JobId_g == "2ebd22ea-e05e-4eb9-9d76-d73cbd4356e0" | sort by TimeGenerated asc | project ResultDescription`

### <a name="view-historical-job-status"></a>기록 작업 상태 보기

마지막으로 시간이 지남에 따라 작업 기록을 시각화할 수 있습니다. 이 쿼리를 사용하여 시간이 지남에 따른 작업 상태를 검색할 수 있습니다.

`AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobLogs" and ResultType != "started" | summarize AggregatedValue = count() by ResultType, bin(TimeGenerated, 1h)`
<br> ![Log Analytics 기록 작업 상태 차트](media/automation-manage-send-joblogs-log-analytics/historical-job-status-chart.png)<br>

## <a name="removing-diagnostic-settings"></a>진단 설정 제거

자동화 계정에서 진단 설정을 제거하려면 다음 명령을 실행합니다.

```powershell-interactive
$automationAccountId = "[resource ID of your Automation account]"

Remove-AzDiagnosticSetting -ResourceId $automationAccountId
```

## <a name="summary"></a>요약

자동화 작업 상태 및 스트림 데이터를 Azure Monitor 로그로 전송하면 다음을 통해 자동화 작업의 상태에 대한 더 나은 통찰력을 얻을 수 있습니다.
+ 문제가 발생할 때 알리도록 경고 설정
+ 사용자 지정 보기와 검색 쿼리를 사용하여 runbook 결과, runbook 작업 상태 및 기타 관련된 핵심 지표 또는 메트릭 시각화.

Azure Monitor 로그는 자동화 작업에 대한 운영 가시성을 높이고 인시던트를 더 빠르게 해결하는 데 도움이 될 수 있습니다.

## <a name="next-steps"></a>다음 단계

* 로그 분석 문제 해결에 대한 도움말은 [로그 애널리틱스가 더 이상 데이터를 수집하지 않는 이유 문제 해결을](../azure-monitor/platform/manage-cost-storage.md#troubleshooting-why-log-analytics-is-no-longer-collecting-data)참조하세요.
* 다른 검색 쿼리를 구성하고 Azure Monitor 로그를 사용하여 자동화 작업 로그를 검토하는 방법에 대한 자세한 내용은 [Azure Monitor 로그의 로그 검색을](../log-analytics/log-analytics-log-searches.md)참조하세요.
* Runbook에서 출력 및 오류 메시지를 만들고 검색하는 방법을 이해하려면 [Runbook 출력 및 메시지를](automation-runbook-output-and-messages.md)참조하십시오.
* Runbook 실행, Runbook 작업 모니터링 방법 및 기타 기술 세부 정보에 대한 자세한 내용은 [Runbook 작업 추적을](automation-runbook-execution.md)참조하십시오.
* Azure 모니터 로그 및 데이터 수집 원본에 대한 자세한 내용은 [Azure Monitor 로그 개요에서 Azure 저장소 데이터 수집을](../azure-monitor/platform/collect-azure-metrics-logs.md)참조하십시오.

