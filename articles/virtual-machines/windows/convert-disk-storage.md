---
title: Azure 관리 디스크 저장소를 표준에서 프리미엄으로, 또 그 반대로 변환 | Microsoft Docs
description: Azure PowerShell을 사용하여 Azure 관리 디스크를 표준에서 프리미엄으로, 또 그 반대로 변환하는 방법
services: virtual-machines-windows
documentationcenter: ''
author: ramankum
manager: kavithag
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 08/07/2017
ms.author: ramankum
ms.openlocfilehash: 7e4a37e318bd45c5cc2dca4e085b2a112db65efe
ms.sourcegitcommit: e45b2aa85063d33853560ec4bc867f230c1c18ce
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/31/2018
ms.locfileid: "43371358"
---
# <a name="convert-azure-managed-disks-storage-from-standard-to-premium-and-vice-versa"></a>Azure 관리 디스크 저장소를 표준에서 프리미엄으로, 또 그 반대로 변환

Managed Disks는 세 가지 저장소 옵션인 [프리미엄 SSD](../windows/premium-storage.md), 표준 SSD(미리 보기) 및 [표준 HDD](../windows/standard-storage.md)를 제공합니다. 성능 요구 사항에 따라 최소한의 가동 중지 시간으로 옵션 사이를 쉽게 전환할 수 있습니다. 이 항목은 현재 관리되지 않는 디스크에 지원되지 않습니다. 하지만 디스크 유형 사이를 쉽게 전환하도록 [관리 디스크로 변환](convert-unmanaged-to-managed-disks.md)할 수 있습니다.

