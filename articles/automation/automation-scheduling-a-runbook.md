<properties 
   pageTitle="Azure 자동화에서 Runbook 예약"
   description="특정 시간 또는 되풀이 일정에 따라 Runbook을 자동으로 시작할 수 있도록 Azure 자동화에서 일정을 만드는 방법을 설명합니다."
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
   ms.date="06/30/2015"
   ms.author="bwren" />

# Azure 자동화에서 Runbook 예약

Azure 자동화에서 Runbook이 지정된 시간에 시작되도록 예약하려면 해당 Runbook을 하나 이상의 일정에 연결합니다. 한 번 실행되거나 지정된 시간 또는 일 수마다 되풀이되도록 일정을 구성할 수 있습니다. Runbook을 여러 일정에 연결할 수 있으며, 하나의 일정에 여러 Runbook을 연결할 수 있습니다.

## 일정 만들기

Azure 관리 포털 또는 Windows PowerShell을 사용하여 새 일정을 만들 수 있습니다. Azure 관리 포털을 사용하여 Runbook을 일정에 연결할 때 새 일정을 만들 수도 있습니다.

### Azure 관리 포털을 사용하여 새 일정을 만들려면

1. Azure 관리 포털에서 자동화를 선택한 다음 자동화 계정의 이름을 클릭합니다.
1. **자산** 탭을 선택합니다.
1. 창의 아래쪽의 **설정 추가**를 클릭합니다.
1. **일정 추가**를 클릭합니다.
1. **이름**을 입력하고 선택적으로 새 일정에 대한 **설명**을 입력합니다.
1. 일정을 **한 번**, **매시간** 또는 **매일** 실행할지 선택합니다.
1. **시작 시간**을 지정하고 선택한 일정 유형에 따라 다른 옵션을 지정합니다.

### Windows PowerShell을 사용하여 새 일정을 만들려면

[New-AzureAutomationSchedule](http://msdn.microsoft.com/library/azure/dn690271.aspx) cmdlet을 사용하여 Azure 자동화에서 새 일정을 만들 수 있습니다. 일정의 시작 시간 및 일정을 실행할 주기(한 번, 매시간 또는 매일)를 지정해야 합니다.

다음 명령 예제에서는 2015년 1월 20일부터 매일 오후 3시 30분에 실행되는 새 일정을 만드는 방법을 보여 줍니다.

	$automationAccountName = "MyAutomationAccount"
	$scheduleName = "Sample-DailySchedule"
	New-AzureAutomationSchedule –AutomationAccountName $automationAccountName –Name $scheduleName –StartTime "1/20/2015 15:30:00" –DayInterval 1

## Runbook에 일정 연결

Runbook을 여러 일정에 연결할 수 있으며, 하나의 일정에 여러 Runbook을 연결할 수 있습니다. Runbook에 매개 변수가 있는 경우 값을 제공할 수 있습니다. 필수 매개 변수의 값은 반드시 제공해야 하며, 선택적 매개 변수의 값은 필요에 따라 제공할 수 있습니다. 이러한 값은 Runbook이 이 일정에 따라 시작할 때마다 사용됩니다. 동일한 Runbook을 다른 일정에 연결하고 다른 매개 변수 값을 지정할 수 있습니다.

### Azure 관리 포털을 사용하여 Runbook에 일정을 연결하려면

1. Azure 관리 포털에서 **자동화**를 선택한 다음 자동화 계정의 이름을 클릭합니다.
1. **Runbook** 탭을 선택합니다.
1. 예약할 Runbook의 이름을 클릭합니다.
1. **일정** 탭을 클릭합니다.
2. 현재 Runbook이 일정에 연결되지 않은 경우 **새 일정에 연결** 또는 **기존 일정에 연결** 옵션이 제공됩니다. 현재 Runbook이 일정에 연결되어 있는 경우 창 아래쪽의 **연결**을 클릭하여 이러한 옵션에 액세스할 수 있습니다.
1. Runbook에 매개 변수가 있는 경우 해당 값에 대한 메시지가 표시됩니다.  

### Windows PowerShell을 사용하여 Runbook에 일정을 연결하려면

[Register-AzureAutomationScheduledRunbook](http://msdn.microsoft.com/library/azure/dn690265.aspx)을 사용하여 일정을 Runbook에 연결할 수 있습니다. Parameters 매개 변수를 사용하여 Runbook의 매개 변수 값을 지정할 수 있습니다. 매개 변수 값 지정에 대한 자세한 내용은 [Azure 자동화에서 Runbook 시작](automation-starting-a-runbook.md)을 참조하세요.

다음 명령 예제에서는 매개 변수를 사용하여 Runbook에 일정을 연결하는 방법을 보여 줍니다.

	$automationAccountName = "MyAutomationAccount"
	$runbookName = "Test-Runbook"
	$scheduleName = "Sample-DailySchedule"
	$params = @{"FirstName"="Joe";"LastName"="Smith";"RepeatCount"=2;"Show"=$true}
	Register-AzureAutomationScheduledRunbook –AutomationAccountName $automationAccountName –Name $runbookName –ScheduleName $scheduleName –Parameters $params

## 일정 해제

일정을 해제하면 연결된 모든 Runbook이 더 이상 해당 일정에 실행되지 않습니다. 일정을 수동으로 해제하거나, 일정을 만들 때 매시간 및 매일 일정에 대한 만료 시간을 설정할 수 있습니다. 만료 시간에 도달하면 일정이 해제됩니다.

### Azure 관리 포털을 사용하여 일정을 해제하려면

Azure 관리 포털의 일정에 대한 일정 세부 정보 페이지에서 일정을 해제할 수 있습니다.

1. Azure 관리 포털에서 자동화를 선택한 다음 자동화 계정의 이름을 클릭합니다.
1. 자산 탭을 선택합니다.
1. 일정 이름을 클릭하여 해당 세부 정보 페이지를 엽니다.
2. **사용**을 **아니오**로 변경합니다.

### Windows PowerShell을 사용하여 일정을 해제하려면

[Set-AzureAutomationSchedule](http://msdn.microsoft.com/library/azure/dn690270.aspx) cmdlet을 사용하여 기존 일정의 속성을 변경할 수 있습니다. 일정을 해제하려면 **IsEnabled** 매개 변수에 대해 **false**를 지정합니다.

다음 명령 예제에서는 일정을 해제하는 방법을 보여 줍니다.

	$automationAccountName = "MyAutomationAccount"
	$scheduleName = "Sample-DailySchedule"
	Set-AzureAutomationSchedule –AutomationAccountName $automationAccountName –Name $scheduleName –IsEnabled $false

## 관련된 문서

- [Azure 자동화의 일정 자산](http://msdn.microsoft.com/library/azure/dn940016.aspx)
- [Azure 자동화에서 Runbook 시작](automation-starting-a-runbook.md) 

<!---HONumber=August15_HO6-->