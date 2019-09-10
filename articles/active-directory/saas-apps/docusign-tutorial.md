---
title: '자습서: DocuSign과 Azure Active Directory SSO(Single Sign-On) 통합 | Microsoft Docs'
description: Azure Active Directory 및 DocuSign 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: a691288b-84c1-40fb-84bd-5b06878865f0
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/02/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: fe1f018612e889f49993895d88cdaf9ad732b393
ms.sourcegitcommit: f176e5bb926476ec8f9e2a2829bda48d510fbed7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70306309"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-docusign"></a>자습서: DocuSign과 Azure Active Directory SSO(Single Sign-On) 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 DocuSign을 통합하는 방법에 대해 알아봅니다. Azure AD와 DocuSign을 통합하는 경우 다음을 수행할 수 있습니다.

* Azure AD에서 DocuSign에 대한 액세스 권한이 있는 사용자를 제어합니다.
* 사용자가 자신의 Azure AD 계정으로 DocuSign에 자동으로 로그인되도록 설정합니다.
* 단일 중앙 위치인 Azure Portal에서 계정을 관리합니다.

Azure AD와 SaaS 앱 통합에 대한 자세한 내용은 [Azure Active Directory를 사용한 애플리케이션 액세스 및 Single Sign-On이란 무엇인가요?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)를 참조하세요.

## <a name="prerequisites"></a>필수 조건

시작하려면 다음 항목이 필요합니다.

