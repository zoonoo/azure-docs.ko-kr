<properties 
	pageTitle="SharePoint 2013 팜 테스트 환경 | Microsoft Azure" 
	description="개발 또는 IT 전문가 테스트용 하이브리드 클라우드 환경에서 2계층 SharePoint Server 2013 인트라넷 팜을 만드는 방법에 대해 알아봅니다." 
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
	ms.date="01/28/2016" 
	ms.author="josephd"/>

# 테스트용 하이브리드 클라우드에 SharePoint 인트라넷 팜 설치

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]리소스 관리자 모델.
 

이 항목에서는 Microsoft Azure에서 호스트되는 인트라넷 SharePoint 팜을 테스트하기 위한 하이브리드 클라우드 환경을 만드는 과정을 안내합니다. 다음은 결과 구성입니다.

![](./media/virtual-networks-setup-sharepoint-hybrid-cloud-testing/CreateSPFarmHybridCloud_3.png)
 
이 구성에서는 인터넷상의 현재 위치에서 Azure 프로덕션 환경의 SharePoint를 시뮬레이션하며, 다음으로 구성됩니다.

- 간소화된 온-프레미스 네트워크(Corpnet 서브넷).
- Microsoft Azure에서 호스트되는 크로스-프레미스 가상 네트워크(TestVNET)
- 사이트 간 VPN 연결
- TestVNET 가상 네트워크의 2계층 SharePoint 팜 및 보조 도메인 컨트롤러

이 구성은 다음 작업을 수행할 수 있는 기초 및 일반적인 시작 지점을 제공합니다.

- 하이브리드 클라우드 환경의 SharePoint 인트라넷 팜에서 응용 프로그램 개발 및 테스트
- 이 하이브리드 클라우드 기반 IT 워크로드의 테스트 수행

이 하이브리드 클라우드 테스트 환경을 설정하는 세 가지 주요 단계가 있습니다.

1.	테스트용 하이브리드 클라우드 환경 설정
2.	SQL Server 컴퓨터(SQL1) 구성
3.	SharePoint Server(SP1) 구성

