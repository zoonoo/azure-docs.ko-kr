<properties
	pageTitle="VM 만들기 및 구성 | Microsoft Azure"
	description="리소스 관리자 배포 모델 및 PowerShell을 사용하여 Azure 가상 컴퓨터를 만들고 구성합니다."
	services="virtual-machines"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="10/08/2015"
	ms.author="cynthn"/>

# 리소스 관리자 및 Azure PowerShell을 사용하여 Windows 가상 컴퓨터 만들기 및 구성

> [AZURE.SELECTOR]
- [Portal - Windows](virtual-machines-windows-tutorial.md)
- [PowerShell](virtual-machines-ps-create-preconfigure-windows-resource-manager-vms.md)
- [PowerShell - Template](virtual-machines-create-windows-powershell-resource-manager-template.md)
- [Portal - Linux](virtual-machines-linux-tutorial-portal-rm.md)
- [CLI](virtual-machines-linux-tutorial.md)

<br>



[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)] [classic deployment model](virtual-machines-ps-create-preconfigure-windows-vms.md).

이러한 단계에서는 Azure 가상 컴퓨터를 만들고 구성하는 Azure PowerShell 명령 집합을 생성하는 방법을 보여줍니다. 이 구성 요소 프로세스를 사용하여 빠르게 새 Windows 기반 가상 컴퓨터에 대한 명령 집합을 만들고 기존 배포를 확장할 수 있습니다. 또한 이 프로세스를 사용하여 사용자 지정 개발/테스트 또는 IT 전문가 환경을 빠르게 구축하는 여러 명령 집합을 만들 수도 있습니다.

다음 단계에서는 빈 칸 채우기 접근 방식에 따라 Azure PowerShell 명령 집합을 만듭니다. 이 접근 방식은 PowerShell을 처음 접하거나 성공적인 구성을 위해 지정할 값만 알기를 원하는 경우에 유용할 수 있습니다. 고급 PowerShell 사용자는 명령을 가져와 고유한 변수 값("$"로 시작하는 줄)을 대체할 수 있습니다.

## 1단계: Azure PowerShell 설치

[AZURE.INCLUDE [powershell-preview](../../includes/powershell-preview-inline-include.md)]

## 2단계: 구독 설정

먼저, Azure PowerShell 프롬프트를 시작합니다.

계정에 로그인합니다.

	Login-AzureRmAccount

다음 명령을 사용하여 구독 이름을 가져옵니다.

	Get-AzureSubscription | Sort SubscriptionName | Select SubscriptionName

Azure 구독을 설정합니다. < and > 문자를 포함하여 따옴표 안의 모든 항목을 올바른 이름으로 바꿉니다.

	$subscr="<subscription name>"
	Select-AzureSubscription -SubscriptionName $subscr –Current


## 3단계: 리소스 만들기

이 섹션에서는 새 가상 컴퓨터에 대한 각 리소스를 만드는 방법을 보여줍니다.

### 리소스 그룹


리소스 관리자 배포 모델을 사용하여 만든 VM에는 리소스 그룹이 필요합니다. 필요한 경우 새 가상 컴퓨터에 대한 새 리소스 그룹을 만듭니다. < and > 문자를 포함하여 따옴표 안의 모든 항목을 올바른 이름으로 바꿉니다.

	$rgName="<resource group name>"
	$locName="<location name, such as West US>"
	New-AzureRmResourceGroup -Name $rgName -Location $locName

이 명령을 사용하여 기존 리소스 그룹을 나열할 수 있습니다.

	Get-AzureRmResourceGroup | Sort ResourceGroupName | Select ResourceGroupName

리소스 관리자 기반 가상 컴퓨터를 만들 수 있는 Azure 위치 목록을 표시하려면 다음 명령을 사용합니다.

	$loc=Get-AzureRmLocation | where { $_.Name –eq "Microsoft.Compute/virtualMachines" }
	$loc.Locations

### 저장소 계정


