---
title: GRUB 및 단일 사용자 모드용 Azure 직렬 콘솔 | Microsoft Docs
description: 이 문서에서는 Azure virtual machines에서 GRUB 용 직렬 콘솔을 사용 하는 방법을 설명 합니다.
services: virtual-machines-linux
documentationcenter: ''
author: asinn826
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 08/06/2019
ms.author: alsin
ms.openlocfilehash: 06cb3fe5d551ddfc95fcbd37cd9620adebd825c5
ms.sourcegitcommit: 7c5a2a3068e5330b77f3c6738d6de1e03d3c3b7d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/11/2019
ms.locfileid: "70883934"
---
# <a name="use-serial-console-to-access-grub-and-single-user-mode"></a>직렬 콘솔을 사용 하 여 GRUB 및 단일 사용자 모드에 액세스
주 통합 부팅 로더 (GRUB)는 VM (가상 머신)을 부팅할 때 가장 먼저 표시 될 것입니다. 운영 체제를 시작 하기 전에 표시 되기 때문에 GRUB는 SSH를 통해 액세스할 수 없습니다. GRUB에서 부팅 구성이 단일 사용자 모드로 부팅 되도록 수정할 수 있습니다.

단일 사용자 모드는 최소한의 기능만 사용 하는 최소 환경입니다. 이는 부팅 문제, 파일 시스템 문제 또는 네트워크 문제를 조사 하는 데 유용할 수 있습니다. 백그라운드에서 실행 될 수 있는 서비스의 수를 줄이고 실행 수준에 따라 파일 시스템이 자동으로 탑재 되지 않을 수도 있습니다.

단일 사용자 모드는 로그인을 위해 SSH 키만 허용 하도록 VM을 구성할 수 있는 경우에도 유용 합니다. 이 경우 단일 사용자 모드를 사용 하 여 암호 인증을 사용 하는 계정을 만들 수 있습니다. 

> [!NOTE]
> 직렬 콘솔 서비스를 사용 하면 *참가자* 수준의 사용 권한이 있는 사용자만 VM의 직렬 콘솔에 액세스할 수 있습니다.

단일 사용자 모드를 시작 하려면 VM이 부팅 될 때 GRUB를 입력 하 고 GRUB에서 부팅 구성을 수정 합니다. 다음 섹션에서 GRUB를 입력 하는 방법에 대 한 자세한 지침을 참조 하세요. 일반적으로 VM이 GRUB를 표시 하도록 구성 된 경우 VM의 직렬 콘솔 내에서 다시 시작 단추를 사용 하 여 VM을 다시 시작 하 고 GRUB를 표시할 수 있습니다.

![Linux 직렬 콘솔 다시 시작 단추](./media/virtual-machines-serial-console/virtual-machine-serial-console-restart-button-bar.png)

## <a name="general-grub-access"></a>일반 GRUB 액세스
GRUB에 액세스 하려면 직렬 콘솔 창이 열려 있는 동안 VM을 다시 부팅 합니다. 일부 배포판에는 GRUB를 표시 하기 위해 키보드 입력이 필요 하 고, 다른 배포에서는 몇 초 동안 자동으로 GRUB를 표시 하 여 사용자 키보드 입력이 시간 제한을 취소할 수 있습니다.

