---
title: Azure VM에에서 원격 데스크톱 연결이 자주 끊김 | Microsoft Docs
description: Azure VM에서 원격 데스크톱 연결이 자주 끊기는 문제를 해결하는 방법을 알아봅니다.
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
ms.date: 10/24/2018
ms.author: genli
ms.openlocfilehash: 7fecf8c5fdafb64f7922054dd2bb9755b0dec031
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60386179"
---
# <a name="remote-desktop-disconnects-frequently-in-azure-vm"></a>Azure VM에에서 원격 데스크톱 연결이 자주 끊김

이 문서에서는 RDP(원격 데스크톱 프로토콜)를 통한 Azure VM(Virtual Machine 연) 연결이 자주 끊기는 문제를 해결하는 방법을 설명합니다.

> [!NOTE] 
> Azure에는 리소스를 만들고 사용하기 위한 [Resource Manager 및 클래식](../../azure-resource-manager/resource-manager-deployment-model.md)이라는 두 가지 배포 모델이 있습니다. 이 문서에서는 리소스 관리자 배포 모델 사용에 대해 설명합니다. 클래식 배포 모델을 사용하는 대신 새 배포에 이 모델을 사용하는 것이 좋습니다.

## <a name="symptom"></a>증상

세션 동안 간헐적인 RDP 연결 문제가 발생합니다. 처음에는 VM에 연결할 수 있지만 연결이 끊어집니다.

## <a name="cause"></a>원인

RDP 수신기가 잘못 구성되면 이 문제가 발생할 수 있습니다. 일반적으로 이 문제는 사용자 지정 이미지를 사용하는 VM에서 발생합니다.

## <a name="solution"></a>해결 방법

다음 단계를 수행하기 전에 영향을 받는 VM의 [OS 디스크 스냅샷을 백업으로 만듭니다](../windows/snapshot-copy-managed-disk.md). 

