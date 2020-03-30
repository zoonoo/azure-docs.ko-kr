---
title: Azure Automation의 내 첫 번째 PowerShell 워크플로 Runbook
description: PowerShell 워크플로를 사용하여 간단한 텍스트 Runbook의 생성, 테스트, 게시 과정을 안내하는 자습서입니다.
keywords: Powershell 워크플로, Powershell 워크플로 예제, 워크플로 Powershell
services: automation
ms.subservice: process-automation
ms.date: 09/24/2018
ms.topic: conceptual
ms.openlocfilehash: a5a1cad3179063f75a5d9a19567624180b5793a1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79367264"
---
# <a name="my-first-powershell-workflow-runbook"></a>내 첫 번째 PowerShell 워크플로 Runbook

> [!div class="op_single_selector"]
> * [그래픽](automation-first-runbook-graphical.md)
> * [Powershell](automation-first-runbook-textual-powershell.md)
> * [PowerShell 워크플로](automation-first-runbook-textual.md)
> * [Python](automation-first-runbook-textual-python2.md)

이 자습서는 Azure Automation에서 [PowerShell 워크플로 Runbook](automation-runbook-types.md#powershell-workflow-runbooks)을 만드는 과정을 안내합니다. Runbook 작업의 상태를 추적하는 방법을 배우는 동안 테스트하고 게시하는 간단한 Runbook으로 시작합니다. 그런 다음 Runbook을 수정하여 Azure 가상 컴퓨터를 시작하여 보여 지을 수 있는 Azure 리소스를 실제로 관리합니다. 마지막으로 Runbook 매개 변수를 추가하여 Runbook을 보다 강력하게 만듭니다.

>[!NOTE]
>이 문서는 새 Azure PowerShell Az 모듈을 사용하도록 업데이트되었습니다. AzureRM 모듈은 적어도 2020년 12월까지 버그 수정을 수신할 예정이므로 계속 사용하셔도 됩니다. 새 Az 모듈 및 AzureRM 호환성에 대한 자세한 내용은 [새 Azure PowerShell Az 모듈 소개](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0)를 참조하세요. 하이브리드 Runbook 작업자의 Az 모듈 설치 지침은 [Azure PowerShell 모듈 설치를](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)참조하십시오. 자동화 계정의 경우 Azure 자동화 에서 [Azure PowerShell 모듈을 업데이트하는 방법을](automation-update-azure-modules.md)사용하여 모듈을 최신 버전으로 업데이트할 수 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

이 자습서를 완료하려면 다음이 필요합니다.

* 동작합니다. 구독이 아직 없는 경우 [MSDN 구독자 혜택을 활성화](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)하거나 [무료 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 등록할 수 있습니다.
* [Automation 계정](automation-offering-get-started.md) . 이 계정은 가상 머신을 시작하고 중지할 수 있는 권한이 있어야 합니다.
* Azure 가상 머신. 이 컴퓨터를 중지하고 시작하기 때문에 프로덕션 VM이 되어서는 안 됩니다.

## <a name="step-1---create-new-runbook"></a>1 단계-새 runbook 만들기

텍스트를 출력하는 간단한 Runbook을 만들어 `Hello World`시작합니다.

1. Azure Portal에서 Automation 계정을 엽니다.

   Automation 계정 페이지는 이 계정의 리소스 간략히 보기를 제공합니다. 사용자에게는 이미 일부 자산이 있어야 합니다. 이러한 자산의 대부분은 새 자동화 계정에 자동으로 포함된 모듈입니다. 또한 구독과 연결된 자격 증명 자산이 있어야 합니다.
 
1. **프로세스 자동화에서** **Runbook을** 선택하여 Runbook 목록을 엽니다.
1. **Runbook 만들기를**선택하여 새 Runbook을 만듭니다.
1. Runbook 이름을 **MyFirstRunbook-Workflow**로 지정합니다.
1. 이 경우 [PowerShell 워크플로 실행책을](automation-runbook-types.md#powershell-workflow-runbooks)만듭니다. Runbook 유형에 대한 **PowerShell 워크플로를** **선택합니다.**
1. **만들기** 를 클릭하여 Runbook을 만들고 그래픽 편집기를 엽니다.

## <a name="step-2---add-code-to-the-runbook"></a>2단계 - Runbook에 코드 추가

Runbook에 직접 코드를 입력하거나 라이브러리 컨트롤에서 cmdlet, runbook 및 자산을 선택하고 관련 매개 변수를 사용하여 Runbook에 추가할 수 있습니다. 이 자습서에서는 Runbook에 직접 코드를 입력합니다.

1. 현재 Runbook은 필요한 `Workflow` 키워드, Runbook 이름 및 전체 워크플로를 포함하는 중괄호만 비어 있습니다.

   ```powershell-interactive
   Workflow MyFirstRunbook-Workflow
   {
   }
   ```

1. 중괄호 사이에 입력합니다. `Write-Output "Hello World"`

   ```powershell-interactive
   Workflow MyFirstRunbook-Workflow
   {
   Write-Output "Hello World"
   }
   ```

1. **저장**을 클릭하여 Runbook을 저장합니다.

## <a name="step-3---test-the-runbook"></a>3 단계 - runbook 테스트

Runbook을 프로덕션 환경에서 사용할 수 있도록 게시하기 전에 제대로 작동하는지 테스트해야 합니다. Runbook을 테스트하면 초안 버전이 실행되고 해당 출력을 대화식으로 볼 수 있습니다.

1. **테스트 창을** 선택하여 테스트 창을 엽니다.
1. 시작을 **Start** 클릭하여 유일한 사용 가능한 옵션을 테스트하여 테스트를 시작합니다.
1. [Runbook 작업이](automation-runbook-execution.md) 만들어지고 해당 상태가 창에 표시됩니다.

   작업 상태는 클라우드에서 Runbook 작업자가 사용 가능해지기를 기다리고 있음을 나타내는 것으로 시작됩니다. `Queued` 작업자가 작업을 `Starting` 클레임할 때 상태가 변경됩니다. 마지막으로 Runbook이 `Running` 실제로 실행되기 시작하면 상태가 됩니다.

1. Runbook 작업이 완료되면 테스트 창에 출력이 표시됩니다. 이 경우 을 `Hello World`볼 수 있습니다.

   ![Hello World](media/automation-first-runbook-textual/test-output-hello-world.png)

1. 캔버스로 돌아가려면 테스트 창을 닫습니다.

## <a name="step-4---publish-and-start-the-runbook"></a>4 단계 - runbook 게시 및 시작

만든 Runbook은 여전히 초안 모드에 있습니다. 프로덕션 환경에서 실행하려면 먼저 게시해야 합니다. Runbook을 게시하면 초안 버전으로 기존의 게시된 버전을 덮어씁니다. 이 경우에 Runbook을 방금 만들었으므로 아직 게시된 버전이 없습니다.

1. **게시**를 클릭하여 Runbook을 게시한 다음 확인 메시지가 표시되면 **예**를 클릭합니다.
1. 왼쪽을 스크롤하여 Runbook 페이지에서 **Runbook을** 보고 **작성 상태** 필드가 **게시됨으로**설정되어 있습니다.
1. 오른쪽으로 다시 스크롤하여 **MyFirstRunbook-워크플로에**대한 페이지를 봅니다.

   상단의 옵션을 사용하면 지금 Runbook을 시작하거나, 향후 시작 시간을 예약하거나, HTTP 호출을 통해 Runbook을 시작할 수 있도록 [웹후크를](automation-webhooks.md) 만들 수 있습니다.

1. **시작**을 선택하고 Runbook을 시작할지 확인하는 메시지가 표시되면 **예**를 선택합니다.

   ![Runbook 시작](media/automation-first-runbook-textual/automation-runbook-controls-start.png)

1. 생성된 Runbook 작업에 대해 작업 창이 열립니다. 이 경우 작업의 진행 상황을 볼 수 있도록 창을 열어 둡니다.

1. 작업 상태는 **작업 요약에**표시됩니다. 이 상태는 Runbook을 테스트할 때 본 상태와 일치합니다.

   ![작업 요약](media/automation-first-runbook-textual/job-pane-status-blade-jobsummary.png)

1. Runbook 상태가 표시되면 `Completed` **출력**을 클릭합니다. `Hello World` 메시지를 볼 수 있는 출력 페이지가 열립니다.

   ![작업 요약](media/automation-first-runbook-textual/job-pane-status-blade-outputtile.png)

1. 출력 페이지를 닫습니다.

1. **모든 로그** 를 클릭하여 Runbook 작업에 대한 스트림 창을 엽니다. 출력 스트림에만 표시됩니다. `Hello World` 스트림 창은 Runbook이 해당 스트림에 쓰는 경우 자세한 내용 및 오류 스트림과 같은 Runbook 작업에 대한 다른 스트림을 표시할 수 있습니다.

   ![작업 요약](media/automation-first-runbook-textual/job-pane-status-blade-alllogstile.png)

1. 스트림 창과 작업 창을 닫고 **MyFirstRunbook** 페이지로 돌아갑니다.
1. **리소스** 아래의 **작업을** 클릭하여 이 Runbook의 작업 페이지를 엽니다. 이 페이지에는 Runbook에서 만든 모든 작업이 나열됩니다. 작업을 한 번만 실행했기 때문에 나열된 작업이 하나만 표시됩니다.

   ![작업](media/automation-first-runbook-textual/runbook-control-job-tile.png)

1. 작업 이름을 클릭하여 Runbook을 시작할 때 보았던 것과 동일한 작업 창을 엽니다. 이 창을 사용하여 Runbook에 대해 생성된 모든 작업의 세부 정보를 볼 수 있습니다.

## <a name="step-5---add-authentication-to-manage-azure-resources"></a>5 단계-Azure 리소스를 관리 인증 추가

지금까지 Runbook을 테스트 하고 게시했지만, 딱히 유용하지는 않습니다. Azure 리소스를 관리하려고 합니다. 구독에 대한 자격 증명을 사용하여 인증하지 않는 한 그렇게 할 수 없습니다. 인증은 `Connect-AzAccount` cmdlet을 사용합니다.

>[!NOTE]
>PowerShell `Add-AzAccount` 런북의 `Add-AzureRMAccount` 경우 에 대한 `Connect-AzAccount`별칭입니다. 이러한 cmdlet을 사용하거나 자동화 계정의 모듈을 최신 버전으로 [업데이트할](automation-update-azure-modules.md) 수 있습니다. 새 자동화 계정을 방금 만든 경우에도 모듈을 업데이트해야 할 수 있습니다.

1. **MyFirstRunbook-워크플로** 페이지로 이동하여 **편집을**클릭하여 텍스트 편집기를 엽니다.
2. 선을 `Write-Output` 삭제합니다.
3. 중괄호 사이의 빈 줄에 커서를 놓습니다.
4. 자동화 실행 계정으로 인증을 처리하는 다음 코드를 입력하거나 복사하여 붙여넣습니다.

   ```powershell-interactive
   # Ensures you do not inherit an AzContext in your runbook
   Disable-AzContextAutosave –Scope Process

   $Conn = Get-AutomationConnection -Name AzureRunAsConnection
   Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID `
   -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint

   $AzureContext = Select-AzSubscription -SubscriptionId $Conn.SubscriptionID
   ```

1. Runbook을 테스트할 수 있도록 **테스트 창을 클릭합니다.**
1. **시작** 을 클릭하여 테스트를 시작합니다. 완료되면 계정의 기본 정보가 표시되는 다음과 유사한 출력이 표시됩니다. 이 작업은 자격 증명이 유효한지 확인합니다.

   ![Authenticate](media/automation-first-runbook-textual/runbook-auth-output.png)

## <a name="step-6---add-code-to-start-a-virtual-machine"></a>6단계 - 가상 머신을 시작하기 위한 코드 추가

이제 Runbook이 Azure 구독에 인증되었으므로 리소스를 관리할 수 있습니다. 가상 컴퓨터를 시작하는 명령을 추가해 보겠습니다. Azure 구독에서 모든 VM을 선택할 수 있으며 지금은 Runbook에서 해당 이름을 하드 코딩할 수 있습니다. 여러 구독에서 리소스를 관리하는 경우 `AzContext` [Get-AzContext](/powershell/module/az.accounts/get-azcontext) cmdlet에서 매개 변수를 사용해야 합니다.

1. 아래와 같이 [Start-AzVM](https://docs.microsoft.com/powershell/module/Az.Compute/Start-AzVM?view=azps-3.5.0
) cmdlet에 대한 호출을 입력하여 시작할 VM의 이름 및 리소스 그룹 이름을 제공합니다. 

   ```powershell-interactive
   workflow MyFirstRunbook-Workflow
   {
   # Ensures that you do not inherit an AzContext in your runbook
   Disable-AzContextAutosave –Scope Process

   $Conn = Get-AutomationConnection -Name AzureRunAsConnection
   Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint

   $AzureContext = Get-AzSubscription -SubscriptionId $Conn.SubscriptionID

   Start-AzVM -Name 'VMName' -ResourceGroupName 'ResourceGroupName' -AzContext $AzureContext
   }
   ```

1. Runbook을 저장한 다음, 테스트할 수 있도록 **테스트 창**을 클릭합니다.
1. **시작** 을 클릭하여 테스트를 시작합니다. 완료되면 VM이 시작되었는지 확인합니다.

## <a name="step-7---add-an-input-parameter-to-the-runbook"></a>7 단계 - runbook에 입력 매개 변수를 추가 합니다.

Runbook은 현재 Runbook에서 하드코딩한 VM을 시작합니다. Runbook이 시작될 때 VM을 지정할 수 있는 경우 더 유용합니다. 해당 기능을 제공하기 위해 Runbook에 입력 매개 변수를 추가해 보겠습니다.

1. Runbook에 `VMName` 및 `ResourceGroupName` 매개 변수에 대한 변수를 추가하고 아래와 `Start-AzVM` 같이 cmdlet이 있는 변수를 사용합니다.

   ```powershell-interactive
   workflow MyFirstRunbook-Workflow
   {
    Param(
     [string]$VMName,
     [string]$ResourceGroupName
    )
   # Ensures you do not inherit an AzContext in your runbook
   Disable-AzContextAutosave –Scope Process

   $Conn = Get-AutomationConnection -Name AzureRunAsConnection
   Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint
   Start-AzVM -Name $VMName -ResourceGroupName $ResourceGroupName
   }
   ```

2. Runbook을 저장하고 테스트 창을 엽니다. 이제 테스트에 있는 두 개의 입력 변수에 값을 제공할 수 있습니다.
3. 창을 닫습니다.
4. **게시** 를 클릭하여 Runbook의 새 버전을 게시합니다.
5. 시작한 VM을 중지합니다.
6. **시작** 을 클릭하여 runbook을 시작합니다. 
7. 시작하려는 **VM에 대한 VMNAME** 및 **RESOURCEGROUPNAME값을** 입력합니다.

   ![Runbook 시작](media/automation-first-runbook-textual/automation-pass-params.png)

8. Runbook이 완료되면 VM이 시작되었는지 확인합니다.

## <a name="next-steps"></a>다음 단계

* 언어 참조 및 학습 모듈을 포함한 [PowerShell에](https://docs.microsoft.com/powershell/scripting/overview)대한 자세한 내용은 PowerShell 문서를 참조하십시오.
* 그래픽 런북을 시작하려면 [내 첫 번째 그래픽 실행북을](automation-first-runbook-graphical.md)참조하십시오.
* PowerShell 실행책을 시작하려면 [내 첫 번째 PowerShell 실행책을](automation-first-runbook-textual-powershell.md)참조하십시오.
* Runbook 유형 및 해당 장점 및 제한 사항에 대해 자세히 알아보려면 [Azure Automation Runbook 유형을](automation-runbook-types.md)참조하십시오.
* PowerShell 스크립트 지원 기능에 대한 자세한 내용은 [Azure 자동화의 네이티브 PowerShell 스크립트 지원을](https://azure.microsoft.com/blog/announcing-powershell-script-support-azure-automation-2/)참조하십시오.
