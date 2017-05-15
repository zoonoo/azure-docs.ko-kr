---
title: "Azure 빠른 시작 - Windows VM 만들기 PowerShell | Microsoft Docs"
description: "PowerShell을 사용하여 Windows 가상 컴퓨터를 만드는 방법을 빠르게 이해합니다."
services: virtual-machines-windows
documentationcenter: virtual-machines
author: neilpeterson
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 05/02/2017
ms.author: nepeters
ms.translationtype: Human Translation
ms.sourcegitcommit: 2db2ba16c06f49fd851581a1088df21f5a87a911
ms.openlocfilehash: 7cdbd8d9fbca20d53c8b920b6bc44851b95d2a07
ms.contentlocale: ko-kr
ms.lasthandoff: 05/09/2017


---

# <a name="create-a-windows-virtual-machine-with-powershell"></a>PowerShell을 사용하여 Windows 가상 컴퓨터 만들기

PowerShell 명령줄 또는 스크립트에서 Azure 리소스를 만들고 관리하는 데 Azure PowerShell 모듈이 사용됩니다. 이 가이드에서는 PowerShell을 사용하여 Windows Server 2016이 실행되는 Azure Virtual Machine을 만드는 방법을 자세히 설명합니다. 배포가 완료되면 서버에 연결하고 IIS를 설치합니다.  

