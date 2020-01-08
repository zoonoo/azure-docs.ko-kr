---
title: 서비스를 다시 시작 하거나 종료 하거나 서비스를 중지 하는 경우 Azure Virtual Machines 다시 시작이 중지 됩니다. | Microsoft Docs
description: 이 문서는 Azure Windows Virtual Machines의 서비스 오류 문제를 해결 하는 데 도움이 됩니다.
services: virtual-machines-windows
documentationCenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 12/19/2019
ms.author: tibasham
ms.openlocfilehash: db7b26402170236843891799738088b9229e4693
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75477409"
---
# <a name="azure-windows-vm-restart-is-stuck-on-restarting-shutting-down-or-stopping-services"></a>Azure Windows VM 다시 시작이 "다시 시작", "종료 중" 또는 "서비스 중지"에 걸려 있습니다.

이 문서에서는 Microsoft Azure에서 Windows VM (가상 머신)을 다시 부팅할 때 발생할 수 있는 "다시 시작", "종료" 또는 "서비스 중지" 메시지의 문제를 해결 하는 단계를 제공 합니다.

## <a name="symptoms"></a>증상

[부팅 진단을](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/boot-diagnostics) 사용 하 여 VM의 스크린샷을 볼 때 스크린샷에 "다시 시작 중", "종료 중" 또는 "서비스 중지 중" 메시지가 표시 될 수 있습니다.

![서비스 화면 다시 시작, 종료 및 중지](./media/boot-error-troubleshooting-windows/restart-shut-down-stop-service.png)
 
## <a name="cause"></a>원인

Windows에서는 시스템 유지 관리 작업을 수행 하 고 업데이트, 역할 및 기능과 같은 변경 내용을 처리 하기 위해 종료 프로세스를 사용 합니다. 이 중요 한 프로세스는 완료 될 때까지 중단 하지 않는 것이 좋습니다. 업데이트/변경 및 VM 크기의 수에 따라 프로세스에 시간이 오래 걸릴 수 있습니다. 프로세스가 중지 되 면 OS가 손상 될 수 있습니다. 과도 하 게 시간이 오래 걸리는 경우에만 프로세스를 중단 합니다.

## <a name="solution"></a>솔루션

### <a name="collect-a-process-memory-dump"></a>프로세스 메모리 덤프 수집

