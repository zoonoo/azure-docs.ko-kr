---
title: "PowerShell을 사용하여 Azure VM 만들기 | Microsoft Docs"
description: "Azure PowerShell 및 Azure Resource Manager를 사용하여 Windows Server VM을 손쉽게 만들 수 있습니다."
services: virtual-machines-windows
documentationcenter: 
author: davidmu1
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 14fe9ca9-e228-4d3b-a5d8-3101e9478f6e
ms.service: virtual-machines-windows
ms.topic: get-started-article
ms.date: 03/07/2017
ms.author: davidmu
translationtype: Human Translation
ms.sourcegitcommit: 72b2d9142479f9ba0380c5bd2dd82734e370dee7
ms.openlocfilehash: 3de1e04c3ce1d6d465c5a54bc9db676639709371
ms.lasthandoff: 03/08/2017

---

# <a name="create-a-windows-vm-using-azure-resource-manager-and-powershell"></a>Azure Resource Manager 및 PowerShell을 사용하여 Windows VM 만들기

이 문서에서는 [Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) 및 Azure PowerShell을 사용하여 Windows Server를 실행하는 Azure Virtual Machine 및 필요한 리소스를 빠르게 만드는 방법을 보여 줍니다.  

이 문서의 모든 단계는 가상 컴퓨터를 만드는 데 필요하며 명령을 복사하고 붙여넣고 실행하려면 약 10분 정도가 걸립니다.

## <a name="step-1-install-azure-powershell"></a>1단계: Azure PowerShell 설치

최신 버전의 Azure PowerShell 설치, 구독 선택, 자신의 계정에 로그인하는 방법에 대해서는 [Azure PowerShell 설치 및 구성 방법](/powershell/azureps-cmdlets-docs)을 참조하세요.

> [!NOTE]
> 이 문서에서 기능을 사용하려면 Azure PowerShell을 다시 설치해야 합니다. Managed Disks 기능은 버전 3.5 이상에 있습니다.
> 
> 

## <a name="step-2-create-a-resource-group"></a>2단계: 리소스 그룹 만들기

모든 리소스가 리소스 그룹에 포함될 수 있도록 먼저 리소스 그룹을 만들어야 합니다.  

1. 리소스를 만들 수 있는 사용 가능한 위치 목록을 가져옵니다.
   
    ```powershell
    Get-AzureRmLocation | sort Location | Select Location
    ```

2. 리소스의 위치를 설정합니다. 이 명령은 위치를 **centralus**로 설정합니다.
   
    ```powershell
    $location = "centralus"
    ```

3. 리소스 그룹을 만듭니다. 이 명령은 설정한 위치에 **myResourceGroup**이라는 리소스 그룹을 만듭니다.
   
    ```powershell
    $myResourceGroup = "myResourceGroup"
    New-AzureRmResourceGroup -Name $myResourceGroup -Location $location
    ```

## <a name="step-3-optional-create-a-storage-account"></a>3단계: (선택 사항) 저장소 계정 만들기

가상 컴퓨터를 만드는 경우 현재 [Azure Managed Disks](../storage/storage-managed-disks-overview.md) 또는 관리되지 않는 디스크를 사용하도록 선택할 수 있습니다. 관리되지 않는 디스크를 사용하도록 선택하는 경우 만드는 가상 컴퓨터에서 사용되는 가상 하드 디스크를 저장하기 위해 [저장소 계정](../storage/storage-introduction.md)을 만들어야 합니다. 관리되는 디스크를 사용하도록 선택하는 경우 저장소 계정이 필요하지 않습니다. 저장소 계정 이름은 3자에서 24자 사이여야 하고 숫자 및 소문자만 포함할 수 있습니다.

1. 저장소 계정 이름의 고유성을 테스트합니다. 이 명령은 **myStorageAccount** 이름을 테스트합니다.
   
    ```powershell
    $myStorageAccountName = "mystorageaccount"
    Get-AzureRmStorageAccountNameAvailability $myStorageAccountName
    ```
   
    이 명령에서 **True**가 반환되면 제안한 이름은 Azure 내에서 고유합니다. 

