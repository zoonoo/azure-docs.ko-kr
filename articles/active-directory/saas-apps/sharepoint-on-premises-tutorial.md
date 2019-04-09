---
title: '자습서: SharePoint 온-프레미스와 Azure Active Directory 통합 | Microsoft Docs'
description: Azure Active Directory와 SharePoint 온-프레미스 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 85b8d4d0-3f6a-4913-b9d3-8cc327d8280d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/21/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8ba9f4df36f753a1caf619ad90015fa073a00de3
ms.sourcegitcommit: a60a55278f645f5d6cda95bcf9895441ade04629
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/03/2019
ms.locfileid: "58883380"
---
# <a name="tutorial-azure-active-directory-integration-with-sharepoint-on-premises"></a>자습서: SharePoint 온-프레미스와 Azure Active Directory 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 SharePoint 온-프레미스를 통합하는 방법에 대해 알아봅니다.
SharePoint 온-프레미스를 Azure AD와 통합하면 다음과 같은 장점이 있습니다.

* SharePoint 온-프레미스에 대한 액세스 권한이 있는 사용자를 Azure AD에서 제어할 수 있습니다.
* 사용자가 해당 Azure AD 계정으로 SharePoint 온-프레미스에 자동으로 로그온(Single Sign-On)되도록 설정할 수 있습니다.
* 단일 중앙 위치인 Azure Portal에서 계정을 관리할 수 있습니다.

Azure AD와의 SaaS 앱 통합에 대한 자세한 내용은 [Azure Active Directory를 사용한 응용 프로그램 액세스 및 Single Sign-On](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)을 참조하세요.
Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/) 계정을 만듭니다.

## <a name="prerequisites"></a>필수 조건

SharePoint 온-프레미스와 Azure AD 통합을 구성하려면 다음 항목이 필요합니다.

