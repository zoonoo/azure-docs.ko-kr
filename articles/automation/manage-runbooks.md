---
title: Azure Automation에서 Runbook 관리
description: 이 문서에서는 Azure Automation에서 Runbook을 관리하는 방법을 설명합니다. 기본 작업을 설명 하 고 몇 가지 모범 사례를 추가 합니다.
services: automation
ms.subservice: process-automation
ms.date: 02/14/2019
ms.topic: conceptual
ms.openlocfilehash: 687579825c652888112ff8ddff7401b3305e3a8e
ms.sourcegitcommit: f57297af0ea729ab76081c98da2243d6b1f6fa63
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/06/2020
ms.locfileid: "82871189"
---
# <a name="manage-runbooks-in-azure-automation"></a>Azure Automation에서 Runbook 관리

새 runbook을 만들거나 파일 또는 [Runbook 갤러리](automation-runbook-gallery.md)에서 기존 항목을 가져와 Azure Automation에 runbook을 추가할 수 있습니다. 이 문서에서는 파일에서 가져온 runbook을 관리 하는 방법에 대 한 정보를 제공 합니다. [Azure Automation에 대 한 Runbook 및 모듈 갤러리](automation-runbook-gallery.md)에서 커뮤니티 runbook 및 모듈에 액세스 하는 모든 세부 정보를 찾을 수 있습니다.

>[!NOTE]
>이 문서는 새 Azure PowerShell Az 모듈을 사용하도록 업데이트되었습니다. AzureRM 모듈은 적어도 2020년 12월까지 버그 수정을 수신할 예정이므로 계속 사용하셔도 됩니다. 새 Az 모듈 및 AzureRM 호환성에 대한 자세한 내용은 [새 Azure PowerShell Az 모듈 소개](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0)를 참조하세요. Hybrid Runbook Worker에 대한 Az 모듈 설치 지침은 [Azure PowerShell 모듈 설치](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)를 참조하세요. Automation 계정의 경우 [Azure Automation에서 Azure PowerShell 모듈을 업데이트하는 방법](automation-update-azure-modules.md)을 사용하여 모듈을 최신 버전으로 업데이트할 수 있습니다.

## <a name="create-a-runbook"></a>Runbook 만들기

Azure Portal 또는 Windows PowerShell을 사용 하 여 Azure Automation에서 새 runbook을 만듭니다. Runbook을 만든 후에는 다음의 정보를 사용 하 여 runbook을 편집할 수 있습니다.

* [Azure Automation에서 텍스트 runbook 편집](automation-edit-textual-runbook.md) 
* [Automation runbook에 대 한 주요 Windows PowerShell 워크플로 개념 알아보기](automation-powershell-workflow.md)
* [Azure Automation에서 그래픽 작성](automation-graphical-authoring-intro.md)
* [Azure Automation에서 Python 2 패키지 관리](python-packages.md)

### <a name="create-a-runbook-in-the-azure-portal"></a>Azure Portal에서 Runbook 만들기

1. Azure Portal에서 Automation 계정을 엽니다.
2. 허브의 **프로세스 자동화** 아래에서 **runbook** 을 선택 하 여 runbook 목록을 엽니다.
3. **Runbook 만들기를**클릭 합니다.
4. Runbook의 이름을 입력 하 고 해당 [형식을](automation-runbook-types.md)선택 합니다. Runbook 이름은 문자로 시작 해야 하며 문자, 숫자, 밑줄 및 대시를 포함할 수 있습니다.
5. **만들기** 를 클릭하여 Runbook을 만들고 편집기를 엽니다.

### <a name="create-a-runbook-with-powershell"></a>PowerShell을 사용하여 Runbook 만들기