리소스 관리자 배포 모델을 사용하여 만든 VM에는 리소스 관리자 기반 저장소 계정이 필요합니다. 필요한 경우 다음 명령을 사용하여 새 가상 컴퓨터에 대한 새 저장소 계정을 만듭니다.

	$rgName="<resource group name>"
	$locName="<location name, such as West US>"
	$saName="<storage account name>"
	$saType="<storage account type, specify one: Standard_LRS, Standard_GRS, Standard_RAGRS, or Premium_LRS>"
	New-AzureRmStorageAccount -Name $saName -ResourceGroupName $rgName –Type $saType -Location $locName

소문자와 숫자만 포함하는 저장소 계정에 대해서는 전역적으로 고유한 이름을 선택해야 합니다. 이 명령을 사용하여 기존 저장소 계정을 나열할 수 있습니다.

	Get-AzureRmStorageAccount | Sort Name | Select Name

선택한 저장소 계정 이름이 전역으로 고유한지 테스트하려면 **Test-AzureName** 명령을 실행해야 합니다.

	Test-AzureName -Storage <Proposed storage account name>

Test-AzureName 명령에서 "False"를 표시하는 경우 제안된 이름이 고유한 것입니다.


### 공용 도메인 이름 레이블


리소스 관리자 배포 모델을 사용하여 만든 VM은 공용 도메인 이름 레이블을 사용할 수 있습니다. 레이블에는 문자, 숫자 및 하이픈만 사용할 수 있습니다. 첫 번째 및 마지막 문자는 문자 또는 숫자여야 합니다.

선택한 도메인 이름 레이블이 전역적으로 고유한지를 테스트하려면 다음 명령을 사용합니다.

	$domName="<domain name label to test>"
	$loc="<short name of an Azure location, for example, for West US, the short name is westus>"
	Test-AzureRmDnsAvailability -DomainQualifiedName $domName -Location $loc

DNSNameAvailability가 "True"인 경우 제안된 이름이 고유한 것입니다.

### 가용성 집합


필요한 경우 다음 명령을 사용하여 새 가상 컴퓨터의 새 가용성 집합을 만듭니다.

	$avName="<availability set name>"
	$rgName="<resource group name>"
	$locName="<location name, such as West US>"
	New-AzureRmAvailabilitySet –Name $avName –ResourceGroupName $rgName -Location $locName

다음 명령을 사용하여 기존 가용성 집합을 나열합니다.

	Get-AzureRmAvailabilitySet –ResourceGroupName $rgName | Sort Name | Select Name

### NAT 규칙

인터넷에서 들어오는 트래픽을 허용하고 부하 분산된 집합에 배치할 수 있도록 인바운드 NAT 규칙을 사용하여 리소스 관리자 기반 가상 컴퓨터를 구성할 수 있습니다. 두 경우 모두 부하 분산 장치 인스턴스 및 기타 설정을 지정해야 합니다. 자세한 내용은 [Azure 리소스 관리자를 사용하여 부하 분산 장치를 만드는 방법](../load-balancer/load-balancer-arm-powershell.md)을 참조하세요.

리소스 관리자 배포 모델을 사용하여 만든 VM에는 리소스 관리자 가상 네트워크가 필요합니다. 필요한 경우 새 가상 컴퓨터에 대한 하나 이상의 서브넷이 있는 새 리소스 관리자 기반 가상 네트워크를 만듭니다. 다음은 **frontendSubnet** 및 **backendSubnet**이라는 두 서브넷을 사용하는 새 가상 네트워크 **TestNet**에 대한 예제입니다.

	$rgName="LOBServers"
	$locName="West US"
	$frontendSubnet=New-AzureRmVirtualNetworkSubnetConfig -Name frontendSubnet -AddressPrefix 10.0.1.0/24
	$backendSubnet=New-AzureRmVirtualNetworkSubnetConfig -Name backendSubnet -AddressPrefix 10.0.2.0/24
	New-AzureRmVirtualNetwork -Name TestNet -ResourceGroupName $rgName -Location $locName -AddressPrefix 10.0.0.0/16 -Subnet $frontendSubnet,$backendSubnet

