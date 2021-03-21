---
title: Azure 방화벽 프리미엄 미리 보기로 마이그레이션
description: Azure 방화벽 표준에서 Azure Firewall Premium Preview로 마이그레이션하는 방법에 대해 알아봅니다.
author: vhorne
ms.service: firewall
services: firewall
ms.topic: how-to
ms.date: 02/16/2021
ms.author: victorh
ms.openlocfilehash: ffdcad33568af9955dbdf5aafb1b6ffe4a51681d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "100549712"
---
# <a name="migrate-to-azure-firewall-premium-preview"></a>Azure 방화벽 프리미엄 미리 보기로 마이그레이션

새 프리미엄 기능을 활용 하기 위해 azure 방화벽 표준을 Azure 방화벽 프리미엄 미리 보기로 마이그레이션할 수 있습니다. Azure 방화벽 프리미엄 미리 보기 기능에 대 한 자세한 내용은 [Azure 방화벽 프리미엄 미리 보기 기능](premium-features.md)을 참조 하세요.

다음 두 예제에서는 방법을 보여 줍니다.
- Azure PowerShell를 사용 하 여 기존 표준 정책 마이그레이션
- 프리미엄 정책으로 기존 표준 방화벽 (클래식 규칙 포함)을 Azure 방화벽 프리미엄으로 마이그레이션합니다.

## <a name="migrate-an-existing-policy-using-azure-powershell"></a>Azure PowerShell를 사용 하 여 기존 정책 마이그레이션

`Transform-Policy.ps1` 는 기존 표준 정책에서 새 프리미엄 정책을 만드는 Azure PowerShell 스크립트입니다.

표준 방화벽 정책 ID를 지정 하는 경우 스크립트는이를 프리미엄 Azure 방화벽 정책으로 변환 합니다. 이 스크립트는 먼저 Azure 계정에 연결 하 고, 정책을 가져오고, 다양 한 매개 변수를 변환/추가 하 고, 새 프리미엄 정책을 업로드 합니다. 새 프리미엄 정책의 이름은 `<previous_policy_name>_premium` 입니다.

사용법 예제:

`Transform-Policy -PolicyId /subscriptions/XXXXX-XXXXXX-XXXXX/resourceGroups/some-resource-group/providers/Microsoft.Network/firewallPolicies/policy-name`

> [!IMPORTANT]
> 이 스크립트는 위협 인텔리전스 설정을 마이그레이션하지 않습니다. 계속 하기 전에 해당 설정을 확인 하 고 수동으로 마이그레이션해야 합니다.

```azurepowershell
<#
    .SYNOPSIS
        Given an Azure firewall policy id the script will transform it to a Premium Azure firewall policy. 
        The script will first pull the policy, transform/add various parameters and then upload a new premium policy. 
        The created policy will be named <previous_policy_name>_premium if no new name provided else new policy will be named as the parameter passed.  
    .Example
        Transform-Policy -PolicyId /subscriptions/XXXXX-XXXXXX-XXXXX/resourceGroups/some-resource-group/providers/Microsoft.Network/firewallPolicies/policy-name -NewPolicyName <optional param for the new policy name>
#>

param (
    #Resource id of the azure firewall policy. 
    [Parameter(Mandatory=$true)]
    [string]
    $PolicyId,

    # #new filewallpolicy name, if not specified will be the previous name with the '_premium' suffix
    [Parameter(Mandatory=$false)]
    [string]
    $NewPolicyName = ""
)
$ErrorActionPreference = "Stop"
$script:PolicyId = $PolicyId
$script:PolicyName = $NewPolicyName

function ValidatePolicy {
    [CmdletBinding()]
    param (
        [Parameter(Mandatory=$true)]
        [Object]
        $Policy
    )

    Write-Host "Validating resource is as expected"

    if ($null -eq $Policy) {
        Write-Error "Recived null policy"
        exit(1)
    }
    if ($Policy.GetType().Name -ne "PSAzureFirewallPolicy") {
        Write-Host "Resource must be of type Microsoft.Network/firewallPolicies" -ForegroundColor Red
        exit(1)
    }

    if ($Policy.Sku.Tier -eq "Premium") {
        Write-Host "Policy is already premium"
        exit(1)
    }
}

function GetPolicyNewName {
    [CmdletBinding()]
    param (
        [Parameter(Mandatory=$true)]
        [Microsoft.Azure.Commands.Network.Models.PSAzureFirewallPolicy]
        $Policy
    )

    if (-not [string]::IsNullOrEmpty($script:PolicyName)) {
        return $script:PolicyName
    }

    return $Policy.Name + "_premium"
}

function TransformPolicyToPremium {
    [CmdletBinding()]
    param (
        [Parameter(Mandatory=$true)]
        [Microsoft.Azure.Commands.Network.Models.PSAzureFirewallPolicy]
        $Policy
    )    
    $NewPolicyParameters = @{
                        Name = (GetPolicyNewName -Policy $Policy) 
                        ResourceGroupName = $Policy.ResourceGroupName 
                        Location = $Policy.Location 
                        ThreatIntelMode = $Policy.ThreatIntelMode 
                        BasePolicy = $Policy.BasePolicy 
                        DnsSetting = $Policy.DnsSettings 
                        Tag = $Policy.Tag 
                        SkuTier = "Premium" 
    }

    Write-Host "Creating new policy"
    $premiumPolicy = New-AzFirewallPolicy @NewPolicyParameters

    Write-Host "Populating rules in new policy"
    foreach ($ruleCollectionGroup in $Policy.RuleCollectionGroups) {
        $ruleResource = Get-AzResource -ResourceId $ruleCollectionGroup.Id
        $ruleToTransfom = Get-AzFirewallPolicyRuleCollectionGroup -AzureFirewallPolicy $Policy -Name $ruleResource.Name
        $ruleCollectionGroup = @{
            FirewallPolicyObject = $premiumPolicy
            Priority = $ruleToTransfom.Properties.Priority
            Name = $ruleToTransfom.Name
        }

        if ($ruleToTransfom.Properties.RuleCollection.Count) {
            $ruleCollectionGroup["RuleCollection"] = $ruleToTransfom.Properties.RuleCollection
        }

        Set-AzFirewallPolicyRuleCollectionGroup @ruleCollectionGroup
    }
}

function ValidateAzNetworkModuleExists {
    Write-Host "Validating needed module exists"
    $networkModule = Get-InstalledModule -Name "Az.Network" -ErrorAction SilentlyContinue
    if (($null -eq $networkModule) -or ($networkModule.Version -lt 4.5)){
        Write-Host "Please install Az.Network module version 4.5.0 or higher, see instructions: https://github.com/Azure/azure-powershell#installation"
        exit(1)
    }
    Import-Module Az.Network -MinimumVersion 4.5.0
}

ValidateAzNetworkModuleExists
$policy = Get-AzFirewallPolicy -ResourceId $script:PolicyId
ValidatePolicy -Policy $policy
TransformPolicyToPremium -Policy $policy
```

