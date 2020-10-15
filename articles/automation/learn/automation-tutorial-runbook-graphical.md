---
title: Azure Automation에서 그래픽 Runbook 만들기
description: 이 문서에서는 Azure Automation에서 간단한 그래픽 Runbook을 만들고, 테스트하고, 게시하는 방법을 설명합니다.
keywords: runbook, runbook 템플릿, runbook 자동화, azure runbook
services: automation
ms.subservice: process-automation
ms.date: 09/15/2020
ms.topic: tutorial
ms.openlocfilehash: 81dc23c208ca9fb292c849bdf35d8b91311ed9ce
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "90987655"
---
# <a name="tutorial-create-a-graphical-runbook"></a>자습서: 그래픽 Runbook 만들기

이 자습서는 Azure Automation에서 [그래픽 Runbook](../automation-runbook-types.md#graphical-runbooks)을 만드는 과정을 안내합니다. Azure Portal에서 그래픽 편집기를 사용하여 그래픽 및 그래픽 PowerShell 워크플로 Runbook을 만들고 편집할 수 있습니다. 

이 자습서에서는 다음 작업 방법을 알아봅니다.

> [!div class="checklist"]
> * 간단한 그래픽 Runbook 만들기
> * Runbook 테스트 및 게시
> * Runbook 작업 실행 및 상태 추적
> * Runbook 매개 변수 및 조건부 링크를 사용하여 Azure 가상 머신을 시작하도록 Runbook 업데이트

## <a name="prerequisites"></a>사전 요구 사항

이 자습서를 완료하려면 다음이 필요합니다.

* 동작합니다. 구독이 아직 없는 경우 [MSDN 구독자 혜택을 활성화](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)하거나 [무료 계정](https://azure.microsoft.com/free)을 등록할 수 있습니다.
* [Automation 계정](../index.yml) . 이 계정은 가상 머신을 시작하고 중지할 수 있는 권한이 있어야 합니다.
* Azure 가상 머신. 이 머신을 중지하고 시작하므로 프로덕션 VM이 아니어야 합니다.
* 필요한 경우 사용하는 cmdlet에 따라 [Azure 모듈을 가져오거나](../shared-resources/modules.md) [모듈을 업데이트](../automation-update-azure-modules.md)합니다.

## <a name="step-1---create-runbook"></a>1단계 - Runbook 만들기

먼저 `Hello World`라는 텍스트를 출력하는 간단한 Runbook을 만듭니다.

1. Azure Portal에서 Automation 계정을 엽니다.

    Automation 계정 페이지는 이 계정의 리소스 간략히 보기를 제공합니다. 사용자에게는 이미 일부 자산이 있어야 합니다. 이러한 자산의 대부분은 새 Automation 계정에 자동으로 포함되는 모듈입니다. 또한 구독과 연결된 자격 증명 자산이 있어야 합니다.

2. **프로세스 자동화** 아래에서 **Runbook**을 선택하여 Runbook 목록을 엽니다.

3. **Runbook 만들기**를 선택하여 새 Runbook을 만듭니다.

4. Runbook 이름을 **MyFirstRunbook-Graphical**로 지정합니다.

5. 여기서는 [그래픽 Runbook](../automation-graphical-authoring-intro.md)을 만듭니다. **Runbook 형식**에 대해 **그래픽**을 선택합니다.

    ![새 runbook](../media/automation-tutorial-runbook-graphical/create-new-runbook.png)

6. **만들기** 를 클릭하여 Runbook을 만들고 그래픽 편집기를 엽니다.

## <a name="step-2---add-activities"></a>2단계 - 활동 추가

편집기의 왼쪽에 있는 라이브러리 컨트롤은 사용자의 runbook에 추가할 활동을 선택할 수 있게 해줍니다. Runbook에서 텍스트를 출력하는 `Write-Output` cmdlet을 추가합니다.

1. [라이브러리] 컨트롤에서 검색 필드를 클릭하고, `write-output`을 입력합니다. 검색 결과는 다음 이미지에 나와 있습니다.

    ![Microsoft.PowerShell.Utility](../media/automation-tutorial-runbook-graphical/search-powershell-cmdlet-writeoutput.png)

2. 목록의 아래로 스크롤하십시오. 마우스 오른쪽 단추로 **Write-Output**을 클릭하고, **캔버스에 추가**를 선택합니다. 또는 cmdlet 이름 옆의 줄임표(...)를 클릭한 다음, **캔버스에 추가**를 선택할 수 있습니다.

3. 캔버스에서 **Write-Output** 활동을 클릭합니다. 이 작업을 통해 구성 제어 페이지가 열리고, 여기서 작업을 구성할 수 있습니다.

4. **레이블** 필드는 기본적으로 cmdlet 이름으로 설정되지만 더 친숙한 이름으로 변경할 수 있습니다. `Write Hello World to output`으로 변경합니다.

5. **매개 변수**를 클릭하여 cmdlet 매개 변수의 값을 제공합니다.

   일부 cmdlet에는 여러 매개 변수 세트가 있으며, 사용할 매개 변수 세트를 선택해야 합니다. 여기서는 `Write-Output`에 하나의 매개 변수 세트만 있습니다.

6. `InputObject` 매개 변수를 선택합니다. 이는 출력 스트림으로 보낼 텍스트를 지정하는 데 사용하는 매개 변수입니다.

7. **데이터 원본** 드롭다운 메뉴는 매개 변수 값을 채우는 데 사용할 수 있는 원본을 제공합니다. 이 메뉴에서 **PowerShell 식**을 선택합니다.

   다른 활동, Automation 자산 또는 PowerShell 식과 같은 원본의 출력을 사용할 수 있습니다. 여기서는 출력이 `Hello World`입니다. PowerShell 식을 사용하고 문자열을 지정할 수 있습니다.

8. **식** 필드에서 `Hello World`를 입력한 다음, **확인**을 두 번 클릭하여 캔버스로 돌아갑니다.

9. **저장**을 클릭하여 Runbook을 저장합니다.

## <a name="step-3---test-the-runbook"></a>3 단계 - runbook 테스트

프로덕션 환경에서 사용할 수 있도록 Runbook을 게시하기 전에 먼저 해당 Runbook을 테스트하여 제대로 작동하는지 확인해야 합니다. Runbook을 테스트하면 초안 버전이 실행되고 출력을 대화형으로 볼 수 있습니다.

1. **테스트 창**을 선택하여 [테스트] 창을 엽니다.

2. **시작** 을 클릭하여 테스트를 시작합니다. 유일하게 사용 가능한 옵션이어야 합니다.

3. [Runbook 작업](../automation-runbook-execution.md)이 만들어지고 해당 상태가 창에 표시됩니다.

   작업 상태는 `Queued`로 시작됩니다. 이는 클라우드에서 runbook worker를 사용할 수 있을 때까지 작업에서 기다리고 있음을 나타냅니다. 작업자에서 작업을 요청하면 상태가 `Starting`으로 변경됩니다. 마지막으로 Runbook이 실제로 실행되기 시작하면 상태가 `Running`이 됩니다.

4. Runbook 작업이 완료되면 [테스트] 창에 해당 출력이 표시됩니다. 여기서는 `Hello World`가 표시됩니다.

    ![Hello World Runbook 출력](../media/automation-tutorial-runbook-graphical/runbook-test-results.png)

5. 캔버스로 돌아가려면 테스트 창을 닫습니다.

## <a name="step-4---publish-and-start-the-runbook"></a>4 단계 - runbook 게시 및 시작

방금 만든 Runbook은 아직 초안 모드입니다. 프로덕션 환경에서 실행하기 전에 먼저 게시해야 합니다. Runbook을 게시하면 초안 버전으로 기존의 게시된 버전을 덮어씁니다. 이 경우에 Runbook을 방금 만들었으므로 아직 게시된 버전이 없습니다.

1. **게시**를 선택하여 Runbook을 게시한 다음, 확인 메시지가 표시되면 **예**를 선택합니다.

2. Runbook 페이지에서 왼쪽으로 스크롤하여 Runbook을 살펴보고, **작성 상태** 값이 **게시됨**으로 설정되어 있는지 확인합니다.

3. 오른쪽으로 다시 스크롤하면 **MyFirstRunbook-Graphical** 페이지가 표시됩니다.

   위쪽의 옵션을 사용하면 지금 Runbook을 시작하거나, 향후의 시작 시간을 예약하거나, [웹후크](../automation-webhooks.md)를 만들어 HTTP 호출을 통해 Runbook을 시작할 수 있습니다.

4. **시작**을 선택하고 Runbook을 시작할지 확인하는 메시지가 표시되면 **예**를 선택합니다.

5. 만든 Runbook 작업에 대한 [작업] 창이 열립니다. **작업 상태** 필드가 **완료됨**으로 표시되는지 확인합니다.

6. **출력**을 클릭하여 [출력] 페이지를 엽니다. 여기서는 `Hello World`가 표시됩니다.

7. 출력 페이지를 닫습니다.

8. **모든 로그** 를 클릭하여 Runbook 작업에 대한 스트림 창을 엽니다. 출력 스트림에는 `Hello World`만 표시됩니다.

    Runbook에서 [스트림] 창에 기록하는 경우 자세한 정보 및 오류 스트림과 같은 Runbook 작업에 대한 다른 스트림이 표시될 수 있습니다.

9. 스트림 창과 작업 창을 닫고 MyFirstRunbook-Graphical 페이지로 돌아갑니다.

10. Runbook에 대한 모든 작업을 보려면 **리소스** 아래에서 **작업**을 선택합니다. 작업 페이지에는 Runbook에서 만든 모든 작업이 나열됩니다. 작업을 한 번만 실행했으므로 작업이 하나만 나열되어 있어야 합니다.

11. 작업 이름을 클릭하여 Runbook을 시작할 때 표시된 것과 동일한 [작업] 창을 엽니다. 이 창을 사용하여 Runbook에 대해 만들어진 작업의 세부 정보를 볼 수 있습니다.

## <a name="step-5---create-variable-assets"></a>5 단계 - 변수 자산 만들기

Runbook을 테스트하고 게시했지만 지금까지는 Azure 리소스를 관리하는 데 유용한 작업을 수행하지 않았습니다. 인증하도록 Runbook을 구성하기 전에 먼저 구독 ID를 보유할 변수를 만들고, 인증 활동을 설정한 다음, 변수를 참조해야 합니다. 구독 컨텍스트에 대한 참조를 포함하면 여러 구독을 쉽게 사용할 수 있습니다.

1. [탐색] 창에서 **구독** 옵션의 구독 ID를 복사합니다.

2. [Automation 계정] 페이지의 **공유 리소스** 아래에서 **변수**를 선택합니다.

3. **변수 추가**를 선택합니다.

4. [새 변수] 페이지에 제공된 필드에서 다음 설정을 지정합니다.

    * **이름** - `AzureSubscriptionId`를 입력합니다.
    * **값** - 구독 ID를 입력합니다.
    * **형식** - 선택된 문자열을 그대로 유지합니다.
    * **암호화** - 기본값을 사용합니다.

5. **만들기** 를 클릭하여 변수를 만듭니다.

## <a name="step-6---add-authentication"></a>6단계 - 인증 추가

이제 구독 ID를 보유할 변수가 있으므로 구독에 대한 실행 자격 증명을 사용하여 인증하도록 Runbook을 구성할 수 있습니다. 이렇게 하려면 Azure 실행 연결을 자산으로 추가합니다. 또한 [Connect-AzAccount](/powershell/module/az.accounts/Connect-AzAccount) cmdlet 및 [Set-AzContext](/powershell/module/az.accounts/Set-AzContext) cmdlet을 캔버스에 추가해야 합니다.

>[!NOTE]
>PowerShell Runbook의 경우 `Add-AzAccount` 및 `Add-AzureRMAccount`는 `Connect-AzAccount`에 대한 별칭입니다. 이러한 별칭은 그래픽 Runbook에서 사용할 수 없습니다. `Connect-AzAccount` 자체만 그래픽 Runbook에서 사용할 수 있습니다.

1. Runbook으로 이동하고, MyFirstRunbook-Graphical 페이지에서 **편집**을 선택합니다.

2. `Write Hello World to output` 항목은 더 이상 필요하지 않습니다. 줄임표를 클릭하고 **삭제**를 선택하면 됩니다.

3. [라이브러리] 컨트롤에서 **자산**, **연결**을 차례로 펼칩니다. **캔버스에 추가**를 선택하여 `AzureRunAsConnection`을 캔버스에 추가합니다.

4. `AzureRunAsConnection`의 이름을 `Get Run As Connection`으로 바꿉니다.

5. [라이브러리] 컨트롤의 검색 필드에서 `Connect-AzAccount`를 입력합니다.

6. `Connect-AzAccount`을 캔버스에 추가합니다.

7. 도형 아래쪽에 원이 나타날 때까지 마우스로 `Get Run As Connection` 위를 가리킵니다. 원을 클릭하고, 화살표를 `Connect-AzAccount`로 끌어 링크를 만듭니다. Runbook에서 `Get Run As Connection`을 사용하여 시작한 다음, `Connect-AzAccount`를 실행합니다.

    ![활동 간 링크 만들기](../media/automation-tutorial-runbook-graphical/runbook-link-auth-activities.png)

8. 캔버스에서 `Connect-AzAccount`를 선택합니다. [구성] 제어 창의 **레이블** 필드에서 **Azure에 로그인**을 입력합니다.

9. **매개 변수**를 클릭합니다. 그러면 [활동 매개 변수 구성] 페이지가 표시됩니다.

10. `Connect-AzAccount` cmdlet에는 여러 매개 변수 세트가 있으며, 매개 변수 값을 제공하기 전에 먼저 매개 변수 세트를 선택해야 합니다. **매개 변수 세트**를 클릭한 다음, **ServicePrincipalCertificateWithSubscriptionId**를 선택합니다.

11. [활동 매개 변수 구성] 페이지에 이 매개 변수 세트의 매개 변수가 표시됩니다. **APPLICATIONID**를 클릭합니다.

    ![Azure 계정 매개 변수 추가](../media/automation-tutorial-runbook-graphical/Add-AzureRmAccount-params.png)

12. [매개 변수 값] 페이지에서 다음 설정을 지정한 다음, **확인**을 클릭합니다.

   * **데이터 원본** - **활동 출력**을 선택합니다.
   * 데이터 원본 목록 - **Automation 연결 가져오기**를 선택합니다.
   * **필드 경로** - `ApplicationId`를 입력합니다. 활동에서 여러 속성이 있는 개체를 출력하므로 필드 경로에 대한 속성의 이름을 지정합니다.

13. **CERTIFICATETHUMBPRINT**를 클릭하고, [매개 변수 값] 페이지에서 다음 설정을 지정한 다음, **확인**을 클릭합니다.

    * **데이터 원본** - **활동 출력**을 선택합니다.
    * 데이터 원본 목록 - **Automation 연결 가져오기**를 선택합니다.
    * **필드 경로** - `CertificateThumbprint`를 입력합니다.

14. **SERVICEPRINCIPAL**을 클릭하고, [매개 변수 값] 페이지에서 **데이터 원본** 필드에 대해 **ConstantValue**를 선택합니다. **True** 옵션을 클릭한 다음, **확인**을 클릭합니다.

15. **TENANTID**를 클릭하고, [매개 변수 값] 페이지에서 다음 설정을 지정합니다. 완료되면 **확인**을 두 번 클릭합니다.

    * **데이터 원본** - **활동 출력**을 선택합니다.
    * 데이터 원본 목록 - **Automation 연결 가져오기**를 선택합니다.
    * **필드 경로** - `TenantId`를 입력합니다.

16. [라이브러리] 컨트롤의 검색 필드에서 `Set-AzContext`를 입력합니다.

17. `Set-AzContext`를 캔버스에 추가합니다.

18. 캔버스에서 `Set-AzContext`를 선택합니다. [구성] 제어 창의 **레이블** 필드에서 `Specify Subscription Id`를 입력합니다.

19. **매개 변수**를 클릭하면 활동 매개 변수 구성 페이지가 표시됩니다.

20. `Set-AzContext` cmdlet에는 여러 매개 변수 세트가 있으며, 매개 변수 값을 제공하기 전에 먼저 매개 변수 세트를 선택해야 합니다. **매개 변수 세트**를 클릭한 다음, **SubscriptionId**를 선택합니다.

21. [활동 매개 변수 구성] 페이지에 이 매개 변수 세트의 매개 변수가 표시됩니다. **SubscriptionID**를 클릭합니다.

22. [매개 변수 값] 페이지에서 **데이터 원본** 필드에 대해 **변수 자산**을 선택하고, 원본 목록에서 **AzureSubscriptionId**를 선택합니다. 완료되면 **확인**을 두 번 클릭합니다.

23. 도형 아래쪽에 원이 나타날 때까지 마우스로 `Login to Azure` 위를 가리킵니다. 원을 클릭하고, 화살표를 `Specify Subscription Id`로 끕니다. 이 시점에서 Runbook은 다음과 같습니다.

    :::image type="content" source="../media/automation-tutorial-runbook-graphical/runbook-auth-config.png" alt-text="화살표를 '구독 ID 지정'으로 끌어온 후에 Runbook의 스크린샷.":::

## <a name="step-7---add-activity-to-start-a-virtual-machine"></a>7 단계 - 가상 머신을 시작하는 활동 추가

이제 가상 머신을 시작하는 `Start-AzVM` 활동을 추가해야 합니다. Azure 구독에서 VM을 선택할 수 있지만, 지금은 해당 이름을 [Start-AzVM](/powershell/module/az.compute/start-azvm) cmdlet에 하드 코딩합니다.

1. [라이브러리] 컨트롤의 검색 필드에서 `Start-Az`를 입력합니다.

2. `Start-AzVM`을 캔버스에 추가한 다음, 이를 클릭하여 `Specify Subscription Id` 아래로 끕니다.

3. 도형 아래쪽에 원이 나타날 때까지 마우스로 `Specify Subscription Id` 위를 가리킵니다. 원을 클릭하고, 화살표를 `Start-AzVM`으로 끕니다.

4. `Start-AzVM`를 선택합니다. **매개 변수**를 클릭한 다음, **매개 변수 세트**를 클릭하여 활동에 대한 세트를 봅니다.

5. 매개 변수 세트에 대해 **ResourceGroupNameParameterSetName**을 선택합니다. **ResourceGroupName** 및 **Name** 필드 옆에 필수 매개 변수임을 나타내는 느낌표가 있습니다. 두 필드 모두에는 문자열 값이 필요합니다.

6. **Name**을 선택합니다. **데이터 원본** 필드에 대해 **PowerShell 식**을 선택합니다. 이 Runbook을 시작하는 데 사용하는 VM의 경우 큰 따옴표로 묶은 머신 이름을 입력합니다. **확인**을 클릭합니다.

7. **ResourceGroupName**을 선택합니다. **데이터 원본** 필드에 대해 **PowerShell 식** 값을 사용하고, 큰 따옴표로 묶은 리소스 그룹 이름을 입력합니다. **확인**을 클릭합니다.

8. Runbook을 테스트할 수 있도록 **테스트 창**을 클릭합니다.

9. **시작**을 클릭하여 테스트를 시작합니다. 완료되면 VM이 시작되었는지 확인합니다. 이 시점에서 Runbook은 다음과 같습니다.

    ![Runbook Start-AzVM 출력](../media/automation-tutorial-runbook-graphical/runbook-startvm.png)

## <a name="step-8---add-additional-input-parameters"></a>8단계 - 추가 입력 매개 변수 추가

Runbook은 현재 `Start-AzVM` cmdlet에 지정한 리소스 그룹의 VM을 시작합니다. Runbook이 시작될 때 이름과 리소스 그룹을 모두 지정하는 경우 Runbook이 더 유용합니다. 입력 매개 변수를 Runbook에 추가하여 해당 기능을 제공해 보겠습니다.

1. MyFirstRunbook-Graphical 페이지에서 **편집**을 클릭하여 그래픽 편집기를 엽니다.

2. **입력 및 출력**과 **입력 추가**를 차례로 선택하여 Runbook 입력 매개 변수 창을 엽니다.

3. 제공된 필드에서 다음 설정을 지정한 다음, **확인**을 클릭합니다.
   * **이름** - `VMName`을 지정합니다.
   * **형식** - 문자열 설정을 유지합니다.
   * **필수** - 값을 **예**로 변경합니다.

4. `ResourceGroupName`이라는 두 번째 필수 입력 매개 변수를 만든 다음, **확인**을 클릭하여 [입력 및 출력] 창을 닫습니다.

    ![Runbook 입력 매개 변수](../media/automation-tutorial-runbook-graphical/start-azurermvm-params-outputs.png)

5. `Start-AzVM` 활동을 선택한 다음, **매개 변수**를 클릭합니다.

6. **이름**에 대한 **데이터 원본** 필드를 **Runbook 입력**으로 변경합니다. 그런 다음, **VMName**을 선택합니다.

7. **ResourceGroupName**에 대한 **데이터 원본** 필드를 **Runbook 입력**으로 변경한 다음, **ResourceGroupName**을 선택합니다.

    ![Start-AzVM 매개 변수](../media/automation-tutorial-runbook-graphical/start-azurermvm-params-runbookinput.png)

8. Runbook을 저장하고 테스트 창을 엽니다. 이제 테스트에서 사용하는 두 입력 변수에 대한 값을 제공할 수 있습니다.

9. 창을 닫습니다.

10. **게시** 를 클릭하여 Runbook의 새 버전을 게시합니다.

11. 이전에 시작한 VM을 중지합니다.

12. **시작** 을 클릭하여 runbook을 시작합니다. 시작할 VM에 대한 `VMName` 및 `ResourceGroupName` 값을 입력합니다.

13. Runbook이 완료되면 VM이 시작되었는지 확인합니다.

## <a name="step-9---create-a-conditional-link"></a>9 단계 - 조건부 링크 만들기

이제 아직 시작되지 않은 VM만 시작하도록 Runbook을 수정할 수 있습니다. 이렇게 하려면 VM의 인스턴스 수준 상태를 검색하는 [Get-AzVM](/powershell/module/Az.Compute/Get-AzVM) cmdlet을 추가합니다. 그런 다음, VM 상태가 실행 중인지 또는 중지되었는지 여부를 확인하는 PowerShell 코드 조각이 있는 `Get Status`라는 PowerShell 워크플로 코드 모듈을 추가할 수 있습니다. 현재 실행 중인 상태가 중지되는 경우에만 `Get Status` 모듈의 조건부 링크에서 `Start-AzVM`을 실행합니다. 이 절차가 완료되면 Runbook에서 `Write-Output` cmdlet을 사용하여 VM이 성공적으로 시작되었는지 알려주는 메시지를 출력합니다.

1. 그래픽 편집기에서 **MyFirstRunbook-Graphical**을 엽니다.

2. `Specify Subscription Id` 및 `Start-AzVM` 사이의 링크를 클릭한 다음, **삭제**를 눌러 해당 링크를 제거합니다.

3. [라이브러리] 컨트롤의 검색 필드에서 `Get-Az`를 입력합니다.

4. `Get-AzVM`을 캔버스에 추가합니다.

5. `Get-AzVM`을 선택한 다음, **매개 변수 세트**를 클릭하여 cmdlet에 대한 세트를 봅니다.

6. **GetVirtualMachineInResourceGroupNameParamSet** 매개 변수 집합을 선택합니다. **ResourceGroupName** 및 **Name** 필드 옆에 필수 매개 변수를 지정했음을 나타내는 느낌표가 있습니다. 두 필드 모두에는 문자열 값이 필요합니다.

7. **Name**에 대한 **데이터 원본** 아래에서 **Runbook 입력**을 선택한 다음, **VMName**을 선택합니다. **확인**을 클릭합니다.

8. **ResourceGroupName**에 대한 **데이터 원본** 아래에서 **Runbook 입력**을 선택한 다음, **ResourceGroupName**을 선택합니다. **확인**을 클릭합니다.

9. **Status**에 대한 **데이터 원본** 아래에서 **상수 값**을 선택한 다음, **True**를 선택합니다. **확인**을 클릭합니다.

10. `Specify Subscription Id`에서 `Get-AzVM`으로의 링크를 만듭니다.

11. [라이브러리] 컨트롤에서 **Runbook 컨트롤**을 펼치고, **코드**를 캔버스에 추가합니다.  

12. `Get-AzVM`에서 `Code`로의 링크를 만듭니다.  

13. `Code`를 클릭하고, [구성] 창에서 레이블을 **상태 가져오기**로 변경합니다.

14. `Code`를 선택합니다. 그러면 코드 편집기 페이지가 표시됩니다.  

15. 다음 코드 조각을 편집기 페이지에 붙여넣습니다.

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

16. `Get Status`에서 `Start-AzVM`으로의 링크를 만듭니다.

    ![코드 모듈이 포함된 Runbook](../media/automation-tutorial-runbook-graphical/runbook-startvm-get-status.png)  

17. 링크를 선택하고, [구성] 창에서 **조건 적용**을 **예**로 변경합니다. 링크가 파선이 되며, 이는 조건이 true로 확인되는 경우에만 대상 활동이 실행됨을 나타냅니다.  

18. **조건 식**에 대해 `$ActivityOutput['Get Status'] -eq "Stopped"`를 입력합니다. 이제 VM이 중지되는 경우에만 `Start-AzVM`이 실행됩니다.

19. 라이브러리 컨트롤에서 **Cmdlet**과 **Microsoft.PowerShell.Utility**를 차례로 확장합니다.

20. `Write-Output`을 캔버스에 두 번 추가합니다.

21. 첫 번째 `Write-Output` 컨트롤에서 **매개 변수**를 클릭하고, **레이블** 값을 **VM 시작 알림**으로 변경합니다.

22. **InputObject**에 대해 **데이터 원본**을 **PowerShell 식**으로 변경하고, `$VMName successfully started.` 식을 입력합니다.

23. 두 번째 `Write-Output` 컨트롤에서 **매개 변수**를 클릭하고, **레이블** 값을 **VM 시작 실패 알림**으로 변경합니다.

24. **InputObject**에 대해 **데이터 원본**을 **PowerShell 식**으로 변경하고, `$VMName could not start` 식을 입력합니다.

25. `Start-AzVM`에서 `Notify VM Started` 및 `Notify VM Start Failed`로의 링크를 만듭니다.

26. `Notify VM Started`에 대한 링크를 선택하고, **조건 적용**을 true로 변경합니다.

27. **조건 식**에 대해 `$ActivityOutput['Start-AzVM'].IsSuccessStatusCode -eq $true`를 입력합니다. 이제 이 `Write-Output` 컨트롤은 VM이 성공적으로 시작된 경우에만 실행됩니다.

28. `Notify VM Start Failed`에 대한 링크를 선택하고, **조건 적용**을 true로 변경합니다.

29. **조건 식**에 대해 `$ActivityOutput['Start-AzVM'].IsSuccessStatusCode -ne $true`를 입력합니다. 이 `Write-Output` 컨트롤은 이제 VM이 성공적으로 시작되지 않은 경우에만 실행됩니다. Runbook은 다음 이미지와 같습니다.

    ![Write-Output이 포함된 Runbook](../media/automation-tutorial-runbook-graphical/runbook-startazurermvm-complete.png)

30. Runbook을 저장하고 테스트 창을 엽니다.

31. VM이 중지된 상태에서 Runbook을 시작하고 머신이 시작됩니다.

## <a name="next-steps"></a>다음 단계

* 그래픽 작성에 대해 자세히 알아보려면 [Azure Automation에서 그래픽 Runbook 작성](../automation-graphical-authoring-intro.md)을 참조하세요.
* PowerShell Runbook을 시작하려면 [PowerShell Runbook 만들기](automation-tutorial-runbook-textual-powershell.md)를 참조하세요.
* PowerShell 워크플로 Runbook을 시작하려면 [PowerShell 워크플로 Runbook 만들기](automation-tutorial-runbook-textual.md)를 참조하세요.
* PowerShell cmdlet 참조는 [Az.Automation](/powershell/module/az.automation)을 참조하세요.
