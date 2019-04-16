---
title: '자습서: Workday와 Azure Active Directory 통합 | Microsoft Docs'
description: Azure Active Directory와 Workday 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: e9da692e-4a65-4231-8ab3-bc9a87b10bca
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/04/2019
ms.author: jeedes
ms.openlocfilehash: 8451fd692409933803f5f8023f1e1161c3a97daf
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/08/2019
ms.locfileid: "59278534"
---
# <a name="tutorial-azure-active-directory-integration-with-workday"></a>자습서: Workday와 Azure Active Directory 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 Workday를 통합하는 방법에 대해 알아봅니다.
Workday를 Azure AD와 통합하면 다음과 같은 이점이 제공됩니다.

* Workday에 대한 액세스 권한이 있는 사용자를 Azure AD에서 제어할 수 있습니다.
* 사용자가 해당 Azure AD 계정으로 Workday에 자동으로 로그인(Single Sign-on)되도록 설정할 수 있습니다.
* 단일 중앙 위치인 Azure Portal에서 계정을 관리할 수 있습니다.

Azure AD와의 SaaS 앱 통합에 대한 자세한 내용은 [Azure Active Directory를 사용한 응용 프로그램 액세스 및 Single Sign-On](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)을 참조하세요.
Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/) 계정을 만듭니다.

## <a name="prerequisites"></a>필수 조건

Workday와 Azure AD 통합을 구성하려면 다음 항목이 필요합니다.

