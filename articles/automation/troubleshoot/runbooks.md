---
title: Azure 자동화 Runbook 오류 문제 해결
description: Azure 자동화 실행책에서 발생할 수 있는 문제를 해결하고 해결하는 방법을 알아봅니다.
services: automation
author: mgoedtel
ms.author: magoedte
ms.date: 01/24/2019
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: 5ed25821f606b98bacf2acf3c2c389a8437406fa
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/22/2020
ms.locfileid: "81770914"
---
# <a name="troubleshoot-runbook-errors"></a>Runbook 오류 문제 해결

 이 문서에서는 발생할 수 있는 다양한 Runbook 오류와 이를 해결하는 방법에 대해 설명합니다.

>[!NOTE]
>이 문서는 새 Azure PowerShell Az 모듈을 사용하도록 업데이트되었습니다. AzureRM 모듈은 적어도 2020년 12월까지 버그 수정을 수신할 예정이므로 계속 사용하셔도 됩니다. 새 Az 모듈 및 AzureRM 호환성에 대한 자세한 내용은 [새 Azure PowerShell Az 모듈 소개](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0)를 참조하세요. 하이브리드 Runbook 작업자의 Az 모듈 설치 지침은 [Azure PowerShell 모듈 설치를](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)참조하십시오. 자동화 계정의 경우 Azure 자동화 에서 [Azure PowerShell 모듈을 업데이트하는 방법을](../automation-update-azure-modules.md)사용하여 모듈을 최신 버전으로 업데이트할 수 있습니다.

## <a name="diagnosing-runbook-issues"></a>Runbook 문제 진단

Azure Automation에서 Runbook 을 실행하는 동안 오류가 발생하면 다음 단계를 사용하여 문제를 진단할 수 있습니다.

