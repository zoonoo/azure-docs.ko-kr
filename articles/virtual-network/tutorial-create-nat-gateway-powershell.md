---
title: '자습서: NAT 게이트웨이 만들기 - PowerShell'
titlesuffix: Azure Virtual Network NAT
description: Azure PowerShell을 사용하여 NAT 게이트웨이를 만듭니다.
author: asudbring
ms.author: allensu
ms.service: virtual-network
ms.subservice: nat
ms.topic: tutorial
ms.date: 03/09/2021
ms.custom: template-tutorial
ms.openlocfilehash: 884697cee84c05916fe19fb8f9435de86bda291e
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/10/2021
ms.locfileid: "102619886"
---
# <a name="tutorial-create-a-nat-gateway-using-azure-powershell"></a>자습서: Azure PowerShell을 사용하여 NAT 게이트웨이 만들기

이 자습서에서는 Azure Virtual Network NAT 서비스를 사용하는 방법을 보여줍니다. Azure에서 가상 머신에 대한 아웃바운드 연결을 제공하는 NAT 게이트웨이를 만듭니다. 

이 자습서에서는 다음 작업 방법을 알아봅니다.

> [!div class="checklist"]
> * 가상 네트워크를 만듭니다.
> * 가상 머신을 만듭니다.
> * NAT 게이트웨이를 만들고 가상 네트워크와 연결합니다.
> * 가상 머신에 연결하고 NAT IP 주소를 확인합니다.

## <a name="prerequisites"></a>사전 요구 사항

