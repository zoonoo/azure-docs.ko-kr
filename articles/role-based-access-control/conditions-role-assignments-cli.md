---
title: Azure CLI(미리 보기)를 사용하여 Azure 역할 할당 조건 추가 또는 편집 - Azure RBAC
description: Azure CLI 및 Azure RBAC(Azure 역할 기반 액세스 제어)를 사용하여 Azure 역할 할당에서 ABAC(특성 기반 액세스 제어) 조건을 추가, 편집, 나열 또는 삭제하는 방법에 대해 알아봅니다.
services: active-directory
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.subservice: conditions
ms.topic: how-to
ms.workload: identity
ms.date: 05/07/2021
ms.author: rolyon
ms.openlocfilehash: c7baafbd34e9af488fbbee6237625aa469dff12c
ms.sourcegitcommit: 3de22db010c5efa9e11cffd44a3715723c36696a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/10/2021
ms.locfileid: "109656635"
---
# <a name="add-or-edit-azure-role-assignment-conditions-using-azure-cli-preview"></a>Azure CLI(미리 보기)를 사용하여 Azure 역할 할당 조건 추가 또는 편집

> [!IMPORTANT]
> Azure ABAC 및 Azure 역할 할당 조건은 현재 미리 보기 상태입니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다.
> 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

[Azure 역할 할당 조건](conditions-overview.md)은 더 세분화된 액세스 제어를 제공하기 위해 선택적으로 역할 할당에 추가할 수 있는 추가 검사입니다. 예를 들어 개체를 읽을 특정 태그를 포함하는 개체를 필요로 하는 조건을 추가할 수 있습니다. 이 문서에서는 Azure CLI를 사용하여 역할 할당에 대한 조건을 추가, 편집, 나열 또는 삭제하는 방법을 설명합니다.

## <a name="prerequisites"></a>필수 구성 요소

역할 할당 조건을 추가하거나 편집하기 위한 사전 요구 사항은 [조건 및 사전 요구 사항](conditions-prerequisites.md)을 참조하세요.

## <a name="add-a-condition"></a>조건 추가

