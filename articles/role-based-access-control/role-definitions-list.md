---
title: Azure Portal, Azure PowerShell 또는 Azure CLI를 사용 하 여 Azure RBAC에서 역할 정의를 나열 합니다. Microsoft Docs
description: Azure Portal, Azure PowerShell 또는 Azure CLI를 사용 하 여 Azure RBAC에서 기본 제공 및 사용자 지정 역할을 나열 하는 방법을 알아봅니다.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: 8078f366-a2c4-4fbb-a44b-fc39fd89df81
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/25/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 839393d7535de530a27752f77e311c87c75825d9
ms.sourcegitcommit: c69c8c5c783db26c19e885f10b94d77ad625d8b4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/03/2019
ms.locfileid: "74710348"
---
# <a name="list-role-definitions-in-azure-rbac"></a>Azure RBAC에서 역할 정의 나열

역할 정의는 읽기, 쓰기 및 삭제와 같이 수행할 수 있는 사용 권한 컬렉션입니다. 일반적으로 역할 이라고 합니다. [Azure 역할 기반 access control (RBAC)](overview.md) 은 120의 [기본 제공 역할을 사용](built-in-roles.md) 하거나 사용자 고유의 사용자 지정 역할을 만들 수 있습니다. 이 문서에서는 Azure 리소스에 대 한 액세스 권한을 부여 하는 데 사용할 수 있는 기본 제공 및 사용자 지정 역할을 나열 하는 방법을 설명 합니다.

Azure Active Directory에 대 한 관리자 역할 목록을 보려면 [Azure Active Directory의 관리자 역할 권한](../active-directory/users-groups-roles/directory-assign-admin-roles.md)을 참조 하세요.

## <a name="azure-portal"></a>Azure Portal

### <a name="list-all-roles"></a>모든 역할 나열

Azure Portal의 모든 역할을 나열 하려면 다음 단계를 수행 합니다.

1. Azure Portal에서 **모든 서비스** 를 클릭 한 다음 범위를 선택 합니다. 예를 들어 **관리 그룹**, **구독**, **리소스 그룹** 또는 리소스를 선택할 수 있습니다.

1. 특정 리소스를 클릭 합니다.

1. **액세스 제어(IAM)** 를 클릭합니다.

1. **역할** 탭을 클릭하여 모든 기본 제공 역할 및 사용자 지정 역할 목록을 봅니다.

   현재 범위에서 각 역할에 할당 된 사용자 및 그룹의 수를 볼 수 있습니다.

   ![역할 목록](./media/role-definitions-list/roles-list.png)

## <a name="azure-powershell"></a>Azure PowerShell

### <a name="list-all-roles"></a>모든 역할 나열

Azure PowerShell의 모든 역할을 나열 하려면 [AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition)를 사용 합니다.

```azurepowershell
Get-AzRoleDefinition | FT Name, Description
```

```Example
AcrImageSigner                                    acr image signer
AcrQuarantineReader                               acr quarantine data reader
AcrQuarantineWriter                               acr quarantine data writer
API Management Service Contributor                Can manage service and the APIs
API Management Service Operator Role              Can manage service but not the APIs
API Management Service Reader Role                Read-only access to service and APIs
Application Insights Component Contributor        Can manage Application Insights components
Application Insights Snapshot Debugger            Gives user permission to use Application Insights Snapshot Debugge...
Automation Job Operator                           Create and Manage Jobs using Automation Runbooks.
Automation Operator                               Automation Operators are able to start, stop, suspend, and resume ...
...
```

### <a name="list-a-role-definition"></a>역할 정의 나열

특정 역할에 대 한 세부 정보를 나열 하려면 [AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition)을 사용 합니다.

```azurepowershell
Get-AzRoleDefinition <role_name>
```

