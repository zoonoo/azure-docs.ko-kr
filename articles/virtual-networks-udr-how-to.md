<properties 
   pageTitle="Azure에서 경로를 만들고 IP 전달을 사용하도록 설정하는 방법"
   description="Azure에서 경로를 만들고 IP 전달을 사용하도록 설정하는 방법"
   services="virtual-networks"
   documentationCenter="na"
   authors="telmosampaio"
   manager="adinah"
   editor="tysonn" /> <tags 
   ms.service="virtual-networks"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="04/22/2015"
   ms.author="telmos" />

# Azure에서 경로를 만들고 IP 전달을 사용하도록 설정하는 방법
Azure의 가상 어플라이언스를 사용하여 Azure 가상 네트워크에서 트래픽을 처리할 수 있습니다. 그러나 가상 네트워크의 VM 및 클라우드 서비스에서 원하는 패킷 대상 대신 사용자의 가상 어플라이언스로 패킷을 보내도록 허용하는 경로를 만들어야 합니다. 또한 실제 가상 어플라이언스 VM으로 주소가 지정되지 않은 패킷을 받고 전달할 수 있도록 가상 어플라이언스 VM에서 IP 전달을 사용하도록 설정해야 합니다.

##경로를 관리하는 방법
PowerShell을 사용하여 Azure에서 경로를 추가, 제거 및 변경할 수 있습니다. 경로를 만들려면 먼저 경로를 호스트할 경로 테이블을 만들어야 합니다.

### 경로 테이블을 만드는 방법
*FrontEndSubnetRouteTable*이라는 경로 테이블을 만들려면 다음 PowerShell 명령을 실행합니다.

	New-AzureRouteTable -Name `FrontEndSubnetRouteTable `
	-Location `usnorth `
	-Label "Route table for frontend subnet"

### 경로 테이블에 경로를 추가하는 방법
위에서 만든 경로 테이블에 *10.1.1.10*을 *10.2.0.0/16* 서브넷의 다음 홉으로 설정하는 경로를 추가하려면 다음 PowerShell 명령을 실행합니다.

	Set-AzureRoute -RouteTableName `FrontEndSubnetRouteTable `
	-RouteName FirewallRoute -`AddressPrefix 10.2.0.0/16 `
	-NextHopType `VirtualAppliance `
	-NextHopIpAddress 10.1.1.10

### 서브넷에 경로를 연결하는 방법
경로 테이블을 사용하려면 하나 이상의 서브넷에 연결해야 합니다. *FrontEndSubnetRouteTable* 경로 테이블을 가상 네트워크 *ProductionVnet*의 *FrontEndSubnet* 서브넷에 연결하려면 다음 PowerShell 명령을 실행합니다.

	Set-AzureSubnetRouteTable -`VNetName ProductionVnet `
	-SubnetName `FrontEndSubnet `
	-RouteTableName FrontEndSubnetRouteTable

### VM에서 적용된 경로를 확인하는 방법
Azure를 쿼리하여 특정 VM 또는 역할 인스턴스에 대해 적용된 실제 경로를 확인할 수 있습니다. 표시되는 경로에는 Azure에서 제공하는 기본 경로 및 VPN 게이트웨이에서 보급하는 경로가 포함됩니다. 표시되는 경로는 800개로 제한됩니다.

*FirewallAppliance1*이라는 VM에서 기본 NIC에 연결된 경로를 보려면 다음 PowerShell 명령을 실행합니다.

	Get-AzureVM -Name `FirewallAppliance1 `
	| Get-AzureEffectiveRouteTable

*FirewallAppliance1*이라는 VM에서 *backendnic*라는 보조 NIC에 연결된 경로를 보려면 다음 PowerShell 명령을 실행합니다.

	Get-AzureVM -Name `FirewallAppliance1 `
	| Get-AzureEffectiveRouteTable -NetworkInterfaceName backendnic

*myservice*라는 클라우드 서비스의 일부인 *myRole*이라는 역할 인스턴스에서 기본 NIC에 연결된 경로를 보려면 다음 PowerShell 명령을 실행합니다.

	Get-AzureEffectiveRouteTable -ServiceName `myservice `
	-RoleInstanceName myRole

## IP 전달을 관리하는 방법
위에서 설명한 것처럼 가상 어플라이언스 역할을 할 모든 VM 또는 역할 인스턴스에서 IP 전달을 사용하도록 설정해야 합니다.

### IP 전달을 사용하도록 설정하는 방법
*FirewallAppliance1*이라는 VM에서 IP 전달을 사용하도록 설정하려면 다음 PowerShell 명령을 실행합니다.

	Get-AzureVM -Name `FirewallAppliance1 `
	| Set-AzureIPForwarding -Enable

*myService*라는 클라우드 서비스의 역할 인스턴스 *FirewallAppliance1*에서 IP 전달을 사용하도록 설정하려면 다음 PowerShell 명령을 실행합니다.

	Set-AzureIPForwarding -ServiceName `myService `
	-RoleName FirewallAppliance1 -Enable

### IP 전달을 사용하지 않도록 설정하는 방법
*FirewallAppliance1*이라는 VM에서 IP 전달을 사용하지 않도록 설정하려면 다음 PowerShell 명령을 실행합니다.

	Get-AzureVM -Name `FirewallAppliance1 `
	| Set-AzureIPForwarding -Disable

*myService*라는 클라우드 서비스의 역할 인스턴스 *FirewallAppliance1*에서 IP 전달을 사용하지 않도록 설정하려면 다음 PowerShell 명령을 실행합니다.

	Set-AzureIPForwarding -ServiceName `myService `
	-RoleName FirewallAppliance1 -Disable

### IP 전달 상태를 확인하는 방법
*FirewallAppliance1*이라는 VM에서 IP 전달 상태를 확인하려면 다음 PowerShell 명령을 실행합니다.

	Get-AzureVM -Name `FirewallAppliance1 `
	| Get-AzureIPForwarding

## 참고 항목

[사용자 정의 경로 및 IP 전달 개요](../virtual-networks-udr-overview)

[ILIP\(인스턴스 수준 공용 IP\)](../virtual-networks-instance-level-public-ip)

[가상 네트워크 개요](https://msdn.microsoft.com/library/azure/jj156007.aspx) 

<!--HONumber=52-->
