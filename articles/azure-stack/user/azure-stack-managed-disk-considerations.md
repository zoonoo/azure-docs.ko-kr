---
title: 차이점 및 고려 사항에 대 한 관리 디스크 및 Azure Stack에서 이미지 관리 | Microsoft Docs
description: Managed disks 및 Azure Stack에서 관리 되는 이미지를 사용 하 여 작업 하는 경우 차이점 및 고려 사항에 알아봅니다.
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
ms.date: 03/23/2019
ms.author: sethm
ms.reviewer: jiahan
ms.lastreviewed: 03/23/2019
ms.openlocfilehash: 87c3be01b5a77aa43a23fa64e5359e8ac4a20a36
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/08/2019
ms.locfileid: "59271240"
---
# <a name="azure-stack-managed-disks-differences-and-considerations"></a>Azure Stack 관리 디스크: 차이점 및 고려 사항

이 문서에는 알려진된 차이점이 요약 되어 있습니다. [관리 디스크를 Azure Stack](azure-stack-manage-vm-disks.md) 하 고 [Azure의 managed disks](../../virtual-machines/windows/managed-disks-overview.md)합니다. Azure Stack 및 Azure 간의 대략적인 차이 대 한 자세한 내용은 참조는 [고려 사항 키](azure-stack-considerations.md) 문서.

관리 디스크를 관리 하 여 IaaS Vm 용 디스크 관리를 간소화 합니다 [저장소 계정](../azure-stack-manage-storage-accounts.md) VM 디스크와 연결 된입니다.

> [!Note]  
> Azure Stack에 managed disks는 1808 업데이트에서 사용할 수 있습니다. 1811 업데이트부터 Azure Stack 포털을 사용 하 여 가상 컴퓨터를 만들 때 기본적으로 활성화 됩니다.
  
## <a name="cheat-sheet-managed-disk-differences"></a>치트 시트: 관리 디스크 차이점

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
> 관리 디스크 IOPs 및 처리량이 Azure Stack 하드웨어 및 Azure Stack에서 실행 중인 워크 로드에 영향을 받을 수 있습니다 하는 프로 비전 된 숫자를 대신 한도입니다.

## <a name="metrics"></a>메트릭

저장소 메트릭 사용 하 여 차이점도 있습니다.

- Azure Stack을 사용 하 여 storage 메트릭에서는 트랜잭션 데이터 내부 또는 외부 네트워크 대역폭을 지지 않습니다.
- Storage 메트릭에서는 azure Stack 트랜잭션 데이터에는 탑재 된 디스크에 대 한 가상 머신 액세스를 포함 되지 않습니다.

## <a name="api-versions"></a>API 버전

Azure Stack 관리 디스크에서는 다음 API 버전:

- 2017-03-30
- 2017-12-01

## <a name="convert-to-managed-disks"></a>Managed disks로 변환

현재 프로 비전 된 VM을 관리 되지 않는에서 managed disks로 변환 하려면 다음 스크립트를 사용할 수 있습니다. 사용자 고유의 값을 사용 하 여 자리 표시자를 바꿉니다.

```powershell
$subscriptionId = 'subid'

# The name of your resource group where your VM to be converted exists
$resourceGroupName ='rgmgd'

# The name of the managed disk
$diskName = 'unmgdvm'

# The size of the disks in GB. It should be greater than the VHD file size.
$diskSize = '50'

# The URI of the VHD file that will be used to create the managed disk.
# The VHD file can be deleted as soon as the managed disk is created.
$vhdUri = 'https://rgmgddisks347.blob.local.azurestack.external/vhds/unmgdvm20181109013817.vhd' 

# The storage type for the managed disk; PremiumLRS or StandardLRS.
$accountType = 'StandardLRS'

# The Azure Stack location where the managed disk will be located.
# The location should be the same as the location of the storage account in which VHD file is stored.
# Configure the new managed VM point to the old unmanaged VM's configuration (network config, vm name, location).
$location = 'local'
$virtualMachineName = 'mgdvm'
$virtualMachineSize = 'Standard_D1'
$pipname = 'unmgdvm-ip'
$virtualNetworkName = 'rgmgd-vnet'
$nicname = 'unmgdvm295'

# Set the context to the subscription ID in which the managed disk will be created
Select-AzureRmSubscription -SubscriptionId $SubscriptionId

#Delete old VM, but keep the OS disk
Remove-AzureRmVm -Name $virtualMachineName -ResourceGroupName $resourceGroupName

$diskConfig = New-AzureRmDiskConfig -AccountType $accountType  -Location $location -DiskSizeGB $diskSize -SourceUri $vhdUri -CreateOption Import

# Create managed disk
New-AzureRmDisk -DiskName $diskName -Disk $diskConfig -ResourceGroupName $resourceGroupName
$disk = get-azurermdisk -DiskName $diskName -ResourceGroupName $resourceGroupName
$VirtualMachine = New-AzureRmVMConfig -VMName $virtualMachineName -VMSize $virtualMachineSize

# Use the managed disk resource ID to attach it to the virtual machine.
# Change the OS type to Linux if the OS disk has Linux OS.
$VirtualMachine = Set-AzureRmVMOSDisk -VM $VirtualMachine -ManagedDiskId $disk.Id -CreateOption Attach -Linux

# Create a public IP for the VM
$publicIp = Get-AzureRmPublicIpAddress -Name $pipname -ResourceGroupName $resourceGroupName 

# Get the virtual network where the virtual machine will be hosted
$vnet = Get-AzureRmVirtualNetwork -Name $virtualNetworkName -ResourceGroupName $resourceGroupName

# Create NIC in the first subnet of the virtual network
$nic = Get-AzureRmNetworkInterface -Name $nicname -ResourceGroupName $resourceGroupName

$VirtualMachine = Add-AzureRmVMNetworkInterface -VM $VirtualMachine -Id $nic.Id

# Create the virtual machine with managed disk
New-AzureRmVM -VM $VirtualMachine -ResourceGroupName $resourceGroupName -Location $location
```

