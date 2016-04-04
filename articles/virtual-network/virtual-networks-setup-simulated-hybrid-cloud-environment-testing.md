<properties 
	pageTitle="시뮬레이션된 하이브리드 클라우드 테스트 환경 | Microsoft Azure" 
	description="Azure 가상 네트워크 두 개와 VNet 간 연결을 사용하여 IT 전문가 또는 개발 테스트용 시뮬레이션된 하이브리드 클라우드 환경을 만들어봅니다." 
	services="virtual-network" 
	documentationCenter="" 
	authors="JoeDavies-MSFT" 
	manager="timlt" 
	editor=""
	tags="azure-service-management"/>

<tags 
	ms.service="virtual-network" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="Windows" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/03/2016" 
	ms.author="josephd"/>

# 테스트를 위한 시뮬레이션된 하이브리드 클라우드 환경 설정(클래식 배포 모드)

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)] [Resource Manager model](../virtual-machines/virtual-machines-setup-simulated-hybrid-cloud-environment-testing.md).

이 문서에서는 별도의 Azure 가상 네트워크 두 개를 사용하여 Microsoft Azure에서 테스트용 시뮬레이션된 하이브리드 클라우드 환경을 만드는 과정을 안내합니다. 인터넷을 직접 연결할 수 없고 사용 가능한 공용 IP 주소가 없는 경우 [테스트용 하이브리드 클라우드 환경 설정](virtual-networks-setup-hybrid-cloud-environment-testing.md)의 대안으로 이 구성을 사용합니다. 다음은 결과 구성입니다.

![](./media/virtual-networks-setup-simulated-hybrid-cloud-environment-testing/CreateSimHybridCloud_4.png)

하이브리드 클라우드 프로덕션 환경을 시뮬레이션하며, 다음으로 구성됩니다.

- Azure 가상 네트워크(TestLab 가상 네트워크)에서 호스트되는 시뮬레이션 및 간소화된 온-프레미스 네트워크
- Azure에서 호스트되는 시뮬레이션된 크로스-프레미스 가상 네트워크(TestVNET)
- 두 가상 네트워크의 VNet 간 연결
- TestVNET 가상 네트워크의 보조 도메인 컨트롤러

이 구성은 다음 작업을 수행할 수 있는 기초 및 일반적인 시작 지점을 제공합니다.

- 시뮬레이션된 하이브리드 클라우드 환경에서 응용 프로그램 개발 및 테스트
- 일부는 TestLab 가상 네트워크 내에 있고 일부는 TestVNET 가상 네트워크에 있는 컴퓨터의 테스트 구성을 만들어 하이브리드 클라우드 기반 IT 작업을 시뮬레이션합니다.

이 하이브리드 클라우드 테스트 환경을 설정하는 네 가지 주요 단계가 있습니다.

1.	TestLab 가상 네트워크 구성
2.	크로스-프레미스 가상 네트워크 만들기
3.	VNet 간 VPN 연결 만들기
4.	DC2 구성 

