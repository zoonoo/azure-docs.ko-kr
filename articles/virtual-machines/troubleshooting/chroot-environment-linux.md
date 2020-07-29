---
title: Linux 복구 VM의 chroot 환경
description: 이 문서에서는 Linux의 VM (가상 머신)을 복구 하는 chroot 환경의 문제를 해결 하는 방법을 설명 합니다.
mservices: virtual-machines-linux
documentationcenter: ''
author: kailashmsft
manager: dcscontentpm
editor: ''
tags: ''
ms.service: virtual-machines-linux
ms.topic: troubleshooting
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.date: 05/05/2020
ms.author: kaib
ms.openlocfilehash: d6605aa77cfa746f60fc3b23e120267a2614f7b1
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "82864683"
---
# <a name="chroot-environment-in-a-linux-rescue-vm"></a>Linux 복구 VM의 chroot 환경

이 문서에서는 Linux의 VM (가상 머신)을 복구 하는 chroot 환경의 문제를 해결 하는 방법을 설명 합니다.

## <a name="ubuntu-16x--ubuntu-18x"></a>Ubuntu 16.x && Ubuntu 18.x

1. 영향을 받는 VM을 중지 하거나 할당 취소 합니다.
1. 동일한 리소스 그룹 (RSG) 및 관리 디스크를 사용 하는 위치에 동일한 OS 버전의 복구 VM 이미지를 만듭니다.
1. Azure Portal를 사용 하 여 영향을 받는 가상 컴퓨터의 OS 디스크에 대 한 스냅숏을 만듭니다.
1. OS 디스크의 스냅숏에서 디스크를 만든 후 복구 VM에 연결 합니다.
1. 디스크를 만든 후에는 복구 VM에서 chroot 환경 문제를 해결 합니다.

   1. 다음 명령을 사용 하 여 루트 사용자로 VM에 액세스 합니다.

      `#sudo su -`

   1. 을 사용 하 여 디스크를 찾습니다 `dmesg` (새 디스크를 검색 하는 데 사용 하는 방법은 다를 수 있음). 다음 예제에서는 **dmesg** 를 사용 하 여 **SCSI** 디스크를 필터링 합니다.

      `dmesg | grep SCSI`

      출력은 다음 예제와 유사 합니다. 이 예제에서는 **sdc** 디스크가 필요 합니다.

      ```
      [    0.294784] SCSI subsystem initialized
      [    0.573458] Block layer SCSI generic (bsg) driver version 0.4 loaded (major 252)
      [    7.110271] sd 2:0:0:0: [sda] Attached SCSI disk
      [    8.079653] sd 3:0:1:0: [sdb] Attached SCSI disk
      [ 1828.162306] sd 5:0:0:0: [sdc] Attached SCSI disk
      ```

   1. 다음 명령을 사용 하 여 chroot 환경에 액세스 합니다.

      ```
      #mkdir /rescue
      #mount /dev/sdc1 /rescue
      #mount /dev/sdc15 /rescue/boot/efi
      #cd /rescue

      #mount -t proc proc proc
      #mount -t sysfs sys sys/
      #mount -o bind /dev dev/
      #mount -o bind /dev/pts dev/pts/
      #mount -o bind /run run/
      #chroot /rescue
      ```

   1. Chroot 환경 문제를 해결 합니다.

   1. 다음 명령을 사용 하 여 chroot 환경을 종료 합니다.

      ```
      #exit

      #umount /rescue/proc/
      #umount /rescue/sys/
      #umount /rescue/dev/pts
      #umount /rescue/dev/
      #umount /rescue/run
      #cd /
      #umount /rescue/boot/efi
      #umount /rescue
      ```

      > [!NOTE]
      > 오류가 표시 `unable to unmount /rescue` 되 면-l 옵션을 분리할 명령에 추가 합니다.
      >
      > 예: `umount -l /rescue`

1. 복구 VM에서 디스크를 분리 하 고 원래 VM을 사용 하 여 디스크 교체를 수행 합니다.
1. 원본 VM을 시작 하 고 연결을 확인 합니다.

