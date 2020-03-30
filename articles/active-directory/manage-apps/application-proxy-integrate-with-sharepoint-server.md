---
title: 공유점 - Azure AD 응용 프로그램 프록시에 대한 원격 액세스 사용
description: 온-프레미스 SharePoint 서버를 Azure AD 애플리케이션 프록시와 통합하는 방법에 대한 기본 사항을 다룹니다.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/02/2019
ms.author: mimart
ms.reviewer: japere
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 983470994c103cb25d0d2aff96ae8544080e6288
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79481299"
---
# <a name="enable-remote-access-to-sharepoint-with-azure-ad-application-proxy"></a>Azure AD 애플리케이션 프록시를 통해 SharePoint에 원격 액세스를 사용하도록 설정

이 단계별 가이드에서는 온-프레미스 SharePoint 팜을 Azure Active Directory(Azure AD) 응용 프로그램 프록시와 통합하는 방법을 설명합니다.

## <a name="prerequisites"></a>사전 요구 사항

구성을 수행하려면 다음 리소스가 필요합니다.
- SharePoint 2013 팜 또는 최신.
- 응용 프로그램 프록시를 포함하는 계획이 있는 Azure AD 테넌트입니다. [Azure AD 요금제 및 가격 책정에](https://azure.microsoft.com/pricing/details/active-directory/)대해 자세히 알아보십시오.
- Azure AD 테넌트에서 [확인된 사용자 지정 도메인입니다.](../fundamentals/add-custom-domain.md)
- 온-프레미스 Active Directory Azure AD Connect와 동기화되어 사용자가 [Azure에 로그인할](../hybrid/plan-connect-user-signin.md)수 있습니다.
- 회사 도메인 내의 컴퓨터에서 응용 프로그램 프록시 커넥터를 설치하고 실행합니다.

응용 프로그램 프록시를 사용 하 고 SharePoint를 구성 하려면 두 개의 URL이 필요 합니다.
- 최종 사용자에게 표시되고 Azure AD에서 결정되는 외부 URL입니다. 이 URL은 사용자 지정 도메인을 사용할 수 있습니다. [Azure AD 응용 프로그램 프록시에서 사용자 지정 도메인으로 작업하는](application-proxy-configure-custom-domain.md)방법에 대해 자세히 알아봅니다.
- 회사 도메인 내에서만 알려져 있으며 직접 사용되지 않는 내부 URL입니다.

> [!IMPORTANT]
> 링크가 올바르게 매핑되었는지 확인하려면 내부 URL에 대한 다음 권장 사항을 따르십시오.
> - HTTPS 사용:
> - 사용자 지정 포트를 사용하지 마십시오.
> - 회사 도메인 이름 시스템(DNS)에서 별칭(CName)이 아닌 SharePoint WFE(또는 로드 밸런서)를 가리키는 호스트(A)를 만듭니다.

이 문서에서는 다음 값을 사용합니다.
- 내부 URL:`https://sharepoint`
- 외부 URL:`https://spsites-demo1984.msappproxy.net/`
- SharePoint 웹 응용 프로그램에 대한 응용 프로그램 풀 계정:`Contoso\spapppool`

## <a name="step-1-configure-an-application-in-azure-ad-that-uses-application-proxy"></a>1단계: 응용 프로그램 프록시를 사용하는 Azure AD에서 응용 프로그램 구성

이 단계에서는 응용 프로그램 프록시를 사용하는 Azure Active Directory 테넌트에서 응용 프로그램을 만듭니다. 외부 URL을 설정하고 SharePoint에서 나중에 사용되는 내부 URL을 지정합니다.

1. 다음 설정으로 설명된 대로 앱을 만듭니다. 단계별 지침은 [Azure AD 애플리케이션 프록시를 사용하여 애플리케이션 게시](application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad)를 참조하세요.
   * **내부 URL**: SharePoint 내부 URL(예: SharePoint에서 나중에 설정됩니다.) `https://sharepoint`
   * **사전 인증**: Azure Active Directory
   * **헤더로 URL 번역**: 아니요
   * **응용 프로그램 본문에서 URL 번역**: 아니오

   ![SharePoint를 애플리케이션으로 게시](./media/application-proxy-integrate-with-sharepoint-server/publish-app.png)

1. 앱이 게시된 후 다음 단계에 따라 단일 사인온 설정을 구성합니다.

   1. 포털의 애플리케이션 페이지에서 **Single Sign-On**을 선택합니다.
   1. **단일 사인온 모드의**경우 **통합 Windows 인증을**선택합니다.
   1. **내부 응용 프로그램 SPN을** 이전에 설정한 값으로 설정합니다. 이 예제의 경우 `HTTP/sharepoint`값은 입니다.
   1. **위임된 로그인 ID에서**Active Directory 포리스트 구성에 가장 적합한 옵션을 선택합니다. 예를 들어 포리스트에 단일 Active Directory 도메인이 있는 경우 다음 스크린샷과 같이 **온-프레미스 SAM 계정 이름을** 선택합니다. 그러나 사용자가 SharePoint 및 응용 프로그램 프록시 커넥터 서버와 동일한 도메인에 없는 경우 **온-프레미스 사용자** 주이름(스크린샷에 표시되지 않음)을 선택합니다.

   ![SSO용 통합 Windows 인증 구성](./media/application-proxy-integrate-with-sharepoint-server/configure-iwa.png)

1. 애플리케이션 설정을 완료하려면 **사용자 및 그룹** 섹션으로 이동하고 이 애플리케이션에 액세스할 사용자를 할당합니다. 

## <a name="step-2-configure-the-sharepoint-web-application"></a>2단계: SharePoint 웹 응용 프로그램 구성

SharePoint 웹 응용 프로그램은 Kerberos및 Azure AD 응용 프로그램 프록시에서 올바르게 작동하려면 적절한 대체 액세스 매핑으로 구성해야 합니다. 두 가지 옵션이 있습니다.

- 새 웹 응용 프로그램을 만들고 기본 영역만 사용합니다. 이 옵션은 SharePoint에서 최상의 환경을 제공하므로 기본 옵션입니다(예: SharePoint에서 생성된 전자 메일 경고의 링크는 항상 기본 영역을 가리킵니다).
- 기존 웹 응용 프로그램을 확장하여 기본이 아닌 영역에서 Kerberos를 구성합니다.

> [!IMPORTANT]
> 사용되는 영역에 관계없이 SharePoint 웹 응용 프로그램의 응용 프로그램 풀 계정은 Kerberos가 올바르게 작동하려면 도메인 계정이어야 합니다.

### <a name="provision-the-sharepoint-web-application"></a>SharePoint 웹 응용 프로그램 프로비전

- 새 웹 응용 프로그램을 만들고 기본 영역만 사용하는 경우(기본 옵션)

    1. **SharePoint 관리 셸을** 시작하고 다음 스크립트를 실행합니다.

       ```powershell
       # This script creates a web application and configures the Default zone with the internal/external URL needed to work with Azure AD Application Proxy
       # Edit variables below to fit your environment. Note that the managed account must exist and it must be a domain account
       $internalUrl = "https://sharepoint"
       $externalUrl = "https://spsites-demo1984.msappproxy.net/"
       $applicationPoolManagedAccount = "Contoso\spapppool"
            
       $winAp = New-SPAuthenticationProvider -UseWindowsIntegratedAuthentication -DisableKerberos:$false
       $wa = New-SPWebApplication -Name "SharePoint - AAD Proxy" -Port 443 -SecureSocketsLayer -URL $externalUrl -ApplicationPool "SharePoint - AAD Proxy" -ApplicationPoolAccount (Get-SPManagedAccount $applicationPoolManagedAccount) -AuthenticationProvider $winAp
       New-SPAlternateURL -Url $internalUrl -WebApplication $wa -Zone Default -Internal
       ```

    2. **SharePoint 중앙 관리** 사이트를 엽니다.
    1. **시스템 설정** 아래에서 **대체 액세스 매핑 구성**을 선택합니다. **대체 액세스 매핑 컬렉션** 상자가 열립니다.
    1. 새 웹 응용 프로그램으로 디스플레이를 필터링하고 다음과 같은 표시가 표시되는지 확인합니다.

       ![웹 응용 프로그램의 대체 액세스 매핑](./media/application-proxy-integrate-with-sharepoint-server/new-webapp-aam.png)

- 기존 웹 응용 프로그램을 새 영역으로 확장하는 경우(기본 영역을 사용할 수 없는 경우)

    1. SharePoint 관리 셸을 시작하고 다음 스크립트를 실행합니다.

       ```powershell
       # This script extends an existing web application to Internet zone with the internal/external URL needed to work with Azure AD Application Proxy
       # Edit variables below to fit your environment
       $webAppUrl = "http://spsites/"
       $internalUrl = "https://sharepoint"
       $externalUrl = "https://spsites-demo1984.msappproxy.net/"
       
       $winAp = New-SPAuthenticationProvider -UseWindowsIntegratedAuthentication -DisableKerberos:$false
       $wa = Get-SPWebApplication $webAppUrl
       New-SPWebApplicationExtension -Name "SharePoint - AAD Proxy" -Identity $wa -SecureSocketsLayer -Zone Extranet -Url $externalUrl -AuthenticationProvider $winAp
       New-SPAlternateURL -Url $internalUrl -WebApplication $wa -Zone Extranet -Internal
       ```

    2. **SharePoint 중앙 관리** 사이트를 엽니다.
    1. **시스템 설정** 아래에서 **대체 액세스 매핑 구성**을 선택합니다. **대체 액세스 매핑 컬렉션** 상자가 열립니다.
    1. 확장된 웹 응용 프로그램으로 디스플레이를 필터링하고 다음과 같은 것이 표시되는지 확인합니다.

        ![확장 응용 프로그램의 대체 액세스 매핑](./media/application-proxy-integrate-with-sharepoint-server/extend-webapp-aam.png)

### <a name="make-sure-the-sharepoint-web-application-is-running-under-a-domain-account"></a>SharePoint 웹 응용 프로그램이 도메인 계정에서 실행되고 있는지 확인합니다.

SharePoint 웹 응용 프로그램의 응용 프로그램 풀을 실행하는 계정을 식별하고 도메인 계정인지 확인하려면 다음 단계를 따르세요.

1. **SharePoint 중앙 관리** 사이트를 엽니다.
1. **보안**으로 이동하고 **서비스 계정 구성**을 선택합니다.
1. 웹 응용 프로그램 풀 선택 **- YourWebApplicationName**.

   ![서비스 계정 구성 옵션](./media/application-proxy-integrate-with-sharepoint-server/service-web-application.png)

1. 다음 단계에서 필요하므로 **이 구성 요소에 대한 계정 선택이** 도메인 계정을 반환하는지 확인하고 기억하십시오.

### <a name="make-sure-that-an-https-certificate-is-configured-for-the-iis-site-of-the-extranet-zone"></a>엑스트라넷 영역의 IIS 사이트에 대해 HTTPS 인증서가 구성되어 있는지 확인합니다.

내부 URL은 HTTPS 프로토콜`https://SharePoint/`()을 사용하므로 IIS(인터넷 정보 서비스) 사이트에서 인증서를 설정해야 합니다.

1. Windows PowerShell 콘솔을 엽니다.
1. 다음 스크립트를 실행하여 자체 서명된 인증서를 생성하고 컴퓨터의 MY 저장소에 추가합니다.

   ```powershell
   # Replace "SharePoint" with the actual hostname of the Internal URL of your Azure AD proxy application
   New-SelfSignedCertificate -DnsName "SharePoint" -CertStoreLocation "cert:\LocalMachine\My"
   ```

   > [!IMPORTANT]
   > 자체 서명된 인증서는 테스트 목적으로만 적합합니다. 프로덕션 환경에서는 인증 기관에서 발급한 인증서를 대신 사용하는 것이 좋습니다.

1. 인터넷 정보 서비스 관리자 콘솔을 엽니다.
1. 트리 보기에서 서버를 확장하고, **사이트를**확장하고, **SharePoint - AAD 프록시** 사이트를 선택하고, **바인딩을 선택합니다.**
1. **https 바인딩을** 선택한 다음 **편집을**선택합니다.
1. TLS/SSL 인증서 필드에서 **SharePoint** 인증서를 선택한 다음 **확인을**선택합니다.

이제 Azure AD 응용 프로그램 프록시를 통해 외부적으로 SharePoint 사이트에 액세스할 수 있습니다.

## <a name="step-3-configure-kerberos-constrained-delegation"></a>3단계: 커베로스 구속위임 구성

사용자는 처음에 Azure AD에서 인증한 다음 Azure AD 프록시 커넥터를 통해 Kerberos를 사용하여 SharePoint로 인증합니다. 커넥터가 Azure AD 사용자를 대신하여 Kerberos 토큰을 가져오도록 하려면 프로토콜 전환을 통해 Kerberos 제한 위임(KCD)을 구성해야 합니다. KCD에 대한 자세한 내용은 [Kerberos 제한된 위임 개요를](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/jj553400(v=ws.11))참조하십시오.

### <a name="set-the-spn-for-the-sharepoint-service-account"></a>공유점 서비스 계정에 대한 SPN 설정

이 문서에서는 내부 URL이 `https://sharepoint`은이며 서비스 주체 이름(SPN)은 . `HTTP/sharepoint` 이러한 값을 사용자 환경에 해당하는 값으로 바꿔야 합니다.
SharePoint 응용 `HTTP/sharepoint` 프로그램 풀 계정에 `Contoso\spapppool`SPN을 등록하려면 명령 프롬프트에서 다음 명령을 도메인 관리자로 실행합니다.

`setspn -S HTTP/sharepoint Contoso\spapppool`

`Setspn` 명령은 SPN을 추가하기 전에 검색합니다. SPN이 이미 있는 경우 **중복 SPN 값** 오류가 표시됩니다. 이 경우 올바른 응용 프로그램 풀 계정으로 설정되지 않은 경우 기존 SPN을 제거하는 것이 좋습니다. -L 옵션을 사용 하 여 `Setspn` 명령을 실행 하 여 SPN 성공적으로 추가 되었는지 확인할 수 있습니다. 이 명령에 대해 자세히 알아보려면 [Setspn](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/cc731241(v=ws.11))을 참조하세요.

### <a name="make-sure-the-connector-is-trusted-for-delegation-to-the-spn-that-was-added-to-the-sharepoint-application-pool-account"></a>SharePoint 응용 프로그램 풀 계정에 추가된 SPN에 대한 위임에 대해 커넥터를 신뢰할 수 있는지 확인합니다.

Azure AD 애플리케이션 프록시 서비스가 사용자 ID를 SharePoint 애플리케이션 풀 계정에 위임할 수 있도록 KCD를 구성합니다. 애플리케이션 프록시 커넥터에서 Azure AD에서 인증된 사용자에 대한 Kerberos 티켓을 검색할 수 있도록 하여 KCD를 구성합니다. 그런 다음 해당 서버는 컨텍스트를 대상 응용 프로그램(이 경우 SharePoint)에 전달합니다.

KCD를 구성하려면 각 커넥터 컴퓨터에 대해 다음 단계를 따르십시오.

1. 도메인 관리자로 도메인 컨트롤러에 로그인한 다음 Active Directory 사용자 및 컴퓨터를 엽니다.
1. Azure AD 프록시 커넥터를 실행하는 컴퓨터를 찾습니다. 이 예제에서는 SharePoint 서버 자체입니다.
1. 컴퓨터를 두 번 클릭한 다음 **위임** 탭을 선택합니다.
1. 위임 옵션이 **지정된 서비스에만 위임하기 위해 이 컴퓨터를 신뢰로**설정되어 있는지 확인합니다. 그런 다음 **모든 인증 프로토콜 사용**을 선택합니다.
1. **추가** 단추를 선택하고 **사용자 또는 컴퓨터를**선택하고 SharePoint 응용 프로그램 풀 계정을 찾습니다. 예: `Contoso\spapppool`
1. SPN 목록에서 서비스 계정에 대해 이전에 만든 SPN을 선택합니다.
1. **확인을** 선택한 다음 **확인을** 다시 선택하여 변경 내용을 저장합니다.
  
   ![위임 설정](./media/application-proxy-integrate-with-sharepoint-server/delegation-box2.png)

이제 외부 URL을 사용하여 SharePoint에 로그인하고 Azure로 인증할 준비가 되었습니다.

## <a name="troubleshoot-sign-in-errors"></a>로그인 오류 문제 해결

사이트에 로그인하지 않는 경우 커넥터 로그에서 문제에 대한 자세한 정보를 얻을 수 있습니다: 커넥터를 실행하는 컴퓨터에서 이벤트 뷰어를 열고 **응용 프로그램 및 서비스 로그로** > **이동하여 Microsoft** > **AadApplicationProxy** > **커넥터로**이동한 다음 **관리** 로그를 검사합니다.

## <a name="next-steps"></a>다음 단계

* [Azure AD 애플리케이션 프록시에서 사용자 지정 도메인 작업](application-proxy-configure-custom-domain.md)
* [Azure AD 응용 프로그램 프록시 커넥터 이해](application-proxy-connectors.md)
