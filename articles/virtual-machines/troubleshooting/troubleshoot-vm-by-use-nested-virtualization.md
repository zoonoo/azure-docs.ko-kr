---
title: Azure에서 중첩된 가상화를 사용하여 문제 Azure VM 해결 | Microsoft Docs
description: Azure에서 중첩된 가상화를 사용하여 문제 Azure VM을 해결하는 방법
services: virtual-machines-windows
documentationcenter: ''
author: glimoli
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 11/19/2019
ms.author: genli
ms.openlocfilehash: 4565eb86727e768ba894d701cbc5e0073c07ee01
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/19/2019
ms.locfileid: "74185515"
---
# <a name="troubleshoot-a-problem-azure-vm-by-using-nested-virtualization-in-azure"></a>Azure에서 중첩된 가상화를 사용하여 문제 Azure VM 해결

이 문서에서는 문제 해결을 위해 Hyper-V 호스트(복구 VM)에서 문제 VM의 디스크를 탑재할 수 있도록 Microsoft Azure에서 중첩된 가상화 환경을 만드는 방법을 보여 줍니다.

## <a name="prerequisites"></a>선행 조건

문제 VM을 탑재하려면 복구 VM에서 다음 필수 구성 요소를 충족해야 합니다.

-   복구 VM은 문제 VM과 동일한 위치에 있어야 합니다.

-   복구 VM은 문제 VM과 동일한 리소스 그룹에 있어야 합니다.

-   복구 VM은 문제 VM과 동일한 유형의 스토리지 계정(표준 또는 프리미엄)을 사용해야 합니다.

## <a name="step-1-create-a-rescue-vm-and-install-hyper-v-role"></a>1단계: 복구 VM 만들기 및 Hyper-V 역할 설치

1.  새 복구 VM을 만듭니다.

    -  운영 체제: Windows Server 2016 Datacenter

    -  크기: 중첩된 가상화를 지원하는 최소 두 개의 코어를 가진 V3 시리즈 자세한 내용은 [새 Dv3 및 Ev3 VM 크기 소개](https://azure.microsoft.com/blog/introducing-the-new-dv3-and-ev3-vm-sizes/)를 참조하세요.

    -  문제 VM과 동일한 위치, 스토리지 계정 리소스 그룹입니다.

    -  문제 VM과 동일한 스토리지 유형(표준 또는 프리미엄)을 선택합니다.

2.  복구 VM을 만든 후 복구 VM에 데스크톱을 원격 연결합니다.

3.  서버 관리자에서 **관리** > **역할 및 기능 추가**를 선택합니다.

4.  **설치 유형** 섹션에서 **역할 기반 또는 기능 기반 설치**를 선택합니다.

5.  **대상 서버 선택** 섹션에서 복구 VM이 선택되어 있는지 확인합니다.

6.  **Hyper-V 역할** > **기능 추가**를 선택합니다.

7.  **기능** 섹션에서 **다음**을 선택합니다.

8.  가상 스위치를 사용할 수 있는 경우 선택합니다. 그렇지 않은 경우 **다음**을 선택합니다.

9.  **마이그레이션** 섹션에서 **다음**을 선택합니다.

10. **기본 저장소** 섹션에서 **다음**을 선택합니다.

11. 확인란을 선택하여 필요한 경우 서버를 자동으로 다시 시작합니다.

12. **설치**를 선택합니다.

13. 서버가 Hyper-V 역할을 설치하도록 허용합니다. 몇 분 정도 걸리며 서버가 자동으로 재부팅됩니다.

## <a name="step-2-create-the-problem-vm-on-the-rescue-vms-hyper-v-server"></a>2단계: 복구 VM의 Hyper-V 서버에서 문제 VM 만들기

1.  문제가 있는 VM의 OS 디스크에 대 한 [스냅숏 디스크를 만든](troubleshoot-recovery-disks-portal-windows.md#take-a-snapshot-of-the-os-disk) 다음 스냅숏 디스크를 연결 사용 VM에 연결 합니다.

2.  복구 VM에 대 한 원격 데스크톱

3.  디스크 관리(diskmgmt.msc)를 엽니다. 문제 VM의 디스크가 **오프라인**으로 설정되어 있는지 확인합니다.

4.  Hyper-V 관리자 열기: **서버 관리자**에서 **Hyper-V 역할**을 선택합니다. 서버를 마우스 오른쪽 단추로 클릭한 다음 **Hyper-V 관리자**를 선택합니다.

5.  Hyper-V 관리자에서 복구 VM을 마우스 오른쪽 단추로 클릭한 다음 **새로 만들기** > **Virtual Machine** > **다음**을 선택합니다.

6.  VM에 대한 이름을 입력한 후 **다음**을 선택합니다.

7.  **1세대**를 선택합니다.

8.  1024MB 이상에서 시작 메모리를 설정합니다.

9. 해당하는 경우 생성된 Hyper-V 네트워크 스위치를 선택합니다. 그렇지 않으면 다음 페이지로 이동합니다.

10. **나중에 가상 하드 디스크 연결**을 선택합니다.

    ![나중에 가상 하드 디스크 연결 옵션에 대한 이미지](media/troubleshoot-vm-by-use-nested-virtualization/attach-disk-later.png)

11. VM이 만들어진 경우 **마침**을 선택합니다.

12. 만든 VM을 마우스 오른쪽 단추로 클릭한 다음 **설정**을 선택합니다.

13. **IDE 컨트롤러 0**을 선택하고, **하드 드라이브**를 선택한 다음 **추가**를 클릭합니다.

    ![새 하드 드라이브 추가에 대한 이미지](media/troubleshoot-vm-by-use-nested-virtualization/create-new-drive.png)    

14. **실제 하드 디스크**에서 Azure VM에 연결한 문제 VM의 디스크를 선택합니다. 디스크가 나열되지 않으면 디스크 관리를 사용하여 디스크가 오프라인으로 설정되어 있는지 확인합니다.

    ![이미지 탑재에 대한 이미지](media/troubleshoot-vm-by-use-nested-virtualization/mount-disk.png)  


15. **적용**을 선택한 다음 **확인**을 선택합니다.

16. VM을 두 번 클릭한 다음 시작합니다.

17. 이제 온-프레미스 VM으로 VM을 사용할 수 있습니다. 필요한 문제 해결 단계를 따를 수 있습니다.

## <a name="step-3-replace-the-os-disk-used-by-the-problem-vm"></a>3 단계: 문제 VM에서 사용 하는 OS 디스크 교체

1.  VM을 온라인으로 다시 가져온 후 Hyper-V 관리자에서 VM을 종료합니다.

2.  [복구 된 OS 디스크를 분리 하 고 분리](troubleshoot-recovery-disks-portal-windows.md#unmount-and-detach-original-virtual-hard-disk
)합니다.
3.  [VM에서 사용 하는 os 디스크를 복구 된 os 디스크로 바꿉니다](troubleshoot-recovery-disks-portal-windows.md#swap-the-os-disk-for-the-vm
).

## <a name="next-steps"></a>다음 단계

VM에 연결하는 데 문제가 있는 경우 [Azure VM에 RDP 연결 문제 해결](troubleshoot-rdp-connection.md)을 참조하세요. VM에서 실행 중인 애플리케이션에 액세스하는 데 문제가 있는 경우 [Windows VM에서 애플리케이션 연결 문제 해결](troubleshoot-app-connection.md)을 참조하세요.
