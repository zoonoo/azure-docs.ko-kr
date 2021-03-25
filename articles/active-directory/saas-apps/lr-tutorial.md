---
title: LoginRadius와 Azure Active Directory 통합 | Microsoft Docs
description: Azure Active Directory 및 LoginRadius 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/14/2019
ms.author: jeedes
ms.openlocfilehash: 832c08123904b9fb889231faa86c1308704a2581
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "97606422"
---
# <a name="tutorial-azure-active-directory-integration-with-loginradius"></a>자습서: LoginRadius와 Azure Active Directory 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 LoginRadius를 통합하는 방법에 대해 알아봅니다.

LoginRadius를 Azure AD와 통합하면 다음과 같은 이점이 제공됩니다.

* LoginRadius에 대한 액세스 권한이 있는 사용자를 Azure AD에서 제어할 수 있습니다.
* 사용자가 해당 Azure AD 계정으로 LoginRadius에 자동으로 로그인(Single Sign-On)되도록 설정할 수 있습니다.
* 단일 중앙 위치인 Azure Portal에서 계정을 관리할 수 있습니다.

Azure AD와의 SaaS 앱 연결에 대한 자세한 내용은 [Azure Active Directory를 사용한 애플리케이션 액세스 및 Single Sign-On](../manage-apps/what-is-single-sign-on.md)을 참조하세요.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/) 계정을 만듭니다.

## <a name="prerequisites"></a>사전 요구 사항

LoginRadius와 Azure AD 통합을 구성하려면 다음 항목이 필요합니다.

