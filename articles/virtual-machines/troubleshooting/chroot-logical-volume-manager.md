---
title: LVM (논리 볼륨 관리자)이 사용 되는 chroot를 사용 하 여 Linux Vm 복구-Azure Vm
description: LVMs을 사용 하 여 Linux Vm을 복구 합니다.
services: virtual-machines-linux
documentationcenter: ''
author: vilibert
manager: spogge
editor: ''
tags: Linux chroot LVM
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 11/24/2019
ms.author: vilibert
ms.openlocfilehash: c0041a835c02263f23c5cdc6f839756edfb070c1
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/20/2020
ms.locfileid: "86526880"
---
# <a name="troubleshooting-a-linux-vm-when-there-is-no-access-to-the-azure-serial-console-and-the-disk-layout-is-using-lvm-logical-volume-manager"></a>Azure 직렬 콘솔에 대 한 액세스 권한이 없고 디스크 레이아웃이 LVM을 사용 하는 경우 Linux VM 문제 해결 (논리 볼륨 관리자)

이 문제 해결 가이드는 Linux VM이 부팅 되지 않는 시나리오에 유용 하며, ssh를 사용할 수 없고 기본 파일 시스템 레이아웃이 LVM (논리 볼륨 관리자)을 사용 하 여 구성 됩니다.

## <a name="take-snapshot-of-the-failing-vm"></a>실패 한 VM의 스냅숏 만들기

영향을 받는 VM의 스냅숏을 만듭니다. 

