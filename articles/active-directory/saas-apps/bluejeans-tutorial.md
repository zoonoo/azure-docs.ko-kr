---
title: '자습서: BlueJeans와 Azure Active Directory 통합 | Microsoft Docs'
description: Azure Active Directory 및 BlueJeans 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: dfc634fd-1b55-4ba8-94a8-b8288429b6a9
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/31/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0d1c10d9df85b04cf7b8ea7e0020bc8e802a0d5d
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/17/2019
ms.locfileid: "59698931"
---
# <a name="tutorial-azure-active-directory-integration-with-bluejeans"></a>자습서: BlueJeans와 Azure Active Directory 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 BlueJeans를 통합하는 방법에 대해 알아봅니다.
BlueJeans를 Azure AD와 통합하면 다음과 같은 이점이 제공됩니다.

* BlueJeans에 대한 액세스 권한이 있는 사용자를 Azure AD에서 제어할 수 있습니다.
* 사용자가 자신의 Azure AD 계정으로 BlueJeans에 자동으로 로그온(Single Sign-On)되도록 설정할 수 있습니다.
* 단일 중앙 위치인 Azure Portal에서 계정을 관리할 수 있습니다.

Azure AD와의 SaaS 앱 통합에 대한 자세한 내용은 [Azure Active Directory를 사용한 응용 프로그램 액세스 및 Single Sign-On](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)을 참조하세요.
Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/) 계정을 만듭니다.

## <a name="prerequisites"></a>필수 조건

BlueJeans와 Azure AD 통합을 구성하려면 다음 항목이 필요합니다.

