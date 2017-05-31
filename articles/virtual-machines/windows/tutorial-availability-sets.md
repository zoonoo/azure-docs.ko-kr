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
ms.date: 05/08/2017
ms.author: cynthn
ms.translationtype: Human Translation
ms.sourcegitcommit: 18d4994f303a11e9ce2d07bc1124aaedf570fc82
ms.openlocfilehash: 022396a8bf0478414be179b9f7341a459ed2bc60
ms.contentlocale: ko-kr
ms.lasthandoff: 05/09/2017


---

# <a name="how-to-use-availability-sets"></a>가용성 집합 사용 방법

이 자습서에서는 가용성 집합이라는 기능을 사용하여 Azure에서 VM(가상 컴퓨터) 솔루션의 가용성과 안정성을 향상시키는 방법에 대해 알아봅니다. 가용성 집합을 사용하면 Azure에 배포한 VM이 격리된 여러 하드웨어 클러스터에 분산되도록 할 수 있습니다. 이렇게 하면 Azure 내의 하드웨어 또는 소프트웨어 오류가 발생할 때 VM의 하위 집합에만 영향을 주며 전체 솔루션을 사용하는 고객의 관점에서 전체 솔루션을 사용할 수 있게 됩니다. 

이 자습서에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * 가용성 집합 만들기
> * 가용성 집합에서 VM 만들기
> * 사용 가능한 VM 크기 확인

이 자습서에는 Azure PowerShell 모듈 버전 3.6 이상이 필요합니다. ` Get-Module -ListAvailable AzureRM`을 실행하여 버전을 찾습니다. 업그레이드해야 하는 경우 [Azure PowerShell 모듈 설치](/powershell/azure/install-azurerm-ps)를 참조하세요.

## <a name="availability-set-overview"></a>가용성 집합 개요

가용성 집합은 해당 집합에 배치한 VM 리소스가 Azure 데이터 센터에 배포될 때 서로 간에 격리되도록 하기 위해 Azure에서 사용할 수 있는 논리적 그룹화 기능입니다. Azure는 가용성 집합 내에 배치한 VM을 여러 물리적 서버, 계산 랙, 저장 단위 및 네트워크 스위치에서 실행되도록 합니다. 이렇게 하면 하드웨어 또는 Azure 소프트웨어 오류가 발생할 경우 VM의 하위 집합에만 영향을 주는 한편 전체 응용 프로그램은 계속 유지되어 고객이 계속 사용할 수 있습니다. 가용성 집합을 사용하는 것은 안정적인 클라우드 솔루션을 구축하려고 할 때 활용할 수 있는 필수적인 기능입니다.

4개의 프런트 엔드 웹 서버가 있고 데이터베이스를 호스팅하는 2개의 백 엔드 VM을 사용하는 일반적인 VM 기반 솔루션을 고려해 보겠습니다. Azure를 사용하면 VM을 배포하기 전에 하나는 “웹” 계층, 다른 하나는 “데이터베이스” 계층에 대한 집합인 두 개의 가용성 집합을 정의해야 합니다. 새 VM을 만들 때 az vm create 명령에 대한 매개 변수로 가용성 집합을 지정할 수 있으며, Azure에서는 사용 가능한 집합 내에 만든 VM이 여러 물리적 하드웨어 리소스에서 자동으로 격리되도록 합니다. 즉, 웹 서버 VM 또는 데이터베이스 서버 VM 중 하나가 실행 중인 물리적 하드웨어에 문제가 있는 경우 웹 서버 VM과 데이터베이스 VM의 다른 인스턴스가 다른 하드웨어에 있기 때문에 계속 실행된다는 것을 알 수 있습니다.

Azure 내에서 신뢰할 수 있는 VM 기반 솔루션을 배포하려면 항상 가용성 집합을 사용해야 합니다.

## <a name="create-an-availability-set"></a>가용성 집합 만들기

[New-AzureRmAvailabilitySet](/powershell/module/azurerm.compute/new-azurermavailabilityset)을 사용하여 가용성 집합을 만들 수 있습니다. 이 예제에서는 *myResourceGroupAvailability* 리소스 그룹의 *myAvailabilitySet*라는 가용성 집합에 대해 업데이트 수와 장애 도메인 수를 모두 *2*로 설정합니다.

리소스 그룹을 만듭니다.

```powershell
New-AzureRmResourceGroup -Name myResourceGroupAvailability -Location EastUS
```


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

이 자습서에서는 다음 방법에 대해 알아보았습니다.

> [!div class="checklist"]
> * 가용성 집합 만들기
> * 가용성 집합에서 VM 만들기
> * 사용 가능한 VM 크기 확인

가상 컴퓨터 확장 집합에 대해 알아보려면 다음 자습서로 이동합니다.

> [!div class="nextstepaction"]
> [VM Scale Set 만들기](tutorial-create-vmss.md)



