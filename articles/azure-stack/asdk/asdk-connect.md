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
ms.date: 10/24/2018
ms.author: jeffgilb
ms.openlocfilehash: 55be312046f5cdea2c1481ed435b5859ab2c2540
ms.sourcegitcommit: f6050791e910c22bd3c749c6d0f09b1ba8fccf0c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50026930"
---
# <a name="connect-to-the-azure-stack-development-kit"></a>Azure Stack 개발 키트에 연결

리소스를 관리 하려면 먼저 Azure Stack 개발 키트 (ASDK)에 연결 해야 합니다. 이 문서는 ASDK에 연결 하기 위해 수행 하는 단계를 설명 합니다. 다음 연결 옵션 중 하나를 사용할 수 있습니다.

* [원격 데스크톱 연결](#connect-with-remote-desktop)합니다. 원격 데스크톱 연결을 사용 하 여 연결 하면 단일 사용자 개발 키트를 빠르게 연결할 수 있습니다.
* [가상 사설망 (VPN)](#connect-with-vpn)합니다. VPN을 사용 하 여 연결 하면 여러 사용자가 Azure Stack 인프라 외부 클라이언트에서 동시에 연결할 수 있습니다. VPN 연결에는 몇 가지 설정이 필요합니다.

<a name="connect-to-azure-stack-with-remote-desktop"></a>
##  <a name="connect-to-azure-stack-by-using-remote-desktop-connection"></a>원격 데스크톱 연결을 사용 하 여 Azure Stack에 연결

한 명의 동시 사용자 연산자 포털 또는 원격 데스크톱 연결을 통해 사용자 포털에서 리소스를 관리할 수 있습니다.

1. 원격 데스크톱 연결 (mstc.exe)를 열고로 개발 키트 호스트 컴퓨터에 연결 **AzureStack\AzureStackAdmin** ASDK 설치 하는 동안 지정한 암호를 사용 하 여 합니다.  

2. 개발 키트 호스트 컴퓨터에서 서버 관리자 (ServerManager.exe)를 엽니다. 선택 **로컬 서버**, 해제 **IE 보안 강화 구성**, 서버 관리자를 닫습니다.

3. 으로 관리 포털에 로그인 **AzureStack\CloudAdmin** 또는 다른 Azure Stack 운영자 자격 증명을 사용 합니다. ASDK 관리 포털 주소가 [ https://adminportal.local.azurestack.external ](https://adminportal.local.azurestack.external)합니다.

4. 로 사용자 포털에 로그인 **AzureStack\CloudAdmin** 또는 다른 Azure Stack 사용자 자격 증명을 사용 합니다. ASDK 사용자 포털 주소가 [ https://portal.local.azurestack.external ](https://portal.local.azurestack.external)합니다.

> [!NOTE]
> 어떤 계정을 사용 하는 경우에 대 한 자세한 내용은 참조 하세요. [ASDK 관리 기본 사항](asdk-admin-basics.md#what-account-should-i-use)합니다.

<a name="connect-to-azure-stack-with-vpn"></a>
## <a name="connect-to-azure-stack-by-using-vpn"></a>VPN을 사용 하 여 Azure Stack에 연결

분할 터널 Azure Stack 포털 및 Visual Studio 및 PowerShell과 같은 로컬에 설치 된 도구에 액세스 하는 ASDK에 VPN 연결을 설정할 수 있습니다. VPN 연결을 사용 하 여 ASDK에서 호스팅하는 Azure Stack 리소스를 동시에 여러 사용자가 연결할 수 있습니다.

VPN 연결은 지원 모두 Azure AD 및 Active Directory Federation Services (AD FS) 배포 합니다.

> [!NOTE]
> VPN 연결을 Azure Stack 인프라 Vm에 대 한 연결을 제공 하지 않습니다.

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

![네트워크 연결](media/asdk-connect/image3.png)  

### <a name="connect-to-azure-stack"></a>Azure Stack에 연결

다음 방법 중 하나를 사용 하 여 Azure Stack 인스턴스에 연결 합니다.  

* 사용 된 `Connect-AzsVpn ` 명령:
    
  ```PowerShell
  Connect-AzsVpn `
    -Password $Password
  ```

  메시지가 표시 되 면 Azure Stack 호스트 신뢰 및에서 인증서를 설치 **AzureStackCertificateAuthority** 로컬 컴퓨터의 인증서 저장소에 있습니다. 
  
  > [!IMPORTANT]
  > PowerShell 창에서 프롬프트 숨겨질 수 있습니다.

* 로컬 컴퓨터에서 선택 **네트워크 설정** > **VPN** > **azurestack** > **연결**. 로그인 프롬프트에서 사용자 이름을 입력 합니다 (**AzureStack\AzureStackAdmin**) 및 암호입니다.

### <a name="test-vpn-connectivity"></a>VPN 연결 테스트

포털 연결을 테스트 하려면 웹 브라우저를 열고 다음 중 하나는 사용자 포털로 이동 (https://portal.local.azurestack.external/) 또는 관리 포털 (https://adminportal.local.azurestack.external/)합니다. 에 로그인 하 고 리소스를 만듭니다.  

## <a name="next-steps"></a>다음 단계

[문제 해결](asdk-troubleshooting.md)
