---
title: Azure의 Windows VM에 대한 RDP 일반 오류 문제 해결 | Microsoft Docs
description: Azure의 Windows VM에 대한 RDP 일반 오류 문제를 해결하는 방법에 관한 자세한 정보 | Microsoft Docs
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
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: 8b12e3cdc53b926f660e12b7cf4b79a8cb6f40c2
ms.sourcegitcommit: ada7419db9d03de550fbadf2f2bb2670c95cdb21
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2018
ms.locfileid: "50960160"
---
# <a name="troubleshoot-an-rdp-general-error-in-azure-vm"></a>Azure VM의 RDP 일반 오류 문제 해결

이 문서에서는 Azure의 Windows VM(가상 머신)에 대해 RDP(원격 데스크톱 프로토콜) 연결을 만들 때 발생할 수 있는 일반적인 오류를 설명합니다.

## <a name="symptom"></a>증상

Azure의 Windows VM에 대한 RDP 연결을 만들 때 다음과 같은 일반 오류 메시지를 받을 수 있습니다.

**다음 이유 중 하나로 인해 원격 데스크톱에서 원격 컴퓨터에 연결할 수 없습니다.**

1. **서버에 대한 원격 액세스를 사용할 수 없음**

2. **원격 컴퓨터가 꺼져 있음**

3. **네트워크에서 원격 컴퓨터를 사용할 수 없음**

**원격 컴퓨터가 켜져 있고 네트워크에 연결되어 있으며 원격 액세스가 사용하도록 설정되었는지 확인합니다.**

## <a name="cause"></a>원인

이 문제는 다음과 같은 원인으로 인해 발생할 수 있습니다.

### <a name="cause-1"></a>원인 1

RDP 구성 요소가 다음과 같이 사용하지 않도록 설정되었습니다.

- 구성 요소 수준에서
- 수신기 수준에서
- 터미널 서버에서
- 원격 데스크톱 세션 호스트 역할에서

### <a name="cause-2"></a>원인 2

원격 데스크톱 서비스(TermService)가 실행되고 있지 않습니다.

### <a name="cause-3"></a>원인 3

RDP 수신기가 잘못 구성되었습니다.

## <a name="solution"></a>해결 방법

이 문제를 해결하려면 [운영 체제 디스크를 백업](../windows/snapshot-copy-managed-disk.md)하고 [운영 체제 디스크를 복구 VM에 연결](troubleshoot-recovery-disks-portal-windows.md)한 다음, 단계를 따르세요.

### <a name="serial-console"></a>직렬 콘솔

#### <a name="step-1-turn-on-remote-desktop"></a>1단계: 원격 데스크톱 켜기

1. **지원 및 문제 해결** > **직렬 콘솔(미리 보기)** 을 선택하여 [직렬 콘솔](serial-console-windows.md)에 액세스합니다. VM에서 기능을 사용하도록 설정하면 VM을 성공적으로 연결할 수 있습니다.

2. CMD 인스턴스에 대한 새 채널을 만듭니다. **CMD**를 입력하여 채널을 시작하고 채널 이름을 가져옵니다.

3. CMD 인스턴스를 실행하는 채널(이 예에서는 채널 1)로 전환합니다.

   ```
   ch -si 1
   ```

#### <a name="step-2-check-the-values-of-rdp-registry-keys"></a>2단계: RDP 레지스트리 키를 값 확인하기:

1. RDP가 정책에서 비활성화되어 있는지 확인합니다.

      ```
      REM Get the local policy 
      reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server " /v fDenyTSConnections

      REM Get the domain policy if any
      reg query "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v fDenyTSConnections
      ```

      - 도메인 정책이 있는 경우 로컬 정책의 설정을 덮어씁니다.
      - 도메인 정책에서 RDP를 사용하지 않도록 설정(1)하는 경우 도메인 컨트롤러에서 AD 정책을 업데이트합니다.
      - 도메인 정책에서 RDP를 사용하도록 설정(0)하는 경우에는 업데이트가 필요하지 않습니다.
      - 도메인 정책이 없고 로컬 정책에서 RDP를 사용하지 않도록 설정(1)하는 경우 다음 명령을 사용하여 RDP를 사용하도록 설정합니다. 
      
            reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v fDenyTSConnections /t REG_DWORD /d 0 /f
                  

