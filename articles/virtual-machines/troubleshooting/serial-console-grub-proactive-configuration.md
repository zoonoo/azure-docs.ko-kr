---
title: Azure 직렬 콘솔 자동 관리 GRUB 구성 | Microsoft Docs
description: Azure virtual machines에서 단일 사용자 및 복구 모드 액세스를 허용 하는 다양 한 배포에서 GRUB를 구성 합니다.
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
ms.openlocfilehash: 573bd0797e63fc512e59b0e0882c718e4569111c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81262896"
---
# <a name="proactively-ensuring-you-have-access-to-grub-and-sysrq-could-save-you-lots-of-down-time"></a>GRUB 및 sysrq에 쉽게 액세스할 수 있도록 하 여 작동 중단 시간을 많이 줄일 수 있습니다.

직렬 콘솔과 GRUB에 액세스할 수 있으면 대부분의 경우 IaaS Linux 가상 머신의 복구 시간이 개선 됩니다. GRUB는 VM을 복구 하는 데 더 오래 걸리는 복구 옵션을 제공 합니다. 


VM 복구를 수행 하는 이유는 다양 하며 다음과 같은 시나리오의 특성을 사용할 수 있습니다.

   - 손상 된 파일 시스템/커널/MBR (마스터 부트 레코드)
   - 커널 업그레이드 실패
   - 잘못 된 GRUB 커널 매개 변수
   - 잘못 된 fstab 구성
   - 방화벽 구성
   - 암호 손실
   - 손상 sshd 구성 파일
   - 네트워킹 구성

 [여기](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-linux#common-scenarios-for-accessing-the-serial-console) 에 자세히 설명 된 다양 한 시나리오가 있습니다.

Azure에 배포 된 Vm에서 GRUB 및 직렬 콘솔에 액세스할 수 있는지 확인 합니다. 

직렬 콘솔을 처음 접하는 경우 [이 링크](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-linux/)를 참조 하세요.

> [!TIP]
> 변경을 수행 하기 전에 파일의 백업을 수행 하십시오.

GRUB에 액세스할 수 있는 경우 Linux VM을 신속 하 게 복구 하는 방법을 보려면 아래 비디오를 시청 하세요.

[Linux VM 복구 비디오](https://youtu.be/KevOc3d_SG4)

Linux Vm을 복구 하는 데 도움이 되는 여러 가지 방법이 있습니다. 클라우드 환경에서이 프로세스는 어렵습니다.
서비스가 신속 하 게 복구 되도록 하기 위해 지속적으로 도구 및 기능을 진행 하 고 있습니다.

Azure 직렬 콘솔을 사용 하면 시스템의 콘솔에 있는 것 처럼 Linux VM과 상호 작용할 수 있습니다.

커널이 부팅 되는 방법을 포함 하 여 많은 구성 파일을 조작할 수 있습니다. 

숙련 된 Linux/Unix sys 관리자는 Azure 직렬 콘솔을 통해 액세스할 수 있는 **단일 사용자** 및 **응급 모드** 에 감사 하 여 여러 복구 시나리오에서 중복 되는 디스크 교환 및 VM 삭제를 수행 합니다.

복구 방법은 발생 하는 문제에 따라 달라 집니다. 예를 들어 암호를 분실 하거나 잘못 배치 하면 Azure Portal 옵션 > **암호 재설정**을 통해 다시 설정할 수 있습니다. **암호 재설정** 기능은 확장 이라고 하며 Linux 게스트 에이전트와 통신 합니다.

사용자 지정 스크립트와 같은 다른 확장을 사용할 수 있지만 이러한 옵션을 사용 하려면 Linux **waagent** 가 작동 하 고 항상 그렇지 않은 정상 상태 여야 합니다.

![에이전트 상태](./media/virtual-machines-serial-console/agent-status.png)


Azure 직렬 콘솔에 대 한 액세스 권한이 있는지 확인 하 고, GRUB는 시간 대신 몇 분 내에 암호 변경 또는 잘못 된 구성을 수정할 수 있음을 의미 합니다. 주 커널이 손상 된 경우에는 디스크에 여러 커널이 있는 경우에도 VM이 대체 커널에서 부팅 되도록 강제할 수도 있습니다.

![다중 커널](./media/virtual-machines-serial-console/more-kernel.png)

## <a name="suggested-order-of-recovery-methods"></a>권장 복구 방법의 순서:

- Azure 직렬 콘솔

- 디스크 교환 – 다음 중 하나를 사용 하 여 자동화할 수 있습니다.

   - [Power Shell 복구 스크립트](https://github.com/Azure/azure-support-scripts/tree/master/VMRecovery/ResourceManager)
   - [bash 복구 스크립트](https://github.com/sribs/azure-support-scripts)

- 레거시 방법

## <a name="disk-swap-video"></a>디스크 교체 비디오:

GRUB에 액세스할 수 없는 경우 [이](https://youtu.be/m5t0GZ5oGAc) 비디오를 시청 하 고, 디스크 교체 절차를 쉽게 자동화 하 여 VM을 복구 하는 방법을 참조 하세요.

## <a name="challenges"></a>어려운

일부 Linux Azure Vm은 GRUB 액세스에 대해 기본적으로 구성 되지 않으며 sysrq 명령을 사용 하 여 중단 하도록 구성 되지 않습니다. SLES 11과 같은 일부 이전 배포판은 Azure 직렬 콘솔에서 로그인 프롬프트를 표시 하도록 구성 되어 있지 않습니다.

이 문서에서는 GRUB를 사용 가능 하 게 설정 하는 방법에 대 한 다양 한 Linux 배포판 및 문서 구성을 검토 합니다.




## <a name="how-to-configure-linux-vm-to-accept-sysrq-keys"></a>SysRq 키를 허용 하도록 Linux VM을 구성 하는 방법
Sysrq 키는 기본적으로 일부 최신 Linux 배포판에서 사용 하도록 설정 되어 있지만 다른 사용자는 특정 SysRq 함수에 대해서만 값을 허용 하도록 구성 될 수 있습니다.
이전 배포판에는 완전히 사용 하지 않도록 설정 되어 있을 수 있습니다.

SysRq 기능은 Azure 직렬 콘솔에서 직접 손상 되거나 정지 된 VM을 다시 부팅 하는 데 유용 합니다. 또한 GRUB 메뉴에 액세스 하거나 다른 포털 창에서 VM을 다시 시작 하거나 ssh 세션에서 현재 콘솔 연결을 삭제 하 여 GRUB 메뉴를 표시 하는 데 사용 되는 GRUB 시간 제한이 만료 될 수 있습니다.
커널 매개 변수에 1 값을 허용 하도록 VM을 구성 해야 합니다 .이 경우 sysrq 또는 128의 모든 기능을 사용할 수 있습니다 .이를 통해 다시 부팅/전원 꺼짐 수 있습니다.


[Sysrq 비디오 사용](https://youtu.be/0doqFRrHz_Mc)


Azure Portal에서 SysRq 명령을 통해 재부팅을 허용 하도록 VM을 구성 하려면 커널 매개 변수 커널의 값을 1로 설정 해야 합니다. sysrq

이 구성을 다시 부팅을 유지 하려면 sysctl 파일에 항목을 추가 **합니다.**

`echo kernel.sysrq = 1 >> /etc/sysctl.conf`

커널 매개 변수를 동적으로 구성 하려면

`sysctl -w kernel.sysrq=1`

**루트** 액세스 권한이 없거나 sudo가 손상 된 경우 셸 프롬프트에서 sysrq를 구성 하지 못할 수 있습니다.

Azure Portal를 사용 하 여이 시나리오에서 sysrq를 사용 하도록 설정할 수 있습니다. **Sudoers/waagent** 파일이 끊어졌거나 삭제 된 경우이 방법이 유용할 수 있습니다.

Azure Portal 작업-> RunShellScript 기능 실행 > waagent 프로세스가 정상 상태 여야 합니다. 그런 다음이 명령을 삽입 하 여 sysrq를 사용 하도록 설정할 수 있습니다.

`sysctl -w kernel.sysrq=1 ; echo kernel.sysrq = 1 >> /etc/sysctl.conf`

Sysrq2 사용을 참조 ![하세요.](./media/virtual-machines-serial-console/enabling-sysrq-2.png)

완료 되 면 **sysrq** 에 액세스를 시도할 수 있으며 다시 부팅이 가능 하다는 것을 알 수 있습니다.

![sysrq3 사용](./media/virtual-machines-serial-console/enabling-sysrq-3.png)

**다시 부팅** 및 **Send sysrq** 명령을 선택 합니다.

![sysrq4 사용](./media/virtual-machines-serial-console/enabling-sysrq-4.png)

시스템이 다음과 같은 다시 설정 메시지를 기록해 야 합니다.

![sysrq5 사용](./media/virtual-machines-serial-console/enabling-sysrq-5.png)


## <a name="ubuntu-grub-configuration"></a>Ubuntu GRUB 구성

기본적으로 VM 부팅 중에 **Esc** 키를 누른 채 grub에 액세스할 수 있어야 합니다. grub 메뉴가 표시 되지 않는 경우에는 이러한 옵션 중 하나를 사용 하 여 Azure 직렬 콘솔에서 grub 메뉴를 강제로 실행 하 고 유지할 수 있습니다.

**옵션 1** -GRUB가 화면에 표시 되도록 합니다. 

/Etc/default/grub.d/50-cloudimg-settings.cfg 파일을 업데이트 하 여 지정 된 시간 제한 내에 GRUB 메뉴를 화면에 유지 합니다.
GRUB가 즉시 표시 되기 때문에 **Esc 키** 를 누를 필요는 없습니다.

```
GRUB_TIMEOUT=0

change to

GRUB_TIMEOUT=5
```

**옵션 2** -부팅 하기 전에 **Esc 키** 를 누를 수 있습니다.

/Etc/default/grub 파일을 변경 하 고 3 초의 시간 제한을 관찰 하 여 **Esc 키** 를 누르면 비슷한 동작이 발생할 수 있습니다.


다음 두 줄을 주석 처리 합니다.

```
#GRUB_HIDDEN_TIMEOUT=0
#GRUB_HIDDEN_TIMEOUT_QUIET=true
```
다음 줄을 추가 합니다.

```
GRUB_TIMEOUT_STYLE=countdown
```


## <a name="ubuntu-1204"></a>Ubuntu 12\.04

Ubuntu 12.04은 직렬 콘솔에 대 한 액세스를 허용 하지만 상호 작용 하는 기능은 제공 하지 않습니다. **로그인:** 프롬프트가 표시 되지 않습니다.


12.04에서 **로그인** 을 가져오려면 프롬프트:
1. 다음 텍스트를 포함 하는/etc/init/ttyS0.conf 라는 파일을 만듭니다.

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

2. Getty를 시작 하도록 요청 합니다.     
    ```
    sudo start ttyS0
    ```
 
Ubuntu 버전용 직렬 콘솔을 구성 하는 데 필요한 설정은 [여기](https://help.ubuntu.com/community/SerialConsoleHowto) 에서 찾을 수 있습니다.

## <a name="ubuntu-recovery-mode"></a>Ubuntu 복구 모드

GRUB를 통해 Ubuntu에서 추가 복구 및 정리 옵션을 사용할 수 있지만, 이러한 설정은 커널 매개 변수를 적절 하 게 구성 하는 경우에만 액세스할 수 있습니다.
이 커널 부팅 매개 변수를 구성 하지 않으면 복구 메뉴가 Azure 직렬 콘솔이 아닌 Azure 진단으로 전송 됩니다.
다음 단계를 수행 하 여 Ubuntu 복구 메뉴에 대 한 액세스 권한을 얻을 수 있습니다.

부팅 프로세스를 중단 하 고 GRUB 메뉴에 액세스 합니다.

Ubuntu에 대 한 고급 옵션을 선택 하 고 enter 키를 누릅니다.

![ubunturec1](./media/virtual-machines-serial-console/ubunturec1.png)

표시 되는 줄을 선택 합니다 *(복구 모드)* enter 키를 누르지 말고 "e"를 누릅니다.

![ubunturec2](./media/virtual-machines-serial-console/ubunturec2.png)

커널을 로드 하 고 마지막 매개 변수 **nomodeset** 을 destination으로 **console = ttyS0** 으로 대체 하는 줄을 찾습니다.

```
linux /boot/vmlinuz-4.15.0-1023-azure root=UUID=21b294f1-25bd-4265-9c4e-d6e4aeb57e97 ro recovery nomodeset

change to

linux /boot/vmlinuz-4.15.0-1023-azure root=UUID=21b294f1-25bd-4265-9c4e-d6e4aeb57e97 ro recovery console=ttyS0
```

![ubunturec3](./media/virtual-machines-serial-console/ubunturec3.png)

**Ctrl + x** 를 눌러 커널을 시작 하 고 로드 합니다.
모두 제대로 작동 하는 경우 다른 복구 옵션을 수행 하는 데 도움이 될 수 있는 추가 옵션이 표시 됩니다.

![ubunturec4](./media/virtual-machines-serial-console/ubunturec4.png)


## <a name="red-hat-grub-configuration"></a>Red Hat GRUB 구성

## <a name="red-hat-74-grub-configuration"></a>Red Hat 7\.4\+ GRUB 구성
이러한 버전에 대 한 기본/etc/default/grub 구성이 적절히 구성 되어 있습니다.

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
수정할 파일은/etc/default/grub – 기본 구성은 다음 예제와 같습니다.

```
GRUB_TIMEOUT=1
GRUB_DISTRIBUTOR="$(sed 's, release .*$,,g' /etc/system-release)"
GRUB_DEFAULT=saved
GRUB_DISABLE_SUBMENU=true
GRUB_TERMINAL_OUTPUT="console"
GRUB_CMDLINE_LINUX="console=tty1 console=ttyS0 earlyprintk=ttyS0 rootdelay=300"
GRUB_DISABLE_RECOVERY="true"
```

/Etc/default/grub에서 다음 줄을 변경 합니다.

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

다음 줄도 추가 합니다.

```
GRUB_SERIAL_COMMAND=”serial –speed=115200 –unit=0 –word=8 –parity=no –stop=1″
```

이제/etc/default/grub는 다음 예와 같이 표시 됩니다.

```
GRUB_TIMEOUT=5
GRUB_DISTRIBUTOR="$(sed 's, release .*$,,g' /etc/system-release)"
GRUB_DEFAULT=saved
GRUB_DISABLE_SUBMENU=true
GRUB_TERMINAL="serial console"
GRUB_CMDLINE_LINUX="console=tty1 console=ttyS0 earlyprintk=ttyS0 rootdelay=300"
GRUB_DISABLE_RECOVERY="true"
```
 
를 사용 하 여 grub 구성 완료 및 업데이트

`grub2-mkconfig -o /boot/grub2/grub.cfg`

SysRq 커널 매개 변수를 설정 합니다.

`sysctl -w kernel.sysrq = 1;echo kernel.sysrq = 1 >> /etc/sysctl.conf;sysctl -a | grep -i sysrq`

또는 셸에서 단일 줄을 사용 하 여 또는 실행 명령을 통해 GRUB 및 SysRq를 구성할 수 있습니다. 이 명령을 실행 하기 전에 파일을 백업 합니다.


`cp /etc/default/grub /etc/default/grub.bak; sed -i 's/GRUB_TIMEOUT=1/GRUB_TIMEOUT=5/g' /etc/default/grub; sed -i 's/GRUB_TERMINAL_OUTPUT="console"/GRUB_TERMINAL="serial console"/g' /etc/default/grub; echo "GRUB_SERIAL_COMMAND=\"serial --speed=115200 --unit=0 --word=8 --parity=no --stop=1\"" >> /etc/default/grub;grub2-mkconfig -o /boot/grub2/grub.cfg;sysctl -w kernel.sysrq=1;echo kernel.sysrq = 1 /etc/sysctl.conf;sysctl -a | grep -i sysrq`


## <a name="red-hat-6x-grub-configuration"></a>Red Hat 6\.x GRUB 구성
수정할 파일은/boot/grub/grub.conf.입니다. 이 `timeout` 값은에 대해 GRUB가 표시 되는 기간을 결정 합니다.

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


마지막 줄 *터미널 –-timeout = 5 직렬 콘솔* 은 **계속 하려면 아무 키나 눌러** 표시 하는 프롬프트를 5 초에 추가 하 여 **GRUB** 제한 시간을 더 늘립니다.

![rh6-1](./media/virtual-machines-serial-console/rh6-1.png)

Esc 키를 누르지 않아도 구성 된 시간 제한 = 15에 대해 GRUB 메뉴가 화면에 표시 됩니다. 브라우저에서 콘솔을 클릭 하 여 메뉴를 활성화 하 고 필요한 커널을 선택 해야 합니다.

![rh6-2](./media/virtual-machines-serial-console/rh6-2.png)

## <a name="suse"></a>SuSE

## <a name="sles-12-sp1"></a>SLES 12 sp1
공식 [문서](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-grub-single-user-mode#grub-access-in-suse-sles) 에 따라 yast 부팅 로더를 사용 합니다.

또는/etc/default/grub를 추가/변경 하 여 다음 매개 변수를 변경 합니다.

```
GRUB_TERMINAL=serial
GRUB_TIMEOUT=5
GRUB_SERIAL_COMMAND="serial --unit=0 --speed=9600 --parity=no"

```
Ttys0이 GRUB_CMDLINE_LINUX에 사용 되는지 또는 GRUB_CMDLINE_LINUX_DEFAULT에 사용 되는지 확인 합니다.

```
GRUB_CMDLINE_LINUX_DEFAULT="console=ttyS0,9600n"
```

Grub를 다시 만듭니다. cfg

`grub2-mkconfig -o /boot/grub2/grub.cfg`


## <a name="sles-11-sp4"></a>SLES 11 SP4 
직렬 콘솔이 나타나면서 부팅 메시지를 표시 하지만 **로그인:** 프롬프트를 표시 하지 않습니다.

VM에 대 한 ssh 세션을 열고 다음 줄의 주석 처리를 취소 하 여 **/etc/inittab** 파일을 업데이트 합니다.

```
#S0:12345:respawn:/sbin/agetty -L 9600 ttyS0 vt102
```

다음 명령을 실행 합니다. 

`telinit q`

GRUB를 사용 하도록 설정 하려면/boot/grub/menu.lst에서 다음과 같이 변경 해야 합니다. 

```
timeout 5
serial --unit=0 --speed=9600 --parity=no
terminal --timeout=5 serial console

root (hd0,0)
kernel /boot/vmlinuz-3.0.101-108.74-default root=/dev/disk/by-uuid/ab6b62bb--
1a8c-45eb-96b1-1fbc535b9265 disk=/dev/sda  USE_BY_UUID_DEVICE_NAMES=1 earlyprinttk=ttyS0 console=ttyS0 rootdelay=300  showopts vga=0x314
```

 이 구성을 사용 하면 5 초 동안 **모든 키를 눌러** 콘솔에 계속 표시 되도록 메시지를 사용할 수 있습니다. 

그러면 추가 5 초 동안 GRUB 메뉴가 표시 됩니다. 아래쪽 화살표를 눌러 카운터를 중단 하 고 부팅 하려는 커널을 선택 하 여 루트 암호를 설정 해야 하는 단일 사용자 모드에 대 한 **단일** 키워드를 추가 합니다.

**Init =/bin/bash** 명령을 추가 하면 커널이 로드 되지만 init 프로그램이 bash 셸로 교체 됩니다.

암호를 입력 하지 않고 셸에 액세스할 수 있습니다. 그런 다음 Linux 계정에 대 한 암호 업데이트를 진행 하거나 다른 구성을 변경할 수 있습니다.


## <a name="force-the-kernel-to-a-bash-prompt"></a>Bash 프롬프트에 커널 강제 적용
GRUB에 액세스할 수 있으면 초기화 프로세스를 중단할 수 있습니다 .이 상호 작용은 많은 복구 절차에 유용 합니다.
루트 암호가 없고 단일 사용자가 루트 암호를 사용 해야 하는 경우 커널을 부팅 하 여 init 프로그램을 bash 프롬프트로 바꿀 수 있습니다.-이 인터럽트는 커널 부팅 줄에 init =/bin/bash를 추가 하 여 수행할 수 있습니다.

![bash1](./media/virtual-machines-serial-console/bash1.png)

명령을 사용 하 여/(루트) 파일 시스템 RW를 다시 탑재 합니다.

`mount -o remount,rw /`

![bash2](./media/virtual-machines-serial-console/bash2.png)


이제 루트 암호 변경 또는 기타 많은 Linux 구성 변경을 수행할 수 있습니다.

![bash3](./media/virtual-machines-serial-console/bash3.png)

VM을 다시 시작 합니다. 

`/sbin/reboot -f`




## <a name="single-user-mode"></a>단일 사용자 모드

또는 단일 사용자 또는 응급 모드에서 VM에 액세스 해야 할 수도 있습니다. 화살표 키를 사용 하 여 부팅 하거나 중단 하려는 커널을 선택 합니다.
커널 부팅 줄에 **단일** 또는 **1** 키워드를 추가 하 여 원하는 모드를 입력 합니다. RHEL 시스템에서는 **rd**을 추가할 수도 있습니다.

단일 사용자 모드에 액세스 하는 방법에 대 한 자세한 내용은 [이 문서](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-grub-single-user-mode#general-single-user-mode-access) 를 참조 하세요. 


![single_user_ubuntu](./media/virtual-machines-serial-console/single-user-ubuntu.png)


## <a name="next-steps"></a>다음 단계
[Azure 직렬 콘솔]( https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-linux) 에 대 한 자세한 정보
