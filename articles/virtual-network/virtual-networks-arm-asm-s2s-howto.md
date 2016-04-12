<properties 
   pageTitle="Azure에서 클래식 VNet을 ARM VNet에 연결하는 방법"
   description="클래식 VNet과 새 VNet 간의 VPN 연결을 만드는 방법에 대해 알아봅니다."
   services="virtual-network"
   documentationCenter="na"
   authors="telmosampaio"
   manager="carmonm"
   editor="tysonn" />
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="03/22/2016"
   ms.author="telmos" />

# 새 VNet에 클래식 VNet 연결

Azure에는 현재 Azure 서비스 관리자(클래식이라고 함)와 Azure 리소스 관리자(ARM)라는 두 가지 관리 모드가 있습니다. 일정 기간 동안 Azure를 사용한 경우 Azure VM 및 인스턴스 역할이 클래식 VNet에서 실행되고 있을 것입니다. 또한 이후의 VM 및 역할 인스턴스는 ARM에서 만들 VNet에서 실행되고 있을 것입니다.

이러한 경우 새 인프라에서 클래식 리소스와 통신할 수 있는지 확인할 수 있습니다. 두 VNet 간의 VPN 연결을 만들면 됩니다.

이 문서에는 클래식 VNet과 ARM VNet 간의 사이트 간(S2S) VPN 연결을 만드는 방법에 대해 알아봅니다.

>[AZURE.NOTE] 이 문서에서는 클래식 VNet과 ARM VNet이 이미 있으며, 클래식 VNet에 대한 S2S VPN 연결 구성에 익숙한 것으로 가정합니다. 클래식 VNet과 ARM VNet 간의 S2S VPN 연결에 대한 자세한 종단 간 솔루션은 [솔루션 가이드 - S2S VPN을 사용하여 클래식 VNet과 ARM VNet 연결](virtual-networks-arm-asm-s2s.md)을 참조하세요.

아래 Azure 게이트웨이를 사용하여 클래식 VNet과 ARM VNet 간의 S2S VPN 연결을 만들기 위해 수행해야 하는 작업에 대한 개요를 볼 수 있습니다.

