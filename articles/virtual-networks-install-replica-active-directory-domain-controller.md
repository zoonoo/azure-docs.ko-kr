<properties urlDisplayName="Replica domain controller" pageTitle="Azure에서 복제본 도메인 컨트롤러 설치" metaKeywords="" description="회사 Active Directory 포리스트에서 Azure 가상 컴퓨터에 도메인 컨트롤러를 설치하는 방법에 대해 설명하는 자습서입니다." metaCanonical="" services="virtual-network" documentationCenter="" title="Install a Replica Active Directory Domain Controller in Azure Virtual Networks" authors="Justinha" solutions="" writer="Justinha" manager="TerryLan" editor="LisaToft" />

<tags ms.service="virtual-network" ms.workload="infrastructure-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/17/2014" ms.author="Justinha" />




#Azure 가상 네트워크에서 복제 Active Directory 도메인 컨트롤러 설치

이 자습서에서는 [Azure 가상 네트워크](http://msdn.microsoft.com/ko-kr/library/windowsazure/jj156007.aspx)의 VM(가상 컴퓨터)에 회사 Active Directory 포리스트의 추가 도메인 컨트롤러를 설치하는 단계를 안내합니다. 이 자습서에서 VM의 가상 네트워크는 회사의 네트워크에 연결됩니다. Azure 가상 네트워크에 AD DS(Active Directory 도메인 서비스) 설치에 대한 개념적인 지침은 [Azure 가상 컴퓨터에 Windows Server Active Directory를 배포하기 위한 지침](http://msdn.microsoft.com/ko-kr/library/windowsazure/jj156090.aspx)을 참조하세요.

##목차##

* [필수 조건](#Prerequisites)
* [1단계: YourPrimaryDC의 고정 IP 주소 확인](#verifystaticip)
* [2단계: 회사 포리스트 설치](#installforest)
* [3단계: 서브넷 및 사이트 만들기](#subnets)
* [4단계: CloudSite에 추가 도메인 컨트롤러 설치](#cloudsite)
* [5단계: 설치 유효성 검사](#validate)
* [6단계: 부팅 시 도메인에 가입되는 가상 컴퓨터 프로비전](#provisionvm)
* [7단계: 도메인 컨트롤러 백업](#backup)
* [8단계: 인증 및 권한 부여 테스트](#test)


<h2><a id="Prerequisites"></a>필수 조건</h2>

Azure 가상 네트워크 및 회사 네트워크 간에 구성된 -	[관리 포털에서 사이트 간 VPN 구성](http://msdn.microsoft.com/ko-kr/library/dn133795.aspx)
-	가상 네트워크에서 클라우드 서비스 만들기
-	클라우드 서비스에 가상 네트워크의 일부로 VM 두 개를 배포(VM을 배치하려는 서브넷 지정)합니다. 자세한 내용은 [가상 네트워크에 가상 컴퓨터 추가](http://azure.microsoft.com/ko-kr/documentation/articles/virtual-networks-add-virtual-machine/)를 참조하세요. VM에 두 개의 데이터 디스크를 연결하려면 VM은 L 크기 이상이어야 합니다. 데이터 디스크는 다음을 복원하는 데 필요합니다.
	- Active Directory 데이터베이스 및 로그
	- 시스템 상태 및 백업
-	두 개의 VM이 포함된 회사 네트워크(YourPrimaryDC 및 FileServer)
-	외부 사용자가 Active Directory에서 계정의 이름을 확인할 수 있어야 하는 경우 배포된 DNS(Domain Name System) 인프라. 이 경우 도메인 컨트롤러에 DNS 서버를 설치하거나 Active Directory 도메인 서비스 설치 마법사에서 위임을 만들도록 허용하기 전에 DNS 영역 위임을 만들어야 합니다. DNS 영역 위임 만들기에 대한 자세한 내용은 [영역 위임 만들기](http://technet.microsoft.com/library/cc753500.aspx)를 참조하세요.
-	Azure VM에 설치하는 DC에서 DNS 클라이언트 확인 프로그램 설정을 다음과 같이 구성합니다.
	- 기본 설정 DNS 서버: 온-프레미스 DNS 서버
	- 대체 DNS 서버: 루프백 주소 또는 가능한 경우 동일한 가상 네트워크의 DC에서 실행 중인 다른 DNS 서버

<div class="dev-callout"> 
<b>참고</b>
<p>Azure 가상 네트워크의 AD DS를 지원하려면 고유한 DNS 인프라를 제공해야 합니다. 이 릴리스의 Azure 제공 DNS 인프라에서는 AD DS에서 필요로 하는 몇 가지 기능(예: 동적 SRV 리소스 레코드 등록)을 지원하지 않습니다. </p>
</div>

<div class="dev-callout"> 
<b>참고</b>
<p><a href="/ko-kr/manage/services/networking/active-directory-forest/">Azure에 새 Active Directory 포리스트 설치</a>의 단계를 이미 완료한 경우 이 자습서를 시작하기 전에 Azure 가상 네트워크의 도메인 컨트롤러에서 AD DS를 제거해야 할 수 있습니다. AD DS를 제거하는 방법에 대한 자세한 내용은 <a href="http://technet.microsoft.com/ko-kr/library/cc771844(v=WS.10).aspx">도메인에서 도메인 컨트롤러 제거</a>를 참조하세요.</p>
</div>


<h2><a id="verifystaticip"></a>1단계: YourPrimaryDC의 고정 IP 주소 확인</h2>

1. 회사 네트워크의 YourPrimaryDC에 로그온합니다.

2. 서버 관리자에서 View Network Connections를 클릭합니다.

3. LAN(Local Area Network) 연결을 마우스 오른쪽 단추로 클릭하고 속성을 클릭합니다.

4. 인터넷 프로토콜 버전 4(TCP/IPv4)를 클릭하고 속성을 클릭합니다.

5. 서버가 고정 IP 주소에 할당되었는지 확인합니다. 

	![VerifystaticIPaddressyourPrimaryDC1](./media/virtual-networks-install-replica-active-directory-domain-controller/VerifystaticIP.png)


<h2><a id="installforest"></a>2단계: 회사 포리스트 설치</h2>

1. VM의 RDP 세션에서 **시작**을 클릭하고 **dcpromo**를 입력하고 Enter 키를 누릅니다.

	![InstallCorpForest1](./media/virtual-networks-install-replica-active-directory-domain-controller/InstallCorpForest1.png)


2. Welcome 페이지에서 **다음**을 클릭합니다.

	![InstallCorpForest2](./media/virtual-networks-install-replica-active-directory-domain-controller/InstallCorpForest2.png)



3. Operating System Compatibility 페이지에서 **다음**을 클릭합니다.

	![InstallCorpForest3](./media/virtual-networks-install-replica-active-directory-domain-controller/InstallCorpForest3.png)

4. Choose a Deployment Configuration 페이지에서, **새 포리스트에 새 도메인 만들기**와 **다음**을 차례로 클릭합니다. 

	![InstallCorpForest4](./media/virtual-networks-install-replica-active-directory-domain-controller/InstallCorpForest4.png)


5. 포리스트 루트 도메인 페이지의 이름에서 포리스트 루트 도메인의 FQDN(정규화된 도메인 이름)인 **corp.contoso.com**을 입력하고 **다음**을 클릭합니다. 

	![InstallCorpForest5](./media/virtual-networks-install-replica-active-directory-domain-controller/InstallCorpForest5.png)


6. Set Forest Functional level 페이지에서 **Windows Server 2008 R2**와 **다음**을 차례로 클릭합니다.

	![InstallCorpForest6](./media/virtual-networks-install-replica-active-directory-domain-controller/InstallCorpForest6.png)

7. Additional Domain Controller Options 페이지에서 **DNS 서버**와 **다음**을 차례로 클릭합니다.

	![InstallCorpForest7](./media/virtual-networks-install-replica-active-directory-domain-controller/InstallCorpForest7.png)

8. 다음과 같은 DNS 위임 경고가 나타날 경우 **예**를 클릭합니다.

	![InstallCorpForest8](./media/virtual-networks-install-replica-active-directory-domain-controller/InstallCorpForest8.png)


9. Location for Active Directory database, log files and SYSVOL 페이지에서 파일의 위치를 입력하거나 선택하고 **다음**을 클릭합니다.

	![InstallCorpForest9](./media/virtual-networks-install-replica-active-directory-domain-controller/InstallCorpForest9.png)


10. Directory Services Restore Administrator 페이지에서 DSRM 암호를 입력하고 확인한 후 **다음**을 클릭합니다.

	![InstallCorpForest10](./media/virtual-networks-install-replica-active-directory-domain-controller/InstallCorpForest10.png)


11. 요약 페이지에서 선택한 항목을 확인하고 **다음**을 클릭합니다. 

	![InstallCorpForest11](./media/virtual-networks-install-replica-active-directory-domain-controller/InstallCorpForest11.png)

12. Active Directory 설치 마법사를 마친 후 **마침**과 **지금 다시 시작**을 차례로 클릭하여 설치를 완료합니다. 

	![InstallCorpForest12](./media/virtual-networks-install-replica-active-directory-domain-controller/InstallCorpForest12.png)



<h2><a id="subnets"></a>3단계: 서브넷 및 사이트 만들기</h2>

1. YourPrimaryDC에서 시작, 관리 도구, Active Directory 사이트 및 서비스를 차례로 클릭합니다.
2. **사이트**를 클릭하고, **서브넷**을 마우스 오른쪽 단추로 클릭한 후 **새 서브넷**을 클릭합니다.

	![CreateSubnetsandSites1](./media/virtual-networks-install-replica-active-directory-domain-controller/CreateSubnetsandSites1.png)

3. **Prefix::**에서 **10.1.0.0/24**를 입력하고, **Default-First-Site-Name** 사이트 개체를 선택하고 **확인**을 클릭합니다.

	![CreateSubnetsandSites2](./media/virtual-networks-install-replica-active-directory-domain-controller/CreateSubnetsandSites2.png)

4. **사이트**를 마우스 오른쪽 단추로 클릭하고 **새 사이트**를 클릭합니다.

	![CreateSubnetsandSites3](./media/virtual-networks-install-replica-active-directory-domain-controller/CreateSubnetsandSites3.png)


5. 이름에서 **CloudSite**를 입력하고, **DEFAULTIPSITELINK**를 선택하고 **확인**을 클릭합니다. 

	![CreateSubnetsandSites4](./media/virtual-networks-install-replica-active-directory-domain-controller/CreateSubnetsandSites4.png)


6. **확인**을 클릭하여 사이트가 만들어졌는지 확인합니다. 

	![CreateSubnetsandSites5](./media/virtual-networks-install-replica-active-directory-domain-controller/CreateSubnetsandSites5.png)

7. **서브넷**을 마우스 오른쪽 단추로 클릭한 후 **새 서브넷**을 클릭합니다.

	![CreateSubnetsandSites6](./media/virtual-networks-install-replica-active-directory-domain-controller/CreateSubnetsandSites6.png)

8. **Prefix::**에서 **10.4.2.0/24**를 입력하고 **CloudSite** 사이트 개체를 선택하고 **확인**을 클릭합니다.

	![CreateSubnetsandSites7](./media/virtual-networks-install-replica-active-directory-domain-controller/CreateSubnetsandSites7.png)


<h2><a id="cloudsite"></a>4단계: CloudSite에 추가 도메인 컨트롤러 설치</h2>

1. YourVMachine에 로그온하고, **시작**을 클릭하고, **dcpromo**를 입력하고, Enter 키를 누릅니다.

	![AddDC1](./media/virtual-networks-install-replica-active-directory-domain-controller/AddDC1.png)

2. Welcome 페이지에서 **다음**을 클릭합니다.

	![AddDC2](./media/virtual-networks-install-replica-active-directory-domain-controller/AddDC2.png)


3. Operating System Compatibility 페이지에서 **다음**을 클릭합니다.

	![AddDC3](./media/virtual-networks-install-replica-active-directory-domain-controller/AddDC3.png)

4. Choose a Deployment Configuration 페이지에서 **기존 포리스트**, **기존 도메인에 도메인 컨트롤러 추가**, **다음**을 차례로 클릭합니다.

	![AddDC4](./media/virtual-networks-install-replica-active-directory-domain-controller/AddDC4.png)


5. 네트워크 자격 증명 페이지에서 **corp.contoso.com** 도메인에 도메인 컨트롤러를 설치하고 있는지 확인하고 Domain Admins 그룹 구성원의 자격 증명을 입력(또는 corp\administrator 자격 증명 사용)합니다. 

	![AddDC5](./media/virtual-networks-install-replica-active-directory-domain-controller/AddDC5.png)


6. 도메인 선택 페이지에서 **다음**을 클릭합니다. 

	![AddDC6](./media/virtual-networks-install-replica-active-directory-domain-controller/AddDC6.png)


7. 사이트 선택 페이지에서 CloudSite가 선택되었는지 확인하고 **다음**을 클릭합니다.

	![AddDC7](./media/virtual-networks-install-replica-active-directory-domain-controller/AddDC7.png)

8. Additional Domain Controller Options 페이지에서 **다음**을 클릭합니다. 

	![AddDC8](./media/virtual-networks-install-replica-active-directory-domain-controller/AddDC8.png)


9. 정적 IP 할당 경고에서 **예, DHCP 서버에서 자동으로 할당된 IP 주소를 사용합니다(권장되지 않음)**을 클릭합니다.

	**중요** 

	Azure 가상 네트워크의 IP 주소가 동적이긴 하지만 임대는 VM 기간 동안 유지됩니다. 따라서 가상 네트워크에 설치하는 도메인 컨트롤러의 고정 IP 주소를 설정할 필요가 없습니다. VM에 고정 IP 주소를 설정하면 통신 오류가 발생합니다.


	![AddDC9](./media/virtual-networks-install-replica-active-directory-domain-controller/AddDC9.png)

10. DNS 위임 경고에 대한 메시지가 표시되면 **예**를 클릭합니다.

	![AddDC10](./media/virtual-networks-install-replica-active-directory-domain-controller/AddDC10.png)


11. Location for Active Directory database, log files and SYSVOL 페이지에서 찾아보기를 클릭하고 데이터 디스크에서 Active Directory 파일의 위치를 입력하거나 선택하고 **다음**을 클릭합니다. 

	![AddDC11](./media/virtual-networks-install-replica-active-directory-domain-controller/AddDC11.png)

12. Directory Services Restore Administrator 페이지에서 DSRM 암호를 입력하고 확인한 후 **다음**을 클릭합니다.

	![AddDC12](./media/virtual-networks-install-replica-active-directory-domain-controller/AddDC12.png)

13. Summary 페이지에서 **다음**을 클릭합니다.

	![AddDC13](./media/virtual-networks-install-replica-active-directory-domain-controller/AddDC13.png)

14. Active Directory 설치 마법사를 마친 후 **마침**과 **지금 다시 시작**을 차례로 클릭하여 설치를 완료합니다. 

	![AddDC14](./media/virtual-networks-install-replica-active-directory-domain-controller/AddDC14.png)


<h2><a id="validate"></a>5단계: 설치 유효성 검사</h2>

1. VM에 다시 연결합니다.

2. **시작**을 클릭하고 **명령 프롬프트**를 마우스 오른쪽 단추로 클릭하고 **관리자 권한으로 실행**을 클릭합니다. 

3. 다음 명령을 입력하고 Enter 키를 누릅니다.  'Dcdiag /c /v'

4. 테스트가 실행되었는지 확인합니다. 

DC가 구성된 후 다음 Windows PowerShell cmdlet을 실행하여 추가 가상 컴퓨터를 프로비전하고 프로비전되었을 때 자동으로 도메인에 가입되도록 하세요. VM을 프로비전할 때 VM에 대한 DNS 클라이언트 확인 프로그램 설정을 구성해야 합니다. 도메인, VM 이름 등을 사용자 도메인의 올바른 이름으로 대체하세요. 

Windows PowerShell 사용에 대한 자세한 내용은 [Azure PowerShell 시작](http://msdn.microsoft.com/ko-kr/library/windowsazure/jj156055.aspx) 및 [Azure 관리 Cmdlet](http://msdn.microsoft.com/ko-kr/library/windowsazure/jj152841)(영문)을 참조하세요.


<h2><a id="provisionvm"></a>6단계: 부팅 시 도메인에 가입되는 가상 컴퓨터 프로비전</h2>

1. 처음 부팅 시 도메인에 가입되는 추가 가상 컴퓨터를 만들려면 Azure PowerShell ISE를 열고, 다음 스크립트를 붙여넣고, 자리 표시자를 사용자의 값으로 대체하고 실행하세요. 

	도메인 컨트롤러의 내부 IP 주소를 확인하려면 해당 도메인 컨트롤러가 실행 중인 가상 네트워크 이름을 클릭하세요. 

	다음 예에서 도메인 컨트롤러의 내부 IP 주소는 10.4.3.1입니다. 또한 Add-AzureProvisioningConfig는 a -MachineObjectOU 매개 변수를 사용합니다. 이 매개 변수는 지정된 경우 Active Directory의 완전한 고유 이름을 필요로 하며 컨테이너의 모든 가상 컴퓨터에 대한 그룹 정책 설정을 허용합니다.

	가상 컴퓨터를 프로비전한 후 UPN(사용자 계정 이름) 형식을 사용하는 도메인 계정(예: administrator@corp.contoso.com)을 지정하여 로그온합니다. 

		#Deploy a new VM and join it to the domain
		#-------------------------------------------
		#Specify my DC's DNS IP (10.4.3.1)
		$myDNS = New-AzureDNS -Name 'ContosoDC13' -IPAddress '10.4.3.1'
		
		# OS Image to Use
		$image = 'MSFT__Sql-Server-11EVAL-11.0.2215.0-08022012-ko-kr-30GB.vhd'
		$service = 'myazuresvcindomainM1'
		$AG = 'YourAffinityGroup'
		$vnet = 'YourVirtualNetwork'
		$pwd = 'p@$$w0rd'
		$size = 'Small'
		
		#VM Configuration
		$vmname = 'MyTestVM1'
		$MyVM1 = New-AzureVMConfig -name $vmname -InstanceSize $size -ImageName $image |
		    Add-AzureProvisioningConfig -WindowsDomain -Password $pwd -Domain 'corp' -DomainPassword 'p@$$w0rd' -DomainUserName 'Administrator' -JoinDomain 'corp.contoso.com'|
		    Set-AzureSubnet -SubnetNames 'BackEnd'
		
		New-AzureVM -ServiceName $service -AffinityGroup $AG -VMs $MyVM1 -DnsSettings $myDNS -VNetName $vnet
		

<h2><a id="backup"></a>7단계: 도메인 컨트롤러 백업</h2>


1. YourVMachine에 연결합니다.

2. **시작**, **서버 관리자**, **기능 추가**를 차례로 클릭한 후 **Windows Server 백업 기능**을 선택합니다. 지침을 따라서 Windows Server 백업을 설치합니다.

3. **시작**, **Windows Server 백업**, **한 번 백업**을 차례로 클릭합니다.
 
4. **다른 옵션**과 **다음**을 차례로 클릭합니다.

5. **전체 서버**와 **다음**을 차례로 클릭합니다.

6. **로컬 드라이브**와 **다음**을 차례로 클릭합니다.

7. 운영 체제 파일 또는 Active Directory 데이터베이스를 호스트하는 대상 드라이브를 선택한 후 다음을 클릭합니다.

	![BackupDC](./media/virtual-networks-install-replica-active-directory-domain-controller/BackupDC.png)


8. 선택한 백업 설정을 확인한 후 **백업**을 클릭합니다.

<h2><a id="test"></a>8단계: 인증 및 권한 부여 테스트</h2>

1. 인증 및 권한 부여를 테스트하려면 Active Directory에 도메인 사용자 계정을 만듭니다. 
각 사이트의 클라이언트 VM에 로그온하고 VM에 공유 폴더를 만듭니다.

2. 다른 계정 및 그룹과 권한을 사용하여 공유 폴더에 대한 액세스를 테스트합니다. 

## 참고 항목

-  [Azure 가상 네트워크](http://msdn.microsoft.com/ko-kr/library/windowsazure/jj156007.aspx)

-  [Microsoft Azure IT Pro IaaS: (01) 가상 컴퓨터 기본 사항](http://channel9.msdn.com/Series/Windows-Azure-IT-Pro-IaaS/01)

-  [Microsoft Azure IT Pro IaaS: (05) 가상 네트워크 및 프레미스 간 연결 만들기](http://channel9.msdn.com/Series/Windows-Azure-IT-Pro-IaaS/05)

-  [Azure PowerShell](http://msdn.microsoft.com/ko-kr/library/windowsazure/jj156055.aspx)

-  [Azure 관리 Cmdlet(영문)](http://msdn.microsoft.com/ko-kr/library/windowsazure/jj152841)
