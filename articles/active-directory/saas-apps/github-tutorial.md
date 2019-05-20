---
title: '자습서: GitHub와 Azure Active Directory 통합 | Microsoft Docs'
description: Azure Active Directory와 GitHub 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 8761f5ca-c57c-4a7e-bf14-ac0421bd3b5e
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/11/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 25540d1f26fa6021ef05108f9743e77a6184f3b3
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/18/2019
ms.locfileid: "59426327"
---
# <a name="tutorial-azure-active-directory-integration-with-github"></a>자습서: GitHub와 Azure Active Directory 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 GitHub를 통합하는 방법에 대해 알아봅니다.
GitHub를 Azure AD와 통합하면 다음과 같은 이점이 제공됩니다.

* GitHub에 대한 액세스 권한이 있는 사용자를 Azure AD에서 제어할 수 있습니다.
* 사용자가 Azure AD 계정으로 GitHub에 자동으로 로그인(Single Sign-On)되도록 설정할 수 있습니다.
* 단일 중앙 위치인 Azure Portal에서 계정을 관리할 수 있습니다.

Azure AD와의 SaaS 앱 연결에 대한 자세한 내용은 [Azure Active Directory를 사용한 응용 프로그램 액세스 및 Single Sign-On](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)을 참조하세요.
Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/) 계정을 만듭니다.

## <a name="prerequisites"></a>필수 조건

GitHub와 Azure AD 통합을 구성하려면 다음 항목이 필요합니다.

