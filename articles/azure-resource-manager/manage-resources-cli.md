---
title: Azure CLI를 사용 하 여 Azure 리소스 관리 | Microsoft Docs
description: Azure CLI 및 Azure 리소스 관리를 사용 하 여 리소스를 관리 합니다.
services: azure-resource-manager
documentationcenter: ''
author: mumian
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 02/11/2019
ms.author: jgao
ms.openlocfilehash: 78dd51c023bc46ed09219acc3df7e4ee7006ac2b
ms.sourcegitcommit: be344deef6b37661e2c496f75a6cf14f805d7381
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/07/2019
ms.locfileid: "72001557"
---
# <a name="manage-azure-resources-by-using-azure-cli"></a>Azure CLI를 사용 하 여 Azure 리소스 관리

[Azure Resource Manager](resource-group-overview.md) 에서 Azure CLI를 사용 하 여 Azure 리소스를 관리 하는 방법을 알아봅니다. 리소스 그룹 관리에 대 한 자세한 내용은 [Azure CLI를 사용 하 여 Azure 리소스 그룹 관리](./manage-resource-groups-cli.md)를 참조 하세요.

리소스 관리에 대 한 다른 문서:

- [Azure Portal를 사용 하 여 Azure 리소스 관리](./manage-resources-portal.md)
- [Azure PowerShell를 사용 하 여 Azure 리소스 관리](./manage-resources-powershell.md)

## <a name="deploy-resources-to-an-existing-resource-group"></a>기존 리소스 그룹에 리소스 배포

Azure PowerShell를 사용 하 여 Azure 리소스를 직접 배포 하거나 리소스 관리자 템플릿을 배포 하 여 Azure 리소스를 만들 수 있습니다.

### <a name="deploy-a-resource"></a>리소스 배포

다음 스크립트는 저장소 계정을 만듭니다.

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
echo "Enter the location (i.e. centralus):" &&
read location &&
echo "Enter the storage account name:" &&
read storageAccountName &&
az storage account create --resource-group $resourceGroupName --name $storageAccountName --location $location --sku Standard_LRS --kind StorageV2 &&
az storage account show --resource-group $resourceGroupName --name $storageAccountName 
```

### <a name="deploy-a-template"></a>템플릿 배포

다음 스크립트는 저장소 계정을 만들기 위한 빠른 시작 템플릿 배포를 만듭니다. 자세한 내용은 [빠른 시작: Visual Studio Code를 사용하여 Azure Resource Manager 템플릿 만들기](./resource-manager-quickstart-create-templates-use-visual-studio-code.md?tabs=PowerShell)를 참조하세요.

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
echo "Enter the location (i.e. centralus):" &&
read location &&
az group deployment create --resource-group $resourceGroupName --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json"
```

자세한 내용은 [Resource Manager 템플릿과 Azure CLI로 리소스 배포](./resource-group-template-deploy-cli.md)를 참조하세요.

## <a name="deploy-a-resource-group-and-resources"></a>리소스 그룹 및 리소스 배포