1. **Runbook 스크립트가 로컬 컴퓨터에서 성공적으로 실행되는지 확인합니다.** 

    언어 참조 및 학습 모듈은 [PowerShell 문서](/powershell/scripting/overview) 또는 [파이썬 문서를](https://docs.python.org/3/) 참조하십시오. 스크립트를 로컬로 실행하면 다음과 같은 일반적인 오류를 검색하고 해결할 수 있습니다.

      * 누락된 모듈
      * 구문 오류
      * 논리 오류

2. **Runbook [오류 스트림을 조사합니다.](https://docs.microsoft.com/azure/automation/automation-runbook-output-and-messages#runbook-output)**

    이러한 스트림에서 특정 메시지를 살펴보고 이 문서에 설명된 오류와 비교합니다.

3. **노드 및 자동화 작업 영역에 필요한 모듈이 있는지 확인합니다.** 

    Runbook에서 모듈을 가져오는 경우 가져오기 모듈에 나열된 단계를 사용하여 자동화 계정에서 모듈을 사용할 수 있는지 [확인합니다.](../shared-resources/modules.md#importing-modules) [Azure 자동화의 Azure 업데이트 모듈의](..//automation-update-azure-modules.md)지침에 따라 모듈을 최신 버전으로 업데이트합니다. 자세한 문제 해결 정보는 [문제 해결 모듈을](shared-resources.md#modules)참조하십시오.

4. **Runbook이 일시 중단되거나 예기치 않게 실패한 경우 수행합니다.**

    * [작업 상태 확인은](https://docs.microsoft.com/azure/automation/automation-runbook-execution#job-statuses) Runbook 상태와 몇 가지 가능한 원인을 정의합니다.
    * Runbook에 [추가 출력을 추가하여](https://docs.microsoft.com/azure/automation/automation-runbook-output-and-messages#message-streams) Runbook이 일시 중단되기 전에 발생하는 문제를 식별합니다.
    * 작업에 의해 throw되는 [예외를 처리합니다.](https://docs.microsoft.com/azure/automation/automation-runbook-execution#handling-exceptions)

## <a name="scenario-run-login-azurermaccount-to-log-in"></a><a name="login-azurerm"></a>시나리오: 로그인-AzureRM계정 실행

### <a name="issue"></a>문제

Runbook을 실행할 때 다음과 같은 오류가 발생합니다.

```error
Run Login-AzureRMAccount to login.
```

### <a name="cause"></a>원인

이 오류는 실행 As 계정을 사용하지 않거나 실행 됨 계정이 만료된 경우에 발생할 수 있습니다. [Azure 자동화 실행 을 계정으로 관리 를](https://docs.microsoft.com/azure/automation/manage-runas-account)참조하십시오.

이 오류에는 두 가지 주요 원인이 있습니다.

* AzureRM 또는 Az 모듈에는 여러 버전이 있습니다.
* 별도의 구독에서 리소스에 액세스하려고 합니다.

### <a name="resolution"></a>해결 방법

하나의 AzureRM 또는 Az 모듈을 업데이트한 후 이 오류가 발생하면 모든 모듈을 동일한 버전으로 업데이트해야 합니다.

다른 구독의 리소스에 액세스하려는 경우 아래 단계에 따라 권한을 구성할 수 있습니다.

1. 자동화 실행 계정으로 이동하여 응용 프로그램 ID와 지문을 복사합니다.

    ![아이디와 지문 복사](../media/troubleshoot-runbooks/collect-app-id.png)

1. 자동화 계정이 호스팅되지 않는 구독의 액세스 제어로 이동하여 새 역할 할당을 추가합니다.

    ![Access Control](../media/troubleshoot-runbooks/access-control.png)

1. 이전에 수집한 응용 프로그램 ID를 추가합니다. 기고자 권한을 선택합니다.

    ![역할 할당 추가](../media/troubleshoot-runbooks/add-role-assignment.png)

1. 구독 이름을 복사합니다.

1. 이제 다음 Runbook 코드를 사용하여 자동화 계정에서 다른 구독으로의 사용 권한을 테스트할 수 있습니다. 1단계에서 복사된 값으로 바꿉습니다. `"\<CertificateThumbprint\>"` 4단계에서 복사된 값으로 바꿉습니다. `"\<SubscriptionName\>"`

    ```powershell
    $Conn = Get-AutomationConnection -Name AzureRunAsConnection
    Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint "<CertificateThumbprint>"
    #Select the subscription you want to work with
    Select-AzSubscription -SubscriptionName '<YourSubscriptionNameGoesHere>'

    #Test and get outputs of the subscriptions you granted access.
    $subscriptions = Get-AzSubscription
    foreach($subscription in $subscriptions)
    {
        Set-AzContext $subscription
        Write-Output $subscription.Name
    }
    ```

## <a name="scenario-unable-to-find-the-azure-subscription"></a><a name="unable-to-find-subscription"></a>시나리오: Azure 구독을 찾을 수 없음

### <a name="issue"></a>문제

`Select-AzureSubscription`에서 `Select-AzureRMSubscription`또는 `Select-AzSubscription` cmdlet로 작업할 때 다음과 같은 오류가 발생합니다.

```error
The subscription named <subscription name> cannot be found.
```

### <a name="error"></a>Error

이 오류는 다음과 같은 경우에 발생할 수 있습니다.

* 구독 이름이 잘못되었습니다.
* 구독 세부 정보를 얻으려고 하는 Azure Active Directory 사용자는 구독관리자로 구성되지 않습니다.
* cmdlet은 사용할 수 없습니다.

### <a name="resolution"></a>해결 방법

아래 단계에 따라 Azure에 인증했는지 확인하고 선택하려는 구독에 액세스할 수 있는지 확인합니다.

1. 스크립트가 독립 실행형으로 작동하는지 확인하려면 Azure Automation 외부에서 테스트합니다.
2. cmdlet을 실행하기 전에 스크립트가 [Connect-AzAccount](https://docs.microsoft.com/powershell/module/Az.Accounts/Connect-AzAccount?view=azps-3.7.0) cmdlet을 `Select-*` 실행했는지 확인합니다.
3. Runbook의 시작 부분에 `Disable-AzContextAutosave –Scope Process`를 추가합니다. 이 cmdlet 호출은 모든 자격 증명이 현재 Runbook의 실행에만 적용되도록 합니다.
4. 이 오류 메시지가 계속 표시되면 에 대한 `AzContext` `Connect-AzAccount`매개 변수를 추가하여 코드를 수정한 다음 코드를 실행합니다.

   ```powershell
   Disable-AzContextAutosave –Scope Process

   $Conn = Get-AutomationConnection -Name AzureRunAsConnection
   Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint

   $context = Get-AzContext

   Get-AzVM -ResourceGroupName myResourceGroup -AzContext $context
    ```

## <a name="scenario-authentication-to-azure-failed-because-multi-factor-authentication-is-enabled"></a><a name="auth-failed-mfa"></a>시나리오: Multi-Factor Authentication이 활성화되어 Azure 인증에 실패

### <a name="issue"></a>문제

Azure 사용자 이름 및 암호를 사용하여 Azure에 인증할 때 다음 오류가 발생합니다.

```error
Add-AzureAccount: AADSTS50079: Strong authentication enrollment (proof-up) is required
```

### <a name="cause"></a>원인

Azure 계정에서 Multi-Factor Authentication을 사용하면 Azure Active Directory 사용자를 사용하여 Azure에 인증할 수 없습니다. 대신 인증하려면 인증서 또는 서비스 주체를 사용해야 합니다.

### <a name="resolution"></a>해결 방법

Azure 클래식 배포 모델 cmdlet이 있는 인증서를 사용하려면 [Azure 서비스를 관리하는 인증서 만들기 및 추가를](https://blogs.technet.com/b/orchestrator/archive/2014/04/11/managing-azure-services-with-the-microsoft-azure-automation-preview-service.aspx)참조하십시오. Azure 리소스 관리자 cmdlet을 사용하여 서비스 주체를 사용하려면 [Azure 포털을 사용하여 서비스 주체 만들기](../../active-directory/develop/howto-create-service-principal-portal.md) 및 [Azure 리소스 관리자를 사용하여 서비스 주체 를 인증합니다.](../../active-directory/develop/howto-authenticate-service-principal-powershell.md)

## <a name="scenario-you-see-an-error-in-your-job-streams-about-the-get_serializationsettings-method"></a><a name="get-serializationsettings"></a>시나리오: get_SerializationSettings 메서드에 대한 작업 스트림에 오류가 표시됩니다.

### <a name="issue"></a>문제

Runbook에 대한 작업 스트림에 다음과 같은 오류가 표시됩니다.

```error
Connect-AzAccount : Method 'get_SerializationSettings' in type
'Microsoft.Azure.Management.Internal.Resources.ResourceManagementClient' from assembly
'Microsoft.Azure.Commands.ResourceManager.Common, Version=4.0.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35'
does not have an implementation.
At line:16 char:1
+ Connect-AZAccount -ServicePrincipal -Tenant $Conn.TenantID -Appl ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : NotSpecified: (:) [Connect-AzAccount], TypeLoadException
    + FullyQualifiedErrorId : System.TypeLoadException,Microsoft.Azure.Commands.Profile.ConnectAzAccountCommand
```

### <a name="cause"></a>원인

이 오류는 Runbook에서 AzureRM 및 Az 모듈 cmdlet을 모두 사용하여 발생합니다. AzureRM 모듈을 가져오기 전에 Az 모듈을 가져올 때 발생합니다.

### <a name="resolution"></a>해결 방법

Az 및 AzureRM cmdlet을 가져오고 동일한 Runbook에서 사용할 수 없습니다. Azure 자동화의 Az cmdlet에 대해 자세히 알아보려면 [Azure 자동화의 모듈 관리를](../shared-resources/modules.md)참조하십시오.

## <a name="scenario-the-runbook-fails-with-the-error-a-task-was-canceled"></a><a name="task-was-cancelled"></a>시나리오: 다음 오류로 인해 Runbook이 실패합니다. 작업이 취소됨

### <a name="issue"></a>문제

다음 예제와 비슷한 오류가 발생하여 Runbook이 실패합니다.

```error
Exception: A task was canceled.
```

### <a name="cause"></a>원인

이 오류는 오래된 Azure 모듈을 사용하여 발생할 수 있습니다.

### <a name="resolution"></a>해결 방법

Azure 모듈을 최신 버전으로 업데이트하여 이 오류를 해결할 수 있습니다. 

1. 자동화 계정에서 **모듈을 클릭한**다음 **Azure 모듈 을 업데이트합니다.** 
2. 업데이트는 약 15분 정도 소요됩니다. 완료되면 실패한 Runbook을 다시 실행합니다.

모듈 업데이트에 대해 자세히 알아 보려면 [Azure Automation에서 Azure 모듈 업데이트](../automation-update-azure-modules.md)를 참조하세요.

## <a name="scenario-runbooks-fail-when-dealing-with-multiple-subscriptions"></a><a name="runbook-auth-failure"></a>시나리오: 여러 구독을 처리할 때 Runbook실패

### <a name="issue"></a>문제

Runbook을 실행할 때 Runbook은 Azure 리소스를 관리하지 못합니다.

### <a name="cause"></a>원인

Runbook이 실행 시 올바른 컨텍스트를 사용하지 않습니다.

### <a name="resolution"></a>해결 방법

Runbook이 여러 Runbook을 호출할 때 구독 컨텍스트가 손실될 수 있습니다. 구독 컨텍스트가 Runbook에 전달되도록 하려면 클라이언트 Runbook이 매개 변수의 `Start-AzureRmAutomationRunbook` cmdlet에 컨텍스트를 전달하도록 합니다. `AzureRmContext` 매개 `Disable-AzureRmContextAutosave` 변수가 설정된 cmdlet을 `Process` 사용하여 지정된 자격 증명이 현재 Runbook에만 사용되도록 합니다. `Scope` 자세한 내용은 [여러 구독 작업을](../automation-runbook-execution.md#working-with-multiple-subscriptions)참조하십시오.

```azurepowershell-interactive
# Ensures that any credentials apply only to the execution of this runbook
Disable-AzContextAutosave –Scope Process

# Connect to Azure with Run As account
$ServicePrincipalConnection = Get-AutomationConnection -Name 'AzureRunAsConnection'

Connect-AzAccount `
    -ServicePrincipal `
    -Tenant $ServicePrincipalConnection.TenantId `
    -ApplicationId $ServicePrincipalConnection.ApplicationId `
    -CertificateThumbprint $ServicePrincipalConnection.CertificateThumbprint

$AzContext = Select-AzSubscription -SubscriptionId $ServicePrincipalConnection.SubscriptionID

$params = @{"VMName"="MyVM";"RepeatCount"=2;"Restart"=$true}

Start-AzAutomationRunbook `
    –AutomationAccountName 'MyAutomationAccount' `
    –Name 'Test-ChildRunbook' `
    -ResourceGroupName 'LabRG' `
    -AzContext $AzureContext `
    –Parameters $params –wait
```

## <a name="scenario-term-not-recognized-as-the-name-of-a-cmdlet-function-script"></a><a name="not-recognized-as-cmdlet"></a>시나리오: cmdlet, 함수, 스크립트의 이름으로 인식되지 않는 용어

### <a name="issue"></a>문제

다음 예제와 비슷한 오류가 발생하여 Runbook이 실패합니다.

```error
The term 'Connect-AzAccount' is not recognized as the name of a cmdlet, function, script file, or operable program. Check the spelling of the name, or if the path was included verify that the path is correct and try again.
```

### <a name="cause"></a>원인

이 오류는 다음과 같은 이유로 발생할 수 있습니다.

* cmdlet을 포함하는 모듈은 자동화 계정으로 가져오지 않습니다.
* cmdlet을 포함하는 모듈을 가져왔지만 오래되었습니다.

### <a name="resolution"></a>해결 방법

이 오류를 해결 하려면 다음 작업 중 하나를 수행 합니다. 

* Azure 모듈의 경우 [Azure 자동화에서 Azure PowerShell 모듈을 업데이트하는 방법을](../automation-update-azure-modules.md) 참조하여 자동화 계정에서 모듈을 업데이트하는 방법을 알아봅니다.

* Azure가 아닌 모듈의 경우 자동화 계정으로 가져온 모듈이 있는지 확인합니다.

## <a name="scenario-the-runbook-job-start-was-attempted-three-times-but-failed-to-start-each-time"></a><a name="job-attempted-3-times"></a>시나리오: Runbook 작업 시작을 세 번 시도했지만 매번 시작하지 못했습니다.

### <a name="issue"></a>문제

다음과 같은 오류로 Runbook에 오류가 발생합니다.

```error
The job was tried three times but it failed
```

### <a name="cause"></a>원인

이 오류는 다음 문제 중 하나로 인해 발생합니다.

* 메모리 제한. 400MB 이상의 메모리를 사용하는 경우 작업이 실패할 수 있습니다. 샌드박스에 할당된 메모리에 대한 문서화된 제한은 [자동화 서비스 제한에서](../../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits)찾을 수 있습니다. 

* 네트워크 소켓입니다. Azure 샌드박스는 1000개의 동시 네트워크 소켓으로 제한됩니다. [자동화 서비스 제한을](../../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits)참조하십시오.

* 모듈이 호환되지 않음. 모듈 종속성이 올바르지 않을 수 있습니다. 이 경우 Runbook은 일반적으로 `Command not found` a `Cannot bind parameter` 또는 메시지를 반환합니다.

* 샌드박스에 대한 Active Directory를 사용하면 인증이 없습니다. Runbook이 Azure 샌드박스에서 실행되는 실행 또는 하위 프로세스를 호출하려고 했습니다. ADAL(Azure Active Directory 인증 라이브러리)을 사용하여 Azure AD로 인증할 런북을 구성하는 것은 지원되지 않습니다.

* 너무 많은 예외 데이터입니다. Runbook에서 출력 스트림에 너무 많은 예외 데이터를 작성하려고 했습니다.

### <a name="resolution"></a>해결 방법

* 메모리 제한, 네트워크 소켓. 메모리 한도 내에서 작업하는 방법을 제안하는 방법은 워크로드를 여러 런북으로 분할하고, 메모리에서 적은 데이터를 처리하고, Runbook에서 불필요한 출력을 작성하지 않도록 하고, PowerShell 워크플로런북에 기록되는 검사점 수를 고려하는 것입니다. 와 같은 `$myVar.clear`clear 메서드를 사용하여 변수를 지우고 가비지 수집을 즉시 실행하는 데 사용합니다. `[GC]::Collect` 이러한 작업은 런타임 중에 Runbook의 메모리 공간을 줄입니다.

* 모듈이 호환되지 않음. Azure 자동화에서 Azure PowerShell 모듈을 업데이트하는 방법의 단계를 수행하여 [Azure 모듈을 업데이트합니다.](../automation-update-azure-modules.md)

* 샌드박스에 대한 Active Directory를 사용하면 인증이 없습니다. Runbook을 사용하여 Azure AD를 인증할 때 자동화 계정에서 Azure AD 모듈을 사용할 수 있는지 확인합니다. Run As 계정에 Runbook이 자동화하는 작업을 수행하는 데 필요한 권한을 부여해야 합니다.

  Runbook이 Azure 샌드박스에서 실행되는 실행 또는 하위 프로세스를 호출할 수 없는 경우 [하이브리드 Runbook Worker에서](../automation-hrw-run-runbooks.md)Runbook을 사용합니다. 하이브리드 작업자는 Azure 샌드박스에 있는 메모리 및 네트워크 제한에 의해 제한되지 않습니다.

* 너무 많은 예외 데이터입니다. 작업 출력 스트림에는 1MB 제한이 있습니다. Runbook이 실행 및 `try` `catch` 블록을 사용하여 실행 가능 또는 하위 프로세스에 대한 호출을 둘러싸는지 확인합니다. 작업이 예외를 throw하는 경우 코드는 예외의 메시지를 자동화 변수로 작성해야 합니다. 이 기술을 사용하면 메시지가 작업 출력 스트림에 기록되지 않습니다.

## <a name="scenario-sign-in-to-azure-account-failed"></a><a name="sign-in-failed"></a>시나리오: Azure 계정에 로그인하지 못했습니다.

### <a name="issue"></a>문제

`Connect-AzAccount` cmdlet으로 작업할 때 다음 오류 중 하나가 표시됩니다.

```error
Unknown_user_type: Unknown User Type
```

```error
No certificate was found in the certificate store with thumbprint
```

### <a name="cause"></a>원인

이러한 오류는 자격 증명 자산 이름이 유효하지 않은 경우에 발생합니다. 자동화 자격 증명 자산을 설정하는 데 사용한 사용자 이름과 암호가 유효하지 않은 경우에도 발생할 수 있습니다.

### <a name="resolution"></a>해결 방법

무엇이 문제인지 확인하기 위해 다음 단계를 수행하세요.

1. 특수 문자가 없는지 확인합니다. 이러한 문자로 `\@` 문자가 Azure에 연결하는 데 사용하는 Automation 자격 증명 자산 이름에 포함됩니다.
2. 로컬 PowerShell ISE 편집기의 Azure 자동화 자격 증명에 저장된 사용자 이름과 암호를 사용할 수 있는지 확인합니다. PowerShell ISE에서 다음 cmdlet을 실행합니다.

   ```powershell
   $Cred = Get-Credential
   #Using Azure Service Management
   Add-AzureAccount –Credential $Cred
   #Using Azure Resource Manager
   Connect-AzAccount –Credential $Cred
   ```

3. 인증이 로컬에서 실패하면 Azure Active Directory 자격 증명을 제대로 설정하지 않은 것입니다. Azure Active Directory 블로그 게시물을 [사용하여 Azure에 대한 인증을](https://azure.microsoft.com/blog/azure-automation-authenticating-to-azure-using-azure-active-directory/) 참조하여 Azure Active Directory 계정을 올바르게 설정합니다.

4. 오류가 일시적인 것으로 나타나면 인증 루틴에 다시 시도 논리를 추가하여 인증을 보다 강력하게 만들어 보십시오.

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
       $connectionResult = Connect-AzAccount `
                              -ServicePrincipal `
                              -Tenant $servicePrincipalConnection.TenantId `
                              -ApplicationId $servicePrincipalConnection.ApplicationId `
                              -CertificateThumbprint $servicePrincipalConnection.CertificateThumbprint

       Start-Sleep -Seconds 30
   }
   ```

## <a name="scenario-object-reference-not-set-to-an-instance-of-an-object"></a><a name="child-runbook-object"></a>시나리오: 개체 참조가 개체의 인스턴스로 설정되지 않음

### <a name="issue"></a>문제

`Wait` 매개 변수를 사용 하 고 출력 스트림 개체를 포함 하는 자식 Runbook을 호출할 때 다음과 같은 오류가 발생 합니다.

```error
Object reference not set to an instance of an object
```

### <a name="cause"></a>원인

`Start-AzAutomationRunbook`스트림에 개체가 포함된 경우 Output 스트림을 올바르게 처리하지 않습니다.

### <a name="resolution"></a>해결 방법

폴링 논리를 구현하고 [Get-AzAutomationJobOutput](https://docs.microsoft.com/powershell/module/Az.Automation/Get-AzAutomationJobOutput?view=azps-3.7.0) cmdlet을 사용하여 출력을 검색하는 것이 좋습니다. 이 논리의 샘플은 아래에 정의되어 있습니다.

```powershell
$automationAccountName = "ContosoAutomationAccount"
$runbookName = "ChildRunbookExample"
$resourceGroupName = "ContosoRG"

function IsJobTerminalState([string] $status) {
    return $status -eq "Completed" -or $status -eq "Failed" -or $status -eq "Stopped" -or $status -eq "Suspended"
}

$job = Start-AzAutomationRunbook -AutomationAccountName $automationAccountName -Name $runbookName -ResourceGroupName $resourceGroupName
$pollingSeconds = 5
$maxTimeout = 10800
$waitTime = 0
while((IsJobTerminalState $job.Status) -eq $false -and $waitTime -lt $maxTimeout) {
   Start-Sleep -Seconds $pollingSeconds
   $waitTime += $pollingSeconds
   $job = $job | Get-AzAutomationJob
}

$jobResults | Get-AzAutomationJobOutput | Get-AzAutomationJobOutputRecord | Select-Object -ExpandProperty Value
```

## <a name="scenario-runbook-fails-because-of-deserialized-object"></a><a name="fails-deserialized-object"></a>시나리오: 역직렬화된 개체로 인해 Runbook 실패

### <a name="issue"></a>문제

다음 오류로 인해 Runbook이 실패합니다.

```error
Cannot bind parameter <ParameterName>.

Cannot convert the <ParameterType> value of type Deserialized <ParameterType> to type <ParameterType>.
```

### <a name="cause"></a>원인

Runbook이 PowerShell 워크플로인 경우, 워크플로가 일시 중단되더라도 Runbook 상태를 유지하기 위해 복합 개체를 역직렬화된 형식으로 저장합니다.

### <a name="resolution"></a>해결 방법

다음 해결 방법 중 한 가지를 사용하여 이 문제를 해결합니다.

* 한 cmdlet에서 다른 cmdlet로 복잡한 오브젝트를 배관하는 `InlineScript` 경우 이러한 cmdlet을 활동에서 래핑합니다.
* 개체 전체를 전달하지 말고 복합 개체에서 필요한 이름 또는 값만 전달합니다.
* PowerShell 워크플로 Runbook 대신 PowerShell Runbook을 사용합니다.

## <a name="scenario-runbook-job-fails-because-allocated-quota-exceeded"></a><a name="quota-exceeded"></a>시나리오: 할당된 할당량을 초과하여 Runbook 작업이 실패합니다.

### <a name="issue"></a>문제

다음 오류로 인해 Runbook 작업이 실패합니다.

```error
The quota for the monthly total job run time has been reached for this subscription
```

### <a name="cause"></a>원인

작업 실행 시간이 계정에 할당된 무료 500분을 초과하면 이 오류가 발생합니다. 이 할당량은 모든 유형의 작업 실행 태스크에 적용됩니다. 이러한 작업 중 일부는 작업을 테스트하거나, 포털에서 작업을 시작하거나, webhook을 사용하여 작업을 실행하거나, Azure 포털 또는 데이터 센터를 사용하여 실행하도록 작업을 예약하는 작업입니다. 자동화 가격 책정에 대한 자세한 내용은 [자동화 가격 책정을](https://azure.microsoft.com/pricing/details/automation/)참조하십시오.

### <a name="resolution"></a>해결 방법

매월 500분 이상의 처리를 사용하려면 구독을 프리 티어에서 기본 계층으로 변경합니다.

1. Azure 구독에 로그인합니다.
2. 업그레이드할 자동화 계정을 선택합니다.
3. **설정을**클릭한 다음 **가격 책정**.
4. 페이지 하단에서 **활성화를** 클릭하여 계정을 기본 계층으로 업그레이드합니다.

## <a name="scenario-cmdlet-not-recognized-when-executing-a-runbook"></a><a name="cmdlet-not-recognized"></a>시나리오: Runbook을 실행해도 cmdlet이 인식되지 않음

### <a name="issue"></a>문제

다음 오류로 인해 Runbook 작업이 실패합니다.

```error
<cmdlet name>: The term <cmdlet name> is not recognized as the name of a cmdlet, function, script file, or operable program.
```

### <a name="cause"></a>원인

PowerShell 엔진을 통해 Runbook에서 사용하는 cmdlet을 찾을 수 없는 경우에 이 오류가 발생합니다. cmdlet을 포함하는 모듈이 계정에서 누락되었거나, Runbook 이름과 이름 충돌이 있거나, cmdlet도 다른 모듈에 존재하고 자동화가 이름을 확인할 수 없습니다.

### <a name="resolution"></a>해결 방법

다음 해결 방법 중 한 가지를 사용하여 문제를 해결합니다.

* cmdlet 이름을 올바르게 입력했는지 확인합니다.
* 자동화 계정에 cmdlet이 있는지, 충돌이 없는지 확인합니다. cmdlet이 있는지 확인하려면 편집 모드에서 Runbook을 열고 라이브러리에서 찾을 cmdlet을 검색하거나 실행합니다. `Get-Command <CommandName>` cmdlet을 계정에서 사용할 수 있고 다른 cmdlet 또는 runbook과 이름이 충돌하지 않는지 확인한 후에는 캔버스에 cmdlet을 추가하고 Runbook에서 유효한 매개 변수 집합을 사용하고 있는지 확인합니다.
* 이름이 충돌하고 cmdlet을 두 개의 서로 다른 모듈에서 사용할 수 있는 경우 cmdlet에 대해 정규화된 이름을 사용하여 문제를 해결합니다. 예를 들어 `ModuleName\CmdletName`를 사용할 수 있습니다.
* 하이브리드 작업자 그룹에서 Runbook 온-프레미스를 실행하는 경우 하이브리드 작업자를 호스팅하는 컴퓨터에 모듈과 cmdlet이 설치되어 있는지 확인합니다.

## <a name="scenario-a-long-running-runbook-fails-to-complete"></a><a name="long-running-runbook"></a>시나리오: 장기 실행 실행 중인 Runbook을 완료하지 못합니다.

### <a name="issue"></a>문제

3시간 동안 실행된 후에 Runbook이 중지됨 상태로 표시됩니다. 다음과 같은 오류가 발생할 수도 있습니다.

```error
The job was evicted and subsequently reached a Stopped state. The job cannot continue running.
```

이 동작은 Azure 자동화 내의 프로세스에 대한 [공정한 공유](../automation-runbook-execution.md#fair-share) 모니터링으로 인해 Azure 샌드박스에서 의도적으로 설계되었습니다. 프로세스가 3시간 이상 실행되면 공정 공유가 런북을 자동으로 중지합니다. 공정 공유 시간 제한을 초과하는 Runbook의 상태는 Runbook 유형에 따라 다릅니다. PowerShell 및 Python Runbook은 중지됨 상태로 설정되며, PowerShell 워크플로 Runbook은 실패 상태로 설정됩니다.

### <a name="cause"></a>원인

Runbook은 Azure 샌드박스에서 공정한 공유로 허용되는 3시간 제한을 초과했습니다.

### <a name="resolution"></a>해결 방법

권장되는 해결 방법 중 하나는 [Hybrid Runbook Worker](../automation-hrw-run-runbooks.md)에서 Runbook을 실행하는 것입니다. 하이브리드 작업자는 Azure 샌드박스가 가지고 있는 3시간 공정 공유 런북 한도에 제한을 받지 않습니다. 하이브리드 Runbook 작업자에서 실행되는 Runbook은 예기치 않은 로컬 인프라 문제가 있는 경우 다시 시작 동작을 지원하도록 개발되어야 합니다.

또 다른 해결책은 자식 Runbook을 만들어 [Runbook을](../automation-child-runbooks.md)최적화하는 것입니다. 예를 들어 여러 데이터베이스의 데이터베이스 작업에서 Runbook이 여러 리소스에서 동일한 함수를 반복하는 경우 함수를 자식 Runbook으로 이동할 수 있습니다. 각 자식 Runbook은 별도의 프로세스에서 병렬로 실행됩니다. 이 동작은 부모 Runbook이 완료되는 총 기간을 감소시킵니다.

자식 Runbook 시나리오를 사용하도록 설정하는 PowerShell cmdlet은 다음과 같습니다.

* [시작-아즈오토메이션런북](https://docs.microsoft.com/powershell/module/Az.Automation/Start-AzAutomationRunbook?view=azps-3.7.0). 이 cmdlet을 사용하면 Runbook을 시작하고 매개 변수를 Runbook에 전달할 수 있습니다.

* [Get-AzAutomationJob](https://docs.microsoft.com/powershell/module/Az.Automation/Get-AzAutomationJob?view=azps-3.7.0). 자식 Runbook이 완료된 후 수행해야 하는 작업이 있는 경우 이 cmdlet을 사용하면 각 자식의 작업 상태를 확인할 수 있습니다.

## <a name="scenario-status-400-bad-request-when-calling-a-webhook"></a><a name="expired webhook"></a>시나리오: 상태: 웹후크를 호출할 때 400 개의 잘못된 요청

### <a name="issue"></a>문제

Azure 자동화 Runbook에 대한 웹후크를 호출하려고 하면 다음과 같은 오류가 발생합니다.

```error
400 Bad Request : This webhook has expired or is disabled
```

### <a name="cause"></a>원인

호출하려는 웹후크가 사용되지 않거나 만료되었습니다.

### <a name="resolution"></a>해결 방법

webhook가 비활성화된 경우Azure Portal을 통해 webhook를 다시 활성화할 수 있습니다. 웹후크가 만료된 경우 삭제한 다음 다시 만들어야 합니다. 아직 만료되지 않은 경우에만 [웹후크를 갱신](../automation-webhooks.md#renew-webhook)할 수 있습니다.

## <a name="scenario-429-the-request-rate-is-currently-too-large"></a><a name="429"></a>시나리오: 429: 현재 요청 속도가 너무 큽...

### <a name="issue"></a>문제

`Get-AzAutomationJobOutput` cmdlet을 실행할 때 다음 오류 메시지가 나타납니다.

```error
429: The request rate is currently too large. Please try again
```

### <a name="cause"></a>원인

이 오류는 [자세한 내용이](../automation-runbook-output-and-messages.md#verbose-stream)많은 Runbook에서 작업 출력을 검색할 때 발생할 수 있습니다.

### <a name="resolution"></a>해결 방법

이 오류를 해결하려면 다음 중 하나를 수행합니다.

* Runbook을 편집하고 내보내는 작업 스트림 수를 줄입니다.

* cmdlet을 실행할 때 검색할 스트림 수를 줄입니다. 이렇게 하려면 [Get-AzAutomationJobOutput](https://docs.microsoft.com/powershell/module/Az.Automation/Get-AzAutomationJobOutput?view=azps-3.7.0) `Stream` cmdlet에 대한 매개 변수 값을 설정하여 출력 스트림만 검색할 수 있습니다. 

## <a name="scenario-powershell-job-fails-with-error-cannot-invoke-method"></a><a name="cannot-invoke-method"></a>시나리오: PowerShell 작업이 오류로 실패: 메서드를 호출할 수 없음

### <a name="issue"></a>문제

Azure에서 실행 중인 Runbook에서 PowerShell 작업을 시작할 때 다음과 같은 오류 메시지가 나타납니다.

```error
Exception was thrown - Cannot invoke method. Method invocation is supported only on core types in this language mode.
```

### <a name="cause"></a>원인

이 오류는 Azure 샌드박스에서 실행되는 Runbook이 [전체 언어 모드에서](/powershell/module/microsoft.powershell.core/about/about_language_modes)실행할 수 없음을 나타낼 수 있습니다.

### <a name="resolution"></a>해결 방법

이 오류를 해결하는 방법에는 두 가지가 있습니다.

* [시작 작업을](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/start-job?view=powershell-7)사용하는 대신 [시작-AzAutomationRunbook을](https://docs.microsoft.com/powershell/module/az.automation/start-azautomationrunbook?view=azps-3.7.0) 사용하여 Runbook을 시작합니다.
* 하이브리드 Runbook 작업자에서 Runbook을 실행해 보십시오.

이 동작 및 Azure 자동화 Runbook의 다른 동작에 대해 자세히 알아보려면 [Runbook 동작을](../automation-runbook-execution.md#runbook-behavior)참조하십시오.

## <a name="scenario-linux-hybrid-runbook-worker-receives-a-prompt-for-a-password-when-signing-a-runbook"></a>시나리오: Linux 하이브리드 Runbook 작업자가 Runbook에 서명할 때 암호에 대한 프롬프트를 받습니다.

### <a name="issue"></a>문제

Linux `sudo` 하이브리드 Runbook 작업자에 대한 명령을 실행하면 암호에 대한 예기치 않은 프롬프트가 검색됩니다.

### <a name="cause"></a>원인

Linux용 로그 분석 에이전트의 **nxautomationuser** 계정이 **sudoers** 파일에 올바르게 구성되지 않았습니다. 하이브리드 Runbook 작업자는 Linux Runbook Worker에서 Runbook에 서명할 수 있도록 계정 사용 권한 및 기타 데이터의 적절한 구성이 필요합니다.

### <a name="resolution"></a>해결 방법

* 하이브리드 Runbook 작업자가 컴퓨터에 GnuPG(GPG) 실행 가능한 실행 가능한 이 있는지 확인합니다.

* **sudoers** 파일에서 **nxautomationuser** 계정의 구성을 확인합니다. [하이브리드 Runbook 작업자에서 실행 중인 책 보기](../automation-hrw-run-runbooks.md)

## <a name="scenario-cmdlet-failing-in-pnp-powershell-runbook-on-azure-automation"></a>시나리오: Azure 자동화에서 PnP PowerShell 실행책에서 Cmdlet 실패

### <a name="issue"></a>문제

Runbook에서 PnP PowerShell에서 생성된 개체를 Azure 자동화 출력에 직접 기록하면 cmdlet 출력이 자동화로 다시 스트리밍될 수 없습니다.

### <a name="cause"></a>원인

이 문제는 Azure Automation이 반환 개체를 catch하지 않고 PnP PowerShell cmdlet을 `add-pnplistitem`호출하는 Runbook을 처리하는 경우에 가장 일반적으로 발생합니다.

### <a name="resolution"></a>해결 방법

cmdlet이 표준 출력에 전체 개체를 작성하지 않도록 스크립트를 편집하여 변수에 반환 값을 할당합니다. 스크립트는 아래와 같이 출력 스트림을 cmdlet으로 리디렉션할 수 있습니다.

```azurecli
  $null = add-pnplistitem
```

스크립트가 cmdlet 출력을 구문 분석하는 경우 스크립트는 출력을 단순히 출력을 스트리밍하는 대신 변수에 출력을 저장하고 변수를 조작해야 합니다.

```azurecli
$SomeVariable = add-pnplistitem ....
if ($SomeVariable.someproperty -eq ....
```

## <a name="scenario-invalid-status-code-forbidden-when-using-key-vault-inside-a-runbook"></a>시나리오: Runbook 내에서 키 볼트를 사용할 때 잘못된 상태 코드 "금지됨"

### <a name="issue"></a>문제

Azure 자동화 실행책을 통해 키 볼트에 액세스하려고 할 때 다음과 같은 오류가 발생합니다.

```error
Operation returned an invalid status code 'Forbidden' 
```

### <a name="cause"></a>원인

이 문제의 가능한 원인:

* 실행 계정을 사용하지 않습니다.
* 사용 권한 부족

### <a name="resolution"></a>해결 방법

#### <a name="not-using-run-as-account"></a>계정으로 실행을 사용하지 않음

[5단계 - 인증 추가를 통해 Azure 리소스를 관리하여](https://docs.microsoft.com/azure/automation/automation-first-runbook-textual-powershell#add-authentication-to-manage-azure-resources) Run As 계정을 사용하여 키 자격 증명 모음에 액세스하고 있는지 확인합니다. 

#### <a name="insufficient-permissions"></a>권한 부족

[키 자격 증명 모음에 대한 사용 권한 추가의](https://docs.microsoft.com/azure/automation/manage-runas-account#add-permissions-to-key-vault) 단계를 수행하여 Run As 계정에 키 자격 증명 모음에 액세스할 수 있는 충분한 권한이 있는지 확인합니다. 

## <a name="my-problem-isnt-listed-above"></a><a name="other"></a>위 목록에 없는 문제가 발생함

아래 섹션에서는 다른 일반적인 오류를 나열하고 문제를 해결하는 데 도움이 되는 지원 설명서를 제공합니다.

### <a name="hybrid-runbook-worker-doesnt-run-jobs-or-isnt-responding"></a>Hybrid Runbook Worker가 작업을 실행하지 않거나 응답하지 않음

Azure 자동화 대신 하이브리드 Runbook 작업자에서 작업을 실행하는 경우 [하이브리드 작업자 자체의 문제를 해결해야](https://docs.microsoft.com/azure/automation/troubleshoot/hybrid-runbook-worker)할 수 있습니다.

### <a name="runbook-fails-with-no-permission-or-some-variation"></a>권한 없음 또는 일부 변형으로 Runbook이 실패합니다.

로 실행 계정은 현재 계정과 Azure 리소스에 대해 동일한 사용 권한이 없을 수 있습니다. 실행 As 계정에 스크립트에 사용된 [모든 리소스에 액세스할 수 있는 권한이](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal) 있는지 확인합니다.

### <a name="issues-passing-parameters-into-webhooks"></a>매개 변수를 웹후크에 전달하는 문제

웹 후크에 매개 변수를 전달하는 데 대한 도움말은 [웹후크에서 Runbook 시작을](../automation-webhooks.md#parameters-used-when-the-webhook-starts-a-runbook)참조하십시오.

### <a name="issues-using-az-modules"></a>Az 모듈사용 문제

동일한 자동화 계정에서 Az 모듈 및 AzureRM 모듈을 사용하는 것은 지원되지 않습니다. 자세한 내용은 [Runbook의 Az 모듈을](https://docs.microsoft.com/azure/automation/az-modules) 참조하십시오.

### <a name="inconsistent-behavior-in-runbooks"></a>Runbook의 동작이 일관적이지 않음

[Runbook 실행의](https://docs.microsoft.com/azure/automation/automation-runbook-execution#runbook-behavior) 지침에 따라 동시 작업, 여러 번 생성되는 리소스 또는 Runbook의 다른 타이밍에 민감한 논리문제를 방지합니다.

### <a name="runbook-fails-with-the-error-no-permission-forbidden-403-or-some-variation"></a>Runbook 오류 권한 없음, 금지됨(403) 또는 일부 변형으로 실패

로 실행 계정은 현재 계정과 Azure 리소스에 대해 동일한 사용 권한이 없을 수 있습니다. 실행 As 계정에 스크립트에 사용된 [모든 리소스에 액세스할 수 있는 권한이](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal) 있는지 확인합니다.

### <a name="runbooks-were-working-but-suddenly-stopped"></a>Runbook이 작동 중에 갑자기 중지됨

* 실행 됨 계정이 만료되지 않았는지 확인합니다. [인증 갱신을](https://docs.microsoft.com/azure/automation/manage-runas-account#cert-renewal)참조하십시오.
* [웹후크를](https://docs.microsoft.com/azure/automation/automation-webhooks#renew-webhook) 사용하여 Runbook을 시작하는 경우 웹후크가 만료되지 않았는지 확인합니다.

### <a name="passing-parameters-into-webhooks"></a>webhook에 매개 변수 전달

웹 후크에 매개 변수를 전달하는 데 대한 도움말은 [웹후크에서 Runbook 시작을](https://docs.microsoft.com/azure/automation/automation-webhooks#parameters-used-when-the-webhook-starts-a-runbook)참조하십시오.

### <a name="using-az-modules"></a>Az 모듈 사용

동일한 자동화 계정에서 Az 모듈 및 AzureRM 모듈을 사용하는 것은 지원되지 않습니다. [Runbook의 Az 모듈을](https://docs.microsoft.com/azure/automation/az-modules)참조하십시오.

### <a name="using-self-signed-certificates"></a>자체 서명된 인증서 사용

자체 서명된 인증서를 사용하려면 [새 인증서 만들기를](https://docs.microsoft.com/azure/automation/shared-resources/certificates#creating-a-new-certificate)참조하십시오.

### <a name="access-denied-when-using-azure-sandbox-for-a-runbook"></a>Runbook에 Azure 샌드박스를 사용할 때 액세스가 거부되었습니다.

Azure 샌드박스는 모든 프로세스 외 COM 서버에 대한 액세스를 방지합니다. 예를 들어 샌드박스응용 프로그램이나 Runbook은 WMI(Windows 관리 계측)나 Windows 설치 관리자 서비스(msiserver.exe)로 호출할 수 없습니다. 샌드박스 사용에 대한 자세한 내용은 [Azure Automation의 Runbook 실행을](https://docs.microsoft.com/azure/automation/automation-runbook-execution)참조하십시오.

## <a name="recommended-documents"></a>권장되는 문서

* [Azure Automation에서 Runbook 시작](https://docs.microsoft.com/azure/automation/automation-starting-a-runbook)
* [Azure Automation에서 Runbook 실행](https://docs.microsoft.com/azure/automation/automation-runbook-execution)

## <a name="next-steps"></a>다음 단계

문제가 표시되지 않거나 문제를 해결할 수 없는 경우 다음 채널 중 하나를 방문하여 추가 지원을 받으세요.

* Azure 포럼을 통해 Azure 전문가의 답변을 얻을 [수 있습니다.](https://azure.microsoft.com/support/forums/)
* 연결 [@AzureSupport](https://twitter.com/azuresupport) - Azure 커뮤니티를 올바른 리소스(답변, 지원 및 전문가)에 연결하여 고객 경험을 개선하기 위한 공식 Microsoft Azure 계정과 연결합니다.
* 추가 지원이 필요한 경우, Azure 기술 지원 인시던트를 제출할 수 있습니다. [Azure 지원 사이트로](https://azure.microsoft.com/support/options/) 이동하여 **지원 받기를**선택합니다.
