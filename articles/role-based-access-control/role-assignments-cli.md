---
title: RBAC 및 Azure CLI를 사용하여 Azure 리소스에 대한 액세스 관리 | Microsoft Docs
description: RBAC(역할 기반 액세스 제어) 및 Azure CLI를 사용하여 Azure 리소스에 대한 사용자, 그룹 및 애플리케이션의 액세스를 관리하는 방법을 알아봅니다. 여기에는 액세스 권한을 나열, 부여 및 제거하는 방법이 포함됩니다.
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
ms.date: 04/17/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 1cc3d3eca4063a8120851a9d3de1a85292eacb11
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60344566"
---
# <a name="manage-access-to-azure-resources-using-rbac-and-azure-cli"></a>RBAC 및 Azure CLI를 사용하여 Azure 리소스에 대한 액세스 관리

[RBAC(역할 기반 액세스 제어)](overview.md)는 Azure 리소스에 대한 액세스를 관리하는 방법입니다. 이 문서에서는 RBAC 및 Azure CLI를 사용하여 사용자, 그룹 및 애플리케이션의 액세스 권한을 관리하는 방법을 설명합니다.

## <a name="prerequisites"></a>필수 조건

액세스를 관리하려면 다음 중 하나가 필요합니다.

* [Azure Cloud Shell의 Bash](/azure/cloud-shell/overview)
* [Azure CLI](/cli/azure)

## <a name="list-roles"></a>역할 나열

