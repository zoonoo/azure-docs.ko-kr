---
title: Azure Automation에서 텍스트 Runbook 편집
description: 이 문서에서는 텍스트 편집기를 사용하여 Azure Automation에서 PowerShell 및 PowerShell 워크플로 Runbook을 작업하기 위한 여러 절차를 제공합니다.
services: automation
ms.service: automation
ms.subservice: process-automation
author: mgoedtel
ms.author: magoedte
ms.date: 08/01/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: c00ab8a5e0d307d89ae483db55bdb0b1258ae6a4
ms.sourcegitcommit: e4c33439642cf05682af7f28db1dbdb5cf273cc6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/03/2020
ms.locfileid: "78246417"
---
# <a name="editing-textual-runbooks-in-azure-automation"></a>Azure Automation에서 텍스트 Runbook 편집

Azure Automation에서 텍스트 편집기를 사용하여 [PowerShell Runbook](automation-runbook-types.md#powershell-runbooks) 및 [PowerShell 워크플로 Runbook](automation-runbook-types.md#powershell-workflow-runbooks)을 편집할 수 있습니다. 이 편집기에는 IntelliSense와 같은 다른 코드 편집기의 일반적인 기능이 있습니다. 또한 runbook에 공통적으로 사용 되는 리소스에 액세스 하는 데 도움이 되는 추가 특수 기능을 제공 하는 색 코딩이 있습니다. 

텍스트 편집기에는 cmdlet, 자산 및 자식 Runbook에 대한 코드를 Runbook에 삽입하는 기능이 포함되어 있습니다. 코드를 직접 입력 하는 대신 사용 가능한 리소스 목록에서 선택할 수 있으며, 편집기에서 적절 한 코드를 runbook에 삽입 합니다.

Azure Automation의 각 Runbook에는 초안과 게시 등 두 버전이 있습니다. Runbook의 초안 버전을 편집한 다음 실행할 수 있도록 게시합니다. 게시된 버전은 편집할 수 없습니다. 자세한 내용은 [Runbook 게시](manage-runbooks.md#publish-a-runbook)를 참조하세요.

이 문서에서는 이 편집기를 사용하여 다양한 기능을 수행하기 위한 상세 단계를 제공합니다. 이러한 기능은 [그래픽 runbook](automation-runbook-types.md#graphical-runbooks)에는 적용 되지 않습니다. 이러한 runbook을 사용 하려면 [Azure Automation에서 그래픽 작성](automation-graphical-authoring-intro.md)을 참조 하세요.

>[!NOTE]
>이 문서는 새 Azure PowerShell Az 모듈을 사용하도록 업데이트되었습니다. AzureRM 모듈은 적어도 2020년 12월까지 버그 수정을 수신할 예정이므로 계속 사용하셔도 됩니다. 새 Az 모듈 및 AzureRM 호환성에 대한 자세한 내용은 [새 Azure PowerShell Az 모듈 소개](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0)를 참조하세요. Hybrid Runbook Worker에 대 한 Az module 설치 지침은 [Azure PowerShell 모듈 설치](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)를 참조 하세요. Automation 계정의 경우 [Azure Automation에서 Azure PowerShell 모듈을 업데이트 하는 방법을](automation-update-azure-modules.md)사용 하 여 모듈을 최신 버전으로 업데이트할 수 있습니다.

## <a name="editing-a-runbook-with-the-azure-portal"></a>Azure Portal를 사용 하 여 runbook 편집

다음 절차를 사용하여 텍스트 편집기에서 편집할 Runbook을 엽니다.

1. Azure Portal에서 Automation 계정을 선택 합니다.
2. **프로세스 자동화**에서 **Runbook**을 선택하여 Runbook 목록을 엽니다.
3. 편집할 runbook을 선택 하 고 **편집**을 클릭 합니다.
4. Runbook을 편집합니다.
5. 편집이 완료되면 **저장** 을 클릭합니다.
6. Runbook의 최신 초안 버전을 게시 하려면 **게시** 를 클릭 합니다.

### <a name="insert-a-cmdlet-into-a-runbook"></a>Runbook에 cmdlet을 삽입 합니다.

1. 텍스트 편집기의 캔버스에서 cmdlet을 배치 하려는 위치에 커서를 놓습니다.
2. 라이브러리 컨트롤에서 **Cmdlet** 노드를 확장합니다.
3. 사용할 cmdlet이 포함 된 모듈을 확장 합니다.
4. 삽입할 cmdlet 이름을 마우스 오른쪽 단추로 클릭 하 고 **캔버스에 추가**를 선택 합니다. Cmdlet에 둘 이상의 매개 변수 집합이 있는 경우 편집기에서 기본 집합을 추가 합니다. cmdlet를 확장하여 다른 매개 변수 설정을 선택할 수 있습니다.
5. Cmdlet에 대 한 코드는 전체 매개 변수 목록과 함께 삽입 됩니다.
6. 모든 필수 매개 변수에 대해 꺾쇠 괄호 (< >)로 둘러싸인 값 대신 적절 한 값을 제공 합니다. 필요 하지 않은 매개 변수를 모두 제거 합니다.

### <a name="insert-code-for-a-child-runbook-into-a-runbook"></a>Runbook에 자식 runbook에 대 한 코드 삽입

1. 텍스트 편집기의 캔버스에서 [자식 runbook](automation-child-runbooks.md)에 대 한 코드를 배치 하려는 위치에 커서를 놓습니다.
2. 라이브러리 컨트롤에서 **Runbook** 노드를 확장합니다.
3. 삽입할 Runbook을 마우스 오른쪽 단추로 클릭하고 **캔버스에 추가**를 선택합니다.
4. 자식 Runbook의 코드가 Runbook 매개 변수의 자리 표시자와 함께 삽입됩니다.
5. 자리 표시자를 각 매개 변수에 적합한 값으로 바꿉니다.

### <a name="insert-an-asset-into-a-runbook"></a>Runbook에 자산 삽입

1. 텍스트 편집기의 캔버스에서 코드를 배치하려는 위치에 커서를 놓습니다.
2. 라이브러리 컨트롤에서 **자산** 노드를 확장합니다.
3. 원하는 자산 형식에 대 한 노드를 확장 합니다.
4. 삽입할 자산 이름을 마우스 오른쪽 단추로 클릭 하 고 **캔버스에 추가**를 선택 합니다. [변수 자산](automation-variables.md)에 대해 변수를 가져오거나 설정할지 여부에 따라 **캔버스에 "변수 가져오기" 추가** 또는 **캔버스에 "변수 설정"** 추가 중 하나를 선택 합니다.
5. 자산에 대 한 코드는 runbook에 삽입 됩니다.

## <a name="editing-an-azure-automation-runbook-using-windows-powershell"></a>Windows PowerShell을 사용 하 여 Azure Automation runbook 편집

Windows PowerShell을 사용 하 여 runbook을 편집 하려면 원하는 편집기를 사용 하 여 runbook을 "ps1" 파일에 저장 합니다. [AzAutomationRunbook](/powershell/module/Az.Automation/Export-AzAutomationRunbook) cmdlet을 사용 하 여 runbook의 콘텐츠를 검색할 수 있습니다. [AzAutomationRunbook](/powershell/module/Az.Automation/import-azautomationrunbook) cmdlet을 사용 하 여 기존 초안 runbook을 수정 된 runbook으로 바꿀 수 있습니다.

### <a name="retrieve-the-contents-of-a-runbook-using-windows-powershell"></a>Windows PowerShell을 사용 하 여 runbook 내용 검색

다음 예제 명령에서는 Runbook에 대한 스크립트를 검색하고 스크립트 파일에 저장하는 방법을 보여줍니다. 이 예에서는 초안 버전이 검색됩니다. 이 버전은 변경할 수 없지만 게시 된 버전의 runbook을 검색할 수 있습니다.

```powershell-interactive
$resourceGroupName = "MyResourceGroup"
$automationAccountName = "MyAutomatonAccount"
$runbookName = "Hello-World"
$scriptFolder = "c:\runbooks"

Export-AzAutomationRunbook -Name $runbookName -AutomationAccountName $automationAccountName -ResourceGroupName $resourceGroupName -OutputFolder $scriptFolder -Slot Draft
```

### <a name="change-the-contents-of-a-runbook-using-windows-powershell"></a>Windows PowerShell을 사용 하 여 runbook 내용 변경

다음 샘플 명령에서는 스크립트 파일의 콘텐츠로 기존 Runbook 콘텐츠를 대체하는 방법을 보여줍니다. 이 내용은 [Windows PowerShell을 사용하여 스크립트 파일에서 Runbook을 가져오려면](manage-runbooks.md#import-a-runbook)에서 설명한 것과 동일한 샘플 절차입니다.

```powershell-interactive
$resourceGroupName = "MyResourceGroup"
$automationAccountName = "MyAutomatonAccount"
$runbookName = "Hello-World"
$scriptFolder = "c:\runbooks"

Import-AzAutomationRunbook -Path "$scriptfolder\Hello-World.ps1" -Name $runbookName -Type PowerShell -AutomationAccountName $automationAccountName -ResourceGroupName $resourceGroupName -Force
Publish-AzAutomationRunbook -Name $runbookName -AutomationAccountName $automationAccountName -ResourceGroupName $resourceGroupName
```

## <a name="related-articles"></a>관련 문서

* [Azure Automation에서 Runbook 관리](manage-runbooks.md)
* [PowerShell 워크플로 학습](automation-powershell-workflow.md)
* [Azure Automation에서 그래픽 작성](automation-graphical-authoring-intro.md)
* [인증서](automation-certificates.md)
* [연결](automation-connections.md)
* [자격 증명](automation-credentials.md)
* [일정](automation-schedules.md)
* [변수](automation-variables.md)

