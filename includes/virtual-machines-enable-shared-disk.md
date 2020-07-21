---
title: 파일 포함
description: 포함 파일
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 07/14/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 29a90b94db5e6e5791361bad004efcf649e1950b
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/20/2020
ms.locfileid: "86500607"
---
## <a name="limitations"></a>제한 사항

[!INCLUDE [virtual-machines-disks-shared-limitations](virtual-machines-disks-shared-limitations.md)]

## <a name="supported-operating-systems"></a>지원되는 운영 체제

공유 디스크는 여러 운영 체제를 지원 합니다. 지원 되는 운영 체제에 대 한 개념 문서의 [Windows](../articles/virtual-machines/windows/disks-shared.md#windows) 및 [Linux](../articles/virtual-machines/linux/disks-shared.md#linux) 섹션을 참조 하세요.

## <a name="disk-sizes"></a>디스크 크기

[!INCLUDE [virtual-machines-disks-shared-sizes](virtual-machines-disks-shared-sizes.md)]

## <a name="deploy-shared-disks"></a>공유 디스크 배포

### <a name="deploy-a-premium-ssd-as-a-shared-disk"></a>프리미엄 SSD를 공유 디스크로 배포

공유 디스크 기능이 사용 하도록 설정 된 관리 디스크를 배포 하려면 새 속성을 사용 하 `maxShares` 고 1 보다 큰 값을 정의 합니다. 이렇게 하면 여러 Vm에서 디스크를 공유할 수 있습니다.

> [!IMPORTANT]
> `maxShares`모든 vm에서 디스크를 분리 하는 경우에만 값을 설정 하거나 변경할 수 있습니다. 에 대해 허용 되는 값은 [디스크 크기](#disk-sizes) 를 참조 하십시오 `maxShares` .

#### <a name="cli"></a>CLI
```azurecli

az disk create -g myResourceGroup -n mySharedDisk --size-gb 1024 -l westcentralus --sku PremiumSSD_LRS --max-shares 2

```

#### <a name="powershell"></a>PowerShell
```azurepowershell-interactive

$datadiskconfig = New-AzDiskConfig -Location 'WestCentralUS' -DiskSizeGB 1024 -AccountType PremiumSSD_LRS -CreateOption Empty

New-AzDisk -ResourceGroupName 'myResourceGroup' -DiskName 'mySharedDisk' -Disk $datadiskconfig

```

#### <a name="azure-resource-manager"></a>Azure Resource Manager
다음 템플릿을 사용 하기 전에,, `[parameters('dataDiskName')]` `[resourceGroup().location]` 및를 `[parameters('dataDiskSizeGB')]` `[parameters('maxShares')]` 사용자 고유의 값으로 바꿉니다.

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

### <a name="deploy-an-ultra-disk-as-a-shared-disk"></a>공유 디스크로 ultra disk 배포

공유 디스크 기능이 사용 하도록 설정 된 관리 디스크를 배포 하려면 `maxShares` 매개 변수를 1 보다 큰 값으로 변경 합니다. 이렇게 하면 여러 Vm에서 디스크를 공유할 수 있습니다.

> [!IMPORTANT]
> `maxShares`모든 vm에서 디스크를 분리 하는 경우에만 값을 설정 하거나 변경할 수 있습니다. 에 대해 허용 되는 값은 [디스크 크기](#disk-sizes) 를 참조 하십시오 `maxShares` .

#### <a name="cli"></a>CLI
```azurecli
#Creating an Ultra shared Disk 
az disk create -g rg1 -n clidisk --size-gb 1024 -l westus --sku UltraSSD_LRS --max-shares 5 --disk-iops-read-write 2000 --disk-mbps-read-write 200 --disk-iops-read-only 100 --disk-mbps-read-only 1

#Updating an Ultra shared Disk 
az disk update -g rg1 -n clidisk --disk-iops-read-write 3000 --disk-mbps-read-write 300 --set diskIopsReadOnly=100 --set diskMbpsReadOnly=1

#Show shared disk properties:
az disk show -g rg1 -n clidisk
```

#### <a name="powershell"></a>PowerShell
```azurepowershell-interactive

$datadiskconfig = New-AzDiskConfig -Location 'WestCentralUS' -DiskSizeGB 1024 -AccountType UltraSSD_LRS -CreateOption Empty -DiskIOPSReadWrite 2000 -DiskMBpsReadWrite 200 -DiskIOPSReadOnly 100 -DiskMBpsReadOnly 1

New-AzDisk -ResourceGroupName 'myResourceGroup' -DiskName 'mySharedDisk' -Disk $datadiskconfig

```

#### <a name="azure-resource-manager"></a>Azure Resource Manager

공유 디스크 기능이 사용 하도록 설정 된 관리 디스크를 배포 하려면 속성을 사용 하 `maxShares` 고 1 보다 큰 값을 정의 합니다. 이렇게 하면 여러 Vm에서 디스크를 공유할 수 있습니다.

> [!IMPORTANT]
> `maxShares`모든 vm에서 디스크를 분리 하는 경우에만 값을 설정 하거나 변경할 수 있습니다. 에 대해 허용 되는 값은 [디스크 크기](#disk-sizes) 를 참조 하십시오 `maxShares` .

다음 템플릿을 사용 하기 전에,,,,,, `[parameters('dataDiskName')]` `[resourceGroup().location]` 및를 `[parameters('dataDiskSizeGB')]` `[parameters('maxShares')]` `[parameters('diskIOPSReadWrite')]` `[parameters('diskMBpsReadWrite')]` `[parameters('diskIOPSReadOnly')]` `[parameters('diskMBpsReadOnly')]` 사용자 고유의 값으로 바꿉니다.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "diskName": {
      "type": "string",
      "defaultValue": "uShared30"
    },
    "location": {
        "type": "string",
        "defaultValue": "westus",
        "metadata": {
                "description": "Location for all resources."
        }
    },
    "dataDiskSizeGB": {
      "type": "int",
      "defaultValue": 1024
    },
    "maxShares": {
      "type": "int",
      "defaultValue": 2
    },
    "diskIOPSReadWrite": {
      "type": "int",
      "defaultValue": 2048
    },
    "diskMBpsReadWrite": {
      "type": "int",
      "defaultValue": 20
    },    
    "diskIOPSReadOnly": {
      "type": "int",
      "defaultValue": 100
    },
    "diskMBpsReadOnly": {
      "type": "int",
      "defaultValue": 1
    }    
  }, 
  "resources": [
    {
        "type": "Microsoft.Compute/disks",
        "name": "[parameters('diskName')]",
        "location": "[parameters('location')]",
        "apiVersion": "2019-07-01",
        "sku": {
            "name": "UltraSSD_LRS"
        },
        "properties": {
            "creationData": {
                "createOption": "Empty"
            },
            "diskSizeGB": "[parameters('dataDiskSizeGB')]",
            "maxShares": "[parameters('maxShares')]",
            "diskIOPSReadWrite": "[parameters('diskIOPSReadWrite')]",
            "diskMBpsReadWrite": "[parameters('diskMBpsReadWrite')]",
            "diskIOPSReadOnly": "[parameters('diskIOPSReadOnly')]",
            "diskMBpsReadOnly": "[parameters('diskMBpsReadOnly')]"
        }
    }
  ]
}
```

### <a name="using-azure-shared-disks-with-your-vms"></a>Vm에서 Azure 공유 디스크 사용

를 사용 하 여 공유 디스크를 배포한 후에 `maxShares>1` 는 하나 이상의 vm에 디스크를 탑재할 수 있습니다.

```azurepowershell-interactive

$resourceGroup = "myResourceGroup"
$location = "WestCentralUS"

$vm = New-AzVm -ResourceGroupName $resourceGroup -Name "myVM" -Location $location -VirtualNetworkName "myVnet" -SubnetName "mySubnet" -SecurityGroupName "myNetworkSecurityGroup" -PublicIpAddressName "myPublicIpAddress"

$dataDisk = Get-AzDisk -ResourceGroupName $resourceGroup -DiskName "mySharedDisk"

$vm = Add-AzVMDataDisk -VM $vm -Name "mySharedDisk" -CreateOption Attach -ManagedDiskId $dataDisk.Id -Lun 0

update-AzVm -VM $vm -ResourceGroupName $resourceGroup
```

## <a name="supported-scsi-pr-commands"></a>지원 되는 SCSI PR 명령

공유 디스크를 클러스터의 Vm에 탑재 한 후에는 SCSI PR을 사용 하 여 쿼럼 및 디스크에 대 한 읽기/쓰기를 설정할 수 있습니다. Azure 공유 디스크를 사용 하는 경우 다음과 같은 PR 명령을 사용할 수 있습니다.

디스크와 상호 작용 하려면 영구 예약 작업 목록으로 시작 합니다.

```
PR_REGISTER_KEY 

PR_REGISTER_AND_IGNORE 

PR_GET_CONFIGURATION 

PR_RESERVE 

PR_PREEMPT_RESERVATION 

PR_CLEAR_RESERVATION 

PR_RELEASE_RESERVATION 
```

PR_RESERVE, PR_PREEMPT_RESERVATION 또는 PR_RELEASE_RESERVATION를 사용 하는 경우 다음의 영구 예약 형식 중 하나를 제공 합니다.

```
PR_NONE 

PR_WRITE_EXCLUSIVE 

PR_EXCLUSIVE_ACCESS 

PR_WRITE_EXCLUSIVE_REGISTRANTS_ONLY 

PR_EXCLUSIVE_ACCESS_REGISTRANTS_ONLY 

PR_WRITE_EXCLUSIVE_ALL_REGISTRANTS 

PR_EXCLUSIVE_ACCESS_ALL_REGISTRANTS 
```

PR_RESERVE, PR_REGISTER_AND_IGNORE, PR_REGISTER_KEY, PR_PREEMPT_RESERVATION, PR_CLEAR_RESERVATION 또는 PR_RELEASE 예약을 사용 하는 경우에도 영구 예약 키를 제공 해야 합니다.


## <a name="next-steps"></a>다음 단계
