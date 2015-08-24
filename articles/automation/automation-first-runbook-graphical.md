<properties
	pageTitle="Azure 자동화에서 나의 첫 번째 graphic runbook"
	description="자습서는 간단한 그래픽 runbook의 생성, 테스트, 게시를 단계별로 안내합니다. Azure 리소스 인증, 입력 매개 변수, 조건부 링크 같은 여러 개념을 설명합니다."
	services="automation"
	documentationCenter=""
	authors="bwren"
	manager="stevenka"
	editor=""/>

<tags
	ms.service="automation"
	ms.workload="tbd"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article" 
	ms.date="08/13/2015"
	ms.author="bwren"/>


# 내 첫 번째 그래픽 Runbook

> [AZURE.SELECTOR]
- [Graphical](automation-first-runbook-graphical.md)
- [Textual](automation-first-runbook-textual.md)

이 자습서는 Azure 자동화에서 [그래픽 Runbook](automation-graphical-authoring-intro.md)을 만드는 과정을 안내합니다. 런북 작업의 상태를 추적하는 방법을 설명하는 동안 테스트하고 게시할 단순한 런북부터 시작하겠습니다. 그 다음, 런북을 실제로 Azure 리소스 관리를 통해 수정합니다. 이 경우에는 Azure 가상 컴퓨터를 가동합니다. 그리고 runbook 매개 변수 및 조건부 링크를 추가하여 runbook을 더욱 강력히 만듭니다.

## 필수 조건

이 자습서를 완료하려면 다음이 필요합니다.

