---
title: Azure DevTest Labs에서 자동화 runbook을 사용 하 여 컴퓨터 시작
description: Azure Automation runbook을 사용 하 여 Azure DevTest Labs에서 랩에서 가상 머신을 시작 하는 방법을 알아봅니다.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2020
ms.author: spelluru
ms.openlocfilehash: 9bb97a73b7ca570ca122323e8e9c5a70c9348b15
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "76166304"
---
# <a name="start-virtual-machines-in-a-lab-in-order-by-using-azure-automation-runbooks"></a>Azure Automation runbook을 사용 하 여 랩에서 가상 머신 시작
DevTest Labs [의 자동 시작 기능을](devtest-lab-set-lab-policy.md#set-autostart) 사용 하면 지정 된 시간에 vm이 자동으로 시작 되도록 구성할 수 있습니다. 그러나이 기능은 특정 순서로 컴퓨터를 시작 하는 것을 지원 하지 않습니다. 이러한 종류의 자동화가 유용한 몇 가지 시나리오가 있습니다.  한 가지 시나리오는 Jumpbox가 다른 vm에 대 한 액세스 지점으로 사용 되기 때문에 다른 Vm 보다 먼저 랩 내의 Jumpbox VM을 먼저 시작 해야 하는 경우입니다.  이 문서에서는 스크립트를 실행 하는 PowerShell runbook을 사용 하 여 Azure Automation 계정을 설정 하는 방법을 보여 줍니다. 스크립트는 랩에서 Vm의 태그를 사용 하 여 스크립트를 변경 하지 않고도 시작 순서를 제어할 수 있도록 합니다.

## <a name="setup"></a>설치 프로그램
이 예제에서 랩의 Vm에는 적절 한 값 (0, 1, 2 등)을 사용 하 여 **Startuporder** 태그가 추가 되어야 합니다. -1로 시작할 필요가 없는 모든 컴퓨터를 지정 합니다.

## <a name="create-an-azure-automation-account"></a>Azure Automation 계정 만들기
[이 문서의](../automation/automation-create-standalone-account.md)지침에 따라 Azure Automation 계정을 만듭니다. 계정을 만들 때 **실행 계정** 옵션을 선택 합니다. Automation 계정이 만들어지면 **모듈** 페이지를 열고 메뉴 모음에서 **Azure 모듈 업데이트** 를 선택 합니다. 기본 모듈은 몇 가지 이전 버전 이며 업데이트가 없는 경우 스크립트가 작동 하지 않을 수 있습니다.

## <a name="add-a-runbook"></a>Runbook 추가
이제 automation 계정에 runbook을 추가 하려면 왼쪽 메뉴에서 **runbook** 을 선택 합니다. 메뉴에서 **Runbook 추가** 를 선택 하 고 지침에 따라 [PowerShell runbook을 만듭니다](../automation/automation-first-runbook-textual-powershell.md).

## <a name="powershell-script"></a>PowerShell 스크립트
다음 스크립트는 구독 이름 및 랩 이름을 매개 변수로 사용 합니다. 이 스크립트 흐름은 랩에서 모든 Vm을 가져온 다음 태그 정보를 구문 분석 하 여 VM 이름 및 해당 시작 순서 목록을 만드는 것입니다. 이 스크립트는 vm을 순서 대로 실행 하 고 Vm을 시작 합니다. 특정 주문 번호에 여러 Vm이 있는 경우 PowerShell 작업을 사용 하 여 비동기적으로 시작 됩니다. 태그를 포함 하지 않는 Vm의 경우 시작 값을 마지막 (10)으로 설정 하면 기본적으로 마지막으로 시작 됩니다.  랩에서 VM을 자동 시작 하지 않으려는 경우 태그 값을 11로 설정 하면 무시 됩니다.

```powershell
#Requires -Version 3.0
#Requires -Module AzureRM.Resources

param
(
    [Parameter(Mandatory=$false, HelpMessage="Name of the subscription that has the lab")]
    [string] $SubscriptionName,

    [Parameter(Mandatory=$false, HelpMessage="Lab name")]
    [string] $LabName
)

# Connect and add the appropriate subscription
$Conn = Get-AutomationConnection -Name AzureRunAsConnection

Add-AzureRMAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationID $Conn.ApplicationId -Subscription $SubscriptionName -CertificateThumbprint $Conn.CertificateThumbprint

# Find the lab
$dtLab = Find-AzResource -ResourceType 'Microsoft.DevTestLab/labs' -ResourceNameEquals $LabName

# Get the VMs
$dtlAllVms = New-Object System.Collections.ArrayList
$AllVMs = Get-AzResource -ResourceId "$($dtLab.ResourceId)/virtualmachines" -ApiVersion 2016-05-15

# Get the StartupOrder tag, if missing set to be run last (10)
ForEach ($vm in $AllVMs) {
    if ($vm.Tags) {
        if ($vm.Tags['StartupOrder']) {
            $startupValue = $vm.Tags['StartupOrder']
        } else {
            $startupValue = 10
        }
        } else {
            $startupValue = 10
        }
        $dtlAllVms.Add(@{$vm.Name = $startupValue}) > $null
}

# Setup for the async multiple vm start

# Save profile
$profilePath = Join-Path $env:Temp "profile.json"
If (Test-Path $profilePath){
    Remove-Item $profilePath
}
Save-AzContext -Path $profilePath

# Job to start VMs asynch
$startVMBlock = {
    Param($devTestLab,$vmToStart,$profilePath)
    Import-AzContext -Path ($profilePath)
    Invoke-AzResourceAction `
        -ResourceId "$($devTestLab.ResourceId)/virtualmachines/$vmToStart" `
        -Action Start `
        -Force
    Write-Output "Started: $vmToStart"
}

