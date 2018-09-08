---
title: Azure Stack에서 PowerShell을 사용 하 여 Linux 가상 머신 만들기 | Microsoft Docs
description: Azure Stack에서 PowerShell을 사용 하 여 Linux 가상 머신을 만듭니다.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: 03EE5929-4F05-47D7-B246-EA93D6FC47CD
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 09/07/2018
ms.author: mabrigg
ms.custom: mvc
ms.openlocfilehash: bb356a8b485817daae803d14b30832e7b1322f29
ms.sourcegitcommit: 2d961702f23e63ee63eddf52086e0c8573aec8dd
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/07/2018
ms.locfileid: "44158911"
---
# <a name="quickstart-create-a-linux-server-virtual-machine-by-using-powershell-in-azure-stack"></a>빠른 시작: Azure Stack에서 PowerShell을 사용 하 여 Linux 서버 가상 머신 만들기

*적용 대상: Azure Stack 통합 시스템 및 Azure Stack 개발 키트*

Azure Stack PowerShell을 사용 하 여 Ubuntu Server 16.04 LTS 가상 컴퓨터를 만들 수 있습니다. 만들고 가상 머신을 사용 하려면이 문서의 단계를 수행 합니다.  이 문서에서는 단계를 제공 합니다.

* 원격 클라이언트를 사용 하 여 가상 컴퓨터에 연결 합니다.
* NGINX 웹 서버를 설치 하 고 기본 홈페이지를 봅니다.
* 사용 되지 않는 리소스를 정리 합니다.

## <a name="prerequisites"></a>필수 조건

* **Azure Stack marketplace에서 Linux 이미지**

   Azure Stack marketplace에는 기본적으로 Linux 이미지를 포함 하지 않습니다. 가져올 수 있도록 Azure Stack 운영자를 **Ubuntu Server 16.04 LTS** 해야 하는 이미지입니다. 연산자에 설명 된 단계를 사용할 수는 [Azure에서 Azure Stack marketplace 항목 다운로드](../azure-stack-download-azure-marketplace-item.md) 문서.

* Azure Stack 리소스를 만들고 관리 하려면 Azure PowerShell의 특정 버전에 필요 합니다. Azure Stack에 대해 구성 된 PowerShell가 없는 경우 수행 하는 단계 [설치](azure-stack-powershell-install.md) PowerShell.

* 설정 된 Azure Stack PowerShell을 사용 하 여 Azure Stack 환경에 연결 해야 합니다. 명령 참조 [사용자로 PowerShell 사용 하 여 Azure Stack에 연결](azure-stack-powershell-configure-user.md)합니다.

* Windows 사용자 프로필의.ssh 디렉터리에 저장 하는 이름 id_rsa.pub 인 공용 SSH 키입니다. SSH 키를 만드는 방법에 대 한 자세한 내용은 참조 하세요. [Windows에서 만드는 SSH 키](../../virtual-machines/linux/ssh-from-windows.md)합니다.

## <a name="create-a-resource-group"></a>리소스 그룹 만들기

리소스 그룹은 배포 하 고 Azure Stack 리소스를 관리할 수 있는 논리적 컨테이너입니다. Azure Stack 통합 시스템, 개발 키트에서 리소스 그룹을 만들려면 다음 코드 블록을 실행 합니다. 이 문서에서 모든 변수에 대해 할당 된 값, 이러한 값을 사용 하거나 새 값을 할당할 수 있습니다.

```powershell
# Create variables to store the location and resource group names.
$location = "local"
$ResourceGroupName = "myResourceGroup"

New-AzureRmResourceGroup `
  -Name $ResourceGroupName `
  -Location $location
```

## <a name="create-storage-resources"></a>저장소 리소스 만들기

저장소 계정을 만들고 Ubuntu Server 16.04 LTS 이미지에 대 한 저장소 컨테이너를 만듭니다.

```powershell
# Create variables to store the storage account name and the storage account SKU information
$StorageAccountName = "mystorageaccount"
$SkuName = "Standard_LRS"

# Create a new storage account
$StorageAccount = New-AzureRMStorageAccount `
  -Location $location `
  -ResourceGroupName $ResourceGroupName `
  -Type $SkuName `
  -Name $StorageAccountName

Set-AzureRmCurrentStorageAccount `
  -StorageAccountName $storageAccountName `
  -ResourceGroupName $resourceGroupName

# Create a storage container to store the virtual machine image
$containerName = 'osdisks'
$container = New-AzureStorageContainer `
  -Name $containerName `
  -Permission Blob
```

## <a name="create-networking-resources"></a>네트워킹 리소스 만들기

