---
title: '자습서: SharePoint 온-프레미스와 Azure Active Directory 통합 | Microsoft Docs'
description: Azure Active Directory와 SharePoint 온-프레미스 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 85b8d4d0-3f6a-4913-b9d3-8cc327d8280d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/21/2018
ms.author: jeedes
ms.openlocfilehash: 9c63808af8d883badd379cf8bc3372a1d65d6624
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/06/2018
ms.locfileid: "52968659"
---
# <a name="tutorial-azure-active-directory-integration-with-sharepoint-on-premises"></a>자습서: SharePoint 온-프레미스와 Azure Active Directory 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 SharePoint 온-프레미스를 통합하는 방법에 대해 알아봅니다.

SharePoint 온-프레미스를 Azure AD와 통합하면 다음과 같은 장점이 있습니다.

- SharePoint 온-프레미스에 대한 액세스 권한이 있는 사용자를 Azure AD에서 제어할 수 있습니다.
- 사용자가 해당 Azure AD 계정으로 SharePoint 온-프레미스에 자동으로 로그온(Single Sign-On)되도록 설정할 수 있습니다.
- 단일 중앙 위치인 Azure Portal에서 계정을 관리할 수 있습니다.

Azure AD와 SaaS 앱 통합에 대한 자세한 내용은 [Azure Active Directory의 응용 프로그램 액세스 및 Single Sign-On이란 무엇인가요?](../manage-apps/what-is-single-sign-on.md)를 참조하세요.

## <a name="prerequisites"></a>필수 조건

SharePoint 온-프레미스와 Azure AD 통합을 구성하려면 다음 항목이 필요합니다.

- Azure AD 구독
- 온-프레미스 SharePoint Single Sign-On이 설정된 구독

> [!NOTE]
> 이 자습서의 단계를 테스트하기 위해 프로덕션 환경을 사용하는 것은 바람직하지 않습니다.

이 자습서의 단계를 테스트하려면 다음 권장 사항을 준수해야 합니다.

