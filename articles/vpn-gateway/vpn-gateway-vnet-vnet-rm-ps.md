<properties
   pageTitle="Azure 리소스 관리자 및 PowerShell을 사용하여 VNet-VNet 연결 만들기 | Microsoft Azure"
	description="이 문서에서는 Azure 리소스 관리자 및 PowerShell을 사용하여 가상 네트워크를 함께 연결하는 과정을 안내합니다."
	services="vpn-gateway"
	documentationCenter="na"
	authors="cherylmc"
	manager="carolz"
	editor=""/>

<tags
   ms.service="vpn-gateway"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="infrastructure-services"
	ms.date="08/20/2015"
	ms.author="cherylmc"/>

# Azure 리소스 관리자 및 PowerShell을 사용하여 VNet-VNet 연결 구성

> [AZURE.SELECTOR]
- [Azure Portal](virtual-networks-configure-vnet-to-vnet-connection.md)
- [PowerShell - Azure Resource Manager](vpn-gateway-vnet-vnet-rm-ps.md)


가상 네트워크를 다른 가상 네트워크에 연결(VNet-VNet)하는 것은 VNet을 온-프레미스 사이트 위치에 연결하는 것과 매우 유사합니다. 두 연결 유형 모두 VPN 게이트웨이를 사용하여 IPsec/IKE를 통한 보안 터널을 제공합니다. 연결하는 VNet은 서로 다른 지역에 있을 수 있습니다. VNet 간 통신을 다중 사이트 구성과 통합할 수도 있습니다. 이렇게 하면 아래 다이어그램에 표시된 것처럼 크로스-프레미스 연결을 가상 네트워크 간 연결과 결합하는 네트워크 토폴로지를 설정할 수 있습니다.


![VNet 간 연결 다이어그램](./media/virtual-networks-configure-vnet-to-vnet-connection/IC727360.png)

 

>[AZURE.NOTE]Azure에는 현재 클래식 배포 모드 및 Azure 리소스 관리자 배포 모드의 두 가지 배포 모드가 있습니다. 구성 cmdlet 및 단계는 배포 모드 간에 서로 다릅니다. 이 항목에서는 Azure 리소스 관리자 모드에서 만든 가상 네트워크를 함께 연결하는 과정을 단계별로 진행합니다. 클래식 배포 모드를 사용하여 VNet-VNet 연결을 만들려면 [Azure 포털을 사용하여 VNet-VNet 연결 구성](virtual-networks-configure-vnet-to-vnet-connection.md)을 참조하세요. 클래식 모드에서 만든 가상 네트워크를 Azure 리소스 관리자에서 만든 가상 네트워크에 연결하려는 경우 [새 VNet에 클래식 VNet 연결](../virtual-network/virtual-networks-arm-asm-s2s.md)을 참조하세요.

## 가상 네트워크에 연결하는 이유

다음과 같은 이유로 가상 네트워크에 연결할 수 있습니다.

- **지역 간 지리적 중복 및 지리적 상태**
	- 인터넷 연결 끝점으로 이동하지 않고도 보안 연결을 통해 지역에서 복제 또는 동기화를 직접 설정할 수 있습니다.
	- Azure 부하 분산 장치 및 Microsoft 또는 타사 클러스터링 기술을 사용하여 여러 Azure 지역 간의 지리적 중복을 통해 워크로드의 가용성을 높게 설정할 수 있습니다. 이러한 작업의 한 가지 주요 예는 여러 Azure 지역에 분산된 가용성 그룹을 사용하여 SQL AlwaysOn을 설정하는 것입니다.

- **분리 경계가 뚜렷한 지역별 다중 계층 응용 프로그램**
	- 같은 지역 내에서 뚜렷한 경계를 적용해 여러 가상 네트워크가 함께 연결된 다중 계층 응용 프로그램을 설정하고 계층 간 통신을 보호할 수 있습니다.



### 주의할 사항

이 문서에서는 VNet1과 VNet2의 두 가상 네트워크를 연결하는 과정을 단계별로 진행합니다. 여기서 실제 네트워크 디자인 요구 사항과 호환되는 IP 주소 범위를 대체하려면 네트워킹에 대해 잘 알고 있어야 합니다.