단일 사용자 모드에 액세스할 수 있으려면 GRUB를 VM에서 사용 하도록 설정 해야 합니다. 배포에 따라 GRUB를 사용 하도록 설정 하기 위해 몇 가지 설정 작업이 필요할 수 있습니다. 배포 관련 정보는 다음 섹션 및 [Azure에서 Linux에 대 한 지원](https://blogs.msdn.microsoft.com/linuxonazure/2018/10/23/why-proactively-ensuring-you-have-access-to-grub-and-sysrq-in-your-linux-vm-could-save-you-lots-of-down-time/) 페이지를 참조 하세요.

### <a name="restart-your-vm-to-access-grub-in-serial-console"></a>VM을 다시 시작하여 직렬 콘솔에서 GRUB에 액세스
직렬 콘솔 내에서 **다시 시작** 단추를 마우스로 가리킨 다음 **vm 다시 시작**을 선택 하 여 vm을 다시 시작할 수 있습니다. 다시 시작에 대 한 알림이 창의 아래쪽에 표시 됩니다.

[Sysrq](./serial-console-nmi-sysrq.md) 를 사용 하도록 설정한 경우 sysrq "b" 명령을 실행 하 여 VM을 다시 시작할 수도 있습니다. 다시 부팅할 때 GRUB에서 예측할 수 있는 작업에 대 한 자세한 내용은 다음 섹션의 배포 관련 지침을 참조 하세요.

![Linux 직렬 콘솔 다시 시작](./media/virtual-machines-serial-console/virtual-machine-serial-console-restart-button-ubuntu.gif)

## <a name="general-single-user-mode-access"></a>일반 단일 사용자 모드 액세스
암호 인증을 사용 하 여 계정을 구성 하지 않은 경우에는 단일 사용자 모드에 대 한 수동 액세스가 필요할 수 있습니다. GRUB 구성을 수동으로 단일 사용자 모드로 시작 하도록 수정 합니다. 이 작업을 완료 한 후에는 "단일 사용자 모드를 사용 하 여 암호를 다시 설정 하거나 추가" 섹션을 참조 하 여 추가 지침을 확인 하세요.

VM을 부팅할 수 없는 경우 배포는 종종 사용자를 단일 사용자 모드 또는 응급 모드로 자동으로 삭제 합니다. 그러나 다른 배포에서는 루트 암호 설정 등의 추가 설정이 필요 하므로 사용자를 단일 사용자 또는 응급 모드로 자동으로 삭제할 수 있습니다.

### <a name="use-single-user-mode-to-reset-or-add-a-password"></a>단일 사용자 모드를 사용 하 여 암호 재설정 또는 추가
단일 사용자 모드에 있는 경우 다음을 수행 하 여 sudo 권한이 있는 새 사용자를 추가 합니다.
1. 을 `useradd <username>` 실행 하 여 사용자를 추가 합니다.
1. 을 `sudo usermod -a -G sudo <username>` 실행 하 여 새 사용자 루트 권한을 부여 합니다.
1. `passwd <username>`을 사용하여 새 사용자의 암호를 설정합니다. 그런 다음 새 사용자로 로그인 할 수 있습니다.


## <a name="access-for-red-hat-enterprise-linux-rhel"></a>RHEL(Red Hat Enterprise Linux)에 대한 액세스
RHEL는 정상적으로 부팅할 수 없는 경우 사용자를 단일 사용자 모드로 자동으로 삭제 합니다. 그러나 단일 사용자 모드에 대 한 루트 액세스를 설정 하지 않은 경우에는 루트 암호가 없으며 로그인 할 수 없습니다. 해결 방법이 있습니다 ("RHEL에서 단일 사용자 모드를 수동으로 입력" 섹션 참조). 하지만 처음에 루트 액세스를 설정 하는 것이 좋습니다.

### <a name="grub-access-in-rhel"></a>RHEL에서 GRUB 액세스
RHEL에는 기본적으로 GRUB이 활성화되어 있습니다. GRUB를 시작 하려면를 실행 `sudo reboot`하 여 VM을 다시 부팅 한 다음 아무 키나 누릅니다. GRUB 창이 표시 됩니다. 그렇지 않은 경우에는 다음 줄이 GRUB 파일 (`/etc/default/grub`)에 표시 되는지 확인 합니다.

**RHEL 8의 경우**

```
GRUB_TIMEOUT=5
GRUB_TERMINAL="serial console"
GRUB_CMDLINE_LINUX="console=tty1 console=ttyS0 earlyprintk=ttyS0 rootdelay=300"
```

**RHEL 7의 경우**

```
GRUB_TIMEOUT=5
GRUB_TERMINAL_OUTPUT="serial console"
GRUB_CMDLINE_LINUX="console=tty1 console=ttyS0,115200n8 earlyprintk=ttyS0,115200 rootdelay=300 net.ifnames=0"
```

> [!NOTE]
> 또한 Red Hat는 복구 모드, 응급 모드 또는 디버그 모드로 부팅 하 고 루트 암호를 다시 설정 하는 방법에 대 한 설명서를 제공 합니다. 지침은 [부팅 하는 동안 터미널 메뉴 편집](https://aka.ms/rhel7grubterminal)을 참조 하세요.

### <a name="set-up-root-access-for-single-user-mode-in-rhel"></a>RHEL에서 단일 사용자 모드에 대 한 루트 액세스 설정
루트 사용자는 기본적으로 사용 하지 않도록 설정 됩니다. RHEL의 단일 사용자 모드에서는 루트 사용자를 사용 하도록 설정 해야 합니다. 단일 사용자 모드를 사용 하도록 설정 해야 하는 경우 다음 지침을 사용 합니다.

1. SSH를 통해 Red Hat 시스템에 로그인 합니다.
1. Root로 전환 합니다.
1. 다음을 수행 하 여 루트 사용자에 대 한 암호를 사용 하도록 설정 합니다.
    * ( `passwd root` 강력한 루트 암호 설정)를 실행 합니다.
1. 루트 사용자가 다음을 수행 하 여 ttyS0를 통해서만 로그인 할 수 있는지 확인 합니다.  
    a. 을 `edit /etc/ssh/sshd_config`실행 하 고 PermitRootLogIn이로 `no`설정 되었는지 확인 합니다.  
    b. TtyS0 `edit /etc/securetty file` 를 통한 로그인만 허용 하려면를 실행 합니다.

이제 시스템이 단일 사용자 모드로 부팅 되 면 루트 암호를 사용 하 여 로그인 할 수 있습니다.

또는 RHEL 7.4 + 또는 6.9 +의 경우 GRUB 프롬프트에서 단일 사용자 모드를 사용 하도록 설정 하려면 [단일 사용자 모드로 부팅](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/5/html/installation_guide/s1-rescuemode-booting-single)을 참조 하세요.

### <a name="manually-enter-single-user-mode-in-rhel"></a>RHEL에서 수동으로 단일 사용자 모드 입력
위의 지침을 사용 하 여 GRUB 및 루트 액세스를 설정한 경우 다음을 수행 하 여 단일 사용자 모드를 시작할 수 있습니다.

1. GRUB를 입력 하려면 VM을 다시 시작할 때 Esc 키를 누릅니다.
1. GRUB에서 E를 눌러 부팅 하려는 OS를 편집 합니다. OS는 일반적으로 첫 번째 줄에 나열 됩니다.
1. 커널 줄을 찾습니다. Azure에서 *linux16*로 시작 합니다.
1. 줄의 끝으로 이동 하려면 Ctrl + E를 누릅니다.
1. 줄의 끝에 *systemd. unit = 구출*를 추가 합니다.
    
    이 작업을 수행 하면 단일 사용자 모드로 부팅 됩니다. 응급 모드를 사용 하려면 systemd *. unit = 비상* 을 줄의 끝에 추가 합니다 ( *systemd. unit = 구출*대신).

1. Ctrl + X를 눌러 종료 하 고 적용 된 설정으로 다시 부팅 합니다.

   단일 사용자 모드를 시작 하기 전에 관리자 암호를 입력 하 라는 메시지가 표시 됩니다. 이 암호는 이전 지침에서 만든 암호입니다.

    ![](../media/virtual-machines-serial-console/virtual-machine-linux-serial-console-rhel-enter-emergency-shell.gif)

### <a name="enter-single-user-mode-without-root-account-enabled-in-rhel"></a>RHEL에서 루트 계정이 사용 하도록 설정 되지 않은 단일 사용자 모드로 전환 합니다.
이전 지침에 따라 루트 사용자를 사용 하도록 설정 하지 않은 경우에도 다음을 수행 하 여 루트 암호를 다시 설정할 수 있습니다.

> [!NOTE]
> SELinux를 사용 하는 경우 루트 암호를 다시 설정할 때 [Red Hat 설명서](https://aka.ms/rhel7grubterminal)에 설명 된 추가 단계를 수행 해야 합니다.

1. GRUB를 입력 하려면 VM을 다시 시작할 때 Esc 키를 누릅니다.

1. GRUB에서 E를 눌러 부팅 하려는 OS를 편집 합니다. OS는 일반적으로 첫 번째 줄에 나열 됩니다.
1. 커널 줄을 찾습니다. Azure에서 *linux16*로 시작 합니다.
1. 줄의 끝에서 줄의 끝에 *를 추가 합니다.* 커널 줄과 *rd. break*사이에 공백을 둡니다.

    이 작업을 수행 하면 [Red Hat 설명서](https://aka.ms/rhel7rootpassword)에 설명 된 `initramfs` 대로 `systemd`컨트롤이에서로 전달 되기 전에 부팅 프로세스가 중단 됩니다.
1. Ctrl + X를 눌러 종료 하 고 적용 된 설정으로 다시 부팅 합니다.

   를 다시 부팅 한 후에는 읽기 전용 파일 시스템을 사용 하 여 응급 모드로 전환 됩니다. 
   
1. 셸에서 읽기/쓰기 권한을 `mount -o remount,rw /sysroot` 사용 하 여 루트 파일 시스템을 다시 탑재 하려면를 입력 합니다.
1. 단일 사용자 모드로 부팅 한 후를 입력 `chroot /sysroot` 하 여 `sysroot` 감옥으로 전환 합니다.
1. 이제 루트에 있습니다. 를 입력 `passwd` 하 고 위의 지침에 따라 단일 사용자 모드로 전환 하 여 루트 암호를 다시 설정할 수 있습니다. 
1. 완료 되 면를 입력 `reboot -f` 하 여 다시 부팅 합니다.

![](../media/virtual-machines-serial-console/virtual-machine-linux-serial-console-rhel-emergency-mount-no-root.gif)

> [!NOTE]
> 이전 지침을 통해를 실행 하면 응급 셸로 이동 하 여 편집과 `fstab`같은 작업을 수행할 수도 있습니다. 그러나 일반적으로 루트 암호를 다시 설정 하 고이를 사용 하 여 단일 사용자 모드로 전환 하는 것이 좋습니다.

## <a name="access-for-centos"></a>CentOS에 대한 액세스
Red Hat Enterprise Linux, CentOS의 단일 사용자 모드와 마찬가지로 GRUB 및 루트 사용자를 사용 하도록 설정 해야 합니다.

### <a name="grub-access-in-centos"></a>CentOS에서 GRUB 액세스
CentOS에는 기본적으로 GRUB이 활성화되어 있습니다. GRUB를 시작 하려면를 입력 `sudo reboot`하 여 VM을 다시 부팅 한 다음 아무 키나 누릅니다. 그러면 GRUB 창이 표시 됩니다.

### <a name="single-user-mode-in-centos"></a>CentOS의 단일 사용자 모드
CentOS에서 단일 사용자 모드를 사용 하도록 설정 하려면 RHEL에 대 한 이전 지침을 따릅니다.

## <a name="access-for-ubuntu"></a>Ubuntu에 대한 액세스
Ubuntu 이미지에는 루트 암호가 필요 하지 않습니다. 시스템이 단일 사용자 모드로 부팅 되는 경우 추가 자격 증명 없이 사용할 수 있습니다.

### <a name="grub-access-in-ubuntu"></a>Ubuntu에서 GRUB 액세스
GRUB에 액세스 하려면 VM이 부팅 되는 동안 Esc 키를 누릅니다.

기본적으로 Ubuntu 이미지는 GRUB 창을 자동으로 표시 하지 않을 수 있습니다. 다음을 수행 하 여 설정을 변경할 수 있습니다.
1. 텍스트 편집기에서 */etc/default/grub.d/50-cloudimg-settings.cfg* 파일을 엽니다.

1. `GRUB_TIMEOUT` 값을 0이 아닌 값으로 변경 합니다.
1. 텍스트 편집기에서 */etc/default/grub*을 엽니다.
1. 줄을 주석 `GRUB_HIDDEN_TIMEOUT=1` 으로 처리 합니다.
1. `GRUB_TIMEOUT_STYLE=menu` 줄이 있는지 확인 합니다.
1. `sudo update-grub`을 실행합니다.

### <a name="single-user-mode-in-ubuntu"></a>Ubuntu의 단일 사용자 모드
Ubuntu를 정상적으로 부팅할 수 없는 경우 사용자를 단일 사용자 모드로 자동으로 삭제 합니다. 단일 사용자 모드를 수동으로 입력 하려면 다음을 수행 합니다.

1. GRUB에서 E 키를 눌러 부팅 항목 (Ubuntu 항목)을 편집 합니다.
1. *Linux*로 시작 하는 줄을 찾은 다음 *ro*을 찾습니다.
1. 단일 행 앞과 뒤에 공백이 있는지 확인 하 여 한 *번* *ro*를 추가 *합니다.*
1. 이러한 설정으로 다시 부팅 하려면 Ctrl + X를 누르고 단일 사용자 모드로 전환 합니다.

### <a name="use-grub-to-invoke-bash-in-ubuntu"></a>GRUB를 사용 하 여 Ubuntu에서 bash 호출
위의 지침을 시도한 후에도 Ubuntu VM에서 단일 사용자 모드에 액세스할 수 없는 상황 (예: 잊어버린 루트 암호)이 있을 수 있습니다. 시스템 init 대신 init로 실행 `/bin/bash` 되도록 커널을 지시할 수도 있습니다. 이 작업은 bash 셸을 제공 하 고 시스템 유지 관리를 허용 합니다. 다음 지침을 따르세요.

1. GRUB에서 E 키를 눌러 부팅 항목 (Ubuntu 항목)을 편집 합니다.

1. *Linux*로 시작 하는 줄을 찾은 다음 *ro*을 찾습니다.
1. *Ro* 을 *rw init =/bin/bash*으로 바꿉니다.

    이 작업은 파일 시스템을 읽기/쓰기로 탑재 하 고 `/bin/bash` 를 초기화 프로세스로 사용 합니다.
1. 이러한 설정으로 다시 부팅 하려면 Ctrl + X를 누릅니다.

## <a name="access-for-coreos"></a>CoreOS에 대한 액세스
CoreOS에서 단일 사용자 모드를 사용 하려면 GRUB를 사용 하도록 설정 해야 합니다.

### <a name="grub-access-in-coreos"></a>CoreOS에서 GRUB 액세스
GRUB에 액세스 하려면 VM이 부팅 되는 동안 아무 키나 누릅니다.

### <a name="single-user-mode-in-coreos"></a>CoreOS의 단일 사용자 모드
CoreOS는 정상적으로 부팅할 수 없는 경우 사용자를 단일 사용자 모드로 자동으로 삭제 합니다. 단일 사용자 모드를 수동으로 입력 하려면 다음을 수행 합니다.

1. GRUB에서 E 키를 눌러 부팅 항목을 편집 합니다.

1. *Linux $* 로 시작 하는 줄을 찾습니다. 줄의 인스턴스가 두 개 있어야 합니다. 각 인스턴스는 서로 다른 경우에 캡슐화 됩니다 *. else* 절.
1. 각 *linux $* line의 끝에 *coreos = ttyS0* 를 추가 합니다.
1. 이러한 설정으로 다시 부팅 하려면 Ctrl + X를 누르고 단일 사용자 모드로 전환 합니다.

## <a name="access-for-suse-sles"></a>SUSE SLES에 대한 액세스
시스템이 응급 모드로 부팅 되는 경우 SLES 12 SP3 이상 이미지의 최신 이미지는 직렬 콘솔을 통해 액세스를 허용 합니다.

### <a name="grub-access-in-suse-sles"></a>SUSE SLES에서 GRUB 액세스
SLES의 GRUB 액세스에는 YaST를 통한 부팅 로더 구성이 필요 합니다. 구성을 만들려면 다음을 수행 합니다.

1. SSH를 사용 하 여 SLES VM에 로그인 한 다음를 실행 `sudo yast bootloader`합니다. Tab 키를 누르고 enter 키를 누른 다음 화살표 키를 사용 하 여 메뉴를 탐색 합니다.

1. **커널 매개 변수**로 이동한 다음 **직렬 콘솔 사용** 확인란을 선택 합니다.
1. 콘솔 `serial --unit=0 --speed=9600 --parity=no` 인수에 를 추가 합니다.
1. F10 키를 눌러 설정을 저장 하 고 종료 합니다.
1. GRUB를 시작 하려면 VM을 다시 부팅 하 고 부팅 순서 중 아무 키나 눌러 GRUB 창을 표시 합니다.

    GRUB의 기본 시간 제한은 1 **입니다.** `GRUB_TIMEOUT` */Etc/default/grub* 파일에서 변수를 변경 하 여이 설정을 수정할 수 있습니다.

![부팅 로더 구성 초기화](../media/virtual-machines-serial-console/virtual-machine-linux-serial-console-sles-yast-grub-config.gif)

### <a name="single-user-mode-in-suse-sles"></a>SUSE SLES의 단일 사용자 모드
SLES가 정상적으로 부팅할 수 없는 경우 응급 셸에 자동으로 삭제 됩니다. 응급 셸을 수동으로 입력 하려면 다음을 수행 합니다.

1. GRUB에서 E 키를 눌러 부팅 항목 (SLES 항목)을 편집 합니다.

1. *Linux*로 시작 하는 커널 줄을 찾습니다.
1. 커널 줄의 끝에 *systemd. unit = 비상. target* 을 추가 합니다.
1. 이러한 설정으로 다시 부팅 하려면 Ctrl + X를 누르고 응급 셸을 입력 합니다.

   > [!NOTE]
   > 이 작업을 수행 하면 읽기 전용 파일 시스템을 통해 응급 셸에 추가 됩니다. 모든 파일을 편집 하려면 읽기/쓰기 권한을 사용 하 여 파일 시스템을 다시 탑재 합니다. 이렇게 하려면 셸에를 입력 `mount -o remount,rw /` 합니다.

## <a name="access-for-oracle-linux"></a>Oracle Linux에 대한 액세스
Red Hat Enterprise Linux, Oracle Linux의 단일 사용자 모드와 마찬가지로 GRUB 및 루트 사용자를 사용 하도록 설정 해야 합니다.

### <a name="grub-access-in-oracle-linux"></a>Oracle Linux에서 GRUB 액세스
Oracle Linux에는 기본적으로 GRUB이 활성화되어 있습니다. GRUB를 시작 하려면를 실행 `sudo reboot`하 여 VM을 다시 부팅 한 다음 esc 키를 누릅니다. 그러면 GRUB 창이 표시 됩니다. GRUB 창이 표시 되지 않으면 `GRUB_TERMINAL` 줄의 값에 *직렬 콘솔* `GRUB_TERMINAL="serial console"`()이 포함 되어 있는지 확인 합니다. 을 사용 하 `grub2-mkconfig -o /boot/grub/grub.cfg`여 GRUB를 다시 빌드합니다.

### <a name="single-user-mode-in-oracle-linux"></a>Oracle Linux의 단일 사용자 모드
Oracle Linux에서 단일 사용자 모드를 사용 하도록 설정 하려면 이전 RHEL에 대 한 지침을 따르세요.

## <a name="next-steps"></a>다음 단계
직렬 콘솔에 대 한 자세한 내용은 다음을 참조 하세요.
* [Linux 직렬 콘솔 설명서](serial-console-linux.md)
* [직렬 콘솔을 사용 하 여 다양 한 배포에서 GRUB 사용](https://blogs.msdn.microsoft.com/linuxonazure/2018/10/23/why-proactively-ensuring-you-have-access-to-grub-and-sysrq-in-your-linux-vm-could-save-you-lots-of-down-time/)
* [NMI 및 SysRq 호출에 직렬 콘솔 사용](serial-console-nmi-sysrq.md)
* [Windows Vm 용 직렬 콘솔](serial-console-windows.md)
* [부팅 진단](boot-diagnostics.md)
