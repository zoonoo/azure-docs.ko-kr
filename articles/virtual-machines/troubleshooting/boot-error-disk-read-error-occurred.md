---
title: 부팅 오류 문제 해결 - 디스크 읽기 오류 발생
description: 이 문서에서는 Azure VM에서 디스크를 읽을 수 없는 문제를 해결하는 단계를 제공합니다.
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: 817c9c5c-6a81-4b42-a6ad-0a0a11858b84
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 06/01/2020
ms.author: v-miegge
ms.openlocfilehash: f59903ed111be1fe414f4b3ded250d754c91d323
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/23/2020
ms.locfileid: "87069152"
---
# <a name="troubleshoot-boot-error---disk-read-error-occurred"></a>부팅 오류 문제 해결 - 디스크 읽기 오류 발생

이 문서에서는 Azure VM(가상 머신)에서 디스크를 읽을 수 없는 문제를 해결하는 단계를 제공합니다.

## <a name="symptoms"></a>증상

[부팅 진단](./boot-diagnostics.md)을 사용하여 VM의 스크린샷을 보면 스크린샷에 다음 메시지와 함께 프롬프트가 표시됨을 확인할 수 있습니다. "디스크 읽기 오류가 발생했습니다. Ctrl+Alt+Del 키를 눌러 다시 시작하세요" 오류 코드를 반영합니다.

   ![오류 메시지: 디스크 읽기 오류가 발생했습니다. Ctrl+Alt+Del 키를 눌러 다시 시작하세요.](./media/disk-read-error-occurred/1.png)

## <a name="cause"></a>원인

이 오류 메시지는 디스크 구조가 손상되어 읽을 수 없음을 나타냅니다. 1세대 VM을 사용하는 경우 부팅 구성 데이터가 포함된 디스크 파티션이 **Active**로 설정되어 있지 않을 수도 있습니다.

## <a name="solution"></a>해결 방법

### <a name="process-overview"></a>프로세스 개요

