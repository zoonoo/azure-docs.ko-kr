---
title: Azure PowerShell을 사용 하 여 Azure Resource Manager 그룹 관리 | Microsoft Docs
description: Azure Resource Manager 그룹을 관리 하려면 Azure PowerShell을 사용 합니다.
services: azure-resource-manager
documentationcenter: ''
author: mumian
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 02/11/2019
ms.author: jgao
ms.openlocfilehash: 5197358e3bd8a3052fbf71cafc2f1e3acda46b26
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/10/2019
ms.locfileid: "67721154"
---
# <a name="manage-azure-resource-manager-resource-groups-by-using-azure-powershell"></a>Azure PowerShell을 사용 하 여 Azure Resource Manager 리소스 그룹 관리

사용 하 여 Azure PowerShell을 사용 하는 방법을 알아봅니다 [Azure Resource Manager](resource-group-overview.md) Azure 리소스 그룹을 관리할 수 있습니다. Azure 리소스 관리를 참조 하세요 [Azure PowerShell을 사용 하 여 Azure 관리 리소스](./manage-resources-powershell.md)합니다.

리소스 그룹을 관리 하는 방법에 대 한 다른 문서:

- [Azure portal을 사용 하 여 Azure 리소스 그룹 관리](./manage-resources-portal.md)
- [Azure CLI를 사용 하 여 Azure 리소스 그룹 관리](./manage-resources-cli.md)

## <a name="what-is-a-resource-group"></a>리소스 그룹 이란

리소스 그룹은 Azure 솔루션에 관련된 리소스를 보유하는 컨테이너입니다. 리소스 그룹에는 솔루션에 대한 모든 리소스 또는 그룹으로 관리하려는 해당 리소스만 포함될 수 있습니다. 사용자의 조직에 가장 적합한 내용에 따라 리소스 그룹에 리소스를 어떻게 할당할지 결정합니다. 일반적으로 쉽게 배포, 업데이트하고 그룹으로 삭제할 수 있도록 동일한 리소스 그룹에 대해 동일한 수명 주기를 공유하는 리소스를 추가합니다.

리소스 그룹은 리소스에 대한 메타데이터를 저장합니다. 따라서 리소스 그룹의 위치를 지정하면 메타데이터가 저장된 위치를 지정하게 됩니다. 규정 준수 때문에 특정 지역에 데이터가 저장되는지 확인해야 합니다.

리소스 그룹은 리소스에 대한 메타데이터를 저장합니다. 리소스 그룹의 위치를 지정 하면 메타 데이터가 저장 된 지정 하는 합니다.

## <a name="create-resource-groups"></a>리소스 그룹 만들기

다음 PowerShell 스크립트를 리소스 그룹을 만들고 리소스 그룹을 표시 합니다.

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"

New-AzResourceGroup -Name $resourceGroupName -Location $location

Get-AzResourceGroup -Name $resourceGroupName
```

## <a name="list-resource-groups"></a>리소스 그룹 나열

다음 PowerShell 스크립트는 구독에서 리소스 그룹을 나열합니다.

```azurepowershell-interactive
Get-AzResourceGroup
```

하나의 리소스 그룹을 가져오려면:

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"

Get-AzResourceGroup -Name $resourceGroupName
```

## <a name="delete-resource-groups"></a>리소스 그룹 삭제

다음 PowerShell 스크립트는 리소스 그룹을 삭제합니다.

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"

Remove-AzResourceGroup -Name $resourceGroupName
```

Azure Resource Manager 리소스의 삭제를 정렬 하는 방법에 대 한 자세한 내용은 참조 하세요. [Azure Resource Manager 리소스 그룹 삭제](./resource-group-delete.md)합니다.

## <a name="deploy-resources-to-an-existing-resource-group"></a>기존 리소스 그룹에 리소스 배포

참조 [기존 리소스 그룹에 리소스를 배포](./manage-resources-powershell.md#deploy-resources-to-an-existing-resource-group)합니다.

리소스 그룹 배포 유효성 검사를 참조 하세요 [테스트 AzResourceGroupDeployment](https://docs.microsoft.com/powershell/module/Az.Resources/Test-AzResourceGroupDeployment?view=azps-1.3.0)합니다.

## <a name="deploy-a-resource-group-and-resources"></a>리소스 그룹 및 리소스 배포

리소스 그룹을 만들고 Resource Manager 템플릿을 사용 하 여 리소스 그룹에 배포할 수 있습니다. 자세한 내용은 [리소스 그룹 만들기 및 리소스 배포](./deploy-to-subscription.md#create-resource-group-and-deploy-resources)를 참조하세요.

## <a name="redeploy-when-deployment-fails"></a>배포 실패 시 다시 배포

이 기능은 라고도 *오류 발생 시 롤백*합니다. 자세한 내용은 [배포 실패 시 다시 배포](./resource-group-template-deploy.md#redeploy-when-deployment-fails)합니다.

## <a name="move-to-another-resource-group-or-subscription"></a>다른 리소스 그룹 또는 구독으로 이동

다른 리소스 그룹으로 그룹의 리소스를 이동할 수 있습니다. 자세한 내용을 보려면 [새 리소스 그룹 또는 구독으로 리소스 이동](./resource-group-move-resources.md)을 참조하세요.

## <a name="lock-resource-groups"></a>잠금 리소스 그룹

잠그면 실수로 삭제 하거나 Azure 구독, 리소스 그룹 또는 리소스와 같은 중요 한 리소스를 수정 하면 조직의 다른 사용자가 수 없습니다. 

다음 스크립트는 리소스 그룹을 삭제할 수 없게 리소스 그룹을 잠급니다.

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"

New-AzResourceLock -LockName LockGroup -LockLevel CanNotDelete -ResourceGroupName $resourceGroupName 
```

