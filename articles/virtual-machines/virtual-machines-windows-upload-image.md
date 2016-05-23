<properties
	pageTitle="리소스 관리자에 대한 Windows VHD 업로드 | Microsoft Azure"
	description="Resource Manager 배포 모델에서 사용할 Windows 가상 컴퓨터 이미지 업로드에 대해 알아봅니다."
	services="virtual-machines-windows"
	documentationCenter=""
	authors="dsk-2015"
	manager="timlt"
	editor="tysonn"
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-windows"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/06/2016"
	ms.author="dkshir"/>

# Resource Manager 배포를 위해 Azure에 Windows VM 이미지 업로드


이 문서에서는 Windows 운영 체제에서 VHD(가상 하드 디스크)를 업로드하는 방법을 보여 주므로 이를 통해 Azure Resource Manager 배포 모델을 사용하여 새 Windows 가상 컴퓨터(VM)를 만들 수 있습니다. Azure의 디스크 및 VHD에 대한 자세한 내용은 [가상 컴퓨터용 디스크 및 VHD에 대하여](virtual-machines-linux-about-disks-vhds.md)를 참조하세요.



## 필수 조건

이 문서에서는 사용자가 다음 작업을 수행한 것으로 가정합니다.

- **Azure 구독** - 아직 구독이 없으면, [Azure 계정을 무료로 개설](/pricing/free-trial/?WT.mc_id=A261C142F)하고 [MSDN 구독자 혜택을 활성화](/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F)하세요.

- **Azure PowerShell 버전 1.0 이상** - 아직 설치하지 않은 경우 [Azure PowerShell 설치 및 구성 방법](../powershell-install-configure.md)을 참조하세요.