역할 할당 조건을 추가하려면 [az role assignment create](/cli/azure/role/assignment#az_role_assignment_create)를 사용합니다. [az role assignment create](/cli/azure/role/assignment#az_role_assignment_create) 명령에는 조건과 관련된 다음 매개 변수가 포함됩니다.

| 매개 변수 | Type | Description |
| --- | --- | --- |
| `condition` | String | 사용자에게 사용 권한을 부여할 수 있는 조건입니다. |
| `condition-version` | String | 조건 구문의 버전입니다. `--condition-version` 없이 `--condition`을 지정하면 버전이 기본값인 2.0으로 설정됩니다. |

다음 예제에서는 조건을 사용하여 [스토리지 Blob 데이터 읽기 권한자](built-in-roles.md#storage-blob-data-reader) 역할을 할당하는 방법을 보여 줍니다. 조건은 컨테이너 이름이 'blobs-example-container'와 같은지 여부를 확인합니다.

```azurecli
az role assignment create --role "Storage Blob Data Reader" --assignee "user1@contoso.com" --resource-group {resourceGroup} \
--description "Read access if container name equals blobs-example-container" \
--condition "((!(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read'})) OR (@Resource[Microsoft.Storage/storageAccounts/blobServices/containers:name] StringEquals 'blobs-example-container'))" \
--condition-version "2.0"
```

다음은 출력 예제입니다.

```azurecli
{
    "canDelegate": null,
    "condition": "((!(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read'})) OR (@Resource[Microsoft.Storage/storageAccounts/blobServices/containers:name] StringEquals 'blobs-example-container'))",
    "conditionVersion": "2.0",
    "description": "Read access if container name equals blobs-example-container",
    "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentId}",
    "name": "{roleAssignmentId}",
    "principalId": "{userObjectId}",
    "principalType": "User",
    "resourceGroup": "{resourceGroup}",
    "roleDefinitionId": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/2a2b9908-6ea1-4ae2-8e65-a410df84e7d1",
    "scope": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}",
    "type": "Microsoft.Authorization/roleAssignments"
}
```

## <a name="edit-a-condition"></a>조건 편집

기존 역할 할당 조건을 편집하려면 [az role assignment update](/cli/azure/role/assignment#az_role_assignment_update) 및 JSON 파일을 입력으로 사용합니다. 다음은 조건 및 설명이 업데이트되는 예제 JSON 파일을 보여 줍니다. `condition`, `conditionVersion` 및 `description` 속성만 편집할 수 있습니다. 역할 할당 조건을 업데이트하려면 모든 속성을 지정해야 합니다.

```json
{
    "canDelegate": null,
    "condition": "((!(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read'})) OR (@Resource[Microsoft.Storage/storageAccounts/blobServices/containers:name] StringEquals 'blobs-example-container' OR @Resource[Microsoft.Storage/storageAccounts/blobServices/containers:name] StringEquals 'blobs-example-container2'))",
    "conditionVersion": "2.0",
    "description": "Read access if container name equals blobs-example-container or blobs-example-container2",
    "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentId}",
    "name": "{roleAssignmentId}",
    "principalId": "{userObjectId}",
    "principalType": "User",
    "resourceGroup": "{resourceGroup}",
    "roleDefinitionId": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/2a2b9908-6ea1-4ae2-8e65-a410df84e7d1",
    "scope": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}",
    "type": "Microsoft.Authorization/roleAssignments"
}
```

[az role assignment update](/cli/azure/role/assignment#az_role_assignment_update)를 사용하여 역할 할당에 대한 조건을 업데이트합니다.

```azurecli
az role assignment update --role-assignment "./path/roleassignment.json"
```

다음은 출력 예제입니다.

```azurecli
{
    "canDelegate": null,
    "condition": "((!(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read'})) OR (@Resource[Microsoft.Storage/storageAccounts/blobServices/containers:name] StringEquals 'blobs-example-container' OR @Resource[Microsoft.Storage/storageAccounts/blobServices/containers:name] StringEquals 'blobs-example-container2'))",
    "conditionVersion": "2.0",
    "description": "Read access if container name equals blobs-example-container or blobs-example-container2",
    "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentId}",
    "name": "{roleAssignmentId}",
    "principalId": "{userObjectId}",
    "principalType": "User",
    "resourceGroup": "{resourceGroup}",
    "roleDefinitionId": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/2a2b9908-6ea1-4ae2-8e65-a410df84e7d1",
    "scope": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}",
    "type": "Microsoft.Authorization/roleAssignments"
}
```

## <a name="list-a-condition"></a>조건 나열

역할 할당 조건을 나열하려면 [az role assignment list](/cli/azure/role/assignment#az_role_assignment_list)를 사용합니다. 자세한 내용은 [Azure CLI를 사용하여 Azure 역할 할당 나열](role-assignments-list-cli.md)을 참조하세요.

## <a name="delete-a-condition"></a>조건 삭제

역할 할당 조건을 삭제하려면 역할 할당 조건을 편집하고 `condition` 및 `condition-version` 속성을 빈 문자열(`""`) 또는 `null`로 설정합니다.

또는 역할 할당과 조건을 모두 삭제하려는 경우 [az role assignment delete](/cli/azure/role/assignment#az_role_assignment_delete) 명령을 사용할 수 있습니다. 자세한 내용은 [Azure 역할 할당 제거](role-assignments-remove.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

- [Azure 역할 할당 조건 예제(미리 보기)](../storage/common/storage-auth-abac-examples.md)
- [자습서: Azure CLI를 사용하여 Blob에 대한 액세스를 제한하는 역할 할당 조건 추가(미리 보기)](../storage/common/storage-auth-abac-cli.md)
- [Azure 역할 할당 조건 문제 해결(미리 보기)](conditions-troubleshoot.md)
