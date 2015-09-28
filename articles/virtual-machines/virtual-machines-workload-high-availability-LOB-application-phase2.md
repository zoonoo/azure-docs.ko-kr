<properties 
	pageTitle="LOB(기간 업무) 응용 프로그램 2단계 | Microsoft Azure" 
	description="Azure의 LOB(기간 업무) 응용 프로그램의 2단계에서 두 Active Directory 복제본 도메인 컨트롤러를 만들고 구성합니다." 
	documentationCenter=""
	services="virtual-machines" 
	authors="JoeDavies-MSFT" 
	manager="timlt" 
	editor=""
	tags="azure-resource-manager"/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="Windows" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="08/11/2015" 
	ms.author="josephd"/>

# LOB(기간 업무) 응용 프로그램 워크로드 2단계: 도메인 컨트롤러 구성

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-include.md)]이 문서에서는 리소스 관리자 배포 모델을 사용하여 리소스를 만드는 방법을 설명합니다.

Azure 인프라 서비스에서 고가용성 LOB(기간 업무) 응용 프로그램을 배포하는 이 단계에서는 온-프레미스 네트워크 연결을 통해 해당 인증 트래픽을 보내는 대신 Azure 가상 네트워크에서 웹 리소스에 대한 클라이언트 웹 요청을 로컬로 인증할 수 있도록 두 복제본 도메인 컨트롤러를 구성합니다.

[3단계](virtual-machines-workload-high-availability-LOB-application-phase3.md)로 진행하기 전에 이 단계를 완료해야 합니다. 모든 단계는 [Azure에서 고가용성 LOB(기간 업무) 응용 프로그램 배포](virtual-machines-workload-high-availability-LOB-application-overview.md)를 참조하세요.

## Azure에서 도메인 컨트롤러 가상 컴퓨터 만들기

먼저 표 M의 **가상 컴퓨터 이름** 열에 정보를 입력하고 **최소 크기** 열에서 필요에 따라 가상 컴퓨터 크기를 수정해야 합니다.

항목 | 가상 컴퓨터 이름 | 갤러리 이미지 | 최소 크기 
--- | --- | --- | --- 
1\. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_(DC1 등의 첫 번째 도메인 컨트롤러) | Windows Server 2012 R2 Datacenter | Standard\_D1
2\. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_(DC2 등의 두 번째 도메인 컨트롤러) | Windows Server 2012 R2 Datacenter | Standard\_D1
3\. | \_ \_ \_ (주 데이터베이스 서버, 예: SQL1) | Microsoft SQL Server 2014 Enterprise – Windows Server 2012 R2 | 	Standard\_DS4
4\. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_ (보조 데이터베이스 서버, 예: SQL2) | Microsoft SQL Server 2014 Enterprise – Windows Server 2012 R2 | 	Standard\_DS4
5\. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_(MN1 등의 클러스터에 대한 주 노드 감시) | Windows Server 2012 R2 Datacenter | Standard\_D1
6\. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_(첫 번째 웹 서버, 예: WEB1) | Windows Server 2012 R2 Datacenter | Standard\_D3
7\. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_(두 번째 웹 서버, 예: WEB2) | Windows Server 2012 R2 Datacenter | Standard\_D3

**표 M – Azure의 고가용성 LOB(기간 업무) 응용 프로그램을 위한 가상 컴퓨터**