## <a name="rhelcentosoracle-6x--oracle-8x--rhelcentos-7x-with-raw-partitions"></a>RHEL/Centos/Oracle 6.x && Oracle 4.x && RHEL/Centos 4.x (원시 파티션 포함)

1. 영향을 받는 VM을 중지 하거나 할당 취소 합니다.
1. 동일한 리소스 그룹 (RSG) 및 관리 디스크를 사용 하는 위치에 동일한 OS 버전의 복구 VM 이미지를 만듭니다.
1. Azure Portal를 사용 하 여 영향을 받는 가상 컴퓨터의 OS 디스크에 대 한 스냅숏을 만듭니다.
1. OS 디스크의 스냅숏에서 디스크를 만든 후 복구 VM에 연결 합니다.
1. 디스크를 만든 후에는 복구 VM에서 chroot 환경 문제를 해결 합니다.

   1. 다음 명령을 사용 하 여 루트 사용자로 VM에 액세스 합니다.

      `#sudo su -`

   1. 을 사용 하 여 디스크를 찾습니다 `dmesg` (새 디스크를 검색 하는 데 사용 하는 방법은 다를 수 있음). 다음 예제에서는 **dmesg** 를 사용 하 여 **SCSI** 디스크를 필터링 합니다.

      `dmesg | grep SCSI`

      출력은 다음 예제와 유사 합니다. 이 예제에서는 **sdc** 디스크가 필요 합니다.

      ```
      [    0.294784] SCSI subsystem initialized
      [    0.573458] Block layer SCSI generic (bsg) driver version 0.4 loaded (major 252)
      [    7.110271] sd 2:0:0:0: [sda] Attached SCSI disk
      [    8.079653] sd 3:0:1:0: [sdb] Attached SCSI disk
      [ 1828.162306] sd 5:0:0:0: [sdc] Attached SCSI disk
      ```

   1. 다음 명령을 사용 하 여 chroot 환경에 액세스 합니다.

      ```
      #mkdir /rescue
      #mount -o nouuid /dev/sdc2 /rescue
      #mount -o nouuid /dev/sdc1 /rescue/boot/
      #cd /rescue

      #mount -t proc proc proc
      #mount -t sysfs sys sys/
      #mount -o bind /dev dev/
      #mount -o bind /dev/pts dev/pts/
      #mount -o bind /run run/
      #chroot /rescue
      ```

   1. Chroot 환경 문제를 해결 합니다.

   1. 다음 명령을 사용 하 여 chroot 환경을 종료 합니다.

      ```
      #exit

      #umount /rescue/proc/
      #umount /rescue/sys/
      #umount /rescue/dev/pts
      #umount /rescue/dev/
      #umount /rescue/run
      #cd /
      #umount /rescue/boot/efi
      #umount /rescue
      ```

      > [!NOTE]
      > 오류가 표시 `unable to unmount /rescue` 되 면-l 옵션을 분리할 명령에 추가 합니다.
      >
      > 예: `umount -l /rescue`

1. 복구 VM에서 디스크를 분리 하 고 원래 VM을 사용 하 여 디스크 교체를 수행 합니다.
1. 원본 VM을 시작 하 고 연결을 확인 합니다.

## <a name="rhelcentos-7x-with-lvm"></a>LVM을 사용 하는 RHEL/Centos 7.x

   > [!NOTE]
   > 원본 VM에 OS 디스크의 LVM (논리 볼륨 관리자)이 포함 된 경우 OS 디스크에 원시 파티션이 있는 이미지를 사용 하 여 복구 VM을 만듭니다.

