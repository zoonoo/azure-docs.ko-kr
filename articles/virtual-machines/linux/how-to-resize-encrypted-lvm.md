---
title: Azure Disk Encryption를 사용 하 여 암호화 된 논리 볼륨 관리 디스크의 크기를 조정 하는 방법
description: 이 문서에서는 논리 볼륨 관리를 사용 하 여 ADE 암호화 된 디스크 크기를 조정 하는 지침을 제공
author: jofrance
ms.service: security
ms.topic: article
ms.author: jofrance
ms.date: 09/21/2020
ms.openlocfilehash: ba652b9424b8d5ce1b6a2c5b7d70b8fd9e999323
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/25/2020
ms.locfileid: "91343966"
---
# <a name="how-to-resize-logical-volume-management-devices-encrypted-with-azure-disk-encryption"></a>Azure Disk Encryption로 암호화 된 논리 볼륨 관리 장치의 크기를 조정 하는 방법

이 문서는 Linux에서 LVM (논리 볼륨 관리)을 사용 하 여 ADE 암호화 된 데이터 디스크의 크기를 조정 하는 방법에 대 한 단계별 프로세스 이며, 여러 시나리오에 적용 됩니다.

프로세스는 다음 환경에 적용 됩니다.

- Linux 배포
    - RHEL 7+
    - Ubuntu 16 이상
    - SUSE 12 이상
- 단일 패스 확장 Azure Disk Encryption
- Azure Disk Encryption 이중 패스 확장

## <a name="considerations"></a>고려 사항

이 문서에서는 다음을 가정 합니다.

1. 기존 LVM 구성이 있습니다.
   
   Linux vm에서 lvm을 구성 하는 방법에 대 한 자세한 내용은 [LINUX vm에서 Lvm 구성](configure-lvm.md) 을 참조 하세요.

2. 디스크가 이미 암호화 되어 Azure Disk Encryption를 사용 하 여 LVM에서 lvm을 구성 하는 방법에 [대 한 정보](how-to-configure-lvm-raid-on-crypt.md) 를 참조 하세요.

3. 이러한 예제를 수행 하는 데 필요한 Linux 및 LVM 전문 기술이 있습니다.

4. [장치 이름 문제 해결](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/troubleshoot-device-names-problems)에 설명 된 대로 Azure에서 데이터 디스크를 사용 하는 권장 사항은/dev/disk/scsi1/경로를 사용 하는 것입니다.

## <a name="scenarios"></a>시나리오

이 문서의 절차는 다음 시나리오에 적용 됩니다.

### <a name="for-traditional-lvm-and-lvm-on-crypt-configurations"></a>기존 LVM 및 LVM-다중 컴퓨터 구성의 경우

- VG에 사용 가능한 공간이 있는 경우 논리 볼륨 확장

### <a name="for-traditional-lvm-encryption-the-logical-volumes-are-encrypted-not-the-whole-disk"></a>기존 LVM 암호화의 경우 (전체 디스크가 아닌 논리 볼륨이 암호화 됨)

- 기존 LVM 볼륨 확장 새 PV 추가
- 기존 LVM 볼륨을 확장 하 여 기존 PV 크기 조정

### <a name="for-lvm-on-crypt-recommended-method-the-entire-disk-is-encrypted-not-only-the-logical-volume"></a>LVM-온-반자 (권장 방법의 경우 논리 볼륨 뿐만 아니라 전체 디스크가 암호화 됨)

- 새 PV를 추가 하는 묘지 볼륨에서 LVM 확장
- 무덤 볼륨의 LVM 확장 기존 PV 크기 조정

> [!NOTE]
> 동일한 VM에서 기존 LVM 암호화 및 LVM 온-도를 혼합 하는 것은 권장 되지 않습니다.

> [!NOTE]
> 이러한 예제에서는 디스크, 실제 볼륨, 볼륨 그룹, 논리 볼륨, 파일 시스템, Uuid 및 탑재 지점이에 대 한 기존 이름을 사용 합니다 .이 예제에서 제공 되는 값을 환경에 맞게 바꾸어야 합니다.

#### <a name="extending-a-logical-volume-when-theres-available-space-in-the-vg"></a>VG에 사용 가능한 공간이 있는 경우 논리 볼륨 확장

