---
title: GPT 파티션을 사용 하 여 OS 디스크 크기 조정 | Microsoft Docs
description: 이 문서에서는 GPT 파티션을 사용 하 여 OS 디스크의 크기를 조정 하는 방법을 설명 합니다.
services: virtual-machines-linux
documentationcenter: ''
author: kailashmsft
manager: dcscontentpm
editor: ''
tags: ''
ms.service: security
ms.topic: troubleshooting
ms.workload: infrastructure-services
ms.devlang: azurecli
ms.date: 05/03/2020
ms.author: kaib
ms.custom: seodec18
ms.openlocfilehash: f863233f0a34271841cc8e973f9aa3ca9416ceeb
ms.sourcegitcommit: c535228f0b77eb7592697556b23c4e436ec29f96
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/06/2020
ms.locfileid: "82858991"
---
# <a name="resize-an-os-disk-with-a-gpt-partition"></a>GPT 파티션이 있는 OS 디스크 크기 조정

> [!NOTE]
> 이 시나리오는 GPT 파티션이 있는 OS 디스크에만 적용 됩니다.

이 문서에서는 Linux에서 GPT 파티션을 사용 하 여 OS 디스크의 크기를 늘리는 방법을 설명 합니다.

## <a name="identify-whether-the-os-disk-has-an-mbr-or-gpt-partition"></a>OS 디스크에 MBR 또는 GPT 파티션이 있는지 확인

**Parted** 명령을 사용 하 여 디스크 파티션이 MBR (마스터 부트 레코드) 파티션이나 GPT (GUID 파티션 테이블) 파티션으로 생성 되었는지 여부를 확인 합니다.

### <a name="mbr-partition"></a>MBR 파티션

다음 출력에서 **파티션 테이블** 은 **MBR** 파티션을 식별 하는 **msdos.sys**값을 표시 합니다.

```
[user@myvm ~]# parted -l /dev/sda
Model: Msft Virtual Disk (scsi)
Disk /dev/sda: 107GB
Sector size (logical/physical): 512B/512B
Partition Table: msdos
Number  Start   End     Size    Type     File system  Flags
1       1049kB  525MB   524MB   primary  ext4         boot
2       525MB   34.4GB  33.8GB  primary  ext4
[user@myvm ~]#
```

### <a name="gpt-partition"></a>GPT 파티션

다음 출력에서 **파티션 테이블** 은 gpt **값을 표시 하 고**gpt 파티션을 식별 합니다.

```
[user@myvm ~]# parted -l /dev/sda
Model: Msft Virtual Disk (scsi)
Disk /dev/sda: 68.7GB
Sector size (logical/physical): 512B/512B
Partition Table: gpt
Disk Flags:

Number  Start   End     Size    File system  Name                  Flags
1       1049kB  525MB   524MB   fat16        EFI System Partition  boot
2       525MB   1050MB  524MB   xfs
3       1050MB  1052MB  2097kB                                     bios_grub
4       1052MB  68.7GB  67.7GB                                     lvm
```

VM (가상 컴퓨터)에 OS 디스크의 GPT 파티션이 있는 경우 OS 디스크의 크기를 늘립니다.

## <a name="increase-the-size-of-the-os-disk"></a>OS 디스크의 크기를 늘립니다.

다음 지침은 Linux 보증 배포판에 적용 됩니다.

> [!NOTE]
> 계속 진행 하기 전에 VM의 백업 복사본을 만들거나 OS 디스크의 스냅숏을 만듭니다.

### <a name="ubuntu-16x-and-18x"></a>Ubuntu 16.x 및 18.x

Ubuntu 16.x 및 18.x에서 OS 디스크의 크기를 늘리려면:

1. VM을 중지합니다.
1. 포털에서 OSDisk 크기를 늘립니다.
1. VM을 다시 시작한 다음 **루트** 사용자로 vm에 로그인 합니다.
1. 이제 OSDisk에 증가 된 파일 시스템 크기가 표시 됩니다.

