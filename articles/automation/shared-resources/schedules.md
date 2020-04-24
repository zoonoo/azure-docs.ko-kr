---
title: Azure Automation에서 일정 관리
description: Automation 일정은 Azure Automation에서 Runbook이 자동으로 시작되도록 예약하는 데 사용됩니다. 특정 시간 또는 되풀이 일정에 따라 Runbook을 자동으로 시작할 수 있도록 일정을 만들고 관리하는 방법을 설명합니다.
services: automation
ms.service: automation
ms.subservice: shared-capabilities
author: mgoedtel
ms.author: magoedte
ms.date: 04/04/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 97dc7cb90f5ffc577a007c23074a0e8a75788fab
ms.sourcegitcommit: f7d057377d2b1b8ee698579af151bcc0884b32b4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/24/2020
ms.locfileid: "82114023"
---
# <a name="manage-schedules-in-azure-automation"></a>Azure Automation에서 일정 관리

Azure Automation에서 Runbook이 지정된 시간에 시작되도록 예약하려면 해당 Runbook을 하나 이상의 일정에 연결합니다. Azure Portal에서 일정이 Runbook에 대해 한 번 실행되거나 매시간 또는 매일 반복되도록 구성할 수 있습니다. 매주, 매월, 주의 특정 요일이나 매월 특정 요일에 또는 특정 날짜에 실행되도록 예약할 수도 있습니다. Runbook을 여러 일정에 연결할 수 있으며, 하나의 일정에 여러 Runbook을 연결할 수 있습니다.

> [!NOTE]
> 일정은 현재 Azure Automation DSC 구성을 지원하지 않습니다.

>[!NOTE]
>이 문서는 새 Azure PowerShell Az 모듈을 사용하도록 업데이트되었습니다. AzureRM 모듈은 적어도 2020년 12월까지 버그 수정을 수신할 예정이므로 계속 사용하셔도 됩니다. 새 Az 모듈 및 AzureRM 호환성에 대한 자세한 내용은 [새 Azure PowerShell Az 모듈 소개](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0)를 참조하세요. Hybrid Runbook Worker에 대 한 Az module 설치 지침은 [Azure PowerShell 모듈 설치](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)를 참조 하세요. Automation 계정의 경우 [Azure Automation에서 Azure PowerShell 모듈을 업데이트 하는 방법을](../automation-update-azure-modules.md)사용 하 여 모듈을 최신 버전으로 업데이트할 수 있습니다.

## <a name="powershell-cmdlets"></a>PowerShell Cmdlet

다음 표의 cmdlet은 Azure Automation에서 PowerShell을 사용 하 여 일정을 만들고 관리 하는 데 사용 됩니다. 이러한 cmdlet은 [Azure PowerShell 모듈](/powershell/azure/overview)의 일부로 제공됩니다.

