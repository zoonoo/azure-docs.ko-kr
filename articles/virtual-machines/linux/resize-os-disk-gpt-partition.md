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
ms.openlocfilehash: 99b723322ce7636edce3ae5b59a69b96e288ca24
ms.sourcegitcommit: 0ce1ccdb34ad60321a647c691b0cff3b9d7a39c8
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/05/2020
ms.locfileid: "93392693"
---
# <a name="resize-an-os-disk-that-has-a-gpt-partition"></a>GPT 파티션이 있는 OS 디스크 크기 조정

> [!NOTE]
> 이 시나리오는 GPT(GUID 파티션 테이블) 파티션이 있는 OS 디스크에만 적용됩니다.

이 문서에서는 Linux에서 GPT 파티션이 있는 OS 디스크의 크기를 늘리는 방법을 설명합니다. 

## <a name="identify-whether-the-os-disk-has-an-mbr-or-gpt-partition"></a>OS 디스크에 MBR 파티션과 GPT 파티션 중 어느 것이 있는지 확인

명령을 사용 `parted` 하 여 디스크 파티션이 MBR (마스터 부트 레코드) 파티션 또는 GPT 파티션으로 만들어졌는지 식별할 수 있습니다.

### <a name="mbr-partition"></a>MBR 파티션

다음 출력에서 **Partition Table** 값이 **msdos** 로 표시됩니다. 이 값은 MBR 파티션임을 나타냅니다.

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

다음 출력에서 **Partition Table** 값이 **gpt** 로 표시됩니다. 이 값은 GPT 파티션임을 나타냅니다.

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

   ```
   # sudo -i
   ```

1. 다음 명령을 사용 하 여 파티션 크기를 조정 하는 데 사용 되는 **growpart** 패키지를 설치 합니다.

   ```
   # zypper install growpart
   ```

1. 명령을 사용 `lsblk` 하 여 파일 시스템 ("/")의 루트에 탑재 된 파티션을 찾습니다. 이 경우 장치 sda의 파티션 4가/:에 탑재 된 것을 알 수 있습니다.

   ```
   # lsblk
   NAME   MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
   sda      8:0    0   48G  0 disk
   ├─sda1   8:1    0    2M  0 part
   ├─sda2   8:2    0  512M  0 part /boot/efi
   ├─sda3   8:3    0    1G  0 part /boot
   └─sda4   8:4    0 28.5G  0 part /
   sdb      8:16   0    4G  0 disk
   └─sdb1   8:17   0    4G  0 part /mnt/resource
   ```

1. `growpart`이전 단계에서 찾은 파티션 번호를 사용 하 여 명령을 사용 하 여 필요한 파티션의 크기를 조정 합니다.

   ```
   # growpart /dev/sda 4
   CHANGED: partition=4 start=3151872 old: size=59762655 end=62914527 new: size=97511391 end=100663263
   ```

1. `lsblk`명령을 다시 실행 하 여 파티션이 증가 했는지 여부를 확인 합니다.

   다음 출력에서는 **/dev/sda4** 파티션이 46.5 GB로 크기 조정 되었음을 보여 줍니다.
   
   ```
   linux:~ # lsblk
   NAME   MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
   sda      8:0    0   48G  0 disk
   ├─sda1   8:1    0    2M  0 part
   ├─sda2   8:2    0  512M  0 part /boot/efi
   ├─sda3   8:3    0    1G  0 part /boot
   └─sda4   8:4    0 46.5G  0 part /
   sdb      8:16   0    4G  0 disk
   └─sdb1   8:17   0    4G  0 part /mnt/resource
   ```

1. `lsblk`명령을 플래그와 함께 사용 하 여 OS 디스크의 파일 시스템 유형을 식별 합니다 `-f` .

   ```
   linux:~ # lsblk -f
   NAME   FSTYPE LABEL UUID                                 MOUNTPOINT
   sda
   ├─sda1
   ├─sda2 vfat   EFI   AC67-D22D                            /boot/efi
   ├─sda3 xfs    BOOT  5731a128-db36-4899-b3d2-eb5ae8126188 /boot
   └─sda4 xfs    ROOT  70f83359-c7f2-4409-bba5-37b07534af96 /
   sdb
   └─sdb1 ext4         8c4ca904-cd93-4939-b240-fb45401e2ec6 /mnt/resource
   ```

