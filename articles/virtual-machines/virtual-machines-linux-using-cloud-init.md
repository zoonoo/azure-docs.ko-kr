<properties
    pageTitle="cloud-init를 사용하여 생성 중인 Linux VM 사용자 지정 | Microsoft Azure"
    description="cloud-init를 사용하여 생성 중인 Linux VM 사용자 지정."
    services="virtual-machines-linux"
    documentationCenter=""
    authors="vlivech"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"
/>

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/30/2016"
    ms.author="v-livech"
/>

# cloud-init를 사용하여 생성 중인 Linux VM 사용자 지정

이 문서에서는 cloud-init 스크립트를 사용하여 호스트 이름 설정, 설치된 패키지 업데이트, 사용자 계정 관리를 수행하는 방법을 설명합니다. VM을 만드는 중에 Azure CLI에서 cloud-init 스크립트를 호출합니다.

## 필수 조건

이러한 작업을 위한 필수 구성 요소는 [Azure 계정](https://azure.microsoft.com/pricing/free-trial/), [SSH 공개 키 및 개인 키](virtual-machines-linux-mac-create-ssh-keys.md), 그리고 설치된 후 `azure config mode arm`을 사용하여 Azure Resource Manager 모드로 전환된 [Azure CLI](../xplat-cli-install.md)입니다.

## 빠른 명령

호스트 이름을 설정하고, 모든 패키지를 업데이트하고, Linux에 sudo 사용자를 추가하는 cloud-init.txt 스크립트를 만듭니다.

```bash
#cloud-config
hostname: exampleServerName
apt_upgrade: true
users:
  - name: exampleUser
    groups: sudo
    shell: /bin/bash
    sudo: ['ALL=(ALL) NOPASSWD:ALL']
    ssh-authorized-keys:
      - ssh-rsa AAAAB3<snip>==exampleuser@slackwarelaptop
```

cloud-init을 사용해 Linux VM을 만들어서 부팅 중에 구성합니다.

```bash
azure group create cloudinitexample westus
```

```bash
azure vm create \
--resource-group cloudinitexample \
--name cloudinitexample \
--location westus \
--os-type Linux \
--nic-name cloudinitnicexample \
--vnet-name cloudinitvnetexample \
--vnet-address-prefix 10.0.0.0/22 \
--vnet-subnet-name cloudinitvsubnet \
--vnet-subnet-address-prefix 10.0.0.0/24 \
--image-urn canonical:ubuntuserver:14.04.2-LTS:latest \
--ssh-publickey-file ~/.ssh/azure_id_rsa.pub \
--admin-username ahmet \
--custom-data cloud-init.txt

```

## 소개

새 Linux VM을 시작하면 사용자 지정된 사항이나 사용자의 요구에 맞게 준비된 기능이 없는 표준 Linux VM이 시작됩니다. [cloud-init](https://cloudinit.readthedocs.org)은 Linux VM을 처음으로 부팅할 때 해당 VM에 스크립트 또는 구성 설정을 삽입하는 표준 방법입니다.

Azure에서는 다음의 세 가지 방법으로 배포 또는 부팅 중인 Linux VM을 변경할 수 있습니다.

- cloud-init을 사용하여 스크립트를 삽입합니다.
- Azure [VMAccess 확장](virtual-machines-linux-using-vmaccess-extension.md)을 사용하여 스크립트를 삽입합니다.
- Azure 템플릿을 사용합니다(cloud-init 사용).
- Azure 템플릿을 사용합니다([CustomScriptExtention](virtual-machines-linux-extensions-customscript.md) 사용).

부팅 후에 언제든지 스크립트를 삽입하려면 다음 방법을 사용합니다.

- SSH를 통해 명령을 직접 실행합니다.
- Azure [VMAccess 확장](virtual-machines-linux-using-vmaccess-extension.md)을 사용하여 명령적으로 또는 Azure 템플릿에서 스크립트를 삽입합니다.
- Ansible, Salt, Chef, Puppet 등의 구성 관리 도구를 사용합니다.

>[AZURE.NOTE]\: VMAccess 확장은 SSH를 사용할 때와 같은 방식으로 스크립트를 루트로 실행합니다. 그러나 VM 확장 사용 시에는 시나리오에 따라 유용할 수 있는 다양한 Azure 제공 기능을 사용할 수 있습니다.

### Azure VM 빨리 만들기 이미지 별칭에 대한 cloud-init 사용 가능 여부

| Alias | 게시자 | 제안 | SKU | 버전 | cloud-init |
|:----------|:----------|:-------------|:------------|:--------|:-----------|
| CentOS | OpenLogic | Centos | 7\.2 | 최신 | no |
| CoreOS | CoreOS | CoreOS | Stable | 최신 | yes |
| Debian | credativ | Debian | 8 | 최신 | no |
| openSUSE | SUSE | openSUSE | 13\.2 | 최신 | no |
| RHEL | Redhat | RHEL | 7\.2 | 최신 | no |
| UbuntuLTS | Canonical | UbuntuServer | 14\.04.4-LTS | 최신 | yes |

Microsoft는 파트너와 협력하여 파트너가 Azure에 제공하는 이미지에 cloud-init를 포함하고 이러한 이미지에서 cloud-init가 작동하도록 설정하고 있습니다.


## 자세한 연습

### Azure CLI를 사용하여 VM 생성에 cloud-init 스크립트 추가

Azure에서 VM을 만들 때 cloud-init 스크립트를 시작하려면 Azure CLI `--custom-data` 스위치를 사용하여 cloud-init 파일을 지정합니다.

```bash
azure group create cloudinitexample westus
```

```bash
azure vm create \
--resource-group cloudinitexample \
--name cloudinitexample \
--location westus \
--os-type Linux \
--nic-name cloudinitnicexample \
--vnet-name cloudinitvnetexample \
--vnet-address-prefix 10.0.0.0/22 \
--vnet-subnet-name cloudinitvsubnet \
--vnet-subnet-address-prefix 10.0.0.0/24 \
--image-urn canonical:ubuntuserver:14.04.2-LTS:latest \
--ssh-publickey-file ~/.ssh/azure_id_rsa.pub \
--admin-username ahmet \
--custom-data cloud-init.txt

```

### cloud-init 스크립트를 만들어 Linux VM의 호스트 이름 설정

Linux VM의 가장 간단하고 중요한 설정 중 하나는 호스트 이름입니다. 이 스크립트와 함께 cloud-init을 사용하여 손쉽게 설정할 수 있습니다.

#### 예제 cloud-init 스크립트 `cloud_config_hostname.txt`

``` bash
#cloud-config
hostname: exampleServerName
```

VM을 처음 시작하는 동안 이 cloud-init 스크립트는 호스트 이름을 `exampleServerName`으로 설정합니다.

```bash
azure vm create \
--resource-group cloudinitexample \
--name cloudinitexample \
--location westus \
--os-type Linux \
--nic-name cloudinitnicexample \
--vnet-name cloudinitvnetexample \
--vnet-address-prefix 10.0.0.0/22 \
--vnet-subnet-name cloudinitvsubnet \
--vnet-subnet-address-prefix 10.0.0.0/24 \
--image-urn canonical:ubuntuserver:14.04.2-LTS:latest \
--ssh-publickey-file ~/.ssh/azure_id_rsa.pub \
--admin-username ahmet \
--custom-data cloud_config_hostname.txt

```

로그인한 다음 새 VM의 호스트 이름을 확인합니다.

```bash
ssh exampleVM
hostname
exampleServerName
```

### Linux를 업데이트하기 위한 cloud-init 만들기

보안상의 이유로 최초 부팅 시 Ubuntu VM을 업데이트하려고 합니다. 사용 중인 Linux 배포에 따라 다음 스크립트와 cloud-init을 사용하여 업데이트할 수 있습니다.

#### Debian 제품군용 예제 cloud-init 스크립트 `cloud_config_apt_upgrade.txt`

```bash
#cloud-config
apt_upgrade: true
```

Linux가 부팅되고 나면 설치된 모든 패키지가 `apt-get`을 통해 업데이트됩니다.

```bash
azure vm create \
--resource-group cloudinitexample \
--name cloudinitexample \
--location westus \
--os-type Linux \
--nic-name cloudinitnicexample \
--vnet-name cloudinitvnetexample \
--vnet-address-prefix 10.0.0.0/22 \
--vnet-subnet-name cloudinitvsubnet \
--vnet-subnet-address-prefix 10.0.0.0/24 \
--image-urn canonical:ubuntuserver:14.04.2-LTS:latest \
--ssh-publickey-file ~/.ssh/azure_id_rsa.pub \
--admin-username ahmet \
--custom-data cloud_config_apt_upgrade.txt
```

로그인한 다음 모든 패키지가 업데이트되었는지 확인합니다.

```bash
ssh exampleVM
sudo apt-get upgrade
Reading package lists... Done
Building dependency tree
Reading state information... Done
Calculating upgrade... Done
The following packages have been kept back:
  linux-generic linux-headers-generic linux-image-generic
0 upgraded, 0 newly installed, 0 to remove and 0 not upgraded.
```

### Linux에 사용자를 추가하기 위한 cloud-init 만들기

새 Linux VM에서 가장 먼저 해야 할 작업 중 하나는 사용자 자신을 추가하는 것이며, 그렇지 않을 경우 `root`를 사용하지 않아야 합니다. 보안 및 유용성 측면의 모범 사례인 SSH 키를 사용하는 것이 좋습니다. SSH 키는 이 cloud-init 스크립트와 함께 `~/.ssh/authorized_keys` 파일에 추가됩니다.

#### Debian 제품군용 예제 cloud-init 스크립트 `cloud_config_add_users.txt`

```bash
#cloud-config
users:
  - name: exampleUser
    groups: sudo
    shell: /bin/bash
    sudo: ['ALL=(ALL) NOPASSWD:ALL']
    ssh-authorized-keys:
      - ssh-rsa AAAAB3<snip>==exampleuser@slackwarelaptop
```

Linux가 부팅되고 나면 나열된 모든 사용자가 작성되어 sudo 그룹에 추가됩니다.

```bash
azure vm create \
--resource-group cloudinitexample \
--name cloudinitexample \
--location westus \
--os-type Linux \
--nic-name cloudinitnicexample \
--vnet-name cloudinitvnetexample \
--vnet-address-prefix 10.0.0.0/22 \
--vnet-subnet-name cloudinitvsubnet \
--vnet-subnet-address-prefix 10.0.0.0/24 \
--image-urn canonical:ubuntuserver:14.04.2-LTS:latest \
--ssh-publickey-file ~/.ssh/azure_id_rsa.pub \
--admin-username ahmet \
--custom-data cloud_config_add_users.txt
```

로그인한 다음 새로 생성된 사용자를 확인합니다.

```bash
cat /etc/group
```

출력

```bash
root:x:0:
<snip />
sudo:x:27:exampleUser
<snip />
exampleUser:x:1000:
```

## 다음 단계

cloud-init은 부팅 시 Linux VM을 수정하는 표준 방식의 하나로 자리잡고 있습니다. Azure에는 부팅 시에 또는 실행 중에 Linux VM을 수정할 수 있는 VM 확장도 있습니다. 예를 들어 VM을 실행하는 동안 Azure VMAccessExtension을 사용하여 SSH 또는 사용자 정보를 다시 설정할 수 있습니다. cloud-init을 사용하는 경우 암호를 다시 설정하려면 VM을 다시 부팅해야 합니다.

[가상 컴퓨터 확장 및 기능 정보](virtual-machines-linux-extensions-features.md)

[VMAccess 확장을 사용하여 사용자, SSH 관리 및 Azure Linux VM의 디스크 검사 또는 복구](virtual-machines-linux-using-vmaccess-extension.md)

<!---HONumber=AcomDC_0831_2016-->