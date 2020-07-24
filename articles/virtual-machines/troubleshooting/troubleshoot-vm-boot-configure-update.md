---
title: VM 시작이 다음 상태에서 중지되었습니다. "Windows가 준비 중입니다. Azure에서 컴퓨터를 끄지 마세요" | Microsoft Docs
description: 다음의 문제 해결을 위한 단계를 소개합니다. VM 시작이 다음 상태에서 중지되었습니다. "Windows가 준비 중입니다. 컴퓨터를 끄지 마세요."
services: virtual-machines-windows
documentationcenter: ''
author: Deland-Han
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 09/18/2018
ms.author: delhan
ms.openlocfilehash: d7e56fe36af3d841cfd888dd6c1bf05502837cdd
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/23/2020
ms.locfileid: "87079845"
---
# <a name="vm-startup-is-stuck-on-getting-windows-ready-dont-turn-off-your-computer-in-azure"></a>VM 시작이 다음 상태에서 중지되었습니다. "Windows가 준비 중입니다. Azure에서 컴퓨터를 끄지 마세요."

이 문서에서는 Microsoft Azure에서 Windows VM (가상 머신)을 부팅할 때 발생할 수 있는 "준비 하기" 및 "Windows 준비" 화면에 대해 설명 합니다. 지원 티켓에 대한 데이터를 수집할 수 있도록 하는 단계를 제공합니다.

 

## <a name="symptoms"></a>증상

Windows VM은 부팅 되지 않습니다. **부팅 진단을** 사용 하 여 vm의 스크린샷을 가져오는 경우 vm에 "준비 중" 또는 "Windows 준비 중" 메시지가 표시 될 수 있습니다.

![Windows Server 2012 R2에 대한 메시지 예제](./media/troubleshoot-vm-configure-update-boot/message1.png)

![메시지 예제](./media/troubleshoot-vm-configure-update-boot/message2.png)

## <a name="cause"></a>원인

일반적으로 이 문제는 구성이 변경된 후 서버가 마지막 다시 부팅을 수행하는 동안 발생합니다. 구성 변경은 Windows 업데이트로 인해 또는 서버 역할/기능 변경으로 인해 초기화될 수 있습니다. Windows 업데이트의 경우, 업데이트의 크기가 크면 운영 체제가 변경 내용을 다시 구성하는 데 더 많은 시간이 걸립니다.

## <a name="collect-an-os-memory-dump"></a>OS 메모리 덤프 수집

처리가 변경 될 때까지 기다린 후 문제가 해결 되지 않으면 메모리 덤프 파일을 수집 하 여 지원 담당자에 게 문의 해야 합니다. 덤프 파일을 수집하려면 다음 단계를 수행합니다.

### <a name="attach-the-os-disk-to-a-recovery-vm"></a>복구 VM에 OS 디스크 연결

1. 영향을 받는 VM의 OS 디스크 스냅샷을 백업으로 만듭니다. 자세한 내용은 [디스크 스냅샷](../windows/snapshot-copy-managed-disk.md)을 참조하세요.
2. [OS 디스크를 복구 VM에 연결](./troubleshoot-recovery-disks-portal-windows.md)합니다.
3. 복구 VM에 원격 데스크톱을 연결합니다. 
4. OS 디스크가 암호화 된 경우 다음 단계로 이동 하기 전에 암호화를 해제 해야 합니다. 자세한 내용은 [부팅할 수 없는 VM에서 암호화 된 OS 디스크 암호 해독](troubleshoot-bitlocker-boot-error.md#solution)을 참조 하세요.

### <a name="locate-dump-file-and-submit-a-support-ticket"></a>덤프 파일을 찾아서 지원 티켓을 제출

1. 복구 VM에서 연결된 OS 디스크의 Windows 폴더로 이동합니다. 연결된 OS 디스크에 할당된 드라이브 문자가 F인 경우 F:\Windows로 이동해야 합니다.
2. Memory.dmp 파일을 찾은 다음 덤프 파일을 사용 하 여 [지원 티켓을 제출](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) 합니다. 

덤프 파일을 찾을 수 없는 경우 덤프 로그 및 직렬 콘솔을 사용하도록 설정하는 단계로 이동합니다.

### <a name="enable-dump-log-and-serial-console"></a>덤프 로그 및 직렬 콘솔을 사용하도록 설정

덤프 로그 및 직렬 콘솔을 사용하도록 설정하려면 다음 스크립트를 실행합니다.

1. 관리자 권한 명령 프롬프트 세션을 엽니다(관리자 권한으로 실행).
2. 다음 스크립트를 실행합니다.

    이 스크립트에서 연결된 OS 디스크에 할당된 드라이브 문자가 F라고 가정합니다. 이를 VM에서 적절한 값으로 바꿉니다.

    ```powershell
    reg load HKLM\BROKENSYSTEM F:\windows\system32\config\SYSTEM.hiv

    REM Enable Serial Console
    bcdedit /store F:\boot\bcd /set {bootmgr} displaybootmenu yes
    bcdedit /store F:\boot\bcd /set {bootmgr} timeout 5
    bcdedit /store F:\boot\bcd /set {bootmgr} bootems yes
    bcdedit /store F:\boot\bcd /ems {<BOOT LOADER IDENTIFIER>} ON
    bcdedit /store F:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200

    REM Suggested configuration to enable OS Dump
    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f
    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f
    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f

    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f
    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f
    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f

    reg unload HKLM\BROKENSYSTEM
    ```

    1. RAM처럼 많은 메모리를 할당할 디스크에 충분한 공간이 있는지 확인합니다. 이 VM에 대해 선택하는 크기에 따라 달라집니다.
    2. 충분한 공간이 없거나 큰 크기의 VM인 경우(G, GS 또는 E 시리즈) 이 파일이 생성될 위치를 변경하고 VM에 연결된 다른 모든 데이터 디스크를 참조할 수 있습니다. 이를 위해 다음 키를 변경해야 합니다.
    
        ```console
        reg load HKLM\BROKENSYSTEM F:\windows\system32\config\SYSTEM.hiv

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "<DRIVE LETTER OF YOUR DATA DISK>:\MEMORY.DMP" /f
        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "<DRIVE LETTER OF YOUR DATA DISK>:\MEMORY.DMP" /f

        reg unload HKLM\BROKENSYSTEM
        ```

3. [OS 디스크를 분리한 다음, OS 디스크를 영향을 받는 VM에 다시 연결합니다](./troubleshoot-recovery-disks-portal-windows.md).
4. VM을 시작 하 고 직렬 콘솔에 액세스 합니다.
5. 메모리 덤프를 트리거하기 위해 **비 마스크 인터럽트 보내기 (NMI)** 를 선택 합니다.
    ![마스크 불가능 인터럽트를 보낼 위치에 대 한 이미지](./media/troubleshoot-vm-configure-update-boot/run-nmi.png)
6. OS 디스크를 복구 VM에 다시 연결 하 고 덤프 파일을 수집 합니다.

## <a name="contact-microsoft-support"></a>Microsoft 지원에 문의

덤프 파일을 수집한 후 [Microsoft 고객 지원팀](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)에 문의하여 근본 원인을 분석합니다.
