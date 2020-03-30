---
title: GRUB 및 단일 사용자 모드를 위한 Azure 직렬 콘솔 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 가상 컴퓨터에서 GRUB용 직렬 콘솔을 사용하는 방법에 대해 설명합니다.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "70883934"
---
# <a name="use-serial-console-to-access-grub-and-single-user-mode"></a>직렬 콘솔을 사용하여 GRUB 및 단일 사용자 모드에 액세스
GRand 통합 부트 로더 (GRUB)는 가상 시스템 (VM)을 부팅 할 때 가장 먼저 볼 수 있습니다. 운영 체제가 시작되기 전에 표시되므로 GRUB은 SSH를 통해 액세스할 수 없습니다. GRUB에서는 부팅 구성을 수정하여 단일 사용자 모드로 부팅할 수 있습니다.

단일 사용자 모드는 최소한의 기능을 갖춘 최소한의 환경입니다. 부팅 문제, 파일 시스템 문제 또는 네트워크 문제를 조사하는 데 유용할 수 있습니다. 백그라운드에서 실행할 수 있는 서비스 수가 적고 런레벨에 따라 파일 시스템이 자동으로 탑재되지 않을 수도 있습니다.

단일 사용자 모드는 VM이 로그인을 위해 SSH 키만 허용하도록 구성될 수 있는 상황에서도 유용합니다. 이 경우 단일 사용자 모드를 사용하여 암호 인증을 사용하여 계정을 만들 수 있습니다. 

> [!NOTE]
> 직렬 콘솔 서비스는 *기여자* 수준 이상 권한이 있는 사용자만 VM의 직렬 콘솔에 액세스할 수 있도록 허용합니다.

단일 사용자 모드를 입력하려면 VM이 부팅될 때 GRUB을 입력하고 GRUB에서 부팅 구성을 수정합니다. 다음 섹션에서 GRUB을 입력하기 위한 자세한 지침을 참조하십시오. 일반적으로 VM이 GRUB을 표시하도록 구성된 경우 VM의 직렬 콘솔 내에서 다시 시작 버튼을 사용하여 VM을 다시 시작하고 GRUB을 표시할 수 있습니다.

![리눅스 직렬 콘솔 다시 시작 버튼](./media/virtual-machines-serial-console/virtual-machine-serial-console-restart-button-bar.png)

## <a name="general-grub-access"></a>일반 GRUB 액세스
GRUB에 액세스하려면 직렬 콘솔 창이 열려 있는 동안 VM을 재부팅합니다. 일부 배포판에서는 GRUB을 표시하기 위해 키보드 입력이 필요하고, 다른 배포판에서는 사용자 키보드 입력이 시간 시간을 취소할 수 있도록 몇 초 동안 GRUB을 자동으로 표시합니다.

