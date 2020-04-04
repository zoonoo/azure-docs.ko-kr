---
title: Windows 업데이트를 적용할 때 Azure VM이 C01A001D 오류로 응답하지 않습니다.
description: 이 문서에서는 Windows 업데이트로 인해 오류가 발생하고 Azure VM에서 응답하지 않는 문제를 해결하는 단계를 제공합니다.
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
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/03/2020
ms.locfileid: "80633958"
---
# <a name="vm-is-unresponsive-with-c01a001d-error-when-applying-windows-update"></a>Windows 업데이트를 적용할 때 VM이 "C01A001D" 오류로 응답하지 않습니다.

이 문서에서는 KB(Windows Update)에서 오류가 발생하고 Azure VM에서 응답하지 않는 문제를 해결하는 단계를 제공합니다.

## <a name="symptoms"></a>증상

부팅 [진단을](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/boot-diagnostics) 사용하여 VM의 스크린샷을 볼 때 진행 중인 Windows 업데이트(KB)가 표시되지만 오류 코드인 'C01A001D'로 실패합니다.

![응답하지 않는 윈도우 업데이트](./media/unresponsive-vm-apply-windows-update/unresponsive-windows-update.png)

## <a name="cause"></a>원인

파일 시스템에서 핵심 파일을 만들 수 없습니다. 운영 체제에서 디스크에 파일을 쓸 수 없습니다.

## <a name="resolution"></a>해결 방법

### <a name="process-overview"></a>프로세스 개요

1. [복구 VM을 만들고 액세스합니다.](#create-and-access-a-repair-vm)
2. [하드 디스크에 공간을 확보합니다.](#free-up-space-on-the-hard-disk)
3. [권장: VM을 다시 빌드하기 전에 직렬 콘솔 및 메모리 덤프 컬렉션을 사용하도록 설정합니다.](#recommended-before-rebuilding-the-vm-enable-serial-console-and-memory-dump-collection)
4. [VM 을 다시 빌드합니다.](#rebuild-the-vm)

> [!NOTE]
> 이 오류가 발생하면 게스트 OS가 작동하지 않습니다. 이 문제를 해결하려면 오프라인 모드에서 트러블슈팅을 해야 합니다.

### <a name="create-and-access-a-repair-vm"></a>복구 VM 만들기 및 액세스

1. [VM 복구 명령의 1-3단계를](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands) 수행하여 복구 VM을 준비합니다.
2. 원격 데스크톱 연결을 사용하여 복구 VM에 연결합니다.

### <a name="free-up-space-on-the-hard-disk"></a>하드 디스크의 공간 확보

디스크가 아직 1Tb가 아닌 경우 디스크 크기를 조정해야 합니다. 디스크가 1TB가 되면 디스크 정리 및 드라이브 조각 모음을 수행합니다.

1. 디스크가 가득 찼는지 확인합니다. 디스크가 1Tb 미만이면 [PowerShell을 사용하여 최대 1Tb까지 확장합니다.](https://docs.microsoft.com/azure/virtual-machines/windows/expand-os-disk?toc=%2Fazure%2Fvirtual-machines%2Fwindows%2Ftoc.json)
2. 디스크가 1Tb가 되면 디스크 정리를 수행합니다.
    - [데이터 디스크를 깨진 VM에서 분리합니다.](https://docs.microsoft.com/azure/virtual-machines/windows/detach-disk)
    - [작동하는 VM에 데이터 디스크를 연결합니다.](https://docs.microsoft.com/azure/virtual-machines/windows/attach-disk-ps#attach-an-existing-data-disk-to-a-vm)
    - 디스크 [정리 도구를](https://support.microsoft.com/help/4026616/windows-10-disk-cleanup) 사용하여 공간을 확보합니다.
3. 크기를 조정하고 정리한 후 드라이브조각 모음을 해제합니다.

    ```
    defrag <LETTER ASSIGN TO THE OS DISK>: /u /x /g
    ```
    조각화 수준에 따라 몇 시간이 걸릴 수 있습니다.

### <a name="recommended-before-rebuilding-the-vm-enable-serial-console-and-memory-dump-collection"></a>권장 사항: VM을 다시 빌드하기 전에 직렬 콘솔 및 메모리 덤프 컬렉션을 사용하도록 설정합니다.

1. 높은 명령 프롬프트 세션을 엽니다(관리자로 실행).
2. 다음 명령을 실행합니다.

    직렬 콘솔 사용:

    ```
    bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /ems {<BOOT LOADER IDENTIFIER>} ON
    bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200
    ```
3. OS 디스크의 사용 공간은 RAM(VM 메모리) 크기와 적어도 동일한지 확인합니다.

    OS 디스크에 공간이 충분하지 않은 경우 메모리 덤프 파일이 생성될 위치를 변경하고 VM에 연결된 데이터 디스크와 충분한 여유 공간을 참조합니다. 위치를 변경하려면 아래 `%SystemRoot%` 명령의 데이터 디스크의 드라이브 문자(예: "F:")로 바꿉니다.

    **OS 덤프 제안 구성 사용:**

    로드 깨진 OS 디스크:

    ```
    REG LOAD HKLM\BROKENSYSTEM <VOLUME LETTER OF BROKEN OS DISK>:\windows\system32\config\SYSTEM
    ```

    제어Set001에서 사용:

    ```
    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f
    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f
    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f
    ```

    제어Set002에서 사용:

    ```
    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f 
    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f
    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f
    ```

    깨진 OS 디스크언지 언로드:

    ```
    REG UNLOAD HKLM\BROKENSYSTEM
    ```

### <a name="rebuild-the-vm"></a>VM 재생성

[VM 복구 명령의 5단계를](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands#repair-process-example) 사용하여 VM을 다시 어셈블합니다.
