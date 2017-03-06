---
title: "PowerShell을 사용하여 Azure 가상 컴퓨터 크기 집합 만들기 | Microsoft Docs"
description: "PowerShell을 사용하여 Azure 가상 컴퓨터 크기 집합 만들기"
services: virtual-machine-scale-sets
documentationcenter: 
author: Thraka
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 7bb03323-8bcc-4ee4-9a3e-144ca6d644e2
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 02/21/2017
ms.author: adegeo
translationtype: Human Translation
ms.sourcegitcommit: 1f8e66fac5b82698525794f0486dd0432c7421a7
ms.openlocfilehash: 7286fed39839675eb960b749f3235f83e36c5e9a
ms.lasthandoff: 02/22/2017


---
# <a name="create-a-windows-virtual-machine-scale-set-using-azure-powershell"></a>Azure PowerShell을 사용하여 Windows 가상 컴퓨터 크기 집합 만들기
다음 단계에서는 빈칸을 채우는 방식을 따라 Azure 가상 컴퓨터 크기 집합을 만듭니다. 크기 집합에 대한 자세한 내용은 [가상 컴퓨터 크기 집합 개요](virtual-machine-scale-sets-overview.md) 를 참조하십시오.

이 문서의 단계를 수행하려면 약 30분이 걸립니다.

## <a name="step-1-install-azure-powershell"></a>1단계: Azure PowerShell 설치
최신 버전의 Azure PowerShell 설치, 구독 선택, 자신의 계정에 로그인하는 방법에 대해서는 [Azure PowerShell 설치 및 구성 방법](/powershell/azureps-cmdlets-docs)을 참조하세요.

## <a name="step-2-create-resources"></a>2단계: 리소스 만들기
새 크기 집합에 필요한 리소스를 만듭니다.

### <a name="resource-group"></a>리소스 그룹
가상 컴퓨터 크기 집합은 리소스 그룹에 포함되어야 합니다.

1. 리소스를 배치할 수 있는 사용 가능한 위치 목록을 가져옵니다.
   
        Get-AzureLocation | Sort Name | Select Name
2. 가장 적합한 위치를 선택하고 **$locName** 값을 해당 위치 이름으로 바꾼 다음 변수를 만듭니다.
   
        $locName = "location name from the list, such as Central US"
3. **$rgName** 값을 새 리소스 그룹에 사용하려는 이름으로 바꾼 다음 변수를 만듭니다. 
   
        $rgName = "resource group name"
4. 리소스 그룹을 만듭니다.
   
        New-AzureRmResourceGroup -Name $rgName -Location $locName
   
    다음 예제와 유사한 결과가 표시됩니다.
   
        ResourceGroupName : myrg1
        Location          : centralus
        ProvisioningState : Succeeded
        Tags              :
        ResourceId        : /subscriptions/########-####-####-####-############/resourceGroups/myrg1

### <a name="virtual-network"></a>가상 네트워크
크기 집합의 가상 컴퓨터에 가상 네트워크가 필요합니다.

1. **$subnetName** 값을 가상 네트워크의 서브넷에 사용하려는 이름으로 바꾼 다음 변수를 만듭니다. 
   
        $subnetName = "subnet name"
2. 서브넷 구성을 만듭니다.
   
        $subnet = New-AzureRmVirtualNetworkSubnetConfig -Name $subnetName -AddressPrefix 10.0.0.0/24
   
    주소 접두사는 가상 네트워크와 다를 수 있습니다.
3. **$netName** 값을 가상 네트워크에 사용하려는 이름으로 바꾼 다음 변수를 만듭니다. 
   
        $netName = "virtual network name"
4. 가상 네트워크를 만듭니다.
   
        $vnet = New-AzureRmVirtualNetwork -Name $netName -ResourceGroupName $rgName -Location $locName -AddressPrefix 10.0.0.0/16 -Subnet $subnet

### <a name="configuration-of-the-scale-set"></a>크기 집합 구성
크기 집합 구성에 필요한 모든 리소스가 있으므로 크기 집합을 만들겠습니다.  

1. **$ipName** 값을 IP 구성에 사용하려는 이름으로 바꾼 다음 변수를 만듭니다. 
   
        $ipName = "IP configuration name"
2. IP 구성을 만듭니다.
   
        $ipConfig = New-AzureRmVmssIpConfig -Name $ipName -LoadBalancerBackendAddressPoolsId $null -SubnetId $vnet.Subnets[0].Id
3. **$vmssConfig** 값을 크기 집합 구성에 사용하려는 이름으로 바꾼 다음 변수를 만듭니다.   
   
        $vmssConfig = "Scale set configuration name"
4. 크기 집합에 대한 구성을 만듭니다.
   
        $vmss = New-AzureRmVmssConfig -Location $locName -SkuCapacity 3 -SkuName "Standard_A0" -UpgradePolicyMode "manual"
   
    이 예제는&3;개의 가상 컴퓨터로 크기 집합을 만드는 방법을 보여줍니다. 크기 집합의 용량에 대한 자세한 내용은 [가상 컴퓨터 크기 집합 개요](virtual-machine-scale-sets-overview.md) 를 참조하십시오. 이 단계에서는 집합에 있는 가상 컴퓨터의 크기(SkuName이라고 함)도 설정합니다. 요구 사항에 맞는 크기를 찾으려면 [가상 컴퓨터의 크기](../virtual-machines/virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)를 참조하세요.
