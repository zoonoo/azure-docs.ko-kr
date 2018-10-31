---
title: Azure VM에서 원격 데스크톱 서비스가 시작되지 않음 | Microsoft Docs
description: Virtual Machine에 연결할 때 원격 데스크톱 서비스에서 발생하는 문제 해결 방법 알아보기 | Microsoft Docs
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
ms.date: 10/23/2018
ms.author: genli
ms.openlocfilehash: 39b793e2722766f3f28829b4dc48534054abd97e
ms.sourcegitcommit: c2c279cb2cbc0bc268b38fbd900f1bac2fd0e88f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/24/2018
ms.locfileid: "49989021"
---
# <a name="remote-desktop-services-is-not-starting-on-an-azure-vm"></a>Azure VM에서 원격 데스크톱 서비스가 시작되지 않음

이 문서에서는 원격 데스크톱 서비스(TermService)가 시작되지 않거나 시작에 실패하는 경우 Azure VM(Virtual Machine) 연결 문제를 해결하는 방법을 설명합니다.

>[!NOTE]
>Azure에는 리소스를 만들고 작업하는 [Resource Manager와 클래식](../../azure-resource-manager/resource-manager-deployment-model.md)이라는 두 가지 배포 모델이 있습니다. 이 문서에서는 Resource Manager 배포 모델 사용에 대해 설명합니다. 클래식 배포 모델을 사용하는 대신 새 배포에 이 모델을 사용하는 것이 좋습니다.

## <a name="symptoms"></a>증상

VM에 연결하려고 시도할 때 다음과 같은 상황이 발생합니다.

- 이 VM 스크린샷은 운영 체제가 완전히 로드되어 자격 증명을 기다리고 있다는 것을 보여줍니다.
- VM의 모든 응용 프로그램이 예상대로 작동 중이며 액세스 가능합니다.
- VM이 Microsoft RDP(원격 데스크톱 프로토콜) 포트(기본 3389)의 TCP 연결에 응답합니다.
- RDP 연결을 시도할 때 자격 증명을 요청하는 메시지가 표시되지 않습니다.

## <a name="cause"></a>원인

원격 데스크톱 서비스가 Virtual Machine에서 실행되고 있지 않기 때문에 이 문제가 발생합니다. 원인은 다음 시나리오에 따라 달라질 수 있습니다.

- TermService 서비스가 **사용 안 함**으로 설정되었습니다.
- TermService 서비스가 충돌하거나 중단됩니다.

## <a name="solution"></a>해결 방법

이 문제를 해결하려면 다음 솔루션 중 하나를 사용하거나 솔루션을 하나씩 시도합니다.

### <a name="solution-1-using-the-serial-console"></a>솔루션 1: 직렬 콘솔 사용

1. **지원 및 문제 해결** > **직렬 콘솔(미리 보기)** 을 선택하여 [직렬 콘솔](serial-console-windows.md)에 액세스합니다. VM에서 기능을 사용하도록 설정하면 VM을 성공적으로 연결할 수 있습니다.

2. CMD 인스턴스에 대한 새 채널을 만듭니다. **CMD**를 입력하여 채널을 시작하고 채널 이름을 가져옵니다.

3. CMD 인스턴스를 실행하는 채널(이 예에서는 채널 1)로 전환합니다.

   ```
   ch -si 1
   ```

4. **Enter** 키를 다시 누르고 VM의 유효한 사용자 이름 및 암호(로컬 또는 도메인 ID)를 입력합니다.

5. TermService 서비스의 상태를 쿼리합니다.

   ```
   sc query TermService
   ```

6. 서비스 상태가 **중지됨**으로 표시되면 서비스를 시작해 봅니다.

   ```
   sc start TermService
   ```

7. 서비스를 다시 쿼리하여 서비스가 성공적으로 시작되었는지 확인합니다.

   ```
   sc query TermService
   ```

### <a name="solution-2-using-a-recovery-vm-to-enable-the-service"></a>솔루션 2: 복구 VM을 사용하여 서비스를 사용하도록 설정

