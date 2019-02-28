---
title: '자습서: Azure Active Directory와 Jamf Pro 통합 | Microsoft Docs'
description: Azure Active Directory와 Jamf Pro 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 35e86d08-c29e-49ca-8545-b0ff559c5faf
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/19/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 78949ad5eeae9b497d9703b5a79616d755f12c6d
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/26/2019
ms.locfileid: "56881042"
---
# <a name="tutorial-azure-active-directory-integration-with-jamf-pro"></a>자습서: Azure Active Directory와 Jamf Pro 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 Jamf Pro를 통합하는 방법에 대해 알아봅니다.
Jamf Pro를 Azure AD와 통합하면 다음과 같은 이점이 제공됩니다.

* Jamf Pro에 대한 액세스 권한이 있는 사용자를 Azure AD에서 제어할 수 있습니다.
* 사용자가 해당 Azure AD 계정으로 Jamf Pro에 자동으로 로그온(Single Sign-On)되도록 설정할 수 있습니다.
* 단일 중앙 위치인 Azure Portal에서 계정을 관리할 수 있습니다.

Azure AD와의 SaaS 앱 통합에 대한 자세한 내용은 [Azure Active Directory를 사용한 응용 프로그램 액세스 및 Single Sign-On](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)을 참조하세요.
Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/) 계정을 만듭니다.

## <a name="prerequisites"></a>필수 조건

Azure AD와 Jamf Pro의 통합을 구성하려면 다음 항목이 필요합니다.

