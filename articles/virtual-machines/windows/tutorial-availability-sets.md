---
title: "Azure에서 Windows VM에 대한 가용성 집합 자습서 | Microsoft Docs"
description: "Azure에서 Windows VM에 대한 가용성 집합에 대해 알아봅니다."
documentationcenter: 
services: virtual-machines-windows
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
ms.date: 04/26/2017
ms.author: cynthn
ms.translationtype: Human Translation
ms.sourcegitcommit: be3ac7755934bca00190db6e21b6527c91a77ec2
ms.openlocfilehash: 831c55939ad3673aa8e44f165e18e826f64b54cc
ms.contentlocale: ko-kr
ms.lasthandoff: 05/03/2017


---

# <a name="how-to-use-availability-sets"></a>가용성 집합 사용 방법

이 자습서에서는 VM(Virtual Machines)을 가용성 집합이라는 논리적 그룹에 배치하여 VM의 가용성을 높이는 방법에 대해 알아봅니다. 가용성 집합 내에서 VM을 만들 경우 Azure 플랫폼은 기본 인프라 내에서 VM을 분산 배치합니다. 플랫폼에서 하드웨어 오류 또는 계획된 유지 관리가 발생하는 경우 가용성 집합을 사용하면 하나 이상의 VM이 계속 실행됩니다.

