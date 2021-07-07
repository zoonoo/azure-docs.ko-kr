---
author: IngridAtMicrosoft
ms.service: media-services
ms.topic: include
ms.date: 08/17/2020
ms.author: inhenkel
ms.custom: CLI, devx-track-azurecli
ms.openlocfilehash: 6f6209eaee172ece5000d48c48b6cc38e4385017
ms.sourcegitcommit: 17345cc21e7b14e3e31cbf920f191875bf3c5914
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/19/2021
ms.locfileid: "110060112"
---
<!-- ### Create a storage account -->

다음 명령은 Media Services 계정과 연결될 스토리지 계정을 만듭니다. 

`your-storage-account-name`을 길이가 24자 미만인 고유한 이름으로 변경합니다. 이 명령은 리소스 그룹을 이미 만들었다고 가정합니다.  `your-resource-group-name`으로 해당 리소스 그룹 이름을 사용합니다. `your-region`으로 기본 설정 지역의 이름을 사용합니다.

```azurecli-interactive
az storage account create --name <your-storage-account-name> --kind StorageV2 --sku Standard_LRS -l <your-region> -g <your-resource-group-name>
```

예제 JSON 응답:

```json
{
  "accessTier": "Hot",
  "allowBlobPublicAccess": null,
  "azureFilesIdentityBasedAuthentication": null,
  "blobRestoreStatus": null,
  "creationTime": "2021-05-12T22:10:22.058640+00:00",
  "customDomain": null,
  "enableHttpsTrafficOnly": true,
  "encryption": {
    "keySource": "Microsoft.Storage",
    "keyVaultProperties": null,
    "requireInfrastructureEncryption": null,
    "services": {
      "blob": {
        "enabled": true,
        "keyType": "Account",
        "lastEnabledTime": "2021-05-12T22:10:22.152394+00:00"
      },
      "file": {
        "enabled": true,
        "keyType": "Account",
        "lastEnabledTime": "2021-05-12T22:10:22.152394+00:00"
      },
      "queue": null,
      "table": null
    }
  },
  "failoverInProgress": null,
  "geoReplicationStats": null,
  "id": "/subscriptions/00000000-0000-0000-000000000000/resourceGroups/your-resource-group-name/providers/Microsoft.Storage/storageAccounts/your-storage-account-name",
  "identity": null,
  "isHnsEnabled": null,
  "kind": "StorageV2",
  "largeFileSharesState": null,
  "lastGeoFailoverTime": null,
  "location": "your-region",
  "minimumTlsVersion": null,
  "name": "your-storage-account-name",
  "networkRuleSet": {
    "bypass": "AzureServices",
    "defaultAction": "Allow",
    "ipRules": [],
    "virtualNetworkRules": []
  },
  "primaryEndpoints": {
    "blob": "https://your-storage-account-name.blob.core.windows.net/",
    "dfs": "https://your-storage-account-name.dfs.core.windows.net/",
    "file": "https://your-storage-account-name.file.core.windows.net/",
    "internetEndpoints": null,
    "microsoftEndpoints": null,
    "queue": "https://your-storage-account-name.queue.core.windows.net/",
    "table": "https://your-storage-account-name.table.core.windows.net/",
    "web": "your-storage-account-name.z5.web.core.windows.net/"
  },
  "primaryLocation": "your-region",
  "privateEndpointConnections": [],
  "provisioningState": "Succeeded",
  "resourceGroup": "your-resource-group-name",
  "routingPreference": null,
  "secondaryEndpoints": null,
  "secondaryLocation": null,
  "sku": {
    "name": "Standard_LRS",
    "tier": "Standard"
  },
  "statusOfPrimary": "available",
  "statusOfSecondary": null,
  "tags": {},
  "type": "Microsoft.Storage/storageAccounts"
}
```