다음 명령을 사용하여 기존 가상 네트워크를 나열합니다.

	$rgName="<resource group name>"
	Get-AzureRmVirtualNetwork -ResourceGroupName $rgName | Sort Name | Select Name

## 4단계: 명령 집합 작성

선택한 텍스트 편집기 또는 PowerShell ISE(통합 스크립팅 환경)의 새 인스턴스를 열고 다음 줄을 복사하여 명령 집합을 시작합니다. 리소스 그룹의 이름, Azure 위치 및 새 가상 컴퓨터의 저장소 계정을 지정합니다. < and > 문자를 포함하여 따옴표 안의 모든 항목을 올바른 이름으로 바꿉니다.

	$rgName="<resource group name>"
	$locName="<Azure location, such as West US>"
	$saName="<storage account name>"

리소스 관리자 기반 가상 네트워크의 이름 및 가상 네트워크에 있는 서브넷의 인덱스 번호를 지정해야 합니다. 다음 명령을 사용하여 가상 네트워크에 대한 서브넷을 나열합니다.

	$rgName="<resource group name>"
	$vnetName="<virtual network name>"
	Get-AzureRmVirtualNetwork -Name $vnetName -ResourceGroupName $rgName | Select Subnets

서브넷 인덱스는 이 명령의 표시에서, 왼쪽에서 오른쪽으로 0부터 시작하여 연속적으로 번호를 매긴 서브넷의 번호입니다.

이 예제의 경우

	PS C:\> Get-AzureRmVirtualNetwork -Name TestNet -ResourceGroupName LOBServers | Select Subnets

	Subnets
	-------
	{frontendSubnet, backendSubnet}

frontendSubnet의 서브넷 인덱스는 0이고 backendSubnet의 서브넷 인덱스는 1입니다.

다음 줄을 명령 집합으로 복사하고 기존 가상 네트워크 이름 및 가상 컴퓨터의 서브넷 인덱스를 지정합니다.

	$vnetName="<name of an existing virtual network>"
	$subnetIndex=<index of the subnet on which to create the NIC for the virtual machine>
	$vnet=Get-AzureRmVirtualNetwork -Name $vnetName -ResourceGroupName $rgName

NIC(네트워크 인터페이스 카드)를 만듭니다. 명령 집합에 다음 옵션 중 하나를 복사하고 필요한 정보를 입력합니다.

### 옵션 1: NIC 이름을 지정하고 공용 IP 주소 할당

다음 줄을 명령 집합으로 복사하고 NIC의 이름을 지정합니다.

	$nicName="<name of the NIC of the VM>"
	$pip = New-AzureRmPublicIpAddress -Name $nicName -ResourceGroupName $rgName -Location $locName -AllocationMethod Dynamic
	$nic = New-AzureRmNetworkInterface -Name $nicName -ResourceGroupName $rgName -Location $locName -SubnetId $vnet.Subnets[$subnetIndex].Id -PublicIpAddressId $pip.Id

### 옵션 2: NIC 이름 및 DNS 도메인 이름 레이블 지정

다음 줄을 명령 집합으로 복사하고 NIC의 이름 및 전역적으로 고유한 도메인 이름 레이블을 지정합니다.

	$nicName="<name of the NIC of the VM>"
	$domName="<domain name label>"
	$pip = New-AzureRmPublicIpAddress -Name $nicName -ResourceGroupName $rgName -DomainNameLabel $domName -Location $locName -AllocationMethod Dynamic
	$nic = New-AzureRmNetworkInterface -Name $nicName -ResourceGroupName $rgName -Location $locName -SubnetId $vnet.Subnets[$subnetIndex].Id -PublicIpAddressId $pip.Id

### 옵션 3: NIC 이름을 지정하고 고정 개인 IP 주소 할당

다음 줄을 명령 집합으로 복사하고 NIC의 이름을 지정합니다.

	$nicName="<name of the NIC of the VM>"
	$staticIP="<available static IP address on the subnet>"
	$pip = New-AzureRmPublicIpAddress -Name $nicName -ResourceGroupName $rgName -Location $locName -AllocationMethod Dynamic
	$nic = New-AzureRmNetworkInterface -Name $nicName -ResourceGroupName $rgName -Location $locName -SubnetId $vnet.Subnets[$subnetIndex].Id -PublicIpAddressId $pip.Id -PrivateIpAddress $staticIP

