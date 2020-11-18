---
title: Windows 중지 오류-하드웨어 오작동
description: 이 문서에서는 Windows Server 2008 가상 컴퓨터가 하드웨어 오작동을 나타내는 오류 메시지와 함께 작동이 중단 되는 문제를 해결 하는 단계를 제공 합니다.
services: virtual-machines-windows
documentationcenter: ''
author: mibufo
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.topic: troubleshooting
ms.date: 11/13/2020
ms.author: v-mibufo
ms.openlocfilehash: eb4e0a246d6a33c3fad5f44b99a37997e4462f05
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/17/2020
ms.locfileid: "94663865"
---
# <a name="windows-stop-error---hardware-malfunction"></a>Windows 중지 오류-하드웨어 오작동

이 문서에서는 Windows Server 2008 가상 컴퓨터가 하드웨어 오작동을 나타내는 오류 메시지와 함께 작동이 중단 되는 문제를 해결 하는 단계를 제공 합니다.

## <a name="symptoms"></a>증상

[부팅 진단을](./boot-diagnostics.md) 사용 하 여 VM의 스크린샷을 볼 때 스크린 샷은 다음과 같은 메시지와 함께 파란색 화면을 표시 하는 것을 볼 수 있습니다.

**\*\*\* 하드웨어 오작동**

**공급 업체에 지원을 요청 하세요.**

**\*\*\* 시스템이 중지 되었습니다. \*\*\***

#### <a name="blue-screen"></a>블루 스크린

![스크린 샷에서는 블루 스크린 하드웨어 오작동 충돌을 보여 줍니다.](media/windows-stop-error-hardware-malfunction/windows-stop-error-hardware-malfunction-1.png)

#### <a name="serial-console"></a>직렬 콘솔

![직렬 콘솔을 사용 하도록 설정한 경우 스크린샷은 직렬 콘솔 기능에서 "하드웨어 오작동" 메시지를 보여 줍니다.](media/windows-stop-error-hardware-malfunction/windows-stop-error-hardware-malfunction-2.png)

## <a name="cause"></a>원인

이 화면은 게스트 OS가 올바르게 설정 되지 않았으며, 마스크 불가능 인터럽트 (NMI)를 보낸 경우에 나타납니다. 오류 메시지는 커널 모드 프로그램이 예외를 생성 하 여 처리기에서 catch 하지 못했음을 나타냅니다. 메모리 덤프를 수집 하 여 생성 된 예외를 식별할 수 있습니다.

## <a name="solution"></a>해결 방법

### <a name="process-overview"></a>프로세스 개요 

1. 비 마스크 인터럽트 (NMI) 레지스트리 키 설정 
2. 복구 VM 만들기 및 액세스 
3. 직렬 콘솔 및 메모리 덤프 수집 사용 
4. VM 다시 빌드 

### <a name="set-up-the-non-maskable-interrupt-nmi-registry-key"></a>비 마스크 인터럽트 (NMI) 레지스트리 키 설정