* Azure AD 구독 Azure AD 환경이 없으면 [여기](https://azure.microsoft.com/pricing/free-trial/)에서 1개월 평가판을 구할 수 있습니다.
* [GitHub Enterprise Cloud](https://help.github.com/articles/github-s-products/#github-enterprise)에서 만든 GitHub 조직. 여기에는 [GitHub Enterprise 요금제](https://help.github.com/articles/github-s-billing-plans/#billing-plans-for-organizations)가 필요합니다.

## <a name="scenario-description"></a>시나리오 설명

이 자습서에서는 테스트 환경에서 Azure AD Single Sign-On을 구성하고 테스트합니다.

* GitHub에서 **SP** 시작 SSO를 지원합니다.

* GitHub에서 [**자동** 사용자 프로비전](github-provisioning-tutorial.md)을 지원합니다.

## <a name="adding-github-from-the-gallery"></a>갤러리에서 GitHub 추가

Azure AD에 GitHub 통합을 구성하려면 갤러리의 GitHub를 관리되는 SaaS 앱 목록에 추가해야 합니다.

**갤러리에서 GitHub를 추가하려면 다음 단계를 수행합니다.**

1. **[Azure Portal](https://portal.azure.com)** 의 왼쪽 탐색 창에서 **Azure Active Directory** 아이콘을 클릭합니다.

    ![Azure Active Directory 단추](common/select-azuread.png)

2. **엔터프라이즈 응용 프로그램**으로 이동한 다음, **모든 응용 프로그램** 옵션을 선택합니다.

    ![엔터프라이즈 애플리케이션 블레이드](common/enterprise-applications.png)

3. 새 애플리케이션을 추가하려면 대화 상자 맨 위 있는 **새 애플리케이션** 단추를 클릭합니다.

    ![새 애플리케이션 단추](common/add-new-app.png)

4. 검색 상자에 **GitHub**를 입력하고 결과 패널에서 **GitHub.com**을 선택한 다음, **추가** 단추를 클릭하여 애플리케이션을 추가합니다.

     ![결과 목록의 GitHub](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성 및 테스트

이 섹션에서는 **Britta Simon**이라는 테스트 사용자를 기반으로 GitHub에서 Azure AD Single Sign-On을 구성하고 테스트합니다.
Single Sign-On이 작동하려면 Azure AD 사용자와 GitHub의 관련 사용자 간에 연결 관계를 설정해야 합니다.

GitHub에서 Azure AD Single Sign-On을 구성하고 테스트하려면 다음 구성 요소를 완료해야 합니다.

1. **[Azure AD Single Sign-On 구성](#configure-azure-ad-single-sign-on)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
2. **[GitHub Single Sign-On 구성](#configure-github-single-sign-on)** - 애플리케이션 쪽에서 Single Sign-On 설정을 구성합니다.
3. **[Azure AD 테스트 사용자 만들기](#create-an-azure-ad-test-user)** - Britta Simon으로 Azure AD Single Sign-On을 테스트하는 데 사용합니다.
4. **[Azure AD 테스트 사용자 할당](#assign-the-azure-ad-test-user)** - Britta Simon이 Azure AD Single Sign-on을 사용할 수 있도록 합니다.
5. **[GitHub 테스트 사용자 만들기](#create-github-test-user)** - Britta Simon의 Azure AD 표현과 연결된 해당 사용자를 GitHub에 만듭니다.
6. **[Single Sign-On 테스트](#test-single-sign-on)** - 구성이 작동하는지 여부를 확인합니다.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성

이 섹션에서는 Azure Portal에서 Azure AD Single Sign-On을 사용하도록 설정합니다.

GitHub에서 Azure AD Single Sign-On을 구성하려면 다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com/)의 **GitHub** 애플리케이션 통합 페이지에서 **Single Sign-On**을 선택합니다.

    ![Single Sign-On 구성 링크](common/select-sso.png)

2. **Single Sign-On 방법 선택** 대화 상자에서 **SAML/WS-Fed** 모드를 선택하여 Single Sign-On을 사용하도록 설정합니다.

    ![Single Sign-On 선택 모드](common/select-saml-option.png)

3. **SAML로 Single Sign-On 설정** 페이지에서 **편집** 아이콘을 클릭하여 **기본 SAML 구성** 대화 상자를 엽니다.

    ![기본 SAML 구성 편집](common/edit-urls.png)

4. **기본 SAML 구성** 섹션에서 다음 단계를 수행합니다.

    ![GitHub 도메인 및 URL Single Sign-On 정보](common/sp-identifier.png)

    a. **로그온 URL** 텍스트 상자에서 `https://github.com/orgs/<entity-id>/sso` 패턴을 사용하는 URL을 입력합니다.

    b. **식별자(엔터티 ID)** 텍스트 상자에서 `https://github.com/orgs/<entity-id>` 패턴을 사용하는 URL을 입력합니다.

    > [!NOTE]
    > 이러한 값은 실제 값이 아닙니다. 이러한 값을 실제 로그온 URL 및 식별자로 업데이트해야 합니다. 식별자에는 고유한 문자열 값을 사용하는 것이 좋습니다. GitHub 관리자 섹션으로 이동하여 이러한 값을 검색합니다.

5. GitHub 애플리케이션은 특정 서식에서 SAML 어설션을 예상하며 이는 GitHub 토큰 특성 구성에 사용자 지정 특성 매핑을 추가합니다. 다음 스크린샷에서는 **nameidentifier**가 **user.userprincipalname**과 매핑된 기본 특성의 목록을 보여 줍니다. GitHub 애플리케이션에서는 **nameidentifier**가 **user.mail**과 매핑되므로 특성 매핑을 변경하기 위해 **편집** 아이콘을 클릭하여 특성 매핑을 편집해야 합니다.

    ![이미지](common/edit-attribute.png)

6. **SAML로 Single Sign-On 설정** 페이지의 **SAML 서명 인증서** 섹션에서 **다운로드**를 클릭하여 요구 사항에 따라 제공된 옵션에서 **인증서(Base64)** 를 다운로드한 다음, 컴퓨터에 저장합니다.

    ![인증서 다운로드 링크](common/certificatebase64.png)

7. **GitHub 설정** 섹션에서 요구 사항에 따라 적절한 URL을 복사합니다.

    ![구성 URL 복사](common/copy-configuration-urls.png)

    a. 로그인 URL

    b. Azure AD 식별자

    다. 로그아웃 URL

### <a name="configure-github-single-sign-on"></a>GitHub Single Sign-On 구성

1. 다른 웹 브라우저 창에서 GitHub 회사 사이트에 관리자로 로그인합니다.

2. **설정**으로 이동하고 **보안**을 클릭합니다.

    ![설정](./media/github-tutorial/tutorial_github_config_github_03.png)

3. **SAML 인증 사용** 상자를 확인하여 Single Sign-On 구성 필드를 표시합니다. 그런 다음 Single Sign-On URL 값을 사용하여 Azure AD 구성에서 Single Sign-On URL을 업데이트합니다.

    ![설정](./media/github-tutorial/tutorial_github_config_github_13.png)

4. 다음 필드를 구성합니다.

    ![설정](./media/github-tutorial/tutorial_github_config_github_051.png)

    a. **로그온 URL** 텍스트 상자에 Azure Portal에서 복사한 **로그인 URL** 값을 붙여넣습니다.

    b. **발급자** 텍스트 상자에 Azure Portal에서 복사한 **Azure AD 식별자** 값을 붙여넣습니다.

    다. Azure Portal에서 다운로드한 인증서 파일의 내용을 메모장에 복사한 다음, **공용 인증서** 텍스트 상자에 붙여넣습니다.

    d. 아래와 같이 **편집** 아이콘을 클릭하여 **RSA-SHA1** 및 **SHA1**의 **서명 방법** 및 **다이제스트 메서드**를 **RSA-SHA256** 및 **SHA256**으로 편집합니다.

    ![이미지](./media/github-tutorial/tutorial_github_sha.png)

5. **SAML 구성 테스트**를 클릭하여 SSO 동안 유효성 검사 실패 또는 오류가 없는지 확인합니다.

    ![설정](./media/github-tutorial/tutorial_github_config_github_06.png)

6. 페이지 맨 아래에 있는 **저장**

> [!NOTE]
> GitHub의 Single Sign-On은 GitHub의 특정 조직에 인증되며 GitHub 자체의 인증을 대체하지 않습니다. 따라서 사용자의 GitHub.com 세션이 만료되면 Single Sign-On 프로세스 중에 GitHub의 ID/암호로 인증하라는 메시지가 표시될 수 있습니다.

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

이 섹션에서는 GitHub에 대한 액세스 권한을 부여하여 Britta Simon이 Azure Single Sign-On을 사용하도록 설정합니다.

1. Azure Portal에서 **엔터프라이즈 애플리케이션**, **모든 애플리케이션**, **GitHub**를 차례로 선택합니다.

    ![엔터프라이즈 애플리케이션 블레이드](common/enterprise-applications.png)

2. 애플리케이션 목록에서 **GitHub**를 선택합니다.

    ![애플리케이션 목록의 GitHub 링크](common/all-applications.png)

3. 왼쪽 메뉴에서 **사용자 및 그룹**을 선택합니다.

    !["사용자 및 그룹" 링크](common/users-groups-blade.png)

4. **사용자 추가** 단추를 클릭한 다음, **할당 추가** 대화 상자에서 **사용자 및 그룹**을 선택합니다.

    ![할당 추가 창](common/add-assign-user.png)

5. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **Britta Simon**을 선택하고 화면 아래쪽에서 **선택** 단추를 클릭합니다.

6. SAML 어설션 및 **역할 선택** 대화 상자에서 모든 역할 값이 필요한 경우 목록에서 적절한 사용자 역할을 선택한 다음, 화면 맨 아래에 있는 **선택** 단추를 클릭합니다.

7. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.

### <a name="create-github-test-user"></a>GitHub 테스트 사용자 만들기

이 섹션은 GitHub에서 Britta Simon이라는 사용자를 만들기 위한 것입니다. GitHub는 자동 사용자 프로비전을 지원하며 기본적으로 사용하도록 설정되어 있습니다. 자동 사용자 프로비전 구성 방법에 대한 자세한 내용은 [여기](github-provisioning-tutorial.md)에서 제공합니다.

**사용자를 수동으로 만들어야 할 경우 다음 단계를 수행합니다.**

1. GitHub 회사 사이트에 관리자 권한으로 로그인합니다.

2. **피플**을 클릭합니다.

    ![사람](./media/github-tutorial/tutorial_github_config_github_08.png "사람")

3. **멤버 초대**를 클릭합니다.

    ![사용자 초대](./media/github-tutorial/tutorial_github_config_github_09.png "사용자 초대")

4. **멤버 초대** 대화 상자 페이지에서 다음 단계를 수행합니다.

    a. **전자 메일** 텍스트 상자에 Britta Simon 계정의 전자 메일 주소를 입력합니다.

    ![피플 초대](./media/github-tutorial/tutorial_github_config_github_10.png "피플 초대")

    b. **초대 보내기**를 클릭합니다.

    ![피플 초대](./media/github-tutorial/tutorial_github_config_github_11.png "피플 초대")

    > [!NOTE]
    > Azure Active Directory 계정 보유자는 활성화되기 전에 전자 메일을 받고 링크를 따라 계정을 확인합니다.

### <a name="test-single-sign-on"></a>Single Sign-On 테스트 

이 섹션에서는 액세스 패널을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다.

액세스 패널에서 GitHub 타일을 클릭하면 SSO를 설정한 GitHub에 자동으로 로그인됩니다. 액세스 패널에 대한 자세한 내용은 [액세스 패널 소개](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)를 참조하세요.

## <a name="additional-resources"></a>추가 리소스

- [Azure Active Directory와 SaaS Apps를 통합하는 방법에 대한 자습서 목록](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory로 애플리케이션 액세스 및 Single Sign-On을 구현하는 방법](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory의 조건부 액세스란?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
