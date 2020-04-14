---
title: Azure 직렬 콘솔 사전 GRUB 구성 | 마이크로 소프트 문서
description: Azure 가상 컴퓨터에서 단일 사용자 및 복구 모드 액세스를 허용하는 다양한 배포에서 GRUB을 구성합니다.
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
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/13/2020
ms.locfileid: "81262896"
---
# <a name="proactively-ensuring-you-have-access-to-grub-and-sysrq-could-save-you-lots-of-down-time"></a>GRUB 및 sysrq에 대한 액세스 권한을 사전에 보장하면 많은 가동 중지 시간을 절약할 수 있습니다.

직렬 콘솔 및 GRUB에 액세스하면 대부분의 경우 IaaS Linux 가상 시스템의 복구 시간이 향상됩니다. GRUB은 VM을 복구하는 데 시간이 오래 걸리는 복구 옵션을 제공합니다. 


VM 복구를 수행하는 이유는 많으며 다음과 같은 시나리오에 기인할 수 있습니다.

   - 파일 시스템/커널/MBR(마스터 부팅 레코드)
   - 실패한 커널 업그레이드
   - 잘못된 GRUB 커널 매개 변수
   - 잘못된 fstab 구성
   - 방화벽 구성
   - 분실된 암호
   - 망가진 sshd 구성 파일
   - 네트워킹 구성

 [여기에](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-linux#common-scenarios-for-accessing-the-serial-console) 설명된 다른 많은 시나리오

Azure에 배포된 VM에서 GRUB 및 직렬 콘솔에 액세스할 수 있는지 확인합니다. 

직렬 콘솔을 새로 접하는 경우 [이 링크를](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-linux/)참조하십시오.

> [!TIP]
> 변경하기 전에 파일 백업을 수행해야 합니다.

GRUB에 액세스 한 후 신속하게 리눅스 VM을 복구 할 수있는 방법을 보려면 아래 비디오를 시청

[리눅스 VM 비디오 복구](https://youtu.be/KevOc3d_SG4)

Linux VM을 복구하는 데 도움이 되는 방법에는 여러 가지가 있습니다. 클라우드 환경에서는 이 프로세스가 어려웠습니다.
서비스가 신속하게 복구될 수 있도록 툴링 및 기능에 대한 지속적인 진전이 이루어지고 있습니다.

Azure 직렬 콘솔을 사용하면 시스템 콘솔에 있는 것처럼 Linux VM과 상호 작용할 수 있습니다.

커널이 부팅되는 방법을 포함하여 많은 구성 파일을 조작할 수 있습니다. 

숙련된 Linux/Unix sys 관리자는 Azure 직렬 **콘솔을** 통해 액세스할 수 있는 **단일 사용자** 및 비상 모드를 통해 디스크 스왑 및 VM 삭제를 많은 복구 시나리오에 중복합니다.

복구 방법은 Azure 포털 옵션 -> **암호 재설정**옵션을 통해 분실되거나 잘못 배치된 암호를 재설정할 수 있는 등 발생하는 문제에 따라 달라집니다. **암호 재설정** 기능은 확장이라고 하며 Linux 게스트 에이전트와 통신합니다.

사용자 지정 스크립트와 같은 다른 확장 은 사용할 수 있지만 이러한 옵션을 사용 하려면 이러한 옵션을 사용 해야 합니다 리눅스 **waagent** 항상 경우 되지 않습니다 정상 상태로.

![에이전트 상태](./media/virtual-machines-serial-console/agent-status.png)


Azure 직렬 콘솔 및 GRUB에 액세스할 수 있는지 확인하면 몇 시간이 아닌 몇 분 만에 암호 변경 또는 잘못된 구성을 수정할 수 있습니다. 기본 커널이 손상된 시나리오에서 디스크에 여러 개의 커널이 있는 경우 VM을 대체 커널에서 부팅하도록 강제할 수도 있습니다.

![멀티 커널](./media/virtual-machines-serial-console/more-kernel.png)

## <a name="suggested-order-of-recovery-methods"></a>복구 방법의 제안된 순서:

- Azure 직렬 콘솔

- 디스크 스왑 – 다음 중 하나를 사용하여 자동화할 수 있습니다.

   - [전원 셸 복구 스크립트](https://github.com/Azure/azure-support-scripts/tree/master/VMRecovery/ResourceManager)
   - [bash 복구 스크립트](https://github.com/sribs/azure-support-scripts)

- 레거시 방법

## <a name="disk-swap-video"></a>디스크 스왑 비디오:

GRUB에 액세스할 수 없는 경우 [이](https://youtu.be/m5t0GZ5oGAc) 비디오를 시청하고 디스크 스왑 프로시저를 쉽게 자동화하여 VM을 복구하는 방법을 확인하십시오.

## <a name="challenges"></a>도전:

모든 Linux Azure VM이 GRUB 액세스에 대해 기본적으로 구성되는 것은 아니며 sysrq 명령으로 중단하도록 모두 구성되지도 않습니다. SLES 11과 같은 일부 이전 배포판은 Azure 직렬 콘솔에 로그인 프롬프트를 표시하도록 구성되지 않았습니다.

이 문서에서는 다양 한 리눅스 배포판 및 GRUB를 사용할 수 있도록 하는 방법에 대 한 문서 구성을 검토 합니다.




## <a name="how-to-configure-linux-vm-to-accept-sysrq-keys"></a>SysRq 키를 수락 하도록 리눅스 VM을 구성 하는 방법
sysrq 키는 기본적으로 일부 최신 리눅스 배포판에서 사용할 수 있습니다., 비록 다른 에 그것은 특정 SysRq 기능에 대 한 값을 허용 하도록 구성 될 수 있습니다.
이전 배포형에서 완전히 비활성화될 수 있습니다.

SysRq 기능은 Azure 직렬 콘솔에서 직접 충돌되거나 중단된 VM을 재부팅하는 데 유용하며, GRUB 메뉴에 대한 액세스 권한을 얻는 데 유용하며, 또는 다른 포털 창에서 VM을 다시 시작하거나 ssh 세션을 다시 시작하면 현재 콘솔 연결이 중단되어 GRUB 메뉴를 표시하는 데 사용되는 GRUB 시간 시간이 만료될 수 있습니다.
VM은 커널 매개 변수에 대해 1값을 허용하도록 구성되어야 하며, 이를 통해 sysrq 또는 128의 모든 기능을 활성화하여 재부팅/전원 차단을 허용합니다.


[sysrq 비디오 사용](https://youtu.be/0doqFRrHz_Mc)


Azure 포털에서 SysRq 명령을 통해 재부팅을 수락하도록 VM을 구성하려면 커널 매개 변수 커널 에 대한 값을 1로 설정해야 합니다.sysrq

이 구성이 재부팅을 유지하려면 파일 **sysctl.conf에** 항목을 추가합니다.

`echo kernel.sysrq = 1 >> /etc/sysctl.conf`

커널 매개 변수를 동적으로 구성하려면

`sysctl -w kernel.sysrq=1`

**루트** 액세스 권한이 없거나 sudo가 끊어진 경우 셸 프롬프트에서 sysrq를 구성할 수 없습니다.

Azure 포털을 사용하여 이 시나리오에서 sysrq를 활성화할 수 있습니다. 이 방법은 **sudoers.d/waagent** 파일이 손상되었거나 삭제된 경우에 도움이 될 수 있습니다.

Azure 포털 작업 -> 실행 명령 -> RunShellScript 기능을 사용하면 waagent 프로세스가 정상이어야 다음 sysrq를 사용하도록 하려면 이 명령을 삽입할 수 있습니다.

`sysctl -w kernel.sysrq=1 ; echo kernel.sysrq = 1 >> /etc/sysctl.conf`

여기에 표시된 ![대로: sysrq2 사용](./media/virtual-machines-serial-console/enabling-sysrq-2.png)

완료되면 **sysrq에** 액세스 할 수 있으며 재부팅이 가능한지 확인해야합니다.

![sysrq3 사용](./media/virtual-machines-serial-console/enabling-sysrq-3.png)

**재부팅** 및 **보내기 SysRq** 명령 보내기 선택

![sysrq4 사용](./media/virtual-machines-serial-console/enabling-sysrq-4.png)

시스템은 다음과 같은 재설정 메시지를 기록해야 합니다.

![sysrq5 사용](./media/virtual-machines-serial-console/enabling-sysrq-5.png)


## <a name="ubuntu-grub-configuration"></a>우분투 GRUB 구성

기본적으로 VM 부팅 중에 **Esc** 키를 누루면 GRUB 메뉴가 표시되지 않으면 이러한 옵션 중 하나를 사용하여 Azure 직렬 콘솔의 화면에 GRUB 메뉴를 강제로 유지할 수 있습니다.

**옵션 1** - 화면에 GRUB을 표시하도록 강제 

지정된 시간 시간에 대한 GRUB 메뉴를 화면에 유지하려면 파일 /etc/default/grub.d/50-cloudimg-settings.cfg를 업데이트합니다.
GRUB이 즉시 표시되기 때문에 **Esc를** 칠 필요가 없습니다.

```
GRUB_TIMEOUT=0

change to

GRUB_TIMEOUT=5
```

**옵션 2** - 부팅 하기 전에 **Esc를** 누를 수 있습니다.

파일 /etc/default/grub을 변경하고 **Esc를** 공격하기 위해 3초 의 시간 시간을 관찰하여 유사한 동작을 경험할 수 있습니다.


이 두 줄을 주석지:

```
#GRUB_HIDDEN_TIMEOUT=0
#GRUB_HIDDEN_TIMEOUT_QUIET=true
```
이 줄을 추가합니다.

```
GRUB_TIMEOUT_STYLE=countdown
```


## <a name="ubuntu-1204"></a>우분투 12\.04

우분투 12.04 직렬 콘솔에 대 한 액세스를 허용 하지만 상호 작용 하는 기능을 제공 하지 않습니다. **로그인:** 프롬프트가 표시되지 않음


12.04로그인을 **받으려면:** 프롬프트:
1. 다음 텍스트를 포함하는 /etc/init/ttyS0.conf라는 파일을 만듭니다.

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

2. 시작에 게티를 시작하도록 요청하십시오.     
    ```
    sudo start ttyS0
    ```
 
우분투 버전에 대 한 직렬 콘솔을 구성 하는 데 필요한 [설정은 여기에서](https://help.ubuntu.com/community/SerialConsoleHowto) 찾을 수 있습니다.

## <a name="ubuntu-recovery-mode"></a>우분투 복구 모드

추가 복구 및 정리 옵션 GRUB을 통해 우분투에 사용할 수 있습니다 하지만 이러한 설정은 그에 따라 커널 매개 변수를 구성 하는 경우에 액세스할 수 있습니다.
이 커널 부팅 매개 변수를 구성하지 않으면 복구 메뉴가 Azure Serial Console이 아닌 Azure 진단 유틸리티로 전송됩니다.
다음 단계에 따라 우분투 복구 메뉴에 액세스할 수 있습니다.

부팅 프로세스를 중단하고 GRUB 메뉴에 액세스

우분투에 대 한 고급 옵션을 선택 하 고 입력 을 누릅니다.

![우분1](./media/virtual-machines-serial-console/ubunturec1.png)

입력을 누르지 않고 표시 *선 (복구 모드)를* 선택하지만 "e"를 누르지 마십시오.

![우분투2](./media/virtual-machines-serial-console/ubunturec2.png)

커널을 로드할 선을 찾아 마지막 매개 변수 **nomodeset을** **콘솔=ttyS0으로** 대상으로 대체합니다.

```
linux /boot/vmlinuz-4.15.0-1023-azure root=UUID=21b294f1-25bd-4265-9c4e-d6e4aeb57e97 ro recovery nomodeset

change to

linux /boot/vmlinuz-4.15.0-1023-azure root=UUID=21b294f1-25bd-4265-9c4e-d6e4aeb57e97 ro recovery console=ttyS0
```

![우분3](./media/virtual-machines-serial-console/ubunturec3.png)

**Ctrl-x를** 눌러 커널을 시작하고 로드합니다.
모든 것이 잘되면 다른 복구 옵션을 수행하는 데 도움이 될 수있는 이러한 추가 옵션이 표시됩니다.

![우분4](./media/virtual-machines-serial-console/ubunturec4.png)


## <a name="red-hat-grub-configuration"></a>레드 햇 GRUB 구성

## <a name="red-hat-74-grub-configuration"></a>레드 햇\.\+ 7 4 GRUB 구성
이러한 버전의 기본 /etc/기본/grub 구성이 적절하게 구성됩니다.

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

## <a name="red-hat-72-and-73-grub-configuration"></a>레드 햇\.7\.2 및 7 3 GRUB 구성
수정할 파일은 /etc/default/grub입니다.

```
GRUB_TIMEOUT=1
GRUB_DISTRIBUTOR="$(sed 's, release .*$,,g' /etc/system-release)"
GRUB_DEFAULT=saved
GRUB_DISABLE_SUBMENU=true
GRUB_TERMINAL_OUTPUT="console"
GRUB_CMDLINE_LINUX="console=tty1 console=ttyS0 earlyprintk=ttyS0 rootdelay=300"
GRUB_DISABLE_RECOVERY="true"
```

/etc/기본값/grub에서 다음 줄을 변경합니다.

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

또한 이 줄을 추가합니다.

```
GRUB_SERIAL_COMMAND=”serial –speed=115200 –unit=0 –word=8 –parity=no –stop=1″
```

/etc/기본값/grub은 이제 이 예제와 유사하게 보입니다.

```
GRUB_TIMEOUT=5
GRUB_DISTRIBUTOR="$(sed 's, release .*$,,g' /etc/system-release)"
GRUB_DEFAULT=saved
GRUB_DISABLE_SUBMENU=true
GRUB_TERMINAL="serial console"
GRUB_CMDLINE_LINUX="console=tty1 console=ttyS0 earlyprintk=ttyS0 rootdelay=300"
GRUB_DISABLE_RECOVERY="true"
```
 
사용 하 여 grub 구성 완료 및 업데이트

`grub2-mkconfig -o /boot/grub2/grub.cfg`

SysRq 커널 매개 변수 설정:

`sysctl -w kernel.sysrq = 1;echo kernel.sysrq = 1 >> /etc/sysctl.conf;sysctl -a | grep -i sysrq`

쉘또는 실행 명령을 통해 한 줄을 사용하여 GRUB 및 SysRq를 구성할 수도 있습니다. 이 명령을 실행하기 전에 파일을 백업하십시오.


`cp /etc/default/grub /etc/default/grub.bak; sed -i 's/GRUB_TIMEOUT=1/GRUB_TIMEOUT=5/g' /etc/default/grub; sed -i 's/GRUB_TERMINAL_OUTPUT="console"/GRUB_TERMINAL="serial console"/g' /etc/default/grub; echo "GRUB_SERIAL_COMMAND=\"serial --speed=115200 --unit=0 --word=8 --parity=no --stop=1\"" >> /etc/default/grub;grub2-mkconfig -o /boot/grub2/grub.cfg;sysctl -w kernel.sysrq=1;echo kernel.sysrq = 1 /etc/sysctl.conf;sysctl -a | grep -i sysrq`


## <a name="red-hat-6x-grub-configuration"></a>레드 햇\.6 x GRUB 구성
수정할 파일은 /boot/grub/grub.conf입니다. 이 `timeout` 값은 GRUB이 표시되는 길이를 결정합니다.

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


마지막 줄 *터미널 --시간 시간=5 직렬 콘솔은* **계속하려면 키를 누르는** 메시지를 표시하는 5초의 프롬프트를 추가하여 **GRUB** 시간 시간을 더욱 증가시가지않습니다.

![rh6-1](./media/virtual-machines-serial-console/rh6-1.png)

Esc를 누를 필요 없이 구성된 시간 설정=15에 대해 GRUB 메뉴가 화면에 나타납니다. 브라우저의 콘솔을 클릭하여 메뉴를 활성화하고 필요한 커널을 선택해야 합니다.

![rh6-2](./media/virtual-machines-serial-console/rh6-2.png)

## <a name="suse"></a>Suse

## <a name="sles-12-sp1"></a>SLES 12 sp1
공식 문서에 따라 yast 부트 [로더를 사용하](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-grub-single-user-mode#grub-access-in-suse-sles)

또는 다음 매개 변수를 /etc/기본값/grub에 추가/변경합니다.

```
GRUB_TERMINAL=serial
GRUB_TIMEOUT=5
GRUB_SERIAL_COMMAND="serial --unit=0 --speed=9600 --parity=no"

```
ttys0이 GRUB_CMDLINE_LINUX 또는 GRUB_CMDLINE_LINUX_DEFAULT 사용되는지 확인합니다.

```
GRUB_CMDLINE_LINUX_DEFAULT="console=ttyS0,9600n"
```

grub.cfg 다시 만들기

`grub2-mkconfig -o /boot/grub2/grub.cfg`


## <a name="sles-11-sp4"></a>SLES 11 SP4 
직렬 콘솔이 나타나고 부팅 메시지를 표시하지만 로그인을 표시하지 **않습니다:** 프롬프트

VM에 ssh 세션을 열고이 줄에 주석을 달지 않고 파일 **/ etc / inittab을** 업데이트하십시오.

```
#S0:12345:respawn:/sbin/agetty -L 9600 ttyS0 vt102
```

다음으로 명령을 실행합니다. 

`telinit q`

GRUB을 사용하려면 /boot/grub/menu.lst로 다음 을 변경해야 합니다. 

```
timeout 5
serial --unit=0 --speed=9600 --parity=no
terminal --timeout=5 serial console

root (hd0,0)
kernel /boot/vmlinuz-3.0.101-108.74-default root=/dev/disk/by-uuid/ab6b62bb--
1a8c-45eb-96b1-1fbc535b9265 disk=/dev/sda  USE_BY_UUID_DEVICE_NAMES=1 earlyprinttk=ttyS0 console=ttyS0 rootdelay=300  showopts vga=0x314
```

 이 구성을 사용하면 메시지를 **누르면 모든 키가** 콘솔에 5초 동안 계속 표시됩니다. 

그런 다음 추가 5 초 동안 GRUB 메뉴를 표시합니다 - 아래쪽 화살표를 누르면 카운터를 중단하고 루트 암호를 설정해야하는 단일 사용자 모드에 대한 키워드 **싱글을** 부팅할 커널을 선택합니다.

명령 **init=/bin/bash를** 추가하면 커널이 로드되지만 init 프로그램이 bash 셸로 대체됩니다.

암호를 입력하지 않고도 셸에 액세스할 수 있습니다. 그런 다음 Linux 계정의 암호를 업데이트하거나 다른 구성을 변경할 수 있습니다.


## <a name="force-the-kernel-to-a-bash-prompt"></a>커널을 bash 프롬프트에 강제
GRUB에 액세스하면 초기화 프로세스를 중단할 수 있으므로 이 상호 작용은 많은 복구 절차에 유용합니다.
루트 암호가 없고 단일 사용자가 루트 암호를 가지고 있어야 하는 경우, init 프로그램을 bash 프롬프트로 대체하는 커널을 부팅할 수 있습니다.

![bash1](./media/virtual-machines-serial-console/bash1.png)

명령을 사용하여 / (루트) 파일 시스템 RW를 다시 마운트

`mount -o remount,rw /`

![bash2](./media/virtual-machines-serial-console/bash2.png)


지금 당신은 루트 암호 변경 또는 다른 많은 리눅스 구성 변경을 수행 할 수 있습니다

![bash3](./media/virtual-machines-serial-console/bash3.png)

VM을 함께 다시 시작 

`/sbin/reboot -f`




## <a name="single-user-mode"></a>단일 사용자 모드

또는 단일 사용자 또는 비상 모드에서 VM에 액세스해야 할 수도 있습니다. 화살표 키를 사용하여 부팅하거나 중단할 커널을 선택합니다.
커널 부팅 라인에 **단일** 또는 **1이라는** 키워드를 더하여 원하는 모드를 입력합니다. RHEL **시스템에서rd.break를**추가할 수도 있습니다.

단일 사용자 모드에 액세스하는 방법에 대한 자세한 내용은 [이 문서를](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-grub-single-user-mode#general-single-user-mode-access) 참조하십시오. 


![single_user_ubuntu](./media/virtual-machines-serial-console/single-user-ubuntu.png)


## <a name="next-steps"></a>다음 단계
[Azure 직렬 콘솔에]( https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-linux) 대해 자세히 알아보기
