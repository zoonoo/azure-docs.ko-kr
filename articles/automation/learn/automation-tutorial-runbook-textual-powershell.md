---
title: Azure Automation에서 PowerShell Runbook 만들기
description: 이 문서에서는 간단한 PowerShell Runbook을 만들고, 테스트하고, 게시하는 방법을 설명합니다.
keywords: azure powershell, powershell 스크립트 자습서, powershell 자동화
services: automation
ms.subservice: process-automation
ms.date: 04/19/2020
ms.topic: tutorial
ms.openlocfilehash: bf06515f98b21c24f5222b51e1b1c97b702c12d4
ms.sourcegitcommit: 958f086136f10903c44c92463845b9f3a6a5275f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/20/2020
ms.locfileid: "83714494"
---
# <a name="tutorial-create-a-powershell-runbook"></a>자습서: PowerShell Runbook 만들기

이 자습서는 Azure Automation에서 [PowerShell runbook](../automation-runbook-types.md#powershell-runbooks)을 만드는 과정을 안내합니다. PowerShell Runbook은 Windows PowerShell을 기반으로 합니다. Azure 포털의 텍스트 편집기를 사용하여 Runbook을 직접 편집합니다.

> [!div class="checklist"]
> * 간단한 PowerShell Runbook 만들기
> * Runbook 테스트 및 게시
> * Runbook 작업 실행 및 상태 추적
> * Runbook 매개 변수를 사용하여 Azure 가상 머신을 시작하도록 Runbook 업데이트

## <a name="prerequisites"></a>사전 요구 사항

이 자습서를 완료하려면 다음이 필요합니다.

* 동작합니다. 구독이 아직 없는 경우 [MSDN 구독자 혜택을 활성화](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)하거나 [무료 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 등록할 수 있습니다.
* [Automation 계정](../automation-quickstart-create-account.md) . 이 계정은 가상 머신을 시작하고 중지할 수 있는 권한이 있어야 합니다.
* Azure 가상 머신. 이 머신을 중지하고 시작하므로 프로덕션 VM이 아니어야 합니다.
* 필요한 경우 사용하는 cmdlet에 따라 [Azure 모듈을 가져오거나](../shared-resources/modules.md) [모듈을 업데이트](../automation-update-azure-modules.md)합니다.

## <a name="differences-from-powershell-workflow-runbooks"></a>PowerShell 워크플로 Runbook과의 차이점

PowerShell Runbook에는 PowerShell 워크플로 Runbook과 동일한 수명 주기, 기능 및 관리가 있습니다. 그러나 몇 가지 차이점과 제한 사항이 있습니다.

| 특성  | PowerShell Runbook | PowerShell 워크플로 Runbook |
| ------ | ----- | ----- |
| 속도 | 컴파일 단계를 사용하지 않으므로 빠르게 실행합니다. | 더 느리게 실행됩니다. |
| 검사점 | 검사점을 지원하지 않습니다. PowerShell Runbook은 처음부터 작업을 다시 시작할 수만 있습니다. | 검사점을 사용하여 통합 문서의 어떤 지점에서든 작업을 다시 시작할 수 있습니다. |
| 명령 실행 | 직렬 실행만 지원합니다. | 직렬 및 병렬 실행을 모두 지원합니다.|
| Runspace | 단일 runspace에서 스크립트의 모든 항목을 실행합니다. | 별도의 runspace를 활동, 명령 또는 스크립트 블록에 사용할 수 있습니다. |

PowerShell Runbook에는 이러한 차이점 외에도 PowerShell 워크플로 Runbook과 다른 몇 가지 [구문상의 차이점](https://technet.microsoft.com/magazine/dn151046.aspx)이 있습니다.

## <a name="step-1---create-runbook"></a>1단계 - Runbook 만들기

먼저 `Hello World`라는 텍스트를 출력하는 간단한 Runbook을 만듭니다.

1. Azure Portal에서 Automation 계정을 엽니다.

2. **프로세스 자동화** 아래에서 **Runbook**을 선택하여 Runbook 목록을 엽니다.

3. **Runbook 만들기**를 선택하여 새 Runbook을 만듭니다.

4. Runbook 이름을 **MyFirstRunbook-PowerShell**로 지정합니다.

5. 여기서는 [PowerShell Runbook](../automation-runbook-types.md#powershell-runbooks)을 만듭니다. **Runbook 형식**에 대해 **PowerShell**을 선택합니다.

6. **만들기** 를 클릭하여 Runbook을 만들고 그래픽 편집기를 엽니다.

## <a name="step-2---add-code-to-the-runbook"></a>2단계 - Runbook에 코드 추가

runbook에 직접 코드를 입력하거나 라이브러리 컨트롤에서 cmdlet, Runbook 및 자산을 선택한 후 관련 매개 변수와 함께 Runbook에 추가되도록 할 수 있습니다. 이 자습서에서는 코드를 Runbook에 직접 입력합니다.

1. 현재 Runbook은 비어 있습니다. `Write-Output "Hello World"`를 스크립트 본문에 입력합니다.

   ![Hello World](../media/automation-tutorial-runbook-textual-powershell/automation-helloworld.png)

2. **저장**을 클릭하여 Runbook을 저장합니다.

## <a name="step-3---test-the-runbook"></a><a name="step-3---test-the-runbook"> </a> 3단계 - Runbook 테스트

프로덕션 환경에서 사용할 수 있도록 Runbook을 게시하기 전에 먼저 해당 Runbook을 테스트하여 제대로 작동하는지 확인해야 합니다. Runbook을 테스트하면 초안 버전이 실행되고 출력을 대화형으로 볼 수 있습니다.

1. **테스트 창** 을 클릭하여 테스트 창을 엽니다.

2. **시작** 을 클릭하여 테스트를 시작합니다. 유일하게 사용 가능한 옵션이어야 합니다.

3. [Runbook 작업](../automation-runbook-execution.md)이 만들어지고 해당 상태가 창에 표시됩니다.

   작업 상태는 '대기'로 시작됩니다. 이는 클라우드에서 runbook worker를 사용할 수 있을 때까지 작업에서 기다리고 있음을 나타냅니다. 작업자에서 작업을 요청하면 상태가 '시작 중'으로 변경됩니다. 마지막으로 Runbook이 실제로 실행되기 시작하면 상태가 '실행 중'이 됩니다.

4. Runbook 작업이 완료되면 [테스트] 창에 해당 출력이 표시됩니다. 여기서는 `Hello World`가 표시됩니다.

   ![테스트 창 출력](../media/automation-tutorial-runbook-textual-powershell/automation-testpane-output.png)

5. 캔버스로 돌아가려면 테스트 창을 닫습니다.

## <a name="step-4---publish-and-start-the-runbook"></a>4 단계 - runbook 게시 및 시작

방금 만든 Runbook은 아직 초안 모드입니다. 프로덕션 환경에서 실행하기 전에 먼저 게시해야 합니다. Runbook을 게시하면 초안 버전으로 기존의 게시된 버전을 덮어씁니다. 이 경우에 Runbook을 방금 만들었으므로 아직 게시된 버전이 없습니다.

1. **게시**를 클릭하여 Runbook을 게시한 다음 확인 메시지가 표시되면 **예**를 클릭합니다.

2. Runbook 페이지에서 왼쪽으로 스크롤하여 Runbook을 살펴보고, **작성 상태** 값이 **게시됨**으로 설정되어 있는지 확인합니다.

3. 오른쪽으로 다시 스크롤하여 **MyFirstRunbook-PowerShell** 페이지를 봅니다.
   
   위쪽의 옵션을 사용하면 지금 Runbook을 시작하거나, 향후의 시작 시간을 예약하거나, [웹후크](../automation-webhooks.md)를 만들어 HTTP 호출을 통해 Runbook을 시작할 수 있습니다.

4. **시작**을 선택하고 Runbook을 시작할지 확인하는 메시지가 표시되면 **예**를 선택합니다. 

5. 만든 Runbook 작업에 대한 [작업] 창이 열립니다. 이 창을 닫을 수 있지만, 작업 진행 상황을 볼 수 있도록 지금 열어 둡니다. 작업 상태는 **작업 요약**에 표시되며, 가능한 상태는 Runbook 테스트에 대해 설명한 대로입니다.

   ![작업 요약](../media/automation-tutorial-runbook-textual-powershell/job-pane-status-blade-jobsummary.png)

6. Runbook 상태가 '완료됨'으로 표시되면 **출력**을 클릭하여 [출력] 페이지를 엽니다. 여기서는 `Hello World`가 표시됩니다.

   ![작업 출력](../media/automation-tutorial-runbook-textual-powershell/job-pane-status-blade-outputtile.png)

7. 출력 페이지를 닫습니다.

8. **모든 로그** 를 클릭하여 Runbook 작업에 대한 스트림 창을 엽니다. 출력 스트림에는 `Hello World`만 표시됩니다.

    Runbook에서 [스트림] 창에 기록하는 경우 자세한 정보 및 오류 스트림과 같은 Runbook 작업에 대한 다른 스트림이 표시될 수 있습니다.

   ![모든 로그](../media/automation-tutorial-runbook-textual-powershell/job-pane-status-blade-alllogstile.png)

9. [스트림] 창과 [작업] 창을 닫고 MyFirstRunbook-PowerShell 페이지로 돌아갑니다.

10. **세부 정보** 아래에서 **작업**을 클릭하여 이 Runbook에 대한 [작업] 페이지를 엽니다. 이 페이지에는 Runbook에서 만든 모든 작업이 나열됩니다. 여기서는 작업을 한 번만 실행했으므로 하나의 작업만 나열됩니다.

   ![작업 목록](../media/automation-tutorial-runbook-textual-powershell/runbook-control-job-tile.png)

11. 작업 이름을 클릭하여 Runbook을 시작할 때 표시된 것과 동일한 [작업] 창을 엽니다. 이 창을 사용하여 Runbook에 대해 만들어진 작업의 세부 정보를 볼 수 있습니다.

## <a name="step-5---add-authentication-to-manage-azure-resources"></a>5 단계-Azure 리소스를 관리 인증 추가

지금까지 Runbook을 테스트 하고 게시했지만, 딱히 유용하지는 않습니다. Azure 리소스를 관리하려고 합니다. 이렇게 하려면 Runbook에서 Automation 계정을 만들 때 자동으로 만들어진 실행 계정을 사용하여 인증할 수 있어야 합니다.

아래 예제에서와 같이 [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-3.5.0) cmdlet을 사용하여 실행 연결이 설정됩니다. 여러 구독에서 리소스를 관리하는 경우 [Get-AzContext](https://docs.microsoft.com/powershell/module/Az.Accounts/Get-AzContext?view=azps-3.5.0)에서 `AzContext` 매개 변수를 사용해야 합니다.

> [!NOTE]
> PowerShell Runbook의 경우 `Add-AzAccount` 및 `Add-AzureRMAccount`는 `Connect-AzAccount`에 대한 별칭입니다. 이러한 cmdlet을 사용하거나 Automation 계정의 [모듈을 최신 버전으로 업데이트](../automation-update-azure-modules.md)할 수 있습니다. 새 Automation 계정을 만든 경우에도 모듈을 업데이트해야 할 수 있습니다.

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

2. `Write-Output` 줄은 더 이상 필요하지 않습니다. 계속 삭제하면 됩니다.

3. Automation 실행 계정을 사용하여 인증을 처리하는 다음 코드를 입력하거나 복사하여 붙여넣습니다.

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

4. Runbook을 테스트할 수 있도록 **테스트 창**을 클릭합니다.

5. **시작** 을 클릭하여 테스트를 시작합니다. 완료되면 다음과 비슷한 출력이 표시됩니다. 여기서는 계정의 기본 정보를 보여 줍니다. 이 출력에서는 실행 계정이 유효한지 확인합니다.

   ![Authenticate](../media/automation-tutorial-runbook-textual-powershell/runbook-auth-output.png)

## <a name="step-6---add-code-to-start-a-virtual-machine"></a>6단계 - 가상 머신을 시작하기 위한 코드 추가

이제 Runbook에서 Azure 구독을 인증하므로 리소스를 관리할 수 있습니다. 가상 머신을 시작하는 명령을 추가해 보겠습니다. Azure 구독에서 가상 머신을 선택할 수 있지만, 지금은 해당 이름을 Runbook에 하드 코딩하면 됩니다.

1. [Start-AzVM](https://docs.microsoft.com/powershell/module/Az.Compute/Start-AzVM?view=azps-3.5.0) cmdlet을 Runbook 스크립트에 추가하여 가상 머신을 시작합니다. 아래와 같이 cmdlet에서 이름이 `VMName`이고 리소스 그룹 이름이 `ResourceGroupName`인 가상 머신을 시작합니다.

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

2. Runbook을 저장한 다음, 테스트할 수 있도록 **테스트 창**을 클릭합니다.

3. **시작**을 클릭하여 테스트를 시작합니다. 완료되면 가상 머신이 시작되었는지 확인합니다.

## <a name="step-7---add-an-input-parameter"></a>7단계 - 입력 매개 변수 추가

Runbook에서 현재 Runbook에 하드 코딩된 가상 머신을 시작합니다. Runbook이 시작될 때 가상 머신을 지정하는 경우 Runbook이 더 유용합니다. 입력 매개 변수를 Runbook에 추가하여 해당 기능을 제공해 보겠습니다.

1. 텍스트 편집기에서 변수를 `VMName` 및 `ResourceGroupName` 매개 변수에 사용하도록 `Start-AzVM` cmdlet을 수정합니다. 

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

2. Runbook을 저장하고 테스트 창을 엽니다. 이제 테스트에서 사용하는 두 입력 변수에 대한 값을 제공할 수 있습니다.

3. 창을 닫습니다.

4. **게시** 를 클릭하여 Runbook의 새 버전을 게시합니다.

5. 이전에 시작한 가상 머신을 중지합니다.

6. **시작** 을 클릭하여 runbook을 시작합니다. 

7. 시작할 가상 머신에 대한 **VMNAME** 및 **RESOURCEGROUPNAME** 값을 입력한 다음, **확인**을 클릭합니다.

    ![매개 변수 전달](../media/automation-tutorial-runbook-textual-powershell/automation-pass-params.png)

8. Runbook이 완료되면 가상 머신이 시작되었는지 확인합니다.

## <a name="next-steps"></a>다음 단계

* [PowerShell 문서](/powershell/scripting/overview)
* [Az.Automation](https://docs.microsoft.com/powershell/module/az.automation/?view=azps-3.7.0#automation)
* [그래픽 Runbook 만들기](automation-tutorial-runbook-graphical.md)
* [PowerShell 워크플로 Runbook 만들기](automation-tutorial-runbook-textual.md)
* [Azure Automation Runbook 형식](../automation-runbook-types.md)
* [Azure Automation의 네이티브 PowerShell 스크립트 지원](https://azure.microsoft.com/blog/announcing-powershell-script-support-azure-automation-2/)
