<properties 
   pageTitle="Azure 자동화의 자식 runbook | Microsoft Azure"
   description="다른 Runbook에서 Azure 자동화의 Runbook을 시작하고 서로 정보를 공유하는 다양한 방법을 설명합니다."
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
   ms.date="09/17/2015"
   ms.author="bwren" />

# Azure 자동화의 자식 runbook


다른 runbook에서 사용할 수 있는 불연속 함수를 사용하여 다시 사용할 수 있는 모듈식 runbook을 작성하는 Azure 자동화의 모범 사례입니다. 부모 runbook은 하나 이상의 자식 runbook를 자주 호출하여 필요한 기능을 수행합니다. 자식 Runbook을 호출하는 방법에는 두 가지가 있으며, 각기 다른 시나리오에 가장 적합한 방법을 결정할 수 있도록 각 방법의 차이점을 이해해야 합니다.

##  인라인 실행을 사용하여 자식 runbook 호출

다른 runbook에서 runbook 인라인을 호출하려면 runbook의 이름을 사용하고 활동 또는 cmdlet을 사용하는 것처럼 정확하게 해당 매개 변수에 대한 값을 제공합니다. 같은 자동화 계정에서 모든 runbook이 이런 방식으로 사용할 다른 모든 사용자에게 제공됩니다. 부모 runbook은 다음 줄으로 이동하기 전에 자식 runbook이 완료하기를 기다리고 어떤 출력도 직접 부모에게 반환됩니다.

runbook 인라인을 호출하면 동일한 작업에서 부모 runbook으로 실행됩니다. 실행된 자식 runbook의 작업 기록에는 표시가 없습니다. 자식 runbook에서 모든 예외 및 출력 스트림을 부모와 연결합니다. 자식 runbook에서 throw된 예외 및 해당 스트림 출력 중 하나가 부모 작업과 연결되므로 이것은 더 적은 작업에서 발생하며 쉽게 추적 및 문제 해결이 가능합니다.

runbook이 게시되면 호출하는 모든 자식 runbook은 이미 게시되어야 합니다. runbook이 컴파일될 때 Azure 자동화가 모든 자식 runbook과 연결을 빌드하기 때문입니다. 그렇지 않은 경우 부모 runbook은 올바르게 게시되도록 표시하지만 시작되면 예외를 생성합니다. 이 경우 자식 runbook을 제대로 참조하기 위해 부모 runbook을 다시 게시할 수 있습니다. 자식 runbook 중 하나가 변경되면 연결이 이미 만들어지기 때문에 부모 runbook을 다시 게시할 필요가 없습니다.

