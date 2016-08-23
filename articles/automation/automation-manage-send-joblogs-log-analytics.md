<properties
    pageTitle="자동화에서 Log Analytics로 작업 상태 및 작업 스트림 전달(OMS) | Microsoft Azure"
    description="이 문서에서는 작업 상태 및 runbook 작업 스트림을 Microsoft Operations Management Suite Log Analytics로 보내 통찰력 및 관리를 강화하는 방법을 알아봅니다."
    services="automation"
    documentationCenter=""
    authors="MGoedtel"
    manager="jwhit"
    editor="tysonn" />
<tags
    ms.service="automation"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="infrastructure-services"
    ms.date="08/08/2016"
    ms.author="magoedte" />

# 자동화에서 Log Analytics로 작업 상태 및 작업 스트림 전달(OMS)

자동화에서 Microsoft Operations Management Suite(OMS) Log Analytics 작업 영역으로 runbook 작업 상태 및 작업 스트림을 보낼 수 있습니다. Azure 포털에서 또는 PowerShell을 사용하여 특정 자동화 계정에 대한 개별 작업 상태 또는 모든 작업별로 이 정보를 볼 수 있지만 작업 요구 사항을 보다 효율적으로 지원하기 위한 향상된 기능에서는 사용자 지정 PowerShell 스크립트를 만들어야 합니다. 이제 Log Anaytics를 사용하여 다음을 수행할 수 있습니다.

- 자동화 작업에 대한 통찰력 확보
- Runbook 작업 상태(예: 실패 또는 일시 중단)를 기반으로 전자 메일 또는 경고 트리거
- 작업 스트림에서 고급 쿼리 작성
- 자동화 계정 간에 작업 상호 연결
- 시간별 작업 기록 시각화

## 필수 구성 요소 및 배포 고려 사항

자동화 로그를 Log Analytics로 보내려면 다음이 있어야 합니다.

1. OMS 구독입니다. 자세한 내용은 [Log Analytics 시작](../log-analytics/log-analytics-get-started.md)을 참조하세요.

    >[AZURE.NOTE]이 구성이 제대로 작동하려면 OMS 작업 영역과 자동화 계정이 동일한 Azure 구독에 있어야 합니다.
  
2. [Azure Storage 계정](../storage/storage-create-storage-account.md).
   
    >[AZURE.NOTE]저장소 계정이 자동화 계정과 동일한 지역에 *있어야* 합니다.
 