* Azure AD 구독 Azure AD 환경이 없으면 [여기](https://azure.microsoft.com/pricing/free-trial/)에서 1개월 평가판을 구할 수 있습니다.
* Jamf Pro Single Sign-On을 사용하도록 설정된 구독

## <a name="scenario-description"></a>시나리오 설명

이 자습서에서는 테스트 환경에서 Azure AD Single Sign-On을 구성하고 테스트합니다.

* Jamf Pro에서 **SP 및 IDP** 시작 SSO를 지원합니다.

## <a name="adding-jamf-pro-from-the-gallery"></a>갤러리에서 Jamf Pro 추가

Azure AD에 Jamf Pro의 통합을 구성하려면 갤러리의 Jamf Pro를 관리되는 SaaS 앱 목록에 추가해야 합니다.

**갤러리에서 Jamf Pro를 추가하려면 다음 단계를 수행합니다.**

1. **[Azure Portal](https://portal.azure.com)** 의 왼쪽 탐색 창에서 **Azure Active Directory** 아이콘을 클릭합니다.

    ![Azure Active Directory 단추](common/select-azuread.png)

2. **엔터프라이즈 응용 프로그램**으로 이동한 다음, **모든 응용 프로그램** 옵션을 선택합니다.

    ![엔터프라이즈 애플리케이션 블레이드](common/enterprise-applications.png)

3. 새 애플리케이션을 추가하려면 대화 상자 맨 위 있는 **새 애플리케이션** 단추를 클릭합니다.

    ![새 애플리케이션 단추](common/add-new-app.png)

4. 검색 상자에 **Jamf Pro**를 입력하고 결과 패널에서 **Jamf Pro**를 선택한 다음, **추가** 단추를 클릭하여 애플리케이션을 추가합니다.

     ![결과 목록의 Jamf Pro](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성 및 테스트

이 섹션에서는 **Britta Simon**이라는 테스트 사용자를 기반으로 하여 Jamf Pro에서 Azure AD Single Sign-On을 구성하고 테스트합니다.
Single Sign-On이 작동하려면 Azure AD 사용자와 Jamf Pro의 관련 사용자 간에 연결 관계를 설정해야 합니다.

Jamf Pro에서 Azure AD Single Sign-On을 구성하고 테스트하려면 다음 구성 요소를 완료해야 합니다.

1. **[Azure AD Single Sign-On 구성](#configure-azure-ad-single-sign-on)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
2. **[Jamf Pro Single Sign-On 구성](#configure-jamf-pro-single-sign-on)** - 애플리케이션 쪽에서 Single Sign-On 설정을 구성합니다.
3. **[Azure AD 테스트 사용자 만들기](#create-an-azure-ad-test-user)** - Britta Simon으로 Azure AD Single Sign-On을 테스트하는 데 사용합니다.
4. **[Azure AD 테스트 사용자 할당](#assign-the-azure-ad-test-user)** - Britta Simon이 Azure AD Single Sign-on을 사용할 수 있도록 합니다.
5. **[Jamf Pro 테스트 사용자 만들기](#create-jamf-pro-test-user)** - Britta Simon의 Azure AD 표현과 연결된 해당 사용자를 Jamf Pro에 만듭니다.
6. **[Single Sign-On 테스트](#test-single-sign-on)** - 구성이 작동하는지 여부를 확인합니다.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성

이 섹션에서는 Azure Portal에서 Azure AD Single Sign-On을 사용하도록 설정합니다.

Jamf Pro에서 Azure AD Single Sign-On을 구성하려면 다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com/)의 **Jamf Pro** 애플리케이션 통합 페이지에서 **Single Sign-On**을 선택합니다.

    ![Single Sign-On 구성 링크](common/select-sso.png)

2. **Single Sign-On 방법 선택** 대화 상자에서 **SAML/WS-Fed** 모드를 선택하여 Single Sign-On을 사용하도록 설정합니다.

    ![Single Sign-On 선택 모드](common/select-saml-option.png)

3. **SAML로 Single Sign-On 설정** 페이지에서 **편집** 아이콘을 클릭하여 **기본 SAML 구성** 대화 상자를 엽니다.

    ![기본 SAML 구성 편집](common/edit-urls.png)

4. **IDP** 시작 모드에서 구성하려면 **기본 SAML 구성** 섹션에서 다음 단계를 수행합니다.

    ![Jamf Pro 도메인 및 URL Single Sign-On 정보](common/idp-intiated.png)

    a. **식별자** 텍스트 상자에서 `https://<subdomain>.jamfcloud.com/saml/metadata` 패턴을 사용하여 URL을 입력합니다.

    b. **회신 URL** 텍스트 상자에서 `https://<subdomain>.jamfcloud.com/saml/SSO` 패턴을 사용하여 URL을 입력합니다.

5. **SP** 시작 모드에서 애플리케이션을 구성하려면 **추가 URL 설정**을 클릭하고 다음 단계를 수행합니다.

    **로그인 URL** 텍스트 상자에서 `https://<subdomain>.jamfcloud.com` 패턴을 사용하여 URL을 입력합니다.

    ![Jamf Pro 도메인 및 URL Single Sign-On 정보](common/metadata-upload-additional-signon.png)

    > [!NOTE]
    > 이러한 값은 실제 값이 아닙니다. 이러한 값을 실제 식별자, 회신 URL 및 로그온 URL로 업데이트합니다. 자습서의 뒷부분에 설명하는 실제 식별자 값을 Jamf Pro 포털의 **Single Sign On** 섹션에서 가져옵니다. 실제 **하위 도메인** 값을 식별자 값에서 추출하여 해당 **하위 도메인** 정보를 로그온 URL 및 회신 URL에서 사용할 수 있습니다. Azure Portal의 **기본 SAML 구성** 섹션에 표시된 패턴을 참조할 수도 있습니다.

6. **SAML로 Single Sign-On 설정** 페이지의 **SAML 서명 인증서** 섹션에서 복사 단추를 클릭하여 **앱 페더레이션 메타데이터 URL**을 복사한 후 컴퓨터에 저장합니다.

    ![인증서 다운로드 링크](common/copy-metadataurl.png)

### <a name="configure-jamf-pro-single-sign-on"></a>Jamf Pro Single Sign-On 구성

1. Jamf Pro 내에서 구성을 자동화하려면 **확장 설치**를 클릭하여 **내 앱 보안 로그인 브라우저 확장**을 설치해야 합니다.

    ![이미지](./media/jamfprosamlconnector-tutorial/install_extension.png)

2. 브라우저에 확장을 추가한 후 **Jamf Pro 설정**을 클릭하면 Jamf Pro 응용 프로그램으로 이동됩니다. 응용 프로그램에서 관리자 자격 증명을 입력하여 Jamf Pro에 로그인합니다. 브라우저 확장에서 애플리케이션을 자동으로 구성하고 3-7단계를 자동화합니다.

    ![이미지](./media/jamfprosamlconnector-tutorial/d1_saml.png)

3. Jamf Pro를 수동으로 설치하려는 경우 새 웹 브라우저 창을 열고 Jamf Pro 회사 사이트에 관리자로 로그인한 후에 다음 단계를 수행합니다.

4. 페이지의 오른쪽 위 모서리에 있는 **설정 아이콘**을 클릭합니다.

    ![Jamf Pro 구성](./media/jamfprosamlconnector-tutorial/configure1.png)

5. **Single Sign-On**을 클릭합니다.

    ![Jamf Pro 구성](./media/jamfprosamlconnector-tutorial/configure2.png)

6. **Single Sign-On** 페이지에서 다음 단계를 수행합니다.

    ![Jamf Pro 단일](./media/jamfprosamlconnector-tutorial/tutorial_jamfprosamlconnector_single.png)

    a. **Jamf Pro 서버**를 선택하여 Single Sign-On 액세스를 사용하도록 설정합니다.

    b. **모든 사용자에 바이패스 허용**을 선택하면 사용자가 인증을 위한 ID 공급자 로그인 페이지로 리디렉션되지 않지만, 대신 Jamf Pro에 직접 로그인할 수 있습니다. 사용자가 ID 공급자를 통해 Jamf Pro에 액세스하려고 시도하는 경우 IdP에서 시작한 SSO 인증 및 권한 부여가 발생합니다.

    다. **사용자 매핑: SAML에 대해 **NameID** 옵션을 선택합니다. 기본적으로 이 설정은 **NameID**로 설정되어 있지만 사용자 지정 특성을 정의할 수도 있습니다.

    d. **사용자 매핑: JAMF PRO에 대해 **이메일**을 선택합니다. Jamf Pro는 사용자 및 그룹에 의한 방법으로 IdP에서 보낸 SAML 특성을 매핑합니다. 사용자가 Jamf Pro에 액세스하려고 시도할 때 Jamf Pro는 기본적으로 ID 공급자로부터 사용자에 대한 정보를 가져와서 Jamf Pro 사용자 계정과 대조해 봅니다. 들어오는 사용자 계정이 Jamf Pro에 존재하지 않으면 그룹 이름 일치가 발생합니다.

    e. `http://schemas.microsoft.com/ws/2008/06/identity/claims/groups` 값을 **그룹 특성 이름** 텍스트 상자에 붙여넣습니다.

7. 같은 페이지에서 **Single Sign-On** 섹션의 **ID 공급 기업**까지 스크롤하여 다음 단계를 수행합니다.

    ![Jamf Pro 구성](./media/jamfprosamlconnector-tutorial/configure3.png)

    a. **ID 공급자** 드롭다운 메뉴에서 **기타**를 옵션으로 선택합니다.

    b. **다른 공급자** 텍스트 상자에 **Azure AD**를 입력합니다.

    다. **ID 공급자 메타데이터 소스** 드롭다운에서 **메타데이터 URL**을 옵션으로 선택하고, 다음 텍스트 상자에 Azure Portal에서 복사한 **앱 페더레이션 메타데이터 URL** 값을 붙여넣습니다.

    d. **엔터티 ID** 값을 복사하고 Azure Portal의 **Jamf Pro 도메인 및 URL** 섹션에 있는 **식별자(엔터티 ID)** 텍스트 상자에 붙여넣습니다.

    > [!NOTE]
    > 여기서 흐릿한 값은 하위 도메인 부분입니다. 이 값을 사용하여 Azure Portal의 **Jamf Pro 도메인 및 URL** 섹션에서 로그온 URL 및 회신 URL을 완료합니다.

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
  
    b. **사용자 이름** 필드에 **brittasimon@yourcompanydomain.extension**을 입력합니다.  
    예를 들어 BrittaSimon@contoso.com

    c. **암호 표시** 확인란을 선택한 다음, [암호] 상자에 표시된 값을 적어둡니다.

    d. **만들기**를 클릭합니다.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD 테스트 사용자 할당

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 Britta Simon에게 Jamf Pro에 대한 액세스 권한을 부여합니다.

1. Azure Portal에서 **엔터프라이즈 애플리케이션**을 선택한 다음, **모든 애플리케이션**을 선택하고 **Jamf Pro**를 선택합니다.

    ![엔터프라이즈 애플리케이션 블레이드](common/enterprise-applications.png)

2. 애플리케이션 목록에서 **Jamf Pro**를 선택합니다.

    ![애플리케이션 목록의 Jamf Pro 링크](common/all-applications.png)

3. 왼쪽 메뉴에서 **사용자 및 그룹**을 선택합니다.

    !["사용자 및 그룹" 링크](common/users-groups-blade.png)

4. **사용자 추가** 단추를 클릭한 다음, **할당 추가** 대화 상자에서 **사용자 및 그룹**을 선택합니다.

    ![할당 추가 창](common/add-assign-user.png)

5. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **Britta Simon**을 선택하고 화면 아래쪽에서 **선택** 단추를 클릭합니다.

6. SAML 어설션 및 **역할 선택** 대화 상자에서 모든 역할 값이 필요한 경우 목록에서 적절한 사용자 역할을 선택한 다음, 화면 맨 아래에 있는 **선택** 단추를 클릭합니다.

7. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.

### <a name="create-jamf-pro-test-user"></a>Jamf Pro 테스트 사용자 만들기

Azure AD 사용자가 Jamf Pro에 로그인할 수 있도록 하려면 Jamf Pro로 프로비전되어야 합니다. Jamf Pro의 경우 프로비전은 수동 작업입니다.

**사용자 계정을 프로비전하려면 다음 단계를 수행합니다.**

1. Jamf Pro 회사 사이트에 관리자 권한으로 로그인합니다.

2. 페이지의 오른쪽 위 모서리에 있는 **설정 아이콘**을 클릭합니다.

    ![직원 추가](./media/jamfprosamlconnector-tutorial/configure1.png)

3. **Jamf Pro 사용자 계정 및 그룹**을 클릭합니다.

    ![직원 추가](./media/jamfprosamlconnector-tutorial/user1.png)

4. **새로 만들기**를 클릭합니다.

    ![직원 추가](./media/jamfprosamlconnector-tutorial/user2.png)

5. **Standard 계정 만들기**를 선택합니다.

    ![직원 추가](./media/jamfprosamlconnector-tutorial/user3.png)

6. **새 계정** 대화 상자에서 다음 단계를 수행합니다.

    ![직원 추가](./media/jamfprosamlconnector-tutorial/user4.png)

    a. **사용자 이름** 텍스트 상자에 BrittaSimon의 전체 이름을 입력합니다.

    b. 사용자 조직별로 **액세스 수준**, **권한 설정** 및 **액세스 상태**의 적절한 옵션을 선택합니다.

    다. **전체 이름** 텍스트 상자에 Britta Simon의 전체 이름을 입력합니다.

    d. **이메일 주소** 텍스트 상자에 Britta Simon 계정의 이메일 주소를 입력합니다.

    e. **암호** 텍스트 상자에 사용자의 암호를 입력합니다.

    f. **암호 확인** 텍스트 상자에 사용자의 암호를 입력합니다.

    g. **저장**을 클릭합니다.

### <a name="test-single-sign-on"></a>Single Sign-On 테스트 

이 섹션에서는 액세스 패널을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다.

액세스 패널에서 Jamf Pro 타일을 클릭하면 SSO를 설정한 Jamf Pro에 자동으로 로그인됩니다. 액세스 패널에 대한 자세한 내용은 [액세스 패널 소개](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)를 참조하세요.

## <a name="additional-resources"></a>추가 리소스

- [Azure Active Directory와 SaaS Apps를 통합하는 방법에 대한 자습서 목록](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory로 애플리케이션 액세스 및 Single Sign-On을 구현하는 방법](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory의 조건부 액세스란?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
