---
title: 시스템에 보안 정책을 적용 하는 동안 Azure VM이 응답 하지 않습니다.
description: 이 문서에서는 Azure VM의 시스템에 보안 정책을 적용 하는 동안 VM이 응답 하지 않을 때 로드 화면이 중단 되는 문제를 해결 하는 단계를 제공 합니다.
services: virtual-machines-windows
documentationcenter: ''
author: TobyTu
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: a97393c3-351d-4324-867d-9329e31b5629
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.topic: troubleshooting
ms.date: 06/15/2020
ms.author: v-mibufo
ms.openlocfilehash: 6b50bffd1a44c0cf53f15650f5ff4d938f45df4d
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84908064"
---
# <a name="azure-vm-is-unresponsive-while-applying-security-policy-to-the-system"></a>시스템에 보안 정책을 적용 하는 동안 Azure VM이 응답 하지 않습니다.

이 문서에서는 OS가 중단 되 고 Azure VM에 보안 정책을 적용 하는 동안 응답 하지 않는 문제를 해결 하는 단계를 제공 합니다.

## <a name="symptoms"></a>증상

[부팅 진단을](boot-diagnostics.md) 사용 하 여 VM의 스크린샷을 볼 때 메시지를 사용 하 여 부팅 하는 동안 OS가 중지 된 것이 표시 됩니다.

> ' 시스템에 보안 정책을 적용 하는 중 '.

:::image type="content" source="media/unresponsive-vm-apply-security-policy/apply-policy.png" alt-text="Windows Server 2012 R2 시작 화면의 스크린샷이 중지 되었습니다.":::

:::image type="content" source="media/unresponsive-vm-apply-security-policy/apply-policy-2.png" alt-text="OS 시작 화면의 스크린샷이 중지 되었습니다.":::

## <a name="cause"></a>원인

이 문제의 가능한 원인은 다양 한 있습니다. 메모리 덤프 분석이 수행 될 때까지 소스를 알 수 없습니다.

## <a name="resolution"></a>해결 방법

### <a name="process-overview"></a>프로세스 개요

