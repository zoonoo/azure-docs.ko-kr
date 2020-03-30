---
title: Azure RBAC 및 Azure CLI를 사용하여 역할 할당 목록
description: 사용자, 그룹, 서비스 주체 또는 관리되는 ID가 AZURE 역할 기반 액세스 제어(RBAC) 및 Azure CLI를 사용하여 액세스할 수 있는 리소스를 확인하는 방법을 알아봅니다.
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
ms.openlocfilehash: 5716e7bb89d017866bd1575256e2d119bb7acbe5
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2020
ms.locfileid: "80385064"
---
# <a name="list-role-assignments-using-azure-rbac-and-azure-cli"></a>Azure RBAC 및 Azure CLI를 사용하여 역할 할당 목록

[!INCLUDE [Azure RBAC definition list access](../../includes/role-based-access-control-definition-list.md)]이 문서에서는 Azure CLI를 사용하여 역할 할당을 나열하는 방법을 설명합니다.

> [!NOTE]
> 조직에서 [Azure 위임리소스 관리를](../lighthouse/concepts/azure-delegated-resource-management.md)사용하는 서비스 공급자에게 관리 기능을 아웃소싱한 경우 해당 서비스 공급자가 승인한 역할 할당은 여기에 표시되지 않습니다.

## <a name="prerequisites"></a>사전 요구 사항

- Azure 클라우드 셸 또는 [Azure CLI에서](/cli/azure) [배쉬](/azure/cloud-shell/overview)

## <a name="list-role-assignments-for-a-user"></a>사용자에 대한 역할 할당 목록

특정 사용자에 대한 역할 할당을 나열하려면 [az role assignment list](/cli/azure/role/assignment#az-role-assignment-list)를 사용합니다.

```azurecli-interactive
az role assignment list --assignee <assignee>
```

기본적으로 현재 구독에 대한 역할 할당만 표시됩니다. 현재 구독 및 아래에 대한 역할 할당을 `--all` 보려면 매개 변수를 추가합니다. 상속된 역할 할당을 보려면 `--include-inherited` 매개 변수를 추가합니다.

다음 예제에는 *patlong\@contoso.com* 사용자에게 직접 할당되는 역할 할당이 나열되어 있습니다.

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

리소스 그룹 범위에 있는 역할 할당을 나열하려면 [az 역할 할당 목록을](/cli/azure/role/assignment#az-role-assignment-list)사용합니다.

```azurecli-interactive
az role assignment list --resource-group <resource_group>
```

다음 예제에는 *제약 판매* 리소스 그룹에 대한 역할 할당이 나열됩니다.

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

구독 범위에서 모든 역할 할당을 나열하려면 [az 역할 할당 목록을](/cli/azure/role/assignment#az-role-assignment-list)사용합니다. 구독 ID를 얻으려면 Azure 포털의 **구독** 블레이드에서 찾거나 [az 계정 목록을](/cli/azure/account#az-account-list)사용할 수 있습니다.

```azurecli-interactive
az role assignment list --subscription <subscription_name_or_id>
```

예제:

```azurecli-interactive
az role assignment list --subscription 00000000-0000-0000-0000-000000000000 --output json | jq '.[] | {"principalName":.principalName, "roleDefinitionName":.roleDefinitionName, "scope":.scope}'
```

## <a name="list-role-assignments-for-a-management-group"></a>관리 그룹의 역할 할당 나열

관리 그룹 범위에서 모든 역할 할당을 나열하려면 [az 역할 할당 목록을](/cli/azure/role/assignment#az-role-assignment-list)사용합니다. 관리 그룹 ID를 얻으려면 Azure 포털의 **관리 그룹** 블레이드에서 찾거나 [az 계정 관리 그룹 목록을](/cli/azure/account/management-group#az-account-management-group-list)사용할 수 있습니다.

```azurecli-interactive
az role assignment list --scope /providers/Microsoft.Management/managementGroups/<group_id>
```

예제:

```azurecli-interactive
az role assignment list --scope /providers/Microsoft.Management/managementGroups/marketing-group --output json | jq '.[] | {"principalName":.principalName, "roleDefinitionName":.roleDefinitionName, "scope":.scope}'
```

## <a name="list-role-assignments-for-a-managed-identity"></a>관리되는 ID에 대한 역할 할당 목록

1. 시스템에서 할당된 또는 사용자가 할당한 관리 ID의 개체 ID를 가져옵니다.

    사용자가 할당한 관리 ID의 개체 ID를 얻으려면 [az 광고 sp 목록](/cli/azure/ad/sp#az-ad-sp-list) 또는 az ID [목록을](/cli/azure/identity#az-identity-list)사용할 수 있습니다.

    ```azurecli-interactive
    az ad sp list --display-name "<name>" --query [].objectId --output tsv
    ```

    시스템에서 할당된 관리 ID의 개체 ID를 얻으려면 [az 광고 sp 목록을](/cli/azure/ad/sp#az-ad-sp-list)사용할 수 있습니다.

    ```azurecli-interactive
    az ad sp list --display-name "<vmname>" --query [].objectId --output tsv
    ```

1. 역할 할당을 나열하려면 [az 역할 할당 목록을](/cli/azure/role/assignment#az-role-assignment-list)사용합니다.

    기본적으로 현재 구독에 대한 역할 할당만 표시됩니다. 현재 구독 및 아래에 대한 역할 할당을 `--all` 보려면 매개 변수를 추가합니다. 상속된 역할 할당을 보려면 `--include-inherited` 매개 변수를 추가합니다.

    ```azurecli-interactive
    az role assignment list --assignee <objectid>
    ```

## <a name="next-steps"></a>다음 단계

- [Azure RBAC 및 Azure CLI를 사용하여 역할 할당 추가 또는 제거](role-assignments-cli.md)
