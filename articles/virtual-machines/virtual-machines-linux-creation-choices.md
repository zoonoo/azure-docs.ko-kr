<properties
	pageTitle="Linux VM을 만드는 다양한 방법 | Microsoft Azure"
	description="Azure에 Linux 가상 컴퓨터를 만드는 다양한 방법을 나열하고 각 방법에 대한 도구 및 자습서에 연결합니다."
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
	ms.date="07/06/2016"
	ms.author="iainfou"/>

# 리소스 관리자로 Linux 가상 컴퓨터를 만드는 다양한 방법

Azure는 다른 사용자와 목적에 맞게 Resource Manager 배포 모델을 사용하여 다른 방법으로 VM을 만들 수 있습니다. 이 문서에는 이러한 차이점 및 Linux 가상 컴퓨터(VM)를 만들 때 선택할 수 있는 옵션이 요약되어 있습니다.

## Azure CLI 

Azure CLI는 npm 패키지, 배포판 제공 패키지 또는 Docker 컨테이너를 통해 플랫폼에서 사용할 수 있습니다. [Azure CLI를 설치하고 구성하는 방법](../xplat-cli-install.md)에 대해 자세히 알아볼 수 있습니다. 다음 자습서에서는 Azure CLI를 사용하는 방법에 대한 예제를 제공합니다. 다음과 같이 표시되는 CLI 빠른 시작 명령에 대한 자세한 내용은 각 문서를 참고합니다.

* [개발 및 테스트를 위해 Azure CLI에서 Linux VM 만들기](virtual-machines-linux-quick-create-cli.md)

	```bash
	azure vm quick-create -M ~/.ssh/azure_id_rsa.pub -Q CoreOS
	```

* [Azure 템플릿을 사용하여 안전한 Linux VM 만들기](virtual-machines-linux-create-ssh-secured-vm-from-template.md)

	```bash
	azure group create --name TestRG --location WestUS 
		--template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json
	```

* [Azure CLI를 사용하여 Linux VM을 처음부터 새로 만들기](virtual-machines-linux-create-cli-complete.md)

* [Linux VM에 디스크 추가](virtual-machines-linux-add-disk.md)

	```bash
	azure vm disk attach-new --resource-group TestRG --vm-name TestVM <size-in-GB>
	```

## Azure 포털

[Azure 포털](https://portal.azure.com)의 그래픽 사용자 인터페이스는 특히 Azure로 시작한 경우 사용자의 시스템에 설치할 수 있는 것이 없기 때문에 쉽게 VM을 사용해 볼 수 있는 방법입니다. Azure 포털을 사용하여 다음과 같이 VM을 만듭니다.

* [Azure 포털을 사용하여 Linux VM 만들기](virtual-machines-linux-quick-create-portal.md)
* [Azure 포털을 사용하여 디스크 연결](virtual-machines-linux-attach-disk-portal.md)

## 운영 체제 및 이미지 선택
VM을 만드는 경우 실행하려는 운영 체제에 따라 이미지를 선택합니다. Azure 및 해당 파트너는 미리 설치된 응용 프로그램 및 도구를 포함하는 여러 이미지를 제공합니다. 또는 사용자 고유의 이미지 중 하나를 업로드할 수 있습니다(아래 참조).

### Azure 이미지
`azure vm image` CLI 명령을 사용하여 게시자, 배포판 릴리스 및 빌드에 따라 사용 가능한 기능을 확인할 수 있습니다.

사용 가능한 게시자 나열:

```bash
azure vm image list-publishers --location WestUS
```

지정된 게시자에 사용 가능한 제품(제품) 나열:

```bash
azure vm image list-offers --location WestUS --publisher Canonical
```

지정된 제품 중 사용 가능한 SKU(배포판 릴리스) 나열:

```bash
azure vm image list-skus --location WestUS --publisher Canonical --offer UbuntuServer
```

지정된 릴리스에 사용 가능한 모든 이미지 나열:

```bash
azure vm image list --location WestUS --publisher Canonical --offer UbuntuServer --sku 16.04.0-LTS
```

검색 및 사용 가능한 이미지를 사용하는 더 많은 예제는 [Azure CLI를 사용하여 Azure 가상 컴퓨터 이미지 탐색 및 선택](virtual-machines-linux-cli-ps-findimage.md)을 참조하세요.

또한 `azure vm quick-create` 및 `azure vm create` 명령에는 일반적인 배포판 및 해당 최신 릴리스에 신속하게 액세스하는 데 사용할 수 있는 몇 가지 별칭이 있습니다. VM을 만들 때마다 게시자, 제품, SKU 및 버전을 지정하는 것보다 쉽습니다.

| Alias | 게시자 | 제안 | SKU | 버전 |
|:----------|:----------|:-------------|:------------|:--------|
| CentOS | OpenLogic | Centos | 7\.2 | 최신 |
| CoreOS | CoreOS | CoreOS | Stable | 최신 |
| Debian | credativ | Debian | 8 | 최신 |
| openSUSE | SUSE | openSUSE | 13\.2 | 최신 |
| RHEL | Redhat | RHEL | 7\.2 | 최신 |
| SLES | SLES | SLES | 12-SP1 | 최신 |
| UbuntuLTS | Canonical | UbuntuServer | 14\.04.4-LTS | 최신 |

### 사용자 고유의 이미지 사용

특정 사용자 지정이 필요한 경우 해당 VM을 *캡처*하여 기존 Azure VM을 기반으로 하는 이미지를 사용하거나 VHD(가상 하드 디스크)에 저장된 자체 이미지를 업로드할 수 있습니다. 지원되는 배포판 및 사용자 고유의 이미지를 사용하는 방법에 대한 자세한 내용은 다음 문서를 참조하세요.

* [Azure 인증 배포](virtual-machines-linux-endorsed-distros.md)

* [보증되지 않는 배포에 대한 정보](virtual-machines-linux-create-upload-generic.md)

* [Linux 가상 컴퓨터를 Resource Manager 템플릿으로 캡처하는 방법](virtual-machines-linux-capture-image.md). 빠른 시작 명령:

	```bash
	azure vm deallocate --resource-group TestRG --vm-name TestVM
	azure vm generalize --resource-group TestRG --vm-name TestVM
	azure vm capture --resource-group TestRG --vm-name TestVM --vhd-name-prefix CapturedVM
	```

## 다음 단계

* 이 자습서 중 하나를 사용하여 [CLI](virtual-machines-linux-quick-create-cli.md)로 [포털](virtual-machines-linux-quick-create-portal.md)에서 Linux VM을 만들거나 Azure Resource Manager [템플릿](virtual-machines-linux-cli-deploy-templates.md)을 사용하여 Linux VM을 만듭니다.

* Linux VM을 만든 후에 쉽게 [데이터 디스크를 추가](virtual-machines-linux-add-disk.md)할 수 있습니다.

* [암호 또는 SSH 키 다시 설정 및 사용자 관리](virtual-machines-linux-using-vmaccess-extension.md)하는 빠른 단계

<!---HONumber=AcomDC_0706_2016-->