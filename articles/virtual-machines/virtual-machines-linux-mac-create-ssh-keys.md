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
	ms.date="04/12/2016"
	ms.author="v-livech"/>

# Azure의 Linux VM용 Linux 및 Mac에서 SSH 키 만들기

암호로 보호된 SSH 공용 및 개인 키를 만들려면 워크스테이션에서 터미널을 열어야 합니다. SSH 키를 가진 후 기본적으로 해당 키를 사용하여 새 VM을 만들거나 Azure CLI와 Azure 템플릿을 사용하여 기존 VM에 공개 키를 추가할 수 있습니다.

## 빠른 명령 나열

다음 명령 예제에서 &lt;와 &gt; 사이의 값을 사용자 환경의 값으로 바꿉니다.

```bash
[ahmet@fedora ~]$ ssh-keygen -t rsa -b 2048 -C "<your_user@yourdomain.com>"

#Enter the name of the file that will be saved in the `~/.ssh/` directory.
<azure_fedora_id_rsa>

#Enter passphrase for azure_fedora_id_rsa:
<correct horse battery staple>

#Add the newly created key to `ssh-agent` on Linux and Mac (also added to OSX Keychain).
[ahmet@fedora ~]$ eval "$(ssh-agent -s)"
[ahmet@fedora ~]$ ssh-add ~/.ssh/azure_fedora_id_rsa

#Copy the SSH public key to your Linux Server.
[ahmet@fedora ~]$ ssh-copy-id -i ~/.ssh/azure_fedora_id_rsa.pub <youruser@yourserver.com>

#Test the login using keys instead of a password.
[ahmet@fedora ~]$ ssh -o PreferredAuthentications=publickey -o PubkeyAuthentication=yes -i ~/.ssh/azure_fedora_id_rsa <youruser@yourserver.com>

Last login: Tue April 12 07:07:09 2016 from 66.215.22.201
[ahmet@fedora ~]$

```

## 소개