다음 예제에 표시 된 것 처럼 OS 디스크는 **/** 현재 탑재 된 **/dev/sda1** 파일 시스템이 97 gb를 표시 하므로 포털에서 100 GB로 크기가 조정 되었습니다.

```
user@myvm:~# df -Th
Filesystem     Type      Size  Used Avail Use% Mounted on
udev           devtmpfs  314M     0  314M   0% /dev
tmpfs          tmpfs      65M  2.3M   63M   4% /run
/dev/sda1      ext4       97G  1.8G   95G   2% /
tmpfs          tmpfs     324M     0  324M   0% /dev/shm
tmpfs          tmpfs     5.0M     0  5.0M   0% /run/lock
tmpfs          tmpfs     324M     0  324M   0% /sys/fs/cgroup
/dev/sda15     vfat      105M  3.6M  101M   4% /boot/efi
/dev/sdb1      ext4       20G   44M   19G   1% /mnt
tmpfs          tmpfs      65M     0   65M   0% /run/user/1000
user@myvm:~#
```

### <a name="suse-12-sp4suse-sles-12-for-sap-suse-sles-15-and-suse-sles-15-for-sap"></a>SUSE 12 SP4, SUSE SLES 12 for SAP, SUSE SLES 15 및 SUSE SLES 15 for SAP

SAP 용 SUSE 12 SP4, SUSE SLES 15 및 SUSE SLES 15에서 OS 디스크의 크기를 늘리려면:

1. VM을 중지합니다.
1. 포털에서 OSDisk 크기를 늘립니다.
1. VM을 다시 시작합니다.

