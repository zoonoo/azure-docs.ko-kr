<properties
	pageTitle="기본 구성 테스트 환경"
	description="Microsoft Azure에서 간소화된 인트라넷을 시뮬레이션하는 간단한 개발/테스트 환경을 만드는 방법에 대해 알아봅니다."
	documentationCenter=""
	services="virtual-machines"
	authors="JoeDavies-MSFT"
	manager="timlt"
	editor=""
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="Windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/12/2016"
	ms.author="josephd"/>

# 기본 구성 테스트 환경

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)] [Resource Manager model](virtual-machines-base-configuration-test-environment-resource-manager.md).

이 문서는 Azure 가상 네트워크의 기본 구성 테스트 환경을 만드는 방법에 대한 단계별 지침을 제공합니다.

결과 테스트 환경을 다음에 사용할 수 있습니다.

- 응용 프로그램 개발 및 테스트
- [시뮬레이션된 하이브리드 클라우드 환경](../virtual-network/virtual-networks-setup-simulated-hybrid-cloud-environment-testing.md)
- 추가 가상 컴퓨터 및 사용자 고유 디자인의 테스트 환경에 대한 Azure 서비스를 사용해 확장.

기본 구성 테스트 환경은 시뮬레이션을 간소화시키는 TestLab이라고 하는 클라우드 전용 가상 네트워크의 Corpnet 서브넷과 인터넷에 연결된 개인 인트라넷으로 구성됩니다.

![](./media/virtual-machines-base-configuration-test-environment/BC_TLG04.png)

다음과 같이 구성됩니다.

- 인트라넷 도메인 컨트롤러 및 DNS(Domain Name System) 서버로 구성된 DC1이라는 Windows Server 2012 R2 실행 Azure 가상 컴퓨터 1대
- 일반 응용 프로그램 및 웹 서버로 구성된 APP1이라는 Windows Server 2012 R2 실행 Azure 가상 컴퓨터 1대
- 인트라넷 클라이언트 역할을 하는 CLIENT1이라는 Windows Server 2012 R2 실행 Azure 가상 컴퓨터 1대

이 구성을 통해 DC1, APP1, CLIENT1 및 추가 Corpnet 서브넷 컴퓨터에서 다음을 수행할 수 있습니다.

- 인터넷에 연결하여 업데이트를 설치하고, 인터넷 리소스에 실시간으로 액세스하고, Microsoft Office 365 및 기타 Azure 서비스와 같은 공용 클라우드 기술에 참여
- 인터넷 또는 조직 네트워크에 연결된 사용자 컴퓨터에서 원격 데스크톱 연결을 사용하여 원격으로 관리

Azure에서 Windows Server 2012 R2 기본 구성 테스트 환경의 Corpnet 서브넷을 설정하는 단계는 다음 4단계로 구성됩니다.

1.	가상 네트워크 만들기
2.	DC1 구성
3.	APP1 구성
4.	CLIENT1 구성

