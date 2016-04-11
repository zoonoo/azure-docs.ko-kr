<properties
		pageTitle="Azure에서 Linux VM에 사용자 추가 | Microsoft Azure"
		description="Azure에서 Linux VM에 사용자를 추가합니다."
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
		ms.date="03/04/2016"
		ms.author="v-livech"
/>

# Azure VM에 사용자 추가

새로 출시된 Linux VM에서는 첫 번째 작업 중 하나가 새 사용자를 만드는 것입니다. 이 문서에서는 sudo 사용자 계정을 만들고, 암호를 설정하고, SSH 공개 키를 추가하고, `visudo`를 사용하여 암호 없이 sudo를 허용하는 과정을 안내합니다.

필수 구성 요소는 [Azure 계정](https://azure.microsoft.com/pricing/free-trial/), [SSH 공개 및 개인 키](virtual-machines-linux-mac-create-ssh-keys.md), Azure 리소스 그룹, 설치되고 `azure config mode arm`을 사용하여 Azure Resource Manager 모드로 전환된 Azure CLI입니다.

## 빠른 명령

```bash
# Add a new user on RedHat family distros
bill@slackware$ sudo useradd -G wheel exampleUser

# Add a new user on Debian family distros
bill@slackware$ sudo useradd -G sudo exampleUser

# Set a password
bill@slackware$ sudo passwd exampleUser
Enter new UNIX password:
Retype new UNIX password:
passwd: password updated successfully

# Copy the SSH Public Key to the new user
bill@slackware$ ssh-copy-id -i ~/.ssh/id_rsa exampleuser@exampleserver

# Change sudoers to allow no password
# Execute visudo as root to edit the /etc/sudoers file
bill@slackware$ visudo

# On RedHat family distros uncomment this line:
## Same thing without a password
# %wheel        ALL=(ALL)       NOPASSWD: ALL

# to this
## Same thing without a password
%wheel        ALL=(ALL)       NOPASSWD: ALL

# On Debian family distros change this line:
# Allow members of group sudo to execute any command
%sudo   ALL=(ALL:ALL) ALL

# to this
%sudo   ALL=(ALL) NOPASSWD:ALL

# Verify everything
# Verify the SSH keys & User account
bill@slackware$ ssh -i ~/.ssh/id_rsa exampleuser@exampleserver

# Verify sudo access
exampleuser@exampleserver$ sudo top
```

## 자세한 연습

### 소개

새 서버에서 수행되는 첫 번째 작업이자 가장 일반적인 작업 중 하나는 사용자 계정을 추가하는 것입니다. 루트 로그인은 항상 비활성화되어야 하며, 루트 계정 자체도 사용해서는 안 됩니다. Linux 서버에서는 sudo만 사용해야 합니다. 새 사용자를 만들고 sudo를 사용하여 새 사용자에게 루트 에스컬레이션 권한을 제공하는 것이 Linux를 관리하고 사용하는 적절한 방법입니다.

사용되는 명령은 `/etc/passwd`, `/etc/shadow`, `/etc/group` 및 `/etc/gshadow`를 수정하여 새 Linux 사용자를 만드는 `useradd`입니다. 명령줄 플래그를 `useradd` 명령에 추가하고 새 사용자를 Linux의 적절한 sudo 그룹에 추가합니다. `useradd`는 `/etc/passwd`에 항목을 만들지만 새 사용자 계정에 암호를 제공하지는 않습니다. 매우 간단한 `passwd` 명령을 사용하여 새 사용자의 초기 암호를 만듭니다. 마지막 단계에서는 사용자가 모든 명령에 암호를 입력할 필요 없이 sudo 권한으로 명령을 실행할 수 있도록 sudo 규칙을 수정합니다. 사용자가 공개 및 개인 키 쌍을 사용하여 로그인한 경우에는 사용자 계정이 부정 행위자로부터 안전한 것으로 가정하여 암호 없는 sudo 액세스를 허용합니다.

### Azure VM에 단일 sudo 사용자 추가

SSH 키를 사용하여 Azure VM에 로그인합니다. SSH 공개 키 액세스를 설정하지 않은 경우 먼저 [Azure에서 공개 키 인증 사용](http://link.to/article) 문서를 완료합니다.

`useradd` 명령은 다음 작업을 완료합니다.

- 새 사용자 계정 만들기
- 이름이 같은 새 사용자 그룹 만들기
- `/etc/passwd`에 빈 항목 추가
- `/etc/gpasswd`에 빈 항목 추가

`-G` 명령줄 플래그는 적절한 Linux 그룹에 새 사용자 계정을 추가하여 새 사용자 계정에 루트 에스컬레이션 권한을 제공합니다.

#### 사용자 추가

```bash
# On RedHat family distros
bill@slackware$ sudo useradd -G wheel exampleUser

# On Debian family distros
bill@slackware$ sudo useradd -G sudo exampleUser
```

#### 암호 설정

`useradd` 명령은 새 사용자를 만들고 `/etc/passwd` 및 `/etc/gpasswd`에 항목을 추가하지만 실제로 암호를 설정하지는 않습니다. 이제 `passwd` 명령을 사용하여 이 작업을 수행합니다.

```bash
bill@slackware$ sudo passwd exampleUser
Enter new UNIX password:
Retype new UNIX password:
passwd: password updated successfully
```

서버에 sudo 권한이 있는 사용자가 있습니다.

### 새 사용자 계정에 SSH 공개 키 추가

컴퓨터에서 방금 설정한 새 암호와 함께 `ssh-copy-id` 명령을 사용합니다.

```bash
bill@slackware$ ssh-copy-id -i ~/.ssh/id_rsa exampleuser@exampleserver
```

### visudo를 사용하여 암호 없이 sudo를 사용하도록 허용

`visudo`를 사용하여 `/etc/sudoers` 파일을 편집하면 매우 중요한 이 파일이 잘못 수정되는 것을 방지하는 몇 개의 보호 계층이 추가됩니다. `visudo`를 실행할 때 `/etc/sudoers` 파일은 편집 중에 누구도 변경할 수 없도록 잠깁니다. 또한 저장하거나 종료하려고 할 때 `visudo`에서 `/etc/sudoers` 파일에 대한 실수를 확인합니다. 따라서 시스템에 손상된 sudoer 파일이 남아 있지 않습니다.

이미 sudo 액세스를 위한 올바른 기본 그룹에 사용자가 있습니다. 이제 암호 없이 sudo를 사용할 수 있도록 이러한 그룹을 활성화합니다.

```bash
# Execute visudo as root to edit the /etc/sudoers file
bill@slackware$ visudo

# On RedHat family distros uncomment this line:
## Same thing without a password
# %wheel        ALL=(ALL)       NOPASSWD: ALL

# to this
## Same thing without a password
%wheel        ALL=(ALL)       NOPASSWD: ALL

# On Debian family distros change this line:
# Allow members of group sudo to execute any command
%sudo   ALL=(ALL:ALL) ALL

# to this
%sudo   ALL=(ALL) NOPASSWD:ALL
```

### 사용자, ssh 키 및 sudo 확인

```bash
# Verify the SSH keys & User account
bill@slackware$ ssh -i ~/.ssh/id_rsa exampleuser@exampleserver

# Verify sudo access
exampleuser@exampleserver$ sudo top
```

<!---HONumber=AcomDC_0330_2016-->