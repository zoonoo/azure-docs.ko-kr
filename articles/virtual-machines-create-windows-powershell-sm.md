<properties 
	pageTitle="PowerShell 및 Azure 서비스 관리자를 사용하여 Windows 가상 컴퓨터 만들기" 
	description="Azure PowerShell을 사용하여 새 Windows 가상 컴퓨터를 신속하게 만들 수 있습니다." 
	services="virtual-machines" 
	documentationCenter="" 
	authors="JoeDavies-MSFT" 
	manager="timlt" 
	editor=""/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/29/2015" 
	ms.author="josephd"/>

# PowerShell 및 Azure 서비스 관리자를 사용하여 Windows 가상 컴퓨터 만들기

Azure PowerShell을 이미 설치한 경우 Azure PowerShell 버전 0.8.0 이상이 있어야 합니다. Azure PowerShell 명령 프롬프트에서 다음 명령을 사용하여 설치한 Azure PowerShell의 버전을 확인할 수 있습니다.

	Get-Module azure | format-table version

[Azure PowerShell을 설치 및 구성하는 방법](install-configure-powershell.md)의 지침을 사용하여 로컬 컴퓨터에 Azure PowerShell을 설치합니다\(아직 설치하지 않은 경우\). 그런 다음 Azure PowerShell 명령 프롬프트를 엽니다.

먼저 다음 명령을 사용하여 Azure에 로그인해야 합니다.

	Add-AzureAccount

Microsoft Azure 로그인 대화 상자에서 Azure 계정의 전자 메일 주소 및 해당 암호를 지정합니다.

다음으로, 여러 Azure 구독이 있는 경우 Azure 구독을 설정해야 합니다. 현재 구독 목록을 보려면 다음 명령을 실행합니다.

	Get-AzureSubscription | sort SubscriptionName | Select SubscriptionName

이제 < and > 문자를 포함하여 따옴표 안의 모든 항목을 올바른 구독 이름으로 바꾸고 다음 명령을 실행합니다.

	$subscrName="<subscription name>"
	Select-AzureSubscription -SubscriptionName $subscrName –Current

다음으로, 저장소 계정이 필요합니다. 다음 명령을 사용하여 현재 저장소 계정 목록을 표시할 수 있습니다.

	Get-AzureStorageAccount | sort Label | Select Label

저장소 계정이 없으면 새 저장소 계정을 만듭니다. 소문자와 숫자만 포함된 고유한 이름을 선택해야 합니다. 다음 명령을 사용하여 저장소 계정 이름의 고유성을 테스트할 수 있습니다.

	Test-AzureName -Storage <Proposed storage account name>

이 명령에서 "False"가 반환되면 제안한 이름이 고유한 것입니다. 저장소 계정을 만들 때 Azure 데이터 센터의 위치를 지정해야 합니다. Azure 데이터 센터 목록을 가져오려면 다음 명령을 실행합니다.

	Get-AzureLocation | sort Name | Select Name

이제 다음 명령을 사용하여 저장소 계정을 만들고 설정합니다. 저장소 계정의 이름을 입력하고, < and > 문자를 포함하여 따옴표 안의 모든 항목을 바꿉니다.

	$stAccount="<chosen storage account name>"
	$locName="<Azure location>"
	New-AzureStorageAccount -StorageAccountName $stAccount -Location $locName
	Set-AzureStorageAccount -StorageAccountName $stAccount
	Set-AzureSubscription -SubscriptionName $subscrName -CurrentStorageAccountName $stAccount

다음으로, 클라우드 서비스가 필요합니다. 기존 클라우드 서비스가 없는 경우 새로 만들어야 합니다. 문자, 숫자 및 하이픈만 포함된 고유한 이름을 선택해야 합니다. 필드의 첫 번째 및 마지막 문자는 문자 또는 숫자여야 합니다.

예를 들어 이름을 TestCS-\*UniqueSequence\*로 지정할 수 있습니다\(여기서 *UniqueSequence*는 조직의 약어\). 예를 들어 조직의 이름이 Tailspin Toys인 경우 클라우드 서비스 이름을 TestCS-Tailspin으로 지정할 수 있습니다.

다음 Azure PowerShell 명령을 사용하여 이름의 고유성을 테스트할 수 있습니다.

	Test-AzureName -Service <Proposed cloud service name>

