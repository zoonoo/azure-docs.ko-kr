---
title: "Azure에서 Windows VM 사용자 지정 | Microsoft Docs"
description: "사용자 지정 스크립트 확장 및 Key Vault를 사용하여 Azure에서 Windows VM을 사용자 지정하는 방법에 대해 알아봅니다."
services: virtual-machines-windows
documentationcenter: virtual-machines
author: iainfoulds
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 08/11/2017
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: e0503cef234b01c4eefb1c1de3d88d9a812c4c39
ms.sourcegitcommit: 7136d06474dd20bb8ef6a821c8d7e31edf3a2820
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/05/2017
---
# <a name="how-to-customize-a-windows-virtual-machine-in-azure"></a>Azure에서 Windows 가상 컴퓨터를 사용자 지정하는 방법
신속하고 일관된 방식으로 VM(Virtual Machines)을 구성하려면 일반적으로 자동화 양식이 필요합니다. Windows VM을 사용자 지정하는 일반적인 방법은 [Windows용 사용자 지정 스크립트 확장](extensions-customscript.md)을 사용하는 것입니다. 이 자습서에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * 사용자 지정 스크립트 확장을 사용하여 IIS 설치
> * 사용자 지정 스크립트 확장을 사용하는 VM 만들기
> * 확장이 적용된 후 실행 중인 IIS 사이트 보기