[OS 디스크를 백업](../windows/snapshot-copy-managed-disk.md)하고 [OS 디스크를 복구 VM에 연결](../windows/troubleshoot-recovery-disks-portal.md)합니다. 그런 다음, 관리자 권한 CMD 인스턴스를 열고 복구 VM에서 다음 스크립트를 실행합니다.

>[!NOTE]
>연결된 OS 디스크에 할당된 드라이브 문자가 F라고 가정하고 VM에서 적절한 값으로 바꿉니다. 여기까지 마쳤으면 복구 VM에서 디스크를 분리하고 [VM을 다시 만듭니다](../windows/create-vm-specialized.md). 직렬 콘솔을 사용하도록 설정되었기 때문에 추가적인 문제 해결에는 **솔루션 1**을 사용하면 됩니다.

```
reg load HKLM\BROKENSYSTEM f:\windows\system32\config\SYSTEM

REM Enable Serial Console
bcdedit /store <Volume Letter Where The BCD Folder Is>:\boot\bcd /set {bootmgr} displaybootmenu yes
bcdedit /store <Volume Letter Where The BCD Folder Is>:\boot\bcd /set {bootmgr} timeout 10
bcdedit /store <Volume Letter Where The BCD Folder Is>:\boot\bcd /set {bootmgr} bootems yes
bcdedit /store <Volume Letter Where The BCD Folder Is>:\boot\bcd /ems {<Boot Loader Identifier>} ON
bcdedit /store <Volume Letter Where The BCD Folder Is>:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200

REM Get the current ControlSet from where the OS is booting
for /f "tokens=3" %x in ('REG QUERY HKLM\BROKENSYSTEM\Select /v Current') do set ControlSet=%x
set ControlSet=%ControlSet:~2,1%

REM Suggested configuration to enable OS Dump
set key=HKLM\BROKENSYSTEM\ControlSet00%ControlSet%\Control\CrashControl
REG ADD %key% /v CrashDumpEnabled /t REG_DWORD /d 2 /f
REG ADD %key% /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f
REG ADD %key% /v NMICrashDump /t REG_DWORD /d 1 /f

REM Set default values back on the broken service
reg add "HKLM\BROKENSYSTEM\ControlSet001\services\<Process Name>" /v start /t REG_DWORD /d <Startup Type> /f
reg add "HKLM\BROKENSYSTEM\ControlSet001\services\<Process Name>" /v ImagePath /t REG_EXPAND_SZ /d "<Image Path>" /f
reg add "HKLM\BROKENSYSTEM\ControlSet001\services\<Process Name>" /v ObjectName /t REG_SZ /d "<Startup Account>" /f
reg add "HKLM\BROKENSYSTEM\ControlSet001\services\<Process Name>" /v type /t REG_DWORD /d 16 /f
reg add "HKLM\BROKENSYSTEM\ControlSet002\services\<Process Name>" /v start /t REG_DWORD /d <Startup Type> /f
reg add "HKLM\BROKENSYSTEM\ControlSet002\services\<Process Name>" /v ImagePath /t REG_EXPAND_SZ /d "<Startup Account>" /f
reg add "HKLM\BROKENSYSTEM\ControlSet002\services\<Process Name>" /v ObjectName /t REG_SZ /d "<Startup Account>" /f
reg add "HKLM\BROKENSYSTEM\ControlSet002\services\<Process Name>" /v type /t REG_DWORD /d 16 /f

REM Enable default dependencies from the broken service
reg add "HKLM\BROKENSYSTEM\ControlSet001\services\<Driver/Service Name>" /v start /t REG_DWORD /d <Startup Type> /f
reg add "HKLM\BROKENSYSTEM\ControlSet002\services\<Driver/Service Name>" /v start /t REG_DWORD /d <Startup Type> /f
reg unload HKLM\BROKENSYSTEM
```

## <a name="need-help-contact-support"></a>도움 필요 시 지원에 문의

다른 도움이 필요한 경우 [지원에 문의](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)하여 문제를 신속하게 해결하세요.