5. 크기 집합 구성에 네트워크 인터페이스 구성을 추가합니다.
   
        Add-AzureRmVmssNetworkInterfaceConfiguration -VirtualMachineScaleSet $vmss -Name $vmssConfig -Primary $true -IPConfiguration $ipConfig
   
    다음 예제와 유사한 결과가 표시됩니다.
   
        Sku                   : Microsoft.Azure.Management.Compute.Models.Sku
        UpgradePolicy         : Microsoft.Azure.Management.Compute.Models.UpgradePolicy
        VirtualMachineProfile : Microsoft.Azure.Management.Compute.Models.VirtualMachineScaleSetVMProfile
        ProvisioningState     :
        OverProvision         :
        Id                    :
        Name                  :
        Type                  :
        Location              : Central US
        Tags                  :

#### <a name="operating-system-profile"></a>운영 체제 프로필
1. **$computerName** 값을 사용하려는 컴퓨터 이름 접두사로 바꾼 다음 변수를 만듭니다. 
   
        $computerName = "computer name prefix"
2. **$adminName** 값을 가상 컴퓨터의 관리자 계정 이름으로 바꾼 다음 변수를 만듭니다.
   
        $adminName = "administrator account name"
3. **$adminPassword** 값을 계정 암호로 바꾼 다음 변수를 만듭니다.
   
        $adminPassword = "password for administrator accounts"
4. 운영 체제 프로필을 만듭니다.
   
        Set-AzureRmVmssOsProfile -VirtualMachineScaleSet $vmss -ComputerNamePrefix $computerName -AdminUsername $adminName -AdminPassword $adminPassword

#### <a name="storage-profile"></a>저장소 프로필
1. **$storageProfile** 값을 저장소 프로필에 사용하려는 이름으로 바꾼 다음 변수를 만듭니다.  
   
        $storageProfile = "storage profile name"
2. 사용할 이미지를 정의하는 변수를 만듭니다.  
   
        $imagePublisher = "MicrosoftWindowsServer"
        $imageOffer = "WindowsServer"
        $imageSku = "2012-R2-Datacenter"
   
    사용할 이미지에 대한 자세한 내용은 [Windows PowerShell 및 Azure CLI를 사용하여 Azure 가상 컴퓨터 이미지 탐색 및 선택](../virtual-machines/virtual-machines-windows-cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)을 참조하세요.

3. 저장소 프로필을 만듭니다.
   
        Set-AzureRmVmssStorageProfile -VirtualMachineScaleSet $vmss -ImageReferencePublisher $imagePublisher -ImageReferenceOffer $imageOffer -ImageReferenceSku $imageSku -ImageReferenceVersion "latest" -OsDiskCreateOption "FromImage" -OsDiskCaching "None"  

### <a name="virtual-machine-scale-set"></a>가상 컴퓨터 크기 집합
마지막으로 크기 집합을 만들 수 있습니다.

1. **$vmssName** 값을 가상 컴퓨터 크기 집합의 이름으로 바꾼 다음 변수를 만듭니다.
   
        $vmssName = "scale set name"
2. 크기 집합을 만듭니다.
   
        New-AzureRmVmss -ResourceGroupName $rgName -Name $vmssName -VirtualMachineScaleSet $vmss
   
    배포가 성공했음을 나타내는 이 예제와 비슷한 내용이 표시됩니다.
   
        Sku                   : Microsoft.Azure.Management.Compute.Models.Sku
        UpgradePolicy         : Microsoft.Azure.Management.Compute.Models.UpgradePolicy
        VirtualMachineProfile : Microsoft.Azure.Management.Compute.Models.VirtualMachineScaleSetVMProfile
        ProvisioningState     : Updating
        OverProvision         :
        Id                    : /subscriptions/########-####-####-####-############/resourceGroups/myrg1/providers/Microso
                                ft.Compute/virtualMachineScaleSets/myvmss1
        Name                  : myvmss1
        Type                  : Microsoft.Compute/virtualMachineScaleSets
        Location              : centralus
        Tags                  :

## <a name="step-3-explore-resources"></a>3단계: 리소스 탐색
다음과 같은 리소스를 사용하여 만든 가상 컴퓨터 크기 집합을 탐색합니다.

* Azure 포털 - 포털을 사용하여 제한된 양의 정보를 얻을 수 있습니다.
* [Azure 리소스 탐색기](https://resources.azure.com/) - 크기 집합의 현재 상태를 탐색하는 데 가장 적합한 도구입니다.
* Azure PowerShell - 이 명령을 사용하여 정보를 가져옵니다.
  
        Get-AzureRmVmss -ResourceGroupName "resource group name" -VMScaleSetName "scale set name"
  
        Or 
  
        Get-AzureRmVmssVM -ResourceGroupName "resource group name" -VMScaleSetName "scale set name"

## <a name="next-steps"></a>다음 단계
* 방금 [가상 컴퓨터 크기 집합의 가상 컴퓨터 관리](virtual-machine-scale-sets-windows-manage.md)
* [자동 크기 조정 및 가상 컴퓨터 크기 집합](virtual-machine-scale-sets-autoscale-overview.md)
* [가상 컴퓨터 크기 집합을 사용하여 수직 자동 크기 조정](virtual-machine-scale-sets-vertical-scale-reprovision.md)


