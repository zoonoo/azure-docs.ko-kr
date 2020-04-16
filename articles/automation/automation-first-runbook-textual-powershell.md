---
title: Azure Automation의 내 첫 번째 PowerShell Runbook
description: 자습서는 간단한 PowerShell Runbook의 생성, 테스트, 게시를 단계별로 안내합니다.
keywords: azure powershell, powershell 스크립트 자습서, powershell 자동화
services: automation
ms.subservice: process-automation
ms.date: 11/27/2018
ms.topic: conceptual
ms.openlocfilehash: 0df2680a9f17fc0af950b0ce744a655348b4cbf7
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81406059"
---
# <a name="my-first-powershell-runbook"></a>내 첫 번째 PowerShell Runbook

> [!div class="op_single_selector"]
> * [그래픽](automation-first-runbook-graphical.md)
> * [PowerShell](automation-first-runbook-textual-powershell.md)
> * [PowerShell 워크플로](automation-first-runbook-textual.md)
> * [Python](automation-first-runbook-textual-python2.md)

이 자습서는 Azure Automation에서 [PowerShell runbook](automation-runbook-types.md#powershell-runbooks)을 만드는 과정을 안내합니다. Runbook 작업의 상태를 추적하는 방법을 배우면서 테스트하고 게시할 수 있는 간단한 Runbook으로 시작합니다. 그런 다음 Runbook을 수정하여 실제로 Azure 리소스를 관리합니다(이 경우 Azure 가상 컴퓨터를 시작). Runbook 매개 변수를 추가하여 Runbook을 보다 강력하게 만들려면 자습서를 완료합니다.

>[!NOTE]
>이 문서는 새 Azure PowerShell Az 모듈을 사용하도록 업데이트되었습니다. AzureRM 모듈은 적어도 2020년 12월까지 버그 수정을 수신할 예정이므로 계속 사용하셔도 됩니다. 새 Az 모듈 및 AzureRM 호환성에 대한 자세한 내용은 [새 Azure PowerShell Az 모듈 소개](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0)를 참조하세요. 하이브리드 Runbook 작업자의 Az 모듈 설치 지침은 [Azure PowerShell 모듈 설치를](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)참조하십시오. 자동화 계정의 경우 Azure 자동화 에서 [Azure PowerShell 모듈을 업데이트하는 방법을](automation-update-azure-modules.md)사용하여 모듈을 최신 버전으로 업데이트할 수 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

이 자습서를 완료하려면 다음이 필요합니다.

* 동작합니다. 구독이 아직 없는 경우 [MSDN 구독자 혜택을 활성화](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)하거나 [무료 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 등록할 수 있습니다.
* [Automation 계정](automation-quickstart-create-account.md) . 이 계정은 가상 머신을 시작하고 중지할 수 있는 권한이 있어야 합니다.
* Azure 가상 머신. 이 컴퓨터를 중지하고 시작하기 때문에 프로덕션 VM이 되어서는 안 됩니다.
* 필요한 경우 사용하는 cmdlet에 따라 [Azure 모듈을 가져오거나](shared-resources/modules.md) [모듈을 업데이트합니다.](automation-update-azure-modules.md)

## <a name="differences-from-powershell-workflow-runbooks"></a>PowerShell 워크플로워크 런북의 차이점

PowerShell 실행 책은 PowerShell 워크플로 런북과 동일한 수명 주기, 기능 및 관리를 갖습니다. 그러나 몇 가지 차이점과 제한 사항이 있습니다.

| 특성  | 파워쉘 런북 | 파워쉘 워크플로우 런북 |
| ------ | ----- | ----- |
| 속도 | 컴파일 단계를 사용하지 않으면 빠르게 실행합니다. | 더 느리게 실행합니다. |
| 검사점 | 검사점은 지원하지 않습니다. PowerShell Runbook은 처음부터 작업을 다시 시작할 수 있습니다. | 통합 문서를 언제든지 작업을 다시 시작할 수 있는 검사점을 사용합니다. |
| 명령 실행 | 직렬 실행만 지원합니다. | 직렬 및 병렬 실행을 모두 지원합니다.|
| Runspace | 단일 런스페이스는 스크립트에서 모든 것을 실행합니다. | 활동, 명령 또는 스크립트 블록에 별도의 런스페이스를 사용할 수 있습니다. |

이러한 차이점 외에도 PowerShell Runbook에는 PowerShell 워크플로 런북과 몇 가지 [구문 차이가](https://technet.microsoft.com/magazine/dn151046.aspx) 있습니다.

## <a name="step-1---create-runbook"></a>1단계 - Runbook 만들기

텍스트를 출력하는 간단한 Runbook을 만들어 `Hello World`시작합니다.

1. Azure Portal에서 Automation 계정을 엽니다.
2. **프로세스 자동화에서** **Runbook을** 선택하여 Runbook 목록을 엽니다.
3. **Runbook 만들기를**선택하여 새 Runbook을 만듭니다.
4. Runbook 이름을 **MyFirstRunbook-PowerShell**로 지정합니다.
5. 이 경우 [PowerShell 실행책을](automation-runbook-types.md#powershell-runbooks)만듭니다. Runbook 유형에 대한 **PowerShell을** **선택합니다.**
6. **만들기** 를 클릭하여 Runbook을 만들고 그래픽 편집기를 엽니다.

## <a name="step-2---add-code-to-the-runbook"></a>2단계 - Runbook에 코드 추가

runbook에 직접 코드를 입력하거나 라이브러리 컨트롤에서 cmdlet, Runbook 및 자산을 선택한 후 관련 매개 변수와 함께 Runbook에 추가되도록 할 수 있습니다. 이 자습서에서는 Runbook에 직접 코드를 입력합니다.

1. Runbook이 현재 비어 있습니다. 스크립트 `Write-Output "Hello World"` 본문에 입력합니다.

   ![Hello World](media/automation-first-runbook-textual-powershell/automation-helloworld.png)

2. **저장**을 클릭하여 Runbook을 저장합니다.

## <a name="step-3---test-the-runbook"></a><a name="step-3---test-the-runbook"> </a> 3단계 - Runbook 테스트

Runbook을 프로덕션 환경에서 사용할 수 있도록 게시하기 전에 제대로 작동하는지 테스트해야 합니다. Runbook을 테스트하면 초안 버전이 실행되고 해당 출력을 대화식으로 볼 수 있습니다.

1. **테스트 창** 을 클릭하여 테스트 창을 엽니다.
2. **시작** 을 클릭하여 테스트를 시작합니다. 유일하게 사용 가능한 옵션이어야 합니다.
3. [Runbook 작업이](automation-runbook-execution.md) 만들어지고 해당 상태가 창에 표시됩니다.

   작업 상태는 큐로 시작되며, 이는 작업이 클라우드의 Runbook 작업자를 사용할 수 있게 되기를 기다리고 있음을 나타냅니다. 작업자가 작업을 클레임할 때 상태가 시작됨으로 변경됩니다. 마지막으로 Runbook이 실제로 실행되기 시작하면 상태가 실행됩니다.

4. Runbook 작업이 완료되면 테스트 창에 출력이 표시됩니다. 이 경우 을 `Hello World`볼 수 있습니다.

   ![테스트 창 출력](media/automation-first-runbook-textual-powershell/automation-testpane-output.png)

5. 캔버스로 돌아가려면 테스트 창을 닫습니다.

## <a name="step-4---publish-and-start-the-runbook"></a>4 단계 - runbook 게시 및 시작

만든 Runbook은 여전히 초안 모드에 있습니다. 프로덕션 환경에서 실행하기 전에 먼저 게시해야 합니다. Runbook을 게시하면 초안 버전으로 기존의 게시된 버전을 덮어씁니다. 이 경우에 Runbook을 방금 만들었으므로 아직 게시된 버전이 없습니다.

1. **게시**를 클릭하여 Runbook을 게시한 다음 확인 메시지가 표시되면 **예**를 클릭합니다.
1. 왼쪽을 스크롤하여 Runbook 페이지에서 Runbook을 보고 **작성 상태** 값이 **게시됨으로**설정되어 있습니다.
1. 오른쪽으로 다시 스크롤하여 **MyFirstRunbook-PowerShell의**페이지를 봅니다.
   
   상단의 옵션을 사용하면 지금 Runbook을 시작하거나, 향후 시작 시간을 예약하거나, HTTP 호출을 통해 Runbook을 시작할 수 있도록 [웹후크를](automation-webhooks.md) 만들 수 있습니다.
1. **시작**을 선택하고 Runbook을 시작할지 확인하는 메시지가 표시되면 **예**를 선택합니다. 
1. 생성된 Runbook 작업에 대해 작업 창이 열립니다. 이 창을 닫을 수는 있지만 작업의 진행 상황을 볼 수 있도록 지금 열어 둡니다. 작업 상태는 작업 **요약에**표시되며 실행북을 테스트하기 위해 가능한 상태는 설명합니다.

   ![작업 요약](media/automation-first-runbook-textual-powershell/job-pane-status-blade-jobsummary.png)

1. Runbook 상태가 완료됨으로 표시되면 **출력을** 클릭하여 출력 페이지를 `Hello World` 열어 표시할 수 있습니다.

   ![작업 출력](media/automation-first-runbook-textual-powershell/job-pane-status-blade-outputtile.png)

1. 출력 페이지를 닫습니다.
1. **모든 로그** 를 클릭하여 Runbook 작업에 대한 스트림 창을 엽니다. 출력 스트림에만 표시됩니다. `Hello World`

    스트림 창은 Runbook이 해당 스트림에 쓰는 경우 자세한 내용 및 오류 스트림과 같은 Runbook 작업에 대한 다른 스트림을 표시할 수 있습니다.

   ![모든 로그](media/automation-first-runbook-textual-powershell/job-pane-status-blade-alllogstile.png)

1. 스트림 창과 작업 창을 닫고 MyFirstRunbook-PowerShell 페이지로 돌아갑니다.
1. **세부 정보**에서 **작업을** 클릭하여 이 Runbook의 작업 페이지를 엽니다. 이 페이지에는 Runbook에서 만든 모든 작업이 나열됩니다. 작업을 한 번만 실행했기 때문에 나열된 작업이 하나만 표시됩니다.

   ![작업 목록](media/automation-first-runbook-textual-powershell/runbook-control-job-tile.png)

1. 작업 이름을 클릭하여 Runbook을 시작할 때 보았던 것과 동일한 작업 창을 엽니다. 이 창을 사용하여 Runbook에 대해 생성된 모든 작업의 세부 정보를 볼 수 있습니다.

## <a name="step-5---add-authentication-to-manage-azure-resources"></a>5 단계-Azure 리소스를 관리 인증 추가

지금까지 Runbook을 테스트 하고 게시했지만, 딱히 유용하지는 않습니다. Azure 리소스를 관리하려고 합니다. 이렇게 하려면 Runbook이 자동화 계정을 만들 때 자동으로 생성된 Run As 계정을 사용하여 인증할 수 있어야 합니다.

아래 예제와 같이 연결로 실행은 [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-3.5.0) cmdlet으로 이루어집니다. 여러 구독에서 리소스를 `AzContext` 관리하는 경우 [Get-AzContext](https://docs.microsoft.com/powershell/module/Az.Accounts/Get-AzContext?view=azps-3.5.0).

> [!NOTE]
> PowerShell `Add-AzAccount` 런북의 `Add-AzureRMAccount` 경우 에 대한 `Connect-AzAccount`별칭입니다. 이러한 cmdlet을 사용하거나 자동화 계정의 모듈을 최신 버전으로 [업데이트할](automation-update-azure-modules.md) 수 있습니다. 새 자동화 계정을 방금 만든 경우에도 모듈을 업데이트해야 할 수 있습니다.

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
1. MyFirstRunbook-PowerShell 페이지에서 **편집**을 클릭하여 텍스트 편집기를 엽니다.
1. 더 이상 `Write-Output` 라인이 필요하지 않습니다. 그냥 가서 삭제합니다.
1. 자동화 실행 계정으로 인증을 처리하는 다음 코드를 입력하거나 복사하여 붙여넣습니다.

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

1. Runbook을 테스트할 수 있도록 **테스트 창을 클릭합니다.**
1. **시작** 을 클릭하여 테스트를 시작합니다. 완료되면 계정의 기본 정보가 표시되는 다음과 유사한 출력이 표시됩니다. 이 출력에서는 실행 계정이 유효한지 확인합니다.

   ![Authenticate](media/automation-first-runbook-textual-powershell/runbook-auth-output.png)

## <a name="step-6---add-code-to-start-a-virtual-machine"></a>6단계 - 가상 머신을 시작하기 위한 코드 추가

이제 Runbook에서 Azure 구독을 인증하므로 리소스를 관리할 수 있습니다. 가상 컴퓨터를 시작하는 명령을 추가해 보겠습니다. Azure 구독에서 가상 컴퓨터를 선택할 수 있으며 지금은 Runbook에서 해당 이름을 하드 코드로 만 지정할 수 있습니다.

1. Runbook 스크립트에 [Start-AzVM](https://docs.microsoft.com/powershell/module/Az.Compute/Start-AzVM?view=azps-3.5.0) cmdlet을 추가하여 가상 컴퓨터를 시작합니다. 아래 그림과 같이 cmdlet은 이름과 `VMName` . `ResourceGroupName`

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
1. 테스트를 시작하려면 **시작을** 클릭합니다. 완료되면 가상 시스템이 시작되었는지 확인합니다.

## <a name="step-7---add-an-input-parameter"></a>7단계 - 입력 매개변수 추가

Runbook은 현재 Runbook에서 하드 코딩한 가상 컴퓨터를 시작합니다. Runbook이 시작될 때 가상 컴퓨터를 지정하는 경우 Runbook이 더 유용합니다. 해당 기능을 제공하기 위해 Runbook에 입력 매개 변수를 추가해 보겠습니다.

1. 텍스트 편집기에서 `Start-AzVM` 매개 변수및 `VMName` `ResourceGroupName`에 대한 변수를 사용하도록 cmdlet을 수정합니다. 

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
1. 이전에 시작한 가상 컴퓨터를 중지합니다.
1. **시작** 을 클릭하여 runbook을 시작합니다. 
1. 시작하려는 가상 시스템에 대한 **VMNAME** 및 **RESOURCEGROUPNAME** 값을 입력한 다음 **확인을**클릭합니다.<br><br> ![매개 변수 전달](media/automation-first-runbook-textual-powershell/automation-pass-params.png)<br>
1. Runbook이 완료되면 가상 시스템이 시작되었는지 확인합니다.

## <a name="next-steps"></a>다음 단계

* 언어 참조 및 학습 모듈을 포함한 [PowerShell에](/powershell/scripting/overview)대한 자세한 내용은 PowerShell 문서를 참조하십시오.
* PowerShell cmdlet 참조는 [Az.Automation](https://docs.microsoft.com/powershell/module/az.automation/?view=azps-3.7.0#automation
)을 참조하십시오.
* 그래픽 런북을 시작하려면 [내 첫 번째 그래픽 실행북을](automation-first-runbook-graphical.md)참조하십시오.
* PowerShell 워크플로 Runbook을 시작하려면 [내 첫 번째 PowerShell 워크플로 Runbook](automation-first-runbook-textual.md)을 참조하세요.
* Runbook 유형 및 해당 장점 및 제한 사항에 대해 자세히 알아보려면 [Azure Automation Runbook 유형을](automation-runbook-types.md)참조하십시오.
* PowerShell 스크립트 지원 기능에 대한 자세한 내용은 [Azure 자동화의 네이티브 PowerShell 스크립트 지원을](https://azure.microsoft.com/blog/announcing-powershell-script-support-azure-automation-2/)참조하십시오.
