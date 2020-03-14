---
title: 리소스 그룹 관리-Azure PowerShell
description: Azure PowerShell를 사용 하 여 Azure Resource Manager를 통해 리소스 그룹을 관리할 수 있습니다. 리소스 그룹을 만들고, 나열 하 고, 삭제 하는 방법을 보여 줍니다.
author: mumian
ms.topic: conceptual
ms.date: 02/11/2019
ms.author: jgao
ms.openlocfilehash: 25e001927d5c06b4a7e5639faaa054ae18b12bb7
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/13/2020
ms.locfileid: "79248360"
---
# <a name="manage-azure-resource-manager-resource-groups-by-using-azure-powershell"></a>Azure PowerShell를 사용 하 여 Azure Resource Manager 리소스 그룹 관리

[Azure Resource Manager](overview.md) 에서 Azure PowerShell를 사용 하 여 Azure 리소스 그룹을 관리 하는 방법을 알아봅니다. Azure 리소스 관리에 대 한 자세한 내용은 [Azure PowerShell를 사용 하 여 azure 리소스 관리](manage-resources-powershell.md)를 참조 하세요.

리소스 그룹 관리에 대 한 기타 문서:

- [Azure Portal를 사용 하 여 Azure 리소스 그룹 관리](manage-resources-portal.md)
- [Azure CLI를 사용 하 여 Azure 리소스 그룹 관리](manage-resources-cli.md)

## <a name="what-is-a-resource-group"></a>리소스 그룹 이란?

리소스 그룹은 Azure 솔루션에 관련된 리소스를 보유하는 컨테이너입니다. 리소스 그룹에는 솔루션에 대한 모든 리소스 또는 그룹으로 관리하려는 해당 리소스만 포함될 수 있습니다. 사용자의 조직에 가장 적합한 내용에 따라 리소스 그룹에 리소스를 어떻게 할당할지 결정합니다. 일반적으로 쉽게 배포, 업데이트하고 그룹으로 삭제할 수 있도록 동일한 리소스 그룹에 대해 동일한 수명 주기를 공유하는 리소스를 추가합니다.

리소스 그룹은 리소스에 대한 메타데이터를 저장합니다. 따라서 리소스 그룹의 위치를 지정하면 메타데이터가 저장된 위치를 지정하게 됩니다. 규정 준수 때문에 특정 지역에 데이터가 저장되는지 확인해야 합니다.

리소스 그룹은 리소스에 대한 메타데이터를 저장합니다. 리소스 그룹의 위치를 지정하면 메타데이터가 저장되는 위치를 지정하게 됩니다.

## <a name="create-resource-groups"></a>리소스 그룹 만들기

다음 PowerShell 스크립트는 리소스 그룹을 만든 다음 리소스 그룹을 표시 합니다.

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"

New-AzResourceGroup -Name $resourceGroupName -Location $location

Get-AzResourceGroup -Name $resourceGroupName
```

## <a name="list-resource-groups"></a>리소스 그룹 나열

다음 PowerShell 스크립트는 구독에서 리소스 그룹을 나열 합니다.

```azurepowershell-interactive
Get-AzResourceGroup
```

하나의 리소스 그룹을 가져오려면 다음을 수행 합니다.

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"

Get-AzResourceGroup -Name $resourceGroupName
```

## <a name="delete-resource-groups"></a>리소스 그룹 삭제

다음 PowerShell 스크립트는 리소스 그룹을 삭제 합니다.

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"

Remove-AzResourceGroup -Name $resourceGroupName
```

리소스 삭제 Azure Resource Manager 주문 하는 방법에 대 한 자세한 내용은 [리소스 그룹 삭제 Azure Resource Manager](delete-resource-group.md)를 참조 하세요.

## <a name="deploy-resources-to-an-existing-resource-group"></a>기존 리소스 그룹에 리소스 배포

[기존 리소스 그룹에 리소스 배포를](manage-resources-powershell.md#deploy-resources-to-an-existing-resource-group)참조 하세요.

리소스 그룹 배포의 유효성을 검사 하려면 [AzResourceGroupDeployment](https://docs.microsoft.com/powershell/module/Az.Resources/Test-AzResourceGroupDeployment?view=azps-1.3.0)을 참조 하세요.

## <a name="deploy-a-resource-group-and-resources"></a>리소스 그룹 및 리소스 배포

리소스 관리자 템플릿을 사용 하 여 리소스 그룹을 만들고 리소스를 그룹에 배포할 수 있습니다. 자세한 내용은 [리소스 그룹 만들기 및 리소스 배포](../templates/deploy-to-subscription.md#resource-group-and-resources)를 참조하세요.

## <a name="redeploy-when-deployment-fails"></a>배포 실패 시 다시 배포

이 기능을 *오류 발생 시 롤백*이 라고도 합니다. 자세한 내용은 [배포 실패 시 재배포](../templates/rollback-on-error.md)를 참조 하세요.

## <a name="move-to-another-resource-group-or-subscription"></a>다른 리소스 그룹 또는 구독으로 이동

그룹의 리소스를 다른 리소스 그룹으로 이동할 수 있습니다. 자세한 내용을 보려면 [새 리소스 그룹 또는 구독으로 리소스 이동](move-resource-group-and-subscription.md)을 참조하세요.

## <a name="lock-resource-groups"></a>리소스 그룹 잠금

잠금은 조직의 다른 사용자가 실수로 Azure 구독, 리소스 그룹 또는 리소스와 같은 중요 한 리소스를 삭제 하거나 수정 하는 것을 방지 합니다. 

다음 스크립트는 리소스 그룹을 삭제할 수 없도록 리소스 그룹을 잠급니다.

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"

New-AzResourceLock -LockName LockGroup -LockLevel CanNotDelete -ResourceGroupName $resourceGroupName 
```

