<properties
	pageTitle="리소스 관리자 및 Azure PowerShell을 사용하여 Windows 가상 컴퓨터 만들기 및 미리 구성"
	description="Azure PowerShell을 사용하여 Azure에서 Windows 및 리소스 관리자 기반 가상 컴퓨터를 만들고 미리 구성하는 방법에 대해 알아봅니다."
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
	ms.date="06/09/2015"
	ms.author="josephd"/>

# 리소스 관리자 및 Azure PowerShell을 사용하여 Windows 가상 컴퓨터 만들기 및 미리 구성

다음 단계에서는 Azure PowerShell의 리소스 관리자 모드에서 Windows 기반 Azure 가상 컴퓨터를 만들고 미리 구성하는 명령 집합을 생성하는 방법을 보여 줍니다. 이 구성 요소 프로세스를 사용하여 빠르게 새 Windows 기반 가상 컴퓨터에 대한 명령 집합을 만들고 기존 배포를 확장할 수 있습니다. 또한 이 프로세스를 사용하여 사용자 지정 개발/테스트 또는 IT 전문가 환경을 빠르게 구축하는 여러 명령 집합을 만들 수도 있습니다.

다음 단계에서는 빈 칸 채우기 접근 방식에 따라 Azure PowerShell 명령 집합을 만듭니다. 이 접근 방식은 PowerShell을 처음 접하거나 성공적인 구성을 위해 지정할 값만 알기를 원하는 경우에 유용할 수 있습니다. 고급 PowerShell 사용자는 명령을 가져와 고유한 변수 값("$"로 시작하는 줄)을 대체할 수 있습니다.

[AZURE.INCLUDE [resource-manager-pointer-to-service-management](../../includes/resource-manager-pointer-to-service-management.md)]

- [Azure PowerShell을 사용하여 Windows 기반 가상 컴퓨터 만들기 및 미리 구성](virtual-machines-ps-create-preconfigure-windows-vms.md)

## 1단계: Azure PowerShell 설치

또한 Azure PowerShell 버전 0.9.0 이상이 있어야 합니다. Azure PowerShell을 설치 및 구성하지 않은 경우 지침을 보려면 [여기](../powershell-install-configure.md)를 클릭하세요.

Azure PowerShell 프롬프트에서 다음 명령을 사용하여 설치한 Azure PowerShell의 버전을 확인할 수 있습니다.

	Get-Module azure | format-table version

다음은 예제입니다.

	Version
	-------
	0.9.0

버전 0.9.0 이상이 없는 경우 프로그램 및 기능 제어판을 사용하여 Azure PowerShell을 제거한 다음 최신 버전을 설치해야 합니다. 자세한 내용은 [Azure PowerShell을 설치 및 구성하는 방법](../powershell-install-configure.md)을 참조하세요.

## 2단계: 구독 설정

먼저, Azure PowerShell 프롬프트를 실행합니다.

그런 다음 Azure PowerShell 프롬프트에서 이러한 명령을 실행하여 Azure 구독을 설정합니다. < and > 문자를 포함하여 따옴표 안의 모든 항목을 올바른 이름으로 바꿉니다.

	$subscr="<subscription name>"
	Select-AzureSubscription -SubscriptionName $subscr –Current

이 명령의 표시에서 올바른 구독 이름을 가져올 수 있습니다.

	Get-AzureSubscription | Sort SubscriptionName | Select SubscriptionName

이제, Azure PowerShell을 리소스 관리자 모드로 전환합니다.

	Switch-AzureMode AzureResourceManager 

## 3단계: 필요한 리소스 만들기

리소스 관리자 기반 가상 컴퓨터를 사용하려면 리소스 그룹이 필요합니다. 필요한 경우 다음 명령을 사용하여 새 가상 컴퓨터에 대한 새 리소스 그룹을 만듭니다. < and > 문자를 포함하여 따옴표 안의 모든 항목을 올바른 이름으로 바꿉니다.

	$rgName="<resource group name>"
	$locName="<location name, such as West US>"
	New-AzureResourceGroup -Name $rgName -Location $locName

고유한 리소스 그룹 이름을 확인하려면 다음 명령을 사용하여 기존 리소스 그룹을 나열합니다.

	Get-AzureResourceGroup | Sort ResourceGroupName | Select ResourceGroupName

리소스 관리자 기반 가상 컴퓨터를 만들 수 있는 Azure 위치를 나열하려면 다음 명령을 사용합니다.

	$loc=Get-AzureLocation | where { $_.Name –eq "Microsoft.Compute/virtualMachines" }
	$loc.Locations

