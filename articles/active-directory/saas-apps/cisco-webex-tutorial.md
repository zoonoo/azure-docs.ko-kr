---
title: '자습서: Cisco Webex와 Azure Active Directory 통합 | Microsoft Docs'
description: Azure Active Directory 및 Cisco Webex 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 26704ca7-13ed-4261-bf24-fd6252e2072b
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 1/4/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2bdd51ee6f3ef69289bad83e7cb344df41020ac8
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/26/2019
ms.locfileid: "56871488"
---
# <a name="tutorial-azure-active-directory-integration-with-cisco-webex"></a>자습서: Cisco Webex와 Azure Active Directory 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 Cisco Webex를 통합하는 방법에 대해 알아봅니다.
Cisco Webex를 Azure AD와 통합하면 다음과 같은 이점이 제공됩니다.

* Cisco Webex에 대한 액세스 권한이 있는 사용자를 Azure AD에서 제어할 수 있습니다.
* 사용자가 해당 Azure AD 계정으로 Cisco Webex에 자동으로 로그인(Single Sign-On)되도록 설정할 수 있습니다.
* 단일 중앙 위치인 Azure Portal에서 계정을 관리할 수 있습니다.

Azure AD와의 SaaS 앱 통합에 대한 자세한 내용은 [Azure Active Directory를 사용한 응용 프로그램 액세스 및 Single Sign-On](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)을 참조하세요.
Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/) 계정을 만듭니다.

## <a name="prerequisites"></a>필수 조건

Cisco Webex와 Azure AD 통합을 구성하려면 다음 항목이 필요합니다.