3. 버전 1.0.8 이상의 Operational Insights cmdlet이 있는 Azure PowerShell 이 릴리스에 대한 정보 및 설치 방법은 [Azure PowerShell 설치 및 구성 방법](../powershell-install-configure.md)을 참조하세요.
4. Azure 진단 및 Log Analytics PowerShell. 이 릴리스에 대한 자세한 내용은 및 설치 방법은 [Azure 진단 및 Log Analytics](https://www.powershellgallery.com/packages/AzureDiagnosticsAndLogAnalytics/0.1)를 참조하세요.
5. [PowerShell 갤러리](https://www.powershellgallery.com/packages/Enable-AzureDiagnostics/1.0/DisplayScript)에서 PowerShell 스크립트 **Enable-AzureDiagnostics.ps1** 다운로드. 이 스크립트는 다음을 구성합니다.
 - 지정한 자동화 계정에 대한 runbook 작업 상태 및 스트림 데이터를 유지하는 저장소 계정
 - JSON 형식의 Azure Blob 저장소 계정에 저장할 수 있도록 자동화 계정에서 이 데이터 수집 활성화
 - Blob 저장소 계정에서 OMS Log Analytics로 데이터를 수집하도록 구성
 - OMS 작업 영역에서 자동화 Log Analytics 솔루션 사용

**Enable-AzureDiagnostics.ps1** 스크립트에는 실행 중 다음 매개 변수가 필요합니다.

- *AutomationAccountName* - 자동화 계정의 이름
- *LogAnalyticsWorkspaceName* - OMS 작업 영역의 이름

*AutomationAccountName* 값을 찾으려면 Azure 포털의 **자동화 계정** 블레이드에서 자동화 계정을 선택한 다음 **모든 설정**을 선택합니다. **계정 설정** 아래에 있는 **모든 설정** 블레이드에서 **속성**을 선택합니다. **속성** 블레이드에서 이러한 값을 기록할 수 있습니다.<br> ![자동화 계정 속성](media/automation-manage-send-joblogs-log-analytics/automation-account-properties.png)


## Log Analytics와의 통합 설정

1. 컴퓨터의 **시작** 화면에서 **Windows PowerShell**을 시작합니다.
2. PowerShell 명령줄 셸에서 다운로드한 스크립트가 포함된 폴더로 이동한 후 *-AutomationAccountName* 및 *-LogAnalyticsWorkspaceName* 매개 변수 값을 변경하여 실행합니다.

    >[AZURE.NOTE] 스크립트를 실행한 후에 Azure 인증을 묻는 메시지가 나타납니다. 구독 관리자 역할의 멤버이자 구독의 공동 관리자인 계정으로 **로그인해야** 합니다.
    
        .\Enable-AzureDiagnostics -AutomationAccountName <NameofAutomationAccount> `
        -LogAnalyticsWorkspaceName <NameofOMSWorkspace> `

3. 이 스크립트를 실행하면 새 진단 데이터가 저장소에 기록되고 약 30분 후에 Log Analytics에서 레코드가 표시됩니다. 이 시간 후에 레코드를 사용할 수 없으면 [Blob 저장소의 JSON 파일](../log-analytics/log-analytics-azure-storage-json.md#troubleshooting-configuration-for-azure-diagnostics-written-to-blob-in-json)에서 문제 해결 섹션을 참조하세요.

### 구성 확인

스크립트를 통해 자동화 계정 및 OMS 작업 영역이 성공적으로 구성되었는지 확인하려면 PowerShell에서 다음 단계를 수행합니다. 그 전제 OMS 작업 영역 이름 및 리소스 그룹 이름에 대한 값을 확인하려면 Azure 포털에서 Log Analytics(OMS)로 이동하여 Log Analytics(OMS) 블레이드에서 **이름** 및 **리소스 그룹** 값을 확인합니다.<br> ![OMS Log Analytics 작업 영역 목록](media/automation-manage-send-joblogs-log-analytics/oms-la-workspaces-list-blade.png) 이 두 값은 PowerShell cmdlet [Get-AzureRmOperationalInsightsStorageInsight](https://msdn.microsoft.com/library/mt603567.aspx)를 사용하여 OMS 작업 영역에서 구성을 확인할 때 사용됩니다.

1.  Azure 포털에서 저장소 계정으로 이동하여 *AutomationAccountNameomsstorage* 명명 규칙을 사용하는 다음 저장소 계정을 검색합니다. Runbook 작업이 완료되면 잠시 후 생성된 두 개의 Blob 컨테이너(**insights-logs-joblogs** 및 **insights-logs-jobstreams**)가 표시됩니다.

2.  PowerShell에서 이전에 복사하거나 적어 둔 **ResourceGroupName** 및 **WorkspaceName** 매개 변수의 값을 변경하여 다음 PowerShell 코드를 실행합니다.

    Login-AzureRmAccount Get-AzureRmSubscription -SubscriptionName 'SubscriptionName' | Set-AzureRmContext Get-AzureRmOperationalInsightsStorageInsight -ResourceGroupName "OMSResourceGroupName" ` -Workspace "OMSWorkspaceName"

    지정한 OMS 작업 영역에 대한 저장소 정보가 반환됩니다. 이전에 지정한 자동화 계정에 대한 저장소 정보가 있고 **State** 개체에 **OK** 값이 표시되어 있는지 확인할 수 있습니다.<br> ![Get-AzureRmOperationalInsightsStorageInsights cmdlet의 결과](media/automation-manage-send-joblogs-log-analytics/automation-posh-getstorageinsights-results.png).

## Log Analytics에서 자동화 로그 보기 

자동화 작업 로그를 Log Analytics로 보내기 시작했으므로 이제 OMS 내에서 이러한 로그로 수행할 수 있는 작업을 살펴보겠습니다.

### Runbook 작업이 실패하거나 일시 중단된 경우 전자 메일 보내기 

고객이 자주 묻는 질문 중 하나는 Runbook 작업에 문제가 발생한 경우 전자 메일 또는 텍스트를 보낼 수 있는지 여부입니다.

경고 규칙을 만들려면 경고를 호출해야 하는 runbook 작업 레코드에 대한 로그 검색을 만드는 것으로 시작합니다. 그러면 **경고** 단추를 사용할 수 있으며 이 단추를 사용하여 경고 규칙을 만들고 구성할 수 있습니다.

1.	OMS 개요 페이지에서 **로그 검색**을 클릭합니다.
2.	쿼리 필드에 다음을 입력하여 경고에 대한 로그 검색 쿼리를 만듭니다. `Category=JobLogs (ResultType=Failed || ResultType=Suspended)`. 다음을 사용하여 RunbookName별로 그룹화할 수도 있습니다 `Category=JobLogs (ResultType=Failed || ResultType=Suspended) | measure Count() by RunbookName_s`.
  
    둘 이상의 자동화 계정 또는 작업 영역에 대한 구독으로 로그를 설정한 경우 구독 또는 자동화 계정별로 경고를 그룹화할 수도 있습니다. 자동화 계정 이름은 JobLogs 검색의 리소스 필드에서 파생될 수 있습니다.

3.	페이지 위쪽에서 **경고**를 클릭하여 **경고 규칙 추가** 화면을 엽니다. 경고 구성 옵션에 자세한 내용은 [Log Analytics의 경고](../log-analytics/log-analytics-alerts.md#creating-an-alert-rule)를 참조하세요.

### 오류와 함께 완료된 모든 작업 찾기 

실패 기반의 경고 외에 runbook 작업에 비종료 오류가 있는지도 알 수 있습니다(PowerShell에서는 오류 스트림을 생성하지만 비종료 오류는 작업의 일시 중단 또는 실패를 유발하지 않음).

1. OMS 포털에서 **로그 검색**을 클릭합니다.
2. 쿼리 필드에서 `Category=JobStreams StreamType_s=Error | measure count() by JobId_g`을 입력하고 **검색**을 클릭합니다.


### 작업에 대한 작업 스트림 보기  

작업을 디버깅할 때 작업 스트림을 살펴볼 수도 있습니다. 아래 쿼리는 GUID가 2ebd22ea-e05e-4eb9-9d76-d73cbd4356e0인 단일 작업의 모든 스트림을 보여 줍니다.

`Category=JobStreams JobId_g="2ebd22ea-e05e-4eb9-9d76-d73cbd4356e0" | sort TimeGenerated | select ResultDescription`

### 기록 작업 상태 보기 

마지막으로 시간별 작업 기록을 시각화할 수 있습니다. 이 쿼리를 사용하여 시간이 지남에 따른 작업 상태를 검색할 수 있습니다.

`Category=JobLogs NOT(ResultType="started") | measure Count() by ResultType interval 1day` <br> ![OMS 기록 작업 상태 차트](media/automation-manage-send-joblogs-log-analytics/historical-job-status-chart.png)<br>

## 요약

자동화 작업 상태 및 스트림 데이터를 Log Analytics로 보내면, 문제가 있는 경우 알리도록 경고를 설정하고 고급 쿼리를 통해 runbook 결과, runbook 작업 상태 및 기타 관련 주요 지표 또는 메트릭을 시각화하는 사용자 지정 대시보드를 사용하여 자동화 작업의 상태에 대한 향상된 정보를 얻을 수 있습니다. 이는 보다 뛰어난 작업 가시성을 제공하고 문제를 보다 신속하게 해결하는 데 도움이 됩니다.


## 다음 단계

- Log Analytics를 사용하여 여러 검색 쿼리를 작성하고 자동화 작업 로그를 검토하는 방법에 대한 자세한 내용은 [Log Analytics의 로그 검색](../log-analytics/log-analytics-log-searches.md)을 참조하세요.
- Runbook에서 출력 및 오류 메시지를 만들고 검색하는 방법은 [Runbook 출력 및 메시지](automation-runbook-output-and-messages.md)를 참조하세요.
- Runbook 실행, Runbook 작업 모니터링 방법 및 기타 기술 세부 정보를 알아보려면 [Runbook 작업 추적](automation-runbook-execution.md)을 참조하세요.
- OMS Log Analytics 및 데이터 수집 소스에 대한 자세한 내용은 [Log Analytics에서 Azure Storage 데이터 수집 개요](../log-analytics/log-analytics-azure-storage.md)를 참조하세요.

<!---HONumber=AcomDC_0810_2016-->