VM이 다시 시작 되 면 다음 단계를 수행 합니다.

   1. 다음 명령을 사용 하 여 **루트 사용자** 로 VM에 액세스 합니다.
   
      `#sudo su`

   1. 다음 명령을 사용 하 여 OS 디스크의 크기를 높이는 데 필요한 **gptfdisk** 패키지를 설치 합니다.

      `#zypper install gptfdisk -y`

   1. 디스크에서 사용할 수 있는 가장 큰 섹터를 보려면 다음 명령을 실행 합니다.

      `#sgdisk -e /dev/sda`

   1. 다음 명령을 사용 하 여 파티션을 삭제 하지 않고 크기를 조정 합니다. **Parted** 명령에는 파티션을 삭제 하지 않고 크기를 조정 하는 **resizepart** 라는 옵션이 있습니다. Resizepart 다음의 숫자 4는 네 번째 (4 번째) 파티션의 크기를 나타냅니다.

      `#parted -s /dev/sda "resizepart 4 -1" quit`

   1. `#lsblk` 명령을 실행 하 여 파티션이 증가 했는지 여부를 확인 합니다.

      다음 출력에서는 **/dev/sda4** 파티션이 98.5 GB로 크기 조정 되었음을 보여 줍니다.

      ```
      user@myvm:~ # lsblk
      NAME   MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
      sda      8:0    0  100G  0 disk
      ├─sda1   8:1    0    2M  0 part
      ├─sda2   8:2    0  512M  0 part /boot/efi
      └─sda4   8:4    0 98.5G  0 part /
      sdb      8:16   0   20G  0 disk
      └─sdb1   8:17   0   20G  0 part /mnt/resource
      ```
      
   1. 다음 명령을 사용 하 여 OSDisk의 파일 시스템 유형을 식별 합니다.

      `blkid`

      예제 출력:

      ```
      #blkid

      user@myvm:~ # blkid
      /dev/sda1: PARTLABEL="p.legacy" PARTUUID="0122fd4c-0069-4a45-bfd4-98b97ccb6e8c"
      /dev/sda2: SEC_TYPE="msdos" LABEL_FATBOOT="EFI" LABEL="EFI" UUID="00A9-D170" TYPE="vfat" PARTLABEL="p.UEFI" PARTUUID="abac3cd8-949b-4e83-81b1-9636493388c7"
      /dev/sda3: LABEL="BOOT" UUID="aa2492db-f9ed-4f5a-822a-1233c06d57cc" TYPE="xfs" PARTLABEL="p.lxboot" PARTUUID="dfb36c61-b15f-4505-8e06-552cf1589cf7"
      /dev/sda4: LABEL="ROOT" UUID="26104965-251c-4e8d-b069-5f5323d2a9ba" TYPE="xfs" PARTLABEL="p.lxroot" PARTUUID="50fecee0-f22b-4406-94c3-622507e2dbce"
      /dev/sdb1: UUID="95239fce-ca97-4f03-a077-4e291588afc9" TYPE="ext4" PARTUUID="953afef3-01"
      ```

   1. 파일 시스템 유형에 따라 적절 한 명령을 사용 하 여 파일 시스템의 크기를 조정 합니다.

      **Xfs**의 경우 다음 명령을 사용 합니다.

      ` #xfs_growfs /`

      예제 출력:

      ```
      user@myvm:~ # xfs_growfs /
      meta-data=/dev/sda4              isize=512    agcount=4, agsize=1867583 blks
               =                       sectsz=512   attr=2, projid32bit=1
               =                       crc=1        finobt=1 spinodes=0 rmapbt=0
               =                       reflink=0
      data     =                       bsize=4096   blocks=7470331, imaxpct=25
               =                       sunit=0      swidth=0 blks
      naming   =version 2              bsize=4096   ascii-ci=0 ftype=1
      log      =internal               bsize=4096   blocks=3647, version=2
               =                       sectsz=512   sunit=0 blks, lazy-count=1
      realtime =none                   extsz=4096   blocks=0, rtextents=0
      data blocks changed from 7470331 to 25820172
      ```

      **Ext4**의 경우 다음 명령을 사용 합니다.

      ```#resize2fs /dev/sda4```

   1. 다음 명령을 사용 하 여 **df**에 대 한 파일 시스템 크기 증가를 확인 합니다.

      `#df -Th`

      예제 출력:

      ```
      user@myvm:~ # df -Th
      Filesystem     Type      Size  Used Avail Use% Mounted on
      devtmpfs       devtmpfs  306M  4.0K  306M   1% /dev
      tmpfs          tmpfs     320M     0  320M   0% /dev/shm
      tmpfs          tmpfs     320M  8.8M  311M   3% /run
      tmpfs          tmpfs     320M     0  320M   0% /sys/fs/cgroup
      /dev/sda4      xfs        99G  1.8G   97G   2% /
      /dev/sda3      xfs      1014M   88M  927M   9% /boot
      /dev/sda2      vfat      512M  1.1M  511M   1% /boot/efi
      /dev/sdb1      ext4       20G   45M   19G   1% /mnt/resource
      tmpfs          tmpfs      64M     0   64M   0% /run/user/1000
      user@myvm:~ #
      ```

앞의 예제와 같이 OSDisk 파일 시스템 크기가 증가 한 것을 볼 수 있습니다.

### <a name="rhel-7x-with-lvm"></a>LVM을 사용 하는 RHEL 7.x

1. VM을 중지합니다.
1. 포털에서 OSDisk 크기를 늘립니다.
1. VM을 시작합니다.

