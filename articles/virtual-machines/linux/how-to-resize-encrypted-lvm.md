---
title: Azure Disk Encryption를 사용 하 여 암호화 된 논리 볼륨 관리 디스크의 크기를 조정 하는 방법
description: 이 문서에서는 논리 볼륨 관리를 사용 하 여 ADE 암호화 된 디스크 크기를 조정 하는 지침을 제공 합니다.
author: jofrance
ms.service: security
ms.topic: article
ms.author: jofrance
ms.date: 09/21/2020
ms.openlocfilehash: 3a3e9b7406e11261aff12d77d9fbeed5debbe938
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91744273"
---
# <a name="how-to-resize-logical-volume-management-devices-that-use-azure-disk-encryption"></a>Azure Disk Encryption를 사용 하는 논리 볼륨 관리 장치의 크기를 조정 하는 방법

이 문서에서는 Azure Disk Encryption를 사용 하는 데이터 디스크의 크기를 조정 하는 방법을 알아봅니다. 디스크 크기를 조정 하려면 Linux에서 LVM (논리 볼륨 관리)을 사용 합니다. 이러한 단계는 여러 시나리오에 적용 됩니다.

다음 환경에서이 크기 조정 프로세스를 사용할 수 있습니다.

- Linux 배포판:
    - Red Hat Enterprise Linux (RHEL) 7 이상
    - Ubuntu 16 이상
    - SUSE 12 이상
- Azure Disk Encryption 버전: 
    - 단일 패스 확장
    - 이중 패스 확장

## <a name="prerequisites"></a>필수 구성 요소

이 문서에서는 사용자가 다음 작업을 수행한 것으로 가정합니다.

- 기존 LVM 구성. 자세한 내용은 [LINUX VM에서 Lvm 구성](configure-lvm.md)을 참조 하세요.

- Azure Disk Encryption으로 이미 암호화 된 디스크입니다. 자세한 내용은 [암호화 된 장치에서 LVM 및 RAID 구성](how-to-configure-lvm-raid-on-crypt.md)을 참조 하세요.

- Linux 및 LVM을 사용 하는 환경.

