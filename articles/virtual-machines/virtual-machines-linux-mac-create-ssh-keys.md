<properties
	pageTitle="Linux 및 Mac에서 SSH 키 만들기 | Microsoft Azure"
	description="Azure의 리소스 관리자 및 클래식 배포 모델을 사용할 수 있도록 Linux 및 Mac에서 SSH 키를 생성하여 사용합니다."
	services="virtual-machines-linux"
	documentationCenter=""
	authors="vlivech"
	manager="timlt"
	editor=""
	tags="" />

<tags
	ms.service="virtual-machines-linux"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="08/08/2016"
	ms.author="v-livech"/>

# Azure의 Linux VM용 Linux 및 Mac에서 SSH 키 만들기

SSH 키 쌍을 사용하면 인증을 위해 기본적으로 SSH 키를 사용하는 Azure에서 가상 컴퓨터를 만들 수 있으며 로그인하기 위해 암호가 필요하지 않게 됩니다. 암호는 추측할 수 있으며 철저한 무차별 암호 대입 시도로 인해 VM을 오픈하여 암호를 추측합니다. Azure 템플릿을 사용하여 만든 VM 또는 `azure-cli`는 배포의 일부로 SSH 공개 키를 포함할 수 있으며 게시 배포 구성을 제거합니다. Windows에서 Linux VM에 연결하는 경우 [이 문서](virtual-machines-linux-ssh-from-windows.md)를 참조하세요.

## 빠른 명령 나열

다음 명령 예제에서 &lt;와 &gt; 사이의 값을 사용자 환경의 값으로 바꿉니다.

```bash
ssh-keygen -t rsa -b 2048 -C "<your_user@yourdomain.com>"
```

`~/.ssh/` 디렉터리에 저장될 파일의 이름을 입력합니다.

```bash
<azure_fedora_id_rsa>
```

azure\_fedora\_id\_rsa에 대한 암호를 입력합니다.

```bash
<correct horse battery staple>
```

Linux 및 Mac에서 새로 만든 키를 `ssh-agent`에 추가합니다(OSX Keychain에도 추가).

```bash
eval "$(ssh-agent -s)"
ssh-add ~/.ssh/azure_fedora_id_rsa
```

Linux 서버에 SSH 공개 키를 복사합니다.

```bash
ssh-copy-id -i ~/.ssh/azure_fedora_id_rsa.pub <youruser@yourserver.com>
```

암호 대신 키를 사용하여 로그인을 테스트합니다.

```bash
ssh -o PreferredAuthentications=publickey -o PubkeyAuthentication=yes -i ~/.ssh/azure_fedora_id_rsa <youruser@yourserver.com>
Last login: Tue April 12 07:07:09 2016 from 66.215.22.201
$
```

## 소개