- 활성 구독이 있는 Azure 계정. [체험 계정을 만듭니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- 로컬로 설치된 Azure PowerShell 또는 Azure Cloud Shell

PowerShell을 로컬로 설치하고 사용하도록 선택하는 경우 이 문서에는 Azure PowerShell 모듈 버전 5.4.1 이상이 필요합니다. 설치되어 있는 버전을 확인하려면 `Get-Module -ListAvailable Az`을 실행합니다. 업그레이드해야 하는 경우 [Azure PowerShell 모듈 설치](/powershell/azure/install-Az-ps)를 참조하세요. 또한 PowerShell을 로컬로 실행하는 경우 `Connect-AzAccount`를 실행하여 Azure와 연결해야 합니다.

## <a name="create-a-resource-group"></a>리소스 그룹 만들기

[New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup)을 사용하여 리소스 그룹을 만듭니다. Azure 리소스 그룹은 Azure 리소스가 배포 및 관리되는 논리적 컨테이너입니다.

다음 예제에서는 **myResourceGroupNAT** 라는 리소스 그룹을 **eastus2** 위치에 만듭니다.

```azurepowershell-interactive
$rsg = @{
    Name = 'myResourceGroupNAT'
    Location = 'eastus2'
}
New-AzResourceGroup @rsg
```
## <a name="create-the-nat-gateway"></a>NAT 게이트웨이 만들기

이 섹션에서는 NAT 게이트웨이와 지원 리소스를 만듭니다.

* 인터넷에 액세스 하려면 NAT 게이트웨이에 대 한 공용 IP 주소가 하나 이상 필요 합니다. [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress)를 사용하여 **myPublicIP** 라는 공용 IP 주소 리소스를 **myResourceGroupNAT** 에 만듭니다. 

* [New-AzNatGateway](/powershell/module/az.network/new-aznatgateway)를 사용하여 글로벌 Azure NAT 게이트웨이를 만듭니다. 이 명령의 결과는 공용 IP 주소 **myPublicIP** 를 사용하는 **myNATgateway** 라는 게이트웨이 리소스를 만듭니다. 유휴 시간 제한은 10분으로 설정됩니다.  

* [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork)를 사용하여 **myResourceGroup** 에서 [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig)를 사용하여 **mySubnet** 이라는 서브넷이 있는 **myVnet** 이라는 가상 네트워크를 만듭니다. 가상 네트워크의 IP 주소 공간은 **10.1.0.0/16** 입니다. 가상 네트워크 내의 서브넷은 **10.1.0.0/24** 입니다.

* 가상 머신에 액세스하려면 **myBastionHost** 라는 Azure Bastion 호스트를 만듭니다. [New-AzBastion](/powershell/module/az.network/new-azbastion)을 사용하여 베스천 호스트를 만듭니다. [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress)를 사용하여 베스천 호스트에 대한 공용 IP 주소를 만듭니다.

```azurepowershell-interactive
## Create public IP address for NAT gateway ##
$ip = @{
    Name = 'myPublicIP'
    ResourceGroupName = 'myResourceGroupNAT'
    Location = 'eastus2'
    Sku = 'Standard'
    AllocationMethod = 'Static'
}
$publicIP = New-AzPublicIpAddress @ip

## Create NAT gateway resource ##
$nat = @{
    ResourceGroupName = 'myResourceGroupNAT'
    Name = 'myNATgateway'
    IdleTimeoutInMinutes = '10'
    Sku = 'Standard'
    Location = 'eastus2'
    PublicIpAddress = $publicIP
}
$natGateway = New-AzNatGateway @nat

## Create subnet config and associate NAT gateway to subnet##
$subnet = @{
    Name = 'mySubnet'
    AddressPrefix = '10.1.0.0/24'
    NatGateway = $natGateway
}
$subnetConfig = New-AzVirtualNetworkSubnetConfig @subnet 

## Create Azure Bastion subnet. ##
$bastsubnet = @{
    Name = 'AzureBastionSubnet' 
    AddressPrefix = '10.1.1.0/24'
}
$bastsubnetConfig = New-AzVirtualNetworkSubnetConfig @bastsubnet

## Create the virtual network ##
$net = @{
    Name = 'myVNet'
    ResourceGroupName = 'myResourceGroupNAT'
    Location = 'eastus2'
    AddressPrefix = '10.1.0.0/16'
    Subnet = $subnetConfig,$bastsubnetConfig
}
$vnet = New-AzVirtualNetwork @net

## Create public IP address for bastion host. ##
$ip = @{
    Name = 'myBastionIP'
    ResourceGroupName = 'myResourceGroupNAT'
    Location = 'eastus2'
    Sku = 'Standard'
    AllocationMethod = 'Static'
}
$publicip = New-AzPublicIpAddress @ip

## Create bastion host ##
$bastion = @{
    ResourceGroupName = 'myResourceGroupNAT'
    Name = 'myBastion'
    PublicIpAddress = $publicip
    VirtualNetwork = $vnet
}
New-AzBastion @bastion -AsJob

```

## <a name="virtual-machine"></a>가상 머신

이 섹션에서는 NAT 게이트웨이를 테스트하고 아웃바운드 연결의 공용 IP 주소를 확인하는 가상 머신을 만듭니다.

* [New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface)를 사용하여 네트워크 인터페이스를 만듭니다.

* [Get-Credential](/powershell/module/microsoft.powershell.security/get-credential)을 사용하여 VM에 대한 관리자 사용자 이름과 암호를 설정합니다.

* 다음을 사용하여 가상 머신을 만듭니다.
    * [New-AzVM](/powershell/module/az.compute/new-azvm)
    * [New-AzVMConfig](/powershell/module/az.compute/new-azvmconfig)
    * [Set-AzVMOperatingSystem](/powershell/module/az.compute/set-azvmoperatingsystem)
    * [Set-AzVMSourceImage](/powershell/module/az.compute/set-azvmsourceimage)
    * [Add-AzVMNetworkInterface](/powershell/module/az.compute/add-azvmnetworkinterface)
    
```azurepowershell-interactive
# Set the administrator and password for the VMs. ##
$cred = Get-Credential

## Place the virtual network into a variable. ##
$vnet = Get-AzVirtualNetwork -Name 'myVNet' -ResourceGroupName 'myResourceGroupNAT'

## Create network interface for virtual machine. ##
$nic = @{
    Name = "myNicVM"
    ResourceGroupName = 'myResourceGroupNAT'
    Location = 'eastus2'
    Subnet = $vnet.Subnets[0]
}
$nicVM = New-AzNetworkInterface @nic

## Create a virtual machine configuration for VMs ##
$vmsz = @{
    VMName = "myVM"
    VMSize = 'Standard_DS1_v2'  
}
$vmos = @{
    ComputerName = "myVM"
    Credential = $cred
}
$vmimage = @{
    PublisherName = 'MicrosoftWindowsServer'
    Offer = 'WindowsServer'
    Skus = '2019-Datacenter'
    Version = 'latest'    
}
$vmConfig = New-AzVMConfig @vmsz `
    | Set-AzVMOperatingSystem @vmos -Windows `
    | Set-AzVMSourceImage @vmimage `
    | Add-AzVMNetworkInterface -Id $nicVM.Id

## Create the virtual machine for VMs ##
$vm = @{
    ResourceGroupName = 'myResourceGroupNAT'
    Location = 'eastus2'
    VM = $vmConfig
}
New-AzVM @vm

```

다음 섹션으로 이동하기 전에 가상 머신 만들기가 완료될 때까지 기다립니다.

## <a name="test-nat-gateway"></a>NAT 게이트웨이 테스트

이 섹션에서는 NAT 게이트웨이를 테스트합니다. 먼저 NAT 게이트웨이의 공용 IP를 검색합니다. 그런 다음, 테스트 가상 머신에 연결하고 NAT 게이트웨이를 통해 아웃바운드 연결을 확인합니다.
    
1. [Azure 포털](https://portal.azure.com)

1. **개요** 화면에서 NAT 게이트웨이에 대한 공용 IP 주소를 찾습니다. 왼쪽 메뉴에서 **모든 서비스** 를 선택하고 **모든 리소스** 를 선택한 다음, **myPublicIP** 를 선택합니다.

2. 공용 IP 주소를 기록해 둡니다.

    :::image type="content" source="./media/tutorial-create-nat-gateway-portal/find-public-ip.png" alt-text="NAT 게이트웨이의 공용 IP 주소 검색" border="true":::

3. 왼쪽 메뉴에서 **모든 서비스** 를 선택하고 **모든 리소스** 를 선택한 다음, 리소스 목록에서 **myResourceGroupNAT** 리소스 그룹에 있는 **myVM** 을 선택합니다.

4. **개요** 페이지에서 **연결** 을 선택한 다음, **Bastion** 을 선택합니다.

5. 파란색 **Bastion 사용** 단추를 선택합니다.

6. VM을 만드는 동안 입력한 사용자 이름과 암호를 입력합니다.

7. **myTestVM** 에서 **Internet Explorer** 를 엽니다.

8. 주소 표시줄에 **https://whatsmyip.com** 을 입력합니다.

9. 표시된 IP 주소가 이전 단계에서 적어둔 NAT 게이트웨이 주소와 일치하는지 확인합니다.

    :::image type="content" source="./media/tutorial-create-nat-gateway-portal/my-ip.png" alt-text="외부 아웃바운드 IP를 표시하는 Internet Explorer" border="true":::

## <a name="clean-up-resources"></a>리소스 정리

이 애플리케이션을 계속 사용하지 않으려면 다음 단계에 따라 가상 네트워크, 가상 머신 및 NAT 게이트웨이를 삭제합니다.

```azurepowershell-interactive
Remove-AzResourceGroup -Name 'myResourceGroupNAT' -Force
```

## <a name="next-steps"></a>다음 단계

Azure Virtual Network NAT에 대한 자세한 내용은 다음을 참조하세요.
> [!div class="nextstepaction"]
> [Virtual Network NAT 개요](nat-overview.md)
