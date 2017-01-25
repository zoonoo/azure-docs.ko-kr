---
title: "Azure에서 Linux VM을 만드는 다양한 방법 | Microsoft Azure"
description: "각 방법에 대한 도구 및 자습서를 비롯하여 Azure에 Linux 가상 컴퓨터를 만드는 다양한 방법을 알아봅니다."
services: virtual-machines-linux
documentationcenter: 
author: iainfoulds
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: f38f8a44-6c88-4490-a84a-46388212d24c
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 01/03/2016
ms.author: iainfou
translationtype: Human Translation
ms.sourcegitcommit: 44c46fff9ccf9c7dba9ee380faf5f8213b58e3c3
ms.openlocfilehash: 4397d84ef4d97bdee387777a193ec0b969f2d5e1


---
# <a name="different-ways-to-create-a-linux-vm-including-azure-cli-20-preview"></a>Azure CLI 2.0(미리 보기)을 포함한 Linux VM을 만드는 다양한 방법
Azure에는 편리한 도구와 워크플로를 사용하여 Linux VM(가상 컴퓨터)을 만드는 유연성이 있습니다. 이 문서에서는 Linux 가상 컴퓨터를 만드는 경우의 차이점과 옵션을 요약하고 있습니다.

## <a name="azure-cli"></a>Azure CLI

다음 CLI 버전 중 하나를 사용하여 태스크를 완료할 수 있습니다.

- Azure CLI 1.0 - 클래식 및 리소스 관리 배포 모델용 CLI
- [Azure CLI 2.0(미리 보기)](../xplat-cli-install.md) - 리소스 관리 배포 모델용 차세대 CLI

Azure CLI 2.0(미리 보기)는 npm 패키지, 배포판 제공 패키지 또는 Docker 컨테이너를 통해 여러 플랫폼에서 사용할 수 있습니다. **az login**을 사용하여 로그인했는지 확인합니다.

다음 자습서에서는 Azure CLI 2.0(미리 보기)을 사용하는 방법에 대한 예제를 제공합니다. 보여 주는 명령에 대한 자세한 내용은 각각의 해당 문서를 참조하세요.

