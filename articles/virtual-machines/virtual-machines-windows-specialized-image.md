<properties
	pageTitle="Windows VM 복사본 만들기 | Microsoft Azure"
	description="*특수 이미지*를 만들어 Resource Manager 배포 모델에서 Windows를 실행하여 Azure 가상 컴퓨터 복사본을 만드는 방법에 대해 알아보세요."
	services="virtual-machines-windows"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-windows"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/26/2016"
	ms.author="cynthn"/>

# Azure Resource Manager 배포 모델에서 Windows 가상 컴퓨터 복사본 만들기


이 문서는 Windows를 실행하는 Azure VM(가상 컴퓨터)의 복사본을 만드는 방법에 대해 설명합니다. 특히, Azure PowerShell 및 Azure 포털을 사용하여 Azure Resource Manager 배포 모델에서 이 작업을 수행하는 방법을 설명합니다. 또한 사용자 계정과 원본 VM의 기타 상태 데이터를 유지하는 Azure VM의 *특수* 이미지를 만드는 방법을 보여 줍니다. 특수 이미지는 Windows VM을 클래식 배포 모델에서 리소스 관리자 배포 모델로 이식하거나 리소스 관리자 배포 모델에서 만든 Windows VM의 백업 복사본을 만드는 경우에 유용합니다. 이러한 방식으로 운영 체제와 데이터 디스크를 복사한 다음 네트워크 리소스를 설정하여 새 가상 컴퓨터를 만들 수 있습니다.

유사한 Windows VM의 대량 배포를 만들어야 할 경우 *일반화된* 이미지를 사용해야 합니다. 이 경우 [Windows 가상 컴퓨터를 캡처하는 방법](virtual-machines-windows-capture-image.md)을 참조하세요.



## 시작하기 전에

다음 단계를 시작하기 전에 다음 필수 조건을 충족하는지 확인합니다.

- 클래식 또는 리소스 관리자 배포 모델을 사용하여 만든 **Windows를 실행하는 Azure 가상 컴퓨터가 있습니다.** 운영 체제를 구성하고, 데이터 디스크를 연결하고, 필수 응용 프로그램 설치와 같은 기타 사용자 지정을 수행했습니다. 이 VM을 사용하여 복사본을 만듭니다. 원본 VM을 만드는 데 도움이 필요하면 [리소스 관리자를 사용하여 Windows VM을 만드는 방법](virtual-machines-windows-ps-create.md)을 참조하세요.

- 컴퓨터에 **Azure PowerShell 1.0 이상**이 설치되어 있으며 Azure 구독에 로그인되어 있습니다. 자세한 내용은 [Azure PowerShell을 설치 및 구성하는 방법](../powershell-install-configure.md)을 참조하세요.

- **AzCopy 도구**를 다운로드 및 설치했습니다. 이 도구에 대한 자세한 내용은 [AzCopy 명령줄 도구를 사용하여 데이터 전송](../storage/storage-use-azcopy.md)을 참조하세요.

