---
title: '자습서: Trello와 Azure Active Directory 통합 | Microsoft Docs'
description: Azure Active Directory와 Trello 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: cd5ae365-9ed6-43a6-920b-f7814b993949
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/02/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 453827b42d12333fd4f27761e7f73484fb749532
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "65865784"
---
# <a name="tutorial-azure-active-directory-integration-with-trello"></a>자습서: Trello와 Azure Active Directory 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 Trello를 통합하는 방법에 대해 알아봅니다.
Trello를 Azure AD와 통합하면 다음과 같은 이점이 제공됩니다.

* Trello에 대한 액세스 권한이 있는 사용자를 Azure AD에서 제어할 수 있습니다.
* 사용자가 해당 Azure AD 계정으로 Trello에 자동으로 로그인(Single Sign-On)되도록 설정할 수 있습니다.
* 단일 중앙 위치인 Azure Portal에서 계정을 관리할 수 있습니다.

Azure AD와 SaaS 앱 통합에 대한 자세한 내용은 [Azure Active Directory를 사용한 애플리케이션 액세스 및 Single Sign-On이란 무엇인가요?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)를 참조하세요.
Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/) 계정을 만듭니다.

## <a name="prerequisites"></a>필수 조건

Trello와 Azure AD 통합을 구성하려면 다음 항목이 필요합니다.

* Azure AD 구독 Azure AD 환경이 없으면 [1개월 평가판](https://azure.microsoft.com/pricing/free-trial/)을 구할 수 있습니다.
* Trello Single Sign-On을 사용하도록 설정된 구독

## <a name="scenario-description"></a>시나리오 설명

이 자습서에서는 테스트 환경에서 Azure AD Single Sign-On을 구성하고 테스트합니다.

* Trello에서 SP 및 IDP 시작 SSO를 지원합니다.

* Trello에서 Just-In-Time 사용자 프로비저닝을 지원합니다.

## <a name="add-trello-from-the-gallery"></a>갤러리에서 Trello 추가

Trello의 Azure AD 통합을 구성하려면 먼저 갤러리의 Trello를 관리형 SaaS 앱 목록에 추가합니다.

갤러리에서 Trello를 추가하려면 다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com)의 왼쪽 창에서 **Azure Active Directory** 아이콘을 선택합니다.

    ![Azure Active Directory 단추](common/select-azuread.png)

2. **엔터프라이즈 애플리케이션**, **모든 애플리케이션**을 차례로 선택합니다.

    ![엔터프라이즈 애플리케이션 블레이드](common/enterprise-applications.png)

3. 새 애플리케이션을 추가하려면 대화 상자 맨 위 있는 **새 애플리케이션** 단추를 선택합니다.

    ![새 애플리케이션 단추](common/add-new-app.png)

4. 검색 상자에서 **Trello**를 입력한 다음, 결과 창에서 **Trello**를 선택합니다.