리소스 그룹을 만들고 리소스를 그룹에 배포할 수 있습니다. 자세한 내용은 [리소스 그룹 만들기 및 리소스 배포](./deploy-to-subscription.md#resource-group-and-resources)를 참조하세요.

## <a name="deploy-resources-to-multiple-subscriptions-or-resource-groups"></a>여러 구독 또는 리소스 그룹에 리소스 배포

일반적으로 단일 리소스 그룹에 템플릿의 모든 리소스를 배포합니다. 그러나 일단의 리소스를 함께 배포하고 다른 리소스 그룹 또는 구독에 배치하려는 시나리오가 있습니다. 자세한 내용은 [여러 구독 또는 리소스 그룹에 Azure 리소스 배포](./resource-manager-cross-resource-group-deployment.md)를 참조 하세요.

## <a name="delete-resources"></a>리소스 삭제

다음 스크립트는 저장소 계정을 삭제 하는 방법을 보여 줍니다.

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
echo "Enter the storage account name:" &&
read storageAccountName &&
az storage account delete --resource-group $resourceGroupName --name $storageAccountName 
```

리소스 삭제 Azure Resource Manager 주문 하는 방법에 대 한 자세한 내용은 [리소스 그룹 삭제 Azure Resource Manager](./resource-group-delete.md)를 참조 하세요.

## <a name="move-resources"></a>리소스 이동

다음 스크립트는 한 리소스 그룹에서 다른 리소스 그룹으로 저장소 계정을 제거 하는 방법을 보여 줍니다.

```azurecli-interactive
echo "Enter the source Resource Group name:" &&
read srcResourceGroupName &&
echo "Enter the destination Resource Group name:" &&
read destResourceGroupName &&
echo "Enter the storage account name:" &&
read storageAccountName &&
storageAccount=$(az resource show --resource-group $srcResourceGroupName --name $storageAccountName --resource-type Microsoft.Storage/storageAccounts --query id --output tsv) &&
az resource move --destination-group $destResourceGroupName --ids $storageAccount
```

자세한 내용을 보려면 [새 리소스 그룹 또는 구독으로 리소스 이동](resource-group-move-resources.md)을 참조하세요.

## <a name="lock-resources"></a>리소스 잠금

잠금은 조직의 다른 사용자가 실수로 Azure 구독, 리소스 그룹 또는 리소스와 같은 중요 한 리소스를 삭제 하거나 수정 하는 것을 방지 합니다. 

다음 스크립트는 계정을 삭제할 수 없도록 저장소 계정을 잠급니다.

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
echo "Enter the storage account name:" &&
read storageAccountName &&
az lock create --name LockSite --lock-type CanNotDelete --resource-group $resourceGroupName --resource-name $storageAccountName --resource-type Microsoft.Storage/storageAccounts 
```

다음 스크립트는 저장소 계정에 대 한 모든 잠금을 가져옵니다.

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
echo "Enter the storage account name:" &&
read storageAccountName &&
az lock list --resource-group $resourceGroupName --resource-name $storageAccountName --resource-type Microsoft.Storage/storageAccounts --parent ""
```

다음 스크립트는 저장소 계정의 잠금을 삭제 합니다.

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
echo "Enter the storage account name:" &&
read storageAccountName &&
lockId=$(az lock show --name LockSite --resource-group $resourceGroupName --resource-type Microsoft.Storage/storageAccounts --resource-name $storageAccountName --output tsv --query id)&&
az lock delete --ids $lockId
```

자세한 내용은 [Azure 리소스 관리자를 사용하여 리소스 잠그기](resource-group-lock-resources.md)를 참조하세요.

## <a name="tag-resources"></a>리소스 태그 지정

태그를 지정 하면 리소스 그룹 및 리소스를 논리적으로 구성할 수 있습니다. 자세한 내용은 [태그를 사용 하 여 Azure 리소스 구성](./resource-group-using-tags.md#azure-cli)을 참조 하세요.

## <a name="manage-access-to-resources"></a>리소스에 대한 액세스 관리

[RBAC(역할 기반 액세스 제어)](../role-based-access-control/overview.md)는 Azure에서 리소스에 대한 액세스를 관리하는 방법입니다. 자세한 내용은 [RBAC를 사용 하 여 액세스 관리 및 Azure CLI](../role-based-access-control/role-assignments-cli.md)을 참조 하세요.

## <a name="next-steps"></a>다음 단계

- Azure Resource Manager에 대 한 자세한 내용은 [Azure Resource Manager 개요](./resource-group-overview.md)를 참조 하세요.
- 리소스 관리자 템플릿 구문에 대 한 자세한 내용은 [Azure Resource Manager 템플릿의 구조 및 구문 이해](./resource-group-authoring-templates.md)를 참조 하세요.
- 템플릿을 개발 하는 방법을 알아보려면 단계별 [자습서](/azure/azure-resource-manager/)를 참조 하세요.
- Azure Resource Manager 템플릿 스키마를 보려면 [템플릿 참조](/azure/templates/)를 참조 하세요.