리소스 관리자 기반 가상 컴퓨터를 사용하려면 리소스 관리자 기반 저장소 계정이 필요합니다. 필요한 경우 다음 명령을 사용하여 새 가상 컴퓨터에 대한 새 저장소 계정을 만듭니다.

	$rgName="<resource group name>"
	$locName="<location name, such as West US>"
	$saName="<storage account name>"
	$saType="<storage account type, specify one: Standard_LRS, Standard_GRS, Standard_RAGRS, or Premium_LRS>"
	New-AzureStorageAccount -Name $saName -ResourceGroupName $rgName –Type $saType -Location $locName

소문자와 숫자만 포함하는 저장소 계정에 대해서는 전역적으로 고유한 이름을 선택해야 합니다. 이 명령을 사용하여 기존 저장소 계정을 나열할 수 있습니다.

	Get-AzureStorageAccount | Sort Name | Select Name

선택한 저장소 계정 이름이 전역적으로 고유한지 여부를 테스트하려면 PowerShell의 Azure 서비스 관리 모드에서 **Test-AzureName** 명령을 실행해야 합니다. 다음 명령을 사용합니다.

	Switch-AzureMode AzureServiceManagement
	Test-AzureName -Storage <Proposed storage account name>

Test-AzureName 명령에서 "False"를 표시하는 경우 제안된 이름이 고유한 것입니다. 고유한 이름을 확인한 경우 다음 명령을 사용하여 Azure PowerShell을 다시 리소스 관리자 모드로 전환합니다.

	Switch-AzureMode AzureResourceManager 

리소스 관리자 기반 가상 컴퓨터는 문자, 숫자 및 하이픈만 포함할 수 있는 공용 도메인 이름 레이블을 사용할 수 있습니다. 필드의 첫 번째 및 마지막 문자는 문자 또는 숫자여야 합니다.

선택한 도메인 이름 레이블이 전역적으로 고유한지를 테스트하려면 다음 명령을 사용합니다.

	$domName="<domain name label to test>"
	$loc="<short name of an Azure location, for example, for West US, the short name is westus>"
	Get-AzureCheckDnsAvailability -DomainQualifiedName $domName -Location $loc 
	
DNSNameAvailability가 "True"인 경우 제안된 이름이 고유한 것입니다.

리소스 관리자 기반 가상 컴퓨터를 리소스 관리자 기반 가용성 집합에 배치할 수 있습니다. 필요한 경우 다음 명령을 사용하여 새 가상 컴퓨터의 새 가용성 집합을 만듭니다.

	$avName="<availability set name>"
	$rgName="<resource group name>"
	$locName="<location name, such as West US>"
	New-AzureAvailabilitySet –Name $avName –ResourceGroupName $rgName -Location $locName

다음 명령을 사용하여 기존 가용성 집합을 나열합니다.

	Get-AzureAvailabilitySet –ResourceGroupName $rgName | Sort Name | Select Name

리소스 관리자 기반 가상 컴퓨터를 사용하려면 리소스 관리자 기반 가상 네트워크가 필요합니다. 필요한 경우 새 가상 컴퓨터에 대한 하나 이상의 서브넷이 있는 새 리소스 관리자 기반 가상 네트워크를 만듭니다. 다음은 frontendSubnet 및 backendSubnet이라는 두 서브넷을 사용하는 새 가상 네트워크에 대한 예입니다.

	$rgName="LOBServers"
	$locName="West US"
	$frontendSubnet=New-AzureVirtualNetworkSubnetConfig -Name frontendSubnet -AddressPrefix 10.0.1.0/24
	$backendSubnet=New-AzureVirtualNetworkSubnetConfig -Name backendSubnet -AddressPrefix 10.0.2.0/24
	New-AzurevirtualNetwork -Name TestNet -ResourceGroupName $rgName -Location $locName -AddressPrefix 10.0.0.0/16 -Subnet $frontendSubnet,$backendSubnet

다음 명령을 사용하여 기존 가상 네트워크를 나열합니다.

	$rgName="<resource group name>"
	Get-AzureVirtualNetwork -ResourceGroupName $rgName | Sort Name | Select Name

## 4단계: 명령 집합 작성

선택한 텍스트 편집기 또는 PowerShell ISE(통합 스크립팅 환경)의 새 인스턴스를 열고 다음 줄을 복사하여 명령 집합을 시작합니다. 리소스 그룹의 이름, Azure 위치 및 새 가상 컴퓨터의 저장소 계정을 지정합니다. < and > 문자를 포함하여 따옴표 안의 모든 항목을 올바른 이름으로 바꿉니다.

	Switch-AzureMode AzureResourceManager
	$rgName="<resource group name>"
	$locName="<Azure location, such as West US>"
	$saName="<storage account name>"

