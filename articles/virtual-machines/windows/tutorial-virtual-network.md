---
title: "Azure Virtual Network 및 Windows Virtual Machines | Microsoft Docs"
description: "자습서 - Azure PowerShell을 사용하여 Azure Virtual Network 및 Windows Virtual Machines 관리"
services: virtual-machines-windows
documentationcenter: virtual-machines
author: davidmu1
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 05/02/2017
ms.author: davidmu
ms.custom: mvc
ms.translationtype: Human Translation
ms.sourcegitcommit: 2db2ba16c06f49fd851581a1088df21f5a87a911
ms.openlocfilehash: 7fd0ace35cfe0286c874e4a75b733053aa945d39
ms.contentlocale: ko-kr
ms.lasthandoff: 05/09/2017

---

# <a name="manage-azure-virtual-networks-and-windows-virtual-machines-with-azure-powershell"></a>Azure PowerShell을 사용하여 Azure Virtual Network 및 Windows Virtual Machines 관리

Azure Virtual Machines는 내부 및 외부 네트워크 통신에서 Azure 네트워킹을 사용합니다. 이 자습서에서는 가상 네트워크에 여러 VM(가상 컴퓨터)을 만들고 이러한 컴퓨터 간에 네트워크 연결을 구성합니다. 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * 가상 네트워크 만들기
> * 가상 네트워크 서브넷 만들기
> * 네트워크 보안 그룹을 사용하여 네트워크 트래픽 제어
> * 트래픽 규칙의 실제 동작 보기

이 자습서에는 Azure PowerShell 모듈 버전 3.6 이상이 필요합니다. ` Get-Module -ListAvailable AzureRM`을 실행하여 버전을 찾습니다. 업그레이드해야 하는 경우 [Azure PowerShell 모듈 설치](/powershell/azure/install-azurerm-ps)를 참조하세요.

## <a name="create-vnet"></a>VNet 만들기

VNet은 클라우드의 사용자 네트워크를 나타내는 표현입니다. 구독 전용 Azure 클라우드를 논리적으로 격리한 것이 VNet입니다. VNet 내에서 서브넷, 해당 서브넷에 대한 연결 규칙 및 VM에서 서브넷으로의 연결을 찾습니다.

다른 Azure 리소스를 만들려면 먼저 [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup)을 사용하여 리소스 그룹을 만들어야 합니다. 다음 예제에서는 *EastUS* 위치에 *myRGNetwork*라는 리소스 그룹을 만듭니다.

```powershell
New-AzureRmResourceGroup -ResourceGroupName myRGNetwork -Location EastUS
```

서브넷은 VNet의 자식 리소스이며, IP 주소 접두사를 사용하여 CIDR 블록 내의 주소 공간 세그먼트를 정의하는 데 도움이 됩니다. NIC는 서브넷에 추가하고 VM에 연결하여 다양한 워크로드에 대한 연결을 제공할 수 있습니다.

[New-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig)를 사용하여 서브넷을 만듭니다.

```powershell
$frontendSubnet = New-AzureRmVirtualNetworkSubnetConfig `
  -Name myFrontendSubnet `
  -AddressPrefix 10.0.0.0/24
```

[New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork) 명령을 통해 *myFrontendSubnet*을 사용하여 *myVNet*이라는 VNET을 만듭니다.

```powershell
$vnet = New-AzureRmVirtualNetwork `
  -ResourceGroupName myRGNetwork `
  -Location EastUS `
  -Name myVNet `
  -AddressPrefix 10.0.0.0/16 `
  -Subnet $frontendSubnet
```

## <a name="create-front-end-vm"></a>프런트 엔드 VM 만들기

VM이 VNet에서 통신하려면 가상 네트워크 인터페이스(NIC)가 필요합니다. *myFrontendVM*은 인터넷에서 액세스되므로 공용 IP 주소가 필요합니다. 

[New-AzureRmPublicIpAddress](/powershell/module/azurerm.network/new-azurermpublicipaddress)를 사용하여 공용 IP 주소를 만듭니다.

```powershell
$pip = New-AzureRmPublicIpAddress `
  -ResourceGroupName myRGNetwork `
  -Location EastUS `
  -AllocationMethod Static `
  -Name myPublicIPAddress
```

[New-AzureRmNetworkInterface](/powershell/module/azurerm.network/new-azurermnetworkinterface)를 사용하여 NIC를 만듭니다.


```powershell
$frontendNic = New-AzureRmNetworkInterface `
  -ResourceGroupName myRGNetwork `
  -Location EastUS `
  -Name myFrontendNic `
  -SubnetId $vnet.Subnets[0].Id `
  -PublicIpAddressId $pip.Id
```

[Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential)을 사용하여 VM의 컴퓨터의 관리자 계정에 필요한 사용자 이름 및 암호를 설정합니다.

```powershell
$cred = Get-Credential
```

