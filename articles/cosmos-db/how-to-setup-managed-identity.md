---
title: Azure Cosmos DB 계정에 대해 Azure AD를 사용하여 관리 ID 구성
description: Azure Cosmos DB 계정에 대해 Azure Active Directory를 사용하여 관리 ID를 구성하는 방법을 알아봅니다.
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: how-to
ms.date: 04/23/2021
ms.author: thweiss
ms.openlocfilehash: 3f33cc08fcb9f3c43d9da312ce9ff12d9b20d722
ms.sourcegitcommit: 5f785599310d77a4edcf653d7d3d22466f7e05e1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/27/2021
ms.locfileid: "108065298"
---
# <a name="configure-managed-identities-with-azure-active-directory-for-your-azure-cosmos-db-account"></a>Azure Cosmos DB 계정에 대해 Azure Active Directory를 사용하여 관리 ID 구성
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Azure 리소스용 관리 ID는 Azure Active Directory에서 자동으로 관리되는 ID를 Azure 서비스에 제공합니다. 이 문서에서는 Azure Cosmos DB 계정에 대한 관리 ID를 만드는 방법을 보여줍니다.

> [!NOTE]
> 현재 시스템 할당 관리 ID만 Azure Cosmos DB에서 지원됩니다.

## <a name="prerequisites"></a>사전 요구 사항

- Azure 리소스에 대한 관리 ID에 익숙하지 않은 경우 [Azure 리소스에 대한 관리 ID란?](../active-directory/managed-identities-azure-resources/overview.md)을 참조하세요. 관리 ID 형식에 대해 알아보려면 [관리 ID 형식](../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types)을 참조하세요.
- 관리 ID를 설정하려면 계정에 [DocumentDB 계정 기여자 역할](../role-based-access-control/built-in-roles.md#documentdb-account-contributor)이 있어야 합니다.

## <a name="add-a-system-assigned-identity"></a>시스템 할당 ID 추가

### <a name="using-an-azure-resource-manager-arm-template"></a>ARM(Azure Resource Manager) 템플릿 사용

> [!IMPORTANT]
> 관리 ID로 작업할 때는 `2021-03-15` 이상의 `apiVersion`을 사용해야 합니다.

신규 또는 기존 Azure Cosmos DB 계정에서 시스템 할당 ID를 사용하도록 설정하려면 리소스 정의에 다음 속성을 포함합니다.

```json
"identity": {
    "type": "SystemAssigned"
}
```

ARM 템플릿의 `resources` 섹션은 다음과 같아야 합니다.

```json
"resources": [
    {
        "type": " Microsoft.DocumentDB/databaseAccounts",
        "identity": {
            "type": "SystemAssigned"
        },
        // ...
    },
    // ...
 ]
```

Azure Cosmos DB 계정이 생성되거나 업데이트되면 다음 속성이 표시됩니다.

```json
"identity": {
    "type": "SystemAssigned",
    "tenantId": "<azure-ad-tenant-id>",
    "principalId": "<azure-ad-principal-id>"
}
```

### <a name="using-the-azure-cli"></a>Azure CLI 사용

새 Azure Cosmos DB 계정을 만드는 동안 시스템 할당 ID를 사용하도록 설정하려면 `--assign-identity` 옵션을 추가합니다.

```azurecli
resourceGroupName='myResourceGroup'
accountName='mycosmosaccount'

az cosmosdb create \
    -n $accountName \
    -g $resourceGroupName \
    --locations regionName='West US 2' failoverPriority=0 isZoneRedundant=False \
    --assign-identity
```

`az cosmosdb identity assign` 명령을 사용하여 기존 계정에 시스템 할당 ID를 추가할 수도 있습니다.

```azurecli
resourceGroupName='myResourceGroup'
accountName='mycosmosaccount'

az cosmosdb identity assign \
    -n $accountName \
    -g $resourceGroupName
```

Azure Cosmos DB 계정이 만들어지거나 업데이트된 후에는 `az cosmosdb identity show` 명령을 사용하여 할당된 ID를 가져올 수 있습니다.

```azurecli
resourceGroupName='myResourceGroup'
accountName='mycosmosaccount'

az cosmosdb identity show \
    -n $accountName \
    -g $resourceGroupName
```

```json
{
    "type": "SystemAssigned",
    "tenantId": "<azure-ad-tenant-id>",
    "principalId": "<azure-ad-principal-id>"
}
```

## <a name="remove-a-system-assigned-identity"></a>시스템 할당 ID 제거

### <a name="using-an-azure-resource-manager-arm-template"></a>ARM(Azure Resource Manager) 템플릿 사용

> [!IMPORTANT]
> 관리 ID로 작업할 때는 `2021-03-15` 이상의 `apiVersion`을 사용해야 합니다.

Azure Cosmos DB 계정에서 시스템 할당 ID를 제거하려면 `identity` 속성의 `type`을 `None`으로 설정합니다.

```json
"identity": {
    "type": "None"
}
```

### <a name="using-the-azure-cli"></a>Azure CLI 사용

Azure Cosmos DB 계정에서 시스템 할당 ID를 제거하려면 `az cosmosdb identity remove` 명령을 사용합니다.

```azurecli
resourceGroupName='myResourceGroup'
accountName='mycosmosaccount'

az cosmosdb identity remove \
    -n $accountName \
    -g $resourceGroupName
```

## <a name="next-steps"></a>다음 단계

- [Azure 리소스에 대한 관리 ID](../active-directory/managed-identities-azure-resources/overview.md)에 대해 자세히 알아보기
- [Azure Cosmos DB의 고객 관리형 키](how-to-setup-cmk.md)에 대한 자세한 정보
