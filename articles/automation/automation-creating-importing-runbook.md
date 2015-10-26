<properties 
	pageTitle="Azure 자동화에서 Runbook 만들기 또는 가져오기"
	description="이 문서에서는 Azure 자동화에서 새 Runbook을 만들거나 파일에서 가져오는 방법을 설명합니다."
	services="automation"
	documentationCenter=""
	authors="bwren"
	manager="stevenka"
	editor="tysonn" />
<tags 
	ms.service="automation"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="infrastructure-services"
	ms.date="09/22/2015"
	ms.author="bwren" />

# Azure 자동화에서 Runbook 만들기 또는 가져오기

[새로 만들거나](#creating-a-new-runbook) [파일 또는Runbook 갤러리에서 기존 Runbook](automation-runbook-gallery.md)을 가져와서 Azure 자동화에 Runbook을 추가할 수 있습니다. 이 문서에서는 파일로부터 Runbook을 만들고 가져오는 것과 관련한 정보를 제공합니다. [Azure 자동화에 대한 Runbook 및 모듈 갤러리](automation-runbook-gallery.md)에서 커뮤니티 Runbook과 모듈 액세스에 대한 모든 상세 정보를 확인할 수 있습니다.

## 새 Runbook 만들기

Azure 포털 또는 Windows PowerShell 중 하나를 사용하여 Azure 자동화에서 새 Runbook을 만들 수 있습니다. Runbook를 만든 후에는 [ PowerShell 워크플로 학습](automation-powershell-workflow.md) 및 [Azure 자동화에서 그래픽 제작](automation-graphical-authoring-intro.md)의 정보를 사용하여 편집할 수 있습니다.

### Azure 포털에서 새 Azure 자동화 Runbook을 만들려면

Azure 포털에서는 [PowerShell 워크플로 Runbook](automation-runbook-types.md#powershell-workflow-runbooks)을 사용한 작업만 가능합니다.

1. Azure 포털에서 **새로 만들기**, **앱 서비스**, **자동화**, **Runbook**, **빠른 생성**을 차례로 클릭합니다.
2. 필요한 정보를 입력한 다음 **만들기**를 클릭합니다. Runbook 이름은 문자로 시작해야 하며 문자, 숫자, 언더바, 대시 등이 포함될 수 있습니다.
3. 지금 runbook을 편집하려면 **Runbook 편집**을 클릭합니다. 그렇지 않은 경우 **확인**을 클릭합니다.
4. 새 Runbook이 **Runbook** 탭에 표시됩니다.


### Azure Preview 포털에서 새 Azure 자동화 Runbook을 만들려면

1. Azure Preview 포털에서 자동화 계정을 엽니다. 
2. **Runbook** 타일을 클릭하여 Runbook 목록을 엽니다.
3. **Runbook 추가** 단추를 클릭하고 **새 Runbook 만들기**를 클릭합니다.
2. Runbook의 **이름**을 입력하고 [유형](automation-runbook-types.md)을 선택합니다. Runbook 이름은 문자로 시작해야 하며 문자, 숫자, 언더바, 대시 등이 포함될 수 있습니다.
3. **만들기**를 클릭하여 Runbook을 만들고 편집기를 엽니다.


### Windows PowerShell에서 새 Azure 자동화 Runbook을 만들려면

[New-AzureAutomationRunbook](https://msdn.microsoft.com/library/dn690272.aspx) cmdlet을 사용하여 빈 [PowerShell 워크플로 Runbook](automation-runbook-types.md#powershell-workflow-runbooks)을 만들 수 있습니다. **이름** 매개 변수를 지정하여 나중에 편집할 빈 Runbook을 만들거나, **경로** 매개 변수를 지정하여 스크립트 파일을 가져올 수 있습니다.

다음 명령 예제에서는 새로운 빈 Runbook을 만드는 방법을 보여줍니다.

    $automationAccountName = "MyAutomationAccount"
    $runbookName = "Sample-TestRunbook"
    New-AzureAutomationRunbook –AutomationAccountName $automationAccountName –Name $runbookName 

## 파일의 Runbook을 Azure 자동화로 가져오기

PowerShell 스크립트나 PowerShell 워크플로(.ps1 확장명) 또는 내보낸된 그래픽 Runbook(.graphrunbook)을 Azure 자동화에 가져와서 새 Runbook을 만들 수 있습니다. 다음 사항을 고려하여 가져오기에서 만들 [Runbook 유형](automation-runbook-types.md)을 지정해야 합니다.

- .graphrunbook 파일은 새 [그래픽 Runbook](automation-runbook-types.md#graphical-runbooks)에만 가져올 수 있으며 그래픽 Runbook은 .graphrunbook 파일을 통해서만 만들 수 있습니다.
- PowerShell 워크플로를 포함하는.ps1 파일은 [PowerShell 워크플로 Runbook](automation-runbook-types.md#powershell-workflow-runbooks)에만 가져올 수 있습니다. 파일에 여러 PowerShell 워크플로 있으면 가져오기가 실패합니다. 각 워크플로를 자체 파일에 저장하 고 각각 개별적으로 가져와야 합니다.
- 워크플로를 포함하지 않은 .ps1 파일은 [PowerShell Runbook](automation-runbook-types.md#powershell-runbooks) 또는 [PowerShell Workflow Runbook](automation-runbook-types.md#powershell-workflow-runbooks)으로 가져올 수 있습니다. PowerShell 워크플로 Runbook에 가져오면 워크플로로 변환되며 Runbook에 적용된 변경 내용을 명시하는 메모가 포함됩니다.

### Azure 포털을 사용하여 파일에서 Runbook을 가져오려면
Azure 자동화에 스크립트 파일을 가져오려면 다음 절차를 사용할 수 있습니다. 이 포털을 사용하는 PowerShell 워크플로 Runbook에만 .ps1 파일을 가져올 수 있습니다. 다른 유형에는 Azure Preview 포털을 사용해야 합니다.

1. Azure 관리 포털에서 **자동화**를 선택한 다음 자동화 계정을 클릭합니다.
2. **가져오기**를 클릭합니다.
3. **파일 찾아보기**를 클릭하고 가져올 스크립트 파일을 찾습니다.
4. 지금 Runbook을 편집하려면 **Runbook 편집**을 클릭합니다. 그렇지 않은 경우 확인을 클릭합니다.
5. 이 새 Runbook이 자동화 계정의**Runbook** 탭에 표시됩니다.
6. 실행에 앞서 [Runbook을 게시](#publishing-a-runbook)해야 합니다.


### Azure Preview 포털로 파일에서 Runbook을 가져오려면
Azure 자동화에 스크립트 파일을 가져오려면 다음 절차를 사용할 수 있습니다. 이 포털을 사용하는 PowerShell 워크플로 Runbook에만 .ps1 파일을 가져올 수 있습니다.

1. Azure Preview 포털에서 자동화 계정을 엽니다. 
2. **Runbook** 타일을 클릭하여 Runbook 목록을 엽니다.
3. **Runbook 추가** 단추를 클릭하고 **가져오기**를 클릭합니다.
4. **Runbook 파일**을 클릭하여 가져올 파일을 선택합니다.
2. **이름** 필드가 활성화된 경우 변경이 가능합니다. Runbook 이름은 문자로 시작해야 하며 문자, 숫자, 언더바, 대시 등이 포함될 수 있습니다.
3. 위에 나열된 제한 사항을 고려하여 [Runbook 유형](automation-runbook-types.md)을 선택합니다.
3. 새 Runbook이 자동화 계정의Runbook 목록에 표시됩니다.
4. 실행에 앞서 [Runbook을 게시](#publishing-a-runbook)해야 합니다.

### Windows PowerShell을 사용하여 스크립트 파일에서 Runbook을 가져오려면

[Set-AzureAutomationRunbookDefinition](https://msdn.microsoft.com/library/dn690267.aspx) cmdlet을 사용하여 스크립트 파일을 기존 Runbook의 초안 버전으로 가져올 수 있습니다. 스크립트 파일에는 단일 Windows PowerShell 워크플로가 포함되어야 합니다. Runbook에 초안 버전이 있는 경우 Overwrite 매개 변수를 사용하지 않으면 가져오기가 실패합니다. Runbook을 가져온 후에는 [Publish-AzureAutomationRunbook](https://msdn.microsoft.com/library/dn690266.aspx)으로 게시할 수 있습니다.

다음 명령 예제는 기존 Runbook에 스크립트 파일을 가져와 게시하는 방법을 보여줍니다.

    $automationAccountName = "MyAutomationAccount"
    $runbookName = "Sample-TestRunbook"
    $scriptPath = "c:\runbooks\Sample-TestRunbook.ps1"

    Set-AzureAutomationRunbookDefinition –AutomationAccountName $automationAccountName –Name $runbookName –Path $ scriptPath -Overwrite
    Publish-AzureAutomationRunbook –AutomationAccountName $automationAccountName –Name $runbookName


## Runbook 게시

새 Runbook을 만들거나 가져올 때는 게시해야 실행할 수 있습니다. Azure 자동화의 각 Runbook에는 초안 버전과 게시된 버전이 있습니다. 게시된 버전만 실행할 수 있으며 초안 버전만 편집할 수 있습니다. 초안 버전을 변경해도 게시된 버전은 영향을 받지 않습니다. 초안 버전을 사용할 수 있게 되면 이를 게시합니다. 그러면 초안 버전이 게시된 버전을 덮어씁니다.

## Azure 포털을 사용하여 Runbook을 게시하려면

1. Azure 포털에서 Runbook을 엽니다.
1. 화면 맨 위에서 **작성자**를 클릭합니다.
1. 화면 아래쪽에서 **게시**를 클릭한 다음 확인 메시지에서 **예**를 클릭합니다.

## Azure Preview 포털을 사용하여 Runbook을 게시하려면

1. Azure Preview 포털에서 Runbook을 엽니다.
1. **Edit** 단추를 클릭합니다.
1. **게시** 단추를 클릭한 다음 확인 메시지에서 **예**를 클릭합니다.


## Windows PowerShell을 사용하여 Runbook을 게시하려면

[Publish-AzureAutomationRunbook](https://msdn.microsoft.com/library/dn690266.aspx) cmdlet을 사용하여 Windows PowerShell에서 Runbook을 게시할 수 있습니다. 다음 명령 예제에서는 샘플 Runbook을 게시하는 방법을 보여줍니다.

	$automationAccountName = "MyAutomationAccount"
	$runbookName = "Sample-TestRunbook"
	
	Publish-AzureAutomationRunbook –AutomationAccountName $automationAccountName –Name $runbookName



## 관련된 문서

- [Azure 자동화용 Runbook 및 모듈 갤러리](automation-runbook-gallery.md)
- [Azure 자동화에서 텍스트 Runbook 편집](automation-edit-textual-runbook.md)
- [Azure 자동화에서 그래픽 작성](automation-graphical-authoring-intro.md)

<!---HONumber=Oct15_HO3-->