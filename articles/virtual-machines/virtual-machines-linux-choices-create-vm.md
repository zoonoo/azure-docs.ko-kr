<properties
	pageTitle="Linux VM을 만드는 다양한 방법 | Microsoft Azure"
	description="Azure에 Linux 가상 컴퓨터를 만드는 다른 방법을 나열하고 추가 지침에 대한 링크를 제공합니다."
	services="virtual-machines"
	documentationCenter=""
	authors="dsk-2015"
	manager="timlt"
	editor=""
	tags="azure-service-management,azure-resource-manager"/>

<tags
	ms.service="virtual-machines"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="vm-linux"
	ms.workload="infrastructure-services"
	ms.date="01/20/2016"
	ms.author="dkshir"/>

# Linux 가상 컴퓨터를 만드는 다양한 방법

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

Azure는 다른 사용자와 목적에 맞게 다른 방법으로 VM을 만들 수 있습니다. 이 문서에는 이러한 차이점 및 Linux 가상 컴퓨터를 만들 때 선택할 수 있는 옵션이 요약되어 있습니다.

또한 Azure 리소스 관리자 템플릿은 하나의 논리적 배포 단위로 다른 리소스 및 가상 컴퓨터를 만들고 관리하는 데 사용될 수 있습니다. 적용 가능한 경우 이 방법에 대한 지침이 포함됩니다. Azure 리소스 관리자 및 리소스를 하나의 단위로 관리하는 방법에 대해 자세히 알려면 [개요][]를 참조하세요.

## 도구 선택

### GUI: Azure 클래식 포털 또는 Azure 포털

