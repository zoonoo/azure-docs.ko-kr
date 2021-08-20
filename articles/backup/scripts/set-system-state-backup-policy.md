---
title: 스크립트 샘플 - 새 시스템 상태 백업 정책을 만들거나 현재 시스템 상태 백업 정책 수정
description: 스크립트를 사용하여 새 시스템 상태 백업 정책을 만들거나 현재 시스템 상태 백업 정책을 수정하는 방법을 알아봅니다.
ms.topic: sample
ms.date: 06/23/2021
ms.openlocfilehash: 5adebca2bbf98307b33a13318334baf1670f5f19
ms.sourcegitcommit: f0168d80eb396ce27032aa02fe9da5a0c10b5af3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/23/2021
ms.locfileid: "112560818"
---
#  <a name="powershell-script-to-create-a-new-or-modify-the-current-system-state-backup-policy"></a>새 시스템 상태 백업 정책을 만들거나 현재 시스템 상태 백업 정책을 수정하기 위한 PowerShell 스크립트

이 스크립트를 사용하면 MARS 에이전트로 보호되는 서버에 대한 새 백업 정책을 만들거나 현재 시스템 상태 백업 정책을 수정할 수 있습니다.

## <a name="sample-script"></a>샘플 스크립트

```azurepowershell
<#

.SYNOPSIS
Modify system state policy

.DESCRIPTION
Modify system state policy

.ROLE
Administrators

#>
param (
    [Parameter(Mandatory = $true)]
    [string[]]
    $daysOfWeek,
    [Parameter(Mandatory = $true)]
    [string[]]
    $timesOfDay,
    [Parameter(Mandatory = $true)]
    [int]
    $weeklyFrequency,

    [Parameter(Mandatory = $false)]
    [int]
    $retentionDays,

    [Parameter(Mandatory = $false)]
    [Boolean]
    $retentionWeeklyPolicy,
    [Parameter(Mandatory = $false)]
    [int]
    $retentionWeeks,

    [Parameter(Mandatory = $false)]
    [Boolean]
    $retentionMonthlyPolicy,
    [Parameter(Mandatory = $false)]
    [int]
    $retentionMonths
)
Set-StrictMode -Version 5.0
$env:PSModulePath = (Get-ItemProperty -Path 'HKLM:\System\CurrentControlSet\Control\Session Manager\Environment' -Name PSModulePath).PSModulePath
Import-Module MSOnlineBackup
$ErrorActionPreference = "Stop"
Try {
    $oldPolicy = Get-OBSystemStatePolicy
    if ($oldPolicy) {
        return
    }
    $policy = New-OBPolicy
    $policy = Add-OBSystemState -Policy $policy

    $timesOfDaySchedule = @()
    foreach ($time in $timesOfDay) {
        $timesOfDaySchedule += ([TimeSpan]$time)
    }
    $daysOfWeekSchedule = @()
    foreach ($day in $daysOfWeek) {
        $daysOfWeekSchedule += ([System.DayOfWeek]$day)
    }

    $schedule = New-OBSchedule -DaysOfWeek $daysOfWeekSchedule -TimesOfDay $timesOfDaySchedule -WeeklyFrequency $weeklyFrequency
    if ($daysOfWeekSchedule.Count -eq 7) {
        if ($retentionWeeklyPolicy -and $retentionMonthlyPolicy) {
            $retention = New-OBRetentionPolicy -RetentionDays $retentionDays -RetentionWeeklyPolicy:$true -WeekDaysOfWeek $daysOfWeekSchedule -WeekTimesOfDay $timesOfDaySchedule -RetentionWeeks $retentionWeeks -RetentionMonthlyPolicy:$true -MonthDaysOfWeek $daysOfWeekSchedule -MonthTimesOfDay $timesOfDaySchedule -RetentionMonths $retentionMonths
        }
        elseif ($retentionWeeklyPolicy) {
            $retention = New-OBRetentionPolicy -RetentionDays $retentionDays -RetentionWeeklyPolicy:$true -WeekDaysOfWeek $daysOfWeekSchedule -WeekTimesOfDay $timesOfDaySchedule -RetentionWeeks $retentionWeeks
        }
        elseif ($retentionMonthlyPolicy) {
            $retention = New-OBRetentionPolicy -RetentionDays $retentionDays -RetentionMonthlyPolicy:$true -MonthDaysOfWeek $daysOfWeekSchedule -MonthTimesOfDay $timesOfDaySchedule -RetentionMonths $retentionMonths
        }
        else {
            $retention = New-OBRetentionPolicy -RetentionDays $retentionDays
        }
    }
    else {
        if ($retentionWeeklyPolicy -and $retentionMonthlyPolicy) {
            $retention = New-OBRetentionPolicy -RetentionWeeklyPolicy:$true -WeekDaysOfWeek $daysOfWeekSchedule -WeekTimesOfDay $timesOfDaySchedule -RetentionWeeks $retentionWeeks -RetentionMonthlyPolicy:$true -MonthDaysOfWeek $daysOfWeekSchedule -MonthTimesOfDay $timesOfDaySchedule -RetentionMonths $retentionMonths
        }
        elseif ($retentionWeeklyPolicy) {
            $retention = New-OBRetentionPolicy -RetentionWeeklyPolicy:$true -WeekDaysOfWeek $daysOfWeekSchedule -WeekTimesOfDay $timesOfDaySchedule -RetentionWeeks $retentionWeeks
        }
        elseif ($retentionMonthlyPolicy) {
            $retention = New-OBRetentionPolicy -RetentionMonthlyPolicy:$true -MonthDaysOfWeek $daysOfWeekSchedule -MonthTimesOfDay $timesOfDaySchedule -RetentionMonths $retentionMonths
        }
    }
    Set-OBSchedule -Policy $policy -Schedule $schedule
    Set-OBRetentionPolicy -Policy $policy -RetentionPolicy $retention
    Set-OBSystemStatePolicy -Policy $policy -Confirm:$false
}
Catch {
    if ($error[0].ErrorDetails) {
        throw $error[0].ErrorDetails
    }
    throw $error[0]
}

```

## <a name="how-to-execute-the-script"></a>스크립트를 실행하는 방법

1. 위의 스크립트를 선택한 이름과 .ps1 확장명으로 머신에 저장합니다.
1. 다음 매개 변수를 제공하여 스크립트를 실행합니다. <br> 백업 일정 및 백업을 보존해야 하는 기간(일/주/월/년 수)

## <a name="next-steps"></a>다음 단계

PowerShell을 사용하여 MARS 에이전트로 온-프레미스 백업을 배포하고 관리하는 방법을 [자세히 알아봅니다.](../backup-client-automation.md)