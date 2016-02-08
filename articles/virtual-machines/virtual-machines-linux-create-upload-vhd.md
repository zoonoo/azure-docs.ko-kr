<properties
	pageTitle="Linux VHD 만들기 및 업로드 | Microsoft Azure"
	description="클래식 배포 모델을 사용하여 Linux 운영 체제가 포함된 Azure VHD(가상 하드 디스크)를 만들고 업로드합니다."
	services="virtual-machines"
	documentationCenter=""
	authors="dsk-2015"
	manager="timlt"
	editor="tysonn"
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/22/2016"
	ms.author="dkshir"/>

# Linux 운영 체제가 포함된 가상 하드 디스크 만들기 및 업로드

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]리소스 관리자 모델.


이 문서에서는 VHD(가상 하드 디스크)를 생성 및 업로드하고 이를 Azure에서 가상 컴퓨터를 만들기 위한 고유한 이미지로 사용하는 방법을 소개합니다. 또한 이 이미지를 기반으로 여러 개의 가상 컴퓨터를 만들 수 있도록 운영 체제를 준비하는 방법을 살펴봅니다.

[AZURE.INCLUDE [free-trial-note](../../includes/free-trial-note.md)]

Azure 가상 컴퓨터는 만드는 동안 선택한 이미지에 따라 운영 체제를 실행합니다. 이러한 이미지는 VHD 형식인 .vhd 파일로 저장소 계정에 저장됩니다. 자세한 내용은 [Azure의 디스크](virtual-machines-disks-vhds.md) 및 [Azure의 이미지](virtual-machines-images.md)를 참조하세요.

가상 컴퓨터를 만드는 경우 실행하려는 응용 프로그램에 적합하도록 일부 운영 체제 설정을 사용자 지정할 수 있습니다. 자세한 내용은 [사용자 지정 가상 컴퓨터를 만드는 방법](virtual-machines-create-custom.md)(영문)을 참조하십시오.

**중요**: [Azure 인증 배포의 Linux](virtual-machines-../linux-endorsed-distributions.md)의 '지원되는 버전'에 지정된 대로 보증 배포판 중 하나가 구성 세부 정보와 함께 사용되는 경우에만 Linux OS를 실행하는 가상 컴퓨터에 Azure 플랫폼 SLA가 적용됩니다. Azure 이미지 갤러리의 모든 Linux 배포는 필요한 구성이 포함된 보증 배포판입니다.


## 필수 조건
이 문서에서는 사용자에게 다음 항목이 있다고 가정합니다.

- **관리 인증서** - VHD를 업로드할 구독에 필요한 관리 인증서를 만들어 .cer 파일로 내보냈습니다. 인증서 만들기에 대한 자세한 내용은 [Azure용 인증서 개요](../cloud-services/cloud-services-certs-create.md)를 참조하세요.