![VNet 간 연결](./media/virtual-networks-configure-vnet-to-vnet-connection/IC727361.png)



- 가상 네트워크는 같은 Azure 지역(위치)에 있을 수도 있고 다른 Azure 지역(위치)에 있을 수도 있습니다.

- 클라우드 서비스 또는 부하 분산 끝점은 연결되어 있더라도 여러 가상 네트워크에 분산될 수 없습니다.

- 크로스-프레미스 연결이 필요한 경우가 아니면 여러 Azure 가상 네트워크를 연결할 때 온-프레미스 VPN 게이트웨이는 필요하지 않습니다.

- VNet 간 연결은 가상 네트워크 연결을 지원합니다. 그러나 가상 네트워크에 포함되어 있지 않은 가상 컴퓨터 또는 클라우드 서비스 연결은 지원하지 않습니다.

- VNet 간 연결에는 RouteBased(이전에는 동적으로 지칭) VPN 유형의 Azure VPN 게이트웨이가 필요합니다.

- 가상 네트워크 연결을 다중 사이트 VPN과 함께 사용할 수 있으며 가상 네트워크 VPN 게이트웨이당 최대 10개의 VPN 터널을 다른 가상 네트워크 또는 온-프레미스 사이트에 연결할 수 있습니다.

- 가상 네트워크 및 온-프레미스 로컬 네트워크 사이트의 주소 공간이 겹쳐서는 안 됩니다. 주소 공간이 겹치면 가상 네트워크 만들기가 실패합니다.

- 가상 네트워크 한 쌍 간의 중복 터널은 지원되지 않습니다.

- 가상 네트워크의 모든 VPN 터널은 Azure VPN 게이트웨이의 사용 가능한 대역폭 및 Azure의 동일 VPN 게이트웨이 작동 시간 SLA를 공유합니다.

- VNet-VNet 트래픽은 Azure 백본 전체에서 이동됩니다.


## 시작하기 전에


시작하기 전에 다음이 있는지 확인합니다.


