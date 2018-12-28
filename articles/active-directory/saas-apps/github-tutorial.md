---
title: '자습서: GitHub와 Azure Active Directory 통합 | Microsoft Docs'
description: Azure Active Directory와 GitHub 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 8761f5ca-c57c-4a7e-bf14-ac0421bd3b5e
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/15/2018
ms.author: jeedes
ms.openlocfilehash: dbd4634c575fd4f1886d3e7714ef9ddabbde0f8a
ms.sourcegitcommit: 1aacea6bf8e31128c6d489fa6e614856cf89af19
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/16/2018
ms.locfileid: "49341160"
---
# <a name="tutorial-azure-active-directory-integration-with-github"></a>자습서: GitHub와 Azure Active Directory 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 GitHub를 통합하는 방법에 대해 알아봅니다.

GitHub를 Azure AD와 통합하면 다음과 같은 이점이 제공됩니다.

- GitHub에 대한 액세스 권한이 있는 사용자를 Azure AD에서 제어할 수 있습니다.
- 사용자가 해당 Azure AD 계정으로 GitHub에 자동으로 로그온(Single Sign-On)되도록 설정할 수 있습니다.
- 단일 중앙 위치인 Azure Portal에서 계정을 관리할 수 있습니다.

Azure AD와 SaaS 앱 통합에 대한 자세한 내용은 [Azure Active Directory의 애플리케이션 액세스 및 Single Sign-On이란 무엇인가요?](../manage-apps/what-is-single-sign-on.md)를 참조하세요.

## <a name="prerequisites"></a>필수 조건

GitHub와 Azure AD 통합을 구성하려면 다음 항목이 필요합니다.

- Azure AD 구독
- GitHub Single Sign-On이 설정된 구독

> [!NOTE]
> 이 자습서의 단계를 테스트하기 위해 프로덕션 환경을 사용하는 것은 바람직하지 않습니다.

이 자습서의 단계를 테스트하려면 다음 권장 사항을 준수해야 합니다.

