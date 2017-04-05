---
title: "GitHub에서 Azure Linux 에이전트 업데이트 | Microsoft Docs"
description: "Azure Linux VM의 Azure Linux 에이전트를 GitHub의 최신 버전으로 업데이트하는 방법을 알아봅니다."
services: virtual-machines-linux
documentationcenter: 
author: SuperScottz
manager: timlt
editor: 
tags: azure-resource-manager,azure-service-management
ms.assetid: f1f19300-987d-4f29-9393-9aba866f049c
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 12/14/2015
ms.author: mingzhan
translationtype: Human Translation
ms.sourcegitcommit: 503f5151047870aaf87e9bb7ebf2c7e4afa27b83
ms.openlocfilehash: d35e70c1d50bd4cb2d47188585354aa3e3d740d4
ms.lasthandoff: 03/29/2017


---
# <a name="how-to-update-the-azure-linux-agent-on-a-vm-to-the-latest-version-from-github"></a>VM의 Azure Linux 에이전트를 GitHub의 최신 버전으로 업데이트하는 방법
Azure Linux VM에서 [Azure Linux 에이전트](https://github.com/Azure/WALinuxAgent) 를 업데이트하려면 다음 항목이 이미 있어야 합니다.

1. Azure에서 실행 중인 Linux VM
2. SSH를 사용하여 해당 Linux VM에 연결

[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

<br>

> [!NOTE]
> Windows 컴퓨터에서 이 태스크를 수행할 경우 Linux 컴퓨터에 PuTTY 및 SSH를 사용할 수 있습니다. 자세한 내용은 [Linux를 실행하는 가상 컴퓨터에 로그온하는 방법](virtual-machines-linux-mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)을 참조하세요.
> 
> 

Azure 인증 Linux 배포판에서는 Azure Linux 에이전트 패키지를 해당 리포지토리에 저장하므로 가능하면 먼저 배포판 리포지토리에서 최신 버전을 확인하고 설치하세요.  

Ubuntu의 경우 다음을 입력하면 됩니다.

```bash
sudo apt-get install walinuxagent
```

그리고 CentOS에서 다음을 입력합니다.

```bash
sudo yum install waagent
```

Oracle Linux의 경우 `Addons` 리포지토리가 사용되도록 설정되었는지 확인합니다. 파일 `/etc/yum.repos.d/public-yum-ol6.repo`(Oracle Linux 6) 또는 파일 `/etc/yum.repos.d/public-yum-ol7.repo`(Oracle Linux)를 편집하고 이 파일의 **[ol6_addons]** 또는 **[ol7_addons]** 아래에서 줄 `enabled=0`을 `enabled=1`로 변경하도록 선택합니다.

그런 다음 최신 버전의 Azure Linux 에이전트를 설치하려면 다음을 입력합니다.

```bash
sudo yum install WALinuxAgent
```

추가 기능 리포지토리를 찾을 수 없는 경우 Oracle Linux 릴리스에 따라 .repo 파일의 맨 뒤에 다음 줄을 추가하기만 하면 됩니다.

Oracle Linux 6 가상 컴퓨터의 경우:

```sh
[ol6_addons]
name=Add-Ons for Oracle Linux $releasever ($basearch)
baseurl=http://public-yum.oracle.com/repo/OracleLinux/OL6/addons/x86_64
gpgkey=http://public-yum.oracle.com/RPM-GPG-KEY-oracle-ol6
gpgcheck=1
enabled=1
```

Oracle Linux 7 가상 컴퓨터의 경우:

```sh
[ol7_addons]
name=Oracle Linux $releasever Add ons ($basearch)
baseurl=http://public-yum.oracle.com/repo/OracleLinux/OL7/addons/$basearch/
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-oracle
gpgcheck=1
enabled=0
```

그런 다음 입력합니다.

```bash
sudo yum update WALinuxAgent
```

일반적으로는 이렇게만 하면 되지만 어떤 이유로든 https://github.com에서 직접 설치해야 하는 경우 다음 단계를 사용하세요.

## <a name="install-wget"></a>wget 설치
SSH를 사용하여 VM에 로그인합니다.

명령줄에 `#sudo yum install wget`를 입력하여 wget를 설치합니다. Redhat, CentOS, Oracle Linux 버전 6.4 및 6.5와 같이 기본적으로 이 방법으로 설치되지 않는 몇 가지 배포판이 있습니다.

## <a name="download-the-latest-version"></a>최신 버전 다운로드
웹 페이지에서 [GitHub의 Azure Linux 에이전트 릴리스](https://github.com/Azure/WALinuxAgent/releases) 를 열고 최신 버전 번호를 확인합니다. `#waagent --version`을 입력하면 현재 버전을 찾을 수 있습니다.

### <a name="for-version-20x-type"></a>버전 2.0.x의 경우 다음을 입력합니다.

```bash
wget https://raw.githubusercontent.com/Azure/WALinuxAgent/WALinuxAgent-[version]/waagent
```

예를 들어 다음 줄에서는 버전 2.0.14를 사용합니다.

```bash
wget https://raw.githubusercontent.com/Azure/WALinuxAgent/WALinuxAgent-2.0.14/waagent
```

### <a name="for-version-21x-or-later-type"></a>2.1.x 이상 버전의 경우 다음을 입력합니다.
```bash
wget https://github.com/Azure/WALinuxAgent/archive/WALinuxAgent-[version].zip
unzip WALinuxAgent-[version].zip
cd WALinuxAgent-[version]
```

예를 들어 다음 줄에서는 버전 2.1.0을 사용합니다.

```bash
wget https://github.com/Azure/WALinuxAgent/archive/WALinuxAgent-2.1.0.zip
unzip WALinuxAgent-2.1.0.zip  
cd WALinuxAgent-2.1.0
```

## <a name="install-the-azure-linux-agent"></a>Azure Linux 에이전트를 설치합니다.
### <a name="for-version-20x-use"></a>버전 2.0.x의 경우 다음을 사용합니다.
waagent 실행 파일 만들기:

```bash
chmod +x waagent
```

/usr/sbin/에 새 실행 파일을 복사합니다.

대부분의 Linux에서는 다음을 사용합니다.

```bash
sudo cp waagent /usr/sbin
```

CoreOS의 경우 다음을 사용합니다.

```bash
sudo cp waagent /usr/share/oem/bin/
```

Azure Linux 에이전트를 새로 설치하는 경우 다음을 실행합니다.

```bash
sudo /usr/sbin/waagent -install -verbose
```

### <a name="for-version-21x-use"></a>버전 2.1.x의 경우 다음을 사용합니다.
`setuptools` 패키지를 먼저 설치해야 할 수도 있습니다. [여기](https://pypi.python.org/pypi/setuptools)를 참조하세요. 그런 후 다음을 실행합니다.

```bash
sudo python setup.py install
```

## <a name="restart-the-waagent-service"></a>waagent 서비스 다시 시작
대부분의 linux 배포판:

```bash
sudo service waagent restart
```

Ubuntu의 경우 다음을 사용합니다.

```bash
sudo service walinuxagent restart
```

CoreOS의 경우 다음을 사용합니다.

```bash
sudo systemctl restart waagent
```

## <a name="confirm-the-azure-linux-agent-version"></a>Azure Linux 에이전트 버전 확인
    
```bash
waagent -version
```

CoreOS에서는 위의 명령이 작동하지 않을 수 있습니다.

Azure Linux 에이전트 버전이 새 버전으로 업데이트된 것을 확인할 수 있습니다.

Azure Linux 에이전트에 대한 자세한 내용은 [Azure Linux 에이전트 추가 정보](https://github.com/Azure/WALinuxAgent)를 참조하세요.


