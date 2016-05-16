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
    ms.date="04/29/2016"
    ms.author="v-livech"
/>

# cloud-init를 사용하여 생성 중인 Linux VM 사용자 지정

이 문서는 cloud-init 스크립트를 사용하여 호스트 이름 설정, 설치된 패키지 업데이트, 사용자 계정 관리를 수행하는 방법을 보여줍니다. 그런 다음 [Azure CLI](../xplat-cli-install.md)에서 VM을 생성하는 동안 이러한 cloud-init 스크립트를 사용합니다.

## 필수 조건

필수 구성 요소는 다음과 같습니다. [Azure 계정](https://azure.microsoft.com/pricing/free-trial/), [SSH 공용 및 개인 키](virtual-machines-linux-mac-create-ssh-keys.md), Linux VM을 실행할 Azure 리소스 그룹이 필요하며 Azure CLI를 설치한 후 `azure config mode arm`을 사용하여 ARM 모드로 전환해야 합니다.

## 소개

새 Linux VM을 시작하면 아무 것도 사용자 지정되어 있지 않고 사용자 요구 사항을 충족할 수 있는 표준 Linux VM 상태입니다. [Cloud-init](https://cloudinit.readthedocs.org)은 Linux VM을 처음으로 부팅할 때 해당 VM에 스크립트 또는 구성 설정을 삽입하는 표준 방법입니다.

Azure에서는 Linux VM을 시작할 때 해당 VM을 변경하는 세 가지 방법이 있습니다.

- cloud-init을 사용하여 스크립트를 삽입할 수 있습니다.
- Azure [CustomScriptExtention](virtual-machines-linux-extensions-customscript.md)을 사용하여 스크립트를 삽입할 수 있습니다.
- Azure 템플릿에서 사용자 지정 설정을 지정하고 이 템플릿을 사용하여 Linux VM(cloud-init 및 CustomScript VM 확장 및 기타 항목에 대한 지원 포함)을 시작 및 사용자 지정할 수 있습니다.

언제든지 스크립트를 삽입하려면 다음을 수행합니다.

- SSH를 사용하여 직접 명령을 실행합니다. Azure [CustomScriptExtention](virtual-machines-linux-extensions-customscript.md)을 필요한 위치 또는 Azure 템플릿에서 사용하거나 Ansible, Salt, Chef, Puppet과 같이 VM이 부팅을 완료한 후 SSH에서 작동하는 공통 구성 관리 도구를 사용할 수 있습니다.

참고: [CustomScriptExtention](virtual-machines-linux-extensions-customscript.md)은 SSH와 같은 방식으로 루트에서 스크립트를 실행하지만 VM 확장을 사용할 경우 시나리오에 따라 유용할 수 있는 Azure의 몇 가지 기능을 사용할 수 있습니다.

## 빠른 명령

호스트 이름 cloud-init 스크립트 만들기

```bash
#cloud-config
hostname: exampleServerName
```

Debian 제품군에 대해 처음 부팅 cloud-init 스크립트에 업데이트 Linux 만들기

```bash
#cloud-config
apt_upgrade: true
```

사용자 cloud-init 스크립트 추가 만들기

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

## 자세한 연습

### Azure CLI를 사용하여 VM 생성에 cloud-init 스크립트 추가

Azure에서 VM을 만들 때 cloud-init 스크립트를 실행하려면 Azure CLI `--custom-data` 스위치를 사용하여 cloud-init 파일을 지정합니다.

참고: 이 문서는 cloud-init 파일에 `--custom-data` 스위치를 만드는 방법에 대해 설명하지만 이 스위치를 사용하여 임시 코드 또는 파일을 전달할 수 있습니다. Linux VM이 이러한 파일에서 어떤 작업을 해야 하는지 이미 알고 있는 경우에는 작업이 자동으로 실행됩니다.

```bash
azure vm create \
--resource-group exampleRG \
--name exampleVM \
--location westus \
--admin-username exampleAdminUserName \
--os-type Linux \
--nic-name exampleNIC \
--image-urn canonical:ubuntuserver:14.04.2-LTS:latest \
--custom-data cloud_init_script.txt
```

### cloud-init 스크립트를 만들어 Linux VM의 호스트 이름 설정

Linux VM의 가장 간단하고 중요한 설정 중 하나는 호스트 이름입니다. 이 스크립트와 함께 cloud-init을 사용하여 손쉽게 설정할 수 있습니다.

#### `cloud_config_hostname.txt` 예제 cloud-init 스크립트

``` bash
#cloud-config
hostname: exampleServerName
```

VM을 처음 시작하는 동안 이 cloud-init 스크립트는 호스트 이름을 `exampleServerName`으로 설정합니다.

```bash
azure vm create \
--resource-group exampleRG \
--name exampleVM \
--location westus \
--admin-username exampleAdminUserName \
--os-type Linux \
--nic-name exampleNIC \
--image-urn canonical:ubuntuserver:14.04.2-LTS:latest \
--custom-data cloud_config_hostname.txt
```

로그인한 다음 새 VM의 호스트 이름을 확인합니다.

```bash
ssh exampleVM
hostname
exampleServerName
```

### Linux를 업데이트하기 위한 cloud-init 만들기

보안 상의 이유로 최초 부팅 시 Ubuntu VM을 업데이트하려고 합니다. 사용 중인 Linux 배포에 따라 다음 스크립트와 cloud-init을 사용하여 업데이트할 수 있습니다.

#### Debian 제품군의 예제 cloud-init 스크립트 `cloud_config_apt_upgrade.txt`

```bash
#cloud-config
apt_upgrade: true
```

새 Linux VM이 부팅되면 `apt-get`을 통해 설치된 모든 패키지를 자동으로 업데이트합니다.

```bash
azure vm create \
--resource-group exampleRG \
--name exampleVM \
--location westus \
--admin-username exampleAdminUserName \
--os-type Linux \
--nic-name exampleNIC \
--image-urn canonical:ubuntuserver:14.04.2-LTS:latest \
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

새 Linux VM에서 가장 먼저 해야 할 작업 중 하나는 사용자 자신을 추가하는 것이며, 그렇지 않을 경우 `root`를 사용하지 않아야 합니다. 그러면 암호 없는 보안 SSH 로그인에 대한 해당 사용자의 `~/.ssh/authorized_keys` 파일에 SSH 공개 키를 추가하여 보안을 강화할 수 있습니다.

#### Debian 제품군의 예제 cloud-init 스크립트 `cloud_config_add_users.txt`

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

새 Linux VM이 부팅되면 새 사용자를 만들어 sudo 그룹에 추가합니다.

```bash
azure vm create \
--resource-group exampleRG \
--name exampleVM \
--location westus \
--admin-username exampleAdminUserName \
--os-type Linux \
--nic-name exampleNIC \
--image-urn canonical:ubuntuserver:14.04.2-LTS:latest \
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

<!---HONumber=AcomDC_0504_2016-->