1. 파일 시스템 유형을 기준으로 적절한 명령을 사용하여 파일 시스템의 크기를 조정합니다.
   
   **xfs** 의 경우 다음 명령을 사용합니다.
   
   ```
   #xfs_growfs /
   ```
   
   예제 출력:
   
   ```
   linux:~ # xfs_growfs /
   meta-data=/dev/sda4              isize=512    agcount=4, agsize=1867583 blks
            =                       sectsz=512   attr=2, projid32bit=1
            =                       crc=1        finobt=0 spinodes=0 rmapbt=0
            =                       reflink=0
   data     =                       bsize=4096   blocks=7470331, imaxpct=25
            =                       sunit=0      swidth=0 blks
   naming   =version 2              bsize=4096   ascii-ci=0 ftype=1
   log      =internal               bsize=4096   blocks=3647, version=2
            =                       sectsz=512   sunit=0 blks, lazy-count=1
   realtime =none                   extsz=4096   blocks=0, rtextents=0
   data blocks changed from 7470331 to 12188923
   ```
   
   **ext4** 의 경우 다음 명령을 사용합니다.
   
   ```
   #resize2fs /dev/sda4
   ```
   
1. 다음 명령을 사용하여 **df -Th** 의 파일 시스템 크기가 늘어났는지 확인합니다.
   
   ```
   #df -Thl
   ```
   
   예제 출력:
   
   ```
   linux:~ # df -Thl
   Filesystem     Type      Size  Used Avail Use% Mounted on
   devtmpfs       devtmpfs  445M  4.0K  445M   1% /dev
   tmpfs          tmpfs     458M     0  458M   0% /dev/shm
   tmpfs          tmpfs     458M   14M  445M   3% /run
   tmpfs          tmpfs     458M     0  458M   0% /sys/fs/cgroup
   /dev/sda4      xfs        47G  2.2G   45G   5% /
   /dev/sda3      xfs      1014M   86M  929M   9% /boot
   /dev/sda2      vfat      512M  1.1M  511M   1% /boot/efi
   /dev/sdb1      ext4      3.9G   16M  3.7G   1% /mnt/resource
   tmpfs          tmpfs      92M     0   92M   0% /run/user/1000
   tmpfs          tmpfs      92M     0   92M   0% /run/user/490
   ```
   
   앞의 예제에서 OS 디스크의 파일 시스템 크기가 늘어난 것을 확인할 수 있습니다.

### <a name="rhel-with-lvm"></a>LVM을 사용 하는 RHEL

1. 다음 명령을 사용하여 **루트** 사용자로 VM에 액세스합니다.

   ```bash
   [root@dd-rhel7vm ~]# sudo -i
   ```

1. 명령을 사용 `lsblk` 하 여 파일 시스템 ("/")의 루트에 탑재 된 논리 볼륨 (LV)을 찾습니다. 이 경우 **_rootvg-rootlv_*_가 _* / 에 탑재** 된 것을 알 수 있습니다.  다른 파일 시스템을 원하는 경우이 문서를 통해 LV 및 탑재 지점으로 대체 합니다.

   ```shell
   [root@dd-rhel7vm ~]# lsblk -f
   NAME                  FSTYPE      LABEL   UUID                                   MOUNTPOINT
   fd0
   sda
   ├─sda1                vfat                C13D-C339                              /boot/efi
   ├─sda2                xfs                 8cc4c23c-fa7b-4a4d-bba8-4108b7ac0135   /boot
   ├─sda3
   └─sda4                LVM2_member         zx0Lio-2YsN-ukmz-BvAY-LCKb-kRU0-ReRBzh
      ├─rootvg-tmplv      xfs                 174c3c3a-9e65-409a-af59-5204a5c00550   /tmp
      ├─rootvg-usrlv      xfs                 a48dbaac-75d4-4cf6-a5e6-dcd3ffed9af1   /usr
      ├─rootvg-optlv      xfs                 85fe8660-9acb-48b8-98aa-bf16f14b9587   /opt
      ├─rootvg-homelv     xfs                 b22432b1-c905-492b-a27f-199c1a6497e7   /home
      ├─rootvg-varlv      xfs                 24ad0b4e-1b6b-45e7-9605-8aca02d20d22   /var
      └─rootvg-rootlv     xfs                 4f3e6f40-61bf-4866-a7ae-5c6a94675193   /
   ```