1. 복구 VM을 만들고 액세스합니다.
1. 다음과 같은 솔루션을 선택합니다.
   - [파티션 상태를 Active(활성)로 설정](#set-partition-status-to-active)
   - [디스크 파티션 수정](#fix-the-disk-partition)
1. 직렬 콘솔과 메모리 덤프 수집을 사용하도록 설정합니다.
1. VM을 다시 빌드합니다.

> [!NOTE]
> 이 부팅 오류가 발생하면 게스트 OS(운영 체제)가 작동하지 않습니다. 문제를 해결하기 위해 오프라인 모드에서 문제 해결을 진행하세요.

### <a name="create-and-access-a-repair-vm"></a>복구 VM 만들기 및 액세스

1. [VM 복구 명령](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md)의 1 ~ 3단계를 사용하여 복구 VM을 준비합니다.
1. 원격 데스크톱 연결을 사용하여 복구 VM에 연결합니다.

### <a name="set-partition-status-to-active"></a>파티션 상태를 Active(활성)로 설정

1세대 VM은 먼저 BCD 저장소를 포함하는 OS 파티션이 **Active**로 표시되어 있는지 확인해야 합니다. 2세대 VM이 있는 경우 이후 세대에서 상태 플래그가 더 이상 사용되지 않으므로 [디스크 파티션 수정](#fix-the-disk-partition)으로 건너뜁니다.

1. 관리자 권한 명령 프롬프트(cmd.exe)를 엽니다.
1. **diskpart**를 입력하고 **DISKPART** 도구를 시작합니다.
1. **list disk**를 입력하여 시스템의 디스크를 나열하고 연결된 OS VHD(가상 하드 디스크)를 확인합니다.
1. 연결된 OS VHD를 찾으면 **sel disk #** 을 입력하여 해당 디스크를 선택합니다. Disk 1이 연결된 OS VHD인 경우의 예는 다음 이미지를 참조하세요.

   ![표에 Disk 0과 Disk 1이 있고, **list disk** 명령의 출력이 표시된 diskpart 창. 이 창에는 **sel disk 1** 명령의 출력도 표시됩니다. 여기에서 Disk 1이 선택된 디스크입니다.](./media/disk-read-error-occurred/2.png)

1. 디스크가 선택되면 **list partition**을 입력하여 선택한 디스크의 파티션을 나열합니다.
1. 부팅 파티션이 식별되면 **sel partition #** 을 입력하여 파티션을 선택합니다. 부팅 파티션의 크기는 대개 약 350MB입니다.  Partition 1이 부팅 파티션인 경우의 예는 다음 이미지를 참조하세요.

   ![표에 Partition 1과 Partition 2가 있고, **list partition** 명령 출력이 표시된 diskpart 창. 이 창에는 **sel partition 1** 명령의 출력도 표시됩니다. 여기에서 Partition 1이 선택된 디스크입니다.](./media/disk-read-error-occurred/3.png)

1. 파티션 상태를 확인하려면 **detail partition**을 입력합니다. Partition이 **Active: No** 또는 **Active: Yes**로 설정된 파티션의 예는 다음 스크린샷을 참조하세요.

   **Active: No**

   ![Partition 1이 **Active: No**로 설정되어 있고 **detail partition** 명령의 출력이 표시된 diskpart 창.](./media/disk-read-error-occurred/4.png)

   **Active: Yes**

   ![Partition 1이 **Active: Yes**로 설정되어 있고 **detail partition** 명령의 출력이 표시된 diskpart 창.](./media/disk-read-error-occurred/5.png)

1. 파티션이 **Active**로 설정되어 있지 않은 경우 **active**를 입력하고 활성 플래그를 변경합니다.
1. **detail partition**을 입력하여 상태 변경이 제대로 완료되었는지 확인하고 출력에 **Active: Yes**로 설정된 파티션의 예는 다음 스크린샷을 참조하세요. 
1. **exit**를 입력하여 DISKPART 도구를 닫고 구성 변경 내용을 저장합니다.

### <a name="fix-the-disk-partition"></a>디스크 파티션 수정

1. 관리자 권한 명령 프롬프트(cmd.exe)를 엽니다.
1. 다음 명령을 사용하여 디스크에서 **CHKDSK**를 실행하고 오류 수정 작업을 수행합니다.

   `chkdsk <DRIVE LETTER>: /f`

   **/f** 명령 옵션을 추가하면 디스크의 모든 오류가 수정됩니다. **<DRIVE LETTER>** 를 연결된 OS VHD의 문자로 바꿔야 합니다.

### <a name="enable-the-serial-console-and-memory-dump-collection"></a>직렬 콘솔과 메모리 덤프 수집 사용

**권장**: VM을 다시 빌드하기 전에 다음 스크립트를 실행하여 직렬 콘솔과 메모리 덤프 수집을 사용하도록 설정합니다.

1. 관리자 권한 명령 프롬프트 세션을 엽니다.
1. 다음 명령을 실행합니다.

   **직렬 콘솔을 사용하도록 설정합니다.**
   
   ```
   bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /ems {<BOOT LOADER IDENTIFIER>} ON 
   bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200
   ```

1. OS 디스크의 사용 가능한 공간이 VM의 메모리 크기(RAM)보다 큰지 확인합니다.

   OS 디스크에 공간이 부족한 경우 메모리 덤프 파일이 생성될 위치를 변경하고 사용 가능한 공간이 충분한 VM에 연결된 데이터 디스크의 위치를 참조합니다. 이 위치를 변경하려면 다음 명령에서 **%SystemRoot%** 를 데이터 디스크의 드라이브 문자(예: **F:** )로 바꿉니다.

   OS 덤프를 사용하도록 설정하기 위한 권장 구성:

   **손상된 OS 디스크에서 레지스트리 하이브를 로드합니다.**

   ```
   REG LOAD HKLM\BROKENSYSTEM <VOLUME LETTER OF BROKEN OS DISK>:\windows\system32\config\SYSTEM
   ```

   **ControlSet001에서 사용하도록 설정합니다.**

   ```
   REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f 
   REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f 
   REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f 
   ```

   **ControlSet002에서 사용하도록 설정합니다.**

   ```
   REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f 
   REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f 
   REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f 
   ```

   **손상된 OS 디스크를 언로드합니다.**

   ```
   REG UNLOAD HKLM\BROKENSYSTEM
   ```
   
### <a name="rebuild-the-vm"></a>VM 다시 빌드

[VM 복구 명령의 5단계](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md#repair-process-example)를 사용하여 VM을 다시 빌드합니다.
