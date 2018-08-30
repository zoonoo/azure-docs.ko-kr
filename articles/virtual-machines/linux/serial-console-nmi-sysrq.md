---
title: SysRq 및 NMI 호출에 대한 Azure 직렬 콘솔 | Microsoft Docs
description: Azure Virtual Machines에서 SysRq 및 NMI 호출에 대한 직렬 콘솔 사용
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
ms.openlocfilehash: 110bdcacf7433c1e0ab0cb31e1a04734137f9596
ms.sourcegitcommit: f1e6e61807634bce56a64c00447bf819438db1b8
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/24/2018
ms.locfileid: "42885242"
---
# <a name="use-serial-console-for-sysrq-and-nmi-calls"></a>SysRq 및 NMI 호출에 대한 직렬 콘솔 사용

## <a name="system-request-sysrq"></a>시스템 요청(SysRq)
SysRq는 일련의 미리 정의된 작업을 트리거할 수 있는 Linux 작업 시스템 커널에서 인식되는 키의 시퀀스입니다. 이러한 명령은 보통 기존 관리를 통해 가상 머신 문제 해결 또는 복구를 수행할 수 없는 경우(예: VM이 중지되는 경우) 사용됩니다. Azure 직렬 콘솔의 SysRq 기능을 사용하면 실제 키보드에서 입력한 문자와 SysRq 키 누르기를 가장합니다.

