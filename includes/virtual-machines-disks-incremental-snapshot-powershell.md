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
ms.openlocfilehash: 3eec6583ebdff35d7e40d2eec305a947de0cb87c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79299461"
---
[!INCLUDE [virtual-machines-disks-incremental-snapshots-description](virtual-machines-disks-incremental-snapshots-description.md)]

### <a name="supported-regions"></a>지원되는 지역
[!INCLUDE [virtual-machines-disks-incremental-snapshots-regions](virtual-machines-disks-incremental-snapshots-regions.md)]

## <a name="restrictions"></a>제한

[!INCLUDE [virtual-machines-disks-incremental-snapshots-restrictions](virtual-machines-disks-incremental-snapshots-restrictions.md)]

## <a name="powershell"></a>PowerShell

Azure PowerShell을 사용하여 증분 스냅숏을 만들 수 있습니다. Azure PowerShell의 최신 버전이 필요하며 다음 명령은 해당 명령을 설치하거나 기존 설치를 최신 으로 업데이트합니다.

```PowerShell
Install-Module -Name Az -AllowClobber -Scope CurrentUser
```

설치가 완료되면 `az login`을 사용하여 PowerShell 세션에 로그인합니다.

Azure PowerShell을 사용하여 증분 스냅숏을 만들려면 `-Incremental` 매개 변수를 사용하여 [New-AzSnapShotConfig로](https://docs.microsoft.com/powershell/module/az.compute/new-azsnapshotconfig?view=azps-2.7.0) 구성을 설정한 다음 `-Snapshot` 매개 변수를 통해 [New-AzSnapshot에](https://docs.microsoft.com/powershell/module/az.compute/new-azsnapshot?view=azps-2.7.0) 변수로 전달합니다.

`<yourDiskNameHere>`을 `<yourResourceGroupNameHere>`대체하고 `<yourDesiredSnapShotNameHere>` 값으로 다음 스크립트를 사용하여 증분 스냅숏을 만들 수 있습니다.

```PowerShell
# Get the disk that you need to backup by creating an incremental snapshot
$yourDisk = Get-AzDisk -DiskName <yourDiskNameHere> -ResourceGroupName <yourResourceGroupNameHere>

# Create an incremental snapshot by setting the SourceUri property with the value of the Id property of the disk
$snapshotConfig=New-AzSnapshotConfig -SourceUri $yourDisk.Id -Location $yourDisk.Location -CreateOption Copy -Incremental 
New-AzSnapshot -ResourceGroupName <yourResourceGroupNameHere> -SnapshotName <yourDesiredSnapshotNameHere> -Snapshot $snapshotConfig 
```

스냅숏의 `SourceResourceId` `SourceUniqueId` 속성과 동일한 디스크에서 증분 스냅숏을 식별할 수 있습니다. `SourceResourceId`은 상위 디스크의 Azure 리소스 관리자 리소스 ID입니다. `SourceUniqueId`은 디스크 `UniqueId` 속성에서 상속된 값입니다. 디스크를 삭제한 다음 이름이 같은 새 디스크를 만들면 `UniqueId` 속성 값이 변경됩니다.

특정 `SourceResourceId` 디스크와 `SourceUniqueId` 연결된 모든 스냅숏 목록을 만들고 만들 수 있습니다. 값으로 바꾼 `<yourResourceGroupNameHere>` 다음 다음 예제를 사용하여 기존 증분 스냅숏을 나열할 수 있습니다.

```PowerShell
$snapshots = Get-AzSnapshot -ResourceGroupName <yourResourceGroupNameHere>

$incrementalSnapshots = New-Object System.Collections.ArrayList
foreach ($snapshot in $snapshots)
{
    
    if($snapshot.Incremental -and $snapshot.CreationData.SourceResourceId -eq $yourDisk.Id -and $snapshot.CreationData.SourceUniqueId -eq $yourDisk.UniqueId){

        $incrementalSnapshots.Add($snapshot)
    }
}

$incrementalSnapshots
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
