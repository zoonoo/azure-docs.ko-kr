<properties
	pageTitle="Linux VM 복사본 만들기 | Microsoft Azure"
	description="*특수 이미지*를 만들어 Resource Manager 배포 모델에서 Linux를 실행하여 Azure 가상 컴퓨터 복사본을 만드는 방법에 대해 알아보세요."
	services="virtual-machines-linux"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-linux"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/26/2016"
	ms.author="cynthn"/>

# Azure Resource Manager 배포 모델에서 Linux 가상 컴퓨터의 복사본 만들기


이 문서에서는 Linux를 실행하는 Azure VM(가상 컴퓨터)의 복사본을 만드는 방법을 보여 줍니다. 특히, Azure CLI 및 Azure 포털을 사용하여 리소스 관리자 배포 모델에서 이 작업을 수행하는 방법을 설명합니다. 또한 사용자 계정과 원본 VM의 기타 상태 데이터를 유지하는 Azure VM의 *특수* 이미지를 만드는 방법을 보여 줍니다. 특수 이미지는 Linux VM을 클래식 배포 모델에서 리소스 관리자 배포 모델로 이식하거나 리소스 관리자 배포 모델에서 만든 Linux VM의 백업 복사본을 만드는 경우에 유용합니다. 이러한 방식으로 운영 체제와 데이터 디스크를 복사한 다음 네트워크 리소스를 설정하여 새 가상 컴퓨터를 만들 수 있습니다.

유사한 Linux VM의 대량 배포를 만들려는 경우 *일반화된* 이미지를 사용해야 합니다. 이 경우 [Linux 가상 컴퓨터를 캡처하는 방법](virtual-machines-linux-capture-image.md)을 참조하세요.



## 시작하기 전에

다음 단계를 시작하기 전에 다음 필수 조건을 충족하는지 확인합니다.

- Linux를 실행하며 클래식 또는 리소스 관리자 배포 모델을 사용하여 만든 Azure 가상 컴퓨터가 있습니다. 운영 체제를 구성하고, 데이터 디스크를 연결하고, 필수 응용 프로그램 설치와 같은 기타 사용자 지정을 수행했습니다. 이 VM을 사용하여 복사본을 만듭니다. 원본 VM을 만드는 데 도움이 필요한 경우 [Azure에서 Linux VM 만들기](virtual-machines-linux-quick-create-cli.md)를 참조하세요.

- 컴퓨터에 Azure CLI를 다운로드 및 설치하고 Azure 구독에 로그인했습니다. 자세한 내용은 [Azure CLI 설치 방법](../xplat-cli-install.md)을 참조하세요.

- 리소스 그룹과 저장소 계정, 해당 리소스 그룹에서 VHD를 복사하기 위해 만든 Blob 컨테이너가 있습니다. 저장소 계정과 Blob 컨테이너를 만드는 방법은 [Azure 저장소와 함께 Azure CLI 사용](../storage/storage-azure-cli.md)을 참조하세요.

> [AZURE.NOTE] 원본 이미지로 두 가지 배포 모델 중 하나를 사용하여 만든 VM에도 유사한 단계가 적용됩니다. 사소한 차이는 해당 부분에서 명시합니다.


## VHD를 Resource Manager 저장소 계정에 복사