1. [복구 VM 만들기 및 액세스](#create-and-access-a-repair-vm)
2. [직렬 콘솔 및 메모리 덤프 수집 사용](#enable-serial-console-and-memory-dump-collection)
3. [VM 다시 빌드](#rebuild-the-vm)
4. [메모리 덤프 파일 수집](#collect-the-memory-dump-file)

### <a name="create-and-access-a-repair-vm"></a>복구 VM 만들기 및 액세스

1. [VM 복구 명령의 1~3단계](repair-windows-vm-using-azure-virtual-machine-repair-commands.md#repair-process-example)를 사용하여 복구 VM을 준비합니다.
2. 원격 데스크톱 연결를 사용 하 여 복구 VM에 연결 합니다.

### <a name="enable-serial-console-and-memory-dump-collection"></a>직렬 콘솔 및 메모리 덤프 수집 사용

메모리 덤프 수집 및 직렬 콘솔을 사용하려면 다음 스크립트를 실행합니다.

1. 관리자 권한 명령 프롬프트 세션을 엽니다(관리자 권한으로 실행).
2. BCD 저장소 데이터를 나열 하 고 다음 단계에서 사용할 부팅 로더 식별자를 확인 합니다.

     1. 1 세대 VM의 경우 다음 명령을 입력 하 고 나열 된 식별자를 확인 합니다.

        ```console
        bcdedit /store <BOOT PARTITON>:\boot\bcd /enum
        ```

        명령에서를 \<BOOT PARTITON> 부팅 폴더를 포함 하는 연결 된 디스크의 파티션 문자로 바꿉니다.

        :::image type="content" source="media/unresponsive-vm-apply-security-policy/store-data.png" alt-text="다이어그램은 Windows 부팅 로더 아래에 식별자 번호를 나열 하는 1 세대 VM에 BCD 저장소를 나열 하는 출력을 보여 줍니다.":::

     2. 2 세대 VM의 경우 다음 명령을 입력 하 고 나열 된 식별자를 확인 합니다.

        ```console
        bcdedit /store <LETTER OF THE EFI SYSTEM PARTITION>:EFI\Microsoft\boot\bcd /enum
        ```

        - 명령에서를 \<LETTER OF THE EFI SYSTEM PARTITION> EFI 시스템 파티션의 문자로 바꿉니다.
        - 디스크 관리 콘솔을 시작 하 여 "EFI 시스템 파티션" 이라는 레이블이 지정 된 적절 한 시스템 파티션을 확인 하는 것이 유용할 수 있습니다.
        - 식별자는 고유한 GUID 이거나 기본 "bootmgr" 일 수 있습니다.
3. 다음 명령을 실행 하 여 직렬 콘솔을 사용 하도록 설정 합니다.

    ```console
    bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /ems {<BOOT LOADER IDENTIFIER>} ON
    ```

    ```console
    bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200
    ```

    - 명령에서를 \<VOLUME LETTER WHERE THE BCD FOLDER IS> BCD 폴더의 문자로 바꿉니다.
    - 명령에서을 \<BOOT LOADER IDENTIFIER> 이전 단계에서 찾은 식별자로 바꿉니다.
4. OS 디스크의 사용 가능한 공간이 VM의 메모리 크기 (RAM) 보다 큰지 확인 합니다.

    1. OS 디스크에 공간이 부족 한 경우 메모리 덤프 파일이 생성 될 위치를 변경 해야 합니다. OS 디스크에서 파일을 만드는 대신 사용 가능한 공간이 충분 한 VM에 연결 된 다른 데이터 디스크를 참조할 수 있습니다. 위치를 변경 하려면 아래 나열 된 명령에서 "% SystemRoot%"를 데이터 디스크의 드라이브 문자 (예: "F:")로 바꿉니다.
    2. 아래 명령을 입력 합니다 (제안 덤프 구성).

        손상된 OS 디스크 로드:

        ```console
        REG LOAD HKLM\BROKENSYSTEM <VOLUME LETTER OF BROKEN OS DISK>:\windows\system32\config\SYSTEM
        ```

        ControlSet001에서 사용:

        ```console
        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f
        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f
        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f
        ```

        ControlSet002에서 사용:

        ```console
        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f
        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f
        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f
        ```

        손상된 OS 디스크를 언로드합니다.

        ```console
        REG UNLOAD HKLM\BROKENSYSTEM
        ```

### <a name="rebuild-the-vm"></a>VM 다시 빌드

[VM 복구 명령의 5단계](repair-windows-vm-using-azure-virtual-machine-repair-commands.md#repair-process-example)를 사용하여 VM을 다시 조합합니다.

### <a name="collect-the-memory-dump-file"></a>메모리 덤프 파일 수집

이 문제를 해결 하려면 먼저 크래시에 대 한 메모리 덤프 파일을 수집 하 고 메모리 덤프 파일을 사용 하 여 지원에 문의 해야 합니다. 덤프 파일을 수집 하려면 다음 단계를 수행 합니다.

1. 새 복구 VM에 OS 디스크를 연결 합니다.

    - [Vm 복구 명령의 1-3 단계](repair-windows-vm-using-azure-virtual-machine-repair-commands.md#repair-process-example) 를 사용 하 여 새 복구 vm을 준비 합니다.
    - 원격 데스크톱 연결를 사용 하 여 복구 VM에 연결 합니다.

2. 덤프 파일을 찾아서 지원 티켓을 제출합니다.

    - 복구 VM에서 연결 된 OS 디스크의 windows 폴더로 이동 합니다. 연결된 OS 디스크에 할당된 드라이브 문자가 `F`인 경우 `F:\Windows`로 이동해야 합니다.
    - Memory.dmp 파일을 찾은 다음 메모리 덤프 파일을 사용 하 여 [지원 티켓을 제출](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) 합니다.
    - Memory.dmp 파일을 찾는 데 문제가 있는 경우 대신 [직렬 콘솔에서 비 마스크 인터럽트 (NMI) 호출](serial-console-windows.md#use-the-serial-console-for-nmi-calls) 을 사용 하는 것이 좋습니다. 이 가이드에 따라 [NMI 호출을 사용 하 여 크래시 덤프 파일을 생성할](/windows/client-management/generate-kernel-or-complete-crash-dump)수 있습니다.

## <a name="next-steps"></a>다음 단계

로컬 사용자 및 그룹 정책을 적용 하는 동안 문제가 발생 하는 경우 [그룹 정책 로컬 사용자 및 그룹 정책을 적용할 때 VM이 응답](unresponsive-vm-apply-group-policy.md) 하지 않음을 참조 하세요.