이 문서는 Azure PowerShell을 사용하여 관리 디스크를 표준에서 프리미엄으로, 또 그 반대로 변환하는 방법을 설명합니다. 설치 또는 업그레이드가 필요한 경우 [Azure PowerShell 설치 및 구성](https://docs.microsoft.com/powershell/azure/install-azurerm-ps?view=azurermps-6.8.1)을 참조하세요.

## <a name="before-you-begin"></a>시작하기 전에

* 변환은 기존 유지 관리 기간 동안 디스크 저장소의 마이그레이션을 예약하도록 VM의 재시작이 필요합니다. 
* 관리되지 않는 디스크를 사용하는 경우, 이 문서를 사용하여 저장소 옵션 사이로 전환하려면 먼저 [관리 디스크로 변환](convert-unmanaged-to-managed-disks.md)해야 합니다. 
* 이 아티클에서는 Azure PowerShell 모듈 버전 6.0.0 이상이 필요합니다. ` Get-Module -ListAvailable AzureRM`을 실행하여 버전을 찾습니다. 업그레이드해야 하는 경우 [Azure PowerShell 모듈 설치](/powershell/azure/install-azurerm-ps)를 참조하세요. 또한 `Connect-AzureRmAccount`을 실행하여 Azure와 연결해야 합니다.


## <a name="convert-all-the-managed-disks-of-a-vm-from-standard-to-premium-and-vice-versa"></a>VM의 모든 관리 디스크를 표준에서 프리미엄으로, 또 그 반대로 변환

다음 예제에서는 VM의 모든 디스크를 표준에서 프리미엄 저장소로 전환하는 방법을 보여줍니다. 프리미엄 관리 디스크를 사용하려면 VM에서 프리미엄 저장소를 지원하는 [VM 크기](sizes.md)를 사용해야 합니다. 또한 이 예제에서는 프리미엄 저장소를 지원하는 크기로 전환합니다.

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
Stop-AzureRmVM -ResourceGroupName $rgName -Name $vmName -Force

$vm = Get-AzureRmVM -Name $vmName -resourceGroupName $rgName

# Change the VM size to a size that supports premium storage
# Skip this step if converting storage from premium to standard
$vm.HardwareProfile.VmSize = $size
Update-AzureRmVM -VM $vm -ResourceGroupName $rgName

# Get all disks in the resource group of the VM
$vmDisks = Get-AzureRmDisk -ResourceGroupName $rgName 

# For disks that belong to the selected VM, convert to premium storage
foreach ($disk in $vmDisks)
{
    if ($disk.ManagedBy -eq $vm.Id)
    {
        $diskUpdateConfig = New-AzureRmDiskUpdateConfig –AccountType $storageType
        Update-AzureRmDisk -DiskUpdate $diskUpdateConfig -ResourceGroupName $rgName `
        -DiskName $disk.Name
    }
}

Start-AzureRmVM -ResourceGroupName $rgName -Name $vmName
```
## <a name="convert-a-managed-disk-from-standard-to-premium-and-vice-versa"></a>관리 디스크를 표준에서 프리미엄으로, 또 그 반대로 변환

개발/테스트 워크로드의 경우 비용을 줄이기 위해 표준 및 프리미엄 디스크를 혼합할 수도 있습니다. 더 나은 성능을 요구하는 디스크만 프리미엄 저장소로 업그레이드하여 이를 수행할 수 있습니다. 다음 예제에서는 VM의 단일 디스크를 표준에서 프리미엄 저장소로, 또 그 반대로 전환하는 방법을 보여줍니다. 프리미엄 관리 디스크를 사용하려면 VM에서 프리미엄 저장소를 지원하는 [VM 크기](sizes.md)를 사용해야 합니다. 또한 이 예제에서는 프리미엄 저장소를 지원하는 크기로 전환합니다.

```azurepowershell-interactive

$diskName = 'yourDiskName'
# resource group that contains the managed disk
$rgName = 'yourResourceGroupName'
# Choose between StandardLRS and PremiumLRS based on your scenario
$storageType = 'Premium_LRS'
# Premium capable size 
$size = 'Standard_DS2_v2'

$disk = Get-AzureRmDisk -DiskName $diskName -ResourceGroupName $rgName

# Get parent VM resource
$vmResource = Get-AzureRmResource -ResourceId $disk.ManagedBy

# Stop and deallocate the VM before changing the storage type
Stop-AzureRmVM -ResourceGroupName $vm.ResourceGroupName -Name $vmResource.Name -Force

$vm = Get-AzureRmVM -ResourceGroupName $vmResource.ResourceGroupName -Name $vmResource.Name 

# Change the VM size to a size that supports premium storage
# Skip this step if converting storage from premium to standard
$vm.HardwareProfile.VmSize = $size
Update-AzureRmVM -VM $vm -ResourceGroupName $rgName

# Update the storage type
$diskUpdateConfig = New-AzureRmDiskUpdateConfig -AccountType $storageType -DiskSizeGB $disk.DiskSizeGB
Update-AzureRmDisk -DiskUpdate $diskUpdateConfig -ResourceGroupName $rgName `
-DiskName $disk.Name

Start-AzureRmVM -ResourceGroupName $vm.ResourceGroupName -Name $vm.Name
```

## <a name="convert-a-managed-disk-from-standard-hdd-to-standard-ssd-and-vice-versa"></a>관리 디스크를 표준 HDD에서 표준 SSD로, 또 그 반대로 변환

다음 예제에서는 VM의 단일 디스크를 표준 HDD에서 표준 SSD로, 또 그 반대로 전환하는 방법을 보여 줍니다.

```azurepowershell-interactive

$diskName = 'yourDiskName'
# resource group that contains the managed disk
$rgName = 'yourResourceGroupName'
# Choose between Standard_LRS and StandardSSD_LRS based on your scenario
$storageType = 'StandardSSD_LRS'

$disk = Get-AzureRmDisk -DiskName $diskName -ResourceGroupName $rgName

# Get parent VM resource
$vmResource = Get-AzureRmResource -ResourceId $disk.ManagedBy

# Stop and deallocate the VM before changing the storage type
Stop-AzureRmVM -ResourceGroupName $vmResource.ResourceGroupName -Name $vmResource.Name -Force

$vm = Get-AzureRmVM -ResourceGroupName $vmResource.ResourceGroupName -Name $vmResource.Name 

# Update the storage type
$diskUpdateConfig = New-AzureRmDiskUpdateConfig -AccountType $storageType -DiskSizeGB $disk.DiskSizeGB
Update-AzureRmDisk -DiskUpdate $diskUpdateConfig -ResourceGroupName $rgName `
-DiskName $disk.Name

Start-AzureRmVM -ResourceGroupName $vm.ResourceGroupName -Name $vm.Name
```

## <a name="next-steps"></a>다음 단계

[스냅숏](snapshot-copy-managed-disk.md)을 사용하여 VM의 읽기 전용 복사본을 만듭니다.

