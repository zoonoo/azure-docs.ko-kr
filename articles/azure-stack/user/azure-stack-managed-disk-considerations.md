---
title: 차이점 및 Azure Stack의 Managed Disks에 대 한 고려 사항 | Microsoft Docs
description: Azure Stack의 Managed Disks를 사용 하 여 작업 하는 경우 차이점 및 고려 사항에 알아봅니다.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/31/2019
ms.author: sethm
ms.reviewer: jiahan
ms.lastreviewed: 01/05/2019
ms.openlocfilehash: ade8ce1cc872c4d954d272f79ef9a3113ef0f683
ms.sourcegitcommit: 1516779f1baffaedcd24c674ccddd3e95de844de
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/26/2019
ms.locfileid: "56820079"
---
# <a name="azure-stack-managed-disks-differences-and-considerations"></a>Azure Stack의 Managed Disks: 차이점 및 고려 사항

이 문서에는 알려진된 차이점이 요약 되어 있습니다. [Azure Stack의 Managed Disks](azure-stack-manage-vm-disks.md) 하 고 [azure Managed Disks](../../virtual-machines/windows/managed-disks-overview.md)합니다. Azure Stack 및 Azure 간의 대략적인 차이 대 한 자세한 내용은 참조는 [고려 사항 키](azure-stack-considerations.md) 문서.

Managed Disks를 관리 하 여 IaaS Vm 용 디스크 관리를 간소화 합니다 [저장소 계정](../azure-stack-manage-storage-accounts.md) 는 VM 디스크와 연결 합니다.

> [!Note]  
> Azure Stack에 managed Disks는 1808 업데이트에서 사용할 수 있습니다. 1811 업데이트에서 Azure Stack 포털을 사용 하 여 가상 컴퓨터를 만들 때 기본적으로 활성화 됩니다.
  
## <a name="cheat-sheet-managed-disk-differences"></a>참고 자료: 관리 디스크 차이점

| 기능 | Azure (전역) | Azure Stack |
| --- | --- | --- |
|미사용 데이터 암호화 |Azure Disk Encryption (ADE) azure Storage 서비스 암호화 (SSE)     |BitLocker 128 비트 AES 암호화      |
|이미지          | 관리 되는 사용자 지정 이미지를 지원 합니다. |지원됨|
|백업 옵션 |Azure Backup 서비스를 지원 합니다. |아직 지원 되지 않음 |
|재해 복구 옵션 |Azure Site Recovery 지원 |아직 지원 되지 않음|
|디스크 유형     |Premium SSD, 표준 SSD (미리 보기) 및 표준 HDD |Premium SSD, HDD 표준 |
|프리미엄 디스크  |완전하게 지원 |성능 제한이 있지만 프로비저닝할 수 있습니다 또는 보장  |
|프리미엄 디스크 IOPs  |디스크 크기에 따라 달라 집니다.  |디스크당 IOPs는 2300까지 |
|프리미엄 디스크 처리량 |디스크 크기에 따라 달라 집니다. |디스크당 초당 145 MB |
|디스크 크기  |Azure Premium Disk: P4 (32 GiB) P80를 (32 TiB)<br>Azure 표준 SSD 디스크: E 10 (128 GiB) E80를 (32 TiB)<br>Azure 표준 HDD 디스크: S4 (32 GiB) S80를 (32 TiB) |M4: 32GiB<br>M6: 64GiB<br>M10: 128GiB<br>M15: 256GiB<br>M20: 512GiB<br>M30: 1024GiB |
|디스크 스냅숏 복사|Azure 스냅숏 지원 되는 실행 중인 VM에 연결 된 디스크를 관리 합니다.|아직 지원 되지 않음 |
|디스크 성능 분석 |메트릭을 집계 하 고 지원 되는 디스크 메트릭 당 |아직 지원 되지 않음 |
|마이그레이션      |VM을 다시 만들 필요 없이 기존 관리 되지 않는 Azure Resource Manager Vm에서 마이그레이션하는 도구를 제공 합니다.  |아직 지원 되지 않음 |

> [!NOTE]  
> 관리 되는 디스크 IOPs 및 처리량이 Azure Stack 하드웨어 및 Azure Stack에서 실행 중인 워크 로드에 영향을 받이 있습니다 프로 비전 된 숫자가 대신 cap 숫자입니다.

## <a name="metrics"></a>메트릭

저장소 메트릭 사용 하 여 차이점도 있습니다.

- Azure Stack을 사용 하 여 storage 메트릭에서는 트랜잭션 데이터 내부 또는 외부 네트워크 대역폭을 지지 않습니다.
- Storage 메트릭에서는 azure Stack 트랜잭션 데이터에는 탑재 된 디스크에 대 한 가상 머신 액세스를 포함 되지 않습니다.

