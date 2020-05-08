---
title: Azure Automation runbook 오류 문제 해결
description: Runbook Azure Automation에서 발생할 수 있는 문제를 해결 하 고 해결 하는 방법을 알아봅니다.
services: automation
author: mgoedtel
ms.author: magoedte
ms.date: 01/24/2019
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.custom: has-adal-ref
ms.openlocfilehash: 08325c8163073c083e927f84fecbde9a9d104572
ms.sourcegitcommit: d662eda7c8eec2a5e131935d16c80f1cf298cb6b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/01/2020
ms.locfileid: "82652789"
---
# <a name="troubleshoot-runbook-errors"></a>Runbook 오류 문제 해결

 이 문서에서는 발생할 수 있는 다양 한 runbook 오류와이를 해결 하는 방법을 설명 합니다.

>[!NOTE]
>이 문서는 새 Azure PowerShell Az 모듈을 사용하도록 업데이트되었습니다. AzureRM 모듈은 적어도 2020년 12월까지 버그 수정을 수신할 예정이므로 계속 사용하셔도 됩니다. 새 Az 모듈 및 AzureRM 호환성에 대한 자세한 내용은 [새 Azure PowerShell Az 모듈 소개](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0)를 참조하세요. Hybrid Runbook Worker에 대 한 Az module 설치 지침은 [Azure PowerShell 모듈 설치](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)를 참조 하세요. Azure Automation 계정의 경우 [Azure Automation에서 Azure PowerShell 모듈을 업데이트 하는 방법을](../automation-update-azure-modules.md)사용 하 여 모듈을 최신 버전으로 업데이트할 수 있습니다.

## <a name="diagnose-runbook-issues"></a>Runbook 문제 진단

Azure Automation에서 runbook을 실행 하는 동안 오류가 발생 하면 다음 단계를 사용 하 여 문제를 진단 하는 데 도움을 받을 수 있습니다.

