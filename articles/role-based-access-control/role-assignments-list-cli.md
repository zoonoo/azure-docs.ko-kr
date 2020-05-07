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
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/10/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: f4b635d6867c36b8b0f385320e3720bea41b54d1
ms.sourcegitcommit: 4499035f03e7a8fb40f5cff616eb01753b986278
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/03/2020
ms.locfileid: "82735745"
---
# <a name="list-azure-role-assignments-using-azure-cli"></a>Azure CLI를 사용 하 여 Azure 역할 할당 나열

[!INCLUDE [Azure RBAC definition list access](../../includes/role-based-access-control-definition-list.md)]이 문서에서는 Azure CLI를 사용 하 여 역할 할당을 나열 하는 방법을 설명 합니다.

> [!NOTE]
> 조직에서 [Azure 위임 된 리소스 관리](../lighthouse/concepts/azure-delegated-resource-management.md)를 사용 하는 서비스 공급자에 대해 아웃소싱 된 관리 기능을 사용 하는 경우 해당 서비스 공급자가 승인한 역할 할당은 여기에 표시 되지 않습니다.

## <a name="prerequisites"></a>사전 요구 사항

- Azure Cloud Shell 또는 [Azure CLI](/cli/azure) [의 Bash](/azure/cloud-shell/overview)

## <a name="list-role-assignments-for-a-user"></a>사용자에 대한 역할 할당 목록

특정 사용자에 대한 역할 할당을 나열하려면 [az role assignment list](/cli/azure/role/assignment#az-role-assignment-list)를 사용합니다.

```azurecli-interactive
az role assignment list --assignee <assignee>
```

기본적으로 현재 구독에 대 한 역할 할당만 표시 됩니다. 현재 구독 및 아래에 대 한 역할 할당을 보려면 `--all` 매개 변수를 추가 합니다. 상속 된 `--include-inherited` 역할 할당을 보려면 매개 변수를 추가 합니다.

다음 예에서는 *patlong\@contoso.com* 사용자에 게 직접 할당 된 역할 할당을 나열 합니다.

```azurecli-interactive
az role assignment list --all --assignee patlong@contoso.com --output json | jq '.[] | {"principalName":.principalName, "roleDefinitionName":.roleDefinitionName, "scope":.scope}'
```

```
{
  "principalName": "patlong@contoso.com",
  "roleDefinitionName": "Backup Operator",
  "scope": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales"
}
{
  "principalName": "patlong@contoso.com",
  "roleDefinitionName": "Virtual Machine Contributor",
  "scope": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales"
}
```

## <a name="list-role-assignments-for-a-resource-group"></a>리소스 그룹에 대한 역할 할당 목록

리소스 그룹 범위에 존재 하는 역할 할당을 나열 하려면 [az role 할당 list](/cli/azure/role/assignment#az-role-assignment-list)를 사용 합니다.

```azurecli-interactive
az role assignment list --resource-group <resource_group>
```

다음 예에서는 *pharma-sales-projectforcast* 리소스 그룹에 대 한 역할 할당을 나열 합니다.

```azurecli-interactive
az role assignment list --resource-group pharma-sales --output json | jq '.[] | {"principalName":.principalName, "roleDefinitionName":.roleDefinitionName, "scope":.scope}'
```

```
{
  "principalName": "patlong@contoso.com",
  "roleDefinitionName": "Backup Operator",
  "scope": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales"
}
{
  "principalName": "patlong@contoso.com",
  "roleDefinitionName": "Virtual Machine Contributor",
  "scope": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales"
}

...
```

## <a name="list-role-assignments-for-a-subscription"></a>구독의 역할 할당 나열

구독 범위에서 모든 역할 할당을 나열 하려면 [az role 할당 list](/cli/azure/role/assignment#az-role-assignment-list)를 사용 합니다. 구독 ID를 가져오려면 Azure Portal의 **구독** 블레이드에서 찾거나 [az account list](/cli/azure/account#az-account-list)를 사용할 수 있습니다.

```azurecli-interactive
az role assignment list --subscription <subscription_name_or_id>
```

예:

```azurecli-interactive
az role assignment list --subscription 00000000-0000-0000-0000-000000000000 --output json | jq '.[] | {"principalName":.principalName, "roleDefinitionName":.roleDefinitionName, "scope":.scope}'
```

## <a name="list-role-assignments-for-a-management-group"></a>관리 그룹의 역할 할당 나열

관리 그룹 범위에서 모든 역할 할당을 나열 하려면 [az role 할당 list](/cli/azure/role/assignment#az-role-assignment-list)를 사용 합니다. 관리 그룹 ID를 가져오려면 Azure Portal의 **관리 그룹** 블레이드에서 찾거나 [az account management-group list](/cli/azure/account/management-group#az-account-management-group-list)를 사용할 수 있습니다.

```azurecli-interactive
az role assignment list --scope /providers/Microsoft.Management/managementGroups/<group_id>
```

예:

```azurecli-interactive
az role assignment list --scope /providers/Microsoft.Management/managementGroups/marketing-group --output json | jq '.[] | {"principalName":.principalName, "roleDefinitionName":.roleDefinitionName, "scope":.scope}'
```

## <a name="list-role-assignments-for-a-managed-identity"></a>관리 id에 대 한 역할 할당 나열

1. 시스템 할당 또는 사용자 할당 관리 id의 개체 ID를 가져옵니다.

    사용자 할당 관리 id의 개체 ID를 가져오려면 [az ad sp list](/cli/azure/ad/sp#az-ad-sp-list) 또는 [az identity list](/cli/azure/identity#az-identity-list)를 사용할 수 있습니다.

    ```azurecli-interactive
    az ad sp list --display-name "<name>" --query [].objectId --output tsv
    ```

    시스템 할당 관리 id의 개체 ID를 가져오려면 [az ad sp list](/cli/azure/ad/sp#az-ad-sp-list)를 사용할 수 있습니다.

    ```azurecli-interactive
    az ad sp list --display-name "<vmname>" --query [].objectId --output tsv
    ```

1. 역할 할당을 나열 하려면 [az role 할당 list](/cli/azure/role/assignment#az-role-assignment-list)를 사용 합니다.

    기본적으로 현재 구독에 대 한 역할 할당만 표시 됩니다. 현재 구독 및 아래에 대 한 역할 할당을 보려면 `--all` 매개 변수를 추가 합니다. 상속 된 `--include-inherited` 역할 할당을 보려면 매개 변수를 추가 합니다.

    ```azurecli-interactive
    az role assignment list --assignee <objectid>
    ```

## <a name="next-steps"></a>다음 단계

- [Azure CLI를 사용 하 여 Azure 역할 할당 추가 또는 제거](role-assignments-cli.md)
