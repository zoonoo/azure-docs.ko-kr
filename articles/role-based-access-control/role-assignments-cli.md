---
title: Azure RBAC 및 Azure CLI를 사용 하 여 역할 할당 추가 또는 제거
description: Azure RBAC (역할 기반 액세스 제어) 및 Azure CLI를 사용 하 여 사용자, 그룹, 서비스 주체 또는 관리 되는 id에 대 한 Azure 리소스에 대 한 액세스 권한을 부여 하는 방법을 알아봅니다.
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
ms.date: 11/25/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: b32df50715d5e7276861e0696df1bd6ceb3f684e
ms.sourcegitcommit: 64def2a06d4004343ec3396e7c600af6af5b12bb
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/19/2020
ms.locfileid: "77471995"
---
# <a name="add-or-remove-role-assignments-using-azure-rbac-and-azure-cli"></a>Azure RBAC 및 Azure CLI를 사용 하 여 역할 할당 추가 또는 제거

이 문서 [!INCLUDE [Azure RBAC definition grant access](../../includes/role-based-access-control-definition-grant.md)] Azure CLI를 사용 하 여 역할을 할당 하는 방법을 설명 합니다.

## <a name="prerequisites"></a>사전 요구 사항

역할 할당을 추가 하거나 제거 하려면 다음을 수행 해야 합니다.

