---
title: "Azure에서 PowerShell을 사용하여 Windows VM에 데이터 디스크 연결 | Microsoft Docs"
description: "Resource Manager 배포 모델에서 PowerShell을 사용하여 Windows VM에 신규 및 기존 데이터 디스크를 연결하는 방법을 설명합니다."
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
ms.date: 02/07/2017
ms.author: cynthn
translationtype: Human Translation
ms.sourcegitcommit: c0f96dfadb41aa05f922629820f29454d49d847a
ms.openlocfilehash: 0d78c93e90af0fa4c813ee53e4003c1f09442157


---

# <a name="attach-a-data-disk-to-a-windows-vm-using-powershell"></a>PowerShell을 사용하여 Windows VM에 데이터 디스크 연결

이 문서에서는 PowerShell을 사용하여 신규 및 기존 디스크를 Windows 가상 컴퓨터에 연결하는 방법을 보여 줍니다. VM에서 관리 디스크를 사용하는 경우 관리 데이터 디스크를 추가로 연결할 수 있습니다. 또한 저장소 계정의 비관리 디스크를 사용하는 VM에 비관리 데이터 디스크를 연결할 수 있습니다.

이 작업을 수행 하기 전에 다음 팁을 검토하세요.
* 가상 컴퓨터의 크기로 연결할 수 있는 디스크 개수가 제어됩니다. 자세한 내용은 [가상 컴퓨터의 크기](virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)를 참조하세요.
* Premium Storage를 사용하려면 DS 시리즈 또는 GS 시리즈 가상 컴퓨터처럼 Premium Storage 지원 VM 크기가 필요합니다. 이 가상 컴퓨터를 사용하여 프리미엄 및 표준 저장소 계정에서 모두 디스크를 사용할 수 있습니다. 프리미엄 저장소는 특정 지역에서만 사용할 수 있습니다. 자세한 내용은 [프리미엄 저장소: Azure 가상 컴퓨터 작업을 위한 고성능 저장소](../storage/storage-premium-storage.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)를 참조하세요.

## <a name="before-you-begin"></a>시작하기 전에
PowerShell을 사용하는 경우 AzureRM.Compute PowerShell 모듈이 최신 버전인지 확인합니다. 다음 명령을 실행하여 PowerShell을 설치합니다.

```powershell
Install-Module AzureRM.Compute -RequiredVersion 2.6.0
```
자세한 내용은 [Azure PowerShell 버전 관리](https://docs.microsoft.com/powershell/azureps-cmdlets-docs/#azure-powershell-versioning)를 참조하세요.


## <a name="add-an-empty-data-disk-to-a-virtual-machine"></a>가상 컴퓨터에 빈 데이터 디스크 추가

이 예에서는 기존 가상 컴퓨터에 빈 데이터 디스크를 추가하는 방법을 보여줍니다.

### <a name="using-managed-disks"></a>관리 디스크 사용

```powershell
$rgName = 'myResourceGroup'
$vmName = 'myVM'
$location = 'West Central US' 
$storageType = 'PremiumLRS'
$dataDiskName = $vmName + '_datadisk1'

$diskConfig = New-AzureRmDiskConfig -AccountType $storageType -Location $location -CreateOption Empty -DiskSizeGB 128

$dataDisk1 = New-AzureRmDisk -DiskName $dataDiskName -Disk $diskConfig -ResourceGroupName $rgName

$vm = Get-AzureRmVM -Name $vmName -ResourceGroupName $rgName 

$vm = Add-AzureRmVMDataDisk -VM $vm -Name $dataDiskName -CreateOption Attach -ManagedDiskId $dataDisk1.Id -Lun 1

Update-AzureRmVM -VM $vm -ResourceGroupName $rgName
```

### <a name="using-unmanaged-disks-in-a-storage-account"></a>저장소 계정에서 비관리 디스크 사용

```powershell
    $vm = Get-AzureRmVM -ResourceGroupName $rgName -Name $vmName
    Add-AzureRmVMDataDisk -VM $vm -Name "disk-name" -VhdUri "https://mystore1.blob.core.windows.net/vhds/datadisk1.vhd" -LUN 0 -Caching ReadWrite -DiskSizeinGB 1 -CreateOption Empty
    Update-AzureRmVM -ResourceGroupName $rgName -VM $vm
```


### <a name="initialize-the-disk"></a>디스크 초기화

빈 디스크를 추가한 후에는 디스크를 초기화해야 합니다. 디스크를 초기화하려면 VM에 로그인하여 디스크 관리를 사용합니다. VM을 만들 때 WinRM 및 인증서를 사용하도록 설정한 경우 원격 PowerShell을 사용하여 디스크를 초기화할 수 있습니다. 또한 사용자 지정 스크립트 확장을 사용할 수 있습니다. 

```powershell
    $location = "location-name"
    $scriptName = "script-name"
    $fileName = "script-file-name"
    Set-AzureRmVMCustomScriptExtension -ResourceGroupName $rgName -Location $locName -VMName $vmName -Name $scriptName -TypeHandlerVersion "1.4" -StorageAccountName "mystore1" -StorageAccountKey "primary-key" -FileName $fileName -ContainerName "scripts"
```
        
스크립트 파일은 디스크를 초기화하기 위해 이 코드와 같은 것을 포함할 수 있습니다.

```powershell
    $disks = Get-Disk | Where partitionstyle -eq 'raw' | sort number

    $letters = 70..89 | ForEach-Object { [char]$_ }
    $count = 0
    $labels = "data1","data2"

    foreach ($disk in $disks) {
        $driveLetter = $letters[$count].ToString()
        $disk | 
        Initialize-Disk -PartitionStyle MBR -PassThru |
        New-Partition -UseMaximumSize -DriveLetter $driveLetter |
        Format-Volume -FileSystem NTFS -NewFileSystemLabel $labels[$count] -Confirm:$false -Force
    $count++
    }
```


## <a name="attach-an-existing-data-disk-to-a-vm"></a>VM에 기존 데이터 디스크 추가

기존 VHD를 가상 컴퓨터에 관리 데이터 디스크로 연결할 수도 있습니다. 

### <a name="using-managed-disks"></a>관리 디스크 사용

```powershell
$rgName = 'myRG'
$vmName = 'ContosoMdPir3'
$location = 'West Central US' 
$storageType = 'PremiumLRS'
$dataDiskName = $vmName + '_datadisk2'
$dataVhdUri = 'https://mystorageaccount.blob.core.windows.net/vhds/managed_data_disk.vhd' 

$diskConfig = New-AzureRmDiskConfig -AccountType $storageType -Location $location -CreateOption Import -SourceUri $dataVhdUri -DiskSizeGB 128

$dataDisk2 = New-AzureRmDisk -DiskName $dataDiskName -Disk $diskConfig -ResourceGroupName $rgName

$vm = Get-AzureRmVM -Name $vmName -ResourceGroupName $rgName 

$vm = Add-AzureRmVMDataDisk -VM $vm -Name $dataDiskName -CreateOption Attach -ManagedDiskId $dataDisk2.Id -Lun 2

Update-AzureRmVM -VM $vm -ResourceGroupName $rgName
```

## <a name="next-steps"></a>다음 단계

[스냅숏](virtual-machines-windows-snapshot-copy-managed-disk.md)을 만듭니다.



<!--HONumber=Feb17_HO2-->


