---
title: Azure 관리 디스크 저장소를 표준에서 프리미엄으로, 또 그 반대로 변환 | Microsoft Docs
description: Azure PowerShell을 사용하여 Azure 관리 디스크를 표준에서 프리미엄으로, 또 그 반대로 변환하는 방법
services: virtual-machines-windows
documentationcenter: ''
author: ramankumarlive
manager: kavithag
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 10/04/2018
ms.author: ramankum
ms.subservice: disks
ms.openlocfilehash: 94482666d0db3157b0c18c0b47f9937457172521
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/12/2019
ms.locfileid: "56116000"
---
# <a name="update-the-storage-type-of-a-managed-disk"></a>관리 디스크의 저장소 형식 업데이트

Azure Managed Disk는 세 가지 스토리지 옵션 [프리미엄 SSD](../windows/premium-storage.md), [표준 SSD](../windows/disks-standard-ssd.md) 및 [표준 HDD](../windows/standard-storage.md)를 제공합니다. 관리 디스크를 성능 요구 사항에 따라 최소한의 가동 중지 시간으로 저장소 형식 간 전환할 수 있습니다. 저장소 형식 간 전환은 관리되지 않는 디스크에는 지원되지 않습니다. 단, [비관리 디스크를 관리 디스크로 변환](convert-unmanaged-to-managed-disks.md)은 손쉽게 수행할 수 있습니다.

[!INCLUDE [updated-for-az-vm.md](../../../includes/updated-for-az-vm.md)]

## <a name="prerequisites"></a>필수 조건

* 변환하려면 VM(가상 머신)을 재시작해야 하므로 기존 유지 관리 기간 동안 디스크 저장소의 마이그레이션을 예약해야 합니다. 
* 비관리 디스크를 사용하는 경우 저장소 형식 간에 전환할 수 있도록 먼저 [관리 디스크로 변환](convert-unmanaged-to-managed-disks.md)합니다. 
* 이 문서의 예제에는 Azure PowerShell 모듈 6.0.0 이상이 필요합니다. `Get-Module -ListAvailable AzureRM`을 실행하여 버전을 찾습니다. 업그레이드해야 하는 경우 [Azure PowerShell 모듈 설치](/powershell/azure/azurerm/install-azurerm-ps)를 참조하세요. [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount)를 실행하여 Azure와 연결합니다.


## <a name="convert-all-the-managed-disks-of-a-vm-from-standard-to-premium"></a>VM의 모든 관리 디스크를 표준에서 프리미엄으로 변환

다음 예제에서는 VM의 모든 디스크를 표준에서 Premium Storage로 전환하는 방법을 보여줍니다. 프리미엄 관리 디스크를 사용하려면 VM에서 Premium Storage를 지원하는 [VM 크기](sizes.md)를 사용해야 합니다. 또한 이 예제에서는 Premium Storage를 지원하는 크기로 전환합니다.

```azurepowershell-interactive
# Name of the resource group that contains the VM
$rgName = 'yourResourceGroup'

# Name of the your virtual machine
$vmName = 'yourVM'

# Choose between StandardLRS and PremiumLRS based on your scenario
$storageType = 'Premium_LRS'

# Premium capable size
# Required only if converting storage from standard to premium
$size = 'Standard_DS2_v2'

# Stop and deallocate the VM before changing the size
Stop-AzVM -ResourceGroupName $rgName -Name $vmName -Force

$vm = Get-AzVM -Name $vmName -resourceGroupName $rgName

# Change the VM size to a size that supports premium storage
# Skip this step if converting storage from premium to standard
$vm.HardwareProfile.VmSize = $size
Update-AzVM -VM $vm -ResourceGroupName $rgName

# Get all disks in the resource group of the VM
$vmDisks = Get-AzDisk -ResourceGroupName $rgName 

# For disks that belong to the selected VM, convert to premium storage
foreach ($disk in $vmDisks)
{
    if ($disk.ManagedBy -eq $vm.Id)
    {
        $diskUpdateConfig = New-AzDiskUpdateConfig –AccountType $storageType
        Update-AzDisk -DiskUpdate $diskUpdateConfig -ResourceGroupName $rgName `
        -DiskName $disk.Name
    }
}

