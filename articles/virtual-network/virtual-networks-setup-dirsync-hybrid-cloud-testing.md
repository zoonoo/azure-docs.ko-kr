<properties 
	pageTitle="Office 365 DirSync 테스트 환경 | Microsoft Azure" 
	description="IT 전문가 또는 개발 테스트용 하이브리드 클라우드에서 Office 365 디렉터리 동기화(DirSync) 서버를 구성하는 방법에 대해 알아봅니다." 
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
	ms.date="12/11/2015" 
	ms.author="josephd"/>

# 테스트용 하이브리드 클라우드에 Office 365 디렉터리 동기화(DirSync) 설치

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]리소스 관리자 모델.
 

이 항목에서는 Microsoft Azure에서 호스트되는 암호 동기화 사용 Office 365 디렉터리 동기화(DirSync)를 테스트하기 위한 하이브리드 클라우드 환경을 만드는 과정을 안내합니다. 다음은 결과 구성입니다.

![](./media/virtual-networks-setup-dirsync-hybrid-cloud-testing/CreateDirSyncHybridCloud_3.png)
 
이 구성에서는 인터넷상의 현재 위치에서 Azure 프로덕션 환경의 DirSync 서버를 시뮬레이션하며, 다음으로 구성됩니다.

- 간소화된 온-프레미스 네트워크(Corpnet 서브넷).
- Azure에서 호스트되는 크로스-프레미스 가상 네트워크(TestVNET)
- 사이트 간 VPN 연결
- Office 365 FastTrack 평가판 구독
- TestVNET 가상 네트워크에서 Azure AD Connect 도구 및 보조 도메인 컨트롤러를 실행하는 DirSync 서버

이 구성은 다음 작업을 수행할 수 있는 기초 및 일반적인 시작 지점을 제공합니다.

- 암호 동기화를 사용하여 온-프레미스 Active Directory 도메인과 동기화하는 Office 365용 응용 프로그램 개발 및 테스트
- 이 클라우드 기반 IT 작업의 테스트 수행

이 하이브리드 클라우드 테스트 환경을 설정하는 세 가지 주요 단계가 있습니다.

1.	테스트용 하이브리드 클라우드 환경 설정
2.	Office 365 FastTrack 평가판 구성
3.	DirSync 서버(DS1) 구성

