---
title: "Azure 자동화의 일정 | Microsoft Docs"
description: "자동화 일정은 Azure 자동화에서 Runbook이 자동으로 시작되도록 예약하는 데 사용됩니다. 특정 시간 또는 되풀이 일정에 따라 Runbook을 자동으로 시작할 수 있도록 일정을 만들고 관리하는 방법을 설명합니다."
services: automation
documentationcenter: 
author: MGoedtel
manager: jwhit
editor: tysonn
ms.assetid: 1c2da639-ad20-4848-920b-88e471b2e1d9
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/10/2016
ms.author: mgoedtel
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 26cbd468e6aa1613bd5f6c9944aedaafbd865a14


---
# <a name="scheduling-a-runbook-in-azure-automation"></a>Azure 자동화에서 Runbook 예약
Azure 자동화에서 Runbook이 지정된 시간에 시작되도록 예약하려면 해당 Runbook을 하나 이상의 일정에 연결합니다. Azure 클래식 포털에서 Runbook 및 Azure 포털에서 Runbook의 경우 한 번 실행하거나 매시간 또는 매일 일정으로 실행되도록 일정을 구성할 수 있습니다. 또한 주별, 월별, 주의 특정 요일이나 월의 며칠 또는 월의 특정 날짜에 예약할 수도 있습니다.  Runbook을 여러 일정에 연결할 수 있으며, 하나의 일정에 여러 Runbook을 연결할 수 있습니다.

> [!NOTE]
> 일정은 현재 Azure 자동화 DSC 구성을 지원하지 않습니다.
> 
> 

## <a name="windows-powershell-cmdlets"></a>Windows PowerShell cmdlet
다음 테이블의 cmdlet은 Azure 자동화에서 Windows PowerShell을 사용하여 일정을 만들고 관리하는 데 사용됩니다. 이러한 cmdlet은 [Azure PowerShell 모듈](../powershell-install-configure.md)의 일부로 제공됩니다.

