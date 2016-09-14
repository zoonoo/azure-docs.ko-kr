<properties
	pageTitle="리소스 관리자에 대한 Windows VHD 업로드 | Microsoft Azure"
	description="Resource Manager 배포 모델에서 사용할 Windows 가상 컴퓨터 이미지 업로드에 대해 알아봅니다."
	services="virtual-machines-windows"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor="tysonn"
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-windows"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/02/2016"
	ms.author="cynthn"/>

# Resource Manager 배포를 위해 Azure에 Windows VM 이미지 업로드


이 문서에서는 VM을 신속하게 만들 수 있도록 Windows VHD(가상 하드 디스크) 이미지를 만들고 업로드하는 방법을 보여 줍니다. Azure의 디스크 및 VHD에 대한 자세한 내용은 [가상 컴퓨터용 디스크 및 VHD에 대하여](virtual-machines-linux-about-disks-vhds.md)를 참조하세요.


## 필수 조건

이 문서에서는 사용자가 다음 작업을 수행한 것으로 가정합니다.

- **Azure 구독** - 아직 구독이 없으면, [Azure 계정을 무료로 개설](/pricing/free-trial/?WT.mc_id=A261C142F)하거나 [MSDN 구독자 혜택을 활성화](/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F)하세요.

- **Azure PowerShell 버전 1.4 이상** - 아직 설치하지 않은 경우 [Azure PowerShell 설치 및 구성 방법](../powershell-install-configure.md)을 참조하세요.

