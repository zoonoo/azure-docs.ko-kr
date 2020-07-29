---
title: 리소스 관리-Azure PowerShell
description: Azure PowerShell 및 Azure Resource Manager를 사용 하 여 리소스를 관리 합니다. 리소스를 배포 하 고 삭제 하는 방법을 보여 줍니다.
author: mumian
ms.topic: conceptual
ms.date: 02/11/2019
ms.author: jgao
ms.openlocfilehash: ce3c0409ba077d81023188530b660eb210daf4d1
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87326803"
---
# <a name="manage-azure-resources-by-using-azure-powershell"></a>Azure PowerShell을 사용하여 Azure 리소스 관리

[Azure Resource Manager](overview.md) 에서 Azure PowerShell를 사용 하 여 Azure 리소스를 관리 하는 방법을 알아봅니다. 리소스 그룹 관리에 대 한 자세한 내용은 [Azure PowerShell를 사용 하 여 Azure 리소스 그룹 관리](manage-resource-groups-powershell.md)를 참조 하세요.

리소스 관리에 대 한 다른 문서:

- [Azure Portal를 사용 하 여 Azure 리소스 관리](manage-resources-portal.md)
- [Azure CLI를 사용 하 여 Azure 리소스 관리](manage-resources-cli.md)

## <a name="deploy-resources-to-an-existing-resource-group"></a>기존 리소스 그룹에 리소스 배포

Azure PowerShell를 사용 하 여 Azure 리소스를 직접 배포 하거나 리소스 관리자 템플릿을 배포 하 여 Azure 리소스를 만들 수 있습니다.

### <a name="deploy-a-resource"></a>리소스 배포

다음 스크립트는 저장소 계정을 만듭니다.

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$storageAccountName = Read-Host -Prompt "Enter the storage account name"

# Create the storage account.
$storageAccount = New-AzStorageAccount -ResourceGroupName $resourceGroupName `
  -Name $storageAccountName `
  -Location $location `
  -SkuName "Standard_LRS"

# Retrieve the context.
$ctx = $storageAccount.Context
```

### <a name="deploy-a-template"></a>템플릿 배포

다음 스크립트는 저장소 계정을 만들기 위한 빠른 시작 템플릿 배포를 만듭니다. 자세한 내용은 [빠른 시작: Visual Studio Code를 사용 하 여 Azure Resource Manager 템플릿 만들기](../templates/quickstart-create-templates-use-visual-studio-code.md?tabs=PowerShell)를 참조 하세요.

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json"
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri -Location $location
```

자세한 내용은 [Resource Manager 템플릿과 Azure PowerShell로 리소스 배포](../templates/deploy-powershell.md)를 참조하세요.

## <a name="deploy-a-resource-group-and-resources"></a>리소스 그룹 및 리소스 배포

리소스 그룹을 만들고 리소스를 그룹에 배포할 수 있습니다. 자세한 내용은 [리소스 그룹 만들기 및 리소스 배포](../templates/deploy-to-subscription.md#resource-groups)를 참조하세요.

## <a name="deploy-resources-to-multiple-subscriptions-or-resource-groups"></a>여러 구독 또는 리소스 그룹에 리소스 배포

일반적으로 단일 리소스 그룹에 템플릿의 모든 리소스를 배포합니다. 그러나 일단의 리소스를 함께 배포하고 다른 리소스 그룹 또는 구독에 배치하려는 시나리오가 있습니다. 자세한 내용은 [여러 구독 또는 리소스 그룹에 Azure 리소스 배포](../templates/cross-resource-group-deployment.md)를 참조 하세요.

## <a name="delete-resources"></a>리소스 삭제

다음 스크립트는 저장소 계정을 삭제 하는 방법을 보여 줍니다.

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$storageAccountName = Read-Host -Prompt "Enter the storage account name"

Remove-AzStorageAccount -ResourceGroupName $resourceGroupName -AccountName $storageAccountName
```

