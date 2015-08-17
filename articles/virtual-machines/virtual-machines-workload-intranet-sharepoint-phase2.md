<properties
	pageTitle="SharePoint 인트라넷 팜 작업 2단계: 도메인 컨트롤러 구성"
	description="Azure 인프라 서비스의 SQL Server AlwaysOn 가용성 그룹을 사용하여 인트라넷 전용 SharePoint 2013 팜을 배포하는 이 두 번째 단계에서는 두 Active Directory 도메인 컨트롤러를 만들고 구성합니다."
	documentationCenter=""
	services="virtual-machines"
	authors="JoeDavies-MSFT"
	manager="timlt"
	editor=""
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows-sharepoint"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/21/2015"
	ms.author="josephd"/>

# SharePoint 인트라넷 팜 작업 2단계: 도메인 컨트롤러 구성

Azure 인프라 서비스의 SQL Server AlwaysOn 가용성 그룹을 사용하여 인트라넷 전용 SharePoint 2013 팜을 배포하는 이 단계에서는 서비스 관리에서 Azure 가상 네트워크의 도메인 컨트롤러 두 개를 구성합니다. 그런 다음 VPN 또는 Azure ExpressRoute 연결을 통해 인증 트래픽을 온-프레미스 네트워크로 전송하는 대신 Azure 가상 네트워크에서 SharePoint 팜 리소스에 대한 클라이언트 웹 요청을 인증할 수 있습니다.

[3단계](virtual-machines-workload-intranet-sharepoint-phase3.md)로 진행하기 전에 이 단계를 완료해야 합니다. 전체 단계를 보려면 [Azure에서 SQL Server AlwaysOn 가용성 그룹을 사용하여 SharePoint 배포](virtual-machines-workload-intranet-sharepoint-overview.md)를 참조하세요.

## Azure에서 도메인 컨트롤러 가상 컴퓨터 만들기

먼저 표 M의 **가상 컴퓨터 이름** 열에 정보를 입력하고 **최소 크기** 열에서 필요에 따라 가상 컴퓨터 크기를 수정해야 합니다.

항목 | 가상 컴퓨터 이름 | 갤러리 이미지 | 최소 크기
--- | --- | --- | ---
1\. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_(DC1 등의 첫 번째 도메인 컨트롤러) | Windows Server 2012 R2 Datacenter | A2(중형)
2\. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_(DC2 등의 두 번째 도메인 컨트롤러) | Windows Server 2012 R2 Datacenter | A2(중형)
3\. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_(SQL1 등의 첫 번째 SQL Server 컴퓨터) | Microsoft SQL Server 2014 Enterprise – Windows Server 2012 R2 | 	A7
4\. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_(SQL2 등의 두 번째 SQL Server 컴퓨터) | Microsoft SQL Server 2014 Enterprise – Windows Server 2012 R2 | 	A7
5\. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_(MN1 등의 클러스터에 대한 주 노드 감시) | Windows Server 2012 R2 Datacenter | A1(소형)
6\. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_(APP1 등의 첫 번째 SharePoint 응용 프로그램 서버) | Microsoft SharePoint Server 2013 평가판 – Windows Server 2012 R2 | A4(초대형)
7\. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_(APP2 등의 두 번째 SharePoint 응용 프로그램 서버) | Microsoft SharePoint Server 2013 평가판 – Windows Server 2012 R2 | A4(초대형)
8\. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_(WEB1 등의 첫 번째 SharePoint 웹 서버) | Microsoft SharePoint Server 2013 평가판 – Windows Server 2012 R2 | A4(초대형)
9\. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_(WEB2 등의 두 번째 SharePoint 웹 서버) | Microsoft SharePoint Server 2013 평가판 – Windows Server 2012 R2 | A4(초대형)

**표 M – Azure의 SharePoint 2013 인트라넷 팜용 가상 컴퓨터**

전체 가상 컴퓨터 크기 목록은 [가상 컴퓨터 크기](virtual-machines-size-specs.md)를 참조하세요.

Azure PowerShell 명령의 다음 블록을 사용하여 두 도메인 컨트롤러용 가상 컴퓨터를 만듭니다. < and > 문자를 제거하고 변수의 값을 지정합니다. 이 Azure PowerShell 명령 집합은 다음 표의 값을 사용합니다.

