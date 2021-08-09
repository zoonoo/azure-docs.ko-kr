---
title: 리소스 그룹 관리 - Azure CLI
description: Azure CLI를 사용하여 Azure Resource Manager를 통해 리소스 그룹을 관리합니다. 리소스 그룹을 만들고, 나열하고, 삭제하는 방법을 보여 줍니다.
author: mumian
ms.topic: conceptual
ms.date: 01/05/2021
ms.author: jgao
ms.custom: devx-track-azurecli
ms.openlocfilehash: fd6848d7ea43b08d6c19263e230b4284d3d81160
ms.sourcegitcommit: c072eefdba1fc1f582005cdd549218863d1e149e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/10/2021
ms.locfileid: "111951344"
---
# <a name="manage-azure-resource-manager-resource-groups-by-using-azure-cli"></a>Azure CLI를 사용하여 Azure Resource Manager 리소스 그룹 관리

[Azure Resource Manager](overview.md)와 함께 Azure CLI를 사용하여 Azure 리소스 그룹을 관리하는 방법을 알아봅니다. Azure 리소스를 관리하려면 [ Azure CLI를 사용하여 Azure 리소스 관리](manage-resources-cli.md)를 참조하세요.

리소스 관리에 관한 다른 문서:

- [Azure Portal를 사용하여 Azure 리소스 그룹 관리](manage-resources-portal.md)
- [Azure PowerShell을 사용하여 Azure 리소스 그룹 관리](manage-resources-powershell.md)

## <a name="what-is-a-resource-group"></a>리소스 그룹이란?

리소스 그룹은 Azure 솔루션에 관련된 리소스를 보유하는 컨테이너입니다. 리소스 그룹에는 솔루션에 대한 모든 리소스 또는 그룹으로 관리하려는 해당 리소스만 포함될 수 있습니다. 사용자의 조직에 가장 적합한 내용에 따라 리소스 그룹에 리소스를 어떻게 할당할지 결정합니다. 일반적으로 쉽게 배포, 업데이트하고 그룹으로 삭제할 수 있도록 동일한 리소스 그룹에 대해 동일한 수명 주기를 공유하는 리소스를 추가합니다.

리소스 그룹은 리소스에 대한 메타데이터를 저장합니다. 따라서 리소스 그룹의 위치를 지정하면 메타데이터가 저장된 위치를 지정하게 됩니다. 규정 준수 때문에 특정 지역에 데이터가 저장되는지 확인해야 합니다.

리소스 그룹은 리소스에 대한 메타데이터를 저장합니다. 리소스 그룹의 위치를 지정하면 메타데이터가 저장되는 위치를 지정하게 됩니다.

## <a name="create-resource-groups"></a>리소스 그룹 만들기

다음 CLI 명령은 리소스 그룹을 만듭니다.

```azurecli-interactive
az group create --name demoResourceGroup --location westus
```

## <a name="list-resource-groups"></a>리소스 그룹 나열

다음 CLI 스크립트는 구독 아래의 리소스 그룹을 나열합니다.

```azurecli-interactive
az group list
```

하나의 리소스 그룹을 가져오려면

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group show --name $resourceGroupName
```

## <a name="delete-resource-groups"></a>리소스 그룹 삭제

다음 CLI 스크립트는 리소스 그룹을 삭제합니다.

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName
```

Azure Resource Manager가 리소스 삭제를 지시하는 방법에 대한 자세한 내용은 [Azure Resource Manager 리소스 그룹 삭제](delete-resource-group.md)를 참조하세요.

## <a name="deploy-resources-to-an-existing-resource-group"></a>기존 리소스 그룹에 리소스 배포