가상 네트워크, 서브넷 및 공용 IP 주소를 만듭니다. 이러한 리소스는 가상 머신에 네트워크 연결을 제공 하는 데 사용 됩니다.

```powershell
# Create a subnet configuration
$subnetConfig = New-AzureRmVirtualNetworkSubnetConfig `
  -Name mySubnet `
  -AddressPrefix 192.168.1.0/24

# Create a virtual network
$vnet = New-AzureRmVirtualNetwork `
  -ResourceGroupName $ResourceGroupName `
  -Location $location `
  -Name MyVnet `
  -AddressPrefix 192.168.0.0/16 `
  -Subnet $subnetConfig

# Create a public IP address and specify a DNS name
$pip = New-AzureRmPublicIpAddress `
  -ResourceGroupName $ResourceGroupName `
  -Location $location `
  -AllocationMethod Static `
  -IdleTimeoutInMinutes 4 `
  -Name "mypublicdns$(Get-Random)"

```

### <a name="create-a-network-security-group-and-a-network-security-group-rule"></a>네트워크 보안 그룹 및 네트워크 보안 그룹 규칙 만들기

네트워크 보안 그룹 인바운드 및 아웃 바운드 규칙을 사용 하 여 가상 컴퓨터를 보호 합니다. 들어오는 원격 데스크톱 연결을 허용 하도록 3389 포트에 대 한 인바운드 규칙을 만들고 들어오는 웹 트래픽을 허용 하도록 80 포트에 대 한 인바운드 규칙을 만듭니다.

```powershell
# Create variables to store the network security group and rules names.
$nsgName = "myNetworkSecurityGroup"
$nsgRuleSSHName = "myNetworkSecurityGroupRuleSSH"
$nsgRuleWebName = "myNetworkSecurityGroupRuleWeb"


# Create an inbound network security group rule for port 22
$nsgRuleSSH = New-AzureRmNetworkSecurityRuleConfig -Name $nsgRuleSSHName -Protocol Tcp `
-Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
-DestinationPortRange 22 -Access Allow

# Create an inbound network security group rule for port 80
$nsgRuleWeb = New-AzureRmNetworkSecurityRuleConfig -Name $nsgRuleWebName -Protocol Tcp `
-Direction Inbound -Priority 1001 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
-DestinationPortRange 80 -Access Allow

# Create a network security group
$nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName $ResourceGroupName -Location $location `
-Name $nsgName -SecurityRules $nsgRuleSSH,$nsgRuleWeb
```

### <a name="create-a-network-card-for-the-virtual-machine"></a>가상 머신에 대한 네트워크 카드 만들기

네트워크 카드는 서브넷, 네트워크 보안 그룹 및 공용 IP 주소에 가상 머신을 연결합니다.

```powershell
# Create a virtual network card and associate it with public IP address and NSG
$nic = New-AzureRmNetworkInterface `
  -Name myNic `
  -ResourceGroupName $ResourceGroupName `
  -Location $location `
  -SubnetId $vnet.Subnets[0].Id `
  -PublicIpAddressId $pip.Id `
  -NetworkSecurityGroupId $nsg.Id
```

## <a name="create-a-virtual-machine"></a>가상 머신 만들기

가상 머신 구성을 만듭니다. 이 구성은 가상 컴퓨터를 배포할 때 사용 하는 설정을 포함 합니다. 예를 들어: 사용자 자격 증명, 크기 및 가상 머신 이미지입니다.

```powershell
# Define a credential object.
$UserName='demouser'
$securePassword = ConvertTo-SecureString ' ' -AsPlainText -Force
$cred = New-Object System.Management.Automation.PSCredential ($UserName, $securePassword)

# Create the virtual machine configuration object
$VmName = "VirtualMachinelatest"
$VmSize = "Standard_D1"
$VirtualMachine = New-AzureRmVMConfig `
  -VMName $VmName `
  -VMSize $VmSize

$VirtualMachine = Set-AzureRmVMOperatingSystem `
  -VM $VirtualMachine `
  -Linux `
  -ComputerName "MainComputer" `
  -Credential $cred

$VirtualMachine = Set-AzureRmVMSourceImage `
  -VM $VirtualMachine `
  -PublisherName "Canonical" `
  -Offer "UbuntuServer" `
  -Skus "16.04-LTS" `
  -Version "latest"

$osDiskName = "OsDisk"
$osDiskUri = '{0}vhds/{1}-{2}.vhd' -f `
  $StorageAccount.PrimaryEndpoints.Blob.ToString(),`
  $vmName.ToLower(), `
  $osDiskName

