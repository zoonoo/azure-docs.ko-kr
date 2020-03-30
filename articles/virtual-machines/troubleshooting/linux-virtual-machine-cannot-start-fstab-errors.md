---
title: Fstab 오류로 인해 리눅스 VM 시작 문제 해결 | 마이크로 소프트 문서
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
ms.openlocfilehash: 7e16eabc4f9572591eabd37b93258fcd783cce7e
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "80351146"
---
# <a name="troubleshoot-linux-vm-starting-issues-due-to-fstab-errors"></a>Fstab 오류로 인해 리눅스 VM 시작 문제 해결

보안 셸(SSH) 연결을 사용하여 Azure Linux 가상 시스템(VM)에 연결할 수 없습니다. [Azure 포털에서](https://portal.azure.com/) [진단 부팅](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/boot-diagnostics) 기능을 실행하면 다음 예제와 유사한 로그 항목이 표시됩니다.

## <a name="examples"></a>예

다음은 가능한 오류의 예입니다.

### <a name="example-1-a-disk-is-mounted-by-the-scsi-id-instead-of-the-universally-unique-identifier-uuid"></a>예 1: 디스크는 보편적으로 고유한 식별자(UUID) 대신 SCSI ID에 의해 탑재됩니다.

```
[K[[1;31m TIME [0m] Timed out waiting for device dev-incorrect.device.
[[1;33mDEPEND[0m] Dependency failed for /data.
[[1;33mDEPEND[0m] Dependency failed for Local File Systems.
…
Welcome to emergency mode! After logging in, type "journalctl -xb" to viewsystem logs, "systemctl reboot" to reboot, "systemctl default" to try again to boot into default mode.
Give root password for maintenance
(or type Control-D to continue)
```

### <a name="example-2-an-unattached-device-is-missing-on-centos"></a>예 2: CentOS에서 연결되지 않은 장치가 없습니다.

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

### <a name="example-3-a-vm-cannot-start-because-of-an-fstab-misconfiguration-or-because-the-disk-is-no-longer-attached"></a>예 3: fstab 잘못 구성으로 인해 또는 디스크가 더 이상 연결되지 않아 VM을 시작할 수 없습니다.

```
The disk drive for /var/lib/mysql is not ready yet or not present.
Continue to wait, or Press S to skip mounting or M for manual recovery
```

### <a name="example-4-a-serial-log-entry-shows-an-incorrect-uuid"></a>예 4: 직렬 로그 항목에 잘못된 UUID가 표시됩니다.

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

파일 시스템 테이블(fstab) 구문이 올바르지 않거나 "/etc/fstab" 파일의 항목에 매핑된 필수 데이터 디스크가 VM에 연결되어 있지 않은 경우 이 문제가 발생할 수 있습니다.

## <a name="resolution"></a>해결 방법

이 문제를 해결하려면 Azure 가상 컴퓨터의 직렬 콘솔을 사용하여 비상 모드에서 VM을 시작합니다. 그런 다음 도구를 사용하여 파일 시스템을 복구합니다. VM에서 직렬 콘솔을 사용할 수 없는 경우 [VM 오프라인 복구](#repair-the-vm-offline) 섹션으로 이동합니다.

## <a name="use-the-serial-console"></a>직렬 콘솔 사용

### <a name="using-single-user-mode"></a>단일 사용자 모드 사용

1. [직렬 콘솔에](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-linux)연결합니다.
2. 직렬 콘솔을 사용하여 단일 사용자 모드 [단일 사용자 모드](https://docs.microsoft.com/azure/virtual-machines/linux/serial-console-grub-single-user-mode) 사용
3. VM이 단일 사용자 모드로 부팅되면 즐겨 찾는 텍스트 편집기를 사용하여 fstab 파일을 엽니다. 

   ```
   # nano /etc/fstab
   ```

4. 나열된 파일 시스템을 검토합니다. fstab 파일의 각 줄은 VM이 시작될 때 탑재되는 파일 시스템을 나타냅니다. fstab 파일의 구문에 대 한 자세한 내용은 man fstab 명령을 실행 합니다. 시작 실패 문제를 해결하려면 각 줄을 검토하여 구조와 콘텐츠 모두에서 올바른지 확인합니다.

   > [!Note]
   > * 각 줄의 필드는 탭이나 공백으로 구분됩니다. 빈 줄은 무시됩니다. 첫 번째 문자로 숫자 기호(#)가 있는 줄은 주석입니다. 주석 처리된 줄은 fstab 파일에 남아 있을 수 있지만 처리되지는 않습니다. 선을 제거하는 대신 확실하지 않은 fstab 줄에 주석을 달수 하는 것이 좋습니다.
   > * VM이 복구되고 시작되려면 파일 시스템 파티션이 필요한 파티션일 뿐이어야 합니다. VM에서 추가 주석 파티션에 대한 응용 프로그램 오류가 발생할 수 있습니다. 그러나 VM은 추가 파티션 없이 시작해야 합니다. 나중에 주석이 있는 줄에 대한 주석 을 해제할 수 있습니다.
   > * 파일 시스템 파티션의 UUID를 사용하여 Azure VM에 데이터 디스크를 탑재하는 것이 좋습니다. 예를 들어 다음 명령을 실행합니다.``/dev/sdc1: LABEL="cloudimg-rootfs" UUID="<UUID>" TYPE="ext4" PARTUUID="<PartUUID>"``
   > * 파일 시스템의 UUID를 확인하려면 blkid 명령을 실행합니다. 구문에 대 한 자세한 내용은 man blkid 명령을 실행 합니다.
   > * nofail 옵션은 파일 시스템이 손상되었거나 시작 시 파일 시스템이 없는 경우에도 VM이 시작되는지 확인하는 데 도움이 됩니다. Fstab 파일에서 nofail 옵션을 사용하여 VM을 시작하는 데 필요하지 않은 파티션에서 오류가 발생한 후 시작을 계속할 수 있도록 하는 것이 좋습니다.

5. VM이 올바르게 시작될 수 있도록 fstab 파일의 부정확하거나 불필요한 줄을 변경하거나 주석을 달수 합니다.

6. 변경 내용을 fstab 파일에 저장합니다.

7. 아래 명령을 사용하여 vm을 재부팅합니다.
   
   ```
   # reboot -f
   ```
> [!Note]
   > 또한 vm을 재부팅 할 "ctrl + x"명령을 사용할 수 있습니다.


8. 항목주석 이나 수정에 성공 하면 시스템은 포털에서 bash 프롬프트에 도달 해야 합니다. VM에 연결할 수 있는지 확인합니다.

### <a name="using-root-password"></a>루트 암호 사용

1. [직렬 콘솔에](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-linux)연결합니다.
2. 로컬 사용자 및 암호를 사용하여 시스템에 로그인합니다.

   > [!Note]
   > SSH 키를 사용하여 직렬 콘솔에서 시스템에 로그인할 수 없습니다.

3. 디스크가 탑재되지 않았음을 나타내는 오류를 찾습니다. 다음 예제에서는 시스템이 더 이상 존재하지 않는 디스크를 연결하려고 했습니다.

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

4. 루트 암호(Red Hat 기반 VM)를 사용하여 VM에 연결합니다.

5. 즐겨 찾는 텍스트 편집기를 사용하여 fstab 파일을 엽니다. 디스크를 탑재한 후 Nano에 대해 다음 명령을 실행합니다.

   ```
   $ nano /mnt/troubleshootingdisk/etc/fstab
   ```

6. 나열된 파일 시스템을 검토합니다. fstab 파일의 각 줄은 VM이 시작될 때 탑재되는 파일 시스템을 나타냅니다. fstab 파일의 구문에 대 한 자세한 내용은 man fstab 명령을 실행 합니다. 시작 실패 문제를 해결하려면 각 줄을 검토하여 구조와 콘텐츠 모두에서 올바른지 확인합니다.

   > [!Note]
   > * 각 줄의 필드는 탭이나 공백으로 구분됩니다. 빈 줄은 무시됩니다. 첫 번째 문자로 숫자 기호(#)가 있는 줄은 주석입니다. 주석 처리된 줄은 fstab 파일에 남아 있을 수 있지만 처리되지는 않습니다. 선을 제거하는 대신 확실하지 않은 fstab 줄에 주석을 달수 하는 것이 좋습니다.
   > * VM이 복구되고 시작되려면 파일 시스템 파티션이 필요한 파티션일 뿐이어야 합니다. VM에서 추가 주석 파티션에 대한 응용 프로그램 오류가 발생할 수 있습니다. 그러나 VM은 추가 파티션 없이 시작해야 합니다. 나중에 주석이 있는 줄에 대한 주석 을 해제할 수 있습니다.
   > * 파일 시스템 파티션의 UUID를 사용하여 Azure VM에 데이터 디스크를 탑재하는 것이 좋습니다. 예를 들어 다음 명령을 실행합니다.``/dev/sdc1: LABEL="cloudimg-rootfs" UUID="<UUID>" TYPE="ext4" PARTUUID="<PartUUID>"``
   > * 파일 시스템의 UUID를 확인하려면 blkid 명령을 실행합니다. 구문에 대 한 자세한 내용은 man blkid 명령을 실행 합니다.
   > * nofail 옵션은 파일 시스템이 손상되었거나 시작 시 파일 시스템이 없는 경우에도 VM이 시작되는지 확인하는 데 도움이 됩니다. Fstab 파일에서 nofail 옵션을 사용하여 VM을 시작하는 데 필요하지 않은 파티션에서 오류가 발생한 후 시작을 계속할 수 있도록 하는 것이 좋습니다.

7. VM이 올바르게 시작될 수 있도록 fstab 파일의 부정확하거나 불필요한 줄을 변경하거나 주석을 달수 합니다.

8. 변경 내용을 fstab 파일에 저장합니다.

9. 가상 머신을 다시 시작합니다.

10. 항목주석 이나 수정에 성공 하면 시스템은 포털에서 bash 프롬프트에 도달 해야 합니다. VM에 연결할 수 있는지 확인합니다.

11. 마운트-명령을 실행하여 fstab 변경 사항을 테스트할 때 마운트 지점을 확인합니다. 오류가 없는 경우 탑재 지점이 양호해야 합니다.

## <a name="repair-the-vm-offline"></a>오프라인으로 VM 복구

1. VM의 시스템 디스크를 복구 VM(모든 작업 Linux VM)에 데이터 디스크로 연결합니다. 이렇게 하려면 [CLI 명령을](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/troubleshoot-recovery-disks-linux) 사용하거나 [VM 복구 명령을](repair-linux-vm-using-azure-virtual-machine-repair-commands.md)사용하여 복구 VM 설정을 자동화할 수 있습니다.

2. 시스템 디스크를 복구 VM에 데이터 디스크로 탑재한 후 변경하기 전에 fstab 파일을 백업한 다음 다음 단계를 수행하여 fstab 파일을 수정합니다.

3.    디스크가 탑재되지 않았음을 나타내는 오류를 찾습니다. 다음 예제에서는 시스템이 더 이상 존재하지 않는 디스크를 연결하려고 했습니다.

    ```
    [DEPEND] Dependency failed for /datadisk1.
    [DEPEND] Dependency failed for Local File Systems.
    [DEPEND] Dependency failed for Relabel all filesystems, if necessary.
    [DEPEND] Dependency failed for Migrate local... structure to the new structure.
    Welcome to emergency mode! After logging in, type "journalctl -xb" to view system logs, "systemctl reboot" to reboot, "systemctl default" or ^D to try again to boot into default mode.
    Give root password for maintenance (or type Control-D to continue):
    ```

4. 루트 암호(Red Hat 기반 VM)를 사용하여 VM에 연결합니다.

5. 즐겨 찾는 텍스트 편집기를 사용하여 fstab 파일을 엽니다. 디스크를 탑재한 후 Nano에 대해 다음 명령을 실행합니다. 복구 VM에 있는 fstab 파일이 아니라 탑재된 디스크에 있는 fstab 파일에서 작업하고 있는지 확인합니다.

   ```
   $ nano /mnt/troubleshootingdisk/etc/fstab
   ```

6. 나열된 파일 시스템을 검토합니다. fstab 파일의 각 줄은 VM이 시작될 때 탑재되는 파일 시스템을 나타냅니다. fstab 파일의 구문에 대 한 자세한 내용은 man fstab 명령을 실행 합니다. 시작 실패 문제를 해결하려면 각 줄을 검토하여 구조와 콘텐츠 모두에서 올바른지 확인합니다.

   > [!Note]
   > * 각 줄의 필드는 탭이나 공백으로 구분됩니다. 빈 줄은 무시됩니다. 첫 번째 문자로 숫자 기호(#)가 있는 줄은 주석입니다. 주석 처리된 줄은 fstab 파일에 남아 있을 수 있지만 처리되지는 않습니다. 선을 제거하는 대신 확실하지 않은 fstab 줄에 주석을 달수 하는 것이 좋습니다.
   > * VM이 복구되고 시작되려면 파일 시스템 파티션이 필요한 파티션일 뿐이어야 합니다. VM에서 추가 주석 파티션에 대한 응용 프로그램 오류가 발생할 수 있습니다. 그러나 VM은 추가 파티션 없이 시작해야 합니다. 나중에 주석이 있는 줄에 대한 주석 을 해제할 수 있습니다.
   > * 파일 시스템 파티션의 UUID를 사용하여 Azure VM에 데이터 디스크를 탑재하는 것이 좋습니다. 예를 들어 다음 명령을 실행합니다.``/dev/sdc1: LABEL="cloudimg-rootfs" UUID="<UUID>" TYPE="ext4" PARTUUID="<PartUUID>"``
   > * 파일 시스템의 UUID를 확인하려면 blkid 명령을 실행합니다. 구문에 대 한 자세한 내용은 man blkid 명령을 실행 합니다. 복구하려는 디스크가 새 VM에 탑재되었습니다. UUID는 일관성이 있어야 하지만 장치 파티션 ID(예: "/dev/sda1")는 이 VM에서 다릅니다. 비시스템 VHD에 있는 원래 실패한 VM의 파일 시스템 파티션은 [CLI 명령을 사용하여](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/troubleshoot-recovery-disks-linux)복구 VM에 사용할 수 없습니다.
   > * nofail 옵션은 파일 시스템이 손상되었거나 시작 시 파일 시스템이 없는 경우에도 VM이 시작되는지 확인하는 데 도움이 됩니다. Fstab 파일에서 nofail 옵션을 사용하여 VM을 시작하는 데 필요하지 않은 파티션에서 오류가 발생한 후 시작을 계속할 수 있도록 하는 것이 좋습니다.

7. VM이 올바르게 시작될 수 있도록 fstab 파일의 부정확하거나 불필요한 줄을 변경하거나 주석을 달수 합니다.

8. 변경 내용을 fstab 파일에 저장합니다.

9. 가상 컴퓨터를 다시 시작하거나 원래 VM을 다시 빌드합니다.

10. 항목주석 이나 수정에 성공 하면 시스템은 포털에서 bash 프롬프트에 도달 해야 합니다. VM에 연결할 수 있는지 확인합니다.

11. 마운트-명령을 실행하여 fstab 변경 사항을 테스트할 때 마운트 지점을 확인합니다. 오류가 없는 경우 탑재 지점이 양호해야 합니다.

12. 원래 가상 하드 디스크의 마운트를 해제하고 분리한 다음 원래 시스템 디스크에서 VM을 만듭니다. 이렇게 하려면 [CLI 명령](troubleshoot-recovery-disks-linux.md) 또는 [VM 복구 명령을](repair-linux-vm-using-azure-virtual-machine-repair-commands.md) 사용 하는 경우 복구 VM을 만들 려면 사용할 수 있습니다.

13. VM을 다시 만들고 SSH를 통해 VM에 연결할 수 있는 후 다음 작업을 수행합니다.
    * 복구 중에 변경되거나 주석이 주석이 없는 fstab 라인을 검토합니다.
    * UUID 및 nofail 옵션을 적절하게 사용하고 있는지 확인합니다.
    * VM을 다시 시작하기 전에 fstab 변경 사항을 테스트합니다. 이렇게 하려면 다음 명령을 사용합니다.``$ sudo mount -a``
    * 향후 복구 시나리오에서 사용할 수정된 fstab 파일의 추가 복사본을 만듭니다.

## <a name="next-steps"></a>다음 단계

* [Azure CLI 2.0을 사용하여 OS 디스크를 복구 VM에 연결함으로써 Linux VM 문제 해결](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-linux-troubleshoot-recovery-disks)
* [Azure Portal을 사용하여 OS 디스크를 복구 VM에 연결함으로써 Linux VM 문제 해결](https://docs.microsoft.com/azure/virtual-machines/linux/troubleshoot-recovery-disks-portal)

