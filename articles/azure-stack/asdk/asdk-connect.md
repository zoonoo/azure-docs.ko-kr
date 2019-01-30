---
title: Azure Stack에 연결 | Microsoft Docs
description: ASDK를 연결 하는 방법에 알아봅니다.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: 3cebbfa6-819a-41e3-9f1b-14ca0a2aaba3
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/25/2018
ms.author: jeffgilb
ms.reviewer: knithinc
ms.lastreviewed: 10/25/2018
ms.openlocfilehash: 44f12c72716e98e2f28796359467685e0af579dd
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/30/2019
ms.locfileid: "55238250"
---
# <a name="connect-to-the-asdk"></a>ASDK에 연결

리소스를 관리 하려면 먼저 Azure Stack 개발 키트 (ASDK)에 연결 해야 합니다. 이 문서에서는 ASDK 다음과 같은 연결 옵션을 사용 하 여 연결을 수행 하는 단계에 설명 합니다.

* [원격 데스크톱 연결 (RDP)](#connect-with-rdp)합니다. 원격 데스크톱 연결을 사용 하 여 연결 하면 단일 사용자 개발 키트를 빠르게 연결할 수 있습니다.
* [가상 사설망 (VPN)](#connect-with-vpn)합니다. VPN을 사용 하 여 연결 하면 여러 사용자가 Azure Stack 인프라 외부 클라이언트에서 동시에 연결할 수 있습니다. VPN 연결에는 몇 가지 설정이 필요합니다.

<a name="connect-with-rdp"></a>
## <a name="connect-to-azure-stack-using-rdp"></a>RDP를 사용 하 여 Azure Stack에 연결

한 명의 동시 사용자 ASDK 호스트 컴퓨터에서 직접 Azure Stack 관리 포털 또는 원격 데스크톱 연결을 통해 사용자 포털에서 리소스를 관리할 수 있습니다. 

> [!TIP]
> 또한이 옵션을 사용 하면 다시 사용 하 여 RDP ASDK 호스트 컴퓨터에 로그인 하는 동안 ASDK 호스트 컴퓨터에서 만든 가상 컴퓨터에 로그인 수 있습니다. 

1. 원격 데스크톱 연결 (mstc.exe)를 열고 ASDK 호스트 컴퓨터에 원격으로 로그인 할 수 있는 권한이 있는 계정을 사용 하 여 개발 키트 호스트 컴퓨터 IP 주소에 연결 합니다. 기본적으로 **AzureStack\AzureStackAdmin** ASDK 호스트 컴퓨터에서 원격 권한이 있습니다.  

2. 개발 키트 호스트 컴퓨터에서 서버 관리자 (ServerManager.exe)를 엽니다. 선택 **로컬 서버**, 해제 **IE 보안 강화 구성**, 서버 관리자를 닫습니다.

3. 으로 관리 포털에 로그인 **AzureStack\CloudAdmin** 또는 다른 Azure Stack 운영자 자격 증명을 사용 합니다. ASDK 관리 포털 주소가 [ https://adminportal.local.azurestack.external ](https://adminportal.local.azurestack.external)합니다.

4. 로 사용자 포털에 로그인 **AzureStack\CloudAdmin** 또는 다른 Azure Stack 사용자 자격 증명을 사용 합니다. ASDK 사용자 포털 주소가 [ https://portal.local.azurestack.external ](https://portal.local.azurestack.external)합니다.

> [!NOTE]
> 어떤 계정을 사용 하는 경우에 대 한 자세한 내용은 참조 하세요. [ASDK 관리 기본 사항](asdk-admin-basics.md#what-account-should-i-use)합니다.

<a name="connect-with-vpn"></a>
## <a name="connect-to-azure-stack-using-vpn"></a>VPN을 사용 하 여 Azure Stack에 연결

분할 터널 Azure Stack 포털 및 Visual Studio 및 PowerShell과 같은 로컬에 설치 된 도구에 액세스 하는 ASDK 호스트 컴퓨터에 VPN 연결을 설정할 수 있습니다. VPN 연결을 사용 하 여 ASDK에서 호스팅하는 Azure Stack 리소스를 동시에 여러 사용자가 연결할 수 있습니다.

VPN 연결은 지원 모두 Azure AD 및 Active Directory Federation Services (AD FS) 배포 합니다.

> [!NOTE]
> VPN 연결 *하지 않습니다* Azure Stack Vm 연결을 제공 합니다. VPN을 통해 연결 하는 동안 Azure Stack Vm에 RDP 할 수 없습니다.

### <a name="prerequisites"></a>필수 조건
ASDK 대 한 VPN 연결을 설정 하기 전에 다음 필수 조건을 충족 했는지 확인 합니다.

- 설치 [Azure PowerShell Azure Stack 호환](asdk-post-deploy.md#install-azure-stack-powershell) 로컬 컴퓨터에 있습니다.  
- 일관 된 [azure storage: 차이점 및 고려 사항](asdk-post-deploy.md#download-the-azure-stack-tools).

### <a name="set-up-vpn-connectivity"></a>VPN 연결 설정

ASDK 대 한 VPN 연결을 만들려면 로컬 Windows 기반 컴퓨터에서 관리자 권한으로 PowerShell을 엽니다. 그런 다음 다음 스크립트 (사용자 환경에 대 한 IP 주소 및 암호 값 업데이트)를 실행 합니다.

```PowerShell
# Change directories to the default Azure Stack tools directory
cd C:\AzureStack-Tools-master

# Configure Windows Remote Management (WinRM), if it's not already configured.
winrm quickconfig  

Set-ExecutionPolicy RemoteSigned

# Import the Connect module.
Import-Module .\Connect\AzureStack.Connect.psm1

# Add the development kit host computer’s IP address as the ASDK certificate authority (CA) to the list of trusted hosts. Make sure you update the IP address and password values for your environment.

$hostIP = "<Azure Stack host IP address>"

$Password = ConvertTo-SecureString `
  "<operator's password provided when deploying Azure Stack>" `
  -AsPlainText `
  -Force

Set-Item wsman:\localhost\Client\TrustedHosts `
  -Value $hostIP `
  -Concatenate

# Create a VPN connection entry for the local user.
Add-AzsVpnConnection `
  -ServerAddress $hostIP `
  -Password $Password

```

설치에 성공 하면 **azurestack** VPN 연결 목록에 표시 됩니다.

![네트워크 연결](media/asdk-connect/vpn.png)  

### <a name="connect-to-azure-stack"></a>Azure Stack에 연결

  다음 방법 중 하나를 사용 하 여 Azure Stack 인스턴스에 연결 합니다.  

  * 사용 된 `Connect-AzsVpn ` 명령:
      
    ```PowerShell
    Connect-AzsVpn `
      -Password $Password
    ```

  * 로컬 컴퓨터에서 선택 **네트워크 설정** > **VPN** > **azurestack** > **연결**. 로그인 프롬프트에서 사용자 이름을 입력 합니다 (**AzureStack\AzureStackAdmin**) 및 암호입니다.

처음에 연결한 메시지가 표시 됩니다에서 Azure Stack 루트 인증서를 설치 하려면 **AzureStackCertificateAuthority** 로컬 컴퓨터의 인증서 저장소에 있습니다. 이 단계에서는 신뢰할 수 있는 호스트 목록에 ASDK 기관 (CA)를 추가 합니다. 클릭 **예** 인증서를 설치 합니다.

![루트 인증서](media/asdk-connect/cert.png)  
  
  > [!IMPORTANT]
  > PowerShell 창 또는 다른 응용 프로그램에서 프롬프트 숨겨질 수 있습니다.

### <a name="test-vpn-connectivity"></a>VPN 연결 테스트

포털 연결을 테스트 하려면 웹 브라우저를 열고 다음 중 하나는 사용자 포털로 이동 (https://portal.local.azurestack.external/) 또는 관리 포털 (https://adminportal.local.azurestack.external/)합니다. 

리소스 만들기 및 관리 하는 적절 한 구독 자격 증명으로 로그인 합니다.  

## <a name="next-steps"></a>다음 단계

[문제 해결](asdk-troubleshooting.md)