리소스 관리자 기반 가상 네트워크의 이름 및 가상 네트워크에 있는 서브넷의 인덱스 번호를 지정해야 합니다. 다음 명령을 사용하여 가상 네트워크에 대한 서브넷을 나열합니다.

	$rgName="<resource group name>"
	$vnetName="<virtual network name>"
	Get-AzureVirtualNetwork -Name $vnetName -ResourceGroupName $rgName | Select Subnets 

서브넷 인덱스는 이 명령의 표시에서, 왼쪽에서 오른쪽으로 0부터 시작하여 연속적으로 번호를 매긴 서브넷의 번호입니다.

이 예제의 경우

	PS C:> Get-AzureVirtualNetwork -Name TestNet -ResourceGroupName LOBServers | Select Subnets
	
	Subnets
	-------
	{frontendSubnet, backendSubnet}

frontendSubnet의 서브넷 인덱스는 0이고 backendSubnet의 서브넷 인덱스는 1입니다.

다음 줄을 명령 집합으로 복사하고 기존 가상 네트워크 이름 및 가상 컴퓨터의 서브넷 인덱스를 지정합니다.

	$vnetName="<name of an existing virtual network>"
	$subnetIndex=<index of the subnet on which to create the NIC for the virtual machine>
	$vnet=Get-AzurevirtualNetwork -Name $vnetName -ResourceGroupName $rgName

이제, NIC(네트워크 인터페이스 카드)를 만들고, 공용 IP 주소를 요청하고, 필요에 따라 DNS 도메인 이름 레이블을 할당합니다. 다음 두 옵션 중 하나를 명령 집합으로 복사하고 NIC 이름 및 DNS 도메인 이름 레이블을 입력합니다.

옵션 1: NIC 이름을 지정합니다.

다음 줄을 명령 집합으로 복사하고 NIC의 이름을 지정합니다.

	$nicName="<name of the NIC of the VM>"
	$pip = New-AzurePublicIpAddress -Name $nicName -ResourceGroupName $rgName -Location $locName -AllocationMethod Dynamic
	$nic = New-AzureNetworkInterface -Name $nicName -ResourceGroupName $rgName -Location $locName -SubnetId $vnet.Subnets[$subnetIndex].Id -PublicIpAddressId $pip.Id

옵션 2: NIC 이름 및 DNS 도메인 이름 레이블을 지정합니다.

다음 줄을 명령 집합으로 복사하고 NIC의 이름 및 전역적으로 고유한 도메인 이름 레이블을 지정합니다. Azure PowerShell의 서비스 관리 모드에서 가상 컴퓨터를 만들면 Azure에서 자동으로 다음 단계를 완료합니다.

	$nicName="<name of the NIC of the VM>"
	$domName="<domain name label>"
	$pip = New-AzurePublicIpAddress -Name $nicName -ResourceGroupName $rgName -DomainNameLabel $domName -Location $locName -AllocationMethod Dynamic
	$nic = New-AzureNetworkInterface -Name $nicName -ResourceGroupName $rgName -Location $locName -SubnetId $vnet.Subnets[$subnetIndex].Id -PublicIpAddressId $pip.Id

이제, 로컬 VM 개체를 만들고 필요에 따라 가용성 집합에 추가합니다. 다음 두 옵션 중 하나를 명령 집합으로 복사하고 이름, 크기 및 가용성 집합 이름을 입력합니다.

옵션 1: 가상 컴퓨터 이름 및 크기를 지정합니다.

	$vmName="<VM name>"
	$vmSize="<VM size string>"
	$vm=New-AzureVMConfig -VMName $vmName -VMSize $vmSize

옵션 1에 대한 VM 크기 문자열의 가능한 값을 확인하려면 다음 명령을 사용합니다.

	$locName="<Azure location of your resource group>"
	Get-AzureVMSize -Location $locName | Select Name

옵션 2: 가상 컴퓨터 이름 및 크기를 지정하고 가용성 집합에 추가합니다.

	$vmName="<VM name>"
	$vmSize="<VM size string>"
	$avName="<availability set name>"
	$avSet=Get-AzureAvailabilitySet –Name $avName –ResourceGroupName $rgName 
	$vm=New-AzureVMConfig -VMName $vmName -VMSize $vmSize -AvailabilitySetId $avset.Id

