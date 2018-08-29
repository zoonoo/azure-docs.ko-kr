---
title: GRUB 및 단일 사용자 모드용 Azure 직렬 콘솔 | Microsoft Docs
description: Azure 가상 머신에서 Grub에 직렬 콘솔 사용
services: virtual-machines-linux
documentationcenter: ''
author: alsin
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 08/14/2018
ms.author: alsin
ms.openlocfilehash: 059cb0cbc7e62af16dbf95693be421feebcc1ee0
ms.sourcegitcommit: 76797c962fa04d8af9a7b9153eaa042cf74b2699
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/21/2018
ms.locfileid: "42144611"
---
# <a name="use-serial-console-to-access-grub-and-single-user-mode"></a>직렬 콘솔을 사용하여 GRUB 및 단일 사용자 모드 액세스
단일 사용자 모드는 최소한의 기능이 포함된 최소한의 환경입니다. 백그라운드에서보다 적은 수의 서비스가 실행될 수 있으므로 부팅 문제나 네트워크 문제를 조사하는 데 유용할 수 있으며 실행 수준에 따라 파일 시스템이 자동으로 탑재되지 않을 수도 있습니다. 따라서 손상된 파일 시스템, 손상된 fstab 또는 네트워크 연결(잘못된 iptables 구성)과 같은 상황을 조사하는 데 유용합니다.

일부 배포판은 VM을 부팅할 수 없는 경우 단일 사용자 모드 또는 비상 모드로 자동으로 전환됩니다. 하지만 다른 배포판은 단일 사용자 또는 비상 모드로 자동으로 전환되기 전에 추가 설정이 필요합니다.

단일 사용자 모드에 액세스할 수 있으려면 VM에서 GRUB을 사용하도록 설정되어 있는지 확인해야 합니다. 배포판에 따라 GRUB을 사용하도록 설정되어 있는지 확인하기 위한 몇 가지 설정 작업이 있을 수 있습니다. 


## <a name="access-for-red-hat-enterprise-linux-rhel"></a>RHEL(Red Hat Enterprise Linux)에 대한 액세스
RHEL은 정상적으로 부팅할 수 없는 경우 단일 사용자 모드로 자동으로 전환됩니다. 하지만 단일 사용자 모드에 대한 루트 액세스를 설정하지 않은 경우에는 루트 암호가 없어서 로그인할 수 없습니다. 해결 방법(아래에서 '단일 사용자 모드 수동 전환' 참조)이 있지만 처음에는 루트 액세스를 설정하는 것이 좋습니다.

### <a name="grub-access-in-rhel"></a>RHEL에서 GRUB 액세스
RHEL에는 기본적으로 GRUB이 활성화되어 있습니다. GRUB으로 전환하려면 `sudo reboot`을 사용하여 VM을 다시 부팅하고 아무 키나 누릅니다. GRUB 화면이 나타납니다.

