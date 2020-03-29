---
title: Azure DevTest 랩에서 자동화 런북을 사용하여 컴퓨터 시작
description: Azure 자동화 실행책을 사용하여 Azure DevTest Labs의 랩에서 가상 컴퓨터를 시작하는 방법을 알아봅니다.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76166304"
---
# <a name="start-virtual-machines-in-a-lab-in-order-by-using-azure-automation-runbooks"></a>Azure 자동화 실행책을 사용하여 랩에서 가상 컴퓨터를 순서대로 시작
DevTest Labs의 [자동 시작](devtest-lab-set-lab-policy.md#set-autostart) 기능을 사용하면 지정된 시간에 VM을 자동으로 시작하도록 구성할 수 있습니다. 그러나 이 기능은 특정 순서로 시작하는 컴퓨터를 지원하지 않습니다. 이러한 유형의 자동화가 유용한 몇 가지 시나리오가 있습니다.  한 가지 시나리오는 점프박스가 다른 VM의 액세스 포인트로 사용되기 때문에 랩 내의 Jumpbox VM을 먼저 다른 VM보다 먼저 시작해야 하는 경우입니다.  이 문서에서는 스크립트를 실행하는 PowerShell 실행책을 사용하여 Azure 자동화 계정을 설정하는 방법을 보여 주며 있습니다. 스크립트는 랩의 VM에 대한 태그를 사용하여 스크립트를 변경하지 않고도 시작 순서를 제어할 수 있습니다.

## <a name="setup"></a>설치 프로그램
이 예제에서는 랩의 VM에 적절한 값(0,1,2 등)으로 **StartupOrder** 태그를 추가해야 합니다. 시작할 필요가 없는 컴퓨터를 -1로 지정합니다.

## <a name="create-an-azure-automation-account"></a>Azure Automation 계정 만들기
[이 문서의](../automation/automation-create-standalone-account.md)지침에 따라 Azure 자동화 계정을 만듭니다. 계정을 만들 때 **계정으로 실행** 옵션을 선택합니다. 자동화 계정이 만들어지면 **모듈** 페이지를 열고 메뉴 모음에서 **Azure 모듈 업데이트를** 선택합니다. 기본 모듈은 이전 버전의 여러 버전이며 업데이트없이 스크립트가 작동하지 않을 수 있습니다.

## <a name="add-a-runbook"></a>Runbook 추가
이제 자동화 계정에 Runbook을 추가하려면 왼쪽 메뉴에서 **Runbook을** 선택합니다. 메뉴에서 **Runbook 추가를** 선택하고 지침에 따라 [PowerShell Runbook을 만듭니다.](../automation/automation-first-runbook-textual-powershell.md)

## <a name="powershell-script"></a>PowerShell 스크립트
다음 스크립트는 구독 이름, 랩 이름을 매개 변수로 사용합니다. 스크립트의 흐름은 랩의 모든 VM을 가져옵니다. 스크립트는 VM을 순서대로 살펴보고 VM을 시작합니다. 특정 주문 번호에 여러 VM이 있는 경우 PowerShell 작업을 사용하여 비동기적으로 시작됩니다. 태그가 없는 VM의 경우 시작 값을 마지막(10)으로 설정하면 기본적으로 마지막으로 시작됩니다.  랩에서 VM을 자동으로 시작하지 않으려면 태그 값을 11로 설정하면 무시됩니다.

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
이 스크립트를 매일 실행하려면 자동화 계정에 [일정을 만듭니다.](../automation/shared-resources/schedules.md#creating-a-schedule) 일정이 만들어지면 [Runbook에 연결합니다.](../automation/shared-resources/schedules.md#linking-a-schedule-to-a-runbook) 

여러 랩이 있는 여러 구독이 있는 대규모 상황에서는 매개 변수 정보를 다른 랩의 파일에 저장하고 개별 매개 변수 대신 스크립트에 파일을 전달합니다. 스크립트를 수정해야 하지만 핵심 실행은 동일합니다. 이 샘플에서는 Azure 자동화를 사용하여 PowerShell 스크립트를 실행하는 동안 빌드/릴리스 파이프라인에서 작업을 사용하는 것과 같은 다른 옵션이 있습니다.

## <a name="next-steps"></a>다음 단계
Azure 자동화: [Azure 자동화 소개에](../automation/automation-intro.md)대해 자세히 알아보려면 다음 문서를 참조하세요.
