---
title: 자습서 - Azure에서 SQL, IIS, .NET 스택을 실행하는 VM 만들기 | Microsoft Docs
description: 이 자습서에서는 Azure에서 Windows 가상 머신에 Azure SQL, IIS, .NET 스택을 설치하는 방법을 알아봅니다.
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 12/05/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: aa8f4e188761c50391cd2ead49ae8d8b9081188f
ms.sourcegitcommit: 943af92555ba640288464c11d84e01da948db5c0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/09/2019
ms.locfileid: "55983494"
---
# <a name="tutorial-install-the-sql-iis-net-stack-in-a-windows-vm-with-azure-powershell"></a>자습서: Azure PowerShell을 사용하여 Windows VM에 SQL, IIS, .NET 스택 설치

이 자습서에서는 Azure PowerShell을 사용하여 SQL, IIS, .NET 스택을 설치합니다. 스택은 Windows Server 2016을 실행하는 두 개의 VM으로 구성되며, 하나는 IIS와 .NET과 실행하고 다른 하나는 SQL Server와 실행합니다.

> [!div class="checklist"]
> * VM 만들기 
> * VM에 IIS 및 .NET Core SDK 설치
> * SQL Server를 실행하는 VM 만들기
> * SQL Server 확장 설치

## <a name="launch-azure-cloud-shell"></a>Azure Cloud Shell 시작

Azure Cloud Shell은 이 항목의 단계를 실행하는 데 무료로 사용할 수 있는 대화형 셸입니다. 공용 Azure 도구가 사전 설치되어 계정에서 사용하도록 구성되어 있습니다. 

Cloud Shell을 열려면 코드 블록의 오른쪽 위 모서리에 있는 **사용해 보세요**를 선택하기만 하면 됩니다. 또한 [https://shell.azure.com/powershell](https://shell.azure.com/powershell)로 이동하여 별도의 브라우저 탭에서 Cloud Shell을 시작할 수도 있습니다. **복사**를 선택하여 코드 블록을 복사하여 Cloud Shell에 붙여넣고, Enter 키를 눌러 실행합니다.

## <a name="create-a-iis-vm"></a>IIS VM 만들기 

이 예제에서는 PowerShell Cloud Shell에서 [New-AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm) cmdlet을 사용하여 Windows Server 2016 VM을 빠르게 만든 다음, IIS와 .NET Framework를 설치합니다. IIS 및 SQL VM은 리소스 그룹 및 가상 네트워크를 공유하므로 그러한 이름의 변수를 만듭니다.


```azurepowershell-interactive
$vmName = "IISVM"
$vNetName = "myIISSQLvNet"
$resourceGroup = "myIISSQLGroup"
New-AzVm `
    -ResourceGroupName $resourceGroup `
    -Name $vmName `
    -Location "East US" `
    -VirtualNetworkName $vNetName `
    -SubnetName "myIISSubnet" `
    -SecurityGroupName "myNetworkSecurityGroup" `
    -AddressPrefix 192.168.0.0/16 `
    -PublicIpAddressName "myIISPublicIpAddress" `
    -OpenPorts 80,3389 
```

[Set-AzVMExtension](https://docs.microsoft.com/powershell/module/az.compute/set-azvmextension) cmdlet으로 사용자 지정 스크립트 확장을 사용하여 IIS 및 .NET Framework를 설치합니다.

```azurepowershell-interactive
Set-AzVMExtension `
    -ResourceGroupName $resourceGroup `
    -ExtensionName IIS `
    -VMName $vmName `
    -Publisher Microsoft.Compute `
    -ExtensionType CustomScriptExtension `
    -TypeHandlerVersion 1.4 `
    -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server,Web-Asp-Net45,NET-Framework-Features"}' `
    -Location EastUS
```

## <a name="create-another-subnet"></a>다른 서브넷 만들기

SQL VM에 대한 두 번째 서브넷을 만듭니다. [Get-AzVirtualNetwork]{/powershell/module/az.network/get-azvirtualnetwork}를 사용하여 vNet을 가져옵니다.

```azurepowershell-interactive
$vNet = Get-AzVirtualNetwork `
   -Name $vNetName `
   -ResourceGroupName $resourceGroup
```

[Add-AzVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/az.network/add-azvirtualnetworksubnetconfig)를 사용하여 서브넷의 구성을 만듭니다.


```azurepowershell-interactive
Add-AzVirtualNetworkSubnetConfig `
   -AddressPrefix 192.168.0.0/24 `
   -Name mySQLSubnet `
   -VirtualNetwork $vNet `
   -ServiceEndpoint Microsoft.Sql
```

[Set-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/set-azvirtualnetwork)를 사용하여 새 서브넷 구성으로 vNet을 업데이트합니다.
   
```azurepowershell-interactive   
$vNet | Set-AzVirtualNetwork
```

## <a name="azure-sql-vm"></a>Azure SQL VM

미리 구성된 Azure 마켓플레이스의 SQL 서버 이미지를 사용하여 SQL VM을 만듭니다. 먼저 VM을 만든 다음 VM에 SQL Server 확장을 설치합니다. 


```azurepowershell-interactive
New-AzVm `
    -ResourceGroupName $resourceGroup `
    -Name "mySQLVM" `
    -ImageName "MicrosoftSQLServer:SQL2016SP1-WS2016:Enterprise:latest" `
    -Location eastus `
    -VirtualNetworkName $vNetName `
    -SubnetName "mySQLSubnet" `
    -SecurityGroupName "myNetworkSecurityGroup" `
    -PublicIpAddressName "mySQLPublicIpAddress" `
    -OpenPorts 3389,1401 
```

[Set-AzVMSqlServerExtension](https://docs.microsoft.com/powershell/module/az.compute/set-azvmsqlserverextension)을 사용하여 [SQL Server 확장](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-agent-extension)을 SQL VM에 추가합니다.

```azurepowershell-interactive
Set-AzVMSqlServerExtension `
   -ResourceGroupName $resourceGroup  `
   -VMName mySQLVM `
   -Name "SQLExtension" `
   -Location "EastUS"
```

## <a name="next-steps"></a>다음 단계

이 자습서에서는 Azure PowerShell을 사용하여 SQL&#92;IIS&#92;.NET 스택을 설치했습니다. 다음 방법에 대해 알아보았습니다.

> [!div class="checklist"]
> * VM 만들기 
> * VM에 IIS 및 .NET Core SDK 설치
> * SQL Server를 실행하는 VM 만들기
> * SQL Server 확장 설치

SSL 인증서로 IIS 웹 서버를 보호하는 방법에 대해 알아보려면 다음 자습서로 이동합니다.

> [!div class="nextstepaction"]
> [SSL 인증서로 IIS 웹 서버 보호](tutorial-secure-web-server.md)

