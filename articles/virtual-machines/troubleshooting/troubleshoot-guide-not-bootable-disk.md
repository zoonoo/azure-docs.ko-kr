---
title: 부팅 오류 – "부팅 가능한 디스크가 아닙니다."
description: 이 문서에서는 Azure 가상 컴퓨터에서 디스크를 부팅할 수 없는 문제를 해결하는 단계를 제공합니다.
services: virtual-machines-windows
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: 5d6db4e3-c2f5-40c7-afea-54edf745f5eb
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.topic: troubleshooting
ms.date: 03/25/2020
ms.author: v-mibufo
ms.openlocfilehash: 9f0c6350b89dcfecefcadcc166f7af35abc4b128
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80300980"
---
# <a name="boot-error--this-is-not-a-bootable-disk"></a>부팅 오류 - 부팅 가능한 디스크가 아닙니다.

이 문서에서는 Azure 가상 시스템(VM)에서 디스크를 부팅할 수 없는 문제를 해결하는 단계를 제공합니다.

## <a name="symptoms"></a>증상

[부팅 진단을](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/boot-diagnostics) 사용하여 VM의 스크린샷을 볼 때 스크린샷에 '부팅 가능한 디스크가 아닙니다.'라는 메시지가 표시됩니다. 부팅 가능한 플로피를 삽입하고 키를 눌러 다시 시도하십시오...'.

   그림 1

   ![그림 1은 *"부팅 가능한 디스크가 아닙니다. 부팅 가능한 플로피를 삽입하고 키를 눌러 다시 시도하십시오..."*](media/troubleshoot-guide-not-bootable-disk/1.jpg)

## <a name="cause"></a>원인

이 오류 메시지는 OS 부팅 프로세스가 활성 시스템 파티션을 찾을 수 없다는 것을 의미합니다. 이 오류는 BCD(부팅 구성 데이터) 저장소에 누락된 참조가 있어 Windows 파티션을 찾을 수 없다는 의미일 수도 있습니다.

## <a name="solution"></a>해결 방법

### <a name="process-overview"></a>프로세스 개요

1. 복구 VM을 만들고 액세스합니다.
2. 파티션 상태를 활성으로 설정합니다.
3. 디스크 파티션을 수정합니다.
4. **권장**: VM을 다시 빌드하기 전에 직렬 콘솔 및 메모리 덤프 컬렉션을 사용하도록 설정합니다.
5. 원래 VM을 다시 빌드합니다.

   > [!NOTE]
   > 이 부팅 오류가 발생하면 게스트 OS가 작동하지 않습니다. 이 문제를 해결 하려면 오프라인 모드에서 문제 해결 됩니다.

### <a name="create-and-access-a-repair-vm"></a>복구 VM 만들기 및 액세스

1. VM 복구 명령의 1-3단계를 사용하여 복구 [VM을 준비합니다.](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands)
2. 원격 데스크톱 연결을 사용하여 복구 VM에 연결합니다.

### <a name="set-partition-status-to-active"></a>파티션 상태를 활성으로 설정

