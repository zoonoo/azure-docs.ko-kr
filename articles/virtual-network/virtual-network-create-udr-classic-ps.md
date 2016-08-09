<properties 
   pageTitle="클래식 배포 모델에서 PowerShell을 사용하여 라우팅 제어 및 가상 어플라이언스 사용 | Microsoft Azure"
   description="클래식 배포 모델에서 PowerShell을 사용하여 VNet에서 라우팅을 제어하는 방법 알아보기"
   services="virtual-network"
   documentationCenter="na"
   authors="telmosampaio"
   manager="carmonm"
   editor=""
   tags="azure-service-management"
/>
<tags  
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/02/2016"
   ms.author="telmos" />

#PowerShell을 사용하여 라우팅 제어 및 가상 어플라이언스(클래식) 사용

[AZURE.INCLUDE [virtual-network-create-udr-classic-selectors-include.md](../../includes/virtual-network-create-udr-classic-selectors-include.md)]

[AZURE.INCLUDE [virtual-network-create-udr-intro-include.md](../../includes/virtual-network-create-udr-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]

이 문서에서는 클래식 배포 모델에 대해 설명합니다.

[AZURE.INCLUDE [virtual-network-create-udr-scenario-include.md](../../includes/virtual-network-create-udr-scenario-include.md)]

아래 샘플 Azure PowerShell 명령에는 위의 시나리오를 기반으로 이미 만들어져 있는 단순한 환경이 필요합니다. 이 문서에 표시된 대로 명령을 실행하려는 경우 먼저 [PowerShell을 사용하여 VNet(클래식) 만들기](virtual-networks-create-vnet-classic-netcfg-ps.md)에 표시된 환경을 구축합니다.

[AZURE.INCLUDE [azure-ps-prerequisites-include.md](../../includes/azure-ps-prerequisites-include.md)]

## 프런트 엔드 서브넷에 대한 UDR 만들기
위의 시나리오에 따라 필요한 경로 테이블 및 경로를 만들려면 다음 단계를 수행합니다.

3. **`New-AzureRouteTable`** cmdlet을 실행하여 프런트 엔드 서브넷에 대한 경로 테이블을 만듭니다.

		New-AzureRouteTable -Name UDR-FrontEnd `
			-Location uswest `
			-Label "Route table for front end subnet"

	출력:

		Name         Location   Label                          
		----         --------   -----                          
		UDR-FrontEnd West US    Route table for front end subnet

4. **`Set-AzureRoute`** cmdlet을 실행하여 위에서 만든 경로 테이블에 경로를 만들고 백 엔드 서브넷(192.168.2.0/24)으로 보내진 모든 트래픽을 **FW1** VM(192.168.0.4)으로 보냅니다.
	
		Get-AzureRouteTable UDR-FrontEnd `
			|Set-AzureRoute -RouteName RouteToBackEnd -AddressPrefix 192.168.2.0/24 `
			-NextHopType VirtualAppliance `
			-NextHopIpAddress 192.168.0.4

	출력:

		Name     : UDR-FrontEnd
		Location : West US
		Label    : Route table for frontend subnet
		Routes   : 
		           Name                 Address Prefix    Next hop type        Next hop IP address
		           ----                 --------------    -------------        -------------------
		           RouteToBackEnd       192.168.2.0/24    VirtualAppliance     192.168.0.4  

5. **`Set-AzureSubnetRouteTable`** cmdlet을 실행하여 위에서 만든 경로 테이블을 **FrontEnd** 서브넷에 연결합니다.

		Set-AzureSubnetRouteTable -VirtualNetworkName TestVNet `
			-SubnetName FrontEnd `
			-RouteTableName UDR-FrontEnd
 
## 백 엔드 서브넷에 대한 UDR 만들기
위의 시나리오에 따라 백 엔드 서브넷에 필요한 경로 테이블 및 경로를 만들려면 다음 단계를 수행합니다.

3. **`New-AzureRouteTable`** cmdlet을 실행하여 백 엔드 서브넷에 대한 경로 테이블을 만듭니다.

		New-AzureRouteTable -Name UDR-BackEnd `
			-Location uswest `
			-Label "Route table for back end subnet"

4. **`Set-AzureRoute`** cmdlet을 실행하여 위에서 만든 경로 테이블에 경로를 만들고 프런트 엔드 서브넷(192.168.1.0/24)으로 보내진 모든 트래픽을 **FW1** VM(192.168.0.4)으로 보냅니다.

		Get-AzureRouteTable UDR-BackEnd `
			|Set-AzureRoute -RouteName RouteToFrontEnd -AddressPrefix 192.168.1.0/24 `
			-NextHopType VirtualAppliance `
			-NextHopIpAddress 192.168.0.4

5. **`Set-AzureSubnetRouteTable`** cmdlet을 실행하여 위에서 만든 경로 테이블을 **BackEnd** 서브넷에 연결합니다.

		Set-AzureSubnetRouteTable -VirtualNetworkName TestVNet `
			-SubnetName BackEnd `
			-RouteTableName UDR-BackEnd

## FW1 VM에서 IP 전달을 사용하도록 설정합니다.
FW1 VM에 IP 전달을 사용하도록 설정하려면 다음 단계를 수행합니다.

1. **`Get-AzureIPForwarding`** cmdlet을 실행하여 IP 전달 상태를 확인합니다.

		Get-AzureVM -Name FW1 -ServiceName TestRGFW `
			| Get-AzureIPForwarding

	출력:

		Disabled

2. **`Set-AzureIPForwarding`** 명령을 실행하여 *FW1* VM에 대한 IP 전달을 사용하도록 설정합니다.

		Get-AzureVM -Name FW1 -ServiceName TestRGFW `
			| Set-AzureIPForwarding -Enable

<!---HONumber=AcomDC_0720_2016-->