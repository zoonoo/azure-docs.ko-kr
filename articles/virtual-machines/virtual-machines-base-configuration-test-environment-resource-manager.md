<properties
	pageTitle="Azure 리소스 관리자를 사용하는 기본 구성 테스트 환경"
	description="리소스 관리자를 사용하여 Microsoft Azure에서 간소화된 인트라넷을 시뮬레이션하는 간단한 개발/테스트 환경을 만드는 방법에 대해 알아봅니다."
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
	ms.date="10/20/2015"
	ms.author="josephd"/>

# Azure 리소스 관리자를 사용하는 기본 구성 테스트 환경

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)] [classic deployment model](virtual-machines-base-configuration-test-environment.md).

이 문서는 리소스 관리자에서 만든 가상 컴퓨터를 사용하는 Microsoft Azure 가상 네트워크에서 기본 구성 테스트 환경을 만드는 방법에 관한 단계별 지침을 제공합니다.

결과 테스트 환경을 다음에 사용할 수 있습니다.

- 응용 프로그램 개발 및 테스트
- 추가 가상 컴퓨터 및 Azure 서비스를 포함하는 사용자 고유 디자인의 확장된 테스트 환경의 초기 구성.

기본 구성 테스트 환경은 시뮬레이션을 간소화시키는 TestLab이라고 하는 클라우드 전용 가상 네트워크의 Corpnet 서브넷과 인터넷에 연결된 개인 인트라넷으로 구성됩니다.

![](./media/virtual-machines-base-configuration-test-environment-resource-manager/BC_TLG04.png)

다음과 같이 구성됩니다.

- 인트라넷 도메인 컨트롤러 및 DNS(Domain Name System) 서버로 구성된 DC1이라는 Windows Server 2012 R2 실행 Azure 가상 컴퓨터.
- 일반 응용 프로그램 및 웹 서버로 구성된 APP1이라는 Windows Server 2012 R2 실행 Azure 가상 컴퓨터.
- 인트라넷 클라이언트 역할을 하는 CLIENT1이라는 Windows Server 2012 R2 실행 Azure 가상 컴퓨터.

이 구성을 통해 DC1, APP1, CLIENT1 및 추가 Corpnet 서브넷 컴퓨터에서 다음을 수행할 수 있습니다.

- 인터넷에 연결하여 업데이트를 설치하고, 인터넷 리소스에 실시간으로 액세스하고, Microsoft Office 365 및 기타 Azure 서비스와 같은 공용 클라우드 기술에 참여
- 인터넷 또는 조직 네트워크에 연결된 사용자 컴퓨터에서 원격 데스크톱 연결을 사용하여 원격으로 관리

Azure에서 Windows Server 2012 R2 기본 구성 테스트 환경의 Corpnet 서브넷을 설정하는 단계는 다음 4단계로 구성됩니다.

1.	가상 네트워크 만들기
2.	DC1 구성
3.	APP1 구성
4.	CLIENT1 구성

