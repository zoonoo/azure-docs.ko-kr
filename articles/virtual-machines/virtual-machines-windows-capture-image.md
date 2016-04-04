<properties
	pageTitle="리소스 관리자에서 Windows VM 캡처 | Microsoft Azure"
	description="Azure 리소스 관리자 배포 모델을 사용하여 만든, Windows 기반 Azure VM(가상 컴퓨터)의 이미지를 캡처하는 방법을 알아봅니다."
	services="virtual-machines-windows"
	documentationCenter=""
	authors="dsk-2015"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-windows"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/29/2016"
	ms.author="dkshir"/>


# 리소스 관리자 배포 모델에서 Windows 가상 컴퓨터를 캡처하는 방법

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)] [classic deployment model](virtual-machines-windows-classic-capture-image.md).


이 문서에서는 Azure PowerShell을 사용하여 Windows가 실행되는 Azure 가상 컴퓨터를 캡처하여 다른 가상 컴퓨터를 만들 때 사용하는 방법을 보여 줍니다. 이 이미지에는 OS 디스크를 비롯해 가상 컴퓨터에 연결된 데이터 디스크가 포함됩니다. Windows VM을 만드는 데 필요한 가상 네트워크 리소스가 포함되지 않으므로 이미지를 사용하는 다른 가상 컴퓨터를 만들기 전에 설정해야 합니다. 이 이미지도 [일반화된 Windows 이미지](https://technet.microsoft.com/library/hh824938.aspx)가 되도록 준비됩니다.


## 필수 조건

이 단계는 리소스 관리자 배포 모델에서 Azure 가상 컴퓨터를 이미 만들었고 응용 프로그램 설치와 같은 사용자 지정 및 데이터 디스크 연결을 비롯한 운영 체제 구성을 완료했다고 가정합니다. 아직 수행하지 않은 경우 [리소스 관리자 및 PowerShell을 사용하여 Windows VM을 만드는 방법](virtual-machines-windows-ps-create.md)을 참조하세요. [Azure 포털](https://portal.azure.com)을 사용하면 쉽게 Windows 가상 컴퓨터를 만들 수 있습니다. [Azure 포털에서 Windows 가상 컴퓨터를 만드는 방법](virtual-machines-windows-hero-tutorial.md)을 참조하세요.


## 이미지 캡처를 위한 VM 준비

이 섹션에서는 Windows 가상 컴퓨터를 일반화하는 방법을 보여 줍니다. 다른 정보 사이에 있는 모든 개인 계정 정보를 제거합니다. 이 VM 이미지를 사용하여 유사한 가상 컴퓨터를 신속히 배포하려고 할 때 일반적으로 이 작업을 수행합니다.

1. Windows 가상 컴퓨터에 로그인합니다. [Azure 포털](https://portal.azure.com)에서 **찾아보기** > **가상 컴퓨터** > Windows 가상 컴퓨터 > **연결**을 통해 이동합니다.

2. 관리자로 명령 프롬프트 창을 엽니다.

3. 디렉터리를 `%windir%\system32\sysprep`로 변경한 후 sysprep.exe를 실행합니다.

4. **시스템 준비 도구** 대화 상자에서 다음을 수행합니다.

	- **시스템 정리 작업**에서 **시스템 OOBE(첫 실행 경험) 입력**을 선택하고 **일반화**를 선택했는지 확인합니다. Sysprep 사용에 대한 자세한 내용은 [Sysprep 사용 방법: 소개](http://technet.microsoft.com/library/bb457073.aspx)를 참조하세요.

	- **종료 옵션**에서 **종료**를 선택합니다.

	- **확인**을 클릭합니다.

	![Sysprep 실행](./media/virtual-machines-windows-capture-image/SysprepGeneral.png)

5.	Sysprep은 가상 컴퓨터를 종료합니다. Azure 포털에서 상태가 **중지됨**으로 변경됩니다.


</br>
## VM 캡처

Azure PowerShell 또는 새 ARM(Azure 리소스 관리자) 탐색기 도구를 사용하여 일반화된 Windows VM을 캡처할 수 있습니다. 이 섹션에서는 두 가지 모두에 대한 단계를 보여줍니다.

### PowerShell 사용

이 문서에서는 Azure PowerShell 버전 1.0.x를 설치했다고 가정합니다. 새 리소스 관리자 기능은 이전 PowerShell 버전에 추가되지 않으므로 이 버전을 사용하는 것이 좋습니다. 버전 차이점에 대해 자세히 알아보려면 [Azure PowerShell 1.0](https://azure.microsoft.com/blog/azps-1-0/)을 참조하세요.

1. Azure PowerShell 1.0.x를 열고 Azure 계정에 로그인합니다.

		Login-AzureRmAccount

	이 명령에서는 Azure 자격 증명을 입력하기 위한 팝업 창이 열립니다.

2. 기본적으로 선택된 구독 ID가 작업하려는 ID와 다른 경우 다음 중 하나를 사용하여 올바른 구독을 설정합니다.

		Set-AzureRmContext -SubscriptionId "xxxx-xxxx-xxxx-xxxx"

	또는

		Select-AzureRmSubscription -SubscriptionId "xxxx-xxxx-xxxx-xxxx"

	`Get-AzureRmSubscription` 명령을 사용하면 Azure 계정이 가진 구독을 찾을 수 있습니다.

3. 이제 가상 컴퓨터에서 사용되는 리소스의 할당을 취소해야 합니다.

		Stop-AzureRmVM -ResourceGroupName YourResourceGroup -Name YourWindowsVM

	Azure 포털의 VM에 대한 상태가 **중지됨**에서 **중지됨(할당 취소됨)**으로 변경된 것을 확인할 수 있습니다.

	>[AZURE.TIP] 다음을 사용하여 PowerShell에서 가상 컴퓨터의 상태를 찾을 수도 있습니다. </br> `$vm = Get-AzureRmVM -ResourceGroupName YourResourceGroup -Name YourWindowsVM -status`</br> `$vm.Statuses`</br> **DisplayStatus** 필드는 Azure 포털에 표시된 **상태**에 해당합니다.

4. 다음에는 가상 컴퓨터의 상태를 _일반화됨_으로 설정해야 합니다. 이를 수행해야 하는 이유는 위의 일반화 단계(`sysprep`)는 Azure가 이해할 수 있는 방식으로 수행하지 않기 때문입니다.

		Set-AzureRmVm -ResourceGroupName YourResourceGroup -Name YourWindowsVM -Generalized

	>[AZURE.NOTE] 위에 설정된 일반화된 상태는 포털에 표시되지 않습니다. 그러나 위의 팁에서 설명한 것처럼 Get-AzureRmVM 명령을 사용하여 이를 확인할 수 있습니다.

5. 이 명령을 사용하여 가상 컴퓨터 이미지를 대상 저장소 컨테이너에 캡처합니다.

		Save-AzureRmVMImage -ResourceGroupName YourResourceGroup -VMName YourWindowsVM -DestinationContainerName YourImagesContainer -VHDNamePrefix YourTemplatePrefix -Path Yourlocalfilepath\Filename.json

	`-Path` 변수는 선택 사항이며 로컬로 JSON 템플릿을 저장하는 데 사용할 수 있습니다. `-DestinationContainerName` 변수는 이미지를 유지할 컨테이너의 이름입니다. 저장된 이미지의 URL은 `https://YourStorageAccountName.blob.core.windows.net/system/Microsoft.Compute/Images/YourImagesContainer/YourTemplatePrefix-osDisk.xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx.vhd`와 유사합니다. 원래 가상 컴퓨터와 동일한 저장소 계정에 만들어집니다.

	>[AZURE.NOTE] 이미지의 위치를 찾으려면 로컬 JSON 파일 템플릿을 엽니다. 이미지의 전체 경로에 대한 **리소스** > **storageProfile** > **osDisk** > **이미지** > **uri** 섹션으로 이동합니다. 현재는 저장소 계정의 _시스템_ 컨테이너가 숨겨져 있으므로 포털에서 이러한 이미지를 쉽게 확인할 수 있는 방법이 없습니다. 이러한 이유로 `-Path` 변수가 선택 사항이긴 하지만 이 변수를 사용하면 확실하게 템플릿을 로컬에 저장하고 이미지 URL을 쉽게 찾을 수 있습니다. 이에 대한 대안으로 다음 섹션에서 설명하는 **Azure 저장소 탐색기**라는 도구를 사용하여 이를 찾을 수 있습니다.


### Azure 리소스 탐색기(미리 보기) 사용

[Azure 리소스 탐색기(미리 보기)](https://azure.microsoft.com/blog/azure-resource-explorer-a-new-tool-to-discover-the-azure-api/)는 리소스 관리자 배포 모델에서 만들어진 Azure 리소스를 관리하기 위해 개발된 새 도구입니다. 이 도구를 사용하면 쉽게

- Azure 리소스 관리 API를 검색하고
- API 설명서를 가져오고
- Azure 구독에서 직접 API를 호출할 수 있습니다.

이 강력한 도구로 수행할 수 있는 모든 작업을 알아보려면 [David Ebbo와 함께 하는 Azure 리소스 관리자 탐색기](https://channel9.msdn.com/Shows/Azure-Friday/Azure-Resource-Manager-Explorer-with-David-Ebbo) 비디오를 보세요.

PowerShell 메서드의 대안으로 리소스 탐색기를 사용하여 가상 컴퓨터를 캡처할 수 있습니다.

1. [리소스 탐색기 웹 사이트](https://resources.azure.com/)를 열고 Azure 계정에 로그인합니다.

2. 도구의 맨 위 오른쪽에서 **읽기/쓰기**를 선택하여 _PUT_ 및 _POST_ 작업을 허용합니다. 이는 기본적으로 **읽기 전용**으로 설정되어 있기 때문에 기본적으로 _GET_ 작업만 수행할 수 있습니다.

	![리소스 탐색기 읽기/쓰기](./media/virtual-machines-windows-capture-image/ArmExplorerReadWrite.png)

3. 그런 다음 Windows 가상 컴퓨터를 찾습니다. 도구의 맨 위에 있는 _검색 상자_에 이름을 입력하거나 왼쪽의 메뉴에서 **구독** > Azure 구독 > **resourceGroups** > 리소스 그룹 > **공급자** > **Microsoft.Compute** > **virtualMachines** > 사용 중인 Windows 가상 컴퓨터를 통해 이동할 수 있습니다. 왼쪽 탐색의 가상 컴퓨터를 클릭하면 도구의 오른쪽에 해당 템플릿이 표시됩니다.

4. 템플릿 페이지의 맨 위 오른쪽에 이 가상 컴퓨터에 사용할 수 있는 다양한 작업에 대한 탭이 표시됩니다. **동작(POST/DELETE)**에 대한 탭을 클릭합니다.

	![리소스 탐색기 작업 메뉴](./media/virtual-machines-windows-capture-image/ArmExplorerActionMenu.png)

5. 가상 컴퓨터에서 수행할 수 있는 모든 동작 목록이 표시됩니다.

	![리소스 탐색기 작업 항목](./media/virtual-machines-windows-capture-image/ArmExplorerActionItems.png)

6. **할당 취소**에 대한 실행 단추를 클릭하여 가상 컴퓨터의 할당을 취소합니다. VM의 상태가 **중지됨**에서 **중지됨(할당 취소됨)**으로 변경됩니다.

7. **일반화**를 위한 실행 단추를 클릭하여 가상 컴퓨터를 일반화된 것으로 표시합니다. 왼쪽의 가상 컴퓨터 이름에서 **InstanceView** 메뉴를 클릭하고 오른쪽의 **상태** 섹션으로 이동하여 상태 변경 내용을 확인할 수 있습니다.

8. **캡처** 실행 단추 아래에서 이미지를 캡처하기 위한 값을 설정할 수 있습니다. 채워진 값은 다음과 같이 보일 수 있습니다.

	![리소스 탐색기 캡처](./media/virtual-machines-windows-capture-image/ArmExplorerCaptureAction.png)

	**캡처** 실행 단추를 클릭하여 가상 컴퓨터 이미지를 캡처합니다. 그러면 리소스 탐색기 또는 [Azure 포털](https://portal.azure.com)을 통해 현재 액세스할 수 없는 JSON 템플릿 파일뿐만 아니라 이미지의 새 VHD가 만들어집니다.

9. 템플릿뿐만 아니라 새 이미지 VHD에 액세스하려면 저장소 리소스를 관리하기 위한 Azure 도구인 [Azure 저장소 탐색기](http://storageexplorer.com/)를 다운로드하고 설치합니다. 컴퓨터에 로컬로 Azure 저장소 탐색기를 설치합니다.

	- 저장소 탐색기를 열고 Azure 구독에 로그인합니다. 구독에 사용할 수 있는 모든 저장소 계정이 표시됩니다.

	- 위의 단계에서 캡처한 가상 컴퓨터의 저장소 계정이 왼쪽에 표시됩니다. 그 아래의 **시스템** 메뉴를 두 번 클릭합니다. 오른쪽에 **시스템** 폴더의 내용이 표시됩니다.

		![저장소 탐색기 시스템](./media/virtual-machines-windows-capture-image/StorageExplorer1.png)

	- **Microsoft.Compute**와 모든 이미지 폴더를 보여 주는 **이미지**를 차례로 두 번 클릭합니다. 리소스 탐색기에서 이미지를 캡처하는 동안 **destinationContainerName** 변수를 위해 입력한 폴더 이름을 두 번 클릭합니다. VHD와 JSON 템플릿 파일이 모두 표시됩니다.

	- 여기에서 URL을 찾거나 VHD/템플릿을 마우스 오른쪽 단추로 클릭하여 다운로드할 수 있습니다.

		![저장소 탐색기 템플릿](./media/virtual-machines-windows-capture-image/StorageExplorer2.png)


## 캡처한 이미지에서 새 VM 만들기

이제 캡처된 이미지를 사용하여 새 Windows VM을 만들 수 있습니다. 이 단계는 위의 단계에서 캡처된 Azure PowerShell 및 VM 이미지를 사용하여 새 가상 네트워크에서 VM을 만드는 방법을 보여 줍니다.

>[AZURE.NOTE] VM 이미지는 실제로 만들어지는 가상 컴퓨터와 동일한 저장소 계정에 있어야 합니다.

### 네트워크 리소스 만들기

다음 샘플 PowerShell 스크립트를 사용하여 가상 네트워크와 새 VM에 대한 NIC를 설정합니다. **$** 기호로 표시되는 변수에 대한 값을 응용 프로그램에 적절하게 사용합니다.

	$pip = New-AzureRmPublicIpAddress -Name $pipName -ResourceGroupName $rgName -Location $location -AllocationMethod Dynamic

	$subnetconfig = New-AzureRmVirtualNetworkSubnetConfig -Name $subnet1Name -AddressPrefix $vnetSubnetAddressPrefix

	$vnet = New-AzureRmVirtualNetwork -Name $vnetName -ResourceGroupName $rgName -Location $location -AddressPrefix $vnetAddressPrefix -Subnet $subnetconfig

	$nic = New-AzureRmNetworkInterface -Name $nicname -ResourceGroupName $rgName -Location $location -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id

### 새 VM 만들기

다음 PowerShell 스크립트는 가상 컴퓨터 구성을 설정하고 캡처된 VM 이미지를 새 설치에 대한 원본으로 사용하는 방법을 보여 줍니다. </br>

	#Enter a new username and password in the pop-up for the following
	$cred = Get-Credential

	#Get the storage account where the captured image is stored
	$storageAcc = Get-AzureRmStorageAccount -ResourceGroupName $rgName -AccountName $storageAccName

	#Set the VM name and size
	$vmConfig = New-AzureRmVMConfig -VMName $vmName -VMSize "Standard_A2"

	#Set the Windows operating system configuration and add the NIC
	$vm = Set-AzureRmVMOperatingSystem -VM $vmConfig -Windows -ComputerName $computerName -Credential $cred -ProvisionVMAgent -EnableAutoUpdate

	$vm = Add-AzureRmVMNetworkInterface -VM $vm -Id $nic.Id

	#Create the OS disk URI
	$osDiskUri = '{0}vhds/{1}{2}.vhd' -f $storageAcc.PrimaryEndpoints.Blob.ToString(), $vmName.ToLower(), $osDiskName

	#Configure the OS disk to be created from image (-CreateOption fromImage) and give the URL of the captured image VHD for the -SourceImageUri parameter.
	#We found this URL in the local JSON template in the previous sections.
	$vm = Set-AzureRmVMOSDisk -VM $vm -Name $osDiskName -VhdUri $osDiskUri -CreateOption fromImage -SourceImageUri $urlOfCapturedImageVhd -Windows

	#Create the new VM
	New-AzureRmVM -ResourceGroupName $rgName -Location $location -VM $vm

**찾아보기** > **가상 컴퓨터** 아래의 [Azure 포털](https://portal.azure.com)에 새로 만든 VM이 표시되어야 합니다. 또는 다음 PowerShell 명령을 사용합니다.

	$vmList = Get-AzureRmVM -ResourceGroupName $rgName
	$vmList.Name


## 다음 단계

Azure PowerShell을 사용하여 새 가상 컴퓨터를 관리하려면 [Azure Resource Manager 및 PowerShell을 사용하여 가상 컴퓨터 관리](virtual-machines-windows-ps-manage.md)를 참조하세요.

<!---HONumber=AcomDC_0323_2016-->