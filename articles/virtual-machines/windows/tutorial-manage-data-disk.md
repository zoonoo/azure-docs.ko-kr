---
title: "Azure PowerShell을 사용하여 Azure 디스크 관리 | Microsoft Docs"
description: "자습서 - Azure PowerShell을 사용하여 Azure 디스크 관리"
services: virtual-machines-windows
documentationcenter: virtual-machines
author: neilpeterson
manager: timlt
editor: tysonn
tags: azure-service-management
ms.assetid: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 05/02/2017
ms.author: nepeters
ms.translationtype: Human Translation
ms.sourcegitcommit: 5e92b1b234e4ceea5e0dd5d09ab3203c4a86f633
ms.openlocfilehash: a7511a35a7b186fc424088e7ff5cbc933d325712
ms.contentlocale: ko-kr
ms.lasthandoff: 05/10/2017

---

# <a name="manage-azure-disks-with-powershell"></a>PowerShell을 사용하여 Azure 디스크 관리

Azure 가상 컴퓨터는 디스크를 사용하여 VM 운영 체제, 응용 프로그램 및 데이터를 저장합니다. VM을 만들 때 예상되는 워크로드에 적합한 디스크 크기와 구성을 선택하는 것이 중요합니다. 이 자습서에서는 VM 디스크의 배포 및 관리에 대해 다룹니다. 다음에 대해 알아봅니다.

> [!div class="checklist"]
> * OS 디스크 및 임시 디스크
> * 데이터 디스크
> * 표준 및 프리미엄 디스크
> * 디스크 성능
> * 데이터 디스크 연결 및 준비

이 자습서에는 Azure PowerShell 모듈 버전 3.6 이상이 필요합니다. ` Get-Module -ListAvailable AzureRM`을 실행하여 버전을 찾습니다. 업그레이드해야 하는 경우 [Azure PowerShell 모듈 설치](/powershell/azure/install-azurerm-ps)를 참조하세요.

## <a name="default-azure-disks"></a>기본 Azure 디스크

Azure Virtual Machine을 만들면 두 개의 디스크가 자동으로 가상 컴퓨터에 연결됩니다. 

**운영 체제 디스크** - 운영 체제 디스크는 최대 1TB까지 가능하며 VM 운영 체제를 호스트합니다.  OS 디스크는 기본적으로 *c:* 드라이브 문자가 할당됩니다. OS 디스크의 디스크 캐싱 구성은 OS 성능에 맞게 최적화됩니다. OS 디스크는 응용 프로그램 또는 데이터를 호스트해서는 **안 됩니다**. 응용 프로그램 및 데이터는 데이터 디스크를 사용하며 여기에 대해서는 이 문서의 뒷부분에서 자세히 설명합니다.

**임시 디스크** - 임시 디스크는 VM과 같은 Azure 호스트에 있는 반도체 드라이브를 사용합니다. 임시 디스크는 성능이 높고 임시 데이터 처리 등의 작업에 사용할 수 있습니다. 그러나 VM이 새 호스트로 이동되면 임시 디스크에 저장된 모든 데이터는 제거됩니다. 임시 디스크의 크기는 VM 크기에 따라 결정됩니다. 임시 디스크는 기본적으로 *d:* 드라이브 문자가 할당됩니다.

### <a name="temporary-disk-sizes"></a>임시 디스크 크기

| 형식 | VM 크기 | 최대 임시 디스크 크기(GB) |
|----|----|----|
| [범용](sizes-general.md) | A 및 D 시리즈 | 800 |
| [Compute에 최적화](sizes-compute.md) | F 시리즈 | 800 |
| [메모리에 최적화](../virtual-machines-windows-sizes-memory.md) | D 및 G 시리즈 | 6144 |
| [Storage에 최적화](../virtual-machines-windows-sizes-storage.md) | L 시리즈 | 5630 |
| [GPU](sizes-gpu.md) | N 시리즈 | 1,440 |
| [고성능](sizes-hpc.md) | A 및 H 시리즈 | 2000 |

## <a name="azure-data-disks"></a>Azure 데이터 디스크

응용 프로그램을 설치하고 데이터를 저장하기 위해 데이터 디스크를 더 추가할 수 있습니다. 데이터 디스크는 지속형 및 반응형 데이터 저장소가 필요한 상황에 사용해야 합니다. 각 데이터 디스크의 최대 용량은 1테라바이트입니다. 가상 컴퓨터의 크기에 따라 VM에 연결할 수 있는 데이터 디스크 수가 결정됩니다. 각 VM 코어에 대해 두 개의 데이터 디스크를 연결할 수 있습니다. 

### <a name="max-data-disks-per-vm"></a>VM당 최대 데이터 디스크 수

| 형식 | VM 크기 | VM당 최대 데이터 디스크 수 |
|----|----|----|
| [범용](sizes-general.md) | A 및 D 시리즈 | 32 |
| [Compute에 최적화](sizes-compute.md) | F 시리즈 | 32 |
| [메모리에 최적화](../virtual-machines-windows-sizes-memory.md) | D 및 G 시리즈 | 64 |
| [Storage에 최적화](../virtual-machines-windows-sizes-storage.md) | L 시리즈 | 64 |
| [GPU](sizes-gpu.md) | N 시리즈 | 48 |
| [고성능](sizes-hpc.md) | A 및 H 시리즈 | 32 |

## <a name="vm-disk-types"></a>VM 디스크 유형

Azure는 두 가지 유형의 디스크를 제공합니다.

### <a name="standard-disk"></a>표준 디스크

