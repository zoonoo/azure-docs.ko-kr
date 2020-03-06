---
title: Azure Automation의 내 첫 번째 그래픽 Runbook
description: 자습서는 간단한 그래픽 runbook의 생성, 테스트, 게시를 단계별로 안내합니다.
keywords: runbook, runbook 템플릿, runbook 자동화, azure runbook
services: automation
ms.subservice: process-automation
ms.date: 04/13/2018
ms.topic: conceptual
ms.openlocfilehash: 6a967f328a4fbe17f2c451d35f413bd7fdcbc24a
ms.sourcegitcommit: 021ccbbd42dea64d45d4129d70fff5148a1759fd
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/05/2020
ms.locfileid: "78331471"
---
# <a name="my-first-graphical-runbook"></a>내 첫 번째 그래픽 Runbook

> [!div class="op_single_selector"]
> * [그래픽](automation-first-runbook-graphical.md)
> * [PowerShell](automation-first-runbook-textual-powershell.md)
> * [PowerShell 워크플로](automation-first-runbook-textual.md)
> * [Python](automation-first-runbook-textual-python2.md)
> 

이 자습서는 Azure Automation에서 [그래픽 Runbook](automation-runbook-types.md#graphical-runbooks)을 만드는 과정을 안내합니다. Runbook 작업의 상태를 추적 하는 방법을 배우면 서 테스트 하 고 게시할 수 있는 간단한 runbook으로 시작 합니다. 그런 다음 실제로 Azure 리소스를 관리 하도록 runbook을 수정 합니다 .이 경우에는 Azure 가상 컴퓨터를 시작 합니다. Runbook 매개 변수와 조건부 링크를 추가 하 여 runbook을 더욱 강력 하 게 만드는 자습서를 완료 합니다.

>[!NOTE]
>이 문서는 새 Azure PowerShell Az 모듈을 사용하도록 업데이트되었습니다. AzureRM 모듈은 적어도 2020년 12월까지 버그 수정을 수신할 예정이므로 계속 사용하셔도 됩니다. 새 Az 모듈 및 AzureRM 호환성에 대한 자세한 내용은 [새 Azure PowerShell Az 모듈 소개](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0)를 참조하세요. Hybrid Runbook Worker에 대 한 Az module 설치 지침은 [Azure PowerShell 모듈 설치](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)를 참조 하세요. Automation 계정의 경우 [Azure Automation에서 Azure PowerShell 모듈을 업데이트 하는 방법을](automation-update-azure-modules.md)사용 하 여 모듈을 최신 버전으로 업데이트할 수 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

이 자습서를 완료하려면 다음이 필요합니다.

* 동작합니다. 구독이 아직 없는 경우 [MSDN 구독자 혜택을 활성화](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)하거나 [무료 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 등록할 수 있습니다.
* [Automation 계정](automation-offering-get-started.md) . 이 계정은 가상 머신을 시작하고 중지할 수 있는 권한이 있어야 합니다.
* Azure 가상 머신. 이 컴퓨터를 중지 하 고 시작 했으므로 프로덕션 VM이 아니어야 합니다.

## <a name="step-1---create-runbook"></a>1단계 - Runbook 만들기

먼저 **Hello World**라는 텍스트를 출력하는 간단한 Runbook을 만듭니다.

1. Azure Portal에서 Automation 계정을 엽니다. 

    Automation 계정 페이지는 이 계정의 리소스 간략히 보기를 제공합니다. 사용자에게는 이미 일부 자산이 있어야 합니다. 이러한 자산의 대부분은 새 Automation 계정에 자동으로 포함 되는 모듈입니다. 또한 구독과 연결 된 자격 증명 자산이 있어야 합니다.
2. **프로세스 자동화** 아래에서 **runbook** 을 선택 하 여 runbook 목록을 엽니다.
3. **Runbook 만들기**를 선택 하 여 새 runbook을 만듭니다.
4. Runbook 이름을 **MyFirstRunbook-Graphical**로 지정합니다.
5. 이 경우 [그래픽 runbook](automation-graphical-authoring-intro.md)을 만들 수 있습니다. **Runbook 형식**으로 **그래픽** 을 선택 합니다.<br> ![새 runbook](media/automation-first-runbook-graphical/create-new-runbook.png)<br>
6. **만들기** 를 클릭하여 Runbook을 만들고 그래픽 편집기를 엽니다.

## <a name="step-2---add-activities"></a>2 단계-작업 추가

편집기의 왼쪽에 있는 라이브러리 컨트롤은 사용자의 runbook에 추가할 활동을 선택할 수 있게 해줍니다. **Write-Output** cmdlet을 추가하여 Runbook의 텍스트를 출력합니다.

1. 라이브러리 컨트롤에서 검색 필드를 클릭 하 고 **write output**을 입력 합니다. 검색 결과는 다음 그림에 나와 있습니다. <br> ![Microsoft.PowerShell.Utility](media/automation-first-runbook-graphical/search-powershell-cmdlet-writeoutput.png)
1. 목록의 아래로 스크롤하십시오. **쓰기-출력** 을 마우스 오른쪽 단추로 클릭 하 고 **캔버스에 추가를**선택 합니다. 또는 cmdlet 이름 옆에 있는 줄임표 (...)를 클릭 하 고 **캔버스에 추가**를 선택할 수도 있습니다.
1. 캔버스에서 **Write-Output** 활동을 클릭합니다. 이 작업을 통해 구성 제어 페이지가 열리고, 여기서 작업을 구성할 수 있습니다.
1. **레이블** 필드의 기본값은 cmdlet의 이름 이지만 보다 친숙 한 항목으로 변경할 수 있습니다. **Hello World 출력을 위해 쓰기**로 바꿉니다.
1. **매개 변수**를 클릭하여 cmdlet 매개 변수의 값을 제공합니다.

   일부 cmdlet에는 여러 매개 변수 집합이 있으며 사용할 하나를 선택 해야 합니다. 이 경우 **쓰기 출력** 에는 하나의 매개 변수 집합만 있습니다.

1. *InputObject* 매개 변수를 선택합니다. 이 매개 변수는 출력 스트림으로 보낼 텍스트를 지정 하는 데 사용 됩니다.
1. **데이터 원본** 드롭다운 메뉴는 매개 변수 값을 채우는 데 사용할 수 있는 소스를 제공 합니다. 이 메뉴에서 **PowerShell 식**을 선택 합니다. 

   이러한 원본의 출력을 다른 작업, 자동화 자산 또는 PowerShell 식으로 사용할 수 있습니다. 이 경우에 바로 **Hello World**가 출력됩니다. PowerShell 식을 사용하고 문자열을 지정할 수 있습니다.<br>

1. **식** 필드에 **Hello World** 를 입력 한 다음 **확인** 을 두 번 클릭 하 여 캔버스로 돌아갑니다.
1. **저장**을 클릭하여 Runbook을 저장합니다.

## <a name="step-3---test-the-runbook"></a>3 단계 - runbook 테스트

프로덕션 환경에서 사용할 수 있도록 runbook을 게시 하기 전에 테스트 하 여 제대로 작동 하는지 확인 해야 합니다. Runbook 테스트는 초안 버전을 실행 하 고 해당 출력을 대화형으로 볼 수 있도록 합니다.

1. 테스트 **창** 을 선택 하 여 테스트 창을 엽니다.
1. **시작** 을 클릭하여 테스트를 시작합니다. 유일하게 사용 가능한 옵션이어야 합니다.
1. [Runbook 작업이](automation-runbook-execution.md) 만들어지고 해당 상태가 창에 표시 됩니다.

   작업 상태는 큐에 **대기**중으로 시작 되며, 작업에서 클라우드의 runbook worker를 사용할 수 있을 때까지 기다리고 있음을 나타냅니다. 작업 자가 작업을 클레임 할 때 상태가 **시작 중** 으로 변경 됩니다. 마지막으로 runbook이 실제로 실행 되기 시작 하면 상태가 **실행 중** 으로 바뀝니다.

1. Runbook 작업이 완료 되 면 테스트 창에 해당 출력이 표시 됩니다. 이 경우에 **Hello World**가 표시됩니다.<br> ![Hello World](media/automation-first-runbook-graphical/runbook-test-results.png)
1. 캔버스로 돌아가려면 테스트 창을 닫습니다.

## <a name="step-4---publish-and-start-the-runbook"></a>4 단계 - runbook 게시 및 시작

만든 runbook은 아직 초안 모드입니다. 프로덕션 환경에서 실행하기 전에 먼저 게시해야 합니다. Runbook을 게시하면 초안 버전으로 기존의 게시된 버전을 덮어씁니다. 이 경우에 Runbook을 방금 만들었으므로 아직 게시된 버전이 없습니다.

1. **게시**를 선택하여 Runbook을 게시한 다음, 확인 메시지가 표시되면 **예**를 선택합니다.
1. 왼쪽으로 스크롤하여 runbook 페이지에서 runbook을 확인 하 고 **제작 상태** 값이 **게시**됨으로 설정 되어 있는지 확인 합니다.
1. 오른쪽으로 다시 스크롤하면 **MyFirstRunbook-Graphical** 페이지가 표시됩니다.

   위쪽의 옵션을 사용 하 여 runbook을 시작 하거나, 이후 시작 시간을 예약 하거나, HTTP 호출을 통해 runbook을 시작할 수 있도록 [webhook](automation-webhooks.md) 를 만들 수 있습니다.

1. **시작**을 선택하고 Runbook을 시작할지 확인하는 메시지가 표시되면 **예**를 선택합니다.
1. 만든 runbook 작업에 대 한 작업 창이 열립니다. **작업 상태** 필드가 **완료 됨**으로 표시 되는지 확인 합니다.
1. **출력** 을 클릭 하 여 표시 된 **Hello World** 볼 수 있는 출력 페이지를 엽니다.
1. 출력 페이지를 닫습니다.
1. **모든 로그** 를 클릭하여 Runbook 작업에 대한 스트림 창을 엽니다. 출력 스트림에 **Hello World** 만 표시 되어야 합니다. 

    Runbook에서 기록 하는 경우 스트림 창에는 자세한 정보 및 오류 스트림과 같은 runbook 작업에 대 한 다른 스트림이 표시 될 수 있습니다.
1. **MyFirstRunbook-그래픽** 페이지로 돌아가려면 스트림 창 및 작업 창을 닫습니다.
1. Runbook에 대 한 모든 작업을 보려면 **리소스**에서 **작업** 을 선택 합니다. 작업 페이지에는 runbook에서 만든 모든 작업이 나열 됩니다. 작업을 한 번만 실행 했으므로 하나의 작업만 표시 됩니다.
1. 작업 이름을 클릭 하 여 runbook을 시작할 때 표시 된 것과 동일한 작업 창을 엽니다. 이 창을 사용 하 여 runbook에 대해 생성 된 작업의 세부 정보를 볼 수 있습니다.

## <a name="step-5---create-variable-assets"></a>5 단계 - 변수 자산 만들기

Runbook을 테스트 하 고 게시 했지만 지금 까지는 Azure 리소스를 관리 하는 데 유용한 작업을 수행 하지 않습니다. Runbook을 인증 하도록 구성 하기 전에 구독 ID를 보유할 변수를 만들고, 인증할 작업을 설정 하 고, 변수를 참조 해야 합니다. 구독 컨텍스트에 대 한 참조를 포함 하면 여러 구독을 쉽게 사용할 수 있습니다.

1. 탐색 창의 **구독 옵션에서 구독 ID** 를 복사 합니다.
1. Automation 계정 페이지의 **공유 리소스**아래에서 **변수** 를 선택 합니다.
1. **변수 추가**를 선택합니다.
1. 새 변수 페이지에서 제공 된 필드에 다음과 같이 설정 합니다.

    * **이름** - **AzureSubscriptionId**을 입력 합니다.
    * **값** --구독 ID를 입력 합니다. 
    * **유형** --문자열 유지를 선택 합니다.
    * **암호화** -기본값을 사용 합니다.
1. **만들기** 를 클릭하여 변수를 만듭니다.

## <a name="step-6---add-authentication"></a>6 단계-인증 추가

이제 구독 ID를 보유 하는 변수가 있으므로 구독의 실행 자격 증명을 사용 하 여 인증 하도록 runbook을 구성할 수 있습니다. Azure 실행 연결을 자산으로 추가 하 여이 작업을 수행 합니다. 또한 [AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/Connect-AzAccount?view=azps-3.5.0) Cmdlet 및 [AzContext](https://docs.microsoft.com/powershell/module/az.accounts/Set-AzContext?view=azps-3.5.0) cmdlet을 캔버스에 추가 해야 합니다.

>[!NOTE]
>PowerShell runbook의 경우 **AzAccount** 및 **connect-azurermaccount** 는 **AzAccount**에 대 한 별칭입니다. 그래픽 runbook에서는 이러한 별칭을 사용할 수 없습니다. 그래픽 runbook은 **AzAccount** 자체를 사용할 수 있습니다.

1. Runbook으로 이동 하 여 **MyFirstRunbook-그래픽** 페이지에서 **편집** 을 선택 합니다.
1. **출력 항목에 대 한 쓰기 Hello World** 필요 하지 않습니다. 줄임표를 클릭 하 고 **삭제**를 선택 하면 됩니다.
1. 라이브러리 컨트롤에서 **자산**, **연결**을 차례로 확장 합니다. **Canvas에 추가**를 선택 하 여 **AzureRunAsConnection** 을 캔버스에 추가 합니다.
1. 라이브러리 컨트롤의 검색 필드에 **AzAccount** 를 입력 합니다.
1. Canvas에 **AzAccount** 를 추가 합니다.
1. 도형 아래에 원이 나타날 때까지 **실행 연결 가져오기** 를 마우스로 가리킵니다. 원을 클릭 하 고 화살표를 **AzAccount** 로 끌어 링크를 구성 합니다. Runbook은 **Get Run As Connection** 로 시작 하 고 **AzAccount**를 실행 합니다.<br> ![활동 간 링크 만들기](media/automation-first-runbook-graphical/runbook-link-auth-activities.png)
1. 캔버스에서 **연결-AzAccount**을 선택 합니다. 구성 제어 창의 **레이블** 필드에 **Azure에 로그인을** 입력 합니다.
1. **매개 변수**를 클릭 하면 활동 매개 변수 구성 페이지가 나타납니다.
1. **AzAccount** cmdlet은 여러 매개 변수 집합을 포함 하며 매개 변수 값을 제공 하기 전에 하나를 선택 해야 합니다. **매개 변수 집합**을 클릭한 후 **ServicePrincipalCertificate** 매개 변수 집합을 선택합니다.
1. 이 매개 변수 집합에 대 한 매개 변수는 활동 매개 변수 구성 페이지에 표시 됩니다. **APPLICATIONID**를 클릭합니다.<br> Azure 계정 매개 변수를 추가 ![](media/automation-first-runbook-graphical/Add-AzureRmAccount-params.png)
1. 매개 변수 값 페이지에서 다음과 같이 설정 하 고 **확인**을 클릭 합니다.

   * **데이터 원본** -- **활동 출력**을 선택 합니다.
   * 데이터 원본 목록-- **실행 연결 가져오기**를 선택 합니다.
   * **필드 경로** -- **ApplicationId**를 입력 합니다. 활동에서 여러 속성을 가진 개체를 출력 하기 때문에 필드 경로에 대 한 속성의 이름을 지정 합니다.
1. **CERTIFICATETHUMBPRINT**를 클릭 하 고 매개 변수 값 페이지에서 다음과 같이 설정한 후 **확인**을 클릭 합니다.

    * **데이터 원본** -- **활동 출력**을 선택 합니다.
    * 데이터 원본 목록-- **실행 연결 가져오기**를 선택 합니다.
    * **필드 경로** -- **CertificateThumbprint**를 입력 합니다.
1. **Serviceprincipal**을 클릭 하 고 매개 변수 값 페이지에서 **데이터 원본** 필드에 대해 **ConstantValue** 를 선택 합니다. **True**옵션을 클릭 합니다. 그런 다음 **확인**을 클릭 합니다.
1. **TENANTID**를 클릭 하 고 매개 변수 값 페이지에서 다음 설정을 지정 합니다. 완료 되 면 **확인** 을 두 번 클릭 합니다.

    * **데이터 원본** -- **활동 출력**을 선택 합니다. 
    * 데이터 원본 목록-- **실행 연결 가져오기**를 선택 합니다.
    * **필드 경로** -- **TenantId**를 입력 합니다. 
1. 라이브러리 컨트롤의 검색 필드에 **AzContext** 을 입력 합니다.
1. **AzContext** 을 캔버스에 추가 합니다.
1. 캔버스에서 **AzContext** 을 선택 합니다. 구성 제어 창에서 **레이블** 필드에 **구독 Id 지정** 을 입력 합니다.
1. **매개 변수**를 클릭하면 활동 매개 변수 구성 페이지가 표시됩니다.
1. **AzContext** cmdlet에는 매개 변수 집합을 여러 개 포함 하 고 매개 변수 값을 제공 하기 전에 하나를 선택 해야 합니다. **매개 변수 집합**을 클릭한 후 **SubscriptionId** 매개 변수 집합을 선택합니다.
1. 이 매개 변수 집합에 대 한 매개 변수는 활동 매개 변수 구성 페이지에 표시 됩니다. **SubscriptionID**를 클릭 합니다.
1. 매개 변수 값 페이지에서 **데이터 원본** 필드에 대해 **변수 자산** 을 선택 하 고 원본 목록에서 **AzureSubscriptionId** 를 선택 합니다. 완료 되 면 **확인** 을 두 번 클릭 합니다.
1. 아래에 원 모양이 나타날 때까지 **Azure에 로그인** 을 마우스로 가리킵니다. 원을 클릭하고 화살표를 **구독 ID 지정**으로 끌어 놓습니다. 

이 시점에서 runbook이 아래와 같이 표시됩니다. <br>![Runbook 인증 구성](media/automation-first-runbook-graphical/runbook-auth-config.png)

## <a name="step-7---add-activity-to-start-a-virtual-machine"></a>7 단계 - 가상 머신을 시작하는 활동 추가

이제 **new-azvm** 작업을 추가 하 여 가상 컴퓨터를 시작 해야 합니다. Azure 구독에서 VM을 선택할 수 있으며, 지금은 [new-azvm](https://docs.microsoft.com/powershell/module/az.compute/start-azvm?view=azps-3.5.0) cmdlet으로 이름을 하드 코딩 합니다.

1. 라이브러리 컨트롤의 검색 필드에 **시작-Az** 를 입력 합니다.
2. **New-azvm** 을 캔버스에 추가 하 고 **구독 Id 지정**아래에서 클릭 하 여 끕니다.
1. 도형 아래에 원이 나타날 때까지 **구독 ID 지정** 을 마우스로 가리킵니다. 원을 클릭 하 고 화살표를 **시작-new-azvm**로 끕니다.
1. **시작-new-azvm**을 선택 합니다. **매개 변수** 를 클릭 한 다음 **매개 변수 집합** 을 클릭 하 여 활동에 대 한 집합을 봅니다.
1. **ResourceGroupNameParameterSetName** 매개 변수 집합을 선택합니다. **ResourceGroupName** 및 **Name** 필드에는 필수 매개 변수 임을 나타내는 느낌표가 표시 됩니다. 두 필드는 모두 문자열 값을 필요로 합니다.
1. **Name**을 선택합니다. **데이터 원본** 필드에 대 한 **PowerShell 식** 을 선택 합니다. 이 runbook을 시작 하는 데 사용 하는 VM의 경우 큰따옴표로 묶은 컴퓨터 이름을 입력 합니다. **확인**을 클릭합니다.
1. **ResourceGroupName**을 선택합니다. **데이터 원본** 필드에 값 **PowerShell 식을** 사용 하 고 큰따옴표로 묶은 리소스 그룹의 이름을 입력 합니다. **확인**을 클릭합니다.
1. Runbook을 테스트할 수 있도록 **테스트 창**을 클릭합니다.
1. **시작** 을 클릭 하 여 테스트를 시작 합니다. 완료 되 면 VM이 시작 되었는지 확인 합니다. 

이 시점에서 runbook이 아래와 같이 표시됩니다. <br>![Runbook 인증 구성](media/automation-first-runbook-graphical/runbook-startvm.png)

## <a name="step-8---add-additional-input-parameters"></a>8 단계-추가 입력 매개 변수 추가

현재 runbook은 **new-azvm** cmdlet에 지정한 리소스 그룹에서 VM을 시작 합니다. Runbook은 runbook이 시작 될 때 이름 및 리소스 그룹을 둘 다 지정 하는 경우에 더 유용 합니다. Runbook에 입력 매개 변수를 추가 하 여 해당 기능을 제공 해 보겠습니다.

1. **MyFirstRunbook-Graphical** 창에서 **편집**을 클릭하여 그래픽 편집기를 엽니다.
1. **입력 및 출력**과 **입력 추가**를 차례로 선택하여 Runbook 입력 매개 변수 창을 엽니다.
1. 제공 된 필드에서 다음과 같이 설정 하 고 **확인**을 클릭 합니다.
   * **이름** -- **VMName**를 지정 합니다.
   * **형식** --문자열 설정을 유지 합니다.
   * **필수** --값을 **예**로 변경 합니다.
1. *ResourceGroupName* 라는 두 번째 필수 입력 매개 변수를 만든 다음 **확인** 을 클릭 하 여 입력 및 출력 창을 닫습니다.<br> ![Runbook 입력 매개 변수](media/automation-first-runbook-graphical/start-azurermvm-params-outputs.png)
1. **New-azvm** 활동을 선택 하 고 **매개 변수**를 클릭 합니다.
1. **Name** 에 대 한 **데이터 원본** 필드를 **Runbook 입력**으로 변경 합니다. 그런 다음 **VMName**를 선택 합니다.
1. **ResourceGroupName** 에 대 한 **데이터 원본** 필드를 **Runbook 입력** 으로 변경한 다음 **ResourceGroupName**를 선택 합니다.<br> ![New-azvm 매개 변수](media/automation-first-runbook-graphical/start-azurermvm-params-runbookinput.png)
1. Runbook을 저장하고 테스트 창을 엽니다. 이제 테스트에서 사용하는 두 입력 변수에 대한 값을 제공할 수 있습니다.
1. 창을 닫습니다.
1. **게시** 를 클릭하여 Runbook의 새 버전을 게시합니다.
1. 이전에 시작한 VM을 중지 합니다.
1. **시작** 을 클릭하여 runbook을 시작합니다. 시작할 VM에 대 한 **VMName** 및 **ResourceGroupName** 값을 입력 합니다.
1. Runbook이 완료 되 면 VM이 시작 되었는지 확인 합니다.

## <a name="step-9---create-a-conditional-link"></a>9 단계 - 조건부 링크 만들기

이제 VM이 아직 시작 되지 않은 경우에만 VM을 시작 하도록 runbook을 수정할 수 있습니다. VM의 인스턴스 수준 상태를 검색 하는 [new-azvm](https://docs.microsoft.com/powershell/module/Az.Compute/Get-AzVM?view=azps-3.5.0) cmdlet을 추가 하 여이 작업을 수행 합니다. 그런 다음 PowerShell 코드 조각과 함께 **Get Status** 라는 powershell 워크플로 코드 모듈을 추가 하 여 VM 상태가 실행 중인지 또는 중지 되었는지 확인할 수 있습니다. **상태 가져오기** 모듈의 조건부 링크는 현재 실행 중인 상태가 중지 된 경우에만 **new-azvm** 를 실행 합니다. 이 절차를 마칠 때 runbook은 **쓰기 출력** cmdlet을 사용 하 여 VM이 성공적으로 시작 된 경우 사용자에 게 알리는 메시지를 출력 합니다.

1. 그래픽 편집기에서 **MyFirstRunbook-Graphical**을 엽니다.
1. **구독 Id** 와 **new-azvm** 지정 간의 링크를 클릭 한 다음 **Delete**키를 눌러 해당 링크를 제거 합니다.
1. 라이브러리 컨트롤에서 검색 필드에 **Get Az** 를 입력 합니다.
1. **New-azvm** 을 캔버스에 추가 합니다.
1. **New-azvm** 를 선택 하 고 **매개 변수 집합** 을 선택 하 여 cmdlet에 대 한 집합을 봅니다. 
1. **GetVirtualMachineInResourceGroupNameParamSet** 매개 변수 집합을 선택합니다. **ResourceGroupName** 및 **Name** 필드에는 필수 매개 변수를 지정 하는 느낌표가 표시 됩니다. 두 필드는 모두 문자열 값을 필요로 합니다.
1. **이름**의 **데이터 원본** 에서 **Runbook 입력**을 선택한 다음 **VMName**를 선택 합니다. **확인**을 클릭합니다.
1. **ResourceGroupName**에 대 한 **데이터 원본** 에서 **Runbook 입력**을 선택한 다음 **ResourceGroupName**를 선택 합니다. **확인**을 클릭합니다.
1. **상태**에 대 한 **데이터 원본** 에서 **상수 값**을 선택 하 고 **True**를 선택 합니다. **확인**을 클릭합니다.
1. **구독 Id** 를 **new-azvm**로 지정에서 링크를 만듭니다.
1. 라이브러리 컨트롤에서 **Runbook 컨트롤** 을 확장 하 고 캔버스에 **코드** 를 추가 합니다.  
1. **New-azvm** 에서 **코드로**링크를 만듭니다.  
1. **코드** 를 클릭 하 고 구성 창에서 **상태 가져오기**로 레이블을 변경 합니다.
1. **코드** 를 선택 하면 코드 편집기 페이지가 나타납니다.  
1. 다음 코드 조각을 편집기 페이지에 붙여 넣습니다.

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

1. **상태 가져오기** 에서 **시작-new-azvm**으로의 링크를 만듭니다.<br> ![코드 모듈과 Runbook](media/automation-first-runbook-graphical/runbook-startvm-get-status.png)  
1. 링크를 선택 하 고 구성 창에서 **조건 적용** 을 **예**로 변경 합니다. 이 링크는 파선으로 되어 조건이 true로 확인 되는 경우에만 대상 활동이 실행 됨을 나타냅니다.  
1. **조건 식**에 `$ActivityOutput['Get Status'] -eq "Stopped"`을 입력 합니다. 이제 VM이 중지 된 경우에만 **new-azvm** 가 실행 됩니다.
1. 라이브러리 컨트롤에서 **Cmdlet**과 **Microsoft.PowerShell.Utility**를 차례로 확장합니다.
1. 캔버스에 **Write-Output**을 두 번 추가합니다.
1. 첫 번째 **쓰기 출력** 컨트롤의 경우 **매개 변수** 를 클릭 하 고 **VM 시작을 알리기**위해 **레이블** 값을 변경 합니다.
1. **InputObject**의 경우 **데이터 원본** 을 **PowerShell 식**으로 변경 하 고 **시작 $VMName**식에를 입력 합니다.
1. 두 번째 **쓰기 출력** 컨트롤에서 **매개 변수** 를 클릭 하 고 **VM 시작 실패를 알리기**위해 **레이블** 값을 변경 합니다.
1. **InputObject**의 경우 **데이터 원본** 을 **PowerShell 식**으로 변경 하 고 **$VMName 시작할 수 없습니다.** 식을 입력 합니다.
1. **Vm** 을 시작 하 고 **vm 시작 실패를 알리도록** **new-azvm** 에서 링크를 만듭니다.
1. **VM을 시작 했음을 알리는** 링크를 선택 하 고 **조건 적용** 을 true로 변경 합니다.
1. **조건 식**에 `$ActivityOutput['Start-AzVM'].IsSuccessStatusCode -eq $true`을 입력 합니다. 이 **쓰기 출력** 컨트롤은 이제 VM이 성공적으로 시작 된 경우에만 실행 됩니다.
1. **VM 시작 실패를 알리는** 링크를 선택 하 고 **조건 적용** 을 true로 변경 합니다.
1. **조건 식** 필드에 `$ActivityOutput['Start-AzVM'].IsSuccessStatusCode -ne $true`을 입력 합니다. 이 **쓰기 출력** 컨트롤은 이제 VM이 성공적으로 시작 되지 않은 경우에만 실행 됩니다. Runbook이 다음 이미지와 같이 표시됩니다. <br> ![쓰기 출력으로 Runbook](media/automation-first-runbook-graphical/runbook-startazurermvm-complete.png)
1. Runbook을 저장하고 테스트 창을 엽니다.
1. VM이 중지 된 상태로 runbook을 시작 하 고 컴퓨터를 시작 해야 합니다.

## <a name="next-steps"></a>다음 단계

* 그래픽 작성에 대해 자세히 알아보려면 [Azure Automation에서 그래픽 작성](automation-graphical-authoring-intro.md)을 참조하세요.
* PowerShell Runbook을 시작하려면 [내 첫 번째 PowerShell Runbook](automation-first-runbook-textual-powershell.md)을 참조하세요.
* PowerShell 워크플로 runbook을 시작 하려면 [내 첫 번째 powershell 워크플로 runbook](automation-first-runbook-textual.md)을 참조 하세요.