- 최신 버전의 Azure PowerShell cmdlet. [다운로드 페이지](http://azure.microsoft.com/downloads/)의 Windows PowerShell 섹션에서 최신 버전을 다운로드하여 설치할 수 있습니다. 
- Azure 구독. Azure 구독이 아직 없는 경우 [MSDN 구독자 혜택](http://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)을 활성화하거나 [무료 평가판](http://azure.microsoft.com/pricing/free-trial/)에 등록할 수 있습니다.
- 이미 가상 네트워크를 만든 경우 [기존 VNet 연결](#connecting-existing-vnets)을 참조하세요.
	


여러 단계에 걸쳐 VNet-VNet 연결을 만들고 구성합니다. 아래에 나열된 순서대로 각 섹션을 구성합니다.


1. [IP 주소 범위 계획](#plan-your-ip-address-ranges)
2. [구독에 연결](#connect-to-your-subscription)
3. [가상 네트워크 만들기](#create-a-virtual-network)
4. [게이트웨이에 대한 공용 IP 주소 요청](#request-a-public-ip-address)
5. [게이트웨이 구성 만들기](#create-the-gateway-configuration)
6. [게이트웨이 만들기](#create-the-gateway)
7. [VNet2 구성 반복](#create-vnet2)
8. [VPN 게이트웨이 연결](#connect-the-gateways)


## IP 주소 범위 계획

### 1단계

네트워크 구성을 구성하는 데 사용할 범위를 결정하는 것이 중요합니다. VNet1 측면에서 볼 때 VNet2는 Azure 플랫폼에 정의되어 있는 또 다른 VPN 연결일 뿐이고 VNet2에서도 마찬가지로 VNet1은 또 하나의 VPN 연결일 뿐입니다. 따라서 VNet 범위 또는 로컬 네트워크 범위가 겹치지 않는지 확인해야 합니다.


아래 단계에서는 두 개의 가상 네트워크와 해당 게이트웨이 서브넷 및 구성을 만듭니다. 그런 다음 두 VNet 간에 VPN 게이트웨이 연결을 만듭니다.

이 연습에서는 VNet에 대해 다음 값을 사용합니다.

VNet1 값:

- 가상 네트워크 이름 = VNet1
- 리소스 그룹 = testrg1
- 주소 공간 = 10.1.0.0/16 
- 지역 = 미국 서부
- GatewaySubnet = 10.1.0.0/28
- Subnet1 = 10.1.1.0/28

VNet2 값:

- 가상 네트워크 이름 = VNet2
- 리소스 그룹 = testrg2
- 주소 공간 = 10.2.0.0/16
- 지역 = 일본 동부
- GatewaySubnet = 10.2.0.0/28
- Subnet1 = 10.2.1.0/28

## 구독에 연결 

### 2단계


PowerShell 콘솔을 열고 Azure 리소스 관리자 모드로 전환합니다. 연결에 도움이 되도록 다음 샘플을 사용합니다.

		Add-AzureAccount

Select-AzureSubscription을 사용하여 사용하려는 구독에 연결합니다.

		Select-AzureSubscription "yoursubscription"

ARM 모드로 전환합니다. 이렇게 하면 모드를 전환하여 ARM cmdlet을 사용할 수 있습니다.

		Switch-AzureMode -Name AzureResourceManager

## 가상 네트워크 만들기

### 3단계


아래 샘플을 사용하여 가상 네트워크 및 게이트웨이 서브넷을 만듭니다. 사용자 고유의 값으로 대체합니다. 이 예제에서는 VNet1을 만듭니다. 나중에 이 단계를 반복해서 VNet2도 만듭니다.

먼저 리소스 그룹을 만듭니다.

			New-AzureResourceGroup -Name testrg1 -Location 'West US'

그런 다음 가상 네트워크를 만듭니다. 아래 샘플은 *VNet1*이라는 가상 네트워크와 *GatewaySubnet* 및 *VNet1*이라는 두 서브넷을 만듭니다. 특히 *GatewaySubnet*이라는 서브넷을 만드는 것이 중요합니다. 다른 이름을 지정하는 경우 연결 구성이 실패합니다. 아래 예제에서는 게이트웨이 서브넷이 /28을 사용합니다. /29만큼 작은 게이트웨이 서브넷을 사용하도록 선택할 수 있습니다. 일부 기능(예: Express 경로/사이트 간 연결 공존)은 더 큰 게이트웨이 /27이 필요하므로 앞으로 사용할 수 있는 추가 기능을 위해 게이트웨이 서브넷을 만들 수 있습니다.

 		$subnet = New-AzureVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.1.0.0/28
		$subnet1 = New-AzureVirtualNetworkSubnetConfig -Name 'Subnet1' -AddressPrefix '10.1.1.0/28'
		New-AzurevirtualNetwork -Name VNet1 -ResourceGroupName testrg1 -Location 'West US' -AddressPrefix 10.1.0.0/16 -Subnet $subnet, $subnet1

## 공용 IP 주소 요청

### 4단계

다음에는 VNet용으로 만들 게이트웨이에 할당할 공용 IP 주소를 요청합니다. 사용할 IP 주소를 지정할 수는 없으며 IP 주소는 게이트웨이에 동적으로 할당됩니다. 다음 구성 섹션에서 이 IP 주소를 사용합니다.

아래 샘플을 사용합니다. 이 주소의 할당 방법은 동적이어야 합니다.


		$gwpip= New-AzurePublicIpAddress -Name gwpip1 -ResourceGroupName testrg1 -Location 'West US' -AllocationMethod Dynamic


## 게이트웨이 구성 만들기

### 5단계

게이트웨이 구성은 사용할 공용 IP 주소 및 서브넷을 정의합니다. 아래 샘플을 사용하여 게이트웨이 구성을 만듭니다.


		$vnet = Get-AzureVirtualNetwork -Name VNet1 -ResourceGroupName testrg1
		$subnet = Get-AzureVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -VirtualNetwork $vnet
		$gwipconfig = New-AzureVirtualNetworkGatewayIpConfig -Name gwipconfig1 -SubnetId $subnet.Id -PublicIpAddressId $gwpip.Id 


## 게이트웨이 만들기

### 6단계

이 단계에서는 VNet용 가상 네트워크 게이트웨이를 만듭니다. VNet-VNet 구성에는 RouteBased VpnType이 필요합니다. 게이트웨이를 만드는 데는 시간이 걸릴 수 있으므로 기다리세요.

		New-AzureVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg1 -Location 'West US' -IpConfigurations $gwipconfig -GatewayType Vpn -VpnType RouteBased

## VNet2 만들기

### 7단계

VNet1을 구성한 후 이전 단계를 반복하여 VNet2과 해당 게이트웨이 구성을 지정합니다. VNet 및 해당 게이트웨이의 구성을 모두 완료했으면 [게이트웨이 연결](#connect-the-gateways)을 계속 진행합니다.


## 게이트웨이 연결

### 8단계

이 단계에서는 두 가상 네트워크 게이트웨이 간에 VPN 게이트웨이 연결을 만듭니다. 예제에서 참조된 공유 키를 볼 수 있습니다. 공유 키에 대해 고유한 값을 사용할 수 있습니다. 중요한 점은 두 구성에서 공유 키가 일치해야 한다는 것입니다.

연결을 만들 때 완료되는 데 다소 시간이 걸릴 수 있습니다.

**VNet1-VNet2**
    
    $vnetgw1 = Get-AzureVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg1
    $vnetgw2 = Get-AzureVirtualNetworkGateway -Name vnetgw2 -ResourceGroupName testrg2
    
    New-AzureVirtualNetworkGatewayConnection -Name conn1 -ResourceGroupName testrg1 -VirtualNetworkGateway1 $vnetgw1 -VirtualNetworkGateway2 $vnetgw2 -Location 'West US' -ConnectionType Vnet2Vnet -SharedKey 'abc123'


**VNet2-VNet1**
    
    $vnetgw1 = Get-AzureVirtualNetworkGateway -Name vnetgw2 -ResourceGroupName testrg2
    $vnetgw2 = Get-AzureVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg1
    
    New-AzureVirtualNetworkGatewayConnection -Name conn2 -ResourceGroupName testrg2 -VirtualNetworkGateway1 $vnetgw1 -VirtualNetworkGateway2 $vnetgw2 -Location 'Japan East' -ConnectionType Vnet2Vnet -SharedKey 'abc123'
    


잠시 후에 연결이 설정됩니다. 현재 Azure 리소스 관리자를 사용하여 만든 게이트웨이 및 연결은 Preview 포털에 표시되지 않습니다.

## 기존 VNet 연결

Azure 리소스 관리자 모드에서 이미 가상 네트워크를 만들었으며 연결하려는 경우 다음을 확인하세요.

- 각 VNet에 대해 /29 이상의 게이트웨이 서브넷이 있습니다.
- 가상 네트워크의 주소 범위는 중첩되지 않습니다.

VNet에 게이트웨이 서브넷을 추가해야 하는 경우 아래 샘플을 사용하여 원하는 값으로 바꿉니다. 게이트웨이 서브넷의 이름을 'GatewaySubnet'으로 지정해야 합니다. 다른 이름을 지정하는 경우 VPN 구성이 예상대로 작동하지 않습니다.

	
		$vnet = Get-AzureVirtualNetwork -ResourceGroupName testrg -Name testvnet
		Add-AzureVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.0.3.0/28 -VirtualNetwork $vnet
		Set-AzureVirtualNetwork -VirtualNetwork $vnet

게이트웨이 서브넷이 올바르게 구성되었는지 확인한 후에 [공용 IP 주소 요청](#request-a-public-ip-address)을 계속하고 단계를 따릅니다.

## 다음 단계

가상 네트워크에 가상 컴퓨터를 추가할 수 있습니다. [가상 컴퓨터를 만듭니다](../virtual-machines/virtual-machines-windows-tutorial.md).

<!---HONumber=August15_HO9-->