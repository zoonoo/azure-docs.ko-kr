---
title: "Azure에서 Linux VM용 SSH 키 쌍 만들기 | Microsoft Docs"
description: "Azure Linux VM을 위한 SSH 공개 및 개인 키 쌍을 안전하게 만듭니다."
services: virtual-machines-linux
documentationcenter: 
author: vlivech
manager: timlt
editor: 
tags: 
ms.assetid: 34ae9482-da3e-4b2d-9d0d-9d672aa42498
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: get-started-article
ms.date: 03/08/2017
ms.author: rasquill
experiment_id: rasquill-ssh-20170308
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: 19acd4efca7ef043f31b436b96f9129caee9591b
ms.lasthandoff: 04/03/2017



---

# <a name="create-an-ssh-public-and-private-key-pair-for-linux-vms"></a>Linux VM에 대한 SSH 공용 및 개인 키 쌍 만들기

이 문서에서는 Linux VM에 사용할 SSH 프로토콜 버전 2 RSA 공개 및 개인 키 파일 쌍을 생성하는 방법을 보여줍니다.  SSH 키 쌍을 사용하면 인증을 위해 기본적으로 SSH 키를 사용하는 Virtual Machines를 Azure에서 만들 수 있으며 로그인하기 위해 암호가 필요하지 않게 됩니다.  암호는 추측할 수 있으며 철저한 무차별 암호 대입 시도로 인해 VM을 오픈하여 암호를 추측합니다. Azure 템플릿 또는 `azure-cli`을 사용하여 만든 VM은 배포의 일부로 SSH 공개 키를 포함할 수 있으며 SSH에 대한 암호 로그인을 사용하지 않도록 설정하는 게시 배포 구성 단계를 제거합니다.

## <a name="quick-commands"></a>빠른 명령

Bash 셸에서 다음 명령을 실행하여 사용자가 선택한 예제로 바꿉니다.

SSH 공개 키 파일은 기본적으로 `~/.ssh/id_rsa.pub`에 생성됩니다. 다음 명령을 사용하여 메시지가 표시되면 개인 키를 보호하기 위한 "암호"를 만들어야 합니다. (이 암호는 개인 키를 암호화하는 데 사용되는 암호입니다.)

```bash
ssh-keygen -t rsa -b 2048 
```

새로 만든 키를 `ssh-agent`에 추가합니다.

```bash
ssh-add ~/.ssh/id_rsa
```

> [!IMPORTANT] 
> 위의 명령은 거의 모든 배포판의 Linux 운영 체제에서 작동하지만 환경이 크게 제한될 수 있으므로 컨테이너에서 반드시 작동하지 않습니다. 

## <a name="detailed-walkthrough"></a>자세한 연습