Start-AzVM -ResourceGroupName $rgName -Name $vmName
```

## <a name="convert-a-managed-disk-from-standard-to-premium"></a>관리 디스크를 표준에서 프리미엄으로 변환

개발/테스트 워크로드의 경우 비용을 줄이기 위해 표준 및 프리미엄 디스크를 혼합할 수도 있습니다. 이를 수행하려면 더 나은 성능을 요구하는 디스크만 Premium Storage로 업그레이드합니다. 다음 예제에서는 VM의 단일 디스크를 표준에서 Premium Storage로, 또 그 반대로 전환하는 방법을 보여줍니다. 프리미엄 관리 디스크를 사용하려면 VM에서 Premium Storage를 지원하는 [VM 크기](sizes.md)를 사용해야 합니다. 또한 이 예제에서는 Premium Storage를 지원하는 크기로 전환하는 방법을 보여줍니다.

```azurepowershell-interactive

$diskName = 'yourDiskName'
# resource group that contains the managed disk
$rgName = 'yourResourceGroupName'
# Choose between StandardLRS and PremiumLRS based on your scenario
$storageType = 'Premium_LRS'
# Premium capable size 
$size = 'Standard_DS2_v2'

$disk = Get-AzDisk -DiskName $diskName -ResourceGroupName $rgName

# Get parent VM resource
$vmResource = Get-AzResource -ResourceId $disk.ManagedBy

# Stop and deallocate the VM before changing the storage type
Stop-AzVM -ResourceGroupName $vmResource.ResourceGroupName -Name $vmResource.Name -Force

$vm = Get-AzVM -ResourceGroupName $vmResource.ResourceGroupName -Name $vmResource.Name 

# Change the VM size to a size that supports premium storage
# Skip this step if converting storage from premium to standard
$vm.HardwareProfile.VmSize = $size
Update-AzVM -VM $vm -ResourceGroupName $rgName

# Update the storage type
$diskUpdateConfig = New-AzDiskUpdateConfig -AccountType $storageType -DiskSizeGB $disk.DiskSizeGB
Update-AzDisk -DiskUpdate $diskUpdateConfig -ResourceGroupName $rgName `
-DiskName $disk.Name

Start-AzVM -ResourceGroupName $vm.ResourceGroupName -Name $vm.Name
```

## <a name="convert-a-managed-disk-from-standard-hdd-to-standard-ssd"></a>관리 디스크를 표준 HDD에서 표준 SSD로 변환

다음 예제에서는 VM의 단일 디스크를 표준 HDD에서 표준 SSD로, 또 그 반대로 전환하는 방법을 보여줍니다.

```azurepowershell-interactive

$diskName = 'yourDiskName'
# resource group that contains the managed disk
$rgName = 'yourResourceGroupName'
# Choose between Standard_LRS and StandardSSD_LRS based on your scenario
$storageType = 'StandardSSD_LRS'

$disk = Get-AzDisk -DiskName $diskName -ResourceGroupName $rgName

# Get parent VM resource
$vmResource = Get-AzResource -ResourceId $disk.ManagedBy

# Stop and deallocate the VM before changing the storage type
Stop-AzVM -ResourceGroupName $vmResource.ResourceGroupName -Name $vmResource.Name -Force

$vm = Get-AzVM -ResourceGroupName $vmResource.ResourceGroupName -Name $vmResource.Name 

# Update the storage type
$diskUpdateConfig = New-AzDiskUpdateConfig -AccountType $storageType -DiskSizeGB $disk.DiskSizeGB
Update-AzDisk -DiskUpdate $diskUpdateConfig -ResourceGroupName $rgName `
-DiskName $disk.Name

Start-AzVM -ResourceGroupName $vm.ResourceGroupName -Name $vm.Name
```

## <a name="next-steps"></a>다음 단계

[스냅숏](snapshot-copy-managed-disk.md)을 사용하여 VM의 읽기 전용 복사본을 만듭니다.