이 문제를 해결하려면 직렬 콘솔을 사용하거나 VM의 OS 디스크를 복구 VM에 연결하여 [오프라인으로 VM을 복구](#repair-the-vm-offline)합니다.

### <a name="serial-control"></a>직렬 콘솔

1. [직렬 콘솔에 연결하고 CMD 인스턴스를 엽니다](./serial-console-windows.md). 그런 후 다음 명령을 실행하여 RDP 구성을 다시 설정합니다. VM에서 직렬 콘솔을 사용하지 않도록 설정한 경우 다음 단계로 이동합니다.
2. RDP 보안 계층을 0으로 낮춥니다. 이 설정에서 서버와 클라이언트 간의 통신은 기본 RDP 암호화를 사용합니다.

        REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'SecurityLayer' /t REG_DWORD /d 0 /f
3. 레거시 RDP 클라이언트가 연결할 수 있도록 암호화 수준을 최소 설정으로 낮춥니다.

        REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'MinEncryptionLevel' /t REG_DWORD /d 1 /f
4. RDP를 설정하여 클라이언트 컴퓨터의 사용자 구성을 로드합니다.

        REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'fQueryUserConfigFromLocalMachine' /t REG_DWORD /d 1 /f
5. RDP 연결 유지 컨트롤을 사용하도록 설정합니다.

        REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'KeepAliveTimeout' /t REG_DWORD /d 1 /f

        REG ADD "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v 'KeepAliveEnable' /t REG_DWORD /d 1 /f

        REG ADD "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v 'KeepAliveInterval' /t REG_DWORD /d 1 /f
6. RDP 다시 연결 컨트롤을 설정합니다.

        REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'fInheritReconnectSame' /t REG_DWORD /d 0 /f

        REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'fReconnectSame' /t REG_DWORD /d 1 /f

        REG ADD "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v 'fDisableAutoReconnect' /t REG_DWORD /d 0 /f
7. RDP 세션 시간 컨트롤을 설정합니다.

        REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'fInheritMaxSessionTime' /t REG_DWORD /d 1 /f
8. RDP 연결 끊기 시간 컨트롤을 설정합니다. 

        REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'fInheritMaxDisconnectionTime' /t REG_DWORD /d 1 /f
        REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'MaxDisconnectionTime' /t REG_DWORD /d 0 /f
9. RDP 연결 시간 컨트롤을 설정합니다.

        REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'MaxConnectionTime' /t REG_DWORD /d 0 /f
10. RDP 세션 유휴 시간 컨트롤을 설정합니다.

        REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'fInheritMaxIdleTime' /t REG_DWORD /d 1 /f

        REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'MaxIdleTime' /t REG_DWORD /d 0 /f
11. "최대 동시 연결 제한" 컨트롤을 설정합니다.

        REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'MaxInstanceCount' /t REG_DWORD /d 4294967295 /f

12. VM을 다시 시작하고 RDP를 사용하여 연결을 다시 시도합니다.

### <a name="repair-the-vm-offline"></a>오프라인으로 VM 복구

1. [복구 VM에 OS 디스크를 연결합니다](../windows/troubleshoot-recovery-disks-portal.md).
2. OS 디스크가 복구 VM에 연결되면 디스크 관리 콘솔에서 디스크의 플래그가 **온라인**으로 지정되어 있는지 확인합니다. 연결된 OS 디스크에 할당된 드라이브 문자를 기록해 둡니다.
3. 연결한 OS 디스크에서 **\windows\system32\config** 폴더로 이동합니다. 롤백이 필요한 경우 이 폴더에 있는 모든 파일을 복사합니다.
4. 레지스트리 편집기(regedit.exe)를 시작합니다.
5. **HKEY_LOCAL_MACHINE** 키를 선택합니다. 메뉴에서 **파일** > **Hive 로드**를 선택합니다.
6. 연결한 OS 디스크에서 **\windows\system32\config\SYSTEM** 폴더로 이동합니다. Hive 이름으로 **BROKENSYSTEM**을 입력합니다. 새 레지스트리 Hive는 **HKEY_LOCAL_MACHINE** 키 아래에 표시됩니다. 그런 후 소프트웨어 하이브 **\windows\system32\config\SOFTWARE**를 **HKEY_LOCAL_MACHINE** 키 아래에 로드합니다. Hive 소프트웨어 이름으로 **BROKENSOFTWARE**를 입력합니다. 
7. 관리자 권한으로 명령 프롬프트 창을 열고(**관리자 권한으로 실행**), 나머지 단계의 명령을 실행하여 RDP 구성을 다시 설정합니다. 
8. 서버와 클라이언트 간의 통신이 기본 RDP 암호화를 사용하도록 RDP 보안 계층을 0으로 낮춥니다.

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v 'SecurityLayer' /t REG_DWORD /d 0 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v 'SecurityLayer' /t REG_DWORD /d 0 /f
9. 레거시 RDP 클라이언트가 연결할 수 있도록 암호화 수준을 최소 설정으로 낮춥니다.

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v 'MinEncryptionLevel' /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v 'MinEncryptionLevel' /t REG_DWORD /d 1 /f
10. RDP를 설정하여 클라이언트 머신의 사용자 구성을 로드합니다.

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v 'fQueryUserConfigFromLocalMachine' /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v 'fQueryUserConfigFromLocalMachine' /t REG_DWORD /d 1 /f
11. RDP 연결 유지 컨트롤을 사용하도록 설정합니다.

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v 'KeepAliveTimeout' /t REG_DWORD /d 1 /f 
        
        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v 'KeepAliveTimeout' /t REG_DWORD /d 1 /f 

        REG ADD "HKLM\BROKENSOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v 'KeepAliveEnable' /t REG_DWORD /d 1 /f 

        REG ADD "HKLM\BROKENSOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v 'KeepAliveInterval' /t REG_DWORD /d 1 /f
12. RDP 다시 연결 컨트롤을 설정합니다.

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v 'fInheritReconnectSame' /t REG_DWORD /d 0 /f 
        
        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v 'fReconnectSame' /t REG_DWORD /d 1 /f 

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v 'fInheritReconnectSame' /t REG_DWORD /d 0 /f 

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v 'fReconnectSame' /t REG_DWORD /d 1 /f 

        REG ADD "HKLM\BROKENSOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v 'fDisableAutoReconnect' /t REG_DWORD /d 0 /f

13. RDP 세션 시간 컨트롤을 설정합니다.

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v 'fInheritMaxSessionTime' /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v 'fInheritMaxSessionTime' /t REG_DWORD /d 1 /f
14. RDP 연결 끊기 시간 컨트롤을 설정합니다.

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v 'fInheritMaxDisconnectionTime' /t REG_DWORD /d 1 /f 

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v 'MaxDisconnectionTime' /t REG_DWORD /d 0 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v 'fInheritMaxDisconnectionTime' /t REG_DWORD /d 1 /f 

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v 'MaxDisconnectionTime' /t REG_DWORD /d 0 /f
15. RDP 연결 시간 컨트롤을 설정합니다.

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v 'MaxConnectionTime' /t REG_DWORD /d 0 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v 'MaxConnectionTime' /t REG_DWORD /d 0 /f
16. RDP 세션 유휴 시간 컨트롤을 설정합니다.     REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v 'fInheritMaxIdleTime' /t REG_DWORD /d 1 /f 

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v ' MaxIdleTime' /t REG_DWORD /d 0 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v 'fInheritMaxIdleTime' /t REG_DWORD /d 1 /f 

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v ' MaxIdleTime' /t REG_DWORD /d 0 /f
17. "최대 동시 연결 제한" 컨트롤을 설정합니다.

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v 'MaxInstanceCount' /t REG_DWORD /d ffffffff /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v 'MaxInstanceCount' /t REG_DWORD /d ffffffff /f
18. VM을 다시 시작하고 RDP를 사용하여 연결을 다시 시도합니다.

## <a name="need-help"></a>도움 필요 시 
지원에 문의 추가 도움이 필요한 경우 [지원에 문의](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)하여 문제를 신속하게 해결하세요.





