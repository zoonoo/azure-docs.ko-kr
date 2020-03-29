---
title: '파워쉘: 근접 배치 그룹 사용'
description: Azure PowerShell을 사용하여 근접 배치 그룹을 만들고 사용하는 방법에 대해 알아봅니다.
services: virtual-machines
ms.service: virtual-machines
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 01/27/2020
ms.author: cynthn
ms.openlocfilehash: f69e245d72a63b942896cdd9f4a2225cb4c1706d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78208528"
---
# <a name="deploy-vms-to-proximity-placement-groups-using-powershell"></a>PowerShell을 사용하여 근접 배치 그룹에 VM 배포


VM을 가능한 한 가깝게 설정하여 가능한 가장 낮은 대기 시간을 달성하려면 [근접 배치 그룹](co-location.md#proximity-placement-groups)내에 배포해야 합니다.

근접 배치 그룹은 Azure 계산 리소스가 물리적으로 서로 가까이 있는지 확인하는 데 사용되는 논리적 그룹입니다. 근접 배치 그룹은 대기 시간이 짧은 워크로드에 유용합니다.


## <a name="create-a-proximity-placement-group"></a>근접 배치 그룹 만들기
[새 Az근접배치그룹](https://docs.microsoft.com/powershell/module/az.compute/new-azproximityplacementgroup) cmdlet을 사용하여 근접 배치 그룹을 작성합니다. 

```azurepowershell-interactive
$resourceGroup = "myPPGResourceGroup"
$location = "East US"
$ppgName = "myPPG"
New-AzResourceGroup -Name $resourceGroup -Location $location
$ppg = New-AzProximityPlacementGroup `
   -Location $location `
   -Name $ppgName `
   -ResourceGroupName $resourceGroup `
   -ProximityPlacementGroupType Standard
```

## <a name="list-proximity-placement-groups"></a>근접 배치 그룹 목록

[Get-AzProximityPlacementGroup](/powershell/module/az.compute/get-azproximityplacementgroup) cmdlet을 사용하여 모든 근접 배치 그룹을 나열할 수 있습니다.

```azurepowershell-interactive
Get-AzProximityPlacementGroup
```


## <a name="create-a-vm"></a>VM 만들기

`-ProximityPlacementGroup $ppg.Id` [New-AzVM을](https://docs.microsoft.com/powershell/module/az.compute/new-azvm) 사용하여 VM을 만들 때 근접 배치 그룹 ID를 참조하여 근접 배치 그룹에서 VM을 만듭니다.

```azurepowershell-interactive
$vmName = "myVM"

New-AzVm `
  -ResourceGroupName $resourceGroup `
  -Name $vmName `
  -Location $location `
  -OpenPorts 3389 `
  -ProximityPlacementGroup $ppg.Id
```

위치 그룹에서 [Get-Az근접 배치그룹을](/powershell/module/az.compute/get-azproximityplacementgroup)사용하여 VM을 볼 수 있습니다.

```azurepowershell-interactive
Get-AzProximityPlacementGroup -ResourceId $ppg.Id |
    Format-Table -Property VirtualMachines -Wrap
```

### <a name="move-an-existing-vm-into-a-proximity-placement-group"></a>기존 VM을 근접 배치 그룹으로 이동

근접 배치 그룹에 기존 VM을 추가할 수도 있습니다. 먼저 VM 할당 할당 을 중지한 다음 VM을 업데이트하고 다시 시작해야 합니다.

```azurepowershell-interactive
$ppg = Get-AzProximityPlacementGroup -ResourceGroupName myPPGResourceGroup -Name myPPG
$vm = Get-AzVM -ResourceGroupName myResourceGroup -Name myVM
Stop-AzVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName
Update-AzVM -VM $vm -ResourceGroupName $vm.ResourceGroupName -ProximityPlacementGroupId $ppg.Id
Start-AzVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName
```

### <a name="move-an-existing-vm-out-of-a-proximity-placement-group"></a>기존 VM을 근접 배치 그룹에서 이동

근접 배치 그룹에서 VM을 제거하려면 먼저 VM 을 삭제한 다음 VM을 업데이트하고 다시 시작해야 합니다.

```azurepowershell-interactive
$ppg = Get-AzProximityPlacementGroup -ResourceGroupName myPPGResourceGroup -Name myPPG
$vm = Get-AzVM -ResourceGroupName myResourceGroup -Name myVM
Stop-AzVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName
$vm.ProximityPlacementGroup = ""
Update-AzVM -VM $vm -ResourceGroupName $vm.ResourceGroupName 
Start-AzVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName
```


## <a name="availability-sets"></a>가용성 집합
근접 배치 그룹에서 가용성 집합을 만들 수도 있습니다. `-ProximityPlacementGroup` [New-AzAvailabilitySet](/powershell/module/az.compute/new-azavailabilityset) cmdlet과 동일한 매개 변수를 사용하여 가용성 집합을 만들고 가용성 집합에서 생성된 모든 VM도 동일한 근접 배치 그룹에서 만들어집니다.

근접 배치 그룹에 기존 가용성 집합을 추가하거나 제거하려면 먼저 가용성 집합의 모든 VM을 중지해야 합니다. 

### <a name="move-an-existing-availability-set-into-a-proximity-placement-group"></a>기존 가용성 세트를 근접 배치 그룹으로 이동

```azurepowershell-interactive
$resourceGroup = "myResourceGroup"
$avSetName = "myAvailabilitySet"
$avSet = Get-AzAvailabilitySet -ResourceGroupName $resourceGroup -Name $avSetName
$vmIds = $avSet.VirtualMachinesReferences
foreach ($vmId in $vmIDs){
    $string = $vmID.Id.Split("/")
    $vmName = $string[8]
    Stop-AzVM -ResourceGroupName $resourceGroup -Name $vmName -Force
    } 

$ppg = Get-AzProximityPlacementGroup -ResourceGroupName myPPG -Name myPPG
Update-AzAvailabilitySet -AvailabilitySet $avSet -ProximityPlacementGroupId $ppg.Id
foreach ($vmId in $vmIDs){
    $string = $vmID.Id.Split("/")
    $vmName = $string[8]
    Start-AzVM -ResourceGroupName $resourceGroup -Name $vmName 
    } 
```

### <a name="move-an-existing-availability-set-out-of-a-proximity-placement-group"></a>근접 배치 그룹에서 설정된 기존 가용성 이동

```azurepowershell-interactive
$resourceGroup = "myResourceGroup"
$avSetName = "myAvailabilitySet"
$avSet = Get-AzAvailabilitySet -ResourceGroupName $resourceGroup -Name $avSetName
$vmIds = $avSet.VirtualMachinesReferences
foreach ($vmId in $vmIDs){
    $string = $vmID.Id.Split("/")
    $vmName = $string[8]
    Stop-AzVM -ResourceGroupName $resourceGroup -Name $vmName -Force
    } 

$avSet.ProximityPlacementGroup = ""
Update-AzAvailabilitySet -AvailabilitySet $avSet 
foreach ($vmId in $vmIDs){
    $string = $vmID.Id.Split("/")
    $vmName = $string[8]
    Start-AzVM -ResourceGroupName $resourceGroup -Name $vmName 
    } 
```

## <a name="scale-sets"></a>확장 집합

근접 배치 그룹에서 배율 세트를 만들 수도 있습니다. `-ProximityPlacementGroup` [New-AzVmss와](https://docs.microsoft.com/powershell/module/az.compute/new-azvmss) 동일한 매개 변수를 사용하여 배율 집합을 만들면 모든 인스턴스가 동일한 근접 배치 그룹에서 만들어집니다.


근접 배치 그룹에 기존 축척 세트를 추가하거나 제거하려면 먼저 축척 세트를 중지해야 합니다. 

### <a name="move-an-existing-scale-set-into-a-proximity-placement-group"></a>기존 축척 세트를 근접 배치 그룹으로 이동

```azurepowershell-interactive
$ppg = Get-AzProximityPlacementGroup -ResourceGroupName myPPG -Name myPPG
$vmss = Get-AzVmss -ResourceGroupName myVMSSResourceGroup -VMScaleSetName myScaleSet
Stop-AzVmss -VMScaleSetName $vmss.Name -ResourceGroupName $vmss.ResourceGroupName
Update-AzVmss -VMScaleSetName $vmss.Name -ResourceGroupName $vmss.ResourceGroupName -ProximityPlacementGroupId $ppg.Id
Start-AzVmss -VMScaleSetName $vmss.Name -ResourceGroupName $vmss.ResourceGroupName
```

### <a name="move-an-existing-scale-set-out-of-a-proximity-placement-group"></a>근접 배치 그룹에서 설정된 기존 축척 이동

```azurepowershell-interactive
$vmss = Get-AzVmss -ResourceGroupName myVMSSResourceGroup -VMScaleSetName myScaleSet
Stop-AzVmss -VMScaleSetName $vmss.Name -ResourceGroupName $vmss.ResourceGroupName
$vmss.ProximityPlacementGroup = ""
Update-AzVmss -VirtualMachineScaleSet $vmss -VMScaleSetName $vmss.Name -ResourceGroupName $vmss.ResourceGroupName  
Start-AzVmss -VMScaleSetName $vmss.Name -ResourceGroupName $vmss.ResourceGroupName
```

## <a name="next-steps"></a>다음 단계

[Azure CLI를](../linux/proximity-placement-groups.md) 사용하여 근접 배치 그룹을 만들 수도 있습니다.