## <a name="api-versions"></a>API 버전

Azure Stack Managed Disks는 다음과 같은 API 버전을 지원합니다.

- 2017-03-30
- 2017-12-01

## <a name="convert-to-managed-disks"></a>Managed disks로 변환

사용할 수는 아래에서 관리 되지 않는 현재 프로 비전 된 VM을 managed disks로 변환 하는 고유한 값을 사용 하 여 스크립트

```powershell
$subscriptionId = 'subid'

#Provide the name of your resource group
$resourceGroupName ='rgmgd'

#Provide the name of the Managed Disk
$diskName = 'unmgdvm'

#Provide the size of the disks in GB. It should be greater than the VHD file size.
$diskSize = '50'

#Provide the URI of the VHD file that will be used to create Managed Disk. 
# VHD file can be deleted as soon as Managed Disk is created.
$vhdUri = 'https://rgmgddisks347.blob.local.azurestack.external/vhds/unmgdvm20181109013817.vhd' 

#Provide the storage type for the Managed Disk. PremiumLRS or StandardLRS.
$accountType = 'StandardLRS'

#Provide the Azure Stack location where Managed Disk will be located. 
#The location should be same as the location of the storage account where VHD file is stored.

$location = 'local'
$virtualMachineName = 'mgdvm'
$virtualMachineSize = 'Standard_D1'
$pipname = 'unmgdvm-ip'
$virtualNetworkName = 'rgmgd-vnet'
$nicname = 'unmgdvm295'

#Set the context to the subscription Id where Managed Disk will be created
Select-AzureRmSubscription -SubscriptionId $SubscriptionId

$diskConfig = New-AzureRmDiskConfig -AccountType $accountType  -Location $location -DiskSizeGB $diskSize -SourceUri $vhdUri -CreateOption Import

#Create Managed disk
New-AzureRmDisk -DiskName $diskName -Disk $diskConfig -ResourceGroupName $resourceGroupName
$disk = get-azurermdisk -DiskName $diskName -ResourceGroupName $resourceGroupName
$VirtualMachine = New-AzureRmVMConfig -VMName $virtualMachineName -VMSize $virtualMachineSize

#Use the Managed Disk Resource Id to attach it to the virtual machine. Please change the OS type to linux if OS disk has linux OS
$VirtualMachine = Set-AzureRmVMOSDisk -VM $VirtualMachine -ManagedDiskId $disk.Id -CreateOption Attach -Linux

#Create a public IP for the VM
$publicIp = Get-AzureRmPublicIpAddress -Name $pipname -ResourceGroupName $resourceGroupName 

#Get the virtual network where virtual machine will be hosted
$vnet = Get-AzureRmVirtualNetwork -Name $virtualNetworkName -ResourceGroupName $resourceGroupName

# Create NIC in the first subnet of the virtual network
$nic = Get-AzureRmNetworkInterface -Name $nicname -ResourceGroupName $resourceGroupName

$VirtualMachine = Add-AzureRmVMNetworkInterface -VM $VirtualMachine -Id $nic.Id

#Create the virtual machine with Managed Disk
New-AzureRmVM -VM $VirtualMachine -ResourceGroupName $resourceGroupName -Location $location
```

## <a name="managed-images"></a>관리 되는 이미지

Azure Stack 지원 *이미지를 관리 되는*, 앞으로 Vm 디스크는 사용의 일반화 된 VM (둘 다 비관리 및 관리)는 관리 되는 만들 수에 관리 되는 이미지 개체를 만들 수 있습니다. 다음 두 가지 시나리오를 사용 하도록 설정 하는 관리 되는 이미지:

- 앞으로 managed disks를 사용 하 고 관리 되지 않는 Vm을 일반화가 있습니다.
- 관리 되는 vm을 일반화 하 고 여러 개 만들 하려고, 유사한 Vm을 관리 합니다.

### <a name="migrate-unmanaged-vms-to-managed-disks"></a>관리 되지 않는 Vm을 managed disks로 마이그레이션