리소스 삭제 Azure Resource Manager 주문 하는 방법에 대 한 자세한 내용은 [리소스 그룹 삭제 Azure Resource Manager](delete-resource-group.md)를 참조 하세요.

## <a name="move-resources"></a>리소스 이동

다음 스크립트는 한 리소스 그룹에서 다른 리소스 그룹으로 저장소 계정을 제거 하는 방법을 보여 줍니다.

```azurepowershell-interactive
$srcResourceGroupName = Read-Host -Prompt "Enter the source Resource Group name"
$destResourceGroupName = Read-Host -Prompt "Enter the destination Resource Group name"
$storageAccountName = Read-Host -Prompt "Enter the storage account name"

$storageAccount = Get-AzResource -ResourceGroupName $srcResourceGroupName -ResourceName $storageAccountName
Move-AzResource -DestinationResourceGroupName $destResourceGroupName -ResourceId $storageAccount.ResourceId
```

자세한 내용을 보려면 [새 리소스 그룹 또는 구독으로 리소스 이동](move-resource-group-and-subscription.md)을 참조하세요.

## <a name="lock-resources"></a>리소스 잠금

잠금은 조직의 다른 사용자가 실수로 Azure 구독, 리소스 그룹 또는 리소스와 같은 중요 한 리소스를 삭제 하거나 수정 하는 것을 방지 합니다. 

다음 스크립트는 계정을 삭제할 수 없도록 저장소 계정을 잠급니다.

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$storageAccountName = Read-Host -Prompt "Enter the storage account name"

New-AzResourceLock -LockName LockStorage -LockLevel CanNotDelete -ResourceGroupName $resourceGroupName -ResourceName $storageAccountName -ResourceType Microsoft.Storage/storageAccounts 
```

다음 스크립트는 저장소 계정에 대 한 모든 잠금을 가져옵니다.

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$storageAccountName = Read-Host -Prompt "Enter the storage account name"

Get-AzResourceLock -ResourceGroupName $resourceGroupName -ResourceName $storageAccountName -ResourceType Microsoft.Storage/storageAccounts
```

다음 스크립트는 저장소 계정의 잠금을 삭제 합니다.

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$storageAccountName = Read-Host -Prompt "Enter the storage account name"

$lockId = (Get-AzResourceLock -ResourceGroupName $resourceGroupName -ResourceName $storageAccountName -ResourceType Microsoft.Storage/storageAccounts).LockId
Remove-AzResourceLock -LockId $lockId
```

자세한 내용은 [Azure Resource Manager를 사용 하 여 리소스 잠그기](lock-resources.md)를 참조 하세요.

## <a name="tag-resources"></a>리소스 태그 지정

태그를 지정 하면 리소스 그룹 및 리소스를 논리적으로 구성할 수 있습니다. 자세한 내용은 [태그를 사용 하 여 Azure 리소스 구성](tag-resources.md#powershell)을 참조 하세요.

## <a name="manage-access-to-resources"></a>리소스에 대 한 액세스 관리

[RBAC(역할 기반 액세스 제어)](../../role-based-access-control/overview.md)는 Azure에서 리소스에 대한 액세스를 관리하는 방법입니다. 자세한 내용은 [RBAC를 사용 하 여 액세스 관리 및 Azure PowerShell](../../role-based-access-control/role-assignments-powershell.md)을 참조 하세요.

## <a name="next-steps"></a>다음 단계

- Azure Resource Manager에 대 한 자세한 내용은 [Azure Resource Manager 개요](overview.md)를 참조 하세요.
- 리소스 관리자 템플릿 구문에 대 한 자세한 내용은 [Azure Resource Manager 템플릿의 구조 및 구문 이해](../templates/template-syntax.md)를 참조 하세요.
- 템플릿을 개발 하는 방법을 알아보려면 단계별 [자습서](../index.yml)를 참조 하세요.
- Azure Resource Manager 템플릿 스키마를 보려면 [템플릿 참조](/azure/templates/)를 참조 하세요.
