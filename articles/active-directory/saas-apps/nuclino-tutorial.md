---
title: '자습서: Nuclino와 Azure Active Directory 통합 | Microsoft Docs'
description: Azure Active Directory와 Nuclino 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 74bbab82-5581-4dcf-8806-78f77c746968
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/05/2019
ms.author: jeedes
ms.openlocfilehash: 4788b65201792292d79cd8c4d1b22f22c5e67eb6
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/08/2019
ms.locfileid: "59278805"
---
# <a name="tutorial-azure-active-directory-integration-with-nuclino"></a>자습서: Nuclino와 Azure Active Directory 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 Nuclino를 통합하는 방법에 대해 알아봅니다.
Nuclino를 Azure AD와 통합하면 다음과 같은 이점이 제공됩니다.

* Nuclino에 대한 액세스 권한이 있는 사용자를 Azure AD에서 제어할 수 있습니다.
* 사용자가 자신의 Azure AD 계정으로 Nuclino에 자동으로 로그인(Single Sign-On)되도록 설정할 수 있습니다.
* 단일 중앙 위치인 Azure Portal에서 계정을 관리할 수 있습니다.

Azure AD와의 SaaS 앱 통합에 대한 자세한 내용은 [Azure Active Directory를 사용한 응용 프로그램 액세스 및 Single Sign-On](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)을 참조하세요.
Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/) 계정을 만듭니다.

## <a name="prerequisites"></a>필수 조건

Nuclino와 Azure AD 통합을 구성하려면 다음 항목이 필요합니다.

