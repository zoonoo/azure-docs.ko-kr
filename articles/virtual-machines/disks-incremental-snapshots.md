---
title: 증분 스냅샷 만들기
description: Azure Portal, Azure PowerShell 모듈 및 Azure Resource Manager를 사용 하 여 생성 하는 방법을 비롯 하 여 관리 디스크의 증분 스냅숏에 대해 알아봅니다.
author: roygara
ms.service: virtual-machines
ms.topic: how-to
ms.date: 01/15/2021
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 52e491c88d3483f21aa74f1a9f176246033bee3c
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/23/2021
ms.locfileid: "98735795"
---
# <a name="create-an-incremental-snapshot-for-managed-disks"></a>관리 디스크에 대 한 증분 스냅숏 만들기

[!INCLUDE [virtual-machines-disks-incremental-snapshots-description](../../includes/virtual-machines-disks-incremental-snapshots-description.md)]

## <a name="restrictions"></a>제한

[!INCLUDE [virtual-machines-disks-incremental-snapshots-restrictions](../../includes/virtual-machines-disks-incremental-snapshots-restrictions.md)]


# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Azure PowerShell를 사용 하 여 증분 스냅숏을 만들 수 있습니다. 최신 버전의 Azure PowerShell 필요 하며, 다음 명령을 설치 하거나 기존 설치를 최신 버전으로 업데이트 합니다.

```PowerShell
Install-Module -Name Az -AllowClobber -Scope CurrentUser
```

이 설치 되 면를 사용 하 여 PowerShell 세션에 로그인 `Connect-AzAccount` 합니다.

Azure PowerShell를 사용 하 여 증분 스냅숏을 만들려면 구성을 [AzSnapShotConfig](/powershell/module/az.compute/new-azsnapshotconfig) 로 설정 하 고 매개 변수를 사용 하 여 `-Incremental` [AzSnapshot](/powershell/module/az.compute/new-azsnapshot) 를 변수로이를 변수로 전달 `-Snapshot` 합니다.

```PowerShell
$diskName = "yourDiskNameHere>"
$resourceGroupName = "yourResourceGroupNameHere"
$snapshotName = "yourDesiredSnapshotNameHere"

# Get the disk that you need to backup by creating an incremental snapshot
$yourDisk = Get-AzDisk -DiskName $diskName -ResourceGroupName $resourceGroupName

# Create an incremental snapshot by setting the SourceUri property with the value of the Id property of the disk
$snapshotConfig=New-AzSnapshotConfig -SourceUri $yourDisk.Id -Location $yourDisk.Location -CreateOption Copy -Incremental 
New-AzSnapshot -ResourceGroupName $resourceGroupName -SnapshotName $snapshotName -Snapshot $snapshotConfig 
```

`SourceResourceId`및 스냅숏의 속성을 사용 하 여 동일한 디스크에서 증분 스냅숏을 식별할 수 있습니다 `SourceUniqueId` . `SourceResourceId` 부모 디스크의 Azure Resource Manager 리소스 ID입니다. `SourceUniqueId` 는 디스크의 속성에서 상속 된 값입니다 `UniqueId` . 디스크를 삭제 한 다음 같은 이름으로 새 디스크를 만들면 속성의 값이 `UniqueId` 변경 됩니다.

`SourceResourceId`및 `SourceUniqueId` 를 사용 하 여 특정 디스크와 연결 된 모든 스냅숏의 목록을 만들 수 있습니다. 을 `<yourResourceGroupNameHere>` 값으로 바꾸고 다음 예를 사용 하 여 기존 증분 스냅숏을 나열할 수 있습니다.

```PowerShell
$snapshots = Get-AzSnapshot -ResourceGroupName $resourceGroupName

$incrementalSnapshots = New-Object System.Collections.ArrayList
foreach ($snapshot in $snapshots)
{
    
    if($snapshot.Incremental -and $snapshot.CreationData.SourceResourceId -eq $yourDisk.Id -and $snapshot.CreationData.SourceUniqueId -eq $yourDisk.UniqueId){

        $incrementalSnapshots.Add($snapshot)
    }
}

$incrementalSnapshots
```

# <a name="portal"></a>[포털](#tab/azure-portal)
[!INCLUDE [virtual-machines-disks-incremental-snapshots-portal](../../includes/virtual-machines-disks-incremental-snapshots-portal.md)]

# <a name="resource-manager-template"></a>[Resource Manager 템플릿](#tab/azure-resource-manager)

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
---

## <a name="next-steps"></a>다음 단계

.NET을 사용 하 여 증분 스냅숏의 차등 기능을 보여 주는 샘플 코드를 보려면 [증분 스냅숏의 차등 기능을 사용 하 여 다른 지역에 Azure Managed Disks 백업 복사](https://github.com/Azure-Samples/managed-disks-dotnet-backup-with-incremental-snapshots)를 참조 하세요.
