---
title: "cloud-init를 사용하여 Linux VM 사용자 지정 | Microsoft Docs"
description: "Azure CLI 2.0에서 cloud-init를 사용하여 생성 중인 Linux VM을 사용자 지정하는 방법"
services: virtual-machines-linux
documentationcenter: 
author: iainfoulds
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 195c22cd-4629-4582-9ee3-9749493f1d72
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: article
ms.date: 05/11/2017
ms.author: iainfou
ms.translationtype: Human Translation
ms.sourcegitcommit: 97fa1d1d4dd81b055d5d3a10b6d812eaa9b86214
ms.openlocfilehash: a7a6daad34525683579e25b9591ed28f2bf29c04
ms.contentlocale: ko-kr
ms.lasthandoff: 05/11/2017


---
# <a name="use-cloud-init-to-customize-a-linux-vm-during-creation"></a>cloud-init를 사용하여 생성 중인 Linux VM 사용자 지정
이 문서에서는 cloud-init 스크립트를 사용하여 Azure CLI 2.0에서 호스트 이름 설정, 설치된 패키지 업데이트, 사용자 계정 관리를 수행하는 방법을 설명합니다. Azure CLI에서 VM(가상 컴퓨터)을 만드는 중에 cloud-init 스크립트가 호출됩니다. 응용 프로그램 설치, 구성 파일 작성 및 Key Vault의 키 삽입에 대한 보다 깊이 있는 개요를 보려면 [이 자습서](tutorial-automate-vm-deployment.md)를 참조하세요. [Azure CLI 1.0](using-cloud-init-nodejs.md)에서 이러한 단계를 수행할 수도 있습니다.

## <a name="quick-commands"></a>빠른 명령
호스트 이름을 설정하고, 모든 패키지를 업데이트하고, Linux에 sudo 사용자를 추가하는 cloud-init.txt 스크립트를 만듭니다.

```yaml
#cloud-config
hostname: myVMhostname
apt_upgrade: true
users:
  - name: myNewAdminUser
    groups: sudo
    shell: /bin/bash
    sudo: ['ALL=(ALL) NOPASSWD:ALL']
    ssh-authorized-keys:
      - ssh-rsa AAAAB3<snip>==myAdminUser@myVM
```