5. **추가** 단추를 선택하여 애플리케이션을 추가합니다.

     ![결과 목록의 Trello](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성 및 테스트

이 섹션에서는 **Britta Simon**이라는 테스트 사용자를 기반으로 하여 Trello에서 Azure AD Single Sign-On을 구성하고 테스트합니다.

Single Sign-On이 작동하려면 Azure AD 사용자와 Trello의 관련 사용자 간에 연결을 설정해야 합니다.

Trello에서 Azure AD Single Sign-On을 구성하고 테스트하려면 다음 구성 요소를 완료해야 합니다.

1. [Azure AD Single Sign-On 구성](#configure-azure-ad-single-sign-on) - 사용자가 이 기능을 사용할 수 있도록 합니다.
2. [Trello Single Sign-On 구성](#configure-trello-single-sign-on) - 애플리케이션 쪽에서 Single Sign-On 설정을 구성합니다.
3. [Azure AD 테스트 사용자 만들기](#create-an-azure-ad-test-user) - Britta Simon으로 Azure AD Single Sign-On을 테스트하는 데 사용합니다.
4. [Azure AD 테스트 사용자 할당](#assign-the-azure-ad-test-user) - Britta Simon이 Azure AD Single Sign-on을 사용할 수 있도록 합니다.
5. [Trello 테스트 사용자 만들기](#create-a-trello-test-user) - Britta Simon의 Azure AD 표현과 연결된 해당 사용자를 Trello에 만듭니다.
6. [Single Sign-On 테스트](#test-single-sign-on) - 구성이 작동하는지 확인합니다.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성

이 섹션에서는 Azure Portal에서 Azure AD Single Sign-On을 사용하도록 설정합니다.

> [!NOTE]
> Trello에서 **\<enterprise\>** 동적 필드를 가져와야 합니다. 동적 필드 값이 없는 경우 enterprise에 대한 동적 필드를 얻으려면 [Trello 지원 팀](mailto:support@trello.com)에 문의하세요.

Trello에서 Azure AD Single Sign-On을 구성하려면 다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com/)의 **Trello** 애플리케이션 통합 페이지에서 **Single Sign-On**을 선택합니다.

    ![Single Sign-On 구성 링크](common/select-sso.png)

2. **Single Sign-On 방법 선택** 대화 상자에서 **SAML**을 선택하여 Single Sign-On을 사용하도록 설정합니다.

    ![Single Sign-On 선택 모드](common/select-saml-option.png)

3. **SAML로 Single Sign-On 설정** 페이지에서 **편집** 아이콘을 선택하여 **기본 SAML 구성** 대화 상자를 엽니다.

    ![기본 SAML 구성 편집](common/edit-urls.png)

4. IDP 시작 모드에서 애플리케이션을 구성하려면 **기본 SAML 구성** 섹션에서 다음 단계를 수행합니다.

    ![Trello 도메인 및 URL Single Sign-On 정보](common/idp-intiated.png)

    a. **식별자** 상자에서 `https://trello.com/auth/saml/metadata` 패턴을 사용하여 URL을 입력합니다.

    b. **회신 URL** 상자에서 `https://trello.com/auth/saml/consume/<enterprise>` 패턴을 사용하여 URL을 입력합니다.

5. SP 시작 모드에서 애플리케이션을 구성하려면 **추가 URL 설정**을 선택하고, 다음 단계를 수행합니다.

    ![Trello 도메인 및 URL Single Sign-On 정보](common/metadata-upload-additional-signon.png)

    **식별자** 상자에서 `https://trello.com/auth/saml/login/<enterprise>` 패턴을 사용하여 URL을 입력합니다.

    > [!NOTE]
    > 이러한 값은 실제 값이 아닙니다. 실제 식별자, 회신 URL 및 로그온 URL을 사용하여 이러한 값을 업데이트합니다. 이러한 값을 얻으려면 [Trello 클라이언트 지원 팀](mailto:support@trello.com)에 문의하세요. Azure Portal의 **기본 SAML 구성** 섹션에 있는 패턴을 참조할 수도 있습니다.

6. Trello 애플리케이션에는 특정 형식의 SAML 어설션이 필요합니다. 이 애플리케이션에 대해 다음 클레임을 구성합니다. 애플리케이션 통합 페이지의 **사용자 특성** 섹션에서 이러한 특성의 값을 관리할 수 있습니다. **SAML로 Single Sign-On 설정** 페이지에서 **편집** 단추를 선택하여 **사용자 특성** 대화 상자를 엽니다.

    ![사용자 특성 대화 상자](common/edit-attribute.png)

7. **사용자 특성** 대화 상자의 **사용자 클레임** 섹션에서 이전 이미지에 표시된 것과 같이 SAML 토큰 특성을 구성합니다. 다음 단계를 수행합니다.

    | Name |  원본 특성|
    | --- | --- |
    | User.Email | user.mail |
    | User.FirstName | user.givenname |
    | User.LastName | user.surname |

    a. **새 클레임 추가**를 선택하여 **사용자 클레임 관리** 대화 상자를 엽니다.

    ![사용자 클레임 대화 상자](common/new-save-attribute.png)

    ![사용자 클레임 관리](common/new-attribute-details.png)

    b. **이름** 상자에서 해당 행에 표시된 특성 이름을 입력합니다.

    다. **네임스페이스**를 비워 둡니다.

    d. **원본**에서 **특성**을 선택합니다.

    e. **원본 특성** 목록에서 해당 행에 표시된 특성 값을 입력합니다.

    f. **확인**을 선택합니다.

    g. **저장**을 선택합니다.

8. **SAML로 Single Sign-On 설정** 페이지의 **SAML 서명 인증서** 섹션에서 **다운로드**를 클릭하여 요구 사항에 따라 제공된 옵션에서 **인증서(Base64)** 를 다운로드합니다. 그런 다음, 컴퓨터에 저장합니다.

    ![인증서 다운로드 링크](common/certificatebase64.png)

9. **Trello 설정** 섹션에서 요구 사항에 따라 적절한 URL을 복사합니다.

    ![구성 URL 복사](common/copy-configuration-urls.png)

    a. 로그인 URL

    b. Azure AD 식별자.

    다. 로그아웃 URL

### <a name="configure-trello-single-sign-on"></a>Trello Single Sign-On 구성

Trello 쪽에서 Single Sign-On을 구성하려면 먼저 Azure Portal에서 다운로드한 **인증서(Base64)** 와 복사한 URL을 [Trello 지원 팀](mailto:support@trello.com)에 보냅니다. SAML SSO 연결이 양쪽에서 모두 올바르게 설정되어 있는지 확인합니다.

### <a name="create-an-azure-ad-test-user"></a>Azure AD 테스트 사용자 만들기 

이 섹션의 목적은 Azure Portal에서 Britta Simon이라는 테스트 사용자를 만드는 것입니다.

1. Azure Portal의 왼쪽 창에서 **Azure Active Directory**, **사용자**를 차례로 선택하고 **모든 사용자**를 선택합니다.

    !["사용자 및 그룹" 및 "모든 사용자" 링크](common/users.png)

2. 화면 위쪽에서 **새 사용자**를 선택합니다.

    ![새 사용자 단추](common/new-user.png)

3. **사용자** 대화 상자에서 다음 단계를 수행합니다.

    ![사용자 대화 상자](common/user-properties.png)

    a. **이름** 필드에 **BrittaSimon**을 입력합니다.
  
    b. **이름** 필드에서 brittasimon@yourcompanydomain.extension을 입력합니다. 예를 들어 이 경우에는 "BrittaSimon@contoso.com"을 입력할 수 있습니다.

    다. **암호 표시** 확인란을 선택한 다음, **암호** 상자에 표시된 값을 적어 둡니다.

    d. **만들기**를 선택합니다.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD 테스트 사용자 할당

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 Britta Simon에게 Trello에 대한 액세스 권한을 부여합니다.

1. Azure Portal에서 **엔터프라이즈 애플리케이션**, **모든 애플리케이션**, **Trello**를 차례로 선택합니다.

    ![엔터프라이즈 애플리케이션 블레이드](common/enterprise-applications.png)

2. 애플리케이션 목록에서 **Trello**를 선택합니다.

    ![애플리케이션 목록의 Trello 링크](common/all-applications.png)

3. 왼쪽 메뉴에서 **사용자 및 그룹**을 선택합니다.

    !["사용자 및 그룹" 링크](common/users-groups-blade.png)

4. **사용자 추가** 단추를 선택합니다. 그런 후 **할당 추가** 대화 상자에서 **사용자 및 그룹**을 선택합니다.

    ![할당 추가 창](common/add-assign-user.png)

5. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **Britta Simon**을 선택합니다. 그런 다음, 화면의 아래쪽에서 **선택** 단추를 클릭합니다.

6. SAML 어설션에 역할 값이 필요한 경우 **역할 선택** 대화 상자의 목록에서 사용자에게 적합한 역할을 선택합니다. 그런 다음, 화면의 아래쪽에서 **선택** 단추를 클릭합니다.

7. **할당 추가** 대화 상자에서 **할당** 단추를 선택합니다.

### <a name="create-a-trello-test-user"></a>Trello 테스트 사용자 만들기

이 섹션에서는 Trello에서 Britta Simon이라는 사용자를 만듭니다. Trello는 기본적으로 사용하도록 설정되는 Just-In-Time 사용자 프로비저닝을 지원합니다. 이 섹션에 작업 항목이 없습니다. Trello에 사용자가 아직 없는 경우 인증 후에 새 사용자가 만들어집니다.

> [!NOTE]
> 사용자를 수동으로 만들어야 하는 경우  [Trello 지원 팀](mailto:support@trello.com)에 문의하세요.

### <a name="test-single-sign-on"></a>Single Sign-On 테스트 

이 섹션에서는 MyApps 포털을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다.

MyApps 포털에서 Trello 타일을 선택하면 Trello에 자동으로 로그인됩니다. MyApps 포털에 대한 자세한 내용은 [MyApps 포털이란?](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)을 참조하세요.

## <a name="additional-resources"></a>추가 리소스

- [Azure Active Directory와 SaaS 앱을 통합하는 방법에 대한 자습서 목록](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory로 애플리케이션 액세스 및 Single Sign-On을 구현하는 방법](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory의 조건부 액세스란?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