- **Windows를 실행하는 가상 컴퓨터** - 온-프레미스 가상 컴퓨터를 만들기 위한 여러 도구가 있습니다. 예를 들어, [Hyper-V 역할 설치 및 가상 컴퓨터 구성](http://technet.microsoft.com/library/hh846766.aspx)을 참조하십시오. Azure에서 지원되는 Windows 운영 체제를 알아보려면, [Microsoft Azure 가상 컴퓨터에 대한 Microsoft 서버 소프트웨어 지원](https://support.microsoft.com/kb/2721672)을 참조하세요.


## VM의 파일 형식이 올바른지 확인해야 합니다.

Azure는 VHD 파일 형식으로 저장된 [1세대 가상 컴퓨터](http://blogs.technet.com/b/ausoemteam/archive/2015/04/21/deciding-when-to-use-generation-1-or-generation-2-virtual-machines-with-hyper-v.aspx)에 대한 이미지만 수용할 수 있습니다. VHD 크기는 고정된 정수 MB여야 합니다(예: 8로 나눌 수 있는 숫자). VHD에 허용되는 최대 크기는 1,023GB입니다.

- VHDX 형식의 Windows VM 이미지가 있으면, 다음 중 하나를 사용하여 VHD로 변환합니다.

	- Hyper-V: Hyper-V를 열고 왼쪽에서 로컬 컴퓨터를 선택합니다. 그 다음 위쪽 메뉴에서 **작업** > **디스크 편집...**을 클릭합니다. **다음**을 클릭하고 *VHDX 파일 경로* > **변환** > **VHD** > **고정 크기** > *새 VHD 파일 경로* 옵션을 입력하여 화면을 이동합니다. **완료**를 클릭하여 닫습니다.

	- [Convert-VHD PowerShell cmdlet](http://technet.microsoft.com/library/hh848454.aspx): 자세한 내용은 블로그 게시물 [Converting Hyper-V .vhdx to .vhd file formats](https://blogs.technet.microsoft.com/cbernier/2013/08/29/converting-hyper-v-vhdx-to-vhd-file-formats-for-use-in-windows-azure/)(Hyper-V .vhdx를 .vhd 파일 형식으로 변환)을 참조하세요.

- [VMDK 파일 형식](https://en.wikipedia.org/wiki/VMDK)의 Windows VM 이미지가 있는 경우 [Microsoft Virtual Machine Converter](https://www.microsoft.com/download/details.aspx?id=42497)를 사용하여 VHD로 변환합니다. 자세한 내용은 블로그 [VMware VMDK를 Hyper-V VHD로 변환하는 방법](http://blogs.msdn.com/b/timomta/archive/2015/06/11/how-to-convert-a-vmware-vmdk-to-hyper-v-vhd.aspx)을 참조하세요.


## VHD 업로드 준비

이 섹션에서는 Windows 가상 컴퓨터를 일반화하는 방법을 보여 줍니다. 다른 정보 사이에 있는 모든 개인 계정 정보를 제거합니다. 이 VM 이미지를 사용하여 빠르게 유사한 가상 컴퓨터를 배포하려고 할 때 일반적으로 이 작업을 수행합니다. Sysprep에 대한 자세한 내용은 [Sysprep 사용 방법: 소개](http://technet.microsoft.com/library/bb457073.aspx)를 참조하세요.

1. Windows 가상 컴퓨터에 로그인

2. 관리자로 명령 프롬프트 창을 엽니다. 디렉터리를 **%windir%\\system32\\sysprep**로 변경한 후 `sysprep.exe`를 실행합니다.

3. **시스템 준비 도구** 대화 상자에서 다음을 수행합니다.

	1. **시스템 정리 작업**에서 **시스템 OOBE(첫 실행 경험) 입력**을 선택하고 **일반화** 확인란을 선택했는지 확인합니다.

	2. **종료 옵션**에서 **종료**를 선택합니다.

	3. **확인**을 클릭합니다.

	![Sysprep 시작](./media/virtual-machines-windows-upload-image/sysprepgeneral.png)

</br> <a id="createstorage"></a>
## Azure 저장소 계정 만들기 또는 검색

VM 이미지를 업로드하려면 Azure 저장소 계정이 필요합니다. 기존 저장소 계정을 사용하거나 새 계정을 만들 수 있습니다. 이를 위해 Azure 포털 또는 PowerShell을 사용할 수 있습니다.

### Azure 포털을 사용하여 Azure 저장소 계정을 만들거나 찾으려면

1. [포털](https://portal.azure.com)에 로그인합니다.

2. **찾아보기** > **저장소 계정**을 클릭합니다.

3. 이 이미지를 업로드하는 데 사용하려는 저장소 계정이 있는지 확인합니다. 이 저장소 계정의 이름을 확인합니다. 기존 저장소 계정을 사용하는 경우 [VM 이미지 업로드](#uploadvm) 섹션을 계속 진행하면 됩니다.

4. 새 저장소 계정을 만들려면 **추가**를 클릭하고 다음 정보를 입력합니다.

	1. 저장소 계정의 **이름**을 입력합니다. 3~24자 사이의 소문자와 숫자만 포함해야 합니다. 이 이름은 저장소 계정에서 BLOB, 파일 및 기타 리소스에 액세스하는 데 사용할 URL의 일부가 됩니다.
	
	2. *Resource Manager*를 **배포 모델**로 선택합니다.

	3. 적절한 **계정 종류**, **성능**, 및 **복제** 값을 선택합니다. 값에 대해 자세히 알아보려면 정보 아이콘을 마우스 포인터로 가리킵니다.

	4. **리소스 그룹**의 *+ 새로 만들기* 또는 기존 항목을 선택합니다. 새 리소스 그룹을 만들려면 새 리소스 그룹의 이름을 입력합니다.

	5. 저장소 계정의 **위치**를 선택하고 **만들기**를 클릭합니다. 이제 계정이 **저장소 계정** 패널에 나타납니다.

		![저장소 계정 세부 정보 입력](./media/virtual-machines-windows-upload-image/portal_create_storage_account.png)

	6. 이 단계와 다음 단계에서는 저장소 계정에서 BLOB 컨테이너를 만드는 방법을 보여 줍니다. 이미지에 대한 새 BLOB 컨테이너를 만들기 위해 이미지를 업로드하는 PowerShell 명령을 사용할 수도 있기 때문에, 이것은 선택 사항입니다. 직접 만들고 싶지 않으면 [VM 이미지 업로드](#uploadvm) 섹션으로 진행합니다. 그렇지 않으면 **서비스** 타일에서 **Blob**을 클릭합니다.

		![Blob 서비스](./media/virtual-machines-windows-upload-image/portal_create_blob.png)

	7. Blob 패널이 표시되면 **+ 컨테이너**를 클릭하여 새 Blob 저장소 컨테이너를 만듭니다. 컨테이너의 이름 및 액세스 유형을 입력합니다.

		![새 Blob 만들기](./media/virtual-machines-windows-upload-image/portal_create_container.png)

  		> [AZURE.NOTE] 기본적으로 컨테이너는 전용이며 계정 소유자만 액세스할 수 있습니다. 컨테이너 속성 및 메타데이터는 제외하고 컨테이너에 있는 Blob에 대한 공용 읽기 권한을 허용하려면 **Blob** 옵션을 사용합니다. 컨테이너 및 Blob에 대한 전체 공용 읽기 권한을 허용하려면 **컨테이너** 옵션을 사용합니다.

	8. **Blob 서비스** 패널에 새 Blob 컨테이너가 나열됩니다. 이 컨테이너의 URL을 적어둡니다. PowerShell 명령이 이미지를 업로드할 때 필요합니다. URL 길이 및 화면 해상도에 따라서, URL이 일부 숨겨질 수 있습니다. 이런 경우, 오른쪽 위 모서리의 **최대화** 아이콘을 클릭하여 패널을 최대화합니다.


### PowerShell을 사용하여 Azure 저장소 계정을 만들거나 찾으려면

1. Azure PowerShell 1.0.x를 열고 Azure 계정에 로그인합니다.

		Login-AzureRmAccount

	이 명령에서는 Azure 자격 증명을 입력하기 위한 팝업 창이 열립니다.

2. 기본적으로 선택된 구독 ID가 작업하려는 ID와 다른 경우 다음 명령 중 하나를 사용하여 올바른 구독을 설정합니다.

		Set-AzureRmContext -SubscriptionId "xxxx-xxxx-xxxx-xxxx"

	또는

		Select-AzureRmSubscription -SubscriptionId "xxxx-xxxx-xxxx-xxxx"

	`Get-AzureRmSubscription` 명령을 사용하면 Azure 계정이 가진 구독을 찾을 수 있습니다.

3. 이 구독에서 사용 가능한 저장소 계정을 찾습니다.

		Get-AzureRmStorageAccount

	기존 저장소 계정을 사용하려면 [VM 이미지 업로드](#uploadvm) 섹션을 진행합니다.

4. 이 이미지를 보유할 새 저장소 계정을 만들려면 다음 단계를 따릅니다.

	1. 이 저장소 계정에 대한 리소스 그룹이 있어야 합니다. 다음을 사용하여 구독의 모든 리소스 그룹을 찾습니다.

			Get-AzureRmResourceGroup

	2. 새 리소스 그룹을 만들려면 다음 명령을 사용합니다.

			New-AzureRmResourceGroup -Name YourResourceGroup -Location "West US"

	3. 다음을 사용하여 이 리소스 그룹에 새 저장소 계정을 만듭니다.

			New-AzureRmStorageAccount -ResourceGroupName YourResourceGroup -Name YourStorageAccountName -Location "West US" -Type "Standard_GRS"


</br> <a id="uploadvm"></a>

## 저장소 계정에 VM 이미지 업로드

Azure PowerShell에서 다음 단계를 사용하여 저장소 계정에 VM 이미지를 업로드합니다. 이 계정에서 Blob 저장소 컨테이너에 이미지를 업로드합니다. 기존 컨테이너를 사용하거나 새 컨테이너를 만들 수 있습니다.

1. `Login-AzureRmAccount`를 사용하여 Azure PowerShell 1.0.x에 로그인합니다. 이전 섹션에 언급된 바와 같이, `Set-AzureRmContext -SubscriptionId "xxxx-xxxx-xxxx-xxxx"`를 사용하여 올바른 구독을 사용하고 있는지 확인합니다.

2. [Add-AzureRmVhd](https://msdn.microsoft.com/library/mt603554.aspx) cmdlet을 사용하여 일반화된 Azure VHD를 저장소 계정에 추가합니다.

		Add-AzureRmVhd -ResourceGroupName YourResourceGroup -Destination "<StorageAccountURL>/<BlobContainer>/<TargetVHDName>.vhd" -LocalFilePath <LocalPathOfVHDFile>

	여기서,
	- **StorageAccountURL**은 저장소 계정에 대한 URL입니다. 일반적인 형식은 다음과 같습니다. `https://YourStorageAccountName.blob.core.windows.net`. *YourStorageAccountName* 대신 기존 또는 새 저장소 계정 이름을 사용해야 합니다.
	- **BlobContainer**는 이미지를 저장하려는 Blob 컨테이너입니다. Cmdlet이 이 이름의 기존 Blob 컨테이너를 찾을 수 없는 경우 새 Blob 컨테이너를 만듭니다.
	- **TargetVHDName**은 이미지를 저장하는 데 사용할 이름입니다.
	- **LocalPathOfVHDFile**은 로컬 컴퓨터에 있는 .vhd 파일의 전체 경로 및 이름입니다.

	`Add-AzureRmVhd`가 성공적으로 실행되면, 다음과 유사합니다.

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

</br>
## 업로드한 이미지에서 새 VM 배포

이제 업로드된 이미지를 사용하여 새 Windows VM을 만들 수 있습니다. 이러한 단계는 위의 단계에서 업로드한 Azure PowerShell 및 VM 이미지를 사용하여 새 가상 네트워크에서 VM을 만드는 방법을 보여 줍니다.

>[AZURE.NOTE] VM 이미지는 실제로 만들어지는 가상 컴퓨터와 동일한 저장소 계정에 있어야 합니다.

### 네트워크 리소스 만들기

다음 샘플 PowerShell 스크립트를 사용하여 가상 네트워크와 새 VM에 대한 NIC를 설정합니다. **$**로 표시되는 변수에 대한 값을 응용 프로그램에 적절하게 사용합니다.

	$pip = New-AzureRmPublicIpAddress -Name $pipName -ResourceGroupName $rgName -Location $location -AllocationMethod Dynamic

	$subnetconfig = New-AzureRmVirtualNetworkSubnetConfig -Name $subnet1Name -AddressPrefix $vnetSubnetAddressPrefix

	$vnet = New-AzureRmVirtualNetwork -Name $vnetName -ResourceGroupName $rgName -Location $location -AddressPrefix $vnetAddressPrefix -Subnet $subnetconfig

	$nic = New-AzureRmNetworkInterface -Name $nicname -ResourceGroupName $rgName -Location $location -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id

### 새 VM 만들기

다음 PowerShell 스크립트는 가상 컴퓨터 구성을 설정하고 업로드된 VM 이미지를 새 설치에 대한 원본으로 사용하는 방법을 보여 줍니다. </br>

	#Enter a new user name and password in the pop-up window for the following
	$cred = Get-Credential

	#Get the storage account where the uploaded image is stored
	$storageAcc = Get-AzureRmStorageAccount -ResourceGroupName $rgName -AccountName $storageAccName

	#Set the VM name and size
	#Use "Get-Help New-AzureRmVMConfig" to know the available options for -VMsize
	$vmConfig = New-AzureRmVMConfig -VMName $vmName -VMSize "Standard_A4"

	#Set the Windows operating system configuration and add the NIC
	$vm = Set-AzureRmVMOperatingSystem -VM $vmConfig -Windows -ComputerName $computerName -Credential $cred -ProvisionVMAgent -EnableAutoUpdate

	$vm = Add-AzureRmVMNetworkInterface -VM $vm -Id $nic.Id

	#Create the OS disk URI
	$osDiskUri = '{0}vhds/{1}{2}.vhd' -f $storageAcc.PrimaryEndpoints.Blob.ToString(), $vmName.ToLower(), $osDiskName

	#Configure the OS disk to be created from the image (-CreateOption fromImage), and give the URL of the uploaded image VHD for the -SourceImageUri parameter
	#You can find this URL in the result of the Add-AzureRmVhd cmdlet above
	$vm = Set-AzureRmVMOSDisk -VM $vm -Name $osDiskName -VhdUri $osDiskUri -CreateOption fromImage -SourceImageUri $urlOfUploadedImageVhd -Windows

	#Create the new VM
	New-AzureRmVM -ResourceGroupName $rgName -Location $location -VM $vm

예를 들어 워크플로는 다음과 같을 수 있습니다.

		C:\> $pipName = "testpip6"
		C:\> $pip = New-AzureRmPublicIpAddress -Name $pipName -ResourceGroupName $rgName -Location $location -AllocationMethod Dynamic
		C:\> $subnet1Name = "testsub6"
		C:\> $nicname = "testnic6"
		C:\> $vnetName = "testvnet6"
		C:\> $subnetconfig = New-AzureRmVirtualNetworkSubnetConfig -Name $subnet1Name -AddressPrefix $vnetSubnetAddressPrefix
		C:\> $vnet = New-AzureRmVirtualNetwork -Name $vnetName -ResourceGroupName $rgName -Location $location -AddressPrefix $vnetAddressPrefix -Subnet $subnetconfig
		C:\> $nic = New-AzureRmNetworkInterface -Name $nicname -ResourceGroupName $rgName -Location $location -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id
		C:\> $vmName = "testupldvm6"
		C:\> $vmConfig = New-AzureRmVMConfig -VMName $vmName -VMSize "Standard_A4"
		C:\> $computerName = "testupldcomp6"
		C:\> $vm = Set-AzureRmVMOperatingSystem -VM $vmConfig -Windows -ComputerName $computerName -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
		C:\> $vm = Add-AzureRmVMNetworkInterface -VM $vm -Id $nic.Id
		C:\> $osDiskName = "testupos6"
		C:\> $osDiskUri = '{0}vhds/{1}{2}.vhd' -f $storageAcc.PrimaryEndpoints.Blob.ToString(), $vmName.ToLower(), $osDiskName
		C:\> $urlOfUploadedImageVhd = "https://testupldstore2.blob.core.windows.net/testblobs/WinServer12.vhd"
		C:\> $vm = Set-AzureRmVMOSDisk -VM $vm -Name $osDiskName -VhdUri $osDiskUri -CreateOption fromImage -SourceImageUri $urlOfUploadedImageVhd -Windows
		C:\> $result = New-AzureRmVM -ResourceGroupName $rgName -Location $location -VM $vm
		C:\> $result
		RequestId IsSuccessStatusCode StatusCode ReasonPhrase
		--------- ------------------- ---------- ------------
		                         True         OK OK

새로 만든 VM은 [Azure 포털](https://portal.azure.com)에서 **찾아보기** > **가상 컴퓨터**에 표시되며 다음 PowerShell 명령을 사용해도 표시할 수 있습니다.

	$vmList = Get-AzureRmVM -ResourceGroupName $rgName
	$vmList.Name


## 다음 단계

Azure PowerShell을 사용하여 새 가상 컴퓨터를 관리하려면 [Azure Resource Manager 및 PowerShell을 사용하여 가상 컴퓨터 관리](virtual-machines-windows-ps-manage.md)를 참조하세요.

<!---HONumber=AcomDC_0511_2016-->