옵션 2에 대한 VM 크기 문자열의 가능한 값을 확인하려면 다음 명령을 사용합니다.

	$rgName="<resource group name>"
	$avName="<availability set name>"
	Get-AzureVMSize -ResourceGroupName $rgName -AvailabilitySetName $avName | Select Name

> [AZURE.NOTE]현재 리소스 관리자를 사용하여, 가상 컴퓨터를 만드는 동안에만 가용성 집합에 추가할 수 있습니다.

VM에 데이터 디스크를 더 추가하려면 다음 줄을 명령 집합으로 복사하고 디스크 설정을 지정합니다.

	$diskSize=<size of the disk in GB>
	$diskLabel="<the label on the disk>"
	$diskName="<name identifier for the disk in Azure storage, such as 21050529-DISK02>"
	$storageAcc=Get-AzureStorageAccount -ResourceGroupName $rgName -Name $saName
	$vhdURI=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/" + $vmName + $diskName  + ".vhd"
	Add-AzureVMDataDisk -VM $vm -Name $diskLabel -DiskSizeInGB $diskSize -VhdUri $vhdURI  -CreateOption empty

이제, 게시자, 제안 및 가상 컴퓨터에 대한 이미지의 SKU를 확인해야 합니다. 다음은 일반적으로 사용되는 Windows 기반 이미지의 테이블입니다.

|Publisher name | Offer name | SKU name
|:---------------------------------|:-------------------------------------------|:---------------------------------|
|MicrosoftWindowsServer | WindowsServer | 2008-R2-SP1 |
|MicrosoftWindowsServer | WindowsServer | 2012-Datacenter |
|MicrosoftWindowsServer | WindowsServer | 2012-R2-Datacenter |
|MicrosoftDynamicsNAV | DynamicsNAV | 2015 |
|MicrosoftSharePoint | MicrosoftSharePointServer | 2013 |
|MicrosoftSQLServer | SQL2014-WS2012R2 | Enterprise-Optimized-for-DW |
|MicrosoftSQLServer | SQL2014-WS2012R2 | Enterprise-Optimized-for-OLTP |
|MicrosoftWindowsServerEssentials | WindowsServerEssentials | WindowsServerEssentials |
|MicrosoftWindowsServerHPCPack | WindowsServerHPCPack | 2012R2 |

