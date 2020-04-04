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
ms.openlocfilehash: 3bbfe26f336a25ee85f2223226d6eb513ae21736
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/03/2020
ms.locfileid: "80632152"
---
# <a name="editing-textual-runbooks-in-azure-automation"></a>Azure Automation에서 텍스트 Runbook 편집

Azure Automation에서 텍스트 편집기를 사용하여 [PowerShell Runbook](automation-runbook-types.md#powershell-runbooks) 및 [PowerShell 워크플로 Runbook](automation-runbook-types.md#powershell-workflow-runbooks)을 편집할 수 있습니다. 이 편집기는 IntelliSense와 같은 다른 코드 편집기의 일반적인 기능을 가지고 있습니다. 또한 Runbook에 공통적인 리소스에 액세스하는 데 도움이되는 추가 특수 기능이있는 색상 코딩이 있습니다. 

텍스트 편집기에는 cmdlet, 자산 및 자식 Runbook에 대한 코드를 Runbook에 삽입하는 기능이 포함되어 있습니다. 코드를 직접 입력하는 대신 사용 가능한 리소스 목록에서 선택할 수 있으며 편집기는 Runbook에 적절한 코드를 삽입합니다.

Azure Automation의 각 Runbook에는 초안과 게시 등 두 버전이 있습니다. Runbook의 초안 버전을 편집한 다음 실행할 수 있도록 게시합니다. 게시된 버전은 편집할 수 없습니다. 자세한 내용은 [Runbook 게시를](manage-runbooks.md#publishing-a-runbook)참조하십시오.

이 문서에서는 이 편집기를 사용하여 다양한 기능을 수행하기 위한 상세 단계를 제공합니다. [그래픽 런북에는](automation-runbook-types.md#graphical-runbooks)적용되지 않습니다. 이러한 Runbook을 사용하려면 [Azure 자동화에서 그래픽 작성을](automation-graphical-authoring-intro.md)참조하십시오.

>[!NOTE]
>이 문서는 새 Azure PowerShell Az 모듈을 사용하도록 업데이트되었습니다. AzureRM 모듈은 적어도 2020년 12월까지 버그 수정을 수신할 예정이므로 계속 사용하셔도 됩니다. 새 Az 모듈 및 AzureRM 호환성에 대한 자세한 내용은 [새 Azure PowerShell Az 모듈 소개](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0)를 참조하세요. 하이브리드 Runbook 작업자의 Az 모듈 설치 지침은 [Azure PowerShell 모듈 설치를](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)참조하십시오. 자동화 계정의 경우 Azure 자동화 에서 [Azure PowerShell 모듈을 업데이트하는 방법을](automation-update-azure-modules.md)사용하여 모듈을 최신 버전으로 업데이트할 수 있습니다.

## <a name="editing-a-runbook-with-the-azure-portal"></a>Azure 포털을 통해 Runbook 편집

다음 절차를 사용하여 텍스트 편집기에서 편집할 Runbook을 엽니다.

1. Azure 포털에서 자동화 계정을 선택합니다.
2. **프로세스 자동화에서** **Runbook을** 선택하여 Runbook 목록을 엽니다.
3. 편집할 Runbook을 선택한 다음 **편집을**클릭합니다.
4. Runbook을 편집합니다.
5. 편집이 끝나면 **저장**을 클릭합니다.
6. Runbook의 최신 초안 버전을 게시하려면 **게시를** 클릭합니다.

### <a name="insert-a-cmdlet-into-a-runbook"></a>런북에 cmdlet 삽입

1. 텍스트 편집기의 캔버스에서 cmdlet을 배치할 커서를 배치합니다.
2. 라이브러리 컨트롤에서 **Cmdlet** 노드를 확장합니다.
3. 사용할 cmdlet이 포함된 모듈을 확장합니다.
4. cmdlet 이름을 마우스 오른쪽 단추로 클릭하여 캔버스에 **추가를**삽입하고 선택합니다. cmdlet에 둘 이상의 매개 변수 집합이 있는 경우 편집기는 기본 집합을 추가합니다. cmdlet를 확장하여 다른 매개 변수 설정을 선택할 수 있습니다.
5. cmdlet에 대한 코드는 전체 매개 변수 목록과 함께 삽입됩니다.
6. 필요한 매개변수에 대해 각도 대괄호(<>)로 둘러싸인 값 대신 적절한 값을 제공합니다. 필요하지 않은 매개 변수를 제거합니다.

### <a name="insert-code-for-a-child-runbook-into-a-runbook"></a>Runbook에 자식 Runbook에 대한 코드를 삽입합니다.

1. 텍스트 편집기의 캔버스에서 [자식 Runbook에](automation-child-runbooks.md)대한 코드를 배치할 커서를 배치합니다.
2. 라이브러리 컨트롤에서 **Runbook** 노드를 확장합니다.
3. 삽입할 Runbook을 마우스 오른쪽 단추로 클릭하고 **캔버스에 추가**를 선택합니다.
4. 자식 Runbook의 코드가 Runbook 매개 변수의 자리 표시자와 함께 삽입됩니다.
5. 자리 표시자를 각 매개 변수에 적합한 값으로 바꿉니다.

### <a name="insert-an-asset-into-a-runbook"></a>런북에 에셋 삽입

1. 텍스트 편집기의 캔버스 컨트롤에서 자식 Runbook에 대한 코드를 배치할 커서를 배치합니다.
2. 라이브러리 컨트롤에서 **자산** 노드를 확장합니다.
3. 원하는 자산 유형에 대한 노드를 확장합니다.
4. 자산 이름을 마우스 오른쪽 단추로 클릭하여 캔버스에 **추가를**삽입하고 선택합니다. [변수 자산의](automation-variables.md)경우 변수를 얻거나 설정할지 여부에 따라 **캔버스에 "변수 Get"을 추가하거나** **캔버스에 "변수 설정"을 추가합니다.**
5. 자산의 코드가 Runbook에 삽입됩니다.

## <a name="editing-an-azure-automation-runbook-using-windows-powershell"></a>Windows PowerShell을 사용하여 Azure 자동화 런북 편집

Windows PowerShell을 사용하여 Runbook을 편집하려면 선택한 편집기를 사용하고 **Runbook을 .ps1** 파일에 저장합니다. [내보내기-AzAutomationRunbook](/powershell/module/Az.Automation/Export-AzAutomationRunbook) cmdlet을 사용하여 Runbook의 내용을 검색할 수 있습니다. [가져오기-AzAutomationRunbook](/powershell/module/Az.Automation/import-azautomationrunbook) cmdlet을 사용하여 기존 초안 Runbook을 수정된 런북으로 바꿀 수 있습니다.

### <a name="retrieve-the-contents-of-a-runbook-using-windows-powershell"></a>Windows PowerShell을 사용하여 Runbook의 내용을 검색합니다.

다음 예제 명령에서는 Runbook에 대한 스크립트를 검색하고 스크립트 파일에 저장하는 방법을 보여줍니다. 이 예에서는 초안 버전이 검색됩니다. 이 버전은 변경할 수 없지만 Runbook의 게시된 버전을 검색할 수도 있습니다.

```powershell-interactive
$resourceGroupName = "MyResourceGroup"
$automationAccountName = "MyAutomatonAccount"
$runbookName = "Hello-World"
$scriptFolder = "c:\runbooks"

Export-AzAutomationRunbook -Name $runbookName -AutomationAccountName $automationAccountName -ResourceGroupName $resourceGroupName -OutputFolder $scriptFolder -Slot Draft
```

### <a name="change-the-contents-of-a-runbook-using-windows-powershell"></a>Windows PowerShell을 사용하여 Runbook의 내용 변경

다음 샘플 명령에서는 스크립트 파일의 콘텐츠로 기존 Runbook 콘텐츠를 대체하는 방법을 보여줍니다. 이 내용은 [Windows PowerShell을 사용하여 스크립트 파일에서 Runbook을 가져오려면](manage-runbooks.md#importing-a-runbook)에서 설명한 것과 동일한 샘플 절차입니다.

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
* [PowerShell 워크플로우 학습](automation-powershell-workflow.md)
* [Azure Automation에서 그래픽 작성](automation-graphical-authoring-intro.md)
* [인증서](automation-certificates.md)
* [연결](automation-connections.md)
* [자격 증명](automation-credentials.md)
* [일정](automation-schedules.md)
* [변수](automation-variables.md)