1. 영향을 받는 VM을 중지 하거나 할당 취소 합니다.
1. 동일한 리소스 그룹 (RSG) 및 관리 디스크를 사용 하는 위치에 동일한 OS 버전의 복구 VM 이미지를 만듭니다.
1. Azure Portal를 사용 하 여 영향을 받는 가상 컴퓨터의 OS 디스크에 대 한 스냅숏을 만듭니다.
1. OS 디스크의 스냅숏에서 디스크를 만든 후 복구 VM에 연결 합니다.
1. 디스크를 만든 후에는 복구 VM에서 chroot 환경 문제를 해결 합니다.

   1. 다음 명령을 사용 하 여 루트 사용자로 VM에 액세스 합니다.

      `#sudo su -`

   1. 을 사용 하 여 디스크를 찾습니다 `dmesg` (새 디스크를 검색 하는 데 사용 하는 방법은 다를 수 있음). 다음 예제에서는 **dmesg** 를 사용 하 여 **SCSI** 디스크를 필터링 합니다.

      `dmesg | grep SCSI`

      출력은 다음 예제와 유사 합니다. 이 예제에서는 **sdc** 디스크가 필요 합니다.

      ```
      [    0.294784] SCSI subsystem initialized
      [    0.573458] Block layer SCSI generic (bsg) driver version 0.4 loaded (major 252)
      [    7.110271] sd 2:0:0:0: [sda] Attached SCSI disk
      [    8.079653] sd 3:0:1:0: [sdb] Attached SCSI disk
      [ 1828.162306] sd 5:0:0:0: [sdc] Attached SCSI disk
      ```

   1. 다음 명령을 사용 하 여 논리 볼륨 그룹을 활성화 합니다.

      ```
      #vgscan --mknodes
      #vgchange -ay
      #lvscan
      ```

   1. 명령을 사용 `lsblk` 하 여 lvm 이름을 검색 합니다.

      ```
      [user@myvm ~]$ lsblk
      NAME              MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
      sda                 8:0    0   64G  0 disk
      ├─sda1              8:1    0  500M  0 part /boot
      ├─sda2              8:2    0   63G  0 part /
      sdb                 8:16   0    4G  0 disk
      └─sdb1              8:17   0    4G  0 part /mnt/resource
      sdc                 8:0    0   64G  0 disk
      ├─sdc1              8:1    0  500M  0 part
      ├─sdc2              8:2    0   63G  0 part
      ├─sdc3              8:3    0    2M  0 part
      ├─sdc4              8:4    0   63G  0 part
        ├─rootvg-tmplv  253:0    0    2G  0 lvm  
        ├─rootvg-usrlv  253:1    0   10G  0 lvm  
        ├─rootvg-optlv  253:2    0    2G  0 lvm  
        ├─rootvg-homelv 253:3    0    1G  0 lvm  
        ├─rootvg-varlv  253:4    0    8G  0 lvm  
        └─rootvg-rootlv 253:5    0    2G  0 lvm
      ```

   1. 다음 명령을 사용 하 여 chroot 환경에 액세스 합니다.

      ```
      #mkdir /rescue
      #mount /dev/mapper/rootvg-rootlv /rescue
      #mount /dev/mapper/rootvg-varlv /rescue/var
      #mount /dev/mapper/rootvg-homelv /rescue/home
      #mount /dev/mapper/rootvg-usrlv /rescue/usr
      #mount /dev/mapper/rootvg-tmplv /rescue/tmp
      #mount /dev/mapper/rootvg-optlv /rescue/opt
      #mount /dev/sdc2 /rescue/boot/
      #mount /dev/sdc1 /rescue/boot/efi
      #cd /rescue

      #mount -t proc proc proc
      #mount -t sysfs sys sys/
      #mount -o bind /dev dev/
      #mount -o bind /dev/pts dev/pts/
      #mount -o bind /run run/
      #chroot /rescue
      ```

   1. Chroot 환경 문제를 해결 합니다.

   1. 다음 명령을 사용 하 여 chroot 환경을 종료 합니다.

      ```
      #exit

      #umount /rescue/proc/
      #umount /rescue/sys/
      #umount /rescue/dev/pts
      #umount /rescue/dev/
      #umount /rescue/run
      #cd /
      #umount /rescue/boot/efi
      #umount /rescue/boot
      #umount /rescue/home
      #umount /rescue/var
      #umount /rescue/usr
      #umount /rescue/tmp
      #umount /rescue/opt
      #umount /rescue
      ```

      > [!NOTE]
      > 오류가 표시 `unable to unmount /rescue` 되 면-l 옵션을 분리할 명령에 추가 합니다.
      >
      > 예: `umount -l /rescue`

