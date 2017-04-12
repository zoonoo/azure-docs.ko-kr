---
title: "Azure Linux Virtual Machines에서 SSHD 구성 | Microsoft Docs"
description: "보안 모범 사례에 대해 SSHD를 구성하여 Azure Linux Virtual Machines에 대한 SSH를 잠급니다."
services: virtual-machines-linux
documentationcenter: 
author: vlivech
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 11/21/2016
ms.author: v-livech
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: 0195c385b00ab92b2b92ce8ff00983a0d91bf3a1
ms.lasthandoff: 04/03/2017


---

# <a name="configure-sshd-on-azure-linux-vms"></a>Azure Linux VM에서 SSHD 구성

이 문서에서는 암호 대신 SSH 키를 사용하여 Linux에서 SSH 서버를 잠그고 모범 사례 보안을 제공하며 SSH 로그인 프로세스를 가속화하는 방법을 보여 줍니다.  추가로 SSHD를 잠그려면 루트 사용자를 로그인하는 작업에서 사용하지 않도록 설정하고 승인된 그룹 목록을 통해 로그인할 수 있는 사용자를 제한하게 되며 SSH 프로토콜 버전 1을 사용하지 않도록 설정하고 최소 키 비트를 설정하고 유휴 사용자의 자동 로그아웃을 구성합니다.  이 문서에 대한 요구 사항은 Azure 계정([무료 평가판 가져오기](https://azure.microsoft.com/pricing/free-trial/)) 및 [SSH 공용 및 개인 키 파일](mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)입니다.

## <a name="quick-commands"></a>빠른 명령

다음 설정을 사용하여 `/etc/ssh/sshd_config`을(를) 구성합니다.

### <a name="disable-password-logins"></a>암호 로그인 사용 안 함

```bash
PasswordAuthentication no
```

### <a name="disable-login-by-the-root-user"></a>루트 사용자가 로그인을 사용하지 않도록 설정

```bash
PermitRootLogin no
```

### <a name="allowed-groups-list"></a>그룹 목록 허용

```bash
AllowGroups wheel
```

### <a name="allowed-users-list"></a>사용자 목록 허용

```bash
AllowUsers ahmet ralph
```

### <a name="disable-ssh-protocol-version-1"></a>SSH 프로토콜 버전 1을 사용하지 않도록 설정

```bash
Protocol 2
```

### <a name="minimum-key-bits"></a>최소 키 비트

```bash
ServerKeyBits 2048
```

### <a name="disconnect-idle-users"></a>유휴 사용자 연결 끊기

```bash
ClientAliveInterval 300
ClientAliveCountMax 0
```

## <a name="detailed-walkthrough"></a>자세한 연습

SSHD는 Linux VM에서 실행되는 SSH 서버입니다.  SSH는 MacBook의 셸, Linux 워크스테이션 또는 Windows의 Bash에서 실행되는 클라이언트입니다.  또한 SSH는 워크스테이션과 Linux VM 간의 통신을 보호하고 암호화하는 데 사용되는 프로토콜이며 SSH를 VPN(사설 가상 네트워크)으로 만듭니다.

이 문서의 경우 전체 연습에 열려 있도록 Linux VM에 대한 로그인을 유지하는 것이 중요합니다.  SSH 연결이 설정되면 창이 닫히지 않는 한 세션을 열어 둡니다.  하나의 터미널에 로그인해 있다면 주요 변경 내용이 적용되는 경우 잠그지 않고 SSHD 서비스를 변경할 수 있습니다.  손상된 SSHD 구성을 사용하는 Linux VM이 잠긴 경우 Azure에서는 [Azure VM 액세스 확장](using-vmaccess-extension.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)을 사용하여 손상된 SSHD 구성을 다시 설정하는 기능을 제공합니다.

이러한 이유로 두 개의 터미널과 SSH을 양 쪽 모두에서 Linux VM에 대해 열어 둡니다.  첫 번째 터미널을 사용하여 SSHD 구성 파일을 변경하고 SSHD 서비스를 다시 시작합니다.  서비스가 다시 시작되면 두 번째 터미널을 사용하여 해당 변경 내용을 테스트합니다.  SSH 암호를 사용하지 않고 SSH 키에 엄격하게 의존하기 때문에 SSH가 올바르지 않고 VM에 대한 연결을 닫을 경우 VM은 영구적으로 잠기며 VM을 삭제하고 다시 요청하기 위해 아무도 로그인할 수 없게 됩니다.

## <a name="disable-password-logins"></a>암호 로그인 사용 안 함

Linux VM의 보안을 유지하는 가장 빠른 방법은 암호 로그인을 사용하지 않도록 설정하는 것입니다.  암호 로그인을 사용하는 경우 웹을 크롤링하는 보트는 즉시 SSH를 사용하여 Linux VM에 대한 암호를 무차별 암호 대입 추측하기 시작합니다.  암호 로그인을 완전히 사용하지 않도록 설정하면 SSH 서버가 모든 암호 로그인 시도를 무시할 수 있습니다.

```bash
PasswordAuthentication no
```

## <a name="disable-login-by-the-root-user"></a>루트 사용자가 로그인을 사용하지 않도록 설정

Linux 모범 사례에 따르면 `root` 사용자는 SSH에 로그인되지 않고 `sudo su`를 사용하지 않습니다.  루트 수준 사용 권한을 필요로 하는 모든 명령은 항상 `sudo` 명령을 통해 실행되어야 하며 여기서는 후속 감사에 대한 모든 작업을 기록합니다.  `root` 사용자가 SSH를 통한 로그인을 사용하지 않도록 설정하는 작업은 권한이 있는 사용자만 SSH에 로그인하도록 허용하는 보안 모범 사례 단계입니다.

```bash
PermitRootLogin no
```

## <a name="allowed-groups-list"></a>그룹 목록 허용

SSH는 사용자 및 그룹에 SSH를 통한 로그인을 허용하거나 허용하지 않도록 제한하는 메서드를 제공합니다.  이 기능은 목록을 사용하여 특정 사용자 및 그룹이 로그인하도록 승인하거나 거부합니다.  휠 그룹을 `AllowGroups` 목록으로 설정하면 SSH를 통해 승인된 휠 그룹에 있는 사용자 계정에 대한 로그인을 제한합니다.

```bash
AllowGroups wheel
```

## <a name="allowed-users-list"></a>사용자 목록 허용

사용자에 대한 SSH 로그인을 제한하는 것은 `AllowGroups`인 동일한 태스크를 수행하는 보다 구체적인 방법입니다.  

```bash
AllowUsers ahmet ralph
```

## <a name="disable-ssh-protocol-version-1"></a>SSH 프로토콜 버전 1을 사용하지 않도록 설정

SSH 프로토콜 버전 1은 안전하지 않고 비활성화되어야 합니다.  SSH 프로토콜 버전 2는 서버에 SSH하는 안전한 방법을 제공하는 현재 버전입니다.  SSH 버전 1을 사용하지 않도록 설정하면 SSH 버전 1을 사용하여 SSH 서버와의 연결을 설정하려고 시도하는 모든 SSH 클라이언트를 거부합니다.  SSH 버전 2 연결을 통해서만 SSH 서버와의 연결이 가능합니다.

```bash
Protocol 2
```

## <a name="minimum-key-bits"></a>최소 키 비트

보안 모범 사례에 따라 암호 SSH 로그인을 사용하지 않도록 설정하고 SSH 서버에서 인증하는 데 SSH 키를 사용할 수 있습니다.  비트 단위로 측정된 길이가 다른 키를 사용하여 이러한 SSH 키를 만들 수 있습니다.  모범 사례에 따르면 키 길이가 2048비트인 경우 허용 가능한 최소 키 길이입니다.  2048비트 미만의 키는 이론적으로 손상되었을 가능성이 있습니다.  `ServerKeyBits`를 `2048`로 설정하면 2048비트 이상의 키를 사용하는 모든 연결을 허용하고 2048비트 미만의 연결을 거부하게 됩니다.

```bash
ServerKeyBits 2048
```

## <a name="disconnect-idle-users"></a>유휴 사용자 연결 끊기

SSH에는 열린 연결이 설정 시간(초) 이상 유휴 상태로 유지되는 사용자 연결을 끊을 수 있는 기능이 있습니다.  활성화된 해당 사용자에게 열려 있는 세션을 유지하면 Linux VM의 노출을 제한합니다.

```bash
ClientAliveInterval 300
ClientAliveCountMax 0
```

## <a name="restart-sshd"></a>SSHD 다시 시작

`/etc/ssh/sshd_config`에서 설정을 사용하려면 SSH 서버를 다시 시작하는 SSHD 프로세스를 다시 시작합니다.  SSH 서버를 다시 시작하는 데 사용하는 터미널 창은 열린 SSH 세션을 손실하지 않고 열려 있게 됩니다.  새 SSH 서버 설정을 테스트하려면 두 번째 터미널 창 또는 탭을 사용합니다.  SSH 연결을 테스트하기 위해 별도 터미널을 사용하면 SSHD 주요 변경 내용으로 잠그지 않고 뒤로 돌아가서 첫 번째 터미널에 있는 `/etc/ssh/sshd_config`을 추가로 변경할 수 있습니다.  

### <a name="on-redhat-centos-and-fedora"></a>Redhat, Centos 및 Fedora에서

```bash
service sshd restart
```

### <a name="on-debian--ubuntu"></a>Debian 및 Ubuntu에서

```bash
service ssh restart
```

## <a name="reset-sshd-using-azure-reset-access"></a>Azure 재설정 액세스를 사용하여 SSHD 다시 설정

SSHD 구성에 주요 변경 내용이 잠겨진 경우 Azure VM 액세스 확장을 사용하여 SSHD 구성을 원래 구성으로 다시 설정할 수 있습니다.

모든 예제 이름을 고유한 설정으로 바꿉니다.

```azurecli
azure vm reset-access \
--resource-group myResourceGroup \
--name myVM \
--reset-ssh
```

## <a name="install-fail2ban"></a>Fail2ban 설치

오픈 소스 앱 Fail2ban을 설치하고 설정하는 것이 가장 좋습니다. 그러면 무차별 암호 대입을 사용하여 SSH를 통해 Linux VM에 로그인하려는 반복적인 시도를 차단합니다.  Fail2ban에서는 반복된 실패 시도를 기록하여 SSH를 통해 로그인한 다음 방화벽 규칙을 만들어서 시도가 시작된 IP 주소를 차단합니다.

* [Fail2ban 홈페이지](http://www.fail2ban.org/wiki/index.php/Main_Page)

## <a name="next-steps"></a>다음 단계

이제 Linux VM에서 SSH 서버를 구성하고 잠갔으므로 추가 보안 모범 사례를 따를 수 있습니다.  

* [VMAccess 확장을 사용하여 사용자, SSH 관리 및 Azure Linux VM의 디스크 검사 또는 복구](using-vmaccess-extension.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

* [Azure CLI를 사용하여 Linux VM에서 디스크 암호화](encrypt-disks.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

* [Azure Resource Manager 템플릿의 액세스 및 보안](dotnet-core-3-access-security.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