| Cmdlet | 설명 |
|:--- |:--- |
| [AzAutomationSchedule](https://docs.microsoft.com/powershell/module/Az.Automation/Get-AzAutomationSchedule?view=azps-3.7.0) |일정을 검색합니다. |
| [AzAutomationScheduledRunbook](https://docs.microsoft.com/powershell/module/az.automation/get-azautomationscheduledrunbook?view=azps-3.7.0) |예약된 Runbook을 검색합니다. |
| [AzAutomationSchedule](https://docs.microsoft.com/powershell/module/Az.Automation/New-AzAutomationSchedule?view=azps-3.7.0) |새 일정을 만듭니다. |
| [AzAutomationScheduledRunbook](https://docs.microsoft.com/powershell/module/Az.Automation/Register-AzAutomationScheduledRunbook?view=azps-3.7.0) |Runbook을 일정에 연결합니다. |
| [AzAutomationSchedule](https://docs.microsoft.com/powershell/module/Az.Automation/Remove-AzAutomationSchedule?view=azps-3.7.0) |일정을 제거합니다. |
| [AzAutomationSchedule](https://docs.microsoft.com/powershell/module/Az.Automation/Set-AzAutomationSchedule?view=azps-3.7.0) |기존 일정에 대한 속성을 설정합니다. |
| [AzAutomationScheduledRunbook](https://docs.microsoft.com/powershell/module/Az.Automation/Unregister-AzAutomationScheduledRunbook?view=azps-3.7.0) |일정에서 Runbook을 분리합니다. |

## <a name="creating-a-schedule"></a>일정 만들기

Azure Portal 또는 PowerShell을 사용 하 여 runbook에 대 한 새 일정을 만들 수 있습니다.

> [!NOTE]
> Azure Automation은 예약된 새 작업이 실행될 때 Automation 계정의 최신 모듈을 사용합니다.  자동화하는 프로세스 및 Runbook에 영향을 주지 않으려면 먼저 테스트 전용 Automation 계정으로 일정을 연결한 모든 Runbook을 테스트해야 합니다.  이렇게 하면 예약된 Runbook이 제대로 계속 작동하는지 확인하고, 그렇지 않은 경우 프로덕션 환경에 업데이트된 Runbook 버전을 마이그레이션하기 전에 추가적으로 문제를 해결하고 필요한 모든 변경 내용을 적용할 수 있습니다.
> **모듈**에서 [Azure 모듈 업데이트](../automation-update-azure-modules.md) 옵션을 선택하여 수동으로 업데이트하지 않는 한 Automation 계정에서 새 버전의 모듈을 자동으로 가져오지 않습니다.

### <a name="create-a-new-schedule-in-the-azure-portal"></a>Azure Portal에서 새 일정을 만듭니다.

1. Azure Portal의 automation 계정에서 왼쪽의 **공유 리소스** 섹션 아래에 있는 **일정** 을 선택 합니다.
2. 페이지의 위쪽에서 **일정 추가**를 클릭합니다.
3. 새 일정 창에서 새 일정에 대 한 이름 및 설명 (선택 사항)을 입력 합니다.
4. **한 번** 또는 **되풀이**를 선택하여 일정을 한 번 실행할지 또는 되풀이 일정에 따라 실행할지를 선택합니다. **한 번**을 선택 하는 경우 시작 시간을 지정한 다음 **만들기**를 클릭 합니다. **되풀이**를 선택 하는 경우 시작 시간을 지정 합니다. **되풀이 간격**에서 시간, 일, 주 또는 월을 기준으로 runbook을 반복할 빈도를 선택 합니다.
    1. **주**를 선택 하는 경우 요일을 선택할 수 있습니다. 요일을 원하는 수만큼 선택합니다. 첫 번째 일정은 시작 시간 이후에 선택한 첫 번째 요일에 실행됩니다. 예를 들어 주말 일정을 선택 하려면 토요일과 일요일을 선택 합니다. 
    
       ![주말 되풀이 일정 설정](../media/schedules/week-end-weekly-recurrence.png)

    2. **Month**를 선택 하면 다른 옵션이 제공 됩니다. **월별 발생 빈도** 옵션에 대해 **월 일** 또는 **주**를 선택 합니다. **월 일**을 선택 하면 원하는 수의 기간을 선택할 수 있는 달력이 표시 됩니다. 현재 달에서 발생 하지 않는 31 등의 날짜를 선택 하면 일정이 실행 되지 않습니다. 일정이 해당 월의 말일에 실행되도록 하려면 **매월 말일 실행** 아래에서 **예**를 선택합니다. **평일**을 선택하는 경우에는 **되풀이 간격** 옵션이 표시됩니다. **첫 번째**, **두 번째**, **세 번째**, **네 번째** 또는 **마지막** 중 하나를 선택하고, 마지막으로 반복할 요일을 선택합니다.

       ![매월 첫 번째, 15 일 및 마지막 날의 월별 일정](../media/schedules/monthly-first-fifteenth-last.png)

5. 선택을 완료한 후 **만들기**를 클릭합니다.

### <a name="create-a-new-schedule-with-powershell"></a>PowerShell을 사용 하 여 새 일정 만들기

[AzAutomationSchedule](https://docs.microsoft.com/powershell/module/Az.Automation/New-AzAutomationSchedule?view=azps-3.7.0) cmdlet을 사용 하 여 일정을 만듭니다. 일정의 시작 시간 및 실행 빈도를 지정합니다. 다음 예에서는 다양 한 일정 시나리오를 만드는 방법을 보여 줍니다.

#### <a name="create-a-one-time-schedule"></a>일회성 일정 만들기

다음 명령 예제에서는 일회성 일정을 만듭니다.

```azurepowershell-interactive
$TimeZone = ([System.TimeZoneInfo]::Local).Id
New-AzAutomationSchedule -AutomationAccountName "ContosoAutomation" -Name "Schedule01" -StartTime "23:00" -OneTime -ResourceGroupName "ResourceGroup01" -TimeZone $TimeZone
```

#### <a name="create-a-recurring-schedule"></a>되풀이 일정 만들기

다음 예에서는 1 년에 오후 1 시에 실행 되는 되풀이 일정을 만드는 방법을 보여 줍니다.

```azurepowershell-interactive
$StartTime = Get-Date "13:00:00"
$EndTime = $StartTime.AddYears(1)
New-AzAutomationSchedule -AutomationAccountName "ContosoAutomation" -Name "Schedule02" -StartTime $StartTime -ExpiryTime $EndTime -DayInterval 1 -ResourceGroupName "ResourceGroup01"
```

#### <a name="create-a-weekly-recurring-schedule"></a>주별 되풀이 일정 만들기

다음 예에서는 평일에만 실행 되는 주별 일정을 만드는 방법을 보여 줍니다.

```azurepowershell-interactive
$StartTime = (Get-Date "13:00:00").AddDays(1)
[System.DayOfWeek[]]$WeekDays = @([System.DayOfWeek]::Monday..[System.DayOfWeek]::Friday)
New-AzAutomationSchedule -AutomationAccountName "ContosoAutomation" -Name "Schedule03" -StartTime $StartTime -WeekInterval 1 -DaysOfWeek $WeekDays -ResourceGroupName "ResourceGroup01"
```

#### <a name="create-a-weekly-recurring-schedule-for-weekends"></a>주말의 주간 되풀이 일정 만들기

다음 명령 예제에서는 주말에만 실행 되는 주별 일정을 만드는 방법을 보여 줍니다.

```azurepowershell-interactive
$StartTime = (Get-Date "18:00:00").AddDays(1)
[System.DayOfWeek[]]$WeekendDays = @([System.DayOfWeek]::Saturday,[System.DayOfWeek]::Sunday)
New-AzAutomationSchedule -AutomationAccountName "ContosoAutomation" -Name "Weekends 6PM" -StartTime $StartTime -WeekInterval 1 -DaysOfWeek $WeekendDays -ResourceGroupName "ResourceGroup01"
```

#### <a name="create-a-recurring-schedule-for-first-15th-and-last-days-of-the-month"></a>매월 첫 번째, 15 일 및 마지막 일에 대 한 되풀이 일정 만들기

다음 예에서는 월의 1 일과 15 일에 실행 되는 되풀이 일정을 만드는 방법을 보여 줍니다.

```azurepowershell-interactive
$StartTime = (Get-Date "18:00:00").AddDays(1)
New-AzAutomationSchedule -AutomationAccountName "TestAzureAuto" -Name "1st, 15th and Last" -StartTime $StartTime -DaysOfMonth @("One", "Fifteenth", "Last") -ResourceGroupName "TestAzureAuto" -MonthInterval 1
```

## <a name="linking-a-schedule-to-a-runbook"></a>Runbook에 일정 연결

Runbook을 여러 일정에 연결할 수 있으며, 하나의 일정에 여러 Runbook을 연결할 수 있습니다. Runbook에 매개 변수가 있는 경우 값을 제공할 수 있습니다. 필수 매개 변수의 값은 반드시 제공해야 하며, 선택적 매개 변수의 값은 필요에 따라 제공할 수 있습니다. 이러한 값은 Runbook이 이 일정에 따라 시작할 때마다 사용됩니다. 동일한 Runbook을 다른 일정에 연결하고 다른 매개 변수 값을 지정할 수 있습니다.

### <a name="link-a-schedule-to-a-runbook-with-the-azure-portal"></a>Azure Portal를 사용 하 여 일정을 runbook에 연결

1. Azure Portal의 automation 계정에서 **프로세스 자동화**아래에 있는 **runbook** 을 선택 합니다.
2. 예약할 Runbook의 이름을 클릭합니다.
3. 현재 runbook이 일정에 연결 되지 않은 경우 새 일정을 만들거나 기존 일정에 연결 하는 옵션이 제공 됩니다.
4. Runbook에 매개 변수가 있는 경우 **실행 설정 수정 (기본값: Azure)** 옵션을 선택 하면 매개 변수 창이 표시 됩니다. 여기에 매개 변수 정보를 입력할 수 있습니다.

### <a name="link-a-schedule-to-a-runbook-with-powershell"></a>PowerShell을 사용 하 여 runbook에 일정 연결

[AzAutomationScheduledRunbook](https://docs.microsoft.com/powershell/module/Az.Automation/Register-AzAutomationScheduledRunbook?view=azps-3.7.0) cmdlet을 사용 하 여 일정을 연결 합니다. Parameters 매개 변수를 사용하여 Runbook의 매개 변수 값을 지정할 수 있습니다. 매개 변수 값 지정에 대한 자세한 내용은 [Azure Automation에서 Runbook 시작](../automation-starting-a-runbook.md)을 참조하세요.
다음 예에서는 매개 변수와 함께 Azure Resource Manager cmdlet을 사용 하 여 일정을 runbook에 연결 하는 방법을 보여 줍니다.

```azurepowershell-interactive
$automationAccountName = "MyAutomationAccount"
$runbookName = "Test-Runbook"
$scheduleName = "Sample-DailySchedule"
$params = @{"FirstName"="Joe";"LastName"="Smith";"RepeatCount"=2;"Show"=$true}
Register-AzAutomationScheduledRunbook –AutomationAccountName $automationAccountName `
–Name $runbookName –ScheduleName $scheduleName –Parameters $params `
-ResourceGroupName "ResourceGroup01"
```

## <a name="scheduling-runbooks-to-run-more-frequently"></a>Runbook이 더 자주 실행 되도록 예약

Azure Automation에서 일정에 구성할 수 있는 가장 빈번한 간격은 1시간입니다. 일정을 이보다 더 자주 실행해야 하는 경우 두 가지 옵션이 있습니다.

* Runbook에 대 한 [webhook](../automation-webhooks.md) 을 만들고 [Azure Logic Apps](../../logic-apps/logic-apps-overview.md) 를 사용 하 여 웹 후크를 호출 합니다. Azure Logic Apps는 일정을 정의할 때 보다 세분화 된 세분성을 제공 합니다.

* 한 시간에 한 번씩 실행되는 일정을 4개 만들어서 서로 15분 이내에 시작되도록 설정합니다. 이 시나리오를 적용하면 Runbook을 다른 일정으로 15분마다 실행할 수 있습니다.

## <a name="disabling-a-schedule"></a>일정 해제

일정을 사용하지 않도록 설정하면 연결된 모든 Runbook이 해당 일정에서 더 이상 실행되지 않습니다. 일정을 수동으로 해제하거나, 일정을 만들 때 빈도를 사용하여 일정에 대한 만료 시간을 설정할 수 있습니다. 만료 시간에 도달 하면 일정이 사용 하지 않도록 설정 됩니다.

### <a name="disable-a-schedule-from-the-azure-portal"></a>Azure Portal에서 일정을 사용 하지 않도록 설정

1. Automation 계정에서 **공유 리소스**아래에 있는 **일정** 을 선택 합니다.
2. 일정 이름을 클릭하여 해당 세부 정보 창을 엽니다.
3. **사용**을 **아니오**로 변경합니다.

> [!NOTE]
> 이전에 시작 시간이 있는 일정을 사용 하지 않도록 설정 하려면 저장 하기 전에 시작 날짜를 미래의 시간으로 변경 해야 합니다.

### <a name="disable-a-schedule-with-powershell"></a>PowerShell에서 일정 사용 안 함

[AzAutomationSchedule](https://docs.microsoft.com/powershell/module/Az.Automation/Set-AzAutomationSchedule?view=azps-3.7.0) cmdlet을 사용 하 여 기존 일정의 속성을 변경할 수 있습니다. 일정을 사용 하지 않도록 설정 하려면 `IsEnabled` 매개 변수에 대해 False를 지정 합니다.

다음 예에서는 Azure Resource Manager cmdlet을 사용 하 여 runbook에 대 한 일정을 사용 하지 않도록 설정 하는 방법을 보여 줍니다.

```azurepowershell-interactive
$automationAccountName = "MyAutomationAccount"
$scheduleName = "Sample-MonthlyDaysOfMonthSchedule"
Set-AzAutomationSchedule –AutomationAccountName $automationAccountName `
–Name $scheduleName –IsEnabled $false -ResourceGroupName "ResourceGroup01"
```

## <a name="removing-a-schedule"></a>일정 제거

일정을 제거할 준비가 되 면 Azure Portal 또는 `Remove-AzureRmAutomationSchedule` cmdlet 중 하나를 사용할 수 있습니다. 이전 섹션에서 설명한 대로 사용 하지 않도록 설정 된 일정은 제거할 수 있습니다.

### <a name="remove-a-schedule-using-the-azure-portal"></a>Azure Portal를 사용 하 여 일정 제거

1. Automation 계정에서 **공유 리소스**아래에 있는 **일정** 을 선택 합니다.
2. 일정 이름을 클릭하여 해당 세부 정보 창을 엽니다.
3. **삭제**를 클릭합니다.

### <a name="remove-a-schedule-with-powershell"></a>PowerShell을 사용 하 여 일정 제거

[AzAutomationSchedule](https://docs.microsoft.com/powershell/module/Az.Automation/Remove-AzAutomationSchedule?view=azps-3.7.0) cmdlet을 사용 하 여 기존 일정을 삭제할 수 있습니다. 

```azurepowershell-interactive
$automationAccountName = "MyAutomationAccount"
$scheduleName = "Sample-MonthlyDaysOfMonthSchedule"
Remove-AzureRmAutomationSchedule -AutomationAccountName $automationAccountName ` -Name $scheduleName -ResourceGroupName "ResourceGroup01"
```

## <a name="next-steps"></a>다음 단계

* Azure Automation에서 runbook을 시작 하려면 [Azure Automation에서 Runbook 시작](../automation-starting-a-runbook.md)을 참조 하세요.