아직 Azure 구독이 없는 경우에는 [Azure 평가판 사용](https://azure.microsoft.com/pricing/free-trial/)에서 무료로 가입할 수 있습니다. MSDN 구독이 있는 경우 [MSDN 구독자를 위한 Azure 혜택](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)을 참조하세요.

## 1 단계: 하이브리드 클라우드 환경 설정

[테스트용 하이브리드 클라우드 환경 설정](virtual-networks-setup-hybrid-cloud-environment-testing.md) 항목의 지침을 따르세요. 이 테스트 환경에는 APP1 서버가 Corpnet 서브넷에 있을 필요가 없으므로 지금은 종료해도 됩니다.

다음은 현재 구성입니다.

![](./media/virtual-networks-setup-dirsync-hybrid-cloud-testing/CreateDirSyncHybridCloud_1.png)

> [AZURE.NOTE] 1 단계에서는 시뮬레이션된 하이브리드 클라우드 테스트 환경 또한 설정할 수 있습니다. 자세한 내용은 [테스트용 하이브리드 클라우드 환경 설정](virtual-networks-setup-simulated-hybrid-cloud-environment-testing.md) 지침을 참조하십시오.

## 2단계: Office 365 FastTrack 평가판 구성

Office 365 FastTrack 평가판을 시작하려면 가상 회사 이름 및 Microsoft 계정이 필요합니다. Contoso라는 회사 이름의 변형을 회사 이름으로 사용하는 것이 좋습니다. 이는 Microsoft 샘플 콘텐츠에서 사용되는 가상 회사이지만 필수 사항은 아닙니다.

새 Microsoft 계정에 등록합니다. ****http://outlook.com**로 이동하여 user123@outlook.com과 같이 전자 메일 주소로 계정을 생성합니다. 이 계정을 사용하여 Office 365 FastTrack 평가판에 등록합니다.

그런 다음 새 Office 365 Enterprise E3 평가판에 등록합니다.

1.	CORP\\User1 계정 자격 증명을 사용하여 CLIENT1에 로그온합니다.
2.	Internet Explorer를 열고 ****https://go.microsoft.com/fwlink/p/?LinkID=403802**로 이동합니다.
3.	Office 365 Enterprise E3 평가판에 등록하는 과정을 단계별로 실행합니다.

**비즈니스 메일 주소**를 입력하라는 메시지가 나타나면 새 Microsoft 계정을 지정합니다.

ID를 만들라는 메시지가 나타나면 초기 Office 365 계정의 이름과 가상 회사 이름을 입력하고 암호를 입력합니다. 결과 메일 주소(예: user123@contoso123.onmicrosoft.com)와 암호를 안전한 위치에 기록해 둡니다. 3 단계에서 Azure AD Connect 구성을 완료하려면 이 정보가 필요합니다.

마치면 기본 Office 365 포털 페이지가 표시됩니다. 상단 리본에서 **관리**를 클릭한 후 **Office 365**를 클릭합니다. Office 365 관리 센터 페이지가 나타납니다. 이 페이지를 CLIENT1에서 열린 상태로 유지합니다.

다음은 현재 구성입니다.

![](./media/virtual-networks-setup-dirsync-hybrid-cloud-testing/CreateDirSyncHybridCloud_2.png)

## 3단계: DirSync 서버(DS1) 구성

먼저 로컬 컴퓨터의 Azure PowerShell 명령 프롬프트에서 다음 명령을 사용하여 DS1용 Azure 가상 컴퓨터를 만듭니다. 이러한 명령을 실행하기 전에 변수 값을 작성하고 < and > 문자를 제거합니다.

	$ServiceName="<The cloud service name for your TestVNET virtual network>"
	$cred1=Get-Credential -Message "Type the name and password of the local administrator account for DS1."
	$cred2=Get-Credential -UserName "CORP\User1" -Message "Now type the password for the CORP\User1 account."
	$image= Get-AzureVMImage | where { $_.ImageFamily -eq "Windows Server 2012 R2 Datacenter" } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1
	$vm1=New-AzureVMConfig -Name DS1 -InstanceSize Medium -ImageName $image
	$vm1 | Add-AzureProvisioningConfig -AdminUsername $cred1.GetNetworkCredential().Username -Password $cred1.GetNetworkCredential().Password -WindowsDomain -Domain "CORP" -DomainUserName "User1" -DomainPassword $cred2.GetNetworkCredential().Password -JoinDomain "corp.contoso.com"
	$vm1 | Set-AzureSubnet -SubnetNames TestSubnet
	New-AzureVM -ServiceName $ServiceName -VMs $vm1 -VNetName TestVNET

그런 다음 DS1 가상 컴퓨터에 연결합니다.

1.	Azure 관리 포털의 가상 컴퓨터 페이지에서 DS1 가상 컴퓨터에 대한 STATUS 열에 표시된 **실행 중**을 클릭합니다.
2.	작업 표시줄에서 **연결**을 클릭합니다. 
3.	DS1.rdp를 열라는 메시지가 나타나면 **열기**를 클릭합니다.
4.	원격 데스크톱 연결 메시지 상자가 포함된 메시지가 나타나면 **연결**을 클릭합니다.
5.	자격 증명을 묻는 메시지가 나타나면 다음을 사용합니다.
	- 이름: **CORP\\User1**
	- 암호: [User1 계정 암호]
6.	인증서를 참조하는 원격 데스크톱 연결 메시지 상자가 포함된 메시지가 나타나면 **예**를 클릭합니다.

그런 다음 기본 연결 테스트에 대한 트래픽을 허용하도록 Windows 방화벽 규칙을 구성합니다. DS1의 관리자 수준 Windows PowerShell 명령 프롬프트에서 다음 명령을 실행합니다.

	Set-NetFirewallRule -DisplayName "File and Printer Sharing (Echo Request - ICMPv4-In)" -enabled True
	ping dc1.corp.contoso.com

Ping 명령을 실행한 경우 IP 주소 10.0.0.1에서 성공적인 회신 4개가 수신되어야 합니다.

Windows PowerShell 명령 프롬프트에서 다음 명령을 사용하여 DS1에 .NET 3.5를 설치합니다.

	Add-WindowsFeature NET-Framework-Core

그런 다음 Office 365 평가판에 대한 디렉터리 동기화를 사용하도록 설정합니다.

1.	CLIENT1의 **Office 365 관리 센터** 페이지 왼쪽 창에서 **사용자**를 클릭한 후 **활성 사용자**를 클릭합니다.
2.	**Active Directory 동기화**를 위해 **설정**을 클릭합니다.
3.	Active Directory 동기화 설정 및 관리 페이지의 3단계에서 **활성화**를 클릭합니다.
4.	**Active Directory 동기화를 활성화하시겠습니까?**라는 메시지가 나타나면 **활성화**를 클릭합니다. 그러면 **Active Directory 동기화가 활성화되었습니다.**가 3단계에 표시됩니다.
5.	CLIENT1에서 **Active Directory 동기화 설정 및 관리 페이지**를 열린 상태로 둡니다.

그런 다음 CORP\\User1 계정으로 DC1에 로그온하여 관리자 수준 Windows PowerShell 명령 프롬프트를 엽니다. 다음 명령을 한 번에 하나씩 실행하여 contoso\_users라는 새 조직 구성 단위를 만들고 Marci Kaufman 및 Lynda Meyer에 대한 새 사용자 계정 두 개를 추가합니다.

	New-ADOrganizationalUnit -Name contoso_users -Path "DC=corp,DC=contoso,DC=com"
	New-ADUser -SamAccountName marcik -AccountPassword (Read-Host "Set user password" -AsSecureString) -name "Marci Kaufman" -enabled $true -PasswordNeverExpires $true -ChangePasswordAtLogon $false -Path "OU=contoso_users,DC=corp,DC=contoso,DC=com"
	New-ADUser -SamAccountName lyndam -AccountPassword (Read-Host "Set user password" -AsSecureString) -name "Lynda Meyer" -enabled $true -PasswordNeverExpires $true -ChangePasswordAtLogon $false -Path "OU=contoso_users,DC=corp,DC=contoso,DC=com"

각 Windows PowerShell 명령을 실행하면 새 사용자의 암호를 묻는 메시지가 표시됩니다. 이러한 암호를 기록하여 안전한 위치에 보관합니다. 나중에 필요합니다.

그런 다음 DS1에서 Azure AD Connect 도구를 설치하고 구성합니다.

1.	Internet Explorer를 열고 **주소 표시줄**에 ****https://www.microsoft.com/download/details.aspx?id=47594**를 입력한 뒤 ENTER를 누릅니다.
2.	Microsoft Azure AD Connect 설치 프로그램을 실행합니다.
3.	데스크톱에서 **Azure AD Connect**을 두 번 클릭합니다.
4.	**시작** 페이지에서 **I agree to the license terms and privacy notice(사용 약관 및 에 개인 정보 취급 방침에 동의)**를 선택하고 **계속**을 클릭합니다.
5.	**기본 설정** 페이지에서 **기본 설정 사용**을 클릭합니다.
6.	**Azure AD에 연결** 페이지에서 단계 2에서 Office 365 FastTrack 평가판을 설치할 때 만든 초기 계정의 메일 주소 및 암호를 입력합니다. **다음**을 클릭합니다.
7.	**AD DS에 연결** 페이지에서 **사용자 이름**에 **CORP\\User1**을 입력하고 **암호**에 User1 계정 암호를 입력합니다. 다음을 클릭합니다.
8.	**구성 준비 완료** 페이지에서 설정을 검토한 후 **설치**를 클릭합니다.
9.	**구성 완료** 페이지에서 **종료**를 클릭합니다.

그런 다음 CORP 도메인의 사용자 계정이 Office 365에 동기화되었는지 확인합니다. 동기화가 시작될 때까지 몇 분 정도 걸릴 수 있습니다.

CLIENT1의 **Active Directory 동기화 설정 및 관리 페이지**에서 이 페이지의 6단계에 있는 **users** 링크를 클릭합니다. 디렉터리 동기화가 성공적으로 실행되면 다음과 유사한 내용이 표시됩니다.

![](./media/virtual-networks-setup-dirsync-hybrid-cloud-testing/CreateDirSyncHybridCloud_4.png)

**상태** 열은 계정을 Active Directory 도메인과의 동기화를 통해 가져왔는지 나타냅니다.

이제 Lynda Myer Active Directory 계정과의 Office 365 암호 동기화를 확인합니다.

1.	CLIENT1의 **활성 사용자** 페이지에서 **Lynda Meyer** 계정을 선택합니다.
2.	Lynda Meyer 계정의 속성에서 **할당된 라이선스** 아래의 **편집**을 클릭합니다.
3.	**라이선스 할당** 탭의 **사용자 위치 선택**에서 위치(예: 미국)를 선택합니다.
4.	**Microsoft Office 365 Plan E3**를 선택하고 **저장**을 클릭합니다.
5.	Internet Explorer를 닫습니다.
6.	Internet Explorer를 열고 ****http://portal.microsoftonline.com**으로 이동합니다.
7.	Lynda Meyer의 Office 365 자격 증명으로 로그온합니다. 사용자 이름은 lyndam@<*가상의 이름*>.onmicrosoft.com입니다. 암호는 Lynda Meyer Active Directory 사용자 계정 암호입니다.
8.	로그온에 성공하면 **Let's make a difference today**가 표시된 Office 365 기본 포털 페이지가 나타납니다.

다음은 현재 구성입니다.

![](./media/virtual-networks-setup-dirsync-hybrid-cloud-testing/CreateDirSyncHybridCloud_3.png)
 
이제 이 환경은 Office 365 DirSync 기능을 기반으로 하는 Office 365 응용 프로그램의 테스트를 수행하거나 DS1에서 DirSync 기능 및 성능을 테스트할 준비가 완료되었습니다.

## 추가 리소스

[Microsoft Azure에서 Office 365 디렉터리 동기화(DirSync) 배포](http://technet.microsoft.com/library/dn635310.aspx)

[Office Server 및 클라우드를 사용하는 솔루션](http://technet.microsoft.com/library/dn262744.aspx)

[테스트용 하이브리드 클라우드 환경 설정](virtual-networks-setup-hybrid-cloud-environment-testing.md)

[테스트용 하이브리드 클라우드에 SharePoint 인트라넷 팜 설치](virtual-networks-setup-sharepoint-hybrid-cloud-testing.md)

[테스트를 위한 하이브리드 클라우드에서 웹 기반 LOB 응용 프로그램 설정](virtual-networks-setup-lobapp-hybrid-cloud-testing.md)

[테스트를 위한 시뮬레이션된 하이브리드 클라우드 환경 설정](virtual-networks-setup-simulated-hybrid-cloud-environment-testing.md)

[Azure 하이브리드 클라우드 테스트 환경](../virtual-machines/virtual-machines-windows-classic-hybrid-test-env.md)

[Azure 인프라 서비스 구현 지침](../virtual-machines/virtual-machines-linux-infrastructure-service-guidelines.md)

<!---HONumber=AcomDC_0323_2016-->