[New-AzureRmVMConfig](/powershell/module/azurerm.compute/new-azurermvmconfig), [Set-AzureRmVMOperatingSystem](/powershell/module/azurerm.compute/set-azurermvmoperatingsystem), [Set-AzureRmVMSourceImage](/powershell/module/azurerm.compute/set-azurermvmsourceimage), [Set-AzureRmVMOSDisk](/powershell/module/azurerm.compute/set-azurermvmosdisk), [Add-AzureRmVMNetworkInterface](/powershell/module/azurerm.compute/add-azurermvmnetworkinterface) 및 [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm)을 사용하여 VM을 만듭니다. 

```powershell
$frontendVM = New-AzureRmVMConfig `
    -VMName myFrontendVM `
    -VMSize Standard_D1
$frontendVM = Set-AzureRmVMOperatingSystem `
    -VM $frontendVM `
    -Windows `
    -ComputerName myFrontendVM `
    -Credential $cred `
    -ProvisionVMAgent `
    -EnableAutoUpdate
$frontendVM = Set-AzureRmVMSourceImage `
    -VM $frontendVM `
    -PublisherName MicrosoftWindowsServer `
    -Offer WindowsServer `
    -Skus 2016-Datacenter `
    -Version latest
$frontendVM = Set-AzureRmVMOSDisk `
    -VM $frontendVM `
    -Name myFrontendOSDisk `
    -DiskSizeInGB 128 `
    -CreateOption FromImage `
    -Caching ReadWrite
$frontendVM = Add-AzureRmVMNetworkInterface `
    -VM $frontendVM `
    -Id $frontendNic.Id
New-AzureRmVM `
    -ResourceGroupName myRGNetwork `
    -Location EastUS `
    -VM $frontendVM
```

## <a name="install-web-server"></a>웹 서버 설치

원격 데스크톱 세션을 사용하여 *myFrontendVM*에 IIS를 설치할 수 있습니다. 액세스하려면 VM의 공용 IP 주소를 가져와야 합니다.

[Get-AzureRmPublicIPAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress)를 사용하여 *myFrontendVM*의 공용 IP 주소를 가져올 수 있습니다. 다음 예제에서는 앞서 만든 *myPublicIPAddress*의 IP 주소를 가져옵니다.

```powershell
Get-AzureRmPublicIPAddress `
    -ResourceGroupName myRGNetwork `
    -Name myPublicIPAddress | select IpAddress
```

이후 단계에서 사용할 수 있도록 이 IP 주소를 기록해 둡니다.

다음 명령을 사용하여 *myFrontendVM*과의 원격 데스크톱 세션을 만듭니다. *<publicIPAddress>*를 이전에 기록한 주소로 바꿉니다. VM을 만들 때 사용되는 자격 증명을 묻는 메시지가 표시되면 입력합니다.

```
mstsc /v:<publicIpAddress>
``` 

이제 *myFrontendVM*에 로그인했으므로 PowerShell 한 줄로 IIS를 설치하고 웹 트래픽을 허용하도록 로컬 방화벽 규칙을 사용할 수 있습니다. PowerShell 프롬프트를 열고 다음 명령을 실행합니다.

[Install-WindowsFeature](https://technet.microsoft.com/itpro/powershell/windows/servermanager/install-windowsfeature)를 사용하여 IIS 웹 서버를 설치하는 사용자 지정 스크립트 확장을 실행합니다.

```powershell
Install-WindowsFeature -name Web-Server -IncludeManagementTools
```

이제 공용 IP 주소를 통해 VM으로 이동하여 IIS 사이트를 볼 수 있습니다.

![IIS 기본 사이트](./media/tutorial-virtual-network/iis.png)

## <a name="manage-internal-traffic"></a>인터넷 트래픽 관리

NSG(네트워크 보안 그룹)에는 VNet에 연결된 리소스에 대한 네트워크 트래픽을 허용하거나 거부하는 보안 규칙 목록이 포함되어 있습니다. NSG는 서브넷 또는 VM에 연결된 개별 NIC에 연결될 수 있습니다. 포트를 통한 VM에 대한 액세스 열기 또는 닫기는 NSG 규칙을 사용하여 수행됩니다. *myFrontendVM*을 만들 때 RDP 연결에 대해 인바운드 포트 3389가 자동으로 열렸습니다.

VM의 내부 통신은 NSG를 사용하여 구성할 수 있습니다. 이 섹션에서는 네트워크에서 추가 서브넷을 만들고 NSG를 서브넷에 할당하여 포트 1433의 *myFrontendVM*에서 *myBackendVM*으로 연결할 수 있도록 하는 방법을 알아봅니다. 그러면 서브넷이 만들어질 때 VM에 할당됩니다.

백 엔드 서브넷에 대한 NSG를 만들어 *myFrontendVM*에서만 *myBackendVM*으로 내부 트래픽을 보내도록 제한할 수 있습니다. 다음 예제에서는 [New-AzureRmNetworkSecurityRuleConfig](/powershell/module/azurerm.network/new-azurermnetworksecurityruleconfig)를 사용하여 *myBackendNSGRule*이라는 NSG 규칙을 만듭니다.

```powershell
$nsgBackendRule = New-AzureRmNetworkSecurityRuleConfig `
  -Name myBackendNSGRule `
  -Protocol Tcp `
  -Direction Inbound `
  -Priority 100 `
  -SourceAddressPrefix 10.0.0.0/24 `
  -SourcePortRange * `
  -DestinationAddressPrefix * `
  -DestinationPortRange 1433 `
  -Access Allow
```

