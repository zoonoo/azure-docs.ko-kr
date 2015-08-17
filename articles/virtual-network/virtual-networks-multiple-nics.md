<properties 
   pageTitle="여러 NIC를 사용하여 VM 만들기"
   description="여러 NIC가 있는 VM을 만들고 구성하는 방법 알아보기"
   services="virtual-network, virtual-machines"
   documentationCenter="na"
   authors="telmosampaio"
   manager="carolz"
   editor="tysonn" />
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/02/2015"
   ms.author="telmos" />

# 여러 NIC를 사용하여 VM 만들기

다중 NIC 기능을 사용하여 Azure 가상 컴퓨터(VM)에서 여러 가상 네트워크 인터페이스 카드(NIC)를 만들어 관리할 수 있습니다. 다중 NIC는 응용 프로그램 전달 및 WAN 최적화 솔루션과 같은 여러 네트워크 가상 장비를 위한 요구 사항입니다. 다중 NIC에는 NIC 프런트엔드 및 백엔드 NIC 간의 격리, 또는 관리 평면 트래픽에서 데이터 평면 트래픽 분리를 포함한 추가 네트워크 트래픽 관리 기능도 제공합니다.

![VM에 대한 다중 NIC](./media/virtual-networks-multiple-nics/IC757773.png)

위의 그림에서는 각각 다른 서브넷에 연결된 3개의 NIC를 사용하여 VM을 보여줍니다.

## 요구 사항 및 제약 조건

이때 다중 NIC에는 다음 요구 사항 및 제약 조건이 있습니다.

- 다중 NIC VM은 Azure VNet(가상 네트워크)에서 생성되어야 합니다. 비 VNet VM은 지원되지 않습니다. 
- 단일 클라우드 서비스 내에서 다음 설정이 허용됩니다. 
	- 해당 클라우드 서비스의 모든 VM에 다중 NIC를 사용하도록 설정해야 합니다. 또는 
	- 해당 클라우드 모든 VM에는 각각 단일 NIC가 있어야 합니다. 

>[AZURE.IMPORTANT]다중 NIC VM이 이미 단일 NIC VM(또는 그 반대로)을 포함하는 배포 (클라우드 서비스)를 추가하려는 경우, 다음과 같은 오류가 나타납니다. 보조 네트워크 인터페이스가 있는 가상 컴퓨터 및 보조 네트워크가 없는 가상 컴퓨터는 동일한 배포에서 지원되지 않으며, 보조 네트워크 인터페이스가 없는 가상 컴퓨터를 업데이트하여 보조 네트워크 인터페이스가 있도록 할 수 없습니다. 그 반대로 마찬가지입니다.
 
- 인터넷 연결 VIP가 "기본" NIC 에서만 지원됩니다. 기본 NIC의 IP에 하나의 VIP만 있습니다. 
- 이때 인스턴스 수준 공용 IP 주소는 다중 NIC VM에 대해 지원되지 않습니다. 
- VM 내에서 Nic의 순서가 임의로 지정되며, Azure 인프라 업데이트에서 변경할 수도 있습니다. 그러나 IP 주소 및 해당 이더넷 MAC 주소는 동일하게 유지됩니다. 예를 들어, **Eth1**의 IP 주소는 10.1.0.100이며 MAC 주소는 00-0D-3A-B0-39-0D입니다. Azure 인프라 업데이트 및 다시 부팅 후, Eth2로 변경될 수 있지만 IP 및 MAC 페어링은 그대로 유지됩니다. 다시 시작은 고객이 시작한 것이며 NIC 순서는 그대로 유지됩니다. 
- 각 VM에서 각 NIC에 대한 주소는 서브넷에 있어야 하며, 단일 VM의 여러 NIC는 동일한 서브넷에 있는 주소에 각각 할당됩니다. 
- VM 크기는 VM에 대해 만들 수 있는 NIC의 수를 결정합니다. 아래 표는 VM의 크기에 해당하는 NIC의 수를 표시합니다. 