1. 루트 파티션이 포함 된 LVM 볼륨 그룹에 사용 가능한 공간이 있는지 확인 합니다.  여유 공간이 있으면 **12** 단계로 건너뜁니다.

   ```bash
   [root@dd-rhel7vm ~]# vgdisplay rootvg
   --- Volume group ---
   VG Name               rootvg
   System ID
   Format                lvm2
   Metadata Areas        1
   Metadata Sequence No  7
   VG Access             read/write
   VG Status             resizable
   MAX LV                0
   Cur LV                6
   Open LV               6
   Max PV                0
   Cur PV                1
   Act PV                1
   VG Size               <63.02 GiB
   PE Size               4.00 MiB
   Total PE              16132
   Alloc PE / Size       6400 / 25.00 GiB
   Free  PE / Size       9732 / <38.02 GiB
   VG UUID               lPUfnV-3aYT-zDJJ-JaPX-L2d7-n8sL-A9AgJb
   ```

   이 예제에서 **FREE PE/Size** 줄은 볼륨 그룹에서 38.02 gb를 사용할 수 있음을 명시 합니다.  볼륨 그룹에 공간을 추가 하기 전에 디스크 크기를 조정할 필요가 없습니다.

1. LVM을 사용하여 RHEL 7.x에서 OS 디스크의 크기를 늘리려면:

   1. VM을 중지합니다.
   1. 포털에서 OS 디스크의 크기를 늘립니다.
   1. VM을 시작합니다.

1. VM이 다시 시작되면 다음 단계를 수행합니다.

   1. **유틸리티-growpart** 패키지를 설치 하 여 OS 디스크의 크기를 늘리는 데 필요한 **growpart** 명령을 제공 합니다.

      이 패키지는 대부분의 Azure Marketplace 이미지에 미리 설치 되어 있습니다.

      ```bash
      [root@dd-rhel7vm ~]# yum install cloud-utils-growpart
      ```

1. **Pvscan** 명령을 사용 하 여 rootvg 라는 볼륨 그룹 (VG)의 lvm 물리적 볼륨 (PV)을 보유 하는 디스크 및 파티션을 결정 합니다.  대괄호 **[]** 사이에 표시 되는 크기와 사용 가능한 공간을 기록해 둡니다.

   ```bash
   [root@dd-rhel7vm ~]# pvscan
     PV /dev/sda4   VG rootvg          lvm2 [<63.02 GiB / <38.02 GiB free]
   ```

1. **Lsblk** 를 사용 하 여 파티션 크기를 확인 합니다.  다음을 확인 합니다. 

   ```bash
   [root@dd-rhel7vm ~]# lsblk /dev/sda4
   NAME            MAJ:MIN RM SIZE RO TYPE MOUNTPOINT
   sda4              8:4    0  63G  0 part
   ├─rootvg-tmplv  253:1    0   2G  0 lvm  /tmp
   ├─rootvg-usrlv  253:2    0  10G  0 lvm  /usr
   ├─rootvg-optlv  253:3    0   2G  0 lvm  /opt
   ├─rootvg-homelv 253:4    0   1G  0 lvm  /home
   ├─rootvg-varlv  253:5    0   8G  0 lvm  /var
   └─rootvg-rootlv 253:6    0   2G  0 lvm  /
   ```

1. **Growpart** , 장치 이름 및 파티션 번호를 사용 하 여이 PV를 포함 하는 파티션을 확장 합니다.  그러면 지정 된 파티션이 확장 되어 장치에서 사용 가능한 모든 연속 공간이 사용 됩니다.

   ```bash
   [root@dd-rhel7vm ~]# growpart /dev/sda 4
   CHANGED: partition=4 start=2054144 old: size=132161536 end=134215680 new: size=199272414 end=201326558
   ```

