---
title: '자습서: Azure PowerShell을 사용하여 하이브리드 네트워크에서 Azure Firewall 배포 및 구성'
description: 이 자습서에서는 Azure Portal을 사용하여 Azure Firewall을 배포하고 구성하는 방법을 알아봅니다.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: tutorial
ms.date: 10/27/2018
ms.author: victorh
ms.openlocfilehash: d69bd055c95592961216f5da1efaedc4a642fd63
ms.sourcegitcommit: a08d1236f737915817815da299984461cc2ab07e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/26/2018
ms.locfileid: "52316399"
---
# <a name="tutorial-deploy-and-configure-azure-firewall-in-a-hybrid-network-using-azure-powershell"></a>자습서: Azure PowerShell을 사용하여 하이브리드 네트워크에서 Azure Firewall 배포 및 구성

온-프레미스 네트워크를 Azure Virtual Network에 연결하여 하이브리드 네트워크를 만들 경우 Azure 네트워크 리소스에 대한 액세스를 제어하는 기능은 전체 보안 계획의 중요한 부분입니다.

Azure Firewall을 사용하여 허용 및 거부된 네트워크 트래픽을 정의하는 규칙을 사용하여 하이브리드 네트워크에서 네트워크 액세스를 제어할 수 있습니다.

이 자습서에서는 다음과 같은 세 가상 네트워크를 만듭니다.

- **VNet-Hub** - 방화벽이 이 가상 네트워크에 있습니다.
- **VNet-Spoke** - 스포크 가상 네트워크는 Azure에 있는 워크로드를 나타냅니다.
- **VNet-Onprem** - 온-프레미스 가상 네트워크는 온-프레미스 네트워크를 나타냅니다. 실제 배포에서 VPN 또는 Express Route 연결을 통해 연결할 수 있습니다. 간단히 하기 위해 이 자습서에서는 VPN 게이트웨이 연결을 사용하며 Azure에 있는 가상 네트워크를 사용하여 온-프레미스 네트워크를 나타냅니다.

![하이브리드 네트워크의 방화벽](media/tutorial-hybrid-ps/hybrid-network-firewall.png)

이 자습서에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * 변수 선언
> * 방화벽 허브 가상 네트워크 만들기
> * 스포크 가상 네트워크 만들기
> * 온-프레미스 가상 네트워크 만들기
> * 방화벽 구성 및 배포
> * VPN 게이트웨이 만들기 및 연결
> * 허브 및 스포크 가상 네트워크 피어링
> * 경로 만들기
> * 가상 머신 만들기
> * 방화벽 테스트

## <a name="prerequisites"></a>필수 조건

