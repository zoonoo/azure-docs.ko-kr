---
title: '자습서: Zoom과 Azure Active Directory 통합 | Microsoft Docs'
description: Azure Active Directory와 Zoom 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 0ebdab6c-83a8-4737-a86a-974f37269c31
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/05/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 641fe5439e320208d41969b9563293257648d488
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2019
ms.locfileid: "57842093"
---
# <a name="tutorial-azure-active-directory-integration-with-zoom"></a>자습서: Zoom과 Azure Active Directory 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 Zoom을 통합하는 방법에 대해 알아봅니다.
Zoom을 Azure AD와 통합하면 다음과 같은 이점이 제공됩니다.

* Zoom에 대한 액세스 권한이 있는 사용자를 Azure AD에서 제어할 수 있습니다.
* 사용자가 자신의 Azure AD 계정으로 Zoom에 자동으로 로그온(Single Sign-On)되도록 설정할 수 있습니다.
* 단일 중앙 위치인 Azure Portal에서 계정을 관리할 수 있습니다.

Azure AD와의 SaaS 앱 통합에 대한 자세한 내용은 [Azure Active Directory를 사용한 응용 프로그램 액세스 및 Single Sign-On](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)을 참조하세요.
Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/) 계정을 만듭니다.

## <a name="prerequisites"></a>필수 조건

Zoom과 Azure AD 통합을 구성하려면 다음 항목이 필요합니다.

