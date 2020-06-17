---
title: GPT 파티션이 있는 OS 디스크 크기 조정 | Microsoft Docs
description: 이 문서에서는 GPT 파티션이 있는 OS 디스크의 크기를 조정하는 방법을 설명합니다.
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
ms.openlocfilehash: 7c408e8e29b3f9ac423a6104c40242f11f93a171
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/19/2020
ms.locfileid: "83651097"
---
# <a name="resize-an-os-disk-that-has-a-gpt-partition"></a>GPT 파티션이 있는 OS 디스크 크기 조정

> [!NOTE]
> 이 시나리오는 GPT(GUID 파티션 테이블) 파티션이 있는 OS 디스크에만 적용됩니다.

이 문서에서는 Linux에서 GPT 파티션이 있는 OS 디스크의 크기를 늘리는 방법을 설명합니다. 

## <a name="identify-whether-the-os-disk-has-an-mbr-or-gpt-partition"></a>OS 디스크에 MBR 파티션과 GPT 파티션 중 어느 것이 있는지 확인

**parted** 명령을 사용하여 디스크 파티션이 MBR(마스터 부트 레코드) 파티션과 GPT 파티션 중 어느 것으로 만들어졌는지 확인합니다.

### <a name="mbr-partition"></a>MBR 파티션

다음 출력에서 **Partition Table** 값이 **msdos**로 표시됩니다. 이 값은 MBR 파티션임을 나타냅니다.

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

다음 출력에서 **Partition Table** 값이 **gpt**로 표시됩니다. 이 값은 GPT 파티션임을 나타냅니다.

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

VM(가상 머신)의 OS 디스크에 GPT 파티션이 있는 경우 OS 디스크의 크기를 늘립니다.

## <a name="increase-the-size-of-the-os-disk"></a>OS 디스크의 크기 늘리기

다음 지침은 Linux 보증 배포판에 적용됩니다.

> [!NOTE]
> 계속하기 전에 VM의 백업 복사본을 만들거나 OS 디스크의 스냅샷을 만드세요.

### <a name="ubuntu"></a>Ubuntu

Ubuntu 16.x 및 18.x에서 OS 디스크의 크기를 늘리려면:

1. VM을 중지합니다.
1. 포털에서 OS 디스크의 크기를 늘립니다.
1. VM을 다시 시작한 다음 **루트** 사용자로 VM에 로그인합니다.
1. OS 디스크에 늘어난 파일 시스템 크기가 표시되는지 확인합니다.

다음 예에서 볼 수 있듯이 포털에서 OS 디스크의 크기가 100GB로 조정되었습니다. **/** 에 탑재된 **/dev/sda1** 파일 시스템이 이제 97GB로 표시됩니다.

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

### <a name="suse"></a>SUSE

SUSE 12 SP4, SAP용 SUSE SLES 12, SUSE SLES 15, SAP용 SUSE SLES 15에서 OS 디스크의 크기를 늘리려면:

1. VM을 중지합니다.
1. 포털에서 OS 디스크의 크기를 늘립니다.
1. VM을 다시 시작합니다.

VM이 다시 시작되면 다음 단계를 수행합니다.

   1. 다음 명령을 사용하여 **루트** 사용자로 VM에 액세스합니다.
   
      `#sudo su`

   1. 다음 명령을 사용하여 OS 디스크의 크기를 늘리는 데 필요한 **gptfdisk** 패키지를 설치합니다.

      `#zypper install gptfdisk -y`

   1. 디스크에서 사용할 수 있는 가장 큰 섹터를 보려면 다음 명령을 실행합니다.

      `#sgdisk -e /dev/sda`

   1. 다음 명령을 사용하여 파티션을 삭제하지 않고 크기를 조정합니다. **parted** 명령에는 파티션을 삭제하지 않고 크기를 조정하는 **resizepart**라는 옵션이 있습니다. **resizepart** 뒤에 오는 숫자 4는 네 번째 파티션의 크기가 조정됨을 나타냅니다.

      `#parted -s /dev/sda "resizepart 4 -1" quit`

   1. **#lsblk** 명령을 실행하여 파티션이 늘어났는지 확인합니다.

      다음 출력에서 **/dev/sda4** 파티션이 98.5GB로 크기 조정된 것을 볼 수 있습니다.

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
      
   1. 다음 명령을 사용하여 OS 디스크의 파일 시스템 유형을 식별합니다.

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

   1. 파일 시스템 유형을 기준으로 적절한 명령을 사용하여 파일 시스템의 크기를 조정합니다.

      **xfs**의 경우 다음 명령을 사용합니다.

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

      **ext4**의 경우 다음 명령을 사용합니다.

      ```#resize2fs /dev/sda4```

   1. 다음 명령을 사용하여 **df -Th**의 파일 시스템 크기가 늘어났는지 확인합니다.

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