* Azure AD 구독 Azure AD 환경이 없으면 [여기](https://azure.microsoft.com/pricing/free-trial/)에서 1개월 평가판을 구할 수 있습니다.
* SharePoint 온-프레미스 Single Sign-On을 사용하도록 설정된 구독

## <a name="scenario-description"></a>시나리오 설명

이 자습서에서는 테스트 환경에서 Azure AD Single Sign-On을 구성하고 테스트합니다.

* SharePoint 온-프레미스에서 **SP** 시작 SSO를 지원합니다.

## <a name="adding-sharepoint-on-premises-from-the-gallery"></a>갤러리에서 SharePoint 온-프레미스 추가

SharePoint 온-프레미스의 Azure AD 통합을 구성하려면 갤러리의 SharePoint 온-프레미스를 관리되는 SaaS 앱 목록에 추가해야 합니다.

**갤러리의 SharePoint 온-프레미스를 추가하려면 다음 단계를 수행합니다.**

1. **[Azure Portal](https://portal.azure.com)** 의 왼쪽 탐색 창에서 **Azure Active Directory** 아이콘을 클릭합니다.

    ![Azure Active Directory 단추](common/select-azuread.png)

2. **엔터프라이즈 응용 프로그램**으로 이동한 다음, **모든 응용 프로그램** 옵션을 선택합니다.

    ![엔터프라이즈 애플리케이션 블레이드](common/enterprise-applications.png)

3. 새 애플리케이션을 추가하려면 대화 상자 맨 위 있는 **새 애플리케이션** 단추를 클릭합니다.

    ![새 애플리케이션 단추](common/add-new-app.png)

4. 검색 상자에 **SharePoint 온-프레미스**를 입력하고 결과 패널에서 **SharePoint 온-프레미스**를 선택한 다음, **추가** 단추를 클릭하여 애플리케이션을 추가합니다.

    ![결과 목록의 SharePoint 온-프레미스](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성 및 테스트

이 섹션에서는 **Britta Simon**이라는 테스트 사용자를 기반으로 하여 SharePoint 온-프레미스에서 Azure AD Single Sign-On을 구성하고 테스트합니다.
Single Sign-On이 작동하려면 Azure AD 사용자와 SharePoint 온-프레미스의 관련 사용자 간에 연결 관계를 설정해야 합니다.

SharePoint 온-프레미스에서 Azure AD Single Sign-On을 구성하고 테스트하려면 다음 구성 요소를 완료해야 합니다.

1. **[Azure AD Single Sign-On 구성](#configure-azure-ad-single-sign-on)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
2. **[SharePoint 온-프레미스 Single Sign-On 구성](#configure-sharepoint-on-premises-single-sign-on)** - 애플리케이션 쪽에서 Single Sign-On 설정을 구성합니다.
3. **[Azure AD 테스트 사용자 만들기](#create-an-azure-ad-test-user)** - Britta Simon으로 Azure AD Single Sign-On을 테스트하는 데 사용합니다.
4. **[Azure Portal에서 Azure AD 보안 그룹 만들기](#create-an-azure-ad-security-group-in-the-azure-portal)** - Azure AD의 새 보안 그룹이 Single Sign-On을 사용하도록 설정합니다.
5. **[SharePoint 온-프레미스 보안 그룹에 대한 액세스 권한 부여](#grant-access-to-sharepoint-on-premises-security-group)** - Azure AD에 특정 그룹에 대한 액세스 권한을 부여합니다.
6. **[Azure Portal에서 Azure AD 보안 그룹 할당](#assign-the-azure-ad-security-group-in-the-azure-portal)** - 인증을 위해 Azure AD에 특정 그룹을 할당합니다.
7. **[Single Sign-On 테스트](#test-single-sign-on)** - 구성이 작동하는지 여부를 확인합니다.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성

이 섹션에서는 Azure Portal에서 Azure AD Single Sign-On을 사용하도록 설정합니다.

SharePoint 온-프레미스에서 Azure AD Single Sign-On을 구성하려면 다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com/)의 **SharePoint 온-프레미스** 애플리케이션 통합 페이지에서 **Single Sign-On**을 선택합니다.

    ![Single Sign-On 구성 링크](common/select-sso.png)

2. **Single Sign-On 방법 선택** 대화 상자에서 **SAML/WS-Fed** 모드를 선택하여 Single Sign-On을 사용하도록 설정합니다.

    ![Single Sign-On 선택 모드](common/select-saml-option.png)

3. **SAML로 Single Sign-On 설정** 페이지에서 **편집** 아이콘을 클릭하여 **기본 SAML 구성** 대화 상자를 엽니다.

    ![기본 SAML 구성 편집](common/edit-urls.png)

4. **기본 SAML 구성** 섹션에서 다음 단계를 수행합니다.

    ![SharePoint 온-프레미스 도메인 및 URL Single Sign-On 정보](common/sp-identifier-reply.png)

    a. **로그온 URL** 텍스트 상자에 다음 패턴을 사용하여 URL을 입력합니다. `https://<YourSharePointServerURL>/_trust/default.aspx`

    b. **식별자** 텍스트 상자에서 다음 패턴을 사용하는 URL을 입력합니다. `urn:sharepoint:federation`

    다. **회신 URL** 텍스트 상자에 다음 패턴을 사용하여 URL을 입력합니다. `https://<YourSharePointServerURL>/_trust/default.aspx`

    > [!NOTE]
    > 이러한 값은 실제 값이 아닙니다. 실제 로그온 URL, 식별자 및 회신 URL로 값을 업데이트합니다. 이러한 값을 얻으려면 [SharePoint 온-프레미스 클라이언트 지원 팀](https://support.office.com/)에 문의하세요. Azure Portal의 **기본 SAML 구성** 섹션에 표시된 패턴을 참조할 수도 있습니다.

5. **SAML로 Single Sign-On 설정** 페이지의 **SAML 서명 인증서** 섹션에서 **다운로드**를 클릭하여 요구 사항에 따라 제공된 옵션에서 **인증서(Base64)** 를 다운로드한 다음, 컴퓨터에 저장합니다.

    ![인증서 다운로드 링크](common/certificatebase64.png)

    > [!Note]
    > 구성을 위해 PowerShell 스크립트에서 나중에 사용해야 하므로 인증서 파일을 다운로드한 파일 경로를 적어 두세요.

6. **SharePoint 온-프레미스 설정** 섹션에서 요구 사항에 따라 적절한 URL을 복사합니다. **Single Sign-On 서비스 URL**의 경우 다음 패턴의 값을 사용합니다. `https://login.microsoftonline.com/_my_directory_id_/wsfed`

    > [!Note]
    > _my_directory_id_는 Azure Ad 구독의 테넌트 ID입니다.

    ![구성 URL 복사](common/copy-configuration-urls.png)

    a. 로그인 URL

    b. Azure AD 식별자

    다. 로그아웃 URL

    > [!NOTE]
    > Sharepoint On-Premises 애플리케이션은 SAML 1.1 토큰을 사용하므로, Azure AD는 SharePoint 서버로부터 WS Fed 요청을 기대하고 인증 후 SAML 1.1 토큰을 발급합니다.

### <a name="configure-sharepoint-on-premises-single-sign-on"></a>SharePoint 온-프레미스 Single Sign-On 구성

1. 다른 웹 브라우저 창에서 SharePoint 온-프레미스 회사 사이트에 관리자 권한으로 로그인합니다.

2. **SharePoint Server 2016에서 신뢰할 수 있는 새 ID 공급자 구성**

    SharePoint Server 2016 서버에 로그인하고 SharePoint 2016 관리 셸을 엽니다. Azure Portal에서 $realm(Azure Portal의 온-프레미스 도메인 및 URL 섹션에서 SharePoint의 식별자 값), $wsfedurl(Single Sign-On 서비스 URL) 및 $filepath(인증서 파일을 다운로드한 파일 경로)의 값을 채우고 다음 명령을 실행하여 새 신뢰할 수 있는 ID 공급자를 구성합니다.

    > [!TIP]
    > PowerShell을 처음 사용하거나 PowerShell 작동 방법에 대해 자세히 알아보려면 [SharePoint PowerShell](https://docs.microsoft.com/powershell/sharepoint/overview?view=sharepoint-ps)을 참조하세요.

    ```powershell
    $realm = "<Identifier value from the SharePoint on-premises Domain and URLs section in the Azure portal>"
    $wsfedurl="<SAML single sign-on service URL value which you have copied from the Azure portal>"
    $filepath="<Full path to SAML signing certificate file which you have downloaded from the Azure portal>"
    $cert = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2($filepath)
    New-SPTrustedRootAuthority -Name "AzureAD" -Certificate $cert
    $map = New-SPClaimTypeMapping -IncomingClaimType "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name" -IncomingClaimTypeDisplayName "name" -LocalClaimType "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn"
    $map2 = New-SPClaimTypeMapping -IncomingClaimType "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname" -IncomingClaimTypeDisplayName "GivenName" -SameAsIncoming
    $map3 = New-SPClaimTypeMapping -IncomingClaimType "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname" -IncomingClaimTypeDisplayName "SurName" -SameAsIncoming
    $map4 = New-SPClaimTypeMapping -IncomingClaimType "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress" -IncomingClaimTypeDisplayName "Email" -SameAsIncoming
    $map5 = New-SPClaimTypeMapping -IncomingClaimType "http://schemas.microsoft.com/ws/2008/06/identity/claims/role" -IncomingClaimTypeDisplayName "Role" -SameAsIncoming
    $ap = New-SPTrustedIdentityTokenIssuer -Name "AzureAD" -Description "SharePoint secured by Azure AD" -realm $realm -ImportTrustCertificate $cert -ClaimsMappings $map,$map2,$map3,$map4 -SignInUrl $wsfedurl -IdentifierClaim "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name"
    ```

    이제 다음 단계에 따라 애플리케이션에 대해 신뢰할 수 있는 ID 공급자를 사용하도록 설정합니다.

    a. 중앙 관리에서 **웹 애플리케이션 관리**로 이동하고 Azure AD로 보호하려는 웹 애플리케이션을 선택합니다.

    b. 리본 메뉴에서 **인증 공급자**를 클릭하고 사용하려는 영역을 선택합니다.

    다. **신뢰할 수 있는 ID 공급자**를 선택하고 방금 등록한 이름이 *AzureAD*인 ID 공급자를 선택합니다.

    d. 로그인 페이지 URL 설정에서 **사용자 지정 로그인 페이지**를 선택하고 “/_trust/”값을 입력합니다.

    e. **확인**을 클릭합니다.

    ![인증 공급자 구성](./media/sharepoint-on-premises-tutorial/fig10-configauthprovider.png)

    > [!NOTE]
    > 일부 외부 사용자는 해당 UPN이 `MYEMAIL_outlook.com#ext#@TENANT.onmicrosoft.com`과 같은 손상된 값을 가지므로 이 Single Sign-On 통합을 사용할 수 없습니다. 곧 사용자 유형에 따라 UPN을 처리하는 방법에 대한 고객 앱 구성을 허용할 계획입니다. 그런 다음, 모든 게스트 사용자는 조직의 직원으로 SSO를 원활하게 사용할 수 있어야 합니다.

### <a name="create-an-azure-ad-test-user"></a>Azure AD 테스트 사용자 만들기

이 섹션의 목적은 Azure Portal에서 Britta Simon이라는 테스트 사용자를 만드는 것입니다.

1. Azure Portal의 왼쪽 창에서 **Azure Active Directory**, **사용자**를 차례로 선택하고 **모든 사용자**를 선택합니다.

    !["사용자 및 그룹" 및 "모든 사용자" 링크](common/users.png)

2. 화면 위쪽에서 **새 사용자**를 선택합니다.

    ![새 사용자 단추](common/new-user.png)

3. 사용자 속성에서 다음 단계를 수행합니다.

    ![사용자 대화 상자](common/user-properties.png)

    a. **이름** 필드에 **BrittaSimon**을 입력합니다.
  
    b. **사용자 이름** 필드에 **brittasimon\@yourcompanydomain.extension**을 입력합니다.  
    예를 들어 BrittaSimon@contoso.com

    c. **암호 표시** 확인란을 선택한 다음, [암호] 상자에 표시된 값을 적어둡니다.

    d. **만들기**를 클릭합니다.

### <a name="create-an-azure-ad-security-group-in-the-azure-portal"></a>Azure Portal에서 Azure AD 보안 그룹 만들기

1. **Azure Active Directory > 모든 그룹**을 클릭합니다.

    ![Azure AD 보안 그룹 만들기](./media/sharepoint-on-premises-tutorial/allgroups.png)

2. **새 그룹**을 클릭합니다.

    ![Azure AD 보안 그룹 만들기](./media/sharepoint-on-premises-tutorial/newgroup.png)

3. **그룹 유형**, **그룹 이름**, **그룹 설명**, **멤버 자격 유형**을 입력합니다. 화살표를 클릭하여 멤버를 선택하고 그룹에 추가하려는 멤버를 검색하거나 클릭합니다. **선택**을 클릭하여 선택한 멤버를 추가하고 **만들기**를 클릭합니다.

    ![Azure AD 보안 그룹 만들기](./media/sharepoint-on-premises-tutorial/addingmembers.png)

    > [!NOTE]
    > Azure Active Directory 보안 그룹을 SharePoint 온-프레미스에 할당하려면 온-프레미스 SharePoint 팜에서 [AzureCP](https://yvand.github.io/AzureCP/)를 설치 및 구성하거나 SharePoint용 대체 사용자 지정 클레임 공급자를 개발 및 구성해야 합니다.  AzureCP를 사용하지 않는 경우, 이 문서 끝에서 사용자 고유의 사용자 지정 클레임 공급자 생성에 대한 섹션을 참조하세요.

### <a name="grant-access-to-sharepoint-on-premises-security-group"></a>SharePoint 온-프레미스 보안 그룹에 대한 액세스 권한 부여

**앱 등록에서 보안 그룹 및 사용 권한 구성**

1. Azure Portal에서 **Azure Active Directory**를 선택한 다음, **앱 등록**을 선택합니다.

    ![엔터프라이즈 애플리케이션 블레이드](./media/sharepoint-on-premises-tutorial/appregistrations.png)

2. 검색 상자에서 **SharePoint 온-프레미스**를 입력하고 선택합니다.

    ![결과 목록의 SharePoint 온-프레미스](./media/sharepoint-on-premises-tutorial/appsearch.png)

3. **매니페스트**를 클릭합니다.

    ![매니페스트 옵션](./media/sharepoint-on-premises-tutorial/manifest.png)

4. `groupMembershipClaims`: `NULL`을 `groupMembershipClaims`: `SecurityGroup`으로 수정합니다. 그런 후, 저장을 클릭합니다.

    ![매니페스트 편집](./media/sharepoint-on-premises-tutorial/manifestedit.png)

5. **설정**을 클릭한 후 **필수 권한**을 클릭합니다.

    ![필요한 사용 권한](./media/sharepoint-on-premises-tutorial/settings.png)

6. **추가**를 클릭하고 **API 선택**을 클릭합니다.

    ![API 액세스](./media/sharepoint-on-premises-tutorial/required_permissions.png)

7. **Microsoft Azure Active Directory** 및 **Microsoft Graph API**를 둘 다 추가합니다. 그렇지만 선택할 때는 한 번에 하나씩만 선택할 수 있습니다.

    ![API 선택](./media/sharepoint-on-premises-tutorial/permissions.png)

8. Microsoft Azure Active Directory를 선택하고 디렉터리 데이터 읽기를 선택한 후 선택을 클릭합니다. 돌아가서 Microsoft Graph를 추가한 후, 마찬가지로 디렉터리 데이터 읽기를 선택합니다.  선택을 클릭하고 완료를 클릭합니다.

    ![액세스 사용](./media/sharepoint-on-premises-tutorial/readpermission.png)

9. 이제 필수 설정 아래에서 **권한 부여**를 클릭하고 예를 클릭하여 권한을 부여합니다.

    ![권한 부여](./media/sharepoint-on-premises-tutorial/grantpermission.png)

    > [!NOTE]
    > 알림 아래에서 권한이 성공적으로 부여되었는지를 확인합니다.  그렇지 않은 경우, AzureCP가 제대로 작동하지 않고, Azure Active Directory 보안 그룹을 사용하여 SharePoint 온-프레미스를 구성할 수 없습니다.

10. SharePoint 온-프레미스 팜 또는 대체 사용자 지정 클레임 공급자 솔루션에서 AzureCP를 구성합니다.  이 예제에서는 AzureCP를 사용합니다.

    > [!NOTE]
    > AzureCP는 Microsoft 제품이 아니며 Microsoft 기술 지원 서비스에서 지원되지 않습니다. https://yvand.github.io/AzureCP/에 따라 온-프레미스 SharePoint 팜에서 AzureCP 다운로드, 설치 및 구성 

11. **온-프레미스 SharePoint에서 Azure Active Directory 보안 그룹에 대한 액세스 권한 부여** - 그룹에 SharePoint 온-프레미스의 애플리케이션에 대한 액세스 권한을 부여해야 합니다.  다음 단계를 사용하여 웹 애플리케이션에 액세스하기 위한 권한을 설정합니다.

12. 중앙 관리에서 애플리케이션 관리, 웹 애플리케이션 관리를 클릭하고, 웹 애플리케이션을 선택하여 리본을 활성화한 후 사용자 정책을 클릭합니다.

    ![중앙 관리](./media/sharepoint-on-premises-tutorial/centraladministration.png)

13. 웹 애플리케이션 정책 아래에서 사용자 추가를 클릭하고 영역을 선택한 후 다음을 클릭합니다.  주소록을 클릭합니다.

    ![웹 애플리케이션 정책](./media/sharepoint-on-premises-tutorial/webapp-policy.png)

14. 그런 다음, Azure Active Directory 보안 그룹을 검색한 후 추가하고 확인을 클릭합니다.

    ![보안 그룹 추가](./media/sharepoint-on-premises-tutorial/securitygroup.png)

15. 권한을 선택한 다음, 마침을 클릭합니다.

    ![보안 그룹 추가](./media/sharepoint-on-premises-tutorial/permissions1.png)

16. 웹 애플리케이션 아래에서 Azure Active Directory 그룹이 추가되었는지 확인합니다.  그룹 클레임은 사용자 이름의 Azure Active Directory 보안 그룹 개체 ID를 표시합니다.

    ![보안 그룹 추가](./media/sharepoint-on-premises-tutorial/addgroup.png)

17. SharePoint 사이트 모음을 찾은 다음, 그룹도 추가합니다. 사이트 설정에서 사이트 권한 및 권한 부여를 차례로 클릭합니다.  그룹 역할 클레임을 검색하고, 사용 권한 수준을 할당한 후 공유를 클릭합니다.

    ![보안 그룹 추가](./media/sharepoint-on-premises-tutorial/grantpermission1.png)

### <a name="configuring-one-trusted-identity-provider-for-multiple-web-applications"></a>여러 웹 애플리케이션에 대해 하나의 신뢰할 수 있는 ID 공급자 구성

이 구성은 단일 웹 애플리케이션에서 작동하지만 여러 웹 애플리케이션에 대해 동일한 신뢰할 수 있는 ID 공급자를 사용하려는 경우 추가 구성이 필요합니다. 예를 들어 웹 애플리케이션을 확장하여 `https://portal.contoso.local` URL를 사용하고 이제 `https://sales.contoso.local`에 대해 사용자를 인증하려고 합니다. 이렇게 하려면 ID 공급자를 업데이트하여 WReply 매개 변수를 인식하고 Azure AD에서 애플리케이션 등록을 업데이트하여 회신 URL을 추가해야 합니다.

1. Azure Portal에서 Azure AD 디렉터리를 엽니다. **앱 등록**을 클릭한 다음, **모든 애플리케이션 보기**를 클릭합니다. 이전에 만든 애플리케이션을 클릭합니다(SharePoint SAML 통합).

2. **설정**을 클릭합니다.

3. 설정 블레이드에서 **회신 URL**을 클릭합니다.

4. URL(예: `https://sales.contoso.local/_trust/default.aspx`)에 추가된 `/_trust/default.aspx`를 사용하여 추가 웹 애플리케이션에 대한 URL을 추가하고 **저장**을 클릭합니다.

5. SharePoint Server에서 **SharePoint 2016 관리 셸**을 열고 다음 명령을 실행하여 이전에 사용한 신뢰할 수 있는 ID 토큰 발급자의 이름을 사용합니다.

    ```powershell
    $t = Get-SPTrustedIdentityTokenIssuer "AzureAD"
    $t.UseWReplyParameter=$true
    $t.Update()
    ```

6. 중앙 관리에서 웹 애플리케이션으로 이동하고 기존의 신뢰할 수 있는 ID 공급자를 사용하도록 설정합니다. 로그인 페이지 URL을 사용자 지정 로그인 페이지 `/_trust/`로 구성해야합니다.

7. 중앙 관리에서 웹 애플리케이션을 클릭하고 **사용자 정책**을 선택합니다. 이 아티클에서 이전에 설명한 대로 적절한 사용 권한이 있는 사용자를 추가합니다.

### <a name="fixing-people-picker"></a>사용자 선택 수정

이제 사용자가 Azure AD의 ID를 사용하여 SharePoint 2016에 로그인할 수 있지만 사용자 환경을 개선할 수 있는 여지가 남아 있습니다. 예를 들어, 사용자 선택에서 사용자를 검색하면 여러 검색 결과가 표시됩니다. 클레임 매핑에서 생성된 3가지 클레임 유형 각각에 대한 검색 결과가 있습니다. 사용자 선택기를 사용하여 사용자를 선택하려면 사용자 이름을 정확하게 입력하고 **이름** 클레임 결과를 선택해야 합니다.

![클레임 검색 결과](./media/sharepoint-on-premises-tutorial/fig16-claimssearchresults.png)

검색한 값에 대해 유효성 검사가 없기 때문에 철자 오류가 발생하거나, 사용자가 실수로 **SurName** 클레임 등, 잘못된 클레임 유형을 선택하여 할당할 수 있습니다. 이렇게 되면 사용자가 리소스에 액세스하지 못할 수 있습니다.

이 시나리오를 지원하기 위해 [AzureCP](https://yvand.github.io/AzureCP/)라고 하는 오픈 소스 솔루션이 SharePoint 2016에 대한 사용자 지정 클레임 공급자를 제공합니다. 여기서는 Azure AD Graph를 사용하여 사용자 입력을 확인하고 유효성 검사를 수행합니다. [AzureCP](https://yvand.github.io/AzureCP/)에 대한 정보를 알아보세요.

### <a name="assign-the-azure-ad-security-group-in-the-azure-portal"></a>Azure Portal에서 Azure AD 보안 그룹 할당

1. Azure Portal에서 **엔터프라이즈 애플리케이션**, **모든 애플리케이션**, **SharePoint 온-프레미스**를 차례로 선택합니다.

    ![엔터프라이즈 애플리케이션 블레이드](common/enterprise-applications.png)

2. 애플리케이션 목록에서 **SharePoint 온-프레미스**를 입력하고 선택합니다.

    ![애플리케이션 목록의 SharePoint 온-프레미스 링크](common/all-applications.png)

3. 왼쪽 메뉴에서 **사용자 및 그룹**을 선택합니다.

    !["사용자 및 그룹" 링크](common/users-groups-blade.png)

4. **사용자 추가**를 클릭합니다.

    ![할당 추가 창](common/add-assign-user.png)

5. 사용하려는 보안 그룹을 검색하고 그룹을 클릭하여 멤버 선택 섹션에 추가합니다. **선택**을 클릭한 다음, **할당**을 클릭합니다.

    ![보안 그룹 검색](./media/sharepoint-on-premises-tutorial/securitygroup1.png)

    > [!NOTE]
    > Azure Portal에서 엔터프라이즈 애플리케이션에 그룹이 할당되었다는 알림을 받으려면 메뉴 모음에서 알림을 선택합니다.

### <a name="create-sharepoint-on-premises-test-user"></a>SharePoint 온-프레미스 테스트 사용자 만들기

이 섹션에서는 SharePoint 온-프레미스에서 Britta Simon이라는 사용자를 만듭니다.  [SharePoint 온-프레미스 지원 팀](https://support.office.com/)과 협력하여 사용자를 SharePoint 온-프레미스 플랫폼에 추가합니다. Single Sign-On을 사용하려면 먼저 사용자를 만들고 활성화해야 합니다.

### <a name="test-single-sign-on"></a>Single Sign-On 테스트

이 섹션에서는 액세스 패널을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다.

액세스 패널에서 SharePoint 온-프레미스 타일을 클릭하면 SSO를 설정한 SharePoint 온-프레미스 애플리케이션에 자동으로 로그인됩니다. 액세스 패널에 대한 자세한 내용은 [액세스 패널 소개](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)를 참조하세요.

## <a name="additional-resources"></a>추가 리소스

- [Azure Active Directory와 SaaS Apps를 통합하는 방법에 대한 자습서 목록](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory로 애플리케이션 액세스 및 Single Sign-On이란 무엇입니까?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory의 조건부 액세스란?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)