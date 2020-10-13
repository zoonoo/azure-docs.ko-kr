---
title: 부팅 오류 – "부팅 디스크가 아닙니다."
description: 이 문서에서는 Azure 가상 머신에서 디스크를 부팅할 수 없는 문제를 해결 하는 단계를 제공 합니다.
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
ms.openlocfilehash: 16f6919577955bda5b04db26deb9fe78a467e364
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "86509038"
---
# <a name="boot-error--this-is-not-a-bootable-disk"></a>부팅 오류 – 부팅 가능한 디스크가 아닙니다.

이 문서에서는 Azure VM (가상 머신)에서 디스크를 부팅할 수 없는 문제를 해결 하는 단계를 제공 합니다.

## <a name="symptoms"></a>증상

[부팅 진단을](./boot-diagnostics.md) 사용 하 여 VM의 스크린샷을 볼 때 스크린 샷에서는 ' 부팅 디스크가 아닙니다. ' 라는 메시지가 표시 되는 것을 볼 수 있습니다. 부팅 가능한 플로피를 삽입 하 고 아무 키나 눌러 다시 시도 하세요.

   그림 1

   ![그림 1에는 "부팅 디스크가 아닙니다." 라는 메시지가 표시 됩니다. 부팅 가능한 플로피를 삽입 하 고 아무 키나 눌러 다시 시도 하세요. "*](media/troubleshoot-guide-not-bootable-disk/1.jpg)

## <a name="cause"></a>원인

이 오류 메시지는 OS 부팅 프로세스에서 활성 시스템 파티션을 찾을 수 없음을 의미 합니다. 이 오류는 부팅 구성 데이터 (BCD) 저장소에 누락 된 참조가 있어 Windows 파티션을 찾을 수 없음을 의미할 수도 있습니다.

## <a name="solution"></a>해결 방법

### <a name="process-overview"></a>프로세스 개요

1. 복구 VM을 만들고 액세스합니다.
2. 파티션 상태를 활성으로 설정 합니다.
3. 디스크 파티션을 수정 합니다.
4. **권장**: VM을 다시 빌드하기 전에 직렬 콘솔과 메모리 덤프 수집을 사용하도록 설정합니다.
5. 원본 VM을 다시 빌드합니다.

   > [!NOTE]
   > 이 부팅 오류가 발생할 경우 게스트 OS가 작동 하지 않습니다. 문제를 해결하기 위해 오프라인 모드에서 문제 해결을 진행하세요.

### <a name="create-and-access-a-repair-vm"></a>복구 VM 만들기 및 액세스

1. [VM 복구 명령](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md)의 1~3단계를 사용하여 복구 VM을 준비합니다.
2. 원격 데스크톱 연결을 사용하여 복구 VM에 연결합니다.

### <a name="set-partition-status-to-active"></a>파티션 상태를 활성으로 설정

1 세대 Vm은 먼저 BCD 저장소를 포함 하는 OS 파티션이 *활성*으로 표시 되는지 확인 해야 합니다. 2 세대 VM이 있는 경우 나중에 생성에서 *상태* 플래그가 더 이상 사용 되지 않으므로 [디스크 파티션 수정](#fix-the-disk-partition)으로 건너뜁니다.

1. 관리자 권한 명령 프롬프트 *(cmd.exe)* 를 엽니다.
2. *diskpart*를 입력하고 DISKPART 도구를 시작합니다.
3. *목록 디스크* 를 입력 하 여 시스템의 디스크를 나열 하 고 연결 된 OS VHD를 확인 합니다.
4. 연결된 OS VHD를 찾으면 *sel disk #* 을 입력하여 해당 디스크를 선택합니다.  그림 2를 참조 하세요. 여기서 Disk 1은 연결 된 OS VHD입니다.

   그림 2

   ![그림 2에는 테이블에 표시 되는 디스크 목록 명령, 디스크 0 및 디스크 1의 출력을 보여 주는 * DISKPART * 창이 표시 됩니다.  또한 sel disk 1 명령의 출력을 표시 하 고 디스크 1은 선택한 디스크를 표시 합니다.](media/troubleshoot-guide-not-bootable-disk/2.jpg)

5. 디스크가 선택되면 *list partition*을 입력하여 선택한 디스크의 파티션을 나열합니다.
6. 부팅 파티션이 식별되면 *sel partition #* 을 입력하여 파티션을 선택합니다.  일반적으로 부팅 파티션은 350 크기가 약입니다.  그림 3을 참조 하십시오. 여기서 파티션 1은 부팅 파티션입니다.

   그림 3

   ![그림 3에는 * 파티션 나열 * 명령의 출력이 포함 된 * DISKPART * 창이 표시 됩니다. 파티션 1과 파티션 2가 테이블에 표시 됩니다. 또한 파티션 1이 선택한 디스크인 경우 * sel 파티션 1 * 명령의 출력을 보여 줍니다.](media/troubleshoot-guide-not-bootable-disk/3.jpg)

7. 파티션 상태를 확인 하려면 ' 정보 파티션 '을 입력 합니다. 파티션이 활성 상태인 그림 4를 참조 하십시오 *. 아니요*또는 그림 5 (파티션이 ' 활성: 예 ' 인 경우).

   그림 4

   ![그림 4에는 파티션 1이 * 활성: 아니요 *로 설정 된 경우 * 정보 파티션 * 명령을 출력 하는 * DISKPART * 창이 표시 됩니다.](media/troubleshoot-guide-not-bootable-disk/4.jpg)

   그림 5

   ![그림 5에서는 파티션 1이 * 활성: 예 *로 설정 된 경우 * 정보 파티션 * 명령을 출력 하는 * DISKPART * 창을 보여 줍니다.](media/troubleshoot-guide-not-bootable-disk/5.jpg)

8. 파티션이 **활성 상태가 아닌** *경우 활성을 입력 하* 여 *활성* 플래그를 변경 합니다.
9. *세부 정보 파티션*을 입력 하 여 상태 변경이 제대로 수행 되었는지 확인 합니다.

   그림 6

   ![그림 6에는 파티션 1이 * 활성: 예 *로 설정 된 경우 * detail partition * 명령을 출력 하는 diskpart 창이 표시 됩니다.](media/troubleshoot-guide-not-bootable-disk/6.jpg)

10. *exit*를 입력하여 DISKPART 도구를 닫고 구성 변경 내용을 저장합니다.

### <a name="fix-the-disk-partition"></a>디스크 파티션 수정

1. 관리자 권한 명령 프롬프트(cmd.exe)를 엽니다.
2. 다음 명령을 사용 하 여 디스크에서 *CHKDSK* 를 실행 하 고 오류를 수정 합니다.

   `chkdsk <DRIVE LETTER>: /f`

   '/F ' 명령 옵션을 추가 하면 디스크의 모든 오류가 수정 됩니다. 을 <DRIVE LETTER> 연결 된 OS VHD의 문자로 바꾸어야 합니다.

### <a name="recommended-before-you-rebuild-the-vm-enable-serial-console-and-memory-dump-collection"></a>권장: VM을 다시 빌드하기 전에 직렬 콘솔과 메모리 덤프 수집을 사용 하도록 설정 합니다.

메모리 덤프 수집 및 직렬 콘솔을 사용 하도록 설정 하려면 다음 스크립트를 실행 합니다.

1. 관리자 권한 명령 프롬프트 세션을 엽니다(관리자 권한으로 실행).
2. 다음 명령을 실행합니다.

   직렬 콘솔 사용

   `bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /ems {<BOOT LOADER IDENTIFIER>} ON`

   `bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200`

3. OS 디스크의 사용 가능한 공간이 VM의 메모리 크기 (RAM)와 동일한 지 확인 합니다.

   OS 디스크에 공간이 부족 한 경우 메모리 덤프 파일이 생성 될 위치를 변경 하 고 사용 가능한 공간이 충분 한 VM에 연결 된 데이터 디스크를 참조 합니다. 위치를 변경 하려면 아래 명령에서 "% SystemRoot%"를 데이터 디스크의 드라이브 문자 (예: "F:")로 바꿉니다.

#### <a name="suggested-configuration-to-enable-os-dump"></a>OS 덤프를 사용 하도록 설정 하기 위한 권장 구성

**손상된 OS 디스크를 로드합니다.**

`REG LOAD HKLM\BROKENSYSTEM <VOLUME LETTER OF BROKEN OS DISK>:\windows\system32\config\SYSTEM`

**ControlSet001에서 사용하도록 설정합니다.**

`REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f`

`REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f`

`REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f`

**ControlSet002에서 사용하도록 설정합니다.**

`REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f`

`REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f`

`REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f`

**손상된 OS 디스크를 언로드합니다.**

`REG UNLOAD HKLM\BROKENSYSTEM`

### <a name="rebuild-the-original-vm"></a>원본 VM 다시 빌드

[VM 복구 명령의 5단계](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md#repair-process-example)를 사용하여 VM을 다시 조합합니다.
