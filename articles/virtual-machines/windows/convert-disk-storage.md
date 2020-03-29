---
title: 관리 디스크 스토리지를 표준 SSD와 프리미엄 SSD 간에 변환
description: Azure PowerShell을 사용하여 Azure 관리 디스크를 표준에서 프리미엄 또는 프리미엄으로 변환하는 방법
author: roygara
ms.service: virtual-machines-windows
ms.topic: conceptual
ms.date: 02/22/2019
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: e339f0c7ca0807eec3e160eeb3464044c2ef29ba
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76720948"
---
# <a name="update-the-storage-type-of-a-managed-disk"></a>관리 디스크의 스토리지 형식 업데이트

Azure 관리 형 디스크에는 Azure 울트라 SSD (미리 보기), 프리미엄 SSD, 표준 SSD 및 표준 HDD의 네 가지 디스크 유형이 있습니다. 성능 요구에 따라 세 가지 GA 디스크 유형(프리미엄 SSD, 표준 SSD 및 표준 HDD) 간에 전환할 수 있습니다. 아직 울트라 SSD에서 전환하거나 울트라 SSD로 전환할 수 없는 경우 새 SSD를 배포해야 합니다.

관리되지 않는 디스크에는 이 기능이 지원되지 않습니다. 그러나 [관리되지 않는 디스크를 관리 디스크로](convert-unmanaged-to-managed-disks.md) 쉽게 변환하여 디스크 유형 간에 전환할 수 있습니다.

 

## <a name="prerequisites"></a>사전 요구 사항

* 변환에는 VM(가상 시스템)을 다시 시작해야 하므로 기존 유지 관리 기간 동안 디스크 저장소마이그레이션을 예약해야 합니다.
* 디스크가 관리되지 않는 경우 먼저 [디스크를 관리 디스크로 변환하여](convert-unmanaged-to-managed-disks.md) 저장소 옵션 간에 전환할 수 있습니다.

## <a name="switch-all-managed-disks-of-a-vm-between-premium-and-standard"></a>프리미엄과 표준 간에 VM의 모든 관리 디스크 전환

이 예제에서는 VM의 모든 디스크를 표준 저장소에서 프리미엄 저장소또는 프리미엄 저장소에서 표준 저장소로 변환하는 방법을 보여 주며 있습니다. 프리미엄 관리 디스크를 사용하려면 VM에서 프리미엄 저장소를 지원하는 [VM 크기를](sizes.md) 사용해야 합니다. 또한 이 예제에서는 Premium Storage를 지원하는 크기로 전환합니다.

```azurepowershell-interactive
# Name of the resource group that contains the VM
$rgName = 'yourResourceGroup'

# Name of the your virtual machine
$vmName = 'yourVM'

# Choose between Standard_LRS and Premium_LRS based on your scenario
$storageType = 'Premium_LRS'

# Premium capable size
# Required only if converting storage from Standard to Premium
$size = 'Standard_DS2_v2'

# Stop and deallocate the VM before changing the size
Stop-AzVM -ResourceGroupName $rgName -Name $vmName -Force

$vm = Get-AzVM -Name $vmName -resourceGroupName $rgName

# Change the VM size to a size that supports Premium storage
# Skip this step if converting storage from Premium to Standard
$vm.HardwareProfile.VmSize = $size
Update-AzVM -VM $vm -ResourceGroupName $rgName

# Get all disks in the resource group of the VM
$vmDisks = Get-AzDisk -ResourceGroupName $rgName 

# For disks that belong to the selected VM, convert to Premium storage
foreach ($disk in $vmDisks)
{
    if ($disk.ManagedBy -eq $vm.Id)
    {
        $disk.Sku = [Microsoft.Azure.Management.Compute.Models.DiskSku]::new($storageType)
        $disk | Update-AzDisk
    }
}

Start-AzVM -ResourceGroupName $rgName -Name $vmName
```

## <a name="switch-individual-managed-disks-between-standard-and-premium"></a>표준 디스크와 프리미엄 간에 개별 관리 디스크 전환

