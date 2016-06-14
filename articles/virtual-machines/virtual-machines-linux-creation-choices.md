<properties
	pageTitle="Linux VM을 만드는 다양한 방법 | Microsoft Azure"
	description="Azure에 Linux 가상 컴퓨터를 만드는 다른 방법을 나열하고 추가 지침에 대한 링크를 제공합니다."
	services="virtual-machines-linux"
	documentationCenter=""
	authors="iainfoulds"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-linux"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.tgt_pltfrm="vm-linux"
	ms.workload="infrastructure-services"
	ms.date="04/12/2016"
	ms.author="iainfou"/>

# 리소스 관리자로 Linux 가상 컴퓨터를 만드는 다양한 방법

Azure는 다른 사용자와 목적에 맞게 Resource Manager 배포 모델을 사용하여 다른 방법으로 VM을 만들 수 있습니다. 이 문서에는 이러한 차이점 및 Linux 가상 컴퓨터(VM)를 만들 때 선택할 수 있는 옵션이 요약되어 있습니다.

## 도구 선택

### 명령 셸: Azure CLI 

CLI에서 Azure 명령줄 인터페이스를 사용합니다. npm, Docker 컨테이너 또는 설치 스크립트를 통해 [Azure CLI를 설치하는 방법](../xplat-cli-install.md)에 대해 자세히 확인할 수 있습니다. 다음 자습서에서는 Azure CLI를 사용하는 방법에 대한 예제를 제공합니다.

* [개발 및 테스트를 위해 Azure CLI에서 Linux VM 만들기](virtual-machines-linux-quick-create-cli.md) 

* [Azure 템플릿을 사용하여 안전한 Linux VM 만들기](virtual-machines-linux-create-ssh-secured-vm-from-template.md)

* [Azure CLI를 사용하여 Linux VM을 처음부터 새로 만들기](virtual-machines-linux-create-cli-complete.md)

### GUI: Azure 포털

[Azure 포털](https://portal.azure.com)의 그래픽 사용자 인터페이스는 특히 Azure로 시작한 경우 사용자의 시스템에 설치할 수 있는 것이 없기 때문에 쉽게 VM을 사용해 볼 수 있는 방법입니다. Azure 포털을 사용하여 다음과 같이 VM을 만듭니다.

* [Azure 포털을 사용하여 Linux를 실행하는 가상 컴퓨터 만들기](virtual-machines-linux-quick-create-portal.md) 

## 운영 체제 및 이미지 선택

두 가지 방법 모두, 실행하려는 운영 체제에 따라 이미지를 선택합니다. Azure 및 해당 파트너는 미리 설치된 응용 프로그램 및 도구를 포함하는 여러 이미지를 제공합니다. 또는 사용자 고유의 이미지 중 하나를 업로드할 수 있습니다.

### Azure 이미지

위의 모든 문서에서 쉽게 기존 Azure 이미지를 사용하여 VM을 만들고 네트워킹, 부하 분산 등에 대해 사용자 지정할 수 있습니다. 포털에서는 이미지를 제공한 Azure에 Azure 마켓플레이스를 제공합니다. 명령줄을 사용하여 유사한 목록을 가져올 수 있습니다. 예를 들어 Azure CLI에서 `azure vm image list`를 실행하여 사용 가능한 모든 이미지 목록을 위치 및 게시자별로 가져옵니다. 검색 및 사용 가능한 이미지를 사용하는 예제는 [Azure CLI를 사용하여 Azure 가상 컴퓨터 이미지 탐색 및 선택](virtual-machines-linux-cli-ps-findimage.md)을 참조하세요.

### 사용자 고유의 이미지 사용

특정 사용자 지정이 필요한 경우 해당 VM을 *캡처*하여 기존 Azure VM을 기반으로 하는 이미지를 사용하거나 VHD(가상 하드 디스크)에 저장된 자체 이미지를 업로드할 수 있습니다. 지원되는 배포판 및 사용자 고유의 이미지를 사용하는 방법에 대한 자세한 내용은 다음 문서를 참조하세요.

* [Azure 인증 배포](virtual-machines-linux-endorsed-distros.md)

* [보증되지 않는 배포에 대한 정보](virtual-machines-linux-create-upload-generic.md)

* [Linux 가상 컴퓨터를 Resource Manager 템플릿으로 캡처하는 방법](virtual-machines-linux-capture-image.md)

## 다음 단계

* 이 자습서 중 하나를 사용하여 [CLI](virtual-machines-linux-quick-create-cli.md)로 [포털](virtual-machines-linux-quick-create-portal.md)에서 Linux VM을 만들거나 Azure Resource Manager [템플릿](virtual-machines-linux-cli-deploy-templates.md)을 사용하여 Linux VM을 만듭니다.

* Linux VM을 만든 후에 쉽게 [데이터 디스크를 추가](virtual-machines-linux-add-disk.md)할 수 있습니다.

* [암호 또는 SSH 키 다시 설정 및 사용자 관리](virtual-machines-linux-using-vmaccess-extension.md)하는 빠른 단계

<!---HONumber=AcomDC_0608_2016-->