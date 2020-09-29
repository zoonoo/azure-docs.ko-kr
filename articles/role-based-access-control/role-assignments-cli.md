---
title: Azure CLI를 사용 하 여 Azure 역할 할당 추가 또는 제거-Azure RBAC
description: Azure CLI 및 Azure RBAC (역할 기반 액세스 제어)를 사용 하 여 사용자, 그룹, 서비스 주체 또는 관리 되는 id에 대 한 Azure 리소스에 대 한 액세스 권한을 부여 하는 방법에 대해 알아봅니다.
services: active-directory
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.topic: how-to
ms.workload: identity
ms.date: 09/28/2020
ms.author: rolyon
ms.custom: contperfq1
ms.openlocfilehash: 17a32b27fba4fcde2e148e44e9db768cc9270e01
ms.sourcegitcommit: 3792cf7efc12e357f0e3b65638ea7673651db6e1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/29/2020
ms.locfileid: "91441989"
---
# <a name="add-or-remove-azure-role-assignments-using-azure-cli"></a>Azure CLI를 사용 하 여 Azure 역할 할당 추가 또는 제거

[!INCLUDE [Azure RBAC definition grant access](../../includes/role-based-access-control-definition-grant.md)] 이 문서에서는 Azure CLI를 사용 하 여 역할을 할당 하는 방법을 설명 합니다.

## <a name="prerequisites"></a>필수 요건

역할 할당을 추가 하거나 제거 하려면 다음을 수행 해야 합니다.