2. 이제 저장소 계정을 만듭니다.
   
    ```powershell    
    $myStorageAccount = New-AzureRmStorageAccount -ResourceGroupName $myResourceGroup `
        -Name $myStorageAccountName -SkuName "Standard_LRS" -Kind "Storage" -Location $location
    ```

## <a name="step-4-create-a-virtual-network"></a>4단계: 가상 네트워크 만들기

모든 가상 컴퓨터는 [가상 네트워크](virtual-machines-windows-network-overview.md)의 일부입니다.

1. 가상 네트워크의 서브넷을 만듭니다. 이 명령은 주소 접두사로 10.0.0.0/24를 사용하는 **mySubnet**이라는 서브넷을 만듭니다.
   
    ```powershell
    $mySubnet = New-AzureRmVirtualNetworkSubnetConfig -Name "mySubnet" -AddressPrefix 10.0.0.0/24
    ```

2. 이제 가상 네트워크를 만듭니다. 이 명령은 만든 서브넷과 **10.0.0.0/16** 주소 접두사를 사용하여 **myVnet**이라는 가상 네트워크를 만듭니다.
   
    ```powershell
    $myVnet = New-AzureRmVirtualNetwork -Name "myVnet" -ResourceGroupName $myResourceGroup `
        -Location $location -AddressPrefix 10.0.0.0/16 -Subnet $mySubnet
    ```

## <a name="step-5-create-a-public-ip-address-and-network-interface"></a>5단계: 공용 IP 주소 및 네트워크 인터페이스 만들기

가상 네트워크에서 가상 컴퓨터와 통신하려면 [공용 IP 주소](../virtual-network/virtual-network-ip-addresses-overview-arm.md) 및 네트워크 인터페이스가 필요합니다.

1. 공용 IP 주소를 만듭니다. 이 명령은 **Dynamic** 할당 메서드를 통해 **myPublicIp**라는 공용 IP 주소를 만듭니다.
   
    ```powershell
    $myPublicIp = New-AzureRmPublicIpAddress -Name "myPublicIp" -ResourceGroupName $myResourceGroup `
        -Location $location -AllocationMethod Dynamic
    ```
2. 네트워크 인터페이스를 만듭니다. 이 명령은 **myNIC**라는 네트워크 인터페이스를 만듭니다.
   
    ```powershell
    $myNIC = New-AzureRmNetworkInterface -Name "myNIC" -ResourceGroupName $myResourceGroup `
        -Location $location -SubnetId $myVnet.Subnets[0].Id -PublicIpAddressId $myPublicIp.Id
    ```

## <a name="step-6-create-a-virtual-machine"></a>6단계: 가상 컴퓨터 만들기

이제 모든 항목이 결합되었으므로 가상 컴퓨터를 만들 시간입니다. 가상 컴퓨터는 [Marketplace 이미지](virtual-machines-windows-cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json), [사용자 지정 범용 (Sysprep을 실행한) 이미지](virtual-machines-windows-create-vm-generalized.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) 또는 [사용자 지정 특수화된(Sysprep을 실행하지 않은) 이미지](virtual-machines-windows-create-vm-specialized.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)를 사용하여 만들 수 있습니다. 이 예제에서는 Marketplace의 Windows Server 이미지를 사용합니다. 

1. 다음 명령을 실행하여 가상 컴퓨터의 관리자 계정 이름과 암호를 설정합니다.

    ```powershell
    $cred = Get-Credential -Message "Type the name and password of the local administrator account."
    ```
   
    암호의 길이는 12-123자여야 하며 1개의 소문자, 1개의 대문자, 1개의 숫자 및 1개의 특수 문자가 있어야 합니다.

