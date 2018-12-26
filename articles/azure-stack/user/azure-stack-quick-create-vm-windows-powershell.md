---
title: Azure Stack에서 PowerShell을 사용 하 여 Windows 가상 머신 만들기 | Microsoft Docs
description: Azure Stack에서 PowerShell을 사용 하 여 Windows 가상 컴퓨터를 만듭니다.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 09/10/2018
ms.author: mabrigg
ms.custom: mvc
ms.openlocfilehash: 4795de2126a34907ecdec69e87a059dbadd0c3d0
ms.sourcegitcommit: 5a9be113868c29ec9e81fd3549c54a71db3cec31
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/11/2018
ms.locfileid: "44380389"
---
# <a name="quickstart-create-a-windows-server-virtual-machine-by-using-powershell-in-azure-stack"></a>빠른 시작: Azure Stack에서 PowerShell을 사용 하 여 Windows Server 가상 머신 만들기

*적용 대상: Azure Stack 통합 시스템 및 Azure Stack 개발 키트*

Azure Stack PowerShell을 사용 하 여 Windows Server 2016 가상 컴퓨터를 만들 수 있습니다. 만들고 가상 머신을 사용 하려면이 문서의 단계를 수행 합니다. 이 문서에서는 단계를 제공 합니다.

* 원격 클라이언트를 사용 하 여 가상 컴퓨터에 연결 합니다.
* IIS 웹 서버를 설치 하 고 기본 홈페이지를 봅니다.
* 리소스를 정리 합니다.

>[!NOTE]
 VPN을 통해 연결 되어 있는 경우 Azure Stack Development Kit 또는 Windows 기반 외부 클라이언트에서이 문서에 설명 된 단계를 실행할 수 있습니다.

## <a name="prerequisites"></a>필수 조건

* 귀하가 Azure Stack 운영자가 추가 되었는지 확인 합니다 **Windows Server 2016** Azure Stack marketplace 이미지입니다.

* Azure Stack 리소스를 만들고 관리 하려면 Azure PowerShell의 특정 버전에 필요 합니다. Azure Stack에 대해 구성 된 PowerShell가 없는 경우 수행 하는 단계 [설치](azure-stack-powershell-install.md) PowerShell.

* 설정 된 Azure Stack PowerShell을 사용 하 여 Azure Stack 환경에 연결 해야 합니다. 명령 참조 [사용자로 PowerShell 사용 하 여 Azure Stack에 연결](azure-stack-powershell-configure-user.md)합니다.

## <a name="create-a-resource-group"></a>리소스 그룹 만들기

리소스 그룹은 리소스는 Azure Stack에 배포 및 관리 되는 논리적 컨테이너입니다. Azure Stack 통합 시스템, 개발 키트에서 리소스 그룹을 만들려면 다음 코드 블록을 실행 합니다. 이 문서에서 모든 변수에 대해 할당 된 값, 이러한 값을 사용 하거나 새 값을 할당할 수 있습니다.

```powershell
# Create variables to store the location and resource group names.
$location = "local"
$ResourceGroupName = "myResourceGroup"

New-AzureRmResourceGroup `
  -Name $ResourceGroupName `
  -Location $location
```

## <a name="create-storage-resources"></a>저장소 리소스 만들기

저장소 계정 및 Windows Server 2016 이미지를 저장할 저장소 컨테이너를 만듭니다.

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

네트워크 보안 그룹 인바운드 및 아웃 바운드 규칙을 사용 하 여 가상 컴퓨터를 보호 합니다. 들어오는 원격 데스크톱 연결을 허용 하도록 3389 포트에 대 한 인바운드 규칙 및 포트 80 들어오는 웹 트래픽을 허용 하도록 인바운드 규칙을 만들 수 있습니다.

```powershell
# Create an inbound network security group rule for port 3389
$nsgRuleRDP = New-AzureRmNetworkSecurityRuleConfig `
  -Name myNetworkSecurityGroupRuleRDP `
  -Protocol Tcp `
  -Direction Inbound `
  -Priority 1000 `
  -SourceAddressPrefix * `
  -SourcePortRange * `
  -DestinationAddressPrefix * `
  -DestinationPortRange 3389 `
  -Access Allow

