---
title: '자습서: Help Scout와 Azure Active Directory 통합 | Microsoft Docs'
description: Azure Active Directory 및 Help Scout 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 0aad9910-0bc1-4394-9f73-267cf39973ab
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/15/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 35f9a8949f5b51f88b9297890fc5562e7b8dd591
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/18/2019
ms.locfileid: "59273382"
---
# <a name="tutorial-azure-active-directory-integration-with-help-scout"></a>자습서: Help Scout와 Azure Active Directory 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 Help Scout를 통합하는 방법에 대해 알아봅니다.
Azure AD에 Help Scout를 통합하면 다음과 같은 이점을 얻을 수 있습니다.

* Help Scout에 대한 액세스 권한이 있는 사용자를 Azure AD에서 제어할 수 있습니다.
* 사용자가 해당 Azure AD 계정으로 Help Scout에 자동으로 로그인(Single Sign-on)되도록 설정할 수 있습니다.
* 단일 중앙 위치인 Azure Portal에서 계정을 관리할 수 있습니다.

Azure AD와의 SaaS 앱 통합에 대한 자세한 내용은 [Azure Active Directory를 사용한 응용 프로그램 액세스 및 Single Sign-On](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)을 참조하세요.
Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/) 계정을 만듭니다.

## <a name="prerequisites"></a>필수 조건

Help Scout와 Azure AD 통합을 구성하려면 다음 항목이 필요합니다.

