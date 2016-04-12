<properties
	pageTitle="SharePoint Server 2013 팜 2단계 | Microsoft Azure"
	description="Azure의 SharePoint Server 2013 팜 2단계에서 두 Active Directory 복제본 도메인 컨트롤러를 만들고 구성합니다."
	documentationCenter=""
	services="virtual-machines-windows"
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
	ms.date="12/11/2015"
	ms.author="josephd"/>

# SharePoint 인트라넷 팜 워크로드 2단계: 도메인 컨트롤러 구성

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]클래식 배포 모델.

Azure 인프라 서비스의 SQL Server AlwaysOn 가용성 그룹을 사용하여 인트라넷 전용 SharePoint 2013 팜을 배포하는 이 단계에서는 Azure 가상 네트워크의 도메인 컨트롤러 두 개를 구성합니다. 그런 다음 사이트 간 VPN 또는 Azure Express 경로 연결을 통해 인증 트래픽을 온-프레미스 네트워크로 전송하는 대신 Azure 가상 네트워크에서 SharePoint 팜 리소스에 대한 클라이언트 웹 요청을 인증할 수 있습니다.

[3단계](virtual-machines-windows-ps-sp-intranet-ph3.md)로 진행하기 전에 이 단계를 완료해야 합니다. 전체 단계를 보려면 [Azure에서 SQL Server AlwaysOn 가용성 그룹을 사용하여 SharePoint 배포](virtual-machines-windows-sp-intranet-overview.md)를 참조하세요.

## Azure에서 도메인 컨트롤러 가상 컴퓨터 만들기

먼저 표 M의 **가상 컴퓨터 이름** 열에 정보를 입력하고 **최소 크기** 열에서 필요에 따라 가상 컴퓨터 크기를 수정해야 합니다.

항목 | 가상 컴퓨터 이름 | 갤러리 이미지 | 최소 크기
--- | --- | --- | ---
1\. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_(DC1 등의 첫 번째 도메인 컨트롤러) | Windows Server 2012 R2 Datacenter | Standard\_A2
2\. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_(DC2 등의 두 번째 도메인 컨트롤러) | Windows Server 2012 R2 Datacenter | Standard\_A2
3\. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_(SQL1 등의 첫 번째 SQL Server 컴퓨터) | Microsoft SQL Server 2014 Enterprise – Windows Server 2012 R2 | Standard\_A5
4\. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_(SQL2 등의 두 번째 SQL Server 컴퓨터) | Microsoft SQL Server 2014 Enterprise – Windows Server 2012 R2 | Standard\_A5
5\. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_(MN1 등의 클러스터에 대한 주 노드) | Windows Server 2012 R2 Datacenter | Standard\_A1
6\. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_(APP1 등의 첫 번째 SharePoint 응용 프로그램 서버) | Microsoft SharePoint Server 2013 평가판 – Windows Server 2012 R2 | Standard\_A4
7\. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_(APP2 등의 두 번째 SharePoint 응용 프로그램 서버) | Microsoft SharePoint Server 2013 평가판 – Windows Server 2012 R2 | Standard\_A4
8\. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_(WEB1 등의 첫 번째 SharePoint 웹 서버) | Microsoft SharePoint Server 2013 평가판 – Windows Server 2012 R2 | Standard\_A4
9\. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_(WEB2 등의 두 번째 SharePoint 웹 서버) | Microsoft SharePoint Server 2013 평가판 – Windows Server 2012 R2 | Standard\_A4

**표 M – Azure의 SharePoint 2013 인트라넷 팜용 가상 컴퓨터**

전체 가상 컴퓨터 크기 목록은 [가상 컴퓨터의 크기](virtual-machines-linux-sizes.md)를 참조하세요.

Azure PowerShell 명령의 다음 블록을 사용하여 두 도메인 컨트롤러용 가상 컴퓨터를 만듭니다. < and > 문자를 제거하고 변수의 값을 지정합니다. 이 Azure PowerShell 명령 집합은 다음 표의 값을 사용합니다.

- 가상 컴퓨터의 경우 표 M
- 가상 네트워크 설정의 경우 표 V
- 서브넷의 경우 표 S
- 저장소 계정의 경우 표 ST
- 가용성 집합의 경우 표 A

표 V, S, ST, A는 [1단계: Azure 구성](virtual-machines-windows-ps-sp-intranet-ph1.md)에서 정의했습니다.

