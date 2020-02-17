---
title: Azure Automation Runbook을 사용하여 오류 해결
description: Runbook Azure Automation에서 발생할 수 있는 문제를 해결 하 고 해결 하는 방법을 알아봅니다.
services: automation
author: mgoedtel
ms.author: magoedte
ms.date: 01/24/2019
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: 571be831d337c71a084780da18b480cdd1e42d20
ms.sourcegitcommit: f97f086936f2c53f439e12ccace066fca53e8dc3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/15/2020
ms.locfileid: "77365217"
---
# <a name="troubleshoot-errors-with-runbooks"></a>Runbook으로 오류 해결

Azure Automation에서 runbook을 실행 하는 동안 오류가 발생 하는 경우 다음 단계를 사용 하 여 문제를 진단할 수 있습니다.

1. **로컬 컴퓨터에서 runbook 스크립트가 성공적으로 실행 되는지 확인 합니다.**  언어 참조 및 학습 모듈은 [PowerShell 문서](/powershell/scripting/overview) 또는 [Python 문서](https://docs.python.org/3/) 를 참조 하세요.

   스크립트를 로컬로 실행 하면 다음과 같은 일반적인 오류를 검색 하 고 해결할 수 있습니다.

   - **누락 된 모듈**
   - **구문 오류**
   - **논리 오류**

2. 특정 메시지에 대 한 **runbook** [오류 스트림을](https://docs.microsoft.com/azure/automation/automation-runbook-output-and-messages#runbook-output) 조사 하 고 아래 오류와 비교 합니다.

3. **노드 및 자동화 작업 영역에 필요한 모듈이 있는지 확인 합니다.** Runbook이 모듈을 가져오는 경우 모듈 [가져오기](../shared-resources/modules.md#import-modules)에 나열 된 단계를 사용 하 여 Automation 계정에 사용할 수 있는지 확인 합니다. [Azure Automation에서 Azure 모듈 업데이트의](..//automation-update-azure-modules.md)지침에 따라 모듈을 최신 버전으로 업데이트 합니다. 문제 해결에 대 한 자세한 내용은 [모듈 문제 해결](shared-resources.md#modules)을 참조 하세요.

Runbook이 일시 중단 되었거나 예기치 않게 실패 한 경우:

* [작업 상태 확인](https://docs.microsoft.com/azure/automation/automation-runbook-execution#job-statuses) 은 runbook 상태와 몇 가지 가능한 원인을 정의 합니다.
* Runbook에 [추가 출력을 추가](https://docs.microsoft.com/azure/automation/automation-runbook-output-and-messages#message-streams) 하 여 runbook이 일시 중단 되기 전에 수행 되는 작업을 식별 합니다.
* 작업에서 throw 되는 [모든 예외를 처리](https://docs.microsoft.com/azure/automation/automation-runbook-execution#handling-exceptions) 합니다.

## <a name="login-azurerm"></a>시나리오: 로그인을 실행 하 여 로그인 Connect-azurermaccount

### <a name="issue"></a>문제

Runbook을 실행할 때 다음과 같은 오류가 표시 됩니다.

```error
Run Login-AzureRMAccount to login.
```

### <a name="cause"></a>원인

실행 계정을 사용 하지 않거나 실행 계정이 만료 된 경우이 오류가 발생할 수 있습니다. [Azure Automation 실행 계정 관리](https://docs.microsoft.com/azure/automation/manage-runas-account)를 참조 하세요.

이 오류에는 두 가지 주요 원인이 있습니다.

* AzureRM 모듈의 여러 버전
* 별도의 구독에 있는 리소스에 액세스 하려고 합니다.

### <a name="resolution"></a>해결 방법

AzureRM 모듈 하나를 업데이트 한 후이 오류가 표시 되 면 모든 AzureRM 모듈을 같은 버전으로 업데이트 해야 합니다.

다른 구독에 있는 리소스에 액세스 하려는 경우 아래 단계에 따라 사용 권한을 구성할 수 있습니다.

1. Automation 실행 계정으로 이동 하 여 응용 프로그램 ID 및 지문을 복사 합니다.
  응용 프로그램 ID 및 지문 ![복사](../media/troubleshoot-runbooks/collect-app-id.png)
1. Automation 계정이 호스팅되지 않은 구독의 Access Control로 이동 하 고 새 역할 할당을 추가 합니다.
  ![액세스 제어](../media/troubleshoot-runbooks/access-control.png)
1. 이전 단계에서 수집한 응용 프로그램 ID를 추가 합니다. 참가자 권한을 선택 합니다.
   역할 할당 추가 ![](../media/troubleshoot-runbooks/add-role-assignment.png)
1. 다음 단계에 대 한 구독 이름을 복사 합니다.
1. 이제 다음 runbook 코드를 사용 하 여 Automation 계정의 사용 권한을 다른 구독으로 테스트할 수 있습니다.

    "\<CertificateThumbprint\>"를 #1 단계에서 복사한 값과\>단계에서 복사한 "\<SubscriptionName #4" 값으로 바꿉니다.

    ```powershell
    $Conn = Get-AutomationConnection -Name AzureRunAsConnection
    Connect-AzureRmAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint "<CertificateThumbprint>"
    #Select the subscription you want to work with
    Select-AzureRmSubscription -SubscriptionName '<YourSubscriptionNameGoesHere>'

    #Test and get outputs of the subscriptions you granted access.
    $subscriptions = Get-AzureRmSubscription
    foreach($subscription in $subscriptions)
    {
        Set-AzureRmContext $subscription
        Write-Output $subscription.Name
    }
    ```

## <a name="unable-to-find-subscription"></a>시나리오: Azure 구독을 찾을 수 없음

### <a name="issue"></a>문제

`Select-AzureSubscription` 또는 `Select-AzureRmSubscription` cmdlet으로 작업하면 다음 오류가 발생합니다.

```error
The subscription named <subscription name> cannot be found.
```

### <a name="error"></a>Error

이 오류가 발생할 수 있는 경우는 다음과 같습니다.

* 구독 이름이 올바르지 않습니다.

* 구독 세부 정보를 가져오려고 하는 Azure Active Directory 사용자가 구독 관리자로 구성되지 않았습니다.

### <a name="resolution"></a>해결 방법

다음 단계에 따라 Azure에서 인증되고 선택하려고 하는 구독에 대한 액세스 권한이 있는지 확인합니다.

1. 독립 실행형으로 작동하는지 확인하기 위해 Azure Automation 외부에서 스크립트를 테스트합니다.
2. `Add-AzureAccount` cmdlet을 실행하기 전에 `Select-AzureSubscription` cmdlet을 실행하고 있는지 확인합니다.
3. Runbook의 시작 부분에 `Disable-AzureRmContextAutosave –Scope Process`를 추가합니다. 이 cmdlet은 모든 자격 증명이 현재 Runbook의 실행에만 적용되도록 합니다.
4. 이 오류 메시지가 계속 나타나면 **cmdlet 뒤에**AzureRmContext`Add-AzureAccount` 매개 변수를 추가하여 코드를 수정한 다음, 해당 코드를 실행합니다.

   ```powershell
   Disable-AzureRmContextAutosave –Scope Process

   $Conn = Get-AutomationConnection -Name AzureRunAsConnection
   Connect-AzureRmAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationID $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint

   $context = Get-AzureRmContext

   Get-AzureRmVM -ResourceGroupName myResourceGroup -AzureRmContext $context
    ```

## <a name="auth-failed-mfa"></a>시나리오: Multi-Factor Authentication이 활성화되어 Azure 인증에 실패

### <a name="issue"></a>문제

Azure 사용자 이름 및 암호를 사용하여 Azure에 인증할 때 다음 오류가 발생합니다.

```error
Add-AzureAccount: AADSTS50079: Strong authentication enrollment (proof-up) is required
```

### <a name="cause"></a>원인

Azure 계정에서 Multi-Factor Authentication을 사용하면 Azure Active Directory 사용자를 사용하여 Azure에 인증할 수 없습니다. 그 대신 인증서 또는 서비스 주체를 사용하여 Azure에 인증해야 합니다.

### <a name="resolution"></a>해결 방법

Azure 클래식 배포 모델 cmdlet에 인증서를 사용 하려면 [azure 서비스를 관리 하는 인증서 만들기 및 추가](https://blogs.technet.com/b/orchestrator/archive/2014/04/11/managing-azure-services-with-the-microsoft-azure-automation-preview-service.aspx)를 참조 하세요. Azure Resource Manager cmdlet에서 서비스 주체를 사용 하려면 [Azure Portal를 사용 하 여 서비스 주체 만들기](../../active-directory/develop/howto-create-service-principal-portal.md) 및 [Azure Resource Manager](../../active-directory/develop/howto-authenticate-service-principal-powershell.md)를 사용 하 여 서비스 주체 인증을 참조 하세요.

## <a name="get-serializationsettings"></a>시나리오: get_SerializationSettings 방법에 대 한 작업 스트림에 오류가 표시 됩니다.

### <a name="issue"></a>문제

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

### <a name="cause"></a>원인

이 오류의 원인은 Runbook에서 AzureRM 및 Az cmdlet을 모두 사용했기 때문입니다. `Az`을 가져오기 전에 `AzureRM`를 가져오면 발생합니다.

### <a name="resolution"></a>해결 방법

Az 및 AzureRM cmdlet은 동일한 runbook에서 가져와서 사용할 수 없습니다. Azure Automation의 Az cmdlet에 대 한 자세한 내용은 [Azure Automation의 az module support](../az-modules.md)을 참조 하십시오.

## <a name="task-was-cancelled"></a>시나리오: 다음 오류로 인해 Runbook이 실패합니다. 작업이 취소됨

### <a name="issue"></a>문제

다음 예제와 비슷한 오류가 발생하여 Runbook이 실패합니다.

```error
Exception: A task was canceled.
```

### <a name="cause"></a>원인

이 오류는 오래된 Azure 모듈을 사용하여 발생할 수 있습니다.

### <a name="resolution"></a>해결 방법

이 오류는 Azure 모듈을 최신 버전으로 업데이트하여 해결할 수 있습니다.

Automation 계정에서 **모듈**을 클릭 하 고 **Azure 모듈 업데이트**를 클릭 합니다. 업데이트하는 데 약 15분 정도 걸리며, 완료되면 실패한 Runbook을 다시 실행합니다. 모듈 업데이트에 대해 자세히 알아 보려면 [Azure Automation에서 Azure 모듈 업데이트](../automation-update-azure-modules.md)를 참조하세요.

## <a name="runbook-auth-failure"></a>시나리오: 여러 구독을 처리할 때 Runbook이 실패 함

### <a name="issue"></a>문제

Runbook을 실행할 때 runbook은 Azure 리소스를 관리 하지 못합니다.

### <a name="cause"></a>원인

Runbook이 실행 시 올바른 컨텍스트를 사용하지 않습니다.

### <a name="resolution"></a>해결 방법

여러 구독으로 작업할 때 Runbook 호출 시 구독 컨텍스트가 손실될 수 있습니다. 구독 컨텍스트가 Runbook에 전달되도록 하려면 cmdlet에 `AzureRmContext` 매개 변수를 추가하여 컨텍스트를 전달합니다. 사용 중인 자격 증명이 현재 Runbook에 대해서만 사용되도록 하려면 `Disable-AzureRmContextAutosave`프로세스**범위와 함께** cmdlet을 사용하는 것이 좋습니다.

```azurepowershell-interactive
# Ensures that any credentials apply only to the execution of this runbook
Disable-AzureRmContextAutosave –Scope Process

# Connect to Azure with Run As account
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

자세한 내용은 [여러 구독 작업](../automation-runbook-execution.md#working-with-multiple-subscriptions)을 참조 하세요.

## <a name="not-recognized-as-cmdlet"></a>시나리오: 용어가 cmdlet, 함수, 스크립트의 이름으로 인식 되지 않습니다.

### <a name="issue"></a>문제

다음 예제와 비슷한 오류가 발생하여 Runbook이 실패합니다.

```error
The term 'Connect-AzureRmAccount' is not recognized as the name of a cmdlet, function, script file, or operable program.  Check the spelling of the name, or if the path was included verify that the path is correct and try again.
```

### <a name="cause"></a>원인

이 오류는 다음과 같은 이유로 인해 발생할 수 있습니다.

* Cmdlet을 포함 하는 모듈은 Automation 계정으로 가져오지 않습니다.
* Cmdlet을 포함 하는 모듈을 가져오지만 최신 상태가 아닙니다.

### <a name="resolution"></a>해결 방법

다음 작업 중 하나를 완료하여 이 오류를 해결할 수 있습니다.

모듈이 Azure 모듈인 경우 Automation 계정에서 모듈을 업데이트 하는 방법을 알아보려면 [Azure Automation에서 Azure PowerShell 모듈을 업데이트 하는 방법](../automation-update-azure-modules.md) 을 참조 하세요.

별도의 모듈인 경우 Automation 계정에 모듈을 가져왔는지 확인 합니다.

## <a name="job-attempted-3-times"></a>시나리오: runbook 작업 시작을 세 번 시도 했지만 매번 시작 하지 못했습니다.

### <a name="issue"></a>문제

다음 오류가 발생 하 여 runbook이 실패 합니다.

```error
The job was tried three times but it failed
```

### <a name="cause"></a>원인

이 오류는 다음 문제 중 하나로 인해 발생 합니다.

* 메모리 제한. 400 MB를 초과 하는 메모리를 사용 하는 경우 작업이 실패할 수 있습니다. Sandbox에 할당 된 메모리에 대 한 문서화 된 제한은 [Automation 서비스 제한](../../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits)에서 찾을 수 있습니다. 

* 네트워크 소켓입니다. Azure 샌드박스는 1000의 동시 네트워크 소켓으로 제한 됩니다. [Automation 서비스 제한](../../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits)을 참조 하세요.

* 모듈이 호환되지 않음. 모듈 종속성이 올바르지 않을 수 있습니다. 이 경우 runbook은 일반적으로 "명령 찾을 수 없음" 또는 "매개 변수를 바인딩할 수 없습니다." 메시지를 반환 합니다.

* 샌드박스에 대 한 Active Directory 인증 안 함 Runbook이 Azure 샌드박스에서 실행 되는 실행 파일 또는 하위 프로세스를 호출 하려고 했습니다. ADAL (Azure Active Directory 인증 라이브러리)을 사용 하 여 Azure AD에서 인증 하도록 runbook을 구성 하는 것은 지원 되지 않습니다.

* 예외 데이터가 너무 많습니다. Runbook이 출력 스트림에 너무 많은 예외 데이터를 쓰려고 했습니다.

### <a name="resolution"></a>해결 방법

* 메모리 제한, 네트워크 소켓 메모리 제한 내에서 작업을 수행 하는 권장 방법은 여러 runbook 간에 워크 로드를 분할 하 고, 메모리에서 더 작은 데이터를 처리 하 고, runbook에서 불필요 한 출력을 쓰지 않도록 하 고, PowerShell 워크플로에 작성 된 검사점 수를 고려 하는 것입니다. runbook. Clear 메서드 (예: `$myVar.clear`)를 사용 하 여 변수를 지우고 `[GC]::Collect`를 사용 하 여 가비지 수집을 즉시 실행 합니다. 이러한 작업은 런타임 중에 Runbook의 메모리 공간을 줄입니다.

* 모듈이 호환되지 않음. [Azure Automation에서 Azure PowerShell 모듈을 업데이트 하는 방법](../automation-update-azure-modules.md)의 단계에 따라 Azure 모듈을 업데이트 합니다.

* 샌드박스에 대해 ADAL을 사용 하 여 인증 하지 않습니다. Runbook을 사용 하 여 Azure AD에 인증할 때 Automation 계정에서 Azure AD 모듈을 사용할 수 있는지 확인 합니다. Runbook이 자동화 하는 작업을 수행 하는 데 필요한 권한을 실행 계정에 부여 해야 합니다.

  Runbook이 Azure 샌드박스에서 실행 되는 실행 파일 또는 하위 프로세스를 호출할 수 없는 경우 [Hybrid Runbook Worker](../automation-hrw-run-runbooks.md)에서 runbook을 사용 합니다. 하이브리드 작업자는 Azure 샌드박스에 있는 메모리 및 네트워크 제한으로 제한 되지 않습니다.

* 예외 데이터가 너무 많습니다. 작업 출력 스트림에는 1MB 제한이 있습니다. Runbook이 try/catch 블록의 실행 파일 또는 하위 프로세스에 대 한 호출을 포함 하는지 확인 합니다. 작업이 예외를 throw 하는 경우 코드에서 예외의 메시지를 자동화 변수에 쓰도록 합니다. 이 방법은 메시지가 작업 출력 스트림에 기록 되지 않도록 합니다.

## <a name="sign-in-failed"></a>시나리오: Azure 계정에 로그인 하지 못했습니다.

### <a name="issue"></a>문제

`Add-AzureAccount` 또는 `Connect-AzureRmAccount` cmdlet으로 작업할 때 다음 오류 중 하나가 표시 됩니다.

```error
Unknown_user_type: Unknown User Type
```

```error
No certificate was found in the certificate store with thumbprint
```

### <a name="cause"></a>원인

자격 증명 자산 이름이 올바르지 않으면 이 오류가 발생합니다. 또한 Automation 자격 증명 자산을 설정하는 데 사용한 사용자 이름과 암호가 올바르지 않은 경우에도 발생할 수 있습니다.

### <a name="resolution"></a>해결 방법

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
       #Logging in to Azure...
       $connectionResult = Connect-AzureRmAccount `
                              -ServicePrincipal `
                              -TenantId $servicePrincipalConnection.TenantId `
                              -ApplicationId $servicePrincipalConnection.ApplicationId `
                              -CertificateThumbprint $servicePrincipalConnection.CertificateThumbprint

       Start-Sleep -Seconds 30
   }
   ```

## <a name="child-runbook-object"></a>시나리오: 개체 참조가 개체의 인스턴스로 설정 되지 않았습니다.

### <a name="issue"></a>문제

`-Wait` 스위치를 사용 하 여 자식 runbook을 호출 하 고 출력 스트림에 개체를 포함 하는 경우 다음과 같은 오류가 표시 됩니다.

```error
Object reference not set to an instance of an object
```

### <a name="cause"></a>원인

Start-azurermautomationrunbook가 개체를 포함 하는 경우 출력 스트림을 올바르게 처리 하지 않는 알려진 문제가 있습니다.

### <a name="resolution"></a>해결 방법

이 문제를 해결 하려면 폴링 논리를 구현 하 고 [AzureRmAutomationJobOutput](/powershell/module/azurerm.automation/get-azurermautomationjoboutput) cmdlet을 사용 하 여 출력을 검색 하는 것이 좋습니다. 이 논리의 샘플은 다음 예에서 정의됩니다.

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

## <a name="fails-deserialized-object"></a>시나리오: 역직렬화된 개체로 인해 Runbook 실패

### <a name="issue"></a>문제

다음 오류로 인해 Runbook이 실패합니다.

```error
Cannot bind parameter <ParameterName>.

Cannot convert the <ParameterType> value of type Deserialized <ParameterType> to type <ParameterType>.
```

### <a name="cause"></a>원인

Runbook이 PowerShell 워크플로인 경우, 워크플로가 일시 중단되더라도 Runbook 상태를 유지하기 위해 복합 개체를 역직렬화된 형식으로 저장합니다.

### <a name="resolution"></a>해결 방법

다음 세 가지 해결 방법 중 하나로 이 문제를 해결할 수 있습니다.

* 한 cmdlet에서 다른 cmdlet으로 복합 개체를 파이핑하는 경우 이러한 cmdlet을 InlineScript에 래핑합니다.
* 개체 전체를 전달하지 말고 복합 개체에서 필요한 이름 또는 값만 전달합니다.
* PowerShell 워크플로 Runbook 대신 PowerShell Runbook을 사용합니다.

## <a name="quota-exceeded"></a>시나리오: 할당 된 할당량이 초과 되어 Runbook 작업이 실패 함

### <a name="issue"></a>문제

다음 오류로 인해 Runbook 작업이 실패합니다.

```error
The quota for the monthly total job run time has been reached for this subscription
```

### <a name="cause"></a>원인

작업 실행 시간이 계정에 할당된 무료 500분을 초과하면 이 오류가 발생합니다. 이 할당량은 모든 유형의 작업 실행 태스크에 적용됩니다. 이러한 태스크 중 일부는 작업을 테스트하거나, 포털에서 작업을 시작하거나, 웹후크를 사용하여 작업을 실행하거나, Azure Portal 또는 데이터 센터를 사용하여 실행할 작업을 예약할 수 있습니다. Automation 가격 책정에 대한 자세한 내용은 [Automation 가격 책정](https://azure.microsoft.com/pricing/details/automation/)을 참조하세요.

### <a name="resolution"></a>해결 방법

매월 처리 시간을 500분 이상 사용하려면 구독을 무료 계층에는 기본 계층으로 변경해야 합니다. 다음 단계에 따라 기본 계층으로 업그레이드할 수 있습니다.

1. Azure 구독에 로그인합니다.
2. 업그레이드할 Automation 계정을 선택 합니다.
3. **설정**, **가격 책정**을 차례로 클릭 합니다.
4. 페이지 아래쪽의 **사용**을 클릭하여 계정을 **기본** 계층으로 업그레이드합니다.

## <a name="cmdlet-not-recognized"></a>시나리오: Runbook을 실행해도 cmdlet이 인식되지 않음

### <a name="issue"></a>문제

다음 오류로 인해 Runbook 작업이 실패합니다.

```error
<cmdlet name>: The term <cmdlet name> is not recognized as the name of a cmdlet, function, script file, or operable program.
```

### <a name="cause"></a>원인

PowerShell 엔진을 통해 Runbook에서 사용하는 cmdlet을 찾을 수 없는 경우에 이 오류가 발생합니다. 이 오류는 cmdlet이 포함된 모듈이 계정에서 누락되었거나, Runbook 이름과 충돌하는 이름이 있거나, cmdlet이 다른 모듈에도 있고 Automation에서 이름을 확인할 수 없기 때문일 수 있습니다.

### <a name="resolution"></a>해결 방법

다음 해결 방법 중 하나를 사용하여 문제를 해결합니다.

* cmdlet 이름을 올바르게 입력했는지 확인합니다.
* Automation 계정에 Cmdlet이 있고 충돌이 없는지 확인합니다. cmdlet이 있는지 확인하려면 Runbook을 편집 모드로 열고 라이브러리에서 원하는 cmdlet을 검색하거나 `Get-Command <CommandName>`를 실행합니다. 계정에서 cmdlet을 사용할 수 있는지와 다른 cmdlet 또는 Runbook과 이름이 충돌하지 않는지를 확인한 후, 캔버스에 해당 cmdlet을 추가하고 Runbook에 유효한 매개 변수 집합을 사용하고 있는지 확인합니다.
* 이름이 충돌하고 서로 다른 두 모듈에서 cmdlet을 사용할 수 있는 경우 이 문제는 cmdlet에 대한 정규화된 이름을 사용하여 해결할 수 있습니다. 예를 들어 **ModuleName\CmdletName**을 사용할 수 있습니다.
* 온-프레미스의 Hybrid Worker 그룹에서 Runbook을 실행하는 경우 Hybrid Worker를 호스트하는 머신에 모듈 및 cmdlet이 설치되어 있는지 확인합니다.

## <a name="long-running-runbook"></a>시나리오: 장기 실행 runbook이 완료 되지 못함

### <a name="issue"></a>문제

3시간 동안 실행된 후에 Runbook이 **중지됨** 상태로 표시됩니다. 또한 다음과 같은 오류가 표시될 수도 있습니다.

```error
The job was evicted and subsequently reached a Stopped state. The job cannot continue running
```

Azure Automation 내에서 프로세스를 "공평 분배" 방식으로 모니터링하므로 이 동작이 Azure 샌드박스에 설계되었습니다. 3시간을 초과하여 실행되면 공평 분배에서 Runbook을 자동으로 중지합니다. 공평 분배 시간 제한을 초과하는 Runbook의 상태는 Runbook 유형에 따라 다릅니다. PowerShell 및 Python Runbook은 **중지됨** 상태로 설정되며, PowerShell 워크플로 Runbook은 **실패** 상태로 설정됩니다.

### <a name="cause"></a>원인

Runbook이 Azure 샌드박스에서 공평 하 게 공유에서 허용 하는 3 시간 제한을 초과 했습니다.

### <a name="resolution"></a>해결 방법

권장되는 해결 방법 중 하나는 [Hybrid Runbook Worker](../automation-hrw-run-runbooks.md)에서 Runbook을 실행하는 것입니다.

하이브리드 작업자는 Azure 샌드박스에 있는 [공평](../automation-runbook-execution.md#fair-share) 하 게 공유 3 시간 runbook 제한에 의해 제한 되지 않습니다. 예기치 않은 로컬 인프라 문제가 있는 경우 다시 시작 동작을 지원 하도록 Hybrid Runbook Worker에서 실행 되는 runbook을 개발 해야 합니다.

또 다른 옵션은 [자식 Runbook](../automation-child-runbooks.md)을 만들어 Runbook을 최적화하는 것입니다. Runbook이 여러 데이터베이스의 데이터베이스 작업과 같이 여러 리소스에서 동일한 함수를 반복하는 경우 해당 함수를 자식 Runbook으로 이동할 수 있습니다. 이러한 자식 Runbook은 각각 개별 프로세스에서 병렬로 실행됩니다. 이 동작은 부모 Runbook이 완료되는 총 기간을 감소시킵니다.

자식 Runbook 시나리오를 사용하도록 설정하는 PowerShell cmdlet은 다음과 같습니다.

[Start-AzureRMAutomationRunbook](/powershell/module/AzureRM.Automation/Start-AzureRmAutomationRunbook) - 이 cmdlet을 사용하면 Runbook을 시작하고 매개 변수를 Runbook에 전달할 수 있습니다.

[Get-AzureRmAutomationJob](/powershell/module/azurerm.automation/get-azurermautomationjob) - 자식 Runbook이 완료된 후에 수행해야 하는 작업이 있는 경우 이 cmdlet을 사용하면 각 자식에 대한 작업 상태를 확인할 수 있습니다.

## <a name="expired webhook"></a>시나리오: 상태: 400 webhook를 호출할 때 잘못 된 요청

### <a name="issue"></a>문제

Azure Automation Runbook에 대한 웹후크를 호출하려고 시도하면 다음 오류 메시지가 나타납니다.

```error
400 Bad Request : This webhook has expired or is disabled
```

### <a name="cause"></a>원인

호출하려는 웹후크가 사용되지 않거나 만료되었습니다.

### <a name="resolution"></a>해결 방법

webhook가 비활성화된 경우Azure Portal을 통해 webhook를 다시 활성화할 수 있습니다. webhook가 만료되면 webhook를 삭제하고 다시 만들어야 합니다. 아직 만료되지 않은 경우에만 [웹후크를 갱신](../automation-webhooks.md#renew-webhook)할 수 있습니다.

## <a name="429"></a>시나리오: 429: 요청 비율이 현재 너무 큼 ...

### <a name="issue"></a>문제

`Get-AzureRmAutomationJobOutput` cmdlet을 실행할 때 다음 오류 메시지가 나타납니다.

```error
429: The request rate is currently too large. Please try again
```

### <a name="cause"></a>원인

[자세한 정보 표시 스트림](../automation-runbook-output-and-messages.md#verbose-stream)이 많은 Runbook에서 작업 출력을 검색할 때 이 오류가 발생할 수 있습니다.

### <a name="resolution"></a>해결 방법

이 오류를 해결하는 방법은 두 가지가 있습니다.

* Runbook을 편집하고 내보내는 작업 스트림 수를 줄입니다.
* cmdlet을 실행할 때 검색할 스트림 수를 줄입니다. 이 동작을 수행하려면 `-Stream Output` cmdlet에 대해 `Get-AzureRmAutomationJobOutput` 매개 변수를 지정하여 출력 스트림만 검색할 수 있습니다. 

## <a name="cannot-invoke-method"></a>시나리오: PowerShell 작업이 실패 하 고 오류가 발생 함: 메서드를 호출할 수 없습니다.

### <a name="issue"></a>문제

Azure에서 실행 되는 runbook에서 PowerShell 작업을 시작할 때 다음과 같은 오류 메시지가 표시 됩니다.

```error
Exception was thrown - Cannot invoke method. Method invocation is supported only on core types in this language mode.
```

### <a name="cause"></a>원인

이 오류는 Azure에서 실행 되는 runbook에서 PowerShell 작업을 시작할 때 발생할 수 있습니다. 이 동작은 Azure 샌드박스에서 실행 되는 runbook이 [전체 언어 모드](/powershell/module/microsoft.powershell.core/about/about_language_modes)에서 실행 되지 않을 수 있기 때문에 발생할 수 있습니다.

### <a name="resolution"></a>해결 방법

이 오류를 해결하는 방법은 두 가지가 있습니다.

* `Start-Job`를 사용 하는 대신 `Start-AzureRmAutomationRunbook`를 사용 하 여 runbook을 시작 합니다.
* Runbook에이 오류 메시지가 있는 경우 Hybrid Runbook Worker에서 실행 합니다.

이 동작 및 Runbook Azure Automation의 다른 동작에 대해 자세히 알아보려면 [runbook 동작](../automation-runbook-execution.md#runbook-behavior)을 참조 하세요.

## <a name="scenario-linux-hybrid-runbook-worker-receives-a-prompt-for-a-password-when-signing-a-runbook"></a>시나리오: Runbook에 서명할 때 Linux Hybrid Runbook Worker 암호에 대 한 프롬프트를 수신 합니다.

### <a name="issue"></a>문제

Linux Hybrid Runbook Worker에 대해 **sudo** 명령을 실행 하면 암호에 대 한 예기치 않은 프롬프트가 검색 됩니다.

### <a name="cause"></a>원인

Linux 용 Log Analytics 에이전트에 대 한 nxautomationuser 계정이 sudoers 파일에 올바르게 구성 되어 있지 않습니다. Hybrid Runbook Worker은 Linux Runbook Worker에서 runbook에 서명할 수 있도록 계정 권한 및 기타 데이터를 적절 하 게 구성 해야 합니다.

### <a name="resolution"></a>해결 방법

* Hybrid Runbook Worker 컴퓨터에 GnuPG (GPG) 실행 파일이 있는지 확인 합니다.

* Sudoers 파일에서 nxautomationuser 계정의 구성을 확인 합니다. [Hybrid Runbook Worker에서 Runbook 실행을](../automation-hrw-run-runbooks.md) 참조 하세요.

## <a name="scenario-cmdlet-failing-in-pnp-powershell-runbook-on-azure-automation"></a>시나리오: Azure Automation의 PnP PowerShell runbook에서 Cmdlet이 실패 했습니다.

### <a name="issue"></a>문제

Runbook이 PnP PowerShell 생성 개체를 Azure Automation 출력에 직접 기록 하는 경우 cmdlet 출력은 자동화로 다시 스트리밍할 수 없습니다.

### <a name="cause"></a>원인

이 문제는 반환 개체를 catch 하지 않고 **pnplistitem**와 같이 PnP PowerShell cmdlet을 호출 하는 runbook을 Azure Automation 처리할 때 가장 일반적으로 발생 합니다.

### <a name="resolution"></a>해결 방법

Cmdlet이 표준 출력에 전체 개체를 쓰려고 시도 하지 않도록 스크립트를 편집 하 여 모든 반환 값을 변수에 할당 합니다. 스크립트는 아래와 같이 출력 스트림을 cmdlet으로 리디렉션할 수 있습니다.

```azurecli
  $null = add-pnplistitem
```
스크립트에서 cmdlet 출력을 구문 분석 하는 경우에는 스크립트에서 출력을 변수에 저장 하 고 단순히 출력을 스트리밍하는 대신 변수를 조작 해야 합니다.

```azurecli
$SomeVariable = add-pnplistitem ....
if ($SomeVariable.someproperty -eq ....
```

## <a name="other"></a>내 문제가 위에 나열 되어 있지 않습니다.

아래 섹션에는 문제를 해결 하는 데 도움이 되는 지원 설명서 외에도 다른 일반적인 오류가 나열 되어 있습니다.

### <a name="hybrid-runbook-worker-doesnt-run-jobs-or-isnt-responding"></a>Hybrid Runbook Worker가 작업을 실행하지 않거나 응답하지 않음

Azure Automation 대신 hybrid worker를 사용 하 여 작업을 실행 하는 경우 [hybrid worker 자체의 문제를 해결](https://docs.microsoft.com/azure/automation/troubleshoot/hybrid-runbook-worker)해야 할 수 있습니다.

### <a name="runbook-fails-with-no-permission-or-some-variation"></a>"권한 없음" 또는 약간의 변형으로 인해 Runbook이 실패

실행 계정에는 현재 계정으로 Azure 리소스에 대 한 사용 권한이 없을 수 있습니다. 실행 계정에 스크립트에서 사용 되는 [리소스에 대 한 액세스 권한이](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal) 있는지 확인 합니다.

### <a name="runbooks-were-working-but-suddenly-stopped"></a>Runbook이 작동 중에 갑자기 중지됨

* Runbook이 이전에 실행 되었지만 중지 된 경우 [실행 계정이](https://docs.microsoft.com/azure/automation/manage-runas-account#cert-renewal) 만료 되지 않았는지 확인 합니다.
* 웹 후크를 사용 하 여 runbook을 시작 하는 경우 [webhook](https://docs.microsoft.com/azure/automation/automation-webhooks#renew-webhook) 가 만료 되지 않았는지 확인 합니다.

### <a name="issues-passing-parameters-into-webhooks"></a>웹 후크에 매개 변수를 전달 하는 문제

웹 후크에 매개 변수를 전달 하는 데 도움이 필요한 경우 [webhook에서 Runbook 시작](https://docs.microsoft.com/azure/automation/automation-webhooks#parameters)을 참조 하세요.

### <a name="issues-using-az-modules"></a>Az modules 사용 문제

동일한 Automation 계정에서 Az modules 및 AzureRM 모듈을 사용 하는 것은 지원 되지 않습니다. 자세한 내용은 [runbook의 Az modules](https://docs.microsoft.com/azure/automation/az-modules) 항목을 참조 하세요.

### <a name="inconsistent-behavior-in-runbooks"></a>Runbook의 동작이 일관적이지 않음

[Runbook 실행](https://docs.microsoft.com/azure/automation/automation-runbook-execution#runbook-behavior) 의 지침에 따라 동시 작업, 여러 번 생성 된 리소스 또는 runbook의 다른 타이밍 관련 논리와 관련 된 문제를 방지 합니다.

### <a name="runbook-fails-with-the-error-no-permission-forbidden-403-or-some-variation"></a>Runbook이 권한 없음, 사용 권한 없음 (403) 또는 일부 변형 오류로 인해 실패 함

실행 계정에는 현재 계정으로 Azure 리소스에 대 한 사용 권한이 없을 수 있습니다. 실행 계정에 스크립트에서 사용 되는 [리소스에 대 한 액세스 권한이](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal) 있는지 확인 합니다.

### <a name="runbooks-were-working-but-suddenly-stopped"></a>Runbook이 작동 중에 갑자기 중지됨

* Runbook이 이전에 실행 되었지만 중지 된 경우 실행 계정이 만료 되지 않았는지 확인 합니다. [인증 갱신](https://docs.microsoft.com/azure/automation/manage-runas-account#cert-renewal)을 참조 하세요.
* 웹 후크를 사용 하 여 runbook을 시작 하는 경우 webhook [가 만료 되지](https://docs.microsoft.com/azure/automation/automation-webhooks#renew-webhook)않았는지 확인 합니다.

### <a name="passing-parameters-into-webhooks"></a>webhook에 매개 변수 전달

웹 후크에 매개 변수를 전달 하는 데 도움이 필요한 경우 [webhook에서 Runbook 시작](https://docs.microsoft.com/azure/automation/automation-webhooks#parameters)을 참조 하세요.

### <a name="using-az-modules"></a>Az 모듈 사용

동일한 Automation 계정에서 Az modules 및 AzureRM 모듈을 사용 하는 것은 지원 되지 않습니다. [Runbook의 Az modules을](https://docs.microsoft.com/azure/automation/az-modules)참조 하십시오.

### <a name="using-self-signed-certificates"></a>자체 서명 된 인증서 사용

자체 서명 된 인증서를 사용 하려면 [새 인증서 만들기](https://docs.microsoft.com/azure/automation/shared-resources/certificates#creating-a-new-certificate)를 참조 하세요.

## <a name="recommended-documents"></a>권장되는 문서

* [Azure Automation에서 Runbook 시작](https://docs.microsoft.com/azure/automation/automation-starting-a-runbook)
* [Azure Automation에서 Runbook 실행](https://docs.microsoft.com/azure/automation/automation-runbook-execution)

## <a name="next-steps"></a>다음 단계

문제가 표시되지 않거나 문제를 해결할 수 없는 경우 다음 채널 중 하나를 방문하여 추가 지원을 받으세요.

* [Azure 포럼](https://azure.microsoft.com/support/forums/)을 통해 Azure 전문가로부터 답변을 얻습니다.
* [@AzureSupport](https://twitter.com/azuresupport)를 사용하여 연결 – Azure 커뮤니티를 적절한 리소스(답변, 지원 및 전문가)에 연결하여 고객 환경을 개선하는 공식 Microsoft Azure 계정입니다.
* 추가 지원이 필요한 경우, Azure 기술 지원 인시던트를 제출할 수 있습니다. [Azure 지원 사이트](https://azure.microsoft.com/support/options/) 로 가서 **지원 받기**를 선택합니다.