* Azure AD 구독 Azure AD 환경이 없으면 [여기](https://azure.microsoft.com/pricing/free-trial/)에서 1개월 평가판을 구할 수 있습니다.
* Cisco Webex Single Sign-On을 사용하도록 설정된 구독

## <a name="scenario-description"></a>시나리오 설명

이 자습서에서는 테스트 환경에서 Azure AD Single Sign-On을 구성하고 테스트합니다.

* Cisco Webex에서 **SP** 시작 SSO를 지원합니다.

* Cisco Webex에서 **Just-In-Time** 사용자 프로비전을 지원합니다.

* Cisco Webex에서 **자동** 사용자 프로비전을 지원합니다.

## <a name="adding-cisco-webex-from-the-gallery"></a>갤러리에서 Cisco Webex 추가

Cisco Webex의 Azure AD 통합을 구성하려면 갤러리의 Cisco Webex를 관리되는 SaaS 앱 목록에 추가해야 합니다.

**갤러리에서 Cisco Webex를 추가하려면 다음 단계를 수행합니다.**

1. **[Azure Portal](https://portal.azure.com)** 의 왼쪽 탐색 창에서 **Azure Active Directory** 아이콘을 클릭합니다.

    ![Azure Active Directory 단추](common/select-azuread.png)

2. **엔터프라이즈 응용 프로그램**으로 이동한 다음, **모든 응용 프로그램** 옵션을 선택합니다.

    ![엔터프라이즈 애플리케이션 블레이드](common/enterprise-applications.png)

3. 새 애플리케이션을 추가하려면 대화 상자 맨 위 있는 **새 애플리케이션** 단추를 클릭합니다.

    ![새 애플리케이션 단추](common/add-new-app.png)

4. 검색 상자에서 **Cisco Webex**를 입력하고, 결과 패널에서 **Cisco Webex**를 선택한 다음, **추가** 단추를 클릭하여 애플리케이션을 추가합니다.

     ![결과 목록의 Cisco Webex](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성 및 테스트

이 섹션에서는 **Britta Simon**이라는 테스트 사용자를 기반으로 하여 Cisco Webex에서 Azure AD Single Sign-On을 구성하고 테스트합니다.
Single Sign-On이 작동하려면 Azure AD 사용자와 Cisco Webex의 관련 사용자 간에 연결 관계를 설정해야 합니다.

Cisco Webex에서 Azure AD Single Sign-On을 구성하고 테스트하려면 다음 구성 요소를 완료해야 합니다.

1. **[Azure AD Single Sign-On 구성](#configure-azure-ad-single-sign-on)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
2. **[Cisco Webex Single Sign-On 구성](#configure-cisco-webex-single-sign-on)** - 애플리케이션 쪽에서 Single Sign-On 설정을 구성합니다.
3. **[Azure AD 테스트 사용자 만들기](#create-an-azure-ad-test-user)** - Britta Simon으로 Azure AD Single Sign-On을 테스트하는 데 사용합니다.
4. **[Azure AD 테스트 사용자 할당](#assign-the-azure-ad-test-user)** - Britta Simon이 Azure AD Single Sign-on을 사용할 수 있도록 합니다.
5. **[Cisco Webex 테스트 사용자 만들기](#create-cisco-webex-test-user)** - Azure AD 표현과 연결된 Britta Simon에 해당하는 사용자를 Cisco Webex에 만듭니다.
6. **[Single Sign-On 테스트](#test-single-sign-on)** - 구성이 작동하는지 여부를 확인합니다.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성

이 섹션에서는 Azure Portal에서 Azure AD Single Sign-On을 사용하도록 설정합니다.

Cisco Webex에서 Azure AD Single Sign-On을 구성하려면 다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com/)의 **Cisco Webex** 애플리케이션 통합 페이지에서 **Single Sign-On**을 선택합니다.

    ![Single Sign-On 구성 링크](common/select-sso.png)

2. **Single Sign-On 방법 선택** 대화 상자에서 **SAML/WS-Fed** 모드를 선택하여 Single Sign-On을 사용하도록 설정합니다.

    ![Single Sign-On 선택 모드](common/select-saml-option.png)

3. 다른 웹 브라우저 창에서 Cisco Webex 회사 사이트에 관리자로 로그인합니다.

4. 메뉴의 왼쪽에서 **설정**을 클릭합니다.

    ![Configure Single Sign-On](./media/cisco-webex-tutorial/tutorial_cisco_webex_10.png)

5. 설정 페이지의 **인증** 섹션에서 아래로 스크롤하여 **수정**을 클릭합니다.

    ![Configure Single Sign-On](./media/cisco-webex-tutorial/tutorial_cisco_webex_14.png)

6. **타사 ID 공급자 통합 (고급)** 을 선택하고 다음 화면으로 이동합니다.

    ![Configure Single Sign-On](./media/cisco-webex-tutorial/tutorial_cisco_webex_15.png)

7. **디렉터리 메타데이터 내보내기** 페이지에서 **메타데이터 파일 다운로드**를 클릭하여 메타데이터 파일을 다운로드합니다.

    ![Configure Single Sign-On](./media/cisco-webex-tutorial/tutorial_cisco_webex_16.png)

8. Azure Portal의 **SAML로 Single Sign-On 설정** 페이지에서 **편집** 아이콘을 클릭하여 **기본 SAML 구성** 대화 상자를 엽니다.

    ![기본 SAML 구성 편집](common/edit-urls.png)

9. **기본 SAML 구성** 섹션에서 다음 단계를 수행하여 다운로드한 **서비스 공급자 메타데이터 파일**을 업로드하고 애플리케이션을 구성합니다.

    a. **메타데이터 파일 업로드**를 클릭합니다.

    ![메타데이터 파일 업로드](common/upload-metadata.png)

    b. **폴더 로고**를 클릭하여 메타데이터 파일을 선택하고 **업로드**를 클릭합니다.

    ![메타데이터 파일 선택](common/browse-upload-metadata.png)

    다. 서비스 공급자 메타데이터 파일이 성공적으로 업로드되면 **기본 SAML 구성** 섹션에 **식별자** 및 **회신 URL** 값이 자동으로 채워집니다.

    ![Cisco Webex 도메인 및 URL Single Sign-On 정보](common/sp-identifier-reply.png)

    **로그인 URL** 텍스트 상자에서 `https://<SUBDOMAIN>.webex.com/` 패턴을 사용하여 URL을 입력합니다.

    > [!Note]
    > 로그온 URL 값은 실제 값이 아닙니다. 이 값을 실제 로그온 URL로 업데이트합니다. 이 값을 얻으려면 [Cisco Webex 클라이언트 지원 팀](https://www.webex.co.in/support/support-overview.html)에 문의하세요.

10. Cisco Webex 애플리케이션에는 특정 형식의 SAML 어설션이 필요합니다. 이 애플리케이션에 대해 다음 클레임을 구성합니다. 응용 프로그램 통합 페이지의 **사용자 특성** 섹션에서 이러한 특성의 값을 관리할 수 있습니다. **SAML로 Single Sign-On 설정** 페이지에서 **편집** 단추를 클릭하여 **사용자 특성** 대화 상자를 엽니다.

    ![이미지](common/edit-attribute.png)

11. **사용자 특성** 대화 상자의 **사용자 클레임** 섹션에서 위의 이미지에 표시된 것과 같이 SAML 토큰 특성을 구성하고 다음 단계를 수행합니다.
    
    | 이름 | 원본 특성|
    | ---------------| --------------- | --------- |
    |   firstname    | user.givenname |
    |   lastname    | user.surname |
    |   uid    | user.mail |

    a. **새 클레임 추가**를 클릭하여 **사용자 클레임 관리** 대화 상자를 엽니다.

    ![이미지](common/new-save-attribute.png)

    ![이미지](common/new-attribute-details.png)

    b. **이름** 텍스트 상자에서 해당 행에 표시된 특성 이름을 입력합니다.

    다. **네임스페이스**를 비워 둡니다.

    d. 원본을 **특성**으로 선택합니다.

    e. **원본 특성** 목록에서 해당 행에 표시된 특성 값을 입력합니다.

    f. **확인**을 클릭합니다.

    g. **저장**을 클릭합니다.

12. **SAML로 Single Sign-On 설정** 페이지의 **SAML 서명 인증서** 섹션에서 **다운로드**를 클릭하여 요구 사항에 따라 제공된 옵션에서 **페더레이션 메타데이터 XML**을 다운로드하고 컴퓨터에 저장합니다.

    ![인증서 다운로드 링크](common/metadataxml.png)

13. **Cisco Webex 설정** 섹션에서 요구 사항에 따라 적절한 URL을 복사합니다.

    ![구성 URL 복사](common/copy-configuration-urls.png)

    a. 로그인 URL

    b. Azure AD 식별자

    다. 로그아웃 URL

### <a name="configure-cisco-webex-single-sign-on"></a>Cisco Webex Single Sign-On 구성

1. Cisco Webex 회사 사이트 관리자 페이지에서 Azure AD 메타데이터 파일을 찾아 업로드하려면 파일 브라우저 옵션을 사용합니다. 그런 다음, **메타데이터에서 인증 기관이 서명한 인증서 필요(더 안전)** 를 선택하고 다음 화면으로 이동합니다. 

    ![Configure Single Sign-On](./media/cisco-webex-tutorial/tutorial_cisco_webex_11.png)

2. **SSO 연결 테스트**를 선택하고 새 브라우저 탭이 열리면 로그인하여 Azure AD로 인증합니다.

3. **Cisco Cloud Collaboration Management** 브라우저 탭으로 돌아갑니다. 테스트에 성공하면 **이 테스트에 성공했습니다. Single Sign-On 사용 옵션**을 사용하고 **저장**을 클릭합니다.

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

    c. **암호 표시** 확인란을 선택한 다음, [암호] 상자에 표시된 값을 적어둡니다.

    d. **만들기**를 클릭합니다.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD 테스트 사용자 할당

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 Britta Simon에게 Cisco Webex에 대한 액세스 권한을 부여합니다.

1. Azure Portal에서 **엔터프라이즈 애플리케이션**, **모든 애플리케이션**, **Cisco Webex**를 차례로 선택합니다.

    ![엔터프라이즈 애플리케이션 블레이드](common/enterprise-applications.png)

2. 애플리케이션 목록에서 **Cisco Webex**를 선택합니다.

    ![애플리케이션 목록의 Cisco Webex 링크](common/all-applications.png)

3. 왼쪽 메뉴에서 **사용자 및 그룹**을 선택합니다.

    !["사용자 및 그룹" 링크](common/users-groups-blade.png)

4. **사용자 추가** 단추를 클릭한 다음, **할당 추가** 대화 상자에서 **사용자 및 그룹**을 선택합니다.

    ![할당 추가 창](common/add-assign-user.png)

5. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **Britta Simon**을 선택하고 화면 아래쪽에서 **선택** 단추를 클릭합니다.

6. SAML 어설션 및 **역할 선택** 대화 상자에서 모든 역할 값이 필요한 경우 목록에서 적절한 사용자 역할을 선택한 다음, 화면 맨 아래에 있는 **선택** 단추를 클릭합니다.

7. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.

### <a name="create-cisco-webex-test-user"></a>Cisco Webex 테스트 사용자 만들기

이 섹션에서는 Cisco Webex에서 Britta Simon이라는 사용자를 만드는 것이 목적입니다. Cisco Webex는 기본적으로 사용하도록 설정된 Just-In-Time 프로비전 및 자동 사용자 프로비전을 지원합니다. 이 섹션에 작업 항목이 없습니다. Cisco Webex에 사용자가 아직 없는 경우 Cisco Webex에 액세스하려고 할 때 새 사용자가 만들어집니다.

### <a name="test-single-sign-on"></a>Single Sign-On 테스트

이 섹션에서는 액세스 패널을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다.

액세스 패널에서 Cisco Webex 타일을 클릭하면 SSO를 설정한 Cisco Webex에 자동으로 로그인됩니다. 액세스 패널에 대한 자세한 내용은 [액세스 패널 소개](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)를 참조하세요.

## <a name="additional-resources"></a>추가 리소스

- [Azure Active Directory와 SaaS Apps를 통합하는 방법에 대한 자습서 목록](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory로 애플리케이션 액세스 및 Single Sign-On을 구현하는 방법](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory의 조건부 액세스란?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