단일 사용자 모드에 액세스하려면 VM에서 GRUB을 사용하도록 설정해야 합니다. 배포에 따라 GRUB을 사용하도록 설정하기 위해 일부 설치 작업이 필요할 수 있습니다. 배포 관련 정보는 Azure에서 다음 섹션 과 [Linux 지원](https://blogs.msdn.microsoft.com/linuxonazure/2018/10/23/why-proactively-ensuring-you-have-access-to-grub-and-sysrq-in-your-linux-vm-could-save-you-lots-of-down-time/) 페이지를 참조하십시오.

### <a name="restart-your-vm-to-access-grub-in-serial-console"></a>VM을 다시 시작하여 직렬 콘솔에서 GRUB에 액세스
**다시 시작** 단추 위로 마우스를 가져간 다음 VM 다시 시작을 선택하여 직렬 콘솔 내에서 **VM을**다시 시작할 수 있습니다. 창 하단에 다시 시작에 대한 알림이 표시됩니다.

SysRq가 활성화된 경우 SysRq "b" 명령을 실행하여 [VM을](./serial-console-nmi-sysrq.md) 다시 시작할 수도 있습니다. 재부팅할 때 GRUB에서 기대할 수 있는 사항에 대해 알아보려면 다음 섹션의 배포 관련 지침을 참조하세요.

![리눅스 직렬 콘솔 다시 시작](./media/virtual-machines-serial-console/virtual-machine-serial-console-restart-button-ubuntu.gif)

## <a name="general-single-user-mode-access"></a>일반 단일 사용자 모드 액세스
암호 인증을 사용하여 계정을 구성하지 않은 경우 단일 사용자 모드에 대한 수동 액세스가 필요할 수 있습니다. GRUB 구성을 수정하여 단일 사용자 모드를 수동으로 입력합니다. 이 작업을 완료한 후에는 추가 지침에 대해 "단일 사용자 모드 사용으로 암호를 재설정하거나 추가" 섹션을 참조하세요.

VM을 부팅할 수 없는 경우 배포판이 자동으로 단일 사용자 모드 또는 비상 모드로 전환됩니다. 그러나 다른 배포판에서는 루트 암호를 설정하는 등의 추가 설정이 필요하며, 이 경우 자동으로 단일 사용자 또는 비상 모드로 전환해야 합니다.

### <a name="use-single-user-mode-to-reset-or-add-a-password"></a>단일 사용자 모드를 사용하여 암호를 재설정하거나 추가
단일 사용자 모드에 있는 후 다음을 수행 하 여 sudo 권한이 있는 새 사용자를 추가 합니다.
1. 실행하여 `useradd <username>` 사용자를 추가합니다.
1. 실행하여 `sudo usermod -a -G sudo <username>` 새 사용자 루트 권한을 부여합니다.
1. `passwd <username>`을 사용하여 새 사용자의 암호를 설정합니다. 그런 다음 새 사용자로 로그인할 수 있습니다.


## <a name="access-for-red-hat-enterprise-linux-rhel"></a>RHEL(Red Hat Enterprise Linux)에 대한 액세스
RHEL이 정상적으로 부팅할 수 없는 경우 자동으로 단일 사용자 모드로 전환됩니다. 그러나 단일 사용자 모드에 대한 루트 액세스를 설정하지 않은 경우 루트 암호가 없고 로그인할 수 없습니다. 해결 방법이 있지만("RHEL에서 단일 사용자 모드를 수동으로 입력" 섹션 참조) 처음에 루트 액세스를 설정하는 것이 좋습니다.

### <a name="grub-access-in-rhel"></a>RHEL에서 GRUB 액세스
RHEL에는 기본적으로 GRUB이 활성화되어 있습니다. GRUB을 입력하려면 를 실행하여 `sudo reboot`VM을 재부팅한 다음 키를 누릅니다. GRUB 창이 표시되어야 합니다. 그렇지 않은 경우 다음 줄이 GRUB 파일에 있는지 확인합니다(`/etc/default/grub`

**RHEL 8용**

```
GRUB_TIMEOUT=5
GRUB_TERMINAL="serial console"
GRUB_CMDLINE_LINUX="console=tty1 console=ttyS0 earlyprintk=ttyS0 rootdelay=300"
```

**RHEL 7용**

```
GRUB_TIMEOUT=5
GRUB_TERMINAL_OUTPUT="serial console"
GRUB_CMDLINE_LINUX="console=tty1 console=ttyS0,115200n8 earlyprintk=ttyS0,115200 rootdelay=300 net.ifnames=0"
```

> [!NOTE]
> 또한 Red Hat은 구조 모드, 비상 모드 또는 디버그 모드로 부팅하고 루트 암호를 재설정하기 위한 설명서를 제공합니다. 지침은 부팅 [하는 동안 터미널 메뉴 편집을](https://aka.ms/rhel7grubterminal)참조 하십시오.

### <a name="set-up-root-access-for-single-user-mode-in-rhel"></a>RHEL에서 단일 사용자 모드에 대한 루트 액세스 설정
루트 사용자는 기본적으로 사용할 수 없습니다. RHEL의 단일 사용자 모드에는 루트 사용자가 활성화되어야 합니다. 단일 사용자 모드를 사용하도록 설정해야 하는 경우 다음 지침을 사용합니다.

1. SSH를 통해 Red Hat 시스템에 로그인합니다.
1. 루트로 전환합니다.
1. 다음을 수행하여 루트 사용자의 암호를 활성화합니다.
    * 실행(강력한 `passwd root` 루트 암호 설정).
1. 루트 사용자가 다음을 수행하여 ttyS0을 통해서만 로그인할 수 있는지 확인합니다.  
    a. 을 `edit /etc/ssh/sshd_config`실행하고 PermitRootLogIn이 로 `no`설정되어 있는지 확인합니다.  
    b. ttyS0을 통해서만 로그인을 허용하도록 실행합니다. `edit /etc/securetty file`

이제 시스템이 단일 사용자 모드로 부팅되면 루트 암호로 로그인할 수 있습니다.

또는 RHEL 7.4+ 또는 6.9+의 경우 GRUB 프롬프트에서 단일 사용자 모드를 활성화하려면 [단일 사용자 모드로 부팅을](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/5/html/installation_guide/s1-rescuemode-booting-single)참조하십시오.

### <a name="manually-enter-single-user-mode-in-rhel"></a>RHEL에서 단일 사용자 모드로 수동으로 입력
앞의 지침을 사용하여 GRUB 및 루트 액세스를 설정한 경우 다음을 수행하여 단일 사용자 모드를 입력할 수 있습니다.

1. GRUB을 입력하려면 VM을 다시 시작할 때 Esc를 누릅니다.
1. GRUB에서 E를 눌러 부팅할 OS를 편집합니다. OS는 일반적으로 첫 번째 줄에 나열됩니다.
1. 커널 선을 찾습니다. Azure에서는 *linux16*.
1. Ctrl+E를 눌러 줄의 끝으로 이동합니다.
1. 줄의 끝에서 *systemd.unit=rescue.target을*추가합니다.
    
    이 작업은 단일 사용자 모드로 부팅됩니다. 비상 모드를 사용하려면 *systemd.unit=emergency.target을* 줄 끝에 추가합니다(systemd.unit=rescue.target 대신). *systemd.unit=rescue.target*

1. Ctrl+X를 눌러 적용된 설정으로 종료하고 재부팅합니다.

   단일 사용자 모드로 들어가기 전에 관리자 암호를 입력하라는 메시지가 표시됩니다. 이 암호는 이전 지침에서 만든 암호입니다.

    ![](../media/virtual-machines-serial-console/virtual-machine-linux-serial-console-rhel-enter-emergency-shell.gif)

### <a name="enter-single-user-mode-without-root-account-enabled-in-rhel"></a>RHEL에서 루트 계정을 사용하도록 설정하지 않고 단일 사용자 모드 입력
이전 지침에 따라 루트 사용자를 사용하도록 설정하지 않은 경우에도 다음을 수행하여 루트 암호를 재설정할 수 있습니다.

> [!NOTE]
> SELinux를 사용하는 경우 루트 암호를 재설정할 때 [Red Hat 설명서에](https://aka.ms/rhel7grubterminal)설명된 추가 단계를 따라야 합니다.

1. GRUB을 입력하려면 VM을 다시 시작할 때 Esc를 누릅니다.

1. GRUB에서 E를 눌러 부팅할 OS를 편집합니다. OS는 일반적으로 첫 번째 줄에 나열됩니다.
1. 커널 선을 찾습니다. Azure에서는 *linux16*.
1. 줄의 끝에서 *rd.break를* 줄의 끝에 추가합니다. 커널 라인과 *rd.break*사이에 공백을 둡니다.

    이 작업은 [Red Hat 설명서에](https://aka.ms/rhel7rootpassword) `initramfs` 설명된 대로 컨트롤이 전달되기 `systemd`전에 부팅 프로세스를 중단합니다.
1. Ctrl+X를 눌러 적용된 설정으로 종료하고 재부팅합니다.

   재부팅한 후 읽기 전용 파일 시스템으로 비상 모드로 전환됩니다. 
   
1. 셸에서 읽기/쓰기 권한이 있는 루트 파일 시스템을 다시 마운트하려면 입력합니다. `mount -o remount,rw /sysroot`
1. 단일 사용자 모드로 부팅한 `chroot /sysroot` 후 입력하여 `sysroot` 감옥으로 전환합니다.
1. 당신은 지금 뿌리에 있어. 루트 암호를 입력하여 `passwd` 재설정한 다음 이전 지침을 사용하여 단일 사용자 모드로 들어갈 수 있습니다. 
1. 작업이 완료되면 다시 부팅하려면 입력합니다. `reboot -f`

![](../media/virtual-machines-serial-console/virtual-machine-linux-serial-console-rhel-emergency-mount-no-root.gif)

> [!NOTE]
> 위의 지침을 실행하면 비상 셸로 떨어지므로 편집과 `fstab`같은 작업도 수행할 수 있습니다. 그러나 일반적으로 루트 암호를 재설정하고 단일 사용자 모드로 들어가는 데 사용하는 것이 좋습니다.

## <a name="access-for-centos"></a>CentOS에 대한 액세스
레드 햇 엔터프라이즈 리눅스와 마찬가지로, CentOS의 단일 사용자 모드는 GRUB과 루트 사용자가 활성화되어야 합니다.

### <a name="grub-access-in-centos"></a>CentOS에서 GRUB 액세스
CentOS에는 기본적으로 GRUB이 활성화되어 있습니다. GRUB을 입력하려면 을 입력하여 `sudo reboot`VM을 다시 부팅한 다음 키를 누릅니다. 이 작업에는 GRUB 창이 표시됩니다.

### <a name="single-user-mode-in-centos"></a>CentOS의 단일 사용자 모드
CentOS에서 단일 사용자 모드를 사용하려면 RHEL에 대한 이전 지침을 따르십시오.

## <a name="access-for-ubuntu"></a>Ubuntu에 대한 액세스
우분투 이미지 루트 암호를 필요로 하지 않습니다. 시스템이 단일 사용자 모드로 부팅되는 경우 추가 자격 증명 없이 사용할 수 있습니다.

### <a name="grub-access-in-ubuntu"></a>Ubuntu에서 GRUB 액세스
GRUB에 액세스하려면 VM이 부팅되는 동안 Esc를 길게 누릅니다.

기본적으로 우분투 이미지는 GRUB 창을 자동으로 표시하지 않을 수 있습니다. 다음을 수행하여 설정을 변경할 수 있습니다.
1. 텍스트 편집기에서 */etc/default/grub.d/50-cloudimg-settings.cfg* 파일을 엽니다.

1. 값을 `GRUB_TIMEOUT` 0이 아닌 값으로 변경합니다.
1. 텍스트 편집기에서 *열기 /etc/기본값/grub*.
1. 라인을 주석으로 작성합니다. `GRUB_HIDDEN_TIMEOUT=1`
1. 줄이 `GRUB_TIMEOUT_STYLE=menu` 있는지 확인합니다.
1. `sudo update-grub`을 실행합니다.

### <a name="single-user-mode-in-ubuntu"></a>우분투에서 단일 사용자 모드
우분투정상적으로 부팅할 수 없는 경우 자동으로 단일 사용자 모드로 전환됩니다. 단일 사용자 모드를 수동으로 입력하려면 다음을 수행합니다.

1. GRUB에서 E를 눌러 부팅 항목(우분투 항목)을 편집합니다.
1. *리눅스로*시작하는 라인을 찾아 *RO를*찾습니다.
1. *ro*후 *단일* 을 추가하여 *단일*이전과 이후의 공간이 있는지 확인합니다.
1. Ctrl+X를 눌러 이러한 설정으로 재부팅하고 단일 사용자 모드로 들어갑니다.

### <a name="use-grub-to-invoke-bash-in-ubuntu"></a>우분투에서 배시를 호출하는 GRUB을 사용하여
앞의 지침을 시도한 후에도 우분투 VM에서 단일 사용자 모드에 액세스할 수 없는 상황(예: 잊어버린 루트 암호)이 있을 수 있습니다. 또한 커널에 시스템 리시널이 아닌 init으로 실행되도록 `/bin/bash` 지시할 수도 있습니다. 이 작업은 bash 셸을 제공하고 시스템 유지 관리를 허용합니다. 다음 지침을 따르세요.

1. GRUB에서 E를 눌러 부팅 항목(우분투 항목)을 편집합니다.

1. *리눅스로*시작하는 라인을 찾아 *RO를*찾습니다.
1. *Rw init =/bin/bash로* *ro를* 바꿉꿉을 바꿉꿉이.

    이 작업은 파일 시스템을 읽기-쓰기로 `/bin/bash` 탑재하고 입기 프로세스로 사용합니다.
1. 이러한 설정으로 재부팅하려면 Ctrl+X를 누릅니다.

## <a name="access-for-coreos"></a>CoreOS에 대한 액세스
CoreOS의 단일 사용자 모드에는 GRUB을 사용하도록 설정해야 합니다.

### <a name="grub-access-in-coreos"></a>CoreOS에서 GRUB 액세스
GRUB에 액세스하려면 VM이 부팅되는 동안 키를 누릅니다.

### <a name="single-user-mode-in-coreos"></a>코어OS의 단일 사용자 모드
CoreOS가 정상적으로 부팅할 수 없는 경우 자동으로 단일 사용자 모드로 전환됩니다. 단일 사용자 모드를 수동으로 입력하려면 다음을 수행합니다.

1. GRUB에서 E를 눌러 부팅 항목을 편집합니다.

1. *리눅스 $로*시작하는 라인을 찾습니다 . 줄의 두 인스턴스가 있어야 하며, 각 인스턴스는 다른 경우 캡슐화되어 있어야 *합니다. else* 절을 참조하십시오.
1. 각 *리눅스 $* 라인의 끝에 *coreos.autologin = ttyS0을* 부속하십시오.
1. Ctrl+X를 눌러 이러한 설정으로 재부팅하고 단일 사용자 모드로 들어갑니다.

## <a name="access-for-suse-sles"></a>SUSE SLES에 대한 액세스
SLES 12 SP3+의 최신 이미지는 시스템이 비상 모드로 부팅되는 경우 직렬 콘솔을 통해 액세스할 수 있도록 합니다.

### <a name="grub-access-in-suse-sles"></a>SUSE SLES에서 GRUB 액세스
SLES의 GRUB 액세스에는 YaST를 통한 부트 로더 구성이 필요합니다. 구성을 만들려면 다음을 수행합니다.

1. SSH를 사용하여 SLES VM에 로그인한 `sudo yast bootloader`다음 을 실행합니다. 탭을 누른 다음 Enter를 누른 다음 화살표 키를 사용하여 메뉴를 탐색합니다.

1. **커널 매개 변수로**이동한 다음 **직렬 콘솔 사용** 확인란을 선택합니다.
1. `serial --unit=0 --speed=9600 --parity=no` **콘솔** 인수에 추가합니다.
1. F10을 눌러 설정을 저장하고 종료합니다.
1. GRUB을 입력하려면 VM을 재부팅하고 부팅 시퀀스 중에 키를 눌러 GRUB 창을 계속 표시합니다.

    GRUB의 기본 시간 설정은 **1입니다.** */etc/default/grub* `GRUB_TIMEOUT` 파일의 변수를 변경하여 이 설정을 수정할 수 있습니다.

![부트 로더 구성 초기화](../media/virtual-machines-serial-console/virtual-machine-linux-serial-console-sles-yast-grub-config.gif)

### <a name="single-user-mode-in-suse-sles"></a>SUSE SLES의 단일 사용자 모드
SLES가 정상적으로 부팅할 수 없는 경우 자동으로 비상 셸에 떨어지게 됩니다. 비상 셸을 수동으로 입력하려면 다음을 수행합니다.

1. GRUB에서 E를 눌러 부팅 항목(SLES 항목)을 편집합니다.

1. *리눅스로*시작하는 커널 라인을 찾습니다.
1. 커널 라인의 끝에 *systemd.unit=emergency.target을* 부속합니다.
1. Ctrl+X를 눌러 이러한 설정으로 재부팅하고 비상 셸을 입력합니다.

   > [!NOTE]
   > 이 작업을 수행하면 읽기 전용 파일 시스템으로 비상 셸로 전환됩니다. 파일을 편집하려면 읽기-쓰기 권한이 있는 파일 시스템을 다시 마운트합니다. 이렇게 하려면 셸을 입력합니다. `mount -o remount,rw /`

## <a name="access-for-oracle-linux"></a>Oracle Linux에 대한 액세스
레드 햇 엔터프라이즈 리눅스와 마찬가지로, 오라클 리눅스에서 단일 사용자 모드는 GRUB및 루트 사용자가 활성화할 것을 요구한다.

### <a name="grub-access-in-oracle-linux"></a>Oracle Linux에서 GRUB 액세스
Oracle Linux에는 기본적으로 GRUB이 활성화되어 있습니다. GRUB을 입력하려면 를 실행하여 `sudo reboot`VM을 재부팅한 다음 Esc를 누릅니다. 이 작업에는 GRUB 창이 표시됩니다. GRUB 창이 표시되지 않으면 `GRUB_TERMINAL` 줄값에 *직렬 콘솔(즉,* `GRUB_TERMINAL="serial console"`)이 포함되어 있는지 확인합니다. 를 통해 `grub2-mkconfig -o /boot/grub/grub.cfg`GRUB을 다시 빌드합니다.

### <a name="single-user-mode-in-oracle-linux"></a>오라클 리눅스의 단일 사용자 모드
오라클 Linux에서 단일 사용자 모드를 사용하려면 RHEL에 대한 이전 지침을 따르십시오.

## <a name="next-steps"></a>다음 단계
직렬 콘솔에 대한 자세한 내용은 다음을 참조하십시오.
* [리눅스 시리얼 콘솔 문서](serial-console-linux.md)
* [직렬 콘솔을 사용하여 다양한 배포판에서 GRUB사용](https://blogs.msdn.microsoft.com/linuxonazure/2018/10/23/why-proactively-ensuring-you-have-access-to-grub-and-sysrq-in-your-linux-vm-could-save-you-lots-of-down-time/)
* [NMI 및 SysRq 호출에 직렬 콘솔 사용](serial-console-nmi-sysrq.md)
* [Windows VM용 직렬 콘솔](serial-console-windows.md)
* [부팅 진단](boot-diagnostics.md)