* Azure AD 구독 구독이 없는 경우 [체험 계정](https://azure.microsoft.com/free/)을 얻을 수 있습니다.
* DocuSign SSO(Single Sign-On)가 설정된 구독

## <a name="scenario-description"></a>시나리오 설명

이 자습서에서는 테스트 환경에서 Azure AD SSO를 구성하고 테스트합니다.

* DocuSign은 **SP** 시작 SSO를 지원합니다.

* DocuSign은 **Just-In-Time** 사용자 프로비저닝을 지원합니다.

* DocuSign이 [자동 사용자 프로비저닝](https://docs.microsoft.com/azure/active-directory/saas-apps/docusign-provisioning-tutorial)을 지원

## <a name="adding-docusign-from-the-gallery"></a>갤러리에서 DocuSign 추가

DocuSign의 Azure AD 통합을 구성하려면 갤러리의 DocuSign을 관리되는 SaaS 앱 목록에 추가해야 합니다.

1. [Azure Portal](https://portal.azure.com)에 회사 또는 학교 계정, 개인 Microsoft 계정으로 로그인합니다.
1. 왼쪽 탐색 창에서 **Azure Active Directory** 서비스를 선택합니다.
1. **엔터프라이즈 애플리케이션**으로 이동한 다음, **모든 애플리케이션**을 선택합니다.
1. 새 애플리케이션을 추가하려면 **새 애플리케이션**을 선택합니다.
1. **갤러리에서 추가** 섹션의 검색 상자에 **DocuSign**을 입력합니다.
1. 결과 패널에서 **DocuSign**을 선택한 다음, 앱을 추가합니다. 앱이 테넌트에 추가될 때까지 잠시 동안 기다려 주세요.


## <a name="configure-and-test-azure-ad-single-sign-on-for-docusign"></a>DocuSign용 Azure AD Single Sign-On 구성 및 테스트

**B.Simon**이라는 테스트 사용자를 사용하여 DocuSign에서 Azure AD SSO를 구성하고 테스트합니다. SSO가 작동하려면 Azure AD 사용자와 DocuSign의 관련 사용자 간에 연결 관계를 설정해야 합니다.

DocuSign에서 Azure AD SSO를 구성하고 테스트하려면 다음 구성 요소를 완료합니다.

1. **[Azure AD SSO 구성](#configure-azure-ad-sso)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
    1. **[Azure AD 테스트 사용자 만들기](#create-an-azure-ad-test-user)** - B.Simon을 사용하여 Azure AD Single Sign-On을 테스트합니다.
    1. **[Azure AD 테스트 사용자 할당](#assign-the-azure-ad-test-user)** - B. Simon이 Azure AD Single Sign-On을 사용할 수 있도록 합니다.
1. **[DocuSign SSO 구성](#configure-docusign-sso)** - 애플리케이션 쪽에서 Single Sign-On 설정을 구성합니다.
    1. **[DocuSign 테스트 사용자 만들기](#create-docusign-test-user)** - B.Simon의 Azure AD 표현과 연결된 해당 사용자를 DocuSign에 만듭니다.
1. **[SSO 테스트](#test-sso)** - 구성이 작동하는지 여부를 확인합니다.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO 구성

Azure Portal에서 Azure AD SSO를 사용하도록 설정하려면 다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com/)의 **DocuSign** 애플리케이션 통합 페이지에서 **관리** 섹션을 찾은 다음, **Single Sign-On**을 선택합니다.
1. **Single Sign-On 방법 선택** 페이지에서 **SAML**을 선택합니다.
1. **SAML로 Single Sign-On 설정** 페이지에서 **기본 SAML 구성**에 대한 편집(연필 모양) 아이콘을 클릭하여 설정을 편집합니다.

   ![기본 SAML 구성 편집](common/edit-urls.png)

1. **기본 SAML 구성** 섹션에서 다음 필드에 대한 값을 입력합니다.

    a. **로그온 URL** 텍스트 상자에서 `https://<subdomain>.docusign.com/organizations/<OrganizationID>/saml2/login/sp/<IDPID>` 패턴을 사용하는 URL을 입력합니다.

    b. **식별자(엔터티 ID)** 텍스트 상자에서 `https://<subdomain>.docusign.com/organizations/<OrganizationID>/saml2` 패턴을 사용하는 URL을 입력합니다.

    > [!NOTE]
    > 이러한 값은 실제 값이 아닙니다. 자습서 뒷부분에 있는 **SAML 2.0 엔드포인트 보기** 섹션에 설명된 실제 로그온 URL 및 식별자로 값을 업데이트합니다.

1. **SAML로 Single Sign-On 설정** 페이지의 **SAML 서명 인증서** 섹션에서 **인증서(Base64)** 를 찾은 후 **다운로드**를 선택하여 인증서를 다운로드하고 컴퓨터에 저장합니다.

    ![인증서 다운로드 링크](common/certificatebase64.png)

1. **DocuSign 설정** 섹션에서 요구 사항에 따라 적절한 URL을 복사합니다.

    ![구성 URL 복사](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD 테스트 사용자 만들기

이 섹션에서는 Azure Portal에서 B.Simon이라는 테스트 사용자를 만듭니다.

1. Azure Portal의 왼쪽 창에서 **Azure Active Directory**, **사용자**, **모든 사용자**를 차례로 선택합니다.
1. 화면 위쪽에서 **새 사용자**를 선택합니다.
1. **사용자** 속성에서 다음 단계를 수행합니다.
   1. **이름** 필드에 `B.Simon`을 입력합니다.  
   1. **사용자 이름** 필드에서 username@companydomain.extension을 입력합니다. 예: `B.Simon@contoso.com`
   1. **암호 표시** 확인란을 선택한 다음, **암호** 상자에 표시된 값을 적어둡니다.
   1. **만들기**를 클릭합니다.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD 테스트 사용자 할당

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 B.Simon에게 DocuSign에 대한 액세스 권한을 부여합니다.

1. Azure Portal에서 **엔터프라이즈 애플리케이션**을 선택한 다음, **모든 애플리케이션**을 선택합니다.
1. 애플리케이션 목록에서 **DocuSign**을 선택합니다.
1. 앱의 개요 페이지에서 **관리** 섹션을 찾고 **사용자 및 그룹**을 선택합니다.

   !["사용자 및 그룹" 링크](common/users-groups-blade.png)

1. **사용자 추가**를 선택한 다음, **할당 추가** 대화 상자에서 **사용자 및 그룹**을 선택합니다.

    ![사용자 추가 링크](common/add-assign-user.png)

1. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **B.Simon**을 선택한 다음, 화면 아래쪽에서 **선택** 단추를 클릭합니다.
1. SAML 어설션에 역할 값이 필요한 경우 **역할 선택** 대화 상자의 목록에서 사용자에 대한 적절한 역할을 선택한 다음, 화면의 아래쪽에 있는 **선택** 단추를 클릭합니다.
1. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.

## <a name="configure-docusign-sso"></a>DocuSign SSO 구성

1. DocuSign 내에서 구성을 자동화하려면 **확장 설치**를 클릭하여 **내 앱 보안 로그인 브라우저 확장**을 설치해야 합니다.

    ![내 앱 확장](common/install-myappssecure-extension.png)

2. 브라우저에 확장을 추가한 후 **DocuSign 설정**을 클릭하면 DocuSign 애플리케이션으로 이동됩니다. 여기서 관리자 자격 증명을 입력하여 DocuSign에 로그인합니다. 브라우저 확장이 애플리케이션을 자동으로 구성하고 3-5단계를 자동으로 수행합니다.

    ![구성 설정](common/setup-sso.png)

3. DocuSign을 수동으로 설정하려면 새 웹 브라우저 창을 열고 DocuSign 회사 사이트에 관리자로 로그인하여 다음 단계를 수행합니다.

4. 오른쪽 위 모서리에서 프로필 **로고**를 클릭한 다음, **관리자로 이동**을 클릭합니다.
  
    ![Single Sign-On 구성][51]

5. 도메인 솔루션 페이지에서 **도메인**을 클릭합니다.

    ![Single Sign-On 구성][50]

6. **도메인** 섹션에서 **도메인 클레임**을 클릭합니다.

    ![Single Sign-On 구성][52]

7. **도메인 클레임** 대화 상자의 **도메인 이름** 텍스트 상자에 회사 도메인을 입력한 다음, **클레임**을 클릭합니다. 도메인을 확인하고 상태가 활성인지 확인합니다.

    ![Single Sign-On 구성][53]

8. 도메인 솔루션 페이지에서 **ID 공급 기업**을 클릭합니다.
  
    ![Single Sign-On 구성][54]

9. **ID 공급 기업** 섹션에서 **ID 공급 기업 추가**를 클릭합니다. 

    ![Single Sign-On 구성][55]

10. **ID 공급자 설정** 페이지에서 다음 단계를 수행합니다.

    ![Single Sign-On 구성][56]

    a. **이름** 텍스트 상자에 구성할 고유한 이름을 입력합니다. 공백을 사용하지 마세요.

    b. Azure Portal에서 복사한 **Azure AD ID** 값을 **ID 공급 기업 발급자 텍스트 상자**에 붙여넣습니다.

    다. Azure Portal에서 복사한 **로그인 URL** 값을 **ID 공급 기업 로그인 URL** 텍스트 상자에 붙여넣습니다.

    d. Azure Portal에서 복사한 **로그인 URL** 값을 **ID 공급 기업 로그아웃 URL** 텍스트 상자에 붙여넣습니다.

    e. **Sign AuthN 요청**을 선택합니다.

    f. **AuthN 요청 보내기**로 **POST**를 선택합니다.

    g. **로그아웃 요청 보내기**로 **GET**을 선택합니다.

    h. **사용자 지정 특성 매핑** 섹션에서 **새 매핑 추가**를 클릭합니다.

    ![Single Sign-On 구성][62]

    i. Azure AD 클레임을 사용하여 매핑할 필드를 선택합니다. 이 예제의 **emailaddress** 클레임은 **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress** 값에 매핑됩니다. 이는 이메일 클레임에 대한 Azure AD의 기본 클레임입니다. 그런 다음, **저장**을 클릭합니다.

    ![Single Sign-On 구성][57]

    > [!NOTE]
    > 적절한 **사용자 ID**를 사용하여 Azure AD에서 DocuSign 사용자 매핑으로 사용자를 매핑합니다. 적절한 필드를 선택하고 조직 설정에 따라 적절한 값을 입력합니다.

    j. **ID 공급 기업 인증서** 섹션에서 **인증서 추가**를 클릭한 다음, Azure AD 포털에서 다운로드한 인증서를 업로드하고 **저장**을 클릭합니다.

    ![Single Sign-On 구성][58]

    k. **ID 공급 기업** 섹션에서 **작업**을 클릭한 다음, **엔드포인트**를 클릭합니다.

    ![Single Sign-On 구성][59]

    l. **DocuSign 관리자 포털**의 **SAML 2.0 엔드포인트 보기** 섹션에서 다음 단계를 수행합니다.

    ![Single Sign-On 구성][60]

    * **서비스 공급 기업 발급자 URL**을 복사한 다음, Azure Portal의 **기본 SAML 구성** 섹션에 있는 **식별자** 텍스트 상자에 붙여넣습니다.

    * **서비스 공급 기업 발급자 URL**을 복사한 다음, Azure Portal의 **기본 SAML 구성** 섹션에 있는 **로그온 URL** 텍스트 상자에 붙여넣습니다.

    * 페이지 맨 아래에 있는 **닫기**

### <a name="create-docusign-test-user"></a>DocuSign 테스트 사용자 만들기

이 섹션에서는 DocuSign에서 B.Simon이라는 사용자를 만듭니다. DocuSign은 기본적으로 사용하도록 설정되는 Just-In-Time 사용자 프로비저닝을 지원합니다. 이 섹션에 작업 항목이 없습니다. DocuSign에 사용자가 아직 없는 경우 인증 후에 새 사용자가 만들어집니다.

>[!Note]
>사용자를 수동으로 만들어야 하는 경우 [DocuSign 지원 팀](https://support.docusign.com/)에 문의해야 합니다.

## <a name="test-sso"></a>SSO 테스트 

이 섹션에서는 액세스 패널을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다.

액세스 패널에서 DocuSign 타일을 클릭하면 SSO를 설정한 DocuSign에 자동으로 로그인되어야 합니다. 액세스 패널에 대한 자세한 내용은 [액세스 패널 소개](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)를 참조하세요.

## <a name="additional-resources"></a>추가 리소스

- [Azure Active Directory와 SaaS 앱을 통합하는 방법에 대한 자습서 목록](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory를 사용한 애플리케이션 액세스 및 Single Sign-On이란?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory의 조건부 액세스란?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Azure AD로 DocuSign 사용해 보기](https://aad.portal.azure.com/)

<!--Image references-->

[50]: ./media/docusign-tutorial/tutorial_docusign_18.png
[51]: ./media/docusign-tutorial/tutorial_docusign_21.png
[52]: ./media/docusign-tutorial/tutorial_docusign_22.png
[53]: ./media/docusign-tutorial/tutorial_docusign_23.png
[54]: ./media/docusign-tutorial/tutorial_docusign_19.png
[55]: ./media/docusign-tutorial/tutorial_docusign_20.png
[56]: ./media/docusign-tutorial/tutorial_docusign_24.png
[57]: ./media/docusign-tutorial/tutorial_docusign_25.png
[58]: ./media/docusign-tutorial/tutorial_docusign_26.png
[59]: ./media/docusign-tutorial/tutorial_docusign_27.png
[60]: ./media/docusign-tutorial/tutorial_docusign_28.png
[61]: ./media/docusign-tutorial/tutorial_docusign_29.png
[62]: ./media/docusign-tutorial/tutorial_docusign_30.png