지침을 따릅니다 [여기](../../virtual-machines/windows/capture-image-resource.md#create-an-image-from-a-vhd-in-a-storage-account) 저장소 계정의 일반화 된 VHD에서 관리 되는 이미지를 만들려고 합니다. 이 이미지 앞으로 관리 되는 Vm을 만드는 데 사용할 수 있습니다.

### <a name="create-managed-image-from-vm"></a>VM에서 관리 되는 이미지 만들기

관리 되는 기존 이미지를 만든 후 스크립트를 사용 하 여 VM 디스크 [여기](../../virtual-machines/windows/capture-image-resource.md#create-an-image-from-a-managed-disk-using-powershell) , 다음 예제 스크립트는 기존 이미지 개체에서 유사한 Linux VM을 만듭니다.

```powershell
# Variables for common values
$resourceGroup = "myResourceGroup"
$location = "redmond"
$vmName = "myVM"
$imagerg = "managedlinuxrg"
$imagename = "simplelinuxvmm-image-2019122"

# Create user object
$cred = Get-Credential -Message "Enter a username and password for the virtual machine."

# Create a resource group
New-AzureRmResourceGroup -Name $resourceGroup -Location $location

# Create a subnet configuration
$subnetConfig = New-AzureRmVirtualNetworkSubnetConfig -Name mySubnet -AddressPrefix 192.168.1.0/24

# Create a virtual network
$vnet = New-AzureRmVirtualNetwork -ResourceGroupName $resourceGroup -Location $location `
  -Name MYvNET -AddressPrefix 192.168.0.0/16 -Subnet $subnetConfig

# Create a public IP address and specify a DNS name
$pip = New-AzureRmPublicIpAddress -ResourceGroupName $resourceGroup -Location $location `
  -Name "mypublicdns$(Get-Random)" -AllocationMethod Static -IdleTimeoutInMinutes 4

# Create an inbound network security group rule for port 3389
$nsgRuleRDP = New-AzureRmNetworkSecurityRuleConfig -Name myNetworkSecurityGroupRuleRDP  -Protocol Tcp `
  -Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
  -DestinationPortRange 3389 -Access Allow

# Create a network security group
$nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName $resourceGroup -Location $location `
  -Name myNetworkSecurityGroup -SecurityRules $nsgRuleRDP

# Create a virtual network card and associate with public IP address and NSG
$nic = New-AzureRmNetworkInterface -Name myNic -ResourceGroupName $resourceGroup -Location $location `
  -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id -NetworkSecurityGroupId $nsg.Id

$image = get-azurermimage -ResourceGroupName $imagerg -ImageName $imagename
# Create a virtual machine configuration
$vmConfig = New-AzureRmVMConfig -VMName $vmName -VMSize Standard_D1 | `
Set-AzureRmVMOperatingSystem -Linux -ComputerName $vmName -Credential $cred | `
Set-AzureRmVMSourceImage -Id $image.Id | `
Add-AzureRmVMNetworkInterface -Id $nic.Id

# Create a virtual machine
New-AzureRmVM -ResourceGroupName $resourceGroup -Location $location -VM $vmConfig
```

자세한 내용은 참조는 Azure 이미지 문서 관리 [Azure에서 일반화 된 VM의 관리 이미지 만들기](../../virtual-machines/windows/capture-image-resource.md) 하 고 [관리 되는 이미지에서 VM 만들기](../../virtual-machines/windows/create-vm-generalized-managed.md)합니다.

## <a name="configuration"></a>구성

1808를 적용 한 후 업데이트 또는 나중에 Managed Disks를 사용 하기 전에 다음 구성을 수행 해야 합니다.

- 구독을 만든 경우 1808 업데이트 하기 전에 구독을 업데이트 하려면 다음 단계에 따릅니다. 그렇지 않은 경우 "디스크 관리자에 내부 오류가 있습니다." 오류 메시지와 함께 실패할 수 있습니다이 구독에서 Vm을 배포 합니다.
   1. 테 넌 트 포털에서로 이동 **구독** 구독을 찾습니다. 클릭 **리소스 공급자**, 클릭 **Microsoft.Compute**를 클릭 하 고 **re-register**합니다.
   2. 로 동일한 구독에 따라 **액세스 제어 (IAM)**, 되어 있는지 확인 하 고 **Azure Stack – Managed Disk** 나열 됩니다.
- 다중 테 넌 트 환경에서 사용 하는 경우 요청에 클라우드 운영자 (서비스 공급자 또는 사용자 조직에서 일 수 있음)를 각 단계에 따라 게스트 디렉터리를 다시 구성 [이 문서에서는](../azure-stack-enable-multitenancy.md#registering-azure-stack-with-the-guest-directory)합니다. 그렇지 않은 경우 "디스크 관리자에 내부 오류가 있습니다." 오류 메시지와 함께 실패할 수 있습니다 게스트 디렉터리와 연결 된 구독에서 Vm을 배포

## <a name="next-steps"></a>다음 단계

- [Azure Stack virtual machines에 알아보기](azure-stack-compute-overview.md)