SSH 공개 키와 개인 키를 사용하는 것이 Linux 서버에 로그인하는 가장 쉬운 방법입니다. [공개 키 암호화](https://en.wikipedia.org/wiki/Public-key_cryptography) 는 암호보다 더 안전하게 Azure의 Linux 또는 BSD VM에 로그인하는 방법을 제공하며 무차별 암호 대입을 훨씬 더 쉽게 수행할 수 있습니다.

> [!IMPORTANT]
> 다른 사람과 공개 키를 공유할 수 있지만 사용자(또는 로컬 보안 인프라)만이 개인 키를 소유합니다.  SSH 개인 키에는 해당 키를 보호할 [매우 안전한 암호](https://www.xkcd.com/936/)(출처: [xkcd.com](https://xkcd.com))가 있어야 합니다.  이 암호는 개인 SSH 키에 액세스하기 위한 것으로 사용자 계정 암호가 **아닙니다** .  SSH 키에 암호를 추가하면 128비트 AES를 사용하여 개인 키를 암호화하므로 암호가 없는 개인 키는 해독하는 데 쓸모가 없습니다.  공격자가 개인 키를 훔치고 해당 키에 암호가 없는 경우 해당 개인 키를 사용하여 해당하는 공개 키가 있는 서버에 로그인할 수 있습니다.  개인 키가 암호로 보호된 경우 공격자가 사용할 수 없어 Azure에서 인프라에 대해 보안 계층을 추가로 제공합니다.

이 문서에서는 Resource Manager에 대한 배포에 권장되는 SSH 프로토콜 버전 2 RSA 공개 및 개인 키 파일을 만듭니다.  클래식 및 Resource Manager 배포를 위해 *ssh-rsa* 키가 [포털](https://portal.azure.com)에 필요합니다.

## <a name="disable-ssh-passwords-by-using-ssh-keys"></a>SSH 키를 사용하여 SSH 암호 비활성화

Azure는 최소한 2048비트, ssh rsa 형식 공개 및 개인 키 서식이 필요합니다. 키를 만들려면 일련의 사항을 질문한 다음 개인 키와 일치하는 공개 키를 작성하는 `ssh-keygen`을 사용합니다. Azure VM을 만드는 경우 공개 키는 `~/.ssh/authorized_keys`에 복사됩니다.  `~/.ssh/authorized_keys` 에서 SSH 키는 SSH 로그인 연결에 대한 해당 개인 키를 일치하도록 클라이언트의 문제를 해결하는 데 사용됩니다.  인증을 위해 SSH 키를 사용하도록 Azure Linux VM이 만들어지면 Azure는 SSH 키만 허용하고 암호 로그인은 허용하지 않도록 SSHD 서버를 구성합니다.  따라서 SSH 키를 사용하여 Azure Linux VM을 만들면 VM 배포 보안을 유지하고 sshd_config 구성 파일에서 암호를 사용하지 않도록 설정하는 일반 사후 배포 구성 단계를 줄일 수 있습니다.

## <a name="using-ssh-keygen"></a>ssh-keygen 사용

이 명령은 2048비트 RSA를 사용하여 암호 보안된(암호화된) SSH 키 쌍을 만들고 쉽게 식별할 수 있도록 주석 처리됩니다.  

SSH 키는 기본적으로 `~/.ssh` 디렉터리에 보관됩니다.  `~/.ssh` 디렉터리가 없는 경우 적절한 권한이 있는 사용자가 `ssh-keygen` 명령으로 해당 디렉터리를 만듭니다.

```bash
ssh-keygen \
-t rsa \
-b 2048 
```

*설명된 명령*

`ssh-keygen` = 키를 만드는 데 사용한 프로그램

`-t rsa` = 만드는 키의 형식은 RSA 형식 [wikipedia](https://en.wikipedia.org/wiki/RSA_(cryptosystem)

`-b 2048` = 키의 비트


## <a name="classic-portal-and-x509-certs"></a>클래식 포털 및 X.509 인증서

Azure [클래식 포털](https://manage.windowsazure.com/)을 사용하는 경우 SSH 키에 X.509 인증서가 필요합니다.  다른 형식의 SSH 공개 키는 허용되지 않으며 *X.509 인증서여야 합니다*.

기존 SSH-RSA 개인 키에서 X.509 인증서를 만들려면 다음을 수행합니다.

```bash
openssl req -x509 \
-key ~/.ssh/id_rsa \
-nodes \
-days 365 \
-newkey rsa:2048 \
-out ~/.ssh/id_rsa.pem
```

## <a name="classic-deploy-using-asm"></a>`asm`을 사용하여 클래식 배포

클래식 배포 모델(`asm` Azure 서비스 관리 CLI)을 사용하는 경우 **.pem** 컨테이너에서 SSH-RSA 공개 키 또는 RFC4716 형식 키를 사용할 수 있습니다.  SSH-RSA 공개 키는 이 문서의 앞부분에서 `ssh-keygen`을 사용하여 작성한 키입니다.

기존 SSH 공개 키에서 RFC4716 형식 키를 만들려면 다음을 수행합니다.

```bash
ssh-keygen \
-f ~/.ssh/id_rsa.pub \
-e \
-m RFC4716 > ~/.ssh/id_ssh2.pem
```

## <a name="example-of-ssh-keygen"></a>ssh-keygen 예제

```bash
ssh-keygen -t rsa -b 2048 -C "ahmet@myserver"
Generating public/private rsa key pair.
Enter file in which to save the key (/home/ahmet/.ssh/id_rsa): 
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
Your identification has been saved in /home/ahmet/.ssh/id_rsa.
Your public key has been saved in /home/ahmet/.ssh/id_rsa.pub.
The key fingerprint is:
14:a3:cb:3e:78:ad:25:cc:55:e9:0c:08:e5:d1:a9:08 ahmet@myserver
The keys randomart image is:
+--[ RSA 2048]----+
|        o o. .   |
|      E. = .o    |
|      ..o...     |
|     . o....     |
|      o S =      |
|     . + O       |
|      + = =      |
|       o +       |
|        .        |
+-----------------+
```

저장된 키 파일:

`Enter file in which to save the key (/home/ahmet/.ssh/id_rsa): ~/.ssh/id_rsa`

이 문서에 대한 키 쌍 이름.  **id_rsa**라는 키 쌍을 기본값으로 가지고 일부 도구는 **id_rsa** 개인 키 파일 이름을 예상하므로 하나 있는 것이 좋습니다. 디렉터리 `~/.ssh/` 은 SSH 키 쌍 및 SSH 구성 파일에 대한 기본 위치입니다.  전체 경로를 지정하지 않으면 `ssh-keygen`에서 `~/.ssh` 기본 디렉터리가 아니라 현재 작업 디렉터리에 키를 만듭니다.

`~/.ssh` 디렉터리의 목록입니다.

```bash
ls -al ~/.ssh
-rw------- 1 ahmet staff  1675 Aug 25 18:04 id_rsa
-rw-r--r-- 1 ahmet staff   410 Aug 25 18:04 rsa.pub
```

키 암호:

`Enter passphrase (empty for no passphrase):`

`ssh-keygen`은 개인 키를 암호화하는 데 사용되는 암호를 "암호(passphrase)"로 나타냅니다.  키 쌍에 암호를 추가하는 것이 *가장* 좋습니다. 개인 키를 보호하는 암호가 없는 경우, 누구나 해당 공개 키가 있는 서버에 로그인하는 데 키 파일을 사용할 수 있습니다. 암호를 추가하면 보호 기능을 제공합니다. 이 경우에 개인 키 파일에 대한 액세스 권한을 얻을 수 있으며 인증하는 데 사용한 키를 변경할 시간을 제공합니다.

## <a name="using-ssh-agent-to-store-your-private-key-password"></a>ssh 에이전트를 사용하여 개인 키 암호 저장

모든 SSH 로그인으로 개인 키 파일 암호를 입력하지 않으려면 `ssh-agent` 을 사용하여 개인 키 파일 암호를 캐시합니다. Mac을 사용하는 경우 `ssh-agent`를 호출할 때 OSX 키 집합은 개인 키 암호를 안전하게 저장합니다.

`ssh-agent` 및 `ssh-add`를 확인하고 사용하여 암호를 대화형으로 사용할 필요가 없도록 SSH 시스템에 키 파일을 알려줍니다.

```bash
eval "$(ssh-agent -s)"
```

이제 `ssh-add` 명령을 사용하여 개인 키를 `ssh-agent`에 추가합니다.

```bash
ssh-add ~/.ssh/id_rsa
```

개인 키 암호는 이제 `ssh-agent`에 저장됩니다.

## <a name="using-ssh-copy-id-to-install-the-new-key"></a>`ssh-copy-id`를 사용하여 새 키 설치
VM을 이미 만든 경우 다음 명령을 사용하여 Linux VM에 새 SSH 공개 키를 설치할 수 있습니다. 이때 VM 사용자 이름 및 서버 주소를 사용자 고유의 값으로 바꿉니다.

```bash
ssh-copy-id -i ~/.ssh/id_rsa.pub ahmet@myserver
```

## <a name="create-and-configure-an-ssh-config-file"></a>SSH 구성 파일 만들기 및 구성

SSH 클라이언트 동작을 최적화하기 위해 `~/.ssh/config` 파일을 만들고 구성하여 로그인 속도를 높이는 것이 가장 좋습니다.

다음 예제에서는 표준 구성을 보여줍니다.

### <a name="create-the-file"></a>파일 만들기

```bash
touch ~/.ssh/config
```

### <a name="edit-the-file-to-add-the-new-ssh-configuration"></a>파일을 편집하여 새 SSH 구성 추가

```bash
vim ~/.ssh/config
```

### <a name="example-sshconfig-file"></a>예제 `~/.ssh/config` 파일:

```bash
# Azure Keys
Host fedora22
  Hostname 102.160.203.241
  User ahmet
# ./Azure Keys
# Default Settings
Host *
  PubkeyAuthentication=yes
  IdentitiesOnly=yes
  ServerAliveInterval=60
  ServerAliveCountMax=30
  ControlMaster auto
  ControlPath ~/.ssh/SSHConnections/ssh-%r@%h:%p
  ControlPersist 4h
  IdentityFile ~/.ssh/id_rsa
```

이 SSH 구성은 각 서버에 대한 섹션을 제공하여 자체 전용 키 쌍을 각각 보유하도록 합니다. 상위 구성 파일에서 특정 호스트와 일치하지 않는 호스트에 대한 기본 설정(`Host *`)입니다.

### <a name="config-file-explained"></a>설명된 구성 파일

`Host` = 터미널에서 호출되는 호스트의 이름.  `ssh fedora22`는 레이블이 `Host fedora22`로 지정된 설정 블록에서 값을 사용하도록 `SSH`에 지시합니다. 참고: 호스트는 사용하기 위한 논리적인 레이블일 수 있고 어떤 서버의 실제 호스트 이름을 나타내지 않습니다.

`Hostname 102.160.203.241` = 액세스된 서버에 대한 IP 주소 또는 DNS 이름.

`User ahmet` = 서버에 로그인할 때 사용할 원격 사용자 계정.

`PubKeyAuthentication yes` = SSH가 로그인에 SSH 키를 사용하도록 지시합니다.

`IdentityFile /home/ahmet/.ssh/id_id_rsa` = 인증을 위해 사용할 SSH 개인 키와 해당 공개 키.

## <a name="ssh-into-linux-without-a-password"></a>암호 없이 Linux에 SSH

SSH 키 쌍 및 구성된 SSH 구성 파일이 있으므로 빠르고 안전하게 Linux VM에 로그인할 수 있습니다. 처음으로 SSH 키를 사용하는 서버에 로그인하면 명령은 해당 키 파일을 입력하라는 메시지를 표시합니다.

```bash
ssh fedora22
```

### <a name="command-explained"></a>설명된 명령

`ssh fedora22`이 SSH를 실행하는 경우 `Host fedora22` 블록에서 설정을 배치하고 로드한 다음 마지막 블록 `Host *`의 나머지 모든 설정을 로드합니다.

## <a name="next-steps"></a>다음 단계

다음으로 새 SSH 공개 키를 사용하여 Azure Linux VM을 만듭니다.  기본 로그인 메서드 암호를 사용하여 만든 VM보다 SSH 공개 키를 로그인으로 사용하여 만든 Azure VM의 보안이 뛰어납니다.  SSH 키를 사용하여 만든 Azure VM은 기본적으로 비활성화된 암호를 사용하여 구성되고 강제 무차별 암호 추측 시도를 방지합니다. 클래식 포털에서 사용과 같은 SSH 키 쌍 만들기에 도움이 필요하거나 추가 인증서가 필요한 경우 [SSH 키 쌍 및 인증서를 만드는 자세한 단계](create-ssh-keys-detailed.md)를 참조하세요.

* [Azure 템플릿을 사용하여 보안 Linux VM 만들기](create-ssh-secured-vm-from-template.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Azure 포털을 사용하여 보안 Linux VM 만들기](quick-create-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Azure CLI를 사용하여 보안 Linux VM 만들기](quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

