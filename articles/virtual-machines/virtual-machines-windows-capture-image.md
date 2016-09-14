<properties
	pageTitle="Azure VM에서 VM 이미지 만들기 | Microsoft Azure"
	description="Resource Manager 배포 모델에서 만든 기존 Azure VM에서 일반화된 VM 이미지를 만드는 방법을 알아봅니다"
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
	ms.date="08/04/2016"
	ms.author="cynthn"/>

# 기존 Azure VM에서 VM 이미지를 만드는 방법


이 문서에서는 Azure PowerShell을 사용하여 기존 Azure VM의 일반화된 이미지를 만드는 방법을 보여 줍니다. 그런 다음 이미지를 사용하여 다른 VM을 만들 수 있습니다. 이 이미지에는 OS 디스크를 비롯해 가상 컴퓨터에 연결된 데이터 디스크가 포함됩니다. 이미지를 사용하여 VM을 만들 때 해당 리소스를 설정해야 하므로 이미지는 가상 네트워크 리소스를 포함하지 않습니다. 이 프로세스에서는 [일반화된 Windows 이미지](https://technet.microsoft.com/library/hh824938.aspx)를 만듭니다.


## 필수 조건

- 이러한 단계에서는 이미지를 만드는 데 사용할 Resource Manager 배포 모델에 Azure 가상 컴퓨터가 이미 있다고 가정합니다. VM 이름 및 리소스 그룹의 이름이 필요합니다. PowerShell cmdlet `Get-AzureRmResourceGroup`을 입력하여 구독에서 리소스 그룹의 목록을 가져올 수 있습니다. `Get-AzureRMVM`를 입력하여 구독에 VM의 목록을 가져올 수 있습니다.

- Azure PowerShell 버전 1.0.x을 설치해야 합니다. PowerShell을 아직 설치하지 않은 경우 설치 단계에 대해서는 [Azure PowerShell 설치 및 구성 방법](../powershell-install-configure.md)을 참조하세요.

- 가상 컴퓨터에서 실행되는 서버 역할이 Sysprep에서 지원되는지 확인합니다. 자세한 내용은 [서버 역할에 대한 Sysprep 지원](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles)을 참조하세요.

## 원본 VM을 준비합니다. 

이 섹션에서는 이미지로 사용할 수 있도록 Windows 가상 컴퓨터를 일반화하는 방법을 보여 줍니다. Sysprep를 실행하기 전에 VM을 백업합니다.

> [AZURE.WARNING] VM을 일반화하면 프로세스가 모든 사용자 계정을 제거하기 때문에 RDP를 통해 로그인할 수 없습니다. 변경 내용은 되돌릴 수 없습니다.

1. Windows 가상 컴퓨터에 로그인합니다. [Azure 포털](https://portal.azure.com)에서 **찾아보기** > **가상 컴퓨터** > Windows 가상 컴퓨터 > **연결**을 통해 이동합니다.

2. 관리자로 명령 프롬프트 창을 엽니다.

3. 디렉터리를 `%windir%\system32\sysprep`로 변경한 후 sysprep.exe를 실행합니다.

4. **시스템 준비 도구** 대화 상자에서 다음을 수행합니다.

	- **시스템 정리 작업**에서 **시스템 OOBE(첫 실행 경험) 입력**을 선택하고 **일반화**를 선택했는지 확인합니다. Sysprep 사용에 대한 자세한 내용은 [Sysprep 사용 방법: 소개](http://technet.microsoft.com/library/bb457073.aspx)를 참조하세요.

	- **종료 옵션**에서 **종료**를 선택합니다.

	- **확인**을 클릭합니다.

	![Sysprep 실행](./media/virtual-machines-windows-capture-image/SysprepGeneral.png)

   Sysprep은 가상 컴퓨터를 종료합니다. Azure 포털에서 상태가 **중지됨**으로 변경됩니다.


## Azure PowerShell에 로그인합니다.

1. Azure PowerShell을 열고 Azure 계정에 로그인합니다.

		Login-AzureRmAccount

	Azure 계정 자격 증명을 입력하기 위한 팝업 창이 열립니다.

2. 사용 가능한 구독에 대한 구독을 ID를 가져옵니다.

		Get-AzureRmSubscription

3. 구독 ID를 사용하여 올바른 구독을 설정합니다.

		Select-AzureRmSubscription -SubscriptionId "<subscriptionID>"


## VM의 할당을 취소하고 상태를 일반화됨으로 설정합니다.		

1. VM 리소스 할당을 취소합니다.

		Stop-AzureRmVM -ResourceGroupName <resourceGroup> -Name <vmName>

	Azure 포털의 VM에 대한 *상태*가 **중지됨**에서 **중지됨(할당 취소됨)**으로 변경됩니다.

2. 가상 컴퓨터의 상태를 **일반화됨**으로 설정합니다.

		Set-AzureRmVm -ResourceGroupName <resourceGroup> -Name <vmName> -Generalized

3. VM의 상태를 확인합니다. VM에 대한 **OSState/일반화됨** 섹션은 **DisplayStatus**를 **VM 일반화됨**으로 설정해야 합니다.
		
		$vm = Get-AzureRmVM -ResourceGroupName <resourceGroup> -Name <vmName> -status
		$vm.Statuses

		
## 이미지 만들기 

1. 이 명령을 사용하여 가상 컴퓨터 이미지를 대상 저장소 컨테이너에 복사합니다. 이미지는 원래 가상 컴퓨터와 동일한 저장소 계정에 만들어집니다. `-Path` 변수는 JSON 템플릿의 복사본을 로컬로 저장합니다. `-DestinationContainerName` 변수는 이미지를 유지할 컨테이너의 이름입니다. 컨테이너가 없으면 컨테이너가 만들어집니다.

		Save-AzureRmVMImage -ResourceGroupName YourResourceGroup -Name YourWindowsVM -DestinationContainerName YourImagesContainer -VHDNamePrefix YourTemplatePrefix -Path Yourlocalfilepath\Filename.json

	JSON 파일 템플릿에서 이미지의 URL을 얻을 수 있습니다. 이미지의 전체 경로에 대한 **resources** > **storageProfile** > **osDisk** > **image** > **uri** 섹션으로 이동합니다. 이미지의 URL은 `https://<storageAccountName>.blob.core.windows.net/system/Microsoft.Compute/Images/<imagesContainer>/<templatePrefix-osDisk>.xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx.vhd`과 같습니다.
	
	포털에서 URI를 확인할 수도 있습니다. 이미지는 저장소 계정에서 **시스템**라는 컨테이너에 복사됩니다.

2. 이미지에 대한 경로에 변수를 만듭니다.

		$imageURI = "<https://<storageAccountName>.blob.core.windows.net/system/Microsoft.Compute/Images/<imagesContainer>/<templatePrefix-osDisk>.xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx.vhd>"


## 가상 네트워크 만들기

[가상 네트워크](../virtual-network/virtual-networks-overview.md)의 VNet 및 서브넷을 만듭니다.
		

1. 변수 값을 사용자의 정보로 바꿉니다. 서브넷에 대한 주소 접두사를 CIDR 형식으로 제공합니다. 변수 및 서브넷을 만듭니다.

    	$rgName = "<resourceGroup>"
		$location = "<location>"
        $subnetName = "<subNetName>"
        $singleSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name $subnetName -AddressPrefix <0.0.0.0/0>
        
2. **$vnetName** 값을 가상 네트워크의 이름으로 바꿉니다. 가상 네트워크에 대한 주소 접두사를 CIDR 형식으로 제공합니다. 서브넷으로 변수 및 가상 네트워크를 만듭니다.

        $vnetName = "<vnetName>"
        $vnet = New-AzureRmVirtualNetwork -Name $vnetName -ResourceGroupName $rgName -Location $locName -AddressPrefix <0.0.0.0/0> -Subnet $singleSubnet
        
            
## 공용 IP 주소 및 네트워크 인터페이스 만들기

가상 네트워크에서 가상 컴퓨터와 통신하려면 [공용 IP 주소](../virtual-network/virtual-network-ip-addresses-overview-arm.md) 및 네트워크 인터페이스가 필요합니다.

1. **$ipName** 값을 공용 IP 주소의 이름으로 바꿉니다. 변수 및 공용 IP 주소를 만듭니다.

        $ipName = "<ipName>"
        $pip = New-AzureRmPublicIpAddress -Name $ipName -ResourceGroupName $rgName -Location $locName -AllocationMethod Dynamic
        
2. **$nicName** 값을 네트워크 인터페이스의 이름으로 바꿉니다. 변수 및 네트워크 인터페이스를 만듭니다.

        $nicName = "<nicName>"
        $nic = New-AzureRmNetworkInterface -Name $nicName -ResourceGroupName $rgName -Location $locName -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id


## VM 만들기

다음 PowerShell 스크립트는 가상 컴퓨터 구성을 설정하고 업로드된 VM 이미지를 새 설치에 대한 소스로 사용하는 방법을 보여 줍니다.

>[AZURE.NOTE] VM은 원래 VHD 파일과 동일한 저장소 계정에 있어야 합니다.

</br>

	
	
	
	#Create variables
	# Enter a new user name and password to use as the local administrator account for the remotely accessing the VM
	$cred = Get-Credential
	
	# Name of the storage account 
	$storageAccName = "<storageAccountName>"
	
	# Name of the virtual machine
	$vmName = "<vmName>"
	
	# Size of the virtual machine. See the VM sizes documentation for more information: https://azure.microsoft.com/documentation/articles/virtual-machines-windows-sizes/
	$vmSize = "<vmSize>"
	
	# Computer name for the VM
	$computerName = "<computerName>"
	
	# Name of the disk that holds the OS
	$osDiskName = "<osDiskName>"
	
	# Assign a SKU name
	# Valid values for -SkuName are: **Standard_LRS** - locally redundant storage, **Standard_ZRS** - zone redundant storage, **Standard_GRS** - geo redundant storage, **Standard_RAGRS** - read access geo redundant storage, **Premium_LRS** - premium locally redundant storage. 
	$skuName = "<skuName>"
	
	# Create a new storage account for the VM
	New-AzureRmStorageAccount -ResourceGroupName $rgName -Name $storageAccName -Location $location -SkuName $skuName -Kind "Storage"

	#Get the storage account where the uploaded image is stored
	$storageAcc = Get-AzureRmStorageAccount -ResourceGroupName $rgName -AccountName $storageAccName

	#Set the VM name and size
	#Use "Get-Help New-AzureRmVMConfig" to know the available options for -VMsize
	$vmConfig = New-AzureRmVMConfig -VMName $vmName -VMSize $vmSize

	#Set the Windows operating system configuration and add the NIC
	$vm = Set-AzureRmVMOperatingSystem -VM $vmConfig -Windows -ComputerName $computerName -Credential $cred -ProvisionVMAgent -EnableAutoUpdate

	$vm = Add-AzureRmVMNetworkInterface -VM $vm -Id $nic.Id

	#Create the OS disk URI
	$osDiskUri = '{0}vhds/{1}-{2}.vhd' -f $storageAcc.PrimaryEndpoints.Blob.ToString(), $vmName.ToLower(), $osDiskName

	#Configure the OS disk to be created from the image (-CreateOption fromImage), and give the URL of the uploaded image VHD for the -SourceImageUri parameter
	#You set this variable when you uploaded the VHD
	$vm = Set-AzureRmVMOSDisk -VM $vm -Name $osDiskName -VhdUri $osDiskUri -CreateOption fromImage -SourceImageUri $imageURI -Windows

	#Create the new VM
	New-AzureRmVM -ResourceGroupName $rgName -Location $location -VM $vm



완료되면 새로 만든 VM은 [Azure 포털](https://portal.azure.com)에서 **찾아보기** > **가상 컴퓨터**에 표시되며 다음 PowerShell 명령을 사용해도 표시할 수 있습니다.

	$vmList = Get-AzureRmVM -ResourceGroupName $rgName
	$vmList.Name


## 다음 단계

Azure PowerShell을 사용하여 새 가상 컴퓨터를 관리하려면 [Azure Resource Manager 및 PowerShell을 사용하여 가상 컴퓨터 관리](virtual-machines-windows-ps-manage.md)를 참조하세요.

<!---HONumber=AcomDC_0831_2016-->