논리적 볼륨의 크기를 조정 하는 데 사용 되는 일반적인 방법입니다 .이 방법은 암호화 되지 않은 디스크, 기존의 lvm 암호화 된 볼륨 및 LVM 온-반자 구성에 적용할 수 있습니다.

1. 증가 시킬 파일 시스템의 현재 크기를 확인 합니다.

    ``` bash
    df -h /mountpoint
    ```

    ![scenarioa-fs1](./media/disk-encryption/resize-lvm/001-resize-lvm-scenarioa-check-fs.png)

2. VG에 LV를 늘릴 수 있는 충분 한 공간이 있는지 확인 합니다.

    ``` bash
    vgs
    ```

    ![scenarioa-vg](./media/disk-encryption/resize-lvm/002-resize-lvm-scenarioa-check-vgs.png)

    "Vgdisplay"를 사용할 수도 있습니다.

    ``` bash
    vgdisplay vgname
    ```

    ![scenarioa-vgdisplay](./media/disk-encryption/resize-lvm/002-resize-lvm-scenarioa-check-vgdisplay.png)

3. 크기를 조정 해야 하는 논리 볼륨 식별

    ``` bash
    lsblk
    ```

    ![scenarioa-lsblk1](./media/disk-encryption/resize-lvm/002-resize-lvm-scenarioa-check-lsblk1.png)

    LVM에 대 한 대/소문자 구분의 경우이 출력에 차이가 있습니다 .이 출력은 암호화 된 계층이 전체 디스크를 포함 하는 암호화 된 계층에 있음을 보여 줍니다.

    ![scenarioa-lsblk2](./media/disk-encryption/resize-lvm/002-resize-lvm-scenarioa-check-lsblk2.png)

4. 논리 볼륨 크기 확인

    ``` bash
    lvdisplay lvname
    ```

    ![scenarioa-lvdisplay01](./media/disk-encryption/resize-lvm/002-resize-lvm-scenarioa-check-lvdisplay01.png)

5. 파일 시스템의 온라인 크기 조정에 "-r"을 사용 하 여 LV 크기를 늘립니다.

    ``` bash
    lvextend -r -L +2G /dev/vgname/lvname
    ```

    ![scenarioa-lv](./media/disk-encryption/resize-lvm/003-resize-lvm-scenarioa-resize-lv.png)

6. LV 및 파일 시스템의 새 크기를 확인 합니다.

    ``` bash
    df -h /mountpoint
    ```

    ![scenarioa-fs](./media/disk-encryption/resize-lvm/004-resize-lvm-scenarioa-check-fs.png)

    새 크기가 반영 되 면 LV 및 filesystem의 크기 조정이 성공 했음을 나타냅니다.

7. Lv 정보를 다시 확인 하 여 LV 수준에서 변경 내용을 확인할 수 있습니다.

    ``` bash
    lvdisplay lvname
    ```

    ![scenarioa-lvdisplay2](./media/disk-encryption/resize-lvm/004-resize-lvm-scenarioa-check-lvdisplay2.png)

#### <a name="extending-a-traditional-lvm-volume-adding-a-new-pv"></a>기존 LVM 볼륨 확장 새 PV 추가

볼륨 그룹 크기를 늘리기 위해 새 디스크를 추가 해야 하는 경우에 적용할 수 있습니다.

1. 증가 시킬 파일 시스템의 현재 크기를 확인 합니다.

    ``` bash
    df -h /mountpoint
    ```

    ![scenariob-fs](./media/disk-encryption/resize-lvm/005-resize-lvm-scenariob-check-fs.png)

2. 현재 물리적 볼륨 구성 확인

    ``` bash
    pvs
    ```

    ![scenariob-pvs](./media/disk-encryption/resize-lvm/006-resize-lvm-scenariob-check-pvs.png)

3. 현재 VG 정보를 확인 합니다.

    ``` bash
    vgs
    ```

    ![scenariob-vgs](./media/disk-encryption/resize-lvm/007-resize-lvm-scenariob-check-vgs.png)

