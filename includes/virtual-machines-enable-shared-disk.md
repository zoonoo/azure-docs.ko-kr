---
title: 포함 파일
description: 포함 파일
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 02/18/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 26e76731f663ac9038bc87182d52c4bd245f1b6e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77471702"
---
## <a name="limitations"></a>제한 사항

[!INCLUDE [virtual-machines-disks-shared-limitations](virtual-machines-disks-shared-limitations.md)]

## <a name="disk-sizes"></a>디스크 크기

[!INCLUDE [virtual-machines-disks-shared-sizes](virtual-machines-disks-shared-sizes.md)]

## <a name="deploy-an-azure-shared-disk"></a>Azure 공유 디스크 배포

공유 디스크 기능이 활성화된 관리 디스크를 배포하려면 `maxShares` 새 속성을 `>1`사용하고 값을 정의합니다. 이렇게 하면 여러 VM에서 디스크를 공유할 수 있습니다.

> [!IMPORTANT]
> 디스크가 `maxShares` 모든 VM에서 마운트 해제된 경우에만 값을 설정하거나 변경할 수 있습니다. 에 대해 허용되는 값에 대한 디스크 크기를 참조하십시오. [Disk sizes](#disk-sizes) `maxShares`

다음 템플릿을 사용하기 `[parameters('dataDiskName')]`전에 `[resourceGroup().location]` `[parameters('dataDiskSizeGB')]`에서 `[parameters('maxShares')]` 를 바꾸고 사용자 고유의 값으로 바꿉습니다.

```json
{ 
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "dataDiskName": {
      "type": "string",
      "defaultValue": "mySharedDisk"
    },
    "dataDiskSizeGB": {
      "type": "int",
      "defaultValue": 1024
    },
    "maxShares": {
      "type": "int",
      "defaultValue": 2
    }
  },
  "resources": [
    {
      "type": "Microsoft.Compute/disks",
      "name": "[parameters('dataDiskName')]",
      "location": "[resourceGroup().location]",
      "apiVersion": "2019-07-01",
      "sku": {
        "name": "Premium_LRS"
      },
      "properties": {
        "creationData": {
          "createOption": "Empty"
        },
        "diskSizeGB": "[parameters('dataDiskSizeGB')]",
        "maxShares": "[parameters('maxShares')]"
      }
    }
  ] 
}
```

### <a name="using-azure-shared-disks-with-your-vms"></a>VM에서 Azure 공유 디스크 사용

`maxShares>1`을 사용하여 공유 디스크를 배포한 후에는 하나 이상의 VM에 디스크를 탑재할 수 있습니다.

> [!IMPORTANT]
> 디스크를 공유하는 모든 VM은 동일한 [근접 배치 그룹에](../articles/virtual-machines/windows/proximity-placement-groups.md)배포해야 합니다.

```azurepowershell-interactive

$resourceGroup = "myResourceGroup"
$location = "WestCentralUS"
$ppgName = "myPPG"
$ppg = New-AzProximityPlacementGroup `
   -Location $location `
   -Name $ppgName `
   -ResourceGroupName $resourceGroup `
   -ProximityPlacementGroupType Standard

$vm = New-AzVm -ResourceGroupName $resourceGroup -Name "myVM" -Location $location -VirtualNetworkName "myVnet" -SubnetName "mySubnet" -SecurityGroupName "myNetworkSecurityGroup" -PublicIpAddressName "myPublicIpAddress" -ProximityPlacementGroup $ppg.Id

$dataDisk = Get-AzDisk -ResourceGroupName $resourceGroup -DiskName "mySharedDisk"

$vm = Add-AzVMDataDisk -VM $vm -Name "mySharedDisk" -CreateOption Attach -ManagedDiskId $dataDisk.Id -Lun 0

update-AzVm -VM $vm -ResourceGroupName $resourceGroup
```

## <a name="supported-scsi-pr-commands"></a>지원되는 SCSI PR 명령

클러스터의 VM에 공유 디스크를 탑재한 후에는 SCSI PR을 사용하여 쿼럼을 설정하고 디스크에 읽기/쓰기를 할 수 있습니다. Azure 공유 디스크를 사용할 때 다음 PR 명령을 사용할 수 있습니다.

디스크와 상호 작용하려면 영구 예약 작업 목록으로 시작합니다.

```
PR_REGISTER_KEY 

PR_REGISTER_AND_IGNORE 

PR_GET_CONFIGURATION 

PR_RESERVE 

PR_PREEMPT_RESERVATION 

PR_CLEAR_RESERVATION 

PR_RELEASE_RESERVATION 
```

PR_RESERVE, PR_PREEMPT_RESERVATION 또는 PR_RELEASE_RESERVATION 사용하는 경우 다음 중 하나를 영구 예약 유형으로 제공합니다.

```
PR_NONE 

PR_WRITE_EXCLUSIVE 

PR_EXCLUSIVE_ACCESS 

PR_WRITE_EXCLUSIVE_REGISTRANTS_ONLY 

PR_EXCLUSIVE_ACCESS_REGISTRANTS_ONLY 

PR_WRITE_EXCLUSIVE_ALL_REGISTRANTS 

PR_EXCLUSIVE_ACCESS_ALL_REGISTRANTS 
```

또한 PR_RESERVE, PR_REGISTER_AND_IGNORE, PR_REGISTER_KEY, PR_PREEMPT_RESERVATION, PR_CLEAR_RESERVATION 또는 PR_RELEASE 예약을 사용할 때 영구 예약 키를 제공해야 합니다.


## <a name="next-steps"></a>다음 단계

공유 디스크를 시도하고 싶다면 미리 [보기에 등록하십시오.](https://aka.ms/AzureSharedDiskPreviewSignUp)