[기존 리소스 그룹에 리소스 배포](manage-resources-cli.md#deploy-resources-to-an-existing-resource-group)를 참조하세요.

## <a name="deploy-a-resource-group-and-resources"></a>리소스 그룹 및 리소스 배포

Resource Manager 템플릿을 사용하여 리소스 그룹을 만들고 리소스를 그룹에 배포할 수 있습니다. 자세한 내용은 [리소스 그룹 만들기 및 리소스 배포](../templates/deploy-to-subscription.md#resource-groups)를 참조하세요.

## <a name="redeploy-when-deployment-fails"></a>배포 실패 시 다시 배포

이 기능을 *오류 발생 시 롤백* 이라고도 합니다. 자세한 내용은 [배포 실패 시 다시 배포](../templates/rollback-on-error.md)를 참조하세요.

## <a name="move-to-another-resource-group-or-subscription"></a>다른 리소스 그룹 또는 구독으로 이동

그룹의 리소스를 다른 리소스 그룹으로 이동할 수 있습니다. 자세한 내용은 [리소스 이동](manage-resources-cli.md#move-resources)을 참조하세요.

## <a name="lock-resource-groups"></a>리소스 그룹 잠금

잠금은 조직의 다른 사용자가 실수로 Azure 구독, 리소스 그룹 또는 리소스와 같은 중요한 리소스를 삭제하거나 수정하는 것을 방지합니다.

다음 스크립트는 리소스 그룹을 삭제할 수 없도록 리소스 그룹을 잠급니다.

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az lock create --name LockGroup --lock-type CanNotDelete --resource-group $resourceGroupName
```

다음 스크립트는 리소스 그룹에 대한 모든 잠금을 가져옵니다.

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az lock list --resource-group $resourceGroupName
```

다음 스크립트는 잠금을 삭제합니다.

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
echo "Enter the lock name:" &&
read lockName &&
az lock delete --name $lockName --resource-group $resourceGroupName
```

자세한 내용은 [Azure 리소스 관리자를 사용하여 리소스 잠그기](lock-resources.md)를 참조하세요.

## <a name="tag-resource-groups"></a>리소스 그룹 태그 지정

리소스 그룹 및 리소스에 태그를 적용하여 논리적으로 자산을 구성할 수 있습니다. 자세한 내용은 [태그를 사용하여 Azure 리소스 구성](tag-resources.md#azure-cli)을 참조하세요.

## <a name="export-resource-groups-to-templates"></a>템플릿으로 리소스 그룹 내보내기

리소스 그룹을 성공적으로 설정한 후에는 리소스 그룹에 대한 Resource Manager 템플릿을 살펴볼 수 있습니다. 템플릿을 내보내면 다음과 같은 두 가지 이점이 있습니다.

- 템플릿에 전체 인프라가 모두 포함되어 있기 때문에 향후 솔루션 배포를 자동화할 수 있습니다.
- 솔루션을 나타내는 JSON(JavaScript Object Notation)을 살펴보면서 템플릿 구문에 익숙해집니다.

리소스 그룹의 모든 리소스를 내보내려면 [az group export](/cli/azure/group#az_group_export)를 사용하고 리소스 그룹 이름을 제공합니다.

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group export --name $resourceGroupName
```

스크립트는 콘솔에 템플릿을 표시합니다. JSON을 복사하고 파일로 저장합니다.

리소스 그룹의 모든 리소스를 내보내는 대신, 내보낼 리소스를 선택할 수 있습니다.

한 리소스를 내보내려면 해당 리소스 ID를 제공합니다.

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
echo "Enter the storage account name:" &&
read storageAccountName &&
storageAccount=$(az resource show --resource-group $resourceGroupName --name $storageAccountName --resource-type Microsoft.Storage/storageAccounts --query id --output tsv) &&
az group export --resource-group $resourceGroupName --resource-ids $storageAccount
```

둘 이상의 리소스를 내보내려면 공백으로 구분된 리소스 ID를 전달합니다. 모든 리소스를 내보내려면 이 인수를 지정하거나 "*"를 제공하지 마세요.

```azurecli-interactive
az group export --resource-group <resource-group-name> --resource-ids $storageAccount1 $storageAccount2
```

템플릿을 내보낼 때 템플릿에서 매개 변수를 사용할지 여부를 지정할 수 있습니다. 기본적으로 리소스 이름에 대한 매개 변수는 포함되지만 기본값은 없습니다. 배포하는 동안 해당 매개 변수 값을 제공해야 합니다.

```json
"parameters": {
  "serverfarms_demoHostPlan_name": {
    "type": "String"
  },
  "sites_webSite3bwt23ktvdo36_name": {
    "type": "String"
  }
}
```

리소스에서 해당 매개 변수가 이름으로 사용됩니다.

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

템플릿을 내보낼 때 `--include-parameter-default-value` 매개 변수를 사용하는 경우 템플릿 매개 변수는 현재 값으로 설정된 기본값을 포함합니다. 기본값을 사용하거나 다른 값을 제공하여 기본값을 덮어쓸 수 있습니다.

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

템플릿을 내보낼 때 `--skip-resource-name-params` 매개 변수를 사용하는 경우 리소스 이름에 대한 매개 변수가 템플릿에 포함되지 않습니다. 대신, 리소스 이름이 현재 값으로 리소스에 직접 설정됩니다. 배포하는 동안 이름을 사용자 지정할 수 없습니다.

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

템플릿 내보내기 기능은 Azure Data Factory 리소스 내보내기를 지원하지 않습니다. Data Factory 리소스를 내보내는 방법에 관한 자세한 내용은 [Azure Data Factory에서 데이터 팩터리 복사 또는 복제](../../data-factory/copy-clone-data-factory.md)를 참조하세요.

클래식 배포 모델을 통해 만든 리소스를 내보내려면 [Resource Manager 배포 모델로 마이그레이션](../../virtual-machines/migration-classic-resource-manager-overview.md)해야 합니다.

[Azure Portal에서 템플릿으로 단일 리소스 및 다중 리소스 내보내기](../templates/export-template-portal.md)를 참조하세요.

## <a name="manage-access-to-resource-groups"></a>리소스 그룹에 대한 액세스 관리

[Azure RBAC(Azure 역할 기반 액세스 제어)](../../role-based-access-control/overview.md)는 Azure에서 리소스에 대한 액세스를 관리하는 한 방법입니다. 자세한 내용은 [Azure CLI를 사용하여 Azure 역할 할당 추가 또는 제거](../../role-based-access-control/role-assignments-cli.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

- Azure Resource Manager에 대한 자세한 내용은 [Azure Resource Manager 개요](overview.md)를 참조하세요.
- Resource Manager 템플릿 구문에 대해 알아보려면 [Azure Resource Manager 템플릿의 구조 및 구문 이해](../templates/syntax.md)를 참조하세요.
- 템플릿을 개발하는 방법을 알아보려면 [단계별 자습서](../index.yml)를 참조하세요.
- Azure Resource Manager 템플릿 스키마를 보려면 [템플릿 참조](/azure/templates/)를 참조하세요.