그러면 스냅숏이 **복구** VM에 연결 됩니다. **스냅숏을**만드는 방법 [에 대 한 지침을](../linux/snapshot-copy-managed-disk.md#use-azure-portal) 따르세요.

## <a name="create-a-rescue-vm"></a>복구 VM 만들기
일반적으로 동일한 운영 체제 버전 또는 유사한 운영 체제 버전의 복구 VM을 권장 합니다. 영향을 받는 VM의 동일한 **지역** 및 **리소스 그룹** 사용

## <a name="connect-to-the-rescue-vm"></a>복구 VM에 연결
Ssh를 사용 하 여 **복구** VM에 연결 합니다. 권한 상승 및를 사용 하 여 슈퍼 사용자가 됩니다.

`sudo su -`

## <a name="attach-the-disk"></a>디스크 연결
이전에 만든 스냅숏에서 디스크를 **복구** VM에 연결 합니다.

Azure Portal- **> VM >** **디스크** 를 선택 합니다. 

![디스크 만들기](./media/chroot-logical-volume-manager/create-disk-from-snap.png)

필드를 채웁니다. 새 디스크에 이름을 할당 하 고, 스냅숏과 동일한 리소스 그룹을 선택 하 고, 영향을 받는 VM을 선택 하 고, VM을 복구 합니다.

**원본 유형이** **Snapshot** 입니다.
**원본 스냅숏은** 이전에 만든 **스냅숏의** 이름입니다.

![디스크 2 만들기](./media/chroot-logical-volume-manager/create-disk-from-snap-2.png)

연결 된 디스크에 대 한 탑재 지점을 만듭니다.

`mkdir /rescue`

**Fdisk-l** 명령을 실행 하 여 스냅숏 디스크가 연결 되어 있는지 확인 하 고 사용 가능한 모든 장치 및 파티션을 나열 합니다.

`fdisk -l`

대부분의 시나리오에서 연결 된 스냅숏 디스크는 **/sv/sdc** 로 표시 되며, 두 개의 파티션을 표시 합니다. **/sv/sdc1** 및 **/stv/sdc2**

![Fdisk](./media/chroot-logical-volume-manager/fdisk-output-sdc.png)

는 **\*** 부팅 파티션을 나타내며 두 파티션이 모두 탑재 됩니다.

**Lsblk** 명령을 실행 하 여 영향을 받는 Vm의 lvms을 확인 합니다.

`lsblk`

![Lsblk 실행](./media/chroot-logical-volume-manager/lsblk-output-mounted.png)


영향을 받는 VM의 LVMs이 표시 되는지 확인 합니다.
그렇지 않은 경우 다음 명령을 사용 하 여 사용 하도록 설정 하 고 **lsblk**를 다시 실행 합니다.
계속 하기 전에 연결 된 디스크의 LVMs이 표시 되는지 확인 합니다.

```
vgscan --mknodes
vgchange -ay
lvscan
mount –a
lsblk
```

/(루트) 파티션을 포함 하는 논리 볼륨을 탑재 하는 경로를 찾습니다. /Etc/default/grub와 같은 구성 파일이 있습니다.

이 예제에서는 이전 **lsblk** 명령 **rootvg-rootlv** 의 출력을 탑재 하는 데 올바른 **루트** LV를 사용 하 고 다음 명령에서 사용할 수 있습니다.

다음 명령의 출력은 **루트** LV에 대해 탑재할 경로를 표시 합니다.

`pvdisplay -m | grep -i rootlv`

![Rootlv](./media/chroot-logical-volume-manager/locate-rootlv.png)

디렉터리에이 장치 탑재/복구로 이동 합니다.

`mount /dev/rootvg/rootlv /rescue`

/Rescue/boot에 **부팅 플래그가** 설정 된 파티션을 탑재 합니다.

`
mount /dev/sdc1 /rescue/boot
`

이제 **lsblk** 명령을 사용 하 여 연결 된 디스크의 파일 시스템이 올바르게 탑재 되었는지 확인 합니다.

![Lsblk 실행](./media/chroot-logical-volume-manager/lsblk-output-1.png)

또는 **Df 번째** 명령

![Df](./media/chroot-logical-volume-manager/df-output.png)

## <a name="gaining-chroot-access"></a>Chroot 액세스 권한 얻기

**Chroot** 액세스를 통해 다양 한 픽스를 수행할 수 있고 각 Linux 배포에 대해 약간의 변형이 있습니다.

```
 cd /rescue
 mount -t proc proc proc
 mount -t sysfs sys sys/
 mount -o bind /dev dev/
 mount -o bind /dev/pts dev/pts/
 chroot /rescue
```

다음과 같은 오류가 발생 하는 경우

**chroot: '/bin/bash ' 명령을 실행 하지 못했습니다. 해당 파일 또는 디렉터리가 없습니다.**

**usr** 논리 볼륨을 탑재 하려고 합니다.

`
mount  /dev/mapper/rootvg-usrlv /rescue/usr
`

> [!TIP]
> **Chroot** 환경에서 명령을 실행할 때 로컬 **복구** VM이 아닌 연결 된 OS 디스크에 대해 실행 됩니다. 

명령을 사용 하 여 소프트웨어를 설치, 제거 및 업데이트할 수 있습니다. 오류를 해결 하기 위해 Vm 문제를 해결 합니다.


Lsblk 명령을 실행 하 고/및/rescue/boot is/boot ![ chrooted 팅](./media/chroot-logical-volume-manager/chrooted.png)

## <a name="perform-fixes"></a>수정 수행

### <a name="example-1---configure-the-vm-to-boot-from-a-different-kernel"></a>예 1-다른 커널에서 부팅 하도록 VM 구성

일반적인 시나리오는 현재 설치 된 커널이 손상 되었거나 업그레이드가 제대로 완료 되지 않았을 수 있으므로 VM을 이전 커널에서 강제로 부팅 하는 것입니다.


```
cd /boot/grub2

grep -i linux grub.cfg

grub2-editenv list

grub2-set-default "CentOS Linux (3.10.0-1062.1.1.el7.x86_64) 7 (Core)"

grub2-editenv list

grub2-mkconfig -o /boot/grub2/grub.cfg
```

*살펴보았습니다*

**Grep** 명령은 grub에서 인식 하는 커널을 나열 합니다 **.**
![커널](./media/chroot-logical-volume-manager/kernels.png)

grub2-다음 부팅 커널 기본값에서 로드 되는 커널을 표시 하는 **editenv 목록** ![](./media/chroot-logical-volume-manager/kernel-default.png)

**grub2-기본값** 은 다른 커널 grub2 집합으로 변경 하는 데 사용 됩니다. ![](./media/chroot-logical-volume-manager/grub2-set-default.png)

**grub2-** 다음 부팅 새 커널에서 로드 되는 커널을 표시 합니다. ![](./media/chroot-logical-volume-manager/kernel-new.png)

**grub2-Grub2-mkconfig** grub2 grub2-mkconfig의 버전을 사용 하 여 cfg를 다시 빌드합니다. ![](./media/chroot-logical-volume-manager/grub2-mkconfig.png)



### <a name="example-2---upgrade-packages"></a>예제 2-패키지 업그레이드

실패 한 커널 업그레이드는 VM을 부팅할 수 없는 상태로 렌더링할 수 있습니다.
모든 논리 볼륨을 탑재 하 여 패키지 제거 또는 다시 설치 허용

**Lvs** 명령을 실행 하 여 탑재에 사용할 수 있는 **lvs** 를 확인 합니다. 마이그레이션 되었거나 다른 클라우드 공급자에서 가져온 모든 VM은 구성에 따라 달라 집니다.

**Chroot** 환경을 종료 하 고 필요한 **LV** 를 탑재 합니다.

![고급](./media/chroot-logical-volume-manager/advanced.png)

이제를 실행 하 여 **chroot** 환경에 다시 액세스 합니다.

`chroot /rescue`

모든 LVs는 탑재 된 파티션으로 표시 되어야 합니다.

![고급](./media/chroot-logical-volume-manager/chroot-all-mounts.png)

설치 된 **커널** 쿼리

![고급](./media/chroot-logical-volume-manager/rpm-kernel.png)

필요한 경우 **커널** 
 고급을 제거 하거나 업그레이드 합니다. ![](./media/chroot-logical-volume-manager/rpm-remove-kernel.png)


### <a name="example-3---enable-serial-console"></a>예제 3-직렬 콘솔 사용
Azure 직렬 콘솔에 액세스할 수 없는 경우 Linux VM에 대 한 GRUB 구성 매개 변수를 확인 하 고 수정 합니다. [이 문서에서](./serial-console-grub-proactive-configuration.md) 자세한 정보를 찾을 수 있습니다.

### <a name="example-4---kernel-loading-with-problematic-lvm-swap-volume"></a>예제 4-문제가 있는 LVM 스왑 볼륨을 사용한 커널 로드

VM이 완전히 부팅 되지 않고 **dracgprompt** 프롬프트로 전환 될 수 있습니다.
실패에 대 한 자세한 내용은 Azure 직렬 콘솔에서 찾을 수도 있고, Azure Portal-> boot diagnostics-> Serial log로 이동할 수도 있습니다.


다음과 같은 오류가 있을 수 있습니다.

```
[  188.000765] dracut-initqueue[324]: Warning: /dev/VG/SwapVol does not exist
         Starting Dracut Emergency Shell...
Warning: /dev/VG/SwapVol does not exist
```

이 예제에서는 이름이 **VG/SwapVol** 인 lv를 로드 하도록 grub를 구성 하 고 VM에서이를 찾을 수 없습니다. 이 줄은 LV를 참조 하는 커널이 로드 되는 방법을 보여 줍니다.

```
[    0.000000] Command line: BOOT_IMAGE=/vmlinuz-3.10.0-1062.4.1.el7.x86_64 root=/dev/mapper/VG-OSVol ro console=tty0 console=ttyS0 earlyprintk=ttyS0 net.ifnames=0 biosdevname=0 crashkernel=256M rd.lvm.lv=VG/OSVol rd.lvm.lv=VG/SwapVol nodmraid rhgb quiet
[    0.000000] e820: BIOS-provided physical RAM map:
```

 /Etc/default/grub 구성에서 잘못 된 LV를 제거 하 고 grub2를 다시 빌드합니다.


## <a name="exit-chroot-and-swap-the-os-disk"></a>Chroot를 종료 하 고 OS 디스크를 교환 합니다.

문제를 복구한 후에는 복구 VM에서 디스크를 분리 하 고 분리 하 여 영향을 받는 VM OS 디스크로 교환할 수 있도록 합니다.

```
exit
cd /
umount /rescue/proc/
umount /rescue/sys/
umount /rescue/dev/pts
umount /rescue/dev/
umount /rescue/boot
umount /rescue
```

복구 VM에서 디스크를 분리 하 고 디스크 교체를 수행 합니다.

포털 **디스크** 에서 VM을 선택 하 고 분리 **detach** 
 디스크 분리를 선택 합니다. ![](./media/chroot-logical-volume-manager/detach-disk.png) 

변경 내용 저장 ![ 분리](./media/chroot-logical-volume-manager/save-detach.png) 

이제 디스크를 사용할 수 있게 되 면 영향을 받는 VM의 원래 OS 디스크와 교체할 수 있습니다.

Azure Portal에서 실패 한 VM으로 이동 하 고 **디스크**  ->  **교체 OS 디스크** 
 ![ 스왑 디스크를 선택 합니다.](./media/chroot-logical-volume-manager/swap-disk.png) 

필드를 완료 합니다. **디스크 선택** 은 이전 단계에서 바로 분리 된 스냅숏 디스크입니다. 영향을 받는 VM의 VM 이름도 필요 합니다. 그런 다음 **확인** 을 선택 합니다.

![새 os 디스크](./media/chroot-logical-volume-manager/new-osdisk.png) 

VM을 실행 하는 경우 디스크 교환이 종료 되 고 디스크 교환 작업이 완료 되 면 VM을 다시 부팅 합니다.


## <a name="next-steps"></a>다음 단계
자세한 정보

 [Azure 직렬 콘솔]( https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-linux)

[단일 사용자 모드](./serial-console-grub-single-user-mode.md)
