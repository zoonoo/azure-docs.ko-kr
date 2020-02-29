---
title: Azure Automation에서 Runbook 실행
description: Azure Automation의 Runbook이 처리되는 방법에 대한 자세한 내용을 설명합니다.
services: automation
ms.subservice: process-automation
ms.date: 04/04/2019
ms.topic: conceptual
ms.openlocfilehash: 4070b004ee791a433b5aeb9e3e0cdd9662fb0429
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/29/2020
ms.locfileid: "78191149"
---
# <a name="runbook-execution-in-azure-automation"></a>Azure Automation에서 Runbook 실행

Runbook은 내부에 정의 된 논리에 따라 실행 됩니다. Runbook이 중단되면 해당 Runbook이 처음부터 다시 시작됩니다. 이 동작을 수행 하려면 일시적인 문제가 발생 하는 경우 다시 시작을 지 원하는 runbook을 작성 해야 합니다.

Azure Automation에서 runbook을 시작 하면 작업이 만들어집니다. 작업은 runbook의 단일 실행 인스턴스입니다. 각 작업은 Azure 구독에 연결 하 여 Azure 리소스에 액세스할 수 있습니다. 작업은 공용 클라우드에서 해당 리소스에 액세스할 수 있는 경우에만 데이터 센터의 리소스에 액세스할 수 있습니다.

Azure Automation는 runbook을 실행 하는 동안 각 작업을 실행할 작업자를 할당 합니다. 작업자가 많은 Azure 계정에서 공유되지만 여러 Automation 계정의 작업은 서로 격리됩니다. 사용자가 작업을 요청 하는 작업자 서비스를 제어할 수 없습니다.

Azure Portal에서 runbook 목록을 볼 때 각 runbook에 대해 시작 된 각 작업의 상태가 표시 됩니다. Azure Automation는 최대 30 일 동안 작업 로그를 저장 합니다. 

다음 다이어그램은 [powershell runbook](automation-runbook-types.md#powershell-runbooks), [그래픽 Runbook](automation-runbook-types.md#graphical-runbooks)및 [powershell 워크플로 runbook](automation-runbook-types.md#powershell-workflow-runbooks)에 대 한 runbook 작업의 수명 주기를 보여 줍니다.

![작업 상태 - PowerShell 워크플로](./media/automation-runbook-execution/job-statuses.png)

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-dsr-and-stp-note.md)]

>[!NOTE]
>이 문서는 새 Azure PowerShell Az 모듈을 사용하도록 업데이트되었습니다. AzureRM 모듈은 적어도 2020년 12월까지 버그 수정을 수신할 예정이므로 계속 사용하셔도 됩니다. 새 Az 모듈 및 AzureRM 호환성에 대한 자세한 내용은 [새 Azure PowerShell Az 모듈 소개](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0)를 참조하세요. Hybrid Runbook Worker에 대 한 Az module 설치 지침은 [Azure PowerShell 모듈 설치](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)를 참조 하세요. Automation 계정의 경우 [Azure Automation에서 Azure PowerShell 모듈을 업데이트 하는 방법을](automation-update-azure-modules.md)사용 하 여 모듈을 최신 버전으로 업데이트할 수 있습니다.

## <a name="where-to-run-your-runbooks"></a>Runbook을 실행하는 위치

Azure Automation의 runbook은 Azure sandbox 또는 [Hybrid Runbook Worker](automation-hybrid-runbook-worker.md)에서 실행할 수 있습니다. 대부분의 runbook은 여러 작업에서 사용할 수 있는 공유 환경인 Azure 샌드박스에서 쉽게 실행할 수 있습니다. 동일한 샌드박스를 사용하는 작업에는 샌드박스의 리소스 제한이 적용됩니다.

Hybrid Runbook Worker를 사용 하 여 역할을 호스트 하는 컴퓨터와 환경의 로컬 리소스에 대해 runbook을 직접 실행할 수 있습니다. Azure Automation는 runbook을 저장 하 고 관리 한 다음 하나 이상의 할당 된 컴퓨터에 배달 합니다.