VM이 다시 시작 되 면 다음 단계를 수행 합니다.

   1. 다음 명령을 사용 하 여 **루트 사용자** 로 VM에 액세스 합니다.
   
      `#sudo su`

   1. OS 디스크의 크기를 늘리는 데 필요한 **gptfdisk** 패키지를 설치 합니다.

      `#yum install gdisk -y`

   1. 디스크에서 사용할 수 있는 가장 큰 섹터를 보려면 다음 명령을 실행 합니다.

      `#sgdisk -e /dev/sda`

   1. 다음 명령을 사용 하 여 파티션을 삭제 하지 않고 크기를 조정 합니다. **Parted** 명령에는 파티션을 삭제 하지 않고 크기를 조정 하는 **resizepart** 라는 옵션이 있습니다. Resizepart 다음의 숫자 4는 네 번째 (4 번째) 파티션의 크기를 나타냅니다.

      `#parted -s /dev/sda "resizepart 4 -1" quit`
    
   1. 다음 명령을 실행 하 여 파티션이 증가 했는지 확인 합니다.

      `#lsblk`

      다음 출력에서는 **/dev/sda4** 파티션이 99 GB로 크기 조정 되었음을 보여 줍니다.

      ```
      [user@myvm ~]# lsblk
      NAME              MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
      fd0                 2:0    1    4K  0 disk
      sda                 8:0    0  100G  0 disk
      ├─sda1              8:1    0  500M  0 part /boot/efi
      ├─sda2              8:2    0  500M  0 part /boot
      ├─sda3              8:3    0    2M  0 part
      └─sda4              8:4    0   99G  0 part
      ├─rootvg-tmplv    253:0    0    2G  0 lvm  /tmp
      ├─rootvg-usrlv    253:1    0   10G  0 lvm  /usr
      ├─rootvg-optlv    253:2    0    2G  0 lvm  /opt
      ├─rootvg-homelv   253:3    0    1G  0 lvm  /home
      ├─rootvg-varlv    253:4    0    8G  0 lvm  /var
      └─rootvg-rootlv   253:5    0    2G  0 lvm  /
      sdb                 8:16   0   50G  0 disk
      └─sdb1              8:17   0   50G  0 part /mnt/resource
      ```

   1. 다음 명령을 사용 하 여 **실제 볼륨 (PV)** 의 크기를 조정 합니다.

      `#pvresize /dev/sda4`

      다음 출력은 PV의 크기가 99.02 GB로 조정 되었음을 보여 줍니다.

      ```
      [user@myvm ~]# pvresize /dev/sda4
      Physical volume "/dev/sda4" changed
      1 physical volume(s) resized or updated / 0 physical volume(s) not resized

      [user@myvm ~]# pvs
      PV         VG     Fmt  Attr PSize   PFree
      /dev/sda4  rootvg lvm2 a--  <99.02g <74.02g
      ```

   1. 다음 예 `/dev/mapper/rootvg-rootlv` 에서는 파일 시스템의 크기를 조정 하는 다음 명령을 사용 하 여 2GB에서 12GB (10gb 씩 증가)로 크기를 조정 하 고 있습니다.

      `#lvresize -r -L +10G /dev/mapper/rootvg-rootlv`

      예제 출력:

      ```
      [user@myvm ~]# lvresize -r -L +10G /dev/mapper/rootvg-rootlv
      Size of logical volume rootvg/rootlv changed from 2.00 GiB (512 extents) to 12.00 GiB (3072 extents).
      Logical volume rootvg/rootlv successfully resized.
      meta-data=/dev/mapper/rootvg-rootlv isize=512    agcount=4, agsize=131072 blks
               =                       sectsz=4096  attr=2, projid32bit=1
               =                       crc=1        finobt=0 spinodes=0
      data     =                       bsize=4096   blocks=524288, imaxpct=25
               =                       sunit=0      swidth=0 blks
      naming   =version 2              bsize=4096   ascii-ci=0 ftype=1
      log      =internal               bsize=4096   blocks=2560, version=2
               =                       sectsz=4096  sunit=1 blks, lazy-count=1
      realtime =none                   extsz=4096   blocks=0, rtextents=0
      data blocks changed from 524288 to 3145728
      ```
         
   1. `/dev/mapper/rootvg-rootlv` 에서 다음 명령을 사용 하지 않고 파일 시스템 크기를 증가 했는지 확인 합니다.

      `#df -Th /`

      예제 출력:

      ```
      [user@myvm ~]# df -Th /
      Filesystem                Type  Size  Used Avail Use% Mounted on
      /dev/mapper/rootvg-rootlv xfs    12G   71M   12G   1% /
      [user@myvm ~]#
      ```

      > [!NOTE]
      > 동일한 절차를 사용 하 여 다른 논리 볼륨의 크기를 조정 하려면 7 단계에서 **lv** 이름을 변경 합니다.

## <a name="next-steps"></a>다음 단계

- [디스크 크기 조정](expand-disks.md)