## <a name="managed-images"></a>관리 되는 이미지

Azure Stack 지원 *이미지를 관리 되는*, 앞으로 Vm 디스크는 사용의 일반화 된 VM (둘 다 비관리 및 관리)는 관리 되는 만들 수에 관리 되는 이미지 개체를 만들 수 있습니다. 다음 두 가지 시나리오를 사용 하도록 설정 하는 관리 되는 이미지:

- 앞으로 managed disks를 사용 하 고 관리 되지 않는 Vm을 일반화가 있습니다.
- 관리 되는 vm을 일반화 하 고 여러 개 만들 하려고, 유사한 Vm을 관리 합니다.

### <a name="step-1-generalize-the-vm"></a>1단계: VM 일반화

Windows를 수행 합니다 [Sysprep를 사용 하 여 Windows VM을 일반화](/azure/virtual-machines/windows/capture-image-resource#generalize-the-windows-vm-using-sysprep) 섹션입니다. Linux의 경우 1 단계를 따릅니다 [여기](/azure/virtual-machines/linux/capture-image#step-1-deprovision-the-vm)합니다.

> [!NOTE]
> VM을 일반화 해야 합니다. 이어질 수 하지 제대로 일반화 된 이미지에서 VM 만들기를 **VMProvisioningTimeout** 오류입니다.

### <a name="step-2-create-the-managed-image"></a>2단계: 관리 되는 이미지 만들기

포털, PowerShell 또는 CLI 관리 되는 이미지를 만드는 데 사용할 수 있습니다. Azure 문서의 단계를 따릅니다 [여기](/azure/virtual-machines/windows/capture-image-resource)합니다.

### <a name="step-3-choose-the-use-case"></a>3단계: 사용 사례를 선택 합니다.

#### <a name="case-1-migrate-unmanaged-vms-to-managed-disks"></a>사례 1: 관리 되지 않는 Vm을 managed disks로 마이그레이션

이 단계를 수행 하기 전에 VM을 올바르게 일반화 해야 합니다. 일반화 한 후이 VM을 더 이상 사용할 수 없습니다. 이어질 수 하지 제대로 일반화 된 이미지에서 VM 만들기를 **VMProvisioningTimeout** 오류입니다.

지침을 따릅니다 [여기](../../virtual-machines/windows/capture-image-resource.md#create-an-image-from-a-vhd-in-a-storage-account) 저장소 계정의 일반화 된 VHD에서 관리 되는 이미지를 만들려고 합니다. 관리 되는 Vm을 만드는 앞이 이미지를 사용할 수 있습니다.

#### <a name="case-2-create-managed-vm-from-managed-image-using-powershell"></a>사례 2: Powershell을 사용 하 여 관리 되는 이미지에서 관리 되는 VM 만들기

관리 되는 기존 이미지를 만든 후 스크립트를 사용 하 여 VM 디스크 [여기](../../virtual-machines/windows/capture-image-resource.md#create-an-image-from-a-managed-disk-using-powershell), 다음 예제 스크립트는 기존 이미지 개체에서 유사한 Linux VM을 만듭니다.

Azure Stack PowerShell 모듈 1.7.0 이상: 지침을 따릅니다 [여기](../../virtual-machines/windows/create-vm-generalized-managed.md)합니다.

Azure Stack PowerShell 모듈 1.6.0 또는 이전 버전:

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

또한 관리 되는 이미지에서 VM을 만들려면 포털을 사용할 수 있습니다. 자세한 내용은 참조는 Azure 이미지 문서 관리 [Azure에서 일반화 된 VM의 관리 이미지 만들기](../../virtual-machines/windows/capture-image-resource.md) 하 고 [관리 되는 이미지에서 VM 만들기](../../virtual-machines/windows/create-vm-generalized-managed.md)합니다.

## <a name="configuration"></a>구성

1808를 적용 한 후 업데이트 또는 나중에 managed disks를 사용 하기 전에 다음 구성을 수행 해야 합니다.

- 구독을 만든 경우 1808 업데이트 하기 전에 구독을 업데이트 하려면 다음 단계에 따릅니다. 그렇지 않은 경우 "디스크 관리자에 내부 오류가 있습니다." 오류 메시지와 함께 실패할 수 있습니다이 구독에서 Vm을 배포 합니다.
   1. 테 넌 트 포털에서로 이동 **구독** 구독을 찾습니다. 클릭 **리소스 공급자**, 클릭 **Microsoft.Compute**를 클릭 하 고 **re-register**합니다.
   2. 로 동일한 구독에 따라 **액세스 제어 (IAM)**, 되어 있는지 확인 하 고 **Azure Stack – Managed Disk** 나열 됩니다.
- 다중 테 넌 트 환경에서 사용 하는 경우 요청에 클라우드 운영자 (서비스 공급자 또는 사용자 조직에서 일 수 있음)를 각 단계에 따라 게스트 디렉터리를 다시 구성 [이 문서에서는](../azure-stack-enable-multitenancy.md#registering-azure-stack-with-the-guest-directory)합니다. 그렇지 않은 경우 "디스크 관리자에 내부 오류가 있습니다." 오류 메시지와 함께 실패할 수 있습니다 게스트 디렉터리와 연결 된 구독에서 Vm을 배포

## <a name="next-steps"></a>다음 단계

- [Azure Stack virtual machines에 알아보기](azure-stack-compute-overview.md)