> [AZURE.NOTE] 다음 명령 집합은 Azure PowerShell 1.0 이상을 사용합니다. 자세한 내용은 [Azure PowerShell 1.0](https://azure.microsoft.com/blog/azps-1-0/)을 참조하세요.

적절한 값을 모두 입력한 후 Azure PowerShell 프롬프트에서 완성된 블록을 실행합니다.

	# Set up key variables
	$rgName="<resource group name>"
	$locName="<Azure location of your resource group>"
	$saName="<Table ST – Item 1 – Storage account name column>"
	$vnetName="<Table V – Item 1 – Value column>"
	$avName="<Table A – Item 1 – Availability set name column>"
	
	# Create the first domain controller
	$vmName="<Table M – Item 1 - Virtual machine name column>"
	$vmSize="<Table M – Item 1 - Minimum size column>"
	$staticIP="<Table V – Item 6 - Value column>"
	$vnet=Get-AzureRMVirtualNetwork -Name $vnetName -ResourceGroupName $rgName
	$nic=New-AzureRMNetworkInterface -Name ($vmName +"-NIC") -ResourceGroupName $rgName -Location $locName -SubnetId $vnet.Subnets[1].Id -PrivateIpAddress $staticIP
	$avSet=Get-AzureRMAvailabilitySet –Name $avName –ResourceGroupName $rgName 
	$vm=New-AzureRMVMConfig -VMName $vmName -VMSize $vmSize -AvailabilitySetId $avset.Id
	
	$diskSize=<size of the extra disk for AD DS data in GB>
	$storageAcc=Get-AzureRMStorageAccount -ResourceGroupName $rgName -Name $saName
	$vhdURI=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/" + $vmName + "-ADDSDisk.vhd"
	Add-AzureRMVMDataDisk -VM $vm -Name "ADDSData" -DiskSizeInGB $diskSize -VhdUri $vhdURI  -CreateOption empty
	
	$cred=Get-Credential -Message "Type the name and password of the local administrator account for the first domain controller." 
	$vm=Set-AzureRMVMOperatingSystem -VM $vm -Windows -ComputerName $vmName -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
	$vm=Set-AzureRMVMSourceImage -VM $vm -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2012-R2-Datacenter -Version "latest"
	$vm=Add-AzureRMVMNetworkInterface -VM $vm -Id $nic.Id
	$storageAcc=Get-AzureRMStorageAccount -ResourceGroupName $rgName -Name $saName
	$osDiskUri=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/" + $vmName + "-OSDisk.vhd"
	$vm=Set-AzureRMVMOSDisk -VM $vm -Name "OSDisk" -VhdUri $osDiskUri -CreateOption fromImage
	New-AzureRMVM -ResourceGroupName $rgName -Location $locName -VM $vm
	
	# Create the second domain controller
	$vmName="<Table M – Item 2 - Virtual machine name column>"
	$vmSize="<Table M – Item 2 - Minimum size column>"
	$staticIP="<Table V – Item 7 - Value column>"
	$vnet=Get-AzureRMVirtualNetwork -Name $vnetName -ResourceGroupName $rgName
	$nic=New-AzureRMNetworkInterface -Name ($vmName +"-NIC") -ResourceGroupName $rgName -Location $locName -SubnetId $vnet.Subnets[1].Id -PrivateIpAddress $staticIP
	$avSet=Get-AzureRMAvailabilitySet –Name $avName –ResourceGroupName $rgName 
	$vm=New-AzureRMVMConfig -VMName $vmName -VMSize $vmSize -AvailabilitySetId $avset.Id
	
	$diskSize=<size of the extra disk for AD DS data in GB>
	$storageAcc=Get-AzureRMStorageAccount -ResourceGroupName $rgName -Name $saName
	$vhdURI=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/" + $vmName + "-ADDSDisk.vhd"
	Add-AzureRMVMDataDisk -VM $vm -Name "ADDSData" -DiskSizeInGB $diskSize -VhdUri $vhdURI  -CreateOption empty
	
	$cred=Get-Credential -Message "Type the name and password of the local administrator account for the second domain controller." 
	$vm=Set-AzureRMVMOperatingSystem -VM $vm -Windows -ComputerName $vmName -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
	$vm=Set-AzureRMVMSourceImage -VM $vm -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2012-R2-Datacenter -Version "latest"
	$vm=Add-AzureRMVMNetworkInterface -VM $vm -Id $nic.Id
	$storageAcc=Get-AzureRMStorageAccount -ResourceGroupName $rgName -Name $saName
	$osDiskUri=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/" + $vmName + "-OSDisk.vhd"
	$vm=Set-AzureRMVMOSDisk -VM $vm -Name "OSDisk" -VhdUri $osDiskUri -CreateOption fromImage
	New-AzureRMVM -ResourceGroupName $rgName -Location $locName -VM $vm

> [AZURE.NOTE] 이러한 가상 컴퓨터는 인트라넷 응용 프로그램용이므로 공용 IP 주소 또는 DNS 도메인 이름 레이블이 할당되지 않으며 인터넷에 노출되지 않습니다. 그러나 이는 Azure 포털에서도 연결할 수 없음을 의미합니다. 가상 컴퓨터의 속성을 볼 때 **연결** 단추를 사용할 수 없습니다. 원격 데스크톱 연결 액세서리 또는 다른 원격 데스크톱 도구를 통해 해당 개인 IP 주소 또는 인트라넷 DNS 이름을 사용하여 가상 컴퓨터에 연결합니다.

## 첫 번째 도메인 컨트롤러 구성

원하는 원격 데스크톱 클라이언트를 사용하여 첫 번째 도메인 컨트롤러 가상 컴퓨터에 대한 원격 데스크톱 연결을 만듭니다. 인트라넷 DNS 또는 컴퓨터 이름 및 로컬 관리자 계정의 자격 증명을 사용합니다.

다음으로는 첫 번째 도메인 컨트롤러에 데이터 디스크를 더 추가해야 합니다.

### <a id="datadisk"></a>빈 디스크를 초기화하려면

1.	Server Manager의 왼쪽 창에서 **파일 및 저장소 서비스**를 클릭한 다음 **디스크**를 클릭합니다.
2.	내용 창의 **디스크** 그룹에서 **디스크 2**(**파티션**이 **알 수 없음**으로 설정됨)를 클릭합니다.
3.	**작업**을 클릭한 후 **새 볼륨**을 클릭합니다.
4.	새 볼륨 마법사의 시작하기 전에 페이지에서 **다음**을 클릭합니다.
5.	서버 및 디스크 선택 페이지에서 **디스크 2**를 클릭하고 **다음**을 클릭합니다. 메시지가 표시되면 확인을 클릭합니다.
6.	볼륨 크기를 선택합니다 페이지에서 **다음**을 클릭합니다.
7.	드라이브 문자 또는 폴더에 할당 페이지에서 **다음**을 클릭합니다.
8.	파일 시스템 설정 선택 페이지에서 **다음**을 클릭합니다.
9.	선택 확인 페이지에서 **만들기**를 클릭합니다.
10.	완료되면 **닫기**를 클릭합니다.

다음으로 조직 네트워크 위치에 대한 첫 번째 도메인 컨트롤러의 연결을 테스트합니다.

### <a id="testconn"></a>연결을 테스트하려면

1.	바탕 화면에서 Windows PowerShell 프롬프트를 엽니다.
2.	**ping** 명령을 사용하여 조직 네트워크의 리소스 이름과 IP 주소에 대해 ping을 실행합니다.

이 절차를 통해 DNS 이름 확인이 정상적으로 작동하는지, 즉 온-프레미스 DNS 서버에서 가상 컴퓨터가 올바르게 구성되어 있는지와 크로스-프레미스 가상 네트워크에 대한 패킷 송수신이 가능한지를 확인할 수 있습니다. 이 기본 테스트가 실패하면 DNS 이름 확인 및 패킷 배달 문제 해결을 위해 IT 부서에 문의합니다.

다음으로 첫 번째 도메인 컨트롤러의 Windows PowerShell 명령 프롬프트에서 다음 명령을 실행합니다.

	$domname="<DNS domain name of the domain for which this computer will be a domain controller>"
	Install-WindowsFeature AD-Domain-Services -IncludeManagementTools
	Install-ADDSDomainController -InstallDns –DomainName $domname  -DatabasePath "F:\NTDS" -SysvolPath "F:\SYSVOL" -LogPath "F:\Logs"

도메인 관리자 계정의 자격 증명을 입력하라는 메시지가 표시됩니다. 컴퓨터가 다시 시작됩니다.

## 두 번째 도메인 컨트롤러 구성

원하는 원격 데스크톱 클라이언트를 사용하여 두 번째 도메인 컨트롤러 가상 컴퓨터에 대한 원격 데스크톱 연결을 만듭니다. 인트라넷 DNS 또는 컴퓨터 이름 및 로컬 관리자 계정의 자격 증명을 사용합니다.

다음으로는 두 번째 도메인 컨트롤러에 데이터 디스크를 더 추가해야 합니다. [빈 디스크를 초기화하려면](#datadisk) 절차를 참조하세요.

다음으로 두 번째 도메인 컨트롤러의 Windows PowerShell 프롬프트에서 다음 명령을 실행합니다.

	$domname="<DNS domain name of the domain for which this computer will be a domain controller>"
	Install-WindowsFeature AD-Domain-Services -IncludeManagementTools
	Install-ADDSDomainController -InstallDns –DomainName $domname  -DatabasePath "F:\NTDS" -SysvolPath "F:\SYSVOL" -LogPath "F:\Logs"

도메인 관리자 계정의 자격 증명을 입력하라는 메시지가 표시됩니다. 컴퓨터가 다시 시작됩니다.

이제 Azure가 두 새 도메인 컨트롤러의 IP 주소를 DNS 서버로 사용하도록 가상 컴퓨터에 할당할 수 있게 DNS 서버를 업데이트해야 합니다. 이 절차에서는 가상 네트워크 설정에 대해 표 V(가상 네트워크 설정용) 및 표 M(가상 컴퓨터용) 값을 사용합니다.

1.	Azure 포털의 왼쪽 창에서 **가상 네트워크**를 클릭하고 가상 네트워크의 이름(표 V - 항목 1 - 값 열)을 클릭합니다.
2.	**설정** 창에서 **DNS 서버**를 클릭합니다.
3.	**DNS 서버** 창에 다음을 입력합니다.
	- **주 DNS 서버**: 표 V – 항목 6 – 값 열
	- **보조 DNS 서버**: 표 V – 항목 7 – 값 열
4.	Azure 포털의 왼쪽 패널에서 **가상 컴퓨터**를 클릭합니다.
5.	**가상 컴퓨터 창**에서 첫 번째 도메인 컨트롤러의 이름(표 M - 항목 1 - 가상 컴퓨터 이름 열)을 클릭합니다.
6.	가상 컴퓨터의 창에서 **다시 시작**을 클릭합니다.
7.	첫 번째 도메인 컨트롤러가 시작되면 **가상 컴퓨터 창**에서 두 번째 도메인 컨트롤러의 이름(표 M - 항목 2 - 가상 컴퓨터 이름 열)을 클릭합니다.
8.	가상 컴퓨터의 창에서 **다시 시작**을 클릭합니다. 두 번째 도메인 컨트롤러가 시작될 때까지 기다립니다.

여기서는 두 도메인 컨트롤러를 다시 시작하여 온-프레미스 DNS 서버와 함께 DNS 서버로 구성되지 않도록 합니다. 이 두 도메인 컨트롤러는 모두 DNS 서버이므로 도메인 컨트롤러로 수준을 올릴 때 온-프레미스 DNS 서버와 함께 DNS 전달자로 자동 구성되었습니다.

이제 Azure 가상 네트워크의 서버가 로컬 도메인 컨트롤러를 사용하도록 Active Directory 복제 사이트를 만들어야 합니다. 도메인 관리자 계정을 사용하여 주 도메인 컨트롤러에 로그온한 다음 관리자 수준 Windows PowerShell 프롬프트에서 다음 명령을 실행합니다.

	$vnet="<Table V – Item 1 – Value column>"
	$vnetSpace="<Table V – Item 5 – Value column>"
	New-ADReplicationSite -Name $vnet 
	New-ADReplicationSubnet –Name $vnetSpace –Site $vnet

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

	New-ADUser -SamAccountName sqlservice -AccountPassword (read-host "Set user password" -assecurestring) -name "sqlservice" -enabled $true -PasswordNeverExpires $true -ChangePasswordAtLogon $false

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

이 단계를 올바르게 완료하면 생성된 구성이 표시되며 컴퓨터 이름은 자리 표시자로 표시됩니다.

![](./media/virtual-machines-windows-ps-sp-intranet-ph2/workload-spsqlao_02.png)

## 다음 단계

- [3단계](virtual-machines-windows-ps-sp-intranet-ph3.md)를 사용하여 이 워크로드의 구성을 계속합니다.

<!---HONumber=AcomDC_0323_2016-->