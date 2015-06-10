<properties 
	pageTitle="테스트용 하이브리드 클라우드 환경 설정" 
	description="IT 전문가 또는 개발 테스트용 하이브리드 클라우드 환경을 만드는 방법에 대해 알아봅니다." 
	services="virtual-network" 
	documentationCenter="" 
	authors="JoeDavies-MSFT" 
	manager="timlt" 
	editor=""/>

<tags 
	ms.service="virtual-network" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/07/2015" 
	ms.author="josephd"/>

# 테스트용 하이브리드 클라우드 환경 설정

이 항목에서는 Microsoft Azure를 사용하여 테스트용 하이브리드 클라우드 환경을 만드는 과정을 안내합니다. 다음은 결과 구성입니다.

![](./media/virtual-networks-set-up-hybrid-cloud-environment-for-testing/CreateHybridCloudVNet_5.png)

이 구성에서는 인터넷상의 현재 위치에서 실제 하이브리드 프로덕션 환경을 시뮬레이션하며, 다음으로 구성됩니다.

-  간소화된 온-프레미스 네트워크(Corpnet 서브넷).
-  Azure에서 호스트되는 크로스-프레미스 가상 네트워크(TestVNET)
-  사이트 간 VPN 연결
-  TestVNET 가상 네트워크의 보조 도메인 컨트롤러

이 구성은 다음 작업을 수행할 수 있는 기초 및 일반적인 시작 지점을 제공합니다.

-  하이브리드 클라우드 환경에서 응용 프로그램 개발 및 테스트
-  일부는 Corpnet 서브넷에 있고 일부는 TestVNET 가상 네트워크에 있는 컴퓨터의 하이브리드 클라우드 기반 IT 작업에 대한 테스트 구성을 만듭니다.

이 하이브리드 클라우드 테스트 환경을 설정하는 다섯 가지 주요 단계가 있습니다.

1.	Corpnet 서브넷에서 컴퓨터 구성
2.	RRAS1 구성
3.	크로스-프레미스 Azure 가상 네트워크 만들기
4.	사이트 간 VPN 연결 만들기
5.	DC2 구성 

