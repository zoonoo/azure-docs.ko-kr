<properties 
	pageTitle="SharePoint 인트라넷 팜 작업 3단계: SQL Server 인프라 구성" 
	description="Azure 인프라 서비스의 SQL Server AlwaysOn 가용성 그룹을 사용하여 인트라넷 전용 SharePoint 2013 팜을 배포하는 이 세 번째 단계에서는 SQL Server 클러스터 컴퓨터와 클러스터 자체를 만듭니다." 
	documentationCenter=""
	services="virtual-machines" 
	authors="JoeDavies-MSFT" 
	manager="timlt" 
	editor=""/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/05/2015" 
	ms.author="josephd"/>

# SharePoint 인트라넷 팜 작업 3단계: SQL Server 인프라 구성

Azure 인프라 서비스에서 SQL Server AlwaysOn 가용성 그룹을 사용하여 인트라넷 전용 SharePoint 2013 팜을 배포하는 이 단계에서는 두 SQL Server 컴퓨터와 클러스터 주 노드 컴퓨터를 구성한 다음 Windows Server 클러스터에 결합합니다.

[4단계](virtual-machines-workload-intranet-sharepoint-phase4.md)로 진행하기 전에 이 단계를 완료해야 합니다. 전체 단계를 보려면 [Azure에서 SQL Server AlwaysOn 가용성 그룹을 사용하여 SharePoint 배포](virtual-machines-workload-intranet-sharepoint-overview.md)를 참조하세요.

## Azure에서 SQL Server 클러스터 가상 컴퓨터 만들기

SQL Server 가상 컴퓨터는 두 개입니다. SQL Server 하나에는 가용성 그룹의 주 데이터베이스 복제본이 포함됩니다. 두 번째 SQL Server에는 보조 백업 복제본이 포함됩니다. 고가용성 보장하기 위해 백업이 제공됩니다. 추가 가상 컴퓨터는 클러스터 주 노드용입니다.

PowerShell 명령의 다음 블록을 사용하여 3개 서버용 가상 컴퓨터를 만듭니다. < and > 문자를 제거하고 변수의 값을 지정합니다. 이 PowerShell 명령 집합은 다음 표의 값을 사용합니다.

- 가상 컴퓨터의 경우 표 M
- 가상 네트워크 설정의 경우 표 V
- 서브넷의 경우 표 S
- 가용성 집합의 경우 표 A
- 클라우드 서비스의 경우 표 C

표 M은 [2단계: 도메인 컨트롤러 구성](virtual-machines-workload-intranet-sharepoint-phase2.md)에서 정의했고 표 V, S, A, C는 [1단계: Azure 구성](virtual-machines-workload-intranet-sharepoint-phase1.md)에서 정의했습니다.

