---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 04/15/2021
ms.author: alkohli
ms.openlocfilehash: 6f732f7c59f76c99d82d5c4f7b062532c60a5a78
ms.sourcegitcommit: 17345cc21e7b14e3e31cbf920f191875bf3c5914
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/19/2021
ms.locfileid: "110164656"
---
클라이언트의 운영 체제에 따라 디바이스에 원격으로 연결하는 절차가 다릅니다.

### <a name="remotely-connect-from-a-windows-client"></a>Windows 클라이언트에서 원격으로 연결


#### <a name="prerequisites"></a>사전 요구 사항

시작하기 전에 다음 사항을 확인합니다.

- Windows 클라이언트에서 Windows PowerShell 5.0 이상을 실행합니다.
- Windows 클라이언트에 디바이스에 설치된 노드 인증서에 해당하는 서명 체인(루트 인증서)이 있습니다. 자세한 지침은 [Windows 클라이언트에 인증서 설치](../articles/databox-online/azure-stack-edge-gpu-manage-certificates.md#import-certificates-on-the-client-accessing-the-device)를 참조하세요.
- `C:\Windows\System32\drivers\etc`에 있는 Windows 클라이언트용 `hosts` 파일에 다음 형식으로 노드 인증서에 해당하는 항목이 있습니다.

    `<Device IP>    <Node serial number>.<DNS domain of the device>`

    다음은 `hosts` 파일의 예입니다.
 
    `10.100.10.10    1HXQG13.wdshcsso.com`
  

#### <a name="detailed-steps"></a>자세한 단계

Windows 클라이언트에서 원격으로 연결하려면 다음 단계를 수행합니다.

1. 관리자 권한으로 Windows PowerShell 세션을 실행합니다.
2. Windows 원격 관리 서비스가 클라이언트에서 실행되고 있는지 확인합니다. 명령 프롬프트에 다음을 입력합니다.

    ```powershell
    winrm quickconfig
    ```

    자세한 내용은 [Windows 원격 관리를 위한 설치 및 구성](/windows/win32/winrm/installation-and-configuration-for-windows-remote-management#quick-default-configuration)을 참조하세요.

3. `hosts` 파일에 사용되는 연결 문자열에 변수를 할당합니다.

    ```powershell
    $Name = "<Node serial number>.<DNS domain of the device>"
    ``` 

    `<Node serial number>` 및 `<DNS domain of the device>`를 디바이스의 노드 일련 번호 및 DNS 도메인으로 바꿉니다. 디바이스의 로컬 웹 UI에 있는 **디바이스** 페이지에서 **인증서** 페이지 및 DNS 도메인의 노드 일련 번호에 대한 값을 가져올 수 있습니다.

4. 디바이스의 연결 문자열을 클라이언트의 신뢰할 수 있는 호스트 목록에 추가하려면 다음 명령을 입력합니다.

    ```powershell
    Set-Item WSMan:\localhost\Client\TrustedHosts $Name -Concatenate -Force
    ```

5. 디바이스에서 Windows PowerShell 세션을 시작합니다.

    ```powershell
    Enter-PSSession -ComputerName $Name -Credential ~\EdgeUser -ConfigurationName Minishell -UseSSL
    ```

    신뢰 관계와 관련된 오류가 표시되는 경우 디바이스에 업로드된 노드 인증서의 서명 체인이 디바이스에 액세스하는 클라이언트에도 설치되어 있는지 확인합니다.

6. 메시지가 표시되면 암호를 입력합니다. 로컬 웹 UI에 로그인하는 데 사용되는 것과 동일한 암호를 사용합니다. 기본 로컬 웹 UI 암호는 *Password1* 입니다. 원격 PowerShell을 사용하여 디바이스에 성공적으로 연결하면 다음 샘플 출력이 표시됩니다.  

    ```
    Windows PowerShell
    Copyright (C) Microsoft Corporation. All rights reserved.
    
    PS C:\WINDOWS\system32> winrm quickconfig
    WinRM service is already running on this machine.
    PS C:\WINDOWS\system32> $Name = "1HXQG13.wdshcsso.com"
    PS C:\WINDOWS\system32> Set-Item WSMan:\localhost\Client\TrustedHosts $Name -Concatenate -Force
    PS C:\WINDOWS\system32> Enter-PSSession -ComputerName $Name -Credential ~\EdgeUser -ConfigurationName Minishell -UseSSL

    WARNING: The Windows PowerShell interface of your device is intended to be used only for the initial network configuration. Please engage Microsoft Support if you need to access this interface to troubleshoot any potential issues you may be experiencing. Changes made through this interface without involving Microsoft Support could result in an unsupported configuration.
    [1HXQG13.wdshcsso.com]: PS>
    ```

`-UseSSL` 옵션을 사용하는 경우 *https* 를 통해 PowerShell 원격 기능을 사용하게 됩니다. 항상 *https* 를 사용하여 PowerShell을 통해 원격으로 연결하는 것이 좋습니다. 신뢰할 수 있는 네트워크 내에서 http를 통해 PowerShell을 통한 원격을 사용할 수 있습니다. 먼저 로컬 UI에서 http를 통해 원격 PowerShell을 사용하도록 설정합니다. 그런 다음 `-UseSSL` 옵션 없이 이전 절차를 사용하여 디바이스의 PowerShell 인터페이스에 연결할 수 있습니다.

인증서를 사용하지 않는 경우(인증서를 사용하는 것을 권장함) 세션 옵션 `-SkipCACheck -SkipCNCheck -SkipRevocationCheck`을 사용하여 인증서 유효성 검사를 건너뛸 수 있습니다.

```powershell
$sessOptions = New-PSSessionOption -SkipCACheck -SkipCNCheck -SkipRevocationCheck 
Enter-PSSession -ComputerName $Name -Credential ~\EdgeUser -ConfigurationName Minishell -UseSSL -SessionOption $sessOptions    
```
인증서 검사를 건너뛸 때의 예시 출력은 다음과 같습니다.

```powershell
PS C:\WINDOWS\system32> $Name = "1HXQG13.wdshcsso.com"
PS C:\WINDOWS\system32> $sessOptions = New-PSSessionOption -SkipCACheck -SkipCNCheck -SkipRevocationCheck
PS C:\WINDOWS\system32> $sessOptions

MaximumConnectionRedirectionCount : 5
NoCompression                     : False
NoMachineProfile                  : False
ProxyAccessType                   : None
ProxyAuthentication               : Negotiate
ProxyCredential                   :
SkipCACheck                       : True
SkipCNCheck                       : True
SkipRevocationCheck               : True
OperationTimeout                  : 00:03:00
NoEncryption                      : False
UseUTF16                          : False
IncludePortInSPN                  : False
OutputBufferingMode               : None
MaxConnectionRetryCount           : 0
Culture                           :
UICulture                         :
MaximumReceivedDataSizePerCommand :
MaximumReceivedObjectSize         :
ApplicationArguments              :
OpenTimeout                       : 00:03:00
CancelTimeout                     : 00:01:00
IdleTimeout                       : -00:00:00.0010000

PS C:\WINDOWS\system32> Enter-PSSession -ComputerName $Name -Credential ~\EdgeUser -ConfigurationName Minishell -UseSSL -SessionOption $sessOptions
WARNING: The Windows PowerShell interface of your device is intended to be used only for the initial network configuration. Please
engage Microsoft Support if you need to access this interface to troubleshoot any potential issues you may be experiencing.
Changes made through this interface without involving Microsoft Support could result in an unsupported configuration.
[1HXQG13.wdshcsso.com]: PS>
```

> [!IMPORTANT]
> 현재 릴리스에서는 Windows 클라이언트를 통해서만 디바이스의 PowerShell 인터페이스에 연결할 수 있습니다. `-UseSSL` 옵션은 Linux 클라이언트에서 작동하지 않습니다.

<!--### Remotely connect from a Linux client-->

<!--On the Linux client that you'll use to connect:

- [Install the latest PowerShell Core for Linux](/powershell/scripting/install/installing-powershell-core-on-linux) from GitHub to get the SSH remoting feature. 
- [Install only the `gss-ntlmssp` package from the NTLM module](https://github.com/Microsoft/omi/blob/master/Unix/doc/setup-ntlm-omi.md). For Ubuntu clients, use the following command:
    - `sudo apt-get install gss-ntlmssp`

For more information, go to [PowerShell remoting over SSH](/powershell/scripting/learn/remoting/ssh-remoting-in-powershell-core).

Follow these steps to remotely connect from an NFS client.

1. To open PowerShell session, type:

    `pwsh`
 
2. For connecting using the remote client, type:

    `Enter-PSSession -ComputerName $ip -Authentication Negotiate -ConfigurationName Minishell -Credential ~\EdgeUser`

    When prompted, provide the password used to sign into your device.
 
> [!NOTE]
> This procedure does not work on Mac OS.-->