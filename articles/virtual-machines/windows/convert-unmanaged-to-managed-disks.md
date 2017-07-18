---
title: "Windows VM을 비관리 디스크에서 Managed Disks로 변환 - Azure | Microsoft Docs"
description: "Resource Manager 배포 모델에서 PowerShell을 사용하여 Windows VM을 비관리 디스크에서 Azure Managed Disks로 변환"
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 06/23/2017
ms.author: cynthn
ms.translationtype: Human Translation
ms.sourcegitcommit: 6efa2cca46c2d8e4c00150ff964f8af02397ef99
ms.openlocfilehash: 636d4f7c5da72973a7837718cfb42dda93bba2cc
ms.contentlocale: ko-kr
ms.lasthandoff: 07/01/2017

---

# <a name="convert-a-windows-vm-from-unmanaged-disks-to-azure-managed-disks"></a>Windows VM을 비관리 디스크에서 Azure Managed Disks로 변환

비관리 디스크를 사용하는 기존 Windows VM(가상 컴퓨터)이 있는 경우 [Azure Managed Disks](../../storage/storage-managed-disks-overview.md)를 사용하도록 VM을 변환할 수 있습니다. 이 프로세스는 OS 디스크와 연결된 데이터 디스크를 변환합니다.

이 문서에서는 Azure PowerShell을 사용하여 VM을 변환하는 방법을 보여 줍니다. 설치 또는 업그레이드해야 하는 경우 [Azure PowerShell 설치 및 구성](/powershell/azure/install-azurerm-ps.md)을 참조하세요.

## <a name="before-you-begin"></a>시작하기 전에


* [Managed Disks로 마이그레이션 계획 수립](on-prem-to-azure.md#plan-for-the-migration-to-managed-disks)을 검토합니다.

[!INCLUDE [virtual-machines-common-convert-disks-considerations](../../../includes/virtual-machines-common-convert-disks-considerations.md)]




## <a name="convert-single-instance-vms"></a>단일 인스턴스 VM 변환
이 섹션에서는 단일 인스턴스 Azure VM을 비관리 디스크에서 Managed Disks로 변환하는 방법을 설명합니다. VM이 가용성 집합에 있는 경우 다음 섹션을 참조하세요. 

1. [Stop-AzureRmVM](/powershell/module/azurerm.compute/stop-azurermvm) cmdlet을 사용하여 VM을 할당 취소합니다. 다음 예제에서는 리소스 그룹 `myResourceGroup`에서 `myVM`이라는 VM의 할당을 취소합니다. 

  ```powershell
  $rgName = "myResourceGroup"
  $vmName = "myVM"
  Stop-AzureRmVM -ResourceGroupName $rgName -Name $vmName -Force
  ```

2. [ConvertTo-AzureRmVMManagedDisk](/powershell/module/azurerm.compute/convertto-azurermvmmanageddisk) cmdlet을 사용하여 VM을 Managed Disks로 변환합니다. 다음 프로세스는 OS 디스크 및 데이터 디스크를 포함하여 이전 VM을 변환합니다.

  ```powershell
  ConvertTo-AzureRmVMManagedDisk -ResourceGroupName $rgName -VMName $vmName
  ```

3. [Start-AzureRmVM](/powershell/module/azurerm.compute/start-azurermvm)을 사용하여 Managed Disks로 변환한 후 VM을 시작합니다. 다음 예제에서는 이전 VM을 다시 시작합니다.

  ```powershell
  Start-AzureRmVM -ResourceGroupName $rgName -Name $vmName
  ```


## <a name="convert-vms-in-an-availability-set"></a>가용성 집합의 VM 변환

관리되는 디스크로 변환하려는 VM이 가용성 집합에 있는 경우 먼저 가용성 집합을 관리되는 가용성 집합으로 변환해야 합니다.

1. [Update-AzureRmAvailabilitySet](/powershell/module/azurerm.compute/update-azurermavailabilityset) cmdlet을 사용하여 가용성 집합을 변환합니다. 다음 예제에서는 리소스 그룹 `myResourceGroup`의 가용성 집합 `myAvailabilitySet`을 업데이트합니다.

  ```powershell
  $rgName = 'myResourceGroup'
  $avSetName = 'myAvailabilitySet'

  $avSet = Get-AzureRmAvailabilitySet -ResourceGroupName $rgName -Name $avSetName
  Update-AzureRmAvailabilitySet -AvailabilitySet $avSet -Sku Aligned 
  ```

  가용성 집합이 있는 지역에 관리되는 장애 도메인은 2개뿐이고 관리되지 않는 장애 도메인은 3개인 경우 이 명령은 "지정된 장애 도메인 수 3은 1~2 범위에 있어야 합니다."와 유사한 오류를 표시합니다. 오류를 해결하려면 다음과 같이 장애 도메인을 2로 업데이트하고 `Sku`를 `Aligned`로 업데이트합니다.

  ```powershell
  $avSet.PlatformFaultDomainCount = 2
  Update-AzureRmAvailabilitySet -AvailabilitySet $avSet -Sku Aligned
  ```

2. 가용성 집합의 VM을 할당 취소하고 변환합니다. 다음 스크립트는 [Stop-AzureRmVM](/powershell/module/azurerm.compute/stop-azurermvm) cmdlet을 사용하여 각 VM을 할당 취소하고 [ConvertTo-AzureRmVMManagedDisk](/powershell/module/azurerm.compute/convertto-azurermvmmanageddisk)를 사용하여 변환한 다음 [Start-AzureRmVM](/powershell/module/azurerm.compute/start-azurermvm)을 사용하여 다시 시작합니다.

  ```powershell
  $avSet = Get-AzureRmAvailabilitySet -ResourceGroupName $rgName -Name $avSetName

  foreach($vmInfo in $avSet.VirtualMachinesReferences)
  {
     $vm = Get-AzureRmVM -ResourceGroupName $rgName | Where-Object {$_.Id -eq $vmInfo.id}
     Stop-AzureRmVM -ResourceGroupName $rgName -Name $vm.Name -Force
     ConvertTo-AzureRmVMManagedDisk -ResourceGroupName $rgName -VMName $vm.Name
     Start-AzureRmVM -ResourceGroupName $rgName -Name $vmName
  }
  ```


## <a name="convert-standard-managed-disks-to-premium"></a>표준 Managed Disks를 Premium으로 변환
VM을 Managed Disks로 변환한 후 저장소 유형 간에 전환할 수도 있습니다. 표준 저장소와 Premium Storage를 혼합해서 사용할 수도 있습니다. 다음 예제에서는 표준에서 Premium Storage로 전환하는 방법을 보여 줍니다. 프리미엄 Managed Disks를 사용하려면 VM이 Premium Storage를 지원하는 [VM 크기](sizes.md)를 사용해야 합니다. 또한 이 예제에서는 Premium Storage를 지원하는 크기로 전환합니다.

```powershell
$rgName = 'myResourceGroup'
$vmName = 'YourVM'
$size = 'Standard_DS2_v2'
$vm = Get-AzureRmVM -Name $vmName -rgName $resourceGroupName

# Stop deallocate the VM before changing the size
Stop-AzureRmVM -ResourceGroupName $rgName -Name $vmName -Force

# Change VM size to a size supporting Premium storage
$vm.HardwareProfile.VmSize = $size
Update-AzureRmVM -VM $vm -ResourceGroupName $rgName

# Get all disks in the resource group of the VM
$vmDisks = Get-AzureRmDisk -ResourceGroupName $rgName 

# For disks that belong to the VM selected, convert to Premium storage
foreach ($disk in $vmDisks)
{
    if ($disk.OwnerId -eq $vm.Id)
    {
        $diskUpdateConfig = New-AzureRmDiskUpdateConfig –AccountType PremiumLRS
        Update-AzureRmDisk -DiskUpdate $diskUpdateConfig -ResourceGroupName $rgName `
        -DiskName $disk.Name
    }
}

