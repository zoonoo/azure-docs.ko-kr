---
title: Azure VM에서 로컬 Linux 암호를 다시 설정하는 방법 | Microsoft Docs
description: Azure VM에서 로컬 Linux 암호를 다시 설정하는 단계 소개
services: virtual-machines-linux
documentationcenter: ''
author: Deland-Han
manager: dcscontentpm
editor: ''
tags: ''
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: troubleshooting
ms.date: 08/20/2019
ms.author: delhan
ms.openlocfilehash: c6bfd5b9ff3626593916533f27c5c2755cebcb13
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "87028484"
---
# <a name="how-to-reset-local-linux-password-on-azure-vms"></a>Azure VM에서 로컬 Linux 암호를 다시 설정하는 방법

이 문서에서는 로컬 Linux VM(Virtual Machine) 암호를 다시 설정하는 여러 가지 방법을 소개합니다. 사용자 계정이 만료되거나 새 계정을 만들려는 경우 다음 메서드를 사용하여 새 로컬 관리자 계정을 만들거나 VM에 대한 액세스를 다시 얻을 수 있습니다.

## <a name="symptoms"></a>증상

VM에 로그인할 수 없다면 사용한 암호가 잘못되었음을 나타내는 메시지가 표시됩니다. 또한 VMAgent를 사용하여 Azure Portal에서 암호를 다시 설정할 수 없습니다.

## <a name="manual-password-reset-procedure"></a>수동 암호 다시 설정 프로시저

> [!NOTE]
> 다음 단계는 관리 되지 않는 디스크가 있는 VM에는 적용 되지 않습니다.

1. 영향을 받는 VM의 OS 디스크에 대 한 스냅숏을 만들고 스냅숏에서 디스크를 만든 다음 문제 해결 VM에 디스크를 연결 합니다. 자자세한 내용은 [Azure Portal을 사용하여 OS 디스크를 복구 VM에 연결함으로써 Windows VM 문제 해결](troubleshoot-recovery-disks-portal-linux.md)을 참조하세요.

2. 원격 데스크톱을 사용하여 문제 해결 VM에 연결합니다.

3.  문제 해결 VM에서 다음 SSH 명령을 실행 하 여 슈퍼 사용자가 될 수 있습니다.

    ```bash
    sudo su
    ```

4.  **fdisk -l**을 실행하거나 시스템 로그를 확인하여 새로 연결된 디스크를 찾습니다. 탑재할 드라이브 이름을 찾습니다. 그런 다음 임시 VM에서 관련 로그 파일에서 찾습니다.

    ```bash
    grep SCSI /var/log/kern.log (ubuntu)
    grep SCSI /var/log/messages (centos, suse, oracle)
    ```

    다음은 grep 명령의 예제 출력입니다.

    ```bash
    kernel: [ 9707.100572] sd 3:0:0:0: [sdc] Attached SCSI disk
    ```

5.  **tempmount**라는 탑재 지점을 만듭니다.

    ```bash
    mkdir /tempmount
    ```

6.  탑재 지점에 OS 디스크를 탑재합니다. 일반적으로 *sdc1* 또는 *sdc2*를 탑재 해야 합니다. 이는 손상 된 컴퓨터 디스크에서 */sing* 디렉터리의 호스팅 파티션에 따라 달라 집니다.

    ```bash
    mount /dev/sdc1 /tempmount
    ```

7.  변경하기 전에 핵심 자격 증명 파일의 복사본을 만듭니다.

    ```bash
    cp /etc/passwd /etc/passwd_orig    
    cp /etc/shadow /etc/shadow_orig    
    cp /tempmount/etc/passwd /etc/passwd
    cp /tempmount/etc/shadow /etc/shadow 
    cp /tempmount/etc/passwd /tempmount/etc/passwd_orig
    cp /tempmount/etc/shadow /tempmount/etc/shadow_orig
    ```

8.  필요한 사용자 암호를 다시 설정합니다.

    ```bash
    passwd <<USER>> 
    ```

9.  손상된 컴퓨터의 디스크의 올바른 위치에 수정된 파일을 이동합니다.

    ```bash
    cp /etc/passwd /tempmount/etc/passwd
    cp /etc/shadow /tempmount/etc/shadow
    cp /etc/passwd_orig /etc/passwd
    cp /etc/shadow_orig /etc/shadow
    ```

10. 루트로 다시 이동하여 디스크를 분리합니다.

    ```bash
    cd /
    umount /tempmount
    ```

11. Azure Portal에서 문제 해결 VM에서 디스크를 분리합니다.

12. [영향을 받는 VM용 OS 디스크를 변경합니다](troubleshoot-recovery-disks-portal-linux.md#swap-the-os-disk-for-the-vm).

## <a name="next-steps"></a>다음 단계

* [OS 디스크를 다른 Azure VM에 연결하여 Azure VM 문제 해결](https://social.technet.microsoft.com/wiki/contents/articles/18710.troubleshoot-azure-vm-by-attaching-os-disk-to-another-azure-vm.aspx)

* [Azure CLI: VHD에서 VM을 삭제하고 다시 배포하는 방법](/archive/blogs/linuxonazure/azure-cli-how-to-delete-and-re-deploy-a-vm-from-vhd)
