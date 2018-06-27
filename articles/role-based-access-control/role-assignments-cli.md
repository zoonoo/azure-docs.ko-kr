---
title: RBAC 및 Azure CLI를 사용하여 액세스 관리 | Microsoft Docs
description: RBAC(역할 기반 액세스 제어) 및 Azure CLI를 사용하여 사용자, 그룹 및 응용 프로그램의 액세스 권한을 관리하는 방법을 알아봅니다. 여기에는 액세스 권한 나열, 액세스 권한 부여 및 액세스 권한 제거가 포함됩니다.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: 3483ee01-8177-49e7-b337-4d5cb14f5e32
ms.service: role-based-access-control
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/03/2018
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 15ff519f5af7471d6adaae44e2af19422ad44fea
ms.sourcegitcommit: 1438b7549c2d9bc2ace6a0a3e460ad4206bad423
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/20/2018
ms.locfileid: "36294407"
---
# <a name="manage-access-using-rbac-and-azure-cli"></a>RBAC 및 Azure CLI를 사용하여 액세스 관리

[RBAC(역할 기반 액세스 제어)](overview.md)는 Azure에서 리소스에 대한 액세스를 관리하는 방법입니다. 이 문서에서는 RBAC 및 Azure CLI를 사용하여 사용자, 그룹 및 응용 프로그램의 액세스 권한을 관리하는 방법을 설명합니다.

## <a name="prerequisites"></a>필수 조건

Azure CLI를 사용하여 역할 할당을 관리하려면 먼저 다음 필수 조건이 필요합니다.

* [Azure CLI](/cli/azure). [Azure Cloud Shell](../cloud-shell/overview.md)을 실행하는 브라우저에서 사용하거나 macOS, Linux 및 Windows에서 [설치](/cli/azure/install-azure-cli)하고 명령줄에서 실행할 수 있습니다.

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

### <a name="list-actions-of-a-role"></a>역할의 작업 나열

