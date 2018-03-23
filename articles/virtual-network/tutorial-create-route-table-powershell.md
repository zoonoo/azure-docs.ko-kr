---
title: 네트워크 트래픽 라우팅 - PowerShell | Microsoft Docs
description: PowerShell을 사용하여 경로 테이블이 포함된 네트워크 트래픽을 라우팅하는 방법을 알아봅니다.
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: ''
ms.topic: article
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 03/05/2018
ms.author: jdial
ms.custom: ''
ms.openlocfilehash: f91b143c75a82aa6760796770b3ae4d0e4ec53dd
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/08/2018
---
# <a name="route-network-traffic-with-a-route-table-using-powershell"></a>PowerShell을 사용하여 경로 테이블이 포함된 네트워크 트래픽 라우팅

기본적으로 Azure에서는 가상 네트워크 내의 모든 서브넷 간에 트래픽을 자동으로 라우팅합니다. 고유의 라우팅을 만들어 Azure의 기본 라우팅을 재정의할 수 있습니다. 사용자 지정 경로를 만드는 기능은, 예를 들어 방화벽을 통해 서브넷 간 트래픽을 라우팅하려는 경우에 유용합니다. 이 문서에서는 다음 방법을 알아봅니다.

> [!div class="checklist"]
> * 경로 테이블 만들기
> * 경로 만들기
> * 가상 네트워크 서브넷에 경로 테이블 연결
> * 테스트 라우팅
> * 라우팅 문제 해결

