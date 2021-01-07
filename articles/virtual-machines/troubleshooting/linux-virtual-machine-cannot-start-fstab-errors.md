---
title: Fstab 오류로 인 한 Linux VM 시작 문제 해결 | Microsoft Docs
description: Linux VM을 시작할 수 없는 이유와 문제를 해결 하는 방법을 설명 합니다.
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
ms.openlocfilehash: fd49993e6825c47bbae8f034715c03191e06ab2d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "89441666"
---
# <a name="troubleshoot-linux-vm-starting-issues-due-to-fstab-errors"></a>Fstab 오류로 인 한 Linux VM 시작 문제 해결

SSH (Secure Shell) 연결을 사용 하 여 Azure Linux VM (가상 머신)에 연결할 수 없습니다. [Azure Portal](https://portal.azure.com/)에서 [부팅 진단](./boot-diagnostics.md) 기능을 실행 하면 다음 예제와 유사한 로그 항목이 표시 됩니다.

## <a name="examples"></a>예

가능한 오류의 예는 다음과 같습니다.

### <a name="example-1-a-disk-is-mounted-by-the-scsi-id-instead-of-the-universally-unique-identifier-uuid"></a>예제 1: 디스크는 UUID (범용 고유 식별자) 대신 SCSI ID로 탑재 됩니다.

```
[K[[1;31m TIME [0m] Timed out waiting for device dev-incorrect.device.
[[1;33mDEPEND[0m] Dependency failed for /data.
[[1;33mDEPEND[0m] Dependency failed for Local File Systems.
…
Welcome to emergency mode! After logging in, type "journalctl -xb" to viewsystem logs, "systemctl reboot" to reboot, "systemctl default" to try again to boot into default mode.
Give root password for maintenance
(or type Control-D to continue)
```

### <a name="example-2-an-unattached-device-is-missing-on-centos"></a>예 2: CentOS에 연결 되지 않은 장치 누락

```
Checking file systems…
fsck from util-linux 2.19.1
Checking all file systems.
/dev/sdc1: nonexistent device ("nofail" fstab option may be used to skip this device)
/dev/sdd1: nonexistent device ("nofail" fstab option may be used to skip this device)
/dev/sde1: nonexistent device ("nofail" fstab option may be used to skip this device)

[/sbin/fsck.ext3 (1) — /CODE] sck.ext3 -a /dev/sdc1
fsck.ext3: No such file or directory while trying to open /dev/sdc1

/dev/sdc1:
The superblock could not be read or does not describe a correct ext2
filesystem. If the device is valid and it really contains an ext2
filesystem (and not swap or ufs or something else), then the superblock
is corrupt, and you might try running e2fsck with an alternate superblock:

e2fsck -b 8193 <device>

[/sbin/fsck.xfs (1) — /GLUSTERDISK] fsck.xfs -a /dev/sdd1
/sbin/fsck.xfs: /dev/sdd1 does not exist
[/sbin/fsck.ext3 (1) — /DATATEMP] fsck.ext3 -a /dev/sde1 fsck.ext3: No such file or directory while trying to open /dev/sde1
```

### <a name="example-3-a-vm-cannot-start-because-of-an-fstab-misconfiguration-or-because-the-disk-is-no-longer-attached"></a>예 3: fstab 잘못 된 구성으로 인해 또는 디스크가 더 이상 연결 되어 있지 않기 때문에 VM을 시작할 수 없습니다.

```
The disk drive for /var/lib/mysql is not ready yet or not present.
Continue to wait, or Press S to skip mounting or M for manual recovery
```

### <a name="example-4-a-serial-log-entry-shows-an-incorrect-uuid"></a>예 4: 직렬 로그 항목에 잘못 된 UUID가 표시 됨

```
Checking filesystems
Checking all file systems.
[/sbin/fsck.ext4 (1) — /] fsck.ext4 -a /dev/sda1
/dev/sda1: clean, 70442/1905008 files, 800094/7608064 blocks
[/sbin/fsck.ext4 (1) — /datadrive] fsck.ext4 -a UUID="<UUID>"
fsck.ext4: Unable to resolve UUID="<UUID>"
[FAILED

*** An error occurred during the file system check.
*** Dropping you to a shell; the system will reboot
*** when you leave the shell.
*** Warning — SELinux is active
*** Disabling security enforcement for system recovery.
*** Run 'setenforce 1' to reenable.
type=1404 audit(1428047455.949:4): enforcing=0 old_enforcing=1 auid=<AUID> ses=4294967295
Give root password for maintenance
(or type Control-D to continue)
```

이 문제는 fstab (파일 시스템 테이블) 구문이 잘못 되었거나 "/etc/fstab" 파일의 항목에 매핑되는 필수 데이터 디스크가 VM에 연결 되지 않은 경우에 발생할 수 있습니다.

## <a name="resolution"></a>해결 방법

이 문제를 해결 하려면 Azure Virtual Machines에 대 한 직렬 콘솔을 사용 하 여 응급 모드로 VM을 시작 합니다. 그런 다음 도구를 사용 하 여 파일 시스템을 복구 합니다. VM에서 직렬 콘솔을 사용 하도록 설정 하지 않은 경우 VM을 [오프 라인으로 복구](#repair-the-vm-offline) 섹션으로 이동 합니다.

## <a name="use-the-serial-console"></a>직렬 콘솔 사용

### <a name="using-single-user-mode"></a>단일 사용자 모드 사용

1. [직렬 콘솔](./serial-console-linux.md)에 연결 합니다.
2. 직렬 콘솔을 사용 하 여 단일 사용자 모드 [단일 사용자 모드](serial-console-grub-single-user-mode.md) 가져오기
3. Vm이 단일 사용자 모드로 부팅 되 면 원하는 텍스트 편집기를 사용 하 여 fstab 파일을 엽니다. 

   ```
   # nano /etc/fstab
   ```

4. 나열 된 파일 시스템을 검토 합니다. Fstab 파일의 각 줄은 VM이 시작 될 때 탑재 되는 파일 시스템을 나타냅니다. Fstab 파일의 구문에 대 한 자세한 내용을 보려면 man fstab 명령을 실행 합니다. 시작 실패 문제를 해결 하려면 각 줄을 검토 하 여 구조와 내용 모두에서 올바른지 확인 합니다.

   > [!Note]
   > * 각 줄의 필드는 탭 이나 공백으로 구분 됩니다. 빈 줄은 무시됩니다. 숫자 기호 (#)가 첫 번째 문자인 줄은 주석입니다. 주석 처리 된 줄은 fstab 파일에 그대로 남아 있지만 처리 되지 않습니다. 줄을 제거 하는 대신 fstab 줄을 주석으로 처리 하는 것이 좋습니다.
   > * VM을 복구 및 시작 하려면 파일 시스템 파티션만 필요한 유일한 파티션입니다. 추가 주석 처리 된 파티션에 대해 VM에서 응용 프로그램 오류가 발생할 수 있습니다. 그러나 VM은 추가 파티션 없이 시작 해야 합니다. 주석 처리 된 줄의 주석 처리를 나중에 수행할 수 있습니다.
   > * 파일 시스템 파티션의 UUID를 사용 하 여 Azure Vm에 데이터 디스크를 탑재 하는 것이 좋습니다. 예를 들어 다음 명령을 실행합니다. ``/dev/sdc1: LABEL="cloudimg-rootfs" UUID="<UUID>" TYPE="ext4" PARTUUID="<PartUUID>"``
   > * 파일 시스템의 UUID를 확인 하려면 blkid 명령을 실행 합니다. 구문에 대 한 자세한 내용을 보려면 man blkid 명령을 실행 합니다.
   > * Nofail 옵션은 파일 시스템이 손상 되었거나 파일 시스템이 시작 시 존재 하지 않는 경우에도 VM이 시작 되도록 하는 데 도움이 됩니다. VM을 시작 하는 데 필요 하지 않은 파티션에서 오류가 발생 한 후에는 fstab 파일에서 nofail 옵션을 사용 하 여 시작을 계속 하도록 설정 하는 것이 좋습니다.

5. Fstab 파일에서 부정확 하거나 불필요 한 줄을 변경 하거나 주석 처리 하 여 VM이 제대로 시작 될 수 있도록 합니다.

6. Fstab 파일에 대 한 변경 내용을 저장 합니다.

7. 아래의 명령을 사용 하 여 vm을 다시 부팅 합니다.
   
   ```
   # reboot -f
   ```
> [!Note]
   > 또한 vm을 다시 부팅 하는 "ctrl + x" 명령을 사용할 수 있습니다.


8. 항목 설명 또는 수정을 완료 한 경우 시스템은 포털에서 bash 프롬프트에 도달 해야 합니다. VM에 연결할 수 있는지 여부를 확인 합니다.

### <a name="using-root-password"></a>루트 암호 사용

1. [직렬 콘솔](./serial-console-linux.md)에 연결 합니다.
2. 로컬 사용자 및 암호를 사용 하 여 시스템에 로그인 합니다.

   > [!Note]
   > SSH 키를 사용 하 여 직렬 콘솔에서 시스템에 로그인 할 수 없습니다.

3. 디스크가 탑재 되지 않았음을 나타내는 오류를 찾습니다. 다음 예에서는 시스템에서 더 이상 존재 하지 않는 디스크를 연결 하려고 했습니다.

   ```
   [DEPEND] Dependency failed for /datadisk1.
   [DEPEND] Dependency failed for Local File Systems.
   [DEPEND] Dependency failed for Relabel all filesystems, if necessary.
   [DEPEND] Dependency failed for Migrate local... structure to the new structure.
   Welcome to emergency mode! After logging in, type "journalctl -xb" to view
   system logs, "systemctl reboot" to reboot, "systemctl default" or ^D to try again to boot into default mode.
   Give root password for maintenance
   (or type Control-D to continue):
   ```

4. 루트 암호 (Red Hat 기반 Vm)를 사용 하 여 VM에 연결 합니다.

5. 원하는 텍스트 편집기를 사용 하 여 fstab 파일을 엽니다. 디스크가 탑재 되 면 Nano에 대해 다음 명령을 실행 합니다.

   ```
   $ nano /mnt/troubleshootingdisk/etc/fstab
   ```

6. 나열 된 파일 시스템을 검토 합니다. Fstab 파일의 각 줄은 VM이 시작 될 때 탑재 되는 파일 시스템을 나타냅니다. Fstab 파일의 구문에 대 한 자세한 내용을 보려면 man fstab 명령을 실행 합니다. 시작 실패 문제를 해결 하려면 각 줄을 검토 하 여 구조와 내용 모두에서 올바른지 확인 합니다.

   > [!Note]
   > * 각 줄의 필드는 탭 이나 공백으로 구분 됩니다. 빈 줄은 무시됩니다. 숫자 기호 (#)가 첫 번째 문자인 줄은 주석입니다. 주석 처리 된 줄은 fstab 파일에 그대로 남아 있지만 처리 되지 않습니다. 줄을 제거 하는 대신 fstab 줄을 주석으로 처리 하는 것이 좋습니다.
   > * VM을 복구 및 시작 하려면 파일 시스템 파티션만 필요한 유일한 파티션입니다. 추가 주석 처리 된 파티션에 대해 VM에서 응용 프로그램 오류가 발생할 수 있습니다. 그러나 VM은 추가 파티션 없이 시작 해야 합니다. 주석 처리 된 줄의 주석 처리를 나중에 수행할 수 있습니다.
   > * 파일 시스템 파티션의 UUID를 사용 하 여 Azure Vm에 데이터 디스크를 탑재 하는 것이 좋습니다. 예를 들어 다음 명령을 실행합니다. ``/dev/sdc1: LABEL="cloudimg-rootfs" UUID="<UUID>" TYPE="ext4" PARTUUID="<PartUUID>"``
   > * 파일 시스템의 UUID를 확인 하려면 blkid 명령을 실행 합니다. 구문에 대 한 자세한 내용을 보려면 man blkid 명령을 실행 합니다.
   > * Nofail 옵션은 파일 시스템이 손상 되었거나 파일 시스템이 시작 시 존재 하지 않는 경우에도 VM이 시작 되도록 하는 데 도움이 됩니다. VM을 시작 하는 데 필요 하지 않은 파티션에서 오류가 발생 한 후에는 fstab 파일에서 nofail 옵션을 사용 하 여 시작을 계속 하도록 설정 하는 것이 좋습니다.

7. Fstab 파일에서 부정확 하거나 불필요 한 줄을 변경 하거나 주석 처리 하 여 VM이 제대로 시작 될 수 있도록 합니다.

8. Fstab 파일에 대 한 변경 내용을 저장 합니다.

9. 가상 컴퓨터를 다시 시작합니다.

10. 항목 설명 또는 수정을 완료 한 경우 시스템은 포털에서 bash 프롬프트에 도달 해야 합니다. VM에 연결할 수 있는지 여부를 확인 합니다.

11. Mount – a 명령을 실행 하 여 fstab 변경을 테스트할 때 탑재 위치를 확인 합니다. 오류가 없으면 탑재 지점이 양호 해야 합니다.

## <a name="repair-the-vm-offline"></a>오프라인으로 VM 복구

1. VM의 시스템 디스크를 복구 VM (모든 작동 중인 Linux VM)에 데이터 디스크로 연결 합니다. 이렇게 하려면 [CLI 명령을](./troubleshoot-recovery-disks-linux.md) 사용 하거나 [vm 복구 명령을](repair-linux-vm-using-azure-virtual-machine-repair-commands.md)사용 하 여 복구 vm 설정을 자동화할 수 있습니다.

2. 시스템 디스크를 복구 VM의 데이터 디스크로 탑재 한 후에는 fstab 파일을 백업한 후 다음 단계에 따라 fstab 파일을 수정 합니다.

3. 디스크가 탑재 되지 않았음을 나타내는 오류를 확인 합니다. 다음 예에서는 시스템에서 더 이상 존재 하지 않는 디스크를 연결 하려고 했습니다.

   ```output
   [DEPEND] Dependency failed for /datadisk1.
   [DEPEND] Dependency failed for Local File Systems.
   [DEPEND] Dependency failed for Relabel all filesystems, if necessary.
   [DEPEND] Dependency failed for Migrate local... structure to the new structure.
   Welcome to emergency mode! After logging in, type "journalctl -xb" to view system logs, "systemctl reboot" to reboot, "systemctl default" or ^D to try again to boot into default mode.
   Give root password for maintenance (or type Control-D to continue):
   ```

4. 루트 암호 (Red Hat 기반 Vm)를 사용 하 여 VM에 연결 합니다.

5. 원하는 텍스트 편집기를 사용 하 여 fstab 파일을 엽니다. 디스크가 탑재 되 면 Nano에 대해 다음 명령을 실행 합니다. 탑재 된 디스크에 있는 fstab 파일에서 작업 하 고 있는지 확인 합니다 .이 파일은 복구 VM에 있는 fstab 파일이 아닙니다.

   ```
   $ nano /mnt/troubleshootingdisk/etc/fstab
   ```

6. 나열 된 파일 시스템을 검토 합니다. Fstab 파일의 각 줄은 VM이 시작 될 때 탑재 되는 파일 시스템을 나타냅니다. Fstab 파일의 구문에 대 한 자세한 내용을 보려면 man fstab 명령을 실행 합니다. 시작 실패 문제를 해결 하려면 각 줄을 검토 하 여 구조와 내용 모두에서 올바른지 확인 합니다.

   > [!Note]
   > * 각 줄의 필드는 탭 이나 공백으로 구분 됩니다. 빈 줄은 무시됩니다. 숫자 기호 (#)가 첫 번째 문자인 줄은 주석입니다. 주석 처리 된 줄은 fstab 파일에 그대로 남아 있지만 처리 되지 않습니다. 줄을 제거 하는 대신 fstab 줄을 주석으로 처리 하는 것이 좋습니다.
   > * VM을 복구 및 시작 하려면 파일 시스템 파티션만 필요한 유일한 파티션입니다. 추가 주석 처리 된 파티션에 대해 VM에서 응용 프로그램 오류가 발생할 수 있습니다. 그러나 VM은 추가 파티션 없이 시작 해야 합니다. 주석 처리 된 줄의 주석 처리를 나중에 수행할 수 있습니다.
   > * 파일 시스템 파티션의 UUID를 사용 하 여 Azure Vm에 데이터 디스크를 탑재 하는 것이 좋습니다. 예를 들어 다음 명령을 실행합니다. ``/dev/sdc1: LABEL="cloudimg-rootfs" UUID="<UUID>" TYPE="ext4" PARTUUID="<PartUUID>"``
   > * 파일 시스템의 UUID를 확인 하려면 blkid 명령을 실행 합니다. 구문에 대 한 자세한 내용을 보려면 man blkid 명령을 실행 합니다. 복구 하려는 디스크가 이제 새 VM에 탑재 되어 있는지 확인 합니다. Uuid가 일치 해야 하지만 장치 파티션 Id (예: "/dev/sda1")는이 VM에서 다릅니다. 비시스템 VHD에 있는 원래 오류가 발생 한 VM의 파일 시스템 파티션은 [CLI 명령을 사용 하 여](./troubleshoot-recovery-disks-linux.md)복구 vm에서 사용할 수 없습니다.
   > * Nofail 옵션은 파일 시스템이 손상 되었거나 파일 시스템이 시작 시 존재 하지 않는 경우에도 VM이 시작 되도록 하는 데 도움이 됩니다. VM을 시작 하는 데 필요 하지 않은 파티션에서 오류가 발생 한 후에는 fstab 파일에서 nofail 옵션을 사용 하 여 시작을 계속 하도록 설정 하는 것이 좋습니다.

7. Fstab 파일에서 부정확 하거나 불필요 한 줄을 변경 하거나 주석 처리 하 여 VM이 제대로 시작 될 수 있도록 합니다.

8. Fstab 파일에 대 한 변경 내용을 저장 합니다.

9. 가상 컴퓨터를 다시 시작 하거나 원래 VM을 다시 빌드하십시오.

10. 항목 설명 또는 수정을 완료 한 경우 시스템은 포털에서 bash 프롬프트에 도달 해야 합니다. VM에 연결할 수 있는지 여부를 확인 합니다.

11. Mount – a 명령을 실행 하 여 fstab 변경을 테스트할 때 탑재 위치를 확인 합니다. 오류가 없으면 탑재 지점이 양호 해야 합니다.

12. 원본 가상 하드 디스크를 분리 한 다음 원래 시스템 디스크에서 VM을 만듭니다. 이렇게 하려면 [CLI 명령](troubleshoot-recovery-disks-linux.md) 또는 [vm 복구 명령](repair-linux-vm-using-azure-virtual-machine-repair-commands.md) (사용 하 여 복구 vm을 만든 경우)을 사용할 수 있습니다.

13. VM을 다시 만들고 SSH를 통해 연결할 수 있는 경우 다음 작업을 수행 합니다.
    * 복구 중에 변경 되었거나 주석 처리 된 fstab 줄을 검토 합니다.
    * UUID와 nofail 옵션을 적절 하 게 사용 하 고 있는지 확인 합니다.
    * VM을 다시 시작 하기 전에 fstab 변경 내용을 테스트 합니다. 이렇게 하려면 다음 명령을 사용 합니다. ``$ sudo mount -a``
    * 이후 복구 시나리오에서 사용할 수정 된 fstab 파일의 추가 복사본을 만듭니다.

## <a name="next-steps"></a>다음 단계

* [Azure CLI 2.0을 사용하여 OS 디스크를 복구 VM에 연결함으로써 Linux VM 문제 해결](./troubleshoot-recovery-disks-linux.md)
* [Azure Portal을 사용하여 OS 디스크를 복구 VM에 연결함으로써 Linux VM 문제 해결](./troubleshoot-recovery-disks-portal-linux.md)
