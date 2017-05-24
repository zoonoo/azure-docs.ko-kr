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
ms.devlang: azurecli
ms.topic: get-started-article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 05/11/2017
ms.author: iainfou
ms.translationtype: Human Translation
ms.sourcegitcommit: 97fa1d1d4dd81b055d5d3a10b6d812eaa9b86214
ms.openlocfilehash: 756711abb014829971af126c5cb60c12e79c920e
ms.contentlocale: ko-kr
ms.lasthandoff: 05/11/2017


---
# <a name="different-ways-to-create-a-linux-vm"></a>Linux VM을 만드는 다양한 방법
Azure에는 편리한 도구와 워크플로를 사용하여 Linux VM(가상 컴퓨터)을 만드는 유연성이 있습니다. 이 문서에서는 Azure CLI 2.0을 포함하여 Linux 가상 컴퓨터를 만드는 경우의 차이점과 옵션을 요약하고 있습니다. [Azure CLI 1.0](creation-choices-nodejs.md)을 포함하여 만들기 선택 항목을 볼 수도 있습니다.

[Azure CLI 2.0](/cli/azure/install-az-cli2)은 npm 패키지, 배포판 제공 패키지 또는 Docker 컨테이너를 통해 여러 플랫폼에서 사용할 수 있습니다. [az login](/cli/azure/#login)을 사용하여 Azure 계정에 환경 및 로그인에 가장 적합한 빌드를 설치합니다.

* [Azure CLI 2.0을 사용하여 Linux VM 만들기](quick-create-cli.md)
  
  * *myResourceGroup*이라는 [az group create](/cli/azure/group#create)를 사용하여 리소스 그룹을 만듭니다. 
   
    ```azurecli
    az group create --name myResourceGroup --location eastus
    ```
    
  * 최신 *UbuntuLTS* 이미지를 사용하여 *myVM*이라는 [az vm create](/cli/azure/vm#create)를 사용하여 VM을 만들고 *~/.ssh*에 이미 있는 경우 SSH 키를 생성합니다.

    ```azurecli
    az vm create \
        --resource-group myResourceGroup \
        --name myVM \
        --image UbuntuLTS \
        --generate-ssh-keys
    ```

* [Azure 템플릿을 사용하여 Linux VM 만들기](create-ssh-secured-vm-from-template.md)
  
  * 다음 예제에서는 GitHub에 저장된 템플릿에서 VM을 만들기 위해 [az group deployment create](/cli/azure/group/deployment#create)를 사용합니다.
    
    ```azurecli
    az group deployment create --resource-group myResourceGroup \ 
      --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json \
      --parameters @myparameters.json
    ```
* [Linux VM을 만들고 cloud-init를 사용하여 사용자 지정](tutorial-automate-vm-deployment.md)

* [여러 Linux VM에서 부하가 분산된 고가용성 응용 프로그램 만들기](tutorial-load-balancer.md)


## <a name="azure-portal"></a>Azure 포털
[Azure Portal](https://portal.azure.com) 을 사용하면 시스템에 설치할 프로그램이 없으므로 VM을 신속하게 만들 수 있습니다. Azure 포털을 사용하여 다음과 같이 VM을 만듭니다.

* [Azure 포털을 사용하여 Linux VM 만들기](quick-create-portal.md) 


## <a name="operating-system-and-image-choices"></a>운영 체제 및 이미지 선택
VM을 만드는 경우 실행하려는 운영 체제에 따라 이미지를 선택합니다. Azure 및 해당 파트너는 미리 설치된 응용 프로그램 및 도구를 포함하는 여러 이미지를 제공합니다. 또는 사용자 고유의 이미지 중 하나를 업로드합니다( [다음 섹션](#use-your-own-image)을 참조).

### <a name="azure-images"></a>Azure 이미지
[az vm image](/cli/azure/vm/image) 명령을 사용하여 게시자, 배포판 릴리스 및 빌드에 따라 사용 가능한 기능을 확인합니다.

사용 가능한 게시자 나열:

```azurecli
az vm image list-publishers --location eastus
```

지정된 게시자에 사용 가능한 제품(제품) 나열:

```azurecli
az vm image list-offers --publisher Canonical --location eastus
```

지정된 제품 중 사용 가능한 SKU(배포판 릴리스) 나열:

```azurecli
az vm image list-skus --publisher Canonical --offer UbuntuServer --location eastus
```

지정된 릴리스에 사용 가능한 모든 이미지 나열:

```azurecli
az vm image list --publisher Canonical --offer UbuntuServer --sku 16.04.0-LTS --location eastus
```

사용 가능한 이미지 검색 및 사용에 대한 추가 예제는 [Azure CLI를 사용하여 Azure 가상 컴퓨터 이미지 탐색 및 선택](cli-ps-findimage.md)을 참조하세요.

[az vm create](/cli/azure/vm#create) 명령에는 일반적인 배포판 및 해당 최신 릴리스에 빠르게 액세스하는 데 사용할 수 있는 몇 가지 별칭이 있습니다. 별칭을 사용하는 방법이 VM을 만들 때마다 게시자, 제품, SKU 및 버전을 지정하는 것보다 빠릅니다.

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
특정 사용자 지정이 필요한 경우 해당 VM을 캡처하여 기존 Azure VM을 기반으로 하는 이미지를 사용할 수 있습니다. 이미지로 만든 온-프레미스를 업로드할 수도 있습니다. 지원되는 배포판 및 사용자 고유의 이미지를 사용하는 방법에 대한 자세한 내용은 다음 문서를 참조하세요.

* [Azure 인증 배포](endorsed-distros.md)
* [보증되지 않는 배포에 대한 정보](create-upload-generic.md)
* [기존 Azure VM에서 이미지를 만드는 방법](tutorial-custom-images.md)
  
  * 기존 Azure VM에서 이미지를 만드는 빠른 시작 예제 명령:
    
    ```azurecli
    az vm deallocate --resource-group myResourceGroup --name myVM
    az vm generalize --resource-group myResourceGroup --name myVM
    az vm image create --resource-group myResourceGroup --source myVM --name myImage
    ```

## <a name="next-steps"></a>다음 단계
* [CLI](quick-create-cli.md)로 [포털](quick-create-portal.md)에서 Linux VM을 만들거나 [Azure Resource Manager 템플릿](../windows/cli-deploy-templates.md)을 사용하여 Linux VM을 만듭니다.
* Linux VM을 만든 후 [Azure 디스크 및 저장소에 대해 알아봅니다](tutorial-manage-disks.md).
* [암호 또는 SSH 키를 다시 설정하고 사용자를 관리](using-vmaccess-extension.md)하는 빠른 단계.

