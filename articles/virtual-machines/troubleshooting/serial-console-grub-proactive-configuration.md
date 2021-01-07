---
title: Azure 직렬 콘솔 사전 GRUB 구성 | Microsoft Docs
description: Azure 가상 머신에서 단일 사용자 및 복구 모드 액세스를 허용하는 다양한 배포에서 GRUB을 구성합니다.
services: virtual-machines-linux
documentationcenter: ''
author: mimckitt
manager: vashan
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 07/10/2019
ms.author: mimckitt
ms.openlocfilehash: aba47500400004c1d6a7044a266bad6f20d5d9c9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91360551"
---
# <a name="proactively-ensuring-you-have-access-to-grub-and-sysrq-could-save-you-lots-of-down-time"></a>사전에 GRUB 및 sysrq에 액세스할 수 있게 하면 가동 중지 시간을 상당히 단축할 수 있습니다.

직렬 콘솔과 GRUB에 액세스할 수 있으면 대부분의 경우 IaaS Linux 가상 머신의 복구 시간이 개선됩니다. GRUB은 VM을 복구하는 데 시간이 더 오래 걸리는 복구 옵션을 제공합니다. 


VM 복구를 수행하는 이유는 다양하며 다음과 같은 시나리오에서 나타납니다.

   - 손상된 파일 시스템/커널/MBR(마스터 부트 레코드)
   - 커널 업그레이드 실패
   - 잘못된 GRUB 커널 매개 변수
   - 잘못된 fstab 구성
   - 방화벽 구성
   - 암호 분실
   - 잘못된 sshd 구성 파일
   - 네트워킹 구성

 [여기](./serial-console-linux.md#common-scenarios-for-accessing-the-serial-console)에 자세히 설명된 많은 다른 시나리오

Azure에 배포된 VM에서 GRUB 및 직렬 콘솔에 액세스할 수 있는지 확인합니다. 

직렬 콘솔을 처음 사용하는 경우 [이 링크](./serial-console-linux.md)를 참조하세요.

> [!TIP]
> 변경하기 전에 파일 백업을 수행해야 합니다.

GRUB에 액세스할 수 있게 된 후 Linux VM을 빠르게 복구하는 방법을 보려면 아래 동영상을 시청하세요.

[Linux VM 복구 동영상](https://youtu.be/KevOc3d_SG4)

Linux VM을 복구하는 데 도움이 되는 여러 가지 방법이 있습니다. 클라우드 환경에서 이 프로세스는 힘들었습니다.
서비스가 빠르게 복구되도록 도구 및 기능이 지속적으로 개선되고 있습니다.

Azure 직렬 콘솔을 사용하면 시스템 콘솔에 있는 것처럼 Linux VM과 상호 작용할 수 있습니다.

커널이 부팅되는 방법을 포함하여 많은 구성 파일을 조작할 수 있습니다. 

경험이 많은 Linux/Unix 시스템 관리자는 많은 복구 시나리오의 디스크 교체 및 VM 삭제를 불필요하게 만들고 Azure 직렬 콘솔을 통해 액세스할 수 있는 **단일 사용자** 및 **긴급 모드**의 가치를 인정합니다.

복구 방법은 발생하는 문제에 따라 달라집니다. 예를 들어 분실하거나 잘못 배치된 암호는 Azure Portal -> **암호 재설정**을 통해 재설정할 수 있습니다. **암호 재설정** 기능은 확장이라고도 하며 Linux 게스트 에이전트와 통신합니다.

사용자 지정 스크립트와 같은 다른 확장을 사용할 수 있지만 이 옵션을 사용하려면 Linux **waagent**가 시작되고 정상 상태여야 합니다(항상 정상 상태인 것은 아님).

![에이전트 상태](./media/virtual-machines-serial-console/agent-status.png)


Azure 직렬 콘솔 및 GRUB에 대한 액세스 권한을 가지면 몇 시간이 아닌 몇 분 내에 암호 변경이나 잘못된 구성을 해결할 수 있습니다. 기본 커널이 손상된 시나리오에서 디스크에 여러 커널이 있는 경우에는 대체 커널에서 VM을 강제로 부팅할 수도 있습니다.

![다중 커널](./media/virtual-machines-serial-console/more-kernel.png)

## <a name="suggested-order-of-recovery-methods"></a>복구 방법의 제안된 순서:

- Azure 직렬 콘솔

- 디스크 교체 – 다음 중 하나를 사용하여 자동화할 수 있습니다.

   - [PowerShell 복구 스크립트](https://github.com/Azure/azure-support-scripts/tree/master/VMRecovery/ResourceManager)
   - [Bash 복구 스크립트](https://github.com/sribs/azure-support-scripts)

- 레거시 방법

## <a name="disk-swap-video"></a>디스크 교체 동영상:

GRUB에 액세스할 수 없는 경우 [이](https://youtu.be/m5t0GZ5oGAc) 동영상을 시청하고 VM을 복구하기 위해 디스크 교체 절차를 쉽게 자동화하는 방법을 확인하세요.

## <a name="challenges"></a>과제:

일부 Linux Azure VM은 기본적으로 GRUB에 액세스하도록 구성되지 않으며 sysrq 명령을 사용하여 중단되도록 구성되지 않습니다. SLES 11과 같은 일부 이전 배포판은 Azure 직렬 콘솔에서 로그인 프롬프트를 표시하도록 구성되지 않습니다.

이 문서에서는 GRUB을 사용 가능하도록 설정하는 방법에 관한 다양한 Linux 배포판 및 문서 구성을 검토합니다.




## <a name="how-to-configure-linux-vm-to-accept-sysrq-keys"></a>SysRq 키를 허용하도록 Linux VM을 구성하는 방법
일부 최신 Linux 배포판에서는 sysrq 키가 기본적으로 사용하도록 설정되지만 다른 배포판에서는 특정 SysRq 함수의 값만 허용하도록 구성될 수 있습니다.
이전 배포판에서는 완전히 사용하지 않도록 설정될 수 있습니다.

SysRq 기능은 Azure 직렬 콘솔에서 직접 충돌하거나 응답하지 않는 VM을 다시 부팅하는 데 유용하며 GRUB 메뉴에 대한 액세스 권한을 얻는 데 유용합니다. 또는 다른 포털 창이나 SSH 세션에서 VM을 다시 시작하면 현재 콘솔 연결이 삭제되어 GRUB 메뉴를 표시하는 데 사용되는 GRUB 시간 제한이 만료될 수 있습니다.
커널 매개 변수에 1 값을 허용하도록 VM을 구성해야 sysrq 또는 128의 모든 함수를 사용할 수 있고 이를 통해 다시 부팅하거나 전원을 끌 수 있습니다.


[Sysrq 동영상 사용](https://youtu.be/0doqFRrHz_Mc)


Azure Portal에서 SysRq 명령을 통해 다시 부팅을 허용하도록 VM을 구성하려면 커널 매개 변수 kernel.sysrq 값을 1로 설정해야 합니다.

계속 이 구성으로 다시 부팅하려면 **sysctl.conf** 파일에 항목을 추가합니다.

`echo kernel.sysrq = 1 >> /etc/sysctl.conf`

커널 매개 변수를 동적으로 구성하려면

`sysctl -w kernel.sysrq=1`

**루트** 액세스 권한이 없거나 sudo가 손상된 경우 셸 프롬프트에서 sysrq를 구성할 수 없습니다.

Azure Portal을 사용하여 이 시나리오에서 sysrq를 사용하도록 설정할 수 있습니다. 이 방법은 **sudoers.d/waagent** 파일이 손상되거나 삭제된 경우 유용할 수 있습니다.

Azure Portal 작업 -> 실행 명령 -> RunShellScript 기능을 사용하려면 waagent 프로세스가 정상 상태여야 합니다. 정상 상태에서 이 명령을 삽입하여 sysrq를 사용하도록 설정할 수 있습니다.

`sysctl -w kernel.sysrq=1 ; echo kernel.sysrq = 1 >> /etc/sysctl.conf`

여기 표시된 대로 입력: ![enable sysrq2](./media/virtual-machines-serial-console/enabling-sysrq-2.png)

완료되면 **sysrq**에 액세스할 수 있으며 다시 부팅이 가능한 것을 알 수 있습니다.

![enable sysrq3](./media/virtual-machines-serial-console/enabling-sysrq-3.png)

**다시 부팅** 및 **SysRq 보내기** 명령을 선택합니다.

![enable sysrq4](./media/virtual-machines-serial-console/enabling-sysrq-4.png)

시스템이 다음과 같은 재설정 메시지를 기록해야 합니다.

![enable sysrq5](./media/virtual-machines-serial-console/enabling-sysrq-5.png)


## <a name="ubuntu-grub-configuration"></a>Ubuntu GRUB 구성

기본적으로 VM 부팅 중에 **Esc** 키를 눌러 GRUB에 액세스할 수 있어야 합니다. GRUB 메뉴가 표시되지 않으면 이 옵션 중 하나를 사용하여 Azure 직렬 콘솔 화면에서 GRUB 메뉴를 강제로 실행하고 유지할 수 있습니다.

**옵션 1** - GRUB을 화면에 강제로 표시합니다. 

/etc/default/grub.d/50-cloudimg-settings.cfg 파일을 업데이트하여 지정된 시간 제한 동안 GRUB 메뉴를 화면에서 유지합니다.
GRUB이 즉시 표시되므로 **Esc** 키를 누를 필요가 없습니다.

```
GRUB_TIMEOUT=0

change to

GRUB_TIMEOUT=5
```

**옵션 2** - 부팅하기 전에 **Esc** 키를 누를 수 있습니다.

/etc/default/grub 파일을 변경하고 3초 시간 제한을 관찰하여 **Esc** 키를 누르면 비슷한 동작이 발생할 수 있습니다.


다음 두 줄을 주석으로 처리합니다.

```
#GRUB_HIDDEN_TIMEOUT=0
#GRUB_HIDDEN_TIMEOUT_QUIET=true
```
그리고 다음 줄을 추가합니다.

```
GRUB_TIMEOUT_STYLE=countdown
```


## <a name="ubuntu-1204"></a>Ubuntu 12\.04

Ubuntu 12.04는 직렬 콘솔에 대한 액세스를 허용하지만 상호 작용하는 기능을 제공하지 않습니다. **login:** 프롬프트가 표시되지 않습니다.


12.04에서 **login:** 프롬프트를 표시하려면:
1. 다음 텍스트를 포함하는 /etc/init/ttyS0.conf 파일을 만듭니다.

    ```
    # ttyS0 - getty
    #
    # This service maintains a getty on ttyS0 from the point the system is
    # started until it is shut down again.
    start on stopped rc RUNLEVEL=[12345]
    stop on runlevel [!12345]
    
    respawn
    exec /sbin/getty -L 115200 ttyS0 vt102
    ```    

2. Getty를 시작하도록 요청합니다.     
    ```
    sudo start ttyS0
    ```
 
Ubuntu 버전용 직렬 콘솔을 구성하는 데 필요한 설정은 [여기](https://help.ubuntu.com/community/SerialConsoleHowto)에서 찾을 수 있습니다.

## <a name="ubuntu-recovery-mode"></a>Ubuntu 복구 모드

GRUB을 통해 Ubuntu에서 추가 복구 및 정리 옵션을 사용할 수 있지만, 이 설정은 커널 매개 변수를 적절하게 구성하는 경우에만 액세스할 수 있습니다.
이 커널 부팅 매개 변수를 구성하지 못하면 복구 메뉴가 Azure 직렬 콘솔이 아닌 Azure Diagnostics로 강제로 전송됩니다.
다음 단계를 수행하여 Ubuntu 복구 메뉴에 대한 액세스 권한을 얻을 수 있습니다.

부팅 프로세스를 중단하고 GRUB 메뉴에 액세스합니다.

Ubuntu의 고급 옵션을 선택하고 Enter 키를 누릅니다.

![선택 된 Ubuntu에 대 한 고급 옵션을 포함 하는 직렬 콘솔를 보여 주는 스크린샷](./media/virtual-machines-serial-console/ubunturec1.png)

*(recovery mode)* 가 표시된 줄을 선택합니다. Enter 키가 아닌 “e” 키를 누릅니다.

![스크린샷 선택한 복구 모드 버전의 직렬 콘솔을 보여 줍니다.](./media/virtual-machines-serial-console/ubunturec2.png)

커널을 로드할 줄을 찾고 마지막 매개 변수 **nomodeset**를 **console=ttyS0**인 대상으로 대체합니다.

```
linux /boot/vmlinuz-4.15.0-1023-azure root=UUID=21b294f1-25bd-4265-9c4e-d6e4aeb57e97 ro recovery nomodeset

change to

linux /boot/vmlinuz-4.15.0-1023-azure root=UUID=21b294f1-25bd-4265-9c4e-d6e4aeb57e97 ro recovery console=ttyS0
```

![변경 된 값이 있는 직렬 콘솔를 보여 주는 스크린샷](./media/virtual-machines-serial-console/ubunturec3.png)

**Ctrl+x**를 눌러 커널을 시작하고 로드합니다.
모두 제대로 작동하는 경우 다른 복구 옵션을 수행하는 데 도움이 될 수 있는 추가 옵션이 표시됩니다.

![스크린샷 추가 복구 옵션을 제공 하는 복구 메뉴의 직렬 콘솔을 보여 줍니다.](./media/virtual-machines-serial-console/ubunturec4.png)


## <a name="red-hat-grub-configuration"></a>Red Hat GRUB 구성

## <a name="red-hat-74-grub-configuration"></a>Red Hat 7\.4\+ GRUB 구성
이 버전의 기본 /etc/default/grub 구성이 적절하게 구성됩니다.

```
GRUB_TIMEOUT=5
GRUB_DISTRIBUTOR="$(sed 's, release .*$,,g' /etc/system-release)"
GRUB_DEFAULT=saved
GRUB_DISABLE_SUBMENU=true
GRUB_TERMINAL="serial console"
GRUB_SERIAL_COMMAND="serial"
GRUB_CMDLINE_LINUX="console=tty1 console=ttyS0 earlyprintk=ttyS0 rootdelay=300"
GRUB_DISABLE_RECOVERY="true"
```

SysRq 키 사용

```
sysctl -w kernel.sysrq=1;echo kernel.sysrq = 1 >> /etc/sysctl.conf;sysctl -a | grep -i sysrq
```

## <a name="red-hat-72-and-73-grub-configuration"></a>Red Hat 7\.2 및 7\.3 GRUB 구성
수정할 파일은 /etc/default/grub입니다. 기본 구성은 다음 예제와 같이 표시됩니다.

```
GRUB_TIMEOUT=1
GRUB_DISTRIBUTOR="$(sed 's, release .*$,,g' /etc/system-release)"
GRUB_DEFAULT=saved
GRUB_DISABLE_SUBMENU=true
GRUB_TERMINAL_OUTPUT="console"
GRUB_CMDLINE_LINUX="console=tty1 console=ttyS0 earlyprintk=ttyS0 rootdelay=300"
GRUB_DISABLE_RECOVERY="true"
```

/etc/default/grub에서 다음 줄을 변경합니다.

```
GRUB_TIMEOUT=1 

to

GRUB_TIMEOUT=5
```


```
GRUB_TERMINAL_OUTPUT="console"

to

GRUB_TERMINAL="serial console"
```

다음 줄도 추가합니다.

```
GRUB_SERIAL_COMMAND=”serial –speed=115200 –unit=0 –word=8 –parity=no –stop=1″
```

이제 /etc/default/grub은 다음 예제와 같이 표시됩니다.

```
GRUB_TIMEOUT=5
GRUB_DISTRIBUTOR="$(sed 's, release .*$,,g' /etc/system-release)"
GRUB_DEFAULT=saved
GRUB_DISABLE_SUBMENU=true
GRUB_TERMINAL="serial console"
GRUB_CMDLINE_LINUX="console=tty1 console=ttyS0 earlyprintk=ttyS0 rootdelay=300"
GRUB_DISABLE_RECOVERY="true"
```
 
GRUB 구성 사용을 완료하고 업데이트합니다.

`grub2-mkconfig -o /boot/grub2/grub.cfg`

SysRq 커널 매개 변수를 설정합니다.

`sysctl -w kernel.sysrq = 1;echo kernel.sysrq = 1 >> /etc/sysctl.conf;sysctl -a | grep -i sysrq`

셸에서 또는 실행 명령을 통해 단일 줄을 사용하여 GRUB 및 SysRq를 구성할 수도 있습니다. 이 명령을 실행하기 전에 파일을 백업합니다.


`cp /etc/default/grub /etc/default/grub.bak; sed -i 's/GRUB_TIMEOUT=1/GRUB_TIMEOUT=5/g' /etc/default/grub; sed -i 's/GRUB_TERMINAL_OUTPUT="console"/GRUB_TERMINAL="serial console"/g' /etc/default/grub; echo "GRUB_SERIAL_COMMAND=\"serial --speed=115200 --unit=0 --word=8 --parity=no --stop=1\"" >> /etc/default/grub;grub2-mkconfig -o /boot/grub2/grub.cfg;sysctl -w kernel.sysrq=1;echo kernel.sysrq = 1 /etc/sysctl.conf;sysctl -a | grep -i sysrq`


## <a name="red-hat-6x-grub-configuration"></a>Red Hat 6\.x GRUB 구성
수정할 파일은 /boot/grub/grub.conf입니다. `timeout` 값은 GRUB이 표시되는 기간을 결정합니다.

```
#boot=/dev/vda
default=0
timeout=15
splashimage=(hd0,0)/grub/splash.xpm.gz
#hiddenmenu
serial --unit=0 --speed=9600
terminal serial
terminal --timeout=5 serial console
```


마지막 줄 *terminal –-timeout=5 serial console*은 **아무 키나 눌러 계속합니다.** 를 표시하는 5초 프롬프트를 추가하여 **GRUB** 시간 제한을 추가로 늘립니다.

![출력이 있는 콘솔을 보여 주는 스크린샷](./media/virtual-machines-serial-console/rh6-1.png)

Esc 키를 누르지 않아도 구성된 시간 제한=15 동안 GRUB 메뉴가 화면에 표시됩니다. 브라우저에서 콘솔을 클릭하여 메뉴를 활성화하고 필요한 커널을 선택해야 합니다.

![스크린샷 두 개의 Linux 옵션이 있는 콘솔을 보여 줍니다.](./media/virtual-machines-serial-console/rh6-2.png)

## <a name="suse"></a>SuSE

## <a name="sles-12-sp1"></a>SLES 12 sp1
공식 [문서](./serial-console-grub-single-user-mode.md#grub-access-in-suse-sles)에 따라 YaST 부팅 로더를 사용하거나

다음 매개 변수를 /etc/default/grub에 추가/변경합니다.

```
GRUB_TERMINAL=serial
GRUB_TIMEOUT=5
GRUB_SERIAL_COMMAND="serial --unit=0 --speed=9600 --parity=no"

```
ttys0이 GRUB_CMDLINE_LINUX 또는 GRUB_CMDLINE_LINUX_DEFAULT에서 사용되는지 확인합니다.

```
GRUB_CMDLINE_LINUX_DEFAULT="console=ttyS0,9600n"
```

grub.cfg를 다시 만듭니다.

`grub2-mkconfig -o /boot/grub2/grub.cfg`


## <a name="sles-11-sp4"></a>SLES 11 SP4 
직렬 콘솔이 표시되고 부팅 메시지를 표시하지만 **login:** 프롬프트를 표시하지 않습니다.

SSH 세션을 VM으로 열고 이 줄의 주석 처리를 취소하여 **/etc/inittab** 파일을 업데이트합니다.

```
#S0:12345:respawn:/sbin/agetty -L 9600 ttyS0 vt102
```

그런 다음, 명령을 실행합니다. 

`telinit q`

GRUB을 사용하도록 설정하려면 /boot/grub/menu.lst를 다음과 같이 변경해야 합니다. 

```
timeout 5
serial --unit=0 --speed=9600 --parity=no
terminal --timeout=5 serial console

root (hd0,0)
kernel /boot/vmlinuz-3.0.101-108.74-default root=/dev/disk/by-uuid/ab6b62bb--
1a8c-45eb-96b1-1fbc535b9265 disk=/dev/sda  USE_BY_UUID_DEVICE_NAMES=1 earlyprinttk=ttyS0 console=ttyS0 rootdelay=300  showopts vga=0x314
```

 이 구성을 사용하면 **계속하려면 아무 키나 누르세요.** 라는 메시지가 5초 동안 콘솔에 표시될 수 있습니다. 

그런 다음, 추가로 5초 동안 GRUB 메뉴를 표시합니다. 아래쪽 화살표를 눌러 카운터를 중단하고 부팅하려는 커널을 선택하여 루트 암호를 설정해야 하는 단일 사용자 모드의 경우 **single** 키워드를 추가합니다.

**init=/bin/bash** 명령을 추가하면 커널이 로드되지만 init 프로그램이 bash 셸로 대체되는지 확인합니다.

암호를 입력하지 않아도 셸에 대한 액세스 권한이 부여됩니다. 그런 다음, 계속해서 Linux 계정의 암호를 업데이트하거나 다른 구성 내용을 변경할 수 있습니다.


## <a name="force-the-kernel-to-a-bash-prompt"></a>Bash 프롬프트에 커널 적용
GRUB에 액세스할 수 있으면 초기화 프로세스를 중단할 수 있습니다. 이 상호 작용은 많은 복구 절차에 유용합니다.
루트 암호가 없고 단일 사용자가 루트 암호를 사용해야 하는 경우 커널을 부팅하여 init 프로그램을 bash 프롬프트로 바꿀 수 있습니다. 커널 부팅 줄에 init=/bin/bash를 추가하면 이 작업이 중단됩니다.

![스크린샷 업데이트 된 부팅 회선이 있는 콘솔을 보여 줍니다.](./media/virtual-machines-serial-console/bash1.png)

명령을 사용하여 /(루트) 파일 시스템 RW를 다시 탑재합니다.

`mount -o remount,rw /`

![다시 탑재 작업을 사용 하는 콘솔을 보여 주는 스크린샷](./media/virtual-machines-serial-console/bash2.png)


이제 루트 암호 변경 또는 많은 다른 Linux 구성 변경을 수행할 수 있습니다.

![루트 암호 및 기타 구성을 변경할 수 있는 콘솔을 보여 주는 스크린샷](./media/virtual-machines-serial-console/bash3.png)

다음으로 VM을 다시 시작합니다. 

`/sbin/reboot -f`




## <a name="single-user-mode"></a>단일 사용자 모드

또는 단일 사용자 또는 긴급 모드에서 VM에 액세스해야 할 수 있습니다. 화살표 키를 사용하여 부팅 또는 중단하려는 커널을 선택합니다.
커널 부팅 줄에 키워드 **single** 또는 **1**을 추가하여 원하는 모드로 전환합니다. RHEL 시스템에서는 **rd.break**를 추가할 수도 있습니다.

단일 사용자 모드에 액세스하는 방법에 관한 자세한 내용은 [이 문서](./serial-console-grub-single-user-mode.md#general-single-user-mode-access)를 참조하세요. 


![single_user_ubuntu](./media/virtual-machines-serial-console/single-user-ubuntu.png)


## <a name="next-steps"></a>다음 단계
[Azure 직렬 콘솔]( ./serial-console-linux.md)에 관한 자세한 정보