[AzAutomationRunbook](https://docs.microsoft.com/powershell/module/az.automation/new-azautomationrunbook?view=azps-3.5.0) cmdlet을 사용 하 여 빈 runbook을 만듭니다. `Type` 매개 변수를 사용 하 여에 대해 `New-AzAutomationRunbook`정의 된 runbook 유형 중 하나를 지정 합니다.

다음 예에서는 비어 있는 새 runbook을 만드는 방법을 보여 줍니다.

```azurepowershell-interactive
New-AzAutomationRunbook -AutomationAccountName MyAccount `
-Name NewRunbook -ResourceGroupName MyResourceGroup -Type PowerShell
```

## <a name="import-a-runbook"></a>Runbook 가져오기

PowerShell 또는 PowerShell 워크플로 (**ps1**) 스크립트, 그래픽 runbook (**Graphrunbook**) 또는 Python 2 스크립트 (**. py**)를 가져와서 고유한 runbook을 만들 수 있습니다.  다음 사항을 고려하여 가져오기 동안 만들어지는 [Runbook 유형](automation-runbook-types.md)을 지정해야 합니다.

* 워크플로를 포함 하지 않는 **ps1** 파일을 [Powershell Runbook](automation-runbook-types.md#powershell-runbooks) 또는 [powershell 워크플로 runbook](automation-runbook-types.md#powershell-workflow-runbooks)으로 가져올 수 있습니다. PowerShell 워크플로 runbook으로 가져오면 워크플로로 변환 됩니다. 이 경우 변경 내용을 설명 하는 주석이 runbook에 포함 됩니다.

* Powershell 워크플로를 포함 하는 **ps1** 파일만 [powershell 워크플로 runbook](automation-runbook-types.md#powershell-workflow-runbooks)으로 가져올 수 있습니다. 파일에 여러 PowerShell 워크플로가 포함 되어 있으면 가져오기가 실패 합니다. 각 워크플로를 자체 파일에 저장하 고 각각 개별적으로 가져와야 합니다.

* Powershell 스크립트 엔진에서 인식할 수 없으므로 powershell 워크플로를 포함 하는 **ps1** 파일을 [powershell runbook](automation-runbook-types.md#powershell-runbooks)으로 가져오지 마십시오.

* 새 [그래픽 runbook](automation-runbook-types.md#graphical-runbooks)으로 **graphrunbook** 파일을 가져옵니다. 

### <a name="import-a-runbook-from-the-azure-portal"></a>Azure Portal에서 runbook 가져오기

Azure Automation에 스크립트 파일을 가져오려면 다음 절차를 사용할 수 있습니다.

> [!NOTE]
> 포털을 사용 하 여 PowerShell 워크플로 runbook으로 ps1 파일만 가져올 수 있습니다 **.**

1. Azure Portal에서 Automation 계정을 엽니다.
2. **프로세스 자동화** 아래에서 **Runbook**을 선택하여 Runbook 목록을 엽니다.
3. **Runbook 가져오기를**클릭 합니다.
4. **Runbook 파일** 을 클릭 하 고 가져올 파일을 선택 합니다.
5. **이름** 필드를 사용 하는 경우 runbook 이름을 변경할 수 있습니다. 이름은 문자로 시작 해야 하며 문자, 숫자, 밑줄 및 대시를 포함할 수 있습니다.
6. [Runbook 형식](automation-runbook-types.md)이 자동으로 선택되지만 해당 제한을 고려한 후에 형식을 변경할 수 있습니다.
7. **만들기**를 클릭합니다. 새 Runbook이 Automation 계정의 Runbook 목록에 표시됩니다.
8. 실행에 앞서 [Runbook을 게시](#publish-a-runbook) 해야 합니다.

> [!NOTE]
> 그래픽 runbook을 가져온 후에는 다른 형식으로 변환할 수 있습니다. 그러나 그래픽 runbook을 텍스트 runbook으로 변환할 수는 없습니다.

### <a name="import-a-runbook-with-windows-powershell"></a>Windows PowerShell을 사용 하 여 runbook 가져오기

[AzAutomationRunbook](https://docs.microsoft.com/powershell/module/az.automation/import-azautomationrunbook?view=azps-3.5.0) cmdlet을 사용 하 여 스크립트 파일을 초안 runbook으로 가져올 수 있습니다. Runbook이 이미 있는 경우 매개 변수를 `Force` cmdlet과 함께 사용 하지 않으면 가져오기가 실패 합니다.

다음 예제에서는 스크립트 파일을 runbook으로 가져오는 방법을 보여 줍니다.

```azurepowershell-interactive
$automationAccountName =  "AutomationAccount"
$runbookName = "Sample_TestRunbook"
$scriptPath = "C:\Runbooks\Sample_TestRunbook.ps1"
$RGName = "ResourceGroup"

Import-AzAutomationRunbook -Name $runbookName -Path $scriptPath `
-ResourceGroupName $RGName -AutomationAccountName $automationAccountName `
-Type PowerShellWorkflow
```

## <a name="handle-resources"></a>리소스 처리

Runbook에서 [리소스](automation-runbook-execution.md#resources)를 만드는 경우 스크립트가 리소스를 만들기 전에 이미 존재 하는지 확인 해야 합니다. 기본 예는 다음과 같습니다.

```powershell
$vmName = "WindowsVM1"
$resourceGroupName = "myResourceGroup"
$myCred = Get-AutomationPSCredential "MyCredential"
$vmExists = Get-AzResource -Name $vmName -ResourceGroupName $resourceGroupName

if(!$vmExists)
    {
    Write-Output "VM $vmName does not exist, creating"
    New-AzVM -Name $vmName -ResourceGroupName $resourceGroupName -Credential $myCred
    }
else
    {
    Write-Output "VM $vmName already exists, skipping"
    }
```

## <a name="retrieve-details-from-activity-log"></a>활동 로그에서 세부 정보를 검색 합니다.

Automation 계정에 대 한 [활동 로그](automation-runbook-execution.md#activity-logging) 에서 runbook을 시작한 개인 또는 계정과 같은 runbook 세부 정보를 검색할 수 있습니다. 다음 PowerShell 예제에서는 지정 된 runbook을 실행 하는 마지막 사용자를 제공 합니다.

```powershell-interactive
$SubID = "00000000-0000-0000-0000-000000000000"
$AutomationResourceGroupName = "MyResourceGroup"
$AutomationAccountName = "MyAutomationAccount"
$RunbookName = "MyRunbook"
$StartTime = (Get-Date).AddDays(-1)
$JobActivityLogs = Get-AzLog -ResourceGroupName $AutomationResourceGroupName -StartTime $StartTime `
                                | Where-Object {$_.Authorization.Action -eq "Microsoft.Automation/automationAccounts/jobs/write"}

$JobInfo = @{}
foreach ($log in $JobActivityLogs)
{
    # Get job resource
    $JobResource = Get-AzResource -ResourceId $log.ResourceId

    if ($JobInfo[$log.SubmissionTimestamp] -eq $null -and $JobResource.Properties.runbook.name -eq $RunbookName)
    {
        # Get runbook
        $Runbook = Get-AzAutomationJob -ResourceGroupName $AutomationResourceGroupName -AutomationAccountName $AutomationAccountName `
                                            -Id $JobResource.Properties.jobId | ? {$_.RunbookName -eq $RunbookName}

        # Add job information to hashtable
        $JobInfo.Add($log.SubmissionTimestamp, @($Runbook.RunbookName,$Log.Caller, $JobResource.Properties.jobId))
    }
}
$JobInfo.GetEnumerator() | sort key -Descending | Select-Object -First 1
```

## <a name="track-progress"></a>진행률 추적

Runbook을 다시 사용 하 고 다시 시작할 수 있는 논리를 사용 하 여 모듈식으로 작성 하는 것이 좋습니다. Runbook에서 진행률을 추적 하면 문제가 있는 경우 runbook 논리가 올바르게 실행 되도록 할 수 있습니다. 

저장소 계정, 데이터베이스, 공유 파일 등의 외부 원본을 사용 하 여 runbook의 진행 상황을 추적할 수 있습니다. Runbook에서 논리를 만들어서 먼저 수행 된 마지막 작업의 상태를 확인 합니다. 그런 다음 검사 결과에 따라 논리는 runbook의 특정 작업을 건너뛰거나 계속할 수 있습니다.

## <a name="prevent-concurrent-jobs"></a>동시 작업 방지

여러 작업에서 동시에 실행 하는 경우 일부 runbook은 이상 작업으로 작동 합니다. 이 경우 runbook이 이미 실행 중인 작업이 있는지 확인 하는 논리를 구현 하는 것이 중요 합니다. 기본 예는 다음과 같습니다.

```powershell
# Authenticate to Azure
$connection = Get-AutomationConnection -Name AzureRunAsConnection
Connect-AzAccount -ServicePrincipal -Tenant $connection.TenantID `
-ApplicationId $connection.ApplicationID -CertificateThumbprint $connection.CertificateThumbprint

$AzContext = Select-AzSubscription -SubscriptionId $connection.SubscriptionID

# Check for already running or new runbooks
$runbookName = "<RunbookName>"
$rgName = "<ResourceGroupName>"
$aaName = "<AutomationAccountName>"
$jobs = Get-AzAutomationJob -ResourceGroupName $rgName -AutomationAccountName $aaName -RunbookName $runbookName -AzContext $AzureContext

# Check to see if it is already running
$runningCount = ($jobs | ? {$_.Status -eq "Running"}).count

If (($jobs.status -contains "Running" -And $runningCount -gt 1 ) -Or ($jobs.Status -eq "New")) {
    # Exit code
    Write-Output "Runbook is already running"
    Exit 1
} else {
    # Insert Your code here
}
```

