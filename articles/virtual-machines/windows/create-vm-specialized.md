---
title: "Azure에서 특수한 디스크로 VM 만들기 | Microsoft Docs"
description: "Resource Manager 배포 모델에서 특수한 관리 디스크 또는 비관리 디스크를 연결하여 새 VM을 만듭니다."
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 3b7d3cd5-e3d7-4041-a2a7-0290447458ea
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 02/06/2017
ms.author: cynthn
translationtype: Human Translation
ms.sourcegitcommit: 197ebd6e37066cb4463d540284ec3f3b074d95e1
ms.openlocfilehash: e9c9f440aa6554e30dc0974d1ab777a9bca50762
ms.lasthandoff: 03/31/2017


---
# <a name="create-a-vm-from-a-specialized-disk"></a>특수한 디스크에서 VM 만들기

Powershell을 사용하여 특수한 디스크를 OS 디스크로 연결하여 새 VM을 만듭니다. 특수한 디스크는 기존 VM의 VHD 복사본으로 사용자 계정, 응용 프로그램 및 원본 VM의 기타 상태 데이터를 유지합니다. 특수한 [관리 디스크](../../storage/storage-managed-disks-overview.md) 또는 특수한 비관리 디스크를 사용하여 새 VM을 만들 수 있습니다.

## <a name="before-you-begin"></a>시작하기 전에
PowerShell을 사용하는 경우 AzureRM.Compute PowerShell 모듈이 최신 버전인지 확인합니다. 다음 명령을 실행하여 PowerShell을 설치합니다.