- 꼭 필요한 경우가 아니면 프로덕션 환경을 사용하지 마세요.
- Azure AD 평가판 환경이 없으면 [1개월 평가판을 얻을](https://azure.microsoft.com/pricing/free-trial/) 수 있습니다.

## <a name="scenario-description"></a>시나리오 설명
이 자습서에서는 테스트 환경에서 Azure AD Single Sign-On을 테스트 합니다.  이 자습서에 설명된 시나리오는 다음 두 가지 주요 구성 요소로 이루어져 있습니다.

1. 갤러리에서 GitHub 추가
2. Azure AD Single Sign-on 구성 및 테스트

## <a name="adding-github-from-the-gallery"></a>갤러리에서 GitHub 추가
Azure AD에 GitHub 통합을 구성하려면 갤러리의 GitHub를 관리되는 SaaS 앱 목록에 추가해야 합니다.

**갤러리에서 GitHub를 추가하려면 다음 단계를 수행합니다.**

1. **[Azure Portal](https://portal.azure.com)** 의 왼쪽 탐색 창에서 **Azure Active Directory** 아이콘을 클릭합니다. 

    ![이미지](./media/github-tutorial/selectazuread.png)

2. **엔터프라이즈 응용 프로그램**으로 이동합니다. 그런 후 **모든 애플리케이션**으로 이동합니다.

    ![이미지](./media/github-tutorial/a_select_app.png)
    
3. 새 애플리케이션을 추가하려면 대화 상자 맨 위 있는 **새 애플리케이션** 단추를 클릭합니다.

    ![이미지](./media/github-tutorial/a_new_app.png)

4. 검색 상자에 **GitHub**를 입력하고 결과 패널에서 **GitHub**를 선택한 다음, **추가** 단추를 클릭하여 애플리케이션을 추가합니다.

     ![이미지](./media/github-tutorial/tutorial_github_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성 및 테스트

이 섹션에서는 "Britta Simon"이라는 테스트 사용자를 기반으로 GitHub에서 Azure AD Single Sign-On을 구성하고 테스트합니다.

Single Sign-On이 작동하려면 Azure AD에서 Azure AD 사용자에 해당하는 GitHub 사용자가 누구인지 알고 있어야 합니다. 즉, Azure AD 사용자와 GitHub의 관련 사용자 간에 연결이 설정되어야 합니다.

GitHub에서 Azure AD Single Sign-On을 구성하고 테스트하려면 다음 구성 요소를 완료해야 합니다.

1. **[Azure AD Single Sign-On 구성](#configure-azure-ad-single-sign-on)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
2. **[Azure AD 테스트 사용자 만들기](#create-an-azure-ad-test-user)** - Britta Simon으로 Azure AD Single Sign-On을 테스트하는 데 사용합니다.
3. **[GitHub 테스트 사용자 만들기](#create-a-github-test-user)** - Britta Simon의 Azure AD 표현과 연결된 해당 사용자를 GitHub에 만듭니다.
4. **[Azure AD 테스트 사용자 할당](#assign-the-azure-ad-test-user)** - Britta Simon이 Azure AD Single Sign-on을 사용할 수 있도록 합니다.
5. **[Single Sign-On 테스트](#test-single-sign-on)** - 구성이 작동하는지 여부를 확인합니다.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성

이 섹션에서는 Azure Portal에서 Azure AD Single Sign-On을 사용하도록 설정하고 GitHub 애플리케이션에서 Single Sign-On을 구성합니다.

**GitHub에서 Azure AD Single Sign-On을 구성하려면 다음 단계를 수행합니다.**

1. [Azure Portal](https://portal.azure.com/)의 **GitHub** 응용 프로그램 통합 페이지에서 **Single Sign-On**을 선택합니다.

    ![이미지](./media/github-tutorial/b1_b2_select_sso.png)

2. **Single Sign-On 방법 선택** 대화 상자에서 **SAML** 모드에 대해 **선택**을 클릭하여 Single Sign-On을 사용하도록 설정합니다.

    ![이미지](./media/github-tutorial/b1_b2_saml_sso.png)

3. **SAML로 Single Sign-On 설정** 페이지에서 **편집** 단추를 클릭하여 **기본 SAML 구성** 대화 상자를 엽니다.

    ![이미지](./media/github-tutorial/b1-domains_and_urlsedit.png)

4. **기본 SAML 구성** 섹션에서 다음 단계를 수행합니다.

    ![이미지](./media/github-tutorial/tutorial_github_url.png) 

    a. **로그온 URL** 텍스트 상자에서 다음과 같은 패턴을 사용하여 URL을 입력합니다. `https://github.com/orgs/<entity-id>/sso`

    b. **식별자(엔터티 ID)** 텍스트 상자에서 다음 패턴을 사용하여 URL을 입력합니다. `https://github.com/orgs/<entity-id>`

    > [!NOTE]
    > 이러한 값은 실제 값이 아닙니다. 이러한 값을 실제 로그온 URL 및 식별자로 업데이트해야 합니다. 식별자에는 고유한 문자열 값을 사용하는 것이 좋습니다. GitHub 관리자 섹션으로 이동하여 이러한 값을 검색합니다.

5. GitHub 응용 프로그램에는 특정 서식의 SAML 어설션이 필요합니다. 이 애플리케이션에 대해 다음 클레임을 구성합니다. 애플리케이션 통합 페이지의 **사용자 특성** 섹션에서 이러한 특성의 값을 관리할 수 있습니다. **편집** 단추를 클릭하여 **사용자 특성** 대화 상자를 엽니다.

    ![이미지](./media/github-tutorial/i3-attribute.png)

6. **사용자 특성** 대화 상자의 **사용자 클레임** 섹션에서 위의 이미지에 표시된 것과 같이 SAML 토큰 특성을 구성하고 다음 단계를 수행합니다.
    
    a. **편집**을 클릭하여 **사용자 클레임 관리** 대화 상자를 엽니다.

    ![이미지](./media/github-tutorial/i2-attribute.png)

    ![이미지](./media/github-tutorial/i4-attribute.png)

    b. **원본 특성** 목록에서 특성 값을 선택합니다.

    다. **저장**을 클릭합니다.
 
7. **SAML 서명 인증서** 섹션에서 **다운로드**를 클릭하여 **인증서(Base64)** 를 다운로드하고 컴퓨터에 저장합니다.

    ![이미지](./media/github-tutorial/tutorial_github_certficate.png)

8. **GitHub 설정** 섹션에서 요구 사항에 따라 적절한 URL을 복사합니다.

    a. 로그인 URL

    b. Azure AD 식별자

    다. 로그아웃 URL

    ![이미지](./media/github-tutorial/d1_samlsonfigure.png) 

9. 다른 웹 브라우저 창에서 GitHub 회사 사이트에 관리자로 로그인합니다.

10. **설정**으로 이동하고 **보안**을 클릭합니다.

    ![설정](./media/github-tutorial/tutorial_github_config_github_03.png)

11. **SAML 인증 사용** 상자를 확인하여 Single Sign-On 구성 필드를 표시합니다. 그런 다음 Single Sign-On URL 값을 사용하여 Azure AD 구성에서 Single Sign-On URL을 업데이트합니다.

    ![설정](./media/github-tutorial/tutorial_github_config_github_13.png)

12. 다음 필드를 구성합니다.

    ![설정](./media/github-tutorial/tutorial_github_config_github_051.png)

    a. **로그온 URL** 텍스트 상자에 Azure Portal에서 복사한 **로그인 URL** 값을 붙여넣습니다.

    b. **발급자** 텍스트 상자에 Azure Portal에서 복사한 **Azure AD 식별자** 값을 붙여넣습니다.

    다. Azure Portal에서 다운로드한 인증서 파일의 내용을 메모장에 복사한 다음, **공용 인증서** 텍스트 상자에 붙여넣습니다.

    d. 아래와 같이 **편집** 아이콘을 클릭하여 **RSA-SHA1** 및 **SHA1**의 **서명 방법** 및 **다이제스트 메서드**를 **RSA-SHA256** 및 **SHA256**으로 편집합니다.

    ![이미지](./media/github-tutorial/tutorial_github_sha.png) 
    
13. **SAML 구성 테스트**를 클릭하여 SSO 동안 유효성 검사 실패 또는 오류가 없는지 확인합니다.

    ![설정](./media/github-tutorial/tutorial_github_config_github_06.png)

14. 페이지 맨 아래에 있는 **저장**

> [!NOTE]
> GitHub의 Single Sign-On은 GitHub의 특정 조직에 인증되며 GitHub 자체의 인증을 대체하지 않습니다. 따라서 사용자의 GitHub.com 세션이 만료되면 Single Sign-On 프로세스 중에 GitHub의 ID/암호로 인증하라는 메시지가 표시될 수 있습니다.

### <a name="create-an-azure-ad-test-user"></a>Azure AD 테스트 사용자 만들기

이 섹션의 목적은 Azure Portal에서 Britta Simon이라는 테스트 사용자를 만드는 것입니다.

1. Azure Portal의 왼쪽 창에서 **Azure Active Directory**, **사용자**, **모든 사용자**를 차례로 선택합니다.

    ![이미지](./media/github-tutorial/d_users_and_groups.png)

2. 화면 위쪽에서 **새 사용자**를 선택합니다.

    ![이미지](./media/github-tutorial/d_adduser.png)

3. 사용자 속성에서 다음 단계를 수행합니다.

    ![이미지](./media/github-tutorial/d_userproperties.png)

    a. **이름** 필드에 **BrittaSimon**을 입력합니다.
  
    b. **사용자 이름** 필드에 **brittasimon@yourcompanydomain.extension**을 입력합니다.  
    예를 들어 BrittaSimon@contoso.com

    다. **속성**을 선택하고, **암호 표시** 확인란을 선택한 다음, 암호 상자에 표시된 값을 적어둡니다.

    d. **만들기**를 선택합니다.
 
### <a name="create-a-github-test-user"></a>GitHub 테스트 사용자 만들기

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

### <a name="assign-the-azure-ad-test-user"></a>Azure AD 테스트 사용자 할당

이 섹션에서는 GitHub에 대한 액세스 권한을 부여하여 Britta Simon이 Azure Single Sign-On을 사용하도록 설정합니다.

1. Azure Portal에서 **엔터프라이즈 응용 프로그램**을 선택하고, **모든 응용 프로그램**을 선택합니다.

    ![이미지](./media/github-tutorial/d_all_applications.png)

2. 애플리케이션 목록에서 **GitHub**를 선택합니다.

    ![이미지](./media/github-tutorial/tutorial_github_app.png)

3. 왼쪽 메뉴에서 **사용자 및 그룹**을 선택합니다.

    ![이미지](./media/github-tutorial/d_leftpaneusers.png)

4. **추가** 단추를 선택한 다음, **할당 추가** 대화 상자에서 **사용자 및 그룹**을 선택합니다.

    ![이미지](./media/github-tutorial/d_assign_user.png)

4. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **Britta Simon**을 선택한 다음, 화면 아래쪽에서 **선택** 단추를 클릭합니다.

5. **할당 추가** 대화 상자에서 **할당** 단추를 선택합니다.
    
### <a name="test-single-sign-on"></a>Single Sign-On 테스트

이 섹션에서는 액세스 패널을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다.

액세스 패널에서 GitHub 타일을 클릭하면 GitHub 애플리케이션에 자동으로 로그온됩니다.
액세스 패널에 대한 자세한 내용은 [액세스 패널 소개](../active-directory-saas-access-panel-introduction.md)를 참조하세요. 

## <a name="additional-resources"></a>추가 리소스

* [Azure Active Directory와 SaaS Apps를 통합하는 방법에 대한 자습서 목록](tutorial-list.md)
* [Azure Active Directory로 응용 프로그램 액세스 및 Single Sign-On을 구현하는 방법](../manage-apps/what-is-single-sign-on.md)


