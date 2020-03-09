---
title: Azure Automation의 내 첫 번째 PowerShell Runbook
description: 자습서는 간단한 PowerShell Runbook의 생성, 테스트, 게시를 단계별로 안내합니다.
keywords: azure powershell, powershell 스크립트 자습서, powershell 자동화
services: automation
ms.subservice: process-automation
ms.date: 11/27/2018
ms.topic: conceptual
ms.openlocfilehash: b9808ddc3b61b0055642c5a0f2a82b0dc7553b33
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/05/2020
ms.locfileid: "78384851"
---
# <a name="my-first-powershell-runbook"></a>내 첫 번째 PowerShell Runbook

> [!div class="op_single_selector"]
> * [그래픽](automation-first-runbook-graphical.md)
> * [PowerShell](automation-first-runbook-textual-powershell.md)
> * [PowerShell 워크플로](automation-first-runbook-textual.md)
> * [Python](automation-first-runbook-textual-python2.md)

이 자습서는 Azure Automation에서 [PowerShell runbook](automation-runbook-types.md#powershell-runbooks)을 만드는 과정을 안내합니다. Runbook 작업의 상태를 추적 하는 방법을 배우면 서 테스트 하 고 게시할 수 있는 간단한 runbook으로 시작 합니다. 그런 다음 실제로 Azure 리소스를 관리 하도록 runbook을 수정 합니다 .이 경우에는 Azure 가상 컴퓨터를 시작 합니다. Runbook 매개 변수를 추가 하 여 runbook을 더욱 강력 하 게 만들려면 자습서를 완료 합니다.

>[!NOTE]
>이 문서는 새 Azure PowerShell Az 모듈을 사용하도록 업데이트되었습니다. AzureRM 모듈은 적어도 2020년 12월까지 버그 수정을 수신할 예정이므로 계속 사용하셔도 됩니다. 새 Az 모듈 및 AzureRM 호환성에 대한 자세한 내용은 [새 Azure PowerShell Az 모듈 소개](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0)를 참조하세요. Hybrid Runbook Worker에 대 한 Az module 설치 지침은 [Azure PowerShell 모듈 설치](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)를 참조 하세요. Automation 계정의 경우 [Azure Automation에서 Azure PowerShell 모듈을 업데이트 하는 방법을](automation-update-azure-modules.md)사용 하 여 모듈을 최신 버전으로 업데이트할 수 있습니다.

## <a name="prerequisites"></a>필수 조건

이 자습서를 완료하려면 다음이 필요합니다.

* 동작합니다. 구독이 아직 없는 경우 [MSDN 구독자 혜택을 활성화](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)하거나 [무료 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 등록할 수 있습니다.
* [Automation 계정](automation-quickstart-create-account.md) . 이 계정은 가상 머신을 시작하고 중지할 수 있는 권한이 있어야 합니다.
* Azure 가상 머신. 이 컴퓨터를 중지 하 고 시작 했으므로 프로덕션 VM이 아니어야 합니다.
* 필요한 경우 사용 하는 cmdlet에 따라 [Azure 모듈](shared-resources/modules.md) 을 가져오거나 [모듈을 업데이트](automation-update-azure-modules.md) 합니다.

## <a name="differences-from-powershell-workflow-runbooks"></a>PowerShell 워크플로 runbook과의 차이점

Powershell runbook에는 PowerShell 워크플로 runbook과 동일한 수명 주기, 기능 및 관리가 있습니다. 그러나 몇 가지 차이점과 제한 사항이 있습니다.

* Powershell runbook은 컴파일 단계를 사용 하지 않으므로 PowerShell 워크플로 runbook에 비해 빠르게 실행 됩니다.
* Powershell 통합 문서는 PowerShell 워크플로 runbook에서 사용 되는 검사점을 지원 하지 않습니다. PowerShell 워크플로 runbook은 검사점을 사용 하 여 어떤 지점에서 든 작업을 다시 시작할 수 있습니다. PowerShell runbook은 처음부터 작업을 다시 시작할 수 있습니다.
* PowerShell Runbook은 명령을 직렬로만 실행할 수 있습니다. PowerShell 워크플로 runbook은 직렬 및 병렬 실행을 모두 지원 합니다.
* PowerShell runbook의 경우 스크립트의 모든 항목은 단일 runspace에서 실행 됩니다. PowerShell 워크플로 Runbook의 활동, 명령 또는 스크립트 블록에는 자체 Runspace가 있을 수 있습니다. 
* PowerShell runbook에는 PowerShell 워크플로 runbook과 몇 가지 [구문상 차이점이](https://technet.microsoft.com/magazine/dn151046.aspx) 있습니다.

## <a name="step-1---create-runbook"></a>1단계 - Runbook 만들기

먼저 **Hello World**라는 텍스트를 출력하는 간단한 Runbook을 만듭니다.

1. Azure Portal에서 Automation 계정을 엽니다.
2. **프로세스 자동화** 아래에서 **runbook** 을 선택 하 여 runbook 목록을 엽니다.
3. **Runbook 만들기**를 선택 하 여 새 runbook을 만듭니다.
4. Runbook 이름을 **MyFirstRunbook-PowerShell**로 지정합니다.
5. 이 경우 [PowerShell runbook](automation-runbook-types.md#powershell-runbooks)을 만듭니다. **Runbook 형식**으로 **Powershell** 을 선택 합니다.
6. **만들기** 를 클릭하여 Runbook을 만들고 그래픽 편집기를 엽니다.

## <a name="step-2---add-code-to-the-runbook"></a>2단계 - Runbook에 코드 추가

runbook에 직접 코드를 입력하거나 라이브러리 컨트롤에서 cmdlet, Runbook 및 자산을 선택한 후 관련 매개 변수와 함께 Runbook에 추가되도록 할 수 있습니다. 이 자습서에서는 runbook에 직접 코드를 입력 합니다.

1. Runbook이 현재 비어 있습니다. 스크립트 본문에 `Write-Output "Hello World"`을 입력 합니다.

   ![Hello World](media/automation-first-runbook-textual-powershell/automation-helloworld.png)

2. **저장**을 클릭하여 Runbook을 저장합니다.

## <a name="step-3---test-the-runbook"></a> 3 단계-runbook 테스트

프로덕션 환경에서 사용할 수 있도록 runbook을 게시 하기 전에 테스트 하 여 제대로 작동 하는지 확인 해야 합니다. Runbook 테스트는 초안 버전을 실행 하 고 해당 출력을 대화형으로 볼 수 있도록 합니다.

1. **테스트 창** 을 클릭하여 테스트 창을 엽니다.
2. **시작** 을 클릭하여 테스트를 시작합니다. 유일하게 사용 가능한 옵션이어야 합니다.
3. [Runbook 작업이](automation-runbook-execution.md) 만들어지고 해당 상태가 창에 표시 됩니다.

   작업 상태는 큐에 **대기**중으로 시작 되며, 작업에서 클라우드의 runbook worker를 사용할 수 있을 때까지 기다리고 있음을 나타냅니다. 작업 자가 작업을 클레임 할 때 상태가 **시작 중** 으로 변경 됩니다. 마지막으로 runbook이 실제로 실행 되기 시작 하면 상태가 **실행 중** 으로 바뀝니다.

4. Runbook 작업이 완료 되 면 테스트 창에 해당 출력이 표시 됩니다. 이 경우에 **Hello World**가 표시됩니다.

   ![테스트 창 출력](media/automation-first-runbook-textual-powershell/automation-testpane-output.png)

5. 캔버스로 돌아가려면 테스트 창을 닫습니다.

## <a name="step-4---publish-and-start-the-runbook"></a>4 단계 - runbook 게시 및 시작

만든 runbook은 아직 초안 모드입니다. 프로덕션 환경에서 실행하기 전에 먼저 게시해야 합니다. Runbook을 게시하면 초안 버전으로 기존의 게시된 버전을 덮어씁니다. 이 경우에 Runbook을 방금 만들었으므로 아직 게시된 버전이 없습니다.

1. **게시**를 클릭하여 Runbook을 게시한 다음 확인 메시지가 표시되면 **예**를 클릭합니다.
1. 왼쪽으로 스크롤하여 runbook 페이지에서 runbook을 확인 하 고 **제작 상태** 값이 **게시**됨으로 설정 되어 있는지 확인 합니다.
1. 다시 오른쪽으로 스크롤하면 **MyFirstRunbook-PowerShell**창이 표시됩니다.
   
   위쪽의 옵션을 사용 하 여 runbook을 시작 하거나, 이후 시작 시간을 예약 하거나, HTTP 호출을 통해 runbook을 시작할 수 있도록 [webhook](automation-webhooks.md) 를 만들 수 있습니다.
1. **시작**을 선택하고 Runbook을 시작할지 확인하는 메시지가 표시되면 **예**를 선택합니다. 
1. 만든 runbook 작업에 대 한 작업 창이 열립니다. 이 창을 닫을 수 있지만 작업 진행 상황을 볼 수 있도록 지금 열기를 그대로 둡니다. 작업 상태는 **작업 요약**에 표시 되 고, 가능한 상태는 runbook 테스트에 설명 된 대로 표시 됩니다.

   ![작업 요약](media/automation-first-runbook-textual-powershell/job-pane-status-blade-jobsummary.png)

1. Runbook 상태가 **완료 됨**으로 표시 되 면 **출력** 을 클릭 하 여 표시 된 **Hello World** 볼 수 있는 출력 페이지를 엽니다.

   ![작업 출력](media/automation-first-runbook-textual-powershell/job-pane-status-blade-outputtile.png)

1. 출력 페이지를 닫습니다.
1. **모든 로그** 를 클릭하여 Runbook 작업에 대한 스트림 창을 엽니다. 출력 스트림에 **Hello World** 만 표시 되어야 합니다.

    Runbook에서 기록 하는 경우 스트림 창에는 자세한 정보 및 오류 스트림과 같은 runbook 작업에 대 한 다른 스트림이 표시 될 수 있습니다.

   ![모든 로그](media/automation-first-runbook-textual-powershell/job-pane-status-blade-alllogstile.png)

1. **MyFirstRunbook-PowerShell** 페이지로 돌아가려면 스트림 창 및 작업 창을 닫습니다.
1. **세부 정보**에서 **작업** 을 클릭 하 여이 Runbook에 대 한 작업 페이지를 엽니다. 이 페이지에는 runbook에서 만든 모든 작업이 나열 됩니다. 작업을 한 번만 실행 했으므로 하나의 작업만 표시 됩니다.

   ![작업 목록](media/automation-first-runbook-textual-powershell/runbook-control-job-tile.png)

1. 작업 이름을 클릭 하 여 runbook을 시작할 때 표시 된 것과 동일한 작업 창을 엽니다. 이 창을 사용 하 여 runbook에 대해 생성 된 작업의 세부 정보를 볼 수 있습니다.

## <a name="step-5---add-authentication-to-manage-azure-resources"></a>5 단계-Azure 리소스를 관리 인증 추가

지금까지 Runbook을 테스트 하고 게시했지만, 딱히 유용하지는 않습니다. Azure 리소스를 관리하려고 합니다. 이렇게 하려면 runbook에서 Automation 계정을 만들 때 자동으로 생성 된 실행 계정을 사용 하 여 인증할 수 있어야 합니다.

아래 예제와 같이 [AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-3.5.0) cmdlet을 사용 하 여 실행 연결을 설정 합니다. 여러 구독에서 리소스를 관리 하는 경우 *AzContext* 매개 변수를 [AzContext](https://docs.microsoft.com/powershell/module/Az.Accounts/Get-AzContext?view=azps-3.5.0)와 함께 사용 해야 합니다.

> [!NOTE]
> PowerShell runbook의 경우 **AzAccount** 및 **connect-azurermaccount** 는 **AzAccount**에 대 한 별칭입니다. 이러한 cmdlet을 사용 하거나 Automation 계정의 모듈을 최신 버전으로 [업데이트할](automation-update-azure-modules.md) 수 있습니다. 새 Automation 계정을 방금 만든 경우에도 모듈을 업데이트 해야 할 수 있습니다.

   ```powershell
   # Ensures you do not inherit an AzContext in your runbook
   Disable-AzContextAutosave –Scope Process

   $connection = Get-AutomationConnection -Name AzureRunAsConnection

   # Wrap authentication in retry logic for transient network failures
   $logonAttempt = 0
   while(!($connectionResult) -And ($logonAttempt -le 10))
   {
       $LogonAttempt++
       # Logging in to Azure...
       $connectionResult =    Connect-AzAccount `
                                  -ServicePrincipal `
                                  -Tenant $connection.TenantID `
                                  -ApplicationId $connection.ApplicationID `
                                  -CertificateThumbprint $connection.CertificateThumbprint

       Start-Sleep -Seconds 30
   }

   $AzureContext = Get-AzSubscription -SubscriptionId $connection.SubscriptionID

   Get-AzVM -ResourceGroupName myResourceGroup -AzContext $AzureContext
   ```
1. **MyFirstRunbook-PowerShell** 페이지에서 **편집** 을 클릭 하 여 텍스트 편집기를 엽니다.
1. **쓰기 출력** 줄이 더 이상 필요 하지 않습니다. 계속 해 서 삭제 합니다.
1. Automation 실행 계정을 사용 하 여 인증을 처리 하는 다음 코드를 입력 하거나 복사 하 여 붙여넣습니다.

   ```powershell
   # Ensures you do not inherit an AzContext in your runbook
   Disable-AzContextAutosave –Scope Process

   $connection = Get-AutomationConnection -Name AzureRunAsConnection

   while(!($connectionResult) -And ($logonAttempt -le 10))
   {
       $LogonAttempt++
       # Logging in to Azure...
       $connectionResult =    Connect-AzAccount `
                                  -ServicePrincipal `
                                  -Tenant $connection.TenantID `
                                  -ApplicationId $connection.ApplicationID `
                                  -CertificateThumbprint $connection.CertificateThumbprint

       Start-Sleep -Seconds 30
   }
   ```

1. Runbook을 테스트할 수 있도록 **테스트 창**을 클릭합니다.
1. **시작** 을 클릭하여 테스트를 시작합니다. 완료 되 면 다음과 유사한 출력이 표시 됩니다. 계정에서 기본 정보를 표시 합니다. 이 출력에서는 실행 계정이 유효한지 확인합니다.

   ![인증](media/automation-first-runbook-textual-powershell/runbook-auth-output.png)

## <a name="step-6---add-code-to-start-a-virtual-machine"></a>6단계 - 가상 머신을 시작하기 위한 코드 추가

이제 Runbook에서 Azure 구독을 인증하므로 리소스를 관리할 수 있습니다. 가상 머신을 시작 하는 명령을 추가 해 보겠습니다. Azure 구독에서 가상 컴퓨터를 선택 하 고, 지금은 runbook에서 해당 이름을 하드 코딩 할 수 있습니다.

1. Runbook 스크립트에 [new-azvm](https://docs.microsoft.com/powershell/module/Az.Compute/Start-AzVM?view=azps-3.5.0) cmdlet을 추가 하 여 가상 컴퓨터를 시작 합니다. 아래와 같이 cmdlet은 이름이 **VMName** 이 고 이름이 **ResourceGroupName**인 리소스 그룹을 사용 하 여 가상 컴퓨터를 시작 합니다.

   ```powershell
   # Ensures you do not inherit an AzContext in your runbook
   Disable-AzContextAutosave –Scope Process

   $connection = Get-AutomationConnection -Name AzureRunAsConnection
   while(!($connectionResult) -And ($logonAttempt -le 10))
   {
       $LogonAttempt++
       # Logging in to Azure...
       $connectionResult =    Connect-AzAccount `
                                  -ServicePrincipal `
                                  -Tenant $connection.TenantID `
                                  -ApplicationId $connection.ApplicationID `
                                  -CertificateThumbprint $connection.CertificateThumbprint

       Start-Sleep -Seconds 30
   }

   Start-AzVM -Name 'VMName' -ResourceGroupName 'ResourceGroupName'
   ```

1. Runbook을 저장한 다음, 테스트할 수 있도록 **테스트 창**을 클릭합니다.
1. **시작** 을 클릭 하 여 테스트를 시작 합니다. 완료 되 면 가상 머신이 시작 되었는지 확인 합니다.

## <a name="step-7---add-an-input-parameter"></a>7 단계-입력 매개 변수 추가

Runbook은 현재 runbook에 하드 코딩 된 가상 머신을 시작 합니다. Runbook은 runbook이 시작 될 때 가상 컴퓨터를 지정 하는 경우에 더 유용 합니다. Runbook에 입력 매개 변수를 추가 하 여 해당 기능을 제공 해 보겠습니다.

1. 텍스트 편집기에서 *VMName* 및 *ResourceGroupName*매개 변수에 대 한 변수를 사용 하도록 **new-azvm** cmdlet을 수정 합니다. 

   ```powershell
   Param(
    [string]$VMName,
    [string]$ResourceGroupName
   )
   # Ensures you do not inherit an AzContext in your runbook
   Disable-AzContextAutosave –Scope Process

   $connection = Get-AutomationConnection -Name AzureRunAsConnection
   while(!($connectionResult) -And ($logonAttempt -le 10))
   {
       $LogonAttempt++
       # Logging in to Azure...
       $connectionResult =    Connect-AzAccount `
                                  -ServicePrincipal `
                                  -Tenant $connection.TenantID `
                                  -ApplicationId $connection.ApplicationID `
                                  -CertificateThumbprint $connection.CertificateThumbprint

       Start-Sleep -Seconds 30
   }

   Start-AzVM -Name $VMName -ResourceGroupName $ResourceGroupName
   ```

1. Runbook을 저장하고 테스트 창을 엽니다. 이제 테스트에서 사용하는 두 입력 변수에 대한 값을 제공할 수 있습니다.
1. 창을 닫습니다.
1. **게시** 를 클릭하여 Runbook의 새 버전을 게시합니다.
1. 이전에 시작한 가상 컴퓨터를 중지 합니다.
1. **시작** 을 클릭하여 runbook을 시작합니다. 
1. 시작할 가상 컴퓨터에 대 한 **VMNAME** 및 **RESOURCEGROUPNAME** 값을 입력 한 다음 **확인**을 클릭 합니다.<br><br> ![매개 변수 전달](media/automation-first-runbook-textual-powershell/automation-pass-params.png)<br>
1. Runbook이 완료 되 면 가상 컴퓨터가 시작 되었는지 확인 합니다.

## <a name="next-steps"></a>다음 단계

* 언어 참조 및 학습 모듈을 비롯 한 PowerShell에 대 한 자세한 내용은 [Powershell 문서](/powershell/scripting/overview)를 참조 하세요.
* 그래픽 runbook을 시작 하려면 [내 첫 번째 그래픽 runbook](automation-first-runbook-graphical.md)을 참조 하세요.
* PowerShell 워크플로 Runbook을 시작하려면 [내 첫 번째 PowerShell 워크플로 Runbook](automation-first-runbook-textual.md)을 참조하세요.
* Runbook 형식 및 해당 장점 및 제한 사항에 대 한 자세한 내용은 [runbook types Azure Automation](automation-runbook-types.md)를 참조 하세요.
* PowerShell 스크립트 지원 기능에 대 한 자세한 내용은 [Azure Automation의 네이티브 PowerShell 스크립트 지원](https://azure.microsoft.com/blog/announcing-powershell-script-support-azure-automation-2/)을 참조 하세요.
