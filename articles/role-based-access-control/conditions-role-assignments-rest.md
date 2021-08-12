---
title: REST API(미리 보기)를 사용하여 Azure 역할 할당 조건 추가 또는 편집 - Azure RBAC
description: REST API 및 Azure RBAC(Azure 역할 기반 액세스 제어)를 사용하여 Azure 역할 할당에서 ABAC(특성 기반 액세스 제어) 조건을 추가, 편집, 나열 또는 삭제하는 방법에 대해 알아봅니다.
services: active-directory
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.subservice: conditions
ms.topic: how-to
ms.workload: identity
ms.date: 05/07/2021
ms.author: rolyon
ms.openlocfilehash: 4a929fd99f10e66ea07dacc0302dd6d0bba83a96
ms.sourcegitcommit: 3de22db010c5efa9e11cffd44a3715723c36696a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/10/2021
ms.locfileid: "109656563"
---
# <a name="add-or-edit-azure-role-assignment-conditions-using-the-rest-api-preview"></a>REST API(미리 보기)를 사용하여 Azure 역할 할당 조건 추가 또는 편집

> [!IMPORTANT]
> Azure ABAC 및 Azure 역할 할당 조건은 현재 미리 보기 상태입니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다.
> 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

[Azure 역할 할당 조건](conditions-overview.md)은 더 세분화된 액세스 제어를 제공하기 위해 선택적으로 역할 할당에 추가할 수 있는 추가 검사입니다. 예를 들어 개체를 읽을 특정 태그를 포함하는 개체를 필요로 하는 조건을 추가할 수 있습니다. 이 문서에서는 REST API를 사용하여 역할 할당에 대한 조건을 추가, 편집, 나열 또는 삭제하는 방법을 설명합니다.

## <a name="prerequisites"></a>필수 구성 요소

역할 할당 조건을 추가하거나 편집하기 위한 사전 요구 사항은 [조건 및 사전 요구 사항](conditions-prerequisites.md)을 참조하세요.

## <a name="add-a-condition"></a>조건 추가

역할 할당 조건을 추가하려면 [역할 할당 - 만들기](/rest/api/authorization/roleassignments/create) REST API를 사용합니다. `api-version`을 `2020-03-01-preview` 이상으로 설정합니다. 역할 할당에 `description` 속성을 활용하려면 `2020-04-01-preview` 이상을 사용합니다.  [역할 할당 - 만들기](/rest/api/authorization/roleassignments/create)에는 조건과 관련된 다음 매개 변수가 포함됩니다.

| 매개 변수 | Type | Description |
| --- | --- | --- |
| `condition` | String | 사용자에게 사용 권한을 부여할 수 있는 조건입니다. |
| `conditionVersion` | String | 조건 구문의 버전입니다. `conditionVersion` 없이 `condition`을 지정하면 버전이 기본값인 2.0으로 설정됩니다. |

다음 요청 및 본문을 사용합니다.

```http
PUT https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentId}?api-version=2020-04-01-preview
```

```json
{
    "properties": {
        "roleDefinitionId": "/{scope}/providers/Microsoft.Authorization/roleDefinitions/{roleDefinitionId}",
        "principalId": "{principalId}",
        "condition": "{condition}",
        "conditionVersion": "2.0",
        "description": "{description}"
    }
}
```