필요한 가상 컴퓨터 이미지가 나열되어 있지 않으면 [여기](resource-groups-vm-searching.md#powershell)의 지침을 따라 게시자, 제안 및 SKU 이름을 확인합니다.

다음 명령을 명령 집합으로 복사하고 게시자, 제안 및 SKU 이름을 입력합니다.

	$pubName="<Image publisher name>"
	$offerName="<Image offer name>"
	$skuName="<Image SKU name>"
	$cred=Get-Credential -Message "Type the name and password of the local administrator account." 
	$vm=Set-AzureVMOperatingSystem -VM $vm -Windows -ComputerName $vmName -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
	$vm=Set-AzureVMSourceImage -VM $vm -PublisherName $pubName -Offer $offerName -Skus $skuName -Version "latest"
	$vm=Add-AzureVMNetworkInterface -VM $vm -Id $nic.Id

마지막으로, 다음 명령을 명령 집합으로 복사하고 VM의 운영 체제 디스크에 대한 이름 식별자를 입력합니다.

	$diskName="<name identifier for the disk in Azure storage, such as OSDisk>"
	$storageAcc=Get-AzureStorageAccount -ResourceGroupName $rgName -Name $saName
	$osDiskUri=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/" + $diskName  + ".vhd"
	$vm=Set-AzureVMOSDisk -VM $vm -Name $diskName -VhdUri $osDiskUri -CreateOption fromImage
	New-AzureVM -ResourceGroupName $rgName -Location $locName -VM $vm

## 5단계: 명령 집합 실행

텍스트 편집기 또는 PowerShell ISE에서 작성한 Azure PowerShell 명령 집합(4단계의 여러 명령 블록으로 구성)을 검토합니다. 필요한 모든 변수를 지정하고 해당 변수에 올바른 값이 있는지 확인합니다. 또한 < and > 문자를 모두 제거했는지 확인합니다.

텍스트 편집기에서 명령을 작성한 경우 명령 집합을 클립보드로 복사한 다음, 열려 있는 Azure PowerShell 프롬프트를 마우스 오른쪽 단추로 클릭합니다. 그러면 명령 집합이 일련의 PowerShell 명령으로 실행되고 Azure 가상 컴퓨터가 만들어집니다. 또는 Azure PowerShell ISE에서 명령 집합을 실행합니다.

이 가상 컴퓨터를 다시 만들거나 유사한 가상 컴퓨터를 생성하는 경우 이 명령 집합을 PowerShell 스크립트 파일(*.ps1)로 저장합니다.

## 예

다음과 같은 웹 기반 LOB(기간 업무) 작업을 위해 추가 가상 컴퓨터를 만들려면 PowerShell 명령 집합이 필요합니다.

- 기존 LOBServers 리소스 그룹에 있음
- Windows Server 2012 R2 Datacenter 이미지를 사용함
- 이름이 LOB07이며, 기존 WEB_AS 가용성 집합에 있음
- 기존 AZDatacenter 가상 네트워크의 프런트 엔드 서브넷(서브넷 인덱스 0)에 공용 IP 주소가 있는 NIC
- 200GB의 추가 데이터 디스크가 있음 

다음은 4단계에서 설명한 프로세스에 따라, 이 가상 컴퓨터를 만드는 Azure PowerShell 명령입니다.

	# Switch to the Resource Manager mode	
	Switch-AzureMode AzureResourceManager
	
	# Set values for existing resource group and storage account names
	$rgName="LOBServers"
	$locName="West US"
	$saName="contosoLOBServersSA"
	
	# Set the existing virtual network and subnet index
	$vnetName="AZDatacenter"
	$subnetIndex=0
	$vnet=Get-AzurevirtualNetwork -Name $vnetName -ResourceGroupName $rgName
	
	# Create the NIC
	$nicName="AzureInterface"
	$domName="contoso-vm-lob07"
	$pip=New-AzurePublicIpAddress -Name $nicName -ResourceGroupName $rgName -DomainNameLabel $domName -Location $locName -AllocationMethod Dynamic
	$nic=New-AzureNetworkInterface -Name $nicName -ResourceGroupName $rgName -Location $locName -SubnetId $vnet.Subnets[$subnetIndex].Id -PublicIpAddressId $pip.Id
	
	# Specify the name, size, and existing availability set
	$vmName="LOB07"
	$vmSize="Standard_A3"
	$avName="WEB_AS"
	$avSet=Get-AzureAvailabilitySet –Name $avName –ResourceGroupName $rgName
	$vm=New-AzureVMConfig -VMName $vmName -VMSize $vmSize -AvailabilitySetId $avset.Id
	
	# Add a 200 GB additional data disk
	$diskSize=200
	$diskLabel="APPStorage"
	$diskName="21050529-DISK02"
	$storageAcc=Get-AzureStorageAccount -ResourceGroupName $rgName -Name $saName
	$vhdURI=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/" + $vmName + $diskName  + ".vhd"
	Add-AzureVMDataDisk -VM $vm -Name $diskLabel -DiskSizeInGB $diskSize -VhdUri $vhdURI -CreateOption empty
	
	# Specify the image and local administrator account, and then add the NIC
	$pubName="MicrosoftWindowsServer"
	$offerName="WindowsServer"
	$skuName="2012-R2-Datacenter"
	$cred=Get-Credential -Message "Type the name and password of the local administrator account." 
	$vm=Set-AzureVMOperatingSystem -VM $vm -Windows -ComputerName $vmName -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
	$vm=Set-AzureVMSourceImage -VM $vm -PublisherName $pubName -Offer $offerName -Skus $skuName -Version "latest"
	$vm=Add-AzureVMNetworkInterface -VM $vm -Id $nic.Id
	
	# Specify the OS disk name and create the VM
	$diskName="OSDisk"
	$storageAcc=Get-AzureStorageAccount -ResourceGroupName $rgName -Name $saName
	$osDiskUri=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/" + $vmName + $diskName  + ".vhd"
	$vm=Set-AzureVMOSDisk -VM $vm -Name $diskName -VhdUri $osDiskUri -CreateOption fromImage
	New-AzureVM -ResourceGroupName $rgName -Location $locName -VM $vm

## 추가 리소스

[Azure 리소스 관리자의 Azure 계산, 네트워크 및 저장소 공급자](virtual-machines-azurerm-versus-azuresm.md)

[Azure 리소스 관리자 개요](../resource-group-overview.md)

[리소스 관리자 템플릿 및 PowerShell을 사용하여 Azure 가상 컴퓨터 배포 및 관리](virtual-machines-deploy-rmtemplates-powershell.md)

[리소스 관리자 템플릿 및 PowerShell을 사용하여 Windows 가상 컴퓨터 만들기](virtual-machines-create-windows-powershell-resource-manager-template-simple)

[Azure PowerShell을 설치 및 구성하는 방법](../install-configure-powershell.md)
 

<!---HONumber=July15_HO2-->