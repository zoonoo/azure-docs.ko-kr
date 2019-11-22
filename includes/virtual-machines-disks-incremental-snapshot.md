---
title: 포함 파일
description: 포함 파일
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 09/23/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 846fd92bce3056dc119f38ac253a0a937e8c56a4
ms.sourcegitcommit: 8a2949267c913b0e332ff8675bcdfc049029b64b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/21/2019
ms.locfileid: "74309819"
---
증분 스냅숏 (미리 보기)은 마지막 스냅숏 이후의 모든 변경 내용 으로만 구성 된 관리 디스크의 지정 시간 백업입니다. 증분 스냅숏을 다운로드 하거나 사용 하지 않으면 전체 VHD가 사용 됩니다. 관리 디스크 스냅숏에 대 한 이러한 새로운 기능을 사용 하면 필요한 경우를 제외 하 고 각 개별 스냅숏으로 전체 디스크를 저장 하는 것이 더 이상 필요 하지 않으므로 관리 디스크 스냅숏에 대 한 이러한 새로운 기능을 사용 하는 것이 더 비용 효율적입니다. 일반 스냅숏과 마찬가지로 증분 스냅숏은 전체 관리 디스크를 만드는 데 사용 하거나 일반 스냅숏을 만드는 데 사용할 수 있습니다.

증분 스냅숏과 일반 스냅숏 간에는 몇 가지 차이점이 있습니다. 증분 스냅숏은 디스크의 저장소 유형과 관계 없이 항상 표준 Hdd 저장소를 사용 하는 반면, 일반 스냅숏은 프리미엄 Ssd를 사용할 수 있습니다. Premium Storage에서 정기적인 스냅숏을 사용 하 여 VM 배포를 강화 하는 경우 [공유 이미지 갤러리](../articles/virtual-machines/linux/shared-image-galleries.md)의 standard Storage에서 사용자 지정 이미지를 사용 하는 것이 좋습니다. 이를 통해 더 저렴 한 비용으로 더 광범위 한 규모를 달성할 수 있습니다. 또한 증분 스냅숏은 ZRS ( [영역 중복 저장소](../articles/storage/common/storage-redundancy-zrs.md) )를 사용 하 여 더 높은 안정성을 제공할 수 있습니다. 선택한 지역에서 ZRS을 사용할 수 있는 경우 증분 스냅숏은 ZRS을 자동으로 사용 합니다. ZRS를 지역에서 사용할 수 없는 경우 스냅숏은 기본적으로 LRS ( [로컬 중복 저장소](../articles/storage/common/storage-redundancy-lrs.md) )로 사용 됩니다. 이 동작을 재정의 하 고 하나를 수동으로 선택할 수 있지만 권장 되지는 않습니다.

또한 증분 스냅숏은 관리 디스크에 고유 하 게 사용할 수 있는 차등 기능을 제공 합니다. 이를 통해 동일한 관리 디스크의 두 증분 스냅숏 간의 변경 내용을 블록 수준까지 가져올 수 있습니다. 이 기능을 사용 하 여 지역 간에 스냅숏을 복사할 때 데이터 공간을 줄일 수 있습니다.

아직 미리 보기에 등록 하지 않았고 증분 스냅숏 사용을 시작 하려는 경우 공개 미리 보기에 대 한 액세스 권한을 얻기 위해 AzureDisks@microsoft.com 전자 메일을 보내 주시기 바랍니다.

## <a name="restrictions"></a>제한

- 증분 스냅숏은 현재 미국 중 북부와 유럽 서 부 에서만 사용할 수 있습니다.
- 디스크 크기를 변경한 후에는 현재 증분 스냅숏을 만들 수 없습니다.
- 현재 증분 스냅숏은 구독 간에 이동할 수 없습니다.
- 현재는 특정 시간에 특정 스냅숏 패밀리의 최대 5 개 스냅숏의 SAS Uri만 생성할 수 있습니다.
- 해당 디스크의 구독 외부에 있는 특정 디스크에 대해 증분 스냅숏을 만들 수는 없습니다.
- 디스크당 최대 7 개의 증분 스냅숏이 5 분 마다 만들어질 수 있습니다.
- 단일 디스크에 대해 총 200 증분 스냅숏을 만들 수 있습니다.

## <a name="powershell"></a>PowerShell

Azure PowerShell를 사용 하 여 증분 스냅숏을 만들 수 있습니다. 최신 버전의 Azure PowerShell 필요 하며, 다음 명령을 설치 하거나 기존 설치를 최신 버전으로 업데이트 합니다.

```PowerShell
Install-Module -Name Az -AllowClobber -Scope CurrentUser
```

설치가 완료 되 면 `az login`를 사용 하 여 PowerShell 세션에 로그인 합니다.