아직 Azure 구독이 없는 경우에는 [Azure 평가판 사용](https://azure.microsoft.com/pricing/free-trial/)에서 무료로 가입할 수 있습니다. MSDN 구독이 있는 경우 [MSDN 구독자를 위한 Azure 혜택](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)을 참조하세요.

## 1 단계: 하이브리드 클라우드 환경 설정

[테스트용 하이브리드 클라우드 환경 설정](virtual-networks-setup-hybrid-cloud-environment-testing.md) 항목의 지침을 따르세요. 이 테스트 환경에는 APP1 서버가 Corpnet 서브넷에 있을 필요가 없으므로 지금은 종료해도 됩니다.

다음은 현재 구성입니다.

![](./media/virtual-networks-setup-sharepoint-hybrid-cloud-testing/CreateSPFarmHybridCloud_1.png)

> [AZURE.NOTE] 1 단계에서는 시뮬레이션된 하이브리드 클라우드 테스트 환경 또한 설정할 수 있습니다. 자세한 내용은 [테스트용 시뮬레이션된 하이브리드 클라우드 환경 설정](virtual-networks-setup-simulated-hybrid-cloud-environment-testing.md) 지침을 참조하세요.
 
## 2단계: SQL Server 컴퓨터(SQL1) 구성

Azure 관리 포털에서 DC2 컴퓨터(필요한 경우)를 시작합니다.

먼저 CORP\\User1 자격 증명을 사용하여 DC2에 대한 원격 데스크톱 연결을 만듭니다.

그런 다음 SharePoint 팜 관리자 계정을 만듭니다. DC2에서 관리자 수준 Windows PowerShell 프롬프트를 열고 다음 명령을 실행합니다.

	New-ADUser -SamAccountName SPFarmAdmin -AccountPassword (read-host "Set user password" -assecurestring) -name "SPFarmAdmin" -enabled $true -ChangePasswordAtLogon $false

SPFarmAdmin 계정 암호를 제공하라는 메시지가 나타나면 강력한 암호를 입력하고 안전한 위치에 기록합니다.

그런 다음 로컬 컴퓨터의 Azure PowerShell 명령 프롬프트에서 다음 명령을 사용하여 SQL1용 Azure 가상 컴퓨터를 만듭니다.


	$storageacct="<Name of the storage account for your TestVNET virtual network>"
	$ServiceName="<The cloud service name for your TestVNET virtual network>"
	$cred1=Get-Credential -Message "Type the name and password of the local administrator account for SQL1."
	$cred2=Get-Credential -UserName "CORP\User1" -Message "Now type the password for the CORP\User1 account."
	Set-AzureStorageAccount -StorageAccountName $storageacct
	$image= Get-AzureVMImage | where { $_.ImageFamily -eq "SQL Server 2014 RTM Standard on Windows Server 2012 R2" } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1
	$vm1=New-AzureVMConfig -Name SQL1 -InstanceSize Large -ImageName $image
	$vm1 | Add-AzureProvisioningConfig -AdminUsername $cred1.GetNetworkCredential().Username -Password $cred1.GetNetworkCredential().Password -WindowsDomain -Domain "CORP" -DomainUserName "User1" -DomainPassword $cred2.GetNetworkCredential().Password -JoinDomain "corp.contoso.com"
	$vm1 | Set-AzureSubnet -SubnetNames TestSubnet
	$vm1 | Add-AzureDataDisk -CreateNew -DiskSizeInGB 100 -DiskLabel SQLFiles -LUN 0 -HostCaching None
	New-AzureVM -ServiceName $ServiceName -VMs $vm1 -VNetName TestVNET

*로컬 관리자 계정을 사용*하여 새 SQL1 가상 컴퓨터에 연결합니다.

1.	Azure 관리 포털의 왼쪽 창에서 **가상 컴퓨터**를 클릭한 다음 SQL1에 대한 상태 열에서 **실행 중**을 클릭합니다.
2.	작업 표시줄에서 **연결**을 클릭합니다. 
3.	SQL1.rdp를 열라는 메시지가 나타나면 **열기**를 클릭합니다.
4.	원격 데스크톱 연결 메시지 상자가 포함된 메시지가 나타나면 **연결**을 클릭합니다.
5.	자격 증명을 묻는 메시지가 나타나면 다음을 사용합니다.
	- 이름: **SQL1\**[로컬 관리자 계정 이름]
	- 암호: [로컬 관리자 계정 암호]
6.	인증서를 참조하는 원격 데스크톱 연결 메시지 상자가 포함된 메시지가 나타나면 **예**를 클릭합니다.

그런 다음 기본 연결 테스트 및 SQL Server에 대한 트래픽을 허용하도록 Windows 방화벽 규칙을 구성합니다. SQL1의 관리자 수준 Windows PowerShell 명령 프롬프트에서 다음 명령을 실행합니다.

	New-NetFirewallRule -DisplayName "SQL Server" -Direction Inbound -Protocol TCP -LocalPort 1433,1434,5022 -Action allow 
	Set-NetFirewallRule -DisplayName "File and Printer Sharing (Echo Request - ICMPv4-In)" -enabled True
	ping dc1.corp.contoso.com

Ping 명령을 실행한 경우 IP 주소 10.0.0.1에서 성공적인 회신 4개가 수신되어야 합니다.

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

SQL1의 Windows PowerShell 명령 프롬프트에서 다음 명령을 실행합니다.

	md f:\Data
	md f:\Log
	md f:\Backup

그런 다음 새 데이터베이스 및 사용자 계정 권한에 F: 드라이브를 사용하도록 SQL Server 2014를 구성합니다.

1.	시작 화면에서 **SQL Server 관리**를 입력하고 **SQL Server 2014 Management Studio**를 클릭합니다.
2.	**서버에 연결**에서 **연결**을 클릭합니다.
3.	개체 탐색기 트리 창에서 **SQL1**을 마우스 오른쪽 단추로 클릭하고 **속성**을 클릭합니다.
4.	**서버 속성** 창에서 **데이터베이스 설정**을 클릭합니다.
5.	**데이터베이스 기본 위치**를 찾아서 다음 값을 설정합니다. 
	- **데이터**에 경로 **f:\\Data**를 입력합니다.
	- **로그**에 경로 **f:\\Log**를 입력합니다.
	- **백업**에 경로 **f:\\Backup**을 입력합니다.
	- 새 데이터베이스에서만 이러한 위치를 사용합니다.
6.	**확인**을 클릭하여 창을 닫습니다.
7.	**개체 탐색기** 트리 창에서 **보안**을 엽니다.
8.	**로그인**을 마우스 오른쪽 단추로 클릭하고 **새 로그인**을 클릭합니다.
9.	**로그인 이름**에 **CORP\\User1**을 입력합니다.
10.	**서버 역할** 페이지에서 **sysadmin**을 클릭한 다음 **확인**을 클릭합니다.
11.	**개체 탐색기** 트리 창에서 **로그인**을 마우스 오른쪽 단추로 클릭하고 **새 로그인**을 클릭합니다.
12.	**일반** 페이지의 **로그인 이름**에 **CORP\\SPFarmAdmin**을 입력합니다.
13.	**서버 역할** 페이지에서 **dbcreator**를 선택한 다음 **확인**을 클릭합니다.
14.	Microsoft SQL Server Management Studio를 닫습니다.

다음은 현재 구성입니다.

![](./media/virtual-networks-setup-sharepoint-hybrid-cloud-testing/CreateSPFarmHybridCloud_2.png)

 
## 3단계: SharePoint 서버(SP1) 구성

먼저 로컬 컴퓨터의 Azure PowerShell 명령 프롬프트에서 다음 명령을 사용하여 SP1용 Azure 가상 컴퓨터를 만듭니다.

	$ServiceName="<The cloud service name for your TestVNET virtual network>"
	$cred1=Get-Credential -Message "Type the name and password of the local administrator account for SP1."
	$cred2=Get-Credential -UserName "CORP\User1" -Message "Now type the password for the CORP\User1 account."
	$image= Get-AzureVMImage | where { $_.Label -eq "SharePoint Server 2013 Trial" } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1
	$vm1=New-AzureVMConfig -Name SP1 -InstanceSize Large -ImageName $image
	$vm1 | Add-AzureProvisioningConfig -AdminUsername $cred1.GetNetworkCredential().Username -Password $cred1.GetNetworkCredential().Password -WindowsDomain -Domain "CORP" -DomainUserName "User1" -DomainPassword $cred2.GetNetworkCredential().Password -JoinDomain "corp.contoso.com"
	$vm1 | Set-AzureSubnet -SubnetNames TestSubnet
	New-AzureVM -ServiceName $ServiceName -VMs $vm1 -VNetName TestVNET

그런 다음 CORP\\User1 자격 증명을 사용하여 SP1 가상 컴퓨터에 연결합니다.

그런 다음 기본 연결 테스트에 대한 트래픽을 허용하도록 Windows 방화벽 규칙을 구성합니다. SP1의 관리자 수준 Windows PowerShell 명령 프롬프트에서 다음 명령을 실행합니다.

	Set-NetFirewallRule -DisplayName "File and Printer Sharing (Echo Request - ICMPv4-In)" -enabled True
	ping dc1.corp.contoso.com

Ping 명령을 실행한 경우 IP 주소 10.0.0.1에서 성공적인 회신 4개가 수신되어야 합니다.

그런 다음 새 SharePoint 팜 및 기본 팀 사이트에 맞게 SP1을 구성합니다.

1.	시작 화면에서 **SharePoint 2013 제품**을 입력하고 **SharePoint 2013 제품 구성 마법사**를 클릭합니다. 프로그램이 컴퓨터를 변경하도록 허용할지 묻는 메시지가 나타나면 **예**를 클릭합니다.
2.	SharePoint 제품 페이지에서 **다음**을 클릭합니다. 
3.	구성하는 동안 일부 서비스를 다시 시작해야 할 수 있음을 알리는 대화 상자에서 **예**를 클릭합니다.
4.	서버 팜에 연결 페이지에서 **새 서버 팜 만들기**를 클릭한 후 **다음**을 클릭합니다.
5.	구성 데이터베이스 설정 지정 페이지에서 **데이터베이스 서버**에 **sql1.corp.contoso.com**을 입력하고, **사용자 이름**에 **CORP\\SPFarmAdmin**을 입력하고, 암호에 SPFarmAdmin 계정 **암호**를 입력한 후 **다음**을 클릭합니다.
6.	팜 보안 설정 지정 페이지에서 **암호**와 **암호 확인** 둘 다에 ****P@ssphrase**를 입력하고 **다음**을 클릭합니다.
7.	SharePoint 중앙 관리 웹 응용 프로그램 구성 페이지에서 **다음**을 클릭합니다.
8.	SharePoint 제품 구성 마법사 완료 페이지에서 **다음**을 클릭합니다. SharePoint 제품 구성 마법사를 완료하는 데 몇 분 정도 걸릴 수 있습니다.
9.	구성 완료 페이지에서 **마침**을 클릭합니다. 완료 후 초기 팜 구성 마법사라는 탭에서 Internet Explorer가 시작됩니다.
10.	**SharePoint 기능 개선에 참여하세요.** 대화 상자에서 **아니요, 참여하지 않겠습니다.**를 클릭한 후 **확인**을 클릭합니다.
11.	**SharePoint 팜 구성 방법을 선택하세요.**에서 **마법사 시작**을 클릭합니다.
12.	SharePoint 팜 구성 페이지의 **서비스 계정**에서 **기존 관리 계정 사용**을 클릭합니다.
13.	**서비스**에서 **상태 서비스** 옆의 상자를 제외하고 모든 확인란의 선택을 취소한 후 **다음**을 클릭합니다. 완료되기 전에 작업 중 페이지가 잠시 동안 표시될 수 있습니다.
14.	사이트 모음 만들기 페이지의 **제목 및 설명**에서 **제목**에 **Contoso Corporation**을 입력하고 URL ****http://sp1**/을 지정한 다음 **확인**을 클릭합니다. 완료되기 전에 작업 중 페이지가 잠시 동안 표시될 수 있습니다. 이 단계는 URL http://sp1에 팀 사이트를 만듭니다.
15.	팜 구성 마법사를 완료합니다 페이지에서 **마침**을 클릭합니다. Internet Explorer 탭에 SharePoint 2013 중앙 관리 사이트가 표시됩니다.
16.	CORP\\User1 계정 자격 증명으로 CLIENT1 컴퓨터에 로그온한 다음 Internet Explorer를 시작합니다.
17.	주소 표시줄에 ****http://sp1/**을 입력하고 Enter 키를 누릅니다. Contoso Corporation에 대한 SharePoint 팀 사이트가 표시됩니다. 사이트를 렌더링하는 데 약간의 시간이 걸릴 수 있습니다.

다음은 현재 구성입니다.

![](./media/virtual-networks-setup-sharepoint-hybrid-cloud-testing/CreateSPFarmHybridCloud_3.png)
 
하이브리드 클라우드 환경의 SharePoint 인트라넷 팜을 테스트할 준비가 완료되었습니다.

## 다음 단계

- [프로덕션 워크로드](../virtual-machines/virtual-machines-windows-sp-intranet-overview.md)를 설정합니다.

<!---HONumber=AcomDC_0323_2016-->