# Sets the operating system disk properties on a virtual machine.
$VirtualMachine = Set-AzureRmVMOSDisk `
  -VM $VirtualMachine `
  -Name $osDiskName `
  -VhdUri $OsDiskUri `
  -CreateOption FromImage | `
  Add-AzureRmVMNetworkInterface -Id $nic.Id

# Configure SSH Keys
$sshPublicKey = Get-Content "$env:USERPROFILE\.ssh\id_rsa.pub"

# Adds the SSH Key to the virtual machine
Add-AzureRmVMSshPublicKey -VM $VirtualMachine `
 -KeyData $sshPublicKey `
 -Path "/home/azureuser/.ssh/authorized_keys"

# Create the virtual machine.
New-AzureRmVM `
  -ResourceGroupName $ResourceGroupName `
 -Location $location `
  -VM $VirtualMachine
```

## <a name="quick-create-virtual-machine---full-script"></a>빠른 가상 컴퓨터 만들기-전체 스크립트

> [!NOTE]
> 이 병합 위의 코드 보다 이지만 SSH 대신 암호 인증에 대 한 키.

```powershell
## Create a resource group

<#
A resource group is a logical container where you can deploy and manage Azure Stack resources. From your development kit or the Azure Stack integrated system, run the following code block to create a resource group. Values are assigned for all the variables in this document, you can use these values or assign new values.
#>

# Edit your variables here if required

# Create variables to store the location and resource group names.
$location = "local"
$ResourceGroupName = "myResourceGroup"

# Create variables to store the storage account name and the storage account SKU information
$StorageAccountName = "mystorageaccount"
$SkuName = "Standard_LRS"

# Create variables to store the network security group and rules names.
$nsgName = "myNetworkSecurityGroup"
$nsgRuleSSHName = "myNetworkSecurityGroupRuleSSH"
$nsgRuleWebName = "myNetworkSecurityGroupRuleWeb"

# Create variable for virtual machine password
$VMPassword = 'Password123!'

# End of Variables - no need to edit anything past that point to deploy a single VM

# Create Resource Group
New-AzureRmResourceGroup `
  -Name $ResourceGroupName `
  -Location $location

## Create storage resources

# Create a storage account and then create a storage container for the Ubuntu Server 16.04 LTS image.

# Create a new storage account
$StorageAccount = New-AzureRMStorageAccount `
  -Location $location `
  -ResourceGroupName $ResourceGroupName `
  -Type $SkuName `
  -Name $StorageAccountName

Set-AzureRmCurrentStorageAccount `
  -StorageAccountName $storageAccountName `
  -ResourceGroupName $resourceGroupName

# Create a storage container to store the virtual machine image
$containerName = 'osdisks'
$container = New-AzureStorageContainer `
  -Name $containerName `
  -Permission Blob


## Create networking resources

# Create a virtual network, subnet, and a public IP address. These resources are used to provide network connectivity to the virtual machine.

# Create a subnet configuration
$subnetConfig = New-AzureRmVirtualNetworkSubnetConfig `
  -Name mySubnet `
  -AddressPrefix 192.168.1.0/24

# Create a virtual network
$vnet = New-AzureRmVirtualNetwork `
  -ResourceGroupName $ResourceGroupName `
  -Location $location `
  -Name MyVnet `
  -AddressPrefix 192.168.0.0/16 `
  -Subnet $subnetConfig

# Create a public IP address and specify a DNS name
$pip = New-AzureRmPublicIpAddress `
  -ResourceGroupName $ResourceGroupName `
  -Location $location `
  -AllocationMethod Static `
  -IdleTimeoutInMinutes 4 `
  -Name "mypublicdns$(Get-Random)"


### Create a network security group and a network security group rule

<#
The network security group secures the virtual machine by using inbound and outbound rules. Create an inbound rule for port 3389 to allow incoming Remote Desktop connections and an inbound rule for port 80 to allow incoming web traffic.
#>

# Create an inbound network security group rule for port 22
$nsgRuleSSH = New-AzureRmNetworkSecurityRuleConfig -Name $nsgRuleSSHName -Protocol Tcp `
-Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
-DestinationPortRange 22 -Access Allow

# Create an inbound network security group rule for port 80
$nsgRuleWeb = New-AzureRmNetworkSecurityRuleConfig -Name $nsgRuleWebName -Protocol Tcp `
-Direction Inbound -Priority 1001 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
-DestinationPortRange 80 -Access Allow

