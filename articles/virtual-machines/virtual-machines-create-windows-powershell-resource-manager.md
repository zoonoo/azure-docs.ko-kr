<properties
	pageTitle="Windows VM 만들기 | Microsoft Azure"
	description="Azure PowerShell 및 리소스 관리자 템플릿을 사용하여 Windows 가상 컴퓨터를 손쉽게 만들 수 있습니다."
	services="virtual-machines"
	documentationCenter=""
	authors="davidmu1"
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
	ms.author="davidmu"/>

# 리소스 관리자 및 PowerShell을 사용하여 Windows VM 만들기

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)] [classic deployment model](virtual-machines-create-windows-powershell-service-manager.md).

이 항목에서는 Azure 리소스 관리자 및 PowerShell을 사용하여 Windows 기반 Azure 가상 컴퓨터를 빠르게 만드는 방법을 설명합니다.

## Windows 가상 컴퓨터 만들기

Azure PowerShell을 이미 설치한 경우 Azure PowerShell 버전 1.0.0 이상이 있어야 합니다. Azure PowerShell 명령 프롬프트에서 다음 명령을 사용하여 설치한 Azure PowerShell의 버전을 확인할 수 있습니다.

	Get-Module azure | format-table version

[AZURE.INCLUDE [powershell-preview](../../includes/powershell-preview-inline-include.md)]

먼저 다음 명령을 사용하여 Azure에 로그온해야 합니다.

	Login-AzureRmAccount

Microsoft Azure 로그인 대화 상자에서 Azure 계정의 전자 메일 주소 및 해당 암호를 지정합니다.

다음으로, 여러 Azure 구독이 있는 경우 Azure 구독을 설정해야 합니다. 현재 구독 목록을 보려면 다음 명령을 실행합니다.

	Get-AzureRmSubscription | sort SubscriptionName | Select SubscriptionName

이제 < and > 문자를 포함하여 따옴표 안의 모든 항목을 올바른 구독 이름으로 바꾸고 다음 명령을 실행합니다.

	$subscrName="<subscription name>"
	Select-AzureRmSubscription -SubscriptionName $subscrName –Current

다음으로, 저장소 계정을 만들어야 합니다. 소문자와 숫자만 포함된 고유한 이름을 선택해야 합니다. 다음 명령을 사용하여 저장소 계정 이름의 고유성을 테스트할 수 있습니다.

	Test-AzureName -Storage <Proposed storage account name>

이 명령에서 "False"가 반환되면 제안한 이름이 고유한 것입니다.

Azure 데이터 센터의 위치를 지정해야 합니다. Azure 데이터 센터 목록을 가져오려면 다음 명령을 실행합니다.

	Get-AzureLocation | sort Name | Select Name

이제 다음 PowerShell 명령 블록을 텍스트 편집기에 복사합니다. < and > 문자를 포함하여 따옴표 안의 모든 항목을 바꿔 선택한 저장소 계정 및 위치를 입력합니다.

	$stName = "<chosen storage account name>"
	$locName = "<chosen Azure location name>"
	$rgName = "TestRG"
	New-AzureRmResourceGroup -Name $rgName -Location $locName
	$storageAcc = New-AzureRmStorageAccount -ResourceGroupName $rgName -Name $stName -Type "Standard_GRS" -Location $locName
	$singleSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name singleSubnet -AddressPrefix 10.0.0.0/24
	$vnet = New-AzureRmVirtualNetwork -Name TestNet -ResourceGroupName $rgName -Location $locName -AddressPrefix 10.0.0.0/16 -Subnet $singleSubnet
	$pip = New-AzureRmPublicIpAddress -Name TestPIP -ResourceGroupName $rgName -Location $locName -AllocationMethod Dynamic
	$nic = New-AzureRmNetworkInterface -Name TestNIC -ResourceGroupName $rgName -Location $locName -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id
	$cred = Get-Credential -Message "Type the name and password of the local administrator account."
	$vm = New-AzureRmVMConfig -VMName WindowsVM -VMSize "Standard_A1"
	$vm = Set-AzureRmVMOperatingSystem -VM $vm -Windows -ComputerName MyWindowsVM -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
	$vm = Set-AzureRmVMSourceImage -VM $vm -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2012-R2-Datacenter -Version "latest"
	$vm = Add-AzureRmVMNetworkInterface -VM $vm -Id $nic.Id
	$osDiskUri = $storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/WindowsVMosDisk.vhd"
	$vm = Set-AzureRmVMOSDisk -VM $vm -Name "windowsvmosdisk" -VhdUri $osDiskUri -CreateOption fromImage
	New-AzureRmVM -ResourceGroupName $rgName -Location $locName -VM $vm

