---
title: Azure Automation에서 Runbook 관리
description: 이 문서에서는 Azure Automation에서 Runbook을 관리하는 방법을 설명합니다.
services: automation
ms.subservice: process-automation
ms.date: 02/14/2019
ms.topic: conceptual
ms.openlocfilehash: ad2a34691a00f217db6cf6835eefed18c8862d32
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/02/2020
ms.locfileid: "80547928"
---
# <a name="manage-runbooks-in-azure-automation"></a>Azure Automation에서 Runbook 관리

새 를 [만들거나](#create-a-runbook) 파일 또는 [Runbook 갤러리에서](automation-runbook-gallery.md)기존 Runbook을 [가져와서](#import-a-runbook) Azure 자동화에 Runbook을 추가할 수 있습니다. 이 문서에서는 파일로부터 Runbook을 만들고 가져오는 것과 관련한 정보를 제공합니다. [Azure Automation에 대한 Runbook 및 모듈 갤러리에서](automation-runbook-gallery.md)커뮤니티 런북 및 모듈에 액세스하는 모든 세부 정보를 얻을 수 있습니다.

>[!NOTE]
>이 문서는 새 Azure PowerShell Az 모듈을 사용하도록 업데이트되었습니다. AzureRM 모듈은 적어도 2020년 12월까지 버그 수정을 수신할 예정이므로 계속 사용하셔도 됩니다. 새 Az 모듈 및 AzureRM 호환성에 대한 자세한 내용은 [새 Azure PowerShell Az 모듈 소개](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0)를 참조하세요. 하이브리드 Runbook 작업자의 Az 모듈 설치 지침은 [Azure PowerShell 모듈 설치를](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)참조하십시오. 자동화 계정의 경우 Azure 자동화 에서 [Azure PowerShell 모듈을 업데이트하는 방법을](automation-update-azure-modules.md)사용하여 모듈을 최신 버전으로 업데이트할 수 있습니다.

## <a name="create-a-runbook"></a>Runbook 만들기

Azure Portal 또는 Windows PowerShell 중 하나를 사용하여 Azure Automation에서 새 Runbook을 만들 수 있습니다. Runbook를 만든 후에는 [PowerShell 워크플로 학습](automation-powershell-workflow.md) 및 [Azure Automation에서 그래픽 제작](automation-graphical-authoring-intro.md)의 정보를 사용하여 편집할 수 있습니다.

### <a name="create-a-runbook-in-the-azure-portal"></a>Azure Portal에서 Runbook 만들기

1. Azure Portal에서 Automation 계정을 엽니다.
2. 허브에서 **프로세스 자동화** 에서 **Runbook을** 선택하여 Runbook 목록을 엽니다.
3. **런북 만들기를 클릭합니다.**
4. Runbook의 이름을 입력하고 [해당 유형을](automation-runbook-types.md)선택합니다. Runbook 이름은 문자로 시작해야 하며 문자, 숫자, 밑줄 및 대시를 포함할 수 있습니다.
5. **만들기** 를 클릭하여 Runbook을 만들고 편집기를 엽니다.

### <a name="create-a-runbook-with-powershell"></a>PowerShell을 사용하여 Runbook 만들기

[New-AzAutomationRunbook](https://docs.microsoft.com/powershell/module/az.automation/new-azautomationrunbook?view=azps-3.5.0) cmdlet을 사용하여 빈 [PowerShell 워크플로 런북을](automation-runbook-types.md#powershell-workflow-runbooks)만들 수 있습니다. 매개 `Type` 변수를 사용하여 에 대해 `New-AzAutomationRunbook`정의된 Runbook 유형 중 하나를 지정합니다.

다음 예제에서는 새 빈 Runbook을 만드는 방법을 보여 주입니다.

```azurepowershell-interactive
New-AzAutomationRunbook -AutomationAccountName MyAccount `
-Name NewRunbook -ResourceGroupName MyResourceGroup -Type PowerShell
```

## <a name="import-a-runbook"></a>Runbook 가져오기

PowerShell 스크립트 또는 PowerShell**워크플로(.ps1),** 내보낸 그래픽**런북(.graphrunbook)** 또는 Python 2**스크립트(.py)를**가져와 Azure 자동화에서 새 Runbook을 만들 수 있습니다.  다음 사항을 고려하여 가져오기 동안 만들어지는 [Runbook 유형](automation-runbook-types.md)을 지정해야 합니다.

* 워크플로를 포함하지 않는 **.ps1** 파일을 [PowerShell Runbook](automation-runbook-types.md#powershell-runbooks) 또는 [PowerShell 워크플로 러북으로](automation-runbook-types.md#powershell-workflow-runbooks)가져올 수 있습니다. PowerShell 워크플로 러북으로 가져오면 워크플로로 변환됩니다. 이 경우 변경 내용을 설명하는 주석이 Runbook에 포함됩니다.

* PowerShell 워크플로가 포함된 **.ps1** 파일은 [PowerShell 워크플로 러북으로만](automation-runbook-types.md#powershell-workflow-runbooks)가져올 수 있습니다. 파일에 여러 PowerShell 워크플로가 포함된 경우 가져오기가 실패합니다. 각 워크플로를 자체 파일에 저장하 고 각각 개별적으로 가져와야 합니다.

* PowerShell 스크립트 엔진이 이를 인식할 수 없기 때문에 PowerShell 워크플로가 포함된 **.ps1** 파일을 [PowerShell 실행책으로](automation-runbook-types.md#powershell-runbooks)가져오면 안 됩니다.

* **.graphrunbook** 파일은 새 [그래픽 런북으로만](automation-runbook-types.md#graphical-runbooks)가져올 수 있습니다. **.graphrunbook** 파일에서만 그래픽 런북을 만들 수 있습니다.

### <a name="import-a-runbook-from-a-file-with-the-azure-portal"></a>Azure 포털이 있는 파일에서 Runbook 가져오기

Azure Automation에 스크립트 파일을 가져오려면 다음 절차를 사용할 수 있습니다.

> [!NOTE]
> 포털을 사용하여 **.ps1** 파일을 PowerShell 워크플로 런북으로만 가져올 수 있습니다.

1. Azure Portal에서 Automation 계정을 엽니다.
2. 허브에서 **프로세스 자동화** 에서 **Runbook을** 선택하여 Runbook 목록을 엽니다.
3. **Runbook 가져오기를 클릭합니다.**
4. **Runbook 파일을** 클릭하고 가져올 파일을 선택합니다.
5. **이름** 필드를 사용하도록 설정한 경우 Runbook 이름을 변경할 수 있습니다. 이름은 문자로 시작해야 하며 문자, 숫자, 밑줄 및 대시를 포함할 수 있습니다.
6. [Runbook 형식](automation-runbook-types.md)이 자동으로 선택되지만 해당 제한을 고려한 후에 형식을 변경할 수 있습니다.
7. **만들기**를 클릭합니다. 새 Runbook이 Automation 계정의 Runbook 목록에 표시됩니다.
8. 실행에 앞서 [Runbook을 게시](#publish-a-runbook) 해야 합니다.

> [!NOTE]
> 그래픽 런북 또는 그래픽 PowerShell 워크플로 러북을 가져온 후 다른 유형으로 변환할 수 있습니다. 그러나 이러한 그래픽 Runbook 중 하나를 텍스트 Runbook으로 변환할 수는 없습니다.

### <a name="import-a-runbook-from-a-script-file-with-windows-powershell"></a>Windows PowerShell을 사용 하 고 스크립트 파일에서 runbook 가져오기

[가져오기-AzAutomationRunbook](https://docs.microsoft.com/powershell/module/az.automation/import-azautomationrunbook?view=azps-3.5.0) cmdlet을 사용하여 스크립트 파일을 PowerShell 워크플로 워크플로 초안으로 가져옵니다. runbook이 이미 있는 경우 cmdlet과 `Force` 함께 매개 변수를 사용하지 않으면 가져오기가 실패합니다.

다음 예제에서는 스크립트 파일을 Runbook으로 가져오는 방법을 보여 주며 있습니다.

```azurepowershell-interactive
$automationAccountName =  "AutomationAccount"
$runbookName = "Sample_TestRunbook"
$scriptPath = "C:\Runbooks\Sample_TestRunbook.ps1"
$RGName = "ResourceGroup"

Import-AzAutomationRunbook -Name $runbookName -Path $scriptPath `
-ResourceGroupName $RGName -AutomationAccountName $automationAccountName `
-Type PowerShellWorkflow
```

## <a name="test-a-runbook"></a>Runbook 테스트

Runbook을 테스트할 때 [초안 버전](#publish-a-runbook) 이 실행되며 해당 Runbook에서 수행하는 모든 작업이 완료됩니다. 작업 기록이 만들어지지 않지만 [출력](automation-runbook-output-and-messages.md#output-stream) 및 [경고 및 오류](automation-runbook-output-and-messages.md#message-streams) 스트림이 테스트 출력 창에 표시됩니다. [자세한 내용 스트림에](automation-runbook-output-and-messages.md#message-streams) 대한 메시지는 `VerbosePreference` 변수(자동화-runbook-출력 및 messages.md#preference-변수)가 계속으로 설정된 경우에만 출력 창에 표시됩니다.

초안 버전을 실행하더라도 Runbook은 정상적으로 실행되며, 해당 환경의 리소스에 대해 모든 작업을 수행합니다. 이러한 이유로 비프로덕션 리소스에서만 Runbook을 테스트해야 합니다.

각 [유형의 Runbook을](automation-runbook-types.md) 테스트하는 절차는 동일합니다. Azure 포털의 텍스트 편집기와 그래픽 편집기 간에는 테스트에 차이가 없습니다.

1. [텍스트 편집기](automation-edit-textual-runbook.md) 또는 그래픽 [편집기에서](automation-graphical-authoring-intro.md)Runbook의 초안 버전을 엽니다.
1. **테스트** 단추를 클릭하여 테스트 페이지를 엽니다.
1. Runbook에 매개 변수가 있는 경우 왼쪽 창에 나열되며 테스트에 사용할 값을 제공할 수 있습니다.
1. [하이브리드 Runbook 작업자에서](automation-hybrid-runbook-worker.md)테스트를 실행하려면 실행 **설정을** **하이브리드 작업자로** 변경하고 대상 그룹의 이름을 선택합니다.  그렇지 않은 경우 기본 **Azure** 를 유지하여 클라우드에서 테스트를 실행합니다.
1. **시작** 단추를 클릭하여 테스트를 시작합니다.
1. 출력 창 아래의 버튼을 사용하여 테스트 하는 동안 [PowerShell 워크플로](automation-runbook-types.md#powershell-workflow-runbooks) 또는 [그래픽](automation-runbook-types.md#graphical-runbooks) Runbook을 중지하거나 일시 중단할 수 있습니다. Runbook을 일시 중단하는 경우 일시 중단하기 전에 현재 작업을 완료합니다. Runbook이 일시 중단되면 중지하거나 다시 시작할 수 있습니다.
1. 출력 창에서 Runbook의 출력을 검사합니다.

## <a name="publish-a-runbook"></a>Runbook 게시

새 Runbook을 만들거나 가져올 때는 게시해야 실행할 수 있습니다. Azure 자동화의 각 Runbook에는 초안 버전과 게시된 버전이 있습니다. 게시된 버전만 실행할 수 있으며 초안 버전만 편집할 수 있습니다. 초안 버전을 변경해도 게시된 버전은 영향을 받지 않습니다. 초안 버전을 사용할 수 있게 되면 현재 게시된 버전을 초안 버전으로 덮어쓰면서 게시합니다.

### <a name="publish-a-runbook-in-the-azure-portal"></a>Azure 포털에서 Runbook 게시

1. Azure Portal에서 Runbook을 엽니다.
2. **편집**을 클릭합니다.
3. **게시를** 클릭한 다음 확인 메시지에 대한 응답으로 **예라고** 클릭합니다.

### <a name="publish-a-runbook-using-powershell"></a>PowerShell을 사용하여 Runbook 게시

[게시-AzAutomationRunbook](https://docs.microsoft.com/powershell/module/Az.Automation/Publish-AzAutomationRunbook?view=azps-3.5.0) cmdlet을 사용 하 여 Windows PowerShell으로 Runbook을 게시 합니다. 다음 예제에서는 샘플 Runbook을 게시하는 방법을 보여 주며 있습니다.

```azurepowershell-interactive
$automationAccountName =  "AutomationAccount"
$runbookName = "Sample_TestRunbook"
$RGName = "ResourceGroup"

Publish-AzAutomationRunbook -AutomationAccountName $automationAccountName `
-Name $runbookName -ResourceGroupName $RGName
```

### <a name="schedule-a-runbook-in-the-azure-portal"></a>Azure 포털에서 Runbook 예약

Runbook이 게시되면 작업을 예약할 수 있습니다.

1. Azure Portal에서 Runbook을 엽니다.
2. **리소스** **에서 일정을 선택합니다.**
3. **일정 추가를 선택합니다.**
4. 실행책 예약 창에서 **Runbook에 일정 링크를 선택합니다.**
5. 일정 창에서 **새 일람표 만들기를** 선택합니다.
6. 새 일정 창에 이름, 설명 및 기타 매개 변수를 입력합니다. 
7. 일정이 만들어지면 강조 표시하고 **확인을**클릭합니다. 이제 Runbook에 연결되어야 합니다.
8. 사서함에서 Runbook 상태를 알리는 전자 메일을 찾습니다.

## <a name="next-steps"></a>다음 단계

* Runbook 및 PowerShell 모듈 갤러리에서 이점을 얻는 방법에 대해 알아보려면 [Azure 자동화에 대한 Runbook 및 모듈 갤러리를](automation-runbook-gallery.md)참조하십시오.
* 텍스트 편집기를 사용하여 PowerShell 및 PowerShell 워크플로 런북 편집에 대해 자세히 알아보려면 [Azure Automation의 텍스트 런북 편집을](automation-edit-textual-runbook.md)참조하십시오.
* 그래픽 런북 작성에 대한 자세한 내용은 [Azure Automation의 그래픽 작성을](automation-graphical-authoring-intro.md)참조하십시오.