* Azure AD 구독 Azure AD 환경이 없으면 [여기](https://azure.microsoft.com/pricing/free-trial/)에서 1개월 평가판을 구할 수 있습니다.
* Zoom Single Sign-On이 설정된 구독

## <a name="scenario-description"></a>시나리오 설명

이 자습서에서는 테스트 환경에서 Azure AD Single Sign-On을 구성하고 테스트합니다.

* Zoom이 **SP**에서 시작된 SSO를 지원

## <a name="adding-zoom-from-the-gallery"></a>갤러리에서 Zoom 추가

Zoom의 Azure AD 통합을 구성하려면 갤러리의 Zoom을 관리되는 SaaS 앱 목록에 추가해야 합니다.

**갤러리에서 Zoom을 추가하려면 다음 단계를 수행합니다.**

1. **[Azure Portal](https://portal.azure.com)** 의 왼쪽 탐색 창에서 **Azure Active Directory** 아이콘을 클릭합니다.

    ![Azure Active Directory 단추](common/select-azuread.png)

2. **엔터프라이즈 응용 프로그램**으로 이동한 다음, **모든 응용 프로그램** 옵션을 선택합니다.

    ![엔터프라이즈 애플리케이션 블레이드](common/enterprise-applications.png)

3. 새 애플리케이션을 추가하려면 대화 상자 맨 위 있는 **새 애플리케이션** 단추를 클릭합니다.

    ![새 애플리케이션 단추](common/add-new-app.png)

4. 검색 상자에 **Zoom**을 입력하고 결과 패널에서 **Zoom**을 선택한 후 **추가** 단추를 클릭하여 애플리케이션을 추가합니다.

     ![결과 목록의 Zoom in](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성 및 테스트

이 섹션에서는 **Britta Simon**이라는 테스트 사용자를 기반으로 Zoom에서 Azure AD Single Sign-On을 구성하고 테스트합니다.
Single Sign-On이 작동하려면 Azure AD 사용자와 Zoom의 관련 사용자 간에 연결 관계를 설정해야 합니다.

Zoom에서 Azure AD Single Sign-On을 구성하고 테스트하려면 다음 구성 요소를 완료해야 합니다.

1. **[Azure AD Single Sign-On 구성](#configure-azure-ad-single-sign-on)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
2. **[Zoom Single Sign-On 구성](#configure-zoom-single-sign-on)** - 애플리케이션 쪽에서 Single Sign-On 설정을 구성합니다.
3. **[Azure AD 테스트 사용자 만들기](#create-an-azure-ad-test-user)** - Britta Simon으로 Azure AD Single Sign-On을 테스트하는 데 사용합니다.
4. **[Azure AD 테스트 사용자 할당](#assign-the-azure-ad-test-user)** - Britta Simon이 Azure AD Single Sign-on을 사용할 수 있도록 합니다.
5. **[Zoom 테스트 사용자 만들기](#create-zoom-test-user)** - Britta Simon의 Azure AD 표현과 연결된 해당 사용자를 Zoom에 만듭니다.
6. **[Single Sign-On 테스트](#test-single-sign-on)** - 구성이 작동하는지 여부를 확인합니다.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성

이 섹션에서는 Azure Portal에서 Azure AD Single Sign-On을 사용하도록 설정합니다.

Zoom에서 Azure AD Single Sign-On을 구성하려면 다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com/)의 **Zoom** 애플리케이션 통합 페이지에서 **Single Sign-On**을 선택합니다.

    ![Single Sign-On 구성 링크](common/select-sso.png)

2. **Single Sign-On 방법 선택** 대화 상자에서 **SAML/WS-Fed** 모드를 선택하여 Single Sign-On을 사용하도록 설정합니다.

    ![Single Sign-On 선택 모드](common/select-saml-option.png)

3. **SAML로 Single Sign-On 설정** 페이지에서 **편집** 아이콘을 클릭하여 **기본 SAML 구성** 대화 상자를 엽니다.

    ![기본 SAML 구성 편집](common/edit-urls.png)

4. **기본 SAML 구성** 섹션에서 다음 단계를 수행합니다.

    ![Zoom 도메인 및 URL Single Sign-On 정보](common/sp-identifier.png)

    a. **로그온 URL** 텍스트 상자에서 `https://<companyname>.zoom.us` 패턴을 사용하는 URL을 입력합니다.

    b. **식별자(엔터티 ID)** 텍스트 상자에서 `<companyname>.zoom.us` 패턴을 사용하는 URL을 입력합니다.

    > [!NOTE]
    > 이러한 값은 실제 값이 아닙니다. 실제 로그온 URL 및 식별자로 이러한 값을 업데이트합니다. 이러한 값을 얻으려면 [Zoom 클라이언트 지원팀](https://support.zoom.us/hc/en-us)에 문의하세요. Azure Portal의 **기본 SAML 구성** 섹션에 표시된 패턴을 참조할 수도 있습니다.

5. Zoom 애플리케이션은 특정 서식에서 SAML 어설션을 예상하며, SAML 토큰 특성 구성에 사용자 할당 특성 매핑을 추가해야 합니다. 다음 스크린샷에서는 기본 특성의 목록을 보여 줍니다.  **편집**  아이콘을 클릭하여  **사용자 특성**  대화 상자를 엽니다.

    ![이미지](common/edit-attribute.png)

6. 위에서 언급한 특성 외에도, Zoom 애플리케이션에는 SAML 응답에서 다시 전달되어야 하는 몇 가지 특성이 추가로 필요합니다. **사용자 특성** 대화 상자의 **사용자 클레임** 섹션에서 다음 단계를 수행하여 아래 표와 같은 SAML 토큰 특성을 추가합니다.
    
    | Name | 네임스페이스  |  원본 특성|
    | ---------------| --------------- | --------- |
    | 메일 주소  | user.mail  | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/mail` |
    | 이름  | user.givenname  | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname` |
    | 성  | user.surname  | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname` |
    | 전화 번호  | user.telephonenumber  | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/phone` |
    | department  | user.department  | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/department` |
    | role |    user.assignedrole |`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/role` |

    > [!NOTE]
    > [여기](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal)를 클릭하여 Azure AD에서 역할을 구성하는 방법을 알아봅니다.

    a. **새 클레임 추가**를 클릭하여 **사용자 클레임 관리** 대화 상자를 엽니다.

    ![이미지](common/new-save-attribute.png)

    ![이미지](common/new-attribute-details.png)

    b. **이름** 텍스트 상자에서 해당 행에 표시된 특성 이름을 입력합니다.

    다. 원본을 **특성**으로 선택합니다.

    d. **원본 특성** 목록에서 해당 행에 표시된 특성 값을 입력합니다.

    e. **확인**을 클릭합니다.

    f. **저장**을 클릭합니다.

    > [!NOTE]
    > Zoom은 SAML 페이로드에서 그룹 클레임이 필요할 수 있으므로 그룹을 만든 경우 해당 그룹 정보와 함께 [Zoom 클라이언트 지원 팀](https://support.zoom.us/hc/en-us)에 문의하여 이 그룹 정보를 최종적으로 구성할 수 있도록 합니다. 또한 최종적인 구성을 위해 개체 ID를 [Zoom 클라이언트 지원 팀](https://support.zoom.us/hc/en-us)에 제공해야 합니다. 개체 ID를 가져오려면 이 [문서](https://support.zoom.us/hc/en-us/articles/115005887566)를 따르세요.

7. **SAML로 Single Sign-On 설정** 페이지의 **SAML 서명 인증서** 섹션에서 **다운로드**를 클릭하여 요구 사항에 따라 제공된 옵션에서 **인증서(Base64)** 를 다운로드한 다음, 컴퓨터에 저장합니다.

    ![인증서 다운로드 링크](common/certificatebase64.png)

8. **Zoom 설정** 섹션에서 요구 사항에 따라 적절한 URL을 복사합니다.

    ![구성 URL 복사](common/copy-configuration-urls.png)

    a. 로그인 URL

    b. Azure AD 식별자

    다. 로그아웃 URL

### <a name="configure-zoom-single-sign-on"></a>Zoom Single Sign-On 구성

1. 다른 웹 브라우저 창에서 관리자 권한으로 Zoom 회사 사이트에 로그인 합니다.

2. **Single Sign-On** 탭을 클릭합니다.

    ![Single Sign-On 탭](./media/zoom-tutorial/ic784700.png "Single Sign-On")

3. **보안 제어**를 클릭하고, **Single Sign-On** 설정으로 이동합니다.

4. Single Sign-On 섹션에서 다음 단계를 수행 합니다.

    ![Single Sign-On 섹션](./media/zoom-tutorial/ic784701.png "Single Sign-On")

    a. **로그인 페이지 URL** 텍스트 상자에, Azure Portal에서 복사한 **로그인 URL** 값을 붙여넣습니다.

    b. **로그아웃 페이지 URL** 값의 경우, Azure Portal로 이동하고 왼쪽의 **Azure Active Directory**를 클릭한 후 **앱 등록**으로 이동합니다.

    ![Azure Active Directory 단추](./media/zoom-tutorial/appreg.png)

    다. **엔드포인트**를 클릭합니다.

    ![엔드포인트 단추](./media/zoom-tutorial/endpoint.png)

    d. **SAML-P 로그아웃 엔드포인트**를 복사한 후 **로그아웃 페이지 URL** 텍스트 상자에 붙여넣습니다.

    ![엔드포인트 복사 단추](./media/zoom-tutorial/endpoint1.png)

    e. Base 64로 인코딩된 인증서를 메모장에서 열고, 내용을 클립보드에 복사한 다음 **ID 공급자 인증서** 텍스트 상자에 붙여넣습니다.

    f. **발급자** 텍스트 상자에 Azure Portal에서 복사한 **Azure AD 식별자** 값을 붙여넣습니다. 

    g. **저장**을 클릭합니다.

    > [!NOTE]
    > 자세한 내용은 확대/축소 설명서 [https://zoomus.zendesk.com/hc/articles/115005887566](https://zoomus.zendesk.com/hc/articles/115005887566)를 참조하세요.

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

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 Britta Simon에게 Zoom에 대한 액세스 권한을 부여합니다.

1. Azure Portal에서 **엔터프라이즈 애플리케이션**을 선택한 다음, **모든 애플리케이션**과 **Zoom**을 차례로 선택합니다.

    ![엔터프라이즈 애플리케이션 블레이드](common/enterprise-applications.png)

2. 애플리케이션 목록에 **Zoom**를 입력하고 선택합니다.

    ![애플리케이션 목록의 Zoom 연결](common/all-applications.png)

3. 왼쪽 메뉴에서 **사용자 및 그룹**을 선택합니다.

    !["사용자 및 그룹" 링크](common/users-groups-blade.png)

4. **사용자 추가** 단추를 클릭한 다음, **할당 추가** 대화 상자에서 **사용자 및 그룹**을 선택합니다.

    ![할당 추가 창](common/add-assign-user.png)

5. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **Britta Simon**을 선택하고 화면 아래쪽에서 **선택** 단추를 클릭합니다.

6. SAML 어설션 및 **역할 선택** 대화 상자에서 모든 역할 값이 필요한 경우 목록에서 적절한 사용자 역할을 선택한 다음, 화면 맨 아래에 있는 **선택** 단추를 클릭합니다.

7. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.

### <a name="create-zoom-test-user"></a>Zoom 테스트 사용자 만들기

Azure AD 사용자가 Zoom에 로그인할 수 있도록 하려면 사용자 계정이 Zoom으로 프로비전되어야 합니다. Zoom의 경우, 수동으로 프로비전합니다.

### <a name="to-provision-a-user-account-perform-the-following-steps"></a>사용자 계정을 프로비저닝하려면 다음 단계를 수행합니다.

1. 관리자 권한으로 **Zoom** 회사 사이트로 로그인합니다.

2. **계정 관리** 탭을 클릭한 후 **사용자 관리**를 클릭합니다.

3. 사용자 관리 섹션에서 **사용자 추가**를 클릭합니다.

    ![사용자 관리](./media/zoom-tutorial/ic784703.png "사용자 관리")

4. **사용자 추가** 페이지에서 다음 단계를 수행합니다.

    ![사용자 추가](./media/zoom-tutorial/ic784704.png "사용자 추가")

    a. **사용자 유형**으로 **기본**을 선택합니다.

    b. **이메일** 텍스트 상자에 프로비전하려는 유효한 Azure AD 계정의 이메일 주소를 입력합니다.

    다. **추가**를 클릭합니다.

> [!NOTE]
> Zoom에서 제공하는 다른 Zoom 사용자 계정 만들기 도구 또는 API를 사용하여 Azure Active Directory 사용자 계정을 프로비전할 수 있습니다.

### <a name="test-single-sign-on"></a>Single Sign-On 테스트

이 섹션에서는 액세스 패널을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다.

액세스 패널에서 Zoom 타일을 클릭하면 SSO를 설정한 Zoom에 자동으로 로그인되어야 합니다. 액세스 패널에 대한 자세한 내용은 [액세스 패널 소개](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)를 참조하세요.

## <a name="additional-resources"></a>추가 리소스

- [Azure Active Directory와 SaaS Apps를 통합하는 방법에 대한 자습서 목록](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory로 애플리케이션 액세스 및 Single Sign-On을 구현하는 방법](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory의 조건부 액세스란?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
