---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 03/06/2019
ms.author: alkohli
ms.openlocfilehash: 348f7bdd333da4f4a6cb41a438b7aee08d6a6bbb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "67182665"
---
클라이언트의 운영 체제에 따라 장치에 원격으로 연결하는 절차가 다릅니다.

### <a name="remotely-connect-from-a-windows-client"></a>Windows 클라이언트에서 원격으로 연결

시작하기 전에 Windows 클라이언트가 Windows PowerShell 5.0 이상을 실행하고 있는지 확인합니다.

다음 단계에 따라 Windows 클라이언트에서 원격으로 연결합니다.

1. 관리자로 Windows PowerShell 세션을 실행합니다.
2. 클라이언트에서 Windows 원격 관리 서비스가 실행되고 있는지 확인합니다. 명령 프롬프트에 다음을 입력합니다.

    `winrm quickconfig`

3. 장치 IP 주소에 변수를 할당합니다.

    $ip = "<device_ip>"

    장치의 `<device_ip>` IP 주소로 바꿉니다.

4. 장치의 IP 주소를 클라이언트의 신뢰할 수 있는 호스트 목록에 추가하려면 다음 명령을 입력합니다.

    `Set-Item WSMan:\localhost\Client\TrustedHosts $ip -Concatenate -Force`

5. 장치에서 Windows PowerShell 세션을 시작합니다.

    `Enter-PSSession -ComputerName $ip -Credential $ip\EdgeUser -ConfigurationName Minishell`

6. 메시지가 표시되면 암호를 입력합니다. 로컬 웹 UI에 로그인하는 데 사용되는 것과 동일한 암호를 사용합니다. 기본 로컬 웹 UI 암호는 *Password1*. 원격 PowerShell을 사용하여 장치에 성공적으로 연결하면 다음 샘플 출력이 표시됩니다.  

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

연결하는 데 사용할 Linux 클라이언트에서:

- SSH 원격 기능을 얻기 위해 GitHub에서 [리눅스에 대한 최신 PowerShell 코어를 설치합니다.](https://docs.microsoft.com/powershell/scripting/install/installing-powershell-core-on-linux?view=powershell-6) 
- [NTLM `gss-ntlmssp` 모듈에서 패키지만 설치합니다.](https://github.com/Microsoft/omi/blob/master/Unix/doc/setup-ntlm-omi.md) 우분투 클라이언트의 경우 다음 명령을 사용합니다.
    - `sudo apt-get install gss-ntlmssp`

자세한 내용은 [SSH를 통해 PowerShell 으로 이동합니다.](https://docs.microsoft.com/powershell/scripting/learn/remoting/ssh-remoting-in-powershell-core?view=powershell-6)

다음 단계에 따라 NFS 클라이언트에서 원격으로 연결합니다.

1. PowerShell 세션을 열려면 다음을 입력합니다.

    `sudo pwsh`
 
2. 원격 클라이언트를 사용하여 연결하기 위해 다음을 입력합니다.

    `Enter-PSSession -ComputerName $ip -Authentication Negotiate -ConfigurationName Minishell -Credential ~\EdgeUser`

    메시지가 표시되면 기기에 로그인하는 데 사용되는 암호를 입력합니다.
 
> [!NOTE]
> 이 절차는 Mac OS에서 작동하지 않습니다.
