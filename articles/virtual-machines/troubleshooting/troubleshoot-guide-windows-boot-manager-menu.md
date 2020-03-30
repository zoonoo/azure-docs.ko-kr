---
title: Windows 가상 컴퓨터는 Windows 부팅 관리자로 인해 부팅할 수 없습니다.
description: 이 문서에서는 Windows 부팅 관리자가 Azure 가상 컴퓨터의 부팅을 방지하는 문제를 해결하는 단계를 제공합니다.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "80373350"
---
# <a name="windows-vm-cannot-boot-due-to-windows-boot-manager"></a>윈도우 VM 인해 윈도우 부팅 관리자를 부팅할 수 없습니다

이 문서에서는 Windows 부팅 관리자가 Azure 가상 컴퓨터(VM)의 부팅을 방지하는 문제를 해결하는 단계를 제공합니다.

## <a name="symptom"></a>증상

VM은 사용자 프롬프트를 기다리지 않고 수동으로 지시하지 않는 한 부팅되지 않습니다.

[부팅 진단을](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/boot-diagnostics) 사용하여 VM의 스크린샷을 볼 때 스크린샷에 Windows 부팅 관리자가 표시되어 *시작운영 체제를 선택하거나 탭을 눌러 도구를 선택하는*것을 볼 수 있습니다.

그림 1
 
![Windows 부팅 관리자 "시작할 운영 체제를 선택하거나 탭을 눌러 도구를 선택합니다."](media/troubleshoot-guide-windows-boot-manager-menu/1.jpg)

## <a name="cause"></a>원인

이 오류는 Windows 부팅 관리자의 BCD 플래그 *디스플레이부트메뉴* 때문입니다. 플래그를 사용하도록 설정하면 Windows Boot Manager는 부팅 프로세스 중에 실행하려는 로더를 선택하라는 메시지를 사용자에게 표시하여 부팅 지연을 일으킵니다. Azure에서 이 기능은 VM을 부팅하는 데 걸리는 시간을 추가할 수 있습니다.

## <a name="solution"></a>해결 방법

프로세스 개요:

1. 직렬 콘솔을 사용하여 더 빠른 부팅 시간을 위해 구성합니다.
2. 복구 VM을 만들고 액세스합니다.
3. 복구 VM에서 더 빠른 부팅 시간을 위해 구성합니다.
4. **권장**: VM을 다시 빌드하기 전에 직렬 콘솔 및 메모리 덤프 컬렉션을 사용하도록 설정합니다.
5. VM을 다시 빌드합니다.

### <a name="configure-for-faster-boot-time-using-serial-console"></a>직렬 콘솔을 사용하여 더 빠른 부팅 시간을 위해 구성

직렬 콘솔에 액세스할 수 있는 경우 부팅 시간을 단축할 수 있는 두 가지 방법이 있습니다. *디스플레이부트메뉴* 대기 시간을 줄이거나 플래그를 완전히 제거합니다.

