---
title: '자습서: DocuSign과 Azure Active Directory 통합 | Microsoft Docs'
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
ms.date: 04/01/2019
ms.author: jeedes
ms.openlocfilehash: 35fc4e855ef53bd7e667fc8de7146916b4d05acb
ms.sourcegitcommit: 6f043a4da4454d5cb673377bb6c4ddd0ed30672d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/08/2019
ms.locfileid: "65407074"
---
# <a name="tutorial-azure-active-directory-integration-with-docusign"></a>자습서: DocuSign와 Azure Active Directory 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 DocuSign을 통합하는 방법에 대해 알아봅니다.
DocuSign을 Azure AD와 통합하면 다음과 같은 이점이 제공됩니다.

* DocuSign에 대한 액세스 권한이 있는 사용자를 Azure AD에서 제어할 수 있습니다.
* 사용자가 자신의 Azure AD 계정으로 DocuSign에 자동으로 로그인(Single Sign-On)되도록 설정할 수 있습니다.
* 단일 중앙 위치인 Azure Portal에서 계정을 관리할 수 있습니다.

Azure AD와의 SaaS 앱 통합에 대한 자세한 내용은 [Azure Active Directory를 사용한 응용 프로그램 액세스 및 Single Sign-On](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)을 참조하세요.
Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/) 계정을 만듭니다.

## <a name="prerequisites"></a>필수 조건

DocuSign과 Azure AD 통합을 구성하려면 다음 항목이 필요합니다.

