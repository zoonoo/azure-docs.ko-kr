---
title: Linux VM에 데이터 디스크 연결 | Microsoft Docs
description: 포털을 사용하여 새 또는 기존 데이터 디스크를 Linux VM에 연결합니다.
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 5e1c6212-976c-4962-a297-177942f90907
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 07/12/2018
ms.author: cynthn
ms.subservice: disks
ms.openlocfilehash: 2c1b46f1c1726a473fe15e490f3000f3c5235a77
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/31/2019
ms.locfileid: "55477508"
---
# <a name="use-the-portal-to-attach-a-data-disk-to-a-linux-vm"></a>포털을 사용하여 데이터 디스크를 Linux VM에 연결 
이 문서에서는 Azure 포털을 통해 신규 및 기존 디스크를 Linux 가상 머신에 연결하는 방법을 보여줍니다. 또한 [Azure Portal에서 Windows VM에 데이터 디스크를 연결](../windows/attach-managed-disk-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)할 수도 있습니다. 

VM에 디스크를 연결하기 전에 다음 팁을 검토합니다.

* 가상 머신의 크기로 연결할 수 있는 디스크 개수가 제어됩니다. 자세한 내용은 [가상 머신의 크기](sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)를 참조하세요.
* Premium Storage를 사용하려면 DS 시리즈 또는 GS 시리즈 가상 머신이 필요합니다. 이러한 가상 머신과 함께 프리미엄 및 표준 디스크를 모두 사용할 수 있습니다. Premium Storage는 특정 지역에서만 사용할 수 있습니다. 자세한 내용은 [Premium Storage: Azure Virtual Machine 워크로드를 위한 고성능 스토리지](../windows/premium-storage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) 문서를 참조하세요.
* 가상 머신에 연결된 디스크는 실제로 Azure에 저장된 .vhd 파일입니다. 자세한 내용은 [가상 머신용 디스크 및 VHD 정보](about-disks-and-vhds.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)를 참조하세요.
* 디스크를 연결한 후 [Linux VM에 연결하여 새 디스크를 탑재](#connect-to-the-linux-vm-to-mount-the-new-disk)해야 합니다.


## <a name="find-the-virtual-machine"></a>가상 머신 찾기
1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.
2. 왼쪽 메뉴에서 **Virtual Machines**를 클릭합니다.
3. 목록에서 가상 머신을 선택합니다.
4. 가상 머신 페이지의 **Essentials**에서 **디스크**를 클릭합니다.
   
    ![디스크 설정 열기](./media/attach-disk-portal/find-disk-settings.png)


## <a name="attach-a-new-disk"></a>새 디스크 연결

1. **디스크** 창에서 **+ 데이터 디스크 추가**를 클릭합니다.
2. **이름**에 대한 드롭다운 메뉴를 클릭하고 **디스크 만들기**를 선택합니다.

    ![Azure Managed Disks 만들기](./media/attach-disk-portal/create-new-md.png)

3. 관리되는 디스크에 대한 이름을 입력합니다. 기본 설정을 검토하고 필요에 따라 업데이트한 다음 **만들기**를 클릭합니다.
   
   ![디스크 설정 검토](./media/attach-disk-portal/create-new-md-settings.png)

4. **저장**을 클릭하여 관리되는 디스크를 만들고 VM 구성을 업데이트합니다.

   ![새 Azure Managed Disk 저장](./media/attach-disk-portal/confirm-create-new-md.png)

5. Azure가 디스크를 만들고 가상 머신에 연결하면 가상 머신의 디스크 설정의 **데이터 디스크**아래에 새 디스크가 나열됩니다. 관리되는 디스크는 최상위 수준 리소스이므로 디스크는 리소스 그룹의 루트에 나타납니다.

   ![리소스 그룹의 Azure Managed Disk](./media/attach-disk-portal/view-md-resource-group.png)

## <a name="attach-an-existing-disk"></a>기존 디스크 연결
1. **디스크** 창에서 **+ 데이터 디스크 추가**를 클릭합니다.
2. **이름**에 대한 드롭다운 메뉴를 클릭하여 Azure 구독에 액세스할 수 있는 기존 관리되는 디스크의 목록을 봅니다. 연결할 관리되는 디스크를 선택합니다.

   ![기존 Azure Managed Disk 연결](./media/attach-disk-portal/select-existing-md.png)

3. **저장**을 클릭하여 기존 관리되는 디스크를 연결하고 VM 구성을 업데이트합니다.
   
   ![Azure Managed Disk 업데이트 저장](./media/attach-disk-portal/confirm-attach-existing-md.png)

4. Azure가 디스크를 가상 머신에 연결한 후 가상 머신의 디스크 설정의 **데이터 디스크**아래에 해당 디스크가 나열됩니다.

## <a name="connect-to-the-linux-vm-to-mount-the-new-disk"></a>Linux VM에 연결하여 새 디스크 탑재
Linux VM에서 사용할 수 있도록 새 디스크를 분할, 포맷 및 탑재하려면 VM에 SSH합니다. 자세한 내용은 [Azure에서 Linux와 함께 SSH를 사용하는 방법](mac-create-ssh-keys.md)을 참조하세요. 다음 예제에서는 사용자 이름 *azureuser*를 사용하여 공용 DNS 항목이 *mypublicdns.westus.cloudapp.azure.com*인 VM에 연결합니다. 

```bash
ssh azureuser@mypublicdns.westus.cloudapp.azure.com
```

VM에 연결하고 나면 디스크를 연결할 준비가 되었습니다. 먼저 `dmesg`를 사용하여 디스크를 찾습니다(새 디스크를 찾는 데 사용하는 방법은 다를 수 있습니다). 다음 예제에서는 dmesg를 사용하여 *SCSI* 디스크를 필터링합니다.

```bash
dmesg | grep SCSI
```

다음 예제와 유사하게 출력됩니다.

```bash
[    0.294784] SCSI subsystem initialized
[    0.573458] Block layer SCSI generic (bsg) driver version 0.4 loaded (major 252)
[    7.110271] sd 2:0:0:0: [sda] Attached SCSI disk
[    8.079653] sd 3:0:1:0: [sdb] Attached SCSI disk
[ 1828.162306] sd 5:0:0:0: [sdc] Attached SCSI disk
```

여기서 *sdc*는 원하는 디스크입니다. 

### <a name="partition-a-new-disk"></a>새 디스크 분할
데이터가 포함된 기존 디스크를 사용 중이라면 디스크 탑재 단계로 건너뜁니다. 새 디스크를 연결하는 경우에는 디스크를 분할해야 합니다.

`fdisk`를 사용하여 디스크를 분할하고 파티션 1의 주 디스크로 지정한 다음 기타 기본값은 그대로 적용합니다. 다음 예제에서는 */dev/sdc*에서 `fdisk` 프로세스를 시작합니다.

```bash
sudo fdisk /dev/sdc
```

새 파티션을 추가하려면 `n` 명령을 사용합니다. 이 예제에서는 주 파티션에 대해 `p`를 선택하고 기본 값의 나머지를 적용합니다. 다음 예제와 유사하게 출력됩니다.

```bash
Device contains neither a valid DOS partition table, nor Sun, SGI or OSF disklabel
Building a new DOS disklabel with disk identifier 0x2a59b123.
Changes will remain in memory only, until you decide to write them.
After that, of course, the previous content won't be recoverable.

Warning: invalid flag 0x0000 of partition table 4 will be corrected by w(rite)

Command (m for help): n
Partition type:
   p   primary (0 primary, 0 extended, 4 free)
   e   extended
Select (default p): p
Partition number (1-4, default 1): 1
First sector (2048-10485759, default 2048):
Using default value 2048
Last sector, +sectors or +size{K,M,G} (2048-10485759, default 10485759):
Using default value 10485759
```

`p`를 입력하여 파티션 테이블을 인쇄한 다음, `w`를 사용하여 디스크에 테이블을 쓰고 종료합니다. 출력은 다음 예제와 비슷해야 합니다.

```bash
Command (m for help): p

Disk /dev/sdc: 5368 MB, 5368709120 bytes
255 heads, 63 sectors/track, 652 cylinders, total 10485760 sectors
Units = sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disk identifier: 0x2a59b123

   Device Boot      Start         End      Blocks   Id  System
/dev/sdc1            2048    10485759     5241856   83  Linux

Command (m for help): w
The partition table has been altered!

Calling ioctl() to re-read partition table.
Syncing disks.
```

이제 `mkfs` 명령을 사용하여 파티션에 파일 시스템을 씁니다. 파일 시스템 형식 및 디바이스 이름을 지정합니다. 다음 예제에서는 이전 단계에서 만든 */dev/sdc1* 파티션에 *ext4* 파일 시스템을 만듭니다.

```bash
sudo mkfs -t ext4 /dev/sdc1
```

다음 예제와 유사하게 출력됩니다.

```bash
mke2fs 1.42.9 (4-Feb-2014)
Discarding device blocks: done
Filesystem label=
OS type: Linux
Block size=4096 (log=2)
Fragment size=4096 (log=2)
Stride=0 blocks, Stripe width=0 blocks
327680 inodes, 1310464 blocks
65523 blocks (5.00%) reserved for the super user
First data block=0
Maximum filesystem blocks=1342177280
40 block groups
32768 blocks per group, 32768 fragments per group
8192 inodes per group
Superblock backups stored on blocks:
    32768, 98304, 163840, 229376, 294912, 819200, 884736
Allocating group tables: done
Writing inode tables: done
Creating journal (32768 blocks): done
Writing superblocks and filesystem accounting information: done
```
### <a name="mount-the-disk"></a>디스크 탑재
`mkdir`을 사용하여 파일 시스템을 탑재할 디렉터리를 만듭니다. 다음 예제에서는 */datadrive*에 디렉터리를 만듭니다.

```bash
sudo mkdir /datadrive
```

`mount`를 사용하여 파일 시스템을 탑재합니다. 다음 예제에서는 */dev/sdc1* 파티션을 */datadrive* 탑재 지점에 탑재합니다.

```bash
sudo mount /dev/sdc1 /datadrive
```

다시 부팅 후 드라이브가 자동으로 다시 탑재되도록 하려면 */etc/fstab* 파일에 추가해야 합니다. 또한 */etc/fstab*에 UUID(Universally Unique IDentifier)를 사용하여 디바이스 이름(예: */dev/sdc1*) 대신 드라이브를 가리키는 것이 좋습니다. 부팅하는 동안 OS에서 디스크 오류를 검색하는 경우 UUID를 사용하여 지정된 위치에 탑재되어 있는 잘못된 디스크를 회피합니다. 그런 다음, 남아 있는 데이터 디스크를 동일한 디바이스 ID에 할당합니다. 새 드라이브의 UUID를 찾으려면 `blkid` 유틸리티를 사용합니다.

```bash
sudo -i blkid
```

출력은 다음 예제와 유사합니다.

```bash
/dev/sda1: UUID="11111111-1b1b-1c1c-1d1d-1e1e1e1e1e1e" TYPE="ext4"
/dev/sdb1: UUID="22222222-2b2b-2c2c-2d2d-2e2e2e2e2e2e" TYPE="ext4"
/dev/sdc1: UUID="33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e" TYPE="ext4"
```

> [!NOTE]
> **/etc/fstab** 파일을 부적절하게 편집하면 부팅할 수 없는 시스템이 발생할 수 있습니다. 확실하지 않은 경우 배포 설명서에서 이 파일을 제대로 편집하는 방법에 대한 자세한 내용을 확인하세요. 또한 편집하기 전에 /etc/fstab 파일의 백업을 만드는 것이 좋습니다.

텍스트 편집기에서 다음과 같이 */etc/fstab* 파일을 엽니다.

```bash
sudo vi /etc/fstab
```

이 예제에서는 이전 단계에서 만든 */dev/sdc1* 디바이스의 UUID 값과 탑재 지점 */datadrive*를 사용합니다. */etc/fstab* 파일의 끝에 다음 줄을 추가합니다.

```bash
UUID=33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive   ext4   defaults,nofail   1   2
```

> [!NOTE]
> 나중에 fstab을 편집하지 않고 데이터 디스크를 제거하면 VM이 부팅되지 않을 수 있습니다. 대부분의 배포는 *nofail* 및/또는 *nobootwait* fstab 옵션을 제공합니다. 이러한 옵션을 사용하면 디스크가 부팅 시 탑재되지 않더라도 시스템을 부팅할 수 있습니다. 이러한 매개 변수에 대한 자세한 내용은 배포 설명서를 참조하세요.
> 
> *nofail* 옵션은 파일 시스템이 손상되었거나 디스크가 부팅 시 존재하지 않더라도 VM이 시작되도록 합니다. 이 옵션이 없으면 [FSTAB 오류로 인해 Linux에 SSH를 사용할 수 없음](https://blogs.msdn.microsoft.com/linuxonazure/2016/07/21/cannot-ssh-to-linux-vm-after-adding-data-disk-to-etcfstab-and-rebooting/)(영문)에 설명되어 있는 동작이 발생할 수 있습니다.

### <a name="trimunmap-support-for-linux-in-azure"></a>Azure에서 Linux에 대한 TRIM/UNMAP 지원
일부 Linux 커널은 디스크에서 사용되지 않은 블록을 버릴 수 있도록 TRIM/UNMAP 작업을 지원합니다. 이 기능은 삭제된 페이지가 더 이상 유효하지 않으며 폐기될 수 있음을 Azure에 알리기 위해 표준 저장소에서 주로 유용하며, 큰 파일을 만들고 삭제하는 경우 비용을 절약할 수 있습니다.

Linux VM에서 TRIM 지원을 사용하는 두 가지 방법이 있습니다. 평소와 같이 권장되는 방법에 대해 배포에 확인하세요.

* */etc/fstab*에 `discard` 탑재 옵션을 사용합니다. 예:

    ```bash
    UUID=33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive   ext4   defaults,discard   1   2
    ```
* 경우에 따라 `discard` 옵션을 사용하면 성능이 저하될 수 있습니다. 또는 `fstrim` 명령을 명령줄에서 수동으로 실행하거나, 또는 정기적으로 실행하기 위해 crontab에 추가할 수 있습니다.
  
    **Ubuntu**
  
    ```bash
    sudo apt-get install util-linux
    sudo fstrim /datadrive
    ```
  
    **RHEL/CentOS**

    ```bash
    sudo yum install util-linux
    sudo fstrim /datadrive
    ```

## <a name="next-steps"></a>다음 단계
[Azure CLI를 사용해서도 데이터 디스크를 연결](add-disk.md)할 수 있습니다.