### 옵션 4: 인바운드 NAT 규칙에 대한 NIC 이름 및 부하 분산 장치 인스턴스 지정

NIC를 만들고 인바운드 NAT 규칙에 대한 부하 분산 장치 인스턴스에 추가하려면 다음이 필요합니다.

- 가상 컴퓨터에 전달되는 트래픽에 대한 인바운드 NAT 규칙이 있는 이전에 만든 부하 분산 장치 인스턴스의 이름
- Nic에 할당할 부하 분산 장치 인스턴스의 백 엔드 주소 풀 인덱스 번호
- Nic에 할당할 인바운드 NAT 규칙의 인덱스 번호

인바운드 NAT 규칙을 사용하여 부하 분산 장치 인스턴스를 만드는 방법에 대한 자세한 내용은 [Azure 리소스 관리자를 사용하여 부하 분산 장치 만들기](../load-balancer/load-balancer-arm-powershell.md)를 참조하세요.

다음 줄을 명령 집합으로 복사하고 필요한 이름과 인덱스 번호를 지정합니다.

	$nicName="<name of the NIC of the VM>"
	$lbName="<name of the load balancer instance>"
	$bePoolIndex=<index of the back end pool, starting at 0>
	$natRuleIndex=<index of the inbound NAT rule, starting at 0>
	$lb=Get-AzureRmLoadBalancer -Name $lbName -ResourceGroupName $rgName
	$nic=New-AzureRmNetworkInterface -Name $nicName -ResourceGroupName $rgName -Location $locName -Subnet $vnet.Subnets[$subnetIndex].Id -LoadBalancerBackendAddressPool $lb.BackendAddressPools[$bePoolIndex] -LoadBalancerInboundNatRule $lb.InboundNatRules[$natRuleIndex]

$nicName 문자열은 리소스 그룹에 대해 고유해야 합니다. "LOB07 NIC"와 같은 가상 컴퓨터 이름을 문자열에 통합하는 것이 좋습니다.

### 옵션 5: 부하 분산된 집합에 대한 부하 분산 장치 인스턴스 및 NIC 이름 지정

NIC를 만들고 부하 분산된 집합에 대한 부하 분산 장치 인스턴스에 추가하려면 다음이 필요합니다.

- 부하 분산된 트래픽에 대한 규칙이 있는 이전에 만든 부하 분산 장치 인스턴스의 이름
- Nic에 할당할 부하 분산 장치 인스턴스의 백 엔드 주소 풀 인덱스 번호

부하가 분산된 트래픽에 대한 규칙을 사용하여 부하 분산 장치 인스턴스를 만드는 방법에 대한 자세한 내용은 [Azure 리소스 관리자를 사용하여 부하 분산 장치 만들기](../load-balancer/load-balancer-arm-powershell.md)를 참조하세요.

다음 줄을 명령 집합으로 복사하고 필요한 이름과 인덱스 번호를 지정합니다.

	$nicName="<name of the NIC of the VM>"
	$lbName="<name of the load balancer instance>"
	$bePoolIndex=<index of the back end pool, starting at 0>
	$lb=Get-AzureRmLoadBalancer -Name $lbName -ResourceGroupName $rgName
	$nic=New-AzureRmNetworkInterface -Name $nicName -ResourceGroupName $rgName -Location $locName -Subnet $vnet.Subnets[$subnetIndex].Id -LoadBalancerBackendAddressPool $lb.BackendAddressPools[$bePoolIndex]

이제, 로컬 VM 개체를 만들고 필요에 따라 가용성 집합에 추가합니다. 다음 두 옵션 중 하나를 명령 집합으로 복사하고 이름, 크기 및 가용성 집합 이름을 입력합니다.

옵션 1: 가상 컴퓨터 이름 및 크기를 지정합니다.

	$vmName="<VM name>"
	$vmSize="<VM size string>"
	$vm=New-AzureRmVMConfig -VMName $vmName -VMSize $vmSize