- 꼭 필요한 경우가 아니면 프로덕션 환경을 사용하지 마세요.
- Azure AD 평가판 환경이 없으면 [1개월 평가판을 얻을](https://azure.microsoft.com/pricing/free-trial/) 수 있습니다.

## <a name="scenario-description"></a>시나리오 설명

이 자습서에서는 테스트 환경에서 Azure AD Single Sign-On을 테스트 합니다.
이 자습서에 설명된 시나리오는 다음 두 가지 주요 구성 요소로 이루어져 있습니다.

1. 갤러리에서 SharePoint 온-프레미스 추가
2. Azure AD Single Sign-on 구성 및 테스트

## <a name="adding-sharepoint-on-premises-from-the-gallery"></a>갤러리에서 SharePoint 온-프레미스 추가

SharePoint 온-프레미스의 Azure AD 통합을 구성하려면 갤러리의 SharePoint 온-프레미스를 관리되는 SaaS 앱 목록에 추가해야 합니다.

**갤러리에서 SharePoint 온-프레미스를 추가하려면 다음 단계를 수행합니다.**

1. **[Azure Portal](https://portal.azure.com)** 의 왼쪽 탐색 창에서 **Azure Active Directory** 아이콘을 클릭합니다.

    ![Azure Active Directory 단추][1]

2. **엔터프라이즈 응용 프로그램**으로 이동합니다. 그런 후 **모든 애플리케이션**으로 이동합니다.

    ![엔터프라이즈 애플리케이션 블레이드][2]

3. 새 애플리케이션을 추가하려면 대화 상자 맨 위 있는 **새 애플리케이션** 단추를 클릭합니다.

    ![새 응용 프로그램 단추][3]

4. 검색 상자에 **SharePoint 온-프레미스**를 입력하고 결과 패널에서 **SharePoint 온-프레미스**를 선택한 다음, **추가** 단추를 클릭하여 응용 프로그램을 추가합니다.

    ![결과 목록의 SharePoint 온-프레미스](./media/sharepoint-on-premises-tutorial/tutorial_sharepointonpremises_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성 및 테스트

이 섹션에서는 “Britta Simon”이라는 테스트 사용자를 기반으로 SharePoint 온-프레미스에서 Azure AD Single Sign-On을 구성하고 테스트합니다.

Single Sign-On이 작동하려면 Azure AD에서 Azure AD 사용자에 해당하는 SharePoint 온-프레미스 사용자가 누구인지 알고 있어야 합니다. 즉, Azure AD 사용자와 SharePoint 온-프레미스의 관련 사용자 간에 연결이 설정되어야 합니다.

SharePoint 온-프레미스에서 Azure AD Single Sign-On을 구성하고 테스트하려면 다음 구성 요소를 완료해야 합니다.

1. **[Azure AD Single Sign-On 구성](#configure-azure-ad-single-sign-on)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
2. **[Azure AD 테스트 사용자 만들기](#create-an-azure-ad-test-user)** - Britta Simon으로 Azure AD Single Sign-On을 테스트하는 데 사용합니다.
3. **[SharePoint 온-프레미스 테스트 사용자에 대한 액세스 권한 부여](#grant-access-to-sharePoint-on-premises-test-user)** - Britta Simon의 Azure AD 표현과 연결된 해당 사용자를 SharePoint 온-프레미스에 만듭니다.
4. **[Azure AD 테스트 사용자 할당](#assign-the-azure-ad-test-user)** - Britta Simon이 Azure AD Single Sign-on을 사용할 수 있도록 합니다.
5. **[Single Sign-On 테스트](#test-single-sign-on)** - 구성이 작동하는지 여부를 확인합니다.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성

이 섹션에서는 Azure Portal에서 Azure AD Single Sign-On을 사용하도록 설정하고 SharePoint 온-프레미스 응용 프로그램에서 Single Sign-On을 구성합니다.

**SharePoint 온-프레미스에서 Azure AD Single Sign-On을 구성하려면 다음 단계를 수행합니다.**

1. Azure Portal의 **SharePoint 온-프레미스** 응용 프로그램 통합 페이지에서 **Single Sign-On**을 클릭합니다.

    ![Single Sign-On 구성 링크][4]

2. **Single Sign-On** 대화 상자에서 **모드**를 **SAML 기반 로그온**으로 선택하여 Single Sign-On을 사용하도록 설정합니다.

    ![Single Sign-On 대화 상자](./media/sharepoint-on-premises-tutorial/tutorial_sharepointonpremises_samlbase.png)

3. **SharePoint 온-프레미스 도메인 및 URL** 섹션에서 다음 단계를 수행합니다.

    ![SharePoint 온-프레미스 도메인 및 URL Single Sign-On 정보](./media/sharepoint-on-premises-tutorial/tutorial_sharepointonpremises_url1.png)

    a. **로그온 URL** 텍스트 상자에서 다음 패턴으로 URL을 입력합니다. `https://<YourSharePointServerURL>/_trust/default.aspx`

    b. **식별자** 텍스트 상자에 URL `urn:sharepoint:federation`을 입력합니다.

    다. **회신 URL** 텍스트 상자에 다음 패턴으로 URL을 입력합니다.`https://<YourSharePointServerURL>/_trust/default.aspx`

4. **SAML 서명 인증서** 섹션에서 **인증서(Base64)** 를 클릭한 후 컴퓨터에 인증서 파일을 저장합니다.

    ![인증서 다운로드 링크](./media/sharepoint-on-premises-tutorial/tutorial_sharepointonpremises_certificate.png)

    > [!Note]
    > 구성을 위해 PowerShell 스크립트에서 나중에 사용해야 하므로 인증서 파일을 다운로드한 파일 경로를 적어 두세요.

5. **저장** 단추를 클릭합니다.

    ![Single Sign-On 구성 저장 단추](./media/sharepoint-on-premises-tutorial/tutorial_general_400.png)

6. **SharePoint 온-프레미스 구성** 섹션에서 **SharePoint 온-프레미스 구성**을 클릭하여 **로그온 구성** 창을 엽니다. **빠른 참조 섹션**에서 **SAML 엔터티 ID**를 복사합니다. **Single Sign-On 서비스 URL**의 경우 다음 패턴의 값을 사용합니다. `https://login.microsoftonline.com/_my_directory_id_/wsfed` 

    > [!Note]
    > _my_directory_id_는 Azure Ad 구독의 테넌트 ID입니다.

    ![SharePoint 온-프레미스 구성](./media/sharepoint-on-premises-tutorial/tutorial_sharepointonpremises_configure.png)

    > [!NOTE]
    > Sharepoint On-Premises 응용 프로그램은 SAML 1.1 토큰을 사용하므로, Azure AD는 SharePoint 서버로부터 WS Fed 요청을 기대하고 인증 후 SAML 1.1 토큰을 발급합니다.

7. 다른 웹 브라우저 창에서 SharePoint 온-프레미스 회사 사이트에 관리자 권한으로 로그인합니다.

8. **SharePoint Server 2016에서 신뢰할 수 있는 새 ID 공급자 구성**

    SharePoint Server 2016 서버에 로그인하고 SharePoint 2016 관리 셸을 엽니다. Azure Portal에서 $realm(Azure Portal의 온-프레미스 도메인 및 URL 섹션에서 SharePoint의 식별자 값), $wsfedurl(Single Sign-On 서비스 URL) 및 $filepath(인증서 파일을 다운로드한 파일 경로)의 값을 채우고 다음 명령을 실행하여 새 신뢰할 수 있는 ID 공급자를 구성합니다.

    > [!TIP]
    > PowerShell을 처음 사용하거나 PowerShell 작동 방법에 대해 자세히 알아보려면 [SharePoint PowerShell](https://docs.microsoft.com/powershell/sharepoint/overview?view=sharepoint-ps)을 참조하세요. 

    ```
    $realm = "<Identifier value from the SharePoint on-premises Domain and URLs section in the Azure portal>"
    $wsfedurl="<SAML single sign-on service URL value which you have copied from the Azure portal>"
    $filepath="<Full path to SAML signing certificate file which you have downloaded from the Azure portal>"
    $cert = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2($filepath)
    New-SPTrustedRootAuthority -Name "AzureAD" -Certificate $cert
    $map = New-SPClaimTypeMapping -IncomingClaimType "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name" -IncomingClaimTypeDisplayName "name" -LocalClaimType "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn"
    $map2 = New-SPClaimTypeMapping -IncomingClaimType "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname" -IncomingClaimTypeDisplayName "GivenName" -SameAsIncoming
    $map3 = New-SPClaimTypeMapping -IncomingClaimType "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname" -IncomingClaimTypeDisplayName "SurName" -SameAsIncoming
    $map4 = New-SPClaimTypeMapping -IncomingClaimType "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress" -IncomingClaimTypeDisplayName "Email" -SameAsIncoming
    $ap = New-SPTrustedIdentityTokenIssuer -Name "AzureAD" -Description "SharePoint secured by Azure AD" -realm $realm -ImportTrustCertificate $cert -ClaimsMappings $map,$map2,$map3,$map4 -SignInUrl $wsfedurl -IdentifierClaim "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name"
    ```

    이제 다음 단계에 따라 응용 프로그램에 대해 신뢰할 수 있는 ID 공급자를 사용하도록 설정합니다.

    a. 중앙 관리에서 **웹 응용 프로그램 관리**로 이동하고 Azure AD로 보호하려는 웹 응용 프로그램을 선택합니다.

    b. 리본 메뉴에서 **인증 공급자**를 클릭하고 사용하려는 영역을 선택합니다.

    다. **신뢰할 수 있는 ID 공급자**를 선택하고 방금 등록한 이름이 *AzureAD*인 ID 공급자를 선택합니다.

    d. 로그인 페이지 URL 설정에서 **사용자 지정 로그인 페이지**를 선택하고 “/_trust/”값을 입력합니다.

    e. **확인**을 클릭합니다.

    ![인증 공급자 구성](./media/sharepoint-on-premises-tutorial/fig10-configauthprovider.png)

    > [!NOTE]
    > 일부 외부 사용자는 해당 UPN이 `MYEMAIL_outlook.com#ext#@TENANT.onmicrosoft.com`과 같은 손상된 값을 가지므로 이 Single Sign-On 통합을 사용할 수 없습니다. 곧 사용자 유형에 따라 UPN을 처리하는 방법에 대한 고객 앱 구성을 허용할 계획입니다. 그런 다음, 모든 게스트 사용자는 조직의 직원으로 SSO를 원활하게 사용할 수 있어야 합니다.

### <a name="create-an-azure-ad-test-user"></a>Azure AD 테스트 사용자 만들기

이 섹션의 목적은 Azure Portal에서 Britta Simon이라는 테스트 사용자를 만드는 것입니다.

   ![Azure AD 테스트 사용자 만들기][100]

**Azure AD에서 테스트 사용자를 만들려면 다음 단계를 수행하세요.**

1. Azure Portal의 왼쪽 창에서 **Azure Active Directory** 단추를 클릭합니다.

    ![Azure Active Directory 단추](./media/sharepoint-on-premises-tutorial/create_aaduser_01.png)

2. 사용자 목록을 표시하려면 **사용자 및 그룹**으로 이동한 후 **모든 사용자**를 클릭합니다.

    !["사용자 및 그룹" 및 "모든 사용자" 링크](./media/sharepoint-on-premises-tutorial/create_aaduser_02.png)

3. **사용자** 대화 상자를 열려면 **모든 사용자** 대화 상자 위쪽에서 **추가**를 클릭합니다.

    ![추가 단추](./media/sharepoint-on-premises-tutorial/create_aaduser_03.png)

4. **사용자** 대화 상자에서 다음 단계를 수행합니다.

    ![사용자 대화 상자](./media/sharepoint-on-premises-tutorial/create_aaduser_04.png)

    a. **이름** 상자에 **BrittaSimon**을 입력합니다.

    나. **사용자 이름** 상자에 사용자인 Britta Simon의 전자 메일 주소를 입력합니다.

    다. **암호 표시** 확인란을 선택한 다음 **암호** 상자에 표시된 값을 적어둡니다.

    d. **만들기**를 클릭합니다.

### <a name="grant-access-to-sharepoint-on-premises-test-user"></a>SharePoint 온-프레미스 테스트 사용자에 대한 액세스 권한 부여

Azure AD에 로그인하고 SharePoint에 액세스할 사용자는 응용 프로그램에 대한 액세스 권한이 부여되어야 합니다. 다음 단계를 사용하여 웹 응용 프로그램에 액세스하는 사용 권한을 설정합니다.

1. 중앙 관리에서 **응용 프로그램 관리**를 클릭합니다.

2. **응용 프로그램 관리** 페이지의 **웹 응용 프로그램** 섹션에서 **웹 응용 프로그램 관리**를 클릭합니다.

3. 적합한 웹 응용 프로그램을 클릭한 다음, **사용자 정책**을 클릭합니다.

4. 웹 응용 프로그램에 대한 정책에서 **사용자 추가**를 클릭합니다.

    ![이름 클레임으로 사용자 검색](./media/sharepoint-on-premises-tutorial/fig11-searchbynameclaim.png)

5. **사용자 추가** 대화 상자의 **영역**에서 적합한 영역을 클릭하고 **다음**을 클릭합니다.

6. **웹 응용 프로그램에 대한 정책** 대화 상자의 **사용자 선택** 섹션에서 **찾아보기** 아이콘을 클릭합니다.

7. **찾기** 텍스트 상자에 Azure AD에서 SharePoint 온-프레미스 응용 프로그램을 구성한 **UPN(사용자 계정 이름)** 값을 입력하고 **검색**을 클릭합니다. </br>예: *brittasimon@contoso.com*.

8. 목록 보기의 AzureAD 머리글에서 이름 속성을 선택하고 **추가**를 클릭한 다음, **확인**을 클릭하여 대화 상자를 닫습니다.

9. 권한에서 **모든 권한**을 클릭합니다.

    ![클레임 사용자에게 모든 권한 부여](./media/sharepoint-on-premises-tutorial/fig12-grantfullcontrol.png)

10. **다음**을 클릭한 후 **확인**을 클릭합니다.

### <a name="configuring-one-trusted-identity-provider-for-multiple-web-applications"></a>여러 웹 응용 프로그램에 대해 하나의 신뢰할 수 있는 ID 공급자 구성

이 구성은 단일 웹 응용 프로그램에서 작동하지만 여러 웹 응용 프로그램에 대해 동일한 신뢰할 수 있는 ID 공급자를 사용하려는 경우 추가 구성이 필요합니다. 예를 들어 웹 응용 프로그램을 확장하여 `https://portal.contoso.local` URL를 사용하고 이제 `https://sales.contoso.local`에 대해 사용자를 인증하려고 합니다. 이렇게 하려면 ID 공급자를 업데이트하여 WReply 매개 변수를 인식하고 Azure AD에서 응용 프로그램 등록을 업데이트하여 회신 URL을 추가해야 합니다.

1. Azure Portal에서 Azure AD 디렉터리를 엽니다. **앱 등록**을 클릭한 다음, **모든 응용 프로그램 보기**를 클릭합니다. 이전에 만든 응용 프로그램을 클릭합니다(SharePoint SAML 통합).

2. **설정**을 클릭합니다.

3. 설정 블레이드에서 **회신 URL**을 클릭합니다. 

4. URL(예: `https://sales.contoso.local/_trust/default.aspx`)에 추가된 `/_trust/default.aspx`를 사용하여 추가 웹 응용 프로그램에 대한 URL을 추가하고 **저장**을 클릭합니다.

5. SharePoint Server에서 **SharePoint 2016 관리 셸**을 열고 다음 명령을 실행하여 이전에 사용한 신뢰할 수 있는 ID 토큰 발급자의 이름을 사용합니다.

    ```
    $t = Get-SPTrustedIdentityTokenIssuer "AzureAD"
    $t.UseWReplyParameter=$true
    $t.Update()
    ```
6. 중앙 관리에서 웹 응용 프로그램으로 이동하고 기존의 신뢰할 수 있는 ID 공급자를 사용하도록 설정합니다. 로그인 페이지 URL을 사용자 지정 로그인 페이지 `/_trust/`로 구성해야합니다.

7. 중앙 관리에서 웹 응용 프로그램을 클릭하고 **사용자 정책**을 선택합니다. 이 아티클에서 이전에 설명한 대로 적절한 사용 권한이 있는 사용자를 추가합니다.

### <a name="fixing-people-picker"></a>사용자 선택 수정

이제 사용자가 Azure AD의 ID를 사용하여 SharePoint 2016에 로그인할 수 있지만 사용자 환경을 개선할 수 있는 여지가 남아 있습니다. 예를 들어, 사용자 선택에서 사용자를 검색하면 여러 검색 결과가 표시됩니다. 클레임 매핑에서 생성된 3가지 클레임 유형 각각에 대한 검색 결과가 있습니다. 사용자 선택기를 사용하여 사용자를 선택하려면 사용자 이름을 정확하게 입력하고 **이름** 클레임 결과를 선택해야 합니다.

![클레임 검색 결과](./media/sharepoint-on-premises-tutorial/fig16-claimssearchresults.png)

검색한 값에 대해 유효성 검사가 없기 때문에 철자 오류가 발생하거나, 사용자가 실수로 **SurName** 클레임 등, 잘못된 클레임 유형을 선택하여 할당할 수 있습니다. 이렇게 되면 사용자가 리소스에 액세스하지 못할 수 있습니다.

이 시나리오를 지원하기 위해 [AzureCP](https://yvand.github.io/AzureCP/)라고 하는 오픈 소스 솔루션이 SharePoint 2016에 대한 사용자 지정 클레임 공급자를 제공합니다. 여기서는 Azure AD Graph를 사용하여 사용자 입력을 확인하고 유효성 검사를 수행합니다. [AzureCP](https://yvand.github.io/AzureCP/)에 대한 정보를 알아보세요.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD 테스트 사용자 할당

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 Britta Simon에게 SharePoint 온-프레미스에 대한 액세스 권한을 부여합니다.

![사용자 역할 할당][200]

**Britta Simon을 SharePoint 온-프레미스에 할당하려면 다음 단계를 수행합니다.**

1. Azure Portal에서 애플리케이션 보기를 연 다음 디렉터리 보기로 이동하고 **엔터프라이즈 애플리케이션**으로 이동한 후 **모든 애플리케이션**을 클릭합니다.

    ![사용자 할당][201]

2. 응용 프로그램 목록에서 **SharePoint 온-프레미스**를 선택합니다.

    ![응용 프로그램 목록의 SharePoint 링크](./media/sharepoint-on-premises-tutorial/tutorial_sharepointonpremises_app.png)

3. 왼쪽 메뉴에서 **사용자 및 그룹**을 클릭합니다.

    !["사용자 및 그룹" 링크][202]

4. **추가** 단추를 클릭합니다. 그런 후 **할당 추가** 대화 상자에서 **사용자 및 그룹**을 선택합니다.

    ![할당 추가 창][203]

5. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **Britta Simon**을 선택합니다.

6. **사용자 및 그룹** 대화 상자에서 **선택** 단추를 클릭합니다.

7. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.

### <a name="test-single-sign-on"></a>Single Sign-On 테스트

이 섹션에서는 액세스 패널을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다.

액세스 패널에서 SharePoint 온-프레미스 타일을 클릭하면 SharePoint 온-프레미스 응용 프로그램에 자동으로 로그온됩니다.
액세스 패널에 대한 자세한 내용은 [액세스 패널 소개](../user-help/active-directory-saas-access-panel-introduction.md)를 참조하세요.

## <a name="additional-resources"></a>추가 리소스

* [Azure Active Directory와 SaaS Apps를 통합하는 방법에 대한 자습서 목록](tutorial-list.md)
* [Azure Active Directory로 응용 프로그램 액세스 및 Single Sign-On을 구현하는 방법](../manage-apps/what-is-single-sign-on.md)
* [SharePoint Server 인증에 대해 Azure AD 사용](https://docs.microsoft.com/office365/enterprise/using-azure-ad-for-sharepoint-server-authentication)

<!--Image references-->

[1]: ./media/sharepoint-on-premises-tutorial/tutorial_general_01.png
[2]: ./media/sharepoint-on-premises-tutorial/tutorial_general_02.png
[3]: ./media/sharepoint-on-premises-tutorial/tutorial_general_03.png
[4]: ./media/sharepoint-on-premises-tutorial/tutorial_general_04.png

[100]: ./media/sharepoint-on-premises-tutorial/tutorial_general_100.png

[200]: ./media/sharepoint-on-premises-tutorial/tutorial_general_200.png
[201]: ./media/sharepoint-on-premises-tutorial/tutorial_general_201.png
[202]: ./media/sharepoint-on-premises-tutorial/tutorial_general_202.png
[203]: ./media/sharepoint-on-premises-tutorial/tutorial_general_203.png
