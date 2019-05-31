---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 03/06/2019
ms.author: alkohli
ms.openlocfilehash: 348f7bdd333da4f4a6cb41a438b7aee08d6a6bbb
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/23/2019
ms.locfileid: "66161193"
---
장치에 원격으로 연결 하는 절차는 클라이언트의 운영 체제에 따라 다릅니다.

### <a name="remotely-connect-from-a-windows-client"></a>Windows 클라이언트에서 원격으로 연결

시작 하기 전에 Windows 클라이언트는 Windows PowerShell 5.0 이상을 실행 해야 합니다.

Windows 클라이언트에서 원격으로 연결 하려면 다음이 단계를 따릅니다.

1. 관리자 권한으로 Windows PowerShell 세션을 실행 합니다.
2. 클라이언트에서 Windows Remote Management 서비스가 실행 되 고 있는지 확인 합니다. 명령 프롬프트에서 다음을 입력합니다.

    `winrm quickconfig`

3. 변수는 장치 IP 주소를 할당 합니다.

    $ip = "<device_ip>"

    대체 `<device_ip>` 장치의 IP 주소를 사용 하 여 합니다.

4. 장치의 IP 주소를 클라이언트의 신뢰할 수 있는 호스트 목록에 추가 하려면 다음 명령을 입력 합니다.

    `Set-Item WSMan:\localhost\Client\TrustedHosts $ip -Concatenate -Force`

5. 장치에서 Windows PowerShell 세션을 시작 합니다.

    `Enter-PSSession -ComputerName $ip -Credential $ip\EdgeUser -ConfigurationName Minishell`

6. 메시지가 표시 되 면 암호를 제공 합니다. 로컬 웹 UI에 로그인 하는 데 사용 되는 동일한 암호를 사용 합니다. 기본 로컬 웹 UI 암호가 *Password1*합니다. 원격 PowerShell을 사용 하는 장치에 성공적으로 연결할 때 다음과 같은 샘플 출력이 표시 됩니다.  

    ```
    Windows PowerShell
    Copyright (C) Microsoft Corporation. All rights reserved.
    
    PS C:\WINDOWS\system32> winrm quickconfig
    WinRM service is already running on this machine.
    PS C:\WINDOWS\system32> $ip = "10.100.10.10"
    PS C:\WINDOWS\system32> Set-Item WSMan:\localhost\Client\TrustedHosts $ip -Concatenate -Force
    PS C:\WINDOWS\system32> Enter-PSSession -ComputerName $ip -Credential $ip\EdgeUser -ConfigurationName Minishell

    WARNING: The Windows PowerShell interface of your device is intended to be used only for the initial network configuration. Please engage Microsoft Support if you need to access this interface to troubleshoot any potential issues you may be experiencing. Changes made through this interface without involving Microsoft Support could result in an unsupported configuration.
    [10.100.10.10]: PS>
    ```

### <a name="remotely-connect-from-a-linux-client"></a>Linux 클라이언트에서 원격으로 연결

연결을 사용 하는 Linux 클라이언트:

- [최신 PowerShell Core for Linux 설치](https://docs.microsoft.com/powershell/scripting/install/installing-powershell-core-on-linux?view=powershell-6) SSH 원격 기능을 가져오려면 GitHub에서. 
- [만 설치 합니다 `gss-ntlmssp` NTLM 모듈에서 패키지](https://github.com/Microsoft/omi/blob/master/Unix/doc/setup-ntlm-omi.md)합니다. Ubuntu 클라이언트의 경우 다음 명령을 사용 합니다.
    - `sudo apt-get install gss-ntlmssp`

자세한 내용은 [SSH 통한 PowerShell 원격](https://docs.microsoft.com/powershell/scripting/learn/remoting/ssh-remoting-in-powershell-core?view=powershell-6)합니다.

NFS 클라이언트에서 원격으로 연결 하려면 다음이 단계를 따릅니다.

1. PowerShell 세션을 열려면 다음을 입력 합니다.

    `sudo pwsh`
 
2. 원격 클라이언트를 사용 하 여 연결을 입력 합니다.

    `Enter-PSSession -ComputerName $ip -Authentication Negotiate -ConfigurationName Minishell -Credential ~\EdgeUser`

    메시지가 표시 되 면 장치에 로그인 하는 데 사용한 암호를 제공 합니다.
 
> [!NOTE]
> 이 절차는 Mac OS에서 작동 하지 않습니다.