이 자습서에서는 PowerShell을 로컬로 실행해야 합니다. Azure PowerShell 모듈 버전 6.12.0 이상이 설치되어 있어야 합니다. `Get-Module -ListAvailable AzureRM`을 실행하여 버전을 찾습니다. 업그레이드해야 하는 경우 [Azure PowerShell 모듈 설치](https://docs.microsoft.com/powershell/azure/install-azurerm-ps)를 참조하세요. PowerShell 버전을 확인한 후 `Login-AzureRmAccount`를 실행하여 Azure와의 연결을 만듭니다.

이 시나리오가 제대로 작동하기 위해서는 세 가지 주요 요구 사항이 있습니다.

- Azure Firewall IP 주소를 가리키는 스포크 서브넷의 사용자 정의 경로가 기본 게이트웨이입니다. 이 경로 테이블에서 BGP 경로 전파를 **사용 안 함**으로 설정해야 합니다.
- 허브 게이트웨이 서브넷의 사용자 정의 경로는 스포크 네트워크에 대한 다음 호프로 방화벽 IP 주소를 가리켜야 합니다.
- Azure Firewall 서브넷에서는 BGP로부터 경로를 학습하므로 사용자 정의 경로가 필요하지 않습니다.
- VNet-Hub와 VNet-Spoke를 피어링할 때는 **AllowGatewayTransit**, VNet-Spoke와 VNet-Hub를 피어링할 때는 **UseRemoteGateways**를 설정합니다.

이 경로를 만드는 방법은 이 자습서의 [경로 만들기](#create-routes) 섹션을 참조하세요.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

## <a name="declare-the-variables"></a>변수 선언

다음 예제에서는 이 자습서에 대한 값을 사용하여 변수를 선언합니다. 경우에 따라 사용자 구독에서 작동하도록 하기 위해 일부 값을 사용자 고유의 값으로 바꾸어야 할 수 있습니다. 필요한 경우 변수를 수정한 다음 복사하여 PowerShell 콘솔에 붙여 넣습니다.

```azurepowershell
$RG1 = "FW-Hybrid-Test"
$Location1 = "East US"

# Variables for the firewall hub VNet

$VNetnameHub = "VNet-hub"
$SNnameHub = "AzureFirewallSubnet"
$VNetHubPrefix = "10.5.0.0/16"
$SNHubPrefix = "10.5.0.0/24"
$SNGWHubPrefix = "10.5.1.0/24"
$GWHubName = "GW-hub"
$GWHubpipName = "VNet-hub-GW-pip"
$GWIPconfNameHub = "GW-ipconf-hub"
$ConnectionNameHub = "hub-to-Onprem"

# Variables for the spoke virtual network

$VnetNameSpoke = "VNet-Spoke"
$SNnameSpoke = "SN-Workload"
$VNetSpokePrefix = "10.6.0.0/16"
$SNSpokePrefix = "10.6.0.0/24"
$SNSpokeGWPrefix = "10.6.1.0/24"

# Variables for the on-premises virtual network

$VNetnameOnprem = "Vnet-Onprem"
$SNNameOnprem = "SN-Corp"
$VNetOnpremPrefix = "192.168.0.0/16"
$SNOnpremPrefix = "192.168.1.0/24"
$SNGWOnpremPrefix = "192.168.2.0/24"
$GWOnpremName = "GW-Onprem"
$GWIPconfNameOnprem = "GW-ipconf-Onprem"
$ConnectionNameOnprem = "Onprem-to-hub"
$GWOnprempipName = "VNet-Onprem-GW-pip"

$SNnameGW = "GatewaySubnet"
```


## <a name="create-the-firewall-hub-virtual-network"></a>방화벽 허브 가상 네트워크 만들기

먼저 이 자습서에 대한 리소스를 포함하는 리소스 그룹을 만듭니다.

```azurepowershell
  New-AzureRmResourceGroup -Name $RG1 -Location $Location1
  ```

가상 네트워크에 포함될 서브넷을 정의합니다.

```azurepowershell
$FWsub = New-AzureRmVirtualNetworkSubnetConfig -Name $SNnameHub -AddressPrefix $SNHubPrefix
$GWsub = New-AzureRmVirtualNetworkSubnetConfig -Name $SNnameGW -AddressPrefix $SNGWHubPrefix
```

이제 방화벽 허브 가상 네트워크를 만듭니다.

```azurepowershell
$VNetHub = New-AzureRmVirtualNetwork -Name $VNetnameHub -ResourceGroupName $RG1 `
-Location $Location1 -AddressPrefix $VNetHubPrefix -Subnet $FWsub,$GWsub
```

가상 네트워크용으로 만들 VPN 게이트웨이에 할당할 공용 IP 주소를 요청합니다. *AllocationMethod*가 **동적**인지 확인합니다. 사용할 IP 주소를 지정할 수는 없습니다. IP 주소는 VPN 게이트웨이에 동적으로 할당됩니다. 

  ```azurepowershell
  $gwpip1 = New-AzureRmPublicIpAddress -Name $GWHubpipName -ResourceGroupName $RG1 `
  -Location $Location1 -AllocationMethod Dynamic
```

## <a name="create-the-spoke-virtual-network"></a>스포크 가상 네트워크 만들기

스포크 가상 네트워크에 포함될 서브넷을 정의합니다.

```azurepowershell
$Spokesub = New-AzureRmVirtualNetworkSubnetConfig -Name $SNnameSpoke -AddressPrefix $SNSpokePrefix
$GWsubSpoke = New-AzureRmVirtualNetworkSubnetConfig -Name $SNnameGW -AddressPrefix $SNSpokeGWPrefix
```

스포크 가상 네트워크를 만듭니다.

```azurepowershell
$VNetSpoke = New-AzureRmVirtualNetwork -Name $VnetNameSpoke -ResourceGroupName $RG1 `
-Location $Location1 -AddressPrefix $VNetSpokePrefix -Subnet $Spokesub,$GWsubSpoke
```

## <a name="create-the-on-premises-virtual-network"></a>온-프레미스 가상 네트워크 만들기

가상 네트워크에 포함될 서브넷을 정의합니다.

```azurepowershell
$Onpremsub = New-AzureRmVirtualNetworkSubnetConfig -Name $SNNameOnprem -AddressPrefix $SNOnpremPrefix
$GWOnpremsub = New-AzureRmVirtualNetworkSubnetConfig -Name $SNnameGW -AddressPrefix $SNGWOnpremPrefix
```

이제 온-프레미스 가상 네트워크를 만듭니다.

```azurepowershell
$VNetOnprem = New-AzureRmVirtualNetwork -Name $VNetnameOnprem -ResourceGroupName $RG1 `
-Location $Location1 -AddressPrefix $VNetOnpremPrefix -Subnet $Onpremsub,$GWOnpremsub
```

가상 네트워크용으로 만들 게이트웨이에 할당할 공용 IP 주소를 요청합니다. *AllocationMethod*가 **동적**인지 확인합니다. 사용할 IP 주소를 지정할 수는 없습니다. IP 주소는 게이트웨이에 동적으로 할당됩니다. 

  ```azurepowershell
  $gwOnprempip = New-AzureRmPublicIpAddress -Name $GWOnprempipName -ResourceGroupName $RG1 `
  -Location $Location1 -AllocationMethod Dynamic
```

## <a name="configure-and-deploy-the-firewall"></a>방화벽 구성 및 배포

이제 허브 가상 네트워크에 방화벽을 배포합니다.

```azurepowershell
# Get a Public IP for the firewall
$FWpip = New-AzureRmPublicIpAddress -Name "fw-pip" -ResourceGroupName $RG1 `
  -Location $Location1 -AllocationMethod Static -Sku Standard
# Create the firewall
$Azfw = New-AzureRmFirewall -Name AzFW01 -ResourceGroupName $RG1 -Location $Location1 -VirtualNetworkName $VNetnameHub -PublicIpName fw-pip

#Save the firewall private IP address for future use

$AzfwPrivateIP = $Azfw.IpConfigurations.privateipaddress
$AzfwPrivateIP

```

### <a name="configure-network-rules"></a>네트워크 규칙 구성

<!--- $Rule3 = New-AzureRmFirewallNetworkRule -Name "AllowPing" -Protocol ICMP -SourceAddress $SNOnpremPrefix `
   -DestinationAddress $VNetSpokePrefix -DestinationPort *--->

```azurepowershell
$Rule1 = New-AzureRmFirewallNetworkRule -Name "AllowWeb" -Protocol TCP -SourceAddress $SNOnpremPrefix `
   -DestinationAddress $VNetSpokePrefix -DestinationPort 80

$Rule2 = New-AzureRmFirewallNetworkRule -Name "AllowRDP" -Protocol TCP -SourceAddress $SNOnpremPrefix `
   -DestinationAddress $VNetSpokePrefix -DestinationPort 3389

$NetRuleCollection = New-AzureRmFirewallNetworkRuleCollection -Name RCNet01 -Priority 100 `
   -Rule $Rule1,$Rule2 -ActionType "Allow"
$Azfw.NetworkRuleCollections = $NetRuleCollection
Set-AzureRmFirewall -AzureFirewall $Azfw
```

## <a name="create-and-connect-the-vpn-gateways"></a>VPN 게이트웨이 만들기 및 연결

허브 및 온-프레미스 가상 네트워크는 VPN Gateway를 통해 연결됩니다.

### <a name="create-a-vpn-gateway-for-the-hub-virtual-network"></a>허브 가상 네트워크에 대한 VPN Gateway 만들기

VPN 게이트웨이 구성을 만듭니다. VPN 게이트웨이 구성은 사용할 공용 IP 주소 및 서브넷을 정의합니다.

  ```azurepowershell
  $vnet1 = Get-AzureRmVirtualNetwork -Name $VNetnameHub -ResourceGroupName $RG1
  $subnet1 = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet1
  $gwipconf1 = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GWIPconfNameHub `
  -Subnet $subnet1 -PublicIpAddress $gwpip1
  ```

이제 허브 가상 네트워크에 대한 VPN Gateway를 만듭니다. 네트워크 간 구성에는 RouteBased VpnType이 필요합니다. 종종 선택한 VPN 게이트웨이 SKU에 따라 VPN 게이트웨이를 만드는 데 45분 이상 걸릴 수 있습니다.

```azurepowershell
New-AzureRmVirtualNetworkGateway -Name $GWHubName -ResourceGroupName $RG1 `
-Location $Location1 -IpConfigurations $gwipconf1 -GatewayType Vpn `
-VpnType RouteBased -GatewaySku basic
```

### <a name="create-a-vpn-gateway-for-the-on-premises-virtual-network"></a>온-프레미스 가상 네트워크에 대한 VPN Gateway 만들기

VPN 게이트웨이 구성을 만듭니다. VPN 게이트웨이 구성은 사용할 공용 IP 주소 및 서브넷을 정의합니다.

  ```azurepowershell
$vnet2 = Get-AzureRmVirtualNetwork -Name $VNetnameOnprem -ResourceGroupName $RG1
$subnet2 = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet2
$gwipconf2 = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GWIPconfNameOnprem `
  -Subnet $subnet2 -PublicIpAddress $gwOnprempip
  ```

이제 온-프레미스 가상 네트워크에 대한 VPN Gateway를 만듭니다. 네트워크 간 구성에는 RouteBased VpnType이 필요합니다. 종종 선택한 VPN 게이트웨이 SKU에 따라 VPN 게이트웨이를 만드는 데 45분 이상 걸릴 수 있습니다.

```azurepowershell
New-AzureRmVirtualNetworkGateway -Name $GWOnpremName -ResourceGroupName $RG1 `
-Location $Location1 -IpConfigurations $gwipconf2 -GatewayType Vpn `
-VpnType RouteBased -GatewaySku basic
```

### <a name="create-the-vpn-connections"></a>VPN 연결 만들기

이제 허브와 온-프레미스 게이트웨이 간의 VPN 연결을 만들 수 있습니다.

#### <a name="get-the-vpn-gateways"></a>VPN 게이트웨이 가져오기

```azurepowershell
$vnetHubgw = Get-AzureRmVirtualNetworkGateway -Name $GWHubName -ResourceGroupName $RG1
$vnetOnpremgw = Get-AzureRmVirtualNetworkGateway -Name $GWOnpremName -ResourceGroupName $RG1
```

#### <a name="create-the-connections"></a>연결 만들기

이 단계에서는 허브 가상 네트워크에서 온-프레미스 가상 네트워크로의 연결을 만듭니다. 예제에서 참조된 공유 키를 볼 수 있습니다. 공유 키에 대해 고유한 값을 사용할 수 있습니다. 중요한 점은 두 연결에서 모두 공유 키가 일치해야 한다는 것입니다. 연결 만들기는 완료하는 데 꽤 오래 걸릴 수 있습니다.

```azurepowershell
New-AzureRmVirtualNetworkGatewayConnection -Name $ConnectionNameHub -ResourceGroupName $RG1 `
-VirtualNetworkGateway1 $vnetHubgw -VirtualNetworkGateway2 $vnetOnpremgw -Location $Location1 `
-ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3'
```
온-프레미스에서 허브 가상 네트워크로의 연결을 만듭니다. 이 단계는 VNet-Onprem에서 VNet-hub로의 연결을 만든다는 점을 제외하고는 이전 단계와 유사합니다. 공유된 키가 일치하는지 확인합니다. 몇 분 후 연결이 설정됩니다.

  ```azurepowershell
  New-AzureRmVirtualNetworkGatewayConnection -Name $ConnectionNameOnprem -ResourceGroupName $RG1 `
  -VirtualNetworkGateway1 $vnetOnpremgw -VirtualNetworkGateway2 $vnetHubgw -Location $Location1 `
  -ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3'
  ```

#### <a name="verify-the-connection"></a>연결 확인

*-Debug*를 포함하거나 포함하지 않은 상태에서 *Get-AzureRmVirtualNetworkGatewayConnection* cmdlet을 사용하여 연결 성공을 확인할 수 있습니다. 일치하는 값을 구성하는 데 다음 cmdlet 예제를 사용합니다. 메시지가 표시되면 **모두** 실행하기 위해 **A**를 선택합니다. 예제에서 *-Name*은 테스트하려는 연결의 이름을 나타냅니다.

```azurepowershell
Get-AzureRmVirtualNetworkGatewayConnection -Name $ConnectionNameHub -ResourceGroupName $RG1
```

cmdlet이 완료되면 값을 봅니다. 다음 예제에서는 연결 상태가 *연결됨*으로 표시되고 송/수신 바이트를 볼 수 있습니다.

```
"connectionStatus": "Connected",
"ingressBytesTransferred": 33509044,
"egressBytesTransferred": 4142431
```

## <a name="peer-the-hub-and-spoke-virtual-networks"></a>허브 및 스포크 가상 네트워크 피어링

이제 허브 및 스포크 가상 네트워크를 피어링합니다.

```azurepowershell
# Peer hub to spoke
Add-AzureRmVirtualNetworkPeering -Name HubtoSpoke -VirtualNetwork $VNetHub -RemoteVirtualNetworkId $VNetSpoke.Id -AllowGatewayTransit

# Peer spoke to hub
Add-AzureRmVirtualNetworkPeering -Name SpoketoHub -VirtualNetwork $VNetSpoke -RemoteVirtualNetworkId $VNetHub.Id -AllowForwardedTraffic -UseRemoteGateways
```

## <a name="create-the-routes"></a>경로 만들기

다음으로 두 경로 만듭니다.

- 허브 게이트웨이 서브넷에서 방화벽 IP 주소를 통해 스포크 서브넷으로 가는 경로
- 방화벽 IP 주소를 통해 스포크 서브넷으로부터의 기본 경로

```azurepowershell
#Create a route table
$routeTableHubSpoke = New-AzureRmRouteTable `
  -Name 'UDR-Hub-Spoke' `
  -ResourceGroupName $RG1 `
  -location $Location1

#Create a route
Get-AzureRmRouteTable `
  -ResourceGroupName $RG1 `
  -Name UDR-Hub-Spoke `
  | Add-AzureRmRouteConfig `
  -Name "ToSpoke" `
  -AddressPrefix $VNetSpokePrefix `
  -NextHopType "VirtualAppliance" `
  -NextHopIpAddress $AzfwPrivateIP `
 | Set-AzureRmRouteTable

#Associate the route table to the subnet

Set-AzureRmVirtualNetworkSubnetConfig `
  -VirtualNetwork $VNetHub `
  -Name $SNnameGW `
  -AddressPrefix $SNGWHubPrefix `
  -RouteTable $routeTableHubSpoke | `
Set-AzureRmVirtualNetwork

#Now create the default route

#Create a table, with BGP route propagation disabled
$routeTableSpokeDG = New-AzureRmRouteTable `
  -Name 'UDR-DG' `
  -ResourceGroupName $RG1 `
  -location $Location1 `
  -DisableBgpRoutePropagation

#Create a route
Get-AzureRmRouteTable `
  -ResourceGroupName $RG1 `
  -Name UDR-DG `
  | Add-AzureRmRouteConfig `
  -Name "ToSpoke" `
  -AddressPrefix 0.0.0.0/0 `
  -NextHopType "VirtualAppliance" `
  -NextHopIpAddress $AzfwPrivateIP `
 | Set-AzureRmRouteTable

#Associate the route table to the subnet

Set-AzureRmVirtualNetworkSubnetConfig `
  -VirtualNetwork $VNetSpoke `
  -Name $SNnameSpoke `
  -AddressPrefix $SNSpokePrefix `
  -RouteTable $routeTableSpokeDG | `
Set-AzureRmVirtualNetwork
```

## <a name="create-virtual-machines"></a>가상 머신 만들기

이제 스포크 워크로드 및 온-프레미스 가상 머신을 만들어 적절한 서브넷에 배치합니다.

### <a name="create-the-workload-virtual-machine"></a>워크로드 가상 머신 만들기

스포크 가상 네트워크에서 IIS를 실행하고 공용 IP 주소가 없으며 Ping 유입을 허용하는 가상 머신을 만듭니다.
메시지가 표시되면 가상 머신의 사용자 이름 및 암호를 입력합니다.

```azurepowershell
# Create an inbound network security group rule for ports 3389 and 80
$nsgRuleRDP = New-AzureRmNetworkSecurityRuleConfig -Name Allow-RDP  -Protocol Tcp `
  -Direction Inbound -Priority 200 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix $SNSpokePrefix -DestinationPortRange 3389 -Access Allow
$nsgRuleWeb = New-AzureRmNetworkSecurityRuleConfig -Name Allow-web  -Protocol Tcp `
  -Direction Inbound -Priority 202 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix $SNSpokePrefix -DestinationPortRange 80 -Access Allow

# Create a network security group
$nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName $RG1 -Location $Location1 -Name NSG-Spoke02 -SecurityRules $nsgRuleRDP,$nsgRuleWeb

#Create the NIC
$NIC = New-AzureRmNetworkInterface -Name spoke-01 -ResourceGroupName $RG1 -Location $Location1 -SubnetId $VnetSpoke.Subnets[0].Id -NetworkSecurityGroupId $nsg.Id

#Define the virtual machine
$VirtualMachine = New-AzureRmVMConfig -VMName VM-Spoke-01 -VMSize "Standard_DS2"
$VirtualMachine = Set-AzureRmVMOperatingSystem -VM $VirtualMachine -Windows -ComputerName Spoke-01 -ProvisionVMAgent -EnableAutoUpdate
$VirtualMachine = Add-AzureRmVMNetworkInterface -VM $VirtualMachine -Id $NIC.Id
$VirtualMachine = Set-AzureRmVMSourceImage -VM $VirtualMachine -PublisherName 'MicrosoftWindowsServer' -Offer 'WindowsServer' -Skus '2016-Datacenter' -Version latest

#Create the virtual machine
New-AzureRmVM -ResourceGroupName $RG1 -Location $Location1 -VM $VirtualMachine -Verbose

#Install IIS on the VM
Set-AzureRmVMExtension `
    -ResourceGroupName $RG1 `
    -ExtensionName IIS `
    -VMName VM-Spoke-01 `
    -Publisher Microsoft.Compute `
    -ExtensionType CustomScriptExtension `
    -TypeHandlerVersion 1.4 `
    -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server"}' `
    -Location $Location1
```

<!---#Create a host firewall rule to allow ping in
Set-AzureRmVMExtension `
    -ResourceGroupName $RG1 `
    -ExtensionName IIS `
    -VMName VM-Spoke-01 `
    -Publisher Microsoft.Compute `
    -ExtensionType CustomScriptExtension `
    -TypeHandlerVersion 1.4 `
    -SettingString '{"commandToExecute":"powershell New-NetFirewallRule –DisplayName “Allow ICMPv4-In” –Protocol ICMPv4"}' `
    -Location $Location1--->

### <a name="create-the-on-premises-virtual-machine"></a>온-프레미스 가상 머신 만들기

원격 데스크톱을 사용하여 공용 IP 주소에 연결하는 간단한 가상 머신입니다. 여기에서 방화벽을 통해 온-프레미스 서버에 연결할 수 있습니다. 메시지가 표시되면 가상 머신의 사용자 이름 및 암호를 입력합니다.

```azurepowershell
New-AzureRmVm `
    -ResourceGroupName $RG1 `
    -Name "VM-Onprem" `
    -Location $Location1 `
    -VirtualNetworkName $VNetnameOnprem `
    -SubnetName $SNNameOnprem `
    -OpenPorts 3389 `
    -Size "Standard_DS2"
```

## <a name="test-the-firewall"></a>방화벽 테스트

먼저 **VM-spoke-01** 가상 머신의 개인 IP 주소를 가져와 기록합니다.

```azurepowershell
$NIC.IpConfigurations.privateipaddress
```

Azure Portal에서 **VM-Onprem** 가상 머신에 연결합니다.
<!---2. Open a Windows PowerShell command prompt on **VM-Onprem**, and ping the private IP for **VM-spoke-01**.

   You should get a reply.--->
**VM-Onprem**에서 웹 브라우저를 열고 http://\<VM-spoke-01 private IP\>로 이동합니다.

Internet Information Services 기본 페이지가 표시됩니다.

**VM-Onprem**에서 개인 IP 주소의 **VM-spoke-01**로 원격 데스크톱을 엽니다.

연결에 성공하며 선택한 사용자 이름과 암호를 사용하여 로그인할 수 있습니다.

이제 방화벽 규칙이 작동하는지 확인했습니다.

<!---- You can ping the server on the spoke VNet.--->
- 스포크 가상 네트워크에서 웹 서버를 탐색할 수 있습니다.
- RDP를 사용하여 스포크 가상 네트워크에 있는 서버에 연결할 수 있습니다.

이제 방화벽 규칙이 예상대로 작동하는지 호가인하기 위해 방화벽 네트워크 규칙 수집 동작을 **거부**로 변경합니다. 다음 스크립트를 실행하여 규칙 수집 동작을 **거부**로 변경합니다.

```azurepowershell
$rcNet = $azfw.GetNetworkRuleCollectionByName("RCNet01")
$rcNet.action.type = "Deny"

Set-AzureRmFirewall -AzureFirewall $azfw
```

이제 테스트를 다시 실행합니다. 이번에는 모두 실패해야 합니다. 변경된 규칙을 테스트하려면 먼저 기존 원격 데스크톱을 모두 닫습니다.

## <a name="clean-up-resources"></a>리소스 정리

다음 자습서에서 사용하기 위해 방화벽 리소스를 그대로 유지하거나, 더 이상 필요하지 않은 경우 **FW-Hybrid-Test** 리소스 그룹을 삭제하여 모든 방화벽 관련 리소스를 삭제할 수 있습니다.

## <a name="next-steps"></a>다음 단계

그런 다음, Azure Firewall 로그를 모니터링할 수 있습니다.

> [!div class="nextstepaction"]
> [자습서: Azure Firewall 로그 모니터링](./tutorial-diagnostics.md)
