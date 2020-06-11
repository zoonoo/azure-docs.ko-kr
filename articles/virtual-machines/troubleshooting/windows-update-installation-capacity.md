---
title: OS 시작 - Windows 업데이트 설치 용량 문제 해결
description: Windows 업데이트(KB)에서 오류가 발생하여 Azure VM에서 응답하지 않는 문제를 해결하는 단계입니다.
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: 6359e486-7b02-4c1e-995c-ef6d505f85f4
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 05/11/2020
ms.author: v-miegge
ms.openlocfilehash: bb7b641a7169c6577320f07a964d278ac1727b1c
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/19/2020
ms.locfileid: "83663311"
---
# <a name="troubleshoot-os-start-up--windows-update-installation-capacity"></a>OS 시작 - Windows 업데이트 설치 용량 문제 해결

이 문서에서는 Windows 업데이트(KB)에서 오류가 발생하고 응답하지 않는 Azure VM(Virtual Machine)의 문제를 해결하는 단계를 제공합니다.

## <a name="symptom"></a>증상

부팅 진단을 사용하여 VM의 스크린샷을 보면 스크린샷에 진행 중이지만 다음 오류 코드를 나타내며 실패하는 Windows 업데이트(KB)가 표시됩니다. **C01A001D**. 다음 이미지는 메시지 “업데이트 작업 ##### / #####(######) 적용 시 오류 C01A001D 발생”을 나타내며 Windows 업데이트(KB)가 중단되는 경우를 보여 줍니다.

!["업데이트 작업 X/Y(Z) 적용 시 오류 C01A001D 발생"을 나타내며 Windows 업데이트(KB)가 중단됩니다.](./media/troubleshoot-windows-update-installation-capacity/1.png)

## <a name="cause"></a>원인

이 경우 OS(운영 체제)는 파일 시스템에서 코어 파일을 만들 수 없기 때문에 Windows 업데이트(KB) 설치를 완료할 수 없습니다. 이 오류 코드에 따라 운영 체제는 디스크에 파일을 쓸 수 없습니다.

## <a name="solution"></a>해결 방법

### <a name="process-overview"></a>프로세스 개요:

1. 복구 VM을 만들고 액세스합니다.
1. 디스크의 공간을 확보합니다.
1. 직렬 콘솔과 메모리 덤프 수집을 사용하도록 설정합니다.
1. VM을 다시 빌드합니다.

> [!NOTE]
> 이 오류가 발생할 경우 게스트 OS가 작동하지 않습니다. 이 문제를 해결하려면 오프라인 모드에서 이 문제를 해결하세요.

### <a name="create-and-access-a-repair-vm"></a>복구 VM 만들기 및 액세스

1. [VM 복구 명령](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands)의 1~3단계를 사용하여 복구 VM을 준비합니다.
1. 원격 데스크톱 연결을 사용하여 복구 VM에 연결합니다.

### <a name="free-up-space-on-the-disk"></a>디스크의 공간 확보

이 문제를 해결하려면

- 아직 최대 크기인 1TB가 아닌 경우 디스크 크기를 1TB까지 조정합니다.
- 디스크 정리를 수행합니다.
- 드라이브의 조각화를 취소합니다.

1. 디스크가 꽉 찼는지 확인합니다. 디스크 크기가 1TB 미만이면 [PowerShell을 사용](https://docs.microsoft.com/azure/virtual-machines/windows/expand-os-disk)하여 최대 1TB까지 확장합니다.
1. 디스크가 이미 1TB인 경우 디스크 정리를 수행해야 합니다.
   1. [손상된 VM](https://docs.microsoft.com/azure/virtual-machines/windows/detach-disk)에서 데이터 디스크를 분리합니다.
   1. 데이터 디스크를 [작동하는 VM](https://docs.microsoft.com/azure/virtual-machines/windows/attach-disk-ps#attach-an-existing-data-disk-to-a-vm)에 연결합니다.
   1. [디스크 정리 도구](https://support.microsoft.com/help/4026616/windows-10-disk-cleanup)를 사용하여 공간을 확보합니다.
1. 크기 조정 및 정리가 완료되면 다음 명령을 사용하여 드라이브의 조각을 제거합니다.

   ```
   defrag <LETTER ASSIGN TO THE OS DISK>: /u /x /g
   ```
   
조각화 수준에 따라 조각화 취소에 몇 시간이 걸릴 수도 있습니다.

### <a name="enable-the-serial-console-and-memory-dump-collection"></a>직렬 콘솔과 메모리 덤프 수집 사용

**권장**: VM을 다시 빌드하기 전에 다음 스크립트를 실행하여 직렬 콘솔과 메모리 덤프 수집을 사용하도록 설정합니다.

1. 관리자 권한 명령 프롬프트 세션을 엽니다.
1. 다음 명령을 실행합니다.

   **직렬 콘솔을 사용하도록 설정합니다**.
   
   ```
   bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /ems {<BOOT LOADER IDENTIFIER>} ON 
   bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200
   ```

1. OS 디스크의 사용 가능한 공간이 VM의 메모리 크기(RAM)보다 큰지 확인합니다.

   OS 디스크에 공간이 부족한 경우 메모리 덤프 파일이 생성될 위치를 변경하고 사용 가능한 공간이 충분한 VM에 연결된 데이터 디스크의 위치를 참조합니다. 이 위치를 변경하려면 다음 명령에서 **%SystemRoot%** 를 데이터 디스크의 드라이브 문자(예: **F:** )로 바꿉니다.

   OS 덤프를 사용하도록 설정하기 위한 권장 구성:

    **손상된 OS 디스크를 로드합니다**.

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

[VM 복구 명령의 5단계](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands#repair-process-example)를 사용하여 VM을 다시 빌드합니다.