다음 예제에서는 조건을 사용하여 [스토리지 Blob 데이터 읽기 권한자](built-in-roles.md#storage-blob-data-reader) 역할을 할당하는 방법을 보여줍니다. 조건은 컨테이너 이름이 'blobs-example-container'와 같은지 여부를 확인합니다.

```http
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentId}?api-version=2020-04-01-preview
```

```json
{
    "properties": {
        "roleDefinitionId": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Authorization/roleDefinitions/2a2b9908-6ea1-4ae2-8e65-a410df84e7d1",
        "principalId": "{principalId}",
        "condition": "((!(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read'})) OR (@Resource[Microsoft.Storage/storageAccounts/blobServices/containers:name] StringEquals 'blobs-example-container'))",
        "conditionVersion": "2.0",
        "description": "Read access if container name equals blobs-example-container"
    }
}
```

다음은 출력 예제입니다.

```json
{
    "properties": {
        "roleDefinitionId": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Authorization/roleDefinitions/2a2b9908-6ea1-4ae2-8e65-a410df84e7d1",
        "principalId": "{principalId}",
        "principalType": "User",
        "scope": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}",
        "condition": "((!(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read'})) OR (@Resource[Microsoft.Storage/storageAccounts/blobServices/containers:name] StringEquals 'blobs-example-container'))",
        "conditionVersion": "2.0",
        "createdOn": "2021-04-20T06:20:44.0205560Z",
        "updatedOn": "2021-04-20T06:20:44.2955371Z",
        "createdBy": null,
        "updatedBy": "{updatedById}",
        "delegatedManagedIdentityResourceId": null,
        "description": "Read access if container name equals blobs-example-container"
    },
    "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentId}",
    "type": "Microsoft.Authorization/roleAssignments",
    "name": "{roleAssignmentId}"
}
```

## <a name="edit-a-condition"></a>조건 편집

기존 역할 할당 조건을 편집하려면 역할 할당 조건을 추가하는 데 사용한 것과 동일한 [역할 할당 - REST API 만들기](/rest/api/authorization/roleassignments/create)를 사용합니다. 다음은 `condition` 및 `description`이 업데이트되는 예제 JSON을 보여줍니다. `condition`, `conditionVersion` 및 `description` 속성만 편집할 수 있습니다. 기존 역할 할당과 일치하도록 다른 속성을 지정해야 합니다.

```http
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentId}?api-version=2020-04-01-preview
```

```json
{
    "properties": {
        "roleDefinitionId": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Authorization/roleDefinitions/2a2b9908-6ea1-4ae2-8e65-a410df84e7d1",
        "principalId": "{principalId}",
        "condition": "((!(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read'})) OR (@Resource[Microsoft.Storage/storageAccounts/blobServices/containers:name] StringEquals 'blobs-example-container' OR @Resource[Microsoft.Storage/storageAccounts/blobServices/containers:name] StringEquals 'blobs-example-container2'))",
        "conditionVersion": "2.0",
        "description": "Read access if container name equals blobs-example-container or blobs-example-container2"
    }
}
```


## <a name="list-a-condition"></a>조건 나열

역할 할당 조건을 나열하려면 [역할 할당 - 나열](/rest/api/authorization/roleassignments/list) API를 사용합니다. `api-version`을 `2020-03-01-preview` 이상으로 설정합니다. 역할 할당에 `description` 속성을 활용하려면 `2020-04-01-preview` 이상을 사용합니다. 자세한 내용은 [REST API를 사용하여 Azure 역할 할당 나열](role-assignments-list-rest.md)을 참조하세요.

## <a name="delete-a-condition"></a>조건 삭제

역할 할당 조건을 삭제하려면 역할 할당 조건을 편집하고 조건 및 조건 버전을 빈 문자열 또는 null로 설정합니다.

또는 역할 할당과 조건을 모두 삭제하려는 경우 [역할 할당 - 삭제 API](/rest/api/authorization/roleassignments/delete)를 사용할 수 있습니다. 자세한 내용은 [Azure 역할 할당 제거](role-assignments-remove.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

- [Azure 역할 할당 조건 예제(미리 보기)](../storage/common/storage-auth-abac-examples.md)
- [자습서: Azure Portal을 사용하여 Blob에 대한 액세스를 제한하는 역할 할당 조건 추가(미리 보기)](../storage/common/storage-auth-abac-portal.md)
- [Azure 역할 할당 조건 문제 해결(미리 보기)](conditions-troubleshoot.md)
