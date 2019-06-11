---
title: Azure VM의 게스트 OS에서 방화벽 규칙 사용 또는 사용 안 함 | Microsoft Docs
description: ''
services: virtual-machines-windows
documentationcenter: ''
author: Deland-Han
manager: willchen
editor: ''
tags: ''
ms.service: virtual-machines
ms.topic: troubleshooting
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: azurecli
ms.date: 11/22/2018
ms.author: delhan
ms.openlocfilehash: ed3d89bc15f960947a48ac4364bd14f3fdf50cc2
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60505588"
---
# <a name="enable-or-disable-a-firewall-rule-on-an-azure-vm-guest-os"></a>Azure VM 게스트 OS에 대 한 방화벽 규칙을 사용할지 설정 합니다.

이 문서에서는 게스트 운영 체제 방화벽에서 VM(가상 머신)의 트래픽 일부를 필터링하는 것으로 의심되는 상황을 해결하기 위한 참조를 제공합니다. 이는 다음과 같은 이유로 유용할 수 있습니다.

*   방화벽이 의도적으로 변경되어 RDP 연결이 실패한 경우 사용자 지정 스크립트 확장 기능을 사용하여 문제를 해결할 수 있습니다.

*   모든 방화벽 프로필을 사용하지 않도록 설정하면 RDP 관련 방화벽 규칙을 설정하는 것보다 훨씬 더 쉽게 문제를 해결할 수 있습니다.

## <a name="solution"></a>해결 방법

방화벽 규칙을 구성하는 방법은 필요한 VM에 대한 액세스 수준에 따라 달라집니다. 다음 예제에서는 RDP 규칙을 사용합니다. 그러나 올바른 레지스트리 키를 가리켜서 다른 모든 종류의 트래픽에도 동일한 방법을 적용할 수 있습니다.

### <a name="online-troubleshooting"></a>온라인 문제 해결 

#### <a name="mitigation-1-custom-script-extension"></a>해결 방법 1: 사용자 지정 스크립트 확장

1.  다음 템플릿을 사용하여 스크립트를 만듭니다.

    *   규칙을 사용하도록 설정합니다.
        ```cmd
        netsh advfirewall firewall set rule dir=in name="Remote Desktop - User Mode (TCP-In)" new enable=yes
        ```

    *   규칙을 사용하지 않도록 설정합니다.
        ```cmd
        netsh advfirewall firewall set rule dir=in name="Remote Desktop - User Mode (TCP-In)" new enable=no
        ```

2.  [사용자 지정 스크립트 확장](../extensions/custom-script-windows.md) 기능을 사용하여 이 스크립트를 Azure Portal에 업로드합니다. 

#### <a name="mitigation-2-remote-powershell"></a>해결 방법 2: 원격 PowerShell

VM이 온라인 상태이고 동일한 가상 네트워크의 다른 VM에서 액세스할 수 있는 경우 다른 VM을 사용하여 다음과 같은 완화를 수행할 수 있습니다.

1.  문제 해결을 위한 VM에서 PowerShell 콘솔 창을 엽니다.

2.  필요에 따라 적절하게 다음 명령을 실행합니다.

    *   규칙을 사용하도록 설정합니다.
        ```powershell
        Enter-PSSession (New-PSSession -ComputerName "<HOSTNAME>" -Credential (Get-Credential) -SessionOption (New-PSSessionOption -SkipCACheck -SkipCNCheck)) 
        Enable-NetFirewallRule -DisplayName  "RemoteDesktop-UserMode-In-TCP"
        exit
        ```

    *   규칙을 사용하지 않도록 설정합니다.
        ```powershell
        Enter-PSSession (New-PSSession -ComputerName "<HOSTNAME>" -Credential (Get-Credential) -SessionOption (New-PSSessionOption -SkipCACheck -SkipCNCheck)) 
        Disable-NetFirewallRule -DisplayName  "RemoteDesktop-UserMode-In-TCP"
        exit
        ```

