---
title: Azure Automation Runbook을 사용하여 오류 해결
description: Azure Automation Runbook을 사용하여 문제를 해결하는 방법 알아보기
services: automation
author: georgewallace
ms.author: gwallace
ms.date: 01/24/2019
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: 65de80004dd05e3eb29f3313bc17405c40450d7a
ms.sourcegitcommit: d89032fee8571a683d6584ea87997519f6b5abeb
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/30/2019
ms.locfileid: "66397138"
---
# <a name="troubleshoot-errors-with-runbooks"></a>Runbook으로 오류 해결

## <a name="authentication-errors-when-working-with-azure-automation-runbooks"></a>Azure Automation Runbook을 사용할 때 인증 오류

### <a name="sign-in-failed"></a>시나리오: Azure 계정 로그인에 실패

#### <a name="issue"></a>문제

`Add-AzureAccount` 또는 `Connect-AzureRmAccount` cmdlet을 사용할 때 오류가 발생합니다.
:

```error
Unknown_user_type: Unknown User Type
```

#### <a name="cause"></a>원인

자격 증명 자산 이름이 올바르지 않으면 이 오류가 발생합니다. 또한 Automation 자격 증명 자산을 설정하는 데 사용한 사용자 이름과 암호가 올바르지 않은 경우에도 발생할 수 있습니다.

#### <a name="resolution"></a>해결 방법

무엇이 문제인지 확인하기 위해 다음 단계를 수행하세요.  

1. 특수 문자가 없는지 확인합니다. 이러한 문자로 **\@** 문자가 Azure에 연결하는 데 사용하는 Automation 자격 증명 자산 이름에 포함됩니다.  
2. Azure Automation 자격 증명에 저장된 사용자 이름 및 암호를 로컬 PowerShell ISE 편집기에서 사용할 수 있는지 확인합니다. PowerShell ISE에서 다음 cmdlet을 실행하여 사용자 이름과 암호가 올바른지 확인할 수 있습니다.  

   ```powershell
   $Cred = Get-Credential  
   #Using Azure Service Management
   Add-AzureAccount –Credential $Cred  
   #Using Azure Resource Manager  
   Connect-AzureRmAccount –Credential $Cred
   ```

