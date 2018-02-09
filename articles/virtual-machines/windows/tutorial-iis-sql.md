---
title: "Azure에서 SQL&#92;IIS&#92;.NET 스택을 실행하는 VM 만들기| Microsoft Docs"
description: "자습서 - Windows 가상 머신에 Azure SQL, IIS, .NET 스택 설치."
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 10/24/2017
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 6533ab205e07243e2f757ea0a66028e1d140c52b
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/01/2018
---
# <a name="install-a-sql92iis92net-stack-in-azure"></a>Azure에서 SQL&#92;IIS&#92;.NET 스택 설치

이 자습서에서는 Azure PowerShell을 사용하여 SQL&#92;IIS&#92;.NET 스택을 설치합니다. 스택은 Windows Server 2016을 실행하는 두 개의 VM으로 구성되며, 하나는 IIS와 .NET과 실행하고 다른 하나는 SQL Server와 실행합니다.

> [!div class="checklist"]
> * New-AzVM을 사용하여 VM 만들기
> * VM에 IIS 및 .NET Core SDK 설치
> * SQL Server를 실행하는 VM 만들기
> * SQL Server 확장 설치

[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

PowerShell을 로컬로 설치하고 사용하도록 선택한 경우 이 자습서에서는 Azure PowerShell 모듈 버전 5.1.1 이상을 실행해야 합니다. ` Get-Module -ListAvailable AzureRM`을 실행하여 버전을 찾습니다. 업그레이드해야 하는 경우 [Azure PowerShell 모듈 설치](/powershell/azure/install-azurerm-ps)를 참조하세요. 또한 PowerShell을 로컬로 실행하는 경우 `Login-AzureRmAccount`를 실행하여 Azure와 연결해야 합니다.

## <a name="create-a-iis-vm"></a>IIS VM 만들기 

이 예제에서는 PowerShell Cloud Shell에서 [New-AzVM](https://www.powershellgallery.com/packages/AzureRM.Compute.Experiments) cmdlet을 사용하여 Windows Server 2016 VM을 빠르게 만든 다음 IIS와 .NET Framework를 설치합니다. IIS 및 SQL VM은 리소스 그룹 및 가상 네트워크를 공유하므로 그러한 이름의 변수를 만듭니다.

이 창에서 Cloud Shell을 시작하려면 코드 블록의 오른쪽 위에 있는 **시도** 단추를 클릭합니다. cmd 프롬프트에 가상 머신에 대한 자격 증명을 제공하라는 메시지가 표시됩니다.

```azurepowershell-interactive
$vmName = "IISVM$(Get-Random)"
$vNetName = "myIISSQLvNet"
$resourceGroup = "myIISSQLGroup"
New-AzureRMVm -Name $vmName -ResourceGroupName $resourceGroup -VirtualNetworkName $vNetName 
```

사용자 지정 스크립트 확장을 사용하여 IIS 및 .NET Framework를 설치합니다.

```azurepowershell-interactive

Set-AzureRmVMExtension -ResourceGroupName $resourceGroup `
    -ExtensionName IIS `
    -VMName $vmName `
    -Publisher Microsoft.Compute `
    -ExtensionType CustomScriptExtension `
    -TypeHandlerVersion 1.4 `
    -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server,Web-Asp-Net45,NET-Framework-Features"}' `
    -Location EastUS
```

## <a name="azure-sql-vm"></a>Azure SQL VM

미리 구성된 Azure 마켓플레이스의 SQL 서버 이미지를 사용하여 SQL VM을 만듭니다. 먼저 VM을 만든 다음 VM에 SQL Server 확장을 설치합니다. 


```azurepowershell-interactive
# Create user object. You get a pop-up prompting you to enter the credentials for the VM.
$cred = Get-Credential -Message "Enter a username and password for the virtual machine."

# Create a subnet configuration
$vNet = Get-AzureRmVirtualNetwork -Name $vNetName -ResourceGroupName $resourceGroup
Add-AzureRmVirtualNetworkSubnetConfig -Name mySQLSubnet -VirtualNetwork $vNet -AddressPrefix "192.168.2.0/24"
Set-AzureRmVirtualNetwork -VirtualNetwork $vNet


# Create a public IP address and specify a DNS name
$pip = New-AzureRmPublicIpAddress -ResourceGroupName $resourceGroup -Location eastus `
  -Name "mypublicdns$(Get-Random)" -AllocationMethod Static -IdleTimeoutInMinutes 4

# Create an inbound network security group rule for port 3389
$nsgRuleRDP = New-AzureRmNetworkSecurityRuleConfig -Name myNetworkSecurityGroupRuleRDP  -Protocol Tcp `
  -Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
  -DestinationPortRange 3389 -Access Allow


# Create a network security group
$nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName $resourceGroup -Location eastus `
  -Name myNetworkSecurityGroup -SecurityRules $nsgRuleRDP

# Create a virtual network card and associate with public IP address and NSG
$nic = New-AzureRmNetworkInterface -Name mySQLNic -ResourceGroupName $resourceGroup -Location eastus `
  -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id -NetworkSecurityGroupId $nsg.Id

# Create a virtual machine configuration
$vmConfig = New-AzureRmVMConfig -VMName mySQLVM -VMSize Standard_D1 | `
Set-AzureRmVMOperatingSystem -Windows -ComputerName mySQLVM -Credential $cred | `
Set-AzureRmVMSourceImage -PublisherName MicrosoftSQLServer -Offer SQL2014SP2-WS2012R2 -Skus Enterprise -Version latest | `
Add-AzureRmVMNetworkInterface -Id $nic.Id

# Create the VM
New-AzureRmVM -ResourceGroupName $resourceGroup -Location eastus -VM $vmConfig
```

[Set-AzureRmVMSqlServerExtension](/powershell/module/azurerm.compute/set-azurermvmsqlserverextension)을 사용하여 [SQL Server 확장](/sql/virtual-machines-windows-sql-server-agent-extension.md)을 SQL VM에 추가합니다.

```azurepowershell-interactive
Set-AzureRmVMSqlServerExtension -ResourceGroupName $resourceGroup -VMName mySQLVM -name "SQLExtension"
```

## <a name="next-steps"></a>다음 단계

이 자습서에서는 Azure PowerShell을 사용하여 SQL&#92;IIS&#92;.NET 스택을 설치했습니다. 다음 방법에 대해 알아보았습니다.

> [!div class="checklist"]
> * New-AzVM을 사용하여 VM 만들기
> * VM에 IIS 및 .NET Core SDK 설치
> * SQL Server를 실행하는 VM 만들기
> * SQL Server 확장 설치

SSL 인증서로 IIS 웹 서버를 보호하는 방법에 대해 알아보려면 다음 자습서로 이동합니다.

> [!div class="nextstepaction"]
> [SSL 인증서로 IIS 웹 서버 보호](tutorial-secure-web-server.md)