Standard Storage는 HDD에 의해 지원되며 성능은 그대로이면서 비용 효율적인 저장소를 제공합니다. 표준 디스크는 비용 효율적인 개발 및 테스트 워크로드에 적합합니다.

### <a name="premium-disk"></a>프리미엄 디스크

프리미엄 디스크는 SSD 기반 고성능의 대기 시간이 짧은 디스크에서 지원합니다. 프로덕션 워크로드를 실행하는 VM에 완벽한 디스크입니다. Premium Storage는 DS 시리즈, DSv2 시리즈, GS 시리즈 및 FS 시리즈 VM을 지원합니다. 프리미엄 디스크는 세 가지 유형(P10, P20 P30)으로 제공되며 디스크 크기에 따라 디스크 유형이 결정됩니다. 디스크 유형 선택 시 디스크 크기 값은 다음 유형으로 반올림됩니다. 예를 들어 크기가 128GB 미만인 경우 디스크 유형은 P10, 129~512GB인 경우 P20, 512GB가 넘으면 P30이 됩니다. 

### <a name="premium-disk-performance"></a>프리미엄 디스크 성능

|Premium Storage 디스크 유형 | P10 | P20 | P30 |
| --- | --- | --- | --- |
| 디스크 크기(반올림) | 128GB | 512GB | 1,024GB(1TB) |
| 디스크당 IOPS | 500 | 2,300 | 5,000 |
디스크당 처리량 | 100MB/초 | 150MB/초 | 200MB/s |

위의 표에 디스크당 최대 IOPS가 나와 있지만 여러 데이터 디스크를 스트라이프하여 더 높은 수준의 성능을 구현할 수 있습니다. 예를 들어 64 데이터 디스크는 Standard_GS5 VM에 연결할 수 있습니다. 이러한 각 디스크는 P30에 해당하는 크기이며 최대 80,000 IOPS를 얻을 수 있습니다. VM당 최대 IOPS에 대한 자세한 내용은 [Linux VM 크기](./sizes.md)를 참조하세요.

## <a name="create-and-attach-disks"></a>디스크 만들기 및 연결

이 자습서의 예제를 완료하려면 기존 가상 컴퓨터가 있어야 합니다. 필요한 경우 이 [스크립트 샘플](../scripts/virtual-machines-windows-powershell-sample-create-vm.md)을 사용하여 가상 컴퓨터를 만들 수 있습니다. 이 자습서를 진행할 때 필요한 경우 리소스 그룹 및 VM 이름을 바꿉니다.

[New-AzureRmDiskConfig](/powershell/module/azurerm.compute/new-azurermdiskconfig)를 사용하여 초기 구성을 만듭니다. 다음 예제에서는 크기가 128GB인 디스크를 구성합니다.

```powershell
$diskConfig = New-AzureRmDiskConfig -Location EastUS -CreateOption Empty -DiskSizeGB 128
```

[New-AzureRmDisk](/powershell/module/azurerm.compute/new-azurermdisk) 명령을 사용하여 데이터 디스크를 만듭니다.

```powershell
$dataDisk = New-AzureRmDisk -ResourceGroupName myResourceGroup -DiskName myDataDisk -Disk $diskConfig
```

[Get-AzureRmVM](/powershell/module/azurerm.compute/get-azurermvm) 명령을 사용하여 데이터 디스크를 추가할 가상 컴퓨터를 가져옵니다.

```powershell
$vm = Get-AzureRmVM -ResourceGroupName myResourceGroup -Name myVM
```

[Add-AzureRmVMDataDisk](/powershell/module/azurerm.compute/add-azurermvmdatadisk) 명령을 사용하여 데이터 디스크를 가상 컴퓨터 구성에 추가합니다.

```powershell
$vm = Add-AzureRmVMDataDisk -VM $vm -Name myDataDisk -CreateOption Attach -ManagedDiskId $dataDisk.Id -Lun 1
```

[Update-AzureRmVM](/powershell/module/azurerm.compute/add-azurermvmdatadisk) 명령을 사용하여 가상 컴퓨터를 업데이트합니다.

```powershell
Update-AzureRmVM -ResourceGroupName myResourceGroup -VM $vm
```

## <a name="prepare-data-disks"></a>데이터 디스크 준비

디스크가 가상 컴퓨터에 연결된 후 디스크를 사용하도록 운영 체제를 구성해야 합니다. 다음 예제에서는 VM에 추가된 첫 번째 디스크를 수동으로 구성하는 방법을 보여 줍니다. [사용자 지정 스크립트 확장](./tutorial-automate-vm-deployment.md)을 사용하여 이 프로세스를 자동화할 수도 있습니다.

### <a name="manual-configuration"></a>수동 구성

가상 컴퓨터와 RDP 연결 만들기 PowerShell을 열고 이 스크립트를 실행합니다.

```powershell
Get-Disk | Where partitionstyle -eq 'raw' | `
Initialize-Disk -PartitionStyle MBR -PassThru | `
New-Partition -AssignDriveLetter -UseMaximumSize | `
Format-Volume -FileSystem NTFS -NewFileSystemLabel "myDataDisk" -Confirm:$false
```

## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음과 같은 VM 디스크 항목에 대해 알아보았습니다.

> [!div class="checklist"]
> * OS 디스크 및 임시 디스크
> * 데이터 디스크
> * 표준 및 프리미엄 디스크
> * 디스크 성능
> * 데이터 디스크 연결 및 준비

VM 구성 자동화에 대해 자세히 알아보려면 다음 자습서로 이동합니다.

> [!div class="nextstepaction"]
> [VM 구성 자동화](./tutorial-automate-vm-deployment.md)