1세대 VM은 먼저 BCD 저장소를 보유하는 OS 파티션이 *활성으로*표시되는지 확인해야 합니다. 2세대 VM이 있는 경우 상태 플래그가 이후 세대에서 더 이상 사용되지 않도록 [디스크 파티션 수정을](#fix-the-disk-partition)위해 미리 건너뜁니다. *Status*

1. 상승된 명령 *프롬프트(cmd.exe)를*엽니다.
2. *DISKPART* 도구를 실행하려면 디스크 부품을 입력합니다.
3. *목록 디스크를* 입력하여 시스템에 디스크를 나열하고 연결된 OS VHD를 식별합니다.
4. 연결된 OS VHD가 있으면 *sel 디스크 #을* 입력하여 디스크를 선택합니다.  디스크 1이 연결된 OS VHD인 그림 2를 참조하십시오.

   그림 2

   ![그림 2는 테이블에 표시된 목록 디스크 명령, 디스크 0 및 디스크 1의 출력을 보여 주며 *DISKPART* 창을 보여 주며 있습니다.  또한 sel 디스크 1 명령의 출력을 표시하고, 디스크 1은 선택한 디스크입니다.](media/troubleshoot-guide-not-bootable-disk/2.jpg)

5. 디스크를 선택하면 목록 *파티션을* 입력하여 선택한 디스크의 파티션을 나열합니다.
6. 부팅 파티션이 식별되면 *sel 파티션 #을* 입력하여 파티션을 선택합니다.  일반적으로 부팅 파티션의 크기는 약 350MB입니다.  파티션 1이 부팅 파티션인 그림 3을 참조하십시오.

   그림 3

   ![그림 3은 *DISKPART* 창을 *목록 파티션* 명령의 출력과 함께 보여줍니다. 파티션 1과 파티션 2가 테이블에 표시됩니다. 또한 파티션 1이 선택한 디스크인 경우 *sel 파티션 1* 명령의 출력을 표시합니다.](media/troubleshoot-guide-not-bootable-disk/3.jpg)

7. '세부 파티션'을 입력하여 파티션의 상태를 확인합니다. 파티션이 활성 상태인 그림 *4: 아니요,* 파티션이 '활성: 예'인 그림 5를 참조하십시오.

   그림 4

   ![그림 4는 파티션 1이 *활성으로 설정된 경우 *DETAIL 파티션* 명령의 출력이 있는 *DISKPART* 창을 보여 주며,](media/troubleshoot-guide-not-bootable-disk/4.jpg)

   그림 5

   ![그림 5는 파티션 1이 *활성: 예*로 설정된 경우 *세부 파티션* 명령의 출력이 있는 *DISKPART* 창을 보여 주었습니다.](media/troubleshoot-guide-not-bootable-disk/5.jpg)

8. 파티션이 **활성**상태가 아닌 경우 *활성을* 입력하여 활성 플래그를 *변경합니다.*
9. *세부 정보 파티션을*입력하여 상태 변경이 제대로 수행되지 않은지 확인합니다.

   그림 6

   ![그림 6은 파티션 1이 *활성으로 설정된 경우 *detail 파티션* 명령의 출력이 있는 diskpart 창을 보여 주며, 예*](media/troubleshoot-guide-not-bootable-disk/6.jpg)

10. *종료를* 입력하여 DISKPART 도구를 닫고 구성 변경 내용을 저장합니다.

### <a name="fix-the-disk-partition"></a>디스크 파티션 수정

1. 상승된 명령 프롬프트(cmd.exe)를 엽니다.
2. 다음 명령을 사용하여 디스크에서 *CHKDSK를* 실행하고 오류를 수정합니다.

   `chkdsk <DRIVE LETTER>: /f`

   '/f' 명령 옵션을 추가하면 디스크의 오류가 해결됩니다. 첨부된 OS <DRIVE LETTER> VHD의 문자로 교체해야 합니다.

### <a name="recommended-before-you-rebuild-the-vm-enable-serial-console-and-memory-dump-collection"></a>권장: VM을 다시 빌드하기 전에 직렬 콘솔 및 메모리 덤프 컬렉션을 사용하도록 설정합니다.

메모리 덤프 컬렉션 및 직렬 콘솔을 사용하려면 다음 스크립트를 실행합니다.

1. 높은 명령 프롬프트 세션을 엽니다(관리자로 실행).
2. 다음 명령을 실행합니다.

   직렬 콘솔 사용

   `bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /ems {<BOOT LOADER IDENTIFIER>} ON`

   `bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200`

3. OS 디스크의 사용 가능한 공간이 VM의 RAM(메모리 크기)만큼인지 확인합니다.

   OS 디스크에 공간이 충분하지 않으면 메모리 덤프 파일이 생성될 위치를 변경하고 여유 공간이 충분한 VM에 연결된 데이터 디스크를 참조해야 합니다. 위치를 변경하려면 아래 명령의 데이터 디스크의 드라이브 문자(예: "F:")로 "%SystemRoot%"를 바꿉니다.

#### <a name="suggested-configuration-to-enable-os-dump"></a>OS 덤프를 사용하도록 설정하는 권장 구성

**로드 깨진 OS 디스크**:

`REG LOAD HKLM\BROKENSYSTEM <VOLUME LETTER OF BROKEN OS DISK>:\windows\system32\config\SYSTEM`

**제어Set001에서 사용:**

`REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f`

`REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f`

`REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f`

**제어Set002에서 사용:**

`REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f`

`REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f`

`REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f`

**깨진 OS 디스크언지 언로드:**

`REG UNLOAD HKLM\BROKENSYSTEM`

### <a name="rebuild-the-original-vm"></a>원래 VM 다시 빌드

[VM 복구 명령의 5단계를](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands#repair-process-example) 사용하여 VM을 다시 어셈블합니다.
