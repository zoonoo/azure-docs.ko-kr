---
title: Azure Automation에서 그래픽 Runbook 만들기
description: 이 문서에서는 Azure Automation에서 그래픽 Runbook을 만들고, 테스트하고, 게시하는 방법을 설명합니다.
services: automation
ms.subservice: process-automation
ms.date: 07/16/2021
ms.topic: tutorial
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: e782fd046af23beb5c84c8e419cd5925db7230ce
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/22/2021
ms.locfileid: "114449074"
---
# <a name="tutorial-create-a-graphical-runbook"></a>자습서: 그래픽 Runbook 만들기

이 자습서는 Azure Automation에서 [그래픽 Runbook](../automation-runbook-types.md#graphical-runbooks)을 만드는 과정을 안내합니다. Azure Portal에서 그래픽 편집기를 사용하여 그래픽 PowerShell 워크플로 Runbook을 만들고 편집할 수 있습니다.

이 자습서에서는 다음 작업 방법을 알아봅니다.

> [!div class="checklist"]
> * 간단한 그래픽 Runbook 만들기
> * Runbook 테스트 및 게시
> * Runbook 작업 실행 및 상태 추적
> * Runbook 매개 변수 및 조건부 링크를 사용하여 Azure 가상 머신을 시작하도록 Runbook 업데이트

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

## <a name="prerequisites"></a>사전 요구 사항

* [Automation 계정](../index.yml) 및 [Azure 실행 계정](../create-run-as-account.md) - Runbook을 보유하고 Azure 리소스에 대해 인증하는 데 사용됩니다. 이 계정은 가상 머신을 시작하고 중지할 수 있는 권한이 있어야 합니다.
* Automation 계정에 대한 PowerShell 모듈 **Az.Accounts** 및 **Az.Compute**. 자세한 내용은 [Azure Automation에서 모듈 관리](../shared-resources/modules.md)를 참조하세요.
* [Azure VM(가상 머신)](../../virtual-machines/windows/quick-create-portal.md). 이 머신을 중지하고 시작하므로 프로덕션 VM이 아니어야 합니다. VM이 **중지된** 상태로 시작합니다.

## <a name="create-runbook"></a>runbook 만들기

먼저 `Hello World`라는 텍스트를 출력하는 간단한 Runbook을 만듭니다.

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.

1. Azure Portal에서 Automation 계정으로 이동합니다.

1. **프로세스 자동화** 에서 **Runbook** 을 선택하여 **Runbook** 페이지를 엽니다.

1. **Runbook 만들기** 를 선택하여 **Runbook 만들기** 페이지를 엽니다.

1. Runbook 이름을 `MyFirstRunbook-Graphical`로 지정합니다.

1. **Runbook 형식** 드롭다운 메뉴에서 **그래픽** 을 선택합니다.

   :::image type="content" source="../media/automation-tutorial-runbook-graphical/create-graphical-runbook.png" alt-text="Runbook 입력 페이지 만들기":::

1. **만들기** 를 선택하여 Runbook을 만들고 그래픽 편집기인 **그래픽 Runbook 편집** 페이지를 엽니다.

## <a name="add-activities"></a>활동 추가

편집기 왼쪽은 **라이브러리 컨트롤** 입니다. 가운데는 **캔버스** 입니다. 오른쪽은 **구성 컨트롤** 입니다. **라이브러리 컨트롤** 을 사용하여 Runbook에 추가할 작업을 선택할 수 있습니다. Runbook에서 텍스트를 출력하는 `Write-Output` cmdlet을 추가합니다.

1. **라이브러리 컨트롤** 검색 필드에 `Write-Output`을 입력합니다.

    ![Microsoft.PowerShell.Utility](../media/automation-tutorial-runbook-graphical/search-powershell-cmdlet-writeoutput.png)

1. 목록의 아래로 스크롤하십시오. 마우스 오른쪽 단추로 **Write-Output** 을 클릭하고, **캔버스에 추가** 를 선택합니다. cmdlet 이름 옆의 줄임표(...)를 선택한 다음, **캔버스에 추가** 를 선택할 수도 있습니다.

1. **캔버스** 에서 **Write-Output** 활동을 선택합니다. 이 작업은 활동을 구성할 수 있는 **구성 컨트롤** 페이지를 채웁니다.

1. **구성 컨트롤** 의 **레이블** 필드는 기본적으로 cmdlet 이름으로 설정되지만 더 친숙한 이름으로 변경할 수 있습니다. `Write Hello World to output`으로 변경합니다.

1. **매개 변수** 를 선택하여 cmdlet 매개 변수의 값을 제공합니다.

   일부 cmdlet에는 여러 매개 변수 세트가 있으며, 사용할 매개 변수 세트를 선택해야 합니다. 여기서는 `Write-Output`에 하나의 매개 변수 세트만 있습니다.

1. **활동 매개 변수 구성** 페이지에서 `INPUTOBJECT` 매개 변수를 선택하여 **매개 변수 값** 페이지를 엽니다. 이 매개 변수는 출력 스트림으로 보낼 텍스트를 지정하는 데 사용합니다.

1. **데이터 원본** 드롭다운 메뉴는 매개 변수 값을 채우는 데 사용할 수 있는 원본을 제공합니다. 이 메뉴에서 **PowerShell 식** 을 선택합니다.

   다른 활동, Automation 자산 또는 PowerShell 식과 같은 원본의 출력을 사용할 수 있습니다. 여기서는 출력이 `Hello World`입니다. PowerShell 식을 사용하고 문자열을 지정할 수 있습니다.

1. **식** 텍스트 상자에 `"Hello World"`를 입력한 다음, **확인** 을 두 번 선택하여 그래픽 편집기로 돌아갑니다.

1. **저장** 을 선택하여 Runbook을 저장합니다.

## <a name="test-the-runbook"></a>Runbook 테스트

프로덕션 환경에서 사용할 수 있도록 Runbook을 게시하기 전에 먼저 해당 Runbook을 테스트하여 제대로 작동하는지 확인해야 합니다. Runbook을 테스트하면 초안 버전이 실행되고 출력을 대화형으로 볼 수 있습니다.

1. 그래픽 편집기에서 **테스트 창** 을 선택하여 **테스트** 창을 엽니다.

1. **시작** 을 선택하여 테스트를 시작합니다.

   [Runbook 작업](../automation-runbook-execution.md)이 생성되고 해당 상태가 이 창에 표시됩니다. 작업 상태는 `Queued`로 시작됩니다. 이는 클라우드에서 runbook worker를 사용할 수 있을 때까지 작업에서 기다리고 있음을 나타냅니다. 작업자에서 작업을 요청하면 상태가 `Starting`으로 변경됩니다. 마지막으로 Runbook이 실제로 실행되기 시작하면 상태가 `Running`이 됩니다.

   Runbook 작업이 완료되면 [테스트] 창에 해당 출력이 표시됩니다. 여기서는 `Hello World`가 표시됩니다.

   :::image type="content" source="../media/automation-tutorial-runbook-graphical/runbook-test-results.png" alt-text="Hello World Runbook 출력":::

1. 오른쪽 위에서 **X** 를 선택하여 **테스트** 창을 닫고 그래픽 편집기로 돌아갑니다.

## <a name="publish-and-start-the-runbook"></a>Runbook 게시 및 시작

만들어진 Runbook은 초안 모드에 있으며 프로덕션에서 실행하려면 먼저 게시해야 합니다. Runbook을 게시하면 초안 버전으로 기존의 게시된 버전을 덮어씁니다.

1. 그래픽 편집기에서 **게시** 를 선택하여 Runbook을 게시한 다음, 메시지가 표시되면 **예** 를 선택합니다. **Runbook** 개요 페이지로 돌아갑니다.

1. **Runbook** 개요 페이지에서 **상태** 값은 **게시됨** 입니다.

1. **시작** 을 선택한 다음, Runbook을 시작할지 확인하는 메시지가 표시되면 **예** 를 선택하고, **작업** 페이지를 엽니다.

   위쪽에 있는 옵션을 사용하면 지금 Runbook을 시작하거나, 향후 시작 시간을 예약하거나, [웹후크](../automation-webhooks.md)를 만들어 HTTP 호출을 통해 Runbook을 시작할 수 있습니다.

   :::image type="content" source="../media/automation-tutorial-runbook-graphical/published-status.png" alt-text="개요 페이지 및 게시된 상태":::

1. **작업** 페이지에서 **상태** 필드가 **완료됨** 으로 표시되는지 확인합니다.

1. **출력** 을 선택하여 `Hello World`가 표시되는지 확인합니다.

1. **모든 로그** 를 선택하여 Runbook 작업의 스트림을 보고 **작업 스트림 세부 정보** 페이지를 여는 유일한 항목을 선택합니다. `Hello World`만 표시됩니다.

    **모든 로그** 에는 Runbook이 해당 스트림에 기록되는 경우 자세한 정보 및 오류 스트림과 같은 Runbook 작업의 다른 스트림이 표시될 수 있습니다.

1. **작업 스트림 세부 정보** 페이지를 닫은 다음, **작업** 페이지를 닫고 **Runbook** 개요 페이지로 돌아갑니다.

1. **리소스** 에서 **작업** 을 선택하여 Runbook의 모든 작업을 확인합니다. 작업 페이지에는 Runbook에서 만든 모든 작업이 나열됩니다. 작업을 한 번만 실행했으므로 작업이 하나만 나열되어 있어야 합니다.

1. 작업을 선택하여 Runbook을 시작할 때 표시된 것과 동일한 **작업** 창을 엽니다.

1. **작업** 페이지를 닫은 다음, 왼쪽 메뉴에서 **개요** 를 선택합니다.

## <a name="create-variable-assets"></a>변수 자산 만들기

Runbook을 테스트하고 게시했지만 지금까지는 Azure 리소스를 관리하는 데 유용한 작업을 수행하지 않았습니다. 인증하도록 Runbook을 구성하기 전에 먼저 구독 ID를 보유할 변수를 만들고, 인증 활동을 설정한 다음, 변수를 참조해야 합니다. 구독 컨텍스트에 대한 참조를 포함하면 여러 구독을 쉽게 사용할 수 있습니다.

1. **개요** 에서 **구독 ID** 옆에 있는 **클립보드로 복사** 아이콘을 선택합니다.

1. **Runbook** 페이지를 닫고 **Automation 계정** 페이지로 돌아갑니다.

1. **공유 리소스** 에서 **변수** 를 선택합니다.

1. **변수 추가** 를 선택하여 **새 변수** 페이지를 엽니다.

1. **새 변수** 페이지에서 다음 값을 설정합니다.

    | 필드| 값|
    |---|---|
    |값|<kbd>Ctrl+V</kbd>를 눌러 구독 ID에 붙여넣습니다.|
    |속성 |`AzureSubscriptionId`를 입력합니다.|
    |Type|기본값 **String** 을 유지합니다.|
    |암호화됨|기본값 **No** 를 유지합니다.|

1. **만들기** 를 선택하여 변수를 만들고 **변수** 페이지로 돌아갑니다.

1. **프로세스 자동화** 에서 **Runbook** 을 선택한 다음, Runbook **MyFirstRunbook-Graphical** 을 선택합니다.

## <a name="add-authentication"></a>인증 추가

이제 구독 ID를 보유할 변수가 있으므로 구독에 대한 실행 자격 증명을 사용하여 인증하도록 Runbook을 구성할 수 있습니다. Azure 실행 연결을 자산으로 추가하여 인증을 구성합니다. 또한 [Connect-AzAccount](/powershell/module/az.accounts/Connect-AzAccount) cmdlet 및 [Set-AzContext](/powershell/module/az.accounts/Set-AzContext) cmdlet을 캔버스에 추가해야 합니다.

> [!NOTE]
> PowerShell Runbook의 경우 `Add-AzAccount` 및 `Add-AzureRMAccount`는 `Connect-AzAccount`에 대한 별칭입니다. 이 별칭은 그래픽 Runbook에서 사용할 수 없습니다. 그래픽 Runbook은 `Connect-AzAccount`만 사용할 수 있습니다.

1. **Runbook** 페이지에서 **편집** 을 선택하여 그래픽 편집기로 돌아갑니다.

1. `Write Hello World to output` 활동이 더 이상 필요하지 않습니다. 활동을 선택하면 활동의 오른쪽 위에 줄임표가 표시됩니다. 줄임표를 확인하기 어려울 수 있습니다. 줄임표를 선택한 다음, **삭제** 를 선택합니다.

1. **라이브러리 컨트롤** 에서 **자산** > **연결** > **AzureRunAsConnection** 으로 이동합니다. 줄임표를 선택한 다음, **캔버스에 추가** 를 선택합니다.

1. **구성 컨트롤** 에서 **레이블** 값을 `Get-AutomationConnection`에서 `Get Run As Connection`으로 변경합니다.

1. **라이브러리 컨트롤** 검색 필드에서 `Connect-AzAccount`를 입력합니다.

1. `Connect-AzAccount`를 캔버스에 추가하고 활동을 `Get Run As Connection` 아래로 끌어옵니다.

1. 도형 아래쪽에 원이 나타날 때까지 마우스로 `Get Run As Connection` 위를 가리킵니다. 원을 선택하고 있으면 화살표가 표시됩니다. 화살표를 `Connect-AzAccount`로 끌어 링크를 구성합니다. Runbook에서 `Get Run As Connection`을 사용하여 시작한 다음, `Connect-AzAccount`를 실행합니다.

    ![활동 간 링크 만들기](../media/automation-tutorial-runbook-graphical/runbook-link-auth-activities.png)

1. **캔버스** 에서 `Connect-AzAccount`를 선택합니다.

1. **구성 컨트롤** 에서 **레이블** 을 `Connect-AzAccount`에서 `Login to Azure`로 변경합니다.

1. **매개 변수** 를 선택하여 **활동 매개 변수 구성** 페이지를 엽니다.

1. `Connect-AzAccount` cmdlet에는 여러 매개 변수 세트가 있으며, 매개 변수 값을 제공하기 전에 먼저 매개 변수 세트를 선택해야 합니다. **매개 변수 세트** 를 선택한 다음, **ServicePrincipalCertificateWithSubscriptionId** 를 선택합니다. 이름이 비슷하므로 **ServicePrincipalCertificateFileWithSubscriptionId** 를 선택하지 않도록 주의하세요.

   **활동 매개 변수 구성** 페이지에 이 매개 변수 세트의 매개 변수가 표시됩니다.

    ![Azure 계정 매개 변수 추가](../media/automation-tutorial-runbook-graphical/Add-AzureRmAccount-params.png)

1. **CERTIFICATETHUMBPRINT** 를 선택하여 **매개 변수 값** 페이지를 엽니다.
    1. **데이터 원본** 드롭다운 메뉴에서 **활동 출력** 을 선택합니다.
    1. **데이터 선택** 에서 **실행 연결 가져오기** 를 선택합니다.
    1. **필드 경로** 텍스트 상자에 `CertificateThumbprint`를 입력합니다.
    1. **확인** 을 선택하여 **활동 매개 변수 구성** 페이지로 돌아갑니다.

1. **SERVICEPRINCIPAL** 을 선택하여 **매개 변수 값** 페이지를 엽니다.
    1. **데이터 원본** 드롭다운 메뉴에서 **상수 값** 을 선택합니다.
    1. **True** 옵션을 선택합니다.
    1. **확인** 을 선택하여 **활동 매개 변수 구성** 페이지로 돌아갑니다.

1. **TENANT** 를 선택하여 **매개 변수 값** 페이지를 엽니다.
    1. **데이터 원본** 드롭다운 메뉴에서 **활동 출력** 을 선택합니다.
    1. **데이터 선택** 에서 **실행 연결 가져오기** 를 선택합니다.
    1. **필드 경로** 텍스트 상자에 `TenantId`를 입력합니다.
    1. **확인** 을 선택하여 **활동 매개 변수 구성** 페이지로 돌아갑니다.

1. **APPLICATIONID** 를 선택하여 **매개 변수 값** 페이지를 엽니다.
    1. **데이터 원본** 드롭다운 메뉴에서 **활동 출력** 을 선택합니다.
    1. **데이터 선택** 에서 **실행 연결 가져오기** 를 선택합니다.
    1. **필드 경로** 텍스트 상자에 `ApplicationId`를 입력합니다.
    1. **확인** 을 선택하여 **활동 매개 변수 구성** 페이지로 돌아갑니다.

1. **확인** 을 선택하여 그래픽 편집기로 돌아갑니다.

1. **라이브러리 컨트롤** 검색 필드에서 `Set-AzContext`를 입력합니다.

1. `Set-AzContext`를 캔버스에 추가하고 활동을 `Login to Azure` 아래로 끌어옵니다.

1. **구성 컨트롤** 에서 **레이블** 을 `Set-AzContext`에서 `Specify Subscription Id`로 변경합니다.

1. **매개 변수** 를 선택하여 **활동 매개 변수 구성** 페이지를 엽니다.

1. `Set-AzContext` cmdlet에는 여러 매개 변수 세트가 있으며, 매개 변수 값을 제공하기 전에 먼저 매개 변수 세트를 선택해야 합니다. **매개 변수 세트** 를 선택한 다음, **구독** 을 선택합니다. **활동 매개 변수 구성** 페이지에 이 매개 변수 세트의 매개 변수가 표시됩니다.

1. **SUBSCRIPTION** 을 선택하여 **매개 변수 값** 페이지를 엽니다.
    1. **데이터 원본** 드롭다운 메뉴에서 **변수 자산** 을 선택합니다.
    1. 변수 목록에서 **AzureSubscriptionId** 를 선택합니다.
    1. **확인** 을 선택하여 **활동 매개 변수 구성** 페이지로 돌아갑니다.

1. **확인** 을 선택하여 그래픽 편집기로 돌아갑니다.

1. `Login to Azure`와 `Specify Subscription Id` 간에 링크를 구성합니다. 이 시점에서 Runbook은 다음과 같습니다.

    :::image type="content" source="../media/automation-tutorial-runbook-graphical/runbook-auth-config.png" alt-text="화살표를 '구독 ID 지정'으로 끌어온 후에 Runbook의 스크린샷.":::

## <a name="add-activity-to-start-a-virtual-machine"></a>가상 머신을 시작하는 활동 추가

이제 가상 머신을 시작하는 `Start-AzVM` 활동을 추가해야 합니다. Azure 구독에서 VM을 선택할 수 있으며, 지금은 해당 이름을 [Start-AzVM](/powershell/module/az.compute/start-azvm) cmdlet에 하드 코딩합니다.

1. **라이브러리 컨트롤** 검색 필드에서 `Start-AzVM`을 입력합니다.

1. `Start-AzVM`를 캔버스에 추가하고 활동을 `Specify Subscription Id` 아래로 끌어옵니다.

1. **구성 컨트롤** 에서 **매개 변수** 를 선택하여 **활동 매개 변수 구성** 페이지를 엽니다.

1. **매개 변수 세트** 를 선택한 다음, **ResourceGroupNameParameterSetName** 을 선택합니다. **활동 매개 변수 구성** 페이지에 이 매개 변수 세트의 매개 변수가 표시됩니다. 매개 변수 **RESOURCEGROUPNAME** 및 **NAME** 옆에 필수 매개 변수임을 나타내는 느낌표가 있습니다. 두 필드에는 모두 문자열 값이 필요합니다.

1. **RESOURCEGROUPNAME** 을 선택하여 **매개 변수 값** 페이지를 엽니다.
    1. **데이터 원본** 드롭다운 메뉴에서 **PowerShell 식** 을 선택합니다.
    1. **식** 텍스트 상자에 리소스 그룹의 이름을 큰따옴표로 묶어서 입력합니다.
    1. **확인** 을 선택하여 **활동 매개 변수 구성** 페이지로 돌아갑니다.

1. **NAME** 을 선택하여 **매개 변수 값** 페이지를 엽니다.
    1. **데이터 원본** 드롭다운 메뉴에서 **PowerShell 식** 을 선택합니다.
    1. **식** 텍스트 상자에 가상 머신의 이름을 큰따옴표로 묶어서 입력합니다.
    1. **확인** 을 선택하여 **활동 매개 변수 구성** 페이지로 돌아갑니다.

1. **확인** 을 선택하여 그래픽 편집기로 돌아갑니다.

1. `Specify Subscription Id`와 `Start-AzVM` 간에 링크를 구성합니다. 이 시점에서 Runbook은 다음과 같습니다.

    ![Runbook Start-AzVM 출력](../media/automation-tutorial-runbook-graphical/runbook-startvm.png)

1. Runbook을 테스트할 수 있도록 **테스트 창** 을 선택합니다.

1. **시작** 을 선택하여 테스트를 시작합니다.

1. 완료되면 VM이 시작되었는지 확인합니다. 그런 다음, 이후 단계를 위해 VM을 중지합니다.

1. Runbook의 그래픽 편집기로 돌아갑니다.

## <a name="add-input-parameters"></a>입력 매개 변수 추가

Runbook은 현재 `Start-AzVM` cmdlet에 지정한 리소스 그룹의 VM을 시작합니다. Runbook이 시작될 때 이름과 리소스 그룹을 모두 지정하는 경우 Runbook이 더 유용합니다. 입력 매개 변수를 Runbook에 추가하여 해당 기능을 제공해 보겠습니다.

1. 그래픽 편집기 위쪽 메뉴 모음에서 **입력 및 출력** 을 선택합니다.

1. **입력 추가** 를 선택하여 **Runbook 입력 매개 변수** 페이지를 엽니다.

1. **Runbook 입력 매개 변수** 페이지에서 다음 값을 설정합니다.

    | 필드| 값|
    |---|---|
    |속성| `VMName`를 입력합니다.|
    |Type|기본값 **String** 을 유지합니다.|
    |필수|값을 **예** 로 변경합니다.|

1. **확인** 을 선택하여 **입력 및 출력** 페이지로 돌아갑니다.

1. **입력 추가** 를 선택하여 **Runbook 입력 매개 변수** 페이지를 다시 엽니다.

1. **Runbook 입력 매개 변수** 페이지에서 다음 값을 설정합니다.

    | 필드| 값|
    |---|---|
    |속성| `ResourceGroupName`를 입력합니다.|
    |Type|기본값 **String** 을 유지합니다.|
    |필수|값을 **예** 로 변경합니다.|

1. **확인** 을 선택하여 **입력 및 출력** 페이지로 돌아갑니다. 이 페이지는 다음과 같이 표시됩니다.

    ![Runbook 입력 매개 변수](../media/automation-tutorial-runbook-graphical/start-azurermvm-params-outputs.png)

1. **확인** 을 선택하여 그래픽 편집기로 돌아갑니다.

1. 새 입력을 즉시 사용하지 못할 수 있습니다. **저장** 을 선택하고 그래픽 편집기를 닫은 다음, 그래픽 편집기를 다시 엽니다. 이제 새 입력을 사용할 수 있습니다.

1. `Start-AzVM` 활동을 선택한 다음, **매개 변수** 를 선택하여 **활동 매개 변수 구성** 페이지를 엽니다.

1. 이전에 구성된 매개 변수인 **RESOURCEGROUPNAME** 의 경우 **데이터 원본** 을 **Runbook 입력** 으로 변경한 다음, **ResourceGroupName** 을 선택합니다. **확인** 을 선택합니다.

1. 이전에 구성된 매개 변수인 **NAME** 의 경우 **데이터 원본** 을 **Runbook 입력** 으로 변경한 다음, **VMName** 을 선택합니다. **확인** 을 선택합니다. 이 페이지는 다음과 같이 표시됩니다.

    ![Start-AzVM 매개 변수](../media/automation-tutorial-runbook-graphical/start-azurermvm-params-runbookinput.png)

1. **확인** 을 선택하여 그래픽 편집기로 돌아갑니다.

1. **저장** 을 선택한 후 **테스트 창** 을 선택합니다. 이제 직접 만든 입력 변수 두 개의 값을 제공할 수 있는지 관찰합니다.

1. **테스트** 페이지를 닫고 그래픽 편집기로 돌아갑니다.

1. 새 버전의 Runbook을 게시할지 확인하는 메시지가 표시되면 **게시** 를 선택한 후 **예** 를 선택합니다. **Runbook** 개요 페이지로 돌아갑니다.

1. **시작** 을 선택하여 **Runbook 시작** 페이지를 엽니다.

1. `VMNAME` 및 `RESOURCEGROUPNAME` 매개 변수에 적절한 값을 입력합니다. 그런 다음, **확인** 을 선택합니다. **작업** 페이지가 열립니다.

1. 작업을 모니터링하고 **상태** 가 **완료** 로 전환된 후 VM이 시작되었는지 확인합니다. 그런 다음, 이후 단계를 위해 VM을 중지합니다.

1. Runbook의 그래픽 편집기로 돌아갑니다.

## <a name="create-a-conditional-link"></a>조건부 링크 만들기

이제 아직 시작되지 않은 VM만 시작하도록 Runbook을 수정할 수 있습니다. 이렇게 하려면 VM의 인스턴스 수준 상태를 검색하는 [Get-AzVM](/powershell/module/Az.Compute/Get-AzVM) cmdlet을 추가합니다. 그런 다음, VM 상태가 실행 중인지 또는 중지되었는지 여부를 확인하는 PowerShell 코드 조각이 있는 `Get Status`라는 PowerShell 워크플로 코드 모듈을 추가할 수 있습니다. 현재 실행 중인 상태가 중지되는 경우에만 `Get Status` 모듈의 조건부 링크에서 `Start-AzVM`을 실행합니다. 이 절차가 완료되면 Runbook에서 `Write-Output` cmdlet을 사용하여 VM이 성공적으로 시작되었는지 알려주는 메시지를 출력합니다.

1. 그래픽 편집기에서 `Specify Subscription Id`와 `Start-AzVM` 간 링크를 마우스 오른쪽 단추로 클릭하고 **삭제** 를 선택합니다.

1. **라이브러리 컨트롤** 검색 필드에서 `Get-AzVM`을 입력합니다.

1. `Get-AzVM`를 캔버스에 추가하고 활동을 `Specify Subscription Id` 아래로 끌어옵니다.

1. **구성 컨트롤** 에서 **매개 변수** 를 선택하여 **활동 매개 변수 구성** 페이지를 엽니다.

   **매개 변수 세트** 를 선택한 다음, **GetVirtualMachineInResourceGroupParamSet** 를 선택합니다. **활동 매개 변수 구성** 페이지에 이 매개 변수 세트의 매개 변수가 표시됩니다. 매개 변수 **RESOURCEGROUPNAME** 및 **NAME** 옆에 필수 매개 변수임을 나타내는 느낌표가 있습니다. 두 필드에는 모두 문자열 값이 필요합니다.

1. **RESOURCEGROUPNAME** 을 선택하여 **매개 변수 값** 페이지를 엽니다.
    1. **데이터 원본** 드롭다운 메뉴에서 **Runbook 입력** 을 선택합니다.
    1. 매개 변수 **ResourceGroupName** 을 선택합니다.
    1. **확인** 을 선택하여 **활동 매개 변수 구성** 페이지로 돌아갑니다.

1. **NAME** 을 선택하여 **매개 변수 값** 페이지를 엽니다.
    1. **데이터 원본** 드롭다운 메뉴에서 **Runbook 입력** 을 선택합니다.
    1. **VMName** 매개 변수를 선택합니다.
    1. **확인** 을 선택하여 **활동 매개 변수 구성** 페이지로 돌아갑니다.

1. **STATUS** 를 선택하여 **매개 변수 값** 페이지를 엽니다.
    1. **데이터 원본** 드롭다운 메뉴에서 **상수 값** 을 선택합니다.
    1. **True** 옵션을 선택합니다.
    1. **확인** 을 선택하여 **활동 매개 변수 구성** 페이지로 돌아갑니다.

1. **확인** 을 선택하여 그래픽 편집기로 돌아갑니다.

1. `Specify Subscription Id`와 `Get-AzVM` 간에 링크를 구성합니다.

1. **라이브러리 컨트롤** 검색 필드를 지운 다음, **RUNBOOK CONTROL** > **코드** 로 이동합니다. 줄임표를 선택한 다음, **캔버스에 추가** 를 선택합니다. 활동을 `Get-AzVM` 아래로 끌어옵니다.

1. **구성 컨트롤** 에서 **레이블** 을 `Code`에서 `Get Status`로 변경합니다.

1. **구성 컨트롤** 에서 **코드** 를 선택하여 **코드 편집기** 페이지를 엽니다.

1. 다음 코드 조각을 **PowerShell 코드** 텍스트 상자에 붙여넣습니다.

    ```powershell
    $Statuses = $ActivityOutput['Get-AzVM'].Statuses
    $StatusOut = ""
    foreach ($Status in $Statuses) {
      if($Status.Code -eq "Powerstate/running")
        {$StatusOut = "running"}
      elseif ($Status.Code -eq "Powerstate/deallocated")
        {$StatusOut = "stopped"}
    }
    $StatusOut
    ```

1. **확인** 을 선택하여 그래픽 편집기로 돌아갑니다.

1. `Get-AzVM`과 `Get Status` 간에 링크를 구성합니다.

1. `Get Status`와 `Start-AzVM` 간에 링크를 구성합니다. 이 시점에서 Runbook은 다음과 같습니다.

    ![코드 모듈이 포함된 Runbook](../media/automation-tutorial-runbook-graphical/runbook-startvm-get-status.png)  

1. `Get Status`와 `Start-AzVM` 간 링크를 선택합니다.

1. **구성 컨트롤** 에서 **조건 적용** 을 **예** 로 변경합니다. 링크가 파선이 되며, 이는 조건이 true로 확인되는 경우에만 대상 활동이 실행됨을 나타냅니다.  

1. **조건 식** 에 대해 `$ActivityOutput['Get Status'] -eq "Stopped"`를 입력합니다. 이제 VM이 중지되는 경우에만 `Start-AzVM`이 실행됩니다.

1. **라이브러리 컨트롤** 검색 필드에서 `Write-Output`을 입력합니다.

1. `Write-Output`를 캔버스에 추가하고 활동을 `Start-AzVM` 아래로 끌어옵니다.

1. 활동 줄임표를 선택하고 **복제** 를 선택합니다. 복제 활동을 첫 번째 활동의 오른쪽으로 끕니다.

1. 첫 번째 `Write-Output` 활동을 선택합니다.
    1. **구성 컨트롤** 에서 **레이블** 을 `Write-Output`에서 `Notify VM Started`로 변경합니다.
    1. **매개 변수** 를 선택하여 **활동 매개 변수 구성** 페이지를 엽니다.
    1. **INPUTOBJECT** 를 선택하여 **매개 변수 값** 페이지를 엽니다.
    1. **데이터 원본** 드롭다운 메뉴에서 **PowerShell 식** 을 선택합니다.
    1. **식** 텍스트 상자에 `"$VMName successfully started."`를 입력합니다.
    1. **확인** 을 선택하여 **활동 매개 변수 구성** 페이지로 돌아갑니다.
    1. **확인** 을 선택하여 그래픽 편집기로 돌아갑니다.

1. 첫 번째 `Write-Output1` 활동을 선택합니다.
    1. **구성 컨트롤** 에서 **레이블** 을 `Write-Output1`에서 `Notify VM Start Failed`로 변경합니다.
    1. **매개 변수** 를 선택하여 **활동 매개 변수 구성** 페이지를 엽니다.
    1. **INPUTOBJECT** 를 선택하여 **매개 변수 값** 페이지를 엽니다.
    1. **데이터 원본** 드롭다운 메뉴에서 **PowerShell 식** 을 선택합니다.
    1. **식** 텍스트 상자에 `"$VMName could not start."`를 입력합니다.
    1. **확인** 을 선택하여 **활동 매개 변수 구성** 페이지로 돌아갑니다.
    1. **확인** 을 선택하여 그래픽 편집기로 돌아갑니다.

1. `Start-AzVM`과 `Notify VM Started` 간에 링크를 구성합니다.

1. `Start-AzVM`과 `Notify VM Start Failed` 간에 링크를 구성합니다.

1. `Notify VM Started`의 링크를 선택하고 **조건 적용** 을 **예** 로 변경합니다.

1. **조건 식** 에 대해 `$ActivityOutput['Start-AzVM'].IsSuccessStatusCode -eq $true`를 입력합니다. 이제 이 `Write-Output` 컨트롤은 VM이 성공적으로 시작된 경우에만 실행됩니다.

1. `Notify VM Start Failed`의 링크를 선택합니다.

1. **컨트롤 페이지** 에서 **조건 적용** 에 대해 **예** 를 선택합니다.

1. **조건 식** 텍스트 상자에 `$ActivityOutput['Start-AzVM'].IsSuccessStatusCode -ne $true`를 입력합니다. 이 `Write-Output` 컨트롤은 이제 VM이 성공적으로 시작되지 않은 경우에만 실행됩니다. Runbook은 다음 이미지와 같습니다.

    ![Write-Output이 포함된 Runbook](../media/automation-tutorial-runbook-graphical/runbook-startazurermvm-complete.png)

1. Runbook을 저장하고 테스트 창을 엽니다.

1. VM이 중지된 상태에서 Runbook을 시작하고 머신이 시작됩니다.

## <a name="next-steps"></a>다음 단계

* 그래픽 작성에 대해 자세히 알아보려면 [Azure Automation에서 그래픽 Runbook 작성](../automation-graphical-authoring-intro.md)을 참조하세요.
* PowerShell Runbook을 시작하려면 [PowerShell Runbook 만들기](automation-tutorial-runbook-textual-powershell.md)를 참조하세요.
* PowerShell 워크플로 Runbook을 시작하려면 [PowerShell 워크플로 Runbook 만들기](automation-tutorial-runbook-textual.md)를 참조하세요.
* PowerShell cmdlet 참조는 [Az.Automation](/powershell/module/az.automation)을 참조하세요.
