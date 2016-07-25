<properties
	pageTitle="Linux VHD 만들기 및 업로드 | Microsoft Azure"
	description="리소스 관리자 배포 모델을 사용하여 가상 하드 디스크 (VHD)를 Azure에 만들고 업로드합니다."
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
	ms.date="07/07/2016"
	ms.author="iainfou"/>

# 가상 하드 디스크 업로드

이 문서에서는 리소스 관리자 배포 모델을 사용하여 가상 하드 디스크 (VHD)를 업로드하는 방법을 보여 줍니다. 이 기능을 사용하면 Linux 배포판을 요구에 맞게 설치 및 구성하고 해당 VHD를 사용하여 Azure 가상 컴퓨터 (Vm)를 신속하게 만들 수 있습니다.

## 빠른 명령
[Azure CLI](../xplat-cli-install.md)에 로그인하여 리소스 관리자 모드(`azure config mode arm`)를 사용하는지 확인합니다.

먼저 리소스 그룹을 만듭니다.

```bash
azure group create TestRG --location "WestUS"
```

가상 디스크를 보유할 저장소 계정을 만듭니다.

```bash
azure storage account create testuploadedstorage --resource-group TestRG \
	--location "WestUS" --kind Storage --sku-name LRS
```

방금 만든 저장소 계정에 대한 저장소 키를 나열하고 `key1`를 기록해 둡니다.

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

가상 네트워크, 공용 IP 주소, 사용자 이름 및 SSH 키 등과 같은 `azure vm create` 명령이 필요로 하는 모든 추가 매개 변수가 여전히 필요합니다. [사용 가능한 CLI 리소스 관리자 매개변수](azure-cli-arm-commands.md#azure-vm-commands-to-manage-your-azure-virtual-machines)에 대해 자세히 알아볼 수 있습니다.

## 요구 사항
위의 단계를 완료하려면 다음이 필요합니다.

- **.vhd 파일에 설치된 Linux 운영 체제** - 가상 디스크에 VHD 형식으로 [Azure 보증 Linux 배포판](virtual-machines-linux-endorsed-distros.md)(또는 [보증되지 않는 배포에 대한 정보](virtual-machines-linux-create-upload-generic.md) 참조)을 설치합니다. VM과 VHD를 만드는 도구는 여러 가지가 있습니다.
	- [QEMU](https://en.wikibooks.org/wiki/QEMU/Installing_QEMU) 또는 [KVM](http://www.linux-kvm.org/page/RunningKVM)을 설치 및 구성하고 VHD를 이미지 형식으로 사용하도록 주의합니다. 필요하댜면 `qemu-img convert`를 사용하여 [이미지를 변환](https://en.wikibooks.org/wiki/QEMU/Images#Converting_image_formats)할 수 있습니다.
	- 또한 [Windows 10](https://msdn.microsoft.com/virtualization/hyperv_on_windows/quick_start/walkthrough_install) 또는 [Windows Server 2012/2012 R2](https://technet.microsoft.com/library/hh846766.aspx)에서 Hyper-v를 사용할 수 있습니다.

> [AZURE.NOTE] 새 VHDX 형식은 Azure에서 지원되지 않습니다. VM을 만들 때 원래 원본 VHD 형식을 지정합니다. 필요하다면 [`qemu-img convert`](https://en.wikibooks.org/wiki/QEMU/Images#Converting_image_formats) 또는 [`Convert-VHD`](https://technet.microsoft.com/library/hh848454.aspx) PowerShell cmdlet을 사용하여 VHDX를 VHD 형식으로 변환할 수 있습니다. 그뿐 아니라 Azure는 동적 VHD 업로드를 지원하지 않으므로 업로드하기 전에 이러한 디스크를 정적 VHD로 변환해야 합니다. Azure로 업로딩하는 과정 중에 [GO용 Azure VHD 유틸리티](https://github.com/Microsoft/azure-vhd-utils-for-go)와 같은 도구를 사용하여 동적 디스크를 변환할 수 있습니다.


<a id="prepimage"> </a>
## 업로드할 이미지 준비

Azure에서는 다양한 Linux 배포를 지원합니다([보증 배포판](virtual-machines-linux-endorsed-distros.md) 참조). 다음 문서에서는 Azure에서 지원되는 다양한 Linux 배포를 준비하는 방법을 안내합니다. 업로드하기 전에 가상 디스크를 적절하게 준비했는지 확인합니다.

- **[CentOS 기반 배포판](virtual-machines-linux-create-upload-centos.md)**
- **[Debian Linux](virtual-machines-linux-debian-create-upload-vhd.md)**
- **[Oracle Linux](virtual-machines-linux-oracle-create-upload-vhd.md)**
- **[Red Hat Enterprise Linux](virtual-machines-linux-redhat-create-upload-vhd.md)**
- **[SLES 및 openSUSE](virtual-machines-linux-suse-create-upload-vhd.md)**
- **[Ubuntu](virtual-machines-linux-create-upload-ubuntu.md)**
- **[기타 - 보증되지 않는 배포](virtual-machines-linux-create-upload-generic.md)**

또한 Azure용 Linux 이미지를 준비하는 방법에 대한 일반적인 추가 팁은 **[Linux 설치 참고 사항](virtual-machines-linux-create-upload-generic.md#general-linux-installation-notes)**을 참조하세요.

> [AZURE.NOTE] [Azure 인증 배포의 Linux](virtual-machines-linux-endorsed-distros.md)의 '지원되는 버전'에 지정된 대로 보증 배포판 중 하나가 구성 세부 정보와 함께 사용되는 경우에만 Linux를 실행하는 VM에 [Azure 플랫폼 SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/)가 적용됩니다.

## 다음 단계
사용자 지정 가상 디스크를 준비하고 업로드한 후 [리소스 관리자 및 템플릿 사용하기](../resource-group-overview.md)에 관해 자세히 알아 볼 수 있습니다. 또한 새 Vm에 [데이터 디스크 추가](virtual-machines-linux-add-disk.md)를 고려할 수도 있습니다. 응용 프로그램이 액세스해야 할 Vm에서 실행되고 있다면 반드시 [포트 및 끝점 열기](virtual-machines-linux-nsg-quickstart.md)를 해야 합니다.

<!---HONumber=AcomDC_0713_2016-->