다음 스크립트는 리소스 그룹에 대 한 모든 잠금을 가져옵니다.

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"

Get-AzResourceLock -ResourceGroupName $resourceGroupName 
```

자세한 내용은 [Azure 리소스 관리자를 사용하여 리소스 잠그기](resource-group-lock-resources.md)를 참조하세요.

## <a name="tag-resource-groups"></a>리소스 그룹 태그

리소스 그룹 및 리소스에 태그를 적용하여 논리적으로 자산을 구성할 수 있습니다. 정보를 참조 하세요 [태그를 사용 하 여 Azure 리소스 구성에](./resource-group-using-tags.md#powershell)입니다.

## <a name="export-resource-groups-to-templates"></a>리소스 그룹 템플릿 내보내기

리소스 그룹을 설정한 후 리소스 그룹에 대 한 Resource Manager 템플릿을 볼 수 있습니다. 템플릿을 내보내면 다음과 같은 두 가지 이점이 있습니다.

- 템플릿은 전체 인프라를 포함 하기 때문에 향후 솔루션 배포를 자동화 합니다.
- 에 개체 JSON (JavaScript Notation) 솔루션을 나타내는 확인 하 여 템플릿 구문에 알아봅니다.

리소스 그룹의 모든 리소스를 내보내려면 합니다 [내보내기 AzResourceGroup](/powershell/module/az.resources/Export-AzResourceGroup) cmdlet 리소스 그룹 이름을 제공 합니다.

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"

Export-AzResourceGroup -ResourceGroupName $resourceGroupName
```

템플릿이 로컬 파일로 저장합니다.

리소스 그룹의 모든 리소스를 내보내는 대신 내보내려면 리소스를 선택할 수 있습니다.

하나의 리소스를 내보내려면 해당 리소스 ID를 전달 합니다.

```azurepowershell-interactive
$resource = Get-AzResource `
  -ResourceGroupName <resource-group-name> `
  -ResourceName <resource-name> `
  -ResourceType <resource-type>
Export-AzResourceGroup `
  -ResourceGroupName <resource-group-name> `
  -Resource $resource.ResourceId
```

둘 이상의 리소스를 내보내려면 배열에 리소스 Id를 전달 합니다.

```azurepowershell-interactive
Export-AzResourceGroup `
  -ResourceGroupName <resource-group-name> `
  -Resource @($resource1.ResourceId, $resource2.ResourceId)
```

템플릿을 내보내면 템플릿 매개 변수 사용 되는지 여부를 지정할 수 있습니다. 기본적으로 리소스 이름에 대 한 매개 변수는 포함 되지만 기본 값이 없어야 합니다. 배포 하는 동안 해당 매개 변수 값을 전달 해야 합니다.

```json
"parameters": {
  "serverfarms_demoHostPlan_name": {
    "defaultValue": null,
    "type": "String"
  },
  "sites_webSite3bwt23ktvdo36_name": {
    "defaultValue": null,
    "type": "String"
  }
}
```

리소스에서 매개 변수 이름에 사용 됩니다.

```json
"resources": [
  {
    "type": "Microsoft.Web/serverfarms",
    "apiVersion": "2016-09-01",
    "name": "[parameters('serverfarms_demoHostPlan_name')]",
    ...
  }
]
```

사용 하는 경우는 `-IncludeParameterDefaultValue` 템플릿을 내보내는 경우 매개 변수를 템플릿 매개 변수는 현재 값으로 설정 된 기본 값을 포함 합니다. 기본값을 사용 하거나 다른 값을 전달 하 여 기본값을 덮어씁니다.

```json
"parameters": {
  "serverfarms_demoHostPlan_name": {
    "defaultValue": "demoHostPlan",
    "type": "String"
  },
  "sites_webSite3bwt23ktvdo36_name": {
    "defaultValue": "webSite3bwt23ktvdo36",
    "type": "String"
  }
}
```

사용 하는 경우는 `-SkipResourceNameParameterization` 템플릿에 포함 되지 리소스 이름에 대 한 템플릿 매개 변수를 내보낼 때 매개 변수입니다. 대신 리소스 이름은 해당 현재 값으로 리소스에 직접 설정 됩니다. 배포 하는 동안 이름을 사용자 지정할 수 없습니다.

```json
"resources": [
  {
    "type": "Microsoft.Web/serverfarms",
    "apiVersion": "2016-09-01",
    "name": "demoHostPlan",
    ...
  }
]
```

자세한 내용은 [Azure portal에서 템플릿에 단일 및 다중 리소스 내보내기](./export-template-portal.md)합니다.

## <a name="manage-access-to-resource-groups"></a>리소스 그룹에 대 한 액세스 관리

[RBAC(역할 기반 액세스 제어)](../role-based-access-control/overview.md)는 Azure에서 리소스에 대한 액세스를 관리하는 방법입니다. 자세한 내용은 [RBAC 및 Azure PowerShell을 사용 하 여 액세스를 관리](../role-based-access-control/role-assignments-powershell.md)합니다.

## <a name="next-steps"></a>다음 단계

- Azure Resource Manager에 알아보려면 [Azure Resource Manager 개요](./resource-group-overview.md)합니다.
- Resource Manager 템플릿 구문에 알아보려면 [구조 및 Azure Resource Manager 템플릿의 구문 이해](./resource-group-authoring-templates.md)합니다.
- 템플릿을 개발 하는 방법에 알아보려면 참조를 [단계별 자습서](/azure/azure-resource-manager/)합니다.
- Azure Resource Manager 템플릿 스키마를 보려면 [템플릿 참조](/azure/templates/)합니다.