다음 스크립트는 리소스 그룹에 대 한 모든 잠금을 가져옵니다.

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"

Get-AzResourceLock -ResourceGroupName $resourceGroupName 
```

자세한 내용은 [Azure 리소스 관리자를 사용하여 리소스 잠그기](lock-resources.md)를 참조하세요.

## <a name="tag-resource-groups"></a>리소스 그룹 태그

리소스 그룹 및 리소스에 태그를 적용하여 논리적으로 자산을 구성할 수 있습니다. 자세한 내용은 [태그를 사용 하 여 Azure 리소스 구성](tag-resources.md#powershell)을 참조 하세요.

## <a name="export-resource-groups-to-templates"></a>템플릿으로 리소스 그룹 내보내기

리소스 그룹을 설정한 후에는 리소스 그룹의 리소스 관리자 템플릿을 볼 수 있습니다. 템플릿을 내보내면 다음과 같은 두 가지 이점이 있습니다.

- 템플릿에 전체 인프라가 포함 되어 있으므로 향후 솔루션 배포를 자동화 합니다.
- 솔루션을 나타내는 JSON (JavaScript Object Notation)을 살펴보면 템플릿 구문에 대해 알아봅니다.

리소스 그룹의 모든 리소스를 내보내려면 [AzResourceGroup](/powershell/module/az.resources/Export-AzResourceGroup) cmdlet을 사용 하 고 리소스 그룹 이름을 제공 합니다.

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"

Export-AzResourceGroup -ResourceGroupName $resourceGroupName
```

템플릿을 로컬 파일로 저장 합니다.

리소스 그룹의 모든 리소스를 내보내는 대신 내보낼 리소스를 선택할 수 있습니다.

한 리소스를 내보내려면 해당 리소스 ID를 전달 합니다.

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

템플릿을 내보낼 때 템플릿에서 매개 변수를 사용할지 여부를 지정할 수 있습니다. 기본적으로 리소스 이름에 대 한 매개 변수는 포함 되지만 기본값은 없습니다. 배포 하는 동안 해당 매개 변수 값을 전달 해야 합니다.

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

리소스에서 매개 변수는 이름에 사용 됩니다.

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

템플릿을 내보낼 때 `-IncludeParameterDefaultValue` 매개 변수를 사용 하는 경우 템플릿 매개 변수는 현재 값으로 설정 된 기본값을 포함 합니다. 기본 값을 사용 하거나 다른 값을 전달 하 여 기본값을 덮어쓸 수 있습니다.

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

템플릿을 내보낼 때 `-SkipResourceNameParameterization` 매개 변수를 사용 하는 경우 리소스 이름에 대 한 매개 변수가 템플릿에 포함 되지 않습니다. 대신 리소스 이름이 현재 값으로 리소스에 직접 설정 됩니다. 배포 하는 동안 이름을 사용자 지정할 수 없습니다.

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

템플릿 내보내기 기능은 Azure Data Factory 리소스 내보내기를 지원 하지 않습니다. Data Factory 리소스를 내보내는 방법에 대 한 자세한 내용은 [Azure Data Factory에서 데이터 팩터리 복사 또는 복제](https://aka.ms/exportTemplateViaAdf)를 참조 하세요.

클래식 배포 모델을 통해 만든 리소스를 내보내려면 [리소스 관리자 배포 모델로 마이그레이션해야](https://aka.ms/migrateclassicresourcetoarm)합니다.

자세한 내용은 [Azure Portal에서 템플릿으로 단일 및 다중 리소스 내보내기](../templates/export-template-portal.md)를 참조 하세요.

## <a name="manage-access-to-resource-groups"></a>리소스 그룹에 대 한 액세스 관리

[RBAC(역할 기반 액세스 제어)](../../role-based-access-control/overview.md)는 Azure에서 리소스에 대한 액세스를 관리하는 방법입니다. 자세한 내용은 [RBAC를 사용 하 여 액세스 관리 및 Azure PowerShell](../../role-based-access-control/role-assignments-powershell.md)을 참조 하세요.

## <a name="next-steps"></a>다음 단계

- Azure Resource Manager에 대 한 자세한 내용은 [Azure Resource Manager 개요](overview.md)를 참조 하세요.
- 리소스 관리자 템플릿 구문에 대 한 자세한 내용은 [Azure Resource Manager 템플릿의 구조 및 구문 이해](../templates/template-syntax.md)를 참조 하세요.
- 템플릿을 개발 하는 방법을 알아보려면 단계별 [자습서](/azure/azure-resource-manager/)를 참조 하세요.
- Azure Resource Manager 템플릿 스키마를 보려면 [템플릿 참조](/azure/templates/)를 참조 하세요.