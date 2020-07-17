---
title: 파일 포함
description: 포함 파일
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 03/05/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: cbd6f821326c86983ceb3ae5b90969e522c187fe
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "82204604"
---
[!INCLUDE [virtual-machines-disks-incremental-snapshots-description](virtual-machines-disks-incremental-snapshots-description.md)]

## <a name="restrictions"></a>제한 사항

[!INCLUDE [virtual-machines-disks-incremental-snapshots-restrictions](virtual-machines-disks-incremental-snapshots-restrictions.md)]

## <a name="cli"></a>CLI

Azure CLI를 사용 하 여 증분 스냅숏을 만들 수 있습니다 Azure CLI 최신 버전의가 필요 합니다. 

Windows에서 다음 명령을 설치 하거나 기존 설치를 최신 버전으로 업데이트 합니다.
```PowerShell
Invoke-WebRequest -Uri https://aka.ms/installazurecliwindows -OutFile .\AzureCLI.msi; Start-Process msiexec.exe -Wait -ArgumentList '/I AzureCLI.msi /quiet'
```
Linux에서 CLI 설치는 운영 체제 버전에 따라 달라 집니다.  특정 Linux 버전에 대 한 [Azure CLI 설치를](https://docs.microsoft.com/cli/azure/install-azure-cli) 참조 하세요.

증분 스냅숏을 만들려면 [az snapshot create](https://docs.microsoft.com/cli/azure/snapshot?view=azure-cli-latest#az-snapshot-create) 를 `--incremental` 매개 변수와 함께 사용 합니다.

다음 예에서는 `<yourDesiredSnapShotNameHere>` ,, `<yourResourceGroupNameHere>` 및를 `<exampleDiskName>` `<exampleLocation>` 고유한 값으로 바꾼 다음 예제를 실행 하는 증분 스냅숏을 만듭니다.

```bash
sourceResourceId=$(az disk show -g <yourResourceGroupNameHere> -n <exampleDiskName> --query '[id]' -o tsv)

az snapshot create -g <yourResourceGroupNameHere> \
-n <yourDesiredSnapShotNameHere> \
-l <exampleLocation> \
--source "$sourceResourceId" \
--incremental
```

`SourceResourceId`및 스냅숏의 속성을 사용 하 여 동일한 디스크에서 증분 스냅숏을 식별할 수 있습니다 `SourceUniqueId` . `SourceResourceId`부모 디스크의 Azure Resource Manager 리소스 ID입니다. `SourceUniqueId`는 디스크의 속성에서 상속 된 값입니다 `UniqueId` . 디스크를 삭제 한 다음 같은 이름으로 새 디스크를 만들면 속성의 값이 `UniqueId` 변경 됩니다.

`SourceResourceId`및 `SourceUniqueId` 를 사용 하 여 특정 디스크와 연결 된 모든 스냅숏의 목록을 만들 수 있습니다. 다음 예제에는 특정 디스크와 연결 된 모든 증분 스냅숏이 나열 되지만 일부 설정이 필요 합니다.

이 예제에서는 데이터를 쿼리 하는 데 jq를 사용 합니다. 예제를 실행 하려면 [jq를 설치](https://stedolan.github.io/jq/download/)해야 합니다.

및을 사용자의 값으로 바꾸고 `<yourResourceGroupNameHere>` `<exampleDiskName>` jq도 설치한 경우 다음 예제를 사용 하 여 기존 증분 스냅숏을 나열할 수 있습니다.

```bash
sourceUniqueId=$(az disk show -g <yourResourceGroupNameHere> -n <exampleDiskName> --query '[uniqueId]' -o tsv)

 
sourceResourceId=$(az disk show -g <yourResourceGroupNameHere> -n <exampleDiskName> --query '[id]' -o tsv)

az snapshot list -g <yourResourceGroupNameHere> -o json \
| jq -cr --arg SUID "$sourceUniqueId" --arg SRID "$sourceResourceId" '.[] | select(.incremental==true and .creationData.sourceUniqueId==$SUID and .creationData.sourceResourceId==$SRID)'
```

## <a name="resource-manager-template"></a>Resource Manager 템플릿

Azure Resource Manager 템플릿을 사용 하 여 증분 스냅숏을 만들 수도 있습니다. ApiVersion가 **2019-03-01** 로 설정 되어 있고 증분 속성도 true로 설정 되어 있는지 확인 해야 합니다. 다음 코드 조각은 리소스 관리자 템플릿으로 증분 스냅숏을 만드는 방법의 예입니다.

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

.NET을 사용 하 여 증분 스냅숏의 차등 기능을 보여 주는 샘플 코드를 보려면 [증분 스냅숏의 차등 기능을 사용 하 여 다른 지역에 Azure Managed Disks 백업 복사](https://github.com/Azure-Samples/managed-disks-dotnet-backup-with-incremental-snapshots)를 참조 하세요.
