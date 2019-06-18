---
title: Azure에서 PowerShell을 사용하여 Windows VM 크기 조정 | Microsoft Docs
description: Azure Powershell을 사용하여 Resource Manager 배포 모델에서 만든 Windows 가상 머신의 크기를 조정합니다.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 057ff274-6dad-415e-891c-58f8eea9ed78
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 05/30/2018
ms.author: cynthn
ms.openlocfilehash: 3dc6ea7419e3ee1f24d19447a7a33e5556ffc70f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "64688715"
---
# <a name="resize-a-windows-vm"></a>Windows VM 크기 조정

이 아티클에서는 Azure Powershell을 사용하여 VM을 다른 [VM 크기](sizes.md)로 전환하는 방법을 보여줍니다.

VM(가상 머신)을 만든 후 VM 크기를 변경하여 VM의 크기를 확장 또는 축소할 수 있습니다. 경우에 따라 먼저 VM의 할당을 취소해야 합니다. 이는 현재 VM을 호스트하는 하드웨어 클러스터에서 새 크기를 사용할 수 없는 경우에 발생할 수 있습니다.

VM에서 Premium Storage를 사용하는 경우 크기의 **s** 버전을 선택하여 Premium Storage 지원을 받는지 확인합니다. 예를 들어 Standard_E4_v3 대신 Standard_E4**s**_v3을 선택합니다.

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]

## <a name="resize-a-windows-vm-not-in-an-availability-set"></a>가용성 집합에 없는 Windows VM의 크기 조정

일부 변수를 설정합니다. 사용자 고유의 정보로 값을 대체합니다.

```powershell
$resourceGroup = "myResourceGroup"
$vmName = "myVM"
```

VM이 호스트되는 하드웨어 클러스터에서 사용할 수 있는 VM 크기를 나열합니다. 
   
```powershell
Get-AzVMSize -ResourceGroupName $resourceGroup -VMName $vmName 
```

원하는 크기가 목록에 나열된 경우 다음 명령을 실행하여 VM 크기를 조정합니다. 원하는 크기가 목록에 나열되지 않으면 3단계로 이동합니다.
   
```powershell
$vm = Get-AzVM -ResourceGroupName $resourceGroup -VMName $vmName
$vm.HardwareProfile.VmSize = "<newVMsize>"
Update-AzVM -VM $vm -ResourceGroupName $resourceGroup
```

원하는 크기가 나열되지 않은 경우에는 다음 명령을 실행하여 VM의 할당을 취소하고 크기를 조정한 다음, VM을 다시 시작합니다. 바꿉니다  **\<newVMsize >** 원하는 크기를 사용 하 여 합니다.
   
```powershell
Stop-AzVM -ResourceGroupName $resourceGroup -Name $vmName -Force
$vm = Get-AzVM -ResourceGroupName $resourceGroup -VMName $vmName
$vm.HardwareProfile.VmSize = "<newVMSize>"
Update-AzVM -VM $vm -ResourceGroupName $resourceGroup
Start-AzVM -ResourceGroupName $resourceGroup -Name $vmName
```

> [!WARNING]
> VM의 할당이 취소되면 VM에 할당된 모든 동적 IP 주소가 해제됩니다. OS 및 데이터 디스크는 영향을 받지 않습니다. 
> 
> 

## <a name="resize-a-windows-vm-in-an-availability-set"></a>가용성 집합에 있는 Windows VM의 크기 조정

가용성 집합에서 VM에 대한 새 크기를 현재 VM을 호스트하는 하드웨어 클러스터에서 사용할 수 없는 경우 가용성 집합의 모든 VM을 할당 취소하여 VM 크기를 조정해야 합니다. 또한 VM 크기를 조정한 후 가용성 집합에서 다른 VM의 크기를 업데이트해야 할 수 있습니다. 가용성 집합에서 VM의 크기를 조정하려면 다음 단계를 수행합니다.

```powershell
$resourceGroup = "myResourceGroup"
$vmName = "myVM"
```

VM이 호스트되는 하드웨어 클러스터에서 사용할 수 있는 VM 크기를 나열합니다. 
   
```powershell
Get-AzVMSize -ResourceGroupName $resourceGroup -VMName $vmName 
```

원하는 크기가 목록에 나열된 경우 다음 명령을 실행하여 VM 크기를 조정합니다. 나열되지 않으면 다음 섹션으로 이동합니다.
   
```powershell
$vm = Get-AzVM -ResourceGroupName $resourceGroup -VMName $vmName 
$vm.HardwareProfile.VmSize = "<newVmSize>"
Update-AzVM -VM $vm -ResourceGroupName $resourceGroup
```
    
원하는 크기가 목록에 나열되지 않는 경우 다음 단계를 진행하여 가용성 집합의 모든 VM을 할당 취소하고 VM 크기를 조정한 후 다시 시작합니다.

가용성 집합의 VM을 모두 중지합니다.
   
```powershell
$as = Get-AzAvailabilitySet -ResourceGroupName $resourceGroup
$vmIds = $as.VirtualMachinesReferences
foreach ($vmId in $vmIDs){
    $string = $vmID.Id.Split("/")
    $vmName = $string[8]
    Stop-AzVM -ResourceGroupName $resourceGroup -Name $vmName -Force
    } 
```

가용성 집합의 VM을 크기 조정하고 다시 시작합니다.
   
```powershell
$newSize = "<newVmSize>"
$as = Get-AzAvailabilitySet -ResourceGroupName $resourceGroup
$vmIds = $as.VirtualMachinesReferences
  foreach ($vmId in $vmIDs){
    $string = $vmID.Id.Split("/")
    $vmName = $string[8]
    $vm = Get-AzVM -ResourceGroupName $resourceGroup -Name $vmName
    $vm.HardwareProfile.VmSize = $newSize
    Update-AzVM -ResourceGroupName $resourceGroup -VM $vm
    Start-AzVM -ResourceGroupName $resourceGroup -Name $vmName
    }
```

## <a name="next-steps"></a>다음 단계

확장성을 높이기 위해서는 여러 VM 인스턴스를 실행하고 규모를 확장합니다. 자세한 내용은 [Virtual Machine Scale Set에서 Windows 컴퓨터 자동 확장](../../virtual-machine-scale-sets/virtual-machine-scale-sets-windows-autoscale.md)을 참조하세요.