> 참고: Red Hat은 복구 모드, 비상 모드, 디버그 모드로 부팅하고 루트 암호를 재설정하는 설명서도 제공합니다. [액세스하려면 여기를 클릭하세요](https://aka.ms/rhel7grubterminal).

### <a name="set-up-root-access-for-single-user-mode-in-rhel"></a>RHEL에서 단일 사용자 모드에 대한 루트 액세스 설정
RHEL에서 단일 사용자 모드를 사용하려면 루트 사용자를 사용하도록 설정해야 합니다. 루트 사용자는 기본적으로 사용하지 않도록 설정되어 있습니다. 단일 사용자 모드를 활성화해야 하는 경우에는 다음 지침을 따르세요.

1. SSH를 통해 Red Hat 시스템에 로그인
1. 루트로 전환
1. 루트 사용자의 암호를 사용하도록 설정 
    * `passwd root`(강력한 루트 암호 설정)
1. 루트 사용자가 ttyS0을 통해서만 로그인 할 수 있는지 확인
    * `edit /etc/ssh/sshd_config` 및 PermitRootLogIn이 no로 설정되어 있는지 확인
    * ttyS0을 통한 로그인만 허용하도록 `edit /etc/securetty file` 

이제 시스템이 단일 사용자 모드로 부팅되면 루트 암호를 통해 로그인할 수 있습니다.

RHEL 7.4+ 또는 6.9+의 경우 GRUB 프롬프트에서 단일 사용자 모드를 활성화할 수 있습니다. 지침은 [여기](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/5/html/installation_guide/s1-rescuemode-booting-single)를 참조하세요.

### <a name="manually-enter-single-user-mode-in-rhel"></a>RHEL에서 단일 사용자 모드로 수동 전환
위의 지침에 따라 GRUB 및 루트 액세스를 설정했으면 다음 지침에 따라 단일 사용자 모드로 전환할 수 있습니다.

1. VM을 다시 시작하는 동안 'Esc' 키를 눌러서 GRUB으로 전환합니다.
1. GRUB에서 'e'를 눌러서 부팅하도록 선택한 OS를 편집합니다(일반적으로 첫 번째 줄).
1. 커널 줄 찾기 - Azure에서 `linux16`으로 시작합니다.
1. Ctrl + E를 눌러서 줄 끝으로 이동합니다.
1. `systemd.unit=rescue.target`을 줄의 끝에 추가합니다.
    * 그러면 단일 사용자 모드로 부팅됩니다. 비상 모드를 사용하려면 줄 끝에 `systemd.unit=rescue.target` 대신 `systemd.unit=emergency.target`을 추가합니다.
1. Ctrl + X를 눌러 끝낸 후 적용된 설정으로 다시 부팅합니다.
1. 단일 사용자 모드로 들어가기 전에 관리자 암호를 묻는 메시지가 표시됩니다. 이것은 위 지침에서 만든 암호와 같습니다.    

    ![](../media/virtual-machines-serial-console/virtual-machine-linux-serial-console-rhel-enter-emergency-shell.gif)

### <a name="enter-single-user-mode-without-root-account-enabled-in-rhel"></a>RHEL에서 루트 계정을 사용하지 않고 단일 사용자 모드로 전환
루트 사용자를 활성화하기 위해 위의 단계를 거치지 않은 경우에도 루트 암호를 재설정할 수 있습니다. 다음 지침을 따르세요.

> 참고: SELinux를 사용하는 경우에는 루트 암호를 재설정할 때 [여기](https://aka.ms/rhel7grubterminal) Red Hat 설명서에 있는 추가 단계를 수행해야 합니다.

1. VM을 다시 시작하는 동안 'Esc' 키를 눌러서 GRUB으로 전환합니다.
1. GRUB에서 'e'를 눌러서 부팅하도록 선택한 OS를 편집합니다(일반적으로 첫 번째 줄).
1. 커널 줄 찾기 - Azure에서 `linux16`으로 시작합니다.
1. 줄 끝에 `rd.break`를 추가하고 `rd.break` 앞에 공백이 있는지 확인합니다(아래 예 참조).
    - 이렇게 하면 [여기](https://aka.ms/rhel7rootpassword) Red Hat 설명서의 내용처럼 `initramfs`에서 `systemd`로 컨트롤이 전달되기 전에 부팅 프로세스가 중단됩니다.
1. Ctrl + X를 눌러 끝낸 후 적용된 설정으로 다시 부팅합니다.
1. 부팅이 되면, 읽기 전용 파일 시스템으로 비상 모드로 전환됩니다. 셸에 `mount -o remount,rw /sysroot`를 입력하여 읽기/쓰기 권한으로 루트 파일 시스템을 다시 탑재합니다.
1. 단일 사용자 모드로 부팅되면 `chroot /sysroot`를 입력하여 `sysroot` jail로 전환합니다.
1. 이제 루트입니다. `passwd`로 루트 암호를 재설정한 다음, 위의 지침을 사용하여 단일 사용자 모드로 전환합니다. `reboot -f`를 입력하여 완료되면 다시 부팅합니다.

![](../media/virtual-machines-serial-console/virtual-machine-linux-serial-console-rhel-emergency-mount-no-root.gif)

> 참고: 위의 지침을 실행하면 비상 셸로 전환될 수 있으므로 `fstab` 편집과 같은 작업을 수행할 수도 있습니다. 그러나 일반적으로 허용되는 제안은 루트 암호를 재설정하고 이를 사용하여 단일 사용자 모드로 전환하는 것입니다. 


## <a name="access-for-centos"></a>CentOS에 대한 액세스
Red Hat Enterprise Linux와 마찬가지로 CentOS에서 단일 사용자 모드를 사용하려면 GRUB과 루트 사용자가 활성화되어 있어야 합니다. 

### <a name="grub-access-in-centos"></a>CentOS에서 GRUB 액세스
CentOS에는 기본적으로 GRUB이 활성화되어 있습니다. GRUB으로 전환하려면 `sudo reboot`을 사용하여 VM을 다시 부팅하고 아무 키나 누릅니다. GRUB 화면이 나타납니다.

### <a name="single-user-mode-in-centos"></a>CentOS의 단일 사용자 모드
위의 RHEL에 대한 지침에 따라 CentOS에서 단일 사용자 모드를 사용하도록 설정합니다.

## <a name="access-for-ubuntu"></a>Ubuntu에 대한 액세스 
Ubuntu 이미지에는 루트 암호가 필요하지 않습니다. 시스템이 단일 사용자 모드로 부팅되면 추가 자격 증명 없이 사용할 수 있습니다. 

### <a name="grub-access-in-ubuntu"></a>Ubuntu에서 GRUB 액세스
GRUB에 액세스하려면 VM이 부팅되는 동안 'Esc' 키를 길게 누릅니다.

### <a name="single-user-mode-in-ubuntu"></a>Ubuntu의 단일 사용자 모드
Ubuntu는 정상적으로 부팅할 수 없는 경우 단일 사용자 모드로 자동으로 전환됩니다. 단일 사용자 모드로 수동으로 전환하려면 다음 지침을 따르세요.

1. GRUB에서 'e'를 눌러서 부팅 항목(Ubuntu 항목)을 편집합니다.
1. `linux`로 시작하는 줄을 찾은 다음, `ro`를 찾습니다.
1. `ro` 다음에 `single`을 추가합니다. `single` 앞뒤에 공백이 있어야 합니다.
1. Ctrl + X를 눌러서 이 설정으로 다시 부팅하고 단일 사용자 모드로 전환합니다.

## <a name="access-for-coreos"></a>CoreOS에 대한 액세스
CoreOS에서 단일 사용자 모드를 사용하려면 GRUB을 사용하도록 설정해야 합니다. 

### <a name="grub-access-in-coreos"></a>CoreOS에서 GRUB 액세스
GRUB에 액세스하려면, VM을 부팅하는 동안 아무 키나 누릅니다.

### <a name="single-user-mode-in-coreos"></a>CoreOS의 단일 사용자 모드
CoreOS는 정상적으로 부팅할 수 없는 경우 단일 사용자 모드로 자동으로 전환됩니다. 단일 사용자 모드로 수동으로 전환하려면 다음 지침을 따르세요.
1. GRUB에서 'e'를 눌러서 부팅 항목을 편집합니다.
1. `linux$`로 시작하는 줄을 찾습니다. 서로 다른 if/else 절에 캡슐화된 줄이 2개 있습니다.
1. 두 `linux$` 줄의 끝에 `coreos.autologin=ttyS0`를 추가합니다.
1. Ctrl + X를 눌러서 이 설정으로 다시 부팅하고 단일 사용자 모드로 전환합니다.

## <a name="access-for-suse-sles"></a>SUSE SLES에 대한 액세스
SLES 12 SP3+의 최신 이미지는 시스템이 비상 모드로 부팅되는 경우 직렬 콘솔을 통한 액세스를 허용합니다. 

### <a name="grub-access-in-suse-sles"></a>SUSE SLES에서 GRUB 액세스
SLES에서 GRUB 액세스에는 YaST를 통한 부팅 로더 구성이 필요합니다. 이렇게 하려면 다음 지침을 따르세요.

1. ssh로 SLES VM에 연결하고 `sudo yast bootloader`를 실행합니다. `tab` 키, `enter` 키 및 화살표 키를 사용하여 메뉴를 탐색합니다. 
1. `Kernel Parameters`로 이동하여 `Use serial console`을 확인합니다. 
1. `serial --unit=0 --speed=9600 --parity=no`를 콘솔 인수에 추가합니다.

1. F10을 눌러서 설정을 저장하고 종료합니다.
1. GRUB으로 전환하려면 VM을 다시 부팅하고 부팅 시퀀스 중에 아무 키나 눌러 GRUB을 화면에 유지합니다.
    - GRUB에 대한 기본 시간 제한은 1초입니다. 이 설정은 `/etc/default/grub`에서 `GRUB_TIMEOUT` 변수를 변경하여 수정할 수 있습니다.

![](../media/virtual-machines-serial-console/virtual-machine-linux-serial-console-sles-yast-grub-config.gif)

### <a name="single-user-mode-in-suse-sles"></a>SUSE SLES의 단일 사용자 모드
SLES가 정상적으로 부팅할 수 없는 경우 비상 셸로 자동으로 전환됩니다. 비상 셸로 수동으로 전환하려면 다음 지침을 따르세요.

1. GRUB에서 'e'를 눌러서 부팅 항목(SLES 항목)을 편집합니다.
1. `linux`로 시작하는 커널 줄을 찾습니다.
1. 줄의 끝에 `systemd.unit=emergency.target`을 추가합니다.
1. Ctrl + X를 눌러서 이 설정으로 다시 부팅하고 비상 셸로 전환합니다.
> _읽기 전용_ 파일 시스템으로 비상 셸로 전환됩니다. 편집하려는 파일이 있는 경우에는 읽기-쓰기 권한으로 파일 시스템을 다시 탑재해야 합니다. 이렇게 하려면 셸에 `mount -o remount,rw /`를 입력합니다.

## <a name="access-for-oracle-linux"></a>Oracle Linux에 대한 액세스
Red Hat Enterprise Linux와 마찬가지로 Oracle Linux에서 단일 사용자 모드를 사용하려면 GRUB과 루트 사용자가 활성화되어 있어야 합니다. 

### <a name="grub-access-in-oracle-linux"></a>Oracle Linux에서 GRUB 액세스
Oracle Linux에는 기본적으로 GRUB이 활성화되어 있습니다. GRUB으로 전환하려면 `sudo reboot`을 사용하여 VM을 다시 부팅하고 'Esc' 키를 누릅니다. GRUB 화면이 나타납니다.

### <a name="single-user-mode-in-oracle-linux"></a>Oracle Linux의 단일 사용자 모드
위의 RHEL에 대한 지침에 따라 Oracle Linux에서 단일 사용자 모드를 사용하도록 설정합니다.

## <a name="next-steps"></a>다음 단계
* 주 직렬 콘솔 Linux 설명서 페이지는 [여기](serial-console.md)에 있습니다.
* [NMI 및 SysRq 호출](serial-console-nmi-sysrq.md)에 직렬 콘솔 사용
* [Windows](../windows/serial-console.md) VM에서도 직렬 콘솔 사용 가능
* [부트 진단](boot-diagnostics.md)에 대해 자세히 알아보기