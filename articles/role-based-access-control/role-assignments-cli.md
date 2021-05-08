---
title: Azure CLI를 사용하여 Azure 역할 할당 - Azure RBAC
description: Azure CLI 및 Azure RBAC(Azure 역할 기반 액세스 제어)를 사용하여 사용자, 그룹, 서비스 주체 또는 관리 ID에 Azure 리소스 액세스 권한을 부여하는 방법 알아보기.
services: active-directory
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.topic: how-to
ms.workload: identity
ms.date: 09/28/2020
ms.author: rolyon
ms.custom: contperf-fy21q1, devx-track-azurecli
ms.openlocfilehash: ee356f32b6799c6182ec1c9e061a35271a4bbc23
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100556966"
---
# <a name="assign-azure-roles-using-azure-cli"></a>Azure CLI를 사용하여 Azure 역할 할당

[!INCLUDE [Azure RBAC definition grant access](../../includes/role-based-access-control/definition-grant.md)] 이 문서에서는 Azure CLI를 사용하여 역할을 할당하는 방법을 설명합니다.

## <a name="prerequisites"></a>사전 요구 사항

역할 할당을 위해 필요한 사항:

- [User Access Administrator](built-in-roles.md#user-access-administrator), [Owner](built-in-roles.md#owner) 등의 `Microsoft.Authorization/roleAssignments/write`사용 권한
- [Azure Cloud Shell의 Bash](../cloud-shell/overview.md) 또는 [Azure CLI](/cli/azure)

## <a name="steps-to-assign-an-azure-role"></a>Azure 역할 할당 단계

역할 할당은 보안 주체, 역할 정의, 범위 등 세 가지 요소로 구성됩니다.

### <a name="step-1-determine-who-needs-access"></a>1단계: 액세스가 필요한 사용자 확인

사용자, 그룹, 서비스 주체 또는 관리 ID에 역할을 할당합니다. 역할을 할당하기 위해 개체의 고유 ID를 지정해야 할 수도 있습니다. ID의 형식은 `11111111-1111-1111-1111-111111111111`입니다. Azure Portal 또는 Azure CLI를 사용하여 ID를 가져올 수 있습니다.

**사용자**

Azure AD 사용자의 경우 *patlong\@contoso.com* 또는 사용자 개체 ID와 같은 사용자 계정 이름을 가져옵니다. 개체 ID를 가져오기 위해 [az ad user show](/cli/azure/ad/user#az_ad_user_show)를 사용할 수 있습니다.

```azurecli
az ad user show --id "{principalName}" --query "objectId" --output tsv
```

**그룹**

Azure AD 그룹의 경우 그룹 개체 ID가 필요합니다. 개체 ID를 가져오려면 [az ad group show](/cli/azure/ad/group#az_ad_group_show) 또는 [az ad group list](/cli/azure/ad/group#az_ad_group_list)를 사용할 수 있습니다.

```azurecli
az ad group show --group "{groupName}" --query "objectId" --output tsv
```

**서비스 주체**

Azure AD 서비스 주체(애플리케이션에서 사용하는 ID)의 경우 서비스 주체 개체 ID가 필요합니다. 개체 ID를 가져오기 위해 [az ad sp list](/cli/azure/ad/sp#az_ad_sp_list)를 사용할 수 있습니다. 서비스 주체의 경우 애플리케이션 ID가 **아니라** 개체 ID를 사용합니다.

```azurecli
az ad sp list --all --query "[].{displayName:displayName, objectId:objectId}" --output tsv
az ad sp list --display-name "{displayName}"
```

**관리 ID**

시스템 할당 또는 사용자 할당 관리 ID의 경우 개체 ID가 필요합니다. 개체 ID를 가져오기 위해 [az ad sp list](/cli/azure/ad/sp#az_ad_sp_list)를 사용할 수 있습니다.

```azurecli
az ad sp list --all --filter "servicePrincipalType eq 'ManagedIdentity'"
```

사용자 할당 관리 ID만 나열하려면 [az identity list](/cli/azure/identity#az_identity_list)를 사용하면 됩니다.

```azurecli
az identity list
```
    
### <a name="step-2-select-the-appropriate-role"></a>2단계: 적절한 역할 선택

사용 권한은 역할에 함께 그룹화됩니다. [Azure 기본 제공 역할](built-in-roles.md) 목록에서 선택하거나 고유한 사용자 지정 역할을 사용할 수 있습니다. 필요한 최소한의 권한을 사용하여 액세스 권한을 부여하는 것이 가장 좋으므로, 광범위한 역할을 할당하지 마세요.

역할을 나열하고 고유한 역할 ID를 가져오려면 [az role definition list](/cli/azure/role/definition#az_role_definition_list)를 사용할 수 있습니다.

```azurecli
az role definition list --query "[].{name:name, roleType:roleType, roleName:roleName}" --output tsv
```

특정 역할에 대한 세부 정보를 나열하는 방법은 다음과 같습니다.

```azurecli
az role definition list --name "{roleName}"
```

자세한 내용은 [Azure 역할 정의 나열하기](role-definitions-list.md#azure-cli)를 참조하세요.
 
### <a name="step-3-identify-the-needed-scope"></a>3단계: 필요한 범위 확인

Azure는 리소스, [리소스 그룹](../azure-resource-manager/management/overview.md#resource-groups), 구독, [관리 그룹](../governance/management-groups/overview.md) 등 네 가지 범위 수준을 제공합니다. 필요한 최소한의 권한을 사용하여 액세스 권한을 부여하는 것이 가장 좋으므로, 역할을 광범위하게 할당하지 마세요. 범위에 대한 자세한 내용은 [범위 이해](scope-overview.md)를 참조하세요.

**리소스 범위**

리소스 범위의 경우, 해당 리소스에 대한 리소스 ID가 필요합니다. Azure Portal 리소스의 속성에서 리소스 ID를 확인할 수 있습니다. 리소스 ID 형식은 다음과 같습니다.

```
/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/{providerName}/{resourceType}/{resourceSubType}/{resourceName}
```

**리소스 그룹 범위**

리소스 그룹 범위에는 리소스 그룹의 이름이 필요합니다. Azure Portal의 **리소스 그룹** 페이지에서 이름을 찾거나 [az group list](/cli/azure/group#az_group_list)를 사용할 수 있습니다.

```azurecli
az group list --query "[].{name:name}" --output tsv
```

**구독 범위** 

구독 범위에는 구독 ID가 필요합니다. Azure Portal의 **구독** 페이지에서 ID를 찾거나 [az account list](/cli/azure/account#az_account_list)를 사용할 수 있습니다.

```azurecli
az account list --query "[].{name:name, id:id}" --output tsv
```

**관리 그룹 범위** 

관리 그룹 범위에는 관리 그룹 이름이 필요합니다. Azure Portal의 **관리 그룹** 페이지에서 이름을 찾거나 [az account management-group list](/cli/azure/account/management-group#az_account_management_group_list)를 사용할 수 있습니다.

```azurecli
az account management-group list --query "[].{name:name, id:id}" --output tsv
```
    
### <a name="step-4-assign-role"></a>4단계: 역할 할당

역할을 할당하려면 [az role assignment create](/cli/azure/role/assignment#az_role_assignment_create) 명령을 사용합니다. 명령은 범위에 따라 일반적으로 다음 형식 중 하나를 가집니다.

**리소스 범위**

```azurecli
az role assignment create --assignee "{assignee}" \
--role "{roleNameOrId}" \
--scope "/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/{providerName}/{resourceType}/{resourceSubType}/{resourceName}"
```

**리소스 그룹 범위**

```azurecli
az role assignment create --assignee "{assignee}" \
--role "{roleNameOrId}" \
--resource-group "{resourceGroupName}"
```

**구독 범위** 

```azurecli
az role assignment create --assignee "{assignee}" \
--role "{roleNameOrId}" \
--subscription "{subscriptionNameOrId}"
```

**관리 그룹 범위** 

```azurecli
az role assignment create --assignee "{assignee}" \
--role "{roleNameOrId}" \
--scope "/providers/Microsoft.Management/managementGroups/{managementGroupName}"
``` 

다음은 리소스 그룹 범위에서 사용자에게 [Virtual Machine Contributor](built-in-roles.md#virtual-machine-contributor) 역할을 할당하는 경우의 출력 예시입니다.

```azurecli
{
  "canDelegate": null,
  "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentId}",
  "name": "{roleAssignmentId}",
  "principalId": "{principalId}",
  "principalType": "User",
  "resourceGroup": "{resourceGroupName}",
  "roleDefinitionId": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/9980e02c-c2be-4d73-94e8-173b1dc7cf3c",
  "scope": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}",
  "type": "Microsoft.Authorization/roleAssignments"
}
```
    
## <a name="assign-role-examples"></a>역할 할당 예제

#### <a name="assign-a-role-for-all-blob-containers-in-a-storage-account-resource-scope"></a>스토리지 계정 리소스 범위에서 모든 Blob 컨테이너에 대해 역할 할당하기

*storage12345* 라는 스토리지 계정에 대한 리소스 범위에서 개체 ID가 *55555555-5555-5555-5555-555555555555* 인 서비스 주체에게 [Storage Blob Data Contributor](built-in-roles.md#storage-blob-data-contributor) 역할을 할당합니다.

```azurecli
az role assignment create --assignee "55555555-5555-5555-5555-555555555555" \
--role "Storage Blob Data Contributor" \
--scope "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Example-Storage-rg/providers/Microsoft.Storage/storageAccounts/storage12345"
```

#### <a name="assign-a-role-for-a-specific-blob-container-resource-scope"></a>특정 Blob 컨테이너 리소스 범위에 대해 역할 할당하기

*blob-container-01* 이라는 Blob 컨테이너에 대한 리소스 범위에서 개체 ID가 *55555555-5555-5555-5555-555555555555* 인 서비스 주체에게 [Storage Blob Data Contributor](built-in-roles.md#storage-blob-data-contributor) 역할을 할당합니다.

```azurecli
az role assignment create --assignee "55555555-5555-5555-5555-555555555555" \
--role "Storage Blob Data Contributor" \
--scope "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Example-Storage-rg/providers/Microsoft.Storage/storageAccounts/storage12345/blobServices/default/containers/blob-container-01"
```

#### <a name="assign-a-role-for-a-group-in-a-specific-virtual-network-resource-scope"></a>특정 가상 네트워크 리소스 범위에서 그룹에 대해 역할 할당하기

*pharma-sales-project-network* 라는 가상 네트워크에 대한 리소스 범위에서 ID가 22222222-2222-2222-2222-222222222222인 *Ann Mack 팀* 그룹에게 [Virtual Machine Contributor](built-in-roles.md#virtual-machine-contributor) 역할을 할당합니다.

```azurecli
az role assignment create --assignee "22222222-2222-2222-2222-222222222222" \
--role "Virtual Machine Contributor" \
--scope "/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/pharma-sales/providers/Microsoft.Network/virtualNetworks/pharma-sales-project-network"
```

#### <a name="assign-a-role-for-a-user-at-a-resource-group-scope"></a>리소스 그룹 범위에서 사용자에 대해 역할 할당하기

*pharma-sales* 리소스 그룹 범위에서 *patlong\@contoso.com* 사용자에게 [Virtual Machine Contributor](built-in-roles.md#virtual-machine-contributor) 역할을 할당합니다.

```azurecli
az role assignment create --assignee "patlong@contoso.com" \
--role "Virtual Machine Contributor" \
--resource-group "pharma-sales"
```

#### <a name="assign-a-role-for-a-user-using-the-unique-role-id-at-a-resource-group-scope"></a>리소스 그룹 범위에서 고유한 역할 ID를 사용하여 사용자에 대해 역할 할당하기

다음은 역할 이름이 변경될 수 있는 경우의 예입니다.

- 고유한 사용자 지정 역할을 사용하고 있으며 역할 이름을 변경하기로 결정했습니다.
- 이름에 **(미리 보기)** 가 있는 미리 보기 역할을 사용하고 있습니다. 역할이 릴리스되면 역할의 이름이 변경됩니다.

역할의 이름이 변경된 경우에도 역할 ID는 변경되지 않습니다. 스크립트 또는 자동화를 사용하여 역할을 할당하는 경우, 역할 이름 대신 고유한 역할 ID를 사용하는 것이 좋습니다. 역할 이름을 변경할 경우 스크립트가 작동할 가능성이 높습니다.

다음 예제에서는 *pharma-sales* 리소스 그룹 범위에서 *patlong\@contoso.com* 사용자에게 [Virtual Machine Contributor](built-in-roles.md#virtual-machine-contributor) 역할을 할당합니다.

```azurecli
az role assignment create --assignee "patlong@contoso.com" \
--role "9980e02c-c2be-4d73-94e8-173b1dc7cf3c" \
--resource-group "pharma-sales"
```

#### <a name="assign-a-role-for-all-blob-containers-at-a-resource-group-scope"></a>리소스 그룹 범위에서 모든 Blob 컨테이너에 대해 역할 할당하기

*Example-Storage-rg* 리소스 그룹 범위에서 개체 ID가 *55555555-5555-5555-5555-555555555555* 인 서비스 주체에게 [Storage Blob Data Contributor](built-in-roles.md#storage-blob-data-contributor) 역할을 할당합니다.

```azurecli
az role assignment create --assignee "55555555-5555-5555-5555-555555555555" \
--role "Storage Blob Data Contributor" \
--resource-group "Example-Storage-rg"
```

또는 `--scope` 매개 변수를 사용하여 정규화된 리소스 그룹을 지정할 수 있습니다.

```azurecli
az role assignment create --assignee "55555555-5555-5555-5555-555555555555" \
--role "Storage Blob Data Contributor" \
--scope "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Example-Storage-rg"
```

#### <a name="assign-a-role-for-an-application-at-a-resource-group-scope"></a>리소스 그룹 범위에서 애플리케이션에 대해 역할 할당하기

*pharma-sales* 리소스 그룹 범위에서 서비스 주체 개체 ID가 44444444-4444-4444-4444-444444444444인 애플리케이션에 [Virtual Machine Contributor](built-in-roles.md#virtual-machine-contributor) 역할을 할당합니다.

```azurecli
az role assignment create --assignee "44444444-4444-4444-4444-444444444444" \
--role "Virtual Machine Contributor" \
--resource-group "pharma-sales"
```

#### <a name="assign-a-role-for-a-new-service-principal-at-a-resource-group-scope"></a>리소스 그룹 범위에서 신규 서비스 주체에 대해 역할 할당하기

새 서비스 주체를 만들고 해당 서비스 주체에 역할을 즉시 할당하려고 하면 경우에 따라 해당 역할 할당이 실패할 수 있습니다. 예를 들어 스크립트를 사용하여 관리 ID를 새로 만든 다음 해당 서비스 주체에 역할을 할당하려고 하면 역할 할당이 실패할 수 있습니다. 이 오류가 발생하는 이유는 복제 지연 때문일 수 있습니다. 서비스 주체는 한 지역에 생성됩니다. 그러나 서비스 주체를 아직 복제하지 않은 다른 지역에서 역할 할당이 발생할 수 있습니다. 해당 시나리오를 해결하려면 역할 할당을 만들 때 보안 주체 속성을 지정해야 합니다.

역할을 할당하려면 [az role assignment create](/cli/azure/role/assignment#az_role_assignment_create)를 사용하고 `--assignee-object-id`의 값을 지정한 다음 `--assignee-principal-type`를 `ServicePrincipal`로 설정합니다.

```azurecli
az role assignment create --assignee-object-id "{assigneeObjectId}" \
--assignee-principal-type "{assigneePrincipalType}" \
--role "{roleNameOrId}" \
--resource-group "{resourceGroupName}" \
--scope "/subscriptions/{subscriptionId}"
```

다음 예제에서는 *pharma-sales* 리소스 그룹 범위에서 *msi-test* 관리 ID에게 [Virtual Machine Contributor](built-in-roles.md#virtual-machine-contributor) 역할을 할당합니다.

```azurecli
az role assignment create --assignee-object-id "33333333-3333-3333-3333-333333333333" \
--assignee-principal-type "ServicePrincipal" \
--role "Virtual Machine Contributor" \
--resource-group "pharma-sales"
```

#### <a name="assign-a-role-for-a-user-at-a-subscription-scope"></a>구독 범위에서 사용자에 대해 역할 할당하기

구독 범위에서 *annm\@example.com* 사용자에게 [Reader](built-in-roles.md#reader) 역할을 할당합니다.

```azurecli
az role assignment create --assignee "annm@example.com" \
--role "Reader" \
--subscription "00000000-0000-0000-0000-000000000000"
```

#### <a name="assign-a-role-for-a-group-at-a-subscription-scope"></a>구독 범위에서 그룹에 대해 역할 할당하기

구독 범위에서 ID가 22222222-2222-2222-2222-222222222222인 *Ann Mack Team* 그룹에게 [Reader](built-in-roles.md#reader) 역할을 할당합니다.

```azurecli
az role assignment create --assignee "22222222-2222-2222-2222-222222222222" \
--role "Reader" \
--subscription "00000000-0000-0000-0000-000000000000"
```

#### <a name="assign-a-role-for-all-blob-containers-at-a-subscription-scope"></a>구독 범위에서 모든 Blob 컨테이너에 대해 역할 할당하기

구독 범위에서 *alain\@example.com* 사용자에게 [Storage Blob Data Reader](built-in-roles.md#storage-blob-data-reader) 역할을 할당합니다.

```azurecli
az role assignment create --assignee "alain@example.com" \
--role "Storage Blob Data Reader" \
--scope "/subscriptions/00000000-0000-0000-0000-000000000000"
```

#### <a name="assign-a-role-for-a-user-at-a-management-group-scope"></a>관리 그룹 범위에서 사용자에 대해 역할 할당하기

관리 그룹 범위에서 *alain\@example.com* 사용자에게 [Billing Reader](built-in-roles.md#billing-reader) 역할을 할당합니다.

```azurecli
az role assignment create --assignee "alain@example.com" \
--role "Billing Reader" \
--scope "/providers/Microsoft.Management/managementGroups/marketing-group"
```

## <a name="next-steps"></a>다음 단계

- [Azure CLI를 사용하여 Azure 역할 할당 나열하기](role-assignments-list-cli.md)
- [Azure 리소스 및 리소스 그룹 관리를 위해 Azure CLI 사용](../azure-resource-manager/management/manage-resources-cli.md)