이 명령에서 "False"가 반환되면 제안한 이름이 고유한 것입니다. 다음 명령을 사용하여 클라우드 서비스를 만듭니다.

	$csName="<cloud service name>"
	$locName="<Azure location>"
	New-AzureService -Service $csName -Location $locName

다음 PowerShell 명령 집합을 메모장과 같은 텍스트 편집기에 복사합니다.

	$vmName="<machine name>"
	$csName="<cloud service name>"
	$locName="<Azure location>"
	$vm=New-AzureVMConfig -Name $vmName -InstanceSize Medium -ImageName "a699494373c04fc0bc8f2bb1389d6106__Windows-Server-2012-R2-201503.01-en.us-127GB.vhd"
	$cred=Get-Credential -Message "Type the name and password of the local administrator account."
	$vm | Add-AzureProvisioningConfig -Windows -AdminUsername $cred.GetNetworkCredential().Username -Password $cred.GetNetworkCredential().Password
	New-AzureVM –ServiceName $csName –Location $locName -VMs $vm

텍스트 편집기에서 가상 컴퓨터 이름, 클라우드 서비스 이름 및 위치를 입력합니다.

마지막으로, 클립보드에 명령 집합을 복사한 다음 열려 있는 Azure PowerShell 명령 프롬프트를 마우스 오른쪽 버튼으로 클릭합니다. 일련의 PowerShell 명령으로 명령 집합이 실행되고, 로컬 관리자 계정의 이름 및 암호를 묻는 메시지가 나타나며, Azure 가상 컴퓨터가 만들어집니다. 명령 집합을 실행하는 예제는 다음과 같습니다.

	PS C:\> $vmName="PSTest"
	PS C:\> $csName=" TestCS-Tailspin"
	PS C:\> $locName="West US"
	PS C:\> $vm=New-AzureVMConfig -Name $vmName -InstanceSize Medium -ImageName "a699494373c04fc0bc8f2bb1389d6106__Windows-Server-2012-R2-201503.01-en.us-127GB.vhd"
	PS C:\> $cred=Get-Credential -Message "Type the name and password of the local administrator account."
	PS C:\> $vm | Add-AzureProvisioningConfig -Windows -AdminUsername $cred.GetNetworkCredential().Username -Password $cred.
	GetNetworkCredential().Password

	
	AvailabilitySetName               :
	ConfigurationSets                 : PSTest,Microsoft.WindowsAzure.Commands.ServiceManagement.Model.NetworkConfigurationSet}
	DataVirtualHardDisks              : {}
	Label                             : PSTest
	OSVirtualHardDisk                 : Microsoft.WindowsAzure.Commands.ServiceManagement.Model.OSVirtualHardDisk
	RoleName                          : PSTest
	RoleSize                          : Medium
	RoleType                          : PersistentVMRole
	WinRMCertificate                  :
	X509Certificates                  : {}
	NoExportPrivateKey                : False
	NoRDPEndpoint                     : False
	NoSSHEndpoint                     : False
	DefaultWinRmCertificateThumbprint :
	ProvisionGuestAgent               : True
	ResourceExtensionReferences       : {BGInfo}
	DataVirtualHardDisksToBeDeleted   :
	VMImageInput                      :
	
	PS C:\> New-AzureVM -ServiceName $csName -Location $locName -VMs $vm
	VERBOSE: 3:01:46 PM - Begin Operation: New-AzureVM - Create Deployment with VM PSTest
	VERBOSE: 3:02:49 PM - Completed Operation: New-AzureVM - Create Deployment with VM PSTest
	
	OperationDescription                    OperationId                            OperationStatus
	--------------------                    -----------                            --------------
	New-AzureVM                             8072cbd1-4abe-9278-9de2-8826b56e9221   Succeeded
	

## 추가 리소스

[Azure 리소스 관리자 및 PowerShell을 사용하여 Windows 가상 컴퓨터 만들기](virtual-machines-create-windows-powershell-rm.md)

[리소스 관리자 템플릿 및 PowerShell을 사용하여 Windows 가상 컴퓨터 만들기](virtual-machines-create-windows-powershell-rm-template-simple.md)

[가상 컴퓨터 설명서](http://azure.microsoft.com/documentation/services/virtual-machines/)

[Azure PowerShell을 설치 및 구성하는 방법](install-configure-powershell.md)

[Azure PowerShell을 사용하여 Windows 기반 가상 컴퓨터 만들기 및 미리 구성](virtual-machines-ps-create-preconfigure-windows-vms.md)


<!--HONumber=52-->
