---
title: "Linux 및 Mac에서 SSH 키 만들기 | Microsoft Docs"
description: "Azure의 리소스 관리자 및 클래식 배포 모델을 사용할 수 있도록 Linux 및 Mac에서 SSH 키를 생성하여 사용합니다."
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
ms.date: 10/25/2016
ms.author: v-livech
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 689445bc656b5cdebc7689f7fec6e2ea2683576e


---
# <a name="create-ssh-keys-on-linux-and-mac-for-linux-vms-in-azure"></a>Azure의 Linux VM용 Linux 및 Mac에서 SSH 키 만들기
SSH 키 쌍을 사용하면 인증을 위해 기본적으로 SSH 키를 사용하는 Azure에서 가상 컴퓨터를 만들 수 있으며 로그인하기 위해 암호가 필요하지 않게 됩니다.  암호는 추측할 수 있으며 철저한 무차별 암호 대입 시도로 인해 VM을 오픈하여 암호를 추측합니다. Azure 템플릿을 사용하여 만든 VM 또는 `azure-cli` 는 배포의 일부로 SSH 공개 키를 포함할 수 있으며 게시 배포 구성을 제거합니다.  Windows에서 Linux VM에 연결하는 경우 [이 문서](virtual-machines-linux-ssh-from-windows.md)

이 문서의 내용을 실행하기 위해 필요한 사항:

* Azure 계정([무료 평가판 받기](https://azure.microsoft.com/pricing/free-trial/))
* `azure login`으로 로그인된 [Azure CLI](../xplat-cli-install.md)
* Azure Resource Manager 모드 `azure config mode arm`으로 *있어야 하는* Azure CLI

## <a name="quick-commands"></a>빠른 명령
다음 명령에서 사용자가 선택한 예제로 바꿉니다.

SSH 키는 기본적으로 `.ssh` 디렉터리에 보관됩니다.  

```bash
cd ~/.ssh/
```

`~/.ssh` 디렉터리가 없는 경우 적절한 권한이 있는 사용자가 `ssh-keygen` 명령으로 해당 디렉터리를 만듭니다.

```bash
ssh-keygen -t rsa -b 2048 -C "myusername@myserver"
```

`~/.ssh/` 디렉터리에 저장될 파일의 이름을 입력합니다.

```bash
id_rsa
```

id_rsa에 대한 암호를 다음과 같이 입력합니다.

```bash
correct horse battery staple
```

이제는 `~/.ssh` 디렉터리에 `id_rsa` 및 `id_rsa.pub` SSH 키 쌍이 있습니다.

```bash
ls -al ~/.ssh
```

Linux 및 Mac에서 새로 만든 키를 `ssh-agent`에 추가합니다(OSX Keychain에도 추가).

```bash
eval "$(ssh-agent -s)"
ssh-add ~/.ssh/id_rsa
```

Linux 서버에 SSH 공개 키를 복사합니다.

```bash
ssh-copy-id -i ~/.ssh/id_rsa.pub myusername@myserver
```

암호 대신 키를 사용하여 로그인을 테스트합니다.

```bash
ssh -o PreferredAuthentications=publickey -o PubkeyAuthentication=yes -i ~/.ssh/id_rsa myusername@myserver
Last login: Tue April 12 07:07:09 2016 from 66.215.22.201
$
```

## <a name="detailed-walkthrough"></a>자세한 연습
SSH 공개 키와 개인 키를 사용하는 것이 Linux 서버에 로그인하는 가장 쉬운 방법입니다. [공개 키 암호화](https://en.wikipedia.org/wiki/Public-key_cryptography) 는 암호보다 더 안전하게 Azure의 Linux 또는 BSD VM에 로그인하는 방법을 제공하며 무차별 암호 대입을 훨씬 더 쉽게 수행할 수 있습니다. 다른 사람과 공개 키를 공유할 수 있지만 사용자(또는 로컬 보안 인프라)만이 개인 키를 소유합니다.  SSH 개인 키에는 해당 키를 보호할 [매우 안전한 암호](https://www.xkcd.com/936/)(원본:[xkcd.com](https://xkcd.com))가 있어야 합니다.  이 암호는 개인 SSH 키에 액세스하기 위한 것으로 사용자 계정 암호가 **아닙니다** .  SSH 키에 암호를 추가하면 잠금 해제하는 데 암호가 없으면 개인 키를 사용할 수 없도록 개인 키를 암호화합니다.  공격자가 개인 키를 훔치고 해당 키에 암호가 없는 경우 해당 개인 키를 사용하여 해당하는 공개 키가 있는 서버에 로그인할 수 있습니다.  개인 키가 암호로 보호된 경우 공격자가 사용할 수 없어 Azure에서 인프라에 대해 보안 계층을 추가로 제공합니다.

이 문서에서는 Resource Manager에 대한 배포에 권장하는 *ssh-rsa* 서식 키 파일을 만듭니다.  *ssh-rsa* 키는 클래식 및 Resource Manager 배포에 대한 [포털](https://portal.azure.com) 에 필요합니다.

## <a name="create-the-ssh-keys"></a>SSH 키 만들기
Azure는 최소한 2048비트, ssh rsa 형식 공개 및 개인 키 서식이 필요합니다. 키를 만들려면 일련의 사항을 질문한 다음 개인 키와 일치하는 공개 키를 작성하는 `ssh-keygen`을 사용합니다. Azure VM을 만드는 경우 공개 키는 `~/.ssh/authorized_keys`에 복사됩니다.  `~/.ssh/authorized_keys` 에서 SSH 키는 SSH 로그인 연결에 대한 해당 개인 키를 일치하도록 클라이언트의 문제를 해결하는 데 사용됩니다.

## <a name="using-sshkeygen"></a>ssh-keygen 사용
이 명령은 2048비트 RSA를 사용하여 암호 보안된(암호화된) SSH 키 쌍을 만들고 쉽게 식별할 수 있도록 주석 처리됩니다.  

모든 ssh 키를 디렉터리에서 생성하도록 해당 디렉터리로 변경하여 시작합니다.

```bash
cd ~/.ssh
```

`~/.ssh` 디렉터리가 없는 경우 적절한 권한이 있는 사용자가 `ssh-keygen` 명령으로 해당 디렉터리를 만듭니다.

```bash
ssh-keygen -t rsa -b 2048 -C "myusername@myserver"
```

*설명된 명령*

`ssh-keygen` = 키를 만드는 데 사용한 프로그램

`-t rsa` = 작성하는 키의 유형인 [RSA 형식](https://en.wikipedia.org/wiki/RSA_(cryptosystem)

`-b 2048` = 키의 비트

`-C "myusername@myserver"` = 쉽게 식별할 수 있도록 공개 키 파일의 끝에 추가된 주석  일반적으로 전자 메일은 주석으로 사용되지만 인프라에 가장 적합한 것을 사용할 수 있습니다.

### <a name="using-pem-keys"></a>PEM 키 사용
클래식 배포 모델(Azure 클래식 포털 또는 Azure 서비스 관리 CLI `asm`)을 사용하는 경우 PEM 서식 SSH 키를 사용하여 Linux VM에 액세스해야 할 수도 있습니다.  기존 SSH 공개 키 및 기존 x509 인증서에서 PEM 키를 만드는 방법은 다음과 같습니다.

기존 SSH 공개 키에서 PEM 서식 키를 만들려면:

```bash
ssh-keygen -f ~/.ssh/id_rsa.pub -e > ~/.ssh/id_ssh2.pem
```

## <a name="example-of-sshkeygen"></a>ssh-keygen 예제
```bash
ssh-keygen -t rsa -b 2048 -C "myusername@myserver"
Generating public/private rsa key pair.
Enter file in which to save the key (/home/myusername/.ssh/id_rsa): id_rsa
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
Your identification has been saved in id_rsa.
Your public key has been saved in id_rsa.pub.
The key fingerprint is:
14:a3:cb:3e:78:ad:25:cc:55:e9:0c:08:e5:d1:a9:08 myusername@myserver
The key's randomart image is:
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

`Enter file in which to save the key (/home/myusername/.ssh/id_rsa): id_rsa`

이 문서에 대한 키 쌍 이름.  **id_rsa**라는 키 쌍을 기본값으로 가지고 일부 도구는 **id_rsa** 개인 키 파일 이름을 예상하므로 하나 있는 것이 좋습니다. 디렉터리 `~/.ssh/` 은 SSH 키 쌍 및 SSH 구성 파일에 대한 기본 위치입니다.

```bash
ls -al ~/.ssh
-rw------- 1 myusername staff  1675 Aug 25 18:04 id_rsa
-rw-r--r-- 1 myusername staff   410 Aug 25 18:04 rsa.pub
```
`~/.ssh` 디렉터리의 목록입니다. `ssh-keygen`가 올바른 소유권 및 파일 모드를 나타내고 설정하지 않는 경우 `~/.ssh` 디렉터리를 만듭니다.

키 암호:

`Enter passphrase (empty for no passphrase):`

`ssh-keygen`는 "암호"인 암호를 나타냅니다.  키 쌍에 암호를 추가하는 것이 *가장* 좋습니다. 키 쌍을 보호하는 암호 없이 누구나 해당 공개 키가 있는 서버에 로그인하는 데 개인 키 파일을 사용할 수 있습니다. 암호를 추가하면 보호 기능을 제공합니다. 이 경우에 개인 키 파일에 대한 액세스 권한을 얻을 수 있으며 인증하는 데 사용한 키를 변경할 시간을 제공합니다.

## <a name="using-sshagent-to-store-your-private-key-password"></a>ssh 에이전트를 사용하여 개인 키 암호 저장
모든 SSH 로그인으로 개인 키 파일 암호를 입력하지 않으려면 `ssh-agent` 을 사용하여 개인 키 파일 암호를 캐시합니다. Mac을 사용하는 경우 `ssh-agent`를 호출할 때 OSX 키 집합은 개인 키 암호를 안전하게 저장합니다.

먼저 `ssh-agent`이 실행 중인지 확인합니다.

```bash
eval "$(ssh-agent -s)"
```

이제 `ssh-add` 명령을 사용하여 개인 키를 `ssh-agent`에 추가합니다.

```bash
ssh-add ~/.ssh/id_rsa
```

개인 키 암호는 이제 `ssh-agent`에 저장됩니다.

## <a name="create-and-configure-an-ssh-config-file"></a>SSH 구성 파일 만들기 및 구성
SSH 클라이언트 동작을 최적화하기 위해 `~/.ssh/config` 파일을 만들고 구성하여 로그인 속도를 높이는 것이 좋습니다.

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
  User myusername
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
`Host` = 터미널에서 호출되는 호스트의 이름.  `ssh fedora22`는 레이블이 `Host fedora22`로 지정된 설정 블록에서 값을 사용하도록 `SSH`에 지시합니다. 참고: 사용하기 위한 논리적인 레이블일 수 있고 어떤 서버의 실제 호스트 이름을 나타내지 않습니다.

`Hostname 102.160.203.241` = 액세스된 서버에 대한 IP 주소 또는 DNS 이름.

`User myusername` = 서버에 로그인할 때 사용할 원격 사용자 계정

`PubKeyAuthentication yes` = SSH가 로그인에 SSH 키를 사용하도록 지시합니다.

`IdentityFile /home/myusername/.ssh/id_id_rsa` = 인증을 위해 사용할 SSH 개인 키와 해당 공개 키.

## <a name="ssh-into-linux-without-a-password"></a>암호 없이 Linux에 SSH
SSH 키 쌍 및 구성된 SSH 구성 파일이 있으므로 빠르고 안전하게 Linux VM에 로그인할 수 있습니다. 처음으로 SSH 키를 사용하는 서버에 로그인하면 명령은 해당 키 파일을 입력하라는 메시지를 표시합니다.

```bash
ssh fedora22
```

### <a name="command-explained"></a>설명된 명령
`ssh fedora22`이 SSH를 실행하는 경우 `Host fedora22` 블록에서 설정을 배치하고 로드한 다음 마지막 블록 `Host *`의 나머지 모든 설정을 로드합니다.

## <a name="next-steps"></a>다음 단계
다음으로 새 SSH 공개 키를 사용하여 Azure Linux VM을 만듭니다.  기본 로그인 메서드 암호를 사용하여 만든 VM보다 SSH 공개 키를 로그인으로 사용하여 만든 Azure VM의 보안이 뛰어납니다.  SSH 키를 사용하여 만든 Azure VM은 기본적으로 비활성화된 암호를 사용하여 구성되고 강제 무차별 암호 추측 시도를 방지합니다.

* [Azure 템플릿을 사용하여 보안 Linux VM 만들기](virtual-machines-linux-create-ssh-secured-vm-from-template.md)
* [Azure 포털을 사용하여 보안 Linux VM 만들기](virtual-machines-linux-quick-create-portal.md)
* [Azure CLI를 사용하여 보안 Linux VM 만들기](virtual-machines-linux-quick-create-cli.md)




<!--HONumber=Nov16_HO2-->


