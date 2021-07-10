---
author: IngridAtMicrosoft
ms.service: media-services
ms.topic: include
ms.date: 08/17/2020
ms.author: inhenkel
ms.custom: CLI, devx-track-azurecli
ms.openlocfilehash: 72dc27bf1600d298d41a034b081b216d3d77c94c
ms.sourcegitcommit: 17345cc21e7b14e3e31cbf920f191875bf3c5914
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/19/2021
ms.locfileid: "110103247"
---
<!--Create a media services account -->

다음 Azure CLI 명령은 새 Media Services 계정을 만듭니다. `your-media-services-account-name`  `your-storage-account-name` 및 `your-resource-group-name` 값을 사용하려는 이름으로 바꿉니다. 이 명령은 리소스 그룹 및 스토리지 계정을 이미 만들었다고 가정합니다. 

Media Services 계정에 `--mi-system-assigned` 플래그가 있는 시스템 할당 관리 ID를 제공합니다.

```azurecli-interactive

az ams account create --name <your-media-services-account-name> --resource-group <your-resource-group-name> --mi-system-assigned --storage-account <your-storage-account-name>

```

예제 JSON 응답:

```json
{
  "encryption": {
    "keyVaultProperties": null,
    "type": "SystemKey"
  },
  "id": "/subscriptions/00000000-0000-0000-0000-00000000/resourceGroups/your-resource-group/providers/Microsoft.Media/mediaservices/your-media-services-account-name",
  "identity": {
    "principalId": "00000000-0000-0000-0000-00000000",
    "tenantId": "00000000-0000-0000-0000-00000000",
    "type": "SystemAssigned"
  },
  "location": "your-region",
  "mediaServiceId": "00000000-0000-0000-0000-00000000",
  "name": "your-media-services-account-name",
  "resourceGroup": "your-resource-group",
  "storageAccounts": [
    {
      "id": "/subscriptions/00000000-0000-0000-0000-00000000/resourceGroups/mediatest1rg/providers/Microsoft.Storage/storageAccounts/your-storage-account-name",
      "resourceGroup": "your-resource-group",
      "type": "Primary"
    }
  ],
  "storageAuthentication": "System",
  "systemData": {
    "createdAt": "2021-05-14T21:25:12.3492071Z",
    "createdBy": "you@example.com",
    "createdByType": "User",
    "lastModifiedAt": "2021-05-14T21:25:12.3492071Z",
    "lastModifiedBy": "you@example.com",
    "lastModifiedByType": "User"
  },
  "tags": null,
  "type": "Microsoft.Media/mediaservices"
}
```