4. 현재 디스크 목록 확인

    /Dev/disk/azure/scsi1/아래에 있는 장치를 확인 하 여 데이터 디스크를 식별 해야 합니다.

    ``` bash
    ls -l /dev/disk/azure/scsi1/
    ```

    ![scenariob-scs1](./media/disk-encryption/resize-lvm/008-resize-lvm-scenariob-check-scs1.png)

5. Lsblk의 출력을 확인 합니다. 

    ``` bash
    lsbk
    ```

    ![scenariob-lsblk](./media/disk-encryption/resize-lvm/008-resize-lvm-scenariob-check-lsblk.png)

6. 새 디스크를 VM에 연결 합니다.

    다음 문서의 4 단계까지 진행 합니다.

   - [VM에 디스크 연결](attach-disk-portal.md)

7. 디스크가 연결 되 면 디스크 목록을 확인 하 고 새 디스크를 확인 합니다.

    ``` bash
    ls -l /dev/disk/azure/scsi1/
    ```

    ![scenariob-scsi12](./media/disk-encryption/resize-lvm/009-resize-lvm-scenariob-check-scsi12.png)

    ``` bash
    lsbk
    ```

    ![scenariob-lsblk12](./media/disk-encryption/resize-lvm/009-resize-lvm-scenariob-check-lsblk1.png)

8. 새 데이터 디스크 위에 새 PV를 만듭니다.

    ``` bash
    pvcreate /dev/newdisk
    ```

    ![scenariob-pvcreate](./media/disk-encryption/resize-lvm/010-resize-lvm-scenariob-pvcreate.png)

    이 방법은 파티션이 없는 PV로 전체 디스크를 사용 합니다. 필요에 따라 "fdisk"를 사용 하 여 파티션을 만든 다음 "pvcreate"에 해당 파티션을 사용할 수 있습니다.

9. Pv 목록에 PV가 추가 되었는지 확인 합니다.

    ``` bash
    pvs
    ```

    ![scenariob-pvs1](./media/disk-encryption/resize-lvm/011-resize-lvm-scenariob-check-pvs1.png)

10. 새 PV를 추가 하 여 VG를 확장 합니다.

    ``` bash
    vgextend vgname /dev/newdisk
    ```

    ![scenariob-vg-확장](./media/disk-encryption/resize-lvm/012-resize-lvm-scenariob-vgextend.png)

11. 새 VG 크기 확인

    ``` bash
    vgs
    ```

    ![scenariob-vgs1](./media/disk-encryption/resize-lvm/013-resize-lvm-scenariob-check-vgs1.png)

12. Lsblk를 사용 하 여 크기를 조정 해야 하는 LV를 식별 합니다.

    ``` bash
    lsblk
    ```

    ![scenariob-lsblk1](./media/disk-encryption/resize-lvm/013-resize-lvm-scenariob-check-lsblk1.png)

13. 파일 시스템의 온라인 증가를 수행 하려면 "-r"을 사용 하 여 LV 크기를 확장 합니다.

    ``` bash
    lvextend -r -L +2G /dev/vgname/lvname
    ```

    ![scenariob-lvextend](./media/disk-encryption/resize-lvm/013-resize-lvm-scenariob-lvextend.png) 

14. 새 LV 및 filesystem 크기 확인

    ``` bash
    df -h /mountpoint
    ```

    ![scenariob-fs1](./media/disk-encryption/resize-lvm/014-resize-lvm-scenariob-check-fs1.png)

    기존 LVM 구성에서 ADE를 사용 하는 경우 암호화 된 계층은 디스크 수준이 아닌 LV 수준에서 만들어집니다.

    이때 암호화 된 계층은 새 디스크로 확장 됩니다.
    실제 데이터 디스크는 플랫폼 수준에서 암호화 설정이 없으므로 암호화 상태는 업데이트 되지 않습니다.

    >[!NOTE]
    >이러한 이유 중 하나는 LVM이 권장 되는 방법입니다. 

15. 포털에서 암호화 정보를 확인 합니다.

    ![scenariob-portal1](./media/disk-encryption/resize-lvm/014-resize-lvm-scenariob-check-portal1.png)

    디스크의 암호화 설정을 업데이트 하려면 새 LV를 추가 하 고 VM에서 확장을 사용 하도록 설정 해야 합니다.
    