```powershell
Install-Module AzureRM.Compute -RequiredVersion 2.6.0
```
자세한 내용은 [Azure PowerShell 버전 관리](https://docs.microsoft.com/powershell/azureps-cmdlets-docs/#azure-powershell-versioning)를 참조하세요.


## <a name="create-the-subnet-and-vnet"></a>서브넷 및 VNet 만들기

[가상 네트워크](../../virtual-network/virtual-networks-overview.md)의 VNet 및 서브넷을 만듭니다.

1. 서브넷을 만듭니다. 이 예제에서는 **myResourceGroup** 리소스 그룹에 **mySubNet**으로 명명된 서브넷을 만들고 **10.0.0.0/24**에 대한 서브넷 주소 접두사를 설정합니다.
   
    ```powershell
    $rgName = "myResourceGroup"
    $subnetName = "mySubNet"
    $singleSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name $subnetName -AddressPrefix 10.0.0.0/24
    ```
2. VNet을 만듭니다. 이 예제에서는 가상 네트워크 이름을 **myVnetName**으로, 위치를 **미국 서부**로, 가상 네트워크의 주소 접두사를 **10.0.0.0/16**으로 설정합니다. 
   
    ```powershell
    $location = "West US"
    $vnetName = "myVnetName"
    $vnet = New-AzureRmVirtualNetwork -Name $vnetName -ResourceGroupName $rgName -Location $location `
        -AddressPrefix 10.0.0.0/16 -Subnet $singleSubnet
    ```    

## <a name="create-a-public-ip-address-and-nic"></a>공용 IP 주소 및 NIC 만들기
가상 네트워크에서 가상 컴퓨터와 통신하려면 [공용 IP 주소](../../virtual-network/virtual-network-ip-addresses-overview-arm.md) 및 네트워크 인터페이스가 필요합니다.

1. 공용 IP를 만듭니다. 이 예제에서는 공용 IP 주소 이름을 **myIP**로 설정합니다.
   
    ```powershell
    $ipName = "myIP"
    $pip = New-AzureRmPublicIpAddress -Name $ipName -ResourceGroupName $rgName -Location $location `
        -AllocationMethod Dynamic
    ```       
2. NIC 만들기. 이 예제에서는 NIC 이름을 **myNicName**으로 설정합니다.
   
    ```powershell
    $nicName = "myNicName"
    $nic = New-AzureRmNetworkInterface -Name $nicName -ResourceGroupName $rgName `
    -Location $location -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id
    ```

## <a name="create-the-network-security-group-and-an-rdp-rule"></a>네트워크 보안 그룹 및 RDP 규칙 만들기
RDP를 사용하여 VM에 로그인할 수 있으려면 포트 3389에 대한 RDP 액세스를 허용하는 보안 규칙이 필요합니다. 새 VM의 VHD가 기존의 특수한 VM에서 생성되었기 때문에 VM이 만들어진 후에 RDP를 사용하여 로그온할 사용 권한을 갖고 있던 원본 가상 컴퓨터에서 기존 계정을 사용할 수 있습니다.
이 예제에서는 NSG 이름을 **myNsg**로 설정하고 RDP 규칙 이름을 **myRdpRule**로 설정합니다.

```powershell
$nsgName = "myNsg"

$rdpRule = New-AzureRmNetworkSecurityRuleConfig -Name myRdpRule -Description "Allow RDP" `
    -Access Allow -Protocol Tcp -Direction Inbound -Priority 110 `
    -SourceAddressPrefix Internet -SourcePortRange * `
    -DestinationAddressPrefix * -DestinationPortRange 3389
$nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName $rgName -Location $location `
    -Name $nsgName -SecurityRules $rdpRule
    
```

끝점 및 NSG 규칙에 대한 자세한 내용은 [PowerShell을 사용하여 Azure에서 VM으로 포트 열기](nsg-quickstart-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)를 참조하세요.

## <a name="set-the-vm-name-and-size"></a>VM 이름 및 크기 설정

이 예에서는 VM 이름을 "myVM"으로 설정하고 VM 크기를 "Standard_A2"로 설정합니다.
```powershell
$vmName = "myVM"
$vmConfig = New-AzureRmVMConfig -VMName $vmName -VMSize "Standard_A2"
```

## <a name="add-the-nic"></a>NIC 추가
    
```powershell
$vm = Add-AzureRmVMNetworkInterface -VM $vmConfig -Id $nic.Id
```
    
    
## <a name="configure-the-os-disk"></a>OS 디스크 구성

특수한 OS는 [Azure에 업로드한](upload-image.md) VHD일 수도 있고 [기존 Azure VM의 VHD 복사본](vhd-copy.md)일 수도 있습니다. 

다음 두 가지 옵션 중 하나를 선택하세요.
- **옵션 1**: 기존 저장소 계정의 특수한 VHD로 특수한 Managed Disk를 만들어 OS 디스크로 사용합니다.

또는 

- **옵션 2**: 사용자 고유의 저장소 계정(관리되지 않은 디스크)에 저장된 특수한 VHD를 사용합니다. 

### <a name="option-1-create-a-managed-disk-from-an-unmanaged-specialized-disk"></a>옵션 1: 관리되지 않은 특수한 디스크로 Managed Disk 만들기

1. 사용자의 저장소 계정에 있는 기존의 특수한 VHD로 Managed Disk를 만듭니다. 이 예에서는 디스크 이름으로 **myOSDisk1**을 사용하고, 디스크를 **StandardLRS** 저장소에 배치하고, 원본 VHD의 URI로 **https://storageaccount.blob.core.windows.net/vhdcontainer/osdisk.vh.vhd**를 사용합니다.

    ```powershell
    $osDisk = New-AzureRmDisk -DiskName "myOSDisk1" -Disk (New-AzureRmDiskConfig `
    -AccountType StandardLRS  -Location $location -CreationDataCreateOption Import `
    -SourceUri https://storageaccount.blob.core.windows.net/vhdcontainer/osdisk.vh.vhd) `
    -ResourceGroupName $rgName
    ```

2. 구성에 OS 디스크를 추가합니다. 이 예에서는 디스크 크기를 **128GB**로 설정하고 Managed Disk를 **Windows** OS 디스크로 연결합니다.
    
    ```powershell
    $vm = Set-AzureRmVMOSDisk -VM $vm -ManagedDiskId $osDisk.Id -StorageAccountType StandardLRS `
    -DiskSizeInGB 128 -CreateOption Attach -Windows
    ```

선택 사항: 추가 Managed Disks를 데이터 디스크로 연결합니다. 이 옵션은 사용자가 [관리되는 데이터 디스크 만들기](create-managed-disk-ps.md)에 따라 관리되는 데이터 디스크를 만들었다고 가정합니다. 

```powershell
$vm = Add-AzureRmVMDataDisk -VM $VirtualMachine -Name $dataDiskName -CreateOption Attach -ManagedDiskId $dataDisk1.Id -Lun 1
```


### <a name="option-2-attach-a-vhd-that-is-in-an-existing-storage-account"></a>옵션 2: 기존 저장소 계정에 있는 VHD 연결

1. 사용하려는 VHD의 URI를 설정합니다. 이 예에서는 **myOsDisk.vhd**라는 VHD 파일을 **myContainer**라는 컨테이너의 **myStorageAccount**라는 저장소 계정에 보관합니다.

    ```powershell
    $osDiskUri = "https://myStorageAccount.blob.core.windows.net/myContainer/myOsDisk.vhd"
    ```
2. 복사한 OS VHD의 URL을 사용하여 OS 디스크를 추가합니다. 이 예에서는 OS 디스크가 만들어지면 VM 이름에 "osDisk"가 추가되어 OS 디스크 이름이 완성됩니다. 또한 이 예에서는 이 Windows 기반 VHD를 VM에 OS 디스크로 연결해야 하는 것으로 지정합니다.
    
    ```powershell
    $osDiskName = $vmName + "osDisk"
    $vm = Set-AzureRmVMOSDisk -VM $vm -Name $osDiskName -VhdUri $osDiskUri -CreateOption attach -Windows
    ```

선택 사항: VM에 연결해야 하는 데이터 디스크가 있는 경우 데이터 VHD의 URL과 적절한 LUN(논리 단위 번호)을 사용하여 데이터 디스크를 추가합니다.

```powershell
$dataDiskName = $vmName + "dataDisk"
$vm = Add-AzureRmVMDataDisk -VM $vm -Name $dataDiskName -VhdUri $dataDiskUri -Lun 1 -CreateOption attach
```

저장소 계정을 사용하는 경우 데이터 및 운영 체제 디스크 URL은 `https://StorageAccountName.blob.core.windows.net/BlobContainerName/DiskName.vhd` 형식입니다. 포털에서 대상 저장소 컨테이너로 이동하고 운영 체제 또는 복사된 데이터 VHD를 클릭한 다음 URL의 내용을 복사하여 이 내용을 찾을 수 있습니다.


## <a name="create-the-vm"></a>VM 만들기

방금 만든 구성을 사용하여 VM을 만듭니다.

```powershell
#Create the new VM
New-AzureRmVM -ResourceGroupName $rgName -Location $location -VM $vm
```

이 명령에 성공할 경우 다음과 유사한 출력이 표시됩니다.

```powershell
RequestId IsSuccessStatusCode StatusCode ReasonPhrase
--------- ------------------- ---------- ------------
                         True         OK OK   

```

## <a name="verify-that-the-vm-was-created"></a>VM이 만들어졌는지 확인
새로 만든 VM은 [Azure Portal](https://portal.azure.com)에서 **찾아보기** > **가상 컴퓨터**에 표시되며 다음 PowerShell 명령을 사용해도 표시할 수 있습니다.

```powershell
$vmList = Get-AzureRmVM -ResourceGroupName $rgName
$vmList.Name
```

## <a name="next-steps"></a>다음 단계
새 가상 컴퓨터에 로그인하려면 [포털](https://portal.azure.com)에서 VM으로 이동한 다음 **연결**을 클릭하고 원격 데스크톱 RDP 파일을 엽니다. 원본 가상 컴퓨터의 계정 자격 증명을 사용하여 새 가상 컴퓨터에 로그인합니다. 자세한 내용은 [Windows를 실행하는 Azure 가상 컴퓨터에 연결하고 로그온하는 방법](connect-logon.md)을 참조하세요.