# Create an inbound network security group rule for port 80
$nsgRuleWeb = New-AzureRmNetworkSecurityRuleConfig `
  -Name myNetworkSecurityGroupRuleWWW `
  -Protocol Tcp `
  -Direction Inbound `
  -Priority 1001 `
  -SourceAddressPrefix * `
  -SourcePortRange * `
  -DestinationAddressPrefix * `
  -DestinationPortRange 80 `
  -Access Allow

# Create a network security group
$nsg = New-AzureRmNetworkSecurityGroup `
  -ResourceGroupName $ResourceGroupName `
  -Location $location `
  -Name myNetworkSecurityGroup `
  -SecurityRules $nsgRuleRDP,$nsgRuleWeb
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

가상 머신 구성을 만듭니다. 이 구성은 가상 컴퓨터를 배포할 때 사용 하는 설정을 포함 합니다. 예를 들어: 자격 증명, 크기 및 가상 머신 이미지입니다.

```powershell
# Define a credential object to store the username and password for the virtual machine
$UserName='demouser'
$Password='Password@123'| ConvertTo-SecureString -Force -AsPlainText
$Credential=New-Object PSCredential($UserName,$Password)

# Create the virtual machine configuration object
$VmName = "VirtualMachinelatest"
$VmSize = "Standard_A1"
$VirtualMachine = New-AzureRmVMConfig `
  -VMName $VmName `
  -VMSize $VmSize

$VirtualMachine = Set-AzureRmVMOperatingSystem `
  -VM $VirtualMachine `
  -Windows `
  -ComputerName "MainComputer" `
  -Credential $Credential

$VirtualMachine = Set-AzureRmVMSourceImage `
  -VM $VirtualMachine `
  -PublisherName "MicrosoftWindowsServer" `
  -Offer "WindowsServer" `
  -Skus "2016-Datacenter" `
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

이전 단계에서 만든 가상 컴퓨터에 원격, 공용 IP 주소를 해야 합니다. 가상 머신의 공용 IP 주소를 가져오려면 다음 명령을 실행 합니다.

```powershell
Get-AzureRmPublicIpAddress `
  -ResourceGroupName $ResourceGroupName | Select IpAddress
```

다음 명령을 사용 하 여 가상 머신에 원격 데스크톱 세션을 만듭니다. 해당 IP 주소를 가상 머신의 publicIPAddress로 바꿉니다. 메시지가 표시 되 면 사용자 이름 및 가상 머신을 만들 때 사용한 암호를 입력 합니다.

```powershell
mstsc /v <publicIpAddress>
```

## <a name="install-iis-via-powershell"></a>PowerShell을 통해 IIS 설치

이제 Azure VM에 로그인했으므로 IIS를 설치하고 웹 트래픽을 허용하는 로컬 방화벽 규칙을 사용하려면 PowerShell의 단일 줄을 사용할 수 있습니다. PowerShell 프롬프트를 열고 다음 명령을 실행합니다.

```powershell
Install-WindowsFeature -name Web-Server -IncludeManagementTools
```

## <a name="view-the-iis-welcome-page"></a>IIS 시작 페이지 보기

IIS를 설치 및 포트 80 열어서 VM에서 사용 하 여 기본 IIS 시작 페이지를 볼 사용자가 선택한 웹 브라우저를 사용할 수 있습니다. 사용 된 *publicIpAddress* 기본 페이지를 방문 하 여 이전 섹션에서 설명 합니다.

![IIS 기본 사이트](./media/azure-stack-quick-create-vm-windows-powershell/default-iis-website.png)

## <a name="delete-the-virtual-machine"></a>가상 머신 삭제

더 이상 필요한 경우 다음 명령을 사용 하 여 가상 컴퓨터 및 해당 관련된 리소스를 포함 하는 리소스 그룹을 제거 하려면:

```powershell
Remove-AzureRmResourceGroup `
  -Name $ResourceGroupName
```

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 간단한 Windows 가상 머신을 배포 했습니다. Azure Stack virtual machines에 대 한 자세한 내용은 계속 [Azure Stack에서 Virtual Machines에 대 한 고려 사항](azure-stack-vm-considerations.md)합니다.