* Azure AD 구독 Azure AD 환경이 없으면 [체험 계정](https://azure.microsoft.com/free/)을 얻을 수 있습니다.
* LoginRadius Single Sign-On이 설정된 구독

## <a name="scenario-description"></a>시나리오 설명

이 자습서에서는 테스트 환경에서 Azure AD Single Sign-On을 구성하고 테스트합니다.

* LoginRadius는 **SP** 시작 SSO를 지원합니다.

## <a name="adding-loginradius-from-the-gallery"></a>갤러리에서 LoginRadius 추가

LoginRadius의 Azure AD 통합을 구성하려면 갤러리의 LoginRadius를 관리되는 SaaS 앱 목록에 추가해야 합니다.

**갤러리에서 LoginRadius를 추가하려면 다음을 수행합니다.**

1. **[Azure Portal](https://portal.azure.com)** 의 왼쪽 탐색 패널에서 **Azure Active Directory** 아이콘을 선택합니다.

    ![Azure Active Directory 단추](common/select-azuread.png)

2. **엔터프라이즈 애플리케이션** 으로 이동한 다음, **모든 애플리케이션** 옵션을 선택합니다.

    ![엔터프라이즈 애플리케이션 블레이드](common/enterprise-applications.png)

3. 새 애플리케이션을 추가하려면 **새 애플리케이션** 단추를 선택합니다.

    ![새 애플리케이션 단추](common/add-new-app.png)

4. 검색 상자에 **LoginRadius** 를 입력하고, 결과 패널에서 **LoginRadius** 를 선택한 다음, **추가** 단추를 선택하여 애플리케이션을 추가합니다.

    ![결과 목록의 LoginRadius](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성 및 테스트

이 섹션에서는 **Britta Simon** 이라는 테스트 사용자를 기반으로 LoginRadius에서 Azure AD Single Sign-On을 구성하고 테스트합니다.
Single Sign-On이 작동하려면 Azure AD 사용자와 LoginRadius의 관련 사용자 간에 연결 관계를 설정해야 합니다.

LoginRadius에서 Azure AD Single Sign-On을 구성하고 테스트하려면 다음 구성 요소를 완료해야 합니다.

1. **[Azure AD Single Sign-On 구성](#configure-azure-ad-single-sign-on)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
2. **[LoginRadius Single Sign-On 구성](#configure-loginradius-single-sign-on)** - 애플리케이션 쪽에서 Single Sign-On 설정을 구성합니다.
3. **[Azure AD 테스트 사용자 만들기](#create-an-azure-ad-test-user)** - Britta Simon으로 Azure AD Single Sign-On을 테스트하는 데 사용합니다.
4. **[Azure AD 테스트 사용자 할당](#assign-the-azure-ad-test-user)** - Britta Simon이 Azure AD Single Sign-on을 사용할 수 있도록 합니다.
5. **[LoginRadius 테스트 사용자 만들기](#create-loginradius-test-user)** - Britta Simon의 Azure AD 표현과 연결된 해당 사용자를 LoginRadius에 만듭니다.
6. **[Single Sign-On 테스트](#test-single-sign-on)** - 구성이 작동하는지 여부를 확인합니다.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성

이 섹션에서는 Azure Portal에서 Azure AD Single Sign-On을 사용하도록 설정합니다.

LoginRadius에서 Azure AD Single Sign-On을 구성하려면 다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com/)의 **LoginRadius** 애플리케이션 통합 페이지에서 **Single Sign-On** 을 선택합니다.

    ![Single Sign-On 구성 링크](common/select-sso.png)

2. **Single Sign-On 방법 선택** 창에서 **SAML/WS-Fed** 모드를 선택하여 Single Sign-On을 사용하도록 설정합니다.

    ![Single Sign-On 선택 모드](common/select-saml-option.png)

3. **SAML로 Single Sign-On 설정** 페이지에서 **편집** 아이콘을 선택하여 **기본 SAML 구성** 창을 엽니다.

    ![기본 SAML 구성 편집](common/edit-urls.png)

4. **기본 SAML 구성** 섹션에서 다음을 수행합니다.

   ![LoginRadius 도메인 및 URL Single Sign-On 정보](common/sp-identifier.png)

   1. **로그온 URL** 텍스트 상자에 URL `https://secure.loginradius.com/login`을 입력합니다.

   1. **식별자(엔터티 ID)** 텍스트 상자에 URL `https://lr.hub.loginradius.com/`을 입력합니다.

   1. **회신 URL(Assertion Consumer Service URL)** 텍스트 상자에 LoginRadius ACS URL `https://lr.hub.loginradius.com/saml/serviceprovider/AdfsACS.aspx`를 입력합니다. 

5. **SAML로 Single Sign-On 설정** 페이지의 **SAML 서명 인증서** 섹션에서 **다운로드** 를 선택하여 요구 사항에 따라 제공된 옵션에서 **페더레이션 메타데이터 XML** 을 다운로드하고 컴퓨터에 저장합니다.

    ![인증서 다운로드 링크](common/metadataxml.png)

6. **LoginRadius 설정** 섹션에서 요구 사항에 따라 적절한 URL을 복사합니다.

   ![구성 URL 복사](common/copy-configuration-urls.png)

   - 로그인 URL

   - Azure AD 식별자

   - 로그아웃 URL

## <a name="configure-loginradius-single-sign-on"></a>LoginRadius Single Sign-on 구성

이 섹션에서는 LoginRadius 관리자 콘솔에서 Azure AD Single Sign-On을 사용하도록 설정합니다.

1. LoginRadius [관리자 콘솔](https://adminconsole.loginradius.com/login) 계정에 로그인합니다.

2. [LoginRadius 관리자 콘솔](https://secure.loginradius.com/account/team)에서 **팀 관리** 섹션으로 이동합니다.

3. **Single Sign-On** 탭을 선택한 다음, **Azure AD** 를 선택합니다.

   ![LoginRadius 팀 관리 콘솔의 Single Sign-On 메뉴를 보여 주는 스크린샷](./media/loginradius-tutorial/azure-ad.png)
4. Azure AD 설정 페이지에서 다음 단계를 완료합니다.

   ![LoginRadius 팀 관리 콘솔에서 Azure Active Directory 구성을 보여 주는 스크린샷](./media/loginradius-tutorial/single-sign-on.png)

    1. **ID 공급자 위치** 에서 Azure AD 계정에서 가져오는 로그온 엔드포인트를 입력합니다.

    1. **ID 공급자 로그아웃 URL** 에서 Azure AD 계정에서 가져오는 로그아웃 엔드포인트를 입력합니다.
 
    1. **ID 공급자 인증서** 에서 Azure AD 계정에서 가져오는 Azure AD 인증서를 입력합니다. 헤더와 바닥글로 인증서 값을 입력합니다. 예: `-----BEGIN CERTIFICATE-----<certifciate value>-----END CERTIFICATE-----`

    1. **서비스 공급자 인증서** 및 **서버 공급자 인증서 키** 에서 인증서와 키를 입력합니다. 

       명령줄(Linux/Mac)에서 다음 명령을 실행하여 자체 서명된 인증서를 만들 수 있습니다.

       - SP에 대한 인증서 키를 가져오는 명령: `openssl genrsa -out lr.hub.loginradius.com.key 2048`

       - SP에 대한 인증서를 가져오는 명령: `openssl req -new -x509 -key lr.hub.loginradius.com.key -out lr.hub.loginradius.com.cert -days 3650 -subj /CN=lr.hub.loginradius.com`

       > [!NOTE]
       > 헤더와 바닥글로 인증서 및 인증서 키 값을 입력해야 합니다.
       > - 인증서 값 예제 형식: `-----BEGIN CERTIFICATE-----<certifciate value>-----END CERTIFICATE-----`
       > - 인증서 키 값 예제 형식: `-----BEGIN RSA PRIVATE KEY-----<certifciate key value>-----END RSA PRIVATE KEY-----`

5. **데이터 매핑** 섹션에서 필드(SP 필드)를 선택하고 해당하는 Azure AD 필드(IdP 필드)를 입력합니다.

    다음은 Azure AD에 대해 나열된 몇 가지 필드 이름입니다.

    | 필드    | 프로필 키                                                          |
    | --------- | -------------------------------------------------------------------- |
    | Email     | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress` |
    | FirstName | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`    |
    | LastName  | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname`      |

    > [!NOTE]
    > **이메일** 필드 매핑이 필요합니다. **FirstName** 및 **LastName** 필드 매핑은 선택 사항입니다.

### <a name="create-an-azure-ad-test-user"></a>Azure AD 테스트 사용자 만들기 

이 섹션의 목적은 Azure Portal에서 Britta Simon이라는 테스트 사용자를 만드는 것입니다.

1. Azure Portal의 왼쪽 창에서 **Azure Active Directory**, **사용자** 를 차례로 선택하고 **모든 사용자** 를 선택합니다.

    !["사용자 및 그룹" 및 "모든 사용자" 링크](common/users.png)

2. 화면 위쪽에서 **새 사용자** 를 선택합니다.

    ![새 사용자 단추](common/new-user.png)

3. **사용자** 속성에서 다음 단계를 수행합니다.

   ![사용자 대화 상자](common/user-properties.png)

   1. **이름** 필드에 **BrittaSimon** 을 입력합니다.
  
   1. **사용자 이름** 필드에서 `brittasimon@yourcompanydomain.extension`을 입력합니다. 예들 들어 BrittaSimon@contoso.com입니다.

   1. **암호 표시** 확인란을 선택한 다음, **암호** 상자에 표시된 값을 적어둡니다.

   1. **만들기** 를 선택합니다.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD 테스트 사용자 할당

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 Britta Simon에게 LoginRadius에 대한 액세스 권한을 부여합니다.

1. Azure Portal에서 **엔터프라이즈 애플리케이션** 을 선택한 다음, **모든 애플리케이션** 을 선택하고 **LoginRadius** 를 선택합니다.

    ![엔터프라이즈 애플리케이션 블레이드](common/enterprise-applications.png)

2. 애플리케이션 목록에서 **LoginRadius** 를 선택합니다.

    ![애플리케이션 목록의 LoginRadius 링크](common/all-applications.png)

3. 왼쪽 메뉴에서 **사용자 및 그룹** 을 선택합니다.

    !["사용자 및 그룹" 링크](common/users-groups-blade.png)

4. **사용자 추가** 단추를 선택한 다음, **할당 추가** 창에서 **사용자 및 그룹** 을 선택합니다.

    ![할당 추가 창](common/add-assign-user.png)

5. **사용자 및 그룹** 창의 **사용자** 목록에서 **Britta Simon** 을 선택한 다음, 화면 아래쪽에서 **선택** 단추를 선택합니다.

6. SAML 어설션에 역할 값이 필요한 경우 **역할 선택** 창의 목록에서 사용자에게 적합한 역할을 선택합니다. 그런 다음, 화면 아래쪽에서 **선택** 단추를 선택합니다.

7. **할당 추가** 창에서 **할당** 단추를 선택합니다.

### <a name="create-loginradius-test-user"></a>LoginRadius 테스트 사용자 만들기

1. LoginRadius [관리자 콘솔](https://adminconsole.loginradius.com/login) 계정에 로그인합니다.

2. LoginRadius 관리자 콘솔에서 팀 관리 섹션으로 이동합니다.

   ![LoginRadius 관리자 콘솔을 보여 주는 스크린샷](./media/loginradius-tutorial/team-management.png)
3. 측면 메뉴에서 **팀 멤버 추가** 를 선택하여 양식을 엽니다. 

4. **팀 멤버 추가** 양식에서 사용자의 세부 정보를 제공하고 사용자에게 부여할 사용 권한을 할당하여 LoginRadius 사이트에서 Britta Simon이라는 사용자를 만듭니다. 역할을 기반으로 하는 사용 권한에 대한 자세한 내용은 LoginRadius [팀 멤버 관리](https://www.loginradius.com/docs/api/v2/admin-console/team-management/manage-team-members#roleaccesspermissions0) 문서의 [역할 액세스 권한](https://www.loginradius.com/docs/api/v2/admin-console/team-management/manage-team-members#roleaccesspermissions0) 섹션을 참조하세요. Single Sign-On을 사용하려면 먼저 사용자를 만들고 활성화해야 합니다.

### <a name="test-single-sign-on"></a>Single Sign-On 테스트 

이 섹션에서는 액세스 패널을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다.

1. 브라우저에서 https://accounts.loginradius.com/auth.aspx 로 이동하여 **Fed SSO 로그인** 을 선택합니다.
2. LoginRadius 앱 이름을 입력한 다음, **로그인** 을 선택합니다.
3. Azure AD 계정에 로그인하라는 팝업이 열립니다.
4. 인증 후에 팝업이 닫히고 LoginRadius 관리자 콘솔에 로그인됩니다.

## <a name="additional-resources"></a>추가 리소스

- [Azure Active Directory와 SaaS Apps를 통합하는 방법에 대한 자습서 목록](./tutorial-list.md)

- [Azure Active Directory로 애플리케이션 액세스 및 Single Sign-On을 구현하는 방법](../manage-apps/what-is-single-sign-on.md)

- [Azure Active Directory의 조건부 액세스란?](../conditional-access/overview.md)
