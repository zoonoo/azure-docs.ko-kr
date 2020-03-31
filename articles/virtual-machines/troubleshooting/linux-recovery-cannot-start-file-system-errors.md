---
title: 파일 시스템 오류로 인해 리눅스 VM 시작 문제 해결 | 마이크로 소프트 문서
description: Linux VM을 시작할 수 없는 이유와 문제를 해결하는 방법을 설명합니다.
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
ms.openlocfilehash: 455cb1e0067217be6edcf665e8c07e8fcd684ab5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76842404"
---
# <a name="troubleshoot-linux-vm-starting-issues-due-to-file-system-errors"></a>파일 시스템 오류로 인해 Linux VM 시작 문제 해결

보안 셸(SSH)을 사용하여 Azure Linux 가상 시스템(VM)에 연결할 수 없습니다. [Azure 포털에서](https://portal.azure.com/)부팅 진단 기능을 실행하면 다음 예제와 유사한 로그 항목이 표시됩니다.

## <a name="examples"></a>예

다음은 가능한 오류의 예입니다.

### <a name="example-1"></a>예 1 

```
Checking all file systems.
[/sbin/fsck.ext4 (1) — /] fsck.ext4 -a /dev/sda1
/dev/sda1 contains a file system with errors, check forced.
/dev/sda1: Inodes that were part of a corrupted orphan linked list found.
/dev/sda1: UNEXPECTED INCONSISTENCY; RUN fsck MANUALLY
```

### <a name="example-2"></a>예제 2

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

이 예제는 깨끗한 fsck로 인해 발생합니다. 이 경우 VM(/dev/sdc1 및 /dev/sde1)에 연결된 추가 데이터 디스크도 있습니다.

```
Checking all file systems. 
[/sbin/fsck.ext4 (1) — /] fsck.ext4 -a /dev/sda1
/dev/sda1: clean, 65405/1905008 files, 732749/7608064 blocks
[/sbin/fsck.ext4 (1) — /tmp] fsck.ext4 -a /dev/sdc1
[/sbin/fsck.ext4 (2) — /backup] fsck.ext4 -a /dev/sde1
/dev/sdc1: clean, 12/1048576 files, 109842/4192957 blocks
/dev/sde1 : clean, 51/67043328 files, 4259482/268173037 blocks
```

파일 시스템이 정리되어 있지 않거나 저장소 관련 문제가 있는 경우 이 문제가 발생할 수 있습니다. 문제는 하드웨어 또는 소프트웨어 오류, 드라이버 또는 프로그램 문제, 쓰기 오류 등을 포함합니다. 중요한 데이터를 백업하는 것은 항상 중요합니다. 이 문서에서 설명하는 도구는 파일 시스템을 복구하는 데 도움이 될 수 있지만 데이터 손실은 여전히 발생할 수 있습니다.

리눅스는 여러 파일 시스템 검사기를 사용할 수 있습니다. Azure에서 배포에 대 한 가장 일반적인: [FSCK,](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/6/html/storage_administration_guide/fsck-fs-specific) [E2FSCK](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/storage_administration_guide/fsck-fs-specific)및 [Xfs_repair.](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/storage_administration_guide/xfsrepair)

## <a name="resolution"></a>해결 방법

이 문제를 해결하려면 [직렬 콘솔을](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-linux) 사용하여 VM을 비상 모드로 부팅하고 해당 도구를 사용하여 파일 시스템을 복구합니다. VM에서 직렬 콘솔을 사용할 수 없거나 작동하지 않는 경우 이 문서의 [VM 오프라인 복구](#repair-the-vm-offline) 섹션을 참조하십시오.

## <a name="use-the-serial-console"></a>직렬 콘솔 사용

1. 직렬 콘솔에 연결합니다.

   > [!Note]
   > Linux용 직렬 콘솔 사용에 대한 자세한 내용은 다음을 참조하십시오.
   > * [시리얼 콘솔을 사용하여 GRUB 및 단일 사용자 모드에 액세스](https://docs.microsoft.com/azure/virtual-machines/linux/serial-console-grub-single-user-mode)
   > * [SysRq 및 NMI 호출에 직렬 콘솔 사용](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-nmi-sysrq)

2. 전원 아이콘 단추를 선택한 다음 VM 다시 시작을 선택합니다. 직렬 콘솔을 사용하지 않거나 성공적으로 연결되지 않으면 단추가 표시되지 않습니다.

   ![IMAGE](./media/linux-recovery-cannot-ssh-to-linux-vm-due-to-file-system-errors-fsck/restart-vm.png)

3. VM을 비상 모드로 부팅합니다.

4. 긴급 모드에 로그인하려면 루트 계정의 암호를 입력합니다.

5. 파일 시스템의 오류를 감지하려면 -n 옵션과 함께 xfs_repair 사용합니다. 다음 샘플에서는 시스템 파티션이 /dev/sda1이라고 가정합니다. VM에 적합한 값으로 바꿉습니다.

   ```
   xfs_repair -n /dev/sda1
   ```

6. 다음 명령을 실행하여 파일 시스템을 복구합니다.

   ```
   xfs_repair /dev/sda1
   ```

7. "오류: 파일 시스템에 재생해야 하는 로그에 중요한 메타데이터 변경 내용"이라는 오류 메시지가 발생하면 임시 디렉터리를 만들고 파일 시스템을 탑재합니다.

   ```
   mkdir /temp
   mount /dev/sda1 /temp
   ```

8. 디스크가 마운트되지 않으면 -L 옵션(강제 로그 비점)으로 xfs_repair 명령을 실행합니다.

   ```
   xfs_repair /dev/sda1 -L
   ```

9. 다음으로 파일 시스템을 탑재해 봅을 사용해 봅을 사용해 봅을. 디스크가 성공적으로 탑재되면 다음과 같은 출력을 받게 됩니다.
 
   ```
   XFS (sda1): Mounting V1 Filesystem
   XFS (sda1): Ending clean mount
   ```

10. VM을 다시 시작한 다음 문제가 해결되었는지 확인합니다.

    ```
    Reboot -f
    ```

## <a name="repair-the-vm-offline"></a>오프라인으로 VM 복구

1. VM의 시스템 디스크를 복구 VM(모든 작업 Linux VM)에 데이터 디스크로 연결합니다. 이렇게 하려면 [CLI 명령을](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/troubleshoot-recovery-disks-linux) 사용하거나 [VM 복구 명령을](repair-linux-vm-using-azure-virtual-machine-repair-commands.md)사용하여 복구 VM 설정을 자동화할 수 있습니다.

2. 연결한 시스템 디스크의 드라이브 레이블을 찾습니다. 이 경우 연결한 시스템 디스크의 레이블이 /dev/sdc1이라고 가정합니다. VM에 적합한 값으로 바꿉습니다.

3. 파일 시스템의 오류를 감지하려면 -n 옵션과 함께 xfs_repair 사용합니다.

   ```
   xfs_repair -n /dev/sdc1
   ```

4. 다음 명령을 실행하여 파일 시스템을 복구합니다.

   ```
   xfs_repair /dev/sdc1
   ```

5. "오류: 파일 시스템에 재생해야 하는 로그에 중요한 메타데이터 변경 내용"이라는 오류 메시지가 발생하면 임시 디렉터리를 만들고 파일 시스템을 탑재합니다.

   ```
   mkdir /temp

   mount /dev/sdc1 /temp
   ```

   디스크가 마운트되지 않으면 -L 옵션(강제 로그 비점)으로 xfs_repair 명령을 실행합니다.

   ```
   xfs_repair /dev/sdc1 -L
   ```

6. 다음으로 파일 시스템을 탑재해 봅을 사용해 봅을 사용해 봅을. 디스크가 성공적으로 탑재되면 다음과 같은 출력을 받게 됩니다.

   ```
   XFS (sdc1): Mounting V1 Filesystem
   
   XFS (sdc1): Ending clean mount
   ```

7. 원래 가상 하드 디스크의 마운트를 해제하고 분리한 다음 원래 시스템 디스크에서 VM을 만듭니다. 이렇게 하려면 [CLI 명령](troubleshoot-recovery-disks-linux.md) 또는 [VM 복구 명령을](repair-linux-vm-using-azure-virtual-machine-repair-commands.md) 사용 하는 경우 복구 VM을 만들 려면 사용할 수 있습니다.

8. 문제가 해결되었는지 확인합니다.

## <a name="next-steps"></a>다음 단계

* [Azure CLI 2.0을 사용하여 OS 디스크를 복구 VM에 연결함으로써 Linux VM 문제 해결](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-linux-troubleshoot-recovery-disks)
* [포털을 사용하여 데이터 디스크를 Linux VM에 연결](https://docs.microsoft.com/azure/virtual-machines/linux/attach-disk-portal)

