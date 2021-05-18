---
title: '자습서: SharePoint 온-프레미스와 Azure Active Directory 통합 | Microsoft Docs'
description: Azure Active Directory와 SharePoint 온-프레미스 간에 페더레이션 인증을 구현하는 방법을 알아봅니다.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/31/2021
ms.author: jeedes
ms.openlocfilehash: ccb58f94045d69dc1cfcfed830fdd401881ecd3f
ms.sourcegitcommit: 02d443532c4d2e9e449025908a05fb9c84eba039
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/06/2021
ms.locfileid: "108746324"
---
# <a name="tutorial-implement-federated-authentication-between-azure-active-directory-and-sharepoint-on-premises"></a>자습서: Azure Active Directory와 SharePoint 온-프레미스 간에 페더레이션 인증 구현

## <a name="scenario-description"></a>시나리오 설명

이 자습서에서는 Azure Active Directory와 SharePoint 온-프레미스 간에 페더레이션 인증을 구성합니다. 목표는 사용자가 Azure Active Directory에 로그인하여 해당 ID를 사용하여 SharePoint 온-프레미스 사이트에 액세스할 수 있도록 하는 것입니다.

## <a name="prerequisites"></a>사전 요구 사항

구성을 수행하려면 다음 리소스가 필요합니다.
* Azure Active Directory 테넌트. 계정이 없는 경우 [무료 계정](https://azure.microsoft.com/free/)에 만들 수 있습니다.
* SharePoint 2013 팜 이상

이 문서에서 사용하는 값은 다음과 같습니다.
- 엔터프라이즈 애플리케이션 이름(Azure AD): `SharePoint corporate farm`
- 신뢰 식별자(Azure AD)/보안영역(SharePoint): `urn:sharepoint:federation`
- loginUrl(Azure AD): `https://login.microsoftonline.com/dc38a67a-f981-4e24-ba16-4443ada44484/wsfed`
- SharePoint 사이트 URL: `https://spsites.contoso.local/`
- SharePoint 사이트 회신 URL: `https://spsites.contoso.local/_trust/`
- SharePoint 신뢰 구성 이름: `AzureADTrust`
- Azure AD 테스트 사용자의 UserPrincipalName: `AzureUser1@demo1984.onmicrosoft.com`

## <a name="configure-an-enterprise-application-in-azure-active-directory"></a>Azure Active Directory에서 엔터프라이즈 애플리케이션 구성

Azure AD에서 페더레이션을 구성하려면 전용 엔터프라이즈 애플리케이션을 만들어야 합니다. 애플리케이션 갤러리에서 찾을 수 있는 미리 구성된 `SharePoint on-premises` 템플릿을 사용하면 구성이 간단해집니다.

### <a name="create-the-enterprise-application"></a>엔터프라이즈 애플리케이션 만들기

1. [Azure Active Directory 포털](https://aad.portal.azure.com/)에 로그인합니다.
1. **엔터프라이즈 애플리케이션** 으로 이동한 다음, **모든 애플리케이션** 을 선택합니다.
1. 새 애플리케이션을 추가하려면 대화 상자 맨 위 있는 **새 애플리케이션** 을 선택합니다.
1. 검색 상자에서 **SharePoint 온-프레미스** 를 입력합니다. 결과 창에서 **SharePoint 온-프레미스** 를 선택합니다.
1. 애플리케이션의 이름(이 자습서에서는 `SharePoint corporate farm`)을 지정하고 **만들기** 를 클릭하여 애플리케이션을 추가합니다.
1. 새 엔터프라이즈 애플리케이션에서 **속성** 을 선택하고, **사용자 할당 필요** 값을 확인합니다. 이 시나리오의 경우 해당 값을 **아니요** 로 설정하고 **저장** 을 클릭합니다.

### <a name="configure-the-enterprise-application"></a>엔터프라이즈 애플리케이션 구성

이 섹션에서는 SAML 인증을 구성하고 성공적으로 인증되면 SharePoint로 전송될 클레임을 정의합니다.

1. 엔터프라이즈 애플리케이션 `SharePoint corporate farm`의 개요에서 **2. Single Sign-On 설정** 을 선택하고 다음 대화 상자에서 **SAML** 을 선택합니다.
 
1. **SAML로 Single Sign-On 설정** 페이지의 **기본 SAML 구성** 창에서 **편집** 아이콘을 선택합니다.

1. **기본 SAML 구성** 섹션에서 다음 단계를 수행합니다.

    1. **식별자** 상자에서 `urn:sharepoint:federation` 값이 있는지 확인합니다.

    1. **회신 URL** 상자에서 `https://spsites.contoso.local/_trust/` 패턴을 사용하여 URL을 입력합니다.

    1. **로그온 URL** 상자에서 `https://spsites.contoso.local/` 패턴을 사용하여 URL을 입력합니다.
    
    1. **저장** 을 선택합니다.

1. **사용자 특성 및 클레임** 섹션에서 SharePoint에서 권한을 부여하는 데 사용되지 않기 때문에 쓸모가 없는 다음 클레임 유형을 삭제합니다.
    - `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`
    - `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`
    - `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname`

1. 해당 설정은 다음과 같습니다.

    ![기본 SAML 설정](./media/sharepoint-on-premises-tutorial/azure-active-directory-app-saml-ids.png)

1. 나중에 SharePoint에서 필요할 정보를 복사합니다.

    - **SAML 서명 인증서** 섹션에서 **인증서(Base64)** 를 **다운로드** 합니다. 이는 Azure AD에서 SAML 토큰에 서명하는 데 사용하는 서명 인증서의 퍼블릭 키입니다. SharePoint는 들어오는 SAML 토큰의 무결성을 확인하기 위해 이 퍼블릭 키가 필요합니다.

    - **SharePoint 회사 팜 설정** 섹션에서 **로그인 URL** 을 메모장에 복사하고 후행 문자열 **/saml2** 를 **/wsfed** 로 바꿉니다.
     
    > [!IMPORTANT]
    > Azure AD가 SharePoint에 필요한 SAML 1.1 토큰을 발급하도록 하려면 **/saml2** 를 **/wsfed** 로 바꿔야 합니다.

    - **SharePoint 회사 팜 설정** 섹션에서 **로그아웃 URL** 을 복사합니다.

## <a name="configure-sharepoint-to-trust-azure-active-directory"></a>Azure Active Directory를 신뢰하도록 SharePoint 구성

### <a name="create-the-trust-in-sharepoint"></a>SharePoint에서 신뢰 만들기

이 단계에서는 SharePoint가 Azure AD를 신뢰하는 데 필요한 구성을 저장할 SPTrustedLoginProvider를 만듭니다. 이를 위해 위에서 복사한 Azure AD의 정보가 필요합니다. SharePoint 관리 셸을 시작하고 다음 스크립트를 실행하여 해당 항목을 만듭니다.

```powershell
# Path to the public key of the Azure AD SAML signing certificate (self-signed), downloaded from the Enterprise application in the Azure AD portal
$signingCert = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2("C:\AAD app\SharePoint corporate farm.cer")
# Unique realm (corresponds to the "Identifier (Entity ID)" in the Azure AD Enterprise application)
$realm = "urn:sharepoint:federation"
# Login URL copied from the Azure AD enterprise application. Make sure to replace "saml2" with "wsfed" at the end of the URL:
$loginUrl = "https://login.microsoftonline.com/dc38a67a-f981-4e24-ba16-4443ada44484/wsfed"

# Define the claim types used for the authorization
$userIdentifier = New-SPClaimTypeMapping -IncomingClaimType "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name" -IncomingClaimTypeDisplayName "name" -LocalClaimType "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn"
$role = New-SPClaimTypeMapping "http://schemas.microsoft.com/ws/2008/06/identity/claims/role" -IncomingClaimTypeDisplayName "Role" -SameAsIncoming

# Let SharePoint trust the Azure AD signing certificate
New-SPTrustedRootAuthority -Name "Azure AD signing certificate" -Certificate $signingCert

# Create a new SPTrustedIdentityTokenIssuer in SharePoint
$trust = New-SPTrustedIdentityTokenIssuer -Name "AzureADTrust" -Description "Azure AD" -Realm $realm -ImportTrustCertificate $signingCert -ClaimsMappings $userIdentifier, $role -SignInUrl $loginUrl -IdentifierClaim $userIdentifier.InputClaimType
```

### <a name="configure-the-sharepoint-web-application"></a>SharePoint 웹 애플리케이션 구성

이 단계에서는 위에서 만든 Azure AD 엔터프라이즈 애플리케이션을 신뢰하도록 SharePoint의 웹 애플리케이션을 구성합니다. 다음과 같은 중요한 규칙을 고려해야 합니다.
- SharePoint 웹 애플리케이션의 기본 영역에서는 Windows 인증이 사용되어야 합니다. 이는 검색 크롤러에 필요합니다.
- Azure AD 인증을 사용할 SharePoint URL은 HTTPS를 사용하여 설정해야 합니다.

1. 웹 애플리케이션을 만들거나 확장합니다. 이 문서에서는 두 가지 가능한 구성을 설명합니다.

    - 기본 영역에서 Windows 및 Azure AD 인증을 둘 다 사용하는 새 웹 애플리케이션을 만드는 경우:

        1. **SharePoint 관리 셸** 을 시작하고 다음 스크립트를 실행합니다.
            ```powershell
            # This script creates a new web application and sets Windows and Azure AD authentication on the Default zone
            # URL of the SharePoint site federated with Azure AD
            $trustedSharePointSiteUrl = "https://spsites.contoso.local/"
            $applicationPoolManagedAccount = "Contoso\spapppool"

            $winAp = New-SPAuthenticationProvider -UseWindowsIntegratedAuthentication -DisableKerberos:$true
            $sptrust = Get-SPTrustedIdentityTokenIssuer "AzureADTrust"
            $trustedAp = New-SPAuthenticationProvider -TrustedIdentityTokenIssuer $sptrust    
            
            New-SPWebApplication -Name "SharePoint - Azure AD" -Port 443 -SecureSocketsLayer -URL $trustedSharePointSiteUrl -ApplicationPool "SharePoint - Azure AD" -ApplicationPoolAccount (Get-SPManagedAccount $applicationPoolManagedAccount) -AuthenticationProvider $winAp, $trustedAp
            ```
        1. **SharePoint 중앙 관리** 사이트를 엽니다.
        1. **시스템 설정** 아래에서 **대체 액세스 매핑 구성** 을 선택합니다. **대체 액세스 매핑 컬렉션** 상자가 열립니다.
        1. 새 웹 애플리케이션으로 디스플레이를 필터링하고 다음과 같이 표시되는지 확인합니다.
    
           ![웹 애플리케이션의 대체 액세스 매핑](./media/sharepoint-on-premises-tutorial/sp-alternate-access-mappings-new-web-app.png)

    - 새 영역에서 Azure AD 인증을 사용하도록 기존 웹 애플리케이션을 확장하는 경우:

        1. SharePoint 관리 셸을 시작하고 다음 스크립트를 실행합니다.

            ```powershell
            # This script extends an existing web application to set Azure AD authentication on a new zone
            # URL of the default zone of the web application
            $webAppDefaultZoneUrl = "http://spsites/"
            # URL of the SharePoint site federated with ADFS
            $trustedSharePointSiteUrl = "https://spsites.contoso.local/"
            $sptrust = Get-SPTrustedIdentityTokenIssuer "AzureADTrust"
            $ap = New-SPAuthenticationProvider -TrustedIdentityTokenIssuer $sptrust
            $wa = Get-SPWebApplication $webAppDefaultZoneUrl
            
            New-SPWebApplicationExtension -Name "SharePoint - Azure AD" -Identity $wa -SecureSocketsLayer -Zone Internet -Url $trustedSharePointSiteUrl -AuthenticationProvider $ap
            ```
        
        1. **SharePoint 중앙 관리** 사이트를 엽니다.
        1. **시스템 설정** 아래에서 **대체 액세스 매핑 구성** 을 선택합니다. **대체 액세스 매핑 컬렉션** 상자가 열립니다.
        1. 확장된 웹 애플리케이션을 사용하여 디스플레이를 필터링하고 다음과 같이 표시되는지 확인합니다.
    
            ![확장된 웹 애플리케이션의 대체 액세스 매핑](./media/sharepoint-on-premises-tutorial/sp-alternate-access-mappings-extended-zone.png)

웹 애플리케이션이 만들어진 후 루트 사이트 모음을 만들고 기본 사이트 모음 관리자로 Windows 계정을 추가할 수 있습니다.

1. SharePoint 사이트의 인증서 만들기

    SharePoint URL은 HTTPS 프로토콜(`https://spsites.contoso.local/`)을 사용하기 때문에 인증서는 해당 IIS(인터넷 정보 서비스) 사이트에서 설정해야 합니다. 해당 단계를 수행하여 자체 서명된 인증서를 생성합니다.
    
    > [!IMPORTANT]
    > 자체 서명된 인증서는 테스트 목적으로만 적합합니다. 프로덕션 환경에서는 인증 기관에서 발급한 인증서를 대신 사용하는 것이 좋습니다.
    
    1. Windows PowerShell 콘솔을 엽니다.
    1. 다음 스크립트를 실행하여 자체 서명된 인증서를 생성하고 컴퓨터의 내 저장소에 추가합니다.
    
        ```powershell
        New-SelfSignedCertificate -DnsName "spsites.contoso.local" -CertStoreLocation "cert:\LocalMachine\My"
        ```
    
1. IIS 사이트에서 인증서 설정
    1. 인터넷 정보 서비스 관리자 콘솔을 엽니다.
    1. 트리 뷰에서 서버를 확장하고, **사이트** 를 확장하고, **SharePoint - Azure AD** 사이트를 선택하고, **바인딩** 을 선택합니다.
    1. **HTTPS 바인딩** 을 선택한 다음, **편집** 을 선택합니다.
    1. TLS/SSL 인증서 필드에서 사용할 인증서(예: 위에서 만든 **spsites.contoso.local**)를 선택하고 **확인** 을 선택합니다.
    
    > [!NOTE]
    > 웹 프런트 엔드 서버가 여러 개인 경우 각각에 대해 이 작업을 반복해야 합니다.

이제 SharePoint와 Azure AD 간 신뢰의 기본 구성이 완료되었습니다. Azure Active Directory 사용자로 SharePoint 사이트에 로그인하는 방법을 살펴보겠습니다.

## <a name="sign-in-as-a-member-user"></a>멤버 사용자로 로그인

Azure Active Directory에는 게스트 사용자 및 멤버 사용자라는 [두 가지 유형의 사용자](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-user-properties)가 있습니다. 조직에 속하는 사용자인 멤버 사용자로 시작하겠습니다.

### <a name="create-a-member-user-in-azure-active-directory"></a>Azure Active Directory에서 멤버 사용자 만들기

1. Azure Portal의 맨 왼쪽 창에서 **Azure Active Directory** 를 선택합니다. **관리** 창에서 **사용자** 를 선택합니다.

1. 화면의 위쪽에서 **모든 사용자** > **새 사용자** 를 차례로 선택합니다.

1. **사용자 만들기** 를 선택하고, 사용자 속성에서 다음 단계를 수행합니다.

    1. **이름** 상자에서 사용자 이름을 입력합니다. 여기서는 **TestUser** 를 사용했습니다.
  
    1. **사용자 이름** 상자에 `AzureUser1@<yourcompanytenant>.onmicrosoft.com`을 입력합니다. 이 예제에서는 `AzureUser1@demo1984.onmicrosoft.com`을 보여 줍니다.

       ![사용자 대화 상자](./media/sharepoint-on-premises-tutorial/azure-active-directory-new-user.png)

    1. **암호 표시** 확인란을 선택한 다음, **암호** 상자에 표시되는 값을 적어 둡니다.

    1. **만들기** 를 선택합니다.

    1. 이제 사이트를 `AzureUser1@demo1984.onmicrosoft.com`과 공유하고, 이 사용자가 액세스하도록 허용할 수 있습니다.

### <a name="grant-permissions-to-the-azure-active-directory-user-in-sharepoint"></a>SharePoint의 Azure Active Directory 사용자에게 권한 부여

SharePoint 루트 사이트 모음에 Windows 계정(사이트 모음 관리자)으로 로그인하고 **공유** 를 클릭합니다.  
대화 상자에서 userprincipalname의 정확한 값(예: `AzureUser1@demo1984.onmicrosoft.com`)을 입력하고 **이름** 클레임 결과를 선택해야 합니다(해당 클레임 유형을 보려면 결과에서 마우스 이동).

> [!IMPORTANT]
> 초대하려는 사용자의 정확한 값을 입력하고 목록에서 적절한 클레임 유형을 선택해야 합니다. 그러지 않으면 공유가 작동하지 않습니다.

![AzureCP를 사용하지 않는 인물 선택기 결과](./media/sharepoint-on-premises-tutorial/sp-people-picker-search-no-azurecp.png)

이 제한 사항은 SharePoint가 인물 선택기에서 입력의 유효성을 검사하지 않아 혼동이 되고 맞춤법 오류가 발생하거나 사용자가 실수로 잘못된 클레임 유형을 선택할 수 있기 때문입니다.  
이 시나리오를 수정하기 위해 [AzureCP](https://yvand.github.io/AzureCP/)라는 오픈 소스 솔루션을 사용하여 SharePoint 2019/2016/2013을 Azure Active Directory와 연결하고 Azure Active Directory 테넌트에 대해 입력을 확인할 수 있습니다. 자세한 내용은 [AzureCP](https://yvand.github.io/AzureCP/)를 참조하세요.

다음은 AzureCP가 구성된 동일한 검색입니다. SharePoint는 입력에 따라 실제 사용자를 반환합니다.

![AzureCP를 사용하는 인물 선택기 결과](./media/sharepoint-on-premises-tutorial/sp-people-picker-search-with-azurecp.png)

> [!IMPORTANT]
> AzureCP는 Microsoft 제품이 아니며 Microsoft 지원에서 지원되지 않습니다. 온-프레미스 SharePoint 팜에서 AzureCP를 다운로드, 설치 및 구성하려면 [AzureCP](https://yvand.github.io/AzureCP/) 웹 사이트를 참조하세요. 

이제 Azure Active Directory 사용자 `AzureUser1@demo1984.onmicrosoft.com`은 해당 ID를 사용하여 SharePoint 사이트 `https://spsites.contoso.local/`에 로그인할 수 있습니다.

## <a name="grant-permissions-to-a-security-group"></a>보안 그룹에 권한 부여

### <a name="add-the-group-claim-type-to-the-enterprise-application"></a>엔터프라이즈 애플리케이션에 그룹 클레임 유형 추가

1. 엔터프라이즈 애플리케이션 `SharePoint corporate farm`의 개요에서 **2. Single Sign-On 설정** 을 선택합니다. 

1. **사용자 특성 및 클레임** 섹션에서 그룹 클레임이 없는 경우 다음 단계를 수행합니다.

    1. **그룹 클레임 추가** 를 선택하고, **보안 그룹** 을 선택하고, **원본 특성** 이 **그룹 ID** 로 설정되어 있는지 확인합니다.
    1. **그룹 클레임 이름 사용자 지정** 을 선택한 다음, **그룹을 역할 클레임으로 내보내기** 를 선택하고, **저장** 을 클릭합니다.
    1. **사용자 특성 및 클레임** 섹션은 다음과 같이 표시됩니다.

    ![사용자 및 그룹의 클레임](./media/sharepoint-on-premises-tutorial/azure-active-directory-claims-with-group.png)

### <a name="create-a-security-group-in-azure-active-directory"></a>Azure Active Directory에서 보안 그룹 생성

Azure Active Directory에서 보안 그룹을 만들겠습니다.

1. **Azure Active Directory** > **그룹** 을 선택합니다.

1. **새 그룹** 을 선택합니다.

1. **그룹 유형**(보안), **그룹 이름**(예: `AzureGroup1`) 및 **멤버 자격 유형** 을 입력합니다. 위에서 만든 사용자를 멤버로 추가하고 **만들기** 를 클릭하여 선택합니다.

    ![Azure AD 보안 그룹 만들기](./media/sharepoint-on-premises-tutorial/azure-active-directory-new-group.png)
  
### <a name="grant-permissions-to-the-security-group-in-sharepoint"></a>SharePoint에서 보안 그룹에 권한 부여

Azure AD 보안 그룹은 GUID인 특성 `Id`(예: `E89EF0A3-46CC-45BF-93A4-E078FCEBFC45`)로 식별됩니다.  
사용자 지정 클레임 공급자가 없으면 사용자는 인물 선택기에서 그룹의 정확한 값(`Id`)을 입력하고 해당 클레임 유형을 선택해야 합니다. 이는 사용자에게 친숙하지도 않고 신뢰할 수도 없습니다.  
이를 방지하기 위해 이 문서에서는 타사 클레임 공급자 [AzureCP](https://yvand.github.io/AzureCP/)를 사용하여 SharePoint에서 친숙한 방식으로 그룹을 찾습니다.

![인물 선택기 검색 Azure AD 그룹](./media/sharepoint-on-premises-tutorial/sp-people-picker-search-azure-active-directory-group.png)

## <a name="manage-guest-users-access"></a>게스트 사용자 액세스 관리

게스트 계정에는 다음과 같은 두 유형이 있습니다.

- B2B 게스트 계정: 해당 사용자는 외부 Azure Active Directory 테넌트에 속합니다.
- MSA 게스트 계정: 해당 사용자는 Microsoft ID 공급자(Hotmail, Outlook) 또는 소셜 계정 공급자(Google 또는 유사한 공급자)에 속합니다.

기본적으로 Azure Active Directory는 “고유한 사용자 ID” 및 클레임 “이름”을 둘 다 `user.userprincipalname` 특성으로 설정합니다.  
아쉽게도 다음 표에 나와 있는 것처럼 게스트 계정의 경우 이 특성이 모호합니다.

| Azure AD에서 설정되는 원본 특성 | B2B 게스트에 대해 Azure AD에서 사용되는 실제 속성 | MSA 게스트에 대해 Azure AD에서 사용되는 실제 속성 | SharePoint가 ID의 유효성을 검사하는 데 사용할 수 있는 속성 |
|--|--|--|--|
| `user.userprincipalname` | `mail`, 예: `guest@PARTNERTENANT` | `userprincipalname`, 예: `guest_outlook.com#EXT#@TENANT.onmicrosoft.com` | 모호함 |
| `user.localuserprincipalname` | `userprincipalname`, 예: `guest_PARTNERTENANT#EXT#@TENANT.onmicrosoft.com` | `userprincipalname`, 예: `guest_outlook.com#EXT#@TENANT.onmicrosoft.com` | `userprincipalname` |

결론적으로 게스트 계정이 모두 동일한 특성을 사용하여 식별되는지 확인하려면 엔터프라이즈 애플리케이션의 식별자 클레임은 `user.userprincipalname` 대신 `user.localuserprincipalname`을 사용하도록 업데이트되어야 합니다.

### <a name="update-the-application-to-use-a-consistent-attribute-for-all-guest-users"></a>모든 게스트 사용자에 대해 일관된 특성을 사용하도록 애플리케이션 업데이트

1. 엔터프라이즈 애플리케이션 `SharePoint corporate farm`의 개요에서 **2. Single Sign-On 설정** 을 선택합니다.
 
1. **SAML로 Single Sign-On 설정** 페이지의 **사용자 특성 및 클레임** 창에서 **편집** 아이콘을 선택합니다.

1. **사용자 특성 및 클레임** 섹션에서 다음 단계를 수행합니다.

    1. **고유한 사용자 ID(이름 ID)** 를 선택하고, **원본 특성** 속성을 **user.localuserprincipalname** 으로 변경하고, **저장** 을 클릭합니다.
    
    1. **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name** 을 선택하고, **원본 특성** 속성을 **user.localuserprincipalname** 으로 변경하고, **저장** 을 클릭합니다.
    
    1. **사용자 특성 및 클레임** 섹션은 다음과 같이 표시됩니다.
    
    ![게스트의 사용자 특성 및 클레임](./media/sharepoint-on-premises-tutorial/azure-active-directory-claims-guests.png)

### <a name="invite-guest-users-in-sharepoint"></a>SharePoint에서 게스트 사용자 초대

> [!NOTE]
> 이 섹션에서는 클레임 공급자 AzureCP가 사용된다고 가정합니다.

위 섹션에서 모든 게스트 계정에 대해 일관된 특성을 사용하도록 엔터프라이즈 애플리케이션을 업데이트했습니다.  
이제 해당 변경을 반영하고 게스트 계정에 대해 `userprincipalname` 특성을 사용하도록 AzureCP의 구성을 업데이트해야 합니다.

1. **SharePoint 중앙 관리** 사이트를 엽니다.
1. **보안** 에서 **AzureCP 전역 구성** 을 선택합니다.
1. **사용자 ID 속성** 섹션에서: **‘게스트’ 사용자의 사용자 ID** 를 **UserPrincipalName** 으로 설정합니다.
1. 확인을 클릭합니다.

![AzureCP 게스트 계정 구성](./media/sharepoint-on-premises-tutorial/sp-azurecp-attribute-for-guests.png)

이제 SharePoint 사이트에서 게스트 사용자를 초대할 수 있습니다.

## <a name="configure-the-federation-for-multiple-web-applications"></a>여러 웹 애플리케이션에 대해 페더레이션 구성

이 구성은 단일 웹 애플리케이션에서 작동하지만 신뢰할 수 있는 동일한 ID 공급자를 여러 웹 애플리케이션에 사용하려는 경우 추가 구성이 필요합니다. 예를 들어, 별도의 웹 애플리케이션 `https://otherwebapp.contoso.local/`이 있고 이제 여기에서 Azure Active Directory 인증을 사용하도록 설정하려는 경우를 가정합니다. 이렇게 하려면 SAML WReply 매개 변수를 전달하도록 SharePoint를 구성하고 엔터프라이즈 애플리케이션에 URL을 추가합니다.

### <a name="configure-sharepoint-to-pass-the-saml-wreply-parameter"></a>SAML WReply 매개 변수를 전달하도록 SharePoint 구성

1. SharePoint 서버에서 SharePoint 201x 관리 셸을 열고 다음 명령을 실행합니다. 이전에 사용한 것과 동일한 신뢰할 수 있는 ID 토큰 발급자 이름을 사용합니다.

```powershell
$t = Get-SPTrustedIdentityTokenIssuer "AzureADTrust"
$t.UseWReplyParameter = $true
$t.Update()
```

### <a name="add-the-urls-in-the-enterprise-application"></a>엔터프라이즈 애플리케이션에서 URL 추가

1. Azure Portal에서 **Azure Active Directory** > **엔터프라이즈 애플리케이션** 을 차례로 선택합니다. 이전에 만든 엔터프라이즈 애플리케이션 이름을 선택하고, **Single Sign-On** 을 선택합니다.

1. **SAML로 Single Sign-On 설정** 페이지에서 **기본 SAML 구성** 을 편집합니다.

1. **회신 URL(Assertion Consumer Service URL)** 섹션에서 Azure Active Directory를 사용하여 사용자를 로그인시켜야 하는 모든 추가 웹 애플리케이션의 URL(예: `https://otherwebapp.contoso.local/`)을 추가하고 **저장** 을 클릭합니다.

![추가 웹 애플리케이션 지정](./media/sharepoint-on-premises-tutorial/azure-active-directory-app-reply-urls.png)
