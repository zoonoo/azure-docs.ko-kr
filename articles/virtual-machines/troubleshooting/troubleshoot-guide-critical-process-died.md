---
title: 윈도우 중지 오류 -
description: ''
services: virtual-machines-windows
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: 5b3ed56a-5a11-4ff9-9ee8-76aea4a5689b
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.topic: troubleshooting
ms.date: 03/26/2020
ms.author: v-mibufo
ms.openlocfilehash: 9e4c4b9c809a626c71b4a7e9235d917b442be160
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "80373363"
---
# <a name="windows-stop-error---0x000000ef-critical-process-died"></a>Windows 중지 오류 - #0x000000EF "중요한 프로세스가 죽었습니다"

이 문서에서는 Azure VM에서 부팅하는 동안 중요한 프로세스가 죽는 문제를 해결하는 단계를 제공합니다.

## <a name="symptom"></a>증상

[부팅 진단을](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/boot-diagnostics) 사용하여 VM의 스크린샷을 볼 때 스크린샷에 중요 *프로세스가 죽었다는*메시지와 함께 *#0x000000EF* 오류가 표시됩니다.

!["PC에 문제가 생겨 다시 시작해야 합니다. 방금 몇 가지 오류 정보를 수집하고 있습니다. 이후에 다시 시작할 수 있습니다. (##% 완료) 자세한 내용은 나중에 이 오류를 온라인으로 검색할 수 있습니다: 0x000000EF"](media/troubleshoot-guide-critical-process-died/1.jpg)

## <a name="cause"></a>원인

일반적으로 이는 부팅 중에 중요한 시스템 프로세스가 실패하기 때문입니다. 중요한 프로세스 문제에 대한 자세한 내용은["버그 확인 0xEF: CRITICAL_PROCESS_DIED"에서](https://docs.microsoft.com/windows-hardware/drivers/debugger/bug-check-0xef--critical-process-died)확인할 수 있습니다.

## <a name="solution"></a>해결 방법

### <a name="process-overview"></a>프로세스 개요:

1. 복구 VM을 만들고 액세스합니다.
2. OS 손상 문제를 해결합니다.
3. **권장**: VM을 다시 빌드하기 전에 직렬 콘솔 및 메모리 덤프 컬렉션을 사용하도록 설정합니다.
4. VM을 다시 빌드합니다.

> [!NOTE]
> 이 부팅 오류가 발생하면 게스트 OS가 작동하지 않습니다. 이 문제를 해결 하려면 오프라인 모드에서 문제 해결 됩니다.

### <a name="create-and-access-a-repair-vm"></a>복구 VM 만들기 및 액세스

1. [VM 복구 명령의 1-3단계를](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands) 사용하여 복구 VM을 준비합니다.
2. 원격 데스크톱 연결을 사용하여 복구 VM에 연결합니다.

### <a name="fix-any-os-corruption"></a>OS 손상 수정

1. 관리자 권한 명령 프롬프트를 엽니다.
2. 다음 시스템 파일 검사기(SFC) 명령을 실행합니다.

   `sfc /scannow /offbootdir=<BOOT DISK DRIVE>:\ /offwindir=<BROKEN DISK DRIVE>:\windows`

   * < 부팅 디스크 드라이브 > 복구 VM(일반적으로 "C:")의 부팅 볼륨과 < 깨진 디스크 드라이브 > 손상된 VM에서 연결된 디스크의 드라이브 문자가 됩니다. 기호보다 크고 기호보다 적은 텍스트와 함께 포함된 텍스트(예: "> 텍스트 <"와 같은 텍스트를 적절한 문자로 바꿉습니다.

3. 그런 다음 [VM 복구 명령의 5단계를](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands#repair-process-example) 사용하여 VM을 다시 어셈블하고 부팅되는지 확인합니다.
4. VM이 아직 부팅되지 않으면 메모리 덤프 파일을 계속 수집합니다.

### <a name="collect-the-memory-dump-file"></a>메모리 덤프 파일 수집

SFC를 실행한 후에도 문제가 지속되면 문제의 원인을 확인하려면 메모리 덤프 파일을 분석해야 합니다. 메모리 덤프 파일을 수집하려면 다음 단계를 따르십시오.

### <a name="attach-the-os-disk-to-a-new-repair-vm"></a>OS 디스크를 새 복구 VM에 연결

1. [VM 복구 명령의 1-3단계를](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands) 사용하여 새 복구 VM을 준비합니다.
2. 원격 데스크톱 연결을 사용하여 복구 VM에 연결합니다.

### <a name="locate-the-dump-file-and-submit-a-support-ticket"></a>덤프 파일을 찾아 지원 티켓을 제출합니다.

3. 복구 VM에서 연결된 OS 디스크의 창 폴더로 이동합니다. 연결된 OS 디스크에 할당된 드라이버 문자가 *F인*경우 *F:\Windows로*이동해야 합니다.
4. *memory.dmp* 파일을 찾은 다음 메모리 덤프 [파일로 지원 티켓을 제출합니다.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)

   > [!NOTE]
   > 덤프 파일을 찾을 수 없는 경우 아래 단계를 완료하여 메모리 덤프 수집 및 직렬 콘솔을 활성화한 다음 이 섹션으로 돌아가 위의 작업의 단계를 반복하여 메모리 덤프 파일을 수집합니다.

### <a name="recommended-before-you-rebuild-the-vm-enable-serial-console-and-memory-dump-collection"></a>권장: VM을 다시 빌드하기 전에 직렬 콘솔 및 메모리 덤프 컬렉션을 사용하도록 설정합니다.

메모리 덤프 컬렉션 및 직렬 콘솔을 사용하려면 다음 스크립트를 실행합니다.

1. 높은 명령 프롬프트 세션을 엽니다(관리자로 실행).
2. 다음 명령을 실행합니다.

   직렬 콘솔 사용

   `bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /ems {<BOOT LOADER IDENTIFIER>} ON`

   `bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200`

   "> 텍스트< 와 같이 기호보다 크거나 적은 기호와 그 안에 있는 텍스트를 바꿉습니다.

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