1. 복구 VM에서 디스크를 분리 하 고 원래 VM을 사용 하 여 디스크 교체를 수행 합니다.
1. 원본 VM을 시작 하 고 연결을 확인 합니다.

## <a name="rhel-8x-with-lvm"></a>LVM을 사용 하는 RHEL .x

   > [!NOTE]
   > 원본 VM에 OS 디스크의 LVM (논리 볼륨 관리자)이 포함 된 경우 OS 디스크에 원시 파티션이 있는 이미지를 사용 하 여 복구 VM을 만듭니다.

1. 영향을 받는 VM을 중지 하거나 할당 취소 합니다.
1. 동일한 리소스 그룹 (RSG) 및 관리 디스크를 사용 하는 위치에 동일한 OS 버전의 복구 VM 이미지를 만듭니다.
1. Azure Portal를 사용 하 여 영향을 받는 가상 컴퓨터의 OS 디스크에 대 한 스냅숏을 만듭니다.
1. OS 디스크의 스냅숏에서 디스크를 만든 후 복구 VM에 연결 합니다.
1. 디스크를 만든 후에는 복구 VM에서 chroot 환경 문제를 해결 합니다.

   1. 다음 명령을 사용 하 여 루트 사용자로 VM에 액세스 합니다.

      `#sudo su -`

   1. 을 사용 하 여 디스크를 찾습니다 `dmesg` (새 디스크를 검색 하는 데 사용 하는 방법은 다를 수 있음). 다음 예제에서는 **dmesg** 를 사용 하 여 **SCSI** 디스크를 필터링 합니다.

      `dmesg | grep SCSI`

      출력은 다음 예제와 유사 합니다. 이 예제에서는 **sdc** 디스크가 필요 합니다.

      ```
      [    0.294784] SCSI subsystem initialized
      [    0.573458] Block layer SCSI generic (bsg) driver version 0.4 loaded (major 252)
      [    7.110271] sd 2:0:0:0: [sda] Attached SCSI disk
      [    8.079653] sd 3:0:1:0: [sdb] Attached SCSI disk
      [ 1828.162306] sd 5:0:0:0: [sdc] Attached SCSI disk
      ```

   1. 다음 명령을 사용 하 여 논리 볼륨 그룹을 활성화 합니다.

      ```
      #vgscan --mknodes
      #vgchange -ay
      #lvscan
      ```

   1. 명령을 사용 `lsblk` 하 여 lvm 이름을 검색 합니다.

      ```
      [user@myvm ~]$ lsblk
      NAME              MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
      sda                 8:0    0   64G  0 disk
      ├─sda1              8:1    0  500M  0 part /boot
      ├─sda2              8:2    0   63G  0 part /
      sdb                 8:16   0    4G  0 disk
      └─sdb1              8:17   0    4G  0 part /mnt/resource
      sdc                 8:0    0   64G  0 disk
      ├─sdc1              8:1    0  500M  0 part
      ├─sdc2              8:2    0   63G  0 part
      │ ├─rootvg-tmplv  253:0    0    2G  0 lvm  
      │ ├─rootvg-usrlv  253:1    0   10G  0 lvm  
      │ ├─rootvg-homelv 253:2    0    1G  0 lvm  
      │ ├─rootvg-varlv  253:3    0    8G  0 lvm  
      │ └─rootvg-rootlv 253:4    0    2G  0 lvm  
      ├─sdc14             8:14   0    4M  0 part
      └─sdc15             8:15   0  495M  0 part
      ```

   1. 다음 명령을 사용 하 여 chroot 환경에 액세스 합니다.

      ```
      #mkdir /rescue
      #mount /dev/mapper/rootvg-rootlv /rescue
      #mount /dev/mapper/rootvg-varlv /rescue/var
      #mount /dev/mapper/rootvg-homelv /rescue/home
      #mount /dev/mapper/rootvg-usrlv /rescue/usr
      #mount /dev/mapper/rootvg-tmplv /rescue/tmp
      #mount /dev/sdc1 /rescue/boot/
      #mount /dev/sdc15 /rescue/boot/efi
      #cd /rescue

      #mount -t proc proc proc
      #mount -t sysfs sys sys/
      #mount -o bind /dev dev/
      #mount -o bind /dev/pts dev/pts/
      #mount -o bind /run run/
      #chroot /rescue
      ```

   1. Chroot 환경 문제를 해결 합니다.

   1. 다음 명령을 사용 하 여 chroot 환경을 종료 합니다.

      ```
      #exit

      #umount /rescue/proc/
      #umount /rescue/sys/
      #umount /rescue/dev/pts
      #umount /rescue/dev/
      #umount /rescue/run
      #cd /
      #umount /rescue/boot/efi
      #umount /rescue/boot
      #umount /rescue/home
      #umount /rescue/var
      #umount /rescue/usr
      #umount /rescue/tmp
      #umount /rescue
      ```

      > [!NOTE]
      > 오류가 표시 `unable to unmount /rescue` 되 면-l 옵션을 분리할 명령에 추가 합니다.
      >
      > 예: `umount -l /rescue`