#### <a name="mitigation-3-pstools-commands"></a>해결 방법 3: PSTools 명령

VM이 온라인 상태이고 동일한 가상 네트워크의 다른 VM에서 액세스할 수 있는 경우 다른 VM을 사용하여 다음과 같은 완화를 수행할 수 있습니다.

1.  문제 해결을 위한 VM에서 [PSTools](https://docs.microsoft.com/sysinternals/downloads/pstools)를 다운로드합니다.

2.  CMD 인스턴스를 열고, 해당 DIP(내부 IP)를 통해 VM에 액세스합니다. 

    * 규칙을 사용하도록 설정합니다.
        ```cmd
        psexec \\<DIP> -u <username> cmd
        netsh advfirewall firewall set rule dir=in name="Remote Desktop - User Mode (TCP-In)" new enable=yes
        ```

    *   규칙을 사용하지 않도록 설정합니다.
        ```cmd
        psexec \\<DIP> -u <username> cmd
        netsh advfirewall firewall set rule dir=in name="Remote Desktop - User Mode (TCP-In)" new enable=no
        ```

#### <a name="mitigation-4-remote-registry"></a>해결 방법 4: 원격 레지스트리

VM이 온라인 상태이고 동일한 가상 네트워크의 다른 VM에서 액세스할 수 있는 경우 다른 VM에서 [원격 레지스트리](https://support.microsoft.com/help/314837/how-to-manage-remote-access-to-the-registry)를 사용할 수 있습니다.

1.  문제 해결을 위한 VM에서 레지스트리 편집기(regedit.exe)를 시작한 다음, **파일** > **네트워크 레지스트리 연결**을 차례로 선택합니다.

2.   *TARGET MACHINE*\SYSTEM 분기를 열고, 다음 값을 지정합니다.

    * 규칙을 사용하도록 설정하려면 다음 레지스트리 값을 엽니다.
    
        *TARGET MACHINE*\SYSTEM\CurrentControlSet\Services\SharedAccess\Parameters\FirewallPolicy\FirewallRules\RemoteDesktop-UserMode-In-TCP
    
        그런 다음, 문자열에서 **Active=FALSE**를 **Active=TRUE**로 변경합니다.

        **v2.22 | 작업 = 허용 | 활성 = TRUE | Dir = In | 프로토콜 = 6 | 프로필: 도메인 | 프로필 = 개인 | 프로필 = Public | LPort 3389 = | App=%SystemRoot%\system32\svchost.exe| Svc termservice = | 이름 =\@FirewallAPI.dll,-28775 | Desc =\@FirewallAPI.dll,-28756 | EmbedCtxt =\@FirewallAPI.dll,-28752 |**
    
    * 규칙을 사용하지 않도록 설정하려면 다음 레지스트리 값을 엽니다.
    
        *TARGET MACHINE*\SYSTEM\CurrentControlSet\Services\SharedAccess\Parameters\FirewallPolicy\FirewallRules\RemoteDesktop-UserMode-In-TCP

        그런 다음, **Active =TRUE**를 **Active=FALSE**로 변경합니다.
        
        **v2.22 | 작업 = 허용 | 활성 = FALSE | Dir = In | 프로토콜 = 6 | 프로필: 도메인 | 프로필 = 개인 | 프로필 = Public | LPort 3389 = | App=%SystemRoot%\system32\svchost.exe| Svc termservice = | 이름 =\@FirewallAPI.dll,-28775 | Desc =\@FirewallAPI.dll,-28756 | EmbedCtxt =\@FirewallAPI.dll,-28752 |**

3.  VM을 다시 시작하여 변경 내용을 적용합니다.

### <a name="offline-troubleshooting"></a>오프라인문제 해결 

어떤 방법으로도 VM에 액세스할 수 없는 경우 사용자 지정 스크립트 확장을 사용할 수 없게 되므로 시스템 디스크를 통해 오프라인 모드에서 직접 작업해야 합니다.

다음 단계를 따르기 전에 영향을 받는 VM의 시스템 디스크 스냅샷을 백업으로 만듭니다. 자세한 내용은  [디스크 스냅숏](../windows/snapshot-copy-managed-disk.md)을 참조하세요.

1.  [복구 VM에 시스템 디스크 연결](troubleshoot-recovery-disks-portal-windows.md).

2.  복구 VM에 대한 원격 데스크톱 연결을 시작합니다.

3.  [디스크 관리] 콘솔에서 디스크의 플래그가  **온라인** 으로 지정되었는지 확인합니다. 연결된 시스템 디스크에 할당된 드라이브 문자입니다.

4.  변경 내용을 롤백해야 하는 경우를 대비하여 변경 전에 \windows\system32\config 폴더의 복사본을 만듭니다.

5.  문제 해결을 위한 VM에서 레지스트리 편집기(regedit.exe)를 시작합니다.

6.  **HKEY_LOCAL_MACHINE**  키를 강조 표시한 다음, 메뉴에서  **파일** > **하이브 로드** 를 차례로 선택합니다.

    ![Regedit](./media/enable-or-disable-firewall-rule-guest-os/load-registry-hive.png)

7.  \windows\system32\config\SYSTEM 파일을 찾아 엽니다. 

    > [!Note]
    > 이름을 묻는 메시지가 표시됩니다.  **BROKENSYSTEM**을 입력한 다음,  **HKEY_LOCAL_MACHINE**을 펼칩니다. 이제  **BROKENSYSTEM**이라는 추가 키가 표시됩니다. 이 문제를 해결하기 위해 이러한 문제 하이브를  **BROKENSYSTEM**으로 탑재합니다.

8.  BROKENSYSTEM 분기에서 다음과 같이 변경합니다.

    1.  VM이 시작되는 **ControlSet** 레지스트리 키를 확인합니다. 키 번호가 HKLM\BROKENSYSTEM\Select\Current에 표시됩니다.

    2.  규칙을 사용하도록 설정하려면 다음 레지스트리 값을 엽니다.
    
        HKLM\BROKENSYSTEM\ControlSet00X\Services\SharedAccess\Parameters\FirewallPolicy\FirewallRules\RemoteDesktop-UserMode-In-TCP
        
        그런 다음, **Active=FALSE**를 **Active=True**로 변경합니다.
        
        **v2.22 | 작업 = 허용 | 활성 = TRUE | Dir = In | 프로토콜 = 6 | 프로필: 도메인 | 프로필 = 개인 | 프로필 = Public | LPort 3389 = | App=%SystemRoot%\system32\svchost.exe| Svc termservice = | 이름 =\@FirewallAPI.dll,-28775 | Desc =\@FirewallAPI.dll,-28756 | EmbedCtxt =\@FirewallAPI.dll,-28752 |**

    3.  규칙을 사용하지 않도록 설정하려면 다음 레지스트리 키를 엽니다.

        HKLM\BROKENSYSTEM\ControlSet00X\Services\SharedAccess\Parameters\FirewallPolicy\FirewallRules\RemoteDesktop-UserMode-In-TCP

        그런 다음, **Active=True**를 **Active=FALSE**로 변경합니다.
        
        **v2.22 | 작업 = 허용 | 활성 = FALSE | Dir = In | 프로토콜 = 6 | 프로필: 도메인 | 프로필 = 개인 | 프로필 = Public | LPort 3389 = | App=%SystemRoot%\system32\svchost.exe| Svc termservice = | 이름 =\@FirewallAPI.dll,-28775 | Desc =\@FirewallAPI.dll,-28756 | EmbedCtxt =\@FirewallAPI.dll,-28752 |**

9.   **BROKENSYSTEM**을 강조 표시한 다음, 메뉴에서  **파일** > **하이브 언로드** 를 차례로 선택합니다.

10. [시스템 디스크를 분리하고 VM을 다시 만듭니다](troubleshoot-recovery-disks-portal-windows.md).

11. 문제가 해결되었는지 확인합니다.