개발/테스트 워크로드의 경우 표준 디스크와 프리미엄 디스크를 혼합하여 비용을 절감할 수 있습니다. 더 나은 성능이 필요한 디스크만 업그레이드하도록 선택할 수 있습니다. 이 예제에서는 단일 VM 디스크를 표준 저장소에서 프리미엄 저장소또는 프리미엄 저장소에서 표준 저장소로 변환하는 방법을 보여 주십습니다. 프리미엄 관리 디스크를 사용하려면 VM에서 프리미엄 저장소를 지원하는 [VM 크기를](sizes.md) 사용해야 합니다. 이 예제에서는 Premium 저장소를 지원하는 크기로 전환하는 방법도 보여 주며 다음과 같은 방법을 보여 주며 다음과 같은 방법을 보여 주며 다음과 같은 방법을 보여 주며 다음과 같은 방법을 보여 주며 다음과 같은 방법을 보여 주며 다음과 같은

```azurepowershell-interactive

$diskName = 'yourDiskName'
# resource group that contains the managed disk
$rgName = 'yourResourceGroupName'
# Choose between Standard_LRS and Premium_LRS based on your scenario
$storageType = 'Premium_LRS'
# Premium capable size 
$size = 'Standard_DS2_v2'

$disk = Get-AzDisk -DiskName $diskName -ResourceGroupName $rgName

# Get parent VM resource
$vmResource = Get-AzResource -ResourceId $disk.ManagedBy

# Stop and deallocate the VM before changing the storage type
Stop-AzVM -ResourceGroupName $vmResource.ResourceGroupName -Name $vmResource.Name -Force

$vm = Get-AzVM -ResourceGroupName $vmResource.ResourceGroupName -Name $vmResource.Name 

# Change the VM size to a size that supports Premium storage
# Skip this step if converting storage from Premium to Standard
$vm.HardwareProfile.VmSize = $size
Update-AzVM -VM $vm -ResourceGroupName $rgName

# Update the storage type
$disk.Sku = [Microsoft.Azure.Management.Compute.Models.DiskSku]::new($storageType)
$disk | Update-AzDisk

Start-AzVM -ResourceGroupName $vm.ResourceGroupName -Name $vm.Name
```

## <a name="convert-managed-disks-from-standard-to-premium-in-the-azure-portal"></a>Azure 포털에서 관리디스크를 표준에서 프리미엄으로 변환

다음 단계를 수행하세요.

1. [Azure 포털에](https://portal.azure.com)로그인합니다.
2. 포털의 **가상 컴퓨터** 목록에서 VM을 선택합니다.
3. VM이 중지되지 않으면 VM **개요** 창 상단에서 **중지를** 선택하고 VM이 멈출 때까지 기다립니다.
3. VM창에서 메뉴에서 **디스크를 선택합니다.**
4. 변환할 디스크를 선택합니다.
5. 메뉴에서 **구성을** 선택합니다.
6. 계정 **유형을** **표준 HDD에서** **프리미엄 SSD로**변경합니다.
7. **저장을**클릭하고 디스크 창을 닫습니다.

디스크 유형 변환은 즉각적입니다. 변환 후 VM을 시작할 수 있습니다.

## <a name="switch-managed-disks-between-standard-hdd-and-standard-ssd"></a>표준 HDD와 표준 SSD 간에 관리 디스크 전환 

이 예제에서는 단일 VM 디스크를 표준 HDD에서 표준 SSD또는 표준 SSD에서 표준 HDD로 변환하는 방법을 보여 주며 다음과 같은 방법을 보여 주며 있습니다.

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
$disk.Sku = [Microsoft.Azure.Management.Compute.Models.DiskSku]::new($storageType)
$disk | Update-AzDisk

Start-AzVM -ResourceGroupName $vm.ResourceGroupName -Name $vm.Name
```

## <a name="next-steps"></a>다음 단계

[스냅샷](snapshot-copy-managed-disk.md)을 사용하여 VM의 읽기 전용 복사본을 만듭니다.