* Azure AD 구독 Azure AD 환경이 없으면 [체험 계정](https://azure.microsoft.com/free/)을 얻을 수 있습니다.
* DocuSign Single Sign-On이 설정된 구독

## <a name="scenario-description"></a>시나리오 설명

이 자습서에서는 테스트 환경에서 Azure AD Single Sign-On을 구성하고 테스트합니다.

* DocuSign은 **SP** 시작 SSO를 지원합니다.

* DocuSign은 **Just In Time** 사용자 프로비저닝을 지원합니다.

## <a name="adding-docusign-from-the-gallery"></a>갤러리에서 DocuSign 추가

DocuSign의 Azure AD 통합을 구성하려면 갤러리의 DocuSign을 관리되는 SaaS 앱 목록에 추가해야 합니다.

**갤러리에서 DocuSign을 추가하려면 다음 단계를 수행합니다.**

1. **[Azure Portal](https://portal.azure.com)** 의 왼쪽 탐색 창에서 **Azure Active Directory** 아이콘을 클릭합니다.

    ![Azure Active Directory 단추](common/select-azuread.png)

2. **엔터프라이즈 응용 프로그램**으로 이동한 다음, **모든 응용 프로그램** 옵션을 선택합니다.

    ![엔터프라이즈 애플리케이션 블레이드](common/enterprise-applications.png)

3. 새 애플리케이션을 추가하려면 대화 상자 맨 위 있는 **새 애플리케이션** 단추를 클릭합니다.

    ![새 애플리케이션 단추](common/add-new-app.png)

4. 검색 상자에 **DocuSign**을 입력하고 결과 패널에서 **DocuSign**을 선택한 다음, **추가** 단추를 클릭하여 애플리케이션을 추가합니다.

     ![결과 목록의 DocuSign](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성 및 테스트

이 섹션에서는 **Britta Simon**이라는 테스트 사용자를 기반으로 DocuSign에서 Azure AD Single Sign-On을 구성하고 테스트합니다.
Single Sign-On이 작동하려면 Azure AD 사용자와 DocuSign의 관련 사용자 간에 연결 관계를 설정해야 합니다.

DocuSign에서 Azure AD Single Sign-On을 구성하고 테스트하려면 다음 구성 요소를 완료해야 합니다.

1. **[Azure AD Single Sign-On 구성](#configure-azure-ad-single-sign-on)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
2. **[DocuSign Single Sign-On 구성](#configure-docusign-single-sign-on)** - 애플리케이션 쪽에서 Single Sign-On 설정을 구성합니다.
3. **[Azure AD 테스트 사용자 만들기](#create-an-azure-ad-test-user)** - Britta Simon으로 Azure AD Single Sign-On을 테스트하는 데 사용합니다.
4. **[Azure AD 테스트 사용자 할당](#assign-the-azure-ad-test-user)** - Britta Simon이 Azure AD Single Sign-on을 사용할 수 있도록 합니다.
5. **[DocuSign 테스트 사용자 만들기](#create-docusign-test-user)** - Britta Simon의 Azure AD 표현과 연결된 해당 사용자를 DocuSign에 만듭니다.
6. **[Single Sign-On 테스트](#test-single-sign-on)** - 구성이 작동하는지 여부를 확인합니다.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성

이 섹션에서는 Azure Portal에서 Azure AD Single Sign-On을 사용하도록 설정합니다.

DocuSign에서 Azure AD Single Sign-On을 구성하려면 다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com/)의 **DocuSign** 애플리케이션 통합 페이지에서 **Single Sign-On**을 선택합니다.

    ![Single Sign-On 구성 링크](common/select-sso.png)

2. **Single Sign-On 방법 선택** 대화 상자에서 **SAML/WS-Fed** 모드를 선택하여 Single Sign-On을 사용하도록 설정합니다.

    ![Single Sign-On 선택 모드](common/select-saml-option.png)

3. **SAML로 Single Sign-On 설정** 페이지에서 **편집** 아이콘을 클릭하여 **기본 SAML 구성** 대화 상자를 엽니다.

    ![기본 SAML 구성 편집](common/edit-urls.png)

4. **기본 SAML 구성** 섹션에서 다음 단계를 수행합니다.

    ![DocuSign 도메인 및 URL Single Sign-On 정보](common/sp-identifier.png)

    a. **로그온 URL** 텍스트 상자에서 `https://<subdomain>.docusign.com/organizations/<OrganizationID>/saml2/login/sp/<IDPID>` 패턴을 사용하는 URL을 입력합니다.

    b. **식별자(엔터티 ID)** 텍스트 상자에서 `https://<subdomain>.docusign.com/organizations/<OrganizationID>/saml2` 패턴을 사용하는 URL을 입력합니다.

    > [!NOTE]
    > 이러한 값은 실제 값이 아닙니다. 자습서 뒷부분에 있는 **SAML 2.0 엔드포인트 보기** 섹션에 설명된 실제 로그온 URL 및 식별자로 값을 업데이트합니다.

5. **SAML로 Single Sign-On 설정** 페이지의 **SAML 서명 인증서** 섹션에서 **다운로드**를 클릭하여 요구 사항에 따라 제공된 옵션에서 **인증서(Base64)** 를 다운로드한 다음, 컴퓨터에 저장합니다.

    ![인증서 다운로드 링크](common/certificatebase64.png)

6. **DocuSign 설정** 섹션에서 요구 사항에 따라 적절한 URL을 복사합니다.

    ![구성 URL 복사](common/copy-configuration-urls.png)

    a. 로그인 URL

    b. Azure AD 식별자

    다. 로그아웃 URL

### <a name="configure-docusign-single-sign-on"></a>DocuSign Single Sign-On 구성

1. 다른 웹 브라우저 창에서 **DocuSign 관리 포털**에 관리자로 로그인합니다.

2. 오른쪽 위 모서리에서 프로필 **로고**를 클릭한 다음, **관리자로 이동**을 클릭합니다.
  
    ![Single Sign-On 구성][51]

3. 도메인 솔루션 페이지에서 **도메인**을 클릭합니다.

    ![Single Sign-On 구성][50]

4. **도메인** 섹션에서 **도메인 클레임**을 클릭합니다.

    ![Single Sign-On 구성][52]

5. **도메인 클레임** 대화 상자의 **도메인 이름** 텍스트 상자에 회사 도메인을 입력한 다음, **클레임**을 클릭합니다. 도메인을 확인하고 상태가 활성인지 확인합니다.

    ![Single Sign-On 구성][53]

6. 도메인 솔루션 페이지에서 **ID 공급 기업**을 클릭합니다.
  
    ![Single Sign-On 구성][54]

7. **ID 공급 기업** 섹션에서 **ID 공급 기업 추가**를 클릭합니다. 

    ![Single Sign-On 구성][55]

8. **ID 공급자 설정** 페이지에서 다음 단계를 수행합니다.

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

    i. Azure AD 클레임을 사용하여 매핑할 필드를 선택합니다. 이 예제의 **emailaddress** 클레임은 **https://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress** 값에 매핑됩니다. 이는 이메일 클레임에 대한 Azure AD의 기본 클레임입니다. 그런 다음, **저장**을 클릭합니다.

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

### <a name="create-an-azure-ad-test-user"></a>Azure AD 테스트 사용자 만들기 

이 섹션의 목적은 Azure Portal에서 Britta Simon이라는 테스트 사용자를 만드는 것입니다.

1. Azure Portal의 왼쪽 창에서 **Azure Active Directory**, **사용자**를 차례로 선택하고 **모든 사용자**를 선택합니다.

    !["사용자 및 그룹" 및 "모든 사용자" 링크](common/users.png)

2. 화면 위쪽에서 **새 사용자**를 선택합니다.

    ![새 사용자 단추](common/new-user.png)

3. 사용자 속성에서 다음 단계를 수행합니다.

    ![사용자 대화 상자](common/user-properties.png)

    a. **이름** 필드에 **BrittaSimon**을 입력합니다.
  
    b. **사용자 이름** 필드에 brittasimon@yourcompanydomain.extension을 입력합니다. 예를 들어 BrittaSimon@contoso.com

    c. **암호 표시** 확인란을 선택한 다음, [암호] 상자에 표시된 값을 적어둡니다.

    d. **만들기**를 클릭합니다.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD 테스트 사용자 할당

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 Britta Simon에게 DocuSign에 대한 액세스 권한을 부여합니다.

1. Azure Portal에서 **엔터프라이즈 애플리케이션**, **모든 애플리케이션**, **DocuSign**을 차례로 선택합니다.

    ![엔터프라이즈 애플리케이션 블레이드](common/enterprise-applications.png)

2. 애플리케이션 목록에서 **DocuSign**을 선택합니다.

    ![애플리케이션 목록의 DocuSign 링크](common/all-applications.png)

3. 왼쪽 메뉴에서 **사용자 및 그룹**을 선택합니다.

    !["사용자 및 그룹" 링크](common/users-groups-blade.png)

4. **사용자 추가** 단추를 클릭한 다음, **할당 추가** 대화 상자에서 **사용자 및 그룹**을 선택합니다.

    ![할당 추가 창](common/add-assign-user.png)

5. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **Britta Simon**을 선택하고 화면 아래쪽에서 **선택** 단추를 클릭합니다.

6. SAML 어설션 및 **역할 선택** 대화 상자에서 모든 역할 값이 필요한 경우 목록에서 적절한 사용자 역할을 선택한 다음, 화면 맨 아래에 있는 **선택** 단추를 클릭합니다.

7. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.

### <a name="create-docusign-test-user"></a>DocuSign 테스트 사용자 만들기

이 섹션에서는 DocuSign에서 Britta Simon이라는 사용자를 만듭니다. DocuSign은 기본적으로 사용하도록 설정되는 Just-In-Time 사용자 프로비저닝을 지원합니다. 이 섹션에 작업 항목이 없습니다. DocuSign에 사용자가 아직 없는 경우 인증 후에 새 사용자가 만들어집니다.

>[!Note]
>사용자를 수동으로 만들어야 하는 경우 [DocuSign 지원 팀](https://support.docusign.com/)에 문의해야 합니다.

### <a name="test-single-sign-on"></a>Single Sign-On 테스트 

이 섹션에서는 액세스 패널을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다.

액세스 패널에서 DocuSign 타일을 클릭하면 SSO를 설정한 DocuSign에 자동으로 로그인되어야 합니다. 액세스 패널에 대한 자세한 내용은 [액세스 패널 소개](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)를 참조하세요.

## <a name="additional-resources"></a>추가 리소스

- [Azure Active Directory와 SaaS Apps를 통합하는 방법에 대한 자습서 목록](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory로 애플리케이션 액세스 및 Single Sign-On을 구현하는 방법](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory의 조건부 액세스란?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

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
