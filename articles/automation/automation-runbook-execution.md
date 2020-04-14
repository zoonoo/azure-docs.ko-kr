---
title: Azure Automation에서 Runbook 실행
description: Azure Automation의 Runbook이 처리되는 방법에 대한 자세한 내용을 설명합니다.
services: automation
ms.subservice: process-automation
ms.date: 04/04/2019
ms.topic: conceptual
ms.openlocfilehash: de01a7a76a5d225770c273c67f864c83226ecd07
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/13/2020
ms.locfileid: "81261315"
---
# <a name="runbook-execution-in-azure-automation"></a>Azure Automation에서 Runbook 실행

Azure 자동화의 프로세스 자동화를 사용하면 PowerShell, PowerShell 워크플로 및 그래픽 런북을 만들고 관리할 수 있습니다. 자세한 내용은 [Azure 자동화 실행문서를](automation-runbook-types.md)참조하십시오. 

자동화는 내부에 정의된 논리에 따라 Runbook을 실행합니다. Runbook이 중단되면 처음에 다시 시작됩니다. 이 동작을 수행하려면 일시적인 문제가 발생할 경우 다시 시작되는 Runbook을 작성해야 합니다.

Azure Automation에서 Runbook을 시작하면 Runbook의 단일 실행 인스턴스인 작업이 만들어집니다. 각 작업은 Azure 구독에 연결하여 Azure 리소스에 액세스합니다. 공용 클라우드에서 해당 리소스에 액세스할 수 있는 경우에만 데이터 센터의 리소스에 액세스할 수 있습니다.

Azure Automation은 Runbook 실행 중에 각 작업을 실행하도록 작업자를 할당합니다. 작업자가 많은 Azure 계정에서 공유되지만 여러 Automation 계정의 작업은 서로 격리됩니다. 작업 요청 서비스를 제어할 수 없습니다.

Azure 포털에서 Runbook 목록을 보면 각 Runbook에 대해 시작된 각 작업의 상태가 표시됩니다. Azure Automation은 작업 로그를 최대 30일 동안 저장합니다. 

다음 다이어그램은 [PowerShell Runbook,](automation-runbook-types.md#powershell-runbooks) [PowerShell 워크플로 런북](automation-runbook-types.md#powershell-workflow-runbooks)및 [그래픽 런북에](automation-runbook-types.md#graphical-runbooks)대한 Runbook 작업의 수명 주기를 보여 주었습니다.

![작업 상태 - PowerShell 워크플로](./media/automation-runbook-execution/job-statuses.png)

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-dsr-and-stp-note.md)]

>[!NOTE]
>이 문서는 새 Azure PowerShell Az 모듈을 사용하도록 업데이트되었습니다. AzureRM 모듈은 적어도 2020년 12월까지 버그 수정을 수신할 예정이므로 계속 사용하셔도 됩니다. 새 Az 모듈 및 AzureRM 호환성에 대한 자세한 내용은 [새 Azure PowerShell Az 모듈 소개](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0)를 참조하세요. 하이브리드 Runbook 작업자의 Az 모듈 설치 지침은 [Azure PowerShell 모듈 설치를](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)참조하십시오. 자동화 계정의 경우 Azure 자동화 에서 [Azure PowerShell 모듈을 업데이트하는 방법을](automation-update-azure-modules.md)사용하여 모듈을 최신 버전으로 업데이트할 수 있습니다.

## <a name="where-to-run-your-runbooks"></a>Runbook을 실행하는 위치

Azure 자동화의 Runbook은 Azure 샌드박스 또는 [하이브리드 Runbook 작업자에서](automation-hybrid-runbook-worker.md)실행할 수 있습니다. 여러 작업에서 사용할 수 있는 공유 환경인 Azure 샌드박스에서 대부분의 Runbook을 쉽게 실행할 수 있습니다. 동일한 샌드박스를 사용하는 작업에는 샌드박스의 리소스 제한이 적용됩니다.

