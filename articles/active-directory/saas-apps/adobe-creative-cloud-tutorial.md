---
title: '자습서: Adobe Creative Cloud와 Azure Active Directory 통합 | Microsoft Docs'
description: Azure Active Directory와 Adobe Creative Cloud 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: c199073f-02ce-45c2-b515-8285d4bbbca2
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/19/2018
ms.author: jeedes
ms.openlocfilehash: 8000208dfe15744c891bbf27ad8f9e69f44ef4dc
ms.sourcegitcommit: 9f87a992c77bf8e3927486f8d7d1ca46aa13e849
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/28/2018
ms.locfileid: "53807791"
---
# <a name="tutorial-azure-active-directory-integration-with-adobe-creative-cloud"></a>자습서: Adobe Creative Cloud와 Azure Active Directory 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 Adobe Creative Cloud를 통합하는 방법에 대해 알아봅니다.
Adobe Creative Cloud를 Azure AD와 통합하면 다음과 같은 이점이 제공됩니다.

* Azure AD에서 Adobe Creative Cloud에 액세스할 수 있는 사용자를 제어할 수 있습니다.
* 사용자가 해당 Azure AD 계정으로 Adobe Creative Cloud에 자동으로 로그인(Single Sign-On)되도록 설정할 수 있습니다.
* 단일 중앙 위치인 Azure Portal에서 계정을 관리할 수 있습니다.

Azure AD와의 SaaS 앱 통합에 대한 자세한 내용은 [Azure Active Directory를 사용한 응용 프로그램 액세스 및 Single Sign-On](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)을 참조하세요.
Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/) 계정을 만듭니다.

## <a name="prerequisites"></a>필수 조건

Adobe Creative Cloud와 Azure AD의 통합을 구성하려면 다음 항목이 필요합니다.