|VM 크기(표준 SKU)|NIC(VM당 허용되는 최대)|
|---|---|
|모든 기본 크기|1|
|A0\\ 매우 작음|1|
|A1\\작음|1|
|A2\\중간|1|
|A3\\큼|2|
|A4\\매우 큼|4|
|A5|1|
|A6|2|
|A7|4|
|A8|2|
|A9|4|
|A10|2|
|A11|4|
|D1|1|
|D2|2|
|D3|4|
|D4|8|
|D11|2|
|D12|4|
|D13|8|
|D14|16|
|DS1|1|
|DS2|2|
|DS3|4|
|DS4|8|
|DS11|2|
|DS12|4|
|DS13|8|
|DS14|16|
|G1|1|
|G2|2|
|G3|4|
|G4|8|
|G5|16|
|기타 모든 크기|1|

## 네트워크 보안 그룹
VM에서 모든 NIC는 다중 NIC가 활성화되어 있는 VM의 모든 NIC를 포함하여 네트워크 보안 그룹(NSG)과 연결 될 수 있습니다. NIC가 서브넷이 NSG와 연관된 서브넷 내에서 할당되면, 서브넷의 NSG의 규칙도 해당 NIC에 적용됩니다. 서브넷을 NSG와 연결하는 것 외에도 NSG와 NIC를 연결할 수도 있습니다.

서브넷이 해당 서브넷이 NSG와 개별적으로 연관된 NIC 및 NSG와 연결된 경우, NIC 안팎으로 전달되는 트래픽의 방향에 따라 연관된 NSG 규칙은 "**흐름 순서**"에 적용됩니다.

- ****들어오는 트래픽** 대상은 서브넷을 통한 논의 중인 NIC 흐름으로, 서브넷의 NSG 규칙을 트리거하며 NIC로 전달되기 전에 NIC의 NSG 규칙을 트리거합니다.
- **들어오는 트래픽** 소스는 처음 NIC를 통한 논의 중인 NIC 흐름으로, NIC의 NSG 규칙을 트리거하며 서브넷으로 전달되기 전에 서브넷의 NSG 규칙을 트리거합니다. 

위의 그림은 NSG 규칙 응용 프로그램이 트래픽 흐름을 기반으로 흐름(VM에서 서브넷으로 또는 서브넷에서 VM으로)을 나타냅니다.

## 다중 NIC VM을 구성하는 방법

아래의 지침에 따라 하나의 기본 NIC 및 두 개의 추가 NIC를 포함한 3개의 NIC를 만듭니다. 구성 단계는 아래 서비스 구성 파일 조각에 따라 구성될 VM을 만듭니다.

	<VirtualNetworkSite name="MultiNIC-VNet" Location="North Europe">
	<AddressSpace>
	  <AddressPrefix>10.1.0.0/16</AddressPrefix>
	    </AddressSpace>
	    <Subnets>
	      <Subnet name="Frontend">
	        <AddressPrefix>10.1.0.0/24</AddressPrefix>
	      </Subnet>
	      <Subnet name="Midtier">
	        <AddressPrefix>10.1.1.0/24</AddressPrefix>
	      </Subnet>
	      <Subnet name="Backend">
	        <AddressPrefix>10.1.2.0/23</AddressPrefix>
	      </Subnet>
	      <Subnet name="GatewaySubnet">
	        <AddressPrefix>10.1.200.0/28</AddressPrefix>
	      </Subnet>
	    </Subnets>
	… Skip over the remainder section …
	</VirtualNetworkSite>


예제의 PowerShell 명령 실행을 시도하기 전에 다음 필수 조건에 부합해야 합니다.