1 - [클래식 VNet에 대한 VPN 게이트웨이 만들기](#Step-1:-Create-a-VPN-gateway-for-the-classic-VNet)

2 - [ARM VNet에 대한 VPN 게이트웨이 만들기](#Step-2:-Create-a-VPN-gateway-for-the-ARM-VNet)

3 - [게이트웨이 간의 연결 만들기](#Step-3:-Create-a-connection-between-the-gateways)

## 1단계: 클래식 VNet에 대한 VPN 게이트웨이 만들기

클래식 VNet에 대한 VPN 게이트웨이를 만들려면 아래 지침을 따르세요.

1. https://manage.windowsazure.com에서 클래식 포털을 열고 필요한 경우 자격 증명을 입력합니다.
2. 화면의 왼쪽 아래에서 **새로 만들기** 단추를 클릭하고 **네트워크 서비스**를 클릭한 다음 **가상 네트워크**를 클릭하고 **로컬 네트워크 추가**를 클릭합니다.
3. **로컬 네트워크 세부 정보 지정** 창에서 연결할 ARM VNet의 이름을 입력하고 오른쪽 아래에서 화살표 단추를 클릭합니다.
3. 주소 공간에서 **시작 IP** 텍스트 상자에 연결할 ARM VNet에 대 한 네트워크 접두사를 입력합니다. 
4. **CIDR(주소 수)** 드롭다운에서 연결할 ARM VNet에서 사용되는 CIDR 블록의 네트워크 부분에 사용되는 비트 수를 선택합니다.
5. **VPN 장치 IP 주소(선택 사항)**에 유효한 공용 IP 주소를 입력합니다. 나중에 이 IP 주소를 변경합니다. 그런 다음 화면의 오른쪽 아래에 있는 확인 표시 단추를 클릭합니다. 아래 그림에서는 이 페이지에 대한 샘플 설정을 보여 줍니다.

	![로컬 네트워크 설정](..\virtual-network\media\virtual-networks-arm-asm-s2s-howto\figurex1.png)

5. **네트워크** 페이지에서 **가상 네트워크**를 클릭하고 클래식 VNet을 클릭한 다음 **구성**을 클릭합니다.
6. **사이트 간 연결**에서 **로컬 네트워크에 연결** 확인란을 선택합니다.
7. **로컬 네트워크** 드롭다운의 사용 가능한 네트워크 목록에서 4단계에서 만든 로컬 네트워크를 선택하고 **저장**을 클릭합니다.
8. 설정이 저장되면 페이지 아래에서 **대시보드**를 클릭하고 **게이트웨이 만들기**를 클릭한 다음 **동적 라우팅**을 클릭하고 **예**를 클릭합니다.
9. 게이트웨이가 만들어질 때까지 기다린 후 해당 공용 IP 주소를 복사합니다. 이 주소는 ARM VNet에서 게이트웨이를 설정하는 데 필요합니다.

## 2단계: ARM VNet에 대한 VPN 게이트웨이 만들기

ARM VNet에 대한 VPN 게이트웨이를 만들려면 아래 지침을 따르세요.

1. PowerShell 콘솔에서 아래 명령을 실행하여 로컬 네트워크를 만듭니다. 로컬 네트워크에서는 연결할 클래식 VNet의 CIDR 블록과 위의 1단계에서 만든 게이트웨이의 공용 IP 주소를 사용해야 합니다.

		New-AzureRmLocalNetworkGateway -Name VNetClassicNetwork `
			-Location "East US" -AddressPrefix "10.0.0.0/20" `
			-GatewayIpAddress "168.62.190.190" -ResourceGroupName RG1

3. 아래 명령을 실행하여 게이트웨이의 공용 IP 주소를 만듭니다.

		$ipaddress = New-AzureRmPublicIpAddress -Name gatewaypubIP`
			-ResourceGroupName RG1 -Location "East US" `
			-AllocationMethod Dynamic

4. 아래 명령을 실행하여 게이트웨이에 사용되는 서브넷을 검색합니다.

		$subnet = Get-AzureRmVirtualNetworkSubnetConfig -Name GatewaySubnet `
			-VirtualNetwork (Get-AzureVirtualNetwork -Name VNetARM -ResourceGroupName RG1) 

	>[AZURE.IMPORTANT] 게이트웨이 서브넷은 이미 존재해야 하며 이름이 GatewaySubnet이어야 합니다.

5. 아래 명령을 실행하여 게이트웨이에 대한 IP 구성 개체를 만듭니다. 게이트웨이 서브넷의 ID를 확인합니다. 이 서브넷이 VNet에 있어야 합니다.

		$ipconfig = New-AzureRmVirtualNetworkGatewayIpConfig `
			-Name ipconfig -PrivateIpAddress 10.1.2.4 `
			-SubnetId $subnet.id -PublicIpAddressId $ipaddress.id

	>[AZURE.IMPORTANT] *SubnetId* 및 *PublicIpAddressId* 매개 변수는 각각 서브넷 및 IP 주소 개체에서 id 속성이 전달되어야 합니다. 간단한 문자열을 사용할 수 없습니다.
	
5. 아래 명령을 실행하여 ARM VNet 게이트웨이를 만듭니다.

		New-AzureRmVirtualNetworkGateway -Name v1v2Gateway -ResourceGroupName RG1 `
			-Location "East US" -GatewayType Vpn -IpConfigurations $ipconfig `
			-EnableBgp $false -VpnType RouteBased

6. VPN 게이트웨이를 만든 후 아래 명령을 실행하여 해당 공용 IP 주소를 검색합니다. IP 주소를 복사합니다. 클래식 VNet에 대한 로컬 네트워크를 구성하는 데 필요합니다.

		Get-AzureRmPublicIpAddress -Name gatewaypubIP -ResourceGroupName RG1

## 3단계: 게이트웨이 간의 연결 만들기

1. https://manage.windowsazure.com에서 클래식 포털을 열고 필요한 경우 자격 증명을 입력합니다.
2. 클래식 포털에서 아래로 스크롤하여 **네트워크**를 클릭하고 **로컬 네트워크**를 클릭한 다음 연결할 ARM VNet을 클릭하고 **편집** 단추를 클릭합니다.
3. **VPN 장치 IP 주소(선택 사항)**에 위 2단계에서 검색한 ARM VNet 게이트웨이의 IP 주소를 입력하고 오른쪽 아래에 있는 오른쪽 화살표를 클릭한 다음 확인 표시 단추를 클릭합니다.
4. PowerShell 콘솔에서 아래 명령을 실행하여 공유 키를 설정합니다. VNet의 이름을 사용자 고유의 VNet 이름으로 변경해야 합니다.

		Set-AzureVNetGatewayKey -VNetName VNetClassic `
			-LocalNetworkSiteName VNetARM -SharedKey abc123

7. 아래 명령을 실행하여 VPN 연결을 만듭니다.

		$vnet01gateway = Get-AzureRmLocalNetworkGateway -Name VNetClassic -ResourceGroupName RG1
		$vnet02gateway = Get-AzureRmVirtualNetworkGateway -Name v1v2Gateway -ResourceGroupName RG1
		
		New-AzureRmVirtualNetworkGatewayConnection -Name arm-asm-s2s-connection `
			-ResourceGroupName RG1 -Location "East US" -VirtualNetworkGateway1 $vnet02gateway `
			-LocalNetworkGateway2 $vnet01gateway -ConnectionType IPsec `
			-RoutingWeight 10 -SharedKey 'abc123'

## 다음 단계

- [ARM에 대한 NRP(네트워크 리소스 공급자)](resource-groups-networking.md)에 대해 자세히 알아봅니다.
- [S2S VPN을 사용하여 클래식 VNet을 ARM VNet에 연결하는 종단 간 솔루션](virtual-networks-arm-asm-s2s.md)을 만듭니다.

<!---HONumber=AcomDC_0330_2016-->