역할 정의의 작업을 나열하려면 [az role definition list](/cli/azure/role/definition#az-role-definition-list)를 사용합니다.

```azurecli
az role definition list --name <role_name>
```

다음 예제에서는 *Contributor* 역할 정의를 나열합니다.

```azurecli
az role definition list --name "Contributor"
```

```Output
  {
    "additionalProperties": {},
    "assignableScopes": [
      "/"
    ],
    "description": "Lets you manage everything except access to resources.",
    "id": "/subscriptions/11111111-1111-1111-1111-111111111111/providers/Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c",
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

다음 예제에서는 목록에서 *Contributor* 역할의 *actions* 및 *notActions*를 나열합니다.

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

다음 예제에서는 *Virtual Machine Contributor* 역할의 작업을 나열합니다.

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

다음 예제에서는 *patlong@contoso.com* 사용자에게 직접 할당된 역할 할당을 나열합니다.

```azurecli
az role assignment list --all --assignee patlong@contoso.com --output json | jq '.[] | {"principalName":.principalName, "roleDefinitionName":.roleDefinitionName, "scope":.scope}'
```

```Output
{
  "principalName": "patlong@contoso.com",
  "roleDefinitionName": "Backup Operator",
  "scope": "/subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/pharma-sales-projectforecast"
}
{
  "principalName": "patlong@contoso.com",
  "roleDefinitionName": "Virtual Machine Contributor",
  "scope": "/subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/pharma-sales-projectforecast"
}
```

### <a name="list-role-assignments-for-a-resource-group"></a>리소스 그룹에 대한 역할 할당 목록

리소스 그룹에 존재하는 역할 할당을 나열하려면 다[az role assignment list](/cli/azure/role/assignment#az-role-assignment-list)를 사용합니다.

```azurecli
az role assignment list --resource-group <resource_group>
```

다음 예제에서는 *pharma-sales-projectforecast* 리소스 그룹에 대한 역할 할당을 나열합니다.

```azurecli
az role assignment list --resource-group pharma-sales-projectforecast --output json | jq '.[] | {"roleDefinitionName":.roleDefinitionName, "scope":.scope}'
```

```Output
{
  "roleDefinitionName": "Backup Operator",
  "scope": "/subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/pharma-sales-projectforecast"
}
{
  "roleDefinitionName": "Virtual Machine Contributor",
  "scope": "/subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/pharma-sales-projectforecast"
}

...
```

## <a name="grant-access"></a>액세스 권한 부여

RBAC에서 액세스 권한을 부여하기 위해 역할 할당을 만듭니다.

### <a name="create-a-role-assignment-for-a-user"></a>사용자에 대한 역할 할당 만들기

리소스 그룹 범위에서 사용자에 대한 역할 할당을 만들려면 [az role assignment create](/cli/azure/role/assignment#az-role-assignment-create)를 사용합니다.

```azurecli
az role assignment create --role <role> --assignee <assignee> --resource-group <resource_group>
```

다음 예제에서는 *pharma-sales-projectforecast* 리소스 그룹 범위에서 *patlong@contoso.com* 사용자에게 *Virtual Machine Contributor* 역할을 부여합니다.

```azurecli
az role assignment create --role "Virtual Machine Contributor" --assignee patlong@contoso.com --resource-group pharma-sales-projectforecast
```

### <a name="create-a-role-assignment-for-a-group"></a>그룹에 대한 역할 할당 만들기

그룹에 대한 역할 할당을 만들려면 [az role assignment create](/cli/azure/role/assignment#az-role-assignment-create)를 사용합니다.

```azurecli
az role assignment create --role <role> --assignee-object-id <assignee_object_id> --resource-group <resource_group> --scope </subscriptions/subscription_id>
```

다음 예제에서는 구독 범위에서 ID 22222222-2222-2222-2222-222222222222인 *Ann Mack Team* 그룹에 *Reader* 역할을 할당합니다. 그룹의 ID를 가져오기 위해 [az ad group list](/cli/azure/ad/group#az-ad-group-list) 또는 [az ad group show](/cli/azure/ad/group#az-ad-group-show)를 사용할 수 있습니다.

```azurecli
az role assignment create --role Reader --assignee-object-id 22222222-2222-2222-2222-222222222222 --scope /subscriptions/11111111-1111-1111-1111-111111111111
```

다음 예제에서는 이름이 *pharma-sales-project-network*인 가상 네트워크에 대한 리소스 범위에서 ID 22222222-2222-2222-2222-222222222222의 *Ann Mack Team* 그룹에 *Virtual Machine Contributor* 역할을 할당합니다.

```azurecli
az role assignment create --role "Virtual Machine Contributor" --assignee-object-id 22222222-2222-2222-2222-222222222222 --scope /subscriptions/11111111-1111-1111-1111-111111111111/resourcegroups/pharma-sales-projectforecast/providers/Microsoft.Network/virtualNetworks/pharma-sales-project-network
```

### <a name="create-a-role-assignment-for-an-application"></a>응용 프로그램에 대한 역할 할당 만들기

응용 프로그램에 역할을 만들려면 [az role assignment create](/cli/azure/role/assignment#az-role-assignment-create)를 사용합니다.

```azurecli
az role assignment create --role <role> --assignee-object-id <assignee_object_id> --resource-group <resource_group> --scope </subscriptions/subscription_id>
```

다음 예제에서는 *pharma-sales-projectforecast* 리소스 그룹 범위에서 개체 ID 44444444-4444-4444-4444-444444444444의 응용 프로그램에 *Virtual Machine Contributor* 역할을 부여합니다. 응용 프로그램의 개체 ID를 가져오기 위해 [az ad app list](/cli/azure/ad/app#az-ad-app-list) 또는 [az ad app show](/cli/azure/ad/app#az-ad-app-show)를 사용할 수 있습니다.

```azurecli
az role assignment create --role "Virtual Machine Contributor" --assignee-object-id 44444444-4444-4444-4444-444444444444 --resource-group pharma-sales-projectforecast
```

## <a name="remove-access"></a>액세스 권한 제거

RBAC에서 액세스 권한을 제거하려면 [az role assignment delete](/cli/azure/role/assignment#az-role-assignment-delete)를 사용하여 역할 할당을 제거합니다.

```azurecli
az role assignment delete --assignee <assignee> --role <role> --resource-group <resource_group>
```

다음 예제에서는 *pharma-sales-projectforecast* 리소스 그룹의 사용자 *patlong@contoso.com*에서 *Virtual Machine Contributor* 역할 할당을 제거합니다.

```azurecli
az role assignment delete --assignee patlong@contoso.com --role "Virtual Machine Contributor" --resource-group pharma-sales-projectforecast
```

다음 예제에서는 구독 범위에서 ID 22222222-2222-2222-2222-222222222222인 *Ann Mack Team* 그룹에서 *Reader* 역할을 제거합니다. 그룹의 ID를 가져오기 위해 [az ad group list](/cli/azure/ad/group#az-ad-group-list) 또는 [az ad group show](/cli/azure/ad/group#az-ad-group-show)를 사용할 수 있습니다.

```azurecli
az role assignment delete --assignee 22222222-2222-2222-2222-222222222222 --role "Reader" --scope /subscriptions/11111111-1111-1111-1111-111111111111
```

## <a name="custom-roles"></a>사용자 지정 역할

### <a name="list-custom-roles"></a>사용자 지정 역할 나열

범위에서 할당할 수 있는 역할을 나열하려면 [az role definition list](/cli/azure/role/definition#az-role-definition-list)를 사용합니다.

다음 예제는 모두 현재 구독의 모든 사용자 지정 역할을 나열합니다.

```azurecli
az role definition list --custom-role-only true --output json | jq '.[] | {"roleName":.roleName, "roleType":.roleType}'
```

```azurecli
az role definition list --output json | jq '.[] | if .roleType == "CustomRole" then {"roleName":.roleName, "roleType":.roleType} else empty end'
```

```Output
{
  "roleName": "My Management Contributor",
  "type": "CustomRole"
}
{
  "roleName": "My Service Operator Role",
  "type": "CustomRole"
}
{
  "roleName": "My Service Reader Role",
  "type": "CustomRole"
}

...
```

### <a name="create-a-custom-role"></a>사용자 지정 역할 만들기

사용자 지정 역할을 만들려면 [az role definition create](/cli/azure/role/definition#az-role-definition-create)를 사용합니다. 역할 정의는 JSON 설명이거나, JSON 설명이 포함된 파일에 대한 경로가 될 수 있습니다.

```azurecli
az role definition create --role-definition <role_definition>
```

다음 예제에서는 *Virtual Machine Operator*라는 사용자 지정 역할을 만듭니다. 이 사용자 지정 역할은 *Microsoft.Compute*, *Microsoft.Storage* 및 *Microsoft.Network* 리소스 공급자의 모든 읽기 작업에 대한 액세스 권한을 부여하고 가상 머신을 시작, 다시 시작 및 모니터링할 수 있는 권한을 부여합니다. 두 구독 모두에서 사용자 지정 역할을 사용할 수 있습니다. 이 예제에서는 입력으로 JSON 파일을 사용합니다.

vmoperator.json

```json
{
  "Name": "Virtual Machine Operator",
  "IsCustom": true,
  "Description": "Can monitor and restart virtual machines.",
  "Actions": [
    "Microsoft.Storage/*/read",
    "Microsoft.Network/*/read",
    "Microsoft.Compute/*/read",
    "Microsoft.Compute/virtualMachines/start/action",
    "Microsoft.Compute/virtualMachines/restart/action",
    "Microsoft.Authorization/*/read",
    "Microsoft.Resources/subscriptions/resourceGroups/read",
    "Microsoft.Insights/alertRules/*",
    "Microsoft.Support/*"
  ],
  "NotActions": [

  ],
  "AssignableScopes": [
    "/subscriptions/11111111-1111-1111-1111-111111111111",
    "/subscriptions/33333333-3333-3333-3333-333333333333"
  ]
}
```

```azurecli
az role definition create --role-definition ~/roles/vmoperator.json
```

### <a name="update-a-custom-role"></a>사용자 지정 역할 업데이트

사용자 지정 역할을 업데이트하려면 먼저 [az role definition list](/cli/azure/role/definition#az-role-definition-list)를 사용하여 역할 정의를 검색합니다. 그런 다음 역할 정의를 원하는 대로 변경합니다. 마지막으로 [az role definition update](/cli/azure/role/definition#az-role-definition-update)를 사용하여 업데이트된 역할 정의를 저장합니다.

```azurecli
az role definition update --role-definition <role_definition>
```

다음 예제에서는 *Virtual Machine Operator* 사용자 지정 역할의 *Actions*에 *Microsoft.Insights/diagnosticSettings/* 작업을 추가합니다.

vmoperator.json

```json
{
  "Name": "Virtual Machine Operator",
  "IsCustom": true,
  "Description": "Can monitor and restart virtual machines.",
  "Actions": [
    "Microsoft.Storage/*/read",
    "Microsoft.Network/*/read",
    "Microsoft.Compute/*/read",
    "Microsoft.Compute/virtualMachines/start/action",
    "Microsoft.Compute/virtualMachines/restart/action",
    "Microsoft.Authorization/*/read",
    "Microsoft.Resources/subscriptions/resourceGroups/read",
    "Microsoft.Insights/alertRules/*",
    "Microsoft.Insights/diagnosticSettings/*",
    "Microsoft.Support/*"
  ],
  "NotActions": [

  ],
  "AssignableScopes": [
    "/subscriptions/11111111-1111-1111-1111-111111111111",
    "/subscriptions/33333333-3333-3333-3333-333333333333"
  ]
}
```

```azurecli
az role definition update --role-definition ~/roles/vmoperator.json
```

### <a name="delete-a-custom-role"></a>사용자 지정 역할 삭제

사용자 지정 역할을 삭제하려면 [az role definition delete](/cli/azure/role/definition#az-role-definition-delete)를 사용합니다. 삭제할 역할을 지정하려면 역할 이름이나 역할 ID를 사용합니다. 역할 ID를 결정하려면 [az role definition list](/cli/azure/role/definition#az-role-definition-list)를 사용합니다.

```azurecli
az role definition delete --name <role_name or role_id>
```

다음 예제에서는 *Virtual Machine Operator* 사용자 지정 역할을 삭제합니다.

```azurecli
az role definition delete --name "Virtual Machine Operator"
```

## <a name="next-steps"></a>다음 단계

[!INCLUDE [role-based-access-control-toc.md](../../includes/role-based-access-control-toc.md)]