1. 파티션이 **lsblk** 명령을 사용 하 여 예상 크기로 크기 조정 되었는지 확인 합니다.  예제 sda4는 63G에서 95G로 변경 되었습니다.

   ```bash
   [root@dd-rhel7vm ~]# lsblk /dev/sda4
   NAME            MAJ:MIN RM SIZE RO TYPE MOUNTPOINT
   sda4              8:4    0  95G  0 part
   ├─rootvg-tmplv  253:1    0   2G  0 lvm  /tmp
   ├─rootvg-usrlv  253:2    0  10G  0 lvm  /usr
   ├─rootvg-optlv  253:3    0   2G  0 lvm  /opt
   ├─rootvg-homelv 253:4    0   1G  0 lvm  /home
   ├─rootvg-varlv  253:5    0   8G  0 lvm  /var
   └─rootvg-rootlv 253:6    0   2G  0 lvm  /
   ```

1. 새로 확장 된 파티션의 나머지를 사용 하려면 PV를 확장 합니다.

   ```bash
   [root@dd-rhel7vm ~]# pvresize /dev/sda4
   Physical volume "/dev/sda4" changed
   1 physical volume(s) resized or updated / 0 physical volume(s) not resized
   ```

1. 원래 **[크기/무료]** 값과 비교 하 여 PV의 새 크기가 예상 크기 인지 확인 합니다.

   ```bash
   [root@dd-rhel7vm ~]# pvscan
   PV /dev/sda4   VG rootvg          lvm2 [<95.02 GiB / <70.02 GiB free]
   ```

