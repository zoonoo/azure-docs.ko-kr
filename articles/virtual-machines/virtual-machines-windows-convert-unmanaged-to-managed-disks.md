---
title: "VM을 비관리 디스크에서 관리 디스크로 변환 - Azure | Microsoft Docs"
description: "Resource Manager 배포 모델에서 PowerShell을 사용하여 VM을 비관리 디스크에서 관리 디스크로 변환"
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
ms.date: 02/22/2017
ms.author: cynthn
translationtype: Human Translation
ms.sourcegitcommit: 4f2230ea0cc5b3e258a1a26a39e99433b04ffe18
ms.openlocfilehash: 96137d49d3caa0444400edcfcfaa097dc71ba8e3
ms.lasthandoff: 03/25/2017


---
# <a name="convert-a-vm-from-unmanaged-disks-to-managed-disks"></a>VM을 비관리 디스크에서 관리 디스크로 변환

저장소 계정에 비관리 디스크를 사용하는 기존 Azure VM이 있고 [관리 디스크](../storage/storage-managed-disks-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)를 활용하고 싶다면 VM을 변환하면 됩니다. 이 프로세스는 저장소 계정의 비관리 디스크를 사용하는 OS 디스크 및 연결된 데이터 디스크를 관리 디스크를 사용하도록 변환합니다. VM이 종료되고 할당이 취소되면 Powershell을 사용하여 관리 디스크를 사용하도록 VM을 변환할 수 있습니다. 변환 후 VM을 다시 시작하면 이제 VM이 관리 디스크를 사용합니다.

