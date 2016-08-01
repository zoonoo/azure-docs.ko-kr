<properties
	pageTitle="사용자 지정 Linux 이미지 만들기 및 업로드 | Microsoft Azure"
	description="리소스 관리자 배포 모델을 사용하여 사용자 지정 Linux 이미지로 Azure에 VHD(가상 하드 디스크)를 만들고 업로드합니다."
	services="virtual-machines-linux"
	documentationCenter=""
	authors="iainfoulds"
	manager="timlt"
	editor="tysonn"
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-linux"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/15/2016"
	ms.author="iainfou"/>

# 사용자 지정 디스크 이미지에서 VM 업로드 및 만들기

이 문서에서는 리소스 관리자 배포 모델을 사용하여 가상 하드 디스크 (VHD)를 업로드하고 이 사용자 지정 이미지에서 VM을 만드는 방법을 보여 줍니다. 이 기능을 사용하면 Linux 배포판을 요구에 맞게 설치 및 구성하고 해당 VHD를 사용하여 Azure 가상 컴퓨터 (Vm)를 신속하게 만들 수 있습니다.

## 빠른 명령
[Azure CLI](../xplat-cli-install.md)에 로그인하여 리소스 관리자 모드(`azure config mode arm`)를 사용하는지 확인합니다.

먼저 리소스 그룹을 만듭니다.

```bash
azure group create TestRG --location "WestUS"
```

가상 디스크를 보유할 저장소 계정을 만듭니다.

```bash
azure storage account create testuploadedstorage --resource-group TestRG \
	--location "WestUS" --kind Storage --sku-name PLRS
```

방금 만든 저장소 계정에 대한 선택키를 나열하고 `key1`를 기록해 둡니다.

```bash
azure storage account keys list testuploadedstorage --resource-group TestRG
```

방금 얻은 저장소 키를 사용하여 저장소 계정 내에 컨테이너를 만듭니다.

```bash
azure storage container create --account-name testuploadedstorage \
	--account-key <key1> --container vm-images
```

마지막으로, 방금 만든 컨테이너에 VHD를 업로드합니다.

```bash
azure storage blob upload --blobtype page --account-name testuploadedstorage \
	--account-key <key1> --container vm-images /path/to/disk/yourdisk.vhd
```

이제 [Resource Manager 템플릿을 사용하거나](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-from-specialized-vhd) 다음과 같이 디스크에 URI을 지정하여 CLI를 통해 업로드된 가상 디스크에서 VM을 만들 수 있습니다.

```bash
azure vm create TestVM -l "WestUS" --resource-group TestRG \
	-Q https://testuploadedstorage.blob.core.windows.net/vm-images/yourdisk.vhd
```