- Azure의 데이터 디스크에 대해 */dev/disk/scsi1/* 경로를 사용 하는 환경. 자세한 내용은 [LINUX VM 장치 이름 문제 해결](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/troubleshoot-device-names-problems)을 참조 하세요. 

## <a name="scenarios"></a>시나리오

이 문서의 절차는 다음 시나리오에 적용 됩니다.

- 기존 LVM 및 LVM-다중 컴퓨터 구성
- 기존 LVM 암호화 
- LVM-대만 

### <a name="traditional-lvm-and-lvm-on-crypt-configurations"></a>기존 LVM 및 LVM-다중 컴퓨터 구성

기존 LVM 및 LVM-온-VG 구성은 볼륨 그룹 ()에 사용 가능한 공간이 있는 경우 논리 볼륨 (LV)을 확장 합니다.

### <a name="traditional-lvm-encryption"></a>기존 LVM 암호화 

기존 LVM 암호화에서 Lvm는 암호화 됩니다. 전체 디스크가 암호화 되지 않습니다.

기존 LVM 암호화를 사용 하 여 다음을 수행할 수 있습니다.

- 새 실제 볼륨 (PV)을 추가할 때 LV를 확장 합니다.
- 기존 PV의 크기를 조정할 때 LV를 확장 합니다.

### <a name="lvm-on-crypt"></a>LVM-대만 

디스크 암호화에 권장 되는 방법은 LVM을 암호화 하는 것입니다. 이 메서드는 LV 뿐만 아니라 전체 디스크를 암호화 합니다.

LVM-사용자를 사용 하 여 다음을 수행할 수 있습니다. 

- 새 PV를 추가할 때 LV를 확장 합니다.
- 기존 PV의 크기를 조정할 때 LV를 확장 합니다.

> [!NOTE]
> 동일한 VM에서 기존 LVM 암호화 및 LVM 온-컴퓨터를 혼합 하는 것은 권장 되지 않습니다.

다음 섹션에서는 LVM 및 LVM을 사용 하는 방법에 대 한 예제를 제공 합니다. 이 예에서는 디스크, PVs, VGs, LVs, 파일 시스템, Uuid (범용 고유 식별자) 및 탑재 지점의 기존 값을 사용 합니다. 이러한 값을 사용자 환경에 맞게 사용자 고유의 값으로 바꿉니다.

#### <a name="extend-an-lv-when-the-vg-has-available-space"></a>VG에 사용 가능한 공간이 있는 경우 LV 확장

LVs의 크기를 조정 하는 일반적인 방법은 VG에 사용 가능한 공간이 있는 경우 LV를 확장 하는 것입니다. 암호화 되지 않은 디스크, 기존 LVM-암호화 된 볼륨 및 LVM-다중 사용자 구성에이 방법을 사용할 수 있습니다.

1. 증가 시킬 파일 시스템의 현재 크기를 확인 합니다.

    ``` bash
    df -h /mountpoint
    ```

    ![파일 시스템의 크기를 확인 하는 코드를 보여 주는 스크린샷 명령 및 결과가 강조 표시 됩니다.](./media/disk-encryption/resize-lvm/001-resize-lvm-scenarioa-check-fs.png)

2. VG에 LV를 늘릴 수 있는 충분 한 공간이 있는지 확인 합니다.

    ``` bash
    vgs
    ```

    ![VG 공간을 확인 하는 코드를 보여 주는 스크린샷 명령 및 결과가 강조 표시 됩니다.](./media/disk-encryption/resize-lvm/002-resize-lvm-scenarioa-check-vgs.png)

    다음을 사용할 수도 있습니다 `vgdisplay` .

    ``` bash
    vgdisplay vgname
    ```

    ![VG 공간을 확인 하는 V G 표시 코드를 보여 주는 스크린샷 명령 및 결과가 강조 표시 됩니다.](./media/disk-encryption/resize-lvm/002-resize-lvm-scenarioa-check-vgdisplay.png)

3. 크기를 조정 해야 하는 LV를 식별 합니다.

    ``` bash
    lsblk
    ```

    ![L s b l k 명령의 결과를 보여 주는 스크린샷 명령 및 결과가 강조 표시 됩니다.](./media/disk-encryption/resize-lvm/002-resize-lvm-scenarioa-check-lsblk1.png)

    LVM에 대 한 대/소문자 구분의 경우이 출력은 암호화 된 계층이 디스크 수준에 있음을 보여 줍니다.

    ![L s b l k 명령의 결과를 보여 주는 스크린샷 출력이 강조 표시 됩니다. 암호화 된 계층을 표시 합니다.](./media/disk-encryption/resize-lvm/002-resize-lvm-scenarioa-check-lsblk2.png)

4. LV 크기를 확인 합니다.

    ``` bash
    lvdisplay lvname
    ```

    ![논리 볼륨 크기를 확인 하는 코드를 보여 주는 스크린샷 명령 및 결과가 강조 표시 됩니다.](./media/disk-encryption/resize-lvm/002-resize-lvm-scenarioa-check-lvdisplay01.png)

5. 를 사용 하 여 `-r` 파일 시스템을 온라인으로 크기를 조정 하 여 LV 크기를 늘립니다.

    ``` bash
    lvextend -r -L +2G /dev/vgname/lvname
    ```

    ![논리 볼륨의 크기를 늘리는 코드를 보여 주는 스크린샷 명령 및 결과가 강조 표시 됩니다.](./media/disk-encryption/resize-lvm/003-resize-lvm-scenarioa-resize-lv.png)

6. LV 및 파일 시스템에 대 한 새 크기를 확인 합니다.

    ``` bash
    df -h /mountpoint
    ```

    ![LV 및 파일 시스템의 크기를 확인 하는 코드를 보여 주는 스크린샷 명령 및 결과가 강조 표시 됩니다.](./media/disk-encryption/resize-lvm/004-resize-lvm-scenarioa-check-fs.png)

    크기 출력은 LV 및 파일 시스템의 크기를 성공적으로 조정 했음을 나타냅니다.

Lv 정보를 다시 확인 하 여 LV 수준에서 변경 내용을 확인할 수 있습니다.

``` bash
lvdisplay lvname
```

![새 크기를 확인 하는 코드를 보여 주는 스크린샷 크기가 강조 표시 됩니다.](./media/disk-encryption/resize-lvm/004-resize-lvm-scenarioa-check-lvdisplay2.png)

#### <a name="extend-a-traditional-lvm-volume-by-adding-a-new-pv"></a>새 PV를 추가 하 여 기존 LVM 볼륨 확장

VG 크기를 늘리기 위해 새 디스크를 추가 해야 하는 경우 새 PV를 추가 하 여 기존 LVM 볼륨을 확장 합니다.

1. 증가 시킬 파일 시스템의 현재 크기를 확인 합니다.

    ``` bash
    df -h /mountpoint
    ```

    ![파일 시스템의 현재 크기를 확인 하는 코드를 보여 주는 스크린샷 명령 및 결과가 강조 표시 됩니다.](./media/disk-encryption/resize-lvm/005-resize-lvm-scenariob-check-fs.png)

2. 현재 PV 구성을 확인 합니다.

    ``` bash
    pvs
    ```

    ![현재 PV 구성을 확인 하는 코드를 보여 주는 스크린샷 명령 및 결과가 강조 표시 됩니다.](./media/disk-encryption/resize-lvm/006-resize-lvm-scenariob-check-pvs.png)

3. 현재 VG 정보를 확인 합니다.

    ``` bash
    vgs
    ```

    ![현재 볼륨 그룹 정보를 확인 하는 코드를 보여 주는 스크린샷 명령 및 결과가 강조 표시 됩니다.](./media/disk-encryption/resize-lvm/007-resize-lvm-scenariob-check-vgs.png)

4. 현재 디스크 목록을 확인 하십시오. */Dev/disk/azure/scsi1/* 에서 장치를 확인 하 여 데이터 디스크를 식별 합니다.

    ``` bash
    ls -l /dev/disk/azure/scsi1/
    ```

    ![현재 디스크 목록을 확인 하는 코드를 보여 주는 스크린샷 명령 및 결과가 강조 표시 됩니다.](./media/disk-encryption/resize-lvm/008-resize-lvm-scenariob-check-scs1.png)

5. 다음의 출력을 확인 합니다 `lsblk` . 

    ``` bash
    lsbk
    ```

    ![L s b l k의 출력을 확인 하는 코드를 보여 주는 스크린샷 명령 및 결과가 강조 표시 됩니다.](./media/disk-encryption/resize-lvm/008-resize-lvm-scenariob-check-lsblk.png)

6. [LINUX vm에 데이터 디스크 연결](attach-disk-portal.md)의 지침에 따라 새 디스크를 VM에 연결 합니다.

7. 디스크 목록을 확인 하 고 새 디스크를 확인 합니다.

    ``` bash
    ls -l /dev/disk/azure/scsi1/
    ```

    ![디스크 목록을 확인 하는 코드를 보여 주는 스크린샷 결과가 강조 표시 됩니다.](./media/disk-encryption/resize-lvm/009-resize-lvm-scenariob-check-scsi12.png)

    ``` bash
    lsbk
    ```

    ![L s b l k를 사용 하 여 디스크 목록을 확인 하는 코드를 보여 주는 스크린샷 명령 및 결과가 강조 표시 됩니다.](./media/disk-encryption/resize-lvm/009-resize-lvm-scenariob-check-lsblk1.png)

8. 새 데이터 디스크의 맨 위에 새 PV를 만듭니다.

    ``` bash
    pvcreate /dev/newdisk
    ```

    ![새 PV를 만드는 코드를 보여 주는 스크린샷 결과가 강조 표시 됩니다.](./media/disk-encryption/resize-lvm/010-resize-lvm-scenariob-pvcreate.png)

    이 메서드는 파티션이 없는 PV로 전체 디스크를 사용 합니다. 또는를 사용 하 여 `fdisk` 파티션을 만든 다음 해당 파티션을에 사용할 수 있습니다 `pvcreate` .

9. Pv 목록에 PV가 추가 되었는지 확인 합니다.

    ``` bash
    pvs
    ```

    ![실제 볼륨 목록을 보여 주는 코드를 보여 주는 스크린샷 결과가 강조 표시 됩니다.](./media/disk-encryption/resize-lvm/011-resize-lvm-scenariob-check-pvs1.png)

10. 새 PV를 추가 하 여 VG를 확장 합니다.

    ``` bash
    vgextend vgname /dev/newdisk
    ```

    ![볼륨 그룹을 확장 하는 코드를 보여 주는 스크린샷 결과가 강조 표시 됩니다.](./media/disk-encryption/resize-lvm/012-resize-lvm-scenariob-vgextend.png)

11. 새 VG 크기를 확인 합니다.

    ``` bash
    vgs
    ```

    ![볼륨 그룹 크기를 확인 하는 코드를 보여 주는 스크린샷 결과가 강조 표시 됩니다.](./media/disk-encryption/resize-lvm/013-resize-lvm-scenariob-check-vgs1.png)

12. `lsblk`크기를 조정 해야 하는 LV를 식별 하는 데 사용 합니다.

    ``` bash
    lsblk
    ```

    ![크기를 조정 해야 하는 로컬 볼륨을 식별 하는 코드를 보여 주는 스크린샷 결과가 강조 표시 됩니다.](./media/disk-encryption/resize-lvm/013-resize-lvm-scenariob-check-lsblk1.png)

13. 를 사용 하 여 LV 크기를 확장 하 여 `-r` 파일 시스템을 온라인 상태로 늘립니다.

    ``` bash
    lvextend -r -L +2G /dev/vgname/lvname
    ```

    ![온라인으로 파일 시스템의 크기를 늘리는 코드를 보여 주는 스크린샷 결과가 강조 표시 됩니다.](./media/disk-encryption/resize-lvm/013-resize-lvm-scenariob-lvextend.png) 

14. LV 및 파일 시스템의 새 크기를 확인 합니다.

    ``` bash
    df -h /mountpoint
    ```

    ![로컬 볼륨 및 파일 시스템의 크기를 확인 하는 코드를 보여 주는 스크린샷 명령 및 결과가 강조 표시 됩니다.](./media/disk-encryption/resize-lvm/014-resize-lvm-scenariob-check-fs1.png)

    >[!IMPORTANT]
    >기존 LVM 구성에서 Azure 데이터 암호화를 사용 하는 경우 암호화 된 계층은 디스크 수준이 아닌 LV 수준에서 만들어집니다.
    >
    >이때 암호화 된 계층은 새 디스크로 확장 됩니다. 실제 데이터 디스크는 플랫폼 수준에서 암호화 설정이 없으므로 암호화 상태가 업데이트 되지 않습니다.
    >
    >이러한 이유 중 하나는 LVM이 권장 되는 방법입니다. 

15. 포털에서 암호화 정보를 확인 합니다.

    ![포털의 암호화 정보를 보여 주는 스크린샷 디스크 이름 및 암호화가 강조 표시 됩니다.](./media/disk-encryption/resize-lvm/014-resize-lvm-scenariob-check-portal1.png)

    디스크의 암호화 설정을 업데이트 하려면 새 LV를 추가 하 고 VM에서 확장을 사용 하도록 설정 합니다.
    
16. 새 LV를 추가 하 고 파일 시스템을 만든 다음에 추가 `/etc/fstab` 합니다.

17. 암호화 확장을 다시 설정 합니다. 이번에는 플랫폼 수준에서 새 데이터 디스크에 대 한 암호화 설정을 스탬프 합니다. CLI 예제는 다음과 같습니다.

    ``` bash
    az vm encryption enable -g ${RGNAME} --name ${VMNAME} --disk-encryption-keyvault "<your-unique-keyvault-name>"
    ```

18. 포털에서 암호화 정보를 확인 합니다.

    ![포털의 암호화 정보를 보여 주는 스크린샷 디스크 이름 및 암호화 정보가 강조 표시 됩니다.](./media/disk-encryption/resize-lvm/014-resize-lvm-scenariob-check-portal2.png)

암호화 설정이 업데이트 된 후에는 새 LV를 삭제할 수 있습니다. 또한 사용자가 만든 및에서 항목을 삭제 `/etc/fstab` `/etc/crypttab` 합니다.

![새 논리 볼륨을 삭제 하는 코드를 보여 주는 스크린샷 삭제 된 F S 탭 및 [가] 탭이 강조 표시 됩니다.](./media/disk-encryption/resize-lvm/014-resize-lvm-scenariob-delete-fstab-crypttab.png)

정리를 완료 하려면 다음 단계를 수행 합니다.

1. LV를 분리 합니다.

    ``` bash
    umount /mountpoint
    ```

1. 볼륨의 암호화 된 계층을 닫습니다.

    ``` bash
    cryptsetup luksClose /dev/vgname/lvname
    ```

1. LV를 삭제 합니다.

    ``` bash
    lvremove /dev/vgname/lvname
    ```

#### <a name="extend-a-traditional-lvm-volume-by-resizing-an-existing-pv"></a>기존 PV의 크기를 조정 하 여 기존 LVM 볼륨 확장

일부 시나리오의 경우 제한 사항에 따라 기존 디스크의 크기를 조정 해야 할 수 있습니다. 방법은 다음과 같습니다.

1. 암호화 된 디스크를 식별 합니다.

    ``` bash
    ls -l /dev/disk/azure/scsi1/
    ```

    ![암호화 된 디스크를 식별 하는 코드를 보여 주는 스크린샷 결과가 강조 표시 됩니다.](./media/disk-encryption/resize-lvm/015-resize-lvm-scenarioc-check-scsi1.png)

    ``` bash
    lsblk -fs
    ```

    ![암호화 된 디스크를 식별 하는 대체 코드를 보여 주는 스크린샷 결과가 강조 표시 됩니다.](./media/disk-encryption/resize-lvm/015-resize-lvm-scenarioc-check-lsblk.png)

2. PV 정보를 확인 합니다.

    ``` bash
    pvs
    ```

    ![실제 볼륨에 대 한 정보를 확인 하는 코드를 보여 주는 스크린샷 결과가 강조 표시 됩니다.](./media/disk-encryption/resize-lvm/016-resize-lvm-scenarioc-check-pvs.png)

    이미지의 결과로 모든 PVs의 모든 공간이 현재 사용 되 고 있음을 알 수 있습니다.

3. VG 정보를 확인 합니다.

    ``` bash
    vgs
    vgdisplay -v vgname
    ```

    ![볼륨 그룹에 대 한 정보를 확인 하는 코드를 보여 주는 스크린샷 결과가 강조 표시 됩니다.](./media/disk-encryption/resize-lvm/017-resize-lvm-scenarioc-check-vgs.png)

4. 디스크 크기를 확인 합니다. `fdisk`또는 `lsblk` 를 사용 하 여 드라이브 크기를 나열할 수 있습니다.

    ``` bash
    for disk in `ls -l /dev/disk/azure/scsi1/* | awk -F/ '{print $NF}'` ; do echo "fdisk -l /dev/${disk} | grep ^Disk "; done | bash

    lsblk -o "NAME,SIZE"
    ```

    ![디스크 크기를 확인 하는 코드를 보여 주는 스크린샷 결과가 강조 표시 됩니다.](./media/disk-encryption/resize-lvm/018-resize-lvm-scenarioc-check-fdisk.png)

    여기서는를 사용 하 여 어떤 LVs와 연결 된 PVs를 확인 했습니다 `lsblk -fs` . 을 실행 하 여 연결을 식별할 수 있습니다 `lvdisplay` .

    ``` bash
    lvdisplay --maps VG/LV
    lvdisplay --maps datavg/datalv1
    ```

    ![로컬 볼륨과 실제 볼륨 연결을 식별 하는 대체 방법을 보여 주는 스크린샷 결과가 강조 표시 됩니다.](./media/disk-encryption/resize-lvm/019-resize-lvm-scenarioc-check-lvdisplay.png)

    이 경우 4 개의 데이터 드라이브는 모두 동일한 VG 및 단일 LV의 일부입니다. 구성이 다를 수 있습니다.

5. 현재 파일 시스템 사용률을 확인 합니다.

    ``` bash
    df -h /datalvm*
    ```

    ![파일 시스템 사용률을 확인 하는 코드를 보여 주는 스크린샷 명령 및 결과가 강조 표시 됩니다.](./media/disk-encryption/resize-lvm/020-resize-lvm-scenarioc-check-df.png)

6. [Azure managed Disk 확장](expand-disks.md#expand-an-azure-managed-disk)의 지침에 따라 데이터 디스크의 크기를 조정 합니다. 포털, CLI 또는 PowerShell을 사용할 수 있습니다.

    >[!IMPORTANT]
    >VM을 실행 하는 동안에는 가상 디스크의 크기를 조정할 수 없습니다. 이 단계를 위해 VM의 할당을 취소 합니다.

7. VM을 시작 하 고를 사용 하 여 새 크기를 확인 합니다 `fdisk` .

    ``` bash
    for disk in `ls -l /dev/disk/azure/scsi1/* | awk -F/ '{print $NF}'` ; do echo "fdisk -l /dev/${disk} | grep ^Disk "; done | bash

    lsblk -o "NAME,SIZE"
    ```

    ![디스크 크기를 확인 하는 코드를 보여 주는 스크린샷 결과가 강조 표시 됩니다.](./media/disk-encryption/resize-lvm/021-resize-lvm-scenarioc-check-fdisk1.png)

    이 경우는 `/dev/sdd` 5 g에서 20 g로 크기가 조정 되었습니다.

8. 현재 PV 크기를 확인 합니다.

    ``` bash
    pvdisplay /dev/resizeddisk
    ```

    ![P V의 크기를 확인 하는 코드를 보여 주는 스크린샷 결과가 강조 표시 됩니다.](./media/disk-encryption/resize-lvm/022-resize-lvm-scenarioc-check-pvdisplay.png)
    
    디스크가 크기 조정 된 경우에도 PV의 크기는 여전히 이전입니다.

9. PV 크기 조정:

    ``` bash
    pvresize /dev/resizeddisk
    ```

    ![실제 볼륨의 크기를 조정 하는 코드를 보여 주는 스크린샷 결과가 강조 표시 됩니다.](./media/disk-encryption/resize-lvm/023-resize-lvm-scenarioc-check-pvresize.png)


10. PV 크기를 확인 합니다.

    ``` bash
    pvdisplay /dev/resizeddisk
    ```

    ![실제 볼륨의 크기를 확인 하는 코드를 보여 주는 스크린샷 결과가 강조 표시 됩니다.](./media/disk-encryption/resize-lvm/024-resize-lvm-scenarioc-check-pvdisplay1.png)

    크기를 조정 하려는 모든 디스크에 동일한 절차를 적용 합니다.

11. VG 정보를 확인 합니다.

    ``` bash
    vgdisplay vgname
    ```

    ![볼륨 그룹에 대 한 정보를 확인 하는 코드를 보여 주는 스크린샷 결과가 강조 표시 됩니다.](./media/disk-encryption/resize-lvm/025-resize-lvm-scenarioc-check-vgdisplay1.png)

    이제 VG에 LVs에 할당할 충분 한 공간이 있습니다.

12. LV 크기 조정:

    ``` bash
    lvresize -r -L +5G vgname/lvname
    lvresize -r -l +100%FREE /dev/datavg/datalv01
    ```

    ![L V의 크기를 조정 하는 코드를 보여 주는 스크린샷 결과가 강조 표시 됩니다.](./media/disk-encryption/resize-lvm/031-resize-lvm-scenarioc-check-lvresize1.png)

13. 파일 시스템의 크기를 확인 합니다.

    ``` bash
    df -h /datalvm2
    ```

    ![파일 시스템의 크기를 확인 하는 코드를 보여 주는 스크린샷 결과가 강조 표시 됩니다.](./media/disk-encryption/resize-lvm/032-resize-lvm-scenarioc-check-df3.png)

#### <a name="extend-an-lvm-on-crypt-volume-by-adding-a-new-pv"></a>새 PV를 추가 하 여 LVM 온-서 볼륨 확장

새 PV를 추가 하 여 LVM 온-서 볼륨을 확장할 수도 있습니다. 이 메서드는 [암호화 된 장치에서 LVM 및 RAID 구성](how-to-configure-lvm-raid-on-crypt.md#general-steps)의 단계를 긴밀 하 게 따릅니다. 새 디스크를 추가 하는 방법에 대해 설명 하는 섹션을 참조 하 고 LVM-다중 클래스 구성에서 설정 합니다.

이 메서드를 사용 하 여 기존 LV에 공간을 추가할 수 있습니다. 또는 새 VGs 또는 LVs를 만들 수 있습니다.

1. VG의 현재 크기를 확인 합니다.

    ``` bash
    vgdisplay vgname
    ```

    ![볼륨 그룹 크기를 확인 하는 코드를 보여 주는 스크린샷 결과가 강조 표시 됩니다.](./media/disk-encryption/resize-lvm/033-resize-lvm-scenarioe-check-vg01.png)

2. 확장 하려는 파일 시스템 및 LV의 크기를 확인 합니다.

    ``` bash
    lvdisplay /dev/vgname/lvname
    ```

    ![로컬 볼륨의 크기를 확인 하는 코드를 보여 주는 스크린샷 결과가 강조 표시 됩니다.](./media/disk-encryption/resize-lvm/034-resize-lvm-scenarioe-check-lv01.png)

    ``` bash
    df -h mountpoint
    ```

    ![파일 시스템의 크기를 확인 하는 코드를 보여 주는 스크린샷 결과가 강조 표시 됩니다.](./media/disk-encryption/resize-lvm/034-resize-lvm-scenarioe-check-fs01.png)

3. VM에 새 데이터 디스크를 추가 하 고 식별 합니다.

    새 디스크를 추가 하기 전에 디스크를 확인 합니다.

    ``` bash
    fdisk -l | egrep ^"Disk /"
    ```

    ![디스크 크기를 확인 하는 코드를 보여 주는 스크린샷 결과가 강조 표시 됩니다.](./media/disk-encryption/resize-lvm/035-resize-lvm-scenarioe-check-newdisk01.png)

    새 디스크를 추가 하기 전에 디스크를 확인 하는 또 다른 방법은 다음과 같습니다.

    ``` bash
    lsblk
    ```

    ![디스크 크기를 확인 하는 대체 코드를 보여 주는 스크린샷 결과가 강조 표시 됩니다.](./media/disk-encryption/resize-lvm/035-resize-lvm-scenarioe-check-newdisk02.png)

    새 디스크를 추가 하기 위해 PowerShell, Azure CLI 또는 Azure Portal를 사용할 수 있습니다. 자세한 내용은 [LINUX VM에 데이터 디스크 연결](attach-disk-portal.md)을 참조 하세요.

    커널 이름 구성표가 새로 추가 된 장치에 적용 됩니다. 새 드라이브에는 일반적으로 다음 사용 가능한 문자가 할당 됩니다. 이 경우 추가 된 디스크는 `sdd` 입니다.

4. 디스크를 확인 하 여 새 디스크가 추가 되었는지 확인 합니다.

    ``` bash
    fdisk -l | egrep ^"Disk /"
    ```

    ![디스크를 나열 하는 코드를 보여 주는 스크린샷 결과가 강조 표시 됩니다.](./media/disk-encryption/resize-lvm/036-resize-lvm-scenarioe-check-newdisk02.png)

    ``` bash
    lsblk
    ```

    ![새로 추가 된 디스크를 출력에 보여 주는 스크린샷](./media/disk-encryption/resize-lvm/036-resize-lvm-scenarioe-check-newdisk03.png)

5. 최근 추가 된 디스크를 기반으로 파일 시스템을 만듭니다. 에서 연결 된 장치와 디스크를 일치 시킵니다 `/dev/disk/azure/scsi1/` .

    ``` bash
    ls -la /dev/disk/azure/scsi1/
    ```

    ![파일 시스템을 만드는 코드를 보여 주는 스크린샷 결과가 강조 표시 됩니다.](./media/disk-encryption/resize-lvm/037-resize-lvm-scenarioe-check-newdisk03.png)

    ``` bash
    mkfs.ext4 /dev/disk/azure/scsi1/${disk}
    ```

    ![파일 시스템을 만들고 디스크와 연결 된 장치를 일치 시키는 추가 코드를 보여 주는 스크린샷 결과가 강조 표시 됩니다.](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-mkfs01.png)

6. 새로 추가 된 디스크에 대 한 임시 탑재 지점을 만듭니다.

    ``` bash
    newmount=/data4
    mkdir ${newmount}
    ```

7. 최근에 만든 파일 시스템을에 추가 `/etc/fstab` 합니다.

    ``` bash
    blkid /dev/disk/azure/scsi1/lun4| awk -F\" '{print "UUID="$2" '${newmount}' "$4" defaults,nofail 0 0"}' >> /etc/fstab
    ```

8. 새로 만든 파일 시스템을 탑재 합니다.

    ``` bash
    mount -a
    ```

9. 새 파일 시스템이 탑재 되어 있는지 확인 합니다.

    ``` bash
    df -h
    ```

    ![파일 시스템이 탑재 되었는지 확인 하는 코드를 보여 주는 스크린샷 결과가 강조 표시 됩니다.](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-df.png)

    ``` bash
    lsblk
    ```

    ![파일 시스템이 탑재 되었는지 확인 하는 추가 코드를 보여 주는 스크린샷 결과가 강조 표시 됩니다.](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-lsblk.png)

10. 데이터 드라이브에 대해 이전에 시작한 암호화를 다시 시작 합니다.

    >[!TIP]
    >LVM에 대 한 사용자의 경우를 사용 하는 것이 좋습니다 `EncryptFormatAll` . 그렇지 않으면 추가 디스크를 설정 하는 동안 이중 암호화가 표시 될 수 있습니다.
    >
    >자세한 내용은 [암호화 된 장치에서 LVM 및 RAID 구성](how-to-configure-lvm-raid-on-crypt.md)을 참조 하세요.

    예를 들면 다음과 같습니다.

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

    ![묘지 계층을 확인 하는 코드를 보여 주는 스크린샷 결과가 강조 표시 됩니다.](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-lsblk2.png)

11. 새 디스크의 암호화 된 계층을 분리 합니다.

    ``` bash
    umount ${newmount}
    ```

12. 현재 PV 정보를 확인 합니다.

    ``` bash
    pvs
    ```

    ![실제 볼륨에 대 한 정보를 확인 하는 코드를 보여 주는 스크린샷 결과가 강조 표시 됩니다.](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-currentpvs.png)

13. 디스크의 암호화 된 계층 위에 PV를 만듭니다. 이전 명령에서 장치 이름을 가져옵니다 `lsblk` . `/dev/`장치 이름 앞에 매퍼를 추가 하 여 PV를 만듭니다.

    ``` bash
    pvcreate /dev/mapper/mapperdevicename
    ```

    ![암호화 된 계층에 실제 볼륨을 만드는 코드를 보여 주는 스크린샷 결과가 강조 표시 됩니다.](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-pvcreate.png)

    현재 서명 지우기에 대 한 경고가 표시 `ext4 fs` 됩니다. 이 경고는 정상적인 것입니다. 을 사용 하 여이 질문에 대답 `y` 합니다.

14. 새 PV가 LVM 구성에 추가 되었는지 확인 합니다.

    ``` bash
    pvs
    ```

    ![실제 볼륨이 LVM 구성에 추가 되었는지 확인 하는 코드를 보여 주는 스크린샷 결과가 강조 표시 됩니다.](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-newpv.png)

15. 증가 해야 하는 VG에 새 PV를 추가 합니다.

    ``` bash
    vgextend vgname /dev/mapper/nameofhenewpv
    ```

    ![볼륨 그룹에 실제 볼륨을 추가 하는 코드를 보여 주는 스크린샷 결과가 강조 표시 됩니다.](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-vgextent.png)

16. VG의 새 크기 및 사용 가능한 공간을 확인 합니다.

    ``` bash
    vgdisplay vgname
    ```

    ![볼륨 그룹의 크기 및 사용 가능한 공간을 확인 하는 코드를 보여 주는 스크린샷 결과가 강조 표시 됩니다.](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-vgdisplay.png)

    개수와의 증가를 확인 합니다 `Total PE` `Free PE / Size` .

17. LV 및 파일 시스템의 크기를 늘립니다. `-r`에서 옵션을 사용 `lvextend` 합니다. 이 예제에서는 VG의 사용 가능한 총 공간을 지정 된 LV에 추가 합니다.

    ``` bash
    lvextend -r -l +100%FREE /dev/vgname/lvname
    ```

    ![로컬 볼륨 및 파일 시스템의 크기를 늘리는 코드를 보여 주는 스크린샷 결과가 강조 표시 됩니다.](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-lvextend.png)

다음 단계를 수행 하 여 변경 내용을 확인 합니다.

1. LV의 크기를 확인 합니다.

    ``` bash
    lvdisplay /dev/vgname/lvname
    ```

    ![로컬 볼륨의 새 크기를 확인 하는 코드를 보여 주는 스크린샷 결과가 강조 표시 됩니다.](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-lvdisplay.png)

1. 파일 시스템의 새 크기를 확인 합니다.

    ``` bash
    df -h mountpoint
    ```

    ![파일 시스템의 새 크기를 확인 하는 코드를 보여 주는 스크린샷 결과가 강조 표시 됩니다.](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-df1.png)

1. LVM 계층이 암호화 된 계층의 맨 위에 있는지 확인 합니다.

    ``` bash
    lsblk
    ```

    ![LVM 계층이 암호화 된 계층의 맨 위에 있는지 확인 하는 코드를 보여 주는 스크린샷 결과가 강조 표시 됩니다.](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-lsblk3.png)

    `lsblk`옵션 없이를 사용 하는 경우 탑재 지점이 여러 번 표시 됩니다. 명령은 장치 및 LVs를 기준으로 정렬 합니다. 

    을 사용 하는 것이 좋습니다 `lsblk -fs` . 이 명령에서는 `-fs` 탑재 지점이 한 번만 표시 되도록 정렬 순서를 반대로 바꿉니다. 디스크가 여러 번 표시 됩니다.

    ``` bash
    lsblk -fs
    ```

    ![LVM 계층이 암호화 된 계층의 맨 위에 있는지 확인 하는 대체 코드를 보여 주는 스크린샷 결과가 강조 표시 됩니다.](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-lsblk4.png)

#### <a name="extend-an-lvm-on-a-crypt-volume-by-resizing-an-existing-pv"></a>기존 PV 크기를 조정 하 여 묘지 볼륨에서 LVM 확장

1. 암호화 된 디스크를 식별 합니다.

    ``` bash
    lsblk
    ```

    ![암호화 된 디스크를 식별 하는 코드를 보여 주는 스크린샷 결과가 강조 표시 됩니다.](./media/disk-encryption/resize-lvm/039-resize-lvm-scenariof-lsblk01.png)

    ``` bash
    lsblk -s
    ```

    ![암호화 된 디스크를 식별 하는 대체 코드를 보여 주는 스크린샷 결과가 강조 표시 됩니다.](./media/disk-encryption/resize-lvm/040-resize-lvm-scenariof-lsblk012.png)

2. PV 정보를 확인 합니다.

    ``` bash
    pvs
    ```

    ![실제 볼륨에 대 한 정보를 확인 하는 코드를 보여 주는 스크린샷 결과가 강조 표시 됩니다.](./media/disk-encryption/resize-lvm/041-resize-lvm-scenariof-pvs.png)

3. VG 정보를 확인 합니다.

    ``` bash
    vgs
    ```

    ![볼륨 그룹에 대 한 정보를 확인 하는 코드를 보여 주는 스크린샷 결과가 강조 표시 됩니다.](./media/disk-encryption/resize-lvm/042-resize-lvm-scenariof-vgs.png)

4. LV 정보를 확인 합니다.

    ``` bash
    lvs
    ```

    ![로컬 볼륨에 대 한 정보를 확인 하는 코드를 보여 주는 스크린샷 결과가 강조 표시 됩니다.](./media/disk-encryption/resize-lvm/043-resize-lvm-scenariof-lvs.png)

5. 파일 시스템 사용률을 확인 합니다.

    ``` bash
    df -h /mountpoint(s)
    ```

    ![사용 되는 파일 시스템의 크기를 확인 하는 코드를 보여 주는 스크린샷 결과가 강조 표시 됩니다.](./media/disk-encryption/resize-lvm/044-resize-lvm-scenariof-fs.png)

6. 디스크 크기를 확인 합니다.

    ``` bash
    fdisk
    fdisk -l | egrep ^"Disk /"
    lsblk
    ```

    ![디스크 크기를 확인 하는 코드를 보여 주는 스크린샷 결과가 강조 표시 됩니다.](./media/disk-encryption/resize-lvm/045-resize-lvm-scenariof-fdisk01.png)

7. 데이터 디스크의 크기를 조정 합니다. Portal, CLI 또는 PowerShell을 사용할 수 있습니다. 자세한 내용은 [LINUX VM에서 가상 하드 디스크 확장](expand-disks.md#expand-an-azure-managed-disk)의 디스크 크기 조정 섹션을 참조 하세요. 

    >[!IMPORTANT]
    >VM을 실행 하는 동안에는 가상 디스크의 크기를 조정할 수 없습니다. 이 단계를 위해 VM의 할당을 취소 합니다.

8. 디스크 크기를 확인 합니다.

    ``` bash
    fdisk
    fdisk -l | egrep ^"Disk /"
    lsblk
    ```

    ![디스크 크기를 확인 하는 코드를 보여 주는 스크린샷 결과가 강조 표시 됩니다.](./media/disk-encryption/resize-lvm/046-resize-lvm-scenariof-fdisk02.png)

    이 경우 두 디스크의 크기가 2gb에서 4gb로 조정 되었습니다. 하지만 파일 시스템, LV 및 PV의 크기는 동일 하 게 유지 됩니다.

9. 현재 PV 크기를 확인 합니다. LVM 온-컴퓨터에서 PV는 `/dev/mapper/` 장치가 아니라 장치입니다 `/dev/sd*` .

    ``` bash
    pvdisplay /dev/mapper/devicemappername
    ```

    ![현재 실제 볼륨의 크기를 확인 하는 코드를 보여 주는 스크린샷 결과가 강조 표시 됩니다.](./media/disk-encryption/resize-lvm/047-resize-lvm-scenariof-pvs.png)

10. PV 크기 조정:

    ``` bash
    pvresize /dev/mapper/devicemappername
    ```

    ![실제 볼륨의 크기를 조정 하는 코드를 보여 주는 스크린샷 결과가 강조 표시 됩니다.](./media/disk-encryption/resize-lvm/048-resize-lvm-scenariof-resize-pv.png)

11. 새 PV 크기를 확인 합니다.

    ``` bash
    pvdisplay /dev/mapper/devicemappername
    ```

    ![실제 볼륨의 크기를 확인 하는 코드를 보여 주는 스크린샷 결과가 강조 표시 됩니다.](./media/disk-encryption/resize-lvm/049-resize-lvm-scenariof-pv.png)

12. PV에서 암호화 된 계층의 크기를 조정 합니다.

    ``` bash
    cryptsetup resize /dev/mapper/devicemappername
    ```

    크기를 조정 하려는 모든 디스크에 동일한 절차를 적용 합니다.

13. VG 정보를 확인 합니다.

    ``` bash
    vgdisplay vgname
    ```

    ![볼륨 그룹에 대 한 정보를 확인 하는 코드를 보여 주는 스크린샷 결과가 강조 표시 됩니다.](./media/disk-encryption/resize-lvm/050-resize-lvm-scenariof-vg.png)

    이제 VG에 LVs에 할당할 충분 한 공간이 있습니다.

14. LV 정보를 확인 합니다.

    ``` bash
    lvdisplay vgname/lvname
    ```

    ![로컬 볼륨에 대 한 정보를 확인 하는 코드를 보여 주는 스크린샷 결과가 강조 표시 됩니다.](./media/disk-encryption/resize-lvm/051-resize-lvm-scenariof-lv.png)

15. 파일 시스템 사용률을 확인 합니다.

    ``` bash
    df -h /mountpoint
    ```

    ![파일 시스템의 사용률을 확인 하는 코드를 보여 주는 스크린샷 결과가 강조 표시 됩니다.](./media/disk-encryption/resize-lvm/052-resize-lvm-scenariof-fs.png)

16. LV 크기 조정:

    ``` bash
    lvresize -r -L +2G /dev/vgname/lvname
    ```

    ![로컬 볼륨의 크기를 조정 하는 코드를 보여 주는 스크린샷 결과가 강조 표시 됩니다.](./media/disk-encryption/resize-lvm/053-resize-lvm-scenariof-lvresize.png)

    여기서는 옵션을 사용 `-r` 하 여 파일 시스템의 크기를 조정 합니다.

17. LV 정보를 확인 합니다.

    ``` bash
    lvdisplay vgname/lvname
    ```

    ![로컬 볼륨에 대 한 정보를 가져오는 코드를 보여 주는 스크린샷 결과가 강조 표시 됩니다.](./media/disk-encryption/resize-lvm/054-resize-lvm-scenariof-lvsize.png)

18. 파일 시스템 사용률을 확인 합니다.

    ``` bash
    df -h /mountpoint
    ```

    ![파일 시스템 사용률을 확인 하는 코드를 보여 주는 스크린샷 결과가 강조 표시 됩니다.](./media/disk-encryption/resize-lvm/055-resize-lvm-scenariof-fs.png)

이를 필요로 하는 다른 모든 LV에 동일한 크기 조정 절차를 적용 합니다.

## <a name="next-steps"></a>다음 단계

[문제 해결 Azure Disk Encryption](disk-encryption-troubleshooting.md)