최신 [Azure PowerShell](https://docs.microsoft.com/powershell/azureps-cmdlets-docs/) 모듈을 사용하여 이 자습서의 단계를 완료할 수 있습니다.

## <a name="availability-set-overview"></a>가용성 집합 개요

기본 Azure 데이터 센터에 있는 하드웨어의 논리적 그룹에서 Virtual Machines를 만들 수 있습니다. 둘 이상의 VM을 만들 경우 계산 및 저장소 리소스가 서버, 네트워크 스위치 및 저장소 등 하드웨어에 분산됩니다. 이러한 분산은 하드웨어 구성 요소가 유지 관리를 진행할 경우 앱의 가용성을 유지해 줍니다. 가용성 집합을 통해 이 논리적 그룹을 정의할 수 있습니다.

가용성 집합은 VM에 고가용성을 제공합니다. 응용 프로그램이 작동 중단 또는 유지 관리 이벤트를 허용하도록 설계되었는지도 확인해야 합니다.

## <a name="create-an-availability-set"></a>가용성 집합 만들기

[New-AzureRmAvailabilitySet](/powershell/module/azurerm.compute/new-azurermavailabilityset)을 사용하여 가용성 집합을 만들 수 있습니다. 이 예제에서는 *myResourceGroupAvailability* 리소스 그룹의 *myAvailabilitySet*라는 가용성 집합에 대해 업데이트 수와 장애 도메인 수를 모두 *2*로 설정합니다.


```powershell
New-AzureRmAvailabilitySet `
   -Location EastUS `
   -Name myAvailabilitySet `
   -ResourceGroupName myResourceGroupAvailability `
   -Managed `
   -PlatformFaultDomainCount 2 `
   -PlatformUpdateDomainCount 2
```

## <a name="create-vms-inside-an-availability-set"></a>가용성 집합에 포함된 VM 만들기

VM은 하드웨어에 제대로 분산되도록 가용성 집합 내에 만들어야 합니다. VM을 만든 후에는 가용성 집합에 기존 VM을 추가할 수 없습니다. 

한 위치의 하드웨어는 여러 개의 업데이트 도메인 및 장애 도메인으로 구분됩니다. **업데이트 도메인**은 동시에 다시 부팅할 수 있는 VM 그룹과 기본 물리적 하드웨어입니다. 동일한 **장애 도메인**의 VM은 공통의 저장소뿐만 아니라 공통의 전원 및 네트워크 스위치를 공유합니다. 

[New-AzureRMVMConfig](/powershell/module/azurerm.compute/new-azurermvmconfig)를 사용하는 구성으로 VM을 만들 때 가용성 집합의 ID를 지정하는 `-AvailabilitySetId` 매개 변수를 사용하여 가용성 집합을 지정합니다.

가용성 집합에 [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm)으로 2개의 VM을 만듭니다.

```powershell
$availabilitySet = Get-AzureRmAvailabilitySet `
    -ResourceGroupName myResourceGroupAvailability `
    -Name myAvailabilitySet

$cred = Get-Credential -Message "Enter a username and password for the virtual machine."

$subnetConfig = New-AzureRmVirtualNetworkSubnetConfig `
    -Name mySubnet `
    -AddressPrefix 192.168.1.0/24
$vnet = New-AzureRmVirtualNetwork `
    -ResourceGroupName myResourceGroupAvailability `
    -Location EastUS `
    -Name MYvNET `
    -AddressPrefix 192.168.0.0/16 `
    -Subnet $subnetConfig

for ($i=1; $i -le 2; $i++)
{
   $pip = New-AzureRmPublicIpAddress `
        -ResourceGroupName myResourceGroupAvailability `
        -Location EastUS `
        -Name "mypublicdns$(Get-Random)" `
        -AllocationMethod Static `
        -IdleTimeoutInMinutes 4

   $nsgRuleRDP = New-AzureRmNetworkSecurityRuleConfig `
        -Name myNetworkSecurityGroupRuleRDP$i `
        -Protocol Tcp `
        -Direction Inbound `
        -Priority 1000 `
        -SourceAddressPrefix * `
        -SourcePortRange * `
        -DestinationAddressPrefix * `
        -DestinationPortRange 3389 `
        -Access Allow

   $nsg = New-AzureRmNetworkSecurityGroup `
        -ResourceGroupName myResourceGroupAvailability `
        -Location EastUS `
        -Name myNetworkSecurityGroup$i `
        -SecurityRules $nsgRuleRDP

   $nic = New-AzureRmNetworkInterface `
        -Name myNic$i `
        -ResourceGroupName myResourceGroupAvailability `
        -Location EastUS `
        -SubnetId $vnet.Subnets[0].Id `
        -PublicIpAddressId $pip.Id `
        -NetworkSecurityGroupId $nsg.Id

   # Here is where we specify the availability set
   $vm = New-AzureRmVMConfig `
        -VMName myVM$i `
        -VMSize Standard_D1 `
        -AvailabilitySetId $availabilitySet.Id

   $vm = Set-AzureRmVMOperatingSystem `
        -VM $vm `
        -Windows -ComputerName myVM$i `   
        -Credential $cred `
        -ProvisionVMAgent `
        -EnableAutoUpdate
   $vm = Set-AzureRmVMSourceImage `
        -VM $vm `
        -PublisherName MicrosoftWindowsServer `
        -Offer WindowsServer `
        -Skus 2016-Datacenter `
        -Version latest
   $vm = Set-AzureRmVMOSDisk `
        -VM $vm `
        -Name myOsDisk$i `
        -DiskSizeInGB 128 `
        -CreateOption FromImage `
        -Caching ReadWrite
   $vm = Add-AzureRmVMNetworkInterface -VM $vm -Id $nic.Id
   New-AzureRmVM `
        -ResourceGroupName myResourceGroupAvailability `
        -Location EastUS `
        -VM $vm
}

```

두 VM을 만들고 구성하는 데 몇 분 정도 소요됩니다. 완료되면 2대의 Virtual Machines가 기본 하드웨어에 분산되었습니다. 

## <a name="check-for-available-vm-sizes"></a>사용 가능한 VM 크기 확인 

나중에 더 많은 VM을 가용성 집합에 추가할 수 있지만 하드웨어에서 사용 가능한 VM 크기를 알아야 합니다. [Get-AzureRMVMSize](/powershell/module/azurerm.compute/get-azurermvmsize)를 사용하여 하드웨어 클러스터에서 사용 가능한 모든 가용성 집합 크기를 나열합니다.

```powershell
Get-AzureRmVMSize `
   -AvailabilitySetName myAvailabilitySet `
   -ResourceGroupName myResourceGroupAvailability  
```

## <a name="next-steps"></a>다음 단계

이 자습서에서는 가상 컴퓨터 확장 집합을 만드는 방법을 배웠습니다. 가상 컴퓨터 확장 집합에 대해 알아보려면 다음 자습서로 이동합니다.

[VM Scale Set 만들기](tutorial-create-vmss.md)



