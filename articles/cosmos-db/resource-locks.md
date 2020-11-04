---
title: Azure Cosmos DB 리소스가 삭제 되거나 변경 되지 않도록 방지
description: Azure 리소스 잠금을 사용 하 여 Azure Cosmos DB 리소스가 삭제 되거나 변경 되는 것을 방지 합니다.
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 10/06/2020
ms.author: mjbrown
ms.openlocfilehash: 5243419d8e2c4780708e9bdee0d57f2734fe78b2
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2020
ms.locfileid: "93341963"
---
# <a name="prevent-azure-cosmos-db-resources-from-being-deleted-or-changed"></a>Azure Cosmos DB 리소스가 삭제 되거나 변경 되지 않도록 방지
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

관리자는 조직의 다른 사용자가 실수로 중요 한 리소스를 삭제 하거나 수정 하는 것을 방지 하기 위해 Azure Cosmos 계정, 데이터베이스 또는 컨테이너를 잠가야 할 수 있습니다. 잠금 수준을 CanNotDelete 또는 ReadOnly로 설정할 수 있습니다.

- **CanNotDelete** 는 권한이 부여된 사용자가 리소스를 읽고 수정할 수 있지만 삭제할 수 없음을 의미합니다.
- **ReadOnly** 는 권한이 부여된 사용자가 리소스를 읽을 수 있지만 해당 리소스를 삭제하거나 업데이트할 수 없음을 의미합니다. 이 잠금을 적용하는 것은 권한 있는 모든 사용자에게 판독기 역할에서 부여한 권한을 제한하는 것과 유사합니다.

## <a name="how-locks-are-applied"></a>잠금을 적용하는 방법

부모 범위에서 잠금을 적용하면 해당 범위 내 모든 리소스가 동일한 잠금을 상속합니다. 나중에 추가하는 리소스도 부모의 잠금을 상속합니다. 상속의 가장 제한적인 잠금이 우선 적용됩니다.

역할 기반 액세스 제어와 달리 관리 잠금을 사용하여 모든 사용자와 역할에 걸쳐 제한을 적용합니다. Azure Cosmos DB RBAC에 대 한 자세한 내용은 [Azure Cosmos DB의 역할 기반 액세스 제어](role-based-access-control.md)를 참조 하세요.

Resource Manager 잠금은 https://management.azure.com에 전송된 작업으로 구성된 관리 평면에서 수행되는 작업에만 적용됩니다. 리소스가 해당 기능을 수행하는 방법은 잠금을 통해 제한되지 않습니다. 리소스 변경은 제한되지만 리소스 작업은 제한되지 않습니다. 예를 들어 Azure Cosmos 컨테이너에 대 한 ReadOnly 잠금을 통해 컨테이너를 삭제 하거나 수정할 수 없습니다. 컨테이너에서 데이터를 생성, 업데이트 또는 삭제 하는 것을 방지 하지 않습니다. 이러한 작업은 https://management.azure.com으로 전송되지 않으므로 데이터 트랜잭션이 허용됩니다.

## <a name="manage-locks"></a>잠금 관리

> [!WARNING]
> DisableKeyBasedMetadataWriteAccess 속성을 사용 하도록 설정 하 여 Azure Cosmos 계정이 처음 잠겨 있지 않으면 계정 키를 사용 하 여 Azure Cosmos DB 액세스 하는 사용자가 변경한 내용에 대해 리소스 잠금이 작동 하지 않습니다. 이 속성을 사용 하기 전에이 속성을 사용 하 여 리소스를 변경 하는 기존 응용 프로그램이 계정 키를 통해 연결 되 고 처리량 변경, 인덱스 정책 업데이트 등의 리소스를 수정 하는 데 Azure Portal 사용 되는 기존 응용 프로그램을 중단 하지 않도록 주의 해야 합니다. 응용 프로그램이 계속 작동 하는 것을 확인 하 고 검사 목록을 검토 하 여 응용 프로그램이 계속 작동 하도록 하는 방법에 대 한 자세한 내용은 [Azure Cosmos DB sdk에서 변경 방지](role-based-access-control.md#prevent-sdk-changes)

### <a name="powershell"></a>PowerShell

```powershell
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

```bash
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

Azure Cosmos DB 리소스에 잠금을 적용 하는 경우 다음 형식을 사용 합니다.

- 이름 - `{resourceName}/Microsoft.Authorization/{lockName}`
- 형식 - `{resourceProviderNamespace}/{resourceType}/providers/locks`

> [!IMPORTANT]
> 기존 Azure Cosmos 계정을 수정 하는 경우이 속성을 사용 하 여 redploying 때 계정 및 자식 리소스에 대 한 다른 속성을 포함 해야 합니다. 이 템플릿을 그대로 배포 하거나 모든 계정 속성을 다시 설정 합니다.

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
