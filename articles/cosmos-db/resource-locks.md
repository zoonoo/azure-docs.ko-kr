---
title: Azure Cosmos DB 리소스가 삭제되거나 변경되지 않도록 방지
description: Azure 리소스 잠금을 사용하여 Azure Cosmos DB 리소스가 삭제되거나 변경되는 것을 방지합니다.
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 05/13/2021
ms.author: mjbrown
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 02bcfa765f9047e3d633402840a37cccc8ada867
ms.sourcegitcommit: df574710c692ba21b0467e3efeff9415d336a7e1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/28/2021
ms.locfileid: "110680724"
---
# <a name="prevent-azure-cosmos-db-resources-from-being-deleted-or-changed"></a>Azure Cosmos DB 리소스가 삭제되거나 변경되지 않도록 방지
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

관리자는 조직의 다른 사용자가 실수로 중요한 리소스를 삭제하거나 수정하는 것을 방지하기 위해 Azure Cosmos 계정, 데이터베이스 또는 컨테이너를 잠가야 할 수 있습니다. 잠금 수준을 CanNotDelete 또는 ReadOnly로 설정할 수 있습니다.

- **CanNotDelete** 는 권한이 부여된 사용자가 리소스를 읽고 수정할 수 있지만 삭제할 수 없음을 의미합니다.
- **ReadOnly** 는 권한이 부여된 사용자가 리소스를 읽을 수 있지만 해당 리소스를 삭제하거나 업데이트할 수 없음을 의미합니다. 이 잠금을 적용하는 것은 권한 있는 모든 사용자에게 판독기 역할에서 부여한 권한을 제한하는 것과 유사합니다.

## <a name="how-locks-are-applied"></a>잠금을 적용하는 방법

부모 범위에서 잠금을 적용하면 해당 범위 내 모든 리소스가 동일한 잠금을 상속합니다. 나중에 추가하는 리소스도 부모의 잠금을 상속합니다. 상속의 가장 제한적인 잠금이 우선 적용됩니다.

Azure 역할 기반 액세스 제어와 달리 관리 잠금을 사용하여 모든 사용자 및 역할에 제한을 적용합니다. Azure Cosmos DB용 Azure RBAC에 대한 자세한 내용은 [Azure Cosmos DB의 Azure 역할 기반 액세스 제어](role-based-access-control.md)를 참조하세요.

Resource Manager 잠금은 https://management.azure.com에 전송된 작업으로 구성된 관리 평면에서 수행되는 작업에만 적용됩니다. 리소스가 해당 기능을 수행하는 방법은 잠금을 통해 제한되지 않습니다. 리소스 변경은 제한되지만 리소스 작업은 제한되지 않습니다. 예를 들어 Azure Cosmos 컨테이너에 대한 ReadOnly 잠금을 통해 컨테이너를 삭제하거나 수정할 수 없습니다. 컨테이너에서 데이터를 생성, 업데이트 또는 삭제하는 것을 방지하지 않습니다. 이러한 작업은 https://management.azure.com으로 전송되지 않으므로 데이터 트랜잭션이 허용됩니다.

## <a name="manage-locks"></a>잠금 관리

> [!WARNING]
> disableKeyBasedMetadataWriteAccess 속성을 사용하도록 설정하여 Azure Cosmos 계정을 먼저 잠그지 않으면 계정 키를 사용하여 Azure Cosmos DB에 액세스하는 사용자가 변경한 사항에 대해 리소스 잠금이 작동하지 않습니다. 이 속성을 사용하기 전에 SDK, Azure Portal 또는 계정 키를 통해 연결하고 처리량 변경, 인덱스 정책 업데이트 등과 같은 리소스를 수정하는 타사 도구를 사용하여 리소스를 변경하는 기존 애플리케이션이 중단되지 않도록 주의해야 합니다. 애플리케이션이 계속 작동하는지 확인하고 자세한 내용을 확인하려면 [Azure Cosmos DB SDK에서 변경 방지](role-based-access-control.md#prevent-sdk-changes)를 참조하세요.

### <a name="powershell"></a>PowerShell

```powershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "my-cosmos-account"
$lockName = "$accountName-Lock"

# First, update the account to prevent changes by anything that connects via account keys
Update-AzCosmosDBAccount -ResourceGroupName $resourceGroupName -Name $accountName -DisableKeyBasedMetadataWriteAccess true

# Create a Delete Lock on an Azure Cosmos account resource and all child resources
New-AzResourceLock `
    -ApiVersion "2020-04-01" `
    -ResourceType "Microsoft.DocumentDB/databaseAccounts" `
    -ResourceGroupName $resourceGroupName `
    -ResourceName $accountName `
    -LockName $lockName `
    -LockLevel "CanNotDelete" # CanNotDelete or ReadOnly
```

### <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
resourceGroupName='myResourceGroup'
accountName='my-cosmos-account'
$lockName="$accountName-Lock"

# First, update the account to prevent changes by anything that connects via account keys
az cosmosdb update  --name $accountName --resource-group $resourceGroupName  --disable-key-based-metadata-write-access true

# Create a Delete Lock on an Azure Cosmos account resource
az lock create --name $lockName \
    --resource-group $resourceGroupName \
    --resource-type Microsoft.DocumentDB/databaseAccount \
    --lock-type 'CanNotDelete' # CanNotDelete or ReadOnly \
    --resource $accountName
```

### <a name="template"></a>템플릿

Azure Cosmos DB 리소스에 잠금을 적용할 때 다음 형식을 사용합니다.

- 이름 - `{resourceName}/Microsoft.Authorization/{lockName}`
- 형식 - `{resourceProviderNamespace}/{resourceType}/providers/locks`

> [!IMPORTANT]
> 기존 Azure Cosmos 계정을 수정할 때 이 속성을 사용하여 재배포하는 경우 계정 및 자식 리소스에 대한 다른 속성을 포함해야 합니다. 이 템플릿을 그대로 배포하면 모든 계정 속성이 초기화됩니다.

```json
"resources": [
    {
        "type": "Microsoft.DocumentDB/databaseAccounts",
        "name": "[variables('accountName')]",
        "apiVersion": "2020-04-01",
        "kind": "GlobalDocumentDB",
        "location": "[parameters('location')]",
        "properties": {
            "consistencyPolicy": "[variables('consistencyPolicy')[parameters('defaultConsistencyLevel')]]",
            "locations": "[variables('locations')]",
            "databaseAccountOfferType": "Standard",
            "enableAutomaticFailover": "[parameters('automaticFailover')]",
            "disableKeyBasedMetadataWriteAccess": true
        }
    },
    {
        "type": "Microsoft.DocumentDB/databaseAccounts/providers/locks",
        "apiVersion": "2020-04-01",
        "name": "[concat(variables('accountName'), '/Microsoft.Authorization/siteLock')]",
        "dependsOn": [
        "[resourceId('Microsoft.DocumentDB/databaseAccounts', variables('accountName'))]"
        ],
        "properties": {
        "level": "CanNotDelete",
        "notes": "Cosmos account should not be deleted."
        }
    }
]
```

## <a name="next-steps"></a>다음 단계

- [Azure Resource Manager 잠금 개요](../azure-resource-manager/management/lock-resources.md)