1. 가장 먼저 다음 중 하나를 수행하여 원본 VM에서 사용하는 VHD의 공간을 확보합니다.

	- 원본 가상 컴퓨터를 복사하려는 경우 해당 가상 컴퓨터를 중지 및 할당 취소합니다. 포털에서 **찾아보기** > **가상 컴퓨터** 또는 **가상 컴퓨터(클래식)** > *사용자 VM* > **중지**를 클릭합니다. 리소스 관리자 배포 모델에서 만든 VM의 경우 Azure CLI 명령 `azure vm stop <yourResourceGroup> <yourVmName>` 다음에 `azure vm deallocate <yourResourceGroup> <yourVmName>`를 사용할 수도 있습니다. 포털에서 VM의 상태가 **실행 중**에서 **중지됨(할당 취소됨)**으로 바뀝니다.

	- 원본 가상 컴퓨터를 마이그레이션하려는 경우에는 해당 VM을 삭제하고 남은 VHD를 사용합니다. [포털](https://portal.azure.com)에서 가상 컴퓨터로 이동한 다음 **삭제**를 클릭합니다.

1. 원본 VHD가 포함된 저장소 계정의 액세스 키를 찾습니다. 액세스 키에 대한 자세한 내용은 [Azure 저장소 계정 방법](../storage/storage-create-storage-account.md)을 참조하세요.

	- 원본 VM을 클래식 배포 모델을 사용하여 만든 경우 **찾아보기** > **저장소 계정(클래식)** > *사용자 저장소 계정* > **모든 설정** > **키**를 클릭합니다. **기본 액세스 키**로 레이블이 지정된 키를 복사합니다. Azure CLI에서는 `azure config mode asm`을 사용하여 클래식 모드로 변경한 다음 `azure storage account keys list <yourSourceStorageAccountName>`을 사용합니다.

	- 원본 VM을 리소스 관리자 배포 모델을 사용하여 만든 경우 **찾아보기** > **저장소 계정** > *사용자 저장소 계정* > **모든 설정** > **액세스 키**를 클릭합니다. **key1**로 레이블이 지정된 텍스트를 복사합니다. Azure CLI인 경우에는 `azure config mode arm` 및 `azure storage account keys list -g <yourDestinationResourceGroup> <yourDestinationStorageAccount>`를 차례로 사용하여 리소스 관리자 모드로 전환하세요.

1. 다음 단계의 설명에 따라 [저장소의 Azure CLI 명령](../storage/storage-azure-cli.md)을 사용하여 VHD 파일을 복사합니다. 또는 사용자 인터페이스를 선호할 경우에는 대신 [Microsoft Azure 저장소 탐색기](http://storageexplorer.com/)를 사용할 수 있습니다. </br>
	1. 대상 저장소 계정에 대해 연결 문자열을 설정합니다. 이 연결 문자열에는 이 저장소 계정에 대한 액세스 키만 포함됩니다.

			$azure storage account connectionstring show -g <yourDestinationResourceGroup> <yourDestinationStorageAccount>
			$export AZURE_STORAGE_CONNECTION_STRING=<the_connectionstring_output_from_above_command>

	2. 원본 저장소 계정의 VHD 파일에 대해 [공유 액세스 서명](../storage/storage-dotnet-shared-access-signature-part-1.md)을 만듭니다. 다음 명령의 **공유 액세스 URL** 출력을 기록해 둡니다.

			$azure storage blob sas create  --account-name <yourSourceStorageAccountName> --account-key <SourceStorageAccessKey> --container <SourceStorageContainerName> --blob <FileNameOfTheVHDtoCopy> --permissions "r" --expiry <mm/dd/yyyy_when_you_want_theSAS_to_expire>

	3. 다음 명령을 사용하여 원본 저장소에서 대상으로 VHD를 복사합니다.

			$azure storage blob copy start <SharedAccessURL_ofTheSourceVHD> <DestinationContainerName>

	4. VHD 파일이 비동기적으로 복사됩니다. 다음 명령을 사용하여 진행률을 확인합니다.

			$azure storage blob copy show <DestinationContainerName> <FileNameOfTheVHDtoCopy>

</br>

>[AZURE.NOTE] 앞에서 설명한 대로 운영 체제 및 데이터 디스크를 별도로 복사해야 합니다.


## 복사된 VHD를 사용하여 VM 만들기

이전 단계에서 복사한 VHD를 사용하여 이제 새 가상 네트워크에 리소스 관리자 기반 Linux VM을 만드는 데 Azure CLI를 사용할 수 있습니다. VHD는 생성될 새 가상 컴퓨터와 동일한 저장소 계정에 있어야 합니다.


다음 스크립트와 유사한 방식으로 새 VM에 대한 가상 네트워크와 NIC를 설정합니다. 응용 프로그램에 적절한 변수 값을 사용합니다.

	$azure network vnet create <yourResourceGroup> <yourVnetName> -l <yourLocation>

	$azure network vnet subnet create <yourResourceGroup> <yourVnetName> <yourSubnetName>

	$azure network public-ip create <yourResourceGroup> <yourIpName> -l <yourLocation>

	$azure network nic create <yourResourceGroup> <yourNicName> -k <yourSubnetName> -m <yourVnetName> -p <yourIpName> -l <yourLocation>


다음 명령과 복사된 VHD를 사용하여 새 가상 컴퓨터를 만듭니다. </br>

	$azure vm create -g <yourResourceGroup> -n <yourVmName> -f <yourNicName> -d <UriOfYourOsDisk> -x <UriOfYourDataDisk> -e <DataDiskSizeGB> -Y -l <yourLocation> -y Linux -z "Standard_A1" -o <DestinationStorageAccountName> -R <DestinationStorageAccountBlobContainer>


데이터 및 운영 체제 디스크 URL은 다음과 유사하게 나타납니다. `https://StorageAccountName.blob.core.windows.net/BlobContainerName/DiskName.vhd` 포털에서 저장소 컨테이너로 이동하고 운영 체제 또는 복사된 데이터 VHD를 클릭한 다음 URL의 내용을 복사하여 이를 찾을 수 있습니다.


이 명령에 성공할 경우 다음과 유사한 출력이 표시됩니다.

	$azure vm create -g "testcopyRG" -n "redhatcopy" -f "LinCopyNic" -d https://testcopystore.blob.core.windows.net/testcopyblob/RedHat201631816334.vhd -x https://testcopystore.blob.core.windows.net/testcopyblob/RedHat-data.vhd -e 10 -Y -l "West US" -y Linux -z "Standard_A1" -o "testcopystore" -R "testcopyblob"
	info:    Executing command vm create
	+ Looking up the VM "redhatcopy"
	info:    Using the VM Size "Standard_A1"
	+ Looking up the NIC "LinCopyNic"
	info:    Found an existing NIC "LinCopyNic"
	info:    Found an IP configuration with virtual network subnet id "/subscriptions/b8e6a92b-d6b7-4dbb-87a8-3c8dfe248156/resourceGroups/testcopyRG/providers/Microsoft.Network/virtualNetworks/LinCopyVnet/subnets/LinCopySub" in the NIC "LinCopyNic"
	info:    This NIC IP configuration has a public ip already configured "/subscriptions/b8e6a92b-d6b7-4dbb-87a8-3c8dfe248156/resourcegroups/testcopyrg/providers/microsoft.network/publicipaddresses/lincopyip", any public ip parameters if provided, will be ignored.
	+ Looking up the storage account testcopystore
	info:    The storage URI 'https://testcopystore.blob.core.windows.net/' will be used for boot diagnostics settings, and it can be overwritten by the parameter input of '--boot-diagnostics-storage-uri'.
	+ Creating VM "redhatcopy"
	info:    vm create command OK

새로 만든 VM은 [Azure 포털](https://portal.azure.com)에서 **찾아보기** > **가상 컴퓨터**에 표시됩니다.

원하는 SSH 클라이언트를 사용하여 새 가상 컴퓨터에 연결하고 원본 가상 컴퓨터의 계정 자격 증명을 사용합니다(예: `ssh OldAdminUser@<IPaddressOfYourNewVM>`). 자세한 내용은 [Azure에서 Linux와 함께 SSH를 사용하는 방법](virtual-machines-linux-ssh-from-linux.md)을 참조하세요.


## 다음 단계

Azure CLI를 사용하여 새 가상 컴퓨터를 관리하는 방법을 알아보려면 [Azure Resource Manager의 Azure CLI 명령](azure-cli-arm-commands.md)을 참조하세요.

<!---HONumber=AcomDC_0525_2016-->