1. 복구 VM에서 디스크를 분리 하 고 원래 VM을 사용 하 여 디스크 교체를 수행 합니다.
1. 원본 VM을 시작 하 고 연결을 확인 합니다.

## <a name="oracle-7x"></a>Oracle 4.x

1. 영향을 받는 VM을 중지 하거나 할당 취소 합니다.
1. 동일한 리소스 그룹 (RSG) 및 관리 디스크를 사용 하는 위치에 동일한 OS 버전의 복구 VM 이미지를 만듭니다.
1. Azure Portal를 사용 하 여 영향을 받는 가상 컴퓨터의 OS 디스크에 대 한 스냅숏을 만듭니다.
1. OS 디스크의 스냅숏에서 디스크를 만든 후 복구 VM에 연결 합니다.
1. 디스크를 만든 후에는 복구 VM에서 chroot 환경 문제를 해결 합니다.

   1. 다음 명령을 사용 하 여 루트 사용자로 VM에 액세스 합니다.

      `#sudo su -`

   1. 을 사용 하 여 디스크를 찾습니다 `dmesg` (새 디스크를 검색 하는 데 사용 하는 방법은 다를 수 있음). 다음 예제에서는 **dmesg** 를 사용 하 여 **SCSI** 디스크를 필터링 합니다.

      `dmesg | grep SCSI`

      출력은 다음 예제와 유사 합니다. 이 예제에서는 **sdc** 디스크가 필요 합니다.

      ```
      [    0.294784] SCSI subsystem initialized
      [    0.573458] Block layer SCSI generic (bsg) driver version 0.4 loaded (major 252)
      [    7.110271] sd 2:0:0:0: [sda] Attached SCSI disk
      [    8.079653] sd 3:0:1:0: [sdb] Attached SCSI disk
      [ 1828.162306] sd 5:0:0:0: [sdc] Attached SCSI disk
      ```

   1. 다음 명령을 사용 하 여 chroot 환경에 액세스 합니다.

      ```
      #mkdir /rescue
      #mount -o nouuid /dev/sdc2 /rescue
      #mount -o nouuid /dev/sdc1 /rescue/boot/
      #mount /dev/sdc15 /rescue/boot/efi
      #cd /rescue

      #mount -t proc proc proc
      #mount -t sysfs sys sys/
      #mount -o bind /dev dev/
      #mount -o bind /dev/pts dev/pts/
      #mount -o bind /run run/
      ##chroot /rescue
      ```

   1. Chroot 환경 문제를 해결 합니다.

   1. 다음 명령을 사용 하 여 chroot 환경을 종료 합니다.

      ```
      #exit

      #umount /rescue/proc/
      #umount /rescue/sys/
      #umount /rescue/dev/pts
      #umount /rescue/dev/
      #umount /rescue/run
      #cd /
      #umount /rescue/boot/efi
      #umount /rescue/boot
      #umount /rescue
      ```

      > [!NOTE]
      > 오류가 표시 `unable to unmount /rescue` 되 면-l 옵션을 분리할 명령에 추가 합니다.
      >
      > 예: `umount -l /rescue`