대상 저장소 계정은 가상 디스크를 업로드한 곳과 같아야 합니다. 또한 가상 네트워크, 공용 IP 주소, 사용자 이름 및 SSH 키 등과 같은 `azure vm create` 명령이 필요로 하는 모든 추가 매개 변수를 지정하거나 프롬프트에 응답할 수 있습니다. [사용 가능한 CLI 리소스 관리자 매개변수](azure-cli-arm-commands.md#azure-vm-commands-to-manage-your-azure-virtual-machines)에 대해 자세히 알아볼 수 있습니다.


## 자세한 단계
사용자 지정 Linux 이미지를 준비하고 Azure에 업로드하는 데에는 여러 단계가 있습니다. 이 문서의 나머지 부분은 이전의 빠른 명령 집합에서 설명된 각 단계에 대한 자세한 정보를 제공합니다.


## 요구 사항
위의 단계를 완료하려면 다음이 필요합니다.

- **.vhd 파일에 설치된 Linux 운영 체제** - 가상 디스크에 VHD 형식으로 [Azure 보증 Linux 배포판](virtual-machines-linux-endorsed-distros.md)(또는 [보증되지 않는 배포에 대한 정보](virtual-machines-linux-create-upload-generic.md) 참조)을 설치합니다. VM과 VHD를 만드는 도구는 여러 가지가 있습니다.
	- [QEMU](https://en.wikibooks.org/wiki/QEMU/Installing_QEMU) 또는 [KVM](http://www.linux-kvm.org/page/RunningKVM)을 설치 및 구성하고 VHD를 이미지 형식으로 사용하도록 주의합니다. 필요하댜면 `qemu-img convert`를 사용하여 [이미지를 변환](https://en.wikibooks.org/wiki/QEMU/Images#Converting_image_formats)할 수 있습니다.
	- 또한 [Windows 10](https://msdn.microsoft.com/virtualization/hyperv_on_windows/quick_start/walkthrough_install) 또는 [Windows Server 2012/2012 R2](https://technet.microsoft.com/library/hh846766.aspx)에서 Hyper-v를 사용할 수 있습니다.

> [AZURE.NOTE] 새 VHDX 형식은 Azure에서 지원되지 않습니다. VM을 만들 때 VHD를 형식으로 지정합니다. 필요하다면 [`qemu-img convert`](https://en.wikibooks.org/wiki/QEMU/Images#Converting_image_formats) 또는 [`Convert-VHD`](https://technet.microsoft.com/library/hh848454.aspx) PowerShell cmdlet을 사용하여 VHDX 디스크를 VHD로 변환할 수 있습니다. 그뿐 아니라 Azure는 동적 VHD 업로드를 지원하지 않으므로 업로드하기 전에 이러한 디스크를 정적 VHD로 변환해야 합니다. Azure로 업로딩하는 과정 중에 [GO용 Azure VHD 유틸리티](https://github.com/Microsoft/azure-vhd-utils-for-go)와 같은 도구를 사용하여 동적 디스크를 변환할 수 있습니다.

- 사용자 지정 이미지에서 생성된 VM은 이미지 자체와 동일한 저장소 계정에 있어야 합니다.
	- 사용자 지정 이미지와 생성된 VM을 모두 저장할 저장소 계정 및 컨테이너 만들기
	- 모든 VM을 만든 후 이미지를 안전하게 삭제할 수 있습니다.


<a id="prepimage"> </a>
## 업로드할 이미지 준비

Azure에서는 다양한 Linux 배포를 지원합니다([보증 배포판](virtual-machines-linux-endorsed-distros.md) 참조). 다음 문서에서는 Azure에서 지원되는 다양한 Linux 배포를 준비하는 방법을 안내합니다.

- **[CentOS 기반 배포판](virtual-machines-linux-create-upload-centos.md)**
- **[Debian Linux](virtual-machines-linux-debian-create-upload-vhd.md)**
- **[Oracle Linux](virtual-machines-linux-oracle-create-upload-vhd.md)**
- **[Red Hat Enterprise Linux](virtual-machines-linux-redhat-create-upload-vhd.md)**
- **[SLES 및 openSUSE](virtual-machines-linux-suse-create-upload-vhd.md)**
- **[Ubuntu](virtual-machines-linux-create-upload-ubuntu.md)**
- **[기타 - 보증되지 않는 배포](virtual-machines-linux-create-upload-generic.md)**

또한 Azure용 Linux 이미지를 준비하는 방법에 대한 일반적인 추가 팁은 **[Linux 설치 참고 사항](virtual-machines-linux-create-upload-generic.md#general-linux-installation-notes)**을 참조하세요.

> [AZURE.NOTE] [Azure 인증 배포의 Linux](virtual-machines-linux-endorsed-distros.md)의 '지원되는 버전'에 지정된 대로 보증 배포판 중 하나가 구성 세부 정보와 함께 사용되는 경우에만 Linux를 실행하는 VM에 [Azure 플랫폼 SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/)가 적용됩니다.


## 리소스 그룹 만들기
리소스 그룹은 가상 네트워킹 및 저장소와 같은 가상 컴퓨터를 지원하기 위해 논리적으로 모든 Azure 리소스를 모읍니다. [여기서 Azure 리소스 그룹](../resource-group-overview.md)에 대해 자세히 알아 봅니다. 사용자 지정 디스크 이미지를 업로드하고 VM을 만들기 전에 먼저 리소스 그룹을 만들어야 합니다.

```bash
azure group create TestRG --location "WestUS"
```

## 저장소 계정 만들기
VM은 저장소 계정 내에서 페이지 blob으로 저장됩니다. [여기서 Azure Blob 저장소](../storage/storage-introduction.md#blob-storage)에 대해 자세히 알아 봅니다. 사용자 지정 디스크 이미지 및 VM에 대한 저장소 계정을 만들어야 합니다. 사용자 지정 디스크 이미지에서 만든 모든 VM는 그 이미지와 동일한 저장소 계정에 있어야 합니다.

방금 만든 리소스 그룹 내에 저장소 계정 만들기.

```bash
azure storage account create testuploadedstorage --resource-group TestRG \
	--location "WestUS" --kind Storage --sku-name PLRS
```

## 저장소 계정 키 나열
Azure는 각 저장소 계정에 대해 두 개의 512 비트 선택키를 생성합니다. 이러한 선택키는 쓰기 작업을 수행할 때와 같이 저장소 계정에 인증할 때에 사용됩니다. [여기서 저장소에 대한 액세스 관리](../storage/storage-create-storage-account.md#manage-your-storage-account)에 대해 자세히 알아 봅니다. `azure storage account keys list` 명령을 사용하여 선택키를 볼 수 있습니다.

방금 만든 저장소 계정에 대한 선택키 보기.

```bash
azure storage account keys list testuploadedstorage --resource-group TestRG
```

다음과 유사하게 출력됩니다.

```
info:    Executing command storage account keys list
+ Getting storage account keys
data:    Name  Key                                                                                       Permissions
data:    ----  ----------------------------------------------------------------------------------------  -----------
data:    key1  d4XAvZzlGAgWdvhlWfkZ9q4k9bYZkXkuPCJ15NTsQOeDeowCDAdB80r9zA/tUINApdSGQ94H9zkszYyxpe8erw==  Full
data:    key2  Ww0T7g4UyYLaBnLYcxIOTVziGAAHvU+wpwuPvK4ZG0CDFwu/mAxS/YYvAQGHocq1w7/3HcalbnfxtFdqoXOw8g==  Full
info:    storage account keys list command OK

```
다음 단계에서 저장소 계정과 상호 작용하는 데 사용할 수 있도록 `key1`을 기록해 둡니다.

## 저장소 컨테이너 만들기
로컬 파일 시스템을 논리적으로 구성하기 위해 서로 다른 디렉토리를 만드는 것과 같은 방식으로 가상 디스크 및 디스크 이미지를 구성하기 위해 저장소 계정 내에 컨테이너를 만듭니다. 저장소 계정에 포함할 수 있는 컨테이너의 수에는 제한이 없습니다.

이전 단계에서 얻은 선택키를 지정하여 새 컨테이너를 만듭니다.

```bash
azure storage container create --account-name testuploadedstorage \
	--account-key <key1> --container vm-images
```

## VHD를 업로드 합니다.
이제 사용자 지정 디스크 이미지를 실제로 업로드할 수 있습니다. VM이 사용하는 모든 가상 디스크를 통해 사용자 지정 디스크 이미지를 페이지 blob으로 업로드하고 저장합니다.

선택키 및 이전 단계에서 만든 컨테이너를 지정한 후, 로컬 컴퓨터에 있는 사용자 지정 디스크 이미지의 경로를 지정해야 합니다.

```bash
azure storage blob upload --blobtype page --account-name testuploadedstorage \
	--account-key <key1> --container vm-images /path/to/disk/yourdisk.vhd
```

## 사용자 지정 이미지에서 VM 만들기
사용자 지정 디스크 이미지에서 VM을 만들 때 URI를 디스크 이미지에 지정해야 하며 대상 저장소 계정이 사용자 지정 디스크 이미지를 저장한 곳과 일치하는지 확인해야 합니다. Azure CLI 또는 리소스 관리자 JSON 템플릿을 사용하여 VM을 만들 수 있습니다.


### Azure CLI를 사용하여 VM 만들기
`azure vm create` 명령을 사용하여 `--image-urn` (또는 단순히 `-Q`) 매개 변수가 사용자 지정 디스크 이미지를 가리키도록 지정합니다. `--storage-account-name` (또는 `-o`)가 사용자 지정 디스크 이미지가 저장된 저장소 계정와 일치하는지 확인합니다. VM을 저장하기 위해 같은 컨테이너를 사용자 지정 디스크 이미지로 사용할 필요는 없습니다. 다만 사용자 지정 디스크 이미지를 업로드 하기 전에 이전 단계들과 동일한 방법으로 추가 컨테이너를 만들어야 합니다.

사용자 지정 디스크 이미지에서 VM 만들기

```bash
azure vm create TestVM -l "WestUS" --resource-group TestRG \
	-Q https://testuploadedstorage.blob.core.windows.net/vm-images/yourdisk.vhd
	-o testuploadedstorage
```

가상 네트워크, 공용 IP 주소, 사용자 이름 및 SSH 키 등과 같은 `azure vm create` 명령이 필요로 하는 모든 추가 매개 변수를 여전히 지정하거나 프롬프트에 응답해야 합니다. [사용 가능한 CLI 리소스 관리자 매개변수](azure-cli-arm-commands.md#azure-vm-commands-to-manage-your-azure-virtual-machines)에 대해 자세히 알아봅니다.

### JSON 템플릿을 사용하여 VM 만들기
Azure Resource Manager 템플릿은 구축하려는 환경을 정의하는 JSON (JavaScript Notation) 파일입니다. 템플릿은 계산 또는 네트워크과 같은 다양한 리소스 공급자로 세분화됩니다. 기존 템플릿을 사용하거나 직접 템플릿을 작성할 수 있습니다. [리소스 관리자 및 템플릿 사용](../resource-group-overview.md)에 대해 자세히 알아봅니다.

템플릿의 `Microsoft.Compute/virtualMachines` 공급자 내에 VM에 대한 구성 세부 정보를 포함하는 `storageProfile` 노드가 있게 될 것입니다. 편집할 두 가지 주요 매개 변수는 사용자 지정 디스크 이미지와 새 VM의 가상 디스크를 가리키는 `image`과 `vhd` URI입니다. 다음은 사용자 지정 디스크 이미지 사용에 대한 JSON의 예입니다.

```bash
"storageProfile": {
          "osDisk": {
            "name": "TestVM",
            "osType": "Linux",
            "caching": "ReadWrite",
			"createOption": "FromImage",
            "image": {
              "uri": "https://testuploadedstorage.blob.core.windows.net/vm-images/yourdisk.vhd"
            },
            "vhd": {
              "uri": "https://testuploadedstorage.blob.core.windows.net/vhds/newvmname.vhd"
            }
          }
```

[사용자 지정 이미지에서 VM을 만드는 데 이 기존 템플릿](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-from-user-image)을 사용할 수 있거나 [고유한 Azure Resource Manager 템플릿 만들기](../resource-group-authoring templates.md)에 관해 알아봅니다.

템플릿을 구성했다면 `azure group deployment create` 명령을 사용하여 VM을 만듭니다. `--template-uri` 매개 변수를 사용하여 JSON 템플릿의 URI를 지정합니다.

```bash
azure group deployment create --resource-group TestTemplateRG
	--template-uri https://uri.to.template/yourtemplate.json
```

컴퓨터에 로컬로 저장된 JSON 파일이 있다면 `--template-file` 매개 변수를 대신 사용할 수 있습니다.

```bash
azure group deployment create --resource-group TestTemplateRG
	--template-file /path/to/yourtemplate.json
```


## 다음 단계
사용자 지정 가상 디스크를 준비하고 업로드한 후 [리소스 관리자 및 템플릿 사용하기](../resource-group-overview.md)에 관해 자세히 알아 볼 수 있습니다. 또한 새 Vm에 [데이터 디스크 추가](virtual-machines-linux-add-disk.md)를 고려할 수도 있습니다. 응용 프로그램이 액세스해야 할 Vm에서 실행되고 있다면 반드시 [포트 및 끝점 열기](virtual-machines-linux-nsg-quickstart.md)를 해야 합니다.

<!---HONumber=AcomDC_0720_2016-->