```Example
PS C:\> Get-AzRoleDefinition "Contributor"

Name             : Contributor
Id               : b24988ac-6180-42a0-ab88-20f7382dd24c
IsCustom         : False
Description      : Lets you manage everything except access to resources.
Actions          : {*}
NotActions       : {Microsoft.Authorization/*/Delete, Microsoft.Authorization/*/Write,
                   Microsoft.Authorization/elevateAccess/Action}
DataActions      : {}
NotDataActions   : {}
AssignableScopes : {/}
```

### <a name="list-a-role-definition-in-json-format"></a>JSON 형식의 역할 정의를 나열 합니다.

JSON 형식의 역할을 나열 하려면 [AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition)를 사용 합니다.

```azurepowershell
Get-AzRoleDefinition <role_name> | ConvertTo-Json
```

```Example
PS C:\> Get-AzRoleDefinition "Contributor" | ConvertTo-Json

{
  "Name": "Contributor",
  "Id": "b24988ac-6180-42a0-ab88-20f7382dd24c",
  "IsCustom": false,
  "Description": "Lets you manage everything except access to resources.",
  "Actions": [
    "*"
  ],
  "NotActions": [
    "Microsoft.Authorization/*/Delete",
    "Microsoft.Authorization/*/Write",
    "Microsoft.Authorization/elevateAccess/Action",
    "Microsoft.Blueprint/blueprintAssignments/write",
    "Microsoft.Blueprint/blueprintAssignments/delete"
  ],
  "DataActions": [],
  "NotDataActions": [],
  "AssignableScopes": [
    "/"
  ]
}
```

### <a name="list-permissions-of-a-role-definition"></a>역할 정의의 사용 권한 나열

특정 역할에 대 한 사용 권한을 나열 하려면 [AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition)을 사용 합니다.

```azurepowershell
Get-AzRoleDefinition <role_name> | FL Actions, NotActions
```

```Example
PS C:\> Get-AzRoleDefinition "Contributor" | FL Actions, NotActions

Actions    : {*}
NotActions : {Microsoft.Authorization/*/Delete, Microsoft.Authorization/*/Write,
             Microsoft.Authorization/elevateAccess/Action,
             Microsoft.Blueprint/blueprintAssignments/write...}
```

```azurepowershell
(Get-AzRoleDefinition <role_name>).Actions
```

```Example
PS C:\> (Get-AzRoleDefinition "Virtual Machine Contributor").Actions

Microsoft.Authorization/*/read
Microsoft.Compute/availabilitySets/*
Microsoft.Compute/locations/*
Microsoft.Compute/virtualMachines/*
Microsoft.Compute/virtualMachineScaleSets/*
Microsoft.DevTestLab/schedules/*
Microsoft.Insights/alertRules/*
Microsoft.Network/applicationGateways/backendAddressPools/join/action
Microsoft.Network/loadBalancers/backendAddressPools/join/action
...
```

## <a name="azure-cli"></a>Azure CLI

### <a name="list-all-roles"></a>모든 역할 나열

Azure CLI의 모든 역할을 나열 하려면 [az role definition list](/cli/azure/role/definition#az-role-definition-list)를 사용 합니다.

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

다음 예에서는 모든 기본 제공 역할을 나열 합니다.

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

### <a name="list-a-role-definition"></a>역할 정의 나열

역할에 대 한 세부 정보를 나열 하려면 [az role definition list](/cli/azure/role/definition#az-role-definition-list)를 사용 합니다.

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

### <a name="list-permissions-of-a-role-definition"></a>역할 정의의 사용 권한 나열

다음 예에서는 *참가자* 역할의 *Actions* 및 *notactions* 만 나열 합니다.

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

다음 예제에서는 *가상 머신 참가자* 역할의 작업만 나열 합니다.

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

## <a name="next-steps"></a>다음 단계

- [Azure 리소스에 대한 기본 제공 역할](built-in-roles.md)
- [Azure 리소스에 대한 사용자 지정 역할](custom-roles.md)
- [Azure RBAC 및 Azure Portal를 사용 하 여 역할 할당 나열](role-assignments-list-portal.md)
- [Azure RBAC 및 Azure Portal를 사용 하 여 역할 할당 추가 또는 제거](role-assignments-portal.md)
