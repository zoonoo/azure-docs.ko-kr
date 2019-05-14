---
title: '자습서: EBSCO와 Azure Active Directory 통합 | Microsoft Docs'
description: Azure Active Directory와 EBSCO 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 144f7f65-69e9-4016-a151-fe1104fd6ba8
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/01/2019
ms.author: jeedes
ms.openlocfilehash: 2753daf225016d3bd8e07383193a1260b40a36d5
ms.sourcegitcommit: b8a8d29fdf199158d96736fbbb0c3773502a092d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2019
ms.locfileid: "59564978"
---
# <a name="tutorial-azure-active-directory-integration-with-ebsco"></a>자습서: EBSCO와 Azure Active Directory 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 EBSCO를 통합하는 방법에 대해 알아봅니다.
EBSCO를 Azure AD와 통합하면 다음과 같은 이점이 제공됩니다.

* EBSCO에 대한 액세스 권한이 있는 사용자를 Azure AD에서 제어할 수 있습니다.
* 사용자가 자신의 Azure AD 계정으로 EBSCO에 자동으로 로그인(Single Sign-On)되도록 설정할 수 있습니다.
* 단일 중앙 위치인 Azure Portal에서 계정을 관리할 수 있습니다.

Azure AD와의 SaaS 앱 연결에 대한 자세한 내용은 [Azure Active Directory를 사용한 응용 프로그램 액세스 및 Single Sign-On](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)을 참조하세요.
Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/) 계정을 만듭니다.

## <a name="prerequisites"></a>필수 조건

EBSCO와 Azure AD 통합을 구성하려면 다음 항목이 필요합니다.