* Azure AD 구독 Azure AD 환경이 없으면 [여기](https://azure.microsoft.com/pricing/free-trial/)에서 1개월 평가판을 구할 수 있습니다.
* Adobe Creative Cloud Single Sign-On을 사용하도록 설정된 구독

## <a name="scenario-description"></a>시나리오 설명

이 자습서에서는 테스트 환경에서 Azure AD Single Sign-On을 구성하고 테스트합니다.

* Adobe Creative Cloud에서 **SP 및 IDP** 시작 SSO를 지원합니다.

## <a name="adding-adobe-creative-cloud-from-the-gallery"></a>갤러리에서 Adobe Creative Cloud 추가

Adobe Creative Cloud가 Azure AD에 통합되도록 구성하려면 Adobe Creative Cloud를 갤러리에서 관리되는 SaaS 앱 목록에 추가해야 합니다.

**갤러리에서 Adobe Creative Cloud를 추가하려면 다음 단계를 수행합니다.**

1. **[Azure Portal](https://portal.azure.com)** 의 왼쪽 탐색 창에서 **Azure Active Directory** 아이콘을 클릭합니다.

    ![Azure Active Directory 단추](common/select-azuread.png)

2. **엔터프라이즈 응용 프로그램**으로 이동한 다음, **모든 응용 프로그램** 옵션을 선택합니다.

    ![엔터프라이즈 애플리케이션 블레이드](common/enterprise-applications.png)

3. 새 애플리케이션을 추가하려면 대화 상자 맨 위 있는 **새 애플리케이션** 단추를 클릭합니다.

    ![새 애플리케이션 단추](common/add-new-app.png)

4. 검색 상자에서 **Adobe Creative Cloud**를 입력하고, 결과 패널에서 **Adobe Creative Cloud**를 선택한 다음, **추가** 단추를 클릭하여 애플리케이션을 추가합니다.

     ![결과 목록의 Adobe Creative Cloud](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성 및 테스트

이 섹션에서는 **Britta Simon**이라는 테스트 사용자를 기반으로 하여 Adobe Creative Cloud에서 Azure AD Single Sign-On을 구성하고 테스트합니다.
Single Sign-On이 작동하려면 Azure AD 사용자와 Adobe Creative Cloud의 관련 사용자 간에 연결 관계를 설정해야 합니다.

Adobe Creative Cloud에서 Azure AD Single Sign-On을 구성하고 테스트하려면 다음 구성 요소를 완료해야 합니다.

1. **[Azure AD Single Sign-On 구성](#configure-azure-ad-single-sign-on)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
2. **[Adobe Creative Cloud Single Sign-On 구성](#configure-adobe-creative-cloud-single-sign-on)** - 애플리케이션 쪽에서 Single Sign-On 설정을 구성합니다.
3. **[Azure AD 테스트 사용자 만들기](#create-an-azure-ad-test-user)** - Britta Simon으로 Azure AD Single Sign-On을 테스트하는 데 사용합니다.
4. **[Azure AD 테스트 사용자 할당](#assign-the-azure-ad-test-user)** - Britta Simon이 Azure AD Single Sign-on을 사용할 수 있도록 합니다.
5. **[Adobe Creative Cloud 테스트 사용자 만들기](#create-adobe-creative-cloud-test-user)** - Britta Simon의 Azure AD 표현과 연결되는 해당 사용자를 Adobe Creative Cloud에 만듭니다.
6. **[Single Sign-On 테스트](#test-single-sign-on)** - 구성이 작동하는지 여부를 확인합니다.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성

이 섹션에서는 Azure Portal에서 Azure AD Single Sign-On을 사용하도록 설정합니다.

Adobe Creative Cloud에서 Azure AD Single Sign-On을 구성하려면 다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com/)의 **Adobe Creative Cloud** 애플리케이션 통합 페이지에서 **Single Sign-On**을 선택합니다.

    ![Single Sign-On 구성 링크](common/select-sso.png)

2. **Single Sign-On 방법 선택** 대화 상자에서 **SAML/WS-Fed** 모드를 선택하여 Single Sign-On을 사용하도록 설정합니다.

    ![Single Sign-On 선택 모드](common/select-saml-option.png)

3. **SAML로 Single Sign-On 설정** 페이지에서 **편집** 아이콘을 클릭하여 **기본 SAML 구성** 대화 상자를 엽니다.

    ![기본 SAML 구성 편집](common/edit-urls.png)

4. **IDP** 시작 모드에서 구성하려면 **기본 SAML 구성** 섹션에서 다음 단계를 수행합니다.

    ![Adobe Creative Cloud 도메인 및 URL Single Sign-On 정보](common/idp-intiated.png)

    a. **식별자** 텍스트 상자에서 `https://www.okta.com/saml2/service-provider/<token>` 패턴을 사용하여 URL을 입력합니다.

    b. **회신 URL** 텍스트 상자에서 `https://<company name>.okta.com/auth/saml20/accauthlinktest` 패턴을 사용하여 URL을 입력합니다.

    > [!NOTE]
    > 이러한 값은 실제 값이 아닙니다. 실제 식별자, 회신 URL로 해당 값을 업데이트합니다. 이러한 값을 얻으려면 [Adobe Creative Cloud 클라이언트 지원 팀](https://www.adobe.com/au/creativecloud/business/teams/plans.html)에 문의하세요. Azure Portal의 **기본 SAML 구성** 섹션에 표시된 패턴을 참조할 수도 있습니다.

5. **SP** 시작 모드에서 애플리케이션을 구성하려면 **추가 URL 설정**을 클릭하고 다음 단계를 수행합니다.

    **로그온 URL** 텍스트 상자에서 `https://adobe.com` 값을 입력합니다.

    ![Adobe Creative Cloud 도메인 및 URL Single Sign-On 정보](common/metadata-upload-additional-signon.png)

6. Adobe Creative Cloud 애플리케이션에는 특정 형식의 SAML 어설션이 필요합니다. 이 애플리케이션에 대해 다음 클레임을 구성합니다. 응용 프로그램 통합 페이지의 **사용자 특성** 섹션에서 이러한 특성의 값을 관리할 수 있습니다. **SAML로 Single Sign-On 설정** 페이지에서 **편집** 단추를 클릭하여 **사용자 특성** 대화 상자를 엽니다.

    ![이미지](common/edit-attribute.png)

7. **사용자 특성** 대화 상자의 **사용자 클레임** 섹션에서 위의 이미지에 표시된 것과 같이 SAML 토큰 특성을 구성하고 다음 단계를 수행합니다.
 
    | 이름 | 원본 특성|
    |----- | --------- |
    | FirstName | user.givenname |
    | LastName | user.surname |
    | Email | user.mail

    a. **새 클레임 추가**를 클릭하여 **사용자 클레임 관리** 대화 상자를 엽니다.

    ![이미지](common/new-save-attribute.png)

    ![이미지](common/new-attribute-details.png)

    b. **이름** 텍스트 상자에서 해당 행에 표시된 특성 이름을 입력합니다.

    다. **네임스페이스**를 비워 둡니다.

    d. 원본을 **특성**으로 선택합니다.

    e. **원본 특성** 목록에서 해당 행에 표시된 특성 값을 입력합니다.

    f. **확인**을 클릭합니다.

    g. **저장**을 클릭합니다.

    > [!NOTE]
    > SAML 응답에서 이메일 클레임 값이 입력되려면 사용자에게 유효한 Office 365 ExO 라이선스가 있어야 합니다.

8. **SAML로 Single Sign-On 설정** 페이지의 **SAML 서명 인증서** 섹션에서 **다운로드**를 클릭하여 요구 사항에 따라 제공된 옵션에서 **인증서(Base64)** 를 다운로드한 다음, 컴퓨터에 저장합니다.

    ![인증서 다운로드 링크](common/certificatebase64.png)

9. **Adobe Creative Cloud 설정** 섹션에서 요구 사항에 따라 적절한 URL을 복사합니다.

    ![구성 URL 복사](common/copy-configuration-urls.png)

    a. 로그인 URL

    b. Azure AD 식별자

    다. 로그아웃 URL

### <a name="configure-adobe-creative-cloud-single-sign-on"></a>Adobe Creative Cloud Single Sign-On 구성

1. 다른 웹 브라우저 창에서 [Adobe 관리 콘솔](https://adminconsole.adobe.com)에 관리자 권한으로 로그인합니다.

2. 위쪽 탐색 모음에서 **설정**으로 이동하고 **ID**를 선택합니다. 도메인 목록을 엽니다. 도메인에 대한 **구성** 링크를 클릭합니다. 그런 다음 **Single Sign On 구성 필요** 섹션에서 다음 단계를 수행합니다. 자세한 내용은 [도메인 설정](https://helpx.adobe.com/enterprise/using/set-up-domain.html)을 참조하세요.

    ![설정](https://helpx.adobe.com/content/dam/help/en/enterprise/using/configure-microsoft-azure-with-adobe-sso/_jcr_content/main-pars/procedure_719391630/proc_par/step_3/step_par/image/edit-sso-configuration.png "설정")

    a. **찾아보기**를 클릭하여 Azure AD에서 다운로드한 인증서를 **IDP 인증서**에 업로드합니다.

    b. **IDP 발급자** 텍스트 상자에 Azure Portal의 **로그온 구성** 섹션에서 복사한 **SAML 엔터티 ID**의 값을 입력합니다.

    다. **IDP 로그인 URL** 텍스트 상자에 Azure Portal의 **로그온 구성** 섹션에서 복사한 **SAML SSO 서비스 URL**의 값을 입력합니다.

    d. **HTTP - 리디렉션**을 **IDP 바인딩**으로 선택합니다.

    e. **전자 메일 주소**를 **사용자 로그인 설정**으로 선택합니다.

    f. **저장** 단추를 클릭합니다.

3. 이제 대시보드에 XML **"메타데이터 다운로드"** 파일이 표시됩니다. 여기에는 Adobe의 EntityDescriptor URL과 AssertionConsumerService URL이 포함되어 있습니다. Azure AD 애플리케이션에서 파일을 열어서 구성하십시오.

    ![앱 쪽에서 Single Sign-On 구성](./media/adobe-creative-cloud-tutorial/tutorial_adobe-creative-cloud_003.png)

    a. **앱 설정 구성** 대화 상자에서 **식별자**에 대해 Adobe가 제공한 EntityDescriptor 값을 사용합니다.

    b. **앱 설정 구성** 대화 상자에서 **회신 URL**에 대해 Adobe가 제공한 AssertionConsumerService 값을 사용합니다.

### <a name="create-an-azure-ad-test-user"></a>Azure AD 테스트 사용자 만들기

이 섹션의 목적은 Azure Portal에서 Britta Simon이라는 테스트 사용자를 만드는 것입니다.

1. Azure Portal의 왼쪽 창에서 **Azure Active Directory**, **사용자**를 차례로 선택하고 **모든 사용자**를 선택합니다.

    !["사용자 및 그룹" 및 "모든 사용자" 링크](common/users.png)

2. 화면 위쪽에서 **새 사용자**를 선택합니다.

    ![새 사용자 단추](common/new-user.png)

3. 사용자 속성에서 다음 단계를 수행합니다.

    ![사용자 대화 상자](common/user-properties.png)

    a. **이름** 필드에 **BrittaSimon**을 입력합니다.
  
    b. **사용자 이름** 필드에 **brittasimon@yourcompanydomain.extension**을 입력합니다.  
    예를 들어 BrittaSimon@contoso.com

    다. **암호 표시** 확인란을 선택한 다음, [암호] 상자에 표시된 값을 적어둡니다.

    d. **만들기**를 클릭합니다.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD 테스트 사용자 할당

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 Britta Simon에게 Adobe Creative Cloud에 대한 액세스 권한을 부여합니다.

1. Azure Portal에서 **엔터프라이즈 애플리케이션**, **모든 애플리케이션**, **Adobe Creative Cloud**를 차례로 선택합니다.

    ![엔터프라이즈 애플리케이션 블레이드](common/enterprise-applications.png)

2. 애플리케이션 목록에서 **Adobe Creative Cloud**를 선택합니다.

    ![애플리케이션 목록의 Adobe Creative Cloud 링크](common/all-applications.png)

3. 왼쪽 메뉴에서 **사용자 및 그룹**을 선택합니다.

    !["사용자 및 그룹" 링크](common/users-groups-blade.png)

4. **사용자 추가** 단추를 클릭한 다음, **할당 추가** 대화 상자에서 **사용자 및 그룹**을 선택합니다.

    ![할당 추가 창](common/add-assign-user.png)

5. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **Britta Simon**을 선택하고 화면 아래쪽에서 **선택** 단추를 클릭합니다.

6. SAML 어설션 및 **역할 선택** 대화 상자에서 모든 역할 값이 필요한 경우 목록에서 적절한 사용자 역할을 선택한 다음, 화면 맨 아래에 있는 **선택** 단추를 클릭합니다.

7. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.

### <a name="create-adobe-creative-cloud-test-user"></a>Adobe Creative Cloud 테스트 사용자 만들기

Azure AD 사용자가 Adobe Creative Cloud에 로그인할 수 있도록 하려면 Adobe Creative Cloud에 프로비전되어야 합니다. Adobe Creative Cloud의 경우 프로비전이 수동 작업입니다.

### <a name="to-provision-a-user-accounts-perform-the-following-steps"></a>사용자 계정을 프로비저닝하려면 다음 단계를 수행합니다.

1. [Adobe 관리 콘솔](https://adminconsole.adobe.com) 사이트에 관리자 권한으로 로그인합니다.

2. Adobe의 콘솔에서 사용자를 페더레이션 ID로 추가하고 제품 프로필에 할당합니다. 사용자를 추가하는 방법에 대한 자세한 내용은 [Adobe 관리 콘솔에서 사용자 추가](https://helpx.adobe.com/enterprise/using/users.html#Addusers)를 참조하세요. 

3. 이 시점에서 이메일 주소/UPN을 Adobe 로그인 양식에 입력하고 Tab 키를 눌러 다음과 같이 Azure AD에 다시 페더레이션되어야 합니다.
    * 웹 액세스: www.adobe.com > 로그인
    * 바탕 화면 앱 유틸리티 내에서 > 로그인
    * 애플리케이션 내에서 &gt; 도움말 &gt; 로그인

### <a name="test-single-sign-on"></a>Single Sign-On 테스트

이 섹션에서는 액세스 패널을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다.

액세스 패널에서 Adobe Creative Cloud 타일을 클릭하면 SSO를 설정한 Adobe Creative Cloud에 자동으로 로그인됩니다. 액세스 패널에 대한 자세한 내용은 [액세스 패널 소개](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)를 참조하세요.

## <a name="additional-resources"></a>추가 리소스

- [Azure Active Directory와 SaaS 앱을 통합하는 방법에 대한 자습서 목록](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory를 사용한 애플리케이션 액세스 및 Single Sign-On이란?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory의 조건부 액세스란?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
  
- [도메인 설정(adobe.com)](https://helpx.adobe.com/enterprise/using/set-up-domain.html)
  
- [Adobe SSO에서 사용할 Azure 구성(adobe.com)](https://helpx.adobe.com/enterprise/kb/configure-microsoft-azure-with-adobe-sso.html)