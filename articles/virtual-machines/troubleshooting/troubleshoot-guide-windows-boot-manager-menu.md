---
title: Windows 부팅 관리자로 인해 windows 가상 머신을 부팅할 수 없습니다.
description: 이 문서에서는 Windows 부팅 관리자에서 Azure 가상 컴퓨터의 부팅을 방지 하는 문제를 해결 하는 단계를 제공 합니다.
services: virtual-machines-windows
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: a97393c3-351d-4324-867d-9329e31b3598
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.topic: troubleshooting
ms.date: 03/26/2020
ms.author: v-mibufo
ms.openlocfilehash: 5d2fb62870e2c41af635627f5d692f08c67f8394
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "80373350"
---
# <a name="windows-vm-cannot-boot-due-to-windows-boot-manager"></a>Windows 부팅 관리자로 인해 windows VM을 부팅할 수 없습니다.

이 문서에서는 Windows 부팅 관리자에서 Azure VM (가상 머신)을 부팅할 수 없는 문제를 해결 하는 단계를 제공 합니다.

## <a name="symptom"></a>증상

VM이 사용자 프롬프트에 대 한 대기 상태를 유지 하 고 수동으로 지시 하지 않는 한 부팅 되지 않습니다.

[부팅 진단을](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/boot-diagnostics) 사용 하 여 VM의 스크린샷을 볼 경우 스크린샷에 Windows 부팅 관리자가 표시 됩니다. 메시지가 표시 되 면 *시작할 운영 체제를 선택 하거나 tab 키를 눌러 도구를 선택*합니다.

그림 1
 
!["시작할 운영 체제를 선택 하거나 TAB 키를 눌러 도구를 선택 하십시오." 라는 Windows 부팅 관리자](media/troubleshoot-guide-windows-boot-manager-menu/1.jpg)

## <a name="cause"></a>원인

이 오류는 Windows 부팅 관리자의 BCD 플래그 *displaybootmenu* 때문에 발생 합니다. 플래그를 사용 하는 경우 Windows 부팅 관리자는 부팅 프로세스 동안 사용자에 게 실행 하려는 로더를 선택 하 라는 메시지를 표시 하 여 부팅 지연을 발생 시킵니다. Azure에서이 기능은 VM을 부팅 하는 데 걸리는 시간에 추가할 수 있습니다.

## <a name="solution"></a>해결 방법

프로세스 개요:

1. 직렬 콘솔을 사용 하 여 더 빠른 부팅 시간을 구성 합니다.
2. 복구 VM을 만들고 액세스합니다.
3. 복구 VM에서 더 빠른 부팅 시간을 구성 합니다.
4. **권장**: VM을 다시 빌드하기 전에 직렬 콘솔과 메모리 덤프 수집을 사용하도록 설정합니다.
5. VM을 다시 빌드합니다.

### <a name="configure-for-faster-boot-time-using-serial-console"></a>직렬 콘솔을 사용 하 여 더 빠른 부팅 시간 구성

직렬 콘솔에 액세스할 수 있는 경우 두 가지 방법으로 부팅 시간을 단축할 수 있습니다. *Displaybootmenu* 대기 시간을 줄이거나 플래그를 모두 제거 합니다.

1. 지침에 따라 [Windows 용 Azure 직렬 콘솔](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-windows) 에 액세스 하 여 텍스트 기반 콘솔에 대 한 액세스 권한을 얻습니다.

   > [!NOTE]
   > 직렬 콘솔에 액세스할 수 없는 경우 [복구 VM 만들기 및 액세스](#create-and-access-a-repair-vm)로 건너뜁니다.

2. **옵션 A**: 대기 시간 단축

   a. 대기 시간은 기본적으로 30 초로 설정 되지만 더 빠른 시간 (예: 5 초)으로 변경할 수 있습니다.

   b. 직렬 콘솔에서 다음 명령을 사용 하 여 시간 제한 값을 조정 합니다.

      `bcdedit /set {bootmgr} timeout 5`

3. **옵션 B**: BCD 플래그 제거

   a. 부팅 메뉴 프롬프트 표시를 모두 방지 하려면 다음 명령을 입력 합니다.

      `bcdedit /deletevalue {bootmgr} displaybootmenu`

      > [!NOTE]
      > 위의 단계에서 직렬 콘솔을 사용 하 여 더 빠른 부팅 시간을 구성할 수 없는 경우, 대신 다음 단계를 계속 진행할 수 있습니다. 이제이 문제를 해결 하기 위해 오프 라인 모드에서 문제를 해결 합니다.

### <a name="create-and-access-a-repair-vm"></a>복구 VM 만들기 및 액세스

1. [VM 복구 명령의 1~3단계](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands)를 사용하여 복구 VM을 준비합니다.
2. 원격 데스크톱 연결를 사용 하 여 복구 VM에 연결 합니다.

### <a name="configure-for-faster-boot-time-on-a-repair-vm"></a>복구 VM의 부팅 시간을 단축 하도록 구성

1. 관리자 권한 명령 프롬프트를 엽니다.
2. 다음을 입력 하 여 DisplayBootMenu를 사용 하도록 설정 합니다.

   **1 세대 vm**에 대해이 명령을 사용 합니다.

   `bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /set {bootmgr} displaybootmenu yes`

   **2 세대 vm**에 대해이 명령을 사용 합니다.

   `bcdedit /store <VOLUME LETTER OF EFI SYSTEM PARTITION>:EFI\Microsoft\boot\bcd /set {bootmgr} displaybootmenu yes`

   "< 텍스트를 >."와 같이 기호 안에 있는 텍스트 뿐만 아니라 보다 큼 또는 보다 작음 기호를 대체 합니다.

3. 제한 시간 값을 5 초로 변경 합니다.

   **1 세대 vm**에 대해이 명령을 사용 합니다.

   `bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /set {bootmgr} timeout 5`

   **2 세대 vm**에 대해이 명령을 사용 합니다.

   `bcdedit /store <VOLUME LETTER OF EFI SYSTEM PARTITION>:EFI\Microsoft\boot\bcd /set {bootmgr} timeout 5`

   "< 텍스트를 >."와 같이 기호 안에 있는 텍스트 뿐만 아니라 보다 큼 또는 보다 작음 기호를 대체 합니다.

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