SysRq 시퀀스가 전달되면 커널 구성이 시스템의 응답을 제어하게 됩니다. SysRq의 설정 및 해제에 대한 내용은 *SysRq 관리자 가이드* [텍스트](https://aka.ms/kernelorgsysreqdoc) | [markdown](https://aka.ms/linuxsysrq)를 참조하세요.  

아래 표시된 명령줄에서 키보드 아이콘을 사용하여 SysRq를 Azure Virtual Machine에 전송하는 데 Azure 직렬 콘솔을 사용할 수 있습니다.

![](../media/virtual-machines-serial-console/virtual-machine-serial-console-command-menu.jpg)

“SysRq 명령 보내기”를 선택하면 일반적인 SysRq 옵션을 제공하거나 대화 상자에 입력된 SysRq 명령의 시퀀스를 허용하는 대화 상자가 열립니다.  이렇게 하면 일련의 SysRq에서 `REISUB`를 사용하여 안전한 부팅처럼 높은 수준의 작업을 수행할 수 있습니다.

![](../media/virtual-machines-serial-console/virtual-machine-serial-console-sysreq_UI.png)

SysRq 명령은 중지된 가상 머신 또는 해당 커널이 응답하지 않는 상태의 가상 머신에서 사용할 수 없습니다. (예: 커널 패닉)

### <a name="enable-sysrq"></a>SysRq를 사용하도록 설정 
위의 *SysRq 관리자 가이드*에 설명된 대로 모두, 없음 또는 특정 명령만 사용할 수 있도록 SysRq를 구성할 수 있습니다. 아래 단계를 사용하여 모든 SysRq 명령을 사용하도록 설정할 수 있지만 재부팅하면 해제됩니다.
```
echo "1" >/proc/sys/kernel/sysrq
```
SysReq 구성을 영구적으로 유지하려면 다음을 수행하여 모든 SysRq 명령을 사용하도록 설정할 수 있습니다.
1. 이 줄을 */etc/sysctl.conf*에 추가 <br>
    `kernel.sysrq = 1`
1. 다음을 실행하여 다시 부팅하거나 sysctl 업데이트 <br>
    `sysctl -p`

### <a name="command-keys"></a>명령 키 
위의 SysRq 관리자 가이드에서:

|명령| 함수
| ------| ----------- |
|``b``  |   디스크를 동기화 또는 분리하지 않고 시스템을 즉시 재부팅합니다.
|``c``  |   NULL 포인터 역참조에 의해 시스템 크래시를 수행합니다. 구성된 경우 크래시덤프가 수행됩니다.
|``d``  |   유지되는 모든 잠금을 표시합니다.
|``e``  |   init를 제외한 모든 프로세스에 SIGTERM을 보냅니다.
|``f``  |   oom killer를 호출하여 메모리 독점 프로세스를 종료합니다. 단, 아무것도 종료시킬 수 없는 경우 당황하지 마십시오.
|``g``  |   kgdb에서 사용됨(커널 디버거)
|``h``  |   도움말을 표시합니다(여기에 나열된 것 외에 다른 키도 도움말을 표시하지만 ``h``가 기억하기 쉬움 :-).
|``i``  |    init를 제외한 모든 프로세스에 SIGKILL을 보냅니다.
|``j``  |    FIFREEZE ioctl에 의해 중단된 파일시스템을 강제로 “재개합니다”.
|``k``  |    SAK(보안 액세스 키)가 현재 가상 콘솔에 있는 모든 프로그램을 종료합니다. 참고: SAK 섹션에서 아래 중요한 설명을 참조하세요.
|``l``  |    모든 활성 CPU에 대한 스택 backtrace를 표시합니다.
|``m``  |    콘솔에 현재 메모리 정보를 덤프합니다.
|``n``  |    RT 작업을 잘 수행할 수 있도록 하는 데 사용됩니다.
|``o``  |    시스템을 종료합니다(구성 및 지원되는 경우).
|``p``  |    콘솔에 현재 등록 및 플래그를 덤프합니다.
|``q``  |    모든 무장한 hrtimer의 CPU별 목록(단, 일반 timer_list 타이머가 아님) 및 모든 clockevent 장치에 대한 자세한 정보를 덤프합니다.
|``r``  |    키보드 원시 모드를 해제하고 XLATE로 설정합니다.
|``s``  |    탑재된 모든 파일 시스템의 동기화를 시도합니다.
|``t``  |    콘솔에 현재 작업 목록과 해당 정보를 덤프합니다.
|``u``  |    모든 탑재된 읽기 전용 파일 시스템을 다시 탑재하려고 시도합니다.
|``v``  |    프레임 버퍼 콘솔을 강제로 복원합니다.
|``v``  |    ETM 버퍼 덤프[ARM 전용]로 이어집니다.
|``w``  |    무정전(차단됨) 상태에 있는 작업을 덤프합니다.
|``x``  |    ppc/powerpc 플랫폼에 있는 xmon 인터페이스에서 사용됩니다. sparc64에서 글로벌 PMU 레지스터를 표시합니다. MIPS에서 모든 TLB 엔트리를 덤프합니다.
|``y``  |    글로벌 CPU 레지스터[SPARC-64 전용]를 표시합니다.
|``z``  |    ftrace 버퍼를 덤프합니다.
|``0``-``9`` | 콘솔에 출력되는 커널 메시지를 제어하여 콘솔 로그 수준을 설정합니다. (예를 들어 ``0``은 PANIC이나 OOPS와 같은 응급 메시지만 콘솔에 표시되도록 합니다.)

### <a name="distribution-specific-documentation"></a>배포 관련 설명서 ###
SysRq의 배포 관련 설명서 및 SysRq “Crash” 명령을 수신하는 경우 크래시 덤프를 만들도록 Linux를 구성하는 단계는 아래 링크를 참조하세요.

#### <a name="ubuntu"></a>Ubuntu ####
 - [커널 크래시 덤프](https://help.ubuntu.com/lts/serverguide/kernel-crash-dump.html)

#### <a name="red-hat"></a>Red Hat ####
- [SysRq 기능이란 무엇이며 어떻게 사용하나요?](https://access.redhat.com/articles/231663)
- [SysRq 기능을 사용하여 RHEL 서버에서 정보를 수집하는 방법](https://access.redhat.com/solutions/2023)

#### <a name="suse"></a>SUSE ####
- [커널 코어 덤프 캡처 구성](https://www.suse.com/support/kb/doc/?id=3374462)

#### <a name="coreos"></a>CoreOS ####
- [크래시 로그 수집](https://coreos.com/os/docs/latest/collecting-crash-logs.html)

## <a name="non-maskable-interrupt-nmi"></a>NMI(마스크 불가능 인터럽트) 
NMI(마스크 불가능 인터럽트)는 가상 머신에 있는 소프트웨어가 무시하는 신호를 만들도록 설계되었습니다. 지금까지 NMI는 특정 응답 시간이 필요한 시스템에서 하드웨어 문제를 모니터링하는 데 사용되었습니다.  현재, 프로그래머 및 시스템 관리자는 종종 중지된 시스템을 디버그하거나 문제를 해결하기 위한 메커니즘으로 NMI를 사용합니다.

아래 표시된 명령줄에서 키보드 아이콘을 사용하여 NMI를 Azure Virtual Machine에 전송하는 데 직렬 콘솔을 사용할 수 있습니다. NMI가 전달되면 가상 머신 구성이 시스템의 응답을 제어하게 됩니다.  운영 체제가 NMI를 수신하는 메모리 덤프를 크래시하고 만들도록 Linux 운영 체제를 구성할 수 있습니다.

![](../media/virtual-machines-serial-console/virtual-machine-serial-console-command-menu.jpg) <br>

커널 매개 변수를 구성하기 위해 sysctl을 지원하는 Linux 시스템의 경우 다음을 사용하여 이 NMI을 받으면 패닉을 사용하도록 설정할 수 있습니다.
1. 이 줄을 */etc/sysctl.conf*에 추가 <br>
    `kernel.panic_on_unrecovered_nmi=1`
1. 다음을 실행하여 다시 부팅하거나 sysctl 업데이트 <br>
    `sysctl -p`

`unknown_nmi_panic`, `panic_on_io_nmi` 및 `panic_on_unrecovered_nmi`를 비롯한 Linux 커널 구성에 대한 자세한 내용은 [/proc/sys/kernel/에 대한 설명서*](https://www.kernel.org/doc/Documentation/sysctl/kernel.txt)를 참조하세요. NMI의 배포 관련 설명서 및 NMI를 수신하는 경우 크래시 덤프를 만들도록 Linux를 구성하는 단계는 아래 링크를 참조하세요.
 
### <a name="ubuntu"></a>Ubuntu 
 - [커널 크래시 덤프](https://help.ubuntu.com/lts/serverguide/kernel-crash-dump.html)

### <a name="red-hat"></a>Red Hat 
 - [NMI란 무엇이며 어디에 사용할 수 있나요?](https://access.redhat.com/solutions/4127)
 - [NMI 스위치가 푸시될 때 크래시하도록 내 시스템을 구성하는 방법](https://access.redhat.com/solutions/125103)
 - [크래시 덤프 관리자 가이드](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/pdf/kernel_crash_dump_guide/kernel-crash-dump-guide.pdf)

### <a name="suse"></a>SUSE 
- [커널 코어 덤프 캡처 구성](https://www.suse.com/support/kb/doc/?id=3374462)

### <a name="coreos"></a>CoreOS 
- [크래시 로그 수집](https://coreos.com/os/docs/latest/collecting-crash-logs.html)

## <a name="next-steps"></a>다음 단계
* 주 직렬 콘솔 Linux 설명서 페이지는 [여기](serial-console.md)에 있습니다.
* 직렬 콘솔을 사용하여 [GRUB로 부팅하고 단일 사용자 모드로 전환](serial-console-grub-single-user-mode.md)
* [Windows](../windows/serial-console.md) VM에서도 직렬 콘솔 사용 가능
* [부트 진단](boot-diagnostics.md)에 대해 자세히 알아보기