시작하기 전에 [Managed Disks로 마이그레이션하기 위한 계획](virtual-machines-windows-on-prem-to-azure.md#plan-for-the-migration-to-managed-disks)을 검토하세요.
마이그레이션 프로세스는 되돌릴 수 없으므로 프로덕션 환경에서 마이그레이션을 수행하기 전에 테스트 가상 컴퓨터를 마이그레이션하여 마이그레이션 프로세스를 테스트하세요.


> [!IMPORTANT] 
> 변환 중에 VM의 할당이 취소됩니다. VM의 할당이 취소된다는 것은 변환 후 시작하면 새 IP 주소를 갖게 된다는 의미입니다. 고정 IP에 종속된 경우 예약된 IP를 사용해야 합니다.


## <a name="managed-disks-and-azure-storage-service-encryption-sse"></a>Managed Disks 및 Azure SSE(저장소 서비스 암호화)

연결된 비관리 디스크가 [Azure SSE(저장소 서비스 암호화)](../storage/storage-service-encryption.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)를 사용하여 현재 암호화되었거나 이전에 암호화된 저장소 계정에 있는 경우 Resource Manager 배포 모델에 생성된 비관리 VM을 Managed Disks로 변환할 수 없습니다. 다음은 현재 암호화되었거나 이전에 암호화된 저장소 계정에 있는 비관리 VM을 변환하는 자세한 단계입니다.

**데이터 디스크**:
1.    VM에서 데이터 디스크를 분리합니다.
2.    SSE가 한 번도 설정되지 않은 저장소 계정에 VHD를 복사합니다. 다른 저장소 계정에 디스크를 복사하려면 [AzCopy](../storage/storage-use-azcopy.md):`AzCopy /Source:https://sourceaccount.blob.core.windows.net/mycontainer1 /Dest:https://destaccount.blob.core.windows.net/mycontainer2 /SourceKey:key1 /DestKey:key2 /Pattern:myDataDisk.vhd`을 사용합니다.
3.    복사한 디스크를 VM에 연결하고 VM을 변환합니다.

**OS 디스크**:
1.    할당 취소된 VM을 중지합니다. 필요한 경우 VM 구성을 저장합니다.
2.    SSE가 한 번도 설정되지 않은 저장소 계정에 OS VHD를 복사합니다. 다른 저장소 계정에 디스크를 복사하려면 [AzCopy](../storage/storage-use-azcopy.md):`AzCopy /Source:https://sourceaccount.blob.core.windows.net/mycontainer1 /Dest:https://destaccount.blob.core.windows.net/mycontainer2 /SourceKey:key1 /DestKey:key2 /Pattern:myVhd.vhd`을 사용합니다.
3.    관리 디스크를 사용하는 VM을 만들고 생성되는 동안 해당 VHD 파일을 OS 디스크로 연결합니다.

## <a name="convert-vms-in-an-availability-set-to-managed-disks-in-a-managed-availability-set"></a>가용성 집합의 VM을 관리 가용성 집합의 관리 디스크로 변환

관리 디스크로 변환하려는 VM이 가용성 집합에 있는 경우 먼저 가용성 집합을 관리 가용성 집합으로 변환해야 합니다.

다음은 가용성 집합을 관리 가용성 집합으로 업데이트한 후 디스크를 변환하고, 가용성 집합의 각 VM을 다시 시작하는 스크립트입니다.

```powershell
$rgName = 'myResourceGroup'
$avSetName = 'myAvailabilitySet'

$avSet =  Get-AzureRmAvailabilitySet -ResourceGroupName $rgName -Name $avSetName

Update-AzureRmAvailabilitySet -AvailabilitySet $avSet -Managed

foreach($vmInfo in $avSet.VirtualMachinesReferences)
    {
   $vm =  Get-AzureRmVM -ResourceGroupName $rgName | Where-Object {$_.Id -eq $vmInfo.id}

   Stop-AzureRmVM -ResourceGroupName $rgName -Name  $vm.Name -Force

   ConvertTo-AzureRmVMManagedDisk -ResourceGroupName $rgName -VMName $vm.Name
   
    }
```

## <a name="convert-existing-azure-vms-to-managed-disks-of-the-same-storage-type"></a>기존 Azure VM을 같은 저장소 유형의 관리 디스크로 변환

> [!IMPORTANT]
> 다음 절차를 수행하면 기본 /vhds 컨테이너에 Blob 하나가 남습니다. 이 파일의 이름은 “VMName.xxxxxxx.status”입니다. 이 파일은 VM에 [VM 확장](windows/classic/agents-and-extensions.md)을 설치한 경우에만 Azure에 의해 만들어집니다. 남아 있는 이 상태 개체를 삭제하지 마세요. 향후 조치를 통해 이 문제를 해결할 것입니다.

이 섹션에서는 같은 저장소 유형을 사용할 때 기존 Azure VM을 저장소 계정의 비관리 디스크에서 관리 디스크로 변환하는 방법을 다룹니다. 이 프로세스를 사용하여 프리미엄(SSD) 비관리 디스크에서 프리미엄 관리 디스크로 또는 표준(HDD) 비관리 디스크에서 표준 관리 디스크로 변환할 수 있습니다. 

1. 변수를 만들고 VM의 할당을 취소합니다. 이 예제에서는 리소스 그룹 이름을 **myResourceGroup**으로 설정하고 VM 이름을 **myVM**으로 설정합니다.

    ```powershell
    $rgName = "myResourceGroup"
    $vmName = "myVM"
    Stop-AzureRmVM -ResourceGroupName $rgName -Name $vmName -Force
    ```
   
    Azure 포털의 VM에 대한 *상태*가 **중지됨**에서 **중지됨(할당 취소됨)**으로 변경됩니다.
    
2. OS 디스크 및 데이터 디스크를 포함하여 VM과 연결된 모든 디스크를 변환합니다.

    ```powershell
    ConvertTo-AzureRmVMManagedDisk -ResourceGroupName $rgName -VMName $vmName
    ```


## <a name="migrate-existing-azure-vms-using-standard-unmanaged-disks-to-premium-managed-disks"></a>표준 비관리 디스크를 사용하는 기존 Azure VM을 프리미엄 관리 디스크로 마이그레이션

이 섹션에서는 표준 비관리 디스크의 기존 Azure VM을 프리미엄 관리 디스크로 변환하는 방법을 보여줍니다. 프리미엄 Managed Disks를 사용하려면 VM이 Premium Storage를 지원하는 [VM 크기](virtual-machines-windows-sizes.md)를 사용해야 합니다.


1.  먼저, 공통 매개 변수를 설정합니다. 선택한 [VM 크기](virtual-machines-windows-sizes.md)가 Premium Storage를 지원하는지 확인합니다.

    ```powershell
    $resourceGroupName = 'YourResourceGroupName'
    $vmName = 'YourVMName'
    $size = 'Standard_DS2_v2'
    ```
1.  비관리 디스크를 사용하는 VM 가져오기

    ```powershell
    $vm = Get-AzureRmVM -Name $vmName -ResourceGroupName $resourceGroupName
    ```
    
1.  VM을 중지(할당 취소)합니다.

    ```powershell
    Stop-AzureRmVM -ResourceGroupName $resourceGroupName -Name $vmName -Force
    ```

1.  VM 크기를 VM이 있는 지역에서 제공하는 Premium Storage 지원 가능 크기로 업데이트합니다.

    ```powershell
    $vm.HardwareProfile.VmSize = $size
    Update-AzureRmVM -VM $vm -ResourceGroupName $resourceGroupName
    ```

1.  비관리 디스크를 사용하는 가상 컴퓨터를 Managed Disks로 변환합니다. 

    내부 서버 오류가 발생하는 경우 2-3회 다시 시도해 보고 그래도 안 되면 지원 팀에 문의하세요.

    ```powershell
    ConvertTo-AzureRmVMManagedDisk -ResourceGroupName $resourceGroupName -VMName $vmName
    ```
1. VM을 중지(할당 취소)합니다.

    ```powershell
    Stop-AzureRmVM -ResourceGroupName $resourceGroupName -Name $vmName -Force
    ```
2.  모든 디스크를 Premium Storage로 업그레이드합니다.

    ```powershell
    $vmDisks = Get-AzureRmDisk -ResourceGroupName $resourceGroupName 
    foreach ($disk in $vmDisks) 
        {
        if($disk.OwnerId -eq $vm.Id)
            {
             $diskUpdateConfig = New-AzureRmDiskUpdateConfig –AccountType PremiumLRS
             Update-AzureRmDisk -DiskUpdate $diskUpdateConfig -ResourceGroupName $resourceGroupName `
             -DiskName $disk.Name
            }
        }
    ```
1. VM을 시작합니다.

    ```powershell
    Start-AzureRmVM -ResourceGroupName $resourceGroupName -Name $vmName
    ```
    
표준 저장소와 Premium Storage를 혼합해서 사용할 수도 있습니다.
    

## <a name="next-steps"></a>다음 단계

[스냅숏](virtual-machines-windows-snapshot-copy-managed-disk.md)을 사용하여 VM의 읽기 전용 복사본을 만듭니다.