- Azure 구독.
- 구성된 가상 네트워크입니다. 자세한 내용은 [가상 네트워크 개요(영문)](https://msdn.microsoft.com/library/azure/jj156007.aspx)를 참조하세요.
- 최신 버전의 Azure PowerShell이 다운로드되어 설치됩니다. [Azure PowerShell 설치 및 구성 방법](../install-configure-powershell)을 참조하세요.

여러 NIC가 있는 VM을 만들려면 다음 단계를 수행합니다.

1. Azure VM 이미지 갤러리에서 VM 이미지를 선택합니다. 이미지를 자주 변경하고 지역에 따라 사용할 수 있습니다. 아래 예제에서 지정된 이미지는 변경되거나 사용자 지역에 없을 수 있으므로, 필요한 이미지를 지정해야 합니다. 
	    
		$image = Get-AzureVMImage `
	    	-ImageName "a699494373c04fc0bc8f2bb1389d6106__Windows-Server-2012-R2-201410.01-en.us-127GB.vhd"

1. VM 구성을 만듭니다.

		$vm = New-AzureVMConfig -Name "MultiNicVM" -InstanceSize "ExtraLarge" `
			-Image $image.ImageName –AvailabilitySetName "MyAVSet"

1. 기본 관리자 로그인을 만듭니다.

		Add-AzureProvisioningConfig –VM $vm -Windows -AdminUserName "<YourAdminUID>" `
			-Password "<YourAdminPassword>"

1. VM 구성에 추가 Nic를 추가합니다.

		Add-AzureNetworkInterfaceConfig -Name "Ethernet1" `
			-SubnetName "Midtier" -StaticVNetIPAddress "10.1.1.111" -VM $vm 
		Add-AzureNetworkInterfaceConfig -Name "Ethernet2" `
			-SubnetName "Backend" -StaticVNetIPAddress "10.1.2.222" -VM $vm

1. 기본 NIC에 대한 서브넷 및 IP 주소를 지정합니다.

		Set-AzureSubnet -SubnetNames "Frontend" -VM $vm 
		Set-AzureStaticVNetIP -IPAddress "10.1.0.100" -VM $vm

1. 가상 네트워크에 VM 만들기

		New-AzureVM -ServiceName "MultiNIC-CS" –VNetName "MultiNIC-VNet" –VMs $vm

>[AZURE.NOTE]여기서 지정하는 VNet(전제 조건에서 설명한 대로)은 이미 존재해야 합니다. 다음 예제에서는 **MultiNIC-VNet**으로 명명된 가상 네트워크를 지정합니다.

## 다른 서브넷에 대한 보조 NIC 액세스

Azure의 현재 모델은 가상 컴퓨터에서 모든 NIC가 기본 게이트웨이를 사용하여 설정됩니다. 따라서 NIC가 그 서브넷 외부 IP 주소와 통신할 수 있습니다. Linux와 같은 취약한 호스트 라우팅 모델을 사용하는 운영 체제에서는 송/수신 트래픽이 다른 NIC를 사용하는 경우 인터넷 연결이 중단됩니다.

이 문제를 해결하기 위해 Azure는 보조 NIC에서 기본 게이트웨이를 제거하는 플랫폼을 2015년 7월의 첫 주에 업데이트할 예정입니다. 이는 기존 가상 컴퓨터를 다시 부팅하기 전까지는 영향을 미치지 않습니다. 다시 부팅한 후 새 설정이 적용되며, 이때 보조 NIC의 트래픽 흐름이 동일한 서브넷에 있도록 제한됩니다. 보조 NIC가 자체 서브넷 외부와 통신할 수 있도록 설정하려는 경우, 아래 설명된 게이트웨이를 구성하도록 라우팅 테이블에 항목을 추가해야 합니다.

### Windows VM 구성

다음과 같은 두 개의 NIC가 있는 Windows VM이 있다고 가정합니다.

- 주 NIC IP 주소: 192.168.1.4
- 보조 NIC IP 주소: 192.168.2.5

이 VM에 대한 IPv4 경로 테이블은 다음과 같습니다.

	IPv4 Route Table
	===========================================================================
	Active Routes:
	Network Destination        Netmask          Gateway       Interface  Metric
	          0.0.0.0          0.0.0.0      192.168.1.1      192.168.1.4      5
	        127.0.0.0        255.0.0.0         On-link         127.0.0.1    306
	        127.0.0.1  255.255.255.255         On-link         127.0.0.1    306
	  127.255.255.255  255.255.255.255         On-link         127.0.0.1    306
	    168.63.129.16  255.255.255.255      192.168.1.1      192.168.1.4      6
	      192.168.1.0    255.255.255.0         On-link       192.168.1.4    261
	      192.168.1.4  255.255.255.255         On-link       192.168.1.4    261
	    192.168.1.255  255.255.255.255         On-link       192.168.1.4    261
	      192.168.2.0    255.255.255.0         On-link       192.168.2.5    261
	      192.168.2.5  255.255.255.255         On-link       192.168.2.5    261
	    192.168.2.255  255.255.255.255         On-link       192.168.2.5    261
	        224.0.0.0        240.0.0.0         On-link         127.0.0.1    306
	        224.0.0.0        240.0.0.0         On-link       192.168.1.4    261
	        224.0.0.0        240.0.0.0         On-link       192.168.2.5    261
	  255.255.255.255  255.255.255.255         On-link         127.0.0.1    306
	  255.255.255.255  255.255.255.255         On-link       192.168.1.4    261
	  255.255.255.255  255.255.255.255         On-link       192.168.2.5    261
	===========================================================================

기본 경로(0.0.0.0)는 주 NIC에서만 사용할 수 있음에 주의합니다. 다음과 같이 보조 NIC의 서브넷 외부 리소스에 액세스할 수 없습니다.

	C:\Users\Administrator>ping 192.168.1.7 -S 192.165.2.5
	 
	Pinging 192.168.1.7 from 192.165.2.5 with 32 bytes of data:
	PING: transmit failed. General failure.
	PING: transmit failed. General failure.
	PING: transmit failed. General failure.
	PING: transmit failed. General failure.

보조 NIC에 기본 경로 추가하려면 다음 단계를 수행합니다.

1. 명령 프롬프트에서 아래 명령을 실행하여 보조 NIC에 대한 인덱스 번호를 식별합니다.

		C:\Users\Administrator>route print
		===========================================================================
		Interface List
		 29...00 15 17 d9 b1 6d ......Microsoft Virtual Machine Bus Network Adapter #16
		 27...00 15 17 d9 b1 41 ......Microsoft Virtual Machine Bus Network Adapter #14
		  1...........................Software Loopback Interface 1
		 14...00 00 00 00 00 00 00 e0 Teredo Tunneling Pseudo-Interface
		 20...00 00 00 00 00 00 00 e0 Microsoft ISATAP Adapter #2
		===========================================================================

2. 테이블에서 27 인덱스(이 예에서)가 있는 두 번째 항목에 주의합니다.
3. 명령 프롬프트에서 다음과 같이 **route add** 명령을 실행합니다. 이 예제에서는 192.168.2.1을 보조 NIC에 대한 기본 게이트웨이로 지정합니다.

		route ADD -p 0.0.0.0 MASK 0.0.0.0 192.168.2.1 METRIC 5000 IF 27

4. 연결을 테스트하려면 명령 프롬프트로 돌아가서 다음 예와 같이 보조 NIC에서 다른 서브넷을 ping합니다.

		C:\Users\Administrator>ping 192.168.1.7 -S 192.165.2.5
		 
		Reply from 192.168.1.7: bytes=32 time<1ms TTL=128
		Reply from 192.168.1.7: bytes=32 time<1ms TTL=128
		Reply from 192.168.1.7: bytes=32 time=2ms TTL=128
		Reply from 192.168.1.7: bytes=32 time<1ms TTL=128

5. 또한 다음과 같이 경로 테이블을 확인하여 새로 추가된 경로를 확인할 수 있습니다.

		C:\Users\Administrator>route print

		...

		IPv4 Route Table
		===========================================================================
		Active Routes:
		Network Destination        Netmask          Gateway       Interface  Metric
		          0.0.0.0          0.0.0.0      192.168.1.1      192.168.1.4      5
		          0.0.0.0          0.0.0.0      192.168.2.1      192.168.2.5   5005
		        127.0.0.0        255.0.0.0         On-link         127.0.0.1    306

### Linux VM 구성

Linux VM의 경우, 기본 동작에서 취약한 호스트 라우팅을 사용하므로 보조 NIC는 동일한 서브넷 내 트래픽 흐름으로만 제한하는 것이 좋습니다. 그러나 특정 시나리오에 서브넷 외부 연결을 요청하는 경우, 사용자는 정책 기반 라우팅을 사용하도록 설정하여 송/수신 트래픽이 동일한 NIC를 사용하게 해야 합니다.

<!---HONumber=August15_HO6-->