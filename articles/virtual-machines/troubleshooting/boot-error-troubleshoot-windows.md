---
title: Azure 가상 시스템 종료가 서비스를 다시 시작, 종료 또는 중지할 때 중단됨 | 마이크로 소프트 문서
description: 이 문서에서는 Azure Windows 가상 컴퓨터에서 서비스 오류를 해결하는 데 도움이 됩니다.
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
ms.openlocfilehash: 5d6396efc9ab25baa0d32e7c33c7715863516249
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77371363"
---
# <a name="azure-windows-vm-shutdown-is-stuck-on-restarting-shutting-down-or-stopping-services"></a>Azure Windows VM 종료가 "다시 시작", "종료" 또는 "서비스 중지"에 갇혀 있습니다.

이 문서에서는 Microsoft Azure에서 Windows 가상 시스템(VM)을 재부팅할 때 발생할 수 있는 "다시 시작", "종료" 또는 "서비스 중지" 메시지의 문제를 해결하는 단계를 제공합니다.

## <a name="symptoms"></a>증상

[부팅 진단을](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/boot-diagnostics) 사용하여 VM의 스크린샷을 볼 때 스크린샷에 "다시 시작", "종료" 또는 "서비스 중지"라는 메시지가 표시될 수 있습니다.

![서비스 화면 다시 시작, 종료 및 중지](./media/boot-error-troubleshooting-windows/restart-shut-down-stop-service.png)
 
## <a name="cause"></a>원인

Windows는 종료 프로세스를 사용하여 시스템 유지 관리 작업을 수행하고 업데이트, 역할 및 기능과 같은 변경 내용을 처리합니다. 이 중요한 프로세스가 완료될 때까지 중단하지 않는 것이 좋습니다. 업데이트/변경 횟수및 VM 크기에 따라 프로세스에 시간이 오래 걸릴 수 있습니다. 프로세스가 중지되면 OS가 손상될 수 있습니다. 지나치게 오래 걸리는 경우에만 프로세스를 중단합니다.

## <a name="solution"></a>해결 방법

### <a name="collect-a-process-memory-dump"></a>프로세스 메모리 덤프 수집