전체 가상 컴퓨터 크기 목록은 [Azure를 위한 가상 컴퓨터 및 클라우드 서비스 크기](https://msdn.microsoft.com/library/azure/dn197896.aspx)를 참조하세요.

Azure PowerShell 명령의 다음 블록을 사용하여 두 도메인 컨트롤러용 가상 컴퓨터를 만듭니다. < and > 문자를 제거하고 변수의 값을 지정합니다. 이 PowerShell 명령 집합은 다음 표의 값을 사용합니다.

- 가상 컴퓨터의 경우 표 M
- 가상 네트워크 설정의 경우 표 V
- 서브넷의 경우 표 S
- 저장소 계정의 경우 표 ST
- 가용성 집합의 경우 표 A

표 V, S, ST, A는 [1단계: Azure 구성](virtual-machines-workload-high-availability-LOB-application-phase1.md)에서 정의했습니다.

적절한 값을 모두 입력한 후 Azure PowerShell 프롬프트에서 완성된 블록을 실행합니다.

	# Set up subscription and key variables
	$subscr="<name of the Azure subscription>"
	Set-AzureSubscription -SubscriptionName $subscr
	Switch-AzureMode AzureResourceManager
	$rgName="<resource group name>"
	$locName="<Azure location of your resource group>"
	$saName="<Table ST – Item 2 – Storage account name column>"
	$vnetName="<Table V – Item 1 – Value column>"
	$avName="<Table A – Item 1 – Availability set name column>"
	
	# Create the first domain controller
	$vmName="<Table M – Item 1 - Virtual machine name column>"
	$vmSize="<Table M – Item 1 - Minimum size column>"
	$staticIP="<Table V – Item 6 - Value column>"
	$vnet=Get-AzurevirtualNetwork -Name $vnetName -ResourceGroupName $rgName
	$nic=New-AzureNetworkInterface -Name ($vmName +"-NIC") -ResourceGroupName $rgName -Location $locName -SubnetId $vnet.Subnets[1].Id -PrivateIpAddress $staticIP
	$avSet=Get-AzureAvailabilitySet –Name $avName –ResourceGroupName $rgName 
	$vm=New-AzureVMConfig -VMName $vmName -VMSize $vmSize -AvailabilitySetId $avset.Id
	
	$diskSize=<size of the extra disk for AD DS data in GB>
	$storageAcc=Get-AzureStorageAccount -ResourceGroupName $rgName -Name $saName
	$vhdURI=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/" + $vmName + "-ADDSDisk.vhd"
	Add-AzureVMDataDisk -VM $vm -Name "ADDSData" -DiskSizeInGB $diskSize -VhdUri $vhdURI  -CreateOption empty
	
	$cred=Get-Credential -Message "Type the name and password of the local administrator account for the first domain controller." 
	$vm=Set-AzureVMOperatingSystem -VM $vm -Windows -ComputerName $vmName -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
	$vm=Set-AzureVMSourceImage -VM $vm -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2012-R2-Datacenter -Version "latest"
	$vm=Add-AzureVMNetworkInterface -VM $vm -Id $nic.Id
	$storageAcc=Get-AzureStorageAccount -ResourceGroupName $rgName -Name $saName
	$osDiskUri=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/" + $vmName + "-OSDisk.vhd"
	$vm=Set-AzureVMOSDisk -VM $vm -Name "OSDisk" -VhdUri $osDiskUri -CreateOption fromImage
	New-AzureVM -ResourceGroupName $rgName -Location $locName -VM $vm
	
	# Create the second domain controller
	$vmName="<Table M – Item 2 - Virtual machine name column>"
	$vmSize="<Table M – Item 2 - Minimum size column>"
	$staticIP="<Table V – Item 7 - Value column>"
	$vnet=Get-AzurevirtualNetwork -Name $vnetName -ResourceGroupName $rgName
	$nic=New-AzureNetworkInterface -Name ($vmName +"-NIC") -ResourceGroupName $rgName -Location $locName -SubnetId $vnet.Subnets[1].Id -PrivateIpAddress $staticIP
	$avSet=Get-AzureAvailabilitySet –Name $avName –ResourceGroupName $rgName 
	$vm=New-AzureVMConfig -VMName $vmName -VMSize $vmSize -AvailabilitySetId $avset.Id
	
	$diskSize=<size of the extra disk for AD DS data in GB>
	$storageAcc=Get-AzureStorageAccount -ResourceGroupName $rgName -Name $saName
	$vhdURI=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/" + $vmName + "-ADDSDisk.vhd"
	Add-AzureVMDataDisk -VM $vm -Name "ADDSData" -DiskSizeInGB $diskSize -VhdUri $vhdURI  -CreateOption empty
	
	$cred=Get-Credential -Message "Type the name and password of the local administrator account for the second domain controller." 
	$vm=Set-AzureVMOperatingSystem -VM $vm -Windows -ComputerName $vmName -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
	$vm=Set-AzureVMSourceImage -VM $vm -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2012-R2-Datacenter -Version "latest"
	$vm=Add-AzureVMNetworkInterface -VM $vm -Id $nic.Id
	$storageAcc=Get-AzureStorageAccount -ResourceGroupName $rgName -Name $saName
	$osDiskUri=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/" + $vmName + "-OSDisk.vhd"
	$vm=Set-AzureVMOSDisk -VM $vm -Name "OSDisk" -VhdUri $osDiskUri -CreateOption fromImage
	New-AzureVM -ResourceGroupName $rgName -Location $locName -VM $vm

> [AZURE.NOTE]이러한 가상 컴퓨터는 인트라넷 응용 프로그램용이므로 공용 IP 주소 또는 DNS 도메인 이름 레이블이 할당되지 않으며 인터넷에 노출되지 않습니다. 그러나 이는 Azure Preview 포털에서도 연결할 수 없음을 의미합니다. 가상 컴퓨터의 속성을 볼 때 **연결** 단추를 사용할 수 없습니다. 원격 데스크톱 연결 액세서리 또는 다른 원격 데스크톱 도구를 통해 해당 개인 IP 주소 또는 인트라넷 DNS 이름을 사용하여 가상 컴퓨터에 연결합니다.

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

1.	[Azure Preview 포털](https://portal.azure.com/)의 왼쪽 창에서 **모두 찾아보기 > 가상 네트워크**를 클릭하고 가상 네트워크의 이름(표 V - 항목 1 - 값 열)을 클릭합니다.
2.	가상 네트워크에 대한 창에서 **모든 설정**을 클릭합니다.
3.	**설정** 창에서 **DNS 서버**를 클릭합니다.
4.	**DNS 서버** 창에서 다음을 입력합니다.
	- **주 DNS 서버**: 표 V – 항목 6 – 값 열
	- **보조 DNS 서버**: 표 V – 항목 7 – 값 열
5.	Azure Preview 포털의 왼쪽 창에서 **모두 찾아보기 > 가상 컴퓨터**를 클릭합니다.
6.	**가상 컴퓨터 창**에서 첫 번째 도메인 컨트롤러의 이름(표 M - 항목 1 - 가상 컴퓨터 이름 열)을 클릭합니다.
7.	가상 컴퓨터의 창에서 **다시 시작**을 클릭합니다.
8.	첫 번째 도메인 컨트롤러가 시작되면 **가상 컴퓨터 창**에서 두 번째 도메인 컨트롤러의 이름(표 M - 항목 2 - 가상 컴퓨터 이름 열)을 클릭합니다.
9.	가상 컴퓨터의 창에서 **다시 시작**을 클릭합니다. 두 번째 도메인 컨트롤러가 시작될 때까지 기다립니다.

여기서는 두 도메인 컨트롤러를 다시 시작하여 온-프레미스 DNS 서버와 함께 DNS 서버로 구성되지 않도록 합니다. 이 두 도메인 컨트롤러는 모두 DNS 서버이므로 도메인 컨트롤러로 수준을 올릴 때 온-프레미스 DNS 서버와 함께 DNS 전달자로 자동 구성되었습니다.

이제 Azure 가상 네트워크의 서버가 로컬 도메인 컨트롤러를 사용하도록 Active Directory 복제 사이트를 만들어야 합니다. 도메인 관리자 계정을 사용하여 주 도메인 컨트롤러에 로그온한 다음 관리자 수준 Windows PowerShell 프롬프트에서 다음 명령을 실행합니다.

	$vnet="<Table V – Item 1 – Value column>"
	$vnetSpace="<Table V – Item 5 – Value column>"
	New-ADReplicationSite -Name $vnet 
	New-ADReplicationSubnet –Name $vnetSpace –Site $vnet

다음으로, SQL Server 가상 컴퓨터를 관리하는 사용자 계정을 추가합니다.

	New-ADUser -SamAccountName sqlservice -AccountPassword (read-host "Set user password" -assecurestring) -name "sqlservice" -enabled $true -PasswordNeverExpires $true -ChangePasswordAtLogon $false

이 다이어그램에서는 이 단계를 올바르게 완료한 경우의 구성을 보여 주며, 컴퓨터 이름은 자리 표시자로 표시되어 있습니다.

![](./media/virtual-machines-workload-high-availability-LOB-application-phase2/workload-lobapp-phase2.png)

## 다음 단계

이 워크로드를 계속 구성하려면 [3단계: SQL Server 인프라 구성](virtual-machines-workload-high-availability-LOB-application-phase3.md)으로 진행하세요.

## 추가 리소스

[Azure에서 고가용성 LOB(기간 업무) 응용 프로그램 배포](virtual-machines-workload-high-availability-LOB-application-overview.md)

[LOB(기간 업무) 응용 프로그램 아키텍처 청사진](http://msdn.microsoft.com/dn630664)

[테스트를 위한 하이브리드 클라우드에서 웹 기반 LOB 응용 프로그램 설정](../virtual-network/virtual-networks-setup-lobapp-hybrid-cloud-testing.md)

[Azure 인프라 서비스 구현 지침](virtual-machines-infrastructure-services-implementation-guidelines.md)

[Azure 인프라 서비스 워크로드: SharePoint Server 2013 팜](virtual-machines-workload-intranet-sharepoint-farm.md)

<!---HONumber=Sept15_HO3-->