인라인을 호출하는 자식 runbook의 매개 변수는 복잡한 개체를 포함한 모든 데이터 형식이 될 수 있습니다. 그리고 Azure 관리 포털을 사용하거나 Start-AzureAutomationRunbook cmdlet과 함께 runbook을 시작하는 경우 [JSON 직렬화](automation-starting-a-runbook.md#runbook-parameters)는 없습니다.

### Runbook 형식

인라인 실행을 사용하는 [PowerShell Runbook](automation-runbook-types.md#powershell-runbooks)에서는 [PowerShell 워크플로 Runbook](automation-runbook-types.md#powershell-workflow-runbooks) 또는 [그래픽 Runbook](automation-runbook-types.md#graphical-runbooks)을 자식으로 사용할 수 없습니다. 마찬가지로, PowerShell 워크플로 Runbook 또는 그래픽 Runbook에서는 인라인 실행을 사용하는 PowerShell Runbook을 자식으로 사용할 수 없습니다. PowerShell Runbook은 다른 PowerShell만 자식으로 사용할 수 있습니다. 그래픽 및 PowerShell 워크플로 Runbook은 서로를 자식 Runbook으로 사용할 수 있습니다.

인라인 실행을 사용하여 그래픽 또는 PowerShell 워크플로 자식 Runbook을 호출하는 경우 Runbook의 이름만 사용합니다. PowerShell 자식 Runbook을 호출하는 경우 스크립트가 로컬 디렉터리에 있음을 지정하기 위해 이름 앞에 *.\*를 추가해야 합니다.

### 예

다음 예제는 세 매개 변수인 복잡한 개체, 정수 및 부울 값을 허용하는 테스트 자식 runbook을 호출합니다. 자식 runbook의 출력을 변수에 할당합니다. 이 경우 자식 Runbook은 PowerShell 워크플로 Runbook입니다.

	$vm = Get-AzureVM –ServiceName "MyVM" –Name "MyVM"
	$output = Test-ChildRunbook –VM $vm –RepeatCount 2 –Restart $true

다음은 PowerShell Runbook을 자식으로 사용하는 동일한 예제입니다.

	$vm = Get-AzureVM –ServiceName "MyVM" –Name "MyVM"
	$output = .\Test-ChildRunbook.ps1 –VM $vm –RepeatCount 2 –Restart $true


##  cmdlet을 사용하여 자식 runbook 시작

[Start-AzureAutomationRunbook](http://msdn.microsoft.com/library/dn690259.aspx) cmdlet을 사용하여 [Windows PowerShell에서 Runbook 시작](../automation-starting-a-runbook.md#starting-a-runbook-with-windows-powershell)에서 설명한 대로 Runbook을 시작할 수 있습니다. cmdlet에서 자식 runbook을 시작하는 경우 부모 runbook은 자식 runbook에 대한 작업이 만들어진 직후 다음 줄으로 이동합니다. Runbook에서 출력을 검색해야 하는 경우 [Get-AzureAutomationJobOutput](http://msdn.microsoft.com/library/dn690268.aspx)을 사용하여 작업에 액세스해야 합니다.

cmdlet으로 시작된 자식 runbook에서 작업은 부모 runbook의 별도 작업에서 실행됩니다. 스크립트 인라인을 호출하는 것 보다 많은 작업이 발생하고 추적하기 어려울 수 있습니다. 부모는 각각이 완료되기를 기다리지 않고 여러 자식 runbook을 시작할 수 있습니다. 인라인에서 자식 Runbook을 호출하는 동일한 종류의 병렬 실행에 대해 부모 Runbook은 [parallel 키워드](automation-powershell-workflow.md#parallel-processing)를 사용해야 합니다.

[Runbook 매개 변수](automation-starting-a-runbook.md#runbook-parameters)에서 설명한 대로 cmdlet을 사용하여 시작된 자식 Runbook에 대한 매개 변수는 해시 테이블로 제공됩니다. 단순한 데이터 형식만 사용할 수 있습니다. runbook에 복잡한 데이터 형식을 가진 매개 변수가 있는 경우 인라인으로 호출해야 합니다.

### 예

다음 예제는 매개 변수로 자식 runbook를 시작한 다음 완료되기를 기다립니다. 완료되면 해당 출력을 부모 runbook가 작업에서 수집합니다.

	$params = @{"VMName"="MyVM";"RepeatCount"=2;"Restart"=$true} 
	$job = Start-AzureAutomationRunbook –AutomationAccountName "MyAutomationAccount" –Name "Test- ChildRunbook" –Parameters $params
	
	$doLoop = $true
	While ($doLoop) {
	   $job = Get-AzureAutomationJob –AutomationAccountName "MyAutomationAccount" -Id $job.Id
	   $status = $job.Status
	   $doLoop = (($status -ne "Completed") -and ($status -ne "Failed") -and ($status -ne "Suspended") -and ($status -ne "Stopped") 
	}
	
	Get-AzureAutomationJobOutput –AutomationAccountName "MyAutomationAccount" -Id $job.Id –Stream Output

[Start-ChildRunbook](http://gallery.technet.microsoft.com/scriptcenter/Start-Azure-Automation-1ac858a9)은 TechNet 갤러리에서 사용할 수 있는 도우미 Runbook으로 cmdlet에서 Runbook을 시작합니다. 자식 runbook을 완료할 때까지 대기 하고 해당 출력을 검색하는 옵션을 제공합니다. 이 runbook은 고유의 Azure 자동화 환경 외에도 cmdlet을 사용하여 runbook 및 작업을 사용하는 작업에 참조로 사용할 수 있습니다. 도우미 runbook 자체는 hashtable 매개 변수를 필요로 하여 자식 runbook에 매개 변수 값을 허용하기 때문에 인라인으로 호출되어야 합니다.


## 자식 runbook을 호출하기 위한 방법 비교

다음 테이블은 다른 runbook에서 runbook을 호출하기 위한 두 메서드 간의 차이점을 요약합니다.

| | 인라인| Cmdlet|
|:---|:---|:---|
|작업|자식 runbook은 부모와 동일한 작업을 실행합니다.|자식 runbook에 대한 별도 작업을 만듭니다.|
|실행|계속하기 전에 부모 runbook은 자식 runbook이 완료되기를 기다립니다.|부모 runbook은 자식 runbook이 시작된 후에 즉시 계속됩니다.|
|출력|부모 runbook은 자식 runbook에서 출력을 직접 가져올 수 있습니다.|부모 runbook은 자식 runbook에서 출력을 검색해야 합니다.|
|매개 변수|자식 runbook 매개 변수 값은 별도로 지정되며 모든 데이터 형식을 사용할 수 있습니다.|자식 runbook 매개 변수 값은 단일 hashtable로 결합해야 하며 JSON 직렬화를 활용하는 간단한 배열 및 개체 데이터 형식만을 포함할 수 있습니다.|
|자동화 계정|부모 runbook은 같은 자동화 계정에서 자식 runbook을 사용할 수 있습니다.|부모 runbook은 연결된 경우 동일한 Azure 구독 및 심지어 다른 구독의 자동화 계정에서 자식 runbook을 사용할 수 있습니다.|
|게시|부모 runbook을 게시하기 전에 자식 runbook을 게시해야 합니다.|부모 runbook을 시작하기 전 언제든 자식 runbook을 게시해야 합니다.|

## 관련된 문서

- [Azure 자동화에서 Runbook 시작](automation-starting-a-runbook.md)
- [Azure 자동화에서 Runbook 출력 및 메시지](automation-runbook-output-and-messages.md)

<!---HONumber=Oct15_HO3-->