Azure 구독이 아직 없는 경우 [Azure 평가판 사용](https://azure.microsoft.com/pricing/free-trial/)에서 무료 평가판에 등록할 수 있습니다. MSDN 구독이 있는 경우 [MSDN 구독자를 위한 Azure 혜택](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)을 참조하세요.

>[AZURE.NOTE] Azure의 가상 컴퓨터 및 가상 네트워크 게이트웨이는 실행 중인 동안 지속적인 비용이 부과됩니다. 이 비용은 무료 평가판, MSDN 구독 또는 유료 구독에 대해 청구됩니다. 이 테스트 환경을 사용하지 않을 때 실행 비용을 절감하려면, 자세한 내용은 이 항목의 [이 환경의 지속적인 비용 최소화](#costs)를 참조하세요.


## 1단계: TestLab 가상 네트워크 구성

[테스트 환경 기본 구성](../virtual-machines/virtual-machines-windows-classic-test-config-env.md)에 설명된 지침을 사용하여 TestLab이라는 Azure 가상 네트워크에서 DC1, APP1 및 CLIENT1 컴퓨터를 구성합니다.

로컬 컴퓨터의 Azure 관리 포털에서 CORP\\User1 자격 증명을 사용하여 DC1에 연결합니다. 컴퓨터 및 사용자가 해당 로컬 도메인 컨트롤러를 사용하여 인증할 수 있도록 CORP 도메인을 구성하려면 관리자 수준 Windows PowerShell 명령 프롬프트에서 다음 명령을 실행합니다.

	New-ADReplicationSite -Name "TestLab" 
	New-ADReplicationSite -Name "TestVNET"
	New-ADReplicationSubnet -Name "10.0.0.0/8" -Site "TestLab"
	New-ADReplicationSubnet -Name "192.168.0.0/16" -Site "TestVNET"

다음은 현재 구성입니다.

![](./media/virtual-networks-setup-simulated-hybrid-cloud-environment-testing/CreateSimHybridCloud_1.png)
 
## 2단계: TestVNET 가상 네트워크 만들기

먼저 TestVNET이라는 새 가상 네트워크를 만듭니다.

1.	Azure 관리 포털의 작업 표시줄에서 **새로 만들기 > 네트워크 서비스 > 가상 네트워크 > 사용자 지정 만들기**를 클릭합니다.
2.	가상 네트워크 세부 정보 페이지에서 **TestVNET**을 **이름**에 입력합니다.
3.	**위치**에서 적절한 위치를 선택합니다.
4.	다음 화살표를 클릭합니다.
5.	DNS 서버 및 VPN 연결 페이지의 **DNS 서버**에서 **이름 선택 또는 입력**에 **DC1**을 입력하고 다음 화살표를 클릭합니다.
6.	가상 네트워크 주소 공간 페이지에서 다음을 수행합니다.
	- **주소 공간**의 **시작 IP**에서 **192.168.0.0**을 선택하거나 입력합니다.
	- **서브넷**에서 **서브넷-1**을 클릭하고 이름을 **TestSubnet**으로 바꿉니다. 
	- TestSubnet에 대한 **CIDR(주소 수)** 열에서 **/24 (256)**를 클릭합니다.
7.	완료 아이콘을 클릭합니다. 가상 네트워크가 만들어질 때까지 기다렸다가 계속 진행합니다.

다음으로는 [Azure PowerShell을 설치 및 구성하는 방법](../install-configure-powershell.md)의 지침을 사용하여 로컬 컴퓨터에 Azure PowerShell을 설치합니다.

그런 다음 TestVNET 가상 네트워크에 대한 새 클라우드 서비스를 만듭니다. 고유한 이름을 선택해야 합니다. 예를 들어, **TestVNET-***UniqueSequence*의 이름을 지정하며, 여기서 *UniqueSequence*는 조직의 약어입니다. 예를 들어 조직의 이름이 Tailspin Toys인 경우 클라우드 서비스 이름을 **TestVNET-Tailspin**으로 지정할 수 있습니다.

로컬 컴퓨터에서 다음 Azure PowerShell 명령을 사용하여 이름의 고유성을 테스트할 수 있습니다.

	Test-AzureName -Service <Proposed cloud service name>

이 명령에서 "False"가 반환되면 제안한 이름이 고유한 것입니다. 다음 명령을 사용하여 클라우드 서비스를 만듭니다.

	New-AzureService -Service <Unique cloud service name> -Location "<Same location name as your virtual network>"

다음은 현재 구성입니다.

![](./media/virtual-networks-setup-simulated-hybrid-cloud-environment-testing/CreateSimHybridCloud_2.png)
 
##3단계: VNet 간 연결 만들기

먼저 각 가상 네트워크의 주소 공간을 나타내는 로컬 네트워크를 만듭니다.

1.	로컬 컴퓨터의 Azure 관리 포털에서 **새로 만들기 > 네트워크 서비스 > 가상 네트워크 > 로컬 네트워크 추가**를 클릭합니다.
2.	로컬 네트워크 세부 정보 지정 페이지에서 **이름**에 **TestLabLNet**을 입력하고 **VPN 장치 IP 주소**에 **131.107.0.1**을 입력한 다음 오른쪽 화살표를 클릭합니다.
3.	주소 공간 지정 페이지에서 **시작 IP**에 **10.0.0.0**을 입력합니다.
4.	**CIDR(주소 수)**에서 **/24(256)**를 선택하고 확인 표시를 클릭합니다.
5.	**새로 만들기 > 네트워크 서비스 > 가상 네트워크 > 로컬 네트워크 추가**를 클릭합니다.
6.	로컬 네트워크 세부 정보 지정 페이지에서 **이름**에 **TestVNETLNet**을 입력하고 **VPN 장치 IP 주소**에 **131.107.0.2**를 입력한 다음 오른쪽 화살표를 클릭합니다.
7.	주소 공간 지정 페이지에서 **시작 IP**에 **192.168.0.0**을 입력합니다.
8.	**CIDR(주소 수)**에서 **/24(256)**를 선택하고 확인 표시를 클릭합니다.

VPN 장치 IP 주소 131.107.0.1 및 131.107.0.2는 두 가상 네트워크에 대한 게이트웨이를 구성할 때까지는 임시 자리 표시자 값에 불과합니다.

그런 다음 사이트 간 VPN 연결 및 다른 가상 네트워크에 해당하는 로컬 네트워크를 사용하여 각 가상 네트워크를 구성합니다.

1.	로컬 컴퓨터의 Azure 관리 포털에서 왼쪽 창에 있는 **네트워크**를 클릭한 다음 **TestLab**의 **상태** 열이 **생성됨**으로 설정되어 있는지 확인합니다.
2.	**TestLab**을 클릭한 후 **구성**을 클릭합니다. TestLab 페이지의 **사이트 간 연결** 섹션에서 **로컬 네트워크에 연결**을 클릭합니다. 
3.	**로컬 네트워크**에서 **TestVNETLNet**을 선택합니다.
4.	작업 표시줄에서 **저장**을 클릭합니다. 경우에 따라 게이트웨이 **서브넷 추가**를 클릭하여 Azure VPN 게이트웨이에서 사용하는 서브넷을 만들어야 할 수도 있습니다.
5.	왼쪽 창에서 **네트워크**를 클릭한 다음 TestVNET의 **상태** 열이 **생성됨**으로 설정되어 있는지 확인합니다.
6.	**TestVNET**을 클릭한 후 **구성**을 클릭합니다. TestVNET 페이지의 **사이트 간 연결** 섹션에서 **로컬 네트워크에 연결**을 클릭합니다. 
7.	**로컬 네트워크**에서 **TestLabLNet**을 선택합니다.
8.	작업 표시줄에서 **저장**을 클릭합니다. 경우에 따라 게이트웨이 **서브넷 추가**를 클릭하여 Azure VPN 게이트웨이에서 사용하는 서브넷을 만들어야 할 수도 있습니다.

그런 다음 두 가상 네트워크에 대한 가상 네트워크 게이트웨이를 만듭니다.

1.	Azure 관리 포털의 **네트워크** 페이지에서 **TestLab**을 클릭합니다. 대시보드 페이지에 **게이트웨이가 만들어지지 않음**의 상태가 표시되어야 합니다.
2.	작업 표시줄에서 **게이트웨이 만들기**, **동적 라우팅**을 차례로 클릭합니다. 메시지가 표시되면 **예**를 클릭합니다. 게이트웨이가 완료되고 해당 상태가 **연결 중**으로 변경될 때까지 대기합니다. 몇 분 정도 걸릴 수 있습니다.
3.	대시보드 페이지에서 **게이트웨이 IP 주소**를 확인합니다. 이는 TestLab 가상 네트워크에 대한 Azure VPN 게이트웨이의 공용 IP 주소입니다. 이 IP 주소는 VNet 간 연결을 구성하는 데 필요하므로 기록해 둡니다.
4.	작업 표시줄에서 **키 관리**를 클릭한 다음 키 옆의 복사 아이콘을 클릭하여 클립보드에 복사합니다. 이 키를 문서에 붙여 넣고 저장합니다. VNet 간 연결을 구성하려면 이 키 값이 필요합니다.
5.	네트워크 페이지에서 **TestVNET**을 클릭합니다. 대시보드 페이지에 **게이트웨이가 만들어지지 않음**의 상태가 표시되어야 합니다.
6.	작업 표시줄에서 **게이트웨이 만들기**, **동적 라우팅**을 차례로 클릭합니다. 메시지가 표시되면 **예**를 클릭합니다. 게이트웨이가 완료되고 해당 상태가 **연결 중**으로 변경될 때까지 대기합니다. 몇 분 정도 걸릴 수 있습니다.
7.	대시보드 페이지에서 **게이트웨이 IP 주소**를 확인합니다. 이는 TestVNET 가상 네트워크에 대한 Azure VPN 게이트웨이의 공용 IP 주소입니다. 이 IP 주소는 VNet 간 연결을 구성하는 데 필요하므로 기록해 둡니다.

그런 다음 가상 네트워크 게이트웨이를 만들기에서 가져온 공용 IP 주소로 TestLabLNet 및 TestVNETLNet 로컬 네트워크를 구성합니다.

1.	Azure 관리 포털의 네트워크 페이지에서 **로컬 네트워크**를 클릭합니다. 
2.	**TestLabLNet**을 클릭한 후 작업 표시줄에서 **편집**을 클릭합니다.
3.	로컬 네트워크 세부 정보 지정 페이지에서 **VPN 장치 IP 주소(선택)**에 TestLab 가상 네트워크에 대한 가상 네트워크 게이트웨이의 IP 주소(위 절차의 3단계)를 입력하고 오른쪽 화살표를 클릭합니다.
4.	주소 공간 지정 페이지에서 확인 표시를 클릭합니다.
5.	로컬 네트워크 페이지에서 **TestVNETLNet**을 클릭한 후 작업 표시줄에서 **편집**을 클릭합니다.
6.	로컬 네트워크 세부 정보 지정 페이지에서 **VPN 장치 IP 주소(선택)**에 TestVNET 가상 네트워크에 대한 가상 네트워크 게이트웨이의 IP 주소(위 절차의 7단계)를 입력하고 오른쪽 화살표를 클릭합니다.
7.	주소 공간 지정 페이지에서 확인 표시를 클릭합니다.

그런 다음 동일한 값(Azure 관리 포털에서 확인한 TestLab 가상 네트워크의 키 값)을 사용하도록 두 게이트웨이의 사전 공유 키를 구성합니다. TestLab 사전 공유 키 값을 입력하여 로컬 컴퓨터의 Azure PowerShell 명령 프롬프트에서 다음 명령을 실행합니다.

	$preSharedKey="<The preshared key for the TestLab virtual network>"
	Set-AzureVNetGatewayKey -VNetName TestVNET -LocalNetworkSiteName TestLabLNet -SharedKey $preSharedKey

그런 다음 로컬 컴퓨터의 Azure 관리 포털 네트워크 페이지에서 **TestLab** 가상 네트워크, **대시보드**를 차례로 클릭한 후 작업 표시줄에서 **연결**을 클릭합니다. TestLab 가상 네트워크 상태가 연결됨으로 표시될 때까지 기다립니다.

다음은 현재 구성입니다.

![](./media/virtual-networks-setup-simulated-hybrid-cloud-environment-testing/CreateSimHybridCloud_3.png)
 
## 4단계: DC2 구성

먼저 DC2용 Azure 가상 컴퓨터를 만듭니다. 로컬 컴퓨터의 Azure PowerShell 명령 프롬프트에서 다음 명령을 실행합니다.

	$ServiceName="<Your cloud service name from Phase 2>"
	$cred=Get-Credential -Message "Type the name and password of the local administrator account for DC2."
	$image = Get-AzureVMImage | where { $_.ImageFamily -eq "Windows Server 2012 R2 Datacenter" } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1
	$vm1=New-AzureVMConfig -Name DC2 -InstanceSize Medium -ImageName $image
	$vm1 | Add-AzureProvisioningConfig -Windows -AdminUsername $cred.GetNetworkCredential().Username -Password $cred.GetNetworkCredential().Password
	$vm1 | Set-AzureSubnet -SubnetNames TestSubnet
	$vm1 | Set-AzureStaticVNetIP -IPAddress 192.168.0.4
	$vm1 | Add-AzureDataDisk -CreateNew -DiskSizeInGB 20 -DiskLabel ADFiles -LUN 0 -HostCaching None
	New-AzureVM -ServiceName $ServiceName -VMs $vm1 -VNetName TestVNET

그런 다음 새 DC2 가상 컴퓨터에 로그온합니다.

1.	Azure 관리 포털의 왼쪽된 창에서 **가상 컴퓨터**를 클릭한 다음 DC2에 대한 **상태** 열에서 **실행**을 클릭합니다.
2.	작업 표시줄에서 **연결**을 클릭합니다. 
3.	DC2.rdp를 여는 대화 상자가 나타나면 **열기**를 클릭합니다.
4.	원격 데스크톱 연결 메시지 상자가 포함된 메시지가 나타나면 **연결**을 클릭합니다.
5.	자격 증명을 묻는 메시지가 나타나면 다음을 사용합니다.
- 이름: **DC2\**[로컬 관리자 계정 이름]
- 암호: [로컬 관리자 계정 암호]
6.	인증서를 참조하는 원격 데스크톱 연결 메시지 상자가 포함된 메시지가 나타나면 **예**를 클릭합니다.

그런 다음 기본 연결 테스트에 대한 트래픽을 허용하도록 Windows 방화벽 규칙을 구성합니다. DC2의 관리자 수준 Windows PowerShell 명령 프롬프트에서 다음 명령을 실행합니다.

	Set-NetFirewallRule -DisplayName "File and Printer Sharing (Echo Request - ICMPv4-In)" -enabled True
	ping dc1.corp.contoso.com

Ping 명령을 실행한 경우 IP 주소 10.0.0.4에서 성공적인 회신 4개가 수신되어야 합니다. 이는 Vnet 간 연결의 트래픽에 대한 테스트입니다.

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

1.	Azure 관리 포털의 왼쪽 창에서 **네트워크**를 클릭한 다음 **TestVNET**를 클릭합니다.
2.	**Configure**를 클릭합니다.
3.	**DNS 서버**에서 10.0.0.4 항목을 제거합니다.
4.	**DNS 서버**에서 **DC2**를 이름으로, **192.168.0.4**를 IP 주소로 항목을 추가합니다. 
5.	아래쪽의 명령 모음에서 **저장**을 클릭한 후 메시지가 나타나면 **예**를 클릭합니다. TestVNet 네트워크의 업데이트가 완료될 때까지 기다립니다.

다음은 현재 구성입니다.

![](./media/virtual-networks-setup-simulated-hybrid-cloud-environment-testing/CreateSimHybridCloud_4.png)
 
이제 시뮬레이션된 하이브리드 클라우드 환경을 테스트할 준비가 완료되었습니다.

## 다음 단계

- TestVNET 가상 네트워크에서 [SharePoint 인트라넷 팜](virtual-networks-setup-sharepoint-hybrid-cloud-testing.md), [웹 기반 LOB 응용 프로그램](virtual-networks-setup-lobapp-hybrid-cloud-testing.md) 또는 [Office 365 디렉터리 동기화(DirSync) 서버](virtual-networks-setup-dirsync-hybrid-cloud-testing.md)를 설정합니다.


## <a id="costs"></a>이 환경의 지속적인 비용 최소화

이 환경에서 가상 컴퓨터를 실행하는 데 드는 비용을 최소화하려면 가능한 신속하게 필요한 테스트 및 데모를 수행한 다음 가상 컴퓨터를 삭제하거나, 사용하지 않을 때 가상 컴퓨터를 종료합니다. 예를 들어 Azure 자동화 및 Runbook을 사용하여 업무 시간이 끝날 때마다 TestLab 및 Test\_VNET 가상 네트워크의 가상 컴퓨터를 자동으로 종료할 수 있습니다. 자세한 내용은 [Azure 자동화 시작](../automation-create-runbook-from-samples.md)을 참조하세요. Corpnet 서브넷의 가상 컴퓨터를 다시 시작할 때는 DC1을 먼저 시작하세요.

Azure VPN 게이트웨이는 지속적인 비용이 발생하는 두 개의 Azure 가상 컴퓨터 집합으로 구현됩니다. 자세한 내용은 [가격-가상 네트워크](https://azure.microsoft.com/pricing/details/virtual-network/)를 참조하세요. 두 VPN 게이트웨이(각각 TestLab과 TestVNET의 VPN 게이트웨이)의 비용을 최소화하려면 테스트 환경을 만들고 가능한 신속하게 필요한 테스트 및 데모를 수행하거나 다음 단계를 사용하여 게이트웨이를 삭제합니다.
 
1.	로컬 컴퓨터의 Azure 관리 포털 왼쪽 창에서 **네트워크**, **TestLab**, **대시보드**를 차례로 클릭합니다.
2.	작업 표시줄에서 **게이트웨이 삭제**를 클릭합니다. 메시지가 표시되면 **예**를 클릭합니다. 게이트웨이가 삭제되고 해당 상태가 **게이트웨이가 만들어지지 않음**으로 변경될 때까지 대기합니다.
3.	왼쪽 창에서 **네트워크**를 클릭한 후 **TestVNET**, **대시보드**를 차례로 클릭합니다.
4.	작업 표시줄에서 **게이트웨이 삭제**를 클릭합니다. 메시지가 표시되면 **예**를 클릭합니다. 게이트웨이가 삭제되고 해당 상태가 **게이트웨이가 만들어지지 않음**으로 변경될 때까지 대기합니다.

게이트웨이를 삭제한 경우 이 테스트 환경을 복원하려면 먼저 새 게이트웨이를 만들어야 합니다.

1.	로컬 컴퓨터의 Azure 관리 포털 왼쪽 창에서 **네트워크**, **TestLab**을 차례로 클릭합니다. 대시보드 페이지에 **게이트웨이가 만들어지지 않음**의 상태가 표시되어야 합니다.
2.	작업 표시줄에서 **게이트웨이 만들기**, **동적 라우팅**을 차례로 클릭합니다. 메시지가 표시되면 **예**를 클릭합니다. 게이트웨이가 완료되고 해당 상태가 **연결 중**으로 변경될 때까지 대기합니다. 몇 분 정도 걸릴 수 있습니다.
3.	대시보드 페이지에서 **게이트웨이 IP 주소**를 확인합니다. 이는 TestLab 가상 네트워크에 대한 Azure VPN 게이트웨이의 새 공용 IP 주소입니다. TestLabLNet 로컬 네트워크를 다시 구성하려면 이 IP 주소가 필요합니다.
4.	작업 표시줄에서 **키 관리**를 클릭한 다음, 키 옆에 있는 복사 아이콘을 클릭하여 클립보드에 복사합니다. 이 키 값을 문서에 붙여 넣고 저장합니다. TestVNET 가상 네트워크에 대한 VPN 게이트웨이를 다시 구성하려면 이 키 값이 필요합니다.
5.	로컬 컴퓨터에서 Azure 관리 포털의 왼쪽 창에서 **네트워크**를 클릭한 다음 **TestVNET**을 클릭합니다. 대시보드 페이지에 **게이트웨이가 만들어지지 않음**의 상태가 표시되어야 합니다.
6.	작업 표시줄에서 **게이트웨이 만들기**, **동적 라우팅**을 차례로 클릭합니다. 메시지가 표시되면 **예**를 클릭합니다. 게이트웨이가 완료되고 해당 상태가 연결 중으로 변경될 때까지 기다립니다. 몇 분 정도 걸릴 수 있습니다.
7.	대시보드 페이지에서 **게이트웨이 IP 주소**를 확인합니다. 이는 TestVNET 가상 네트워크에 대한 Azure VPN 게이트웨이의 새 공용 IP 주소입니다. TestVNETLNet 로컬 네트워크를 다시 구성하려면 이 IP 주소가 필요합니다.

그런 다음 가상 네트워크 게이트웨이를 만들기에서 가져온 새 공용 IP 주소로 TestLabLNet 및 TestVNETLNet 로컬 네트워크를 구성합니다.

1.	Azure 관리 포털의 네트워크 페이지에서 **로컬 네트워크**를 클릭합니다. 
2.	**TestLabLNet**을 클릭한 후 작업 표시줄에서 **편집**을 클릭합니다.
3.	로컬 네트워크 세부 정보 지정 페이지에서 **VPN 장치 IP 주소(선택)**에 TestLab 가상 네트워크에 대한 가상 네트워크 게이트웨이의 IP 주소(위 절차의 3단계)를 입력하고 오른쪽 화살표를 클릭합니다.
4.	주소 공간 지정 페이지에서 확인 표시를 클릭합니다.
5.	로컬 네트워크 페이지에서 **TestVNETLNet**을 클릭한 후 작업 표시줄에서 **편집**을 클릭합니다.
6.	로컬 네트워크 세부 정보 지정 페이지에서 **VPN 장치 IP 주소(선택)**에 TestVNET 가상 네트워크에 대한 가상 네트워크 게이트웨이의 IP 주소(위 절차의 7단계)를 입력하고 오른쪽 화살표를 클릭합니다.
7.	주소 공간 지정 페이지에서 확인 표시를 클릭합니다.

그런 다음 동일한 값(Azure 관리 포털에서 확인한 TestLab 가상 네트워크의 키 값)을 사용하도록 두 게이트웨이의 사전 공유 키를 구성합니다. TestLab 사전 공유 키 값을 입력하여 로컬 컴퓨터의 Azure PowerShell 명령 프롬프트에서 다음 명령을 실행합니다.

	$preSharedKey="<The preshared key for the TestLab virtual network>"
	Set-AzureVNetGatewayKey -VNetName TestVNET -LocalNetworkSiteName TestLabLNet -SharedKey $preSharedKey

그런 다음 Azure 관리 포털의 네트워크 페이지에서 **TestLab** 가상 네트워크를 클릭한 후 작업 표시줄에서 **연결**을 클릭합니다. TestLab 가상 네트워크가 TestVNET 로컬 네트워크에 연결된 상태로 표시될 때까지 기다립니다.

<!---HONumber=AcomDC_0323_2016-->