1. [Procdump 도구를](http://download.sysinternals.com/files/Procdump.zip) 동일한 지역의 작업 VM에 연결된 새 데이터 또는 기존 데이터 디스크에 다운로드합니다.

2. 작업 VM에서 필요한 파일이 포함된 디스크를 분리하고 디스크를 깨진 VM에 연결합니다. 이 디스크를 **유틸리티 디스크라고**합니다.

[직렬 콘솔을](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-windows) 사용하여 다음 단계를 완료합니다.

1. 관리 Powershell을 열고 중지시 중단된 서비스를 확인합니다.

   ``
   Get-Service | Where-Object {$_.Status -eq "STOP_PENDING"}
   ``

2. 관리 CMD에서 중단된 서비스의 PID를 가져옵니다.

   ``
   tasklist /svc | findstr /i <STOPING SERVICE>
   ``

3. 중단 된 프로세스에서 <STOPPING SERVICE>메모리 덤프 샘플을 가져옵니다.

   ``
   procdump.exe -s 5 -n 3 -ma <PID>
   ``

4. 이제 중단 된 프로세스를 종료 프로세스의 잠금을 해제합니다.

   ``
   taskkill /PID <PID> /t /f
   ``

OS가 다시 시작되면 정상적으로 부팅되면 OS 일관성이 정상인지 확인하십시오. 손상이 보고되면 디스크가 손상되지 않은 때까지 다음 명령을 실행합니다.

``
dism /online /cleanup-image /restorehealth
``

프로세스 메모리 덤프를 수집할 수 없거나 이 문제가 재귀적이고 근본 원인 분석이 필요한 경우 아래의 OS 메모리 덤프 수집을 진행하여 지원 요청을 엽니다.

### <a name="collect-an-os-memory-dump"></a>OS 메모리 덤프 수집

변경 내용이 처리될 때까지 기다린 후에도 문제가 해결되지 않으면 메모리 덤프 파일을 수집하고 지원팀에 문의해야 합니다. 덤프 파일을 수집하려면 다음 단계를 수행합니다.

**복구 VM에 OS 디스크 연결**

1. 영향을 받는 VM의 OS 디스크 스냅샷을 백업으로 만듭니다. 자세한 내용은 [디스크 스냅샷](https://docs.microsoft.com/azure/virtual-machines/windows/snapshot-copy-managed-disk)을 참조하세요.

2. [복구 VM에 OS 디스크를 연결합니다.](https://docs.microsoft.com/azure/virtual-machines/windows/troubleshoot-recovery-disks-portal)

3. 복구 VM에 원격 데스크톱을 연결합니다.

4. OS 디스크가 암호화된 경우 다음 단계로 이동하기 전에 암호화를 해제해야 합니다. 자세한 내용은 [부팅할 수 없는 VM의 암호화된 OS 디스크 해독을](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/troubleshoot-bitlocker-boot-error#solution)참조하십시오.

**덤프 파일을 찾아서 지원 티켓을 제출**

1. 복구 VM에서 연결된 OS 디스크의 Windows 폴더로 이동합니다. 연결된 OS 디스크에 할당된 드라이브 문자가 F인 경우 F:\Windows로 이동해야 합니다.

2. memory.dmp 파일을 찾은 다음 덤프 [파일로 지원 티켓을 제출합니다.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)

덤프 파일을 찾을 수 없는 경우 덤프 로그 및 직렬 콘솔을 사용하도록 설정하는 단계로 이동합니다.

**덤프 로그 및 직렬 콘솔을 사용하도록 설정**

덤프 로그 및 직렬 콘솔을 사용하도록 설정하려면 다음 스크립트를 실행합니다.

1. 관리자 권한 명령 프롬프트 세션을 엽니다(관리자 권한으로 실행).

2. 다음 스크립트를 실행합니다.

   이 스크립트에서는 연결된 OS 디스크에 할당된 드라이브 문자가 F. VM의 적절한 값으로 교체한다고 가정합니다.

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

3. 이 VM에 대해 선택하는 크기에 따라 달라지므로 디스크에 RAM만큼 메모리를 할당할 수 있는 공간이 충분한지 확인합니다.

4. 공간이 충분하지 않거나 VM이 큰 경우(G, GS 또는 E 계열) 이 파일이 생성될 위치를 변경하고 VM에 연결된 다른 데이터 디스크를 참조할 수 있습니다. 위치를 변경하려면 다음 키를 변경해야 합니다.

   ```
   reg load HKLM\BROKENSYSTEM F:\windows\system32\config\SYSTEM.hiv

   REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "<DRIVE LETTER OF YOUR DATA DISK>:\MEMORY.DMP" /f
   REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "<DRIVE LETTER OF YOUR DATA DISK>:\MEMORY.DMP" /f
   
   reg unload HKLM\BROKENSYSTEM
   ```

5. [OS 디스크를 분리한 다음 영향을 받는 VM에 OS 디스크를 다시 연결합니다.](https://docs.microsoft.com/azure/virtual-machines/windows/troubleshoot-recovery-disks-portal)

6. VM을 시작하고 직렬 콘솔에 액세스합니다.

7. 메모리 덤프를 트리거하려면 NMI(비마스크 인터럽트) 보내기를 선택합니다.

   ![마스크할 수 없는 인터럽트 보내기](./media/boot-error-troubleshooting-windows/send-nonmaskable-interrupt.png)

8. OS 디스크를 복구 VM에 다시 연결하고 덤프 파일을 수집합니다.

## <a name="contact-microsoft-support"></a>Microsoft 지원에 문의

덤프 파일을 수집한 후 Microsoft 지원에 문의하여 근본 원인을 확인합니다.