Azure 구독이 아직 없는 경우 시작하기 전에 [무료 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 을 만듭니다.

이 자습서에는 Azure PowerShell 모듈 버전 3.6 이상이 필요합니다. ` Get-Module -ListAvailable AzureRM`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우 [Azure PowerShell 모듈 설치](/powershell/azure/install-azurerm-ps)를 참조하세요.

## <a name="log-in-to-azure"></a>Azure에 로그인

`Login-AzureRmAccount` 명령으로 Azure 구독에 로그인하고 화면의 지시를 따릅니다.

```powershell
Login-AzureRmAccount
```

## <a name="create-resource-group"></a>리소스 그룹 만들기

[New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup)을 사용하여 Azure 리소스 그룹을 만듭니다. 리소스 그룹은 Azure 리소스가 배포 및 관리되는 논리적 컨테이너입니다. 

```powershell
New-AzureRmResourceGroup -Name myResourceGroup -Location EastUS
```

## <a name="create-networking-resources"></a>네트워킹 리소스 만들기

### <a name="create-a-virtual-network-subnet-and-a-public-ip-address"></a>가상 네트워크, 서브넷 및 공용 IP 주소를 만듭니다. 
이러한 리소스는 가상 컴퓨터에 네트워크 연결을 제공하고 인터넷에 연결하는 데 사용됩니다.

```powershell
# Create a subnet configuration
$subnetConfig = New-AzureRmVirtualNetworkSubnetConfig -Name mySubnet -AddressPrefix 192.168.1.0/24

# Create a virtual network
$vnet = New-AzureRmVirtualNetwork -ResourceGroupName myResourceGroup -Location EastUS `
    -Name MYvNET -AddressPrefix 192.168.0.0/16 -Subnet $subnetConfig

# Create a public IP address and specify a DNS name
$pip = New-AzureRmPublicIpAddress -ResourceGroupName myResourceGroup -Location EastUS `
    -AllocationMethod Static -IdleTimeoutInMinutes 4 -Name "mypublicdns$(Get-Random)"
```

### <a name="create-a-network-security-group-and-a-network-security-group-rule"></a>네트워크 보안 그룹 및 네트워크 보안 그룹 규칙을 만듭니다. 
네트워크 보안 그룹은 인바운드 및 아웃바운드 규칙을 사용하여 가상 컴퓨터를 보호합니다. 이 경우 포트 3389에 대해 들어오는 원격 데스크톱 연결을 허용하는 인바운드 규칙이 만들어집니다. 포트 80에 대한 인바운드 규칙을 만들어서 들어오는 웹 트래픽을 허용하려고 합니다.

```powershell
# Create an inbound network security group rule for port 3389
$nsgRuleRDP = New-AzureRmNetworkSecurityRuleConfig -Name myNetworkSecurityGroupRuleRDP  -Protocol Tcp `
    -Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
    -DestinationPortRange 3389 -Access Allow

# Create an inbound network security group rule for port 80
$nsgRuleWeb = New-AzureRmNetworkSecurityRuleConfig -Name myNetworkSecurityGroupRuleWWW  -Protocol Tcp `
    -Direction Inbound -Priority 1001 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
    -DestinationPortRange 80 -Access Allow

# Create a network security group
$nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName myResourceGroup -Location EastUS `
    -Name myNetworkSecurityGroup -SecurityRules $nsgRuleRDP,$nsgRuleWeb
```

### <a name="create-a-network-card-for-the-virtual-machine"></a>가상 컴퓨터에 대한 네트워크 카드를 만듭니다. 
[New-AzureRmNetworkInterface](/powershell/module/azurerm.network/new-azurermnetworkinterface)를 사용하여 가상 컴퓨터에 네트워크 카드를 만듭니다. 네트워크 카드는 서브넷, 네트워크 보안 그룹 및 공용 IP 주소에 가상 컴퓨터를 연결합니다.

```powershell
# Create a virtual network card and associate with public IP address and NSG
$nic = New-AzureRmNetworkInterface -Name myNic -ResourceGroupName myResourceGroup -Location EastUS `
    -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id -NetworkSecurityGroupId $nsg.Id
```

## <a name="create-virtual-machine"></a>가상 컴퓨터 만들기

가상 컴퓨터 구성을 만듭니다. 이 구성은 가상 컴퓨터 이미지, 크기 및 인증 구성 등 가상 컴퓨터를 배포할 때 사용되는 설정을 포함합니다. 이 단계를 실행할 때 자격 증명을 묻는 메시지가 나타납니다. 입력하는 값은 가상 컴퓨터에 대한 사용자 이름 및 암호로 구성됩니다.

```powershell
# Define a credential object
$cred = Get-Credential

# Create a virtual machine configuration
$vmConfig = New-AzureRmVMConfig -VMName myVM -VMSize Standard_DS2 | `
    Set-AzureRmVMOperatingSystem -Windows -ComputerName myVM -Credential $cred | `
    Set-AzureRmVMSourceImage -PublisherName MicrosoftWindowsServer -Offer WindowsServer `
    -Skus 2016-Datacenter -Version latest | Add-AzureRmVMNetworkInterface -Id $nic.Id
```

[New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm)을 사용하여 가상 컴퓨터를 만듭니다.

```powershell
New-AzureRmVM -ResourceGroupName myResourceGroup -Location EastUS -VM $vmConfig
```

## <a name="connect-to-virtual-machine"></a>가상 컴퓨터에 연결

배포가 완료된 후 가상 컴퓨터에 대한 원격 데스크톱 연결을 만듭니다.

[Get-AzureRmPublicIpAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress) 명령을 사용하여 가상 컴퓨터의 공용 IP 주소를 반환합니다. 다음 단계에서 웹 연결을 테스트하기 위해 브라우저와 연결할 수 있도록 이 IP 주소를 기록해 둡니다.

```powershell
Get-AzureRmPublicIpAddress -ResourceGroupName myResourceGroup | Select IpAddress
```

다음 명령을 사용하여 가상 컴퓨터와의 원격 데스크톱 세션을 만듭니다. 해당 IP 주소를 가상 컴퓨터의 *publicIPAddress*로 바꿉니다. 가상 컴퓨터를 만들 때 사용되는 자격 증명을 묻는 메시지가 표시되면 입력합니다.

```bash 
mstsc /v:<publicIpAddress>
```

## <a name="install-iis-via-powershell"></a>PowerShell을 통해 IIS 설치

이제 Azure VM에 로그인했으므로 IIS를 설치하고 웹 트래픽을 허용하는 로컬 방화벽 규칙을 사용하려면 PowerShell의 단일 줄을 사용할 수 있습니다. PowerShell 프롬프트를 열고 다음 명령을 실행합니다.

```powershell
Install-WindowsFeature -name Web-Server -IncludeManagementTools
```

## <a name="view-the-iis-welcome-page"></a>IIS 시작 페이지 보기

IIS를 설치하고 현재 포트 80이 인터넷에서 VM에 열려 있으면 사용자가 선택한 웹 브라우저를 사용하여 기본 IIS 시작 페이지를 볼 수 있습니다. 위에 설명한 *publicIpAddress*를 사용하여 기본 페이지를 방문해야 합니다. 

![IIS 기본 사이트](./media/quick-create-powershell/default-iis-website.png) 

## <a name="delete-virtual-machine"></a>가상 컴퓨터 삭제

더 이상 필요하지 않은 경우 [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) 명령을 사용하여 리소스 그룹, VM 및 모든 관련된 리소스를 제거할 수 있습니다.

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup
```

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서 간단한 가상 컴퓨터, 네트워크 보안 그룹 규칙을 배포했으며 웹 서버를 설치했습니다. Azure 가상 컴퓨터에 대한 자세한 내용을 알아보려면 Windows VM의 자습서를 계속 진행합니다.

> [!div class="nextstepaction"]
> [Azure Windows 가상 컴퓨터 자습서](./tutorial-manage-vm.md)

