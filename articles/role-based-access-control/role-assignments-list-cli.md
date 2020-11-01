---
title: Azure CLI를 사용 하 여 Azure 역할 할당 나열-Azure RBAC
description: 사용자, 그룹, 서비스 사용자 또는 관리 id에서 Azure CLI 및 Azure 역할 기반 액세스 제어 (Azure RBAC)를 사용 하 여 액세스할 수 있는 리소스를 확인 하는 방법에 대해 알아봅니다.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: 3483ee01-8177-49e7-b337-4d5cb14f5e32
ms.service: role-based-access-control
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/30/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 027cd8eb9c855afb845b08ce6aada7ddfd44daba
ms.sourcegitcommit: 4b76c284eb3d2b81b103430371a10abb912a83f4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/01/2020
ms.locfileid: "93147014"
---
# <a name="list-azure-role-assignments-using-azure-cli"></a>Azure CLI를 사용 하 여 Azure 역할 할당 나열

[!INCLUDE [Azure RBAC definition list access](../../includes/role-based-access-control-definition-list.md)] 이 문서에서는 Azure CLI를 사용 하 여 역할 할당을 나열 하는 방법을 설명 합니다.

> [!NOTE]
> 조직에서 [Azure 위임 된 리소스 관리](../lighthouse/concepts/azure-delegated-resource-management.md)를 사용 하는 서비스 공급자에 대해 아웃소싱 된 관리 기능을 사용 하는 경우 해당 서비스 공급자가 승인한 역할 할당은 여기에 표시 되지 않습니다.

## <a name="prerequisites"></a>필수 구성 요소

- Azure Cloud Shell 또는 [Azure CLI](/cli/azure) [의 Bash](/azure/cloud-shell/overview)

## <a name="list-role-assignments-for-a-user"></a>사용자에 대한 역할 할당 목록