- 가상 컴퓨터의 경우 표 M
- 가상 네트워크 설정의 경우 표 V
- 서브넷의 경우 표 S
- 가용성 집합의 경우 표 A
- 클라우드 서비스의 경우 표 C

표 V, S, A, C는 [1단계: Azure 구성](virtual-machines-workload-intranet-sharepoint-phase1.md)에서 정의했습니다.

적절한 값을 모두 입력한 후 Azure PowerShell 명령 프롬프트에서 완성된 블록을 실행합니다.

	# Create the first domain controller
	$vmName="<Table M – Item 1 - Virtual machine name column>"
	$vmSize="<Table M – Item 1 - Minimum size column, specify one: Small, Medium, Large, ExtraLarge, A5, A6, A7, A8, A9>"
	$availSet="<Table A – Item 1 – Availability set name column>"
	$image= Get-AzureVMImage | where { $_.ImageFamily -eq "Windows Server 2012 R2 Datacenter" } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1
	$vm1=New-AzureVMConfig -Name $vmName -InstanceSize $vmSize -ImageName $image -AvailabilitySetName $availSet

	$cred=Get-Credential –Message "Type the name and password of the local administrator account for the first domain controller."
	$vm1 | Add-AzureProvisioningConfig -Windows -AdminUsername $cred.GetNetworkCredential().Username -Password $cred.GetNetworkCredential().Password

	$diskSize=<size of the additional data disk in GB>
	$diskLabel="<the label on the disk>"
	$lun=<Logical Unit Number (LUN) of the disk>
	$vm1 | Add-AzureDataDisk -CreateNew -DiskSizeInGB $diskSize -DiskLabel $diskLabel -LUN $lun -HostCaching None

	$subnetName="<Table S – Item 1 – Subnet name column>"
	$vm1 | Set-AzureSubnet -SubnetNames $subnetName

	$vm1 | Set-AzureStaticVNetIP -IPAddress <Table V – Item 6 – Value column>

	$serviceName="<Table C – Item 1 – Cloud service name column>"
	$vnetName="<Table V – Item 1 – Value column>"
	New-AzureVM –ServiceName $serviceName -VMs $vm1 -VNetName $vnetName

	# Create the second domain controller
	$vmName="<Table M – Item 2 - Virtual machine name column>"
	$vmSize="<Table M – Item 2 - Minimum size column, specify one: Small, Medium, Large, ExtraLarge, A5, A6, A7, A8, A9>"
	$image= Get-AzureVMImage | where { $_.ImageFamily -eq "Windows Server 2012 R2 Datacenter" } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1

	$vm1=New-AzureVMConfig -Name $vmName -InstanceSize $vmSize -ImageName $image -AvailabilitySetName $availSet

	$cred=Get-Credential –Message "Type the name and password of the local administrator account for the second domain controller."
	$vm1 | Add-AzureProvisioningConfig -Windows -AdminUsername $cred.GetNetworkCredential().Username -Password $cred.GetNetworkCredential().Password

	$diskSize=<size of the additional data disk in GB>
	$diskLabel="<the label on the disk>"
	$lun=<Logical Unit Number (LUN) of the disk>
	$vm1 | Add-AzureDataDisk -CreateNew -DiskSizeInGB $diskSize -DiskLabel $diskLabel -LUN $lun -HostCaching None

	$vm1 | Set-AzureSubnet -SubnetNames $subnetName

	$vm1 | Set-AzureStaticVNetIP -IPAddress <Table V – Item 7 – Value column>

	New-AzureVM –ServiceName $serviceName -VMs $vm1 -VNetName $vnetName

## 첫 번째 도메인 컨트롤러 구성

로컬 관리자 계정의 자격 증명을 사용하여 첫 번째 도메인 컨트롤러 컴퓨터에 로그온합니다.

### <a id="logon"></a>원격 데스크톱 연결을 사용하여 가상 컴퓨터에 로그온하려면

