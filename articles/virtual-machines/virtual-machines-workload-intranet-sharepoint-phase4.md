<properties
	pageTitle="SharePoint Server 2013 팜 4단계 | Microsoft Azure"
	description="Azure의 SharePoint Server 2013 팜 4단계에서 SharePoint 서버 가상 컴퓨터 및 새 SharePoint 팜을 만듭니다."
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
	ms.date="07/22/2015"
	ms.author="josephd"/>

# SharePoint 인트라넷 팜 워크로드 4단계: SharePoint 서버 구성

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-include.md)]이 문서에서는 클래식 배포 모델을 사용하여 리소스를 만드는 방법을 설명합니다.

Azure 인프라 서비스에서 SQL Server AlwaysOn 가용성 그룹을 사용하여 인트라넷 전용 SharePoint 2013을 배포하는 이 단계에서는 SharePoint 팜의 응용 프로그램 및 웹 계층을 구축하고 SharePoint 구성 마법사를 사용하여 팜을 만듭니다.

[5단계](virtual-machines-workload-intranet-sharepoint-phase5.md)로 진행하기 전에 이 단계를 완료해야 합니다. 전체 단계를 보려면 [Azure에서 SQL Server AlwaysOn 가용성 그룹을 사용하여 SharePoint 배포](virtual-machines-workload-intranet-sharepoint-overview.md)를 참조하세요.

## Azure에서 SharePoint 서버 가상 컴퓨터 만들기

SharePoint 서버 가상 컴퓨터는 네 개입니다. 두 SharePoint 서버 가상 컴퓨터는 프런트 엔드 웹 서버용이고 다른 두 컴퓨터는 SharePoint 응용 프로그램 관리 및 호스팅용입니다. 각 계층에 SharePoint 서버가 두 개씩이므로 고가용성이 제공됩니다.

Azure PowerShell 명령의 다음 블록을 사용하여 4개 SharePoint 서버용 가상 컴퓨터를 만듭니다. < and > 문자를 제거하고 변수의 값을 지정합니다. 이 PowerShell 명령 집합은 다음 표의 값을 사용합니다.

- 가상 컴퓨터의 경우 표 M
- 가상 네트워크 설정의 경우 표 V
- 서브넷의 경우 표 S
- 가용성 집합의 경우 표 A
- 클라우드 서비스의 경우 표 C

표 M은 [2단계: 도메인 컨트롤러 구성](virtual-machines-workload-intranet-sharepoint-phase2.md)에서 정의했고 표 V, S, A, C는 [1단계: Azure 구성](virtual-machines-workload-intranet-sharepoint-phase1.md)에서 정의했습니다.