16. 새 LV를 추가 하 고 파일 시스템을 만든 다음/etc/fstab에 추가 합니다.

17. 플랫폼 수준에서 새 데이터 디스크에 암호화 설정을 스탬프 하려면 암호화 확장을 다시 설정 합니다.

    예:

    CLI

    ``` bash
    az vm encryption enable -g ${RGNAME} --name ${VMNAME} --disk-encryption-keyvault "<your-unique-keyvault-name>"
    ```

18. 포털에서 암호화 정보를 확인 합니다.

    ![scenariob-portal2](./media/disk-encryption/resize-lvm/014-resize-lvm-scenariob-check-portal2.png)

19. 암호화 설정이 업데이트 된 후에는 새 LV를 삭제할 수 있습니다./etc/fstab 및/etc/crypttab에 대해 만들어진 항목을 삭제 해야 합니다.

    ![scenariob-fstab-crypttab](./media/disk-encryption/resize-lvm/014-resize-lvm-scenariob-delete-fstab-crypttab.png)

20. 논리 볼륨을 분리 합니다.

    ``` bash
    umount /mountpoint
    ```

21. 볼륨의 암호화 된 계층을 닫습니다.

    ``` bash
    cryptsetup luksClose /dev/vgname/lvname
    ```

22. LV 삭제

    ``` bash
    lvremove /dev/vgname/lvname
    ```

#### <a name="extending-a-traditional-lvm-volume-resizing-an-existing-pv"></a>기존 LVM 볼륨을 확장 하 여 기존 PV 크기 조정

특정 시나리오 또는 제한 사항에 따라 기존 디스크의 크기를 조정 해야 합니다.

1. 암호화 된 디스크 식별

    ``` bash
    ls -l /dev/disk/azure/scsi1/
    ```

    ![scenarioc-scsi1](./media/disk-encryption/resize-lvm/015-resize-lvm-scenarioc-check-scsi1.png)

    ``` bash
    lsblk -fs
    ```

    ![scenarioc-lsblk](./media/disk-encryption/resize-lvm/015-resize-lvm-scenarioc-check-lsblk.png)

2. Pv 정보를 확인 하십시오.

    ``` bash
    pvs
    ```

    ![scenarioc-pvs](./media/disk-encryption/resize-lvm/016-resize-lvm-scenarioc-check-pvs.png)

    모든 PVs의 모든 공간이 현재 사용 되 고 있습니다.

3. VGs 정보 확인

    ``` bash
    vgs
    vgdisplay -v vgname
    ```

    ![scenarioc-vgs](./media/disk-encryption/resize-lvm/017-resize-lvm-scenarioc-check-vgs.png)