1. 원하는 크기 만큼 원하는 논리 볼륨 (lv)을 확장 합니다. 볼륨 그룹의 모든 여유 공간이 필요 하지는 않습니다.  다음 예제에서는 다음 명령에 의해 **/dev/mapper/rootvg-rootlv** 가 2GB에서 12GB로 크기 조정됩니다(10GB 증가). 이 명령은 파일 시스템의 크키도 조정합니다.

   ```bash
   [root@dd-rhel7vm ~]# lvresize -r -L +10G /dev/mapper/rootvg-rootlv
   ```

   예제 출력:

   ```bash
   [root@dd-rhel7vm ~]# lvresize -r -L +10G /dev/mapper/rootvg-rootlv
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

1. Lvresize 명령은 LV에서 파일 시스템에 대해 적절 한 크기 조정 명령을 자동으로 호출 합니다. 다음 명령을 사용 하 여에 탑재 된 **/dev/mapper/rootvg-rootlv** 의 **/** 파일 시스템 크기가 증가 하는지 여부를 확인 합니다.

   ```shell
   [root@dd-rhel7vm ~]# df -Th /
   ```

   예제 출력:

   ```shell
   [root@dd-rhel7vm ~]# df -Th /
   Filesystem                Type  Size  Used Avail Use% Mounted on
   /dev/mapper/rootvg-rootlv xfs    12G   71M   12G   1% /
   [root@dd-rhel7vm ~]#
   ```

> [!NOTE]
> 동일한 절차를 사용 하 여 다른 논리 볼륨의 크기를 조정 하려면 **12** 단계에서 **lv** 이름을 변경 합니다.

### <a name="rhel-raw"></a>RHEL RAW
>[!NOTE]
>OS 디스크 크기를 늘리려면 항상 VM의 스냅숏을 만듭니다.

RAW 파티션이 있는 RHEL에서 OS 디스크의 크기를 늘리려면 다음을 수행 하십시오.

VM을 중지합니다.
포털에서 OS 디스크의 크기를 늘립니다.
VM을 시작합니다.
VM이 다시 시작되면 다음 단계를 수행합니다.

1. 다음 명령을 사용하여 **루트** 사용자로 VM에 액세스합니다.
 
   ```
   sudo su
   ```

1. OS 디스크의 크기를 늘리는 데 필요한 **gptfdisk** 패키지를 설치합니다.

   ```
   yum install gdisk -y
   ```

1.  디스크에서 사용할 수 있는 모든 섹터를 보려면 다음 명령을 실행 합니다.
    ```
    gdisk -l /dev/sda
    ```

1. 파티션 형식에 대 한 자세한 정보가 표시 됩니다. GPT 인지 확인 합니다. 루트 파티션을 식별 합니다. 부팅 파티션 (BIOS 부팅 파티션) 및 시스템 파티션 (' EFI 시스템 파티션 ')을 변경 하거나 삭제 하지 마십시오.

1. 다음 명령을 사용 하 여 처음으로 분할을 시작 합니다. 
    ```
    gdisk /dev/sda
    ```

1. 이제 다음 명령을 요청 하는 메시지가 표시 됩니다 (' Command:? 도움말의 경우). 

   ```
   w
   ```

1. "경고! 보조 헤더가 디스크에 너무 일찍 배치 되었습니다. 이 문제를 해결 하 시겠습니까? (Y/N): " ' Y '를 눌러야 합니다.

   ```
   Y
   ```

1. 최종 검사가 완료 되 고 확인을 요청 하는 메시지가 표시 됩니다. ' Y '를 누릅니다.

   ```
   Y
   ```

1. Partprobe 명령을 사용 하 여 모든 것이 올바르게 발생 했는지 확인 합니다.

   ```
   partprobe
   ```

1. 위의 단계에서 보조 GPT 헤더가 끝에 배치 되었는지 확인 했습니다. 다음 단계는 gdisk 도구를 다시 사용 하 여 크기를 조정 하는 프로세스를 시작 하는 것입니다. 다음 명령을 사용합니다.

   ```
   gdisk /dev/sda
   ```
1. 명령 메뉴에서 ' p '를 눌러 파티션 목록을 표시 합니다. 루트 파티션을 식별 하 고 (단계에서는 sda2가 루트 파티션으로 간주 됨) 부팅 파티션을 식별 합니다 (단계에서는 sda3가 부팅 파티션으로 간주 됨). 

   ```
   p
   ```
    ![루트 파티션 및 부팅 파티션](./media/resize-os-disk-rhelraw/resize-os-disk-rhelraw1.png)

1. ' D '를 눌러 파티션을 삭제 하 고 부팅에 할당 된 파티션 번호 (이 예제에서는 ' 3 ')를 선택 합니다.
   ```
   d
   3
   ```
1. ' D '를 눌러 파티션을 삭제 하 고 부팅에 할당 된 파티션 번호 (이 예제에서는 ' 2 ')를 선택 합니다.
   ```
   d
   2
   ```
    ![루트 파티션 및 부팅 파티션 삭제](./media/resize-os-disk-rhelraw/resize-os-disk-rhelraw2.png)

1. 크기가 증가 된 루트 파티션을 다시 만들려면 ' n '을 누르고 루트에 대해 이전에 삭제 한 파티션 번호 (이 예제의 경우 ' 2 ')를 입력 하 고 첫 번째 섹터를 ' 기본값 '으로, 마지막 섹터를 ' 마지막 섹터 값-부팅 크기 섹터 8300 4096 '로
   ```
   n
   2
   (Enter default)
   (Calculateed value of Last sector value - 4096)
   8300
   ```
1. 부팅 파티션을 다시 만들려면 ' n '을 누르고, 이전에 부팅 하기 위해 삭제 한 파티션 번호 (이 예에서는 ' 3 ')를 입력 하 고 첫 번째 섹터를 ' 기본값 '으로, 마지막 섹터를 ' 기본값 '으로, 16 진수 코드를 ' EF02 '로 선택 합니다.
   ```
   n
   3
   (Enter default)
   (Enter default)
   EF02
   ```

1. ' W ' 명령을 사용 하 여 변경 내용을 작성 하 고 ' Y ' 키를 눌러 확인 합니다.
   ```
   w
   Y
   ```
1. ' Partprobe ' 명령을 실행 하 여 디스크 안정성 확인
   ```
   partprobe
   ```
1. VM을 다시 부팅 하면 루트 파티션 크기가 늘어났습니다.
   ```
   reboot
   ```

   ![새 루트 파티션 및 부팅 파티션](./media/resize-os-disk-rhelraw/resize-os-disk-rhelraw3.png)

1. 파티션에서 xfs_growfs 명령을 실행 하 여 크기를 조정 합니다.
   ```
   xfs_growfs /dev/sda2
   ```

   ![XFS 확장 FS](./media/resize-os-disk-rhelraw/resize-os-disk-rhelraw4.png)

## <a name="next-steps"></a>다음 단계

- [디스크 크기 조정](expand-disks.md)