Azure 구독이 아직 없는 경우 [Azure 평가판 사용](http://azure.microsoft.com/pricing/free-trial/)에서 무료 평가판에 등록할 수 있습니다. MSDN 구독이 있는 경우 [MSDN 구독자를 위한 Azure 혜택](http://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)을 참조하세요.

>[AZURE.NOTE]Azure의 가상 컴퓨터 및 가상 네트워크 게이트웨이는 실행 중인 동안 지속적인 비용이 부과됩니다. 이 비용은 무료 평가판, MSDN 구독 또는 유료 구독에 대해 청구됩니다. 이 테스트 환경을 사용하지 않을 때 실행 비용을 절감하려면, 자세한 내용은 이 항목의 [이 환경의 지속적인 비용 최소화](#costs)를 참조하세요.

이 구성에서는 공용 IP 주소를 사용하여 인터넷에 직접 연결된 최대 네 대의 컴퓨터의 테스트 서브넷이 필요합니다. 이러한 리소스를 설정하지 않은 경우, [테스트를 위해 시뮬레이션된 하이브리드 클라우드 환경 설정](virtual-networks-setup-simulated-hybrid-cloud-environment-testing.md)을 수행할 수도 있습니다. 시뮬레이션된 하이브리드 클라우드 테스트 환경은 Azure 구독에만 필요합니다.

## 1단계: Corpnet 서브넷에서 컴퓨터 구성

[테스트 랩 가이드: Windows Server 2012 R2 기본 구성](http://www.microsoft.com/download/details.aspx?id=39638)의 "Corpnet 서브넷 구성을 위한 단계" 섹션의 지침을 사용하여 Corpnet 이라는 서브넷에서 DC1, APP1 및 CLIENT1 컴퓨터를 구성합니다. **이 서브넷은 RRAS1 컴퓨터를 통해 인터넷에 직접 연결되므로 조직 네트워크와 격리해야 합니다.**

그런 다음 CORP\User1 자격 증명으로 DC1에 로그온합니다. 컴퓨터 및 사용자가 해당 로컬 도메인 컨트롤러를 사용하여 인증할 수 있도록 CORP 도메인을 구성하려면 관리자 수준 Windows PowerShell 명령 프롬프트에서 다음 명령을 실행합니다.

	New-ADReplicationSite -Name "TestLab" 
	New-ADReplicationSite -Name "TestVNET"
	New-ADReplicationSubnet –Name "10.0.0.0/8" –Site "TestLab"
	New-ADReplicationSubnet –Name "192.168.0.0/16" –Site "TestVNET

다음은 현재 구성입니다.

![](./media/virtual-networks-set-up-hybrid-cloud-environment-for-testing/CreateHybridCloudVNet_1.png)
 
## 2단계: RRAS1 구성

RRAS1은 Corpnet 서브넷에 있는 컴퓨터와 TestVNET 가상 네트워크에 있는 컴퓨터 간에 트래픽 라우팅 및 VPN 장치 서비스를 제공합니다. RRAS1에는 두 개의 네트워크 어댑터가 설치되어야 합니다.

먼저 RRAS1에 운영 체제를 설치합니다.

1.	Windows Server 2012 R2 설치를 시작합니다.
2.	로컬 관리자 계정에 대한 강력한 암호를 지정하여 지침에 따라 설치를 완료합니다. 로컬 관리자 계정을 사용하여 로그온합니다.
3.	인터넷에 액세스할 수 있는 네트워크에 RRAS1을 연결하고 Windows 업데이트를 실행하여 Windows Server 2012 R2의 최신 업데이트를 설치합니다.
4.	Corpnet 서브넷에 네트워크 어댑터 하나를 연결하고 나머지 하나를 인터넷에 직접 연결합니다. RRAS1은 인터넷 방화벽 뒤에 둘 수 있지만 NAT(Network Address Translator) 뒤에 있어서는 안 됩니다.

그런 다음 RRAS1의 TCP/IP 속성을 구성합니다. ISP(인터넷 서비스 공급자)의 주소, 서브넷 마스크(또는 접두사 길이), 기본 게이트웨이 및 DNS 서버 등 공용 IP 주소 구성이 필요합니다.

RRAS1의 관리자 수준 Windows PowerShell 명령 프롬프트에서 다음 명령을 실행합니다. 이러한 명령을 실행하기 전에 변수 값을 작성하고 < and > 문자를 제거합니다. **Get-NetAdapter** 명령 표시에서 네트워크 어댑터의 현재 이름을 가져올 수 있습니다.

	$corpnetAdapterName="<Name of the adapter attached to the Corpnet subnet>"
	$internetAdapterName="<Name of the adapter attached to the Internet>"
	[Ipaddress]$publicIP="<Your public IP address>"
	$publicIPpreflength=<Prefix length of your public IP address>
	[IPAddress]$publicDG="<Your ISP default gateway>"
	[IPAddress]$publicDNS="<Your ISP DNS server(s)>"
	Rename-NetAdapter –Name $corpnetAdapterName –NewName Corpnet
	Rename-NetAdapter –Name $internetAdapterName –NewName Internet
	New-NetIPAddress -InterfaceAlias "Internet" -IPAddress $publicIP -PrefixLength $publicIPpreflength –DefaultGateway $publicDG
	Set-DnsClientServerAddress -InterfaceAlias Internet -ServerAddresses $publicDNS
	New-NetIPAddress -InterfaceAlias "Corpnet" -IPAddress 10.0.0.2 -AddressFamily IPv4 -PrefixLength 24
	Set-DnsClientServerAddress -InterfaceAlias "Corpnet" -ServerAddresses 10.0.0.1
	Set-DnsClient -InterfaceAlias "Corpnet" -ConnectionSpecificSuffix corp.contoso.com
	New-NetFirewallRule –DisplayName “Allow ICMPv4-In” –Protocol ICMPv4
	New-NetFirewallRule –DisplayName “Allow ICMPv4-Out” –Protocol ICMPv4 –Direction Outbound
	Disable-NetAdapterBinding -Name "Internet" -ComponentID ms_msclient
	Disable-NetAdapterBinding -Name "Internet" -ComponentID ms_server
	ping dc1.corp.contoso.com

마지막 명령에 대해 IP 주소 10.0.0.1에서 4개의 회신이 수신되는지 확인합니다.

다음은 현재 구성입니다.

![](./media/virtual-networks-set-up-hybrid-cloud-environment-for-testing/CreateHybridCloudVNet_2.png)

## 3단계: 크로스-프레미스 Azure 가상 네트워크 만들기

먼저 Azure 구독 자격 증명을 사용하여 [Azure 관리 포털](https://manage.windowsazure.com/)에 로그온하고 TestVNET이라는 가상 네트워크를 만듭니다.

1.	Azure 관리 포털의 작업 표시줄에서 **새로 만들기 > 네트워크 서비스 > 가상 네트워크 > 사용자 지정 만들기**를 클릭합니다.
2.	가상 네트워크 세부 정보 페이지에서 **TestVNET**을 **이름**에 입력합니다.
3.	**위치**에서 사용자 위치에 적절한 데이터 센터를 선택합니다.
4.	다음 화살표를 클릭합니다.
5.	DNS 서버 및 VPN 연결 페이지의 **DNS 서버**에서, **DC1**를 **이름 선택 또는 입력**에 입력하고, **10.0.0.1**을 **IP 주소**에 입력한 다음 **사이트 간 VPN 구성**을 선택합니다.
6.	**로컬 네트워크**에서 **새 로컬 네트워크 지정**을 선택합니다. 
7.	다음 화살표를 클릭합니다.
8.	사이트 간 연결 페이지에서 다음을 수행합니다.
	- **이름**에 **Corpnet**을 입력합니다. 
	- **VPN 장치 IP 주소**에 RRAS1의 인터넷 인터페이스에 할당된 공용 IP 주소를 입력합니다.
	- **주소 공간**의 **시작 IP** 열에 **10.0.0.0**을 입력합니다. **CIDR(주소 수)**에서 **/8**를 선택한 다음 **주소 공간 추가**를 클릭합니다.
9.	다음 화살표를 클릭합니다.
10.	가상 네트워크 주소 공간 페이지에서 다음을 수행합니다.
	- **주소 공간**의 **시작 IP**에서 **192.168.0.0**을 선택합니다.
	- **서브넷**에서 **서브넷-1**을 클릭하고 이름을 **TestSubnet**으로 바꿉니다. 
	- TestSubnet에 대한 **CIDR(주소 수)** 열에서 **/24 (256)**를 클릭합니다.
	- **게이트웨이 서브넷 추가**를 클릭합니다.
11.	완료 아이콘을 클릭합니다. 가상 네트워크가 만들어질 때까지 기다렸다가 계속 진행합니다.

다음으로는 [Azure PowerShell을 설치 및 구성하는 방법](install-configure-powershell.md)의 지침을 사용하여 로컬 컴퓨터에 Azure PowerShell을 설치합니다.

그런 다음 TestVNET 가상 네트워크에 대한 새 클라우드 서비스를 만듭니다. 고유한 이름을 선택해야 합니다. 예를 들어, TestVNET-*UniqueSequence*의 이름을 지정하며, 여기서 *UniqueSequence*는 조직의 약어입니다. 예를 들어 조직의 이름이 Tailspin Toys인 경우 클라우드 서비스 이름을 TestVNET-Tailspin으로 지정할 수 있습니다.

로컬 컴퓨터에서 다음 Azure PowerShell 명령을 사용하여 이름의 고유성을 테스트할 수 있습니다.

	Test-AzureName -Service <Proposed cloud service name>

이 명령에서 "False"가 반환되면 제안한 이름이 고유한 것입니다. 다음 명령을 사용하여 클라우드 서비스를 만듭니다.

	New-AzureService -Service <Unique cloud service name> -Location "<Same location name as your virtual network>"

그런 다음 TestVNET 가상 네트워크에 대한 새 저장소 계정을 만듭니다. 고유한 이름을 선택해야 합니다. 다음 명령을 사용하여 저장소 계정 이름의 고유성을 테스트할 수 있습니다.

	Test-AzureName -Storage <Proposed storage account name>

이 명령에서 "False"가 반환되면 제안한 이름이 고유한 것입니다. 다음 명령을 사용하여 저장소 계정을 만들고 설정합니다.

	New-AzureStorageAccount -StorageAccountName <Unique storage account name> -Location "<Same location name as your virtual network>"
	Set-AzureStorageAccount -StorageAccountName <Unique storage account name>

다음은 현재 구성입니다.

![](./media/virtual-networks-set-up-hybrid-cloud-environment-for-testing/CreateHybridCloudVNet_3.png)

 
## 4단계: 사이트 간 VPN 연결 만들기

먼저 가상 네트워크 게이트웨이를 만듭니다.

1.	로컬 컴퓨터의 Azure 관리 포털의 왼쪽 창에서 **네트워크**를 클릭한 다음 TestVNET에 대한 **상태** 열이 **Created**로 설정되었는지 확인합니다.
2.	**TestVNET**를 클릭합니다. 대시보드 페이지에서 **게이트웨이가 만들어지지 않음** 상태가 표시되어야 합니다.
3.	작업 표시줄에서 **게이트웨이 만들기**, **동적 라우팅**을 차례로 클릭합니다. 메시지가 표시되면 **예**를 클릭합니다. 게이트웨이가 완료되고 해당 상태가 **연결 중**으로 변경될 때까지 대기합니다. 몇 분 정도 걸릴 수 있습니다.
4.	대시보드 페이지에서 **게이트웨이 IP 주소**를 확인합니다. 이는 TestVNET 가상 네트워크에 대한 Azure VPN 게이트웨이의 공용 IP 주소입니다. RRAS1을 구성하려면 이 IP 주소가 필요합니다.
5.	작업 표시줄에서 **키 관리**를 클릭한 다음, 키 옆에 있는 복사 아이콘을 클릭하여 클립보드에 복사합니다. 이 키를 문서에 붙여 넣고 저장합니다. RRAS1을 구성하려면 이 키 값이 필요합니다. 

그런 다음 라우팅 및 원격 액세스 서비스를 사용하여 RRAS1이 Corpnet 서브넷의 VPN 장치 역할을 하도록 구성합니다. RRAS1에 로컬 관리자로 로그온하여 Windows PowerShell 명령 프롬프트에서 다음 명령을 실행합니다.

	Import-Module ServerManager
	Install-WindowsFeature RemoteAccess -IncludeManagementTools
	Add-WindowsFeature -name Routing -IncludeManagementTools

그런 다음 Azure VPN 게이트웨이에서 사이트 간 VPN 연결을 받도록 RRAS1을 구성합니다. RRAS1을 다시 시작한 다음 로컬 관리자로 로그온하여 Windows PowerShell 명령 프롬프트에서 다음 명령을 실행합니다. Azure VPN 게이트웨이의 IP 주소 및 키 값을 제공해야 합니다.

	$PresharedKey="<Key value>"
	Import-Module RemoteAccess
	Install-RemoteAccess -VpnType VpnS2S
	Add-VpnS2SInterface -Protocol IKEv2 -AuthenticationMethod PSKOnly -Persistent -NumberOfTries 3 -ResponderAuthenticationMethod PSKOnly -Name S2StoTestVNET -Destination "<IP address of the Azure VPN gateway>" -IPv4Subnet @("192.168.0.0/24:100") -SharedSecret $PresharedKey
	Set-VpnServerIPsecConfiguration -EncryptionType MaximumEncryption
	Set-VpnServerIPsecConfiguration -SADataSizeForRenegotiationKilobytes 33553408
	New-ItemProperty -Path HKLM:\System\CurrentControlSet\Services\RemoteAccess\Parameters\IKEV2 -Name SkipConfigPayload -PropertyType DWord -Value 1
	Restart-Service RemoteAccess

다음으로, 로컬 컴퓨터의 Azure 관리 포털로 이동하고 TestVNET 가상 네트워크의 상태가 **연결됨**으로 표시될 때까지 대기합니다.

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

	New-NetRoute –DestinationPrefix "0.0.0.0/0" –InterfaceAlias "Ethernet" –NextHop 10.0.0.2
	Set-DhcpServerv4OptionValue –Router 10.0.0.2

이더넷 인터페이스의 이름이 이더넷이 아닌 경우 **Get-NetAdapter** 명령을 사용하여 인터페이스 이름을 확인합니다.

APP1의 관리자 수준 Windows PowerShell 명령 프롬프트에서 다음 명령을 실행합니다.

	New-NetRoute –DestinationPrefix "0.0.0.0/0" –InterfaceAlias "Ethernet" –NextHop 10.0.0.2

CLIENT1의 관리자 수준 Windows PowerShell 명령 프롬프트에서 다음 명령을 실행합니다.

	ipconfig /renew

다음은 현재 구성입니다.
 

![](./media/virtual-networks-set-up-hybrid-cloud-environment-for-testing/CreateHybridCloudVNet_4.png)


## 5단계: DC2 구성

먼저 로컬 컴퓨터의 Azure PowerShell 명령 프롬프트에서 다음 명령을 사용하여 DS2용 Azure 가상 컴퓨터를 만듭니다.

	$ServiceName="<Your cloud service name from Phase 3>"
	$image = Get-AzureVMImage | where { $_.ImageFamily -eq "Windows Server 2012 R2 Datacenter" } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1
	$vm1=New-AzureVMConfig -Name DC2 -InstanceSize Medium -ImageName $image
	$cred=Get-Credential –Message "Type the name and password of the local administrator account for DC2."
	$vm1 | Add-AzureProvisioningConfig -Windows -AdminUsername $cred.GetNetworkCredential().Username -Password $cred.GetNetworkCredential().Password 
	$vm1 | Add-AzureProvisioningConfig -Windows -AdminUsername $LocalAdminName -Password $LocalAdminPW	
	$vm1 | Set-AzureSubnet -SubnetNames TestSubnet
	$vm1 | Set-AzureStaticVNetIP -IPAddress 192.168.0.4
	$vm1 | Add-AzureDataDisk -CreateNew -DiskSizeInGB 20 -DiskLabel ADFiles –LUN 0 -HostCaching None
	New-AzureVM –ServiceName $ServiceName -VMs $vm1 -VNetName TestVNET


그런 다음 새 DC2 가상 컴퓨터에 로그온합니다.

1.	Azure 관리 포털의 왼쪽된 창에서 **가상 컴퓨터**를 클릭한 다음 DC2에 대한 **상태** 열에서 **실행**을 클릭합니다.
2.	작업 표시줄에서 **연결**을 클릭합니다. 
3.	DC2.rdp를 여는 대화 상자가 나타나면 **열기**를 클릭합니다.
4.	원격 데스크톱 연결 메시지 상자가 포함된 메시지가 나타나면 **연결**을 클릭합니다.
5.	자격 증명을 묻는 메시지가 나타나면 다음을 사용합니다.
	- 이름: **DC2**[로컬 관리자 계정 이름을]
	- 암호: [로컬 관리자 계정 암호]
6.	인증서를 참조하는 원격 데스크톱 연결 메시지 상자가 포함된 메시지가 나타나면 **예**를 클릭합니다.

그런 다음 기본 연결 테스트에 대한 트래픽을 허용하도록 Windows 방화벽 규칙을 구성합니다. DC2의 관리자 수준 Windows PowerShell 명령 프롬프트에서 다음 명령을 실행합니다.

	Set-NetFirewallRule -DisplayName "File and Printer Sharing (Echo Request - ICMPv4-In)" -enabled True
	ping dc1.corp.contoso.com

Ping 명령을 실행한 경우 IP 주소 10.0.0.1에서 성공적인 회신 4개가 수신되어야 합니다. 이는 사이트 간 VPN 연결의 트래픽에 대한 테스트입니다.

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

CORP\User1 암호와 DSRM(디렉터리 서비스 복원 모드) 암호를 둘 다 제공하고 DC2를 다시 시작하라는 메시지가 표시됩니다.

TestVNET 가상 네트워크에는 고유한 DNS 서버(DC2)가 있으므로 이 DNS 서버를 사용하도록 TestVNET 가상 네트워크를 구성해야 합니다.

1.	Azure 관리 포털의 왼쪽 창에서 **네트워크**를 클릭한 다음 **TestVNET**를 클릭합니다.
2.	**Configure**를 클릭합니다.
3.	**DNS 서버**에서 **10.0.0.1** 항목을 제거합니다.
4.	**DNS 서버**에서 **DC2**를 이름으로, **192.168.0.4**를 IP 주소로 항목을 추가합니다. 
5.	아래쪽의 명령 모음에서 **저장**을 클릭합니다.
6.	Azure 관리 포털의 왼쪽 창에서 **가상 컴퓨터**를 클릭한 다음 DC2 옆의 **상태** 열을 클릭합니다.
7.	명령 모음에서 **다시 시작**을 클릭합니다. DC2가 다시 시작될 때까지 기다립니다.


다음은 현재 구성입니다.

![](./media/virtual-networks-set-up-hybrid-cloud-environment-for-testing/CreateHybridCloudVNet_5.png)

 
이제 하이브리드 클라우드 환경을 테스트할 준비가 완료되었습니다.

## 추가 리소스

[테스트용 하이브리드 클라우드에 SharePoint 인트라넷 팜 설치](virtual-networks-setup-sharepoint-hybrid-cloud-testing.md)

[테스트를 위한 하이브리드 클라우드에서 웹 기반 LOB 응용 프로그램 설정](virtual-networks-setup-lobapp-hybrid-cloud-testing.md)

[테스트를 위한 하이브리드 클라우드에서 Office 365 디렉터리 동기화(DirSync) 설정](virtual-networks-setup-dirsync-hybrid-cloud-testing.md)

[테스트를 위한 시뮬레이션된 하이브리드 클라우드 환경 설정](virtual-networks-setup-simulated-hybrid-cloud-environment-testing.md)

[Azure 하이브리드 클라우드 테스트 환경](virtual-machines-hybrid-cloud-test-environments.md)


## 이 환경의 지속적인 비용 최소화

이 환경에서 가상 컴퓨터를 실행하는 데 드는 비용을 최소화하려면 가능한 신속하게 필요한 테스트 및 데모를 수행한 다음 가상 컴퓨터를 삭제하거나, 사용하지 않을 때 가상 컴퓨터를 종료합니다. 예를 들어 Azure 자동화 및 Runbook을 사용하여 업무 시간이 끝날 때마다 Test_VNET 가상 네트워크의 가상 컴퓨터를 자동으로 종료할 수 있습니다. 자세한 내용은 [Azure 자동화 시작](automation-create-runbook-from-samples.md)을 참조하세요.

Azure VPN 게이트웨이는 지속적인 비용이 발생하는 두 개의 Azure 가상 컴퓨터 집합으로 구현됩니다. 자세한 내용은 [가격-가상 네트워크](http://azure.microsoft.com/pricing/details/virtual-network/)를 참조하세요. VPN 게이트웨이의 비용을 최소화하려면 테스트 환경을 만들고 가능한 신속하게 필요한 테스트 및 데모를 수행하거나 다음 단계를 사용하여 게이트웨이를 삭제합니다.

1.	로컬 컴퓨터의 Azure 관리 포털의 왼쪽 창에서 **네트워크**, **TestVNET**, **대시보드**를 차례로 클릭합니다.
2.	작업 표시줄에서 **게이트웨이 삭제**를 클릭합니다. 메시지가 표시되면 **예**를 클릭합니다. 게이트웨이가 삭제되고 해당 상태가 **게이트웨이가 만들어지지 않음**으로 변경될 때까지 대기합니다.

게이트웨이를 삭제한 경우 테스트 환경을 복원하려면 먼저 새 게이트웨이를 만들어야 합니다.

1.	로컬 컴퓨터에서 Azure 관리 포털의 왼쪽 창에서 **네트워크**를 클릭한 다음 **TestVNET**을 클릭합니다. 대시보드 페이지에 **게이트웨이가 만들어지지 않음**의 상태가 표시되어야 합니다.
2.	작업 표시줄에서 **게이트웨이 만들기**, **동적 라우팅**을 차례로 클릭합니다. 메시지가 표시되면 **예**를 클릭합니다. 게이트웨이가 완료되고 해당 상태가 **연결 중**으로 변경될 때까지 대기합니다. 몇 분 정도 걸릴 수 있습니다.
3.	대시보드 페이지에서 **게이트웨이 IP 주소**를 확인합니다. 이는 TestVNET 가상 네트워크에 대한 Azure VPN 게이트웨이의 새 공용 IP 주소입니다. RRAS1을 다시 구성하려면 이 IP 주소가 필요합니다.
4.	작업 표시줄에서 **키 관리**를 클릭한 다음, 키 옆에 있는 복사 아이콘을 클릭하여 클립보드에 복사합니다. 이 키 값을 문서에 붙여 넣고 저장합니다. RRAS1을 다시 구성하려면 이 키 값이 필요합니다. 

이제 RRAS1에 로컬 관리자로 로그온한 후 관리자 수준 Windows PowerShell 명령 프롬프트에서 다음 명령을 실행하여 새 공용 IP 주소 및 사전 공유 키로 RRAS1을 다시 구성합니다.

	$PresharedKey="<Key value>"
	Set-VpnS2SInterface -Name S2StoTestVNET -Destination "<IP address of the Azure VPN gateway>" -SharedSecret $PresharedKey

그런 다음 로컬 컴퓨터에서 Azure 관리 포털로 이동하여 TestVNET 가상 네트워크의 상태가 연결됨으로 표시될 때까지 기다립니다.

<!---HONumber=58-->