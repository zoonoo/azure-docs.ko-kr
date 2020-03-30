---
title: Azure Automation의 내 첫 번째 그래픽 Runbook
description: 자습서는 간단한 그래픽 runbook의 생성, 테스트, 게시를 단계별로 안내합니다.
keywords: runbook, runbook 템플릿, runbook 자동화, azure runbook
services: automation
ms.subservice: process-automation
ms.date: 04/13/2018
ms.topic: conceptual
ms.openlocfilehash: 6bd360b2075c337e3ed3d69d84368d16571a9335
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79536057"
---
# <a name="my-first-graphical-runbook"></a>내 첫 번째 그래픽 Runbook

> [!div class="op_single_selector"]
> * [그래픽](automation-first-runbook-graphical.md)
> * [Powershell](automation-first-runbook-textual-powershell.md)
> * [PowerShell 워크플로](automation-first-runbook-textual.md)
> * [Python](automation-first-runbook-textual-python2.md)
> 

이 자습서는 Azure Automation에서 [그래픽 Runbook](automation-runbook-types.md#graphical-runbooks)을 만드는 과정을 안내합니다. Runbook 작업의 상태를 추적하는 방법을 배우면서 테스트하고 게시할 수 있는 간단한 Runbook으로 시작합니다. 그런 다음 Runbook을 수정하여 실제로 Azure 리소스를 관리합니다(이 경우 Azure 가상 컴퓨터를 시작). Runbook 매개 변수와 조건부 링크를 추가하여 Runbook을 보다 강력하게 만들려면 자습서를 완료합니다.

>[!NOTE]
>이 문서는 새 Azure PowerShell Az 모듈을 사용하도록 업데이트되었습니다. AzureRM 모듈은 적어도 2020년 12월까지 버그 수정을 수신할 예정이므로 계속 사용하셔도 됩니다. 새 Az 모듈 및 AzureRM 호환성에 대한 자세한 내용은 [새 Azure PowerShell Az 모듈 소개](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0)를 참조하세요. 하이브리드 Runbook 작업자의 Az 모듈 설치 지침은 [Azure PowerShell 모듈 설치를](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)참조하십시오. 자동화 계정의 경우 Azure 자동화 에서 [Azure PowerShell 모듈을 업데이트하는 방법을](automation-update-azure-modules.md)사용하여 모듈을 최신 버전으로 업데이트할 수 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

이 자습서를 완료하려면 다음이 필요합니다.

* 동작합니다. 구독이 아직 없는 경우 [MSDN 구독자 혜택을 활성화](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)하거나 [무료 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 등록할 수 있습니다.
* [Automation 계정](automation-offering-get-started.md) . 이 계정은 가상 머신을 시작하고 중지할 수 있는 권한이 있어야 합니다.
* Azure 가상 머신. 이 컴퓨터를 중지하고 시작하기 때문에 프로덕션 VM이 되어서는 안 됩니다.

## <a name="step-1---create-runbook"></a>1단계 - Runbook 만들기

텍스트를 출력하는 간단한 Runbook을 만들어 `Hello World`시작합니다.

1. Azure Portal에서 Automation 계정을 엽니다. 

    Automation 계정 페이지는 이 계정의 리소스 간략히 보기를 제공합니다. 사용자에게는 이미 일부 자산이 있어야 합니다. 이러한 자산의 대부분은 새 자동화 계정에 자동으로 포함된 모듈입니다. 또한 구독과 연결된 자격 증명 자산이 있어야 합니다.
2. **프로세스 자동화에서** **Runbook을** 선택하여 Runbook 목록을 엽니다.
3. **Runbook 만들기를**선택하여 새 Runbook을 만듭니다.
4. Runbook 이름을 **MyFirstRunbook-Graphical**로 지정합니다.
5. 이 경우 [그래픽 런북을](automation-graphical-authoring-intro.md)만듭니다. Runbook 유형에 대한 **그래픽을** **선택합니다.**<br> ![새 runbook](media/automation-first-runbook-graphical/create-new-runbook.png)<br>
6. **만들기** 를 클릭하여 Runbook을 만들고 그래픽 편집기를 엽니다.

## <a name="step-2---add-activities"></a>2단계 - 활동 추가

편집기의 왼쪽에 있는 라이브러리 컨트롤은 사용자의 runbook에 추가할 활동을 선택할 수 있게 해줍니다. runbook에서 텍스트를 출력하는 `Write-Output` cmdlet을 추가합니다.

1. 라이브러리 컨트롤에서 검색 필드를 클릭하고 `write-output`을 입력합니다. 검색 결과는 다음 이미지에 표시됩니다. <br> ![Microsoft.PowerShell.Utility](media/automation-first-runbook-graphical/search-powershell-cmdlet-writeoutput.png)
1. 목록의 아래로 스크롤하십시오. **쓰기 출력을** 마우스 오른쪽 단추로 클릭하고 **캔버스에 추가를**선택합니다. 또는 cmdlet 이름 옆에 있는 타원(...)을 클릭한 다음 **캔버스에 추가를**선택할 수 있습니다.
1. 캔버스에서 **Write-Output** 활동을 클릭합니다. 이 작업을 통해 구성 제어 페이지가 열리고, 여기서 작업을 구성할 수 있습니다.
1. **레이블** 필드는 기본적으로 cmdlet의 이름으로 설정되지만 좀 더 친숙한 것으로 변경할 수 있습니다. `Write Hello World to output`로 변경합니다.
1. **매개 변수**를 클릭하여 cmdlet 매개 변수의 값을 제공합니다.

   일부 cmdlet에는 여러 매개 변수 집합이 있으므로 사용할 매개 변수를 선택해야 합니다. 이 경우 `Write-Output` 매개 변수 집합이 하나만 있습니다.

1. 매개변수를 `InputObject` 선택합니다. 이 매개 변수는 출력 스트림에 보낼 텍스트를 지정하는 데 사용합니다.
1. **데이터 원본** 드롭다운 메뉴는 매개 변수 값을 채우는 데 사용할 수 있는 원본을 제공합니다. 이 메뉴에서 **PowerShell 식을 선택합니다.** 

   다른 활동, 자동화 자산 또는 PowerShell 식과 같은 소스의 출력을 사용할 수 있습니다. 이 경우 출력은 에 `Hello World`불과합니다. PowerShell 식을 사용하고 문자열을 지정할 수 있습니다.<br>

1. **표현식** 필드에서 입력한 `Hello World` 다음 **확인을** 두 번 클릭하여 캔버스로 돌아갑니다.
1. **저장**을 클릭하여 Runbook을 저장합니다.

## <a name="step-3---test-the-runbook"></a>3 단계 - runbook 테스트

Runbook을 프로덕션 환경에서 사용할 수 있도록 게시하기 전에 제대로 작동하는지 테스트해야 합니다. Runbook을 테스트하면 초안 버전이 실행되고 해당 출력을 대화식으로 볼 수 있습니다.

1. **테스트 창을** 선택하여 테스트 창을 엽니다.
1. **시작** 을 클릭하여 테스트를 시작합니다. 유일하게 사용 가능한 옵션이어야 합니다.
1. [Runbook 작업이](automation-runbook-execution.md) 만들어지고 해당 상태가 창에 표시됩니다.

   작업 상태는 클라우드에서 Runbook 작업자가 사용 가능해지기를 기다리고 있음을 나타내는 것으로 시작됩니다. `Queued` 작업자가 작업을 `Starting` 클레임할 때 상태가 변경됩니다. 마지막으로 Runbook이 `Running` 실제로 실행되기 시작하면 상태가 됩니다.

1. Runbook 작업이 완료되면 테스트 창에 출력이 표시됩니다. 이 경우 을 `Hello World`볼 수 있습니다.

    ![Hello World](media/automation-first-runbook-graphical/runbook-test-results.png)
1. 캔버스로 돌아가려면 테스트 창을 닫습니다.

## <a name="step-4---publish-and-start-the-runbook"></a>4 단계 - runbook 게시 및 시작

만든 Runbook은 여전히 초안 모드에 있습니다. 프로덕션 환경에서 실행하기 전에 먼저 게시해야 합니다. Runbook을 게시하면 초안 버전으로 기존의 게시된 버전을 덮어씁니다. 이 경우에 Runbook을 방금 만들었으므로 아직 게시된 버전이 없습니다.

1. **게시**를 선택하여 Runbook을 게시한 다음, 확인 메시지가 표시되면 **예**를 선택합니다.
1. 왼쪽을 스크롤하여 Runbook 페이지에서 Runbook을 보고 **작성 상태** 값이 **게시됨으로**설정되어 있습니다.
1. 오른쪽으로 다시 스크롤하면 **MyFirstRunbook-Graphical** 페이지가 표시됩니다.

   상단의 옵션을 사용하면 지금 Runbook을 시작하거나, 향후 시작 시간을 예약하거나, HTTP 호출을 통해 Runbook을 시작할 수 있도록 [웹후크를](automation-webhooks.md) 만들 수 있습니다.

1. **시작**을 선택하고 Runbook을 시작할지 확인하는 메시지가 표시되면 **예**를 선택합니다.
1. 생성된 Runbook 작업에 대해 작업 창이 열립니다. **작업 상태** 필드에 **완료됨**이 있는지 확인합니다.
1. **출력을** 클릭하여 `Hello World` 표시되는 출력 페이지를 엽니다.
1. 출력 페이지를 닫습니다.
1. **모든 로그** 를 클릭하여 Runbook 작업에 대한 스트림 창을 엽니다. 출력 스트림에만 표시됩니다. `Hello World` 

    스트림 창은 Runbook이 해당 스트림에 쓰는 경우 자세한 내용 및 오류 스트림과 같은 Runbook 작업에 대한 다른 스트림을 표시할 수 있습니다.
1. 스트림 창과 작업 창을 닫고 **MyFirstRunbook-그래픽** 페이지로 돌아갑니다.
1. Runbook의 모든 작업을 보려면 **리소스**에서 **작업을** 선택합니다. 작업 페이지에는 Runbook에서 만든 모든 작업이 나열됩니다. 작업을 한 번만 실행했기 때문에 나열된 작업이 하나만 표시됩니다.
1. 작업 이름을 클릭하여 Runbook을 시작할 때 보았던 것과 동일한 작업 창을 엽니다. 이 창을 사용하여 Runbook에 대해 생성된 모든 작업의 세부 정보를 볼 수 있습니다.

## <a name="step-5---create-variable-assets"></a>5 단계 - 변수 자산 만들기

Runbook을 테스트하고 게시했지만 지금까지는 Azure 리소스를 관리하는 데 유용한 작업을 수행하지 않습니다. Runbook을 인증하도록 구성하기 전에 구독 ID를 보유하는 변수를 만들고, 인증할 활동을 설정한 다음 변수를 참조해야 합니다. 구독 컨텍스트에 대한 참조를 포함하면 여러 구독으로 쉽게 작업할 수 있습니다.

1. 탐색 창의 구독 옵션에서 **구독** ID를 복사합니다.
1. 자동화 계정 페이지에서 **공유 리소스**에서 **변수를 선택합니다.**
1. **변수 추가**를 선택합니다.
1. 새 변수 페이지에서 제공된 필드에 다음 설정을 설정합니다.

    * **이름** -- `AzureSubscriptionId`를 입력합니다.
    * **값** -- 구독 ID를 입력합니다. 
    * Type -- 문자열을 선택된 상태로 **유지합니다.**
    * **암호화** -- 기본값을 사용합니다.
1. **만들기** 를 클릭하여 변수를 만듭니다.

## <a name="step-6---add-authentication"></a>6단계 - 인증 추가

구독 ID를 보유하는 변수가 있으므로 구독에 대한 실행 As 자격 증명으로 인증하도록 Runbook을 구성할 수 있습니다. Azure Run As 연결을 자산으로 추가하여 이 작업을 수행합니다. 또한 [커넥트-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/Connect-AzAccount?view=azps-3.5.0) cmdlet 및 [Set-AzContext](https://docs.microsoft.com/powershell/module/az.accounts/Set-AzContext?view=azps-3.5.0) cmdlet을 캔버스에 추가해야 합니다.

>[!NOTE]
>PowerShell `Add-AzAccount` 런북의 `Add-AzureRMAccount` 경우 에 대한 `Connect-AzAccount`별칭입니다. 이러한 별칭은 그래픽 런북에 사용할 수 없습니다. 그래픽 런북은 자체만 `Connect-AzAccount`사용할 수 있습니다.

1. Runbook으로 이동하여 **MyFirstRunbook-그래픽** 페이지에서 **편집을** 선택합니다.
1. 더 이상 항목이 `Write Hello World to output` 필요하지 않습니다. 타원을 클릭하고 **삭제를**선택하기만 하면 됩니다.
1. 라이브러리 컨트롤에서 **ASSETS**를 확장한 다음 **연결을**확장합니다. 캔버스에 추가를 선택하여 `AzureRunAsConnection` **캔버스에 추가합니다.**
1. `AzureRunAsConnection`의 이름을 `Get Run As Connection`으로 바꿉니다.
1. 라이브러리 컨트롤에서 검색 `Connect-AzAccount` 필드를 입력합니다.
1. 캔버스에 추가합니다. `Connect-AzAccount`
1. 셰이프 `Get Run As Connection` 아래쪽에 원이 나타날 때까지 마우스를 가져갑니다. 원을 클릭하고 화살표를 `Connect-AzAccount` 드래그하여 링크를 형성합니다. Runbook에서 시작하여 `Get Run As Connection` 실행합니다. `Connect-AzAccount`<br> ![활동 간 링크 만들기](media/automation-first-runbook-graphical/runbook-link-auth-activities.png)
1. 캔버스에서 을 `Connect-AzAccount`선택합니다. 구성 제어 창에서 **레이블** 필드에 **Azure에 로그인을** 입력합니다.
1. **매개 변수를**클릭하고 활동 매개 변수 구성 페이지가 나타납니다.
1. `Connect-AzAccount` cmdlet에는 여러 매개 변수 집합이 있으며 매개 변수 값을 제공하기 전에 하나를 선택해야 합니다. **매개 변수 집합을** 클릭한 다음 **ServicePrincipalCertificateWithSubscriptionId**를 선택합니다.
1. 이 매개변수 집합의 매개 변수는 활동 매개 변수 구성 페이지에 표시됩니다. **APPLICATIONID**를 클릭합니다.<br> ![Azure 계정 매개 변수 추가](media/automation-first-runbook-graphical/Add-AzureRmAccount-params.png)
1. 매개 변수 값 페이지에서 다음 설정을 한 다음 **확인을**클릭합니다.

   * **데이터 원본** -- **활동 출력을 선택합니다.**
   * 데이터 원본 목록 - **자동화 연결 받기를**선택합니다.
   * **필드 경로** `ApplicationId`-- 입력 . 활동이 여러 속성으로 개체를 출력하기 때문에 필드 경로에 대한 속성 이름을 지정합니다.

1. **인증서지문**및 매개 변수 값 페이지에서 다음 설정을 한 다음 **확인을**클릭합니다.

    * **데이터 원본** -- **활동 출력을 선택합니다.**
    * 데이터 원본 목록 - **자동화 연결 받기를**선택합니다.
    * **필드 경로** `CertificateThumbprint`-- 입력 .
1. **SERVICEPRINCIPAL을**클릭하고 매개 변수 값 페이지에서 **데이터 원본** 필드에 대해 **ConstantValue를** 선택합니다. 옵션을 **클릭합니다 True;** 그런 다음 **확인을**클릭합니다.
1. **테넌트 ID를**클릭하고 매개 변수 값 페이지에서 다음 설정을 만듭니다. 완료되면 **확인을** 두 번 클릭합니다.

    * **데이터 원본** -- **활동 출력을 선택합니다.** 
    * 데이터 원본 목록 - **자동화 연결 받기를**선택합니다.
    * **필드 경로** `TenantId`-- 입력 . 
1. 라이브러리 컨트롤에서 검색 `Set-AzContext` 필드를 입력합니다.
1. 캔버스에 추가합니다. `Set-AzContext`
1. 캔버스에서 선택합니다. `Set-AzContext` 구성 제어 창에서 `Specify Subscription Id` **레이블** 필드를 입력합니다.
1. **매개 변수**를 클릭하면 활동 매개 변수 구성 페이지가 표시됩니다.
1. `Set-AzContext` cmdlet에는 여러 매개 변수 집합이 있으며 매개 변수 값을 제공하기 전에 하나를 선택해야 합니다. **매개 변수 집합을** 클릭한 다음 **구독Id를**선택합니다.
1. 이 매개변수 집합의 매개 변수는 활동 매개 변수 구성 페이지에 표시됩니다. **구독 ID를 클릭합니다.**
1. 매개 변수 값 페이지에서 **데이터 원본** 필드에 대한 **가변 자산을** 선택하고 원본 목록에서 **AzureSubscriptionId를** 선택합니다. 완료되면 **확인을** 두 번 클릭합니다.
1. 셰이프 `Login to Azure` 아래쪽에 원이 나타날 때까지 마우스를 가져갑니다. 원을 클릭하고 화살표를 `Specify Subscription Id`로 드래그합니다. 이 시점에서 Runbook은 다음과 같아야 합니다.

    ![Runbook 인증 구성](media/automation-first-runbook-graphical/runbook-auth-config.png)

## <a name="step-7---add-activity-to-start-a-virtual-machine"></a>7 단계 - 가상 머신을 시작하는 활동 추가

이제 가상 컴퓨터를 `Start-AzVM` 시작하려면 활동을 추가해야 합니다. Azure 구독에서 모든 VM을 선택할 수 있으며 지금은 [시작-AzVM](https://docs.microsoft.com/powershell/module/az.compute/start-azvm?view=azps-3.5.0) cmdlet에 이름을 하드 코딩하고 있습니다.

1. 라이브러리 컨트롤에서 검색 `Start-Az` 필드를 입력합니다.
2. 캔버스에 추가한 `Start-AzVM` 다음 아래로 클릭하고 `Specify Subscription Id`드래그합니다.
1. 셰이프 `Specify Subscription Id` 아래쪽에 원이 나타날 때까지 마우스를 가져갑니다. 원을 클릭하고 화살표를 `Start-AzVM`로 드래그합니다.
1. `Start-AzVM`를 선택합니다. **매개 변수를 클릭한** 다음 **매개 변수 집합을** 클릭하여 활동에 대한 집합을 봅니다.
1. 매개 변수 집합에 대한 **리소스 그룹이름매개 변수집합을** 선택합니다. **리소스GroupName** 및 **Name** 필드에는 필요한 매개 변수임을 나타내는 느낌표가 옆에 있습니다. 두 필드 모두 문자열 값을 기대합니다.
1. **이름**을 선택합니다. **데이터 원본** 필드에 대해 **PowerShell 식을** 선택합니다. 이 Runbook을 시작하는 데 사용하는 VM의 경우 큰따옴표로 둘러싸인 컴퓨터 이름을 입력합니다. **확인**을 클릭합니다.
1. **ResourceGroupName**을 선택합니다. **데이터 원본** 필드에 대해 **PowerShell 식** 값을 사용하고 큰따옴표로 둘러싸인 리소스 그룹의 이름을 입력합니다. **확인**을 클릭합니다.
1. Runbook을 테스트할 수 있도록 **테스트 창을 클릭합니다.**
1. 테스트를 시작하려면 **시작을** 클릭합니다. 완료되면 VM이 시작되었는지 확인합니다. 이 시점에서 Runbook은 다음과 같아야 합니다.

    ![Runbook 인증 구성](media/automation-first-runbook-graphical/runbook-startvm.png)

## <a name="step-8---add-additional-input-parameters"></a>8단계 - 추가 입력 매개변수 추가

Runbook은 현재 cmdlet에 대해 지정한 리소스 `Start-AzVM` 그룹의 VM을 시작합니다. Runbook이 시작될 때 이름과 리소스 그룹을 모두 지정하면 Runbook이 더 유용합니다. 해당 기능을 제공하기 위해 Runbook에 입력 매개 변수를 추가해 보겠습니다.

1. **MyFirstRunbook-Graphical** 창에서 **편집**을 클릭하여 그래픽 편집기를 엽니다.
1. **입력 및 출력**과 **입력 추가**를 차례로 선택하여 Runbook 입력 매개 변수 창을 엽니다.
1. 제공된 필드에서 다음 설정을 한 다음 **확인을**클릭합니다.
   * **이름** -- `VMName`를 지정합니다.
   * **Type** -- 문자열 설정을 유지합니다.
   * **필수** -- 값을 **예로**변경합니다.
1. 라는 `ResourceGroupName` 두 번째 필수 입력 매개 변수를 만든 다음 **확인을** 클릭하여 입력 및 출력 창을 닫습니다.<br> ![Runbook 입력 매개 변수](media/automation-first-runbook-graphical/start-azurermvm-params-outputs.png)
1. `Start-AzVM` 활동을 선택한 다음 **매개 변수를 클릭합니다.**
1. **이름에서** **Runbook 입력으로** **데이터 원본** 필드를 변경합니다. 그런 다음 **VMName**을 선택합니다.
1. **ResourceGroupName의** **데이터 원본** 필드를 **Runbook 입력으로** 변경한 다음 **ResourceGroupName**을 선택합니다.<br> ![시작-AzVM 매개 변수](media/automation-first-runbook-graphical/start-azurermvm-params-runbookinput.png)
1. Runbook을 저장하고 테스트 창을 엽니다. 이제 테스트에서 사용하는 두 입력 변수에 대한 값을 제공할 수 있습니다.
1. 창을 닫습니다.
1. **게시** 를 클릭하여 Runbook의 새 버전을 게시합니다.
1. 이전에 시작한 VM을 중지합니다.
1. **시작** 을 클릭하여 runbook을 시작합니다. 시작하려는 `VMName` VM에 `ResourceGroupName` 대한 값을 입력합니다.
1. 실행책이 완료되면 VM이 시작되었는지 확인합니다.

## <a name="step-9---create-a-conditional-link"></a>9 단계 - 조건부 링크 만들기

이제 Runbook을 수정하여 아직 시작하지 않은 경우에만 VM을 시작하려고 시도할 수 있습니다. VM의 인스턴스 수준 상태를 검색하는 [Get-AzVM](https://docs.microsoft.com/powershell/module/Az.Compute/Get-AzVM?view=azps-3.5.0) cmdlet을 추가하여 이 작업을 수행합니다. 그런 다음 PowerShell 코드 조각으로 `Get Status` 호출된 PowerShell 워크플로 코드 모듈을 추가하여 VM 상태가 실행 중이거나 중지되었는지 확인할 수 있습니다. 모듈의 `Get Status` 조건부 링크는 `Start-AzVM` 현재 실행 상태가 중지된 경우에만 실행됩니다. 이 절차가 끝나면 Runbook은 `Write-Output` cmdlet을 사용하여 메시지를 출력하여 VM이 성공적으로 시작되었는지 알려줍니다.

1. 그래픽 편집기에서 **MyFirstRunbook-Graphical**을 엽니다.
1. 사이 `Specify Subscription Id` 링크를 `Start-AzVM` 클릭한 다음 **삭제를**눌러 링크를 제거합니다.
1. 라이브러리 컨트롤에서 검색 `Get-Az` 필드를 입력합니다.
1. 캔버스에 추가합니다. `Get-AzVM`
1. 매개변수 `Get-AzVM` **설정을** 선택한 다음 클릭하여 cmdlet의 집합을 봅니다. 
1. **GetVirtualMachineInResourceGroupNameParamSet** 매개 변수 집합을 선택합니다. **ResourceGroupName** 및 **Name** 필드에는 옆에 느낌표가 있으며 필수 매개 변수를 지정했음을 나타냅니다. 두 필드 모두 문자열 값을 기대합니다.
1. **이름에**대 한 **데이터 소스에서** **Runbook 입력을**선택한 다음 **VMName**. **확인**을 클릭합니다.
1. 리소스 그룹 **이름에**대 한 **데이터 원본에서** **Runbook 입력을**선택한 다음 **ResourceGroupName**. **확인**을 클릭합니다.
1. **상태에**대한 **데이터 원본에서** **상수 값을**선택한 다음 True 를 **선택합니다.** **확인**을 클릭합니다.
1. 에서 `Specify Subscription Id` 링크를 `Get-AzVM`만듭니다.
1. 라이브러리 컨트롤에서 **Runbook 컨트롤을** 확장하고 캔버스에 **코드를** 추가합니다.  
1. 에서 `Get-AzVM` 링크를 `Code`만듭니다.  
1. 구성 `Code` 창에서 레이블을 클릭하고 상태 **가져옵니다.**
1. 코드 `Code` 편집기 페이지가 나타납니다.  
1. 다음 코드 조각을 편집기 페이지에 붙여넣습니다.

    ```powershell-interactive
     $StatusesJson = $ActivityOutput['Get-AzVM'].StatusesText
     $Statuses = ConvertFrom-Json $StatusesJson
     $StatusOut =""
     foreach ($Status in $Statuses){
     if($Status.Code -eq "Powerstate/running"){$StatusOut = "running"}
     elseif ($Status.Code -eq "Powerstate/deallocated") {$StatusOut = "stopped"}
     }
     $StatusOut
     ```

1. 에서 `Get Status` 링크를 `Start-AzVM`만듭니다.

    ![코드 모듈과 Runbook](media/automation-first-runbook-graphical/runbook-startvm-get-status.png)  
1. 링크를 선택하고 구성 창에서 변경 **조건을** **예로**적용합니다. 이 링크는 조건이 true로 확인될 때만 대상 활동이 실행됨을 나타내는 파선이 됩니다.  
1. **조건 식에** `$ActivityOutput['Get Status'] -eq "Stopped"`대해 을 입력합니다. `Start-AzVM`이제 VM이 중지된 경우에만 실행됩니다.
1. 라이브러리 컨트롤에서 **Cmdlet**과 **Microsoft.PowerShell.Utility**를 차례로 확장합니다.
1. 캔버스에 두 번 추가합니다. `Write-Output`
1. 첫 번째 `Write-Output` 컨트롤의 경우 **매개 변수를** 클릭하고 **레이블** 값을 **VM 시작 알림으로**변경합니다.
1. **InputObject의**경우 **데이터 원본을** **PowerShell 식으로** `$VMName successfully started.`변경하고 식을 입력합니다.
1. 두 번째 `Write-Output` 컨트롤에서 **매개 변수를** 클릭하고 **레이블** 값을 **VM 시작 실패 알림으로**변경합니다.
1. **InputObject의**경우 **데이터 원본을** **PowerShell 식으로** `$VMName could not start.`변경하고 식을 입력합니다.
1. 에서 `Start-AzVM` `Notify VM Started` 및 `Notify VM Start Failed`에 대한 링크를 만듭니다.
1. 링크를 선택하고 `Notify VM Started` 적용 **조건을** true로 변경합니다.
1. 조건 **식에**대해 `$ActivityOutput['Start-AzVM'].IsSuccessStatusCode -eq $true`을 입력합니다. 이제 `Write-Output` 이 컨트롤은 VM이 성공적으로 시작되는 경우에만 실행됩니다.
1. 링크를 선택하고 `Notify VM Start Failed` 적용 **조건을** true로 변경합니다.
1. 조건 **식** 필드의 경우 `$ActivityOutput['Start-AzVM'].IsSuccessStatusCode -ne $true`을 입력합니다. 이제 `Write-Output` 이 컨트롤은 VM이 성공적으로 시작되지 않은 경우에만 실행됩니다. Runbook은 다음 이미지와 같아야 합니다.

    ![쓰기 출력으로 Runbook](media/automation-first-runbook-graphical/runbook-startazurermvm-complete.png)
1. Runbook을 저장하고 테스트 창을 엽니다.
1. VM이 중지된 후 Runbook을 시작하면 컴퓨터가 시작됩니다.

## <a name="next-steps"></a>다음 단계

* 그래픽 작성에 대한 자세한 내용은 [Azure Automation의 그래픽 작성을](automation-graphical-authoring-intro.md)참조하십시오.
* PowerShell 실행책을 시작하려면 [내 첫 번째 PowerShell 실행책을](automation-first-runbook-textual-powershell.md)참조하십시오.
* PowerShell 워크플로 런북을 시작하려면 [내 첫 번째 PowerShell 워크플로 런북을](automation-first-runbook-textual.md)참조하십시오.