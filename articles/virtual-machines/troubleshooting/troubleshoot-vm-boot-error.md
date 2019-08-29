---
title: Azure 가상 머신 부팅 오류
description: 가상 머신이 복구 콘솔에 들어간 가상 머신을 부팅 하지 못했습니다.
services: virtual-machines-windows
documentationcenter: ''
author: v-miegge
manager: ''
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 08/28/2019
ms.author: tiag
ms.openlocfilehash: 14bf7b289f3d03dd1437a18b0b5bd35fdec567ea
ms.sourcegitcommit: d200cd7f4de113291fbd57e573ada042a393e545
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/29/2019
ms.locfileid: "70143311"
---
# <a name="vm-boot-error"></a>VM 부팅 오류

VM (가상 컴퓨터)에서 복구 콘솔을 입력 한 것으로 확인 되었습니다. 이 문제는 Linux VM이 최근에 커널 업그레이드와 같은 커널 변경 내용을 적용 했 고 부팅 프로세스 중 커널 오류로 인해 더 이상 제대로 시작 되지 않을 때 발생 합니다. 부팅 프로세스 중에 부팅 로더에서 Linux 커널을 찾아 부팅 제어를 해제 하려고 하면 전달에 실패 하는 경우 VM이 복구 콘솔에 들어갑니다.

나중에 VM에 연결할 수 없는 경우 Azure Portal의 부트 진단 블레이드를 사용 하 여 VM의 스크린샷을 볼 수 있습니다. 이렇게 하면 문제를 진단하고 유사한 부트 오류가 원인인지 확인하는 데 도움이 됩니다.

## <a name="recommended-steps"></a>권장 단계

수신 하는 오류에 따라 아래의 완화 단계를 수행 합니다.

### <a name="error---unknown-filesystem"></a>오류-알 수 없는 파일 시스템

* **알 수 없는**오류 파일 시스템을 가져오는 경우이 오류는 부팅 파티션의 파일 시스템 손상 또는 잘못 된 커널 구성으로 인해 발생할 수 있습니다.

   * 파일 시스템 문제에 대 한 자세한 내용은 [Linux 복구: 파일 시스템 오류 (fsck, inode)](https://blogs.msdn.microsoft.com/linuxonazure/2016/09/13/linux-recovery-cannot-ssh-to-linux-vm-due-to-file-system-errors-fsck-inodes/)로 인해 Linux VM에 대해 SSH를 실행할 수 없습니다.
   * 커널 문제를 [해결 하려면 Linux 복구: 커널 문제](https://blogs.msdn.microsoft.com/linuxonazure/2016/10/09/linux-recovery-manually-fixing-non-boot-issues-related-to-kernel-problems/)또는 [Linux 복구와 관련 된 부팅 이외의 문제를 수동으로 수정 합니다. Chroot](https://blogs.msdn.microsoft.com/linuxonazure/2016/10/09/linux-recovery-fixing-non-boot-issues-related-to-kernel-problems-using-chroot/)를 사용 하 여 커널 문제와 관련 된 부팅 이외의 문제를 해결 합니다.
   
### <a name="error---file-not-found"></a>오류-파일을 찾을 수 없음

* 오류 15를 발생 하는 **경우: 파일을 찾을 수 없거나 초기 RAM** 디스크 또는 초기 RAM 디스크를 **찾을**수 없습니다. 다음 단계를 수행 하십시오.

    * 누락 된 파일 `/boot/grub2/grub.cfg` `initrd/initramfs` 의 경우 다음 프로세스를 진행 합니다.

    1. 존재 `/etc/default/grub` 하 고 올바른 설정이 있는지 확인 하십시오. 기본 설정에 대해 잘 모르는 경우 작동 하는 VM으로 확인할 수 있습니다.

    2. 그런 다음, 다음 명령을 실행 하 여 구성을 다시 생성 합니다.`grub2-mkconfig -o /boot/grub2/grub.cfg`

   * 파일이 `/boot/grub/menu.lst`없는 경우이 오류는 이전 OS 버전 (**RHEL**, **Centos** , **14.04 및 Ubuntu**)에 대 한 것 이므로 명령이 지연 될 수 있습니다. 이전 서버를 실행 하 고 올바른 명령이 제공 되는지 테스트 해야 합니다.

### <a name="error---no-such-partition"></a>오류-해당 파티션 없음

* **이러한 파티션이 없는**오류를 발생 하는 경우 [OS 드라이브를 확장 한 후 VM을 시작 하는 동안 "해당 파티션 없음" 오류가](https://blogs.technet.microsoft.com/shwetanayak/2017/03/12/case-scenario-no-such-partition-error-while-trying-to-start-the-vm-after-attempting-to-extend-the-os-drive/)발생 하는 경우를 참조 하세요.

### <a name="error---grubcfg-file-not-found"></a>오류-grub. cfg 파일을 찾을 수 없습니다.

* **/Boot/grub2/grub.cfg 파일을 찾을 수 없는**오류가 발생 하는 경우 다음 단계를 수행 합니다.

    * 누락 된 파일 `/boot/grub2/grub.cfg` `initrd/initramfs` 의 경우 다음 프로세스를 진행 합니다.

    1. 존재 `/etc/default/grub` 하 고 올바른 설정이 있는지 확인 하십시오. 기본 설정에 대해 잘 모르는 경우 작동 하는 VM으로 확인할 수 있습니다.

    2. 그런 다음, 다음 명령을 실행 하 여 구성을 `grub2-mkconfig -o /boot/grub2/grub.cfg`다시 생성 합니다.

   * 파일이 `/boot/grub/menu.lst`없는 경우이 오류는 이전 OS 버전 (**RHEL**, **Centos** , **14.04 및 Ubuntu**)에 대 한 것 이므로 명령이 지연 될 수 있습니다. 이전 서버를 실행 하 고 테스트 하 여 올바른 명령이 제공 되는지 확인 합니다.

## <a name="next-steps"></a>다음 단계

* [Azure 가상 머신 에이전트 개요](../extensions/agent-windows.md)
* [Windows용 가상 머신 확장 및 기능](../extensions/features-windows.md)

