---
author: IngridAtMicrosoft
ms.service: media-services
ms.topic: include
ms.date: 08/17/2020
ms.author: inhenkel
ms.custom: CLI, devx-track-azurecli
ms.openlocfilehash: a79851e48e40b3c282d18c06e851f6c92d2391be
ms.sourcegitcommit: 17345cc21e7b14e3e31cbf920f191875bf3c5914
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/19/2021
ms.locfileid: "110103308"
---
<!-- ### Create a Reader role -->

다음 명령은 읽기 권한자 역할을 만듭니다. 

`assignee`를 `principalId`로 변경합니다. 이 명령은 리소스 그룹 및 스토리지 계정을 이미 만들었다고 가정합니다.  아래 명령과 같이 `your-resource-group-name`및 `your-storage-account-name`을 `scope` 값의 일부로 사용합니다.

```azurecli-interactive
az role assignment create --assignee 00000000-0000-0000-000000000000 --role "Reader" --scope "/subscriptions/00000000-0000-0000-000000000000/resourceGroups/your-resource-group-name/providers/Microsoft.Storage/storageAccounts/your-storage-account-name"
```

예제 JSON 응답:

```json
{
  "canDelegate": null,
  "condition": null,
  "conditionVersion": null,
  "description": null,
  "id": "/subscriptions/00000000-0000-0000-000000000000/resourceGroups/your-resource-group-name/providers/Microsoft.Storage/storageAccounts/your-storage-account-name/providers/Microsoft.Authorization/roleAssignments/00000000-0000-0000-000000000000",
  "name": "00000000-0000-0000-000000000000",
  "principalId": "00000000-0000-0000-000000000000",
  "principalType": "Reader",
  "resourceGroup": "your-resource-group-name",
  "roleDefinitionId": "/subscriptions/00000000-0000-0000-000000000000/providers/Microsoft.Authorization/roleDefinitions/00000000-0000-0000-000000000000",
  "scope": "/subscriptions/00000000-0000-0000-000000000000/resourceGroups/your-resource-group-name/providers/Microsoft.Storage/storageAccounts/your-storage-account-name",
  "type": "Microsoft.Authorization/roleAssignments"
}

```