1. 동일한 지역의 작업 중인 VM에 연결 된 새 데이터 디스크 또는 기존 데이터 디스크로 [Procdump 도구](http://download.sysinternals.com/files/Procdump.zip) 를 다운로드 합니다.

2. 작업 중인 VM에서 필요한 파일을 포함 하는 디스크를 분리 하 고 손상 된 VM에 디스크를 연결 합니다. 이 디스크를 **유틸리티 디스크로**호출 하 고 있습니다.

[직렬 콘솔](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-windows) 을 사용 하 여 다음 단계를 완료 합니다.

1. 관리 Powershell을 열고 중지할 때 중지 된 서비스를 확인 합니다.

   ``
   Get-Service | Where-Object {$_.Status -eq "STOP_PENDING"}
   ``

2. 관리 CMD에서 정지 된 서비스의 PID를 가져옵니다.

   ``
   tasklist /svc | findstr /i <STOPING SERVICE>
   ``

3. <STOPPING SERVICE>정지 된 프로세스에서 메모리 덤프 샘플을 가져옵니다.

   ``
   procdump.exe -s 5 -n 3 -ma <PID>
   ``

4. 이제 중지 된 프로세스를 중지 하 여 종료 프로세스의 잠금을 해제 합니다.

   ``
   taskkill /PID <PID> /t /f
   ``

OS가 다시 시작 되 면 정상적으로 부팅 되는 경우 OS 일관성이 양호 한지 확인 합니다. 손상이 보고 되 면 디스크가 손상 될 때까지 다음 명령을 실행 합니다.

``
dism /online /cleanup-image /restorehealth
``

프로세스 메모리 덤프를 수집할 수 없거나이 문제가 재귀적 이며 근본 원인 분석이 필요한 경우 지원 요청을 계속 진행 하 여 아래의 OS 메모리 덤프 수집을 진행 합니다.

### <a name="collect-an-os-memory-dump"></a>OS 메모리 덤프 수집

처리가 변경 될 때까지 기다린 후 문제가 해결 되지 않으면 메모리 덤프 파일을 수집 하 여 지원 담당자에 게 문의 해야 합니다. 덤프 파일을 수집하려면 다음 단계를 수행합니다.

**OS 디스크를 복구 VM에 연결**

1. 영향을 받는 VM의 OS 디스크 스냅샷을 백업으로 만듭니다. 자세한 내용은 [디스크 스냅샷](https://docs.microsoft.com/azure/virtual-machines/windows/snapshot-copy-managed-disk)을 참조하세요.

2. [복구 VM에 OS 디스크를 연결합니다](https://docs.microsoft.com/azure/virtual-machines/windows/troubleshoot-recovery-disks-portal).

3. 복구 VM에 원격 데스크톱을 연결합니다.

4. OS 디스크가 암호화 된 경우 다음 단계로 이동 하기 전에 암호화를 해제 해야 합니다. 자세한 내용은 [부팅할 수 없는 VM에서 암호화 된 OS 디스크 암호 해독](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/troubleshoot-bitlocker-boot-error#solution)을 참조 하세요.

**덤프 파일을 찾고 지원 티켓을 제출 합니다.**

1. 복구 VM에서 연결된 OS 디스크의 Windows 폴더로 이동합니다. 연결된 OS 디스크에 할당된 드라이브 문자가 F인 경우 F:\Windows로 이동해야 합니다.

2. Memory.dmp 파일을 찾은 다음 덤프 파일을 사용 하 여 [지원 티켓을 제출](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) 합니다.

덤프 파일을 찾을 수 없는 경우 덤프 로그 및 직렬 콘솔을 사용하도록 설정하는 단계로 이동합니다.

**덤프 로그 및 직렬 콘솔 사용**

덤프 로그 및 직렬 콘솔을 사용하도록 설정하려면 다음 스크립트를 실행합니다.

1. 관리자 권한 명령 프롬프트 세션을 엽니다(관리자 권한으로 실행).

2. 다음 스크립트를 실행합니다.

   이 스크립트에서는 연결 된 OS 디스크에 할당 된 드라이브 문자가 F 인 것으로 가정 합니다. VM의 적절 한 값으로 대체 합니다.

   ```
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
   REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v NMICrashDump / t REG_DWORD /d 1 /f
   
   reg unload HKLM\BROKENSYSTEM
   ```

3. RAM으로 많은 메모리를 할당할 수 있는 공간이 디스크에 충분 한지 확인 합니다 .이는이 VM에 대해 선택 하는 크기에 따라 달라 집니다.

4. 공간이 부족 하거나 VM이 클 경우 (G, GS 또는 E 시리즈)이 파일이 생성 될 위치를 변경 하 고 VM에 연결 된 다른 데이터 디스크를 참조할 수 있습니다. 위치를 변경 하려면 다음 키를 변경 해야 합니다.

   ```
   reg load HKLM\BROKENSYSTEM F:\windows\system32\config\SYSTEM.hiv

   REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "<DRIVE LETTER OF YOUR DATA DISK>:\MEMORY.DMP" /f
   REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "<DRIVE LETTER OF YOUR DATA DISK>:\MEMORY.DMP" /f
   
   reg unload HKLM\BROKENSYSTEM
   ```

5. [Os 디스크를 분리 한 다음, 영향을 받는 VM에 os 디스크를 다시](https://docs.microsoft.com/azure/virtual-machines/windows/troubleshoot-recovery-disks-portal)연결 합니다.

6. VM을 시작 하 고 직렬 콘솔에 액세스 합니다.

7. 메모리 덤프를 트리거하기 위해 비 마스크 인터럽트 보내기 (NMI)를 선택 합니다.

   ![마스크 불가능 인터럽트 보내기](./media/boot-error-troubleshooting-windows/send-nonmaskable-interrupt.png)

8. OS 디스크를 복구 VM에 다시 연결 하 고 덤프 파일을 수집 합니다.

## <a name="contact-microsoft-support"></a>Microsoft 지원에 문의

덤프 파일을 수집한 후에는 Microsoft 지원에 문의 하 여 근본 원인을 확인 하십시오.