1.	Azure 포털의 왼쪽 패널에서 **가상 컴퓨터**를 클릭합니다.
2.	VM에 연결하려면 VM 이름 옆의 **상태** 열에서 **실행**을 클릭합니다.
3.	페이지 아래쪽의 명령 모음에서 **연결**을 클릭합니다.
4.	포털에서 .rdp 파일을 검색 중이라는 알림이 표시됩니다. **확인**을 클릭합니다.
5.	manage.windowsazure.com에서 ComputerName.rdp를 열거나 저장할지를 묻는 브라우저 대화 상자가 나타납니다. **열기**를 클릭합니다.
6.	**원격 데스크톱 연결** 대화 상자에서 **연결**을 클릭합니다.
7.	**Windows 보안** 대화 상자에서 **다른 계정 사용**을 클릭합니다.
8.	**사용자 이름**에 VM의 이름 및 VM과 함께 만든 로컬 관리자 계정(로컬 컴퓨터 계정)의 사용자 이름을 입력합니다. *ComputerName*\*LocalAdministratorAccountName* 형식을 사용합니다.
9.	**암호**에는 로컬 관리자 계정의 암호를 입력합니다.
10.	**확인**을 클릭합니다.
11.	**원격 데스크톱 연결** 대화 상자에서 **예**를 클릭합니다. 새 컴퓨터의 바탕 화면이 원격 데스크톱 세션 창에 표시됩니다.

다음으로는 첫 번째 도메인 컨트롤러에 데이터 디스크를 더 추가해야 합니다.

### <a id="datadisk"></a>빈 디스크를 초기화하려면

1.	Server Manager의 왼쪽 창에서 **파일 및 저장소 서비스**를 클릭한 다음 **디스크**를 클릭합니다.
2.	내용 창의 **디스크** 그룹에서 **디스크 2**(**파티션**이 **알 수 없음**으로 설정됨)를 클릭합니다.
3.	**작업**을 클릭한 후 **새 볼륨**을 클릭합니다.
4.	새 볼륨 마법사를 **시작하기 전**에 페이지에서 **다음**을 클릭합니다.
5.	**서버 및 디스크 선택** 페이지에서 **디스크 2**를 클릭하고 **다음**을 클릭합니다. 메시지가 표시되면 **확인**을 클릭합니다.
6.	**볼륨 크기를 선택합니다** 페이지에서 **다음**을 클릭합니다.
7.	**드라이브 문자 또는 폴더에 할당** 페이지에서 **다음**을 클릭합니다.
8.	**파일 시스템 설정 선택** 페이지에서 **다음**을 클릭합니다.
9.	**선택 확인** 페이지에서 **만들기**를 클릭합니다.
10.	초기화가 완료되면 **닫기**를 클릭합니다.

다음으로 조직 네트워크 위치에 대한 첫 번째 도메인 컨트롤러의 연결을 테스트합니다.

### <a id="testconn"></a>연결을 테스트하려면

1.	바탕 화면에서 Windows PowerShell 명령 프롬프트를 엽니다.
2.	**ping** 명령을 사용하여 조직 네트워크의 리소스 이름과 IP 주소에 대해 ping을 실행합니다.

이 절차를 통해 DNS 이름 확인이 정상적으로 작동하는지, 즉 온-프레미스 DNS 서버에서 가상 컴퓨터가 올바르게 구성되어 있는지와 크로스-프레미스 가상 네트워크에 대한 패킷 송수신이 가능한지를 확인할 수 있습니다.

다음으로 첫 번째 도메인 컨트롤러의 Windows PowerShell 명령 프롬프트에서 다음 명령을 실행합니다.

	$domname="<DNS domain name of the domain for which this computer will be a domain controller>"
	Install-WindowsFeature AD-Domain-Services -IncludeManagementTools
	Install-ADDSDomainController -InstallDns –DomainName $domname  -DatabasePath "F:\NTDS" -SysvolPath "F:\SYSVOL" -LogPath "F:\Logs"

컴퓨터가 다시 시작됩니다.

## 두 번째 도메인 컨트롤러 구성