- 동작합니다. 계정이 아직 없는 경우 [MSDN 구독자 혜택을 활성화](http://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)하거나 <a href="/pricing/free-trial/" target="_blank">[무료 평가판에 등록](http://azure.microsoft.com/pricing/free-trial/)할 수 있습니다.
- Runbook을 저장할 [자동화 계정](automation-configuring.md)
- Azure 가상 컴퓨터. 프로덕션이 되지 않게하기 위해 이 가상 컴퓨터를 중지하고 시작합니다.
- Azure 리소스에서 인증을 받을 [Azure Active Directory 사용자 및 자동화 자격 증명 자산](automation-configuring.md) 이 사용자는 가상 컴퓨터를 시작하고 중지할 수 있는 권한이 있어야 합니다.

## 1 단계-새 runbook 만들기

먼저 *Hello World*라는 텍스트를 출력하는 단순한 Runbook을 생성할 것입니다.

1. Azure Preview 포털에서 자동화 계정을 엽니다. 자동화 계정 페이지는 이 계정의 리소스 간략히 보기를 제공합니다. 사용자는 일부 자산이 이미 있어야 합니다. 대부분의 자산들은 새 자동화 계정에 자동적으로 포함되 있는 모듈입니다. 또한 사용자는 [필수 구성 요소](#prerequisites)에서 언급된 자격 증명 자산이 있어야 합니다.
2. **Runbook** 타일을 클릭하여 Runbook 목록을 엽니다.<br>![Runbook 컨트롤](media/automation-first-runbook-graphical/runbooks-control.png)
2. **Runbook 추가** 단추를 클릭하고 **새 Runbook 만들기**를 클릭하여 새 Runbook을 만듭니다.
3. Runbook 이름을 *MyFirstRunbook-Graphical*로 지정합니다.
4. 이 경우 **Runbook 유형**으로 **그래픽**을 선택하여 [그래픽 Runbook](automation-graphical-authoring-intro.md)을 만듭니다. <br>![새 runbook](media/automation-first-runbook-graphical/new-runbook.png)
5. **만들기**를 클릭하여 Runbook을 만들고 그래픽 편집기를 엽니다.

## 2 단계 - runbook에 활동을 추가 합니다.

편집기의 왼쪽에 있는 라이브러리 컨트롤은 사용자의 runbook에 추가할 활동을 선택할 수 있게 해줍니다. **Write-Output** cmdlet을 추가하여 Runbook의 텍스트를 출력합니다.

1.   라이브러리 컨트롤에서 **Cmdlet** 노드와 **Microsoft.PowerShell.Utility**를 차례로 확장합니다.<br>![Microsoft.PowerShell.Utility](media/automation-first-runbook-graphical/cmdlets-powershell-utility.png)
2.   목록의 아래로 스크롤하십시오. **Write-Output**을 마우스 오른쪽 단추로 클릭하고 **캔버스에 추가**를 클릭합니다.
4.   캔버스에서 **Write-Output** 활동을 클릭합니다. 활동을 구성할 수 있게 해주는 구성 제어판이 열립니다.
5.   **레이블**은 기본적으로 cmdlet의 이름이지만 보다 친숙하게 바꿀 수 있습니다. *Write Hello World to output*으로 변경합니다.
6.   **매개 변수**를 클릭하여 cmdlet 매개 변수의 값을 제공합니다. 일부 cmdlet는 여러 매개 변수 집합이 있으며, 사용자는 사용하고자 하는 것을 선택해야 합니다. 이 경우 **Write-Output**에는 매개 변수 집합이 하나만 있으므로 선택하지 않아도 됩니다.<br>![쓰기 출력 속성](media/automation-first-runbook-graphical/write-output-properties.png)
7.   **InputObject** 매개 변수를 선택합니다. 출력 스트림으로 보낼 텍스트를 지정하는 매개 변수입니다.
9.   **데이터 원본** 드롭다운에서 **PowerShell 식**을 선택합니다. **데이터 원본** 드롭다운은 매개 변수 값을 채우는 데 사용하는 다른 소스들을 제공합니다. 다른 활동, 자동화 자산, 혹은 Power Shell식 같은 특정 소스들로부터 출력을 사용할 수 있습니다. 여기서는 *Hello World* 텍스트만 출력하려고 합니다. PowerShell 식을 사용하고 문자열을 지정할 수 있습니다.
10.   **식** 상자에 *"Hello World"*를 입력한 후 **확인**을 두 번 클릭하여 캔버스로 돌아갑니다.<br>![PowerShell 식](media/automation-first-runbook-graphical/expression-hello-world.png)
11.   **저장**을 클릭하여 Runbook을 저장합니다.<br>![Runbook을 저장 합니다.](media/automation-first-runbook-graphical/runbook-edit-toolbar-save.png)

## 3 단계 - runbook 테스트

프로덕션 환경에서 사용할 수 있도록 runbook을 게시하기 전에 제대로 작동하는지 확인할 수 있게 테스트합니다. Runbook을 테스트할 때, **초안** 버전을 실행하고 해당 출력을 대화형으로 봅니다.
 
2. 테스트 창을 열려면 **테스트 창**을 클릭합니다.<br>![테스트 창](media/automation-first-runbook-graphical/runbook-edit-toolbar-test-pane.png)
2. **시작**을 클릭하여 테스트를 시작합니다. 유일하게 사용 가능한 옵션이어야 합니다.
3. 이 창에서 [Runbook 작업](automation-runbook-execution)이 생성되고 해당 상태가 표시됩니다. 작업 상태는 클라우드의 Runbook 작업자가 사용 가능해질 때까지 기다리고 있음을 나타내는 *대기 중*에서 시작됩니다. 작업자가 작업을 요구한 경우, *시작 중*으로 바뀐 다음 Runbook이 실제로 실행되기 시작하면 *실행 중*으로 바뀝니다.  
4. Runbook 작업이 완료되면 해당 출력이 표시됩니다. 여기서는 *Hello World*가 표시됩니다.<br>![Hello World](media/automation-first-runbook-graphical/test-output-hello-world.png)
5. 캔버스로 돌아가려면 테스트 창을 닫습니다.


## 4 단계 - runbook 게시 및 시작

방금 만든 runbook은 아직 초안 모드입니다. 프로덕션 환경에서 실행하기 전에 게시해야 합니다. Runbook을 게시하면 초안 버전으로 기존의 게시된 버전을 덮어씁니다. 사용자의 경우, 방금전에 runbook을 만들었기 때문에 게시 버전이 아직 없습니다.

1. **게시**를 클릭하여 Runbook을 게시한 다음 확인 메시지가 표시되면 **예**를 클릭합니다.<br>![게시](media/automation-first-runbook-graphical/runbook-edit-toolbar-publish.png)
2. **Runbook** 창의 Runbook을 보기 위해 왼쪽으로 스크롤하면 **작성 상태**가 **게시됨**으로 표시됩니다.
3. 오른쪽으로 다시 스크롤하면 **MyFirstRunbook** 창이 표시됩니다. 위쪽에 표시되는 옵션을 사용하여 Runbook을 시작하거나, 미래의 특정 시간에 시작하도록 예약하거나, [webhook](automation-webhooks.md)을 생성하여 HTTP 호출을 통해 시작할 수 있게 할 수 있습니다. 
4. 여기서는 Runbook을 시작하기만 하면 되므로 **시작**을 클릭하고 확인 메시지가 표시되면 **예**를 클릭합니다.<br>![Runbook 시작](media/automation-first-runbook-graphical/runbook-toolbar-start.png)
5. 우리가 방금 만들었던 runbook작업에 대한 작업 창이 열립니다. 창을 닫을 수 있지만, 이 경우에는 작업의 진행 상황을 보기 위해 열어둡니다.
6.  작업 상태가 **작업 요약**에 표시되며 Runbook을 테스트할 때의 상태와 일치합니다.<br>![작업 요약](media/automation-first-runbook-graphical/job-pane-summary.png)
7.  Runbook 상태가 *완료됨*으로 표시되면 **출력**을 클릭합니다. **출력** 창이 열리면 *Hello World*를 볼 수 있습니다.<br>![작업 요약](media/automation-first-runbook-graphical/job-pane-output.png)  
8.  출력 창을 닫습니다.
9.  **스트림**을 클릭하여 Runbook 작업에 대한 스트림 창을 엽니다. 출력 스트림에는 *Hello World*만 표시되지만 Runbook이 Runbook 작업에 대해 자세한 정보 및 오류 등의 다른 스트림을 쓰면 해당 스트림이 표시될 수 있습니다.<br>![작업 요약](media/automation-first-runbook-graphical/job-pane-streams.png) 
9. MyFirstRunbook 창으로 돌아가려면 스트림 창 및 작업 창을 닫습니다.
9.  **작업**을 클릭하여 Runbook에 대한 작업 창을 엽니다. runbook으로 만든 모든 작업을 나열합니다. 작업을 한 번만 실행했으므로 하나의 작업만 표시됩니다.<br>![작업](media/automation-first-runbook-graphical/runbook-control-jobs.png) 
9. runbook을 시작했을 때 우리가 봤던 동일한 작업창을 열려면 이 작업을 클릭합니다. 이 기능을 사용하면 예전으로 돌아가 특정 runbook으로 생성된 모든 작업의 세부 정보를 볼 수 있습니다.

## 5 단계-Azure 리소스를 관리 인증 추가

지금까지 runbook을 테스트 하고 게시했지만, 딱히 유용하지는 않습니다. Azure 리소스를 관리하고자 합니다. [필수 구성 요소](#prerequisites)에서 언급된 자격 증명을 사용하여 인증하지 않은 경우 이 작업을 수행할 수 없습니다. **Set-Azure Account** cmdlet으로 이 작업을 수행합니다.

1.  MyFirstRunbook 창에 있는 **편집**을 클릭하여 그래픽 편집기를 엽니다.<br> ![Runbook 편집](media/automation-first-runbook-graphical/runbook-toolbar-edit.png) 
2.  **Write Hello World to output**은 더 이상 필요하지 않으므로 마우스 오른쪽 버튼을 클릭하고 **삭제**를 선택합니다.
8.  라이브러리 컨트롤에서 **Cmdlet**과 **Azure**를 차례로 확장합니다. 
9.  캔버스에 **Add-AzureAccount**를 추가합니다.<br>![Add-AzureAccount](media/automation-first-runbook-graphical/add-azureaccount.png) 
12.  구성 창에서 **Add-AzureAccount**를 선택하고 **매개 변수**를 클릭합니다.
13.  **Add-AzureAccount**는 여려 매개 변수 집합을 갖고 있으므로, 매개 변수 값을 제공하기 전에 하나를 선택해야 합니다. **매개 변수 설정**을 클릭한 후 **사용자** 매개 변수 집합을 선택합니다.
14.  매개 변수 집합을 선택하면, 매개 변수는 작업 매개 변수 구성 창에 표시 됩니다. **자격 증명**을 클릭합니다.<br>![Azure 계정 매개 변수를 추가](media/automation-first-runbook-graphical/add-azureaccount-parameters.png)
15.  이 cmdlet를 자동화 계정의 자격 증명 자산으로 사용해야 하기 때문에 **데이터 원본**의 **자격 증명 자산**을 선택합니다.
16.  사용자 Azure 환경에서 가상 컴퓨터를 시작하고 중지하는 권한이 있는 자격 증명 자산을 선택합니다.<br>![Azure 계정 데이터 소스 추가](media/automation-first-runbook-graphical/credential-data-source.png)
17.  **확인**을 두 번 클릭하여 캔버스로 돌아갑니다.


## 6 단계 - 활동을 추가하여 가상 컴퓨터 시작하기

이제 가상 컴퓨터를 시작하기 위한 **Start-AzureVm**활동을 추가합니다. Azure 구독에서 모든 가상 컴퓨터를 선택 할 수 있지만 지금은 cmdlet의 이름을 하드코딩합니다.

1. **Cmdlet**와 **Azure**를 차례로 확장합니다.
2. **Start-AzureVM**을 캔버스에 추가하고 **Add-AzureAccount** 아래로 끕니다.
11.  아래에 원 모양이 나타날 때까지 **Add-AzureAccount** 위로 마우스를 가져갑니다. 원을 클릭하고 화살표를 **Start-AzureVm**으로 끕니다. 방금 만든 화살표는 *링크*입니다. Runbook은 **Add-AzureAccount**로 시작된 후 **Start-AzureVM**을 실행합니다.<br>![Start-azurevm](media/automation-first-runbook-graphical/start-azurevm.png)
5. **Start-AzureVM**을 선택합니다. **매개 변수**와 **매개 변수 집합**을 차례로 클릭하여 **Start-AzureVM**에 대한 집합을 표시합니다. **ByName** 매개 변수 집합을 선택합니다. **Name** 및 **ServiceName** 옆에는 느낌표가 있습니다. 이것은 매개 변수가 필요하다는 것을 나타냅니다.
7. **Name**을 선택합니다. **데이터 원본**에 대해 **상수 값**을 사용하고 시작할 가상 컴퓨터의 이름을 입력합니다. **확인**을 클릭합니다.
8. **ServiceName**을 선택합니다. **데이터 원본**에 대해 **상수 값**을 사용하고 시작할 가상 컴퓨터의 이름을 입력합니다. **확인**을 클릭합니다.<br>![Start-azurevm 매개 변수](media/automation-first-runbook-graphical/start-azurevm-params.png)
9. runbook을 테스트할 수 있도록 테스트 창을 클릭합니다.
10. **시작**을 클릭하여 테스트를 시작합니다. 완료되면, 가상 컴퓨터가 시작되었다는 것을 확인합니다.


## 7 단계 - runbook에 입력 매개 변수를 추가 합니다.

현재 Runbook은 **Start-AzureVM** cmdlet에서 지정한 가상 컴퓨터를 시작하지만 Runbook이 시작될 때 가상 컴퓨터를 지정할 수 있으면 더욱더 유용할 것입니다. 이제 runbook이 해당 기능을 제공하는 입력 매개 변수를 추가 합니다.

1. **MyFirstRunbook** 창에서 **편집**을 클릭하여 그래픽 편집기를 엽니다.
2. **입력 및 출력**과 **입력 추가**를 차례로 클릭하여 Runbook 입력 매개 변수 창을 엽니다.<br>![Runbook 입력 및 출력](media/automation-first-runbook-graphical/runbook-edit-toolbar-inputoutput.png)
4. **Name**에 대해 *VMName*을 지정합니다. **형식**에 대해 *문자열*을 유지하고 **필수**를 *예*로 변경합니다. **확인**을 클릭합니다.
5. *VMServiceName*이라는 두 번째 필수 입력 매개 변수를 만들고, **확인**을 클릭하여 **입력 및 출력** 창을 닫습니다.<br>![Runbook 입력 매개 변수](media/automation-first-runbook-graphical/input-parameters.png) 
6. **Start-AzureVM** 활동을 선택하고 **매개 변수**를 클릭합니다.
7. **Name**에 대한 **데이터 원본**을 **Runbook 입력**으로 바꾼 다음 **VMName**을 선택합니다.<br>![Start-azurevm Name 매개 변수](media/automation-first-runbook-graphical/vmname-property.png) 
8. **ServiceName**에 대한 **데이터 원본**을 **Runbook 입력**으로 바꾼 다음 **VMServiceName**을 선택합니다.<br>![Start-azurevm 매개 변수](media/automation-first-runbook-graphical/start-azurevm-params-inputs.png) 
9. Runbook을 저장하고 테스트 창을 엽니다. 사용자는 테스트에 사용될 두 개의 입력변수에 대한 값을 제공할 수 있음을 참고하세요. 
11.  창을 닫습니다.
12.  **게시**를 클릭하여 Runbook의 새 버전을 게시합니다.
13.  이전 단계에서 실행시킨 가상 컴퓨터를 중지합니다.
13.  **시작**을 클릭하여 Runbook을 시작합니다. 시작하려는 가상 컴퓨터의 **VMName** 및**VMServiceName**을 입력합니다.<br>![Runbook 시작](media/automation-first-runbook-graphical/start-runbook-input-params.png) 
14.  Runbook이 완료 되 면 가상 컴퓨터 시작 되었다는 것을 확인합니다.

## 8 단계 - 조건부 링크를 만들기

만약 runbook이 이미 시작되지 않은 경우 runbook을 시작하도록 시도하기 위해 runbook을 수정합니다. 가상 컴퓨터의 현재 상태를 포함하는 Runbook에 **Get-AzureVM** cmdlet을 추가하여 이 작업을 수행합니다. 그런 후 현재의 실행 중 상태가 중지될 경우에만 **Start-AzureVM**을 실행하는 조건부 링크를 추가합니다. Runbook 중지되지 않은 경우, 다음 메시지가 출력됩니다.

1. 그래픽 편집기에서 **MyFirstRunbook**을 엽니다.
2. **Add-AzureAccount**와 **Start-AzureVM** 간의 링크를 제거하려면 링크를 클릭하고 *Delete* 키를 누릅니다.
3. 라이브러리 컨트롤에서 **Cmdlet**과 **Azure**를 차례로 확장합니다.
4. 캔버스에 **Get-AzureVM**을 추가합니다.
5. **Add-AzureAccount**와 **Get-AzureVM**를 연결하는 링크를 만듭니다. **Get-AzureVM**과 **Start-AzureVM**을 연결하는 또 다른 링크를 만듭니다.<br>![Get AzureVM Runbook](media/automation-first-runbook-graphical/get-azurevm.png)   
6.  **Get-AzureVM**을 선택하고 **매개 변수**를 클릭합니다. *GetVMByServiceAndVMName* 매개 변수 집합을 선택합니다.
7.  **Name**은 **데이터 원본**을 **Runbook 입력**으로 설정한 다음 **VMName**을 선택합니다.   
7.  **ServiceName**은 **데이터 원본**을 **Runbook 입력**으로 설정한 다음 **VMServiceName**를 선택합니다.  
8.  **Get-AzureVM**과 **Start-AzureVM** 간의 링크를 선택합니다.
9.  구성 창에서 **조건 적용**을 **True**로 변경합니다. 링크가 점선으로 바뀌면 조건이 True로 해결되어 목표 활동이 실행된 것을 나타냅니다.
10.  **조건 식**으로 *$ActivityOutput['Get-AzureVM'].PowerState -eq "Stopped"*를 입력합니다. **Start-AzureVM**은 가상 컴퓨터가 중지된 경우에 실행됩니다.<br>![링크 조건](media/automation-first-runbook-graphical/link-condition.png) 
11.  라이브러리 컨트롤에서 **Cmdlet**과 **Microsoft.PowerShell.Utility**를 차례로 확장합니다.
12.  캔버스에 **Write-Output**을 추가합니다.
13.  **Get-AzureVM**과 **Write-Output** 간의 링크를 만듭니다.
14.  링크를 선택하고 **조건 적용**을 **True**로 변경합니다.
14.  **조건 식**으로 *$ActivityOutput['Get-AzureVM'].PowerState -ne "Stopped"*를 입력합니다. 가상 컴퓨터가 중지되지 않은 경우에만 **Write-Output**이 실행됩니다.<br>![쓰기 출력으로 Runbook](media/automation-first-runbook-graphical/write-output-link.png) 
15.  **Write-Output**을 선택하고 **매개 변수**를 클릭합니다.
16.  **InputObject**대해서는, **데이터 원본**을 **PowerShell 식**으로 변경하고 *"$VMName.Name already started."* 식을 입력합니다.
17.  Runbook을 저장하고 테스트 창을 엽니다.
18.  중지된 가상 컴퓨터로 runbook을 시작합니다.
19.  시작된 가상 컴퓨터로 runbook을 시작하고 이미 시작된 출력을 구해야 합니다.
 

## 관련된 문서

- [Azure 자동화에서 그래픽 작성](automation-graphical-authoring-intro.md)
- [내 첫 번째 텍스트 Runbook](automation-first-runbook-textual.md)

 

<!---HONumber=August15_HO7-->