4. 디스크 크기를 확인 합니다. fdisk 또는 lsblk를 사용 하 여 드라이브 크기를 나열할 수 있습니다.

    ``` bash
    for disk in `ls -l /dev/disk/azure/scsi1/* | awk -F/ '{print $NF}'` ; do echo "fdisk -l /dev/${disk} | grep ^Disk "; done | bash

    lsblk -o "NAME,SIZE"
    ```

    ![scenarioc-fdisk](./media/disk-encryption/resize-lvm/018-resize-lvm-scenarioc-check-fdisk.png)

    Lsblk를 사용 하 여 어떤 LVs와 연결 된 PVs를 확인 했습니다. "lvdisplay"를 실행 하 여 식별할 수도 있습니다.

    ``` bash
    lvdisplay --maps VG/LV
    lvdisplay --maps datavg/datalv1
    ```

    ![check-lvdisplay](./media/disk-encryption/resize-lvm/019-resize-lvm-scenarioc-check-lvdisplay.png)

    이 특정 사례에서 4 개의 데이터 드라이브는 모두 동일한 VG 및 단일 LV의 일부 이므로 구성이이 예와 다를 수 있습니다.

5. 현재 파일 시스템 사용률을 확인 합니다.

    ``` bash
    df -h /datalvm*
    ```

    ![scenarioc-df](./media/disk-encryption/resize-lvm/020-resize-lvm-scenarioc-check-df.png)

6. 데이터 디스크 크기 조정:

    [Linux 확장 디스크](expand-disks.md) (디스크 크기 조정만 참조)를 참조할 수 있으며, 포털, CLI 또는 PowerShell을 사용 하 여이 단계를 수행할 수 있습니다.

    >[!NOTE]
    >VM을 실행 하는 가상 디스크에 대 한 크기 조정 작업을 수행할 수 없습니다. 이 단계를 위해 VM의 할당을 취소 해야 합니다.

7. 디스크 크기를 필요한 값으로 조정 하는 경우에는 VM을 시작 하 고 fdisk를 사용 하 여 새 크기를 확인 합니다.

    ``` bash
    for disk in `ls -l /dev/disk/azure/scsi1/* | awk -F/ '{print $NF}'` ; do echo "fdisk -l /dev/${disk} | grep ^Disk "; done | bash

    lsblk -o "NAME,SIZE"
    ```

    ![scenarioc-fdisk1](./media/disk-encryption/resize-lvm/021-resize-lvm-scenarioc-check-fdisk1.png)

    이 경우/sv/sdd는 5G에서 20G로 크기가 조정 되었습니다.

8. 현재 PV 크기를 확인 합니다.

    ``` bash
    pvdisplay /dev/resizeddisk
    ```

    ![scenarioc-pvdisplay](./media/disk-encryption/resize-lvm/022-resize-lvm-scenarioc-check-pvdisplay.png)
    
    디스크 크기가 조정 된 경우에도 pv의 크기는 여전히 이전입니다.

9. PV 크기 조정

    ``` bash
    pvresize /dev/resizeddisk
    ```

    ![scenarioc-pvresize](./media/disk-encryption/resize-lvm/023-resize-lvm-scenarioc-check-pvresize.png)


10. PV 크기 확인

    ``` bash
    pvdisplay /dev/resizeddisk
    ```

    ![scenarioc-pvdisplay1](./media/disk-encryption/resize-lvm/024-resize-lvm-scenarioc-check-pvdisplay1.png)

    크기를 조정 하려는 모든 디스크에 동일한 절차를 적용 합니다.

11. VG 정보 확인

    ``` bash
    vgdisplay vgname
    ```

    ![scenarioc-vgdisplay1](./media/disk-encryption/resize-lvm/025-resize-lvm-scenarioc-check-vgdisplay1.png)

    이제 VG에 LVs에 할당할 공간이 있습니다.

12. LV 크기 조정

    ``` bash
    lvresize -r -L +5G vgname/lvname
    lvresize -r -l +100%FREE /dev/datavg/datalv01
    ```

    ![scenarioc-lvresize1](./media/disk-encryption/resize-lvm/031-resize-lvm-scenarioc-check-lvresize1.png)

13. FS 크기 확인

    ``` bash
    df -h /datalvm2
    ```

    ![scenarioc-df3](./media/disk-encryption/resize-lvm/032-resize-lvm-scenarioc-check-df3.png)

#### <a name="extending-an-lvm-on-crypt-volume-adding-a-new-pv"></a>LVM-다중 컴퓨터 볼륨 확장 새 PV 추가

이 메서드는 새 디스크를 추가 하 고 LVM-다중 컴퓨터 구성에서 구성 하는 단계를 지 수 [에서 LVM 구성](how-to-configure-lvm-raid-on-crypt.md) 의 단계와 긴밀 하 게 따릅니다.

이 메서드를 사용 하 여 이미 존재 하는 LV에 공간을 추가 하거나 대신 새 VGs 또는 LVs를 만들 수 있습니다.

1. VG의 현재 크기를 확인 합니다.

    ``` bash
    vgdisplay vgname
    ```

    ![scenarioe-vg01](./media/disk-encryption/resize-lvm/033-resize-lvm-scenarioe-check-vg01.png)

2. 증가 시킬 fs 및 lv의 크기를 확인 합니다.

    ``` bash
    lvdisplay /dev/vgname/lvname
    ```

    ![scenarioe-lv01](./media/disk-encryption/resize-lvm/034-resize-lvm-scenarioe-check-lv01.png)

    ``` bash
    df -h mountpoint
    ```

    ![scenarioe-fs01](./media/disk-encryption/resize-lvm/034-resize-lvm-scenarioe-check-fs01.png)

3. VM에 새 데이터 디스크를 추가 하 고 식별 합니다.

    디스크를 추가 하기 전에 디스크를 확인 합니다.

    ``` bash
    fdisk -l | egrep ^"Disk /"
    ```

    ![scenarioe-newdisk01](./media/disk-encryption/resize-lvm/035-resize-lvm-scenarioe-check-newdisk01.png)

    새 디스크를 추가 하기 전에 디스크를 확인 합니다.

    ``` bash
    lsblk
    ```

    ![scenarioe-newdisk002](./media/disk-encryption/resize-lvm/035-resize-lvm-scenarioe-check-newdisk02.png)

    PowerShell, Azure CLI 또는 Azure Portal를 사용 하 여 새 디스크를 추가 합니다. VM에 디스크를 추가 하는 방법에 대 한 참조는 [디스크를 연결](attach-disk-portal.md) 하는 방법을 참조 하세요.

    장치의 커널 이름 스키마를 따라 새 드라이브에는 일반적으로 다음 사용 가능한 문자가 할당 됩니다 .이 특정 경우에 추가 된 새 디스크는 sdd입니다.

4. 새 디스크를 추가한 후 디스크를 확인 합니다.

    ``` bash
    fdisk -l | egrep ^"Disk /"
    ```

    ![scenarioe-newdisk02](./media/disk-encryption/resize-lvm/036-resize-lvm-scenarioe-check-newdisk02.png)-

    ``` bash
    lsblk
    ```

    ![scenarioe-newdisk003](./media/disk-encryption/resize-lvm/036-resize-lvm-scenarioe-check-newdisk03.png)

5. 최근 추가 된 디스크 위에 파일 시스템 만들기

    최근 추가 된 디스크를/dev/disk/azure/scsi1/의 연결 된 장치에 일치

    ``` bash
    ls -la /dev/disk/azure/scsi1/
    ```

    ![scenarioe-newdisk03](./media/disk-encryption/resize-lvm/037-resize-lvm-scenarioe-check-newdisk03.png)

    ``` bash
    mkfs.ext4 /dev/disk/azure/scsi1/${disk}
    ```

    ![scenarioe-mkfs01](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-mkfs01.png)

6. 새로 추가 된 디스크에 대 한 새 임시 탑재 지점을 만듭니다.

    ``` bash
    newmount=/data4
    mkdir ${newmount}
    ```

7. /Etc/fstab에 최근에 만든 파일 시스템 추가

    ``` bash
    blkid /dev/disk/azure/scsi1/lun4| awk -F\" '{print "UUID="$2" '${newmount}' "$4" defaults,nofail 0 0"}' >> /etc/fstab
    ```

8. 탑재-a를 사용 하 여 만든 새 fs를 탑재 합니다.

    ``` bash
    mount -a
    ```

9. 추가 된 새 FS가 탑재 되었는지 확인

    ``` bash
    df -h
    ```

    ![scenarioe-df](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-df.png)

    ``` bash
    lsblk
    ```

    ![scenarioe-lsblk](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-lsblk.png)

10. 데이터 드라이브에 대해 이전에 시작 된 암호화를 다시 시작 합니다.

    LVM-다중 컴퓨터의 경우 EncryptFormatAll를 사용 하는 것이 좋습니다. 그렇지 않으면 추가 디스크를 설정 하는 동안 이중 암호화가 발생할 수 있습니다.

    사용에 대 한 자세한 내용은 컴퓨터를 사용 하 여 [LVM 구성](how-to-configure-lvm-raid-on-crypt.md)을 참조 하세요.

    예:

    ``` bash
    az vm encryption enable \
    --resource-group ${RGNAME} \
    --name ${VMNAME} \
    --disk-encryption-keyvault ${KEYVAULTNAME} \
    --key-encryption-key ${KEYNAME} \
    --key-encryption-keyvault ${KEYVAULTNAME} \
    --volume-type "DATA" \
    --encrypt-format-all \
    -o table
    ```

    암호화가 완료 되 면 새로 추가 된 디스크에 대 한에 지 수 계층이 표시 됩니다.

    ``` bash
    lsblk
    ```

    ![scenarioe-lsblk2](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-lsblk2.png)

11. 새 디스크의 암호화 된 계층을 분리 합니다.

    ``` bash
    umount ${newmount}
    ```

12. 현재 pvs 정보 확인

    ``` bash
    pvs
    ```

    ![scenarioe-currentpvs](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-currentpvs.png)

13. 디스크의 암호화 된 계층 위에 PV를 만듭니다.

    이전 lsblk 명령에서 장치 이름을 잡고 장치 이름 앞에/cv/perma를 추가 하 여 pv를 만듭니다.

    ``` bash
    pvcreate /dev/mapper/mapperdevicename
    ```

    ![scenarioe-pvcreate](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-pvcreate.png)

    현재 ext4 fs 서명을 초기화 하는 방법에 대 한 경고가 표시 됩니다 .이 문제는이 질문에 대 한 답입니다.

14. Lvm 구성에 새 PV가 추가 되었는지 확인 합니다.

    ``` bash
    pvs
    ```

    ![scenarioe-newpv](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-newpv.png)

15. 증가 해야 하는 VG에 새 PV를 추가 합니다.

    ``` bash
    vgextend vgname /dev/mapper/nameofhenewpv
    ```

    ![scenarioe-vgextent](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-vgextent.png)

16. VG의 새 크기 및 사용 가능한 공간을 확인 합니다.

    ``` bash
    vgdisplay vgname
    ```

    ![scenarioe-vgdisplay](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-vgdisplay.png)

    총 PE 수와 사용 가능한 PE/크기가 증가 합니다.

17. Lvextend에서-r 옵션을 사용 하 여 lv 및 파일 시스템의 크기를 늘립니다 (이 예제에서는 VG에서 사용 가능한 총 공간을 차지 하 고 지정 된 논리 볼륨에 추가).

    ``` bash
    lvextend -r -l +100%FREE /dev/vgname/lvname
    ```

    ![scenarioe-lvextend](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-lvextend.png)

18. LV의 크기를 확인 합니다.

    ``` bash
    lvdisplay /dev/vgname/lvname
    ```

    ![scenarioe-lvdisplay](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-lvdisplay.png)

19. 방금 크기를 조정한 파일 시스템의 크기를 확인 합니다.

    ``` bash
    df -h mountpoint
    ```

    ![scenarioe-df1](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-df1.png)

20. LVM 계층이 암호화 된 계층의 맨 위에 만들어졌는지 확인 합니다.

    ``` bash
    lsblk
    ```

    ![scenarioe-lsblk3](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-lsblk3.png)

    옵션을 사용 하지 않고 lsblk를 사용 하는 경우 장치 및 논리 볼륨을 기준으로 정렬 하기 때문에 탑재 지점이 여러 번 표시 됩니다. lsblk를 사용 하는 경우-s는 탑재 지점이를 한 번 표시 하기 위해 정렬을 반대로 합니다. 그러면 디스크가 여러 번 표시 됩니다.

    ``` bash
    lsblk -fs
    ```

    ![scenarioe-lsblk4](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-lsblk4.png)

#### <a name="extending-an-lvm-on-crypt-volume-resizing-an-existing-pv"></a>무덤 볼륨의 LVM 확장 기존 PV 크기 조정

1. 암호화 된 디스크 식별

    ``` bash
    lsblk
    ```

    ![scenariof-lsblk01](./media/disk-encryption/resize-lvm/039-resize-lvm-scenariof-lsblk01.png)

    ``` bash
    lsblk -s
    ```

    ![scenariof-lsblk012](./media/disk-encryption/resize-lvm/040-resize-lvm-scenariof-lsblk012.png)

2. Pv 정보 확인

    ``` bash
    pvs
    ```

    ![scenariof-pvs1](./media/disk-encryption/resize-lvm/041-resize-lvm-scenariof-pvs.png)

3. Vg 정보 확인

    ``` bash
    vgs
    ```

    ![scenariof-vgs](./media/disk-encryption/resize-lvm/042-resize-lvm-scenariof-vgs.png)

4. Lv 정보 확인

    ``` bash
    lvs
    ```

    ![scenariof-lvs](./media/disk-encryption/resize-lvm/043-resize-lvm-scenariof-lvs.png)

5. 파일 시스템 사용률 확인

    ``` bash
    df -h /mountpoint(s)
    ```

    ![lvm-scenariof-fs](./media/disk-encryption/resize-lvm/044-resize-lvm-scenariof-fs.png)

6. 디스크 크기 확인

    ``` bash
    fdisk
    fdisk -l | egrep ^"Disk /"
    lsblk
    ```

    ![scenariof-fdisk01](./media/disk-encryption/resize-lvm/045-resize-lvm-scenariof-fdisk01.png)

7. 데이터 디스크 크기 조정

    [Linux 확장 디스크](expand-disks.md) (디스크 크기 조정만 참조)를 참조할 수 있으며, 포털, CLI 또는 PowerShell을 사용 하 여이 단계를 수행할 수 있습니다.

    >[!NOTE]
    >VM을 실행 하는 가상 디스크에 대 한 크기 조정 작업을 수행할 수 없습니다. 이 단계를 위해 VM의 할당을 취소 해야 합니다.

8. 디스크 크기 확인

    ``` bash
    fdisk
    fdisk -l | egrep ^"Disk /"
    lsblk
    ```

    ![scenariof-fdisk02](./media/disk-encryption/resize-lvm/046-resize-lvm-scenariof-fdisk02.png)

    (이 경우) 두 디스크의 크기가 2GB에서 4GB로 조정 되었지만 FS, LV 및 PV 크기는 동일 하 게 유지 됩니다.

9. 현재 pv 크기를 확인 합니다.

    LVM에 대 한 비-자리에 있는 경우 pv는/dev/mapper/장치 이며/sv/sd * 장치가 아닙니다.

    ``` bash
    pvdisplay /dev/mapper/devicemappername
    ```

    ![scenariof-pvs](./media/disk-encryption/resize-lvm/047-resize-lvm-scenariof-pvs.png)

10. Pv 크기 조정

    ``` bash
    pvresize /dev/mapper/devicemappername
    ```

    ![scenariof-pv](./media/disk-encryption/resize-lvm/048-resize-lvm-scenariof-resize-pv.png)

11. 크기 조정 후 pv 크기 확인

    ``` bash
    pvdisplay /dev/mapper/devicemappername
    ```

    ![scenariof-pv](./media/disk-encryption/resize-lvm/049-resize-lvm-scenariof-pv.png)

12. PV의 암호화 된 계층 크기 조정

    ``` bash
    cryptsetup resize /dev/mapper/devicemappername
    ```

    크기를 조정 하려는 모든 디스크에 동일한 절차를 적용 합니다.

13. Vg 정보 확인

    ``` bash
    vgdisplay vgname
    ```

    ![scenariof-vg](./media/disk-encryption/resize-lvm/050-resize-lvm-scenariof-vg.png)

    이제 VG에 LVs에 할당할 공간이 있습니다.

14. Lv 정보 확인

    ``` bash
    lvdisplay vgname/lvname
    ```

    ![scenariof-lv](./media/disk-encryption/resize-lvm/051-resize-lvm-scenariof-lv.png)

15. Fs 사용률 확인

    ``` bash
    df -h /mountpoint
    ```

    ![scenariof-fs](./media/disk-encryption/resize-lvm/052-resize-lvm-scenariof-fs.png)

16. Lv 크기 조정

    ``` bash
    lvresize -r -L +2G /dev/vgname/lvname
    ```

    ![scenariof-lvresize](./media/disk-encryption/resize-lvm/053-resize-lvm-scenariof-lvresize.png)

    -R 옵션을 사용 하 여 FS 크기 조정을 수행 합니다.

17. Lv 정보 확인

    ``` bash
    lvdisplay vgname/lvname
    ```

    ![scenariof-lvsize](./media/disk-encryption/resize-lvm/054-resize-lvm-scenariof-lvsize.png)

18. 파일 시스템 사용률 확인

    ``` bash
    df -h /mountpoint
    ```

    ![파일 시스템 만들기](./media/disk-encryption/resize-lvm/055-resize-lvm-scenariof-fs.png)

    이를 필요로 하는 모든 추가 lv에 동일한 크기 조정 절차를 적용 합니다.

## <a name="next-steps"></a>다음 단계

- [Azure Disk Encryption 문제 해결](disk-encryption-troubleshooting.md)