* [Azure CLI 2.0(미리 보기)을 사용하여 Linux VM 만들기](virtual-machines-linux-quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
  
  * 이 예제에서는 myResourceGroup이라는 리소스 그룹을 만듭니다. 
    
    ```azurecli
    az group create -n myResourceGroup -l westus
    ```

  * 이 예제에서는 `id_rsa.pub`이라는 공개 키를 가진 최신 Debian 이미지를 사용하여 VM을 새 리소스 그룹에 만듭니다.

    ```azurecli
    az vm create \
    --image credativ:Debian:8:latest \
    --admin-username ops \
    --ssh-key-value ~/.ssh/id_rsa.pub \
    --public-ip-address-dns-name mydns \
    --resource-group myResourceGroup \
    --location westus \
    --name myVM
    ```

* [Azure 템플릿을 사용하여 안전한 Linux VM 만들기](virtual-machines-linux-create-ssh-secured-vm-from-template.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
  
  * 다음 예제에서는 GitHub에 저장된 템플릿을 사용하여 VM을 만듭니다.
    
    ```azurecli
    az group deployment create -g myResourceGroup \ 
      --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json \
      --parameters @myparameters.json
    ```
    
* [Azure CLI를 사용하여 완전한 Linux 환경 만들기](virtual-machines-linux-create-cli-complete.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
  
  * 가용성 집합에서 부하 분산 장치 및 여러 VM을 만드는 방법이 포함됩니다.

* [Linux VM에 디스크 추가](virtual-machines-linux-add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
  
  * 다음 예제에서는 `myVM`라는 기존 VM에 5Gb 디스크를 추가합니다.
    
    ```azurecli
    az vm disk attach-new --resource-group myResourceGroup --vm-name myVM \
      --disk-size 5 --vhd https://myStorage.blob.core.windows.net/vhds/myDataDisk1.vhd
    ```

## <a name="azure-portal"></a>Azure 포털
[Azure Portal](https://portal.azure.com) 을 사용하면 시스템에 설치할 프로그램이 없으므로 VM을 신속하게 만들 수 있습니다. Azure 포털을 사용하여 다음과 같이 VM을 만듭니다.

* [Azure 포털을 사용하여 Linux VM 만들기](virtual-machines-linux-quick-create-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) 
* [Azure 포털을 사용하여 디스크 연결](virtual-machines-linux-attach-disk-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="operating-system-and-image-choices"></a>운영 체제 및 이미지 선택
VM을 만드는 경우 실행하려는 운영 체제에 따라 이미지를 선택합니다. Azure 및 해당 파트너는 미리 설치된 응용 프로그램 및 도구를 포함하는 여러 이미지를 제공합니다. 또는 사용자 고유의 이미지 중 하나를 업로드합니다( [다음 섹션](#use-your-own-image)을 참조).

### <a name="azure-images"></a>Azure 이미지
`az vm image` CLI 명령을 사용하여 게시자, 배포판 릴리스 및 빌드에 따라 사용 가능한 기능을 확인합니다.

사용 가능한 게시자 나열:

```azurecli
az vm image list-publishers -l WestUS
```

지정된 게시자에 사용 가능한 제품(제품) 나열:

```azurecli
az vm image list-offers --publisher-name Canonical -l WestUS
```

지정된 제품 중 사용 가능한 SKU(배포판 릴리스) 나열:

```azurecli
az vm image list-skus --publisher-name Canonical --offer UbuntuServer -l WestUS
```

지정된 릴리스에 사용 가능한 모든 이미지 나열:

```azurecli
az vm image list --publisher Canonical --offer UbuntuServer --sku 16.04.0-LTS -l WestUS
```

사용 가능한 이미지 검색 및 사용에 대한 추가 예제는 [Azure CLI를 사용하여 Azure 가상 컴퓨터 이미지 탐색 및 선택](virtual-machines-linux-cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)을 참조하세요.

`az vm create` 명령에는 일반적인 배포판 및 해당 최신 릴리스에 빠르게 액세스하는 데 사용할 수 있는 몇 가지 별칭이 있습니다. 별칭을 사용하는 방법이 VM을 만들 때마다 게시자, 제품, SKU 및 버전을 지정하는 것보다 빠릅니다.

| Alias | 게시자 | 제안 | SKU | 버전 |
|:--- |:--- |:--- |:--- |:--- |
| CentOS |OpenLogic |Centos |7.2 |최신 |
| CoreOS |CoreOS |CoreOS |Stable |최신 |
| Debian |credativ |Debian |8 |최신 |
| openSUSE |SUSE |openSUSE |13.2 |최신 |
| RHEL |Redhat |RHEL |7.2 |최신 |
| SLES |SLES |SLES |12-SP1 |최신 |
| UbuntuLTS |Canonical |UbuntuServer |14.04.4-LTS |최신 |

### <a name="use-your-own-image"></a>사용자 고유의 이미지 사용
특정 사용자 지정이 필요한 경우 해당 VM을 *캡처* 하여 기존 Azure VM을 기반으로 하는 이미지를 사용할 수 있습니다. 이미지로 만든 온-프레미스를 업로드할 수도 있습니다. 지원되는 배포판 및 사용자 고유의 이미지를 사용하는 방법에 대한 자세한 내용은 다음 문서를 참조하세요.

* [Azure 인증 배포](virtual-machines-linux-endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [보증되지 않는 배포에 대한 정보](virtual-machines-linux-create-upload-generic.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Linux 가상 컴퓨터를 Resource Manager 템플릿으로 캡처하는 방법](virtual-machines-linux-capture-image.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
  
  * 기존 VM을 캡처하는 빠른 시작 예제 명령은 다음과 같습니다.
    
    ```azurecli
    az vm deallocate -g myResourceGroup -n myVM
    az vm generalize -g myResourceGroup -n myVM
    az vm capture -g myResourceGroup -n myVM --vhd-name-prefix myCapturedVM
    ```

## <a name="next-steps"></a>다음 단계
* [CLI](virtual-machines-linux-quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)로 [포털](virtual-machines-linux-quick-create-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)에서 Linux VM을 만들거나 [Azure Resource Manager 템플릿](virtual-machines-linux-cli-deploy-templates.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)을 사용하여 Linux VM을 만듭니다.
* Linux VM을 만든 후에 [데이터 디스크를 추가합니다](virtual-machines-linux-add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
*  [암호 또는 SSH 키 다시 설정 및 사용자 관리](virtual-machines-linux-using-vmaccess-extension.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)




<!--HONumber=Jan17_HO1-->