1. Runbook 스크립트가 로컬 컴퓨터에서 성공적으로 실행 되는지 확인 합니다.

    언어 참조 및 학습 모듈은 [PowerShell 문서](/powershell/scripting/overview) 또는 [Python 문서](https://docs.python.org/3/)를 참조 하세요. 스크립트를 로컬로 실행 하면 다음과 같은 일반적인 오류를 검색 하 고 해결할 수 있습니다.

      * 누락 된 모듈
      * 구문 오류
      * 논리 오류

1. Runbook [오류 스트림을](../automation-runbook-output-and-messages.md#runbook-output)조사 합니다.

    이러한 스트림에서 특정 메시지를 확인 하 고이 문서에서 설명 하는 오류와 비교 합니다.

1. 노드 및 자동화 작업 영역에 필요한 모듈이 있는지 확인 합니다.

    Runbook이 모듈을 가져오는 경우 모듈 [가져오기](../shared-resources/modules.md#import-modules)의 단계를 사용 하 여 Automation 계정에 사용할 수 있는지 확인 합니다. [Azure Automation에서 Azure PowerShell 모듈 업데이트](../automation-update-azure-modules.md)의 지침에 따라 PowerShell 모듈을 최신 버전으로 업데이트 합니다. 문제 해결에 대 한 자세한 내용은 [모듈 문제 해결](shared-resources.md#modules)을 참조 하세요.

1. Runbook이 일시 중단 되거나 예기치 않게 실패 하는 경우:

    * 실행 계정이 만료 된 경우 [인증서를 갱신](../manage-runas-account.md#cert-renewal) 합니다.
    * 만료 된 webhook를 사용 하 여 runbook을 시작 하려는 경우 [webhook를 갱신](../automation-webhooks.md#renew-a-webhook) 합니다.
    * [작업 상태를 확인](../automation-runbook-execution.md#job-statuses) 하 여 현재 runbook 상태와 문제의 가능한 원인을 확인 합니다.
    * Runbook에 [추가 출력을 추가](../automation-runbook-output-and-messages.md#message-streams) 하 여 runbook이 일시 중단 되기 전에 수행 되는 작업을 식별 합니다.
    * 작업에서 throw 되는 [모든 예외를 처리](../automation-runbook-execution.md#handling-exceptions) 합니다.

1. Runbook 작업 또는 Hybrid Runbook Worker의 환경이 응답 하지 않는 경우이 단계를 수행 합니다.

    Azure Automation 대신 Hybrid Runbook Worker에서 runbook을 실행 하는 경우 [Hybrid Worker 자체의 문제를 해결](https://docs.microsoft.com/azure/automation/troubleshoot/hybrid-runbook-worker)해야 할 수 있습니다.

## <a name="scenario-runbook-fails-with-a-no-permission-or-forbidden-403-error"></a><a name="runbook-fails-no-permission"></a>시나리오: Runbook이 권한 없음 또는 금지 403 오류와 함께 실패 합니다.

### <a name="issue"></a>문제

Runbook이 실패 하거나, 금지 된 403 오류 또는 이와 동등한 권한으로 실패 합니다.

### <a name="cause"></a>원인

실행 계정에는 현재 Automation 계정으로 Azure 리소스에 대 한 사용 권한이 없을 수 있습니다. 

### <a name="resolution"></a>해결 방법

실행 계정에 스크립트에서 사용 되는 [리소스에 대 한 액세스 권한이](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal) 있는지 확인 합니다.

## <a name="scenario-sign-in-to-azure-account-failed"></a><a name="sign-in-failed"></a>시나리오: Azure 계정에 로그인 하지 못했습니다.

### <a name="issue"></a>문제

`Connect-AzAccount` Cmdlet으로 작업할 때 다음 오류 중 하나가 표시 됩니다.

```error
Unknown_user_type: Unknown User Type
```

```error
No certificate was found in the certificate store with thumbprint
```

### <a name="cause"></a>원인

이러한 오류는 자격 증명 자산 이름이 유효 하지 않은 경우에 발생 합니다. Automation 자격 증명 자산을 설정 하는 데 사용한 사용자 이름과 암호가 유효 하지 않은 경우에도 발생할 수 있습니다.

### <a name="resolution"></a>해결 방법

무엇이 잘못 되었는지 확인 하려면 다음 단계를 수행 합니다.

1. 특수 문자를 포함 하지 않는지 확인 합니다. 이러한 문자로 `\@` 문자가 Azure에 연결하는 데 사용하는 Automation 자격 증명 자산 이름에 포함됩니다.
1. 로컬 PowerShell ISE 편집기에서 Azure Automation 자격 증명에 저장 된 사용자 이름 및 암호를 사용할 수 있는지 확인 합니다. PowerShell ISE에서 다음 cmdlet을 실행 합니다.

   ```powershell
   $Cred = Get-Credential
   #Using Azure Service Management
   Add-AzureAccount –Credential $Cred
   #Using Azure Resource Manager
   Connect-AzAccount –Credential $Cred
   ```

1. 인증이 로컬에서 실패 하면 Azure Active Directory (Azure AD) 자격 증명을 올바르게 설정 하지 않은 것입니다. Azure AD 계정을 올바르게 설정 하려면 블로그 게시물 [Azure Active Directory를 사용 하 여 azure에 인증](https://azure.microsoft.com/blog/azure-automation-authenticating-to-azure-using-azure-active-directory/)을 참조 하세요.

1. 오류가 일시적인 것으로 나타나는 경우 인증 루틴에 재시도 논리를 추가 하 여 인증을 더욱 강력 하 게 합니다.

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

## <a name="scenario-run-login-azurermaccount-to-log-in"></a><a name="login-azurerm"></a>시나리오: 로그인을 실행 하 여 로그인 Connect-azurermaccount

### <a name="issue"></a>문제

Runbook을 실행할 때 다음과 같은 오류가 표시 됩니다.

```error
Run Login-AzureRMAccount to login.
```

### <a name="cause"></a>원인

실행 계정을 사용 하 고 있지 않거나 실행 계정이 만료 된 경우이 오류가 발생할 수 있습니다. 자세한 내용은 [Azure Automation 실행 계정 관리](https://docs.microsoft.com/azure/automation/manage-runas-account)를 참조 하세요.

이 오류에는 두 가지 주요 원인이 있습니다.

* AzureRM 또는 Az module의 서로 다른 버전이 있습니다.
* 별도의 구독에 있는 리소스에 액세스 하려고 합니다.

### <a name="resolution"></a>해결 방법

AzureRM 또는 Az module 중 하나를 업데이트 한 후이 오류가 표시 되 면 모든 모듈을 동일한 버전으로 업데이트 합니다.

다른 구독에 있는 리소스에 액세스 하려는 경우 다음 단계를 수행 하 여 사용 권한을 구성 합니다.

1. Automation 실행 계정으로 이동한 후 **응용 프로그램 ID** 및 **지문을**복사 합니다.

    ![응용 프로그램 ID 및 지문 복사](../media/troubleshoot-runbooks/collect-app-id.png)

1. Automation 계정이 호스팅되지 *않은* 구독의 **액세스 제어** 로 이동 하 여 새 역할 할당을 추가 합니다.

    ![Access Control](../media/troubleshoot-runbooks/access-control.png)

1. 이전에 수집 된 **응용 프로그램 ID** 를 추가 합니다. **참가자** 권한을 선택 합니다.

    ![역할 할당 추가](../media/troubleshoot-runbooks/add-role-assignment.png)

1. 구독 이름을 복사 합니다.

1. 이제 다음 runbook 코드를 사용 하 여 Automation 계정의 사용 권한을 다른 구독으로 테스트할 수 있습니다. 을 `"\<CertificateThumbprint\>"` 1 단계에서 복사한 값으로 바꿉니다. 을 `"\<SubscriptionName\>"` 4 단계에서 복사한 값으로 바꿉니다.

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

`Select-AzureSubscription`, `Select-AzureRMSubscription`또는 `Select-AzSubscription` cmdlet으로 작업할 때 다음과 같은 오류가 표시 됩니다.

```error
The subscription named <subscription name> cannot be found.
```

### <a name="error"></a>Error

이 오류는 다음과 같은 경우에 발생할 수 있습니다.

* 구독 이름이 잘못 되었습니다.
* 구독 세부 정보를 가져오려는 Azure AD 사용자가 구독의 관리자로 구성 되지 않았습니다.
* Cmdlet을 사용할 수 없습니다.

### <a name="resolution"></a>해결 방법

Azure에 인증 하 고 선택 하려는 구독에 대 한 액세스 권한이 있는지 확인 하려면 다음 단계를 수행 합니다.

1. 스크립트가 독립 실행형으로 작동 하는지 확인 하려면 Azure Automation 외부에서 테스트 합니다.
1. Cmdlet을 실행 하기 전에 스크립트가 AzAccount cmdlet을 실행 하는지 확인 합니다. [Connect-AzAccount](https://docs.microsoft.com/powershell/module/Az.Accounts/Connect-AzAccount?view=azps-3.7.0) `Select-*`
1. Runbook의 시작 부분에 `Disable-AzContextAutosave –Scope Process`를 추가합니다. 이 cmdlet은 모든 자격 증명이 현재 Runbook의 실행에만 적용되도록 합니다.
1. 오류 메시지가 계속 표시 되는 경우에 대 한 `AzContext` `Connect-AzAccount`매개 변수를 추가 하 여 코드를 수정 하 고 코드를 실행 합니다.

   ```powershell
   Disable-AzContextAutosave –Scope Process

   $Conn = Get-AutomationConnection -Name AzureRunAsConnection
   Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint

   $context = Get-AzContext

   Get-AzVM -ResourceGroupName myResourceGroup -AzContext $context
    ```

## <a name="scenario-runbooks-fail-when-dealing-with-multiple-subscriptions"></a><a name="runbook-auth-failure"></a>시나리오: 여러 구독을 처리할 때 Runbook이 실패 함

### <a name="issue"></a>문제

Runbook을 실행할 때 runbook은 Azure 리소스를 관리 하지 못합니다.

### <a name="cause"></a>원인

Runbook이 실행 시 올바른 컨텍스트를 사용하지 않습니다.

### <a name="resolution"></a>해결 방법

Runbook이 여러 runbook을 호출 하면 구독 컨텍스트가 손실 될 수 있습니다. 구독 컨텍스트가 runbook에 전달 되도록 하려면 클라이언트 runbook이 `Start-AzureRmAutomationRunbook` `AzureRmContext` 매개 변수의 cmdlet에 컨텍스트를 전달 하도록 합니다. 로 `Process` 설정 `Disable-AzureRmContextAutosave` 된 `Scope` 매개 변수와 함께 cmdlet을 사용 하 여 지정 된 자격 증명이 현재 runbook에만 사용 되도록 합니다. 자세한 내용은 [여러 구독 작업](../automation-runbook-execution.md#working-with-multiple-subscriptions)을 참조 하세요.

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

## <a name="scenario-authentication-to-azure-fails-because-multifactor-authentication-is-enabled"></a><a name="auth-failed-mfa"></a>시나리오: 다단계 인증을 사용 하도록 설정 되어 있으므로 Azure에 대 한 인증이 실패 합니다.

### <a name="issue"></a>문제

Azure 사용자 이름 및 암호를 사용 하 여 Azure에 인증 하는 경우 다음과 같은 오류가 표시 됩니다.

```error
Add-AzureAccount: AADSTS50079: Strong authentication enrollment (proof-up) is required
```

### <a name="cause"></a>원인

Azure 계정에 다단계 인증을 사용 하는 경우 Azure Active Directory 사용자를 사용 하 여 Azure에 인증할 수 없습니다. 대신 인증서 또는 서비스 주체를 사용 하 여 인증 해야 합니다.

### <a name="resolution"></a>해결 방법

Azure 클래식 배포 모델 cmdlet에 인증서를 사용 하려면 [azure 서비스를 관리 하기 위해 인증서 만들기 및 추가](https://blogs.technet.com/b/orchestrator/archive/2014/04/11/managing-azure-services-with-the-microsoft-azure-automation-preview-service.aspx)를 참조 하세요. Azure Resource Manager cmdlet에서 서비스 주체를 사용 하려면 Azure Portal를 [사용 하 여 서비스 주체 만들기](../../active-directory/develop/howto-create-service-principal-portal.md) 및 [Azure Resource Manager를](../../active-directory/develop/howto-authenticate-service-principal-powershell.md)사용 하 여 서비스 주체 인증을 참조 하세요.

## <a name="scenario-runbook-fails-with-a-task-was-canceled-error-message"></a><a name="task-was-cancelled"></a>시나리오: "작업이 취소 되었습니다." 오류 메시지와 함께 Runbook이 실패 합니다.

### <a name="issue"></a>문제

다음 예제와 비슷한 오류가 발생하여 Runbook이 실패합니다.

```error
Exception: A task was canceled.
```

### <a name="cause"></a>원인

이 오류는 오래된 Azure 모듈을 사용하여 발생할 수 있습니다.

### <a name="resolution"></a>해결 방법

Azure 모듈을 최신 버전으로 업데이트 하 여이 오류를 해결할 수 있습니다.

1. Automation 계정에서 **모듈**을 선택 하 고 **Azure 모듈 업데이트**를 선택 합니다.
1. 업데이트는 약 15 분이 걸립니다. 완료 되 면 실패 한 runbook을 다시 실행 합니다.

모듈 업데이트에 대해 자세히 알아 보려면 [Azure Automation에서 Azure 모듈 업데이트](../automation-update-azure-modules.md)를 참조하세요.

## <a name="scenario-term-not-recognized-as-the-name-of-a-cmdlet-function-or-script"></a><a name="not-recognized-as-cmdlet"></a>시나리오: 용어가 cmdlet, 함수 또는 스크립트의 이름으로 인식 되지 않습니다.

### <a name="issue"></a>문제

다음 예제와 비슷한 오류가 발생하여 Runbook이 실패합니다.

```error
The term 'Connect-AzAccount' is not recognized as the name of a cmdlet, function, script file, or operable program. Check the spelling of the name, or if the path was included verify that the path is correct and try again.
```

### <a name="cause"></a>원인

이 오류는 다음과 같은 이유로 발생할 수 있습니다.

* Cmdlet을 포함 하는 모듈은 Automation 계정으로 가져오지 않습니다.
* Cmdlet을 포함 하는 모듈을 가져오지만 최신 상태가 아닙니다.

### <a name="resolution"></a>해결 방법

이 오류를 해결 하려면 다음 작업 중 하나를 수행 합니다.

* Azure 모듈의 경우 Automation 계정에서 모듈을 업데이트 하는 방법을 알아보려면 [Azure Automation에서 Azure PowerShell 모듈을 업데이트 하는 방법](../automation-update-azure-modules.md) 을 참조 하세요.
* 비 Azure 모듈의 경우 Automation 계정으로 모듈을 가져왔는지 확인 합니다.

## <a name="scenario-cmdlet-fails-in-pnp-powershell-runbook-on-azure-automation"></a>시나리오: Azure Automation의 PnP PowerShell runbook에서 Cmdlet이 실패 함

### <a name="issue"></a>문제

Runbook이 PnP PowerShell 생성 개체를 Azure Automation 출력에 직접 기록 하는 경우 cmdlet 출력은 자동화로 다시 스트리밍할 수 없습니다.

### <a name="cause"></a>원인

이 문제는 반환 개체를 catch 하지 않고와 같이 PnP PowerShell cmdlet을 `add-pnplistitem`호출 하는 runbook을 Azure Automation 처리할 때 가장 일반적으로 발생 합니다.

### <a name="resolution"></a>해결 방법

Cmdlet이 표준 출력에 전체 개체를 쓰려고 시도 하지 않도록 스크립트를 편집 하 여 모든 반환 값을 변수에 할당 합니다. 스크립트는 다음과 같이 출력 스트림을 cmdlet으로 리디렉션할 수 있습니다.

```azurecli
  $null = add-pnplistitem
```

스크립트에서 cmdlet 출력을 구문 분석 하는 경우에는 스크립트에서 출력을 변수에 저장 하 고 단순히 출력을 스트리밍하는 대신 변수를 조작 해야 합니다.

```azurecli
$SomeVariable = add-pnplistitem ....
if ($SomeVariable.someproperty -eq ....
```

## <a name="scenario-cmdlet-not-recognized-when-executing-a-runbook"></a><a name="cmdlet-not-recognized"></a>시나리오: Runbook을 실행해도 cmdlet이 인식되지 않음

### <a name="issue"></a>문제

다음 오류로 인해 Runbook 작업이 실패합니다.

```error
<cmdlet name>: The term <cmdlet name> is not recognized as the name of a cmdlet, function, script file, or operable program.
```

### <a name="cause"></a>원인

PowerShell 엔진을 통해 Runbook에서 사용하는 cmdlet을 찾을 수 없는 경우에 이 오류가 발생합니다. Cmdlet을 포함 하는 모듈이 계정에 없거나, 이름이 runbook 이름과 충돌 하거나, cmdlet이 다른 모듈에도 있으며, Automation에서 이름을 확인할 수 없습니다.

### <a name="resolution"></a>해결 방법

다음 해결 방법 중 하나를 사용 하 여 문제를 해결 합니다.

* Cmdlet 이름을 올바르게 입력 했는지 확인 합니다.
* Automation 계정에 cmdlet이 있는지와 충돌이 없는지 확인 합니다. Cmdlet이 있는지 확인 하려면 runbook을 편집 모드에서 열고 라이브러리에서 찾을 cmdlet을 검색 하거나를 실행 `Get-Command <CommandName>`합니다. 계정에서 cmdlet을 사용할 수 있는지 확인 하 고 다른 cmdlet 또는 runbook과 이름이 충돌 하지 않는지 확인 한 후에 cmdlet을 캔버스에 추가 합니다. Runbook에 올바른 매개 변수 집합을 사용 하 고 있는지 확인 합니다.
* 이름 충돌이 발생 하 고 cmdlet을 두 개의 다른 모듈에서 사용할 수 있는 경우 cmdlet에 대 한 정규화 된 이름을 사용 하 여 문제를 해결 합니다. 예를 들어 `ModuleName\CmdletName`를 사용할 수 있습니다.
* Hybrid worker 그룹의 온-프레미스에서 runbook을 실행 하는 경우에는 하이브리드 작업자를 호스트 하는 컴퓨터에 모듈 및 cmdlet이 설치 되어 있는지 확인 합니다.

## <a name="scenario-incorrect-object-reference-on-call-to-add-azaccount"></a><a name="object-reference-not-set"></a>시나리오: AzAccount에 대 한 호출에 대 한 개체 참조가 잘못 되었습니다.

### <a name="issue"></a>문제

`Connect-AzAccount` Cmdlet에 대 한 별칭인 `Add-AzAccount`를 사용할 때이 오류가 표시 됩니다.

```error
Add-AzAccount : Object reference not set to an instance of an object
```

### <a name="cause"></a>원인

Runbook이 Automation 계정을 추가 하기 위해를 호출 `Add-AzAccount` 하기 전에 적절 한 단계를 수행 하지 않는 경우이 오류가 발생할 수 있습니다. 필요한 단계 중 하나는 실행 계정으로 로그인 하는 것입니다. Runbook에서 사용할 올바른 작업은 [Azure Automation에서 runbook 실행](https://docs.microsoft.com/azure/automation/automation-runbook-execution)을 참조 하세요.

## <a name="scenario-object-reference-not-set-to-an-instance-of-an-object"></a><a name="child-runbook-object"></a>시나리오: 개체 참조가 개체의 인스턴스로 설정 되지 않았습니다.

### <a name="issue"></a>문제

`Wait` 매개 변수를 사용 하 여 자식 runbook을 호출 하 고 출력 스트림에 개체가 포함 되어 있는 경우 다음과 같은 오류가 표시 됩니다.

```error
Object reference not set to an instance of an object
```

### <a name="cause"></a>원인

스트림에 개체가 포함 되어 있으면는 `Start-AzAutomationRunbook` 출력 스트림을 올바르게 처리 하지 않습니다.

### <a name="resolution"></a>해결 방법

폴링 논리를 구현 하 고 [AzAutomationJobOutput](https://docs.microsoft.com/powershell/module/Az.Automation/Get-AzAutomationJobOutput?view=azps-3.7.0) cmdlet을 사용 하 여 출력을 검색 합니다. 이 논리의 샘플은 다음에 정의 되어 있습니다.

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

이 문제를 해결 하려면 다음 해결 방법 중 하나를 사용 합니다.

* 한 cmdlet에서 다른 cmdlet으로 복잡 한 개체를 파이프 하는 `InlineScript` 경우 이러한 cmdlet을 작업에 래핑합니다.
* 개체 전체를 전달하지 말고 복합 개체에서 필요한 이름 또는 값만 전달합니다.
* PowerShell 워크플로 Runbook 대신 PowerShell Runbook을 사용합니다.



## <a name="scenario-400-bad-request-status-when-calling-a-webhook"></a><a name="expired webhook"></a>시나리오: 400 webhook를 호출할 때 잘못 된 요청 상태

### <a name="issue"></a>문제

Azure Automation runbook에 대 한 webhook을 호출 하려고 하면 다음과 같은 오류가 표시 됩니다.

```error
400 Bad Request : This webhook has expired or is disabled
```

### <a name="cause"></a>원인

호출하려는 웹후크가 사용되지 않거나 만료되었습니다. 

### <a name="resolution"></a>해결 방법

웹 후크를 사용 하지 않도록 설정 된 경우 Azure Portal를 통해 다시 사용 하도록 설정할 수 있습니다. 웹 후크가 만료 된 경우 삭제 한 후 다시 만들어야 합니다. 아직 만료되지 않은 경우에만 [웹후크를 갱신](../automation-webhooks.md#renew-a-webhook)할 수 있습니다. 

## <a name="scenario-429-the-request-rate-is-currently-too-large"></a><a name="429"></a>시나리오: 429: 요청 비율이 현재 너무 큼

### <a name="issue"></a>문제

`Get-AzAutomationJobOutput` cmdlet을 실행할 때 다음 오류 메시지가 나타납니다.

```error
429: The request rate is currently too large. Please try again
```

### <a name="cause"></a>원인

이 오류는 [자세한 정보 표시 스트림이](../automation-runbook-output-and-messages.md#verbose-stream)많은 runbook에서 작업 출력을 검색 하는 경우에 발생할 수 있습니다.

### <a name="resolution"></a>해결 방법

다음 중 하나를 수행 하 여이 오류를 해결 합니다.

* Runbook을 편집하고 내보내는 작업 스트림 수를 줄입니다.
* cmdlet을 실행할 때 검색할 스트림 수를 줄입니다. 이렇게 하려면 `Stream` [AzAutomationJobOutput](https://docs.microsoft.com/powershell/module/Az.Automation/Get-AzAutomationJobOutput?view=azps-3.7.0) cmdlet에 대 한 매개 변수 값을 설정 하 여 출력 스트림도 검색 합니다. 

## <a name="scenario-runbook-job-fails-because-allocated-quota-was-exceeded"></a><a name="quota-exceeded"></a>시나리오: 할당 된 할당량을 초과 하 여 Runbook 작업이 실패 합니다.

### <a name="issue"></a>문제

다음 오류로 인해 Runbook 작업이 실패합니다.

```error
The quota for the monthly total job run time has been reached for this subscription
```

### <a name="cause"></a>원인

작업 실행 시간이 계정에 할당된 무료 500분을 초과하면 이 오류가 발생합니다. 이 할당량은 모든 유형의 작업 실행 태스크에 적용됩니다. 이러한 작업 중 일부는 작업을 테스트 하 고, 포털에서 작업을 시작 하 고, 웹 후크를 사용 하 여 작업을 실행 하거나, Azure Portal 또는 데이터 센터 중 하나를 사용 하 여 실행할 작업을 예약 하는 것입니다. Automation 가격 책정에 대 한 자세한 내용은 [automation 가격 책정](https://azure.microsoft.com/pricing/details/automation/)을 참조 하세요.

### <a name="resolution"></a>해결 방법

매월 처리를 500 분 이상 사용 하려면 무료 계층에서 기본 계층으로 구독을 변경 합니다.

1. Azure 구독에 로그인합니다.
1. 업그레이드할 Automation 계정을 선택 합니다.
1. **설정**을 선택한 다음 **가격 책정**을 선택 합니다.
1. 아래 페이지에서 **사용** 을 선택 하 여 계정을 기본 계층으로 업그레이드 합니다.

## <a name="scenario-runbook-job-start-attempted-three-times-but-fails-to-start-each-time"></a><a name="job-attempted-3-times"></a>시나리오: Runbook 작업 시작을 세 번 시도 했지만 매번 시작 하지 못함

### <a name="issue"></a>문제

다음 오류가 발생 하 여 runbook이 실패 합니다.

```error
The job was tried three times but it failed
```

### <a name="cause"></a>원인

이 오류는 다음 문제 중 하나로 인해 발생 합니다.

* **메모리 제한입니다.** 400 MB를 초과 하는 메모리를 사용 하는 경우 작업이 실패할 수 있습니다. Sandbox에 할당 된 메모리에 대 한 문서화 된 제한은 [Automation 서비스 제한](../../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits)에서 찾을 수 있습니다. 
* **네트워크 소켓.** Azure 샌드박스는 1000의 동시 네트워크 소켓으로 제한 됩니다. 자세한 내용은 [Automation 서비스 제한](../../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits)을 참조 하세요.
* **모듈이 호환 되지 않습니다.** 모듈 종속성이 올바르지 않을 수 있습니다. 이 경우 runbook은 일반적으로 또는 `Command not found` `Cannot bind parameter` 메시지를 반환 합니다.
* **샌드박스에 대 한 Active Directory 인증 안 함** Runbook이 Azure 샌드박스에서 실행 되는 실행 파일 또는 하위 프로세스를 호출 하려고 했습니다. ADAL (Azure Active Directory 인증 라이브러리)을 사용 하 여 Azure AD에서 인증 하도록 runbook을 구성 하는 것은 지원 되지 않습니다.
* **예외 데이터가 너무 많습니다.** Runbook이 출력 스트림에 너무 많은 예외 데이터를 쓰려고 했습니다.

### <a name="resolution"></a>해결 방법

* **메모리 제한, 네트워크 소켓** 메모리 제한 내에서 작업 하는 방법은 작업을 여러 runbook으로 분할 하 고, 메모리에서 더 작은 데이터를 처리 하 고, runbook에서 불필요 한 출력을 작성 하지 않도록 하 고, PowerShell 워크플로 runbook에 기록할 검사점 수를 고려 하는 것입니다. 등의 clear 메서드 `$myVar.clear`를 사용 하 여 변수를 지운 다음를 사용 `[GC]::Collect` 하 여 가비지 수집을 즉시 실행 합니다. 이러한 작업은 런타임 중에 Runbook의 메모리 공간을 줄입니다.
* **모듈이 호환 되지 않습니다.** [Azure Automation에서 Azure PowerShell 모듈을 업데이트 하는 방법](../automation-update-azure-modules.md)의 단계에 따라 Azure 모듈을 업데이트 합니다.
* **샌드박스에 대 한 Active Directory 인증 안 함** Runbook을 사용 하 여 Azure AD에 인증 하는 경우 Automation 계정에서 Azure AD 모듈을 사용할 수 있는지 확인 합니다. Runbook이 자동화 하는 작업을 수행 하는 데 필요한 권한을 실행 계정에 부여 해야 합니다.

  Runbook이 Azure 샌드박스에서 실행 되는 실행 파일 또는 하위 프로세스를 호출할 수 없는 경우 [Hybrid Runbook Worker](../automation-hrw-run-runbooks.md)에서 runbook을 사용 합니다. 하이브리드 작업자는 Azure 샌드박스에 있는 메모리 및 네트워크 제한으로 제한 되지 않습니다.

* **예외 데이터가 너무 많습니다.** 작업 출력 스트림에는 1mb 제한이 있습니다. 및 `try` `catch` 블록을 사용 하 여 runbook이 실행 파일 또는 하위 프로세스에 대 한 호출을 포함 하는지 확인 합니다. 작업이 예외를 throw 하는 경우 코드에서 예외의 메시지를 자동화 변수에 쓰도록 합니다. 이 방법은 메시지가 작업 출력 스트림에 기록 되지 않도록 합니다.

## <a name="scenario-powershell-job-fails-with-cannot-invoke-method-error-message"></a><a name="cannot-invoke-method"></a>시나리오: PowerShell 작업이 실패 하 고 "메서드를 호출할 수 없습니다." 오류 메시지가 나타납니다.

### <a name="issue"></a>문제

Azure에서 실행 되는 runbook에서 PowerShell 작업을 시작 하면 다음과 같은 오류 메시지가 표시 됩니다.

```error
Exception was thrown - Cannot invoke method. Method invocation is supported only on core types in this language mode.
```

### <a name="cause"></a>원인

이 오류는 Azure 샌드박스에서 실행 되는 runbook을 [전체 언어 모드](/powershell/module/microsoft.powershell.core/about/about_language_modes)에서 실행할 수 없음을 나타낼 수 있습니다.

### <a name="resolution"></a>해결 방법

이 오류를 해결하는 방법은 두 가지가 있습니다.

* 시작- [작업](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/start-job?view=powershell-7)을 사용 하는 대신 [AzAutomationRunbook](https://docs.microsoft.com/powershell/module/az.automation/start-azautomationrunbook?view=azps-3.7.0) 를 사용 하 여 runbook을 시작 합니다.
* Hybrid Runbook Worker에서 runbook을 실행 해 보세요.

이 동작 및 runbook Azure Automation의 다른 동작에 대 한 자세한 내용은 [Azure Automation에서 Runbook 실행](../automation-runbook-execution.md)을 참조 하세요.

## <a name="scenario-a-long-running-runbook-fails-to-complete"></a><a name="long-running-runbook"></a>시나리오: 장기 실행 runbook이 완료 되지 못함

### <a name="issue"></a>문제

Runbook이 3 시간 동안 실행 된 후 중지 됨 상태로 표시 됩니다. 또한 다음과 같은 오류가 표시 될 수 있습니다.

```error
The job was evicted and subsequently reached a Stopped state. The job cannot continue running.
```

이 동작은 Azure Automation 내의 프로세스에 대 한 [공평 한 공유](../automation-runbook-execution.md#fair-share) 모니터링으로 인해 Azure 샌드박스에서 설계 되었습니다. 프로세스가 3 시간 이상 실행 되는 경우 공평 공유는 runbook을 자동으로 중지 합니다. 공평 공유 시간 제한을 초과 하는 runbook의 상태는 runbook 유형에 따라 다릅니다. PowerShell 및 Python Runbook은 중지됨 상태로 설정되며, PowerShell 워크플로 Runbook은 실패 상태로 설정됩니다.

### <a name="cause"></a>원인

Runbook은 Azure 샌드박스에서 공평 하 게 공유에서 허용 하는 3 시간 제한을 초과 하 여 실행 되었습니다.

### <a name="resolution"></a>해결 방법

권장되는 해결 방법 중 하나는 [Hybrid Runbook Worker](../automation-hrw-run-runbooks.md)에서 Runbook을 실행하는 것입니다. Hybrid worker는 Azure 샌드박스에 있는 3 시간 공평 공유 runbook 제한에 의해 제한 되지 않습니다. 예기치 않은 로컬 인프라 문제가 있는 경우 다시 시작 동작을 지원 하도록 Hybrid Runbook Worker에서 실행 되는 runbook을 개발 해야 합니다.

또 다른 해결 방법은 [자식 runbook](../automation-child-runbooks.md)을 만들어 runbook을 최적화 하는 것입니다. Runbook이 여러 리소스에서 동일한 기능을 반복 하는 경우 (예: 여러 데이터베이스의 데이터베이스 작업에서) 함수를 자식 runbook으로 이동할 수 있습니다. 각 자식 runbook은 별도의 프로세스에서 병렬로 실행 됩니다. 이 동작은 부모 Runbook이 완료되는 총 기간을 감소시킵니다.

자식 Runbook 시나리오를 사용하도록 설정하는 PowerShell cmdlet은 다음과 같습니다.

* [AzAutomationRunbook를 실행](https://docs.microsoft.com/powershell/module/Az.Automation/Start-AzAutomationRunbook?view=azps-3.7.0)합니다. 이 cmdlet을 사용하면 Runbook을 시작하고 매개 변수를 Runbook에 전달할 수 있습니다.
* [AzAutomationJob](https://docs.microsoft.com/powershell/module/Az.Automation/Get-AzAutomationJob?view=azps-3.7.0). 자식 runbook이 완료 된 후 수행 해야 하는 작업이 있는 경우이 cmdlet을 사용 하 여 각 자식에 대 한 작업 상태를 확인할 수 있습니다.

## <a name="scenario-error-in-job-streams-about-the-get_serializationsettings-method"></a><a name="get-serializationsettings"></a>시나리오: get_SerializationSettings 메서드에 대 한 작업 스트림에 오류가 있습니다.

### <a name="issue"></a>문제

Runbook에 대 한 작업 스트림에 다음과 같은 오류가 표시 됩니다.

```error
Connect-AzAccount : Method 'get_SerializationSettings' in type
'Microsoft.Azure.Management.Internal.Resources.ResourceManagementClient' from assembly
'Microsoft.Azure.Commands.ResourceManager.Common, Version=4.0.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35'
does not have an implementation.
At line:16 char:1
+ Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID -Appl ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : NotSpecified: (:) [Connect-AzAccount], TypeLoadException
    + FullyQualifiedErrorId : System.TypeLoadException,Microsoft.Azure.Commands.Profile.ConnectAzAccountCommand
```

### <a name="cause"></a>원인

이 오류는 runbook에서 AzureRM에서 Az modules로 불완전 한 마이그레이션을 사용 하 여 발생할 수 있습니다. 이 경우 Azure Automation는 AzureRM 모듈만 사용 하 여 runbook 작업을 시작한 다음, 다른 작업을 시작 하 고,이로 인해 샌드박스가 중단 될 수 있습니다.

### <a name="resolution"></a>해결 방법

동일한 runbook에서 Az 및 AzureRM cmdlet을 사용 하지 않는 것이 좋습니다. 이러한 모듈의 올바른 사용에 대해 자세히 알아보려면 [Az modules로 마이그레이션](../shared-resources/modules.md#migrating-to-az-modules)을 참조 하세요.

## <a name="scenario-access-denied-when-using-azure-sandbox-for-runbook-or-application"></a><a name="access-denied-azure-sandbox"></a>시나리오: runbook 또는 응용 프로그램에 Azure 샌드박스를 사용 하는 경우 액세스가 거부 됨

### <a name="issue"></a>문제

Runbook 또는 응용 프로그램이 Azure 샌드박스에서 실행 되려고 하면 환경에서 액세스를 거부 합니다.

### <a name="cause"></a>원인

이 문제는 Azure 샌드박스에서 모든 out-of-process COM 서버에 대 한 액세스를 차단 하기 때문에 발생할 수 있습니다. 예를 들어 샌드박스가 적용 된 응용 프로그램 또는 runbook은 WMI(Windows Management Instrumentation) (WMI) 또는 Windows Installer 서비스 (msiserver)를 호출할 수 없습니다. 

### <a name="resolution"></a>해결 방법

Azure 샌드박스 사용에 대 한 자세한 내용은 [Azure Automation에서 Runbook 실행](../automation-runbook-execution.md#where-to-run-your-runbooks)을 참조 하세요.

## <a name="scenario-invalid-forbidden-status-code-when-using-key-vault-inside-a-runbook"></a>시나리오: runbook 내에서 Key Vault를 사용 하는 경우 잘못 된 금지 상태 코드

### <a name="issue"></a>문제

Azure Automation runbook을 통해 Azure Key Vault에 액세스 하려고 하면 다음 오류가 발생 합니다.

```error
Operation returned an invalid status code 'Forbidden'
```

### <a name="cause"></a>원인

이 문제는 다음과 같은 경우에 발생할 수 있습니다.

* 실행 계정을 사용 하지 않습니다.
* 사용 권한 부족

### <a name="resolution"></a>해결 방법

#### <a name="not-using-a-run-as-account"></a>실행 계정을 사용 하지 않음

[5 단계-인증 추가](https://docs.microsoft.com/azure/automation/automation-first-runbook-textual-powershell#add-authentication-to-manage-azure-resources) 를 수행 하 여 Azure 리소스를 관리 합니다. 그러면 실행 계정을 사용 하 여 Key Vault에 액세스할 수 있습니다.

#### <a name="insufficient-permissions"></a>권한 부족

[Key Vault에 권한을 추가](https://docs.microsoft.com/azure/automation/manage-runas-account#add-permissions-to-key-vault) 하 여 실행 계정에 Key Vault에 대 한 액세스 권한이 있는지 확인 합니다.

## <a name="recommended-documents"></a>권장되는 문서

* [Azure Automation에서 Runbook 실행](../automation-runbook-execution.md)
* [Azure Automation에서 Runbook 시작](https://docs.microsoft.com/azure/automation/automation-starting-a-runbook)

## <a name="next-steps"></a>다음 단계

여기에 문제가 표시 되지 않거나 문제를 해결할 수 없는 경우 다음 채널 중 하나를 사용해 보세요.

* Azure [포럼](https://azure.microsoft.com/support/forums/)을 통해 azure 전문가 로부터 답변을 받으세요.
* 사용자 환경을 [@AzureSupport](https://twitter.com/azuresupport)개선 하기 위한 공식 Microsoft Azure 계정인를 사용 하 여 연결 하세요. Azure 지원에서는 답변, 지원 및 전문가를 위해 Azure 커뮤니티에 연결 합니다.
* 추가 지원이 필요한 경우, Azure 기술 지원 인시던트를 제출할 수 있습니다. [Azure 지원 사이트](https://azure.microsoft.com/support/options/)로 이동 하 여 **지원 받기**를 선택 합니다.
