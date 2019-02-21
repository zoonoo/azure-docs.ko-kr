---
title: VM 가용성 집합 변경 | Microsoft Docs
description: Azure PowerShell 및 Resource Manager 배포 모델을 사용하여 가상 머신에 대한 가용성 집합을 변경하는 방법에 대해 알아봅니다.
keywords: ''
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 02/12/2019
ms.author: cynthn
ms.openlocfilehash: 1935286d94b0d72a59fc5d478705e23a7f7425e9
ms.sourcegitcommit: de81b3fe220562a25c1aa74ff3aa9bdc214ddd65
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/13/2019
ms.locfileid: "56236609"
---
# <a name="change-the-availability-set-for-a-windows-vm"></a>Windows VM에 대한 가용성 집합 변경
다음 단계에서는 Azure PowerShell을 사용하여 VM의 가용성 집합을 변경하는 방법을 설명합니다. VM은 생성될 때만 가용성 집합에 추가될 수 있습니다. 가용성 집합을 변경하려면 가상 머신을 삭제한 다음, 다시 만들어야 합니다. 

이 문서는 2019/2/12에 [Azure Cloud Shell](https://shell.azure.com/powershell) 및 [Az PowerShell 모듈](https://docs.microsoft.com/powershell/azure/install-az-ps) 버전 1.2.0을 사용하여 마지막으로 테스트되었습니다.

[!INCLUDE [updated-for-az-vm.md](../../../includes/updated-for-az-vm.md)]

## <a name="change-the-availability-set"></a>가용성 집합 변경 

다음 스크립트는 필요한 정보를 수집하고, 원본 VM을 삭제한 다음 새 가용성 집합에서 다시 만드는 예제를 제공합니다.

```powershell
# Set variables
    $resourceGroup = "myResourceGroup"
    $vmName = "myVM"
    $newAvailSetName = "myAvailabilitySet"

# Get the details of the VM to be moved to the Availability Set
    $originalVM = Get-AzVM `
       -ResourceGroupName $resourceGroup `
       -Name $vmName

# Create new availability set if it does not exist
    $availSet = Get-AzAvailabilitySet `
       -ResourceGroupName $resourceGroup `
       -Name $newAvailSetName `
       -ErrorAction Ignore
    if (-Not $availSet) {
    $availSet = New-AzAvailabilitySet `
       -Location $originalVM.Location `
       -Name $newAvailSetName `
       -ResourceGroupName $resourceGroup `
       -PlatformFaultDomainCount 2 `
       -PlatformUpdateDomainCount 2 `
       -Sku Aligned
    }
    
# Remove the original VM
    Remove-AzVM -ResourceGroupName $resourceGroup -Name $vmName    

# Create the basic configuration for the replacement VM
    $newVM = New-AzVMConfig `
       -VMName $originalVM.Name `
       -VMSize $originalVM.HardwareProfile.VmSize `
       -AvailabilitySetId $availSet.Id
  
    Set-AzVMOSDisk `
       -VM $newVM -CreateOption Attach `
       -ManagedDiskId $originalVM.StorageProfile.OsDisk.ManagedDisk.Id `
       -Name $originalVM.StorageProfile.OsDisk.Name `
       -Windows

# Add Data Disks
    foreach ($disk in $originalVM.StorageProfile.DataDisks) { 
    Add-AzVMDataDisk -VM $newVM `
       -Name $disk.Name `
       -ManagedDiskId $disk.ManagedDisk.Id `
       -Caching $disk.Caching `
       -Lun $disk.Lun `
       -DiskSizeInGB $disk.DiskSizeGB `
       -CreateOption Attach
    }
    
# Add NIC(s) and keep the same NIC as primary
    foreach ($nic in $originalVM.NetworkProfile.NetworkInterfaces) {    
    if ($nic.Primary -eq "True")
        {
            Add-AzVMNetworkInterface `
            -VM $newVM `
            -Id $nic.Id -Primary
            }
        else
            {
              Add-AzVMNetworkInterface `
              -VM $newVM `
              -Id $nic.Id 
                }
    }

# Recreate the VM
    New-AzVM `
       -ResourceGroupName $resourceGroup `
       -Location $originalVM.Location `
       -VM $newVM `
       -DisableBginfoExtension
```

## <a name="next-steps"></a>다음 단계

[데이터 디스크](attach-managed-disk-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)를 더 추가하여 VM에 저장소를 좀 더 추가합니다.