Azure 구독이 아직 없는 경우 시작하기 전에 [무료 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

PowerShell을 로컬로 설치하고 사용하도록 선택하는 경우 이 문서에는 Azure PowerShell 모듈 버전 5.4.1 이상이 필요합니다. 설치되어 있는 버전을 확인하려면 `Get-Module -ListAvailable AzureRM`을 실행합니다. 업그레이드해야 하는 경우 [Azure PowerShell 모듈 설치](/powershell/azure/install-azurerm-ps)를 참조하세요. 또한 PowerShell을 로컬로 실행하는 경우 `Login-AzureRmAccount`를 실행하여 Azure와 연결해야 합니다. 

## <a name="create-a-route-table"></a>경로 테이블 만들기

기본적으로 Azure에서는 가상 네트워크 내의 모든 서브넷 간에 트래픽을 라우팅합니다. Azure의 기본 경로를 자세히 알아보려면 [시스템 경로](virtual-networks-udr-overview.md)를 참조하세요. Azure의 기본 라우팅을 재정의하려면 경로를 포함하는 경로 테이블을 만들고 가상 네트워크 서브넷에 경로 테이블을 연결합니다.

경로 테이블을 만들려면 먼저 [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup)을 사용하여 리소스 그룹을 만듭니다. 다음 예제에서는 이 문서에서 만든 모든 리소스에 대해 *myResourceGroup*이라는 리소스 그룹을 만듭니다. 

```azurepowershell-interactive
New-AzureRmResourceGroup -ResourceGroupName myResourceGroup -Location EastUS
```

[New-AzureRmRouteTable](/powershell/module/azurerm.network/new-azurermroutetable)을 사용하여 경로 테이블을 만듭니다. 다음 예제에서는 *myRouteTablePublic*이라는 경로 테이블을 만듭니다.

```azurepowershell-interactive
$routeTablePublic = New-AzureRmRouteTable `
  -Name 'myRouteTablePublic' `
  -ResourceGroupName myResourceGroup `
  -location EastUS
```

## <a name="create-a-route"></a>경로 만들기

경로 테이블에는 0개 또는 여러 개의 경로가 포함됩니다. [Get-AzureRmRouteTable](/powershell/module/azurerm.network/get-azurermroutetable)을 사용하여 경로 테이블 개체를 검색하여 경로를 만들고, [Add-AzureRmRouteConfig](/powershell/module/azurerm.network/add-azurermrouteconfig)를 사용하여 경로를 만든 다음, [Set-AzureRmRouteTable](/powershell/module/azurerm.network/set-azurermroutetable)을 사용하여 경로 테이블에 경로 구성을 작성합니다. 

```azurepowershell-interactive
Get-AzureRmRouteTable `
  -ResourceGroupName "myResourceGroup" `
  -Name "myRouteTablePublic" `
  | Add-AzureRmRouteConfig `
  -Name "ToPrivateSubnet" `
  -AddressPrefix 10.0.1.0/24 `
  -NextHopType "VirtualAppliance" `
  -NextHopIpAddress 10.0.2.4 `
 | Set-AzureRmRouteTable
```

경로는 10.0.2.4 IP 주소를 사용하는 네트워크 가상 어플라이언스를 통해 10.0.1.0/24 주소 접두사를 향하는 모든 트래픽을 보냅니다. 지정된 주소 접두사가 있는 네트워크 가상 어플라이언스 및 서브넷은 이후 단계에서 만들어집니다. 경로는 직접 서브넷 간 트래픽을 라우팅하는 Azure의 기본 라우팅을 재정의합니다. 각 경로는 다음 홉 형식을 지정합니다. 다음 홉 형식은 Azure가 트래픽을 라우팅하는 방법을 지정합니다. 이 예제에서 다음 홉 형식은 *VirtualAppliance*입니다. Azure에서 사용 가능한 모든 다음 홉 형식 및 사용 시기에 대해 자세히 알아보려면 [다음 홉 형식](virtual-networks-udr-overview.md#custom-routes)을 참조하세요.

## <a name="associate-a-route-table-to-a-subnet"></a>서브넷에 경로 테이블 연결

서브넷에 경로 테이블을 연결하려면 먼저 가상 네트워크 및 서브넷을 만들어야 합니다. [New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork)를 사용하여 가상 네트워크를 만듭니다. 다음 예제는 주소 접두사 *10.0.0.0/16*을 사용하는 *myVirtualNetwork*라는 가상 네트워크를 만듭니다.

```azurepowershell-interactive
$virtualNetwork = New-AzureRmVirtualNetwork `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -Name myVirtualNetwork `
  -AddressPrefix 10.0.0.0/16
```

[New-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig)를 사용하여 세 개의 서브넷 구성을 만들어 세 개의 서브넷을 만듭니다. 다음 예제에서는 *공용*, *개인* 및 *DMZ* 서브넷에 대한 세 개의 서브넷 구성을 만듭니다.

```azurepowershell-interactive
$subnetConfigPublic = Add-AzureRmVirtualNetworkSubnetConfig `
  -Name Public `
  -AddressPrefix 10.0.0.0/24 `
  -VirtualNetwork $virtualNetwork

$subnetConfigPrivate = Add-AzureRmVirtualNetworkSubnetConfig `
  -Name Private `
  -AddressPrefix 10.0.1.0/24 `
  -VirtualNetwork $virtualNetwork

$subnetConfigDmz = Add-AzureRmVirtualNetworkSubnetConfig `
  -Name DMZ `
  -AddressPrefix 10.0.2.0/24 `
  -VirtualNetwork $virtualNetwork
```

주소 접두사는 가상 네트워크에 대해 정의된 주소 접두사 내에 있어야 합니다. 서브넷 주소 접두사는 서로 겹칠 수 없습니다.

[Set-AzureRmVirtualNetwork](/powershell/module/azurerm.network/Set-AzureRmVirtualNetwork)를 사용하여 가상 네트워크에 서브넷 구성을 씁니다. 그러면 가상 네트워크에 서브넷이 만들어집니다.

```azurepowershell-interactive
$virtualNetwork | Set-AzureRmVirtualNetwork
```

경로 테이블을 0개 또는 여러 개의 서브넷에 연결할 수 있습니다. 서브넷에는 0개 또는 1개의 경로 테이블이 연결될 수 있습니다. 서브넷의 아웃바운드 트래픽은 Azure의 기본 경로 및 서브넷에 연결한 경로 테이블에 추가한 모든 사용자 지정 경로에 따라 라우팅됩니다. [Set-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/set-azurermvirtualnetworksubnetconfig)를 사용하여 *myRouteTablePublic* 경로 테이블을 *공용* 서브넷에 연결한 다음, [Set-AzureRmVirtualNetwork](/powershell/module/azurerm.network/set-azurermvirtualnetwork)를 사용하여 가상 네트워크에 대한 서브넷 구성을 작성합니다.

```azurepowershell-interactive
Set-AzureRmVirtualNetworkSubnetConfig `
  -VirtualNetwork $virtualNetwork `
  -Name 'Public' `
  -AddressPrefix 10.0.0.0/24 `
  -RouteTable $routeTablePublic | `
Set-AzureRmVirtualNetwork
```

프로덕션 사용을 위해 경로 테이블을 배포하기 전에 [Azure에서 라우팅](virtual-networks-udr-overview.md) 및 [Azure 제한](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)에 대해 자세히 알아두는 것이 좋습니다.

## <a name="test-routing"></a>테스트 라우팅

라우팅을 테스트하려면 이전 단계에서 만든 경로를 통해 라우팅하는 네트워크 가상 어플라이언스로 사용되는 가상 머신을 만들어야 합니다. 네트워크 가상 어플라이언스를 만든 후에 가상 머신을 *공용* 및 *개인* 서브넷에 배포합니다. 그런 다음, *공용* 서브넷의 트래픽을 네트워크 가상 어플라이언스를 통해 *개인* 서브넷에 라우팅하게 됩니다.

### <a name="create-a-network-virtual-appliance"></a>네트워크 가상 어플라이언스 만들기

네트워크 응용 프로그램을 실행하는 가상 머신을 종종 네트워크 가상 어플라이언스라고 합니다. 네트워크 가상 어플라이언스는 일반적으로 네트워크 트래픽을 수신하고, 일부 작업을 수행한 다음, 네트워크 응용 프로그램에 구성된 논리에 따라 네트워크 트래픽을 전달하거나 삭제합니다. 

#### <a name="create-a-network-interface"></a>네트워크 인터페이스 만들기

이전 단계에서 다음 홉 형식으로 네트워크 가상 어플라이언스를 지정하는 경로를 만들었습니다. 네트워크 응용 프로그램을 실행하는 가상 머신을 종종 네트워크 가상 어플라이언스라고 합니다. 프로덕션 환경에서 배포한 네트워크 가상 어플라이언스는 종종 미리 구성된 가상 머신입니다. 몇 가지 네트워크 가상 어플라이언스는 [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking?search=network%20virtual%20appliance&page=1)에서 확인할 수 있습니다. 이 문서에서는 기본 가상 머신이 생성됩니다.

가상 머신에는 하나 이상의 네트워크 인터페이스가 연결되어 있어 가상 머신이 네트워크와 통신할 수 있도록 합니다. 네트워크 인터페이스가 자체 IP 주소를 향하지 않고 수신된 네트워크 트래픽을 전달할 수 있으려면 네트워크 인터페이스에 대해 IP 전달을 사용 가능으로 설정해야 합니다. 네트워크 인터페이스를 만들기 전에 [Get-AzureRmVirtualNetwork](/powershell/module/azurerm.network/get-azurermvirtualnetwork)를 사용하여 가상 네트워크 ID를 검색한 다음, [Get-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/get-azurermvirtualnetworksubnetconfig)를 사용하여 서브넷 ID를 검색해야 합니다. IP 전달이 사용 가능하도록 설정된 *DMZ* 서브넷에서 [New-AzureRmNetworkInterface](/powershell/module/azurerm.network/new-azurermnetworkinterface)를 사용하여 네트워크 인터페이스를 만듭니다.

```azurepowershell-interactive
# Retrieve the virtual network object into a variable.
$virtualNetwork=Get-AzureRmVirtualNetwork `
  -Name myVirtualNetwork `
  -ResourceGroupName myResourceGroup

# Retrieve the subnet configuration into a variable.
$subnetConfigDmz = Get-AzureRmVirtualNetworkSubnetConfig `
  -Name DMZ `
  -VirtualNetwork $virtualNetwork

# Create the network interface.
$nic = New-AzureRmNetworkInterface `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -Name 'myVmNva' `
  -SubnetId $subnetConfigDmz.Id `
  -EnableIPForwarding
```

#### <a name="create-a-virtual-machine"></a>가상 머신 만들기

가상 머신을 만들고 여기에 기존 네트워크 인터페이스를 연결하려면 먼저 [New-AzureRmVMConfig](/powershell/module/azurerm.compute/new-azurermvmconfig)를 사용하여 가상 머신 구성을 만들어야 합니다. 구성에는 이전 단계에서 만든 네트워크 인터페이스가 포함됩니다. 사용자 이름 및 암호에 대한 메시지가 나타나면 가상 머신에 로그인할 사용자 이름과 암호를 선택합니다. 

```azurepowershell-interactive
# Create a credential object.
$cred = Get-Credential -Message "Enter a username and password for the virtual machine."

# Create a virtual machine configuration.
$vmConfig = New-AzureRmVMConfig `
  -VMName 'myVmNva' `
  -VMSize Standard_DS2 | `
  Set-AzureRmVMOperatingSystem -Windows `
    -ComputerName 'myVmNva' `
    -Credential $cred | `
  Set-AzureRmVMSourceImage `
    -PublisherName MicrosoftWindowsServer `
    -Offer WindowsServer `
    -Skus 2016-Datacenter `
    -Version latest | `
  Add-AzureRmVMNetworkInterface -Id $nic.Id
```

[New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm)으로 가상 머신 구성을 사용한 가상 머신을 만듭니다. 다음 예제에서는 *myVmNva*이라는 가상 머신을 만듭니다. 

```azurepowershell-interactive
$vmNva = New-AzureRmVM `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -VM $vmConfig `
  -AsJob
```

`-AsJob` 옵션은 백그라운드에서 가상 머신을 만들기 때문에 다음 단계를 계속 진행할 수 있습니다. 메시지가 나타나면 가상 머신에 로그인할 사용자 이름과 암호를 입력합니다. 프로덕션 환경에서 배포한 네트워크 가상 어플라이언스는 종종 미리 구성된 가상 머신입니다. 몇 가지 네트워크 가상 어플라이언스는 [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking?search=network%20virtual%20appliance&page=1)에서 확인할 수 있습니다.

Azure는 10.0.2.4를 가상 머신의 개인 IP 주소로 할당했습니다. *myVirtualNetwork*의 *DMZ* 서브넷에서 사용 가능한 첫 번째 IP 주소가 10.0.2.4이기 때문입니다.

### <a name="create-virtual-machines"></a>가상 머신 만들기

이후 단계에서 *공용* 서브넷의 해당 트래픽이 네트워크 가상 어플라이언스를 통해 *개인* 서브넷에 라우팅되는지 유효성을 검사할 수 있도록 가상 네트워크에 두 개의 가상 머신을 만듭니다. 

[New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm)를 사용하여 *공용* 서브넷에 가상 머신을 만듭니다. 다음 예제는 *myVirtualNetwork* 가상 네트워크의 *Public* 서브넷에 *myVmWeb*이라는 가상 머신을 만듭니다. 

```azurepowershell-interactive
New-AzureRmVm `
  -ResourceGroupName "myResourceGroup" `
  -Location "East US" `
  -VirtualNetworkName "myVirtualNetwork" `
  -SubnetName "Public" `
  -ImageName "Win2016Datacenter" `
  -Name "myVmWeb" `
  -AsJob
```

Azure는 10.0.0.4를 가상 머신의 개인 IP 주소로 할당했습니다. *myVirtualNetwork*의 *공용* 서브넷에서 사용 가능한 첫 번째 IP 주소가 10.0.0.4이기 때문입니다.

*Private* 서브넷에 가상 머신을 만듭니다.

```azurepowershell-interactive
New-AzureRmVm `
  -ResourceGroupName "myResourceGroup" `
  -Location "East US" `
  -VirtualNetworkName "myVirtualNetwork" `
  -SubnetName "Private" `
  -ImageName "Win2016Datacenter" `
  -Name "myVmMgmt"
```

가상 머신을 만드는 데 몇 분 정도 걸립니다. Azure는 10.0.1.4를 가상 머신의 개인 IP 주소로 할당했습니다. *myVirtualNetwork*의 *개인* 서브넷에서 사용 가능한 첫 번째 IP 주소가 10.0.1.4이기 때문입니다. 

가상 머신이 만들어지고 Azure에서 PowerShell로 출력을 반환할 때까지 나머지 단계를 진행하지 마십시오.

### <a name="route-traffic-through-a-network-virtual-appliance"></a>네트워크 가상 어플라이언스를 통한 트래픽 라우팅

[Set-AzureRmVMExtension](/powershell/module/azurerm.compute/set-azurermvmextension)을 사용하여 이후 단계에서 가상 머신 간 통신을 테스트하는 데 tracert를 사용하도록 Windows 방화벽을 통해 *myVmWeb* 및 *myVmMgmt* 가상 머신에 대한 ICMP 인바운드를 활성화합니다.

```powershell-interactive
Set-AzureRmVMExtension `
  -ResourceGroupName myResourceGroup `
  -VMName myVmWeb `
  -ExtensionName AllowICMP `
  -Publisher Microsoft.Compute `
  -ExtensionType CustomScriptExtension `
  -TypeHandlerVersion 1.8 `
  -SettingString '{"commandToExecute": "netsh advfirewall firewall add rule name=Allow-ping protocol=icmpv4 dir=in action=allow"}' `
  -Location EastUS

Set-AzureRmVMExtension `
  -ResourceGroupName myResourceGroup `
  -VMName myVmMgmt `
  -ExtensionName AllowICMP `
  -Publisher Microsoft.Compute `
  -ExtensionType CustomScriptExtension `
  -TypeHandlerVersion 1.8 `
  -SettingString '{"commandToExecute": "netsh advfirewall firewall add rule name=Allow-ping protocol=icmpv4 dir=in action=allow"}' `
  -Location EastUS
```

이전 명령을 완료하려면 몇 분 정도 걸릴 수 있습니다. 명령이 완료되고 출력이 PowerShell에 반환될 때까지 다음 단계를 진행하지 마십시오. 이 문서에서는 라우팅을 테스트하는 데 tracert가 사용되지만, 프로덕션 배포에 Windows 방화벽을 통한 ICMP는 허용하지 않는 것이 좋습니다.

인터넷에서 가상 머신의 공용 IP 주소에 연결합니다. [Get-AzureRmPublicIpAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress)를 사용하여 가상 머신의 공용 IP 주소를 반환합니다. 다음 예제에서는 *myVmMgmt* 가상 머신의 공용 IP 주소를 반환합니다.

```azurepowershell-interactive
Get-AzureRmPublicIpAddress `
  -Name myVmMgmt `
  -ResourceGroupName myResourceGroup `
  | Select IpAddress
```

다음 명령을 사용하여 로컬 컴퓨터에서 *myVmMgmt* 가상 머신과의 원격 데스크톱 세션을 만듭니다. `<publicIpAddress>`를 이전 명령에서 반환된 IP 주소로 바꿉니다.

```
mstsc /v:<publicIpAddress>
```

원격 데스크톱 프로토콜(.rdp) 파일이 마들어지고 컴퓨터에 다운로드된 후 열립니다. 가상 머신을 만들 때 지정한 사용자 이름과 암호를 입력한 다음, **확인**을 선택합니다. (가상 머신을 만들 때 입력한 자격 증명을 지정하기 위해 **다른 옵션 선택**을 선택한 다음, **다른 계정 사용**을 선택해야 할 수도 있습니다.) 로그인 프로세스 중에 인증서 경고가 나타날 수 있습니다. **예** 또는 **계속**을 클릭하여 연결을 진행합니다. 

[IP 전달을 사용하도록 설정](#enable-ip-forwarding)에서 가상 머신의 네트워크 인터페이스에 대해 Azure 내에서 IP 전달을 사용하도록 설정했습니다. 가상 머신 내 운영 체제 또는 가상 머신 내에서 실행 중인 응용 프로그램도 네트워크 트래픽을 전달할 수 있어야 합니다. 프로덕션 환경에서 네트워크 가상 어플라이언스를 배포할 때 어플라이언스는 일반적으로 트래픽을 전달하기 전에 몇 가지 다른 기능을 필터링, 로그 또는 수행합니다. 하지만 이 문서에서는 운영 체제가 수신한 모든 트래픽을 전달하기만 합니다. *myVmNva*의 운영 체제 내에서 IP 전달을 사용하도록 설정합니다.

*myVmMgmt* 가상 머신에 있는 명령 프롬프트에서 *myVmNva* 가상 머신에 대한 원격 데스크톱:

``` 
mstsc /v:myVmNva
```
    
*myVmNva* 가상 머신의 운영 체제 내에서 IP 전달을 사용할 수 있도록 하려면 *myVmNva* 가상 머신의 PowerShell에서 다음 명령을 입력합니다.

```powershell
Set-ItemProperty -Path HKLM:\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters -Name IpEnableRouter -Value 1
```
    
*myVmNva* 가상 머신을 다시 시작합니다. 그러면 원격 데스크톱 세션의 연결도 끊어지고, *myVmMgmt* 가상 머신에 대해 열었던 원격 데스크톱 세션 내에 남아 있는 상태가 됩니다.

*myVmNva* 가상 머신을 다시 시작한 후, *myVmMgmt* 가상 머신에서 *myVmWeb* 가상 머신에 대한 네트워크 트래픽의 라우팅을 테스트하려면 다음 명령을 사용합니다.

```
tracert myvmweb
```

응답은 다음 예제와 유사합니다.

```
Tracing route to myvmweb.vpgub4nqnocezhjgurw44dnxrc.bx.internal.cloudapp.net [10.0.0.4]
over a maximum of 30 hops:
    
1     1 ms     1 ms     1 ms  10.0.0.4
  
Trace complete.
```

트래픽이 *myVmMgmt* 가상 머신에서 *myVmWeb* 가상 머신으로 직접 라우팅되는지 볼 수 있습니다. Azure의 기본 경로는 트래픽을 서브넷 간에 직접 라우팅합니다.

*myVmMgmt* 가상 머신에서 *myVmWeb* 가상 머신에 원격 데스크톱을 설정하려면 다음 명령을 사용합니다.

``` 
mstsc /v:myVmWeb
```

*myVmWeb* 가상 머신에서 *myVmMgmt* 가상 머신에 대한 네트워크 트래픽의 라우팅을 테스트하려면 명령 프롬프트에 다음 명령을 입력합니다.

```
tracert myvmmgmt
```

응답은 다음 예제와 유사합니다.

```
Tracing route to myvmmgmt.vpgub4nqnocezhjgurw44dnxrc.bx.internal.cloudapp.net [10.0.1.4]
over a maximum of 30 hops:
        
1    <1 ms     *        1 ms  10.0.2.4
2     1 ms     1 ms     1 ms  10.0.1.4
        
Trace complete.
```

첫 번째 홉이 네트워크 가상 어플라이언스의 개인 IP 주소인 10.0.2.4인 것을 확인할 수 있습니다. 두 번째 홉은 *myVmMgmt* 가상 머신의 개인 IP 주소인 10.0.1.4입니다. 경로가 *myRouteTablePublic* 경로 테이블에 추가되고 *공용* 서브넷에 연결되었으므로 Azure는 트래픽을 직접 *개인* 서브넷에 라우팅하는 대신 NVA를 통해 트래픽을 라우팅합니다.

*myVmWeb* 및 *myVmMgmt* 가상 머신 모두에 대해 원격 데스크톱 세션을 닫습니다.

## <a name="troubleshoot-routing"></a>라우팅 문제 해결

이전 단계에서 설명한 대로 Azure는 사용자가 필요에 따라 자신 고유의 경로로 재정의할 수 있는 기본 경로를 적용합니다. 경우에 따라 트래픽이 예상 대로 라우팅하지 못할 수 있습니다. 미국 동부 지역에 Network Watcher가 없는 경우 [New-AzureRmNetworkWatcher](/powershell/module/azurerm.network/new-azurermnetworkwatcher)를 사용하여 해당 지역에서 Network Watcher를 사용할 수 있도록 설정합니다.

```azurepowershell-interactive
# Enable network watcher for east region, if you don't already have a network watcher enabled for the region:
$nw = New-AzureRmNetworkWatcher `
 -Location eastus `
 -Name myNetworkWatcher_eastus `
 -ResourceGroupName myResourceGroup
```

트래픽이 두 가상 머신 간에 라우팅되는 방법을 결정하려면 [Get-AzureRmNetworkWatcherNextHop](/powershell/module/azurerm.network/get-azurermnetworkwatchernexthop)을 사용합니다. 예를 들어 다음 명령은 *myVmWeb*(10.0.0.4) 가상 머신에서 *myVmMgmt*(10.0.1.4) 가상 머신으로의 트래픽 라우팅을 테스트합니다.

```azurepowershell-interactive
$vmWeb = Get-AzureRmVM `
  -Name myVmWeb `
  -ResourceGroupName myResourceGroup

Get-AzureRmNetworkWatcherNextHop `
  -DestinationIPAddress 10.0.1.4 `
  -NetworkWatcherName myNetworkWatcher_eastus `
  -ResourceGroupName myResourceGroup `
  -SourceIPAddress 10.0.0.4 `
  -TargetVirtualMachineId $vmWeb.Id
```
잠시 후 다음과 같은 출력이 반환됩니다.

```azurepowershell
NextHopIpAddress    NextHopType       RouteTableId
------------------  ---------------- ---------------------------------------------------------------------------------------------------------------------------
10.0.2.4            VirtualAppliance  /subscriptions/<Subscription-Id>/resourceGroups/myResourceGroup/providers/Microsoft.Network/routeTables/myRouteTablePublic
```

출력에는 *myVmWeb*에서 *myVmMgmt*의 트래픽에 대한 다음 홉 IP 주소가 10.0.2.4(*myVmNva* 가상 머신)이며, 다음 홉 형식은 *VirtualAppliance*이고, 라우팅을 유발하는 경로 테이블은 *myRouteTablePublic*이라는 정보가 포함됩니다.

각 네트워크 인터페이스에 대한 유효한 경로는 Azure의 기본 경로와 사용자가 정의한 경로의 조합입니다. [Get-AzureRmEffectiveRouteTable](/powershell/module/azurerm.network/get-azurermeffectiveroutetable)을 사용하여 가상 머신에서 네트워크 인터페이스에 유효한 모든 경로를 확인합니다. 예를 들어 *myVmWeb* 네트워크 인터페이스에 대한 유효한 경로를 *myVmWeb* 가상 머신에 표시하려면 다음 명령을 입력합니다.

```azurepowershell-interactive
Get-AzureRmEffectiveRouteTable `
  -NetworkInterfaceName myVmWeb `
  -ResourceGroupName myResourceGroup `
  | Format-Table
```

모든 기본 경로 및 이전 단계에서 추가한 경로가 반환됩니다.

## <a name="clean-up-resources"></a>리소스 정리

더 이상 필요하지 않은 경우 [Remove-AzureRmResourcegroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup)을 사용하여 리소스 그룹 및 그룹에 포함된 모든 리소스를 제거합니다.

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>다음 단계

이 문서에서는 경로 테이블을 만들고 서브넷에 연결했습니다. 공용 서브넷에서 개인 서브넷으로 트래픽을 라우팅하는 네트워크 가상 어플라이언스를 만들었습니다. 가상 네트워크 내에서 여러 Azure 리소스를 배포할 수 있는 반면, 일부 Azure PaaS 서비스에 대한 리소스는 가상 네트워크에 배포할 수 없습니다. 하지만 일부 Azure PaaS 서비스의 리소스에 대한 액세스를 가상 네트워크 서브넷의 트래픽만으로 제한할 수 있습니다. 다음 문서에서 Azure PaaS 리소스에 대한 네트워크 액세스를 제한하는 방법에 대해 알아보세요.

> [!div class="nextstepaction"]
> [PaaS 리소스에 대한 네트워크 액세스 제한](virtual-network-service-endpoints-configure.md#azure-powershell)