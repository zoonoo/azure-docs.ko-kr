---
title: 포함 파일
description: 포함 파일
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 03/05/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: cbd6f821326c86983ceb3ae5b90969e522c187fe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80343046"
---
[!INCLUDE [virtual-machines-disks-incremental-snapshots-description](virtual-machines-disks-incremental-snapshots-description.md)]

## <a name="restrictions"></a>제한

[!INCLUDE [virtual-machines-disks-incremental-snapshots-restrictions](virtual-machines-disks-incremental-snapshots-restrictions.md)]

## <a name="cli"></a>CLI

Azure CLI를 사용하여 증분 스냅숏을 만들 수 있으며 최신 버전의 Azure CLI가 필요합니다. 

Windows에서 다음 명령은 기존 설치를 최신 버전으로 설치하거나 업데이트합니다.
```PowerShell
Invoke-WebRequest -Uri https://aka.ms/installazurecliwindows -OutFile .\AzureCLI.msi; Start-Process msiexec.exe -Wait -ArgumentList '/I AzureCLI.msi /quiet'
```
Linux에서 CLI 설치는 운영 체제 버전에 따라 다릅니다.  특정 Linux 버전에 대한 [Azure CLI 설치를](https://docs.microsoft.com/cli/azure/install-azure-cli) 참조하십시오.

증분 스냅숏을 만들려면 매개 변수를 `--incremental` 사용하여 [az 스냅샷 만들기를](https://docs.microsoft.com/cli/azure/snapshot?view=azure-cli-latest#az-snapshot-create) 사용합니다.

다음 예제에서는 증분 스냅숏을 `<yourDesiredSnapShotNameHere>` `<yourResourceGroupNameHere>`만들고`<exampleDiskName>`, `<exampleLocation>` 을 대체하고, 고유의 값으로, 다음 예제를 실행합니다.

```bash
sourceResourceId=$(az disk show -g <yourResourceGroupNameHere> -n <exampleDiskName> --query '[id]' -o tsv)

az snapshot create -g <yourResourceGroupNameHere> \
-n <yourDesiredSnapShotNameHere> \
-l <exampleLocation> \
--source "$sourceResourceId" \
--incremental
```

스냅숏의 `SourceResourceId` `SourceUniqueId` 속성과 동일한 디스크에서 증분 스냅숏을 식별할 수 있습니다. `SourceResourceId`은 상위 디스크의 Azure 리소스 관리자 리소스 ID입니다. `SourceUniqueId`은 디스크 `UniqueId` 속성에서 상속된 값입니다. 디스크를 삭제한 다음 이름이 같은 새 디스크를 만들면 `UniqueId` 속성 값이 변경됩니다.

특정 `SourceResourceId` 디스크와 `SourceUniqueId` 연결된 모든 스냅숏 목록을 만들고 만들 수 있습니다. 다음 예제에서는 특정 디스크와 관련된 모든 증분 스냅숏을 나열하지만 일부 설정이 필요합니다.

이 예제에서는 jq를 사용하여 데이터를 쿼리합니다. 예제를 실행하려면 jq 를 [설치해야](https://stedolan.github.io/jq/download/)합니다.

`<exampleDiskName>` 다음 `<yourResourceGroupNameHere>` 예제를 사용하여 jq를 설치한 경우 다음 예제를 사용하여 기존 증분 스냅숏을 나열할 수 있습니다.

```bash
sourceUniqueId=$(az disk show -g <yourResourceGroupNameHere> -n <exampleDiskName> --query '[uniqueId]' -o tsv)

 
sourceResourceId=$(az disk show -g <yourResourceGroupNameHere> -n <exampleDiskName> --query '[id]' -o tsv)

az snapshot list -g <yourResourceGroupNameHere> -o json \
| jq -cr --arg SUID "$sourceUniqueId" --arg SRID "$sourceResourceId" '.[] | select(.incremental==true and .creationData.sourceUniqueId==$SUID and .creationData.sourceResourceId==$SRID)'
```

## <a name="resource-manager-template"></a>Resource Manager 템플릿

Azure 리소스 관리자 템플릿을 사용하여 증분 스냅숏을 만들 수도 있습니다. apiVersion이 **2019-03-01로** 설정되어 있고 증분 속성도 true로 설정되어 있는지 확인해야 합니다. 다음 코드 조각은 리소스 관리자 템플릿을 사용하여 증분 스냅숏을 만드는 방법의 예입니다.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "diskName": {
      "type": "string",
      "defaultValue": "contosodisk1"
    },
  "diskResourceId": {
    "defaultValue": "<your_managed_disk_resource_ID>",
    "type": "String"
  }
  }, 
  "resources": [
  {
    "type": "Microsoft.Compute/snapshots",
    "name": "[concat( parameters('diskName'),'_snapshot1')]",
    "location": "[resourceGroup().location]",
    "apiVersion": "2019-03-01",
    "properties": {
      "creationData": {
        "createOption": "Copy",
        "sourceResourceId": "[parameters('diskResourceId')]"
      },
      "incremental": true
    }
  }
  ]
}
```

## <a name="next-steps"></a>다음 단계

.NET을 사용하여 증분 스냅숏의 차등 기능을 보여 주는 샘플 코드를 보려면 [증분 스냅숏의 차동 기능을 사용하여 다른 지역으로 Azure 관리 디스크 백업 복사를](https://github.com/Azure-Samples/managed-disks-dotnet-backup-with-incremental-snapshots)참조하십시오.
