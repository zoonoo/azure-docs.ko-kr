---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 03/08/2021
ms.author: alkohli
ms.openlocfilehash: 0ad760caedffa97599548b8dd1b59a887b5690af
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/24/2021
ms.locfileid: "105104447"
---
클라이언트의 운영 체제에 따라 장치에 원격으로 연결 하는 절차가 다릅니다.

### <a name="remotely-connect-from-a-windows-client"></a>Windows 클라이언트에서 원격으로 연결


#### <a name="prerequisites"></a>사전 요구 사항

시작하기 전에 다음 사항을 확인합니다.

- Windows 클라이언트에서 Windows PowerShell 5.0 이상을 실행 하 고 있습니다.
- Windows 클라이언트에는 장치에 설치 된 노드 인증서에 해당 하는 서명 체인 (루트 인증서)이 있습니다. 자세한 지침은 [Windows 클라이언트에 인증서 설치](../articles/databox-online/azure-stack-edge-gpu-manage-certificates.md#import-certificates-on-the-client-accessing-the-device)를 참조 하세요.
- `hosts` `C:\Windows\System32\drivers\etc` Windows 클라이언트에 대 한에 있는 파일에는 다음 형식의 노드 인증서에 해당 하는 항목이 있습니다.

    `<Device IP>    <Node serial number>.<DNS domain of the device>`

    다음은 파일에 대 한 예제 항목입니다 `hosts` .
 
    `10.100.10.10    1HXQG13.wdshcsso.com`
  

#### <a name="detailed-steps"></a>자세한 단계

Windows 클라이언트에서 원격으로 연결 하려면 다음 단계를 수행 합니다.

1. 관리자 권한으로 Windows PowerShell 세션을 실행 합니다.
2. Windows 원격 관리 서비스가 클라이언트에서 실행 되 고 있는지 확인 합니다. 명령 프롬프트에서 다음을 입력합니다.

    `winrm quickconfig`

    자세한 내용은 [Windows 원격 관리를 위한 설치 및 구성](/windows/win32/winrm/installation-and-configuration-for-windows-remote-management#quick-default-configuration)을 참조하세요.

3. 장치 IP 주소에 변수를 할당 합니다.

    $ip = "<device_ip>"

    `<device_ip>`을 장치의 IP 주소로 바꿉니다.

4. 장치의 IP 주소를 클라이언트의 신뢰할 수 있는 호스트 목록에 추가 하려면 다음 명령을 입력 합니다.

    `Set-Item WSMan:\localhost\Client\TrustedHosts $ip -Concatenate -Force`

5. 장치에서 Windows PowerShell 세션을 시작 합니다.

    `Enter-PSSession -ComputerName $ip -Credential $ip\EdgeUser -ConfigurationName Minishell -UseSSL`

    트러스트 관계와 관련 된 오류가 표시 되는 경우 장치에 업로드 된 노드 인증서의 서명 체인이 장치에 액세스 하는 클라이언트에도 설치 되어 있는지 확인 합니다.

    > [!NOTE] 
    > 옵션을 사용 하는 경우 `-UseSSL` *https* 를 통해 PowerShell을 통해 원격으로 사용 됩니다. 항상 *https* 를 사용 하 여 PowerShell을 통해 원격으로 연결 하는 것이 좋습니다. *Http* 세션은 가장 안전한 연결 방법이 아니지만 신뢰할 수 있는 네트워크에서 허용 됩니다.

6. 메시지가 표시 되 면 암호를 입력 합니다. 로컬 웹 UI에 로그인 하는 데 사용 되는 것과 동일한 암호를 사용 합니다. 기본 로컬 웹 UI 암호는 *Password1* 입니다. 원격 PowerShell을 사용 하 여 장치에 성공적으로 연결 하면 다음 샘플 출력이 표시 됩니다.  

    ```
    Windows PowerShell
    Copyright (C) Microsoft Corporation. All rights reserved.
    
    PS C:\WINDOWS\system32> winrm quickconfig
    WinRM service is already running on this machine.
    PS C:\WINDOWS\system32> $ip = "10.100.10.10"
    PS C:\WINDOWS\system32> Set-Item WSMan:\localhost\Client\TrustedHosts $ip -Concatenate -Force
    PS C:\WINDOWS\system32> Enter-PSSession -ComputerName $ip -Credential $ip\EdgeUser -ConfigurationName Minishell -UseSSL

    WARNING: The Windows PowerShell interface of your device is intended to be used only for the initial network configuration. Please engage Microsoft Support if you need to access this interface to troubleshoot any potential issues you may be experiencing. Changes made through this interface without involving Microsoft Support could result in an unsupported configuration.
    [10.100.10.10]: PS>
    ```

### <a name="remotely-connect-from-a-linux-client"></a>Linux 클라이언트에서 원격으로 연결

연결에 사용할 Linux 클라이언트에서 다음을 수행 합니다.

- GitHub에서 [Linux 용 최신 PowerShell Core를 설치](/powershell/scripting/install/installing-powershell-core-on-linux) 하 여 SSH 원격 기능을 가져옵니다. 
- [ `gss-ntlmssp` NTLM 모듈의 패키지만 설치](https://github.com/Microsoft/omi/blob/master/Unix/doc/setup-ntlm-omi.md)합니다. Ubuntu 클라이언트의 경우 다음 명령을 사용 합니다.
    - `sudo apt-get install gss-ntlmssp`

자세한 내용은 [SSH를 통한 PowerShell 원격](/powershell/scripting/learn/remoting/ssh-remoting-in-powershell-core)을 참조 하세요.

NFS 클라이언트에서 원격으로 연결 하려면 다음 단계를 수행 합니다.

1. PowerShell 세션을 열려면 다음을 입력 합니다.

    `pwsh`
 
2. 원격 클라이언트를 사용 하 여 연결 하려면 다음을 입력 합니다.

    `Enter-PSSession -ComputerName $ip -Authentication Negotiate -ConfigurationName Minishell -Credential ~\EdgeUser`

    메시지가 표시 되 면 장치에 로그인 하는 데 사용할 암호를 제공 합니다.
 
> [!NOTE]
> 이 절차는 Mac OS에서 작동 하지 않습니다.