포털의 그래픽 사용자 인터페이스는 특히 Azure로 시작한 경우 쉽게 가상 컴퓨터를 사용해 볼 수 있는 방법입니다. [Azure 클래식 포털](https://manage.windowsazure.com) 또는 [Azure 포털](https://portal.azure.com)을 사용하여 VM을 만듭니다. 일반적인 지침은 [사용자 지정 가상 컴퓨터 만들기][]를 참조하고 **갤러리**에서 Linux 이미지를 선택합니다. [Azure 클래식 포털](https://manage.windowsazure.com)에서는 클래식 배포 모델만 사용하여 가상 컴퓨터를 만듭니다.

### 명령 셸: Azure CLI 또는 Azure PowerShell

명령 셸에서 작업하려는 경우 Mac, Linux 및 Windows 사용자의 경우 Azure CLI(명령줄 인터페이스)를 선택하거나 Azure용 Windows PowerShell cmdlet 및 사용자 지정 콘솔이 포함된 Azure PowerShell을 선택합니다.

Azure CLI를 사용하여 리소스 관리자 배포 모델에서 가상 컴퓨터를 만들려면 [Linux를 실행하는 가상 컴퓨터 만들기][]를 참조하세요. Azure PowerShell 및 템플릿 사용 지침을 읽으려면 이 문서의 탭 또는 문서 선택기를 따릅니다.

클래식 배포 모델은 [Azure CLI를 사용하여 사용자 지정 Linux 가상 컴퓨터 만들기](virtual-machines-linux-create-custom.md) 및 [Azure PowerShell을 사용하여 Linux 기반 가상 컴퓨터 만들기 및 미리 구성][]을 참조하세요.


### 개발 환경: Visual Studio

Visual Studio는 Azure 가상 컴퓨터 만들기도 지원합니다. 클래식 배포 모델은 [Visual Studio에서 웹 사이트용 가상 컴퓨터 만들기][]를 참조하세요. 리소스 관리자 배포 모델은 [계산, 네트워크 및 저장소 .NET 라이브러리를 사용하여 Azure 리소스 배포][]를 참조하세요.


## 운영 체제 및 이미지 선택

실행하려는 운영 체제에 따라 이미지를 선택합니다. Azure 및 해당 파트너는 응용 프로그램 및 도구를 포함하는 여러 이미지를 제공합니다. 또는 사용자 고유의 이미지 중 하나를 사용합니다.


### Azure 이미지

위의 모든 문서에서 쉽게 기존 Azure 이미지를 사용하여 가상 컴퓨터를 만들고 네트워킹, 부하 분산 등에 대해 사용자 지정할 수 있습니다. 포털에서 Azure 제공 이미지에 대한 갤러리 또는 이미지 목록을 제공합니다. 명령줄을 사용하여 유사한 목록을 가져올 수 있습니다. 예를 들어 Azure CLI에서 `azure vm image list`를 실행하여 사용 가능한 모든 이미지 목록을 위치 및 게시자별로 가져옵니다.


### 사용자 고유의 이미지 사용

해당 VM을 *캡처*하여 기존 Azure 가상 컴퓨터를 기반으로 하는 이미지를 사용하거나 VHD(가상 하드 디스크)에 저장된 자체 이미지를 업로드합니다. 클래식 배포 모델은 [Linux 기반 가상 컴퓨터를 캡처하여 CLI의 템플릿으로 사용하는 방법][] 및 [Linux 운영 체제가 포함된 가상 하드 디스크 만들기 및 업로드][]를 참조하세요. 리소스 관리자 배포 모델은 [Linux 가상 컴퓨터를 캡처하여 리소스 관리자 템플릿으로 사용하는 방법](virtual-machines-linux-capture-image-resource-manager.md)을 참조하세요.

## 다음 단계

[가상 컴퓨터에 로그온][]

[데이터 디스크 연결][]

## 추가 리소스

[기본 구성 테스트 환경][]

[Azure 하이브리드 클라우드 테스트 환경][]

[Mac, Linux 및 Windows용 Azure CLI를 사용한 VM 작업에 해당하는 리소스 관리자 및 서비스 관리 명령][]

<!-- LINKS -->
[개요]: ../resource-group-overview.md

[Create a Virtual Machine Running Windows]: virtual-machines-windows-tutorial.md
[Create a Virtual Machine Running Linux]: virtual-machines-linux-tutorial.md

[Mac, Linux 및 Windows용 Azure CLI를 사용한 VM 작업에 해당하는 리소스 관리자 및 서비스 관리 명령]: xplat-cli-azure-manage-vm-asm-arm.md
[Deploy and Manage Virtual Machines using Azure Resource Manager Templates and the Azure CLI]: virtual-machines-deploy-rmtemplates-azure-cli.md
[Deploy and Manage Virtual Machines using Azure Resource Manager Templates and PowerShell]: virtual-machines-deploy-rmtemplates-powershell.md
[Azure PowerShell을 사용하여 Linux 기반 가상 컴퓨터 만들기 및 미리 구성]: virtual-machines-ps-create-preconfigure-linux-vms.md

[How to Create a Custom Virtual Machine Running Linux in Azure]: virtual-machines-linux-create-custom.md
[Linux 기반 가상 컴퓨터를 캡처하여 CLI의 템플릿으로 사용하는 방법]: virtual-machines-linux-capture-image.md

[Linux 운영 체제가 포함된 가상 하드 디스크 만들기 및 업로드]: virtual-machines-linux-create-upload-vhd.md

[Visual Studio에서 웹 사이트용 가상 컴퓨터 만들기]: virtual-machines-dotnet-create-visual-studio-powershell.md
[계산, 네트워크 및 저장소 .NET 라이브러리를 사용하여 Azure 리소스 배포]: virtual-machines-arm-deployment.md

[가상 컴퓨터에 로그온]: virtual-machines-linux-how-to-log-on.md

[데이터 디스크 연결]: virtual-machines-linux-how-to-attach-disk.md

[기본 구성 테스트 환경]: virtual-machines-base-configuration-test-environment.md
[Azure 하이브리드 클라우드 테스트 환경]: virtual-machines-hybrid-cloud-test-environments.md

[Linux를 실행하는 가상 컴퓨터 만들기]: virtual-machines-linux-tutorial.md
[사용자 지정 가상 컴퓨터 만들기]: virtual-machines-create-custom.md

<!---HONumber=AcomDC_0128_2016-->