| Cmdlet | 설명 |
|:--- |:--- |
| **Azure Resource Manager cmdlet** | |
| [Get-AzureRmAutomationSchedule](https://msdn.microsoft.com/library/mt603733.aspx) |일정을 검색합니다. |
| [New-AzureRmAutomationSchedule](https://msdn.microsoft.com/library/mt603577.aspx) |새 일정을 만듭니다. |
| [Remove-AzureRmAutomationSchedule](https://msdn.microsoft.com/library/mt603691.aspx) |일정을 제거합니다. |
| [Set-AzureRmAutomationSchedule](https://msdn.microsoft.com/library/mt603566.aspx) |기존 일정에 대한 속성을 설정합니다. |
| [Get-AzureRmAutomationScheduledRunbook](https://msdn.microsoft.com/library/mt619406.aspx) |예약된 Runbook을 검색합니다. |
| [Register-AzureRmAutomationScheduledRunbook](https://msdn.microsoft.com/library/mt603575.aspx) |Runbook을 일정에 연결합니다. |
| [Unregister-AzureRmAutomationScheduledRunbook](https://msdn.microsoft.com/library/mt603844.aspx) |일정에서 Runbook을 분리합니다. |
| **Azure Service Management cmdlet** | |
| [Get-AzureAutomationSchedule](http://msdn.microsoft.com/library/dn690274.aspx) |일정을 검색합니다. |
| [New-AzureAutomationSchedule](http://msdn.microsoft.com/library/dn690271.aspx) |새 일정을 만듭니다. |
| [Remove-AzureAutomationSchedule](http://msdn.microsoft.com/library/dn690279.aspx) |일정을 제거합니다. |
| [Set-AzureAutomationSchedule](http://msdn.microsoft.com/library/dn690270.aspx) |기존 일정에 대한 속성을 설정합니다. |
| [Get-AzureAutomationScheduledRunbook](http://msdn.microsoft.com/library/dn913778.aspx) |예약된 Runbook을 검색합니다. |
| [Register-AzureAutomationScheduledRunbook](http://msdn.microsoft.com/library/dn690265.aspx) |Runbook을 일정에 연결합니다. |
| [Unregister-AzureAutomationScheduledRunbook](http://msdn.microsoft.com/library/dn690273.aspx) |일정에서 Runbook을 분리합니다. |

## <a name="creating-a-schedule"></a>일정 만들기
Azure 포털, 클래식 포털에서 또는 Windows PowerShell을 사용하여 runbook에 대한 새 일정을 만들 수 있습니다. Azure 클래식 또는 Azure 포털을 사용하여 Runbook을 일정에 연결할 때 새 일정을 만들 수도 있습니다.

> [!NOTE]
> Runbook과 일정을 연결하는 경우 자동화는 계정에 모듈의 현재 버전을 저장하고 해당 일정에 연결합니다.  즉, 일정을 만들고 모듈을 버전 2.0으로 업데이트할 때 계정에 버전 1.0을 사용하는 모듈이 있는 경우 일정은 계속 1.0을 사용합니다.  업데이트된 모듈 버전을 사용하려면 새 일정을 만들어야 합니다. 
> 
> 

### <a name="to-create-a-new-schedule-in-the-azure-portal"></a>Azure 포털에서 새 일정을 만들려면
1. Azure 포털의 자동화 계정에서 **자산** 타일을 클릭하여 **자산** 블레이드를 엽니다.
2. **일정** 타일을 클릭하여 **일정** 블레이드를 엽니다.
3. 블레이드의 위쪽에서 **일정 추가**를 클릭합니다.
4. **새 일정** 블레이드에서 **이름**을 입력하고 선택적으로 새 일정에 대한 **설명**을 입력합니다.
5. **한 번** 또는 **되풀이**를 선택하여 일정이 한 번 실행되는지 또는 되풀이되어 실행되는지를 선택합니다.  **한 번**을 선택하는 경우 **시작 시간**을 지정한 다음 **만들기**를 클릭합니다.  **되풀이**를 선택하는 경우 **시작 시간**을 지정하고 얼마나 자주 runbook을 반복할지 빈도를 **시간**, **일**, **주** 또는 **달**로 지정합니다.  드롭 다운 목록에서 **주** 또는 **달**을 선택하는 경우 선택하면 **되풀이 옵션**이 블레이드에 나타납니다. **되풀이 옵션** 블레이드가 표시되면 **주**를 선택한 경우 요일을 선택할 수 있습니다.  **달**을 선택한 경우 **요일** 또는 달력에서 월의 특정한 날짜를 선택하고 마지막으로 월의 마지막 날에 실행할지 여부를 선택할 수 있습니다. 그런 다음 **확인**을 클릭합니다.   

### <a name="to-create-a-new-schedule-in-the-azure-classic-portal"></a>Azure 클래식 포털에서 새 일정을 만들려면
1. Azure 클래식 포털에서 자동화를 선택한 다음 자동화 계정의 이름을 선택합니다.
2. **자산** 탭을 선택합니다.
3. 창의 아래쪽의 **설정 추가**를 클릭합니다.
4. **일정 추가**를 클릭합니다.
5. **이름**을 입력하고 선택적으로 새 일정에 대한 **설명**을 입력합니다.일정은 **한 번**, **매시간**, **매일**, **매주** 또는 **매월** 실행됩니다.
6. **시작 시간** 을 지정하고 선택한 일정 유형에 따라 다른 옵션을 지정합니다.

### <a name="to-create-a-new-schedule-with-windows-powershell"></a>Windows PowerShell을 사용하여 새 일정을 만들려면
[New-AzureAutomationSchedule](http://msdn.microsoft.com/library/azure/dn690271.aspx) cmdlet을 사용하여 Azure 자동화에서 기존 runbook에 대한 새 일정을 만들거나 [New-AzureRmAutomationSchedule](https://msdn.microsoft.com/library/mt603577.aspx) cmdlet을 Azure 포털에서 Runbook에 사용할 수 있습니다. 일정을 실행해야 할 일정의 시작 시간 및 빈도를 지정해야 합니다.

다음 샘플 명령에서는 Azure Resource Manager cmdlet을 사용하여 매월 15일 및 30일에 일정을 만드는 방법을 보여 줍니다.

    $automationAccountName = "MyAutomationAccount"
    $scheduleName = "Sample-MonthlyDaysOfMonthSchedule"
    New-AzureRMAutomationSchedule –AutomationAccountName $automationAccountName –Name `
    $scheduleName -StartTime "7/01/2016 15:30:00" -MonthInterval 1 `
    -DaysOfMonth Fifteenth,Thirtieth -ResourceGroupName "ResourceGroup01"

다음 샘플 명령에서는 Azure 서비스 관리 cmdlet을 사용하여 2015년 1월 20일부터 매일 오후 3시 30분에 실행되는 새 일정을 만드는 방법을 보여 줍니다.

    $automationAccountName = "MyAutomationAccount"
    $scheduleName = "Sample-DailySchedule"
    New-AzureAutomationSchedule –AutomationAccountName $automationAccountName –Name `
    $scheduleName –StartTime "1/20/2016 15:30:00" –DayInterval 1

## <a name="linking-a-schedule-to-a-runbook"></a>Runbook에 일정 연결
Runbook을 여러 일정에 연결할 수 있으며, 하나의 일정에 여러 Runbook을 연결할 수 있습니다. Runbook에 매개 변수가 있는 경우 값을 제공할 수 있습니다. 필수 매개 변수의 값은 반드시 제공해야 하며, 선택적 매개 변수의 값은 필요에 따라 제공할 수 있습니다.  이러한 값은 Runbook이 이 일정에 따라 시작할 때마다 사용됩니다.  동일한 Runbook을 다른 일정에 연결하고 다른 매개 변수 값을 지정할 수 있습니다.

### <a name="to-link-a-schedule-to-a-runbook-with-the-azure-portal"></a>Azure 포털을 사용하여 Runbook에 일정을 연결하려면
1. Azure 포털의 자동화 계정에서 **Runbooks** 타일을 클릭하여 **Runbooks** 블레이드를 엽니다.
2. 예약할 Runbook의 이름을 클릭합니다.
3. 현재 Runbook이 일정에 연결되지 않은 경우 새 일정 만들거나 기존 일정에 연결하는 옵션이 제공됩니다.  
4. runbook에 매개 변수가 있는 경우 **실행 설정 수정(기본값: Azure)** 옵션을 선택할 수 있고 적절하게 정보를 입력할 수 있는 **매개 변수** 블레이드가 표시됩니다.  

### <a name="to-link-a-schedule-to-a-runbook-with-the-azure-classic-portal"></a>Azure 클래식 포털을 사용하여 Runbook에 일정을 연결하려면
1. Azure 클래식 포털에서 **자동화**를 선택한 다음 자동화 계정의 이름을 클릭합니다.
2. **Runbook** 탭을 선택합니다.
3. 예약할 Runbook의 이름을 클릭합니다.
4. **일정** 탭을 클릭합니다.
5. 현재 Runbook이 일정에 연결되지 않은 경우 **새 일정에 연결** 또는 **기존 일정에 연결** 옵션이 제공됩니다.  현재 Runbook이 일정에 연결되어 있는 경우 창 아래쪽의 **연결** 을 클릭하여 이러한 옵션에 액세스할 수 있습니다.
6. Runbook에 매개 변수가 있는 경우 해당 값에 대한 메시지가 표시됩니다.  

### <a name="to-link-a-schedule-to-a-runbook-with-windows-powershell"></a>Windows PowerShell을 사용하여 Runbook에 일정을 연결하려면
[Register-AzureAutomationScheduledRunbook](http://msdn.microsoft.com/library/azure/dn690265.aspx)을 사용하여 Azure 포털에서 Runbook에 대한 클래식 runbook 또는 [Register-AzureRmAutomationScheduledRunbook](https://msdn.microsoft.com/library/mt603575.aspx) cmdlet에 일정을 연결할 수 있습니다.  Parameters 매개 변수를 사용하여 Runbook의 매개 변수 값을 지정할 수 있습니다. 매개 변수 값 지정에 대한 자세한 내용은 [Azure 자동화에서 Runbook 시작](automation-starting-a-runbook.md) 을 참조하세요.

다음 샘플 명령에서는 매개 변수를 포함하는 Azure Resource Manager cmdlet을 사용하여 runbook에 일정을 연결하는 방법을 보여 줍니다.

    $automationAccountName = "MyAutomationAccount"
    $runbookName = "Test-Runbook"
    $scheduleName = "Sample-DailySchedule"
    $params = @{"FirstName"="Joe";"LastName"="Smith";"RepeatCount"=2;"Show"=$true}
    Register-AzureRmAutomationScheduledRunbook –AutomationAccountName $automationAccountName `
    –Name $runbookName –ScheduleName $scheduleName –Parameters $params `
    -ResourceGroupName "ResourceGroup01"
다음 샘플 명령에서는 매개 변수를 포함하는 Azure 서비스 관리 cmdlet을 사용하여 일정을 연결하는 방법을 보여 줍니다.

    $automationAccountName = "MyAutomationAccount"
    $runbookName = "Test-Runbook"
    $scheduleName = "Sample-DailySchedule"
    $params = @{"FirstName"="Joe";"LastName"="Smith";"RepeatCount"=2;"Show"=$true}
    Register-AzureAutomationScheduledRunbook –AutomationAccountName $automationAccountName `
    –Name $runbookName –ScheduleName $scheduleName –Parameters $params

## <a name="disabling-a-schedule"></a>일정 해제
일정을 해제하면 연결된 모든 Runbook이 더 이상 해당 일정에 실행되지 않습니다. 일정을 수동으로 해제하거나, 일정을 만들 때 빈도를 사용하여 일정에 대한 만료 시간을 설정할 수 있습니다. 만료 시간에 도달하면 일정이 해제됩니다.

### <a name="to-disable-a-schedule-from-the-azure-portal"></a>Azure 포털에서 일정을 비활성화하려면
1. Azure 포털의 자동화 계정에서 **자산** 타일을 클릭하여 **자산** 블레이드를 엽니다.
2. **일정** 타일을 클릭하여 **일정** 블레이드를 엽니다.
3. 일정 이름을 클릭하여 해당 세부 정보 블레이드를 엽니다.
4. **사용**을 **아니오**로 변경합니다.

### <a name="to-disable-a-schedule-from-the-azure-classic-portal"></a>Azure 클래식 포털에서 일정을 비활성화하려면
Azure 클래식 포털의 일정에 대한 일정 세부 정보 페이지에서 일정을 해제할 수 있습니다.

1. Azure 클래식 포털에서 자동화를 선택한 다음 자동화 계정의 이름을 클릭합니다.
2. 자산 탭을 선택합니다.
3. 일정 이름을 클릭하여 해당 세부 정보 페이지를 엽니다.
4. **사용**을 **아니오**로 변경합니다.

### <a name="to-disable-a-schedule-with-windows-powershell"></a>Windows PowerShell을 사용하여 일정을 해제하려면
[Set-AzureAutomationSchedule](http://msdn.microsoft.com/library/azure/dn690270.aspx) cmdlet을 사용하여 기존 runbook에 대한 기존 일정의 속성을 변경하거나 [Set-AzureRmAutomationSchedule](https://msdn.microsoft.com/library/mt603566.aspx) cmdlet을 Azure 포털에서 runbook에 사용할 수 있습니다. 일정을 해제하려면 **IsEnabled** 매개 변수에 대해 **false**를 지정합니다.

다음 샘플 명령에서는 Azure Resource Manager cmdlet을 사용하여 runbook에 일정을 비활성화하는 방법을 보여 줍니다.

    $automationAccountName = "MyAutomationAccount"
    $scheduleName = "Sample-MonthlyDaysOfMonthSchedule"
    Set-AzureRmAutomationSchedule –AutomationAccountName $automationAccountName `
    –Name $scheduleName –IsEnabled $false -ResourceGroupName "ResourceGroup01"

다음 샘플 명령에서는 Azure 서비스 관리 cmdlet을 사용하여 일정을 비활성화하는 방법을 보여 줍니다.

    $automationAccountName = "MyAutomationAccount"
    $scheduleName = "Sample-DailySchedule"
    Set-AzureAutomationSchedule –AutomationAccountName $automationAccountName `
    –Name $scheduleName –IsEnabled $false

## <a name="next-steps"></a>다음 단계
* Azure 자동화에서 Runbook을 시작하려면 [Azure 자동화에서 Runbook 시작](automation-starting-a-runbook.md) 




<!--HONumber=Nov16_HO3-->


