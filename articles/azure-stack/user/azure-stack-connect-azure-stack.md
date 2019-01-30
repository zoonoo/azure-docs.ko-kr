---
title: Azure Stack에 연결 | Microsoft Docs
description: Azure Stack에 연결 하는 방법 알아보기
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: 3cebbfa6-819a-41e3-9f1b-14ca0a2aaba3
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/14/2019
ms.author: mabrigg
ms.lastreviewed: 01/14/2019
ms.openlocfilehash: b7f9bda37d63956ea672528c28b81eaaac9891a6
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/30/2019
ms.locfileid: "55243769"
---
# <a name="connect-to-azure-stack"></a>Azure Stack에 연결

리소스를 관리 하려면 Azure Stack 개발 키트에 연결 해야 합니다. 이 문서에서는 자세히 설명 된 단계 개발 키트를 연결 해야 합니다. 다음 연결 옵션 중 하나를 사용할 수 있습니다.

* [원격 데스크톱](#connect-with-remote-desktop): 개발 키트의 신속 하 게 연결 하는 단일 동시 사용자 수 있습니다.
* [가상 개인 네트워크 (VPN)](#connect-with-vpn): 클라이언트 (구성 필요) 하는 Azure Stack 인프라 외부에서 여러 동시 사용자 연결 수 있습니다.

## <a name="connect-to-azure-stack-with-remote-desktop"></a>원격 데스크톱을 사용 하 여 Azure Stack에 연결
원격 데스크톱 연결을 사용 하 여 한 명의 동시 사용자 리소스를 관리 포털을 사용 하 여 작업할 수 있습니다.

1. 원격 데스크톱 연결을 열고 개발 키트를 연결 합니다. 입력 **AzureStack\AzureStackAdmin** 사용자 이름 및 Azure Stack 설치 하는 동안 제공한 관리자 암호입니다.  

2. 서버 관리자를 열고 개발 키트 컴퓨터에서 클릭 **로컬 서버**, Internet Explorer 보안 강화를 해제 하 고 서버 관리자를 닫습니다.

3. 포털을 열로 이동 (https://portal.local.azurestack.external/) 사용자 자격 증명을 사용 하 여 로그인 합니다.


## <a name="connect-to-azure-stack-with-vpn"></a>VPN 사용 하 여 Azure Stack에 연결

가상 개인 네트워크 (VPN) 연결 된 Azure Stack 개발 키트에 대 한 분할 터널을 설정할 수 있습니다. VPN 연결을 통해 사용자 포털 관리자 포털에 액세스할 수 있습니다 하 고 로컬로 Azure Stack 리소스를 관리 하는 Visual Studio 및 PowerShell 같은 도구를 설치 합니다. Active Directory 페더레이션 Services(AD FS) 기반 배포 및 VPN 연결 모두 Azure Active Directory(AAD)에서 지원 됩니다. VPN 연결을 여러 클라이언트가 동시에 Azure Stack에 연결할 사용 합니다. 

> [!NOTE] 
> 이 VPN 연결에서 Azure Stack 인프라 Vm에 대 한 연결을 제공 하지 않습니다. 

### <a name="prerequisites"></a>필수 조건

* 설치할 [Azure Stack 호환 가능한 Azure PowerShell](azure-stack-powershell-install.md) 로컬 컴퓨터에 있습니다.  
* 일관 된 [azure storage: 차이점 및 고려 사항](azure-stack-powershell-download.md). 

### <a name="configure-vpn-connectivity"></a>VPN 연결 구성

개발 키트에는 VPN 연결을 만들려면 로컬 Windows 기반 컴퓨터에서 관리자 권한 PowerShell 세션을 열고 다음 스크립트 (사용자 환경에 대 한 IP 주소 및 암호 값을 업데이트 하도록 확인)를 실행 합니다.

```PowerShell 
# Configure winrm if it's not already configured
winrm quickconfig  

Set-ExecutionPolicy RemoteSigned

# Import the Connect module
Import-Module .\Connect\AzureStack.Connect.psm1 

# Add the development kit computer’s host IP address & certificate authority (CA) to the list of trusted hosts. Make sure to update the IP address and password values for your environment. 

$hostIP = "<Azure Stack host IP address>"

$Password = ConvertTo-SecureString `
  "<Administrator password provided when deploying Azure Stack>" `
  -AsPlainText `
  -Force

Set-Item wsman:\localhost\Client\TrustedHosts `
  -Value $hostIP `
  -Concatenate

# Create a VPN connection entry for the local user
Add-AzsVpnConnection `
  -ServerAddress $hostIP `
  -Password $Password

```

설치에 성공 하면 표시 됩니다 **azurestack** VPN 연결 목록에 있습니다.

![네트워크 연결](media/azure-stack-connect-azure-stack/image3.png)  

### <a name="connect-to-azure-stack"></a>Azure Stack에 연결

다음 두 가지 방법 중 하나를 사용 하 여 Azure Stack 인스턴스에 연결 합니다.  

* 사용 하 여는 `Connect-AzsVpn ` 명령: 
    
  ```PowerShell
  Connect-AzsVpn `
    -Password $Password
  ```

  메시지가 표시 되 면 Azure Stack 호스트 신뢰 및에서 인증서를 설치 **AzureStackCertificateAuthority** 로컬 컴퓨터의 인증서 저장소에 있습니다. (프롬프트 PowerShell 세션 창 뒤에 나타날 수 있습니다). 

* 로컬 컴퓨터의 엽니다 **네트워크 설정** > **VPN** > 클릭 **azurestack** > **연결**합니다. 로그인 프롬프트 (AzureStack\AzureStackAdmin) 사용자 이름 및 암호를 입력 합니다.

### <a name="test-the-vpn-connectivity"></a>VPN 연결을 테스트 합니다.

포털 연결을 테스트 하려면 인터넷 브라우저를 열고 사용자 포털로 이동 (https://portal.local.azurestack.external/), 로그인 및 리소스를 만듭니다.  

## <a name="next-steps"></a>다음 단계