특정 사용자에 대한 역할 할당을 나열하려면 [az role assignment list](/cli/azure/role/assignment#az-role-assignment-list)를 사용합니다.

```azurecli
az role assignment list --assignee {assignee}
```

기본적으로 현재 구독에 대 한 역할 할당만 표시 됩니다. 현재 구독 및 아래에 대 한 역할 할당을 보려면 `--all` 매개 변수를 추가 합니다. 상속 된 역할 할당을 보려면 `--include-inherited` 매개 변수를 추가 합니다.

다음 예에서는 *patlong \@ contoso.com* 사용자에 게 직접 할당 된 역할 할당을 나열 합니다.

```azurecli
az role assignment list --all --assignee patlong@contoso.com --output json --query '[].{principalName:principalName, roleDefinitionName:roleDefinitionName, scope:scope}'
```

```json
[
  {
    "principalName": "patlong@contoso.com",
    "roleDefinitionName": "Backup Operator",
    "scope": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales"
  },
  {
    "principalName": "patlong@contoso.com",
    "roleDefinitionName": "Virtual Machine Contributor",
    "scope": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales"
  }
]
```

## <a name="list-role-assignments-for-a-resource-group"></a>리소스 그룹에 대한 역할 할당 목록

리소스 그룹 범위에 존재 하는 역할 할당을 나열 하려면 [az role 할당 list](/cli/azure/role/assignment#az-role-assignment-list)를 사용 합니다.

```azurecli
az role assignment list --resource-group {resourceGroup}
```

다음 예에서는 *pharma-sales-projectforcast* 리소스 그룹에 대 한 역할 할당을 나열 합니다.

```azurecli
az role assignment list --resource-group pharma-sales --output json --query '[].{principalName:principalName, roleDefinitionName:roleDefinitionName, scope:scope}'
```

```json
[
  {
    "principalName": "patlong@contoso.com",
    "roleDefinitionName": "Backup Operator",
    "scope": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales"
  },
  {
    "principalName": "patlong@contoso.com",
    "roleDefinitionName": "Virtual Machine Contributor",
    "scope": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales"
  },
  
  ...

]
```

## <a name="list-role-assignments-for-a-subscription"></a>구독의 역할 할당 나열

구독 범위에서 모든 역할 할당을 나열 하려면 [az role 할당 list](/cli/azure/role/assignment#az-role-assignment-list)를 사용 합니다. 구독 ID를 가져오려면 Azure Portal의 **구독** 블레이드에서 찾거나 [az account list](/cli/azure/account#az-account-list)를 사용할 수 있습니다.

```azurecli
az role assignment list --subscription {subscriptionNameOrId}
```

예제:

```azurecli
az role assignment list --subscription 00000000-0000-0000-0000-000000000000 --output json --query '[].{principalName:principalName, roleDefinitionName:roleDefinitionName, scope:scope}'
```

```json
[
  {
    "principalName": "admin@contoso.com",
    "roleDefinitionName": "Owner",
    "scope": "/subscriptions/00000000-0000-0000-0000-000000000000"
  },
  {
    "principalName": "Subscription Admins",
    "roleDefinitionName": "Owner",
    "scope": "/subscriptions/00000000-0000-0000-0000-000000000000"
  },
  {
    "principalName": "alain@contoso.com",
    "roleDefinitionName": "Reader",
    "scope": "/subscriptions/00000000-0000-0000-0000-000000000000"
  },

  ...

]
```

## <a name="list-role-assignments-for-a-management-group"></a>관리 그룹의 역할 할당 나열

관리 그룹 범위에서 모든 역할 할당을 나열 하려면 [az role 할당 list](/cli/azure/role/assignment#az-role-assignment-list)를 사용 합니다. 관리 그룹 ID를 가져오려면 Azure Portal의 **관리 그룹** 블레이드에서 찾거나 [az account management-group list](/cli/azure/account/management-group#az-account-management-group-list)를 사용할 수 있습니다.

```azurecli
az role assignment list --scope /providers/Microsoft.Management/managementGroups/{groupId}
```

예제:

```azurecli
az role assignment list --scope /providers/Microsoft.Management/managementGroups/sales-group --output json --query '[].{principalName:principalName, roleDefinitionName:roleDefinitionName, scope:scope}'
```

```json
[
  {
    "principalName": "admin@contoso.com",
    "roleDefinitionName": "Owner",
    "scope": "/providers/Microsoft.Management/managementGroups/sales-group"
  },
  {
    "principalName": "alain@contoso.com",
    "roleDefinitionName": "Reader",
    "scope": "/providers/Microsoft.Management/managementGroups/sales-group"
  }
]
```

## <a name="list-role-assignments-for-a-managed-identity"></a>관리 id에 대 한 역할 할당 나열

1. 시스템 할당 또는 사용자 할당 관리 id의 보안 주체 ID를 가져옵니다.

    사용자 할당 관리 id의 보안 주체 ID를 가져오기 위해 [az ad sp list](/cli/azure/ad/sp#az-ad-sp-list) 또는 [az identity list](/cli/azure/identity#az-identity-list)를 사용할 수 있습니다.

    ```azurecli
    az ad sp list --display-name "{name}" --query [].objectId --output tsv
    ```

    시스템 할당 관리 id의 보안 주체 ID를 가져오기 위해 [az ad sp list](/cli/azure/ad/sp#az-ad-sp-list)를 사용할 수 있습니다.

    ```azurecli
    az ad sp list --display-name "{vmname}" --query [].objectId --output tsv
    ```

1. 역할 할당을 나열 하려면 [az role 할당 list](/cli/azure/role/assignment#az-role-assignment-list)를 사용 합니다.

    기본적으로 현재 구독에 대 한 역할 할당만 표시 됩니다. 현재 구독 및 아래에 대 한 역할 할당을 보려면 `--all` 매개 변수를 추가 합니다. 상속 된 역할 할당을 보려면 `--include-inherited` 매개 변수를 추가 합니다.

    ```azurecli
    az role assignment list --assignee {objectId}
    ```

## <a name="next-steps"></a>다음 단계

- [Azure CLI를 사용하여 Azure 역할 할당 추가 또는 제거](role-assignments-cli.md)