1. Azure Portal 사용 하 여 게스트 OS가 정상적으로 부팅 되도록 VM을 다시 시작 합니다. 
2. VM에 대 한 일부 액세스가 복원 된 후 관리자 권한 명령 프롬프트를 엽니다 (관리자 권한으로 실행). 
3. 다음 명령을 사용 하 여 NMI 레지스트리 키를 설정 합니다.

    ```
    REG ADD "HKLM\SYSTEM\CurrentControlSet\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f
    ```
    [REG ADD 명령에 대 한 추가 정보 보기](https://docs.microsoft.com/windows-server/administration/windows-commands/reg-add)
4. *(선택 사항)* 설치 메모리 덤프 컬렉션:

    ```
    REG ADD "HKLM\SYSTEM\CurrentControlSet\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f  
    REG ADD "HKLM\SYSTEM\CurrentControlSet\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f 

    ```
5. *(선택 사항)* 직렬 콘솔 액세스 설정:

    ```
    BCDEDIT /ems {current} on, or bcdedit /ems '{current}' on if you are using PowerShell
    BCDEDIT /emssettings EMSPORT:1 EMSBAUDRATE:115200 
    ```
    [BCDEDIT 명령에 대 한 추가 정보 보기](https://docs.microsoft.com/windows-server/administration/windows-commands/bcdedit)
6. 다음 명령을 사용 하 여 VM을 다시 시작 합니다.

    ```
    SHUTDOWN /r /t 0 /f 
    ```
    [SHUTDOWN 명령에 대 한 추가 정보 보기](https://docs.microsoft.com/windows-server/administration/windows-commands/shutdown)

> [!IMPORTANT]
> 이제 문제를 해결 해야 합니다.

> [!NOTE]
> 다시 시작한 후 VM을 테스트 하 여 평소와 같이 작동 하는지 확인 합니다. 여전히 문제가 발생 하는 경우 다음 섹션을 계속 진행 하 여 자세한 지침을 확인할 수 있습니다.

> [!TIP]
> 위의 섹션에서 비 마스크 인터럽트 (NMI) 레지스트리 키를 설정 하는 것이 좋습니다. 그러나 VM이 정상적으로 부팅 되지 않은 경우에는 게스트 OS 레지스트리에 대 한 의도 된 변경이 수행 되지 않았을 수 있습니다. 해당 하는 경우 아래 지침에 따라 수동으로 레지스트리 설정을 추가할 수 있습니다.

### <a name="create-and-access-a-repair-vm"></a>복구 VM 만들기 및 액세스

1. [VM 복구 명령](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md)의 1 ~ 3단계를 사용하여 복구 VM을 준비합니다.
2. 원격 데스크톱 연결를 사용 하 여 복구 VM에 연결 합니다.

### <a name="enable-serial-console-and-memory-dump-collection"></a>직렬 콘솔 및 메모리 덤프 수집 사용

VM을 다시 작성 하기 전에 메모리 덤프 수집 및 직렬 콘솔을 사용 하도록 설정 하는 것이 좋습니다. 이렇게 하려면 다음 스크립트를 실행 합니다. 

1. 관리자 권한 명령 프롬프트 세션을 엽니다(관리자 권한으로 실행). 
2. BCD 저장소 데이터를 나열 하 고 다음 단계에서 사용할 부팅 로더 식별자를 확인 합니다. 
    1. 1 세대 VM의 경우 다음 명령을 입력 하 고 나열 된 식별자를 확인 합니다. 
 
        ```
        bcdedit /store <BOOT PARTITON>:\boot\bcd /enum
        ```
        명령에서를 `<BOOT PARTITON>` 부팅 폴더를 포함 하는 연결 된 디스크의 파티션 문자로 바꿉니다. 

        ![스크린샷은 Windows 부팅 로더 아래에 식별자 번호를 나열 하는 1 세대 VM에 BCD 저장소를 나열 하는 출력을 보여 줍니다.](media/windows-stop-error-hardware-malfunction/windows-stop-error-hardware-malfunction-3.png)
    2. 2 세대 VM의 경우 다음 명령을 입력 하 고 나열 된 식별자를 확인 합니다.
    
        ```
        BCDEDIT /store <LETTER OF THE EFI SYSTEM PARTITION>:EFI\Microsoft\boot\bcd /enum 
        ```
        * 명령에서를 `<LETTER OF THE EFI SYSTEM PARTITION>` EFI 시스템 파티션의 문자로 바꿉니다.
        * *EFI 시스템 파티션* 으로 레이블이 지정 된 적절 한 시스템 파티션을 식별 하는 디스크 관리 콘솔을 시작 하는 것이 유용할 수 있습니다.
        * 식별자는 고유한 GUID 이거나 기본 *bootmgr* 일 수 있습니다.
3. 다음 명령을 실행 하 여 직렬 콘솔을 사용 하도록 설정 합니다.

    ```
    BCDEDIT /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /ems {<BOOT LOADER IDENTIFIER>} ON  
    BCDEDIT /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200 

    ```
    * 명령에서를 `<VOLUME LETTER WHERE THE BCD FOLDER IS>` BCD 폴더의 문자로 바꿉니다.
    * 명령에서을 `<BOOT LOADER IDENTIFIER>` 이전 단계에서 찾은 식별자로 바꿉니다.
4. OS 디스크의 사용 가능한 공간이 VM의 메모리 크기 (RAM) 보다 큰지 확인 합니다. 
    1. OS 디스크에 공간이 부족 한 경우 메모리 덤프 파일이 생성 될 위치를 변경 해야 합니다. OS 디스크에서 파일을 만드는 대신 사용 가능한 공간이 충분 한 VM에 연결 된 다른 데이터 디스크를 참조할 수 있습니다. 위치를 변경 하려면 아래 나열 된 명령에서 **% SystemRoot%** 를 데이터 디스크의 드라이브 문자 (예 **: F:**)로 바꿉니다. 
    2. 아래 명령을 입력 합니다 (제안 덤프 구성).

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
### <a name="rebuild-the-virtual-machine"></a>가상 컴퓨터 다시 빌드

* [VM 복구 명령의 5단계](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md#repair-process-example)를 사용하여 VM을 다시 빌드합니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Azure 가상 머신 부팅 오류 문제 해결](./boot-error-troubleshoot.md)