1. 지침에 따라 [Windows용 Azure 직렬 콘솔에](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-windows) 액세스하여 텍스트 기반 콘솔에 액세스할 수 있습니다.

   > [!NOTE]
   > 직렬 콘솔에 액세스할 수 없는 경우 미리 건너뛰고 [VM 복구 를 만듭니다.](#create-and-access-a-repair-vm)

2. **옵션 A**: 대기 시간 단축

   a. 대기 시간은 기본적으로 30초로 설정되지만 더 빠른 시간(예: 5초)으로 변경할 수 있습니다.

   b. 직렬 콘솔에서 다음 명령을 사용하여 시간 시간 값을 조정합니다.

      `bcdedit /set {bootmgr} timeout 5`

3. **옵션 B**: BCD 플래그 제거

   a. 표시 부팅 메뉴 프롬프트를 완전히 방지하려면 다음 명령을 입력합니다.

      `bcdedit /deletevalue {bootmgr} displaybootmenu`

      > [!NOTE]
      > 위의 단계에서 직렬 콘솔을 사용하여 더 빠른 부팅 시간을 구성할 수 없는 경우 다음 단계를 계속할 수 있습니다. 이제 오프라인 모드에서 문제를 해결하여 이 문제를 해결합니다.

### <a name="create-and-access-a-repair-vm"></a>복구 VM 만들기 및 액세스

1. [VM 복구 명령의 1-3단계를](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands) 사용하여 복구 VM을 준비합니다.
2. 복구 VM에 원격 데스크톱 연결 연결을 사용합니다.

### <a name="configure-for-faster-boot-time-on-a-repair-vm"></a>복구 VM에서 더 빠른 부팅 시간을 위해 구성

1. 관리자 권한 명령 프롬프트를 엽니다.
2. 디스플레이 부트 메뉴를 사용 하려면 다음을 입력 합니다.

   **1세대 VM에**이 명령을 사용합니다.

   `bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /set {bootmgr} displaybootmenu yes`

   **2세대 VM에**이 명령을 사용합니다.

   `bcdedit /store <VOLUME LETTER OF EFI SYSTEM PARTITION>:EFI\Microsoft\boot\bcd /set {bootmgr} displaybootmenu yes`

   "> 텍스트< 와 같이 기호보다 크거나 적은 기호와 그 안에 있는 텍스트를 바꿉습니다.

3. 시간 시간 값을 5초로 변경합니다.

   **1세대 VM에**이 명령을 사용합니다.

   `bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /set {bootmgr} timeout 5`

   **2세대 VM에**이 명령을 사용합니다.

   `bcdedit /store <VOLUME LETTER OF EFI SYSTEM PARTITION>:EFI\Microsoft\boot\bcd /set {bootmgr} timeout 5`

   "> 텍스트< 와 같이 기호보다 크거나 적은 기호와 그 안에 있는 텍스트를 바꿉습니다.

### <a name="recommended-before-you-rebuild-the-vm-enable-serial-console-and-memory-dump-collection"></a>권장: VM을 다시 빌드하기 전에 직렬 콘솔 및 메모리 덤프 컬렉션을 사용하도록 설정합니다.

메모리 덤프 컬렉션 및 직렬 콘솔을 사용하려면 다음 스크립트를 실행합니다.

1. 높은 명령 프롬프트 세션을 엽니다(관리자로 실행).
2. 다음 명령을 실행합니다.

   직렬 콘솔 사용

   `bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /ems {<BOOT LOADER IDENTIFIER>} ON`

   `bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200`

   "> 텍스트< 와 같이 기호보다 크거나 적은 기호와 그 안에 있는 텍스트를 바꿉습니다.

3. OS 디스크의 사용 가능한 공간이 VM의 RAM(메모리 크기)만큼인지 확인합니다.

   OS 디스크에 공간이 충분하지 않으면 메모리 덤프 파일이 생성될 위치를 변경하고 여유 공간이 충분한 VM에 연결된 데이터 디스크를 참조해야 합니다. 위치를 변경하려면 아래 명령의 데이터 디스크의 드라이브 문자(예: "F:")로 "%SystemRoot%"를 바꿉니다.

#### <a name="suggested-configuration-to-enable-os-dump"></a>OS 덤프를 사용하도록 설정하는 권장 구성

**로드 깨진 OS 디스크**:

`REG LOAD HKLM\BROKENSYSTEM <VOLUME LETTER OF BROKEN OS DISK>:\windows\system32\config\SYSTEM`

**제어Set001에서 사용:**

`REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f`

`REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f`

`REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f`

**제어Set002에서 사용:**

`REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f`

`REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f`

`REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f`

**깨진 OS 디스크언지 언로드:**

`REG UNLOAD HKLM\BROKENSYSTEM`

### <a name="rebuild-the-original-vm"></a>원래 VM 다시 빌드

[VM 복구 명령의 5단계를](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands#repair-process-example) 사용하여 VM을 다시 어셈블합니다.