- **리소스 그룹**과 **저장소 계정**, 해당 리소스 그룹에서 VHD를 복사하기 위해 만든 **Blob 컨테이너**가 있습니다. 기존 저장소 계정을 사용하거나 새 저장소 계정을 만드는 단계를 보려면 [Azure 저장소 계정 만들기 또는 찾기](virtual-machines-windows-upload-image.md#createstorage)를 참조하세요.

> [AZURE.NOTE] 원본 이미지로 두 가지 배포 모델 중 하나를 사용하여 만든 VM에도 유사한 단계가 적용됩니다. 사소한 차이는 해당 부분에서 명시합니다.


## VHD를 Resource Manager 저장소 계정에 복사


1. 가장 먼저 다음 중 하나를 수행하여 원본 VM에서 사용하는 VHD의 공간을 확보합니다.

	- 원본 가상 컴퓨터를 복사하려는 경우 해당 가상 컴퓨터를 중지 및 할당 취소합니다.

		- 클래식 배포 모델을 사용하여 만든 VM의 경우 [포털](https://portal.azure.com) **찾아보기** > **가상 컴퓨터(클래식)** > *사용자 VM* > **중지**를 클릭하거나 PowerShell 명령 `Stop-AzureVM -ServiceName <yourServiceName> -Name <yourVmName>`을 사용합니다.

		- 리소스 관리자 배포 모델을 사용하여 만든 VM의 경우 포털에 로그인한 다음 **찾아보기** > **가상 컴퓨터** > *사용자 VM* > **중지**를 클릭하거나 PowerShell 명령 `Stop-AzureRmVM -ResourceGroupName <yourResourceGroup> -Name <yourVmName>`을 사용합니다. 포털에서 VM의 상태가 **실행 중**에서 **중지됨(할당 취소됨)**으로 바뀝니다.

	- 원본 가상 컴퓨터를 마이그레이션하려는 경우에는 해당 VM을 삭제하고 남은 VHD를 사용합니다. [포털](https://portal.azure.com)에서 가상 컴퓨터로 이동한 다음 **삭제**를 클릭합니다.

1. 원본 VHD가 포함된 저장소 계정과 새 VM을 만들기 위해 VHD를 복사할 저장소 계정의 액세스 키를 찾습니다. VHD를 복사하는 원본 계정의 키를 *원본 키*라고 하며 복사 대상 계정의 키를 *대상 키*라고 합니다. 액세스 키에 대한 자세한 내용은 [Azure 저장소 계정 방법](../storage/storage-create-storage-account.md)을 참조하세요.

	- 원본 VM을 클래식 배포 모델을 사용하여 만든 경우 **찾아보기** > **저장소 계정(클래식)** > *사용자 저장소 계정* > **모든 설정** > **키**를 클릭합니다. **기본 액세스 키**로 레이블이 지정된 키를 복사합니다.

	- 원본 VM을 리소스 관리자 배포 모델을 사용하여 만든 경우 또는 새 VM에 사용할 저장소 계정의 경우 **찾아보기** > **저장소 계정** > *사용자 저장소 계정* > **모든 설정** > **액세스 키**를 클릭합니다. **key1**로 레이블이 지정된 키를 복사합니다.

1. 원본 및 대상 저장소 계정에 액세스하기 위한 URL을 가져옵니다. 포털에서 사용자의 저장소 계정으로 이동한 다음 **Blob**을 클릭합니다. 그런 다음 원본 VHD를 호스트하고 있는 컨테이너(예: 클래식 배포 모델의 경우 *vhd*) 또는 VHD를 복사하려는 대상 컨테이너를 클릭합니다. 컨테이너의 **속성**을 클릭하고 레이블이 **URL**인 텍스트를 복사합니다. 원본 및 대상 컨테이너의 URL이 모두 필요합니다. URL은 `https://myaccount.blob.core.windows.net/mycontainer`와 유사하게 표시됩니다.

1. 로컬 컴퓨터에서 명령 창을 열고 AzCopy가 설치된 폴더로 이동합니다. 이 폴더는 *C:\\Program Files (x86)\\Microsoft SDKs\\Azure\\AzCopy*와 유사합니다. 여기에서 다음 명령을 실행합니다. </br>

		AzCopy /Source:<URL_of_the_source_blob_container> /Dest:<URL_of_the_destination_blob_container> /SourceKey:<Access_key_for_the_source_storage> /DestKey:<Access_key_for_the_destination_storage> /Pattern:<File_name_of_the_VHD_you_are_copying>
</br>

>[AZURE.NOTE] 이전 단계에 설명된 대로 AzCopy를 사용하여 운영 체제와 데이터 디스크를 별도로 복사해야 합니다.


## 복사된 VHD를 사용하여 VM 만들기

이전 단계에서 복사한 VHD를 사용하여 이제 새 가상 네트워크에 리소스 관리자 기반 Windows VM을 만드는 데 Azure PowerShell을 사용할 수 있습니다. VHD는 생성될 새 가상 컴퓨터와 동일한 저장소 계정에 있어야 합니다.


다음 스크립트와 유사한 방식으로 새 VM에 대한 가상 네트워크와 NIC를 설정합니다. **$** 기호로 표시되는 변수에 대한 값을 응용 프로그램에 적절하게 사용합니다.

	$pip = New-AzureRmPublicIpAddress -Name $pipName -ResourceGroupName $rgName -Location $location -AllocationMethod Dynamic

	$subnetconfig = New-AzureRmVirtualNetworkSubnetConfig -Name $subnet1Name -AddressPrefix $vnetSubnetAddressPrefix

	$vnet = New-AzureRmVirtualNetwork -Name $vnetName -ResourceGroupName $rgName -Location $location -AddressPrefix $vnetAddressPrefix -Subnet $subnetconfig

	$nic = New-AzureRmNetworkInterface -Name $nicname -ResourceGroupName $rgName -Location $location -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id


이제 VM 구성을 설정하고 복사된 VHD를 사용하여 새 가상 컴퓨터를 만듭니다. </br>

	#Set the VM name and size
	$vmConfig = New-AzureRmVMConfig -VMName $vmName -VMSize "Standard_A2"

	#Add the NIC
	$vm = Add-AzureRmVMNetworkInterface -VM $vmConfig -Id $nic.Id

	#Add the OS disk by using the URL of the copied OS VHD
	$osDiskName = $vmName + "osDisk"
	$vm = Set-AzureRmVMOSDisk -VM $vm -Name $osDiskName -VhdUri $osDiskUri -CreateOption attach -Windows

	#Add data disks by using the URLs of the copied data VHDs at the appropriate Logical Unit Number (Lun)
	$dataDiskName = $vmName + "dataDisk"
	$vm = Add-AzureRmVMDataDisk -VM $vm -Name $dataDiskName -VhdUri $dataDiskUri -Lun 0 -CreateOption attach

데이터 및 운영 체제 디스크 URL은 다음과 유사하게 나타납니다. `https://StorageAccountName.blob.core.windows.net/BlobContainerName/DiskName.vhd` 포털에서 대상 저장소 컨테이너로 이동하고 운영 체제 또는 복사된 데이터 VHD를 클릭한 다음 URL의 내용을 복사하여 이 내용을 찾을 수 있습니다.

	#Create the new VM
	New-AzureRmVM -ResourceGroupName $rgName -Location $location -VM $vm

이 명령에 성공할 경우 다음과 유사한 출력이 표시됩니다.

	RequestId IsSuccessStatusCode StatusCode ReasonPhrase
	--------- ------------------- ---------- ------------
	                         True         OK OK


새로 만든 VM은 [Azure 포털](https://portal.azure.com)에서 **찾아보기** > **가상 컴퓨터**에 표시되며 다음 PowerShell 명령을 사용해도 표시할 수 있습니다.

	$vmList = Get-AzureRmVM -ResourceGroupName $rgName
	$vmList.Name

새 가상 컴퓨터에 로그인하려면 [포털](https://portal.azure.com)에서 VM으로 이동한 다음 **연결**을 클릭하고 원격 데스크톱 RDP 파일을 엽니다. 원본 가상 컴퓨터의 계정 자격 증명을 사용하여 새 가상 컴퓨터에 로그인합니다.


## 다음 단계

Azure PowerShell을 사용하여 새 가상 컴퓨터를 관리하려면 [Azure Resource Manager 및 PowerShell을 사용하여 가상 컴퓨터 관리](virtual-machines-windows-ps-manage.md)를 참조하세요.

<!---HONumber=AcomDC_0525_2016-->