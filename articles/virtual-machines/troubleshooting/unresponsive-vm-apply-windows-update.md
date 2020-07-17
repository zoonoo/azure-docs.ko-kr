---
title: Windows 업데이트 적용 하는 동안 Azure VM이 C01A001D 오류로 응답 하지 않음
description: 이 문서에서는 Windows update가 오류를 생성 하 고 Azure VM에서 응답 하지 않는 문제를 해결 하는 단계를 제공 합니다.
services: virtual-machines-windows
documentationcenter: ''
author: TobyTu
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: a97393c3-351d-4324-867d-9329e31b3528
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.topic: troubleshooting
ms.date: 03/31/2020
ms.author: v-mibufo
ms.openlocfilehash: 16c8eed3377c2191b4345ec59ec1eba8be01369d
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "80633958"
---
# <a name="vm-is-unresponsive-with-c01a001d-error-when-applying-windows-update"></a>Windows 업데이트를 적용 하는 동안 "C01A001D" 오류가 발생 하 여 VM이 응답 하지 않습니다.

이 문서에서는 Windows 업데이트 (KB)가 오류를 생성 하 고 Azure VM에서 응답 하지 않는 문제를 해결 하는 단계를 제공 합니다.

## <a name="symptoms"></a>증상

[부팅 진단을](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/boot-diagnostics) 사용 하 여 VM의 스크린샷을 볼 때 진행 중인 WINDOWS 업데이트 (KB)가 표시 되지만 오류 코드 ' C01A001D '와 함께 실패 합니다.

![응답 하지 않는 Windows 업데이트](./media/unresponsive-vm-apply-windows-update/unresponsive-windows-update.png)

## <a name="cause"></a>원인

파일 시스템에서 핵심 파일을 만들 수 없습니다. 운영 체제에서 디스크에 파일을 쓸 수 없습니다.

## <a name="resolution"></a>해결 방법

### <a name="process-overview"></a>프로세스 개요

1. [복구 VM 만들기 및 액세스](#create-and-access-a-repair-vm)
2. [하드 디스크의 공간을 확보](#free-up-space-on-the-hard-disk)합니다.
3. [권장: VM을 다시 작성 하기 전에 직렬 콘솔과 메모리 덤프 수집을 사용 하도록 설정](#recommended-before-rebuilding-the-vm-enable-serial-console-and-memory-dump-collection)합니다.
4. [VM을 다시 빌드합니다](#rebuild-the-vm).

> [!NOTE]
> 이 오류가 발생 하면 게스트 OS가 작동 하지 않습니다. 이 문제를 해결 하려면 오프 라인 모드에서 문제를 해결 해야 합니다.

### <a name="create-and-access-a-repair-vm"></a>복구 VM 만들기 및 액세스

1. [Vm 복구 명령의 1-3 단계](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands) 를 수행 하 여 복구 vm을 준비 합니다.
2. 원격 데스크톱 연결를 사용 하 여 복구 VM에 연결 합니다.

### <a name="free-up-space-on-the-hard-disk"></a>하드 디스크 공간 확보

디스크가 아직 1Tb가 아니면 크기를 조정 해야 합니다. 디스크가 1tb 이면 디스크 정리 및 드라이브 조각 모음을 수행 합니다.

1. 디스크가 꽉 찼는지 확인합니다. 디스크가 1tb 미만이 면 [PowerShell을 사용 하 여 최대 1tb까지 확장](https://docs.microsoft.com/azure/virtual-machines/windows/expand-os-disk?toc=%2Fazure%2Fvirtual-machines%2Fwindows%2Ftoc.json)합니다.
2. 디스크가 1tb 이면 디스크 정리를 수행 합니다.
    - [손상 된 VM에서 데이터 디스크를 분리](https://docs.microsoft.com/azure/virtual-machines/windows/detach-disk)합니다.
    - [작동 하는 VM에 데이터 디스크를 연결](https://docs.microsoft.com/azure/virtual-machines/windows/attach-disk-ps#attach-an-existing-data-disk-to-a-vm)합니다.
    - [디스크 정리 도구](https://support.microsoft.com/help/4026616/windows-10-disk-cleanup)를 사용하여 공간을 확보합니다.
3. 크기 조정 및 정리 후 드라이브 조각 모음을 수행 합니다.

    ```
    defrag <LETTER ASSIGN TO THE OS DISK>: /u /x /g
    ```
    조각화 수준에 따라 몇 시간이 걸릴 수 있습니다.

### <a name="recommended-before-rebuilding-the-vm-enable-serial-console-and-memory-dump-collection"></a>권장: VM을 다시 작성 하기 전에 직렬 콘솔과 메모리 덤프 수집을 사용 하도록 설정 합니다.

1. 관리자 권한 명령 프롬프트 세션을 엽니다(관리자 권한으로 실행).
2. 다음 명령을 실행합니다.

    직렬 콘솔 사용:

    ```
    bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /ems {<BOOT LOADER IDENTIFIER>} ON
    bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200
    ```
3. OS 디스크의 사용 가능한 공간이 최소한 VM 메모리 (RAM) 크기와 일치 하는지 확인 합니다.

    OS 디스크에 충분 한 공간이 없는 경우 메모리 덤프 파일이 생성 될 위치를 변경 하 고 VM에 연결 된 데이터 디스크와 사용 가능한 공간이 충분 한 데이터 디스크를 참조 합니다. 위치를 변경 하려면 `%SystemRoot%` 아래 명령에서 데이터 디스크의 드라이브 문자 (예: "F:")로 대체 합니다.

    **OS 덤프 권장 구성 사용:**

    손상된 OS 디스크 로드:

    ```
    REG LOAD HKLM\BROKENSYSTEM <VOLUME LETTER OF BROKEN OS DISK>:\windows\system32\config\SYSTEM
    ```

    ControlSet001에서 사용:

    ```
    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f
    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f
    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f
    ```

    ControlSet002에서 사용:

    ```
    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f 
    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f
    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f
    ```

    손상된 OS 디스크 언로드:

    ```
    REG UNLOAD HKLM\BROKENSYSTEM
    ```

### <a name="rebuild-the-vm"></a>VM 다시 빌드

Vm [복구 명령의 5 단계](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands#repair-process-example) 를 사용 하 여 vm을 리 어셈블합니다.