- **Windows를 실행하는 가상 컴퓨터** - 온-프레미스 가상 컴퓨터를 만들기 위한 여러 도구가 있습니다. 예를 들어, [Hyper-V 역할 설치 및 가상 컴퓨터 구성](http://technet.microsoft.com/library/hh846766.aspx)을 참조하십시오. Azure에서 지원되는 Windows 운영 체제에 대한 자세한 내용은 [Microsoft Azure 가상 컴퓨터에 대한 Microsoft 서버 소프트웨어 지원](https://support.microsoft.com/kb/2721672)을 참조하세요.

- VM에서 실행되는 서버 역할이 sysprep을 지원하는지 확인합니다. 자세한 내용은 [서버 역할에 대한 Sysprep 지원](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles)을 참조하세요. Sysprep를 실행하기 전에 VM을 백업합니다.


## VM의 파일 형식이 올바른지 확인해야 합니다.

Azure에서는 VHD 파일 형식의 [1세대 가상 컴퓨터](http://blogs.technet.com/b/ausoemteam/archive/2015/04/21/deciding-when-to-use-generation-1-or-generation-2-virtual-machines-with-hyper-v.aspx)만 사용할 수 있습니다. VHD는 고정 크기여야 하고 정수 MB(즉 8로 나눌 수 있는 숫자)여야 합니다. VHD에 허용되는 최대 크기는 1,023GB입니다.

- VHDX 형식의 Windows VM 이미지가 있으면, 다음 중 하나를 사용하여 VHD로 변환합니다.

	- Hyper-V: Hyper-V를 열고 왼쪽에서 로컬 컴퓨터를 선택합니다. 그 다음 위쪽 메뉴에서 **작업** > **디스크 편집...**을 클릭합니다. **다음**을 클릭하고 *VHDX 파일 경로* > **변환** > **VHD** > **고정 크기** > *새 VHD 파일 경로* 옵션을 입력하여 화면을 이동합니다. **완료**를 클릭하여 닫습니다.

	- [Convert-VHD PowerShell cmdlet](http://technet.microsoft.com/library/hh848454.aspx): 자세한 내용은 블로그 게시물 [Converting Hyper-V .vhdx to .vhd file formats](https://blogs.technet.microsoft.com/cbernier/2013/08/29/converting-hyper-v-vhdx-to-vhd-file-formats-for-use-in-windows-azure/)(Hyper-V .vhdx를 .vhd 파일 형식으로 변환)을 참조하세요.

- [VMDK 파일 형식](https://en.wikipedia.org/wiki/VMDK)의 Windows VM 이미지가 있는 경우 [Microsoft Virtual Machine Converter](https://www.microsoft.com/download/details.aspx?id=42497)를 사용하여 VHD로 변환합니다. 자세한 내용은 블로그 [VMware VMDK를 Hyper-V VHD로 변환하는 방법](http://blogs.msdn.com/b/timomta/archive/2015/06/11/how-to-convert-a-vmware-vmdk-to-hyper-v-vhd.aspx)을 참조하세요.


## VHD 업로드 준비

이 섹션에서는 Windows 가상 컴퓨터를 일반화하는 방법을 보여 줍니다. Sysprep가 여러 정보 중에서 모든 개인 계정 정보를 제거합니다. Sysprep에 대한 자세한 내용은 [Sysprep 사용 방법: 소개](http://technet.microsoft.com/library/bb457073.aspx)를 참조하세요.

1. Windows 가상 컴퓨터에 로그인

2. 관리자로 명령 프롬프트 창을 엽니다. 디렉터리를 **%windir%\\system32\\sysprep**로 변경한 후 `sysprep.exe`를 실행합니다.

3. **시스템 준비 도구** 대화 상자에서 다음을 수행합니다.

	1. **시스템 정리 작업**에서 **시스템 OOBE(첫 실행 경험) 입력**을 선택하고 **일반화** 확인란을 선택했는지 확인합니다.

	2. **종료 옵션**에서 **종료**를 선택합니다.

	3. **확인**을 클릭합니다.

	![Sysprep 시작](./media/virtual-machines-windows-upload-image/sysprepgeneral.png)

</br>


## Azure에 로그인

1. Azure PowerShell을 열고 Azure 계정에 로그인합니다.

		Login-AzureRmAccount

	Azure 계정 자격 증명을 입력하기 위한 팝업 창이 열립니다.

2. 사용 가능한 구독에 대한 구독을 ID를 가져옵니다.

		Get-AzureRmSubscription

3. 구독 ID를 사용하여 올바른 구독을 설정합니다.

		Select-AzureRmSubscription -SubscriptionId "<subscriptionID>"

	
## 저장소 계정 가져오기

업로드한 VM 이미지를 포함할 Azure 저장소 계정이 필요합니다. 기존 저장소 계정을 사용하거나 새 계정을 만들 수 있습니다.

사용 가능한 저장소 계정을 표시합니다.

		Get-AzureRmStorageAccount

기존 저장소 계정을 사용하려면 [VM 이미지 업로드](#upload-the-vm-image-to-your-storage-account) 섹션을 진행합니다.

저장소 계정을 만들려면 다음 단계를 따릅니다.

1. 이 저장소 계정에 대한 리소스 그룹이 있어야 합니다. 다음을 사용하여 구독의 모든 리소스 그룹을 찾습니다.

		Get-AzureRmResourceGroup

2. 리소스 그룹을 만들려면 다음 명령을 사용합니다.

		New-AzureRmResourceGroup -Name <resourceGroupName> -Location <location>

3. [New-AzureRmStorageAccount](https://msdn.microsoft.com/library/mt607148.aspx) cmdlet를 사용하여 이 리소스 그룹에 저장소 계정을 만듭니다.

		New-AzureRmStorageAccount -ResourceGroupName <resourceGroupName> -Name <storageAccountName> -Location "<location>" -SkuName "<skuName>" -Kind "Storage"
			
-SkuName에 대한 유효한 값은 다음과 같습니다.

- **Standard\_LRS** - 로컬 중복 저장소
- **Standard\_ZRS** - 영역 중복 저장소
- **Standard\_GRS** - 지역 중복 저장소
- **Standard\_RAGRS** - 읽기 액세스 지역 중복 저장소
- **Premium\_LRS** - 프리미엄 로컬 중복 저장소



## 저장소 계정에 VM 이미지 업로드

[Add-AzureRmVhd](https://msdn.microsoft.com/library/mt603554.aspx) cmdlet를 사용하여 저장소 계정의 컨테이너에 이미지를 업로드합니다.

		$rgName = "<resourceGroupName>"
		$urlOfUploadedImageVhd = "<storageAccount>/<blobContainer>/<targetVHDName>.vhd"
		Add-AzureRmVhd -ResourceGroupName $rgName -Destination $urlOfUploadedImageVhd -LocalFilePath <localPathOfVHDFile>

여기서,

- **storageAccount**는 이미지의 저장소 계정 이름입니다.

- **blobContainer**는 이미지를 저장하려는 Blob 컨테이너입니다. 이 이름의 기존 blob 컨테이너가 없으면 만들어집니다.

- **targetVHDName**은 업로드된 VHD 파일에 사용할 이름입니다.

- **localPathOfVHDFile**은 로컬 컴퓨터에 있는 .vhd 파일의 전체 경로 및 이름입니다.


성공하면 다음과 유사한 응답을 얻게 됩니다.

		C:\> Add-AzureRmVhd -ResourceGroupName testUpldRG -Destination https://testupldstore2.blob.core.windows.net/testblobs/WinServer12.vhd -LocalFilePath "C:\temp\WinServer12.vhd"
		MD5 hash is being calculated for the file C:\temp\WinServer12.vhd.
		MD5 hash calculation is completed.
		Elapsed time for the operation: 00:03:35
		Creating new page blob of size 53687091712...
		Elapsed time for upload: 01:12:49

		LocalFilePath           DestinationUri
		-------------           --------------
		C:\temp\WinServer12.vhd https://testupldstore2.blob.core.windows.net/testblobs/WinServer12.vhd

이 명령은 네트워크 연결 및 VHD 파일의 크기에 따라 완료하는 데 다소 시간이 걸립니다.




## 가상 네트워크 만들기

[가상 네트워크](../virtual-network/virtual-networks-overview.md)의 VNet 및 서브넷을 만듭니다.

1. 변수 값을 사용자의 정보로 바꿉니다. 서브넷에 대한 주소 접두사를 CIDR 형식으로 제공합니다. 변수 및 서브넷을 만듭니다.

    	$rgName = "<resourceGroup>"
		$location = "<location>"
        $subnetName = "<subNetName>"
        $singleSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name $subnetName -AddressPrefix <0.0.0.0/0>
        
2. **$vnetName** 값을 가상 네트워크의 이름으로 바꿉니다. 가상 네트워크에 대한 주소 접두사를 CIDR 형식으로 제공합니다. 서브넷으로 변수 및 가상 네트워크를 만듭니다.

        $vnetName = "<vnetName>"
        $vnet = New-AzureRmVirtualNetwork -Name $vnetName -ResourceGroupName $rgName -Location $location -AddressPrefix <0.0.0.0/0> -Subnet $singleSubnet
        
            
## 공용 IP 주소 및 네트워크 인터페이스 만들기

가상 네트워크에서 가상 컴퓨터와 통신하려면 [공용 IP 주소](../virtual-network/virtual-network-ip-addresses-overview-arm.md) 및 네트워크 인터페이스가 필요합니다.

1. **$ipName** 값을 공용 IP 주소의 이름으로 바꿉니다. 변수 및 공용 IP 주소를 만듭니다.

        $ipName = "<ipName>"
        $pip = New-AzureRmPublicIpAddress -Name $ipName -ResourceGroupName $rgName -Location $location -AllocationMethod Dynamic
        
2. **$nicName** 값을 네트워크 인터페이스의 이름으로 바꿉니다. 변수 및 네트워크 인터페이스를 만듭니다.

        $nicName = "<nicName>"
        $nic = New-AzureRmNetworkInterface -Name $nicName -ResourceGroupName $rgName -Location $location -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id

		

## VM 만들기

다음 PowerShell 스크립트는 가상 컴퓨터 구성을 설정하고 업로드된 VM 이미지를 새 설치에 대한 소스로 사용하는 방법을 보여 줍니다.

>[AZURE.NOTE] VM은 업로드된 VHD 파일과 동일한 저장소 계정에 있어야 합니다.

</br>

	
	
	#Create variables
	# Enter a new user name and password to use as the local administrator account for the remotely accessing the VM
	$cred = Get-Credential
	
	# Name of the storage account where the VHD file is and where the OS disk will be created
	$storageAccName = "<storageAccountName>"
	
	# Name of the virtual machine
	$vmName = "<vmName>"
	
	# Size of the virtual machine. See the VM sizes documentation for more information: https://azure.microsoft.com/documentation/articles/virtual-machines-windows-sizes/
	$vmSize = "<vmSize>"
	
	# Computer name for the VM
	$computerName = "<computerName>"
	
	# Name of the disk that holds the OS
	$osDiskName = "<osDiskName>"

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
	$vm = Set-AzureRmVMOSDisk -VM $vm -Name $osDiskName -VhdUri $osDiskUri -CreateOption fromImage -SourceImageUri $urlOfUploadedImageVhd -Windows

	#Create the new VM
	New-AzureRmVM -ResourceGroupName $rgName -Location $location -VM $vm



완료되면 새로 만든 VM은 [Azure 포털](https://portal.azure.com)에서 **찾아보기** > **가상 컴퓨터**에 표시되며 다음 PowerShell 명령을 사용해도 표시할 수 있습니다.

	$vmList = Get-AzureRmVM -ResourceGroupName $rgName
	$vmList.Name


## 다음 단계

Azure PowerShell을 사용하여 새 가상 컴퓨터를 관리하려면 [Azure Resource Manager 및 PowerShell을 사용하여 가상 컴퓨터 관리](virtual-machines-windows-ps-manage.md)를 참조하세요.

<!---HONumber=AcomDC_0831_2016-->