적절한 값을 모두 입력한 후 Azure PowerShell 명령 프롬프트에서 완성된 블록을 실행합니다.

	# Create the first SQL server
	$vmName="<Table M – Item 3 - Virtual machine name column>"
	$vmSize="<Table M – Item 3 - Minimum size column, specify one: Small, Medium, Large, ExtraLarge, A5, A6, A7, A8, A9>"
	$availSet="<Table A – Item 2 – Availability set name column>"
	
	$image= Get-AzureVMImage | where { $_.ImageFamily -eq "SQL Server 2014 RTM Standard on Windows Server 2012 R2" } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1
	$vm1=New-AzureVMConfig -Name $vmName -InstanceSize $vmSize -ImageName $image -AvailabilitySetName $availSet
	
	$cred1=Get-Credential –Message "Type the name and password of the local administrator account for the first SQL Server computer."
	$cred2=Get-Credential –Message "Now type the name and password of an account that has permissions to add this virtual machine to the domain."
	$ADDomainName="<name of the AD domain that the server is joining (example CORP)>"
	$domainDNS="<FQDN of the AD domain that the server is joining (example corp.contoso.com)>"
	$vm1 | Add-AzureProvisioningConfig -AdminUsername $cred1.GetNetworkCredential().Username -Password $cred1.GetNetworkCredential().Password -WindowsDomain -Domain $ADDomainName -DomainUserName $cred2.GetNetworkCredential().Username -DomainPassword $cred2.GetNetworkCredential().Password -JoinDomain $domainDNS
	
	$diskSize=<size of the additional data disk in GB>
	$diskLabel="<the label on the disk>"
	$lun=<Logical Unit Number (LUN) of the disk>
	$vm1 | Add-AzureDataDisk -CreateNew -DiskSizeInGB $diskSize -DiskLabel $diskLabel -LUN $lun -HostCaching None
	
	$subnetName="<Table 6 – Item 1 – Subnet name column>"
	$vm1 | Set-AzureSubnet -SubnetNames $subnetName
	
	$serviceName="<Table C – Item 2 – Cloud service name column>"
	$vnetName="<Table V – Item 1 – Value column>"
	New-AzureVM –ServiceName $serviceName -VMs $vm1 -VNetName $vnetName
	
	# Create the second SQL server
	$vmName="<Table M – Item 4 - Virtual machine name column>"
	$vmSize="<Table M – Item 4 - Minimum size column, specify one: Small, Medium, Large, ExtraLarge, A5, A6, A7, A8, A9>"
	$vm1=New-AzureVMConfig -Name $vmname -InstanceSize $vmsize -ImageName $image -AvailabilitySetName $availSet
	
	$cred1=Get-Credential –Message "Type the name and password of the local administrator account for the second SQL Server computer."
	$vm1 | Add-AzureProvisioningConfig -AdminUsername $cred1.GetNetworkCredential().Username -Password $cred1.GetNetworkCredential().Password -WindowsDomain -Domain $ADDomainName -DomainUserName $cred2.GetNetworkCredential().Username -DomainPassword $cred2.GetNetworkCredential().Password -JoinDomain $domainDNS
	
	$diskSize=<size of the additional data disk in GB>
	$diskLabel="<the label on the disk>"
	$lun=<Logical Unit Number (LUN) of the disk>
	$vm1 | Add-AzureDataDisk -CreateNew -DiskSizeInGB $diskSize -DiskLabel $diskLabel -LUN $lun -HostCaching None
	
	$vm1 | Set-AzureSubnet -SubnetNames $subnetName
	
	New-AzureVM –ServiceName $serviceName -VMs $vm1 -VNetName $vnetName
	
	# Create the cluster majority node server
	$vmName="<Table M – Item 5 - Virtual machine name column>"
	$vmSize="<Table M – Item 5 - Minimum size column, specify one: Small, Medium, Large, ExtraLarge, A5, A6, A7, A8, A9>"
	$image= Get-AzureVMImage | where { $_.ImageFamily -eq "Windows Server 2012 R2 Datacenter" } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1
	$vm1=New-AzureVMConfig -Name $vmName -InstanceSize $vmSize -ImageName $image -AvailabilitySetName $availSet
	
	$cred1=Get-Credential –Message "Type the name and password of the local administrator account for the cluster majority node server."
	$vm1 | Add-AzureProvisioningConfig -AdminUsername $cred1.GetNetworkCredential().Username -Password $cred1.GetNetworkCredential().Password -WindowsDomain -Domain $ADDomainName -DomainUserName $cred2.GetNetworkCredential().Username -DomainPassword $cred2.GetNetworkCredential().Password -JoinDomain $domainDNS
	
	$vm1 | Set-AzureSubnet -SubnetNames $subnetName
	
	New-AzureVM –ServiceName $serviceName -VMs $vm1 -VNetName $vnetName

## SQL Server 컴퓨터 구성

각 SQL Server에 대해 다음을 수행합니다.