옵션 1에 대한 VM 크기 문자열의 가능한 값을 확인하려면 다음 명령을 사용합니다.

	$locName="<Azure location of your resource group>"
	Get-AzureRmVMSize -Location $locName | Select Name

옵션 2: 가상 컴퓨터 이름 및 크기를 지정하고 가용성 집합에 추가합니다.

	$vmName="<VM name>"
	$vmSize="<VM size string>"
	$avName="<availability set name>"
	$avSet=Get-AzureRmAvailabilitySet –Name $avName –ResourceGroupName $rgName
	$vm=New-AzureRmVMConfig -VMName $vmName -VMSize $vmSize -AvailabilitySetId $avset.Id

옵션 2에 대한 VM 크기 문자열의 가능한 값을 확인하려면 다음 명령을 사용합니다.

	$rgName="<resource group name>"
	$avName="<availability set name>"
	Get-AzureRmVMSize -ResourceGroupName $rgName -AvailabilitySetName $avName | Select Name

> [AZURE.NOTE]현재 리소스 관리자를 사용하여, 가상 컴퓨터를 만드는 동안에만 가용성 집합에 추가할 수 있습니다.

VM에 데이터 디스크를 더 추가하려면 다음 줄을 명령 집합으로 복사하고 디스크 설정을 지정합니다.

	$diskSize=<size of the disk in GB>
	$diskLabel="<the label on the disk>"
	$diskName="<name identifier for the disk in Azure storage, such as 21050529-DISK02>"
	$storageAcc=Get-AzureRmStorageAccount -ResourceGroupName $rgName -Name $saName
	$vhdURI=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/" + $vmName + $diskName  + ".vhd"
	Add-AzureRmVMDataDisk -VM $vm -Name $diskLabel -DiskSizeInGB $diskSize -VhdUri $vhdURI  -CreateOption empty

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
	$vm=Set-AzureRmVMOperatingSystem -VM $vm -Windows -ComputerName $vmName -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
	$vm=Set-AzureRmVMSourceImage -VM $vm -PublisherName $pubName -Offer $offerName -Skus $skuName -Version "latest"
	$vm=Add-AzureRmVMNetworkInterface -VM $vm -Id $nic.Id

마지막으로, 다음 명령을 명령 집합으로 복사하고 VM의 운영 체제 디스크에 대한 이름 식별자를 입력합니다.

	$diskName="<name identifier for the disk in Azure storage, such as OSDisk>"
	$storageAcc=Get-AzureRmStorageAccount -ResourceGroupName $rgName -Name $saName
	$osDiskUri=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/" + $diskName  + ".vhd"
	$vm=Set-AzureRmVMOSDisk -VM $vm -Name $diskName -VhdUri $osDiskUri -CreateOption fromImage
	New-AzureRmVM -ResourceGroupName $rgName -Location $locName -VM $vm

## 5단계: 명령 집합 실행

4단계에서 작성한 Azure PowerShell 명령 집합을 텍스트 편집기나 PowerShell ISE에서 검토합니다. 모든 변수를 지정하고 해당 변수에 올바른 값이 있는지 확인합니다. 또한 < and > 문자를 모두 제거했는지 확인합니다.

텍스트 편집기에서 명령을 작성한 경우 명령 집합을 클립보드로 복사한 다음, Azure PowerShell 프롬프트를 마우스 오른쪽 단추로 클릭합니다. 그러면 명령 집합이 일련의 PowerShell 명령으로 전송되고 Azure 가상 컴퓨터가 만들어집니다. 또는 Azure PowerShell ISE에서 명령 집합을 실행합니다.

이 정보를 다시 사용하여 VM을 추가로 만들려면 이 명령 집합을 PowerShell 스크립트 파일(*.ps1)로 저장할 수 있습니다.

## 예제

다음과 같은 웹 기반 LOB(기간 업무) 작업을 위해 추가 가상 컴퓨터를 만들려면 PowerShell 명령 집합이 필요합니다.