* Azure AD 구독 Azure AD 환경이 없으면 [여기](https://azure.microsoft.com/pricing/free-trial/)에서 1개월 평가판을 구할 수 있습니다.
* Workday Single Sign-On을 사용하도록 설정된 구독

## <a name="scenario-description"></a>시나리오 설명

이 자습서에서는 테스트 환경에서 Azure AD Single Sign-On을 구성하고 테스트합니다.

* Workday에서 **SP** 및 **IDP** 시작 SSO를 지원합니다.

## <a name="adding-workday-from-the-gallery"></a>갤러리에서 Workday 추가

Workday가 Azure AD에 통합되도록 구성하려면 갤러리에서 Workday를 관리되는 SaaS 앱 목록에 추가해야 합니다.

**갤러리에서 Workday를 추가하려면 다음 단계를 수행합니다.**

1. **[Azure Portal](https://portal.azure.com)** 의 왼쪽 탐색 창에서 **Azure Active Directory** 아이콘을 클릭합니다.

    ![Azure Active Directory 단추](common/select-azuread.png)

2. **엔터프라이즈 응용 프로그램**으로 이동한 다음, **모든 응용 프로그램** 옵션을 선택합니다.

    ![엔터프라이즈 애플리케이션 블레이드](common/enterprise-applications.png)

3. 새 애플리케이션을 추가하려면 대화 상자 맨 위 있는 **새 애플리케이션** 단추를 클릭합니다.

    ![새 애플리케이션 단추](common/add-new-app.png)

4. 검색 상자에 **Workday**를 입력하고 결과 패널에서 **Workday**를 선택한 후 **추가** 단추를 클릭하여 애플리케이션을 추가합니다.

     ![결과 목록의 Workday](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성 및 테스트

이 섹션에서는 **Britta Simon**이라는 테스트 사용자를 기반으로 Workday에서 Azure AD Single Sign-On을 구성하고 테스트합니다.
Single Sign-On이 작동하려면 Azure AD 사용자와 Workday의 관련 사용자 간에 연결 관계를 설정해야 합니다.

Workday에서 Azure AD Single Sign-On을 구성하고 테스트하려면 다음 구성 요소를 완료해야 합니다.

1. **[Azure AD Single Sign-On 구성](#configure-azure-ad-single-sign-on)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
2. **[Workday Single Sign-On 구성](#configure-workday-single-sign-on)** - 애플리케이션 쪽에서 Single Sign-On 설정을 구성합니다.
3. **[Azure AD 테스트 사용자 만들기](#create-an-azure-ad-test-user)** - Britta Simon으로 Azure AD Single Sign-On을 테스트하는 데 사용합니다.
4. **[Azure AD 테스트 사용자 할당](#assign-the-azure-ad-test-user)** - Britta Simon이 Azure AD Single Sign-on을 사용할 수 있도록 합니다.
5. **[Workday 테스트 사용자 만들기](#create-workday-test-user)** - Britta Simon의 Azure AD 표현과 연결되는 대응 사용자를 Workday에 만듭니다.
6. **[Single Sign-On 테스트](#test-single-sign-on)** - 구성이 작동하는지 여부를 확인합니다.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성

이 섹션에서는 Azure Portal에서 Azure AD Single Sign-On을 사용하도록 설정합니다.

Workday에서 Azure AD Single Sign-on을 구성하려면 다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com/)의 **Workday** 애플리케이션 통합 페이지에서 **Single Sign-On**을 선택합니다.

    ![Single Sign-On 구성 링크](common/select-sso.png)

2. **Single Sign-On 방법 선택** 대화 상자에서 **SAML/WS-Fed** 모드를 선택하여 Single Sign-On을 사용하도록 설정합니다.

    ![Single Sign-On 선택 모드](common/select-saml-option.png)

3. **SAML로 Single Sign-On 설정** 페이지에서 **편집** 아이콘을 클릭하여 **기본 SAML 구성** 대화 상자를 엽니다.

    ![기본 SAML 구성 편집](common/edit-urls.png)

4. **기본 SAML 구성** 섹션에서 다음 단계를 수행합니다.

    ![Workday 도메인 및 URL Single Sign-On 정보](common/sp-identifier.png)

    a. **로그온 URL** 텍스트 상자에 다음 패턴을 사용하여 URL을 입력합니다. `https:\//impl.workday.com/<tenant>/login-saml2.flex`

    b. **식별자** 텍스트 상자에서 다음 패턴을 사용하여 URL을 입력합니다. `https://www.workday.com`

5. **추가 URL 설정**을 클릭하고 다음 단계를 수행합니다.

    ![Workday 도메인 및 URL Single Sign-On 정보](./media/workday-tutorial/reply.png)

    **회신 URL** 텍스트 상자에 `https:\//impl.workday.com/<tenant>/login-saml.htmld` 패턴을 사용하여 URL을 입력합니다.

    > [!NOTE]
    > 이러한 값은 실제 값이 아닙니다. 실제 로그온 URL 및 회신 URL을 사용하여 이러한 값을 업데이트합니다. 회신 URL에 하위 도메인(예: www, wd2, wd3, wd3-impl, wd5, wd5-impl)이 있어야 합니다.
    > `http://www.myworkday.com`과 같이 사용해도 되지만 `http://myworkday.com`은 가능하지 않습니다. 이러한 값을 얻으려면 [Workday 클라이언트 지원 팀](https://www.workday.com/en-us/partners-services/services/support.html)에 문의하세요. Azure Portal의 **기본 SAML 구성** 섹션에 표시된 패턴을 참조할 수도 있습니다.

6. Workday 애플리케이션은 특정 서식에서 SAML 어설션을 예상하며 이는 SAML 토큰 특성 구성에 사용자 지정 특성 매핑을 추가합니다. 다음 스크린샷에서는 **nameidentifier**가 **user.userprincipalname**과 매핑되는 기본 특성 목록을 보여줍니다. Workday 애플리케이션에서는 **nameidentifier**가 **user.mail**, **UPN** 등과 매핑되므로 **편집** 아이콘을 클릭하여 특성 매핑을 편집하여 특성 매핑을 변경해야 합니다.

    ![이미지](common/edit-attribute.png)

    > [!NOTE]
    > 여기에서는 기본적으로 UPN(user.userprincipalname)을 사용하여 이름 ID를 매핑했습니다. SSO의 성공적인 작업을 위해 Workday 계정(이메일, UPN 등)에서 실제 사용자 ID를 사용하여 이름 ID를 매핑해야 합니다.

7. **SAML로 Single Sign-On 설정** 페이지의 **SAML 서명 인증서** 섹션에서 **다운로드**를 클릭하여 요구 사항에 따라 제공된 옵션에서 **인증서(Base64)** 를 다운로드한 다음, 컴퓨터에 저장합니다.

    ![인증서 다운로드 링크](common/certificatebase64.png)

8. **Workable 설정** 섹션에서 요구 사항에 따라 적절한 URL을 복사합니다.

    ![구성 URL 복사](common/copy-configuration-urls.png)

    a. 로그인 URL

    b. Azure AD 식별자

    다. 로그아웃 URL

### <a name="configure-workday-single-sign-on"></a>Workday Single Sign-On 구성

1. 다른 웹 브라우저 창에서 Workday 회사 사이트에 관리자로 로그인합니다.

2. **검색 상자**에서 홈페이지 왼쪽 상단의 이름 **테넌트 설정 편집 - 보안**으로 검색합니다.

    ![테넌트 보안 편집](./media/workday-tutorial/IC782925.png "테넌트 보안 편집")

3. **리디렉션 URL** 섹션에서 다음 단계를 수행합니다.

    ![리디렉션 URL](./media/workday-tutorial/IC7829581.png "리디렉션 URL")

    a. **행 추가**를 클릭합니다.

    b. **로그인 리디렉션 URL** 텍스트 상자 및 **모바일 리디렉션 URL** 텍스트 상자에서 Azure Portal의 **기본 SAML 구성** 섹션에 입력한 **로그온 URL**을 입력합니다.

    다. Azure Portal의 **Workday 설정** 섹션에서 **로그아웃 URL**을 복사한 다음, **로그아웃 리디렉션 URL** 텍스트 상자에 붙여넣습니다.

    d. **환경에 사용됨** 텍스트 상자에서 환경 이름을 선택합니다.  

   > [!NOTE]
   > 테넌트 URL의 값에 연결된 환경 특성의 값은:  
   > -Workday 테넌트 URL의 도메인 이름이 impl로 시작되는 경우(예: *https:\//impl.workday.com/\<tenant\>/login-saml2.flex*) **환경** 특성이 Implementation으로 설정되어있어야 합니다.  
   > -도메인 이름이 다르게 시작되면 일치하는 **환경** 값을 [Workday 클라이언트 지원 팀](https://www.workday.com/en-us/partners-services/services/support.html)에 문의해야 합니다.

4. **SAML 설정** 섹션에서 다음 단계를 수행합니다.

    ![SAML 설정](./media/workday-tutorial/IC782926.png "SAML 설정")

    a.  **SAML 인증 사용**을 선택합니다.

    b.  **행 추가**를 클릭합니다.

5. **SAML ID 공급자** 섹션에서 다음 단계를 수행합니다.

    ![SAML ID 공급자](./media/workday-tutorial/IC7829271.png "SAML ID 공급자")

    a. **ID 공급 기업 이름** 텍스트 상자에 공급 기업 이름(예: *SPInitiatedSSO*)을 입력합니다.

    b. Azure Portal의 **Workday 설정** 섹션에서 **Azure AD 식별자** 값을 복사한 다음, **발급자** 텍스트 상자에 붙여넣습니다.

    ![SAML ID 공급자](./media/workday-tutorial/IC7829272.png "SAML ID 공급자")

    다. Azure Portal의 **Workday 설정** 섹션에서 **로그아웃 URL** 값을 복사한 다음, **로그아웃 응답 URL** 텍스트 상자에 붙여넣습니다.

    d. Azure Portal의 **Workday 설정** 섹션에서 **로그인 URL** 값을 복사한 다음, **IdP SSO 서비스 URL** 텍스트 상자에 붙여넣습니다.

    e. **환경에 사용됨** 텍스트 상자에서 환경 이름을 선택합니다.

    f. **ID 공급자 공개 키 인증서**를 클릭한 다음 **만들기**를 클릭합니다.

    ![만들기](./media/workday-tutorial/IC782928.png "만들기")

    g. **x509 공개 키 만들기**를 클릭합니다.

    ![만들기](./media/workday-tutorial/IC782929.png "만들기")

6. **x509 공개 키 보기** 섹션에서 다음 단계를 수행합니다.

    ![x509 공개 키 보기](./media/workday-tutorial/IC782930.png "x509 공개 키 보기")

    a. **이름** 텍스트 상자에 인증서 이름(예: *PPE\_SP*)을 입력합니다.

    b. **유효 시작** 텍스트 상자에 인증서의 유효 시작 특성 값을 입력합니다.

    다.  **유효 만료** 텍스트 상자에 인증서의 유효 만료 특성 값을 입력합니다.

    > [!NOTE]
    > 유효 시작일과 유효 만료일은 다운로드한 인증서를 두 번 클릭하여 확인할 수 있습니다.  날짜는 **세부 정보** 탭에 나열됩니다.
    >
    >

    d.  메모장에서 Base-64로 인코딩된 인증서를 열고 콘텐츠를 복사합니다.

    e.  **인증서** 텍스트 상자에 클립보드의 내용을 붙여 넣습니다.

    f.  **확인**을 클릭합니다.

7. 다음 단계를 수행합니다.

    ![SSO 구성](./media/workday-tutorial/WorkdaySSOConfiguratio.png "SSO 구성")

    a.  **서비스 공급자 ID** 텍스트 상자에 **https://www.workday.com**을 입력합니다.

    b. **SP에서 시작한 인증 요청을 Deflate하지 않음**을 선택합니다.

    다. **인증 요청 서명 메서드**로 **SHA256**을 선택합니다.

    ![인증 요청 서명 메서드](./media/workday-tutorial/WorkdaySSOConfiguration.png "인증 요청 서명 메서드") 

    d. **확인**을 클릭합니다.

    ![확인](./media/workday-tutorial/IC782933.png "확인")

    > [!NOTE]
    > Single Sign-On을 올바르게 설정했는지 확인하세요. 잘못된 설정으로 Single Sign-On을 활성화한 경우 자격 증명을 사용하여 애플리케이션을 입력하고 잠글 수 없을 수도 있습니다. 이 경우 Workday는 사용자가 [Your Workday URL]/login.flex?redirect=n 형식으로 해당 일반 사용자 이름 및 암호를 사용하여 로그인할 수 있는 백업 로그인 url을 제공합니다.

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

### <a name="assign-the-azure-ad-test-user"></a>Azure AD 테스트 사용자 할당

이 섹션에서는 Britta Simon이 Azure Single Sign-On을 사용할 수 있도록 Workday에 대한 액세스 권한을 부여합니다.

1. Azure Portal에서 **엔터프라이즈 애플리케이션**을 선택한 다음, **모든 애플리케이션**과 **Workday**를 차례로 선택합니다.

    ![엔터프라이즈 애플리케이션 블레이드](common/enterprise-applications.png)

2. 애플리케이션 목록에서 **Workday**를 선택합니다.

    ![애플리케이션 목록의 Workday 링크](common/all-applications.png)

3. 왼쪽 메뉴에서 **사용자 및 그룹**을 선택합니다.

    !["사용자 및 그룹" 링크](common/users-groups-blade.png)

4. **사용자 추가** 단추를 클릭한 다음, **할당 추가** 대화 상자에서 **사용자 및 그룹**을 선택합니다.

    ![할당 추가 창](common/add-assign-user.png)

5. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **Britta Simon**을 선택하고 화면 아래쪽에서 **선택** 단추를 클릭합니다.

6. SAML 어설션 및 **역할 선택** 대화 상자에서 모든 역할 값이 필요한 경우 목록에서 적절한 사용자 역할을 선택한 다음, 화면 맨 아래에 있는 **선택** 단추를 클릭합니다.

7. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.

### <a name="create-workday-test-user"></a>Workday 테스트 사용자 만들기

이 섹션에서는 Workday에서 Britta Simon이라는 사용자를 만듭니다. Workday 플랫폼에서 사용자를 추가하려면 [Workday 클라이언트 지원 팀](https://www.workday.com/en-us/partners-services/services/support.html)에 문의하세요. Single Sign-On을 사용하려면 먼저 사용자를 만들고 활성화해야 합니다.

### <a name="test-single-sign-on"></a>Single Sign-On 테스트 

이 섹션에서는 액세스 패널을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다.

액세스 패널에서 Workday 타일을 클릭하면 SSO를 설정한 Workday에 자동으로 로그인됩니다. 액세스 패널에 대한 자세한 내용은 [액세스 패널 소개](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)를 참조하세요.

## <a name="additional-resources"></a>추가 리소스

- [Azure Active Directory와 SaaS Apps를 통합하는 방법에 대한 자습서 목록](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory로 애플리케이션 액세스 및 Single Sign-On이란 무엇입니까?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory의 조건부 액세스란?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

