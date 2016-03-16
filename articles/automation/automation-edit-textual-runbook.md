<properties 
	pageTitle="Azure 자동화에서 텍스트 Runbook 편집"
	description="이 문서에서는 텍스트 편집기를 사용하여 Azure 자동화에서 PowerShell 및 PowerShell 워크플로 Runbook을 작업하기 위한 여러 절차를 제공합니다."
	services="automation"
	documentationCenter=""
	authors="mgoedtel"
	manager="stevenka"
	editor="tysonn" />
<tags 
	ms.service="automation"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="infrastructure-services"
	ms.date="02/23/2016"
	ms.author="magoedte;bwren" />

# Azure 자동화에서 텍스트 Runbook 편집

Azure 자동화에서 텍스트 편집기를 사용하여 [PowerShell Runbook](automation-runbook-types.md#powershell-runbooks) 및 [PowerShell 워크플로 Runbook](automation-runbook-types.md#powershell-workflow-runbooks)을 편집할 수 있습니다. 텍스트 편집기에는 Intellisense와 색 구분 같은 타 코드 편집기의 일반적인 기능과, Runbook에 일반적인 리소스를 액세스하는 데 도움이 되는 추가적인 특수 기능이 있습니다. 이 문서에서는 이 편집기를 사용하여 다양한 기능을 수행하기 위한 상세 단계를 제공합니다.

텍스트 편집기에는 활동, 자산 및 자식 Runbook에 대한 코드를 Runbook에 삽입하는 기능이 포함되어 있습니다. 코드를 직접 입력하지 않고 사용 가능한 리스소 목록에서 선택하여 적합한 코드를 Runbook에 에 삽입할 수 있습니다.

Azure 자동화의 각 Runbook에는 초안과 게시 등 두 버전이 있습니다. Runbook의 초안 버전을 편집한 다음 실행할 수 있도록 게시합니다. 게시된 버전은 편집할 수 없습니다. 자세한 내용은 [Runbook 게시](automation-creating-importing-runbook.md#publishing-a-runbook)를 참조하세요.

[그래픽 Runbook](automation-runbook-types.md#graphical-runbooks) 작업은 [Azure 자동화에서 그래픽 작성](automation-graphical-authoring-intro.md)을 참조하세요.

## Azure 포털을 사용하여 Runbook을 편집하려면

다음 절차를 사용하여 텍스트 편집기에서 편집할 Runbook을 엽니다.

1. Azure 포털에서 자동화 계정을 선택합니다.
2. **Runbook** 타일을 클릭하여 Runbook 목록을 엽니다.
3. 편집할 Runbook 이름을 클릭한 다음 **편집** 단추를 클릭합니다.
6. 필요한 편집을 수행합니다.
7. 편집이 완료되면 **저장**을 클릭합니다.
8. Runbook의 최신 초안 버전을 게시하려면 **게시**를 클릭합니다.

### Runbook에 cmdlet을 삽입하려면

2. 텍스트 편집기의 캔버스에서 cmdlet을 배치하려면 위치에 커서를 놓습니다.
3. 라이브러리 컨트롤에서 **Cmdlet** 노드를 확장합니다. 
3. 사용하려는 cmdlet을 포함하는 모듈을 확장합니다.
4. 마우스 오른쪽 단추로 삽입할 cmdlet을 클릭하고 **캔버스에 추가**를 선택합니다. Cmdlet에 둘 이상의 매개 변수를 설정한 경우 기본 설정이 추가됩니다. cmdlet를 확장하여 다른 매개 변수 설정을 선택할 수 있습니다.
4. Cmdlet의 코드가 전체 매개 변수 목록과 함께 삽입됩니다.
5. 필요한 매개 변수에 대해 괄호 <>로 둘러 쌓인 데이터 형식 대신에 적합한 값을 입력합니다. 필요가 없는 매개 변수는 제거합니다.

### Runbook에 자식 Runbook에 대한 코드를 삽입하려면

2. 텍스트 편집기의 캔버스에서 [자식 Runbook](automation-child-runbooks.md)을 배치하려는 위치에 커서를 놓습니다.
3. 라이브러리 컨트롤에서 **Runbook** 노드를 확장합니다. 
3. 마우스 오른쪽 단추로 삽입할 Runbook을 클릭하고 **캔버스에 추가**를 선택합니다.
4. 자식 Runbook의 코드가 Runbook 매개 변수의 자리 표시자와 함께 삽입됩니다.
5. 자리 표시자를 각 매개 변수에 적합한 값으로 바꿉니다.

### Runbook에 자산을 삽입하려면

2. 텍스트 편집기의 캔버스에서 코드를 배치하려는 위치에 커서를 놓습니다.
3. 라이브러리 컨트롤에서 **자산** 노드를 확장합니다. 
4. 원하는 자산 유형의 노드를 확장합니다.
3. 마우스 오른쪽 단추로 삽입할 자산을 클릭하고 **캔버스에 추가**를 선택합니다. [변수 자산](automation-variables.md)의 경우 변수를 가져올지 또는 설정할지 여부에 따라 **"변수 가져오기"를 캔버스에 추가** 또는 **"변수 설정"을 캔버스에 추가** 중에서 선택합니다.
4. 자산에 대한 코드가 Runbook에 삽입됩니다.



## Azure 포털을 사용하여 Runbook을 편집하려면

다음 절차를 사용하여 텍스트 편집기에서 편집할 Runbook을 엽니다.

1. Azure 포털에서 **자동화**를 선택한 다음 자동화 계정의 이름을 클릭합니다.
2. **Runbook** 탭을 선택합니다.
3. 편집할 Runbook 이름을 클릭한 다음 **작성** 단추를 선택합니다.
5. 화면 아래쪽에 있는 **편집** 단추를 클릭합니다.
6. 필요한 편집을 수행합니다.
7. 편집이 완료되면 **저장**을 클릭합니다.
8. Runbook의 최신 초안 버전을 게시하려면 **게시**를 클릭합니다.

### Runbook에 활동을 삽입하려면

1. 텍스트 편집기의 캔버스에서 활동을 배치할 위치에 커서를 놓습니다.
1. 화면 아래쪽에서 **삽입**과 **활동**을 차례로 클릭합니다.
1. **통합 모듈** 열에서 활동이 포함 된 모듈을 선택합니다.
1. **활동** 창에서 활동을 선택합니다.
1. **설명** 열에서 활동의 설명을 확인합니다. 선택적으로 상세 도움말 보기를 클릭하여 브라우저에서 활동에 대한 도움말을 실행할 수 있습니다.
1. 오른쪽 화살표를 클릭합니다. 활동에 매개 변수가 있으면 정보를 위해 목록에 표시됩니다.
1. 선택 단추를 클릭합니다. 활동을 실행하는 코드가 Runbook에 삽입됩니다.
1. 활동에 매개 변수가 필요한 경우 괄호 <>로 둘러 쌓인 데이터 형식 대신에 적합한 값을 입력합니다.

### Runbook에 자식 Runbook에 대한 코드를 삽입하려면

1. 텍스트 편집기의 캔버스에서 [자식 Runbook](automation-child-runbooks.md)을 배치하려는 위치에 커서를 놓습니다.
2. 화면 아래쪽에서 **삽입**과 **Runbook**을 차례로 클릭합니다.
3. 가운데 열부터 삽입할 Runbook을 선택하고 오른쪽 화살표를 클릭합니다.
4. Runbook에 매개 변수가 있으면 정보를 위해 목록에 표시됩니다.
5. 선택 단추를 클릭합니다. 선택한 Runbook을 실행하는 코드가 현재 Runbook에 삽입됩니다.
7. Runbook에 매개 변수가 필요한 경우 괄호 <>로 둘러 쌓인 데이터 형식 대신에 적합한 값을 입력합니다.

### Runbook에 자산을 삽입하려면

1. 텍스트 편집기의 캔버스에서 자산을 가져오는 활동을 배치할 위치에 커서를 놓습니다.
1. 화면 아래쪽에서 **삽입**과 **설정**을 차례로 클릭합니다.
1. **설정 작업** 열에서 원하는 작업을 선택합니다.
1. 가운데 열에서 사용 가능한 자산을 선택합니다.
1. 선택 단추를 클릭합니다. 자산을 가져오거나 설정하는 코드가 Runbook에 삽입됩니다.



## Windows PowerShell을 사용하여 Azure 자동화 Runbook을 편집하려면

Windows PowerShell을 사용하여 Runbook을 편집하려면 원하는 편집기를 사용하고 .ps1 파일에 저장합니다. [Get AzureAutomationRunbookDefinition](http://aka.ms/runbookauthor/cmdlet/getazurerunbookdefinition) cmdlet을 사용하여 Runbook의 콘텐츠를 가져온 다음 [Set-AzureAutomationRunbookDefinition](http://aka.ms/runbookauthor/cmdlet/setazurerunbookdefinition) cmdlet을 사용하여 기존 초안 Runbook를 수정 항목으로 교체할 수 있습니다.

### Windows PowerShell을 사용하여 Runbook의 콘텐츠를 검색하려면

다음 예제 명령에서는 Runbook에 대한 스크립트를 검색하고 스크립트 파일에 저장하는 방법을 보여줍니다. 이 예에서는 초안 버전이 검색됩니다. 이 버전은 변경할 수 없지만 Runbook의 게시 버전은 검색할 수 있습니다.

    $automationAccountName = "MyAutomationAccount"
    $runbookName = "Sample-TestRunbook"
    $scriptPath = "c:\runbooks\Sample-TestRunbook.ps1"
    
    $runbookDefinition = Get-AzureAutomationRunbookDefinition -AutomationAccountName $automationAccountName -Name $runbookName -Slot Draft
    $runbookContent = $runbookDefinition.Content

    Out-File -InputObject $runbookContent -FilePath $scriptPath

### Windows PowerShell을 사용하여 Runbook의 콘텐츠를 변경하려면

다음 샘플 명령에서는 스크립트 파일의 콘텐츠로 기존 Runbook 콘텐츠를 대체하는 방법을 보여줍니다. 이 내용은 [Windows PowerShell을 사용하여 스크립트 파일에서 Runbook을 가져오려면](../automation-creating-or-importing-a-runbook#ImportRunbookScriptPS)에서 설명한 절차와 동일한 예제입니다.

    $automationAccountName = "MyAutomationAccount"
    $runbookName = "Sample-TestRunbook"
    $scriptPath = "c:\runbooks\Sample-TestRunbook.ps1"

    Set-AzureAutomationRunbookDefinition -AutomationAccountName $automationAccountName -Name $runbookName -Path $scriptPath -Overwrite
    Publish-AzureAutomationRunbook –AutomationAccountName $automationAccountName –Name $runbookName

## 관련된 문서

- [Azure 자동화에서 Runbook 만들기 또는 가져오기](automation-creating-importing-runbook.md)
- [PowerShell 워크플로 학습](automation-powershell-workflow.md)
- [Azure 자동화에서 그래픽 작성](automation-graphical-authoring-intro.md)
- [인증서](automation-certificates.md)
- [연결](automation-connections.md)
- [자격 증명](automation-credentials.md)
- [일정](automation-schedules.md)
- [변수](automation-variables.md)

<!---HONumber=AcomDC_0302_2016-->