$current = 0
# Start in order from 0 to 10

While ($current -le 10) {
# Get the VMs in the current stage
    $tobeStarted = $null
    $tobeStarted = $dtlAllVms | Where-Object { $_.Values -eq $current}
    if ($tobeStarted.Count -eq 1) {
        # Run sync – jobs not necessary for a single VM
        $returnStatus = Invoke-AzResourceAction `
                -ResourceId "$($dtLab.ResourceId)/virtualmachines/$($tobeStarted.Keys)" `
                -Action Start `
                -Force
        Write-Output "$($tobeStarted.Keys) status: $($returnStatus.status)"
    } elseif ($tobeStarted.Count -gt 1) {
        # Start multiple VMs async
        $jobs = @()
        Write-Output "Start Jobs start: $(Get-Date)"
        
        # Jobs
        $jobs += Start-Job -ScriptBlock $startVMBlock -ArgumentList $dtLab, $($singlevm.Keys), $profilePath
        Write-Output "Start Jobs end: $(Get-Date)"
    }

    # Get results from all jobs
    if($jobs.Count -ne 0) {
        Write-Output "Receive Jobs start: $(Get-Date)"
        foreach ($job in $jobs){
            $jobResult = Receive-Job -Job $job -Wait | Write-Output
        }
        Remove-Job -Job $jobs -Force
    }
    else
    {
        Write-Output "Information: No jobs available"
    }
}
```

## <a name="create-a-schedule"></a>일정 만들기
이 스크립트를 매일 실행 하려면 automation 계정에 [일정을 만듭니다](../automation/shared-resources/schedules.md#creating-a-schedule) . 일정이 만들어지면 [runbook에 연결](../automation/shared-resources/schedules.md#linking-a-schedule-to-a-runbook)합니다. 

여러 랩을 포함 하는 여러 구독이 있는 대규모 상황에서 여러 랩을 위한 파일에 매개 변수 정보를 저장 하 고 개별 매개 변수 대신 스크립트에 파일을 전달 합니다. 스크립트를 수정 해야 하지만 핵심 실행은 동일 합니다. 이 샘플에서는 Azure Automation 사용 하 여 PowerShell 스크립트를 실행 하는 동안 빌드/릴리스 파이프라인에서 작업을 사용 하는 것과 같은 다른 옵션을 사용할 수 있습니다.

## <a name="next-steps"></a>다음 단계
Azure Automation: [Azure Automation 소개](../automation/automation-intro.md)에 대 한 자세한 내용은 다음 문서를 참조 하세요.
