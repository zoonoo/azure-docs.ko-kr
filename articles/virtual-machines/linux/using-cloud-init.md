---
title: "cloud-init를 사용하여 Linux VM 사용자 지정 | Microsoft Docs"
description: "Azure CLI 2.0에서 cloud-init를 사용하여 생성 중인 Linux VM을 사용자 지정하는 방법"
services: virtual-machines-linux
documentationcenter: 
author: iainfoulds
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 195c22cd-4629-4582-9ee3-9749493f1d72
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: article
ms.date: 10/03/2017
ms.author: iainfou
ms.openlocfilehash: 5559f258f5c29b07edb5e61be4755d67173019e0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="use-cloud-init-to-customize-a-linux-vm-in-azure"></a>Azure에서 cloud-init를 사용하여 Linux VM 사용자 지정 
이 문서에서는 [cloud-init](https://cloudinit.readthedocs.io) 스크립트를 사용하여 Azure의 가상 컴퓨터에 대해 호스트 이름 설정, 패키지 업데이트, 사용자 계정 관리를 수행하는 방법을 설명합니다. 이러한 cloud-init 스크립트는 Azure CLI 2.0으로 VM을 만들 때 부팅 시 실행됩니다. 응용 프로그램 설치, 구성 파일 작성 및 Key Vault의 키 삽입에 대한 더 상세한 개요는 [이 자습서](tutorial-automate-vm-deployment.md)를 참조하세요. [Azure CLI 1.0](using-cloud-init-nodejs.md)에서 이러한 단계를 수행할 수도 있습니다.


## <a name="cloud-init-overview"></a>Cloud-init 개요
[Cloud-init](https://cloudinit.readthedocs.io)는 처음 부팅 시 Linux VM을 사용자 지정하는 데 널리 사용되는 방법입니다. Cloud-init를 사용하여 패키지를 설치하고 파일을 쓰거나, 사용자 및 보안을 구성할 수 있습니다. 초기 부팅 프로세스 중에 cloud-init가 실행되면 구성을 적용하기 위한 추가 단계나 필요한 에이전트가 없습니다.

Cloud-init는 배포에서도 작동합니다. 예를 들어, 패키지를 설치하는 데 **apt-get install** 또는 **yum install**은 사용하지 않습니다. 대신 설치할 패키지 목록을 정의할 수 있습니다. cloud-init에서 선택한 배포판의 기본 패키지 관리 도구를 자동으로 사용합니다.

당사는 파트너와 협력하여 파트너가 Azure에 제공하는 이미지에 cloud-init를 포함하고 이러한 이미지에서 cloud-init가 작동하도록 설정하고 있습니다. 다음 표에서는 Azure 플랫폼 이미지에서 현재 cloud-init 가용성을 간략하게 설명합니다.

| Alias | 게시자 | 제안 | SKU | 버전 |
|:--- |:--- |:--- |:--- |:--- |:--- |
| UbuntuLTS |Canonical |UbuntuServer |16.04-LTS |최신 |
| UbuntuLTS |Canonical |UbuntuServer |14.04.5-LTS |최신 |
| CoreOS |CoreOS |CoreOS |Stable |최신 |


## <a name="set-the-hostname-with-cloud-init"></a>cloud-init로 호스트 이름 설정
cloud-init 파일은 [YAML](http://www.yaml.org)로 작성됩니다. Azure에서 [az vm create](/cli/azure/vm#create)를 사용하여 VM을 만들 때 cloud-init 스크립트를 실행하려면 cloud-init 파일을 `--custom-data` 스위치로 지정합니다. cloud-init 파일로 구성할 수 있는 몇 가지 예제를 살펴보겠습니다. 일반적인 시나리오는 VM의 호스트 이름을 설정하는 것입니다. 기본적으로 호스트 이름은 VM 이름과 동일합니다. 

먼저 [az group create](/cli/azure/group#create)를 사용하여 리소스 그룹을 만듭니다. 다음 예제에서는 *eastus* 위치에 *myResourceGroup*이라는 리소스 그룹을 만듭니다.

```azurecli
az group create --name myResourceGroup --location eastus
```

현재 셸에서*cloud_init_hostname.txt*라는 파일을 만들고 다음 구성을 붙여 넣습니다. 예를 들어 로컬 컴퓨터에 없는 Cloud Shell에서 파일을 만듭니다. 원하는 모든 편집기를 사용할 수 있습니다. Cloud Shell에서 `sensible-editor cloud_init_hostname.txt`를 입력하여 파일을 만들고 사용할 수 있는 편집기의 목록을 봅니다. 전체 cloud-init 파일, 특히 첫 줄이 올바르게 복사되었는지 확인합니다.

```yaml
#cloud-config
hostname: myhostname
```

이제 [az vm create](/cli/azure/vm#create)로 VM을 만들고 다음과 같이 `--custom-data cloud_init_hostname.txt`로 cloud-init 파일을 지정합니다.

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVMHostname \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys \
    --custom-data cloud_init_hostname.txt
```

만든 후에는 Azure CLI에 VM 관련 정보가 표시됩니다. VM에 SSH에 대한 `publicIpAddress`를 사용합니다. 사용자 고유의 주소를 다음과 같이 입력합니다.

```bash
ssh azureuser@publicIpAddress
```

VM 이름을 보려면 다음과 같이 `hostname` 명령을 사용합니다.

```bash
hostname
```

다음 예제 출력에서처럼 VM은 호스트 이름을 cloud-init 파일에 설정된 해당 값으로 보고해야 합니다.

```bash
myhostname
```

## <a name="update-a-vm-with-cloud-init"></a>cloud-init를 사용하여 VM 업데이트
보안상의 이유로 최초 부팅에서 최신 업데이트를 적용하도록 VM을 구성하려 할 수 있습니다. cloud-init는 다양한 Linux 배포에서 작동하므로 패키지 관리자에 대해 `apt` 또는 `yum`을 지정할 필요가 없습니다. 그 대신 `package_upgrade`를 지정하고 cloud-init 프로세스가 사용 중인 배포에 적합한 메커니즘을 판단하게 합니다. 이 워크플로를 통해 전체 배포에서 동일한 cloud-init 스크립트를 사용할 수 있습니다.

진행 중인 업그레이드 프로세스를 확인하려면 이름이 *cloud_init_upgrade.txt*인 cloud-init 파일을 만들고 다음 구성을 붙여 넣습니다.

```yaml
#cloud-config
package_upgrade: true
```

이제 [az vm create](/cli/azure/vm#create)로 VM을 만들고 다음과 같이 `--custom-data cloud_init_upgrade.txt`로 cloud-init 파일을 지정합니다.

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVMUpgrade \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys \
    --custom-data cloud_init_upgrade.txt
```

이전 명령에서 출력에 표시된 VM의 공용 IP 주소에 대한 SSH. 사용자 고유의 공용 IP 주소를 다음과 같이 입력합니다.

```bash
ssh azureuser@publicIpAddress
```

패키지 관리 도구를 실행하여 업데이트를 확인합니다. 다음 예제에서는 Ubuntu VM에서 `apt-get`을 사용합니다.

```bash
sudo apt-get upgrade
```

cloud-init가 부팅 시 업데이트를 확인하여 설치했으므로 다음 예제 출력에서처럼 적용할 업데이트가 없습니다.

```bash
Reading package lists... Done
Building dependency tree
Reading state information... Done
Calculating upgrade... Done
0 upgraded, 0 newly installed, 0 to remove and 0 not upgraded.
```

## <a name="add-a-user-to-a-vm-with-cloud-init"></a>cloud-init를 사용하여 VM에 사용자 추가
모든 새 Linux VM에서 가장 먼저 해야 할 작업 중 하나는 *root* 사용을 방지하기 위해 자신에 대한 사용자를 추가하는 것입니다. SSH 키는 보안 및 가용성의 모범 사례입니다. 키는 이 cloud-init 스크립트를 통해 *~/.ssh/authorized_keys* 파일에 추가됩니다.

Linux VM에 사용자를 추가하려면 이름이 *cloud_init_add_user.txt*인 cloud-init 파일을 만들고 다음 구성을 붙여 넣습니다. *ssh-authorized-keys*에 자체 공용 키(예: *~/.ssh/id_rsa.pub*의 내용)를 제공합니다.

```yaml
#cloud-config
users:
  - name: myadminuser
    groups: sudo
    shell: /bin/bash
    sudo: ['ALL=(ALL) NOPASSWD:ALL']
    ssh-authorized-keys:
      - ssh-rsa AAAAB3<snip>
```

이제 [az vm create](/cli/azure/vm#create)로 VM을 만들고 다음과 같이 `--custom-data cloud_init_add_user.txt`로 cloud-init 파일을 지정합니다.

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVMUser \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys \
    --custom-data cloud_init_add_user.txt
```

이전 명령에서 출력에 표시된 VM의 공용 IP 주소에 대한 SSH. 사용자 고유의 공용 IP 주소를 다음과 같이 입력합니다.

```bash
ssh myadminuser@publicIpAddress
```

VM 및 지정된 그룹에 사용자가 추가되었는지 확인하려면 다음과 같이 */etc/group* 파일의 내용을 확인합니다.

```bash
cat /etc/group
```

다음 예제 출력에서는 *cloud_init_add_user.txt* 파일의 사용자가 VM과 적합한 그룹에 추가되었음을 나타냅니다.

```bash
root:x:0:
<snip />
sudo:x:27:myadminuser
<snip />
myadminuser:x:1000:
```

## <a name="next-steps"></a>다음 단계
cloud-init는 부팅 시 Linux VM을 수정하는 표준 방식의 하나입니다. Azure에서는 VM 확장을 사용하여 부팅 시 또는 실행된 후 Linux VM을 수정할 수도 있습니다. 예를 들어 Azure VM 확장을 사용하여 최초 부팅이 아니라 실행 중인 VM에 스크립트를 실행할 수 있습니다. VM 확장에 대한 자세한 내용은 [VM 확장 및 기능](extensions-features.md)을, 확장 사용 방법에 대한 예제는 [VMAccess 확장을 사용하여 사용자, SSH 관리 및 Azure Linux VM의 디스크 검사 또는 복구](using-vmaccess-extension.md)를 참조하세요