<properties 
	pageTitle="하이브리드 클라우드 테스트 환경 | Microsoft Azure" 
	description="IT 전문가 또는 개발 테스트용 하이브리드 클라우드 환경을 만드는 방법에 대해 알아보고 단순한 온-프레미스 네트워크까지 살펴봅니다." 
	services="virtual-machines-windows" 
	documentationCenter="" 
	authors="JoeDavies-MSFT" 
	manager="timlt" 
	editor=""
	tags="azure-resource-manager"/>

<tags 
	ms.service="virtual-machines-windows" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="Windows" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/01/2016" 
	ms.author="josephd"/>

# 테스트용 하이브리드 클라우드 환경 설정

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]클래식 배포 모델.
 
이 항목에서는 Microsoft Azure를 사용하여 테스트용 하이브리드 클라우드 환경을 만드는 과정을 안내합니다. 다음은 결과 구성입니다.

![](./media/virtual-machines-windows-ps-hybrid-cloud-test-env-base/virtual-machines-windows-ps-hybrid-cloud-test-env-base-ph5.png)

이 구성에서는 인터넷상의 현재 위치에서 실제 하이브리드 프로덕션 환경을 시뮬레이션하며, 다음으로 구성됩니다.

-  간소화된 온-프레미스 네트워크(Corpnet 서브넷).
-  Azure에서 호스트되는 크로스-프레미스 가상 네트워크(TestVNET)
-  사이트 간 VPN 연결
-  TestVNET 가상 네트워크의 보조 도메인 컨트롤러

이 구성은 다음 작업을 수행할 수 있는 기초 및 일반적인 시작 지점을 제공합니다.

-  하이브리드 클라우드 환경에서 응용 프로그램 개발 및 테스트
-  일부는 Corpnet 서브넷에 있고 일부는 TestVNET 가상 네트워크에 있는 컴퓨터의 하이브리드 클라우드 기반 IT 워크로드에 대한 테스트 구성을 만듭니다.

이 하이브리드 클라우드 테스트 환경을 설정하는 다섯 가지 주요 단계가 있습니다.

1.	Corpnet 서브넷에서 컴퓨터 구성
2.	RRAS1 구성
3.	크로스-프레미스 Azure 가상 네트워크 만들기
4.	사이트 간 VPN 연결 만들기
5.	DC2 구성 