* Azure AD 구독 Azure AD 환경이 없으면 [체험 계정](https://azure.microsoft.com/free/)을 얻을 수 있습니다.
* EBSCO Single Sign-On이 설정된 구독

## <a name="scenario-description"></a>시나리오 설명

이 자습서에서는 테스트 환경에서 Azure AD Single Sign-On을 구성하고 테스트합니다.

* EBSCO는 **SP** 및 **IDP** 시작 SSO를 지원합니다.

* EBSCO는 **Just-In-Time** 사용자 프로비저닝을 지원합니다.

## <a name="adding-ebsco-from-the-gallery"></a>갤러리에서 EBSCO 추가

EBSCO의 Azure AD 통합을 구성하려면 갤러리의 EBSCO를 관리되는 SaaS 앱 목록에 추가해야 합니다.

**갤러리에서 EBSCO를 추가하려면 다음 단계를 수행합니다.**

1. **[Azure Portal](https://portal.azure.com)** 의 왼쪽 탐색 패널에서 **Azure Active Directory** 아이콘을 클릭합니다.

    ![Azure Active Directory 단추](common/select-azuread.png)

2. **엔터프라이즈 응용 프로그램**으로 이동한 다음, **모든 응용 프로그램** 옵션을 선택합니다.

    ![엔터프라이즈 애플리케이션 블레이드](common/enterprise-applications.png)

3. 새 애플리케이션을 추가하려면 대화 상자 맨 위에 있는 **새 애플리케이션** 단추를 클릭합니다.

    ![새 애플리케이션 단추](common/add-new-app.png)

4. 검색 상자에 **EBSCO**를 입력하고 결과 패널에서 **EBSCO**를 선택한 다음, **추가** 단추를 클릭하여 애플리케이션을 추가합니다.

     ![결과 목록의 EBSCO](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성 및 테스트

이 섹션에서는 **Britta Simon**이라는 테스트 사용자를 기반으로 EBSCO에서 Azure AD Single Sign-On을 구성하고 테스트합니다.
Single Sign-On이 작동하려면 Azure AD 사용자와 EBSCO의 관련 사용자 간에 연결 관계를 설정해야 합니다.

EBSCO에서 Azure AD Single Sign-On을 구성하고 테스트하려면 다음 구성 요소를 완료해야 합니다.

1. **[Azure AD Single Sign-On 구성](#configure-azure-ad-single-sign-on)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
2. **[EBSCO Single Sign-On 구성](#configure-ebsco-single-sign-on)** - 애플리케이션 쪽에서 Single Sign-On 설정을 구성합니다.
3. **[Azure AD 테스트 사용자 만들기](#create-an-azure-ad-test-user)** - Britta Simon으로 Azure AD Single Sign-On을 테스트하는 데 사용합니다.
4. **[Azure AD 테스트 사용자 할당](#assign-the-azure-ad-test-user)** - Britta Simon이 Azure AD Single Sign-on을 사용할 수 있도록 합니다.
5. **[EBSCO 테스트 사용자 만들기](#create-ebsco-test-user)** - Britta Simon의 Azure AD 표현과 연결된 해당 사용자를 EBSCO에 만듭니다.
6. **[Single Sign-On 테스트](#test-single-sign-on)** - 구성이 작동하는지 여부를 확인합니다.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성

이 섹션에서는 Azure Portal에서 Azure AD Single Sign-On을 사용하도록 설정합니다.

EBSCO에서 Azure AD Single Sign-On을 구성하려면 다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com/)의 **EBSCO** 애플리케이션 통합 페이지에서 **Single Sign-On**을 선택합니다.

    ![Single Sign-On 구성 링크](common/select-sso.png)

2. **Single Sign-On 방법 선택** 대화 상자에서 **SAML/WS-Fed** 모드를 선택하여 Single Sign-On을 사용하도록 설정합니다.

    ![Single Sign-On 선택 모드](common/select-saml-option.png)

3. **SAML로 Single Sign-On 설정** 페이지에서 **편집** 아이콘을 클릭하여 **기본 SAML 구성** 대화 상자를 엽니다.

    ![기본 SAML 구성 편집](common/edit-urls.png)

4. **IDP** 시작 모드에서 애플리케이션을 구성하려면 **기본 SAML 구성** 섹션에서 다음 단계를 수행합니다.

    ![EBSCO 도메인 및 URL Single Sign-On 정보](common/idp-identifier.png)

    **식별자** 텍스트 상자에 URL을 입력합니다. `pingsso.ebscohost.com`

5. **SP** 시작 모드에서 애플리케이션을 구성하려면 **추가 URL 설정**을 클릭하고 다음 단계를 수행합니다.

    ![이미지](common/both-preintegrated-signon.png)

    **로그인 URL** 텍스트 상자에서 `http://search.ebscohost.com/login.aspx?authtype=sso&custid=<unique EBSCO customer ID>&profile=<profile ID>` 패턴을 사용하여 URL을 입력합니다.

    > [!NOTE]
    > 로그온 URL 값은 실제 값이 아닙니다. 이 값을 실제 로그온 URL로 업데이트합니다. 이 값을 얻으려면 [EBSCO 클라이언트 지원 팀](mailto:sso@ebsco.com)에 문의하세요. Azure Portal의 **기본 SAML 구성** 섹션에 표시된 패턴을 참조할 수도 있습니다.

    o   **고유한 요소:**  

    o   **Custid** = 고유한 EBSCO 고객 ID를 입력합니다. 

    o   **Profile** = 클라이언트는 링크를 조정하여 사용자를 특정 프로필에 연결할 수 있습니다(EBSCO에서 구매한 제품에 따라 다름). 특정 프로필 ID를 입력할 수 있습니다. 주 ID는 eds(EBSCO 검색 서비스) 및 ehost(EBSOCOhost 데이터베이스)입니다. 동일한 경우에 대한 지침은 [여기](https://help.ebsco.com/interfaces/EBSCOhost/EBSCOhost_FAQs/How_do_I_set_up_direct_links_to_EBSCOhost_profiles_and_or_databases#profile)를 참조하세요.

6. EBSCO 애플리케이션은 특정 서식에서 SAML 어설션을 예상하며, SAML 토큰 특성 구성에 사용자 지정 특성 매핑을 추가해야 합니다. 다음 스크린샷에서는 기본 특성의 목록을 보여 줍니다.  **편집**  아이콘을 클릭하여  **사용자 특성**  대화 상자를 엽니다.

    ![이미지](common/edit-attribute.png)

     > [!Note]
    > **name** 특성은 필수이며 EBSCO 애플리케이션의 **이름 식별자 값**에 매핑됩니다. 기본적으로 추가되므로 수동으로 추가할 필요가 없습니다.

7. 위에서 언급한 특성 외에도, SAML 응답을 통해 몇 가지 특성이 추가로 EBSCO 애플리케이션에 전달되어야 합니다. **사용자 특성** 대화 상자의 **사용자 클레임** 섹션에서 다음 단계를 수행하여 아래 표와 같은 SAML 토큰 특성을 추가합니다. 

    | Name | 원본 특성|
    | ---------------| --------------- |    
    | FirstName   | user.givenname |
    | LastName   | user.surname |
    | Email   | user.mail |

    a. **새 클레임 추가**를 클릭하여 **사용자 클레임 관리** 대화 상자를 엽니다.

    ![이미지](common/new-save-attribute.png)

    ![이미지](common/new-attribute-details.png)

    b. **이름** 텍스트 상자에서 해당 행에 표시된 특성 이름을 입력합니다.

    다. **네임스페이스**를 비워 둡니다.

    d. 원본을 **특성**으로 선택합니다.

    e. **원본 특성** 목록에서 해당 행에 표시된 특성 값을 입력합니다.

    f. **저장**을 클릭합니다.

8. **SAML로 Single Sign-On 설정** 페이지의 **SAML 서명 인증서** 섹션에서 **다운로드**를 클릭하여 요구 사항에 따라 제공된 옵션에서 **페더레이션 메타데이터 XML**을 다운로드하고 컴퓨터에 저장합니다.

    ![인증서 다운로드 링크](common/metadataxml.png)

9. **EBSCO 설정** 섹션에서 요구 사항에 따라 적절한 URL을 복사합니다.

    ![구성 URL 복사](common/copy-configuration-urls.png)

    a. 로그인 URL

    b. Azure AD 식별자

    다. 로그아웃 URL

### <a name="configure-ebsco-single-sign-on"></a>EBSCO Single Sign-On 구성

**EBSCO** 쪽에서 Single Sign-On을 구성하려면 Azure Portal에서 다운로드한 **메타데이터 XML**과 적절히 복사한 URL을 [EBSCO 지원 팀](mailto:sso@ebsco.com)으로 보내야 합니다. 이렇게 설정하면 SAML SSO 연결이 양쪽에서 제대로 설정됩니다.

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

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 Britta Simon에게 EBSCO에 대한 액세스 권한을 부여합니다.

1. Azure Portal에서 **엔터프라이즈 애플리케이션**, **모든 애플리케이션**, **EBSCO**를 차례로 선택합니다.

    ![엔터프라이즈 애플리케이션 블레이드](common/enterprise-applications.png)

2. 애플리케이션 목록에서 **EBSCO**를 선택합니다.

    ![애플리케이션 목록의 EBSCO 연결](common/all-applications.png)

3. 왼쪽 메뉴에서 **사용자 및 그룹**을 선택합니다.

    !["사용자 및 그룹" 링크](common/users-groups-blade.png)

4. **사용자 추가** 단추를 클릭한 다음, **할당 추가** 대화 상자에서 **사용자 및 그룹**을 선택합니다.

    ![할당 추가 창](common/add-assign-user.png)

5. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **Britta Simon**을 선택하고 화면 아래쪽에서 **선택** 단추를 클릭합니다.

6. SAML 어설션 및 **역할 선택** 대화 상자에서 모든 역할 값이 필요한 경우 목록에서 적절한 사용자 역할을 선택한 다음, 화면 맨 아래에 있는 **선택** 단추를 클릭합니다.

7. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.

### <a name="create-ebsco-test-user"></a>EBSCO 테스트 사용자 만들기

EBSCO의 경우 사용자 프로비저닝은 자동입니다.

**사용자 계정을 프로비전하려면 다음 단계를 수행합니다.**

Azure AD는 필요한 데이터를 EBSCO 애플리케이션으로 전달합니다. EBSCO의 사용자 프로비저닝은 자동으로 진행되거나 일회성 양식이 필요합니다. 어떤 방식인지는 클라이언트에 개인 설정이 저장된 기존 EBSCOhost 계정이 많이 있는지에 따라 좌우됩니다. 구현 동안 [EBSCO 지원 팀](mailto:sso@ebsco.com)에서 동일한 내용이 논의될 수 있습니다. 어떤 방법이든, 클라이언트는 테스트하기 전에 EBSCOhost 계정을 만들 필요가 없습니다.

   >[!Note]
   >EBSCOhost 사용자 프로비저닝/개인 설정을 자동화할 수 있습니다. Just-In-Time 사용자 프로비저닝에 대해서는 [EBSCO 지원 팀](mailto:sso@ebsco.com)에 문의하세요. 

### <a name="test-single-sign-on"></a>Single Sign-On 테스트 

이 섹션에서는 액세스 패널을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다.

1. 액세스 패널에서 EBSCO 타일을 클릭하면 EBSCO 애플리케이션에 자동으로 로그온됩니다.
액세스 패널에 대한 자세한 내용은 [액세스 패널 소개](../user-help/active-directory-saas-access-panel-introduction.md)를 참조하세요.

2. 애플리케이션에 로그인한 후에 오른쪽 위 구석에 있는 **로그인** 단추를 클릭합니다.

    ![애플리케이션 목록의 EBSCO 로그인](./media/ebsco-tutorial/tutorial_ebsco_signin.png)
 
3. **지금 기관 계정에 기존 MyEBSCOhost 계정 연결** 또는 **새 MyEBSCOhost 계정을 만든 후 기관 계정에 연결**을 사용하여 기관/SAML 로그인을 연결하라는 일회성 메시지가 나타납니다. 계정은 EBSCOhost 애플리케이션의 개인 설정에 사용됩니다. **새 계정 만들기** 옵션을 선택하면 개인 설정에 대한 양식이 아래 스크린샷에 표시된 것처럼 SAML 응답의 값으로 미리 채워진 것을 볼 수 있습니다. **'계속'** 을 클릭하여 이 선택 내용을 저장합니다.
    
     ![애플리케이션 목록의 EBSCO 사용자](./media/ebsco-tutorial/tutorial_ebsco_user.png)

1. 위 설정이 완료되면 쿠키/캐시를 지우고 다시 로그인합니다. 다시 수동으로 로그인할 필요가 없으며, 개인 설정이 저장됩니다.

## <a name="additional-sesources"></a>추가 리소스

- [Azure Active Directory와 SaaS Apps를 통합하는 방법에 대한 자습서 목록](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory로 애플리케이션 액세스 및 Single Sign-On을 구현하는 방법](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory의 조건부 액세스란?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