1. 복구 VM에서 디스크를 분리 하 고 원래 VM을 사용 하 여 디스크 교체를 수행 합니다.
1. 원본 VM을 시작 하 고 연결을 확인 합니다.

## <a name="suse-sles-12-sp4-suse-sles-12-sp4-for-sap---suse-sles-15-sp1-suse-sles-15-sp1-for-sap"></a>SUSE-SLES 12 SP4, SUSE-SLES 12 SP4 For SAP &&  # # SUSE-SLES 15 SP1, SUSE-SLES 15 SP1 For SAP

1. 영향을 받는 VM을 중지 하거나 할당 취소 합니다.
1. 동일한 리소스 그룹 (RSG) 및 관리 디스크를 사용 하는 위치에 동일한 OS 버전의 복구 VM 이미지를 만듭니다.
1. Azure Portal를 사용 하 여 영향을 받는 가상 컴퓨터의 OS 디스크에 대 한 스냅숏을 만듭니다.
1. OS 디스크의 스냅숏에서 디스크를 만든 후 복구 VM에 연결 합니다.
1. 디스크를 만든 후에는 복구 VM에서 chroot 환경 문제를 해결 합니다.

   1. 다음 명령을 사용 하 여 루트 사용자로 VM에 액세스 합니다.

      `#sudo su -`

   1. 을 사용 하 여 디스크를 찾습니다 `dmesg` (새 디스크를 검색 하는 데 사용 하는 방법은 다를 수 있음). 다음 예제에서는 **dmesg** 를 사용 하 여 **SCSI** 디스크를 필터링 합니다.

      `dmesg | grep SCSI`

      출력은 다음 예제와 유사 합니다. 이 예제에서는 **sdc** 디스크가 필요 합니다.

      ```
      [    0.294784] SCSI subsystem initialized
      [    0.573458] Block layer SCSI generic (bsg) driver version 0.4 loaded (major 252)
      [    7.110271] sd 2:0:0:0: [sda] Attached SCSI disk
      [    8.079653] sd 3:0:1:0: [sdb] Attached SCSI disk
      [ 1828.162306] sd 5:0:0:0: [sdc] Attached SCSI disk
      ```

   1. 다음 명령을 사용 하 여 chroot 환경에 액세스 합니다.

      ```
      #mkdir /rescue
      #mount -o nouuid /dev/sdc4 /rescue
      #mount -o nouuid /dev/sdc3 /rescue/boot/
      #mount /dev/sdc2 /rescue/boot/efi
      #cd /rescue

      #mount -t proc proc proc
      #mount -t sysfs sys sys/
      #mount -o bind /dev dev/
      #mount -o bind /dev/pts dev/pts/
      #mount -o bind /run run/
      #chroot /rescue
      ```

   1. Chroot 환경 문제를 해결 합니다.

   1. 다음 명령을 사용 하 여 chroot 환경을 종료 합니다.

      ```
      #exit

      #umount /rescue/proc/
      #umount /rescue/sys/
      #umount /rescue/dev/pts
      #umount /rescue/dev/
      #umount /rescue/run
      #cd /
      #umount /rescue/boot/efi
      #umount /rescue/boot
      #umount /rescue
      ```

      > [!NOTE]
      > 오류가 표시 `unable to unmount /rescue` 되 면-l 옵션을 분리할 명령에 추가 합니다.
      >
      > 예: `umount -l /rescue`

1. 복구 VM에서 디스크를 분리 하 고 원래 VM을 사용 하 여 디스크 교체를 수행 합니다.
1. 원본 VM을 시작 하 고 연결을 확인 합니다.

## <a name="next-steps"></a>다음 단계

- [Ssh 연결 문제 해결](troubleshoot-ssh-connection.md)