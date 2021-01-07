---
title: 정책을 적용 하는 동안 Azure 가상 컴퓨터가 응답 하지 않음
description: 이 문서에서는 Azure VM에서 시작 하는 동안 정책을 적용할 때 로드 화면이 응답 하지 않는 문제를 해결 하는 단계를 제공 합니다.
services: virtual-machines-windows
documentationcenter: ''
author: TobyTu
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: a97393c3-351d-4324-867d-9329e31b5628
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.topic: troubleshooting
ms.date: 05/07/2020
ms.author: v-mibufo
ms.openlocfilehash: cbf2fe491e1fe0b553eab04ca7190da0413a3ba6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "86526013"
---
# <a name="vm-is-unresponsive-when-applying-group-policy-local-users-and-groups-policy"></a>그룹 정책 로컬 사용자 및 그룹 정책을 적용 하는 동안 VM이 응답 하지 않음

이 문서에서는 Azure VM (가상 컴퓨터)에서 시작 중에 정책을 적용할 때 로드 화면이 응답 하지 않는 문제를 해결 하는 단계를 제공 합니다.

## <a name="symptoms"></a>증상

[부팅 진단을](./boot-diagnostics.md) 사용 하 여 VM의 스크린샷을 볼 때 "로컬 사용자 및 그룹 정책 적용 그룹 정책" 메시지와 함께 화면이 로드 되지 않습니다.

:::image type="content" source="media//unresponsive-vm-apply-group-policy/applying-group-policy-1.png" alt-text="그룹 정책 로컬 사용자 및 그룹 정책 적용 로드의 스크린샷(Windows Server 2012 R2).":::

:::image type="content" source="media/unresponsive-vm-apply-group-policy/applying-group-policy-2.png" alt-text="그룹 정책 로컬 사용자 및 그룹 정책 적용 로드의 스크린샷(Windows Server 2012 R2).":::

## <a name="cause"></a>원인

정책이 이전 사용자 프로필을 정리 하려고 할 때 충돌 하는 잠금이 있습니다.

> [!NOTE]
> 이는 Windows Server 2012 및 Windows Server 2012 R2에만 적용됩니다.

다음은 문제가 있는 정책입니다.

`Computer Configuration\Policies\Administrative Templates\System/User Profiles\Delete user profiles older than a specified number of days on system restart`

## <a name="resolution"></a>해결 방법

### <a name="process-overview"></a>프로세스 개요

1. [복구 VM 만들기 및 액세스](#step-1-create-and-access-a-repair-vm)
1. [정책 사용 안 함](#step-2-disable-the-policy)
1. [직렬 콘솔 및 메모리 덤프 수집 사용](#step-3-enable-serial-console-and-memory-dump-collection)
1. [VM 다시 빌드](#step-4-rebuild-the-vm)

> [!NOTE]
> 이 부팅 오류가 발생 하면 게스트 OS가 작동 하지 않습니다. 오프라인 모드에서 문제를 해결해야 합니다.

### <a name="step-1-create-and-access-a-repair-vm"></a>1단계: 복구 VM 만들기 및 액세스

1. [VM 복구 명령의 1~3단계](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md#repair-process-example)를 사용하여 복구 VM을 준비합니다.
2. 원격 데스크톱 연결를 사용 하 여 복구 VM에 연결 합니다.

### <a name="step-2-disable-the-policy"></a>2단계: 정책 사용 안 함

1. 복구 VM에서 레지스트리 편집기를 엽니다.
1. 키 **HKEY_LOCAL_MACHINE** 를 찾고 메뉴에서 **파일**  >  **로드 하이브** 를 선택 합니다.

    :::image type="content" source="media/unresponsive-vm-apply-group-policy/registry.png" alt-text="그룹 정책 로컬 사용자 및 그룹 정책 적용 로드의 스크린샷(Windows Server 2012 R2)." /v CleanupProfiles /f
    ```
1.  다음 명령을 사용 하 여 BROKENSOFTWARE hive를 언로드합니다.

    ```
    reg unload HKLM\BROKENSOFTWARE
    ```

### <a name="step-3-enable-serial-console-and-memory-dump-collection"></a>3 단계: 직렬 콘솔 및 메모리 덤프 수집 사용

메모리 덤프 컬렉션과 직렬 콘솔을 사용 하도록 설정 하려면 다음 스크립트를 실행 합니다.

1. 관리자 권한 명령 프롬프트 세션을 엽니다. (관리자 권한으로 실행)
1. 다음 명령을 실행 하 여 직렬 콘솔을 사용 하도록 설정 합니다.
    
    ```
    bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /ems {<BOOT LOADER IDENTIFIER>} ON
    ```

    ```
    bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200
    ```
1. OS 디스크의 사용 가능한 공간이 VM의 메모리 크기 (RAM)와 같은지 확인 합니다.

    OS 디스크에 충분 한 공간이 없는 경우 메모리 덤프 위치를 변경 하 고 사용 가능한 공간이 충분 한 연결 된 데이터 디스크를 참조 합니다. 위치를 변경 하려면 다음 명령에서 "% SystemRoot%"를 데이터 디스크의 드라이브 문자 (예: "F:")로 바꿉니다.

    **OS 덤프를 사용 하도록 설정 하기 위한 권장 구성**

    손상 된 OS 디스크 로드:

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

### <a name="step-4-rebuild-the-vm"></a>4단계: VM 다시 빌드

Vm [복구 명령의 5 단계](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md#repair-process-example) 를 사용 하 여 vm을 리 어셈블합니다.

문제가 해결 되 면 정책을 로컬에서 사용할 수 없습니다. 영구 솔루션의 경우 Vm에서 CleanupProfiles 정책을 사용 하지 마세요. 다른 방법을 사용하여 프로필 정리를 수행합니다.

다음 정책을 사용하지 마세요.

`Machine\Admin Templates\System\User Profiles\Delete user profiles older than a specified number of days on system restart`

## <a name="next-steps"></a>다음 단계

Windows 업데이트 적용 하는 동안 문제가 발생 하는 경우 [Windows 업데이트를 적용할 때 VM이 "C01A001D" 오류와 함께 응답](./unresponsive-vm-apply-windows-update.md)하지 않음을 참조 하세요.