- **.vhd 파일에 설치된 Linux 운영 체제** - 가상 하드 디스크에 지원되는 Linux 운영 체제를 설치했습니다. 다양한 도구를 사용하여 .vhd 파일을 만들 수 있습니다. 예를 들어 Hyper-V와 같은 가상화 솔루션을 사용하여 .vhd 파일을 만들고 운영 체제를 설치할 수 있습니다. 자세한 내용은 [Hyper-V 역할 설치 및 가상 시스템 구성](http://technet.microsoft.com/library/hh846766.aspx)을 참조하십시오.

	**중요**: 새 VHDX 형식은 Azure에서 지원되지 않습니다. Hyper-V 관리자 또는 convert-vhd cmdlet을 사용하여 디스크를 VHD 형식으로 변환할 수 있습니다.

	보증된 분산 목록은 [Azure의 Linux-보증된 분산](../linux-endorsed-distributions.md)(영문)을 참조하십시오. Linux 배포판 일반 목록은 [보증되지 않는 배포에 대한 정보](virtual-machines-linux-create-upload-vhd-generic.md)를 참조하세요.

- **Azure 명령줄 인터페이스** - Linux 운영 체제를 사용하여 이미지를 만드는 경우에는 [Azure 명령줄 인터페이스](../virtual-machines-command-line-tools.md)를 사용하여 VHD를 업로드합니다.

- **Azure Powershell 도구** - `Add-AzureVhd` cmdlet을 사용하여 VHD를 업로드할 수도 있습니다. Azure PowerShell cmdlet을 다운로드하려면 [Azure 다운로드](https://azure.microsoft.com/downloads/)를 방문하세요. 참조 정보는 [Add-AzureVhd](https://msdn.microsoft.com/library/azure/dn495173.aspx)(영문)를 참조하십시오.

<a id="prepimage"> </a>
## 1단계: 업로드할 이미지 준비

Azure에서는 다양한 Linux 배포를 지원합니다([보증 배포판](../linux-endorsed-distributions.md) 참조). 다음 문서에서는 Azure에서 지원되는 다양한 Linux 배포를 준비하는 방법을 안내합니다.

- **[CentOS 기반 배포판](virtual-machines-linux-create-upload-vhd-centos.md)**
- **[Debian Linux](virtual-machines-linux-create-upload-vhd-debian.md)**
- **[Oracle Linux](virtual-machines-linux-create-upload-vhd-oracle.md)**
- **[Red Hat Enterprise Linux](virtual-machines-linux-create-upload-vhd-redhat.md)**
- **[SLES 및 openSUSE](../virtual-machines-linux-create-upload-vhd-suse)**
- **[Ubuntu](virtual-machines-linux-create-upload-vhd-ubuntu.md)**
- **[기타 - 보증되지 않는 배포](virtual-machines-linux-create-upload-vhd-generic.md)**

또한 Azure용 Linux 이미지를 준비하는 방법에 대한 추가 팁은 **[Linux 설치 참고 사항](virtual-machines-linux-create-upload-vhd-generic.md#linuxinstall)**을 참조하세요.

위 가이드의 단계를 수행하면 Azure에 업로드할 수 있는 VHD 파일을 만들 수 있습니다.

<a id="connect"> </a>
## 2단계: Azure 연결 준비

.vhd 파일을 업로드하려면 컴퓨터와 Azure의 구독 사이에 보안 연결을 설정해야 합니다.


### Azure CLI를 사용하는 경우

최신 Azure CLI는 리소스 관리자 배포 모델을 기본값으로 하므로 다음 명령을 통해 클래식 배포 모델에 있는지 확인합니다.

		azure config mode asm  

이제 다음 로그인 방법 중 하나를 사용하여 Azure 구독에 연결합니다.

Azure AD 메서드를 사용한 로그인:

1. Azure CLI 창 열기

2. 형식:

	`azure login`

	메시지가 표시되면 사용자 이름 및 암호를 입력합니다.

**또는** PublishSettings 파일을 대신 사용합니다.

1. Azure CLI 창 열기

2. 형식:

	`azure account download`

	이 명령은 브라우저 창을 열고 Azure 구독에 대한 정보와 인증서가 포함된 .publishsettings 파일을 자동으로 다운로드합니다.

3. .publishsettings 파일 저장

4. 형식:

	`azure account import <PathToFile>`

	여기서 `<PathToFile>`은 .publishsettings 파일의 전체 경로입니다.

	자세한 내용은 [Azure CLI에서 Azure에 연결](../xplat-cli-connect.md)을 참조하세요.


### Azure PowerShell을 사용하는 경우

Azure AD 메서드를 사용한 로그인:

1. Azure PowerShell 창을 엽니다.

2. 형식:

	`Add-AzureAccount`

	메시지가 표시되면 조직 사용자 ID와 암호를 입력합니다.

**또는** PublishSettings 파일을 대신 사용합니다.

1. Azure PowerShell 창을 엽니다.

2. 형식:

	`Get-AzurePublishSettingsFile`

	이 명령은 브라우저 창을 열고 Azure 구독에 대한 정보와 인증서가 포함된 .publishsettings 파일을 자동으로 다운로드합니다.

3. .publishsettings 파일을 저장합니다.

4. 형식:

	`Import-AzurePublishSettingsFile <PathToFile>`

	여기서 `<PathToFile>`은 .publishsettings 파일의 전체 경로입니다.

	자세한 내용은 [Azure PowerShell을 설치 및 구성하는 방법](powershell-install-configure.md)을 참조하세요.

> [AZURE.NOTE] Azure CLI 또는 Azure PowerShell에서 최신 Azure Active Directory 메서드를 사용하여 Azure 구독에 로그인하는 것이 좋습니다.

<a id="upload"> </a>
## 3단계: Azure에 이미지 업로드

VHD 파일을 업로드할 저장소 계정이 필요합니다. 기존 계정을 선택하거나 새로 만들 수 있습니다. 저장소 계정을 만들려면 [저장소 계정 만들기](../storage-create-storage-account.md)를 참조하세요.

.vhd 파일을 업로드하는 경우 Blob 저장소 내 임의의 위치에 .vhd 파일을 배치할 수 있습니다. 다음 명령 예제에서 **BlobStorageURL**은 사용하려는 저장소 계정의 URL이고, **YourImagesFolder**는 이미지를 저장할 Blob 저장소 내 컨테이너입니다. **VHDName**은 가상 하드 디스크를 식별하기 위해 [Azure 포털](http://portal.azure.com) 또는 [Azure 클래식 포털](http://manage.windowsazure.com)에 표시되는 레이블입니다. **PathToVHDFile**은 컴퓨터에 있는 .vhd 파일의 전체 경로 및 이름입니다.


### Azure CLI를 사용하는 경우

Azure CLI에서 다음 명령을 사용하여 이미지를 업로드합니다.

		azure vm image create <ImageName> --blob-url <BlobStorageURL>/<YourImagesFolder>/<VHDName> --os Linux <PathToVHDFile>

자세한 내용은 [Azure 서비스 관리를 위한 Azure CLI 참조](virtual-machines-command-line-tools.md)를 참조하세요.


### PowerShell을 사용하는 경우

이전 단계에서 사용한 Azure PowerShell 창에서 다음을 입력합니다.

		Add-AzureVhd -Destination <BlobStorageURL>/<YourImagesFolder>/<VHDName> -LocalFilePath <PathToVHDFile>

자세한 내용은 [Add-AzureVhd](https://msdn.microsoft.com/library/azure/dn495173.aspx)(영문)를 참조하십시오.


[Step 1: Prepare the image to be uploaded]: #prepimage
[Step 2: Prepare the connection to Azure]: #connect
[Step 3: Upload the image to Azure]: #upload

<!---HONumber=AcomDC_0128_2016-->