## <a name="handle-transient-errors-in-a-time-dependent-script"></a>시간 종속 스크립트의 일시적인 오류를 처리 합니다.

Runbook은 강력 하 고 오류를 다시 시작 하거나 실패할 수 있는 일시적인 오류를 비롯 하 여 [오류](automation-runbook-execution.md#errors)를 처리할 수 있어야 합니다. Runbook이 실패 하면 Azure Automation 다시 시도 합니다.

Runbook이 시간 제약 조건 내에서 정상적으로 실행 되는 경우 스크립트가 실행 시간을 확인 하는 논리를 구현 하도록 합니다. 이 검사는 특정 시간 동안에만 시작, 종료 또는 확장 등의 작업 실행을 보장 합니다.

> [!NOTE]
> Azure sandbox 프로세스의 현지 시간은 UTC로 설정 됩니다. Runbook에서 날짜 및 시간을 계산 하는 것을 고려해 야 합니다.

## <a name="work-with-multiple-subscriptions"></a>여러 구독 작업

Runbook은 [구독과](automation-runbook-execution.md#subscriptions)함께 작업할 수 있어야 합니다. 예를 들어 여러 구독을 처리 하기 위해 runbook은 [AzContextAutosave](https://docs.microsoft.com/powershell/module/Az.Accounts/Disable-AzContextAutosave?view=azps-3.5.0) cmdlet을 사용 합니다. 이 cmdlet을 사용 하면 동일한 샌드박스에서 실행 되는 다른 runbook에서 인증 컨텍스트가 검색 되지 않습니다. Runbook은 또한 Az module`AzContext` cmdlet에 대 한 매개 변수를 사용 하 여 적절 한 컨텍스트를 전달 합니다.

```powershell
# Ensures that you do not inherit an AzContext in your runbook
Disable-AzContextAutosave –Scope Process

$Conn = Get-AutomationConnection -Name AzureRunAsConnection
Connect-AzAccount -ServicePrincipal `
-Tenant $Conn.TenantID `
-ApplicationId $Conn.ApplicationID `
-CertificateThumbprint $Conn.CertificateThumbprint

$context = Get-AzContext

$ChildRunbookName = 'ChildRunbookDemo'
$AutomationAccountName = 'myAutomationAccount'
$ResourceGroupName = 'myResourceGroup'

Start-AzAutomationRunbook `
    -ResourceGroupName $ResourceGroupName `
    -AutomationAccountName $AutomationAccountName `
    -Name $ChildRunbookName `
    -DefaultProfile $context
```

## <a name="work-with-a-custom-script"></a>사용자 지정 스크립트 작업

> [!NOTE]
> 일반적으로 Log Analytics 에이전트가 설치 된 호스트에서 사용자 지정 스크립트 및 runbook을 실행할 수 없습니다. 

사용자 지정 스크립트를 사용 하려면 다음을 수행 합니다.

1. Automation 계정을 만들고 [참가자 역할](automation-role-based-access-control.md)을 가져옵니다.
2. [Azure 작업 영역에 계정을 연결](https://docs.microsoft.com/azure/security-center/security-center-enable-data-collection.md)합니다.
3. [Hybrid Runbook Worker](automation-hybrid-runbook-worker.md), [업데이트 관리](automation-update-management.md)또는 다른 자동화 기능을 사용 하도록 설정 합니다. 
4. Linux 컴퓨터의 경우 높은 권한이 필요 합니다. 로그인 하 여 [서명 확인을 해제](automation-linux-hrw-install.md#turn-off-signature-validation)합니다.

## <a name="test-a-runbook"></a>Runbook 테스트

Runbook을 테스트할 때 [초안 버전](#publish-a-runbook) 이 실행되며 해당 Runbook에서 수행하는 모든 작업이 완료됩니다. 작업 기록은 생성 되지 않지만 [출력](automation-runbook-output-and-messages.md#output-stream) 및 [경고 및 오류](automation-runbook-output-and-messages.md#message-streams) 스트림은 테스트 출력 창에 표시 됩니다. [자세한 정보 표시 스트림에](automation-runbook-output-and-messages.md#message-streams) 대 한 메시지는 [VerbosePreference](automation-runbook-output-and-messages.md#preference-variables) 변수가로 `Continue`설정 된 경우에만 출력 창에 표시 됩니다.

초안 버전을 실행하더라도 Runbook은 정상적으로 실행되며, 해당 환경의 리소스에 대해 모든 작업을 수행합니다. 이러한 이유로 비프로덕션 리소스에서만 Runbook을 테스트해야 합니다.

각 [runbook 유형을](automation-runbook-types.md) 테스트 하는 절차는 동일 합니다. Azure Portal에서 텍스트 편집기와 그래픽 편집기 간의 테스트에는 차이가 없습니다.

1. [텍스트 편집기나](automation-edit-textual-runbook.md) [그래픽 편집기](automation-graphical-authoring-intro.md)에서 runbook의 초안 버전을 엽니다.
1. 테스트 **를 클릭 하 여** 테스트 페이지를 엽니다.
1. Runbook에 매개 변수가 있는 경우 해당 매개 변수는 테스트에 사용할 값을 제공할 수 있는 왼쪽 창에 나열 됩니다.
1. [Hybrid Runbook Worker](automation-hybrid-runbook-worker.md)에서 테스트를 실행 하려면 **실행 설정** 을 **Hybrid Worker** 로 변경 하 고 대상 그룹의 이름을 선택 합니다.  그렇지 않은 경우 기본 **Azure** 를 유지하여 클라우드에서 테스트를 실행합니다.
1. **시작**을 클릭하여 테스트를 시작합니다.
1. 출력 창 아래의 단추를 사용 하 여 [PowerShell 워크플로](automation-runbook-types.md#powershell-workflow-runbooks) 또는 [그래픽](automation-runbook-types.md#graphical-runbooks) runbook을 테스트 하는 동안 중지 하거나 일시 중단할 수 있습니다. Runbook을 일시 중단하는 경우 일시 중단하기 전에 현재 작업을 완료합니다. Runbook이 일시 중단되면 중지하거나 다시 시작할 수 있습니다.
1. 출력 창에서 runbook의 출력을 검사 합니다.

## <a name="publish-a-runbook"></a>Runbook 게시

새 Runbook을 만들거나 가져올 때는 게시해야 실행할 수 있습니다. Azure Automation의 각 runbook에는 초안 버전과 게시 된 버전이 있습니다. 게시된 버전만 실행할 수 있으며 초안 버전만 편집할 수 있습니다. 초안 버전을 변경해도 게시된 버전은 영향을 받지 않습니다. 초안 버전을 사용할 수 있도록 설정 해야 하는 경우 게시 하 여 현재 게시 된 버전을 초안 버전으로 덮어씁니다.

### <a name="publish-a-runbook-in-the-azure-portal"></a>Azure Portal에 runbook 게시

1. Azure Portal에서 Runbook을 엽니다.
2. **편집**을 클릭합니다.
3. **게시** 를 클릭 한 다음 확인 메시지에 대 한 응답으로 **예** 를 클릭 합니다.

### <a name="publish-a-runbook-using-powershell"></a>PowerShell을 사용 하 여 runbook 게시

[AzAutomationRunbook](https://docs.microsoft.com/powershell/module/Az.Automation/Publish-AzAutomationRunbook?view=azps-3.5.0) cmdlet을 사용 하 여 runbook을 게시 합니다. 

```azurepowershell-interactive
$automationAccountName =  "AutomationAccount"
$runbookName = "Sample_TestRunbook"
$RGName = "ResourceGroup"

Publish-AzAutomationRunbook -AutomationAccountName $automationAccountName `
-Name $runbookName -ResourceGroupName $RGName
```

## <a name="schedule-a-runbook-in-the-azure-portal"></a>Azure Portal에서 runbook 예약

Runbook이 게시 되 면 작업에 대해 예약할 수 있습니다.

1. Azure Portal에서 Runbook을 엽니다.
2. **리소스**에서 **일정** 을 선택 합니다.
3. **일정 추가를**선택 합니다.
4. Runbook 예약 창에서 **runbook에 일정 연결**을 선택 합니다.
5. 일정 창에서 **새 일정 만들기** 를 선택 합니다.
6. 새 일정 창에 이름, 설명 및 기타 매개 변수를 입력 합니다. 
7. 일정이 생성 되 면 강조 표시 하 고 **확인**을 클릭 합니다. 이제 runbook에 연결 됩니다.
8. 사용자의 사서함에서 runbook 상태를 알리는 전자 메일을 찾습니다.

## <a name="obtain-job-statuses"></a>작업 상태 가져오기

### <a name="view-statuses-in-the-azure-portal"></a>Azure Portal 상태 보기

Azure Automation에서 작업 처리에 대 한 세부 정보는 [작업](automation-runbook-execution.md#jobs)에 제공 됩니다. Runbook 작업을 볼 준비가 되 면 Azure Portal를 사용 하 여 Automation 계정에 액세스 합니다. 오른쪽에서 **작업 통계**의 모든 runbook 작업에 대 한 요약을 볼 수 있습니다. 

![작업 통계 타일](./media/manage-runbooks/automation-account-job-status-summary.png)

요약에는 실행 된 각 작업에 대 한 작업 상태의 수와 그래픽 표현이 표시 됩니다.

타일을 클릭하면 실행된 모든 작업의 요약 목록이 포함된 작업 페이지가 표시됩니다. 이 페이지에는 각 작업의 상태, runbook 이름, 시작 시간 및 완료 시간이 표시 됩니다.

![Automation 계정 작업 페이지](./media/manage-runbooks/automation-account-jobs-status-blade.png)

작업 **필터링**을 선택 하 여 작업 목록을 필터링 할 수 있습니다. 드롭다운 목록에서 특정 runbook, 작업 상태 또는 선택 항목을 필터링 하 고 검색에 대 한 시간 범위를 제공 합니다.

![작업 상태 필터링](./media/manage-runbooks/automation-account-jobs-filter.png)

또는 Automation 계정의 Runbook 페이지에서 runbook을 선택한 다음 **작업**을 선택 하 여 특정 runbook에 대 한 작업 요약 세부 정보를 볼 수 있습니다. 이 작업은 작업 페이지를 표시 합니다. 여기에서 작업 레코드를 클릭 하 여 세부 정보 및 출력을 볼 수 있습니다.

![Automation 계정 작업 페이지](./media/manage-runbooks/automation-runbook-job-summary-blade.png)

### <a name="retrieve-job-statuses-using-powershell"></a>PowerShell을 사용 하 여 작업 상태 검색

[AzAutomationJob](https://docs.microsoft.com/powershell/module/Az.Automation/Get-AzAutomationJob?view=azps-3.7.0) cmdlet을 사용 하 여 runbook에 대해 만들어진 작업과 특정 작업의 세부 정보를 검색 합니다. 을 사용 하 여 `Start-AzAutomationRunbook`runbook을 시작 하는 경우 결과 작업이 반환 됩니다. [AzAutomationJobOutput](https://docs.microsoft.com/powershell/module/Az.Automation/Get-AzAutomationJobOutput?view=azps-3.5.0) 를 사용 하 여 작업 출력을 검색 합니다.

다음 예에서는 샘플 runbook에 대 한 마지막 작업을 가져오고 해당 상태, runbook 매개 변수에 제공 된 값 및 작업 출력을 표시 합니다.

```azurepowershell-interactive
$job = (Get-AzAutomationJob –AutomationAccountName "MyAutomationAccount" `
–RunbookName "Test-Runbook" -ResourceGroupName "ResourceGroup01" | sort LastModifiedDate –desc)[0]
$job.Status
$job.JobParameters
Get-AzAutomationJobOutput -ResourceGroupName "ResourceGroup01" `
–AutomationAccountName "MyAutomationAcct" -Id $job.JobId –Stream Output
```

다음 예에서는 특정 작업에 대 한 출력을 검색 하 고 각 레코드를 반환 합니다. 레코드 중 하나에 대 한 [예외가](automation-runbook-execution.md#exceptions) 있는 경우 스크립트는 값 대신 예외를 씁니다. 이 동작은 예외가 출력 중에 정상적으로 기록 되지 않을 수 있는 추가 정보를 제공할 수 있기 때문에 유용 합니다.

```azurepowershell-interactive
$output = Get-AzAutomationJobOutput -AutomationAccountName <AutomationAccountName> -Id <jobID> -ResourceGroupName <ResourceGroupName> -Stream "Any"
foreach($item in $output)
{
    $fullRecord = Get-AzAutomationJobOutputRecord -AutomationAccountName <AutomationAccountName> -ResourceGroupName <ResourceGroupName> -JobId <jobID> -Id $item.StreamRecordId
    if ($fullRecord.Type -eq "Error")
    {
        $fullRecord.Value.Exception
    }
    else
    {
    $fullRecord.Value
    }
}
```

## <a name="next-steps"></a>다음 단계

* Runbook 처리에 대해 알아보려면 [Azure Automation에서 runbook 실행](automation-runbook-execution.md)을 참조 하세요.
* 텍스트 편집기를 사용 하 여 PowerShell 및 PowerShell 워크플로 runbook을 편집 하는 방법에 대해 자세히 알아보려면 [Azure Automation에서 텍스트 Runbook 편집](automation-edit-textual-runbook.md)을 참조 하세요.
* 그래픽 runbook 작성에 대해 자세히 알아보려면 [Azure Automation의 그래픽 작성](automation-graphical-authoring-intro.md)을 참조 하세요.