다음 표에서는 각각에 대해 나열 된 권장 실행 환경을 사용 하는 몇 가지 runbook 실행 태스크를 보여 줍니다.

|작업|최선의 선택|참고|
|---|---|---|
|Azure 리소스와 통합|Azure 샌드박스|Azure에서 호스트 되는 인증은 더 간단 합니다. Azure VM에서 Hybrid Runbook Worker를 사용 하는 경우 [azure 리소스에 관리 되는 id](automation-hrw-run-runbooks.md#managed-identities-for-azure-resources)를 사용할 수 있습니다.|
|Azure 리소스를 관리 하기 위한 최적의 성능 얻기|Azure 샌드박스|스크립트는 대기 시간이 짧은 동일한 환경에서 실행 됩니다.|
|운영 비용 최소화|Azure 샌드박스|계산 오버 헤드가 없으며 VM이 필요 하지 않습니다.|
|장기 실행 스크립트 실행|Hybrid Runbook Worker|Azure 샌드박스에는 [리소스에 대 한 제한이](../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits)있습니다.|
|로컬 서비스와 상호 작용|Hybrid Runbook Worker|호스트 컴퓨터에 직접 액세스할 수 있습니다.|
|타사 소프트웨어 및 실행 파일 필요|Hybrid Runbook Worker|운영 체제를 관리 하 고 소프트웨어를 설치할 수 있습니다.|
|Runbook으로 파일 또는 폴더 모니터링|Hybrid Runbook Worker|Hybrid Runbook Worker에서 [감시자 태스크](automation-watchers-tutorial.md) 를 사용 합니다.|
|리소스를 많이 사용 하는 스크립트 실행|Hybrid Runbook Worker| Azure 샌드박스에는 [리소스에 대 한 제한이](../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits)있습니다.|
|특정 요구 사항과 함께 모듈 사용| Hybrid Runbook Worker|예는 다음과 같습니다.</br> WinSCP-winscp에 대 한 종속성 </br> IISAdministration-IIS 사용에 대 한 종속성입니다.|
|설치 관리자를 사용 하 여 모듈 설치|Hybrid Runbook Worker|샌드박스에 대 한 모듈은 복사를 지원 해야 합니다.|
|4\.7.2와 다른 .NET Framework 버전이 필요한 runbook 또는 모듈 사용|Hybrid Runbook Worker|Automation 샌드박스에는 .NET Framework 4.7.2 있지만이를 업그레이드할 수 있는 방법은 없습니다.|
|권한 상승이 필요한 스크립트 실행|Hybrid Runbook Worker|샌드박스에서 권한 상승을 허용 하지 않습니다. Hybrid Runbook Worker를 사용 하 여 UAC를 끄고 권한 상승이 필요한 명령을 실행할 때 **Invoke 명령을** 사용할 수 있습니다.|
|WMI에 액세스 해야 하는 스크립트 실행|Hybrid Runbook Worker|클라우드의 샌드박스에서 실행 되는 작업에는 WMI에 대 한 액세스 권한이 없습니다. |

## <a name="runbook-behavior"></a>Runbook 동작

### <a name="creating-resources"></a>리소스 만들기

Runbook에서 리소스를 만드는 경우 스크립트가 리소스를 만들기 전에 이미 존재 하는지 확인 해야 합니다. 기본 예는 다음과 같습니다.

```powershell
$vmName = "WindowsVM1"
$resourceGroupName = "myResourceGroup"
$myCred = Get-AutomationPSCredential "MyCredential"
$vmExists = Get-AzResource -Name $vmName -ResourceGroupName $resourceGroupName

if(!$vmExists)
    {
    Write-Output "VM $vmName does not exist, creating"
    New-AzureRMVM -Name $vmName -ResourceGroupName $resourceGroupName -Credential $myCred
    }
else
    {
    Write-Output "VM $vmName already exists, skipping"
    }
```

### <a name="supporting-time-dependent-scripts"></a>시간 종속 스크립트 지원

Runbook이 강력 하 고 오류를 다시 시작 하거나 실패할 수 있는 일시적인 오류를 처리할 수 있어야 합니다. Runbook이 실패 하면 Azure Automation 다시 시도 합니다.

Runbook이 시간 제약 조건 내에서 정상적으로 실행 되는 경우 스크립트가 실행 시간을 확인 하는 논리를 구현 하도록 합니다. 이 검사는 특정 시간 동안에만 시작, 종료 또는 확장 등의 작업 실행을 보장 합니다.

> [!NOTE]
> Azure sandbox 프로세스의 현지 시간은 UTC로 설정 됩니다. Runbook에서 날짜 및 시간을 계산 하는 것을 고려해 야 합니다.

### <a name="tracking-progress"></a>진행률 추적

Runbook을 사용 하 여 쉽게 다시 사용 하 고 다시 시작할 수 있도록 runbook을 구조화 된 방식으로 작성 하는 것이 좋습니다. Runbook에서 진행률을 추적 하는 것은 문제가 있는 경우 runbook 논리가 올바르게 실행 되도록 하는 좋은 방법입니다. 저장소 계정, 데이터베이스, 공유 파일 등의 외부 원본을 사용 하 여 runbook의 진행 상황을 추적할 수 있습니다. Runbook에서 논리를 만들어서 먼저 수행 된 마지막 작업의 상태를 확인할 수 있습니다. 그런 다음 검사 결과에 따라 논리는 runbook의 특정 작업을 건너뛰거나 계속할 수 있습니다.

### <a name="preventing-concurrent-jobs"></a>동시 작업 방지

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

### <a name="working-with-multiple-subscriptions"></a>여러 구독 작업

여러 구독을 처리 하려면 runbook에서 [AzContextAutosave](https://docs.microsoft.com/powershell/module/Az.Accounts/Disable-AzContextAutosave?view=azps-3.5.0) cmdlet을 사용 하 여 동일한 샌드박스에서 실행 되는 다른 runbook에서 인증 컨텍스트가 검색 되지 않도록 해야 합니다. 또한 runbook은 Az module cmdlet에 *AzContext* 매개 변수를 사용 하 고 적절 한 컨텍스트를 전달 합니다.

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

### <a name="handling-exceptions"></a>예외 처리

이 섹션에서는 runbook에서 예외 또는 일시적인 문제를 처리 하는 몇 가지 방법을 설명 합니다.

#### <a name="erroractionpreference"></a>$ErrorActionPreference

[$ErrorActionPreference](/powershell/module/microsoft.powershell.core/about/about_preference_variables#erroractionpreference) 변수는 PowerShell이 종료 되지 않는 오류에 응답 하는 방법을 결정 합니다. 종료 오류는 항상 종료 되며 *$ErrorActionPreference*의 영향을 받지 않습니다.

Runbook에서 *$ErrorActionPreference*사용 하는 경우 **Get-Childitem** Cmdlet의 **pathnotfound** 와 같은 일반적인 종료 되지 않는 오류는 runbook이 완료 되지 않도록 중지 합니다. 다음 예에서는 *$ErrorActionPreference*를 사용 하는 방법을 보여 줍니다. 스크립트를 중지 하면 최종 **쓰기 출력** 명령이 실행 되지 않습니다.

```powershell-interactive
$ErrorActionPreference = 'Stop'
Get-Childitem -path nofile.txt
Write-Output "This message will not show"
```

#### <a name="try-catch-finally"></a>마지막으로 Catch 시도

[Try Catch Finally](/powershell/module/microsoft.powershell.core/about/about_try_catch_finally) 는 PowerShell 스크립트에서 종료 오류를 처리 하는 데 사용 됩니다. 스크립트는이 메커니즘을 사용 하 여 특정 예외 또는 일반 예외를 catch 할 수 있습니다. **Catch** 문은 오류를 추적 하거나 처리 하는 데 사용 해야 합니다. 다음 예에서는 존재 하지 않는 파일을 다운로드 하려고 시도 합니다. WebException 예외를 catch 하 고 다른 예외에 대 한 마지막 값을 반환 합니다.

```powershell-interactive
try
{
   $wc = new-object System.Net.WebClient
   $wc.DownloadFile("http://www.contoso.com/MyDoc.doc")
}
catch [System.Net.WebException]
{
    "Unable to download MyDoc.doc from http://www.contoso.com."
}
catch
{
    "An error occurred that could not be resolved."
}
```

#### <a name="throw"></a>Throw

[Throw](/powershell/module/microsoft.powershell.core/about/about_throw) 를 사용 하 여 종료 오류를 생성할 수 있습니다. 이 메커니즘은 runbook에서 고유한 논리를 정의할 때 유용할 수 있습니다. 스크립트가 중지 되어야 하는 조건을 충족 하는 경우 **throw** 문을 사용 하 여 중지할 수 있습니다. 다음 예에서는이 문을 사용 하 여 필수 함수 매개 변수를 표시 합니다.

```powershell-interactive
function Get-ContosoFiles
{
  param ($path = $(throw "The Path parameter is required."))
  Get-ChildItem -Path $path\*.txt -recurse
}
```

### <a name="using-executables-or-calling-processes"></a>실행 파일 또는 호출 프로세스 사용

Azure 샌드박스에서 실행 되는 runbook은 실행 파일 ( **.exe** 파일) 또는 하위 프로세스와 같은 호출을 지원 하지 않습니다.  그 이유는 Azure sandbox는 모든 기본 Api에 대 한 액세스 권한이 없을 수 있는 컨테이너에서 실행 되는 공유 프로세스 이기 때문입니다. 타사 소프트웨어 또는 하위 프로세스에 대 한 호출이 필요한 시나리오의 경우 [Hybrid Runbook Worker](automation-hybrid-runbook-worker.md)에서 runbook을 실행 하는 것이 좋습니다.

### <a name="accessing-device-and-application-characteristics"></a>장치 및 응용 프로그램 특성 액세스

Azure 샌드박스에서 실행 되는 Runbook 작업은 장치나 응용 프로그램 특성에 액세스할 수 없습니다. Windows에서 성능 메트릭을 쿼리 하는 데 사용 되는 가장 일반적인 API는 WMI 이며, 일부 일반적인 메트릭은 메모리와 CPU 사용량입니다. 그러나 클라우드에서 실행 되는 작업에는 Microsoft의 WBEM (웹 기반 엔터프라이즈 관리) 구현에 대 한 액세스 권한이 없기 때문에 API를 사용 하는 것은 중요 하지 않습니다. 이 플랫폼은 CIM(Common Information Model) (CIM)를 기반으로 하며, 장치 및 응용 프로그램 특징을 정의 하는 산업 표준을 제공 합니다.

## <a name="handling-errors"></a>오류 처리

Runbook에서 오류를 처리할 수 있어야 합니다. PowerShell에는 종료 및 종료 되지 않는 두 가지 유형의 오류가 있습니다. 종료 오류가 발생 하면 runbook 실행이 중지 됩니다. 작업 상태가 **실패**로 인해 runbook이 중지 됩니다.

종료 되지 않는 오류는 스크립트가 발생 한 후에도 스크립트를 계속 실행할 수 있습니다. 종료 되지 않는 오류의 예는 runbook이 존재 하지 않는 경로를 사용 하 여 **Get ChildItem** cmdlet을 사용 하는 경우 발생 하는 오류입니다. PowerShell은 경로가 없다는 것을 확인하고 오류가 throw한 후 다음 폴더로 계속 진행됩니다. 이 경우 오류는 runbook 작업 상태 상태를 **실패**로 설정 하지 않으며 작업이 완료 될 수도 있습니다. 종료되지 않는 오류에서 강제로 Runbook을 중지하려면 cmdlet에서 `-ErrorAction Stop`을 사용할 수 있습니다.

## <a name="handling-jobs"></a>작업 처리

동일한 Automation 계정의 작업에 대 한 실행 환경을 다시 사용할 수 있습니다. 단일 Runbook에서 동시에 많은 작업을 실행할 수 있습니다. 더 많은 작업을 동시에 실행할 수록 동일한 샌드박스에 더 자주 디스패치될 수 있습니다.

동일한 샌드박스 프로세스에서 실행 되는 작업은 서로 영향을 줄 수 있습니다. 한 가지 예는 **AzAccount** cmdlet을 실행 하는 것입니다. 이 cmdlet을 실행 하면 공유 샌드박스 프로세스에서 각 runbook 작업의 연결이 끊어집니다.

Azure 샌드박스에서 실행 되는 runbook에서 시작 된 PowerShell 작업은 전체 언어 모드에서 실행 되지 않을 수 있습니다. PowerShell 언어 모드에 대해 자세히 알아보려면 [powershell 언어 모드](/powershell/module/microsoft.powershell.core/about/about_language_modes)를 참조 하세요. Azure Automation에서 작업과 상호 작용 하는 방법에 대 한 자세한 내용은 [PowerShell을 사용 하 여 작업 상태 검색](#retrieving-job-status-using-powershell)을 참조 하세요.

### <a name="job-statuses"></a>작업 상태

다음 표에서는 작업에 사용할 수 있는 상태에 대해 설명 합니다.

| 상태 | 설명 |
|:--- |:--- |
| 완료 |작업이 완료되었습니다. |
| 실패 |그래픽 또는 PowerShell 워크플로 runbook을 컴파일하지 못했습니다. PowerShell 스크립트 runbook을 시작 하지 못했거나 작업에서 예외가 발생 했습니다. [Azure Automation runbook 형식](automation-runbook-types.md)을 참조 하세요.|
| Failed, waiting for resources |작업이 [공평 분배](#fair-share) 한도에 세 번 도달했기 때문에 실패했고 매번 동일한 검사점 또는 Runbook의 처음부터 시작되었습니다. |
| 대기 |작업은 자동화 작업자의 리소스를 사용 하 여 시작할 수 있을 때까지 대기 중입니다. |
| 시작 중 |작업이 작업자에게 지정되었으며 시스템이 작업을 시작하고 있습니다. |
| Resuming |시스템이 일시 중단된 후 작업을 재개하는 중입니다. |
| 실행 중 |작업이 실행 중입니다. |
| Running, waiting for resources |작업이 공평 분배 한도에 도달 하 여 언로드 되었습니다. 잠시 후 마지막 검사점에서 작업이 다시 시작됩니다. |
| 중지됨 |작업이 완료되기 전에 사용자에 의해 중지되었습니다. |
| Stopping |시스템이 작업을 중지하는 중입니다. |
| Suspended |[그래픽 및 PowerShell 워크플로 runbook](automation-runbook-types.md) 에만 적용 됩니다. 작업이 Runbook의 사용자, 시스템 또는 명령에 의해 일시 중단되었습니다. Runbook에 검사점이 없으면 처음부터 시작 합니다. 검사점이 있으면 다시 시작되고 마지막 검사점에서 재개될 수 있습니다. 시스템은 예외가 발생 하는 경우에만 runbook을 일시 중단 합니다. 기본적으로 *Erroractionpreference 설정* 변수는 Continue로 설정 되며이는 작업이 오류 발생 시 **계속**실행 됨을 나타냅니다. 기본 설정 변수가 **Stop**으로 설정 된 경우에는 오류 발생 시 작업이 일시 중단 됩니다.  |
| Suspending |[그래픽 및 PowerShell 워크플로 runbook](automation-runbook-types.md) 에만 적용 됩니다. 시스템이 사용자의 요청에 따라 작업을 일시 중단하려고 합니다. Runbook의 다음 검사점에 도달해야만 Runbook을 일시 중단할 수 있습니다. 이미 마지막 검사점을 통과 한 경우 일시 중단 되기 전에 완료 됩니다. |

### <a name="viewing-job-status-from-the-azure-portal"></a>Azure Portal에서 작업 상태 보기

모든 Runbook 작업의 요약 상태를 보거나 Azure Portal에서 특정 Runbook 작업의 세부 정보로 드릴할 수 있습니다. 또한 Runbook의 작업 상태와 작업 스트림을 전달하기 위해 Log Analytics 작업 영역과 통합하도록 구성할 수도 있습니다. Azure Monitor 로그와 통합 하는 방법에 대 한 자세한 내용은 [자동화에서 작업 상태 및 작업 스트림을 Azure Monitor 로그로 전달](automation-manage-send-joblogs-log-analytics.md)을 참조 하세요.

선택한 Automation 계정의 오른쪽에서 **작업 통계** 타일 아래에 있는 모든 runbook 작업의 요약을 볼 수 있습니다.

![작업 통계 타일](./media/automation-runbook-execution/automation-account-job-status-summary.png)

이 타일에는 실행 된 각 작업에 대 한 작업 상태의 수와 그래픽 표현이 표시 됩니다.

타일을 클릭하면 실행된 모든 작업의 요약 목록이 포함된 **작업** 페이지가 표시됩니다. 이 페이지에는 각 작업의 상태, runbook 이름, 시작 시간 및 완료 시간이 표시 됩니다.

![Automation 계정 작업 페이지](./media/automation-runbook-execution/automation-account-jobs-status-blade.png)

작업 **필터링**을 선택 하 여 작업 목록을 필터링 할 수 있습니다. 드롭다운 목록에서 특정 runbook, 작업 상태 또는 선택 항목을 필터링 하 고 검색에 대 한 시간 범위를 제공 합니다.

![작업 상태 필터링](./media/automation-runbook-execution/automation-account-jobs-filter.png)

또는 Automation 계정의 **runbook** 페이지에서 runbook을 선택 하 고 **작업** 타일을 선택 하 여 특정 runbook에 대 한 작업 요약 세부 정보를 볼 수 있습니다. 이 작업은 **작업** 페이지를 표시 합니다. 여기에서 작업 레코드를 클릭 하 여 세부 정보 및 출력을 볼 수 있습니다.

![Automation 계정 작업 페이지](./media/automation-runbook-execution/automation-runbook-job-summary-blade.png)

### <a name="viewing-the-job-summary"></a>작업 요약 보기

위에서 설명한 작업 요약을 사용 하면 특정 runbook에 대해 생성 된 모든 작업의 목록과 가장 최근 상태를 확인할 수 있습니다. 작업에 대 한 세부 정보 및 출력을 보려면 목록에서 해당 이름을 클릭 합니다. 작업의 자세히 보기에는 해당 작업에 제공 된 runbook 매개 변수에 대 한 값이 포함 됩니다.

다음 단계를 수행하여 Runbook의 작업을 볼 수 있습니다.

1. Azure Portal에서 **Automation**을 선택한 다음 Automation 계정의 이름을 선택합니다.
2. 허브의 **프로세스 자동화**아래에서 **runbook** 을 선택 합니다.
3. **Runbook** 페이지의 목록에서 runbook을 선택 합니다.
3. 선택한 Runbook에 대한 페이지에서 **작업** 타일을 클릭합니다.
4. 목록에서 작업 중 하나를 클릭 하 고 runbook 작업 세부 정보 페이지에서 세부 정보 및 출력을 확인 합니다.

### <a name="retrieving-job-status-using-powershell"></a>PowerShell을 사용 하 여 작업 상태 검색

**AzAutomationJob** cmdlet을 사용 하 여 runbook에 대해 만들어진 작업과 특정 작업의 세부 정보를 검색 합니다. **AzAutomationRunbook**를 사용 하 여 PowerShell을 사용 하 여 runbook을 시작 하는 경우 결과 작업을 반환 합니다. [AzAutomationJobOutput](https://docs.microsoft.com/powershell/module/Az.Automation/Get-AzAutomationJobOutput?view=azps-3.5.0) 를 사용 하 여 작업 출력을 검색 합니다.

다음 예에서는 샘플 runbook에 대 한 마지막 작업을 가져오고 해당 상태, runbook 매개 변수에 제공 된 값 및 작업 출력을 표시 합니다.

```azurepowershell-interactive
$job = (Get-AzAutomationJob –AutomationAccountName "MyAutomationAccount" `
–RunbookName "Test-Runbook" -ResourceGroupName "ResourceGroup01" | sort LastModifiedDate –desc)[0]
$job.Status
$job.JobParameters
Get-AzAutomationJobOutput -ResourceGroupName "ResourceGroup01" `
–AutomationAccountName "MyAutomationAcct" -Id $job.JobId –Stream Output
```

다음 예에서는 특정 작업에 대 한 출력을 검색 하 고 각 레코드를 반환 합니다. 레코드 중 하나에 대 한 예외가 있는 경우 스크립트는 값 대신 예외를 씁니다. 이 동작은 예외가 출력 중에 정상적으로 기록 되지 않을 수 있는 추가 정보를 제공할 수 있기 때문에 유용 합니다.

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

## <a name="getting-details-from-the-activity-log"></a>활동 로그에서 세부 정보를 가져오는 중

Automation 계정에 대 한 활동 로그에서 runbook을 시작한 개인 또는 계정과 같은 runbook 세부 정보를 검색할 수 있습니다. 다음 PowerShell 예제에서는 지정 된 runbook을 실행 하는 마지막 사용자를 제공 합니다.

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

## <a name="fair-share"></a>Runbook 간 리소스 공유

클라우드의 모든 runbook에서 리소스를 공유 하기 위해 Azure Automation는 3 시간 이상 실행 된 작업을 일시적으로 언로드 또는 중지 합니다. [PowerShell runbook](automation-runbook-types.md#powershell-runbooks) 및 [Python runbook](automation-runbook-types.md#python-runbooks) 에 대 한 작업은 중지 되며 다시 시작 되지 않으며, 작업 상태가 **중지 됨**이 됩니다.

장기 실행 작업의 경우 Hybrid Runbook Worker를 사용 하는 것이 좋습니다. Hybrid Runbook Worker는 공평 분배로 제한되지 않으며, Runbook을 실행할 수 있는 기간에 대한 제한이 없습니다. 다른 작업 [제한](../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits)은 Azure 샌드박스 및 Hybrid Runbook Worker에 모두 적용됩니다. Hybrid Runbook Worker는 3 시간 공평 공유 제한에 의해서만 제한 되지 않지만 예기치 않은 로컬 인프라 문제에서 다시 시작을 지 원하는 작업자에서 실행할 runbook을 개발 해야 합니다.

또 다른 옵션은 자식 runbook을 사용 하 여 runbook을 최적화 하는 것입니다. 예를 들어 runbook은 여러 데이터베이스에서 데이터베이스 작업과 같은 여러 리소스에서 동일한 기능을 통해 반복 될 수 있습니다. 이 함수를 [자식 runbook](automation-child-runbooks.md) 으로 이동 하 고 Runbook에서 **AzAutomationRunbook**를 사용 하 여 호출 하도록 할 수 있습니다. 자식 runbook은 별도의 프로세스에서 병렬로 실행 됩니다.

자식 runbook을 사용 하면 부모 runbook을 완료 하는 데 걸리는 총 시간을 줄일 수 있습니다. **AzAutomationJob** cmdlet을 사용 하 여 자식 runbook에 대 한 작업 상태를 확인할 수 있습니다. 자식 runbook이 완료 된 후에도 작업을 수행할 수 있습니다.

## <a name="next-steps"></a>다음 단계

* Azure Automation에서 runbook을 시작 하는 데 사용할 수 있는 방법에 대해 자세히 알아보려면 [Azure Automation에서 Runbook 시작](automation-starting-a-runbook.md)을 참조 하세요.
* 언어 참조 및 학습 모듈을 비롯 한 PowerShell에 대 한 자세한 내용은 [Powershell 문서](https://docs.microsoft.com/powershell/scripting/overview)를 참조 하세요.