로컬 관리자 계정의 자격 증명을 사용하여 두 번째 도메인 컨트롤러 컴퓨터에 로그온합니다. 해당 지침은 [원격 데스크톱 연결을 사용하여 가상 컴퓨터에 로그온](#logon) 절차를 참조하세요.

다음으로는 두 번째 도메인 컨트롤러에 데이터 디스크를 더 추가해야 합니다. [빈 디스크를 초기화하려면](#datadisk) 절차를 참조하세요.

그런 후에 두 번째 도메인 컨트롤러에서 조직 네트워크 위치로의 연결을 테스트합니다. [연결을 테스트하려면](#testconn) 절차를 참조하세요. 이 절차를 통해 DNS 이름 확인이 정상적으로 작동하는지, 즉 온-프레미스 DNS 서버에서 가상 컴퓨터가 올바르게 구성되어 있는지와 크로스-프레미스 가상 네트워크에 대한 패킷 송수신이 가능한지를 확인할 수 있습니다.

다음으로 두 번째 도메인 컨트롤러의 Windows PowerShell 명령 프롬프트에서 다음 명령을 실행합니다.

	$domname="<DNS domain name of the domain for which this computer will be a domain controller>"
	Install-WindowsFeature AD-Domain-Services -IncludeManagementTools
	Install-ADDSDomainController -InstallDns –DomainName $domname  -DatabasePath "F:\NTDS" -SysvolPath "F:\SYSVOL" -LogPath "F:\Logs"

컴퓨터가 다시 시작됩니다.

## SharePoint 팜 계정 및 권한 구성

SharePoint 팜에는 다음 사용자 계정이 필요합니다.

- sp\_farm: SharePoint 팜 관리용 사용자 계정입니다.
- sp\_farm\_db: SQL Server 인스턴스에 대한 sysadmin 권한이 있는 사용자 계정입니다.
- sp\_install: 역할 및 기능 설치에 필요한 도메인 관리 권한이 있는 사용자 계정입니다.
- sqlservice: SQL Server 인스턴스를 실행할 수 있는 사용자 계정입니다.

다음으로 도메인 컨트롤러가 멤버로 속해 있는 도메인의 도메인 관리자 계정을 사용하여 원하는 컴퓨터에 로그온한 다음 관리자 수준 Windows PowerShell 명령 프롬프트를 열고 다음 명령을 *한 번에 하나씩* 실행합니다.

	New-ADUser -SamAccountName sp_farm -AccountPassword (read-host "Set user password" -assecurestring) -name "sp_farm" -enabled $true -PasswordNeverExpires $true -ChangePasswordAtLogon $false

	New-ADUser -SamAccountName sp_farm_db -AccountPassword (read-host "Set user password" -assecurestring) -name "sp_farm_db" -enabled $true -PasswordNeverExpires $true -ChangePasswordAtLogon $false

	New-ADUser -SamAccountName sp_install -AccountPassword (read-host "Set user password" -assecurestring) -name "sp_install" -enabled $true -PasswordNeverExpires $true -ChangePasswordAtLogon $false

	New-	ADUser -SamAccountName sqlservice -AccountPassword (read-host "Set user password" -assecurestring) -name "sqlservice" -enabled $true -PasswordNeverExpires $true -ChangePasswordAtLogon $false

각 명령에 대해 암호를 입력하라는 메시지가 표시됩니다. 이러한 계정 이름과 암호를 기록하여 안전한 위치에 보관합니다.

그런 다음 아래 단계를 수행하여 새 사용자 계정에 계정 속성을 더 추가합니다

1.	시작 화면에서 **Active Directory 사용자**를 입력하고 **Active Directory 사용자 및 컴퓨터**를 클릭합니다.
2.	트리 창에서 도메인을 열고 **사용자**를 클릭합니다.
3.	내용 창에서 **sp\_install**을 마우스 오른쪽 단추로 클릭하고 **그룹에 추가**를 클릭합니다.
4.	**그룹 선택** 대화 상자에서 **도메인 관리자**를 입력하고 **확인**을 두 번 클릭합니다.
5.	대화 상자에서 **고급 기능 보기 및 클릭**을 클릭합니다. 이 옵션을 사용하면 Active Directory 개체에 대해 속성 창에서 숨겨진 컨테이너와 탭을 모두 표시할 수 있습니다.
6.	도메인 이름을 마우스 오른쪽 단추로 클릭하고 **속성**을 클릭합니다.
7.	**속성** 대화 상자에서 **보안** 탭을 클릭하고 **고급** 단추를 클릭합니다.
8.	**<YourDomain> 고급 보안 설정** 창에서 **추가**를 클릭합니다.
9.	**<YourDomain> 권한 항목** 창에서 **보안 주체 선택**을 클릭합니다.
10.	텍스트 상자에 **<YourDomain>\\sp\_install**을 입력하고 **확인**을 클릭합니다.
11.	**컴퓨터 개체 만들기**에 대해 **허용**을 선택하고 **확인**을 세 번 클릭합니다.

다음으로 가상 네트워크의 DNS 서버를 업데이트합니다. 그러면 Azure에서 두 새 도메인 컨트롤러의 IP 주소를 DNS 서버로 사용하도록 가상 컴퓨터에 할당합니다. 이 절차에서는 가상 네트워크 설정에 대해 표 V의 값을 사용합니다.

1.	Azure 포털의 왼쪽 창에서 **네트워크**를 클릭하고 가상 네트워크의 이름(표 V - 항목 1 - 값 열)을 클릭합니다.
2.	**Configure**를 클릭합니다.
3.	**DNS 서버**에서 온-프레미스 네트워크에 있는 DNS 서버에 해당하는 항목을 제거합니다.
4.	**DNS 서버**에서 다음 두 표 항목의 이름 및 IP 주소를 사용하여 항목 두 개를 추가합니다.
 - 표 V – 항목 6 – 값 열
 - 표 V – 항목 7 – 값 열
5.	아래쪽의 명령 모음에서 **저장**을 클릭합니다.
6.	Azure 포털의 왼쪽 창에서 **가상 컴퓨터**를 클릭한 다음 첫 번째 도메인 컨트롤러 이름 옆에 있는 **상태** 열을 클릭합니다.
7.	명령 모음에서 **다시 시작**을 클릭합니다.
8.	첫 번째 도메인 컨트롤러가 시작되면 두 번째 도메인 컨트롤러 이름 옆의 **상태** 열을 클릭합니다.
9.	명령 모음에서 **다시 시작**을 클릭합니다. 두 번째 도메인 컨트롤러가 시작될 때까지 기다립니다.

여기서는 두 도메인 컨트롤러를 다시 시작하여 온-프레미스 DNS 서버와 함께 DNS 서버로 구성되지 않도록 합니다. 이 두 도메인 컨트롤러는 모두 DNS 서버이므로 도메인 컨트롤러로 수준을 올릴 때 온-프레미스 DNS 서버와 함께 DNS 전달자로 자동 구성됩니다.

다음으로는 Azure 가상 네트워크의 서버가 로컬 도메인 컨트롤러를 사용하도록 Active Directory 복제 사이트를 만듭니다. sp\_install 계정을 사용하여 주 도메인 컨트롤러에 로그온한 다음 관리자 수준 Windows PowerShell 명령 프롬프트에서 다음 명령을 실행합니다.

	$vnet="<Table V – Item 1 – Value column>"
	$vnetSpace="<Table V – Item 5 – Value column>"
	New-ADReplicationSite -Name $vnet
	New-ADReplicationSubnet –Name $vnetSpace –Site $vnet

이 다이어그램에서는 이 단계를 올바르게 완료한 경우의 구성을 보여 주며, 컴퓨터 이름은 자리 표시자로 표시되어 있습니다.

![](./media/virtual-machines-workload-intranet-sharepoint-phase2/workload-spsqlao_02.png)

## 다음 단계

이 작업을 계속 구성하려면 [3단계: SQL Server 인프라 구성](virtual-machines-workload-intranet-sharepoint-phase3.md)으로 진행하세요.

## 추가 리소스

[Azure에서 SQL Server AlwaysOn 가용성 그룹을 사용하여 SharePoint 배포](virtual-machines-workload-intranet-sharepoint-overview.md)

[Azure 인프라 서비스에서 호스트되는 SharePoint 팜](virtual-machines-sharepoint-infrastructure-services.md)

[SQL Server AlwaysOn이 포함된 SharePoint 인포그래픽](http://go.microsoft.com/fwlink/?LinkId=394788)

[SharePoint 2013용 Microsoft Azure 아키텍처](https://technet.microsoft.com/library/dn635309.aspx)

[Azure 인프라 서비스 구현 지침](virtual-machines-infrastructure-services-implementation-guidelines.md)

<!---HONumber=August15_HO6-->