- `Microsoft.Authorization/roleAssignments/write` 및 `Microsoft.Authorization/roleAssignments/delete` 사용 권한(예: [사용자 액세스 관리자](built-in-roles.md#user-access-administrator) 또는 [소유자](built-in-roles.md#owner))
- Azure Cloud Shell 또는 [Azure CLI](/cli/azure) [의 Bash](/azure/cloud-shell/overview)

## <a name="get-object-ids"></a>개체 Id 가져오기

역할 할당을 추가 하거나 제거 하려면 개체의 고유 ID를 지정 해야 할 수 있습니다. ID의 형식은 `11111111-1111-1111-1111-111111111111`입니다. Azure Portal 또는 Azure CLI를 사용 하 여 ID를 가져올 수 있습니다.

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

## <a name="add-a-role-assignment"></a>역할 할당 추가

RBAC에서 액세스 권한을 부여 하려면 역할 할당을 추가 합니다.

### <a name="user-at-a-resource-group-scope"></a>리소스 그룹 범위의 사용자

리소스 그룹 범위에서 사용자에 대 한 역할 할당을 추가 하려면 [az role 대입문 create](/cli/azure/role/assignment#az-role-assignment-create)를 사용 합니다.

```azurecli
az role assignment create --role <role_name_or_id> --assignee <assignee> --resource-group <resource_group>
```

다음 예에서는 *pharma-sales-projectforcast-sales* 리소스 그룹 범위에서 *patlong\@contoso.com* 사용자에 게 *가상 컴퓨터 참가자* 역할을 할당 합니다.

```azurecli
az role assignment create --role "Virtual Machine Contributor" --assignee patlong@contoso.com --resource-group pharma-sales
```

### <a name="using-the-unique-role-id"></a>고유한 역할 ID 사용

역할 이름이 변경 될 수 있는 몇 가지 경우가 있습니다. 예를 들면 다음과 같습니다.

- 사용자 고유의 사용자 지정 역할을 사용 하 고 있으며 이름을 변경 하기로 결정 했습니다.
- 이름에 **(미리 보기)** 가 있는 미리 보기 역할을 사용 하 고 있습니다. 역할을 해제 하면 역할의 이름이 바뀝니다.

> [!IMPORTANT]
> 미리 보기 버전은 서비스 수준 계약 없이 제공 되며 프로덕션 워크 로드에는 권장 되지 않습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다.
> 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

역할의 이름이 바뀐 경우에도 역할 ID는 변경 되지 않습니다. 스크립트 또는 자동화를 사용 하 여 역할 할당을 만드는 경우 역할 이름 대신 고유한 역할 ID를 사용 하는 것이 좋습니다. 따라서 역할의 이름을 바꾸면 스크립트가 작동할 가능성이 높습니다.

역할 이름 대신 고유한 역할 ID를 사용 하 여 역할 할당을 추가 하려면 [az role 대입문 create](/cli/azure/role/assignment#az-role-assignment-create)를 사용 합니다.

```azurecli
az role assignment create --role <role_id> --assignee <assignee> --resource-group <resource_group>
```

다음 예에서는 *pharma-sales-projectforcast-sales* 리소스 그룹 범위에서 *patlong\@contoso.com* 사용자에 게 [가상 컴퓨터 참가자](built-in-roles.md#virtual-machine-contributor) 역할을 할당 합니다. 고유한 역할 ID를 가져오기 위해 [az role definition list](/cli/azure/role/definition#az-role-definition-list) 를 사용 하거나 [Azure 리소스에 대 한 기본 제공 역할](built-in-roles.md)을 볼 수 있습니다.

```azurecli
az role assignment create --role 9980e02c-c2be-4d73-94e8-173b1dc7cf3c --assignee patlong@contoso.com --resource-group pharma-sales
```

### <a name="group-at-a-subscription-scope"></a>구독 범위에서 그룹

그룹에 대 한 역할 할당을 추가 하려면 [az role 대입문 create](/cli/azure/role/assignment#az-role-assignment-create)를 사용 합니다. 그룹의 개체 ID를 가져오는 방법에 대 한 자세한 내용은 [개체 Id 가져오기](#get-object-ids)를 참조 하세요.

```azurecli
az role assignment create --role <role_name_or_id> --assignee-object-id <assignee_object_id> --resource-group <resource_group> --scope </subscriptions/subscription_id>
```

다음 예에서는 구독 범위에서 ID가 22222222-2222-2222-2222-222222222222 인 *Ann Mack 팀* 그룹에 *읽기 권한자* 역할을 할당 합니다.

```azurecli
az role assignment create --role Reader --assignee-object-id 22222222-2222-2222-2222-222222222222 --scope /subscriptions/00000000-0000-0000-0000-000000000000
```

### <a name="group-at-a-resource-scope"></a>리소스 범위의 그룹

그룹에 대 한 역할 할당을 추가 하려면 [az role 대입문 create](/cli/azure/role/assignment#az-role-assignment-create)를 사용 합니다. 그룹의 개체 ID를 가져오는 방법에 대 한 자세한 내용은 [개체 Id 가져오기](#get-object-ids)를 참조 하세요.

다음 예제에서는 *pharma-sales-projectforcast*이라는 가상 네트워크에 대 한 리소스 범위에서 ID가 22222222-2222-2222-2222-222222222222 인 *Ann mack 팀* 그룹에 *가상 컴퓨터 참가자* 역할을 할당 합니다.

```azurecli
az role assignment create --role "Virtual Machine Contributor" --assignee-object-id 22222222-2222-2222-2222-222222222222 --scope /subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/pharma-sales/providers/Microsoft.Network/virtualNetworks/pharma-sales-project-network
```

### <a name="application-at-a-resource-group-scope"></a>리소스 그룹 범위의 응용 프로그램

응용 프로그램에 대 한 역할 할당을 추가 하려면 [az role 대입문 create](/cli/azure/role/assignment#az-role-assignment-create)를 사용 합니다. 응용 프로그램의 개체 ID를 가져오는 방법에 대 한 자세한 내용은 [개체 Id 가져오기](#get-object-ids)를 참조 하세요.

```azurecli
az role assignment create --role <role_name_or_id> --assignee-object-id <assignee_object_id> --resource-group <resource_group>
```

다음 예에서는 *pharma-sales-projectforcast* 리소스 그룹 범위에서 개체 ID가 44444444-4444-4444-4444-444444444444 인 응용 프로그램에 *가상 컴퓨터 참가자* 역할을 할당 합니다.

```azurecli
az role assignment create --role "Virtual Machine Contributor" --assignee-object-id 44444444-4444-4444-4444-444444444444 --resource-group pharma-sales
```

### <a name="user-at-a-subscription-scope"></a>구독 범위에서 사용자

구독 범위에서 사용자에 대 한 역할 할당을 추가 하려면 [az role 대입문 create](/cli/azure/role/assignment#az-role-assignment-create)를 사용 합니다. 구독 ID를 가져오려면 Azure Portal의 **구독** 블레이드에서 찾거나 [az account list](/cli/azure/account#az-account-list)를 사용할 수 있습니다.

```azurecli
az role assignment create --role <role_name_or_id> --assignee <assignee> --subscription <subscription_name_or_id>
```

다음 예에서는 구독 범위에서 *\@example.com* 사용자에 게 *독자* 역할을 할당 합니다.

```azurecli
az role assignment create --role "Reader" --assignee annm@example.com --subscription 00000000-0000-0000-0000-000000000000
```

### <a name="user-at-a-management-group-scope"></a>관리 그룹 범위에 있는 사용자

관리 그룹 범위에서 사용자에 대 한 역할 할당을 추가 하려면 [az role 할당 create](/cli/azure/role/assignment#az-role-assignment-create)를 사용 합니다. 관리 그룹 ID를 가져오려면 Azure Portal의 **관리 그룹** 블레이드에서 찾거나 [az account management-group list](/cli/azure/account/management-group#az-account-management-group-list)를 사용할 수 있습니다.

```azurecli
az role assignment create --role <role_name_or_id> --assignee <assignee> --scope /providers/Microsoft.Management/managementGroups/<group_id>
```

다음 예에서는 관리 그룹 범위에서 *alain\@example.com* 사용자에 게 *청구 읽기 권한자* 역할을 할당 합니다.

```azurecli
az role assignment create --role "Billing Reader" --assignee alain@example.com --scope /providers/Microsoft.Management/managementGroups/marketing-group
```

### <a name="new-service-principal"></a>새 서비스 사용자

새 서비스 주체를 만들고 해당 서비스 주체에 역할을 즉시 할당 하려고 하면 경우에 따라 해당 역할 할당이 실패할 수 있습니다. 예를 들어 스크립트를 사용 하 여 새로운 관리 되는 id를 만든 다음 해당 서비스 주체에 역할을 할당 하려고 하면 역할 할당이 실패할 수 있습니다. 이 오류가 발생 하는 이유는 복제 지연 일 수 있습니다. 서비스 사용자는 한 지역에 생성 됩니다. 그러나 서비스 사용자를 아직 복제 하지 않은 다른 지역에서 역할 할당이 발생할 수 있습니다. 이 시나리오를 해결 하려면 역할 할당을 만들 때 보안 주체 유형을 지정 해야 합니다.

역할 할당을 추가 하려면 [az role 할당 create](/cli/azure/role/assignment#az-role-assignment-create)를 사용 하 고 `--assignee-object-id`값을 지정한 다음 `--assignee-principal-type`를 `ServicePrincipal`로 설정 합니다.

```azurecli
az role assignment create --role <role_name_or_id> --assignee-object-id <assignee_object_id> --assignee-principal-type <assignee_principal_type> --resource-group <resource_group> --scope </subscriptions/subscription_id>
```

다음 예에서는 *pharma-sales-projectforcast* 리소스 그룹 범위에서 *msi 테스트* 관리 Id에 *가상 컴퓨터 참가자* 역할을 할당 합니다.

```azurecli
az role assignment create --role "Virtual Machine Contributor" --assignee-object-id 33333333-3333-3333-3333-333333333333 --assignee-principal-type ServicePrincipal --resource-group pharma-sales
```

## <a name="remove-a-role-assignment"></a>역할 할당 제거

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

다음 예에서는 관리 그룹 범위에 있는 *alain\@example.com* 사용자에서 *청구 읽기 권한자* 역할을 제거 합니다. 관리 그룹의 ID를 가져오려면 [az account management-group list](/cli/azure/account/management-group#az-account-management-group-list)를 사용할 수 있습니다.

```azurecli
az role assignment delete --assignee alain@example.com --role "Billing Reader" --scope /providers/Microsoft.Management/managementGroups/marketing-group
```

## <a name="next-steps"></a>다음 단계

- [Azure RBAC 및 Azure CLI를 사용 하 여 역할 할당 나열](role-assignments-list-cli.md)
- [Azure 리소스 및 리소스 그룹 관리를 위해 Azure CLI 사용](../azure-resource-manager/cli-azure-resource-manager.md)
