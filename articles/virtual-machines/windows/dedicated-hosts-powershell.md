---
title: Azure PowerShell을 사용하여 Azure 전용 호스트 배포
description: Azure PowerShell을 사용하여 전용 호스트에 VM을 배포합니다.
author: cynthn
ms.service: virtual-machines-windows
ms.topic: article
ms.workload: infrastructure
ms.date: 08/01/2019
ms.author: cynthn
ms.reviewer: zivr
ms.openlocfilehash: b90189c6ba5e51a24d0c248b5aa08e9a5e4bbd9b
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2020
ms.locfileid: "82082852"
---
# <a name="deploy-vms-to-dedicated-hosts-using-the-azure-powershell"></a>Azure PowerShell을 사용하여 전용 호스트에 VM 배포

이 문서에서는 가상 시스템(VM)을 호스트하는 Azure [전용 호스트를](dedicated-hosts.md) 만드는 방법을 설명합니다. 

Azure PowerShell 버전 2.8.0 이상에 설치하였고 `Connect-AzAccount`에서 Azure 계정에 로그인했는지 확인합니다. 

## <a name="limitations"></a>제한 사항

- 가상 시스템 크기 집합은 현재 전용 호스트에서 지원되지 않습니다.
- 전용 호스트에 사용할 수 있는 크기와 하드웨어 유형은 지역에 따라 다릅니다. 자세한 내용은 호스트 [가격 페이지를](https://aka.ms/ADHPricing) 참조하십시오.

## <a name="create-a-host-group"></a>호스트 그룹 만들기

**호스트 그룹은** 전용 호스트의 컬렉션을 나타내는 리소스입니다. 리전 및 가용성 영역에서 호스트 그룹을 만들고 호스트를 추가합니다. 고가용성을 계획할 때 추가 옵션이 있습니다. 전용 호스트에서 다음 옵션 중 하나 또는 둘 다사용할 수 있습니다. 
- 여러 가용성 영역에 걸쳐 있습니다. 이 경우 사용하려는 각 영역에 호스트 그룹이 있어야 합니다.
- 물리적 랙에 매핑되는 여러 오류 도메인에 걸쳐 스팬합니다. 
 
두 경우 모두 호스트 그룹에 대한 장애 도메인 수를 제공해야 합니다. 그룹의 장애 도메인을 포괄하지 않으려면 오류 도메인 수를 1로 사용합니다. 

가용성 영역과 장애 도메인을 모두 사용하도록 결정할 수도 있습니다. 이 예제에서는 2개의 오류 도메인이 있는 영역 1에 호스트 그룹을 만듭니다. 


```azurepowershell-interactive
$rgName = "myDHResourceGroup"
$location = "EastUS"

New-AzResourceGroup -Location $location -Name $rgName
$hostGroup = New-AzHostGroup `
   -Location $location `
   -Name myHostGroup `
   -PlatformFaultDomain 2 `
   -ResourceGroupName $rgName `
   -Zone 1
```

## <a name="create-a-host"></a>호스트 만들기

이제 호스트 그룹에 전용 호스트를 만들어 보겠습니다. 호스트의 이름 외에도 호스트에 대한 SKU를 제공해야 합니다. Host SKU는 지원되는 VM 시리즈와 전용 호스트의 하드웨어 생성을 캡처합니다.

호스트 SCO 및 가격 책정에 대한 자세한 내용은 [Azure 전용 호스트 가격 책정을](https://aka.ms/ADHPricing)참조하십시오.

호스트 그룹에 대한 오류 도메인 수를 설정하면 호스트에 대한 오류 도메인을 지정하라는 메시지가 표시됩니다. 이 예제에서는 호스트의 오류 도메인을 1로 설정합니다.


```azurepowershell-interactive
$dHost = New-AzHost `
   -HostGroupName $hostGroup.Name `
   -Location $location -Name myHost `
   -ResourceGroupName $rgName `
   -Sku DSv3-Type1 `
   -AutoReplaceOnFailure 1 `
   -PlatformFaultDomain 1
```

## <a name="create-a-vm"></a>VM 만들기

전용 호스트에 가상 컴퓨터를 만듭니다. 

호스트 그룹을 만들 때 가용성 영역을 지정한 경우 가상 컴퓨터를 만들 때 동일한 영역을 사용해야 합니다. 이 예제에서는 호스트 그룹이 영역 1에 있으므로 영역 1에서 VM을 만들어야 합니다.  


```azurepowershell-interactive
$cred = Get-Credential
New-AzVM `
   -Credential $cred `
   -ResourceGroupName $rgName `
   -Location $location `
   -Name myVM `
   -HostId $dhost.Id `
   -Image Win2016Datacenter `
   -Zone 1 `
   -Size Standard_D4s_v3
```

> [!WARNING]
> 리소스가 충분하지 않은 호스트에 가상 컴퓨터를 만들면 가상 시스템이 FAILED 상태로 만들어집니다. 

## <a name="check-the-status-of-the-host"></a>호스트의 상태 확인

매개 변수를 사용하여 호스트 상태와 호스트에 배포할 수 있는 가상 컴퓨터 수를 확인할 수 있습니다. [GetAzHost](/powershell/module/az.compute/get-azhost) `-InstanceView`

```azurepowershell-interactive
Get-AzHost `
   -ResourceGroupName $rgName `
   -Name myHost `
   -HostGroupName $hostGroup.Name `
   -InstanceView
```

출력은 다음과 유사합니다.

```
ResourceGroupName      : myDHResourceGroup
PlatformFaultDomain    : 1
AutoReplaceOnFailure   : True
HostId                 : 12345678-1234-1234-abcd-abc123456789
ProvisioningTime       : 7/28/2019 5:31:01 PM
ProvisioningState      : Succeeded
InstanceView           : 
  AssetId              : abc45678-abcd-1234-abcd-123456789abc
  AvailableCapacity    : 
    AllocatableVMs[0]  : 
      VmSize           : Standard_D2s_v3
      Count            : 32
    AllocatableVMs[1]  : 
      VmSize           : Standard_D4s_v3
      Count            : 16
    AllocatableVMs[2]  : 
      VmSize           : Standard_D8s_v3
      Count            : 8
    AllocatableVMs[3]  : 
      VmSize           : Standard_D16s_v3
      Count            : 4
    AllocatableVMs[4]  : 
      VmSize           : Standard_D32-8s_v3
      Count            : 2
    AllocatableVMs[5]  : 
      VmSize           : Standard_D32-16s_v3
      Count            : 2
    AllocatableVMs[6]  : 
      VmSize           : Standard_D32s_v3
      Count            : 2
    AllocatableVMs[7]  : 
      VmSize           : Standard_D64-16s_v3
      Count            : 1
    AllocatableVMs[8]  : 
      VmSize           : Standard_D64-32s_v3
      Count            : 1
    AllocatableVMs[9]  : 
      VmSize           : Standard_D64s_v3
      Count            : 1
  Statuses[0]          : 
    Code               : ProvisioningState/succeeded
    Level              : Info
    DisplayStatus      : Provisioning succeeded
    Time               : 7/28/2019 5:31:01 PM
  Statuses[1]          : 
    Code               : HealthState/available
    Level              : Info
    DisplayStatus      : Host available
Sku                    : 
  Name                 : DSv3-Type1
Id                     : /subscriptions/10101010-1010-1010-1010-101010101010/re
sourceGroups/myDHResourceGroup/providers/Microsoft.Compute/hostGroups/myHostGroup/hosts
/myHost
Name                   : myHost
Location               : eastus
Tags                   : {}
```

## <a name="add-an-existing-vm"></a>기존 VM 추가 

전용 호스트에 기존 VM을 추가할 수 있지만 VM은 먼저 Stop\Deallocated이어야 합니다. VM을 전용 호스트로 이동하기 전에 VM 구성이 지원되는지 확인합니다.

- VM 크기는 전용 호스트와 동일한 크기 패밀리여야 합니다. 예를 들어 전용 호스트가 DSv3인 경우 VM 크기는 Standard_D4s_v3 수 있지만 Standard_A4_v2 수 없습니다. 
- VM은 전용 호스트와 동일한 지역에 있어야 합니다.
- VM은 근접 배치 그룹에 속할 수 없습니다. 전용 호스트로 이동하기 전에 근접 배치 그룹에서 VM을 제거합니다. 자세한 내용은 [근접 배치 그룹에서 VM 이동을](https://docs.microsoft.com/azure/virtual-machines/windows/proximity-placement-groups#move-an-existing-vm-out-of-a-proximity-placement-group) 참조하세요.
- VM은 가용성 집합에 있을 수 없습니다.
- VM이 가용성 영역에 있는 경우 호스트 그룹과 동일한 가용성 영역이어야 합니다. VM 및 호스트 그룹에 대한 가용성 영역 설정이 일치해야 합니다.

변수의 값을 사용자 고유의 정보로 바꿉습니다.

```azurepowershell-interactive
$vmRGName = "movetohost"
$vmName = "myVMtoHost"
$dhRGName = "myDHResourceGroup"
$dhGroupName = "myHostGroup"
$dhName = "myHost"

$myDH = Get-AzHost `
   -HostGroupName $dhGroupName `
   -ResourceGroupName $dhRGName `
   -Name $dhName
   
$myVM = Get-AzVM `
   -ResourceGroupName $vmRGName `
   -Name $vmName
   
$myVM.Host = New-Object Microsoft.Azure.Management.Compute.Models.SubResource

$myVM.Host.Id = "$myDH.Id"

Stop-AzVM `
   -ResourceGroupName $vmRGName `
   -Name $vmName -Force
   
Update-AzVM `
   -ResourceGroupName $vmRGName `
   -VM $myVM -Debug
   
Start-AzVM `
   -ResourceGroupName $vmRGName `
   -Name $vmName
```


## <a name="clean-up"></a>정리

가상 컴퓨터를 배포하지 않은 경우에도 전용 호스트에 대한 요금이 부과됩니다. 비용을 절감하기 위해 현재 사용하지 않는 호스트를 삭제해야 합니다.  

호스트를 사용하는 가상 시스템이 더 이상 없는 경우에만 호스트를 삭제할 수 있습니다. [제거 AzVM을](/powershell/module/az.compute/remove-azvm)사용하여 VM을 삭제합니다.

```azurepowershell-interactive
Remove-AzVM -ResourceGroupName $rgName -Name myVM
```

VM을 삭제한 후 [제거-AzHost](/powershell/module/az.compute/remove-azhost)를 사용하여 호스트를 삭제할 수 있습니다.

```azurepowershell-interactive
Remove-AzHost -ResourceGroupName $rgName -Name myHost
```

모든 호스트를 삭제한 후에는 [제거-AzHostGroup](/powershell/module/az.compute/remove-azhostgroup)을 사용하여 호스트 그룹을 삭제할 수 있습니다. 

```azurepowershell-interactive
Remove-AzHost -ResourceGroupName $rgName -Name myHost
```

[제거-AzResourceGroup을](/powershell/module/az.resources/remove-azresourcegroup)사용 하 여 단일 명령에서 전체 리소스 그룹을 삭제할 수도 있습니다. 이렇게 하면 모든 VM, 호스트 및 호스트 그룹을 포함하여 그룹에서 만든 모든 리소스가 삭제됩니다.
 
```azurepowershell-interactive
Remove-AzResourceGroup -Name $rgName
```


## <a name="next-steps"></a>다음 단계

- 영역과 오류 도메인을 모두 사용하여 영역의 복원력을 극대화하는 샘플 템플릿이 [있습니다.](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vm-dedicated-hosts/README.md)

- [Azure 포털을](dedicated-hosts-portal.md)사용하여 전용 호스트를 배포할 수도 있습니다.