## <a name="migrate-an-existing-standard-firewall-using-the-azure-portal"></a>Azure Portal를 사용 하 여 기존 표준 방화벽 마이그레이션

이 예제에서는 Azure Portal를 사용 하 여 프리미엄 정책을 통해 표준 방화벽 (클래식 규칙)을 Azure 방화벽 프리미엄으로 마이그레이션하는 방법을 보여 줍니다.

1. Azure Portal에서 표준 방화벽을 선택 합니다. **개요** 페이지에서 **방화벽 정책으로 마이그레이션** 을 선택 합니다.

   :::image type="content" source="media/premium-migrate/firewall-overview-migrate.png" alt-text="방화벽 정책으로 마이그레이션":::

1. **방화벽 정책으로 마이그레이션** 페이지에서 **검토 + 만들기** 를 선택 합니다.
1. **만들기** 를 선택합니다.

   배포를 완료하는 데 몇 분이 걸립니다.
1. `Transform-Policy.ps1` [Azure PowerShell 스크립트](#migrate-an-existing-policy-using-azure-powershell) 를 사용 하 여이 새 표준 정책을 프리미엄 정책으로 변환 합니다.
1. 포털에서 표준 방화벽 리소스를 선택 합니다. 
1. **Automation** 에서 **템플릿 내보내기** 를 선택 합니다. 이 브라우저 탭을 열어 둡니다. 나중에 다시 돌아올 것입니다.
   > [!TIP]
   > 템플릿이 손실 되지 않도록 하려면 브라우저 탭이 닫히거나 새로 고쳐질 때를 다운로드 하 여 저장 합니다.
1. 새 브라우저 탭을 열고 Azure Portal로 이동 하 여 방화벽을 포함 하는 리소스 그룹을 엽니다.
1. 기존 표준 방화벽 인스턴스를 삭제 합니다.

   이 작업은 완료하는 데 몇 분이 소요됩니다.

1. 내보낸 템플릿을 사용 하 여 브라우저 탭으로 돌아갑니다.
1. **배포** 를 선택 하 고 **사용자 지정 배포** 페이지에서 **템플릿 편집** 을 선택 합니다.
1. 템플릿 텍스트를 편집 합니다.
   
   1. 리소스 아래의에서 `Microsoft.Network/azureFirewalls` `Properties` `sku` `tier` "Standard"를 "Premium"으로 변경 합니다.
   1. 템플릿 아래에서의를 `Parameters` 변경 `defaultValue` 합니다 `firewallPolicies_FirewallPolicy_,<your policy name>_externalid` .
      
       `"/subscriptions/<subscription id>/resourceGroups/<your resource group>/providers/Microsoft.Network/firewallPolicies/FirewallPolicy_<your policy name>"`

      다음과 같이 변경합니다.

      `"/subscriptions/<subscription id>/resourceGroups/<your resource group>/providers/Microsoft.Network/firewallPolicies/FirewallPolicy_<your policy name>_premium"`
1. **저장** 을 선택합니다.
1. **검토 + 만들기** 를 선택합니다.
1. **만들기** 를 선택합니다.

배포가 완료 되 면 이제 모든 새 Azure 방화벽 프리미엄 미리 보기 기능을 구성할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [Azure 방화벽 프리미엄 기능에 대 한 자세한 정보](premium-features.md)