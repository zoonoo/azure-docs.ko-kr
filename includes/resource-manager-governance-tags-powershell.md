---
title: 포함 파일
description: 포함 파일
services: azure-resource-manager
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: include
ms.date: 05/21/2018
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: 5dc4ce00685c74b2974cf1bfb5e8606eb3063e8d
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/01/2018
ms.locfileid: "34675326"
---
리소스 그룹에 두 개의 태그를 추가하려면 [Set-AzureRmResourceGroup](/powershell/module/azurerm.resources/set-azurermresourcegroup) 명령을 사용합니다.

```azurepowershell-interactive
Set-AzureRmResourceGroup -Name myResourceGroup -Tag @{ Dept="IT"; Environment="Test" }
```

세 번째 태그를 추가하려 한다고 가정해보겠습니다. 리소스 또는 리소스 그룹에 태그를 적용할 때마다 해당 리소스 또는 리소스 그룹의 기존 태그가 덮어써집니다. 기존 태그를 그대로 유지하면서 새 태그를 추가하려면 기존 태그를 검색하고, 새 태그를 추가한 후 태그 컬렉션을 다시 적용해야 합니다.

```azurepowershell-interactive
# Get existing tags and add a new tag
$tags = (Get-AzureRmResourceGroup -Name myResourceGroup).Tags
$tags.Add("Project", "Documentation")

# Reapply the updated set of tags 
Set-AzureRmResourceGroup -Tag $tags -Name myResourceGroup
```

리소스는 리소스 그룹에서 태그를 상속하지 않습니다. 현재, 리소스 그룹에 3개의 태그가 있지만 리소스에는 태그가 없습니다. 리소스의 중복되지 않는 기존 태그를 유지하지 않고 리소스 그룹의 모든 태그를 리소스에 적용하려면 다음 스크립트를 사용합니다.

```azurepowershell-interactive
# Get the resource group
$group = Get-AzureRmResourceGroup myResourceGroup

if ($group.Tags -ne $null) {
    # Get the resources in the resource group
    $resources = Get-AzureRmResource -ResourceGroupName $group.ResourceGroupName

    # Loop through each resource
    foreach ($r in $resources)
    {
        # Get the tags for this resource
        $resourcetags = (Get-AzureRmResource -ResourceId $r.ResourceId).Tags
        
        # If the resource has existing tags, add new ones
        if ($resourcetags)
        {
            foreach ($key in $group.Tags.Keys)
            {
                if (-not($resourcetags.ContainsKey($key)))
                {
                    $resourcetags.Add($key, $group.Tags[$key])
                }
            }

            # Reapply the updated tags to the resource 
            Set-AzureRmResource -Tag $resourcetags -ResourceId $r.ResourceId -Force
        }
        else
        {
            Set-AzureRmResource -Tag $group.Tags -ResourceId $r.ResourceId -Force
        }
    }
}
```

또는 기존 태그를 유지하지 않고 리소스 그룹의 태그를 리소스에 적용할 수도 있습니다.

```azurepowershell-interactive
# Get the resource group
$g = Get-AzureRmResourceGroup -Name myResourceGroup

# Find all the resources in the resource group, and for each resource apply the tags from the resource group
Get-AzureRmResource -ResourceGroupName $g.ResourceGroupName | ForEach-Object {Set-AzureRmResource -ResourceId $_.ResourceId -Tag $g.Tags -Force }
```

여러 값을 단일 태그에 결합하려면 JSON 문자열을 사용합니다.

```azurepowershell-interactive
Set-AzureRmResourceGroup -Name myResourceGroup -Tag @{ CostCenter="{`"Dept`":`"IT`",`"Environment`":`"Test`"}" }
```

모든 태그를 제거하려면 빈 해시 테이블을 전달합니다.

```azurepowershell-interactive
Set-AzureRmResourceGroup -Name myResourceGroup -Tag @{ }
```
