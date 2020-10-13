---
title: 파일 시스템 오류로 인 한 Linux VM 시작 문제 해결 | Microsoft Docs
description: 파일 시스템 오류로 인 한 Linux VM 시작 문제를 해결 하는 방법을 알아봅니다.
services: virtual-machines-linux
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: ''
ms.service: virtual-machines-linux
ms.topic: troubleshooting
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.date: 10/09/2019
ms.author: v-six
ms.openlocfilehash: 8f12b88a0ddbc6ae31f40ab31b0126e4fd66b1a5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91325949"
---
# <a name="troubleshoot-linux-vm-starting-issues-due-to-file-system-errors"></a>파일 시스템 오류로 인 한 Linux VM 시작 문제 해결

Secure Shell (SSH)를 사용 하 여 Azure Linux VM (가상 머신)에 연결할 수 없습니다. [Azure Portal](https://portal.azure.com/)에서 부팅 진단 기능을 실행 하면 다음 예제와 유사한 로그 항목이 표시 됩니다.

## <a name="examples"></a>예제

가능한 오류의 예는 다음과 같습니다.

### <a name="example-1"></a>예제 1 

```
Checking all file systems.
[/sbin/fsck.ext4 (1) — /] fsck.ext4 -a /dev/sda1
/dev/sda1 contains a file system with errors, check forced.
/dev/sda1: Inodes that were part of a corrupted orphan linked list found.
/dev/sda1: UNEXPECTED INCONSISTENCY; RUN fsck MANUALLY
```

### <a name="example-2"></a>예 2

```
EXT4-fs (sda1): INFO: recovery required on readonly filesystem
EXT4-fs (sda1): write access will be enabled during recovery
EXT4-fs warning (device sda1): ext4_clear_journal_err:4531: Filesystem error recorded from previous mount: IO failure
EXT4-fs warning (device sda1): ext4_clear_journal_err:4532: Making fs in need of filesystem check.
```

### <a name="example-3"></a>예제 3

```
[  14.252404] EXT4-fs (sda1): Couldn’t remount RDWR because of unprocessed orphan inode list.  Please unmount/remount instead
An error occurred while mounting /.
```

### <a name="example-4"></a>예제 4 

이 예는 정리 된 fsck에 의해 발생 합니다. 이 경우 VM에 추가 데이터 디스크 (/sv/sdc1 및/dev/sde1)도 연결 되어 있습니다.

```
Checking all file systems. 
[/sbin/fsck.ext4 (1) — /] fsck.ext4 -a /dev/sda1
/dev/sda1: clean, 65405/1905008 files, 732749/7608064 blocks
[/sbin/fsck.ext4 (1) — /tmp] fsck.ext4 -a /dev/sdc1
[/sbin/fsck.ext4 (2) — /backup] fsck.ext4 -a /dev/sde1
/dev/sdc1: clean, 12/1048576 files, 109842/4192957 blocks
/dev/sde1 : clean, 51/67043328 files, 4259482/268173037 blocks
```

이 문제는 파일 시스템이 완전히 종료 되지 않거나 저장소 관련 문제가 발생 한 경우에 발생할 수 있습니다. 이러한 문제에는 하드웨어 또는 소프트웨어 오류, 드라이버 또는 프로그램에 대 한 문제, 쓰기 오류 등이 포함 됩니다. 중요 한 데이터의 백업은 항상 중요 합니다. 이 문서에서 설명 하는 도구는 파일 시스템을 복구 하는 데 도움이 될 수 있지만 여전히 데이터 손실이 발생할 수 있습니다.

Linux에는 사용할 수 있는 몇 가지 파일 시스템 검사기가 있습니다. Azure의 배포에 대 한 가장 일반적인은 [FSCK](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/6/html/storage_administration_guide/fsck-fs-specific), [E2FSCK](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/storage_administration_guide/fsck-fs-specific)및 [Xfs_repair](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/storage_administration_guide/xfsrepair)입니다.

## <a name="resolution"></a>해결 방법

이 문제를 해결 하려면 [직렬 콘솔](./serial-console-linux.md) 을 사용 하 여 VM을 응급 모드로 부팅 하 고 해당 도구를 사용 하 여 파일 시스템을 복구 합니다. VM에서 직렬 콘솔을 사용 하도록 설정 하지 않았거나 작동 하지 않는 경우이 문서의 [vm 오프 라인 복구](#repair-the-vm-offline) 섹션을 참조 하세요.

## <a name="use-the-serial-console"></a>직렬 콘솔 사용

1. 직렬 콘솔에 연결합니다.

   > [!Note]
   > Linux 용 직렬 콘솔을 사용 하는 방법에 대 한 자세한 내용은 다음을 참조 하세요.
   > * [직렬 콘솔을 사용 하 여 GRUB 및 단일 사용자 모드에 액세스](serial-console-grub-single-user-mode.md)
   > * [SysRq 및 NMI 호출에 직렬 콘솔 사용](./serial-console-nmi-sysrq.md)

2. 전원 아이콘 단추를 선택한 다음, VM 다시 시작을 선택 합니다. (직렬 콘솔이 사용 하도록 설정 되지 않았거나 연결 되지 않은 경우 단추가 표시 되지 않습니다.)

   ![이미지](./media/linux-recovery-cannot-ssh-to-linux-vm-due-to-file-system-errors-fsck/restart-vm.png)

3. 에서 VM을 응급 모드로 부팅 합니다.

4. 응급 모드에 로그인 하려면 루트 계정의 암호를 입력 하세요.

5. 파일 시스템에서 오류를 검색 하려면-n 옵션과 함께 xfs_repair를 사용 합니다. 다음 샘플에서는 시스템 파티션이/dev/sda1. 것으로 가정 합니다. VM에 대 한 적절 한 값으로 바꿉니다.

   ```
   xfs_repair -n /dev/sda1
   ```

6. 다음 명령을 실행 하 여 파일 시스템을 복구 합니다.

   ```
   xfs_repair /dev/sda1
   ```

7. "오류: 파일 시스템에 재생 해야 하는 로그에 중요 한 메타 데이터 변경 내용이 있습니다." 라는 오류 메시지가 표시 되 면 임시 디렉터리를 만들고 파일 시스템을 탑재 합니다.

   ```
   mkdir /temp
   mount /dev/sda1 /temp
   ```

8. 디스크를 탑재 하지 못하는 경우-L 옵션 (force log 제로화)을 사용 하 여 xfs_repair 명령을 실행 합니다.

   ```
   xfs_repair /dev/sda1 -L
   ```

9. 다음으로 파일 시스템을 탑재 해 봅니다. 디스크가 성공적으로 탑재 되 면 다음과 같은 출력이 표시 됩니다.
 
   ```
   XFS (sda1): Mounting V1 Filesystem
   XFS (sda1): Ending clean mount
   ```

10. VM을 다시 시작 하 고 문제가 해결 되었는지 확인 합니다.

    ```
    Reboot -f
    ```

## <a name="repair-the-vm-offline"></a>오프라인으로 VM 복구

1. VM의 시스템 디스크를 복구 VM (모든 작동 중인 Linux VM)에 데이터 디스크로 연결 합니다. 이렇게 하려면 [CLI 명령을](./troubleshoot-recovery-disks-linux.md) 사용 하거나 [vm 복구 명령을](repair-linux-vm-using-azure-virtual-machine-repair-commands.md)사용 하 여 복구 vm 설정을 자동화할 수 있습니다.

2. 연결한 시스템 디스크의 드라이브 레이블을 찾습니다. 이 경우 연결 된 시스템 디스크의 레이블이/sv/sdc1 이라고 가정 합니다. VM에 대 한 적절 한 값으로 대체 합니다.

3. 파일 시스템에서 오류를 검색 하려면-n 옵션과 함께 xfs_repair를 사용 합니다.

   ```
   xfs_repair -n /dev/sdc1
   ```

4. 다음 명령을 실행 하 여 파일 시스템을 복구 합니다.

   ```
   xfs_repair /dev/sdc1
   ```

5. "오류: 파일 시스템에 재생 해야 하는 로그에 중요 한 메타 데이터 변경 내용이 있습니다." 라는 오류 메시지가 표시 되 면 임시 디렉터리를 만들고 파일 시스템을 탑재 합니다.

   ```
   mkdir /temp

   mount /dev/sdc1 /temp
   ```

   디스크를 탑재 하지 못하는 경우-L 옵션 (force log 제로화)을 사용 하 여 xfs_repair 명령을 실행 합니다.

   ```
   xfs_repair /dev/sdc1 -L
   ```

6. 다음으로 파일 시스템을 탑재 해 봅니다. 디스크가 성공적으로 탑재 되 면 다음과 같은 출력이 표시 됩니다.

   ```
   XFS (sdc1): Mounting V1 Filesystem
   
   XFS (sdc1): Ending clean mount
   ```

7. 원본 가상 하드 디스크를 분리 한 다음 원래 시스템 디스크에서 VM을 만듭니다. 이렇게 하려면 [CLI 명령](troubleshoot-recovery-disks-linux.md) 또는 [vm 복구 명령](repair-linux-vm-using-azure-virtual-machine-repair-commands.md) (사용 하 여 복구 vm을 만든 경우)을 사용할 수 있습니다.

8. 문제가 해결 되었는지 확인 합니다.

## <a name="next-steps"></a>다음 단계

* [Azure CLI 2.0을 사용하여 OS 디스크를 복구 VM에 연결함으로써 Linux VM 문제 해결](./troubleshoot-recovery-disks-linux.md)
* [포털을 사용하여 데이터 디스크를 Linux VM에 연결](../linux/attach-disk-portal.md)