아직 Azure 구독이 없는 경우에는 [Azure 평가판 사용](https://azure.microsoft.com/pricing/free-trial/)에서 무료 계정에 등록할 수 있습니다. MSDN 또는 Visual Studio 구독이 있는 경우에는 [Visual Studio 구독자를 위한 월간 Azure 크레딧](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)을 참조하세요.

>[AZURE.NOTE] Azure의 가상 컴퓨터 및 가상 네트워크 게이트웨이는 실행 중인 동안 지속적인 비용이 부과됩니다. Azure VPN 게이트웨이는 두 개의 Azure 가상 컴퓨터 집합으로 구현됩니다. 자세한 내용은 [가격-가상 네트워크](https://azure.microsoft.com/pricing/details/virtual-network/)를 참조하세요. VPN 게이트웨이 실행 비용을 최소화하려면 테스트 환경을 만들고 가능한 한 신속하게 필요한 테스트 및 데모를 수행합니다.

이 구성에서는 공용 IP 주소를 사용하여 인터넷에 직접 연결된 최대 네 대의 컴퓨터의 테스트 서브넷이 필요합니다. 이러한 리소스를 설정하지 않은 경우, [테스트를 위해 시뮬레이션된 하이브리드 클라우드 환경 설정](virtual-machines-windows-ps-hybrid-cloud-test-env-sim.md)을 수행할 수도 있습니다. 시뮬레이션된 하이브리드 클라우드 테스트 환경은 Azure 구독에만 필요합니다.

## 1단계: Corpnet 서브넷에서 컴퓨터 구성

[테스트 랩 가이드: Windows Server 2012 R2 기본 구성](http://www.microsoft.com/download/details.aspx?id=39638)의 "Corpnet 서브넷 구성을 위한 단계" 섹션의 지침을 사용하여 Corpnet 이라는 서브넷에서 DC1, APP1 및 CLIENT1 컴퓨터를 구성합니다. **이 서브넷은 RRAS1 컴퓨터를 통해 인터넷에 직접 연결되므로 조직 네트워크와 격리해야 합니다.**

그런 다음 CORP\\User1 자격 증명으로 DC1에 로그온합니다. 컴퓨터 및 사용자가 해당 로컬 도메인 컨트롤러를 사용하여 인증할 수 있도록 CORP 도메인을 구성하려면 관리자 수준 Windows PowerShell 명령 프롬프트에서 다음 명령을 실행합니다.

	New-ADReplicationSite -Name "TestLab" 
	New-ADReplicationSite -Name "TestVNET"
	New-ADReplicationSubnet “Name "10.0.0.0/8" “Site "TestLab"
	New-ADReplicationSubnet “Name "192.168.0.0/16" “Site "TestVNET

다음은 현재 구성입니다.

![](./media/virtual-machines-windows-ps-hybrid-cloud-test-env-base/virtual-machines-windows-ps-hybrid-cloud-test-env-base-ph1.png)
 
## 2단계: RRAS1 구성

RRAS1은 Corpnet 서브넷에 있는 컴퓨터와 TestVNET 가상 네트워크에 있는 컴퓨터 간에 트래픽 라우팅 및 VPN 장치 서비스를 제공합니다. RRAS1에는 두 개의 네트워크 어댑터가 설치되어야 합니다.

먼저 RRAS1에 운영 체제를 설치합니다.

1.	Windows Server 2012 R2 설치를 시작합니다.
2.	로컬 관리자 계정에 대한 강력한 암호를 지정하여 지침에 따라 설치를 완료합니다. 로컬 관리자 계정을 사용하여 로그온합니다.
3.	인터넷에 액세스할 수 있는 네트워크에 RRAS1을 연결하고 Windows 업데이트를 실행하여 Windows Server 2012 R2의 최신 업데이트를 설치합니다.
4.	Corpnet 서브넷에 네트워크 어댑터 하나를 연결하고 나머지 하나를 인터넷에 직접 연결합니다. RRAS1은 인터넷 방화벽 뒤에 둘 수 있지만 NAT(Network Address Translator) 뒤에 있어서는 안 됩니다.

그런 다음 RRAS1의 TCP/IP 속성을 구성합니다. ISP(인터넷 서비스 공급자)의 주소, 서브넷 마스크(또는 접두사 길이), 기본 게이트웨이 및 DNS 서버 등 공용 IP 주소 구성이 필요합니다. 이 공용 IP 주소는 3단계에 필요합니다.

RRAS1의 관리자 수준 Windows PowerShell 명령 프롬프트에서 다음 명령을 실행합니다. 이러한 명령을 실행하기 전에 변수 값을 작성하고 < and > 문자를 제거합니다. **Get-NetAdapter** 명령 표시에서 네트워크 어댑터의 현재 이름을 가져올 수 있습니다.

	$corpnetAdapterName="<Name of the adapter attached to the Corpnet subnet>"
	$internetAdapterName="<Name of the adapter attached to the Internet>"
	[Ipaddress]$publicIP="<Your public IP address>"
	$publicIPpreflength=<Prefix length of your public IP address>
	[IPAddress]$publicDG="<Your ISP default gateway>"
	[IPAddress]$publicDNS="<Your ISP DNS server(s)>"
	Rename-NetAdapter -Name $corpnetAdapterName -NewName Corpnet
	Rename-NetAdapter -Name $internetAdapterName -NewName Internet
	New-NetIPAddress -InterfaceAlias "Internet" -IPAddress $publicIP -PrefixLength $publicIPpreflength “DefaultGateway $publicDG
	Set-DnsClientServerAddress -InterfaceAlias Internet -ServerAddresses $publicDNS
	New-NetIPAddress -InterfaceAlias "Corpnet" -IPAddress 10.0.0.2 -AddressFamily IPv4 -PrefixLength 24
	Set-DnsClientServerAddress -InterfaceAlias "Corpnet" -ServerAddresses 10.0.0.1
	Set-DnsClient -InterfaceAlias "Corpnet" -ConnectionSpecificSuffix corp.contoso.com
	New-NetFirewallRule -DisplayName "Allow ICMPv4-Input" -Protocol ICMPv4
	New-NetFirewallRule -DisplayName "Allow ICMPv4-Output" -Protocol ICMPv4 -Direction Outbound
	Disable-NetAdapterBinding -Name "Internet" -ComponentID ms_msclient
	Disable-NetAdapterBinding -Name "Internet" -ComponentID ms_server
	ping dc1.corp.contoso.com

마지막 명령에 대해 IP 주소 10.0.0.1에서 4개의 회신이 수신되는지 확인합니다.

다음은 현재 구성입니다.

![](./media/virtual-machines-windows-ps-hybrid-cloud-test-env-base/virtual-machines-windows-ps-hybrid-cloud-test-env-base-ph2.png)

## 3단계: 크로스-프레미스 Azure 가상 네트워크 만들기

Azure PowerShell 프롬프트를 시작합니다.

> [AZURE.NOTE] 다음 명령 집합은 Azure PowerShell 1.0 이상을 사용합니다. 자세한 내용은 [Azure PowerShell 1.0](https://azure.microsoft.com/blog/azps-1-0/)을 참조하세요.

계정에 로그인합니다.

	Login-AzureRMAccount

다음 명령을 사용하여 구독 이름을 가져옵니다.

	Get-AzureRMSubscription | Sort SubscriptionName | Select SubscriptionName

Azure 구독을 설정합니다. 기본 구성을 빌드하는 데 사용했던 동일한 구독을 사용합니다. < and > 문자를 포함하여 따옴표 안의 모든 항목을 올바른 이름으로 바꿉니다.

	$subscr="<subscription name>"
	Get-AzureRmSubscription –SubscriptionName $subscr | Select-AzureRmSubscription

다음으로, 하이브리드 테스트 환경에 대한 새 리소스 그룹을 만듭니다.

고유한 리소스 그룹 이름을 확인하려면 다음 명령을 사용하여 기존 리소스 그룹을 나열합니다.

	Get-AzureRMResourceGroup | Sort ResourceGroupName | Select ResourceGroupName

이러한 명령을 사용하여 새 리소스 그룹을 만듭니다.

	$rgName="<resource group name>"
	$locName="<an Azure location, such as West US>"
	New-AzureRMResourceGroup -Name $rgName -Location $locName

리소스 관리자 기반 가상 컴퓨터를 사용하려면 리소스 관리자 기반 저장소 계정이 필요합니다. 소문자와 숫자만 포함하는 각 저장소 계정에 대해서 전역적으로 고유한 이름을 선택해야 합니다. 이 명령을 사용하여 기존 저장소 계정을 나열할 수 있습니다.

	Get-AzureRMStorageAccount | Sort Name | Select Name

다음 명령을 사용하여 새 저장소 계정을 만듭니다.

	$rgName="<your new resource group name>"
	$locName="<the location of your new resource group>"
	$saName="<unique storage account name >"
	New-AzureRMStorageAccount -Name $saName -ResourceGroupName $rgName –Type Standard_LRS -Location $locName

다음으로, 하이브리드 클라우드 환경을 호스트하고 네트워크 보안 그룹을 통해 이를 보호하는 Azure 가상 네트워크를 만듭니다.

	$locShortName="<the location of your new resource group in lowercase with spaces removed, example: westus>"
	$gwSubnet=New-AzureRMVirtualNetworkSubnetConfig -Name "GatewaySubnet" -AddressPrefix "192.168.255.248/29"
	$vmSubnet=New-AzureRMVirtualNetworkSubnetConfig -Name "TestSubnet" -AddressPrefix "192.168.0.0/24"
	New-AzureRMVirtualNetwork -Name "TestVNET" -ResourceGroupName $rgName -Location $locName -AddressPrefix "192.168.0.0/16" -Subnet $gwSubnet,$vmSubnet -DNSServer "10.0.0.1"
	$rule1=New-AzureRMNetworkSecurityRuleConfig -Name "RDPTraffic" -Description "Allow RDP to all VMs on the subnet" -Access Allow -Protocol Tcp -Direction Inbound -Priority 100 -SourceAddressPrefix Internet -SourcePortRange * -DestinationAddressPrefix * -DestinationPortRange 3389
	New-AzureRMNetworkSecurityGroup -Name "TestSubnet" -ResourceGroupName $rgName -Location $locShortName -SecurityRules $rule1
	$vnet=Get-AzureRMVirtualNetwork -ResourceGroupName $rgName -Name "TestVNET"
	$nsg=Get-AzureRMNetworkSecurityGroup -Name "TestSubnet" -ResourceGroupName $rgName
	Set-AzureRMVirtualNetworkSubnetConfig -VirtualNetwork $vnet -Name TestSubnet -AddressPrefix "192.168.0.0/24" -NetworkSecurityGroup $nsg

다음으로 이러한 명령을 사용하여 사이트 간 VPN 연결을 위한 게이트웨이를 만듭니다. 2단계에서 사용된 RRAS1의 인터넷 인터페이스에 대한 공용 IP 주소가 필요합니다.

	$localGatewayIP="<the public IP address assigned to the Internet interface of RRAS1>"
	$vnet=Get-AzureRMVirtualNetwork -Name TestVNET -ResourceGroupName $rgName
	
	# Attach a virtual network gateway to a public IP address and the gateway subnet
	$publicGatewayVipName="HybCloudPublicIPAddress"
	$vnetGatewayIpConfigName="HybCloudPublicIPConfig"
	New-AzureRMPublicIpAddress -Name $vnetGatewayIpConfigName -ResourceGroupName $rgName -Location $locName -AllocationMethod Dynamic
	$publicGatewayVip=Get-AzureRMPublicIpAddress -Name $vnetGatewayIpConfigName -ResourceGroupName $rgName
	$vnetGatewayIpConfig=New-AzureRMVirtualNetworkGatewayIpConfig -Name $vnetGatewayIpConfigName -PublicIpAddressId $publicGatewayVip.Id -SubnetId $vnet.Subnets[0].Id
	
	# Create the Azure gateway
	$vnetGatewayName="HybCloudAzureGateway"
	$vnetGateway=New-AzureRMVirtualNetworkGateway -Name $vnetGatewayName -ResourceGroupName $rgName -Location $locName -GatewayType Vpn -VpnType RouteBased -IpConfigurations $vnetGatewayIpConfig
	
	# Create the gateway for the local network
	$localGatewayName="HybCloudLocalNetGateway"
	$localNetworkPrefix="10.0.0.0/8"
	$localGateway=New-AzureRMLocalNetworkGateway -Name $localGatewayName -ResourceGroupName $rgName -Location $locName -GatewayIpAddress $localGatewayIP -AddressPrefix $localNetworkPrefix

새 게이트웨이를 완료하는 데 20분 이상 걸릴 수 있습니다.

다음으로, 다음 명령을 사용하여 가상 네트워크에 대한 Azure VPN 게이트웨이의 공용 IP 주소를 확인합니다.

	Get-AzureRMPublicIpAddress -Name $vnetGatewayIpConfigName -ResourceGroupName $rgName

화면에 표시된 **IPAddress** 필드의 IP 주소를 적어 둡니다. 4단계에 필요합니다.

다음으로, 네트워크 또는 보안 관리자에서 임의로 암호화된 강력한 32자의 미리 공유한 키를 얻습니다. 또는 [IPsec 미리 공유한 키에 대한 임의 문자열 만들기](http://social.technet.microsoft.com/wiki/contents/articles/32330.create-a-random-string-for-an-ipsec-preshared-key.aspx)에서 정보를 사용하여 미리 공유한 키를 얻습니다.

이러한 명령을 사용하여 Azure에서 사이트 간 VPN 연결을 만듭니다.

	# Define the Azure virtual network VPN connection
	$vnetConnectionKey="<32-character pre-shared key>"
	$vnetConnectionName="HybCloudS2SConnection"
	$vnetConnection=New-AzureRMVirtualNetworkGatewayConnection -Name $vnetConnectionName -ResourceGroupName $rgName -Location $locName -ConnectionType IPsec -SharedKey $vnetConnectionKey -VirtualNetworkGateway1 $vnetGateway -LocalNetworkGateway2 $localGateway

다음은 현재 구성입니다.

![](./media/virtual-machines-windows-ps-hybrid-cloud-test-env-base/virtual-machines-windows-ps-hybrid-cloud-test-env-base-ph3.png)
 
## 4단계: 사이트 간 VPN 연결 만들기

먼저, 라우팅 및 원격 액세스 서비스를 사용하여 RRAS1이 Corpnet 서브넷의 VPN 장치 역할을 하도록 구성합니다. RRAS1에 로컬 관리자로 로그온하여 Windows PowerShell 명령 프롬프트에서 다음 명령을 실행합니다.

	Import-Module ServerManager
	Install-WindowsFeature RemoteAccess -IncludeManagementTools
	Add-WindowsFeature -name Routing -IncludeManagementTools

그런 다음 Azure VPN 게이트웨이에서 사이트 간 VPN 연결을 받도록 RRAS1을 구성합니다. RRAS1을 다시 시작한 다음 로컬 관리자로 로그온하여 Windows PowerShell 명령 프롬프트에서 다음 명령을 실행합니다. Azure VPN 게이트웨이의 공용 IP 주소 및 3단계에서 사용된 사전 공유 키 값을 제공해야 합니다.

	$PresharedKey="<32-character pre-shared key>"
	Import-Module RemoteAccess
	Install-RemoteAccess -VpnType VpnS2S
	Add-VpnS2SInterface -Protocol IKEv2 -AuthenticationMethod PSKOnly -Persistent -NumberOfTries 3 -ResponderAuthenticationMethod PSKOnly -Name S2StoTestVNET -Destination "<public IP address of the Azure VPN gateway>" -IPv4Subnet @("192.168.0.0/24:100") -SharedSecret $PresharedKey
	Set-VpnServerIPsecConfiguration -EncryptionType MaximumEncryption
	Set-VpnServerIPsecConfiguration -SADataSizeForRenegotiationKilobytes 33553408
	New-ItemProperty -Path HKLM:\System\CurrentControlSet\Services\RemoteAccess\Parameters\IKEV2 -Name SkipConfigPayload -PropertyType DWord -Value 1
	Restart-Service RemoteAccess

RRAS1과 Azure VPN 게이트웨이 간의 연결을 설정하는 데 몇 분 정도 걸립니다.

그런 다음 인터넷 위치에 대한 변환된 트래픽을 지원하도록 RRAS1을 구성합니다. RRAS1에서 다음을 수행합니다.

1.	시작 화면에서 **rras**를 입력한 다음 **라우팅 및 원격 액세스**를 클릭합니다. 
2.	콘솔 트리에서 서버 이름을 열고 **IPv4**를 클릭합니다.
3.	**일반**을 마우스 오른쪽 단추로 클릭한 다음 **새 라우팅 프로토콜**을 클릭합니다.
4.	**NAT**를 클릭한 다음 **확인**을 클릭합니다.
5.	콘솔 트리에서 마우스 오른쪽 단추로 **NAT**, **새로운 인터페이스**, **Corpnet**을 차례로 클릭한 다음 **확인**을 두 번 클릭합니다.
6.	마우스 오른쪽 단추로 **NAT**를 클릭하고 **새로운 인터페이스**, **인터넷**을 차례로 클릭한 다음 **확인**을 클릭합니다.
7.	**NAT** 탭에서 **인터넷에 연결된 공용 인터페이스**를 클릭하고 **이 인터페이스에서 NAT 사용**을 클릭한 다음 **확인**을 클릭합니다.

그런 다음 RRAS1을 해당 기본 게이트웨이로 사용하도록 DC1, APP1 및 CLIENT1을 구성합니다.
 
DC1의 관리자 수준 Windows PowerShell 명령 프롬프트에서 다음 명령을 실행합니다.

	New-NetRoute -DestinationPrefix "0.0.0.0/0" -InterfaceAlias "Ethernet" -NextHop 10.0.0.2
	Set-DhcpServerv4OptionValue -Router 10.0.0.2

이더넷 인터페이스의 이름이 이더넷이 아닌 경우 **Get-NetAdapter** 명령을 사용하여 인터페이스 이름을 확인합니다.

APP1의 관리자 수준 Windows PowerShell 명령 프롬프트에서 다음 명령을 실행합니다.

	New-NetRoute -DestinationPrefix "0.0.0.0/0" -InterfaceAlias "Ethernet" -NextHop 10.0.0.2

CLIENT1의 관리자 수준 Windows PowerShell 명령 프롬프트에서 다음 명령을 실행합니다.

	ipconfig /renew

다음은 현재 구성입니다.
 

![](./media/virtual-machines-windows-ps-hybrid-cloud-test-env-base/virtual-machines-windows-ps-hybrid-cloud-test-env-base-ph4.png)


## 5단계: DC2 구성

먼저 로컬 컴퓨터의 Azure PowerShell 명령 프롬프트에서 다음 명령을 사용하여 DS2용 Azure 가상 컴퓨터를 만듭니다.

	$rgName="<your resource group name>"
	$locName="<your Azure location, such as West US>"
	$saName="<your storage account name>"
	
	$vnet=Get-AzureRMVirtualNetwork -Name "TestVNET" -ResourceGroupName $rgName
	$subnet=Get-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $vnet -Name "TestSubnet"
	$pip=New-AzureRMPublicIpAddress -Name DC2-NIC -ResourceGroupName $rgName -Location $locName -AllocationMethod Dynamic
	$nic=New-AzureRMNetworkInterface -Name DC2-NIC -ResourceGroupName $rgName -Location $locName -Subnet $subnet -PublicIpAddress $pip -PrivateIpAddress 192.168.0.4
	$vm=New-AzureRMVMConfig -VMName DC2 -VMSize Standard_A1
	$storageAcc=Get-AzureRMStorageAccount -ResourceGroupName $rgName -Name $saName
	$vhdURI=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/DC2-TestVNET-ADDSDisk.vhd"
	Add-AzureRMVMDataDisk -VM $vm -Name ADDS-Data -DiskSizeInGB 20 -VhdUri $vhdURI  -CreateOption empty
	$cred=Get-Credential -Message "Type the name and password of the local administrator account for DC2."
	$vm=Set-AzureRMVMOperatingSystem -VM $vm -Windows -ComputerName DC2 -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
	$vm=Set-AzureRMVMSourceImage -VM $vm -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2012-R2-Datacenter -Version "latest"
	$vm=Add-AzureRMVMNetworkInterface -VM $vm -Id $nic.Id
	$osDiskUri=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/DC2-TestLab-OSDisk.vhd"
	$vm=Set-AzureRMVMOSDisk -VM $vm -Name DC2-TestVNET-OSDisk -VhdUri $osDiskUri -CreateOption fromImage
	New-AzureRMVM -ResourceGroupName $rgName -Location $locName -VM $vm

다음으로, Azure 포털을 사용하여 로컬 관리자 계정의 자격 증명으로 새 DC2 가상 컴퓨터에 연결합니다.

그런 다음 기본 연결 테스트에 대한 트래픽을 허용하도록 Windows 방화벽 규칙을 구성합니다. DC2의 관리자 수준 Windows PowerShell 명령 프롬프트에서 다음 명령을 실행합니다.

	Set-NetFirewallRule -DisplayName "File and Printer Sharing (Echo Request - ICMPv4-In)" -enabled True
	ping dc1.corp.contoso.com

Ping 명령을 실행한 경우 IP 주소 10.0.0.1에서 성공적인 회신 4개가 수신되어야 합니다. *시뮬레이션된 하이브리드 클라우드 구성*을 사용하는 경우 IP 주소 10.0.0.4로부터의 성공적인 회신 4개가 표시됩니다. 이는 사이트 간 VPN 연결 또는 VNet 간 연결의 트래픽에 대한 테스트입니다.

그런 다음 추가 데이터 디스크를 드라이브 문자가 F:인 새 볼륨으로 추가합니다.

1.	Server Manager의 왼쪽 창에서 **파일 및 저장소 서비스**를 클릭한 다음 **디스크**를 클릭합니다.
2.	내용 창의 **디스크** 그룹에서 **디스크 2**(**파티션**이 **알 수 없음**으로 설정됨)를 클릭합니다.
3.	**작업**을 클릭한 후 **새 볼륨**을 클릭합니다.
4.	새 볼륨 마법사의 시작하기 전에 페이지에서 **다음**을 클릭합니다.
5.	서버 및 디스크 선택 페이지에서 **디스크 2**를 클릭하고 **다음**을 클릭합니다. 메시지가 표시되면 **확인**을 클릭합니다.
6.	볼륨 크기를 선택합니다 페이지에서 **다음**을 클릭합니다.
7.	드라이브 문자 또는 폴더에 할당 페이지에서 **다음**을 클릭합니다.
8.	파일 시스템 설정 선택 페이지에서 **다음**을 클릭합니다.
9.	선택 확인 페이지에서 **만들기**를 클릭합니다.
10.	완료되면 **닫기**를 클릭합니다.

그런 다음 DC2를 corp.contoso.com 도메인의 복제본 도메인 컨트롤러로 구성합니다. DC2의 관리자 수준 Windows PowerShell 명령 프롬프트에서 다음 명령을 실행합니다.

	Install-WindowsFeature AD-Domain-Services -IncludeManagementTools
	Install-ADDSDomainController -Credential (Get-Credential CORP\User1) -DomainName "corp.contoso.com" -InstallDns:$true -DatabasePath "F:\NTDS" -LogPath "F:\Logs" -SysvolPath "F:\SYSVOL"

CORP\\User1 암호와 DSRM(디렉터리 서비스 복원 모드) 암호를 둘 다 제공하고 DC2를 다시 시작하라는 메시지가 표시됩니다.

TestVNET 가상 네트워크에는 고유한 DNS 서버(DC2)가 있으므로 이 DNS 서버를 사용하도록 TestVNET 가상 네트워크를 구성해야 합니다.

1.	Azure 포털의 왼쪽 창에서 가상 네트워크 아이콘을 클릭한 다음 **TestVNET**을 클릭합니다.
2.	**설정** 탭에서 **DNS 서버**를 클릭합니다.
3.	**주 DNS 서버**에서 10.0.0.4를 **192.168.0.4**로 바꿔서 입력합니다.
4.	저장을 클릭합니다.

다음으로, Azure PowerShell 명령 프롬프트에서 이러한 명령으로 새 DNS 서버 구성을 사용할 수 있도록 DC2를 다시 시작합니다.

	Stop-AzureRmVM -ResourceGroupName $rgName -Name DC2
	Start-AzureRmVM -ResourceGroupName $rgName -Name DC2

다음은 현재 구성입니다.

![](./media/virtual-machines-windows-ps-hybrid-cloud-test-env-base/virtual-machines-windows-ps-hybrid-cloud-test-env-base-ph5.png)

이제 하이브리드 클라우드 환경을 테스트할 준비가 완료되었습니다.
 
## 다음 단계

- 이 환경에서 [SharePoint 인트라넷 팜](virtual-machines-windows-ps-hybrid-cloud-test-env-sp.md), [웹 기반 LOB 응용 프로그램](virtual-machines-windows-ps-hybrid-cloud-test-env-lob.md) 또는 [Office 365 디렉터리 동기화(DirSync) 서버](virtual-machines-windows-ps-hybrid-cloud-test-env-dirsync.md)를 설정합니다.

<!---HONumber=AcomDC_0413_2016-->