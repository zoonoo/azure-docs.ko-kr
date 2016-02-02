<properties
	pageTitle="Azure 자동화의 내 첫 번째 PowerShell Runbook | Microsoft Azure"
	description="자습서는 간단한 PowerShell Runbook의 생성, 테스트, 게시를 단계별로 안내합니다."
	services="automation"
	documentationCenter=""
	authors="SnehaGunda"
	manager="stevenka"
	editor="tysonn"/>

<tags
	ms.service="automation"
	ms.workload="tbd"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article" 
	ms.date="12/19/2015"
	ms.author="sngun"/>


# 내 첫번째 PowerShell Runbook

> [AZURE.SELECTOR]
- [Graphical](automation-first-runbook-graphical.md)
- [PowerShell Workflow](automation-first-runbook-textual.md)
- [PowerShell](automation-first-runbook-textual-PowerShell.md)

이 자습서는 Azure 자동화에서 [PowerShell Runbook](automation-runbook-types.md#powershell-runbooks)을 만드는 과정을 안내합니다. 런북 작업의 상태를 추적하는 방법을 설명하는 동안 테스트하고 게시할 단순한 런북부터 시작하겠습니다. 그 다음, 런북을 실제로 Azure 리소스 관리를 통해 수정합니다. 이 경우에는 Azure 가상 컴퓨터를 가동합니다. 그리고 runbook 매개 변수를 추가하여 Runbook을 더욱 강력히 만듭니다.

## 필수 조건

이 자습서를 완료하려면 다음이 필요합니다.

- 동작합니다. 계정이 아직 없는 경우 [MSDN 구독자 혜택을 활성화](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)하거나 <a href="/pricing/free-trial/" target="_blank">[무료 평가판에 등록](https://azure.microsoft.com/pricing/free-trial/)할 수 있습니다.
- Runbook을 저장할 [자동화 계정](automation-configuring.md)
- Azure 가상 컴퓨터. 프로덕션이 되지 않게하기 위해 이 가상 컴퓨터를 중지하고 시작합니다.
- Azure 리소스에서 인증을 받을 [Azure Active Directory 사용자 및 자동화 자격 증명 자산](automation-configuring.md) 이 사용자는 가상 컴퓨터를 시작하고 중지할 수 있는 권한이 있어야 합니다.

## 1 단계-새 runbook 만들기

먼저 *Hello World*라는 텍스트를 출력하는 단순한 Runbook을 생성할 것입니다.
 
1. Azure Preview 포털에서 자동화 계정을 엽니다. 자동화 계정 페이지는 이 계정의 리소스 간략히 보기를 제공합니다. 사용자는 일부 자산이 이미 있어야 합니다. 대부분의 자산들은 새 자동화 계정에 자동적으로 포함되 있는 모듈입니다. 또한 사용자는 [필수 구성 요소](#prerequisites)에서 언급된 자격 증명 자산이 있어야 합니다.
2. **Runbook** 타일을 클릭하여 Runbook 목록을 엽니다. ![RunbooksControl](media/automation-first-runbook-textual-powershell/automation-runbooks-control.png)  
3. **Runbook 추가** 단추를 클릭하고 **새 Runbook 만들기**를 클릭하여 새 Runbook을 만듭니다.
4. Runbook 이름을 *MyFirstRunbook-PowerShell*로 지정합니다.
5. 이 경우 [PowerShell Runbook](automation-runbook-types.md#powershell-runbooks)을 만들 예정이므로 **Runbook 형식**으로 **Powershell**을 선택합니다. ![Runbook 형식](media/automation-first-runbook-textual-powershell/automation-runbook-type.png)  
6. **만들기**를 클릭하여 Runbook을 만들고 그래픽 편집기를 엽니다.

## 2단계 - Runbook에 코드 추가

runbook에 직접 코드를 입력하거나 라이브러리 컨트롤에서 cmdlet, Runbook 및 자산을 선택한 후 관련 매개 변수와 함께 Runbook에 추가되도록 할 수 있습니다. 이 연습에서는 Runbook에 직접 입력합니다.

1. Runbook은 현재 비어 있습니다. *쓰기 출력 "Hello World"*를 입력합니다. ![Hello World](media/automation-first-runbook-textual-powershell/automation-helloworld.png)  
2. **저장**을 클릭하여 Runbook을 저장합니다.![저장 단추](media/automation-first-runbook-textual-powershell/automation-save-button.png)  

## 3 단계 - runbook 테스트

프로덕션 환경에서 사용할 수 있도록 runbook을 게시하기 전에 제대로 작동하는지 확인할 수 있게 테스트합니다. Runbook을 테스트할 때 **초안**버전을 실행하고 해당 출력을 대화형으로 봅니다.
 
1. 테스트 창을 열려면 **테스트 창**을 클릭합니다.![테스트 창](media/automation-first-runbook-textual-powershell/automation-testpane.png)  
2. **시작**을 클릭하여 테스트를 시작합니다. 유일하게 사용 가능한 옵션이어야 합니다.
3. 이 창에서 [runbook 작업](automation-runbook-execution)이 생성되고 상태를 보여줍니다. 작업 상태는 클라우드의 Runbook 작업자가 사용 가능해질 때까지 기다리고 있음을 나타내는 *대기 중*에서 시작됩니다. 작업자가 작업을 요구한 경우, *시작 중*으로 바뀐 다음 Runbook이 실제로 실행되기 시작하면 *실행 중*으로 바뀝니다.  
4. Runbook 작업이 완료되면 해당 출력이 표시됩니다. 여기서는 *Hello World*가 표시됩니다. ![테스트 창 출력](media/automation-first-runbook-textual-powershell/automation-testpane-output.png)  
5. 캔버스로 돌아가려면 테스트 창을 닫습니다.

## 4 단계 - runbook 게시 및 시작

방금 만든 runbook은 아직 초안 모드입니다. 프로덕션 환경에서 실행하기 전에 게시해야 합니다. Runbook을 게시하면 초안 버전으로 기존의 게시된 버전을 덮어씁니다. 사용자의 경우, 방금전에 runbook을 만들었기 때문에 게시 버전이 아직 없습니다.

1. **게시**를 클릭하여 Runbook을 게시한 다음 확인 메시지가 표시되면 **예**를 클릭합니다. ![게시 단추](media/automation-first-runbook-textual-powershell/automation-publish-button.png)  
2. **Runbook** 창의 Runbook을 보기 위해 왼쪽으로 스크롤하면 **작성 상태**가 **게시됨**으로 표시됩니다.
3. 오른쪽으로 다시 스크롤하면 **MyFirstRunbook-PowerShell** 창이 표시됩니다. 위쪽에 표시되는 옵션을 사용하여 Runbook을 시작하거나, Runbook을 보거나, 미래의 특정 시간에 시작하도록 예약하거나, [webhook](automation-webhooks.md)을 생성하여 HTTP 호출을 통해 시작할 수 있습니다. 
4. Runbook을 시작하고자 하므로 Runbook 시작 블레이드를 열 때 **시작**을 클릭한 다음 **확인**을 클릭합니다. ![시작 단추](media/automation-first-runbook-textual-powershell/automation-start-button.png)  
5. 우리가 방금 만들었던 runbook작업에 대한 작업 창이 열립니다. 창을 닫을 수 있지만, 이 경우에는 작업의 진행 상황을 보기 위해 열어둡니다.
6. 작업 상태가 **작업 요약**에 표시되며 Runbook을 테스트할 때의 상태와 일치합니다.![작업 요약](media/automation-first-runbook-textual-powershell/automation-job-summary.png)  
7. Runbook 상태가 *완료됨*으로 표시되면 **출력**을 클릭합니다. 출력 창이 열리면 *Hello World*를 볼 수 있습니다. ![작업 출력](media/automation-first-runbook-textual-powershell/automation-job-output.png)
8. 출력 창을 닫습니다.
9. **모든 로그**를 클릭하여 Runbook 작업에 대한 스트림 창을 엽니다. 출력 스트림에 *Hello World*만 표시되어야 하지만 Runbook이 자세한 정보 표시 및 오류와 같은 Runbook 작업에 대한 다른 스트림에 쓰는 경우 해당 스트림이 표시될 수 있습니다. ![모든 로그](media/automation-first-runbook-textual-powershell/automation-alllogs.png)  
10. MyFirstRunbook-PowerShell 창으로 돌아가려면 스트림 창 및 작업 창을 닫습니다.
11. **작업**을 클릭하여 이 Runbook에 대한 작업 창을 엽니다. runbook으로 만든 모든 작업을 나열합니다. 작업을 한 번만 실행했으므로 하나의 작업만 표시됩니다. ![작업 목록](media/automation-first-runbook-textual-powershell/automation-job-list.png)  
12. runbook을 시작했을 때 우리가 봤던 동일한 작업창을 열려면 이 작업을 클릭합니다. 이 기능을 사용하면 예전으로 돌아가 특정 runbook으로 생성된 모든 작업의 세부 정보를 볼 수 있습니다.

## 5 단계-Azure 리소스를 관리 인증 추가

지금까지 runbook을 테스트 하고 게시했지만, 딱히 유용하지는 않습니다. Azure 리소스를 관리하고자 합니다. 그러나 [필수 조건](#prerequisites)에서 언급된 자격 증명을 사용하여 인증하지 않은 경우에는 Runbook을 통해 관리할 수 없습니다. **Add-AzureAccount** cmdlet을 사용하여 리소스를 관리합니다.

1. MyFirstRunbook-PowerShell 창에 있는 **편집**을 클릭하여 텍스트 편집기를 엽니다. ![Runbook 편집](media/automation-first-runbook-textual-powershell/automation-edit-runbook.png)  
2. **Write-Output** 줄은 더 이상 필요하지 않으므로 삭제합니다.
3. 라이브러리 컨트롤에서 **자산**, **자격 증명**을 차례로 확장합니다.
4. 자격 증명을 마우스 오른쪽 단추로 클릭하고 **캔버스에 추가**를 클릭합니다. 이렇게 하면 자격 증명에 대해 **Get-AutomationPSCredential** 작업이 추가됩니다.
5. **Get-AutomationPSCredential** 앞에 *$Credential =*을 입력하여 자격 증명을 변수에 할당합니다. 
6. 다음 줄에 *Add-AzureAccount -Credential $Credential*을 입력합니다.![자격 증명](media/automation-first-runbook-textual-powershell/automation-get-credential.png)
7. Runbook을 테스트할 수 있도록 **테스트 창**을 클릭합니다.
8. **시작**을 클릭하여 테스트를 시작합니다. 완료되면 구독 ID 형식, 계정의 테넌트를 포함하는 출력을 수신해야 합니다. 이를 통해 자격 증명이 유효한지 확인합니다.

## 6단계 - 가상 컴퓨터를 시작하기 위한 코드 추가

Runbook이 Azure 구독에서 인증을 받으므로 리소스를 관리할 수 있습니다. 가상 컴퓨터를 시작하는 명령을 추가합니다. Azure 구독에서 모든 가상 컴퓨터를 선택 할 수 있지만 지금은 cmdlet의 이름을 하드코딩합니다.

1. *Add-AzureAccount* 다음에 *Start-AzureVM -Name 'VMName' -ServiceName 'VMServiceName'*을 입력하여 시작할 가상 컴퓨터의 이름과 서비스 이름을 제공합니다. ![StartVM](media/automation-first-runbook-textual-powershell/automation-startvm.png)  
2. Runbook을 저장하고 테스트할 수 있도록 **테스트 창**을 클릭합니다.
3. **시작**을 클릭하여 테스트를 시작합니다. 완료되면, 가상 컴퓨터가 시작되었다는 것을 확인합니다.

## 7 단계 - runbook에 입력 매개 변수를 추가 합니다.

현재 Runbook은 Runbook에 하드 코딩된 가상 컴퓨터를 시작하지만 Runbook이 시작될 때 가상 컴퓨터를 지정할 수 있으면 더욱더 유용할 것입니다. 이제 해당 기능을 제공하도록 Runbook에 입력 매개 변수를 추가합니다.

1. *VMName* 및 *VMServiceName*에 대한 매개 변수를 Runbook에 추가하고 다음 그림과 같이 이러한 변수를 **Start-AzureVM** cmdlet에서 사용합니다. ![매개 변수 추가](media/automation-first-runbook-textual-powershell/automation-add-parameter.png)  
2. Runbook을 저장하고 테스트 창을 엽니다. 사용자는 테스트에 사용될 두 개의 입력변수에 대한 값을 제공할 수 있음을 참고하세요. 
3. 창을 닫습니다.
4. **게시**를 클릭하여 Runbook의 새 버전을 게시합니다.
5. 이전 단계에서 실행시킨 가상 컴퓨터를 중지합니다.
6. **시작**을 클릭하여 runbook을 시작합니다. 시작하려는 가상 컴퓨터의 **VMName** 및**VMServiceName**을 입력합니다. ![매개 변수 전달](media/automation-first-runbook-textual-powershell/automation-pass-parameter.png)  
7. Runbook이 완료되면 가상 컴퓨터가 시작되었다는 것을 확인합니다.

## PowerShell 워크플로의 차이점

PowerShell Runbook에는 PowerShell 워크플로 Runbook과 동일한 수명 주기, 기능 및 관리가 있지만 몇 가지 차이 및 제한 사항이 있습니다.

1. 컴파일 단계가 없기 때문에 PowerShell 워크플로 Runbook에 비해 PowerShell Runbook이 빠르게 실행됩니다.
2. PowerShell 워크플로 Runbook은 검사점 및 검사점 사용을 지원합니다. PowerShell Runbook은 처음부터만 다시 시작할 수 있지만 PowerShell 워크플로 Runbook은 Runbook의 어느 지점에서든지 다시 시작할 수 있습니다.
3. PowerShell Runbook은 직렬로 명령을 실행할 수 있는 반면 PowerShell 워크플로 Runbook은 병렬 및 직렬 실행을 지원합니다.
4. PowerShell Runbook에서 스크립트의 모든 항목은 단일 runspace에서 실행되는 반면 PowerShell 워크플로 Runbook에서 활동, 명령 또는 스크립트 블록에는 자체 runspace가 있을 수 있습니다. 또한 네이티브 PowerShell Runbook과 PowerShell 워크플로 Runbook 간에 몇 가지 [구문 차이](https://technet.microsoft.com/magazine/dn151046.aspx)가 있습니다. 


## 다음 단계

- 그래픽 Runbook을 시작하려면 [내 첫 번째 그래픽 Runbook](automation-first-runbook-graphical.md)을 참조하세요.
- PowerShell 워크플로 Runbook을 시작하려면 [내 첫 번째 PowerShell 워크플로 Runbook](automation-first-runbook-textual.md)을 참조하세요.
- Runbook 형식, 해당 장점 및 제한 사항에 대해 자세히 확인하려면 [Azure 자동화 Runbook 형식](automation-runbook-types.md)을 참조하세요.
- PowerShell 스크립트 지원 기능에 대한 자세한 내용은 [Azure 자동화에서 네이티브 PowerShell 스크립트 지원](https://azure.microsoft.com/blog/announcing-powershell-script-support-azure-automation-2/)을 참조하세요.

<!---HONumber=AcomDC_0128_2016-->