아직 Azure 계정이 없는 경우에는 [1개월 무료 평가판](http://azure.microsoft.com/pricing/free-trial/)에서 무료로 가입할 수 있습니다. MSDN 플랫폼 구독이 있는 경우 [MSDN 플랫폼 구독자에 대한 Azure 혜택](https://azure.microsoft.com/offers/ms-azr-0062p/)을 참조하세요.

> [AZURE.NOTE]Azure의 가상 컴퓨터는 실행 중인 동안 지속적인 비용이 부과됩니다. 이 비용은 무료 평가판, MSDN 플랫폼 구독 또는 유료 구독에 대해 청구됩니다. 실행 중인 Azure 가상 컴퓨터의 비용에 대한 자세한 내용은 [가상 컴퓨터 가격 정보](http://azure.microsoft.com/pricing/details/virtual-machines/) 및 [Azure 가격 계산기](http://azure.microsoft.com/pricing/calculator/)를 참조하세요. 비용을 절감하려면 [Azure에서 테스트 환경 가상 컴퓨터의 비용 최소화](#costs)를 참조하세요.

## 1단계: 가상 네트워크 만들기

먼저, 기본 구성의 Corpnet 서브넷을 호스팅하는 TestLab 가상 네트워크를 만듭니다.

1.	[Azure 클래식 포털](https://manage.windowsazure.com)의 작업 표시줄에서 **새로 만들기 > 네트워크 서비스 > 가상 네트워크 >사용자 지정 만들기**를 차례로 클릭합니다.
2.	가상 네트워크 세부 정보 페이지에서 **이름**에 **TestLab**을 입력합니다.
3.	**위치**에서 적절한 지역을 선택합니다.
4.	다음 화살표를 클릭합니다.
5.	DNS 서버 및 VPN 연결 페이지의 **DNS 서버**에서 **이름 선택 또는 입력**에 **DC1**을 입력하고 **IP 주소**에 **10.0.0.4**를 입력한 후 다음 화살표를 클릭합니다.
6.	가상 네트워크 주소 공간 페이지의 **서브넷**에서 **서브넷-1**을 클릭하고 이름을 **Corpnet**으로 바꿉니다.
7.	Corpnet 서브넷에 대한 **CIDR(주소 수)** 열에서 **/24(256)**를 클릭합니다.
8.	완료 아이콘을 클릭합니다. 가상 네트워크가 만들어질 때까지 기다렸다가 계속 진행합니다.

이제 [Azure PowerShell을 설치 및 구성하는 방법](../install-configure-powershell.md)의 지침을 사용하여 로컬 컴퓨터에 Azure PowerShell을 설치합니다. Azure PowerShell 명령 프롬프트를 엽니다.

먼저 다음 명령을 사용하여 올바른 Azure 구독을 선택합니다. < and > 문자를 포함하여 따옴표 안의 모든 항목을 올바른 이름으로 바꿉니다.

	$subscr="<Subscription name>"
	Select-AzureSubscription -SubscriptionName $subscr –Current

**Get-AzureSubscription** 명령 표시의 **SubscriptionName** 속성에서 올바른 구독 이름을 가져올 수 있습니다.

다음으로, Azure 클라우드 서비스를 만듭니다. 클라우드 서비스는 가상 네트워크에 배치된 가상 컴퓨터의 보안 경계 및 논리적 컨테이너 역할을 합니다. 또한 Corpnet 서브넷의 가상 컴퓨터를 원격으로 연결하고 관리할 수 있는 방법을 제공합니다.

클라우드 서비스의 고유한 이름을 선택해야 합니다. *클라우드 서비스 이름은 문자, 숫자 및 하이픈만 포함할 수 있습니다. 필드의 첫 번째 및 마지막 문자는 문자 또는 숫자여야 합니다.*

예를 들어 클라우드 서비스 이름을 TestLab-*UniqueSequence*로 지정할 수 있습니다(여기서 *UniqueSequence*는 조직의 약어임). 예를 들어 조직의 이름이 Tailspin Toys인 경우 클라우드 서비스 이름을 TestLab-Tailspin으로 지정할 수 있습니다.

로컬 컴퓨터에서 다음 Azure PowerShell 명령을 사용하여 이름의 고유성을 테스트할 수 있습니다.

	Test-AzureName -Service <Proposed cloud service name>

이 명령에서 "False"가 반환되면 제안한 이름이 고유한 것입니다. 다음 명령을 사용하여 클라우드 서비스를 만듭니다.

	$loc="<the same location (region) as your TestLab virtual network>"
	New-AzureService -Service <Unique cloud service name> -Location $loc

클라우드 서비스의 실제 이름을 기록합니다.

다음으로, 가상 컴퓨터의 디스크 및 추가 데이터 디스크를 포함할 저장소 계정을 구성합니다. *소문자와 숫자만 포함된 고유한 이름을 선택해야 합니다.* 다음 Azure PowerShell 명령을 사용하여 저장소 계정 이름의 고유성을 테스트할 수 있습니다.

	Test-AzureName -Storage <Proposed storage account name>

이 명령에서 "False"가 반환되면 제안한 이름이 고유한 것입니다. 그런 다음 저장소 계정을 만들고 다음 명령에서 사용하여 구독을 설정합니다.

	$stAccount="<your storage account name>"
	New-AzureStorageAccount -StorageAccountName $stAccount -Location $loc
	Set-AzureSubscription -SubscriptionName $subscr -CurrentStorageAccountName $stAccount

다음은 현재 구성입니다.

![](./media/virtual-machines-base-configuration-test-environment/BC_TLG01.png)

## 2단계: DC1 구성

DC1은 corp.contoso.com AD DS(Active Directory 도메인 서비스) 도메인의 도메인 컨트롤러이자 TestLab 가상 네트워크의 가상 컴퓨터에 대한 DNS 서버입니다.

먼저 클라우드 서비스의 이름을 입력하고 로컬 컴퓨터의 Azure PowerShell 명령 프롬프트에서 다음 명령을 실행하여 DS1용 Azure 가상 컴퓨터를 만듭니다.

	$serviceName="<your cloud service name>"
	$cred=Get-Credential –Message "Type the name and password of the local administrator account for DC1."
	$image= Get-AzureVMImage | where { $_.ImageFamily -eq "Windows Server 2012 R2 Datacenter" } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1
	$vm1=New-AzureVMConfig -Name DC1 -InstanceSize Small -ImageName $image
	$vm1 | Add-AzureProvisioningConfig -Windows -AdminUsername $cred.GetNetworkCredential().Username -Password $cred.GetNetworkCredential().Password
	$vm1 | Add-AzureDataDisk -CreateNew -DiskSizeInGB 20 -DiskLabel "AD" -LUN 0 -HostCaching None
	$vm1 | Set-AzureSubnet -SubnetNames Corpnet
	$vm1 | Set-AzureStaticVNetIP -IPAddress 10.0.0.4
	New-AzureVM –ServiceName $serviceName -VMs $vm1 -VNetName TestLab

그런 다음 DC1 가상 컴퓨터에 연결합니다.

1.	Azure 클래식 포털의 왼쪽 창에서 **가상 컴퓨터**를 클릭하고 DC1 가상 컴퓨터에 대한 **상태** 열에서 **시작**을 클릭합니다.  
2.	작업 표시줄에서 **연결**을 클릭합니다.
3.	DC1.rdp를 열라는 메시지가 나타나면 **열기**를 클릭합니다.
4.	원격 데스크톱 연결 메시지 상자가 포함된 메시지가 나타나면 **연결**을 클릭합니다.
5.	자격 증명을 묻는 메시지가 나타나면 다음을 사용합니다.
- 이름: **DC1\**[로컬 관리자 계정 이름]
- 암호: [로컬 관리자 계정 암호]
6.	인증서를 참조하는 원격 데스크톱 연결 메시지 상자가 포함된 메시지가 나타나면 **예**를 클릭합니다.

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

다음으로, DC1을 corp.contoso.com 도메인의 도메인 컨트롤러 및 DNS 서버로 구성합니다. 관리자 수준 Windows PowerShell 명령 프롬프트에서 다음 명령을 실행합니다.

	Install-WindowsFeature AD-Domain-Services -IncludeManagementTools
	Install-ADDSForest -DomainName corp.contoso.com -DatabasePath "F:\NTDS" -SysvolPath "F:\SYSVOL" -LogPath "F:\Logs"

DC1이 다시 시작된 후 DC1 가상 컴퓨터에 다시 연결합니다.

1.	Azure 클래식 포털의 가상 컴퓨터 페이지에서 DC1 가상 컴퓨터에 대한 **상태** 열의 **실행**을 클릭합니다.
2.	작업 표시줄에서 **연결**을 클릭합니다.
3.	DC1.rdp를 열라는 메시지가 나타나면 **열기**를 클릭합니다.
4.	원격 데스크톱 연결 메시지 상자가 포함된 메시지가 나타나면 **연결**을 클릭합니다.
5.	자격 증명을 묻는 메시지가 나타나면 다음을 사용합니다.
- 이름: **CORP\**[로컬 관리자 계정 이름]
- 암호: [로컬 관리자 계정 암호]
6.	인증서를 참조하는 원격 데스크톱 연결 메시지 상자가 포함된 메시지가 나타나면 **예**를 클릭합니다.

다음으로, Active Directory에서 CORP 도메인 구성원 컴퓨터에 로그인할 때 사용할 사용자 계정을 만듭니다. 관리자 수준 Windows PowerShell 명령 프롬프트에서 다음 명령을 한 번에 하나씩 실행합니다.

	New-ADUser -SamAccountName User1 -AccountPassword (read-host "Set user password" -assecurestring) -name "User1" -enabled $true -PasswordNeverExpires $true -ChangePasswordAtLogon $false
	Add-ADPrincipalGroupMembership -Identity "CN=User1,CN=Users,DC=corp,DC=contoso,DC=com" -MemberOf "CN=Enterprise Admins,CN=Users,DC=corp,DC=contoso,DC=com","CN=Domain Admins,CN=Users,DC=corp,DC=contoso,DC=com"

첫 번째 명령을 실행하면 User1 계정 암호를 입력하라는 메시지가 나타납니다. 이 계정은 모든 CORP 도메인 구성원 컴퓨터에 대한 원격 데스크톱 연결에 사용되므로 강력한 암호를 선택해야 합니다. 암호 강도를 확인하려면 [암호 검사기: 강력한 암호 사용](https://www.microsoft.com/security/pc-security/password-checker.aspx)을 참조하세요. User1 계정 암호를 기록하고 안전한 위치에 저장합니다.

CORP\\User1 계정을 사용하여 DC1 가상 컴퓨터에 다시 연결합니다.

다음으로, Ping 도구에 대한 트래픽을 허용하려면 관리자 수준 Windows PowerShell 명령 프롬프트에서 다음 명령을 실행합니다.

	Set-NetFirewallRule -DisplayName "File and Printer Sharing (Echo Request - ICMPv4-In)" -enabled True

다음은 현재 구성입니다.

![](./media/virtual-machines-base-configuration-test-environment/BC_TLG02.png)

## 3단계: APP1 구성

APP1에서는 웹 및 파일 공유 서비스를 제공합니다.

먼저 클라우드 서비스의 이름을 입력하고 로컬 컴퓨터의 Azure PowerShell 명령 프롬프트에서 다음 명령을 실행하여 APP1용 Azure 가상 컴퓨터를 만듭니다.

	$serviceName="<your cloud service name>"
	$cred1=Get-Credential –Message "Type the name and password of the local administrator account for APP1."
	$cred2=Get-Credential –UserName "CORP\User1" –Message "Now type the password for the CORP\User1 account."
	$image= Get-AzureVMImage | where { $_.ImageFamily -eq "Windows Server 2012 R2 Datacenter" } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1
	$vm1=New-AzureVMConfig -Name APP1 -InstanceSize Small -ImageName $image
	$vm1 | Add-AzureProvisioningConfig -AdminUsername $cred1.GetNetworkCredential().Username -Password $cred1.GetNetworkCredential().Password -WindowsDomain -Domain "CORP" -DomainUserName "User1" -DomainPassword $cred2.GetNetworkCredential().Password -JoinDomain "corp.contoso.com"
	$vm1 | Set-AzureSubnet -SubnetNames Corpnet
	New-AzureVM –ServiceName $serviceName -VMs $vm1 -VNetName TestLab

다음으로, CORP\\User1 자격 증명을 사용하여 APP1 가상 컴퓨터에 연결하고 관리자 수준 Windows PowerShell 명령 프롬프트를 엽니다.

APP1과 DC1 간의 이름 확인 및 네트워크 통신을 확인하려면 **ping dc1.corp.contoso.com** 명령을 실행하고 4개의 응답을 확인합니다.

Windows PowerShell 명령 프롬프트에서 다음 명령을 사용하여 APP1을 웹 서버로 설정합니다.

	Install-WindowsFeature Web-WebServer –IncludeManagementTools

다음 명령을 사용하여 APP1에서 공유 폴더를 만들고 해당 폴더 내에 텍스트 파일을 만듭니다.

	New-Item -path c:\files -type directory
	Write-Output "This is a shared file." | out-file c:\files\example.txt
	New-SmbShare -name files -path c:\files -changeaccess CORP\User1

다음은 현재 구성입니다.

![](./media/virtual-machines-base-configuration-test-environment/BC_TLG03.png)

## 4단계: CLIENT1 구성

CLIENT1은 Contoso 인트라넷에서 일반적인 랩톱, 태블릿 또는 데스크톱 컴퓨터 역할을 합니다.

먼저 클라우드 서비스의 이름을 입력하고 로컬 컴퓨터의 Azure PowerShell 명령 프롬프트에서 다음 명령을 실행하여 CLIENT1용 Azure 가상 컴퓨터를 만듭니다.

	$serviceName="<your cloud service name>"
	$cred1=Get-Credential –Message "Type the name and password of the local administrator account for CLIENT1."
	$cred2=Get-Credential –UserName "CORP\User1" –Message "Now type the password for the CORP\User1 account."
	$image= Get-AzureVMImage | where { $_.ImageFamily -eq "Windows Server 2012 R2 Datacenter" } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1
	$vm1=New-AzureVMConfig -Name CLIENT1 -InstanceSize Small -ImageName $image
	$vm1 | Add-AzureProvisioningConfig -AdminUsername $cred1.GetNetworkCredential().Username -Password $cred1.GetNetworkCredential().Password -WindowsDomain -Domain "CORP" -DomainUserName "User1" -DomainPassword $cred2.GetNetworkCredential().Password -JoinDomain "corp.contoso.com"
	$vm1 | Set-AzureSubnet -SubnetNames Corpnet
	New-AzureVM –ServiceName $serviceName -VMs $vm1 -VNetName TestLab

그런 다음 CORP\\User1 자격 증명을 사용하여 CLIENT1 가상 컴퓨터에 연결합니다.

CLIENT1과 DC1 간의 이름 확인 및 네트워크 통신을 확인하려면 Windows PowerShell 명령 프롬프트에서 **ping dc1.corp.contoso.com** 명령을 실행하고 4개의 응답을 확인합니다.

다음으로, CLIENT1에서 APP1의 웹 및 파일 공유 리소스에 액세스할 수 있는지 확인합니다.

1.	서버 관리자의 트리 창에서 **로컬 서버**를 클릭합니다.
2.	**CLIENT1에 대한 속성**에서 **IE 보안 강화 구성** 옆의 **설정**을 클릭합니다.
3.	**Internet Explorer 보안 강화 구성**에서 **관리자** 및 **사용자**에 대해 **해제**를 클릭한 후 **확인**을 클릭합니다.
4.	시작 화면에서 **Internet Explorer**를 클릭한 후 **확인**을 클릭합니다.
5.	주소 표시줄에 ****http://app1.corp.contoso.com/**을 입력하고 Enter 키를 누릅니다. APP1에 대한 기본 IIS(인터넷 정보 서비스) 웹 페이지가 표시됩니다.
6.	바탕 화면 작업 표시줄에서 파일 탐색기 아이콘을 클릭합니다.
7.	주소 표시줄에 **\\\app1\\Files**를 입력하고 Enter 키를 누릅니다.
8.	Files 공유 폴더의 내용이 포함된 폴더 창이 표시됩니다.
9.	**Files** 공유 폴더 창에서 **Example.txt** 파일을 두 번 클릭합니다. Example.txt 파일의 내용이 표시됩니다.
10.	**example.txt - 메모장** 및 **Files** 공유 폴더 창을 닫습니다.

최종 구성은 다음과 같습니다.

![](./media/virtual-machines-base-configuration-test-environment/BC_TLG04.png)

Azure 기본 구성이 응용 프로그램 개발 및 테스트 또는 추가적인 테스트 환경에 대한 준비를 마쳤습니다.

## 다음 단계

- [시뮬레이트된 하이브리드 클라우드 환경](../virtual-network/virtual-networks-setup-simulated-hybrid-cloud-environment-testing.md)을 설정하여 하이브리드 구성을 테스트합니다.

## <a id="costs"></a>Azure에서 테스트 환경 가상 컴퓨터의 비용 최소화

실행 중인 테스트 환경 가상 컴퓨터의 비용을 최소화하려면 다음 중 하나를 수행합니다.

- 테스트 환경을 만들고 가능한 한 신속하게 필요한 테스트 및 데모를 수행합니다. 완료되면 테스트 환경 가상 컴퓨터를 삭제합니다.
- 테스트 환경 가상 컴퓨터를 할당 취소된 상태로 종료합니다.

Azure PowerShell을 사용하여 가상 컴퓨터를 종료하려면 클라우드 서비스 이름을 입력하고 다음 명령을 실행합니다.

	$serviceName="<your cloud service name>"
	Stop-AzureVM -ServiceName $serviceName -Name "CLIENT1"
	Stop-AzureVM -ServiceName $serviceName -Name "APP1"
	Stop-AzureVM -ServiceName $serviceName -Name "DC1" -Force -StayProvisioned


중지됨(할당 취소됨) 상태에서 시작할 때 가상 컴퓨터가 제대로 작동하도록 하려면 다음 순서대로 시작해야 합니다.

1.	DC1
2.	APP1
3.	CLIENT1

Azure PowerShell을 사용하여 가상 컴퓨터를 순서대로 시작하려면 클라우드 서비스 이름을 입력하고 다음 명령을 실행합니다.

	$serviceName="<your cloud service name>"
	Start-AzureVM -ServiceName $serviceName -Name "DC1"
	Start-AzureVM -ServiceName $serviceName -Name "APP1"
	Start-AzureVM -ServiceName $serviceName -Name "CLIENT1"

<!---HONumber=AcomDC_0114_2016-->