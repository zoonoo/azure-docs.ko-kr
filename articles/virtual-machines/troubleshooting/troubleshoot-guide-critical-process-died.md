---
title: Windows 중지 오류-
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
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "80373363"
---
# <a name="windows-stop-error---0x000000ef-critical-process-died"></a>Windows 중지 오류-"심각한 프로세스를 #0x000000EF."

이 문서에서는 Azure VM에서 부팅 하는 동안 중요 한 프로세스가 중단 되는 문제를 해결 하는 단계를 제공 합니다.

## <a name="symptom"></a>증상

[부팅 진단을](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/boot-diagnostics) 사용 하 여 VM의 스크린샷을 볼 때 메시지에 *중요 한 프로세스*를 종료 하는 오류 *#0x000000EF* 스크린 샷에 표시 되는 것을 볼 수 있습니다.

!["PC에 문제가 생겨 다시 시작해야 합니다. 방금 몇 가지 오류 정보를 수집하고 있습니다. 이후에 다시 시작할 수 있습니다. (# #% 완료) 자세히 알아보려면 나중에이 오류에 대해 온라인으로 검색할 수 있습니다. 0x000000EF "](media/troubleshoot-guide-critical-process-died/1.jpg)

## <a name="cause"></a>원인

일반적으로 부팅 하는 동안 심각한 시스템 프로세스가 실패 하기 때문입니다. 중요 프로세스 문제에 대 한 자세한 내용은 "[버그 확인 0xEF: CRITICAL_PROCESS_DIED](https://docs.microsoft.com/windows-hardware/drivers/debugger/bug-check-0xef--critical-process-died)"에서 확인할 수 있습니다.

## <a name="solution"></a>해결 방법

### <a name="process-overview"></a>프로세스 개요:

1. 복구 VM을 만들고 액세스합니다.
2. 모든 OS 손상을 수정 합니다.
3. **권장**: VM을 다시 빌드하기 전에 직렬 콘솔과 메모리 덤프 수집을 사용하도록 설정합니다.
4. VM을 다시 빌드합니다.

> [!NOTE]
> 이 부팅 오류가 발생할 경우 게스트 OS가 작동 하지 않습니다. 이 문제를 해결 하려면 오프 라인 모드에서 문제를 해결 합니다.

### <a name="create-and-access-a-repair-vm"></a>복구 VM 만들기 및 액세스

1. [VM 복구 명령의 1~3단계](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands)를 사용하여 복구 VM을 준비합니다.
2. 원격 데스크톱 연결을 사용하여 복구 VM에 연결합니다.

### <a name="fix-any-os-corruption"></a>모든 OS 손상 수정

1. 관리자 권한 명령 프롬프트를 엽니다.
2. 다음 SFC (시스템 파일 검사기) 명령을 실행 합니다.

   `sfc /scannow /offbootdir=<BOOT DISK DRIVE>:\ /offwindir=<BROKEN DISK DRIVE>:\windows`

   * 여기서 < 부팅 디스크 > 드라이브는 복구 VM의 부팅 볼륨 (일반적으로 "C:")이 고 < 손상 된 디스크 드라이브 >은 손상 된 VM에서 연결 된 디스크의 드라이브 문자입니다. 기호에 포함 된 텍스트 뿐만 아니라 보다 큼/보다 작음 기호를 적절 한 문자로 바꿉니다. 예를 들어 "< 텍스트 >"

3. 그런 다음 vm [복구 명령의 5 단계](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands#repair-process-example) 를 사용 하 여 vm을 다시 조립 하 고 부팅 하는지 확인 합니다.
4. VM이 여전히 부팅 되지 않으면 메모리 덤프 파일을 계속 수집 합니다.

### <a name="collect-the-memory-dump-file"></a>메모리 덤프 파일 수집

SFC를 실행 한 후에도 문제가 지속 되 면 메모리 덤프 파일을 분석 하 여 문제의 원인을 확인 해야 합니다. 메모리 덤프 파일을 수집 하려면 다음 단계를 수행 합니다.

### <a name="attach-the-os-disk-to-a-new-repair-vm"></a>새 복구 VM에 OS 디스크를 연결 합니다.

1. [Vm 복구 명령의 1-3 단계](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands) 를 사용 하 여 새 복구 vm을 준비 합니다.
2. 원격 데스크톱 연결을 사용하여 복구 VM에 연결합니다.

### <a name="locate-the-dump-file-and-submit-a-support-ticket"></a>덤프 파일을 찾고 지원 티켓을 제출 합니다.

3. 복구 VM에서 연결 된 OS 디스크의 windows 폴더로 이동 합니다. 연결 된 OS 디스크에 할당 된 드라이버 문자가 *F*인 경우에는 *F:\Windows*으로 이동 해야 합니다.
4. *Memory.dmp* 파일을 찾은 다음 메모리 덤프 파일을 사용 하 여 [지원 티켓을 제출](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) 합니다.

   > [!NOTE]
   > 덤프 파일을 찾을 수 없는 경우 아래 단계를 완료 하 여 메모리 덤프 수집 및 직렬 콘솔을 사용 하도록 설정한 다음이 섹션으로 돌아가서 위의 작업 단계를 반복 하 여 메모리 덤프 파일을 수집 합니다.

### <a name="recommended-before-you-rebuild-the-vm-enable-serial-console-and-memory-dump-collection"></a>권장: VM을 다시 빌드하기 전에 직렬 콘솔과 메모리 덤프 수집을 사용 하도록 설정 합니다.

메모리 덤프 수집 및 직렬 콘솔을 사용 하도록 설정 하려면 다음 스크립트를 실행 합니다.

1. 관리자 권한 명령 프롬프트 세션을 엽니다(관리자 권한으로 실행).
2. 다음 명령을 실행합니다.

   직렬 콘솔 사용

   `bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /ems {<BOOT LOADER IDENTIFIER>} ON`

   `bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200`

   "< 텍스트를 >."와 같이 기호 안에 있는 텍스트 뿐만 아니라 보다 큼 또는 보다 작음 기호를 대체 합니다.

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

[VM 복구 명령의 5단계](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands#repair-process-example)를 사용하여 VM을 다시 조합합니다.