[az group create](/cli/azure/group#create)를 사용하여 VM을 시작하려면 리소스 그룹을 만듭니다. 다음 예제에서는 *myResourceGroup*이라는 리소스 그룹을 만듭니다.

```azurecli
az group create --name myResourceGroup --location eastus
```

cloud-init을 사용하여 [az vm create](/cli/azure/vm#create)로 Linux VM을 만든 후 `--custom-data` 매개 변수를 사용하여 부팅 중에 구성합니다.

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys \
    --custom-data cloud-init.txt
```

## <a name="detailed-walkthrough"></a>자세한 연습
새 Linux VM을 시작하면 사용자 지정된 사항이나 사용자의 요구에 맞게 준비된 기능이 없는 표준 Linux VM이 시작됩니다. [cloud-init](https://cloudinit.readthedocs.org) 은 Linux VM을 처음으로 부팅할 때 해당 VM에 스크립트 또는 구성 설정을 삽입하는 표준 방법입니다.

Azure에서는 다음의 몇 가지 방법으로 배포 또는 부팅 중인 Linux VM을 변경할 수 있습니다.

* cloud-init을 사용하여 스크립트를 삽입합니다.
* Azure [VMAccess 확장](using-vmaccess-extension.md)을 사용하여 스크립트를 삽입합니다.
* Azure 템플릿을 사용합니다(cloud-init 사용).
* Azure 템플릿을 사용합니다( [CustomScriptExtention](extensions-customscript.md)사용).

부팅 후에 언제든지 스크립트를 삽입하려면 다음 방법을 사용합니다.

* SSH를 통해 명령을 직접 실행합니다.
* Azure [VMAccess 확장](using-vmaccess-extension.md)을 사용하여 명령적으로 또는 Azure 템플릿에서 스크립트를 삽입합니다.
* Ansible, Salt, Chef, Puppet 등의 구성 관리 도구를 사용합니다.

> [!NOTE]
> VMAccess 확장은 SSH를 사용할 때와 같은 방식으로 스크립트를 루트로 실행합니다. 그러나 VM 확장 사용 시에는 시나리오에 따라 유용할 수 있는 다양한 Azure 제공 기능을 사용할 수 있습니다.

## <a name="cloud-init-availability-on-azure-vm-quick-create-image-aliases"></a>Azure VM 빨리 만들기 이미지 별칭에 대한 cloud-init 사용 가능 여부
| Alias | 게시자 | 제안 | SKU | 버전 | cloud-init |
|:--- |:--- |:--- |:--- |:--- |:--- |
| CentOS |OpenLogic |Centos |7.2 |최신 |no |
| CoreOS |CoreOS |CoreOS |Stable |최신 |yes |
| Debian |credativ |Debian |8 |최신 |no |
| openSUSE |SUSE |openSUSE |13.2 |최신 |no |
| RHEL |Redhat |RHEL |7.2 |최신 |no |
| UbuntuLTS |Canonical |UbuntuServer |14.04.4-LTS |최신 |yes |

당사는 파트너와 협력하여 파트너가 Azure에 제공하는 이미지에 cloud-init를 포함하고 이러한 이미지에서 cloud-init가 작동하도록 설정하고 있습니다.

## <a name="add-a-cloud-init-script-to-the-vm-creation-with-the-azure-cli"></a>Azure CLI를 사용하여 VM 생성에 cloud-init 스크립트 추가
Azure에서 VM을 만들 때 cloud-init 스크립트를 시작하려면 Azure CLI `--custom-data` 스위치를 사용하여 cloud-init 파일을 지정합니다.

[az group create](/cli/azure/group#create)를 사용하여 VM을 시작하려면 리소스 그룹을 만듭니다. 다음 예제에서는 *myResourceGroup*이라는 리소스 그룹을 만듭니다.

```azurecli
az group create --name myResourceGroup --location eastus
```

cloud-init을 사용하여 [az vm create](/cli/azure/vm#create)로 Linux VM을 만들어서 부팅 중에 구성합니다.

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys \
    --custom-data cloud-init.txt
```

## <a name="create-a-cloud-init-script-to-set-the-hostname-of-a-linux-vm"></a>cloud-init 스크립트를 만들어 Linux VM의 호스트 이름 설정
Linux VM의 가장 간단하고 중요한 설정 중 하나는 호스트 이름입니다. 이 스크립트와 함께 cloud-init을 사용하여 손쉽게 설정할 수 있습니다.  

### <a name="example-cloud-init-script-named-cloudconfighostnametxt"></a>예제 cloud-init 스크립트 `cloud_config_hostname.txt`
```yaml
#cloud-config
hostname: myservername
```

VM을 처음 시작하는 동안 이 cloud-init 스크립트는 호스트 이름을 *myservername*으로 설정합니다. cloud-init을 사용하여 [az vm create](/cli/azure/vm#create)로 Linux VM을 만들어서 부팅 중에 구성합니다.

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys \
    --custom-data cloud-init.txt
```

로그인한 다음 새 VM의 호스트 이름을 확인합니다.

```bash
ssh myVM
hostname
myservername
```

## <a name="create-a-cloud-init-script"></a>cloud-init 스크립트 만들기
보안상의 이유로 최초 부팅 시 Ubuntu VM을 업데이트하려고 합니다. 사용 중인 Linux 배포에 따라 다음 스크립트와 cloud-init을 사용하여 업데이트할 수 있습니다.

### <a name="example-cloud-init-script-cloudconfigaptupgradetxt-for-the-debian-family"></a>Debian 제품군용 예제 cloud-init 스크립트 `cloud_config_apt_upgrade.txt`
```yaml
#cloud-config
apt_upgrade: true
```

Linux가 부팅되고 나면 설치된 모든 패키지가 **apt-get**을 통해 업데이트됩니다. cloud-init을 사용하여 [az vm create](/cli/azure/vm#create)로 Linux VM을 만들어서 부팅 중에 구성합니다.

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys \
    --custom-data cloud_config_apt_upgrade.txt
```

로그인한 다음 모든 패키지가 업데이트되었는지 확인합니다.

```bash
ssh myUbuntuVM
sudo apt-get upgrade
Reading package lists... Done
Building dependency tree
Reading state information... Done
Calculating upgrade... Done
The following packages have been kept back:
  linux-generic linux-headers-generic linux-image-generic
0 upgraded, 0 newly installed, 0 to remove and 0 not upgraded.
```

## <a name="create-a-cloud-init-script-to-add-a-user-to-linux"></a>Linux에 사용자를 추가하기 위한 cloud-init 만들기
새 Linux VM에서 가장 먼저 해야 할 작업 중 하나는 사용자 자신을 추가하는 것이며, 그렇지 않을 경우 *root*를 사용하지 않아야 합니다. 보안 및 유용성 측면의 모범 사례인 SSH 키를 사용하는 것이 좋습니다. SSH 키는 이 cloud-init 스크립트와 함께 *~/.ssh/authorized_keys* 파일에 추가됩니다.

### <a name="example-cloud-init-script-cloudconfigadduserstxt-for-debian-family"></a>Debian 제품군용 예제 cloud-init 스크립트 `cloud_config_add_users.txt`
```yaml
#cloud-config
users:
  - name: myCloudInitAddedAdminUser
    groups: sudo
    shell: /bin/bash
    sudo: ['ALL=(ALL) NOPASSWD:ALL']
    ssh-authorized-keys:
      - ssh-rsa AAAAB3<snip>==myAdminUser@myUbuntuVM
```

Linux가 부팅되고 나면 나열된 모든 사용자가 작성되어 sudo 그룹에 추가됩니다. cloud-init을 사용하여 [az vm create](/cli/azure/vm#create)로 Linux VM을 만들어서 부팅 중에 구성합니다.

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys \
    --custom-data cloud_config_add_users.txt
```

로그인한 다음 새로 생성된 사용자를 확인합니다.

```bash
ssh myVM
cat /etc/group
```

출력

```bash
root:x:0:
<snip />
sudo:x:27:myCloudInitAddedAdminUser
<snip />
myCloudInitAddedAdminUser:x:1000:
```

## <a name="next-steps"></a>다음 단계
cloud-init은 부팅 시 Linux VM을 수정하는 표준 방식의 하나로 자리잡고 있습니다. Azure에는 부팅 시에 또는 실행 중에 Linux VM을 수정할 수 있는 VM 확장도 있습니다. 예를 들어 VM을 실행하는 동안 Azure VMAccessExtension을 사용하여 SSH 또는 사용자 정보를 다시 설정할 수 있습니다. cloud-init을 사용하는 경우 암호를 다시 설정하려면 VM을 다시 부팅해야 합니다.

[가상 컴퓨터 확장 및 기능 정보](extensions-features.md)

[VMAccess 확장을 사용하여 사용자, SSH 관리 및 Azure Linux VM의 디스크 검사 또는 복구](using-vmaccess-extension.md)