아직 Azure 계정이 없는 경우에는 [Azure 평가판 사용](http://azure.microsoft.com/pricing/free-trial/)에서 무료로 가입할 수 있습니다. MSDN 구독이 있는 경우 [MSDN 구독자를 위한 Azure 혜택](http://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)을 참조하세요.

> [AZURE.NOTE]Azure의 가상 컴퓨터는 실행 중인 동안 지속적인 비용이 부과됩니다. 이 비용은 무료 평가판, MSDN 구독 또는 유료 구독에 대해 청구됩니다. 실행 중인 Azure 가상 컴퓨터의 비용에 대한 자세한 내용은 [가상 컴퓨터 가격 정보](http://azure.microsoft.com/pricing/details/virtual-machines/) 및 [Azure 가격 계산기](http://azure.microsoft.com/pricing/calculator/)를 참조하세요. 비용을 절감하려면 [Azure에서 테스트 환경 가상 컴퓨터의 비용 최소화](#costs)를 참조하세요.

## 1단계: 가상 네트워크 만들기

> [AZURE.NOTE]이 문서에는 Azure PowerShell Preview 1.0에 대한 명령이 포함되어 있습니다. Azure PowerShell 0.9.8 및 이전 버전에서 이러한 명령을 실행하려면 "-AzureRM"의 모든 인스턴스를 "-Azure"와 대체하고 모든 명령을 실행하기 전에 **Switch-azuremode AzureResourceManager** 명령을 추가합니다. 자세한 내용은 [Azure PowerShell 1.0 Preview](https://azure.microsoft.com/blog/azps-1-0-pre/)를 참조하세요.

먼저 Azure PowerShell 프롬프트를 엽니다.

다음으로, 기초 구성 테스트 랩에 대한 새 리소스 그룹을 만듭니다. 고유한 리소스 그룹 이름을 확인하려면 다음 명령을 사용하여 기존 리소스 그룹을 나열합니다.

	Get-AzureRMResourceGroup | Sort ResourceGroupName | Select ResourceGroupName

이러한 명령을 사용하여 새 리소스 그룹을 만듭니다. < and > 문자를 포함하여 따옴표 안의 모든 항목을 올바른 이름으로 바꿉니다.

	$rgName="<resource group name>"
	$locName="<location name, such as West US>"
	New-AzureRMResourceGroup -Name $rgName -Location $locName

리소스 관리자 기반 가상 컴퓨터를 사용하려면 리소스 관리자 기반 저장소 계정이 필요합니다. 소문자와 숫자만 포함하는 저장소 계정에 대해서는 전역적으로 고유한 이름을 선택해야 합니다. 이 명령을 사용하여 기존 저장소 계정을 나열할 수 있습니다.

	Get-AzureRMStorageAccount | Sort Name | Select Name

이러한 명령을 사용하여 새 테스트 환경의 새 저장소 계정을 만듭니다.

	$rgName="<your new resource group name>"
	$locName="<the location of your new resource group>"
	$saName="<storage account name>"
	New-AzureRMStorageAccount -Name $saName -ResourceGroupName $rgName –Type Standard_LRS -Location $locName

이제, 기본 구성의 Corpnet 서브넷에 호스팅하는 TestLab Azure 가상 네트워크를 만듭니다.

	$rgName="<name of your new resource group>"
	$locName="<Azure location name, such as West US>"
	$corpnetSubnet=New-AzureRMVirtualNetworkSubnetConfig -Name Corpnet -AddressPrefix 10.0.0.0/24
	New-AzureRMVirtualNetwork -Name TestLab -ResourceGroupName $rgName -Location $locName -AddressPrefix 10.0.0.0/8 -Subnet $corpnetSubnet –DNSServer 10.0.0.4

다음은 현재 구성입니다.

![](./media/virtual-machines-base-configuration-test-environment-resource-manager/BC_TLG01.png)

## 2단계: DC1 구성

DC1은 corp.contoso.com AD DS(Active Directory 도메인 서비스) 도메인의 도메인 컨트롤러이자 TestLab 가상 네트워크의 가상 컴퓨터에 대한 DNS 서버입니다.

먼저, 리소스 그룹의 이름, Azure 위치 및 저장소 계정 이름을 입력하고, 로컬 컴퓨터의 Azure PowerShell 명령 프롬프트에서 이 명령들을 실행하여 DC1에 대한 Azure 가상 컴퓨터를 만듭니다.

	$rgName="<resource group name>"
	$locName="<Azure location, such as West US>"
	$saName="<storage account name>"
	$vnet=Get-AzureRMVirtualNetwork -Name TestLab -ResourceGroupName $rgName
	$pip = New-AzureRMPublicIpAddress -Name DC1-NIC -ResourceGroupName $rgName -Location $locName -AllocationMethod Dynamic
	$nic = New-AzureRMNetworkInterface -Name DC1-NIC -ResourceGroupName $rgName -Location $locName -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id -PrivateIpAddress 10.0.0.4
	$vm=New-AzureRMVMConfig -VMName DC1 -VMSize Standard_A1
	$storageAcc=Get-AzureRMStorageAccount -ResourceGroupName $rgName -Name $saName
	$vhdURI=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/DC1-TestLab-ADDSDisk.vhd"
	Add-AzureRMVMDataDisk -VM $vm -Name ADDS-Data -DiskSizeInGB 20 -VhdUri $vhdURI  -CreateOption empty
	$cred=Get-Credential -Message "Type the name and password of the local administrator account for DC1." 
	$vm=Set-AzureRMVMOperatingSystem -VM $vm -Windows -ComputerName DC1 -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
	$vm=Set-AzureRMVMSourceImage -VM $vm -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2012-R2-Datacenter -Version "latest"
	$vm=Add-AzureRMVMNetworkInterface -VM $vm -Id $nic.Id
	$osDiskUri=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/DC1-TestLab-OSDisk.vhd"
	$vm=Set-AzureRMVMOSDisk -VM $vm -Name DC1-TestLab-OSDisk -VhdUri $osDiskUri -CreateOption fromImage
	New-AzureRMVM -ResourceGroupName $rgName -Location $locName -VM $vm

그런 다음 DC1 가상 컴퓨터에 연결합니다.

1.	Azure Preview 포털에서, 왼쪽 창에 있는 **모두 찾아보기**를 클릭하고, **찾아보기** 목록의 **가상 컴퓨터**를 클릭한 다음 **DC1** 가상 컴퓨터를 클릭합니다.  
2.	**DC1** 창에서 **연결**을 클릭합니다.
3.	메시지가 나타나면 다운로드한 DC1.rdp 파일을 엽니다.
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

1.	Azure Preview 포털에서, 왼쪽 창에 있는 모두 찾아보기를 클릭하고, 찾아보기 목록의 가상 컴퓨터를 클릭한 다음 DC1 가상 컴퓨터를 클릭합니다.
2.	DC1 창에서 연결을 클릭합니다.
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

DC1으로 연결된 원격 데스크톱 세션을 닫고 CORP\\User1 계정을 사용하여 다시 연결합니다.

다음으로, Ping 도구에 대한 트래픽을 허용하려면 관리자 수준 Windows PowerShell 명령 프롬프트에서 다음 명령을 실행합니다.

	Set-NetFirewallRule -DisplayName "File and Printer Sharing (Echo Request - ICMPv4-In)" -enabled True

다음은 현재 구성입니다.

![](./media/virtual-machines-base-configuration-test-environment-resource-manager/BC_TLG02.png)

## 3단계: APP1 구성

APP1에서는 웹 및 파일 공유 서비스를 제공합니다.

먼저, 리소스 그룹의 이름, Azure 위치 및 저장소 계정 이름을 입력하고, 로컬 컴퓨터의 Azure PowerShell 명령 프롬프트에서 이 명령들을 실행하여 APP1에 대한 Azure 가상 컴퓨터를 만듭니다.

	$rgName="<resource group name>"
	$locName="<Azure location, such as West US>"
	$saName="<storage account name>"
	$vnet=Get-AzureRMVirtualNetwork -Name TestLab -ResourceGroupName $rgName
	$pip = New-AzureRMPublicIpAddress -Name APP1-NIC -ResourceGroupName $rgName -Location $locName -AllocationMethod Dynamic
	$nic = New-AzureRMNetworkInterface -Name APP1-NIC -ResourceGroupName $rgName -Location $locName -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id
	$vm=New-AzureRMVMConfig -VMName APP1 -VMSize Standard_A1
	$storageAcc=Get-AzureRMStorageAccount -ResourceGroupName $rgName -Name $saName
	$cred=Get-Credential -Message "Type the name and password of the local administrator account for APP1." 
	$vm=Set-AzureRMVMOperatingSystem -VM $vm -Windows -ComputerName APP1 -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
	$vm=Set-AzureRMVMSourceImage -VM $vm -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2012-R2-Datacenter -Version "latest"
	$vm=Add-AzureRMVMNetworkInterface -VM $vm -Id $nic.Id
	$osDiskUri=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/APP1-TestLab-OSDisk.vhd"
	$vm=Set-AzureRMVMOSDisk -VM $vm -Name APP1-TestLab-OSDisk -VhdUri $osDiskUri -CreateOption fromImage
	New-AzureRMVM -ResourceGroupName $rgName -Location $locName -VM $vm

다음으로, APP1 로컬 관리자 계정 이름 및 암호로 APP1 가상 컴퓨터에 연결하고 관리자 수준의 Windows PowerShell 명령 프롬프트를 엽니다.

APP1과 DC1 간의 이름 확인 및 네트워크 통신을 확인하려면 **ping dc1.corp.contoso.com** 명령을 실행하고 4개의 응답을 확인합니다.

다음으로, Window Power Shell 프롬프트에서 이러한 명령을 사용하여 APP1 가상 컴퓨터로 CORP 도메인에 가입합니다.

	Add-Computer -DomainName corp.contoso.com
	Restart-Computer

Add-Computer 명령을 입력한 후에는 반드시 CORP/User1 도메인 계정 자격 증명을 제공해야 합니다.

APP1이 다시 시작되면 CORP\\User1 계정 이름 및 암호로 연결하고 관리자 수준의 Windows PowerShell 명령 프롬프트를 엽니다.

Windows PowerShell 명령 프롬프트에서 다음 명령을 사용하여 APP1을 웹 서버로 설정합니다.

	Install-WindowsFeature Web-WebServer –IncludeManagementTools

다음 명령을 사용하여 APP1에서 공유 폴더를 만들고 해당 폴더 내에 텍스트 파일을 만듭니다.

	New-Item -path c:\files -type directory
	Write-Output "This is a shared file." | out-file c:\files\example.txt
	New-SmbShare -name files -path c:\files -changeaccess CORP\User1

다음은 현재 구성입니다.

![](./media/virtual-machines-base-configuration-test-environment-resource-manager/BC_TLG03.png)

## 4단계: CLIENT1 구성

CLIENT1은 Contoso 인트라넷에서 일반적인 랩톱, 태블릿 또는 데스크톱 컴퓨터 역할을 합니다.

먼저, 리소스 그룹의 이름, Azure 위치 및 저장소 계정 이름을 입력하고, 로컬 컴퓨터의 Azure PowerShell 명령 프롬프트에서 이 명령들을 실행하여 CLIENT1에 대한 Azure 가상 컴퓨터를 만듭니다.

	$rgName="<resource group name>"
	$locName="<Azure location, such as West US>"
	$saName="<storage account name>"
	$vnet=Get-AzureRMVirtualNetwork -Name TestLab -ResourceGroupName $rgName
	$pip = New-AzureRMPublicIpAddress -Name CLIENT1-NIC -ResourceGroupName $rgName -Location $locName -AllocationMethod Dynamic
	$nic = New-AzureRMNetworkInterface -Name CLIENT1-NIC -ResourceGroupName $rgName -Location $locName -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id
	$vm=New-AzureRMVMConfig -VMName CLIENT1 -VMSize Standard_A1
	$storageAcc=Get-AzureRMStorageAccount -ResourceGroupName $rgName -Name $saName
	$cred=Get-Credential -Message "Type the name and password of the local administrator account for CLIENT1." 
	$vm=Set-AzureRMVMOperatingSystem -VM $vm -Windows -ComputerName CLIENT1 -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
	$vm=Set-AzureRMVMSourceImage -VM $vm -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2012-R2-Datacenter -Version "latest"
	$vm=Add-AzureRMVMNetworkInterface -VM $vm -Id $nic.Id	
	$osDiskUri=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/CLIENT1-TestLab-OSDisk.vhd"
	$vm=Set-AzureRMVMOSDisk -VM $vm -Name CLIENT1-TestLab-OSDisk -VhdUri $osDiskUri -CreateOption fromImage
	New-AzureRMVM -ResourceGroupName $rgName -Location $locName -VM $vm

다음으로 CLIENT1 로컬 관리자 계정 이름 및 암호로 CLIENT1 가상 컴퓨터에 연결하고 관리자 수준의 Windows PowerShell 명령 프롬프트를 엽니다.

CLIENT1과 DC1 간의 이름 확인 및 네트워크 통신을 확인하려면 Windows PowerShell 명령 프롬프트에서 **ping dc1.corp.contoso.com** 명령을 실행하고 4개의 응답을 확인합니다.

다음으로, Window Power Shell 프롬프트에서 이러한 명령을 사용하여 CLIENT1 가상 컴퓨터로 CORP 도메인에 가입합니다.

	Add-Computer -DomainName corp.contoso.com
	Restart-Computer

Add-Computer 명령을 입력한 후에는 반드시 CORP/User1 도메인 계정 자격 증명을 제공해야 합니다.

CLIENT1이 다시 시작되면 CORP\\User1 계정 이름 및 암호로 연결하고 관리자 수준의 Windows PowerShell 명령 프롬프트를 엽니다.

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

![](./media/virtual-machines-base-configuration-test-environment-resource-manager/BC_TLG04.png)

Azure 기본 구성이 응용 프로그램 개발 및 테스트 또는 추가적인 테스트 환경에 대한 준비를 마쳤습니다.

## 추가 리소스

[하이브리드 클라우드 테스트 환경](../virtual-network/virtual-networks-setup-hybrid-cloud-environment-testing.md)

[기본 구성 테스트 환경](virtual-machines-base-configuration-test-environment.md)


## <a id="costs"></a>Azure에서 테스트 환경 가상 컴퓨터의 비용 최소화

실행 중인 테스트 환경 가상 컴퓨터의 비용을 최소화하려면 다음 중 하나를 수행합니다.

- 테스트 환경을 만들고 가능한 한 신속하게 필요한 테스트 및 데모를 수행합니다. 완료되면 테스트 환경 가상 컴퓨터를 삭제합니다.
- 테스트 환경 가상 컴퓨터를 할당 취소된 상태로 종료합니다.

Azure PowerShell을 사용한 가상 컴퓨터를 종료하려면 리소스 그룹 이름을 입력하고 다음 명령을 실행합니다.

	$rgName="<your resource group name>"
	Stop-AzureRMVM -ResourceGroupName $rgName -Name "CLIENT1"
	Stop-AzureRMVM -ResourceGroupName $rgName -Name "APP1"
	Stop-AzureRMVM -ResourceGroupName $rgName -Name "DC1" -Force -StayProvisioned

중지됨(할당 취소됨) 상태에서 시작할 때 가상 컴퓨터가 제대로 작동하도록 하려면 다음 순서대로 시작해야 합니다.

1.	DC1
2.	APP1
3.	CLIENT1

Azure PowerShell을 사용하여 가상 컴퓨터를 순서대로 시작하려면, 리소스 그룹 이름을 입력하고 다음 명령을 실행합니다.

	$rgName="<your resource group name>"
	Start-AzureRMVM -ResourceGroupName $rgName -Name "DC1"
	Start-AzureRMVM -ResourceGroupName $rgName -Name "APP1"
	Start-AzureRMVM -ResourceGroupName $rgName -Name "CLIENT1"

<!---HONumber=Oct15_HO4-->