[New-AzureRmNetworkSecurityGroup](/powershell/module/azurerm.network/new-azurermnetworksecuritygroup)을 사용하여 *myBackendNSG*라는 네트워크 보안 그룹을 추가합니다.

```powershell
$nsgBackend = New-AzureRmNetworkSecurityGroup `
  -ResourceGroupName myRGNetwork `
  -Location EastUS `
  -Name myBackendNSG `
  -SecurityRules $nsgBackendRule
```
## <a name="add-back-end-subnet"></a>백 엔드 서브넷 추가

[Add-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/add-azurermvirtualnetworksubnetconfig)를 사용하여 *myBackEndSubnet*을 *myVNet*에 추가합니다.

```powershell
Add-AzureRmVirtualNetworkSubnetConfig `
  -Name myBackendSubnet `
  -VirtualNetwork $vnet `
  -AddressPrefix 10.0.1.0/24 `
  -NetworkSecurityGroup $nsgBackend
Set-AzureRmVirtualNetwork -VirtualNetwork $vnet
$vnet = Get-AzureRmVirtualNetwork `
  -ResourceGroupName myRGNetwork `
  -Name myVNet
```

## <a name="create-back-end-vm"></a>백 엔드 VM 만들기

백 엔드 VM을 만드는 가장 쉬운 방법은 SQL Server 이미지를 사용하는 것입니다. 이 자습서에서는 데이터베이스 서버를 사용하여 VM을 만들기만 하며 데이터베이스에 액세스하는 방법에 대한 정보는 제공하지 않습니다.

*myBackendNic*을 만듭니다.

```powershell
$backendNic = New-AzureRmNetworkInterface `
  -ResourceGroupName myRGNetwork `
  -Location EastUS `
  -Name myBackendNic `
  -SubnetId $vnet.Subnets[1].Id
```

Get-Credential을 사용하여 가상 컴퓨터의 관리자 계정에 필요한 사용자 이름 및 암호를 설정합니다.

```powershell
$cred = Get-Credential
```

*myBackendVM*을 만듭니다.

```powershell
$backendVM = New-AzureRmVMConfig `
  -VMName myBackendVM `
  -VMSize Standard_D1
$backendVM = Set-AzureRmVMOperatingSystem `
  -VM $backendVM `
  -Windows `
  -ComputerName myBackendVM `
  -Credential $cred `
  -ProvisionVMAgent `
  -EnableAutoUpdate
$backendVM = Set-AzureRmVMSourceImage `
  -VM $backendVM `
  -PublisherName MicrosoftSQLServer `
  -Offer SQL2016-WS2016 `
  -Skus Enterprise `
  -Version latest
$backendVM = Set-AzureRmVMOSDisk `
  -VM $backendVM `
  -Name myBackendOSDisk `
  -DiskSizeInGB 128 `
  -CreateOption FromImage `
  -Caching ReadWrite
$backendVM = Add-AzureRmVMNetworkInterface `
  -VM $backendVM `
  -Id $backendNic.Id
New-AzureRmVM `
  -ResourceGroupName myRGNetwork `
  -Location EastUS `
  -VM $backendVM
```

사용되는 이미지에는 SQL Server가 설치되어 있지만 이 자습서에서는 사용하지 않습니다. 여기서는 웹 트래픽을 처리하도록 VM을 구성하는 방법 및 데이터베이스 관리를 처리하도록 VM을 구성하는 방법을 보여 주기 위해 포함되었습니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 가상 컴퓨터와 관련된 Azure Networks를 만들고 보호했습니다. 

> [!div class="checklist"]
> * 가상 네트워크 만들기
> * 가상 네트워크 서브넷 만들기
> * 네트워크 보안 그룹을 사용하여 네트워크 트래픽 제어
> * 트래픽 규칙의 실제 동작 보기

다음 자습서로 이동하여 Azure Backup을 사용하는 가상 컴퓨터의 데이터 보안 모니터링에 대해 알아보세요. 을 참조하세요.

> [!div class="nextstepaction"]
> [Azure에서 Windows 가상 컴퓨터 백업](./tutorial-backup-vms.md)