사용 가능한 모든 역할 정의를 나열하려면 [az role definition list](/cli/azure/role/definition#az-role-definition-list)를 사용합니다.

```azurecli
az role definition list
```

다음 예제에서는 사용 가능한 모든 역할 정의의 이름과 설명을 나열합니다.

```azurecli
az role definition list --output json | jq '.[] | {"roleName":.roleName, "description":.description}'
```

```Output
{
  "roleName": "API Management Service Contributor",
  "description": "Can manage service and the APIs"
}
{
  "roleName": "API Management Service Operator Role",
  "description": "Can manage service but not the APIs"
}
{
  "roleName": "API Management Service Reader Role",
  "description": "Read-only access to service and APIs"
}

...
```

다음 예제에서는 모든 기본 제공 역할 정의를 나열합니다.

```azurecli
az role definition list --custom-role-only false --output json | jq '.[] | {"roleName":.roleName, "description":.description, "roleType":.roleType}'
```

```Output
{
  "roleName": "API Management Service Contributor",
  "description": "Can manage service and the APIs",
  "roleType": "BuiltInRole"
}
{
  "roleName": "API Management Service Operator Role",
  "description": "Can manage service but not the APIs",
  "roleType": "BuiltInRole"
}
{
  "roleName": "API Management Service Reader Role",
  "description": "Read-only access to service and APIs",
  "roleType": "BuiltInRole"
}

...
```

## <a name="list-a-role-definition"></a>역할 정의 나열

역할 정의 나열 하려면 사용 하 여 [az 역할 정의 목록](/cli/azure/role/definition#az-role-definition-list):

```azurecli
az role definition list --name <role_name>
```

다음 예제에서는 *Contributor* 역할 정의를 나열합니다.

```azurecli
az role definition list --name "Contributor"
```

```Output
[
  {
    "additionalProperties": {},
    "assignableScopes": [
      "/"
    ],
    "description": "Lets you manage everything except access to resources.",
    "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c",
    "name": "b24988ac-6180-42a0-ab88-20f7382dd24c",
    "permissions": [
      {
        "actions": [
          "*"
        ],
        "additionalProperties": {},
        "dataActions": [],
        "notActions": [
          "Microsoft.Authorization/*/Delete",
          "Microsoft.Authorization/*/Write",
          "Microsoft.Authorization/elevateAccess/Action"
        ],
        "notDataActions": []
      }
    ],
    "roleName": "Contributor",
    "roleType": "BuiltInRole",
    "type": "Microsoft.Authorization/roleDefinitions"
  }
]
```

### <a name="list-actions-of-a-role"></a>역할의 작업 나열

다음 예에서는 나열 합니다 *작업* 하 고 *notActions* 의 *참가자* 역할:

```azurecli
az role definition list --name "Contributor" --output json | jq '.[] | {"actions":.permissions[0].actions, "notActions":.permissions[0].notActions}'
```

```Output
{
  "actions": [
    "*"
  ],
  "notActions": [
    "Microsoft.Authorization/*/Delete",
    "Microsoft.Authorization/*/Write",
    "Microsoft.Authorization/elevateAccess/Action"
  ]
}
```

다음 예제에서는 방금의 작업을 나열 합니다 *Virtual Machine 참여자* 역할:

```azurecli
az role definition list --name "Virtual Machine Contributor" --output json | jq '.[] | .permissions[0].actions'
```

```Output
[
  "Microsoft.Authorization/*/read",
  "Microsoft.Compute/availabilitySets/*",
  "Microsoft.Compute/locations/*",
  "Microsoft.Compute/virtualMachines/*",
  "Microsoft.Compute/virtualMachineScaleSets/*",
  "Microsoft.Insights/alertRules/*",
  "Microsoft.Network/applicationGateways/backendAddressPools/join/action",
  "Microsoft.Network/loadBalancers/backendAddressPools/join/action",

  ...

  "Microsoft.Storage/storageAccounts/listKeys/action",
  "Microsoft.Storage/storageAccounts/read"
]
```

## <a name="list-access"></a>액세스 권한 나열

RBAC에서 역할 할당을 나열하면 액세스 권한이 나열됩니다.

### <a name="list-role-assignments-for-a-user"></a>사용자에 대한 역할 할당 목록

특정 사용자에 대한 역할 할당을 나열하려면 [az role assignment list](/cli/azure/role/assignment#az-role-assignment-list)를 사용합니다.

```azurecli
az role assignment list --assignee <assignee>
```

기본적으로 구독 범위의 할당만 표시됩니다. 리소스 또는 그룹 범위의 할당을 보려면 `--all`을 사용합니다.

다음 예제에서는 직접 할당 된 역할 할당을 나열 합니다 *patlong\@contoso.com* 사용자:

```azurecli
az role assignment list --all --assignee patlong@contoso.com --output json | jq '.[] | {"principalName":.principalName, "roleDefinitionName":.roleDefinitionName, "scope":.scope}'
```

```Output
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

### <a name="list-role-assignments-for-a-resource-group"></a>리소스 그룹에 대한 역할 할당 목록

리소스 그룹에 존재하는 역할 할당을 나열하려면 다[az role assignment list](/cli/azure/role/assignment#az-role-assignment-list)를 사용합니다.

```azurecli
az role assignment list --resource-group <resource_group>
```

다음 예제에 대 한 역할 할당을 나열 합니다 *제약 sales* 리소스 그룹:

```azurecli
az role assignment list --resource-group pharma-sales --output json | jq '.[] | {"roleDefinitionName":.roleDefinitionName, "scope":.scope}'
```

```Output
{
  "roleDefinitionName": "Backup Operator",
  "scope": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales"
}
{
  "roleDefinitionName": "Virtual Machine Contributor",
  "scope": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales"
}

...
```

## <a name="grant-access"></a>액세스 허용

RBAC에서 액세스 권한을 부여하기 위해 역할 할당을 만듭니다.

### <a name="create-a-role-assignment-for-a-user"></a>사용자에 대한 역할 할당 만들기

리소스 그룹 범위에서 사용자에 대한 역할 할당을 만들려면 [az role assignment create](/cli/azure/role/assignment#az-role-assignment-create)를 사용합니다.

```azurecli
az role assignment create --role <role_name_or_id> --assignee <assignee> --resource-group <resource_group>
```

다음 예제에서는 합니다 *Virtual Machine Contributor* 역할을 *patlong\@contoso.com* 사용자에 게는 *제약 sales* 리소스 그룹 범위:

```azurecli
az role assignment create --role "Virtual Machine Contributor" --assignee patlong@contoso.com --resource-group pharma-sales
```

### <a name="create-a-role-assignment-using-the-unique-role-id"></a>역할의 고유 ID를 사용 하 여 역할 할당 만들기

몇 번 하면 역할 이름이 변경 될 수 있습니다, 예를 들어 가지가 있습니다.

- 사용자 고유의 사용자 지정 역할을 사용 하는 이름을 변경 하기로 결정 한 합니다.
- 에 미리 보기 역할을 사용 하는 **(미리 보기)** 이름에서입니다. 역할 해제 되는 역할의 이름이 바뀝니다.

> [!IMPORTANT]
> 미리 보기 버전을 서비스 수준 계약 없이 제공 하 고 프로덕션 워크 로드에 대 한 권장 되지 않습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다.
> 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

역할 이름이 바뀐 경우에 역할 ID가 변경 되지 않습니다. 있습니다를 사용 하는 스크립트 또는 자동화에 역할 할당을 만드는 경우 역할의 고유 ID를 사용 하 여 역할 이름 대신이 가장 좋습니다. 따라서 역할의 이름을 바꾸면 스크립트 작업 가능성이 높습니다.

역할 이름 대신 고유한 역할 ID를 사용 하 여 역할 할당을 만들려면 사용 [az 역할 할당 만들기](/cli/azure/role/assignment#az-role-assignment-create)합니다.

```azurecli
az role assignment create --role <role_id> --assignee <assignee> --resource-group <resource_group>
```

다음 예제에서는 합니다 [Virtual Machine Contributor](built-in-roles.md#virtual-machine-contributor) 역할을 *patlong\@contoso.com* 사용자에 게는 *제약 sales* 리소스 그룹 범위. 역할의 고유 ID를 가져오려면 사용할 수 있습니다 [az 역할 정의 목록](/cli/azure/role/definition#az-role-definition-list) 보거나 [Azure 리소스에 대 한 기본 제공 역할](built-in-roles.md)입니다.

```azurecli
az role assignment create --role 9980e02c-c2be-4d73-94e8-173b1dc7cf3c --assignee patlong@contoso.com --resource-group pharma-sales
```

### <a name="create-a-role-assignment-for-a-group"></a>그룹에 대한 역할 할당 만들기

그룹에 대한 역할 할당을 만들려면 [az role assignment create](/cli/azure/role/assignment#az-role-assignment-create)를 사용합니다.

```azurecli
az role assignment create --role <role_name_or_id> --assignee-object-id <assignee_object_id> --resource-group <resource_group> --scope </subscriptions/subscription_id>
```

다음 예제에서는 구독 범위에서 ID 22222222-2222-2222-2222-222222222222인 *Ann Mack Team* 그룹에 *Reader* 역할을 할당합니다. 그룹의 ID를 가져오기 위해 [az ad group list](/cli/azure/ad/group#az-ad-group-list) 또는 [az ad group show](/cli/azure/ad/group#az-ad-group-show)를 사용할 수 있습니다.

```azurecli
az role assignment create --role Reader --assignee-object-id 22222222-2222-2222-2222-222222222222 --scope /subscriptions/00000000-0000-0000-0000-000000000000
```

다음 예제에서는 이름이 *pharma-sales-project-network*인 가상 네트워크에 대한 리소스 범위에서 ID 22222222-2222-2222-2222-222222222222의 *Ann Mack Team* 그룹에 *Virtual Machine Contributor* 역할을 할당합니다.

```azurecli
az role assignment create --role "Virtual Machine Contributor" --assignee-object-id 22222222-2222-2222-2222-222222222222 --scope /subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/pharma-sales/providers/Microsoft.Network/virtualNetworks/pharma-sales-project-network
```

### <a name="create-a-role-assignment-for-an-application"></a>애플리케이션에 대한 역할 할당 만들기

애플리케이션에 역할을 만들려면 [az role assignment create](/cli/azure/role/assignment#az-role-assignment-create)를 사용합니다.

```azurecli
az role assignment create --role <role_name_or_id> --assignee-object-id <assignee_object_id> --resource-group <resource_group> --scope </subscriptions/subscription_id>
```

다음 예제에서는 합니다 *Virtual Machine 참여자* 역할에 대 한 개체 ID 44444444-4444-4444-4444-444444444444 응용 프로그램에는 *제약 sales* 리소스 그룹 범위. 애플리케이션의 개체 ID를 가져오기 위해 [az ad app list](/cli/azure/ad/app#az-ad-app-list) 또는 [az ad app show](/cli/azure/ad/app#az-ad-app-show)를 사용할 수 있습니다.

```azurecli
az role assignment create --role "Virtual Machine Contributor" --assignee-object-id 44444444-4444-4444-4444-444444444444 --resource-group pharma-sales
```

## <a name="remove-access"></a>액세스 권한 제거

RBAC에서 액세스 권한을 제거하려면 [az role assignment delete](/cli/azure/role/assignment#az-role-assignment-delete)를 사용하여 역할 할당을 제거합니다.

```azurecli
az role assignment delete --assignee <assignee> --role <role_name_or_id> --resource-group <resource_group>
```

다음 예제에서는 제거는 *Virtual Machine 참여자* 에서 역할 할당을 *patlong\@contoso.com* 에서 사용자를 *제약 sales* 리소스 그룹:

```azurecli
az role assignment delete --assignee patlong@contoso.com --role "Virtual Machine Contributor" --resource-group pharma-sales
```

다음 예제에서는 구독 범위에서 ID 22222222-2222-2222-2222-222222222222인 *Ann Mack Team* 그룹에서 *Reader* 역할을 제거합니다. 그룹의 ID를 가져오기 위해 [az ad group list](/cli/azure/ad/group#az-ad-group-list) 또는 [az ad group show](/cli/azure/ad/group#az-ad-group-show)를 사용할 수 있습니다.

```azurecli
az role assignment delete --assignee 22222222-2222-2222-2222-222222222222 --role "Reader" --scope /subscriptions/00000000-0000-0000-0000-000000000000
```

## <a name="next-steps"></a>다음 단계

- [자습서: Azure CLI를 사용하여 Azure 리소스에 대한 사용자 지정 역할 만들기](tutorial-custom-role-cli.md)
- [Azure 리소스 및 리소스 그룹 관리를 위해 Azure CLI 사용](../azure-resource-manager/cli-azure-resource-manager.md)