[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

PowerShell을 로컬로 설치하고 사용하도록 선택한 경우 이 자습서에서는 Azure PowerShell 모듈 버전 3.6 이상을 실행해야 합니다. ` Get-Module -ListAvailable AzureRM`을 실행하여 버전을 찾습니다. 업그레이드해야 하는 경우 [Azure PowerShell 모듈 설치](/powershell/azure/install-azurerm-ps)를 참조하세요. 또한 PowerShell을 로컬로 실행하는 경우 `Login-AzureRmAccount`를 실행하여 Azure와 연결해야 합니다. 


## <a name="custom-script-extension-overview"></a>사용자 지정 스크립트 확장 개요
사용자 지정 스크립트 확장은 Azure VM에서 스크립트를 다운로드하고 실행합니다. 이 확장은 배포 후 구성, 소프트웨어 설치 또는 기타 구성/관리 작업에 유용합니다. 스크립트를 Azure Storage 또는 GitHub에서 다운로드하거나 확장 런타임에서 Azure Portal에 제공할 수 있습니다.

사용자 지정 스크립트 확장은 Azure Resource Manager 템플릿과 통합되고, Azure CLI, PowerShell, Azure Portal 또는 Azure 가상 컴퓨터 REST API를 사용하여 실행할 수도 있습니다.

Windows VM 및 Linux VM 둘 다에 사용자 지정 스크립트 확장을 사용할 수 있습니다.


## <a name="create-virtual-machine"></a>가상 컴퓨터 만들기
VM을 만들려면 먼저 [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup)을 사용하여 리소스 그룹을 만듭니다. 다음 예제에서는 *EastUS* 위치에 *myResourceGroupAutomate*라는 리소스 그룹을 만듭니다.

```azurepowershell-interactive
New-AzureRmResourceGroup -ResourceGroupName myResourceGroupAutomate -Location EastUS
```

[Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential)을 사용하여 VM의 관리자 사용자 이름과 암호를 설정합니다.

```azurepowershell-interactive
$cred = Get-Credential
```

이제 [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm)을 사용하여 VM을 만들 수 있습니다. 다음 예제에서는 필요한 가상 네트워크 구성 요소, OS 구성을 만든 다음 *myVM*이라는 VM을 만듭니다.

```azurepowershell-interactive
# Create a subnet configuration
$subnetConfig = New-AzureRmVirtualNetworkSubnetConfig `
    -Name mySubnet `
    -AddressPrefix 192.168.1.0/24

# Create a virtual network
$vnet = New-AzureRmVirtualNetwork `
    -ResourceGroupName myResourceGroupAutomate `
    -Location EastUS `
    -Name myVnet `
    -AddressPrefix 192.168.0.0/16 `
    -Subnet $subnetConfig

# Create a public IP address and specify a DNS name
$publicIP = New-AzureRmPublicIpAddress `
    -ResourceGroupName myResourceGroupAutomate `
    -Location EastUS `
    -AllocationMethod Static `
    -IdleTimeoutInMinutes 4 `
    -Name "myPublicIP"

# Create an inbound network security group rule for port 3389
$nsgRuleRDP = New-AzureRmNetworkSecurityRuleConfig `
    -Name myNetworkSecurityGroupRuleRDP  `
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
    -Name myNetworkSecurityGroupRuleWWW  `
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
    -ResourceGroupName myResourceGroupAutomate `
    -Location EastUS `
    -Name myNetworkSecurityGroup `
    -SecurityRules $nsgRuleRDP,$nsgRuleWeb

# Create a virtual network card and associate with public IP address and NSG
$nic = New-AzureRmNetworkInterface `
    -Name myNic `
    -ResourceGroupName myResourceGroupAutomate `
    -Location EastUS `
    -SubnetId $vnet.Subnets[0].Id `
    -PublicIpAddressId $publicIP.Id `
    -NetworkSecurityGroupId $nsg.Id

# Create a virtual machine configuration
$vmConfig = New-AzureRmVMConfig -VMName myVM -VMSize Standard_DS2 | `
Set-AzureRmVMOperatingSystem -Windows -ComputerName myVM -Credential $cred | `
Set-AzureRmVMSourceImage -PublisherName MicrosoftWindowsServer `
    -Offer WindowsServer -Skus 2016-Datacenter -Version latest | `
Add-AzureRmVMNetworkInterface -Id $nic.Id

# Create a virtual machine using the configuration
New-AzureRmVM -ResourceGroupName myResourceGroupAutomate -Location EastUS -VM $vmConfig
```

리소스 및 VM을 만드는 데 몇 분 정도 걸립니다.


## <a name="automate-iis-install"></a>IIS 설치 자동화
[Set-AzureRmVMExtension](/powershell/module/azurerm.compute/set-azurermvmextension)을 사용하여 사용자 지정 스크립트 확장을 설치합니다. 확장은 `powershell Add-WindowsFeature Web-Server`를 실행하여 IIS 웹 서버를 설치한 다음 *Default.htm* 페이지를 업데이트하여 VM의 호스트 이름을 표시합니다.

```azurepowershell-interactive
Set-AzureRmVMExtension -ResourceGroupName myResourceGroupAutomate `
    -ExtensionName IIS `
    -VMName myVM `
    -Publisher Microsoft.Compute `
    -ExtensionType CustomScriptExtension `
    -TypeHandlerVersion 1.4 `
    -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server; powershell Add-Content -Path \"C:\\inetpub\\wwwroot\\Default.htm\" -Value $($env:computername)"}' `
    -Location EastUS
```


## <a name="test-web-site"></a>웹 사이트 테스트
[Get-AzureRmPublicIPAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress)를 사용하여 부하 분산 장치의 공용 IP 주소를 가져옵니다. 다음 예제에서는 앞서 만든 *myPublicIP*의 IP 주소를 가져옵니다.

```azurepowershell-interactive
Get-AzureRmPublicIPAddress `
    -ResourceGroupName myResourceGroupAutomate `
    -Name myPublicIP | select IpAddress
```

그런 다음 웹 브라우저에 공용 IP 주소를 입력할 수 있습니다. 다음 예제와 같이 부하 분산 장치가 트래픽을 분산한 VM의 호스트 이름을 포함하여 웹 사이트가 표시됩니다.

![실행 중인 IIS 웹 사이트](./media/tutorial-automate-vm-deployment/running-iis-website.png)


## <a name="next-steps"></a>다음 단계

이 자습서에서는 VM에서 IIS 설치를 자동화합니다. 다음 방법에 대해 알아보았습니다.

> [!div class="checklist"]
> * 사용자 지정 스크립트 확장을 사용하여 IIS 설치
> * 사용자 지정 스크립트 확장을 사용하는 VM 만들기
> * 확장이 적용된 후 실행 중인 IIS 사이트 보기

사용자 지정 VM 이미지를 만드는 방법에 대해 알아보려면 다음 자습서로 이동합니다.

> [!div class="nextstepaction"]
> [사용자 지정 VM 이미지 만들기](./tutorial-custom-images.md)