SSH 공개 키와 개인 키를 사용하는 것이 Linux 서버에 로그인하는 가장 쉬운 방법입니다. [공개 키 암호화](https://en.wikipedia.org/wiki/Public-key_cryptography)는 암호보다 더 안전하게 Azure의 Linux 또는 BSD VM에 로그인하는 방법을 제공하며 무차별 암호 대입을 훨씬 더 쉽게 수행할 수 있습니다. 다른 사람과 공개 키를 공유할 수 있지만 사용자(또는 로컬 보안 인프라)만이 개인 키를 소유합니다. SSH 개인 키에는 해당 키를 보호할 [매우 안전한 암호](https://www.xkcd.com/936/)(원본:[xkcd.com](https://xkcd.com))가 있어야 합니다. 이 암호는 개인 SSH 키에 액세스하기 위한 것으로 사용자 계정 암호가 **아닙니다**. SSH 키에 암호를 추가하면 잠금 해제하는 데 암호가 없으면 개인 키를 사용할 수 없도록 개인 키를 암호화합니다. 공격자가 개인 키를 훔치고 해당 키에 암호가 없는 경우 해당 개인 키를 사용하여 해당하는 공개 키가 있는 서버에 로그인할 수 있습니다. 개인 키가 암호로 보호된 경우 공격자가 사용할 수 없어 Azure에서 인프라에 대해 보안 계층을 추가로 제공합니다.



이 문서에서는 Resource Manager에 대한 배포에 권장하는 *ssh-rsa* 서식 키 파일을 만듭니다. *ssh-rsa* 키는 클래식 및 Resource Manager 배포에 대한 [포털](https://portal.azure.com)에 필요합니다.


## SSH 키 만들기

Azure는 최소한 2048비트, ssh rsa 형식 공개 및 개인 키 서식이 필요합니다. 키를 만들려면 일련의 사항을 질문한 다음 개인 키와 일치하는 공개 키를 작성하는 `ssh-keygen`을 사용합니다. Azure VM을 만드는 경우 공개 키는 `~/.ssh/authorized_keys`에 복사됩니다. `~/.ssh/authorized_keys`에서 SSH 키는 SSH 로그인 연결에 대한 해당 개인 키를 일치하도록 클라이언트의 문제를 해결하는 데 사용됩니다.


## ssh-keygen 사용

이 명령은 2048비트 RSA를 사용하여 암호 보안된(암호화된) SSH 키 쌍을 만들고 쉽게 식별할 수 있도록 주석 처리됩니다.

```bash
ssh-keygen -t rsa -b 2048 -C "ahmet@fedoraVMAzure"
```

_설명된 명령_

`ssh-keygen` = 키를 만드는 데 사용한 프로그램

`-t rsa` = 만들려는 [RSA 서식](https://en.wikipedia.org/wiki/RSA_(cryptosystem)인 키의 유형

`-b 2048` = 키의 비트

`-C "ahmet@fedoraVMAzure"` = 쉽게 식별할 수 있도록 공개 키 파일의 끝에 추가된 주석 일반적으로 전자 메일은 주석으로 사용되지만 인프라에 가장 적합한 것을 사용할 수 있습니다.

### PEM 키 사용

클래식 배포 모델(Azure 클래식 포털 또는 Azure 서비스 관리 CLI `asm`)을 사용하는 경우 PEM 서식 SSH 키를 사용하여 Linux VM에 액세스해야 할 수도 있습니다. 기존 SSH 공개 키 및 기존 x509 인증서에서 PEM 키를 만드는 방법은 다음과 같습니다.

기존 SSH 공개 키에서 PEM 서식 키를 만들려면:

```bash
ssh-keygen -f id_rsa.pub -m 'PEM' -e > id_rsa.pem
```

## ssh-keygen 연습

자세히 설명한 각 단계: `ssh-keygen`을 실행하여 시작합니다.

```bash
ssh-keygen -t rsa -b 2048 -C "ahmet@fedoraVMAzure"
Generating public/private rsa key pair.
Enter file in which to save the key (/home/ahmet/.ssh/id_rsa): azure_fedora_id_rsa
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
Your identification has been saved in azure_fedora_id_rsa.
Your public key has been saved in azure_fedora_id_rsa.pub.
The key fingerprint is:
14:a3:cb:3e:78:ad:25:cc:55:e9:0c:08:e5:d1:a9:08 ahmet@fedoraVMAzure
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

`Enter file in which to save the key (/home/ahmet/.ssh/id_rsa): azure_fedora_id_rsa`

이 문서에 대한 키 쌍 이름. **id\_rsa**라는 키 쌍을 기본값으로 가지고 일부 도구는 **id\_rsa** 개인 키 파일 이름을 예상하므로 하나 있는 것이 좋습니다. 디렉터리 `~/.ssh/`은 SSH 키 쌍 및 SSH 구성 파일에 대한 기본 위치입니다.

```bash
ahmet@fedora$ ls -al ~/.ssh
-rw------- 1 ahmet staff  1675 Aug 25 18:04 azure_fedora_id_rsa
-rw-r--r-- 1 ahmet staff   410 Aug 25 18:04 azure_fedora_id_rsa.pub
```
`~/.ssh` 디렉터리의 목록입니다. `ssh-keygen`가 올바른 소유권 및 파일 모드를 나타내고 설정하지 않는 경우 `~/.ssh` 디렉터리를 만듭니다.

키 암호:

`Enter passphrase (empty for no passphrase):`

`ssh-keygen`는 "암호"인 암호를 나타냅니다. 키 쌍에 암호를 추가 하는 것이 *가장* 좋습니다. 키 쌍을 보호하는 암호 없이 누구나 해당 공개 키가 있는 서버에 로그인하는 데 개인 키 파일을 사용할 수 있습니다. 암호를 추가하면 보호 기능을 제공합니다. 이 경우에 개인 키 파일에 대한 액세스 권한을 얻을 수 있으며 인증하는 데 사용한 키를 변경할 시간을 제공합니다.

## ssh 에이전트를 사용하여 개인 키 암호 저장

모든 SSH 로그인으로 개인 키 파일 암호를 입력하지 않으려면 `ssh-agent`을 사용하여 개인 키 파일 암호를 캐시합니다. Mac을 사용하는 경우 `ssh-agent`를 호출할 때 OSX 키 집합은 개인 키 암호를 안전하게 저장합니다.

먼저 `ssh-agent`이 실행 중인지 확인합니다.

```bash
eval "$(ssh-agent -s)"
```

이제 `ssh-add` 명령을 사용하여 개인 키를 `ssh-agent`에 추가합니다.

```bash
ssh-add ~/.ssh/azure_fedora_id_rsa
```

개인 키 암호는 이제 `ssh-agent`에 저장됩니다.

## SSH 구성 파일 만들기 및 구성

SSH 클라이언트 동작을 최적화하기 위해 `~/.ssh/config` 파일을 만들고 구성하여 로그인 속도를 높이는 것이 좋습니다.

다음 예제에서는 표준 구성을 보여줍니다.

### 파일 만들기

```bash
touch ~/.ssh/config
```

### 파일을 편집하여 새 SSH 구성 추가

```bash
vim ~/.ssh/config
```

### 예제 `~/.ssh/config` 파일:

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


### 설명된 구성 파일

`Host` = 터미널에서 호출되는 호스트의 이름. `ssh fedora22`는 `SSH`이 레이블이 `Host fedora22`로 지정된 설정 블록에서 값을 사용하도록 지시합니다. 참고: 사용하기 위한 논리적인 레이블일 수 있고 어떤 서버의 실제 호스트 이름을 나타내지 않습니다.

`Hostname 102.160.203.241` = 액세스된 서버에 대한 IP 주소 또는 DNS 이름.

`User git` = 사용할 원격 사용자 계정.

`PubKeyAuthentication yes` = SSH가 로그인에 SSH 키를 사용하도록 지시합니다.

`IdentityFile /home/ahmet/.ssh/id_id_rsa` = 인증을 위해 사용할 SSH 개인 키와 해당 공개 키.


## 암호 없이 Linux에 SSH

SSH 키 쌍 및 구성된 SSH 구성 파일이 있으므로 빠르고 안전하게 Linux VM에 로그인할 수 있습니다. 처음으로 SSH 키를 사용하는 서버에 로그인하면 명령은 해당 키 파일을 입력하라는 메시지를 표시합니다.

```bash
ssh fedora22
```

### 설명된 명령

`ssh fedora22`이 SSH를 실행하는 경우 `Host fedora22` 블록에서 설정을 배치하고 로드한 다음 마지막 블록 `Host *`의 나머지 모든 설정을 로드합니다.

## 다음 단계

다음으로 새 SSH 공개 키를 사용하여 Azure Linux VM을 만듭니다. 기본 로그인 메서드 암호를 사용하여 만든 VM보다 SSH 공개 키를 로그인으로 사용하여 만든 Azure VM의 보안이 뛰어납니다. SSH 키를 사용하여 만든 Azure VM은 기본적으로 비활성화된 암호를 사용하여 구성되고 강제 무차별 암호 추측 시도를 방지합니다.

- [Azure 템플릿을 사용하여 보안 Linux VM 만들기](virtual-machines-linux-create-ssh-secured-vm-from-template.md)
- [Azure 포털을 사용하여 보안 Linux VM 만들기](virtual-machines-linux-quick-create-portal.md)
- [Azure CLI를 사용하여 보안 Linux VM 만들기](virtual-machines-linux-quick-create-cli.md)

<!---HONumber=AcomDC_0831_2016-->