SSH 공개 키 및 개인 키를 사용하는 것은 Linux 서버에 로그인하는 가장 쉬운 방법이지만 [공개 키 암호화](https://en.wikipedia.org/wiki/Public-key_cryptography)도 암호보다 Azure의 Linux 또는 BSD VM에 로그인하는 훨씬 더 안전한 방법을 제공하며 이는 훨씬 더 쉽게 무차별 암호 대입 공격을 받을 수 있습니다. 다른 사람과 공개 키를 공유할 수 있지만 사용자(또는 로컬 보안 인프라)만이 개인 키를 소유합니다. 만든 SSH 개인 키는 이를 보호하는 [보안 암호](https://www.xkcd.com/936/)를 가지고 이 암호는 단지 개인 SSH 키에 액세스하기 위한 것이며 사용자 계정 암호가 **아닙니다**. 암호 없이 개인 키를 소유한 모든 사용자는 설치된 공개 키로 모든 서버에 액세스할 수 있습니다. 암호 없이 개인 키를 사용할 수 없습니다.


리소스 관리자에 대한 배포에 권장하고 클래식 및 리소스 관리자에 대한 [포털](https://portal.azure.com)에 필요한 파일처럼 이 문서에서는 *ssh-rsa* 서식 키 파일을 만듭니다.


## SSH 키 만들기

Azure는 최소한 2048비트, ssh rsa 형식 공개 및 개인 키 서식이 필요합니다. 쌍을 만들려면 일련의 사항을 질문한 다음 개인 키와 일치하는 공개 키를 작성하는 `ssh-keygen`을 사용합니다. Azure VM을 만들 때 공개 키 콘텐츠를 전달합니다. 이는 Linux VM에 복사되고 로컬로 사용되며 개인 키를 안전하게 저장하여 로그인할 경우 사용자를 인증합니다

### `ssh-keygen` 사용

이 명령은 2048비트 RSA를 사용하여 암호 보안된 SSH 키 쌍을 만들고 쉽게 식별할 수 있도록 주석 처리됩니다.

```
ahmet@fedora$ ssh-keygen -t rsa -b 2048 -C "ahmet@fedoraVMAzure"
```

##### 설명된 명령

`ssh-keygen` = 키를 만드는 데 사용한 프로그램

`-t rsa` = [RSA 형식](https://en.wikipedia.org/wiki/RSA_(cryptosystem))인 만들 키의 유형

`-b 2048` = 키의 비트

`-C "ahmet@fedoraVMAzure"` = 쉽게 식별할 수 있도록 공개 키 파일의 끝에 추가된 주석. 일반적으로 전자 메일은 주석으로 사용되지만 인프라에 가장 적합한 것을 사용할 수 있습니다.

#### `ssh-keygen`의 연습

```bash
ahmet@fedora$ ssh-keygen -t rsa -b 2048 -C "ahmet@fedoraVMAzure"
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

ahmet@fedora$ ls -al ~/.ssh
-rw------- 1 ahmet staff  1675 Aug 25 18:04 azure_fedora_id_rsa
-rw-r--r-- 1 ahmet staff   410 Aug 25 18:04 azure_fedora_id_rsa.pub
```

`Enter file in which to save the key (/home/ahmet/.ssh/id_rsa): azure_fedora_id_rsa` 이 문서에 대한 키 쌍 이름. **id\_rsa**라고 명명된 키 쌍을 보유한 것은 기본이며 일부 도구는 **id\_rsa** 개인 키 파일 이름을 예상하므로 하나 있는 편이 좋습니다.(`~/.ssh/`는 SSH 키 쌍 및 SSH 구성 파일에 대한 일반적인 기본 위치입니다.)

`Enter passphrase (empty for no passphrase):` 키 쌍에 암호를 추가 하는 것이 좋습니다(`ssh-keygen`에서 "암호"라고 함). 키 쌍을 보호하는 암호 없이 누구나 개인 키 파일의 복사본을 서버에 로그인하는 데 사용할 수 있습니다. 서버는 공개 키에 해당합니다. 따라서 암호를 추가하면 보호 기능을 제공합니다. 이 경우에 개인 키 파일에 대한 액세스 권한을 얻을 수 있으며 인증하는 데 사용한 키를 변경할 시간을 제공합니다.

`ahmet@fedora$ ls -al ~/.ssh` 새 키 쌍 및 해당 사용 권한을 보여줍니다. `ssh-keygen`는 올바른 소유권 및 파일 모드를 나타내고 설정하지 않는 경우에 `~/.ssh` 디렉터리를 만듭니다.

## ssh 에이전트를 사용하여 개인 키 암호 저장

모든 SSH 로그인으로 개인 키 파일 암호를 입력하지 않으려면 `ssh-agent`을(를) 사용하여 Linux VM에 대한 신속하고 안전한 로그인을 허용하는 개인 키 파일 암호를 캐시합니다. OSX를 사용하는 경우 `ssh-agent`을(를) 호출할 때 키 집합은 개인 키 암호를 안전하게 저장합니다.

먼저 `ssh-agent`이(가) 실행 중인지 확인합니다.

`[ahmet@fedora ~]$ eval "$(ssh-agent -s)"`

이제 `ssh-add` 명령을 사용하여 개인 키를 `ssh-agent`에 추가하면 다시 OSX에서 자격 증명을 저장하는 키 집합이 시작됩니다.

`[ahmet@fedora ~]$ ssh-add ~/.ssh/azure_fedora_id_rsa`

모든 SSH 로그인마다 키 암호를 입력하지 않아도 되도록 이제 개인 키 암호가 저장됩니다.

## SSH 구성 파일 만들기 및 구성

Linux VM을 시작하고 실행하는 동안 필수적이지 않지만 `~/.ssh/config` 파일을 만들고 구성하는 모범 사례입니다. 이렇게 하여 실수로 암호를 사용하여 VM에 로그온하고 다른 Azure VM에 다른 키 쌍을 사용하여 자동화하며 **git**와 같은 다른 프로그램을 구성하여 여러 서버를 대상으로 하지 않도록 방지합니다.

다음 예제에서는 표준 구성을 보여줍니다.

### 파일 만들기

```bash
ahmet@fedora$ touch ~/.ssh/config
```

### 파일을 편집하여 새 SSH 구성 추가

```bash
ahmet@fedora$ vim ~/.ssh/config

#Azure Keys
Host fedora22
  Hostname 102.160.203.241
  User ahmet
  PubkeyAuthentication yes
  IdentityFile /home/ahmet/.ssh/azure_fedora_id_rsa
# ./Azure Keys
# GitHub keys
Host github.com
  Hostname github.com
  User git
  PubKeyAuthentication yes
  IdentityFile /home/ahmet/.ssh/azure_fedora_id_rsa
Host github.private
  Hostname github.com
  User git
  PubKeyAuthentication yes
  IdentityFile /home/ahmet/.ssh/private_repo_azure_fedora_id_rsa
# ./Github Keys
# Default Settings
Host *
  PubkeyAuthentication=no
  IdentitiesOnly=yes
  ServerAliveInterval=60
  ServerAliveCountMax=30
  ControlMaster auto
  ControlPath /home/ahmet/.ssh/Connections/ssh-%r@%h:%p
  ControlPersist 4h
  StrictHostKeyChecking=no
  IdentityFile /home/ahmet/.ssh/id_rsa
  UseRoaming=no
```

이 SSH 구성은 각 서비스에 대한 섹션을 제공하여 자체 전용 키 쌍을 각각 보유하도록 합니다. 위 그룹과 일치하지 않는 곳에 로그인하는 호스트에 대한 기본 설정입니다. 또한 SSH 구성을 사용하면 공용 작업에 하나, 작업에 일반적인 전용 리포지토리에 하나 등 두 개의 분리된 [GitHub](https://github.com) 로그인을 보유할 수 있습니다.


##### 설명된 구성 파일

`Host` = 터미널에서 호출되는 호스트의 이름. `ssh fedora22`는 `SSH`이 레이블이 `Host fedora22`로 지정된 설정 블록에서 값을 사용하도록 지시합니다. 참고: 사용하기 위한 논리적인 레이블일 수 있고 어떤 서버의 실제 호스트 이름을 나타내지 않습니다.

`Hostname 102.160.203.241` = 로그인하는 서버에 대한 IP 주소 또는 DNS 이름. 서버에 라우팅하는 데 사용되고 내부 IP 주소에 매핑하는 외부 IP일 수 있습니다.

`User git` = Azure VM에 로그인할 때 사용하는 원격 사용자 계정.

`PubKeyAuthentication yes` = SSH가 로그인에 SSH 키를 사용하도록 지시합니다.

`IdentityFile /home/ahmet/.ssh/azure_fedora_id_rsa` = 서버에 나타나는 키 쌍인 SSH가 로그인을 인증하도록 지시합니다.


## 암호 없이 Linux VM에 대한 SSH

SSH 키 쌍 및 구성된 SSH 구성 파일이 있으므로 빠르고 안전하게 Linux VM에 로그인할 수 있습니다. 처음으로 SSH 키를 사용하는 서버에 로그인하면 명령은 해당 키 파일을 입력하라는 메시지를 표시합니다.

`ahmet@fedora$ ssh fedora22`

##### 설명된 명령

`ahmet@fedora$ ssh fedora22`이 SSH를 실행하는 경우 `Host fedora22` 블록에서 설정을 배치하고 로드한 다음 마지막 블록 `Host *`의 나머지 모든 설정을 로드합니다.

## 다음 단계

이제 다음 작업에 SSH 키 파일을 사용할 수 있습니다.

- [Azure 템플릿을 사용하여 보안 Linux VM 만들기](virtual-machines-linux-create-ssh-secured-vm-from-template.md)
- [Azure 포털을 사용하여 보안 Linux VM 만들기](virtual-machines-linux-quick-create-portal.md)
- [Azure CLI를 사용하여 보안 Linux VM 만들기](virtual-machines-linux-quick-create-cli.md)

<!---HONumber=AcomDC_0420_2016-->