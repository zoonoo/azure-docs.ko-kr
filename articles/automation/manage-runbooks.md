---
title: Azure Automation에서 Runbook 관리
description: 이 문서에서는 Azure Automation에서 Runbook을 관리하는 방법을 설명합니다.
services: automation
ms.subservice: process-automation
ms.date: 02/14/2019
ms.topic: conceptual
ms.openlocfilehash: 4531e5d483508ca99722182f97c6a7aa0e0b68f3
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/29/2020
ms.locfileid: "78191115"
---
# <a name="manage-runbooks-in-azure-automation"></a>Azure Automation에서 Runbook 관리

새 runbook을 [만들거나](#create-a-runbook) 파일 또는 [runbook 갤러리](automation-runbook-gallery.md)에서 [기존 항목을 가져와](#import-a-runbook) Azure Automation에 runbook을 추가할 수 있습니다. 이 문서에서는 파일로부터 Runbook을 만들고 가져오는 것과 관련한 정보를 제공합니다. [Azure Automation에 대 한 Runbook 및 모듈 갤러리](automation-runbook-gallery.md)에서 커뮤니티 runbook 및 모듈에 액세스 하는 모든 세부 정보를 가져올 수 있습니다.

>[!NOTE]
>이 문서는 새 Azure PowerShell Az 모듈을 사용하도록 업데이트되었습니다. AzureRM 모듈은 적어도 2020년 12월까지 버그 수정을 수신할 예정이므로 계속 사용하셔도 됩니다. 새 Az 모듈 및 AzureRM 호환성에 대한 자세한 내용은 [새 Azure PowerShell Az 모듈 소개](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0)를 참조하세요. Hybrid Runbook Worker에 대 한 Az module 설치 지침은 [Azure PowerShell 모듈 설치](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)를 참조 하세요. Automation 계정의 경우 [Azure Automation에서 Azure PowerShell 모듈을 업데이트 하는 방법을](automation-update-azure-modules.md)사용 하 여 모듈을 최신 버전으로 업데이트할 수 있습니다.

## <a name="create-a-runbook"></a>Runbook 만들기

Azure Portal 또는 Windows PowerShell 중 하나를 사용하여 Azure Automation에서 새 Runbook을 만들 수 있습니다. Runbook를 만든 후에는 [PowerShell 워크플로 학습](automation-powershell-workflow.md) 및 [Azure Automation에서 그래픽 제작](automation-graphical-authoring-intro.md)의 정보를 사용하여 편집할 수 있습니다.

### <a name="create-a-runbook-in-the-azure-portal"></a>Azure Portal에서 Runbook 만들기

1. Azure Portal에서 Automation 계정을 엽니다.
2. 허브의 **프로세스 자동화** 아래에서 **runbook** 을 선택 하 여 runbook 목록을 엽니다.
3. **Runbook 만들기를**클릭 합니다.
4. Runbook의 이름을 입력 하 고 해당 [형식을](automation-runbook-types.md)선택 합니다. Runbook 이름은 문자로 시작 해야 하며 문자, 숫자, 밑줄 및 대시를 포함할 수 있습니다.
5. **만들기** 를 클릭하여 Runbook을 만들고 편집기를 엽니다.

### <a name="create-a-runbook-with-powershell"></a>PowerShell을 사용하여 Runbook 만들기

[AzAutomationRunbook](https://docs.microsoft.com/powershell/module/az.automation/new-azautomationrunbook?view=azps-3.5.0) cmdlet을 사용 하 여 빈 [PowerShell 워크플로 runbook](automation-runbook-types.md#powershell-workflow-runbooks)을 만들 수 있습니다. *Type* 매개 변수를 사용 하 여 **AzAutomationRunbook**에 대해 정의 된 runbook 유형 중 하나를 지정 합니다.

다음 예에서는 비어 있는 새 runbook을 만드는 방법을 보여 줍니다.

```azurepowershell-interactive
New-AzAutomationRunbook -AutomationAccountName MyAccount `
-Name NewRunbook -ResourceGroupName MyResourceGroup -Type PowerShell
```

## <a name="import-a-runbook"></a>Runbook 가져오기

PowerShell 스크립트 또는 PowerShell 워크플로 (**ps1**), 내보낸 그래픽 runbook (**Graphrunbook**) 또는 Python 2 스크립트 ( **. py**)를 가져와서 Azure Automation에서 새 runbook을 만들 수 있습니다.  다음 사항을 고려하여 가져오기 동안 만들어지는 [Runbook 유형](automation-runbook-types.md)을 지정해야 합니다.

* 워크플로를 포함 하지 않는 **ps1** 파일은 [Powershell Runbook](automation-runbook-types.md#powershell-runbooks) 또는 [powershell 워크플로 runbook](automation-runbook-types.md#powershell-workflow-runbooks)으로 가져올 수 있습니다. PowerShell 워크플로 runbook으로 가져오면 워크플로로 변환 됩니다. 이 경우 변경 내용을 설명 하는 주석이 runbook에 포함 됩니다.

* PowerShell 워크플로를 포함 하는 **ps1** 파일은 [powershell 워크플로 runbook](automation-runbook-types.md#powershell-workflow-runbooks)으로만 가져올 수 있습니다. 파일에 여러 PowerShell 워크플로가 포함 되어 있으면 가져오기가 실패 합니다. 각 워크플로를 자체 파일에 저장하 고 각각 개별적으로 가져와야 합니다.

* Powershell 워크플로를 포함 하는 **ps1** 파일은 powershell 스크립트 엔진에서 인식할 수 없으므로 powershell [runbook](automation-runbook-types.md#powershell-runbooks)으로 가져올 수 없습니다.

* **Graphrunbook** 파일은 새 [그래픽 runbook](automation-runbook-types.md#graphical-runbooks)으로만 가져올 수 있습니다. **Graphrunbook** 파일에서 그래픽 runbook을 만들 수 있습니다.

### <a name="import-a-runbook-from-a-file-with-the-azure-portal"></a>Azure Portal를 사용 하 여 파일에서 runbook 가져오기

Azure Automation에 스크립트 파일을 가져오려면 다음 절차를 사용할 수 있습니다.

> [!NOTE]
> 포털을 사용 하 여 PowerShell 워크플로 runbook으로 ps1 파일만 가져올 수 있습니다 **.**

1. Azure Portal에서 Automation 계정을 엽니다.
2. 허브의 **프로세스 자동화** 아래에서 **runbook** 을 선택 하 여 runbook 목록을 엽니다.
3. **Runbook 가져오기를**클릭 합니다.
4. **Runbook 파일** 을 클릭 하 고 가져올 파일을 선택 합니다.
5. **이름** 필드를 사용 하는 경우 runbook 이름을 변경할 수 있습니다. 이름은 문자로 시작 해야 하며 문자, 숫자, 밑줄 및 대시를 포함할 수 있습니다.
6. [Runbook 형식](automation-runbook-types.md)이 자동으로 선택되지만 해당 제한을 고려한 후에 형식을 변경할 수 있습니다.
7. **만들기**를 클릭합니다. 새 Runbook이 Automation 계정의 Runbook 목록에 표시됩니다.
8. 실행에 앞서 [Runbook을 게시](#publish-a-runbook) 해야 합니다.

> [!NOTE]
> 그래픽 runbook 또는 그래픽 PowerShell 워크플로 runbook을 가져온 후에는 다른 형식으로 변환할 수 있습니다. 그러나 이러한 그래픽 runbook 중 하나를 텍스트 runbook으로 변환할 수는 없습니다.

### <a name="import-a-runbook-from-a-script-file-with-windows-powershell"></a>Windows PowerShell을 사용 하 여 스크립트 파일에서 runbook 가져오기

[AzAutomationRunbook](https://docs.microsoft.com/powershell/module/az.automation/import-azautomationrunbook?view=azps-3.5.0) cmdlet을 사용 하 여 스크립트 파일을 초안 PowerShell 워크플로 runbook으로 가져올 수 있습니다. Runbook이 이미 있는 경우 *Force* 매개 변수를 cmdlet과 함께 사용 하지 않으면 가져오기가 실패 합니다.

다음 예제에서는 스크립트 파일을 runbook으로 가져오는 방법을 보여 줍니다.

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

Runbook을 테스트할 때 [초안 버전](#publish-a-runbook) 이 실행되며 해당 Runbook에서 수행하는 모든 작업이 완료됩니다. 작업 기록은 생성 되지 않지만 [출력](automation-runbook-output-and-messages.md#output-stream) 및 [경고 및 오류](automation-runbook-output-and-messages.md#message-streams) 스트림은 테스트 출력 창에 표시 됩니다. [자세한 정보 표시 스트림으로](automation-runbook-output-and-messages.md#message-streams) 의 메시지는 [$VerbosePreference 변수가](automation-runbook-output-and-messages.md#preference-variables) **Continue**로 설정 된 경우에만 출력 창에 표시 됩니다.

초안 버전을 실행하더라도 Runbook은 정상적으로 실행되며, 해당 환경의 리소스에 대해 모든 작업을 수행합니다. 이러한 이유로 비프로덕션 리소스에서만 Runbook을 테스트해야 합니다.

각 [runbook 유형을](automation-runbook-types.md) 테스트 하는 절차는 동일 합니다. Azure Portal에서 텍스트 편집기와 그래픽 편집기 간의 테스트에는 차이가 없습니다.

1. [텍스트 편집기나](automation-edit-textual-runbook.md) [그래픽 편집기](automation-graphical-authoring-intro.md)에서 runbook의 초안 버전을 엽니다.
1. **테스트** 단추를 클릭하여 테스트 페이지를 엽니다.
1. Runbook에 매개 변수가 있는 경우 해당 매개 변수는 테스트에 사용할 값을 제공할 수 있는 왼쪽 창에 나열 됩니다.
1. [Hybrid Runbook Worker](automation-hybrid-runbook-worker.md)에서 테스트를 실행 하려면 **실행 설정** 을 **Hybrid Worker** 로 변경 하 고 대상 그룹의 이름을 선택 합니다.  그렇지 않은 경우 기본 **Azure** 를 유지하여 클라우드에서 테스트를 실행합니다.
1. **시작** 단추를 클릭 하 여 테스트를 시작 합니다.
1. 출력 창 아래의 단추를 사용 하 여 [PowerShell 워크플로](automation-runbook-types.md#powershell-workflow-runbooks) 또는 [그래픽](automation-runbook-types.md#graphical-runbooks) runbook을 테스트 하는 동안 중지 하거나 일시 중단할 수 있습니다. Runbook을 일시 중단하는 경우 일시 중단되기 전에 현재 작업이 완료됩니다. Runbook이 일시 중단되면 중지하거나 다시 시작할 수 있습니다.
1. 출력 창에서 runbook의 출력을 검사 합니다.

## <a name="publish-a-runbook"></a>Runbook 게시

새 Runbook을 만들거나 가져올 때는 게시해야 실행할 수 있습니다. Azure Automation의 각 runbook에는 초안 버전과 게시 된 버전이 있습니다. 게시된 버전만 실행할 수 있으며 초안 버전만 편집할 수 있습니다. 게시됨 버전은 초안 버전 변경 내용의 영향을 받지 않습니다. 초안 버전을 사용할 수 있도록 설정 해야 하는 경우 게시 하 여 현재 게시 된 버전을 초안 버전으로 덮어씁니다.

### <a name="publish-a-runbook-in-the-azure-portal"></a>Azure Portal에 runbook 게시

1. Azure Portal에서 Runbook을 엽니다.
2. **편집**을 클릭합니다.
3. **게시** 를 클릭 한 다음 확인 메시지에 대 한 응답으로 **예** 를 클릭 합니다.

### <a name="publish-a-runbook-using-powershell"></a>PowerShell을 사용 하 여 runbook 게시

Windows PowerShell을 사용 하 여 runbook을 게시 하려면 [AzAutomationRunbook](https://docs.microsoft.com/powershell/module/Az.Automation/Publish-AzAutomationRunbook?view=azps-3.5.0) cmdlet을 사용 합니다. 다음 예제에서는 샘플 runbook을 게시 하는 방법을 보여 줍니다.

```azurepowershell-interactive
$automationAccountName =  "AutomationAccount"
$runbookName = "Sample_TestRunbook"
$RGName = "ResourceGroup"

Publish-AzAutomationRunbook -AutomationAccountName $automationAccountName `
-Name $runbookName -ResourceGroupName $RGName
```

## <a name="next-steps"></a>다음 단계

* Runbook 및 PowerShell 모듈 갤러리를 활용 하는 방법에 대 한 자세한 내용은 [Azure Automation에 대 한 runbook 및 모듈 갤러리](automation-runbook-gallery.md)를 참조 하세요.
* 텍스트 편집기를 사용 하 여 PowerShell 및 PowerShell 워크플로 runbook을 편집 하는 방법에 대 한 자세한 내용은 [Azure Automation에서 텍스트 Runbook 편집](automation-edit-textual-runbook.md)을 참조 하세요.
* 그래픽 runbook 작성에 대해 자세히 알아보려면 [Azure Automation의 그래픽 작성](automation-graphical-authoring-intro.md)을 참조 하세요.