2. 터미널 서버의 현재 구성을 확인합니다.

      ```
      reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v TSEnabled
      ```

      명령이 0을 반환하면 터미널 서버를 사용할 수 없습니다. 이 경우 다음과 같이 터미널 서버를 사용하도록 설정합니다.

      ```
      reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v TSEnabled /t REG_DWORD /d 1 /f
      ```

3. 서버가 터미널 서버 팜(RDS 또는 Citrix)에 있는 경우 터미널 서버 모듈이 드레이닝 모드로 설정됩니다. 터미널 서버 모듈의 현재 모드를 확인합니다.

      ```
      reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v TSServerDrainMode
      ```

      명령이 1을 반환하면 터미널 서버 모듈이 드레이닝 모드로 설정되었습니다. 이 경우 다음과 같이 모듈을 작업 모드로 설정합니다.

      ```
      reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v TSServerDrainMode /t REG_DWORD /d 0 /f
      ```

4. 터미널 서버에 연결할 수 있는지 확인합니다.

      ```
      reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v TSUserEnabled
      ```

      명령이 1을 반환하면 터미널 서버에 연결할 수 없습니다. 이 경우 다음과 같이 연결을 사용하도록 설정합니다.

      ```
      reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v TSUserEnabled /t REG_DWORD /d 0 /f
      ```
5. RDP 수신기의 현재 구성을 확인합니다.

      ```
      reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp" /v fEnableWinStation
      ```

      명령이 0을 반환하면 RDP 수신기를 사용할 수 없습니다. 이 경우 다음과 같이 수신기를 사용하도록 설정합니다.

      ```
      reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp" /v fEnableWinStation /t REG_DWORD /d 1 /f
      ```

6. RDP 수신기에 연결할 수 있는지 확인합니다.

      ```
      reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp" /v fLogonDisabled
      ```

   명령이 1을 반환하면 RDP 수신기에 연결할 수 없습니다. 이 경우 다음과 같이 연결을 사용하도록 설정합니다.

      ```
      reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp" /v fLogonDisabled /t REG_DWORD /d 0 /f
      ```

7. VM을 다시 시작합니다.

8. `exit`를 입력하여 CMD 인스턴스에서 종료하고 **Enter** 키를 두 번 누릅니다.

9. `restart`를 입력하여 VM을 다시 시작한 다음, VM에 연결합니다.

문제가 계속되면 2단계로 이동합니다.

#### <a name="step-2-enable-remote-desktop-services"></a>2단계: 원격 데스크톱 서비스 사용

자세한 내용은 [Azure VM에서 원격 데스크톱 서비스가 시작되지 않음](troubleshoot-remote-desktop-services-issues.md)을 참조하세요.

#### <a name="step-3-reset-rdp-listener"></a>3단계: RDP 수신기 다시 설정

자세한 내용은 [Azure VM에서 원격 데스크톱 연결이 자주 끊김](troubleshoot-rdp-intermittent-connectivity.md)을 참조하세요.

### <a name="offline-repair"></a>오프라인 복구

#### <a name="step-1-turn-on-remote-desktop"></a>1단계: 원격 데스크톱 켜기