>[!NOTE]
>Azure 샌드박스 환경은 대화형 작업을 지원하지 않습니다. 또한 Win32 호출을 만드는 runbook에 로컬 MOF 파일을 사용해야 합니다.

하이브리드 Runbook 워커를 사용하여 역할을 호스팅하는 컴퓨터에서 직접 실행 책을 실행하고 환경의 로컬 리소스에 대해 실행할 수 있습니다. Azure Automation은 Runbook을 저장 및 관리한 다음 하나 이상의 할당된 컴퓨터에 배달합니다.

다음 표에는 각 실행에 대해 나열된 권장 실행 환경이 있는 일부 Runbook 실행 작업이 나열되어 있습니다.

|Task|최선의 선택|메모|
|---|---|---|
|Azure 리소스와 통합|Azure 샌드박스|Azure에서 호스팅되는 인증은 더 간단합니다. Azure VM에서 하이브리드 Runbook 워커를 사용하는 경우 [Azure 리소스에 관리되는 ID를](automation-hrw-run-runbooks.md#managed-identities-for-azure-resources)사용할 수 있습니다.|
|Azure 리소스 를 관리하기 위한 최적의 성능 확보|Azure 샌드박스|스크립트는 대기 시간이 적은 동일한 환경에서 실행됩니다.|
|운영 비용 최소화|Azure 샌드박스|계산 오버헤드가 없고 VM이 필요하지 않습니다.|
|장기 실행 스크립트 실행|Hybrid Runbook Worker|Azure 샌드박스에는 [리소스에 제한이 있습니다.](../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits)|
|로컬 서비스와 상호 작용|Hybrid Runbook Worker|호스트 컴퓨터에 직접 액세스할 수 있습니다.|
|타사 소프트웨어 및 실행 정보 요구|Hybrid Runbook Worker|운영 체제를 관리하고 소프트웨어를 설치할 수 있습니다.|
|Runbook으로 파일 또는 폴더 모니터링|Hybrid Runbook Worker|하이브리드 Runbook 작업자에서 [감시자 작업을](automation-watchers-tutorial.md) 사용합니다.|
|리소스 를 사용하는 스크립트 실행|Hybrid Runbook Worker| Azure 샌드박스에는 [리소스에 제한이 있습니다.](../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits)|
|특정 요구 사항이 있는 모듈 사용| Hybrid Runbook Worker|예는 다음과 같습니다.</br> WinSCP - winscp.exe에 대한 종속성 </br> IIS 관리 - IIS 의 사용 설정에 대한 종속성입니다.|
|설치 프로그램이 있는 모듈 설치|Hybrid Runbook Worker|샌드박스용 모듈은 복사를 지원해야 합니다.|
|4.7.2와 다른 .NET Framework 버전이 필요한 Runbook 또는 모듈 사용|Hybrid Runbook Worker|자동화 샌드박스에는 .NET Framework 4.7.2가 있으며 버전을 업그레이드할 수 있는 방법은 없습니다.|
|고도가 필요한 스크립트 실행|Hybrid Runbook Worker|샌드박스는 고도를 허용하지 않습니다. 하이브리드 Runbook 워커를 사용하면 고도가 필요한 명령을 실행할 때 UAC를 끄고 [Invoke-Command를](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/invoke-command?view=powershell-7) 사용할 수 있습니다.|
|WMI(Windows 관리 계측)에 액세스해야 하는 스크립트 실행|Hybrid Runbook Worker|클라우드의 샌드박스에서 실행중인 작업은 WMI에 액세스할 수 없습니다. |

## <a name="runbook-behavior"></a>Runbook 동작

### <a name="creating-resources"></a>리소스 만들기

Runbook에서 리소스를 만드는 경우 스크립트는 리소스를 만들기 전에 리소스가 이미 있는지 확인해야 합니다. 다음은 기본적인 예입니다.

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

### <a name="supporting-time-dependent-scripts"></a>시간에 따라 달라진 스크립트 지원

Runbook은 강력해야 하며 다시 시작하거나 실패할 수 있는 일시적인 오류를 처리할 수 있어야 합니다. Runbook에 실패하면 Azure 자동화에서 다시 시도합니다.

Runbook이 일반적으로 시간 제약 조건 내에서 실행되는 경우 실행 시간을 확인하는 스크립트 구현 논리가 있습니다. 이 검사는 특정 시간에만 시작, 종료 또는 확장과 같은 작업을 실행할 수 있도록 합니다.

> [!NOTE]
> Azure 샌드박스 프로세스의 현지 시간이 UTC로 설정됩니다. Runbook의 날짜 및 시간 계산은 이 사실을 고려해야 합니다.

### <a name="tracking-progress"></a>진행률 추적

Runbook을 모듈식으로 작성하여 쉽게 재사용하고 다시 시작할 수 있는 논리를 사용하는 것이 좋습니다. Runbook에서 진행률을 추적하는 것은 문제가 있는 경우 Runbook 논리가 올바르게 실행되도록 하는 좋은 방법입니다. 저장소 계정, 데이터베이스 또는 공유 파일과 같은 외부 소스를 사용하여 Runbook의 진행 률을 추적할 수 있습니다. Runbook에서 논리를 만들어 마지막으로 수행한 작업의 상태를 먼저 확인할 수 있습니다. 그런 다음 검사 결과에 따라 논리는 Runbook에서 특정 작업을 건너뛰거나 계속할 수 있습니다.

### <a name="preventing-concurrent-jobs"></a>동시 작업 방지

일부 Runbook은 동시에 여러 작업에서 실행되는 경우 이상하게 행동합니다. 이 경우 Runbook에서 논리를 구현하여 실행 중인 작업이 이미 있는지 확인하는 것이 중요합니다. 다음은 기본적인 예입니다.

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

여러 구독을 처리하려면 Runbook에서 [사용 안 함-AzContextAutosave](https://docs.microsoft.com/powershell/module/Az.Accounts/Disable-AzContextAutosave?view=azps-3.5.0) cmdlet을 사용해야 합니다. 이 cmdlet은 동일한 샌드박스에서 실행되는 다른 Runbook에서 인증 컨텍스트가 검색되지 않도록 합니다. 또한 Runbook은`AzContext` Az 모듈 cmdlet의 매개 변수를 사용하여 적절한 컨텍스트를 전달합니다.

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

이 섹션에서는 Runbook에서 예외 또는 간헐적인 문제를 처리하는 몇 가지 방법을 설명합니다.

#### <a name="erroractionpreference"></a>ErrorActionPreference

[ErrorActionPreference](/powershell/module/microsoft.powershell.core/about/about_preference_variables#erroractionpreference) 변수는 PowerShell이 종료되지 않는 오류에 응답하는 방법을 결정합니다. 종료 오류는 항상 종료되며 의 `ErrorActionPreference`영향을 받지 않습니다.

Runbook을 사용하는 `ErrorActionPreference`경우 [Get-ChildItem](https://docs.microsoft.com/powershell/module/microsoft.powershell.management/get-childitem?view=powershell-7) cmdlet과 `PathNotFound` 같은 일반적으로 종료되지 않는 오류는 Runbook이 완료되지 않도록 합니다. 다음 예에서는 `ErrorActionPreference`의 사용법을 보여줍니다. 스크립트가 중지될 때 최종 [Write-Output](https://docs.microsoft.com/powershell/module/microsoft.powershell.utility/write-output?view=powershell-7) 명령이 실행되지 않습니다.

```powershell-interactive
$ErrorActionPreference = 'Stop'
Get-ChildItem -path nofile.txt
Write-Output "This message will not show"
```

#### <a name="try-catch-finally"></a>마지막으로 캐치를 시도

[마지막으로 Catch Catch는](/powershell/module/microsoft.powershell.core/about/about_try_catch_finally) 종료 오류를 처리하기 위해 PowerShell 스크립트에서 사용됩니다. 스크립트는 이 메커니즘을 사용하여 특정 예외 또는 일반 예외를 catch할 수 있습니다. `catch` 문을 추적하거나 오류를 처리하는 데 사용해야 합니다. 다음 예제는 존재하지 않는 파일을 다운로드하려고 시도합니다. 예외를 `System.Net.WebException` catch 하 고 다른 예외에 대 한 마지막 값을 반환 합니다.

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

[throw를](/powershell/module/microsoft.powershell.core/about/about_throw) 사용하여 종료 오류를 생성할 수 있습니다. 이 메커니즘은 Runbook에서 사용자 고유의 논리를 정의할 때 유용할 수 있습니다. 스크립트가 중지해야 하는 기준을 충족하는 경우 `throw` 문을 사용하여 중지할 수 있습니다. 다음 예제에서는 이 문을 사용하여 필수 함수 매개 변수를 표시합니다.

```powershell-interactive
function Get-ContosoFiles
{
  param ($path = $(throw "The Path parameter is required."))
  Get-ChildItem -Path $path\*.txt -recurse
}
```

### <a name="using-executables-or-calling-processes"></a>실행 파일 또는 호출 프로세스 사용

Azure 샌드박스에서 실행되는 Runbook은 실행**파일(.exe** 파일) 또는 하위 프로세스와 같은 호출 프로세스를 지원하지 않습니다. 그 이유는 Azure 샌드박스가 모든 기본 API에 액세스하지 못할 수 있는 컨테이너에서 실행되는 공유 프로세스이기 때문입니다. 타사 소프트웨어 또는 하위 프로세스에 대한 호출이 필요한 시나리오의 경우 [하이브리드 Runbook Worker에서](automation-hybrid-runbook-worker.md)Runbook을 실행해야 합니다.

### <a name="accessing-device-and-application-characteristics"></a>장치 및 애플리케이션 특성 액세스

Azure 샌드박스에서 실행되는 Runbook 작업은 장치 또는 응용 프로그램 특성에 액세스할 수 없습니다. Windows에서 성능 메트릭을 쿼리하는 데 사용되는 가장 일반적인 API는 WMI이며 일부 공통 메트릭은 메모리 및 CPU 사용량입니다. 그러나 클라우드에서 실행 중인 작업이 WBEM(웹 기반 엔터프라이즈 관리)의 Microsoft 구현에 액세스할 수 없기 때문에 어떤 API가 사용되는지는 중요하지 않습니다. 이 플랫폼은 CIM(공통 정보 모델)을 기반으로 구축되어 장치 및 애플리케이션 특성을 정의하기 위한 업계 표준을 제공합니다.

## <a name="handling-errors"></a>오류 처리

Runbook은 오류를 처리할 수 있어야 합니다. PowerShell에는 종료 및 비종료의 두 가지 유형의 오류가 있습니다. 종료 오류는 실행 북 실행이 발생할 때 중지합니다. 실행북이 실패의 작업 상태로 중지됩니다.

종료되지 않는 오류는 스크립트가 발생한 후에도 계속할 수 있도록 합니다. 종료되지 않는 오류의 예는 Runbook이 존재하지 않는 `Get-ChildItem` 경로가 있는 cmdlet을 사용할 때 발생하는 오류입니다. PowerShell은 경로가 없다는 것을 확인하고 오류가 throw한 후 다음 폴더로 계속 진행됩니다. 이 경우 오류는 Runbook 작업 상태를 실패로 설정하지 않으며 작업이 완료될 수도 있습니다. 종료되지 않는 오류에서 강제로 Runbook을 중지하려면 cmdlet에서 `ErrorAction Stop`을 사용할 수 있습니다.

## <a name="handling-jobs"></a>작업 처리

동일한 자동화 계정의 작업에 대해 실행 환경을 다시 사용할 수 있습니다. 단일 Runbook에서 동시에 많은 작업을 실행할 수 있습니다. 더 많은 작업을 동시에 실행할 수록 동일한 샌드박스에 더 자주 디스패치될 수 있습니다.

동일한 샌드박스 프로세스에서 실행되는 작업은 서로 영향을 줄 수 있습니다. 한 가지 예는 [연결 끊김-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/disconnect-azaccount?view=azps-3.7.0) cmdlet을 실행하는 것입니다. 이 cmdlet의 실행은 공유 샌드박스 프로세스의 각 Runbook 작업의 연결을 끊습니다.

Azure 샌드박스에서 실행되는 Runbook에서 시작된 PowerShell 작업은 전체 [PowerShell 언어 모드에서](/powershell/module/microsoft.powershell.core/about/about_language_modes)실행되지 않을 수 있습니다. Azure 자동화에서 작업과 상호 작용하는 방법에 대한 자세한 내용은 [PowerShell을 통해 작업 상태 검색을](#retrieving-job-status-using-powershell)참조하십시오.

### <a name="job-statuses"></a>작업 상태

다음 표는 작업에 대해 가능한 상태에 대해 설명합니다.

| 상태 | Description |
|:--- |:--- |
| Completed |작업이 완료되었습니다. |
| 실패 |그래픽 또는 PowerShell 워크플로 런북을 컴파일하지 못했습니다. PowerShell 스크립트 실행책을 시작하지 못했거나 작업에 예외가 있습니다. [Azure 자동화 Runbook 유형을](automation-runbook-types.md)참조하십시오.|
| Failed, waiting for resources |작업이 [공평 분배](#fair-share) 한도에 세 번 도달했기 때문에 실패했고 매번 동일한 검사점 또는 Runbook의 처음부터 시작되었습니다. |
| Queued |이 작업은 자동화 작업자의 리소스를 시작할 수 있도록 사용할 수 있도록 대기하고 있습니다. |
| 시작 중 |작업이 작업자에게 지정되었으며 시스템이 작업을 시작하고 있습니다. |
| Resuming |시스템이 일시 중단된 후 작업을 재개하는 중입니다. |
| 실행 중 |작업이 실행 중입니다. |
| Running, waiting for resources |작업이 공평 분배 한도에 도달했기 때문에 언로드되었습니다. 잠시 후 마지막 검사점에서 작업이 다시 시작됩니다. |
| 중지됨 |작업이 완료되기 전에 사용자에 의해 중지되었습니다. |
| 중지 중 |시스템이 작업을 중지하는 중입니다. |
| 일시 중단 |그래픽 [및 PowerShell 워크플로 런북에만](automation-runbook-types.md) 적용됩니다. 작업이 Runbook의 사용자, 시스템 또는 명령에 의해 일시 중단되었습니다. Runbook에 검사점이 없는 경우 처음부터 시작합니다. 검사점이 있으면 다시 시작되고 마지막 검사점에서 재개될 수 있습니다. 시스템은 예외가 발생할 때만 Runbook을 일시 중단합니다. 기본적으로 `ErrorActionPreference` 변수는 계속으로 설정되어 작업이 오류에서 계속 실행되고 있음을 나타냅니다. 기본 설정 변수가 중지로 설정되어 있으면 작업이 오류로 일시 중단됩니다.  |
| Suspending |그래픽 [및 PowerShell 워크플로 런북에만](automation-runbook-types.md) 적용됩니다. 시스템이 사용자의 요청에 따라 작업을 일시 중단하려고 합니다. Runbook의 다음 검사점에 도달해야만 Runbook을 일시 중단할 수 있습니다. 이미 마지막 검사점통과한 경우 일시 중단하기 전에 완료됩니다. |

### <a name="viewing-job-status-from-the-azure-portal"></a>Azure Portal에서 작업 상태 보기

모든 Runbook 작업에 대한 상태 요약을 보거나 Azure Portal에서 특정 Runbook 작업에 대한 세부 정보를 드릴다운할 수 있습니다. 또한 Runbook의 작업 상태와 작업 스트림을 전달하기 위해 Log Analytics 작업 영역과 통합하도록 구성할 수도 있습니다. Azure Monitor 로그와의 통합에 대한 자세한 내용은 [자동화에서 Azure Monitor 로그로의 작업 상태 및 작업 스트림 을](automation-manage-send-joblogs-log-analytics.md)참조하십시오.

선택한 자동화 계정 의 오른쪽에 **작업 통계** 타일 아래 모든 runbook 작업의 요약을 볼 수 있습니다.

![작업 통계 타일](./media/automation-runbook-execution/automation-account-job-status-summary.png)

이 타일에는 실행된 각 작업에 대한 작업 상태의 개수 및 그래픽 표현이 표시됩니다.

타일을 클릭하면 실행된 모든 작업의 요약 목록이 포함된 작업 페이지가 표시됩니다. 이 페이지에는 각 작업의 상태, Runbook 이름, 시작 시간 및 완료 시간이 표시됩니다.

![Automation 계정 작업 페이지](./media/automation-runbook-execution/automation-account-jobs-status-blade.png)

작업 필터를 선택하여 작업 목록을 필터링할 수 **있습니다.** 특정 Runbook, 작업 상태 또는 드롭다운 목록에서 선택 사항을 필터링하고 검색에 대한 시간 범위를 제공합니다.

![작업 상태 필터링](./media/automation-runbook-execution/automation-account-jobs-filter.png)

또는 자동화 계정의 Runbook 페이지에서 해당 Runbook을 선택한 다음 **작업** 타일을 선택하여 특정 Runbook에 대한 작업 요약 세부 정보를 볼 수 있습니다. 이 작업은 작업 페이지를 제공합니다. 여기에서 작업 레코드를 클릭하여 세부 정보 및 출력을 볼 수 있습니다.

![Automation 계정 작업 페이지](./media/automation-runbook-execution/automation-runbook-job-summary-blade.png)

### <a name="viewing-the-job-summary"></a>작업 요약 보기

위에서 설명한 작업 요약을 사용하면 특정 Runbook및 해당 최신 상태에 대해 생성된 모든 작업 목록을 볼 수 있습니다. 작업에 대한 자세한 정보 및 출력을 보려면 목록에서 해당 이름을 클릭합니다. 작업의 자세한 보기에는 해당 작업에 제공된 Runbook 매개 변수에 대한 값이 포함됩니다.

다음 단계를 수행하여 Runbook의 작업을 볼 수 있습니다.

1. Azure Portal에서 **Automation**을 선택한 다음 Automation 계정의 이름을 선택합니다.
2. 허브에서 **프로세스 자동화**에서 **Runbook을** 선택합니다.
3. Runbook 페이지에서 목록에서 Runbook을 선택합니다.
3. 선택한 Runbook에 대한 페이지에서 **작업** 타일을 클릭합니다.
4. 목록에서 작업 중 하나를 클릭하고 Runbook 작업 세부 정보 페이지에서 해당 세부 정보 및 출력을 봅니다.

### <a name="retrieving-job-status-using-powershell"></a>PowerShell을 사용하여 작업 상태 검색

cmdlet을 `Get-AzAutomationJob` 사용하여 Runbook에 대해 생성된 작업과 특정 작업의 세부 정보를 검색합니다. 을 사용하여 `Start-AzAutomationRunbook`RunBook을 시작하면 결과 작업이 반환됩니다. [Get-AzAutomationJobOutput을](https://docs.microsoft.com/powershell/module/Az.Automation/Get-AzAutomationJobOutput?view=azps-3.5.0) 사용하여 작업 출력을 검색합니다.

다음 예제에서는 샘플 Runbook에 대한 마지막 작업을 가져옵니다 및 해당 상태, Runbook 매개 변수에 대해 제공된 값 및 작업 출력을 표시합니다.

```azurepowershell-interactive
$job = (Get-AzAutomationJob –AutomationAccountName "MyAutomationAccount" `
–RunbookName "Test-Runbook" -ResourceGroupName "ResourceGroup01" | sort LastModifiedDate –desc)[0]
$job.Status
$job.JobParameters
Get-AzAutomationJobOutput -ResourceGroupName "ResourceGroup01" `
–AutomationAccountName "MyAutomationAcct" -Id $job.JobId –Stream Output
```

다음 예제는 특정 작업에 대한 출력을 검색하고 각 레코드를 반환합니다. 레코드 중 하나에 대한 예외가 있는 경우 스크립트는 값 대신 예외를 씁니다. 이 동작은 예외가 출력 중에 정상적으로 기록되지 않을 수 있는 추가 정보를 제공할 수 있기 때문에 유용합니다.

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

## <a name="getting-details-from-the-activity-log"></a>활동 로그에서 세부 정보 가져오기

자동화 계정의 활동 로그에서 Runbook을 시작한 사람 또는 계정과 같은 Runbook 세부 정보를 검색할 수 있습니다. 다음 PowerShell 예제에서는 지정된 Runbook을 실행할 마지막 사용자를 제공합니다.

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

## <a name="sharing-resources-among-runbooks"></a><a name="fair-share"></a>Runbook 간에 리소스 공유

클라우드의 모든 Runbook 간에 리소스를 공유하기 위해 Azure Automation은 3시간 이상 실행된 모든 작업을 일시적으로 언로드하거나 중지합니다. [PowerShell 런북](automation-runbook-types.md#powershell-runbooks) 및 [파이썬 런북에](automation-runbook-types.md#python-runbooks) 대한 작업은 중지되고 다시 시작되지 않으며 작업 상태가 중지됩니다.

장기 실행 작업의 경우 하이브리드 Runbook 워커를 사용하는 것이 좋습니다. Hybrid Runbook Worker는 공평 분배로 제한되지 않으며, Runbook을 실행할 수 있는 기간에 대한 제한이 없습니다. 다른 작업 [제한](../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits)은 Azure 샌드박스 및 Hybrid Runbook Worker에 모두 적용됩니다. 하이브리드 Runbook 작업자는 3시간 공정 한도에 제한을 받지 않지만 예기치 않은 로컬 인프라 문제에서 다시 시작을 지원하는 작업자에서 실행할 런북을 개발해야 합니다.

또 다른 옵션은 자식 Runbook을 사용하여 Runbook을 최적화하는 것입니다. 예를 들어 Runbook은 여러 데이터베이스의 데이터베이스 작업과 같은 여러 리소스에서 동일한 함수를 반복할 수 있습니다. 이 함수를 [자식 Runbook으로](automation-child-runbooks.md) 이동하고 [Start-AzAutomationRunbook을](https://docs.microsoft.com/powershell/module/az.automation/start-azautomationrunbook?view=azps-3.7.0)사용하여 runbook을 호출하도록 할 수 있습니다. 자식 실행책은 별도의 프로세스에서 병렬로 실행됩니다.

자식 Runbook을 사용하면 상위 Runbook을 완료하는 데 걸리는 총 시간이 줄어듭니다. Runbook은 [Get-AzAutomationJob](https://docs.microsoft.com/powershell/module/az.automation/get-azautomationjob?view=azps-3.7.0) cmdlet을 사용하여 자식이 완료된 후에도 더 많은 작업이 있는 경우 자식 Runbook의 작업 상태를 확인할 수 있습니다.

## <a name="next-steps"></a>다음 단계

* Runbook을 사용하는 방법을 알아보려면 [Azure Automation의 Runbook 관리를](manage-runbooks.md)참조하십시오.
* Azure 자동화에서 Runbook을 시작하는 데 사용할 수 있는 방법에 대해 자세히 알아보려면 [Azure Automation에서 Runbook 시작을](automation-starting-a-runbook.md)참조하십시오.
* 언어 참조 및 학습 모듈을 포함한 [PowerShell에](https://docs.microsoft.com/powershell/scripting/overview)대한 자세한 내용은 PowerShell 문서를 참조하십시오.