* Azure AD 구독 Azure AD 환경이 없으면 [체험 계정](https://azure.microsoft.com/free/)을 얻을 수 있습니다.
* BlueJeans Single Sign-On이 설정된 구독

## <a name="scenario-description"></a>시나리오 설명

이 자습서에서는 테스트 환경에서 Azure AD Single Sign-On을 구성하고 테스트합니다.

* BlueJeans가 **SP**에서 시작된 SSO를 지원

* BlueJeans가 [**자동화된** 사용자 프로비전](bluejeans-provisioning-tutorial.md)을 지원

## <a name="adding-bluejeans-from-the-gallery"></a>갤러리에서 BlueJeans 추가

BlueJeans의 Azure AD 통합을 구성하려면 갤러리의 BlueJeans를 관리되는 SaaS 앱 목록에 추가해야 합니다.

**갤러리에서 BlueJeans를 추가하려면 다음 단계를 수행합니다.**

1. **[Azure Portal](https://portal.azure.com)** 의 왼쪽 탐색 창에서 **Azure Active Directory** 아이콘을 클릭합니다.

    ![Azure Active Directory 단추](common/select-azuread.png)

2. **엔터프라이즈 응용 프로그램**으로 이동한 다음, **모든 응용 프로그램** 옵션을 선택합니다.

    ![엔터프라이즈 애플리케이션 블레이드](common/enterprise-applications.png)

3. 새 애플리케이션을 추가하려면 대화 상자 맨 위 있는 **새 애플리케이션** 단추를 클릭합니다.

    ![새 애플리케이션 단추](common/add-new-app.png)

4. 검색 상자에 **BlueJeans**를 입력하고 결과 패널에서 **BlueJeans**를 선택한 다음, **추가** 단추를 클릭하여 응용 프로그램을 추가합니다.

     ![결과 목록의 BlueJeans](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성 및 테스트

이 섹션에서는 **Britta Simon**이라는 테스트 사용자를 기준으로 BlueJeans에서 Azure AD Single Sign-On을 구성하고 테스트합니다.
Single Sign-On이 작동하려면 Azure AD 사용자와 BlueJeans의 관련 사용자 간에 연결 관계를 설정해야 합니다.

BlueJeans에서 Azure AD Single Sign-On을 구성하고 테스트하려면 다음 구성 요소를 완료해야 합니다.

1. **[Azure AD Single Sign-On 구성](#configure-azure-ad-single-sign-on)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
2. **[BlueJeans Single Sign-On 구성](#configure-bluejeans-single-sign-on)** - 애플리케이션 쪽에서 Single Sign-On 설정을 구성합니다.
3. **[Azure AD 테스트 사용자 만들기](#create-an-azure-ad-test-user)** - Britta Simon으로 Azure AD Single Sign-On을 테스트하는 데 사용합니다.
4. **[Azure AD 테스트 사용자 할당](#assign-the-azure-ad-test-user)** - Britta Simon이 Azure AD Single Sign-on을 사용할 수 있도록 합니다.
5. **[BlueJeans 테스트 사용자 만들기](#create-bluejeans-test-user)** - Britta Simon의 Azure AD 표현과 연결되는 대응 사용자를 BlueJeans에 만듭니다.
6. **[Single Sign-On 테스트](#test-single-sign-on)** - 구성이 작동하는지 여부를 확인합니다.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성

이 섹션에서는 Azure Portal에서 Azure AD Single Sign-On을 사용하도록 설정합니다.

BlueJeans에서 Azure AD Single Sign-On을 구성하려면 다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com/)의 **BlueJeans** 애플리케이션 통합 페이지에서 **Single Sign-On**을 선택합니다.

    ![Single Sign-On 구성 링크](common/select-sso.png)

2. **Single Sign-On 방법 선택** 대화 상자에서 **SAML/WS-Fed** 모드를 선택하여 Single Sign-On을 사용하도록 설정합니다.

    ![Single Sign-On 선택 모드](common/select-saml-option.png)

3. **SAML로 Single Sign-On 설정** 페이지에서 **편집** 아이콘을 클릭하여 **기본 SAML 구성** 대화 상자를 엽니다.

    ![기본 SAML 구성 편집](common/edit-urls.png)

4. **기본 SAML 구성** 섹션에서 다음 단계를 수행합니다.

    ![BlueJeans 도메인 및 URL Single Sign-On 정보](common/sp-identifier.png)

    a. **로그인 URL** 텍스트 상자에서 `https://<companyname>.BlueJeans.com` 패턴을 사용하여 URL을 입력합니다.

    b. **식별자** 텍스트 상자에 URL을 입력합니다. `http://samlsp.bluejeans.com`

    > [!NOTE]
    > 로그온 URL 값은 실제 값이 아닙니다. 이 값을 실제 로그온 URL로 업데이트합니다. 값을 얻으려면 [BlueJeans 클라이언트 지원 팀](https://support.bluejeans.com/contact)에 문의하세요. Azure Portal의 **기본 SAML 구성** 섹션에 표시된 패턴을 참조할 수도 있습니다.

5. **SAML로 Single Sign-On 설정** 페이지의 **SAML 서명 인증서** 섹션에서 **다운로드**를 클릭하여 요구 사항에 따라 제공된 옵션에서 **인증서(Base64)** 를 다운로드한 다음, 컴퓨터에 저장합니다.

    ![인증서 다운로드 링크](common/certificatebase64.png)

6. **BlueJeans 설정** 섹션에서 요구 사항에 따라 적절한 URL을 복사합니다.

    ![구성 URL 복사](common/copy-configuration-urls.png)

    a. 로그인 URL

    b. Azure AD 식별자

    다. 로그아웃 URL

### <a name="configure-bluejeans-single-sign-on"></a>BlueJeans Single Sign-On 구성

1. 다른 웹 브라우저 창에서 **BlueJeans** 회사 사이트에 관리자로 로그인합니다.

2. **관리자 \> 그룹 설정 \> 보안**으로 이동합니다.

    ![관리자](./media/bluejeans-tutorial/ic785868.png "관리자")

3. **보안** 섹션에서 다음 단계를 수행합니다.

    ![SAML Single Sign-On](./media/bluejeans-tutorial/ic785869.png "SAML Single Sign-On")

    a. **SAML Single Sign On**을 선택합니다.

    b. **자동 프로비전닝 사용**을 선택합니다.

4. 다음 단계로 이동합니다.

    ![인증서 경로](./media/bluejeans-tutorial/ic785870.png "인증서 경로")

    a. **파일 선택**을 클릭하여 Azure Portal에서 다운로드한 base-64로 인코드된 인증서를 업로드합니다.

    b. Azure Portal에서 복사한 **로그인 URL** 값을 **로그인 URL** 텍스트 상자에 붙여넣습니다.

    다. Azure Portal에서 복사한 **암호 변경 URL** 값을 **암호 변경 URL** 텍스트 상자에 붙여넣습니다.

    d. Azure Portal에서 복사한 **로그아웃 URL** 값을 **로그아웃 URL** 텍스트 상자에 붙여넣습니다.

5. 다음 단계로 이동합니다.

    ![변경 내용 저장](./media/bluejeans-tutorial/ic785874.png "변경 내용 저장")

    a. **사용자 Id** 텍스트 상자에 `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`을 입력합니다.

    b. **전자 메일 주소** 텍스트 상자에 `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`를 입력합니다.

    다. **변경 내용 저장**을 클릭합니다.

### <a name="create-an-azure-ad-test-user"></a>Azure AD 테스트 사용자 만들기

이 섹션의 목적은 Azure Portal에서 Britta Simon이라는 테스트 사용자를 만드는 것입니다.

1. Azure Portal의 왼쪽 창에서 **Azure Active Directory**, **사용자**를 차례로 선택하고 **모든 사용자**를 선택합니다.

    !["사용자 및 그룹" 및 "모든 사용자" 링크](common/users.png)

2. 화면 위쪽에서 **새 사용자**를 선택합니다.

    ![새 사용자 단추](common/new-user.png)

3. 사용자 속성에서 다음 단계를 수행합니다.

    ![사용자 대화 상자](common/user-properties.png)

    a. **이름** 필드에 **BrittaSimon**을 입력합니다.
  
    b. **사용자 이름** 필드에 `brittasimon\@yourcompanydomain.extension`을 입력합니다. 예: BrittaSimon@contoso.com

    다. **암호 표시** 확인란을 선택한 다음, [암호] 상자에 표시된 값을 적어둡니다.

    d. **만들기**를 클릭합니다.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD 테스트 사용자 할당

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 Britta Simon에게 BlueJeans에 대한 액세스 권한을 부여합니다.

1. Azure Portal에서 **엔터프라이즈 애플리케이션**을 선택한 다음, **모든 애플리케이션**과 **BlueJeans**를 차례로 선택합니다.

    ![엔터프라이즈 애플리케이션 블레이드](common/enterprise-applications.png)

2. 애플리케이션 목록에서 **BlueJeans**를 선택합니다.

    ![애플리케이션 목록의 BlueJeans 링크](common/all-applications.png)

3. 왼쪽 메뉴에서 **사용자 및 그룹**을 선택합니다.

    !["사용자 및 그룹" 링크](common/users-groups-blade.png)

4. **사용자 추가** 단추를 클릭한 다음, **할당 추가** 대화 상자에서 **사용자 및 그룹**을 선택합니다.

    ![할당 추가 창](common/add-assign-user.png)

5. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **Britta Simon**을 선택하고 화면 아래쪽에서 **선택** 단추를 클릭합니다.

6. SAML 어설션 및 **역할 선택** 대화 상자에서 모든 역할 값이 필요한 경우 목록에서 적절한 사용자 역할을 선택한 다음, 화면 맨 아래에 있는 **선택** 단추를 클릭합니다.

7. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.

### <a name="create-bluejeans-test-user"></a>BlueJeans 테스트 사용자 만들기

이 섹션은 BlueJeans에서 Britta Simon이라는 사용자를 만들기 위한 것입니다. BlueJeans는 자동 사용자 프로비전을 지원하며 기본적으로 사용하도록 설정되어 있습니다. 자동 사용자 프로비전 구성 방법에 대한 자세한 내용은 [여기](bluejeans-provisioning-tutorial.md)에서 제공합니다.

**사용자를 수동으로 만들어야 할 경우 다음 단계를 수행합니다.**

1. **BlueJeans** 회사 사이트에 관리자 권한으로 로그인합니다.

2. **관리자 \> 사용자 관리 \> 사용자 추가**로 이동합니다.

    ![관리자](./media/bluejeans-tutorial/ic785877.png "관리자")

    > [!IMPORTANT]
    > **사용자 추가** 탭은 **보안 탭**에서 **자동 프로비저닝 사용**이 선택 취소된 경우에만 사용할 수 있습니다.

3. **사용자 추가** 섹션에서 다음 단계를 수행합니다.

    ![사용자 추가](./media/bluejeans-tutorial/ic785886.png "사용자 추가")

    a. **이름** 텍스트 상자에 사용자의 이름(예: **Britta**)을 입력합니다.

    b. **성** 텍스트 상자에 사용자의 성(예: **Simon**)을 입력합니다.

    다. **BlueJeans 사용자 이름 선택** 텍스트 상자에 사용자 이름(예: **Brittasimon**)을 입력합니다.

    d. **암호 만들기** 텍스트 상자에 암호를 입력합니다.

    e. **회사** 텍스트 상자에 회사를 입력합니다.

    f. **이메일 주소** 텍스트 상자에 `brittasimon\@contoso.com`과 같은 사용자의 이메일을 입력합니다.

    g. **BlueJeans 모임 ID 만들기** 텍스트 상자에 모임 ID를 입력합니다.

    h. **중재자 암호 선택** 텍스트 상자에 암호를 입력합니다.

    i. **계속**을 클릭합니다.

    ![사용자 추가](./media/bluejeans-tutorial/ic785887.png "사용자 추가")

    J. **사용자 추가**를 클릭합니다.

> [!NOTE]
> BlueJeans에서 제공하는 기타 모든 BlueJeans 사용자 계정 만들기 도구 또는 API를 사용하여 Azure AD 사용자 계정을 프로비전할 수 있습니다.

### <a name="test-single-sign-on"></a>Single Sign-On 테스트

이 섹션에서는 액세스 패널을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다.

액세스 패널에서 BlueJeans 타일을 클릭하면, SSO를 설정한 BlueJeans에 자동으로 로그인되어야 합니다. 액세스 패널에 대한 자세한 내용은 [액세스 패널 소개](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)를 참조하세요.

## <a name="additional-resources"></a>추가 리소스

- [Azure Active Directory와 SaaS Apps를 통합하는 방법에 대한 자습서 목록](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory로 애플리케이션 액세스 및 Single Sign-On을 구현하는 방법](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory의 조건부 액세스란?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