* Azure AD 구독 Azure AD 환경이 없으면 [여기](https://azure.microsoft.com/pricing/free-trial/)에서 1개월 평가판을 구할 수 있습니다.
* Nuclino Single Sign-on이 설정된 구독

## <a name="scenario-description"></a>시나리오 설명

이 자습서에서는 테스트 환경에서 Azure AD Single Sign-On을 구성하고 테스트합니다.

* Nuclino에서 **SP** 및 **IDP** 시작 SSO를 지원합니다.

* Nuclino가 **Just-In-Time** 사용자 프로비저닝 지원

## <a name="adding-nuclino-from-the-gallery"></a>갤러리에서 Nuclino 추가

Nuclino의 Azure AD 통합을 구성하려면 갤러리의 Nuclino를 관리되는 SaaS 앱 목록에 추가해야 합니다.

**갤러리에서 Nuclino를 추가하려면 다음 단계를 수행합니다.**

1. **[Azure Portal](https://portal.azure.com)** 의 왼쪽 탐색 창에서 **Azure Active Directory** 아이콘을 클릭합니다.

    ![Azure Active Directory 단추](common/select-azuread.png)

2. **엔터프라이즈 응용 프로그램**으로 이동한 다음, **모든 응용 프로그램** 옵션을 선택합니다.

    ![엔터프라이즈 애플리케이션 블레이드](common/enterprise-applications.png)

3. 새 애플리케이션을 추가하려면 대화 상자 맨 위 있는 **새 애플리케이션** 단추를 클릭합니다.

    ![새 애플리케이션 단추](common/add-new-app.png)

4. 검색 상자에 **Nuclino**를 입력하고 결과 패널에서 **Nuclino**를 선택한 후 **추가** 단추를 클릭하여 응용 프로그램을 추가합니다.

     ![결과 목록의 Nuclino](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성 및 테스트

이 섹션에서는 **Britta Simon**이라는 테스트 사용자를 기반으로 Nuclino에서 Azure AD Single Sign-On을 구성하고 테스트합니다.
Single Sign-On이 작동하려면 Azure AD 사용자와 Nuclino의 관련 사용자 간에 연결 관계를 설정해야 합니다.

Nuclino에서 Azure AD Single Sign-On을 구성하고 테스트하려면 다음 구성 요소를 완료해야 합니다.

1. **[Azure AD Single Sign-On 구성](#configure-azure-ad-single-sign-on)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
2. **[Nuclino Single Sign-On 구성](#configure-nuclino-single-sign-on)** - 애플리케이션 쪽에서 Single Sign-On 설정을 구성합니다.
3. **[Azure AD 테스트 사용자 만들기](#create-an-azure-ad-test-user)** - Britta Simon으로 Azure AD Single Sign-On을 테스트하는 데 사용합니다.
4. **[Azure AD 테스트 사용자 할당](#assign-the-azure-ad-test-user)** - Britta Simon이 Azure AD Single Sign-on을 사용할 수 있도록 합니다.
5. **[Nuclino 테스트 사용자 만들기](#create-nuclino-test-user)** - Britta Simon의 Azure AD 표현과 연결된 해당 사용자를 Nuclino에 만듭니다.
6. **[Single Sign-On 테스트](#test-single-sign-on)** - 구성이 작동하는지 여부를 확인합니다.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성

이 섹션에서는 Azure Portal에서 Azure AD Single Sign-On을 사용하도록 설정합니다.

Nuclino에서 Azure AD Single Sign-On을 구성하려면 다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com/)의 **Nuclino** 애플리케이션 통합 페이지에서 **Single Sign-On**을 선택합니다.

    ![Single Sign-On 구성 링크](common/select-sso.png)

2. **Single Sign-On 방법 선택** 대화 상자에서 **SAML/WS-Fed** 모드를 선택하여 Single Sign-On을 사용하도록 설정합니다.

    ![Single Sign-On 선택 모드](common/select-saml-option.png)

3. **SAML로 Single Sign-On 설정** 페이지에서 **편집** 아이콘을 클릭하여 **기본 SAML 구성** 대화 상자를 엽니다.

    ![기본 SAML 구성 편집](common/edit-urls.png)

4. **IDP** 시작 모드에서 애플리케이션을 구성하려면 **기본 SAML 구성** 섹션에서 다음 단계를 수행합니다.

    ![Nuclino 도메인 및 URL Single Sign-On 정보](common/idp-intiated.png)

    a. **식별자** 텍스트 상자에서 다음 패턴을 사용하여 URL을 입력합니다. `https://api.nuclino.com/api/sso/<UNIQUE-ID>/metadata`

    b. **회신 URL** 텍스트 상자에 다음 패턴을 사용하여 URL을 입력합니다. `https://api.nuclino.com/api/sso/<UNIQUE-ID>/acs`

    > [!NOTE]
    > 이러한 값은 실제 값이 아닙니다. 자습서 뒷부분에 설명된 **인증** 섹션의 실제 식별자, 응답 URL로 이 값을 업데이트합니다.

5. **SP** 시작 모드에서 애플리케이션을 구성하려면 **추가 URL 설정**을 클릭하고 다음 단계를 수행합니다.

    ![Nuclino 도메인 및 URL Single Sign-On 정보](common/metadata-upload-additional-signon.png)

    **로그인 URL** 텍스트 상자에서 `https://app.nuclino.com/<UNIQUE-ID>/login` 패턴을 사용하여 URL을 입력합니다.

    > [!NOTE]
    > 이 값은 실제 값이 아닙니다. 이 값을 실제 로그온 URL로 업데이트합니다. 이 값을 얻으려면 [Nuclino 클라이언트 지원 팀](mailto:contact@nuclino.com)에 문의하세요. Azure Portal의 **기본 SAML 구성** 섹션에 표시된 패턴을 참조할 수도 있습니다.

6. Nuclino 애플리케이션은 특정 서식에서 SAML 어설션을 예상하며, SAML 토큰 특성 구성에 사용자 할당 특성 매핑을 추가해야 합니다. 다음 스크린샷에서는 기본 특성의 목록을 보여 줍니다.  **편집**  아이콘을 클릭하여  **사용자 특성**  대화 상자를 엽니다.

    ![이미지](common/edit-attribute.png)

7. 위에서 언급한 특성 외에도, Nuclino 애플리케이션에는 SAML 응답에서 다시 전달되어야 하는 몇 가지 특성이 추가로 필요합니다. **사용자 특성** 대화 상자의 **사용자 클레임** 섹션에서 다음 단계를 수행하여 아래 표와 같은 SAML 토큰 특성을 추가합니다.

    | Name |  원본 특성|
    | ---------------| --------- |
    | first_name | user.givenname |
    | last_name | user.surname |

    a. **새 클레임 추가**를 클릭하여 **사용자 클레임 관리** 대화 상자를 엽니다.

    ![이미지](common/new-save-attribute.png)

    ![이미지](common/new-attribute-details.png)

    b. **이름** 텍스트 상자에서 해당 행에 표시된 특성 이름을 입력합니다.

    다. **네임스페이스**를 비워 둡니다.

    d. 원본을 **특성**으로 선택합니다.

    e. **원본 특성** 목록에서 해당 행에 표시된 특성 값을 입력합니다.

    f. **확인**을 클릭합니다.

    g. **저장**을 클릭합니다.

8. **SAML로 Single Sign-On 설정** 페이지의 **SAML 서명 인증서** 섹션에서 **다운로드**를 클릭하여 요구 사항에 따라 제공된 옵션에서 **인증서(Base64)** 를 다운로드한 다음, 컴퓨터에 저장합니다.

    ![인증서 다운로드 링크](common/certificatebase64.png)

9. **Nuclino 설정** 섹션에서 요구 사항에 따라 적절한 URL을 복사합니다.

    ![구성 URL 복사](common/copy-configuration-urls.png)

    a. 로그인 URL

    b. Azure AD 식별자

    다. 로그아웃 URL

### <a name="configure-nuclino-single-sign-on"></a>Nuclino Single Sign-on 구성

1. 다른 웹 브라우저 창에서 Nuclino 회사 사이트에 관리자로 로그인합니다.

2. **ICON**을 클릭합니다.

    ![Nuclino 구성](./media/nuclino-tutorial/configure1.png)

3. **Azure AD SSO**를 클릭하고 드롭다운에서 **팀 설정**을 클릭합니다.

    ![Nuclino 구성](./media/nuclino-tutorial/configure2.png)

4. 왼쪽 탐색 창에서 **인증**을 선택합니다.

    ![Nuclino 구성](./media/nuclino-tutorial/configure3.png)

5. **인증** 섹션에서 다음 단계를 수행합니다.

    ![Nuclino 구성](./media/nuclino-tutorial/configure4.png)

    a. **SAML 기반 SSO(Single Sign-On)** 를 선택합니다.

    b. **ACS URL(이 URL을 복사하여 SSO 공급자에 붙여넣어야 함)** 값을 복사하여 Azure Portal의 **기본 SAML 구성** 섹션에 있는 **회신 URL** 텍스트 상자에 붙여넣습니다.

    다. **엔터티 ID(이 URL을 복사하여 SSO 공급자에 붙여넣어야 함)** 값을 복사하여 Azure Portal의 **기본 SAML 구성** 섹션에 있는 **식별자** 텍스트 상자에 붙여넣습니다.

    d. **SSO URL** 텍스트 상자에 Azure Portal에서 복사한 **로그인 URL** 값을 붙여넣습니다.

    e. Azure Portal에서 복사한 **Azure AD 식별자** 값을 **엔터티 ID** 텍스트 상자에 붙여넣습니다.

    f. 다운로드한 **인증서(Base64)** 파일을 메모장에서 엽니다. 내용을 클립보드로 복사한 다음, **공용 인증서** 텍스트 상자에 붙여넣습니다.

    g. **변경 내용 저장**을 클릭합니다.

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

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 Britta Simon에게 Nuclino에 대한 액세스 권한을 부여합니다.

1. Azure Portal에서 **엔터프라이즈 애플리케이션**, **모든 애플리케이션** 및 **Nuclino**를 차례로 선택합니다.

    ![엔터프라이즈 애플리케이션 블레이드](common/enterprise-applications.png)

2. 애플리케이션 목록에서 **Nuclino**를 선택합니다.

    ![애플리케이션 목록의 Nuclino 링크](common/all-applications.png)

3. 왼쪽 메뉴에서 **사용자 및 그룹**을 선택합니다.

    !["사용자 및 그룹" 링크](common/users-groups-blade.png)

4. **사용자 추가** 단추를 클릭한 다음, **할당 추가** 대화 상자에서 **사용자 및 그룹**을 선택합니다.

    ![할당 추가 창](common/add-assign-user.png)

5. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **Britta Simon**을 선택하고 화면 아래쪽에서 **선택** 단추를 클릭합니다.

6. SAML 어설션 및 **역할 선택** 대화 상자에서 모든 역할 값이 필요한 경우 목록에서 적절한 사용자 역할을 선택한 다음, 화면 맨 아래에 있는 **선택** 단추를 클릭합니다.

7. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.

### <a name="create-nuclino-test-user"></a>Nuclino 테스트 사용자 만들기

이 섹션에서는 Nuclino에서 Britta Simon이라는 사용자를 만듭니다. Nuclino는 기본적으로 사용하도록 설정되는 Just-In-Time 사용자 프로비전을 지원합니다. 이 섹션에 작업 항목이 없습니다. Nuclino에 사용자가 아직 없는 경우 인증 후에 새 사용자가 만들어집니다.

> [!Note]
> 사용자를 수동으로 만들어야 하는 경우 [Nuclino 지원 팀](mailto:contact@nuclino.com)에 문의하세요.

### <a name="test-single-sign-on"></a>Single Sign-On 테스트 

이 섹션에서는 액세스 패널을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다.

액세스 패널에서 Nuclino 타일을 클릭하면 SSO를 설정한 Nuclino에 자동으로 로그인되어야 합니다. 액세스 패널에 대한 자세한 내용은 [액세스 패널 소개](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)를 참조하세요.

## <a name="additional-resources"></a>추가 리소스

- [Azure Active Directory와 SaaS Apps를 통합하는 방법에 대한 자습서 목록](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory로 애플리케이션 액세스 및 Single Sign-On이란 무엇입니까?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory의 조건부 액세스란?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

