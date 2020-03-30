---
title: 그럽 구조에 리눅스 VM 부츠
description: 가상 시스템이 구조 콘솔에 입력했기 때문에 가상 컴퓨터를 부팅하지 못했습니다.
services: virtual-machines-windows
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: troubleshooting
ms.date: 08/28/2019
ms.author: tiag
ms.openlocfilehash: c24a840716841d04537ac5b77bcaf26fca4b78cf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77561952"
---
# <a name="linux-vm-boots-to-grub-rescue"></a>그럽 구조에 리눅스 VM 부츠

VM(가상 머신)이 구조 콘솔에 들어갔는 것을 확인했습니다. 이 문제는 Linux VM에서 커널 업그레이드와 같이 최근에 커널 변경 사항이 적용되어 부팅 프로세스 중 커널 오류로 인해 더 이상 제대로 시작되지 않는 경우에 발생합니다. 부팅 프로세스 중에 부트 로더가 Linux 커널을 찾아 부팅 제어를 해제하려고 하면 VM이 핸드오프가 실패하면 구조 콘솔에 들어갑니다.

나중에 VM에 연결할 수 없는 경우 Azure 포털에서 부팅 진단 블레이드를 사용하여 VM의 스크린샷을 볼 수 있습니다. 이렇게 하면 문제를 진단하고 유사한 부트 오류가 원인인지 확인하는 데 도움이 됩니다.

## <a name="recommended-steps"></a>권장되는 단계

수신되는 오류에 따라 아래 완화 단계를 따르십시오.

### <a name="error---unknown-filesystem"></a>오류 - 알 수 없는 파일 시스템

* **알 수 없는 파일 시스템에서**오류가 발생하는 경우 이 오류는 부팅 파티션의 파일 시스템 손상 또는 잘못된 커널 구성으로 인해 발생할 수 있습니다.

   * 파일 시스템 문제의 경우, 문서 리눅스 복구의 단계를 따르십시오 [: 파일 시스템 오류 (fsck, inodes)로 인해 리눅스 VM에 SSH 를 할 수 없습니다](https://blogs.msdn.microsoft.com/linuxonazure/2016/09/13/linux-recovery-cannot-ssh-to-linux-vm-due-to-file-system-errors-fsck-inodes/).
   * 커널 문제의 경우, 문서 [리눅스 복구의 단계를 따르십시오: 수동으로 커널 문제와 관련된 비 부팅 문제를 해결](https://blogs.msdn.microsoft.com/linuxonazure/2016/10/09/linux-recovery-manually-fixing-non-boot-issues-related-to-kernel-problems/), 또는 [리눅스 복구 : chroot를 사용하여 커널 문제와 관련된 비 부팅 문제를 해결](https://blogs.msdn.microsoft.com/linuxonazure/2016/10/09/linux-recovery-fixing-non-boot-issues-related-to-kernel-problems-using-chroot/).
   
### <a name="error---file-not-found"></a>오류 - 파일을 찾을 수 없습니다.

* **오류 오류 15: 파일을 찾을 수 없거나 초기 RAM 디스크** 또는 **initrd/initramfs 파일을 찾을 수 없는**경우 아래 단계를 따르십시오.

    * 누락된 `/boot/grub2/grub.cfg` 파일의 `initrd/initramfs` 경우 다음 프로세스를 진행하십시오.

    1. 올바른/원하는 설정이 있는지 확인합니다. `/etc/default/grub` 기본 설정을 모르는 경우 작업 중인 VM으로 확인할 수 있습니다.

    2. 그런 다음 다음 명령을 실행하여 구성을 다시 생성합니다.`grub2-mkconfig -o /boot/grub2/grub.cfg`

   * 누락 `/boot/grub/menu.lst`된 파일인 경우이 오류는 이전 OS 버전 **(RHEL 6.x,** **Centos 6.x** 및 **우분투 14.04)에**대한 것이므로 명령이 다를 수 있습니다. 이전 서버를 스핀업하고 테스트하여 올바른 명령이 제공되는지 확인해야 합니다.

### <a name="error---no-such-partition"></a>오류 - 이러한 파티션 없음

* 오류가 발생하면 **이러한 파티션 없음은**대/소문자 시나리오를 참조 : [OS 드라이브를 확장하려고 시도한 후 VM을 시작하려고하는 동안 "이러한 파티션 없음"오류를](https://blogs.technet.microsoft.com/shwetanayak/2017/03/12/case-scenario-no-such-partition-error-while-trying-to-start-the-vm-after-attempting-to-extend-the-os-drive/)참조하십시오.

### <a name="error---grubcfg-file-not-found"></a>오류 - grub.cfg 파일을 찾을 수 없습니다.

* 오류 **/boot/grub2/grub.cfg 파일을 찾을 수 없는**경우 아래 단계를 따르십시오.

    * 누락된 `/boot/grub2/grub.cfg` 파일의 `initrd/initramfs` 경우 다음 프로세스를 진행하십시오.

    1. 올바른/원하는 설정이 있는지 확인합니다. `/etc/default/grub` 기본 설정을 모르는 경우 작업 중인 VM으로 확인할 수 있습니다.

    2. 그런 다음 다음 명령을 실행하여 구성을 다시 `grub2-mkconfig -o /boot/grub2/grub.cfg`생성합니다.

   * 누락 `/boot/grub/menu.lst`된 파일인 경우이 오류는 이전 OS 버전 **(RHEL 6.x,** **Centos 6.x** 및 **우분투 14.04)에**대한 것이므로 명령이 연기 될 수 있습니다. 이전 서버를 스핀업하고 테스트하여 올바른 명령이 제공되는지 확인합니다.

## <a name="next-steps"></a>다음 단계

* [Azure 가상 머신 에이전트 개요](../extensions/agent-windows.md)
* [Windows용 가상 머신 확장 및 기능](../extensions/features-windows.md)