클립보드에 위 명령 집합을 복사한 다음 열려 있는 Azure PowerShell 명령 프롬프트를 마우스 오른쪽 버튼으로 클릭합니다. 일련의 PowerShell 명령으로 명령 집합이 실행되고, 로컬 관리자 계정의 이름 및 암호를 묻는 메시지가 나타나며, Azure 가상 컴퓨터가 만들어집니다.

예를 들면 다음과 같습니다.

	PS C:\> $stName="contosost"
	PS C:\> $locName="West US"
	PS C:\> $rgName="TestRG"
	PS C:\> New-AzureRmResourceGroup -Name $rgName -Location $locName
	VERBOSE: 12:45:15 PM - Created resource group 'TestRG' in location 'westus'


	ResourceGroupName : TestRG
	Location          : westus
	ProvisioningState : Succeeded
	Tags              :
	Permissions       :
	                    Actions  NotActions
	                    =======  ==========
	                    *

	ResourceId        : /subscriptions/fd92919d-eeca-4f5b-840a-e45c6770d92e/resourceGroups/TestRG


	PS C:\> $storageAcc=New-AzureRmStorageAccount -ResourceGroupName $rgName -Name $stName -Type "Standard_GRS" -Location $locName
	PS C:\> $singleSubnet=New-AzureRmVirtualNetworkSubnetConfig -Name singleSubnet -AddressPrefix 10.0.0.0/24
	PS C:\> $vnet=New-AzureRmVirtualNetwork -Name TestNet3 -ResourceGroupName $rgName -Location $locName -AddressPrefix 10.0.0.0/16 -Subnet $singleSubnet
	PS C:\> $pip = New-AzureRmPublicIpAddress -Name TestNIC -ResourceGroupName $rgName -Location $locName -AllocationMethod Dynamic
	PS C:\> $nic = New-AzureRmNetworkInterface -Name TestNIC -ResourceGroupName $rgName -Location $locName -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id
	PS C:\> $cred = Get-Credential -Message "Type the name and password of the local administrator account."
	PS C:\> $vm = New-AzureRmVMConfig -VMName WindowsVM -VMSize "Standard_A1"
	PS C:\> $vm = Set-AzureRmVMOperatingSystem -VM $vm -Windows -ComputerName MyWindowsVM -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
	PS C:\> $vm = Set-AzureRmVMSourceImage -VM $vm -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2012-R2-Datacenter -Version "latest"
	PS C:\> $vm = Add-AzureRmVMNetworkInterface -VM $vm -Id $nic.Id
	PS C:\> $osDiskUri = $storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/MyWindowsVMosDisk.vhd"
	PS C:\> $vm = Set-AzureRmVMOSDisk -VM $vm -Name "windowsvmosdisk" -VhdUri $osDiskUri -CreateOption fromImage
	PS C:\> New-AzureRmVM -ResourceGroupName $rgName -Location $locName -VM $vm


	EndTime             : 4/28/2015 1:00:05 PM -07:00
	Error               :
	Output              :
	StartTime           : 4/28/2015 12:52:52 PM -07:00
	Status              : Succeeded
	TrackingOperationId : 45035a90-ea12-4e1e-87e7-2a5e9ed12c93
	RequestId           : 98c7b4fb-b26e-4a58-b17a-b0983d896aae
	StatusCode          : OK

## 추가 리소스

[Azure 리소스 관리자의 Azure 계산, 네트워크 및 저장소 공급자](virtual-machines-azurerm-versus-azuresm.md)

[Azure 리소스 관리자 개요](resource-group-overview.md)

[리소스 관리자 템플릿 및 PowerShell을 사용하여 Windows 가상 컴퓨터 만들기](virtual-machines-create-windows-powershell-resource-manager-template-simple.md)

[PowerShell 및 Azure 서비스 관리를 사용하여 Windows 가상 컴퓨터 만들기](virtual-machines-create-windows-powershell-service-manager.md)

[가상 컴퓨터 설명서](http://azure.microsoft.com/documentation/services/virtual-machines/)

[Azure PowerShell 설치 및 구성하는 방법](install-configure-powershell.md)

<!---HONumber=Oct15_HO4-->