- 기존 LOBServers 리소스 그룹에 배치됨
- Windows Server 2012 R2 Datacenter 이미지를 사용함
- 이름이 LOB07이며, 기존 WEB\_AS 가용성 집합에 있음
- 기존 AZDatacenter 가상 네트워크의 프런트 엔드 서브넷(서브넷 인덱스 0)에 공용 IP 주소가 있는 NIC
- 200GB의 추가 데이터 디스크가 있음

다음은 이 가상 컴퓨터를 만드는 Azure PowerShell 명령 집합입니다.

	# Set values for existing resource group and storage account names
	$rgName="LOBServers"
	$locName="West US"
	$saName="contosolobserverssa"

	# Set the existing virtual network and subnet index
	$vnetName="AZDatacenter"
	$subnetIndex=0
	$vnet=Get-AzureRMVirtualNetwork -Name $vnetName -ResourceGroupName $rgName

	# Create the NIC
	$nicName="LOB07-NIC"
	$domName="contoso-vm-lob07"
	$pip=New-AzureRmPublicIpAddress -Name $nicName -ResourceGroupName $rgName -DomainNameLabel $domName -Location $locName -AllocationMethod Dynamic
	$nic=New-AzureRmNetworkInterface -Name $nicName -ResourceGroupName $rgName -Location $locName -SubnetId $vnet.Subnets[$subnetIndex].Id -PublicIpAddressId $pip.Id

	# Specify the name, size, and existing availability set
	$vmName="LOB07"
	$vmSize="Standard_A3"
	$avName="WEB_AS"
	$avSet=Get-AzureRmAvailabilitySet –Name $avName –ResourceGroupName $rgName
	$vm=New-AzureRmVMConfig -VMName $vmName -VMSize $vmSize -AvailabilitySetId $avset.Id

	# Add a 200 GB additional data disk
	$diskSize=200
	$diskLabel="APPStorage"
	$diskName="21050529-DISK02"
	$storageAcc=Get-AzureRmStorageAccount -ResourceGroupName $rgName -Name $saName
	$vhdURI=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/" + $vmName + $diskName  + ".vhd"
	Add-AzureRmVMDataDisk -VM $vm -Name $diskLabel -DiskSizeInGB $diskSize -VhdUri $vhdURI -CreateOption empty

	# Specify the image and local administrator account, and then add the NIC
	$pubName="MicrosoftWindowsServer"
	$offerName="WindowsServer"
	$skuName="2012-R2-Datacenter"
	$cred=Get-Credential -Message "Type the name and password of the local administrator account."
	$vm=Set-AzureRmVMOperatingSystem -VM $vm -Windows -ComputerName $vmName -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
	$vm=Set-AzureRmVMSourceImage -VM $vm -PublisherName $pubName -Offer $offerName -Skus $skuName -Version "latest"
	$vm=Add-AzureRmVMNetworkInterface -VM $vm -Id $nic.Id

	# Specify the OS disk name and create the VM
	$diskName="OSDisk"
	$storageAcc=Get-AzureRmStorageAccount -ResourceGroupName $rgName -Name $saName
	$osDiskUri=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/" + $vmName + $diskName  + ".vhd"
	$vm=Set-AzureRmVMOSDisk -VM $vm -Name $diskName -VhdUri $osDiskUri -CreateOption fromImage
	New-AzureRmVM -ResourceGroupName $rgName -Location $locName -VM $vm

## 추가 리소스

[Azure 리소스 관리자의 Azure 계산, 네트워크 및 저장소 공급자](virtual-machines-azurerm-versus-azuresm.md)

[Azure 리소스 관리자 개요](../resource-group-overview.md)

[리소스 관리자 템플릿 및 PowerShell을 사용하여 Azure 가상 컴퓨터 배포 및 관리](virtual-machines-deploy-rmtemplates-powershell.md)

[리소스 관리자 템플릿 및 PowerShell을 사용하여 Windows 가상 컴퓨터 만들기](virtual-machines-create-windows-powershell-resource-manager-template-simple)

[Azure PowerShell 설치 및 구성하는 방법](../install-configure-powershell.md)

<!---HONumber=AcomDC_0114_2016-->