3. 로컬에서 인증이 실패하면 Azure Active Directory 자격 증명을 올바르게 설정하지 않은 것입니다. Azure Active Directory 계정을 올바르게 설정하는 방법은 [Azure Active Directory를 사용하여 Azure에 인증](https://azure.microsoft.com/blog/azure-automation-authenticating-to-azure-using-azure-active-directory/) 블로그 게시물을 참조하세요.  

4. 일시적인 오류처럼 보이는 경우 인증 루틴에 다시 시도 논리를 추가하여 인증을 더 강화시킵니다.

   ```powershell
   # Get the connection "AzureRunAsConnection"
   $connectionName = "AzureRunAsConnection"
   $servicePrincipalConnection = Get-AutomationConnection -Name $connectionName

   $logonAttempt = 0
   $logonResult = $False

   while(!($connectionResult) -And ($logonAttempt -le 10))
   {
   $LogonAttempt++
   # Logging in to Azure...
   $connectionResult = Connect-AzureRmAccount `
      -ServicePrincipal `
      -TenantId $servicePrincipalConnection.TenantId `
      -ApplicationId $servicePrincipalConnection.ApplicationId `
      -CertificateThumbprint $servicePrincipalConnection.CertificateThumbprint

   Start-Sleep -Seconds 30
   }
   ```

### <a name="unable-to-find-subscription"></a>시나리오: Azure 구독을 찾을 수 없음

#### <a name="issue"></a>문제

`Select-AzureSubscription` 또는 `Select-AzureRmSubscription` cmdlet으로 작업하면 다음 오류가 발생합니다.

```error
The subscription named <subscription name> cannot be found.
```

#### <a name="error"></a>오류

이 오류가 발생할 수 있는 경우는 다음과 같습니다.

* 구독 이름이 올바르지 않습니다.

* 구독 세부 정보를 가져오려고 하는 Azure Active Directory 사용자가 구독 관리자로 구성되지 않았습니다.

#### <a name="resolution"></a>해결 방법

다음 단계에 따라 Azure에서 인증되고 선택하려고 하는 구독에 대한 액세스 권한이 있는지 확인합니다.  

1. 독립 실행형으로 작동하는지 확인하기 위해 Azure Automation 외부에서 스크립트를 테스트합니다.
2. `Select-AzureSubscription` cmdlet을 실행하기 전에 `Add-AzureAccount` cmdlet을 실행하고 있는지 확인합니다. 
3. Runbook의 시작 부분에 `Disable-AzureRmContextAutosave –Scope Process`를 추가합니다. 이 cmdlet은 모든 자격 증명이 현재 Runbook의 실행에만 적용되도록 합니다.
4. 이 오류 메시지가 계속 나타나면 `Add-AzureAccount` cmdlet 뒤에 **AzureRmContext** 매개 변수를 추가하여 코드를 수정한 다음, 해당 코드를 실행합니다.

   ```powershell
   Disable-AzureRmContextAutosave –Scope Process

   $Conn = Get-AutomationConnection -Name AzureRunAsConnection
   Connect-AzureRmAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationID $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint

   $context = Get-AzureRmContext

   Get-AzureRmVM -ResourceGroupName myResourceGroup -AzureRmContext $context
    ```

### <a name="auth-failed-mfa"></a>시나리오: Multi-Factor Authentication이 활성화되어 Azure 인증에 실패

#### <a name="issue"></a>문제

Azure 사용자 이름 및 암호를 사용하여 Azure에 인증할 때 다음 오류가 발생합니다.

```error
Add-AzureAccount: AADSTS50079: Strong authentication enrollment (proof-up) is required
```

#### <a name="cause"></a>원인

Azure 계정에서 Multi-Factor Authentication을 사용하면 Azure Active Directory 사용자를 사용하여 Azure에 인증할 수 없습니다. 그 대신 인증서 또는 서비스 주체를 사용하여 Azure에 인증해야 합니다.

#### <a name="resolution"></a>해결 방법

Azure 클래식 배포 모델 cmdlet에 인증서를 사용하려면 [인증서를 만들고 추가하여 Azure 서비스 관리](https://blogs.technet.com/b/orchestrator/archive/2014/04/11/managing-azure-services-with-the-microsoft-azure-automation-preview-service.aspx)를 참조하세요. Azure Resource Manager cmdlet에 서비스 주체를 사용하려면 [Azure Portal을 사용하여 서비스 주체 만들기](../../active-directory/develop/howto-create-service-principal-portal.md) 및 [Azure Resource Manager를 사용하여 서비스 주체 인증](../../active-directory/develop/howto-authenticate-service-principal-powershell.md)을 참조하세요.

## <a name="common-errors-when-working-with-runbooks"></a>Runbook을 사용할 때 발생하는 일반적인 오류

### <a name="child-runbook-object"></a>출력 스트림에 단순 데이터 형식이 아닌 개체가 포함되어 있으면 자식 Runbook에서 오류를 반환합니다.

#### <a name="issue"></a>문제

사용 하 여 자식 runbook을 호출할 때 다음 오류가 표시 된 `-Wait` 스위치 및 출력 스트림을 포함 하 고 개체:

```error
Object reference not set to an instance of an object
```

#### <a name="cause"></a>원인

[Start-AzureRmAutomationRunbook](/powershell/module/AzureRM.Automation/Start-AzureRmAutomationRunbook)에 개체가 포함되어 있으면 출력 스트림을 올바르게 처리하지 못하는 알려진 문제가 있습니다.

#### <a name="resolution"></a>해결 방법

이 문제를 해결하려면 폴링 논리를 대신 구현하고 [Get-AzureRmAutomationJobOutput](/powershell/module/azurerm.automation/get-azurermautomationjoboutput) cmdlet을 사용하여 출력을 검색하는 것이 좋습니다. 이 논리의 샘플은 다음 예에서 정의됩니다.

```powershell
$automationAccountName = "ContosoAutomationAccount"
$runbookName = "ChildRunbookExample"
$resourceGroupName = "ContosoRG"

function IsJobTerminalState([string] $status) {
    return $status -eq "Completed" -or $status -eq "Failed" -or $status -eq "Stopped" -or $status -eq "Suspended"
}

$job = Start-AzureRmAutomationRunbook -AutomationAccountName $automationAccountName -Name $runbookName -ResourceGroupName $resourceGroupName
$pollingSeconds = 5
$maxTimeout = 10800
$waitTime = 0
while((IsJobTerminalState $job.Status) -eq $false -and $waitTime -lt $maxTimeout) {
   Start-Sleep -Seconds $pollingSeconds
   $waitTime += $pollingSeconds
   $job = $job | Get-AzureRmAutomationJob
}

$jobResults | Get-AzureRmAutomationJobOutput | Get-AzureRmAutomationJobOutputRecord | Select-Object -ExpandProperty Value
```

### <a name="get-serializationsettings"></a>시나리오: get_SerializationSettings 메서드에 대한 작업 스트림에 오류가 표시됨

#### <a name="issue"></a>문제

다음 메시지와 함께 Runbook에 대한 작업 스트림에 오류가 표시됨

```error
Connect-AzureRMAccount : Method 'get_SerializationSettings' in type 
'Microsoft.Azure.Management.Internal.Resources.ResourceManagementClient' from assembly 
'Microsoft.Azure.Commands.ResourceManager.Common, Version=4.0.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35' 
does not have an implementation.
At line:16 char:1
+ Connect-AzureRMAccount -ServicePrincipal -Tenant $Conn.TenantID -Appl ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : NotSpecified: (:) [Connect-AzureRmAccount], TypeLoadException
    + FullyQualifiedErrorId : System.TypeLoadException,Microsoft.Azure.Commands.Profile.ConnectAzureRmAccountCommand
```

#### <a name="cause"></a>원인

이 오류의 원인은 Runbook에서 AzureRM 및 Az cmdlet을 모두 사용했기 때문입니다. `AzureRM`을 가져오기 전에 `Az`를 가져오면 발생합니다.

#### <a name="resolution"></a>해결 방법

Az 및 AzureRM cmdlet은 동일한 Runbook에 가져와 사용할 수 없습니다. Azure Automation에서 Az 지원에 대한 자세한 내용을 보려면 [Azure Automation에서 Az 모듈 지원](../az-modules.md)을 참조하세요.

### <a name="task-was-cancelled"></a>시나리오: 다음 오류로 인해 Runbook 실패: 작업이 취소됨

#### <a name="issue"></a>문제

다음 예제와 비슷한 오류가 발생하여 Runbook이 실패합니다.

```error
Exception: A task was canceled.
```

#### <a name="cause"></a>원인

이 오류는 오래된 Azure 모듈을 사용하여 발생할 수 있습니다.

#### <a name="resolution"></a>해결 방법

이 오류는 Azure 모듈을 최신 버전으로 업데이트하여 해결할 수 있습니다.

Automation 계정에서 **모듈**을 클릭하고 **Azure 모듈 업데이트**를 클릭합니다. 업데이트하는 데 약 15분 정도 걸리며, 완료되면 실패한 Runbook을 다시 실행합니다. 모듈 업데이트에 대해 자세히 알아 보려면 [Azure Automation에서 Azure 모듈 업데이트](../automation-update-azure-modules.md)를 참조하세요.

### <a name="runbook-auth-failure"></a>시나리오: 여러 구독으로 처리하는 경우 Runbook 실패

#### <a name="issue"></a>문제

`Start-AzureRmAutomationRunbook`으로 Runbook을 실행하는 경우 Runbook이 Azure 리소스를 관리하지 못합니다.

#### <a name="cause"></a>원인

Runbook이 실행 시 올바른 컨텍스트를 사용하지 않습니다.

#### <a name="resolution"></a>해결 방법

여러 구독으로 작업할 때 Runbook 호출 시 구독 컨텍스트가 손실될 수 있습니다. 구독 컨텍스트가 Runbook에 전달되도록 하려면 cmdlet에 `AzureRmContext` 매개 변수를 추가하여 컨텍스트를 전달합니다. 사용 중인 자격 증명이 현재 Runbook에 대해서만 사용되도록 하려면 **프로세스** 범위와 함께 `Disable-AzureRmContextAutosave` cmdlet을 사용하는 것이 좋습니다.

```azurepowershell-interactive
# Ensures that any credentials apply only to the execution of this runbook
Disable-AzureRmContextAutosave –Scope Process

# Connect to Azure with RunAs account
$ServicePrincipalConnection = Get-AutomationConnection -Name 'AzureRunAsConnection'

Add-AzureRmAccount `
    -ServicePrincipal `
    -TenantId $ServicePrincipalConnection.TenantId `
    -ApplicationId $ServicePrincipalConnection.ApplicationId `
    -CertificateThumbprint $ServicePrincipalConnection.CertificateThumbprint

$AzureContext = Select-AzureRmSubscription -SubscriptionId $ServicePrincipalConnection.SubscriptionID

$params = @{"VMName"="MyVM";"RepeatCount"=2;"Restart"=$true}

Start-AzureRmAutomationRunbook `
    –AutomationAccountName 'MyAutomationAccount' `
    –Name 'Test-ChildRunbook' `
    -ResourceGroupName 'LabRG' `
    -AzureRmContext $AzureContext `
    –Parameters $params –wait
```

### <a name="not-recognized-as-cmdlet"></a>시나리오: 누락된 cmdlet으로 인해 Runbook 실패

#### <a name="issue"></a>문제

다음 예제와 비슷한 오류가 발생하여 Runbook이 실패합니다.

```error
The term 'Connect-AzureRmAccount' is not recognized as the name of a cmdlet, function, script file, or operable program.  Check the spelling of the name, or if the path was included verify that the path is correct and try again.
```

#### <a name="cause"></a>원인

다음 이유 중 하나로 인해 이 오류가 발생할 수 있습니다.

1. cmdlet이 포함된 모듈을 자동화 계정으로 가져오지 못했습니다.
2. cmdlet이 포함된 모듈을 가져왔지만 만료되었습니다.

#### <a name="resolution"></a>해결 방법

다음 작업 중 하나를 완료하여 이 오류를 해결할 수 있습니다.

모듈이 Azure 모듈인 경우 [Azure Automation에서 Azure PowerShell 모듈을 업데이트하는 방법](../automation-update-azure-modules.md)을 참조하여 자동화 계정에서 모듈을 업데이트하는 방법을 알아봅니다.

별도의 모듈인 경우 Automation 계정에 모듈을 가져와야 합니다.

### <a name="job-attempted-3-times"></a>시나리오: Runbook 작업 시작을 세 번 시도했지만 매번 시작하지 못했음

#### <a name="issue"></a>문제

다음 오류로 인해 Runbook이 실패합니다.

```error
The job was tried three times but it failed
```

#### <a name="cause"></a>원인

다음 문제 중 하나로 인해 이 오류가 발생합니다.

1. 메모리 제한. 샌드박스에 대한 메모리 할당량의 문서화된 제한은 [Automation 서비스 제한](../../azure-subscription-service-limits.md#automation-limits)에서 확인할 수 있습니다. 400MB를 초과하는 메모리를 사용하는 경우 작업에 실패할 수 있습니다.

2. 네트워크 소켓입니다. [Automation 서비스 제한](../../azure-subscription-service-limits.md#automation-limits)에 설명된 대로 Azure 샌드박스에서는 동시 네트워크 소켓이 1000개로 제한됩니다.

3. 모듈이 호환되지 않음. 모듈 종속성이 올바르지 않은 경우에 이 오류가 발생할 수 있습니다. 일반적으로 Runbook에서 "명령을 찾을 수 없습니다." 또는 "매개 변수를 바인딩할 수 없습니다."라는 메시지를 반환합니다.

4. Runbook 호출 실행 파일 또는 샌드박스를 Azure에서 실행 되는 runbook에서 subprocess 하려고 시도 합니다. 이 시나리오는 Azure 샌드박스에서 지원되지 않습니다.

5. Runbook 출력 스트림에 너무 많은 예외 데이터를 쓰려고 했습니다.

#### <a name="resolution"></a>해결 방법

다음 해결 방법 중 하나를 사용하여 문제를 해결합니다.

* 메모리 제한 내에서 작업하기 위해 제안된 방법은 여러 Runbook 간에 워크로드를 분할하고, 메모리에서 많은 데이터를 처리하지 않고, Runbook에서 불필요한 출력을 작성하지 않으며, PowerShell 워크플로 Runbook에 작성하는 검사점의 수를 고려하는 것입니다. `$myVar.clear()`와 같은 clear 메서드를 사용하여 변수를 지우고, `[GC]::Collect()`를 사용하여 가비지 수집을 즉시 실행할 수 있습니다. 이러한 작업은 런타임 중에 Runbook의 메모리 공간을 줄입니다.

* [Azure Automation에서 Azure PowerShell 모듈을 업데이트하는 방법](../automation-update-azure-modules.md) 단계에 따라 Azure 모듈을 업데이트합니다.  

* 다른 솔루션은 [Hybrid Runbook Worker](../automation-hrw-run-runbooks.md)에서 Runbook을 실행하는 것입니다. Hybrid Worker는 Azure 샌드박스의 메모리 및 네트워크 제한 사항에 따라 제한되지 않습니다.

* Runbook에서 프로세스(예: .exe 또는 subprocess.call)를 호출해야 하는 경우 [Hybrid Runbook Worker](../automation-hrw-run-runbooks.md)에서 Runbook을 실행해야 합니다.

* 작업 출력 스트림 1MB 제한이 있습니다. Try/catch 블록에서 실행 파일 또는 하위 프로세스에 대 한 호출을 묶습니다 있는지 확인 합니다. 예외를 throw 하는 Automation 변수를 해당 예외에서 메시지를 작성 합니다. 이에서 방해 하는 작업 출력 스트림에 기록 합니다.

### <a name="fails-deserialized-object"></a>시나리오: deserialize된 개체로 인해 Runbook 실패

#### <a name="issue"></a>문제

다음 오류로 인해 Runbook이 실패합니다.

```error
Cannot bind parameter <ParameterName>.

Cannot convert the <ParameterType> value of type Deserialized <ParameterType> to type <ParameterType>.
```

#### <a name="cause"></a>원인

Runbook이 PowerShell 워크플로인 경우, 워크플로가 일시 중단되더라도 Runbook 상태를 유지하기 위해 복합 개체를 역직렬화된 형식으로 저장합니다.

#### <a name="resolution"></a>해결 방법

다음 세 가지 해결 방법 중 하나로 이 문제를 해결할 수 있습니다.

1. 한 cmdlet에서 다른 cmdlet으로 복합 개체를 파이핑하는 경우 이러한 cmdlet을 InlineScript에 래핑합니다.
2. 개체 전체를 전달하지 말고 복합 개체에서 필요한 이름 또는 값만 전달합니다.
3. PowerShell 워크플로 Runbook 대신 PowerShell Runbook을 사용합니다.

### <a name="runbook-fails"></a>시나리오: 내 Runbook이 실패하지만 로컬에서 실행되면 작동함

#### <a name="issue"></a>문제

스크립트가 Runbook으로 실행되면 실패하지만 로컬에서 실행되면 작동합니다.

#### <a name="cause"></a>원인

스크립트가 Runbook으로 실행되면 다음 이유 중 하나로 인해 실패할 수 있습니다.

1. 인증 문제
2. 필요한 모듈을 가져오지 않았거나 만료되었습니다.
3. 스크립트에서 사용자 상호 작용을 요청하는 메시지를 표시할 수 있습니다.
4. 일부 모듈에서 Windows 컴퓨터에 있는 라이브러리에 대해 가정합니다. 이러한 라이브러리는 샌드박스에 없을 수 있습니다.
5. 일부 모듈은 샌드박스에서 사용할 수 있는 버전과 다른 .NET 버전을 사용합니다.

#### <a name="resolution"></a>해결 방법

이 문제는 다음 해결 방법 중 하나를 사용하여 해결할 수 있습니다.

1. [Azure에서 제대로 인증](../manage-runas-account.md)되고 있는지 확인합니다.
2. [Azure 모듈을 가져와서 최신 상태로 유지](../automation-update-azure-modules.md)합니다.
3. 정보를 요구하는 cmdlet이 없는지 확인합니다. 이 동작은 Runbook에서 지원되지 않습니다.
4. 모듈에 포함되지 않은 항목에 대한 종속성이 모듈에 속한 항목에 있는지 확인합니다.
5. Azure 샌드박스는 .NET Framework 4.7.2를 사용하며, 모듈에서 더 높은 버전을 사용하는 경우 작동하지 않습니다. 이 경우 [Hybrid Runbook Worker](../automation-hybrid-runbook-worker.md)를 사용해야 합니다.

이러한 솔루션 모두에서 문제를 해결할 수 없는 경우 [작업 로그](../automation-runbook-execution.md#viewing-job-status-from-the-azure-portal)에서 특정 세부 정보를 검토하여 Runbook이 실패한 이유를 확인합니다.

### <a name="quota-exceeded"></a>시나리오: 할당된 할당량을 초과하여 Runbook 실패

#### <a name="issue"></a>문제

다음 오류로 인해 Runbook 작업이 실패합니다.

```error
The quota for the monthly total job run time has been reached for this subscription
```

#### <a name="cause"></a>원인

작업 실행 시간이 계정에 할당된 무료 500분을 초과하면 이 오류가 발생합니다. 이 할당량은 모든 유형의 작업 실행 태스크에 적용됩니다. 이러한 태스크 중 일부는 작업을 테스트하거나, 포털에서 작업을 시작하거나, 웹후크를 사용하여 작업을 실행하거나, Azure Portal 또는 데이터 센터를 사용하여 실행할 작업을 예약할 수 있습니다. Automation 가격 책정에 대한 자세한 내용은 [Automation 가격 책정](https://azure.microsoft.com/pricing/details/automation/)을 참조하세요.

#### <a name="resolution"></a>해결 방법

매월 처리 시간을 500분 이상 사용하려면 구독을 무료 계층에는 기본 계층으로 변경해야 합니다. 다음 단계에 따라 기본 계층으로 업그레이드할 수 있습니다.  

1. Azure 구독에 로그인합니다.  
2. 업그레이드할 Automation 계정을 선택합니다.  
3. **설정** > **가격 책정**을 차례로 클릭합니다.
4. 페이지 아래쪽의 **사용**을 클릭하여 계정을 **기본** 계층으로 업그레이드합니다.

### <a name="cmdlet-not-recognized"></a>시나리오: Runbook을 실행해도 cmdlet이 인식되지 않음

#### <a name="issue"></a>문제

다음 오류로 인해 Runbook 작업이 실패합니다.

```error
<cmdlet name>: The term <cmdlet name> is not recognized as the name of a cmdlet, function, script file, or operable program.
```

#### <a name="cause"></a>원인

PowerShell 엔진을 통해 Runbook에서 사용하는 cmdlet을 찾을 수 없는 경우에 이 오류가 발생합니다. 이 오류는 cmdlet이 포함된 모듈이 계정에서 누락되었거나, Runbook 이름과 충돌하는 이름이 있거나, cmdlet이 다른 모듈에도 있고 Automation에서 이름을 확인할 수 없기 때문일 수 있습니다.

#### <a name="resolution"></a>해결 방법

다음 해결 방법 중 하나를 사용하여 문제를 해결합니다.  

* cmdlet 이름을 올바르게 입력했는지 확인합니다.  
* Automation 계정에 Cmdlet이 있고 충돌이 없는지 확인합니다. cmdlet이 있는지 확인하려면 Runbook을 편집 모드로 열고 라이브러리에서 원하는 cmdlet을 검색하거나 `Get-Command <CommandName>`를 실행합니다. 계정에서 cmdlet을 사용할 수 있는지와 다른 cmdlet 또는 Runbook과 이름이 충돌하지 않는지를 확인한 후, 캔버스에 해당 cmdlet을 추가하고 Runbook에 유효한 매개 변수 집합을 사용하고 있는지 확인합니다.  
* 이름이 충돌하고 서로 다른 두 모듈에서 cmdlet을 사용할 수 있는 경우 이 문제는 cmdlet에 대한 정규화된 이름을 사용하여 해결할 수 있습니다. 예를 들어 **ModuleName\CmdletName**을 사용할 수 있습니다.  
* 온-프레미스의 Hybrid Worker 그룹에서 Runbook을 실행하는 경우 Hybrid Worker를 호스트하는 머신에 모듈 및 cmdlet이 설치되어 있는지 확인합니다.

### <a name="long-running-runbook"></a>시나리오: 장기 실행 Runbook이 완료되지 않음

#### <a name="issue"></a>문제

3시간 동안 실행된 후에 Runbook이 **중지됨** 상태로 표시됩니다. 또한 다음과 같은 오류가 표시될 수도 있습니다.

```error
The job was evicted and subsequently reached a Stopped state. The job cannot continue running
```

Azure Automation 내에서 프로세스를 "공평 분배" 방식으로 모니터링하므로 이 동작이 Azure 샌드박스에 설계되었습니다. 3시간을 초과하여 실행되면 공평 분배에서 Runbook을 자동으로 중지합니다. 공평 분배 시간 제한을 초과하는 Runbook의 상태는 Runbook 유형에 따라 다릅니다. PowerShell 및 Python Runbook은 **중지됨** 상태로 설정되며, PowerShell 워크플로 Runbook은 **실패** 상태로 설정됩니다.

#### <a name="cause"></a>원인

Runbook이 Azure 샌드박스의 공평 분배에 허용된 3시간 제한을 초과하여 실행되었습니다.

#### <a name="resolution"></a>해결 방법

권장되는 해결 방법 중 하나는 [Hybrid Runbook Worker](../automation-hrw-run-runbooks.md)에서 Runbook을 실행하는 것입니다.

Hybrid Worker는 Azure 샌드박스의 [공평 분배](../automation-runbook-execution.md#fair-share)에 허용된 3시간 Runbook 제한 사항에 따라 제한되지 않습니다. 예기치 않은 로컬 인프라 문제가 있는 경우 Hybrid Runbook Worker에서 실행된 Runbook은 다시 시작 동작을 지원하도록 개발되어야 합니다.

또 다른 옵션은 [자식 Runbook](../automation-child-runbooks.md)을 만들어 Runbook을 최적화하는 것입니다. Runbook이 여러 데이터베이스의 데이터베이스 작업과 같이 여러 리소스에서 동일한 함수를 반복하는 경우 해당 함수를 자식 Runbook으로 이동할 수 있습니다. 이러한 자식 Runbook은 각각 개별 프로세스에서 병렬로 실행됩니다. 이 동작은 부모 Runbook이 완료되는 총 기간을 감소시킵니다.

자식 Runbook 시나리오를 사용하도록 설정하는 PowerShell cmdlet은 다음과 같습니다.

[Start-AzureRMAutomationRunbook](/powershell/module/AzureRM.Automation/Start-AzureRmAutomationRunbook) - 이 cmdlet을 사용하면 Runbook을 시작하고 매개 변수를 Runbook에 전달할 수 있습니다.

[Get-AzureRmAutomationJob](/powershell/module/azurerm.automation/get-azurermautomationjob) - 자식 Runbook이 완료된 후에 수행해야 하는 작업이 있는 경우 이 cmdlet을 사용하면 각 자식에 대한 작업 상태를 확인할 수 있습니다.

### <a name="expired webhook"></a>시나리오: 상태: 400 웹후크를 호출할 때 요청이 잘못되었음

#### <a name="issue"></a>문제

Azure Automation Runbook에 대한 웹후크를 호출하려고 시도하면 다음 오류 메시지가 나타납니다.

```error
400 Bad Request : This webhook has expired or is disabled
```

#### <a name="cause"></a>원인

호출하려는 웹후크가 사용되지 않거나 만료되었습니다.

#### <a name="resolution"></a>해결 방법

webhook가 비활성화된 경우Azure Portal을 통해 webhook를 다시 활성화할 수 있습니다. webhook가 만료되면 webhook를 삭제하고 다시 만들어야 합니다. 아직 만료되지 않은 경우에만 [웹후크를 갱신](../automation-webhooks.md#renew-webhook)할 수 있습니다.

### <a name="429"></a>시나리오: 429: 현재 요청 속도가 너무 큽니다. 다시 해 보세요.

#### <a name="issue"></a>문제

`Get-AzureRmAutomationJobOutput` cmdlet을 실행할 때 다음 오류 메시지가 나타납니다.

```error
429: The request rate is currently too large. Please try again
```

#### <a name="cause"></a>원인

[자세한 정보 표시 스트림](../automation-runbook-output-and-messages.md#verbose-stream)이 많은 Runbook에서 작업 출력을 검색할 때 이 오류가 발생할 수 있습니다.

#### <a name="resolution"></a>해결 방법

이 오류를 해결하는 방법은 두 가지가 있습니다.

* Runbook을 편집하고 내보내는 작업 스트림 수를 줄입니다.
* cmdlet을 실행할 때 검색할 스트림 수를 줄입니다. 이 동작을 수행하려면 `Get-AzureRmAutomationJobOutput` cmdlet에 대해 `-Stream Output` 매개 변수를 지정하여 출력 스트림만 검색할 수 있습니다. 

### <a name="cannot-invoke-method"></a>시나리오: PowerShell 작업 오류로 인해 실패 합니다. 메서드를 호출할 수 없습니다.

#### <a name="issue"></a>문제

Azure에서 실행 되는 runbook에서 PowerShell 작업을 시작할 때 다음 오류 메시지가 나타날 있습니다.

```error
Exception was thrown - Cannot invoke method. Method invocation is supported only on core types in this language mode.
```

#### <a name="cause"></a>원인

Runbook의 작업이 Azure에서 실행 하는 PowerShell을 시작할 때이 오류가 발생할 수 있습니다. Azure에서 runbook 실행 때문에이 동작이 발생할 수 있습니다 샌드박스 실행 되지 않을 수는 [전체 언어 모드](/powershell/module/microsoft.powershell.core/about/about_language_modes)).

#### <a name="resolution"></a>해결 방법

이 오류를 해결하는 방법은 두 가지가 있습니다.

* 사용 하는 대신 `Start-Job`를 사용 하 여 `Start-AzureRmAutomationRunbook` runbook을 시작 하려면
* Runbook이 오류 메시지에 있는 경우 Hybrid Runbook Worker에서 실행

Azure Automation Runbook의 다른 동작과이 동작에 대 한 자세한 내용은 참조 하세요 [Runbook 동작](../automation-runbook-execution.md#runbook-behavior)합니다.

## <a name="next-steps"></a>다음 단계

문제가 표시되지 않거나 문제를 해결할 수 없는 경우 다음 채널 중 하나를 방문하여 추가 지원을 받으세요.

* [Azure 포럼](https://azure.microsoft.com/support/forums/)을 통해 Azure 전문가로부터 답변을 얻습니다.
* [@AzureSupport](https://twitter.com/azuresupport)를 사용하여 연결 – Azure 커뮤니티를 적절한 리소스(답변, 지원 및 전문가)에 연결하여 고객 환경을 개선하는 공식 Microsoft Azure 계정입니다.
* 추가 지원이 필요한 경우, Azure 기술 지원 인시던트를 제출할 수 있습니다. [Azure 지원 사이트](https://azure.microsoft.com/support/options/) 로 가서 **지원 받기**를 선택합니다.
