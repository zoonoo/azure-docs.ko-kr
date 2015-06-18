<properties 
   pageTitle="여러 NIC를 사용하여 VM 만들기"
   description="여러 nic가 있는 vm을 만드는 방법"
   services="virtual-network, virtual-machines"
   documentationCenter="na"
   authors="telmosampaio"
   manager="adinah"
   editor="tysonn" />
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="04/30/2015"
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
|A0\ 매우 작음|1|
|A1\작음|1|
|A2\중간|1|
|A3\큼|2|
|A4\매우 큼|4|
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

- **들어오는 트래픽** 대상은 서브넷을 통한 논의 중인 NIC 흐름으로, 서브넷의 NSG 규칙을 트리거하며 NIC로 전달되기 전에 NIC의 NSG 규칙을 트리거합니다.- **들어오는 트래픽** 소스는 처음 NIC를 통한 논의 중인 NIC 흐름으로, NIC의 NSG 규칙을 트리거하며 서브넷으로 전달되기 전에 서브넷의 NSG 규칙을 트리거합니다. 

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


예제에서 PowerShell 명령을 실행하려면 다음 prerequisitesbefore를 시도해야 합니다.

- Azure 구독.
- 구성된 가상 네트워크입니다. 자세한 내용은 [가상 네트워크 개요(영문)](https://msdn.microsoft.com/library/azure/jj156007.aspx)를 참조하세요.
- 최신 버전의 Azure PowerShell이 다운로드되어 설치됩니다. [Azure PowerShell 설치 및 구성 방법](../install-configure-powershell)을 참조하세요.

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

		Set-AzureSubnet -SubnetNames "Frontend" -VM $vm Set-AzureStaticVNetIP  `
			-IPAddress "10.1.0.100" -VM $vm

1. 가상 네트워크에 VM 만들기

		New-AzureVM -ServiceName "MultiNIC-CS" –VNetName "MultiNIC-VNet" –VMs $vm

>[AZURE.NOTE]여기서 지정하는 VNet(전제 조건에서 설명한 대로)은 이미 존재해야 합니다. 다음 예제에서는 “MultiNIC-VNet”이라는 가상 네트워크를 지정합니다.

## 참고 항목

[가상 네트워크 개요](https://msdn.microsoft.com/library/azure/jj156007.aspx)

[가상 네트워크 구성 작업](https://msdn.microsoft.com/library/azure/jj156206.aspx)

[블로그 게시물 - 여러 VM NIC 및 Azure에서 VNet 어플라이언스](../multiple-vm-nics-and-network-virtual-appliances-in-azure)

<!---HONumber=58--> 