2. 가상 컴퓨터의 구성 개체를 만듭니다. 이 명령은 VM의 이름과 크기를 정의하는 **myVmConfig**라는 구성 개체를 만듭니다.
   
    ```powershell
    $myVM = New-AzureRmVMConfig -VMName "myVM" -VMSize "Standard_DS1_v2"
    ```
   
    가상 컴퓨터에 사용 가능한 크기 목록은 [Azure에서 가상 컴퓨터에 대한 크기](virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) 를 참조하세요.

3. VM의 운영 체제 설정을 구성합니다. 이 명령은 VM의 컴퓨터 이름, 운영 체제 유형 및 계정 자격 증명을 설정합니다.
   
    ```powershell
    $myVM = Set-AzureRmVMOperatingSystem -VM $myVM -Windows -ComputerName "myVM" -Credential $cred `
        -ProvisionVMAgent -EnableAutoUpdate
    ```

4. VM을 프로비전하는 데 사용할 이미지를 정의합니다. 이 명령은 VM에 대해 사용할 Windows Server 이미지를 정의합니다. 

    ```powershell
    $myVM = Set-AzureRmVMSourceImage -VM $myVM -PublisherName "MicrosoftWindowsServer" `
        -Offer "WindowsServer" -Skus "2012-R2-Datacenter" -Version "latest"
    ```

5. 만든 네트워크 인터페이스를 구성에 추가합니다.
   
    ```powershell
    $myVM = Add-AzureRmVMNetworkInterface -VM $myVM -Id $myNIC.Id
    ```

6. 관리되지 않는 디스크를 사용하는 경우 이 명령을 실행하여 VM 하드 디스크의 이름 및 위치를 정의하거나 이 단계를 건너뜁니다. 관리되지 않는 디스크의 가상 하드 디스크 파일은 컨테이너에 저장됩니다. 이 명령은 만든 저장소 계정에 있는 **vhds/myOsDisk1.vhd**라는 컨테이너에 디스크를 만듭니다.
   
    ```powershell
    $blobPath = "vhds/myOsDisk1.vhd"
    $osDiskUri = $myStorageAccount.PrimaryEndpoints.Blob.ToString() + $blobPath
    ```

7. VM 구성에 운영 체제 디스크 정보를 추가합니다. 이 명령은 **myOsDisk1**이라는 디스크를 만듭니다.
   
    관리되는 디스크를 사용하는 경우 구성에서 이 명령을 실행하여 운영 체제 디스크를 설정합니다.

    ```powershell
    $myVM = Set-AzureRmVMOSDisk -VM $myVM -Name "myOsDisk1" -StorageAccountType PremiumLRS -DiskSizeInGB 128 -CreateOption FromImage -Caching ReadWrite
    ```

    관리되지 않는 디스크를 사용하는 경우 구성에서 이 명령을 실행하여 운영 체제 디스크를 설정합니다.

    ```powershell
    $myVM = Set-AzureRmVMOSDisk -VM $myVM -Name "myOsDisk1" -VhdUri $osDiskUri -CreateOption fromImage
    ```

8. 마지막으로 가상 컴퓨터를 만듭니다.
   
    ```powershell
    New-AzureRmVM -ResourceGroupName $myResourceGroup -Location $location -VM $myVM
    ```

## <a name="next-steps"></a>다음 단계

* 배포에 문제가 있는 경우 [Azure Resource Manager를 사용한 일반적인 Azure 배포 오류 해결](../azure-resource-manager/resource-manager-common-deployment-errors.md)을 살펴봅니다.
* [Azure Resource Manager 및 PowerShell을 사용하여 가상 컴퓨터 관리](virtual-machines-windows-ps-manage.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)를 검토하여 자신이 만든 가상 컴퓨터를 관리하는 방법을 알아봅니다.
* [Resource Manager 템플릿을 사용하여 Windows 가상 컴퓨터 만들기](virtual-machines-windows-ps-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)