1. [원격 데스크톱 연결을 사용하여 가상 컴퓨터에 로그온](virtual-machines-workload-intranet-sharepoint-phase2.md#logon) 절차를 수행하여 로컬 관리자 계정의 자격 증명을 사용하여 로그온합니다.

2. [빈 디스크를 초기화하려면](virtual-machines-workload-intranet-sharepoint-phase2.md#datadisk) 절차를 각 SQL Server에 대해 한 번씩 수행하여 데이터 디스크를 더 추가합니다.

3. Windows PowerShell 명령 프롬프트에서 다음 명령을 실행합니다.

		md f:\Data
		md f:\Log
		md f:\Backup

4. [연결을 테스트하려면](virtual-machines-workload-intranet-sharepoint-phase2.md#testconn) 절차를 수행하여 조직 네트워크의 위치에 대한 연결을 테스트합니다. 이 절차를 통해 DNS 이름 확인이 정상적으로 작동하는지, 즉 가상 네트워크의 DNS 서버에서 가상 컴퓨터가 올바르게 구성되어 있는지와 크로스-프레미스 가상 네트워크에 대한 패킷 송수신이 가능한지를 확인할 수 있습니다.

다음 절차를 두 번(각 SQL Server에 대해 한 번씩) 수행하여 새 데이터베이스와 계정 및 권한에 대해 F: 드라이브를 사용하도록 SQL Server를 구성합니다.


1.	시작 화면에서 **SQL Studio**를 입력하고 **SQL Server 2014 Management Studio**를 클릭합니다.
2.	**서버에 연결**에서 **연결**을 클릭합니다.
3.	왼쪽 창에서 최상위 노드(컴퓨터 이름이 지정된 기본 인스턴스)를 마우스 오른쪽 단추로 클릭하고 **속성**을 클릭합니다.
4.	**서버 속성**에서 **데이터베이스 설정**을 클릭합니다.
5.	**데이터베이스 기본 위치**에서 다음 값을 설정합니다. 
- **데이터**의 경우 경로를 **f:\Data**로 설정합니다.
- **로그**의 경우 경로를 **f:\Log**로 설정합니다.
- **백업**의 경우 경로를 **f:\Backup**으로 설정합니다.
- 새 데이터베이스에서만 이러한 위치를 사용합니다.
6.	**확인**을 클릭하여 창을 닫습니다.
7.	왼쪽 창에서 **보안** 폴더를 확장합니다.
8.	**로그인**을 마우스 오른쪽 단추로 클릭하고 **새 로그인**을 클릭합니다.
9.	**로그인 이름**에 *도메인*\sp_farm_db를 입력합니다. 여기서 *도메인*은 sp_farm_db 계정을 만든 도메인의 이름입니다. 
10.	**페이지 선택**에서 **서버 역할**, **sysadmin**, **확인**을 차례로 클릭합니다.
11.	SQL Server 2014 Management Studio를 닫습니다.

다음 절차를 두 번(각 SQL Server에 대해 한 번씩) 수행하여 sp_farm_db 계정을 사용한 원격 데스크톱 연결을 허용합니다.

1.	시작 화면에서 **이 PC**를 마우스 오른쪽 단추로 클릭하고 **속성**을 클릭합니다.
2.	**시스템** 창에서 **원격 설정**을 클릭합니다.
3.	**원격 데스크톱** 섹션에서 **사용자 선택**을 클릭하고 **추가**를 클릭합니다.
4.	**선택할 개체 이름을 입력하십시오**에 [도메인]**\sp_farm_db**를 입력하고 **확인**을 세 번 클릭합니다.

SQL Server에서는 클라이언트가 데이터베이스 서버에 액세스하는 데 사용하는 포트가 필요합니다. 또한 SQL Server Management Studio에 연결하고 고가용성 그룹을 관리하기 위한 포트도 필요합니다. 다음으로 관리자 수준 Windows PowerShell 명령 프롬프트에서 아래 명령을 두 번(각 SQL Server에 대해 한 번씩) 실행하여 SQL Server에 대한 인바운드 트래픽을 허용하는 방화벽 규칙을 추가합니다.

	New-NetFirewallRule -DisplayName "SQL Server ports 1433, 4234, and 5022" -Direction Inbound –Protocol TCP –LocalPort 1433,1434,5022 -Action Allow

각 SQL Server 가상 컴퓨터에 대해 로컬 관리자로 로그아웃합니다.

Azure에서 SQL Server 성능을 최적화하는 방법에 대한 자세한 내용은 [ Azure 가상 컴퓨터의 SQL Server에 대한 성능 모범 사례](https://msdn.microsoft.com/library/azure/dn133149.aspx)를 참조하세요. SharePoint 팜 저장소 계정에 대해 GRS(지역 중복 저장소)를 사용하지 않도록 설정하고 저장소 공간을 사용해 IOPS를 최적화할 수도 있습니다.

## 클러스터 주 노드 서버 구성

클러스터 주 노드에 대해 [원격 데스크톱 연결을 사용하여 가상 컴퓨터에 로그온](virtual-machines-workload-intranet-sharepoint-phase2.md#logon) 절차를 수행하여 도메인 계정의 자격 증명을 사용해 로그온합니다.

클러스터 주 노드에서 [연결을 테스트하려면](virtual-machines-workload-intranet-sharepoint-phase2.md#testconn) 절차를 수행하여 조직 네트워크의 위치에 대한 연결을 테스트합니다.

## Windows Server 클러스터 만들기

SQL Server AlwaysOn 가용성 그룹은 Windows Server의 WSFC(Windows Server 장애 조치(failover) 클러스터링) 기능을 사용합니다. 이 기능을 통해 여러 컴퓨터가 클러스터에 그룹으로 참가할 수 있습니다. 컴퓨터 하나에 오류가 발생하면 두 번째 컴퓨터가 해당 역할을 대신할 수 있습니다. 따라서 먼저 다음을 비롯한 모든 참가 컴퓨터에서 장애 조치(failover) 클러스터링 기능을 사용하도록 설정해야 합니다.

- 주 SQL Server
- 보조 SQL Server
- 클러스터 주 노드

장애 조치(failover) 클러스터에는 VM이 3개 이상 필요합니다. 두 컴퓨터는 SQL Server를 호스팅합니다. 두 번째 SQL Server VM은 주 컴퓨터에서 오류가 발생하더라도 데이터가 손실되지 않도록 하는 동기 보조 복제본입니다. 세 번째 컴퓨터는 SQL Server를 호스팅하지 않아도 됩니다. 클러스터 주 노드는 WSFC에서 쿼럼 감시 역할도 수행합니다. WSFC 클러스터는 쿼럼을 사용하여 상태를 모니터링하므로 WSFC 클러스터가 온라인 상태로 유지되도록 항상 주 노드가 있어야 합니다. 클러스터에 컴퓨터가 두 개뿐인데 그 중 하나에 오류가 발생하면 주 노드가 없어지게 됩니다. 자세한 내용은 [WSFC 쿼럼 모드 및 응답 구성(SQL Server)](http://msdn.microsoft.com/library/hh270280.aspx)을 참조하세요.

두 SQL Server 컴퓨터 및 클러스터 주 노드에 대해 관리자 수준 Windows PowerShell 명령 프롬프트에서 다음 명령을 실행합니다.

	Install-WindowsFeature Failover-Clustering -IncludeManagementTools

현재는 Azure의 DHCP에서 수행하는 RFC 미준수 동작으로 인해 WSFC(Windows Server 장애 조치(failover) 클러스터) 만들기가 실패할 수 있습니다. 자세한 내용은 Azure 가상 컴퓨터의 SQL Server에 대한 고가용성 및 재해 복구에서 “Azure 네트워킹의 WSFC 클러스터 동작”을 검색하세요. 그러나 이러한 현상을 해결할 수 있는 방법이 있습니다. 클러스터를 만들려면 다음 단계를 수행합니다.

1.	**sp_install** 계정을 사용하여 주 SQL Server 가상 컴퓨터에 로그온합니다.
2.	시작 화면에서 **장애 조치**를 입력하고 **장애 조치(Failover) 클러스터 관리자**를 클릭합니다.
3.	왼쪽 창에서 **장애 조치(Failover) 클러스터 관리자**를 마우스 오른쪽 단추로 클릭하고 **클러스터 만들기**를 클릭합니다.
4.	시작하기 전에 페이지에서 **다음**을 클릭합니다.
5.	서버 선택 페이지에서 주 SQL Server 컴퓨터의 이름을 입력하고 **추가**, **다음**을 차례로 클릭합니다.
6.	유효성 검사 경고 페이지에서 **아니요. 이 클러스터에 대한 Microsoft의 지원이 필요 없으므로 유효성 검사 테스트를 실행하지 않습니다. [다음]을 클릭하면 클러스터 만들기를 계속합니다.**를 클릭하고 **다음**을 클릭합니다.
7.	클러스터 관리 액세스 지점 페이지의 **클러스터 이름** 텍스트 상자에 클러스터의 이름을 입력하고 **다음**을 클릭합니다.
8.	확인 페이지에서 **다음**을 클릭하여 클러스터 만들기를 시작합니다. 
9.	요약 페이지에서 **마침**을 클릭합니다.
10.	왼쪽 창에서 새 클러스터를 클릭합니다. 내용 창의 **클러스터 코어 리소스** 섹션에서 서버 클러스터 이름을 엽니다. **IP 주소** 리소스가 **실패** 상태로 표시됩니다. 클러스터에 컴퓨터 자체와 같은 IP 주소가 할당되므로 IP 주소 리소스는 온라인으로 설정할 수 없습니다. 따라서 주소가 중복됩니다. 
11.	오류가 발생한 **IP 주소** 리소스를 마우스 오른쪽 단추로 클릭하고 **속성**을 클릭합니다.
12.	**IP 주소 속성** 대화 상자에서 **고정 IP 주소**를 클릭합니다.
13.	SQL Server가 있는 서브넷에 해당하는 주소 범위에서 사용되지 않은 IP를 입력하고 **확인**을 클릭합니다.
14.	오류가 발생한 IP 주소 리소스를 마우스 오른쪽 단추로 클릭하고 **온라인 상태로 전환**을 클릭합니다. 두 리소스가 모두 온라인 상태로 전환될 때까지 기다립니다. 클러스터 이름 리소스가 온라인 상태가 되면 새 AD(Active Directory) 컴퓨터 계정으로 도메인 컨트롤러를 업데이트합니다. 이 AD 계정은 나중에 가용성 그룹 클러스터된 서비스를 실행하는 데 사용됩니다.
15.	이제 AD 계정을 만들었으므로 클러스터 이름을 오프라인으로 전환합니다. **클러스터 코어 리소스**에서 클러스터 이름을 마우스 오른쪽 단추로 클릭하고 **오프라인 상태로 만들기**를 클릭합니다.
16.	클러스터 IP 주소를 제거하려면 **IP 주소**를 마우스 오른쪽 단추로 클릭하고 **제거**를 클릭한 후에 메시지가 표시되면 **예**를 클릭합니다. 클러스터 리소스는 IP 주소 리소스를 사용하므로 더 이상 온라인으로 전환할 수 없습니다. 그러나 가용성 그룹은 정상적으로 작동하기 위해 클러스터 이름 또는 IP 주소를 사용하지 않습니다. 따라서 클러스터 이름은 오프라인 상태로 유지할 수 있습니다.
17.	나머지 노드를 클러스터에 추가하려면 왼쪽 창에서 클러스터 이름을 마우스 오른쪽 단추로 클릭하고 **노드 추가**를 클릭합니다.
18.	시작하기 전에 페이지에서 **다음**을 클릭합니다. 
19.	서버 선택 페이지에서 이름을 입력한 다음 **추가**를 클릭하여 보조 SQL Server와 클러스터 주 노드를 모두 클러스터에 추가합니다. 두 컴퓨터를 추가한 후 **다음**을 클릭합니다. 컴퓨터를 추가할 수 없으며 “원격 레지스트리가 실행 중이 아님" 오류 메시지가 표시되면 다음을 수행합니다. 컴퓨터에 로그온한 다음 서비스 스냅인(services.msc)을 열고 원격 레지스트리를 사용하도록 설정합니다. 자세한 내용은 [원격 레지스트리 서비스에 연결할 수 없음](http://technet.microsoft.com/library/bb266998.aspx)을 참조하세요. 
20.	유효성 검사 경고 페이지에서 **아니요. 이 클러스터에 대한 Microsoft의 지원이 필요 없으므로 유효성 검사 테스트를 실행하지 않습니다. [다음]을 클릭하면 클러스터 만들기를 계속합니다.**를 클릭하고 **다음**을 클릭합니다. 
21.	확인 페이지에서 **다음**을 클릭합니다.
22.	요약 페이지에서 **마침**을 클릭합니다.
23.	왼쪽 창에서 **노드**를 클릭합니다. 세 컴퓨터가 모두 표시됩니다.

## AlwaysOn 가용성 그룹 사용

다음 단계에서는 SQL Server 구성 관리자를 사용하여 AlwaysOn 가용성 그룹을 사용하도록 설정합니다. SQL Server의 가용성 그룹은 Azure 가용성 집합과는 다릅니다. 가용성 그룹에는 항상 사용 가능하며 복구 가능한 데이터베이스가 포함됩니다. Azure 가용성 집합은 여러 장애 도메인에 가상 컴퓨터를 할당합니다. 장애 도메인에 대한 자세한 내용은 [가상 컴퓨터의 가용성 관리](virtual-machines-manage-availability.md)를 참조하세요.

SQL Server에서 AlwaysOn 가용성 그룹을 사용하도록 설정하려면 다음 단계를 수행합니다.

1.	**sp_farm_db** 계정 또는 SQL Server에 대한 sysadmin 서버 역할이 있는 기타 계정을 사용하여 주 SQL Server에 로그온합니다.
2.	시작 화면에서 **SQL Server 구성**을 입력한 다음 **SQL Server 구성 관리자**를 클릭합니다.
3.	왼쪽 창에서 **SQL Server 서비스**를 클릭합니다.
4.	내용 창에서 **SQL Server(MSSQLSERVER)**를 두 번 클릭합니다.
5.	**SQL Server(MSSQLSERVER) 속성**에서 **AlwaysOn 고가용성** 탭을 클릭하고 **AlwaysOn 가용성 그룹 사용**을 선택한 후에 **적용**을 클릭하고 메시지가 표시되면 **확인**을 클릭합니다. 아직 속성 창을 닫지 마세요. 
6.	가상 컴퓨터 관리 가용성 탭을 클릭한 다음 **계정 이름**에 [도메인]**\sqlservice**를 입력합니다. **암호** 및 **암호 확인**에 sqlservice 계정 암호를 입력하고 **확인**을 클릭합니다.
7.	메시지 창에서 **예**를 클릭하여 SQL Server 서비스를 다시 시작합니다.
8.	보조 SQL server에 로그온하여 이 프로세스를 반복합니다. 

이 단계를 올바르게 완료하면 생성된 구성이 표시되며 컴퓨터 이름은 자리 표시자로 표시됩니다.

![](./media/virtual-machines-workload-intranet-sharepoint-phase3/workload-spsqlao_03.png)

## 다음 단계

이 작업을 계속 구성하려면 [4단계: SharePoint 서버 구성](virtual-machines-workload-intranet-sharepoint-phase4.md)으로 진행하세요.

## 추가 리소스

[Azure에서 SQL Server AlwaysOn 가용성 그룹을 사용하여 SharePoint 배포](virtual-machines-workload-intranet-sharepoint-overview.md)

[Azure 인프라 서비스에서 호스트되는 SharePoint 팜](virtual-machines-sharepoint-infrastructure-services.md)

[SQL Server AlwaysOn이 포함된 SharePoint 인포그래픽](http://go.microsoft.com/fwlink/?LinkId=394788)

[SharePoint 2013용 Microsoft Azure 아키텍처](https://technet.microsoft.com/library/dn635309.aspx)

[Azure 인프라 서비스 구현 지침](virtual-machines-infrastructure-services-implementation-guidelines.md)
 

<!---HONumber=July15_HO2-->