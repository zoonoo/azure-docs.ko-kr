---
title: VM이 안전 모드로 부팅되어 Azure Virtual Machines에 원격으로 연결할 수 없음 | Microsoft Docs
description: VM이 안전 모드로 부팅되어 VM에 RDP로 연결할 수 없는 문제 해결 방법을 알아봅니다.| Microsoft Docs
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: cshepard
editor: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 11/13/2018
ms.author: genli
ms.openlocfilehash: 8e108d88282894a7b1bf014146083008bedd483d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60319482"
---
#  <a name="cannot-rdp-to-a-vm-because-the-vm-boots-into-safe-mode"></a>VM이 안전 모드로 부팅되어 VM에 RDP로 연결할 수 없음

이 문서에서는 VM이 안전 모드로 부팅하도록 구성되어 Azure Windows VM(Virtual Machines)에 연결할 수 없는 문제 해결 방법을 보여줍니다.

> [!NOTE]
> Azure에는 리소스를 만들고 사용하기 위한 [Resource Manager 및 클래식](../../azure-resource-manager/resource-manager-deployment-model.md)이라는 두 가지 배포 모델이 있습니다. 이 문서에서는 Resource Manager 배포 모델 사용에 대해 설명합니다. 이 배포 모델은 클래식 배포 모델 대신 새 배포에 사용하는 것이 좋습니다.

## <a name="symptoms"></a>증상

VM이 안전 모드로 부팅하도록 구성되어 VM에 RDP로 또는 기타 방법(예: HTTP)으로 연결할 수 없습니다. Azure Portal에서 [부팅 진단](../troubleshooting/boot-diagnostics.md)의 스크린샷을 확인하면 VM이 정상적으로 부팅되지만 네트워크 인터페이스를 사용할 수 없는 것이 보일 것입니다.

![안전 모드의 네트워크 인터페이스에 대한 이미지](./media/troubleshoot-rdp-safe-mode/network-safe-mode.png)

## <a name="cause"></a>원인

안전 모드에서는 RDP 서비스를 사용할 수 없습니다. VM이 안전 모드로 부팅되면 핵심 시스템 프로그램 및 서비스만 로드됩니다. 이것은 "Safe Boot minimal" 및 "Safe Boot with connectivity"의 두 가지 안전 모드 버전에 적용됩니다.


## <a name="solution"></a>해결 방법

다음 단계를 수행하기 전에 영향을 받는 VM의 OS 디스크 스냅샷을 백업으로 만듭니다. 자세한 내용은 [디스크 스냅샷](../windows/snapshot-copy-managed-disk.md)을 참조하세요.

이 문제를 해결하려면 직렬 컨트롤을 사용하여 일반 모드로 부팅하도록 VM을 구성하거나 복구 VM을 사용하여 [오프라인으로 VM 복구](#repair-the-vm-offline)를 수행합니다.

### <a name="use-serial-control"></a>직렬 콘솔 사용

1. [직렬 콘솔에 연결하고 CMD 인스턴스를 엽니다](./serial-console-windows.md#use-cmd-or-powershell-in-serial-console
   ). VM에서 직렬 콘솔을 사용하도록 설정되지 않은 경우 [오프라인으로 VM 복구](#repair-the-vm-offline)를 참조하세요.
2. 부팅 구성 데이터를 확인합니다.

        bcdedit /enum

    VM이 안전 모드로 부팅하도록 구성된 경우 **Windows 부팅 로더** 섹션 아래에 **safeboot**라는 추가 플래그가 보입니다. **safeboot** 플래그가 보이지 않으면 VM이 현재 안전 모드가 아닙니다. 이 문서는 고객의 시나리오에 적용되지 않습니다.

    **safeboot** 플래그는 다음 값으로 표시될 수 있습니다.
   - 최소
   - 네트워크

     둘 중 어떤 모드에서도 RDP가 시작되지 않습니다. 따라서 픽스가 동일하게 유지됩니다.

     ![안전 모드 플래그에 대한 이미지](./media/troubleshoot-rdp-safe-mode/safe-mode-tag.png)

3. VM이 일반 모드로 부팅하도록 **safemoade** 플래그를 삭제합니다.

        bcdedit /deletevalue {current} safeboot

4. 부팅 구성 데이터를 검사하여 **safeboot** 플래그가 제거되었는지 확인합니다.

        bcdedit /enum

5. VM을 다시 시작한 다음, 문제가 해결되었는지 확인합니다.

### <a name="repair-the-vm-offline"></a>오프라인으로 VM 복구

#### <a name="attach-the-os-disk-to-a-recovery-vm"></a>복구 VM에 OS 디스크 연결

1. [복구 VM에 OS 디스크를 연결합니다](../windows/troubleshoot-recovery-disks-portal.md).
2. 복구 VM에 대한 원격 데스크톱 연결을 시작합니다.
3. 디스크 관리 콘솔에서 디스크의 플래그가 **온라인**으로 지정되었는지 확인합니다. 연결된 OS 디스크에 할당된 드라이브 문자를 적어 둡니다.

#### <a name="enable-dump-log-and-serial-console-optional"></a>덤프 로그 및 직렬 콘솔을 사용하도록 설정(선택 사항)

덤프 로그 및 직렬 콘솔은 이 문서의 솔루션으로 문제를 해결할 수 없는 경우에 문제 해결을 도와줍니다.

덤프 로그 및 직렬 콘솔을 사용하도록 설정하려면 다음 스크립트를 실행합니다.

1. 관리자 권한 명령 프롬프트 세션을 엽니다(**관리자 권한으로 실행**).
2. 다음 스크립트를 실행합니다.

    이 스크립트에서 연결된 OS 디스크에 할당된 드라이브 문자가 F라고 가정합니다. 이 드라이브 문자를 VM에서 적절한 값으로 바꿉니다.

    ```powershell
    reg load HKLM\BROKENSYSTEM F:\windows\system32\config\SYSTEM

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

#### <a name="configure-the-windows-to-boot-into-normal-mode"></a>Windows를 표준 모드로 부팅되도록 구성합니다.

1. 관리자 권한 명령 프롬프트 세션을 엽니다(**관리자 권한으로 실행**).
2. 부팅 구성 데이터를 확인합니다. 다음 명령에서 연결된 OS 디스크에 할당된 드라이브 문자가 F라고 가정합니다. 이 드라이브 문자를 VM에서 적절한 값으로 바꿉니다.

        bcdedit /store F:\boot\bcd /enum
    **\windows** 폴더가 있는 파티션의 식별자 이름을 적어 둡니다. 기본적으로 식별자 이름은 “기본값”입니다.

    VM이 안전 모드로 부팅하도록 구성된 경우 **Windows 부팅 로더** 섹션 아래에 **safeboot**라는 추가 플래그가 보입니다. **safeboot** 플래그가 표시되지 않는 경우 이 문서는 시나리오에 적용되지 않습니다.

    ![부트 식별자에 대한 이미지](./media/troubleshoot-rdp-safe-mode/boot-id.png)

3. VM이 표준 모드로 부팅되도록 **safeboot** 플래그를 제거합니다.

        bcdedit /store F:\boot\bcd /deletevalue {Default} safeboot
4. 부팅 구성 데이터를 검사하여 **safeboot** 플래그가 제거되었는지 확인합니다.

        bcdedit /store F:\boot\bcd /enum
5. [OS 디스크를 분리하고 VM을 다시 만듭니다](../windows/troubleshoot-recovery-disks-portal.md). 그런 다음, 문제가 해결되었는지 확인합니다.