Azure PowerShell를 사용 하 여 증분 스냅숏을 만들려면 `-Incremental` 매개 변수를 사용 하 여 [AzSnapShotConfig](https://docs.microsoft.com/powershell/module/az.compute/new-azsnapshotconfig?view=azps-2.7.0) 로 구성을 설정한 다음 `-Snapshot` 매개 변수를 통해 [AzSnapshot](https://docs.microsoft.com/powershell/module/az.compute/new-azsnapshot?view=azps-2.7.0) 에 변수로 전달 합니다.

`<yourDiskNameHere>`, `<yourResourceGroupNameHere>`및 `<yourDesiredSnapShotNameHere>`를 값으로 바꾸고 다음 스크립트를 사용 하 여 증분 스냅숏을 만들 수 있습니다.

```PowerShell
# Get the disk that you need to backup by creating an incremental snapshot
$yourDisk = Get-AzDisk -DiskName <yourDiskNameHere> -ResourceGroupName <yourResourceGroupNameHere>

# Create an incremental snapshot by setting the SourceUri property with the value of the Id property of the disk
$snapshotConfig=New-AzSnapshotConfig -SourceUri $yourDisk.Id -Location $yourDisk.Location -CreateOption Copy -Incremental 
New-AzSnapshot -ResourceGroupName <yourResourceGroupNameHere> -SnapshotName <yourDesiredSnapshotNameHere> -Snapshot $snapshotConfig 
```

`SourceResourceId` 및 스냅숏의 `SourceUniqueId` 속성을 사용 하 여 동일한 디스크에서 증분 스냅숏을 식별할 수 있습니다. `SourceResourceId`는 부모 디스크의 Azure Resource Manager 리소스 ID입니다. `SourceUniqueId`은 디스크의 `UniqueId` 속성에서 상속 된 값입니다. 디스크를 삭제 하 고 동일한 이름으로 새 디스크를 만드는 경우 `UniqueId` 속성의 값이 변경 됩니다.

`SourceResourceId` 및 `SourceUniqueId`를 사용 하 여 특정 디스크와 연결 된 모든 스냅숏의 목록을 만들 수 있습니다. `<yourResourceGroupNameHere>`를 값으로 바꾸고 다음 예를 사용 하 여 기존 증분 스냅숏을 나열할 수 있습니다.

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

## <a name="cli"></a>CLI

Azure CLI를 사용 하 여 증분 스냅숏을 만들 수 있습니다 Azure CLI 최신 버전의가 필요 합니다. 

Windows에서 다음 명령을 설치 하거나 기존 설치를 최신 버전으로 업데이트 합니다.
```PowerShell
Invoke-WebRequest -Uri https://aka.ms/installazurecliwindows -OutFile .\AzureCLI.msi; Start-Process msiexec.exe -Wait -ArgumentList '/I AzureCLI.msi /quiet'
```
Linux에서 CLI 설치는 운영 체제 버전에 따라 달라 집니다.  특정 Linux 버전에 대 한 [Azure CLI 설치를](https://docs.microsoft.com/cli/azure/install-azure-cli) 참조 하세요.

증분 스냅숏을 만들려면 [az snapshot create](https://docs.microsoft.com/cli/azure/snapshot?view=azure-cli-latest#az-snapshot-create) 를 `--incremental` 매개 변수와 함께 사용 합니다.

다음 예에서는 증분 스냅숏을 만들고 `<yourDesiredSnapShotNameHere>`, `<yourResourceGroupNameHere>`,`<exampleDiskName>`및 `<exampleLocation>`를 고유한 값으로 바꾼 다음 예제를 실행 합니다.

```bash
sourceResourceId=$(az disk show -g <yourResourceGroupNameHere> -n <exampleDiskName> --query '[id]' -o tsv)

az snapshot create -g <yourResourceGroupNameHere> \
-n <yourDesiredSnapShotNameHere> \
-l <exampleLocation> \
--source "$sourceResourceId" \
--incremental
```

`SourceResourceId` 및 스냅숏의 `SourceUniqueId` 속성을 사용 하 여 동일한 디스크에서 증분 스냅숏을 식별할 수 있습니다. `SourceResourceId`는 부모 디스크의 Azure Resource Manager 리소스 ID입니다. `SourceUniqueId`은 디스크의 `UniqueId` 속성에서 상속 된 값입니다. 디스크를 삭제 하 고 동일한 이름으로 새 디스크를 만드는 경우 `UniqueId` 속성의 값이 변경 됩니다.

`SourceResourceId` 및 `SourceUniqueId`를 사용 하 여 특정 디스크와 연결 된 모든 스냅숏의 목록을 만들 수 있습니다. 다음 예제에는 특정 디스크와 연결 된 모든 증분 스냅숏이 나열 되지만 일부 설정이 필요 합니다.

이 예제에서는 데이터를 쿼리 하는 데 jq를 사용 합니다. 예제를 실행 하려면 [jq를 설치](https://stedolan.github.io/jq/download/)해야 합니다.

`<yourResourceGroupNameHere>` 및 `<exampleDiskName>`을 사용자의 값으로 바꾸고 jq도 설치한 경우 다음 예제를 사용 하 여 기존 증분 스냅숏을 나열할 수 있습니다.

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

1. 아직 미리 보기에 등록 하지 않았고 증분 스냅숏 사용을 시작 하려는 경우 공개 미리 보기에 대 한 액세스 권한을 얻기 위해 AzureDisks@microsoft.com 전자 메일을 보내 주시기 바랍니다. 

2. 차등 기능을 사용 하 여 증분 스냅숏의 지역 간 복사본에 대 한 다음 샘플을 탐색 합니다.   

    - [Azure .Net Sdk 사용](https://github.com/Azure-Samples/managed-disks-dotnet-backup-with-incremental-snapshots)
