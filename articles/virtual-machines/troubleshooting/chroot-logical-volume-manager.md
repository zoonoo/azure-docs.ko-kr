---
title: LVM(논리 볼륨 관리자)이 사용되는 chroot를 사용하여 Linux VM 복구 - Azure VM
description: LvM을 가진 리눅스 VM의 복구.
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
ms.openlocfilehash: 20d710f717a9dff26f46ac7a201a9b694f3fbe84
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74684140"
---
# <a name="troubleshooting-a-linux-vm-when-there-is-no-access-to-the-azure-serial-console-and-the-disk-layout-is-using-lvm-logical-volume-manager"></a>Azure 시리얼 콘솔에 액세스할 수 없고 디스크 레이아웃이 LVM(논리 볼륨 관리자)을 사용하는 경우 Linux VM 문제 해결

이 문제 해결 가이드는 Linux VM이 부팅되지 않고 ssh가 불가능하며 기본 파일 시스템 레이아웃이 LVM(논리 볼륨 관리자)으로 구성된 시나리오에 유용합니다.

## <a name="take-snapshot-of-the-failing-vm"></a>실패한 VM의 스냅샷 생성

영향을 받는 VM의 스냅숏을 가져옵니다. 

그러면 스냅샷이 **구조** VM에 연결됩니다. **스냅샷을**찍는 방법에 대한 지침을 [따르십시오.](https://docs.microsoft.com/azure/virtual-machines/linux/snapshot-copy-managed-disk#use-azure-portal)

## <a name="create-a-rescue-vm"></a>구조 VM 만들기
일반적으로 동일하거나 유사한 운영 체제 버전의 구조 VM을 권장합니다. 영향을 받는 VM의 동일한 **지역** 및 **리소스 그룹** 사용

## <a name="connect-to-the-rescue-vm"></a>구조 VM에 연결
**구조** VM에 ssh를 사용하여 연결합니다. 권한을 높이고 사용 하 여 슈퍼 사용자가 되 십시오.

`sudo su -`

## <a name="attach-the-disk"></a>디스크 연결
이전에 수행한 스냅숏에서 만든 **구조** VM에 디스크를 연결합니다.

Azure 포털 -> **구조** VM -> **디스크를 선택합니다.** 

![디스크 만들기](./media/chroot-logical-volume-manager/create-disk-from-snap.png)

필드를 채웁니다. 새 디스크에 이름을 할당하고 스냅샷, 영향을 받는 VM 및 구조 VM과 동일한 리소스 그룹을 선택합니다.

**소스 유형은** **스냅숏** 입니다.
**소스 스냅숏은** 이전에 만든 **스냅숏의** 이름입니다.

![디스크 2 만들기](./media/chroot-logical-volume-manager/create-disk-from-snap-2.png)

연결된 디스크에 대해 마운트 지점을 작성합니다.

`mkdir /rescue`

**fdisk -l** 명령을 실행하여 스냅샷 디스크가 연결되었는지 확인하고 사용 가능한 모든 장치 및 파티션을 나열합니다.

`fdisk -l`

대부분의 시나리오에서 첨부된 스냅샷 디스크는 두 개의 **파티션/dev/sdc1** 및 **/dev/sdc2를** 표시하는 **/dev/sdc로** 표시됩니다.

![Fdisk](./media/chroot-logical-volume-manager/fdisk-output-sdc.png)

는 **\*** 부팅 파티션을 나타내며 두 파티션을 모두 마운트해야 합니다.

**lsblk** 명령을 실행하여 영향을 받는 VM의 LPM을 확인합니다.

`lsblk`

![실행 lsblk](./media/chroot-logical-volume-manager/lsblk-output-mounted.png)


영향을 받는 VM의 LvM이 표시되는지 확인합니다.
그렇지 않은 경우 아래 명령을 사용하여 활성화하고 **lsblk를**다시 실행합니다.
계속하기 전에 연결된 디스크의 LvM이 표시되도록 하십시오.

```
vgscan --mknodes
vgchange -ay
lvscan
mount –a
lsblk
```

/(루트) 파티션이 포함된 논리 볼륨을 탑재할 경로를 찾습니다. /etc/기본/grub과 같은 구성 파일이 있습니다.

이 예제에서는 이전 **lsblk** 명령 **rootvg-rootlv의** 출력을 탑재하는 올바른 **루트** LV이며 다음 명령에서 사용할 수 있습니다.

다음 명령의 출력에는 **루트** LV에 대해 마운트할 경로가 표시됩니다.

`pvdisplay -m | grep -i rootlv`

![루트LV](./media/chroot-logical-volume-manager/locate-rootlv.png)

디렉토리 / 구조에이 장치를 탑재 진행

`mount /dev/rootvg/rootlv /rescue`

/rescue/boot에 **부팅 플래그가** 설정된 파티션마운트

`
mount /dev/sdc1 /rescue/boot
`

**이제 lsblk** 명령을 사용하여 연결된 디스크의 파일 시스템이 올바르게 마운트되었는지 확인합니다.

![실행 lsblk](./media/chroot-logical-volume-manager/lsblk-output-1.png)

또는 **df -Th** 명령

![Df](./media/chroot-logical-volume-manager/df-output.png)

## <a name="gaining-chroot-access"></a>chroot 액세스 권한 얻기

이득 **chroot** 액세스, 다양 한 수정을 수행할 수 있도록 합니다., 각 리눅스 배포판에 대 한 약간의 변화 존재.

```
 cd /rescue
 mount -t proc proc proc
 mount -t sysfs sys sys/
 mount -o bind /dev dev/
 mount -o bind /dev/pts dev/pts/
 chroot /rescue
```

다음과 같은 오류가 발생한 경우:

**chroot: 명령 '/bin/bash'를 실행하지 못했습니다: 이러한 파일이나 디렉토리가 없습니다.**

**usr** 논리 볼륨을 마운트하려고 시도

`
mount  /dev/mapper/rootvg-usrlv /rescue/usr
`

> [!TIP]
> **chroot** 환경에서 명령을 실행할 때 로컬 **구조** VM이 아니라 연결된 OS 디스크에 대해 실행됩니다. 

명령을 사용하여 소프트웨어를 설치, 제거 및 업데이트할 수 있습니다. 오류를 수정하기 위해 VM 문제를 해결합니다.


lsblk 명령을 실행하고 /rescue는 지금 / / / 구조 ![/ 부팅 / 부팅 Chrooted입니다](./media/chroot-logical-volume-manager/chrooted.png)

## <a name="perform-fixes"></a>수정 수행

### <a name="example-1---configure-the-vm-to-boot-from-a-different-kernel"></a>예제 1 - VM이 다른 커널에서 부팅하도록 구성

일반적인 시나리오는 현재 설치된 커널이 손상되었거나 업그레이드가 올바르게 완료되지 않았기 때문에 이전 커널에서 VM을 부팅하도록 하는 것입니다.


```
cd /boot/grub2

grep -i linux grub.cfg

grub2-editenv list

grub2-set-default "CentOS Linux (3.10.0-1062.1.1.el7.x86_64) 7 (Core)"

grub2-editenv list

grub2-mkconfig -o /boot/grub2/grub.cfg
```

*연습*

**grep** 명령은 **grub.cfg가** 알고 있는 커널을 나열합니다.
![커널](./media/chroot-logical-volume-manager/kernels.png)

**grub2-editenv 목록** 표시 어떤 커널 다음 ![부팅 커널 기본에 로드 됩니다.](./media/chroot-logical-volume-manager/kernel-default.png)

**grub2-set-default는** 다른 커널 ![Grub2 세트로 변경하는 데 사용됩니다.](./media/chroot-logical-volume-manager/grub2-set-default.png)

**grub2-editenv** 목록 표시 어떤 커널 다음 ![부팅 새 커널에 로드 됩니다.](./media/chroot-logical-volume-manager/kernel-new.png)

**grub2-mkconfig** 는 Grub2 mkconfig필요한 ![버전을 사용하여 grub.cfg를 재구축합니다.](./media/chroot-logical-volume-manager/grub2-mkconfig.png)



### <a name="example-2---upgrade-packages"></a>예제 2 - 패키지 업그레이드

실패한 커널 업그레이드는 VM을 부팅할 수 없게 만들 수 있습니다.
패키지를 제거하거나 다시 설치할 수 있도록 모든 논리 볼륨 마운트

**lvs** 명령을 실행하여 장착할 수 있는 **LV를** 확인하고 마이그레이션되었거나 다른 클라우드 공급자에서 온 모든 VM은 구성에 따라 다릅니다.

필요한 **LV를** 장착하는 **chroot** 환경을 종료합니다.

![고급](./media/chroot-logical-volume-manager/advanced.png)

이제 실행하여 **chroot** 환경에 다시 액세스

`chroot /rescue`

모든 LV는 마운트 된 파티션으로 표시되어야합니다.

![고급](./media/chroot-logical-volume-manager/chroot-all-mounts.png)

설치된 **커널** 쿼리

![고급](./media/chroot-logical-volume-manager/rpm-kernel.png)

필요한 경우 **커널**
![어드밴스드를 제거하거나 업그레이드해야 합니다.](./media/chroot-logical-volume-manager/rpm-remove-kernel.png)


### <a name="example-3---enable-serial-console"></a>예 3 - 직렬 콘솔 사용
Azure 시리얼 콘솔에 액세스할 수 없는 경우 Linux VM에 대한 GRUB 구성 매개 변수를 확인하고 수정합니다. 자세한 내용은 [이 문서에서](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-grub-proactive-configuration) 찾을 수 있습니다.

### <a name="example-4---kernel-loading-with-problematic-lvm-swap-volume"></a>예제 4 - 문제가 있는 LVM 스왑 볼륨이 있는 커널 로딩

VM이 완전히 부팅되지 못하고 **dracut** 프롬프트에 떨어질 수 있습니다.
오류에 대한 자세한 내용은 Azure 직렬 콘솔에서 찾거나 Azure portal -> 부팅 진단 -> 직렬 로그로 이동할 수 있습니다.


이와 유사한 오류가 있을 수 있습니다.

```
[  188.000765] dracut-initqueue[324]: Warning: /dev/VG/SwapVol does not exist
         Starting Dracut Emergency Shell...
Warning: /dev/VG/SwapVol does not exist
```

이 예제에서는 **rd.lvm.lv=VG/SwapVol의** 이름으로 LV를 로드하도록 grub.cfg가 구성되었으며 VM이 이를 찾을 수 없습니다. 이 줄은 LV SwapVol을 참조하여 커널이 로드되는 방식을 보여 주며

```
[    0.000000] Command line: BOOT_IMAGE=/vmlinuz-3.10.0-1062.4.1.el7.x86_64 root=/dev/mapper/VG-OSVol ro console=tty0 console=ttyS0 earlyprintk=ttyS0 net.ifnames=0 biosdevname=0 crashkernel=256M rd.lvm.lv=VG/OSVol rd.lvm.lv=VG/SwapVol nodmraid rhgb quiet
[    0.000000] e820: BIOS-provided physical RAM map:
```

 /etc/기본/grub 구성에서 잘못된 LV를 제거하고 grub2.cfg를 다시 빌드합니다.


## <a name="exit-chroot-and-swap-the-os-disk"></a>chroot를 종료하고 OS 디스크를 교체

문제를 복구한 후 복구 VM에서 디스크마운트를 분리하고 분리하여 영향을 받는 VM OS 디스크로 교체할 수 있습니다.

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

구조 VM에서 디스크를 분리하고 디스크 스왑을 수행합니다.

포털 **디스크에서 VM을** 선택하고 **detach**
![분리 디스크를 선택합니다.](./media/chroot-logical-volume-manager/detach-disk.png) 

변경 ![내용 저장 분리 저장](./media/chroot-logical-volume-manager/save-detach.png) 

이제 영향을 받는 VM의 원래 OS 디스크로 교환할 수 있도록 디스크를 사용할 수 있게 됩니다.

Azure 포털에서 실패한 VM으로 이동하여 **디스크** -> **스왑 OS 디스크**
![스왑 디스크를 선택합니다.](./media/chroot-logical-volume-manager/swap-disk.png) 

**디스크 선택** 필드 완료는 이전 단계에서 분리된 스냅샷 디스크입니다. 영향을 받는 VM의 VM 이름도 필요한 다음 **확인을** 선택합니다.

![새 OS 디스크](./media/chroot-logical-volume-manager/new-osdisk.png) 

VM이 디스크 스왑을 실행 중이면 디스크 스왑 작업이 완료되면 VM을 재부팅합니다.


## <a name="next-steps"></a>다음 단계
자세한 정보

 [Azure 직렬 콘솔]( https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-linux)

[단일 사용자 모드](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-grub-single-user-mode)
