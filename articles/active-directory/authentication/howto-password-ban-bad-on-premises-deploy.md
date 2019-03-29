---
title: Azure AD 암호 보호-Azure Active Directory를 배포 합니다.
description: 잘못 된 암호가 온-프레미스를 금지 하려면 Azure AD 암호 보호를 배포 합니다.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: article
ms.date: 02/01/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3be702d1f75b0a96e22ea03602c924be580b0968
ms.sourcegitcommit: f24fdd1ab23927c73595c960d8a26a74e1d12f5d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2019
ms.locfileid: "58499253"
---
# <a name="deploy-azure-ad-password-protection"></a>Azure AD 암호 보호 배포

이해 했으므로 [Windows Server Active Directory에 대 한 Azure AD 암호 보호를 적용 하는 방법](concept-password-ban-bad-on-premises.md)를 계획 하 고 배포를 실행 하는 다음 단계입니다.

## <a name="deployment-strategy"></a>배포 전략

감사 모드에서 배포를 시작 하는 것이 좋습니다. 감사 모드는 암호를 설정할 계속할 수 있는 기본 초기 설정 합니다. 차단 되는 암호는 이벤트 로그에 기록 됩니다. 감사 모드에서 프록시 서버 및 DC 에이전트를 배포한 후에 암호 정책이 사용자 환경에 정책을 적용 하는 경우 미치는 영향을 모니터링 해야 합니다.

감사 단계 중 대부분의 조직에서는 알아낼:

* 더 안전한 암호를 사용하도록 기존 운영 프로세스를 개선해야 합니다.
* 사용자는 종종 보안 되지 않은 암호를 사용합니다.
* 사용자에 게 보다 안전한 암호를 선택 하는 방법, 보안 적용 및에 미치는 영향에 예정 된 변경 해야 합니다.

적당 한 시간에 대 한 감사 모드에서 실행 된 기능, 후 구성을 전환할 수 있습니다 *감사* 하 *적용* 더 안전한 암호를 요구 하도록 합니다. 이 시간 동안 집중적으로 모니터링하는 것이 좋습니다.

## <a name="deployment-requirements"></a>배포 요구 사항