적절한 값을 모두 입력한 후 Azure PowerShell 명령 프롬프트에서 완성된 블록을 실행합니다.

	# Create the first SharePoint application server
	$vmName="<Table M – Item 6 - Virtual machine name column>"
	$vmSize="<Table M – Item 6 - Minimum size column, specify one: Small, Medium, Large, ExtraLarge, A5, A6, A7, A8, A9>"
	$availSet="<Table A – Item 3 – Availability set name column>"
	$image= Get-AzureVMImage | where { $_.Label -eq "SharePoint Server 2013 Trial" } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1
	$vm1=New-AzureVMConfig -Name $vmName -InstanceSize $vmSize -ImageName $image -AvailabilitySetName $availSet

	$cred1=Get-Credential –Message "Type the name and password of the local administrator account for the first SharePoint application server."
	$cred2=Get-Credential –Message "Now type the name and password of an account that has permissions to add this virtual machine to the domain."
	$ADDomainName="<name of the AD domain that the server is joining (example CORP)>"
	$domainDNS="<FQDN of the AD domain that the server is joining (example corp.contoso.com)>"
	$vm1 | Add-AzureProvisioningConfig -AdminUsername $cred1.GetNetworkCredential().Username -Password $cred1.GetNetworkCredential().Password -WindowsDomain -Domain $ADDomainName -DomainUserName $cred2.GetNetworkCredential().Username -DomainPassword $cred2.GetNetworkCredential().Password -JoinDomain $domainDNS

	$subnetName="<Table 6 – Item 1 – Subnet name column>"
	$vm1 | Set-AzureSubnet -SubnetNames $subnetName

	$serviceName="<Table C – Item 3 – Cloud service name column>"
	$vnetName="<Table V – Item 1 – Value column>"
	New-AzureVM –ServiceName $serviceName -VMs $vm1 -VNetName $vnetName

	# Create the second SharePoint application server
	$vmName="<Table M – Item 7 - Virtual machine name column>"
	$vmSize="<Table M – Item 7 - Minimum size column, specify one: Small, Medium, Large, ExtraLarge, A5, A6, A7, A8, A9>"
	$vm1=New-AzureVMConfig -Name $vmName -InstanceSize $vmSize -ImageName $image -AvailabilitySetName $availSet

	$cred1=Get-Credential –Message "Type the name and password of the local administrator account for the second SharePoint application server."
	$vm1 | Add-AzureProvisioningConfig -AdminUsername $cred1.GetNetworkCredential().Username -Password $cred1.GetNetworkCredential().Password -WindowsDomain -Domain $ADDomainName -DomainUserName $cred2.GetNetworkCredential().Username -DomainPassword $cred2.GetNetworkCredential().Password -JoinDomain $domainDNS

	$vm1 | Set-AzureSubnet -SubnetNames $subnetName

	New-AzureVM –ServiceName $serviceName -VMs $vm1 -VNetName $vnetName

	# Create the first SharePoint web server
	$vmName="<Table M – Item 8 - Virtual machine name column>"
	$vmSize="<Table M – Item 8 - Minimum size column, specify one: Small, Medium, Large, ExtraLarge, A5, A6, A7, A8, A9>"
	$availSet="<Table A – Item 4 – Availability set name column>"
	$vm1=New-AzureVMConfig -Name $vmName -InstanceSize $vmSize -ImageName $image -AvailabilitySetName $availSet

	$cred1=Get-Credential –Message "Type the name and password of the local administrator account for the first SharePoint web server."
	$vm1 | Add-AzureProvisioningConfig -AdminUsername $cred1.GetNetworkCredential().Username -Password $cred1.GetNetworkCredential().Password -WindowsDomain -Domain $ADDomainName -DomainUserName $cred2.GetNetworkCredential().Username -DomainPassword $cred2.GetNetworkCredential().Password -JoinDomain $domainDNS

	$vm1 | Set-AzureSubnet -SubnetNames $subnetName

	New-AzureVM –ServiceName $serviceName -VMs $vm1 -VNetName $vnetName

	# Create the second SharePoint web server
	$vmName="<Table M – Item 9 - Virtual machine name column>"
	$vmSize="<Table M – Item 9 - Minimum size column, specify one: Small, Medium, Large, ExtraLarge, A5, A6, A7, A8, A9>"
	$vm1=New-AzureVMConfig -Name $vmName -InstanceSize $vmSize -ImageName $image -AvailabilitySetName $availSet

	$cred1=Get-Credential –Message "Type the name and password of the local administrator account for the second SharePoint web server."
	$vm1 | Add-AzureProvisioningConfig -AdminUsername $cred1.GetNetworkCredential().Username -Password $cred1.GetNetworkCredential().Password -WindowsDomain -Domain $ADDomainName -DomainUserName $cred2.GetNetworkCredential().Username -DomainPassword $cred2.GetNetworkCredential().Password -JoinDomain $domainDNS

	$vm1 | Set-AzureSubnet -SubnetNames $subnetName

	New-AzureVM –ServiceName $serviceName -VMs $vm1 -VNetName $vnetName

