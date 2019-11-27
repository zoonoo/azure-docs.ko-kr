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
ms.date: 11/21/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 795a97f84bebf6c0e7c1692e82df2f7ce11e0bbd
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/22/2019
ms.locfileid: "74384090"
---
# <a name="manage-access-to-azure-resources-using-rbac-and-azure-cli"></a>RBAC 및 Azure CLI를 사용하여 Azure 리소스에 대한 액세스 관리

[RBAC(역할 기반 액세스 제어)](overview.md)는 Azure 리소스에 대한 액세스를 관리하는 방법입니다. 이 문서에서는 RBAC 및 Azure CLI를 사용하여 사용자, 그룹 및 애플리케이션의 액세스 권한을 관리하는 방법을 설명합니다.

## <a name="prerequisites"></a>선행 조건

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

역할 정의를 나열 하려면 [az role definition list](/cli/azure/role/definition#az-role-definition-list)를 사용 합니다.

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

## <a name="list-access"></a>액세스 권한 나열

RBAC에서 역할 할당을 나열하면 액세스 권한이 나열됩니다.

### <a name="list-role-assignments-for-a-user"></a>사용자에 대한 역할 할당 목록

특정 사용자에 대한 역할 할당을 나열하려면 [az role assignment list](/cli/azure/role/assignment#az-role-assignment-list)를 사용합니다.

```azurecli
az role assignment list --assignee <assignee>
```

기본적으로 구독으로 범위가 지정 된 직접 할당만 표시 됩니다. 리소스 또는 그룹별 범위가 지정 된 할당을 보려면 `--all`를 사용 하 고 상속 된 할당을 보려면 `--include-inherited`를 사용 합니다.

다음 예에서는 *patlong\@contoso.com* 사용자에 게 직접 할당 된 역할 할당을 나열 합니다.

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

### <a name="list-role-assignments-at-a-resource-group-scope"></a>리소스 그룹 범위에서 역할 할당 나열

리소스 그룹 범위에 존재 하는 역할 할당을 나열 하려면 [az role 할당 list](/cli/azure/role/assignment#az-role-assignment-list)를 사용 합니다.

```azurecli
az role assignment list --resource-group <resource_group>
```

다음 예에서는 *pharma-sales-projectforcast* 리소스 그룹에 대 한 역할 할당을 나열 합니다.

```azurecli
az role assignment list --resource-group pharma-sales --output json | jq '.[] | {"principalName":.principalName, "roleDefinitionName":.roleDefinitionName, "scope":.scope}'
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

...
```

### <a name="list-role-assignments-at-a-subscription-scope"></a>구독 범위에서 역할 할당 나열

구독 범위에서 모든 역할 할당을 나열 하려면 [az role 할당 list](/cli/azure/role/assignment#az-role-assignment-list)를 사용 합니다. 구독 ID를 가져오려면 Azure Portal의 **구독** 블레이드에서 찾거나 [az account list](/cli/azure/account#az-account-list)를 사용할 수 있습니다.

```azurecli
az role assignment list --subscription <subscription_name_or_id>
```

```Example
az role assignment list --subscription 00000000-0000-0000-0000-000000000000 --output json | jq '.[] | {"principalName":.principalName, "roleDefinitionName":.roleDefinitionName, "scope":.scope}'
```

### <a name="list-role-assignments-at-a-management-group-scope"></a>관리 그룹 범위에서 역할 할당 나열

관리 그룹 범위에서 모든 역할 할당을 나열 하려면 [az role 할당 list](/cli/azure/role/assignment#az-role-assignment-list)를 사용 합니다. 관리 그룹 ID를 가져오려면 Azure Portal의 **관리 그룹** 블레이드에서 찾거나 [az account management-group list](/cli/azure/ext/managementgroups/account/management-group#ext-managementgroups-az-account-management-group-list)를 사용할 수 있습니다.

```azurecli
az role assignment list --scope /providers/Microsoft.Management/managementGroups/<group_id>
```

```Example
az role assignment list --scope /providers/Microsoft.Management/managementGroups/marketing-group --output json | jq '.[] | {"principalName":.principalName, "roleDefinitionName":.roleDefinitionName, "scope":.scope}'
```

## <a name="get-object-ids"></a>개체 Id 가져오기

역할 할당을 나열, 추가 또는 제거 하려면 개체의 고유 ID를 지정 해야 할 수 있습니다. ID의 형식은 `11111111-1111-1111-1111-111111111111`입니다. Azure Portal 또는 Azure CLI를 사용 하 여 ID를 가져올 수 있습니다.

### <a name="user"></a>사용자

Azure AD 사용자의 개체 ID를 가져오려면 [az AD user show](/cli/azure/ad/user#az-ad-user-show)를 사용할 수 있습니다.

```azurecli
az ad user show --id "{email}" --query objectId --output tsv
```

### <a name="group"></a>그룹

Azure AD 그룹의 개체 ID를 가져오려면 [az ad group show](/cli/azure/ad/group#az-ad-group-show) 또는 [az ad group list](/cli/azure/ad/group#az-ad-group-list)를 사용할 수 있습니다.

```azurecli
az ad group show --group "{name}" --query objectId --output tsv
```

### <a name="application"></a>애플리케이션

응용 프로그램에서 사용 하는 id 인 Azure AD 서비스 사용자의 개체 ID를 가져오려면 [az AD sp list](/cli/azure/ad/sp#az-ad-sp-list)를 사용할 수 있습니다. 서비스 사용자의 경우 응용 프로그램 ID가 **아니라** 개체 id를 사용 합니다.

```azurecli
az ad sp list --display-name "{name}" --query [].objectId --output tsv
```

## <a name="grant-access"></a>액세스 권한 부여

RBAC에서 액세스 권한을 부여하기 위해 역할 할당을 만듭니다.

### <a name="create-a-role-assignment-for-a-user-at-a-resource-group-scope"></a>리소스 그룹 범위에서 사용자에 대한 역할 할당 만들기

리소스 그룹 범위에서 사용자에 게 액세스 권한을 부여 하려면 [az role 대입문 create](/cli/azure/role/assignment#az-role-assignment-create)를 사용 합니다.

```azurecli
az role assignment create --role <role_name_or_id> --assignee <assignee> --resource-group <resource_group>
```

다음 예에서는 *pharma-sales-projectforcast-sales* 리소스 그룹 범위에서 *patlong\@contoso.com* 사용자에 게 *가상 컴퓨터 참가자* 역할을 할당 합니다.

```azurecli
az role assignment create --role "Virtual Machine Contributor" --assignee patlong@contoso.com --resource-group pharma-sales
```

### <a name="create-a-role-assignment-using-the-unique-role-id"></a>고유한 역할 ID를 사용 하 여 역할 할당 만들기

역할 이름이 변경 될 수 있는 몇 가지 경우가 있습니다. 예를 들면 다음과 같습니다.

- 사용자 고유의 사용자 지정 역할을 사용 하 고 있으며 이름을 변경 하기로 결정 했습니다.
- 이름에 **(미리 보기)** 가 있는 미리 보기 역할을 사용 하 고 있습니다. 역할을 해제 하면 역할의 이름이 바뀝니다.

> [!IMPORTANT]
> 미리 보기 버전은 서비스 수준 계약 없이 제공 되며 프로덕션 워크 로드에는 권장 되지 않습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다.
> 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

역할의 이름이 바뀐 경우에도 역할 ID는 변경 되지 않습니다. 스크립트 또는 자동화를 사용 하 여 역할 할당을 만드는 경우 역할 이름 대신 고유한 역할 ID를 사용 하는 것이 좋습니다. 따라서 역할의 이름을 바꾸면 스크립트가 작동할 가능성이 높습니다.

역할 이름 대신 고유한 역할 ID를 사용 하 여 역할 할당을 만들려면 [az role 대입문 create](/cli/azure/role/assignment#az-role-assignment-create)를 사용 합니다.

```azurecli
az role assignment create --role <role_id> --assignee <assignee> --resource-group <resource_group>
```

다음 예에서는 *pharma-sales-projectforcast-sales* 리소스 그룹 범위에서 *patlong\@contoso.com* 사용자에 게 [가상 컴퓨터 참가자](built-in-roles.md#virtual-machine-contributor) 역할을 할당 합니다. 고유한 역할 ID를 가져오기 위해 [az role definition list](/cli/azure/role/definition#az-role-definition-list) 를 사용 하거나 [Azure 리소스에 대 한 기본 제공 역할](built-in-roles.md)을 볼 수 있습니다.

```azurecli
az role assignment create --role 9980e02c-c2be-4d73-94e8-173b1dc7cf3c --assignee patlong@contoso.com --resource-group pharma-sales
```

### <a name="create-a-role-assignment-for-a-group"></a>그룹에 대한 역할 할당 만들기

그룹에 대 한 액세스 권한을 부여 하려면 [az role 대입문 create](/cli/azure/role/assignment#az-role-assignment-create)를 사용 합니다. 그룹의 개체 ID를 가져오는 방법에 대 한 자세한 내용은 [개체 Id 가져오기](#get-object-ids)를 참조 하세요.

```azurecli
az role assignment create --role <role_name_or_id> --assignee-object-id <assignee_object_id> --resource-group <resource_group> --scope </subscriptions/subscription_id>
```

다음 예에서는 구독 범위에서 ID가 22222222-2222-2222-2222-222222222222 인 *Ann Mack 팀* 그룹에 *읽기 권한자* 역할을 할당 합니다.

```azurecli
az role assignment create --role Reader --assignee-object-id 22222222-2222-2222-2222-222222222222 --scope /subscriptions/00000000-0000-0000-0000-000000000000
```

다음 예제에서는 *pharma-sales-projectforcast*이라는 가상 네트워크에 대 한 리소스 범위에서 ID가 22222222-2222-2222-2222-222222222222 인 *Ann mack 팀* 그룹에 *가상 컴퓨터 참가자* 역할을 할당 합니다.

```azurecli
az role assignment create --role "Virtual Machine Contributor" --assignee-object-id 22222222-2222-2222-2222-222222222222 --scope /subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/pharma-sales/providers/Microsoft.Network/virtualNetworks/pharma-sales-project-network
```

### <a name="create-a-role-assignment-for-an-application-at-a-resource-group-scope"></a>리소스 그룹 범위에서 응용 프로그램에 대 한 역할 할당 만들기

응용 프로그램에 대 한 액세스 권한을 부여 하려면 [az role 대입문 create](/cli/azure/role/assignment#az-role-assignment-create)를 사용 합니다. 응용 프로그램의 개체 ID를 가져오는 방법에 대 한 자세한 내용은 [개체 Id 가져오기](#get-object-ids)를 참조 하세요.

```azurecli
az role assignment create --role <role_name_or_id> --assignee-object-id <assignee_object_id> --resource-group <resource_group>
```

다음 예에서는 *pharma-sales-projectforcast* 리소스 그룹 범위에서 개체 ID가 44444444-4444-4444-4444-444444444444 인 응용 프로그램에 *가상 컴퓨터 참가자* 역할을 할당 합니다.

```azurecli
az role assignment create --role "Virtual Machine Contributor" --assignee-object-id 44444444-4444-4444-4444-444444444444 --resource-group pharma-sales
```

### <a name="create-a-role-assignment-for-a-user-at-a-subscription-scope"></a>구독 범위에서 사용자에 대 한 역할 할당 만들기

구독 범위에서 사용자에 게 액세스 권한을 부여 하려면 [az role 대입문 create](/cli/azure/role/assignment#az-role-assignment-create)를 사용 합니다. 구독 ID를 가져오려면 Azure Portal의 **구독** 블레이드에서 찾거나 [az account list](/cli/azure/account#az-account-list)를 사용할 수 있습니다.

```azurecli
az role assignment create --role <role_name_or_id> --assignee <assignee> --subscription <subscription_name_or_id>
```

다음 예에서는 구독 범위에서 *\@example.com* 사용자에 게 *독자* 역할을 할당 합니다.

```azurecli
az role assignment create --role "Reader" --assignee annm@example.com --subscription 00000000-0000-0000-0000-000000000000
```

### <a name="create-a-role-assignment-for-a-user-at-a-management-group-scope"></a>관리 그룹 범위에서 사용자에 대 한 역할 할당 만들기

관리 그룹 범위에서 사용자에 게 액세스 권한을 부여 하려면 [az role 할당 create](/cli/azure/role/assignment#az-role-assignment-create)를 사용 합니다. 관리 그룹 ID를 가져오려면 Azure Portal의 **관리 그룹** 블레이드에서 찾거나 [az account management-group list](/cli/azure/ext/managementgroups/account/management-group#ext-managementgroups-az-account-management-group-list)를 사용할 수 있습니다.

```azurecli
az role assignment create --role <role_name_or_id> --assignee <assignee> --scope /providers/Microsoft.Management/managementGroups/<group_id>
```

다음 예에서는 관리 그룹 범위에서 *alain\@example.com* 사용자에 게 *청구 읽기 권한자* 역할을 할당 합니다.

```azurecli
az role assignment create --role "Billing Reader" --assignee alain@example.com --scope /providers/Microsoft.Management/managementGroups/marketing-group
```

### <a name="create-a-role-assignment-for-a-new-service-principal"></a>새 서비스 사용자에 대 한 역할 할당 만들기

새 서비스 주체를 만들고 해당 서비스 주체에 역할을 즉시 할당 하려고 하면 경우에 따라 해당 역할 할당이 실패할 수 있습니다. 예를 들어 스크립트를 사용 하 여 새로운 관리 되는 id를 만든 다음 해당 서비스 주체에 역할을 할당 하려고 하면 역할 할당이 실패할 수 있습니다. 이 오류가 발생 하는 이유는 복제 지연 일 수 있습니다. 서비스 사용자는 한 지역에 생성 됩니다. 그러나 서비스 사용자를 아직 복제 하지 않은 다른 지역에서 역할 할당이 발생할 수 있습니다. 이 시나리오를 해결 하려면 역할 할당을 만들 때 보안 주체 유형을 지정 해야 합니다.

역할 할당을 만들려면 [az role 할당 create](/cli/azure/role/assignment#az-role-assignment-create)를 사용 하 고 `--assignee-object-id`값을 지정한 다음 `--assignee-principal-type`를 `ServicePrincipal`로 설정 합니다.

```azurecli
az role assignment create --role <role_name_or_id> --assignee-object-id <assignee_object_id> --assignee-principal-type <assignee_principal_type> --resource-group <resource_group> --scope </subscriptions/subscription_id>
```

다음 예에서는 *pharma-sales-projectforcast* 리소스 그룹 범위에서 *msi 테스트* 관리 Id에 *가상 컴퓨터 참가자* 역할을 할당 합니다.

```azurecli
az role assignment create --role "Virtual Machine Contributor" --assignee-object-id 33333333-3333-3333-3333-333333333333 --assignee-principal-type ServicePrincipal --resource-group pharma-sales
```

## <a name="remove-access"></a>액세스 권한 제거

RBAC에서 액세스 권한을 제거하려면 [az role assignment delete](/cli/azure/role/assignment#az-role-assignment-delete)를 사용하여 역할 할당을 제거합니다.

```azurecli
az role assignment delete --assignee <assignee> --role <role_name_or_id> --resource-group <resource_group>
```

다음 예에서는 *pharma-sales-projectforcast sales* 리소스 그룹의 *patlong\@Contoso.com* 사용자에서 *가상 컴퓨터 참가자* 역할 할당을 제거 합니다.

```azurecli
az role assignment delete --assignee patlong@contoso.com --role "Virtual Machine Contributor" --resource-group pharma-sales
```

다음 예제에서는 구독 범위에서 ID가 22222222-2222-2222-2222-222222222222 인 *Ann Mack 팀* 그룹에서 *판독기* 역할을 제거 합니다. 그룹의 개체 ID를 가져오는 방법에 대 한 자세한 내용은 [개체 Id 가져오기](#get-object-ids)를 참조 하세요.

```azurecli
az role assignment delete --assignee 22222222-2222-2222-2222-222222222222 --role "Reader" --subscription 00000000-0000-0000-0000-000000000000
```

다음 예에서는 관리 그룹 범위에 있는 *alain\@example.com* 사용자에서 *청구 읽기 권한자* 역할을 제거 합니다. 관리 그룹의 ID를 가져오려면 [az account management-group list](/cli/azure/ext/managementgroups/account/management-group#ext-managementgroups-az-account-management-group-list)를 사용할 수 있습니다.

```azurecli
az role assignment delete --assignee alain@example.com --role "Billing Reader" --scope /providers/Microsoft.Management/managementGroups/marketing-group
```

## <a name="next-steps"></a>다음 단계

- [자습서: Azure CLI을 사용 하 여 Azure 리소스에 대 한 사용자 지정 역할 만들기](tutorial-custom-role-cli.md)
- [Azure 리소스 및 리소스 그룹 관리를 위해 Azure CLI 사용](../azure-resource-manager/cli-azure-resource-manager.md)
