<properties
   pageTitle="Azure 자동화의 일정 | Microsoft Azure"
   description="자동화 일정은 Azure 자동화에서 Runbook이 자동으로 시작되도록 예약하는 데 사용됩니다. 이 문서에서는 일정을 만드는 방법을 설명합니다."
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
   ms.date="03/18/2016"
   ms.author="bwren" />

# Azure 자동화의 일정

자동화 일정은 Runbook이 자동으로 실행되도록 예약하는 데 사용됩니다. 이는 Runbook을 한 번 실행할 단일 날짜 및 시간이거나, Runbook을 여러 번 매시간, 매일, 매주 또는 매달 시작할 되풀이 일정일 수 있습니다. 일반적으로 Runbook에서는 일정에 액세스할 수 없습니다.

>[AZURE.NOTE]  일정은 현재 Azure 자동화 DSC 구성을 지원하지 않습니다.

## Windows PowerShell cmdlet

다음 테이블의 cmdlet은 Azure 자동화에서 Windows PowerShell을 사용하여 일정을 만들고 관리하는 데 사용됩니다. 이러한 cmdlet은 [Azure PowerShell 모듈](../powershell-install-configure.md)의 일부로 제공됩니다.

|Cmdlet|설명|
|:---|:---|
|[Get-AzureAutomationSchedule](http://msdn.microsoft.com/library/dn690274.aspx)|일정을 검색합니다.|
|[New-AzureAutomationSchedule](http://msdn.microsoft.com/library/dn690271.aspx)|새 일정을 만듭니다.|
|[Remove-AzureAutomationSchedule](http://msdn.microsoft.com/library/dn690279.aspx)|일정을 제거합니다.|
|[Set-AzureAutomationSchedule](http://msdn.microsoft.com/library/dn690270.aspx)|기존 일정에 대한 속성을 설정합니다.|
|[Get-AzureAutomationScheduledRunbook](http://msdn.microsoft.com/library/dn913778.aspx)|예약된 Runbook을 검색합니다.|
|[Register-AzureAutomationScheduledRunbook](http://msdn.microsoft.com/library/dn690265.aspx)|Runbook을 일정에 연결합니다.|
|[Unregister-AzureAutomationScheduledRunbook](http://msdn.microsoft.com/library/dn690273.aspx)|일정에서 Runbook을 분리합니다.|

## 새 일정 만들기

### Azure 클래식 포털을 사용하여 새 일정을 만들려면


1. 자동화 계정에서 창의 위쪽에 있는 **자산**을 클릭합니다.
1. 창의 아래쪽의 **설정 추가**를 클릭합니다.
1. **일정 추가**를 클릭합니다.
1. 마법사를 완료하고 새 일정을 저장하는 확인란을 클릭합니다.

### Azure 포털을 사용하여 새 일정을 만들려면

1. 자동화 계정에서 **자산** 파트를 클릭하여 **자산** 블레이드를 엽니다.
1. **일정** 파트를 클릭하여 **일정** 블레이드를 엽니다.
1. 블레이드의 위쪽에서 **일정 추가**를 클릭합니다.
1. 양식을 완료하고 **만들기**를 클릭하여 새 일정을 저장합니다.

### Windows PowerShell을 사용하여 새 일정을 만들려면

[New-AzureAutomationSchedule](http://msdn.microsoft.com/library/dn690271.aspx) cmdlet은 새 일정을 만들고 기존 일정에 대한 값을 설정합니다. 다음 Windows PowerShell 명령 예제에서는 내일 정오에 시작하여 1년 동안 매일 실행되는 My Daily Schedule이라는 새 일정을 만듭니다.

	$automationAccountName = "MyAutomationAccount"
	$scheduleName = "My Daily Schedule"
	$startTime = (Get-Date).Date.AddDays(1).AddHours(12)
	$expiryTime = $startTime.AddYears(1)

	New-AzureAutomationSchedule –AutomationAccountName $automationAccountName –Name $scheduleName –StartTime $startTime –ExpiryTime $expiryTime –DayInterval 1


## 참고 항목
- [Azure 자동화에서 Runbook 예약](automation-scheduling-a-runbook.md)

<!---HONumber=AcomDC_0601_2016-->