앞의 예제에서 OS 디스크의 파일 시스템 크기가 늘어난 것을 확인할 수 있습니다.

### <a name="rhel"></a>RHEL

LVM을 사용하여 RHEL 7.x에서 OS 디스크의 크기를 늘리려면:

1. VM을 중지합니다.
1. 포털에서 OS 디스크의 크기를 늘립니다.
1. VM을 시작합니다.

VM이 다시 시작되면 다음 단계를 수행합니다.

   1. 다음 명령을 사용하여 **루트** 사용자로 VM에 액세스합니다.
   
      `#sudo su`

   1. OS 디스크의 크기를 늘리는 데 필요한 **gptfdisk** 패키지를 설치합니다.

      `#yum install gdisk -y`

   1. 디스크에서 사용할 수 있는 가장 큰 섹터를 보려면 다음 명령을 실행합니다.

      `#sgdisk -e /dev/sda`

   1. 다음 명령을 사용하여 파티션을 삭제하지 않고 크기를 조정합니다. **parted** 명령에는 파티션을 삭제하지 않고 크기를 조정하는 **resizepart**라는 옵션이 있습니다. **resizepart** 뒤에 오는 숫자 4는 네 번째 파티션의 크기가 조정됨을 나타냅니다.

      `#parted -s /dev/sda "resizepart 4 -1" quit`
    
   1. 다음 명령을 실행하여 파티션이 늘어났는지 확인합니다.

      `#lsblk`

      다음 출력에서 **/dev/sda4** 파티션이 99GB로 크기 조정된 것을 볼 수 있습니다.

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

   1. 다음 명령을 사용하여 PV(실제 볼륨)의 크기를 조정합니다.

      `#pvresize /dev/sda4`

      다음 출력에서 PV가 99.02GB로 크기 조정된 것을 볼 수 있습니다.

      ```
      [user@myvm ~]# pvresize /dev/sda4
      Physical volume "/dev/sda4" changed
      1 physical volume(s) resized or updated / 0 physical volume(s) not resized

      [user@myvm ~]# pvs
      PV         VG     Fmt  Attr PSize   PFree
      /dev/sda4  rootvg lvm2 a--  <99.02g <74.02g
      ```

   1. 다음 예제에서는 다음 명령에 의해 **/dev/mapper/rootvg-rootlv**가 2GB에서 12GB로 크기 조정됩니다(10GB 증가). 이 명령은 파일 시스템의 크키도 조정합니다.

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
         
   1. 다음 명령을 사용하여 **/dev/mapper/rootvg-rootlv**의 파일 시스템 크기가 증가했는지 확인합니다.

      `#df -Th /`

      예제 출력:

      ```
      [user@myvm ~]# df -Th /
      Filesystem                Type  Size  Used Avail Use% Mounted on
      /dev/mapper/rootvg-rootlv xfs    12G   71M   12G   1% /
      [user@myvm ~]#
      ```

   > [!NOTE]
   > 동일한 절차를 사용하여 다른 논리 볼륨의 크기를 조정하려면 7단계에서 **lv** 이름을 변경하세요.

## <a name="next-steps"></a>다음 단계

- [디스크 크기 조정](expand-disks.md)