[원격 데스크톱 연결을 사용하여 가상 컴퓨터에 로그온](virtual-machines-workload-intranet-sharepoint-phase2.md#logon) 절차를 네 번(각 SharePoint 서버에 대해 한 번씩) 수행하여 [도메인]\\sp\_farm\_db 계정 자격 증명으로 로그온합니다. 이러한 자격 증명은 [2단계: 도메인 컨트롤러 구성](virtual-machines-workload-intranet-sharepoint-phase2.md)에서 만들었습니다.

[연결을 테스트하려면](virtual-machines-workload-intranet-sharepoint-phase2.md#testconn) 절차를 네 번(각 SharePoint 서버에 대해 한 번씩) 수행하여 조직 네트워크의 위치에 대한 연결을 테스트합니다.

> [AZURE.NOTE]SharePoint 서버는 SharePoint Server 2013 평가판 이미지에서 만들어집니다. SharePoint Server 2013의 Standard 또는 Enterprise 버전용 일반 정품 또는 볼륨 라이선스 키를 사용하도록 설치를 전환해야 합니다.

## SharePoint 팜 구성

다음 단계를 수행하여 팜의 첫 번째 SharePoint 서버를 구성합니다.

1.	첫 번째 SharePoint 응용 프로그램 서버의 바탕 화면에서 **SharePoint 2013 제품 구성 마법사**를 두 번 클릭합니다. 프로그램이 컴퓨터를 변경할 수 있도록 허용할지 묻는 메시지가 나타나면 **예**를 클릭합니다.
2.	**SharePoint 제품** 페이지에서 **다음**을 클릭합니다.
3.	**SharePoint 제품 구성 마법사** 대화 상자가 나타나고 IIS 등의 서비스가 다시 시작되거나 다시 설정된다는 경고가 표시됩니다. **예**를 클릭합니다.
4.	**서버 팜에 연결** 페이지에서 **새 서버 팜 만들기**를 클릭한 후 **다음**을 클릭합니다.
5.	**구성 데이터베이스 설정 지정** 페이지에서 다음을 수행합니다.
 - **데이터베이스 서버**에 주 데이터베이스 서버의 이름을 입력합니다.
 - **사용자 이름**에 [2단계: 도메인 컨트롤러 구성](virtual-machines-workload-intranet-sharepoint-phase2.md)에서 만든 [도메인]**\\sp\_farm\_db**를 입력합니다. sp\_farm\_db 계정에는 데이터베이스 서버에 대한 sysadmin 권한이 있습니다.
 - **암호**에 sp\_farm\_db 계정의 암호를 입력합니다.
6.	**다음**을 클릭합니다.
7.	**팜 보안 설정 지정** 페이지에서 암호를 두 번 입력합니다. 암호를 기록하여 나중에 참조할 수 있도록 안전한 위치에 저장합니다. **다음**을 클릭합니다.
8.	**SharePoint 중앙 관리 웹 응용 프로그램 구성** 페이지에서 **다음**을 클릭합니다.
9.	**SharePoint 제품 구성 마법사 완료** 페이지가 표시됩니다. **다음**을 클릭합니다.
10.	**SharePoint 제품 구성** 페이지가 나타납니다. 구성 프로세스가 완료될 때까지 약 8분 정도 기다립니다.
11.	팜이 정상적으로 구성되면 **마침**을 클릭합니다. 새 관리 웹 사이트가 시작됩니다.
12.	SharePoint 팜 구성을 시작하려면 **마법사 시작**을 클릭합니다.

두 번째 SharePoint 응용 프로그램 서버와 두 프런트 엔드 웹 서버에서 다음 절차를 수행합니다.

1.	바탕 화면에서 **SharePoint 2013 제품 구성 마법사**를 두 번 클릭합니다. 프로그램이 컴퓨터를 변경할 수 있도록 허용할지 묻는 메시지가 나타나면 **예**를 클릭합니다.
2.	**SharePoint 제품** 페이지에서 **다음**을 클릭합니다.
3.	**SharePoint 제품 구성 마법사** 대화 상자가 나타나고 IIS 등의 서비스가 다시 시작되거나 다시 설정된다는 경고가 표시됩니다. **예**를 클릭합니다.
4.	**서버 팜에 연결** 페이지에서 **기존 서버 팜에 연결**을 클릭한 후 **다음**을 클릭합니다.
5.	**구성 데이터베이스 설정 지정** 페이지에서 **데이터베이스 서버**에 주 데이터베이스 서버의 이름을 입력하고 **데이터베이스 이름 검색**을 클릭합니다.
6.	데이터베이스 이름 목록에서 **SharePoint\_Config**를 클릭하고 **다음**을 클릭합니다.
7.	**팜 보안 설정 지정** 페이지에서 이전 절차의 암호를 입력합니다. **다음**을 클릭합니다.
8.	**SharePoint 제품 구성 마법사 완료** 페이지가 표시됩니다. **다음**을 클릭합니다.
9.	**구성 완료** 페이지에서 **마침**을 클릭합니다.

SharePoint에서는 팜을 만들 때 주 SQL Server 가상 컴퓨터에 서버 로그인 집합을 구성합니다. SQL Server 2012에서는 포함된 데이터베이스에 대해 암호를 사용하는 사용자 개념이 도입되었습니다. 데이터베이스 자체에 모든 데이터베이스 메타데이터 및 사용자 정보가 저장되므로 이 데이터베이스에 정의된 사용자에게는 해당 로그인 정보가 없어도 됩니다. 이 데이터베이스의 정보는 가용성 그룹에 의해 복제되며 장애 조치(failover) 이후 제공됩니다. 자세한 내용은 [포함된 데이터베이스](http://go.microsoft.com/fwlink/p/?LinkId=262794)를 참조하세요.

그러나 기본적으로 SharePoint 데이터베이스는 포함된 데이터베이스가 아닙니다. 따라서 주 데이터베이스 서버와 같은 SharePoint 팜 계정용 로그인 집합을 포함하도록 보조 데이터베이스 서버를 수동으로 구성해야 합니다. SQL Server Management Studio에서 두 서버에 동시에 연결하여 이 동기화를 수행할 수 있습니다.

이 초기 설정을 완료하고 나면 SharePoint 팜 기능에 대해 구성 옵션이 추가로 제공합니다. 자세한 내용은 [Azure 인프라 서비스에서 SharePoint 2013 계획](http://msdn.microsoft.com/library/dn275958.aspx)을 참조하세요.

## 내부 부하 분산 구성

SharePoint 팜으로의 클라이언트 트래픽이 두 프런트 엔드 웹 서버로 균일하게 분산되도록 내부 부하 분산을 구성합니다. 이렇게 하려면 서브넷 주소 공간에서 할당되는 자체 IP 주소와 이름으로 구성되는 내부 부하 분산 인스턴스를 지정해야 합니다. 내부 부하 분산 장치에 대해 선택한 IP 주소를 사용할 수 있는지를 확인하려면 다음 Azure PowerShell 명령을 사용합니다.

	$vnet="<Table V – Item 1 – Value column>"
	$testIP="<a chosen IP address from the subnet address space, Table S - Item 1 – Subnet address space column>"
	Test-AzureStaticVNetIP –VNetName $vnet –IPAddress $testIP

표시되는 **Test-AzureStaticVNetIP** 명령에서 **IsAvailable** 필드가 **True**이면 해당 IP 주소를 사용할 수 있습니다.

로컬 컴퓨터의 Azure PowerShell 명령 프롬프트에서 다음 명령 집합을 실행합니다.

	$serviceName="<Table C – Item 3 – Cloud service name column>"
	$ilb="<name of your internal load balancer instance>"
	$subnet="<Table S – Item 1 – Subnet name column>"
	$IP="<an available IP address for your ILB instance>"
	Add-AzureInternalLoadBalancer –ServiceName $serviceName -InternalLoadBalancerName $ilb –SubnetName $subnet –StaticVNetIPAddress $IP

	$prot="tcp"
	$locport=80
	$pubport=80
	# This example assumes unsecured HTTP traffic to the SharePoint farm.

	$epname="SPWeb1"
	$vmname="<Table M – Item 8 – Virtual machine name column>"
	Get-AzureVM –ServiceName $serviceName –Name $vmname | Add-AzureEndpoint -Name $epname -LBSetName $ilb -Protocol $prot -LocalPort $locport -PublicPort $pubport –DefaultProbe -InternalLoadBalancerName $ilb | Update-AzureVM

	$epname="SPWeb2"
	$vmname="<Table M – Item 9 – Virtual machine name column>"
	Get-AzureVM –ServiceName $serviceName –Name $vmname | Add-AzureEndpoint -Name $epname -LBSetName $ilb -Protocol $prot -LocalPort $locport -PublicPort $pubport –DefaultProbe -InternalLoadBalancerName $ilb | Update-AzureVM

다음으로, sp.corp.contoso.com과 같은 SharePoint 팜의 정규화된 도메인 이름을 내부 부하 분산 장치 인스턴스에 할당된 IP 주소(위 Azure PowerShell 명령 블록에서 **$IP**의 값)로 확인하는 DNS 주소 레코드를 조직 DNS 인프라에 추가합니다.

이 단계를 올바르게 완료하면 해당 구성이 생성됩니다.

![](./media/virtual-machines-workload-intranet-sharepoint-phase4/workload-spsqlao_04.png)

## 다음 단계

이 워크로드를 계속 구성하려면 [5단계: 가용성 그룹을 만들고 SharePoint 데이터베이스 추가](virtual-machines-workload-intranet-sharepoint-phase5.md)로 진행하세요.

## 추가 리소스

[Azure에서 SQL Server AlwaysOn 가용성 그룹을 사용하여 SharePoint 배포](virtual-machines-workload-intranet-sharepoint-overview.md)

[Azure 인프라 서비스에서 호스트되는 SharePoint 팜](virtual-machines-sharepoint-infrastructure-services.md)

[SQL Server AlwaysOn이 포함된 SharePoint 인포그래픽](http://go.microsoft.com/fwlink/?LinkId=394788)

[SharePoint 2013용 Microsoft Azure 아키텍처](https://technet.microsoft.com/library/dn635309.aspx)

[Azure 인프라 서비스 구현 지침](virtual-machines-infrastructure-services-implementation-guidelines.md)

[Azure 인프라 서비스 워크로드: 고가용성 LOB(기간 업무) 응용 프로그램](virtual-machines-workload-high-availability-lob-application.md)

<!---HONumber=Oct15_HO2-->