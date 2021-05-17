---
title: Azure CLI를 사용하여 Azure 역할 할당 나열 - Azure RBAC
description: Azure CLI 및 Azure RBAC(Azure 역할 기반 액세스 제어)를 사용하여 액세스할 수 있는 사용자, 그룹, 서비스 주체 또는 관리 ID를 확인하는 방법을 알아봅니다.
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
ms.openlocfilehash: cc64e314a8acb035736df0521987cb78a7297326
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100556926"
---
# <a name="list-azure-role-assignments-using-azure-cli"></a>Azure CLI를 사용하여 Azure 역할 할당 나열

[!INCLUDE [Azure RBAC definition list access](../../includes/role-based-access-control/definition-list.md)] 이 문서에서는 Azure CLI를 사용하여 역할 할당을 나열하는 방법에 관해 설명합니다.

> [!NOTE]
> 조직에서 [Azure 위임된 리소스 관리](../lighthouse/concepts/azure-delegated-resource-management.md)를 사용하는 서비스 공급자에 대해 아웃소싱된 관리 기능을 사용하는 경우 해당 서비스 공급자가 승인한 역할 할당은 여기에 표시되지 않습니다.

## <a name="prerequisites"></a>사전 요구 사항

- [Azure Cloud Shell의 Bash](../cloud-shell/overview.md) 또는 [Azure CLI](/cli/azure)

## <a name="list-role-assignments-for-a-user"></a>사용자에 대한 역할 할당 목록

특정 사용자에 대한 역할 할당을 나열하려면 [az role assignment list](/cli/azure/role/assignment#az-role-assignment-list)를 사용합니다.

```azurecli
az role assignment list --assignee {assignee}
```

기본적으로 현재 구독에 대한 역할 할당만 표시됩니다. 현재 구독 및 이하에 대한 역할 할당을 보려면 `--all` 매개 변수를 추가합니다. 상속된 역할 할당을 보려면 `--include-inherited` 매개 변수를 추가합니다.

다음 예제에서는 *patlong\@contoso.com* 사용자에게 직접 할당된 역할 할당을 나열합니다.

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

리소스 그룹 범위에 존재하는 역할 할당을 나열하려면 [az role assignment list](/cli/azure/role/assignment#az-role-assignment-list)를 사용합니다.

```azurecli
az role assignment list --resource-group {resourceGroup}
```

다음 예제에서는 *pharma-sales* 리소스 그룹에 대한 역할 할당을 나열합니다.

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

구독 범위에서 모든 역할 할당을 나열하려면 [az role assignment list](/cli/azure/role/assignment#az-role-assignment-list)를 사용합니다. 구독 ID를 가져오려면 Azure Portal의 **구독** 블레이드에서 확인하거나 [az account list](/cli/azure/account#az-account-list)를 사용할 수 있습니다.

```azurecli
az role assignment list --subscription {subscriptionNameOrId}
```

예:

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

관리 그룹 범위에서 모든 역할 할당을 나열하려면 [az role assignment list](/cli/azure/role/assignment#az-role-assignment-list)를 사용합니다. 관리 그룹 ID를 가져오려면 Azure Portal의 **관리 그룹** 블레이드에서 확인하거나 [az account management-group list](/cli/azure/account/management-group#az-account-management-group-list)를 사용할 수 있습니다.

```azurecli
az role assignment list --scope /providers/Microsoft.Management/managementGroups/{groupId}
```

예:

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

## <a name="list-role-assignments-for-a-managed-identity"></a>관리 ID에 대한 역할 할당 나열

1. 시스템 할당 또는 사용자 할당 관리 ID의 보안 주체 ID를 가져옵니다.

    사용자 할당 관리 ID의 보안 주체 ID를 가져오려면 [az ad sp list](/cli/azure/ad/sp#az-ad-sp-list) 또는 [az identity list](/cli/azure/identity#az-identity-list)를 사용할 수 있습니다.

    ```azurecli
    az ad sp list --display-name "{name}" --query [].objectId --output tsv
    ```

    시스템 할당 관리 ID의 보안 주체 ID를 가져오려면 [az ad sp list](/cli/azure/ad/sp#az-ad-sp-list)를 사용할 수 있습니다.

    ```azurecli
    az ad sp list --display-name "{vmname}" --query [].objectId --output tsv
    ```

1. 역할 할당을 나열하려면 [az role assignment list](/cli/azure/role/assignment#az-role-assignment-list)를 사용합니다.

    기본적으로 현재 구독에 대한 역할 할당만 표시됩니다. 현재 구독 및 이하에 대한 역할 할당을 보려면 `--all` 매개 변수를 추가합니다. 상속된 역할 할당을 보려면 `--include-inherited` 매개 변수를 추가합니다.

    ```azurecli
    az role assignment list --assignee {objectId}
    ```

## <a name="next-steps"></a>다음 단계

- [Azure CLI를 사용하여 Azure 역할 할당](role-assignments-cli.md)