- `Microsoft.Authorization/roleAssignments/write` 및 `Microsoft.Authorization/roleAssignments/delete` 사용 권한(예: [사용자 액세스 관리자](built-in-roles.md#user-access-administrator) 또는 [소유자](built-in-roles.md#owner))
- Azure Cloud Shell 또는 [Azure CLI](/cli/azure) [의 Bash](/azure/cloud-shell/overview)

## <a name="steps-to-add-a-role-assignment"></a>역할 할당을 추가 하는 단계

Azure RBAC에서 액세스 권한을 부여하기 위해 역할 할당을 추가합니다. 역할 할당은 보안 주체, 역할 정의, 범위의 세 가지 요소로 구성됩니다. 역할 할당을 추가 하려면 다음 단계를 수행 합니다.

### <a name="step-1-determine-who-needs-access"></a>1 단계: 액세스 해야 하는 사용자 확인

사용자, 그룹, 서비스 주체 또는 관리 id에 역할을 할당할 수 있습니다. 역할 할당을 추가 하려면 개체의 고유 ID를 지정 해야 할 수 있습니다. ID의 형식은 `11111111-1111-1111-1111-111111111111`입니다. Azure Portal 또는 Azure CLI를 사용 하 여 ID를 가져올 수 있습니다.

**사용자**

Azure AD 사용자의 경우 *patlong \@ contoso.com* 또는 사용자 개체 ID와 같은 사용자 계정 이름을 가져옵니다. 개체 ID를 가져오려면 [az ad user show](/cli/azure/ad/user#az_ad_user_show)를 사용할 수 있습니다.

```azurecli
az ad user show --id "{principalName}" --query "objectId" --output tsv
```

**그룹**

Azure AD 그룹의 경우 그룹 개체 ID가 필요 합니다. 개체 ID를 가져오려면 [az ad group show](/cli/azure/ad/group#az_ad_group_show) 또는 [az ad group list](/cli/azure/ad/group#az_ad_group_list)를 사용할 수 있습니다.

```azurecli
az ad group show --group "{groupName}" --query "objectId" --output tsv
```

**서비스 주체**

Azure AD 서비스 사용자 (응용 프로그램에서 사용 하는 id)의 경우 서비스 주체 개체 ID가 필요 합니다. 개체 ID를 가져오려면 [az ad sp list](/cli/azure/ad/sp#az_ad_sp_list)를 사용할 수 있습니다. 서비스 주체의 경우 애플리케이션 ID가 **아니라** 개체 ID를 사용합니다.

```azurecli
az ad sp list --all --query "[].{displayName:displayName, objectId:objectId}" --output tsv
az ad sp list --display-name "{displayName}"
```

**관리 ID**

시스템 할당 또는 사용자 할당 관리 id의 경우 개체 ID가 필요 합니다. 개체 ID를 가져오려면 [az ad sp list](/cli/azure/ad/sp#az_ad_sp_list)를 사용할 수 있습니다.

```azurecli
az ad sp list --all --filter "servicePrincipalType eq 'ManagedIdentity'"
```

사용자 할당 관리 id만 나열 하려면 [az identity list](/cli/azure/identity#az_identity_list)를 사용 하면 됩니다.

```azurecli
az identity list
```
    
### <a name="step-2-find-the-appropriate-role"></a>2 단계: 적절 한 역할 찾기

사용 권한은 역할에 함께 그룹화 됩니다. 여러 [Azure 기본 제공 역할](built-in-roles.md) 목록에서 선택 하거나 고유한 사용자 지정 역할을 사용할 수 있습니다. 필요한 최소한의 권한을 사용 하 여 액세스 권한을 부여 하는 것이 가장 좋습니다. 따라서 더 광범위 한 역할을 할당 하지 마십시오.

역할을 나열 하 고 고유한 역할 ID를 가져오려면 [az role definition list](/cli/azure/role/definition#az_role_definition_list)를 사용할 수 있습니다.

```azurecli
az role definition list --query "[].{name:name, roleType:roleType, roleName:roleName}" --output tsv
```

특정 역할에 대 한 세부 정보를 나열 하는 방법은 다음과 같습니다.

```azurecli
az role definition list --name "{roleName}"
```

자세한 내용은 [Azure 역할 정의 나열](role-definitions-list.md#azure-cli)을 참조 하세요.
 
### <a name="step-3-identify-the-needed-scope"></a>3 단계: 필요한 범위 확인

Azure는 리소스, [리소스 그룹](../azure-resource-manager/management/overview.md#resource-groups), 구독 및 [관리 그룹](../governance/management-groups/overview.md)의 네 가지 범위 수준을 제공 합니다. 필요한 최소 권한을 사용 하 여 액세스 권한을 부여 하는 것이 가장 좋습니다. 따라서 더 광범위 한 범위에서 역할을 할당 하지 마십시오.

**리소스 범위**

리소스 범위의 경우 리소스의 리소스 ID가 필요 합니다. Azure Portal 리소스의 속성을 살펴보면 리소스 ID를 찾을 수 있습니다. 리소스 ID의 형식은 다음과 같습니다.

```
/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/{providerName}/{resourceType}/{resourceSubType}/{resourceName}
```

**리소스 그룹 범위**

리소스 그룹 범위에는 리소스 그룹의 이름이 필요 합니다. Azure Portal의 **리소스 그룹** 페이지에서 이름을 찾거나 [az group list](/cli/azure/group#az_group_list)를 사용할 수 있습니다.

```azurecli
az group list --query "[].{name:name}" --output tsv
```

**구독 범위** 

구독 범위에는 구독 ID가 필요 합니다. Azure Portal의 **구독** 페이지에서 ID를 찾거나 [az account list](/cli/azure/account#az_account_list)를 사용할 수 있습니다.

```azurecli
az account list --query "[].{name:name, id:id}" --output tsv
```

**관리 그룹 범위** 

관리 그룹 범위에는 관리 그룹 이름이 필요 합니다. Azure Portal의 **관리 그룹** 페이지에서 이름을 찾거나 [az account Management-group 목록을](/cli/azure/account/management-group#az_account_management_group_list)사용할 수 있습니다.

```azurecli
az account management-group list --query "[].{name:name, id:id}" --output tsv
```
    
### <a name="step-4-add-role-assignment"></a>4 단계: 역할 할당 추가

역할 할당을 추가 하려면 [az role 대입문 create](/cli/azure/role/assignment#az_role_assignment_create) 명령을 사용 합니다. 범위에 따라 명령은 일반적으로 다음 형식 중 하나를 갖습니다.

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

다음은 리소스 그룹 범위에서 사용자에 게 [가상 컴퓨터 참가자](built-in-roles.md#virtual-machine-contributor) 역할을 할당 하는 경우의 출력 예입니다.

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
    
## <a name="add-role-assignment-examples"></a>역할 할당 예제 추가

### <a name="add-role-assignment-for-a-specific-blob-container-resource-scope"></a>특정 blob 컨테이너 리소스 범위에 대 한 역할 할당 추가

Blob 컨테이너 *-01*이라는 blob 컨테이너에 대 한 리소스 범위에서 개체 ID가 *55555555-5555-5555-5555-555555555555* 인 서비스 사용자에 게 [저장소 Blob 데이터 참가자](built-in-roles.md#storage-blob-data-contributor) 역할을 할당 합니다.

```azurecli
az role assignment create --assignee "55555555-5555-5555-5555-555555555555" \
--role "Storage Blob Data Contributor" \
--scope "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Example-Storage-rg/providers/Microsoft.Storage/storageAccounts/storage12345/blobServices/default/containers/blob-container-01"
```

### <a name="add-role-assignment-for-all-blob-containers-in-a-storage-account-resource-scope"></a>저장소 계정 리소스 범위에서 모든 blob 컨테이너에 대 한 역할 할당 추가

*Storage12345*라는 저장소 계정에 대 한 리소스 범위에서 개체 ID가 *55555555-5555-5555-5555-555555555555* 인 서비스 사용자에 게 [저장소 Blob 데이터 참가자](built-in-roles.md#storage-blob-data-contributor) 역할을 할당 합니다.

```azurecli
az role assignment create --assignee "55555555-5555-5555-5555-555555555555" \
--role "Storage Blob Data Contributor" \
--scope "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Example-Storage-rg/providers/Microsoft.Storage/storageAccounts/storage12345"
```

### <a name="add-role-assignment-for-a-group-in-a-specific-virtual-network-resource-scope"></a>특정 가상 네트워크 리소스 범위에서 그룹에 대 한 역할 할당 추가

*Pharma-sales-projectforcast*이라는 가상 네트워크에 대 한 리소스 범위에서 ID가 22222222-2222-2222-2222-222222222222 인 *Ann mack 팀* 그룹에 [가상 컴퓨터 참가자](built-in-roles.md#virtual-machine-contributor) 역할을 할당 합니다.

```azurecli
az role assignment create --assignee "22222222-2222-2222-2222-222222222222" \
--role "Virtual Machine Contributor" \
--scope "/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/pharma-sales/providers/Microsoft.Network/virtualNetworks/pharma-sales-project-network"
```

### <a name="add-role-assignment-for-a-user-at-a-resource-group-scope"></a>리소스 그룹 범위에서 사용자에 대 한 역할 할당 추가

*Pharma-sales-projectforcast-sales* 리소스 그룹 범위에서 *patlong \@ Contoso.com* user에 [가상 컴퓨터 참가자](built-in-roles.md#virtual-machine-contributor) 역할을 할당 합니다.

```azurecli
az role assignment create --assignee "patlong@contoso.com" \
--role "Virtual Machine Contributor" \
--resource-group "pharma-sales"
```

### <a name="add-role-assignment-for-a-user-using-the-unique-role-id-at-a-resource-group-scope"></a>리소스 그룹 범위에서 고유한 역할 ID를 사용 하 여 사용자에 대 한 역할 할당 추가

역할 이름이 변경 될 수 있는 몇 가지 경우가 있습니다. 예를 들면 다음과 같습니다.

- 사용자 고유의 사용자 지정 역할을 사용 하 고 있으며 이름을 변경 하기로 결정 했습니다.
- 이름에 **(미리 보기)** 가 있는 미리 보기 역할을 사용 하 고 있습니다. 역할을 해제 하면 역할의 이름이 바뀝니다.

역할의 이름이 바뀐 경우에도 역할 ID는 변경 되지 않습니다. 스크립트 또는 자동화를 사용 하 여 역할 할당을 만드는 경우 역할 이름 대신 고유한 역할 ID를 사용 하는 것이 좋습니다. 따라서 역할의 이름을 바꾸면 스크립트가 작동할 가능성이 높습니다.

다음 예에서는 *pharma-sales-projectforcast-sales* 리소스 그룹 범위에서 *patlong \@ Contoso.com* 사용자에 게 [가상 컴퓨터 참가자](built-in-roles.md#virtual-machine-contributor) 역할을 할당 합니다.

```azurecli
az role assignment create --assignee "patlong@contoso.com" \
--role "9980e02c-c2be-4d73-94e8-173b1dc7cf3c" \
--resource-group "pharma-sales"
```

### <a name="add-role-assignment-for-all-blob-containers-at-a-resource-group-scope"></a>리소스 그룹 범위에서 모든 blob 컨테이너에 대 한 역할 할당 추가

저장소 rg 리소스 그룹 범위 *예제* 에서 개체 ID가 *55555555-5555-5555-5555-555555555555* 인 서비스 사용자에 게 [저장소 Blob 데이터 참가자](built-in-roles.md#storage-blob-data-contributor) 역할을 할당 합니다.

```azurecli
az role assignment create --assignee "55555555-5555-5555-5555-555555555555" \
--role "Storage Blob Data Contributor" \
--resource-group "Example-Storage-rg"
```

또는 매개 변수를 사용 하 여 정규화 된 리소스 그룹을 지정할 수 있습니다 `--scope` .

```azurecli
az role assignment create --assignee "55555555-5555-5555-5555-555555555555" \
--role "Storage Blob Data Contributor" \
--scope "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Example-Storage-rg"
```

### <a name="add-role-assignment-for-an-application-at-a-resource-group-scope"></a>리소스 그룹 범위에서 응용 프로그램에 대 한 역할 할당 추가

*Pharma-sales-projectforcast-sales* 리소스 그룹 범위에서 서비스 사용자 개체 ID가 44444444-4444-4444-4444-444444444444 인 응용 프로그램에 [가상 컴퓨터 참가자](built-in-roles.md#virtual-machine-contributor) 역할을 할당 합니다.

```azurecli
az role assignment create --assignee "44444444-4444-4444-4444-444444444444" \
--role "Virtual Machine Contributor" \
--resource-group "pharma-sales"
```

### <a name="add-role-assignment-for-a-new-service-principal-at-a-resource-group-scope"></a>리소스 그룹 범위에서 새 서비스 사용자에 대 한 역할 할당 추가

새 서비스 주체를 만들고 해당 서비스 주체에 역할을 즉시 할당하려고 하면 경우에 따라 해당 역할 할당이 실패할 수 있습니다. 예를 들어 스크립트를 사용 하 여 새로운 관리 되는 id를 만든 다음 해당 서비스 주체에 역할을 할당 하려고 하면 역할 할당이 실패할 수 있습니다. 이 오류가 발생하는 이유는 복제 지연 때문일 수 있습니다. 서비스 주체는 한 지역에 생성됩니다. 그러나 서비스 주체를 아직 복제하지 않은 다른 지역에서 역할 할당이 발생할 수 있습니다. 이 시나리오를 해결 하려면 역할 할당을 만들 때 보안 주체 유형을 지정 해야 합니다.

역할 할당을 추가 하려면 [az role 할당 create](/cli/azure/role/assignment#az_role_assignment_create)를 사용 하 고 값을 지정한 `--assignee-object-id` 다음 `--assignee-principal-type` 를로 설정 `ServicePrincipal` 합니다.

```azurecli
az role assignment create --assignee-object-id "{assigneeObjectId}" \
--assignee-principal-type "{assigneePrincipalType}" \
--role "{roleNameOrId}" \
--resource-group "{resourceGroupName}" \
--scope "/subscriptions/{subscriptionId}"
```

다음 예에서는 *pharma-sales-projectforcast* 리소스 그룹 범위에서 *msi 테스트* 관리 Id에 [가상 컴퓨터 참가자](built-in-roles.md#virtual-machine-contributor) 역할을 할당 합니다.

```azurecli
az role assignment create --assignee-object-id "33333333-3333-3333-3333-333333333333" \
--assignee-principal-type "ServicePrincipal" \
--role "Virtual Machine Contributor" \
--resource-group "pharma-sales"
```

### <a name="add-role-assignment-for-a-user-at-a-subscription-scope"></a>구독 범위에서 사용자에 대 한 역할 할당 추가

구독 범위에서 * \@ example.com* 사용자에 게 [읽기 권한자](built-in-roles.md#reader) 역할을 할당 합니다.

```azurecli
az role assignment create --assignee "annm@example.com" \
--role "Reader" \
--subscription "00000000-0000-0000-0000-000000000000"
```

### <a name="add-role-assignment-for-a-group-at-a-subscription-scope"></a>구독 범위에서 그룹에 대 한 역할 할당 추가

구독 범위에서 ID가 22222222-2222-2222-2222-222222222222 인 *Ann Mack 팀* 그룹에 [읽기 권한자](built-in-roles.md#reader) 역할을 할당 합니다.

```azurecli
az role assignment create --assignee "22222222-2222-2222-2222-222222222222" \
--role "Reader" \
--subscription "00000000-0000-0000-0000-000000000000"
```

### <a name="add-role-assignment-for-all-blob-containers-at-a-subscription-scope"></a>구독 범위에서 모든 blob 컨테이너에 대 한 역할 할당 추가

구독 범위에서 *alain \@ example.com* 사용자에 게 [저장소 Blob 데이터 판독기](built-in-roles.md#storage-blob-data-reader) 역할을 할당 합니다.

```azurecli
az role assignment create --assignee "alain@example.com" \
--role "Storage Blob Data Reader" \
--scope "/subscriptions/00000000-0000-0000-0000-000000000000"
```

### <a name="add-role-assignment-for-a-user-at-a-management-group-scope"></a>관리 그룹 범위에서 사용자에 대 한 역할 할당 추가

관리 그룹 범위에서 *alain \@ example.com* 사용자에 게 [청구 읽기 권한자](built-in-roles.md#billing-reader) 역할을 할당 합니다.

```azurecli
az role assignment create --assignee "alain@example.com" \
--role "Billing Reader" \
--scope "/providers/Microsoft.Management/managementGroups/marketing-group"
```

## <a name="remove-role-assignment"></a>역할 할당 제거

Azure RBAC에서 액세스 권한을 제거 하려면 [az role 할당 delete](/cli/azure/role/assignment#az_role_assignment_delete)를 사용 하 여 역할 할당을 제거 합니다.

다음 예에서는 *pharma-sales-projectforcast sales* 리소스 그룹의 *patlong \@ Contoso.com* 사용자에서 [가상 컴퓨터 참가자](built-in-roles.md#virtual-machine-contributor) 역할 할당을 제거 합니다.

```azurecli
az role assignment delete --assignee "patlong@contoso.com" \
--role "Virtual Machine Contributor" \
--resource-group "pharma-sales"
```

구독 범위에서 ID가 22222222-2222-2222-2222-222222222222 인 *Ann Mack 팀* 그룹에서 [판독기](built-in-roles.md#reader) 역할을 제거 합니다.

```azurecli
az role assignment delete --assignee "22222222-2222-2222-2222-222222222222" \
--role "Reader" \
--subscription "00000000-0000-0000-0000-000000000000"
```

관리 그룹 범위의 *alain \@ example.com* 사용자에서 [청구 읽기 권한자](built-in-roles.md#billing-reader) 역할을 제거 합니다.

```azurecli
az role assignment delete --assignee "alain@example.com" \
--role "Billing Reader" \
--scope "/providers/Microsoft.Management/managementGroups/marketing-group"
```

## <a name="next-steps"></a>다음 단계

- [Azure CLI를 사용 하 여 Azure 역할 할당 나열](role-assignments-list-cli.md)
- [Azure 리소스 및 리소스 그룹 관리를 위해 Azure CLI 사용](../azure-resource-manager/cli-azure-resource-manager.md)