1. [복구 VM에 OS 디스크를 연결합니다](../windows/troubleshoot-recovery-disks-portal.md).
2. 복구 VM에 대한 원격 데스크톱 연결을 시작합니다.
3. 디스크 관리 콘솔에서 디스크의 플래그가 **온라인**으로 지정되었는지 확인합니다. 연결된 OS 디스크에 할당된 드라이브 문자를 적어 둡니다.
4. 복구 VM에 대한 원격 데스크톱 연결을 시작합니다.
5. 관리자 권한 명령 프롬프트 세션을 엽니다(**관리자 권한으로 실행**). 다음 스크립트를 실행합니다. 이 스크립트에서 연결된 OS 디스크에 할당된 드라이브 문자가 F라고 가정합니다. 이 드라이브 문자를 VM에서 적절한 값으로 바꿉니다.

      ```
      reg load HKLM\BROKENSYSTEM F:\windows\system32\config\SYSTEM.hiv 
      reg load HKLM\BROKENSOFTWARE F:\windows\system32\config\SOFTWARE.hiv 
 
      REM Ensure that Terminal Server is enabled 

      reg add "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server" /v TSEnabled /t REG_DWORD /d 1 /f 
      reg add "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server" /v TSEnabled /t REG_DWORD /d 1 /f 

      REM Ensure Terminal Service is not set to Drain mode 
      reg add "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server" /v TSServerDrainMode /t REG_DWORD /d 0 /f 
      reg add "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server" /v TSServerDrainMode /t REG_DWORD /d 0 /f 

      REM Ensure Terminal Service has logon enabled 
      reg add "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server" /v TSUserEnabled /t REG_DWORD /d 0 /f 
      reg add "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server" /v TSUserEnabled /t REG_DWORD /d 0 /f 

      REM Ensure the RDP Listener is not disabled 
      reg add "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v fEnableWinStation /t REG_DWORD /d 1 /f 
      reg add "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v fEnableWinStation /t REG_DWORD /d 1 /f 

      REM Ensure the RDP Listener accepts logons 
      reg add "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v fLogonDisabled /t REG_DWORD /d 0 /f 
      reg add "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v fLogonDisabled /t REG_DWORD /d 0 /f 

      REM RDP component is enabled 
      reg add "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server" /v fDenyTSConnections /t REG_DWORD /d 0 /f 
      reg add "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server" /v fDenyTSConnections /t REG_DWORD /d 0 /f 
      reg add "HKLM\BROKENSOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v fDenyTSConnections /t REG_DWORD /d 0 /f 

      reg unload HKLM\BROKENSYSTEM 
      reg unload HKLM\BROKENSOFTWARE 
      ```

6. VM이 도메인에 가입된 경우 다음 레지스트리 키를 검사하여 RDP를 사용하지 않도록 설정할 그룹 정책이 있는지 확인합니다. 

      ```
      HKLM\BROKENSOFTWARE\Policies\Microsoft\Windows NT\Terminal Services\fDenyTSConnectionS
      ```

      이 키 값이 1로 설정된 경우에는 정책에서 RDP를 사용하지 않도록 설정합니다. GPO 정책을 통해 원격 데스크톱을 사용하도록 설정하려면 도메인 컨트롤러에서 다음 정책을 변경합니다.

   
      **컴퓨터 구성\정책\관리 템플릿:**

      정책 정의\Windows 구성 요소\원격 데스크톱 서비스\원격 데스크톱 세션 호스트\연결\사용자가 원격 데스크톱 서비스를 사용하여 원격으로 연결하도록 허용
  
7. 복구 VM에서 디스크를 분리합니다.
8. [디스크에서 새 VM을 만듭니다](../windows/create-vm-specialized.md).

문제가 계속되면 2단계로 이동합니다.

#### <a name="step-2-enable-remote-desktop-services"></a>2단계: 원격 데스크톱 서비스 사용

자세한 내용은 [Azure VM에서 원격 데스크톱 서비스가 시작되지 않음](troubleshoot-remote-desktop-services-issues.md)을 참조하세요.

#### <a name="step-3-reset-rdp-listener"></a>3단계: RDP 수신기 다시 설정

자세한 내용은 [Azure VM에서 원격 데스크톱 연결이 자주 끊김](troubleshoot-rdp-intermittent-connectivity.md)을 참조하세요.

## <a name="need-help-contact-support"></a>도움 필요 시 지원에 문의

다른 도움이 필요한 경우 [지원에 문의](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)하여 문제를 신속하게 해결하세요.