# Create a network security group
$nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName $ResourceGroupName -Location $location `
-Name $nsgName -SecurityRules $nsgRuleSSH,$nsgRuleWeb

### Create a network card for the virtual machine

# The network card connects the virtual machine to a subnet, network security group, and public IP address.

# Create a virtual network card and associate it with public IP address and NSG
$nic = New-AzureRmNetworkInterface `
  -Name myNic `
  -ResourceGroupName $ResourceGroupName `
  -Location $location `
  -SubnetId $vnet.Subnets[0].Id `
  -PublicIpAddressId $pip.Id `
  -NetworkSecurityGroupId $nsg.Id

## Create a virtual machine
<#
Create a virtual machine configuration. This configuration includes the settings used when deploying the virtual machine. For example: user credentials, size, and the virtual machine image.
#>

# Define a credential object.
$UserName='demouser'
$securePassword = ConvertTo-SecureString $VMPassword -AsPlainText -Force
$cred = New-Object System.Management.Automation.PSCredential ($UserName, $securePassword)

# Create the virtual machine configuration object
$VmName = "VirtualMachinelatest"
$VmSize = "Standard_D1"
$VirtualMachine = New-AzureRmVMConfig `
  -VMName $VmName `
  -VMSize $VmSize

$VirtualMachine = Set-AzureRmVMOperatingSystem `
  -VM $VirtualMachine `
  -Linux `
  -ComputerName "MainComputer" `
  -Credential $cred

$VirtualMachine = Set-AzureRmVMSourceImage `
  -VM $VirtualMachine `
  -PublisherName "Canonical" `
  -Offer "UbuntuServer" `
  -Skus "16.04-LTS" `
  -Version "latest"

$osDiskName = "OsDisk"
$osDiskUri = '{0}vhds/{1}-{2}.vhd' -f `
  $StorageAccount.PrimaryEndpoints.Blob.ToString(),`
  $vmName.ToLower(), `
  $osDiskName

# Sets the operating system disk properties on a virtual machine.
$VirtualMachine = Set-AzureRmVMOSDisk `
  -VM $VirtualMachine `
  -Name $osDiskName `
  -VhdUri $OsDiskUri `
  -CreateOption FromImage | `
  Add-AzureRmVMNetworkInterface -Id $nic.Id

# Create the virtual machine.
New-AzureRmVM `
  -ResourceGroupName $ResourceGroupName `
 -Location $location `
  -VM $VirtualMachine
```

## <a name="connect-to-the-virtual-machine"></a>가상 머신에 연결

가상 컴퓨터를 배포한 후 가상 컴퓨터에 SSH 연결을 구성 합니다. [Get-AzureRmPublicIpAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress?view=azurermps-4.3.1) 명령을 사용하여 가상 머신의 공용 IP 주소를 반환합니다.

```powershell
Get-AzureRmPublicIpAddress -ResourceGroupName myResourceGroup | Select IpAddress
```

설치 하는 SSH 사용 하 여 클라이언트 시스템에서 다음 명령을 사용 하 여 가상 머신에 연결 합니다. Windows에서 작업할 경우 사용할 수 있습니다 [Putty](http://www.putty.org/) 연결을 만듭니다.

```
ssh <Public IP Address>
```

메시지가 표시 되 면 로그인 한 사용자로 azureuser를 입력 합니다. SSH 키를 만들 때 암호를 사용한 경우 암호를 제공 해야 합니다.

## <a name="install-the-nginx-web-server"></a>NGINX 웹 서버 설치

패키지 리소스를 업데이트 하 고 최신 NGINX 패키지를 설치 하려면 다음 스크립트를 실행 합니다.

```bash
#!/bin/bash

# update package source
apt-get -y update

# install NGINX
apt-get -y install nginx
```

## <a name="view-the-nginx-welcome-page"></a>NGINX 시작 페이지 보기

NGINX를 설치 및 포트 80 열어서 가상 머신에서 사용 하 여 가상 머신의 공용 IP 주소를 사용 하 여 웹 서버를 액세스할 수 있습니다. 웹 브라우저를 열고 ```http://<public IP address>```합니다.

![NGINX 웹 서버에 대 한 시작 페이지](./media/azure-stack-quick-create-vm-linux-cli/nginx.png)

## <a name="clean-up-resources"></a>리소스 정리

더 이상 필요 하지 않은 리소스를 정리 합니다. 사용할 수는 [Remove-azurermresourcegroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup?view=azurermps-4.3.1) 이러한 리소스를 제거 하는 명령입니다. 리소스 그룹 및 모든 해당 리소스를 삭제 하려면 다음 명령을 실행 합니다.

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup
```

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 기본 Linux server 가상 컴퓨터를 배포 했습니다. Azure Stack virtual machines에 대 한 자세한 내용은로 이동 [Azure Stack에서 Virtual Machines에 대 한 고려 사항](azure-stack-vm-considerations.md)합니다.