* Azure AD 구독 Azure AD 환경이 없으면 [여기](https://azure.microsoft.com/pricing/free-trial/)에서 1개월 평가판을 구할 수 있습니다.
* Help Scout Single Sign-On을 사용하도록 설정된 구독

## <a name="scenario-description"></a>시나리오 설명

이 자습서에서는 테스트 환경에서 Azure AD Single Sign-On을 구성하고 테스트합니다.

* Help Scout에서 **SP 및 IDP** 시작 SSO를 지원합니다.
* Help Scout에서 **Just-In-Time** 사용자 프로비전을 지원합니다.

## <a name="adding-help-scout-from-the-gallery"></a>갤러리에서 Help Scout 추가

Help Scout의 Azure AD 통합을 구성하려면 갤러리의 Help Scout를 관리되는 SaaS 앱 목록에 추가해야 합니다.

**갤러리에서 Help Scout를 추가하려면 다음 단계를 수행합니다.**

1. **[Azure Portal](https://portal.azure.com)** 의 왼쪽 탐색 창에서 **Azure Active Directory** 아이콘을 클릭합니다.

    ![Azure Active Directory 단추](common/select-azuread.png)

2. **엔터프라이즈 응용 프로그램**으로 이동한 다음, **모든 응용 프로그램** 옵션을 선택합니다.

    ![엔터프라이즈 애플리케이션 블레이드](common/enterprise-applications.png)

3. 새 애플리케이션을 추가하려면 대화 상자 맨 위 있는 **새 애플리케이션** 단추를 클릭합니다.

    ![새 애플리케이션 단추](common/add-new-app.png)

4. 검색 상자에 **Help Scout**를 입력하고, 결과 패널에서 **Help Scout**를 선택한 다음, **추가** 단추를 클릭하여 애플리케이션을 추가합니다.

     ![결과 목록의 Help Scout](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성 및 테스트

이 섹션에서는 **Britta Simon**이라는 테스트 사용자를 기반으로 하여 Help Scout에서 Azure AD Single Sign-On을 구성하고 테스트합니다.
Single Sign-On이 작동하려면 Azure AD 사용자와 Workable의 관련 사용자 간에 연결 관계를 설정해야 합니다.

Help Scout에서 Azure AD Single Sign-On을 구성하고 테스트하려면 다음 구성 요소를 완료해야 합니다.

1. **[Azure AD Single Sign-On 구성](#configure-azure-ad-single-sign-on)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
2. **[Help Scout Single Sign-On 구성](#configure-help-scout-single-sign-on)** - 애플리케이션 쪽에서 Single Sign-On 설정을 구성합니다.
3. **[Azure AD 테스트 사용자 만들기](#create-an-azure-ad-test-user)** - Britta Simon으로 Azure AD Single Sign-On을 테스트하는 데 사용합니다.
4. **[Azure AD 테스트 사용자 할당](#assign-the-azure-ad-test-user)** - Britta Simon이 Azure AD Single Sign-on을 사용할 수 있도록 합니다.
5. **[Help Scout 테스트 사용자 만들기](#create-help-scout-test-user)** - Britta Simon의 Azure AD 표현과 연결된 해당 사용자를 Help Scout에 만듭니다.
6. **[Single Sign-On 테스트](#test-single-sign-on)** - 구성이 작동하는지 여부를 확인합니다.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성

이 섹션에서는 Azure Portal에서 Azure AD Single Sign-On을 사용하도록 설정합니다.

Help Scout에서 Azure AD Single Sign-On을 구성하려면 다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com/)의 **Help Scout** 애플리케이션 통합 페이지에서 **Single Sign-On**을 선택합니다.

    ![Single Sign-On 구성 링크](common/select-sso.png)

2. **Single Sign-On 방법 선택** 대화 상자에서 **SAML/WS-Fed** 모드를 선택하여 Single Sign-On을 사용하도록 설정합니다.

    ![Single Sign-On 선택 모드](common/select-saml-option.png)

3. **SAML로 Single Sign-On 설정** 페이지에서 **편집** 아이콘을 클릭하여 **기본 SAML 구성** 대화 상자를 엽니다.

    ![기본 SAML 구성 편집](common/edit-urls.png)

4. **IDP** 시작 모드에서 애플리케이션을 구성하려면 **기본 SAML 구성** 섹션에서 다음 단계를 수행합니다.

    ![Help Scout 도메인 및 URL Single Sign-On 정보](common/idp-intiated.png)

    a. **식별자**는 Help Scout의 **대상 URI(서비스 공급자 엔터티 ID)** 이며, `urn:`으로 시작합니다.

    b. **회신 URL**은 Help Scout의 **다시 게시 URL(Assertion Consumer Service URL)** 이며, `https://`로 시작합니다. 

    > [!NOTE]
    > 이러한 URL의 값은 데모용으로만 사용합니다. 실제 회신 URL 및 식별자에서 이러한 값을 업데이트해야 합니다. 자습서의 뒷부분에 설명되어 있는 인증 섹션 아래의 **Single Sign-On** 탭에서 이러한 값을 가져옵니다.

5. **SP** 시작 모드에서 애플리케이션을 구성하려면 **추가 URL 설정**을 클릭하고 다음 단계를 수행합니다.

    ![Help Scout 도메인 및 URL Single Sign-On 정보](common/metadata-upload-additional-signon.png)

    **로그온 URL** 텍스트 상자에 URL을 입력합니다. `https://secure.helpscout.net/members/login/`

6. **SAML로 Single Sign-On 설정** 페이지의 **SAML 서명 인증서** 섹션에서 **다운로드**를 클릭하여 요구 사항에 따라 제공된 옵션에서 **인증서(Base64)** 를 다운로드한 다음, 컴퓨터에 저장합니다.

    ![인증서 다운로드 링크](common/certificatebase64.png)

7. **Help Scout 설정** 섹션에서 요구 사항에 따라 적절한 URL을 복사합니다.

    ![구성 URL 복사](common/copy-configuration-urls.png)

    a. 로그인 URL

    b. Azure AD 식별자

    다. 로그아웃 URL

### <a name="configure-help-scout-single-sign-on"></a>Help Scout Single Sign-On 구성

1. 다른 웹 브라우저 창에서 Help Scout 회사 사이트에 관리자로 로그인합니다.

2. 위쪽 메뉴에서 **관리**를 클릭한 다음, 드롭다운 메뉴에서 **"회사"** 를 선택합니다.

    ![Configure Single Sign-On](./media/helpscout-tutorial/settings1.png)

3. 왼쪽 탐색 창에서 **인증**을 선택합니다.

    ![Configure Single Sign-On](./media/helpscout-tutorial/settings2.png)

4. SAML 설정 섹션으로 이동하고 다음 단계를 수행합니다.

    ![Configure Single Sign-On](./media/helpscout-tutorial/settings3.png)

    a. **다시 게시 URL(Assertion Consumer Service URL)** 값을 복사하여 Azure Portal의 **기본 SAML 구성** 섹션에 있는 **회신 URL** 텍스트 상자에 해당 값을 붙여넣습니다.

    b. **대상 URI(서비스 공급자 엔터티 ID)** 값을 복사하여 Azure Portal의 **기본 SAML 구성** 섹션에 있는 **식별자** 텍스트 상자에 해당 값을 붙여넣습니다.

5. **SAML 사용**을 설정하고 다음 단계를 수행합니다.

    ![Configure Single Sign-On](./media/helpscout-tutorial/settings4.png)

    a. **Single Sign-On URL** 텍스트 상자에 Azure Portal에서 복사한 **로그인 URL** 값을 붙여넣습니다.

    b. **인증서 업로드**를 클릭하여 Azure Portal에서 다운로드한 **인증서(Base64)** 를 업로드합니다.

    다. 조직의 전자 메일 도메인(예: `contoso.com`)을 **전자 메일 도메인** 텍스트 상자에 입력합니다. 쉼표로 여러 도메인을 구분할 수 있습니다. [Help Scout 로그인 페이지](https://secure.helpscout.net/members/login/)의 해당 특정 도메인에 들어가는 Help Scout 사용자 또는 관리자는 언제든지 해당 자격 증명을 인증하기 위해 ID 공급자로 라우팅됩니다.

    d. 마지막으로 사용자만 이 메서드를 통해 Help Scout에 로그인하기를 원하는 경우 **강제 SAML 로그온**을 설정/해제할 수 있습니다. Help Scout 자격 증명을 사용하여 로그인하는 옵션을 계속해서 제공하려는 경우 설정을 해제할 수 있습니다. 활성화된 경우에도 계정 소유자는 해당 계정 암호를 사용하여 Help Scout에 항상 로그인할 수 있습니다.

    e. **저장**을 클릭합니다.

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

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 Britta Simon에게 Help Scout에 대한 액세스 권한을 부여합니다.

1. Azure Portal에서 **엔터프라이즈 애플리케이션**, **모든 애플리케이션**, **Help Scout**를 차례로 선택합니다.

    ![엔터프라이즈 애플리케이션 블레이드](common/enterprise-applications.png)

2. 애플리케이션 목록에서 **Help Scout**를 선택합니다.

    ![애플리케이션 목록의 Help Scout 링크](common/all-applications.png)

3. 왼쪽 메뉴에서 **사용자 및 그룹**을 선택합니다.

    !["사용자 및 그룹" 링크](common/users-groups-blade.png)

4. **사용자 추가** 단추를 클릭한 다음, **할당 추가** 대화 상자에서 **사용자 및 그룹**을 선택합니다.

    ![할당 추가 창](common/add-assign-user.png)

5. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **Britta Simon**을 선택하고 화면 아래쪽에서 **선택** 단추를 클릭합니다.

6. SAML 어설션 및 **역할 선택** 대화 상자에서 모든 역할 값이 필요한 경우 목록에서 적절한 사용자 역할을 선택한 다음, 화면 맨 아래에 있는 **선택** 단추를 클릭합니다.

7. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.

### <a name="create-help-scout-test-user"></a>Help Scout 테스트 사용자 만들기

이 섹션에서는 Help Scout에서 Britta Simon이라는 사용자를 만듭니다. Help Scout는 기본적으로 사용하도록 설정되는 Just-In-Time 사용자 프로비전을 지원합니다. 이 섹션에 작업 항목이 없습니다. Help Scout에 사용자가 아직 없는 경우 인증 후에 새 사용자가 만들어집니다.

### <a name="test-single-sign-on"></a>Single Sign-On 테스트

이 섹션에서는 액세스 패널을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다.

액세스 패널에서 Help Scout 타일을 클릭하면 SSO를 설정한 Help Scout에 자동으로 로그인됩니다. 액세스 패널에 대한 자세한 내용은 [액세스 패널 소개](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)를 참조하세요.

## <a name="additional-resources"></a>추가 리소스

- [Azure Active Directory와 SaaS Apps를 통합하는 방법에 대한 자습서 목록](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory로 애플리케이션 액세스 및 Single Sign-On을 구현하는 방법](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory의 조건부 액세스란?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)