* Windows Server 2012를 실행 해야 설치 하는 Azure AD 암호 보호에 대 한 서비스 이상 DC 에이전트를 받는 모든 도메인 컨트롤러입니다. 이 요구 사항은 Active Directory 도메인 또는 포리스트에 Windows Server 2012 도메인 또는 포리스트 기능 수준에서 수도 있어야 하는 것을 의미 하지 않습니다. 설명 했 듯이 [디자인 원칙](concept-password-ban-bad-on-premises.md#design-principles), 최소 DFL 또는 중 하나는 DC 에이전트 또는 프록시 소프트웨어를 실행 하는 데 필요한 FFL 필요 하지 않습니다.
* 설치 하는 Azure AD 암호 보호는 Windows Server 2012 R2를 실행 해야 하는 한 서비스 이상 프록시를 받는 모든 컴퓨터입니다.
* Azure AD 암호 보호 프록시 서비스를 설치할 모든 컴퓨터에 설치 하는.NET 4.7 있어야 합니다.
  .NET 4.7 완전히 업데이트 된 Windows 서버에 이미 설치 되어야 합니다. 없는 경우 다운로드 하 고 있는 설치 관리자를 실행 [The.NET Framework 4.7 오프 라인 설치 관리자에서 Windows에 대 한](https://support.microsoft.com/en-us/help/3186497/the-net-framework-4-7-offline-installer-for-windows)합니다.
* 모든 컴퓨터에 설치 된 유니버설 C 런타임 가져올 Azure AD 암호 보호 구성 요소를 설치 하는 도메인 컨트롤러를 포함 하 여 있어야 합니다. 런타임은 Windows Update에서 모든 업데이트가 있는지 확인 하 여 가져올 수 있습니다. 또는 OS 특정 업데이트 패키지에서 가져올 수 있습니다. 자세한 내용은 [Windows의 유니버설 C 런타임 업데이트](https://support.microsoft.com/help/2999226/update-for-uniersal-c-runtime-in-windows)합니다.
* 네트워크 연결이 존재 해야 각 도메인에 하나 이상의 도메인 컨트롤러와 하나 이상의 서버를 호스트 하는 암호 보호에 대 한 프록시 서비스입니다. 이 연결에 프록시 서비스에서 RPC 서버 포트와 RPC 끝점 매퍼 포트 135 액세스 하려면 도메인 컨트롤러를 허용 해야 합니다. 기본적으로 RPC 서버 포트를 RPC 동적 포트를 이지만 하도록 구성할 수 있습니다 [정적 포트를 사용 하 여](#static)입니다.
* 프록시 서비스를 호스팅하는 모든 컴퓨터에 네트워크는 다음 끝점에 액세스할 수 있어야 합니다.

    |**엔드포인트**|**목적**|
    | --- | --- |
    |`https://login.microsoftonline.com`|인증 요청|
    |`https://enterpriseregistration.windows.net`|Azure AD 암호 보호 기능|

* 암호 보호에 대 한 프록시 서비스를 호스팅하는 모든 컴퓨터는 아웃 바운드 TLS 1.2 HTTP 트래픽을 허용 하도록 구성 되어야 합니다.
* Azure AD 암호 보호 및 포리스트를 위한 프록시 서비스를 등록 하는 전역 관리자 계정.
* Azure AD를 사용 하 여 Windows Server Active Directory 포리스트를 등록 하려면 포리스트 루트 도메인의 Active Directory 도메인 관리자 권한이 있는 계정입니다.
* DC 에이전트 서비스 소프트웨어를 실행 하는 Active Directory 도메인의 sysvol 복제에 대 한 파일 시스템 복제 (DFSR (분산)를 사용 해야 합니다.
* 도메인의 Windows Server 2012를 실행 하는 모든 도메인 컨트롤러에서 키 배포 서비스를 사용할 수 있어야 합니다. 기본적으로이 서비스는 수동 트리거 시작을 통해 활성화 됩니다.

## <a name="single-forest-deployment"></a>단일 포리스트 배포

다음 다이어그램에서는 Azure AD 암호 보호의 기본 구성 요소 함께 온-프레미스 Active Directory 환경에서 작업 하는 방법을 보여 줍니다.

![Azure AD 암호 보호 구성 요소가 함께 작동하는 방법](./media/concept-password-ban-bad-on-premises/azure-ad-password-protection.png)

배포 하기 전에 소프트웨어의 작동 방식을 검토 하는 것이 좋습니다. 참조 [Azure AD 암호 보호에 대 한 개념적 개요](concept-password-ban-bad-on-premises.md)합니다.

### <a name="download-the-software"></a>소프트웨어 다운로드

Azure AD 암호 보호를 위한 두 명의 필수 설치 관리자 이며 사용할 수는 [Microsoft 다운로드 센터](https://www.microsoft.com/download/details.aspx?id=57071)합니다.

### <a name="install-and-configure-the-proxy-service-for-password-protection"></a>암호 보호에 대 한 프록시 서비스 설치 및 구성

1. 암호 보호에 대 한 프록시 서비스를 호스팅하는 하나 이상의 서버를 선택 합니다.
   * 만 이러한 각 서비스는 단일 포리스트에 대 한 암호 정책의 제공할 수 있습니다. 호스트 컴퓨터는 해당 포리스트의 도메인에에서 조인할 수 있어야 합니다. 루트 및 자식 도메인 둘 다 지원 됩니다. 포리스트의 각 도메인에 하나 이상의 DC와 암호 보호 컴퓨터 간의 네트워크 연결을 해야합니다.
   * 테스트에 대 한 도메인 컨트롤러에서 프록시 서비스를 실행할 수 있습니다. 하지만 해당 도메인 컨트롤러에는 다음 보안 문제가 발생 될 수 있는 인터넷 연결이 필요 합니다. 이 구성은 테스트용 으로만 사용 하는 것이 좋습니다.
   * 중복성을 위해 두 개 이상의 프록시 서버가 사용 하는 것이 좋습니다. 참조 [고가용성](howto-password-ban-bad-on-premises-deploy.md#high-availability)합니다.

1. 사용 하 여 설치 된 Azure AD 암호 보호 프록시 서비스는 `AzureADPasswordProtectionProxySetup.exe` 소프트웨어 설치 관리자입니다.
   * 소프트웨어가 설치되면 다시 부팅할 필요가 없습니다. 소프트웨어 설치는 표준 MSI 절차를 사용하여 자동화할 수 있습니다. 예를 들면 다음과 같습니다.

      `AzureADPasswordProtectionProxySetup.exe /quiet`

      > [!NOTE]
      > 설치 오류를 방지 하려면 AzureADPasswordProtectionProxySetup.msi 패키지를 설치 하기 전에 Windows 방화벽 서비스를 실행 해야 합니다. 를 실행 하지 않도록 Windows 방화벽을 구성 된 경우 해결 일시적으로 사용 하도록 설정 하 고 방화벽 서비스를 설치 하는 동안 실행 됩니다. 프록시 소프트웨어 설치 후 Windows 방화벽에서 특정 종속성을이 합니다. 타사 방화벽을 사용 하는 경우 여전히 배포 요구 사항을 충족 하도록 구성 합니다. 여기에 135 및 프록시 서버 포트를 RPC 포트에 대 한 인바운드 액세스를 허용 합니다. 참조 [배포 요구 사항](howto-password-ban-bad-on-premises-deploy.md#deployment-requirements)합니다.

1. 관리자 권한으로 PowerShell 창을 엽니다.
   * 새 PowerShell 모듈의 경우를 포함 하는 암호 보호 프록시 소프트웨어 *AzureADPasswordProtection*합니다. 다음 단계를이 PowerShell 모듈에서 다양 한 cmdlet을 실행 합니다. 다음과 같이 새 모듈을 가져옵니다.

      ```powershell
      Import-Module AzureADPasswordProtection
      ```

   * 서비스가 실행 되 고 있는지를 확인 하려면 다음 PowerShell 명령을 사용 합니다.

      `Get-Service AzureADPasswordProtectionProxy | fl`.

     결과 표시할지를 **상태** "running."

1. 프록시를 등록합니다.
   * 3 단계 완료 되 면 프록시 서비스는 컴퓨터에서 실행 됩니다. 하지만 서비스는 Azure AD와 통신 하는 데 필요한 자격을 아직 없습니다. Azure AD 사용 하 여 등록 하는 것이 필요 합니다.

     `Register-AzureADPasswordProtectionProxy`

     이 cmdlet에는 Azure 테 넌 트에 대 한 전역 관리자 자격 증명이 필요합니다. 포리스트 루트 도메인에 온-프레미스 Active Directory 도메인 관리자 권한을 해야합니다. 프록시 서비스에 대 한이 명령은 한 번에 성공한 경우의 추가 호출은 성공 하지만 필요 하지 않습니다.

      `Register-AzureADPasswordProtectionProxy` cmdlet은 다음 세 가지 인증 모드를 지원 합니다.

     * 대화형 인증 모드:

        ```powershell
        Register-AzureADPasswordProtectionProxy -AccountUpn 'yourglobaladmin@yourtenant.onmicrosoft.com'
        ```
        > [!NOTE]
        > 이 모드는 Server Core 운영 체제에서 작동 하지 않습니다. 대신 다음 인증 모드 중 하나를 사용 합니다. 또한이 모드는 Internet Explorer 보안 강화 구성을 사용 하는 경우 실패할 수 있습니다. 해결 방법은 해당 구성을 사용 하지 않도록 설정, 프록시를 등록 한 다음 다시 설정 하는 것입니다.

     * 디바이스 코드 인증 모드:

        ```powershell
        Register-AzureADPasswordProtectionProxy -AccountUpn 'yourglobaladmin@yourtenant.onmicrosoft.com' -AuthenticateUsingDeviceCode
        To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code XYZABC123 to authenticate.
        ```

        그런 다음 다른 장치에서 표시 된 지침에 따라 인증을 완료 합니다.

     * 자동(암호 기반) 인증 모드:

        ```powershell
        $globalAdminCredentials = Get-Credential
        Register-AzureADPasswordProtectionProxy -AzureCredential $globalAdminCredentials
        ```

        > [!NOTE]
        > 이 모드는 Azure Multi-factor Authentication이 필요한 경우 실패 합니다. 이 경우 이전 두 가지 인증 모드 중 하나를 사용 합니다.

       지정할 필요가 현재 합니다 *-ForestCredential* 향후 기능에 대 한 예약 된 매개 변수입니다.

   
   암호 보호에 대 한 프록시 서비스의 등록이 필요한 한 번만 서비스의 수명에 합니다. 프록시 서비스는 그 후에 다른 필요한 유지 관리를 자동으로 수행 합니다.

   > [!TIP]
   > 이 cmdlet은 특정 Azure 테 넌 트에 대해 실행 되는 처음으로 완료 되기 전에 현저한 지연이 있을 수 있습니다. 오류가 보고 된 경우가 아니면이 지연에 대 한 걱정 하지 마세요.

1. 포리스트를 등록합니다.
   * 사용 하 여 Azure와 통신 하는 데 필요한 자격 증명을 사용 하 여 온-프레미스 Active Directory 포리스트를 초기화 해야 합니다는 `Register-AzureADPasswordProtectionForest` PowerShell cmdlet. Cmdlet는 Azure 테 넌 트에 대 한 전역 관리자 자격 증명이 필요합니다. 또한 포리스트 루트 도메인의 온-프레미스 Active Directory 도메인 관리자 권한이 필요합니다. 이 단계는 포리스트마다 한 번씩 실행됩니다.

      `Register-AzureADPasswordProtectionForest` cmdlet은 다음 세 가지 인증 모드를 지원 합니다.

     * 대화형 인증 모드:

        ```powershell
        Register-AzureADPasswordProtectionForest -AccountUpn 'yourglobaladmin@yourtenant.onmicrosoft.com'
        ```
        > [!NOTE]
        > 이 모드는 Server Core 운영 체제에서 작동 하지 않습니다. 대신 다음 두 가지 인증 모드 중 하나를 사용 합니다. 또한이 모드는 Internet Explorer 보안 강화 구성을 사용 하는 경우 실패할 수 있습니다. 해결 방법은 해당 구성을 사용 하지 않도록 설정, 프록시를 등록 한 다음 다시 설정 하는 것입니다.  

     * 디바이스 코드 인증 모드:

        ```powershell
        Register-AzureADPasswordProtectionForest -AccountUpn 'yourglobaladmin@yourtenant.onmicrosoft.com' -AuthenticateUsingDeviceCode
        To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code XYZABC123 to authenticate.
        ```

        그런 다음 다른 장치에서 표시 된 지침에 따라 인증을 완료 합니다.

     * 자동(암호 기반) 인증 모드:
        ```powershell
        $globalAdminCredentials = Get-Credential
        Register-AzureADPasswordProtectionForest -AzureCredential $globalAdminCredentials
        ```

        > [!NOTE]
        > 이 모드는 Azure Multi-factor Authentication이 필요한 경우 실패 합니다. 이 경우 이전 두 가지 인증 모드 중 하나를 사용 합니다.

       이 예제에서 현재 로그인 한 사용자 루트 도메인의 Active Directory 도메인 관리자 이기도 한 경우에 성공 합니다. 이 없으면 통해 대체 도메인 자격 증명을 제공할 수 있습니다 합니다 *-ForestCredential* 매개 변수입니다.

   > [!NOTE]
   > 여러 프록시 서버 환경에 설치 되는 경우 프록시 서버를 사용 하는 포리스트 등록 중요 하지 않습니다.

   > [!TIP]
   > 이 cmdlet은 특정 Azure 테 넌 트에 대해 실행 되는 처음으로 완료 되기 전에 현저한 지연이 있을 수 있습니다. 오류가 보고 된 경우가 아니면이 지연에 대 한 걱정 하지 마세요.

   Active Directory 포리스트의 등록이 필요한 한 번만 포리스트의 수명에서 합니다. 포리스트의 도메인 컨트롤러 에이전트는 그 후에 다른 필요한 유지 관리를 자동으로 수행 합니다. 후 `Register-AzureADPasswordProtectionForest` 포리스트에 대해 성공적으로 실행 cmdlet의 추가 호출은 성공 하지만 필요 하지 않습니다.

   에 대 한 `Register-AzureADPasswordProtectionForest` 성공 하기 위해서는 Windows Server 2012 이상을 실행 하는 하나 이상의 도메인 컨트롤러에서에서 사용할 수 있어야 해당 프록시 서버의 도메인입니다. 하지만 DC 에이전트 소프트웨어를이 단계 전에 모든 도메인 컨트롤러에 설치할 필요가 없습니다.

1. HTTP 프록시를 통해 통신할 수 있도록 암호 보호에 대 한 프록시 서비스를 구성 합니다.

   환경의 Azure와 통신 하는 특정 HTTP 프록시 사용에 필요한 경우이 메서드를 사용 합니다. 만들기는 *AzureADPasswordProtectionProxy.exe.config* %ProgramFiles%\Azure AD 암호 보호 Proxy\Service 폴더의 파일입니다. 다음 콘텐츠를 포함 합니다.

      ```xml
      <configuration>
        <system.net>
          <defaultProxy enabled="true">
           <proxy bypassonlocal="true"
               proxyaddress="http://yourhttpproxy.com:8080" />
          </defaultProxy>
        </system.net>
      </configuration>
      ```

   HTTP 프록시에 인증이 필요한 경우 추가 합니다 *useDefaultCredentials* 태그:

      ```xml
      <configuration>
        <system.net>
          <defaultProxy enabled="true" useDefaultCredentials="true">
           <proxy bypassonlocal="true"
               proxyaddress="http://yourhttpproxy.com:8080" />
          </defaultProxy>
        </system.net>
      </configuration>
      ```

   두 경우 모두 대체 `http://yourhttpproxy.com:8080` 특정 HTTP 프록시 서버 포트와 주소입니다.

   HTTP 프록시에 구성 된 경우 저희에 게 권한 부여 정책에 부여 해야 암호 보호에 대 한 프록시 서비스를 호스팅하는 컴퓨터의 Active Directory 컴퓨터 계정에 대 한 액세스.

   중지 하 고 만들기 또는 업데이트 한 후에 프록시 서비스를 다시 시작 하는 것이 좋습니다 합니다 *AzureADPasswordProtectionProxy.exe.config* 파일입니다.

   프록시 서비스는 HTTP 프록시에 연결 하기 위한 특정 자격 증명을 사용을 지원 하지 않습니다.

1. 선택 사항: 특정 포트에서 수신 대기할 암호 보호에 대 한 프록시 서비스를 구성 합니다.
   * 도메인 컨트롤러에 암호 보호를 위한 DC 에이전트 소프트웨어를 프록시 서비스와 통신 하도록 TCP를 통한 RPC를 사용 합니다. 프록시 서비스는 기본적으로 모든 사용 가능한 동적 RPC 끝점에서 수신합니다. 하지만이 필요한 경우 네트워킹 토폴로지 또는 환경의 방화벽 요구 사항으로 인해 특정 TCP 포트로 수신 하도록 서비스를 구성할 수 있습니다.
      * <a id="static" /></a>고정 포트에서 실행 되도록 서비스를 구성 하려면 사용 된 `Set-AzureADPasswordProtectionProxyConfiguration` cmdlet.
         ```powershell
         Set-AzureADPasswordProtectionProxyConfiguration –StaticPort <portnumber>
         ```

         > [!WARNING]
         > 이러한 변경 내용을 적용하려면 서비스를 중지했다가 다시 시작해야 합니다.

      * 동적 포트에서 실행 되도록 서비스를 구성 하려면 동일한 절차를 사용 하지만 설정 *StaticPort* 0 돌아가기:
         ```powershell
         Set-AzureADPasswordProtectionProxyConfiguration –StaticPort 0
         ```

         > [!WARNING]
         > 이러한 변경 내용을 적용하려면 서비스를 중지했다가 다시 시작해야 합니다.

   > [!NOTE]
   > 암호 보호에 대 한 프록시 서비스 포트 구성에서 변경 된 후 수동으로 다시 시작이 필요 합니다. 하지만 이러한 구성 변경을 수행한 후에 도메인 컨트롤러에서 DC 에이전트 서비스 소프트웨어를 다시 시작 하지 않아도 됩니다.

   * 서비스의 현재 구성에 대 한 쿼리를 사용 하 여는 `Get-AzureADPasswordProtectionProxyConfiguration` cmdlet:

      ```powershell
      Get-AzureADPasswordProtectionProxyConfiguration | fl

      ServiceName : AzureADPasswordProtectionProxy
      DisplayName : Azure AD password protection Proxy
      StaticPort  : 0
      ```

### <a name="install-the-dc-agent-service"></a>DC 에이전트 서비스 설치

   암호 보호를 위한 DC 에이전트 서비스를 사용 하 여 설치를 `AzureADPasswordProtectionDCAgentSetup.msi` 패키지 있습니다.

   소프트웨어 설치 또는 제거, 다시 시작을 해야 합니다. 암호 필터 Dll이 로드 되거나 다시 시작 하 여 언로드만 때문입니다.

   도메인 컨트롤러를 아직 되지 않은 컴퓨터에서 DC 에이전트 서비스를 설치할 수 있습니다. 이 경우 서비스는 시작 및 실행 되지만 컴퓨터는 도메인 컨트롤러로 승격 될 때까지 비활성 상태로 유지 합니다.

   표준 MSI 절차를 사용 하 여 소프트웨어 설치를 자동화할 수 있습니다. 예를 들면 다음과 같습니다.

   `msiexec.exe /i AzureADPasswordProtectionDCAgentSetup.msi /quiet /qn`

   > [!WARNING]
   > 다음 예제에서는 msiexec 명령을 즉시 다시 부팅 합니다. 이 문제를 방지 하려면 사용 된 `/norestart` 플래그입니다.

도메인 컨트롤러에서 DC 에이전트 소프트웨어를 설치 하는 해당 컴퓨터를 다시 부팅 후 설치가 완료 되었습니다. 다른 구성은 필요하지 않거나 가능하지 않습니다.

## <a name="multiple-forest-deployments"></a>다중 포리스트 배포

Azure AD 암호 보호를 여러 포리스트에 배포하기 위한 추가 요구 사항은 없습니다. 각 포리스트에 별도로 구성할 "단일 포리스트 배포" 섹션에 설명 된 대로 합니다. 각 암호 보호 프록시 도메인 컨트롤러에 가입 된 포리스트를 지원할 수 있습니다. 모든 포리스트에서 암호 보호 소프트웨어는 다른 포리스트의 Active Directory 트러스트 구성에 관계 없이 배포 되는 암호 보호 소프트웨어 인식 하지 못합니다.

## <a name="read-only-domain-controllers"></a>읽기 전용 도메인 컨트롤러

암호 변경 하거나 설정 하지 처리 되 고 읽기 전용 도메인 컨트롤러 (Rodc)에 저장 합니다. 이러한 쓰기 가능한 도메인 컨트롤러에 전달 됩니다. 따라서 Rodc에 DC 에이전트 소프트웨어를 설치할 필요가 없습니다.

## <a name="high-availability"></a>고가용성

암호 보호를 위해 주 가용성 문제를 프록시 서버의 가용성을 경우 포리스트의 도메인 컨트롤러를 Azure에서 새 정책 또는 다른 데이터를 다운로드 하려고 합니다. 각 DC 에이전트를 호출 하는 프록시 서버를 결정할 때 단순한 라운드 로빈 스타일 알고리즘을 사용 합니다. 에이전트가 응답 하지 않는 프록시 서버를 건너뜁니다. Directory 및 sysvol 폴더 상태 정상 복제 하는 가장 완전 하 게 연결 된 Active Directory 배포에서는 두 명의 프록시 서버는 가용성을 보장 하기에 충분 합니다. 그러면 새 정책의 시기 적절 하 게 다운로드 및 기타 데이터. 하지만 추가 프록시 서버를 배포할 수 있습니다.

DC 에이전트 소프트웨어 설계의 높은 가용성과 관련 된 일반적인 문제를 완화 합니다. DC 에이전트의 암호 정책 가장 최근에 다운로드 한 로컬 캐시를 유지 관리합니다. 사용할 수 없게 하는 프록시 서버를 등록 하는 모든 경우에 DC 에이전트 계속 해당 캐시 된 암호 정책 강제 적용 합니다. 대규모 배포에서 암호 정책에 대 한 적절 한 업데이트 빈도 일반적으로 *일*, 몇 시간이 아닌 미만입니다. 따라서 프록시 서버에 대 한 간략 한 중단 없는 상당한 영향을 줄 Azure AD 암호 보호.

## <a name="next-steps"></a>다음 단계

온-프레미스 서버에서 Azure AD 암호 보호 해야 하는 서비스를 설치한 했으므로 [설치 후 구성 및 보고 정보 수집을 수행할](howto-password-ban-bad-on-premises-operations.md) 배포를 완료 합니다.

[Azure AD 암호 보호에 대한 개념적 개요](concept-password-ban-bad-on-premises.md)