Start-AzureRmVM -ResourceGroupName $rgName -Name $vmName
```

## <a name="troubleshooting"></a>문제 해결

변환하는 동안 오류가 발생한 경우 또는 이전 변환에서 문제로 인해 VM 상태가 실패함인 경우 `ConvertTo-AzureRmVMManagedDisk` cmdlet을 다시 실행합니다. 다시 시도만으로 상황이 해결되는 경우가 많습니다.


## <a name="managed-disks-and-azure-storage-service-encryption"></a>Managed Disks 및 Azure Storage 서비스 암호화

[Azure Storage 서비스 암호화](../../storage/storage-service-encryption.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)를 사용하여 암호화된 적이 있는 저장소 계정에 비관리 디스크가 있는 경우 앞의 단계를 사용하여 비관리 디스크를 관리 디스크로 변환할 수 없습니다. 다음 단계에서는 암호화된 저장소 계정에 있는 비관리 디스크를 복사하고 사용하는 방법을 자세히 설명합니다.

1. [AzCopy](../../storage/storage-use-azcopy.md)를 사용하여 Azure Storage 서비스 암호화를 사용하도록 설정되지 않은 저장소 계정으로 VHD(가상 하드 디스크)를 복사합니다.

2. 복사된 VM을 다음 방법 중 하나로 사용합니다.

  * Managed Disks를 사용하는 VM을 만들고 `New-AzureRmVm`로 생성되는 동안 해당 VHD 파일을 지정합니다.

  * `Add-AzureRmVmDataDisk`로 복사된 VHD를 관리되는 디스크가 있는 실행 중인 VM에 연결합니다.


## <a name="next-steps"></a>다음 단계

[스냅숏](snapshot-copy-managed-disk.md)을 사용하여 VM의 읽기 전용 복사본을 만듭니다.


