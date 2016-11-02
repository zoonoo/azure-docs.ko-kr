<properties
	pageTitle="SSHD를 구성하여 Linux VM에 SSH 암호 사용 안 함 | Microsoft Azure"
	description="SSH에 암호 로그인을 사용하지 않도록 설정하여 Azure에서 Linux VM을 보호합니다."
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
	ms.topic="article"
	ms.date="08/26/2016"
	ms.author="v-livech"/>

# SSHD를 구성하여 Linux VM에 SSH 암호 사용 안 함

이 문서는 Linux VM의 로그인 보안을 잠그는 방법에 중점을 둡니다. SSH 포트 22가 World Bot에 열리는 즉시 암호를 추측하여 로그인하기 시작합니다. 이 문서에서는 SSH를 통해 암호 로그인을 사용하지 않도록 설정합니다. 암호를 사용하는 기능을 완전히 제거하여 이러한 유형의 무차별 암호 대입 공격으로부터 Linux VM를 보호합니다. 추가된 사항은 Linux SSHD를 구성하여 Linux에 로그인하는 가장 안전한 방법인 SSH 공용 및 개인 키를 통해 로그인을 허용합니다. 가능한 조합은 개인 키가 매우 크고 따라서 무차별 암호 대입 SSH 키를 사용해 보기 위해 신경 쓸 필요 없이 Bot을 권장하지 않는다고 추측해야 합니다.


## 목표

- 허용되지 않는 SSHD 구성:
  - 암호 로그인
  - 루트 사용자 로그인
  - 시도-응답 인증
- 허용되는 SSHD 구성:
  - SSH 키 로그인만 해당
- 로그인 중에 SSHD 다시 시작
- 새 SSHD 구성 테스트

## 소개

[정의된 SSH](https://en.wikipedia.org/wiki/Secure_Shell)

SSHD는 Linux VM에서 실행되는 SSH 서버입니다. SSH는 MacBook 또는 Linux 워크스테이션의 셸에서 실행되는 클라이언트입니다. 또한 SSH는 워크스테이션과 Linux VM 간의 통신을 보호하고 암호화하는 데 사용되는 프로토콜입니다.

이 문서의 경우 전체 연습에 열려 있도록 Linux VM에 대한 로그인을 유지하는 것이 중요합니다. 이러한 이유로 두 개의 터미널과 SSH을 양 쪽 모두에서 Linux VM에 대해 엽니다. 첫 번째 터미널을 사용하여 SSHD 구성 파일을 변경하고 SSHD 서비스를 다시 시작합니다. 서비스가 다시 시작되면 두 번째 터미널을 사용하여 해당 변경 내용을 테스트합니다. SSH 암호를 사용하지 않고 SSH 키에 엄격하게 의존하기 때문에 SSH가 올바르지 않고 VM에 대한 연결을 닫을 경우 VM은 영구적으로 잠기며 VM을 삭제하고 다시 요청하기 위해 아무도 로그인할 수 없게 됩니다.

## 필수 조건

- [Azure의 Linux VM용 Linux 및 Mac에서 SSH 키 만들기](virtual-machines-linux-mac-create-ssh-keys.md)
- Azure 계정
  - [무료 평가판 등록](https://azure.microsoft.com/pricing/free-trial/)
  - [Azure 포털](http://portal.azure.com)
- Azure에서 실행 중인 Linux VM
- `~/.ssh/`에서 SSH 공용 및 개인 키 쌍
- Linux VM의 `~/.ssh/authorized_keys`에서 SSH 공개 키
- VM의 Sudo 권한
- 열린 포트 22

## 빠른 명령

_TLDR 버전이 필요한 숙련된 Linux 관리자는 여기서부터 시작합니다. 자세한 설명과 연습이 필요한 다른 사용자는 이 섹션을 건너뜁니다._

```
username@macbook$ sudo vim /etc/ssh/sshd_config

# Change PasswordAuthentication to this:
PasswordAuthentication no

# Change PubkeyAuthentication to this:
PubkeyAuthentication yes

# Change PermitRootLogin to this:
PermitRootLogin no

# Change ChallengeResponseAuthentication to this:
ChallengeResponseAuthentication no

# On the Debian Family
username@macbook$ sudo service ssh restart

# On the RedHat Family
username@macbook$ sudo service sshd restart
```

## 자세한 연습

터미널 1(T1)에서 Linux VM에 로그인합니다. 터미널 2(T2)에서 Linux VM에 로그인합니다.

T2에서 SSHD 구성 파일을 편집합니다.

```
username@macbook$ sudo vim /etc/ssh/sshd_config
```

여기에서부터 암호를 사용하지 않고 SSH 키 로그인을 사용하도록 설정을 편집합니다. Linux 및 SSH을 필요한 만큼으로 안전하게 만들기 위해 조사하고 변경해야 하는 이 파일에는 많은 설정이 있습니다.

#### 암호 로그인 사용 안 함

```
username@macbook$ sudo vim /etc/ssh/sshd_config

# Change PasswordAuthentication to this:
PasswordAuthentication no
```

#### 공개 키 인증 사용

```
username@macbook$ sudo vim /etc/ssh/sshd_config

# Change PubkeyAuthentication to this:
PubkeyAuthentication yes
```

#### 루트 로그인 사용 안 함

```
username@macbook$ sudo vim /etc/ssh/sshd_config

# Change PermitRootLogin to this:
PermitRootLogin no
```

#### 시도-응답 인증 사용 안 함

```
# Change ChallengeResponseAuthentication to this:
ChallengeResponseAuthentication no
```

### SSHD 다시 시작

T1 셸에서 계속 로그인되어 있는지 확인합니다. 암호를 이제 사용하지 않기 때문에 SSH 키가 올바르지 않은 경우 VM을 잠그지 않는 것이 중요합니다. Linux VM에서 설정이 올바르지 않은 경우 여전히 로그인한 상태이며 SSHD 서비스가 다시 시작하는 동안 SSH가 연결되어 있으므로 T1을 사용하여 sshd\_config를 수정합니다.

T2에서 다음을 실행합니다.

##### Debian 제품군에서

```
username@macbook$ sudo service ssh restart
```

##### RedHat 제품군에서

```
username@macbook$ sudo service sshd restart
```

암호는 이제 무차별 암호 로그인 시도에서 VM을 보호하는 기능을 사용하지 않습니다. SSH로 키를 사용하면 훨씬 더 신속하고 안전하게 로그인할 수 있습니다.

<!---HONumber=AcomDC_0831_2016-->